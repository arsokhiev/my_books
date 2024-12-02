# Gameplay Tags
`Gameplay Tags` представляют собой иерархические текстовые метки, разделяемые символом `.` точки в формате `SuperTag.SubTag.Tag`. Теги являются очень удобным инструментом классификации и разделения игровой логики.
Используя `Gameplay Tags` можно создавать гибкие системы искусственного интеллекта, управлять объектами на уровне, обмениваться переменными и событиями. Часть подобных механик будет реализована в этой книге.
С использованием `Gameplay Tags` построен ключевой фреймворк `Unreal Engine`, а именно - `Gameplay Ability System`.
Давайте изучим как теги работают и как их использовать в собственном проекте.
## Принципы системы
Формат тега - `TagCategody.TagName`.
Например:
```cpp
Combat
Combat.InCombatMode
Combat.hasTargeting
Locomotion
Locomotion.isCrouching
Locomotion.isInWater
Locomotion.isRun
Locomotion.isRun.Fast
Locomotion.isRun.Injured
```
Каждый тег разделяется символом точки `.`. Система сама генерирует из тегов, разбитых точками, древовидные структуры.
Все теги, используемые в проекте, можно найти на соответствующей странице параметров проекта `Edit -> Project Settings -> Gameplay Tags`. По умолчанию там пусто.
![ad419d5545b95facc5de2ab94db2b437.png](../images/ad419d5545b95facc5de2ab94db2b437.png)
Нажав `Add New Gameplay Tag` можно добавить собственные теги.
![42751468920beeb14458bbba15278534.png](../images/42751468920beeb14458bbba15278534.png)
![56bdba559f21f5a17c3b3c6e60dd1695.png](../images/56bdba559f21f5a17c3b3c6e60dd1695.png)
Хотя я не рекомендую использовать страницу настроек для ручного задания тегов.
Лучше всего использовать вынос тегов в отдельные таблицы `Data Table` или жестко задавать их через `с++ код`. Если добавлять теги "вручную", то они просто слетят, когда вы будете мигрировать проект. А это может привести к выходу из строя кучи игровых систем.
Чтобы использовать `Gameplay Tags` из `blueprint` кода надо создать переменную с типом `Gameplay Tag`.
![fc0d25953a019e4c51296db330de218a.png](../images/fc0d25953a019e4c51296db330de218a.png)
![59c2c2dbf135bb12b38b3e7d78ce5f51.png](../images/59c2c2dbf135bb12b38b3e7d78ce5f51.png)
Так-же нужен контейнер, в котором с тегами предполагается работать. Он реализован в виде типа `Gameplay Tag Container`. Контейнер позволяет хранить в  себе теги, а так-же добавлять или удалять их.
![e67d2b341cda4a97b092c9fe38c698c9.png](../images/e67d2b341cda4a97b092c9fe38c698c9.png)
![8bbf36238ec7166c2e9ffd67798eea60.png](../images/8bbf36238ec7166c2e9ffd67798eea60.png)
Используя эти два типа, тег и контейнер, можно проверять те или иные состояния персонажей, объектов на уровне или распределять задачи искусственного интеллекта.
* Пример из моей практики
Используя систему `Gameplay Tags` я сделал два своих предыдущих проекта. Давайте рассмотрим один из них - `Ultimative Locomotion`.
![d2faf425cf4c7017caffb415e60684a8.png](../images/d2faf425cf4c7017caffb415e60684a8.png)
Все теги системы я поместил в отдельные таблицы, реализующие структуру `GameplayTagsTableRow`.
![03ec050af28675e141b6a3e404e825db.png](../images/03ec050af28675e141b6a3e404e825db.png)
В самом проекте я создал компонент, это `blueprint` компонент - `Tags Component`.
![2b13843b984050e068ed3ceb04584046.png](../images/2b13843b984050e068ed3ceb04584046.png)
Данный компонент содержит в себе контейнер тегов, а так-же функции, которые позволяют добавлять и удалять теги из контейнера. Эти функции, затем, были вынесены в библиотеку макросов, для более удобного доступа.
![2ca51f6c6f20b52ab6e8fd4f0deb1a34.png](../images/2ca51f6c6f20b52ab6e8fd4f0deb1a34.png)
Так-же в компоненте есть диспетчер событий, генерирующий событие всякий раз, как тег добавляется или удаляется из контейнера.
с++ открывает нам куда более широкие возможности по работе с тегами, нежели `blueprint`, например пресловутые `latent action`. Поэтому - давайте реализуем этот компонент тегов на с++. За одно это ознакомит нас системой (да и мне полезно будет, давно хотел это сделать).
## Подготовка к работе
По умолчанию классы `AActor` не поддерживают `gameplay tags`. Поддержку системы надо добавлять самостоятельно и чаще всего это делают через компоненты.
У компонентов есть одно несравненное преимущество - у любого объекта типа `AActor` можно получить компонент, методом `FindComponentByClass`. Это значит, что если мы реализуем контейнер тегов внутри компонента, то доступ к этому контейнеру легко будет получить из любого объекта на уровне.
### Создаем модуль
Создадим наши задачи отдельный модуль. Я назвал модуль `Tags Module`.
![9889c1f076039e8e63f9115149f212f9.png](../images/9889c1f076039e8e63f9115149f212f9.png)
### Создаем компонент тегов
Добавим новый `с++` класс, унаследованный от `ActorComponent`.
![24b4a98c39d2f2fda04e71aeac4e8b26.png](../images/24b4a98c39d2f2fda04e71aeac4e8b26.png)
![03daa4c62a9dc4af295c26561bf9b8a4.png](../images/03daa4c62a9dc4af295c26561bf9b8a4.png)
### Создаем библиотеку функций
А так-же библиотеку функций - `TagsLibrary`.
![990c2d46c62882185130e876b1a15ea6.png](../images/990c2d46c62882185130e876b1a15ea6.png)
![34a264779bc630926508dca927504d5d.png](../images/34a264779bc630926508dca927504d5d.png)
Не забудьте выполнить `Tools->Refresh Visual Studio 2022 Project`, чтобы файлы появились в `Solution Explorer`.
![4c924149a9c52e6a3a8b4cd52c76881e.png](../images/4c924149a9c52e6a3a8b4cd52c76881e.png)
### Дополняем список зависимостей у модуля
Теперь надо добавить модуль `GameplayTags` в зависимости. Для этого откройте файл `TagsModule.build.cs` и добавьте туда строчку:
```cpp
PrivateDependencyModuleNames.AddRange(new string[] { "GameplayTags" });
```
Полный код файла `TagsModule.build.cs`.
```cpp
using UnrealBuildTool;
public class TagsModule : ModuleRules
{
	public TagsModule(ReadOnlyTargetRules Target) : base(Target)
	{
        PCHUsage = PCHUsageMode.UseExplicitOrSharedPCHs;
		PublicDependencyModuleNames.AddRange(new string[] { "Core", "CoreUObject", "Engine"});
        PrivateDependencyModuleNames.AddRange(new string[] { "GameplayTags" });
        PublicIncludePaths.AddRange(new string[] {"TagsModule/Public"});
		PrivateIncludePaths.AddRange(new string[] {"TagsModule/Private"});
	}
}
```
## Пишем компонент тегов
Одной из ключевых особенностей системы `Gameplay Tags` является их привязка к контейнерам. Контейнеры тегов наследуются от класса `FGameplayTagContainer`.
![8bbf36238ec7166c2e9ffd67798eea60.png](../images/8bbf36238ec7166c2e9ffd67798eea60.png)
При этом по умолчанию ни у класса персонажа, ни у любого другого класса нет контейнера тегов. Его надо добавлять вручную. Можно использовать обычную переменную.
![f039a4e757ed7d0de415ef10825cff35.png](../images/f039a4e757ed7d0de415ef10825cff35.png)
Но такой подход имеет недостаток - для каждого класса, в котором мы захотим использовать теги - придется писать отдельное API.
Поэтому, куда выгоднее создать унифицированный компонент тегов, который с легкостью добавляется к любому классу `AActor` через удобное меню.
Мы уже сгенерировали класс компонента, так что добавим его к `BP_ThirdPersonCharacter`, для тестов.
![414d91ac21f8cbd6a63a9b8a8a1fad6e.png](../images/414d91ac21f8cbd6a63a9b8a8a1fad6e.png)
Теперь добавим контейнер тегов в наш компонент. Это будет обычная переменная.
```cpp
//.h
#include "GameplayTagContainer.h" // Gameplay Tags
// UActorComponent
UPROPERTY(BlueprintReadWrite, EditAnywhere, Category = "Default", meta = (DisplayName = "Tags Container"))
FGameplayTagContainer TagsContainer;
```
![e3c6e4f92152373642f9d7fbc818d57d.png](../images/e3c6e4f92152373642f9d7fbc818d57d.png)
После компиляции переменная контейнера тегов `TagsContainer` будет доступна в `bluerpint` коде.
Доступность контейнера для чтения и записи через редактор откроет в будущем прекрасные возможности для отладки. Ведь мы в любой момент сможем посмотреть - какие состояния и значения записаны в компонент тегов, просто открыв этот компонент в редакторе. Это можно делать прямо во время отладки запущенного проекта.
![2e53ea016a948dc77e019bee47490f3c.png](../images/2e53ea016a948dc77e019bee47490f3c.png)
Теперь требуется создать функционал по добавлению и удалению тегов из компонента, а так-же по генерации соответствующих событий.
Итоговый компонент выглядит так:
```cpp
DECLARE_DYNAMIC_MULTICAST_DELEGATE_OneParam(FTagAddedDelegate, FGameplayTag, Tag);
DECLARE_DYNAMIC_MULTICAST_DELEGATE_TwoParams(FTagRemovedDelegate, FGameplayTag, Tag, bool, bIsRemoved);
UCLASS( ClassGroup=(Custom), meta=(BlueprintSpawnableComponent, DisplayName = "Tags Component"))
class TAGSMODULE_API UTagsComponent : public UActorComponent
{
	GENERATED_BODY()
public:
	// Sets default values for this component's properties
	UTagsComponent();
	// Getter for Tags Container
	UFUNCTION(BlueprintCallable, BlueprintPure, Category = "Tags Component|Container")
	FGameplayTagContainer GetTagsContainer() const {
		return TagsContainer;
	}
	// Adding Tag To Container
	UFUNCTION(BlueprintCallable, Category = "Tags Component")
	bool AddTagToContainer(const FGameplayTag& Tag) {
		if (!ContainerHasTag(Tag, true)) { // add only non-existing
			TagsContainer.AddTag(Tag);
			OnTagAdded.Broadcast(Tag); // Call tag added Event
			return true;
		}
		return false;
	}
	// Merge two Containers
	UFUNCTION(BlueprintCallable, Category = "Tags Component")
	void MergeContainers(const FGameplayTagContainer& inTagsContainer) {
		for (FGameplayTag Tag : inTagsContainer) {
			AddTagToContainer(Tag);
		}
	}
	// Remove Tag from Container
	UFUNCTION(BlueprintCallable, Category = "Tags Component")
	bool RemoveTagFromContainer(const FGameplayTag& Tag) {
		bool bRes = TagsContainer.RemoveTag(Tag);
		OnTagRemoved.Broadcast(Tag, bRes); // Call tag removed Event
		return bRes;
	}
	// Remove Tags from Container
	UFUNCTION(BlueprintCallable, Category = "Tags Component")
	void RemoveTagsFromContainer(const FGameplayTagContainer& inTagsContainer) {
		//TagsContainer.RemoveTags(inTagsContainer);
		for (FGameplayTag Tag : inTagsContainer) { // since we use delegates, go thru loop
			RemoveTagFromContainer(Tag);
		}
	}
	// Returns true if component container has any tags that match specified container
	// if Exact = true will also compare tag sizes e.g: this.tag == this.tag => true and this.tag == this => false
	UFUNCTION(BlueprintCallable, BlueprintPure, Category = "Tags Component|Container")
	bool ContainerHasAnyTags(const FGameplayTagContainer& inTagsContainer, bool Exact = false) const {
		if (Exact) {
			return TagsContainer.HasAnyExact(inTagsContainer);
		}
		return TagsContainer.HasAny(inTagsContainer);
	}
	// Returns true if component container has all tags from specified container
	// if Exact = true will also compare tag sizes e.g: this.tag == this.tag => true and this.tag == this => false
	UFUNCTION(BlueprintCallable, BlueprintPure, Category = "Tags Component|Container")
	bool ContainerHasAllTags(const FGameplayTagContainer& inTagsContainer, bool Exact = false) const {
		if (Exact) {
			return TagsContainer.HasAllExact(inTagsContainer);
		}
		return TagsContainer.HasAll(inTagsContainer);
	}
	// Search in container for tag
	// if Exact = true will also compare tag sizes e.g: this.tag == this.tag => true and this.tag == this => false
	UFUNCTION(BlueprintCallable, BlueprintPure, Category = "Tags Component|Container")
	bool ContainerHasTag(const FGameplayTag& Tag, bool Exact = false) const {
		if (Exact) {
			return TagsContainer.HasTagExact(Tag);
		}
		return TagsContainer.HasTag(Tag);
	}
	// On Tag Added Event
	UPROPERTY(BlueprintAssignable, Category = "Delegates")
	FTagAddedDelegate OnTagAdded;
	// On Tag Removed Event
	UPROPERTY(BlueprintAssignable, Category = "Delegates")
	FTagRemovedDelegate OnTagRemoved;
	// Core Tags Container
	UPROPERTY(BlueprintReadWrite, EditAnywhere, Category = "Default", meta = (DisplayName = "Tags Container"))
	FGameplayTagContainer TagsContainer;
protected:
	// Called when the game starts
	virtual void BeginPlay() override;
public:
	// Called every frame
	virtual void TickComponent(float DeltaTime, ELevelTick TickType, FActorComponentTickFunction* ThisTickFunction) override;
};
```
Данный компонент выводит в `blueprint` пространство целый набор интерфейсов для взаимодействия с вложенном контейнером тегов `TagsContainer`. Так-же тут происходит генерация событий через два делегата. Одно событие для удаления тега, второе для добавления.
![8c2ebb09b73a25147d432cd513153c4d.png](../images/8c2ebb09b73a25147d432cd513153c4d.png)
![f2cb288a5d8889cac8ee5cd22a6f990d.png](../images/f2cb288a5d8889cac8ee5cd22a6f990d.png)
## Упрощем доступ к методам компонента используя библиотеку функций
Для использования методов компонента, очевидно, всегда придется брать ссылку на сам компонент.
![fa0e84bee94cc3a2490f1a6a7df4cb7d.png](../images/fa0e84bee94cc3a2490f1a6a7df4cb7d.png)
Это не всегда удобно, было бы не плохо вывести эти функции в библиотеку функций, которая автоматизировала бы за нас всю работу.
Используя `WorldContext` можно провернуть весьма любопытный трюк, который откроет прямой доступ к компоненту тегов(если он добавлен) как из классов `AActor` так и из классов `UActorComponent`.
Для начала я написал функцию получения "владельца" из `WorldContext`.
```cpp
// helper functions
// search for owner that contains components
static inline AActor* GetOwner(UObject * Context){
    AActor* Owner = Cast<AActor>(Context);
    if (Owner == nullptr) {
        UActorComponent* OwningComponent = Cast<UActorComponent>(Context);
        if (OwningComponent == nullptr) return nullptr; // node called not in actor and not in actor component
        Owner = OwningComponent->GetOwner();
    }
    return Owner;
}
```
Далее в каждой из функций библиотеки я использовал `meta = (WorldContext = "WorldContextObject")`. Что такое `WorldContext`? Это ссылка на объект, который вызывает `blueprint` ноду функции. Если мы вызываем функцию библиотеки из, например, персонажа то `WorldContext` будет ссылаться на персонажа. Если мы вызывам функцию из компонента, то `WorldContext` будет ссылаться на компонент.
```cpp
UFUNCTION(BlueprintCallable, BlueprintPure, Category = "Tags Library", meta = (WorldContext = "WorldContextObject"))
static UTagsComponent* GetTagsComponent(UObject* WorldContextObject) {
    AActor* Owner = UTagsLibrary::GetOwner(WorldContextObject);
    if(Owner == nullptr) return nullptr;
    return Owner->FindComponentByClass<UTagsComponent>();
}
// Getter for Tags Container
UFUNCTION(BlueprintCallable, BlueprintPure, Category = "Tags Library", meta = (WorldContext = "WorldContextObject"))
static FGameplayTagContainer GetTagsContainer(UObject* WorldContextObject){
    UTagsComponent* TC = UTagsLibrary::GetTagsComponent(WorldContextObject);
    if (TC != nullptr) {
        return TC->GetTagsContainer();
    }
    return FGameplayTagContainer();
}
```
Я использовал этот подход для начала написав функцию `GetTagsComponent` которая ищет компонент тегов в `WorldContextObject`. А потом функцию, которая ищет контейнер тегов в компоненте тегов, который находится в `WorldContextObject`.
![7d33044e70c9952b46910ad00ed081ee.png](../images/7d33044e70c9952b46910ad00ed081ee.png)
Обратите внимание на то, какие чистые получились ноды у функций. При этом результат они возвращают корректный.
![d8e85c3ff415061749a77a7e1a2fbaf3.png](../images/d8e85c3ff415061749a77a7e1a2fbaf3.png)
Этот-же код будет работать и внутри компонентов.
![33895ba52ca7386017264482bc3fcaaf.png](../images/33895ba52ca7386017264482bc3fcaaf.png)
![a8c1653c246d5e828e74dcf2554af112.png](../images/a8c1653c246d5e828e74dcf2554af112.png)
![30f536fcaff991b4502c6cbe54f6d9ab.png](../images/30f536fcaff991b4502c6cbe54f6d9ab.png)
![0e5ef932cdeedc08992d2bfa68799fe2.png](../images/0e5ef932cdeedc08992d2bfa68799fe2.png)
Используя этот подход перенесем все функции из компонента в библиотеку функций.
```cpp
#include "CoreMinimal.h"
#include "Kismet/BlueprintFunctionLibrary.h"
#include "GameplayTagContainer.h" // Gameplay Tags
#include "TagsComponent.h" // Tags Component
#include "TagsLibrary.generated.h"
/**
 *
 */
UCLASS()
class TAGSMODULE_API UTagsLibrary : public UBlueprintFunctionLibrary
{
	GENERATED_BODY()
public:
	// Getter for Tags Component
	UFUNCTION(BlueprintCallable, BlueprintPure, Category = "Tags Library", meta = (WorldContext = "WorldContextObject"))
	static UTagsComponent* GetTagsComponent(UObject* WorldContextObject) {
		AActor* Owner = UTagsLibrary::GetOwner(WorldContextObject);
		if(Owner == nullptr) return nullptr;
		return Owner->FindComponentByClass<UTagsComponent>();
	}
	// Getter for Tags Container
	UFUNCTION(BlueprintCallable, BlueprintPure, Category = "Tags Library", meta = (WorldContext = "WorldContextObject"))
	static FGameplayTagContainer GetTagsContainer(UObject* WorldContextObject){
		UTagsComponent* TC = UTagsLibrary::GetTagsComponent(WorldContextObject);
		if (TC != nullptr) {
			return TC->GetTagsContainer();
		}
		return FGameplayTagContainer();
	}
	// Adding Tag To Container
	UFUNCTION(BlueprintCallable, Category = "Tags Component", meta = (WorldContext = "WorldContextObject"))
	static bool AddTagToContainer(UObject* WorldContextObject, const FGameplayTag& Tag) {
		UTagsComponent* TC = UTagsLibrary::GetTagsComponent(WorldContextObject);
		if (TC != nullptr) {
			return TC->AddTagToContainer(Tag);
		}
		return false;
	}
	// Merge tags container with tag component container
	UFUNCTION(BlueprintCallable, Category = "Tags Component", meta = (WorldContext = "WorldContextObject"))
	static void MergeContainers(UObject* WorldContextObject, const FGameplayTagContainer& inTagsContainer) {
		UTagsComponent* TC = UTagsLibrary::GetTagsComponent(WorldContextObject);
		if (TC != nullptr) {
			TC->MergeContainers(inTagsContainer);
		}
	}
	// remove tag from component container
	UFUNCTION(BlueprintCallable, Category = "Tags Component", meta = (WorldContext = "WorldContextObject"))
	static bool RemoveTagFromContainer(UObject* WorldContextObject, const FGameplayTag& Tag) {
		UTagsComponent* TC = UTagsLibrary::GetTagsComponent(WorldContextObject);
		if (TC != nullptr) {
			return TC->RemoveTagFromContainer(Tag);
		}
		return false;
	}
	// remove tags sequence from tags container in tags component
	UFUNCTION(BlueprintCallable, Category = "Tags Component", meta = (WorldContext = "WorldContextObject"))
	static void RemoveTagsFromContainer(UObject* WorldContextObject, FGameplayTagContainer& inTagsContainer) {
		UTagsComponent* TC = UTagsLibrary::GetTagsComponent(WorldContextObject);
		if (TC != nullptr) {
			TC->RemoveTagsFromContainer(inTagsContainer);
		}
	}
	// Returns true if component tag container has any tags that match specified container
	// if Exact = true will also compare tag sizes e.g: this.tag == this.tag => true and this.tag == this => false
	UFUNCTION(BlueprintCallable, Category = "Tags Component", meta = (WorldContext = "WorldContextObject"))
	static bool TagsContainerHasAny(UObject* WorldContextObject, const FGameplayTagContainer& inTagsContainer, bool Exact = false) {
		UTagsComponent* TC = UTagsLibrary::GetTagsComponent(WorldContextObject);
		if (TC != nullptr) {
			return TC->ContainerHasAnyTags(inTagsContainer, Exact);
		}
		return false;
	}
	// Returns true if component container has all tags from specified container
	// if Exact = true will also compare tag sizes e.g: this.tag == this.tag => true and this.tag == this => false
	UFUNCTION(BlueprintCallable, Category = "Tags Component", meta = (WorldContext = "WorldContextObject"))
	static bool TagsContainerHasAll(UObject* WorldContextObject, const FGameplayTagContainer& inTagsContainer, bool Exact = false) {
		UTagsComponent* TC = UTagsLibrary::GetTagsComponent(WorldContextObject);
		if (TC != nullptr) {
			return TC->ContainerHasAllTags(inTagsContainer, Exact);
		}
		return false;
	}
	// Search in container for tag
	// if Exact = true will also compare tag sizes e.g: this.tag == this.tag => true and this.tag == this => false
	UFUNCTION(BlueprintCallable, Category = "Tags Component", meta = (WorldContext = "WorldContextObject"))
	static bool TagsContainerHas(UObject* WorldContextObject, const FGameplayTag& Tag, bool Exact = false) {
		UTagsComponent* TC = UTagsLibrary::GetTagsComponent(WorldContextObject);
		if (TC != nullptr) {
			return TC->ContainerHasTag(Tag, Exact);
		}
		return false;
	}
private:
	// helper functions
	// search for owner that contains components
	static inline AActor* GetOwner(UObject * Context){
		AActor* Owner = Cast<AActor>(Context);
		if (Owner == nullptr) {
			UActorComponent* OwningComponent = Cast<UActorComponent>(Context);
			if (OwningComponent == nullptr) return nullptr; // node called not in actor and not in actor component
			Owner = OwningComponent->GetOwner();
		}
		return Owner;
	}
};
```
![7e4abe9a097dd1d57fdd1cb82ec963c9.png](../images/7e4abe9a097dd1d57fdd1cb82ec963c9.png)
![5cf36499b5d45493344c60143e2f871d.png](../images/5cf36499b5d45493344c60143e2f871d.png)
![0c1ce6a6740f566e982b13e29543c114.png](../images/0c1ce6a6740f566e982b13e29543c114.png)
## Привязываем `Async Action` к событиям компонента
Как узнать что тег был добавлен или удален? Через подключение к событиям. В примитивном виде это выглядит так:
![cf92fa00c08188fbfe485bcff1d49955.png](../images/cf92fa00c08188fbfe485bcff1d49955.png)
![717aad22bca00486690535781ed27d26.png](../images/717aad22bca00486690535781ed27d26.png)
Однако, такой набор `blueprint` узлов очень неудобен. К тому-же подобные привязки крайне тяжело встраивать в сложную логику.
Создадим для действий `OnTagRemoved` и `OnTagAdded` ожидающие действия `Async Action`.
Это сильно упростит работу с компонентом и по сути поднимет его до уровня полноценной игровой системы. Поскольку мы планируем подключаться к диспетчерам событий - использовать `Latent Action` не получится. Нужен `Async Action`, то есть ожидающее действие, которое будет отслеживать вызов делегата.
Идем `Tools -> New C++ Class` и ищем `Cancellable Async Action`.
![746ef29e4b9a19540c092a31ba380d0e.png](../images/746ef29e4b9a19540c092a31ba380d0e.png)
Называем `TagsCompCancellableAsyncAction`.
![7a3b81f4e3f5a2e2a41621d068478208.png](../images/7a3b81f4e3f5a2e2a41621d068478208.png)
После добавления файла не забудьте выполнить команду `Tools -> Refresh Visual Studio 2022 Project`.
![93030711e8a4cd41322e898faa962cb1.png](../images/93030711e8a4cd41322e898faa962cb1.png)
Реализация действий будет тривиальна. Нам даже не придется переопределять метод `Cancel`, потому что он и так делает по умолчанию то, что от него требуется - отвязывает события.
### `OnTagRemoved AsyncAction`
Реализация асинхронного действия для события `OnTagRemoved` выглядит так:
```cpp
#include "CoreMinimal.h"
#include "Engine/CancellableAsyncAction.h"
#include "GameplayTagContainer.h" // Gameplay Tags
#include "TagsComponent.h" // Tags Component
#include "TagsLibrary.h" // Tags Function Library
// generated.h
#include "TagsCompCancellableAsyncAction.generated.h"
DECLARE_DYNAMIC_MULTICAST_DELEGATE_TwoParams(FTagRemovedPin, FGameplayTag, Tag, bool, bIsRemoved);
/**
 * Tags Component Tag Remove binding
 */
UCLASS()
class TAGSMODULE_API UTagRemovedCancellableAsyncAction : public UCancellableAsyncAction
{
	GENERATED_BODY()
	// world context
	UObject* WorldContextObject;
	// search for owner that contains components
	inline AActor* GetNodeOwner() {
		AActor* Owner = Cast<AActor>(WorldContextObject);
		if (Owner == nullptr) {
			UActorComponent* OwningComponent = Cast<UActorComponent>(WorldContextObject);
			if (OwningComponent == nullptr) return nullptr; // node called not in actor and not in actor component
			Owner = OwningComponent->GetOwner();
		}
		return Owner;
	}
public:
	UPROPERTY(BlueprintAssignable)
	FTagRemovedPin TagRemoved;
	// Main Logics go here
	virtual void Activate() override {
		AActor* Owner = GetNodeOwner();
		if (Owner == nullptr) return;
		UTagsComponent* TC = Owner->FindComponentByClass<UTagsComponent>();
		//ensure(TC != nullptr);
		if(TC == nullptr) return;
		FScriptDelegate Delegate;
		Delegate.BindUFunction(this, "OnRemovedCallback");
		TC->OnTagRemoved.Add(Delegate);
	}
	// Callback for delegate
	UFUNCTION(BlueprintCallable)
	void OnRemovedCallback(FGameplayTag Tag, bool bIsRemoved) {
		TagRemoved.Broadcast(Tag, bIsRemoved); // Activate pin
	}
	// Node with pin that activates once tag is removed from component
	UFUNCTION(BlueprintCallable, Category = "Tags Component", meta = (BlueprintInternalUseOnly = "true", WorldContext = "WorldContextObject"))
	static UTagRemovedCancellableAsyncAction* OnceTagRemoved(UObject* WorldContextObject) {
		UTagRemovedCancellableAsyncAction* BlueprintNode = NewObject<UTagRemovedCancellableAsyncAction>(); // construct async action
		// Set internal class variables. Can't be done otherwise since function is static.
		BlueprintNode->WorldContextObject = WorldContextObject;
		return BlueprintNode;
	}
};
```
![56d3039fc11c91973c81193d74181bb1.png](../images/56d3039fc11c91973c81193d74181bb1.png)
![1220571f3e1bc721068ad8ebe7e69111.png](../images/1220571f3e1bc721068ad8ebe7e69111.png)
### `OnTagAdded AsyncAction`
Сделаем аналогичное действие для `OnTagAdded`.
Поскольку у действий разное количество аргументов - необходимо создать класс асинхронного действия. Я не буду делать для этого отдельный файл, использую этот-же.
```cpp
#include "CoreMinimal.h"
#include "Engine/CancellableAsyncAction.h"
#include "GameplayTagContainer.h" // Gameplay Tags
#include "TagsComponent.h" // Tags Component
#include "TagsLibrary.h" // Tags Function Library
// generated.h
#include "TagsCompCancellableAsyncAction.generated.h"
// On Tag Added pin
DECLARE_DYNAMIC_MULTICAST_DELEGATE_OneParam(FTagAddedPin, FGameplayTag, Tag);
/**
 * Tags Component Tag Add binding
 */
UCLASS()
class TAGSMODULE_API UTagAddedCancellableAsyncAction : public UCancellableAsyncAction
{
	GENERATED_BODY()
		// world context
		UObject* WorldContextObject;
	// search for owner that contains components
	inline AActor* GetNodeOwner() {
		AActor* Owner = Cast<AActor>(WorldContextObject);
		if (Owner == nullptr) {
			UActorComponent* OwningComponent = Cast<UActorComponent>(WorldContextObject);
			if (OwningComponent == nullptr) return nullptr; // node called not in actor and not in actor component
			Owner = OwningComponent->GetOwner();
		}
		return Owner;
	}
public:
	UPROPERTY(BlueprintAssignable)
	FTagAddedPin TagAdded;
	// Main Logics go here
	virtual void Activate() override {
		AActor* Owner = GetNodeOwner();
		if (Owner == nullptr){
			//UE_LOG(LogTemp, Warning, TEXT("[tags] Onwer is NULL! "))
			return;
		}
		UTagsComponent* TC = Owner->FindComponentByClass<UTagsComponent>();
		//ensure(TC != nullptr);
		if (TC == nullptr){
			return;
		}
		FScriptDelegate Delegate;
		Delegate.BindUFunction(this, "OnTagAddedCallback");
		TC->OnTagAdded.Add(Delegate);
	}
	// Callback for delegate
	UFUNCTION(BlueprintCallable)
	void OnTagAddedCallback(FGameplayTag Tag) {
		//UE_LOG(LogTemp, Warning, TEXT("[tags] Tag Added"))
		TagAdded.Broadcast(Tag); // Activate pin
	}
	// Node with pin that activates once tag is removed from component
	UFUNCTION(BlueprintCallable, Category = "Tags Component", meta = (BlueprintInternalUseOnly = "true", WorldContext = "WorldContextObject"))
	static UTagAddedCancellableAsyncAction* OnceTagAdded(UObject* WorldContextObject) {
		UTagAddedCancellableAsyncAction* BlueprintNode = NewObject<UTagAddedCancellableAsyncAction>(); // construct async action
		// Set internal class variables. Can't be done otherwise since function is static.
		BlueprintNode->WorldContextObject = WorldContextObject;
		return BlueprintNode;
	}
};
```
![ee78654f913f1309f201877c13b3e423.png](../images/ee78654f913f1309f201877c13b3e423.png)
![bbc3cf4c577279a022ac72356a2878f5.png](../images/bbc3cf4c577279a022ac72356a2878f5.png)
### Тестирование
Использовать ноды `OnceTagAdded` и `OnceTagRemoved` можно как из класса персонажа или любого `AActor'а`, так и из классов `UActorComponent`, которые подключены к персонажу. При этом достаточным условием, чтобы эти ноды работали, является наличие компонента тегов.
![6e2e6e6d8e45c0945a1ba0b2c83205ba.png](../images/6e2e6e6d8e45c0945a1ba0b2c83205ba.png)
![e4dc7011c78b11691172190f4e62e1ff.png](../images/e4dc7011c78b11691172190f4e62e1ff.png)
## Добавление тегов из `c++`
Предположим вы делаете модуль с системой передвижения. При этом вы бы хотели, чтобы теги, используемые в этом модуле, сразу активировались на стороне `blueprint` редактора, без ручного добавления их через меню настроек. О том, как это сделать - поговорим в этой главе.
### `RequestGameplayTag`
Самый простой способ заставить тег появиться в проекте - это использовать `FGameplayTag::RequestGameplayTag`.
```cpp
//.h
// Gameplay tag
UPROPERTY(EditAnywhere, Category = "GameplayTags")
FGameplayTag TagTest;
//...
//.cpp
// in constructor
TagTest = FGameplayTag::RequestGameplayTag(FName("Locomotion.Grounded.Jog"));
```
![daafd338ef4c06f29e32be53420deb91.png](../images/daafd338ef4c06f29e32be53420deb91.png)
Этим способом можно создавать теги как в теле класса, так и внутри функций.
Недостаток у этой функции один - она обязательно должна быть вызвана или в теле класса или в конструкторе. Если этого не произойдет - тег не добавится.
### Macro Tags
Если нужны перманентно присутствующие теги, то есть макросы.
Макросы, отвечающие за теги, можно найти в файле `NativeGameplayTags.h` по пути `\Runtime\GameplayTags\Public\NativeGameplayTags.h`.
![431969a3399ccfbe681a4c81bb9a1cf8.png](../images/431969a3399ccfbe681a4c81bb9a1cf8.png)
Давайте рассмотрим их код.
```cpp
/**
 * Declares a native gameplay tag that is defined in a cpp with UE_DEFINE_GAMEPLAY_TAG to allow other modules or code to use the created tag variable.
 */
#define UE_DECLARE_GAMEPLAY_TAG_EXTERN(TagName) extern FNativeGameplayTag TagName;
/**
 * Defines a native gameplay tag that is externally declared in a header to allow other modules or code to use the created tag variable.
 */
#define UE_DEFINE_GAMEPLAY_TAG(TagName, Tag) FNativeGameplayTag TagName(UE_PLUGIN_NAME, UE_MODULE_NAME, Tag, TEXT(""), ENativeGameplayTagToken::PRIVATE_USE_MACRO_INSTEAD); static_assert(UE::GameplayTags::Private::HasFileExtension(__FILE__, ".cpp"), "UE_DEFINE_GAMEPLAY_TAG can only be used in .cpp files, if you're trying to share tags across modules, use UE_DECLARE_GAMEPLAY_TAG_EXTERN in the public header, and UE_DEFINE_GAMEPLAY_TAG in the private .cpp");
/**
 * Defines a native gameplay tag such that it's only available to the cpp file you define it in.
 */
#define UE_DEFINE_GAMEPLAY_TAG_STATIC(TagName, Tag) static FNativeGameplayTag TagName(UE_PLUGIN_NAME, UE_MODULE_NAME, Tag, TEXT(""), ENativeGameplayTagToken::PRIVATE_USE_MACRO_INSTEAD); static_assert(UE::GameplayTags::Private::HasFileExtension(__FILE__, ".cpp"), "UE_DEFINE_GAMEPLAY_TAG_STATIC can only be used in .cpp files, if you're trying to share tags across modules, use UE_DECLARE_GAMEPLAY_TAG_EXTERN in the public header, and UE_DEFINE_GAMEPLAY_TAG in the private .cpp");
```
#### `UE_DEFINE_GAMEPLAY_TAG_STATIC`
Рассмотрим для начала самый простой макрос - `UE_DEFINE_GAMEPLAY_TAG_STATIC`.
Он используется только в `.cpp` файле и позволяет на ходу задавать и использовать макросы тегов.
```cpp
//.h
// Gameplay Tags
#include "GameplayTagContainer.h"
#include "BlueprintGameplayTagLibrary.h"
#include "NativeGameplayTags.h"
//...
//.cpp
// gameplay tags
UE_DEFINE_GAMEPLAY_TAG_STATIC(TAG_FROZEN, "State.Frozen");
UE_DEFINE_GAMEPLAY_TAG_STATIC(TAG_STUN, "State.Stun");
// will add gen.cpp
#include UE_INLINE_GENERATED_CPP_BY_NAME(K2Node_Test)
// now we can use TAG_FROZEN and TAG_STUN within this .cpp file
```
#### `UE_DECLARE_GAMEPLAY_TAG_EXTERN` и `UE_DEFINE_GAMEPLAY_TAG`
Следующие макросы служат для добавления глобально доступных игровых тегов, это: `UE_DECLARE_GAMEPLAY_TAG_EXTERN` и `UE_DEFINE_GAMEPLAY_TAG`.
Макрос `UE_DECLARE_GAMEPLAY_TAG_EXTERN` должен быть указан в заголовочном файле (`.h`), например, библиотеки функций.
```cpp
//.h file NativeTags/Tags.h
#pragma once
#include "NativeGameplayTags.h" // Gameplay Tags Native Macros
UE_DECLARE_GAMEPLAY_TAG_EXTERN(TAG_IS_ARMED)
UE_DECLARE_GAMEPLAY_TAG_EXTERN(TAG_WEARING_SWORD)
UE_DECLARE_GAMEPLAY_TAG_EXTERN(TAG_WEARING_BOW)
```
Макрос `UE_DEFINE_GAMEPLAY_TAG` должен быть указан в файле реализации (`.cpp`).
```cpp
//.cpp file NativeTags/Tags.cpp
#include "NativeTags/Tags.h"
UE_DEFINE_GAMEPLAY_TAG(TAG_IS_ARMED, "Combat.IsArmed");
UE_DEFINE_GAMEPLAY_TAG(TAG_WEARING_SWORD, "Equipment.Wearing.Sword");
UE_DEFINE_GAMEPLAY_TAG(TAG_WEARING_BOW, "Equipment.Wearing.Bow");
```
Давайте создадим отдельный набор файлов под макросы тегов. Чтобы идентификаторы тегов легко можно было подключить в любом проекте.
Добавляем в модуль `TagsModule/Public` подпапку `NativeTags`.
![6af47defaed0608893ac2d0db037fff7.png](../images/6af47defaed0608893ac2d0db037fff7.png)
В эту папку добавляем файл - `Tags.h`.
![22175aed7a4f1de9249fc7ef49c9d92f.png](../images/22175aed7a4f1de9249fc7ef49c9d92f.png)
Теперь идем в папку `TagsModule/Private` и добавляем туда подпапку `NativeTags`.
![3d9a6cd478e1b0ef86f973d22229e708.png](../images/3d9a6cd478e1b0ef86f973d22229e708.png)
И добавляем туда файл -  `Tags.cpp`.
![f80c64eb19bc5f552d53e4f2d85de2bb.png](../images/f80c64eb19bc5f552d53e4f2d85de2bb.png)
Теперь нам надо, чтобы эти файлы появились в проекте. Идем в Unreal Engine - `Tools / Refresh Visual Studio 2022 Project`.
![933218517c0e744d6204fef1f60b133d.png](../images/933218517c0e744d6204fef1f60b133d.png)
Теперь добавляем туда приведенный выше код.
![115434a61b16d6351f93dae6ff0d2d58.png](../images/115434a61b16d6351f93dae6ff0d2d58.png)
![4e2b932a2621193a3ee9a02701cebf59.png](../images/4e2b932a2621193a3ee9a02701cebf59.png)
Если теперь просто скомпилировать проект - в нем появятся указанные теги. Переходим в `Tools -> Project Settings -> Gameplay Tags`.

> *Если теги  не появились - перезапустите движок.*

![3b9782053edbab573b86152a132bf273.png](../images/3b9782053edbab573b86152a132bf273.png)
К тому-же, теперь, просто подключив исполняемый файл `NativeTags/Tags.h` мы сможем использовать макросы тегов, заданные там, для программирования  собственной системы.
Важно помнить, что теги будут удалены из системы, если из нее будет удален модуль, который эти теги добавляет (особенно это актуально, если модуль находится в плагине).
## Meta `Categories`
Для системы `GameplayTags` существует собственный `meta` параметр, который работает только с параметрами типа `FGameplayTag`.
```cpp
// use "GameplayTags" in PublicDependencyModuleNames
// Gameplay Tags
#include <NativeGameplayTags.h>
//...
// Gameplay tag locked category
UPROPERTY(EditAnywhere, Category = "GameplayTags", meta = (Categories = "Locomotion.Grounded"))
FGameplayTag GroundedLocomotionMode;
// Gameplay tag
UPROPERTY(EditAnywhere, Category = "GameplayTags")
FGameplayTag TagTest;
// also can be used with function parameters like so:
// this will broadcast given tag to all listeners
UFUNCTION(BlueprintCallable, Category = "Operations", Meta=(EventTag="Events.All"))
void CallEvent(UPARAM(meta=(Categories = "Events")) FGameplayTag EventTag);
```
Используя такой параметр можно конкретизировать категорию тегов, которая будет доступна для устанвоки значений в переменной.
Переменная `GroundedLocomotionMode` у которой есть параметр `Category` выглядит так:
![d5c0ab67e6514e819941ee723669b8a2.png](../images/d5c0ab67e6514e819941ee723669b8a2.png)
Переменная `TagTest` у которой нет параметра `Category` выглядит так:
![df2b9260f1a9af5fdd4014604bb6036f.png](../images/df2b9260f1a9af5fdd4014604bb6036f.png)

## Получение списка "суб тегов", `UGameplayTagsManager`
В `blueprint` менеджер тегов `UGameplayTagsManager` не доступен. Но из `с++` обращаться к нему можно, например, для получения списка "под тегов", от заданного тега.
```cpp
// in function library
// list of child tags, wrapped to container
UFUNCTION(BlueprintCallable, Category = "Tags")
static FGameplayTagContainer GetChildTags(FGameplayTag BaseTag) {
	UGameplayTagsManager& Manager = UGameplayTagsManager::Get();

	FGameplayTagContainer Children = Manager.RequestGameplayTagChildren(BaseTag);
	return Children;
}

// array of child tags
UFUNCTION(BlueprintCallable, Category = "Tags")
static TArray<FGameplayTag> GetChildTagsArray(FGameplayTag BaseTag) {
	UGameplayTagsManager& Manager = UGameplayTagsManager::Get();

	FGameplayTagContainer Children = Manager.RequestGameplayTagChildren(BaseTag);
	
	TArray<FGameplayTag> Tags;
	Children.GetGameplayTagArray(Tags);

	return Tags;
}
```
Вызывать так:
![1231233wrwer435](../images/1231233wrwer435.png)

## Links
https://docs.unrealengine.com/4.26/en-US/ProgrammingAndScripting/Tags/
https://www.tomlooman.com/unreal-engine-gameplaytags-data-driven-design/
https://www.unrealdirective.com/tip/declare-define-native-gameplay-tags
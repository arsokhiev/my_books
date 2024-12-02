# Gameplay Ability System
`Gameplay Ability System` или `GAS` - это встроенный в `Unreal Engine` высокоуровневый фреймворк, упрощающий разработку игровой логики. На его основе разработчик может реализовать удобный функционал управления способностями, характеристиками и эффектами игровых объектов, в том числе - персонажей.
Все крупные `AAA` игры на `Unreal Engine` построены вокруг системы `GAS`. Среди таких: `Borderlands`, `Fortnite`, `PUBG`, `Tower of Fantasy` и  так далее.
![f6b716c32504b734f29211b81a03601b.png](../images/f6b716c32504b734f29211b81a03601b.png)
![01ad3ee952ff2e129fe560278fbf79f4.png](../images/01ad3ee952ff2e129fe560278fbf79f4.png)
![7e017f664bb744114ee19b6eee93c350.png](../images/7e017f664bb744114ee19b6eee93c350.png)
Основное, что должен уметь программист под `Unreal Engine` относится к установке и настройке системы `GAS`. Благо сделать это достаточно просто и пусть размер статьи вас не пугает. `GAS` мы установим и настроим уже во второй главе. Все остальное посвящено неочевидным моментам работы с системой.
Фреймворк `GAS` предлагает абсолютно уникальный опыт по совмещению `с++` и `blueprint` кода. Установить и настроить его корректно надо всего один раз. Достаточно лишь определиться с набором характеристик, которые будут использоваться в вашей игре, а так-же с клавишами ввода - и каркас вашей игры будет готов. Всю остальную логику вы сможете писать исключительно на `blueprint`.
Никакие плагины, никакие платные ассеты не заменят для вас полноценно установленную и настроенную систему `Gameplay Abilities`.
На `Guthub` есть отличный репозиторий с примером проекта для `GAS` - [GAS Documentation](https://github.com/tranek/GASDocumentation). Он немного устарел и относится к `Unreal Engine 5.1`, а так-же не поддерживает `Enhanced Input`. В этой статье я исправлю недочеты старого проекта и постараюсь сделать повествование максимально доступным даже для того, кто не читал всю мою книгу, а открыл только эту статью.
## Подготовка к работе
Мы будем работать с абсолютно новым проектом.
### Создаем новый проект
В `Epic Games Launcher` запускаем выбранный нами игровой движок. В моем случае это `Unreal Engine 5.1`.
![c39ecebb2e876a4f9ec2bcbbb6e7ef18.png](../images/c39ecebb2e876a4f9ec2bcbbb6e7ef18.png)
В появившемся окне переходим на вкладку `Games` и выбираем пункт `ThirdPerson`.
Называем будущий проект `GASBase`, выбираем место сохранения проекта. В настройках проекта выставляете `Blueprint` и галочку на `StarterContent`, он нам тоже понадобится.
Почему я создаю `Blueprint` проект? Потому что основные способности и эффекты, которые вы будете создавать для системы `GAS` реализованы в виде `Blueprint`. К тому-же из `blueprint` проекта в разы проще вести отладку игровой логики.
![114dc1b5f9237f20e950cbfb1730f877.png](../images/114dc1b5f9237f20e950cbfb1730f877.png)
### Включаем требуемые плагины
После создания и запуска, проекта перейдите на вкладку `Edit -> Plugins`.
Для работы с `Visual Studio` необходимо установить плагин `Visual Studio Integration Tools`. Скачайте его с [официального маркетплейса](https://www.unrealengine.com/marketplace/en-US/product/visual-studio-integration-tool), это бесплатный плагин.
Активируйте плагины `Visual Studio Integration` и `Visual Studio Integration Tools`.
![46fd48b54ccc4252a215552cf814835c.png](../images/46fd48b54ccc4252a215552cf814835c.png)
Фреймворк `GAS` реализован в плагине `Gameplay Abilities`, который поставляется вместе с `Unreal Engine`. Найти его можно в подкатегории `Gameplay`. Активируйте.
![b67514883be165b480947905942b5c1e.png](../images/b67514883be165b480947905942b5c1e.png)
Если у вас не включен плагин `EnhancedInput` - включите его.
![1dab3060bdce572efa5139b46d04ef2d.png](../images/1dab3060bdce572efa5139b46d04ef2d.png)
После активации плагинов - перезапускаем движок.
![6f95ff1896a71a6b342e30ccf2cbaf43.png](../images/6f95ff1896a71a6b342e30ccf2cbaf43.png)
### Включаем поддержку `c++`
Поскольку мы создали `Blueprint` проект - поддержку `с++` в нем придется включать вручную. Для этого необходимо добавить в проект любой `с++` класс из редактора.
Предлагаю создать `BlueprintFunctionLibrary`.
Идем в меню `Tools -> New C++ Class`, в появившемся окне выбираем пункт `BlueprintFunctionLibrary`.
![4a093624b6f27c85f3d82f126fb43f82.png](../images/4a093624b6f27c85f3d82f126fb43f82.png)
![439ba41ed61e26b4ffc4c42081441e83.png](../images/439ba41ed61e26b4ffc4c42081441e83.png)
Называем наш класс `CoreProjectFunctionLibrary`. Размещаем его в подпапке `Library`.
![e15d095e8f45ebc3a2c0e6a27769f1c5.png](../images/e15d095e8f45ebc3a2c0e6a27769f1c5.png)
После нажатия кнопки `CreateClass` движок откроет перед вами окно с вопросом, желаете ли вы запуска `Visual Studio`. Жмем - `No`.
![e952e0627683b5183c41ce15ad16b0f3.png](../images/e952e0627683b5183c41ce15ad16b0f3.png)
Закрываем движок.
Переоткрываем проект с пересборкой модулей. Для этого нажимаем на файл `GASBase.uproject` в папке проекта. После чего перед вами появится всплывающее окно, с вопросом - желаете ли вы пересобрать проект. Жмете `Да` / `Yes`.
![6afe809824005f20ce06b79a88034a66.png](../images/6afe809824005f20ce06b79a88034a66.png)
### Добавляем зависимости
Если все сделано правильно, то, после запуска движка, в меню `Tools` появятся новые пункты - `Refresh Visual Studio 2022 Project` и `Open Visual Studio 2022`.
![242d8da89a9e4eb027cefc93b0edea81.png](../images/242d8da89a9e4eb027cefc93b0edea81.png)
Выбираем `Open Visual Studio 2022`.
В `Solution Explorer` открывавем папку `Source -> GASBase`. Это главный модуль игры. Тут есть файл с расширением `.build.cs`, в моем случае он называется `GASBase.Build.cs`.
![07a73edb0da5b7c2dda0a249edf53a4b.png](../images/07a73edb0da5b7c2dda0a249edf53a4b.png)
В этом файле необходимо указать зависимости нашего проекта.
Система `GAS` рекомендует включать следующие зависимости: `GameplayAbilities`, `GameplayTags` и `GameplayTasks`. Я так-же добавлю к ним `InputCore`, `EnhancedInput`, `SlateCore` и `Slate`.
```cpp
using UnrealBuildTool;
public class GASBase : ModuleRules
{
	public GASBase(ReadOnlyTargetRules Target) : base(Target)
	{
		PCHUsage = PCHUsageMode.UseExplicitOrSharedPCHs;
		PublicDependencyModuleNames.AddRange(new string[] {
			"Core"
			, "CoreUObject"
			, "Engine"
			, "InputCore"
			, "EnhancedInput"
		});
		PrivateDependencyModuleNames.AddRange(new string[] {
            "GameplayAbilities"
			, "GameplayTags"
			, "GameplayTasks"
			, "SlateCore"
			, "Slate"
        });
	}
}
```
![4fd1d537f452be82d6784d7fcbb09cb5.png](../images/4fd1d537f452be82d6784d7fcbb09cb5.png)
Пересобираем проект с помощью `LiveCoding` сочетанием клавиш `Ctrl + Alt + F11`.
![9b3454b505c597226dee66e4459621ec.png](../images/9b3454b505c597226dee66e4459621ec.png)
Если все скомпилировалось успешно - мы готовы к работе.
## Добавляем `AttributeSet`
###  Введение в `AttributeSet`
`AttributeSet` это класс, в котором разработчик должен задать аттрибуты для работы с системой `GAS`. Про аттрибуты мы поговорим чуть ниже, в этой главе, а пока просто создадим класс и заполним его базовым набором инструкций.
### Создаем класс
Выполняем команду `Tools -> New C++ Class -> All Classes` ищем в списке `AttributeSet`.
![39c93a7bbeb3bc336b976fc659fd62ee.png](../images/39c93a7bbeb3bc336b976fc659fd62ee.png)
Называем новый класс `CharacterAttributeSet` и добавляем его в модуль `GASModule`, в папку `Attributes`.
![5974dde8c78f2d174b3fd6a856b77841.png](../images/5974dde8c78f2d174b3fd6a856b77841.png)
![602aa13ad7f1228d8f5a1005cadec033.png](../images/602aa13ad7f1228d8f5a1005cadec033.png)
Открываем файл `CharacterAttributeSet.h`.
### Добавляем макрос `ATTRIBUTE_ACCESSORS`
Каждый класс `AttributeSet` должен содержать макрос `ATTRIBUTE_ACCESSORS` со следующим определением:
```cpp
// Uses macros from AttributeSet.h
#define ATTRIBUTE_ACCESSORS(ClassName, PropertyName) \
	GAMEPLAYATTRIBUTE_PROPERTY_GETTER(ClassName, PropertyName) \
	GAMEPLAYATTRIBUTE_VALUE_GETTER(PropertyName) \
	GAMEPLAYATTRIBUTE_VALUE_SETTER(PropertyName) \
	GAMEPLAYATTRIBUTE_VALUE_INITTER(PropertyName)
```
Рекомендации по использованию данного макроса могут быть найдены в исходных файлах движка -  `\Engine\Plugins\Runtime\GameplayAbilities\Source\GameplayAbilities\Public\AttributeSet.h`.
![1c03e0b66f368b684d98bd315047085b.png](../images/1c03e0b66f368b684d98bd315047085b.png)
Добавляем макрос `ATTRIBUTE_ACCESSORS` в `CharacterAttributeSet`.
```cpp
//CharacterAttributeSet.h
#pragma once
#include "CoreMinimal.h"
#include "AttributeSet.h" // core
#include "Net/UnrealNetwork.h" // DOREPLIFETIME_CONDITION_NOTIFY macro
#include "GameplayEffectExtension.h"
// .generated.h
#include "CharacterAttributeSet.generated.h"
// Uses macros from AttributeSet.h
#define ATTRIBUTE_ACCESSORS(ClassName, PropertyName) \
	GAMEPLAYATTRIBUTE_PROPERTY_GETTER(ClassName, PropertyName) \
	GAMEPLAYATTRIBUTE_VALUE_GETTER(PropertyName) \
	GAMEPLAYATTRIBUTE_VALUE_SETTER(PropertyName) \
	GAMEPLAYATTRIBUTE_VALUE_INITTER(PropertyName)
/**
 * Character related attributes
 */
UCLASS()
class GASBASE_API UCharacterAttributeSet : public UAttributeSet
{
	GENERATED_BODY()
public:
    /* Attributes will be here */
};
```
Самое время добавить аттрибуты.
### Введение в Аттрибуты
Аттрибут - это переменная, которая используется разработчиком, для описания игровых характеристик персонажа. Аттрибутами являются: здоровье, выносливость, броня, скорость передвижения.
Пример аттрибута, отвечающего за здоровье персонажа:
```cpp
// Current Health
UPROPERTY(BlueprintReadOnly, Category = "Attributes", ReplicatedUsing = OnRep_Health)
FGameplayAttributeData Health;
ATTRIBUTE_ACCESSORS(UGameCharacterAttributeSet, Health)
```
Аттрибут описывается структурой `FGameplayAttributeData`. Данная структура может быть найдена в файле `\Engine\Plugins\Runtime\GameplayAbilities\Source\GameplayAbilities\Public\AttributeSet.h`.
![81eef4cce767fddafc5c700c74e2d9ea.png](../images/81eef4cce767fddafc5c700c74e2d9ea.png)
#### Шаги для добавления аттрибута
Рассмотрим формулу, по которой добавляется почти каждый аттрибут.
В коде используются следующие заголовочные файлы:
```cpp
#include "AttributeSet.h" // core
#include "Net/UnrealNetwork.h" // DOREPLIFETIME_CONDITION_NOTIFY macro
#include "GameplayEffectExtension.h"
#include "AbilitySystemComponent.h"
```
##### Шаг 1
Добавляем аттрибут.
```cpp
// Current Health
UPROPERTY(BlueprintReadOnly, Category = "Attributes", ReplicatedUsing = OnRep_Health)
FGameplayAttributeData Health;
ATTRIBUTE_ACCESSORS(UCharacterAttributeSet, Health)
```
##### Шаг 2
Задаем значение "по умолчанию" в конструкторе класса.
```cpp
//.h
// Set Attribute Default Values in Constructor
UCharacterAttributeSet();
//...
//.cpp
// Set Attribute Default Values in Constructor
UCharacterAttributeSet::UCharacterAttributeSet()
{
	Health = 100.f;
}
```
##### Шаг 3
Создаем метод сетевой репликации аттрибута. Это надо делать только для основных аттрибутов, например для здоровья. Если аттрибут влияет на другие аттрибуты, но сам не предоставляет дополнительной логики - например регенерация здоровья - делать для него репликацию не нужно.
```cpp
//.h
// Current Health
// make ReplicatedUsing = OnRep_Health
UPROPERTY(BlueprintReadOnly, Category = "Attributes", ReplicatedUsing = OnRep_Health)
FGameplayAttributeData Health;
ATTRIBUTE_ACCESSORS(UCharacterAttributeSet, Health)
 
// Attributes replication live time
virtual void GetLifetimeReplicatedProps(TArray<FLifetimeProperty>& OutLifetimeProps) const override;
// Health replication
UFUNCTION()
virtual void OnRep_Health(const FGameplayAttributeData& Old);
//...
//.cpp
// Attributes replication live time
void UCharacterAttributeSet::GetLifetimeReplicatedProps(TArray<FLifetimeProperty>& OutLifetimeProps) const
{
	Super::GetLifetimeReplicatedProps(OutLifetimeProps);
	// Health
    // do same for all attributes:
	DOREPLIFETIME_CONDITION_NOTIFY(UCharacterAttributeSet, Health, COND_None, REPNOTIFY_Always);
}
// Health Replication
// for all attributes this method has same pattern
void UCharacterAttributeSet::OnRep_Health(const FGameplayAttributeData& Old)
{
	GAMEPLAYATTRIBUTE_REPNOTIFY(UCharacterAttributeSet, Health, Old);
}
```
##### Шаг 4
Фиксируем изменение аттрибута в определенных рамках, чтобы избежать нарушения игровой логики.
```cpp
//.h
/**
 * BASE and CURRENT value attribute change
 * BASE attribute values are NOT Modified by the Gameplay Effects
 * CURRENT attribute value is BASE value +- Gameplay Effect modifiers
 */
/**
 * CURRENT attribute value change notifies :
 */
// Called just before any modification happens to an attribute. This is lower level than PreAttributeModify/PostAttribute modify.
virtual void PreAttributeChange(const FGameplayAttribute& Attribute, float& NewValue) override;
/**
 * BASE attribute value change notifies :
 */
// Called just before any modification happens to an attribute's base value when an attribute aggregator exists
virtual void PreAttributeBaseChange(const FGameplayAttribute& Attribute, float& NewValue) const override;
// Clamp attributes to keep their values in range
virtual void ClampAttributeOnChange(const FGameplayAttribute& AffectedAttribute, float& NewValue) const;
//...
//.cpp
// Called just before any modification happens to an attribute's base value when an attribute aggregator exists
void UCharacterAttributeSet::PreAttributeBaseChange(const FGameplayAttribute& Attribute, float& NewValue) const
{
	Super::PreAttributeBaseChange(Attribute, NewValue);
	ClampAttributeOnChange(Attribute, NewValue);
}
// Called just before any modification happens to an attribute. This is lower level than PreAttributeModify/PostAttribute modify.
void UCharacterAttributeSet::PreAttributeChange(const FGameplayAttribute& Attribute, float& NewValue)
{
	Super::PreAttributeChange(Attribute, NewValue);
	ClampAttributeOnChange(Attribute, NewValue);
}
// Clamp attributes to keep their values in range
void UCharacterAttributeSet::ClampAttributeOnChange(const FGameplayAttribute& AffectedAttribute, float& NewValue) const
{
	if (AffectedAttribute == GetHealthAttribute()) {
		NewValue = FMath::Clamp(NewValue, 0, GetMaxHealth());
	}
}
```
Эта последовательность шагов повторяется для почти каждого из создаваемых аттрибутов.
---
#### `BaseValue` и `CurrentValue`
Каждый аттрибут имеет два значения:
1. `BaseValue` - значение аттрибута, без модификаций.
2. `CurrentValue` - значение аттрибута, который модифицирован наложенными эффектами.
Предположим, есть аттрибут скорости передвижения персонажа - `Speed`.
Изначальное значение аттрибута равно:`Speed = 600`. Если наложить на персонажа эффект ускорения, увеличивающий `Speed` на `50` единиц, то внутри аттрибута мы буедем иметь следующие значения: `BaseValue = 600`, `CurrentValue = 650`.
Когда все эффекты снимаются с персонажа или их время действия истекает `CurrentValue` принимает значение, которое хранится в `BaseValue`.
#### Максимальные и минимальные значения атрибутов
Макисмальные и минимальные значения аттрибута должны задаваться в виде отдельных аттрибутов. Необходимость такого разделения связана с сетевой составляющей системы.
Если минимальным значением аттрибута всегда является `0`, а максимальным, например, `100` и менять эти значения вы не собираетесь в будущем - можете отдельные аттрибуты под эти значения не создавать.
Рассмотрим реализацию аттрибута здоровья.
Поскольку с повышением уровня персонажа предполагается увеличение его запаса здоровья, максимальное значение здоровья должно быть задано в виде отдельного аттрибута.
```cpp
// Current Health
UPROPERTY(BlueprintReadOnly, Category = "Attributes", ReplicatedUsing = OnRep_Health)
FGameplayAttributeData Health;
ATTRIBUTE_ACCESSORS(UGameCharacterAttributeSet, Health)
// MaxHealth is its own attribute since GameplayEffects may modify it
UPROPERTY(BlueprintReadOnly, Category = "Attributes", ReplicatedUsing = OnRep_MaxHealth)
FGameplayAttributeData MaxHealth;
ATTRIBUTE_ACCESSORS(UGameCharacterAttributeSet, MaxHealth)
```
Для фиксации значения аттрибута в границах от минимума до максимума используют вспомогательный метод `ClampAttributeOnChange` который выглядит так:
```cpp
// Clamp attributes to keep their values in range
void ClampAttributeOnChange(const FGameplayAttribute& AffectedAttribute, float& NewValue) const
{
	if (AffectedAttribute == GetHealthAttribute()) {
		NewValue = FMath::Clamp(NewValue, 0, GetMaxHealth());
	}
    /* add more if necessary */
}
```
Для каждого аттрибута, который необходимо фиксировать в определенных рамках, используется дополнительное условие внутри метода `ClampAttributeOnChange`.
Данный метод вызывается в `PreAttributeChange` и `PreAttributeBaseChange`. Это гарантирует, что аттрибут с наложенными на него эффектами и без эффектов - всегда останется в заданных границах.
```cpp
// Called just before any modification happens to an attribute. This is lower level than PreAttributeModify/PostAttribute modify.
void PreAttributeChange(const FGameplayAttribute& Attribute, float& NewValue)
{
	Super::PreAttributeChange(Attribute, NewValue);
	ClampAttributeOnChange(Attribute, NewValue);
}
// Called just before any modification happens to an attribute's base value when an attribute aggregator exists
void PreAttributeBaseChange(const FGameplayAttribute& Attribute, float& NewValue) const
{
	Super::PreAttributeBaseChange(Attribute, NewValue);
	ClampAttributeOnChange(Attribute, NewValue);
}
```
#### Репликация вторичных аттрибутов
Аттрибуты, которые не имеют сетевой репликации и используются для обновления других аттрибутов считаются "вторичными" аттрибутами. К примеру, таким аттрибутом является `HealthRegenRate`, скорость восстановления здоровья. Этому  аттрибуту не нужна сетевая репликация, потому что показатель здоровья, на который этот аттрибут влияет - и без того реплицируется по сети, а значит мы избавляемся от лишней нагрузки.
```cpp
// Health Regen Rate
UPROPERTY(BlueprintReadOnly, Category = "Attributes")
FGameplayAttributeData HealthRegenRate;
ATTRIBUTE_ACCESSORS(UCharacterAttributeSet, HealthRegenRate)
```
##### `PostGameplayEffectExecute`
Данный метод вызывается после применения всех модификаторов эффекта к `BaseValue` аттрибута.
Логика нанесения урона обычно выполняется с использованием соответствующих эффектов урона. В методе `PostGameplayEffectExecute` вы можете проверить - убил урон персонажа или нет.
Так-же, используя данный метод можно "аккумулировать" урон, нанесенный персонажу. Это бывает полезно в сетевых играх, где соединение не всегда достаточно надежное и время применения эффектов урона на игроках между клиентами - разное. В этом случае вы можете использовать стакающийся эффект урона, например такой:
![dbd42ca544120429d175f13d0a2af221.png](../images/dbd42ca544120429d175f13d0a2af221.png)
При стрельбе очередями в шутерах крайне накладно обрабатывать каждое попадание на сервере. Куда удобнее будет реализовать такой стакающийся эффект, который затем будет обработан через `PostGameplayEffectExecute`.
Тут мы немного забегаем вперед, но я все-же покажу как такой подход работает.
У нас есть нпц-цель.
![9a998f5f255cc38b2ed891a73c855119.png](../images/9a998f5f255cc38b2ed891a73c855119.png)
В лог печатается урон, который будет нанесен здоровью за текущий тик:
![7670d5ddfa47ecad649a55a382e70498.png](../images/7670d5ddfa47ecad649a55a382e70498.png)
При множественном наложении эффектов урона - их значение возрастает и они накладываются на здоровье все "разом".
Именно для такой `batched` обработки изменений от всех эффектов стоит использовать `PostGameplayEffectExecute`.
```cpp
//.h
/**
 * Gameplay Effects execution
 */
 // Called just before a GameplayEffect is executed to modify the base value of an attribute
virtual void PostGameplayEffectExecute(const struct FGameplayEffectModCallbackData& Data) override;
//...
//.cpp
// Called just before a GameplayEffect is executed to modify the base value of an attribute
void UCharacterAttributeSet::PostGameplayEffectExecute(const FGameplayEffectModCallbackData& Data)
{
	Super::PostGameplayEffectExecute(Data);
	FGameplayEffectContextHandle Context = Data.EffectSpec.GetContext();
	UAbilitySystemComponent* Source = Context.GetOriginalInstigatorAbilitySystemComponent();
	// Get the Target actor, which should be our owner
	AActor* TargetActor = nullptr;
	AController* TargetController = nullptr;
	UAbilitySystemComponent* TargetAbilitySystem = nullptr;
	if (Data.Target.AbilityActorInfo.IsValid() && Data.Target.AbilityActorInfo->AvatarActor.IsValid())
	{
		TargetActor = Data.Target.AbilityActorInfo->AvatarActor.Get();
		TargetAbilitySystem = Data.Target.AbilityActorInfo->AbilitySystemComponent.Get();
		TargetController = Data.Target.AbilityActorInfo->PlayerController.Get();
	}
	// Get the Source actor
	AActor* SourceActor = nullptr;
	AController* SourceController = nullptr;
	UAbilitySystemComponent* SourceAbilitySystem = nullptr;
	if (Source && Source->AbilityActorInfo.IsValid() && Source->AbilityActorInfo->AvatarActor.IsValid())
	{
		SourceActor = Source->AbilityActorInfo->AvatarActor.Get();
		SourceAbilitySystem = Source->AbilityActorInfo->AbilitySystemComponent.Get();
		SourceController = Source->AbilityActorInfo->PlayerController.Get();
	}
	// damage application
	if (Data.EvaluatedData.Attribute == GetHealthAttribute()
	&& Data.EvaluatedData.ModifierOp == EGameplayModOp::Additive // modifier with Add value set
	) {
		// get clamped damage to prevent very big damage numbers, just in case
		float LocalDamageDone = GetAppliedDamage();
		SetAppliedDamage(0.f); // clear damage attribute to prevent multiple applications
		// *note, damage can be - or +
		if((int)LocalDamageDone != 0){
			UE_LOG(LogTemp, Error, TEXT("LocalDamageDone = %f"), LocalDamageDone)
			// Apply the health change and then clamp it
			float NewValue = FMath::Clamp(GetHealth() - LocalDamageDone, 0.0f, GetMaxHealth());
			// update health
			SetHealth(NewValue);
		}
	}
}
```
### Код
#### `CharacterAttributeSet.h`
```cpp
#pragma once
#include "CoreMinimal.h"
#include "AttributeSet.h" // core
#include "Net/UnrealNetwork.h" // DOREPLIFETIME_CONDITION_NOTIFY macro
#include "GameplayEffectExtension.h"
#include "AbilitySystemComponent.h"
// .generated.h
#include "CharacterAttributeSet.generated.h"
// Uses macros from AttributeSet.h
#define ATTRIBUTE_ACCESSORS(ClassName, PropertyName) \
	GAMEPLAYATTRIBUTE_PROPERTY_GETTER(ClassName, PropertyName) \
	GAMEPLAYATTRIBUTE_VALUE_GETTER(PropertyName) \
	GAMEPLAYATTRIBUTE_VALUE_SETTER(PropertyName) \
	GAMEPLAYATTRIBUTE_VALUE_INITTER(PropertyName)
/**
 * Character related attributes
 */
UCLASS()
class GASBASE_API UCharacterAttributeSet : public UAttributeSet
{
	GENERATED_BODY()
public:
	// Set Attribute Default Values in Constructor
	UCharacterAttributeSet();
	// Current Health
	UPROPERTY(BlueprintReadOnly, Category = "Attributes", ReplicatedUsing = OnRep_Health)
	FGameplayAttributeData Health;
	ATTRIBUTE_ACCESSORS(UCharacterAttributeSet, Health)
	// MaxHealth is its own attribute since GameplayEffects may modify it
	UPROPERTY(BlueprintReadOnly, Category = "Attributes", ReplicatedUsing = OnRep_MaxHealth)
	FGameplayAttributeData MaxHealth;
	ATTRIBUTE_ACCESSORS(UCharacterAttributeSet, MaxHealth)
	// Health Regen Rate
	UPROPERTY(BlueprintReadOnly, Category = "Attributes")
	FGameplayAttributeData HealthRegenRate;
	ATTRIBUTE_ACCESSORS(UCharacterAttributeSet, HealthRegenRate)
	// Current stamina Capped by MaxStamina.
	UPROPERTY(BlueprintReadOnly, Category = "Attributes", ReplicatedUsing = OnRep_Stamina)
	FGameplayAttributeData Stamina;
	ATTRIBUTE_ACCESSORS(UCharacterAttributeSet, Stamina)
	// MaxStamina is its own attribute since GameplayEffects may modify it
	UPROPERTY(BlueprintReadOnly, Category = "Attributes", ReplicatedUsing = OnRep_MaxStamina)
	FGameplayAttributeData MaxStamina;
	ATTRIBUTE_ACCESSORS(UCharacterAttributeSet, MaxStamina)
	// Stamina Regen Rate
	UPROPERTY(BlueprintReadOnly, Category = "Attributes")
	FGameplayAttributeData StaminaRegenRate;
	ATTRIBUTE_ACCESSORS(UCharacterAttributeSet, StaminaRegenRate)
	// Current Mana, used to execute special abilities. Capped by MaxMana.
	UPROPERTY(BlueprintReadOnly, Category = "Attributes", ReplicatedUsing = OnRep_Mana)
	FGameplayAttributeData Mana;
	ATTRIBUTE_ACCESSORS(UCharacterAttributeSet, Mana)
	// MaxMana is its own attribute since GameplayEffects may modify it
	UPROPERTY(BlueprintReadOnly, Category = "Attributes", ReplicatedUsing = OnRep_MaxMana)
	FGameplayAttributeData MaxMana;
	ATTRIBUTE_ACCESSORS(UCharacterAttributeSet, MaxMana)
	// Mana Regen Rate
	UPROPERTY(BlueprintReadOnly, Category = "Attributes")
	FGameplayAttributeData ManaRegenRate;
	ATTRIBUTE_ACCESSORS(UCharacterAttributeSet, ManaRegenRate)
	// Current Armor
	UPROPERTY(BlueprintReadOnly, Category = "Attributes", ReplicatedUsing = OnRep_Armor)
	FGameplayAttributeData Armor;
	ATTRIBUTE_ACCESSORS(UCharacterAttributeSet, Armor)
	// Applied Damage to the character on current update
	UPROPERTY(BlueprintReadOnly, Category = "Attributes")
	FGameplayAttributeData AppliedDamage;
	ATTRIBUTE_ACCESSORS(UCharacterAttributeSet, AppliedDamage)
	// Current character's strength
	UPROPERTY(BlueprintReadOnly, Category = "Attributes")
	FGameplayAttributeData Strength;
	ATTRIBUTE_ACCESSORS(UCharacterAttributeSet, Strength)
	/**
	 * BASE and CURRENT value attribute change
	 * BASE attribute values are NOT Modified by the Gameplay Effects
	 * CURRENT attribute value is BASE value +- Gameplay Effect modifiers
	 */
	/**
	 * CURRENT attribute value change notifies :
	 */
	// Called just before any modification happens to an attribute. This is lower level than PreAttributeModify/PostAttribute modify.
	virtual void PreAttributeChange(const FGameplayAttribute& Attribute, float& NewValue) override;
	// Called just after any modification happens to an attribute.
	virtual void PostAttributeChange(const FGameplayAttribute& Attribute, float OldValue, float NewValue) override;
	/**
	 * BASE attribute value change notifies :
	 */
	// Called just before any modification happens to an attribute's base value when an attribute aggregator exists
	virtual void PreAttributeBaseChange(const FGameplayAttribute& Attribute, float& NewValue) const override;
	// Called just after any modification happens to an attribute's base value when an attribute aggregator exists
	virtual void PostAttributeBaseChange(const FGameplayAttribute& Attribute, float OldValue, float NewValue) const override;
	/**
	 * Gameplay Effects execution
	 */
	 // Called just before a GameplayEffect is executed to modify the base value of an attribute
	virtual void PostGameplayEffectExecute(const struct FGameplayEffectModCallbackData& Data) override;
	// Called just before modifying the value of an attribute.
	// AttributeSet can make additional modifications here. Return true to continue, or false to throw out the modification
	virtual bool PreGameplayEffectExecute(struct FGameplayEffectModCallbackData& Data) { return true; }
protected:
	// Clamp attributes to keep their values in range
	virtual void ClampAttributeOnChange(const FGameplayAttribute& AffectedAttribute, float& NewValue) const;
	// Replication
	// Attributes replication live time
	virtual void GetLifetimeReplicatedProps(TArray<FLifetimeProperty>& OutLifetimeProps) const override;
	UFUNCTION()
	virtual void OnRep_Health(const FGameplayAttributeData& Old);
	UFUNCTION()
	virtual void OnRep_MaxHealth(const FGameplayAttributeData& Old);
	UFUNCTION()
	virtual void OnRep_Stamina(const FGameplayAttributeData& Old);
	UFUNCTION()
	virtual void OnRep_MaxStamina(const FGameplayAttributeData& Old);
	UFUNCTION()
	virtual void OnRep_Mana(const FGameplayAttributeData& Old);
	UFUNCTION()
	virtual void OnRep_MaxMana(const FGameplayAttributeData& Old);
	UFUNCTION()
	virtual void OnRep_Armor(const FGameplayAttributeData& Old);
};
```
#### `CharacterAttributeSet.cpp`
```cpp
#include "Attributes/CharacterAttributeSet.h"
// Set Attribute Default Values in Constructor
UCharacterAttributeSet::UCharacterAttributeSet()
{
	Health = 100.f;
	MaxHealth = 100.f;
	HealthRegenRate = 0.f;
	Stamina = 100.f;
	MaxStamina = 100.f;
	StaminaRegenRate = 0.f;
	Mana = 100.f;
	MaxMana = 100.f;
	ManaRegenRate = 0.f;
	AppliedDamage = 0.f;
	Armor = 0.f;
}
/**
 * CURRENT attribute value change notifies :
 */
// Called just before any modification happens to an attribute. This is lower level than PreAttributeModify/PostAttribute modify.
void UCharacterAttributeSet::PreAttributeChange(const FGameplayAttribute& Attribute, float& NewValue)
{
	Super::PreAttributeChange(Attribute, NewValue);
	ClampAttributeOnChange(Attribute, NewValue);
}
// Called just after any modification happens to an attribute
void UCharacterAttributeSet::PostAttributeChange(const FGameplayAttribute& Attribute, float OldValue, float NewValue)
{
	Super::PostAttributeChange(Attribute, OldValue, NewValue);
}
/**
 * BASE attribute value change notifies :
 */
// Called just before any modification happens to an attribute's base value when an attribute aggregator exists
void UCharacterAttributeSet::PreAttributeBaseChange(const FGameplayAttribute& Attribute, float& NewValue) const
{
	Super::PreAttributeBaseChange(Attribute, NewValue);
	ClampAttributeOnChange(Attribute, NewValue);
}
// Called just after any modification happens to an attribute's base value when an attribute aggregator exists.
void UCharacterAttributeSet::PostAttributeBaseChange(const FGameplayAttribute& Attribute, float OldValue, float NewValue) const
{
	Super::PostAttributeBaseChange(Attribute, OldValue, NewValue);
}
/**
 * Gameplay Effects execution
 */
// Called just before a GameplayEffect is executed to modify the base value of an attribute
void UCharacterAttributeSet::PostGameplayEffectExecute(const FGameplayEffectModCallbackData& Data)
{
	Super::PostGameplayEffectExecute(Data);
	FGameplayEffectContextHandle Context = Data.EffectSpec.GetContext();
	UAbilitySystemComponent* Source = Context.GetOriginalInstigatorAbilitySystemComponent();
	// Get the Target actor, which should be our owner
	AActor* TargetActor = nullptr;
	AController* TargetController = nullptr;
	UAbilitySystemComponent* TargetAbilitySystem = nullptr;
	if (Data.Target.AbilityActorInfo.IsValid() && Data.Target.AbilityActorInfo->AvatarActor.IsValid())
	{
		TargetActor = Data.Target.AbilityActorInfo->AvatarActor.Get();
		TargetAbilitySystem = Data.Target.AbilityActorInfo->AbilitySystemComponent.Get();
		TargetController = Data.Target.AbilityActorInfo->PlayerController.Get();
	}
	// Get the Source actor
	AActor* SourceActor = nullptr;
	AController* SourceController = nullptr;
	UAbilitySystemComponent* SourceAbilitySystem = nullptr;
	if (Source && Source->AbilityActorInfo.IsValid() && Source->AbilityActorInfo->AvatarActor.IsValid())
	{
		SourceActor = Source->AbilityActorInfo->AvatarActor.Get();
		SourceAbilitySystem = Source->AbilityActorInfo->AbilitySystemComponent.Get();
		SourceController = Source->AbilityActorInfo->PlayerController.Get();
	}
	// damage application
	if (Data.EvaluatedData.Attribute == GetHealthAttribute()
	&& Data.EvaluatedData.ModifierOp == EGameplayModOp::Additive // modifier with Add value set
	) {
		// get clamped damage to prevent very big damage numbers, just in case
		float LocalDamageDone = GetAppliedDamage();
		SetAppliedDamage(0.f); // clear damage attribute to prevent multiple applications
		// *note, damage can be - or +
		if((int)LocalDamageDone != 0){
			UE_LOG(LogTemp, Error, TEXT("LocalDamageDone = %f"), LocalDamageDone)
			// Apply the health change and then clamp it
			float NewValue = FMath::Clamp(GetHealth() - LocalDamageDone, 0.0f, GetMaxHealth());
			// update health
			SetHealth(NewValue);
		}
	}
}
// Clamp attributes to keep their values in range
void UCharacterAttributeSet::ClampAttributeOnChange(const FGameplayAttribute& AffectedAttribute, float& NewValue) const
{
	// health
	if (AffectedAttribute == GetHealthAttribute()) {
		NewValue = FMath::Clamp(NewValue, 0, GetMaxHealth());
	}
	// health regen rate
	if (AffectedAttribute == GetHealthRegenRateAttribute()) {
		NewValue = FMath::Clamp(NewValue, 0, GetMaxHealth());
	}
	// stamina
	if (AffectedAttribute == GetStaminaAttribute()) {
		NewValue = FMath::Clamp(NewValue, 0, GetMaxStamina());
	}
	// stamina regen rate
	if (AffectedAttribute == GetStaminaRegenRateAttribute()) {
		NewValue = FMath::Clamp(NewValue, 0, GetMaxStamina());
	}
	// mana
	if (AffectedAttribute == GetManaAttribute()) {
		NewValue = FMath::Clamp(NewValue, 0, GetMaxMana());
	}
	// mana regen rate
	if (AffectedAttribute == GetManaRegenRateAttribute()) {
		NewValue = FMath::Clamp(NewValue, 0, GetMaxMana());
	}
}
// Attributes replication live time
void UCharacterAttributeSet::GetLifetimeReplicatedProps(TArray<FLifetimeProperty>& OutLifetimeProps) const
{
	Super::GetLifetimeReplicatedProps(OutLifetimeProps);
	// Health
	DOREPLIFETIME_CONDITION_NOTIFY(UCharacterAttributeSet, Health, COND_None, REPNOTIFY_Always);
	DOREPLIFETIME_CONDITION_NOTIFY(UCharacterAttributeSet, MaxHealth, COND_None, REPNOTIFY_Always);
	// Stamina
	DOREPLIFETIME_CONDITION_NOTIFY(UCharacterAttributeSet, Stamina, COND_None, REPNOTIFY_Always);
	DOREPLIFETIME_CONDITION_NOTIFY(UCharacterAttributeSet, MaxStamina, COND_None, REPNOTIFY_Always);
	// Mana
	DOREPLIFETIME_CONDITION_NOTIFY(UCharacterAttributeSet, Mana, COND_None, REPNOTIFY_Always);
	DOREPLIFETIME_CONDITION_NOTIFY(UCharacterAttributeSet, MaxMana, COND_None, REPNOTIFY_Always);
	// Armor
	DOREPLIFETIME_CONDITION_NOTIFY(UCharacterAttributeSet, Armor, COND_None, REPNOTIFY_Always);
}
// Health Replication
void UCharacterAttributeSet::OnRep_Health(const FGameplayAttributeData& Old)
{
	GAMEPLAYATTRIBUTE_REPNOTIFY(UCharacterAttributeSet, Health, Old);
}
void UCharacterAttributeSet::OnRep_MaxHealth(const FGameplayAttributeData& Old)
{
	GAMEPLAYATTRIBUTE_REPNOTIFY(UCharacterAttributeSet, MaxHealth, Old);
}
// Stamina Replication
void UCharacterAttributeSet::OnRep_Stamina(const FGameplayAttributeData& Old)
{
	GAMEPLAYATTRIBUTE_REPNOTIFY(UCharacterAttributeSet, Stamina, Old);
}
void UCharacterAttributeSet::OnRep_MaxStamina(const FGameplayAttributeData& Old)
{
	GAMEPLAYATTRIBUTE_REPNOTIFY(UCharacterAttributeSet, MaxStamina, Old);
}
// Mana Replication
void UCharacterAttributeSet::OnRep_Mana(const FGameplayAttributeData& Old)
{
	GAMEPLAYATTRIBUTE_REPNOTIFY(UCharacterAttributeSet, Mana, Old);
}
void UCharacterAttributeSet::OnRep_MaxMana(const FGameplayAttributeData& Old)
{
	GAMEPLAYATTRIBUTE_REPNOTIFY(UCharacterAttributeSet, MaxMana, Old);
}
// Armor Replication
void UCharacterAttributeSet::OnRep_Armor(const FGameplayAttributeData& Old)
{
	GAMEPLAYATTRIBUTE_REPNOTIFY(UCharacterAttributeSet, Armor, Old);
}
```
## Создаем класс персонажа
Добавим класс персонажа и зарегистрируем его в системе `GAS` через `Ability System Component`.
Выполняем команду `Tools -> New C++ Class` и в появившемся окне выбираем класс - `Character`.
![9ad556c6be7d0c8e3b0f1a866e8fab53.png](../images/9ad556c6be7d0c8e3b0f1a866e8fab53.png)
Называем будущий класс `CoreCharacter` и размещаем его в папке `Characters`.
![31a69b8d1f8ed429122c240d2fcfe683.png](../images/31a69b8d1f8ed429122c240d2fcfe683.png)
![15ab243cfbb9d3a2bc29ef7ac3bd2994.png](../images/15ab243cfbb9d3a2bc29ef7ac3bd2994.png)
### Добавляем стартовый код персонажа
В одной из предыдущих глав я создал базовую заготовку для класса `Character`, которая уже поддерживает `EnhancedInput`. Используем эту заготовку в нашем проекте.
#### `CoreCharacter.h`
```cpp
#pragma once
#include "CoreMinimal.h"
#include "GameFramework/Character.h"
// Enhanced Input
#include "InputMappingContext.h" // Mapping Context
#include "EnhancedInputSubsystems.h"
#include "EnhancedInputComponent.h"
// Kismet
#include "Kismet/KismetSystemLibrary.h"
#include "Kismet/GameplayStatics.h"
// Capsule Component
#include "Components/CapsuleComponent.h"
// Character Movement
#include "GameFramework/CharacterMovementComponent.h"
// Camera
#include "Camera/CameraComponent.h"
// Spring Arm
#include "GameFramework/SpringArmComponent.h"
// generated.h
#include "CoreCharacter.generated.h"
UCLASS()
class ACoreCharacter : public ACharacter
{
	GENERATED_BODY()
public:
	// Sets default values for this character's properties
	ACoreCharacter(const FObjectInitializer& ObjectInitializer);
	/** =======================================================
	 * Sub Objects
	 */
	// Spring Arm
	UPROPERTY(VisibleAnywhere, BlueprintReadOnly, Category = Camera)
	TObjectPtr<USpringArmComponent> CameraBoom;
	// Camera
	UPROPERTY(VisibleAnywhere, BlueprintReadOnly, Category = Camera)
	TObjectPtr<UCameraComponent> FollowCamera;
	/** =======================================================
	 * Variables
	 */
	// MappingContext
	UPROPERTY(EditAnywhere, BlueprintReadWrite, Category = Input)
	TSoftObjectPtr<UInputMappingContext> MappingContext = nullptr;
protected:
	// Called when the game starts or when spawned
	virtual void BeginPlay() override;
	// Movement Input
	void Move(const FInputActionValue& Value);
	// Camera Input
	void Rotate(const FInputActionValue& Value);
	// Sprint
	void StartSprint(const FInputActionValue& Value);
	// End Sprint
	void EndSprint(const FInputActionValue& Value);
public:
	// will bind new mapping context
	UFUNCTION(BlueprintCallable, Category = "Input")
	void BindContext(TSoftObjectPtr<UInputMappingContext> Context = nullptr, APlayerController* OnController = nullptr);
	// Called every frame
	virtual void Tick(float DeltaTime) override;
	// Called to bind functionality to input
	virtual void SetupPlayerInputComponent(class UInputComponent* PlayerInputComponent) override;
	// Once character posessed
	virtual void PossessedBy(AController* NewController) override;
};
```
#### `CoreCharacter.cpp`
```cpp
#include "Characters/CoreCharacter.h"
// Sets default values
ACoreCharacter::ACoreCharacter(const FObjectInitializer& ObjectInitializer)
{
	// SpringArm for the camera
	{
		CameraBoom = CreateDefaultSubobject<USpringArmComponent>(TEXT("CameraBoom"));
		CameraBoom->SetRelativeLocation(FVector3d(0.0f, 0.0f, 8.492264f));
		CameraBoom->SetupAttachment(RootComponent);
		CameraBoom->TargetArmLength = 400.0f; // The camera follows at this distance behind the character
		CameraBoom->bUsePawnControlRotation = true; // Rotate the arm based on the controller
		CameraBoom->bInheritPitch = true;
		CameraBoom->bInheritYaw = true;
		CameraBoom->bInheritRoll = true;
	}
	// Camera
	{
		FollowCamera = CreateDefaultSubobject<UCameraComponent>(TEXT("FollowCamera"));
		FollowCamera->SetupAttachment(CameraBoom, USpringArmComponent::SocketName); // Attach the camera to the end of the boom and let the boom adjust to match the controller orientation
		FollowCamera->bUsePawnControlRotation = false; // Camera does not rotate relative to arm
		FollowCamera->Deactivate(); // Deactivate camera by default to prevent performance issues
	}
	// Default Rotation Settings
	{
		bUseControllerRotationPitch = false;
		bUseControllerRotationYaw = false;
		bUseControllerRotationRoll = false;
	}
	// Set Character Movement values
	{
		UCharacterMovementComponent* CharMoveComp = GetCharacterMovement();
		CharMoveComp->bOrientRotationToMovement = true; // will force character to rotate towards velocity direction
		CharMoveComp->RotationRate = FRotator(0.0f, 500.0f, 0.0f); // with rotation rate
		// Speed values
		CharMoveComp->JumpZVelocity = 700.f;
		CharMoveComp->AirControl = 0.35f;
		CharMoveComp->MaxWalkSpeed = 250.f;
		CharMoveComp->MinAnalogWalkSpeed = 20.f;
		CharMoveComp->BrakingDecelerationWalking = 2000.f;
	}
	// Set Capsule Size
	{
		UCapsuleComponent* CapsuleComp = GetCapsuleComponent();
		CapsuleComp->InitCapsuleSize(35.0f, 90.0f);
	}
	// Load Default Skeletal Mesh
	{
		USkeletalMeshComponent* MeshComp = GetMesh();
		if (MeshComp->GetSkeletalMeshAsset() == nullptr) {
			// set refrence here
			const FString MannequinAsset = TEXT("/Script/Engine.SkeletalMesh'/Game/Assets/Mannequins/Meshes/SKM_Quinn.SKM_Quinn'");
			const TSoftObjectPtr<USkeletalMesh> SKMesh(MannequinAsset);
			if (!SKMesh.IsNull()) {
				MeshComp->SetSkeletalMesh(SKMesh.LoadSynchronous());
			}
			// Load ainm instance as well
			// since we loading blueprint class, add _C at the end of the asset refrence
			const FString AnimationInstanceAsset = TEXT("/Script/Engine.AnimBlueprint'/Game/Assets/Mannequins/Animations/ABP_Quinn.ABP_Quinn_C'");
			const TSoftClassPtr<UAnimInstance> CAnimInst(AnimationInstanceAsset);
			if (!CAnimInst.IsNull()) {
				MeshComp->SetAnimationMode(EAnimationMode::AnimationBlueprint);
				MeshComp->SetAnimInstanceClass(CAnimInst.LoadSynchronous());
			}
			// Apply default parameters to the mesh
			// Relative Location
			MeshComp->SetRelativeLocation(FVector3d(0.0f, 0.0f, -89.0f));
			// Relative Rotation
			MeshComp->SetRelativeRotation(FRotator(0.0f, 270.0f, 0.0f));
		}
	}
	// Load Default Mapping Context
	if (MappingContext == nullptr) {
		const FString Path = TEXT("/Script/EnhancedInput.InputMappingContext'/Game/Blueprints/Input/IMC_Default.IMC_Default'");
		MappingContext = UKismetSystemLibrary::MakeSoftObjectPath(Path);
	}
}
// Called when the game starts or when spawned
void ACoreCharacter::BeginPlay()
{
	Super::BeginPlay();
	// bind input context to player controller
	// Important to be called in BeginPlay, since SetupPlayerInputComponent dosen't get called for AI's
	BindContext(MappingContext.LoadSynchronous());
}
// will bind new mapping context
void ACoreCharacter::BindContext(TSoftObjectPtr<UInputMappingContext> Context, APlayerController* OnController)
{
	if (Context == nullptr) {
		Context = MappingContext;
	}
	if (Context == nullptr) return; // only because i don't want to crash app with check()
	APlayerController* PlayerController = OnController == nullptr ? Cast<APlayerController>(GetController()) : OnController;
	if (PlayerController == nullptr) {
		return;
	}
	const ULocalPlayer* LocalPlayer = PlayerController->GetLocalPlayer();
	// get enhanced input component's subsystem
	UEnhancedInputLocalPlayerSubsystem* EiSubsystem = ULocalPlayer::GetSubsystem<UEnhancedInputLocalPlayerSubsystem>(LocalPlayer);
	ensure(LocalPlayer && EiSubsystem);
	// input component
	UEnhancedInputComponent* EnhancedInputComponent = Cast<UEnhancedInputComponent>(PlayerController->InputComponent);
	//EnhancedInputComponent->ClearActionBindings(); // clear all bindings if necessary
	EnhancedInputComponent->ClearBindingsForObject(this); // clear bindings only from this object
	if (EiSubsystem != nullptr) {
		// register mapping context
		EiSubsystem->AddMappingContext(Context.Get(), InputPriority);
	}
	// iterate over all mappings and put every unique action into the Map
	// since actions occur in the map multiple times, for every key binding
	TMap<FName, const UInputAction*> Actions;
	for (FEnhancedActionKeyMapping Mapping : Context->GetMappings()) {
		FName ActionName = Mapping.Action->GetFName();
		if (!Actions.Contains(ActionName)) {
			Actions.Add(ActionName, Mapping.Action.Get());
		}
		UE_LOG(LogTemp, Warning, TEXT("Action Name = %s"), *Mapping.Action->GetName())
	}
	// now differentiate actions in the loop and assign them according to the names
	// make sure to give action names accordingly
	for (const TPair<FName, const UInputAction*>& ActionInfo : Actions)
	{
		FString AName = ActionInfo.Key.ToString();
		const UInputAction* Action = ActionInfo.Value;
		// Jumping
		if (AName.Contains(TEXT("Jump"))) {
			// use integrated jump
			EnhancedInputComponent->BindAction(Action, ETriggerEvent::Triggered, this, &ACharacter::Jump);
			EnhancedInputComponent->BindAction(Action, ETriggerEvent::Completed, this, &ACharacter::StopJumping);
		}
		// Moving
		if (AName.Contains(TEXT("Move"))) {
			EnhancedInputComponent->BindAction(Action, ETriggerEvent::Triggered, this, &ACoreCharacter::Move);
		}
		// Sprinting
		if (AName.Contains(TEXT("Sprint"))) {
			EnhancedInputComponent->BindAction(Action, ETriggerEvent::Triggered, this, &ACoreCharacter::StartSprint);
			EnhancedInputComponent->BindAction(Action, ETriggerEvent::Completed, this, &ACoreCharacter::EndSprint);
		}
		// Looking around
		if (AName.Contains(TEXT("Look"))) {
			EnhancedInputComponent->BindAction(Action, ETriggerEvent::Triggered, this, &ACoreCharacter::Rotate);
		}
	}
}
// On Possessed
void ACoreCharacter::PossessedBy(AController* NewController)
{
	Super::PossessedBy(NewController);
	bool bIsPlayer = NewController->IsLocalPlayerController();
	// Enable Camera only when character posessed by player
	if (bIsPlayer) {
		FollowCamera->Activate();
	}
}
// Start Sprint
void ACoreCharacter::StartSprint(const FInputActionValue& Value)
{
	UCharacterMovementComponent* CharMoveComp = GetCharacterMovement();
	CharMoveComp->MaxWalkSpeed = 500.f;
}
// End Sprint
void ACoreCharacter::EndSprint(const FInputActionValue& Value)
{
	UCharacterMovementComponent* CharMoveComp = GetCharacterMovement();
	CharMoveComp->MaxWalkSpeed = 250.f;
}
// Movement Input
void ACoreCharacter::Move(const FInputActionValue& Value)
{
	// input is a Vector2D
	FVector2D MovementVector = Value.Get<FVector2D>();
	if (GetController() != nullptr)
	{
		// find out which way is forward
		const FRotator Rotation = GetController()->GetControlRotation();
		const FRotator YawRotation(0, Rotation.Yaw, 0);
		// get forward vector
		FVector ForwardDirection = FRotationMatrix(YawRotation).GetUnitAxis(EAxis::X);
		// get right vector
		FVector RightDirection = FRotationMatrix(YawRotation).GetUnitAxis(EAxis::Y);
		// add movement
		AddMovementInput(ForwardDirection, MovementVector.Y);
		AddMovementInput(RightDirection, MovementVector.X);
	}
}
// Roation Input
void ACoreCharacter::Rotate(const FInputActionValue& Value)
{
	// input is a Vector2D
	FVector2D LookAxisVector = Value.Get<FVector2D>();
	AddControllerYawInput(LookAxisVector.X);
	AddControllerPitchInput(LookAxisVector.Y);
}
// Called every frame
void ACoreCharacter::Tick(float DeltaTime)
{
	Super::Tick(DeltaTime);
	/* nothing */
}
// Called to bind functionality to input
void ACoreCharacter::SetupPlayerInputComponent(UInputComponent* PlayerInputComponent)
{
	Super::SetupPlayerInputComponent(PlayerInputComponent);
}
```
### Создаем `blueprint` персонажа
В `Content Browser` нажмите на кнопку `Add -> Blueprint Class`.
![12d7deb7b62d2f9406c264e0b60ea0c4.png](../images/12d7deb7b62d2f9406c264e0b60ea0c4.png)
В окне выбора класса, на вкладке `All Classes` найдите пункт `CoreCharacter`.
![1b5a1a3c681266fd24ffaac629964453.png](../images/1b5a1a3c681266fd24ffaac629964453.png)
Добавляем `blueprint` и называем его `BP_CoreCharacter`.
![7e521b349a600d06b0036173fad7aa8f.png](../images/7e521b349a600d06b0036173fad7aa8f.png)
В созданном классе уже установлен базовый меш и подключены основные элементы управления.
![5c6b6d1af8f8b645d60ef1d86b3c232d.png](../images/5c6b6d1af8f8b645d60ef1d86b3c232d.png)
Если меш персонажа пуст - проверьте конструктор класса `ACoreCharacter`. В нем указаны ссылки на основные установленные "по умолчанию" ассеты.
![62c5b5efc6c66e3a8f7df43e4463a2a6.png](../images/62c5b5efc6c66e3a8f7df43e4463a2a6.png)
Добавим созданный нами класс в `GameMode`. В проекте `ThirdPerson` уже есть класс `GameMode`, который называется `BP_ThirdPersonGameMode`.
![aca983194e4ac06a3f1e0d425fd2975f.png](../images/aca983194e4ac06a3f1e0d425fd2975f.png)
Открываем его. Устанавливаем ссылку на нашего персонажа в параметр `Default Pawn Class`.
![899d027ee6340a79057225148fe5d681.png](../images/899d027ee6340a79057225148fe5d681.png)
Запускаем проект и проверяем работу системы.
![db1ff50a00ed3d4bf9e7f8e18ffba7d2.png](../images/db1ff50a00ed3d4bf9e7f8e18ffba7d2.png)
### Подключение `GAS`
Подключим систему `Gameplay Ability System` к созданному персонажу.
Для работы с `GAS`, персонаж или любой `UObject` должен иметь два суб объекта: `AbilitySystemComponent` и `AttributeSet`. Займемся их созданием.
#### Добавляем переменные `AbilitySystemComponent` и `AttributeSet`
В заголовочном файле, в `public` секции добавляем два указателя:
```cpp
//.h
// GAS
#include "AbilitySystemComponent.h"
#include "AbilitySystemInterface.h"
#include "Attributes/CharacterAttributeSet.h" // custom attributes
public:
/** =======================================================
 * GAS
 */
// Gameplay Ability Component SubObject
UPROPERTY(VisibleAnywhere, BlueprintReadOnly, Category="Gameplay Abilities")
TObjectPtr<UAbilitySystemComponent> GameAbilitySystemComponent;
// Attribute Set Subobject
UPROPERTY(VisibleAnywhere, BlueprintReadOnly, Category = "Gameplay Abilities")
TObjectPtr<UCharacterAttributeSet> AttributeSet; // our custom attribute set refrence
```
#### Создаем суб объект для `AttributeSet`
Рекомендуется первым создать суб объект для `AttributeSet`. Делается это в конструкторке класса персонажа.
```cpp
//.cpp
//constructor
// Attribute Set
{
    AttributeSet = CreateDefaultSubobject<UCharacterAttributeSet>(TEXT("Attribute Set"));
}
```
Суб объект `AttributeSet` будет автоматически подхвачен `AbilitySystemComponent` в момент создания. Таким образом система будет знать о атрибутах, которые ей передаются.
#### Создем суб объект для `AbilitySystemComponent`
Теперь необходимо создать суб объект для компонента `UAbilitySystemComponent`. Делается это так-же в конструкторе.
```cpp
//.cpp
//constructor
// Gameplay Ability System
{
    GameAbilitySystemComponent = CreateDefaultSubobject<UAbilitySystemComponent>(TEXT("AbilitySystemComponent"));
    GameAbilitySystemComponent->SetIsReplicated(true); // make GAS component states replicated
    GameAbilitySystemComponent->SetReplicationMode(EGameplayEffectReplicationMode::Full); // will replicate only necessary info
}
```
![034e8625bc9e05d638557bd5d0008178.png](../images/034e8625bc9e05d638557bd5d0008178.png)
Для созданного суб объекта надо включить сетевую репликацию методами `SetReplicationMode` и `SetIsReplicated`. При этом `EGameplayEffectReplicationMode` должен быть установлен в соответствии со следующими правилами:
```cpp
EGameplayEffectReplicationMode::Minimal - Only replicate minimal gameplay effect info. Note: this does not work for Owned AbilitySystemComponents (Use Mixed instead).
EGameplayEffectReplicationMode::Mixed - Only replicate minimal gameplay effect info to simulated proxies but full info to owners and autonomous proxies
EGameplayEffectReplicationMode::Full - Replicate full gameplay info to all
```
В зависимости от режима репликации меняется нагрузка на сеть.
#### Реализуем `IAbilitySystemInterface`
Интерфейс `IAbilitySystemInterface` содержит метод `GetAbilitySystemComponent` который разработчик обязан переопределить. В этом методе необходимо вернуть указатель на активный `AbilitySystemComponent`.
```cpp
//.h
//...
// inherit class from IAbilitySystemInterface
class GASBASE_API ACoreCharacter : public ACharacter, public IAbilitySystemInterface
{
    //...
    // Begin IAbilitySystemInterface
	// GAS component registration
	virtual UAbilitySystemComponent* GetAbilitySystemComponent() const override;
	// End IAbilitySystemInterface
}
//...
//.cpp
// GAS component registration
UAbilitySystemComponent* ACoreCharacter::GetAbilitySystemComponent() const
{
	return GameAbilitySystemComponent;
}
```
#### `Owner Actor` и `AvatarActor`
Компоненту `ASC` необходимо передать информацию о двух объектах: `OwnerActor` и с `AvatarActor`.
`OwnerActor` - объект, к которому привязан `AbilitySystemComponent`. В нашем случае это персонаж.
`AvatarActor` - объект, который является визуальным представлением `Owner'а`. В нашем случае это тоже персонаж.
Таким образом, инициализация `AbilitySystemComponent`, в нашем случае, выглядит так:
```cpp
GameAbilitySystemComponent->InitAbilityActorInfo(/* Owner Actor */ this, /* Avatar Actor */ this);
```
Поскольку `Unreal Engine` позволяет добавлять суб объекты к любым объектам, разработчик может создать `AbilitySystemComponent`, например, внутри `APlayerState`.
Тогда передача информации о владельце и аватаре будет выглядеть так:
```cpp
AGDPlayerState* PS = GetPlayerState<AGDPlayerState>();
if (PS)
{
    // Init ASC with PS (Owner) and our new Pawn (AvatarActor)
    PS->GetAbilitySystemComponent()->InitAbilityActorInfo(PS, this);
}
```
Переданные в `ASC` указатели на `OwnerActor` и `AvatarActor`, будут доступны разработчику внутри `GameplayAbility` в качестве `blueprint` нод: `Get Owning Actor from Actor Info`, и `Get Avatar Actor from Actor Info`.
![978044ebc2300c5204eed7cade833f7b.png](../images/978044ebc2300c5204eed7cade833f7b.png)
#### Переопределяем методы `OnRep_PlayerState` и `PossessedBy`
Привязка компонента `AbilitySystemComponent` к `OwnerActor` и `AvatarActor` должна быть проведена как на сервере, так и на клиенте. Для этих целей необходимо реализовать методы `OnRep_PlayerState` и `PossessedBy`.
Метод `OnRep_PlayerState` будет использоваться для инициализации `ASC` на сервере.
Метод `PossessedBy` будет использоваться для инициализации `ASC` на клиенте.
```cpp
//.h
// PlayerState Replication Notification Callback
virtual void OnRep_PlayerState() override;
// Once character posessed
virtual void PossessedBy(AController* NewController) override;
//...
//.cpp
// On Possessed
void ACoreCharacter::PossessedBy(AController* NewController)
{
	Super::PossessedBy(NewController);
	bool bIsPlayer = NewController->IsLocalPlayerController();
	// Enable Camera only when character posessed by player
	if (bIsPlayer) {
		FollowCamera->Activate();
	}
	// init GAS actor info
	if (GameAbilitySystemComponent != nullptr) {
		GameAbilitySystemComponent->InitAbilityActorInfo(/* Owner Actor */ this, /* Avatar Actor */ this);
	}
}
// PlayerState Replication Notification Callback
void ACoreCharacter::OnRep_PlayerState()
{
	Super::OnRep_PlayerState();
	// init GAS actor info
	if (GameAbilitySystemComponent != nullptr) {
		GameAbilitySystemComponent->InitAbilityActorInfo(/* Owner Actor */ this, /* Avatar Actor */ this);
	}
}
```
### Код персонажа на данном этапе
#### `CoreCharacter.h`
```cpp
#pragma once
#include "CoreMinimal.h"
#include "GameFramework/Character.h"
// GAS
#include "AbilitySystemComponent.h"
#include "AbilitySystemInterface.h"
#include "Attributes/CharacterAttributeSet.h"
#include "Abilities/CoreGameplayAbility.h"
// Enhanced Input
#include "InputMappingContext.h" // Mapping Context
#include "EnhancedInputSubsystems.h"
#include "EnhancedInputComponent.h"
// Kismet
#include "Kismet/KismetSystemLibrary.h"
#include "Kismet/GameplayStatics.h"
// Capsule Component
#include "Components/CapsuleComponent.h"
// Character Movement
#include "GameFramework/CharacterMovementComponent.h"
// Camera
#include "Camera/CameraComponent.h"
// Spring Arm
#include "GameFramework/SpringArmComponent.h"
// generated.h
#include "CoreCharacter.generated.h"
UCLASS()
class ACoreCharacter : public ACharacter, public IAbilitySystemInterface
{
	GENERATED_BODY()
public:
	// Sets default values for this character's properties
	ACoreCharacter(const FObjectInitializer& ObjectInitializer);
	/** =======================================================
	 * Sub Objects
	 */
	// Spring Arm
	UPROPERTY(VisibleAnywhere, BlueprintReadOnly, Category = Camera)
	TObjectPtr<USpringArmComponent> CameraBoom;
	// Camera
	UPROPERTY(VisibleAnywhere, BlueprintReadOnly, Category = Camera)
	TObjectPtr<UCameraComponent> FollowCamera;
	/** =======================================================
	 * GAS
	 */
	// Gameplay Ability Component SubObject
	UPROPERTY(VisibleAnywhere, BlueprintReadOnly, Category="Gameplay Abilities")
	TObjectPtr<UAbilitySystemComponent> GameAbilitySystemComponent;
	// Attribute Set Subobject
	UPROPERTY(VisibleAnywhere, BlueprintReadOnly, Category = "Gameplay Abilities")
	TObjectPtr<UCharacterAttributeSet> AttributeSet; // our custom attribute set refrence
	/** =======================================================
	 * Variables
	 */
	// MappingContext
	UPROPERTY(EditAnywhere, BlueprintReadWrite, Category = Input)
	TSoftObjectPtr<UInputMappingContext> MappingContext = nullptr;
	// Begin IAbilitySystemInterface
	// GAS component registration
	virtual UAbilitySystemComponent* GetAbilitySystemComponent() const override;
	// End IAbilitySystemInterface
protected:
	// Called when the game starts or when spawned
	virtual void BeginPlay() override;
	// Movement Input
	void Move(const FInputActionValue& Value);
	// Camera Input
	void Rotate(const FInputActionValue& Value);
	// Sprint
	void StartSprint(const FInputActionValue& Value);
	// End Sprint
	void EndSprint(const FInputActionValue& Value);
public:
	// will bind new mapping context
	UFUNCTION(BlueprintCallable, Category = "Input")
	void BindContext(TSoftObjectPtr<UInputMappingContext> Context = nullptr, APlayerController* OnController = nullptr);
	// Called every frame
	virtual void Tick(float DeltaTime) override;
	// Called to bind functionality to input
	virtual void SetupPlayerInputComponent(class UInputComponent* PlayerInputComponent) override;
	// Once character posessed
	virtual void PossessedBy(AController* NewController) override;
	// PlayerState Replication Notification Callback
	virtual void OnRep_PlayerState() override;
};
```
#### `CoreCharacter.cpp`
```cpp
#include "Characters/CoreCharacter.h"
// Sets default values
ACoreCharacter::ACoreCharacter(const FObjectInitializer& ObjectInitializer)
{
	// Attribute Set
	{
		AttributeSet = CreateDefaultSubobject<UCharacterAttributeSet>(TEXT("Attribute Set"));
	}
	// Gameplay Ability System
	{
		GameAbilitySystemComponent = CreateDefaultSubobject<UAbilitySystemComponent>(TEXT("AbilitySystemComponent"));
		GameAbilitySystemComponent->SetIsReplicated(true); // make GAS component states replicated
		GameAbilitySystemComponent->SetReplicationMode(EGameplayEffectReplicationMode::Full); // will replicate only necessary info
	}
	// SpringArm for the camera
	{
		CameraBoom = CreateDefaultSubobject<USpringArmComponent>(TEXT("CameraBoom"));
		CameraBoom->SetRelativeLocation(FVector3d(0.0f, 0.0f, 8.492264f));
		CameraBoom->SetupAttachment(RootComponent);
		CameraBoom->TargetArmLength = 400.0f; // The camera follows at this distance behind the character
		CameraBoom->bUsePawnControlRotation = true; // Rotate the arm based on the controller
		CameraBoom->bInheritPitch = true;
		CameraBoom->bInheritYaw = true;
		CameraBoom->bInheritRoll = true;
	}
	// Camera
	{
		FollowCamera = CreateDefaultSubobject<UCameraComponent>(TEXT("FollowCamera"));
		FollowCamera->SetupAttachment(CameraBoom, USpringArmComponent::SocketName); // Attach the camera to the end of the boom and let the boom adjust to match the controller orientation
		FollowCamera->bUsePawnControlRotation = false; // Camera does not rotate relative to arm
		FollowCamera->Deactivate(); // Deactivate camera by default to prevent performance issues
	}
	// Default Rotation Settings
	{
		bUseControllerRotationPitch = false;
		bUseControllerRotationYaw = false;
		bUseControllerRotationRoll = false;
	}
	// Set Character Movement values
	{
		UCharacterMovementComponent* CharMoveComp = GetCharacterMovement();
		CharMoveComp->bOrientRotationToMovement = true; // will force character to rotate towards velocity direction
		CharMoveComp->RotationRate = FRotator(0.0f, 500.0f, 0.0f); // with rotation rate
		// Speed values
		CharMoveComp->JumpZVelocity = 700.f;
		CharMoveComp->AirControl = 0.35f;
		CharMoveComp->MaxWalkSpeed = 250.f;
		CharMoveComp->MinAnalogWalkSpeed = 20.f;
		CharMoveComp->BrakingDecelerationWalking = 2000.f;
	}
	// Set Capsule Size
	{
		UCapsuleComponent* CapsuleComp = GetCapsuleComponent();
		CapsuleComp->InitCapsuleSize(35.0f, 90.0f);
	}
	// Load Default Skeletal Mesh
	{
		USkeletalMeshComponent* MeshComp = GetMesh();
		if (MeshComp->GetSkeletalMeshAsset() == nullptr) {
			// set refrence here
			const FString MannequinAsset = TEXT("/Script/Engine.SkeletalMesh'/Game/Assets/Mannequins/Meshes/SKM_Quinn.SKM_Quinn'");
			const TSoftObjectPtr<USkeletalMesh> SKMesh(MannequinAsset);
			if (!SKMesh.IsNull()) {
				MeshComp->SetSkeletalMesh(SKMesh.LoadSynchronous());
			}
			// Load ainm instance as well
			// since we loading blueprint class, add _C at the end of the asset refrence
			const FString AnimationInstanceAsset = TEXT("/Script/Engine.AnimBlueprint'/Game/Assets/Mannequins/Animations/ABP_Quinn.ABP_Quinn_C'");
			const TSoftClassPtr<UAnimInstance> CAnimInst(AnimationInstanceAsset);
			if (!CAnimInst.IsNull()) {
				MeshComp->SetAnimationMode(EAnimationMode::AnimationBlueprint);
				MeshComp->SetAnimInstanceClass(CAnimInst.LoadSynchronous());
			}
			// Apply default parameters to the mesh
			// Relative Location
			MeshComp->SetRelativeLocation(FVector3d(0.0f, 0.0f, -89.0f));
			// Relative Rotation
			MeshComp->SetRelativeRotation(FRotator(0.0f, 270.0f, 0.0f));
		}
	}
	// Load Default Mapping Context
	if (MappingContext == nullptr) {
		const FString Path = TEXT("/Script/EnhancedInput.InputMappingContext'/Game/Blueprints/Input/IMC_Default.IMC_Default'");
		MappingContext = UKismetSystemLibrary::MakeSoftObjectPath(Path);
	}
}
// Called when the game starts or when spawned
void ACoreCharacter::BeginPlay()
{
	Super::BeginPlay();
	// bind input context to player controller
	// Important to be called in BeginPlay, since SetupPlayerInputComponent dosen't get called for AI's
	BindContext(MappingContext.LoadSynchronous());
}
// On Possessed
void ACoreCharacter::PossessedBy(AController* NewController)
{
	Super::PossessedBy(NewController);
	bool bIsPlayer = NewController->IsLocalPlayerController();
	// Enable Camera only when character posessed by player
	if (bIsPlayer) {
		FollowCamera->Activate();
	}
	// init GAS actor info
	if (GameAbilitySystemComponent != nullptr) {
		GameAbilitySystemComponent->InitAbilityActorInfo(/* Owner Actor */ this, /* Avatar Actor */ this);
	}
}
// PlayerState Replication Notification Callback
void ACoreCharacter::OnRep_PlayerState()
{
	Super::OnRep_PlayerState();
	// init GAS actor info
	if (GameAbilitySystemComponent != nullptr) {
		GameAbilitySystemComponent->InitAbilityActorInfo(/* Owner Actor */ this, /* Avatar Actor */ this);
	}
}
// Called every frame
void ACoreCharacter::Tick(float DeltaTime)
{
	Super::Tick(DeltaTime);
	/* nothing */
}
/**===============================================
 * GAS
 */
// GAS component registration
UAbilitySystemComponent* ACoreCharacter::GetAbilitySystemComponent() const
{
	return GameAbilitySystemComponent;
}
/**===============================================
 * Input
 */
// will bind new mapping context
void ACoreCharacter::BindContext(TSoftObjectPtr<UInputMappingContext> Context, APlayerController* OnController)
{
	if (Context == nullptr) {
		Context = MappingContext;
	}
	if (Context == nullptr) return; // only because i don't want to crash app with check()
	APlayerController* PlayerController = OnController == nullptr ? Cast<APlayerController>(GetController()) : OnController;
	if (PlayerController == nullptr) {
		return;
	}
	const ULocalPlayer* LocalPlayer = PlayerController->GetLocalPlayer();
	// get enhanced input component's subsystem
	UEnhancedInputLocalPlayerSubsystem* EiSubsystem = ULocalPlayer::GetSubsystem<UEnhancedInputLocalPlayerSubsystem>(LocalPlayer);
	ensure(LocalPlayer && EiSubsystem);
	// input component
	UEnhancedInputComponent* EnhancedInputComponent = Cast<UEnhancedInputComponent>(PlayerController->InputComponent);
	//EnhancedInputComponent->ClearActionBindings(); // clear all bindings if necessary
	EnhancedInputComponent->ClearBindingsForObject(this); // clear bindings only from this object
	if (EiSubsystem != nullptr) {
		// register mapping context
		EiSubsystem->AddMappingContext(Context.Get(), InputPriority);
	}
	// iterate over all mappings and put every unique action into the Map
	// since actions occur in the map multiple times, for every key binding
	TMap<FName, const UInputAction*> Actions;
	for (FEnhancedActionKeyMapping Mapping : Context->GetMappings()) {
		FName ActionName = Mapping.Action->GetFName();
		if (!Actions.Contains(ActionName)) {
			Actions.Add(ActionName, Mapping.Action.Get());
		}
		UE_LOG(LogTemp, Warning, TEXT("Action Name = %s"), *Mapping.Action->GetName())
	}
	// now differentiate actions in the loop and assign them according to the names
	// make sure to give action names accordingly
	for (const TPair<FName, const UInputAction*>& ActionInfo : Actions)
	{
		FString AName = ActionInfo.Key.ToString();
		const UInputAction* Action = ActionInfo.Value;
		// Jumping
		if (AName.Contains(TEXT("Jump"))) {
			// use integrated jump
			EnhancedInputComponent->BindAction(Action, ETriggerEvent::Triggered, this, &ACharacter::Jump);
			EnhancedInputComponent->BindAction(Action, ETriggerEvent::Completed, this, &ACharacter::StopJumping);
		}
		// Moving
		if (AName.Contains(TEXT("Move"))) {
			EnhancedInputComponent->BindAction(Action, ETriggerEvent::Triggered, this, &ACoreCharacter::Move);
		}
		// Sprinting
		if (AName.Contains(TEXT("Sprint"))) {
			EnhancedInputComponent->BindAction(Action, ETriggerEvent::Triggered, this, &ACoreCharacter::StartSprint);
			EnhancedInputComponent->BindAction(Action, ETriggerEvent::Completed, this, &ACoreCharacter::EndSprint);
		}
		// Looking around
		if (AName.Contains(TEXT("Look"))) {
			EnhancedInputComponent->BindAction(Action, ETriggerEvent::Triggered, this, &ACoreCharacter::Rotate);
		}
	}
}
// Called to bind functionality to input
void ACoreCharacter::SetupPlayerInputComponent(UInputComponent* PlayerInputComponent)
{
	Super::SetupPlayerInputComponent(PlayerInputComponent);
}
// Start Sprint
void ACoreCharacter::StartSprint(const FInputActionValue& Value)
{
	UCharacterMovementComponent* CharMoveComp = GetCharacterMovement();
	CharMoveComp->MaxWalkSpeed = 500.f;
}
// End Sprint
void ACoreCharacter::EndSprint(const FInputActionValue& Value)
{
	UCharacterMovementComponent* CharMoveComp = GetCharacterMovement();
	CharMoveComp->MaxWalkSpeed = 250.f;
}
// Movement Input
void ACoreCharacter::Move(const FInputActionValue& Value)
{
	// input is a Vector2D
	FVector2D MovementVector = Value.Get<FVector2D>();
	if (GetController() != nullptr)
	{
		// find out which way is forward
		const FRotator Rotation = GetController()->GetControlRotation();
		const FRotator YawRotation(0, Rotation.Yaw, 0);
		// get forward vector
		FVector ForwardDirection = FRotationMatrix(YawRotation).GetUnitAxis(EAxis::X);
		// get right vector
		FVector RightDirection = FRotationMatrix(YawRotation).GetUnitAxis(EAxis::Y);
		// add movement
		AddMovementInput(ForwardDirection, MovementVector.Y);
		AddMovementInput(RightDirection, MovementVector.X);
	}
}
// Roation Input
void ACoreCharacter::Rotate(const FInputActionValue& Value)
{
	// input is a Vector2D
	FVector2D LookAxisVector = Value.Get<FVector2D>();
	AddControllerYawInput(LookAxisVector.X);
	AddControllerPitchInput(LookAxisVector.Y);
}
```
#### Проверка работоспособности
Поздравляю. Система `GAS` установлена, правда в весьма базовой комплектации. Осталось проверить, работает ли она.
Откройте созданный ранее `blueprint` класс - `BP_CoreCharacter`.
![e054f80e0d1be23ba035f52449897b7d.png](../images/e054f80e0d1be23ba035f52449897b7d.png)
В списке компонентов этого класса должен появиться `Ability System Component`, который мы задали.
![d80492d39fce7281e55602a7d86cb281.png](../images/d80492d39fce7281e55602a7d86cb281.png)
Этот компонент можно протестировать. Для этого создадим первый `Gameplay Ability`.
В `Content Browser` нажимаем на кнопку `Add -> Blueprint Class`.
![5250e93e6f34aba13ebf13b8bfd238ce.png](../images/5250e93e6f34aba13ebf13b8bfd238ce.png)
В появившемся окне выбираем подкатегорию `All` и ищем класс `GameplayAbility`.
![83f91797d6c93e0dc42dbd18d596ab90.png](../images/83f91797d6c93e0dc42dbd18d596ab90.png)
Создаем файл и называем `GA_TestAbility`.
![52b5b10796e0911751fd0bc2e3509508.png](../images/52b5b10796e0911751fd0bc2e3509508.png)
Пишем внутри `GA_TestAbility` код, который просто выводит сообщение `AbilityActivated`. В конце логики, очень важно вызвать ноду `EndAbility`, иначе способность так и будет висеть в памяти.
![2e827e8388cea57dd306d497db28b352.png](../images/2e827e8388cea57dd306d497db28b352.png)
Далее, в классе `BP_CoreCharacter` добавляем ноды `GiveAbility` и `TryActivateAbility`. В ноде `GiveAbility` укажите класс способности - `GA_TestAbility`. Я привязал вызов обоих нод к нажатию кнопки `1`.
![49c2a05203ea7f42553e247853ae5f89.png](../images/49c2a05203ea7f42553e247853ae5f89.png)
При нажатии клавиши `1` на экране появляется сообщение `AbilityActivated`.
![5589382ac197327325826533d31f5ea7.png](../images/5589382ac197327325826533d31f5ea7.png)
Способность работает.
### Отладка, `showdebug AbilitySystem`
Команда `showdebug AbilitySystem` позволяет отобразить отладочную информацию с текущими характеристиками персонажа, а так-же его тегами, способностями и эффектами.
Запустите проект, нажмите на клавишу тильды - `~` и отправьте в появившееся поле ввода команду:
```cpp
showdebug AbilitySystem
```
![90987b36d6b28954a29584e54e387b33.png](../images/90987b36d6b28954a29584e54e387b33.png)
Если все установлено правильно, мы увидим имя нашего персонажа, а так-же информацию о его характеристиках в виде отладочной информации.
![2d4eea425adb51275611906d1d05e202.png](../images/2d4eea425adb51275611906d1d05e202.png)
### Обработка ввода в `Ability System Component`
Способности, добавленные в `ASC` можно вызвать по нажатию клавиш и их сочетаний.
Подобный подход подразумевает, что разработчику уже известно, какие именно события ввода будет использовать игра.
Из известного набора событий  создается набор слотов в виде перечисления `Enum`, выполненного по специальной формуле. Первые три пункта `Enum` отводятся под слоты: `None`, `Confirm` и `Cancel`, про обработку событий `Confirm` и `Cancel` мы поговорим в отдельной главе. Остальные пункты перечисления разработчик может добавлять по своему усмотрению.
```cpp
// Ability Input Action
UENUM(BlueprintType)
enum class EAbilityInput: uint8
{
	None,
	Confirm,
	Cancel,
    /* Other Items */
};
```
#### Создаем `Enum` со слотами ввода
Предлагаю добавить под наш `Enum` отдельный файл. Идем в папку проекта `/Source/GASBase/Public`. Добавляем подпапку `Enums`.
![c10990c803f7b70c821480999298ec4d.png](../images/c10990c803f7b70c821480999298ec4d.png)
Внутри папки `Enums` добавляем пустой текстовый файл `AbilityInputEnum.h`.
![a59490b2cf35dacffaa7beb6ddd054b3.png](../images/a59490b2cf35dacffaa7beb6ddd054b3.png)
Чтобы созданный файл зарегистрировался в проекте - выполняем команду `Tools -> Refresh Visual Studio 2022 Project`.
![f7ab6f9356cbf7e36f707fa79222df19.png](../images/f7ab6f9356cbf7e36f707fa79222df19.png)
![65a94196ea1c19410bb185ebf1a50ffc.png](../images/65a94196ea1c19410bb185ebf1a50ffc.png)
Добавляем код, описывающий слоты ввода.
```cpp
#pragma once
#include "CoreMinimal.h"
// somehow my VS dosen't detect .generated.h for nested files so i have to inclde this manyally to get rid of red errors
#include "UObject/ObjectMacros.h"
// UHT
#include "AbilityInputEnum.generated.h"
// Ability Action
UENUM(BlueprintType)
enum class EAbilityInput : uint8
{
	None
	, Confirm
	, Cancel
	, Slot1 UMETA(DisplayName = "Fire Slot")
	, Slot2 UMETA(DisplayName = "Aim Slot")
	, Slot3 UMETA(DisplayName = "Sprint Slot")
	, Slot4 UMETA(DisplayName = "Jump Slot")
};
```
На данном этапе нам достаточно всего `4` слотов под способности.
#### Привязка `Ability System Component` к слотам способностей
Чтобы `ASC` начал отправлять события ввода к слотам способностей, необходимо вызвать метод `BindAbilityActivationToInputComponent` следующим образом:
```cpp
#include "Enums/AbilityInputEnum.h"
// register ability inputs
// https://forums.unrealengine.com/t/somebody-know-ftoplevelassetpath-of-5-1/734537/3
// Format is: /Script/ModuleName that owns this enum, EnumName
// if format is wrong - engine will crash because of assertion error
// My Module name is GASBase
// My Enum name is EAbilityInput
FTopLevelAssetPath EnumAssetPath = { FName("/Script/GASBase"), FName("EAbilityInput") };
GameAbilitySystemComponent->BindAbilityActivationToInputComponent(InputComponent, FGameplayAbilityInputBinds{ TEXT(""), TEXT(""), EnumAssetPath, (int)EAbilityInput::Confirm, (int)EAbilityInput::Cancel });
```
Привязку `Ability System Component` к слотам надо выполнять как на сервере, так и на клиенте, поэтому необходимо обернуть приведенный выше код в отдельный метод. У меня это `BindASCInput`. Данный метод должен быть вызван в `SetupPlayerInputComponent` на клиенте и в `OnRep_PlayerState` на сервере.
```cpp
//.h
#include "Enums/AbilityInputEnum.h"
//...
// Called to bind functionality to input
virtual void SetupPlayerInputComponent(class UInputComponent* PlayerInputComponent) override;
// PlayerState Replication Notification Callback
virtual void OnRep_PlayerState() override;
// Will bind GAS input
virtual void BindASCInput(class UInputComponent* PlayerInputComponent = NULL);
private:
	bool bGasInputBound = false; // true if gas is already bound. prevents multiple initialisations
//...
//.cpp
// Called to bind functionality to input
void ACoreCharacter::SetupPlayerInputComponent(UInputComponent* PlayerInputComponent)
{
	Super::SetupPlayerInputComponent(PlayerInputComponent);
	BindASCInput(PlayerInputComponent);
}
// PlayerState Replication Notification Callback
void ACoreCharacter::OnRep_PlayerState()
{
	Super::OnRep_PlayerState();
	// init GAS actor info
	if (GameAbilitySystemComponent != nullptr) {
		GameAbilitySystemComponent->InitAbilityActorInfo(/* Owner Actor */ this, /* Avatar Actor */ this);
	}
	BindASCInput();
}
// Will bind GAS input
void ACoreCharacter::BindASCInput(UInputComponent* PlayerInputComponent /* = NULL */)
{
	// if we already bound - just return
	if (bGasInputBound == true) {
		return;
	}
	UInputComponent* IC = PlayerInputComponent == NULL ? InputComponent : PlayerInputComponent;
	// if ASC not ready
	if (GameAbilitySystemComponent == nullptr || IC == nullptr) {
		bGasInputBound = false;
		return;
	}
	// register ability inputs
	// https://forums.unrealengine.com/t/somebody-know-ftoplevelassetpath-of-5-1/734537/3
	// Format is: /Script/ModuleName that owns this enum, EnumName
	// if format is wrong - engine will crash because of assertion error
	// My Module name is GASBase
	// My Enum name is EAbilityInput
	FTopLevelAssetPath EnumAssetPath = { FName("/Script/GASBase"), FName("EAbilityInput") };
	GameAbilitySystemComponent->BindAbilityActivationToInputComponent(IC, FGameplayAbilityInputBinds{ TEXT(""), TEXT(""), EnumAssetPath, (int)EAbilityInput::Confirm, (int)EAbilityInput::Cancel });
	bGasInputBound = true;
}
```
Обратите внимание, что в параметре `FGameplayAbilityInputBinds` я передаю две пустые текстовые метки - `TEXT(""), TEXT("")`. В `Unreal Engine 4.27` и ниже, они использовались для указания событий `Confirm` и `Cancel`. В `Unreal Engine 5` появился плагин `EnhancedInput`, поэтому данные метки стоит пропускать.
![c25a98347a0035adab90709eae79cd93.png](../images/c25a98347a0035adab90709eae79cd93.png)
Мы рассмотрим привязку действий `Confirm` и `Cancel` в одной из следующих глав, а пока просто передавайте пустые строки. Это совершенно нормально, в коде метода `FGameplayAbilityInputBinds` есть проверка для этого случая.
![4091442a24345180114c3a5c1fea887f.png](../images/4091442a24345180114c3a5c1fea887f.png)
#### Переопределяем `GameplayAbility`
Мы привязали `ASC` к слотам, заданным в `Enum`.
Необходимо переопределить класс `GameplayAbility` и добавить в него поддержку слотов ввода.
Выполняем команду `Tools -> New C++ Class`, в появившемся окне переходим на вкладку `All Classes` и ищем класс `GameplayAbility`.
![0d52a3d3882ce4e41741ce1b98a798f4.png](../images/0d52a3d3882ce4e41741ce1b98a798f4.png)
Называем класс `CoreGameplayAbility` и добавляем его в папку `Abilities`.
![5d6f34aa9c0bb7e087a512539858be14.png](../images/5d6f34aa9c0bb7e087a512539858be14.png)
Чтобы созданный файл появился в `Solution Explorer` - выполните команду `Tools -> Refresh Visual Studio 2022 Project`.
![6745235e667ff8d8738c7caddb1b7342.png](../images/6745235e667ff8d8738c7caddb1b7342.png)
Откройте файл `CoreGameplayAbility.h`. Все что нам надо - добавить в него одну переменную типа `EAbilityInput`.
```cpp
#pragma once
#include "CoreMinimal.h"
#include "Abilities/GameplayAbility.h"
#include "Enums/AbilityInputEnum.h"
// .generated.h
#include "CoreGameplayAbility.generated.h"
/**
 * Base Ability class with ActionSlot support
 */
UCLASS()
class GASBASE_API UCoreGameplayAbility : public UGameplayAbility
{
	GENERATED_BODY()
public:
	// Ability Action
	UPROPERTY(EditAnywhere, BlueprintReadOnly, Category = "Default")
	EAbilityInput AbilitySlot = EAbilityInput::None;
};
```
Скомпилируйте код.
##### `Jump Ability`
Посмотрим, как выглядит способность, которую мы создали. Для этого в `Content Browser` нажмите на кнопку `Add -> Blueprint Class`.
![013695c069bc61afa49be3f733a7667c.png](../images/013695c069bc61afa49be3f733a7667c.png)
Во вкладке `All Classes` найдите `CoreGameplayAbility` и создайте `blueprint`. Я назвал его `GA_JumpAbility`.
![7604138d23439342ffa3bcd37bcdd36a.png](../images/7604138d23439342ffa3bcd37bcdd36a.png)
Открываем созданный `blueprint`.
В правом верхнем углу панели `Details` вы можете увидеть параметр `AbilitySlot`.
Установите в слот значение `Jump`.
![c0f619a590e07b79281ce2c02da9b41c.png](../images/c0f619a590e07b79281ce2c02da9b41c.png)
Добавляем логику прыжка в код класса.
![ad299e63f56c60bc12c2a1d5440ac9d5.png](../images/ad299e63f56c60bc12c2a1d5440ac9d5.png)
![2266cf3ca12c94b54ac4bfd9c5970594.png](../images/2266cf3ca12c94b54ac4bfd9c5970594.png)
Устанавливаем для способности тег - `Locomotion.Jump`. Он должен быть установлен как для `Ability Tags`, так и для `Activation Owned Tags`.
![2104d5bac617d4b9914f244fe78f094e.png](../images/2104d5bac617d4b9914f244fe78f094e.png)
Способность готова. Теперь ее необходимо привязать к нажатию соответствующей клавиши.
#### Создаем и регистрируем дополнительные `InputAction`
Создадим недостающие`InputAction`.
Для этого в `Content Browser` нажмите на кнопку `Add -> Input -> Input Action`.
![037f6272da593d550733a33ede3aa799.png](../images/037f6272da593d550733a33ede3aa799.png)
Нужны следующие `Input Action` для наших слотов: `IA_Sprint`, `IA_Fire`, `IA_Aim`, `IA_Jump`. Все события должны иметь тип `bool`.
![8d10f8709fbb6259f9a53cd7ebf7d874.png](../images/8d10f8709fbb6259f9a53cd7ebf7d874.png)
Каждое действие должно быть добавлено в контекст ввода, с привязкой к соответствующей клавише. Откройте стандартный контекст ввода `IMC_Default`.
![b5419a7ce870007e138324383142b9da.png](../images/b5419a7ce870007e138324383142b9da.png)
Добавьте созданные нами действия в контекст, а так-же присвойте им соответствующие клавиши ввода.
![2d22b0bab649038b49ade0e36c349ade.png](../images/2d22b0bab649038b49ade0e36c349ade.png)
#### Запускаем событие в слоте используя `blueprint` код
При запуске проекта слоты способностей пусты. Мы еще не реализовали логику автоматического добавления способностей. Поэтому, для тестирования, добавим `Gameplay Action` в нужный слот по событию `BeginPlay`, внутри класса персонажа `BP_CoreCharacter`.
![ded6ee874c2564ce59a24ac25e7a38f1.png](../images/ded6ee874c2564ce59a24ac25e7a38f1.png)
У ноды `GiveAbility` есть параметр `InputID`. Именно сюда надо задать номер слота, при активации которого автоматически будет срабатывать способность.
Откуда взять `Input ID`?
Поскольку мы передали слоты `EAbilityInput` в `GAS`, именно из `EAbilityInput` надо брать`Input ID`.
Добавляем в `blueprint` код новую ноду типа `Literal EAbilityInput`. В этой ноде устанавливаем слот для способности на `Jump`.
Соединяем ноду `Literal EAbilityInput` c пином `InputId` через приведение типа `Enum -> Int(byte)`.
![5b71d7575ed93b127d9b494ec6d4f6cf.png](../images/5b71d7575ed93b127d9b494ec6d4f6cf.png)
Теперь необходимо настроить логику вызова события из слота. Сделать это можно как в `c++` (о чем мы поговорим ниже), так и из `blueprint`. Добавляем на сцену две ноды, от компонента `AbilitySystemComponent`, а именно: `FindAll Abilities with InputID` и `TryActivateAbility`.
По событию `IA_Jump` необходимо вызвать ноду `FindAll Abilities with InputID` и передать номер слота в параметр `Input Id`.
![9aba20004ba12bcdda31c99f3f9a44cf.png](../images/9aba20004ba12bcdda31c99f3f9a44cf.png)
Запустите проект и нажмите клавишу пробела. Она отвечает за событие `Jump`. Персонаж прыгает, а на экране появляется отладочный текст.
![60cc928692603ff36faec8122116955d.png](../images/60cc928692603ff36faec8122116955d.png)
Однако, у такого подохода есть весьма серьезный недостаток.
Если мы добавим ноду `Wait Input Release` в конец нашего `blueprint` скрипта следующим образом:
![f6f5e07f5db214e7182aea104b0d8f08.png](../images/f6f5e07f5db214e7182aea104b0d8f08.png)
То, запустив проект, увидим, что она не работает.
Нода `Wait Input Release` ждет, пока разработчик отпустит привязанную клавишу. Поскольку мы не привязывали наши клавиши ввода через метод `AbilityLocalInputPressed` - нода `Wait Input Release` не работает.
![5117219ed3646e17f5d88010f23cd9be.png](../images/5117219ed3646e17f5d88010f23cd9be.png)
Сообщение `Ability Stopped!` не появляется. Так происходит, потому что нода `Wait Input Release` не работает. А не работает она, потому что активировать способности надо  через `AbilityLocalInputPressed` и `AbilityLocalInputReleased`. К сожалению, данные методы не доступны в `blueprint`, поэтому привяжем их через `с++`.
#### Настраиваем вызов способностей через `с++`
Привязка слотов `InputId` к клавишам ввода осуществляется в методе `AbilityLocalInputPressed`, который может быть вызван исключительно в `c++`.
Добавляем в код персонажа метод `SendAbilityLocalInput`, который который служит оберткой для вызова `AbilityLocalInputPressed`.
```cpp
//.h
// Will send input value to local Gas Component
virtual void SendAbilityLocalInput(const FInputActionValue& Value, int InputId);
//...
//.cpp
// Will send input value to local Gas Component
void AGameBaseCharacter::SendAbilityLocalInput(const FInputActionValue& Value, int InputId)
{
	if (GameAbilitySystemComponent == nullptr) {
		return;
	}
	if (Value.Get<bool>() == true) {
		GameAbilitySystemComponent->AbilityLocalInputPressed(InputId);
	}
	else {
		GameAbilitySystemComponent->AbilityLocalInputReleased(InputId);
	}
}
```
Теперь идем в метод
Изменяем секцию `Jumping` следующим образом:
```cpp
// Jumping
if (AName.Contains(TEXT("Jump"))) {
    EnhancedInputComponent->BindAction(Action, ETriggerEvent::Triggered, this, &ACoreCharacter::SendAbilityLocalInput, (int)EAbilityInput::Slot4);
    EnhancedInputComponent->BindAction(Action, ETriggerEvent::Completed, this, &ACoreCharacter::SendAbilityLocalInput, (int)EAbilityInput::Slot4);
    // use integrated jump
    //EnhancedInputComponent->BindAction(Action, ETriggerEvent::Triggered, this, &ACharacter::Jump);
    //EnhancedInputComponent->BindAction(Action, ETriggerEvent::Completed, this, &ACharacter::StopJumping);
}
```
Как вы видите, я передаю в метод `SendAbilityLocalInput` два события. Одно - для начала прыжка `ETriggerEvent::Triggered`, второе - для окончания прыжка `ETriggerEvent::Completed`.
Если скомпилировать код с этим изменением мы увидим, что логика работает корректно.
![7408d35390fc835f7a1028d562675f57.png](../images/7408d35390fc835f7a1028d562675f57.png)
Нода `WaitInputRelease` корректно отслеживает событие `ETriggerEvent::Completed`.
![63cf7aee3fb04128472cc8b75595629c.png](../images/63cf7aee3fb04128472cc8b75595629c.png)
Создадим `GameplayAbility` для бега.
Для начала регистрируем слот.
```cpp
// Sprinting
if (AName.Contains(TEXT("Sprint"))) {
    EnhancedInputComponent->BindAction(Action, ETriggerEvent::Triggered, this, &ACoreCharacter::SendAbilityLocalInput, (int)EAbilityInput::Slot3);
    EnhancedInputComponent->BindAction(Action, ETriggerEvent::Completed, this, &ACoreCharacter::SendAbilityLocalInput, (int)EAbilityInput::Slot3);
    //EnhancedInputComponent->BindAction(Action, ETriggerEvent::Triggered, this, &ACoreCharacter::StartSprint);
    //EnhancedInputComponent->BindAction(Action, ETriggerEvent::Completed, this, &ACoreCharacter::EndSprint);
}
```
Компилируем код.
##### `Sprint Ability`
В `Content Browser` нажимаем кнопку `Add -> Blueprint Class`. В качестве класса выбираем `CoreGameplayAbility`.
![2bf5734b3f42a6b67062fc3a6a4b89ad.png](../images/2bf5734b3f42a6b67062fc3a6a4b89ad.png)
Называем новый ассет - `GA_Sprint`.
![319fc872b61296b5bc2fb2fe1689fa4a.png](../images/319fc872b61296b5bc2fb2fe1689fa4a.png)
Открываем ассет и добавляем в него `blueprin` код, управляющий параметром `MaxWalkSpeed`.
![1525edc956a2bf1f573f159ce3b20ba7.png](../images/1525edc956a2bf1f573f159ce3b20ba7.png)
Созданное событие надо добавить в соответствующий слот персонажа. Для этого в методе `BeginPlay` класса `BP_CoreCharacter` добавляем еще одну ноду `GiveAbility`, с указанием на `GA_Sprint`.
![573848573cb0f9146d93b699b0908b6d.png](../images/573848573cb0f9146d93b699b0908b6d.png)
Теперь, если запустить проект, мы увидим, что персонаж получил возможность бегать за счет использования `GameplayAbility`.
![a879a1bca085f162e3aa49506e5133ec.png](../images/a879a1bca085f162e3aa49506e5133ec.png)
Как вы видите, слоты заработали.
Для каждого из слотов способностей, в методе `BindContext` необходимо добавлять отдельное условие. Для всех четырех  слотов код выглядит так:
```cpp
// Will send input value to local Gas Component
void ACoreCharacter::SendAbilityLocalInput(const FInputActionValue& Value, int InputId)
{
	if (GameAbilitySystemComponent == nullptr) {
		return;
	}
	if (Value.Get<bool>() == true) {
		GameAbilitySystemComponent->AbilityLocalInputPressed(InputId);
	}
	else {
		GameAbilitySystemComponent->AbilityLocalInputReleased(InputId);
	}
}
// ...
// BindContext
// Jumping
if (AName.Contains(TEXT("Jump"))) {
    EnhancedInputComponent->BindAction(Action, ETriggerEvent::Triggered, this, &ACoreCharacter::SendAbilityLocalInput, (int)EAbilityInput::Slot4);
    EnhancedInputComponent->BindAction(Action, ETriggerEvent::Completed, this, &ACoreCharacter::SendAbilityLocalInput, (int)EAbilityInput::Slot4);
}
// Fire
if (AName.Contains(TEXT("Fire"))) {
    EnhancedInputComponent->BindAction(Action, ETriggerEvent::Triggered, this, &ACoreCharacter::SendAbilityLocalInput, (int)EAbilityInput::Slot1);
    EnhancedInputComponent->BindAction(Action, ETriggerEvent::Completed, this, &ACoreCharacter::SendAbilityLocalInput, (int)EAbilityInput::Slot1);
}
// Aim
if (AName.Contains(TEXT("Aim"))) {
    EnhancedInputComponent->BindAction(Action, ETriggerEvent::Triggered, this, &ACoreCharacter::SendAbilityLocalInput, (int)EAbilityInput::Slot2);
    EnhancedInputComponent->BindAction(Action, ETriggerEvent::Completed, this, &ACoreCharacter::SendAbilityLocalInput, (int)EAbilityInput::Slot2);
}
// Sprinting
if (AName.Contains(TEXT("Sprint"))) {
    EnhancedInputComponent->BindAction(Action, ETriggerEvent::Triggered, this, &ACoreCharacter::SendAbilityLocalInput, (int)EAbilityInput::Slot3);
    EnhancedInputComponent->BindAction(Action, ETriggerEvent::Completed, this, &ACoreCharacter::SendAbilityLocalInput, (int)EAbilityInput::Slot3);
}
```
Итоговый код метода `BindContext` выглядит так:
```cpp
// will bind new mapping context
void ACoreCharacter::BindContext(TSoftObjectPtr<UInputMappingContext> Context, APlayerController* OnController)
{
	if (Context == nullptr) {
		Context = MappingContext;
	}
	if (Context == nullptr) return; // only because i don't want to crash app with check()
	APlayerController* PlayerController = OnController == nullptr ? Cast<APlayerController>(GetController()) : OnController;
	if (PlayerController == nullptr) {
		return;
	}
	const ULocalPlayer* LocalPlayer = PlayerController->GetLocalPlayer();
	// get enhanced input component's subsystem
	UEnhancedInputLocalPlayerSubsystem* EiSubsystem = ULocalPlayer::GetSubsystem<UEnhancedInputLocalPlayerSubsystem>(LocalPlayer);
	ensure(LocalPlayer && EiSubsystem);
	// input component
	UEnhancedInputComponent* EnhancedInputComponent = Cast<UEnhancedInputComponent>(PlayerController->InputComponent);
	//EnhancedInputComponent->ClearActionBindings(); // clear all bindings if necessary
	EnhancedInputComponent->ClearBindingsForObject(this); // clear bindings only from this object
	if (EiSubsystem != nullptr) {
		// register mapping context
		EiSubsystem->AddMappingContext(Context.Get(), InputPriority);
	}
	// iterate over all mappings and put every unique action into the Map
	// since actions occur in the map multiple times, for every key binding
	TMap<FName, const UInputAction*> Actions;
	for (FEnhancedActionKeyMapping Mapping : Context->GetMappings()) {
		FName ActionName = Mapping.Action->GetFName();
		if (!Actions.Contains(ActionName)) {
			Actions.Add(ActionName, Mapping.Action.Get());
		}
		UE_LOG(LogTemp, Warning, TEXT("Action Name = %s"), *Mapping.Action->GetName())
	}
	// now differentiate actions in the loop and assign them according to the names
	// make sure to give action names accordingly
	for (const TPair<FName, const UInputAction*>& ActionInfo : Actions)
	{
		FString AName = ActionInfo.Key.ToString();
		const UInputAction* Action = ActionInfo.Value;
		// Jumping
		if (AName.Contains(TEXT("Jump"))) {
			EnhancedInputComponent->BindAction(Action, ETriggerEvent::Triggered, this, &ACoreCharacter::SendAbilityLocalInput, (int)EAbilityInput::Slot4);
			EnhancedInputComponent->BindAction(Action, ETriggerEvent::Completed, this, &ACoreCharacter::SendAbilityLocalInput, (int)EAbilityInput::Slot4);
			// use integrated jump
			//EnhancedInputComponent->BindAction(Action, ETriggerEvent::Triggered, this, &ACharacter::Jump);
			//EnhancedInputComponent->BindAction(Action, ETriggerEvent::Completed, this, &ACharacter::StopJumping);
		}
		// Fire
		if (AName.Contains(TEXT("Fire"))) {
			EnhancedInputComponent->BindAction(Action, ETriggerEvent::Triggered, this, &ACoreCharacter::SendAbilityLocalInput, (int)EAbilityInput::Slot1);
			EnhancedInputComponent->BindAction(Action, ETriggerEvent::Completed, this, &ACoreCharacter::SendAbilityLocalInput, (int)EAbilityInput::Slot1);
		}
		// Aim
		if (AName.Contains(TEXT("Aim"))) {
			EnhancedInputComponent->BindAction(Action, ETriggerEvent::Triggered, this, &ACoreCharacter::SendAbilityLocalInput, (int)EAbilityInput::Slot2);
			EnhancedInputComponent->BindAction(Action, ETriggerEvent::Completed, this, &ACoreCharacter::SendAbilityLocalInput, (int)EAbilityInput::Slot2);
		}
		// Moving
		if (AName.Contains(TEXT("Move"))) {
			EnhancedInputComponent->BindAction(Action, ETriggerEvent::Triggered, this, &ACoreCharacter::Move);
		}
		// Sprinting
		if (AName.Contains(TEXT("Sprint"))) {
			EnhancedInputComponent->BindAction(Action, ETriggerEvent::Triggered, this, &ACoreCharacter::SendAbilityLocalInput, (int)EAbilityInput::Slot3);
			EnhancedInputComponent->BindAction(Action, ETriggerEvent::Completed, this, &ACoreCharacter::SendAbilityLocalInput, (int)EAbilityInput::Slot3);
			//EnhancedInputComponent->BindAction(Action, ETriggerEvent::Triggered, this, &ACoreCharacter::StartSprint);
			//EnhancedInputComponent->BindAction(Action, ETriggerEvent::Completed, this, &ACoreCharacter::EndSprint);
		}
		// Looking around
		if (AName.Contains(TEXT("Look"))) {
			EnhancedInputComponent->BindAction(Action, ETriggerEvent::Triggered, this, &ACoreCharacter::Rotate);
		}
	}
}
```
На данном этапе старые функции обработки `StartSprint` и `EndSprint` можно убрать. Все способности должны работать исключительно через `GameplayAbility`. Стандартная обработка ввода остается лишь для камеры и движения персонажа.
#### Добавляем действия `Confirm` и `Cancel`
Для инициализации слотов событий используется метод `BindAbilityActivationToInputComponent`. Мы вызывали этот метод в одной из предыдущих глав следующим образом:
```cpp
#include "Enums/AbilityInputEnum.h"
// register ability inputs
// https://forums.unrealengine.com/t/somebody-know-ftoplevelassetpath-of-5-1/734537/3
// Format is: /Script/ModuleName that owns this enum, EnumName
// if format is wrong - engine will crash because of assertion error
// My Module name is GASBase
// My Enum name is EAbilityInput
FTopLevelAssetPath EnumAssetPath = { FName("/Script/GASBase"), FName("EAbilityInput") };
GameAbilitySystemComponent->BindAbilityActivationToInputComponent(InputComponent, FGameplayAbilityInputBinds{ TEXT(""), TEXT(""), EnumAssetPath, (int)EAbilityInput::Confirm, (int)EAbilityInput::Cancel });
```
Обратите внимание на параметры `TEXT(""), TEXT("")`. Это пустые текстовые метки, в которые должна быть передана информация о двух действиях: `Confirm` и `Cancel`.
Данные действия отвечают за `blueprint` ноды `Wait For Cancel Input` и `Wait For Confirm Input`.
![c5c3fb515954857e2a4a88a047e0eefc.png](../images/c5c3fb515954857e2a4a88a047e0eefc.png)
![84641f000dbb06fcb36a48702ea47137.png](../images/84641f000dbb06fcb36a48702ea47137.png)
Если не привязать действия `Confirm` и `Cancel`, то данные ноды работать не будут.
##### Классический способ привязки
Действия `Confirm` и `Cancel` создаются вручную через меню `Edit -> Project Settings -> Input`.
![c876833dd593b53dadbae5fef860eacf.png](../images/c876833dd593b53dadbae5fef860eacf.png)
Затем, их названия передаются в метод `BindAbilityActivationToInputComponent` следующим образом:
```cpp
GameAbilitySystemComponent->BindAbilityActivationToInputComponent(InputComponent, FGameplayAbilityInputBinds{ TEXT("ConfirmAction"), TEXT("CancelAction"), EnumAssetPath, (int)EAbilityInput::Confirm, (int)EAbilityInput::Cancel });
```
Именно так это сделано в проекте `GAS Documentation`.
![07b46635fa8489ba224179900c1deb13.png](../images/07b46635fa8489ba224179900c1deb13.png)
Однако, если перейти в соответствующее меню, можно увидеть, что стандартная система ввода имеет пометку - `Deprecated`.
![8d6bbe169dfad819e15a955a2317b41f.png](../images/8d6bbe169dfad819e15a955a2317b41f.png)
Создать стандартные действия ввода все еще можно и они будут работать. Но `Deprecated` статус заданных таким образом действий не внушает оптимизма.
##### Привязка действий с применением плагина `EnhancedInput`
Необходимо привязать действия `Confirm` и `Cancel` с применением плагина `EnhancedInput`.
Для выполнения данной задачи, рассмотрим реализацию метода `BindAbilityActivationToInputComponent`. В коде метода можно найти функции, отвечающие за обработку действий `Confirm` и `Cancel`.
![36633d98904fb791a1542030b46e79e1.png](../images/36633d98904fb791a1542030b46e79e1.png)

- Действие `Confirm` обрабатывается методом `&UAbilitySystemComponent::LocalInputConfirm`.
- Действие `Cancel` обрабатывается методом `&UAbilitySystemComponent::LocalInputCancel`

Разработчик может привязать данные методы к собственным событиям, используя `EnhancedInput` и `MappingContext`.
Добавим `Input Action`под наши действия.
В `Content Browser` нажимаем на кнопку `Add`, в меню `Input` выбираем `Input Action`.
![4a17004af3a5cbe294c2252897f5395b.png](../images/4a17004af3a5cbe294c2252897f5395b.png)
Добавляем таким образом два действия `IA_Confirm` и `IA_Cancel`.
![2450cf32aaed2f04b70aa2fade850e18.png](../images/2450cf32aaed2f04b70aa2fade850e18.png)
`Value Type` в каждом действии - `bool`.
![86a11f61e17022a323790fd1b87f0fb7.png](../images/86a11f61e17022a323790fd1b87f0fb7.png)
![0d62ac41fc3e280bc211114399ccd887.png](../images/0d62ac41fc3e280bc211114399ccd887.png)
Созданные `InputAction` необходимо добавить в `MappingContext`, в нашем случае это `IMC_Default`.
![b302d9e94c7314d04abe49f3702420b7.png](../images/b302d9e94c7314d04abe49f3702420b7.png)
Добавляем привязки клавиш к обоим действиям в контексте. В моем случае, действие `IA_Confirm` будет срабатывать по нажатию левой кнопки мыши, а действие `IA_Cancel` - по нажатию правой кнопки мыши. Если левая кнопка у вас уже привязана, например, к действию `IA_Fire` - ничего страшного, код все равно будет работать корректно.
![21d2710521a6e6e99a437b6321221da6.png](../images/21d2710521a6e6e99a437b6321221da6.png)
Дополняем код метода `ACoreCharacter::BindContext` следующими строчками:
```cpp
// Confirm
if (AName.Contains(TEXT("Confirm")) && GameAbilitySystemComponent != nullptr) {
    // bind Confirm action
    EnhancedInputComponent->BindAction(Action, ETriggerEvent::Started, GameAbilitySystemComponent.Get(), &UAbilitySystemComponent::LocalInputConfirm);
    GameAbilitySystemComponent->GenericCancelInputID = (int)EAbilityInput::Confirm;
}
// Cancel
if (AName.Contains(TEXT("Cancel")) && GameAbilitySystemComponent != nullptr) {
    // bind Confirm action
    EnhancedInputComponent->BindAction(Action, ETriggerEvent::Started, GameAbilitySystemComponent.Get(), &UAbilitySystemComponent::LocalInputCancel);
    GameAbilitySystemComponent->GenericCancelInputID = (int) EAbilityInput::Cancel;
}
```
Общий код метода `ACoreCharacter::BindContext` выглядит так:
```cpp
// will bind new mapping context
void ACoreCharacter::BindContext(TSoftObjectPtr<UInputMappingContext> Context, APlayerController* OnController)
{
	if (Context == nullptr) {
		Context = MappingContext;
	}
	if (Context == nullptr) return; // only because i don't want to crash app with check()
	APlayerController* PlayerController = OnController == nullptr ? Cast<APlayerController>(GetController()) : OnController;
	if (PlayerController == nullptr) {
		return;
	}
	const ULocalPlayer* LocalPlayer = PlayerController->GetLocalPlayer();
	// get enhanced input component's subsystem
	UEnhancedInputLocalPlayerSubsystem* EiSubsystem = ULocalPlayer::GetSubsystem<UEnhancedInputLocalPlayerSubsystem>(LocalPlayer);
	ensure(LocalPlayer && EiSubsystem);
	// input component
	UEnhancedInputComponent* EnhancedInputComponent = Cast<UEnhancedInputComponent>(PlayerController->InputComponent);
	//EnhancedInputComponent->ClearActionBindings(); // clear all bindings if necessary
	EnhancedInputComponent->ClearBindingsForObject(this); // clear bindings only from this object
	if (EiSubsystem != nullptr) {
		// register mapping context
		EiSubsystem->AddMappingContext(Context.Get(), InputPriority);
	}
	// iterate over all mappings and put every unique action into the Map
	// since actions occur in the map multiple times, for every key binding
	TMap<FName, const UInputAction*> Actions;
	for (FEnhancedActionKeyMapping Mapping : Context->GetMappings()) {
		FName ActionName = Mapping.Action->GetFName();
		if (!Actions.Contains(ActionName)) {
			Actions.Add(ActionName, Mapping.Action.Get());
		}
		//UE_LOG(LogTemp, Warning, TEXT("Action Name = %s"), *Mapping.Action->GetName())
	}
	// now differentiate actions in the loop and assign them according to the names
	// make sure to give action names accordingly
	for (const TPair<FName, const UInputAction*>& ActionInfo : Actions)
	{
		FString AName = ActionInfo.Key.ToString();
		const UInputAction* Action = ActionInfo.Value;
		// Confirm
		if (AName.Contains(TEXT("Confirm")) && GameAbilitySystemComponent != nullptr) {
			// bind Confirm action
			EnhancedInputComponent->BindAction(Action, ETriggerEvent::Started, GameAbilitySystemComponent.Get(), &UAbilitySystemComponent::LocalInputConfirm);
			GameAbilitySystemComponent->GenericCancelInputID = (int)EAbilityInput::Confirm;
		}
		// Cancel
		if (AName.Contains(TEXT("Cancel")) && GameAbilitySystemComponent != nullptr) {
			// bind Confirm action
			EnhancedInputComponent->BindAction(Action, ETriggerEvent::Started, GameAbilitySystemComponent.Get(), &UAbilitySystemComponent::LocalInputCancel);
			GameAbilitySystemComponent->GenericCancelInputID = (int) EAbilityInput::Cancel;
		}
		// Jumping
		if (AName.Contains(TEXT("Jump"))) {
			EnhancedInputComponent->BindAction(Action, ETriggerEvent::Triggered, this, &ACoreCharacter::SendAbilityLocalInput, (int)EAbilityInput::Slot4);
			EnhancedInputComponent->BindAction(Action, ETriggerEvent::Completed, this, &ACoreCharacter::SendAbilityLocalInput, (int)EAbilityInput::Slot4);
			// use integrated jump
			//EnhancedInputComponent->BindAction(Action, ETriggerEvent::Triggered, this, &ACharacter::Jump);
			//EnhancedInputComponent->BindAction(Action, ETriggerEvent::Completed, this, &ACharacter::StopJumping);
		}
		// Fire
		if (AName.Contains(TEXT("Fire"))) {
			EnhancedInputComponent->BindAction(Action, ETriggerEvent::Triggered, this, &ACoreCharacter::SendAbilityLocalInput, (int)EAbilityInput::Slot1);
			EnhancedInputComponent->BindAction(Action, ETriggerEvent::Completed, this, &ACoreCharacter::SendAbilityLocalInput, (int)EAbilityInput::Slot1);
		}
		// Aim
		if (AName.Contains(TEXT("Aim"))) {
			EnhancedInputComponent->BindAction(Action, ETriggerEvent::Triggered, this, &ACoreCharacter::SendAbilityLocalInput, (int)EAbilityInput::Slot2);
			EnhancedInputComponent->BindAction(Action, ETriggerEvent::Completed, this, &ACoreCharacter::SendAbilityLocalInput, (int)EAbilityInput::Slot2);
		}
		// Moving
		if (AName.Contains(TEXT("Move"))) {
			EnhancedInputComponent->BindAction(Action, ETriggerEvent::Triggered, this, &ACoreCharacter::Move);
		}
		// Sprinting
		if (AName.Contains(TEXT("Sprint"))) {
			EnhancedInputComponent->BindAction(Action, ETriggerEvent::Triggered, this, &ACoreCharacter::SendAbilityLocalInput, (int)EAbilityInput::Slot3);
			EnhancedInputComponent->BindAction(Action, ETriggerEvent::Completed, this, &ACoreCharacter::SendAbilityLocalInput, (int)EAbilityInput::Slot3);
		}
		// Looking around
		if (AName.Contains(TEXT("Look"))) {
			EnhancedInputComponent->BindAction(Action, ETriggerEvent::Triggered, this, &ACoreCharacter::Rotate);
		}
	}
}
```
##### Проверка, Confirmable `Fire Ability`
Для проверки кода, создадим новый `GameplayAction`. В `Content Browser` нажимаем на кнопку `Add -> Blueprint Class`.
Выбираем в пункте `All Classes` подкласс `CoreGameplayAbility`.
![916e0fc33b16a60342ebf136593c3e85.png](../images/916e0fc33b16a60342ebf136593c3e85.png)
Называем новый `blueprint` - `GA_Fire`.
![9513f7c22329022d1fca8214d84d3aa1.png](../images/9513f7c22329022d1fca8214d84d3aa1.png)
Открываем действие и устанавливаем слот - `Fire Slot`.
![47a8f08421033076f6c6148c3cf382d5.png](../images/47a8f08421033076f6c6148c3cf382d5.png)
Добавляем в код действия `GA_Fire` следующий граф:
![57d8e706083558c74b7dfd7b4916e39d.png](../images/57d8e706083558c74b7dfd7b4916e39d.png)
Добавляем действие к `ASC` в методе `BeginPlay` персонажа `BP_CoreCharacter`:
![b02ba7f2cdd1396f92115fbc76a5e59a.png](../images/b02ba7f2cdd1396f92115fbc76a5e59a.png)
Запускаем проект и нажимаем кнопку, к которой привязано действие `IA_Fire`. У меня это левая кнопка мыши.
Сначала появится сообщение `Ready To Fire, Ser!`:
![bb04e3bce8a161cedb7794c354c50a0e.png](../images/bb04e3bce8a161cedb7794c354c50a0e.png)
Если повторно нажать левую кнопку мыши, это приведет к срабатыванию действия `Confirm`.
![4cd7a8a5fb07f8affbaa9aae9dc5aecc.png](../images/4cd7a8a5fb07f8affbaa9aae9dc5aecc.png)
В результате на экране, с периодом в `0.2` секунды будет печататься слово `PeW!`.
![114d14e9ebcccf57f340aba39681d20b.png](../images/114d14e9ebcccf57f340aba39681d20b.png)
![194cfed09feee399995682f02fcc65e0.png](../images/194cfed09feee399995682f02fcc65e0.png)
Если нажать правую кнопку мыши, произойдет срабатывание действия `Cancel` и выведется текст `Hold Fire!`.
![15f7897ff01a64c8fff136240ac89e41.png](../images/15f7897ff01a64c8fff136240ac89e41.png)
![719dbd63ddd5869ad0f396c5012cdc61.png](../images/719dbd63ddd5869ad0f396c5012cdc61.png)
### Добавление способностей и эффектов "по умолчанию"
Добавлять способности в `ASC` из события `BeginPlay`, как я это делал выше - крайне неудобно.
![6e812601a1c6032ca53838489cd783b2.png](../images/6e812601a1c6032ca53838489cd783b2.png)
В разы удобнее было бы создать массив с базовым набором автоматически загружаемых способностей.
Массив такого типа можно разместить прямо внутри нашего `Ability System Component`, просто переопределив его класс. Однако разработчики рекомендуют использовать в таких ситуациях `DataAsset`. Это позволит легко переключаться между наборами способностей для разного вида персонажей.
Помимо способностей, в `DataAsset` можно хранить настройки внешнего вида персонажей, их экипировку и любую другую информацию. Оставлю это на будущее.
#### Создаем `Data Asset` для способностей и эффектов
Выполняем команду `Tools -> New C++ Class`, в появившемся окне, на вкладке `All Classes` ищем базовый класс `PrimaryDataAsset`.
![dfff9bef0be2cba3089487d96cd3c44a.png](../images/dfff9bef0be2cba3089487d96cd3c44a.png)
Называем будущий класс `CharacterAbilitiesData` и размещаем его в подпапке `Data`.
![ccd654b1bbfd4e391ffe02578fa69078.png](../images/ccd654b1bbfd4e391ffe02578fa69078.png)
#### Файл `CharacterAbilitiesData.h`
```cpp
#pragma once
#include "CoreMinimal.h"
#include "Engine/DataAsset.h" // core
// GAS
#include "AbilitySystemComponent.h"
#include "GameplayEffect.h"
#include "Abilities/GameplayAbility.h"
#include "NativeGameplayTags.h" // gameplay tags
#include "InputAction.h" // enhanced input
#include "InputMappingContext.h"
// .generated.h
#include "CharacterAbilitiesData.generated.h"
/**
 * Character Initial Abilities
 */
UCLASS()
class GASBASE_API UCharacterAbilitiesData : public UPrimaryDataAsset
{
	GENERATED_BODY()
public:
	// constructor for default values
	UCharacterAbilitiesData(){ /* nothing here yet */ };
	// Default Abilities for this character
	UPROPERTY(BlueprintReadOnly, EditAnywhere, Category = "Gameplay")
	TArray<TSubclassOf<class UCoreGameplayAbility>> Abilities;
	// Default Effects for this character
	UPROPERTY(BlueprintReadOnly, EditAnywhere, Category = "Gameplay")
	TArray<TSubclassOf<UGameplayEffect>> Effects;
};
```
Внутри класса `UCharacterAbilitiesData` я сделал две переменные. Одну - для способностей, которые будут даны персонажу после инициализации, вторую - для хранения эффектов, которые тоже будут даны после инициализации.
Создадим новый `Data Asset` с использованием добавленного нами типа `CharacterAbilitiesData`.
Для этого в `Content Browser` нажмите кнопку `Add` и перейдите в меню `Miscelanneous -> DataAsset`.
![bda17a032cf203de00db72458d35eddc.png](../images/bda17a032cf203de00db72458d35eddc.png)
В появившемся окне ищите класс `CharacterAbilitiesData`.
![fcf810ff83c2e5930d57cbca81dc757e.png](../images/fcf810ff83c2e5930d57cbca81dc757e.png)
Называем созданный файл - `DA_PlayerAbilities`.
![88a45a4e2b7142f71d845da9c6885eec.png](../images/88a45a4e2b7142f71d845da9c6885eec.png)
Открываем. Задаем созданные нами способности.
![947ac861567629f1148a86800c2ccd86.png](../images/947ac861567629f1148a86800c2ccd86.png)
#### Применяем `DataAsset` к персонажу
`DataAsset` с информацией о способностям необходимо загрузить в персонажа. Напишем для этих целей дополнительный метод.
```cpp
//.h
// will apply abilities and effects, that specified in Data asset
UFUNCTION(BlueprintCallable, Category = "Input")
void ApplyAbilitiesData(TSoftObjectPtr<class UCharacterAbilitiesData> Data = nullptr, bool bOnlyEffects = false);
//...
//.cpp
// will apply abilities and effects, that specified in Data asset
void ACoreCharacter::ApplyAbilitiesData(TSoftObjectPtr<class UCharacterAbilitiesData> Data, bool bOnlyEffects)
{
	// check initialisation
	if (GameAbilitySystemComponent == nullptr) {
		return;
	}
	const int Level = 0; // ability grant level
	if (bOnlyEffects == false){
		/**
		 * Add Abilities
		 */
		for (TSubclassOf<UCoreGameplayAbility>& AbilityClass : Data->Abilities) {
            if(AbilityClass == nullptr) continue;
			// form ability info
			FGameplayAbilitySpec Spec{
				AbilityClass
				, Level // character level on that this ability will be granted
				, (int)AbilityClass->GetDefaultObject<UCoreGameplayAbility>()->AbilitySlot // enum - ability slot type
				, this
			};
			GameAbilitySystemComponent->GiveAbility(Spec);
		}
	}
	/**
	 * Add Effects
	 */
	FGameplayEffectContextHandle EffectContext = GameAbilitySystemComponent->MakeEffectContext();
	EffectContext.AddSourceObject(this); // owner of effect
	for (TSubclassOf<UGameplayEffect>& EffectClass : Data->Effects) {
		FGameplayEffectSpecHandle SpecHandle = GameAbilitySystemComponent->MakeOutgoingSpec(EffectClass, Level, EffectContext);
		if (SpecHandle.IsValid()) {
			/*FActiveGameplayEffectHandle GEHandle = */GameAbilitySystemComponent->ApplyGameplayEffectSpecToSelf(*SpecHandle.Data.Get());
		}
	}
}
```
Метод `ApplyAbilitiesData` создан с модификатором `BlueprintCallable`  и потому его можно вызвать из `blueprint` кода.
Используя соответствующую ноду в событии `BeginPlay` персонажа, можно предоставить ему набор событий и эффектов.
![e38f4ceeca2450abe46e422dcca1dd08.png](../images/e38f4ceeca2450abe46e422dcca1dd08.png)
Это, несомненно, важная возможность. Однако мне бы хотелось перименять `GameplayAbilities` к персонажу прямо из параметров класса.
Добавим к классу переменную:
```cpp
// Character Abilities info
UPROPERTY(EditAnywhere, BlueprintReadWrite, Category = Data)
TSoftObjectPtr<class UCharacterAbilitiesData> DefaultAbilitiesInfo = nullptr;
```
Используем данную переменную для загрузки способностей в класс. Сделать это надо в двух методах - `PossessedBy` и `OnRep_PlayerState` следующим образом:
```cpp
//.h
// Character Abilities info
UPROPERTY(EditAnywhere, BlueprintReadWrite, Category = Data)
TSoftObjectPtr<class UCharacterAbilitiesData> DefaultAbilitiesInfo = nullptr;
// Once character posessed
virtual void PossessedBy(AController* NewController) override;
// PlayerState Replication Notification Callback
virtual void OnRep_PlayerState() override;
private:
	bool bDefaultAttributesApplied = false; // true if default attributes are applied
//...
//.cpp
// On Possessed
void ACoreCharacter::PossessedBy(AController* NewController)
{
	Super::PossessedBy(NewController);
	bool bIsPlayer = NewController->IsLocalPlayerController();
	// Enable Camera only when character posessed by player
	if (bIsPlayer) {
		FollowCamera->Activate();
	}
	// init GAS actor info
	if (GameAbilitySystemComponent != nullptr) {
		GameAbilitySystemComponent->InitAbilityActorInfo(/* Owner Actor */ this, /* Avatar Actor */ this);
	}
	// apply default abilities and effects
	if (bDefaultAttributesApplied == false && DefaultAbilitiesInfo != nullptr) {
		ApplyAbilitiesData(DefaultAbilitiesInfo);
		bDefaultAttributesApplied =  true;
	}
}
// PlayerState Replication Notification Callback
void ACoreCharacter::OnRep_PlayerState()
{
	Super::OnRep_PlayerState();
	// init GAS actor info
	if (GameAbilitySystemComponent != nullptr) {
		GameAbilitySystemComponent->InitAbilityActorInfo(/* Owner Actor */ this, /* Avatar Actor */ this);
	}
	// apply default abilities and effects
	if (bDefaultAttributesApplied == false && DefaultAbilitiesInfo != nullptr) {
		ApplyAbilitiesData(DefaultAbilitiesInfo, true); // on replication apply only effects
		bDefaultAttributesApplied = true;
	}
	BindASCInput();
}
```
После добавления кода и компиляции, у персонажа появится параметр `DefaultAbilitiesInfo`. В нем можно указать ассет с базовым набором способностей и эффектов персонажа. Они будут загружены автоматически.
![c386997b8b823c47a24274a78cdd1666.png](../images/c386997b8b823c47a24274a78cdd1666.png)
![09d46080f4b8e434fa0d6a7e4fa4b246.png](../images/09d46080f4b8e434fa0d6a7e4fa4b246.png)
###  Фоновые способности / пассивные способности
Способности такого типа автоматически запускаются, при добавлении в персонажа. Их `blueprint` логика используется для отслеживания определенных событий.
Например:

+ фоновая способность может накладывать на персонаж баф к здоровью, если он бежит,
+ фоновая  способность может защищать игрока ледяным щитом, с шансом в `25%`, при получении урона,
+ фоновая способность может с шансом в `16%`, при атаке наносить `AOE` урон всем окружающим противникам.
![b090ee403c1a408c962626e3310ef294.png](../images/b090ee403c1a408c962626e3310ef294.png)

Это не совсем пассивные эффекты, это активные пассивные эффекты. Такие есть, например, в `Diablo`.
Как создать подобные способности?
В классе способности `CoreGameplayAbility`, который мы реализовали в главе "переопределяем `GameplayAbility`", необходимо реализовать метод `OnAvatarSet`, а так-же `bool` переменную `bRunOnAssign`.
![0bf9327d67af784af197d264d34983b5.png](../images/0bf9327d67af784af197d264d34983b5.png)
В методе `OnAvatarSet` способность будет запускаться автоматически, если параметр `bRunOnAssign == true`.
#### `CoreGameplayAbility.h`
```cpp
#pragma once
#include "CoreMinimal.h"
#include "Abilities/GameplayAbility.h"
#include "AbilitySystemComponent.h"
#include "Enums/AbilityInputEnum.h"
// .generated.h
#include "CoreGameplayAbility.generated.h"
/**
 * Base Ability class with ActionSlot support
 */
UCLASS()
class GASBASE_API UCoreGameplayAbility : public UGameplayAbility
{
	GENERATED_BODY()
public:
	// Ability Action
	UPROPERTY(EditAnywhere, BlueprintReadOnly, Category = "Default")
	EAbilityInput AbilitySlot = EAbilityInput::None;
	// Run Ability once it assigned to avatar
	UPROPERTY(EditAnywhere, BlueprintReadOnly, Category = "Default")
	bool bRunOnAssign = false;
	// Called when the avatar actor is set/changes
	virtual void OnAvatarSet(const FGameplayAbilityActorInfo* ActorInfo, const FGameplayAbilitySpec& Spec);
};
```
#### `CoreGameplayAbility.cpp`
```cpp
#include "Abilities/CoreGameplayAbility.h"
// Called when the avatar actor is set/changes
void UCoreGameplayAbility::OnAvatarSet(const FGameplayAbilityActorInfo* ActorInfo, const FGameplayAbilitySpec& Spec)
{
	Super::OnAvatarSet(ActorInfo, Spec); // call parent
	if (bRunOnAssign == true) { // run ability automatically, once it assigned
		ActorInfo->AbilitySystemComponent->TryActivateAbility(Spec.Handle, false);
	}
}
```
После компиляции кода, у способностей, унаследованных от `UCoreGameplayAbility`, в секции `Default` будет доступен новый параметр - `RunOnAssign`.
![f8f681a420d1d8a72bdb661922d743b9.png](../images/f8f681a420d1d8a72bdb661922d743b9.png)
Если выставить его в `True` - такая способность активируется при присвоении.
#### Создаем фоновую способность
Предлагаю протестировать данный функционал на примере способности, которая будет увеличивать здоровье персонажа, во время бега.
Для начала надо открыть способность бега - `GA_Sprint` и добавить в параметр `Activation Owned Tags`  новый тег `Locomotion.Sprint`.
![d2700c175492aa00f7b060a0aff26de1.png](../images/d2700c175492aa00f7b060a0aff26de1.png)
Для добавления тега достаточно нажать на кнопку `Add New Gameplay Tag` в выпадающем меню параметра .
![ad9842bb62b5edaba2708879f0d58248.png](../images/ad9842bb62b5edaba2708879f0d58248.png)
![e655d98e645da467bd7f14f85b75429a.png](../images/e655d98e645da467bd7f14f85b75429a.png)
Теперь необходимо создать новую способность. В `Content Browser` нажимаем кнопку `Add -> Blueprint Class`, в поле `All Classes` ищем `CoreGameplayAbility`, создаем ассет.
![2afce8d09adc8f4b4b788f712ff361ef.png](../images/2afce8d09adc8f4b4b788f712ff361ef.png)
Называем новую способность - `GA_SprintHealthBoost`. Она будет увеличивать здоровье персонажа во время бега.
![8c624df9bbdc5eeaa07137d4086b4473.png](../images/8c624df9bbdc5eeaa07137d4086b4473.png)
Так-же нам нужен `GameplayEffect`, который будет влиять на показатель здоровья.
В `Content Browser` нажимаем кнопку `Add -> Blueprint Class`, в поле `All Classes` ищем `GameplayEffect`, создаем ассет.
![7f0663b1cb6e6fb9d733822838ce995b.png](../images/7f0663b1cb6e6fb9d733822838ce995b.png)
Называем новый эффект - `GE_SprintHealthBoost` и открываем файл эффекта.
![0fdd388510f17179be5cfad8bbc9ba58.png](../images/0fdd388510f17179be5cfad8bbc9ba58.png)
В параметрах выставляем `Duration Policy` = `Instant`. Наш эффект будет мгновенным.
![c3242496eae432c2545de67770333890.png](../images/c3242496eae432c2545de67770333890.png)
Добавляем два элемента в список `Mofieirs`. Первый - увеличивает параметр `MaxHealth` на переданное значение, второй - увеличивает параметр `Health` на переданное значение.
![ad0101f4b214adf10c6701fd07707cec.png](../images/ad0101f4b214adf10c6701fd07707cec.png)
Значение передается в эффект через `Set by Caller Magnitude` по тегу `Buff.Health.Increased.Value`. Тег можно добавить следующим образом:
![6fd73cbbc9b6d2cd27d1ce4dbfd3b338.png](../images/6fd73cbbc9b6d2cd27d1ce4dbfd3b338.png)
Полученный эффект, при наложении на персонажа, будет увеличивать показатель здоровья на переданное через тег `Buff.Health.Increased.Value` значение.
Возвращаемся к способности `GA_SprintHealthBoost`. Открываем `blueprint` способности и добавляем туда следующий код:
![ebe59bae3f7fc7afa901ff86eff14158.png](../images/ebe59bae3f7fc7afa901ff86eff14158.png)
Ноды `Wait Gameplay Tag Add` и `Wait Gameplay Tag Remove` активируются, когда персонаж с этой способностью получает или теряет `GameplayTag`. В нашем случае это `Locomotion.Sprint`. Данный тег накладывается на персонажа способностью `Sprint`, которую мы изменили чуть выше. Активация ожидающих нод приводит к наложению на персонажа эффекта, который увеличивает здоровье на `Boost Value` единиц.
Когда тег `Locomotion.Sprint` удаляется, мы так-же вычитаем значение `Boost Value` из текущего значения здоровья, используя тот-же самый эффект.
В моем случае `Boost Value` имеет значение в `10`, то есть здоровье будет увеличиваться на `10` единиц при беге.
![3e9ec4c3f771a4a7b0698889b2e6ed7f.png](../images/3e9ec4c3f771a4a7b0698889b2e6ed7f.png)
Устанавливаем для способности `GA_SprintHealthBoost` параметр `RunOnAssign` в `true`.
![511d7b1f7dfff97827a7991ecda8b6e7.png](../images/511d7b1f7dfff97827a7991ecda8b6e7.png)
Теперь наша способность будет постоянно активироваться, при беге персонажа.
Добавляем способности `GA_Sprint` и `GA_SprintHealthBoost` в `DA_PlayerAbilities`, чтобы они загружались в класс "по умолчанию".
![23940fc82c902ee1306fd62287540d1b.png](../images/23940fc82c902ee1306fd62287540d1b.png)
Запускаем проект, вбиваем в консоль команду `showdebug AbilitySystem` и следим за изменением характеристик.
До бега `Health - 70, Max Health - 120`.
![1ee4181677622f494703200069e76d37.png](../images/1ee4181677622f494703200069e76d37.png)
В беге  `Health - 80, Max Health - 130`.
![4d9bd307f21eaff506c65d0fff3ef752.png](../images/4d9bd307f21eaff506c65d0fff3ef752.png)
## Отслеживание изменений в аттрибутах
Как отследить изменение уровня здоровья или выносливости из кода персонажа?
Для отслеживания аттрибутов `GAS` в `blueprint` есть два способа реализации.
### Отслеживаем аттрибуты через `BlueprintImplementableEvent`
Это самый простой способ.
Добавляем два метода с модификатором `BlueprintImplementableEvent` для двух характеристик (выносливости и  здоровья) в класс персонажа `CoreCharacter.h`.
```cpp
/** =======================================================
 * Attribute Changes
 */
// event, that will be called once HealthChanged
UFUNCTION(BlueprintImplementableEvent)
void OnHealthChanged(float OldValue, float NewValue);
UFUNCTION(BlueprintImplementableEvent)
void OnStaminaChanged(float OldValue, float NewValue);
```
Реализацию этим событиям писать не надо, потому что она будет сгенерирована автоматически системой `UHT`.
В методе `BeginPlay` вызываем каждую из функций внутри лямбды, связанной с изменением аттрибута:
```cpp
// Called when the game starts or when spawned
void ACoreCharacter::BeginPlay()
{
    // ....
	/**
	 * Bind Attribute Changes
	 */
	// bind health changes
	GameAbilitySystemComponent->GetGameplayAttributeValueChangeDelegate(AttributeSet->GetHealthAttribute())
	.AddLambda([this](const FOnAttributeChangeData& Data){
		OnHealthChanged(Data.OldValue, Data.NewValue); // call BlueprintImplementableEvent
	});
	// bind stamina changes
	GameAbilitySystemComponent->GetGameplayAttributeValueChangeDelegate(AttributeSet->GetStaminaAttribute())
	.AddLambda([this](const FOnAttributeChangeData& Data){
		OnStaminaChanged(Data.OldValue, Data.NewValue); // call BlueprintImplementableEvent
	});
}
```
После компиляции кода у вас появится возможность добавить новые `Event'ы` в `blueprint` персонажа: `OnStaminaChanged` и `OnHealthChanged`. Они срабатывают, когда изменяется соответствующая характеристика.
![6e6e2ac3c1dbbc5d1c079e9682c8395c.png](../images/6e6e2ac3c1dbbc5d1c079e9682c8395c.png)
Я написал небольшой эффект. который расходует выносливость персонажа при беге.
![6356d5e6c8c8ede2cf3f65c1553e0a89.png](../images/6356d5e6c8c8ede2cf3f65c1553e0a89.png)
Данный эффект вызывается из способности `GA_Sprint`, к как ее `cost`.
![3c65a7142460e4864d2abe386cd5cd40.png](../images/3c65a7142460e4864d2abe386cd5cd40.png)
![a61810cf30ef6378f6539bfec8e49095.png](../images/a61810cf30ef6378f6539bfec8e49095.png)
Из-за изменения показателя `Stamina`, происходит вызов события `OnStaminaChanged`, что приводит к печати текста на экране.
![cc5cc070f7a3ba6ccba559a5ff4e2f8a.png](../images/cc5cc070f7a3ba6ccba559a5ff4e2f8a.png)
![266b108c975119d13c43af785a780ba6.png](../images/266b108c975119d13c43af785a780ba6.png)
### Использование `Async Task` для отслеживания аттрибутов
Мы можем написать `Async Task` для отслеживания изменений в аттрибутах. (подробнее про `Async Task` можно прочитать в одной из предыдущих статей книги)
Автор репозитория `GAS Documentation` привел три примера `AsyncTask` - [`AsyncTaskAttributeChanged`](https://github.com/tranek/GASDocumentation/blob/master/Source/GASDocumentation/Public/Characters/Abilities/AsyncTaskAttributeChanged.h), [`AsyncTaskCooldownChanged`](https://github.com/tranek/GASDocumentation/blob/master/Source/GASDocumentation/Public/Characters/Abilities/AsyncTaskCooldownChanged.h) и [`AsyncTaskEffectStackChanged`](https://github.com/tranek/GASDocumentation/blob/master/Source/GASDocumentation/Public/Characters/Abilities/AsyncTaskEffectStackChanged.h).
Мы можем перенести их в свой проект без каких-либо сложностей.
В движке выполняем команду `Tools -> New C++ Class`.
В появившемся окне переходим на вкладку `All Classes` и выбираем класс - `BlueprintAsyncActionBase`.
![781cace0a0e027d79f6f2ea3add3ae6f.png](../images/781cace0a0e027d79f6f2ea3add3ae6f.png)
Называем класс `AttributeChangedAsyncAction` и добавляем его в каталог `AsyncActions`.
![fa44d4a737cee673a23deb2607cc8486.png](../images/fa44d4a737cee673a23deb2607cc8486.png)
![69926bef5d7c5a6d239aac13cbcf66da.png](../images/69926bef5d7c5a6d239aac13cbcf66da.png)
Добавляем код
#### `AttributeChangedAsyncAction.h`
```cpp
#pragma once
#include "CoreMinimal.h"
#include "Kismet/BlueprintAsyncActionBase.h"
#include "AbilitySystemComponent.h"
// .generated.h
#include "AttributeChangedAsyncAction.generated.h"
DECLARE_DYNAMIC_MULTICAST_DELEGATE_ThreeParams(FOnAttributeChangedPin, FGameplayAttribute, Attribute, float, NewValue, float, OldValue);
/**
 * Blueprint node to automatically register a listener for all attribute changes in an AbilitySystemComponent.
 * Useful to use in UI.
 */
UCLASS(BlueprintType, meta = (ExposedAsyncProxy = AsyncTask))
class GASBASE_API UAttributeChangedAsyncAction : public UBlueprintAsyncActionBase
{
	GENERATED_BODY()
public:
	UPROPERTY(BlueprintAssignable)
	FOnAttributeChangedPin AttributeChanged; // this will create pin
	// Listens for an attribute changing.
	UFUNCTION(BlueprintCallable, meta = (BlueprintInternalUseOnly = "true"))
	static UAttributeChangedAsyncAction* ListenForAttributeChange(UAbilitySystemComponent* AbilitySystemComponent, FGameplayAttribute Attribute);
	// Listens for an attribute changing.
	// Version that takes in an array of Attributes. Check the Attribute output for which Attribute changed.
	UFUNCTION(BlueprintCallable, meta = (BlueprintInternalUseOnly = "true"))
	static UAttributeChangedAsyncAction* ListenForAttributesChange(UAbilitySystemComponent* AbilitySystemComponent, TArray<FGameplayAttribute> Attributes);
	// You must call this function manually when you want the AsyncTask to end.
	// For UMG Widgets, you would call it in the Widget's Destruct event.
	UFUNCTION(BlueprintCallable)
	void EndTask();
protected:
	UPROPERTY()
	UAbilitySystemComponent* ASC;
	FGameplayAttribute AttributeToListenFor;
	TArray<FGameplayAttribute> AttributesToListenFor;
	// this will be bound to GetGameplayAttributeValueChangeDelegate
	void OnAttributeChanged(const FOnAttributeChangeData& Data);
};
```
#### `AttributeChangedAsyncAction.cpp`
```cpp
#include "AsyncActions/AttributeChangedAsyncAction.h"
// Listens for an attribute changing.
UAttributeChangedAsyncAction* UAttributeChangedAsyncAction::ListenForAttributeChange(UAbilitySystemComponent* AbilitySystemComponent, FGameplayAttribute Attribute)
{
	UAttributeChangedAsyncAction* WaitForAttributeChangedTask = NewObject<UAttributeChangedAsyncAction>();
	WaitForAttributeChangedTask->ASC = AbilitySystemComponent;
	WaitForAttributeChangedTask->AttributeToListenFor = Attribute;
	if (!IsValid(AbilitySystemComponent) || !Attribute.IsValid())
	{
		WaitForAttributeChangedTask->RemoveFromRoot();
		return nullptr;
	}
	// bind AttributeChanged
	AbilitySystemComponent->GetGameplayAttributeValueChangeDelegate(Attribute).AddUObject(WaitForAttributeChangedTask, &UAttributeChangedAsyncAction::OnAttributeChanged);
	return WaitForAttributeChangedTask;
}
// Listens for an attribute changing.
// Version that takes in an array of Attributes. Check the Attribute output for which Attribute changed.
UAttributeChangedAsyncAction* UAttributeChangedAsyncAction::ListenForAttributesChange(UAbilitySystemComponent* AbilitySystemComponent, TArray<FGameplayAttribute> Attributes)
{
	UAttributeChangedAsyncAction* WaitForAttributeChangedTask = NewObject<UAttributeChangedAsyncAction>();
	WaitForAttributeChangedTask->ASC = AbilitySystemComponent;
	WaitForAttributeChangedTask->AttributesToListenFor = Attributes;
	if (!IsValid(AbilitySystemComponent) || Attributes.Num() < 1)
	{
		WaitForAttributeChangedTask->RemoveFromRoot();
		return nullptr;
	}
	// for each attribute
	for (FGameplayAttribute Attribute : Attributes)
	{
		// bind AttributeChanged
		AbilitySystemComponent->GetGameplayAttributeValueChangeDelegate(Attribute).AddUObject(WaitForAttributeChangedTask, &UAttributeChangedAsyncAction::OnAttributeChanged);
	}
	return WaitForAttributeChangedTask;
}
// You must call this function manually when you want the AsyncTask to end.
// For UMG Widgets, you would call it in the Widget's Destruct event.
void UAttributeChangedAsyncAction::EndTask()
{
	if (IsValid(ASC))
	{
		ASC->GetGameplayAttributeValueChangeDelegate(AttributeToListenFor).RemoveAll(this);
		for (FGameplayAttribute Attribute : AttributesToListenFor)
		{
			ASC->GetGameplayAttributeValueChangeDelegate(Attribute).RemoveAll(this);
		}
	}
	SetReadyToDestroy();
	MarkAsGarbage();
}
// this will be bound to GetGameplayAttributeValueChangeDelegate
void UAttributeChangedAsyncAction::OnAttributeChanged(const FOnAttributeChangeData& Data)
{
	AttributeChanged.Broadcast(Data.Attribute, Data.NewValue, Data.OldValue);
}
```
После компиляции данного кода, вы сможете использовать две новые `blueprint` ноды: `Listen for Attribute Change` и `Listen for Attributes Change`.
![6e6ecc8007a3f1f2784ded9a42a0bbf5.png](../images/6e6ecc8007a3f1f2784ded9a42a0bbf5.png)
Данные ноды будут срабатывать всякий раз при изменении выбранного аттрибута или списка аттрибутов.
Аналогичным образом переносятся действия `AsyncTaskCooldownChanged` и `AsyncTaskEffectStackChanged`.
#### `CooldownChangedAsyncAction.h`
```cpp
// Fill out your copyright notice in the Description page of Project Settings.
#pragma once
#include "CoreMinimal.h"
#include "Kismet/BlueprintAsyncActionBase.h"
#include "AbilitySystemComponent.h"
#include "GameplayTagContainer.h"
// .generated.h
#include "CooldownChangedAsyncAction.generated.h"
DECLARE_DYNAMIC_MULTICAST_DELEGATE_ThreeParams(FOnCooldownChangedPin, FGameplayTag, CooldownTag, float, TimeRemaining, float, Duration);
/**
 * Blueprint node to automatically register a listener for changes (Begin and End) to an array of Cooldown tags.
 * Useful to use in UI.
 */
UCLASS(BlueprintType, meta = (ExposedAsyncProxy = AsyncTask))
class GASBASE_API UCooldownChangedAsyncAction : public UBlueprintAsyncActionBase
{
	GENERATED_BODY()
public:
	UPROPERTY(BlueprintAssignable)
	FOnCooldownChangedPin CooldownBegin;
	UPROPERTY(BlueprintAssignable)
	FOnCooldownChangedPin CooldownEnd;
	// Listens for changes (Begin and End) to cooldown GameplayEffects based on the cooldown tag.
	// UseServerCooldown determines if the Sever's cooldown is returned in addition to the local predicted cooldown.
	// If using ServerCooldown, TimeRemaining and Duration will return -1 to signal local predicted cooldown has begun.
	UFUNCTION(BlueprintCallable, meta = (BlueprintInternalUseOnly = "true"))
	static UCooldownChangedAsyncAction* ListenForCooldownChange(UAbilitySystemComponent* AbilitySystemComponent, FGameplayTagContainer InCooldownTags, bool InUseServerCooldown);
	// You must call this function manually when you want the AsyncTask to end.
	// For UMG Widgets, you would call it in the Widget's Destruct event.
	UFUNCTION(BlueprintCallable)
	void EndTask();
protected:
	UPROPERTY()
	UAbilitySystemComponent* ASC;
	FGameplayTagContainer CooldownTags;
	bool UseServerCooldown;
	// will be bound to OnActiveGameplayEffectAddedDelegateToSelf
	virtual void OnActiveGameplayEffectAddedCallback(UAbilitySystemComponent* Target, const FGameplayEffectSpec& SpecApplied, FActiveGameplayEffectHandle ActiveHandle);
	// Callback for RegisterGameplayTagEvent Added or Removed
	virtual void CooldownTagAddedRemoved(const FGameplayTag CooldownTag, int32 NewCount);
	// calculates cooldown for tag
	bool GetCooldownRemainingForTag(FGameplayTagContainer InCooldownTags, float& TimeRemaining, float& CooldownDuration);
};
```
#### `CooldownChangedAsyncAction.h`
```cpp
// Fill out your copyright notice in the Description page of Project Settings.
#include "AsyncActions/CooldownChangedAsyncAction.h"
// Listens for changes (Begin and End) to cooldown GameplayEffects based on the cooldown tag.
// UseServerCooldown determines if the Sever's cooldown is returned in addition to the local predicted cooldown.
// If using ServerCooldown, TimeRemaining and Duration will return -1 to signal local predicted cooldown has begun.
UCooldownChangedAsyncAction* UCooldownChangedAsyncAction::ListenForCooldownChange(UAbilitySystemComponent* AbilitySystemComponent, FGameplayTagContainer InCooldownTags, bool InUseServerCooldown)
{
	UCooldownChangedAsyncAction* ListenForCooldownChange = NewObject<UCooldownChangedAsyncAction>();
	ListenForCooldownChange->ASC = AbilitySystemComponent;
	ListenForCooldownChange->CooldownTags = InCooldownTags;
	ListenForCooldownChange->UseServerCooldown = InUseServerCooldown;
	if (!IsValid(AbilitySystemComponent) || InCooldownTags.Num() < 1)
	{
		ListenForCooldownChange->EndTask();
		return nullptr;
	}
	// bind OnActiveGameplayEffectAddedCallback
	AbilitySystemComponent->OnActiveGameplayEffectAddedDelegateToSelf.AddUObject(ListenForCooldownChange, &UCooldownChangedAsyncAction::OnActiveGameplayEffectAddedCallback);
	TArray<FGameplayTag> CooldownTagArray;
	InCooldownTags.GetGameplayTagArray(CooldownTagArray);
	for (FGameplayTag CooldownTag : CooldownTagArray)
	{
		// bind tag Added or Removed
		AbilitySystemComponent->RegisterGameplayTagEvent(CooldownTag, EGameplayTagEventType::NewOrRemoved).AddUObject(ListenForCooldownChange, &UCooldownChangedAsyncAction::CooldownTagAddedRemoved);
	}
	return ListenForCooldownChange;
}
// You must call this function manually when you want the AsyncTask to end.
// For UMG Widgets, you would call it in the Widget's Destruct event.
void UCooldownChangedAsyncAction::EndTask()
{
	if (IsValid(ASC))
	{
		ASC->OnActiveGameplayEffectAddedDelegateToSelf.RemoveAll(this);
		TArray<FGameplayTag> CooldownTagArray;
		CooldownTags.GetGameplayTagArray(CooldownTagArray);
		for (FGameplayTag CooldownTag : CooldownTagArray)
		{
			ASC->RegisterGameplayTagEvent(CooldownTag, EGameplayTagEventType::NewOrRemoved).RemoveAll(this);
		}
	}
	SetReadyToDestroy();
	MarkAsGarbage();
}
// will be bound to OnActiveGameplayEffectAddedDelegateToSelf
void UCooldownChangedAsyncAction::OnActiveGameplayEffectAddedCallback(UAbilitySystemComponent* Target, const FGameplayEffectSpec& SpecApplied, FActiveGameplayEffectHandle ActiveHandle)
{
	FGameplayTagContainer AssetTags;
	SpecApplied.GetAllAssetTags(AssetTags);
	FGameplayTagContainer GrantedTags;
	SpecApplied.GetAllGrantedTags(GrantedTags);
	TArray<FGameplayTag> CooldownTagArray;
	CooldownTags.GetGameplayTagArray(CooldownTagArray);
	for (FGameplayTag CooldownTag : CooldownTagArray)
	{
		if (AssetTags.HasTagExact(CooldownTag) || GrantedTags.HasTagExact(CooldownTag))
		{
			float TimeRemaining = 0.0f;
			float Duration = 0.0f;
			// Expecting cooldown tag to always be first tag
			FGameplayTagContainer CooldownTagContainer(GrantedTags.GetByIndex(0));
			GetCooldownRemainingForTag(CooldownTagContainer, TimeRemaining, Duration);
			if (ASC->GetOwnerRole() == ROLE_Authority)
			{
				// Player is Server
				CooldownBegin.Broadcast(CooldownTag, TimeRemaining, Duration);
			}
			else if (!UseServerCooldown && SpecApplied.GetContext().GetAbilityInstance_NotReplicated())
			{
				// Client using predicted cooldown
				CooldownBegin.Broadcast(CooldownTag, TimeRemaining, Duration);
			}
			else if (UseServerCooldown && SpecApplied.GetContext().GetAbilityInstance_NotReplicated() == nullptr)
			{
				// Client using Server's cooldown. This is Server's corrective cooldown GE.
				CooldownBegin.Broadcast(CooldownTag, TimeRemaining, Duration);
			}
			else if (UseServerCooldown && SpecApplied.GetContext().GetAbilityInstance_NotReplicated())
			{
				// Client using Server's cooldown but this is predicted cooldown GE.
				// This can be useful to gray out abilities until Server's cooldown comes in.
				CooldownBegin.Broadcast(CooldownTag, -1.0f, -1.0f);
			}
		}
	}
}
// Callback for RegisterGameplayTagEvent Added or Removed
void UCooldownChangedAsyncAction::CooldownTagAddedRemoved(const FGameplayTag CooldownTag, int32 NewCount)
{
	if (NewCount == 0)
	{
		CooldownEnd.Broadcast(CooldownTag, -1.0f, -1.0f);
	}
}
// calculates cooldown for tag
bool UCooldownChangedAsyncAction::GetCooldownRemainingForTag(FGameplayTagContainer InCooldownTags, float& TimeRemaining, float& CooldownDuration)
{
	if (IsValid(ASC) && InCooldownTags.Num() > 0)
	{
		TimeRemaining = 0.f;
		CooldownDuration = 0.f;
		FGameplayEffectQuery const Query = FGameplayEffectQuery::MakeQuery_MatchAnyOwningTags(InCooldownTags);
		TArray< TPair<float, float> > DurationAndTimeRemaining = ASC->GetActiveEffectsTimeRemainingAndDuration(Query);
		if (DurationAndTimeRemaining.Num() > 0)
		{
			int32 BestIdx = 0;
			float LongestTime = DurationAndTimeRemaining[0].Key;
			for (int32 Idx = 1; Idx < DurationAndTimeRemaining.Num(); ++Idx)
			{
				if (DurationAndTimeRemaining[Idx].Key > LongestTime)
				{
					LongestTime = DurationAndTimeRemaining[Idx].Key;
					BestIdx = Idx;
				}
			}
			TimeRemaining = DurationAndTimeRemaining[BestIdx].Key;
			CooldownDuration = DurationAndTimeRemaining[BestIdx].Value;
			return true;
		}
	}
 
	return false;
}
```
![a92cb1c4d2b0f167dae7a327132ad771.png](../images/a92cb1c4d2b0f167dae7a327132ad771.png)
#### `EffectStackChangedAsyncAction.h`
```cpp
#pragma once
#include "CoreMinimal.h"
#include "Kismet/BlueprintAsyncActionBase.h"
#include "AbilitySystemComponent.h"
// .generated.h
#include "EffectStackChangedAsyncAction.generated.h"
DECLARE_DYNAMIC_MULTICAST_DELEGATE_FourParams(FOnGameplayEffectStackChangedPin, FGameplayTag, EffectGameplayTag, FActiveGameplayEffectHandle, Handle, int32, NewStackCount, int32, OldStackCount);
/**
 * Blueprint node to automatically register a listener for changes to a GameplayEffect's stack count based on an Asset or Granted tag on the Effect.
 * Useful to use in UI.
 */
UCLASS(BlueprintType, meta = (ExposedAsyncProxy = AsyncTask))
class GASBASE_API UEffectStackChangedAsyncAction : public UBlueprintAsyncActionBase
{
	GENERATED_BODY()
	UPROPERTY(BlueprintAssignable, Meta=(DisplayName = "EffectStackChange"))
	FOnGameplayEffectStackChangedPin OnGameplayEffectStackChange;
	UFUNCTION(BlueprintCallable, meta = (BlueprintInternalUseOnly = "true"))
	static UEffectStackChangedAsyncAction* ListenForGameplayEffectStackChange(UAbilitySystemComponent* AbilitySystemComponent, FGameplayTag EffectGameplayTag);
	// You must call this function manually when you want the AsyncTask to end.
	// For UMG Widgets, you would call it in the Widget's Destruct event.
	UFUNCTION(BlueprintCallable)
	void EndTask();
protected:
	UPROPERTY()
	UAbilitySystemComponent* ASC;
	FGameplayTag EffectGameplayTag;
	virtual void OnActiveGameplayEffectAddedCallback(UAbilitySystemComponent* Target, const FGameplayEffectSpec& SpecApplied, FActiveGameplayEffectHandle ActiveHandle);
	virtual void OnRemoveGameplayEffectCallback(const FActiveGameplayEffect& EffectRemoved);
	virtual void GameplayEffectStackChanged(FActiveGameplayEffectHandle EffectHandle, int32 NewStackCount, int32 PreviousStackCount);
};
```
#### `EffectStackChangedAsyncAction.cpp`
```cpp
#include "AsyncActions/EffectStackChangedAsyncAction.h"
UEffectStackChangedAsyncAction* UEffectStackChangedAsyncAction::ListenForGameplayEffectStackChange(UAbilitySystemComponent* AbilitySystemComponent, FGameplayTag InEffectGameplayTag)
{
	UEffectStackChangedAsyncAction* ListenForGameplayEffectStackChange = NewObject<UEffectStackChangedAsyncAction>();
	ListenForGameplayEffectStackChange->ASC = AbilitySystemComponent;
	ListenForGameplayEffectStackChange->EffectGameplayTag = InEffectGameplayTag;
	if (!IsValid(AbilitySystemComponent) || !InEffectGameplayTag.IsValid())
	{
		ListenForGameplayEffectStackChange->EndTask();
		return nullptr;
	}
	AbilitySystemComponent->OnActiveGameplayEffectAddedDelegateToSelf.AddUObject(ListenForGameplayEffectStackChange, &UEffectStackChangedAsyncAction::OnActiveGameplayEffectAddedCallback);
	AbilitySystemComponent->OnAnyGameplayEffectRemovedDelegate().AddUObject(ListenForGameplayEffectStackChange, &UEffectStackChangedAsyncAction::OnRemoveGameplayEffectCallback);
	return ListenForGameplayEffectStackChange;
}
void UEffectStackChangedAsyncAction::EndTask()
{
	if (IsValid(ASC))
	{
		ASC->OnActiveGameplayEffectAddedDelegateToSelf.RemoveAll(this);
		ASC->OnAnyGameplayEffectRemovedDelegate().RemoveAll(this);
	}
	SetReadyToDestroy();
	MarkAsGarbage();
}
void UEffectStackChangedAsyncAction::OnActiveGameplayEffectAddedCallback(UAbilitySystemComponent* Target, const FGameplayEffectSpec& SpecApplied, FActiveGameplayEffectHandle ActiveHandle)
{
	FGameplayTagContainer AssetTags;
	SpecApplied.GetAllAssetTags(AssetTags);
	FGameplayTagContainer GrantedTags;
	SpecApplied.GetAllGrantedTags(GrantedTags);
	if (AssetTags.HasTagExact(EffectGameplayTag) || GrantedTags.HasTagExact(EffectGameplayTag))
	{
		ASC->OnGameplayEffectStackChangeDelegate(ActiveHandle)->AddUObject(this, &UEffectStackChangedAsyncAction::GameplayEffectStackChanged);
		OnGameplayEffectStackChange.Broadcast(EffectGameplayTag, ActiveHandle, 1, 0);
	}
}
void UEffectStackChangedAsyncAction::OnRemoveGameplayEffectCallback(const FActiveGameplayEffect& EffectRemoved)
{
	FGameplayTagContainer AssetTags;
	EffectRemoved.Spec.GetAllAssetTags(AssetTags);
	FGameplayTagContainer GrantedTags;
	EffectRemoved.Spec.GetAllGrantedTags(GrantedTags);
	if (AssetTags.HasTagExact(EffectGameplayTag) || GrantedTags.HasTagExact(EffectGameplayTag))
	{
		OnGameplayEffectStackChange.Broadcast(EffectGameplayTag, EffectRemoved.Handle, 0, 1);
	}
}
void UEffectStackChangedAsyncAction::GameplayEffectStackChanged(FActiveGameplayEffectHandle EffectHandle, int32 NewStackCount, int32 PreviousStackCount)
{
	OnGameplayEffectStackChange.Broadcast(EffectGameplayTag, EffectHandle, NewStackCount, PreviousStackCount);
}
```
![a1114b18d8f90695022fdb759231c65e.png](../images/a1114b18d8f90695022fdb759231c65e.png)
## Добавляем `GUI`
Теперь, когда мы можем отслеживать изменения аттрибутов - самое время добавить интерфейс для нашего проекта.
В `Content Browser` нажимаем на кнопку `Add -> User Interface -> Widget Blueprint` чтобы создать новый виджет.
![70c1be32eea040c52a5c058acefbc765.png](../images/70c1be32eea040c52a5c058acefbc765.png)
Называем виджет - `WB_Hud`.
![6ec53647b9c6d504adcc64720cc1fcbf.png](../images/6ec53647b9c6d504adcc64720cc1fcbf.png)
Откройте виджет.
Добавляем в `Designer` виджета два прогресс бара для отображения здоровья и выносливости, а так-же текстовые метки, которые будут отображать цифровое значение этих параметров:
![6be26d52657e6de2516331c9db90d076.png](../images/6be26d52657e6de2516331c9db90d076.png)
Для `Progress Bar` выставляем  параметр `Percent` в `1`.
![e34591e455963165fb75909fbe9380b9.png](../images/e34591e455963165fb75909fbe9380b9.png)
![8aeff445033b2fab7a009da589d663d2.png](../images/8aeff445033b2fab7a009da589d663d2.png)
В `Graph` виджета добавьте переменную `ASC` типа `AbilitySystemComponent`, она должна иметь параметры `Instance Editable` и `Expose On Spawn`.
![e4c0448bf940e492d842c8826ae115b9.png](../images/e4c0448bf940e492d842c8826ae115b9.png)
Далее добавляем логику для обновления обоих `Progress` баров и информации о показателях здоровья.
![377ef4448f131615863664ef4942f372.png](../images/377ef4448f131615863664ef4942f372.png)
![f4c113b32f6d3c53c05294284873f2fa.png](../images/f4c113b32f6d3c53c05294284873f2fa.png)
![1cd2a6d78f1a5149c3e7e04886c3f26a.png](../images/1cd2a6d78f1a5149c3e7e04886c3f26a.png)
![e58b4a5c8e38e1f0180c9c114ae4c1ec.png](../images/e58b4a5c8e38e1f0180c9c114ae4c1ec.png)
![bc04f9d5c4cc6f5e2810814728f8e19b.png](../images/bc04f9d5c4cc6f5e2810814728f8e19b.png)
Чтобы виджет с показателями здоровья и выносливости отображался на главном экране, откроем `blueprint` нашего персонажа - `BP_CoreCharacter`.
В событии `BeginPlay` реализуйте логику создания виджета `WB_Hud` и добавления его в `Viewport`.
![3f2676fa0dbff8ce982d483d0ba7888e.png](../images/3f2676fa0dbff8ce982d483d0ba7888e.png)
В запущенном проекте виджет выглядит так:
![399bcd05a486acfa3b27a788de220414.png](../images/399bcd05a486acfa3b27a788de220414.png)
Теперь, когда у нас есть визуальное представление характеристик, можно переходить к изучению эффектов.
## `GameplayEffects`
`GameplayEffects` используются чтобы манипулировать аттрибутами в `Ability System Component`. Через эффекты разработчик может накладывать бафы, дебафы, эффекты периодического урона или исцеления.
### Эффект, периодически расходующий выносливость при беге
Самый элементарный пример эффекта - расход выносливости при беге. Данный эффект может быть создан двумя способами - через применение `Effect Cost` и через периодический эффект. В этой главе поговорим о периодическом эффекте расхода выносливости при беге.
Чтобы создать эффект, в `Content Browser` нажмите на кнопку `Add -> Blueprint Class`.
В появившемся окне выбора класса найдите `GameplayEffect`.
![d5fae89c5eebee5cda9c94e3cbdc76c3.png](../images/d5fae89c5eebee5cda9c94e3cbdc76c3.png)
Называем эффект `GE_SprintDrain`.
![5ca4beb8430e3021c94f6bfbdc8975b6.png](../images/5ca4beb8430e3021c94f6bfbdc8975b6.png)
Открываем.
На панеди `Details` вы можете видеть основное меню настройки эффекта. Установите в этом меню следующие параметры:
![24a13ae32dc8d1a8441530a3edae056e.png](../images/24a13ae32dc8d1a8441530a3edae056e.png)
Параметр `Duration` отвечает за время активности эффекта. Я выставил там значение `Infinite`, потому что эффект будет накладываться и убираться по нажатию клавиши бега.
Параметр `Modifiers` содержит массив модификаторов, которые будут применены к аттрибутам персонажа, при активации эффекта. В список модификаторов необходимо добавить всего один - добавляющий к аттрибуту `Stamina` значение `-1`. Это будет убавлять выносливость при наложении модификатора.
Параметр `Period` отвечает за периодичность наложения модификаторов. В этом параметре необходимо установить значение в `0.1` - именно с такой периодичностью параметр `Stamina` будет расходоваться во время бега.
Эффект готов. Теперь необходимо его наложить из `Gameplay Action`, которое отвечает за `Sprint`, у нас это `GA_Sprint`.
![63476e3068e4a6fe0cceba094bce1548.png](../images/63476e3068e4a6fe0cceba094bce1548.png)
Открываем `blueprint` действия. Добавляем туда следующий код:
![4caacaaecd64cf92d13d9cde8fc0c17f.png](../images/4caacaaecd64cf92d13d9cde8fc0c17f.png)
![0f6edcfb4bc99613ee17b9b018873054.png](../images/0f6edcfb4bc99613ee17b9b018873054.png)
Эффект применяется нодой `Apply Gameplay Effect To Owner`, убирается нодой - `Remove Gameplay Effect From Owner`.
За уровнем выносливости следит нода `Wait Attribute Change Treshold`. За состоянием нажатия клавиши следит нода `Wait Input Release`. Если выносливость кончилась или клавиша бега была отпущена - эффект расхода выносливости спадает, а скорость персонажа восстанавливает свое значение.
Метод работает, но у него есть один крайне серьезный минус. Чтобы понять это - достаточно подпрыгнуть во время бега. Выносливость продолжит расходоваться, даже если персонаж в воздухе.
![4b03a2d92aa2eb4f592e3d0669be0276.png](../images/4b03a2d92aa2eb4f592e3d0669be0276.png)
Очевидно, что во время прыжка выносливость расходоваться не должна.
Для этого можно использовать `Ongoing Tag Requirements -> Ignore Tags`. Если указать тут тег `Locomotion.Jump`, то при прыжках выносливость расходоваться не будет.
![cf2d287e6ad91f19cfa86b54dd9e7405.png](../images/cf2d287e6ad91f19cfa86b54dd9e7405.png)
Проблема наступит тогда, когда персонаж упрется в стену и не будет фактически перемещаться, что все равно будет расходовать его выносливость. И эту проблему тегами не исправить.
![a02c3023753e3ac5d30eacc47c4e6440.png](../images/a02c3023753e3ac5d30eacc47c4e6440.png)
Рассмотрим альтернативный способ расхода выносливости.
### Расход выносливости за счет `Effect Cost`
У `GameplayAbility` есть параметр `Effect Cost`.
![07e9a5fa0e6b3859d795f9c3152abfc0.png](../images/07e9a5fa0e6b3859d795f9c3152abfc0.png)
В это поле надо добавлять `Duration = Instant` эффект. Данный эффект применяется на способность всякий раз, как вызывается нода `Commit Ability`.
![709584b40119f8ebb5908eeeb72f760d.png](../images/709584b40119f8ebb5908eeeb72f760d.png)
Создаем новый эффект для `Effect Cost`.
В `Content Browser` нажмите на кнопку `Add -> Blueprint Class`.
В появившемся окне выбора класса найдите `GameplayEffect`.
![d5fae89c5eebee5cda9c94e3cbdc76c3.png](../images/d5fae89c5eebee5cda9c94e3cbdc76c3.png)
Называем эффект `GE_SprintCost`.
![75585c5639f4cde5a3d3ba723cc00ce8.png](../images/75585c5639f4cde5a3d3ba723cc00ce8.png)
Открываем.
На панели `Details` вы можете видеть основное меню настройки эффекта. Установите в этом меню следующие параметры:
![068e90970a296c8d772db7296c25d0f4.png](../images/068e90970a296c8d772db7296c25d0f4.png)
`Duration` - `Instant`, потому что все `Cost` эффекты применяются единовременно и `Modifier Magnitude` -> `Add` -> `-1`, именно столько выносливости будет отниматься у персонажа, при применении эффекта.
Теперь идем в `GameplayAction` с логикой бега - `GA_Sprint` и устанавливаем эффект в слот `Cost Gameplay Effect Class`:
![97518d7a7b02a5436c98f5ee7d944b6a.png](../images/97518d7a7b02a5436c98f5ee7d944b6a.png)
Реализуем в теле класса следующую логику:
![b4e9d30cb3a0608118945073886a23df.png](../images/b4e9d30cb3a0608118945073886a23df.png)
![f259dfa03f289e8c2e606dd5a356eb00.png](../images/f259dfa03f289e8c2e606dd5a356eb00.png)
![9b74a93dc81388d82340887a516122d6.png](../images/9b74a93dc81388d82340887a516122d6.png)
![cdedbdad3bf7e31a8589fd0777140f62.png](../images/cdedbdad3bf7e31a8589fd0777140f62.png)
![9a7da3581065baed79042bff7e234ef7.png](../images/9a7da3581065baed79042bff7e234ef7.png)
В этом коде, раз в `0.1` секунды вызывается нода `Commit Ability`, которая применяет эффект из параметра `Cost` к персонажу до тех пор, пока не кончится выносливость.
![7e4e475d6b2670c600f46e5527324e1c.png](../images/7e4e475d6b2670c600f46e5527324e1c.png)
Благодаря сложной логике проверок - выносливость не расходуется, если игрок прыгнул или уперся в стену и не бежит, или если его скорость равна той, что была до бега.
![f2fa783a64af376e62ec6bfa0a27a762.png](../images/f2fa783a64af376e62ec6bfa0a27a762.png)
### Эффект, переопределяющий начальные значения характеристик
Базовые значения характеристик, которые разработчик задал в конструкторе `AttributeSet` могут быть переопределены за счет автоматически применяемого эффекта.
![beb4a699b4449a9f2bacd1e252139383.png](../images/beb4a699b4449a9f2bacd1e252139383.png)
В частности, нам надо переопределить такие параметры как `Stamina Regen Rate` и `Health Regen Rate`. Остальное я пока трогать не буду.
Добавляем новый `GameplayEffect`.
В `Content Browser` нажмите на кнопку `Add -> Blueprint Class`.
В появившемся окне выбора класса найдите `GameplayEffect`.
![d5fae89c5eebee5cda9c94e3cbdc76c3.png](../images/d5fae89c5eebee5cda9c94e3cbdc76c3.png)
Называем эффект `GE_InitialAttributes`.
Это должен быть `Intant` эффект.
![87b8aa68b5f0f649b27671faacf5d71d.png](../images/87b8aa68b5f0f649b27671faacf5d71d.png)
Я задал начальное значение здоровья на `70`, а максимальное на `100`.
![b1b2a27fdae780fb6763ad8fed962b47.png](../images/b1b2a27fdae780fb6763ad8fed962b47.png)
Скорось восстановления здоровья - `1`, максимальная выносливость - `200`.
![ce8de0168f18724b26f9fcad55753b6b.png](../images/ce8de0168f18724b26f9fcad55753b6b.png)
Выносливость - `200`, скорость восстановления выносливости - `1`.
![cbd48bcd9aca633045e75f00bd779f33.png](../images/cbd48bcd9aca633045e75f00bd779f33.png)
Мана - `300`, скорость восстановления маны - `1`.
![e5cc3a10b04455cf0cbce9b5e6f29000.png](../images/e5cc3a10b04455cf0cbce9b5e6f29000.png)
Все модификаторы эффекта создаются в режиме `Override`.
Первым задается модификатор `Max` значения характеристики, а потом - значение характеристики. Порядок важен.
Добавляем ссылку на созданный эффект в наш `DataAsset` со стартовыми способностями - `DA_PlayerAbilities`.
![e604cc47369c139288b9609dcdc9da2c.png](../images/e604cc47369c139288b9609dcdc9da2c.png)
Запускаем проект и видим, что параметры изменились.
![509fbafcb9e56163e0053ae2fbc35a41.png](../images/509fbafcb9e56163e0053ae2fbc35a41.png)
### Эффект пассивного восстановления здоровья и выносливости
Давайте создадим эффект, пассивно восстанавливающий выносливость и здоровье.
Добавляем новый `GameplayEffect`.
В `Content Browser` нажмите на кнопку `Add -> Blueprint Class`.
В появившемся окне выбора класса найдите `GameplayEffect`.
![d5fae89c5eebee5cda9c94e3cbdc76c3.png](../images/d5fae89c5eebee5cda9c94e3cbdc76c3.png)
Называем эффект `GE_HealthStaminaRegen`.
![3594000b7fe7d022e86ef095355394e6.png](../images/3594000b7fe7d022e86ef095355394e6.png)
Открываем созданный эффект. Задаем `Duration Policy = Infinite` и добавляем два модификатора, для здоровья и для выносливости.
![d2c8d11f233f578fe005ac3874bf2db3.png](../images/d2c8d11f233f578fe005ac3874bf2db3.png)
Выставляем `Modifier Magnitude` - `Attribute Based` и в параметре `Attribute To Capture` выбираем аттрибут - `Health Regen Rate`.
![21287cfbc42c7c7c82aaccb9f363f033.png](../images/21287cfbc42c7c7c82aaccb9f363f033.png)
Точно так-же выставляем и `Stamina Regen Rate`.
Сохраняем и запускаем проект. Видим постепенное восстановление здоровья и выносливости.
![b2533e09fe85548b7e63919dcd51770c.png](../images/b2533e09fe85548b7e63919dcd51770c.png)
Однако, этого не достаточно. Во время бега происходит восстановление выносливости, что сказывается на поведении прогресс бара в интерфейсе.
Остановим восстановление выносливости во время бега.
Действие `GA_Sprint` накладывает тег `Locomotion.Sprint`, зная этот факт мы можем блокировать применение способности.
![d44981c2bb9a7587a1ae40cfcc9d8629.png](../images/d44981c2bb9a7587a1ae40cfcc9d8629.png)
Для этого, в эффекте `GE_HealthStaminaRegen` выставляем параметр `Ongoing Tag Requirements -> Ignore Tags` в значение `Locomotion.Sprint`.
![f6f6c2a21ef68189c04933504b4a460e.png](../images/f6f6c2a21ef68189c04933504b4a460e.png)
В результате, во время бега выносливость восстанавливаться не будет.
Вынесем логику восстановления здоровья в отдельный эффект, чтобы оно не останавливалось, во время бега.
![9c789fd85e2f1079fd54f7785c9fe2a5.png](../images/9c789fd85e2f1079fd54f7785c9fe2a5.png)
![e3e11b2f65584201c7f070b9f86e9587.png](../images/e3e11b2f65584201c7f070b9f86e9587.png)
### Стаки эффектов, стаки брони
Создадим эффект который будет пассивно восстанавливать до 10 стаков брони на персонаже.
#### Добавляем эффект стаков брони
В `Content Browser` нажимаем кнопку `Add -> Blueprint Class`, в появившемся окне, на вкладке `All Classes` ищем `Gameplay Effect`.
![249f28bb2be5d32d277fbd0265f963fd.png](../images/249f28bb2be5d32d277fbd0265f963fd.png)
Создаем новый `blueprint` и называем его `GE_ArmorStacks`.
![5be9aff9375d3a62c80d7f3c3dfe3dc5.png](../images/5be9aff9375d3a62c80d7f3c3dfe3dc5.png)
В коде эффекта добавляем модификатор, который увеличивает количество параметра `Armor` на 3.
Так-же устанавливаем `Duration Policy` на `Infinite`.
![7f4c884683d5acd168466aae7c6cf35d.png](../images/7f4c884683d5acd168466aae7c6cf35d.png)
В параметре `Stack Limit Count` ставим `5` - максимум `5` стаков брони. Так-же устанавливаем `Stacking Type`, этот параметр отвечает за накопление стаков на цели эффекта, выбираем `Aggregate by Target`.
![f16d6f8e34c6f3b719d1d2707dec45c6.png](../images/f16d6f8e34c6f3b719d1d2707dec45c6.png)
Добавляем для эффекта тег, в секции `Granted Tags`, по которому мы будем отслеживать стаки. В моем случае тег - `Buff.Armor`.
![fab0b8f76c3d9e5219710996f77490ea.png](../images/fab0b8f76c3d9e5219710996f77490ea.png)
![630a5e2a28145b7a3c6bd75c9d1e4cba.png](../images/630a5e2a28145b7a3c6bd75c9d1e4cba.png)
#### Пишем способность, добавляющую стаки брони
Добавляем пассивную способность, которая будет восстанавливать стаки брони с течением времени.
Для этого в `Content Browser` нажимаем на кнопку `Add -> Blueprint Class`. В появившемся окне, на вкладке `All Classes` ищем класс - `CoreGameplayAbility`.
![e3f80d1163ab005edef045ec35b0b34a.png](../images/e3f80d1163ab005edef045ec35b0b34a.png)
Создаем новый `blueprint` на его основе. Называем новый класс `GA_ArmorRegen`.
![3fb0b30cb7f596ab77987c8482a89410.png](../images/3fb0b30cb7f596ab77987c8482a89410.png)
В настройках эффекта устанавливаем параметр `Run on Assign` в `True`.
![457cacc1c3e5b08af1a47ef1c8b4c1a2.png](../images/457cacc1c3e5b08af1a47ef1c8b4c1a2.png)
Пишем код, который будет накладывать эффекты брони с течением времени.
![75878a14454227e5eca10e4f8c297ba5.png](../images/75878a14454227e5eca10e4f8c297ba5.png)
![9cff18ad3655d68460f5f18b41ca6f93.png](../images/9cff18ad3655d68460f5f18b41ca6f93.png)
![13bda7695c44ee72b533d0e8a87abdf3.png](../images/13bda7695c44ee72b533d0e8a87abdf3.png)
Добавим созданное действие в список загружаемых "по умолчанию". Для этого откройте `Data Asset` - `DA_PlayerAbilities` и добавьте способность `GA_ArmorRegen` в список.
![63d6fd44242a411af98a5f66ef83488b.png](../images/63d6fd44242a411af98a5f66ef83488b.png)
#### Добавляем индикатор брони в интерфейс
Открываем виджет интерфейса `WB_Hud`.
![789bce00c966ebfbe265c443f3e84157.png](../images/789bce00c966ebfbe265c443f3e84157.png)
Добавляем справа от полосок здоровья и выносливости дополнительную иконку брони. Я нарисовал ее за пару секунд в gimp, изображение иконки приложено к книге в папке "дополнительно".
![c57fd1c7cb52daf9d89136e69aba0fe1.png](../images/c57fd1c7cb52daf9d89136e69aba0fe1.png)
Интерфейс выглядит так:
![5c9796f0d6639af33f17ff6dfaff3c0d.png](../images/5c9796f0d6639af33f17ff6dfaff3c0d.png)
Добавляем логику отслеживания стаков брони в `Graph` виджета, по событию `Construct`.
![cd068778c20a8a8e932d2ff2156e69fa.png](../images/cd068778c20a8a8e932d2ff2156e69fa.png)
![5dd1f3ccb9ffcd82ed5e4e57b5359b18.png](../images/5dd1f3ccb9ffcd82ed5e4e57b5359b18.png)
#### Проверка
При запуске игры вы увидите, как над индикатором брони увеличивается число стаков.
![f89d50cd229560bd33ec647a67f2254f.png](../images/f89d50cd229560bd33ec647a67f2254f.png)
## Передача информации в `Gameplay Effect`
Рассмотрим различные способы передачи информации о уроне в `Gameplay Effect`, а так-же взаимодействия эффектов со способностями.
### Зоны урона и восстановления здоровья `Set By Caller Magnitude`
Реализуем зону урона, в которой у персонажа будет отниматься здоровье и еще одну зону, которая будет ускорять восстановление здоровья.
Передача значения урона, который наносит соответствующая зона, будет реализована с применением ноды `Set By Called Magnitude`. Это самый простой способ передать динамическое значение внутрь эффекта.
Добавляем новый `blueprint` класс типа `Actor`, он и будет олицетворять собой зону, которая применяет на персонажа требуемый эффект.
В `Content Browser` нажимаем на кнопку `Add -> Blueprint Class`. В появившемся окне выбираем `Actor`.
![40ddde2399fa125954ecdb0f8c7346ae.png](../images/40ddde2399fa125954ecdb0f8c7346ae.png)
Называем класс `BP_EffectZoneActor`.
![d61954c2f56cd97907a7ad8272393ac8.png](../images/d61954c2f56cd97907a7ad8272393ac8.png)
Открываем. Для обработки коллизий в класс необходимо добавить компонент - `Box Collision`.
Перетаскиваем его вверх, на `Default Scene Root`, чтобы сделать основным компоенентом класса.
![22783de9077d45d7b71d0dfb891f734c.png](../images/22783de9077d45d7b71d0dfb891f734c.png)
![9147903e64f550378a6e14bd340db812.png](../images/9147903e64f550378a6e14bd340db812.png)
Для отображения места установки зоны, добавим `Static Mesh Component` и загрузим туда предзаданный меш `Plane`.
![00aa54e4f4780073a0605c11158e9918.png](../images/00aa54e4f4780073a0605c11158e9918.png)
Настроим коллизии. В компоненте `Box`, переключаемся на вкладку `Collision`.
Устанавливаем пресет - `OverlapAllDynamic`.
![eb67bc17e0453bba5186f35232124007.png](../images/eb67bc17e0453bba5186f35232124007.png)
В компоненте `Plane` отключаем все коллизии, потому что он используется только для визуализации зоны.
![7651e32df55d353faab67d2807829911.png](../images/7651e32df55d353faab67d2807829911.png)
В код класса добавляем переменную `Gameplay Effect Class`.
![b86d6cd343871fdf808a12da33f2cb39.png](../images/b86d6cd343871fdf808a12da33f2cb39.png)
Делаем данную переменную `Expose On Spawn` и `Instance Editable`.
![5dafeee8f2158094f691f373d4a864d0.png](../images/5dafeee8f2158094f691f373d4a864d0.png)
Добавляем ряд других переменных.
![de9df36215ceba194bb9936b4c0e1a13.png](../images/de9df36215ceba194bb9936b4c0e1a13.png)
`EffectValue` по умолчанию имеет значение в `-10`.
`Period` по умолчанию имеет значение в `0.1`.
Пишем логику применения выбранного эффекта по событию `Begin Play`.
![3562c3af6790303f6b4b87a6750b6839.png](../images/3562c3af6790303f6b4b87a6750b6839.png)
![c70653cb465224bbf6f8f78038af8174.png](../images/c70653cb465224bbf6f8f78038af8174.png)
![5569c9a1751308a83442825bb0c62e21.png](../images/5569c9a1751308a83442825bb0c62e21.png)
Размещаем созданный класс где-нибудь на уровне.
![808ce1fc8dff032b635aea17fb9811ba.png](../images/808ce1fc8dff032b635aea17fb9811ba.png)
#### Добавляем  эффект нанесения урона здоровью
В `Content Browser` нажимаем кнопку `Add -> Blueprint Class`, в появившемся окне, на вкладке `All Classes` ищем `Gameplay Effect`.
![249f28bb2be5d32d277fbd0265f963fd.png](../images/249f28bb2be5d32d277fbd0265f963fd.png)
Создаем новый `blueprint` и называем его `GE_ZoneEffect`.
![6fff4b92be49d23573b5173b87afd5e6.png](../images/6fff4b92be49d23573b5173b87afd5e6.png)
Значение урона, которое будет накладываться в эффектом, можно передавать через параметр `Caller Magnitude`. Вот как это делается:
![112c0054c4489ff7da91b940a3122353.png](../images/112c0054c4489ff7da91b940a3122353.png)
Параметр `Caller Magnitude` создается с использованием `blueprint` ноды `Assign Tag Set by Caller Magnitude`. Для маркировки значения урона я использовал тег `Damage.Value`.
Открываем созданный нами эффект `GE_DamageZoneHealth`.
Добавляем один модификатор и устанавливаем ему параметр `Modifier Magnitude -> Set By Caller`. В выпадающем списке `Set by Caller Magnitude` выбираем наш тег - `Damage.Value`.
![7bbc6f08e5352224acddfd6809962c57.png](../images/7bbc6f08e5352224acddfd6809962c57.png)
Таким способом разработчик может передавать цифровые значения внутрь `Gameplay Effect`.
Установим выбранный эффект, в качестве параметра `Gameplay Effect Class` нашей зоны с уроном.
![8b5fa17b9c83eb26d95a7acc05a8cfba.png](../images/8b5fa17b9c83eb26d95a7acc05a8cfba.png)
Запустим проект и войдем в зону персонажем. Здоровье начнет убывать и остановится у нуля.
![18c7e262651c78f9d3f6ddc8a592e86f.png](../images/18c7e262651c78f9d3f6ddc8a592e86f.png)
Благодаря передаче значения прямо в эффект через `Caller Magnitude`, мы можем создать вторую зону такого-же типа, но с другим значением `Effect Value`. Для нанесения урона здоровью мы ставили значение `-10`. Если мы поставим положительное значение `5` в параметр `Effect Value`, то такая зона будет выступать как зона лечения.
![4f61e3e87bf1b7266714d88e5aa94042.png](../images/4f61e3e87bf1b7266714d88e5aa94042.png)
Проверим.
Урон:
![4538cb4215963e2df1a4679e6093edb2.png](../images/4538cb4215963e2df1a4679e6093edb2.png)
Лечение:
![dcfba876b76bf3fd2bdcc9b11fec7edb.png](../images/dcfba876b76bf3fd2bdcc9b11fec7edb.png)
### Пишем логику смерти персонажа
При падении здоровья до `0` персонаж должен прекращать всякую активность и падать в `ragdoll`.
#### Исправляем `Physics Asset`
Для начала нам нужно исправить стандартный `Phycisc Asset`, чтобы `Ragdoll` в нем работал корректно.
В шаблоне `ThirdPerson`, который мы используем для этого проекта, есть `Physics Asset` маникена. Называется он `PA_Mannequin`.
![d5a9484ebe421c311cb2f3c046eb21cd.png](../images/d5a9484ebe421c311cb2f3c046eb21cd.png)
![d629885cf21fb2d79054b794f4d72bea.png](../images/d629885cf21fb2d79054b794f4d72bea.png)
В этом ассете, к кости `root` необходимо добавить физическое тело с `Kinematic` режимом симуляции. Я уже писал на форуме `Epic Games` длинный пост [как такое делается](https://forums.unrealengine.com/t/attempting-to-move-fully-simulated-skeletal-mesh-error/402585/15?u=alexanderst). Напишу еще раз.
В редакторе `Physics Asset` нажимаем на неприметную кнопку с иконкой колесика, смотрите на картинку. После нажатия перед вами появится меню, в котором надо будет выбрать пункт - `Show All Bones`.
![09f8f98576c478094bed7feca430c136.png](../images/09f8f98576c478094bed7feca430c136.png)
Теперь выделяем кость `Root` и щелкаем по ней правой кнопкой мыши.
![a096036008f15e9dbfbda166ee3ef04c.png](../images/a096036008f15e9dbfbda166ee3ef04c.png)
В появившемся меню переходим по пунктам `Add Shape -> Add Sphere`.
В результате редактор сгенерирует нам новое физическое тело у ног персонажа - сферу.
![3d1b12e5abf0195dee393a1f2671bb28.png](../images/3d1b12e5abf0195dee393a1f2671bb28.png)
Щелкните по ней мышью. Поскольку я не хочу, чтобы данная сфера симулировала физику и хоть как-то влияла на `ragdoll` - необходимо установить в параметрах сферы `Physics Type` - `Kinematic`.
![3e3b5cbb25267ddc77f5c870a5682b27.png](../images/3e3b5cbb25267ddc77f5c870a5682b27.png)
Наличие этого тела  позволит сохранять трансформ персонажа при входе в рагдолл. Свяжем `root` тело с остальным физическим скелетом.
Для этого снизу, в редакторе, есть окно - `Graph`, а точнее `Joint Graph`. В этом графе можно отслеживать связи нашего физического тела с другими телами. Поскольку `root` тело только что создано - связей у него нет.
![5ecad98ef88df3fb6d19ee94b241717b.png](../images/5ecad98ef88df3fb6d19ee94b241717b.png)
Щелкаем по узлу `Body root` правой кнопкой мыши и переходим в меню `Constraints`. Выберите тут кость `pelvis`.
![40be91881519c50ae5a2eed4bac1db60.png](../images/40be91881519c50ae5a2eed4bac1db60.png)
Теперь физическое тело `root` связано с физическим телом `pelvis`.
![35e44059a36b27744dc54982728f0135.png](../images/35e44059a36b27744dc54982728f0135.png)
Нажмите на соединяющую оба тела прослойку - `Constraint pelvis:root`.
Тут необходимо установить допустимые лимиты отступа тел друг от друга.
![9ccb201358e6006a77a8a1a27fd9ab46.png](../images/9ccb201358e6006a77a8a1a27fd9ab46.png)
Выставляете в параметрах `Linear Limits` и  `Angular Limits` все в `Free`. Не надо экспериментировать с `Limited`, вы только сломаете `Ragdoll`. Тут обязательно должно быть `Free`.
Давайте повысим производительность нашего рига в движке. Переключите параметр отображения костей с `Show All Bones` на `Hide Bones`.
![7991a55e4b1d408a8b14b9a575208095.png](../images/7991a55e4b1d408a8b14b9a575208095.png)
Выделите в редакторе все физические тела рига, кроме `hand_l, hand_r, head, foot_l, foot_r`
![c75153f175881c48624c6ea6400b97fc.png](../images/c75153f175881c48624c6ea6400b97fc.png)
Снимите у выделенных тел параметр `Consider for Bounds`. Установите данным физическим телам параметр `Skip Scale from Animation`.
Это повысит производительность `Radoll`, без каких-либо видимых потерь качества.
#### Добавляем эффект, обнуляющий статы персонажа - `GE_Death`
Поскольку при смерти персонажа все его характеристики должны обнуляться, включая выносливость и здоровье - создадим эффект, который все это сделает.
В `Content Browser` нажимаем кнопку `Add -> Blueprint Class`, в появившемся окне, на вкладке `All Classes` ищем `Gameplay Effect`.
![249f28bb2be5d32d277fbd0265f963fd.png](../images/249f28bb2be5d32d277fbd0265f963fd.png)
Создаем новый `blueprint` и называем его `GE_Death`.
![96bff4309cf4fa7c3d924b86ac41b206.png](../images/96bff4309cf4fa7c3d924b86ac41b206.png)
Создаем два модификатора `Modifier` для обнуления здоровья и выносливости. Значение, которое будут применять эти модификаторы должно быть основано на соответствующем аттрибуте. Выбираем параметр `Magnitude Calculation Type` - `Attribute Based`. Выставляем параметр `Coefficient` в `-1`. Таким образом значения аттрибутов здоровье и выносливость, при наложении эффекта, будут вычитаться сами из себя.
![36dc177d9d91a6b5de792b927ddcc2e3.png](../images/36dc177d9d91a6b5de792b927ddcc2e3.png)
#### Удаляем эффекты и способности при смерти персонажа
Чтобы активные эффекты и способности удалялись при смерти персонажа, надо создать особый тег, я предлагаю назвать его `Effect.RemoveOnDeath`. Этот тег необходимо присвоить каждому эффекту и каждой способности, которые должны удаляться при смерти персонажа.
Начнем с эффекта восстановления здоровья, который мы написали ранее - `GE_HealthRegen`.
![36300d25aa72fb02b0dda2c8a141b912.png](../images/36300d25aa72fb02b0dda2c8a141b912.png)
Откроем ассет и добавим в список `GrantedTags` тег `Effect.RemoveOnDeath`.
![a9b6f4d8c3f99c8d164c27eea47b2767.png](../images/a9b6f4d8c3f99c8d164c27eea47b2767.png)
Аналогичные действия надо провести с эффектом брони `GE_ArmorStacks`.
![8e17e6a6f1f539191775049ca6346164.png](../images/8e17e6a6f1f539191775049ca6346164.png)
Теперь откройте эффект смерти персонажа - `GE_Death`.
![a44c58c5d140d5069adfc6950e6c94ca.png](../images/a44c58c5d140d5069adfc6950e6c94ca.png)
При наложении этого эффекта, все эффекты, помеченные тегом `Effect.RemoveOnDeath` должны удаляться. Чтобы этого добиться, переходим вниз, к параметру `Remove Gameplay Effects With Tags`, выставляем `Effect.RemoveOnDeath`. Все эффекты, с этим тегом, будут удалены при наложении `GE_Death`.
Так-же задаем `GrantedBlockedAbilityTags` и `GrantedTags` в `Buff.Death`. Это позволит блокировать работу способностей.
![3200428ff3e9bcd8be179abd10b31703.png](../images/3200428ff3e9bcd8be179abd10b31703.png)
Поскольку эффект `GE_Death` имеет продолжительность - `Infinite` мы можем блокировать способности, используя `Buff.Death`.
Способностям, работу которых необходимо блокировать выставляем `Activation Blocked Tags` - `Buff.Death`. Например в способности `GA_Sprint` выставите следующее:
![a7b79aa9a9216de20898831add81802d.png](../images/a7b79aa9a9216de20898831add81802d.png)
#### Добавляем функционал включения / выключения `Ragdoll`
В класс персонажа надо добавить функцию, которая позволит включать рагдолл.
Окрываем `blueprint` персонажа, у нас это `BP_CoreCharacter`.
![e69d2b496dfdc606c4b77417658ab063.png](../images/e69d2b496dfdc606c4b77417658ab063.png)
Добавляем следующий метод:
![524707b5fb241e2196286456233782f3.png](../images/524707b5fb241e2196286456233782f3.png)
При его активации персонаж будет падать в рагдолл.
Протестируем.
Добавьте в персонажа событие нажатия клавиши `3` следующим образом:
![f7db64fa10c0d978f5f6e05af41a2dd4.png](../images/f7db64fa10c0d978f5f6e05af41a2dd4.png)
Рагдолл работает:
![7e5a6370a86407e8697bded43e3377b4.png](../images/7e5a6370a86407e8697bded43e3377b4.png)
Напишем функцию выхода из рагдолла.
Для начала, по событию `BeginPlay`, нам надо сохранять базовый трансформ меша, а так-же режим работы коллизий на меше и капсуле.
![21055fe07c6e3a5642fba903a5a0a007.png](../images/21055fe07c6e3a5642fba903a5a0a007.png)
![acc41ef1248e0c74460717c69c1a4b07.png](../images/acc41ef1248e0c74460717c69c1a4b07.png)
Далее пишем следующую функцию:
![b358d17cbaee72515520201934b3ead0.png](../images/b358d17cbaee72515520201934b3ead0.png)
Переменные:
![1f3f53f88678bbe8a410c16dc64c0059.png](../images/1f3f53f88678bbe8a410c16dc64c0059.png)
Для тестирования, на событие обработки клавиши `3` добавляем следующий код:
![a58c686176887e5c3f1ca19ae735e452.png](../images/a58c686176887e5c3f1ca19ae735e452.png)
Теперь мы можем свободно выходить и входить в `Ragdoll`, по желанию.
![dc081ff621755e34bdf96f84b2c3928a.png](../images/dc081ff621755e34bdf96f84b2c3928a.png)
![aeab2e53acb43e0af1747daabe938a08.png](../images/aeab2e53acb43e0af1747daabe938a08.png)
#### Добавляем способность `GA_Death`
Создаем пассивную способность для отслеживания состояния здоровья. Именно тут мы будем активировать `Ragdoll`, если здоровье равно `0`.
Для этого в `Content Browser` нажимаем на кнопку `Add -> Blueprint Class`. В появившемся окне, на вкладке `All Classes` ищем класс - `CoreGameplayAbility`.
![e3f80d1163ab005edef045ec35b0b34a.png](../images/e3f80d1163ab005edef045ec35b0b34a.png)
Создаем новый `blueprint` на его основе. Называем новый класс `GA_Death`.
![cdbdfddf8d041863345685c2a3a1b95b.png](../images/cdbdfddf8d041863345685c2a3a1b95b.png)
Устанавливаем для способности параметр `Run on Assign` в `True`.
В событии `Event ActivateAbility` добавляем ноду отслеживающую характеристику `Health` - `Wait for Attribute Change Treshold -> Exactly Equal To -> 0`.
![28e1cb5dead1a3fbbbc761b2cc3ac75d.png](../images/28e1cb5dead1a3fbbbc761b2cc3ac75d.png)
Когда параметр `Health` достигает значения `0`, эта нода активируется и вызывается метод `Apply Ragdoll` на аватаре эффекта. Помимо рагдолла, к персонажу применяется и эффект `GE_Death`, который обнуляет ему  выносливость и здоровье уже окончательно.
Добавляем созданную способность в списко применяемых "по умолчанию". Для этого открываем `Data Asset` - `DA_PlayerAbilities` и добавляем способность `GA_Death` в список.
![cdf5a5c7fa7b94d83c42cd70acc46d8d.png](../images/cdf5a5c7fa7b94d83c42cd70acc46d8d.png)
Если теперь войти персонажем в зону, наносящую урон - персонаж упадет в `ragdoll`.
![c7ff13c3bf22ecfb7de5f9c6d3a5de92.png](../images/c7ff13c3bf22ecfb7de5f9c6d3a5de92.png)
Что нам и требовалось.
Однако, у нашей реализации есть одна явная проблема.
### Вычисляем модификатор эффекта по собственной формуле, `Mod Magnitude Calculation`
Значение модификатора, накладываемого эффектом на характеристику, может быть вычислено по требуемой формуле, с использованием собственной формулы.
У нашего персонажа есть показатель брони, мы можем вычитать значение брони персонажа из урона, который по нему наносится.
#### Введение
Класс вычисления значения модификатора может быть установлен через поле `Magnitude Calculation Type` - `Custom Calculation Class`.
![966e7b55db14d8196f126bfa42cab667.png](../images/966e7b55db14d8196f126bfa42cab667.png)
В появившемся списке параметров будет поле `Calculation Class`. Объект, который можно установить в это поле, должен быть унаследован от класса `UGameplayModMagnitudeCalculation`.
Как и в случае с другими классами такого типа, у него есть определенный паттерн реализации, который можно понять, если посмотреть на исходные файлы движка.
Сам класс `UGameplayModMagnitudeCalculation` можно найти в файле `\Engine\Plugins\Runtime\GameplayAbilities\Source\GameplayAbilities\Public\GameplayModMagnitudeCalculation.h`.
![e35486f1a1384b21d632593d1798f63a.png](../images/e35486f1a1384b21d632593d1798f63a.png)
Самый главный метод тут - `CalculateBaseMagnitude`. Он должен возвращать `float` значение, которое будет применено к эффекту.
Поскольку метод `CalculateBaseMagnitude` создан с модификатром `BlueprintNativeEvent`, чтобы его переопределить надо использовать постфикс `_Implementation`.
Класс `UGameplayModMagnitudeCalculation`  можно использовать из `blueprint`. Однако его возможности в `blueprint` сильно ограничены и ничего серьезного посчитать вы не сможете.
![8a95c11a32ccf57a15ee2378e173c525.png](../images/8a95c11a32ccf57a15ee2378e173c525.png)
Тем не менее, подобная возможность открывает для разработчика способ вынести часть `c++` логики в `blueprint`, для более легкого редактирования. Однако основа класса должна быть написана на `c++`.
#### Создаем заголовочный файл для класса
Открываем расположение проекта по пути `Source / GASBase / Public / Attributes` и создаем там подпапку `Modifiers`.
![cd72c3eaeb6c978d00d03b2be8915396.png](../images/cd72c3eaeb6c978d00d03b2be8915396.png)
В папку `Modifiers` добавляем пустой текстовый файл `DamageModMagnitudeCalculation.h`.
![d3b1dbbff6124d3bd954a0545e42792f.png](../images/d3b1dbbff6124d3bd954a0545e42792f.png)
Чтобы файл отобразился в `Visual Studio` - выполните из `Unreal Engine` команду `Tools -> Refresh Visual Studio 2022 Project`.
![5d61a278d3196aa5dd379c3c7b95fb25.png](../images/5d61a278d3196aa5dd379c3c7b95fb25.png)
Открываем файл для редактирования и пишем код.
#### Базовый шаблон класса
Все классы, вычисляющие значение `Magnitude Modifier` должны следовать следующему паттерну:
```cpp
#pragma once
#include "CoreMinimal.h"
#include "GameplayModMagnitudeCalculation.h"
#include "GameplayEffectExecutionCalculation.h"
#include "AbilitySystemComponent.h"
// project files
#include "Attributes/CharacterAttributeSet.h"
// .generated.h
#include "DamageModMagnitudeCalculation.generated.h"
// singleton struct to get captured fields data
struct FDamageMagnitudeCapturedStatics
{
	DECLARE_ATTRIBUTE_CAPTUREDEF(Health);
	FDamageMagnitudeCapturedStatics()
	{
		DEFINE_ATTRIBUTE_CAPTUREDEF(UCharacterAttributeSet, Health, Target, true);
	}
	// singleton
	static FDamageMagnitudeCapturedStatics& instance()
	{
		static FDamageMagnitudeCapturedStatics INSTANCE;
		return INSTANCE;
	}
};
/**
 * Damage to Health Magnitude Calculation
 */
UCLASS()
class UDamageModMagnitudeCalculation : public UGameplayModMagnitudeCalculation
{
	GENERATED_BODY()
public:
	// constructor
	UDamageModMagnitudeCalculation() {
		RelevantAttributesToCapture.Add(FDamageMagnitudeCapturedStatics::instance().HealthDef);
    }
	// calculate magnitude here
	virtual float CalculateBaseMagnitude_Implementation(const FGameplayEffectSpec& Spec) const override {
		float Result = 0.f;
		return Result;
	}
};
```
#### `DamageModMagnitudeCalculation.h`
```cpp
#pragma once
#include "CoreMinimal.h"
#include "GameplayModMagnitudeCalculation.h"
#include "GameplayEffectExecutionCalculation.h"
#include "AbilitySystemComponent.h"
// project files
#include "Attributes/CharacterAttributeSet.h"
// .generated.h
#include "DamageModMagnitudeCalculation.generated.h"
// singleton struct to get captured fields data
struct FDamageMagnitudeCapturedStatics
{
	DECLARE_ATTRIBUTE_CAPTUREDEF(Health);
	DECLARE_ATTRIBUTE_CAPTUREDEF(Armor);
	FDamageMagnitudeCapturedStatics()
	{
		DEFINE_ATTRIBUTE_CAPTUREDEF(UCharacterAttributeSet, Health, Target, true);
		DEFINE_ATTRIBUTE_CAPTUREDEF(UCharacterAttributeSet, Armor, Target, true);
	}
	// singleton
	static FDamageMagnitudeCapturedStatics& instance()
	{
		static FDamageMagnitudeCapturedStatics INSTANCE;
		return INSTANCE;
	}
};
/**
 * Damage to Health Magnitude Calculation
 */
UCLASS()
class UDamageModMagnitudeCalculation : public UGameplayModMagnitudeCalculation
{
	GENERATED_BODY()
public:
	// constructor
	UDamageModMagnitudeCalculation() {
		RelevantAttributesToCapture.Add(FDamageMagnitudeCapturedStatics::instance().HealthDef);
		RelevantAttributesToCapture.Add(FDamageMagnitudeCapturedStatics::instance().ArmorDef);
	}
	// calculate magnitude here
	virtual float CalculateBaseMagnitude_Implementation(const FGameplayEffectSpec& Spec) const override {
		float Result = 0.f; // result
		const FGameplayTagContainer* SourceTags = Spec.CapturedSourceTags.GetAggregatedTags();
		const FGameplayTagContainer* TargetTags = Spec.CapturedTargetTags.GetAggregatedTags();
		// Get DamageValue from SetByCallerMagnitude tag
		float DamageValue = Spec.GetSetByCallerMagnitude(FGameplayTag::RequestGameplayTag(FName("Damage.Value")), true, -1.0f);
		// Getting Values from captured args:
		// set evalupation parameters
		FAggregatorEvaluateParameters EvaluationParameters;
		EvaluationParameters.SourceTags = SourceTags;
		EvaluationParameters.TargetTags = TargetTags;
		// Captured Armor
		float Armor = 0.0f;
		GetCapturedAttributeMagnitude(FDamageMagnitudeCapturedStatics::instance().ArmorDef, Spec, EvaluationParameters, Armor);
		// Capured Health
		float Health = 0.0f;
		GetCapturedAttributeMagnitude(FDamageMagnitudeCapturedStatics::instance().HealthDef, Spec, EvaluationParameters, Health);
		if (Health > 0) {
			Result = FMath::Clamp(DamageValue - Armor, 0, 1000);
		}
		UE_LOG(LogTemp, Error, TEXT("float val = %f"), Result)
		return Result;
	}
};
```
Обратите внимание, что значение урона `DamageValue` внутри эффекта получается с использованием `GetSetByCallerMagnitude`. То есть передается в эффект динамически.
#### Проверка
Для проверки написанного нами класса используем зоны урона. Копируем эффект зоны урона - `GE_ZoneEffect`.
![b57fe24b7c2cc6494bef3b3dbbdb39e1.png](../images/b57fe24b7c2cc6494bef3b3dbbdb39e1.png)
Называем копию - `GE_ZoneModifier`.
![b904b1315762da8ca1ee70d592d91af5.png](../images/b904b1315762da8ca1ee70d592d91af5.png)
Открываем, устанавливаем `Magnitude Calculation Type` - `Custom Calculation Class` и `Calculation Class` - `DamageModMagnitudeCalculation`.
Поскольку предполагается, что цифра урона будет положительной - выставляем `Coefficient` в `-1`.
![9548b597620266194f529512c788f667.png](../images/9548b597620266194f529512c788f667.png)
Устанавливаем наш эффект в зону для урона. Цифра урона должна быть положительной.
![91fd10ff8fecba5badd108c3aa54cf7e.png](../images/91fd10ff8fecba5badd108c3aa54cf7e.png)
Если войти в такую зону, урон, который она наносит, будет снижен, в зависимости от количества стаков брони персонажа.
![3b43b2fd84a13290d0124b6d3e5cc467.png](../images/3b43b2fd84a13290d0124b6d3e5cc467.png)
Однако, применение подобной логики при нанесении урона не позволяет разработчику уменьшать количество стаков брони на персонаже. А это важно для нашей работы.
### `Effect Execution Calculation`, обработчик эффекта
После списка модификаторов, в эффекте есть параметр `Executions`. Тут можно указать собственный класс-обработчик эффекта.
![a38853985503a36de2e3c96760262574.png](../images/a38853985503a36de2e3c96760262574.png)
Данный класс должен быть унаследован от `UGameplayEffectExecutionCalculation` и установлен в поле `Calculation Class`.
Используя обработчик, можно вызывать сложную логику, при срабатывании эффекта, можно влиять на другие эффекты или управлять тегами. Мы будем уменьшать стаки брони, используя обработчик.
#### Создаем заголовочный файл для класса
Создадим заголовочный файл для обработчика эффекта.
Открываем расположение проекта по пути `Source / GASBase / Public / Attributes` и создаем там подпапку `Executions`.
![299b79c1b8cc20d9031bb9af5a789a7f.png](../images/299b79c1b8cc20d9031bb9af5a789a7f.png)
В папку `Executions` добавляем пустой текстовый файл `DamageExecCalculation.h`.
![97c11564378ae7a9fcedd2742b2ae502.png](../images/97c11564378ae7a9fcedd2742b2ae502.png)
Чтобы файл отобразился в `Visual Studio` - выполните из `Unreal Engine` команду `Tools -> Refresh Visual Studio 2022 Project`.
![92497d43b05cf262af148f1edef66b69.png](../images/92497d43b05cf262af148f1edef66b69.png)
#### Базовый шаблон класса
```cpp
#pragma once
#include "CoreMinimal.h"
#include "GameplayModMagnitudeCalculation.h"
#include "GameplayEffectExecutionCalculation.h"
#include "AbilitySystemComponent.h"
#include "AbilitySystemBlueprintLibrary.h"
// project files
#include "Attributes/CharacterAttributeSet.h"
// .generated.h
#include "DamageExecCalculation.generated.h"
// singleton struct to get captured fields data
struct FDamageExecutionCapturedStatics
{
	DECLARE_ATTRIBUTE_CAPTUREDEF(Health);
	DECLARE_ATTRIBUTE_CAPTUREDEF(Armor);
	FDamageExecutionCapturedStatics()
	{
		DEFINE_ATTRIBUTE_CAPTUREDEF(UCharacterAttributeSet, Health, Target, true);
	}
	// singleton
	static FDamageExecutionCapturedStatics& instance()
	{
		static FDamageExecutionCapturedStatics INSTANCE;
		return INSTANCE;
	}
};
/**
 * Damage to Health Magnitude Calculation
 */
UCLASS()
class UDamageExecCalculation : public UGameplayEffectExecutionCalculation
{
	GENERATED_BODY()
public:
	// constructor
	UDamageExecCalculation() {
		RelevantAttributesToCapture.Add(FDamageExecutionCapturedStatics::instance().HealthDef);
    }
	virtual void Execute_Implementation(const FGameplayEffectCustomExecutionParameters& ExecutionParams, OUT FGameplayEffectCustomExecutionOutput& OutExecutionOutput) const override
    {
    /* your code here */
	}
};
```
#### `DamageModMagnitudeCalculation.h`
```cpp
#pragma once
#include "CoreMinimal.h"
#include "GameplayModMagnitudeCalculation.h"
#include "GameplayEffectExecutionCalculation.h"
#include "AbilitySystemComponent.h"
#include "AbilitySystemBlueprintLibrary.h"
// project files
#include "Attributes/CharacterAttributeSet.h"
// .generated.h
#include "DamageExecCalculation.generated.h"
// singleton struct to get captured fields data
struct FDamageExecutionCapturedStatics
{
	DECLARE_ATTRIBUTE_CAPTUREDEF(Health);
	DECLARE_ATTRIBUTE_CAPTUREDEF(Armor);
	FDamageExecutionCapturedStatics()
	{
		DEFINE_ATTRIBUTE_CAPTUREDEF(UCharacterAttributeSet, Health, Target, true);
		DEFINE_ATTRIBUTE_CAPTUREDEF(UCharacterAttributeSet, Armor, Target, true);
	}
	// singleton
	static FDamageExecutionCapturedStatics& instance()
	{
		static FDamageExecutionCapturedStatics INSTANCE;
		return INSTANCE;
	}
};
/**
 * Damage to Health Magnitude Calculation
 */
UCLASS()
class UDamageExecCalculation : public UGameplayEffectExecutionCalculation
{
	GENERATED_BODY()
public:
	// constructor
	UDamageExecCalculation() {
		RelevantAttributesToCapture.Add(FDamageExecutionCapturedStatics::instance().HealthDef);
		RelevantAttributesToCapture.Add(FDamageExecutionCapturedStatics::instance().ArmorDef);
	}
	virtual void Execute_Implementation(const FGameplayEffectCustomExecutionParameters& ExecutionParams, OUT FGameplayEffectCustomExecutionOutput& OutExecutionOutput) const override {
		UAbilitySystemComponent* TargetAbilitySystemComponent = ExecutionParams.GetTargetAbilitySystemComponent();
		UAbilitySystemComponent* SourceAbilitySystemComponent = ExecutionParams.GetSourceAbilitySystemComponent();
		AActor* SourceActor = SourceAbilitySystemComponent ? SourceAbilitySystemComponent->GetAvatarActor() : nullptr;
		AActor* TargetActor = TargetAbilitySystemComponent ? TargetAbilitySystemComponent->GetAvatarActor() : nullptr;
		const FGameplayEffectSpec& Spec = ExecutionParams.GetOwningSpec();
		// Gather the tags from the source and target as that can affect which buffs should be used
		const FGameplayTagContainer* SourceTags = Spec.CapturedSourceTags.GetAggregatedTags();
		const FGameplayTagContainer* TargetTags = Spec.CapturedTargetTags.GetAggregatedTags();
		FAggregatorEvaluateParameters EvaluationParameters;
		EvaluationParameters.SourceTags = SourceTags;
		EvaluationParameters.TargetTags = TargetTags;
		// Captured Armor
		float Armor = 0.0f;
		ExecutionParams.AttemptCalculateCapturedAttributeMagnitude(FDamageExecutionCapturedStatics::instance().ArmorDef, EvaluationParameters, Armor);
		Armor = FMath::Max<float>(Armor, 0.0f);
		// Capured Health
		float Health = 0.0f;
		ExecutionParams.AttemptCalculateCapturedAttributeMagnitude(FDamageExecutionCapturedStatics::instance().HealthDef, EvaluationParameters, Health);
		Health = FMath::Max<float>(Health, 0.0f);
		// Get DamageValue from SetByCallerMagnitude tag
		float DamageValue = Spec.GetSetByCallerMagnitude(FGameplayTag::RequestGameplayTag(FName("Damage.Value")), true, -1.0f);
		float BlockedDamage = 0.f; // damage blocked by armor
		if (DamageValue > Armor) {
			DamageValue = DamageValue - Armor;
			BlockedDamage = Armor;
		} else {
			DamageValue = 0;
			BlockedDamage = Armor - DamageValue;
		}
		UE_LOG(LogTemp, Error, TEXT("DamageValue = %f"), DamageValue)
		UE_LOG(LogTemp, Error, TEXT("BlockedDamage = %f"), BlockedDamage)
		if (TargetAbilitySystemComponent)
		{
			// iterate over all effects
			for (FActiveGameplayEffectsContainer::ConstIterator ActiveEffectIt = TargetAbilitySystemComponent->GetActiveGameplayEffects().CreateConstIterator(); ActiveEffectIt; ++ActiveEffectIt)
			{
				FGameplayTagContainer EffectTagContainer;
				ActiveEffectIt->Spec.GetAllGrantedTags(EffectTagContainer);
				int EffectStackCount = ActiveEffectIt->Spec.StackCount; // effect stacks
				int SingleStackMitigation = ActiveEffectIt->Spec.GetModifierMagnitude(0, false);
				bool bStackRemoved = false;
				// iterate thru all tags of effect
				for (auto EffectTagIt = EffectTagContainer.CreateConstIterator(); EffectTagIt; ++EffectTagIt)
				{
					// search for effect with tag "Buff.Armor"
					bool bIsArmorEffect = *EffectTagIt == FGameplayTag::RequestGameplayTag(FName("Buff.Armor"));
					if ( bIsArmorEffect )
					{
						UE_LOG(LogTemp, Error, TEXT("SingleStackMitigation = %i"), SingleStackMitigation) // just in case
						// remove one stack of armor from effect
						TargetAbilitySystemComponent->RemoveActiveGameplayEffect((*ActiveEffectIt).Handle, 1);
						bStackRemoved = true; // exit loop
						break;
					}
				}
				if(bStackRemoved) break; // exit loop
			}
		}
		// this way we can modify properties
		if (DamageValue > 0) {
			// Apply modifier to decrease Health attribute based on amount of DamageValue
			OutExecutionOutput.AddOutputModifier(
				FGameplayModifierEvaluatedData(FDamageExecutionCapturedStatics().HealthProperty, EGameplayModOp::Additive, (-1) * DamageValue)
			);
		}
	}
};
```
Как вы видите, из класса `UDamageExecCalculation` мы можем управлять модификаторами эффекта, через `OutExecutionOutput.AddOutputModifier`, а так-же удалять или добавлять стеки эффектов через указатель на `Ability System Component`.
#### Проверка
Для проверки написанного нами класса используем зоны урона. Копируем эффект зоны урона - `GE_ZoneEffect`.
![b57fe24b7c2cc6494bef3b3dbbdb39e1.png](../images/b57fe24b7c2cc6494bef3b3dbbdb39e1.png)
Называем копию - `GE_ZoneExecCalculation`.
![3467a52ed36f418062f8e77328ed2a9c.png](../images/3467a52ed36f418062f8e77328ed2a9c.png)
Открываем. Удаляем все модификаторы.
Добавляем один элемент в список `Executions`. Устанавливаем `Calculation Class` на `UDamageExecCalculation`.
![627d21a744a99805ceaea3580999d000.png](../images/627d21a744a99805ceaea3580999d000.png)
Устанавливаем наш эффект в зону для урона. Цифра урона должна быть положительной.
![91fd10ff8fecba5badd108c3aa54cf7e.png](../images/91fd10ff8fecba5badd108c3aa54cf7e.png)
Если войти в такую зону, урон, который она наносит, будет снижен, в зависимости от количества стаков брони персонажа. При нанесении урона, стаки брони персонажа будут спадать. Когда броня полностью пропадет - персонаж будет получать полный урон.
![16939e8afd589b8ea2ce1a311af1654e.png](../images/16939e8afd589b8ea2ce1a311af1654e.png)
![6ab742a3533fd8a2ec57b77e60d0e627.png](../images/6ab742a3533fd8a2ec57b77e60d0e627.png)
Напомню, что `1` стак брони блокирует `3` единицы урона.
![eab6f47c1d310aa9a2a278ec20128c4a.png](../images/eab6f47c1d310aa9a2a278ec20128c4a.png)
`5` стаков блокируют `15` урона, `4` - `12`, `3` - `9`.
Зона наносит `10` единиц урона. Если значение брони выше, чем наносимый урон - то урон снижается до `0`, а стак брони - снимается.
## Сложные эффекты
Рассмотрим работу с `Gameplay Effects` на сложных примерах. Для начала реализуем логику стрельбы, а потом - способность метеор, с выбором зоны применения.
### Примитивная логика стрельбы и нанесения урона
Реализуем логику стрельбы и нанесения урона, с проигрыванием процедурного эффекта попадания.
#### Добавляем `Impact Effect` на персонажа
Чтобы персонаж, при попаданиях пуль, реалистично реагировал, мы можем использовать сесьма занятный трюк с физикой, включая ее в момент попадания, а потом постепенно уменьшая влияние физики на тело персонажа.
Открывает `blueprint` персонажа, в нашем случае это `BP_CoreCharacter`.
![73b5c0a78ff2419175479be800562c25.png](../images/73b5c0a78ff2419175479be800562c25.png)
Добавляем новый `Timeline`.
![31d5b957d0a62c099e64454b713f856d.png](../images/31d5b957d0a62c099e64454b713f856d.png)
![208f46b980cab6c566d9decd939d0513.png](../images/208f46b980cab6c566d9decd939d0513.png)
Двойным щелчком открываем `Timeline`.
Добавляем `Track` нажав на соответствующую кнопку.
![41d920e454ba5100ec1757b8b9d99ce3.png](../images/41d920e454ba5100ec1757b8b9d99ce3.png)
Добавляем две точки на `Track`, через клик правой кнопкой мыши.
![05a097652b0188f37637c2ae7fe1be0f.png](../images/05a097652b0188f37637c2ae7fe1be0f.png)
Первая точка должна иметь значения `Time - 0.0, Value = 0.5`, вторая точка - значения `Time - 0.4, Value - 0.0`.
![d13d0328cc475d15631425fa7df14731.png](../images/d13d0328cc475d15631425fa7df14731.png)
Таким образом мы сможем плавно переходить от значения `0.5` к `0.0` за `0.4` секунды.
Оборачиваем логику `Timeline` в `Custom Event` - `ApplyImpactWeight`.
![213f7dcab7ab74bdd01eac1441e04e63.png](../images/213f7dcab7ab74bdd01eac1441e04e63.png)
Добавляем метод `Recive Impact` со следующей логикой:
![92913af0c13ae0876d1bb7a53342029c.png](../images/92913af0c13ae0876d1bb7a53342029c.png)
![928c15eccb1f7c1b42ae6553ad9aa404.png](../images/928c15eccb1f7c1b42ae6553ad9aa404.png)
![dab3b8469e77ef318fd6b059c29e851d.png](../images/dab3b8469e77ef318fd6b059c29e851d.png)
Теперь мы сможем вызывать метод `Recive Impact` и видеть, как персонажи реагируют на попадания игрока.
![45509202ff4c551f292b00d4971ae4c7.png](../images/45509202ff4c551f292b00d4971ae4c7.png)
![ae0ea79f94deb3ee6e456bbd680c909a.png](../images/ae0ea79f94deb3ee6e456bbd680c909a.png)
#### Добавляем прожектайл
В `Content Browser` создаем папку `FireBullet`. Внутрь добавляем `blueprint` класс `Actor` используя `ContentBrowser -> Add -> Blueprint Clas -> Actor`. Называем созданный класс - `BP_Bullet`.
![b88cc7463cbc27b0a33d29889ef8fcb5.png](../images/b88cc7463cbc27b0a33d29889ef8fcb5.png)
Открываем созданный класс и добавляем в него следующие компоненты: `Projectile Movement`, `Sphere Collision`, и `Static Mesh Component`.
Передаскиваем компонент `SphereCollision` на `DefaultSceneRoot`.
![428f1746316039073133b85a2528b480.png](../images/428f1746316039073133b85a2528b480.png)
Чтобы было так:
![0448559695327fb11143a6f41f58ee7c.png](../images/0448559695327fb11143a6f41f58ee7c.png)
По желанию, можете добавить систему партиклов, которая будет проигрываться при попадании патрона по цели, в моем случае это `P_Explosion` из папки `Starter Content`.
![8786a52f23dd8170efc7123a22be7a98.png](../images/8786a52f23dd8170efc7123a22be7a98.png)
В системе партиклов отключите автоматическую активацию.
![c2c823792304249290a4acb234ee20dc.png](../images/c2c823792304249290a4acb234ee20dc.png)
Добавляем в `Static Mesh Component` - `Sphere` меш сферы из `Starter Content`.
![733acca1c0f613a445de48d6da299b5e.png](../images/733acca1c0f613a445de48d6da299b5e.png)
Скалируем компонент так, чтобы внешне он стал похож на пулю.
![bc31f6f05da577e118610e07bfda6790.png](../images/bc31f6f05da577e118610e07bfda6790.png)
Можете написать для пули отдельный шейдер, который будет  светиться. Для этого создайте материал, через `Content Browser` меню `Add -> Materials -> Material`.
![1331e70e5e8d7fde629bc0e8be887f05.png](../images/1331e70e5e8d7fde629bc0e8be887f05.png)
Установите для материала `Sdading Mode -> Unlit` и  добавьте в него ноду `Vector3`, для вывода цвета. Подключите ноду к параметру `Emissive Color`.
![4b9dfc64a1b9daa02ff8105839174758.png](../images/4b9dfc64a1b9daa02ff8105839174758.png)
Чтобы объект с материалом начал светиться, в параметре цвета измените поле `V`, например, на `35`.
![07fd0d96e6a833a6dec82eeb98768db6.png](../images/07fd0d96e6a833a6dec82eeb98768db6.png)
Создайте `Material Instance` на основе нашего материала. Для этого щелкните по материалу правой кнопкой мыши -> `Create Material Instance`.
![fe18c4185de4aa5b0aa6f4c0daa408f9.png](../images/fe18c4185de4aa5b0aa6f4c0daa408f9.png)
Я назвал `Material Instance` - `MI_Bullet`. Поменяйте материал у пули.
![f0e9d7d55b08db1f70baf9d3338a7720.png](../images/f0e9d7d55b08db1f70baf9d3338a7720.png)
Идем далее.
![c2fdcc57981cebcbb15d0b7f5a42b02f.png](../images/c2fdcc57981cebcbb15d0b7f5a42b02f.png)
В компоненте `Projectile Movement` выставляем параметр `Initial Speed` на `3000` и параметр `Projectile Gravity Scale` на `0`.
![4c8a88ad19c0470a831a277179422b74.png](../images/4c8a88ad19c0470a831a277179422b74.png)
Эти настройки заставят прожектайл лететь вперед, игнорируя гравитацию.
Установите параметр `Rotation Foolows Velocity`, чтобы вращение прожектайла соовпадало с направлением полета.
Выключаем все коллизии на `Staic Mesh Component` - `Sphere`.
![4d6648ba568be7259512def0094326fc.png](../images/4d6648ba568be7259512def0094326fc.png)
![979c6aecd3f8fa251281b71bb63505dc.png](../images/979c6aecd3f8fa251281b71bb63505dc.png)
На `Sphere Collision Component` ставим все коллизии в `Overlap`, кроме `Visibility` и `Camera`, а параметр `Collision Enabled` в `Query and Physics`.
![8d76cf2745844984d91eec1eb9412221.png](../images/8d76cf2745844984d91eec1eb9412221.png)
![3b839e2cf67ba97e681ea7bf06873eb5.png](../images/3b839e2cf67ba97e681ea7bf06873eb5.png)
Добавляем в прожектайл переменную `Max Range`, это максимальная дистанция, после которой прожектайл самоуничтожится. Данный параметр должен иметь два тега - `Instance Editable` и `Expose On Spawn`, значение в параметре - `1000`.
![352f1d553387c2d8be0b543c15b8a860.png](../images/352f1d553387c2d8be0b543c15b8a860.png)
Добавляем в прожектайл переменную `EffectSpecHandle` типа `Gameplay Effect Spec Handle`. Через эту переменную мы будем передавать в пулю эффект, который она наложит на цель, при попадании. Параметры `Instance Editable` и `Expose On Spawn` - должны быть включены.
![9e610ec7574580415e526dd09971d3c4.png](../images/9e610ec7574580415e526dd09971d3c4.png)
![5b0d6f730193a5ac64ede3ee80944a77.png](../images/5b0d6f730193a5ac64ede3ee80944a77.png)
Чтобы прожектайл уничтожался после прохождения этой дистанции создаем в событии `BeginPlay` вызов ноды `Set Life Span` следующим образом:
![6c805c9618ef4cdc04933222ce8a12f7.png](../images/6c805c9618ef4cdc04933222ce8a12f7.png)
Срок существования прожектайла в секундах считается по следующей логике:
если `V = S / T` тогда `T = S / V`.
Подключаем событие `OnComponentBeginOverlap` у `Sphere Collision`. При коллизии прожектайл должен останавливаться, для этого используем метод `Stop Movement Immediately` от компонента `Projectile Movement`.
![30c617334c5530236982dfe8e2a2e729.png](../images/30c617334c5530236982dfe8e2a2e729.png)
События `Overlap` не вызываются для тех объектов на уровне, у которых отключен параметр `Generate Overlap Events`. Выделяем все меши на уровне и включаем его.
![c4aa8a3be064c52b1b7d7a576e7bacbb.png](../images/c4aa8a3be064c52b1b7d7a576e7bacbb.png)
Полный `blueprint` код метода `OnComponentBeginOverlap` достаточно длинный, я приведу его по частям.
![ce5b5969ced98b1291c0fc242c208674.png](../images/ce5b5969ced98b1291c0fc242c208674.png)
![6175ca2e4f629da20161f53bcecc5eec.png](../images/6175ca2e4f629da20161f53bcecc5eec.png)
![54dcecf22c7090781228446b8dcd269d.png](../images/54dcecf22c7090781228446b8dcd269d.png)
![26f871b4661963b04261afad0ce642e5.png](../images/26f871b4661963b04261afad0ce642e5.png)
#### Добавляем способность `GA_FireBullet`
Прожектайл должен призываться из способности выстрела. Реализуем ее.
Для этого в `Content Browser` нажимаем на кнопку `Add -> Blueprint Class`. В появившемся окне, на вкладке `All Classes` ищем класс - `CoreGameplayAbility`.
![e3f80d1163ab005edef045ec35b0b34a.png](../images/e3f80d1163ab005edef045ec35b0b34a.png)
Создаем новый `blueprint` на его основе. Называем новый класс `GA_FireBullet`.
![6023606f9c7c856f047561ef46071f30.png](../images/6023606f9c7c856f047561ef46071f30.png)
Открываем файл и выставляем `AbilitySlot` на `FireSlot`, чтобы наша способность активировалась при нажатии соотествтующей клавиши.
![100f347d92cc655f2beb0fab06d8bd7e.png](../images/100f347d92cc655f2beb0fab06d8bd7e.png)
Добавляем в граф способности логику призыва новых прожектайлов.
![076ecb1dc9509c09a4ce88328ba0a592.png](../images/076ecb1dc9509c09a4ce88328ba0a592.png)
![79cf72c101a01f5341665cbc1a310e29.png](../images/79cf72c101a01f5341665cbc1a310e29.png)
![648f8e8bece505d7098e984611c5447c.png](../images/648f8e8bece505d7098e984611c5447c.png)
![b4ab21761078623942a5adc53378777f.png](../images/b4ab21761078623942a5adc53378777f.png)
Так-же, в класс способности была добавлена функция, которая высчитывает начальную позицию и вращение прожектайла в соответствии с точкой прицела - `GetBulletInitialTransform`.
![0c8e9531b6158c851c9fbb3728041e23.png](../images/0c8e9531b6158c851c9fbb3728041e23.png)
![a0999eb056f06c7ec2655f093481e590.png](../images/a0999eb056f06c7ec2655f093481e590.png)
![f12b9d84613a745b66bd713dc7cd04b0.png](../images/f12b9d84613a745b66bd713dc7cd04b0.png)
![12063b755fd5e0b2255e3bd69d49aa29.png](../images/12063b755fd5e0b2255e3bd69d49aa29.png)
Устанавливаем параметр `Fire Rate` в `0.3`.
![00d4c563a5f7fe11a675223f886e44e9.png](../images/00d4c563a5f7fe11a675223f886e44e9.png)
`Bullet Damage` - `10`.
![0acacaedcb926f924856ae51eebcdab9.png](../images/0acacaedcb926f924856ae51eebcdab9.png)
`Bullet Max Range` - `3000`.
![6af4dcc13dd9d7704c8ab4048e0d0020.png](../images/6af4dcc13dd9d7704c8ab4048e0d0020.png)
Добавляем действие `GA_FireBullet` в `DataAsset` со списком способностей персонажа - `DA_PlayerAbilities`.
![6e5ff33bfd677d68df3f46833ac9457f.png](../images/6e5ff33bfd677d68df3f46833ac9457f.png)
Если запустить проект и нажать на левую кнопку мыши, мы увидим как раз в `0.3` секунды, в цель летят прожектайлы.
![50b18b8c7c3862525d5109016a974681.png](../images/50b18b8c7c3862525d5109016a974681.png)
#### Создаем бота с примитивным ии
Создадим бота с примитивным ИИ, который будет выступать в качестве цели для прожектайлов.
В `Content Browser` нажимаем кнопку `Add -> Blueprint Class`, в списке `All Classes` ищем `BP_CoreCharacter`.
![1b181b937cb449188a4ef876b28e3654.png](../images/1b181b937cb449188a4ef876b28e3654.png)
Называем новый ассет `BP_NPC_Character`.
![83cce0246ffdd672b882af30ecc7d5c0.png](../images/83cce0246ffdd672b882af30ecc7d5c0.png)
Открываем. Добавляем следующую логику:
![ca9c5c94f5fe1721ced281a2145ac687.png](../images/ca9c5c94f5fe1721ced281a2145ac687.png)
Добавляем на уровень `Nav Mesh Bounds Volume`.
![a609b586b91a5332987f018ab58e4997.png](../images/a609b586b91a5332987f018ab58e4997.png)
Скалируем `Nav Mesh Bounds Volume` по осям `X` и `Y` так чтобы он покрывал всю карту.
![80c49ff59882916f522f7361ee70b0c3.png](../images/80c49ff59882916f522f7361ee70b0c3.png)
Добавляем `BP_NPC_Character` на уровень.
![8e1e3330a276d4cfb6a56c8af92fb3cb.png](../images/8e1e3330a276d4cfb6a56c8af92fb3cb.png)
При запуске проекта, написанный нами `NPC` будет двигаться в случайную точку уровня.
![e8d732f0cbe79514b305edc6907d14a1.png](../images/e8d732f0cbe79514b305edc6907d14a1.png)
Передайте боту те-же самые способности, которые есть у персонажа. Для этого установите в параметр `Default Abilities Info` файл `DA_PlayerAbilities`.
![59c9926f5cae2f0b589e7c75eb4d3722.png](../images/59c9926f5cae2f0b589e7c75eb4d3722.png)
Давайте заставим бота бегать по уровню. Cпособность `Sprint` можно активировать следующим образом:
![634811c46b665297796cc9faee8429ee.png](../images/634811c46b665297796cc9faee8429ee.png)
Тогда функция `Random Point Movement` будет выглядеть так:
![fbb10127907818a273b237ecef876af8.png](../images/fbb10127907818a273b237ecef876af8.png)
![5d96b7811fb6b4f9b8910b685045159c.png](../images/5d96b7811fb6b4f9b8910b685045159c.png)
![c060eecad3c0953bfeaa9d3e7ce35394.png](../images/c060eecad3c0953bfeaa9d3e7ce35394.png)
Запускаем проект и смотрим как бот бегает, расходуя выносливость. Правда этого еще не видно, потому что мы не реализовали виджет с соответствующей информацией.
![e4247653f88ad6d76432221934629169.png](../images/e4247653f88ad6d76432221934629169.png)
#### Создаем виджет полоски здоровья для `NPC`
Хотя полоски здоровья лучше всего выводить через класс `HUD`, как я делал это в соответствующей статье, тут, в этой статье, мы выведем полоску здоровья через обычный `Widget Blueprint`. Это очень плохо для производительности, но очень хороше для прототипирования.
В `Content Browser` выполняем команду `Add -> User Interface -> Widget Blueprint`.
![8e7218bd1e836f9fd773b33ba5d29df5.png](../images/8e7218bd1e836f9fd773b33ba5d29df5.png)
Называем новый ассет `WB_FloatingStats`.
![fa64abe58fe1de60fcf9277cb613ff20.png](../images/fa64abe58fe1de60fcf9277cb613ff20.png)
Внутрь виджета добавляем `SizeBox`, рамзера `300` на `70` и выставляем размерность на `Desired`.
![7870fa53faca4dbb81ae8a318d874a7e.png](../images/7870fa53faca4dbb81ae8a318d874a7e.png)
Добавляем `Vertical Box` и два `Progerss Bar'а`, а так-же иконку брони.
![e8be30cba1a9aac4c1d43e137a069c0a.png](../images/e8be30cba1a9aac4c1d43e137a069c0a.png)
Для `Progress Bar` выставляем  параметр `Percent` в `1`.
![e34591e455963165fb75909fbe9380b9.png](../images/e34591e455963165fb75909fbe9380b9.png)
![8aeff445033b2fab7a009da589d663d2.png](../images/8aeff445033b2fab7a009da589d663d2.png)
Переключаемся в `Graph`.
![8dbe862a22c47f066176ca910042335a.png](../images/8dbe862a22c47f066176ca910042335a.png)
Создаем или копируем переменную `ASC` из виджета `HUD`. Тип переменной - `Ability System Component`.
![e905e6a637187b5f4de21cf2244620f3.png](../images/e905e6a637187b5f4de21cf2244620f3.png)
Переносим остальную логику из виджета `WB_HUD` с небольшими правками.
![9b2eb9dafc7e51a37be647175d2ac150.png](../images/9b2eb9dafc7e51a37be647175d2ac150.png)
![bc2b34017aed2e72bf0bf5817e9cdd9b.png](../images/bc2b34017aed2e72bf0bf5817e9cdd9b.png)
![3e5e8a49382d82e2d8fd01d9da351b59.png](../images/3e5e8a49382d82e2d8fd01d9da351b59.png)
![e6bc83534656418b4cbfb98f04060384.png](../images/e6bc83534656418b4cbfb98f04060384.png)
Возвращаемся к классу нашего `NPC` - `BP_NPC_Character`.
Добавляем в него компонент `Widget`, устанавливаем параметр `Widget Class` в `WB_FloatingStats`.
![d255451d6b7fea912c47867dad15f750.png](../images/d255451d6b7fea912c47867dad15f750.png)
Выставляем рендеринг в `Screen Space` и размер `150` на `30`.
![175f778709c44093729a32abdce3ba86.png](../images/175f778709c44093729a32abdce3ba86.png)
Виджеты, которые рендерятся в `Screen Space` всегда будут повернуты по направлению к камере игрока.
Чтобы характеристики загружались в виджет - его надо инициализировать через переменную `ASC`.
Добавляем следующий код инициализации виджета в методе `Begin Play` класса `BP_NPC_Character`.
![09c6d05ab4ffb016fb990e696deef075.png](../images/09c6d05ab4ffb016fb990e696deef075.png)
Запускаем проект и видим бегающего бота, у которого расходуется выносливость и накапливается броня.
![800cc44e2a45494d85dedaae5fe0e00b.png](../images/800cc44e2a45494d85dedaae5fe0e00b.png)
#### Применение урона
Просчет урона в нашем проекте осуществляется за счет `Execution Calculation Modifier`. Мы применяли этот модификатор на зоны для урона, но ничто не  мешает нам накладывать данный эффект на цели, в которые попадают пули. К тому-же сам класс рассчета урона настроен на динамическое считывание значений через `Set by Caller Magnitude`.
В `Content Browser` нажимаем на кнопку `Add -> Blueprint Class`, в пункте `All Classes` ищем `Gameplay Effect`.
![cbbf1c21097587ae4e7556ebd29e59e2.png](../images/cbbf1c21097587ae4e7556ebd29e59e2.png)
Называем эффект `GE_BulletHealthModifierDamage`.
![af86371cb4c7f60e8b191f31bad5489b.png](../images/af86371cb4c7f60e8b191f31bad5489b.png)
Устанавливаем `Duration Policy` - `Instant` и `Executions -> Calculation Class` - `DamageExecCalculation`.
![3dda76db92529b0fdccf9b121ca64ffa.png](../images/3dda76db92529b0fdccf9b121ca64ffa.png)
Созданный эффект надо применять к цели, при попадании прожектайла.
Открываем `blueprint` нашего прожектайла - `BP_Bullet`. Добавляем туда переменную `EffectSpecHandle` с типом `GameplayEffectSpecHandle`. Переменная должна иметь параметры `Instance Editable` и `Expose on Spawn`.
![0b918f998ff95f0e0ab84ad542d531a7.png](../images/0b918f998ff95f0e0ab84ad542d531a7.png)
Теперь идем в `blueprint`, призывающий прожектайл - `GA_FireBullet`. Отсюда надо передать `EffectSpecHandle` с установленным значением урона в тег `Damage.Value`.
![5540045cd6f88368f1df530ed17f5d56.png](../images/5540045cd6f88368f1df530ed17f5d56.png)
Обратите внимание на ноду`MakeOutgoingGameplayEffectSpec`, параметр `Gameplay Effect Class` в ней имеет значение `GE_BulletApplied`. Именно этот эффект мы будем применять на цель, в которую попадет прожектайл.
Возвращаемся в класс прожектайла - `BP_Bullet`.
Тут необходимо написать логику применения переданного эффекта.
![157e8189b009048fd79647420e23a82f.png](../images/157e8189b009048fd79647420e23a82f.png)
![194d88911828028a8de07391bb2fbb21.png](../images/194d88911828028a8de07391bb2fbb21.png)
Запускаем проект, нажимаем левую кнопку мыши и видим, что урон наносится, стаки брони - снимаются, `NPC` падают в `Ragdoll`.
![f112a0f370290b5c68bbd7019665ce86.png](../images/f112a0f370290b5c68bbd7019665ce86.png)
Если вы хотите, чтобы "мертвые" `NPC` пропадали с уровня, можете добавить следующий код в класс `BP_NPC_Character`:
![7d1f89672591350e9cd552e455fa8f25.png](../images/7d1f89672591350e9cd552e455fa8f25.png)
### Способность `Meteor`
Реализуем способность с выбором области действия. Такие способности, например, есть в `World Of Warcraft`, в частности пресловутый `Rain of Fire` чернокнижника.
![81d66be93d97bb8530cbedb932dfc781.png](../images/81d66be93d97bb8530cbedb932dfc781.png)
Такие способности можно создавать и в `Unreal Engine`, при помощи системы `GAS`. Чем мы и займемся в этой главе.
#### Добавляем слот под нашу способность
Дополняем список слотов, указанный в `EAbilityInput` новым слотом, под способность.
`AbilityInputEnum.h`
```cpp
#pragma once
#include "CoreMinimal.h"
// somehow my VS dosen't detect .generated.h for nested files so i have to inclde this manyally to get rid of red errors
#include "UObject/ObjectMacros.h"
// UHT
#include "AbilityInputEnum.generated.h"
// Ability Action
UENUM(BlueprintType)
enum class EAbilityInput : uint8
{
	None
	, Confirm
	, Cancel
	, Slot1 UMETA(DisplayName = "Fire Slot")
	, Slot2 UMETA(DisplayName = "Aim Slot")
	, Slot3 UMETA(DisplayName = "Sprint Slot")
	, Slot4 UMETA(DisplayName = "Jump Slot")
	, Slot5 UMETA(DisplayName = "Meteor Slot")
};
```
Добавляем `InputAction` для использования способности `Meteor`.
В `Content Browser` нажимаем `Add -> Input -> Input Action`.
![791a8b42a133d820b9ae6b2db7d85404.png](../images/791a8b42a133d820b9ae6b2db7d85404.png)
Называем новое действие - `IA_Meteor`.
Тип действия оставляем стандартным - `bool`.
![f24ebca979c691347cbc0fc67aacdb89.png](../images/f24ebca979c691347cbc0fc67aacdb89.png)
Добавляем созданное действие в глобальный `Input Mapping Context`, в нашем проекте это `IMC_Default`.
![85f5f47f9feffd2e8ddadfec5dca847c.png](../images/85f5f47f9feffd2e8ddadfec5dca847c.png)
Назначаем клавишу `F` для применения способности.
Теперь идем в `Visual Studio` и дополняем метод `BindContext` в классе `ACoreCharacter` новым обработчиком.
```cpp
// Meteor
if (AName.Contains(TEXT("Meteor"))) {
    EnhancedInputComponent->BindAction(Action, ETriggerEvent::Triggered, this, &ACoreCharacter::SendAbilityLocalInput, (int)EAbilityInput::Slot5);
    EnhancedInputComponent->BindAction(Action, ETriggerEvent::Completed, this, &ACoreCharacter::SendAbilityLocalInput, (int)EAbilityInput::Slot5);
}
```
На всякий случай привожу полный код метода `BindContext` в данный момент.
`CoreCharacter.cpp`
```cpp
/**===============================================
 * Input
 */
// will bind new mapping context
void ACoreCharacter::BindContext(TSoftObjectPtr<UInputMappingContext> Context, APlayerController* OnController)
{
	if (Context == nullptr) {
		Context = MappingContext;
	}
	if (Context == nullptr) return; // only because i don't want to crash app with check()
	APlayerController* PlayerController = OnController == nullptr ? Cast<APlayerController>(GetController()) : OnController;
	if (PlayerController == nullptr) {
		return;
	}
	const ULocalPlayer* LocalPlayer = PlayerController->GetLocalPlayer();
	// get enhanced input component's subsystem
	UEnhancedInputLocalPlayerSubsystem* EiSubsystem = ULocalPlayer::GetSubsystem<UEnhancedInputLocalPlayerSubsystem>(LocalPlayer);
	ensure(LocalPlayer && EiSubsystem);
	// input component
	UEnhancedInputComponent* EnhancedInputComponent = Cast<UEnhancedInputComponent>(PlayerController->InputComponent);
	//EnhancedInputComponent->ClearActionBindings(); // clear all bindings if necessary
	EnhancedInputComponent->ClearBindingsForObject(this); // clear bindings only from this object
	if (EiSubsystem != nullptr) {
		// register mapping context
		EiSubsystem->AddMappingContext(Context.Get(), InputPriority);
	}
	// iterate over all mappings and put every unique action into the Map
	// since actions occur in the map multiple times, for every key binding
	TMap<FName, const UInputAction*> Actions;
	for (FEnhancedActionKeyMapping Mapping : Context->GetMappings()) {
		FName ActionName = Mapping.Action->GetFName();
		if (!Actions.Contains(ActionName)) {
			Actions.Add(ActionName, Mapping.Action.Get());
		}
		//UE_LOG(LogTemp, Warning, TEXT("Action Name = %s"), *Mapping.Action->GetName())
	}
	// now differentiate actions in the loop and assign them according to the names
	// make sure to give action names accordingly
	for (const TPair<FName, const UInputAction*>& ActionInfo : Actions)
	{
		FString AName = ActionInfo.Key.ToString();
		const UInputAction* Action = ActionInfo.Value;
		// Confirm
		if (AName.Contains(TEXT("Confirm")) && GameAbilitySystemComponent != nullptr) {
			// bind Confirm action
			EnhancedInputComponent->BindAction(Action, ETriggerEvent::Started, GameAbilitySystemComponent.Get(), &UAbilitySystemComponent::LocalInputConfirm);
			GameAbilitySystemComponent->GenericCancelInputID = (int)EAbilityInput::Confirm;
		}
		// Cancel
		if (AName.Contains(TEXT("Cancel")) && GameAbilitySystemComponent != nullptr) {
			// bind Confirm action
			EnhancedInputComponent->BindAction(Action, ETriggerEvent::Started, GameAbilitySystemComponent.Get(), &UAbilitySystemComponent::LocalInputCancel);
			GameAbilitySystemComponent->GenericCancelInputID = (int) EAbilityInput::Cancel;
		}
		// Jumping
		if (AName.Contains(TEXT("Jump"))) {
			EnhancedInputComponent->BindAction(Action, ETriggerEvent::Triggered, this, &ACoreCharacter::SendAbilityLocalInput, (int)EAbilityInput::Slot4);
			EnhancedInputComponent->BindAction(Action, ETriggerEvent::Completed, this, &ACoreCharacter::SendAbilityLocalInput, (int)EAbilityInput::Slot4);
			// use integrated jump
			//EnhancedInputComponent->BindAction(Action, ETriggerEvent::Triggered, this, &ACharacter::Jump);
			//EnhancedInputComponent->BindAction(Action, ETriggerEvent::Completed, this, &ACharacter::StopJumping);
		}
		// Fire
		if (AName.Contains(TEXT("Fire"))) {
			EnhancedInputComponent->BindAction(Action, ETriggerEvent::Triggered, this, &ACoreCharacter::SendAbilityLocalInput, (int)EAbilityInput::Slot1);
			EnhancedInputComponent->BindAction(Action, ETriggerEvent::Completed, this, &ACoreCharacter::SendAbilityLocalInput, (int)EAbilityInput::Slot1);
		}
		// Meteor
		if (AName.Contains(TEXT("Meteor"))) {
			EnhancedInputComponent->BindAction(Action, ETriggerEvent::Triggered, this, &ACoreCharacter::SendAbilityLocalInput, (int)EAbilityInput::Slot5);
			EnhancedInputComponent->BindAction(Action, ETriggerEvent::Completed, this, &ACoreCharacter::SendAbilityLocalInput, (int)EAbilityInput::Slot5);
		}
		// Aim
		if (AName.Contains(TEXT("Aim"))) {
			EnhancedInputComponent->BindAction(Action, ETriggerEvent::Triggered, this, &ACoreCharacter::SendAbilityLocalInput, (int)EAbilityInput::Slot2);
			EnhancedInputComponent->BindAction(Action, ETriggerEvent::Completed, this, &ACoreCharacter::SendAbilityLocalInput, (int)EAbilityInput::Slot2);
		}
		// Moving
		if (AName.Contains(TEXT("Move"))) {
			EnhancedInputComponent->BindAction(Action, ETriggerEvent::Triggered, this, &ACoreCharacter::Move);
		}
		// Sprinting
		if (AName.Contains(TEXT("Sprint"))) {
			EnhancedInputComponent->BindAction(Action, ETriggerEvent::Triggered, this, &ACoreCharacter::SendAbilityLocalInput, (int)EAbilityInput::Slot3);
			EnhancedInputComponent->BindAction(Action, ETriggerEvent::Completed, this, &ACoreCharacter::SendAbilityLocalInput, (int)EAbilityInput::Slot3);
		}
		// Looking around
		if (AName.Contains(TEXT("Look"))) {
			EnhancedInputComponent->BindAction(Action, ETriggerEvent::Triggered, this, &ACoreCharacter::Rotate);
		}
	}
}
```
#### Добавляем `Gameplay Ability Target Actor`
Система `GAS` предоставляет ряд классов, которые могут быть использованы для указания области или цели, в которой будет применена способность.
Увидеть доступные `Target Actor` классы можно из меню добавления нового `blueprint`.
![5f84fc4c26a2faae27bde5662f2a9192.png](../images/5f84fc4c26a2faae27bde5662f2a9192.png)
Для способности метеор необходимо использовать класс `GameplayAbilityTargetActor_GroundTrace`. При призыве, экземпляр этого класса будет следовать за поверхностью террейна, позволяя выбрать область на ней.
За призыв классов `Target Actor` отвечает нода `Wait Target Data`. Она создает `Target Actor` и переключает на него контроллер персонажа. Когда система получае событие `Cancel` или `Commit` - `Target Actor` автоматически уничтожается, а управление возвращается к персонажу.
![d25afa11955875d87d68a877ff54f982.png](../images/d25afa11955875d87d68a877ff54f982.png)
Создаем `Target Actor` от класса `GameplayAbilityTargetActor_GroundTrace`. Называем его `BP_MeteorTargetGroundTrace`.
![3240c8c391f945a9954c0e079a622b05.png](../images/3240c8c391f945a9954c0e079a622b05.png)
Добавляем компонент декали.
![95cb9a96458e67776f215bcc7dc76381.png](../images/95cb9a96458e67776f215bcc7dc76381.png)
Эта декаль будет проецироваться на поверхность, по которой двигается `Target Actor`.
Создадим материал для декали.
В `Content Browser` нажимаем кнопку `Add -> Materials -> Material`.
![dc8560e80bb09752b9259598d941dedd.png](../images/dc8560e80bb09752b9259598d941dedd.png)
Называем материал `M_GroundCircularDecal`.
![dae4dd4fd80f6f4bdc099bf65ce635fe.png](../images/dae4dd4fd80f6f4bdc099bf65ce635fe.png)
Я сделал простой круг для декали. Его исходник приложен к книге, в папке "Дополнительно".
![8a482965315dfa4d06f6e67e0832cb52.png](../images/8a482965315dfa4d06f6e67e0832cb52.png)
Добавляем текстуру круга в материал.
![937e99e9bd2c53a4e06552af9d7e3ccc.png](../images/937e99e9bd2c53a4e06552af9d7e3ccc.png)
В параметрах материала выбираем `Material Domain` - `Deferred Decal` и `Blend Mode` - `Translucent`.
![559e98f95e4217033d07f2bade179ea6.png](../images/559e98f95e4217033d07f2bade179ea6.png)
Щелкаем по материалу правой кнопкой мыши и создаем `Material Instance`.
![e2542fc618753810dec8b20faac13e3e.png](../images/e2542fc618753810dec8b20faac13e3e.png)
![85fd45db306742f6ab7a36360bf4f0f3.png](../images/85fd45db306742f6ab7a36360bf4f0f3.png)
Выбирае мозданный материал в компоненте декали.
![4df2f849351b0bd84ebda1f6918521a5.png](../images/4df2f849351b0bd84ebda1f6918521a5.png)
Устанавливаем трансформы компонента декали следующим образом:
![e7355dc5838d7f8044ffc62e1f7830b8.png](../images/e7355dc5838d7f8044ffc62e1f7830b8.png)
Больше в классе `Ability Target` добавлять ничего не надо.
#### Добавляем эффект, наносящий урон целям
За урон, который будет наноситься целям метеора, должен отвечать отдельный `Gameplay Effect`. Создадим его.
В `Content Browser` нажимаем кнопку `Add -> Blueprint Class`, в появившемся окне, на вкладке `All Classes` ищем `Gameplay Effect`.
![249f28bb2be5d32d277fbd0265f963fd.png](../images/249f28bb2be5d32d277fbd0265f963fd.png)
Создаем новый `blueprint` и называем его `GE_MeteorDamage`.
![696f6e8498a7aabbe11afbe4a7f03e40.png](../images/696f6e8498a7aabbe11afbe4a7f03e40.png)
В этот эффект надо добавить всего одну вещ - `Execution` класс `DamageExecCalculation`, который мы используем для всех эффектов, наносящих урон.
![8c0a49bbeb4db7f3cc75cc2ed48384b0.png](../images/8c0a49bbeb4db7f3cc75cc2ed48384b0.png)
#### Создаем метеор
В качестве метеора будет выступать обычный `Actor` со сферой внутри.
Создаем его через `Content Browser -> Add -> Blueprint Class -> Actor`.
![a7e620c319a1c574919933f85b430f4f.png](../images/a7e620c319a1c574919933f85b430f4f.png)
Называем `BP_MeteorActor`.
![31d85fa52f38cab439c022f75d3c3425.png](../images/31d85fa52f38cab439c022f75d3c3425.png)
Падение метеора будет работать за счет компонента `Projectile Movement`. Логика метеора сходна с логикой пули, которую мы уже реализовывали до этого.
Добавляем в класс компоненты `Static Mesh`, `Sphere Collision` и `Projectile Movement`, опционально можно добавить в класс визуальный эффект `P_Explosion`.
![79c7fadb9be8684f0bde8df7d01317d3.png](../images/79c7fadb9be8684f0bde8df7d01317d3.png)
Найти эффект можно в папке `Starter Content`.
![ac67ec8299f711a5666dcdf2421aa43a.png](../images/ac67ec8299f711a5666dcdf2421aa43a.png)
Компонент, отвечающий за эффект, должен иметь параметр `Auto Activate` = `False`.
![bf718cbff7700ee57aadd5e09084b259.png](../images/bf718cbff7700ee57aadd5e09084b259.png)
Мы будем активировать этот компонент из кода.
В компоненте `Sphere Collision` установите следующие параметры коллизии:
![33965ba4eca73544bdc1cea845d14908.png](../images/33965ba4eca73544bdc1cea845d14908.png)
В компоненте `Static Mesh` отключаем все коллизии.
![e65cb9687a20a1a3734566624e2069f5.png](../images/e65cb9687a20a1a3734566624e2069f5.png)
Загружаем в компонент простую сферу из `Starter Content`.
![3807087423dd5b8610eace237619ab47.png](../images/3807087423dd5b8610eace237619ab47.png)
Настроим компонент `Projectile Movement`. Нам надо, чтобы метеор падал вниз. Ищем праметр `Vecolity`, он отвечает за вектор скоростей по осям.
![2a9426750f485fb2a3acfaae0c801b90.png](../images/2a9426750f485fb2a3acfaae0c801b90.png)
Устанавливаем ось `Z` на `-1`, чтобы прожектайл двигался вниз.
Начальная скорость метеора задается в параметре `Initial Speed`. Тут установлено значение в `500`, потому что я не хочу, чтобы метеор падал слишком быстро.
![9b5c061b36f84cc16bb0058e56f4431f.png](../images/9b5c061b36f84cc16bb0058e56f4431f.png)
Параметр `Gravity Scale` оставляете стандартным.
Пишем логику работы класса.
Добавляем переменную `EffectSpecHandle`, которая будет получать  информацию об накладываемом эффекте и его уроне.
![c6ccf6e819c7ffa82ff22b08d1b9ca32.png](../images/c6ccf6e819c7ffa82ff22b08d1b9ca32.png)
Данная переменная должна иметь флаги `Expose On Spawn` и `Instance Editable`.
![734bdd107bf9ee980e01a59060239237.png](../images/734bdd107bf9ee980e01a59060239237.png)
Добавляем переменную `Damage Radius`, которая будет отвечать за радиус, в котором персонажи, окружающие метеор, получат урон.
![14d0c994ab7700a0b42684346f15ba62.png](../images/14d0c994ab7700a0b42684346f15ba62.png)
Данная переменная должна иметь флаги `Expose On Spawn` и `Instance Editable`.
Добавляем остальные переменные.
![47f2778a1c661d00b06174910305d612.png](../images/47f2778a1c661d00b06174910305d612.png)
Реализуем метод `OnComponentBeginOverlap` у компонента `SphereCollision`.
![a47632800593f28a4d592b8b5d9d0a5c.png](../images/a47632800593f28a4d592b8b5d9d0a5c.png)
![3915b4094e7b09d6ba000361fdcdfb34.png](../images/3915b4094e7b09d6ba000361fdcdfb34.png)
Добавляем сюда логику метеора.
![c8094a9ecb4cc865f4375191003c34ed.png](../images/c8094a9ecb4cc865f4375191003c34ed.png)
![0f8fc7a03af5c5b4159d4c1421ce1f4b.png](../images/0f8fc7a03af5c5b4159d4c1421ce1f4b.png)
![9252a0f449c26f378e36fcf89d847e78.png](../images/9252a0f449c26f378e36fcf89d847e78.png)
![9a7d9aa67851352f7d8b86e6f82442d9.png](../images/9a7d9aa67851352f7d8b86e6f82442d9.png)
Логика работает следующим образом: метеор, при коллизии о статик меш или землю прекращает движение, проигрывается визуальный эффект взрыва, сфера метеора - скрывается, происходит `Sphere Trace` в заданном радиусе на окружающих метеор персонажей, каждый персонаж, попавший в радиус, получает на себя эффект урона.
#### Добавляем способность, применяющую метеор
Для этого в `Content Browser` нажимаем на кнопку `Add -> Blueprint Class`. В появившемся окне, на вкладке `All Classes` ищем класс - `CoreGameplayAbility`.
![e3f80d1163ab005edef045ec35b0b34a.png](../images/e3f80d1163ab005edef045ec35b0b34a.png)
Создаем новый `blueprint` на его основе. Называем новый класс `GA_CastMeteor`.
![e820f7ec4a643a6667f676c0370fd042.png](../images/e820f7ec4a643a6667f676c0370fd042.png)
Открываем `blueprint`. Устанавливаем слот, из которого способность будет запускаться, на `Meteor Slot`.
![220b97f17e3eb78b08efaccc4e3b114a.png](../images/220b97f17e3eb78b08efaccc4e3b114a.png)
Добавляем код.
![fff431cf59bc85f0b0df9e0347b34814.png](../images/fff431cf59bc85f0b0df9e0347b34814.png)
![e22f45e6dba9dfbc1a5e6928bc256924.png](../images/e22f45e6dba9dfbc1a5e6928bc256924.png)
![8eacb735a631388c7ff2936fdc6edf2f.png](../images/8eacb735a631388c7ff2936fdc6edf2f.png)
![13bdba1829218caec2263d79cee0e726.png](../images/13bdba1829218caec2263d79cee0e726.png)
![5caf8db6047ae734dea92847d698288a.png](../images/5caf8db6047ae734dea92847d698288a.png)
![624cbf760646620294c0cee556cae66f.png](../images/624cbf760646620294c0cee556cae66f.png)
![e9d81c541e69006b012381c42a3b30af.png](../images/e9d81c541e69006b012381c42a3b30af.png)
Добавляем способность в список предоставляемых "по умолчанию", через `DataAsset` - `DA_PlayerAbilities`.
![d5ff03643f498e3ac49479a3c30b952e.png](../images/d5ff03643f498e3ac49479a3c30b952e.png)
Способность уже можно применять.
![e05e067ddcbd08c47557af370df2b21f.png](../images/e05e067ddcbd08c47557af370df2b21f.png)
![7bf36df5b0c0f6ce70a243c46811b472.png](../images/7bf36df5b0c0f6ce70a243c46811b472.png)
Урон наносится, импакт проигрывается исправно, однако, этой способности не хватает эффекта кулдауна и расхода маны.
#### Исправляем интерфейс
Я, изначально, не планировал добавлять эту способность, поэтому не вывел ману, в качестве характеристики в интерфейс. Давайте исправим этот недочет, это достаточно просто и быстро сделать.
Открываем виджет интерфейса, `WB_Hud` и добавляем в него индикатор маны.
![08c637bc169aac704a1463ca78749911.png](../images/08c637bc169aac704a1463ca78749911.png)
Копируем логику работы аттрибута `Health` и  перенастраиваем его под ману.
![3f236b32c41b8132cc98ea4655eb561e.png](../images/3f236b32c41b8132cc98ea4655eb561e.png)
![73664c8d71317f746b630466d92182c9.png](../images/73664c8d71317f746b630466d92182c9.png)
#### Добавляем эффект `GE_MeteorCost`
Эффекты `Cost` задаются в поле `Cost Gameplay Effect Class`.
![8314f2993ab5014f1697f3254d76bd4e.png](../images/8314f2993ab5014f1697f3254d76bd4e.png)
Достаточно ли ресурсов для применения способности или нет, можно проверить нодой `CheckAbilityCost`.
![3596b97700ba53987fb57363d2b1dc12.png](../images/3596b97700ba53987fb57363d2b1dc12.png)
Эффект `cost` применяется и ресурсы отнимаются, при вызове ноды `CommitAbility`.
![0f5aaf8618f3c437ff6fa3557a9ccf7a.png](../images/0f5aaf8618f3c437ff6fa3557a9ccf7a.png)
Все эффекты `cost` имеют `Duration Policy` - `Instant`.
![70dffac8bfdeba1d25a35569aea80ee3.png](../images/70dffac8bfdeba1d25a35569aea80ee3.png)
При этом такие эффекты имеют модификаторы, необходимые для изменения зависимых характеристик. Например в эффекте `GE_MeteorCost` я отнимаю `30` единиц маны.
#### Добавляем `GE_MeteorCooldown`
Кулдаун способности можно задать в параметре `Cooldown Gameplay Effect Class`.
![6106ab849300f4e9e142dee805a015e1.png](../images/6106ab849300f4e9e142dee805a015e1.png)
Эффект, работающий как кулдаун, не имеет модификаторов. Все что в нем есть - `Duration Policy` = `Has Duration`. Значение кулдауна задается как продолжительность эффекта, в поле `Duration Magnitude`.
![edd74159823dc8465585bbe4a9e78222.png](../images/edd74159823dc8465585bbe4a9e78222.png)
Активен кулдаун или нет, можно проверит нодой `CheckAbilityCooldown`.
![44abeefad0742227072f166e32421a2d.png](../images/44abeefad0742227072f166e32421a2d.png)
Кулдауны активируются, при применении ноды `CommitAbility`.
![0f5aaf8618f3c437ff6fa3557a9ccf7a.png](../images/0f5aaf8618f3c437ff6fa3557a9ccf7a.png)
Логика способности работает так: сначала мы проверяем, находится наша способность на кулдауне или нет. Если она не находится на кулдауне - вызываем таргетинг, если таргетинг успешен - отправляем способность на кулдаун и вызываем метеор по цели.
![16d4b3955b3e6c32bacbc1d049d8c651.png](../images/16d4b3955b3e6c32bacbc1d049d8c651.png)
Выведем иконку кулдауна в интерфейс.
Я нарисовал следующую иконку в `Gimp`, найти ее можно в папке "Дополнительно" книги.
![d7cf8ca43cf40a42ef303d44583642e7.png](../images/d7cf8ca43cf40a42ef303d44583642e7.png)
Добавляем новую иконку в интерфейс.
![4039b68a70f9e263f8588ac09674ee50.png](../images/4039b68a70f9e263f8588ac09674ee50.png)
Прописываем для нее логику.
![afe91f8383c4c96003f5d0ccec95ef4b.png](../images/afe91f8383c4c96003f5d0ccec95ef4b.png)
Теперь, при запуске метеорита, вы будете видеть его кулдаун.
![e851cb2d87f7a168e9c23930f7bfb90a.png](../images/e851cb2d87f7a168e9c23930f7bfb90a.png)
# Работа с ассетами и данными
(Assets and Data Management)
Рассмотрим методы индексации ассетов, а так-же файлы `Data Asset`.
## Подготовка к работе
### Создаем модуль
Cоздадим модуль `DataAccess`.
![06c4fd0edbf4454b5139d9dcf621a781.png](../images/06c4fd0edbf4454b5139d9dcf621a781.png)
После генерации модуля не забудьте выполнить команду `Tools -> Refresh Visual Studio 2022 Project`, чтобы файлы появились в Visual Studio.
![8d3c9df261421a091b3b44bd0cb4a48b.png](../images/8d3c9df261421a091b3b44bd0cb4a48b.png)
### Создаем `ActorComponent`
Теперь добавим `ActorComponent`, в котором и будем писать наш код.
Идем `Tools -> New C++ Class -> Actor Component`.
![18025c01963dfbf9b8aa50a7c0e71515.png](../images/18025c01963dfbf9b8aa50a7c0e71515.png)
Называем компонент `DataAccessComponent`.
![4ce015213a61bbebbcb09670bff20a91.png](../images/4ce015213a61bbebbcb09670bff20a91.png)
Не забудьте выполнить команду `Tools -> Refresh Visual Studio 2022 Project`, чтобы файл компонента появился в Visual Studio.
![800bd7556f379abc0a12c40db033b32f.png](../images/800bd7556f379abc0a12c40db033b32f.png)
Теперь добавим полученный компонент к `BP_ThirdPersonCharacter`.
![f42ae2a754a2aa747bceb82c0ca972bb.png](../images/f42ae2a754a2aa747bceb82c0ca972bb.png)
### Зависимости сборки
Прописываем в файл `DataAccess.build.cs` следующие зависимости:
```cpp
using UnrealBuildTool;
public class DataAccess : ModuleRules
{
	public DataAccess(ReadOnlyTargetRules Target) : base(Target)
	{
        PCHUsage = PCHUsageMode.UseExplicitOrSharedPCHs;
		PublicDependencyModuleNames.AddRange(new string[] {
			"Core",
			"CoreUObject",
			"Engine",
			"TestModule",
			"CharactersModule",
            "GameplayTags",
            "InputCore",
            "EnhancedInput"
        });
        PrivateDependencyModuleNames.AddRange(new string[] { "AssetRegistry", "TestModule" });
		PublicIncludePaths.AddRange(new string[] {"DataAccess/Public"});
		PrivateIncludePaths.AddRange(new string[] {"DataAccess/Private"});
	}
}
```
## Data Assets или ассеты данных
`UDataAsset` - это базовый класс, для организации различного рода файловых хранилищ данных. Реализуя данный класс разработчик создает заготовку, на основе которой формируются ассеты данных.
Давайте рассмотрим на практике.
Идем `Tools->New C++ Class -> AllClasses` и ищем `Data Asset`.
![458a8593d0f8abe133ef10ac8c328ecb.png](../images/458a8593d0f8abe133ef10ac8c328ecb.png)
Нас интересует пока просто обычный `Data Asset`, не `Primary Data Asset`, о нем я поговорю ниже.
Добавляем новый `Data Asset` в модуль `DataAccess`, называем - `CharacterData`.
![e0d8d0c69e7e937f06c47b50d0fac4e0.png](../images/e0d8d0c69e7e937f06c47b50d0fac4e0.png)
Далее обновляем индексацию проекта командой `Tools->Refresh Visual Studio 2022 Project`, чтобы добавленный файл появился в `Solution Explorer`.
![cc418223e9708e4ce72a3561c096befe.png](../images/cc418223e9708e4ce72a3561c096befe.png)
В полученном классе `UCharacterData` мы можем выстроить модель данных, которая будет храниться внутри файлов `DataAsset`. Делается это через банальное добавление переменных с использованием макроса `UPROPERTRY`.
```cpp
// Packages used:
// "GameplayTags", "EnhancedInput", "TestModule"
#include "NativeGameplayTags.h" // gameplay tags
#include "InputAction.h" // enhanced input
#include "InputMappingContext.h"
// Item Types Enum
// This enum size limited by 8 maximum items since it's bitmask enum from uint8
UENUM(BlueprintType, meta = (Bitflags, UseEnumValuesAsMaskValuesInEditor = "true"))
enum class EItemTypes : uint8
{
	NONE = 0 UMETA(Hidden),
	Default = 1 << 0,
	Helmet = 1 << 1,
	Gloves = 1 << 2,
	Sword = 1 << 3,
	Boots = 1 << 4,
};
ENUM_CLASS_FLAGS(EItemTypes);
/**
 * Character Data Asset
 */
UCLASS()
class DATAACCESS_API UCharacterData : public UDataAsset
{
	GENERATED_BODY()
public:
	// Input Action as parameter in Data Asset
	UPROPERTY(EditDefaultsOnly, Category = "Input")
	TSoftObjectPtr<UInputAction> InputAction = nullptr;
	// Input Context as parameter in Data Asset
	UPROPERTY(EditDefaultsOnly, Category = "Input")
	TSoftObjectPtr<UInputMappingContext> InputContext = nullptr;
	// Gameplay tag locked category
	UPROPERTY(EditDefaultsOnly, Category = "GameplayTags", meta = (Categories = "Locomotion.Grounded"))
	FGameplayTag GroundedLocomotionMode;
	// Edit Condition
	UPROPERTY(BlueprintReadOnly, EditDefaultsOnly, Category = "EditConditions", meta = (EditCondition = "bVarCanBeEdited"/*, EditConditionHides*/))
	FString EditConditionTestVariable = "Test";
	UPROPERTY(BlueprintReadOnly, EditDefaultsOnly, Category = "EditConditions", meta = (InlineEditConditionToggle))
	bool bVarCanBeEdited = false;
	// Item Type Enum
	UPROPERTY(BlueprintReadOnly, EditDefaultsOnly, Category = "EditConditions|Enum")
	EItemTypes ItemType = EItemTypes::Boots;
	// Bit Flags variable
	UPROPERTY(BlueprintReadOnly, EditDefaultsOnly, Category = "BitFlags", meta = (Bitmask, BitmaskEnum = "/Script/TestModule.EItemTypes"))
	int32 ItemTypeFlags = StaticCast<int32>(EItemTypes::Helmet | EItemTypes::Gloves);
	// enum based edit condition
	UPROPERTY(BlueprintReadOnly, EditDefaultsOnly, Category = "EditConditions|Enum", meta = (EditCondition = "ItemType != EItemTypes::Sword", EditConditionHides))
	int Armor = 15;
	// BitFlag based condition
	UPROPERTY(BlueprintReadOnly, EditDefaultsOnly, Category = "EditConditions", meta = (EditCondition = "ItemTypeFlags & '/Script/TestModule.EItemTypes::Sword'"))
	FString EditConditionBitFlags = "This Editable With BitFlag";
	// Array Test
	UPROPERTY(BlueprintReadOnly, EditDefaultsOnly, Category = "Arrays")
	TArray<int32> TestIntArray;
	// Map. Filled in constructor
	UPROPERTY(BlueprintReadOnly, EditDefaultsOnly, Category = "Map")
	TMap<FString, float> TestMap;
	// Test Output Function
	UFUNCTION(BlueprintPure, Category = "Map")
	TMap<FString, float> GetTestMap() const {
		return TestMap;
	}
};
```
В код я перенес наиболее актуальные примеры переменных, которые так или иначе могут быть использованы на практике.
После добавления кода и его компиляции - самое время создать непосредственно `DataAsset` на основе класса `UCharacterData`.
Для этого идем в `Content Browser -> Add -> Miscellaneous -> Data Asset`.
![8f3aa4c819acb97d59ecccd855810cfc.png](../images/8f3aa4c819acb97d59ecccd855810cfc.png)
Перед вами появится меню генерации `Data Asset`, в котором необходимо будте выбрать тип, на основе которого этот ассет создается. В нашем случае это `CharacterData`.
![069b87c9d1560527992e277ceea7db0c.png](../images/069b87c9d1560527992e277ceea7db0c.png)
После создания вы увидите новый `DataAsset`, в котором будут содержаться все поля, которые мы указали в классе.
![0e901eed97c33b0e4bbaaf027be21fab.png](../images/0e901eed97c33b0e4bbaaf027be21fab.png)
![2119d44533b8448b1565b290214e1b34.png](../images/2119d44533b8448b1565b290214e1b34.png)
Особое внимание прошу обратить на параметры типа `EditCondition`. Их крайне часто применяют именно в файлах типа `DataAsset`, потому что таким образом можно обеспечить разные типы настроек для разных типов параметров.
В нашем примере, если выбрать для параметра `Item Type` тип `Helmet` то появится параметр `Armor`.
![615b15da2b791d308c03aea1688f48bb.png](../images/615b15da2b791d308c03aea1688f48bb.png)
Если же выбрать `Sword` то параметр пропадет.
![7da9dd857c56731740931f0eab5a6956.png](../images/7da9dd857c56731740931f0eab5a6956.png)
Вы можете крайне успешно применять похожую логику. Более подробно про `EditCondition` я уже писал в статье про макросы.
Рассмотрим получение ссылок на `DataAsset`.
### `DataAsset UPROPERTY`
```cpp
//.h
//Data asset refrence
UPROPERTY(BlueprintReadWrite, EditAnywhere, Category = "VariableExperiments")
UDataAsset* DataAssetRefrence;
//...
//.cpp
// default value
if (DataAssetRefrence == nullptr) {
    const FString Path = TEXT("/Script/DataAccess.CharacterData'/Game/Characters/Data/DA_Character.DA_Character'");
    DataAssetRefrence = UKismetSystemLibrary::MakeSoftObjectPath(Path);
}
```
![792d495586ea94dee866a28fe382db8e.png](../images/792d495586ea94dee866a28fe382db8e.png)
### Чтение данных из `DataAsset` по ссылке
Делается элементарно.
```cpp
#include "CharacterData.h"
//...
UCharacterData* Asset = LoadObject<UCharacterData>(NULL, TEXT("/Script/DataAccess.CharacterData'/Game/Characters/Data/DA_Character.DA_Character'"));
UE_LOG(LogTemp, Warning, TEXT("GroundedLocomotionMode = %s"), *Asset->GroundedLocomotionMode.ToString());
```
![2e35ff6a302bbbd2950e5904eeb67626.png](../images/2e35ff6a302bbbd2950e5904eeb67626.png)
## AssetRegistry или реестр ассетов
`AssetRegistry` или реестр ассетов - это интерфейс файловой системы движка Unreal Engine.
Реестр ассетов хранит ссылки на все файлы в директории проекта. Это позволяет выбирать из файловой системы, например, файлы данных, относящиеся к предметам инвентаря или к кастомизации персонажа.
Используя реестр можно создавать `ui` виджеты, элементы в которых появляются или удаляются автоматически, при добавлении или удалении файлов из директории проекта.
К примеру: можно сканировать папки с текстурами кожи или прическами, а потом выдавать результат сканирования напрямую в `ui` виджет кастомизации персонажа. Такой подход позволяет работать без использования `Data Table` и сторонних хранилищ данных. То есть вам не придется вручную всякий раз указывать файлы причесок в таблице или `DataAsset`, если виджет сам будет искать их в нужной папке.
Реестр ассетов относительно быстр, но при большом количестве файлов (тысячи), их фильтрование может создавать проблемы.
`Content Browser` в Unreal Engine работает, используя `AssetRegistry`.
![2ac2524b57c3e49ef72db1d2bf436e3d.png](../images/2ac2524b57c3e49ef72db1d2bf436e3d.png)
Несмотря на тот факт, что реестр ассетов используется, в основном, редактором Unreal Engine, по своей сущности он является `runtime` модулем и может быть использован в других `runtime` модулях.
Найти модуль реестра ассетов можно по пути: `\Runtime\AssetRegistry\`.
![9daba809dee766f2beb1544927253bd6.png](../images/9daba809dee766f2beb1544927253bd6.png)
В папке модуля нам особенно интересен сам интерфейс реестра ассетов, это интерфейс (класс) `IAssetRegistry`, найти его можно по пути `\Runtime\AssetRegistry\Public\AssetRegistry\IAssetRegistry.h`.
Данный интерфейс является классическим с++ синглтоном, не подсистемой (как написано в официальной документации), а именно просто синглтоном через `static` переменную.
Сам синглтон можно найти в файле `\Runtime\CoreUObject\Public\Misc\AssetRegistryInterface.h` и выглядит он так.
![65247d693ba23c501f1c1f7e23ca8cd8.png](../images/65247d693ba23c501f1c1f7e23ca8cd8.png)
В интерфейс он выводится так:
![f150aa7492f311d6594634c830d6e64f.png](../images/f150aa7492f311d6594634c830d6e64f.png)
Доступ к этому синглтону извне проекта можно получить обратившись к конструктору модуля. То есть к файлу `AssetRegistryModule.h`, в котором есть метод `Get`. Данный метод возвращает указатель на синглтон реестра ассетов.
![c7b5529e088eab32acad4281c3b17cd5.png](../images/c7b5529e088eab32acad4281c3b17cd5.png)
Его мы и будем использовать.
Однако, прежде чем писать код, надо удовлетворить зависимости между модулями.
Находим в Solution Explorer наш модуль `DataAccess` (создавали в главе "подготовка к работе") идем в файл `DataAccess.build.cs` и добавляем туда строчку:
```cpp
PrivateDependencyModuleNames.AddRange(new string[] { "AssetRegistry" });
```
![8610381d34037241595be74be2bea544.png](../images/8610381d34037241595be74be2bea544.png)
Теперь модуль `AssetRegistry` будет загружаться вместе с модулем `DataAccess`. Так-же, из модуля `DataAccess` мы получаем возможность делать `#include` к файлам `AssetRegistry`.
В частности, нас интересует этот файл:
```cpp
#include "AssetRegistry/AssetRegistryModule.h" // Asset Registry
```
Подключаем его в файле `DataAccessComponent.h`.
![942969b404a1f3cdd1343d5cb1697cd2.png](../images/942969b404a1f3cdd1343d5cb1697cd2.png)
Чаще всего реестр ассетов используют для фильтрования файлов в папках. Это и рассмотрим.
### Получение списка ассетов по их классу
Давайте получим список файлов в папке `/Game/LevelPrototyping/Meshes`. Эта папка содержит базовые заготовки мешей для шаблона `ThirdPerson`.
![6117e720412ee62b9b8fcaf585dc4d12.png](../images/6117e720412ee62b9b8fcaf585dc4d12.png)
Все меши в папке являются `StaticMesh`.
Получение ассетов типа `UStaticMesh` из папки `/Game/LevelPrototyping/Meshes` выглядит так:
```cpp
//.h
#include "AssetRegistryModule.h" // Asset Registry
#include "AssetRegistry/ARFilter.h" // Asset Registry Filter
//...
//.cpp
// BeginPlay
FAssetRegistryModule& Registry = FModuleManager::LoadModuleChecked<FAssetRegistryModule>("AssetRegistry");
TArray<FAssetData> AssetData; // This Array will contain our assets
FARFilter Filter; // Assets will be filtered by this
Filter.ClassPaths.Add(UStaticMesh::StaticClass()->GetClassPathName()); // add class to filter
Filter.PackagePaths.Add("/Game/LevelPrototyping/Meshes"); // all location to filter
Registry.Get().GetAssets(Filter, AssetData); // apply filter and get results
for (FAssetData Asset : AssetData) { // loop thru found assets
    Asset.PrintAssetData();
}
```
Тут, для выбора ассетов, я использовал два фильтра - по классу `Filter.ClassPaths` и по пути `Filter.PackagePaths`.
Если запустить проект - в логе можно будет увидеть отчет по каждому файлу, найденному в папке.
![3e020408d4a85f3773e2d7e22048598c.png](../images/3e020408d4a85f3773e2d7e22048598c.png)
К сожалению, такой лог не очень то читабельный. Поэтому лучше использовать такой код:
```cpp
//.h
#include "AssetRegistryModule.h" // Asset Registry
#include "AssetRegistry/ARFilter.h" // Asset Registry Filter
//...
//.cpp
// BeginPlay
// get assets of class UStaticMesh in folder /Game/LevelPrototyping/Meshes
UE_LOG(LogTemp, Warning, TEXT("=== Get Assets of class UStaticMesh in folder /Game/LevelPrototyping/Meshes ==="));
FAssetRegistryModule& Registry = FModuleManager::LoadModuleChecked<FAssetRegistryModule>("AssetRegistry");
TArray<FAssetData> AssetData; // This Array will contain our assets
FARFilter Filter; // Assets will be filtered by this
Filter.ClassPaths.Add(UStaticMesh::StaticClass()->GetClassPathName()); // add class to filter
Filter.PackagePaths.Add("/Game/LevelPrototyping/Meshes"); // all location to filter
Registry.Get().GetAssets(Filter, AssetData); // apply filter and get results
for (FAssetData Asset : AssetData) { // loop thru found assets
    //Asset.PrintAssetData();
    UE_LOG(LogTemp, Warning, TEXT("AssetName: %s"), * Asset.AssetName.ToString());
}
```
![cd7a0bcf42580713dea04f6ca4a8d16a.png](../images/cd7a0bcf42580713dea04f6ca4a8d16a.png)

> Если убрать фильтр по пути, можно получить список вообще всех ассетов `UStaticMesh` в проекте.

### Получение списка `blueprint` классов, унаследованных от `UObjectTestClass`
Теперь получим список `blueprint` классов, унаследованных от `UObjectTestClass`. Проблема в том, что мы не можем использовать метод `::StaticClass()` для выявления `blueprint` ассетов. Например, следующий код выдаст пустой результат при попытке найти `blueprint` класы `UObjectTestClass` в проекте.
```cpp
FAssetRegistryModule& Registry = FModuleManager::LoadModuleChecked<FAssetRegistryModule>("AssetRegistry");
TArray<FAssetData> AssetData; // This Array will contain our assets
FARFilter Filter; // Assets will be filtered by this
Filter.ClassPaths.Add(UObjectTestClass::StaticClass()->GetClassPathName()); // add class to filter
Filter.bRecursiveClasses = true; // to include subclasses of given class
Registry.Get().GetAssets(Filter, AssetData); // apply filter and get results
UE_LOG(LogTemp, Warning, TEXT("Found Amount = %i"), AssetData.Num());
for (FAssetData Asset : AssetData) { // loop thru found assets
    //Asset.PrintAssetData();
    UE_LOG(LogTemp, Warning, TEXT("AssetName: %s"), *Asset.AssetName.ToString());
}
UE_LOG(LogTemp, Warning, TEXT("======"));
```
![5fddb0c3f8a4222ef9c00a1008030365.png](../images/5fddb0c3f8a4222ef9c00a1008030365.png)
Единственный способ получить `blueprint` классы через фильтрование сводится к применению типа `UBlueprint` в фильтре. Это выдаст вообще все `blueprint` классы, которые потом можно отфильтровать в цикле, чтобы найти нужные нам.
```cpp
// module
PublicDependencyModuleNames.AddRange(new string[] { "Core", "CoreUObject", "Engine", "TestModule"});
//.h
#include "AssetRegistryModule.h" // Asset Registry
#include "AssetRegistry/ARFilter.h" // Asset Registry Filter
#include "ObjectTestClass.h" // to access UObjectTestClass
//...
// .cpp
//BeginPlay
// get blueprint assets of class UObjectTestClass
UE_LOG(LogTemp, Warning, TEXT("=== Get Blueprint Assets of class UObjectTestClass ==="));
FAssetRegistryModule& Registry = FModuleManager::LoadModuleChecked<FAssetRegistryModule>("AssetRegistry");
IAssetRegistry& AssetRegistry = Registry.Get();
TArray<FAssetData> AssetData; // This Array will contain our assets
FARFilter Filter; // Assets will be filtered by this
// add blueprint class to filter, it will list us all the bp classes in project
// UBlueprint - for blueprint classes
// UWidgetBlueprint  - for bp widget classes
Filter.ClassPaths.Add(UBlueprint::StaticClass()->GetClassPathName());
Filter.bRecursiveClasses = true; // to include subclasses of given class
// in loop we will use this set to filter only blueprints with class UObjectTestClass
TSet<FName> DerivedNames;
{
    TArray< FName > BaseNames;
    BaseNames.Add(UObjectTestClass::StaticClass()->GetFName()); // we will search for UObjectTestClass
    //BaseNames.Add(AActor::StaticClass()->GetFName());
    TSet<FName> Excluded;
    AssetRegistry.GetDerivedClassNames(BaseNames, Excluded, DerivedNames);
}
AssetRegistry.GetAssets(Filter, AssetData); // apply filter and get results
UE_LOG(LogTemp, Warning, TEXT("Found Amount = %i"), AssetData.Num());
for (FAssetData Asset : AssetData) { // loop thru found assets
    //Asset.PrintAssetData();
    //Asset.GetClass();
    //Asset.GetAsset();
    FString GeneratedClassPathRef = Asset.TagsAndValues.FindRef(TEXT("GeneratedClass"));
    if(GeneratedClassPathRef == "") continue; // it's not a blueprint
    const FString ClassObjectPath = FPackageName::ExportTextPathToObjectPath(*GeneratedClassPathRef);
    const FString ClassName = FPackageName::ObjectPathToObjectName(ClassObjectPath);
    // skip blueprints that are not in list of DerivedNames
    if (!DerivedNames.Contains(*ClassName)) continue;
    UE_LOG(LogTemp, Warning, TEXT("Class Path: %s"), *ClassObjectPath);
    UE_LOG(LogTemp, Warning, TEXT("Class Name: %s"), *ClassName);
    UE_LOG(LogTemp, Warning, TEXT("Class Name Classic: %s"), *Asset.GetClass()->GetName());
    UE_LOG(LogTemp, Warning, TEXT("AssetName: %s"), *Asset.AssetName.ToString());
    UE_LOG(LogTemp, Warning, TEXT("======"));
}
```
Используя данный код я нашел все `blueprint` потомки класса `UObjectTestClass` (он всего один, на данный момент).
![fbf9157813070142e1ac7beffdf63acc.png](../images/fbf9157813070142e1ac7beffdf63acc.png)
![83cf62995daa8bec141125dad868c1ae.png](../images/83cf62995daa8bec141125dad868c1ae.png)
Обратите внимание на лог, а именно на строчку `Class Name Classic`, которая имеет значение `Blueprint`. Таким образом показано, что вы не можете взять класс у `blueprint` асета стандартным способом, через `Asset.GetClass()`. Класс `blueprint` ассета можно получить через параметр `GeneratedClass`.
Доступ к полю `GeneratedClass` можно получить через `TagsAndValues`:
```cpp
Asset.TagsAndValues.FindRef(TEXT("GeneratedClass"))
```
Полученная строчка возвращает ту-же самую ссылку, которую вы можете получить, кликнув по ассету правой кнопкой мыши `-> Copy Refrence`.
![95022cfb8fe42b5b7d4e357dd99105fd.png](../images/95022cfb8fe42b5b7d4e357dd99105fd.png)
Далее эту строчку можно использовать в стандартном цикле загрузки ассета.
Так-же параметр `GeneratedClass` можно получить через приведение типа:
```cpp
UBlueprint* FoundBP = Cast<UBlueprint>(Asset.GetAsset());
FoundBP->GeneratedClass
```
В этом случае код будет выглядеть так:
```cpp
// module
PublicDependencyModuleNames.AddRange(new string[] { "Core", "CoreUObject", "Engine", "TestModule"});
//.h
#include "AssetRegistryModule.h" // Asset Registry
#include "AssetRegistry/ARFilter.h" // Asset Registry Filter
#include "ObjectTestClass.h" // to access UObjectTestClass
//...
// .cpp
//BeginPlay
// get blueprint assets of class UObjectTestClass, with each found loading
UE_LOG(LogTemp, Warning, TEXT("=== Get Blueprint Assets of class UObjectTestClass using Cast ==="));
FAssetRegistryModule& Registry = FModuleManager::LoadModuleChecked<FAssetRegistryModule>("AssetRegistry");
IAssetRegistry& AssetRegistry = Registry.Get();
TArray<FAssetData> AssetData; // This Array will contain our assets
FARFilter Filter; // Assets will be filtered by this
// add blueprint class to filter, it will list us all the bp classes in project
// UBlueprint - for blueprint classes
// UWidgetBlueprint  - for bp widget classes
Filter.ClassPaths.Add(UBlueprint::StaticClass()->GetClassPathName());
Filter.bRecursiveClasses = true; // to include subclasses of given class
// in loop we will use this set to filter only blueprints with class UObjectTestClass
TSet<FName> DerivedNames;
{
    TArray< FName > BaseNames;
    BaseNames.Add(UObjectTestClass::StaticClass()->GetFName()); // we will search for UObjectTestClass
    //BaseNames.Add(AActor::StaticClass()->GetFName());
    TSet<FName> Excluded;
    AssetRegistry.GetDerivedClassNames(BaseNames, Excluded, DerivedNames);
}
AssetRegistry.GetAssets(Filter, AssetData); // apply filter and get results
UE_LOG(LogTemp, Warning, TEXT("Found Amount = %i"), AssetData.Num());
for (FAssetData Asset : AssetData) { // loop thru found assets
    //Asset.PrintAssetData();
    //Asset.GetClass();
    //Asset.GetAsset();
    UBlueprint* FoundBP = Cast<UBlueprint>(Asset.GetAsset());
    if (FoundBP == nullptr) continue; // it's not a blueprint
    const FString ClassObjectPath = FPackageName::ExportTextPathToObjectPath(*FoundBP->GeneratedClass->GetClassPathName().ToString());
    const FString ClassName = FPackageName::ObjectPathToObjectName(ClassObjectPath);
    // skip blueprints that are not in list of DerivedNames
    if (!DerivedNames.Contains(*ClassName)) continue;
    UE_LOG(LogTemp, Warning, TEXT("Class Object Path: %s"), *ClassObjectPath);
    UE_LOG(LogTemp, Warning, TEXT("Class Name: %s"), *ClassName);
    UE_LOG(LogTemp, Warning, TEXT("Class Name Classic: %s"), *Asset.GetClass()->GetName());
    UE_LOG(LogTemp, Warning, TEXT("AssetName: %s"), *Asset.AssetName.ToString());
    UE_LOG(LogTemp, Warning, TEXT("======"));
}
```
![be5f13415e5f3f17a3984a751ffdd1d5.png](../images/be5f13415e5f3f17a3984a751ffdd1d5.png)
Такой подход  можно применять как для получения списка `UBlueprint` классов, так и для классов `UWidgetBlueprint`.
Важно учитывать тот факт, что в упакованной к релизу игре нет никаких `UBlueprint` ассетов. Там есть лишь объекты типа `UBlueprintGeneratedClass`. Поэтому в цикле, при фильтрации, мы не можем использовать `Asset.GetClass()` для сравнения классов. Вместо этого необходимо использовать информацию о том, какой именно `UBlueprintGeneratedClass` будет сгенерирован системой для текущего `blueprint` ассета. Эта информация может быть получена из тега `GeneratedClass`. Я получаю ее в строчке `Asset.TagsAndValues.FindRef(TEXT("GeneratedClass"))`. Эта строчка гарантирует, что код будет работать и в запакованном проекте.
### Фильтрование ассетов по значениям их параметров `AssetRegistrySearchable`
Некоторым параметрам классов можно сообщить свойство `AssetRegistrySearchable`, чтобы сделать эти параметры доступными для поиска через реестр ассетов.
Например я добавил такой параметр в наш `UObjectTestClass`:
```cpp
// Allow asset registry to filter assets with this field using
UPROPERTY(EditAnywhere, Category = "AssetRegistryMark", AssetRegistrySearchable)
int ParameterFilter = 0;
```
![af1cfb2817ea7507a708da5e750f2cc3.png](../images/af1cfb2817ea7507a708da5e750f2cc3.png)
Значение параметра тоже важно. Мы можем фильтровать классы и по названию параметров и по их значению.
Делается это так.
```cpp
// module
PublicDependencyModuleNames.AddRange(new string[] { "Core", "CoreUObject", "Engine", "TestModule"});
//.h
#include "AssetRegistryModule.h" // Asset Registry
#include "AssetRegistry/ARFilter.h" // Asset Registry Filter
#include "ObjectTestClass.h" // to access UObjectTestClass
//...
// .cpp
//BeginPlay
// get assets by AssetRegistrySearchable TagsAndValues
UE_LOG(LogTemp, Warning, TEXT("=== Get Assets by AssetRegistrySearchable TagsAndValues ==="));
FAssetRegistryModule& Registry = FModuleManager::LoadModuleChecked<FAssetRegistryModule>("AssetRegistry");
IAssetRegistry& AssetRegistry = Registry.Get();
TArray<FAssetData> AssetData; // This Array will contain our assets
FARFilter Filter; // Assets will be filtered by this
FString ClassPath = UObjectTestClass::StaticClass()->GetClassPathName().ToString();
UE_LOG(LogTemp, Warning, TEXT("Class Path: %s"), *ClassPath);
Filter.TagsAndValues.Add(FName("ParameterFilter"), FString("1")); // ParameterName / Value
// To Allow asset registry filter assets by property field use it this way:
// UPROPERTY(EditAnywhere, Category = "AssetRegistryMark", AssetRegistrySearchable)
// int ParameterFilter = 0;
AssetRegistry.GetAssets(Filter, AssetData); // apply filter and get results
for (FAssetData Asset : AssetData) { // loop thru found assets
    //Asset.PrintAssetData();
    UE_LOG(LogTemp, Warning, TEXT("AssetName: %s"), *Asset.AssetName.ToString());
}
UE_LOG(LogTemp, Warning, TEXT("======"));
```
![f8586bb65e6d1bb3406f5c57e6d7aa87.png](../images/f8586bb65e6d1bb3406f5c57e6d7aa87.png)
Если в строке фильтра `Filter.TagsAndValues.Add(FName("ParameterFilter"), FString("0"))` установить значение `FString("0")`, например в `FString("1")`, то код ничего не найдет.
![0ae0a94489790063506ebb337c3e43ad.png](../images/0ae0a94489790063506ebb337c3e43ad.png)
![361f63a3b19566cf29a22044fc4712a6.png](../images/361f63a3b19566cf29a22044fc4712a6.png)
Но! Если копировать `blueprint` ассет с этим параметром, зайти туда и поставить значение 1 для строки `ParameterFilter`, то файл будет найден.
![e5bea4f96199bceb836f2a5e3a4dadf5.png](../images/e5bea4f96199bceb836f2a5e3a4dadf5.png)
![1fbcbf1edf32ee1378162851cc8cc2f6.png](../images/1fbcbf1edf32ee1378162851cc8cc2f6.png)
![ae02881306375e280ec5771fe9962877.png](../images/ae02881306375e280ec5771fe9962877.png)
Используя метки типа `AssetRegistrySearchable` можно очень эффективно работать с ассетами данных `UPrimaryDataAsset` и виджетами.
### Реестр ассетов в `blueprint` коде
В `blueprint` коде есть глобальная нода `AssetRegistry`, которую можно вызвать из любого места.
![1a3ef94e0b651076bc85d24980b76ce5.png](../images/1a3ef94e0b651076bc85d24980b76ce5.png)
Однако возможности `blueprint` интерфейса сильно ограничены. Например там нельзя создать `literal class name`, что создает проблемы при задании параметров дял структур типа `TopLevelAssetPath`.
### Спавн всех `blueprint` `Actor'ов` в папке
Помимо умения искать ассеты на диске, не менее важно понимать, как их использовать.
Давайте заспавним перед нашим персонажем `blueprint` классы, унаследованные от `UObjectTestClass`, найденные в папке `/Game/Characters` .
```cpp
// spawn all Actor blueprint assets in folder /Game/Characters
UE_LOG(LogTemp, Warning, TEXT("=== Spawn All Actor Blueprint Assets in folder /Game/Characters ==="));
FAssetRegistryModule& Registry = FModuleManager::LoadModuleChecked<FAssetRegistryModule>("AssetRegistry");
IAssetRegistry& AssetRegistry = Registry.Get();
TArray<FAssetData> AssetData; // This Array will contain our assets
FARFilter Filter; // Assets will be filtered by this
// add blueprint class to filter, it will list us all the bp classes in project
Filter.ClassPaths.Add(UBlueprint::StaticClass()->GetClassPathName());
Filter.PackagePaths.Add("/Game/Characters"); // only spawn actors in this area
Filter.bRecursiveClasses = true; // to include subclasses of given class
// in loop we will use this set to filter only blueprints with class UObjectTestClass
TSet<FName> DerivedNames;
{
    TArray<FName> BaseNames;
    BaseNames.Add(AActor::StaticClass()->GetFName()); // we will search for all AActor's
    TSet<FName> Excluded;
    AssetRegistry.GetDerivedClassNames(BaseNames, Excluded, DerivedNames);
}
AssetRegistry.GetAssets(Filter, AssetData); // apply filter and get results
UE_LOG(LogTemp, Warning, TEXT("Found Amount = %i"), AssetData.Num());
int NumberSpawned = 0; // spawned actors count
for(FAssetData Asset : AssetData) { // loop thru found assets
    UBlueprint* FoundBP = Cast<UBlueprint>(Asset.GetAsset());
    if (FoundBP == nullptr) continue; // it's not a blueprint
    const FString ClassObjectPath = FPackageName::ExportTextPathToObjectPath(*FoundBP->GeneratedClass->GetClassPathName().ToString());
    const FString ClassName = FPackageName::ObjectPathToObjectName(ClassObjectPath);
    // skip blueprints that are not in list of DerivedNames
    if (!DerivedNames.Contains(*ClassName)) continue;
    // spawn found actors
    AActor* Owner = this->GetOwner();
    FActorSpawnParameters SpawnParams;
    SpawnParams.Owner = Owner;
    SpawnParams.Instigator = Owner->GetInstigator();
    SpawnParams.bNoFail = true;
    SpawnParams.SpawnCollisionHandlingOverride = ESpawnActorCollisionHandlingMethod::AdjustIfPossibleButAlwaysSpawn;
    // spawn location
    FVector CLocation = Owner->GetActorForwardVector() * 120 + // on front of owner
                        Owner->GetActorRightVector() * 100 * NumberSpawned + // offset on right based on NumberSpawned to form line
                        Owner->GetActorLocation();
    World->SpawnActor<AActor>(FoundBP->GeneratedClass, CLocation, Owner->GetActorRotation(), SpawnParams);
    UE_LOG(LogTemp, Warning, TEXT("[%i] Actor Spawned: %s"), NumberSpawned,*ClassObjectPath);
    NumberSpawned++; // increment offset
    UE_LOG(LogTemp, Warning, TEXT("======"));
}
```
Конкретно в данный момент у меня в папке `/Game/Characters` всего два `AActor'а`.
![5f8990f89e8e2b0002822eb37510878b.png](../images/5f8990f89e8e2b0002822eb37510878b.png)
Приведенный выше код заспавнит оба этих `Actor'а` перед персонажем игрока, с отступом вперед в 120 см и вправо в 100 см.
![536f4640bac2893590f1c31aa62cadbe.png](../images/536f4640bac2893590f1c31aa62cadbe.png)
![471f72a0c3e5f987ad722ba78bc18df0.png](../images/471f72a0c3e5f987ad722ba78bc18df0.png)
## Asset Manager или менеджер ассетов
Asset Manager - это встроенная в движок Unreal система индексации ассетов в файловой системе. Данная система является надстройкой над реестром ассетов. Она предоставляет удобный набор `blueprint` функций для работы ассетов, их загрузки и выгрузки.
### Проблемы менеджера ассетов
Проблемы менеджера ассетов вытекают из двух вещей: непонятной документации и устаревшей лекции на ютубе по этой системе. Менеджер ассетов появился в `Unreal Engine 4.17`, а сейчас `Unreal Engine 5`. За столь длительный промежуток система преобразилась в лучшую сторону. И там, где раньше люди вручную добавляли методы асинхронной загрузки ассетов, сегодня вручную делать уже ничего не надо.
Все уже сделано за нас.
![68f642dff89c66e4ed472200fa4d037c.png](../images/68f642dff89c66e4ed472200fa4d037c.png)
Но об этом почти никто не знает. Поэтому, большая часть этой статьи будет посвящена раскрытию предназначения системы `AssetManager` на примерах.
Наиболее подробное объяснение принципов работы `Asset Manager'а` мне удалось найти в `.pdf` файле с документацией, он приложен к книге, в секции (дополнительные материалы). Так-же скачать файл документации можно [на форуме Unreal Engine.](https://forums.unrealengine.com/t/no-editor-game-hanging-on-splash-screen-constructorhelpers-fobjectfinder-skeletalmesh-is-null/94356/2?u=alexanderst)
Отмечу, что приведенная выше документация мне скорее навредила, чем помогла.
Согласно сему мудрому(от слова муд) талмуду(тоже от слова муд), менеджер ассетов может работать лишь с классами, у которых переопределен метод `GetPrimaryAssetId`.
Хороше, подумал я, открыл класс `UObjectTestClass` (мы писали его в одной из ранних статей) и переопределил вышеуказанный метод.
```cpp
virtual FPrimaryAssetId GetPrimaryAssetId() const override {
    FPrimaryAssetId AssetId(TEXT("UObjects"), GetFName());
    return AssetId;
}
```
Теперь я иду в `blueprint` редактор и скармливаю экземпляр этого `UObject` класса узлу `Get Primary Asset Id from Class`.
В моем понимании, этот узел долежен вызвать функцию `GetPrimaryAssetId` у класса `UObjectTestClass` и вернуть ее результаты.
![9af74d23e60c477906577e7616906e29.png](../images/9af74d23e60c477906577e7616906e29.png)
Но если протестировать такой код то мы ничего не получим. Вообще ничего.
![6560cacf623032205955f8944381d178.png](../images/6560cacf623032205955f8944381d178.png)
Ни сообщений об ошибке, ни предупреждений, ничего. Понимай как хочешь.
Причем, можно прочитать миллион статей других авторов в интернете, а я это упорно делал, но все равно не понять какого хрена этот код не работает.
И знаете. Если бы я не разобрался тогда, этой статьи бы вообще небыло. В какой-то момент я решил, что система `Asset Manager` вообще не работает в текущей версии Unreal Engine из-за бага или кривого переноса (кто ее знает). Но все оказалось куда интереснее.
На странице настроек проекта `Edit -> Project Settings` можно найти пункт `Asset Manager`.

![4af6fbef98ce981f4d48ed580cf35d34.png](../images/4af6fbef98ce981f4d48ed580cf35d34.png)

Открыв этот пункт меню можно увидеть настройки для сканирования ключей ассетов - `Primary Asset Types to Scan`.

![47bf9fd53fe26aad6b4f2a26065bbd27.png](../images/47bf9fd53fe26aad6b4f2a26065bbd27.png)

> Классы, которые возвращают `GetPrimaryAssetId`, добавляются в `Asset Manager`, при условии, что возвращаемое значение функции `GetPrimaryAssetId` присутствует в списке ключей.

То есть: не достаточно переопределить метод `GetPrimaryAssetId`, надо еще и сделать так, чтобы ключ, который этот метод возвращает, присутствовал в списке `Primary Asset Types to Scan`.
Об этом "незначительной" детали нет ни единого слова ни в одном из материалов, которые мне довелось изучить. Я узнал об этой особенности случайно, когда прочитал комментарий к классу `UPrimaryDataAsset`.
Приведенный выше `blueprint` код заработал, как только я добавил ключ `UObjects` в список `Primary Asset Types to Scan`, а так-же указал, что этот тип `Has Blueprint Classes`.
![01dfb49e5ac95b07d7e7be19ffadd91b.png](../images/01dfb49e5ac95b07d7e7be19ffadd91b.png)
![87467bedf246406547bc51c3c05fbd9f.png](../images/87467bedf246406547bc51c3c05fbd9f.png)
![94a5b7686856e045ace7a12e6b34c2f5.png](../images/94a5b7686856e045ace7a12e6b34c2f5.png)
Теперь, когда нам стало понятно, как заставить `Asset Manager` работать, можно приступать к изучению его особенностей и принципов. А так-же к объяснению того, зачем он вообще нужен.
### Основные  принципы системы `Asset Manager`
Основная суть системы `Asset Manager` может быть изложена следующим образом: это высокоуровневый настраиваемый интерфейс индексации игровых ассетов.
Важно понимать, что эта система, в большей степени ориентирована на `blueprint` составляющую движка, где предоставляет удобный способ асинхронной загрузки ассетов и работы с ними.
![06d80d193ba9d13c9b0c5956c41932aa.png](../images/06d80d193ba9d13c9b0c5956c41932aa.png)
Если кликнуть на параметр `Primary Asset Id`, например, у `blueprint` узла `Get Objects from Primary Asset Id` можно увидеть список присутствующих в системе первичных ассетов.
![d4510192d433d3728b80665dd09d0ac1.png](../images/d4510192d433d3728b80665dd09d0ac1.png)
Это очень удобно. Бонусом идет возможность пройтись в цикле по всем первичным ассетам какой-либо категории, например - `UObjects`. Для этого используется `blueprint` узел `Get Primary Asset Id List`.
![cc8f249d548327f499970831c63a37b3.png](../images/cc8f249d548327f499970831c63a37b3.png)
![9b70b70f09c8c610b88cfe634dcf6f63.png](../images/9b70b70f09c8c610b88cfe634dcf6f63.png)
С менеджером ассетов можно работать и из с++, но основа мощного функционала этой системы вынесена именно в `blueprint` код. На стороне с++ мы можем расширить возможности класса `AssetManager`, добавив дополнительный функционал или изменив существующий.
#### Переопределение менеджера ассетов
Класс `AssetManager'а`, который используется в системе "по умолчанию", можно переопределить в настройках движка по пути `Edit -> Project Settings -> General Settings -> Default Classes -> Asset Manager`.
![9a8412f0587d0f6d62bb2c3ac414150c.png](../images/9a8412f0587d0f6d62bb2c3ac414150c.png)
Сам класс менеджера ассеов можно найти по пути `Runtime\Engine\Classes\Engine\AssetManager.h`.
![873b2eaf9c6d006caed9fa0a2be7cfad.png](../images/873b2eaf9c6d006caed9fa0a2be7cfad.png)
Переопределение этого класса позволяет создать собственные первичные типы ассетов `Primary Asset Types`, а так-же изменить или дополнить логику имеющихся в классе методов.
Идем в `Tools -> New C++ Class -> All Classes` и ищем в списке `AssetManager`.
![96d7281bfeb1c1098028a53cdce60859.png](../images/96d7281bfeb1c1098028a53cdce60859.png)
Называем класс `DataModuleAssetManager`, размещаем его в модуле `DataAccess`.
![4a895558a821dd8733f68a4db8e4643d.png](../images/4a895558a821dd8733f68a4db8e4643d.png)
После добавления класса не забудьте обновить проект через `Tools -> Refresh Visual Studio 2022 Project`.
![dbd5586eeda28d34e7ac39e2224f5c27.png](../images/dbd5586eeda28d34e7ac39e2224f5c27.png)
Теперь давайте используем этот класс для добавления собственного `PrimaryAssetType`.
Делается это через `static const` переменные в теле класса. Так-же придется написать код, добавляющий эти переменные в `PrimaryAssetTypesToScan`.
```cpp
//.h
#include "CoreMinimal.h"
#include "Engine/AssetManager.h"
#include "Engine/AssetManagerSettings.h"
#include "DataModuleAssetManager.generated.h"
/**
 * Custom Asset Manager class
 */
UCLASS()
class DATAACCESS_API UDataModuleAssetManager : public UAssetManager
{
	GENERATED_BODY()
public:
	UDataModuleAssetManager() = default;
	virtual void PostInitProperties() override;
	// Return true if PrimaryAssetTypesToScan has PrimaryAssetType == Type
	virtual bool HasAssetTypeN(FName Type);
	// Inventory Item Groups
	// item group for abilities
	static const FPrimaryAssetType AbilityItemGroup;
	// item group for equippables
	static const FPrimaryAssetType ArmorItemGroup; // pauldrons, helmets..., will have stats for strength and others
	// item group for consumables
	static const FPrimaryAssetType ConsumableItemGroup; // potions, magic scrolls..., will be stackable
	// item group for weapons
	static const FPrimaryAssetType WeaponItemGroup; // swords, bows, shields ..., will have stats for damage
	// item group for buildable stuff
	static const FPrimaryAssetType BuildableItemGroup; // floor, walls..., will be stackable and can be build
	// item group for containers
	static const FPrimaryAssetType ContainerItemGroup; // containers, chests..., will have inventory size related parameters
	// item group for books
	static const FPrimaryAssetType TextItemGroup; // will have text field, usefull for books, scrolls, letters
	// Returns the current AssetManager with applied type
	static UDataModuleAssetManager& Get(); // *note override can't be applied to static functions
};
//...
//.cpp
// Since Static Const class members can't have "in class initialisation", we should initialize them in global scope
// it's ok, integrated Primary Asset Type - UAssetManager::MapType initialized same way
// this anyway will be avaivible in the constructor with it's value properly set
const FPrimaryAssetType UDataModuleAssetManager::AbilityItemGroup = FName(TEXT("AbilityItemGroup"));
const FPrimaryAssetType UDataModuleAssetManager::ArmorItemGroup = FName(TEXT("ArmorItemGroup"));
const FPrimaryAssetType UDataModuleAssetManager::ConsumableItemGroup = FName(TEXT("ConsumableItemGroup"));
const FPrimaryAssetType UDataModuleAssetManager::WeaponItemGroup = FName(TEXT("WeaponItemGroup"));
const FPrimaryAssetType UDataModuleAssetManager::BuildableItemGroup = FName(TEXT("BuildableItemGroup"));
const FPrimaryAssetType UDataModuleAssetManager::ContainerItemGroup = FName(TEXT("ContainerItemGroup"));
const FPrimaryAssetType UDataModuleAssetManager::TextItemGroup = FName(TEXT("TextItemGroup"));
void UDataModuleAssetManager::PostInitProperties()
{
	UAssetManager::PostInitProperties(); // call parent
	// Register our custom types in Asset Manager
	// GetMutableDefault can be used here instead
	UAssetManagerSettings* Settings = Cast<UAssetManagerSettings>(UAssetManagerSettings::StaticClass()->GetDefaultObject());
	/* my test code, leaving it for you as refrence
	SFName TypeName = TEXT("TestPrimaryType");
	for (FPrimaryAssetTypeInfo Info : Settings->PrimaryAssetTypesToScan) {
		if (Info.PrimaryAssetType == TypeName) {
			return; // don't add config if already exist
		}
	}
	Settings->PrimaryAssetTypesToScan.Add(FPrimaryAssetTypeInfo{TypeName ,
        AActor::StaticClass(),
        true,
        false,
        TArray<FDirectoryPath>( { FDirectoryPath{ TEXT("/Game/Characters") } } ),
        TArray<FSoftObjectPath>({})
    });*/
									
	if(!HasAssetTypeN(UDataModuleAssetManager::AbilityItemGroup))
		Settings->PrimaryAssetTypesToScan.Add(FPrimaryAssetTypeInfo{ UDataModuleAssetManager::AbilityItemGroup,
			UAbilityItemGroup::StaticClass(),
			false,
			false,
			TArray<FDirectoryPath>({}),
			TArray<FSoftObjectPath>({})
		});
	if(!HasAssetTypeN(UDataModuleAssetManager::ArmorItemGroup))
		Settings->PrimaryAssetTypesToScan.Add(FPrimaryAssetTypeInfo{ UDataModuleAssetManager::ArmorItemGroup,
			nullptr, // no type yet
			false,
			false,
			TArray<FDirectoryPath>({}),
			TArray<FSoftObjectPath>({})
		});
	if(!HasAssetTypeN(UDataModuleAssetManager::ConsumableItemGroup))
		Settings->PrimaryAssetTypesToScan.Add(FPrimaryAssetTypeInfo{ UDataModuleAssetManager::ConsumableItemGroup,
			nullptr, // no type yet
			false,
			false,
			TArray<FDirectoryPath>({}),
			TArray<FSoftObjectPath>({})
		});
	if (!HasAssetTypeN(UDataModuleAssetManager::WeaponItemGroup))
		Settings->PrimaryAssetTypesToScan.Add(FPrimaryAssetTypeInfo{ UDataModuleAssetManager::WeaponItemGroup,
			nullptr, // no type yet
			false,
			false,
			TArray<FDirectoryPath>({}),
			TArray<FSoftObjectPath>({})
		});
	if (!HasAssetTypeN(UDataModuleAssetManager::BuildableItemGroup))
		Settings->PrimaryAssetTypesToScan.Add(FPrimaryAssetTypeInfo{ UDataModuleAssetManager::BuildableItemGroup,
			nullptr, // no type yet
			false,
			false,
			TArray<FDirectoryPath>({}),
			TArray<FSoftObjectPath>({})
		});
	if (!HasAssetTypeN(UDataModuleAssetManager::ContainerItemGroup))
		Settings->PrimaryAssetTypesToScan.Add(FPrimaryAssetTypeInfo{ UDataModuleAssetManager::ContainerItemGroup,
			nullptr, // no type yet
			false,
			false,
			TArray<FDirectoryPath>({}),
			TArray<FSoftObjectPath>({})
		});
	if (!HasAssetTypeN(UDataModuleAssetManager::TextItemGroup))
		Settings->PrimaryAssetTypesToScan.Add(FPrimaryAssetTypeInfo{ UDataModuleAssetManager::TextItemGroup,
			nullptr, // no type yet
			false,
			false,
			TArray<FDirectoryPath>({}),
			TArray<FSoftObjectPath>({})
		});
	// We don't need to save. Setting will appear anyway.
	// Benefits from not saving - setting will be removed if asset manager class is removed
	// if you edit any added asettings edition results will be saved automatically
	//Settings->SaveConfig();
}
// Return true if PrimaryAssetTypesToScan has PrimaryAssetType == Type
bool UDataModuleAssetManager::HasAssetTypeN(FName Type)
{
	const UAssetManagerSettings& Settings = GetSettings();
	for (FPrimaryAssetTypeInfo Info : Settings.PrimaryAssetTypesToScan) {
		if (Info.PrimaryAssetType == Type) {
			return true;
		}
	}
	return false;
}
// Asset Manager singleton getter
UDataModuleAssetManager& UDataModuleAssetManager::Get()
{
	UDataModuleAssetManager* This = Cast<UDataModuleAssetManager>(GEngine->AssetManager);
	if (This)
	{
		return *This;
	}
	else
	{
		UE_LOG(LogTemp, Error, TEXT("Invalid AssetManager in DefaultEngine.ini, must be UDataModuleAssetManager!"));
		return *NewObject<UDataModuleAssetManager>();
	}
}
```
После компиляции кода необходимо переопределить встроенный менеджер ассетов перейдя по пути `Edit -> Project Settings -> General Settings -> Default Classes -> Asset Manager`.
![4a94357583ca6ee86510fe2bd57aeaf0.png](../images/4a94357583ca6ee86510fe2bd57aeaf0.png)
Однако, переопределения не достаточно, чтобы первичные типы ассетов были зарегистрированы. Закройте движок, удалите папку `Binaries` в проекте и пересоберите его целиком.
![b75e31239b5537eefcce55d882246468.png](../images/b75e31239b5537eefcce55d882246468.png)
После этого на странице `Edit -> Project Settings -> Asset Manager` вы увидите добавленные первичные типы ассетов.
![7c1c4df8b46df3adb4c8c72180910449.png](../images/7c1c4df8b46df3adb4c8c72180910449.png)

В коде я указал группы предметов. Поясню почему группы. Я до этого уже разрабатывал две системы инвентаря и скажу, что не достаточно просто делить предметы по типам. Нужны еще и группы, которые объединяют между собой предметы одного типа. Например зелья маны и куски сыра будут иметь схожий функционал взаимодействия, но разные типы предметов, куски сыра это еда, а зелья маны - эликсиры. Поэтому во многих играх помимо дифференциации предметов по типам используется еще и группировка.

#### Важное дополнение
Обратите внимание на сочетание имени ассета и его класса.
```cpp
// name
AbilityItemGroup = FName(TEXT("AbilityItemGroup"))
//...
if(!HasAssetTypeN(UDataModuleAssetManager::AbilityItemGroup))
        Settings->PrimaryAssetTypesToScan.Add(FPrimaryAssetTypeInfo{ UDataModuleAssetManager::AbilityItemGroup,
            UAbilityItemGroup::StaticClass(),
            false,
            false,
            TArray<FDirectoryPath>({}),
            TArray<FSoftObjectPath>({})
        });
```
Класс `UAbilityItemGroup` и имя `AbilityItemGroup`. 
Они должны "совпадать", разумеется, без наличия буквы `U`.

Если имя ассета будет отличаться от типа, который используется, то `AssetManager` не будет работать корректно.

> Вы не можете использовать любые имена для названий `Primary Asset Types`. Имя должно совпадать с классом, за вычетом буквы `U`.


![7c1c4df8b46df3adb4c8c72180910449.png](../images/2207441.png)

#### Использование менеджера ассетов через синглтон
Давайте используем менеджер ассетов из нашего компонента `UDataAccessComponent`. Для этого достаточно вывзвать синглтон:
```cpp
#include "Engine/AssetManager.h" // don't forget to include first
//...
UAssetManager &Manager = UAssetManager::Get();
```
Или, в случае с пользовательским классом менеджера ассетов:
```cpp
// module dependency: DataAccess
#include "DataModuleAssetManager.h" // don't forget to include first
//...
UDataModuleAssetManager &Manager = UDataModuleAssetManager::Get();
```
Далее мы можем, например, получить список ассетов из определенной категории. Поскольку у меня, в данный момент, не создано ни одного `PrimaryDataAsset'а` для групп предметов, я выведу просто список карт `UWorld` в папке `/Game/Maps`. Их у меня две.
![d933f5674598e2f077530467d536042b.png](../images/d933f5674598e2f077530467d536042b.png)
```cpp
#include "DataModuleAssetManager.h"
//...
// BeginPlay
// working with asset manager from c++
UDataModuleAssetManager& Manager = UDataModuleAssetManager::Get();
TArray<FName> Bundles{ "/Game/Maps/" };
TArray<FPrimaryAssetId> AssetIds;
Manager.GetPrimaryAssetIdList(UDataModuleAssetManager::MapType, AssetIds);
UE_LOG(LogTemp, Warning, TEXT("Array Size = %i"), AssetIds.Num());
for (FPrimaryAssetId AssetId : AssetIds) {
    TSharedPtr<FStreamableHandle> MapHandle = Manager.LoadPrimaryAsset(AssetId, Bundles);
    if (MapHandle->WaitUntilComplete() == EAsyncPackageState::Complete) {
        UWorld* MapAsset = Cast<UWorld>(MapHandle->GetLoadedAsset());
        UE_LOG(LogTemp, Warning, TEXT("Found Map Name = %s"), *MapAsset->GetFName().ToString());
    }
}
```
![d3412371c76cfa50ec93840259fe2e6f.png](../images/d3412371c76cfa50ec93840259fe2e6f.png)
Учтите, что указанный выше код не сработает, пока вы не перезапустите движок.
### Первичные и вторичные ассеты
Основной функционал системы `Asset Manager` строится вокруг двух категорий ассетов: перивичные `primary` и вторичные `secondary`.
Первичными являются те ассеты, которые разработчик добавил в список `Primary Asset Types to Scan` на странице настроек `Edit->Project Settings -> Asset Manager`.
![9187de690b713bf4a195ec0b80957b1f.png](../images/9187de690b713bf4a195ec0b80957b1f.png)
Ассеты, которые не добавлены в список перивичных - считаются вторичными.
Старая документация говорит, что первичным ассетом может быть лишь класс, в котором переопределен метод `GetPrimaryAssetId`. К сожалению, это не так, перечитайте главу "Проблемы менеджера ассетов", в которой я рассмотрел поведение менеджера ассетов по отношению к классам, у которых реализован `GetPrimaryAssetId`. Менеджер ассетов не замечает такие классы, пока вы не добавите их, собственными руками, в список `Primary Asset Types to Scan`.
Чтобы понять, почему так происходит, рассмотрим реализацию метода `GetPrimaryAssetId` у базового, для всех ассетов, класса `UObject`. Найти код можно в файле `\Runtime\CoreUObject\Private\UObject\Obj.cpp`.
![f61ffc965eff7b01f76e7c82a171ebe1.png](../images/f61ffc965eff7b01f76e7c82a171ebe1.png)
Обратите особое внимание на строчку `FCoreUObjectDelegates::GetPrimaryAssetIdForObject.IsBound()`.
Это делегат, к которому подключается `Asset Manager` в том случае, если ссылка на этот ассет указана в списке `Primary Asset Types To Scan`.
К делегату привязан метод `DeterminePrimaryAssetIdForObject`, который и определяет является ли ассет первичным или нет. Найти этот метод можно по пути `\Runtime\Engine\Private\AssetManager.cpp`.
![fe2379800ad47fc039daa29b99a4881c.png](../images/fe2379800ad47fc039daa29b99a4881c.png)
![24278630ae2576249c903fc239c1166b.png](../images/24278630ae2576249c903fc239c1166b.png)
В этом методе путь до объекта сравнивается со списком первичных ассетов из настроек системы. Если путь не содержится в списке - объект не является первичным.
Таким образом: метод `GetPrimaryAssetId`, встроенный в любой `UObject` умный и сам понимает, отмечен класс в качестве первичного или нет.
Исключением из этого правила является единственный класс - `UPrimaryDataAsset`, каждый файл, унаследованный от этого класса, может быть использован лишь единственным способом - как отдельная категория в списке `Primary Asset Types To Scan`.
### Primary Data Asset
Данный класс представляет из себя `DataAsset`, у которого переопределен метод `GetPrimaryAssetId`. Реализация этого метода внутри класса `UPrimaryDataAsset` использует имя файла в качестве типа ассета для `AssetManager` (`PrimaryAssetType`).
![1d039a629f35ff80e90a7de8ecdf41c8.png](../images/1d039a629f35ff80e90a7de8ecdf41c8.png)
Для `DataAsset'ов`, созданных на основе экземпляра `PrimaryDataAsset` категория `PrimaryAssetType` равна названию файла исходного экземпляра  `PrimaryDataAsset`.
Сам класс `UPrimaryDataAsset` является `abstract`, что не позволяет разработчикам создавать ассеты, унаследованные от этого класса, напрямую.
![cb53302b8212621458c672c13c5344ee.png](../images/cb53302b8212621458c672c13c5344ee.png)
Найти исходники класса можно по пути `Runtime\Engine\Classes\Engine\DataAsset.h`.
В комментарии к исходникам сказано указано следующее:
```cpp
/**
 * PrimaryDataAsset это DataAsset, который реализует метод GetPrimaryAssetId
 * A DataAsset that implements GetPrimaryAssetId and has asset bundle support, which allows it to be manually loaded/unloaded from the AssetManager.
 * Instances of native subclasses can be created directly as Data Assets and will use the name of the native class as the PrimaryAssetType.
 * Or, blueprint subclasses can be created with a PrimaryAssetType equal to the name of the first native class going up the hierarchy, or the highest level blueprint class.
 * IE, if you have UPrimaryDataAsset -> UParentNativeClass -> UChildNativeClass -> DataOnlyBlueprintClass the type will be UChildNativeClass.
 * Whereas if you have UPrimaryDataAsset -> ParentBlueprintClass -> DataOnlyBlueprintClass the type will be ParentBlueprintClass.
 * To change this behavior, override GetPrimaryAssetId in your native class or copy those functions into a different native base class.
 */
```
Таким образом, мы можем задавать каждый файл, унаследованный от `Primary Data Asset` лишь в качестве отдельной категории в `Primary Asset Types To Scan`.
Поскольку `UPrimaryDataAsset` поддерживает систему `AssetManager` мы можем создать на основе связки из этих двух систем игровой инвентарь. Точнее - заготовку игрового инвентаря, поскольку интерфейс и прочие системы писать в этой статье я не буду. Инвентарь, качественный, многослотовый, это предмет не малой статьи но целой серии статей. Тут будет лишь фундамент, структура данных.
Для начала нам нужен базовый класс элемента инвентаря. Я не собираюсь использовать отдельно `.cpp` файлы, достаточно будет заголовочных. Открываем папку модуля `DataAccess/Public`, создаем подпапку `Inventory`.
![d20f2649943d2dfb462e11ca2e1bf19d.png](../images/d20f2649943d2dfb462e11ca2e1bf19d.png)
Внутри нам нужен файл `ItemBase.h`, который будет родительским для остальных категорий предметов.
![27794c8324edc1939e6b57169d3edd54.png](../images/27794c8324edc1939e6b57169d3edd54.png)
Так-же добавим файлы:
* `AbilityItemGroup.h`
* `ArmorItemGroup.h`
* `ConsumableItemGroup.h`
* `WeaponItemGroup.h`
* `BuildableItemGroup.h`
* `ContainerItemGroup.h`
* `TextItemGroup.h`
По файлу под каждую из категорий предметов.
![3c90308a9266943aac2c6769778e9ddb.png](../images/3c90308a9266943aac2c6769778e9ddb.png)
Так-же, советую сделать отдельный файл под перечисления.
В папке модуля `DataAccess/Public` создаем подпапку `Enums`, а в ней файл - `Enums.h`.
![55416319919a3c00a397864ed6385b01.png](../images/55416319919a3c00a397864ed6385b01.png)
Теперь надо чтобы все файлы появились в `Solution Explorer`. Идем в Unreal Engine далее `Tools -> Refresh Visual Studio 2022 Project`.
![137ecf41b0def23ecdbe93f087197723.png](../images/137ecf41b0def23ecdbe93f087197723.png)
Теперь давайте создадим перечисление для типов предметов.
```cpp
#pragma once
#include "CoreMinimal.h"
// somehow my VS dosen't detect .generated.h for nested files so i have to inclde this manually to get rid of red errors
#include <UObject/ObjectMacros.h>
// UHT
#include "Enums.generated.h"
// Inventory Item Type Enum
UENUM(BlueprintType)
enum class EInventoryItemsEnum : uint8
{
	NONE UMETA(Hidden, DiplaywName = "---"),
	Undefined,
	Helmet,
	Gloves,
	UpperBody,
	LowerBody,
	Boots,
	Cloak,
	ShortSword,
	LongSword,
	Bow,
	Shield,
	Mace,
	Axe,
	Arrow,
	Potion,
	Scroll,
	Book,
	Wall,
	Chest,
	// and much more, once you need them
};
```
Далее нам нужно реализовать базовый класс для каждого из предметов. Базовый класс, на начальном этапе, будет содержать всего два поля: тип предмета и его название. Для названия надо использовать параметр `Text`, потому что оно, очевидно, будет локализуемым.
```cpp
#pragma once
#include "CoreMinimal.h"
#include "Engine/DataAsset.h"
#include "NativeGameplayTags.h" // gameplay tags
#include "InputAction.h" // enhanced input
#include "InputMappingContext.h"
#include "Enums/Enums.h" // ItemTypes
// generated.h
#include "ItemBase.generated.h"
// Packages used:
// "GameplayTags", "EnhancedInput", "TestModule"
// for FText
#define LOCTEXT_NAMESPACE "ItemTextNamespace"
/**
 * ItemBase PrimaryDataAsset
 */
UCLASS()
class DATAACCESS_API UItemBase : public UPrimaryDataAsset
{
	GENERATED_BODY()
public:
	UPROPERTY(BlueprintReadOnly, EditDefaultsOnly, Category = "Item")
	FText ItemName = LOCTEXT(LOCTEXT_NAMESPACE, "Basic Item");
	UPROPERTY(BlueprintReadOnly, EditDefaultsOnly, Category = "Item")
	EInventoryItemsEnum ItemType = EInventoryItemsEnum::NONE;
};
#undef LOCTEXT_NAMESPACE
```
От этого класса унаследуем реализацию для класса `AbilityItemGroup`.
```cpp
#pragma once
#include "CoreMinimal.h"
#include "Engine/DataAsset.h"
#include "NativeGameplayTags.h" // gameplay tags
#include "InputAction.h" // enhanced input
#include "InputMappingContext.h"
#include "ItemBase.h" // base item
#include "Enums/Enums.h" // ItemTypes
// generated.h
#include "AbilityItemGroup.generated.h"
// for FText
#define LOCTEXT_NAMESPACE "ItemTextNamespace"
/**
 * ItemBase PrimaryDataAsset
 */
UCLASS()
class DATAACCESS_API UAbilityItemGroup : public UItemBase
{
	GENERATED_BODY()
public:
	// Icon
	UPROPERTY(BlueprintReadOnly, EditDefaultsOnly, Category = "Ability")
	TObjectPtr<UTexture2D> AbilityIcon;
	UPROPERTY(BlueprintReadOnly, EditDefaultsOnly, Category = "Ability")
	FText AbilityDescription;
	UPROPERTY(BlueprintReadOnly, EditDefaultsOnly, Category = "Ability")
	float Cooldown = 0.5f;
	// Ability Activation key
	UPROPERTY(BlueprintReadWrite, EditAnywhere, Category = "Input")
	TSoftObjectPtr<UInputAction> InputAction = nullptr;
};
#undef LOCTEXT_NAMESPACE
```
Теперь надо указать этот класс в качестве первичного ассета. Идем в файл с реализацией собственного менеджера ассетов и добавляем ссылку на класс в `PrimaryAssetTypesToScan`.
```cpp
//.h
// add to includes
#include "Inventory/ItemBase.h" // base item class
#include "Inventory/AbilityItemGroup.h"
//...
if(!HasAssetTypeN(UDataModuleAssetManager::AbilityItemGroup))
		Settings->PrimaryAssetTypesToScan.Add(FPrimaryAssetTypeInfo{ UDataModuleAssetManager::AbilityItemGroup,
			UAbilityItemGroup::StaticClass(),
			true,
			false,
			TArray<FDirectoryPath>({}),
			TArray<FSoftObjectPath>({})
		});
```
![94591dd9e4f23999d3772cab6f051bd3.png](../images/94591dd9e4f23999d3772cab6f051bd3.png)
Аналогичные действия можно проделать для каждой группы предметов. Чтобы не растягивать эту статью, я не буду описывать такие шаги.
Компилируем код и перезапускаем движок.
![86a6d288804718801807904f1ca03e91.png](../images/86a6d288804718801807904f1ca03e91.png)
Теперь можно создать ассет данных для типа `UAbilityItemGroup`.
Идем в `Unreal Engine -> Content Browser -> Add -> Miscellaneous -> Data Asset`.
![1c0df27d7b36f7991724fe7cd67efb46.png](../images/1c0df27d7b36f7991724fe7cd67efb46.png)
Выбираем `AbilityItemGroup`.
![042eb48d7d57ca4d35067cf52d27653a.png](../images/042eb48d7d57ca4d35067cf52d27653a.png)
В результате получится новый первичный ассет данных.
![579a6931be0050f08ec571ad820d30e3.png](../images/579a6931be0050f08ec571ad820d30e3.png)
![3f148d1438985e4e4c5234384fd3f3f5.png](../images/3f148d1438985e4e4c5234384fd3f3f5.png)
Я добавил этот ассет в отдельную папку `Characters / Data / Items / Abilities`. В этой папки будут создаваться все `Data Asset'ы` способностей.
![73456b3e86d212255dfc63ffcd9ba9b3.png](../images/73456b3e86d212255dfc63ffcd9ba9b3.png)
Осталось сделать так, чтобы `AssetManager` получил доступ к ассетам способностей. Идем в `Edit -> Project Settings -> Game -> Asset Manager` и ищем категорию `AbilityItemGroup`.
Нас интересует параметр `Directories`. Добавляем туда директорию для наших способностей.
![ef18adb66ac6a2e1cef6a8a24da38a13.png](../images/ef18adb66ac6a2e1cef6a8a24da38a13.png)
Теперь ассеты, отвечающие за способности, должны быть доступны для `Asset Manager'а`. Добавьте `blueprint` ноду `AsyncLoadPrimaryAsset`, чтобы посмотреть так ли это.
![5df8439ff22dc1f92b332b82ef3f37f4.png](../images/5df8439ff22dc1f92b332b82ef3f37f4.png)
![cb3194b73ee2ea366ed8adc4f1d63893.png](../images/cb3194b73ee2ea366ed8adc4f1d63893.png)
![655383fbb1efe7c9e85a691f255e66b2.png](../images/655383fbb1efe7c9e85a691f255e66b2.png)
### Реализация поиска предмета по названию файла
Остался последний штрих. Функция для нахождения `item'а` по его `fname`. Она позволит работать с файлами предметов так, как будто они занесены в базу данных.
Добавим библиотеку функций под эти цели `Tools -> New C++ Class -> BlueprintFunctionLibrary`, называем `DataAccessLibrary`.
![f1a4ac6fb3111f9883e7a23368afc7eb.png](../images/f1a4ac6fb3111f9883e7a23368afc7eb.png)
После добавления библиотеки не забудьте выполнить команду `Tools -> Refresh Visual Studio 2022 Project`.
Далее пишем такой код:
```cpp
#pragma once
#include "CoreMinimal.h"
#include "Kismet/BlueprintFunctionLibrary.h"
#include "DataModuleAssetManager.h"
#include "Inventory/AbilityItemGroup.h"
#include "Engine/DataAsset.h"
#include "Enums/Enums.h" // ItemTypes
// .generated.h
#include "DataAccessLibrary.generated.h"
/**
 * Data Access Function Library
 */
UCLASS()
class DATAACCESS_API UDataAccessCLibrary : public UBlueprintFunctionLibrary
{
	GENERATED_BODY()
public:
	// find item by PrimaryAssetName
	UFUNCTION(BlueprintCallable, BlueprintPure = true, Category = "Inventory")
	static UItemBase* FindItemByName(FPrimaryAssetType AssetGroup, FName Name) {
		if( Name.IsNone() ) return nullptr;
		UDataModuleAssetManager& Manager = UDataModuleAssetManager::Get();
		if( !Manager.IsValid() ) return nullptr;
		TArray<FPrimaryAssetId> AssetIds; // asset list container
		Manager.GetPrimaryAssetIdList(AssetGroup, AssetIds); // get list of assets
		for (FPrimaryAssetId ItemID : AssetIds) {
			if (ItemID.PrimaryAssetName == Name) {
				// will async load asset by id
				Manager.LoadPrimaryAsset(ItemID,{})->WaitUntilComplete(); // this is sync function
				UObject* Obj = Manager.GetPrimaryAssetObject(ItemID); // this will return only objects loaded in memory
				if (Obj == nullptr) {
					UE_LOG(LogTemp, Warning, TEXT("FindInventoryItemByFName Asset not found"))
					return nullptr;
				}
				UItemBase* ItemData = Cast<UItemBase>(Obj);
				return ItemData;
			}
		}
		UE_LOG(LogTemp, Warning, TEXT("FindInventoryItemByFName Find Failed!"))
		return  nullptr;
	}
};
```
Этот код позволит использовать функцию `FindItemByName`, которая ищет `PrimaryAsset` из выбранной категории по его параметру `PrimaryAssetName`, который, я напомню, у `PrimaryDataAsset'ов` совпадает с именем файла, то есть с `FName`.
Откроем один из тестовых файлов, например `PDA_FrostNova`.
![b3115dc61978f77b629c16d0be61801f.png](../images/b3115dc61978f77b629c16d0be61801f.png)
Изменим его параметр `Item Type` с `NONE` на `Potion`, для теста. Название  тоже изменим на `Frost Nova Item`.
![da2475111782562084a69b7d1c66282f.png](../images/da2475111782562084a69b7d1c66282f.png)
Используем нашу функцию для получения содержимого этих полей в `blueprnt` коде.
![bf89f492fd20b26350d8a48cd1ab0c01.png](../images/bf89f492fd20b26350d8a48cd1ab0c01.png)
![f399e5b4a4d8eddf9c8b04611412b011.png](../images/f399e5b4a4d8eddf9c8b04611412b011.png)
Можно улучшить нашу функцию, добавив в нее возможность проверки на успешнось поиска.
```cpp
// Enums/Enums.h
// Function Exit Pins Enum
UENUM(BlueprintType)
enum class EFindItemByNameOutputs : uint8
{
	Then	UMETA(DisplayName = "Then"),
	Fail	UMETA(DisplayName = "On Failure")
};
//...
//DataAccessLibrary.h
// find item by PrimaryAssetName
UFUNCTION(BlueprintCallable, /*BlueprintPure = "true",*/ Category = "Inventory", meta=(ExpandEnumAsExecs = "Outputs"))
static void FindItemByName(FPrimaryAssetType AssetGroup, FName Name, EFindItemByNameOutputs& Outputs, UItemBase*& ItemData) {
    if( Name.IsNone() ){
        Outputs = EFindItemByNameOutputs::Fail;
        return;
    }
    UDataModuleAssetManager& Manager = UDataModuleAssetManager::Get();
    if( !Manager.IsValid() ) {
        Outputs = EFindItemByNameOutputs::Fail;
        return;
    }
    TArray<FPrimaryAssetId> AssetIds; // asset list container
    Manager.GetPrimaryAssetIdList(AssetGroup, AssetIds); // get list of assets
    for (FPrimaryAssetId ItemID : AssetIds) {
        if (ItemID.PrimaryAssetName == Name) {
            // will async load asset by id
            Manager.LoadPrimaryAsset(ItemID,{})->WaitUntilComplete(); // this is sync function
            UObject* Obj = Manager.GetPrimaryAssetObject(ItemID); // this will return only objects loaded in memory
            if (Obj == nullptr) {
                UE_LOG(LogTemp, Warning, TEXT("FindInventoryItemByFName Asset not found"))
                Outputs = EFindItemByNameOutputs::Fail;
                return;
            }
            ItemData = Cast<UItemBase>(Obj);
            Outputs = EFindItemByNameOutputs::Then;
            return;
        }
    }
    UE_LOG(LogTemp, Warning, TEXT("FindInventoryItemByFName Find Failed!"))
    Outputs = EFindItemByNameOutputs::Fail;
    return;
}
```
При такой реализации функция получает два выходных пина, которыми очень удобно пользоваться.
![0a97b9007564506369ad58cba0c23bfd.png](../images/0a97b9007564506369ad58cba0c23bfd.png)
![0ce3d853e98b91fa94882772f4e3baf4.png](../images/0ce3d853e98b91fa94882772f4e3baf4.png)
Моя реализация загрузки ассета - синхронна, я использую `WaitUntilComplete` чтобы блокировать поток до завершения операции. Это не всегда верное решение, но оно работает. Можно, конечно, написать асинхронное дейсвтие `AsyncAction` чтобы решить эту проблему, но есть куда более простой способ.
Дело в том, что в движке уже есть встроенное асинхронно действие `AsyncLoadPrimaryAsset`, которое как раз и загружает ассет. Но данное действие принимает в качестве входа параметр типа `FPrimaryAssetId`. Мы можем взять приведенную выше функцию и упростить ее, чтобы она возвращала этот самый `FPrimaryAssetId`, а потом скормить ее в асинхронный загрузчик.
```cpp
// Enums/Enums.h
// Function Exit Pins Enum
UENUM(BlueprintType)
enum class EFindItemByNameOutputs : uint8
{
	Then	UMETA(DisplayName = "Then"),
	Fail	UMETA(DisplayName = "On Failure")
};
//...
//DataAccessLibrary.h
// Find primary asset type of item by name
UFUNCTION(BlueprintCallable, Category = "Inventory", meta = (ExpandEnumAsExecs = "Outputs"))
static void FindItemAssetId(FPrimaryAssetType AssetGroup, FName Name, EFindItemByNameOutputs& Outputs, FPrimaryAssetId& ItemData) {
    if (Name.IsNone()) {
        Outputs = EFindItemByNameOutputs::Fail;
        return;
    }
    UDataModuleAssetManager& Manager = UDataModuleAssetManager::Get();
    if (!Manager.IsValid()) {
        Outputs = EFindItemByNameOutputs::Fail;
        return;
    }
    TArray<FPrimaryAssetId> AssetIds; // asset list container
    Manager.GetPrimaryAssetIdList(AssetGroup, AssetIds); // get list of assets
    for (FPrimaryAssetId ItemID : AssetIds) {
        if (ItemID.PrimaryAssetName == Name) {
            ItemData = ItemID;
            Outputs = EFindItemByNameOutputs::Then;
            return;
        }
    }
    UE_LOG(LogTemp, Warning, TEXT("Find Failed!"))
    Outputs = EFindItemByNameOutputs::Fail;
    return;
}
```
![7964dbae709947ec9fc3bc5ee827445a.png](../images/7964dbae709947ec9fc3bc5ee827445a.png)
![f8153bea4e78240469f8829fd1963a8e.png](../images/f8153bea4e78240469f8829fd1963a8e.png)
Разумеется. для реализации полноценного инвентаря этих двух функций будет не достаточно. Но они - самые важные. Возможность быстро найти предмет по его названию крайне важна для получения информации об этом предмете.
### Асинхронная загрузка ассетов
Делегат, передаваемый методу `LoadPrimaryAsset` можно использовать для асинхронной загрузки ассетов, если это требуется.
```cpp
// This function will run async asset loading test
UFUNCTION(BlueprintCallable, Category = "Inventory")
// will async load all assets in AssetGroup
static void AsyncAssetLoadTest() {
    UDataModuleAssetManager& Manager = UDataModuleAssetManager::Get();
    TArray<FPrimaryAssetId> AssetIds; // asset list container
    // async load all assets in AbilityItemGroup
    Manager.GetPrimaryAssetIdList(UDataModuleAssetManager::AbilityItemGroup, AssetIds);
    for (FPrimaryAssetId ItemID : AssetIds) {
        // this delegate will be called after Item is loaded
        FStreamableDelegate Delegate;
        Delegate.BindLambda([&](FPrimaryAssetId ID, UDataModuleAssetManager *Manager)->void {
            UObject* Obj = Manager->GetPrimaryAssetObject(ID); // this will return only objects loaded in memory
            if (Obj == nullptr) {
                UE_LOG(LogTemp, Warning, TEXT("Asset not found"))
                return;
            }
            UItemBase* ItemData = Cast<UItemBase>(Obj);
            // item is loaded
            //... do something
            UE_LOG(LogTemp, Warning, TEXT("loaded item name = %s"), *ItemData->ItemName.ToString())
        }, ItemID, &Manager);
        // start async item loading
        //Manager.LoadPrimaryAsset(ItemID, {}, Delegate);
        TSharedPtr<FStreamableHandle> DelegateHandle = Manager.LoadPrimaryAsset(ItemID, {}, Delegate);
        DelegateHandle->WaitUntilComplete(); // lock thread and wait for the calculations
    }
}
```
Этот код  асинхронно, в цикле, загрузит в память все ассеты, с `AssetIds`.
![c6d071bfa2fd4e8e28faeeddc49e78cf.png](../images/c6d071bfa2fd4e8e28faeeddc49e78cf.png)
![8f0374a5779177dd7e4338d13020be9b.png](../images/8f0374a5779177dd7e4338d13020be9b.png)
## Links
https://unrealcommunity.wiki/using-the-asset-manager-qj38astq
https://www.reddit.com/r/unrealengine/comments/p7ak9z/get_all_data_assets_from_a_specified_primary_data/
https://www.tomlooman.com/unreal-engine-asset-manager-async-loading/
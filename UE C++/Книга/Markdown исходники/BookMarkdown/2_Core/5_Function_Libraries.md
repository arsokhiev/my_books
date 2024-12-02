# Библиотеки Функций для `Blueprint`
Самый простой способ сделать с++ функцию доступной в `Blueprint` коде без каких-либо условий - это библиотеки функций.
Для использования функций из таких библиотек не требуется создание отдельных объектов. Эти функции доступны безусловно, потому что представляют собой `static` методы классов.
Написанный мной ранее метод сортировки `Actor'ов`, приведенный в статье про делегаты, зависит от компонента - `UDelegatesTest` и не будет работать без него.
![714ff11bcdce80b201eaea268de124d3.png](../images/714ff11bcdce80b201eaea268de124d3.png)
Мне бы хотелось, чтобы этот метод можно было использовать без добавления компонентов. В этой статье мы вынесем его в отдельную библиотеку функций.
Библиотеки функций представлены как классы `UCLASS`, унаследованные от `UBlueprintFunctionLibrary`.
Самая популярная библиотека функций, из встроенных в Unreal Engine, это `Kismet/KismetSystemLibrary.h`, которая содержит в себе почти все дополнительные `blueprint` функции движка от `Delay` до `LineTrace`.
![17906ab80f35055d2abc9a095c75e3ae.png](../images/17906ab80f35055d2abc9a095c75e3ae.png)
Найти эту библиотеку, чтобы изучить подходы к созданию своих функций, можно по пути: `\Runtime\Engine\Classes\Kismet\KismetSystemLibrary.h`.
Вынос высоконагруженной логики из `blueprint` кода в с++ библиотеки функций - самый очевидный из способов оптимизации игры. Особенно, если вы прототипировали игру на `blueprint`.
## Подготовка к работе
Давайте  создадим отдельный модуль, в котором будут размещены наши библиотеки. Как и раньше, используем для этих целей бесплатный плагин `c++ Module Generator`.
![344a042f71af4622833531d7c5d1f642.png](../images/344a042f71af4622833531d7c5d1f642.png)
Модуль я назвал `FunctionLibraries`. В этом модуле планируется создавать все библиотеки функций, которые будут в проекте.
![4be54d5ac54046d509545dc517dca0e0.png](../images/4be54d5ac54046d509545dc517dca0e0.png)
После создания модуля - необходимо перезапустить движок, а так-же выполнить команду `Tools -> Refresh Visual Studio 2022 Project`.
![1ff80f25ac26cf2846f062980ae83df9.png](../images/1ff80f25ac26cf2846f062980ae83df9.png)
После чего файлы модуля появятся в Visual Studio `SolutionExplorer`.
![8786132771697c93fff6ebb778339c20.png](../images/8786132771697c93fff6ebb778339c20.png)
## Создание библиотеки функций
Движок Unreal Engine сам способен генерировать заготовку для библиотеки функций. Давайте воспользуемся этой возможностью.
Идем `Tools -> New C++ Class`.
![b4fe369458017c42309ebadf3212b930.png](../images/b4fe369458017c42309ebadf3212b930.png)
В появившемся окне переключаем вкладку на `All Classes` и ищем `BlueprintFunctionLibrary`.
![8f36630dc5544bdd80a9085fc42289f0.png](../images/8f36630dc5544bdd80a9085fc42289f0.png)
Я решил назвать библиотеку функций - `SortLibrary`. Давайте поместим в нее метод сортировки `Actor'ов`, написанный в статье про делегаты.
![e965a7e5b8de932e22afe455cdc22c5c.png](../images/e965a7e5b8de932e22afe455cdc22c5c.png)
После создания файла не забудьте выполнить команду - `Tools -> Refresh Visual Studio 2022 Project`, чтобы сгенерированный шаблон появился в `Solution Explorer`.
![ee9870e64ac52f4a4bc71728b67ea521.png](../images/ee9870e64ac52f4a4bc71728b67ea521.png)
![d83e78b45d126f038fff90e97a1da2f4.png](../images/d83e78b45d126f038fff90e97a1da2f4.png)
## Добавление функций в библиотеку
Любая функция с модификатором `static` и макросом `UFUNCTION()`, добавленная в класс библиотеки функций, будет доступна на глобальном уровне для `blueprint`.
Давайте напишем тестовую функцию, для начала. Например, для печати числа в лог.
```cpp
// Function that prints number
UFUNCTION(BlueprintCallable, Category = "USortLibrary|Print", meta = (WorldContext = "WorldContextObject"))
static void LogNumber(const UObject* WorldContextObject, float FloatNum = 0.0f) {
    UE_LOG(LogTemp, Warning, TEXT("[log number] Number = %f"), FloatNum);
}
```
Так-же эта функция имеет скрытый пин `WorldContextObject`, который бывает нужен в некоторых случаях.
![9fa0f3892144f371fdd9da9ede94284e.png](../images/9fa0f3892144f371fdd9da9ede94284e.png)
Если скомпилировать библиотеку с этим кодом, то в `blueprin'ах`, например в `BP_ThirdPersonCharacter` появится возможность добавить ноду `LogNumber`.
![1b9ef9abbd942a559889c0f8ed5f67e1.png](../images/1b9ef9abbd942a559889c0f8ed5f67e1.png)
Если добавить ноду функции к событию `BeginPlay`, то после запуска проекта - можно будет увидеть соответствующее сообщение в логе.
![cc052bffb54f22892dbf4b2d08e225a3.png](../images/cc052bffb54f22892dbf4b2d08e225a3.png)
Давайте проверим как в библиотеке функций будет чувствовать себя функция с несколькими пинами, а так-же `blueprint pure` функция.
```cpp
// global scope
// Function Exit Pins Enum
UENUM(BlueprintType)
enum class ESL_MPOutputs : uint8
{
	Then	UMETA(DisplayName = "Then"),
	Warning	UMETA(DisplayName = "On Warning"),
	Error	UMETA(DisplayName = "On Error")
};
// Function Input Pins Enum
UENUM(BlueprintType)
enum class ESL_MPInputs : uint8
{
	Execute	UMETA(DisplayName = "Execute"),
	Yellow,
	Red
};
// UBlueprintFunctionLibrary
// Expand Enum As Execs
UFUNCTION(BlueprintCallable, Category = "SortLibrary|Print", Meta = (ExpandEnumAsExecs = "Inputs, Outputs"))
static void MultiplePinsLogNumber(int Number, ESL_MPInputs Inputs, ESL_MPOutputs& Outputs) {
    if (Inputs == ESL_MPInputs::Execute)
    {
        UE_LOG(LogTemp, Display, TEXT("[log number] Number = %i"), Number);
        Outputs = ESL_MPOutputs::Then;
    }
    else if (Inputs == ESL_MPInputs::Yellow)
    {
        UE_LOG(LogTemp, Warning, TEXT("[log number] Number = %i"), Number);
        Outputs = ESL_MPOutputs::Warning;
    }
    else
    {
        UE_LOG(LogTemp, Error, TEXT("[log number] Number = %i"), Number);
        Outputs = ESL_MPOutputs::Error;
    }
}
```
Данный код позволит создать функцию с несколькими входными и выходными пинами.
![64615f8cbb5b2bbb0af5a90759a10423.png](../images/64615f8cbb5b2bbb0af5a90759a10423.png)
![fb8a15003ef9c42d491ab6a61a346ee3.png](../images/fb8a15003ef9c42d491ab6a61a346ee3.png)
Теперь проверим `Pure` функции.
```cpp
// Pure Function that allows to lerp color
UFUNCTION(BlueprintPure, Category = "Custom SortLibrary|Pure", meta = (InColor1 = "(R=1,G=1,B=1,A=1)", InColor2 = "(R=0,G=0,B=0,A=1)"))
static FLinearColor LerpUsingHSV(FLinearColor InColor1, FLinearColor InColor2, float LerpAmount = 0.3f) {
    return FLinearColor::LerpUsingHSV(InColor1, InColor2, LerpAmount);
};
// Pure function with CompactNodeTitle
UFUNCTION(BlueprintPure, Category = "Custom SortLibrary|Pure", meta = (CompactNodeTitle = "+1"))
static int APlusOne(int a) {
    return a + 1;
};
```
![6b19b454e6c4e88dd8a04ba5f125c4b7.png](../images/6b19b454e6c4e88dd8a04ba5f125c4b7.png)
![ae010c07d9a07d4acae87108ce8a0ecb.png](../images/ae010c07d9a07d4acae87108ce8a0ecb.png)
Теперь, когда мы убедились, что все работает - давайте перенесем функции сортировки массива `AActor'ов` по дистанции до них.
Создаем два делегата в начале файла.
```cpp
// Delegate for SortActors()
DECLARE_DYNAMIC_DELEGATE_ThreeParams(FDActorsComparator, TSoftObjectPtr<AActor>, FirstActor, TSoftObjectPtr<AActor>, SecondActor, bool&, Result);
// Delegate for SortActorsRaw()
DECLARE_DYNAMIC_DELEGATE_ThreeParams(FDActorsRawComparator, AActor*, FirstActor, AActor*, SecondActor, bool&, Result);
```
Через них ведется сравнение  двух `AActor'ов`.
Далее переносим в код класса функции сортировки.
```cpp
//.h
// UBlueprintFunctionLibrary
// Actors Sorting
// Array of Actors sorting by ref
UFUNCTION(BlueprintCallable, Category = "SortLibrary|Sort", meta = (WorldContext = "WorldContextObject"))
static void SortActors(UPARAM(ref) TArray<TSoftObjectPtr<AActor>>& ActorsList, FDActorsComparator ActorsComparator, const UObject* WorldContextObject);
// Array of Actors sorting by raw ref
UFUNCTION(BlueprintCallable, Category = "SortLibrary|Sort", meta = (WorldContext = "WorldContextObject"))
static void SortActorsRaw(UPARAM(ref) TArray<AActor*>& ActorsList, FDActorsRawComparator ActorsComparator, const UObject* WorldContextObject);
// Resolving Soft PointersArray
UFUNCTION(BlueprintPure, Category = "SortLibrary|Convert")
static TArray<AActor*> ResolveArray(TArray<TSoftObjectPtr<AActor>> Actors);
```
![126fdbe59d6e4018b7db904695d33336.png](../images/126fdbe59d6e4018b7db904695d33336.png)
Реализация функций:
```cpp
//.cpp
void USortLibrary::SortActors(UPARAM(ref)TArray<TSoftObjectPtr<AActor>>& ActorsList, FDActorsComparator ActorsComparator, const UObject* WorldContextObject)
{
	// Call Array.Sort with lambda comparator in which call comparator delegate
	ActorsList.Sort([&](TSoftObjectPtr<AActor> A, TSoftObjectPtr<AActor> B) -> bool {
		bool Result = false;
		ActorsComparator.ExecuteIfBound(A, B, Result);
		return Result;
	});
}
void USortLibrary::SortActorsRaw(UPARAM(ref)TArray<AActor*>& ActorsList, FDActorsRawComparator ActorsComparator, const UObject* WorldContextObject)
{
	// Call Array.Sort with lambda comparator in which call comparator delegate
	ActorsList.Sort([&](AActor& A, AActor& B) -> bool {
		bool Result = false;
		ActorsComparator.ExecuteIfBound(&A, &B, Result);
		return Result;
	});
}
TArray<AActor*> USortLibrary::ResolveArray(TArray<TSoftObjectPtr<AActor>> Actors)
{
	TArray<TObjectPtr<AActor>> Result;
	for (TSoftObjectPtr Item : Actors) {
		Result.Add(Item.LoadSynchronous());
	}
	return Result;
}
```
![d5dbd8d788cd6bdded8a562b05c8069d.png](../images/d5dbd8d788cd6bdded8a562b05c8069d.png)
После компиляции станут доступны три функции - `SortActors`, для сортировки массива `SoftObject` ссылок, `SortActorsRaw` - для сортировки массива прямых ссылок и `ResolveArray` - для конверсии массива `TSoftObjectPtr<AActor>` в массив `AActor*`.
В результате чего мы сможем сортировать `Actor'ы` без использования компонента `DelegatesTest`.
![d275037ec40a01519dfd8027c76277c0.png](../images/d275037ec40a01519dfd8027c76277c0.png)
![3406483db24f14947aedb390adbab3f0.png](../images/3406483db24f14947aedb390adbab3f0.png)
Я подробно разбирал реализацию сортировки через делегаты в, собственно, статье про делегаты. Если вы не помните - можете почитать.
![e31c22aa756fe19b9e55791c63304615.png](../images/e31c22aa756fe19b9e55791c63304615.png)
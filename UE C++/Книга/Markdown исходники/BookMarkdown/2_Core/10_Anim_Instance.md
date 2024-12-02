# `Anim Instance`
## Вступление
За работу с анимациями персонажа отвечает класс `AnimInstance`. Именно от `AnimInstance` наследуются классы `AnimtaionBlueprint`. В шаблоне `ThirdPerson` уже есть два ассета данного типа. Это `ABP_Manny` и `ABP_Quinn`.
![3a6e514dd8247f22bf8789a6fd933741.png](../images/3a6e514dd8247f22bf8789a6fd933741.png)
Нас интересует `ABP_Manny`.
![803186398435ee2c3ef63e0c4500b3be.png](../images/803186398435ee2c3ef63e0c4500b3be.png)
## Зачем создавать `AnimInstance` на с++.
Низкая производительность - это основная проблема `AnimBlueprint`. Сильнее всего проблемы с производительностью сказываются в случаях, когда надо отрисовать на сцене несколько (дестяков) персонажей.
Давайте рассмотрим подобный сценарий.
Я сделал тестовую карту, на которую добавил всего `60` тестовых персонажей.
![2cd82b275b10232cdfa1d4ab577caaa9.png](../images/2cd82b275b10232cdfa1d4ab577caaa9.png)
Тестовые персонажи имеют только меш и установленный `Animation Blueprint`.
![74ea7521e948a7e2ec4861316622b168.png](../images/74ea7521e948a7e2ec4861316622b168.png)
Даже ИИ на этих тестовых персонажах не добавлен. Тем не менее, фреймрейт на моем слабом пк снизился до `17` кадров в секунду. (обычно он в районе 40)
Выясним почему так происходит. Вводим команды `stat game`.
![edd201ed388d7041dc760c117c87199d.png](../images/edd201ed388d7041dc760c117c87199d.png)
В появившемся окне видим, что сильнее всего замедляет систему `WorldTickTime`, на который приходится `20ms` общего фреймтайма.
Главная причина такой просадки производитеьности - метод `BlueprintUpdateAnimation`.
![c4a9b10f7dc34fb42271cfc2cff1e922.png](../images/c4a9b10f7dc34fb42271cfc2cff1e922.png)
Математические операции, а так-же проверки условий в `blueprint` коде медленнее, чем в `с++`.
## Добавляем поддержку многопоточности в `Animation Blueprint`
`Unreal Engine` позволяет просчитывать логику метода `BlueprintUpdateAnimation` для разных персонажей - в разных потоках. Такой подход, от части, нивелирует низкую производительность `blueprint` кода.
Несмотря на то, что моя книга посвящена `с++`, я рассмотрю этот способ работы с `Animation Blueprint`.
### Включаем плагины
Для начала проверяем настройки проекта. Переходим по пути `Edit -> Project Settings -> General Settins` и ищем настройку `Allow Multi Threaded Animation Update`. Ставим `True`.
![d3f02aa0a55699a91671b70290aa97be.png](../images/d3f02aa0a55699a91671b70290aa97be.png)
Теперь надо активировать плагин, добавляющий поддержку многопоточного доступа к переменным в `AnimBP`. Плагин называется `Property Access Node`. Найти его можно в секции `Edit -> Pluigins -> Blueprints`.
![c68c0475732f382de77c2067e4464d43.png](../images/c68c0475732f382de77c2067e4464d43.png)
### Добавляем ноды `Property Access`
После активации плагина и перезапуска движка, снова откройте `AnimBp` и, в `EventGraph`, щелкните правой кнопкой мыши. В открывшемся меню поиска `blueprint` нод вбейте строчку `PropertyAccess`.
![b385157c4791fb9a75b847c641206859.png](../images/b385157c4791fb9a75b847c641206859.png)
Добавьте найденную ноду в граф. В ноде `PropertyAccess` можно задать любую переменную, доступ к которой надо сделать потокобезопасным.
![d085efcd22b436227ea51719c145fc72.png](../images/d085efcd22b436227ea51719c145fc72.png)
### Переопределяем `BlueprintThreadSafeUpdateAnimation`
Для многопоточного обновления параметров в `AnimationBlueprint` есть метод `BlueprintThreadSafeUpdateAnimation`, который выступает альтернативой для старого метода `BlueprintUpdateAnimation`.
Идем во вкладку `Functions`, щелкаем кнопку `Override` и переопределяем метод `BlueprintThreadSafeUpdateAnimation`.
![9590b38ac0d66f4f3d963ff44794e5f9.png](../images/9590b38ac0d66f4f3d963ff44794e5f9.png)
Логика метода `BlueprintUpdateAnimation` должна быть переписана внутри метода `BlueprintThreadSafeUpdateAnimation`, с применением нод `PropertyAccess`.
![9f6aa99712fe5b39416f18329ec20a53.png](../images/9f6aa99712fe5b39416f18329ec20a53.png)
Дело в том, что если внутри метода `BlueprintThreadSafeUpdateAnimation` мы попытаемся обработать обычную переменную - при запуске игры проекта возникнет сбой. Так происходит, потому что доступ к обычным `blueprint` переменным не потокобезопасен. Нода `PropertyAccess` исправляет эту проблему.
Я переписал логику метода следующим образом:
![3c5d64a30508dff7a9c7aba89bbbbf22.png](../images/3c5d64a30508dff7a9c7aba89bbbbf22.png)
На скриншете очень плохо видно целевой набор нод, поэтому вот вам приближенные участки графа.
![a19bc33a0f67ab68e7d90b1daf0de5f6.png](../images/a19bc33a0f67ab68e7d90b1daf0de5f6.png)
![a2de003bf8a403009ec20a8c1cf9a483.png](../images/a2de003bf8a403009ec20a8c1cf9a483.png)
![ff0bef79bdabd053b75abd28b064e8e0.png](../images/ff0bef79bdabd053b75abd28b064e8e0.png)
![8c657752c7bf756243ba30b656f5073e.png](../images/8c657752c7bf756243ba30b656f5073e.png)
Разумеется, логику старого метода `BlueprintUpdateAnimation` надо отключить.
![904378cb1a098d21be0f1acb6bf0aa32.png](../images/904378cb1a098d21be0f1acb6bf0aa32.png)
### Дополняем `AnimGraph`
В `AnimGraph` доступ ко всем переменным тоже надо обернуть в ноду `Property Access`.
![973f3486d725cf515cfe3e4081a432da.png](../images/973f3486d725cf515cfe3e4081a432da.png)
В каждом графе, в каждом узле.
![ce6b965ecce8997470ee4091650e03a0.png](../images/ce6b965ecce8997470ee4091650e03a0.png)
![f3f6f353abc31c59f6e8b19e2f786eff.png](../images/f3f6f353abc31c59f6e8b19e2f786eff.png)
![be8612b2c3828568417e7acaffbab782.png](../images/be8612b2c3828568417e7acaffbab782.png)
![64a2149463fe0e68ced87bcfe0ad02d2.png](../images/64a2149463fe0e68ced87bcfe0ad02d2.png)
### Результат
Запускаем проект.
После всех этих операций, производительность сцены с `60` персонажами, в моем случае, улучшилась, но не так значительно. Фреймрейт с `17` поднялся до `22-25`, а фреймтайм сократился с `55+` до `45`.
![4b599effa827b149a910c5fc639731a0.png](../images/4b599effa827b149a910c5fc639731a0.png)
Как вы видите, прирост производительности от `blueprint` многопоточности есть - но он не достаточен.
Чтобы получить еще более мощный прирост - реализуем обновление данных анимации через `с++`.
## Создаем класс `AnimInstance`
Предлагаю переписать логику методов `Blueprint Update Animation` на с++  с применением `Multithreaded Animation Update`.
Про `Multithreaded Animation Update` есть статья в официальной [документации](https://docs.unrealengine.com/5.0/en-US/animation-optimization-in-unreal-engine/). К сожалению она устарела. О том, как надо работать с `AnimInstance` можно посмотреть в официальном репозитории `Advanced Locomotion System`, по [ссылке](https://github.com/dyanikoglu/ALS-Community/blob/main/Source/ALSV4_CPP/Private/Character/Animation/ALSCharacterAnimInstance.cpp), обратите внимание на метод `NativeUpdateAnimation`.
Но даже код `ALS` немного устарел.
Рассмотрим более актуальный подход.
Добавляем новый класс `Tools -> New C++ Class -> All Classes -> Anim Instance`.
![2b7ec796db0de57240d6303311ce4a6b.png](../images/2b7ec796db0de57240d6303311ce4a6b.png)
Я назвал класс `AnimInstanceBase`.
![04e7a2b5a683480110577d24271e99f9.png](../images/04e7a2b5a683480110577d24271e99f9.png)
Созданный класс будет унаследован от `UAnimInstance`. Он выглядит так:
![debc25e8e6d263a0558cfd9bbbb34335.png](../images/debc25e8e6d263a0558cfd9bbbb34335.png)
Мы будем переопределять методы класса `UAnimInstance`, с префиксом `Native`.
Логика префиксов такова: `BlueprintUpdateAnimation` = `NativeUpdateAnimation`.
![12715a4bb34e6a9f959b647433ec503e.png](../images/12715a4bb34e6a9f959b647433ec503e.png)
### Файл `AnimInstanceBase.h`
```cpp
#pragma once
#include "CoreMinimal.h"
#include "Animation/AnimInstance.h"
#include "Animation/AnimInstanceProxy.h"
#include "GameFramework/CharacterMovementComponent.h"
// Kismet
#include "Kismet/KismetMathLibrary.h"
// ACharacter class refrence
#include "GameFramework/Character.h"
// generated.h
#include "AnimInstanceBase.generated.h"
/**
 * Anim Instance Base Class
 */
UCLASS(Blueprintable, BlueprintType)
class TESTMODULE_API UAnimInstanceBase : public UAnimInstance
{
	GENERATED_BODY()
public:
	// constructor
	UAnimInstanceBase(const FObjectInitializer& ObjectInitializer);
	// Native initialization override point
	// here we get base object refrences that not change over time
	virtual void NativeInitializeAnimation() override;
	// Native update override point.
	// It is usually a good idea to simply collect data in this step
	// and then process heavy calculations on collected data in NativeThreadSafeUpdateAnimation
	virtual void NativeUpdateAnimation(float DeltaSeconds) override;
	// Native thread safe update override point.
	// Executed on a worker thread just prior to graph update
	// for linked anim instances, only called when the hosting node(s) are relevant
	virtual void NativeThreadSafeUpdateAnimation(float DeltaSeconds) override;
	// Executed when begin play is called on the owning component
	virtual void NativeBeginPlay() override;
private:
	// Character refrence
	UPROPERTY(Transient, BlueprintReadOnly, Category = "Refrences", meta = (AllowPrivateAccess = "true"))
	TObjectPtr<ACharacter> Character = nullptr;
	// Movement Component refrence
	UPROPERTY(Transient, BlueprintReadOnly, Category = "Refrences", meta = (AllowPrivateAccess = "true"))
	TObjectPtr<UCharacterMovementComponent> MovementComponent = nullptr;
	// Velocity Vector
	UPROPERTY(VisibleDefaultsOnly, BlueprintReadOnly, Category = "Animation Params", meta = (AllowPrivateAccess = "true"))
	FVector Velocity;
	// Current Acceleration
	UPROPERTY(VisibleDefaultsOnly, BlueprintReadOnly, Category = "Animation Params", meta = (AllowPrivateAccess = "true"))
	FVector CurrentAcceleration;
	// Velocity Vector Length
	UPROPERTY(VisibleDefaultsOnly, BlueprintReadOnly, Category = "Animation Params", meta = (AllowPrivateAccess = "true"))
	float GroundSpeed;
	UPROPERTY(VisibleDefaultsOnly, BlueprintReadOnly, Category = "Animation Params", meta = (AllowPrivateAccess = "true"))
	bool ShouldMove = false;
	UPROPERTY(VisibleDefaultsOnly, BlueprintReadOnly, Category = "Animation Params", meta = (AllowPrivateAccess = "true"))
	bool IsFalling = false;
};
```
### Файл `AnimInstanceBase.cpp`
```cpp
#include "AnimInstanceBase.h"
// constructor
UAnimInstanceBase::UAnimInstanceBase(const FObjectInitializer& ObjectInitializer) {
	/* Nothing */
}
// Executed when begin play is called on the owning component
void UAnimInstanceBase::NativeBeginPlay()
{
	Super::NativeBeginPlay();
	/* Nothing */
}
// Native initialization override point
// here we get base object refrences that not change over time
void UAnimInstanceBase::NativeInitializeAnimation()
{
	Super::NativeInitializeAnimation(); // call parent
	Character = Cast<ACharacter>(TryGetPawnOwner());
	if (Character != nullptr) {
		// character movement refrence
		MovementComponent = Character->GetCharacterMovement();
	}
}
// Native update override point.
// It is usually a good idea to simply collect data in this step
// and then process heavy calculations on collected data in NativeThreadSafeUpdateAnimation
void UAnimInstanceBase::NativeUpdateAnimation(float DeltaSeconds)
{
	Super::NativeUpdateAnimation(DeltaSeconds);
	if(Character == nullptr || MovementComponent == nullptr) return;
	// collect values here
	Velocity = MovementComponent->Velocity;
	IsFalling = MovementComponent->IsFalling();
}
// Native thread safe update override point.
// Executed on a worker thread just prior to graph update
// for linked anim instances, only called when the hosting node(s) are relevant
void UAnimInstanceBase::NativeThreadSafeUpdateAnimation(float DeltaSeconds)
{
	Super::NativeThreadSafeUpdateAnimation(DeltaSeconds);
	if (Character == nullptr || MovementComponent == nullptr) return;
	// all calculations go here
	GroundSpeed = UKismetMathLibrary::VSizeXY(Velocity);
	CurrentAcceleration = MovementComponent->GetCurrentAcceleration();
	//UE_LOG(LogTemp, Warning, TEXT("Speed: %f"), GroundSpeed)
	ShouldMove = (GroundSpeed > 3.f || !UKismetMathLibrary::Vector_IsNearlyZero(CurrentAcceleration));
}
```
### Интеграция кода
После компиляции надо будет создать копию файла `ABP_Manny`, я назвал ее `ABP_Manny_CPP`.
![cf6358a704f5493327eac748d7b501a8.png](../images/cf6358a704f5493327eac748d7b501a8.png)
В полученной копии надо удалить код методов `BlueprintThreadSafeUpdateAnimation` и `BlueprintUpdateAnimation`.
![b8721921c7c49cf37ded5ad0f91be053.png](../images/b8721921c7c49cf37ded5ad0f91be053.png)
Переменные удалять не нужно. Система сама с ними разберется, потом подчистим.
Устанавливаем новый родительский класс. Для этого идем в классе `ABP_Manny_CPP` открываем пункю меню `File -> Reparent Blueprint`.
![846f2996b6bba8713422e97405d965c7.png](../images/846f2996b6bba8713422e97405d965c7.png)
В появившемся списке выбираем класс `AnimInstanceBase`, который только что создали.
![49c66d60c1816a87ca5b9988b3729e1d.png](../images/49c66d60c1816a87ca5b9988b3729e1d.png)
Теперь открываем `AnimGraph` и выставляем вместо `blueprint` переменных - переменные от родительского класса там, где они не выставились автоматически.
![a392a745b913f1462ac29c58b2a72e4a.png](../images/a392a745b913f1462ac29c58b2a72e4a.png)
Причем выставлять переменные вы можете как через ноды `Property Access`, так и напрямую. `c++` переменные, выведенные в `AnimBlueprint` являются потокобезопасными "по умолчанию".
![3fb97ec49f7f1dc6c9a853e7e8eac00f.png](../images/3fb97ec49f7f1dc6c9a853e7e8eac00f.png)
### Результат
При использовании `с++` и `NativeThreadSafeUpdateAnimation` фреймрейт вырос с `22` кадров до `25` кадров. И фреймтайм улучшился  с `45ms` до `39ms`.
![49368739cbbb0f6df7875f9609dbac1d.png](../images/49368739cbbb0f6df7875f9609dbac1d.png)
Я ожидал более существенных изменений, но и эти - вполне неплохие.
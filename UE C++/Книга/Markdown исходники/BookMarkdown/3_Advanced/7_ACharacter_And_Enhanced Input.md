# `ACharacter` и `Enhanced Input`
В этой статье рассмотрим пример работы с классом `ACharacter`, а так-же плагином `Enhanced Input`.
## Введение в плагин `Enhanced Input`
Для начала разберемся с плагином. Потому что его я буду использовать для обработки игровых событий.
В проекте типа `Third Person`, который я использую для написания статей, плагин `Enhanced Input` уже включен. Если у вас он не активен - включите.
![f7d7e8cd3f1e56d8fc51441c18ee10bc.png](../images/f7d7e8cd3f1e56d8fc51441c18ee10bc.png)
Плагин `Enhanced Input` позволяет динамически подключаться к устройствам ввода и отслеживать нажатия клавиш  / сочетаний клавиш.
### Работа с плагином в `blueprint`
Использование данного плагина на стороне `blueprint` кода крайне простое. Плагин предоставляет доступ к особому типу событий - `EnhancedInputAction`, которые можно создавать внутри любого `Character'a`.
![9f1c05f94d087c77ae7235ffff9cff88.png](../images/9f1c05f94d087c77ae7235ffff9cff88.png)
Сами события генерируются из файлов `Input Action`, которые разработчик может создавать вручную.
![0396b0b46ee240c8ae872be60ff2db9c.png](../images/0396b0b46ee240c8ae872be60ff2db9c.png)
Для этого щелкните правой кнопкой мыши в проводнике, далее `Input->Input Action`.
Несколько действий - `Input Action` можно объединить в файле `Input Mapping Context`.
![ba3f68735fc3c8fbced3eb88488f5061.png](../images/ba3f68735fc3c8fbced3eb88488f5061.png)
В файле `Input Mapping Context` указываются сочетания клавиш, которые вызывают то или иное действие.
Файлов типа `Input Mapping Context` может быть несколько - под каждое из состояний игры. Контексты ввода могут переключатсья как через `bluepruint'ы`, так и посредством `с++`. В `blueprint` для изменения или загрузки контекста ввода используется узел `Add Mapping Context`.
![475fcc7de645c9b2b69d9aa6e6cce4e1.png](../images/475fcc7de645c9b2b69d9aa6e6cce4e1.png)
Для добавления своей клавиши в активный контекст из `blueprint` кода можно использовать узел `AddPlayerMappedKey`.
![de0165a338e59db4d0133736b1750fdd.png](../images/de0165a338e59db4d0133736b1750fdd.png)
Вообще-же список основных `blueprint` узлов для работы с плагином `Enhanced Input` выглядит так:
![279d7f88108e6f6f7a4c0ff825141109.png](../images/279d7f88108e6f6f7a4c0ff825141109.png)
## Исходники плагина `Enhanced Input`
Исходники плагина, которые я настоятельно рекомендую изучить, можно найти в папке `Engine\Plugins\EnhancedInput\Source\`.
![9eeea360d372844080b91069c324e87e.png](../images/9eeea360d372844080b91069c324e87e.png)
## Подготовка к работе
Добавляем модуль `EnhancedCharacter`.
![48e8e1250cd74c6b64656030880f2b6b.png](../images/48e8e1250cd74c6b64656030880f2b6b.png)
После создания модуля обновите файлы проекта командой `Tools->Refresh Visual Studio 2022 Project`.
![001363a30b1701950f20404f3f9db285.png](../images/001363a30b1701950f20404f3f9db285.png)
### Подключаем зависимости
В файл сборки  модуля `EnhancedCharacter.build.cs` необходимо добавить `EnhancedInput` и `InputCore`.
```cpp
using UnrealBuildTool;
public class EnhancedCharacter : ModuleRules
{
	public EnhancedCharacter(ReadOnlyTargetRules Target) : base(Target)
	{
        PCHUsage = PCHUsageMode.UseExplicitOrSharedPCHs;
		PublicDependencyModuleNames.AddRange(new string[] { "Core", "CoreUObject", "Engine", "InputCore", "EnhancedInput" });
		PublicIncludePaths.AddRange(new string[] {"EnhancedCharacter/Public"});
		PrivateIncludePaths.AddRange(new string[] {"EnhancedCharacter/Private"});
	}
}
```
![dea789995c29a6ed19c69f781d15c135.png](../images/dea789995c29a6ed19c69f781d15c135.png)
### Создаем класс персонажа
Добавим класс новый `Character` класс в модуль `EnhancedCharacter`.
Для этого идем `Tools -> New C++ Class`, в появившемся меню ищем пункт `Character`.
![4524edef98e6380b002ae746cd68c916.png](../images/4524edef98e6380b002ae746cd68c916.png)
Называем новый класс - `EnCharacter`.
![f3155020b0f4d9d89748f1f46601391e.png](../images/f3155020b0f4d9d89748f1f46601391e.png)
Обновляем файлы проекта командой `Tools -> Refresh Visual Studio 2022 Project`.
![c87e77a06dabe3cae8a7956c365b31b0.png](../images/c87e77a06dabe3cae8a7956c365b31b0.png)
Открываем файл `EnCharacter.h` для редактирования.
## Подключение плагина `Enhanced Input` к персонажу
Давайте подключим обработку событий через плагин `Enhanced Input` к созданному персонажу.
### Добавляем `InputContext`
Начнем с добавления `InputContext`.
Добавим переменную типа `UInputMappingContext`. Для нее придется подключить заголовочный файл, а мы не знаем какой. Чтобы узнать вбиваем в гугл название класса и идем в [документацию](https://docs.unrealengine.com/4.26/en-US/API/Plugins/EnhancedInput/UInputMappingContext/).
![cfd8b4e08aa4b7d9e18b76400619ec66.png](../images/cfd8b4e08aa4b7d9e18b76400619ec66.png)
```cpp
#include "InputMappingContext.h"
// ...
// MappingContext
UPROPERTY(EditAnywhere, BlueprintReadWrite, Category = Input)
TSoftObjectPtr<UInputMappingContext> DefaultMappingContext = nullptr;
```
![3196e60469b74b28d08788aa18c84b81.png](../images/3196e60469b74b28d08788aa18c84b81.png)
Переменной контекста надо дать значение "по умолчанию", чтобы она не была пустой.
У нас в проекте уже есть заготовленный `MappingContext` в папке `ThirdPerson/Input/'`.
![437608d80f3ed4bef581963febeca237.png](../images/437608d80f3ed4bef581963febeca237.png)
Копируем его рефренс и загружаем в переменную из конструктора класса.
```cpp
// Kismet
#include "Kismet/KismetSystemLibrary.h"
// ...
// Load Default Mapping Context
if (DefaultMappingContext == nullptr) {
    const FString DefaultMappingContextAsset = TEXT("/Script/EnhancedInput.InputMappingContext'/Game/ThirdPerson/Input/IMC_Default.IMC_Default'");
    DefaultMappingContext = UKismetSystemLibrary::MakeSoftObjectPath(DefaultMappingContextAsset);
}
```
![191ca58693657796fc5d9183e1d3f59f.png](../images/191ca58693657796fc5d9183e1d3f59f.png)
Теперь у переменной контекста будет значение "по умолчанию".
Чтобы проверить - перекиньте полученного персонажа на уровень и посмотрите его настройки в панели `Details`.
![c65cc6f5295e06ca887655b88379ce48.png](../images/c65cc6f5295e06ca887655b88379ce48.png)
### Загружаем `Input Context`
Загрузим выбранный контекст ввода.
В `blueprint` части за это отвечает такой код:
![54e2aaa3d7d2d26f58f9026d0839bf69.png](../images/54e2aaa3d7d2d26f58f9026d0839bf69.png)
Воспроизведу `blueprint` код его в секции `BeginPlay`.
```cpp
// Enhanced Input
#include "EnhancedInputSubsystems.h"
// ...
// Called when the game starts or when spawned
void AEnCharacter::BeginPlay()
{
	Super::BeginPlay();
	// Load Mapping Context if controller is posessed
	APlayerController *PController = Cast<APlayerController>(GetController());
	if (PController != nullptr) {
		const ULocalPlayer* LPlayer = PController->GetLocalPlayer();
		UEnhancedInputLocalPlayerSubsystem* ESubsystem = ULocalPlayer::GetSubsystem<UEnhancedInputLocalPlayerSubsystem>(LPlayer);
		if (ESubsystem != nullptr) {
			ESubsystem->AddMappingContext(DefaultMappingContext.Get(), InputPriority);
		}
	}
}
```
![3a7df7a475be7a8344c1bc276323283c.png](../images/3a7df7a475be7a8344c1bc276323283c.png)
### Создаем ссылки на `Input Action`
Помимо контекста в `с++` нужно получить сслыки на сами действия ввода. Потому что, в отличие от `blueprint` кода в `с++` у нас нет возможности загрузить данные действия автоматически.
![c4c175b93561854414c11e00b2723fbe.png](../images/c4c175b93561854414c11e00b2723fbe.png)
Создаем переменные для каждого действия.
```cpp
// Jump
UPROPERTY(EditAnywhere, BlueprintReadWrite, Category = Input)
TSoftObjectPtr<UInputAction> JumpAction = nullptr;
// Move
UPROPERTY(EditAnywhere, BlueprintReadWrite, Category = Input)
TSoftObjectPtr<UInputAction> MoveAction = nullptr;
// Rotate
UPROPERTY(EditAnywhere, BlueprintReadWrite, Category = Input)
TSoftObjectPtr<UInputAction> RotateAction = nullptr;
```
Разумеется, полученным переменным тоже надо скормить ассет, установленный "по умолчанию".
Идем в конструктор класса и дополняем его следующим кодом.
```cpp
// Load Jump Action
if (JumpAction == nullptr) {
    const FString JumpActionAsset = TEXT("/Script/EnhancedInput.InputAction'/Game/ThirdPerson/Input/Actions/IA_Jump.IA_Jump'");
    JumpAction = UKismetSystemLibrary::MakeSoftObjectPath(JumpActionAsset);
}
// Load Move Action
if (MoveAction == nullptr) {
    const FString MoveActionAsset = TEXT("/Script/EnhancedInput.InputAction'/Game/ThirdPerson/Input/Actions/IA_Move.IA_Move'");
    MoveAction = UKismetSystemLibrary::MakeSoftObjectPath(MoveActionAsset);
}
// Load Rotate Action
if (RotateAction == nullptr) {
    const FString RotateActionAsset = TEXT("/Script/EnhancedInput.InputAction'/Game/ThirdPerson/Input/Actions/IA_Look.IA_Look'");
    RotateAction = UKismetSystemLibrary::MakeSoftObjectPath(RotateActionAsset);
}
```
![2bec4715401389373b3594aab50a7c8f.png](../images/2bec4715401389373b3594aab50a7c8f.png)
### Добавляем логику `Input Action`
Зададим логику, которая будет вызываться для каждого `Input Action`.
Для начала нам нужны функции, которые мы будем соединять с командами ввода.
Создадим определения для функций в файле `EnCharacter.h`.
```cpp
// Movement Input
void Move(const FInputActionValue& Value);
// Camera Input
void Rotate(const FInputActionValue& Value);
```
И их реализацию.
Она будет совпадать с той, что имеет место в `blueprint` коде.
![ad2af8095bdb4246167e21fe7af975bc.png](../images/ad2af8095bdb4246167e21fe7af975bc.png)
Для функции `Rotate` реализация будет такой:
```cpp
// Roation Input
void AEnCharacter::Rotate(const FInputActionValue& Value)
{
	// input is a Vector2D
	FVector2D LookAxisVector = Value.Get<FVector2D>();
	// if controller is possessed
	if (Controller != nullptr) {
		AddControllerYawInput(LookAxisVector.X);
		AddControllerPitchInput(LookAxisVector.Y);
	}
}
```
Для функции `Move` реализация будет такой:
```cpp
// Movement Input
void AEnCharacter::Move(const FInputActionValue& Value)
{
	// input is a Vector2D
	FVector2D MovementVector = Value.Get<FVector2D>();
	if (Controller != nullptr)
	{
		// find out which way is forward
		const FRotator Rotation = Controller->GetControlRotation();
		const FRotator YawRotation(0, Rotation.Yaw, 0);
		// get forward vector
		const FVector ForwardDirection = FRotationMatrix(YawRotation).GetUnitAxis(EAxis::X);
		// get right vector
		const FVector RightDirection = FRotationMatrix(YawRotation).GetUnitAxis(EAxis::Y);
		// add movement
		AddMovementInput(ForwardDirection, MovementVector.Y);
		AddMovementInput(RightDirection, MovementVector.X);
	}
}
```
Для события `Jump` метод не нужен - он уже есть внутри класса `ACharacter` и реализован как подбрасывание персонажа вверх. Мы просто его вызовем.
### Привязываем `Input Action` к контексту ввода
В базовом классе `ACharacter` по умолчанию представлен метод - `SetupPlayerInputComponent`, предназначенный для переопределения ввода.
![a335662770e75b640fc58f5fdc38c798.png](../images/a335662770e75b640fc58f5fdc38c798.png)
Этот метод нам понадобится для работы.
Именно в тут надо подключать события ввода к методам класса.
Можно, конечно, делать это и  в `BeginPlay`, но там нет прямой ссылки на `PlayerInputComponent` (хотя ее легко можно получить).
Переопределяем метод и пишем его реализацию:
```cpp
//.h
#include "EnhancedInputComponent.h"
// Called to bind functionality to input
virtual void SetupPlayerInputComponent(class UInputComponent* PlayerInputComponent) override;
// ...
//.cpp
// Called to bind functionality to input
void AEnCharacter::SetupPlayerInputComponent(UInputComponent* PlayerInputComponent)
{
	Super::SetupPlayerInputComponent(PlayerInputComponent);
	UEnhancedInputComponent* EnhancedInputComponent = Cast<UEnhancedInputComponent>(PlayerInputComponent);
	// Bind Actions
	if (EnhancedInputComponent != nullptr) {
		//Jumping
		EnhancedInputComponent->BindAction(JumpAction.Get(), ETriggerEvent::Triggered, this, &ACharacter::Jump);
		EnhancedInputComponent->BindAction(JumpAction.Get(), ETriggerEvent::Completed, this, &ACharacter::StopJumping);
		//Moving
		EnhancedInputComponent->BindAction(MoveAction.Get(), ETriggerEvent::Triggered, this, &AEnCharacter::Move);
		//Looking
		EnhancedInputComponent->BindAction(RotateAction.Get(), ETriggerEvent::Triggered, this, &AEnCharacter::Rotate);
	}
}
```
Все готово и должно работать.
### Файл `EnCharacter.h`
```cpp
#pragma once
#include "CoreMinimal.h"
#include "GameFramework/Character.h"
// Mapping Context
#include "InputMappingContext.h"
// Enhanced Input
#include "EnhancedInputSubsystems.h"
#include "EnhancedInputComponent.h"
// Kismet
#include "Kismet/KismetSystemLibrary.h"
#include "Kismet/GameplayStatics.h"
// Animation Asset
#include "Animation/AnimationAsset.h"
// Capsule Component
#include "Components/CapsuleComponent.h"
// Character Movement
#include "GameFramework/CharacterMovementComponent.h"
// Camera
#include "Camera/CameraComponent.h"
// Spring Arm
#include "GameFramework/SpringArmComponent.h"
// generated.h
#include "EnCharacter.generated.h"
UCLASS()
class ENHANCEDCHARACTER_API AEnCharacter : public ACharacter
{
	GENERATED_BODY()
public:
	// Sets default values for this character's properties
	AEnCharacter(const FObjectInitializer& ObjectInitializer);
	// Spring Arm
	UPROPERTY(VisibleAnywhere, BlueprintReadOnly, Category = Camera)
	TObjectPtr<USpringArmComponent> CameraBoom;
	// Camera
	UPROPERTY(VisibleAnywhere, BlueprintReadOnly, Category = Camera)
	TObjectPtr<UCameraComponent> FollowCamera;
	// MappingContext
	UPROPERTY(EditAnywhere, BlueprintReadWrite, Category = Input)
	TSoftObjectPtr<UInputMappingContext> DefaultMappingContext = nullptr;
	// Jump
	UPROPERTY(EditAnywhere, BlueprintReadWrite, Category = Input)
	TSoftObjectPtr<UInputAction> JumpAction = nullptr;
	// Move
	UPROPERTY(EditAnywhere, BlueprintReadWrite, Category = Input)
	TSoftObjectPtr<UInputAction> MoveAction = nullptr;
	// Rotate
	UPROPERTY(EditAnywhere, BlueprintReadWrite, Category = Input)
	TSoftObjectPtr<UInputAction> RotateAction = nullptr;
protected:
	// Called when the game starts or when spawned
	virtual void BeginPlay() override;
	// Movement Input
	void Move(const FInputActionValue& Value);
	// Camera Input
	void Rotate(const FInputActionValue& Value);
public:
	// Called every frame
	virtual void Tick(float DeltaTime) override;
	// Called to bind functionality to input
	virtual void SetupPlayerInputComponent(class UInputComponent* PlayerInputComponent) override;
	// Once character posessed
	virtual void PossessedBy(AController* NewController) override;
};
```
### Файл `EnCharacter.cpp`
```cpp
#include "EnCharacter.h"
// Sets default values
AEnCharacter::AEnCharacter(const FObjectInitializer& ObjectInitializer)
{
	// SpringArm for the camera
	CameraBoom = CreateDefaultSubobject<USpringArmComponent>(TEXT("CameraBoom"));
	CameraBoom->SetRelativeLocation(FVector3d(0.0f, 0.0f, 8.492264f));
	CameraBoom->SetupAttachment(RootComponent);
	CameraBoom->TargetArmLength = 400.0f; // The camera follows at this distance behind the character
	CameraBoom->bUsePawnControlRotation = true; // Rotate the arm based on the controller
	CameraBoom->bInheritPitch = true;
	CameraBoom->bInheritYaw = true;
	CameraBoom->bInheritRoll = true;
	// Camera
	FollowCamera = CreateDefaultSubobject<UCameraComponent>(TEXT("FollowCamera"));
	FollowCamera->SetupAttachment(CameraBoom, USpringArmComponent::SocketName); // Attach the camera to the end of the boom and let the boom adjust to match the controller orientation
	FollowCamera->bUsePawnControlRotation = false; // Camera does not rotate relative to arm
	FollowCamera->Deactivate(); // Deactivate camera by default to prevent performance issues
	// Default Rotation Settings
	bUseControllerRotationPitch = false;
	bUseControllerRotationYaw = false;
	bUseControllerRotationRoll = false;
	// Set Character Movement values
	UCharacterMovementComponent* CharMoveComp = GetCharacterMovement();
	CharMoveComp->bOrientRotationToMovement = true; // will force character to rotate towards velocity direction
	CharMoveComp->RotationRate = FRotator(0.0f, 500.0f, 0.0f); // with rotation rate
	// Speed values
	CharMoveComp->JumpZVelocity = 700.f;
	CharMoveComp->AirControl = 0.35f;
	CharMoveComp->MaxWalkSpeed = 500.f;
	CharMoveComp->MinAnalogWalkSpeed = 20.f;
	CharMoveComp->BrakingDecelerationWalking = 2000.f;
	// Set Capsule Size
	UCapsuleComponent* CapsuleComp = GetCapsuleComponent();
	CapsuleComp->InitCapsuleSize(35.0f,90.0f);
	USkeletalMeshComponent* MeshComp = GetMesh();
	// Load Default Skeletal Mesh
	if (MeshComp->GetSkeletalMeshAsset() == nullptr) {
		const FString MannequinAsset = TEXT("/Script/Engine.SkeletalMesh'/Game/Characters/Mannequins/Meshes/SKM_Manny.SKM_Manny'");
		const TSoftObjectPtr<USkeletalMesh> SKMesh(MannequinAsset);
		if (!SKMesh.IsNull()) {
			MeshComp->SetSkeletalMesh(SKMesh.LoadSynchronous());
		}
		// Load ainm instance as well
		// since we loading blueprint class, add _C at the end of the asset refrence
		const FString AnimationInstanceAsset = TEXT("/Script/Engine.AnimBlueprint'/Game/Characters/Mannequins/Animations/ABP_Manny.ABP_Manny_C'");
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
	// Load Default Mapping Context
	if (DefaultMappingContext == nullptr) {
		const FString DefaultMappingContextAsset = TEXT("/Script/EnhancedInput.InputMappingContext'/Game/ThirdPerson/Input/IMC_Default.IMC_Default'");
		DefaultMappingContext = UKismetSystemLibrary::MakeSoftObjectPath(DefaultMappingContextAsset);
	}
	// Load Jump Action
	if (JumpAction == nullptr) {
		const FString JumpActionAsset = TEXT("/Script/EnhancedInput.InputAction'/Game/ThirdPerson/Input/Actions/IA_Jump.IA_Jump'");
		JumpAction = UKismetSystemLibrary::MakeSoftObjectPath(JumpActionAsset);
	}
	// Load Move Action
	if (MoveAction == nullptr) {
		const FString MoveActionAsset = TEXT("/Script/EnhancedInput.InputAction'/Game/ThirdPerson/Input/Actions/IA_Move.IA_Move'");
		MoveAction = UKismetSystemLibrary::MakeSoftObjectPath(MoveActionAsset);
	}
	// Load Rotate Action
	if (RotateAction == nullptr) {
		const FString RotateActionAsset = TEXT("/Script/EnhancedInput.InputAction'/Game/ThirdPerson/Input/Actions/IA_Look.IA_Look'");
		RotateAction = UKismetSystemLibrary::MakeSoftObjectPath(RotateActionAsset);
	}
}
// Called when the game starts or when spawned
void AEnCharacter::BeginPlay()
{
	Super::BeginPlay();
	// Load Mapping Context if controller is posessed
	APlayerController* PController = Cast<APlayerController>(GetController());
	if (PController != nullptr) {
		const ULocalPlayer* LPlayer = PController->GetLocalPlayer();
		UEnhancedInputLocalPlayerSubsystem* ESubsystem = ULocalPlayer::GetSubsystem<UEnhancedInputLocalPlayerSubsystem>(LPlayer);
		if (ESubsystem != nullptr) {
			ESubsystem->AddMappingContext(DefaultMappingContext.Get(), InputPriority);
		}
	}
}
// On Possessed
void AEnCharacter::PossessedBy(AController* NewController)
{
	// Enable Camera when posessed
	if (NewController->IsLocalPlayerController()) {
		FollowCamera->Activate();
	}
}
// Movement Input
void AEnCharacter::Move(const FInputActionValue& Value)
{
	// input is a Vector2D
	FVector2D MovementVector = Value.Get<FVector2D>();
	if (Controller != nullptr)
	{
		// find out which way is forward
		const FRotator Rotation = Controller->GetControlRotation();
		const FRotator YawRotation(0, Rotation.Yaw, 0);
		// get forward vector
		const FVector ForwardDirection = FRotationMatrix(YawRotation).GetUnitAxis(EAxis::X);
		// get right vector
		const FVector RightDirection = FRotationMatrix(YawRotation).GetUnitAxis(EAxis::Y);
		// add movement
		AddMovementInput(ForwardDirection, MovementVector.Y);
		AddMovementInput(RightDirection, MovementVector.X);
	}
}
// Roation Input
void AEnCharacter::Rotate(const FInputActionValue& Value)
{
	// input is a Vector2D
	FVector2D LookAxisVector = Value.Get<FVector2D>();
	// if controller is possessed
	if (Controller != nullptr) {
		AddControllerYawInput(LookAxisVector.X);
		AddControllerPitchInput(LookAxisVector.Y);
	}
}
// Called every frame
void AEnCharacter::Tick(float DeltaTime)
{
	Super::Tick(DeltaTime);
}
// Called to bind functionality to input
void AEnCharacter::SetupPlayerInputComponent(UInputComponent* PlayerInputComponent)
{
	UE_LOG(LogTemp, Warning, TEXT("Setup Input"))
	Super::SetupPlayerInputComponent(PlayerInputComponent);
	UEnhancedInputComponent* EnhancedInputComponent = Cast<UEnhancedInputComponent>(PlayerInputComponent);
	// Bind Actions
	if (EnhancedInputComponent != nullptr) {
		//Jumping
		EnhancedInputComponent->BindAction(JumpAction.Get(), ETriggerEvent::Triggered, this, &ACharacter::Jump);
		EnhancedInputComponent->BindAction(JumpAction.Get(), ETriggerEvent::Completed, this, &ACharacter::StopJumping);
		//Moving
		EnhancedInputComponent->BindAction(MoveAction.Get(), ETriggerEvent::Triggered, this, &AEnCharacter::Move);
		//Looking
		EnhancedInputComponent->BindAction(RotateAction.Get(), ETriggerEvent::Triggered, this, &AEnCharacter::Rotate);
	}
}
```
### Тестирование
Проверим код, который у нас получился.
Для начала надо создать `blueprint` класс, унаследованный от нашего `EnCharacter`. Идем в `Content Browser`, нажимаем на кнопку `Add -> Blueprint Class`.
![cbfcf65ee3e19bb609665cabdcfc68c5.png](../images/cbfcf65ee3e19bb609665cabdcfc68c5.png)
В появившемся окне, во вкладке `All Classes`, ищем класс `EnCharacter` и создаем `blueprint`.
![57c5b10063c1e585519296b2c7559eca.png](../images/57c5b10063c1e585519296b2c7559eca.png)
Называем новый класс `BP_EnCharacter`.
![d25846be3dcea3785f606939394ada01.png](../images/d25846be3dcea3785f606939394ada01.png)
Если открыть созданный `blueprint`, мы увидим, что меш персонажа и все компоненты загружены корректно.
![62ab2b90635dca1b46c15204053c095b.png](../images/62ab2b90635dca1b46c15204053c095b.png)
Чтобы протестировать работу нашего класса - добавим его в текущий `Game Mode`.
Поскольку у мы работаем в проекте `Third Person Template`, у нас уже есть реализация `Game Mode`. Ее можно найти в папке `Content/ThirdPerson/Blueprints`.
![6218681f01d249d9654ccc27db1f937b.png](../images/6218681f01d249d9654ccc27db1f937b.png)
Открываем `BP_ThirdPersonGameMode` и устанавливаем `BP_EnCharacter`, как `DefaultPawnClass`.
(у меня персонаж уже создан и называется `BP_EnCharacterTest`)
![d112b08dd650bf0d806aef3fb86a16ff.png](../images/d112b08dd650bf0d806aef3fb86a16ff.png)
Если сейчас запустить тестовую карту, то выбранный класс персонажа загрузится и мы сможем увидеть свой код в действии.
![37c7eea30f5e951c1b2586ca8c183c64.png](../images/37c7eea30f5e951c1b2586ca8c183c64.png)
Все инпуты работают, камера двигается, прыжки работают.
## Улучшаем логику персонажа
У подключенного таким образом плагина `EnhancedInput` есть один весьма существенный недостаток -  необходимость постоянно создавать новые переменные под новые `InputAction`.
![ce3176cd7eaf8a81ab62212be68a794d.png](../images/ce3176cd7eaf8a81ab62212be68a794d.png)
Причем создавать их придется всякий раз из кода, что крайне неудобно.
![2a560f9b2e04f0978633a80cd7c9a7c9.png](../images/2a560f9b2e04f0978633a80cd7c9a7c9.png)
При том, что у нас уже есть `DefaultMappingContext`, который содержит ссылки на все, необходимые, `InputAction`.
![376b5d2f7710d6355e165233ac008308.png](../images/376b5d2f7710d6355e165233ac008308.png)
Предлагаю использовать `MappingContext` для автоматического поиска и загрузки `InputAction`.
Под эти цели я написал метод:
```cpp
//.h
// will bind new mapping context
UFUNCTION(BlueprintCallable, Category = "Input")
void BindContext(TSoftObjectPtr<UInputMappingContext> Context = nullptr);
//...
//.cpp
// will bind new mapping context
// will bind new mapping context
void AEnCharacter::BindContext(TSoftObjectPtr<UInputMappingContext> Context)
{
	if (Context == nullptr) {
		Context = MappingContext;
	}
	if(Context == nullptr) return; // only because i don't want to crash app with check()
	// Load Mapping Context if controller is posessed
	APlayerController* PlayerController = Cast<APlayerController>(GetController());
	if (PlayerController == nullptr) return;
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
			EnhancedInputComponent->BindAction(Action, ETriggerEvent::Triggered, this, &ACharacter::Jump);
			EnhancedInputComponent->BindAction(Action, ETriggerEvent::Completed, this, &ACharacter::StopJumping);
		}
		// Moving
		if (AName.Contains(TEXT("Move"))) {
			EnhancedInputComponent->BindAction(Action, ETriggerEvent::Triggered, this, &AEnCharacter::Move);
		}
		// Sprinting
		if (AName.Contains(TEXT("Sprint"))) {
			EnhancedInputComponent->BindAction(Action, ETriggerEvent::Triggered, this, &AEnCharacter::StartSprint);
			EnhancedInputComponent->BindAction(Action, ETriggerEvent::Completed, this, &AEnCharacter::EndSprint);
		}
		// Looking around
		if (AName.Contains(TEXT("Look"))) {
			EnhancedInputComponent->BindAction(Action, ETriggerEvent::Triggered, this, &AEnCharacter::Rotate);
		}
	}
}
```
В этом методе я присваиваю `InputAction` по к обработчикам по их имени. Это очень сильно упрощает код, потому что создавать указатели под каждый `InputAction` в теле класса `EnCharacter` больше не надо. Старые указатели можно удалить.
Поскольку функция `BindContext` помечена как `BlueprintCallable`, ее можно спокойно использовать из `blueprint` кода для загрузки другого контекста, при необходимости.
![4b4c8b8b3690434d0f68cad98aa1be1a.png](../images/4b4c8b8b3690434d0f68cad98aa1be1a.png)
При этом переживать насчет имен файлов не стоит. `Unreal Engine` сохраняет имена и структуру файловой системы в упакованных проектах. Именно поэтому ассеты, загруженные по текстовым ссылкам - продолжают работать.
### Файл `EnCharacter.h`
```cpp
#pragma once
#include "CoreMinimal.h"
#include "GameFramework/Character.h"
// Mapping Context
#include "InputMappingContext.h"
// Enhanced Input
#include "EnhancedInputSubsystems.h"
#include "EnhancedInputComponent.h"
// Kismet
#include "Kismet/KismetSystemLibrary.h"
#include "Kismet/GameplayStatics.h"
// Animation Asset
#include "Animation/AnimationAsset.h"
// Capsule Component
#include "Components/CapsuleComponent.h"
// Character Movement
#include "GameFramework/CharacterMovementComponent.h"
// Camera
#include "Camera/CameraComponent.h"
// Spring Arm
#include "GameFramework/SpringArmComponent.h"
// generated.h
#include "EnCharacter.generated.h"
UCLASS()
class ENHANCEDCHARACTER_API AEnCharacter : public ACharacter
{
	GENERATED_BODY()
public:
	// Sets default values for this character's properties
	AEnCharacter(const FObjectInitializer& ObjectInitializer);
	// Spring Arm
	UPROPERTY(VisibleAnywhere, BlueprintReadOnly, Category = Camera)
	TObjectPtr<USpringArmComponent> CameraBoom;
	// Camera
	UPROPERTY(VisibleAnywhere, BlueprintReadOnly, Category = Camera)
	TObjectPtr<UCameraComponent> FollowCamera;
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
	void BindContext(TSoftObjectPtr<UInputMappingContext> Context = nullptr);
	// Called every frame
	virtual void Tick(float DeltaTime) override;
	// Called to bind functionality to input
	virtual void SetupPlayerInputComponent(class UInputComponent* PlayerInputComponent) override;
	// Once character posessed
	virtual void PossessedBy(AController* NewController) override;
};
```
### Файл `EnCharacter.cpp`
```cpp
#include "EnCharacter.h"
// Sets default values
AEnCharacter::AEnCharacter(const FObjectInitializer& ObjectInitializer)
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
			const FString MannequinAsset = TEXT("/Script/Engine.SkeletalMesh'/Game/Characters/Mannequins/Meshes/SKM_Manny.SKM_Manny'");
			const TSoftObjectPtr<USkeletalMesh> SKMesh(MannequinAsset);
			if (!SKMesh.IsNull()) {
				MeshComp->SetSkeletalMesh(SKMesh.LoadSynchronous());
			}
			// Load ainm instance as well
			// since we loading blueprint class, add _C at the end of the asset refrence
			const FString AnimationInstanceAsset = TEXT("/Script/Engine.AnimBlueprint'/Game/Characters/Mannequins/Animations/ABP_Manny.ABP_Manny_C'");
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
		const FString DefaultMappingContextAsset = TEXT("/Script/EnhancedInput.InputMappingContext'/Game/ThirdPerson/Input/IMC_Default.IMC_Default'");
		MappingContext = UKismetSystemLibrary::MakeSoftObjectPath(DefaultMappingContextAsset);
	}
}
// Called when the game starts or when spawned
void AEnCharacter::BeginPlay()
{
	Super::BeginPlay();
	BindContext(MappingContext.LoadSynchronous());
}
// will bind new mapping context
void AEnCharacter::BindContext(TSoftObjectPtr<UInputMappingContext> Context)
{
	if (Context == nullptr) {
		Context = MappingContext;
	}
	if(Context == nullptr) return; // only because i don't want to crash app with check()
	// Load Mapping Context if controller is posessed
	APlayerController* PlayerController = Cast<APlayerController>(GetController());
	if (PlayerController == nullptr) return;
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
			EnhancedInputComponent->BindAction(Action, ETriggerEvent::Triggered, this, &ACharacter::Jump);
			EnhancedInputComponent->BindAction(Action, ETriggerEvent::Completed, this, &ACharacter::StopJumping);
		}
		// Moving
		if (AName.Contains(TEXT("Move"))) {
			EnhancedInputComponent->BindAction(Action, ETriggerEvent::Triggered, this, &AEnCharacter::Move);
		}
		// Sprinting
		if (AName.Contains(TEXT("Sprint"))) {
			EnhancedInputComponent->BindAction(Action, ETriggerEvent::Triggered, this, &AEnCharacter::StartSprint);
			EnhancedInputComponent->BindAction(Action, ETriggerEvent::Completed, this, &AEnCharacter::EndSprint);
		}
		// Looking around
		if (AName.Contains(TEXT("Look"))) {
			EnhancedInputComponent->BindAction(Action, ETriggerEvent::Triggered, this, &AEnCharacter::Rotate);
		}
	}
}
// On Possessed
void AEnCharacter::PossessedBy(AController* NewController)
{
	// Enable Camera when posessed
	if (NewController->IsLocalPlayerController()) {
		FollowCamera->Activate();
	}
}
// Start Sprint
void AEnCharacter::StartSprint(const FInputActionValue& Value)
{
	UCharacterMovementComponent* CharMoveComp = GetCharacterMovement();
	CharMoveComp->MaxWalkSpeed = 500.f;
}
// End Sprint
void AEnCharacter::EndSprint(const FInputActionValue& Value)
{
	UCharacterMovementComponent* CharMoveComp = GetCharacterMovement();
	CharMoveComp->MaxWalkSpeed = 250.f;
}
// Movement Input
void AEnCharacter::Move(const FInputActionValue& Value)
{
	// input is a Vector2D
	FVector2D MovementVector = Value.Get<FVector2D>();
	if (Controller != nullptr)
	{
		// find out which way is forward
		const FRotator Rotation = Controller->GetControlRotation();
		const FRotator YawRotation(0, Rotation.Yaw, 0);
		// get forward vector
		const FVector ForwardDirection = FRotationMatrix(YawRotation).GetUnitAxis(EAxis::X);
		// get right vector
		const FVector RightDirection = FRotationMatrix(YawRotation).GetUnitAxis(EAxis::Y);
		// add movement
		AddMovementInput(ForwardDirection, MovementVector.Y);
		AddMovementInput(RightDirection, MovementVector.X);
	}
}
// Roation Input
void AEnCharacter::Rotate(const FInputActionValue& Value)
{
	// input is a Vector2D
	FVector2D LookAxisVector = Value.Get<FVector2D>();
	// if controller is possessed
	if (Controller != nullptr) {
		AddControllerYawInput(LookAxisVector.X);
		AddControllerPitchInput(LookAxisVector.Y);
	}
}
// Called every frame
void AEnCharacter::Tick(float DeltaTime)
{
	Super::Tick(DeltaTime);
}
// Called to bind functionality to input
void AEnCharacter::SetupPlayerInputComponent(UInputComponent* PlayerInputComponent)
{
	Super::SetupPlayerInputComponent(PlayerInputComponent);
}
```
## Pass Inputs without Possess

> Передать события ввода на персонажа, без вызова метода `Possess`.

Одной из фундаментальных проблем `Unreal Engine` является тот факт, что этот движок заточен под шутеры. Если же мы пытаемся сделать а нем рпг или стратегию - приходится заниматься извращениями. Именно таким извращениям посвящена эта статья.
В стратегии игрок должен иметь возможность управлять не одним персонажем, а целой группой персонажей. Из одного контроллера.
Единственный способ решить эту задачу - перехват событий у не подключенного контроллера, без вызова метода `posses` и прямого подключения персонажа.
Отмечу, что средствами `blueprint` эту задачу решить куда сложнее, чем через `с++`.
### Файл `EnCharacter.h`
```cpp
#pragma once
#include "CoreMinimal.h"
#include "GameFramework/Character.h"
// Mapping Context
#include "InputMappingContext.h"
// Enhanced Input
#include "EnhancedInputSubsystems.h"
#include "EnhancedInputComponent.h"
// Kismet
#include "Kismet/KismetSystemLibrary.h"
#include "Kismet/GameplayStatics.h"
// Animation Asset
#include "Animation/AnimationAsset.h"
// Capsule Component
#include "Components/CapsuleComponent.h"
// Character Movement
#include "GameFramework/CharacterMovementComponent.h"
// Camera
#include "Camera/CameraComponent.h"
// Spring Arm
#include "GameFramework/SpringArmComponent.h"
// generated.h
#include "EnCharacter.generated.h"
UCLASS()
class ENHANCEDCHARACTER_API AEnCharacter : public ACharacter
{
	GENERATED_BODY()
public:
	// Sets default values for this character's properties
	AEnCharacter(const FObjectInitializer& ObjectInitializer);
	// Spring Arm
	UPROPERTY(VisibleAnywhere, BlueprintReadOnly, Category = Camera)
	TObjectPtr<USpringArmComponent> CameraBoom;
	// Camera
	UPROPERTY(VisibleAnywhere, BlueprintReadOnly, Category = Camera)
	TObjectPtr<UCameraComponent> FollowCamera;
	// MappingContext
	UPROPERTY(EditAnywhere, BlueprintReadWrite, Category = Input)
	TSoftObjectPtr<UInputMappingContext> MappingContext = nullptr;
	// if true - will recive player input without being possessed
	UPROPERTY(EditDefaultsOnly, Category = Pawn)
	bool RecivePlayerInputWitoutPossess = false;
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
	void BindContext(TSoftObjectPtr<UInputMappingContext> Context = nullptr, AController* OnController = nullptr);
	// Called every frame
	virtual void Tick(float DeltaTime) override;
	// Called to bind functionality to input
	virtual void SetupPlayerInputComponent(class UInputComponent* PlayerInputComponent) override;
	// Once character posessed
	virtual void PossessedBy(AController* NewController) override;
};
```
### Файл `EnCharacter.cpp`
```cpp
#include "EnCharacter.h"
// Sets default values
AEnCharacter::AEnCharacter(const FObjectInitializer& ObjectInitializer)
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
			const FString MannequinAsset = TEXT("/Script/Engine.SkeletalMesh'/Game/Characters/Mannequins/Meshes/SKM_Manny.SKM_Manny'");
			const TSoftObjectPtr<USkeletalMesh> SKMesh(MannequinAsset);
			if (!SKMesh.IsNull()) {
				MeshComp->SetSkeletalMesh(SKMesh.LoadSynchronous());
			}
			// Load ainm instance as well
			// since we loading blueprint class, add _C at the end of the asset refrence
			const FString AnimationInstanceAsset = TEXT("/Script/Engine.AnimBlueprint'/Game/Characters/Mannequins/Animations/ABP_Manny.ABP_Manny_C'");
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
		const FString DefaultMappingContextAsset = TEXT("/Script/EnhancedInput.InputMappingContext'/Game/ThirdPerson/Input/IMC_Default.IMC_Default'");
		MappingContext = UKismetSystemLibrary::MakeSoftObjectPath(DefaultMappingContextAsset);
	}
}
// Called when the game starts or when spawned
void AEnCharacter::BeginPlay()
{
	Super::BeginPlay();
	if (Controller != nullptr) {
		BindContext(MappingContext.LoadSynchronous(), Controller); // bind input context
	}
	else
	if (RecivePlayerInputWitoutPossess) {
		// get player controller from the world
		APlayerController* PlayerController = UGameplayStatics::GetPlayerController(GetWorld(), 0);
		EnableInput(PlayerController); // thiw sill allow player inputs to work on this actor
		BindContext(MappingContext.LoadSynchronous(), PlayerController); // bind input context to player controller
		// set controller refrence. IHIS STEP IS VERY IMPORTANT
		Controller = PlayerController;
		// finalise
		SetupPlayerInputComponent(PlayerController->InputComponent);
	}
}
// will bind new mapping context
void AEnCharacter::BindContext(TSoftObjectPtr<UInputMappingContext> Context, AController* OnController)
{
	if (Context == nullptr) {
		Context = MappingContext;
	}
	if(Context == nullptr) return; // only because i don't want to crash app with check()
	// if OnController dosen't set - use default GetController()
	AController* CurrentContoller = OnController == nullptr ? GetController() : OnController;
	// cast controller to the PlayerController, because we need LocalPlayerSubsystem access
	APlayerController* PlayerController = Cast<APlayerController>(CurrentContoller);
	if (PlayerController == nullptr) return;
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
			EnhancedInputComponent->BindAction(Action, ETriggerEvent::Triggered, this, &ACharacter::Jump);
			EnhancedInputComponent->BindAction(Action, ETriggerEvent::Completed, this, &ACharacter::StopJumping);
		}
		// Moving
		if (AName.Contains(TEXT("Move"))) {
			EnhancedInputComponent->BindAction(Action, ETriggerEvent::Triggered, this, &AEnCharacter::Move);
		}
		// Sprinting
		if (AName.Contains(TEXT("Sprint"))) {
			EnhancedInputComponent->BindAction(Action, ETriggerEvent::Triggered, this, &AEnCharacter::StartSprint);
			EnhancedInputComponent->BindAction(Action, ETriggerEvent::Completed, this, &AEnCharacter::EndSprint);
		}
		// Looking around
		if (AName.Contains(TEXT("Look"))) {
			EnhancedInputComponent->BindAction(Action, ETriggerEvent::Triggered, this, &AEnCharacter::Rotate);
		}
	}
}
// On Possessed
void AEnCharacter::PossessedBy(AController* NewController)
{
	// Enable Camera when posessed
	if (NewController->IsLocalPlayerController()) {
		FollowCamera->Activate();
	}
}
// Start Sprint
void AEnCharacter::StartSprint(const FInputActionValue& Value)
{
	UCharacterMovementComponent* CharMoveComp = GetCharacterMovement();
	CharMoveComp->MaxWalkSpeed = 500.f;
}
// End Sprint
void AEnCharacter::EndSprint(const FInputActionValue& Value)
{
	UCharacterMovementComponent* CharMoveComp = GetCharacterMovement();
	CharMoveComp->MaxWalkSpeed = 250.f;
}
// Movement Input
void AEnCharacter::Move(const FInputActionValue& Value)
{
	// input is a Vector2D
	FVector2D MovementVector = Value.Get<FVector2D>();
	if (Controller != nullptr)
	{
		// find out which way is forward
		const FRotator Rotation = Controller->GetControlRotation();
		const FRotator YawRotation(0, Rotation.Yaw, 0);
		// get forward vector
		const FVector ForwardDirection = FRotationMatrix(YawRotation).GetUnitAxis(EAxis::X);
		// get right vector
		const FVector RightDirection = FRotationMatrix(YawRotation).GetUnitAxis(EAxis::Y);
		// add movement
		AddMovementInput(ForwardDirection, MovementVector.Y);
		AddMovementInput(RightDirection, MovementVector.X);
	}
}
// Roation Input
void AEnCharacter::Rotate(const FInputActionValue& Value)
{
	// input is a Vector2D
	FVector2D LookAxisVector = Value.Get<FVector2D>();
	// this methods won't work without possess anyway
	if(IsPlayerControlled()){
		AddControllerYawInput(LookAxisVector.X);
		AddControllerPitchInput(LookAxisVector.Y);
	}
}
// Called every frame
void AEnCharacter::Tick(float DeltaTime)
{
	Super::Tick(DeltaTime);
	// Since AddControllerYawInput and AddControllerPitchInput dosen't work without "Posess", i have to copy rotation directly
	if (InputEnabled()) {
		ACharacter* PLCharacter = UGameplayStatics::GetPlayerCharacter(GetWorld(), 0);
		if (PLCharacter != this) {
			SetActorRotation(PLCharacter->GetActorRotation());
		}
	}
}
// Called to bind functionality to input
void AEnCharacter::SetupPlayerInputComponent(UInputComponent* PlayerInputComponent)
{
	Super::SetupPlayerInputComponent(PlayerInputComponent);
}
```
### Принцип работы
В коде есть переменная `RecivePlayerInputWitoutPossess` которая будет видна в персонаже лишь на панели `Details`. Выставим ее в `true`.
![28507df7fb98b8f04cd6a23713fd0fee.png](../images/28507df7fb98b8f04cd6a23713fd0fee.png)
Добавим 2 или 3 персонажа типа `BP_EnCharacter` на уровень.
![dda32fe1f964542ddf26e847e8c95692.png](../images/dda32fe1f964542ddf26e847e8c95692.png)
Если запустить проект, то мы увидим, как добавленные на сцену персонажи следуют за игроком, копируя все его действия, включая вращение.
При этом на персонажах нет никакого ИИ. Персонажи управляются за счет копирования событий ввода.
![f30d80508bedd420a29b1e4075e6ed21.png](../images/f30d80508bedd420a29b1e4075e6ed21.png)
Таким способом можно управлять целыми группами песонажей, отключая их ии, когда они рядом, и включая ии, когда они далеко.
![8637090b4d113b2b1f448633ba11c74a.png](../images/8637090b4d113b2b1f448633ba11c74a.png)
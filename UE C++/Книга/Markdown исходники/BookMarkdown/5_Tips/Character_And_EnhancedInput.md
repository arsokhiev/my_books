# Character Boilerplate with Enhanced Input Setup
##`CoreCharacter.h`
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
##`CoreCharacter.h`
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
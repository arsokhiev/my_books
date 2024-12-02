# Полезные сниппеты `Slate`
В ходе разработки я вывел несколько крайне полезных сниппетов, для работы со `Slate`.
## Simple Attribute Boilerplate
```cpp
//.h
#pragma once
#include "CoreMinimal.h"
#include "Widgets/SCompoundWidget.h"
/**
 * Slate Widget boilerplate with attribute setup
 */
class SLATERUNTIMEMODULE_API SWidgetBoilerplate : public SCompoundWidget
{
public:
	SLATE_BEGIN_ARGS(SWidgetBoilerplate):
		_NumberAttribute(0)
	{}
		SLATE_ATTRIBUTE(float, NumberAttribute)
	SLATE_END_ARGS()
	// constructor used to set attribute default values
	SWidgetBoilerplate():
		NumberAttribute(*this, 0)
	{}
	/** Constructs this widget with InArgs */
	void Construct(const FArguments& InArgs);
	// attribute setter
	void SetNumberAttribute(TAttribute<float> inNumber) {
		NumberAttribute.Assign(*this, inNumber);
	}
	// attribute getter
	float GetNumberAttribute() {
		return NumberAttribute.Get();
	}
private:
	// attributes
	TSlateAttribute<float,EInvalidateWidgetReason::Paint> NumberAttribute;
};
//...
//.cpp
#include "SWidgetBoilerplate.h"
#include "SlateOptMacros.h"
BEGIN_SLATE_FUNCTION_BUILD_OPTIMIZATION
void SWidgetBoilerplate::Construct(const FArguments& InArgs)
{
	// sync values
	SetNumberAttribute(InArgs._NumberAttribute);
	/*
	ChildSlot
	[
		// Populate the widget
	];
	*/
}
END_SLATE_FUNCTION_BUILD_OPTIMIZATION
```
## Complex Attribute Boilerplate
```cpp
//.h
#pragma once
#include "CoreMinimal.h"
#include "Widgets/SCompoundWidget.h"
/**
 * Slate Widget boilerplate with attribute setup
 */
class SLATERUNTIMEMODULE_API SWidgetBoilerplate : public SCompoundWidget
{
	SLATE_DECLARE_WIDGET(SWidgetBoilerplate, SCompoundWidget)
public:
	SLATE_BEGIN_ARGS(SWidgetBoilerplate):
		_NumberAttribute(0)
	{}
		SLATE_ATTRIBUTE(float, NumberAttribute)
	SLATE_END_ARGS()
	// constructor used to set attribute default values
	SWidgetBoilerplate():
		NumberAttribute(*this, 0)
	{}
	/** Constructs this widget with InArgs */
	void Construct(const FArguments& InArgs);
	// attribute setter
	void SetNumberAttribute(TAttribute<float> inNumber) {
		NumberAttribute.Assign(*this, inNumber);
	}
	// attribute getter
	float GetNumberAttribute() {
		return NumberAttribute.Get();
	}
	// updater
	void UpdateNumberAttribute() {
		float AttributeValue = GetNumberAttribute();
		// do something with value
	}
private:
	// attributes
	TSlateAttribute<float> NumberAttribute;
};
//...
//.cpp
#include "SWidgetBoilerplate.h"
#include "SlateOptMacros.h"
BEGIN_SLATE_FUNCTION_BUILD_OPTIMIZATION
SLATE_IMPLEMENT_WIDGET(SWidgetBoilerplate)
void SWidgetBoilerplate::PrivateRegisterAttributes(FSlateAttributeInitializer& AttributeInitializer)
{
	SLATE_ADD_MEMBER_ATTRIBUTE_DEFINITION_WITH_NAME(AttributeInitializer, "NumberAttribute", NumberAttribute, EInvalidateWidgetReason::Paint)
		.OnValueChanged(FSlateAttributeDescriptor::FAttributeValueChangedDelegate::CreateLambda([](SWidget& Widget)
			{
				SWidgetBoilerplate& inWidget = static_cast<SWidgetBoilerplate&>(Widget);
				// do something with widget
				inWidget.UpdateNumberAttribute();
			})
		);
	// or simple, since our
	// TSlateAttribute<float,EInvalidateWidgetReason::Paint> NumberAttribute
	// and SLATE_ATTRIBUTE(float, NumberAttribute)
	// share same name
	//SLATE_ADD_MEMBER_ATTRIBUTE_DEFINITION(AttributeInitializer, NumberAttribute, EInvalidateWidgetReason::Paint);
}
void SWidgetBoilerplate::Construct(const FArguments& InArgs)
{
	// sync values
	SetNumberAttribute(InArgs._NumberAttribute);
	/*
	ChildSlot
	[
		// Populate the widget
	];
	*/
}
END_SLATE_FUNCTION_BUILD_OPTIMIZATION
```
## Style Set Boilerplate
```cpp
//.h
#pragma once
#include "Styling/ISlateStyle.h"
#include "Styling/SlateStyle.h"
#include "Styling/SlateStyleRegistry.h" // to register our styles in
#include "Brushes/SlateImageBrush.h" // Image Brush
// Core Style include files
#include "Styling/CoreStyle.h"
#include "Styling/SlateTypes.h"
#include "Styling/AppStyle.h"
// Style set - collection of slate styles
// Just static class, like it's done here - FProfilerStyle
//\Engine\Source\Developer\Profiler\Private\ProfilerStyle.h
class FStyleSetBoilerplate
	: public FSlateStyleSet
{
public:
	// style set name
	virtual const FName& GetStyleSetName() const override;
	// here we will store our style instance in Inst variable
	static const FStyleSetBoilerplate& Get();
	// here we will unregister our style instance
	static void Shutdown();
	// destructor
	~FStyleSetBoilerplate();
	// this method should form relative path to assets of the plugin / module
	// idea taken from \Plugins\Runtime\StateTree\Source\StateTreeEditorModule\Private\StateTreeEditorStyle.cpp
	// (it's new Mass based Ai State Tree)
	static FString InContent(const FString& RelativePath, const ANSICHAR* Extension) {
		// this is how it should work with plugins:
		//static FString ContentDir = IPluginManager::Get().FindPlugin(TEXT("StateTreeEditorModule"))->GetContentDir() / TEXT("Slate");
		//return (ContentDir / RelativePath) + Extension;
		// since we work with module:
		static FString ContentDir = FPaths::GameSourceDir() + TEXT("SlateRuntimeModule/Resources/");
		return (ContentDir / RelativePath) + Extension;
	}
private:
	// constructor
	// here we gonna make our styles and load variable Inst
	FStyleSetBoilerplate();
	static FName StyleName;
	static TUniquePtr<FStyleSetBoilerplate> Inst;
};
//...
//.cpp
#include "Styles/StyleSetBoilerplate.h"
// note* static members of class should be ALVAYS initialized with default value
// Even if they are in the Private scope (which i did not know)
// static initialization should be outside of the class
// or you will get following error:
// unresolved external symbol "private: static class...etc"
TUniquePtr<FStyleSetBoilerplate> FStyleSetBoilerplate::Inst = nullptr;
// static for teh style name
FName FStyleSetBoilerplate::StyleName = TEXT("SlateModuleBoilerplateStyles");
// constructor
// here we gonna make our styles and load variable Inst
FStyleSetBoilerplate::FStyleSetBoilerplate() : FSlateStyleSet(StyleName)
{
	// first set the Parent Style
	// Parent Style always loads before our style
	// Inheriting Parent Style will allow us to use all styles, defined in it as our own class styles
	// so, for example "Icons.Cross" defined in core style - FCoreStyle::Get().GetBrush("Icons.Cross")
	// will be accessible thru - FStyleSetBoilerplate::Get().GetBrush("Icons.Cross")
	// if we inherit from the CoreStyle (and we will do so)
	//*note: since FCoreStyle::StyleName dosen't exist(it's very old class), instead just use string, it will work
	//*note2: CoreStyle can be found here - \Source\Runtime\SlateCore\Private\Styling\CoreStyle.cpp
	SetParentStyleName("CoreStyle");
	//*note: if you don't want all CoreStyle styles - use this instead:
	//SetParentStyleName(FAppStyle::GetAppStyleSetName());
	// this will allow to work with: RootToContentDir(TEXT("file_name"), TEXT(".extension")) to refrence content in the "Root" folder
	// as "Root" we use FPaths::EngineContentDir() / TEXT("Slate"), because same walue is used in CoreStyle, which is our parent style
	// you can find this directory in engine installataion folder - \Engine\Content\Slate
	SetContentRoot(FPaths::EngineContentDir() / TEXT("Slate"));
	// this will allow to work with: RootToCoreContentDir(TEXT("file_name"), TEXT(".extension")) to refrence content in the "Root" folder
	// as "Root" we use FPaths::EngineContentDir() / TEXT("Slate"), because same walue is used in CoreStyle, which is our parent style
	// you can find this directory in engine installataion folder - \Engine\Content\Slate
	SetCoreContentRoot(FPaths::EngineContentDir() / TEXT("Slate"));
	// --- common variables ---
	// we can refrence files, that are not imported into project's filesystem, using direct links
	const FString BitterLightFontPath = InContent(TEXT("Fonts/Bitter-Light"), ".ttf"); // will search for file Bitter-Light.ttf in Resources / Fonts folder of the module
	const FString BitterBlackFontPath = InContent(TEXT("Fonts/Bitter-Black"), ".ttf");
	// if you refrencing assets imported inside game project - preload them first
	FString MaterialPath = TEXT("/Game/Characters/UI/UiMaterial_Inst.UiMaterial_Inst");
	UMaterialInstance* SlotMaterial = LoadObject<UMaterialInstance>(NULL, *MaterialPath);
	// --- styles ---
	// custom brushes
	{
		// some custom brushes
		// This brush image loaded from module's Resource folder
		Set("Brushes:SlotImageBrush", new FSlateBoxBrush{ // Box Brush projection
									*InContent(TEXT("InventorySlot_Icon"), ".png"), // will return path of InventorySlot_Icon from modeule Resources folder
									FVector2D{0.4f}, // margins on box and border brush projections work differenty, check it in umg widgets editor
									FSlateColor(FLinearColor::White) // tint
			});
		// This brush loaded from folder that is set as ContentRoot
		Set("Brushes:SlotContentBrush", new FSlateBorderBrush{ // Border Brush Projection
									RootToContentDir(TEXT("Common/BoxShadow"), TEXT(".png")), // will search image in folder we set in SetContentRoot()
									FVector2D{0.4f}, // margins on box and border brush projections work differenty, check it in umg widgets editor
									FSlateColor(FLinearColor::White) // tint
			});
		// This brush uses material as it's value
		Set("Brushes:SlotMaterialBrush", new FSlateImageBrush{
									SlotMaterial,
									FVector2D{50.f, 50.f},
									FLinearColor::White,
									ESlateBrushTileType::NoTile // image brushes can be tiled, but since it's just material - there is no meaning of tiling at all
			});
		Set("Brushes:InventoryIcon", new FSlateImageBrush{
									*InContent(TEXT("ThreeLinesIcon"), ".png"),
									FVector2D{100.f, 100.f},
									FLinearColor::White,
									ESlateBrushTileType::NoTile // image brushes can be tiled, but since it's just material - there is no meaning of tiling at all
			});
		// This brush will draw rounded box
		Set("Brushes:RoundedDark", new FSlateRoundedBoxBrush{
										FLinearColor::Black, // background
										FLinearColor::Red, // outline
										1.0f, // outline size
										FVector2D{50.f, 50.f} // image size
			});
		// just color brush
		Set("Brushes:Dark", new FSlateImageBrush{
									"Brush:DarkColorBrush",
									FVector2D{},
									FLinearColor{0.0f, 0.0f, 0.0f, 0.7f}
			});
		// This brush loads font Bitter-Light.ttf from Resources folder of the module
		Set("Fonts:BitterLight16", FSlateFontInfo(BitterLightFontPath, 16));
		// This brush loads font Bitter-Black.ttf from Resources folder of the module		
		Set("Fonts:BitterBlack32", FSlateFontInfo(BitterBlackFontPath, 32));
	}
	// override existing style
	{
		// this way we can make custom TextBlock
		// big text
		const FTextBlockStyle TBStyleY = FTextBlockStyle()
			.SetFont(FSlateFontInfo(BitterBlackFontPath, 32))
			.SetColorAndOpacity(FLinearColor::White)
			;
		Set("Controls:TextBlock32Big", TBStyleY); // store style
		// basic text
		const FTextBlockStyle TBStyle = FTextBlockStyle()
			.SetFont(FSlateFontInfo(BitterBlackFontPath, 24))
			.SetColorAndOpacity(FLinearColor::White)
			;
		Set("Controls:TextBlock24", TBStyle); // store style
		// buttons:
		// slightly blue
		FSlateBrush ButtonBrush = FButtonStyle::GetDefault().Normal;
		ButtonBrush.TintColor = FLinearColor(0.7f, 0.0f, 0.f, 1.f);
		const FButtonStyle BStyleBlue = FButtonStyle()
			.SetNormal(ButtonBrush)
			;
		Set("Controls:BlueButton", BStyleBlue); // store style
		// slightly green
		ButtonBrush.TintColor = FLinearColor(0.f, 0.0f, 7.f, 1.f);
		const FButtonStyle BStyleGreen = FButtonStyle()
			.SetNormal(ButtonBrush)
			;
		Set("Controls:GreenButton", BStyleGreen); // store style
	}
	// register style
	FSlateStyleRegistry::RegisterSlateStyle(*this);
}
// destructor
FStyleSetBoilerplate::~FStyleSetBoilerplate()
{
	// unregister style
	FSlateStyleRegistry::UnRegisterSlateStyle(*this);
}
const FName& FStyleSetBoilerplate::GetStyleSetName() const
{
	return StyleName;
}
// initialize static pointer
const FStyleSetBoilerplate& FStyleSetBoilerplate::Get()
{
	if (!Inst.IsValid())
	{
		// make class instance and store is as static
		// *note - this also calls constructor, where RegisterSlateStyle is called
		Inst = TUniquePtr<FStyleSetBoilerplate>(new FStyleSetBoilerplate());
	}
	return *(Inst.Get());
}
void FStyleSetBoilerplate::Shutdown()
{
	// clear unique pointer
	// *note - this will also call destructor, where UnRegisterSlateStyle is called
	Inst.Reset();
}
```
Регистрация осуществляется в конструкторе модуля, удаление - в деструкторе модуля.
```cpp
//.h
#pragma once
#include "Modules/ModuleManager.h"
DECLARE_LOG_CATEGORY_EXTERN(SlateRuntimeModule, All, All);
class FSlateRuntimeModule : public IModuleInterface
{
	public:
	/* Called when the module is loaded */
	virtual void StartupModule() override;
	/* Called when the module is unloaded */
	virtual void ShutdownModule() override;
};
//...
//.cpp
#include "SlateRuntimeModule.h"
#include "Styles/StyleSetBoilerplate.h" // StyleSet to register
#include "Slate/SlateGameResources.h"
DEFINE_LOG_CATEGORY(SlateRuntimeModule);
#define LOCTEXT_NAMESPACE "FSlateRuntimeModule"
void FSlateRuntimeModule::StartupModule()
{
	// register style set
	FStyleSetBoilerplate::Get();
}
void FSlateRuntimeModule::ShutdownModule()
{
    // unregister style set
	FStyleSetBoilerplate::Shutdown();
}
#undef LOCTEXT_NAMESPACE
IMPLEMENT_MODULE(FSlateRuntimeModule, SlateRuntimeModule)
```
## Передача в `Slate` класса `APlayerController`
Делается через атрибуты.
```cpp
//.h
class SClassConstructor : public SCompoundWidget
{
	SLATE_DECLARE_WIDGET(SClassConstructor, SCompoundWidget)
    public:
    SLATE_BEGIN_ARGS(SGameWindow)
	{}
    // Local Player Attribute
    SLATE_ATTRIBUTE(APlayerController*, PlayerController)
	SLATE_END_ARGS()
 
    SClassConstructor()
        // Attribute Default Values
        : PlayerControllerAttribute(*this, nullptr)
        {}
    private:
    // setter for PlayerControllerAttribute
	void SetNewPlayerController(TAttribute<APlayerController*> LPlayer){
		PlayerControllerAttribute.Assign(*this, LPlayer);
	}
	// getter for PlayerControllerAttribute
	APlayerController* GetPlayerController() const {
		return PlayerControllerAttribute.Get();
	}
	// will return FSlateUser user object from current LocalPlayer
	FSlateUser* GetSlateUser() {
		if (GetPlayerController() != nullptr) {
			return FSlateApplication::Get().GetUser(GetPlayerController()->GetLocalPlayer()->GetControllerId()).Get();
		}
		return nullptr;
	}
 
    TSlateAttribute<APlayerController*> PlayerControllerAttribute;
}
//...
//.cpp
BEGIN_SLATE_FUNCTION_BUILD_OPTIMIZATION
SLATE_IMPLEMENT_WIDGET(SGameWindow)
void SGameWindow::PrivateRegisterAttributes(FSlateAttributeInitializer& AttributeInitializer)
{
	SLATE_ADD_MEMBER_ATTRIBUTE_DEFINITION_WITH_NAME(AttributeInitializer, "PlayerController", PlayerControllerAttribute, EInvalidateWidgetReason::Paint);
}
```
## Получение доступа к `SlateUser`
Объект `FSlateUser` позволяет получить доступ к положению курсора, что бывает Очень важно при перетаскивании объектов по экрану.
```cpp
// will return FSlateUser user object from current LocalPlayer
FSlateUser* GetSlateUser() {
    if (GetPlayerController() != nullptr) {
        return FSlateApplication::Get().GetUser(GetPlayerController()->GetLocalPlayer()->GetControllerId()).Get();
    }
    return nullptr;
}
```
Получение позиции курсора в виджете:
```cpp
FSlateUser* User = GetSlateUser();
InitialMousePosition = User->GetCursorPosition();
```
## Привязка к событиям прямо внутри виджета
Иногда надо отследить то или иное нажатие клавиши в определенных состояниях интерфейса. Если у нас есть доступ к объекту `APlayerController`, то проблем не возникнет:
```cpp
FReply SGameWindow::OnTitleMouseButtonDown(const FGeometry& MyGeometry, const FPointerEvent& MouseEvent)
{
	APlayerController* Controller = GetPlayerController();
	if (Controller == nullptr) {
		return FReply::Unhandled();
	}
	//UE_LOG(LogTemp, Warning, TEXT("[SGameWindow] Title Mouse Button Down Key = %s"), *MouseEvent.GetEffectingButton().ToString())
	// Bind to same key event "key Released" to stop window drag
	// this is necessary if user released "drag mouse button" outside of the window title
	FInputKeyBinding ReleasedBinding = FInputKeyBinding(MouseEvent.GetEffectingButton(), EInputEvent::IE_Released);
	//ReleasedBinding.KeyDelegate.GetDelegateForManualSet().BindRaw(this,&SGameWindow::StopWindowDrag);
	ReleasedBinding.KeyDelegate.GetDelegateForManualSet().BindLambda([this]() {
			StopWindowDrag(); // stop drag
		});
	Controller->InputComponent->KeyBindings.Emplace(MoveTemp(ReleasedBinding));
	StartWindowDrag(); // will start drag
	return FReply::Handled();
}
```
## Приведение типа для виджетов `Slate`
Делается через `static_cast` или `StaticCastSharedPtr / StaticCastSharedRef`. Поскольку операция `static_cast` не является типобезопасной, у каждого виджета есть метод `GetType`.
Вариант 1:
```cpp
FChildren* ChildWingets = Canvas->GetChildren();
SWidget& ChildWidget = ChildWingets->GetChildAt(0);
if (ChildWidget.GetType() == "SGameWindow") {
    SGameWindow* GameWindow = static_cast<SGameWindow*>(&ChildWidget);
    res.Add(GameWindow);
}
```
Вариант 2:
```cpp
if (ChildWingets->GetChildAt(i)->GetType() == "SGameWindow") {
    TSharedPtr<SGameWindow> GameWindow = StaticCastSharedRef<SGameWindow>(ChildWingets->GetChildAt(0));
    res.Add(GameWindow.Get());
}
```
## Получение списка виджетов заданного типа внутри `SConstraintCanvas`
На примере `SGameWindow`.
```cpp
// will search all SGameWindow objects on the parent canvas
TArray<SGameWindow*> GetAllGameWindowsOnCanvas() {
    SConstraintCanvas* Canvas = GetParentCanvas();
    if (Canvas == nullptr) {
        return {}; // we are not in the canvas
    }
    TArray<SGameWindow*> res;
    // in loop check every canvas slot widget
    FChildren* ChildWingets = Canvas->GetChildren();
    for (int i = 0; i < ChildWingets->Num(); i++) {
        SWidget& ChildWidget = ChildWingets->GetChildAt(i).Get();
        //SConstraintCanvas::FSlot* Slot = (SConstraintCanvas::FSlot*)(&ChildWingets->GetSlotAt(i));
        if (ChildWidget.GetType() == "SGameWindow") {
            SGameWindow* GameWindow = static_cast<SGameWindow*>(&ChildWidget);
            //StaticCastSharedPtr
            res.Add(GameWindow);
        }
    }
    return res;
}
```
## Получение ссылки на слот `SConstraintCanvas::FSlot` для текущего виджета
Доступ к слоту, в котором находится текущий виджет, может быть использован для изменения якорей `Anchors` этого слота
```cpp
// will return FSlot object, related to our Window on SConstraintCanvas
SConstraintCanvas::FSlot* SGameWindow::GetCanvasSlot(){
	SConstraintCanvas* Canvas = GetParentCanvas();
	if (Canvas == nullptr) {
		return nullptr; // we are not in the canvas
	}
	// in loop check every canvas slot widget
	FChildren* ChildWingets = Canvas->GetChildren();
	for (int i = 0; i < ChildWingets->Num(); i++) {
		SWidget& ChildWidget = ChildWingets->GetChildAt(i).Get();
		SConstraintCanvas::FSlot* Slot = (SConstraintCanvas::FSlot*)(&ChildWingets->GetSlotAt(i));
		if (&(Slot->GetWidget().Get()) == this) { // if slot widget is our current window - then slot is our window slot
			return Slot; // return found slot
		}
	}
	return nullptr; // nothing found
}
```
Якоря меняются так:
```cpp
SConstraintCanvas::FSlot* Slot = GetCanvasSlot();
Slot->SetAnchors(TargetAnchors);
```
## Получение объекта на самом первом слое в `SConstraintCanvas`
Иногда надо узнать, какой объект находится выше всех остальных по `ZOrder`. Делается это так:
```cpp
// will return FSlot object, related to our Window on SConstraintCanvas
int32 SGameWindow::GetHighestZOrder(){
	SConstraintCanvas* Canvas = GetParentCanvas();
	if (Canvas == nullptr) {
		return -1; // we are not in the canvas
	}
	int32 HighestZOrder = 0;
	// in loop check every canvas slot widget
	FChildren* ChildWingets = Canvas->GetChildren();
	for (int i = 0; i < ChildWingets->Num(); i++) {
		SWidget& ChildWidget = ChildWingets->GetChildAt(i).Get();
		SConstraintCanvas::FSlot* Slot = (SConstraintCanvas::FSlot*)(&ChildWingets->GetSlotAt(i));
		if (Slot->GetZOrder() > HighestZOrder) {
			HighestZOrder = Slot->GetZOrder();
		}
	}
	// take care of int size limits. It's impossible to reach them, using widgets because your pc memory will fill up, but...
	if (HighestZOrder == INT_MAX - 1) {
		return -1;
	}
	return HighestZOrder;
}
```
## Захват ввода клавиатуры
Для начала надо переопределить метод `SupportsKeyboardFocus`:
```cpp
// Events like OnKeyDown, OnKeyUp and all others related to keyboard won't work without this
virtual bool SupportsKeyboardFocus() const override { return true; };
```
Далее мы можем переопределить метод `OnKeyDown`:
```cpp
//.h
virtual FReply OnKeyDown(const FGeometry& MyGeometry, const FKeyEvent& InKeyEvent) override;
//...
//.cpp
FReply SGameWindow::OnKeyDown(const FGeometry& MyGeometry, const FKeyEvent& InKeyEvent)
{
	UE_LOG(LogTemp, Warning, TEXT("[SGameWindow] KeyDown Key = %s"), *InKeyEvent.GetKey().ToString())
	return FReply::Handled();
}
```
Теперь, если щелкнуть на виджете и начать печатать - вывод клавиатуры будет захвачен.
![c774ea00b1fce97048ba765ecee3cb9b.png](../images/c774ea00b1fce97048ba765ecee3cb9b.png)
## Вывод виджета на первый план и передача ему фокуса ввода
Захват фокуса ввода делается так:
```cpp
// will set keyboard focus to current window
void SGameWindow::SetFocusToWindow() {
	APlayerController* Controller = GetPlayerController();
	if (Controller == nullptr) {
		return;
	}
	FWidgetPath PathToWidget;
	FSlateApplication::Get().FindPathToWidget(this->AsShared(), PathToWidget, EVisibility::Visible);
	// Apply user focus
	FSlateApplication::Get().SetUserFocus(Controller->GetLocalPlayer()->GetControllerId(), PathToWidget, EFocusCause::Mouse);
}
```
Тут используется метод `FindPathToWidget`, который задает переменную `PathToWidget`.
```cpp
FWidgetPath PathToWidget;
FSlateApplication::Get().FindPathToWidget(this->AsShared(), PathToWidget, EVisibility::Visible);
```
Выведение виджета на первый план, с передачей ему фокуса ввода, в моем случае, осуществляется по событию `OnMouseEnter`.
```cpp
//.h
virtual void OnMouseEnter(const FGeometry& MyGeometry, const FPointerEvent& MouseEvent) override;
//...
//.cpp
void SGameWindow::OnMouseEnter(const FGeometry& MyGeometry, const FPointerEvent& MouseEvent)
{
	// don't clear focus if any of window on the canvas currently resized or dragged
	TArray<SGameWindow*> GameWindows = GetAllGameWindowsOnCanvas();
	for (SGameWindow* Wnd : GameWindows) {
		if (Wnd->isDrag() || Wnd->isResize()) {
			return;
		}
	}
	// set focus to window under mouse cursor
	if (GetPlayerController() != nullptr && FSlateApplication::IsInitialized() && SupportsKeyboardFocus()) {
		SetFocusToWindow(); // Set focus to widget once mouse is entered it
		//UE_LOG(LogTemp, Error, TEXT("[SGameWindow] Parent Widget = %s"), *this->GetParentWidget()->ToString())
	}
	// move currently working window to top of the canvas z-order
	int HOrder = GetHighestZOrder();
	if (HOrder != -1) {
		GetCanvasSlot()->SetZOrder(GetHighestZOrder() + 1);
	}
	//UE_LOG(LogTemp, Error, TEXT("[SGameWindow] Mouse Entered"))
}
```
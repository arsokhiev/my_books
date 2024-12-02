# Изменение GameInputMode

![e0516fa4027e8f11ce1ab20611b2aee6.png](../images/e0516fa4027e8f11ce1ab20611b2aee6.png)

Делается так:
```cpp
#include "Kismet/GameplayStatics.h"
///
// Simple:
APlayerController* Controller = UGameplayStatics::GetPlayerController(this, 0);
bool bLock = true;
// update input mode
FInputModeGameAndUI Mode;
Mode.SetHideCursorDuringCapture(true);
if (bLock) {
    Mode.SetLockMouseToViewportBehavior(EMouseLockMode::LockAlways);
}
else {
    Mode.SetLockMouseToViewportBehavior(EMouseLockMode::DoNotLock);
}
Controller->SetInputMode(MoveTemp(Mode));
Controller->SetShowMouseCursor(true);
Controller->bEnableClickEvents = 1;
Controller->bEnableMouseOverEvents = 1;
```
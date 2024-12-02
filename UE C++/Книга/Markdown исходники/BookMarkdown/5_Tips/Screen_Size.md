# Размер экрана
```cpp
#include "Kismet/GameplayStatics.h"
APlayerController* PController = UGameplayStatics::GetPlayerController(this, 0);
int XVSize = 0;
int YVSize = 0;
PController->GetViewportSize(XVSize, YVSize);
```
## Как отследить изменение размера экрана
```cpp
// this is how to track viewport resize if necessary
GEngine->GameViewport->Viewport->ViewportResizedEvent.AddLambda([this](FViewport* Viewport, uint32 Unused)->void {
    UE_LOG(LogTemp, Warning, TEXT("Viewport Resized!"))
});
```
# Запуск консольных команд
Есть два способа. Для запуска локальных команд, требующих доступа к контроллеру персонажа используется такой код:
```cpp
APlayerController* PController = UGameplayStatics::GetPlayerController(this, 0);
PController->ConsoleCommand("showdebug");
```
Для запуска глобальных команд, не требующих доступа к контроллеру персонажа:
```cpp
GEngine->Exec(GetWorld(), TEXT("stat fps"));
```
## Links
https://forums.unrealengine.com/t/how-do-i-run-a-console-command-in-c/317988/5
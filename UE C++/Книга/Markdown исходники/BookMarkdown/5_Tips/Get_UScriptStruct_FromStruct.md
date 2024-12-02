# `::UScriptStruct` из встроенной структуры
Получить значение метода `::StaticStruct()` для  структур типа `FVector` и его подвидов, а так-же для `FRotator` невозможно, потому что они не реализуют этот метод.
Надо делать так:
```cpp
UScriptStruct* VectorStruct = TBaseStructure<FVector>::Get(); // well... i'd never expected THIS
UScriptStruct* RotatorStruct = TBaseStructure<FRotator>::Get();
```
Значение типа `UScriptStruct` можно отправлять полям, которые требуют для себя `::StaticStruct()` или `::StaticClass()`
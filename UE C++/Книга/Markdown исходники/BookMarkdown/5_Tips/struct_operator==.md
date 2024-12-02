# `struct` и оператор `==`
Оператор `==` должен быть перегружен в каждой структуре, которую разработчик собирается использовать вместе с контейнерными типами, например `TArray` или `TMap`. Так-же подобный оператор может быть нужен в структурах, применяемых для виджетов `Slate`.
Вот как выглядит пример структуры с таким оператором:
```cpp
// This struct will hold actor and it's damage
struct FActorDamageInfo {
	AActor * Actor = nullptr;
	int DamageAmount = 0;
	FVector2D Offset = {}; // offset of damage number from actor's middle
	float Height = 0.0f;
	float Opacity = 0.0f;
	// oveload operator == because without it functions like TArray::IndexOfByKey will not work
	bool operator==(const FActorDamageInfo& Other) const {
		bool result = true;
		// use simple AND
		result &= Actor == Other.Actor;
		result &= DamageAmount == Other.DamageAmount;
		result &= Offset == Other.Offset;
		result &= Height == Other.Height;
		result &= Opacity == Other.Opacity;
		return result;
	}
};
```
или
```cpp
// struct to hold slot border parameters
struct FSlotBorderSettings {
    bool bShowBorder = true;
    FMargin Padding = {5.f, 5.f};
    const FSlateBrush* BorderImageBrush;
    // oveload operator == because without it functions like TArray::IndexOfByKey will not work
    bool operator==(const FSlotBorderSettings& Other) const {
        bool result = true;
        // use simple AND
        result &= bShowBorder == Other.bShowBorder;
        result &= Padding == Other.Padding;
        result &= BorderImageBrush == Other.BorderImageBrush;
        return result;
    }
};
```
# `UEnum` to `FString`
Есть очень простой способ конвертировать переменную `UENUM` в `FString`:
```cpp
EEdGraphPinDirection Direction = EGPD_Input; // our enum
FString Value = UEnum::GetValueAsString(Direction) // will make string : "EGPD_Input"
```
Так-же вы можете использовать следующий шаблон, взятый из поста на [форуме](https://forums.unrealengine.com/t/conversion-of-enum-to-string/337869/22):
```cpp
/**
 * Convert the value of an enum to a string.
 *
 * @param EnumValue
 *	The enumerated type value to convert to a string.
 *
 * @return
 *	The key/name that corresponds to the value in the enumerated type.
 */
template<typename T>
FString EnumToString(const T EnumValue)
{
	FString Name = StaticEnum<T>()->GetNameStringByValue(static_cast<__underlying_type(T)>(EnumValue));
	check(Name.Len() != 0);
	return Name;
}
///...
EnumToString(EnumTypeName::EnumOnTheList);
```
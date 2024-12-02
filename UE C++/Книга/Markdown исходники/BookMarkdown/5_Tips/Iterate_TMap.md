# Iterate `TMap`
```cpp
TMap<FName, const UInputAction*> Actions;
for (const TPair<FName, const UInputAction*>& ActionInfo : Actions)
{
    //ActionInfo.Key;
    //ActionInfo.Value;
}
```
# Шаблон для печати типа переменной
Иногда надо напечатать тип переменной. Для этого есть два способа, простой и чуть сложнее.
Простой способ печати типа переменной выглядит так:
```cpp
int main(){
    const int ci = 0;
    std::cout << typeid(ci).name() << '\n';
    return 0;
}
```
![9c5ca6f03156b1bea08d128bead1aaac.png](../images/9c5ca6f03156b1bea08d128bead1aaac.png)
Главная проблема такого кода в том, что тип не печатается полностью. А это у нас - `const int`. Если вам нужен полный тип переменной, а иногда он бывает ОЧЕНЬ нужен, есть второй способ решения - использовать под эти задачи продвинутый шаблон, найденный мной на [stackoverflow](https://stackoverflow.com/a/20170989/2574125):
```cpp
#include <iostream>
#include <string_view>
template <typename T>
constexpr auto type_name() {
    std::string_view name, prefix, suffix;
#ifdef __clang__
    name = __PRETTY_FUNCTION__;
    prefix = "auto type_name() [T = ";
    suffix = "]";
#elif defined(__GNUC__)
    name = __PRETTY_FUNCTION__;
    prefix = "constexpr auto type_name() [with T = ";
    suffix = "]";
#elif defined(_MSC_VER)
    name = __FUNCSIG__;
    prefix = "auto __cdecl type_name<";
    suffix = ">(void)";
#endif
    name.remove_prefix(prefix.size());
    name.remove_suffix(suffix.size());
    return name;
}
int main(){
    const int ci = 0;
    std::cout << type_name<decltype(ci)>() << '\n';
    std::cout << type_name<decltype(std::move(ci))>() << '\n';
    return 0;
}
```

![da09a46504c45a4be7752adf87883d37.png](../images/da09a46504c45a4be7752adf87883d37.png)

Как вы видите, этот подход позволяет печатать даже `lvalue` и `rvalue` значения. Рекомендую добавить эту функцию в личную библиотеку для дальнейшего использования.
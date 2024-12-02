# Проблемы с файлом `.generated.h`
Иногда бывает так, что строчка `#include "***.generated.h"` подсвечивается красным.
![89c629f5e7c4750ada743448e66f2a59.png](../images/89c629f5e7c4750ada743448e66f2a59.png)
При этом никаких проблем с компиляцией проекта не возникает.
Что делать?
Файл `.generated.h` создается в соответствующей папке модуля - `\Intermediate\Build\Win64\UnrealEditor\Inc\[MODULE_NAME]\UHT`.
![fec0d76ce7ba71529f73473c8c6ca534.png](../images/fec0d76ce7ba71529f73473c8c6ca534.png)
Открываем, видим, что файл там. Но красное подчеркивание в VS не пропадает все равно. Это значит, что `VisualStudio` не подгружает файлы из данной папки.
Исправляем.
Идем в VS, `Project -> ProjectName.Properties`.
![cf2da69843d60201c165fb478984ccdf.png](../images/cf2da69843d60201c165fb478984ccdf.png)
Далее `Configuration -> All Configurations`.
![67a5298bacd7a49a79d91505a78af812.png](../images/67a5298bacd7a49a79d91505a78af812.png)
Секция `Nmake` строка `IncludeSearchPaths`.
![6af7d48c8c81d0834ad4ed4c9ad19c89.png](../images/6af7d48c8c81d0834ad4ed4c9ad19c89.png)
Щелкаем на выпадающий список -> `Edit`.
![f75ec27570244c184921e9baa368ee9d.png](../images/f75ec27570244c184921e9baa368ee9d.png)
Откроется окно редактирования путей. Щелкаем там иконку `NewLine`.
![3dfb325e2bfa6f96bde5a18b7b04e6c1.png](../images/3dfb325e2bfa6f96bde5a18b7b04e6c1.png)
Далее открываем папку `\Intermediate\Build\Win64\UnrealEditor\Inc`, тут мы видим список модулей, для которых `UHT` будет генерировать свои заголовки.
![cf31060b0d21665ce70d41c2186ae9e7.png](../images/cf31060b0d21665ce70d41c2186ae9e7.png)
Путь до каждого из этих модулей надо добавить в список `Include Search Path` в формате такого макроса:
```cpp
$(ProjectDir)..\Build\Win64\UnrealEditor\Inc\[ModuleName]\UHT\
```
В моем случае надо добавить такие строки:
```cpp
$(ProjectDir)..\Build\Win64\UnrealEditor\Inc\TestModule\UHT\
$(ProjectDir)..\Build\Win64\UnrealEditor\Inc\SubsystemTest\UHT\
$(ProjectDir)..\Build\Win64\UnrealEditor\Inc\ThreadsModule\UHT\
$(ProjectDir)..\Build\Win64\UnrealEditor\Inc\EnhancedCharacter\UHT\
$(ProjectDir)..\Build\Win64\UnrealEditor\Inc\CharactersModule\UHT\
```
![041790f7ada9b6b6bb0c007b5433b762.png](../images/041790f7ada9b6b6bb0c007b5433b762.png)
Жмем `OK`, жмем `Применить`, закрываем.
![107287cfd1c59f0a10f384cbfa01aa76.png](../images/107287cfd1c59f0a10f384cbfa01aa76.png)
Ошибка пропадает:
![2ac40776816faa66b044b29aa360d10a.png](../images/2ac40776816faa66b044b29aa360d10a.png)
Да, это неудобно, тем более делать так для каждого модуля. Но что поделать. Система сборки Unreal Engine плохо совместима с Visual Studio. А проект часто багует. Так что красное подчеркивание над `.generated.h` можно или терпеть или править вручную приведенным выше способом.
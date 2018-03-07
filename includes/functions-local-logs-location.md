Если узел службы "Функции" запускается в локальной среде, журналы записываются по следующему пути:

```
<DefaultTempDirectory>\LogFiles\Application\Functions
```

В Windows `<DefaultTempDirectory>` — это первое значение, найденное для переменных среды TMP, TEMP, USERPROFILE или каталога Windows.
В MacOS или Linux `<DefaultTempDirectory>` — это переменная среды TMPDIR.

[!Note]
При запуске узла службы "Функции" существующая структура файлов в каталоге будет перезаписана.
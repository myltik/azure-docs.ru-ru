1. Скопируйте установщик в локальную папку (например, C:\Temp) на сервере, который требуется защитить, а затем выполните следующие команды из командной строки с повышенными привилегиями.

  ```
  cd C:\Temp
  ren Microsoft-ASR_UA*Windows*release.exe MobilityServiceInstaller.exe
  MobilityServiceInstaller.exe /q /xC:\Temp\Extracted
  cd C:\Temp\Extracted.
  ```
2. Теперь можно установить службу Mobility Service, выполнив в командной строке следующую команду.

  ```
  UnifiedAgent.exe /Role "Agent" /CSEndpoint "IP Address of Configuration Server" /PassphraseFilePath <Full path to the passphrase file>``
  ```

#### <a name="mobility-service-installer-command-line-arguments"></a>Команда установщика службы Mobility Service: аргументы строки

```
Usage :
UnifiedAgent.exe [/Role <Agent/MasterTarget>] [/InstallLocation <Installation Directory>] [/CSIP <IP address>] [/PassphraseFilePath <Passphrase file path>] [/LogFilePath <Log File Path>]<br/>
```

  | Параметр|Тип|Описание|Возможные значения|
  |-|-|-|-|
  |/Role|Обязательно|Указывает, следует ли устанавливать службу Mobility Service.|Агент </br> MasterTarget|
  |/InstallLocation|Обязательно|Расположение, в которое будет установлена служба Mobility Service.|Любая папка на компьютере.|
  |/CSIP|Обязательно|IP-адрес сервера конфигурации.| Любой допустимый IP-адрес.|
  |/PassphraseFilePath|Обязательно|Расположение, в котором хранится парольная фраза хранилища. |Любой допустимый локальный путь к файлу или UNC.|
  |/LogGilePath|Необязательно|Расположение для журнала установки.|Любая допустимая папка на компьютере.|

#### <a name="sample-usage"></a>Пример использования

```
  UnifiedAgent.exe /Role "Agent" /CSEndpoint "I192.168.2.35" /PassphraseFilePath "C:\Temp\MobSvc.passphrase"
```


<!--HONumber=Jan17_HO3-->



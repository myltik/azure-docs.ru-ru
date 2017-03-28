1. Скопируйте установщик в локальную папку (например, C:\Temp) на сервере, который необходимо защитить. В командной строке выполните следующие команды от имени администратора.

  ```
  cd C:\Temp
  ren Microsoft-ASR_UA*Windows*release.exe MobilityServiceInstaller.exe
  MobilityServiceInstaller.exe /q /x:C:\Temp\Extracted
  cd C:\Temp\Extracted.
  ```
2. Чтобы установить службу Mobility Service, выполните следующую команду.

  ```
  UnifiedAgent.exe /Role "Agent" /CSEndpoint "IP address of the configuration server" /PassphraseFilePath <Full path to the passphrase file>``
  ```

#### <a name="mobility-service-installer-command-line-arguments"></a>Команда установщика службы Mobility Service: аргументы строки

```
Usage :
UnifiedAgent.exe [/Role <Agent/MasterTarget>] [/InstallLocation <Installation directory>] [/CSIP <IP address>] [/PassphraseFilePath <Passphrase file path>] [/LogFilePath <Log file path>]<br/>
```

  | Параметр|Тип|Описание|Возможные значения|
  |-|-|-|-|
  |/Role|Обязательно|Указывает, следует ли устанавливать службу Mobility Service.|Агент </br> MasterTarget|
  |/InstallLocation|Обязательно|Расположение, в котором установлена служба Mobility Service|Любая папка на компьютере.|
  |/CSIP|Обязательно|IP-адрес сервера конфигурации| Любой допустимый IP-адрес|
  |/PassphraseFilePath|Обязательно|Расположение файла с парольной фразой |Любой допустимый локальный путь к файлу или UNC|
  |/LogFilePath|Необязательно|Расположение журнала установки|Любая допустимая папка на компьютере.|

#### <a name="example"></a>Пример

```
  UnifiedAgent.exe /Role "Agent" /CSEndpoint "I192.168.2.35" /PassphraseFilePath "C:\Temp\MobSvc.passphrase"
```

UnifiedSetup.exe [/ServerMode <CS/PS>] [/InstallDrive <DriveLetter>] [/MySQLCredsFilePath <MySQL credentials file path>] [/VaultCredsFilePath <Vault credentials file path>] [/EnvType <VMWare/NonVMWare>] [/PSIP <IP-адрес для передачи данных] [/CSIP <IP address of CS to be registered with>] [/PassphraseFilePath <Passphrase file path>]

Параметры

* /ServerMode. (Обязательный параметр.) Указывает, нужно ли установить и сервер конфигурации, и сервер обработки или только север обработки. Входные значения: CS, PS.
* InstallLocation. Папка для установки компонентов.
* /MySQLCredsFilePath. (Обязательный параметр.) Путь к файлу, в котором хранятся учетные данные сервера MySQL. Файл должен быть в следующем формате:
* [MySQLCredentials]
* MySQLRootPassword = "<Password>"
* MySQLUserPassword = "<Password>"
* /VaultCredsFilePath. (Обязательный параметр.) Расположение файла с учетными данными хранилища.
* /EnvType. (Обязательный параметр.) Тип установки. Значения: VMware, NonVMware.
* /PSIP и /CSIP. (Обязательный параметр.) IP-адрес сервера обработки и сервера конфигурации.
* /PassphraseFilePath. (Обязательный параметр.) Расположение файла с парольной фразой.
* /BypassProxy. необязательный параметр. Указывает, что сервер конфигурации подключается к Azure без использования прокси-сервера.
* /ProxySettingsFilePath. необязательный параметр. Параметры прокси-сервера (по умолчанию прокси-серверу требуется проверка подлинности или пользовательский прокси-сервер). Файл должен быть в следующем формате:
* [ProxySettings]
* ProxyAuthentication = "Да/Нет"
* Proxy IP = "IP-адрес>"
* ProxyPort = "<Port>"
* ProxyUserName="<User Name>"
* ProxyPassword="<Password>"
* DataTransferSecurePort. необязательный параметр. Номер порта для данных репликации.
* SkipSpaceCheck. необязательный параметр. Пропускает проверку пространства для кэша.
* AcceptThirdpartyEULA. (Обязательный параметр.) Принимает лицензионное соглашение со сторонним разработчиком.
* ShowThirdpartyEULA. (Обязательный параметр.) Отображает лицензионное соглашение со сторонним разработчиком. Если оно задано как источник данных, все остальные параметры игнорируются.


<!--HONumber=Feb17_HO2-->



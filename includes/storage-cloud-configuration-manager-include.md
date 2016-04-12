### Анализ строки подключения

Указанная выше библиотека Microsoft Azure Configuration Manager содержит класс для анализа строки подключения из файла конфигурации. [Класс CloudConfigurationManager](https://msdn.microsoft.com/library/azure/mt634650.aspx) анализирует параметры конфигурации независимо от того, где работает клиентское приложение — на настольном компьютере, мобильном устройстве, виртуальной машине Azure или в облачной службе Azure.

Добавьте в метод `Main()` следующий код:

    // Parse the connection string and return a reference to the storage account.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(CloudConfigurationManager.GetSetting("StorageConnectionString"));

Использование диспетчера конфигураций Azure не является обязательным. Вы также можете использовать API, например [класс ConfigurationManager](https://msdn.microsoft.com/library/system.configuration.configurationmanager.aspx) для .NET Framework.

<!---HONumber=AcomDC_0406_2016-->
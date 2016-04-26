[Библиотека Microsoft Azure Configuration Manager для .NET](https://www.nuget.org/packages/Microsoft.WindowsAzure.ConfigurationManager/) содержит класс для анализа строки подключения из файла конфигурации. [Класс CloudConfigurationManager](https://msdn.microsoft.com/library/azure/mt634650.aspx) анализирует параметры конфигурации независимо от того, где работает клиентское приложение — на настольном компьютере, мобильном устройстве, виртуальной машине Azure или в облачной службе Azure.

Ниже приведен пример, в котором показано получение строки подключения из файла конфигурации.

    // Parse the connection string and return a reference to the storage account.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
		CloudConfigurationManager.GetSetting("StorageConnectionString"));

Использование диспетчера конфигураций Azure не является обязательным. Вы также можете использовать API, например [класс ConfigurationManager](https://msdn.microsoft.com/library/system.configuration.configurationmanager.aspx) для .NET Framework.

<!---HONumber=AcomDC_0420_2016-->
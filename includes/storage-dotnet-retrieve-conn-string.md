### Получение строки подключения
Для представления информации об учетной записи хранения можно использовать тип **CloudStorageAccount**. Если вы используете шаблон проекта Azure и/или ссылаетесь на пространство имен Microsoft.WindowsAzure.CloudConfigurationManager, то с помощью типа **CloudConfigurationManager** можно извлечь строку подключения и информацию об учетной записи хранения из конфигурации службы Azure:

    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

Если вы создаете приложение без ссылки на пространство имен Microsoft.WindowsAzure.CloudConfigurationManager и строка подключения находится в `web.config` или `app.config`, как показано выше, то для получения строки подключения можно использовать **ConfigurationManager**. Необходимо добавить ссылку на файл System.Configuration.dll в проект и добавить для него другое объявление пространства имен:

	using System.Configuration;
	...
	CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
		ConfigurationManager.ConnectionStrings["StorageConnectionString"]);

<!---HONumber=July15_HO3-->
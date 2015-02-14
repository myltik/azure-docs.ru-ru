<properties 
	pageTitle="Начало работы с хранилищем Azure" 
	description="" 
	services="storage" 
	documentationCenter="" 
	authors="kempb" 
	manager="douge" 
	editor=""/>

<tags 
	ms.service="storage" 
	ms.workload="web" 
	ms.tgt_pltfrm="vs-getting-started" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/10/2014" 
	ms.author="kempb"/>

> [AZURE.SELECTOR]
> - [Приступая к работе](/documentation/articles/vs-storage-aspnet5-getting-started-queues/)
> - [Что произошло?](/documentation/articles/vs-storage-aspnet5-what-happened/)

## Начало работы со службой хранилища Azure (проекты ASP.NET 5)

> [AZURE.SELECTOR]
> - [BLOB-объектов](/documentation/articles/vs-storage-aspnet5-getting-started-blobs/)
> - [Очереди](/documentation/articles/vs-storage-aspnet5-getting-started-queues/)
> - [Таблицы](/documentation/articles/vs-storage-aspnet5-getting-started-tables/)

Хранилище очередей Azure - это служба хранения большого количества сообщений, к которым можно получить доступ практически из любой точки мира с помощью вызовов с проверкой подлинности по протоколу HTTP или HTTPS. Одно сообщение очереди может быть размером до 64 КБ, а очередь может содержать миллионы сообщений до общего ограничения емкости учетной записи хранения. Дополнительные сведения см. в статье [Как использовать хранилище очередей из .NET](http://azure.microsoft.com/ru-ru/documentation/articles/storage-dotnet-how-to-use-queues/ "How to use Queue Storage from .NET").

Для программного доступа к очередям в проектах ASP.NET 5 необходимо добавить следующие элементы, если они еще не существуют.

1. Добавьте следующие объявления пространств имен кода в начало любого файла C#,в котором вы собираетесь получать доступ к хранилищу Azure программным способом.

		using Microsoft.Framework.ConfigurationModel;
		using Microsoft.WindowsAzure.Storage;
		using Microsoft.WindowsAzure.Storage.Queue;
		using System.Threading.Tasks;

2. Используйте следующий код для получения параметров настройки.

		 Configuration = new Configuration()
                .AddJsonFile("config.json")
                .AddEnvironmentVariables();

##### Получение строки подключения к хранилищу
Прежде чем что-либо делать с очередью, необходимо получить строку подключения для учетной записи хранилища, в котором будет располагаться эта очередь. Можно использовать тип **CloudStorageAccount** для представления сведений учетной записи хранилища. При использовании проекта ASP.NET 5 можно вызвать метод GET объекта Configuration, чтобы получить строку подключения к хранилищу и сведения учетной записи хранилища из конфигурации службы Azure, как показано в следующем примере кода.

**Примечание:** Интерфейсы API, которые выполняют обращения к хранилищу Azure в ASP.NET 5, являются асинхронными. Для получения дополнительных сведений см. раздел [Асинхронное программирование с помощью Async и Await](http://msdn.microsoft.com/library/hh191443.aspx). В следующем примере кода предполагается, что используются асинхронные методы программирования.

	CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
      config.Get("MicrosoftAzureStorage:<storageAccountName>_AzureStorageConnectionString"));

##### Создание очереди
Объект **CloudQueueClient** позволяет ссылаться на объекты очередей. Следующий код создает объект **CloudQueueClient**. Для всего кода в этой статье используется строка подключения к хранилищу, сохраненная в конфигурации службы приложения Azure. Существуют также другие способы создания объекта **CloudStorageAccount**. Подробнее см. в документации по [CloudStorageAccount](http://msdn.microsoft.com/ru-ru/library/microsoft.windowsazure.cloudstorageaccount_methods.aspx "CloudStorageAccount").

**Примечание:** Интерфейсы API, которые выполняют обращения к хранилищу Azure в ASP.NET 5, являются асинхронными. Для получения дополнительных сведений см. раздел [Асинхронное программирование с помощью Async и Await](http://msdn.microsoft.com/library/hh191443.aspx). В следующем примере кода предполагается, что используются асинхронные методы программирования.

	// Create the queue client.
	CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

С помощью объекта **queueClient** получите ссылку на очередь, которую необходимо использовать. Код ссылается на очередь под названием myqueue. Если очередь с таким именем отсутствует, она будет создана.

	// Get a reference to a queue named "myqueue".
	CloudQueue queue = queueClient.GetQueueReference("myqueue");

	// If the queue isn't already there, then create it.
	await queue.CreateIfNotExistsAsync();

**Примечание** Вставьте этот код полностью перед кодом из следующих разделов.

##### Вставка сообщения в очередь
Чтобы вставить сообщение в существующую очередь, сначала создайте новый объект **CloudQueueMessage**. Затем вызовите метод AddMessageAsync(). Для создания объекта **CloudQueueMessage** можно использовать либо строку (в формате UTF-8), либо массив типа byte. Ниже приведен код, который создает очередь (если она не существует) и вставляет сообщение 'Hello, World'.

	// Create a message and add it to the queue.
	CloudQueueMessage message = new CloudQueueMessage("Hello, World");
	await queue.AddMessageAsync(message);

##### Просмотр следующего сообщения
Вы можете просмотреть сообщение в начале очереди, не удаляя его из очереди, вызвав метод PeekMessageAsync().

	// Peek at the next message in the queue.
	CloudQueueMessage peekedMessage = queue.PeekMessage();

	// Display the message.
	CloudQueueMessage peekedMessage = await queue.PeekMessageAsync();

##### Удаление следующего сообщения
Ваш код может удалить сообщение из очереди в два этапа. 


1. Вызовите GetMessageAsync(), чтобы получить следующее сообщение в очереди. Сообщение, возвращаемое методом GetMessageAsync(), становится невидимым для любого другого кода, считывающего сообщения из этой очереди. По умолчанию это сообщение остается невидимым в течение 30 секунд. 
2.	Чтобы завершить удаление сообщения из очереди, вызовите DeleteMessageAsync(). 

Этот двухэтапный процесс удаления сообщения позволяет удостовериться, что если коду не удастся обработать сообщение из-за сбоя оборудования или программного обеспечения, другой экземпляр кода сможет получить то же сообщение и повторить попытку. Следующий код вызывает метод DeleteMessageAsync() сразу после обработки сообщения.

	// Get the next message in the queue.
	CloudQueueMessage retrievedMessage = await queue.GetMessageAsync();

	// Process the message in less than 30 seconds, and then delete the message.
	await queue.DeleteMessageAsync(retrievedMessage);

[Дополнительные сведения о хранилище Azure](http://azure.microsoft.com/documentation/services/storage/)
См. также [Просмотр ресурсов хранилища в обозревателе серверов](http://msdn.microsoft.com/ru-ru/library/azure/ff683677.aspx) и [ASP.NET 5](http://www.asp.net/vnext).
<!--HONumber=42-->

<properties 
	pageTitle="Использование хранилища очередей из .NET в Microsoft Azure "
	description="Вы узнаете дополнительные сведения об использовании службы очередей Microsoft Azure для создания и удаления очередей, вставки, получения и удаления сообщений." 
	services="storage" 
	documentationCenter=".net" 
	authors="tamram" 
	manager="adinah" 
	editor=""/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="03/11/2015" 
	ms.author="tamram"/>

# Использование хранилища очередей из .NET

[AZURE.INCLUDE [storage-selector-queue-include](../../includes/storage-selector-queue-include.md)]

## Обзор

В этом руководстве показано, как реализовать типичные сценарии с использованием
службы хранилища очередей Microsoft Azure. Примеры написаны на C# и в них используется клиент службы хранилища Azure для .NET. Здесь описаны такие сценарии, как **вставка**,
**просмотр**, **получение** и **удаление** сообщений очереди, а также
**создание и удаление очередей**.

> [AZURE.NOTE] В этом руководстве используется клиентская библиотека хранилища Azure для .NET 2.x и выше. Рекомендуется использовать клиентскую библиотеку хранилища 4.x, которая доступна через [NuGet](https://www.nuget.org/packages/WindowsAzure.Storage/) или как часть [пакета SDK для Azure для .NET](/downloads/). См. раздел [Программный доступ к хранилищу очередей](#programmatically-access-queue-storage) ниже, в котором приводятся дополнительные сведения о получении клиентской библиотеки хранилища.

[AZURE.INCLUDE [storage-queue-concepts-include](../../includes/storage-queue-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

[AZURE.INCLUDE [storage-configure-connection-string-include](../../includes/storage-configure-connection-string-include.md)]

## Программный доступ к хранилищу очередей

### Получение сборки
Можно использовать NuGet для получения сборки  `Microsoft.WindowsAzure.Storage.dll`. Щелкните правой кнопкой мыши проект в **Обозревателе решений** и выберите **Управление пакетами NuGet**.  Выполните в Интернете поиск "WindowsAzure.Storage" и нажмите кнопку **Установить**, чтобы установить пакет хранилища Azure и зависимые компоненты.

Библиотека Microsoft.WindowsAzure.Storage.dll также включена в состав пакета SDK для Azure для .NET, который можно скачать в <a href="http://azure.microsoft.com/develop/net/#">Центре разработчиков .NET</a>. Сборка устанавливается в каталог  `%Program Files%\Microsoft SDKs\Azure.NET SDK<sdk-version>\ref`.

### Объявления пространств имен
Добавьте следующие объявления пространств имен кода в начало любого файла C#, в котором вы собираетесь получать доступ к службе хранилища Azure программным способом.

    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;
	using Microsoft.WindowsAzure.Storage.Queue;

Убедитесь, что указана ссылка на сборку  `Microsoft.WindowsAzure.Storage.dll`.

### Получение строки подключения
Информацию о своей учетной записи хранения можно представить с помощью типа **CloudStorageAccount**. Если используется 
шаблон проекта Azure и/или ссылка на 
Microsoft.WindowsAzure.CloudConfigurationManager, вы можете использовать тип **CloudConfigurationManager**, чтобы получить строку подключения к хранилищу и сведения учетной записи хранения из конфигурации службы Azure:

    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

Если вы создаете приложение без ссылки на пространство имен Microsoft.WindowsAzure.CloudConfigurationManager, и строка подключения находится в файле `web.config` или `app.config`, как показано выше, то для получения строки подключения можно использовать **ConfigurationManager**.  Необходимо добавить ссылку на файл System.Configuration.dll в проект и добавить для него другое объявление пространства имен:

	using System.Configuration;
	...
	CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
		ConfigurationManager.ConnectionStrings["StorageConnectionString"].ConnectionString);

### Зависимости ODataLib
Зависимости ODataLib в клиентской библиотеке хранения для .NET разрешаются через пакеты ODataLib (версия 5.0.2), доступные через NuGet, а не через службы данных WCF.  Библиотеки ODataLib можно загрузить напрямую или указать на них ссылку в проекте через NuGet.  Мы используем пакеты ODataLib [OData], [Edm] и [Spatial].

## Создание очереди

Объект **CloudQueueClient** позволяет ссылаться на объекты очередей.
Следующий код создает объект **CloudQueueClient**. Для всего кода в этом руководстве используется строка подключения к хранилищу, сохраненная в конфигурации службы приложения Azure. Существуют также другие способы создания объекта **CloudStorageAccount**. Дополнительные сведения см. в документации по [CloudStorageAccount][].

    // Retrieve storage account from connection string
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

С помощью объекта **queueClient** получите ссылку на очередь, которую необходимо использовать. Очередь можно создать, если она не существует.

    // Retrieve a reference to a queue
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Create the queue if it doesn't already exist
    queue.CreateIfNotExist();

## Вставка сообщения в очередь

Чтобы вставить сообщение в существующую очередь, сначала создайте новый
объект **CloudQueueMessage**. Затем вызовите метод **AddMessage**. A
Для создания **CloudQueueMessage** можно использовать строку (в формате UTF-8) или массив **байтов**. Ниже приведен код, который создает очередь (если она не существует) и вставляет сообщение  'Hello, World':

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Create the queue if it doesn't already exist.
    queue.CreateIfNotExist();

    // Create a message and add it to the queue.
    CloudQueueMessage message = new CloudQueueMessage("Hello, World");
    queue.AddMessage(message);

## Просмотр следующего сообщения

Вы можете просмотреть сообщение в начале очереди, не удаляя его из очереди, вызвав метод **PeekMessage**.

    // Retrieve storage account from connection string
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Retrieve a reference to a queue
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Peek at the next message
    CloudQueueMessage peekedMessage = queue.PeekMessage();

	// Display message.
	Console.WriteLine(peekedMessage.AsString);

## Изменение содержимого сообщения в очереди

Вы можете изменить содержимое сообщения непосредственно в очереди. Если сообщение представляет собой рабочую задачу, можно использовать эту функцию для обновления состояния рабочей задачи. Следующий код добавляет новое содержимое в очередь сообщений и продлевает время ожидания видимости еще на 60 секунд. Это сохраняет состояние работы, связанной с данным сообщением, и позволяет клиенту продолжить работу с сообщением на протяжении еще одной минуты. Этот метод можно использовать для отслеживания многошаговых рабочих процессов по сообщениям в очереди без необходимости начинать с самого начала в случае сбоя шага обработки в связи с ошибкой аппаратного или программного обеспечения. Обычно также сохраняется счетчик повторов. Если количество повторов сообщения превысит *n* раз, его нужно удалить. Это обеспечивает защиту от сообщений, которые инициируют ошибку приложения при каждой попытке обработки.

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

	// Get the message from the queue and update the message contents.
    CloudQueueMessage message = queue.GetMessage();
    message.SetMessageContent("Updated contents.") ;
    queue.UpdateMessage(message, 
        TimeSpan.FromSeconds(0.0),  // Make it visible immediately.
        MessageUpdateFields.Content | MessageUpdateFields.Visibility);

## Удаление следующего сообщения из очереди

Код удаляет сообщение из очереди в два этапа. При вызове
метода **GetMessage** вы получаете следующее сообщение в очереди. Сообщение, возвращаемое методом **GetMessage**, становится невидимым для другого кода, считывающего сообщения из этой очереди. По умолчанию это сообщение остается невидимым в течение 30 секунд. Чтобы завершить удаление сообщения из очереди, необходимо также вызвать метод **DeleteMessage**. Этот двухэтапный процесс удаления сообщения позволяет удостовериться, что если коду не удастся обработать сообщение из-за сбоя оборудования или программного обеспечения, другой экземпляр кода сможет получить то же сообщение и повторить попытку. Код вызывает метод **deleteMessage** сразу после обработки сообщения.

    // Retrieve storage account from connection string
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Retrieve a reference to a queue
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Get the next message
    CloudQueueMessage retrievedMessage = queue.GetMessage();

    //Process the message in less than 30 seconds, and then delete the message
    queue.DeleteMessage(retrievedMessage);

## Дополнительные параметры для удаления сообщений из очереди

Способ извлечения сообщения из очереди можно настроить двумя способами.
Во-первых, можно получить пакет сообщений (до 32 сообщений). Во-вторых, можно задать более длительное или короткое время ожидания видимости, чтобы предоставить коду больше или меньше времени на полную обработку каждого сообщения. В следующем примере кода метод
**GetMessages** используется для получения 20 сообщений в одном вызове. Затем он обрабатывает каждое сообщение с помощью цикла **foreach**. Он также задает время ожидания невидимости 5 минут для каждого сообщения. Обратите внимание, что пятиминутный период начинается для всех сообщений одновременно, поэтому по прошествии 5 минут с момента вызова **GetMessages** все сообщения, которые не были удалены, снова становятся видимыми.

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    foreach (CloudQueueMessage message in queue.GetMessages(20, TimeSpan.FromMinutes(5)))
    {
        // Process all messages in less than 5 minutes, deleting each message after processing.
        queue.DeleteMessage(message);
    }

## Получение длины очереди

Вы можете узнать приблизительное количество сообщений в очереди. Метод
**FetchAttributes** отправляет в службу очередей запрос на извлечение атрибутов очереди, включая количество сообщений. Свойство **ApproximateMethodCount** возвращает последнее значение, полученное методом
**FetchAttributes**, без вызова службы очередей.

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

	// Fetch the queue attributes.
	queue.FetchAttributes();

    // Retrieve the cached approximate message count.
    int? cachedMessageCount = queue.ApproximateMessageCount;

	// Display number of messages.
	Console.WriteLine("Number of messages in queue: " + cachedMessageCount);

## Удаление очереди

Для удаления очереди и всех сообщений в ней вызовите метод
**Delete** для объекта очереди.

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Delete the queue.
    queue.Delete();

## Дальнейшие действия

Вы изучили основные сведения о хранилище очередей. Дополнительные сведения о более сложных задачах по использованию хранилища можно найти по следующим ссылкам.

<ul>
<li>Дополнительные сведения о доступных API-интерфейсах см. в справочной документации по службе очередей:
  <ul>
    <li><a href="http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409">Справочник по клиентской библиотеке хранилища для .NET</a>
    </li>
    <li><a href="http://msdn.microsoft.com/library/azure/dd179355">Справочник по REST API</a></li>
  </ul>
</li>
<li>Дополнительные сведения о более сложных задачах, которые можно выполнить с хранилищем Azure, см. в статье <a href="http://msdn.microsoft.com/library/azure/gg433040.aspx">Хранение данных и доступ к ним в Azure</a>.</li>
<li>Узнайте, как упростить код, предназначенный для работы со службой хранилища Azure с помощью <a href="../websites-dotnet-webjobs-sdk/">пакета SDK для Azure WebJobs.</li>
<li>Просмотрите дополнительные руководства, чтобы изучить дополнительные возможности хранения данных в Azure.
  <ul>
    <li>Использование <a href="/documentation/articles/storage-dotnet-how-to-use-tables/">табличного хранилища</a> для хранения структурированных данных.</li>
    <li>Использование <a href="/documentation/articles/storage-dotnet-how-to-use-blobs/">хранилища больших двоичных объектов</a> для хранения неструктурированных данных.</li>
    <li>Использование <a href="/documentation/articles/sql-database-dotnet-how-to-use/">Базы данных SQL</a> для хранения реляционных данных.</li>
  </ul>
</li>
</ul>

  [Загрузка и установка пакета SDK Azure для .NET]: /develop/net/
  [Справочник по клиентской библиотеке .NET]: http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409
  [Создание проекта Azure в Visual Studio]: http://msdn.microsoft.com/library/azure/ee405487.aspx 
  [CloudStorageAccount]: http://msdn.microsoft.com/library/azure/microsoft.windowsazure.cloudstorageaccount_methods.aspx
  [Хранение данных и доступ к ним в Azure]: http://msdn.microsoft.com/library/azure/gg433040.aspx
  [Блог рабочей группы службы хранилища Azure]: http://blogs.msdn.com/b/windowsazurestorage/
  [Настройка строк подключения]: http://msdn.microsoft.com/library/azure/ee758697.aspx
  [OData]: http://nuget.org/packages/Microsoft.Data.OData/5.0.2
  [Edm]: http://nuget.org/packages/Microsoft.Data.Edm/5.0.2
  [Spatial]: http://nuget.org/packages/System.Spatial/5.0.2

<!--HONumber=49--> 
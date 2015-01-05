<properties urlDisplayName="Queue Service" pageTitle="Вы научитесь использовать хранилище очередей из .NET в Microsoft Azure" metaKeywords="Get started Azure queue   Azure asynchronous processing   Azure queue   Azure queue storage   Azure queue .NET   Azure queue storage .NET   Azure queue C#   Azure queue storage C#" description="Learn how to use Microsoft Azure Queue storage to create and delete queues and insert, peek, get, and delete queue messages." metaCanonical="" disqusComments="1" umbracoNaviHide="1" services="storage" documentationCenter=".NET" title="How to use Microsoft Azure Queue Storage" authors="tamram" manager="adinah" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="11/10/2014" ms.author="tamram" />

# Использование хранилища очередей из .NET
 В этом руководстве показано, как реализовать типичные сценарии с использованием службы хранения очередей Azure. Примеры написаны на C# и в них используется клиент службы хранилища Azure для .NET. Здесь описаны такие сценарии, как **вставка**, **просмотр**, **получение** и **удаление** сообщений очереди, а также **создание и удаление очередей**. Дополнительную информацию об очередях см. в разделе [Дальнейшие действия][].

> [WACOM.NOTE] В этом руководстве используется клиентская библиотека хранилища Azure для .NET 2.x и выше. Рекомендуется использовать клиентскую библиотеку хранилища версии 4.x, которую можно скачать с помощью [NuGet](https://www.nuget.org/packages/WindowsAzure.Storage/) или в составе [пакета SDK для Azure для .NET](/ru-ru/downloads/). См. [Практическое руководство. Программный доступ к хранилищу очередей][] ниже, чтобы получить дополнительную информацию о получении библиотеки клиента хранения.

<h2>Оглавление</h2>

-   [Что такое хранилище очередей?][]
-   [Основные понятия][]
-   [Создание учетной записи хранения Azure][]
-   [Настройка строки подключения к службе хранилища Azure][]
-   [Практическое руководство. Программный доступ к хранилищу очередей][]
-   [Практическое руководство. Создание очереди][]
-   [Практическое руководство. Вставка сообщения в очередь][]
-   [Практическое руководство. Просмотр следующего сообщения][]
-   [Практическое руководство. Изменение содержимого сообщения в очереди][]
-   [Практическое руководство. Удаление следующего сообщения из очереди][]
-   [Практическое руководство. Дополнительные варианты удаления сообщений из очереди][]
-   [Практическое руководство. Получение длины очереди][]
-   [Практическое руководство. Удаление очереди][]
-   [Дальнейшие действия][]

[WACOM.INCLUDE [howto-queue-storage](../includes/howto-queue-storage.md)]

## <h2><a name="create-account"></a>Создание учетной записи хранения Azure</h2>

[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

## <h2><a name="setup-connection-string"></a>Настройка строки подключения к хранилищу Azure</h2>

[WACOM.INCLUDE [storage-configure-connection-string](../includes/storage-configure-connection-string.md)]

## <a name="configure-access"> </a>Практическое руководство: Программный доступ к хранилищу очередей

<h3>Получение сборки</h3>
Вы можете получить сборку "Microsoft.WindowsAzure.Storage.dll" с помощью NuGet. Щелкните правой кнопкой мыши проект в **обозревателе решений** и выберите **Управление пакетами NuGet**.  Выполните в Интернете поиск "WindowsAzure.Storage" и нажмите кнопку **Установить**, чтобы установить пакет службы хранилища Azure и зависимые компоненты.

Библиотека Microsoft.WindowsAzure.Storage.dll также включена в пакет SDK для Azure для .NET, который можно скачать из <a href="http://www.windowsazure.com/ru-ru/develop/net/#">Центра разработчиков .NET</a>. Эта сборка устанавливается в каталог %Program Files%\Microsoft SDKs\Windows Azure\.NET SDK\<версия-sdk>\ref\.

<h3>Объявления пространств имен</h3>
Добавьте следующие объявления пространств имен в начало всех файлов C#
из которого вы хотите программно обращаться к службе хранилища Azure:

    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;
	using Microsoft.WindowsAzure.Storage.Queue;

Убедитесь, что указана ссылка на сборку "Microsoft.WindowsAzure.Storage.dll".

<h3>Получение строки подключения</h3>
Информацию о вашей учетной записи хранения можно представить с помощью типа **CloudStorageAccount**. Если вы используете шаблон проекта Microsoft Azure или ссылаетесь на пространство имен Microsoft.WindowsAzure.CloudConfigurationManager, с помощью типа **CloudConfigurationManager** можно извлечь строку подключения и информацию об учетной записи хранения из конфигурации службы Azure:

    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

Если вы создаете приложение без ссылки на пространство имен Microsoft.WindowsAzure.CloudConfigurationManager и строка подключения находится в файле web.config или app.config, как показано выше, то для получения строки подключения можно использовать **ConfigurationManager**.  Необходимо добавить ссылку на файл System.Configuration.dll в проект и добавить для него другое объявление пространства имен:

	using System.Configuration;
	...
	CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
		ConfigurationManager.ConnectionStrings["StorageConnectionString"].ConnectionString);

<h3>Зависимости ODataLib</h3>
Зависимости ODataLib в клиентской библиотеке хранения для .NET разрешаются через пакеты ODataLib (версия 5.0.2), доступные через NuGet, а не через службы данных WCF.  Библиотеки ODataLib можно загрузить напрямую или указать на них ссылку в проекте через NuGet.  Мы используем пакеты [OData], [Edm] и [Spatial].

<h2><a name="create-queue"></a>Практическое руководство: Создание очереди</h2>

Объект **CloudQueueClient** позволяет ссылаться на объекты очередей. Следующий код создает объект **CloudQueueClient**. Для всего кода в этом руководстве используется строка подключения хранилища, сохраненная в
конфигурации службы приложения Azure. Существуют также другие способы создания
объекта **CloudStorageAccount**. Дополнительную информацию см. в документации по [CloudStorageAccount][]
.

    // Retrieve storage account from connection string
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

Use the **queueClient** object to get a reference to the queue you want
to use. You can create the queue if it doesn't exist.

    // Retrieve a reference to a queue
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Create the queue if it doesn't already exist
    queue.CreateIfNotExists();

<h2><a name="insert-message"> </a>Практическое руководство: Вставка сообщения в очередь</h2>

Чтобы вставить сообщение в существующую очередь, сначала создайте новый
**CloudQueueMessage**. Затем вызовите метод **AddMessage**. A
**CloudQueueMessage** можно использовать строку (в формате
UTF-8) или массив **байтов**. Ниже приведен код, который создает очередь (если она
не существует) и вставляет сообщение "Hello, World":

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Create the queue if it doesn't already exist.
    queue.CreateIfNotExists();

    // Create a message and add it to the queue.
    CloudQueueMessage message = new CloudQueueMessage("Hello, World");
    queue.AddMessage(message);

<h2><a name="peek-message"></a>Практическое руководство: Просмотр следующего сообщения</h2>

Вы можете просмотреть сообщение в начале очереди, не удаляя его
из очереди, вызвав метод **PeekMessage**.

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

<h2><a name="change-contents"></a>Практическое руководство: Изменение содержимого сообщения в очереди</h2>

Вы можете изменить содержимое сообщения непосредственно в очереди. Если сообщение представляет собой рабочую задачу, можно использовать эту функцию для обновления состояния рабочей задачи. Следующий код добавляет новое содержимое в очередь сообщений и продлевает время ожидания видимости еще на 60 секунд. Это сохраняет состояние работы, связанной с данным сообщением, и позволяет клиенту продолжить работу с сообщением на протяжении еще одной минуты. Этот метод можно использовать для отслеживания многошаговых рабочих процессов по сообщениям в очереди без необходимости начинать с самого начала в случае сбоя шага обработки в связи с ошибкой аппаратного или программного обеспечения. Обычно также сохраняется счетчик повторов; если количество повторов сообщения превысит n раз, его нужно удалить. Это обеспечивает защиту от сообщений, которые инициируют ошибку приложения при каждой попытке обработки.

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

<h2><a name="get-message"></a>Практическое руководство: Удаление следующего сообщения из очереди</h2>

Код удаляет сообщение из очереди в два этапа. При вызове метода **GetMessage** вы получаете следующее сообщение в очереди. Сообщение, возвращаемое методом **GetMessage**, становится невидимым для другого кода, считывающего сообщения
из этой очереди. По умолчанию это сообщение остается невидимым в течение 30 секунд. Чтобы завершить удаление сообщения из очереди, необходимо также вызвать метод **DeleteMessage**. Этот двухэтапный процесс удаления сообщения позволяет удостовериться, что если коду не удастся обработать сообщение из-за сбоя оборудования или программного обеспечения, другой экземпляр кода сможет получить то же сообщение и повторить попытку. Код вызывает метод **DeleteMessage** сразу после обработки сообщения.

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

<h2><a name="advanced-get"></a>Практическое руководство: Дополнительные параметры для удаления сообщений из очереди</h2>

Способ извлечения сообщения из очереди можно настроить двумя способами. Во-первых, можно получить пакет сообщений (до 32 сообщений). Во-вторых, можно задать более длительное или короткое время ожидания видимости, чтобы предоставить коду больше или меньше времени на полную обработку каждого сообщения. В следующем примере кода метод **GetMessages** используется для получения 20 сообщений за один вызов. Затем он обрабатывает каждое сообщение с помощью цикла **foreach**. Он также задает время ожидания невидимости 5 минут для каждого сообщения. Обратите внимание, что пятиминутный период начинается для всех сообщений одновременно, поэтому по прошествии 5 минут с момента вызова **GetMessages** все сообщения, которые не были удалены, снова становятся видимыми.

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

<h2><a name="get-queue-length"></a>Практическое руководство: Получение длины очереди</h2>

Вы можете узнать приблизительное количество сообщений в очереди. Метод **FetchAttributes** отправляет запрос в службу очередей на извлечение атрибутов очереди, включая количество сообщений. Свойство **ApproximateMethodCount** возвращает последнее значение, полученное с использованием метода **FetchAttributes**, без обращения к службе очередей.

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

<h2><a name="delete-queue"></a>Практическое руководство: Удаление очереди</h2>

Чтобы удалить очередь и все сообщения в ней, вызовите метод **Delete** в объекте очереди.

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Delete the queue.
    queue.Delete();

<h2><a name="next-steps"></a>Дальнейшие действия</h2>

Вы получили общее представление об управлении хранилищем очередей.
чтобы узнать о том, как выполнять более сложные задачи хранения.

<ul>
<li>Дополнительные сведения о доступных API-интерфейсах см. в справочной документации по службе очередей:
  <ul>
    <li><a href="http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409">Справочник по клиентской библиотеке хранения для .NET</a>
    </li>
    <li><a href="http://msdn.microsoft.com/ru-ru/library/windowsazure/dd179355">Справочник по REST API</a></li>
  </ul>
</li>
<li>Дополнительную информацию о более сложных задачах, которые можно выполнить со службой хранилища Azure, см. в статье <a href="http://msdn.microsoft.com/ru-ru/library/windowsazure/gg433040.aspx">Хранение данных и доступ к ним в Azure</a>.</li>
<li>Дополнительную информацию о работе со службой хранилища Azure в серверных процессах для веб-сайтов Azure см. в статье <a href="/ru-ru/documentation/articles/websites-dotnet-webjobs-sdk-get-started/">Начало работы с пакетом SDK веб-заданий Azure</a>.</li>
<li>Просмотрите дополнительные руководства, чтобы изучить дополнительные возможности хранения данных в Azure.
  <ul>
    <li>Использовать <a href="/ru-ru/documentation/articles/storage-dotnet-how-to-use-tables/">Хранилище таблиц</a> для хранения структурированных данных.</li>
    <li>Использовать <a href="/ru-ru/documentation/articles/storage-dotnet-how-to-use-blobs/">Хранилище BLOB-объектов</a> для хранения неструктурированных данных.</li>
    <li>Использовать <a href="/ru-ru/documentation/articles/sql-database-dotnet-how-to-use/">База данных SQL</a> для хранения реляционных данных.</li>
  </ul>
</li>
</ul>



  [Дальнейшие действия]: #next-steps
  [Что такое хранилище очередей?]: #what-is
  [Основные понятия]: #concepts
  [Создание учетной записи хранения Azure]: #create-account
  [Настройка строки подключения к службе хранилища Azure]: #setup-connection-string
  [Практическое руководство. Программный доступ к хранилищу очередей]: #configure-access
  [Практическое руководство. Создание очереди]: #create-queue
  [Практическое руководство. Вставка сообщения в очередь]: #insert-message
  [Практическое руководство. Просмотр следующего сообщения]: #peek-message
  [Практическое руководство. Изменение содержимого сообщения в очереди]: #change-contents
  [Практическое руководство. Удаление следующего сообщения из очереди]: #get-message
  [Практическое руководство. Дополнительные варианты удаления сообщений из очереди]: #advanced-get
  [Практическое руководство. Получение длины очереди]: #get-queue-length
  [Практическое руководство. Удаление очереди]: #delete-queue
  [Скачивание и установка пакета SDK для Microsoft Azure для .NET]: /ru-ru/develop/net/
  [Справочник по клиентской библиотеке .NET]: http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409
  [Создание проекта Azure в Visual Studio]: http://msdn.microsoft.com/ru-ru/library/windowsazure/ee405487.aspx 
  [CloudStorageAccount]: http://msdn.microsoft.com/ru-ru/library/microsoft.windowsazure.cloudstorageaccount_methods.aspx
  [Хранение данных и доступ к ним в Azure]: http://msdn.microsoft.com/ru-ru/library/windowsazure/gg433040.aspx
  [Блог команды разработчиков службы хранилища Azure]: http://blogs.msdn.com/b/windowsazurestorage/
  [Настройка строк подключения]: http://msdn.microsoft.com/ru-ru/library/windowsazure/ee758697.aspx
  [OData]: http://nuget.org/packages/Microsoft.Data.OData/5.0.2
  [Edm]: http://nuget.org/packages/Microsoft.Data.Edm/5.0.2
  [Пространственный]: http://nuget.org/packages/System.Spatial/5.0.2

<!--HONumber=35.1-->

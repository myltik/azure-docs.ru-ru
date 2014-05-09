<properties linkid="dev-net-2-how-to-queue-service" urlDisplayName="Служба очередей (2.0)" pageTitle="Как пользоваться службой хранилища очередей | Microsoft Azure" metaKeywords="Начало работы с очередями Azure, асинхронная обработка в Azure, очередь Azure, хранилище очередей Azure, очередь .NET Azure. хранилище очередей .NET Azure, очередь Azure C#, хранилище очередей Azure C#" description="Использование службы хранилища очередей Azure для создания и удаления очередей, а также для вставки, просмотра и удаления сообщений в очереди." metaCanonical="" services="storage" documentationCenter=".NET" title="Как пользоваться службой хранилища очередей" authors="" solutions="" manager="paulettm" editor="cgronlun" />





# Использование службы хранилища очередей

<div class="dev-center-tutorial-selector">
<a href="/ru-ru/develop/net/how-to-guides/queue-service-v17/" title="версия 1.7" class="current">версия 1.7</a>
<a href="/ru-ru/develop/net/how-to-guides/queue-service/" title="версия 2.0">версия 2.0</a> 
</div>


В этом руководстве показано, как реализовать типичные сценарии
с использованием службы хранилища очередей Azure. Примеры написаны на языке C\#, и в них
используется API-интерфейс .NET. Здесь описаны такие сценарии, как **вставка**,
**просмотр**, **получение** и **удаление** сообщений очереди, а также
**создание и удаление очередей**. Дополнительные сведения об очередях см. в разделе [Дальнейшие действия][].

<h2>Оглавление</h2>

-   [Что такое хранилище очередей?][]
-   [Основные понятия][]
-   [Создание учетной записи хранения Azure][]
-   [Настройка строки подключения к хранилищу Azure][]
-   [Практическое руководство. Программный доступ к очередям с использованием .NET][]
-   [Практическое руководство. Создание очереди][]
-   [Практическое руководство. Вставка сообщения в очередь][]
-   [Практическое руководство. Просмотр следующего сообщения][]
-   [Практическое руководство. Изменение содержимого сообщения в очереди][]
-   [Практическое руководство. Удаление следующего сообщения из очереди][]
-   [Практическое руководство. Дополнительные варианты для удаления сообщений из очереди][]
-   [Практическое руководство. Получение длины очереди][]
-   [Практическое руководство. Удаление очереди][]
-   [Следующие шаги][]

[WACOM.INCLUDE [howto-queue-storage](../includes/howto-queue-storage.md)]

<h2><a name="create-account"></a><span  class="short-header">Создание учетной записи</span>Создание учетной записи хранения Azure</h2>

[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

<h2><a name="setup-connection-string"></a><span  class="short-header">Настройка строки подключения</span>Настройка строки подключения к хранилищу Azure</h2>

API хранилища Azure для .NET поддерживает использование строки подключения
для настройки конечных точек и учетных данных для доступа к
службам хранилища. Строку подключения можно разместить в файле конфигурации,
а не жестко задать ее в коде:

- При использовании облачных служб Azure рекомендуется хранить строки подключения с помощью системы конфигурации службы Azure (файлы `*CSDEF` и `*CSCFG`).
- При использовании веб-сайтов Azure или виртуальных машин Azure рекомендуется хранить строки подключения с помощью системы конфигурации .NET (например, в файле `web.config`).

В обоих случаях можно получить строку подключения с помощью метода `CloudConfigurationManager.GetSetting`, как показано далее в этом руководстве.

### Настройка строки подключения при использовании облачных служб

Механизм настройки службы является уникальным для проектов облачных служб Azure
и позволяет динамически изменять параметры конфигурации на портале управления Azure
без повторного развертывания приложения.

Настройка строки подключения в конфигурации службы
Azure:

1.  В обозревателе решений Visual Studio щелкните правой кнопкой мыши веб-роль или рабочую роль в папке **Роли** вашего проекта развертывания Azure и выберите **Свойства**.  
	![Blob5](./media/storage-dotnet-how-to-use-queues-17/blob5.png)

2.  Откройте вкладку **Параметры** и нажмите кнопку **Добавить параметр**.  
	![Blob6](./media/storage-dotnet-how-to-use-queues-17/blob6.png)

	В таблице параметров появится новая запись **Setting1**.

3.  В раскрывающемся списке **Тип** новой записи **Setting1** выберите **Строка подключения**.  
	![Blob7](./media/storage-dotnet-how-to-use-queues-17/blob7.png)

4.  Нажмите кнопку **...** в правой части записи **Setting1**. Откроется диалоговое окно **Строка подключения учетной записи хранения**.

5.  Выберите для использования целевой эмулятор (хранилище Azure, смоделированное на локальном компьютере) или фактическую учетную запись хранения в облаке. Код, представленный в этом руководстве, работает в любом случае. Введите **первичный ключ доступа**, скопированный на предыдущем шаге этого учебника, чтобы сохранить данные BLOB-объекта в учетную запись хранилища, созданную ранее в Azure.   

	![Blob8](./media/storage-dotnet-how-to-use-queues-17/blob8.png)

6.  Измените **Имя** записи с **Setting1** на более понятное, например **StorageConnectionString**. Эта строка подключения будет использоваться далее в данном руководстве.  

	![Blob9](./media/storage-dotnet-how-to-use-queues-17/blob9.png)
	
### Настройка строки подключения при использовании веб-сайтов или виртуальных машин

При использовании веб-сайтов или виртуальных машин рекомендуется использовать систему конфигурации .NET (например, `web.config`).  Строка подключения хранится с помощью элемента `<appSettings>`:

	<configuration>
	    <appSettings>
		    <add key="StorageConnectionString"
			     value="DefaultEndpointsProtocol=https;AccountName=[AccountName];AccountKey=[AccountKey]" />
		</appSettings>
	</configuration>

Дополнительные сведения о строках подключения к хранилищу см. в разделе [Настройка строк подключения][].
	
Теперь все готово для выполнения задач, описанных в данном руководстве.

<h2><a name="access"></a><span  class="short-header">Программный доступ</span>Практическое руководство. Программный доступ к очередям с использованием .NET</h2>

Добавьте следующие объявления пространств имен кода в начало любого файла C\#,
в котором вы собираетесь получать доступ к хранилищу Azure программным способом:

    using Microsoft.WindowsAzure;
    using Microsoft.WindowsAzure.StorageClient;

Можно использовать тип **CloudStorageAccount** и
**CloudConfigurationManager**,
чтобы извлечь строку подключения и сведения об учетной записи
хранения из конфигурации службы Azure:

    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

<h2><a name="create-queue"></a><span  class="short-header">Создание очереди</span>Практическое руководство. Создание очереди</h2>

Объект **CloudQueueClient** позволяет ссылаться на объекты очередей.
Следующий код создает объект **CloudQueueClient**. Для всего кода
в этом руководстве используется строка подключения хранилища, сохраненная
в конфигурации службы приложения Azure. Существуют также другие способы создания объекта **CloudStorageAccount**. Дополнительные сведения см. в документации по [CloudStorageAccount][]
.

    // Retrieve storage account from connection string
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

С помощью объекта **queueClient** получите ссылку на очередь,
которую необходимо использовать. Очередь можно создать, если она не существует.

    // Retrieve a reference to a queue
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Create the queue if it doesn't already exist
    queue.CreateIfNotExist();

<h2><a name="insert-message"> </a><span  class="short-header">Вставка сообщения</span>Практическое руководство. Вставка сообщения в очередь</h2>

Чтобы вставить сообщение в существующую очередь, сначала создайте новый
объект **CloudQueueMessage**. Затем вызовите метод **AddMessage**. A
Для создания **CloudQueueMessage** можно использовать либо строку (в формате UTF-8), либо массив **байтов**. Ниже приведен код, который создает очередь (если она
не существует) и вставляет сообщение "Hello, World".

    // Retrieve storage account from connection string
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Retrieve a reference to a queue
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Create the queue if it doesn't already exist
    queue.CreateIfNotExist();

    // Create a message and add it to the queue
    CloudQueueMessage message = new CloudQueueMessage("Hello, World");
    queue.AddMessage(message);

<h2><a name="peek-message"></a><span  class="short-header">Просмотр следующего сообщения</span>Практическое руководство. Просмотр следующего сообщения</h2>

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

<h2><a name="change-contents"></a><span  class="short-header">Изменение содержимого сообщения</span>Практическое руководство. Изменение содержимого сообщения в очереди</h2>

Вы можете изменить содержимое сообщения непосредственно в очереди. Если сообщение представляет собой рабочую задачу, можно использовать эту функцию для обновления состояния рабочей задачи. Следующий код добавляет новое содержимое в очередь сообщений и продлевает время ожидания видимости еще на 60 секунд. Это сохраняет состояние работы, связанной с данным сообщением, и позволяет клиенту продолжить работу с сообщением на протяжении еще одной минуты. Этот метод можно использовать для отслеживания многошаговых рабочих процессов по сообщениям в очереди без необходимости начинать с самого начала в случае сбоя шага обработки в связи с ошибкой аппаратного или программного обеспечения. Обычно также сохраняется счетчик повторов; если количество повторов сообщения превысит *n* раз, его нужно удалить. Это обеспечивает защиту от сообщений, которые инициируют ошибку приложения при каждой попытке обработки.

    // Retrieve storage account from connection string
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Retrieve a reference to a queue
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    CloudQueueMessage message = queue.GetMessage();
    message.SetMessageContent("Updated contents.") ;
    queue.UpdateMessage(message, 
        TimeSpan.FromSeconds(0.0),  // visible immediately
        MessageUpdateFields.Content | MessageUpdateFields.Visibility);

<h2><a name="get-message"></a><span  class="short-header">Удаление следующего сообщения из очереди</span>Практическое руководство. Удаление следующего сообщения из очереди</h2>

Код удаляет сообщение из очереди в два этапа. При вызове
**GetMessage** вы получаете следующее сообщение в очереди. Сообщение,
возвращаемое методом **GetMessage**, становится невидимым для другого
кода, считывающего сообщения из этой очереди. По умолчанию это сообщение остается невидимым в течение 30 секунд. Чтобы завершить удаление сообщения из очереди,
необходимо вызвать метод **DeleteMessage**. Этот двухэтапный процесс удаления сообщения позволяет удостовериться, что если коду не удастся обработать сообщение из-за сбоя оборудования или программного обеспечения, другой экземпляр кода сможет получить то же сообщение и повторить попытку. Код вызывает метод **DeleteMessage** сразу
после обработки сообщения.

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

<h2><a name="advanced-get"></a><span  class="short-header">Дополнительные варианты удаления из очереди</span>Практическое руководство. Дополнительные варианты для удаления сообщений из очереди</h2>

Способ извлечения сообщения из очереди можно настроить двумя способами.
Во-первых, можно получить пакет сообщений (до 32 сообщений). Во-вторых, можно задать более длительное или короткое время ожидания невидимости, чтобы предоставить коду больше или меньше времени на полную обработку каждого сообщения. В следующем примере кода метод
**GetMessages** используется для получения 20 сообщений в одном вызове. Затем он обрабатывает
каждое сообщение с помощью цикла **foreach**. Он также задает время ожидания невидимости 5 минут для каждого сообщения. Обратите внимание, что пятиминутный период
начинается для всех сообщений одновременно, поэтому по прошествии 5 минут с момента
вызова **GetMessages** все сообщения, которые не были удалены,
снова становятся видимыми.

    // Retrieve storage account from connection string
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Retrieve a reference to a queue
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    foreach (CloudQueueMessage message in queue.GetMessages(20, TimeSpan.FromMinutes(5)))
    {
        // Do processing for all messages in less than 5 minutes, deleting each message after processing
        queue.DeleteMessage(message);
    }

<h2><a name="get-queue-length"></a><span  class="short-header">Получение длины очереди</span>Практическое руководство. Получение длины очереди</h2>

Вы можете узнать приблизительное количество сообщений в очереди. Метод
**RetrieveApproximateMessageCount** запрашивает количество сообщений в
очереди у службы очередей. Счетчик указывает
число лишь приблизительно, так как сообщения могут добавляться или
удаляться после ответа службы очередей на ваш запрос. Свойство **ApproximateMethodCount**
возвращает последнее значение, полученное
методом **RetrieveApproximateMessageCount** без вызова службы очередей.

    // Retrieve storage account from connection string
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Retrieve a reference to a queue
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Retrieve the approximate message count
    int freshMessageCount = queue.RetrieveApproximateMessageCount();

    // Retrieve the cached approximate message count
    int? cachedMessageCount = queue.ApproximateMessageCount;

<h2><a name="delete-queue"></a><span  class="short-header">Удаление очереди</span>Практическое руководство. Удаление очереди</h2>

Для удаления очереди и всех сообщений в ней вызовите метод
**Delete** в объекте очереди.

    // Retrieve storage account from connection string
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Retrieve a reference to a queue
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Delete the queue
    queue.Delete();

<h2><a name="next-steps"></a>Следующие шаги</h2>

Вы изучили основные сведения о хранилище очередей. Дополнительные сведения о более сложных задачах по использованию хранилища можно найти по следующим ссылкам.

<ul>
<li>Дополнительные сведения о доступных API-интерфейсах см. в справочной документации по службе очередей:
  <ul>
    <li><a href="http://msdn.microsoft.com/ru-ru/library/windowsazure/wl_svchosting_mref_reference_home">Справочник по библиотеке клиента .NET</a>
    </li>
    <li><a href="http://msdn.microsoft.com/ru-ru/library/windowsazure/dd179355">Справочник по REST API</a></li>
  </ul>
</li>
<li>Дополнительные сведения о более сложных задачах, которые можно выполнить с хранилищем Azure, см. в статье <a href="http://msdn.microsoft.com/ru-ru/library/windowsazure/gg433040.aspx">Хранение данных и доступ к ним в Azure</a>.</li>
<li>Просмотрите дополнительные руководства, чтобы изучить дополнительные возможности хранения данных в Azure.
  <ul>
    <li>Использование <a href="/ru-ru/develop/net/how-to-guides/table-services/">табличного хранилища</a> для хранения структурированных данных.</li>
    <li>Использование <a href="/ru-ru/develop/net/how-to-guides/blob-storage/">хранилища BLOB-объектов</a> для хранения неструктурированных данных.</li>
    <li>Использование <a href="/ru-ru/develop/net/how-to-guides/sql-database/">базы данных SQL</a> для хранения реляционных данных.</li>
  </ul>
</li>
</ul>



  [Следующие шаги]: #next-steps
  [Что такое хранилище очередей?]: #what-is
  [Основные понятия]: #concepts
  [Создание учетной записи хранения Azure]: #create-account
  [Настройка строки подключения к хранилищу Azure]: #setup-connection-string
  [Практическое руководство. Программный доступ к очередям с использованием .NET]: #access
  [Практическое руководство. Создание очереди]: #create-queue
  [Практическое руководство. Вставка сообщения в очередь]: #insert-message
  [Практическое руководство. Просмотр следующего сообщения]: #peek-message
  [Практическое руководство. Изменение содержимого сообщения в очереди]: #change-contents
  [Практическое руководство. Удаление следующего сообщения из очереди]: #get-message
  [Практическое руководство. Дополнительные варианты для удаления сообщений из очереди]: #advanced-get
  [Практическое руководство. Получение длины очереди]: #get-queue-length
  [Практическое руководство. Удаление очереди]: #delete-queue
  [Загрузите и установите пакет Azure SDK для .NET]: /ru-ru/develop/net/
  [Создание проекта Azure в Visual Studio]: http://msdn.microsoft.com/ru-ru/library/windowsazure/ee405487.aspx
  

  
  [CloudStorageAccount]: http://msdn.microsoft.com/ru-ru/library/microsoft.windowsazure.cloudstorageaccount_methods.aspx
  [Хранение данных и доступ к ним в Azure]: http://msdn.microsoft.com/ru-ru/library/windowsazure/gg433040.aspx
  [Блог команды разработчиков хранилища Azure]: http://blogs.msdn.com/b/windowsazurestorage/
  [Настройка строк подключения]: http://msdn.microsoft.com/ru-ru/library/windowsazure/ee758697.aspx


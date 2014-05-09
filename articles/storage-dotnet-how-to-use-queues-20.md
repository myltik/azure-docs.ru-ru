<properties linkid="dev-net-2-how-to-queue-service" urlDisplayName="Служба очередей (2.0)" pageTitle="Как пользоваться службой хранилища очередей — Windows Azure" metaKeywords="Начало работы с очередями Azure Асинхронная обработка в Azure Очередь Azure Хранилища очередей Очередь .NET Azure Хранилище очередей .NET Azure Очередь Azure C# Хранилище очередей Azure C#" description="Использование службы хранилища очередей Windows Azure для создания и удаления очередей, а также для вставки, просмотра и удаления сообщений в очереди." metaCanonical="" disqusComments="1" umbracoNaviHide="1" title="Как пользоваться службой хранилища очередей" />



# Использование службы хранилища очередей

<div class="dev-center-tutorial-selector">
<a href="/ru-ru/develop/net/how-to-guides/queue-service-v17/" title="версия 1.7">версия 1.7</a>
<a href="/ru-ru/develop/net/how-to-guides/queue-service/" title="версия 2.0" class="current">версия 2.0</a> 
</div>


В этом руководстве показано, как реализовать типичные сценарии с использованием службы хранилища очередей Windows Azure. Примеры написаны на C\#
и используют клиент хранения Windows Azure для .NET (версия 2.0). Здесь описаны такие сценарии, как **вставка**, **просмотр**, **получение** и **удаление** сообщений очереди, а также **создание и удаление очередей**. Дополнительные сведения об очередях см. в разделе [Дальнейшие действия][].

<h2>Оглавление</h2>

-   [Что такое хранилище очередей?][]
-   [Основные понятия][]
-   [Создание учетной записи хранения Windows Azure][]
-   [Настройка строки подключения к хранилищу Windows Azure][]
-   [Практическое руководство. Программный доступ к очередям с использованием .NET][]
-   [Практическое руководство. Создание очереди][]
-   [Практическое руководство. Вставка сообщения в очередь][]
-   [Практическое руководство. Просмотр следующего сообщения][]
-   [Практическое руководство. Изменение содержимого сообщения в очереди][]
-   [Практическое руководство. Удаление следующего сообщения из очереди][]
-   [Практическое руководство. Использование дополнительных параметров для удаления сообщений из очереди][]
-   [Практическое руководство. Получение длины очереди][]
-   [Практическое руководство. Удаление очереди][]
-   [Дальнейшие действия][]

[WACOM.INCLUDE [howto-queue-storage](../includes/howto-queue-storage.md)]

<h2><a name="create-account"></a><span  class="short-header">Создание учетной записи</span>Создание учетной записи хранения Windows Azure</h2>

[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

<h2><a name="setup-connection-string"></a><span  class="short-header">Настройка строки подключения</span>Настройка строки подключения к хранилищу Windows Azure</h2>

Библиотека клиента хранения Windows Azure для .NET поддерживает использование строки подключения для настройки конечных точек и учетных данных для доступа к службам хранилища. Строку подключения можно разместить в файле конфигурации,
а не жестко задать ее в коде:

-При использовании облачных служб Windows Azure рекомендуется хранить строки подключения с помощью системы конфигурации службы Windows Azure (*CSDEF- и *CSCFG-файлы).
- Если используются веб-сайты Windows Azure, виртуальных машин Windows Azure или создаются приложения .NET, которые должны работать вне Windows Azure, рекомендуется хранить строки подключения с помощью системы конфигурации .NET (например, в файле web.config или app.config).

В обоих случаях можно извлечь строку подключения с помощью метода `CloudConfigurationManager.GetSetting`, как показано далее в этом руководстве.

### Настройка строки подключения при использовании облачных служб

Механизм настройки службы является уникальным для проектов облачных служб Windows Azure и позволяет динамически изменять параметры конфигурации на портале управления Windows Azure без повторного развертывания приложения.

Настройка строки подключения в конфигурации службы Windows Azure:

1.  В обозревателе решений Visual Studio щелкните правой кнопкой мыши в папке **Роли**
    вашего проекта развертывания Windows Azure веб-роль или рабочую роль и выберите **Свойства**  

	![Blob5](./media/storage-dotnet-how-to-use-queues-20/blob5.png)

2.  Откройте вкладку **Параметры** и нажмите кнопку **Добавить параметр**. 
 
	![Blob6](./media/storage-dotnet-how-to-use-queues-20/blob6.png)

	В таблице параметров появится новая запись **Setting1**.

3.  В раскрывающемся списке **Тип** новой записи **Setting1** выберите **Строка подключения**.  

	![Blob7](./media/storage-dotnet-how-to-use-queues-20/blob7.png)

4.  Нажмите кнопку **...** в правой части записи **Setting1**. Откроется диалоговое окно **Строка подключения учетной записи хранения**.

5.  Выберите для использования целевой эмулятор (хранилище Windows Azure, смоделированное на локальном компьютере) или фактическую учетную запись хранения в облаке. Код, представленный в этом руководстве, работает в любом случае. Введите **Первичный ключ доступа**, скопированный на предыдущем шаге этого учебника, чтобы сохранить данные очереди в учетную запись хранилища, созданную ранее в Windows Azure.   

	![Blob8](./media/storage-dotnet-how-to-use-queues-20/blob8.png)

6.  Измените **Имя** записи с **Setting1** на более понятное, например **StorageConnectionString**. Эта строка подключения будет использоваться далее в данном руководстве.  

	![Blob9](./media/storage-dotnet-how-to-use-queues-20/blob9.png)
	
### Настройка строки подключения с помощью конфигурации .NET

При написании приложения, которое не является облачной службой Windows Azure (см. предыдущий раздел), рекомендуется использовать систему конфигурации .NET (например, файл web.config или app.config).  Это относится к веб-сайтам Windows Azure или виртуальным машинам для Windows Azure, а также приложениям, разработанным для работы вне Windows Azure.  Строка подключения хранится с помощью элемента "<appSettings>" следующим образом:

	<configuration>
  		<appSettings>
    		<add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=[AccountName];AccountKey=[AccountKey" />
  		</appSettings>
	</configuration>

Дополнительные сведения о строках подключения к хранилищу см. в разделе [Настройка строк подключения][].
	
Теперь все готово для выполнения задач, описанных в данном руководстве.

<h2><a name="access"></a><span  class="short-header">Программный доступ</span>Практическое руководство. Программный доступ к очередям с использованием .NET</h2>

<h3>Получение сборки</h3>
Вы можете получить сборку "Microsoft.WindowsAzure.Storage.dll" с помощью NuGet. Щелкните правой кнопкой мыши проект в **Обозревателе решений** и выберите **Управление пакетами NuGet**.  Выполните в Интернете поиск "WindowsAzure.Storage" и нажмите кнопку **Установить**, чтобы установить пакет хранилища Windows Azure и зависимые компоненты.

Библиотека "Microsoft.WindowsAzure.Storage.dll" также включена в пакет Windows Azure SDK для .NET 2.0, который можно загрузить из <a href="http://www.windowsazure.com/ru-ru/develop/net/#">Центра разработчиков .NET</a>. Эта сборка устанавливается в каталог %Program Files%\Microsoft SDKs\Windows Azure\.NET SDK\v2.0\ref\.

<h3>Объявления пространств имен</h3>
Добавьте следующие объявления пространств имен в начало любого файла C\#, в котором вы собираетесь получать доступ к хранилищу Windows Azure программным способом:

    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;
	using Microsoft.WindowsAzure.Storage.Queue;

Убедитесь, что указана ссылка на сборку "Microsoft.WindowsAzure.Storage.dll".

<h3>Получение строки подключения</h3>
Для представлений сведений о вашей учетной записи хранения можно использовать тип **CloudStorageAccount**. Если вы используете шаблон проекта Windows Azure или ссылаетесь на пространство имен Microsoft.WindowsAzure.CloudConfigurationManager, с помощью типа **CloudConfigurationManager** можно извлечь строку подключения и сведения об учетной записи хранения из конфигурации службы Windows Azure:

    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

Если вы создаете приложение без ссылки на Microsoft.WindowsAzure.CloudConfigurationManager и строка подключения находится в "web.config" или "app.config", как показано выше, для получения строки подключения можно использовать **ConfigurationManager**.  Необходимо добавить ссылку на файл System.Configuration.dll в проект и добавить для него другое объявление пространства имен:

	using System.Configuration;
	...
	CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
		ConfigurationManager.ConnectionStrings["StorageConnectionString"].ConnectionString);

<h3>Зависимости ODataLib</h3>
Зависимости ODataLib в клиентской библиотеке хранения для .NET разрешаются через пакеты ODataLib (версия 5.0.2), доступные через NuGet, а не через службы данных WCF.  Библиотеки ODataLib можно загрузить напрямую или указать на них ссылку в проекте через NuGet.  Мы используем пакеты ODataLib [OData], [Edm] и [Spatial].

<h2><a name="create-queue"></a><span  class="short-header">Создание очереди</span>Практическое руководство. Создание очереди</h2>

Объект **CloudQueueClient** позволяет ссылаться на объекты очередей.
Следующий код создает объект **CloudQueueClient**. Для всего кода в этом руководстве используется строка подключения хранилища, сохраненная в конфигурации службы приложения Windows Azure. Существуют также другие способы создания объекта **CloudStorageAccount**. Дополнительные сведения см. в документации
к [CloudStorageAccount][].

    // Получить учетную запись хранения из строки подключения
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Создать клиент очереди
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

С помощью объекта **queueClient** получите ссылку на очередь, которую необходимо использовать. Очередь можно создать, если она не существует.

    // Получить ссылку на очередь
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Создать очередь, если она не существует
    queue.CreateIfNotExists();

<h2><a name="insert-message"> </a><span  class="short-header">Вставка сообщения</span>Практическое руководство. Вставка сообщения в очередь</h2>

Чтобы вставить сообщение в существующую очередь, сначала создайте новый
**CloudQueueMessage**. Затем вызовите метод **AddMessage**. Для создания
**CloudQueueMessage** можно использовать либо строку (в формате UTF-8), либо массив **байтов**. Ниже приведен код, который создает очередь (если она не существует) и вставляет сообщение "Hello, World":

    // Получить учетную запись хранения из строки подключения.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Создать клиент очереди.
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Получить ссылку на очередь.
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Создать очередь, если она не существует.
    queue.CreateIfNotExists();

    // Создать сообщение и добавить его в очередь.
    CloudQueueMessage message = new CloudQueueMessage("Hello, World");
    queue.AddMessage(message);

<h2><a name="peek-message"></a><span  class="short-header">Просмотр следующего сообщения</span>Практическое руководство. Просмотр следующего сообщения</h2>

Вы можете просмотреть сообщение в начале очереди, не удаляя его из очереди, вызвав метод **PeekMessage**.

    // Получить учетную запись хранения из строки подключения
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Создать клиент очереди
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Получить ссылку на очередь
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Просмотреть следующее сообщение
    CloudQueueMessage peekedMessage = queue.PeekMessage();

	// Показать сообщение.
	Console.WriteLine(peekedMessage.AsString);

<h2><a name="change-contents"></a><span  class="short-header">Изменение содержимого сообщения</span>Практическое руководство. Изменение содержимого сообщения в очереди</h2>

Вы можете изменить содержимое сообщения непосредственно в очереди. Если сообщение представляет собой рабочую задачу, можно использовать эту функцию для обновления состояния рабочей задачи. Следующий код добавляет новое содержимое в очередь сообщений и продлевает время ожидания видимости еще на 60 секунд. Это сохраняет состояние работы, связанной с данным сообщением, и позволяет клиенту продолжить работу с сообщением на протяжении еще одной минуты. Этот метод можно использовать для отслеживания многошаговых рабочих процессов по сообщениям в очереди без необходимости начинать с самого начала в случае сбоя шага обработки в связи с ошибкой аппаратного или программного обеспечения. Обычно также сохраняется счетчик повторов; если количество повторов сообщения превысит *n* раз, его нужно удалить. Это обеспечивает защиту от сообщений, которые инициируют ошибку приложения при каждой попытке обработки.

    // Получить учетную запись хранения из строки подключения.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Создать клиент очереди.
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Получить ссылку на очередь.
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

	// Получить сообщение из очереди и обновить содержимое сообщения.
    CloudQueueMessage message = queue.GetMessage();
    message.SetMessageContent("Updated contents.") ;
    queue.UpdateMessage(message, 
        TimeSpan.FromSeconds(0.0),  // Make it visible immediately.
        MessageUpdateFields.Content | MessageUpdateFields.Visibility);

<h2><a name="get-message"></a><span  class="short-header">Удаление следующего сообщения из очереди</span>Практическое руководство. Удаление следующего сообщения из очереди</h2>

Код удаляет сообщение из очереди в два этапа. При вызове метода **GetMessage** вы получаете следующее сообщение в очереди. Сообщения, возвращаемые методом **GetMessage**, становятся невидимыми для другого кода, читающего сообщения из этой очереди. По умолчанию это сообщение остается невидимым в течение 30 секунд. Чтобы завершить удаление сообщения из очереди, необходимо вызвать метод **DeleteMessage**. Этот двухэтапный процесс удаления сообщения позволяет удостовериться, что если коду не удастся обработать сообщение из-за сбоя оборудования или программного обеспечения, другой экземпляр кода сможет получить то же сообщение и повторить попытку. Код вызывает метод **DeleteMessage** сразу после обработки сообщения.

    // Получить учетную запись хранения из строки подключения
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Создать клиент очереди
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Получить ссылку на очередь
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Получить следующее сообщение
    CloudQueueMessage retrievedMessage = queue.GetMessage();

    //Обработать сообщение менее чем за 30 секунд, а затем удалить сообщение
    queue.DeleteMessage(retrievedMessage);

<h2><a name="advanced-get"></a><span  class="short-header">Дополнительные варианты удаления из очереди</span>Практическое руководство. Дополнительные варианты для удаления сообщений из очереди</h2>

Способ извлечения сообщения из очереди можно настроить двумя способами.
Во-первых, можно получить пакет сообщений (до 32 сообщений). Во-вторых, можно задать более длительное или короткое время ожидания невидимости, чтобы предоставить коду больше или меньше времени на полную обработку каждого сообщения. В следующем примере кода метод **GetMessages** используется для получения 20 сообщений в одном вызове. Затем он обрабатывает каждое сообщение с помощью цикла **foreach**. Он также задает время ожидания невидимости 5 минут для каждого сообщения. Обратите внимание, что пятиминутный период начинается для всех сообщений одновременно, поэтому по прошествии 5 минут с момента вызова до **GetMessages** все сообщения, которые не были удалены, снова становятся видимыми.

    // Получить учетную запись хранения из строки подключения.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Создать клиент очереди.
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Получить ссылку на очередь.
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    foreach (CloudQueueMessage message in queue.GetMessages(20, TimeSpan.FromMinutes(5)))
    {
        // Process all messages in less than 5 minutes, deleting each message after processing.
        queue.DeleteMessage(message);
    }

<h2><a name="get-queue-length"></a><span  class="short-header">Получение длины очереди</span>Практическое руководство. Получение длины очереди</h2>

Вы можете узнать приблизительное количество сообщений в очереди. Метод **FetchAttributes** отправляет запрос в службу очередей на извлечение атрибутов очереди, включая количество сообщений. Свойство **ApproximateMethodCount**
возвращает последнее значение, полученное с использованием метода
**FetchAttributes**, без обращения к службе очередей.

    // Получить учетную запись хранения из строки подключения.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Создать клиент очереди.
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Получить ссылку на очередь.
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

	// Получить атрибуты очереди.
	queue.FetchAttributes();

    // Получить кэшированное приблизительное количество сообщений.
    int? cachedMessageCount = queue.ApproximateMessageCount;

	// Отобразить количество сообщений.
	Console.WriteLine("Number of messages in queue: " + cachedMessageCount);

<h2><a name="delete-queue"></a><span  class="short-header">Удаление очереди</span>Практическое руководство. Удаление очереди</h2>

Для удаления очереди и всех сообщений в ней вызовите метод **Delete** в объекте очереди.

    // Получить учетную запись хранения из строки подключения.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Создать клиент очереди.
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Получить ссылку на очередь.
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Удалить очередь.
    queue.Delete();

<h2><a name="next-steps"></a>Следующие шаги</h2>

Вы изучили основные сведения о хранилище очередей. Дополнительные сведения о более сложных задачах по использованию хранилища можно найти по следующим ссылкам.

<ul>
<li>Дополнительные сведения о доступных API-интерфейсах см. в справочной документации по службе очередей:
  <ul>
    <li><a href="http://msdn.microsoft.com/ru-ru/library/windowsazure/wa_storage_api_ref_reference_home.aspx">Справочник по клиентской библиотеке хранения для .NET</a>
    </li>
    <li><a href="http://msdn.microsoft.com/ru-ru/library/windowsazure/dd179355">Справочник по REST API</a></li>
  </ul>
</li>
<li>Дополнительные сведения о более сложных задачах, которые можно выполнить с хранилищем Windows Azure, см. в статье <a href="http://msdn.microsoft.com/ru-ru/library/windowsazure/gg433040.aspx">Хранение данных и доступ к ним в Windows Azure</a>.</li>
<li>Просмотрите дополнительные руководства, чтобы изучить дополнительные возможности хранения данных в Windows Azure.
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
  [Создание учетной записи хранения Windows Azure]: #create-account
  [Настройка строки подключения к хранилищу Windows Azure]: #setup-connection-string
  [Практическое руководство. Программный доступ к очередям с использованием .NET]: #access
  [Практическое руководство. Создание очереди]: #create-queue
  [Практическое руководство. Вставка сообщения в очередь]: #insert-message
  [Практическое руководство. Просмотр следующего сообщения]: #peek-message
  [Практическое руководство. Изменение содержимого сообщения в очереди]: #change-contents
  [Практическое руководство. Удаление следующего сообщения из очереди]: #get-message
  [Практическое руководство. Использование дополнительных параметров для удаления сообщений из очереди]: #advanced-get
  [Практическое руководство. Получение длины очереди]: #get-queue-length
  [Практическое руководство. Удаление очереди]: #delete-queue
  [Загрузка и установка Windows Azure SDK для .NET]: /ru-ru/develop/net/
  [Создание проекта Windows Azure в Visual Studio]: http://msdn.microsoft.com/ru-ru/library/windowsazure/ee405487.aspx 
  [CloudStorageAccount]: http://msdn.microsoft.com/ru-ru/library/microsoft.windowsazure.cloudstorageaccount_methods.aspx
  [Хранение данных и доступ к ним в Windows Azure]: http://msdn.microsoft.com/ru-ru/library/windowsazure/gg433040.aspx
  [Блог команды разработчиков хранилища Windows Azure]: http://blogs.msdn.com/b/windowsazurestorage/
  [Настройка строк подключения]: http://msdn.microsoft.com/ru-ru/library/windowsazure/ee758697.aspx
  [OData]: http://nuget.org/packages/Microsoft.Data.OData/5.0.2
  [Edm]: http://nuget.org/packages/Microsoft.Data.Edm/5.0.2
  [Spatial]: http://nuget.org/packages/System.Spatial/5.0.2


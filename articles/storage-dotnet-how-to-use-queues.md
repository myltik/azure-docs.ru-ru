<properties urlDisplayName="Queue Service" pageTitle="Вы научитесь использовать хранилище очередей из .NET в Microsoft Azure" metaKeywords="Get started Azure queue   Azure asynchronous processing   Azure queue   Azure queue storage   Azure queue .NET   Azure queue storage .NET   Azure queue C#   Azure queue storage C#" description="Learn how to use Microsoft Azure Queue storage to create and delete queues and insert, peek, get, and delete queue messages." metaCanonical="" disqusComments="1" umbracoNaviHide="1" services="storage" documentationCenter=".NET" title="How to use Microsoft Azure Queue Storage" authors="tamram" manager="adinah" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="tamram" />

# Использование хранилища очередей из .NET

В этом руководстве показано, как реализовать типичные сценарии с использованием
службы хранения очередей Microsoft Azure. Примеры кода написаны на C\#
и используют клиент хранения Azure для .NET. Здесь описаны такие сценарии, как **вставка**,
**просмотр**, **получение** и **удаление** сообщений очереди, а также
**создание и удаление очередей**. Дополнительные сведения см. в разделе 
[Дальнейшие действия][Дальнейшие действия].

> [WACOM.NOTE] В этом руководстве используется библиотека клиента хранения Azure для .NET 2.x и выше. Рекомендуется использовать библиотеку клиента хранения 4.x, которая доступна через [NuGet](https://www.nuget.org/packages/WindowsAzure.Storage/) или как часть [Пакета Azure SDK для .NET](/ru-ru/downloads/). См. [Практическое руководство. Программный доступ к хранилищу очередей][Практическое руководство. Программный доступ к хранилищу очередей] ниже для дополнительной информации о получении библиотеки клиента хранения.

<h2>Оглавление</h2>

-   [Что такое хранилище очередей?][Что такое хранилище очередей?]
-   [Основные понятия][Основные понятия]
-   [Создание учетной записи хранения Azure][Создание учетной записи хранения Azure]
-   [Настройка строки подключения к хранилищу Azure][Настройка строки подключения к хранилищу Azure]
-   [Практическое руководство. Программный доступ к хранилищу очередей][Практическое руководство. Программный доступ к хранилищу очередей]
-   [Практическое руководство. Создание очереди][Практическое руководство. Создание очереди]
-   [Практическое руководство. Вставка сообщения в очередь][Практическое руководство. Вставка сообщения в очередь]
-   [Практическое руководство. Просмотр следующего сообщения][Практическое руководство. Просмотр следующего сообщения]
-   [Практическое руководство. Изменение содержимого сообщения в очереди][Практическое руководство. Изменение содержимого сообщения в очереди]
-   [Практическое руководство. Удаление следующего сообщения из очереди][Практическое руководство. Удаление следующего сообщения из очереди]
-   [Практическое руководство. Дополнительные варианты удаления сообщений из очереди][Практическое руководство. Дополнительные варианты удаления сообщений из очереди]
-   [Практическое руководство. Получение длины очереди][Практическое руководство. Получение длины очереди]
-   [Практическое руководство. Удаление очереди][Практическое руководство. Удаление очереди]
-   [Дальнейшие действия][Дальнейшие действия]

[WACOM.INCLUDE [руководство-очередь-хранилище](../includes/howto-queue-storage.md)]

## <h2><a name="create-account"></a><span  class="short-header">Создание учетной записи</span>Создание учетной записи хранения Azure</h2>

[WACOM.INCLUDE [создание-хранилище-учетная запись](../includes/create-storage-account.md)]

## <h2><a name="setup-connection-string"></a><span  class="short-header">Настройка строки подключения</span>Настройка строки подключения к хранилищу Azure</h2>

[WACOM.INCLUDE [хранилище-настройка-подключение-строка](../includes/storage-configure-connection-string.md)]

## <a name="configure-access"> </a><span  class="short-header">Программный доступ</span>Практическое руководство: Программный доступ к хранилищу очередей

<h3>Получение сборки</h3>
Вы можете получить сборку "Microsoft.WindowsAzure.Storage.dll" с помощью NuGet. Щелкните правой кнопкой мыши проект в **Обозревателе решений** и выберите **Управление пакетами NuGet**.  Выполните в Интернете поиск "WindowsAzure.Storage" и нажмите кнопку **Установить**, чтобы установить пакет хранилища Azure и зависимые компоненты.

Библиотека `Microsoft.WindowsAzure.Storage.dll` также включена в пакет Azure SDK для .NET, который можно загрузить из <a href="http://www.windowsazure.com/ru-ru/develop/net/#">Центра разработчиков .NET</a>. Эта сборка устанавливается в каталог `%Program Files%\Microsoft SDKs\Windows Azure\.NET SDK\<sdk-version>\ref\`.

<h3>Объявления пространств имен</h3>
Добавьте следующие объявления пространств имен кода в начало любого файла\#,
в котором вы собираетесь получать доступ к хранилищу Azure программным способом:

    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;
	using Microsoft.WindowsAzure.Storage.File;

Убедитесь, что указана ссылка на сборку `Microsoft.WindowsAzure.Storage.dll`.

<h3>Получение строки подключения</h3>
Можно использовать тип **CloudStorageAccount** для представлений 
сведений о вашей учетной записи хранения. Если используется 
шаблон проекта Azure или ссылаетесь на 
пространство имен Microsoft.WindowsAzure.CloudConfigurationManager, 
с помощью типа **CloudConfigurationManager**
можно извлечь строку подключения и сведения об учетной записи
хранения из конфигурации службы Azure:

    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

Если вы создаете приложение без ссылки на Microsoft.WindowsAzure.CloudConfigurationManager, и строка подключения находится в `web.config` или `app.config` как показано выше, для получения строки подключения можно использовать **ConfigurationManager** для получения строки подключения.  Необходимо добавить ссылку на файл System.Configuration.dll в проект и добавить для него другое объявление пространства имен:

	using System.Configuration;
	...
	CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
		ConfigurationManager.ConnectionStrings["StorageConnectionString"].ConnectionString);

<h3>Зависимости ODataLib</h3>
Зависимости ODataLib в клиентской библиотеке хранения для .NET разрешаются через пакеты ODataLib (версия 5.0.2), доступные через NuGet, а не через службы данных WCF.  Библиотеки ODataLib можно загрузить напрямую или указать на них ссылку в проекте через NuGet.  Мы используем пакеты [OData], [Edm] и [Spatial].

<h2><a name="create-queue"></a><span  class="short-header">Создание очереди</span>Практическое руководство: Создание очереди</h2>

Объект **CloudQueueClient** позволяет ссылаться на объекты очередей.
Следующий код создает объект **CloudQueueClient**. Для всего кода в 
этом руководстве используется строка подключения хранилища,сохраненная в
конфигурации службы приложения Azure. Существуют также другие способы создания
объекта **CloudStorageAccount**. Дополнительные сведения см. в документации по [CloudStorageAccount][CloudStorageAccount]
.

    // Получить учетную запись хранения из строки подключения
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Создать клиент очереди
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

С помощью объекта **queueClient** получите ссылку на очередь, которую необходимо
использовать. Очередь можно создать, если она не существует.

    // Получить ссылку на очередь
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Создать очередь, если она не существует
    queue.CreateIfNotExists();

<h2><a name="insert-message"> </a><span  class="short-header">Вставка сообщения</span>Практическое руководство: Вставка сообщения в очередь</h2>

Чтобы вставить сообщение в существующую очередь, сначала создайте новый
объект **CloudQueueMessage**. Затем вызовите метод **AddMessage**. A
**CloudQueueMessage** можно использовать либо строку (в формате UTF-8),
либо массив **байтов**. Ниже приведен код, который создает очередь (если она
не существует) и вставляет сообщение "Hello, World":

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

<h2><a name="peek-message"></a><span  class="short-header">Просмотр следующего сообщения</span>Практическое руководство: Просмотр следующего сообщения</h2>

Вы можете просмотреть сообщение в начале очереди, не удаляя его
из очереди, вызвав метод **PeekMessage**.

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

<h2><a name="change-contents"></a><span  class="short-header">Изменение содержимого сообщения</span>Практическое руководство: Изменение содержимого сообщения в очереди</h2>

Вы можете изменить содержимое сообщения непосредственно в очереди. Если
сообщение представляет собой рабочую задачу, можно использовать эту функцию для обновления
состояния рабочей задачи. Следующий код добавляет новое содержимое в очередь сообщений
и продлевает время ожидания видимости еще на 60
секунд. Это сохраняет состояние работы, связанной с данным сообщением, и
позволяет клиенту продолжить работу с сообщением на протяжении еще одной минуты. Этот метод
можно использовать для отслеживания многошаговых рабочих процессов по сообщениям в очереди
без необходимости начинать с самого начала в случае
сбоя шага обработки в связи с ошибкой аппаратного или программного обеспечения. Обычно
также сохраняется счетчик повторов; если количество повторов для сообщения 
превысит *n* раз, его нужно удалить. Это обеспечивает защиту от сообщений,
которые инициируют ошибку приложения при каждой попытке обработки.

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
        TimeSpan.FromSeconds(0.0),  // Сделать ее видимой.
        MessageUpdateFields.Content | MessageUpdateFields.Visibility);

<h2><a name="get-message"></a><span  class="short-header">Удаление следующего сообщения из очереди</span>Практическое руководство: Удаление следующего сообщения из очереди</h2>

Код удаляет сообщение из очереди в два этапа. При вызове
**GetMessage** вы получаете следующее сообщение в очереди. Сообщение, возвращаемое
из **GetMessage** становится невидимыми для другого кода, читающего сообщения
из этой очереди. По умолчанию это сообщение остается невидимым в течение 30
секунд. Чтобы завершить удаление сообщения из очереди, необходимо
вызвать **DeleteMessage**. Этот двухэтапный процесс удаления сообщения
позволяет удостовериться, что если коду не удастся обработать сообщение из-за сбоя оборудования или
программного обеспечения, другой экземпляр кода сможет получить то же сообщение
и повторить попытку. Код вызывает метод **DeleteMessage** сразу после
обработки сообщения

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

<h2><a name="advanced-get"></a><span  class="short-header">Дополнительные варианты удаления из очереди</span>Практическое руководство: Дополнительные параметры для удаления сообщений из очереди</h2>

Способ извлечения сообщения из очереди можно настроить двумя способами.
Во-первых, можно получить пакет сообщений (до 32 сообщений). Во-вторых, можно задать
более длительное или короткое время ожидания невидимости, чтобы предоставить коду больше или меньше
времени на полную обработку каждого сообщения. В следующем примере кода метод
**GetMessages** используется для получения 20 сообщений в одном вызове. Затем он обрабатывает
каждое сообщение с помощью цикла **foreach**. Он также задает время ожидания невидимости
5 минут для каждого сообщения. Обратите внимание, что пятиминутный период начинается
для всех сообщений одновременно, поэтому по прошествии 5 минут с момента
вызова до **GetMessages**,   все сообщения, которые не были удалены,
снова становятся видимыми.

    // Получить учетную запись хранения из строки подключения.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Создать клиент очереди.
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Получить ссылку на очередь.
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    foreach (CloudQueueMessage message in queue.GetMessages(20, TimeSpan.FromMinutes(5)))
    {
        // Обработать сообщение менее чем за 5 минут, удалив после обработки каждое сообщение.
        queue.DeleteMessage(message);
    }

<h2><a name="get-queue-length"></a><span  class="short-header">Получение длины очереди</span>Практическое руководство: Получение длины очереди</h2>

Вы можете узнать приблизительное количество сообщений в очереди. Метод
**FetchAttributes** отправляет запрос в службу очередей на
извлечение атрибутов очереди, включая количество сообщений. **ApproximateMethodCount**
возвращает последнее значение, полученное методом
**FetchAttributes** без вызова службы очередей.

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
	Console.WriteLine("Количество сообщений в очереди: " + cachedMessageCount);

<h2><a name="delete-queue"></a><span  class="short-header">Удаление очереди</span>Практическое руководство: Удаление очереди</h2>

Для удаления очереди и всех сообщений в ней вызовите метод
**Delete** в объекте очереди.

    // Получить учетную запись хранения из строки подключения.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Создать клиент очереди.
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Получить ссылку на очередь.
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Удалить очередь.
    queue.Delete();

<h2><a name="next-steps"></a>Дальнейшие действия</h2>

Вы узнали основные сведения о хранилище очередей. По следующим ссылкам
можно найти дополнительные сведения о более сложных задачах по использованию хранилища.

<ul>
<li>Дополнительные сведения о доступных API-интерфейсах см. в справочной документации по службе очередей:
  <ul>
    <li><a href="http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409">Справочник по клиентской библиотеке хранения для .NET</a>
    </li>
    <li><a href="http://msdn.microsoft.com/ru-ru/library/windowsazure/dd179355">Справочник по REST API</a></li>
  </ul>
</li>
<li>Дополнительные сведения о более сложных задачах, которые можно выполнить с хранилищем Azure, см. в статье <a href="http://msdn.microsoft.com/ru-ru/library/windowsazure/gg433040.aspx">Хранение данных и доступ к ним в Azure</a>.</li>
<li>Дополнительные сведения о работе с хранилищем Azure в серверных процессах для веб-сайтов Azure см. в статье <a href="/ru-ru/documentation/articles/websites-dotnet-webjobs-sdk-get-started/">Начиная работу с пакетом Azure WebJobs SDK</a>.</li>
<li>Просмотрите дополнительные руководства, чтобы изучить дополнительные возможности хранения данных в Azure.
  <ul>
    <li>Использование <a href="/ru-ru/documentation/articles/storage-dotnet-how-to-use-tables/">табличного хранилища</a> для хранения структурированных данных.</li>
    <li>Использование <a href="/ru-ru/documentation/articles/storage-dotnet-how-to-use-blobs/">хранилища BLOB-объектов</a> для хранения неструктурированных данных.</li>
    <li>Использование <a href="/ru-ru/documentation/articles/sql-database-dotnet-how-to-use/">базы данных SQL</a> для хранения реляционных данных.</li>
  </ul>
</li>
</ul>



  [Дальнейшие действия]: #next-steps
  [Основные понятия]: #concepts
  [Создание учетной записи хранения Azure]: #create-account
  [Настройка строки подключения к хранилищу Azure]: #setup-connection-string
  [Практическое руководство. Программный доступ к хранилищу очередей]: #configure-access
  [Практическое руководство. Вставка сообщения в очередь]: #insert-message
  [Практическое руководство. Просмотр следующего сообщения]: #peek-message
  [Практическое руководство. Изменение содержимого сообщения в очереди]: #change-contents
  [Практическое руководство. Удаление следующего сообщения из очереди]: #get-message
  [Практическое руководство. Дополнительные варианты удаления сообщений из очереди]: #advanced-get
  [Практическое руководство. Получение длины очереди]: #get-queue-length
  [Практическое руководство. Удаление очереди]: #delete-queue
  [CloudStorageAccount]: http://msdn.microsoft.com/ru-ru/library/microsoft.windowsazure.cloudstorageaccount_methods.aspx
  [OData]: http://nuget.org/packages/Microsoft.Data.OData/5.0.2
  [Edm]: http://nuget.org/packages/Microsoft.Data.Edm/5.0.2
  [Spatial]: http://nuget.org/packages/System.Spatial/5.0.2

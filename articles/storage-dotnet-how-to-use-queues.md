<properties urlDisplayName="Queue Service" pageTitle="Вы научитесь использовать хранилище очередей из .NET в Microsoft Azure" metaKeywords="Get started Azure queue   Azure asynchronous processing   Azure queue   Azure queue storage   Azure queue .NET   Azure queue storage .NET   Azure queue C#   Azure queue storage C#" description="Вы узнаете дополнительные сведения об использовании службы очередей Microsoft Azure для создания и удаления очередей, вставки, получения и удаления сообщений." metaCanonical="" disqusComments="1" umbracoNaviHide="1" services="storage" documentationCenter=".NET" title="Вы научитесь использовать хранилище очередей Microsoft Azure." authors="tamram" manager="adinah" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="tamram" />

# Использование хранилища очередей из .NET

В этом руководстве показано, как реализовать типичные сценарии с использованием
службы хранения очередей Azure. Примеры написаны на C# и используют
библиотеку клиента хранения Azure для .NET. Здесь описаны такие сценарии, как **вставка**,
**просмотр**, **получение** и **удаление** сообщений очереди, а также
**создание и удаление очередей**. Дополнительные сведения об очередях см. в разделе
Дальнейшие действия[.][.]

> [WACOM.NOTE] В этом руководстве используется библиотека клиента хранения Azure для .NET 2.x и выше. Рекомендуется использовать библиотеку клиента хранения 4.x, которая доступна через [NuGet][NuGet] или как часть [Пакета Azure SDK для .NET][Пакета Azure SDK для .NET]. См. [Практическое руководство. Программный доступ к хранилищу очередей][Практическое руководство. Программный доступ к хранилищу очередей] ниже для дополнительной информации о получении библиотеки клиента хранения.

## Оглавление

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
-   [Дальнейшие действия][.]

[WACOM.INCLUDE [руководство-очередь-хранилище](../includes/howto-queue-storage.md)]

## 

## <a name="create-account"></a><span class="short-header">Создание учетной записи</span>Создание учетной записи хранения Azure

</h2>
[WACOM.INCLUDE [создание-хранилище-учетная запись](../includes/create-storage-account.md)]

## 

## <a name="setup-connection-string"></a><span class="short-header">Настройка строки подключения</span>Настройка строки подключения к хранилищу Azure

</h2>
[WACOM.INCLUDE [хранилище-настройка-подключение-строка](../includes/storage-configure-connection-string.md)]

## <a name="configure-access"> </a><span class="short-header">Программный доступ</span>Практическое руководство: Программный доступ к хранилищу очередей

### Получение сборки

Можно использовать NuGet для получения сборки `Microsoft.WindowsAzure.Storage.dll`. Щелкните правой кнопкой мыши проект в **Обозревателе решений** и выберите **Управление пакетами NuGet**. Выполните в Интернете поиск "WindowsAzure.Storage" и нажмите кнопку **Установить**, чтобы установить пакет хранилища Azure и зависимые компоненты.

`Microsoft.WindowsAzure.Storage.dll` также включена в состав пакета Azure SDK для .NET, который можно загрузить из [Центра разработчиков .NET][Центра разработчиков .NET]. Сборка устанавливается в каталог `%Program Files%\Microsoft SDKs\Windows Azure\.NET SDK%Program Files%\Microsoft SDKs\Windows Azure\.NET SDK\<sdk-version>\ref\`lt;sdk-version\>\\ref\\</code>.

### Объявления пространств имен

Добавьте следующие объявления пространств имен кода в начало любого файла C#
,в котором вы собираетесь получать доступ к хранилищу Azure программным способом:

    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;
    using Microsoft.WindowsAzure.Storage.Queue;

Убедитесь, что указана ссылка на сборку `Microsoft.WindowsAzure.Storage.dll`.

### Получение строки подключения

Для представлений сведений о вашей учетной записи хранения можно использовать тип **CloudStorageAccount**.
 Если вы используете шаблон проекта
Azure или ссылаетесь на
пространство имен Microsoft.WindowsAzure.CloudConfigurationManager, с помощью типа
**CloudConfigurationManager**
 можно извлечь строку подключения и сведения об учетной записи хранения из
конфигурации службы Azure:

    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

Если вы создаете приложение без ссылки на пространство имен Microsoft.WindowsAzure.CloudConfigurationManager и строка подключения находится в файле `web.config` или `app.config`, как показано выше, то для получения строки подключения можно использовать **ConfigurationManager**. Необходимо добавить ссылку на файл System.Configuration.dll в проект и добавить для него другое объявление пространства имен:

    using System.Configuration;
    ...
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        ConfigurationManager.ConnectionStrings["StorageConnectionString"].ConnectionString);

### Зависимости ODataLib

Зависимости ODataLib в клиентской библиотеке хранения для .NET разрешаются через пакеты ODataLib (версия 5.0.2), доступные через NuGet, а не через службы данных WCF. Библиотеки ODataLib можно загрузить напрямую или указать на них ссылку в проекте через NuGet. Мы используем пакеты ODataLib [OData][OData], [Edm][Edm] и [Spatial][Spatial].

## <a name="create-queue"></a><span class="short-header">Создание очереди</span>Практическое руководство: Создание очереди

Объект **CloudQueueClient** позволяет ссылаться на объекты очередей.
Следующий код создает объект **CloudQueueClient**. Для всего кода
в этом руководстве используется строка подключения
хранилища, сохраненная в конфигурации службы приложения Azure. Существуют также другие способы создания
объекта **CloudStorageAccount**. Дополнительные сведения см. в документации по [CloudStorageAccount][CloudStorageAccount]
.

    // Retrieve storage account from connection string
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

С помощью объекта **queueClient** получите ссылку на очередь, которую необходимо
использовать. Очередь можно создать, если она не существует.

    // Retrieve a reference to a queue
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Create the queue if it doesn't already exist
    queue.CreateIfNotExists();

## <a name="insert-message"> </a><span class="short-header">Вставка сообщения</span>Практическое руководство: Вставка сообщения в очередь

Чтобы вставить сообщение в существующую очередь, сначала создайте новый объект
**CloudQueueMessage** Затем вызовите метод **AddMessage**. Для создания
**CloudQueueMessage** можно использовать либо строку (в формате
 UTF-8), либо массив типа **byte**. Ниже приведен код, который создает очередь (если она
не существует) и вставляет сообщение "Привет, мир":

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

## <a name="peek-message"></a><span class="short-header">Просмотр следующего сообщения</span>Практическое руководство: Просмотр следующего сообщения

Вы можете просмотреть сообщение в начале очереди, не удаляя его из
очереди, вызвав метод **peek\_messages()**.

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

## <a name="change-contents"></a><span class="short-header">Изменение содержимого сообщения</span>Практическое руководство: Изменение содержимого сообщения в очереди

Вы можете изменить содержимое сообщения непосредственно в очереди. Если
сообщение представляет собой рабочую задачу, можно использовать эту
функцию для обновления состояния рабочей задачи. Следующий код добавляет новое содержимое в
очередь сообщений и продлевает время ожидания видимости еще на 60
секунд. Это сохраняет состояние работы, связанной с данным сообщением, и
позволяет клиенту продолжить работу с сообщением на протяжении еще одной минуты. Этот
метод можно использовать для отслеживания многошаговых
рабочих процессов по сообщениям в очереди без необходимости
начинать с самого начала в случае сбоя шага обработки в связи с ошибкой аппаратного или программного обеспечения. Обычно
также сохраняется счетчик повторов; если количество повторов сообщения
превысит *n* раз, его нужно удалить. Это обеспечивает защиту от
сообщений, которые инициируют ошибку приложения при каждой попытке обработки.

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

## <a name="get-message"></a><span class="short-header">Удаление следующего сообщения из очереди</span>Практическое руководство: Удаление следующего сообщения из очереди

Код удаляет сообщение из очереди в два этапа. При вызове метода
**GetMessage** вы получаете следующее сообщение в очереди. Сообщение,возвращаемое методом
**GetMessage**, становится невидимым для другого кода, считывающего сообщения из
этой очереди. По умолчанию это сообщение остается невидимым в течение 30
секунд. Чтобы завершить удаление сообщения из очереди, необходимо также
вызвать метод **DeleteMessage**. Этот двухэтапный процесс удаления сообщения
позволяет удостовериться, что если коду не удастся обработать сообщение из-за сбоя оборудования или
программного обеспечения, другой экземпляр кода сможет получить то же сообщение и
повторить попытку. Код вызывает метод **deleteMessage** сразу после обработки
сообщения.

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

## <a name="advanced-get"></a><span class="short-header">Дополнительные варианты удаления из очереди</span>Практическое руководство: Дополнительные параметры для удаления сообщений из очереди

Способ извлечения сообщения из очереди можно настроить двумя способами.
 Во-первых, можно получить пакет сообщений (до 32 сообщений). Во-вторых, можно задать
более длительное или короткое время ожидания видимости, чтобы предоставить коду больше или меньше
времени на полную обработку каждого сообщения. В следующем примере кода метод
**GetMessages** используется для получения 20 сообщений в одном вызове. Затем он обрабатывает
 каждое сообщение с помощью цикла **foreach**. Он также задает время
ожидания невидимости 5 минут для каждого сообщения. Обратите внимание, что пятиминутный
период начинается для всех сообщений одновременно, поэтому по прошествии 5 минут с момента
вызова **GetMessages** все сообщения, которые не были удалены, снова
становятся видимыми.

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

## <a name="get-queue-length"></a><span class="short-header">Получение длины очереди</span>Практическое руководство: Получение длины очереди

Вы можете узнать приблизительное количество сообщений в очереди. Метод
**FetchAttributes** отправляет запрос в службу очередей на
извлечение атрибутов очереди, включая количество сообщений. Свойство **ApproximateMethodCount**
 возвращает последнее значение, полученное с использованием метода
**FetchAttributes**, без обращения к службе очередей.

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

## <a name="delete-queue"></a><span class="short-header">Удаление очереди</span>Практическое руководство: Удаление очереди

Для удаления очереди и всех сообщений в ней вызовите метод
**Удалить** в объекте очереди.

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Delete the queue.
    queue.Delete();

## <a name="next-steps"></a>Дальнейшие действия

Вы изучили основные сведения о хранилище очередей. Дополнительные
сведения о более сложных задачах по использованию хранилища можно найти по следующим ссылкам.

-   Дополнительные сведения о доступных API-интерфейсах см. в справочной документации по службе очередей:
    -   [Справочник по клиентской библиотеке хранения для .NET][Справочник по клиентской библиотеке хранения для .NET]
    -   [Справочник по REST API][Справочник по REST API]
-   Дополнительные сведения о более сложных задачах, которые можно выполнить с хранилищем Azure, см. в статье [Хранение данных и доступ к ним в Azure][Хранение данных и доступ к ним в Azure].
-   Дополнительные сведения о работе с хранилищем Azure в серверных процессах для веб-сайтов Azure см. в статье [Начиная работу с пакетом Azure WebJobs SDK][Начиная работу с пакетом Azure WebJobs SDK].
-   Просмотрите дополнительные руководства, чтобы изучить дополнительные возможности хранения данных в Azure.
    -   Использование [Табличного хранилища][Табличного хранилища] для хранения структурированных данных.
    -   Использование [Хранилища Blob-объектов][Хранилища Blob-объектов] для хранения неструктурированных данных.
    -   Использование [Базы данных SQL][Базы данных SQL] для хранения реляционных данных.

  [.]: #next-steps
  [NuGet]: https://www.nuget.org/packages/WindowsAzure.Storage/
  [Пакета Azure SDK для .NET]: /ru-ru/downloads/
  [Практическое руководство. Программный доступ к хранилищу очередей]: #configure-access
  [Что такое хранилище очередей?]: #what-is
  [Основные понятия]: #concepts
  [Создание учетной записи хранения Azure]: #create-account
  [Настройка строки подключения к хранилищу Azure]: #setup-connection-string
  [Практическое руководство. Создание очереди]: #create-queue
  [Практическое руководство. Вставка сообщения в очередь]: #insert-message
  [Практическое руководство. Просмотр следующего сообщения]: #peek-message
  [Практическое руководство. Изменение содержимого сообщения в очереди]: #change-contents
  [Практическое руководство. Удаление следующего сообщения из очереди]: #get-message
  [Практическое руководство. Дополнительные варианты удаления сообщений из очереди]: #advanced-get
  [Практическое руководство. Получение длины очереди]: #get-queue-length
  [Практическое руководство. Удаление очереди]: #delete-queue
  [Центра разработчиков .NET]: http://www.windowsazure.com/ru-ru/develop/net/#
  [OData]: http://nuget.org/packages/Microsoft.Data.OData/5.0.2
  [Edm]: http://nuget.org/packages/Microsoft.Data.Edm/5.0.2
  [Spatial]: http://nuget.org/packages/System.Spatial/5.0.2
  [CloudStorageAccount]: http://msdn.microsoft.com/ru-ru/library/microsoft.windowsazure.cloudstorageaccount_methods.aspx
  [Справочник по клиентской библиотеке хранения для .NET]: http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409
  [Справочник по REST API]: http://msdn.microsoft.com/ru-ru/library/windowsazure/dd179355
  [Хранение данных и доступ к ним в Azure]: http://msdn.microsoft.com/ru-ru/library/windowsazure/gg433040.aspx
  [Начиная работу с пакетом Azure WebJobs SDK]: /ru-ru/documentation/articles/websites-dotnet-webjobs-sdk-get-started/
  [Табличного хранилища]: /ru-ru/documentation/articles/storage-dotnet-how-to-use-tables/
  [Хранилища Blob-объектов]: /ru-ru/documentation/articles/storage-dotnet-how-to-use-blobs/
  [Базы данных SQL]: /ru-ru/documentation/articles/sql-database-dotnet-how-to-use/

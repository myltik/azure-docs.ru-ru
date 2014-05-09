<properties linkid="dev-net-how-to-use-queue-storage-service-java" urlDisplayName="Служба очередей" pageTitle="Использование службы очередей (Java) | Microsoft Azure" metaKeywords="Служба очередей Azure, служба хранилища очередей Azure, просмотр очередей, вставка сообщений в очереди, получение сообщений из очередей, удаление сообщений в очередях, создание сообщений, удаление сообщений, служба очередей Java" description="Сведения об использовании службы очередей Azure для создания и удаления очередей, а также вставки, получения и удаления сообщений. Примеры написаны на языке Java." metaCanonical="" services="storage" documentationCenter="Java" title="Использование службы хранилища очередей из Java" authors="" solutions="" manager="" editor="" />





# Использование службы хранилища очередей из Java

В этом руководстве показано, как реализовать типичные сценарии
с использованием службы хранилища очередей Azure. Примеры написаны на Java и используют [Пакет Azure SDK для Java][]. Здесь описаны такие сценарии,
как вставка, просмотр, получение и удаление сообщений очереди,
а также создание и удаление очередей. Дополнительные сведения об очередях см. в разделе
[Дальнейшие действия](#NextSteps).

## <a name="Contents"> </a>Оглавление

* [Что такое хранилище очередей?](#what-is)
* [Основные понятия](#Concepts)
* [Создание учетной записи хранения Azure](#CreateAccount)
* [Создание приложения Java](#CreateApplication)
* [Настройка приложения для доступа к хранилищу очередей](#ConfigureStorage)
* [Настройка строки подключения к хранилищу Azure](#ConnectionString)
* [Практическое руководство. Создание очереди](#create-queue)
* [Практическое руководство. Добавление сообщения в очередь](#add-message)
* [Практическое руководство. Просмотр следующего сообщения](#peek-message)
* [Практическое руководство. Удаление следующего сообщения из очереди](#dequeue-message)
* [Практическое руководство. Изменение содержимого сообщения в очереди](#change-message)
* [Дополнительные варианты удаления сообщений из очереди](#additional-options)
* [Практическое руководство. Получение длины очереди](#get-queue-length)
* [Практическое руководство. Удаление очереди](#delete-queue)
* [Следующие шаги](#NextSteps)

[WACOM.INCLUDE [howto-queue-storage](../includes/howto-queue-storage.md)]

<h2><a id="CreateAccount"></a>Создание учетной записи хранения Azure</h2>

[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

## <a name="CreateApplication"> </a>Создание приложения Java

В этом руководстве будут использоваться компоненты хранилища, которые могут быть вызваны локально в приложении Java или в коде, работающем в веб-роли или рабочей роли в Azure. Предполагается, что комплект разработчика Java (JDK) уже загружен и установлен и что вы, следуя инструкциям, приведенным в статье [Загрузка пакета Azure SDK для Java][Пакет Azure SDK для Java], установили библиотеки Azure для Java и пакет Azure SDK и создали учетную запись хранения Azure в своей подписке Azure. Для создания приложения можно использовать любые средства
разработки, включая Блокнот. Все, что нужно — это возможность компилировать
проект Java и сослаться на библиотеки Azure для Java.

## <a name="ConfigureStorage"> </a>Настройка приложения для доступа к хранилищу очередей

Если нужно использовать API-интерфейсы Azure для доступа к очередям,
добавьте следующие инструкции импорта в верхнюю часть файла Java:

    // Include the following imports to use queue APIs
    import com.microsoft.windowsazure.services.core.storage.*;
    import com.microsoft.windowsazure.services.queue.client.*;

## <a name="ConnectionString"> </a>Настройка строки подключения к хранилищу Azure

Клиент хранилища Azure использует строку подключения с целью
хранения конечных точек и учетных данных для доступа к службам
управления данными. При работе
в клиентском приложении необходимо указать для хранилища строку подключения
в следующем формате, используя имя своей учетной записи хранения и первичный ключ
доступа для учетной записи хранения, указанные на портале управления
значениями *AccountName* и *AccountKey*. В этом примере показано,
как объявить статическое поле для размещения строки подключения:

    // Define the connection-string with your values
    public static final String storageConnectionString = 
        "DefaultEndpointsProtocol=http;" + 
        "AccountName=your_storage_account;" + 
        "AccountKey=your_storage_account_key";

Если приложение выполняется в роли Azure, эта строка может
храниться в файле конфигурации службы, ServiceConfiguration.cscfg,
и для доступа к ней можно использовать вызов
метода RoleEnvironment.getConfigurationSettings. Ниже приведен пример
получения строки подключения из элемента **Setting** с именем
*StorageConnectionString* в файле конфигурации службы:

    // Retrieve storage account from connection-string
    String storageConnectionString = 
        RoleEnvironment.getConfigurationSettings().get("StorageConnectionString");

## <a name="create-queue"> </a>Практическое руководство. Создание очереди

Объект CloudQueueClient позволяет ссылаться на объекты очередей. Следующий
код создает объект CloudQueueClient.

Весь код в этом руководстве использует строку подключения к хранилищу, объявленную
одним из двух описанных выше способов. Существуют также другие способы создания
объектов CloudStorageAccount. Обратитесь к документации по Javadocs для получения
дополнительных сведений о CloudStorageAccount.

    // Retrieve storage account from connection-string
    CloudStorageAccount storageAccount = 
        CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

С помощью объекта CloudQueueClient получите ссылку на очередь,
которую необходимо использовать. Очередь можно создать, если она не существует.

    // Retrieve a reference to a queue
    CloudQueue queue = queueClient.getQueueReference("myqueue");

    // Create the queue if it doesn't already exist
    queue.createIfNotExist();

## <a name="add-message"> </a>Практическое руководство. Добавление сообщения в очередь

Чтобы вставить сообщение в существующую очередь, сначала создайте новый
CloudQueueMessage. Затем вызовите метод addMessage. Для создания CloudQueueMessage
можно использовать либо строку (в формате UTF-8), либо массив байтов.
Ниже приведен код, который создает очередь (если она не существует)
и вставляет сообщение "Hello, World":

    // Retrieve storage account from connection-string
    CloudStorageAccount storageAccount = 
        CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Retrieve a reference to a queue
    CloudQueue queue = queueClient.getQueueReference("myqueue");

    // Create the queue if it doesn't already exist
    queue.createIfNotExist();

    // Create a message and add it to the queue
    CloudQueueMessage message = new CloudQueueMessage("Hello, World");
    queue.addMessage(message);

## <a name="peek-message"> </a>Практическое руководство. Просмотр следующего сообщения

Вы можете просмотреть сообщение в начале очереди, не удаляя его из
очереди, вызвав метод peekMessage.

    // Retrieve storage account from connection-string
    CloudStorageAccount storageAccount = 
        CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Retrieve a reference to a queue
    CloudQueue queue = queueClient.getQueueReference("myqueue");

    // Peek at the next message
    CloudQueueMessage peekedMessage = queue.peekMessage();

## <a name="dequeue-message"> </a>Практическое руководство. Удаление следующего сообщения из очереди

Код удаляет сообщение из очереди в два этапа. При вызове
метода retrieveMessage вы получаете следующее сообщение в очереди. Сообщение, возвращаемое
методом retrieveMessage, становится невидимым для другого кода,
считывающего сообщения из этой очереди. По умолчанию это сообщение остается
невидимым в течение 30 секунд. Чтобы завершить удаление сообщения из очереди,
необходимо вызвать метод deleteMessage. Этот двухэтапный процесс удаления сообщения позволяет удостовериться,
что если коду не удастся обработать сообщение из-за сбоя оборудования или программного обеспечения,
другой экземпляр кода сможет получить то же сообщение и повторить
попытку. Код вызывает метод deleteMessage сразу после
обработки сообщения.

    // Retrieve storage account from connection-string
    CloudStorageAccount storageAccount = 
        CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Retrieve a reference to a queue
    CloudQueue queue = queueClient.getQueueReference("myqueue");

    // Retrieve the first visible message in the queue
    CloudQueueMessage retrievedMessage = queue.retrieveMessage();

    // Process the message in less than 30 seconds, and then delete the message.
    queue.deleteMessage(retrievedMessage);

## <a name="change-message"> </a>Практическое руководство. Изменение содержимого сообщения в очереди

Вы можете изменить содержимое сообщения непосредственно в очереди. Если сообщение представляет собой рабочую задачу, можно использовать эту функцию для обновления состояния рабочей задачи. Следующий код добавляет новое содержимое в очередь сообщений и продлевает время ожидания видимости еще на 60 секунд. Это сохраняет состояние работы, связанной с данным сообщением, и позволяет клиенту продолжить работу с сообщением на протяжении еще одной минуты. Этот метод можно использовать для отслеживания многошаговых рабочих процессов по сообщениям в очереди без необходимости начинать с самого начала в случае сбоя шага обработки в связи с ошибкой аппаратного или программного обеспечения. Обычно
также сохраняется счетчик повторов; если количество повторов для сообщения превысит n раз,
его нужно удалить. Это обеспечивает защиту от сообщений, которые
инициируют ошибку приложения при каждой попытке обработки.

    // Retrieve storage account from connection-string
    CloudStorageAccount storageAccount = 
        CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Retrieve a reference to a queue
    CloudQueue queue = queueClient.getQueueReference("myqueue");

    // Retrieve the first visible message in the queue
    CloudQueueMessage message = queue.retrieveMessage();

    // Modify the message content and set it to be visible in 60 seconds
    message.setMessageContent("Updated contents.");
    EnumSet<MessageUpdateFields> updateFields = 
        EnumSet.of(MessageUpdateFields.CONTENT, MessageUpdateFields.VISIBILITY);
    queue.updateMessage(message, 60, updateFields, null, null);

## <a name="additional-options"> </a>Дополнительные варианты удаления сообщений из очереди

Способ извлечения сообщения из очереди можно настроить двумя способами.
Во-первых, можно получить пакет сообщений (до 32 сообщений). Во-вторых, можно задать более длительное или короткое время ожидания невидимости, чтобы предоставить коду больше или меньше времени на полную обработку каждого сообщения.

В следующем примере кода метод retrieveMessages используется для получения 20
сообщений в одном вызове. Затем он обрабатывает каждое сообщение с помощью
цикла **for**. Пример также задает время ожидания невидимости в размере пяти минут
(300 секунд) для каждого сообщения. Обратите внимание, что пятиминутный период начинается для всех
сообщений одновременно, поэтому по прошествии пяти минут с момента
вызова retrieveMessages все сообщения, которые не были удалены,
снова становятся видимыми.

    // Retrieve storage account from connection-string
    CloudStorageAccount storageAccount = 
        CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Retrieve a reference to a queue
    CloudQueue queue = queueClient.getQueueReference("myqueue");

    // Retrieve 20 messages from the queue with a visibility timeout of 300 seconds
    for (CloudQueueMessage message : queue.retrieveMessages(20, 300, null, null)) {
        // Do processing for all messages in less than 5 minutes, 
        // deleting each message after processing.
        queue.deleteMessage(message);
    }

## <a name="get-queue-length"> </a>Практическое руководство. Получение длины очереди

Вы можете узнать приблизительное количество сообщений в очереди. Метод
downloadAttributes запрашивает у службы очередей несколько текущих
значений, включая количество сообщений в очереди. Счетчик указывает
число лишь приблизительно, так как сообщения могут добавляться или
удаляться после ответа службы очередей на ваш запрос. Метод getApproximateMethodCount
возвращает последнее значение, полученное при вызове
downloadAttributes без обращения к службе очередей.

    // Retrieve storage account from connection-string
    CloudStorageAccount storageAccount = 
        CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Retrieve a reference to a queue
    CloudQueue queue = queueClient.getQueueReference("myqueue");

    // Download the approximate message count from the server
    queue.downloadAttributes();

    // Retrieve the newly cached approximate message count
    long cachedMessageCount = queue.getApproximateMessageCount();

## <a name="delete-queue"> </a>Практическое руководство. Удаление очереди

Для удаления очереди и всех сообщений в ней вызовите метод delete
для объекта очереди.

    // Retrieve storage account from connection-string
    CloudStorageAccount storageAccount = 
        CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Retrieve a reference to a queue
    CloudQueue queue = queueClient.getQueueReference("myqueue");

    // Delete the queue
    queue.delete();

## <a name="NextSteps"> </a>Дальнейшие действия

Вы изучили основные сведения о хранилище очередей. Дополнительные сведения о более сложных задачах по использованию хранилища можно найти по следующим ссылкам.

-   См. справочник MSDN: [Хранение данных и доступ к ним в Windows
    Azure]
-   Посетите блог команды разработчиков хранилища Azure <http://blogs.msdn.com/b/windowsazurestorage/>

[Пакет Azure SDK для Java]: http://www.windowsazure.com/ru-ru/develop/java/
[Хранение данных и доступ к ним в Azure]: http://msdn.microsoft.com/ru-ru/library/windowsazure/gg433040.aspx


<properties
	pageTitle="Использование хранилища очередей из Java | Microsoft Azure"
	description="Вы узнаете, как использовать службы очередей Azure для создания и удаления очередей, вставки, получения и удаления сообщений. Примеры кода написаны на Java."
	services="storage"
	documentationCenter="java"
	authors="rmcmurray"
	manager="wpickett"
	editor="jimbe"/>

<tags
	ms.service="storage"
	ms.workload="storage"
	ms.tgt_pltfrm="na"
	ms.devlang="Java"
	ms.topic="article"
	ms.date="12/01/2015"
	ms.author="micurd"/>

# Использование хранилища очередей из Java

[AZURE.INCLUDE [storage-selector-queue-include](../../includes/storage-selector-queue-include.md)]

## Обзор

В этом руководстве показано, как реализовать типичные сценарии с использованием службы хранения очередей Azure. Примеры написаны на Java и используют [Пакет SDK хранилища Azure для Java][]. Здесь описаны такие сценарии, как **вставка**, **просмотр**, **получение** и **удаление** сообщений очереди, а также **создание** и **удаление очередей**. Дополнительные сведения об очередях см. в разделе [Дальнейшие действия](#NextSteps).

Примечание. Пакет SDK доступен для разработчиков, которые используют хранилище Azure на устройствах под управлением Android. Дополнительные сведения см. в разделе [Пакет SDK хранилища Azure для Android][].

[AZURE.INCLUDE [storage-queue-concepts-include](../../includes/storage-queue-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## Создание приложения Java

В этом руководстве будут использоваться компоненты хранилища, которые могут быть вызваны локально в приложении Java или в коде, работающем в веб-роли или рабочей роли в Azure.

Чтобы это сделать, необходимо установить комплект разработчика Java (JDK) и создать учетную запись Azure в подписке Azure. После того, как это будет сделано, необходимо убедиться, что ваша система разработки отвечает минимальным требованиям и зависимостям, указанным в репозитории [Пакет SDK хранилища Azure для Java][] в GitHub. Если ваша система отвечает указанным требованиям можно приступить к выполнению инструкций по загрузке библиотек хранилища Azure для Java из репозитория и их установке на своей системе. После завершение этих задач вы сможете приступить к созданию приложения Java с использованием примеров из данной статьи.

## Настройка приложения для доступа к хранилищу очередей

Если нужно использовать API-интерфейсы Azure для доступа к очередям, добавьте следующие инструкции импорта в верхнюю часть файла Java:

    // Include the following imports to use queue APIs.
    import com.microsoft.azure.storage.*;
    import com.microsoft.azure.storage.queue.*;

## Настройка строки подключения к службе хранилища Azure

Клиент хранилища Azure использует строку подключения с целью хранения конечных точек и учетных данных для доступа к службам управления данными. При работе в клиентском приложении необходимо указать для хранилища строку подключения в следующем формате, используя имя своей учетной записи хранения и первичную клавишу доступа для учетной записи хранения, указанные на [портале Azure](portal.azure.com) значениями *AccountName* и *AccountKey*. В этом примере показано, как объявить статическое поле для размещения строки подключения:

    // Define the connection-string with your values.
    public static final String storageConnectionString =
        "DefaultEndpointsProtocol=http;" +
        "AccountName=your_storage_account;" +
        "AccountKey=your_storage_account_key";

Если приложение выполняется в роли на платформе Microsoft Azure, эта строка может храниться в файле конфигурации службы *ServiceConfiguration.cscfg*, для доступа к которой можно использовать вызов метода **RoleEnvironment.getConfigurationSettings**. Ниже приведен пример получения строки подключения из элемента **Setting** с именем *StorageConnectionString* в файле конфигурации службы:

    // Retrieve storage account from connection-string.
    String storageConnectionString =
        RoleEnvironment.getConfigurationSettings().get("StorageConnectionString");

В приведенных ниже примерах предполагается, что вы использовали одно из этих двух определений для получения строки подключения к хранилищу.

## Практическое руководство. Создание очереди

Объект **CloudQueueClient** позволяет ссылаться на объекты очередей. Следующий код создает объект **CloudQueueClient**. (Примечание. Есть другие способы создания объектов **CloudStorageAccount**. Дополнительные сведения см. в разделе **CloudStorageAccount** в [справочнике по пакету SDK для клиента хранилища Azure].)

С помощью объекта **CloudqueueClient** получите ссылку на очередь, которую необходимо использовать. Очередь можно создать, если она не существует.

    try
    {
    	// Retrieve storage account from connection-string.
    	CloudStorageAccount storageAccount =
	       CloudStorageAccount.parse(storageConnectionString);

	   // Create the queue client.
	   CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

	   // Retrieve a reference to a queue.
	   CloudQueue queue = queueClient.getQueueReference("myqueue");

	   // Create the queue if it doesn't already exist.
	   queue.createIfNotExists();
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## Практическое руководство. Добавление сообщения в очередь

Чтобы вставить сообщение в существующую очередь, сначала создайте новый объект**CloudQueueMessage** Затем вызовите метод **AddMessage**. Для создания **CloudQueueMessage** можно использовать либо строку (в формате UTF-8), либо массив типа byte. Ниже приведен код, который создает очередь (если она не существует) и вставляет сообщение "Привет, мир":

    try
    {
    	// Retrieve storage account from connection-string.
    	CloudStorageAccount storageAccount =
	       CloudStorageAccount.parse(storageConnectionString);

    	// Create the queue client.
    	CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    	// Retrieve a reference to a queue.
    	CloudQueue queue = queueClient.getQueueReference("myqueue");

    	// Create the queue if it doesn't already exist.
    	queue.createIfNotExists();

    	// Create a message and add it to the queue.
    	CloudQueueMessage message = new CloudQueueMessage("Hello, World");
    	queue.addMessage(message);
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## Практическое руководство. Просмотр следующего сообщения

Вы можете просмотреть сообщение в начале очереди, не удаляя его из очереди, вызвав метод **peekMessage**.

    try
    {
    	// Retrieve storage account from connection-string.
    	CloudStorageAccount storageAccount =
	       CloudStorageAccount.parse(storageConnectionString);

    	// Create the queue client.
    	CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    	// Retrieve a reference to a queue.
    	CloudQueue queue = queueClient.getQueueReference("myqueue");

    	// Peek at the next message.
    	CloudQueueMessage peekedMessage = queue.peekMessage();

    	// Output the message value.
    	if (peekedMessage != null)
    	{
		  System.out.println(peekedMessage.getMessageContentAsString());
	   }
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## Практическое руководство. Изменение содержимого сообщения в очереди

Вы можете изменить содержимое сообщения непосредственно в очереди. Если сообщение представляет собой рабочую задачу, можно использовать эту функцию для обновления состояния рабочей задачи. Следующий код добавляет новое содержимое в очередь сообщений и продлевает время ожидания видимости еще на 60 секунд. Это сохраняет состояние работы, связанной с данным сообщением, и позволяет клиенту продолжить работу с сообщением на протяжении еще одной минуты. Этот метод можно использовать для отслеживания многошаговых рабочих процессов по сообщениям в очереди без необходимости начинать с самого начала в случае сбоя шага обработки в связи с ошибкой аппаратного или программного обеспечения. Обычно также сохраняется счетчик повторов. Если количество повторов сообщения превысит *n* раз, его нужно удалить. Это обеспечивает защиту от сообщений, которые инициируют ошибку приложения при каждой попытке обработки.

Приведенный ниже пример кода выполняет поиск в очереди сообщения, находит первое сообщение, которое содержит "Привет, мир", затем изменяет контент сообщения и выполняет выход.

    try
    {
        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount =
            CloudStorageAccount.parse(storageConnectionString);

    	// Create the queue client.
    	CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    	// Retrieve a reference to a queue.
    	CloudQueue queue = queueClient.getQueueReference("myqueue");

        // The maximum number of messages that can be retrieved is 32.
        final int MAX_NUMBER_OF_MESSAGES_TO_PEEK = 32;

        // Loop through the messages in the queue.
        for (CloudQueueMessage message : queue.retrieveMessages(MAX_NUMBER_OF_MESSAGES_TO_PEEK,1,null,null))
        {
            // Check for a specific string.
            if (message.getMessageContentAsString().equals("Hello, World"))
            {
                // Modify the content of the first matching message.
                message.setMessageContent("Updated contents.");
                // Set it to be visible in 30 seconds.
                EnumSet<MessageUpdateFields> updateFields =
                    EnumSet.of(MessageUpdateFields.CONTENT,
                    MessageUpdateFields.VISIBILITY);
                // Update the message.
                queue.updateMessage(message, 30, updateFields, null, null);
                break;
            }
        }
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

В отличие от этого, следующий пример кода просто обновляет первое видимое сообщение в очереди.

    try
    {
    	// Retrieve storage account from connection-string.
    	CloudStorageAccount storageAccount =
	       CloudStorageAccount.parse(storageConnectionString);

    	// Create the queue client.
    	CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    	// Retrieve a reference to a queue.
    	CloudQueue queue = queueClient.getQueueReference("myqueue");

    	// Retrieve the first visible message in the queue.
    	CloudQueueMessage message = queue.retrieveMessage();

    	if (message != null)
    	{
            // Modify the message content.
            message.setMessageContent("Updated contents.");
            // Set it to be visible in 60 seconds.
            EnumSet<MessageUpdateFields> updateFields =
                EnumSet.of(MessageUpdateFields.CONTENT,
                MessageUpdateFields.VISIBILITY);
            // Update the message.
            queue.updateMessage(message, 60, updateFields, null, null);
    	}
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## Практическое руководство. Получение длины очереди

Вы можете узнать приблизительное количество сообщений в очереди. Метод **downloadAttributes** запрашивает у службы очередей несколько текущих значений, включая количество сообщений в очереди. Счетчик указывает число лишь приблизительно, так как сообщения могут добавляться или удаляться после ответа службы очередей на ваш запрос. Метод **getApproximateMessageCount** возвращает последнее значение, полученное при вызове **downloadAttributes** без обращения к службе очередей.

    try
    {
    	// Retrieve storage account from connection-string.
    	CloudStorageAccount storageAccount =
	       CloudStorageAccount.parse(storageConnectionString);

    	// Create the queue client.
    	CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    	// Retrieve a reference to a queue.
    	CloudQueue queue = queueClient.getQueueReference("myqueue");

	   // Download the approximate message count from the server.
    	queue.downloadAttributes();

    	// Retrieve the newly cached approximate message count.
    	long cachedMessageCount = queue.getApproximateMessageCount();

    	// Display the queue length.
    	System.out.println(String.format("Queue length: %d", cachedMessageCount));
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## Практическое руководство. Удаление следующего сообщения из очереди

Код удаляет сообщение из очереди в два этапа. При вызове метода **retrieveMessage** вы получаете следующее сообщение в очереди. Сообщение, возвращаемое методом **retrieveMessage**, становится невидимым для другого кода, считывающего сообщения из этой очереди. По умолчанию это сообщение остается невидимым в течение 30 секунд. Чтобы завершить удаление сообщения из очереди, необходимо также вызвать метод **deleteMessage**. Этот двухэтапный процесс удаления сообщения позволяет удостовериться, что если коду не удастся обработать сообщение из-за сбоя оборудования или программного обеспечения, другой экземпляр кода сможет получить то же сообщение и повторить попытку. Код вызывает метод **deleteMessage** сразу после обработки сообщения.

    try
    {
    	// Retrieve storage account from connection-string.
    	CloudStorageAccount storageAccount =
    	    CloudStorageAccount.parse(storageConnectionString);

    	// Create the queue client.
    	CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    	// Retrieve a reference to a queue.
    	CloudQueue queue = queueClient.getQueueReference("myqueue");

    	// Retrieve the first visible message in the queue.
    	CloudQueueMessage retrievedMessage = queue.retrieveMessage();

    	if (retrievedMessage != null)
    	{
    		// Process the message in less than 30 seconds, and then delete the message.
    		queue.deleteMessage(retrievedMessage);
    	}
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }


## Дополнительные варианты удаления сообщений из очереди

Способ извлечения сообщения из очереди можно настроить двумя способами. Во-первых, можно получить пакет сообщений (до 32 сообщений). Во-вторых, можно задать более длительное или короткое время ожидания видимости, чтобы предоставить коду больше или меньше времени на полную обработку каждого сообщения.

В следующем примере кода метод **retrieveMessages** используется для получения 20 сообщений в одном вызове. Затем он обрабатывает каждое сообщение с помощью цикла **for**. Пример также задает время ожидания невидимости в размере пяти минут (300 секунд) для каждого сообщения. Обратите внимание, что пятиминутный период начинается для всех сообщений одновременно, поэтому по прошествии 5 минут с момента вызова **retreiveMessages** все сообщения, которые не были удалены, снова становятся видимыми.

    try
    {
        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount =
            CloudStorageAccount.parse(storageConnectionString);

        // Create the queue client.
        CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

        // Retrieve a reference to a queue.
        CloudQueue queue = queueClient.getQueueReference("myqueue");

        // Retrieve 20 messages from the queue with a visibility timeout of 300 seconds.
        for (CloudQueueMessage message : queue.retrieveMessages(20, 300, null, null)) {
            // Do processing for all messages in less than 5 minutes,
            // deleting each message after processing.
            queue.deleteMessage(message);
        }
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## Как перечислять очереди

Чтобы получить список текущей очереди, вызовите метод **CloudQueueClient.listQueues()**, который возвращает коллекцию объектов **CloudQueue**.

    try
    {
        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount =
            CloudStorageAccount.parse(storageConnectionString);

        // Create the queue client.
        CloudQueueClient queueClient =
            storageAccount.createCloudQueueClient();

        // Loop through the collection of queues.
        for (CloudQueue queue : queueClient.listQueues())
        {
            // Output each queue name.
            System.out.println(queue.getName());
        }
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## Практическое руководство. Удаление очереди

Для удаления очереди и всех сообщений в ней вызовите метод **deleteIfExists** в объекте **CloudQueue**.

    try
    {
        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount =
            CloudStorageAccount.parse(storageConnectionString);

        // Create the queue client.
        CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

        // Retrieve a reference to a queue.
        CloudQueue queue = queueClient.getQueueReference("myqueue");

        // Delete the queue if it exists.
        queue.deleteIfExists();
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## Дальнейшие действия

Вы изучили основные сведения о хранилище очередей. Дополнительные сведения о более сложных задачах по использованию хранилища можно найти по следующим ссылкам.

- [Пакет SDK для службы хранилища Azure для Java][]
- [Справочник по пакету SDK для клиента хранилища Azure][]
- [REST API службы хранилища Azure][]
- [Блог рабочей группы службы хранилища Azure][]

[Azure SDK for Java]: http://go.microsoft.com/fwlink/?LinkID=525671
[Пакет SDK для службы хранилища Azure для Java]: https://github.com/azure/azure-storage-java
[Пакет SDK хранилища Azure для Java]: https://github.com/azure/azure-storage-java
[Пакет SDK хранилища Azure для Android]: https://github.com/azure/azure-storage-android
[Справочник по пакету SDK для клиента хранилища Azure]: http://dl.windowsazure.com/storage/javadoc/
[справочнике по пакету SDK для клиента хранилища Azure]: http://dl.windowsazure.com/storage/javadoc/
[REST API службы хранилища Azure]: https://msdn.microsoft.com/library/azure/dd179355.aspx
[Блог рабочей группы службы хранилища Azure]: http://blogs.msdn.com/b/windowsazurestorage/

<!---HONumber=AcomDC_0114_2016-->
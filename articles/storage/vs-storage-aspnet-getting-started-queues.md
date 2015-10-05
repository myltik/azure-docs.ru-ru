<properties
	pageTitle="Начало работы с хранилищем очередей и подключенными службами Visual Studio (ASP.NET) | Microsoft Azure"
	description="Как приступить к работе, используя хранилище очередей Azure в проекте ASP.NET в Visual Studio после подключения к учетной записи хранения с помощью подключенных служб Visual Studio"
	services="storage"
	documentationCenter=""
	authors="patshea123"
	manager="douge"
	editor="tglee"/>

<tags
	ms.service="storage"
	ms.workload="web"
	ms.tgt_pltfrm="vs-getting-started"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/03/2015"
	ms.author="patshea"/>

# Начало работы с хранилищем очередей Azure и подключенными службами Visual Studio

> [AZURE.SELECTOR]
> - [Getting Started](vs-storage-aspnet-getting-started-queues.md)
> - [What Happened](vs-storage-aspnet-what-happened.md)

> [AZURE.SELECTOR]
> - [Blobs](vs-storage-aspnet-getting-started-blobs.md)
> - [Queues](vs-storage-aspnet-getting-started-queues.md)
> - [Tables](vs-storage-aspnet-getting-started-tables.md)

## Обзор

В этой статье описывается, как приступить к использованию хранилища очередей Azure в Visual Studio после создания учетной записи хранения Azure или указания ссылки на нее в проекте ASP.NET с помощью диалогового окна **Добавление подключенных служб** в Visual Studio.

Мы покажем, как создать очередь Azure и получить к ней доступ с помощью учетной записи хранения. Кроме того, вы узнаете, как выполнять базовые операции с очередями, например добавлять, изменять, считывать и удалять сообщения в очередях. Примеры написаны на C#, и в них используется [клиентская библиотека службы хранилища Azure для .NET](https://msdn.microsoft.com/library/azure/dn261237.aspx). Дополнительные сведения см. на сайте [ASP.NET](http://www.asp.net).

Хранилище очередей Azure — это служба для хранения большого количества сообщений, к которым можно получить доступ практически из любой точки мира с помощью вызовов с проверкой подлинности по протоколам HTTP или HTTPS. Одно сообщение очереди может быть размером до 64 КБ, а очередь может содержать миллионы сообщений до общего ограничения емкости учетной записи хранения.

## Доступ к очередям в коде

Для доступа к очередям в проектах ASP.NET необходимо включить следующие элементы во все файлы исходного кода C#, которые обращаются к хранилищу очередей Azure.

1. Убедитесь, что объявления пространств имен в верхней части файла C# содержат указанные ниже выражения **using**.

		using Microsoft.Framework.Configuration;
		using Microsoft.WindowsAzure.Storage;
		using Microsoft.WindowsAzure.Storage.Queue;

2. Получите объект **CloudStorageAccount**, представляющий данные учетной записи хранения. Используйте следующий код, чтобы получить строку подключения и сведения об учетной записи хранения из конфигурации службы Azure.

		 CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
		   CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));

3. Получите объект **CloudQueueClient**, чтобы указать ссылку на объекты очереди в вашей учетной записи хранения.

	    // Create the queueclient.
    	CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

4. Получите объект **CloudQueue**, чтобы указать ссылку на определенную очередь.

    	// Get a reference to a queue named "messageQueue"
	    CloudQueue messageQueue = queueClient.GetQueueReference("messageQueue");


**ПРИМЕЧАНИЕ.** Вставьте весь код, представленный выше, перед кодом в указанных ниже примерах.

## Создание очереди в коде

Чтобы создать очередь Azure в коде, просто добавьте вызов **CreateIfNotExists** в приведенный выше код.

	// Create the CloudQuecClient  if it does not exist
	messageQueue.CreateIfNotExists();

## Добавление сообщения в очередь

Чтобы вставить сообщение в существующую очередь, создайте объект **CloudQueueMessage**, а затем вызовите метод **AddMessage**.

Для создания объекта **CloudQueueMessage** можно использовать либо строку (в формате UTF-8), либо массив типа byte.

Ниже приведен пример, который вставляет сообщение "Hello, World".

	// Get a reference to the CloudQueue object named 'messageQueue' as described in "Access a queue in code"

	// Create a message and add it to the queue.
	CloudQueueMessage message = new CloudQueueMessage("Hello, World");
	messageQueue.AddMessage(message);

## Чтение сообщения в очереди

Можно просмотреть сообщение в начале очереди, не удаляя его из очереди, вызвав метод PeekMessage().

    // Get a reference to the CloudQueue object named 'messageQueue' as described in "Access a queue in code"

	// Peek at the next message
    CloudQueueMessage peekedMessage = messageQueue.PeekMessage();

## Чтение и удаление сообщения в очереди

Ваш код может удалить сообщение из очереди в два этапа. 1. Вызовите метод GetMessage(), чтобы получить следующее сообщение в очереди. Сообщение, возвращаемое методом GetMessage(), становится невидимым для другого кода, считывающего сообщения из этой очереди. По умолчанию это сообщение остается невидимым в течение 30 секунд. 2. Чтобы завершить удаление сообщения из очереди, вызовите метод **DeleteMessage**.

Этот двухэтапный процесс удаления сообщения позволяет удостовериться, что если коду не удастся обработать сообщение из-за сбоя оборудования или программного обеспечения, другой экземпляр кода сможет получить то же сообщение и повторить попытку. Указанный ниже код вызывает метод **DeleteMessage** сразу после обработки сообщения.

	// Get a reference to the CloudQueue object named 'messageQueue' as described in "Access a queue in code"

	// Get the next message in the queue.
	CloudQueueMessage retrievedMessage = messageQueue.GetMessage();

	// Process the message in less than 30 seconds

  	// Then delete the message.
	await messageQueue.DeleteMessage(retrievedMessage);


## Использование дополнительных параметров для удаления сообщений из очереди

Способ извлечения сообщения из очереди можно настроить двумя способами. Во-первых, можно получить пакет сообщений (до 32 сообщений). Во-вторых, можно задать более длительное или короткое время ожидания видимости, чтобы предоставить коду больше или меньше времени на полную обработку каждого сообщения. В следующем примере кода метод **GetMessages** используется для получения 20 сообщений в одном вызове. Затем он обрабатывает каждое сообщение с помощью цикла **foreach**. Он также задает время ожидания невидимости 5 минут для каждого сообщения. Обратите внимание на то, что пятиминутный период начинается для всех сообщений одновременно, поэтому по прошествии пяти минут с момента вызова **GetMessages** все сообщения, которые не были удалены, снова становятся видимыми.

    // Get a reference to the CloudQueue object named 'messageQueue' as described in "Access a queue in code"

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

Вы можете узнать приблизительное количество сообщений в очереди. Метод **FetchAttributes** отправляет в службу очередей запрос на извлечение атрибутов очереди, включая количество сообщений. Свойство **ApproximateMethodCount** возвращает последнее значение, полученное с использованием метода **FetchAttributes**, без обращения к службе очередей.

    // Get a reference to the CloudQueue object named 'messageQueue' as described in "Access a queue in code"

	// Fetch the queue attributes.
	messageQueue.FetchAttributes();

    // Retrieve the cached approximate message count.
    int? cachedMessageCount = messageQueue.ApproximateMessageCount;

	// Display number of messages.
	Console.WriteLine("Number of messages in queue: " + cachedMessageCount);

## Использование алгоритма Async-Await со стандартными API очередей

В этом примере показано, как использовать алгоритм Async-Await со стандартными API очередей. Вызывается асинхронная версия каждого из методов (на это указывает постфикс Async после их названий). При использовании асинхронного метода алгоритм Async-Await приостанавливает локальное выполнение процесса до завершения вызова. Благодаря этому текущий поток может выполнять другие задачи, что позволяет избежать возникновения узких мест и повысить общую скорость реагирования приложения. Дополнительные сведения об использовании алгоритма Async-Await в .NET см. в статье [Асинхронное программирование с использованием ключевых слов Async и Await (C# и Visual Basic)](https://msdn.microsoft.com/library/hh191443.aspx).

    // Get a reference to the CloudQueue object named 'messageQueue' as described in "Access a queue in code"

    // Create a message to put in the queue
    CloudQueueMessage cloudQueueMessage = new CloudQueueMessage("My message");

    // Async enqueue the message
    await messageQueue.AddMessageAsync(cloudQueueMessage);
    Console.WriteLine("Message added");

    // Async dequeue the message
    CloudQueueMessage retrievedMessage = await messageQueue.GetMessageAsync();
    Console.WriteLine("Retrieved message with content '{0}'", retrievedMessage.AsString);

    // Async delete the message
    await messageQueue.DeleteMessageAsync(retrievedMessage);
    Console.WriteLine("Deleted message");

## Удаление очереди

Чтобы удалить очередь и все сообщения в ней, вызовите метод **Delete** для объекта очереди.

    // Get a reference to the CloudQueue object named 'messageQueue' as described in "Access a queue in code"

    // Delete the queue.
    messageQueue.Delete();



## Дальнейшие действия

[AZURE.INCLUDE [vs-storage-dotnet-queues-next-steps](../../includes/vs-storage-dotnet-queues-next-steps.md)]

<!---HONumber=Sept15_HO4-->
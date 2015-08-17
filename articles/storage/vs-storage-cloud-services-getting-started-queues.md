<properties 
	pageTitle="Приступая к работе с хранилищем очередей Azure и подключенными службами Visual Studio" 
	description="Начало работы с использованием хранилища очередей Azure в проекте облачной службы в Visual Studio" 
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
	ms.date="08/04/2015" 
	ms.author="patshea123"/>

# Приступая к работе с хранилищем очередей Azure и подключенными службами Visual Studio

> [AZURE.SELECTOR]
> - [Getting started](vs-storage-cloud-services-getting-started-queues.md)
> - [What happened](vs-storage-cloud-services-what-happened.md)

> [AZURE.SELECTOR]
> - [Blobs](vs-storage-cloud-services-getting-started-blobs.md)
> - [Queues](vs-storage-cloud-services-getting-started-queues.md)
> - [Tables](vs-storage-cloud-services-getting-started-tables.md)

В этой статье описывается, как приступить к использованию табличного хранилища Azure в Visual Studio после создания учетной записи хранения Azure или указания ссылки на нее в проекте облачных служб с помощью диалогового окна **Добавление подключенных служб** в Visual Studio.

Мы покажем, как создать очередь в коде. Кроме того, вы узнаете, как выполнять базовые операции с очередями, например добавлять, изменять, считывать и удалять сообщения в очередях. Примеры написаны на C#, и в них используется [клиентская библиотека службы хранилища Azure для .NET](https://msdn.microsoft.com/library/azure/dn261237.aspx).

 - Дополнительные сведения о программных операциях с большими двоичными объектами см. в статье [Использование хранилища BLOB-объектов из .NET](storage-dotnet-how-to-use-blobs.md "Использование хранилища BLOB-объектов из .NET").
 - Общие сведения о службе хранилища Azure см. в [документации по службе хранилища](https://azure.microsoft.com/documentation/services/storage/).
 - Общие сведения об облачных службах Azure см. в [документации по облачным службам](http://azure.microsoft.com/documentation/services/cloud-services/).
 - Дополнительную информацию о программировании для ASP.NET см. в разделе [ASP.NET](http://www.asp.net).


Хранилище очередей Azure — это служба хранения большого количества сообщений, к которым можно получить доступ практически из любой точки мира с помощью вызовов с проверкой подлинности по протоколу HTTP или HTTPS. Одно сообщение очереди может быть размером до 64 КБ, а очередь может содержать миллионы сообщений до общего ограничения емкости учетной записи хранения.

Операция **Добавить подключенные службы** устанавливает соответствующие пакеты NuGet для доступа к хранилищу Azure в вашем проекте и добавляет строку подключения для учетной записи хранения в файлы конфигурации проекта.

Чтобы начать работу, сначала необходимо создать очередь Azure в учетной записи хранения.

##Доступ к очередям в коде

Для доступа к очередям в проектах ASP.NET необходимо включить следующие элементы во все файлы исходного кода C#, которые обращаются к хранилищу очередей Azure.

1. Убедитесь, что объявления пространств имен в верхней части файла C# содержат следующие операторы `using`.

		using Microsoft.Framework.Configuration;
		using Microsoft.WindowsAzure.Storage;
		using Microsoft.WindowsAzure.Storage.Queue;

2. Получите объект `CloudStorageAccount`, представляющий данные учетной записи хранения. Используйте следующий код, чтобы получить строку подключения и сведения об учетной записи хранения из конфигурации службы Azure.

		 CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
		   CloudConfigurationManager.GetSetting("<storage account name>_AzureStorageConnectionString"));

3. Получите объект `CloudQueueClient`, чтобы указать ссылку на объекты очереди в вашей учетной записи хранения.

	    // Create the table client.
    	CloudQuecClient queueClient = storageAccount.CreateCloudTableClient();

4. Получите объект `CloudQueue` для ссылки на определенную очередь.

    	// Get a reference to a table named "messageQueue"
	    CloudTable messageQueue = queueClient.GetQueueReference("messageQueue");


**ПРИМЕЧАНИЕ.** Вставьте весь код, представленный выше, перед кодом в следующих примерах.

###Создание очереди в коде

Чтобы создать очередь Azure в коде, просто добавьте вызов `CreateIfNotExists`.

	// Get a reference to a CloudQueue object with the variable name 'messageQueue' as described in 
    // the "Access blob containers in code" section.

	// Create the CloudTable if it does not exist
	messageQueue.CreateIfNotExists();

##Добавление сообщения в очередь

Чтобы вставить сообщение в существующую очередь, создайте объект `CloudQueueMessage`, а затем вызовите метод AddMessage().

Для создания объекта `CloudQueueMessage` можно использовать строку (в формате UTF-8) или массив байтов.

Ниже приведен пример, который вставляет сообщение "Hello, World".

    // Get a reference to a CloudQueue object with the variable name 'messageQueue' as described in 
    // the "Access blob containers in code" section.

	// Create a message and add it to the queue.
	CloudQueueMessage message = new CloudQueueMessage("Hello, World");
	messageQueue.AddMessage(message);

##Чтение сообщения в очереди

Можно просмотреть сообщение в начале очереди, не удаляя его из очереди, вызвав метод PeekMessage().

    // Get a reference to a CloudQueue object with the variable name 'messageQueue' as described in 
    // the "Access blob containers in code" section.

	// Peek at the next message
    CloudQueueMessage peekedMessage = messageQueue.PeekMessage();

##Чтение и удаление сообщения в очереди

Ваш код может удалить сообщение из очереди в два этапа. 1. Вызовите метод GetMessage(), чтобы получить следующее сообщение в очереди. Сообщение, возвращаемое методом GetMessage(), становится невидимым для другого кода, считывающего сообщения из этой очереди. По умолчанию это сообщение остается невидимым в течение 30 секунд. 2. Чтобы завершить удаление сообщения из очереди, вызовите метод DeleteMessage1().

Этот двухэтапный процесс удаления сообщения позволяет удостовериться, что если коду не удастся обработать сообщение из-за сбоя оборудования или программного обеспечения, другой экземпляр кода сможет получить то же сообщение и повторить попытку. Код вызывает метод DeleteMessage() сразу после обработки сообщения.

    // Get a reference to a CloudQueue object with the variable name 'messageQueue' as described in 
    // the "Access blob containers in code" section.

	// Get the next message in the queue.
	CloudQueueMessage retrievedMessage = messageQueue.GetMessage();

	// Process the message in less than 30 seconds

  	// Then delete the message.
	await messageQueue.DeleteMessage(retrievedMessage);


## Использование дополнительных вариантов обработки и удаления сообщений в очереди

Способ извлечения сообщения из очереди можно настроить двумя способами. Во-первых, можно получить пакет сообщений (до 32 сообщений). Во-вторых, можно задать более длительное или короткое время ожидания видимости, чтобы предоставить коду больше или меньше времени на полную обработку каждого сообщения. В следующем примере кода метод `GetMessages` используется, чтобы получить 20 сообщений в одном вызове. Затем он обрабатывает каждое сообщение с помощью цикла `foreach`. Он также задает время ожидания невидимости 5 минут для каждого сообщения. Обратите внимание на то, что пятиминутный период начинается для всех сообщений одновременно, поэтому по прошествии пяти минут с момента вызова `GetMessages` все сообщения, которые не были удалены, снова становятся видимыми.

    // Get a reference to a CloudQueue object with the variable name 'messageQueue' as described in 
    // the "Access blob containers in code" section.

    foreach (CloudQueueMessage message in messageQueue.GetMessages(20, TimeSpan.FromMinutes(5)))
    {
        // Process all messages in less than 5 minutes, deleting each message after processing.

        messageQueue.DeleteMessage(message);
    }

## Получение длины очереди

Вы можете узнать приблизительное количество сообщений в очереди. Метод `FetchAttributes` отправляет в службу очередей запрос на извлечение атрибутов очереди, включая количество сообщений. Свойство `ApproximateMethodCount` возвращает последнее значение, полученное с использованием метода `FetchAttributes`, без обращения к службе очередей.

    // Get a reference to a CloudQueue object with the variable name 'messageQueue' as described in 
    // the "Access blob containers in code" section.

	// Fetch the queue attributes.
	messageQueue.FetchAttributes();

    // Retrieve the cached approximate message count.
    int? cachedMessageCount = messageQueue.ApproximateMessageCount;

	// Display number of messages.
	Console.WriteLine("Number of messages in queue: " + cachedMessageCount);

## Использование схемы Async-Await со стандартными API очереди

В этом примере показано использование алгоритма Async-Await со стандартными API очередей. Вызывается асинхронная версия каждого из методов (на это указывает постфикс Async после их названий). При использовании асинхронного метода алгоритм Async-Await приостанавливает локальное выполнение процесса до завершения вызова. Благодаря этому текущий поток может выполнять другие задачи, что позволяет избежать возникновения узких мест и повысить общую скорость реагирования приложения. Дополнительные сведения об использовании алгоритма Async-Await в .NET см. в статье [Асинхронное программирование с использованием ключевых слов Async и Await (C# и Visual Basic)](https://msdn.microsoft.com/library/hh191443.aspx).

    // Get a reference to a CloudQueue object with the variable name 'messageQueue' as described in 
    // the "Access blob containers in code" section.

    // Create a message to put in the queue
    CloudQueueMessage cloudQueueMessage = new CloudQueueMessage("My message");

    // Add the message asynchronously
    await messageQueue.AddMessageAsync(cloudQueueMessage);
    Console.WriteLine("Message added");

    // Async dequeue the message
    CloudQueueMessage retrievedMessage = await messageQueue.GetMessageAsync();
    Console.WriteLine("Retrieved message with content '{0}'", retrievedMessage.AsString);

    // Delete the message asynchronously
    await messageQueue.DeleteMessageAsync(retrievedMessage);
    Console.WriteLine("Deleted message");
## Удаление очереди

Для удаления очереди и всех сообщений в ней вызовите метод `Delete` в объекте очереди.

    // Get a reference to a CloudQueue object with the variable name 'messageQueue' as described in 
    // the "Access blob containers in code" section.

    // Delete the queue.
    messageQueue.Delete();



##Дальнейшие действия

[AZURE.INCLUDE [vs-storage-dotnet-queues-next-steps](../../includes/vs-storage-dotnet-queues-next-steps.md)]
			

<!---HONumber=August15_HO6-->
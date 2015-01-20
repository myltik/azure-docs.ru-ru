<properties linkid="" pageTitle="Использование пакета SDK веб-заданий для работы с хранилищем очередей Azure" metaKeywords="пакет SDK веб-заданий хранилище очередей Azure C# .NET" description="Узнайте, как работать с хранилищем очередей Azure с помощью пакета SDK для WebJobs. Создание и удаление очередей; вставка, обзор, получение и удаление сообщений очереди, а также многое другое." metaCanonical="" disqusComments="1" umbracoNaviHide="1" services="web-sites,storage" documentationCenter=".NET" title="How to work with Azure queue storage using the WebJobs SDK" authors="tdykstra" manager="wpickett" editor="jimbe" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="10/13/2014" ms.author="tdykstra" />

# Использование пакета SDK веб-заданий для работы с хранилищем очередей Azure

В этом руководстве показано, как писать код на C# для распространенных сценариев использования
службы хранилища очередей Azure и пакета SDK веб-заданий Azure версии 1.0.0.

Предполагается, что вы уже знаете, [что такое пакет SDK веб-заданий](../websites-webjobs-sdk-storage-queues-how-to) и [как выполнять типовые задачи](../websites-dotnet-webjobs-sdk-get-started/), такие как установка пакета NuGet SDK веб-заданий, создание учетной записи хранения Azure и создание строк подключения для пакета SDK веб-заданий, указывающих на вашу учетную запись хранения Azure.

В большинстве фрагментов кода показаны только функции, а не код, создающий объект `JobHost`, как в следующем примере:

		static void Main(string[] args)
		{
		    JobHost host = new JobHost();
		    host.RunAndBlock();
		}
		
## Оглавление

-   [Вызов функции при получении сообщения очереди](#trigger)
	- Строковые сообщения очереди
	- Сообщения очереди объекта POCO
	- Асинхронные функции
	- Алгоритм опроса
	- Параллельное выполнение
	- Получение очереди или метаданных очереди сообщений 
	- Корректное завершение работы
-   [Создание сообщения очереди во время обработки сообщения очереди](#createqueue)
	- Строковые сообщения очереди
	- Сообщения очереди объекта POCO
	- Создание нескольких сообщений
	- Использование атрибутов очереди в теле функции
-   [Чтение и запись BLOB-объектов во время обработки сообщения очереди](#blobs)
	- Строковые сообщения очереди
	- Сообщения очереди объекта POCO
	- Использование атрибутов BLOB-объекта в теле функции
-   [Способ обработки подозрительных сообщений](#poison)
	- Автоматическая обработка сообщений
	- Ручная обработка подозрительных сообщений
-   [Установка параметров конфигурации](#config)
	- Установка строк подключения пакета SDK в коде
	- Настройка параметров атрибута QueueTrigger
	- Получение имен очередей из конфигурации
-   [Ручной вызов функции](#manual)
-   [Запись журналов](#logs)
-   [Дальнейшие действия](#nextsteps)

## <a id="trigger"></a> Вызов функции при получении сообщения очереди

Чтобы написать функцию, которую пакет SDK будет вызывать при получении сообщения очереди, используйте атрибут `QueueTrigger` со строкой или параметр POCO, в зависимости от ожидаемого содержимого сообщения. Конструктор атрибута принимает строковый параметр, в котором указано имя очереди для опроса. Также можно [задать имя очереди динамически](#config).

Если ваш веб-сайт работает на нескольких виртуальных машинах, веб-задания запустятся на каждой машине, и каждая машина будет ожидать триггеров и попытается запустить функции. В некоторых сценариях это может привести к тому, что некоторые функции обработают часть данных дважды, поэтому функции должны быть идемпотентными (написанными так, что постоянный их вызов с одинаковыми входными данными не создаст дублирующие результаты).  

### Строковые сообщения очереди

В следующем примере очередь содержит строковое сообщение, поэтому `QueueTrigger` применяется к строковому параметру с именем `logMessage`, в котором находится содержимое сообщения очереди. Функция [записывает сообщение журнала в панель мониторинга](#logs).
 

		public static void ProcessQueueMessage([QueueTrigger("logqueue")] string logMessage, TextWriter logger)
		{
		    logger.WriteLine(logMessage);
		}

Помимо значения `string` возможными значениями этого параметра являются массив байтов, объект `CloudQueueMessage` или заданный вами объект POCO.

### Сообщения очереди объекта POCO

В следующем примере сообщения очереди содержат JSON для объекта `BlobInformation`, который имеет свойство `BlobName`. Пакет SDK автоматически выполняет десериализацию объекта.

		public static void WriteLogPOCO([QueueTrigger("logqueue")] BlobInformation blobInfo, TextWriter logger)
		{
		    logger.WriteLine("Queue message refers to blob: " + blobInfo.BlobName);
		}

Следующий пример показывает, как создать сообщение очереди POCO без использования пакета SDK веб-заданий, которое может быть проанализировано SDK. Для сериализации и десериализации сообщений в пакете SDK используется [пакет NuGet Newtonsoft.Json](http://www.nuget.org/packages/Newtonsoft.Json).

		BlobInformation blobInfo = new BlobInformation() { BlobName = "log.txt" };
		var queueMessage = new CloudQueueMessage(JsonConvert.SerializeObject(blobInfo));
		logQueue.AddMessage(queueMessage);

### Асинхронные функции

Следующая асинхронная функция [записывает журнал для панели мониторинга](#logs).

		public async static Task ProcessQueueMessageAsync([QueueTrigger("logqueue")] string logMessage, TextWriter logger)
		{
		    await logger.WriteLineAsync(logMessage);
		}

Асинхронные функции могут принимать токены отмены, как показано в следующем примере, который копирует BLOB-объект. (Объяснение заполнителя `queueTrigger` см. в разделе [BLOB-объекты](#blobs) .)

		public async static Task ProcessQueueMessageAsyncCancellationToken(
		    [QueueTrigger("blobcopyqueue")] string blobName, 
		    [Blob("textblobs/{queueTrigger}",FileAccess.Read)] Stream blobInput,
		    [Blob("textblobs/{queueTrigger}-new",FileAccess.Write)] Stream blobOutput,
		    CancellationToken token)
		{
		    await blobInput.CopyToAsync(blobOutput, 4096, token);
		}

### Алгоритм опроса

В пакете SDK реализован алгоритм случайной экспоненциальной отсрочки, который позволяет уменьшить влияние опроса очереди ожидающих задач на затраты на транзакции хранилища.  При обнаружении сообщения пакет SDK ожидает в течение двух секунд и затем проверяет, не поступило ли еще одно сообщение; если сообщение не найдено, он ожидает около четырех секунд перед повторной попыткой. После последующих неудачных попыток получения сообщения очереди время ожидания продолжает увеличиваться, пока не достигнет максимального времени ожидания, по умолчанию - одна минута. [Можно настроить максимальное время ожидания](#config).

### Параллельное выполнение

Если имеется несколько функций, которые прослушивают различные очереди, при одновременном получении сообщений пакет SDK будет вызывать их параллельно. 

То же самое происходит при получении нескольких сообщений для одной очереди. По умолчанию SDK одновременно получает пакет из 16 сообщений очереди и выполняет функцию, которая обрабатывает их параллельно. [Можно настроить размер пакета](#config). Когда число обрабатываемых сообщений достигает половины размера пакета, SDK запрашивает следующий пакет и начинает обработку содержащихся в нем сообщений. Поэтому максимальное количество сообщений, одновременно обрабатываемых каждой функцией, в полтора раза больше размера пакета. Это ограничение применяется отдельно к каждой функции с атрибутом `QueueTrigger`. Если вы не хотите, чтобы сообщения из одной очереди обрабатывались параллельно, установите размер пакета равный 1.

### <a id="queuemetadata"></a>Получение очереди или метаданных очереди сообщений

Следующие свойства сообщений можно получить путем добавления параметров к сигнатуре метода:

* `DateTimeOffset` expirationTime
* `DateTimeOffset` insertionTime
* `DateTimeOffset` nextVisibleTime
* `string` queueTrigger (содержит текст сообщения)
* `string` id
* `string` popReceipt
* `int` dequeueCount

Если требуется работать непосредственно с API хранилища Azure, можно также добавить параметр `CloudStorageAccount`.

Следующий пример записывает все эти метаданные в журнал INFO приложения. В примере содержимое сообщения очереди находится и в logMessage, и в queueTrigger.

		public static void WriteLog([QueueTrigger("logqueue")] string logMessage,
		    DateTimeOffset expirationTime,
		    DateTimeOffset insertionTime,
		    DateTimeOffset nextVisibleTime,
		    string id,
		    string popReceipt,
		    int dequeueCount,
		    string queueTrigger,
		    CloudStorageAccount cloudStorageAccount,
		    TextWriter logger)
		{
		    logger.WriteLine(
		        "logMessage={0}\n" +
			"expirationTime={1}\ninsertionTime={2}\n" +
		        "nextVisibleTime={3}\n" +
		        "id={4}\npopReceipt={5}\ndequeueCount={6}\n" +
		        "queue endpoint={7} queueTrigger={8}",
		        logMessage, expirationTime,
		        insertionTime,
		        nextVisibleTime, id,
		        popReceipt, dequeueCount,
		        cloudStorageAccount.QueueEndpoint,
		        queueTrigger);
		}

Ниже приведен образец журнала, созданный с помощью кода из примера.

		logMessage=Hello world!
		expirationTime=10/14/2014 10:31:04 PM +00:00
		insertionTime=10/7/2014 10:31:04 PM +00:00
		nextVisibleTime=10/7/2014 10:41:23 PM +00:00
		id=262e49cd-26d3-4303-ae88-33baf8796d91
		popReceipt=AgAAAAMAAAAAAAAAfc9H0n/izwE=
		dequeueCount=1
		queue endpoint=https://contosoads.queue.core.windows.net/
		queueTrigger=Hello world!

### <a id="graceful"></a>Корректное завершение работы

Функция, которая запускается в постоянном веб-задании, может принимать параметр `CancellationToken`, который позволяет операционной системе уведомлять ее о том, что выполнение веб-задания будет завершено. Это уведомление можно использовать для предотвращения ситуации, когда выполнение функции завершается неожиданно, оставляя данные в несогласованном состоянии.

Следующий пример показывает, как проверить функцию на наличие предстоящего завершения веб-задания.

	public static void GracefulShutdownDemo(
	            [QueueTrigger("inputqueue")] string inputText,
	            TextWriter logger,
	            CancellationToken token)
	{
	    for (int i = 0; i < 100; i++)
	    {
	        if (token.IsCancellationRequested)
	        {
	            logger.WriteLine("Function was cancelled at iteration {0}", i);
	            break;
	        }
	        Thread.Sleep(1000);
	        logger.WriteLine("Normal processing for queue message={0}", inputText);
	    }
	}

**Примечание.** Панель мониторинга может неправильно показывать состояние и выходные данные завершенных функций.
 
Дополнительные сведения см. в статье [Корректное завершение работы веб-заданий](http://blog.amitapple.com/post/2014/05/webjobs-graceful-shutdown/#.VCt1GXl0wpR).   

## <a id="createqueue"></a> Создание сообщения очереди во время обработки сообщения очереди

Чтобы написать функцию, которая создает новое сообщение очереди, используйте атрибут `Queue` в выводимой строке или параметре POCO. Как и в случае с `QueueTrigger`, имя очереди можно передать в виде строки или [задать динамически](#config).

### Строковые сообщения очереди

Следующий пример создает новое сообщение очереди в очереди с именем "outputqueue" с тем же содержимым, что и сообщение очереди, поступившее в очередь с именем "inputqueue".

		public static void CreateQueueMessage(
		    [QueueTrigger("inputqueue")] string queueMessage,
		    [Queue("outputqueue")] out string outputQueueMessage )
		{
		    outputQueueMessage = queueMessage;
		}

Если объект не равен null, когда заканчивается выполнение функции, и выходной параметр принадлежит к одному из следующих типов, пакет SDK создает сообщение очереди:

* `string` 
* `byte[]`
* Сериализуемый тип POCO, заданный вами
* `CloudQueueMessage`

Для создания нескольких сообщений см. **Создание нескольких сообщений** далее в этом разделе.

Если сообщения требуется отправлять вручную, в качестве типа выходного параметра можно также использовать `CloudQueue`.

### Сообщения очереди объекта POCO

Чтобы создать сообщение очереди, содержащее объект POCO, а не строку, передайте тип POCO в качестве выходного параметра конструктору атрибута `Queue`. 

		public static void CreateQueueMessage(
		    [QueueTrigger("inputqueue")] BlobInformation blobInfoInput,
		    [Queue("outputqueue")] out BlobInformation blobInfoOutput )
		{
		    blobInfoOutput = blobInfoInput;
		}

Пакет SDK автоматически выполняет сериализацию объекта в формат JSON. Сообщение очереди создается всегда, даже если объект имеет значение null.

### Создание нескольких сообщений

Чтобы создать несколько сообщений, установите для очереди вывода тип параметра `ICollector<T>` или `IAsyncCollector`, как показано в следующем примере.

		public static void CreateQueueMessages(
		    [QueueTrigger("inputqueue")] string queueMessage,
		    [Queue("outputqueue")] ICollector<string> outputQueueMessage,
		    TextWriter logger)
		{
		    logger.WriteLine("Creating 2 messages in outputqueue");
		    outputQueueMessage.Add(queueMessage + "1");
		    outputQueueMessage.Add(queueMessage + "2");
		}

Каждое сообщение очереди создается сразу после вызова метода `Add`.

### <a id="queuebody"></a>Использование атрибутов очереди в теле функции

Если необходимо выполнить некоторую работу в функции перед использованием атрибута `Queue` для создания сообщения очереди, можно использовать интерфейс `IBinder` для получения объекта `CloudQueue`, который позволяет работать непосредственно с очередью. 

Следующий пример принимает сообщения очереди ввода и создает новое сообщение очереди вывода с тем же содержимым. Имя очереди вывода определяется кодом в теле функции.

		public static void CreateQueueMessage(
		    [QueueTrigger("inputqueue")] string queueMessage,
		    IBinder binder)
		{
		    string outputQueueName = "outputqueue" + DateTime.Now.Month.ToString();
		    QueueAttribute queueAttribute = new QueueAttribute(outputQueueName);
		    CloudQueue outputQueue = binder.Bind<CloudQueue>(queueAttribute);
		    outputQueue.AddMessage(new CloudQueueMessage(queueMessage));
		}

## <a id="blobs"></a> Чтение и запись BLOB-объектов и таблиц во время обработки сообщения очереди

Атрибуты `Blob` и `Table` позволяют осуществлять чтение и запись BLOB-объектов и таблиц. Примеры в этом разделе применимы к BLOB-объектам.

К типам, с которыми может использоваться атрибут `Blob`, принадлежат `Stream`, `TextReader`, `TextWriter` и `CloudBlockBlob`. Конструктор атрибута берет параметр `blobPath`, указывающий контейнер и имя BLOB-объекта; и если атрибут принадлежит объекту `Stream`, другой параметр конструктора устанавливает режим доступа `FileAccess` как чтение, запись или чтение и запись. Если необходимо выполнить некоторую работу в функции перед привязкой BLOB-объекта к объекту, можно использовать атрибут в теле функции, [как показано выше для атрибута Queue](#queuebody).

### Строковые сообщения очереди

Для сообщения очереди, содержащего строку, `queueTrigger` является заполнителем, который можно использовать в параметре `blobPath` атрибута `Blob`, в котором находится содержимое сообщения. 

Следующий пример использует объекты `Stream` для чтения и записи BLOB-объектов. Сообщение очереди предоставляет имя BLOB-объекта, размещенного в контейнере textblobs. Копия BLOB-объекта с приставкой "-new", добавленной к имени, создается в том же контейнере. 

		public static void ProcessQueueMessage(
		    [QueueTrigger("blobcopyqueue")] string blobName, 
		    [Blob("textblobs/{queueTrigger}",FileAccess.Read)] Stream blobInput,
		    [Blob("textblobs/{queueTrigger}-new",FileAccess.Write)] Stream blobOutput)
		{
		    blobInput.CopyTo(blobOutput, 4096);
		}

Следующий пример использует объект `CloudBlockBlob` для удаления BLOB-объекта.

		public static void DeleteBlob(
		    [QueueTrigger("deleteblobqueue")] string blobName,
		    [Blob("textblobs/{queueTrigger}")] CloudBlockBlob blobToDelete)
		{
		    blobToDelete.Delete();
		}

### Сообщения очереди объекта POCO

Для объектов POCO, сохраненных в формате JSON в сообщении очереди, можно использовать заполнители, которые называют свойства объектов в параметре `blobPath` атрибута `Queue`. В качестве заполнителей можно также использовать [имена свойств метаданных очереди](#queuemetadata) . 

Следующий пример копирует BLOB-объект в новый BLOB-объект с другим расширением, используя свойства объекта `BlobInformation` для указания имен входного и выходного BLOB-объекта. 
 
		public static void CopyBlobPOCO(
		    [QueueTrigger("copyblobqueue")] BlobInformation blobInfo,
		    [Blob("textblobs/{BlobName}", FileAccess.Read)] Stream blobInput,
		    [Blob("textblobs/{BlobNameWithoutExtension}.txt", FileAccess.Write)] Stream blobOutput)
		{
		    blobInput.CopyTo(blobOutput, 4096);
		}

Следующий пример показывает, как создать сообщение очереди POCO без использования пакета SDK веб-заданий, которое может быть проанализировано SDK. Для сериализации и десериализации сообщений в пакете SDK используется [пакет NuGet Newtonsoft.Json](http://www.nuget.org/packages/Newtonsoft.Json).

		BlobInformation blobInfo = new BlobInformation() { BlobName = "boot.log", BlobNameWithoutExtension = "boot" };
		var queueMessage = new CloudQueueMessage(JsonConvert.SerializeObject(blobInfo));
		logQueue.AddMessage(queueMessage);

## <a id="poison"></a> Способ обработки подозрительных сообщений

Сообщения, содержимое которых вызывает сбой функции называются *подозрительными сообщениями*. При сбое функции сообщение очереди не удаляется и в конечном итоге забирается снова, вызывая повтор цикла. Пакет SDK может автоматически прервать цикл после ограниченного числа итераций или это можно сделать вручную.

### Автоматическая обработка сообщений

Пакет SDK вызывает функцию обработки сообщения очереди до 5 раз. В случае сбоя во время пятой попытки сообщение перемещается в очередь подозрительных сообщений. [Можно настроить максимальное число повторных попыток](#config). 

Очередь подозрительных сообщений называется *{originalqueuename}*-poison. Можно написать функции для обработки сообщений из очереди подозрительных сообщений путем внесения их в журнал или отправки уведомления о необходимости ручного вмешательства. 

В следующем примере функция `CopyBlob` завершится ошибкой, если сообщение очереди содержит имя несуществующего BLOB-объекта. В таком случае сообщение перемещается из очереди copyblobqueue в очередь copyblobqueue-poison. Затем функция `ProcessPoisonMessage` записывает подозрительное сообщение в журнал.

		public static void CopyBlob(
		    [QueueTrigger("copyblobqueue")] string blobName,
		    [Blob("textblobs/{queueTrigger}", FileAccess.Read)] Stream blobInput,
		    [Blob("textblobs/{queueTrigger}-new", FileAccess.Write)] Stream blobOutput)
		{
		    blobInput.CopyTo(blobOutput, 4096);
		}
		
		public static void ProcessPoisonMessage(
		    [QueueTrigger("copyblobqueue-poison")] string blobName, TextWriter logger)
		{
		    logger.WriteLine("Failed to copy blob, name=" + blobName);
		}

На следующем рисунке показан вывод консоли при обработке этими функциями подозрительного сообщения.

![Console output for poison message handling](./media/websites-dotnet-webjobs-sdk-storage-queues-how-to/poison.png)

### Ручная обработка подозрительных сообщений

Чтобы узнать, сколько попыток обработки сообщения было совершено, добавьте к функции параметр `int` с именем `dequeueCount`. Затем можно проверить счетчик вывода из очереди в коде функции и выполнить собственную обработку подозрительных сообщений, если это число превышает пороговое значение, как показано в следующем примере.

		public static void CopyBlob(
		    [QueueTrigger("copyblobqueue")] string blobName, int dequeueCount,
		    [Blob("textblobs/{queueTrigger}", FileAccess.Read)] Stream blobInput,
		    [Blob("textblobs/{queueTrigger}-new", FileAccess.Write)] Stream blobOutput,
		    TextWriter logger)
		{
		    if (dequeueCount > 5)
		    {
		        logger.WriteLine("Failed to copy blob, name=" + blobName);
		    }
		    else
		    {
		    blobInput.CopyTo(blobOutput, 4096);
		    }
		}

Чтобы этот код работал должным образом, необходимо увеличить максимальное число повторных попыток для автоматической обработки подозрительных сообщений. В противном случае значение счетчика вывода из очереди в этом примере никогда не превысит 5.

## <a id="config"></a> Установка параметров конфигурации

Чтобы задать следующие параметры конфигурации, можно использовать тип `JobHostConfiguration`:

* Установка строк подключения пакета SDK в коде.
* Настройка таких параметров атрибута QueueTrigger, как максимальное значение счетчика вывода из очереди.
* Получение имен очередей из конфигурации.

### <a id="setconnstr"></a>Установка строк подключения пакета SDK в коде

Установка строк подключения пакета SDK в коде позволяет использовать собственные имена строк подключения в файлах конфигурации или переменных среды, как показано в следующем примере.

		static void Main(string[] args)
		{
		    var _storageConn = ConfigurationManager
		        .ConnectionStrings["MyStorageConnection"].ConnectionString;
		
		    var _dashboardConn = ConfigurationManager
		        .ConnectionStrings["MyDashboardConnection"].ConnectionString;
		
		    var _serviceBusConn = ConfigurationManager
		        .ConnectionStrings["MyServiceBusConnection"].ConnectionString;
		
		    JobHostConfiguration config = new JobHostConfiguration();
		    config.StorageConnectionString = _storageConn;
		    config.DashboardConnectionString = _dashboardConn;
		    config.ServiceBusConnectionString = _serviceBusConn;
		    JobHost host = new JobHost(config);
		    host.RunAndBlock();
		}

### <a id="configqueue"></a>Настройка параметров очереди

Можно настроить следующие параметры, которые применяются для обработки сообщений очереди:

- Максимальное количество сообщений очереди, которые забираются одновременно для параллельной обработки (по умолчанию - 16).
- Максимальное число повторных попыток перед отправкой сообщения очереди в очередь подозрительных сообщений (по умолчанию - 5).
- Максимальное время ожидания перед повторным опросом, когда очередь пуста (по умолчанию - 1 минута).

В примере показано, как настроить эти параметры:

		static void Main(string[] args)
		{
		    JobHostConfiguration config = new JobHostConfiguration();
		    config.Queues.BatchSize = 8;
		    config.Queues.MaxDequeueCount = 4;
		    config.Queues.MaxPollingInterval = TimeSpan.FromMinutes(10);
		    JobHost host = new JobHost(config);
		    host.RunAndBlock();
		}

### <a id="configqueuenames"></a>Получение имен очередей из конфигурации

Тип `JobHostConfiguration` позволяет передавать объект `NameResolver`, который предоставляет имя очереди атрибутам `QueueTrigger` и `Queue` пакета SDK.

Например, предположим, что вы хотите использовать очередь с именем logqueuetest в тестовой среде и очередь с именем logqueueprod в производственной среде. Вместо фиксированного имени очереди требуется указать имя элемента в коллекции `appSettings` с фактическим именем очереди. Если logqueue - значение ключа `appSettings`, функция может выглядеть как в следующем примере.

		public static void WriteLog([QueueTrigger("%logqueue%")] string logMessage)
		{
		    Console.WriteLine(logMessage);
		}

Затем класс `NameResolver` может получить имя очереди из `appSettings`, как показано в следующем примере:

		public class QueueNameResolver : INameResolver
		{
		    public string Resolve(string name)
		    {
		        return ConfigurationManager.AppSettings[name].ToString();
		    }
		}

Необходимо передать класс `NameResolver` в объект `JobHost`, как показано в следующем примере.

		static void Main(string[] args)
		{
		    JobHostConfiguration config = new JobHostConfiguration();
		    config.NameResolver = new QueueNameResolver();
		    JobHost host = new JobHost(config);
		    host.RunAndBlock();
		}
 

## <a id="manual"></a>Ручной вызов функции

Чтобы вызвать функцию вручную, используйте метод `Call` или `CallAsync` объекта `JobHost` и атрибут функции `NoAutomaticTrigger`, как показано в следующем примере. 

		public class Program
		{
		    static void Main(string[] args)
		    {
		        JobHost host = new JobHost();
		        host.Call(typeof(Program).GetMethod("CreateQueueMessage"), new { value = "Hello world!" });
		    }
		
		    [NoAutomaticTrigger]
		    public static void CreateQueueMessage(
		        TextWriter logger, 
		        string value, 
		        [Queue("outputqueue")] out string message)
		    {
		        message = value;
		        logger.WriteLine("Creating queue message: ", message);
		    }
		}

## <a id="logs"></a>Запись журналов

Для записи журналов, которые отображаются в панели мониторинга веб-заданий, связанных с вызовом определенной функции, используйте объект `TextWriter`, получаемый из параметра в сигнатуре вашего метода.

Для записи [журналов трассировки приложения](../web-sites-dotnet-troubleshoot-visual-studio/#logsoverview)используйте свойства `Console.Out` (создает журналы, помеченные как INFO) и Console.Error (создает журналы, помеченные как ERROR). Альтернативой является использование класса [Trace или TraceSource](http://blogs.msdn.com/b/mcsuksoldev/archive/2014/09/04/adding-trace-to-azure-web-sites-and-web-jobs.aspx).

Журналы приложений отображаются в файлах журналов веб-сайта, таблицах Azure или BLOB-объектах Azure, в зависимости от настроек вашего веб-сайта Azure. Если программа выполняется в веб-заданиях Azure, последние 100 журналов приложений также отображаются в панели мониторинга. (В панели мониторинга не отображаются журналы приложений из программ, которые работают локально или в других средах.)   

Можно отключить ведение журнала, [присвоив строке подключения к панели мониторинга значение null](#config).

В следующем примере показано несколько способов записи журналов:

		public static void WriteLog(
		    [QueueTrigger("logqueue")] string logMessage,
		    TextWriter logger)
		{
		    Console.WriteLine("Console.Write - " + logMessage);
		    Console.Out.WriteLine("Console.Out - " + logMessage);
		    Console.Error.WriteLine("Console.Error - " + logMessage);
		    logger.WriteLine("TextWriter - " + logMessage);
		}

Выходные данные из объекта `TextWriter` отобразятся на панели мониторинга SDK веб-заданий, если перейти на страницу вызова определенной функции и нажать кнопку **Переключить вывод**:

![Click function invocation link](./media/websites-dotnet-webjobs-sdk-storage-queues-how-to/dashboardinvocations.png)

![Logs in function invocation page](./media/websites-dotnet-webjobs-sdk-storage-queues-how-to/dashboardlogs.png)

Последние 100 строк журналов приложений отобразятся на панели мониторинга SDK веб-заданий, если перейти на страницу веб-задания (не вызова функции) и нажать кнопку **Переключить вывод**.
 
![Click Toggle Output](./media/websites-dotnet-webjobs-sdk-storage-queues-how-to/dashboardapplogs.png)

Журналы приложений для непрерывных веб-заданий находятся по адресу /data/jobs/continuous/*{webjobname}*/job_log.txt в файловой системе веб-сайта.

		[09/26/2014 21:01:13 > 491e54: INFO] Console.Write - Hello world!
		[09/26/2014 21:01:13 > 491e54: ERR ] Console.Error - Hello world!
		[09/26/2014 21:01:13 > 491e54: INFO] Console.Out - Hello world!

В BLOB-объектах Azure журналы приложений выглядят следующим образом:
		2014-09-26T21:01:13,Information,contosoadsnew,491e54,635473620738373502,0,17404,17,Console.Write - Hello world!,
		2014-09-26T21:01:13,Error,contosoadsnew,491e54,635473620738373502,0,17404,19,Console.Error - Hello world!,
		2014-09-26T21:01:13,Information,contosoadsnew,491e54,635473620738529920,0,17404,17,Console.Out - Hello world!,

В таблице Azure журналы `Console.Out` и `Console.Error` выглядят следующим образом:

![Info log in table](./media/websites-dotnet-webjobs-sdk-storage-queues-how-to/tableinfo.png)

![Error log in table](./media/websites-dotnet-webjobs-sdk-storage-queues-how-to/tableerror.png)

## <a id="nextsteps"></a> Дальнейшие действия

В этом разделе были представлены образцы кода для распространенных сценариев работы с очередями Azure. Дополнительные сведения об использовании веб-заданий Azure и пакета SDK веб-заданий см. в разделе [Веб-задания Azure - рекомендуемые ресурсы](http://go.microsoft.com/fwlink/?linkid=390226).

<!--HONumber=35.2-->

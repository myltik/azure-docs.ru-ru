<properties linkid="" pageTitle="Как работать с хранилищем очередей Azure с помощью пакета SDK WebJobs" metaKeywords="хранилище очередей Azure SDK WebJobs .NET C#" description="Узнайте, как работать с хранилищем очередей Azure с помощью пакета SDK для WebJobs. Создание и удаление очередей; вставка, обзор, получение и удаление сообщений очереди, а также многое другое." metaCanonical="" disqusComments="1" umbracoNaviHide="1" services="web-sites,storage" documentationCenter=".NET" title="How to work with Azure queue storage using the WebJobs SDK" authors="tdykstra" manager="wpickett" editor="jimbe" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="10/13/2014" ms.author="tdykstra" />

# Как работать с хранилищем очередей Azure с помощью пакета SDK WebJobs

В этом руководстве описано создание кода C# для распространенных сценариев с помощью
Службы хранилища очередей Azure и пакета Azure SDK WebJobs версии 1.0.0.

В данном руководстве предполагается, что вы уже знаете, [что такое Webjobs SDK](../websites-webjobs-sdk-storage-queues-how-to) и [как выполнять базовые задания](../websites-dotnet-webjobs-sdk-get-started/), например установку пакета WebJobs SDK NuGet, создание учетной записи хранилища Azure, создание строк подключения для WebJobs SDK с переадресацией к вашей учетной записи хранилища.

Большинство фрагментов кода отображают только функции, а не код, который создает объект "JobHost", как показано в следующем примере:

		static void Main(string[] args)
		{
		    JobHost host = new JobHost();
		    host.RunAndBlock();
		}
		
## Оглавление

-   [Вызов функции при получении сообщения очереди](#trigger)
	- Сообщения очереди строк
	- Сообщения очереди объекта POCO
	- Функции Async
	- Алгоритм опроса
	- Параллельное выполнение
	- Получение метаданных очереди или сообщения очереди
	- Корректное завершение работы
-   [Создание сообщения очереди во время обработки сообщения очереди](#createqueue)
	- Сообщения очереди строк
	- Сообщения очереди объекта POCO
	- Создание нескольких сообщений
	- Используйте атрибуты очереди в тексте функции
-   [Чтение и запись BLOB-объектов во время обработки сообщения очереди](#blobs)
	- Сообщения очереди строк
	- Сообщения очереди объекта POCO
	- Используйте атрибуты большого двоичного объекта в тексте функции
-   [Обработка сообщений о сбое](#poison)
	- Автоматическая обработка сообщений о сбое
	- Обработка сообщений о сбое вручную
-   [Настройка параметров конфигурации](#config)
	- Настройка строк подключения пакета SDK в коде
	- Настройка параметров QueueTrigger
	- Получение имен очередей из конфигурации
-   [Вызов функции вручную](#manual)
-   [Ведение записей в журналах](#logs)
-   [Дальнейшие действия](#nextsteps)

## <a id="trigger"></a> Вызов функции при получении сообщения очереди

Чтобы написать функцию, которую пакет SDK вызывает при получении сообщения очереди, используйте атрибут "QueueTrigger" в строке или параметре POCO, в зависимости от ожидаемого содержания сообщения. Конструктор атрибута принимает строковый параметр, который указывает имя очереди для опроса. Можно также [задать имя очереди динамически](#config).

Если ваш веб-сайт работает на нескольких виртуальных машинах, веб-задание запустится на каждой машине, и каждая машина будет ожидать триггеры и пытаться запустить функции. В некоторых сценариях это может привести к тому, что некоторые функции обработают часть данных дважды, поэтому функции должны быть идемпотентными (написанными так, что постоянный их вызов с одинаковыми входными данными не создаст повторяющиеся результаты).  

### Сообщения очереди строк

В следующем примере очередь содержит строковое сообщение, поэтому "QueueTrigger" применяется к строковому параметру с именем "logMessage", который содержит содержимое сообщения очереди. Функция [записывает сообщение журнала на панель мониторинга](#logs).
 

		public static void ProcessQueueMessage([QueueTrigger("logqueue")] string logMessage, TextWriter logger)
		{
		    logger.WriteLine(logMessage);
		}

Помимо "string" этот параметр может иметь вид массива байтов, объекта "CloudQueueMessage" или объекта POCO, который определяется вами.

### Сообщения очереди объекта POCO

В следующем примере сообщение очереди содержит JSON для объекта "BlobInformation", который содержит свойство "BlobName". Пакет SDK автоматически выполняет десериализацию объекта.

		public static void WriteLogPOCO([QueueTrigger("logqueue")] BlobInformation blobInfo, TextWriter logger)
		{
		    logger.WriteLine("Сообщение очереди ссылается на большой двоичный объект: " + blobInfo.BlobName);
		}

В следующем примере показано, как создать сообщение очереди POCO без использования пакета SDK WebJobs, которое можно проанализировать с помощью пакета SDK. Пакет SDK использует [пакет Newtonsoft.Json NuGet](http://www.nuget.org/packages/Newtonsoft.Json) для сериализации и десериализации сообщений.

		BlobInformation blobInfo = new BlobInformation() { BlobName = "log.txt" };
		var queueMessage = new CloudQueueMessage(JsonConvert.SerializeObject(blobInfo));
		logQueue.AddMessage(queueMessage);

### Функции Async

Следующая функция async [записывает журнал на панель мониторинга](#logs).

		открытое асинхронное статическое задание ProcessQueueMessageAsync([QueueTrigger("logqueue")] строковое logMessage, регистратор TextWriter)
		{
		    await logger.WriteLineAsync(logMessage);
		}

Функции Async могут иметь токен отмены, как показано в следующем примере, который копирует BLOB-объект. (Описание заполнителя "queueTrigger" см. в разделе [BLOB-объекты](#blobs).)

		public async static Task ProcessQueueMessageAsyncCancellationToken(
		    [QueueTrigger("blobcopyqueue")] string blobName, 
		    [Blob("textblobs/{queueTrigger}",FileAccess.Read)] Stream blobInput,
		    [Blob("textblobs/{queueTrigger}-new",FileAccess.Write)] Stream blobOutput,
		    CancellationToken token)
		{
		    await blobInput.CopyToAsync(blobOutput, 4096, token);
		}

### Алгоритм опроса

Пакет SDK реализует алгоритм случайной экспоненциальной отсрочки для уменьшения влияния опроса, ожидающего в очереди, на затраты на транзакции хранения.  При обнаружении сообщения пакет SDK ожидает в течение двух секунд, затем проверяет наличие другого сообщения; если сообщение не найдено, SDK ожидает в течение 4 секунд перед повторной попыткой. После последующих неудачных попыток получения сообщения очереди время ожидания продолжает увеличиваться, пока не достигнет максимального времени ожидания (по умолчанию - одна минута). [Можно настроить максимальное время ожидания](#config).

### Параллельное выполнение

Если имеется несколько функций, которые прослушивают различные очереди, пакет SDK будет вызывать их параллельно при одновременном получении сообщений. 

То же условие действует при получении нескольких сообщений для одной очереди. По умолчанию пакет SDK получает пакет из 16 сообщений очереди одновременно и выполняет функцию, которая обрабатывает их параллельно. [Можно настроить размер пакета](#config). Если обрабатываемое число сообщений сокращается до половины размера пакета, SDK приступает к обработке сообщений другого пакета. Поэтому максимальное количество одновременных сообщений, обрабатываемых для каждой функции, составляет полтора размера пакета. Это ограничение применяется отдельно к каждой функции с атрибутом "QueueTrigger". Если вы не хотите использовать параллельное выполнение для сообщений, полученных в одной очереди, установите размер пакета, равный 1.

### <a id="queuemetadata"></a>Получение метаданных очереди или сообщения очереди

Следующие свойства сообщений можно получить путем добавления параметров к сигнатуре метода:

* `DateTimeOffset` expirationTime
* `DateTimeOffset` insertionTime
* `DateTimeOffset` nextVisibleTime
* `string` queueTrigger (contains message text)
* `string` id
* `string` popReceipt
* `int` dequeueCount

Если необходимо работать непосредственно с API хранилища Azure, можно также добавить параметр "CloudStorageAccount".

В следующем примере все эти метаданные записываются в журнал приложения INFO. В следующем примере и logMessage, и queueTrigger содержит содержимое сообщения очереди.

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
		        "logMessage={0}\n"
				nexpirationTime={1}\ninsertionTime={2}\n" +
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

Ниже приведен образец журнала, созданный с использованием образца кода:

		logMessage=Здравствуй, мир!
		expirationTime=10/14/2014 10:31:04 PM +00:00
		insertionTime=10/7/2014 10:31:04 PM +00:00
		nextVisibleTime=10/7/2014 10:41:23 PM +00:00
		id=262e49cd-26d3-4303-ae88-33baf8796d91
		popReceipt=AgAAAAMAAAAAAAAAfc9H0n/izwE=
		dequeueCount=1
		queue endpoint=https://contosoads.queue.core.windows.net/
		queueTrigger=Здравствуй, мир!

### <a id="graceful"></a>Корректное завершение работы

Функция, которая запускается в непрерывном веб-задании, может принимать параметр "CancellationToken", который позволяет операционной системе уведомлять функцию, если веб-задание будет скоро завершено. Это уведомление можно использовать, чтобы убедиться, что функция не завершается непредвиденным способом, при котором данные остаются в несогласованном состоянии.

На следующем примере показано, как проверить наличие предстоящего завершения веб-задания в функции.

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

**Примечание.** Панель мониторинга может неправильно показывать состояние и результат функций, работа которых завершена.
 
Дополнительные сведения см. в разделе [корректное завершение работы WebJobs](http://blog.amitapple.com/post/2014/05/webjobs-graceful-shutdown/#.VCt1GXl0wpR).   

## <a id="createqueue"></a> Создание сообщения очереди во время обработки сообщения очереди

Чтобы написать функцию, которая создает новое сообщение очереди, используйте атрибут "Queue" в выходной строке или параметре POCO. Подобно "QueueTrigger", имя очереди можно передать в виде строки или [задать динамически](#config).

### Сообщения очереди строк

В следующем примере создается новое сообщение очереди в очереди с именем "outputqueue" с тем же содержимым, что и в сообщении очереди, полученного в очереди с именем "inputqueue".

		public static void CreateQueueMessage(
		    [QueueTrigger("inputqueue")] string queueMessage,
		    [Queue("outputqueue")] out string outputQueueMessage )
		{
		    outputQueueMessage = queueMessage;
		}

Если объект не равен NULL, когда функция заканчивается, а тип выходного параметра имеет один из следующих типов, пакет SDK создает сообщение очереди:

* `string` 
* `byte[]`
* Сериализуемый тип POCO, который определяется
* `CloudQueueMessage`

Для создания нескольких сообщений см. **Создание нескольких сообщений** далее в этом разделе.

Также можно использовать тип выходного параметра "CloudQueue", если требуется вручную отправлять сообщения.

### Сообщения очереди объекта POCO

Чтобы создать сообщение очереди, содержащее объект POCO, а не строку, передайте тип POCO как выходной параметр в конструктор атрибута "Queue". 

		public static void CreateQueueMessage(
		    [QueueTrigger("inputqueue")] BlobInformation blobInfoInput,
		    [Queue("outputqueue")] out BlobInformation blobInfoOutput )
		{
		    blobInfoOutput = blobInfoInput;
		}

Пакет SDK автоматически сериализует объект в формат JSON. Сообщение очереди создается всегда, даже если объект имеет значение NULL.

### Создание нескольких сообщений

Чтобы создать несколько сообщений, задайте тип параметра для выходной очереди как "ICollector<T>" или "IAsyncCollector", как показано в следующем примере.

		public static void CreateQueueMessages(
		    [QueueTrigger("inputqueue")] string queueMessage,
		    [Queue("outputqueue")] ICollector<string> outputQueueMessage,
		    TextWriter logger)
		{
		    logger.WriteLine("Creating 2 messages in outputqueue");
		    outputQueueMessage.Add(queueMessage + "1");
		    outputQueueMessage.Add(queueMessage + "2");
		}

Каждое сообщение очереди создается сразу после вызова метода "Add".

### <a id="queuebody"></a>Используйте атрибуты очереди в тексте функции

Если необходимо выполнить некоторую работу в функции перед использованием атрибута "Queue" для создания сообщения очереди, можно использовать интерфейс "IBinder" для получения объекта "CloudQueue", который позволяет работать непосредственно с очередью. 

В следующем примере в выходной очереди создается новое сообщение с тем же содержимым, что и в сообщении входной очереди. Имя выходной очереди определяется кодом в тексте функции.

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

Атрибуты "Blob" и "Table" дают возможность чтения и записи BLOB-объектов и таблиц. Примеры в этом разделе применяются к BLOB-объектам.

Некоторые типы атрибута "Blob" могут использоваться с элементом "Stream", "TextReader", "TextWriter" и "CloudBlockBlob". Конструктор атрибута использует параметр "blobPath", указывающий контейнер и имя BLOB-объекта; если атрибут оформляет объект "Stream", другой параметр конструктора указывает режим "FileAccess", как режим чтения, записи или чтения/записи. Если необходимо выполнить некоторую работу в функции перед привязкой BLOB-объекта к объекту, можно использовать атрибут в тексте функции, [как показано ранее для атрибута Queue](#queuebody).

### Сообщения очереди строк

Для сообщения очереди, которое содержит строку, "queueTrigger" является заполнителем, который можно использовать в параметре "blobPath" атрибута "Blob" с содержимым сообщения. 

В следующем примере объекты "Stream" используются для чтения и записи BLOB-объектов. Сообщение очереди предоставляет имя BLOB-объекта, размещенного в контейнере textblobs. Копия BLOB-объекта с префиксом "-new" к имени создается в том же контейнере. 

		public static void ProcessQueueMessage(
		    [QueueTrigger("blobcopyqueue")] string blobName, 
		    [Blob("textblobs/{queueTrigger}",FileAccess.Read)] Stream blobInput,
		    [Blob("textblobs/{queueTrigger}-new",FileAccess.Write)] Stream blobOutput)
		{
		    blobInput.CopyTo(blobOutput, 4096);
		}

В следующем примере объект "CloudBlockBlob" используется для удаления BLOB-объекта.

		public static void DeleteBlob(
		    [QueueTrigger("deleteblobqueue")] string blobName,
		    [Blob("textblobs/{queueTrigger}")] CloudBlockBlob blobToDelete)
		{
		    blobToDelete.Delete();
		}

### Сообщения очереди объекта POCO

Для объектов POCO, хранящихся в формате JSON в сообщении очереди, можно использовать заполнители, которые присваивают имя свойствам объекта в параметре "blobPath" атрибута "Queue". Можно также использовать [имена свойства метаданных очереди](#queuemetadata) в качестве заполнителей. 

Следующий пример копирует BLOB-объект в новый BLOB-объект с другим расширением с помощью свойств объекта "BlobInformation" для указания имен входных и выходных BLOB-объектов. 
 
		public static void CopyBlobPOCO(
		    [QueueTrigger("copyblobqueue")] BlobInformation blobInfo,
		    [Blob("textblobs/{BlobName}", FileAccess.Read)] Stream blobInput,
		    [Blob("textblobs/{BlobNameWithoutExtension}.txt", FileAccess.Write)] Stream blobOutput)
		{
		    blobInput.CopyTo(blobOutput, 4096);
		}

В следующем примере показано, как создать сообщение очереди POCO без использования пакета SDK WebJobs, которое можно проанализировать с помощью пакета SDK. Пакет SDK использует [пакет Newtonsoft.Json NuGet](http://www.nuget.org/packages/Newtonsoft.Json) для сериализации и десериализации сообщений.

		BlobInformation blobInfo = new BlobInformation() { BlobName = "boot.log", BlobNameWithoutExtension = "boot" };
		var queueMessage = new CloudQueueMessage(JsonConvert.SerializeObject(blobInfo));
		logQueue.AddMessage(queueMessage);

## <a id="poison"></a> Обработка сообщений о сбое

Сообщения, содержимое которых вызывает сбой функции, называются *опасными сообщениями*. При сбое функции сообщение очереди не удаляется и в конечном итоге снова вызывается, при этом цикл повторяется. Пакет SDK автоматически может прервать цикл после ограниченного количества итераций или вы можете выполнить прерывание вручную.

### Автоматическая обработка сообщений о сбое

Пакет SDK выполняет 5 попыток вызова функции для обработки сообщения очереди. В случае неудачной пятой попытки сообщение перемещается в очередь опасных сообщений. [Можно настроить максимальное количество попыток](#config). 

Очередь опасных сообщений именуется *{originalqueuename}*-poison. Можно написать необходимую функцию для обработки сообщений из опасной очереди сообщений путем внесения их в журнал или отправки уведомления о необходимости ручного вмешательства. 

В следующем примере функция "CopyBlob" завершится ошибкой, если сообщение очереди содержит имя BLOB-объекта, который не существует. В этом случае сообщение перемещается из очереди copyblobqueue в очередь copyblobqueue-poison. Затем "ProcessPoisonMessage" вносит опасное сообщение в журнал.

		public static void CopyBlob(
		    [QueueTrigger("copyblobqueue")] string blobName,
		    [Blob("textblobs/{queueTrigger}",FileAccess.Read)] Stream blobInput,
		    [Blob("textblobs/{queueTrigger}-new", FileAccess.Write)] Stream blobOutput)
		{
		    blobInput.CopyTo(blobOutput, 4096);
		}
		
		public static void ProcessPoisonMessage(
		    [QueueTrigger("copyblobqueue-poison")] string blobName, TextWriter logger)
		{
		    logger.WriteLine("Failed to copy blob, name=" + blobName);
		}

На следующем рисунке показан вывод на консоль из этих функций при обработке опасного сообщения.

![Console output for poison message handling](./media/websites-dotnet-webjobs-sdk-storage-queues-how-to/poison.png)

### Обработка сообщений о сбое вручную

Можно узнать, сколько раз сообщение было выбрано для обработки, добавив параметр "int" с именем "dequeueCount" к функции. Можно проверить счетчик вывода из очереди в код функции и выполнить собственную обработку опасных сообщений, если их количество превышает пороговое значение, как показано в следующем примере.

		public static void CopyBlob(
		    [QueueTrigger("copyblobqueue")] string blobName, int dequeueCount,
		    [Blob("textblobs/{queueTrigger}",FileAccess.Read)] Stream blobInput,
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

Чтобы этот код работал должным образом, необходимо увеличить максимальное количество повторных попыток для автоматической обработки опасных сообщений, в противном случае счетчик вывода из очереди в этом примере никогда не превысит 5.

## <a id="config"></a> Настройка параметров конфигурации

Чтобы задать следующие параметры конфигурации, можно использовать тип "JobHostConfiguration":

* Настройка строк подключения SDK в коде.
* Настройка параметров "QueueTrigger", например максимального счетчика вывода из очереди.
* Настройка имен очередей из конфигурации.

### <a id="setconnstr"></a>Настройка строк подключения пакета SDK в коде

После установки строк подключения SDK в коде можно использовать собственные имена строк подключения в файлах конфигурации или переменных среды, как показано в следующем примере.

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

- Максимальное количество сообщений очереди, которые выбираются одновременно для выполнения в параллельном режиме (по умолчанию - 16).
- Максимальное количество повторных попыток перед отправкой сообщения очереди в очередь опасных сообщений (по умолчанию - 5).
- Максимальное время ожидания перед повторным опросом, когда очередь пуста (по умолчанию - 1 минута).

В следующем примере показано, как настроить эти параметры:

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

Тип "JobHostConfiguration" позволяет передавать в "NameResolver" объект, который предоставляет имя очереди для пакета SDK для атрибутов "QueueTrigger" и "Queue".

Например, предположим, что вы хотите использовать очередь с именем logqueuetest в тестовой среде и очередь с именем logqueueprod в производственной среде. Вместо жестко запрограммированного имени очереди требуется указать имя элемента в коллекции "appSettings", который будет иметь текущее имя очереди. Если ключ "appSettings" имеет значение logqueue, функция может выглядеть, как показано в следующем примере.

		public static void WriteLog([QueueTrigger("%logqueue%")] string logMessage)
		{
		    Console.WriteLine(logMessage);
		}

Затем класс "NameResolver" может получить имя очереди из "appSettings", как показано в следующем примере:

		public class QueueNameResolver : INameResolver
		{
		    public string Resolve(string name)
		    {
		        return ConfigurationManager.AppSettings[name].ToString();
		    }
		}

Необходимо передать класс "NameResolver" в объект "JobHost", как показано в следующем примере.

		static void Main(string[] args)
		{
		    JobHostConfiguration config = new JobHostConfiguration();
		    config.NameResolver = new QueueNameResolver();
		    JobHost host = new JobHost(config);
		    host.RunAndBlock();
		}
 

## <a id="manual"></a>Вызов функции вручную

Чтобы вручную инициировать функцию, используйте метод "Call" или "CallAsync" в объекте "JobHost" и атрибуте "NoAutomaticTrigger" в функции, как показано в следующем примере. 

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

## <a id="logs"></a>Ведение записей в журналах

Для ведения записей в журналах, которые отображаются в панели мониторинга WebJobs, связанной с вызовом определенной функции, используйте объект "TextWriter", полученный из параметра в подписи метода.

Чтобы внести запись в [журналы трассировки приложений](../web-sites-dotnet-troubleshoot-visual-studio/#logsoverview), используйте "Console.Out" (создает журналы с пометкой INFO) и "Console.Error" (создает журналы с пометкой ERROR). В качестве альтернативы можно использовать [Трассировка или TraceSource](http://blogs.msdn.com/b/mcsuksoldev/archive/2014/09/04/adding-trace-to-azure-web-sites-and-web-jobs.aspx).

Журналы приложений отображаются в файлах журналов веб-сайта, таблицах Azure или больших двоичных объектах Azure, в зависимости от настройки вашего веб-сайта Azure. Если программа выполняется в Azure WebJob, в панели мониторинга также отображаются последние 100 журналов приложений. (Журналы приложения не отображаются в панели мониторинга из программы, которая работает локально или в другой среде.)   

Ведение журналов можно отключить с помощью элемента [задание значения NULL для строки подключения панели мониторинга](#config).

В следующем примере показано несколько способов ведения журналов:

		public static void WriteLog(
		    [QueueTrigger("logqueue")] string logMessage,
		    TextWriter logger)
		{
		    Console.WriteLine("Console.Write - " + logMessage);
		    Console.Out.WriteLine("Console.Out - " + logMessage);
		    Console.Error.WriteLine("Console.Error - " + logMessage);
		    logger.WriteLine("TextWriter - " + logMessage);
		}

В панели мониторинга WebJobs SDK вывод из объекта "TextWriter" отображается при переходе на страницу для вызова определенной функции и выборе **Переключить вывод**:

![Click function invocation link](./media/websites-dotnet-webjobs-sdk-storage-queues-how-to/dashboardinvocations.png)

![Logs in function invocation page](./media/websites-dotnet-webjobs-sdk-storage-queues-how-to/dashboardlogs.png)

В панели мониторинга WebJobs SDK последние 100 строк журналов приложения отображаются при переходе на страницу для веб-задания (не для вызова функции) и выборе **Переключить вывод**.
 
![Click Toggle Output](./media/websites-dotnet-webjobs-sdk-storage-queues-how-to/dashboardapplogs.png)

В случае  непрерывного веб-задания журналы приложений отображаются в /data/jobs/continuous/*{webjobname}*/job_log.txt в файловой системе веб-сайта.

		[09/26/2014 21:01:13 > 491e54: INFO] Console.Write. Здравствуй, мир!
		[09/26/2014 21:01:13 > 491e54: ERR ] Console.Error. Здравствуй, мир!
		[09/26/2014 21:01:13 > 491e54: INFO] Console.Out. Здравствуй, мир!

В большом двоичном объекте Azure журналы приложений имеют следующий вид:
		2014-09-26T21:01:13,Information,contosoadsnew,491e54,635473620738373502,0,17404,17,Console.Write - Hello world!,
		2014-09-26T21:01:13,Error,contosoadsnew,491e54,635473620738373502,0,17404,19,Console.Error - Hello world!,
		2014-09-26T21:01:13,Information,contosoadsnew,491e54,635473620738529920,0,17404,17,Console.Out - Hello world!,

В таблице Azure журналы "Console.Out" и "Console.Error" имеют следующий вид:

![Info log in table](./media/websites-dotnet-webjobs-sdk-storage-queues-how-to/tableinfo.png)

![Error log in table](./media/websites-dotnet-webjobs-sdk-storage-queues-how-to/tableerror.png)

## <a id="nextsteps"></a> Дальнейшие действия

В этом разделе предоставлены образцы кода, которые показывают, как обрабатывать обычные сценарии для работы с очередями Azure. Дополнительные сведения о WebJobs Azure и пакетах SDK веб-заданий см. в разделе [Рекомендуемые ресурсы Azure WebJobs](http://go.microsoft.com/fwlink/?linkid=390226).

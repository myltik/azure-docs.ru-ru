<properties 
	pageTitle="Использование пакета SDK веб-заданий для работы с хранилищем очередей Azure" 
	description="Информация об использовании хранилища очередей Azure с пакетом SDK для WebJob Создание и удаление очередей; вставка, обзор, получение и удаление сообщений очереди, а также многое другое." 
	services="app-service\web, storage" 
	documentationCenter=".net" 
	authors="tdykstra" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="12/14/2015" 
	ms.author="tdykstra"/>

# Использование пакета SDK веб-заданий для работы с хранилищем очередей Azure

## Обзор

Это руководство содержит примеры кода C#, в которых показано, как использовать пакет SDK для заданий Azure WebJob версии 1 и более поздней версии со службой хранения очередей Azure.

В этом руководстве предполагается, что вы уже знаете, [как создать проект веб-задания в Visual Studio со строками подключения, указывающими на вашу учетную запись хранения](websites-dotnet-webjobs-sdk-get-started.md#configure-storage) или [несколько учетных записей хранения](https://github.com/Azure/azure-webjobs-sdk/blob/master/test/Microsoft.Azure.WebJobs.Host.EndToEndTests/MultipleStorageAccountsEndToEndTests.cs).

В большинстве фрагментов кода показаны только функции, а не код, создающий объект `JobHost`, как в следующем примере:

		static void Main(string[] args)
		{
		    JobHost host = new JobHost();
		    host.RunAndBlock();
		}
		
Руководство включает в себя следующие разделы:

-   [Как вызвать функцию при получении сообщения очереди](#trigger)
	- Строковые сообщения очереди
	- Сообщения очереди POCO
	- Асинхронные функции
	- Типы, с которыми используется атрибут QueueTrigger
	- Алгоритм опроса
	- Выполнение на нескольких экземплярах
	- Параллельное выполнение
	- Получение очереди или метаданных очереди сообщений
	- Корректное завершение работы
-   [Как создать сообщение очереди во время обработки сообщения очереди](#createqueue)
	- Строковые сообщения очереди
	- Сообщения очереди POCO
	- Создание нескольких сообщений или сообщений в асинхронных функциях
	- Типы, с которыми используется атрибут Queue
	- Использование атрибутов пакета SDK для заданий WebJob очереди в теле функции
-   [Как выполнить чтение и запись больших двоичных объектов во время обработки сообщения очереди](#blobs)
	- Строковые сообщения очереди
	- Сообщения очереди POCO
	- Типы, с которыми используется атрибут Blob
-   [Как обработать подозрительные сообщения](#poison)
	- Автоматическая обработка сообщений
	- Ручная обработка подозрительных сообщений
-   [Как установить параметры конфигурации](#config)
	- Установка строк подключения пакета SDK в коде
	- Настройка параметров атрибута QueueTrigger
	- Установка значений параметров конструктора пакета SDK для заданий WebJob в коде
-   [Как вызвать функцию вручную](#manual)
-   [Как записать журналы](#logs) 
-   [Как обрабатывать ошибки и как настроить время ожидания](#errors)
-   [Дальнейшие действия](#nextsteps)

## <a id="trigger"></a> Как вызвать функцию при получении сообщения очереди

Чтобы написать функцию, которую пакет SDK для веб-заданий будет вызывать при получении сообщения очереди, используйте атрибут `QueueTrigger`. Конструктор атрибута принимает строковый параметр, который указывает имя очереди для опроса. Также можно [задать имя очереди динамически](#config).

### Строковые сообщения очереди

В следующем примере очередь содержит строковое сообщение, поэтому `QueueTrigger` применяется к строковому параметру с именем `logMessage`, в котором находится содержимое сообщения очереди. Функция [записывает сообщение журнала на панель мониторинга](#logs).
 

		public static void ProcessQueueMessage([QueueTrigger("logqueue")] string logMessage, TextWriter logger)
		{
		    logger.WriteLine(logMessage);
		}

Помимо значения `string` возможными значениями этого параметра являются массив байтов, объект `CloudQueueMessage` или заданный вами объект POCO.

### Сообщения очереди [POCO](http://en.wikipedia.org/wiki/Plain_Old_CLR_Object)

В следующем примере сообщения очереди содержат JSON для объекта `BlobInformation`, у которого есть свойство `BlobName`. Пакет SDK автоматически выполняет десериализацию объекта.

		public static void WriteLogPOCO([QueueTrigger("logqueue")] BlobInformation blobInfo, TextWriter logger)
		{
		    logger.WriteLine("Queue message refers to blob: " + blobInfo.BlobName);
		}

Для сериализации и десериализации сообщений в пакете SDK используется [пакет NuGet Newtonsoft.Json](http://www.nuget.org/packages/Newtonsoft.Json). В случае создания сообщений очереди с помощью программы, которая не использует пакет SDK для заданий WebJob, чтобы создать сообщение очереди POCO, которое сможет проанализировать такой пакет, вы можете написать код по следующему образцу.

		BlobInformation blobInfo = new BlobInformation() { BlobName = "log.txt" };
		var queueMessage = new CloudQueueMessage(JsonConvert.SerializeObject(blobInfo));
		logQueue.AddMessage(queueMessage);

### Асинхронные функции

Следующая асинхронная функция [записывает журнал на панель мониторинга](#logs).

		public async static Task ProcessQueueMessageAsync([QueueTrigger("logqueue")] string logMessage, TextWriter logger)
		{
		    await logger.WriteLineAsync(logMessage);
		}

Асинхронные функции могут принимать [маркер отмены](http://www.asp.net/mvc/overview/performance/using-asynchronous-methods-in-aspnet-mvc-4#CancelToken), как показано в следующем примере, который копирует большой двоичный объект. (Объяснение заполнителя `queueTrigger` см. в разделе [Большие двоичные объекты](#blobs).)

		public async static Task ProcessQueueMessageAsyncCancellationToken(
		    [QueueTrigger("blobcopyqueue")] string blobName, 
		    [Blob("textblobs/{queueTrigger}",FileAccess.Read)] Stream blobInput,
		    [Blob("textblobs/{queueTrigger}-new",FileAccess.Write)] Stream blobOutput,
		    CancellationToken token)
		{
		    await blobInput.CopyToAsync(blobOutput, 4096, token);
		}

### <a id="qtattributetypes"></a> Типы, с которыми используется атрибут QueueTrigger

Вы можете использовать `QueueTrigger` со следующими типами:

* `string`
* тип POCO, сериализованный как JSON;
* `byte[]`
* `CloudQueueMessage`

### <a id="polling"></a> Алгоритм опроса

В пакете SDK реализован алгоритм случайной экспоненциальной отсрочки, который позволяет уменьшить влияние опроса очереди ожидающих задач на затраты на транзакции хранилища. При обнаружении сообщения пакет SDK ожидает в течение двух секунд и затем проверяет, не поступило ли еще одно сообщение; если сообщение не найдено, он ожидает около четырех секунд перед повторной попыткой. После последующих неудачных попыток получения сообщения очереди время ожидания продолжает увеличиваться, пока не достигнет максимального времени ожидания, по умолчанию — одна минута. [Можно настроить максимальное время ожидания](#config).

### <a id="instances"></a> Выполнение на нескольких экземплярах

Если ваше веб-приложение работает на нескольких экземплярах, веб-задания непрерывно выполняются на каждом компьютере, и каждый компьютер будет ожидать триггеры и пытаться запустить функции. Очередь триггера пакета SDK веб-заданий автоматически предотвращает многократную обработку функцией сообщения из очереди. Не обязательно создавать идемпотентные функции. Тем не менее, если вы хотите, чтобы выполнялся только один экземпляр функции, даже если существует несколько экземпляров несущего веб-приложения, можно использовать атрибут `Singleton`.

### <a id="parallel"></a> Параллельное выполнение

Если имеется несколько функций, которые прослушивают различные очереди, при одновременном получении сообщений пакет SDK будет вызывать их параллельно.

То же самое происходит при получении нескольких сообщений для одной очереди. По умолчанию SDK одновременно получает пакет из 16 сообщений очереди и выполняет функцию, которая обрабатывает их параллельно. [Можно настроить размер пакета](#config). Когда число обрабатываемых сообщений достигает половины размера пакета, SDK запрашивает следующий пакет и начинает обработку содержащихся в нем сообщений. Поэтому максимальное количество сообщений, одновременно обрабатываемых каждой функцией, в полтора раза больше размера пакета. Это ограничение применяется отдельно к каждой функции с атрибутом `QueueTrigger`.

Если вы не хотите, чтобы сообщения из одной очереди обрабатывались параллельно, можно установить размер пакета равный 1. См. также подраздел **More control over Queue processing** в статье [Azure WebJobs SDK 1.1.0 RTM](/blog/azure-webjobs-sdk-1-1-0-rtm/).

### <a id="queuemetadata"></a> Получение очереди или метаданных очереди сообщений

Следующие свойства сообщений можно получить путем добавления параметров к сигнатуре метода:

* `DateTimeOffset` expirationTime;
* `DateTimeOffset` insertionTime;
* `DateTimeOffset` nextVisibleTime;
* `string` queueTrigger (содержит текст сообщения);
* `string` id;
* `string` popReceipt;
* `int` dequeueCount.

Если требуется работать непосредственно с API службы хранилища Azure, можно также добавить параметр `CloudStorageAccount`.

В следующем примере все эти метаданные записываются в журнал приложения INFO. В примере содержимое сообщения очереди находится и в logMessage, и в queueTrigger.

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

### <a id="graceful"></a>Нормальное завершение работы

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
 
Дополнительную информацию см. в статье [Нормальное завершение работы веб-заданий](http://blog.amitapple.com/post/2014/05/webjobs-graceful-shutdown/#.VCt1GXl0wpR).

## <a id="createqueue"></a>Как создать сообщение очереди во время обработки сообщения очереди

Чтобы написать функцию, которая создает новое сообщение очереди, используйте атрибут `Queue`. Как и в случае с `QueueTrigger`, имя очереди можно передать в виде строки или [задать динамически](#config).

### Строковые сообщения очереди

Следующий пример неасинхронного кода создает новое сообщение очереди в очереди с именем «outputqueue» с тем же содержимым, что и сообщение очереди, поступившее в очередь с именем «inputqueue». (Для асинхронных функций используйте `IAsyncCollector<T>`, следуя указаниям далее в этом разделе.)


		public static void CreateQueueMessage(
		    [QueueTrigger("inputqueue")] string queueMessage,
		    [Queue("outputqueue")] out string outputQueueMessage )
		{
		    outputQueueMessage = queueMessage;
		}
  
### Сообщения очереди [POCO](http://en.wikipedia.org/wiki/Plain_Old_CLR_Object)

Чтобы создать сообщение очереди, содержащее объект POCO, а не строку, передайте тип POCO в качестве выходного параметра конструктору атрибута `Queue`.
 
		public static void CreateQueueMessage(
		    [QueueTrigger("inputqueue")] BlobInformation blobInfoInput,
		    [Queue("outputqueue")] out BlobInformation blobInfoOutput )
		{
		    blobInfoOutput = blobInfoInput;
		}

Пакет SDK автоматически выполняет сериализацию объекта в формат JSON. Сообщение очереди создается всегда, даже если объект имеет значение null.

### Создание нескольких сообщений или сообщений в асинхронных функциях

Чтобы создать несколько сообщений, установите для очереди вывода тип параметра `ICollector<T>` или `IAsyncCollector<T>`, как показано в следующем примере.

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

### Типы, с которыми используется атрибут Queue

Атрибут `Queue` можно использовать со следующими типами параметров:

* `out string` (создает сообщение очереди, если по завершении вызова функции значение параметра не равно null);
* `out byte[]` (действует как `string`); 
* `out CloudQueueMessage` (действует как `string`); 
* `out POCO` (сериализуемый тип, создает сообщение с пустым объектом, если по завершении функции значение параметра — null);
* `ICollector`
* `IAsyncCollector`
* `CloudQueue` (позволяет создавать сообщения вручную и напрямую с помощью API службы хранилища Azure).

### <a id="ibinder"></a>Использование атрибутов пакета SDK для веб-заданий очереди в основном тексте функции

Если перед использованием атрибута пакета SDK для веб-заданий, например `Queue`, `Blob` или `Table`, необходимо выполнить какие-либо действия с функцией, можно использовать интерфейс `IBinder`.

В следующем примере в выходной очереди создается новое сообщение с тем же содержимым, что и в сообщении входной очереди. Имя очереди вывода определяется кодом в теле функции.

		public static void CreateQueueMessage(
		    [QueueTrigger("inputqueue")] string queueMessage,
		    IBinder binder)
		{
		    string outputQueueName = "outputqueue" + DateTime.Now.Month.ToString();
		    QueueAttribute queueAttribute = new QueueAttribute(outputQueueName);
		    CloudQueue outputQueue = binder.Bind<CloudQueue>(queueAttribute);
		    outputQueue.AddMessage(new CloudQueueMessage(queueMessage));
		}

Интерфейс `IBinder` также можно использовать при работе с атрибутами `Table` и `Blob`.

## <a id="blobs"></a> Как выполнить чтение и запись больших двоичных объектов и таблиц во время обработки сообщения очереди

Атрибуты `Blob` и `Table` позволяют осуществлять чтение и запись больших двоичных объектов и таблиц. Примеры в этом разделе применимы к BLOB-объектам. Примеры кода, в которых показаны способы запуска процессов при создании или обновлении больших двоичных объектов, см. в статье [Как использовать хранилище больших двоичных объектов Azure с пакетом SDK для WebJob](websites-dotnet-webjobs-sdk-storage-blobs-how-to.md), а примеры кода для чтения и записи таблиц см. в статье [Как использовать табличное хранилище Azure с пакетом SDK для WebJob](websites-dotnet-webjobs-sdk-storage-tables-how-to.md).

### Сообщения очереди строк, которые запускают операции с большими двоичными объектами

Для сообщения очереди, содержащего строку, `queueTrigger` является заполнителем, который можно использовать в атрибуте `Blob` для параметра `blobPath`, в котором находится содержимое сообщения.

Следующий пример использует объекты `Stream` для чтения и записи больших двоичных объектов. Сообщение очереди — это имя большого двоичного объекта, размещенного в контейнере textblobs. Копия большого двоичного объекта с приставкой «-new», добавленной к имени, создается в том же контейнере.

		public static void ProcessQueueMessage(
		    [QueueTrigger("blobcopyqueue")] string blobName, 
		    [Blob("textblobs/{queueTrigger}",FileAccess.Read)] Stream blobInput,
		    [Blob("textblobs/{queueTrigger}-new",FileAccess.Write)] Stream blobOutput)
		{
		    blobInput.CopyTo(blobOutput, 4096);
		}

Конструктор атрибута `Blob` учитывает параметр `blobPath`, который указывает имя контейнера и большого двоичного объекта. Дополнительную информацию об этом заполнителе см. в статье [Как использовать хранилище больших двоичных объектов Azure с пакетом SDK для WebJob](websites-dotnet-webjobs-sdk-storage-blobs-how-to.md).

Если атрибут помечает объект `Stream`, другой параметр конструктора задает `FileAccess` в качестве режима чтения, записи или чтения и записи.

Следующий пример использует объект `CloudBlockBlob` для удаления большого двоичного объекта. Очередь сообщений — это имя большого двоичного объекта.

		public static void DeleteBlob(
		    [QueueTrigger("deleteblobqueue")] string blobName,
		    [Blob("textblobs/{queueTrigger}")] CloudBlockBlob blobToDelete)
		{
		    blobToDelete.Delete();
		}

### <a id="pocoblobs"></a>Сообщения очереди [POCO](http://en.wikipedia.org/wiki/Plain_Old_CLR_Object)

Для объектов POCO, сохраненных в формате JSON в сообщении очереди, можно использовать заполнители, которые называют свойства объектов в атрибуте `Queue` для параметра `blobPath`. Можно также использовать [имена свойства метаданных очереди](#queuemetadata) в качестве заполнителей.

В следующем примере выполняется копирование большого двоичного объекта в новый большой двоичный объект с другим расширением. Сообщение очереди представляет собой объект `BlobInformation` со свойствами `BlobName` и `BlobNameWithoutExtension`. В качестве заполнителей в пути к большому двоичному объекту используются имена свойств для атрибутов `Blob`.
 
		public static void CopyBlobPOCO(
		    [QueueTrigger("copyblobqueue")] BlobInformation blobInfo,
		    [Blob("textblobs/{BlobName}", FileAccess.Read)] Stream blobInput,
		    [Blob("textblobs/{BlobNameWithoutExtension}.txt", FileAccess.Write)] Stream blobOutput)
		{
		    blobInput.CopyTo(blobOutput, 4096);
		}

Для сериализации и десериализации сообщений в пакете SDK используется [пакет NuGet Newtonsoft.Json](http://www.nuget.org/packages/Newtonsoft.Json). В случае создания сообщений очереди с помощью программы, которая не использует пакет SDK для заданий WebJob, чтобы создать сообщение очереди POCO, которое сможет проанализировать такой пакет, вы можете написать код по следующему образцу.

		BlobInformation blobInfo = new BlobInformation() { BlobName = "boot.log", BlobNameWithoutExtension = "boot" };
		var queueMessage = new CloudQueueMessage(JsonConvert.SerializeObject(blobInfo));
		logQueue.AddMessage(queueMessage);

Если необходимо выполнить некоторую работу в функции перед привязкой большого двоичного объекта к объекту, можно использовать атрибут в основном тексте функции, [как показано выше для атрибута Queue](#ibinder).

### <a id="blobattributetypes"></a> Типы, которые можно использовать с атрибутом Blob
 
Атрибут `Blob` можно использовать со следующими типами:

* `Stream` (чтение или запись, указанные с помощью параметра конструктора FileAccess);
* `TextReader`
* `TextWriter`
* `string` (чтение);
* `out string` (запись; создает большой двоичный объект, только если для параметра строки не задано значение null при возврате функции);
* POCO (чтение);
* out POCO (запись; всегда создает пустой большой двоичный объект, если при возврате функции значение параметра POCO — null);
* `CloudBlobStream` (запись);
* `ICloudBlob` (чтение или запись);
* `CloudBlockBlob` (чтение или запись); 
* `CloudPageBlob` (чтение или запись); 

## <a id="poison"></a> Как обработать подозрительные сообщения

Сообщения, содержимое которых вызывает сбой функции, называются *подозрительными сообщениями*. При сбое функции сообщение очереди не удаляется и в конечном итоге забирается снова, вызывая повтор цикла. Пакет SDK может автоматически прервать цикл после ограниченного числа итераций или это можно сделать вручную.

### Автоматическая обработка сообщений

Пакет SDK вызывает функцию обработки сообщения очереди до 5 раз. В случае сбоя во время пятой попытки сообщение перемещается в очередь подозрительных сообщений. [Можно настроить максимальное количество попыток](#config).

Очередь подозрительных сообщений называется *{originalqueuename}*-poison. Можно написать функции для обработки сообщений из очереди подозрительных сообщений путем внесения их в журнал или отправки уведомления о необходимости ручного вмешательства.

В следующем примере функция `CopyBlob` завершится ошибкой, если сообщение очереди содержит имя несуществующего большого двоичного объекта. В таком случае сообщение перемещается из очереди copyblobqueue в очередь copyblobqueue-poison. Затем функция `ProcessPoisonMessage` записывает подозрительное сообщение в журнал.

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

![Вывод консоли для обработки подозрительных сообщений](./media/websites-dotnet-webjobs-sdk-storage-queues-how-to/poison.png)

### Ручная обработка подозрительных сообщений

Чтобы узнать, сколько попыток обработки сообщения было совершено, добавьте к функции параметр `int` с именем `dequeueCount`. Затем можно проверить счетчик вывода из очереди в коде функции и выполнить собственную обработку подозрительных сообщений, если это число превышает пороговое значение, как показано в следующем примере.

		public static void CopyBlob(
		    [QueueTrigger("copyblobqueue")] string blobName, int dequeueCount,
		    [Blob("textblobs/{queueTrigger}", FileAccess.Read)] Stream blobInput,
		    [Blob("textblobs/{queueTrigger}-new", FileAccess.Write)] Stream blobOutput,
		    TextWriter logger)
		{
		    if (dequeueCount > 3)
		    {
		        logger.WriteLine("Failed to copy blob, name=" + blobName);
		    }
		    else
		    {
		    blobInput.CopyTo(blobOutput, 4096);
		    }
		}

## <a id="config"></a>Как установить параметры конфигурации

Чтобы задать следующие параметры конфигурации, можно использовать тип `JobHostConfiguration`:

* Установка строк подключения пакета SDK в коде.
* Настройка таких параметров атрибута `QueueTrigger`, как максимальное значение счетчика вывода из очереди.
* Получение имен очередей из конфигурации

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

### <a id="configqueue"></a>Настройка параметров атрибута QueueTrigger

Можно настроить следующие параметры, которые применяются для обработки сообщений очереди:

- Максимальное количество сообщений очереди, которые забираются одновременно для параллельной обработки (по умолчанию — 16).
- Максимальное число повторных попыток перед отправкой сообщения очереди в очередь подозрительных сообщений (по умолчанию — 5).
- Максимальное время ожидания перед повторным опросом, когда очередь пуста (по умолчанию — 1 минута).

В примере показано, как настроить эти параметры:

		static void Main(string[] args)
		{
		    JobHostConfiguration config = new JobHostConfiguration();
		    config.Queues.BatchSize = 8;
		    config.Queues.MaxDequeueCount = 4;
		    config.Queues.MaxPollingInterval = TimeSpan.FromSeconds(15);
		    JobHost host = new JobHost(config);
		    host.RunAndBlock();
		}

### <a id="setnamesincode"></a>Установка значений параметров конструктора пакета SDK для веб-заданий в коде

Иногда требуется указать в коде имя очереди, имя большого двоичного объекта, контейнера или таблицы, а не жестко задавать их. Например, в некоторых случаях в файле конфигурации или в переменной среды необходимо указывать имя очереди для `QueueTrigger`.

Вы можете сделать это, передав объект `NameResolver` типу `JobHostConfiguration`. В параметрах конструктора атрибута пакета SDK для веб-заданий нужно указать специальные заполнители со знаками процента (%) с обеих сторон, тогда код `NameResolver` будет указывать фактические значения, которые следует использовать вместо этих заполнителей.

Например, предположим, что вы хотите использовать очередь с именем logqueuetest в тестовой среде и очередь с именем logqueueprod в производственной среде. Вместо фиксированного имени очереди требуется указать имя элемента в коллекции `appSettings` с фактическим именем очереди. Если logqueue — значение ключа `appSettings`, функция может выглядеть как в следующем примере.

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
 
**Примечание.** Имена очередей, таблиц и больших двоичных объектов не разрешаются при каждом вызове функции, а имена контейнеров больших двоичных объектов разрешаются только при запуске приложения. Во время выполнения задания нельзя изменить имя контейнера больших двоичных объектов.

## <a id="manual"></a>Как вызвать функции вручную

Чтобы вызвать функцию вручную, используйте метод `Call` или `CallAsync` объекта `JobHost` и атрибут `NoAutomaticTrigger` функции, как показано в следующем примере.

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

## <a id="logs"></a>Как записать журналы

На панели мониторинга отображаются журналы: один на странице для заданий WebJob, а другой — на отдельной странице для определенного вызова задания WebJob.

![Журналы на странице задания WebJob](./media/websites-dotnet-webjobs-sdk-storage-queues-how-to/dashboardapplogs.png)

![Журналы на странице вызова функций](./media/websites-dotnet-webjobs-sdk-storage-queues-how-to/dashboardlogs.png)

Выходные данные методов консоли, которые вызываются с помощью функции или метода `Main()`, отображаются на панели мониторинга на странице веб-задания, а не на странице для вызова определенного метода. Выходные данные объекта TextWriter, полученного из параметра в сигнатуре метода, отображаются на панели мониторинга на странице для вызова метода.

Выходные данные консоли нельзя связать с вызовом определенного метода, поскольку у консоли есть только один поток, хотя одновременно может выполняться сразу несколько функций. Поэтому пакет SDK обеспечивает вызов каждой функции с помощью уникального объекта модуля записи в журнал.

Чтобы внести запись в [журналы трассировки приложений](web-sites-dotnet-troubleshoot-visual-studio.md#logsoverview), используйте `Console.Out` (создает журналы с пометкой INFO) и `Console.Error` (создает журналы с пометкой ERROR). Кроме того, можно использовать [трассировку или TraceSource](http://blogs.msdn.com/b/mcsuksoldev/archive/2014/09/04/adding-trace-to-azure-web-sites-and-web-jobs.aspx), благодаря которым помимо информации и данных об ошибках можно получать подробные данные, предупреждения и оповещения о критическом уровне. Журналы трассировки приложений отображаются в файлах журналов веб-приложения, таблицах Azure или больших двоичных объектах Azure, в зависимости от настроек вашего веб-приложения Azure. Что касается всех выходных данных консоли, журналы последних 100 приложений отображаются на странице панели мониторинга для заданий WebJob, а не на странице для вызова функции.

Выходные данные консоли отображаются на панели мониторинга, только если программа запущена в задании Azure WebJob, а не локально или в другой среде.

Отключите ведение журнала панели мониторинга для сценариев с высокой пропускной способностью. По умолчанию SDK записывает журналы в хранилище, и это действие может привести к снижению производительности при обработке большого числа сообщений. Чтобы отключить ведение журнала, задайте для строки подключения мониторинга значение null, как показано в следующем примере.

		JobHostConfiguration config = new JobHostConfiguration();       
		config.DashboardConnectionString = “”;        
		JobHost host = new JobHost(config);
		host.RunAndBlock();

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

Выходные данные из объекта `TextWriter` отобразятся на панели мониторинга SDK для веб-заданий, если перейти на страницу вызова определенной функции и нажать кнопку **Переключить вывод**:

![Щелкнуть ссылку вызова функции](./media/websites-dotnet-webjobs-sdk-storage-queues-how-to/dashboardinvocations.png)

![Журналы на странице вызова функций](./media/websites-dotnet-webjobs-sdk-storage-queues-how-to/dashboardlogs.png)

Последние 100 строк вывода консоли отобразятся на панели мониторинга SDK для веб-заданий, если перейти на страницу для веб-задания (не для вызова функции) и нажать кнопку **Переключить вывод**.
 
![Нажать кнопку «Переключить вывод»](./media/websites-dotnet-webjobs-sdk-storage-queues-how-to/dashboardapplogs.png)

Журналы приложений для непрерывных веб-заданий находятся по адресу data/jobs/continuous/*{имя\_веб-задания}*/job\_log.txt в файловой системе веб-приложения.

		[09/26/2014 21:01:13 > 491e54: INFO] Console.Write - Hello world!
		[09/26/2014 21:01:13 > 491e54: ERR ] Console.Error - Hello world!
		[09/26/2014 21:01:13 > 491e54: INFO] Console.Out - Hello world!

Вот как выглядят журналы приложений в большом двоичном объекте Azure: 2014-09-26T21:01:13,Information,contosoadsnew,491e54,635473620738373502,0,17404,17,Console.Write - Hello world!, 2014-09-26T21:01:13,Error,contosoadsnew,491e54,635473620738373502,0,17404,19,Console.Error - Hello world!, 2014-09-26T21:01:13,Information,contosoadsnew,491e54,635473620738529920,0,17404,17,Console.Out - Hello world!,

В таблице Azure журналы `Console.Out` и `Console.Error` выглядят следующим образом:

![Журнал Info в таблице](./media/websites-dotnet-webjobs-sdk-storage-queues-how-to/tableinfo.png)

![Журнал Error в таблице](./media/websites-dotnet-webjobs-sdk-storage-queues-how-to/tableerror.png)

Если вы хотите подключить собственное средство ведения журнала, см. [этот пример](http://github.com/Azure/azure-webjobs-sdk-samples/blob/master/BasicSamples/MiscOperations/Program.cs).

## <a id="errors"></a>Как обрабатывать ошибки и как настроить время ожидания

Пакет SDK веб-заданий также включает в себя атрибут [Timeout](http://github.com/Azure/azure-webjobs-sdk-samples/blob/master/BasicSamples/MiscOperations/Functions.cs), который можно использовать для отмены функции, если ее выполнение не завершается в течение заданного времени. А если вы хотите создавать предупреждение, когда в течение указанного периода времени происходит слишком много ошибок, можно использовать атрибут `ErrorTrigger`. Вот [пример ErrorTrigger](https://github.com/Azure/azure-webjobs-sdk-extensions/wiki/Error-Monitoring).

```
public static void ErrorMonitor(
[ErrorTrigger("00:01:00", 1)] TraceFilter filter, TextWriter log,
[SendGrid(
    To = "admin@emailaddress.com",
    Subject = "Error!")]
 SendGridMessage message)
{
    // log last 5 detailed errors to the Dashboard
   log.WriteLine(filter.GetDetailedMessage(5));
   message.Text = filter.GetDetailedMessage(1);
}
```

Можно также динамически отключать и включать функции, чтобы управлять их активацией. Для вы можете использовать параметр конфигурации, который может быть параметром приложения или именем переменной среды. Пример кода см. описание атрибута `Disable` в [репозитории примеров для пакета SDK](https://github.com/Azure/azure-webjobs-sdk-samples/blob/master/BasicSamples/MiscOperations/Functions.cs).

## <a id="nextsteps"></a>Дальнейшие действия

В этом руководстве предоставлены примеры кода обработки обычных сценариев для работы с очередями Azure. Дополнительную информацию об использовании веб-заданий Azure и пакета SDK для веб-заданий см. в статье [Рекомендуемые ресурсы по веб-заданиям Azure](http://go.microsoft.com/fwlink/?linkid=390226).
 

<!---HONumber=AcomDC_1217_2015-->
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
	ms.date="04/03/2015" 
	ms.author="tdykstra"/>

# Использование пакета SDK веб-заданий для работы с хранилищем очередей Azure

## Обзор

Это руководство содержит примеры кода C#, в которых показано, как использовать пакет SDK для заданий Azure WebJob версии 1 и более поздней версии со службой хранения очередей Azure.

В этом руководстве предполагается, что вы уже знаете, [как создавать проект веб-заданий в Visual Studio со строками подключения, указывающими на учетную запись хранения](websites-dotnet-webjobs-sdk-get-started.md).

В большинстве фрагментов кода показаны только функции, а не код, создающий объект `JobHost`, как в следующем примере:

		static void Main(string[] args)
		{
		    JobHost host = new JobHost();
		    host.RunAndBlock();
		}
		
Руководство включает в себя следующие разделы:

-   [Вызов функции при получении сообщения очереди](#trigger)
	- Строковые сообщения очереди
	- Сообщения очереди POCO
	- Асинхронные функции
	- Типы, с которыми используется атрибут QueueTrigger
	- Алгоритм опроса
	- Выполнение на нескольких экземплярах
	- Параллельное выполнение
	- Получение очереди или метаданных очереди сообщений
	- Корректное завершение работы
-   [Создание сообщения очереди во время обработки сообщения очереди](#createqueue)
	- Строковые сообщения очереди
	- Сообщения очереди POCO
	- Создание нескольких сообщений или сообщений в асинхронных функциях
	- Типы, с которыми используется атрибут Queue
	- Использование атрибутов пакета SDK для заданий WebJob очереди в теле функции
-   [Чтение и запись BLOB-объектов во время обработки сообщения очереди](#blobs)
	- Строковые сообщения очереди
	- Сообщения очереди POCO
	- Типы, с которыми используется атрибут Blob
-   [Способ обработки подозрительных сообщений](#poison)
	- Автоматическая обработка сообщений
	- Ручная обработка подозрительных сообщений
-   [Установка параметров конфигурации](#config)
	- Установка строк подключения пакета SDK в коде
	- Настройка параметров атрибута QueueTrigger
	- Установка значений параметров конструктора пакета SDK для заданий WebJob в коде
-   [Ручной вызов функции](#manual)
-   [Запись журналов](#logs)
-   [Дальнейшие действия](#nextsteps)

## <a id="trigger"></a> Вызов функции при получении сообщения очереди

Чтобы написать функцию, которую пакет SDK для заданий WebJob будет вызывать при получении сообщения очереди, используйте атрибут `QueueTrigger`. Конструктор атрибута принимает строковый параметр, который указывает имя очереди для опроса. Также можно [задать имя очереди динамически](#config).

### Строковые сообщения очереди

В следующем примере очередь содержит строковое сообщение, поэтому `QueueTrigger` применяется к строковому параметру с именем `logMessage`, в котором находится содержимое сообщения очереди. Функция [записывает сообщение журнала в панель мониторинга](#logs).
 

		public static void ProcessQueueMessage([QueueTrigger("logqueue")] string logMessage, TextWriter logger)
		{
		    logger.WriteLine(logMessage);
		}

Помимо значения `string` возможными значениями этого параметра являются массив байтов, объект `CloudQueueMessage` или заданный вами объект POCO.

### Сообщения очереди [POCO](http://en.wikipedia.org/wiki/Plain_Old_CLR_Object)

В следующем примере сообщения очереди содержат JSON для объекта `BlobInformation`, который имеет свойство `BlobName`. Пакет SDK автоматически выполняет десериализацию объекта.

		public static void WriteLogPOCO([QueueTrigger("logqueue")] BlobInformation blobInfo, TextWriter logger)
		{
		    logger.WriteLine("Queue message refers to blob: " + blobInfo.BlobName);
		}

Для сериализации и десериализации сообщений в пакете SDK используется [пакет NuGet Newtonsoft.Json](http://www.nuget.org/packages/Newtonsoft.Json). В случае создания сообщений очереди с помощью программы, которая не использует пакет SDK для заданий WebJob, чтобы создать сообщение очереди POCO, которое сможет проанализировать такой пакет, вы можете написать код по следующему образцу. 

		BlobInformation blobInfo = new BlobInformation() { BlobName = "log.txt" };
		var queueMessage = new CloudQueueMessage(JsonConvert.SerializeObject(blobInfo));
		logQueue.AddMessage(queueMessage);

### Асинхронные функции

Следующая асинхронная функция [записывает журнал для панели мониторинга](#logs).

		public async static Task ProcessQueueMessageAsync([QueueTrigger("logqueue")] string logMessage, TextWriter logger)
		{
		    await logger.WriteLineAsync(logMessage);
		}

Асинхронные функции могут принимать [маркеры отмены](http://www.asp.net/mvc/overview/performance/using-asynchronous-methods-in-aspnet-mvc-4#CancelToken), как показано в следующем примере, который копирует большой двоичный объект. (Объяснение заполнителя `queueTrigger` см. в разделе [Большие двоичные объекты](#blobs) ).

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

* string
* тип POCO, сериализованный как JSON;
* byte[]
* CloudQueueMessage

### <a id="polling"></a> Алгоритм опроса

В пакете SDK реализован алгоритм случайной экспоненциальной отсрочки, который позволяет уменьшить влияние опроса очереди ожидающих задач на затраты на транзакции хранилища.  При обнаружении сообщения пакет SDK ожидает в течение двух секунд и затем проверяет, не поступило ли еще одно сообщение; если сообщение не найдено, он ожидает около четырех секунд перед повторной попыткой. После последующих неудачных попыток получения сообщения очереди время ожидания продолжает увеличиваться, пока не достигнет максимального времени ожидания, по умолчанию - одна минута. [Можно настроить максимальное время ожидания](#config).

### <a id="instances"></a> Выполнение на нескольких экземплярах

Если ваше веб-приложение работает на нескольких экземплярах, веб-задания непрерывно выполняются на каждой машине, и каждая машина будет ожидать триггеры и пытаться запустить функции. В некоторых сценариях это может привести к тому, что некоторые функции обработают часть данных дважды, поэтому функции должны быть идемпотентными (написанными так, чтобы их постоянный вызов с одинаковыми входными данными не создавал дублирующие результаты).  

### <a id="parallel"></a> Параллельное выполнение

Если имеется несколько функций, которые прослушивают различные очереди, при одновременном получении сообщений пакет SDK будет вызывать их параллельно. 

То же самое происходит при получении нескольких сообщений для одной очереди. По умолчанию SDK одновременно получает пакет из 16 сообщений очереди и выполняет функцию, которая обрабатывает их параллельно. [Можно настроить размер пакета](#config). Когда число обрабатываемых сообщений достигает половины размера пакета, SDK запрашивает следующий пакет и начинает обработку содержащихся в нем сообщений. Поэтому максимальное количество сообщений, одновременно обрабатываемых каждой функцией, в полтора раза больше размера пакета. Это ограничение применяется отдельно к каждой функции с атрибутом `QueueTrigger`. Если вы не хотите, чтобы сообщения из одной очереди обрабатывались параллельно, установите размер пакета равный 1.

### <a id="queuemetadata"></a>Получение очереди или метаданных очереди сообщений

Следующие свойства сообщений можно получить путем добавления параметров к сигнатуре метода:

* `DateTimeOffset` expirationTime
* `DateTimeOffset` insertionTime
* `DateTimeOffset` nextVisibleTime
* `string` queueTrigger (contains message text)
* `string` id
* `string` popReceipt
* `int` dequeueCount

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

### <a id="graceful"></a>Корректное завершение работы

Функция, которая запускается в постоянном задании WebJob, может принимать параметр `CancellationToken`, который позволяет операционной системе уведомлять ее о том, что выполнение задания WebJob будет завершено. Это уведомление можно использовать для предотвращения ситуации, когда выполнение функции завершается неожиданно, оставляя данные в несогласованном состоянии.

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

## <a id="createqueue"></a>Создание сообщения очереди во время обработки сообщения очереди

Чтобы написать функцию, которая создает новое сообщение очереди, используйте атрибут `Queue`. Как и в случае с `QueueTrigger`, имя очереди можно передать в виде строки или [задать динамически](#config).

### Строковые сообщения очереди

Следующий пример неасинхронного кода создает новое сообщение очереди в очереди с именем "outputqueue" с тем же содержимым, что и сообщение очереди, поступившее в очередь с именем "inputqueue". (Для асинхронных функций используйте `IAsyncCollector<T>`, следуя указаниям далее в этом разделе.)


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

* out string (создает сообщение очереди, если по завершению вызова функции значение параметра не равно null)
* out byte[] (работает, как `string`) 
* out CloudQueueMessage (работает, как `string`) 
* out POCO (сериализуемый тип, создает сообщение с пустым объектом, если по завершению функции значение параметра - null)
* ICollector
* IAsyncCollector
* CloudQueue (позволяет создавать сообщения вручную непосредственно с помощью API службы хранилища Azure)

### <a id="ibinder"></a>Использование атрибутов пакета SDK для заданий WebJob очереди в теле функции

Если перед использованием атрибута пакета SDK для заданий WebJob, например `Queue`, `Blob` или `Table`, необходимо выполнить какие-либо действия с функций, можно использовать интерфейс `IBinder`.

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

## <a id="blobs"></a> Чтение и запись больших двоичных объектов и таблиц во время обработки сообщения очереди

Атрибуты `Blob` и `Table` позволяют осуществлять чтение и запись больших двоичных объектов и таблиц. Примеры в этом разделе применимы к BLOB-объектам. Образцы кода для запуска процессов во время создания или обновления больших двоичных объектов см. в статье [Использование хранилища больших двоичных объектов Azure с пакетом SDK для веб-заданий](websites-dotnet-webjobs-sdk-storage-blobs-how-to.md), образцы кода для чтения и записи таблиц см. в статье [Использование табличного хранилища Azure с пакетом SDK для веб-заданий](websites-dotnet-webjobs-sdk-storage-tables-how-to.md).

### Сообщения очереди строк, которые запускают операции с большими двоичными объектами

Для сообщения очереди, содержащего строку, `queueTrigger` является заполнителем, который можно использовать в параметре `Blob` атрибута `blobPath`, в котором находится содержимое сообщения. 

Следующий пример использует объекты `Stream` для чтения и записи больших двоичных объектов. Сообщение очереди - это имя большого двоичного объекта, размещенного в контейнере textblobs. Копия большого двоичного объекта с приставкой "-new", добавленной к имени, создается в том же контейнере. 

		public static void ProcessQueueMessage(
		    [QueueTrigger("blobcopyqueue")] string blobName, 
		    [Blob("textblobs/{queueTrigger}",FileAccess.Read)] Stream blobInput,
		    [Blob("textblobs/{queueTrigger}-new",FileAccess.Write)] Stream blobOutput)
		{
		    blobInput.CopyTo(blobOutput, 4096);
		}

Конструктор атрибута `Blob` учитывает параметр `blobPath`, который указывает имя контейнера и большого двоичного объекта. Дополнительную информацию об этом заполнителе см. в статье [Как использовать хранилище больших двоичных объектов Azure с пакетом SDK для веб-заданий](websites-dotnet-webjobs-sdk-storage-blobs-how-to.md), 

Если атрибут помечает объект `Stream`, другой параметр конструктора задает в качестве режима `FileAccess` режим чтения, записи или чтения и записи. 

Следующий пример использует объект `CloudBlockBlob` для удаления большого двоичного объекта. Очередь сообщений - это имя большого двоичного объекта.

		public static void DeleteBlob(
		    [QueueTrigger("deleteblobqueue")] string blobName,
		    [Blob("textblobs/{queueTrigger}")] CloudBlockBlob blobToDelete)
		{
		    blobToDelete.Delete();
		}

### <a id="pocoblobs"></a> Сообщения очереди [POCO ](http://en.wikipedia.org/wiki/Plain_Old_CLR_Object)

Для объектов POCO, сохраненных в формате JSON в сообщении очереди, можно использовать заполнители, которые называют свойства объектов в параметре `blobPath` атрибута `Queue`. В качестве заполнителей можно также использовать [имена свойств метаданных очереди](#queuemetadata) . 

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

Если необходимо выполнить некоторую работу в функции перед привязкой BLOB-объекта к объекту, можно использовать атрибут в теле функции, [как показано выше для атрибута Queue](#ibinder).

### <a id="blobattributetypes"></a> Типы, которые можно использовать с атрибутом Blob
 
Атрибут `Blob` можно использовать со следующими типами:

* Stream (чтение или запись, указанные с помощью параметра конструктора FileAccess)
* TextReader
* TextWriter
* string (чтение)
* out string (запись; создает большой двоичный объект, только если для параметра строки не задано значение null при возврате функции)
* POCO (чтение);
* out POCO (запись; всегда создает пустой большой двоичный объект, если при возврате функции значение параметра POCO - null);
* CloudBlobStream (запись)
* ICloudBlob (чтение или запись)
* CloudBlockBlob (чтение или запись) 
* CloudPageBlob (чтение или запись) 

## <a id="poison"></a>Способ обработки подозрительных сообщений

Сообщения, содержимое которых вызывает сбой функции называются *poison messages*. При сбое функции сообщение очереди не удаляется и в конечном итоге забирается снова, вызывая повтор цикла. Пакет SDK может автоматически прервать цикл после ограниченного числа итераций или это можно сделать вручную.

### Автоматическая обработка сообщений

Пакет SDK вызывает функцию обработки сообщения очереди до 5 раз. В случае сбоя во время пятой попытки сообщение перемещается в очередь подозрительных сообщений. [Можно настроить максимальное число повторных попыток](#config). 

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

## <a id="config"></a> Установка параметров конфигурации

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

- Максимальное количество сообщений очереди, которые забираются одновременно для параллельной обработки (по умолчанию - 16).
- Максимальное число повторных попыток перед отправкой сообщения очереди в очередь подозрительных сообщений (по умолчанию - 5).
- Максимальное время ожидания перед повторным опросом, когда очередь пуста (по умолчанию - 1 минута).

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

### <a id="setnamesincode"></a>Установка значений параметров конструктора пакета SDK для заданий WebJob в коде

Иногда требуется указать в коде имя очереди, имя большого двоичного объекта, контейнера или таблицы, а не жестко задавать их. Например, в некоторых случаях в файле конфигурации или в переменной среды необходимо указывать имя очереди для `QueueTrigger`. 

Вы можете делать это, передав объект `NameResolver` типу `JobHostConfiguration`. В параметрах конструктора атрибута пакета SDK для заданий WebJobs нужно указать специальные заполнители со знаками процента (%) с обеих сторон, а код `NameResolver` будет указывать фактические значения, которые следует использовать вместо этих заполнителей.

Например, предположим, что вы хотите использовать очередь с именем logqueuetest в тестовой среде и очередь с именем logqueueprod в производственной среде. Вместо фиксированного имени очереди требуется указать имя элемента в коллекции `appSettings` с фактическим именем очереди. Если значение ключа `appSettings` - logqueue, функция может выглядеть как в следующем примере.

		public static void WriteLog([QueueTrigger("%logqueue%")] string logMessage)
		{
		    Console.WriteLine(logMessage);
		}

Your `NameResolver` class could then get the queue name from `appSettings` as shown in the following example:

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
 
**Примечание.** Имена очередей, таблиц и больших двоичных объектов не разрешаются при каждом вызове функции, а имена контейнеров больших двоичных объектов разрешаются только при запуске приложения. Нельзя менять имя контейнера больших двоичных объектов во время выполнения задания. 

## <a id="manual"></a>Вызов функции вручную

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

На панели мониторинга отображаются журналы: один на странице для заданий WebJob, а другой - на отдельной странице для определенного вызова задания WebJob. 

![Журналы на странице задания WebJob](./media/websites-dotnet-webjobs-sdk-storage-queues-how-to/dashboardapplogs.png)

![Журналы на странице вызова функций](./media/websites-dotnet-webjobs-sdk-storage-queues-how-to/dashboardlogs.png)

Выходные данные методов консоли, которые вызываются с помощью функции или метода `Main()`, отображаются на панели мониторинга на странице задания WebJob, а не на странице для вызова определенного метода. Выходные данные объекта TextWriter , полученного из параметра в сигнатуре метода, отображаются на панели мониторинга на странице для вызова метода.

Выходные данные консоли нельзя связать с вызовом определенного метода, поскольку у консоли есть только один поток, хотя одновременно может выполняться сразу несколько функций. Поэтому пакет SDK обеспечивает вызов каждой функции с помощью уникального объекта модуля записи в журнал.

Для записи [журналов трассировки приложения](web-sites-dotnet-troubleshoot-visual-studio.md#logsoverview)используйте `Console.Out` (создает журналы, помеченные как INFO) и `Console.Error` (создает журналы, помеченные как). Кроме того, можно использовать [трассировку или TraceSource](http://blogs.msdn.com/b/mcsuksoldev/archive/2014/09/04/adding-trace-to-azure-web-sites-and-web-jobs.aspx), благодаря которым помимо информации и данных об ошибках можно получать подробные данные, предупреждения и оповещения о критическом уровне. Журналы трассировки приложений отображаются в файлах журналов веб-приложения, таблицах Azure или больших двоичных объектах Azure, в зависимости от настроек вашего веб-приложения Azure. Что касается всех выходных данных консоли, журналы последних 100 приложений отображаются на странице панели мониторинга для заданий WebJob, а не на странице для вызова функции. 

Выходные данные консоли отображаются на панели мониторинга, только если программа запущена в задании Azure WebJob, а не локально или в другой среде.

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

Выходные данные из объекта `TextWriter` отобразятся на панели мониторинга SDK для заданий WebJob, если перейти на страницу вызова определенной функции и нажать кнопку **Переключить вывод**:

![Щелкнуть ссылку вызова функции](./media/websites-dotnet-webjobs-sdk-storage-queues-how-to/dashboardinvocations.png)

![Журналы на странице вызова функций](./media/websites-dotnet-webjobs-sdk-storage-queues-how-to/dashboardlogs.png)

Последние 100 строк вывода консоли отобразятся на панели мониторинга SDK заданий WebJob, если перейти на страницу задания WebJob (не вызова функции) и нажать кнопку **Переключить вывод**.
 
![Нажать кнопку "Переключить вывод"](./media/websites-dotnet-webjobs-sdk-storage-queues-how-to/dashboardapplogs.png)

При непрерывном задании WebJob журналы приложений отображаются в /data/jobs/continuous/*{webjobname}*/job_log.txt в файловой системе веб-приложения.

		[09/26/2014 21:01:13 > 491e54: INFO] Console.Write - Hello world!
		[09/26/2014 21:01:13 > 491e54: ERR ] Console.Error - Hello world!
		[09/26/2014 21:01:13 > 491e54: INFO] Console.Out - Hello world!

В большом двоичном объекте Azure журналы приложений выглядят следующим образом:
		2014-09-26T21:01:13,Information,contosoadsnew,491e54,635473620738373502,0,17404,17,Console.Write - Hello world!,
		2014-09-26T21:01:13,Error,contosoadsnew,491e54,635473620738373502,0,17404,19,Console.Error - Hello world!,
		2014-09-26T21:01:13,Information,contosoadsnew,491e54,635473620738529920,0,17404,17,Console.Out - Hello world!,

В таблице Azure журналы `Console.Out` и `Console.Error` выглядят следующим образом:

![Журнал Info в таблице](./media/websites-dotnet-webjobs-sdk-storage-queues-how-to/tableinfo.png)

![Журнал Error в таблице](./media/websites-dotnet-webjobs-sdk-storage-queues-how-to/tableerror.png)

## <a id="nextsteps"></a> Дальнейшие действия

В этом руководстве предоставлены примеры кода обработки обычных сценариев для работы с очередями Azure. Дополнительные сведения об использовании веб-заданий Azure и пакета SDK веб-заданий см. в разделе [Веб-задания Azure - рекомендуемые ресурсы](http://go.microsoft.com/fwlink/?linkid=390226).

<!--HONumber=52-->
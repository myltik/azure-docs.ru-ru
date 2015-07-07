<properties 
	pageTitle="Как использовать хранилище больших двоичных объектов Azure с пакетом SDK для WebJob" 
	description="Информация об использовании хранилища больших двоичных объектов Azure с пакетом SDK для WebJob Вызов процесса при появлении нового BLOB-объекта в контейнере и обработка «подозрительных BLOB-объектов»." 
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
	ms.date="06/08/2015" 
	ms.author="tdykstra"/>

# Как использовать хранилище больших двоичных объектов Azure с пакетом SDK для WebJob

## Обзор

В этом руководстве приведены примеры кода на C#для запуска процессов при создании или обновлении большого двоичного объекта Azure. В примерах кода используется [Пакет SDK для веб-заданий](websites-dotnet-webjobs-sdk.md) версии 1.x.

Примеры кода для создания BLOB-объектов см. в статье [Использование пакета SDK для веб-заданий для работы с хранилищем очередей Azure](websites-dotnet-webjobs-sdk-storage-queues-how-to.md).
		
В этом руководстве предполагается, что вы уже знаете, [как создавать проект веб-заданий в Visual Studio со строками подключения, указывающими на учетную запись хранения](websites-dotnet-webjobs-sdk-get-started.md)

## <a id="trigger"></a> Вызов функции при создании или обновлении BLOB-объекта

В этом разделе показано, как использовать атрибут `BlobTrigger`.

> **Примечание.** Пакет SDK для заданий проверяет файлы журнала и отслеживает новые или измененные BLOB-объекты. Это медленный процесс. После создания BLOB-объекта функция может не вызываться несколько минут или даже дольше. Если вашему приложению нужно обработать BLOB-объект немедленно, рекомендуем во время его создания создать сообщение очереди и использовать атрибут триггера [QueueTrigger](websites-dotnet-webjobs-sdk-storage-queues-how-to.md#trigger) вместо атрибута `BlobTrigger` в функции, которая обрабатывает BLOB-объект.

### Один заполнитель для имени большого двоичного объекта и расширения  

Следующий пример кода копирует текстовые BLOB-объекты, которые появляются в контейнере *input*, в контейнер *output*:

		public static void CopyBlob([BlobTrigger("input/{name}")] TextReader input,
		    [Blob("output/{name}")] out string output)
		{
		    output = input.ReadToEnd();
		}

Конструктор атрибута принимает параметр строки, который указывает имя контейнера и заполнитель для имени большого двоичного объекта. В этом примере при создании BLOB-объекта с именем *Blob1.txt* в контейнере *input* функция создает BLOB-объект *Blob1.txt* в контейнере *output*.

Можно указать шаблон имени с заполнителем имени большого двоичного объекта, как показано в следующем примере кода:

		public static void CopyBlob([BlobTrigger("input/original-{name}")] TextReader input,
		    [Blob("output/copy-{name}")] out string output)
		{
		    output = input.ReadToEnd();
		}

Этот код копируется только большие двоичные объекты, имена которых начинаются с original-. Например, *original-Blob1.txt* в контейнере *input* копируется в *copy-Blob1.txt* в контейнер *output*.

Если необходимо указать шаблон для имен больших двоичных объектов с фигурными скобками, используйте две фигурные скобки. Например, если нужно найти BLOB-объекты в контейнере *images* с такими именами:

		{20140101}-soundfile.mp3

используйте следующую команду для своего шаблона:

		images/{{20140101}}-{name}

В этом примере у заполнителя *name* будет значение *soundfile.mp3*.

### Отдельные заполнители для имени большого двоичного объекта и расширения

Следующий пример кода изменяет расширение файла во время копирования BLOB-объектов, которые появляются в контейнере *input*, в контейнер *output*. Код записывает в журнал расширение BLOB-объекта в *input* и задает для объекта в *output* расширение *.txt*.

		public static void CopyBlobToTxtFile([BlobTrigger("input/{name}.{ext}")] TextReader input,
		    [Blob("output/{name}.txt")] out string output,
		    string name,
		    string ext,
		    TextWriter logger)
		{
		    logger.WriteLine("Blob name:" + name);
		    logger.WriteLine("Blob extension:" + ext);
		    output = input.ReadToEnd();
		}

## <a id="types"></a> Типы, которые можно привязать к BLOB-объектам

Атрибут `BlobTrigger` можно использовать со следующими типами:

* `string`
* `TextReader`
* `Stream`
* `ICloudBlob`
* `CloudBlockBlob`
* `CloudPageBlob`
* другие типы, десериализованные с помощью [ICloudBlobStreamBinder](#icbsb) 

При работе непосредственно с учетной записью хранения Azure можно также добавить параметр `CloudStorageAccount` в сигнатуру метода.

## <a id="string"></a> Получение содержимого BLOB-объекта с помощью привязки к строке

Если будут использоваться BLOB-объекты, можно применить `BlobTrigger` к параметру `string`. Следующий пример кода привязывает BLOB-объект к параметру `string` с именем `logMessage`. Функция использует этот параметр для записи содержимого большого двоичного объекта на панель мониторинга пакета SDK для заданий WebJob.
 
		public static void WriteLog([BlobTrigger("input/{name}")] string logMessage,
		    string name, 
		    TextWriter logger)
		{
		     logger.WriteLine("Blob name: {0}", name);
		     logger.WriteLine("Content:");
		     logger.WriteLine(logMessage);
		}

## <a id="icbsb"></a> Получение содержимого сериализованного BLOB-объекта с помощью ICloudBlobStreamBinder

В следующем примере кода используется класс, реализующий `ICloudBlobStreamBinder`, что позволяет атрибуту `BlobTrigger` привязать BLOB-объект к типу `WebImage`.

		public static void WaterMark(
		    [BlobTrigger("images3/{name}")] WebImage input,
		    [Blob("images3-watermarked/{name}")] out WebImage output)
		{
		    output = input.AddTextWatermark("WebJobs SDK", 
		        horizontalAlign: "Center", verticalAlign: "Middle",
		        fontSize: 48, opacity: 50);
		}
		public static void Resize(
		    [BlobTrigger("images3-watermarked/{name}")] WebImage input,
		    [Blob("images3-resized/{name}")] out WebImage output)
		{
		    var width = 180;
		    var height = Convert.ToInt32(input.Height * 180 / input.Width);
		    output = input.Resize(width, height);
		}

Код привязки `WebImage` предусмотрен в классе `WebImageBinder`, который является производным от `ICloudBlobStreamBinder`.

		public class WebImageBinder : ICloudBlobStreamBinder<WebImage>
		{
		    public Task<WebImage> ReadFromStreamAsync(Stream input, 
		        System.Threading.CancellationToken cancellationToken)
		    {
		        return Task.FromResult<WebImage>(new WebImage(input));
		    }
		    public Task WriteToStreamAsync(WebImage value, Stream output,
		        System.Threading.CancellationToken cancellationToken)
		    {
		        var bytes = value.GetBytes();
		        return output.WriteAsync(bytes, 0, bytes.Length, cancellationToken);
		    }
		}

## <a id="poison"></a> Способ обработки подозрительных BLOB-объектов

При сбое функции `BlobTrigger` пакет SDK вызывает ее снова, если сбой вызван временной ошибкой. Если сбой вызван содержимым большого двоичного объекта, каждый раз при попытке обработать большой двоичный объект будет происходить сбой функции. По умолчанию пакет SDK вызывает функцию до 5 раз для заданного большого двоичного объекта. Если при пятой попытке происходит сбой, пакет SDK добавляет сообщение в очередь с именем *webjobs-blobtrigger-poison*.

Можно настроить максимальное количество попыток. Тот же параметр [MaxDequeueCount](websites-dotnet-webjobs-sdk-storage-queues-how-to.md#configqueue) используется для обработки подозрительных BLOB-объектов и подозрительных сообщений очереди.

Сообщением очереди для подозрительных больших двоичных объектов является объект JSON, содержащий следующие свойства:

* FunctionId (в формате *{WebJob name}*. Functions.* {Function name}*, например: WebJob1.Functions.CopyBlob)
* BlobType (BlockBlob или PageBlob);
* ContainerName;
* BlobName
* ETag (идентификатор версии BLOB-объектов, например 0x8D1DC6E70A277EF)

В следующем примере кода функция `CopyBlob` содержит код, который приводит к сбою при каждом ее вызове. После того, как пакет SDK вызывает функцию максимальное количество раз, в очереди подозрительных BLOB-объектов создается сообщение, которое обрабатывается функцией `LogPoisonBlob`.

		public static void CopyBlob([BlobTrigger("input/{name}")] TextReader input,
		    [Blob("textblobs/output-{name}")] out string output)
		{
		    throw new Exception("Exception for testing poison blob handling");
		    output = input.ReadToEnd();
		}
		
		public static void LogPoisonBlob(
		[QueueTrigger("webjobs-blobtrigger-poison")] PoisonBlobMessage message,
		    TextWriter logger)
		{
		    logger.WriteLine("FunctionId: {0}", message.FunctionId);
		    logger.WriteLine("BlobType: {0}", message.BlobType);
		    logger.WriteLine("ContainerName: {0}", message.ContainerName);
		    logger.WriteLine("BlobName: {0}", message.BlobName);
		    logger.WriteLine("ETag: {0}", message.ETag);
		}

Пакет SDK автоматически выполняет десериализацию сообщения JSON. Так выглядит класс `PoisonBlobMessage`:

		public class PoisonBlobMessage
		{
		    public string FunctionId { get; set; }
		    public string BlobType { get; set; }
		    public string ContainerName { get; set; }
		    public string BlobName { get; set; }
		    public string ETag { get; set; }
		}

### <a id="polling"></a> Алгоритм опроса BLOB-объектов

Пакет SDK для веб-заданий проверяет все контейнеры, задаваемые атрибутами `BlobTrigger` при запуске приложения. Такая проверка может занять некоторое время в большой учетной записи хранения. Поэтому для поиска новых BLOB-объектов и выполнения функций `BlobTrigger` может понадобиться много времени.

Чтобы обнаружить новые или измененные большие двоичные объекты после запуска приложения, пакет SDK периодически проверяет записи в журналах хранилища больших двоичных объектов. Журналы BLOB-объектов помещаются в буфер, а физическая запись происходит каждые 10 минут или около того. Поэтому после создания или обновления BLOB-объекта соответствующая функция `BlobTrigger` может выполняться со значительной задержкой.

Существует одно исключение для BLOB-объектов, создаваемых с помощью атрибута `Blob`. Когда пакет SDK для веб-заданий создает новый BLOB-объект, он немедленно передает его любой соответствующей функции `BlobTrigger`. Поэтому при объединении входящих и исходящих больших двоичных объектов в цепочку пакет SDK может их эффективно обрабатывать. Но если при выполнении функций обработки BLOB-объектов, созданных или обновленных другими способами, нужны малые задержки, советуем использовать `QueueTrigger` вместо `BlobTrigger`.

### <a id="receipts"></a> Уведомления о получении BLOB-объекта

Пакет SDK для веб-заданий гарантирует, что для одного и того же нового или обновленного BLOB-объекта функция `BlobTrigger` будет вызываться только один раз. Это достигается за счет сохранения *уведомлений о получении *, которые позволяют определить, обработана ли версия этого BLOB-объекта.

Уведомления о получении BLOB-объекта хранятся в контейнере с именем *azure-webjobs-hosts* в учетной записи хранения Azure, указанной в строке подключения AzureWebJobsStorage. Уведомление о получении большого двоичного объекта содержит следующую информацию:

* Функция, вызванная для BLOB-объекта (*{WebJob name}*.Functions.*{Function name}*, например WebJob1.Functions.CopyBlob)
* имя контейнера;
* тип большого двоичного объекта (BlockBlob или PageBlob);
* имя большого двоичного объекта;
* ETag (идентификатор версии BLOB-объектов, например 0x8D1DC6E70A277EF)

Если вам необходимо выполнить принудительную повторную обработку BLOB-объекта, уведомление о его получении можно удалить из контейнера *azure-webjobs-hosts* вручную.

## <a id="queues"></a>Связанные разделы, которые описаны в статье об очередях

Дополнительную информацию об обработке BLOB-объектов, которая инициируется сообщением очереди, а также несвязанные с обработкой BLOB-объектов сценарии для пакета SDK для веб-заданий см. в статье [Использование пакета SDK для веб-заданий для работы с хранилищем очередей](websites-dotnet-webjobs-sdk-storage-queues-how-to.md).

В этой статье рассматриваются следующие связанные разделы:

* Асинхронные функции
* Выполнение на нескольких экземплярах
* Корректное завершение работы
* Использование атрибутов пакета SDK для заданий WebJob очереди в теле функции
* Установка строк подключения пакета SDK в коде.
* Установка значений параметров конструктора пакета SDK для заданий WebJob в коде
* Настройка `MaxDequeueCount` для обработки подозрительных BLOB-объектов
* Вызов функции вручную
* Запись журналов

## <a id="nextsteps"></a>Дальнейшие действия

В этом руководстве предоставлены примеры кода обработки обычных сценариев для работы с большими двоичными объектами Azure. Дополнительные сведения об использовании веб-заданий Azure и пакета SDK для веб-заданий см. в статье [Веб-задания Azure — рекомендуемые ресурсы](http://go.microsoft.com/fwlink/?linkid=390226).
 

<!---HONumber=62-->
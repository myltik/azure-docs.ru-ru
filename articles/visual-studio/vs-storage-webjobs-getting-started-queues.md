---
title: Приступая к работе с хранилищем очередей и подключенными службами Visual Studio (проекты веб-заданий) | Документация Майкрософт
description: Как приступить к работе, используя хранилище очередей Azure в проекте веб-задания после подключения к учетной записи хранения с использованием подключенных служб Visual Studio.
services: storage
author: ghogen
manager: douge
ms.assetid: 5c3ef267-2a67-44e9-ab4a-1edd7015034f
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.workload: azure
ms.topic: article
ms.date: 12/02/2016
ms.author: ghogen
ms.openlocfilehash: 332d682147ba832f631052d8348039f74b46c438
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2018
ms.locfileid: "31798575"
---
# <a name="getting-started-with-azure-queue-storage-and-visual-studio-connected-services-webjob-projects"></a>Приступая к работе с подключенными службами хранилища очередей Azure и Visual Studio (проекты веб-заданий)
[!INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Обзор
В этой статье описывается, как начать использовать хранилище очередей Azure в проекте веб-заданий Azure в Visual Studio после создания учетной записи хранения Azure или указания ссылки на нее с помощью диалогового окна **Добавление подключенных служб** в Visual Studio. Когда вы добавляете учетную запись хранения в проект веб-задания с помощью диалогового окна **Добавление подключенных служб** в Visual Studio, устанавливается соответствующий пакет NuGet службы хранилища Azure. Также в проект добавляются соответствующие ссылки .NET, а в файле App.config обновляются строки подключения для учетной записи хранения.  

Эта статья содержит примеры кода C#, в которых показано, как использовать пакет SDK для веб-заданий Azure версии 1.x со службой хранилища очередей Azure.

Хранилище очередей Azure — это служба для хранения большого количества сообщений, к которым можно получить доступ практически из любой точки мира с помощью вызовов с проверкой подлинности по протоколам HTTP или HTTPS. Одно сообщение очереди может быть размером до 64 КБ, а очередь может содержать миллионы сообщений до общего ограничения емкости учетной записи хранения. Дополнительные сведения см. в разделе [Приступая к работе с хранилищем очередей Azure с помощью .NET](../storage/queues/storage-dotnet-how-to-use-queues.md). Дополнительные сведения см. на сайте [ASP.NET](http://www.asp.net).

## <a name="how-to-trigger-a-function-when-a-queue-message-is-received"></a>Вызов функции при получении сообщения очереди
Чтобы написать функцию, которую пакет SDK для веб-заданий будет вызывать при получении сообщения очереди, используйте атрибут **QueueTrigger** . Конструктор атрибута принимает строковый параметр, который указывает имя очереди для опроса. Чтобы узнать, как динамически задать имя очереди, ознакомьтесь с разделом [Установка параметров конфигурации](#how-to-set-configuration-options).

### <a name="string-queue-messages"></a>Строковые сообщения очереди
В следующем примере очередь содержит строковое сообщение, поэтому атрибут **QueueTrigger** применяется к строковому параметру с именем **logMessage**, в котором находится содержимое сообщения очереди. Функция [записывает сообщение журнала на панель мониторинга](#how-to-write-logs).

        public static void ProcessQueueMessage([QueueTrigger("logqueue")] string logMessage, TextWriter logger)
        {
            logger.WriteLine(logMessage);
        }

Помимо значения **string** возможными значениями этого параметра являются массив байтов, объект **CloudQueueMessage** или заданный вами объект POCO.

### <a name="poco-plain-old-clr-objecthttpenwikipediaorgwikiplainoldclrobject-queue-messages"></a>Сообщения очереди [POCO](http://en.wikipedia.org/wiki/Plain_Old_CLR_Object)
В следующем примере сообщения очереди содержат JSON для объекта **BlobInformation**, который имеет свойство **BlobName**. Пакет SDK автоматически выполняет десериализацию объекта.

        public static void WriteLogPOCO([QueueTrigger("logqueue")] BlobInformation blobInfo, TextWriter logger)
        {
            logger.WriteLine("Queue message refers to blob: " + blobInfo.BlobName);
        }

Для сериализации и десериализации сообщений в пакете SDK используется [пакет NuGet Newtonsoft.Json](http://www.nuget.org/packages/Newtonsoft.Json) . В случае создания сообщений очереди с помощью программы, которая не использует пакет SDK для заданий WebJob, чтобы создать сообщение очереди POCO, которое сможет проанализировать такой пакет, вы можете написать код по следующему образцу.

        BlobInformation blobInfo = new BlobInformation() { BlobName = "log.txt" };
        var queueMessage = new CloudQueueMessage(JsonConvert.SerializeObject(blobInfo));
        logQueue.AddMessage(queueMessage);

### <a name="async-functions"></a>Асинхронные функции
Следующая асинхронная функция [записывает журнал на панель мониторинга](#how-to-write-logs).

        public async static Task ProcessQueueMessageAsync([QueueTrigger("logqueue")] string logMessage, TextWriter logger)
        {
            await logger.WriteLineAsync(logMessage);
        }

Асинхронные функции могут принимать [маркер отмены](http://www.asp.net/mvc/overview/performance/using-asynchronous-methods-in-aspnet-mvc-4#CancelToken), как показано в следующем примере, который копирует большой двоичный объект. (Описание заполнителя **queueTrigger** см. в статье [Большие двоичные объекты](#how-to-read-and-write-blobs-and-tables-while-processing-a-queue-message).)

        public async static Task ProcessQueueMessageAsyncCancellationToken(
            [QueueTrigger("blobcopyqueue")] string blobName,
            [Blob("textblobs/{queueTrigger}",FileAccess.Read)] Stream blobInput,
            [Blob("textblobs/{queueTrigger}-new",FileAccess.Write)] Stream blobOutput,
            CancellationToken token)
        {
            await blobInput.CopyToAsync(blobOutput, 4096, token);
        }

## <a name="types-the-queuetrigger-attribute-works-with"></a>Типы, с которыми используется атрибут QueueTrigger
Атрибут **QueueTrigger** можно использовать со следующими типами:

* **string**
* тип POCO, сериализованный как JSON;
* **byte[]**
* **CloudQueueMessage**

## <a name="polling-algorithm"></a>Алгоритм опроса
В пакете SDK реализован алгоритм случайной экспоненциальной отсрочки, который позволяет уменьшить влияние опроса очереди ожидающих задач на затраты на транзакции хранилища.  При обнаружении сообщения пакет SDK ожидает в течение двух секунд и затем проверяет, не поступило ли еще одно сообщение; если сообщение не найдено, он ожидает около четырех секунд перед повторной попыткой. После последующих неудачных попыток получения сообщения очереди время ожидания продолжает увеличиваться, пока не достигнет максимального времени ожидания, по умолчанию — одна минута. [Можно настроить максимальное время ожидания](#how-to-set-configuration-options).

## <a name="multiple-instances"></a>Выполнение на нескольких экземплярах
Если ваше веб-приложение работает на нескольких экземплярах, веб-задания непрерывно выполняются на каждой машине, и каждая машина будет ожидать триггеры и пытаться запустить функции. В некоторых сценариях это может привести к тому, что некоторые функции обработают часть данных дважды, поэтому функции должны быть идемпотентными (написанными так, чтобы их постоянный вызов с одинаковыми входными данными не создавал дублирующие результаты).  

## <a name="parallel-execution"></a>Параллельное выполнение
Если имеется несколько функций, которые прослушивают различные очереди, при одновременном получении сообщений пакет SDK будет вызывать их параллельно.

То же самое происходит при получении нескольких сообщений для одной очереди. По умолчанию SDK одновременно получает пакет из 16 сообщений очереди и выполняет функцию, которая обрабатывает их параллельно. [Можно настроить размер пакета](#how-to-set-configuration-options). Когда число обрабатываемых сообщений достигает половины размера пакета, SDK запрашивает следующий пакет и начинает обработку содержащихся в нем сообщений. Поэтому максимальное количество сообщений, одновременно обрабатываемых каждой функцией, в полтора раза больше размера пакета. Это ограничение применяется отдельно к каждой функции с атрибутом **QueueTrigger** . Если вы не хотите, чтобы сообщения из одной очереди обрабатывались параллельно, установите размер пакета равный 1.

## <a name="get-queue-or-queue-message-metadata"></a>Получение очереди или метаданных очереди сообщений
Следующие свойства сообщений можно получить путем добавления параметров к сигнатуре метода:

* **DateTimeOffset** expirationTime
* **DateTimeOffset** insertionTime
* **DateTimeOffset** nextVisibleTime
* **string** queueTrigger (содержит текст сообщения)
* **string** id
* **string** popReceipt
* **int** dequeueCount

Если требуется работать непосредственно с API хранилища Azure, можно также добавить параметр **CloudStorageAccount** .

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

## <a name="graceful-shutdown"></a>Корректное завершение работы
Функция, которая запускается в постоянном веб-задании, может принимать параметр **CancellationToken** , который позволяет операционной системе уведомлять ее о том, что выполнение веб-задания будет завершено. Это уведомление можно использовать для предотвращения ситуации, когда выполнение функции завершается неожиданно, оставляя данные в несогласованном состоянии.

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

## <a name="how-to-create-a-queue-message-while-processing-a-queue-message"></a>Создание сообщения очереди во время обработки сообщения очереди
Чтобы написать функцию, которая создает новое сообщение очереди, используйте атрибут **Queue** . Как и в случае с **QueueTrigger**, имя очереди можно передать в виде строки или [задать динамически](#how-to-set-configuration-options).

### <a name="string-queue-messages"></a>Строковые сообщения очереди
Следующий пример неасинхронного кода создает новое сообщение очереди в очереди с именем «outputqueue» с тем же содержимым, что и сообщение очереди, поступившее в очередь с именем «inputqueue». (Для асинхронных функций используйте параметр **IAsyncCollector;<T>** , следуя указаниям далее в этом разделе.)

        public static void CreateQueueMessage(
            [QueueTrigger("inputqueue")] string queueMessage,
            [Queue("outputqueue")] out string outputQueueMessage )
        {
            outputQueueMessage = queueMessage;
        }

### <a name="poco-plain-old-clr-objecthttpenwikipediaorgwikiplainoldclrobject-queue-messages"></a>Сообщения очереди [POCO](http://en.wikipedia.org/wiki/Plain_Old_CLR_Object)
Чтобы создать сообщение очереди, содержащее объект POCO, а не строку, передайте тип POCO в качестве выходного параметра конструктору атрибута **Queue** .

        public static void CreateQueueMessage(
            [QueueTrigger("inputqueue")] BlobInformation blobInfoInput,
            [Queue("outputqueue")] out BlobInformation blobInfoOutput )
        {
            blobInfoOutput = blobInfoInput;
        }

Пакет SDK автоматически выполняет сериализацию объекта в формат JSON. Сообщение очереди создается всегда, даже если объект имеет значение null.

### <a name="create-multiple-messages-or-in-async-functions"></a>Создание нескольких сообщений или сообщений в асинхронных функциях
Чтобы создать несколько сообщений, установите для очереди вывода тип параметра **ICollector<T>** или **IAsyncCollector<T>**, как показано в следующем примере.

        public static void CreateQueueMessages(
            [QueueTrigger("inputqueue")] string queueMessage,
            [Queue("outputqueue")] ICollector<string> outputQueueMessage,
            TextWriter logger)
        {
            logger.WriteLine("Creating 2 messages in outputqueue");
            outputQueueMessage.Add(queueMessage + "1");
            outputQueueMessage.Add(queueMessage + "2");
        }

Каждое сообщение очереди создается сразу после вызова метода **Add** .

### <a name="types-that-the-queue-attribute-works-with"></a>Типы, с которыми используется атрибут Queue
Атрибут **Queue** можно использовать со следующими типами параметров:

* **out string** (создает сообщение очереди, если по завершении вызова функции значение параметра не равно null);
* **out byte[]** (работает как параметр **string**);
* **out CloudQueueMessage** (работает как параметр **string**);
* **out POCO** (сериализуемый тип, создает сообщение с пустым объектом, если по завершении функции значение параметра — null);
* **ICollector;**
* **IAsyncCollector;**
* **CloudQueue** (позволяет создавать сообщения вручную непосредственно с помощью API службы хранилища Azure).

### <a name="use-webjobs-sdk-attributes-in-the-body-of-a-function"></a>Использование атрибутов пакета SDK для заданий WebJob очереди в теле функции
Если перед использованием атрибута пакета SDK для веб-заданий, например **Queue**, **Blob** или **Table**, необходимо выполнить какие-либо действия с функцией, то можно использовать интерфейс **IBinder**.

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

Интерфейс **IBinder** можно также использовать при работе с атрибутами **Table** и **Blob**.

## <a name="how-to-read-and-write-blobs-and-tables-while-processing-a-queue-message"></a>Чтение и запись больших двоичных объектов и таблиц во время обработки сообщения очереди
Атрибуты **Blob** и **Table** позволяют осуществлять чтение и запись больших двоичных объектов и таблиц. Примеры в этом разделе применимы к BLOB-объектам. Примеры кода, в которых показаны способы запуска процессов при создании или обновлении больших двоичных объектов, см. в статье об [использовании хранилища больших двоичных объектов Azure с пакетом SDK веб-заданий](https://github.com/Azure/azure-webjobs-sdk/wiki).
<!-- , and for code samples that read and write tables, see [How to use Azure table storage with the WebJobs SDK](../app-service-web/websites-dotnet-webjobs-sdk-storage-tables-how-to.md). -->

### <a name="string-queue-messages-triggering-blob-operations"></a>Сообщения очереди строк, которые запускают операции с большими двоичными объектами
Для сообщения очереди, содержащего строку, **queueTrigger** является заполнителем, который можно использовать в параметре **blobPath** атрибута **Blob**, в котором находится содержимое сообщения.

Следующий пример использует объекты **Stream** для чтения и записи больших двоичных объектов. Сообщение очереди — это имя большого двоичного объекта, размещенного в контейнере textblobs. Копия большого двоичного объекта с приставкой «-new», добавленной к имени, создается в том же контейнере.

        public static void ProcessQueueMessage(
            [QueueTrigger("blobcopyqueue")] string blobName,
            [Blob("textblobs/{queueTrigger}",FileAccess.Read)] Stream blobInput,
            [Blob("textblobs/{queueTrigger}-new",FileAccess.Write)] Stream blobOutput)
        {
            blobInput.CopyTo(blobOutput, 4096);
        }

Конструктор атрибута **Blob** учитывает параметр **blobPath**, который указывает контейнер и имя большого двоичного объекта. Дополнительную информацию об этом заполнителе см. в статье [Как использовать хранилище больших двоичных объектов Azure с пакетом SDK для WebJob](https://github.com/Azure/azure-webjobs-sdk/wiki).

Если атрибут декорирует объект **Stream**, другой параметр конструктора задает режим **FileAccess** как режим чтения, записи или чтения и записи.

В следующем примере для удаления большого двоичного объекта используется объект **CloudBlockBlob** . Очередь сообщений — это имя большого двоичного объекта.

        public static void DeleteBlob(
            [QueueTrigger("deleteblobqueue")] string blobName,
            [Blob("textblobs/{queueTrigger}")] CloudBlockBlob blobToDelete)
        {
            blobToDelete.Delete();
        }

### <a name="poco-plain-old-clr-objecthttpenwikipediaorgwikiplainoldclrobject-queue-messages"></a>Сообщения очереди [POCO](http://en.wikipedia.org/wiki/Plain_Old_CLR_Object)
Для объектов POCO, сохраненных в формате JSON в сообщении очереди, можно использовать заполнители, которые называют свойства объектов в параметре **blobPath** атрибута **Queue**. Можно также использовать имена свойства метаданных очереди в качестве заполнителей. Ознакомьтесь с разделом [Получение метаданных очереди или сообщения в очереди](#get-queue-or-queue-message-metadata).

В следующем примере выполняется копирование большого двоичного объекта в новый большой двоичный объект с другим расширением. Сообщение очереди представляет собой объект **BlobInformation** со свойствами **BlobName** и **BlobNameWithoutExtension**. В качестве заполнителей в пути к большому двоичному объекту используются имена свойств для атрибутов **Blob** .

        public static void CopyBlobPOCO(
            [QueueTrigger("copyblobqueue")] BlobInformation blobInfo,
            [Blob("textblobs/{BlobName}", FileAccess.Read)] Stream blobInput,
            [Blob("textblobs/{BlobNameWithoutExtension}.txt", FileAccess.Write)] Stream blobOutput)
        {
            blobInput.CopyTo(blobOutput, 4096);
        }

Для сериализации и десериализации сообщений в пакете SDK используется [пакет NuGet Newtonsoft.Json](http://www.nuget.org/packages/Newtonsoft.Json) . В случае создания сообщений очереди с помощью программы, которая не использует пакет SDK для заданий WebJob, чтобы создать сообщение очереди POCO, которое сможет проанализировать такой пакет, вы можете написать код по следующему образцу.

        BlobInformation blobInfo = new BlobInformation() { BlobName = "boot.log", BlobNameWithoutExtension = "boot" };
        var queueMessage = new CloudQueueMessage(JsonConvert.SerializeObject(blobInfo));
        logQueue.AddMessage(queueMessage);

Если необходимо выполнить некоторую работу в функции перед привязкой большого двоичного объекта к объекту, можно использовать атрибут в основном тексте функции, как показано в разделе [Использование атрибутов пакета SDK для заданий WebJob очереди в теле функции](#use-webjobs-sdk-attributes-in-the-body-of-a-function).

### <a name="types-you-can-use-the-blob-attribute-with"></a>Типы, которые можно использовать с атрибутом Blob
Атрибут **Blob** можно использовать со следующими типами:

* **Stream** (чтение или запись, указанные с помощью параметра конструктора FileAccess);
* **TextReader;**
* **TextWriter**
* **string** (чтение);
* **out string** (запись; создает большой двоичный объект, только если для параметра строки не задано значение null при возврате функции);
* POCO (чтение);
* out POCO (запись; всегда создает пустой большой двоичный объект, если при возврате функции значение параметра POCO — null);
* **CloudBlobStream** (запись);
* **ICloudBlob** (чтение или запись);
* **CloudBlockBlob** (чтение или запись);
* **CloudPageBlob** (чтение или запись).

## <a name="how-to-handle-poison-messages"></a>Способ обработки подозрительных сообщений
Сообщения, содержимое которых вызывает сбой функции, называются *подозрительными сообщениями*. При сбое функции сообщение очереди не удаляется и в конечном итоге забирается снова, вызывая повтор цикла. Пакет SDK может автоматически прервать цикл после ограниченного числа итераций или это можно сделать вручную.

### <a name="automatic-poison-message-handling"></a>Автоматическая обработка сообщений
Пакет SDK вызывает функцию обработки сообщения очереди до 5 раз. В случае сбоя во время пятой попытки сообщение перемещается в очередь подозрительных сообщений. Как настроить максимальное число повторных попыток описывается в разделе [Установка параметров конфигурации](#how-to-set-configuration-options).

Очередь подозрительных сообщений называется *{originalqueuename}*-poison. Можно написать функции для обработки сообщений из очереди подозрительных сообщений путем внесения их в журнал или отправки уведомления о необходимости ручного вмешательства.

В следующем примере функция **CopyBlob** завершится ошибкой, если сообщение очереди содержит имя несуществующего большого двоичного объекта. В таком случае сообщение перемещается из очереди copyblobqueue в очередь copyblobqueue-poison. Затем функция **ProcessPoisonMessage** записывает подозрительное сообщение в журнал.

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

![Вывод консоли для обработки подозрительных сообщений](./media/vs-storage-webjobs-getting-started-queues/poison.png)

### <a name="manual-poison-message-handling"></a>Ручная обработка подозрительных сообщений
Чтобы узнать, сколько попыток обработки сообщения было совершено, добавьте в функцию параметр **int** с именем **dequeueCount**. Затем можно проверить счетчик вывода из очереди в коде функции и выполнить собственную обработку подозрительных сообщений, если это число превышает пороговое значение, как показано в следующем примере.

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

## <a name="how-to-set-configuration-options"></a>Установка параметров конфигурации
Чтобы задать следующие параметры конфигурации, можно использовать тип **JobHostConfiguration** :

* Установка строк подключения пакета SDK в коде.
* Настройка таких параметров атрибута **QueueTrigger** , как максимальное значение счетчика вывода из очереди.
* Получение имен очередей из конфигурации

### <a name="set-sdk-connection-strings-in-code"></a>Установка строк подключения пакета SDK в коде
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

### <a name="configure-queuetrigger--settings"></a>Настройка параметров атрибута QueueTrigger
Можно настроить следующие параметры, которые применяются для обработки сообщений очереди:

* Максимальное количество сообщений очереди, которые забираются одновременно для параллельной обработки (по умолчанию — 16).
* Максимальное число повторных попыток перед отправкой сообщения очереди в очередь подозрительных сообщений (по умолчанию — 5).
* Максимальное время ожидания перед повторным опросом, когда очередь пуста (по умолчанию — 1 минута).

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

### <a name="set-values-for-webjobs-sdk-constructor-parameters-in-code"></a>Установка значений параметров конструктора пакета SDK для заданий WebJob в коде
Иногда требуется указать в коде имя очереди, имя большого двоичного объекта, контейнера или таблицы, а не жестко задавать их. Например, в некоторых случаях в файле конфигурации или в переменной среды необходимо указывать имя очереди для **QueueTrigger** .

Вы можете сделать это, передав объект **NameResolver** типу **JobHostConfiguration**. В параметрах конструктора атрибута пакета SDK для веб-заданий нужно указать специальные заполнители со знаками процента (%) с обеих сторон, тогда код **NameResolver** будет указывать фактические значения, которые следует использовать вместо этих заполнителей.

Например, предположим, что вы хотите использовать очередь с именем logqueuetest в тестовой среде и очередь с именем logqueueprod в производственной среде. Вместо фиксированного имени очереди требуется указать имя элемента в коллекции **appSettings** с фактическим именем очереди. Если logqueue — значение ключа **appSettings** , функция может выглядеть как в следующем примере.

        public static void WriteLog([QueueTrigger("%logqueue%")] string logMessage)
        {
            Console.WriteLine(logMessage);
        }

Затем класс **NameResolver** может получить имя очереди из **appSettings**, как показано в следующем примере.

        public class QueueNameResolver : INameResolver
        {
            public string Resolve(string name)
            {
                return ConfigurationManager.AppSettings[name].ToString();
            }
        }

Необходимо передать класс **NameResolver** в объект **JobHost**, как показано в следующем примере.

        static void Main(string[] args)
        {
            JobHostConfiguration config = new JobHostConfiguration();
            config.NameResolver = new QueueNameResolver();
            JobHost host = new JobHost(config);
            host.RunAndBlock();
        }

**Примечание.** Имена очередей, таблиц и больших двоичных объектов не разрешаются при каждом вызове функции, а имена контейнеров больших двоичных объектов разрешаются только при запуске приложения. Во время выполнения задания нельзя изменить имя контейнера больших двоичных объектов.

## <a name="how-to-trigger-a-function-manually"></a>Вызов функции вручную
Чтобы вызвать функцию вручную, используйте метод **Call** или **CallAsync** объекта **JobHost** и атрибут функции **NoAutomaticTrigger**, как показано в следующем примере.

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

## <a name="how-to-write-logs"></a>Запись журналов
На панели мониторинга отображаются журналы: один на странице для заданий WebJob, а другой — на отдельной странице для определенного вызова задания WebJob.

![Журналы на странице задания WebJob](./media/vs-storage-webjobs-getting-started-queues/dashboardapplogs.png)

![Журналы на странице вызова функций](./media/vs-storage-webjobs-getting-started-queues/dashboardlogs.png)

Выходные данные методов консоли, которые вызываются с помощью функции или метода **Main()** , отображаются на панели мониторинга на странице веб-задания, а не на странице для вызова определенного метода. Выходные данные объекта TextWriter, полученного из параметра в сигнатуре метода, отображаются на панели мониторинга на странице для вызова метода.

Выходные данные консоли нельзя связать с вызовом определенного метода, поскольку у консоли есть только один поток, хотя одновременно может выполняться сразу несколько функций. Поэтому пакет SDK обеспечивает вызов каждой функции с помощью уникального объекта модуля записи в журнал.

Чтобы внести запись в [журналы трассировки приложений](../app-service/web-sites-dotnet-troubleshoot-visual-studio.md#logsoverview), используйте **Console.Out** (создает журналы с пометкой INFO) и **Console.Error** (создает журналы с пометкой ERROR). Кроме того, можно использовать [трассировку или TraceSource](http://blogs.msdn.com/b/mcsuksoldev/archive/2014/09/04/adding-trace-to-azure-web-sites-and-web-jobs.aspx), благодаря которым помимо информации и данных об ошибках можно получать подробные данные, предупреждения и оповещения о критическом уровне. Журналы трассировки приложений отображаются в файлах журналов веб-приложения, таблицах Azure или больших двоичных объектах Azure, в зависимости от настроек вашего веб-приложения Azure. Что касается всех выходных данных консоли, журналы последних 100 приложений отображаются на странице панели мониторинга для заданий WebJob, а не на странице для вызова функции.

Выходные данные консоли отображаются на панели мониторинга, только если программа запущена в задании Azure WebJob, а не локально или в другой среде.

Ведение журналов можно отключить с помощью элемента задание значения NULL для строки подключения панели мониторинга. Дополнительную информацию см. в разделе [Установка параметров конфигурации](#how-to-set-configuration-options).

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

Выходные данные объекта **TextWriter** отобразятся на панели мониторинга пакета SDK для веб-заданий, если перейти на страницу вызова определенной функции и щелкнуть **Переключить выходные данные**.

![Ссылка для вызова](./media/vs-storage-webjobs-getting-started-queues/dashboardinvocations.png)

![Журналы на странице вызова функций](./media/vs-storage-webjobs-getting-started-queues/dashboardlogs.png)

Последние 100 строк выходных данных консоли отобразятся на панели мониторинга пакета SDK веб-заданий, если перейти на страницу для веб-задания (не для вызова функции) и щелкнуть **Переключить выходные данные**.

![Переключить выходные данные](./media/vs-storage-webjobs-getting-started-queues/dashboardapplogs.png)

Журналы приложений для непрерывных веб-заданий находятся по пути data/jobs/continuous/*{имя_веб-задания}*/job_log.txt в файловой системе веб-приложения.

        [09/26/2014 21:01:13 > 491e54: INFO] Console.Write - Hello world!
        [09/26/2014 21:01:13 > 491e54: ERR ] Console.Error - Hello world!
        [09/26/2014 21:01:13 > 491e54: INFO] Console.Out - Hello world!

Вот как выглядят журналы приложений в большом двоичном объекте Azure: 2014-09-26T21:01:13,Information,contosoadsnew,491e54,635473620738373502,0,17404,17,Console.Write - Hello world!, 2014-09-26T21:01:13,Error,contosoadsnew,491e54,635473620738373502,0,17404,19,Console.Error - Hello world!, 2014-09-26T21:01:13,Information,contosoadsnew,491e54,635473620738529920,0,17404,17,Console.Out - Hello world!,

В таблице Azure журналы **Console.Out** и **Console.Error** выглядят следующим образом.

![Журнал Info в таблице](./media/vs-storage-webjobs-getting-started-queues/tableinfo.png)

![Журнал Error в таблице](./media/vs-storage-webjobs-getting-started-queues/tableerror.png)

## <a name="next-steps"></a>Дополнительная информация
В этой статье предоставлены примеры кода обработки обычных сценариев для работы с очередями Azure. Дополнительная информация об использовании веб-заданий Azure и пакета SDK для веб-заданий доступна в [ресурсах с документацией по веб-заданиям Azure](http://go.microsoft.com/fwlink/?linkid=390226).


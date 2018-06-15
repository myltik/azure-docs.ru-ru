---
title: Приступая к работе с хранилищем BLOB-объектов Azure и подключенными службами Visual Studio (проекты веб-заданий) | Документация Майкрософт
description: Как приступить к работе, используя хранилище больших двоичных объектов в проекте веб-задания после подключения к хранилищу Azure с использованием подключенных служб Visual Studio.
services: storage
author: ghogen
manager: douge
ms.assetid: 324c9376-0225-4092-9825-5d1bd5550058
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.workload: azure
ms.topic: conceptual
ms.date: 12/02/2016
ms.author: ghogen
ms.openlocfilehash: 4800d6ad0482462b9eccb72de34de56e4f7c370c
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2018
ms.locfileid: "31796998"
---
# <a name="get-started-with-azure-blob-storage-and-visual-studio-connected-services-webjob-projects"></a>Начало работы с подключенными службами хранилища больших двоичных объектов Azure и Visual Studio (проекты веб-заданий)
[!INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

## <a name="overview"></a>Обзор
В этой статье приведены примеры кода на C# для запуска процессов при создании или обновлении большого двоичного объекта Azure. В примерах кода используется [пакет SDK для веб-заданий](https://github.com/Azure/azure-webjobs-sdk/wiki) версии 1.x. Когда вы добавляете учетную запись хранения в проект веб-задания с помощью диалогового окна **Добавление подключенных служб** в Visual Studio, устанавливается соответствующий пакет NuGet службы хранилища Azure. Также в проект добавляются соответствующие ссылки .NET, а в файле App.config обновляются строки подключения для учетной записи хранения.

## <a name="how-to-trigger-a-function-when-a-blob-is-created-or-updated"></a>Вызов функции при создании или обновлении большого двоичного объекта
В этом разделе показано, как использовать атрибут **BlobTrigger** .

 **Примечание**. Пакет SDK для веб-заданий проверяет файлы журнала и отслеживает создание и изменение больших двоичных объектов. Это медленный процесс. После создания большого двоичного объекта функция может не вызываться несколько минут или даже дольше.  Если вашему приложению нужно обработать большой двоичный объект немедленно, советуем во время его создания создать сообщение очереди и использовать атрибут **QueueTrigger** вместо атрибута **BlobTrigger** в функции, которая обрабатывает большой двоичный объект.

### <a name="single-placeholder-for-blob-name-with-extension"></a>Один заполнитель для имени большого двоичного объекта и расширения
Следующий пример кода копирует текстовые большие двоичные объекты, которые появляются в контейнере *input*, в контейнер *output*.

        public static void CopyBlob([BlobTrigger("input/{name}")] TextReader input,
            [Blob("output/{name}")] out string output)
        {
            output = input.ReadToEnd();
        }

Конструктор атрибута принимает параметр строки, который указывает имя контейнера и заполнитель для имени большого двоичного объекта. В этом примере при создании большого двоичного объекта с именем *Blob1.txt* в контейнере *input* функция создает большой двоичный объект *Blob1.txt* в контейнере *output*.

Можно указать шаблон имени с заполнителем имени большого двоичного объекта, как показано в следующем примере кода:

        public static void CopyBlob([BlobTrigger("input/original-{name}")] TextReader input,
            [Blob("output/copy-{name}")] out string output)
        {
            output = input.ReadToEnd();
        }

Этот код копируется только большие двоичные объекты, имена которых начинаются с original-. Например, *original-Blob1.txt* в контейнере *input* копируется в *copy-Blob1.txt* в контейнере *output*.

Если необходимо указать шаблон для имен больших двоичных объектов с фигурными скобками, используйте две фигурные скобки. Например, если нужно найти большие двоичные объекты в контейнере *images* с такими именами:

        {20140101}-soundfile.mp3

используйте следующую команду для своего шаблона:

        images/{{20140101}}-{name}

В этом примере у заполнителя *name* будет значение *soundfile.mp3*.

### <a name="separate-blob-name-and-extension-placeholders"></a>Отдельные заполнители для имени большого двоичного объекта и расширения
Следующий пример кода изменяет расширение файла во время копирования больших двоичных объектов, которые появляются в контейнере *input*, в контейнер *output*. Код записывает в журнал расширение большого двоичного объекта в *input* и задает для большого двоичного объекта в *output* расширение *TXT*.

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

## <a name="types-that-you-can-bind-to-blobs"></a>Типы, которые можно привязать к большим двоичным объектам
Атрибут **BlobTrigger** можно использовать со следующими типами:

* **string**
* **TextReader;**
* **Поток**
* **ICloudBlob;**
* **CloudBlockBlob**
* **CloudPageBlob.**
* другие типы, десериализованные с помощью [ICloudBlobStreamBinder](#getting-serialized-blob-content-by-using-icloudblobstreambinder)

При работе непосредственно с учетной записью хранения Azure можно также добавить параметр **CloudStorageAccount** в сигнатуру метода.

## <a name="getting-text-blob-content-by-binding-to-string"></a>Получение содержимого текстового большого двоичного объекта путем привязки к строке
Если будут использоваться текстовые большие двоичные объекты, то можно применить к параметру **string** метод **BlobTrigger**. Код в следующем примере привязывает текстовый большой двоичный объект к параметру **string** с именем **logMessage**. Функция использует этот параметр для записи содержимого большого двоичного объекта на панель мониторинга пакета SDK для заданий WebJob.

        public static void WriteLog([BlobTrigger("input/{name}")] string logMessage,
            string name,
            TextWriter logger)
        {
             logger.WriteLine("Blob name: {0}", name);
             logger.WriteLine("Content:");
             logger.WriteLine(logMessage);
        }

## <a name="getting-serialized-blob-content-by-using-icloudblobstreambinder"></a>Получение содержимого сериализованного большого двоичного объекта с помощью ICloudBlobStreamBinder
В следующем примере кода используется класс, который реализует привязку **ICloudBlobStreamBinder**, чтобы задействовать атрибут **BlobTrigger** для привязки большого двоичного объекта к типу **WebImage**.

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

Код привязки **WebImage** предоставляется в классе **WebImageBinder**, производном от **ICloudBlobStreamBinder**.

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

## <a name="how-to-handle-poison-blobs"></a>Способ обработки подозрительных больших двоичных объектов
При сбое функции **BlobTrigger** пакет SDK вызывает ее снова, если сбой вызван временной ошибкой. Если сбой вызван содержимым большого двоичного объекта, каждый раз при попытке обработать большой двоичный объект будет происходить сбой функции. По умолчанию пакет SDK вызывает функцию до 5 раз для заданного большого двоичного объекта. Если при пятой попытке происходит сбой, пакет SDK добавляет сообщение в очередь с именем *webjobs-blobtrigger-poison*.

Можно настроить максимальное количество попыток. Тот же параметр **MaxDequeueCount** используется для обработки подозрительных больших двоичных объектов и подозрительных сообщений очереди.

Сообщением очереди для подозрительных больших двоичных объектов является объект JSON, содержащий следующие свойства:

* FunctionId (в формате *{имя_веб-задания}*.Functions.*{имя_функции}*, например: WebJob1.Functions.CopyBlob);
* BlobType (BlockBlob или PageBlob);
* ContainerName;
* BlobName
* ETag (идентификатор версии BLOB-объектов, например 0x8D1DC6E70A277EF)

В следующем примере кода функция **CopyBlob** содержит код, который приводит к сбою при каждом вызове функции. После того как пакет SDK вызывает ее максимальное количество раз, в очереди подозрительных больших двоичных объектов создается сообщение, и оно обрабатывается функцией **LogPoisonBlob** .

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

Пакет SDK автоматически выполняет десериализацию сообщения JSON. Вот класс **PoisonBlobMessage** :

        public class PoisonBlobMessage
        {
            public string FunctionId { get; set; }
            public string BlobType { get; set; }
            public string ContainerName { get; set; }
            public string BlobName { get; set; }
            public string ETag { get; set; }
        }

### <a name="blob-polling-algorithm"></a>Алгоритм опроса больших двоичных объектов
Пакет SDK для веб-заданий проверяет все контейнеры, задаваемые атрибутами **BlobTrigger**, при запуске приложения. Такая проверка может занять некоторое время в большой учетной записи хранения. Поэтому для поиска новых больших двоичных объектов и выполнения функций **BlobTrigger** может потребоваться заметное количество времени.

Чтобы обнаружить новые или измененные большие двоичные объекты после запуска приложения, пакет SDK периодически проверяет записи в журналах хранилища больших двоичных объектов. Журналы больших двоичных объектов помещаются в буфер, а физическая запись происходит каждые 10 минут или около того. Поэтому после создания или обновления большого двоичного объекта соответствующая функция **BlobTrigger** может выполняться со значительной задержкой.

Существует одно исключение для больших двоичных объектов, создаваемых с помощью атрибута **BlobTrigger** . Когда пакет SDK для веб-заданий создает новый большой двоичный объект, он немедленно передает его любой соответствующей функции **BlobTrigger** . Поэтому при объединении входящих и исходящих больших двоичных объектов в цепочку пакет SDK может их эффективно обрабатывать. Но если при выполнении функций обработки больших двоичных объектов, созданных или обновленных другими способами, нужны малые задержки, мы советуем использовать **QueueTrigger** вместо **BlobTrigger**.

### <a name="blob-receipts"></a>Уведомления о получении большого двоичного объекта
Пакет SDK для веб-заданий гарантирует, что для одного и того же нового или обновленного большого двоичного объекта функция **BlobTrigger** будет вызываться только один раз. Это достигается за счет сохранения *уведомлений о получении большого двоичного объекта* , которые позволяют определить, обработана ли версия этого большого двоичного объекта.

Уведомления о получении большого двоичного объекта хранятся в контейнере с именем *azure-webjobs-hosts* в учетной записи хранения Azure, указанной в строке подключения AzureWebJobsStorage. Уведомление о получении большого двоичного объекта содержит следующую информацию:

* функция, вызванная для большого двоичного объекта (*{имя_веб-задания}*.Functions.*{имя_функции}*, например WebJob1.Functions.CopyBlob);
* имя контейнера;
* тип большого двоичного объекта (BlockBlob или PageBlob);
* имя большого двоичного объекта;
* ETag (идентификатор версии больших двоичных объектов, например 0x8D1DC6E70A277EF).

Если вам необходимо выполнить принудительную повторную обработку большого двоичного объекта, уведомление о получении этого большого двоичного объекта можно удалить из контейнера *azure-webjobs-hosts* вручную.

## <a name="related-topics-covered-by-the-queues-article"></a>Связанные разделы, которые описаны в статье об очередях
Дополнительную информацию об обработке больших двоичных объектов, которая инициируется сообщением очереди, а также несвязанные с обработкой больших двоичных объектов сценарии для пакета SDK для веб-заданий см. в статье [Использование пакета SDK веб-заданий для работы с хранилищем очередей Azure](https://github.com/Azure/azure-webjobs-sdk/wiki).

В этой статье рассматриваются следующие связанные разделы:

* Асинхронные функции
* Выполнение на нескольких экземплярах
* Корректное завершение работы
* Использование атрибутов пакета SDK для заданий WebJob очереди в теле функции
* Установка строк подключения пакета SDK в коде.
* Установка значений параметров конструктора пакета SDK для заданий WebJob в коде
* Настройте **MaxDequeueCount** для обработки подозрительных больших двоичных объектов.
* Вызов функции вручную
* Запись журналов

## <a name="next-steps"></a>Дополнительная информация
В этой статье приведены примеры кода обработки обычных сценариев для работы с большими двоичными объектами Azure. Дополнительная информация об использовании веб-заданий Azure и пакета SDK для веб-заданий доступна в [ресурсах с документацией по веб-заданиям Azure](http://go.microsoft.com/fwlink/?linkid=390226).


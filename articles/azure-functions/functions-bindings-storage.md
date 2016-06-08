<properties
	pageTitle="Триггеры и привязки для службы хранилища Azure в функциях Azure | Microsoft Azure"
	description="Узнайте, как использовать триггеры и привязки службы хранилища Azure в функциях Azure."
	services="functions"
	documentationCenter="na"
	authors="christopheranderson"
	manager="erikre"
	editor=""
	tags=""
	keywords="функции azure, функции, обработка событий, динамические вычисления, независимая архитектура"/>

<tags
	ms.service="functions"
	ms.devlang="multiple"
	ms.topic="reference"
	ms.tgt_pltfrm="multiple"
	ms.workload="na"
	ms.date="05/16/2016"
	ms.author="chrande"/>

# Триггеры и привязки для службы хранилища Azure в функциях Azure

Эта статья объясняет, как настроить и запрограммировать триггеры и привязки для службы хранилища Azure в функциях Azure.

[AZURE.INCLUDE [общие сведения](../../includes/functions-bindings-intro.md)]

## <a id="storagequeuetrigger"></a> Триггер очереди службы хранилища Azure

#### Файл function.json для триггера очереди службы хранилища

Файл *function.json* содержит следующие свойства:

- `name` — имя переменной, используемой в коде функции для очереди или сообщения очереди. 
- `queueName` — имя очереди для опроса. Сведения о правилах именования очередей см. в статье [Именование очередей и метаданных](https://msdn.microsoft.com/library/dd179349.aspx).
- `connection` — имя параметра приложения, содержащего строку подключения к службе хранилища. Если оставить свойство `connection` пустым, триггер будет использовать строку подключения службы хранилища по умолчанию для приложения-функции. Она задается в параметре приложения AzureWebJobsStorage.
- `type` — для этого свойства нужно задать значение *queueTrigger*.
- `direction` — для этого свойства нужно задать значение *in*. 

Пример файла *function.json* для триггера очереди службы хранилища:

```json
{
    "disabled": false,
    "bindings": [
        {
            "name": "myQueueItem",
            "queueName": "myqueue-items",
            "connection":"",
            "type": "queueTrigger",
            "direction": "in"
        }
    ]
}
```

#### Поддерживаемые типы триггеров очереди

Сообщение очереди можно десериализировать в один из следующих типов:

* объект (JSON);
* Строка
* массив байтов; 
* `CloudQueueMessage` (C#) 

#### Метаданные триггера очереди

Метаданные очереди можно добавить в функцию, используя следующие имена переменных:

* expirationTime;
* insertionTime;
* nextVisibleTime;
* id
* popReceipt;
* dequeueCount.
* queueTrigger (другой способ получения текста сообщения очереди в виде строки).

В этом примере кода C# получаются и регистрируются метаданные очереди:

```csharp
public static void Run(string myQueueItem, 
    DateTimeOffset expirationTime, 
    DateTimeOffset insertionTime, 
    DateTimeOffset nextVisibleTime,
    string queueTrigger,
    string id,
    string popReceipt,
    int dequeueCount,
    TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}\n" +
        $"queueTrigger={queueTrigger}\n" +
        $"expirationTime={expirationTime}\n" +
        $"insertionTime={insertionTime}\n" +
        $"nextVisibleTime={nextVisibleTime}\n" +
        $"id={id}\n" +
        $"popReceipt={popReceipt}\n" + 
        $"dequeueCount={dequeueCount}");
}
```

#### Обработка подозрительных сообщений очереди

Сообщения, содержимое которых вызывает сбой функции, называются *подозрительными сообщениями*. При сбое функции сообщение очереди не удаляется и в конечном итоге забирается снова, вызывая повтор цикла. Пакет SDK может автоматически прервать цикл после ограниченного числа итераций или это можно сделать вручную.

Пакет SDK вызывает функцию обработки сообщения очереди до 5 раз. В случае сбоя во время пятой попытки сообщение перемещается в очередь подозрительных сообщений.

Очередь подозрительных сообщений называется *{originalqueuename}*-poison. Можно написать функции для обработки сообщений из очереди подозрительных сообщений путем внесения их в журнал или отправки уведомления о необходимости ручного вмешательства.

Если подозрительные сообщения очереди нужно обрабатывать вручную, определите количество попыток обработки сообщения, проверив значение свойства `dequeueCount`.

## <a id="storagequeueoutput"></a> Выходная привязка очереди службы хранилища Azure

#### Файл function.json для выходной привязки очереди службы хранилища

Файл *function.json* содержит следующие свойства:

- `name` — имя переменной, используемой в коде функции для очереди или сообщения очереди. 
- `queueName` — имя очереди. Сведения о правилах именования очередей см. в статье [Именование очередей и метаданных](https://msdn.microsoft.com/library/dd179349.aspx).
- `connection` — имя параметра приложения, содержащего строку подключения к службе хранилища. Если оставить свойство `connection` пустым, триггер будет использовать строку подключения службы хранилища по умолчанию для приложения-функции. Она задается в параметре приложения AzureWebJobsStorage.
- `type` — для этого свойства нужно задать значение *queue*.
- `direction` — для этого свойства нужно задать значение *out*. 

Пример файла *function.json* для выходной привязки очереди службы хранилища, которая использует очередь триггера и записывает сообщение в очередь:

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnection",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "myQueue",
      "queueName": "samples-workitems-out",
      "connection": "MyStorageConnection",
      "type": "queue",
      "direction": "out"
    }
  ],
  "disabled": false
}
``` 

#### Поддерживаемые типы выходных привязок очереди

Привязка `queue` может сериализировать в сообщения очереди следующие типы:

* объект (`out T` в C#, создает сообщение с пустым объектом, если по завершении функции значение параметра равно null);
* строка (`out string` в C#, создает сообщение очереди, если по завершении вызова функции значение параметра не равно null);
* массив байтов (`out byte[]` в C#, действует как строка); 
* `out CloudQueueMessage` (C#, действует как строка). 

В C# также можно выполнить привязку к `ICollector<T>` или `IAsyncCollector<T>`, где `T` — это один из поддерживаемых типов.

#### Пример кода выходной привязки очереди

В этом примере кода C# записывается по одному сообщению выходной очереди для каждого сообщения входной очереди.

```csharp
public static void Run(string myQueueItem, out string myOutputQueueItem, TraceWriter log)
{
    myOutputQueueItem = myQueueItem + "(next step)";
}
```

В этом примере кода C# записывается несколько сообщений с использованием параметра `ICollector<T>` (используйте `IAsyncCollector<T>` в асинхронной функции):

```csharp
public static void Run(string myQueueItem, ICollector<string> myQueue, TraceWriter log)
{
    myQueue.Add(myQueueItem + "(step 1)");
    myQueue.Add(myQueueItem + "(step 2)");
}
```

## <a id="storageblobtrigger"></a> Триггер BLOB-объекта службы хранилища Azure

#### Файл function.json для триггера BLOB-объекта службы хранилища

Файл *function.json* содержит следующие свойства:

- `name` — имя переменной, используемой в коде функции для BLOB-объекта. 
- `path` — путь, по которому расположен отслеживаемый контейнер, и при необходимости шаблон имени BLOB-объекта.
- `connection` — имя параметра приложения, содержащего строку подключения к службе хранилища. Если оставить свойство `connection` пустым, триггер будет использовать строку подключения службы хранилища по умолчанию для приложения-функции. Она задается в параметре приложения AzureWebJobsStorage.
- `type` — для этого свойства нужно задать значение *blobTrigger*.
- `direction` — для этого свойства нужно задать значение *in*.

Пример файла *function.json* для триггера BLOB-объекта службы хранилища, который отслеживает BLOB-объекты, добавляемые к контейнеру samples-workitems:

```json
{
    "disabled": false,
    "bindings": [
        {
            "name": "myBlob",
            "type": "blobTrigger",
            "direction": "in",
            "path": "samples-workitems",
            "connection":""
        }
    ]
}
```

#### Поддерживаемые типы триггеров больших двоичных объектов

Большой двоичный объект можно десериализировать в один из следующих типов функций Node или C#:

* объект (JSON);
* Строка

В функции C# можно выполнить привязку одного из следующих типов:

* `TextReader`
* `Stream`
* `ICloudBlob`
* `CloudBlockBlob`
* `CloudPageBlob`
* `CloudBlobContainer`
* `CloudBlobDirectory`
* `IEnumerable<CloudBlockBlob>`
* `IEnumerable<CloudPageBlob>`
* другие типы, десериализованные с помощью [ICloudBlobStreamBinder](../app-service-web/websites-dotnet-webjobs-sdk-storage-blobs-how-to.md#icbsb). 

#### Пример кода C# триггера большого двоичного объекта

В этом примере кода C# регистрируется содержимое каждого большого двоичного объекта, который добавляется в отслеживаемый контейнер.

```csharp
public static void Run(string myBlob, TraceWriter log)
{
    log.Info($"C# Blob trigger function processed: {myBlob}");
}
```

#### Шаблоны имен триггеров больших двоичных объектов

Шаблон имени BLOB-объекта можно указать в свойстве `path`. Например:

```json
"path": "input/original-{name}",
```

По этому пути можно найти BLOB-объект с именем *original-Blob1.txt* в контейнере *input*. В качестве значения переменной `name` в коде функции будет указано `Blob1`.

Другой пример:

```json
"path": "input/{blobname}.{blobextension}",
```

По этому пути можно также найти BLOB-объект с именем *original-Blob1.txt*. В качестве значений переменных `blobname` и `blobextension` в коде функции будет указано *original-Blob1* и *txt*.

Типы больших двоичных объектов, которые активируют функции, можно ограничить, указав шаблон с фиксированным значением расширения файла. Если задать для свойства `path` значение *samples/{имя}.png*, функцию будут активировать только BLOB-объекты с расширением *.png* в контейнере *samples*.

Если необходимо указать шаблон для имен больших двоичных объектов с фигурными скобками, используйте две фигурные скобки. Например, если нужно найти большие двоичные объекты в контейнере *images* с такими именами:

		{20140101}-soundfile.mp3

Используйте для свойства `path` следующее значение:

		images/{{20140101}}-{name}

В этом примере у переменной `name` будет значение *soundfile.mp3*.

#### Уведомления о получении большого двоичного объекта

Среда выполнения Функций Azure гарантирует, что для одного и того же нового или обновленного большого двоичного объекта функция активации большого двоичного объекта будет вызываться только один раз. Это достигается за счет сохранения *уведомлений о получении большого двоичного объекта*, которые позволяют определить, обработана ли версия этого большого двоичного объекта.

Уведомления о получении большого двоичного объекта хранятся в контейнере с именем *azure-webjobs-hosts* в учетной записи хранения Azure, указанной в строке подключения AzureWebJobsStorage. Уведомление о получении большого двоичного объекта содержит следующую информацию:

* функция, вызванная для большого двоичного объекта ("*{имя\_приложения\_функции}*.Functions.*{имя\_функции}*", например: functionsf74b96f7.Functions.CopyBlob);
* имя контейнера;
* тип большого двоичного объекта (BlockBlob или PageBlob);
* имя большого двоичного объекта;
* ETag (идентификатор версии больших двоичных объектов, например 0x8D1DC6E70A277EF).

Если вам необходимо выполнить принудительную повторную обработку большого двоичного объекта, уведомление о получении этого большого двоичного объекта можно удалить из контейнера *azure-webjobs-hosts* вручную.

#### Обработка подозрительных больших двоичных объектов

При сбое функции активации большого двоичного объекта пакет SDK вызывает ее снова, если причиной сбоя была временная ошибка. Если сбой вызван содержимым большого двоичного объекта, каждый раз при попытке обработать большой двоичный объект будет происходить сбой функции. По умолчанию пакет SDK вызывает функцию до 5 раз для заданного большого двоичного объекта. Если при пятой попытке происходит сбой, пакет SDK добавляет сообщение в очередь с именем *webjobs-blobtrigger-poison*.

Сообщением очереди для подозрительных больших двоичных объектов является объект JSON, содержащий следующие свойства:

* FunctionId (в формате *{имя\_приложения\_функции}*.Functions.*{имя\_функции}*);
* BlobType (BlockBlob или PageBlob);
* ContainerName;
* BlobName
* ETag (идентификатор версии BLOB-объектов, например 0x8D1DC6E70A277EF)

#### Опрос больших двоичных объектов для больших контейнеров

Если контейнер больших двоичных объектов, который отслеживает триггер, содержит более 10 000 больших двоичных объектов, среда выполнения Функций проверяет файлы журналов, чтобы найти новые или измененные большие двоичные объекты. Это не происходит в режиме реального времени. После создания большого двоичного объекта функция может не вызываться несколько минут или даже дольше. Кроме того, [журналы хранилища создаются на условиях максимально возможного исполнения](https://msdn.microsoft.com/library/azure/hh343262.aspx): регистрация всех событий не гарантируется. В некоторых случаях журналы могут пропускаться. Если ограниченная скорость и надежность триггеров больших двоичных объектов для больших контейнеров недопустимы для вашего приложения, для обработки большого двоичного объекта во время его создания рекомендуется создать сообщение очереди и использовать триггер очереди вместо триггера большого двоичного объекта.
 
## <a id="storageblobbindings"></a> Входные и выходные привязки для BLOB-объектов службы хранилища Azure

#### Файл function.json для входной и выходной привязки для BLOB-объектов службы хранилища

Файл *function.json* содержит следующие свойства:

- `name` — имя переменной, используемой в коде функции для BLOB-объекта. 
- `path` — путь, по которому расположен контейнер для чтения из BLOB-объекта или записи в BLOB-объект, и при необходимости шаблон имени BLOB-объекта.
- `connection` — имя параметра приложения, содержащего строку подключения к службе хранилища. Если оставить свойство `connection` пустым, привязка будет использовать строку подключения к службе хранилища по умолчанию для приложения-функции. Она указана в параметре приложения AzureWebJobsStorage.
- `type` — для этого свойства нужно задать значение *blob*.
- `direction` — для этого свойства нужно задать значения *in* или *out*. 

Пример файла *function.json* для входной или выходной привязки BLOB-объектов службы хранилища. В этом примере для копирования BLOB-объекта используется триггер:

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnection",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "myInputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}",
      "connection": "MyStorageConnection",
      "direction": "in"
    },
    {
      "name": "myOutputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}-Copy",
      "connection": "MyStorageConnection",
      "direction": "out"
    }
  ],
  "disabled": false
}
``` 

#### Поддерживаемые типы входных и выходных привязок больших двоичных объектов

Привязка `blob` в функциях Node.js или C# может сериализировать или десериализировать следующие типы:

* объект (`out T` в C#, для выходного BLOB-объекта: создает BLOB-объект в качестве объекта со значением null, если значение параметра равно null при завершении функции);
* строка (`out string` d C#, для выходного BLOB-объекта: создает BLOB-объект, только если для параметра строки не задано значение null при возврате функции).

В функции C# можно выполнить привязку следующих типов:

* `TextReader` (только для входных BLOB-объектов)
* `TextWriter` (только для выходных BLOB-объектов)
* `Stream`
* `CloudBlobStream` (только для выходных BLOB-объектов)
* `ICloudBlob`
* `CloudBlockBlob` 
* `CloudPageBlob` 

#### Пример кода C# с выходными данными большого двоичного объекта

В этом примере кода C# копируется большой двоичный объект, имя которого поступает в сообщение очереди.

```csharp
public static void Run(string myQueueItem, string myInputBlob, out string myOutputBlob, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    myOutputBlob = myInputBlob;
}
```

## <a id="storagetablesbindings"></a> Входные и выходные привязки для таблиц службы хранилища Azure

#### Файл function.json для таблиц службы хранилища

Файл *function.json* содержит следующие свойства:

- `name` — имя переменной, используемой в коде функции для привязки таблицы. 
- `tableName` — имя таблицы.
- `partitionKey` и `rowKey` — используются вместе для чтения одной сущности в функции C# или Node или записи одной сущности в функцию Node.
- `take` — максимальное число строк, которые нужно считать, для входных данных таблицы в функции Node.
- `filter` — выражение фильтра OData для входных данных таблицы в функции Node.
- `connection` — имя параметра приложения, содержащего строку подключения к службе хранилища. Если оставить свойство `connection` пустым, привязка будет использовать строку подключения к службе хранилища по умолчанию для приложения-функции. Она указана в параметре приложения AzureWebJobsStorage.
- `type` — для этого свойства нужно задать значение *table*.
- `direction` — для этого свойства нужно задать значения *in* или *out*. 

В следующем примере файла *function.json* используется триггер очереди для чтения одной строки таблицы. В JSON значение ключа раздела жестко закодировано и указывает, что ключ строки берется из сообщения очереди.

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnection",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "personEntity",
      "type": "table",
      "tableName": "Person",
      "partitionKey": "Test",
      "rowKey": "{queueTrigger}",
      "connection": "MyStorageConnection",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

#### Поддерживаемые типы входных и выходных таблиц службы хранилища

Привязка `table` может сериализировать или десериализировать объекты в функциях Node.js или C#. В объектах будут использоваться свойства RowKey и PartitionKey.

В функции C# можно выполнить привязку следующих типов:

* `T`, где `T` реализует `ITableEntity`
* `IQueryable<T>` (только для входных BLOB-объектов)
* `ICollector<T>` (только для выходных BLOB-объектов)
* `IAsyncCollector<T>` (только для выходных BLOB-объектов)

#### Сценарии привязки таблиц службы хранилища

Привязка таблицы применима в следующих сценариях.

* Чтение одной строкой в функции C# или Node.

	Задайте значения `partitionKey` и `rowKey`. Свойства `filter` и `take` не используются в этом сценарии.

* Чтение нескольких строк в функции C#.

	Среда выполнения функций предоставляет объект `IQueryable<T>`, привязанный к таблице. Тип `T` должен быть производным от параметра `TableEntity` или реализовать параметр `ITableEntity`. Свойства `partitionKey`, `rowKey`, `filter` и `take` не используются в этом сценарии. Для фильтрации можно использовать объект `IQueryable`.

* Чтение нескольких строк в функции Node.

	Укажите свойства `filter` и `take`. Не устанавливайте свойства `partitionKey` и `rowKey`.

* Запись одной или нескольких строк в функции C#.

	Среда выполнения функций предоставляет параметр `ICollector<T>` или `IAsyncCollector<T>`, привязанный к таблице, а в свойстве `T` указывается схема сущностей, которые требуется добавить. Как правило, тип `T` является производным от `TableEntity` или реализует `ITableEntity`, но это не обязательно. Свойства `partitionKey`, `rowKey`, `filter` и `take` не используются в этом сценарии.

#### Пример таблиц службы хранилища: чтение одной сущности таблицы в коде C# или Node

Следующий пример кода C# используется с предыдущим файлом *function.json*, приведенным ранее, для чтения одной сущности таблицы. В сообщении очереди содержится значение ключа строки, а сущность таблицы считывается в соответствии с типом, заданным в файле *run.csx*. Этот тип содержит свойства `PartitionKey` и `RowKey`. Он не является производным от `TableEntity`.

```csharp
public static void Run(string myQueueItem, Person personEntity, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    log.Info($"Name in Person entity: {personEntity.Name}");
}

public class Person
{
    public string PartitionKey { get; set; }
    public string RowKey { get; set; }
    public string Name { get; set; }
}
```

Следующий пример кода Node также используется с предыдущим файлом *function.json* для чтения одной сущности таблицы.

```javascript
module.exports = function (context, myQueueItem) {
    context.log('Node.js queue trigger function processed work item', myQueueItem);
    context.log('Person entity name: ' + context.bindings.personEntity.Name);
    context.done();
};
```

#### Пример таблиц службы хранилища: чтение нескольких сущностей таблицы на языке C# 

В следующем примере файла *function.json* и кода C# считываются сущности для ключа секции, указанного в сообщении очереди.

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnection",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "tableBinding",
      "type": "table",
      "connection": "MyStorageConnection",
      "tableName": "Person",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

В коде C# добавляется ссылка на пакет SDK для службы хранилища Azure, чтобы тип сущности мог быть производным от `TableEntity`.

```csharp
#r "Microsoft.WindowsAzure.Storage"
using Microsoft.WindowsAzure.Storage.Table;

public static void Run(string myQueueItem, IQueryable<Person> tableBinding, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    foreach (Person person in tableBinding.Where(p => p.PartitionKey == myQueueItem).ToList())
    {
        log.Info($"Name: {person.Name}");
    }
}

public class Person : TableEntity
{
    public string Name { get; set; }
}
``` 

#### Пример таблиц службы хранилища: создание сущностей таблицы на языке C# 

В следующем примере файлов *function.json* и *run.csx* показано, как создавать сущности таблицы на языке C#.

```json
{
  "bindings": [
    {
      "name": "input",
      "type": "manualTrigger",
      "direction": "in"
    },
    {
      "tableName": "Person",
      "connection": "MyStorageConnection",
      "name": "tableBinding",
      "type": "table",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

```csharp
public static void Run(string input, ICollector<Person> tableBinding, TraceWriter log)
{
    for (int i = 1; i < 10; i++)
        {
            log.Info($"Adding Person entity {i}");
            tableBinding.Add(
                new Person() { 
                    PartitionKey = "Test", 
                    RowKey = i.ToString(), 
                    Name = "Name" + i.ToString() }
                );
        }

}

public class Person
{
    public string PartitionKey { get; set; }
    public string RowKey { get; set; }
    public string Name { get; set; }
}

```

#### Пример таблиц службы хранилища: создание сущности таблицы на платформе Node

В следующем примере файлов *function.json* и *run.csx* показано, как создавать сущности таблицы на платформе Node.

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnection",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "tableName": "Person",
      "partitionKey": "Test",
      "rowKey": "{queueTrigger}",
      "connection": "MyStorageConnection",
      "name": "personEntity",
      "type": "table",
      "direction": "out"
    }
  ],
  "disabled": true
}
```

```javascript
module.exports = function (context, myQueueItem) {
    context.log('Node.js queue trigger function processed work item', myQueueItem);
    context.bindings.personEntity = {"Name": "Name" + myQueueItem }
    context.done();
};
```

## Дальнейшие действия

[AZURE.INCLUDE [дальнейшие действия](../../includes/functions-bindings-next-steps.md)]

<!---HONumber=AcomDC_0525_2016-->
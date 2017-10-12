---
title: "Привязки BLOB-объектов службы для Функций Azure | Документы Майкрософт"
description: "Узнайте, как использовать триггеры и привязки службы хранилища Azure в функциях Azure."
services: functions
documentationcenter: na
author: lindydonna
manager: cfowler
editor: 
tags: 
keywords: "функции azure, функции, обработка событий, динамические вычисления, независимая архитектура"
ms.assetid: aba8976c-6568-4ec7-86f5-410efd6b0fb9
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 05/25/2017
ms.author: glenga
ms.openlocfilehash: b123578dbac48018f674f85ec923e4c6e65fb9f8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="azure-functions-blob-storage-bindings"></a>Привязки хранилища BLOB-объектов для Функций Azure
[!INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

В этой статье рассматривается настройка привязок хранилища BLOB-объектов Azure в Функциях Azure, а также работа с ними. Функции Azure поддерживают привязки триггера, а также входные и выходные привязки для хранилища BLOB-объектов Azure. Описание возможностей, доступных во всех привязках, см. в статье [Основные понятия триггеров и привязок в Функциях Azure](functions-triggers-bindings.md).

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

> [!NOTE]
> [Учетная запись хранения только для больших двоичных объектов](../storage/common/storage-create-storage-account.md#blob-storage-accounts) не поддерживается. Триггерам и привязкам хранилища BLOB-объектов требуется учетная запись хранения общего назначения. 
> 

<a name="trigger"></a>
<a name="storage-blob-trigger"></a>
## <a name="blob-storage-triggers-and-bindings"></a>Триггеры и привязки хранилища BLOB-объектов

При использовании триггера хранилища BLOB-объектов Azure код функции вызывается при обнаружении нового или обновленного BLOB-объекта. Содержимое BLOB-объекта предоставляется в качестве входных данных функции.

Определите триггер хранилища BLOB-объектов на вкладке **Интеграция** портала функций. Портал создает следующее определение в разделе **bindings** файла *function.json*:

```json
{
    "name": "<The name used to identify the trigger data in your code>",
    "type": "blobTrigger",
    "direction": "in",
    "path": "<container to monitor, and optionally a blob name pattern - see below>",
    "connection": "<Name of app setting - see below>"
}
```

Привязки для ввода и вывода BLOB-объекта определяются с использованием `blob` в качестве типа привязки:

```json
{
  "name": "<The name used to identify the blob input in your code>",
  "type": "blob",
  "direction": "in", // other supported directions are "inout" and "out"
  "path": "<Path of input blob - see below>",
  "connection":"<Name of app setting - see below>"
},
```

* Свойство `path` поддерживает выражения привязки и параметры фильтра. См. раздел [Шаблоны имен](#pattern).
* Свойство `connection` должно включать в себя имя параметра приложения, содержащего строку подключения к службе хранилища. На портале Azure стандартный редактор на вкладке **Интеграция** автоматически настраивает этот параметр приложения при выборе учетной записи хранения.

> [!NOTE]
> Если используется триггер BLOB-объекта в плане потребления, то после того как приложение-функция стало неактивным, обработка новых BLOB-объектов может осуществляться с задержкой до 10 минут. После запуска приложения-функции BLOB-объекты обрабатываются немедленно. Во избежание этой начальной задержки рассмотрите один из следующих вариантов:
> - Используйте план службы приложений с включенной функцией AlwaysOn.
> - Используйте другой механизм для активации обработки большого двоичного объекта, например сообщение очереди, содержащее имя большого двоичного объекта. Пример см. в разделе [Триггер очереди с привязкой BLOB-объекта для ввода](#input-sample).

<a name="pattern"></a>

### <a name="name-patterns"></a>Шаблоны имен
Шаблон имени BLOB-объекта можно указать в свойстве `path`, которое может представлять собой фильтр или выражение привязки. См. раздел [Выражения привязки и шаблоны](functions-triggers-bindings.md#binding-expressions-and-patterns).

Например, чтобы отфильтровать BLOB-объекты, которые начинаются со строки "original", используйте приведенное ниже определение. Этот путь находит BLOB-объект с именем *original-Blob1.txt* в контейнере *input*. В этом примере переменная `name` в коде функции имеет значение `Blob1`.

```json
"path": "input/original-{name}",
```

Чтобы выполнить привязку имени и расширения файла BLOB-объекта по отдельности, используйте два шаблона. Этот путь также находит BLOB-объект с именем *original-Blob1.txt*, но передает в код функции другие переменные: `blobname` и `blobextension` со значениями *original-Blob1* и *txt* соответственно.

```json
"path": "input/{blobname}.{blobextension}",
```

Тип файлов больших двоичных объектов можно ограничить, используя фиксированное значение для расширения файла. Например, чтобы триггер активировался только для файлов PNG, используйте следующий шаблон:

```json
"path": "samples/{name}.png",
```

Фигурные скобки используются в качестве специальных символов в шаблонах имен. Чтобы указать имена BLOB-объектов с фигурными скобками, экранируйте скобки, используя две фигурные скобки. Следующий пример находит BLOB-объект с именем *{20140101}-soundfile.mp3* в контейнере *images*, а в качестве значения переменной `name` в коде функции будет задано *soundfile.mp3*. 

```json
"path": "images/{{20140101}}-{name}",
```

### <a name="trigger-metadata"></a>Метаданные триггера

Триггер BLOB-объектов предоставляет несколько свойств метаданных. Эти свойства можно использовать как часть выражений привязки в других привязках или как параметры в коде. Эти значения имеют ту же семантику, что и [CloudBlob](https://docs.microsoft.com/en-us/dotnet/api/microsoft.windowsazure.storage.blob.cloudblob?view=azure-dotnet).

- **BlobTrigger**. Введите `string`. Путь к BLOB-объекту, активирующему триггер
- **Uri** Введите `System.Uri`. Код URI BLOB-объекта для основного расположения.
- **Properties** Введите `Microsoft.WindowsAzure.Storage.Blob.BlobProperties`. Системные свойства BLOB-объекта.
- **Metadata** Введите `IDictionary<string,string>`. Определяемые пользователем метаданные для BLOB-объекта.

<a name="receipts"></a>

### <a name="blob-receipts"></a>Уведомления о получении большого двоичного объекта
Среда выполнения Функций Azure гарантирует, что для одного и того же нового или обновленного BLOB-объекта функция активации BLOB-объекта будет вызываться только один раз. Для определения того, обработана ли версия этого BLOB-объекта, сохраняются *уведомления о получении BLOB-объекта*.

Функции Azure сохраняют уведомления о получении BLOB-объектов в контейнере с именем *azure-webjobs-hosts* в учетной записи хранения Azure для приложения-функции (указывается с помощью параметра приложения `AzureWebJobsStorage`). Уведомление о получении большого двоичного объекта содержит следующую информацию:

* активированная функция (*&lt;имя_приложения-функции>*.Functions.*&lt;имя_функции>*, например: MyFunctionApp.Functions.CopyBlob);
* имя контейнера;
* тип большого двоичного объекта (BlockBlob или PageBlob);
* имя большого двоичного объекта;
* ETag (идентификатор версии больших двоичных объектов, например 0x8D1DC6E70A277EF).

Чтобы выполнить принудительную повторную обработку большого двоичного объекта, удалите уведомление о получении этого большого двоичного объекта из контейнера *azure-webjobs-hosts* вручную.

<a name="poison"></a>

### <a name="handling-poison-blobs"></a>Обработка подозрительных больших двоичных объектов
При сбое функции триггера BLOB-объекта Функции Azure по умолчанию выполняют ее для этого BLOB-объекта еще 5 раз. 

В случае сбоя после 5 попыток запуска Функции Azure добавляют сообщение в очередь службы хранилища с именем *webjobs-blobtrigger-poison*. Сообщением очереди для подозрительных больших двоичных объектов является объект JSON, содержащий следующие свойства:

* FunctionId (идентификатор функции в формате *&lt;имя_приложения-функции>*.Functions.*&lt;имя_функции>*);
* BlobType (BlockBlob или PageBlob);
* ContainerName;
* BlobName
* ETag (идентификатор версии BLOB-объектов, например 0x8D1DC6E70A277EF)

### <a name="blob-polling-for-large-containers"></a>Опрос больших двоичных объектов для больших контейнеров
Если отслеживаемый контейнер BLOB-объектов содержит более 10 000 BLOB-объектов, среда выполнения Функций проверяет файлы журналов, чтобы найти новые или измененные BLOB-объекты. Это не происходит в режиме реального времени. После создания большого двоичного объекта функция может не вызываться несколько минут или даже дольше. Кроме того, [журналы службы хранилища создаются по принципу лучшего из возможного](/rest/api/storageservices/About-Storage-Analytics-Logging), то есть не гарантируется регистрация всех событий. В некоторых случаях журналы могут пропускаться. Если требуется повысить скорость или надежность обработки BLOB-объектов, при создании BLOB-объекта рекомендуем создать [сообщение очереди](../storage/queues/storage-dotnet-how-to-use-queues.md). Затем для обработки BLOB-объекта используйте [триггер очереди](functions-bindings-storage-queue.md) вместо триггера BLOB-объекта.

<a name="triggerusage"></a>

## <a name="using-a-blob-trigger-and-input-binding"></a>Использование триггера BLOB-объекта и привязки для ввода
В функциях .NET для доступа к данным BLOB-объекта используйте параметр метода, например `Stream paramName`. Здесь `paramName` — это значение, указанное в [конфигурации триггера](#trigger). В функциях Node.js доступ к данным входного BLOB-объекта можно получить, используя `context.bindings.<name>`.

В .NET привязку можно выполнять к любым типам, приведенным в списке ниже. При использовании в привязке для ввода некоторые из этих типов требуют направления привязки `inout` в файле *function.json*. Это направление не поддерживается стандартным редактором, поэтому необходимо использовать расширенный редактор.

* `TextReader`
* `Stream`
* `ICloudBlob` (требует направления привязки inout)
* `CloudBlockBlob` (требует направления привязки inout)
* `CloudPageBlob` (требует направления привязки inout)
* `CloudAppendBlob` (требует направления привязки inout)

Если ожидаются текстовые BLOB-объекты, привязку также можно выполнить к типу .NET `string`. Это рекомендуется делать только в том случае, если BLOB-объект имеет небольшой размер, так как в память загружается все содержимое BLOB-объекта. Как правило, предпочтительнее использовать тип `Stream` или `CloudBlockBlob`.

## <a name="trigger-sample"></a>Пример триггера
Предположим, что у вас есть следующий файл function.json, определяющий триггер хранилища BLOB-объектов:

```json
{
    "disabled": false,
    "bindings": [
        {
            "name": "myBlob",
            "type": "blobTrigger",
            "direction": "in",
            "path": "samples-workitems",
            "connection":"MyStorageAccount"
        }
    ]
}
```

Ознакомьтесь с примером для конкретного языка, регистрирующим содержимое каждого большого двоичного объекта, который добавляется в отслеживаемый контейнер.

* [C#](#triggercsharp)
* [Node.js](#triggernodejs)

<a name="triggercsharp"></a>

### <a name="blob-trigger-examples-in-c"></a>Примеры триггеров BLOB-объектов на C# #

```cs
// Blob trigger sample using a Stream binding
public static void Run(Stream myBlob, TraceWriter log)
{
   log.Info($"C# Blob trigger function Processed blob\n Name:{name} \n Size: {myBlob.Length} Bytes");
}
```

```cs
// Blob trigger binding to a CloudBlockBlob
#r "Microsoft.WindowsAzure.Storage"

using Microsoft.WindowsAzure.Storage.Blob;

public static void Run(CloudBlockBlob myBlob, string name, TraceWriter log)
{
    log.Info($"C# Blob trigger function Processed blob\n Name:{name}\nURI:{myBlob.StorageUri}");
}
```

<a name="triggernodejs"></a>

### <a name="trigger-example-in-nodejs"></a>Пример триггера в Node.js

```javascript
module.exports = function(context) {
    context.log('Node.js Blob trigger function processed', context.bindings.myBlob);
    context.done();
};
```
<a name="outputusage"></a>
<a name="storage-blob-output-binding"></a>

## <a name="using-a-blob-output-binding"></a>Использование привязки BLOB-объекта для вывода

В функциях .NET следует использовать либо параметр `out string` в сигнатуре функции, либо один из типов из приведенного ниже списка. В функциях Node.js доступ к выходному большому двоичному объекту можно получить, используя `context.bindings.<name>`.

В функциях .NET можно выполнить вывод одного из следующих типов:

* `out string`
* `TextWriter`
* `Stream`
* `CloudBlobStream`
* `ICloudBlob`
* `CloudBlockBlob` 
* `CloudPageBlob` 

<a name="input-sample"></a>

## <a name="queue-trigger-with-blob-input-and-output-sample"></a>Пример триггера очереди с вводом и выводом BLOB-объекта
Предположим, что у вас есть приведенный ниже файл function.json, определяющий [триггер хранилища очередей](functions-bindings-storage-queue.md), а также входные и выходные данные хранилища BLOB-объектов. Обратите внимание на использование свойства метаданных `queueTrigger` В свойствах `path` входных и выходных данных BLOB-объекта:

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

Ознакомьтесь с примером для конкретного языка, копирующим входной большой двоичный объект в выходной большой двоичный объект.

* [C#](#incsharp)
* [Node.js](#innodejs)

<a name="incsharp"></a>

### <a name="blob-binding-example-in-c"></a>Пример привязки BLOB-объекта на C# #

```cs
// Copy blob from input to output, based on a queue trigger
public static void Run(string myQueueItem, Stream myInputBlob, out string myOutputBlob, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    myOutputBlob = myInputBlob;
}
```

<a name="innodejs"></a>

### <a name="blob-binding-example-in-nodejs"></a>Пример привязки BLOB-объекта в Node.js

```javascript
// Copy blob from input to output, based on a queue trigger
module.exports = function(context) {
    context.log('Node.js Queue trigger function processed', context.bindings.myQueueItem);
    context.bindings.myOutputBlob = context.bindings.myInputBlob;
    context.done();
};
```

## <a name="next-steps"></a>Дальнейшие действия
[!INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)]


---
title: "Триггеры и привязки в функциях Azure"
description: "Узнайте, как подключить выполнение кода к интерактивным мероприятиям и облачным службам, используя триггеры и привязки в Функциях Azure."
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: 
tags: 
keywords: "функции azure, функции, обработка событий, веб-перехватчики, динамические вычисления, независимая архитектура"
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 11/21/2017
ms.author: glenga
ms.openlocfilehash: 92194b0d54de1271580a237e16e652b761b4d6d4
ms.sourcegitcommit: 1d423a8954731b0f318240f2fa0262934ff04bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/05/2018
---
# <a name="azure-functions-triggers-and-bindings-concepts"></a>Основные понятия триггеров и привязок в Функциях Azure

Эта статья является концептуальный обзор привязок в Azure функции и триггеры. Ниже описаны функции, которые являются общими для всех привязок и всех поддерживаемых языков.

## <a name="overview"></a>Обзор

*Триггер* определяет способ вызова функции. У функции должен быть только один триггер. С триггерами могут быть связанны данные, которые обычно являются полезными и активируют функцию.

Входные и выходные *привязки* реализуют декларативный способ подключения к данным из кода. Привязки необязательны, а у функции может быть несколько входных и выходных привязок. 

Триггеры и привязки позволяют избежать жесткого задания подробные сведения о службах, которые вы работаете. Вы функция получает данные (например, содержимое сообщения в очереди) в параметры функции. Отправка данных (например, чтобы создать очередь сообщений) с помощью возвращаемого значения функции, `out` параметра, или [объект "сборщик"](functions-reference-csharp.md#writing-multiple-output-values).

При разработке с помощью портала Azure функции, триггеры и привязки настраиваются в *function.json* файла. Портал предоставляет пользовательский Интерфейс для этой конфигурации, но файл можно изменять непосредственно, заменив **расширенный редактор**.

При разработке функции с помощью Visual Studio для создания библиотеки классов, настройка триггеров и привязки с помощью оформления методов и параметров с атрибутами.

## <a name="supported-bindings"></a>Поддерживаемые привязки

[!INCLUDE [Full bindings table](../../includes/functions-bindings.md)]

Сведения о том, какие привязки доступны в предварительной версии или утверждены для использования в рабочей среде, см. в статье [Поддерживаемые языки в решении "Функции Azure"](supported-languages.md).

## <a name="example-queue-trigger-and-table-output-binding"></a>Пример. Триггер очередей и выходная привязка таблицы

Предположим, что нужно для записи новой строки в хранилище таблиц Azure каждый раз, когда новое сообщение появляется в хранилища очередей Azure. Этот сценарий может быть реализован с помощью очереди Azure привязка для вывода триггер хранилище и хранилище таблиц Azure. 

Вот *function.json* файл для этого сценария. 

```json
{
  "bindings": [
    {
      "name": "order",
      "type": "queueTrigger",
      "direction": "in",
      "queueName": "myqueue-items",
      "connection": "MY_STORAGE_ACCT_APP_SETTING"
    },
    {
      "name": "$return",
      "type": "table",
      "direction": "out",
      "tableName": "outTable",
      "connection": "MY_TABLE_STORAGE_ACCT_APP_SETTING"
    }
  ]
}
```

Первый элемент в `bindings` массива является триггером хранилища очереди. `type` И `direction` свойства идентификации триггера. `name` Свойство идентифицирует параметр функции, который будет получать содержимое сообщения очереди. Имя очереди для мониторинга находится в `queueName`, и строка подключения — в параметрах приложения, определяется `connection`.

Второй элемент в `bindings` массив является хранилищем таблиц Azure привязка для вывода. `type` И `direction` свойства идентификации привязки. `name` Свойство указывает, как функция предоставит новую строку таблицы, в этом случае с помощью возвращаемого значения функции. Имя таблицы находится в `tableName`, и строка подключения — в параметрах приложения, определяется `connection`.

Чтобы просмотреть и отредактировать содержимое файла *function.json* на портале Azure, щелкните параметр **Расширенный редактор** на вкладке **Интегрировать** этой функции.

> [!NOTE]
> Значение `connection` имя параметра приложения, содержащий строку соединения, а не саму строку подключения. Привязки использовать соединения строк, хранящихся в параметрах приложения для обеспечения наиболее практики, *function.json* не содержит секретные данные службы.

Ниже приведен код скрипта C#, с этого триггера и привязки. Обратите внимание, что имя параметра, который предоставляет содержимое сообщения очереди `order`; это имя является обязательным, поскольку `name` значение свойства в *function.json* —`order` 

```cs
#r "Newtonsoft.Json"

using Newtonsoft.Json.Linq;

// From an incoming queue message that is a JSON object, add fields and write to Table storage
// The method return value creates a new row in Table Storage
public static Person Run(JObject order, TraceWriter log)
{
    return new Person() { 
            PartitionKey = "Orders", 
            RowKey = Guid.NewGuid().ToString(),  
            Name = order["Name"].ToString(),
            MobileNumber = order["MobileNumber"].ToString() };  
}
 
public class Person
{
    public string PartitionKey { get; set; }
    public string RowKey { get; set; }
    public string Name { get; set; }
    public string MobileNumber { get; set; }
}
```

С помощью функции JavaScript, можно использовать один и тот же файл function.json:

```javascript
// From an incoming queue message that is a JSON object, add fields and write to Table Storage
// The second parameter to context.done is used as the value for the new row
module.exports = function (context, order) {
    order.PartitionKey = "Orders";
    order.RowKey = generateRandomId(); 

    context.done(null, order);
};

function generateRandomId() {
    return Math.random().toString(36).substring(2, 15) +
        Math.random().toString(36).substring(2, 15);
}
```

В библиотеке классов того же триггера и сведения о привязке &mdash; очередей и таблиц имена учетных записей хранения, параметры для входа и выхода функции &mdash; обеспечивается атрибуты:

```csharp
 public static class QueueTriggerTableOutput
 {
     [FunctionName("QueueTriggerTableOutput")]
     [return: Table("outTable", Connection = "MY_TABLE_STORAGE_ACCT_APP_SETTING")]
     public static Person Run(
         [QueueTrigger("myqueue-items", Connection = "MY_STORAGE_ACCT_APP_SETTING")]JObject order, 
         TraceWriter log)
     {
         return new Person() {
                 PartitionKey = "Orders",
                 RowKey = Guid.NewGuid().ToString(),
                 Name = order["Name"].ToString(),
                 MobileNumber = order["MobileNumber"].ToString() };
     }
 }

 public class Person
 {
     public string PartitionKey { get; set; }
     public string RowKey { get; set; }
     public string Name { get; set; }
     public string MobileNumber { get; set; }
 }
```

## <a name="binding-direction"></a>Направление привязки

Все триггеры и привязки имеют свойство `direction` в файле *function.json*:

- Для триггеров направление всегда входящее (`in`).
- Для входных и выходных привязок используется как входящее (`in`), так и исходящее (`out`) направление.
- Некоторые привязки поддерживают специальное направление `inout`. При использовании `inout` на вкладке **Интегрировать** доступен только параметр **Расширенный редактор**.

При использовании [атрибуты в библиотеке классов](functions-dotnet-class-library.md) Настройка триггеров и привязок, направление в конструктор атрибута или выведены из типа параметра.

## <a name="using-the-function-return-type-to-return-a-single-output"></a>Использование возвращаемого функцией типа для возврата одного выходного значения

Предыдущий пример показано, как использовать возвращаемое значение функции для предоставления выходные данные для привязки, который указывается в *function.json* с помощью специальное значение `$return` для `name` свойства. (Поддерживается только в языках, которые возвращают значение, например, скрипт C#, JavaScript и F #.) Если у функции несколько выходных привязок, используйте `$return` только для одной из них. 

```json
// excerpt of function.json
{
    "name": "$return",
    "type": "blob",
    "direction": "out",
    "path": "output-container/{id}"
}
```

Примеры ниже показано как возвращаемые типы используются с привязками выходные данные в C# скрипта, JavaScript и F #.

```cs
// C# example: use method return value for output binding
public static string Run(WorkItem input, TraceWriter log)
{
    string json = string.Format("{{ \"id\": \"{0}\" }}", input.Id);
    log.Info($"C# script processed queue message. Item={json}");
    return json;
}
```

```cs
// C# example: async method, using return value for output binding
public static Task<string> Run(WorkItem input, TraceWriter log)
{
    string json = string.Format("{{ \"id\": \"{0}\" }}", input.Id);
    log.Info($"C# script processed queue message. Item={json}");
    return Task.FromResult(json);
}
```

```javascript
// JavaScript: return a value in the second parameter to context.done
module.exports = function (context, input) {
    var json = JSON.stringify(input);
    context.log('Node.js script processed queue message', json);
    context.done(null, json);
}
```

```fsharp
// F# example: use return value for output binding
let Run(input: WorkItem, log: TraceWriter) =
    let json = String.Format("{{ \"id\": \"{0}\" }}", input.Id)   
    log.Info(sprintf "F# script processed queue message '%s'" json)
    json
```

## <a name="binding-datatype-property"></a>Свойство привязки dataType

В .NET используйте тип параметра для определения типа данных для входных данных. Например, используйте `string` для привязки к тексту триггера очереди, массив байтов — для чтения в двоичном формате и пользовательский тип — для десериализации объекта POCO.

Для языков, которые вводятся динамически, таких как JavaScript, использовать `dataType` свойство в *function.json* файла. Например, для считывания содержимого HTTP-запроса в двоичном формате, задать `dataType` для `binary`:

```json
{
    "type": "httpTrigger",
    "name": "req",
    "direction": "in",
    "dataType": "binary"
}
```

Другие варианты для `dataType` — `stream` и `string`.

## <a name="resolving-app-settings"></a>Разрешение параметров приложения

Для управления секретами и строками подключения рекомендуется использовать параметры приложения, а не файлы конфигурации. Это ограничивает доступ к таким секретам и обеспечивает безопасное хранение файла *function.json* в общедоступном репозитории системы управления версиями.

Параметры приложений также удобно использовать при необходимости изменить конфигурации в соответствии со средой. Например, в тестовой среде можно отслеживать другую очередь или контейнер хранилища BLOB-объектов.

Параметры приложения разрешаются, если значение заключено в знаки процента, например `%MyAppSetting%`. Обратите внимание, что свойство `connection` триггеров и привязок является особым случаем и автоматически разрешает значения как параметры приложения. 

Следующий пример представляет собой триггер хранилища очередей Azure. Этот триггер использует параметр приложения `%input-queue-name%`, чтобы определить очередь, для которой он должен срабатывать.

```json
{
  "bindings": [
    {
      "name": "order",
      "type": "queueTrigger",
      "direction": "in",
      "queueName": "%input-queue-name%",
      "connection": "MY_STORAGE_ACCT_APP_SETTING"
    }
  ]
}
```

Тот же подход можно использовать в библиотеках классов:

```csharp
[FunctionName("QueueTrigger")]
public static void Run(
    [QueueTrigger("%input-queue-name%")]string myQueueItem, 
    TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
}
```

## <a name="trigger-metadata-properties"></a>Свойства метаданных триггера

Помимо полезных данных, предоставляемых триггером (например, сообщения очереди, инициирующего вызов функции), многие триггеры предоставляют также дополнительные значения метаданных. Эти значения можно использовать в качестве входных параметров в C# и F# или свойств объекта `context.bindings` в JavaScript. 

Например триггер хранилища очередей Azure поддерживает следующие свойства:

* QueueTrigger (содержимое активирующего сообщения, если строка допустима)
* DequeueCount
* ExpirationTime
* Идентификатор
* InsertionTime
* NextVisibleTime
* PopReceipt

Значения этих метаданных доступны в *function.json* свойства файла. Например предположим, используйте триггеры очереди и сообщения в очереди содержит имя большого двоичного объекта, который требуется прочитать. В *function.json* файл, можно использовать `queueTrigger` свойство метаданных в большом двоичном объекте `path` свойства, как показано в следующем примере:

```json
  "bindings": [
    {
      "name": "myQueueItem",
      "type": "queueTrigger",
      "queueName": "myqueue-items",
      "connection": "MyStorageConnection",
    },
    {
      "name": "myInputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}",
      "direction": "in",
      "connection": "MyStorageConnection"
    }
  ]
```

Подробные сведения о метаданных свойства для каждого триггера описываются в соответствующей статье ссылку. Пример см. в разделе [метаданных очереди триггера](functions-bindings-storage-queue.md#trigger---message-metadata). Документация доступна также на портале на вкладке **Интегрировать** в разделе **Документация** под областью конфигурации привязки.  

## <a name="binding-expressions-and-patterns"></a>Выражения привязки и шаблоны

*Выражения привязки* — это одна из самых эффективных функций триггеров и привязок. В конфигурации для привязки можно определить шаблон выражения, которые затем могут использоваться в коде или в других привязок. Метаданные триггер также могут использоваться в выражениях привязки, как показано в предыдущем разделе.

Предположим, например, вы хотите изменить размер изображения в контейнер хранилища отдельного большого двоичного объекта, аналогично **изменения размера изображения** шаблона в **новая функция** на портале Azure (см. **образцы**  сценария). 

Ниже приведено определение *function.json*:

```json
{
  "bindings": [
    {
      "name": "image",
      "type": "blobTrigger",
      "path": "sample-images/{filename}",
      "direction": "in",
      "connection": "MyStorageConnection"
    },
    {
      "name": "imageSmall",
      "type": "blob",
      "path": "sample-images-sm/{filename}",
      "direction": "out",
      "connection": "MyStorageConnection"
    }
  ],
}
```

Обратите внимание, что `filename` параметр используется в определении триггера больших двоичных объектов и большого двоичного объекта привязка для вывода. Этот параметр можно также использовать в коде функции.

```csharp
// C# example of binding to {filename}
public static void Run(Stream image, string filename, Stream imageSmall, TraceWriter log)  
{
    log.Info($"Blob trigger processing: {filename}");
    // ...
} 
```

<!--TODO: add JavaScript example -->
<!-- Blocked by bug https://github.com/Azure/Azure-Functions/issues/248 -->

Же возможность использования шаблонов и выражения привязки применяется к атрибутам в библиотеках классов. Например вот изображения, изменение размеров функции в библиотеке классов.

```csharp
[FunctionName("ResizeImage")]
[StorageAccount("AzureWebJobsStorage")]
public static void Run(
    [BlobTrigger("sample-images/{name}")] Stream image, 
    [Blob("sample-images-sm/{name}", FileAccess.Write)] Stream imageSmall, 
    [Blob("sample-images-md/{name}", FileAccess.Write)] Stream imageMedium)
{
    var imageBuilder = ImageResizer.ImageBuilder.Current;
    var size = imageDimensionsTable[ImageSize.Small];

    imageBuilder.Build(image, imageSmall,
        new ResizeSettings(size.Item1, size.Item2, FitMode.Max, null), false);

    image.Position = 0;
    size = imageDimensionsTable[ImageSize.Medium];

    imageBuilder.Build(image, imageMedium,
        new ResizeSettings(size.Item1, size.Item2, FitMode.Max, null), false);
}

public enum ImageSize { ExtraSmall, Small, Medium }

private static Dictionary<ImageSize, (int, int)> imageDimensionsTable = new Dictionary<ImageSize, (int, int)>() {
    { ImageSize.ExtraSmall, (320, 200) },
    { ImageSize.Small,      (640, 400) },
    { ImageSize.Medium,     (800, 600) }
};
```

### <a name="create-guids"></a>Создать GUID

`{rand-guid}` Выражение привязки создает идентификатор GUID. В следующем примере идентификатор GUID для создания имени уникальный BLOB-объектов: 

```json
{
  "type": "blob",
  "name": "blobOutput",
  "direction": "out",
  "path": "my-output-container/{rand-guid}"
}
```

### <a name="current-time"></a>Текущее время

Выражение привязки `DateTime` разрешается в `DateTime.UtcNow`.

```json
{
  "type": "blob",
  "name": "blobOutput",
  "direction": "out",
  "path": "my-output-container/{DateTime}"
}
```

## <a name="bind-to-custom-input-properties"></a>Привязки к свойствам пользовательского ввода

Выражения привязки могут также ссылаться на свойства, определенные в самих полезных данных. Например, можно установить динамическую привязку к файлу хранилища BLOB-объектов из файла, имя которого указанно в webhook.

Например, в приведенном ниже файле *function.json* используется свойство `BlobName` из полезных данных триггера.

```json
{
  "bindings": [
    {
      "name": "info",
      "type": "httpTrigger",
      "direction": "in",
      "webHookType": "genericJson"
    },
    {
      "name": "blobContents",
      "type": "blob",
      "direction": "in",
      "path": "strings/{BlobName}",
      "connection": "AzureWebJobsStorage"
    },
    {
      "name": "res",
      "type": "http",
      "direction": "out"
    }
  ]
}
```

Для этого в C# и F# необходимо определить тип POCO, определяющий поля, которые будут десериализованы в полезные данные триггера.

```csharp
using System.Net;

public class BlobInfo
{
    public string BlobName { get; set; }
}
  
public static HttpResponseMessage Run(HttpRequestMessage req, BlobInfo info, string blobContents)
{
    if (blobContents == null) {
        return req.CreateResponse(HttpStatusCode.NotFound);
    } 

    return req.CreateResponse(HttpStatusCode.OK, new {
        data = $"{blobContents}"
    });
}
```

В JavaScript десериализация JSON выполняется автоматически и свойства можно использовать напрямую.

```javascript
module.exports = function (context, info) {
    if ('BlobName' in info) {
        context.res = {
            body: { 'data': context.bindings.blobContents }
        }
    }
    else {
        context.res = {
            status: 404
        };
    }
    context.done();
}
```

## <a name="configuring-binding-data-at-runtime"></a>Настройка данных привязки во время выполнения

В C# и других языков .NET, можно использовать шаблон императивного привязки, в отличие от декларативной привязки в *function.json* и атрибуты. Императивную привязку удобно использовать, когда параметры привязки должны вычисляться не при проектировании, а во время выполнения. Дополнительные сведения см. в разделе справочника разработчика C# [Привязка в среде выполнения с помощью императивных привязок](functions-reference-csharp.md#imperative-bindings).

## <a name="functionjson-file-schema"></a>схема файла Function.JSON

*Function.json* файл схемы находится в [http://json.schemastore.org/function](http://json.schemastore.org/function).

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о конкретной привязке см. в следующих статьях:

- [HTTP и вызовы webhook](functions-bindings-http-webhook.md)
- [Таймер](functions-bindings-timer.md)
- [Хранилище очередей](functions-bindings-storage-queue.md)
- [Хранилище BLOB-объектов](functions-bindings-storage-blob.md)
- [Хранилище таблиц](functions-bindings-storage-table.md)
- [Концентратор событий](functions-bindings-event-hubs.md)
- [Служебная шина](functions-bindings-service-bus.md)
- [База данных Azure Cosmos](functions-bindings-documentdb.md)
- [Microsoft Graph](functions-bindings-microsoft-graph.md)
- [SendGrid](functions-bindings-sendgrid.md)
- [Twilio](functions-bindings-twilio.md)
- [Центры уведомлений](functions-bindings-notification-hubs.md)
- [Мобильные приложения](functions-bindings-mobile-apps.md)
- [Внешний файл](functions-bindings-external-file.md)

---
title: "Триггеры и привязки в решении \"Функции Azure\""
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
ms.openlocfilehash: 90a192f58f0e4b285f7aece8a3555c08df051f38
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/09/2018
---
# <a name="azure-functions-triggers-and-bindings-concepts"></a>Основные понятия триггеров и привязок в Функциях Azure

В этой статье приведены общие сведения о триггерах и привязках в решении "Функции Azure". Здесь описаны функции, которые являются общими для всех привязок и всех поддерживаемых языков.

## <a name="overview"></a>Обзор

*Триггер* определяет способ вызова функции. У функции должен быть только один триггер. С триггерами могут быть связанны данные, которые обычно являются полезными и активируют функцию.

Входные и выходные *привязки* реализуют декларативный способ подключения к данным из кода. Привязки необязательны, а у функции может быть несколько входных и выходных привязок. 

Триггеры и привязки позволяют не включать в код программ сведения о конкретных службах, с которыми вы работаете. Функция получает нужные данные (например, содержимое сообщения из очереди) в виде параметров. Вы отправляете данные (например, для создания в очереди сообщения) через возвращаемое значение функции, параметр `out`, или [объект "сборщик"](functions-reference-csharp.md#writing-multiple-output-values).

Если вы создаете функции на портале Azure, триггеры и привязки настраиваются в файле *function.json*. Портал предоставляет для этой настройки пользовательский интерфейс, но файл можно изменять напрямую, перейдя в **расширенный редактор**.

Если для создания функций вы пишете библиотеку класса в Visual Studio, для настройки триггеров и привязок методам и параметрам присваиваются атрибуты.

## <a name="supported-bindings"></a>Поддерживаемые привязки

[!INCLUDE [Full bindings table](../../includes/functions-bindings.md)]

Сведения о том, какие привязки доступны в предварительной версии или утверждены для использования в рабочей среде, см. в статье [Поддерживаемые языки в решении "Функции Azure"](supported-languages.md).

## <a name="example-queue-trigger-and-table-output-binding"></a>Пример. Триггер очередей и выходная привязка таблицы

Предположим, вам нужно, чтобы каждый раз, когда в хранилище очередей Azure появляется новое сообщение, создавалась строка в хранилище таблиц Azure. Этот сценарий можно реализовать с помощью триггера для хранилища очередей Azure и выходной привязки хранилища таблиц Azure. 

Ниже представлен пример файла *function.json* для этого сценария. 

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

Первым элементом массива `bindings` является триггер хранилища очередей. Свойства `type` и `direction` идентифицируют триггер. Свойство `name` идентифицирует параметр функции, который будет получать содержимое из сообщения очереди. Отслеживаемая очередь имеет имя `queueName`, а строка подключения содержится в параметре приложения, указанном в `connection`.

Вторым элементом массива `bindings` является выходная привязка хранилища таблиц Azure. Свойства `type` и `direction` идентифицируют привязку. Свойство `name` указывает, как функция предоставит новую строку таблицы. В нашем примере используется возвращаемое значение функции. Таблица имеет имя `tableName`, а строка подключения содержится в параметре приложения, указанном в `connection`.

Чтобы просмотреть и отредактировать содержимое файла *function.json* на портале Azure, щелкните параметр **Расширенный редактор** на вкладке **Интегрировать** этой функции.

> [!NOTE]
> Параметр `connection` содержит имя параметра приложения, в котором хранится строка подключения, но не саму строку. Привязки используют параметры приложения для хранения строк подключения, чтобы обеспечить соблюдение рекомендаций: файл *function.json* не должен содержать секреты службы.

Ниже приведен код скрипта C#, который работает с этим триггером и этой привязкой. Обратите внимание, что содержимое сообщения очереди предоставляется в параметре `order`. Это имя является обязательным, поскольку свойство `name` в файле *function.json* имеет значение `order`. 

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

Этот же файл function.json можно использовать и в функции JavaScript:

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

В библиотеке классов эта информация о триггере и привязке &mdash; имена очередей и таблиц, учетные записи хранения, входные и выходные параметры функции &mdash; передаются в виде атрибутов:

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

Если вы используете для настройки триггеров и привязок [атрибуты в библиотеке классов](functions-dotnet-class-library.md), направление задается в конструкторе атрибута или вычисляется по типу параметра.

## <a name="using-the-function-return-type-to-return-a-single-output"></a>Использование возвращаемого функцией типа для возврата одного выходного значения

В предыдущем примере показано, как использовать возвращаемое значение функции, чтобы предоставить выходное значение привязке, указанной в файле *function.json* с помощью специального значения `$return` для свойства `name`. (это поддерживается только в языках, у которых есть возвращаемое значение, например в скриптах C#, JavaScript и F#). Если у функции несколько выходных привязок, используйте `$return` только для одной из них. 

```json
// excerpt of function.json
{
    "name": "$return",
    "type": "blob",
    "direction": "out",
    "path": "output-container/{id}"
}
```

В примерах ниже показано, как возвращаемые типы используются с выходными привязками в скрипте C#, JavaScript и F#.

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

В .NET тип входных данных определяется типом параметра. Например, используйте `string` для привязки к тексту триггера очереди, массив байтов — для чтения в двоичном формате и пользовательский тип — для десериализации объекта POCO.

В языках с динамическим типированием, таких как JavaScript, используйте свойство `dataType` в файле *function.json*. Например, задайте для `dataType` значение `binary`, чтобы прочитать содержимое HTTP-запроса в двоичном формате.

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

Например, триггер хранилища очередей Azure поддерживает следующие свойства:

* QueueTrigger (содержимое активирующего сообщения, если строка допустима)
* DequeueCount
* ExpirationTime
* Идентификатор
* InsertionTime
* NextVisibleTime
* PopReceipt

Значения этих метаданных доступны в свойствах файла *function.json*. Предположим, что вы используете триггер очереди, а сообщение в очереди содержит имя большого двоичного объекта, который вам нужно прочитать. В файле *function.json* укажите свойство `queueTrigger` в метаданных большого двоичного объекта `path`, как показано в следующем примере:

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

Сведения о свойствах метаданных для каждого триггера приведены в соответствующих статьях документации. Пример см. в разделе [о метаданных триггера очередей](functions-bindings-storage-queue.md#trigger---message-metadata). Документация доступна также на портале на вкладке **Интегрировать** в разделе **Документация** под областью конфигурации привязки.  

## <a name="binding-expressions-and-patterns"></a>Выражения привязки и шаблоны

*Выражения привязки* — это одна из самых эффективных функций триггеров и привязок. При настройке привязки вы можете определить выражения шаблонов, чтобы использовать их в других привязках или в коде. Также в выражениях привязки можно использовать метаданные триггеров, как показано в предыдущем разделе.

Предположим, вам нужно изменить размер изображения в определенном контейнере хранилища BLOB-объектов, примерно как в шаблоне **изменения размера изображения** на странице **Новая функция** портала Azure (см. сценарий **Примеры**). 

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

Обратите внимание, что параметр `filename` используется в определении триггера больших двоичных объектов и в выходной привязке большого двоичного объекта. Этот параметр можно также использовать в коде функции.

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

Возможность применять выражения и шаблоны привязки распространяется и на атрибуты в библиотеке классов. Ниже приведен пример функции для изменения размеров изображения из библиотеки классов.

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

### <a name="create-guids"></a>Создание глобальных уникальных идентификаторов

Выражение привязки `{rand-guid}` создает идентификатор GUID. В следующем примере идентификатор GUID используется для создания уникального имени большого двоичного объекта: 

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

## <a name="bind-to-custom-input-properties"></a>Привязка к пользовательским входным свойствам

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

В C# и других языках .NET можно использовать шаблон императивной привязки, которая отличается от декларативной привязки в файле *function.json* или в атрибутах. Императивную привязку удобно использовать, когда параметры привязки должны вычисляться не при проектировании, а во время выполнения. Дополнительные сведения см. в разделе справочника разработчика C# [Привязка в среде выполнения с помощью императивных привязок](functions-reference-csharp.md#imperative-bindings).

## <a name="functionjson-file-schema"></a>Схема файла function.json

Схему файла *Function.json* вы можете найти по адресу [http://json.schemastore.org/function](http://json.schemastore.org/function).

## <a name="handling-binding-errors"></a>Обработка ошибок привязки

[!INCLUDE [bindings errors intro](../../includes/functions-bindings-errors-intro.md)]

Ссылки на соответствующие разделы с описанием ошибок различных служб, поддерживаемых в Функциях Azure, см. в разделе [Binding error codes](functions-bindings-error-pages.md#binding-error-codes) (Коды ошибок привязки) статьи [Azure Functions error handling](functions-bindings-error-pages.md) (Обработка ошибок службы "Функции Azure").  

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о конкретной привязке см. в следующих статьях:

- [HTTP и вызовы webhook](functions-bindings-http-webhook.md)
- [Таймер](functions-bindings-timer.md)
- [Хранилище очередей](functions-bindings-storage-queue.md)
- [Хранилище BLOB-объектов](functions-bindings-storage-blob.md)
- [Хранилище таблиц](functions-bindings-storage-table.md)
- [Концентратор событий](functions-bindings-event-hubs.md)
- [Служебная шина](functions-bindings-service-bus.md)
- [База данных Azure Cosmos](functions-bindings-cosmosdb.md)
- [Microsoft Graph](functions-bindings-microsoft-graph.md)
- [SendGrid](functions-bindings-sendgrid.md)
- [Twilio](functions-bindings-twilio.md)
- [Центры уведомлений](functions-bindings-notification-hubs.md)
- [Мобильные приложения](functions-bindings-mobile-apps.md)
- [Внешний файл](functions-bindings-external-file.md)

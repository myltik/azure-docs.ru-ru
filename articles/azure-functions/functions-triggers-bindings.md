---
title: "Работа с триггерами и привязками в Функциях Azure | Документация Майкрософт"
description: "Узнайте, как подключить выполнение кода к интерактивным мероприятиям и облачным службам, используя триггеры и привязки в Функциях Azure."
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: 
tags: 
keywords: "функции azure, функции, обработка событий, веб-перехватчики, динамические вычисления, независимая архитектура"
ms.assetid: cbc7460a-4d8a-423f-a63e-1cd33fef7252
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 05/30/2017
ms.author: glenga
ms.openlocfilehash: 74933d9c3535ab71f47c792e20bfbc35e589ec08
ms.sourcegitcommit: 9ae92168678610f97ed466206063ec658261b195
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/17/2017
---
# <a name="azure-functions-triggers-and-bindings-concepts"></a>Основные понятия триггеров и привязок в Функциях Azure
Функции Azure позволяют писать код в ответ на события в Azure и других службах с помощью *триггеров* и *привязок*. В этой статье приведены общие сведения о триггерах и привязках для всех поддерживаемых языков программирования. Здесь описываются функции, которые являются общими для всех привязок.

## <a name="overview"></a>Обзор

Триггеры и привязки реализуют декларативный способ определения того, как должны вызываться функции и с какими данными одни будут работать. *Триггер* определяет способ вызова функции. У функции должен быть только один триггер. С триггерами могут быть связанны данные, которые обычно являются полезными и активируют функцию. 

Входные и выходные *привязки* реализуют декларативный способ подключения к данным из кода. Как и в случае триггеров, для них необходимо указать строки подключения и другие свойства в конфигурации функции. Привязки необязательны, а у функции может быть несколько входных и выходных привязок. 

Используя триггеры и привязки, можно писать более универсальный код без жесткого кодирования данных служб, с которыми он взаимодействует. Данные, поступающие из служб, становятся для кода вашей функции обычными входными значениями. Для вывода данных в другую службу (например, создания новой строки в Хранилище таблиц Azure) следует использовать возвращаемое значение метода. Если требуется вывести несколько значений, воспользуйтесь вспомогательным объектом. Триггеры и привязки получают свойство **name**, которое представляет собой идентификатор, используемый в коде для доступа к привязке.

Триггеры и привязки можно настроить на вкладке **Интегрировать** на портале Функций Azure. На самом деле пользовательский интерфейс изменяет файл с именем *function.json* в каталоге функции. Этот файл можно изменить с помощью **расширенного редактора**.

В следующей таблице приведены триггеры и привязки, поддерживаемые Функциями Azure. 

[!INCLUDE [Full bindings table](../../includes/functions-bindings.md)]

### <a name="example-queue-trigger-and-table-output-binding"></a>Пример. Триггер очередей и выходная привязка таблицы

Предположим, что всякий раз, когда в хранилище очередей Azure появляется новое сообщение, требуется создать новую строку для Хранилища таблиц Azure. Этот сценарий может быть реализован с помощью триггера хранилища очередей Azure и выходной привязки хранилища таблиц Azure. 

Для триггера хранилища очередей Azure требуются следующие данные с вкладки **Интегрировать**:

* имя параметра приложения, который содержит строку подключения учетной записи хранения Azure для хранилища очередей Azure;
* имя очереди;
* идентификатор в коде, используемый для считывания содержимого сообщений очереди, например `order`.

Для записи в Хранилище таблиц Azure необходимо использовать выходную привязку со следующими параметрами:

* имя параметра приложения, который содержит строку подключения учетной записи хранения Azure для хранилища таблиц Azure;
* имя таблицы;
* идентификатор в коде для создания выходных элементов или возвращаемое значение функции.

Привязки используют значения строк подключения, хранимые в параметрах приложения, чтобы реализовать рекомендацию, согласно которой файл *function.json* не должен содержать секреты служб, а вместо этого просто содержит имена параметров приложения.

Затем используются идентификаторы, указанные в коде для интеграции со службой хранилища Azure.

```cs
#r "Newtonsoft.Json"

using Newtonsoft.Json.Linq;

// From an incoming queue message that is a JSON object, add fields and write to Table Storage
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

Ниже приведено содержимое файла *function.json*, соответствующее предыдущему коду. Обратите внимание, что конфигурацию можно использовать независимо от языка реализации функции.

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
Чтобы просмотреть и отредактировать содержимое файла *function.json* на портале Azure, щелкните параметр **Расширенный редактор** на вкладке **Интегрировать** этой функции.

Дополнительные примеры кода и сведения об интеграции со службой хранилища Azure см. в статье [Привязки больших двоичных объектов службы хранилища для Функций Azure](functions-bindings-storage.md).

### <a name="binding-direction"></a>Направление привязки

У всех триггеров и привязок есть направление (свойство `direction`):

- Для триггеров направление всегда входящее (`in`).
- Для входных и выходных привязок используется как входящее (`in`), так и исходящее (`out`) направление.
- Некоторые привязки поддерживают специальное направление `inout`. При использовании `inout` на вкладке **Интегрировать** доступен только параметр **Расширенный редактор**.

## <a name="using-the-function-return-type-to-return-a-single-output"></a>Использование возвращаемого функцией типа для возврата одного выходного значения

В предыдущем примере показано, как использовать возвращаемое значение функции для предоставления выходного значения привязке, обращение к которой осуществляется с помощью параметра специального имени `$return`. (Это поддерживается только в языках, у которых есть возвращаемое значение, например C#, JavaScript и F#.) Если у функции несколько выходных привязок, используйте `$return` только для одной из них. 

```json
// excerpt of function.json
{
    "name": "$return",
    "type": "blob",
    "direction": "out",
    "path": "output-container/{id}"
}
```

В примерах ниже показано, как возвращаемые типы используются с выходными привязками в C#, JavaScript и F#.

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

В .NET используйте типы для определения типа входных данных. Например, используйте `string` для привязки к тексту триггера очереди, массив байтов — для чтения в двоичном формате и пользовательский тип — для десериализации объекта POCO.

Для языков с динамическим вводом, таких как JavaScript, используйте свойство `dataType` в определении привязки. Например, чтобы считать содержимое HTTP-запроса в двоичном формате, используйте тип `binary`:

```json
{
    "type": "httpTrigger",
    "name": "req",
    "direction": "in",
    "dataType": "binary"
}
```

Другие параметры для `dataType`: `stream` и `string`.

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

Сведения о свойствах метаданных для каждого триггера приведены в соответствующем разделе справки. Документация доступна также на портале на вкладке **Интегрировать** в разделе **Документация** под областью конфигурации привязки.  

Например, так как у триггеров BLOB-объекта есть определенная задержка, для выполнения функции можно использовать триггер очереди (см. раздел [Триггер хранилища BLOB-объектов](functions-bindings-storage-blob.md#storage-blob-trigger)). Сообщение очереди будет содержать имя файла BLOB-объекта, по которому активируется триггер. Используя свойство метаданных `queueTrigger`, можно задать такое поведение не в коде, а в конфигурации.

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

Свойства метаданных из триггера могут также использоваться в *выражении привязки* для другой привязки, как описано в следующем разделе.

## <a name="binding-expressions-and-patterns"></a>Выражения привязки и шаблоны

*Выражения привязки* — это одна из самых эффективных функций триггеров и привязок. В привязке можно определить шаблоны выражений, которые затем можно использовать в других привязках или коде. Метаданные триггера также могут использоваться в выражениях привязки, как показано в примере в предыдущем разделе.

Предположим, например, что необходимо изменить размер изображения в определенном контейнере хранилища BLOB-объектов аналогично тому, как это делается в шаблоне **изменения размера изображения** на странице **Новая функция**. Последовательно выберите **Новая функция** -> Язык **C#** -> Сценарий **Примеры** -> **ImageResizer-CSharp**. 

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

Обратите внимание, что параметр `filename` используется как в определении триггера BLOB-объектов, так и в выходной привязке большого двоичного объекта. Этот параметр можно также использовать в коде функции.

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


### <a name="random-guids"></a>Случайные значения GUID
Функции Azure предоставляют удобный синтаксис для создания в привязках идентификаторов GUID с помощью выражения привязки `{rand-guid}`. В следующем примере эта возможность используется для создания уникального имени большого двоичного объекта: 

```json
{
  "type": "blob",
  "name": "blobOutput",
  "direction": "out",
  "path": "my-output-container/{rand-guid}"
}
```

### <a name="current-time"></a>Текущее время

Вы можете использовать выражение привязки `DateTime`, разрешающееся в `DateTime.UtcNow`.

```json
{
  "type": "blob",
  "name": "blobOutput",
  "direction": "out",
  "path": "my-output-container/{DateTime}"
}
```

## <a name="bind-to-custom-input-properties-in-a-binding-expression"></a>Привязка к пользовательским входным свойствам в выражении привязки

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

В C# и других языках .NET вы можете использовать императивный шаблон привязки в отличие от декларативных привязок в *function.json*. Императивную привязку удобно использовать, когда параметры привязки должны вычисляться не при проектировании, а во время выполнения. Дополнительные сведения см. в разделе справочника разработчика C# [Привязка в среде выполнения с помощью императивных привязок](functions-reference-csharp.md#imperative-bindings).

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

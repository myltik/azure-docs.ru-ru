---
title: Получение событий из Сетки событий Azure в конечную точку HTTP
description: Описано, как проверить конечную точку HTTP, затем проверить и десериализировать события из Сетки событий Azure
services: event-grid
author: banisadr
manager: darosa
ms.service: event-grid
ms.topic: conceptual
ms.date: 04/26/2018
ms.author: babanisa
ms.openlocfilehash: 89d0f11ccfb9a359ca3e43bc1a370e0fb7514574
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/18/2018
---
# <a name="receive-events-to-an-http-endpoint"></a>Получение событий через конечную точку HTTP

В этой статье описано, как [проверить конечную точку HTTP](security-authentication.md#webhook-event-delivery), чтобы получать события из подписки событий, а затем получать события и десериализировать их. В этой статье в целях демонстрации используется служба "Функции Azure", однако те же принципы применяются независимо от того, где размещается приложение.

> [!NOTE]
> При активации службы "Функции Azure" с помощью Сетки событий **настоятельно** рекомендуется использовать [триггер Сетки событий](../azure-functions/functions-bindings-event-grid.md). Универсальный триггер веб-перехватчика используется для демонстрации.

## <a name="prerequisites"></a>предварительным требованиям

* Вам потребуется приложение-функция с [функцией, активируемой HTTP](../azure-functions/functions-create-generic-webhook-triggered-function.md).

## <a name="add-dependencies"></a>Добавление зависимостей

При разработке в .NET [добавьте зависимость](../azure-functions/functions-reference-csharp.md#referencing-custom-assemblies) в функцию для `Microsoft.Azure.EventGrid` [пакета Nuget](https://www.nuget.org/packages/Microsoft.Azure.EventGrid). Пакеты SDK для других языков доступны по ссылке на [общедоступные пакеты SDK](./sdk-overview.md#data-plane-sdks). Эти пакеты содержат модели для собственных типов событий, таких как `EventGridEvent`, `StorageBlobCreatedEventData` и `EventHubCaptureFileCreatedEventData`.

Щелкните ссылку просмотра файлов в службе "Функции Azure" (правая область на портале службы "Функции Azure") и создайте файл с именем project.json. Добавьте следующее содержимое в файл `project.json` и сохраните его:

 ```json
{
  "frameworks": {
    "net46":{
      "dependencies": {
        "Microsoft.Azure.EventGrid": "1.1.0-preview"
      }
    }
   }
}
```

![Добавленный пакет NuGet](./media/receive-events/add-dependencies.png)

## <a name="endpoint-validation"></a>Проверка конечной точки

Сначала нужно обработать событие `Microsoft.EventGrid.SubscriptionValidationEvent`. Каждый раз, когда пользователь подписывается на событие, служба "Сетка событий" отправляет событие проверки в конечную точку с `validationCode` в полезных данных. Конечная точка необходима для передачи этих данных обратно в тело ответа, чтобы [подтвердить, что конечная точка допустима и вы являетесь ее владельцем](security-authentication.md#webhook-event-delivery). При использовании [триггера Сетки событий](../azure-functions/functions-bindings-event-grid.md) вместо функции, активируемой веб-перехватчиком, проверка конечной точки выполняется автоматически. При использовании службы API сторонних производителей (например, [Zapier](https://zapier.com) или [IFTTT](https://ifttt.com/)), возможно, не удастся вывести на экран код проверки программными средствами. Для этих служб можно вручную проверить подписку с помощью URL-адреса проверки, который отправляется в событии проверки подписки. Скопируйте этот URL-адрес в свойство `validationUrl` и отправьте запрос GET через клиент REST или веб-браузер.

Возможность выполнения проверки вручную реализована предварительной версии. Чтобы использовать ее, нужно установить [расширение службы "Сетка событий"](/cli/azure/azure-cli-extensions-list) для [AZ CLI 2.0](/cli/azure/install-azure-cli). Расширение можно установить с помощью `az extension add --name eventgrid`. Если вы используйте REST API, убедитесь, что установлена версия `api-version=2018-05-01-preview`.

Чтобы вывести на экран код проверки программными средствами, используйте следующий код:

```csharp
using System.Net;
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;
using Newtonsoft.Json.Serialization;
using Microsoft.Azure.EventGrid.Models;

class SubscriptionValidationEventData
{
    public string ValidationCode { get; set; }
}

class SubscriptionValidationResponseData
{
    public string ValidationResponse { get; set; }
}

public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
{

    log.Info($"C# HTTP trigger function begun");
    string response = string.Empty;
    const string SubscriptionValidationEvent = "Microsoft.EventGrid.SubscriptionValidationEvent";

    string requestContent = await req.Content.ReadAsStringAsync();
    EventGridEvent[] eventGridEvents = JsonConvert.DeserializeObject<EventGridEvent[]>(requestContent);

    foreach (EventGridEvent eventGridEvent in eventGridEvents)
    {
        JObject dataObject = eventGridEvent.Data as JObject;

        // Deserialize the event data into the appropriate type based on event type
        if (string.Equals(eventGridEvent.EventType, SubscriptionValidationEvent, StringComparison.OrdinalIgnoreCase))
        {
            var eventData = dataObject.ToObject<SubscriptionValidationEventData>();
            log.Info($"Got SubscriptionValidation event data, validation code: {eventData.ValidationCode}, topic: {eventGridEvent.Topic}");
            // Do any additional validation (as required) and then return back the below response
            var responseData = new SubscriptionValidationResponseData();
            responseData.ValidationResponse = eventData.ValidationCode;
            return req.CreateResponse(HttpStatusCode.OK, responseData);
        }
    }

    return req.CreateResponse(HttpStatusCode.OK, response);
}

```

```javascript
var http = require('http');

module.exports = function (context, req) {
    context.log('JavaScript HTTP trigger function begun');
    var validationEventType = "Microsoft.EventGrid.SubscriptionValidationEvent";

    for (var events in req.body) {
        var body = req.body[events];
        // Deserialize the event data into the appropriate type based on event type
        if (body.data && body.eventType == validationEventType) {
            context.log("Got SubscriptionValidation event data, validation code: " + body.data.validationCode + " topic: " + body.topic);

            // Do any additional validation (as required) and then return back the below response
            var code = body.data.validationCode;
            context.res = { status: 200, body: { "ValidationResponse": code } };
        }
    }
    context.done();
};
```

### <a name="test-validation-response"></a>Проверка тестового ответа

Проверьте функцию ответа проверки, вставив в примере событие в поле проверки функции:

```json
[{
  "id": "2d1781af-3a4c-4d7c-bd0c-e34b19da4e66",
  "topic": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "subject": "",
  "data": {
    "validationCode": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6"
  },
  "eventType": "Microsoft.EventGrid.SubscriptionValidationEvent",
  "eventTime": "2018-01-25T22:12:19.4556811Z",
  "metadataVersion": "1",
  "dataVersion": "1"
}]
```

При нажатии кнопки запуска выходные данные должны иметь значение "200 — OK" и `{"ValidationResponse":"512d38b6-c7b8-40c8-89fe-f46f9e9622b6"}` в теле:

![ответ проверки](./media/receive-events/validation-response.png)

## <a name="handle-blob-storage-events"></a>Обработка событий хранилища BLOB-объектов

Теперь можно расширить функцию для обработки `Microsoft.Storage.BlobCreated`:

```cs
using System.Net;
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;
using Newtonsoft.Json.Serialization;
using Microsoft.Azure.EventGrid.Models;

class SubscriptionValidationEventData
{
    public string ValidationCode { get; set; }
}

class SubscriptionValidationResponseData
{
    public string ValidationResponse { get; set; }
}

public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
{
    log.Info($"C# HTTP trigger function begun");
    string response = string.Empty;
    const string SubscriptionValidationEvent = "Microsoft.EventGrid.SubscriptionValidationEvent";
    const string StorageBlobCreatedEvent = "Microsoft.Storage.BlobCreated";

    string requestContent = await req.Content.ReadAsStringAsync();
    EventGridEvent[] eventGridEvents = JsonConvert.DeserializeObject<EventGridEvent[]>(requestContent);

    foreach (EventGridEvent eventGridEvent in eventGridEvents)
    {
        JObject dataObject = eventGridEvent.Data as JObject;

        // Deserialize the event data into the appropriate type based on event type 
        if (string.Equals(eventGridEvent.EventType, SubscriptionValidationEvent, StringComparison.OrdinalIgnoreCase))
        {
            var eventData = dataObject.ToObject<SubscriptionValidationEventData>();
            log.Info($"Got SubscriptionValidation event data, validation code: {eventData.ValidationCode}, topic: {eventGridEvent.Topic}");

            // Do any additional validation (as required) and then return back the below response
            var responseData = new SubscriptionValidationResponseData();
            responseData.ValidationResponse = eventData.ValidationCode;
            return req.CreateResponse(HttpStatusCode.OK, responseData);
        }

        else if (string.Equals(eventGridEvent.EventType, StorageBlobCreatedEvent, StringComparison.OrdinalIgnoreCase))
        {
            var eventData = dataObject.ToObject<StorageBlobCreatedEventData>();
            log.Info($"Got BlobCreated event data, blob URI {eventData.Url}");
        }
    }

    return req.CreateResponse(HttpStatusCode.OK, response);
}

```

```javascript
var http = require('http');

module.exports = function (context, req) {
    context.log('JavaScript HTTP trigger function begun');
    var validationEventType = "Microsoft.EventGrid.SubscriptionValidationEvent";
    var storageBlobCreatedEvent = "Microsoft.Storage.BlobCreated";

    for (var events in req.body) {
        var body = req.body[events];
        // Deserialize the event data into the appropriate type based on event type  
        if (body.data && body.eventType == validationEventType) {
            context.log("Got SubscriptionValidation event data, validation code: " + body.data.validationCode + " topic: " + body.topic);

            // Do any additional validation (as required) and then return back the below response
            var code = body.data.validationCode;
            context.res = { status: 200, body: { "ValidationResponse": code } };
        }

        else if (body.data && body.eventType == storageBlobCreatedEvent) {
            var blobCreatedEventData = body.data;
            context.log("Relaying received blob created event payload:" + JSON.stringify(blobCreatedEventData));
        }
    }
    context.done();
};

```

### <a name="test-blob-created-event-handling"></a>Обработка события создания тестового большого двоичного объекта

Проверьте новую возможность функции, поместив [событие хранилища BLOB-объектов](./event-schema-blob-storage.md#example-event) в поле тестирования и выполнив следующий код:

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/xstoretestaccount",
  "subject": "/blobServices/default/containers/testcontainer/blobs/testfile.txt",
  "eventType": "Microsoft.Storage.BlobCreated",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "api": "PutBlockList",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "eTag": "0x8D4BCC2E4835CD0",
    "contentType": "text/plain",
    "contentLength": 524288,
    "blobType": "BlockBlob",
    "url": "https://example.blob.core.windows.net/testcontainer/testfile.txt",
    "sequencer": "00000000000004420000000000028963",
    "storageDiagnostics": {
      "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

В журнале функции отобразятся выходные данные URL-адреса большого двоичного объекта:

![Журнал выходных данных](./media/receive-events/blob-event-response.png)

Кроме того, вы можете протестировать эти выходные данные, создав учетную запись хранилища BLOB-объектов или учетную запись хранения общего назначения версии 2 (GPv2), [добавив подписку события](../storage/blobs/storage-blob-event-quickstart.md) и задав для конечной точки URL-адрес функции:

![URL-адрес функции](./media/receive-events/function-url.png)

## <a name="handle-custom-events"></a>Обработка пользовательских событий

Наконец, давайте расширим функцию еще раз, чтобы она могла обрабатывать и пользовательские события. Установите флажок для события `Contoso.Items.ItemReceived`. Итоговый код должен выглядеть следующим образом:

```cs
using System.Net;
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;
using Newtonsoft.Json.Serialization;
using Microsoft.Azure.EventGrid.Models;

class SubscriptionValidationEventData
{
    public string ValidationCode { get; set; }
}

class SubscriptionValidationResponseData
{
    public string ValidationResponse { get; set; }
}

class ContosoItemReceivedEventData
{
    public string id { get; set; }
    public string message { get; set; }
    public string time { get; set; }
}

public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
{
    log.Info($"C# HTTP trigger function begun");
    string response = string.Empty;
    const string SubscriptionValidationEvent = "Microsoft.EventGrid.SubscriptionValidationEvent";
    const string StorageBlobCreatedEvent = "Microsoft.Storage.BlobCreated";
    const string CustomTopicEvent = "Contoso.Items.ItemReceived";

    string requestContent = await req.Content.ReadAsStringAsync();
    EventGridEvent[] eventGridEvents = JsonConvert.DeserializeObject<EventGridEvent[]>(requestContent);

    foreach (EventGridEvent eventGridEvent in eventGridEvents)
    {
        JObject dataObject = eventGridEvent.Data as JObject;

        // Deserialize the event data into the appropriate type based on event type
        if (string.Equals(eventGridEvent.EventType, SubscriptionValidationEvent, StringComparison.OrdinalIgnoreCase))
        {
            var eventData = dataObject.ToObject<SubscriptionValidationEventData>();
            log.Info($"Got SubscriptionValidation event data, validation code: {eventData.ValidationCode}, topic: {eventGridEvent.Topic}");
            // Do any additional validation (as required) and then return back the below response
            var responseData = new SubscriptionValidationResponseData();
            responseData.ValidationResponse = eventData.ValidationCode;
            return req.CreateResponse(HttpStatusCode.OK, responseData);
        }

        else if (string.Equals(eventGridEvent.EventType, StorageBlobCreatedEvent, StringComparison.OrdinalIgnoreCase))
        {
            var eventData = dataObject.ToObject<StorageBlobCreatedEventData>();
            log.Info($"Got BlobCreated event data, blob URI {eventData.Url}");
        }

        else if (string.Equals(eventGridEvent.EventType, CustomTopicEvent, StringComparison.OrdinalIgnoreCase))
        {
            var eventData = dataObject.ToObject<ContosoItemReceivedEventData>();
            log.Info($"Got ContosoItemReceived event data, item URI {eventData.id}");
        }
    }

    return req.CreateResponse(HttpStatusCode.OK, response);
}

```

```javascript
var http = require('http');
var t = require('tcomb');

module.exports = function (context, req) {
    context.log('JavaScript HTTP trigger function begun');
    var validationEventType = "Microsoft.EventGrid.SubscriptionValidationEvent";
    var storageBlobCreatedEvent = "Microsoft.Storage.BlobCreated";
    var customEventType = "Contoso.Items.ItemReceived";
    var ContosoItemReceivedEventData = t.struct({
        id: t.Str,
        message: t.Str,
        time: t.Str,
    })

    for (var events in req.body) {
        var body = req.body[events];
        // Deserialize the event data into the appropriate type based on event type
        if (body.data && body.eventType == validationEventType) {
            context.log("Got SubscriptionValidation event data, validation code: " + body.data.validationCode + " topic: " + body.topic);

            // Do any additional validation (as required) and then return back the below response
            var code = body.data.validationCode;
            context.res = { status: 200, body: { "ValidationResponse": code } };
        }

        else if (body.data && body.eventType == storageBlobCreatedEvent) {
            var blobCreatedEventData = body.data;
            context.log("Relaying received blob created event payload:" + JSON.stringify(blobCreatedEventData));
        }

        else if (body.data && body.eventType == customEventType) {
            var payload = new ContosoItemReceivedEventData(body.data);
            context.log("Relaying received custom payload:" + JSON.stringify(payload));
            context.log(payload instanceof ContosoItemReceivedEventData);
        }
    }
    context.done();
};

```

### <a name="test-custom-event-handling"></a>Тестирование обработки пользовательских событий

Наконец, проверьте, может ли расширенная функция теперь обрабатывать ваш тип пользовательского события:

```json
[{
    "subject": "Contoso/foo/bar/items",
    "eventType": "Microsoft.EventGrid.CustomEventType",
    "eventTime": "2017-08-16T01:57:26.005121Z",
    "id": "602a88ef-0001-00e6-1233-1646070610ea",
    "data": { 
            "id": "831e1650-001e-001b-66ab-eeb76e069631",
            "message": "Contoso item Foo",
            "time": "2017-06-26T18:41:00.9584103Z"
            },
    "dataVersion": "",
    "metadataVersion": "1"
}]
```

Кроме того, эту функцию можно протестировать в реальном времени, [отправив пользовательское событие с помощью CURL с портала](./custom-event-quickstart-portal.md) или [опубликовав в пользовательском разделе](./post-to-custom-topic.md), используя любую службу или приложение, которое может выполнять оператор POST в конечной точке, например [Postman](https://www.getpostman.com/). Создайте пользовательский раздел и подписку событий с помощью конечной точки, заданной в качестве URL-адреса функции.

## <a name="next-steps"></a>Дополнительная информация

* Ознакомьтесь со статьей [Пакеты SDK для управления службой "Сетка событий Azure" и публикации в ней](./sdk-overview.md).
* Дополнительные сведения см. в статье [Публикация в пользовательском разделе для службы "Сетка событий Azure"](./post-to-custom-topic.md).
* Попробуйте выполнить инструкции одного из подробных руководств по службе "Сетка событий" и "Функции", например [Автоматическое изменение размера переданных изображений с помощью службы "Сетка событий"](resize-images-on-storage-blob-upload-event.md).

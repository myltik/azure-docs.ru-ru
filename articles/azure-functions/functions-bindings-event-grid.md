---
title: Триггер службы "Сетка событий" для службы "Функции Azure"
description: Узнайте, как обрабатывать события службы "Сетка событий" в службе "Функции Azure".
services: functions
documentationcenter: na
author: tdykstra
manager: cfowler
editor: ''
tags: ''
keywords: ''
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 01/26/2018
ms.author: tdykstra
ms.openlocfilehash: a2d8f66b0364535cbb7e8cadd8067dd8f7facb2c
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/03/2018
---
# <a name="event-grid-trigger-for-azure-functions"></a>Триггер службы "Сетка событий" для службы "Функции Azure"

В этом руководстве показано, как обрабатывать события службы [Сетка событий](../event-grid/overview.md) в службе "Функции Azure".

Служба "Сетка событий" — это служба Azure, которая отправляет HTTP-запросы для уведомления о событиях, которые происходят в *издателях*. Издатель — это служба или ресурс, в котором происходит событие. Например, учетная запись хранения больших двоичных объектов Azure является издателем, а отправка или удаление большого двоичного объекта — это событие. Некоторые [службы Azure имеют встроенную поддержку публикации событий в службу "Сетка событий"](../event-grid/overview.md#event-sources). 

*Обработчики* событий получают и обрабатывают события. Служба "Функции Azure" является одной из нескольких [служб Azure, в которых есть встроенная поддержка обработки событий Сетки событий](../event-grid/overview.md#event-handlers). В этой статье вы узнаете, как использовать триггер службы "Сетка событий" для вызова функции при получении события из Сетки событий.

При желании можно использовать триггер HTTP для обработки событий службы "Сетка событий". Дополнительные сведения см. в разделе [Использование триггера HTTP в качестве триггера службы "Сетка событий"](#use-an-http-trigger-as-an-event-grid-trigger) этой статьи.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages"></a>Пакеты

Триггер Сетки событий предоставляется в пакете NuGet [Microsoft.Azure.WebJobs.Extensions.EventGrid](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventGrid). Исходный код для пакета находится в репозитории GitHub [azure-functions-eventgrid-extension](https://github.com/Azure/azure-functions-eventgrid-extension).

<!--
If you want to bind to the `Microsoft.Azure.EventGrid.Models.EventGridEvent` type instead of `JObject`, install the [Microsoft.Azure.EventGrid](https://www.nuget.org/packages/Microsoft.Azure.EventGrid) package.
-->

[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="example"></a>Пример

Просмотрите пример триггера Сетки событий для конкретного языка:

* [C#](#c-example)
* [Скрипт C# (CSX)](#c-script-example)
* [JavaScript](#javascript-example)

Пример триггера HTTP см. в разделе [Использование триггера HTTP в качестве триггера службы "Сетка событий"](#use-an-http-trigger-as-an-event-grid-trigger) в этой статье.

### <a name="c-example"></a>Пример C#

В следующем примере показана [функция C#](functions-dotnet-class-library.md), которая выполняет привязку к `JObject`:

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Azure.WebJobs.Extensions.EventGrid;
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;

namespace Company.Function
{
    public static class EventGridTriggerCSharp
    {
        [FunctionName("EventGridTriggerCSharp")]
        public static void Run([EventGridTrigger]JObject eventGridEvent, TraceWriter log)
        {
            log.Info(eventGridEvent.ToString(Formatting.Indented));
        }
    }
}
```

<!--
The following example shows a [C# function](functions-dotnet-class-library.md) that binds to `EventGridEvent`:

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Azure.WebJobs.Extensions.EventGrid;

namespace Company.Function
{
    public static class EventGridTriggerCSharp
    {
        [FunctionName("EventGridTest")]
            public static void EventGridTest([EventGridTrigger] Microsoft.Azure.EventGrid.Models.EventGridEvent eventGridEvent, TraceWriter log)
        {
            log.Info("C# Event Grid function processed a request.");
            log.Info($"Subject: {eventGridEvent.Subject}");
            log.Info($"Time: {eventGridEvent.EventTime}");
            log.Info($"Data: {eventGridEvent.Data.ToString()}");
        }
    }
}
```
-->

См. дополнительные сведения о [пакетах](#packages), [атрибутах](#attributes), [конфигурации](#configuration) и [использовании](#usage).

### <a name="c-script-example"></a>Пример сценария C#

В следующем примере показаны привязка триггера в файле *function.json* и [функция сценария C#](functions-reference-csharp.md), которая использует эту привязку.

Данные привязки в файле *function.json*:

```json
{
  "bindings": [
    {
      "type": "eventGridTrigger",
      "name": "eventGridEvent",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

Ниже приведен код сценария C#, который выполняет привязку к `JObject`:

```cs
#r "Newtonsoft.Json"

using Newtonsoft.Json;
using Newtonsoft.Json.Linq;

public static void Run(JObject eventGridEvent, TraceWriter log)
{
    log.Info(eventGridEvent.ToString(Formatting.Indented));
}
```

<!--
Here's C# script code that binds to `EventGridEvent`:

```csharp
#r "Newtonsoft.Json"
#r "Microsoft.Azure.WebJobs.Extensions.EventGrid"
#r "Microsoft.Azure.EventGrid"

using Microsoft.Azure.WebJobs.Extensions.EventGrid;
Using Microsoft.Azure.EventGrid.Models;

public static void Run(EventGridEvent eventGridEvent, TraceWriter log)
{
    log.Info("C# Event Grid function processed a request.");
    log.Info($"Subject: {eventGridEvent.Subject}");
    log.Info($"Time: {eventGridEvent.EventTime}");
    log.Info($"Data: {eventGridEvent.Data.ToString()}");
}
```
-->

См. дополнительные сведения о [пакетах](#packages), [атрибутах](#attributes), [конфигурации](#configuration) и [использовании](#usage).

### <a name="javascript-example"></a>Пример JavaScript

В следующем примере показана привязка триггера в файле *function.json* и [функция JavaScript](functions-reference-node.md), которая использует привязку.

Данные привязки в файле *function.json*:

```json
{
  "bindings": [
    {
      "type": "eventGridTrigger",
      "name": "eventGridEvent",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

Ниже показан код JavaScript.

```javascript
module.exports = function (context, eventGridEvent) {
    context.log("JavaScript Event Grid function processed a request.");
    context.log("Subject: " + eventGridEvent.subject);
    context.log("Time: " + eventGridEvent.eventTime);
    context.log("Data: " + JSON.stringify(eventGridEvent.data));
    context.done();
};
```
     
## <a name="attributes"></a>Атрибуты

В [библиотеках классов C#](functions-dotnet-class-library.md) используйте атрибут [EventGridTrigger](https://github.com/Azure/azure-functions-eventgrid-extension/blob/master/src/EventGridExtension/EventGridTriggerAttribute.cs).

Вот как выглядит атрибут `EventGridTrigger` в сигнатуре метода:

```csharp
[FunctionName("EventGridTest")]
public static void EventGridTest([EventGridTrigger] JObject eventGridEvent, TraceWriter log)
{
    ...
}
 ```

Полный пример см. в разделе [Пример C#](#c-example).

## <a name="configuration"></a>Параметр Configuration

В следующей таблице описываются свойства конфигурации привязки, которые задаются в файле *function.json*. В атрибуте `EventGridTrigger` не задаются параметры или свойства конструкции.

|свойство function.json |ОПИСАНИЕ|
|---------|---------|----------------------|
| **type** | Обязательное. Необходимо задать значение `eventGridTrigger`. |
| **direction** | Обязательное. Необходимо задать значение `in`. |
| **name** | Обязательное. Имя переменной, используемой в коде функции, для параметра, получающего данные события. |

## <a name="usage"></a>Использование

В функциях C# и F# для триггера Сетки событий можно использовать следующие типы параметров:

* `JObject`
* `string`
* `Microsoft.Azure.WebJobs.Extensions.EventGrid.EventGridEvent` — определяет свойства для полей, которые являются общими для всех типов событий. **Этот тип является устаревшим**, но его замена еще не опубликована в NuGet.

Для функций JavaScript параметр, названный по свойству `name` в файле *function.json*, имеет ссылку на объект события.

## <a name="event-schema"></a>Схема событий

Данные для события службы "Сетка событий" получаются в качестве объекта JSON в теле HTTP-запроса. JSON должен быть аналогичным приведенному ниже:

```json
[{
  "topic": "/subscriptions/{subscriptionid}/resourceGroups/eg0122/providers/Microsoft.Storage/storageAccounts/egblobstore",
  "subject": "/blobServices/default/containers/{containername}/blobs/blobname.jpg",
  "eventType": "Microsoft.Storage.BlobCreated",
  "eventTime": "2018-01-23T17:02:19.6069787Z",
  "id": "{guid}",
  "data": {
    "api": "PutBlockList",
    "clientRequestId": "{guid}",
    "requestId": "{guid}",
    "eTag": "0x8D562831044DDD0",
    "contentType": "application/octet-stream",
    "contentLength": 2248,
    "blobType": "BlockBlob",
    "url": "https://egblobstore.blob.core.windows.net/{containername}/blobname.jpg",
    "sequencer": "000000000000272D000000000003D60F",
    "storageDiagnostics": {
      "batchId": "{guid}"
    }
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

Показанный пример является массивом из одного элемента. Сетка событий всегда отправляет массив, который может содержать более одного события. Среда выполнения вызывает функцию один раз для каждого элемента массива.

Свойства верхнего уровня в данных JSON события одинаковы для всех типов событий, тогда как содержимое свойства `data` определено для каждого типа события. Пример показан для события хранилища BLOB-объектов.

Описания распространенных и связанных с событием свойств см. в разделе [Свойства события](../event-grid/event-schema.md#event-properties) в документации по службе "Сетка событий".

Тип `EventGridEvent` определяет только свойства верхнего уровня. Свойство `Data` имеет значение `JObject`. 

## <a name="create-a-subscription"></a>Создание подписки

Чтобы начать получать HTTP-запросы Сетки событий, создайте подписку службы "Сетка событий", которая указывает URL-адрес конечной точки, вызывающей функцию.

### <a name="azure-portal"></a>Портал Azure

Для функций, разрабатываемых на портале Azure с использованием триггера службы "Сетка событий", выберите **Добавить подписку сетки событий**.

![Создание подписки на портале](media/functions-bindings-event-grid/portal-sub-create.png)

Когда вы щелкните эту ссылку, откроется страница **создания подписки на событие** с автоматически заполненным URL-адресом конечной точки.

![Автоматически заполненный URL-адрес конечной точки](media/functions-bindings-event-grid/endpoint-url.png)

Дополнительные сведения о создании подписок с помощью портала Azure см. в разделе [Создание и перенаправление пользовательских событий с помощью портала Azure и службы "Сетка событий"](../event-grid/custom-event-quickstart-portal.md) документации по службе "Сетка событий".

### <a name="azure-cli"></a>Инфраструктура CLI Azure

Чтобы создать подписку с помощью [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest), используйте команду [az eventgrid event-subscription create](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az_eventgrid_event_subscription_create).

Для команды нужен URL-адрес конечной точки, который вызывает функцию. В следующем примере показан шаблон URL-адреса:

```
https://{functionappname}.azurewebsites.net/admin/extensions/EventGridExtensionConfig?functionName={functionname}&code={systemkey}
```

Системный ключ — это ключ авторизации, который необходимо включить в URL-адрес конечной точки для триггера службы "Сетка событий". В следующем разделе показано, как получить системный ключ.

Ниже приведен пример, который подписывается на учетную запись хранения больших двоичных объектов (с заполнителем для системного ключа):

```azurecli
az eventgrid resource event-subscription create -g myResourceGroup \
--provider-namespace Microsoft.Storage --resource-type storageAccounts \
--resource-name glengablobstorage --name myFuncSub  \
--included-event-types Microsoft.Storage.BlobCreated \
--subject-begins-with /blobServices/default/containers/images/blobs/ \
--endpoint https://glengastorageevents.azurewebsites.net/admin/extensions/EventGridExtensionConfig?functionName=imageresizefunc&code=LUwlnhIsNtSiUjv/sNtSiUjvsNtSiUjvsNtSiUjvYb7XDonDUr/RUg==
```

Дополнительные сведения о создании подписки см. в разделе [Примеры использования хранилища больших двоичных объектов](../storage/blobs/storage-blob-event-quickstart.md#subscribe-to-your-storage-account) или других коротких руководствах по использованию службы "Сетка событий".

### <a name="get-the-system-key"></a>Получение системного ключа

Системный ключ можно получить с помощью следующего API-интерфейса (HTTP GET):

```
http://{functionappname}.azurewebsites.net/admin/host/systemkeys/eventgridextensionconfig_extension?code={adminkey}
```

Это API администратора, поэтому для него нужен [ключ администратора](functions-bindings-http-webhook.md#authorization-keys). Не путайте системный ключ (для вызова функции триггера службы "Сетка событий") с ключом администратора (для выполнения административных задач в приложении-функции). Во время оформления подписки на раздел службы "Сетка событий" необходимо использовать системный ключ.

Ниже приведен пример ответа, который предоставляет системный ключ:

```
{
  "name": "eventgridextensionconfig_extension",
  "value": "{the system key for the function}",
  "links": [
    {
      "rel": "self",
      "href": "{the URL for the function, without the system key}"
    }
  ]
}
```

Дополнительные сведения см. в разделе [Ключи авторизации](functions-bindings-http-webhook.md#authorization-keys) в справочнике по триггерам HTTP. 

Кроме того, вы можете отправить HTTP-запрос PUT, чтобы самостоятельно указать значение ключа.

## <a name="local-testing-with-requestbin"></a>Локальное тестирование с помощью RequestBin

Чтобы протестировать триггер службы "Сетка событий" локально, необходимо отправить HTTP-запросы службы "Сетка событий", переданные из своего источника в облаке, на локальный компьютер. Для этого можно записать запросы в сети и вручную отправить их на локальный компьютер:

2. [Создайте конечную точку RequestBin](#create-a-RequestBin-endpoint).
3. [Создайте подписку службы "Сетка событий"](#create-an-event-grid-subscription), которая отправляет события в конечную точку RequestBin.
4. [Создайте запрос](#generate-a-request) и скопируйте текст запроса с сайта RequestBin.
5. [Вручную вставьте запрос](#manually-post-the-request) в URL-адрес localhost функции триггера службы "Сетка событий".

Закончив тестирование, можно использовать ту же подписку для рабочей среды, обновив конечную точку. Используйте команду Azure CLI [az eventgrid event-subscription update](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az_eventgrid_event_subscription_update).

### <a name="create-a-requestbin-endpoint"></a>Создание конечной точки RequestBin

RequestBin является средством с открытым исходным кодом, которое принимает HTTP-запросы и показывает текст запроса. Служба "Сетка событий Azure" взаимодействует с URL-адресом http://requestb.in особым образом. Чтобы упростить тестирование, Сетка событий отправляет события в URL-адрес RequestBin, не требуя правильного ответа на запросы подтверждения подписки. Два других средства тестирования обрабатываются точно так же: http://webhookinbox.com и http://hookbin.com.

RequestBin не предназначено для использования с высокой пропускной способностью. Если вы одновременно отправляете несколько событий, в средстве могут отобразиться не все события.

Создайте конечную точку.

![Создание конечной точки RequestBin](media/functions-bindings-event-grid/create-requestbin.png)

Скопируйте URL-адрес конечной точки.

![Копирование конечной точки RequestBin](media/functions-bindings-event-grid/save-requestbin-url.png)

### <a name="create-an-event-grid-subscription"></a>Создание подписки Сетки событий

Создайте подписку службы "Сетка событий" типа, который необходимо протестировать, и присвойте ее конечной точке RequestBin. Дополнительные сведения о создании подписки см. в разделе [Создание подписки](#create-a-subscription) ранее в этой статье.

### <a name="generate-a-request"></a>Создание запроса

Активируйте событие, которое будет создавать HTTP-трафик в конечную точку RequestBin.  Например, если вы создали подписку хранилища BLOB-объектов, отправьте или удалите большой двоичный объект. Скопируйте текст запроса, когда запрос отобразится на странице RequestBin.

Сначала будет получен запрос на подтверждение подписки. Игнорируйте такие запросы и скопируйте запрос события.

![Копирование текста запроса из RequestBin](media/functions-bindings-event-grid/copy-request-body.png)

### <a name="manually-post-the-request"></a>Публикация запроса вручную

Запустите функцию службы "Сетка событий" локально.

Используйте такие средства, как [Postman](https://www.getpostman.com/) или [cURL](https://curl.haxx.se/docs/httpscripting.html), чтобы создать HTTP-запрос POST:

* Задайте заголовок `Content-Type: application/json`.
* Задайте заголовок `aeg-event-type: Notification`.
* Вставьте данные RequestBin в текст запроса. 
* Опубликуйте по URL-адресу функции триггера службы "Сетка событий", используя следующий шаблон:

```
http://localhost:7071/admin/extensions/EventGridExtensionConfig?functionName={functionname}
``` 

Для параметра `functionName` нужно указать имя, заданное в атрибуте `FunctionName`.

На следующих снимках экрана показаны заголовки и текст запроса в Postman:

![Заголовки в Postman](media/functions-bindings-event-grid/postman2.png)

![Текст запроса в Postman](media/functions-bindings-event-grid/postman.png)

Функция триггера службы "Сетка событий" выполняет и показывает журналы, похожие на приведенные ниже:

![Пример журнала функций триггера службы "Сетка событий"](media/functions-bindings-event-grid/eg-output.png)

## <a name="local-testing-with-ngrok"></a>Локальное тестирование с помощью ngrok

Другим способом локально проверить триггер Сетки событий является автоматизация HTTP-подключения между Интернетом и компьютером разработчика. Это можно сделать с помощью средства с открытым исходным кодом, которое называется [ngrok](https://ngrok.com/):

3. [Создайте конечную точку ngrok](#create-an-ngrok-endpoint).
4. [Запустите функцию триггера службы "Сетка событий"](#run-the-event-grid-trigger-function).
5. [Создайте подписку службы "Сетка событий"](#create-a-subscription), которая отправляет события в конечную точку ngrok.
6. [Активируйте событие](#trigger-an-event).

Закончив тестирование, можно использовать ту же подписку для рабочей среды, обновив конечную точку. Используйте команду Azure CLI [az eventgrid event-subscription update](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az_eventgrid_event_subscription_update).

### <a name="create-an-ngrok-endpoint"></a>Создание конечной точки ngrok

Скачайте *ngrok.exe* из [ngrok](https://ngrok.com/) и выполните следующую команду:

```
ngrok http -host-header=localhost 7071
```

Параметр -host-header необходим, так как среда выполнения функций ожидает запросов от localhost при выполнении на локальном узле. 7071 — номер порта по умолчанию, если среда выполнения запущена локально.

Команда создает выходные данные следующего вида:

```
Session Status                online
Version                       2.2.8
Region                        United States (us)
Web Interface                 http://127.0.0.1:4040
Forwarding                    http://263db807.ngrok.io -> localhost:7071
Forwarding                    https://263db807.ngrok.io -> localhost:7071

Connections                   ttl     opn     rt1     rt5     p50     p90
                              0       0       0.00    0.00    0.00    0.00
```

Для подписки службы "Сетка событий" используется URL-адрес https://{поддомен}.ngrok.io.

### <a name="run-the-event-grid-trigger-function"></a>Запуск функций триггера службы "Сетка событий"

Служба "Сетка событий" не обрабатывает URL-адрес ngrok особым образом, поэтому при создании подписки функция должна выполняться локально. В противном случае ответ проверки не отправляется и происходит сбой при создании подписки.

### <a name="create-a-subscription"></a>Создание подписки

Создайте подписку службы "Сетка событий" типа, который необходимо протестировать, и присвойте ей конечную точку ngrok, используя следующий шаблон:

```
https://{subdomain}.ngrok.io/admin/extensions/EventGridExtensionConfig?functionName={functionname}
``` 

Для параметра `functionName` нужно указать имя, заданное в атрибуте `FunctionName`.

Ниже приведен пример с использованием Azure CLI:

```
az eventgrid event-subscription create --resource-id /subscriptions/aeb4b7cb-b7cb-b7cb-b7cb-b7cbb6607f30/resourceGroups/eg0122/providers/Microsoft.Storage/storageAccounts/egblobstor0122 --name egblobsub0126 --endpoint https://263db807.ngrok.io/admin/extensions/EventGridExtensionConfig?functionName=EventGridTrigger
```

Дополнительные сведения о создании подписки см. в разделе [Создание подписки](#create-a-subscription) ранее в этой статье.

### <a name="trigger-an-event"></a>Активация события

Активируйте событие, которое будет создавать HTTP-трафик в конечную точку ngrok.  Например, если вы создали подписку хранилища BLOB-объектов, отправьте или удалите большой двоичный объект.

Функция триггера службы "Сетка событий" выполняет и показывает журналы, похожие на приведенные ниже:

![Пример журнала функций триггера службы "Сетка событий"](media/functions-bindings-event-grid/eg-output.png)

## <a name="use-an-http-trigger-as-an-event-grid-trigger"></a>Использование триггера HTTP в качестве триггера службы "Сетка событий"

События службы "Сетка событий" получаются в качестве HTTP-запросов, поэтому их можно обрабатывать с использованием триггера HTTP, а не триггера службы "Сетка событий". Одна из возможных причин — получение большего контроля над URL-адресом конечной точки, вызывающим функцию. 

При использовании триггера HTTP необходимо написать код действий, которые триггер службы "Сетка событий" выполняет автоматически:

* Отправляет запрос проверки в [запрос подтверждения подписки](../event-grid/security-authentication.md#webhook-event-delivery).
* Вызывает функцию один раз для каждого элемента массива событий, содержащихся в тексте запроса.

Следующий пример кода C# для триггера HTTP имитирует поведение триггера службы "Сетка событий":

```csharp
[FunctionName("HttpTrigger")]
public static async Task<HttpResponseMessage> Run(
    [HttpTrigger(AuthorizationLevel.Anonymous, "post")]HttpRequestMessage req,
    TraceWriter log)
{
    log.Info("C# HTTP trigger function processed a request.");

    var messages = await req.Content.ReadAsAsync<JArray>();

    // If the request is for subscription validation, send back the validation code.
    if (messages.Count > 0 && string.Equals((string)messages[0]["eventType"], 
        "Microsoft.EventGrid.SubscriptionValidationEvent", 
        System.StringComparison.OrdinalIgnoreCase))
    {
        log.Info("Validate request received");
        return req.CreateResponse<object>(new
        {
            validationResponse = messages[0]["data"]["validationCode"]
        });
    }

    // The request is not for subscription validation, so it's for one or more events.
    foreach (JObject message in messages)
    {
        // Handle one event.
        EventGridEvent eventGridEvent = message.ToObject<EventGridEvent>();
        log.Info($"Subject: {eventGridEvent.Subject}");
        log.Info($"Time: {eventGridEvent.EventTime}");
        log.Info($"Event data: {eventGridEvent.Data.ToString()}");
    }

    return req.CreateResponse(HttpStatusCode.OK);
}
```

Следующий пример кода JavaScript для триггера HTTP имитирует поведение триггера службы "Сетка событий":

```javascript
module.exports = function (context, req) {
    context.log('JavaScript HTTP trigger function processed a request.');

    var messages = req.body;
    // If the request is for subscription validation, send back the validation code.
    if (messages.length > 0 && messages[0].eventType == "Microsoft.EventGrid.SubscriptionValidationEvent") {
        context.log('Validate request received');
        context.res = { status: 200, body: messages[0].data.validationCode }
    }
    else {
        // The request is not for subscription validation, so it's for one or more events.
        for (var i = 0; i < messages.length; i++) {
            // Handle one event.
            var message = messages[i];
            context.log('Subject: ' + message.subject);
            context.log('Time: ' + message.eventTime);
            context.log('Data: ' + JSON.stringify(message.data));
        }
    }
    context.done();
};
```

Код обработки событий переходит в цикл через массив `messages`.

Дополнительные сведения об URL-адресе, используемом для вызова функции, выполняемой локально или в Azure, см. в [справочной документации по привязке триггера HTTP](functions-bindings-http-webhook.md). 

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Основные понятия триггеров и привязок в Функциях Azure](functions-triggers-bindings.md)

> [!div class="nextstepaction"]
> [Общие сведения о службе "Сетка событий Azure"](../event-grid/overview.md)

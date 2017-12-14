---
title: "Привязка Twilio для службы \"Функции Azure\""
description: "Узнайте, как использовать привязки Twilio с функциями Azure."
services: functions
documentationcenter: na
author: wesmc7777
manager: cfowler
editor: 
tags: 
keywords: "функции azure, функции, обработка событий, динамические вычисления, независимая архитектура"
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 11/21/2017
ms.author: wesmc
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ae97045c27f3ad8b62e7798b2060ea59ccd66ac5
ms.sourcegitcommit: cfd1ea99922329b3d5fab26b71ca2882df33f6c2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/30/2017
---
# <a name="twilio-binding-for-azure-functions"></a>Привязка Twilio для службы "Функции Azure"

В этой статье описано, как отправлять текстовые сообщения с помощью привязок [Twilio](https://www.twilio.com/) в службу "Функции Azure". Служба "Функции Azure" поддерживают выходные привязки для Twilio.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="example"></a>Пример

Языковой пример см. в разделах:

* [Предкомпилированный код C#](#c-example)
* [Сценарий C#](#c-script-example)
* [JavaScript](#javascript-example)

### <a name="c-example"></a>Пример C#

В следующем примере показана [предкомпилированная функция C#](functions-dotnet-class-library.md), которая отправляет текстовое сообщение при активации сообщением из очереди.

```cs
[FunctionName("QueueTwilio")]
[return: TwilioSms(AccountSidSetting = "TwilioAccountSid", AuthTokenSetting = "TwilioAuthToken", From = "+1425XXXXXXX" )]
public static SMSMessage Run(
    [QueueTrigger("myqueue-items", Connection = "AzureWebJobsStorage")] JObject order,
    TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {order}");

    var message = new SMSMessage()
    {
        Body = $"Hello {order["name"]}, thanks for your order!",
        To = order["mobileNumber"].ToString()
    };

    return message;
}
```

В этом примере используется `TwilioSms` атрибут с помощью возвращаемого значения метода. Альтернативой является использование атрибута с параметрами `out SMSMessage`, `ICollector<SMSMessage>` или `IAsyncCollector<SMSMessage>`.

### <a name="c-script-example"></a>Пример сценария C#

В следующем примере показаны выходная привязка Twilio*function.json* и [функция сценария C#](functions-reference-csharp.md), которая использует эту привязку. Эта функция использует параметр `out` для отправки текстового сообщения.

Данные привязки в файле *function.json*:

Пример файла function.json:

```json
{
  "type": "twilioSms",
  "name": "message",
  "accountSid": "TwilioAccountSid",
  "authToken": "TwilioAuthToken",
  "to": "+1704XXXXXXX",
  "from": "+1425XXXXXXX",
  "direction": "out",
  "body": "Azure Functions Testing"
}
```

Ниже приведен код сценария C#:

```cs
#r "Newtonsoft.Json"
#r "Twilio.Api"

using System;
using Newtonsoft.Json;
using Twilio;

public static void Run(string myQueueItem, out SMSMessage message,  TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");

    // In this example the queue item is a JSON string representing an order that contains the name of a 
    // customer and a mobile number to send text updates to.
    dynamic order = JsonConvert.DeserializeObject(myQueueItem);
    string msg = "Hello " + order.name + ", thank you for your order.";

    // Even if you want to use a hard coded message and number in the binding, you must at least 
    // initialize the SMSMessage variable.
    message = new SMSMessage();

    // A dynamic message can be set instead of the body in the output binding. In this example, we use 
    // the order information to personalize a text message to the mobile number provided for
    // order status updates.
    message.Body = msg;
    message.To = order.mobileNumber;
}
```

Вы не можете использовать параметры вывода в асинхронном коде. Ниже приведен пример асинхронного кода сценария C#:

```cs
#r "Newtonsoft.Json"
#r "Twilio.Api"

using System;
using Newtonsoft.Json;
using Twilio;

public static async Task Run(string myQueueItem, IAsyncCollector<SMSMessage> message,  TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");

    // In this example the queue item is a JSON string representing an order that contains the name of a 
    // customer and a mobile number to send text updates to.
    dynamic order = JsonConvert.DeserializeObject(myQueueItem);
    string msg = "Hello " + order.name + ", thank you for your order.";

    // Even if you want to use a hard coded message and number in the binding, you must at least 
    // initialize the SMSMessage variable.
    SMSMessage smsText = new SMSMessage();

    // A dynamic message can be set instead of the body in the output binding. In this example, we use 
    // the order information to personalize a text message to the mobile number provided for
    // order status updates.
    smsText.Body = msg;
    smsText.To = order.mobileNumber;

    await message.AddAsync(smsText);
}
```

### <a name="javascript-example"></a>Пример JavaScript

В следующем примере показана выходная привязка Twilio в файле *function.json* и функции [JavaScript](functions-reference-node.md), которая использует привязку.

Данные привязки в файле *function.json*:

Пример файла function.json:

```json
{
  "type": "twilioSms",
  "name": "message",
  "accountSid": "TwilioAccountSid",
  "authToken": "TwilioAuthToken",
  "to": "+1704XXXXXXX",
  "from": "+1425XXXXXXX",
  "direction": "out",
  "body": "Azure Functions Testing"
}
```

Ниже показан код JavaScript.

```javascript
module.exports = function (context, myQueueItem) {
    context.log('Node.js queue trigger function processed work item', myQueueItem);

    // In this example the queue item is a JSON string representing an order that contains the name of a 
    // customer and a mobile number to send text updates to.
    var msg = "Hello " + myQueueItem.name + ", thank you for your order.";

    // Even if you want to use a hard coded message and number in the binding, you must at least 
    // initialize the message binding.
    context.bindings.message = {};

    // A dynamic message can be set instead of the body in the output binding. In this example, we use 
    // the order information to personalize a text message to the mobile number provided for
    // order status updates.
    context.bindings.message = {
        body : msg,
        to : myQueueItem.mobileNumber
    };

    context.done();
};
```

## <a name="attributes"></a>Атрибуты

Для [предкомпилированной функции C#](functions-dotnet-class-library.md) используйте атрибут [TwilioSms](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.Twilio/TwilioSMSAttribute.cs), который определен в пакете NuGet [Microsoft.Azure.WebJobs.Extensions.Twilio](http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Twilio).

Дополнительные сведения о настройке свойств атрибутов см. в разделе [Конфигурация](#configuration). Ниже приведен пример атрибута `TwilioSms` в сигнатуре метода:

```csharp
[FunctionName("QueueTwilio")]
[return: TwilioSms(
    AccountSidSetting = "TwilioAccountSid", 
    AuthTokenSetting = "TwilioAuthToken", 
    From = "+1425XXXXXXX" )]
public static SMSMessage Run(
    [QueueTrigger("myqueue-items", Connection = "AzureWebJobsStorage")] JObject order,
    TraceWriter log)
{
    ...
}
 ```

Полный пример см. в разделе [Пример C#](#c-example).

## <a name="configuration"></a>Конфигурация

В следующей таблице описываются свойства конфигурации привязки, которые задаются в файле *function.json* и атрибуте `TwilioSms`.

|свойство function.json | Свойство атрибута |Описание|
|---------|---------|----------------------|
|**type**|| Нужно задать значение `twilioSms`.|
|**direction**|| Нужно задать значение `out`.|
|**name**|| Имя переменной, используемое в коде функции для текстового SMS-сообщения Twilio. |
|**accountSid**|**accountSid**| Требуемое значение: имя параметра приложения, содержащего идентификатор безопасности учетной записи Twilio.|
|**authToken**|**authToken**| Требуемое значение: имя параметра приложения, содержащего токен проверки подлинности Twilio.|
|**to**|**To**| Требуемое значение: номер телефона, на который отправляется текст SMS-сообщения.|
|**from**|**from**| Требуемое значение: номер телефона, с которого отправляется текст SMS-сообщения.|
|**body**|**Текст**| Это значение можно использовать для жесткого кодирования текстового SMS-сообщения, если его не нужно задавать динамически в коде функции. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Основные понятия триггеров и привязок в Функциях Azure](functions-triggers-bindings.md)



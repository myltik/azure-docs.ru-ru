---
title: "Привязка Twilio для Функций Azure | Документация Майкрософт"
description: "Узнайте, как использовать привязки Twilio с функциями Azure."
services: functions
documentationcenter: na
author: wesmc7777
manager: erikre
editor: 
tags: 
keywords: "функции azure, функции, обработка событий, динамические вычисления, независимая архитектура"
ms.assetid: a60263aa-3de9-4e1b-a2bb-0b52e70d559b
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 10/20/2016
ms.author: wesmc
translationtype: Human Translation
ms.sourcegitcommit: 96f253f14395ffaf647645176b81e7dfc4c08935
ms.openlocfilehash: 29fa4620885ec787f362966f732510fded600d53


---
# <a name="azure-functions-twilio-output-binding"></a>Выходная привязка Twilio для функций Azure
[!INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

В этой статье описано, как настроить привязки Twilio и использовать их с функциями Azure. 

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

Функции Azure поддерживают выходные привязки Twilio, позволяя вашим функциям отправлять текстовые SMS-сообщения с помощью нескольких строк кода и учетной записи [Twilio](https://www.twilio.com/). 

## <a name="functionjson-for-azure-notification-hub-output-binding"></a>Файл function.json для выходной привязки центра уведомлений Azure
Файл function.json содержит следующие свойства:

* `name` — имя переменной, используемое в коде функции для текстового SMS-сообщения Twilio.
* `type` — для этого значения следует указать значение *"twilioSms"*.
* `accountSid` — для этого значения следует указать имя параметра приложения, содержащего идентификатор безопасности учетной записи Twilio.
* `authToken` — для этого значения следует указать имя параметра приложения, содержащего маркер проверки подлинности Twilio.
* `to` — для этого значения следует указать номер телефона, на который отправляется текст SMS-сообщения.
* `from` — для этого значения следует указать номер телефона, с которого отправляется текст SMS-сообщения.
* `direction` — для этого свойства необходимо задать значение *out*.
* `body` — это значение можно использовать для жесткого кодирования текстового SMS-сообщения, если его не нужно задавать динамически в коде функции. 

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


## <a name="example-c-queue-trigger-with-twilio-output-binding"></a>Пример триггера очереди C# с выходной привязкой Twilio
#### <a name="synchronous"></a>Синхронный
Этот пример синхронного кода для триггера очереди службы хранилища Azure использует выходной параметр для отправки текстового сообщения клиенту, разместившему заказ.

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

#### <a name="asynchronous"></a>Асинхронный
Этот пример асинхронного кода для триггера очереди службы хранилища Azure отправляет текстовое сообщение клиенту, разместившему заказ.

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

## <a name="example-nodejs-queue-trigger-with-twilio-output-binding"></a>Пример триггера очереди Node.js с выходной привязкой Twilio
Этот пример Node.js отправляет текстовое сообщение клиенту, разместившему заказ.

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

## <a name="next-steps"></a>Дальнейшие действия
[!INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)]




<!--HONumber=Nov16_HO3-->



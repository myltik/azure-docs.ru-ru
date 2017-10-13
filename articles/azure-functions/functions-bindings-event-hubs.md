---
title: "Привязки концентраторов событий Функций Azure | Документация Майкрософт"
description: "Узнайте, как использовать привязки концентраторов событий Azure в функциях Azure."
services: functions
documentationcenter: na
author: wesmc7777
manager: cfowler
editor: 
tags: 
keywords: "функции azure, функции, обработка событий, динамические вычисления, независимая архитектура"
ms.assetid: daf81798-7acc-419a-bc32-b5a41c6db56b
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 06/20/2017
ms.author: wesmc
ms.openlocfilehash: 85eb6985ef3579b1b2313db3ce5f91c3471da72f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="azure-functions-event-hubs-bindings"></a>Привязки концентраторов событий функций Azure
[!INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

Здесь объясняется, как настроить и использовать привязки [концентраторов событий Azure](../event-hubs/event-hubs-what-is-event-hubs.md) для Функций Azure.
Функции Azure поддерживают привязки триггера и выходные привязки для концентраторов событий Azure.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

Если вы еще не работали с концентраторами событий Azure, ознакомьтесь с [их обзором](../event-hubs/event-hubs-what-is-event-hubs.md).

<a name="trigger"></a>

## <a name="event-hub-trigger"></a>Триггер концентратора событий
Используйте триггер концентраторов событий Azure для ответа на событие, отправленное в поток событий концентратора событий. Чтобы настроить триггер, необходимо иметь доступ для чтения к концентратору событий.

Триггер концентратора событий для функции использует следующий объект JSON в массиве `bindings` файла function.json:

```json
{
    "type": "eventHubTrigger",
    "name": "<Name of trigger parameter in function signature>",
    "direction": "in",
    "path": "<Name of the event hub>",
    "consumerGroup": "Consumer group to use - see below",
    "connection": "<Name of app setting with connection string - see below>"
}
```

`consumerGroup` — необязательное свойство, которое используется для задания [группы потребителей](../event-hubs/event-hubs-features.md#event-consumers), используемой для подписки на события в концентраторе. Если аргумент опущен, используется группа потребителей `$Default`.  
`connection` — имя параметра приложения, содержащего строку подключения к пространству имен концентратора событий.
Скопируйте эту строку подключения, нажав кнопку **Сведения о подключении** для *пространства имен*, а не сам концентратор событий. Для активации триггера эта строка подключения должна обладать, по крайней мере, правами на чтение.

[Дополнительные параметры](https://github.com/Azure/azure-webjobs-sdk-script/wiki/host.json) можно указать в файле host.json для дальнейшей настройки триггеров концентратора событий.  

<a name="triggerusage"></a>

## <a name="trigger-usage"></a>Использование триггера
При активации функции триггера концентратора событий сообщение, вызвавшее активацию, передается в функцию в качестве строки.

<a name="triggersample"></a>

## <a name="trigger-sample"></a>Пример триггера
Предположим, что у вас есть следующий триггер концентраторов событий в массиве `bindings` файла function.json:

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "path": "MyEventHub",
  "connection": "myEventHubReadConnectionString"
}
```

Ознакомьтесь с примером для конкретного языка, записывающим текст сообщения триггера концентратора событий.

* [C#](#triggercsharp)
* [F#](#triggerfsharp)
* [Node.js](#triggernodejs)

<a name="triggercsharp"></a>

### <a name="trigger-sample-in-c"></a>Пример триггера на языке C# #

```cs
using System;

public static void Run(string myEventHubMessage, TraceWriter log)
{
    log.Info($"C# Event Hub trigger function processed a message: {myEventHubMessage}");
}
```

Вы также можете получить событие как объект [EventData](/dotnet/api/microsoft.servicebus.messaging.eventdata), который предоставляет доступ к метаданным события.

```cs
#r "Microsoft.ServiceBus"
using System.Text;
using Microsoft.ServiceBus.Messaging;

public static void Run(EventData myEventHubMessage, TraceWriter log)
{
    log.Info($"{Encoding.UTF8.GetString(myEventHubMessage.GetBytes())}");
}
```

Чтобы получить пакет событий, измените сигнатуру метода на `string[]` или `EventData[]`.

```cs
public static void Run(string[] eventHubMessages, TraceWriter log)
{
    foreach (var message in eventHubMessages)
    {
        log.Info($"C# Event Hub trigger function processed a message: {message}");
    }
}
```

<a name="triggerfsharp"></a>

### <a name="trigger-sample-in-f"></a>Пример триггера на языке F# #

```fsharp
let Run(myEventHubMessage: string, log: TraceWriter) =
    log.Info(sprintf "F# eventhub trigger function processed work item: %s" myEventHubMessage)
```

<a name="triggernodejs"></a>

### <a name="trigger-sample-in-nodejs"></a>Пример триггера для Node.js

```javascript
module.exports = function (context, myEventHubMessage) {
    context.log('Node.js eventhub trigger function processed work item', myEventHubMessage);    
    context.done();
};
```

<a name="output"></a>

## <a name="event-hubs-output-binding"></a>Выходная привязка концентраторов событий
Используйте выходную привязку концентратора событий для записи событий в поток событий концентратора событий. Чтобы записывать события в концентратор событий, необходимо иметь разрешение на оправку в него событий.

Выходная привязка использует следующий объект JSON в массиве `bindings` файла function.json:

```json
{
    "type": "eventHub",
    "name": "<Name of output parameter in function signature>",
    "path": "<Name of event hub>",
    "connection": "<Name of app setting with connection string - see below>"
    "direction": "out"
}
```

`connection` — имя параметра приложения, содержащего строку подключения к пространству имен концентратора событий.
Скопируйте эту строку подключения, нажав кнопку **Сведения о подключении** для *пространства имен*, а не сам концентратор событий. Чтобы отправлять сообщения в поток событий, эта строка подключения должна иметь разрешения на отправку.

## <a name="output-usage"></a>Использование выходной привязки
В этом разделе показано, как использовать выходную привязку концентраторов событий в коде функции.

Можно выводить сообщения в настроенный концентратор событий со следующими типами параметров.

* `out string`
* `ICollector<string>` (для вывода нескольких сообщений).
* `IAsyncCollector<string>` (асинхронная версия `ICollector<T>`).

<a name="outputsample"></a>

## <a name="output-sample"></a>Пример выходной привязки
Предположим, что у вас есть следующая выходная привязка концентраторов событий в массиве `bindings` файла function.json:

```json
{
    "type": "eventHub",
    "name": "outputEventHubMessage",
    "path": "myeventhub",
    "connection": "MyEventHubSend",
    "direction": "out"
}
```

Ознакомьтесь с примером для конкретного языка, записывающим событие в поток событий.

* [C#](#outcsharp)
* [F#](#outfsharp)
* [Node.js](#outnodejs)

<a name="outcsharp"></a>

### <a name="output-sample-in-c"></a>Пример выходной привязки для языка C# #

```cs
using System;

public static void Run(TimerInfo myTimer, out string outputEventHubMessage, TraceWriter log)
{
    String msg = $"TimerTriggerCSharp1 executed at: {DateTime.Now}";
    log.Verbose(msg);   
    outputEventHubMessage = msg;
}
```

Создание нескольких сообщений:

```cs
public static void Run(TimerInfo myTimer, ICollector<string> outputEventHubMessage, TraceWriter log)
{
    string message = $"Event Hub message created at: {DateTime.Now}";
    log.Info(message);
    outputEventHubMessage.Add("1 " + message);
    outputEventHubMessage.Add("2 " + message);
}
```

<a name="outfsharp"></a>

### <a name="output-sample-in-f"></a>Пример выходной привязки для языка F# #

```fsharp
let Run(myTimer: TimerInfo, outputEventHubMessage: byref<string>, log: TraceWriter) =
    let msg = sprintf "TimerTriggerFSharp1 executed at: %s" DateTime.Now.ToString()
    log.Verbose(msg);
    outputEventHubMessage <- msg;
```

<a name="outnodejs"></a>

### <a name="output-sample-for-nodejs"></a>Пример выходной привязки для Node.js

```javascript
module.exports = function (context, myTimer) {
    var timeStamp = new Date().toISOString();
    context.log('Event Hub message created at: ', timeStamp);   
    context.bindings.outputEventHubMessage = "Event Hub message created at: " + timeStamp;
    context.done();
};
```

Отправка нескольких сообщений:

```javascript
module.exports = function(context) {
    var timeStamp = new Date().toISOString();
    var message = 'Event Hub message created at: ' + timeStamp;

    context.bindings.outputEventHubMessage = [];

    context.bindings.outputEventHubMessage.push("1 " + message);
    context.bindings.outputEventHubMessage.push("2 " + message);
    context.done();
};
```

## <a name="next-steps"></a>Дальнейшие действия
[!INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)]

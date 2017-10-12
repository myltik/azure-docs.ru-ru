---
title: "Триггеры и привязки служебной шины в Функциях Azure | Документация Майкрософт"
description: "Узнайте, как использовать триггеры и привязки служебной шины Azure в функциях Azure."
services: functions
documentationcenter: na
author: christopheranderson
manager: cfowler
editor: 
tags: 
keywords: "функции azure, функции, обработка событий, динамические вычисления, независимая архитектура"
ms.assetid: daedacf0-6546-4355-a65c-50873e74f66b
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 04/01/2017
ms.author: glenga
ms.openlocfilehash: 71149aaacc940a62e085cf1ce103a0214d05bd1c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="azure-functions-service-bus-bindings"></a>Привязки служебной шины в Функциях Azure
[!INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

В этой статье рассматривается настройка привязок служебной шины Azure в Функциях Azure, а также работа с ними. 

Функции Azure поддерживают привязки триггера и выходные привязки для очередей и разделов служебной шины.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

<a name="trigger"></a>

## <a name="service-bus-trigger"></a>Триггер служебной шины
Используйте триггер служебной шины для ответа на сообщения из очереди или раздела служебной шины. 

Триггеры очередей и разделов служебной шины определяются с помощью следующих объектов JSON в массиве `bindings` function.json:

* Триггер *очереди*:

    ```json
    {
        "name" : "<Name of input parameter in function signature>",
        "queueName" : "<Name of the queue>",
        "connection" : "<Name of app setting that has your queue's connection string - see below>",
        "accessRights" : "<Access rights for the connection string - see below>",
        "type" : "serviceBusTrigger",
        "direction" : "in"
    }
    ```

* Триггер *раздела*:

    ```json
    {
        "name" : "<Name of input parameter in function signature>",
        "topicName" : "<Name of the topic>",
        "subscriptionName" : "<Name of the subscription>",
        "connection" : "<Name of app setting that has your topic's connection string - see below>",
        "accessRights" : "<Access rights for the connection string - see below>",
        "type" : "serviceBusTrigger",
        "direction" : "in"
    }
    ```

Обратите внимание на следующее.

* Для `connection` [создайте в приложении-функции параметр приложения](functions-how-to-use-azure-function-app-settings.md), содержащий строку подключения к пространству имен служебной шины, а затем укажите имя этого параметра в свойстве `connection` для триггера. Чтобы получить строку подключения, следуйте инструкциям, указанным в разделе [Получение учетных данных управления](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md#obtain-the-management-credentials).
  Строка подключения указывается для пространства имен служебной шины, и она не должна ограничиваться определенной очередью или разделом.
  Если оставить свойство `connection` пустым, триггер предполагает, что в параметре приложения `AzureWebJobsServiceBus` указывается строка подключения служебной шины по умолчанию.
* Для свойства `accessRights` возможны такие значения, как `manage` и `listen`. Значение по умолчанию — `manage`. Это означает, что у свойства `connection` есть разрешение на **управление**. При использовании строки подключения без разрешения на **управление**, задайте для свойства `accessRights` значение `listen`. В противном случае выполнение операций, для которых требуются права на управление, в среде выполнения Функций Azure может завершиться ошибкой.

## <a name="trigger-behavior"></a>Поведение триггера
* **Однопоточная обработка**. По умолчанию в среде выполнения Функций одновременно обрабатываются несколько сообщений очереди. Чтобы среда выполнения обрабатывала в любой момент времени только одно сообщение очереди или раздела, для свойства `serviceBus.maxConcurrentCalls` в файле *host.json* нужно задать значение 1. 
  Сведения о файле *host.json* см. в разделе [Структура папок](functions-reference.md#folder-structure) и статье [host.json](https://github .com/Azure/azure-webjobs-sdk-script/wiki/host.json).
* **Обработка подозрительных сообщений.** В служебной шине выполняется собственная обработка подозрительных сообщений, которую нельзя контролировать или настраивать с помощью конфигурации или кода Функций Azure. 
* **Поведение PeekLock.** Среда выполнения Функций получает сообщение в [режиме `PeekLock`](../service-bus-messaging/service-bus-performance-improvements.md#receive-mode) и вызывает для сообщения метод `Complete`, если функция выполнена успешно, или метод `Abandon` в случае сбоя. 
  Если функция выполняется дольше времени ожидания `PeekLock` , блокировка возобновляется автоматически.

<a name="triggerusage"></a>

## <a name="trigger-usage"></a>Использование триггера
В этом разделе показано, как использовать триггер служебной шины в коде функции. 

В языке C# и F# сообщение триггера служебной шины можно десериализировать в один из следующих входных типов:

* `string` используется для строковых сообщений.
* `byte[]` используется для двоичных данных.
* Любой [объект](https://msdn.microsoft.com/library/system.object.aspx) используется для сериализованных данных JSON.
  Если объявить пользовательский тип входных данных (например, `CustomType`), Функции Azure попытаются десериализировать данные JSON в указанный тип.
* `BrokeredMessage` предоставляет десериализированное сообщение с методом [BrokeredMessage.GetBody<T>()](https://msdn.microsoft.com/library/hh144211.aspx).

В Node.js сообщение триггера служебной шины передается в функцию в качестве строки или объекта JSON.

<a name="triggersample"></a>

## <a name="trigger-sample"></a>Пример триггера
Предположим, что у вас есть следующий файл function.json:

```json
{
"bindings": [
    {
    "queueName": "testqueue",
    "connection": "MyServiceBusConnection",
    "name": "myQueueItem",
    "type": "serviceBusTrigger",
    "direction": "in"
    }
],
"disabled": false
}
```

Ознакомьтесь с примером для конкретного языка, обрабатывающим сообщение очереди служебной шины.

* [C#](#triggercsharp)
* [F#](#triggerfsharp)
* [Node.js](#triggernodejs)

<a name="triggercsharp"></a>

### <a name="trigger-sample-in-c"></a>Пример триггера на языке C# #

```cs
public static void Run(string myQueueItem, TraceWriter log)
{
    log.Info($"C# ServiceBus queue trigger function processed message: {myQueueItem}");
}
```

<a name="triggerfsharp"></a>

### <a name="trigger-sample-in-f"></a>Пример триггера на языке F# #

```fsharp
let Run(myQueueItem: string, log: TraceWriter) =
    log.Info(sprintf "F# ServiceBus queue trigger function processed message: %s" myQueueItem)
```

<a name="triggernodejs"></a>

### <a name="trigger-sample-in-nodejs"></a>Пример триггера для Node.js

```javascript
module.exports = function(context, myQueueItem) {
    context.log('Node.js ServiceBus queue trigger function processed message', myQueueItem);
    context.done();
};
```

<a name="output"></a>

## <a name="service-bus-output-binding"></a>Выходная привязка служебной шины
Выходные данные очереди и раздела служебной шины для функции используют следующие объекты JSON в массиве `bindings` файла function.json:

* Выходные данные *очереди*:

    ```json
    {
        "name" : "<Name of output parameter in function signature>",
        "queueName" : "<Name of the queue>",
        "connection" : "<Name of app setting that has your queue's connection string - see below>",
        "accessRights" : "<Access rights for the connection string - see below>",
        "type" : "serviceBus",
        "direction" : "out"
    }
    ```
* Выходные данные *раздела*:

    ```json
    {
        "name" : "<Name of output parameter in function signature>",
        "topicName" : "<Name of the topic>",
        "subscriptionName" : "<Name of the subscription>",
        "connection" : "<Name of app setting that has your topic's connection string - see below>",
        "accessRights" : "<Access rights for the connection string - see below>",
        "type" : "serviceBus",
        "direction" : "out"
    }
    ```

Обратите внимание на следующее.

* Для `connection` [создайте в приложении-функции параметр приложения](functions-how-to-use-azure-function-app-settings.md), содержащий строку подключения к пространству имен служебной шины, а затем укажите имя этого параметра в свойстве `connection` для выходной привязки. Чтобы получить строку подключения, следуйте инструкциям, указанным в разделе [Получение учетных данных управления](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md#obtain-the-management-credentials).
  Строка подключения указывается для пространства имен служебной шины, и она не должна ограничиваться определенной очередью или разделом.
  Если оставить параметр `connection` пустым, выходная привязка предполагает, что в параметре приложения `AzureWebJobsServiceBus` указывается строка подключения служебной шины по умолчанию.
* Для свойства `accessRights` возможны такие значения, как `manage` и `listen`. Значение по умолчанию — `manage`. Это означает, что у свойства `connection` есть разрешение на **управление**. При использовании строки подключения без разрешения на **управление**, задайте для свойства `accessRights` значение `listen`. В противном случае выполнение операций, для которых требуются права на управление, в среде выполнения Функций Azure может завершиться ошибкой.

<a name="outputusage"></a>

## <a name="output-usage"></a>Использование выходной привязки
В языке C# и F# Функции Azure могут создать сообщение очереди служебной шины из следующих типов:

* Любой [объект](https://msdn.microsoft.com/library/system.object.aspx). Определение параметра выглядит так: `out T paramName` (C#).
  Функции десериализируют объект в сообщение JSON. Если при выходе из функции выходное значение равно null, Функции создают сообщение с пустым объектом.
* `string`. Определение параметра выглядит так: `out string paraName` (C#). Если при выходе из функции значение параметра не равно null, Функции создают сообщение.
* `byte[]`. Определение параметра выглядит так: `out byte[] paraName` (C#). Если при выходе из функции значение параметра не равно null, Функции создают сообщение.
* `BrokeredMessage`. Определение параметра выглядит так: `out BrokeredMessage paraName` (C#). Если при выходе из функции значение параметра не равно null, Функции создают сообщение.

Чтобы создать несколько сообщений в функции C#, можно использовать `ICollector<T>` или `IAsyncCollector<T>`. Сообщение создается при вызове метода `Add` .

В Node.js для `context.binding.<paramName>` можно назначить строку, массив байтов или объект Javascript (десериализируется в JSON).

<a name="outputsample"></a>

## <a name="output-sample"></a>Пример выходной привязки
Предположим, что у вас есть следующий файл function.json, определяющий выходные данные очереди служебной шины:

```json
{
    "bindings": [
        {
            "schedule": "0/15 * * * * *",
            "name": "myTimer",
            "runsOnStartup": true,
            "type": "timerTrigger",
            "direction": "in"
        },
        {
            "name": "outputSbQueue",
            "type": "serviceBus",
            "queueName": "testqueue",
            "connection": "MyServiceBusConnection",
            "direction": "out"
        }
    ],
    "disabled": false
}
```

Ознакомьтесь с примером для конкретного языка, отправляющим сообщение в очередь служебной шины.

* [C#](#outcsharp)
* [F#](#outfsharp)
* [Node.js](#outnodejs)

<a name="outcsharp"></a>

### <a name="output-sample-in-c"></a>Пример выходной привязки для языка C# #

```cs
public static void Run(TimerInfo myTimer, TraceWriter log, out string outputSbQueue)
{
    string message = $"Service Bus queue message created at: {DateTime.Now}";
    log.Info(message); 
    outputSbQueue = message;
}
```

Создание нескольких сообщений:

```cs
public static void Run(TimerInfo myTimer, TraceWriter log, ICollector<string> outputSbQueue)
{
    string message = $"Service Bus queue message created at: {DateTime.Now}";
    log.Info(message); 
    outputSbQueue.Add("1 " + message);
    outputSbQueue.Add("2 " + message);
}
```

<a name="outfsharp"></a>

### <a name="output-sample-in-f"></a>Пример выходной привязки для языка F# #

```fsharp
let Run(myTimer: TimerInfo, log: TraceWriter, outputSbQueue: byref<string>) =
    let message = sprintf "Service Bus queue message created at: %s" (DateTime.Now.ToString())
    log.Info(message)
    outputSbQueue = message
```

<a name="outnodejs"></a>

### <a name="output-sample-in-nodejs"></a>Пример выходной привязки для Node.js

```javascript
module.exports = function (context, myTimer) {
    var message = 'Service Bus queue message created at ' + timeStamp;
    context.log(message);   
    context.bindings.outputSbQueueMsg = message;
    context.done();
};
```

Создание нескольких сообщений:

```javascript
module.exports = function (context, myTimer) {
    var message = 'Service Bus queue message created at ' + timeStamp;
    context.log(message);   
    context.bindings.outputSbQueueMsg = [];
    context.bindings.outputSbQueueMsg.push("1 " + message);
    context.bindings.outputSbQueueMsg.push("2 " + message);
    context.done();
};
```

## <a name="next-steps"></a>Дальнейшие действия
[!INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)]


---
title: Привязки служебной шины Azure для службы "Функции Azure"
description: Узнайте, как использовать триггеры и привязки служебной шины Azure в функциях Azure.
services: functions
documentationcenter: na
author: tdykstra
manager: cfowler
editor: ''
tags: ''
keywords: функции azure, функции, обработка событий, динамические вычисления, независимая архитектура
ms.assetid: daedacf0-6546-4355-a65c-50873e74f66b
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 04/01/2017
ms.author: tdykstra
ms.openlocfilehash: 01ddebd219a97a59ba3f979d32d6c563a0d31f8a
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/18/2018
ms.locfileid: "34304119"
---
# <a name="azure-service-bus-bindings-for-azure-functions"></a>Привязки служебной шины Azure для службы "Функции Azure"

В этой статье описывается использование привязок служебной шины Azure в службе "Функции Azure". Функции Azure поддерживают привязки триггера и выходные привязки для очередей и разделов служебной шины.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages"></a>Пакеты

Привязки служебной шины доступны в пакете NuGet [Microsoft.Azure.WebJobs.ServiceBus](http://www.nuget.org/packages/Microsoft.Azure.WebJobs.ServiceBus). Исходный код для пакета находится в репозитории GitHub [azure-webjobs-sdk](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/).

[!INCLUDE [functions-package](../../includes/functions-package.md)]

[!INCLUDE [functions-package-versions](../../includes/functions-package-versions.md)]

## <a name="trigger"></a>Триггер

Используйте триггер служебной шины для ответа на сообщения из очереди или раздела служебной шины. 

## <a name="trigger---example"></a>Пример триггера

Языковой пример см. в разделах:

* [C#](#trigger---c-example)
* [Скрипт C# (CSX)](#trigger---c-script-example)
* [F#](#trigger---f-example)
* [JavaScript](#trigger---javascript-example)

### <a name="trigger---c-example"></a>Пример C# в триггере

В следующем примере показана [функция C#](functions-dotnet-class-library.md), которая считывает [метаданные сообщения](#trigger---message-metadata) и заносит в журнал сообщение из очереди службы "Служебная шина".

```cs
[FunctionName("ServiceBusQueueTriggerCSharp")]                    
public static void Run(
    [ServiceBusTrigger("myqueue", AccessRights.Manage, Connection = "ServiceBusConnection")] 
    string myQueueItem,
    Int32 deliveryCount,
    DateTime enqueuedTimeUtc,
    string messageId,
    TraceWriter log)
{
    log.Info($"C# ServiceBus queue trigger function processed message: {myQueueItem}");
    log.Info($"EnqueuedTimeUtc={enqueuedTimeUtc}");
    log.Info($"DeliveryCount={deliveryCount}");
    log.Info($"MessageId={messageId}");
}
```

Этот пример предназначен для Функций Azure версии 1.x; для версии 2.x необходимо [опустить параметр прав доступа](#trigger---configuration).
 
### <a name="trigger---c-script-example"></a>Пример скрипта C# в триггере

В следующем примере показаны привязка триггера служебной шины в файле *function.json* и [функция сценария C#](functions-reference-csharp.md), которая использует эту привязку. Функция считывает [метаданные сообщения](#trigger---message-metadata) и заносит в журнал сообщение из очереди службы "Служебная шина".

Данные привязки в файле *function.json*:

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

Ниже приведен код скрипта C#.

```cs
using System;

public static void Run(string myQueueItem,
    Int32 deliveryCount,
    DateTime enqueuedTimeUtc,
    string messageId,
    TraceWriter log)
{
    log.Info($"C# ServiceBus queue trigger function processed message: {myQueueItem}");

    log.Info($"EnqueuedTimeUtc={enqueuedTimeUtc}");
    log.Info($"DeliveryCount={deliveryCount}");
    log.Info($"MessageId={messageId}");
}
```

### <a name="trigger---f-example"></a>Пример F# в триггере

В следующем примере показаны привязка триггера служебной шины в файле *function.json* и [функция F#](functions-reference-fsharp.md), которая использует эту привязку. Эта функция заносит в журнал сообщение очереди служебной шины. 

Данные привязки в файле *function.json*:

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

Ниже приведен код сценария F#.

```fsharp
let Run(myQueueItem: string, log: TraceWriter) =
    log.Info(sprintf "F# ServiceBus queue trigger function processed message: %s" myQueueItem)
```

### <a name="trigger---javascript-example"></a>Пример JavaScript в триггере

В следующем примере показаны привязка триггера служебной шины в файле *function.json* и [функция JavaScript](functions-reference-node.md), которая использует эту привязку. Функция считывает [метаданные сообщения](#trigger---message-metadata) и заносит в журнал сообщение из очереди службы "Служебная шина". 

Данные привязки в файле *function.json*:

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

Ниже показан код сценария JavaScript.

```javascript
module.exports = function(context, myQueueItem) {
    context.log('Node.js ServiceBus queue trigger function processed message', myQueueItem);
    context.log('EnqueuedTimeUtc =', context.bindingData.enqueuedTimeUtc);
    context.log('DeliveryCount =', context.bindingData.deliveryCount);
    context.log('MessageId =', context.bindingData.messageId);
    context.done();
};
```

## <a name="trigger---attributes"></a>Атрибуты триггера

В [библиотеках классов C#](functions-dotnet-class-library.md) используйте следующие атрибуты для настройки триггера службы "Служебная шина":

* [ServiceBusTriggerAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/ServiceBusTriggerAttribute.cs)

  Конструктор этого атрибута принимает имя очереди или раздела и подписки. В Функциях Azure версии 1.х можно также указать права доступа для подключения. Если права доступа не указаны, то используется значение по умолчанию, `Manage`. Дополнительные сведения см. в разделе [Конфигурация триггера](#trigger---configuration).

  Ниже приведен пример, в котором показано, как атрибут используется со строковым параметром.

  ```csharp
  [FunctionName("ServiceBusQueueTriggerCSharp")]                    
  public static void Run(
      [ServiceBusTrigger("myqueue")] string myQueueItem, TraceWriter log)
  {
      ...
  }
  ```

  Чтобы указать учетную запись служебной шины, которую нужно использовать, можно задать свойство `Connection`, как показано в следующем примере.

  ```csharp
  [FunctionName("ServiceBusQueueTriggerCSharp")]                    
  public static void Run(
      [ServiceBusTrigger("myqueue", Connection = "ServiceBusConnection")] 
      string myQueueItem, TraceWriter log)
  {
      ...
  }
  ```

  Полный пример см. в разделе [Пример C# в триггере](#trigger---c-example).

* [ServiceBusAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/ServiceBusAccountAttribute.cs)

  Предоставляет еще один способ указать используемую учетную запись служебной шины. Конструктор принимает имя параметра приложения, содержащего строку подключения к служебной шине. Атрибут может применяться на уровне класса, метода или параметра. Ниже показан пример уровня класса и метода.

  ```csharp
  [ServiceBusAccount("ClassLevelServiceBusAppSetting")]
  public static class AzureFunctions
  {
      [ServiceBusAccount("MethodLevelServiceBusAppSetting")]
      [FunctionName("ServiceBusQueueTriggerCSharp")]
      public static void Run(
          [ServiceBusTrigger("myqueue", AccessRights.Manage)] 
          string myQueueItem, TraceWriter log)
  {
      ...
  }
  ```

Используемая учетная запись служебной шины определяется в следующем порядке:

* Свойство `ServiceBusTrigger` атрибута `Connection`.
* Атрибут `ServiceBusAccount`, примененный к тому же параметру, что и `ServiceBusTrigger`.
* Атрибут `ServiceBusAccount`, примененный к функции.
* Атрибут `ServiceBusAccount`, примененный к классу.
* Параметр приложения AzureWebJobsServiceBus.

## <a name="trigger---configuration"></a>Конфигурация триггера

В следующей таблице описываются свойства конфигурации привязки, которые задаются в файле *function.json* и атрибуте `ServiceBusTrigger`.

|свойство function.json | Свойство атрибута |ОПИСАНИЕ|
|---------|---------|----------------------|
|**type** | Недоступно | Для этого свойства нужно задать значение "serviceBusTrigger". Это свойство задается автоматически при создании триггера на портале Azure.|
|**direction** | Недоступно | Для этого свойства необходимо задать значение "in". Это свойство задается автоматически при создании триггера на портале Azure. |
|**name** | Недоступно | Имя переменной, представляющей сообщение очереди или раздела в коде функции. Задайте значение "$return", ссылающееся на возвращаемое значение функции. | 
|**queueName**|**QueueName**|Имя очереди для отслеживания.  Задается только в том случае, если отслеживается очередь, но не раздел.
|**topicName**|**TopicName**|Имя отслеживаемого раздела. Задается только в том случае, если отслеживается раздел, но не очередь.|
|**subscriptionName**|**Параметр SubscriptionName**|Имя отслеживаемой подписки. Задается только в том случае, если отслеживается раздел, но не очередь.|
|**подключение**|**Connection**|Имя параметра приложения, содержащего строку подключения к служебной шине, используемую для этой привязки. Если имя параметра приложения начинается с AzureWebJobs, можно указать только остальную часть имени. Например, если задать для `connection` значение MyServiceBus, то среда выполнения службы "Функции" будет искать параметр приложения AzureWebJobsMyServiceBus. Если оставить строку `connection` пустой, то среда выполнения службы "Функции" будет использовать строку подключения к служебной шине по умолчанию для параметра приложения AzureWebJobsServiceBus.<br><br>Чтобы получить строку подключения, следуйте инструкциям, указанным в разделе [Получение учетных данных управления](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md#obtain-the-management-credentials). Строка подключения указывается для пространства имен служебной шины, и она не должна ограничиваться определенной очередью или разделом. |
|**accessRights**|**Access**|Права доступа для строки подключения. Доступные значения: `manage` и `listen`. Значение по умолчанию — `manage`. Это означает, что у свойства `connection` есть разрешение на **управление**. При использовании строки подключения без разрешения на **управление** задайте для свойства `accessRights` значение "listen". В противном случае выполнение операций, для которых требуются права на управление, в среде выполнения Функций Azure может завершиться ошибкой. В Функциях Azure версии 2.x это свойство недоступно, так как последняя версия пакета SDK службы хранилища не поддерживает управление операциями.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="trigger---usage"></a>Использование триггера

В C# и сценарии C# для сообщения очереди или раздела можно использовать параметры следующих типов:

* `string`. Если сообщение является текстом.
* `byte[]`. Используется для двоичных данных.
* Пользовательский тип. Если сообщение содержит JSON, то служба "Функции Azure" пытается выполнить десериализацию данных JSON.
* `BrokeredMessage`. Предоставляет десериализированное сообщение с использованием метода [BrokeredMessage.GetBody<T>()](https://msdn.microsoft.com/library/hh144211.aspx).

Эти параметры предназначены для решения "Функции Azure" версии 1.x. Если используется версия 2.x, вместо `BrokeredMessage` примените [`Message`](https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.message).

В JavaScript доступ к сообщению очереди или раздела осуществляется с помощью `context.bindings.<name from function.json>`. Сообщение служебной шины передается в функцию в качестве строки или объекта JSON.

## <a name="trigger---poison-messages"></a>Триггеры сообщений о сбое

В службе "Функции Azure" невозможно управление обработкой сообщений о сбое или настройка этой обработки. Служебная шина сама обрабатывает сообщения о сбое.

## <a name="trigger---peeklock-behavior"></a>Поведение PeekLock в триггере

Среда выполнения службы "Функции" получает сообщение в [режиме PeekLock](../service-bus-messaging/service-bus-performance-improvements.md#receive-mode). Она вызывает `Complete` для сообщения, если функция выполнена успешно, или `Abandon` в случае сбоя. Если функция выполняется дольше времени ожидания `PeekLock`, блокировка возобновляется автоматически до тех пор, пока выполняется функция. 

Функции версии 1.x позволяют настроить в файле *host.json* параметр `autoRenewTimeout`, который сопоставляется с [OnMessageOptions.AutoRenewTimeout](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.onmessageoptions.autorenewtimeout?view=azure-dotnet#Microsoft_ServiceBus_Messaging_OnMessageOptions_AutoRenewTimeout). В соответствии с документацией по служебной шине максимально допустимое значение для этого параметра равно 5 минутам, тогда как ограничение времени выполнения функций можно увеличить с 5 минут по умолчанию до 10 минут. Этого не стоит делать для функций служебной шины, потому что лимит продления служебной шины будет превышен.

## <a name="trigger---message-metadata"></a>Метаданные сообщения триггера

Триггер служебной шины предоставляет несколько [свойств метаданных](functions-triggers-bindings.md#binding-expressions---trigger-metadata). Эти свойства можно использовать как часть выражений привязки в других привязках или как параметры в коде. Эти свойства относятся к классу [BrokeredMessage](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.brokeredmessage).

|Свойство|type|ОПИСАНИЕ|
|--------|----|-----------|
|`DeliveryCount`|`Int32`|Число доставок.|
|`DeadLetterSource`|`string`|Источник недоставленных сообщений.|
|`ExpiresAtUtc`|`DateTime`|Время окончания срока действия в формате UTC.|
|`EnqueuedTimeUtc`|`DateTime`|Время попадания в очередь в формате UTC.|
|`MessageId`|`string`|Определяемое пользователем значение, используемое служебной шиной для выявления повторяющихся сообщений.|
|`ContentType`|`string`|Идентификатор типа содержимого, используемый отправителем и получателем для логики конкретного приложения.|
|`ReplyTo`|`string`|Адрес очереди для ответа.|
|`SequenceNumber`|`Int64`|Уникальный номер, назначенный сообщению службой "Служебная шина".|
|`To`|`string`|Адрес для отправки.|
|`Label`|`string`|Метка конкретного приложения.|
|`CorrelationId`|`string`|Идентификатор корреляции.|
|`Properties`|`IDictionary<String,Object>`|Свойства сообщения конкретного приложения.|

См. [примеры кода](#trigger---example), в которых используются эти свойства, в предыдущих разделах этой статьи.

## <a name="trigger---hostjson-properties"></a>Свойства host.json в триггере

В файле [host.json](functions-host-json.md#servicebus) содержатся параметры, управляющие поведением очереди триггера служебной шины.

[!INCLUDE [functions-host-json-event-hubs](../../includes/functions-host-json-service-bus.md)]

## <a name="output"></a>Выходные данные

Используйте выходную привязку служебной шины Azure для отправки сообщений очереди или раздела.

## <a name="output---example"></a>Пример выходных данных

Языковой пример см. в разделах:

* [C#](#output---c-example)
* [Скрипт C# (CSX)](#output---c-script-example)
* [F#](#output---f-example)
* [JavaScript](#output---javascript-example)

### <a name="output---c-example"></a>Пример выходных данных C#

В следующем примере показана [функция C#](functions-dotnet-class-library.md), которая отправляет сообщение из очереди службы "Служебная шина":

```cs
[FunctionName("ServiceBusOutput")]
[return: ServiceBus("myqueue", Connection = "ServiceBusConnection")]
public static string ServiceBusOutput([HttpTrigger] dynamic input, TraceWriter log)
{
    log.Info($"C# function processed: {input.Text}");
    return input.Text;
}
```

### <a name="output---c-script-example"></a>Пример выходных данных скрипта C#

В следующем примере показаны выходная привязка служебной шины в файле *function.json* и [функция сценария C#](functions-reference-csharp.md), которая использует эту привязку. Функция использует триггер таймера для отправки сообщения очереди каждые 15 секунд.

Данные привязки в файле *function.json*:

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

Ниже приведен код сценария C#, который создает одно сообщение.

```cs
public static void Run(TimerInfo myTimer, TraceWriter log, out string outputSbQueue)
{
    string message = $"Service Bus queue message created at: {DateTime.Now}";
    log.Info(message); 
    outputSbQueue = message;
}
```

Ниже приведен код сценария C#, который создает несколько сообщений.

```cs
public static void Run(TimerInfo myTimer, TraceWriter log, ICollector<string> outputSbQueue)
{
    string message = $"Service Bus queue messages created at: {DateTime.Now}";
    log.Info(message); 
    outputSbQueue.Add("1 " + message);
    outputSbQueue.Add("2 " + message);
}
```

### <a name="output---f-example"></a>Пример выходных данных F#

В следующем примере показаны выходная привязка служебной шины в файле *function.json* и [функция сценария F#](functions-reference-fsharp.md), которая использует эту привязку. Функция использует триггер таймера для отправки сообщения очереди каждые 15 секунд.

Данные привязки в файле *function.json*:

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

Ниже приведен код сценария F#, который создает одно сообщение.

```fsharp
let Run(myTimer: TimerInfo, log: TraceWriter, outputSbQueue: byref<string>) =
    let message = sprintf "Service Bus queue message created at: %s" (DateTime.Now.ToString())
    log.Info(message)
    outputSbQueue = message
```

### <a name="output---javascript-example"></a>Пример выходных данных JavaScript

В следующем примере показаны выходная привязка служебной шины в файле *function.json* и [функция JavaScript](functions-reference-node.md), которая использует эту привязку. Функция использует триггер таймера для отправки сообщения очереди каждые 15 секунд.

Данные привязки в файле *function.json*:

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

Ниже приведен код сценария JavaScript, который создает одно сообщение.

```javascript
module.exports = function (context, myTimer) {
    var message = 'Service Bus queue message created at ' + timeStamp;
    context.log(message);   
    context.bindings.outputSbQueue = message;
    context.done();
};
```

Ниже приведен код сценария JavaScript, который создает несколько сообщений.

```javascript
module.exports = function (context, myTimer) {
    var message = 'Service Bus queue message created at ' + timeStamp;
    context.log(message);   
    context.bindings.outputSbQueue = [];
    context.bindings.outputSbQueue.push("1 " + message);
    context.bindings.outputSbQueue.push("2 " + message);
    context.done();
};
```

## <a name="output---attributes"></a>Выходные атрибуты

В [библиотеках классов C#](functions-dotnet-class-library.md) используйте [ServiceBusAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/ServiceBusAttribute.cs).

Конструктор этого атрибута принимает имя очереди или раздела и подписки. Можно также указать права доступа для подключения. Выбор параметра прав доступа описан в разделе [Привязки служебной шины в Функциях Azure](#output---configuration). Ниже приведен пример, в котором этот атрибут применяется к возвращаемому значению функции.

```csharp
[FunctionName("ServiceBusOutput")]
[return: ServiceBus("myqueue")]
public static string Run([HttpTrigger] dynamic input, TraceWriter log)
{
    ...
}
```

Чтобы указать учетную запись служебной шины, которую нужно использовать, можно задать свойство `Connection`, как показано в следующем примере.

```csharp
[FunctionName("ServiceBusOutput")]
[return: ServiceBus("myqueue", Connection = "ServiceBusConnection")]
public static string Run([HttpTrigger] dynamic input, TraceWriter log)
{
    ...
}
```

Полный пример см. в разделе [Пример выходных данных C#](#output---c-example).

Чтобы указать учетную запись служебной шины для использования на уровне класса, метода или параметра, можно применить атрибут `ServiceBusAccount`.  Дополнительные сведения см. в разделе [Атрибуты триггера для предкомпилированного кода C#](#trigger---attributes).

## <a name="output---configuration"></a>Выходная конфигурация

В следующей таблице описываются свойства конфигурации привязки, которые задаются в файле *function.json* и атрибуте `ServiceBus`.

|свойство function.json | Свойство атрибута |ОПИСАНИЕ|
|---------|---------|----------------------|
|**type** | Недоступно | Для этого свойства нужно задать значение "serviceBus". Это свойство задается автоматически при создании триггера на портале Azure.|
|**direction** | Недоступно | Для этого свойства необходимо задать значение out. Это свойство задается автоматически при создании триггера на портале Azure. |
|**name** | Недоступно | Имя переменной, представляющей очередь или раздел в коде функции. Задайте значение "$return", ссылающееся на возвращаемое значение функции. | 
|**queueName**|**QueueName**|Имя очереди.  Задается только в случае отправки сообщений очереди, а не раздела.
|**topicName**|**TopicName**|Имя отслеживаемого раздела. Задается только в случае отправки сообщений раздела, а не очереди.|
|**подключение**|**Connection**|Имя параметра приложения, содержащего строку подключения к служебной шине, используемую для этой привязки. Если имя параметра приложения начинается с AzureWebJobs, можно указать только остальную часть имени. Например, если задать для `connection` значение MyServiceBus, то среда выполнения службы "Функции" будет искать параметр приложения AzureWebJobsMyServiceBus. Если оставить строку `connection` пустой, то среда выполнения службы "Функции" будет использовать строку подключения к служебной шине по умолчанию для параметра приложения AzureWebJobsServiceBus.<br><br>Чтобы получить строку подключения, следуйте инструкциям, указанным в разделе [Получение учетных данных управления](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md#obtain-the-management-credentials). Строка подключения указывается для пространства имен служебной шины, и она не должна ограничиваться определенной очередью или разделом.|
|**accessRights**|**Access**|Права доступа для строки подключения. Доступные значения: `manage` и `listen`. Значение по умолчанию — `manage`. Это означает, что у свойства `connection` есть разрешение на **управление**. При использовании строки подключения без разрешения на **управление** задайте для свойства `accessRights` значение "listen". В противном случае выполнение операций, для которых требуются права на управление, в среде выполнения Функций Azure может завершиться ошибкой. В Функциях Azure версии 2.x это свойство недоступно, так как последняя версия пакета SDK службы хранилища не поддерживает управление операциями.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>Использование выходной привязки

В Функциях Azure версии 1.x среда выполнения создает очередь, если ее не существует, и для параметра `accessRights` необходимо установить значение `manage`. В Функциях Azure версии 2.x очередь или раздел должны уже существовать. Если вы укажете несуществующую очередь или раздел, функция завершится с ошибкой. 

В C# и сценарии C# для привязки к выходным данным можно использовать параметры следующих типов:

* `out T paramName` - `T` может быть любым сериализуемым в JSON типом. Если при выходе из функции параметр имеет значение NULL, то служба "Функции" создает сообщение с пустым объектом.
* `out string`. Если при выходе из функции параметр имеет значение NULL, то служба "Функции" не создает сообщение.
* `out byte[]`. Если при выходе из функции параметр имеет значение NULL, то служба "Функции" не создает сообщение.
* `out BrokeredMessage`. Если при выходе из функции параметр имеет значение NULL, то служба "Функции" не создает сообщение.
* `ICollector<T>` или `IAsyncCollector<T>`. Используется для создания нескольких сообщений. Сообщение создается при вызове метода `Add` .

В асинхронных функциях используйте возвращаемое значение или `IAsyncCollector` вместо параметра `out`.

Эти параметры предназначены для решения "Функции Azure" версии 1.x. Если используется версия 2.x, вместо `BrokeredMessage` примените [`Message`](https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.message).

В JavaScript доступ к очереди или разделу осуществляется с помощью `context.bindings.<name from function.json>`. `context.binding.<name>` можно назначить строку, массив байтов или объект Javascript (десериализируется в JSON).

## <a name="exceptions-and-return-codes"></a>Исключения и коды возврата

| Привязка | Справочные материалы |
|---|---|
| Служебная шина Azure | [Коды ошибок службы "Служебная шина"](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-messaging-exceptions) |
| Служебная шина Azure | [Ограничения службы "Служебная шина"](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-quotas) |

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Основные понятия триггеров и привязок в Функциях Azure](functions-triggers-bindings.md)

---
title: "Привязки хранилища очередей для Функций Azure | Документы Майкрософт"
description: "Узнайте, как использовать триггеры и привязки службы хранилища Azure в функциях Azure."
services: functions
documentationcenter: na
author: lindydonna
manager: cfowler
editor: 
tags: 
keywords: "функции azure, функции, обработка событий, динамические вычисления, независимая архитектура"
ms.assetid: 4e6a837d-e64f-45a0-87b7-aa02688a75f3
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 05/30/2017
ms.author: glenga
ms.translationtype: HT
ms.sourcegitcommit: 94d1d4c243bede354ae3deba7fbf5da0652567cb
ms.openlocfilehash: f39f674bf576a2661a0e03710b9005b0515b3aa5
ms.contentlocale: ru-ru
ms.lasthandoff: 07/18/2017

---
# <a name="azure-functions-queue-storage-bindings"></a>Привязки хранилища очередей для Функций Azure
[!INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

В этой статье описывается, как настроить и запрограммировать привязки хранилища очередей Azure в Функциях Azure. Функции Azure поддерживают привязки триггера и выходные привязки для очередей Azure. Описание возможностей, доступных во всех привязках, см. в статье [Основные понятия триггеров и привязок в Функциях Azure](functions-triggers-bindings.md).

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

<a name="trigger"></a>

## <a name="queue-storage-trigger"></a>Триггер хранилища очередей
Триггер хранилища очередей Azure позволяет отслеживать появление новых сообщений в очереди хранилища и реагировать на них. 

Определите триггер очереди на вкладке **Интеграция** портала функций. Портал создает следующее определение в разделе **bindings** файла *function.json*:

```json
{
    "type": "queueTrigger",
    "direction": "in",
    "name": "<The name used to identify the trigger data in your code>",
    "queueName": "<Name of queue to poll>",
    "connection":"<Name of app setting - see below>"
}
```

* Свойство `connection` должно включать в себя имя параметра приложения, содержащего строку подключения к службе хранилища. На портале Azure стандартный редактор на вкладке **Интеграция** автоматически настраивает этот параметр приложения при выборе учетной записи хранения.

Дополнительные параметры можно указать в [файле host.json](https://github.com/Azure/azure-webjobs-sdk-script/wiki/host.json) для дальнейшей настройки триггеров хранилища очередей. Например, в файле host.json можно изменить интервал опроса очереди.

<a name="triggerusage"></a>

## <a name="using-a-queue-trigger"></a>Использование триггера очереди
В функциях Node.js доступ к данным очереди можно получить, используя `context.bindings.<name>`.


В функциях .NET для доступа к полезным данным очереди используйте параметр метода, например `CloudQueueMessage paramName`. Здесь `paramName` — это значение, указанное в [конфигурации триггера](#trigger). Сообщение очереди можно десериализировать в один из следующих типов:

* Объект POCO. Используется, если полезные данные очереди являются объектом JSON. Среда выполнения функций десериализирует полезные данные в объект POCO. 
* `string`
* `byte[]`
* [`CloudQueueMessage`]

<a name="meta"></a>

### <a name="queue-trigger-metadata"></a>Метаданные триггера очереди
Триггер очереди предоставляет несколько свойств метаданных. Эти свойства можно использовать как часть выражений привязки в других привязках или как параметры в коде. Эти значения имеют ту же семантику, что и [`CloudQueueMessage`].

* **QueueTrigger** — полезные данные очереди (в случае, если это допустимая строка)
* **DequeueCount** — тип `int`. Количество раз, когда сообщение было выведено из очереди.
* **ExpirationTime** — тип `DateTimeOffset?`. Время истечения срока действия сообщения.
* **Id** — тип `string`. Идентификатор сообщения в очереди.
* **InsertionTime** — тип `DateTimeOffset?`. Время, когда сообщение было добавлено в очередь.
* **NextVisibleTime** — введите `DateTimeOffset?`. Время, когда сообщение станет видимым в следующий раз.
* **PopReceipt** — тип `string`. Уведомление о получении сообщения.

Сведения об использовании метаданных очереди см. в разделе [Пример триггера](#triggersample)

<a name="triggersample"></a>

## <a name="trigger-sample"></a>Пример триггера
Предположим, что у вас есть следующий файл function.json, определяющий триггер очереди:

```json
{
    "disabled": false,
    "bindings": [
        {
            "type": "queueTrigger",
            "direction": "in",
            "name": "myQueueItem",
            "queueName": "myqueue-items",
            "connection":"MyStorageConnectionString"
        }
    ]
}
```

Ознакомьтесь с примером для конкретного языка, загружающим и регистрирующим метаданные очереди.

* [C#](#triggercsharp)
* [Node.js](#triggernodejs)

<a name="triggercsharp"></a>

### <a name="trigger-sample-in-c"></a>Пример триггера на языке C# #
```csharp
#r "Microsoft.WindowsAzure.Storage"

using Microsoft.WindowsAzure.Storage.Queue;
using System;

public static void Run(CloudQueueMessage myQueueItem, 
    DateTimeOffset expirationTime, 
    DateTimeOffset insertionTime, 
    DateTimeOffset nextVisibleTime,
    string queueTrigger,
    string id,
    string popReceipt,
    int dequeueCount,
    TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem.AsString}\n" +
        $"queueTrigger={queueTrigger}\n" +
        $"expirationTime={expirationTime}\n" +
        $"insertionTime={insertionTime}\n" +
        $"nextVisibleTime={nextVisibleTime}\n" +
        $"id={id}\n" +
        $"popReceipt={popReceipt}\n" + 
        $"dequeueCount={dequeueCount}");
}
```

<!--
<a name="triggerfsharp"></a>
### Trigger sample in F# ## 
```fsharp

```
-->

<a name="triggernodejs"></a>

### <a name="trigger-sample-in-nodejs"></a>Пример триггера для Node.js

```javascript
module.exports = function (context) {
    context.log('Node.js queue trigger function processed work item', context.bindings.myQueueItem);
    context.log('queueTrigger =', context.bindingData.queueTrigger);
    context.log('expirationTime =', context.bindingData.expirationTime);
    context.log('insertionTime =', context.bindingData.insertionTime);
    context.log('nextVisibleTime =', context.bindingData.nextVisibleTime);
    context.log('id=', context.bindingData.id);
    context.log('popReceipt =', context.bindingData.popReceipt);
    context.log('dequeueCount =', context.bindingData.dequeueCount);
    context.done();
};
```

### <a name="handling-poison-queue-messages"></a>Обработка подозрительных сообщений очереди
При сбое функции триггера очереди по умолчанию Функции Azure выполняют ее для заданного сообщения очереди еще пять раз (включая первую попытку). В случае сбоя всех пяти попыток среда выполнения функций добавляет сообщение в хранилище очередей с именем *&lt;имя_превоначальной_очереди>-poison*. Можно написать функции для обработки сообщений из очереди подозрительных сообщений путем внесения их в журнал или отправки уведомления о необходимости ручного вмешательства. 

Для обработки сообщений о сбоях вручную проверьте значение `dequeueCount` сообщения в очереди (см. раздел [Метаданные триггера очереди](#meta)).

<a name="output"></a>

## <a name="queue-storage-output-binding"></a>Выходная привязка хранилища очередей
Выходная привязка хранилища очередей Azure позволяет записывать сообщения в очередь. 

Определите выходную привязку очереди на вкладке **Интеграция** портала функций. Портал создает следующее определение в разделе **bindings** файла *function.json*:

```json
{
   "type": "queue",
   "direction": "out",
   "name": "<The name used to identify the trigger data in your code>",
   "queueName": "<Name of queue to write to>",
   "connection":"<Name of app setting - see below>"
}
```

* Свойство `connection` должно включать в себя имя параметра приложения, содержащего строку подключения к службе хранилища. На портале Azure стандартный редактор на вкладке **Интеграция** автоматически настраивает этот параметр приложения при выборе учетной записи хранения.

<a name="outputusage"></a>

## <a name="using-a-queue-output-binding"></a>Использование выходной привязки очереди
В функциях Node.js доступ к выходной очереди можно получить, используя `context.bindings.<name>`.

В функциях .NET можно выполнить вывод одного из указанных ниже типов. Если имеется параметр типа `T`, параметр `T` должен иметь один из поддерживаемых типов вывода, например `string` или POCO.

* `out T` (сериализируется как JSON)
* `out string`
* `out byte[]`
* `out` [`CloudQueueMessage`] 
* `ICollector<T>`
* `IAsyncCollector<T>`
* [`CloudQueue`](/dotnet/api/microsoft.windowsazure.storage.queue.cloudqueue)

В качестве выходной привязки можно также использовать возвращаемый тип метода.

<a name="outputsample"></a>

## <a name="queue-output-sample"></a>Пример вывода очереди
В следующем файле *function.json* определяется триггер HTTP с выходной привязкой очереди:

```json
{
  "bindings": [
    {
      "type": "httpTrigger",
      "direction": "in",
      "authLevel": "function",
      "name": "input"
    },
    {
      "type": "http",
      "direction": "out",
      "name": "return"
    },
    {
      "type": "queue",
      "direction": "out",
      "name": "$return",
      "queueName": "outqueue",
      "connection": "MyStorageConnectionString",
    }
  ]
}
``` 

См. пример для конкретного языка, который выводит сообщение в очереди с входящими полезными данными HTTP.

* [C#](#outcsharp)
* [Node.js](#outnodejs)

<a name="outcsharp"></a>

### <a name="queue-output-sample-in-c"></a>Пример вывода очереди на C# #

```cs
// C# example of HTTP trigger binding to a custom POCO, with a queue output binding
public class CustomQueueMessage
{
    public string PersonName { get; set; }
    public string Title { get; set; }
}

public static CustomQueueMessage Run(CustomQueueMessage input, TraceWriter log)
{
    return input;
}
```

Для отправки нескольких сообщений используйте `ICollector`:

```cs
public static void Run(CustomQueueMessage input, ICollector<CustomQueueMessage> myQueueItem, TraceWriter log)
{
    myQueueItem.Add(input);
    myQueueItem.Add(new CustomQueueMessage { PersonName = "You", Title = "None" });
}
```

<a name="outnodejs"></a>

### <a name="queue-output-sample-in-nodejs"></a>Пример вывода очереди для Node.js

```javascript
module.exports = function (context, input) {
    context.done(null, input.body);
};
```

Отправка нескольких сообщений:

```javascript
module.exports = function(context) {
    // Define a message array for the myQueueItem output binding. 
    context.bindings.myQueueItem = ["message 1","message 2"];
    context.done();
};
```

## <a name="next-steps"></a>Дальнейшие действия

Пример функции, которая использует триггеры и привязки хранилища очередей, доступен в разделе [Создание функции Azure, подключенной к службе Azure](functions-create-an-azure-connected-function.md).

[!INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)]

<!-- LINKS -->

[`CloudQueueMessage`]: /dotnet/api/microsoft.windowsazure.storage.queue.cloudqueuemessage


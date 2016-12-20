---
title: "Привязки очередей службы хранилища для Функций Azure | Документация Майкрософт"
description: "Узнайте, как использовать триггеры и привязки службы хранилища Azure в функциях Azure."
services: functions
documentationcenter: na
author: christopheranderson
manager: erikre
editor: 
tags: 
keywords: "функции azure, функции, обработка событий, динамические вычисления, независимая архитектура"
ms.assetid: 4e6a837d-e64f-45a0-87b7-aa02688a75f3
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 11/02/2016
ms.author: chrande
translationtype: Human Translation
ms.sourcegitcommit: 96f253f14395ffaf647645176b81e7dfc4c08935
ms.openlocfilehash: 36cf563a8318acb9371c48ba7d29e24694446e45


---
# <a name="azure-functions-storage-queue-bindings"></a>Привязки очередей службы хранилища для Функций Azure
[!INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

Эта статья объясняет, как настроить и запрограммировать привязки для очередей службы хранилища Azure в Функциях Azure. Функции Azure поддерживают привязки триггера и выходные привязки для очередей службы хранилища Azure.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

<a name="trigger"></a>

## <a name="storage-queue-trigger"></a>Триггер очереди службы хранилища
Триггер очереди службы хранилища Azure позволяет отслеживать появление новых сообщений в очереди хранилища и реагировать на них. 

Триггер очереди службы хранилища для функции использует следующий объект JSON в массиве `bindings` файла function.json:

```json
{
    "name": "<Name of input parameter in function signature>",
    "queueName": "<Name of queue to poll>",
    "connection":"<Name of app setting - see below>",
    "type": "queueTrigger",
    "direction": "in"
}
```

Свойство `connection` должно включать в себя имя параметра приложения, содержащего строку подключения к службе хранилища. На портале Azure стандартный редактор на вкладке **Интеграция** автоматически настраивает этот параметр приложения при создании учетной записи хранения или выборе одной из имеющихся. Чтобы создать этот параметр приложения вручную, [настройте его вручную]().

[Дополнительные параметры](https://github.com/Azure/azure-webjobs-sdk-script/wiki/host.json) можно указать в файле host.json для дальнейшей настройки триггеров очередей хранилища.  

### <a name="handling-poison-queue-messages"></a>Обработка подозрительных сообщений очереди
При сбое функции триггера очереди по умолчанию Функции Azure выполняют ее для заданного сообщения очереди еще 5 раз (включая первую попытку). В случае сбоя после 5 попыток запуска Функции добавляют сообщение в очередь службы хранилища с именем *&lt;originalqueuename>-poison*. Можно написать функции для обработки сообщений из очереди подозрительных сообщений путем внесения их в журнал или отправки уведомления о необходимости ручного вмешательства. 

Если подозрительные сообщения очереди нужно обрабатывать вручную, количество попыток обработки сообщения можно узнать, проверив значение свойства `dequeueCount` (ознакомьтесь с разделом [Метаданные триггера очереди](#meta)).

<a name="triggerusage"></a>

## <a name="trigger-usage"></a>Использование триггера
В функциях C# можно выполнить привязку к входному сообщению, используя именованный параметр в сигнатуре функции, например `<T> <name>`,
где `T` — это тип данных, в который требуется десериализировать данные, а `paramName` — это имя, указанное в [привязке триггера](#trigger). В функциях Node.js доступ к данным входного большого двоичного объекта можно получить, используя `context.bindings.<name>`.

Сообщение очереди можно десериализировать в один из следующих типов:

* Любой [объект](https://msdn.microsoft.com/library/system.object.aspx) используется для сериализованных сообщений JSON.
  Если объявить пользовательский входной тип (например, `FooType`), Функции Azure попытаются десериализировать данные JSON в указанный тип.
* Строка
* массив байтов; 
* `CloudQueueMessage` (C#) 

<a name="meta"></a>

### <a name="queue-trigger-metadata"></a>Метаданные триггера очереди
Метаданные очереди можно добавить в функцию, используя следующие имена переменных:

* expirationTime;
* insertionTime;
* nextVisibleTime;
* id
* popReceipt;
* dequeueCount.
* queueTrigger (другой способ получения текста сообщения очереди в виде строки).

Сведения об использовании метаданных очереди см. в разделе [Пример триггера](#triggersample)

<a name="triggersample"></a>

## <a name="trigger-sample"></a>Пример триггера
Предположим, что у вас есть следующий файл function.json, определяющий триггер очереди службы хранилища:

```json
{
    "disabled": false,
    "bindings": [
        {
            "name": "myQueueItem",
            "queueName": "myqueue-items",
            "connection":"",
            "type": "queueTrigger",
            "direction": "in"
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
public static void Run(string myQueueItem, 
    DateTimeOffset expirationTime, 
    DateTimeOffset insertionTime, 
    DateTimeOffset nextVisibleTime,
    string queueTrigger,
    string id,
    string popReceipt,
    int dequeueCount,
    TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}\n" +
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
    context.log('Node.js queue trigger function processed work item' context.bindings.myQueueItem);
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

<a name="output"></a>

## <a name="storage-queue-output-binding"></a>Выходная привязка очереди службы хранилища
Выходная привязка очереди службы хранилища Azure позволяет записывать сообщения в очередь службы хранилища в функции. 

Выходные данные очереди службы хранилища для функции используют следующий объект JSON в массиве `bindings` файла function.json:

```json
{
  "name": "<Name of output parameter in function signature>",
    "queueName": "<Name of queue to write to>",
    "connection":"<Name of app setting - see below>",
  "type": "queue",
  "direction": "out"
}
```

Свойство `connection` должно включать в себя имя параметра приложения, содержащего строку подключения к службе хранилища. На портале Azure стандартный редактор на вкладке **Интеграция** автоматически настраивает этот параметр приложения при создании учетной записи хранения или выборе одной из имеющихся. Чтобы создать этот параметр приложения вручную, [настройте его вручную]().

<a name="outputusage"></a>

## <a name="output-usage"></a>Использование выходной привязки
В функциях C# можно записать сообщение очереди, используя именованный параметр `out` в сигнатуре функции, например `out <T> <name>`, где `T` — это тип данных, в который нужно сериализовать сообщение, а `paramName` — это имя, указанное в [выходной привязке](#output). В функциях Node.js доступ к выходным данным можно получить, используя `context.bindings.<name>`.

Сообщение очереди можно вывести, используя любой из типов данных в коде:

* Любой [объект](https://msdn.microsoft.com/library/system.object.aspx) используется для сериализации JSON.
  Если объявить пользовательский выходной тип (например, `out FooType paramName`), Функции Azure попытаются сериализовать объект в JSON. Если при выходе из функции выходной параметр имеет значение null, среда выполнения Функций создает сообщение очереди в качестве пустого объекта.
* Строка (`out string paramName`) используется для тестовых сообщений. Среда выполнения Функций создает сообщение, только если при выходе из функции для параметра строки не задано значение null.
* Массив байтов (`out byte[]`). 
* `out CloudQueueMessage` — только для C#. 

При использовании C# также можно выполнить привязку к `ICollector<T>` или `IAsyncCollector<T>`, где `T` — любой из поддерживаемых типов.

<a name="outputsample"></a>

## <a name="output-sample"></a>Пример выходной привязки
Предположим, что у вас есть следующий файл function.json, определяющий [триггер очереди службы хранилища](functions-bindings-storage-queue.md), а также входные и выходные данные большого двоичного объекта службы хранилища:

Пример файла *function.json* для выходной привязки очереди службы хранилища, в котором реализованы триггер очереди и запись сообщения в очередь:

```json
{
  "bindings": [
    {
      "name": "myQueueItem",
      "queueName": "myqueue-items",
      "connection": "MyStorageConnection",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "myQueue",
      "queueName": "samples-workitems-out",
      "connection": "MyStorageConnection",
      "type": "queue",
      "direction": "out"
    }
  ],
  "disabled": false
}
``` 

Ознакомьтесь с примером для конкретного языка, записывающим выходное сообщение очереди для каждого входного сообщения очереди.

* [C#](#outcsharp)
* [Node.js](#outnodejs)

<a name="outcsharp"></a>

### <a name="output-sample-in-c"></a>Пример выходной привязки для языка C# #

```cs
public static void Run(string myQueueItem, out string myQueue, TraceWriter log)
{
    myQueue = myQueueItem + "(next step)";
}
```

Отправка нескольких сообщений:

```cs
public static void Run(string myQueueItem, ICollector<string> myQueue, TraceWriter log)
{
    myQueue.Add(myQueueItem + "(step 1)");
    myQueue.Add(myQueueItem + "(step 2)");
}
```

<!--
<a name="outfsharp"></a>
### Output sample in F# ## 
```fsharp

```
-->

<a name="outnodejs"></a>

### <a name="output-sample-in-nodejs"></a>Пример выходной привязки для Node.js

```javascript
module.exports = function(context) {
    context.bindings.myQueue = context.bindings.myQueueItem + "(next step)";
    context.done();
};
```

Отправка нескольких сообщений:

```javascript
module.exports = function(context) {
    context.bindings.myQueue = [];

    context.bindings.myQueueItem.push("(step 1)");
    context.bindings.myQueueItem.push("(step 2)");
    context.done();
};
```

## <a name="next-steps"></a>Дальнейшие действия
[!INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)]




<!--HONumber=Nov16_HO3-->



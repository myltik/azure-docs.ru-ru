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
ms.date: 01/18/2017
ms.author: chrande, glenga
translationtype: Human Translation
ms.sourcegitcommit: 770cac8809ab9f3d6261140333ec789ee1390daf
ms.openlocfilehash: bf9bd2a1b5acdf5a4a4f862bef693f8c60c63a33


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

Свойство `connection` должно включать в себя имя параметра приложения, содержащего строку подключения к службе хранилища. На портале Azure этот параметр приложения можно настроить на вкладке **Интеграция** при создании учетной записи хранения или выборе одной из имеющихся. Чтобы вручную создать этот параметр приложения, ознакомьтесь с разделом [Настройка параметров приложения-функции Azure](functions-how-to-use-azure-function-app-settings.md#manage-app-service-settings).

[Дополнительные параметры](https://github.com/Azure/azure-webjobs-sdk-script/wiki/host.json) можно указать в файле host.json для дальнейшей настройки триггеров очередей службы хранилища.  

### <a name="handling-poison-queue-messages"></a>Обработка подозрительных сообщений очереди
При сбое функции триггера очереди по умолчанию Функции Azure выполняют ее для заданного сообщения очереди еще пять раз (включая первую попытку). В случае сбоя после пяти попыток запуска Функции Azure добавляют сообщение в очередь службы хранилища *&lt;имя_превоначальной_очереди>-poison*. Можно написать функции для обработки сообщений из очереди подозрительных сообщений путем внесения их в журнал или отправки уведомления о необходимости ручного вмешательства. 

Чтобы вручную обрабатывать подозрительные сообщения, количество попыток обработки сообщения можно узнать, проверив значение свойства `dequeueCount` (ознакомьтесь с разделом [Метаданные триггера очереди](#meta)).

<a name="triggerusage"></a>

## <a name="trigger-usage"></a>Использование триггера
В функциях C# можно выполнить привязку к входному сообщению, используя именованный параметр в сигнатуре функции, например `<T> <name>`,
где `T` — это тип данных, в который требуется десериализировать данные, а `paramName` — это имя, указанное в [привязке триггера](#trigger). В функциях Node.js доступ к данным входного большого двоичного объекта можно получить, используя `context.bindings.<name>`.

Сообщение очереди можно десериализировать в один из следующих типов:

* [Object](https://msdn.microsoft.com/library/system.object.aspx): используется для сериализованных сообщений JSON. При объявлении пользовательского типа входных данных среда выполнения пытается десериализовать объект JSON. 
* Строка
* массив байтов;
* [CloudQueueMessage](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.queue.cloudqueuemessage.aspx) (только для C#).

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

Свойство `connection` должно включать в себя имя параметра приложения, содержащего строку подключения к службе хранилища. На портале Azure стандартный редактор на вкладке **Интеграция** автоматически настраивает этот параметр приложения при создании учетной записи хранения или выборе одной из имеющихся. Чтобы вручную создать этот параметр приложения, ознакомьтесь с разделом [Настройка параметров приложения-функции Azure](functions-how-to-use-azure-function-app-settings.md#manage-app-service-settings).

<a name="outputusage"></a>

## <a name="output-usage"></a>Использование выходной привязки
В функциях C# сообщение очереди создается с помощью именованного параметра `out` в сигнатуре функции, например `out <T> <name>`. В этом случае `T` — это тип данных, в который требуется сериализировать сообщение, а `paramName` — это имя, указанное в [выходной привязке](#output). В функциях Node.js доступ к выходным данным можно получить, используя `context.bindings.<name>`.

Сообщение очереди можно вывести, используя любой из типов данных в коде:

* Любой [Object](https://msdn.microsoft.com/library/system.object.aspx): `out MyCustomType paramName`.  
Используется для сериализации в JSON.  При объявлении пользовательского типа выходных данных среда выполнения пытается сериализовать объект в JSON. Если при выходе из функции выходной параметр имеет значение NULL, среда выполнения создает сообщение очереди в качестве пустого объекта.
* String: `out string paramName`.  
Используется для тестовых сообщений. Среда выполнения создает сообщение, только если при выходе из функции для параметра строки задано значение, отличное от NULL.
* Массив байтов: `out byte[]`. 

Эти дополнительные типы выходных данных поддерживают функцию C#:

* [CloudQueueMessage](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.queue.cloudqueuemessage.aspx): `out CloudQueueMessage`. 
* `ICollector<T>` или `IAsyncCollector<T>`, где `T` является одним из поддерживаемых типов.

<a name="outputsample"></a>

## <a name="output-sample"></a>Пример выходной привязки
Предположим, что у вас есть следующий файл function.json, определяющий [триггер очереди службы хранилища](functions-bindings-storage-queue.md), а также входные и выходные данные большого двоичного объекта службы хранилища:

Пример файла *function.json* для выходной привязки очереди службы хранилища, в котором используется ручной триггер и запись входных данных в сообщение очереди.

```json
{
  "bindings": [
    {
      "type": "manualTrigger",
      "direction": "in",
      "name": "input"
    },
    {
      "type": "queue",
      "name": "myQueueItem",
      "queueName": "myqueue",
      "connection": "my_storage_connection",
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
public static void Run(string input, out string myQueueItem, TraceWriter log)
{
    myQueueItem = "New message: " + input;
}
```

Отправка нескольких сообщений:

```cs
public static void Run(string input, ICollector<string> myQueueItem, TraceWriter log)
{
    myQueueItem.Add("Message 1: " + input);
    myQueueItem.Add("Message 2: " + "Some other message.");
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
    // Define a new message for the myQueueItem output binding.
    context.bindings.myQueueItem = "new message";
    context.done();
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

Пример функции, которая использует триггеры и привязки очереди службы хранилища, доступен в разделе [Создание функции Azure, подключенной к службе Azure](functions-create-an-azure-connected-function.md).

[!INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)]




<!--HONumber=Jan17_HO3-->



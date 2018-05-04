---
title: Привязки хранилища очередей Azure для службы "Функции Azure"
description: Узнайте, как использовать триггер службы хранилища очередей Azure и выходную привязку в службе "Функции Azure".
services: functions
documentationcenter: na
author: tdykstra
manager: cfowler
editor: ''
tags: ''
keywords: функции azure, функции, обработка событий, динамические вычисления, независимая архитектура
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 10/23/2017
ms.author: tdykstra
ms.custom: cc996988-fb4f-47
ms.openlocfilehash: d140822b47325e8749d3b2788b47cf820f720a39
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2018
---
# <a name="azure-queue-storage-bindings-for-azure-functions"></a>Привязки хранилища очередей Azure для службы "Функции Azure"

В этой статье рассматривается работа с привязками хранилища очередей Azure в службе "Функции Azure". Служба "Функции Azure" поддерживает привязки триггера и выходные привязки для очередей.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages"></a>Пакеты

Привязки служебной шины доступны в пакете NuGet [Microsoft.Azure.WebJobs](http://www.nuget.org/packages/Microsoft.Azure.WebJobs). Исходный код для пакета находится в репозитории GitHub [azure-webjobs-sdk](https://github.com/Azure/azure-webjobs-sdk/tree/master/src).

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

## <a name="trigger"></a>Триггер

Используйте триггер очереди для запуска функции при получении нового элемента очереди. Сообщение очереди предоставляется в качестве входных данных функции.

## <a name="trigger---example"></a>Пример триггера

Языковой пример см. в разделах:

* [C#](#trigger---c-example)
* [Скрипт C# (CSX)](#trigger---c-script-example)
* [JavaScript](#trigger---javascript-example)

### <a name="trigger---c-example"></a>Пример C# в триггере

В следующем примере показана [функция C#](functions-dotnet-class-library.md), которая выполняет опрос очереди `myqueue-items`, а затем делает запись в журнал при каждой обработке элемента очереди.

```csharp
public static class QueueFunctions
{
    [FunctionName("QueueTrigger")]
    public static void QueueTrigger(
        [QueueTrigger("myqueue-items")] string myQueueItem, 
        TraceWriter log)
    {
        log.Info($"C# function processed: {myQueueItem}");
    }
}
```

### <a name="trigger---c-script-example"></a>Пример скрипта C# в триггере

В следующем примере показана привязка триггера очереди в файле *function.json* и коде [сценария C# (CSX)](functions-reference-csharp.md), который использует привязку. Эта функция выполняет опрос очереди `myqueue-items`, а затем делает запись в журнал при каждой обработке элемента очереди.

Ниже показан файл *function.json*.

```json
{
    "disabled": false,
    "bindings": [
        {
            "type": "queueTrigger",
            "direction": "in",
            "name": "myQueueItem",
            "queueName": "myqueue-items",
            "connection":"MyStorageConnectionAppSetting"
        }
    ]
}
```

В разделе [Конфигурация](#trigger---configuration) описываются эти свойства.

Ниже приведен код скрипта C#.

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

В этом [разделе](#trigger---usage) показано свойство `myQueueItem`, имя которому назначает свойство `name` в function.json.  В разделе [Метаданные сообщений](#trigger---message-metadata) показаны все остальные переменные.

### <a name="trigger---javascript-example"></a>Пример JavaScript в триггере

В следующем примере показана привязка триггера очереди в файле *function.json* и [функция JavaScript](functions-reference-node.md), которая использует привязку. Эта функция выполняет опрос очереди `myqueue-items`, а затем делает запись в журнал при каждой обработке элемента очереди.

Ниже показан файл *function.json*.

```json
{
    "disabled": false,
    "bindings": [
        {
            "type": "queueTrigger",
            "direction": "in",
            "name": "myQueueItem",
            "queueName": "myqueue-items",
            "connection":"MyStorageConnectionAppSetting"
        }
    ]
}
```

В разделе [Конфигурация](#trigger---configuration) описываются эти свойства.

Ниже показан код JavaScript.

```javascript
module.exports = function (context) {
    context.log('Node.js queue trigger function processed work item', context.bindings.myQueueItem);
    context.log('queueTrigger =', context.bindingData.queueTrigger);
    context.log('expirationTime =', context.bindingData.expirationTime);
    context.log('insertionTime =', context.bindingData.insertionTime);
    context.log('nextVisibleTime =', context.bindingData.nextVisibleTime);
    context.log('id =', context.bindingData.id);
    context.log('popReceipt =', context.bindingData.popReceipt);
    context.log('dequeueCount =', context.bindingData.dequeueCount);
    context.done();
};
```

В этом [разделе](#trigger---usage) показано свойство `myQueueItem`, имя которому назначает свойство `name` в function.json.  В разделе [Метаданные сообщений](#trigger---message-metadata) показаны все остальные переменные.

## <a name="trigger---attributes"></a>Атрибуты триггера
 
В [библиотеках классов C#](functions-dotnet-class-library.md) используйте следующие атрибуты для настройки триггера очереди:

* [QueueTriggerAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/QueueTriggerAttribute.cs)

  Конструктор атрибута принимает имя очереди для отслеживания, как показано в следующем примере:

  ```csharp
  [FunctionName("QueueTrigger")]
  public static void Run(
      [QueueTrigger("myqueue-items")] string myQueueItem, 
      TraceWriter log)
  {
      ...
  }
  ```

  Чтобы указать учетную запись хранения, которую нужно использовать, можно задать свойство `Connection`, как показано в следующем примере.

  ```csharp
  [FunctionName("QueueTrigger")]
  public static void Run(
      [QueueTrigger("myqueue-items", Connection = "StorageConnectionAppSetting")] string myQueueItem, 
      TraceWriter log)
  {
      ....
  }
  ```
 
  Полный пример см. в разделе [Пример C# в триггере](#trigger---c-example).

* [StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs)

  Предоставляет еще один способ указать используемую учетную запись хранения. Конструктор принимает имя параметра приложения, содержащего строку подключения к службе хранилища. Атрибут может применяться на уровне класса, метода или параметра. Ниже показан пример уровня класса и метода.

  ```csharp
  [StorageAccount("ClassLevelStorageAppSetting")]
  public static class AzureFunctions
  {
      [FunctionName("QueueTrigger")]
      [StorageAccount("FunctionLevelStorageAppSetting")]
      public static void Run( //...
  {
      ...
  }
  ```

Используемая учетная запись хранения определяется в следующем порядке:

* Свойство `QueueTrigger` атрибута `Connection`.
* Атрибут `StorageAccount`, примененный к тому же параметру, что и `QueueTrigger`.
* Атрибут `StorageAccount`, примененный к функции.
* Атрибут `StorageAccount`, примененный к классу.
* Параметр приложения AzureWebJobsStorage.

## <a name="trigger---configuration"></a>Конфигурация триггера

В следующей таблице описываются свойства конфигурации привязки, которые задаются в файле *function.json* и атрибуте `QueueTrigger`.

|свойство function.json | Свойство атрибута |ОПИСАНИЕ|
|---------|---------|----------------------|
|**type** | Недоступно| Нужно задать значение `queueTrigger`. Это свойство задается автоматически при создании триггера на портале Azure.|
|**direction**| Недоступно | Только в файле *function.json*. Нужно задать значение `in`. Это свойство задается автоматически при создании триггера на портале Azure. |
|**name** | Недоступно |Имя переменной, представляющей очередь в коде функции.  | 
|**queueName** | **QueueName**| Имя очереди для опроса. | 
|**подключение** | **Connection** |Имя параметра приложения, содержащего строку подключения к службе хранилища, используемой для этой привязки. Если имя параметра приложения начинается с AzureWebJobs, можно указать только остальную часть имени. Например, если задать для `connection` значение MyStorage, среда выполнения службы "Функции" будет искать параметр приложения с именем AzureWebJobsMyStorage. Если оставить строку `connection` пустой, среда выполнения службы "Функции" будет использовать строку подключения к службе хранилища по умолчанию для параметра приложения с именем `AzureWebJobsStorage`.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="trigger---usage"></a>Использование триггера
 
В коде и сценарии C# для доступа к данным сообщения используйте параметр метода, например `string paramName`. В скрипте C# `paramName` — это значение, заданное в свойстве `name` файла *function.json*. Вы можете выполнить привязку к одному из следующих типов:

* Объект. Среда выполнения службы "Функции" десериализует полезные данные JSON в экземпляр произвольного класса, определенного в коде. 
* `string`
* `byte[]`
* [CloudQueueMessage]

В JavaScript используйте `context.bindings.<name>`, чтобы получить доступ к полезным данным элемента очереди. Если полезные данные представлены в виде JSON, они десериализируются в объект.

## <a name="trigger---message-metadata"></a>Метаданные сообщения триггера

Триггер очереди предоставляет несколько [свойств метаданных](functions-triggers-bindings.md#binding-expressions---trigger-metadata). Эти свойства можно использовать как часть выражений привязки в других привязках или как параметры в коде. Эти значения имеют ту же семантику, что и [CloudQueueMessage](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.queue.cloudqueuemessage).

|Свойство|type|ОПИСАНИЕ|
|--------|----|-----------|
|`QueueTrigger`|`string`|Полезные данные очереди (если это допустимая строка). Если полезные данные очереди сообщений представлены в виде строки, значение `QueueTrigger` совпадает со значением переменной, имя которой назначено свойством `name` в файле *function.json*.|
|`DequeueCount`|`int`|Количество раз, когда сообщение было выведено из очереди.|
|`ExpirationTime`|`DateTimeOffset`|Время истечения срока действия сообщения.|
|`Id`|`string`|Идентификатор сообщения в очереди.|
|`InsertionTime`|`DateTimeOffset`|Время, когда сообщение было добавлено в очередь.|
|`NextVisibleTime`|`DateTimeOffset`|Время, когда сообщение станет видимым в следующий раз.|
|`PopReceipt`|`string`|Уведомление о получении сообщения.|

## <a name="trigger---poison-messages"></a>Триггеры сообщений о сбое

При сбое функции триггера очереди по умолчанию служба "Функции Azure" выполняет ее для заданного сообщения очереди еще пять раз (включая первую попытку). В случае сбоя всех пяти попыток среда выполнения функций добавляет сообщение в очередь с именем *&lt;имя_первоначальной_очереди>-poison*. Можно написать функции для обработки сообщений из очереди подозрительных сообщений путем внесения их в журнал или отправки уведомления о необходимости ручного вмешательства.

Для обработки сообщений о сбоях вручную проверьте значение [dequeueCount](#trigger---message-metadata) сообщения очереди.

## <a name="trigger---polling-algorithm"></a>Триггер алгоритма опроса

В триггере очереди реализован алгоритм случайной экспоненциальной отсрочки, который позволяет уменьшить влияние опроса очереди ожидающих задач на затраты на транзакции хранилища.  При обнаружении сообщения среда выполнения ожидает в течение двух секунд, а затем проверяет, не поступило ли еще одно сообщение. Если сообщение не найдено, она ожидает около четырех секунд перед повторной попыткой. После последующих неудачных попыток получения сообщения очереди время ожидания продолжает увеличиваться, пока не достигнет максимального времени ожидания, по умолчанию — одна минута. Максимальное время ожидания настраивается при помощи свойства `maxPollingInterval` в [файле host.json](functions-host-json.md#queues).

## <a name="trigger---concurrency"></a>Триггер параллелизма

При наличии нескольких сообщений, ожидающих в очереди, триггер очереди извлекает пакет сообщений и в параллельном режиме вызывает экземпляры функций для обработки. По умолчанию в пакете содержится 16 сообщений. Когда число обрабатываемых сообщений снижается до 8, среда выполнения получает следующий пакет и начинает обработку содержащихся в нем сообщений. Поэтому максимальное количество сообщений, одновременно обрабатываемых каждой функцией на одной виртуальной машине, равно 24. Это ограничение применяется отдельно к каждой функции, активируемой с помощью очереди, на каждой виртуальной машине. Если приложение-функция масштабируется на несколько виртуальных машин, каждая машина будет ожидать триггеров и пытаться выполнить функции. Например, если приложение-функция масштабируется на 3 виртуальные машины, то максимальное количество параллельных экземпляров одной функции, активируемой с помощью очереди, равно 72.

Размер пакета и порог для получения нового пакета настраиваются в [файле host.json](functions-host-json.md#queues). Если в приложении-функции требуется свести к минимуму параллельное выполнение функций, активируемых с помощью очереди, вы можете задать для размера пакета значение 1. Этот параметр позволяет исключить параллелизм только при условии, что приложение-функция выполняется на одной виртуальной машине. 

Триггер очереди автоматически предотвращает многократную обработку функцией сообщения из очереди. Не обязательно создавать идемпотентные функции.

## <a name="trigger---hostjson-properties"></a>Свойства host.json в триггере

В файле [host.json](functions-host-json.md#queues) содержатся параметры, управляющие поведением очереди триггера.

[!INCLUDE [functions-host-json-queues](../../includes/functions-host-json-queues.md)]

## <a name="output"></a>Выходные данные

Используйте выходную привязку хранилища очередей Azure, чтобы записать сообщения в очередь.

## <a name="output---example"></a>Пример выходных данных

Языковой пример см. в разделах:

* [C#](#output---c-example)
* [Скрипт C# (CSX)](#output---c-script-example)
* [JavaScript](#output---javascript-example)

### <a name="output---c-example"></a>Пример выходных данных C#

В следующем примере показана [функция C#](functions-dotnet-class-library.md), которая создает сообщения очереди для каждого полученного HTTP-запроса.

```csharp
[StorageAccount("AzureWebJobsStorage")]
public static class QueueFunctions
{
    [FunctionName("QueueOutput")]
    [return: Queue("myqueue-items")]
    public static string QueueOutput([HttpTrigger] dynamic input,  TraceWriter log)
    {
        log.Info($"C# function processed: {input.Text}");
        return input.Text;
    }
}
```

### <a name="output---c-script-example"></a>Пример выходных данных скрипта C#

В следующем примере показана привязка триггера HTTP в файле *function.json* и коде [сценария C# (CSX)](functions-reference-csharp.md), который использует привязку. Функция создает элемент очереди с полезными данными объекта **CustomQueueMessage** для каждого полученного HTTP-запроса.

Ниже показан файл *function.json*.

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
      "connection": "MyStorageConnectionAppSetting",
    }
  ]
}
``` 

В разделе [Конфигурация](#output---configuration) описываются эти свойства.

Ниже приведен код скрипта C#, который создает одно сообщение очереди.

```cs
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

За один раз можно отправить несколько сообщений с помощью параметра `ICollector` или `IAsyncCollector`. Ниже приведен код скрипта C#, который отправляет несколько сообщений (одно — с данными HTTP-запроса, а другое — с кодовыми значениями).

```cs
public static void Run(
    CustomQueueMessage input, 
    ICollector<CustomQueueMessage> myQueueItems, 
    TraceWriter log)
{
    myQueueItems.Add(input);
    myQueueItems.Add(new CustomQueueMessage { PersonName = "You", Title = "None" });
}
```

### <a name="output---javascript-example"></a>Пример выходных данных JavaScript

В следующем примере показана привязка триггера HTTP в файле *function.json* и [функция JavaScript](functions-reference-node.md), которая использует привязку. Эта функция создает элемент очереди для каждого полученного HTTP-запроса.

Ниже показан файл *function.json*.

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
      "connection": "MyStorageConnectionAppSetting",
    }
  ]
}
``` 

В разделе [Конфигурация](#output---configuration) описываются эти свойства.

Ниже показан код JavaScript.

```javascript
module.exports = function (context, input) {
    context.done(null, input.body);
};
```

Вы можете отправить несколько сообщений одновременно, определив массив сообщений для выходной привязки `myQueueItem`. Следующий код JavaScript отправляет два сообщения очереди с четко фиксированными значениями для каждого полученного HTTP-запроса.

```javascript
module.exports = function(context) {
    context.bindings.myQueueItem = ["message 1","message 2"];
    context.done();
};
```

## <a name="output---attributes"></a>Выходные атрибуты
 
В [библиотеках классов C#](functions-dotnet-class-library.md) используйте [QueueAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/QueueAttribute.cs).

Этот атрибут применяется к параметру `out` или возвращаемому значению функции. Конструктор атрибута принимает имя очереди, как показано в следующем примере:

```csharp
[FunctionName("QueueOutput")]
[return: Queue("myqueue-items")]
public static string Run([HttpTrigger] dynamic input,  TraceWriter log)
{
    ...
}
```

Чтобы указать учетную запись хранения, которую нужно использовать, можно задать свойство `Connection`, как показано в следующем примере.

```csharp
[FunctionName("QueueOutput")]
[return: Queue("myqueue-items", Connection = "StorageConnectionAppSetting")]
public static string Run([HttpTrigger] dynamic input,  TraceWriter log)
{
    ...
}
```

Полный пример см. в разделе [Пример выходных данных C#](#output---c-example).

Чтобы указать учетную запись хранения на уровне класса, метода или параметра, можно использовать атрибут `StorageAccount`. Дополнительные сведения см. в разделе [Атрибуты триггера для предкомпилированного кода C#](#trigger---attribute).

## <a name="output---configuration"></a>Выходная конфигурация

В следующей таблице описываются свойства конфигурации привязки, которые задаются в файле *function.json* и атрибуте `Queue`.

|свойство function.json | Свойство атрибута |ОПИСАНИЕ|
|---------|---------|----------------------|
|**type** | Недоступно | Нужно задать значение `queue`. Это свойство задается автоматически при создании триггера на портале Azure.|
|**direction** | Недоступно | Нужно задать значение `out`. Это свойство задается автоматически при создании триггера на портале Azure. |
|**name** | Недоступно | Имя переменной, представляющей очередь в коде функции. Задайте значение `$return`, ссылающееся на возвращаемое значение функции.| 
|**queueName** |**QueueName** | Имя очереди. | 
|**подключение** | **Connection** |Имя параметра приложения, содержащего строку подключения к службе хранилища, используемой для этой привязки. Если имя параметра приложения начинается с AzureWebJobs, можно указать только остальную часть имени. Например, если задать для `connection` значение MyStorage, среда выполнения службы "Функции" будет искать параметр приложения с именем AzureWebJobsMyStorage. Если оставить строку `connection` пустой, среда выполнения службы "Функции" будет использовать строку подключения к службе хранилища по умолчанию для параметра приложения с именем `AzureWebJobsStorage`.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>Использование выходной привязки
 
В коде и скрипте C# запишите одно сообщение очереди с помощью параметра метода, например `out T paramName`. В скрипте C# `paramName` — это значение, заданное в свойстве `name` файла *function.json*. Вы можете использовать этот метод типа возвращаемого значения вместо параметра `out`. `T` может быть любого из следующих типов:

* Объект, сериализуемый как JSON
* `string`
* `byte[]`
* [CloudQueueMessage] 

В коде и скрипте C# запишите несколько сообщений очереди с помощью одного из следующих типов: 

* `ICollector<T>` или `IAsyncCollector<T>`
* [CloudQueue](/dotnet/api/microsoft.windowsazure.storage.queue.cloudqueue).

В функциях JavaScript используйте `context.bindings.<name>`, чтобы получить доступ к выходной очереди сообщений. Строку или сериализуемый объект JSON можно использовать для полезных данных элемента очереди.


## <a name="exceptions-and-return-codes"></a>Исключения и коды возврата

| Привязка |  Справочные материалы |
|---|---|
| Очередь | [Коды ошибок очередей](https://docs.microsoft.com/rest/api/storageservices/queue-service-error-codes) |
| Большой двоичный объект, таблица, очередь | [Коды ошибок хранилища](https://docs.microsoft.com/rest/api/storageservices/fileservices/common-rest-api-error-codes) |
| Большой двоичный объект, таблица, очередь |  [Устранение неполадок](https://docs.microsoft.com/rest/api/storageservices/fileservices/troubleshooting-api-operations) |

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Создание функции, активируемой хранилищем очередей Azure](functions-create-storage-queue-triggered-function.md)

> [!div class="nextstepaction"]
> [Добавление сообщений в очередь службы хранилища Azure с помощью Функций](functions-integrate-storage-queue-output-binding.md)

> [!div class="nextstepaction"]
> [Основные понятия триггеров и привязок в Функциях Azure](functions-triggers-bindings.md)

<!-- LINKS -->

[CloudQueueMessage]: /dotnet/api/microsoft.windowsazure.storage.queue.cloudqueuemessage

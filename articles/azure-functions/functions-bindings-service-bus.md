<properties
	pageTitle="Триггеры и привязки служебной шины в функциях Azure | Microsoft Azure"
	description="Узнайте, как использовать триггеры и привязки служебной шины Azure в функциях Azure."
	services="functions"
	documentationCenter="na"
	authors="christopheranderson"
	manager="erikre"
	editor=""
	tags=""
	keywords="функции azure, функции, обработка событий, динамические вычисления, независимая архитектура"/>

<tags
	ms.service="functions"
	ms.devlang="multiple"
	ms.topic="reference"
	ms.tgt_pltfrm="multiple"
	ms.workload="na"
	ms.date="08/22/2016"
	ms.author="chrande; glenga"/>

# Триггеры и привязки служебной шины в функциях Azure, используемые для очередей и разделов

[AZURE.INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

Эта статья объясняет, как настроить и запрограммировать триггеры и привязки служебной шины в функциях Azure.

[AZURE.INCLUDE [общие сведения](../../includes/functions-bindings-intro.md)]

## <a id="sbtrigger"></a> Триггер служебной шины для очереди или раздела

#### function.json

Файл *function.json* определяет следующие свойства:

- `name` — имя переменной, используемой в коде функции для очереди или раздела либо для сообщения очереди или раздела.
- `queueName` — имя очереди для опроса (только для триггера очереди).
- `topicName` — имя раздела для опроса (только для триггера раздела).
- `subscriptionName` — имя подписки (только для триггера раздела).
- `connection` — имя параметра приложения, содержащего строку подключения к служебной шине. Строка подключения указывается для пространства имен служебной шины, и она не должна ограничиваться определенной очередью или разделом. Если строка подключения не имеет прав на управление, задайте свойство `accessRights`. Если оставить свойство `connection` пустым, привязка или триггер будут использовать строку подключения служебной шины по умолчанию для приложения-функции. Она задается в параметре приложения AzureWebJobsServiceBus.
- `accessRights` — указывает права доступа для строки подключения. Значение по умолчанию — `manage`. Если используется строка подключения, которая не предоставляет разрешения на управление, задайте здесь значение `listen`. В противном случае выполнение операций, для которых требуются права на управление, в среде выполнения функций может завершиться ошибкой.
- `type` — для этого свойства нужно задать значение *serviceBusTrigger*.
- `direction` — для этого свойства необходимо задать значение *in*.

Пример файла *function.json* для триггера очереди служебной шины:

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

#### Пример кода C#, который обрабатывает сообщение очереди служебной шины

```csharp
public static void Run(string myQueueItem, TraceWriter log)
{
    log.Info($"C# ServiceBus queue trigger function processed message: {myQueueItem}");
}
```

#### Пример кода F#, который обрабатывает сообщение очереди служебной шины

```fsharp
let Run(myQueueItem: string, log: TraceWriter) =
    log.Info(sprintf "F# ServiceBus queue trigger function processed message: %s" myQueueItem)
```

#### Пример кода Node.js, который обрабатывает сообщение очереди служебной шины

```javascript
module.exports = function(context, myQueueItem) {
    context.log('Node.js ServiceBus queue trigger function processed message', myQueueItem);
    context.done();
};
```

#### Поддерживаемые типы

Сообщение очереди служебной шины можно десериализировать в один из следующих типов:

* объект (JSON);
* string
* массив байтов;
* `BrokeredMessage` (C#)

#### <a id="sbpeeklock"></a> Поведение PeekLock

Среда выполнения функций получает сообщение в режиме `PeekLock` и вызывает для сообщения метод `Complete`, если функция выполнена успешно, или метод `Abandon` в случае сбоя. Если функция выполняется дольше времени ожидания `PeekLock`, блокировка возобновляется автоматически.

#### <a id="sbpoison"></a> Обработка подозрительных сообщений

В служебной шине выполняется собственная обработка подозрительных сообщений, которую нельзя контролировать или настраивать с помощью конфигурации или кода функций Azure.

#### <a id="sbsinglethread"></a> Однопоточная обработка

По умолчанию в среде выполнения функций одновременно обрабатываются несколько сообщений очереди. Чтобы среда выполнения обрабатывала в любой момент времени только одно сообщение очереди или раздела, для свойства `serviceBus.maxConcurrrentCalls` в файле *host.json* нужно задать значение 1. Дополнительные сведения о файле *host.json* см. в разделе [Структура папок](functions-reference.md#folder-structure) статьи "Справочник разработчика по функциям Azure" и в разделе [host.json](https://github.com/Azure/azure-webjobs-sdk-script/wiki/host.json) в репозитории вики WebJobs.Script.

## <a id="sboutput"></a> Выходная привязка для очереди или раздела служебной шины

#### function.json

Файл *function.json* определяет следующие свойства:

- `name` — имя переменной, используемой в коде функции для очереди или сообщения очереди.
- `queueName` — имя очереди для опроса (только для триггера очереди).
- `topicName` — имя раздела для опроса (только для триггера раздела).
- `subscriptionName` — имя подписки (только для триггера раздела).
- `connection` — то же, что и для триггера служебной шины.
- `accessRights` — указывает права доступа для строки подключения. Значение по умолчанию — `manage`. Если используется строка подключения, которая не предоставляет разрешения на управление, задайте здесь значение `send`. В противном случае выполнение операций, для которых требуются права на управление (например, для создания очереди), в среде выполнения функций может завершиться ошибкой.
- `type` — для этого свойства нужно задать значение *serviceBus*.
- `direction` — для этого свойства необходимо задать значение *out*.

Пример файла *function.json* с использованием триггера таймера для записи сообщений очереди служебной шины:

```JSON
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

#### Поддерживаемые типы

Функции Azure могут создать сообщение очереди служебной шины из следующих типов:

* объект (всегда создает сообщение JSON; создает сообщение с пустым объектом, если по завершении функции значение — null);
* строка (создает сообщение, если по завершении функции значение не равно null);
* массив байтов (действует как строка);
* `BrokeredMessage` (C#, действует как строка).

Чтобы создать несколько сообщений в функции C#, можно использовать `ICollector<T>` или `IAsyncCollector<T>`. Сообщение создается при вызове метода `Add`.

#### Примеры кода C# для создания сообщений очереди служебной шины

```csharp
public static void Run(TimerInfo myTimer, TraceWriter log, out string outputSbQueue)
{
	string message = $"Service Bus queue message created at: {DateTime.Now}";
    log.Info(message); 
    outputSbQueue = message;
}
```

```csharp
public static void Run(TimerInfo myTimer, TraceWriter log, ICollector<string> outputSbQueue)
{
	string message = $"Service Bus queue message created at: {DateTime.Now}";
    log.Info(message); 
    outputSbQueue.Add("1 " + message);
    outputSbQueue.Add("2 " + message);
}
```

#### Пример кода F#, который создает сообщение очереди служебной шины

```fsharp
let Run(myTimer: TimerInfo, log: TraceWriter, outputSbQueue: byref<string>) =
    let message = sprintf "Service Bus queue message created at: %s" (DateTime.Now.ToString())
    log.Info(message)
    outputSbQueue = message
```

#### Пример кода Node.js, которые создает сообщение очереди служебной шины

```javascript
module.exports = function (context, myTimer) {
    var timeStamp = new Date().toISOString();
    
    if(myTimer.isPastDue)
    {
        context.log('Node.js is running late!');
    }
    var message = 'Service Bus queue message created at ' + timeStamp;
    context.log(message);   
    context.bindings.outputSbQueueMsg = message;
    context.done();
};
```

## Дальнейшие действия

[AZURE.INCLUDE [дальнейшие действия](../../includes/functions-bindings-next-steps.md)]

<!---HONumber=AcomDC_0921_2016-->
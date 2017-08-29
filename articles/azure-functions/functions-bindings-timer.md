---
title: "Триггеры с таймерами | Документация Майкрософт"
description: "Узнайте, как использовать триггеры с таймерами в функциях Azure."
services: functions
documentationcenter: na
author: christopheranderson
manager: erikre
editor: 
tags: 
keywords: "функции azure, функции, обработка событий, динамические вычисления, независимая архитектура"
ms.assetid: d2f013d1-f458-42ae-baf8-1810138118ac
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 02/27/2017
ms.author: glenga
ms.custom: 
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: 6a97ab8508f889b77d064a5da70e3c726d62900c
ms.contentlocale: ru-ru
ms.lasthandoff: 08/21/2017

---
# <a name="azure-functions-timer-trigger"></a>Триггеры с таймерами в функциях Azure

[!INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

В этой статье описывается настройка и программирование триггеров с таймерами в Функциях Azure. Функции Azure поддерживают привязку триггеров к таймерам, благодаря чему вы можете выполнять код функции по определенному расписанию. 

Триггер с таймером поддерживает развертывание с несколькими экземплярами. Один экземпляр функции конкретного таймера выполняется для всех экземпляров.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

<a id="trigger"></a>

## <a name="timer-trigger"></a>Триггер таймера
Триггер с таймером для функции использует следующий объект JSON в массиве `bindings` файла function.json:

```json
{
    "schedule": "<CRON expression - see below>",
    "name": "<Name of trigger parameter in function signature>",
    "type": "timerTrigger",
    "direction": "in"
}
```

Значение `schedule` представляет собой [выражение CRON](http://en.wikipedia.org/wiki/Cron#CRON_expression) с шестью полями: 

    {second} {minute} {hour} {day} {month} {day-of-week}
&nbsp;
>[!NOTE]   
>Во многих выражениях CRON в сети отсутствует поле `{second}`. Поэтому если скопировать текст в одном из таких выражений, понадобится добавить дополнительное поле `{second}`. Конкретные примеры см. в разделе [Примеры расписания](#examples) ниже.

Часовой пояс по умолчанию, используемый с выражениями CRON — время в формате UTC. Если нужно использовать другой часовой пояс в выражении CRON, создайте новый параметр приложения с именем `WEBSITE_TIME_ZONE` для приложения-функции. В качестве значения задайте имя нужного часового пояса, как показано в статье [Microsoft Time Zone Index](https://msdn.microsoft.com/library/ms912391.aspx) (Индексы часовых поясов Майкрософт). 

Например, *восточное поясное время* — UTC-05:00. Если требуется, чтобы триггер с таймером активировался в 10:00 по восточному поясному времени каждый день, можно использовать следующее выражение CRON, в котором учитывается часовой пояс UTC:

```json
"schedule": "0 0 15 * * *",
``` 

Кроме того, можно добавить новый параметр приложения с именем `WEBSITE_TIME_ZONE` для приложения-функции и задать **Восточное поясное время** в качестве значения.  Затем можно использовать следующее выражение CRON для 10:00 по восточному поясному времени: 

```json
"schedule": "0 0 10 * * *",
``` 


<a name="examples"></a>

## <a name="schedule-examples"></a>Примеры расписания
Ниже приведены некоторые примеры выражений CRON, которые можно использовать для свойства `schedule`. 

Активация через каждые пять минут:

```json
"schedule": "0 */5 * * * *"
```

Активация через каждый час:

```json
"schedule": "0 0 * * * *",
```

Активация через каждые 2 часа.

```json
"schedule": "0 0 */2 * * *",
```

Активация каждый час с 9:00 до 17:00:

```json
"schedule": "0 0 9-17 * * *",
```

Активация в 9:30 каждый день:

```json
"schedule": "0 30 9 * * *",
```

Активация в 9:30 каждый будний день:

```json
"schedule": "0 30 9 * * 1-5",
```

<a name="usage"></a>

## <a name="trigger-usage"></a>Использование триггера
При вызове функции триггера с таймером [объект таймера](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/TimerInfo.cs) передается в функцию. Далее представлен JSON в качестве примера объекта таймера. 

```json
{
    "Schedule":{
    },
    "ScheduleStatus": {
        "Last":"2016-10-04T10:15:00.012699+00:00",
        "Next":"2016-10-04T10:20:00+00:00"
    },
    "IsPastDue":false
}
```

<a name="sample"></a>

## <a name="trigger-sample"></a>Пример триггера
Предположим, что у вас есть следующий триггер с таймером в массиве `bindings` файла function.json:

```json
{
    "schedule": "0 */5 * * * *",
    "name": "myTimer",
    "type": "timerTrigger",
    "direction": "in"
}
```

Ознакомьтесь с примером для конкретного языка, считывающим объект таймера, чтобы определить, опаздывает ли он.

* [C#](#triggercsharp)
* [F#](#triggerfsharp)
* [Node.js](#triggernodejs)

<a name="triggercsharp"></a>

### <a name="trigger-sample-in-c"></a>Пример триггера на языке C# #
```csharp
public static void Run(TimerInfo myTimer, TraceWriter log)
{
    if(myTimer.IsPastDue)
    {
        log.Info("Timer is running late!");
    }
    log.Info($"C# Timer trigger function executed at: {DateTime.Now}" );  
}
```

<a name="triggerfsharp"></a>

### <a name="trigger-sample-in-f"></a>Пример триггера на языке F# #
```fsharp
let Run(myTimer: TimerInfo, log: TraceWriter ) =
    if (myTimer.IsPastDue) then
        log.Info("F# function is running late.")
    let now = DateTime.Now.ToLongTimeString()
    log.Info(sprintf "F# function executed at %s!" now)
```

<a name="triggernodejs"></a>

### <a name="trigger-sample-in-nodejs"></a>Пример триггера для Node.js
```JavaScript
module.exports = function (context, myTimer) {
    var timeStamp = new Date().toISOString();

    if(myTimer.isPastDue)
    {
        context.log('Node.js is running late!');
    }
    context.log('Node.js timer trigger function ran!', timeStamp);   

    context.done();
};
```

## <a name="next-steps"></a>Дальнейшие действия
[!INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)]



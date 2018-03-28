---
title: Триггеры таймера для службы "Функции Azure"
description: Узнайте, как использовать триггеры с таймерами в функциях Azure.
services: functions
documentationcenter: na
author: tdykstra
manager: cfowler
editor: ''
tags: ''
keywords: функции azure, функции, обработка событий, динамические вычисления, независимая архитектура
ms.assetid: d2f013d1-f458-42ae-baf8-1810138118ac
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 02/27/2017
ms.author: tdykstra
ms.custom: ''
ms.openlocfilehash: bd1a2643d9faf65d664c786169c38f01767fb7e5
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/16/2018
---
# <a name="timer-trigger-for-azure-functions"></a>Триггеры таймера для службы "Функции Azure" 

В этой статье описывается, как работать с триггерами таймера в службе "Функции Azure". Триггер таймера позволяет выполнять функцию по расписанию. 

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages"></a>Пакеты

Триггер таймера доступен в пакете NuGet [Microsoft.Azure.WebJobs.Extensions](http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions). Исходный код для пакета находится в репозитории GitHub [azure-webjobs-sdk-extensions](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/).

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

## <a name="example"></a>Пример

Языковой пример см. в разделах:

* [C#](#trigger---c-example)
* [Скрипт C# (CSX)](#trigger---c-script-example)
* [F#](#trigger---f-example)
* [JavaScript](#trigger---javascript-example)

### <a name="c-example"></a>Пример C#

В следующем примере показана [функция C#](functions-dotnet-class-library.md), которая выполняется каждые пять минут.

```cs
[FunctionName("TimerTriggerCSharp")]
public static void Run([TimerTrigger("0 */5 * * * *")]TimerInfo myTimer, TraceWriter log)
{
    log.Info($"C# Timer trigger function executed at: {DateTime.Now}");
}
```

### <a name="c-script-example"></a>Пример сценария C#

В следующем примере показаны привязка триггера таймера в файле *function.json* и [функция сценария C#](functions-reference-csharp.md), которая использует эту привязку. Эта функция выполняет запись в журнал, указывая, когда ее вызов выполняется из-за пропущенного запуска по расписанию.

Данные привязки в файле *function.json*:

```json
{
    "schedule": "0 */5 * * * *",
    "name": "myTimer",
    "type": "timerTrigger",
    "direction": "in"
}
```

Ниже приведен код скрипта C#.

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

### <a name="f-example"></a>Пример F#

В следующем примере показаны привязка триггера таймера в файле *function.json* и [функция сценария F#](functions-reference-fsharp.md), которая использует эту привязку. Эта функция выполняет запись в журнал, указывая, когда ее вызов выполняется из-за пропущенного запуска по расписанию.

Данные привязки в файле *function.json*:

```json
{
    "schedule": "0 */5 * * * *",
    "name": "myTimer",
    "type": "timerTrigger",
    "direction": "in"
}
```

Ниже приведен код сценария F#.

```fsharp
let Run(myTimer: TimerInfo, log: TraceWriter ) =
    if (myTimer.IsPastDue) then
        log.Info("F# function is running late.")
    let now = DateTime.Now.ToLongTimeString()
    log.Info(sprintf "F# function executed at %s!" now)
```

### <a name="javascript-example"></a>Пример JavaScript

В следующем примере показаны привязка триггера таймера в файле *function.json* и [функция JavaScript](functions-reference-node.md), которая использует эту привязку. Эта функция выполняет запись в журнал, указывая, когда ее вызов выполняется из-за пропущенного запуска по расписанию.

Данные привязки в файле *function.json*:

```json
{
    "schedule": "0 */5 * * * *",
    "name": "myTimer",
    "type": "timerTrigger",
    "direction": "in"
}
```

Ниже показан код сценария JavaScript.

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

## <a name="attributes"></a>Атрибуты

В [библиотеках классов C#](functions-dotnet-class-library.md) используйте [TimerTriggerAttribute](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/TimerTriggerAttribute.cs).

Конструктор атрибута принимает выражение CRON, как показано в следующем примере.

```csharp
[FunctionName("TimerTriggerCSharp")]
public static void Run([TimerTrigger("0 */5 * * * *")]TimerInfo myTimer, TraceWriter log)
{
   ...
}
 ```

Можно указать `TimeSpan` вместо выражения CRON, если ваше приложение-функция запускается в рамках плана службы приложений (а не плана потребления).

Полный пример см. в разделе [Пример C#](#c-example).

## <a name="configuration"></a>Параметр Configuration

В следующей таблице описываются свойства конфигурации привязки, которые задаются в файле *function.json* и атрибуте `TimerTrigger`.

|свойство function.json | Свойство атрибута |ОПИСАНИЕ|
|---------|---------|----------------------|
|**type** | Недоступно | Этому свойству необходимо присвоить значение "timerTrigger". Это свойство задается автоматически при создании триггера на портале Azure.|
|**direction** | Недоступно | Для этого свойства необходимо задать значение "in". Это свойство задается автоматически при создании триггера на портале Azure. |
|**name** | Недоступно | Имя переменной, представляющей объект таймера в коде функции. | 
|**schedule**|**ScheduleExpression**|В плане потребления можно определить расписания с помощью выражений CRON. Если используется план службы приложений, вы также можете использовать строку `TimeSpan`. В следующих разделах описано, как использовать выражения CRON. Можно поместить выражение расписания в параметр приложения и присвоить этому свойству значение, заключенное в знаки **%**, например: "%NameOfAppSettingWithCRONExpression%". |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

### <a name="cron-format"></a>Формат CRON 

[Выражение CRON](http://en.wikipedia.org/wiki/Cron#CRON_expression) для триггера таймера службы "Функции Azure" содержит следующие шесть полей. 

```
{second} {minute} {hour} {day} {month} {day-of-week}
```

>[!NOTE]   
>Во многих выражениях CRON в Интернете отсутствует поле `{second}`. При копировании такого выражения добавьте отсутствующее поле `{second}`.

### <a name="cron-time-zones"></a>Часовые пояса CRON

Часовой пояс по умолчанию, используемый с выражениями CRON — время в формате UTC. Если нужно использовать другой часовой пояс в выражении CRON, создайте новый параметр приложения с именем `WEBSITE_TIME_ZONE` для приложения-функции. В качестве значения задайте имя нужного часового пояса, как показано в статье [Microsoft Time Zone Index](https://technet.microsoft.com/library/cc749073(v=ws.10).aspx) (Индексы часовых поясов Майкрософт). 

Например, *восточное поясное время* — UTC-05:00. Если требуется, чтобы триггер с таймером активировался в 10:00 по восточному поясному времени каждый день, можно использовать следующее выражение CRON, в котором учитывается часовой пояс UTC:

```json
"schedule": "0 0 15 * * *",
``` 

Кроме того, можно добавить новый параметр приложения с именем `WEBSITE_TIME_ZONE` для приложения-функции и задать **Восточное поясное время** в качестве значения.  Затем можно использовать следующее выражение CRON для 10:00 по восточному поясному времени: 

```json
"schedule": "0 0 10 * * *",
``` 
### <a name="cron-examples"></a>Примеры CRON

Ниже приведены примеры выражений CRON, которые можно использовать для триггера таймера в службе "Функции Azure". 

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

## <a name="usage"></a>Использование

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

## <a name="scale-out"></a>Горизонтальное масштабирование

Триггер с таймером поддерживает развертывание с несколькими экземплярами. Один экземпляр функции конкретного таймера выполняется для всех экземпляров.

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Перейдите к краткому руководству по использованию триггера таймера](functions-create-scheduled-function.md)

> [!div class="nextstepaction"]
> [Основные понятия триггеров и привязок в Функциях Azure](functions-triggers-bindings.md)

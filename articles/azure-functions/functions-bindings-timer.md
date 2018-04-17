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
ms.openlocfilehash: 89469af2b1d02ef00fc347e47719956885e7f142
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2018
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
    if(myTimer.IsPastDue)
    {
        log.Info("Timer is running late!");
    }
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

Конструктор атрибута принимает выражение CRON или `TimeSpan`. `TimeSpan` можно использовать, только если приложение-функция выполняется в рамках плана службы приложений. В приведенном ниже примере показано выражение CRON.

```csharp
[FunctionName("TimerTriggerCSharp")]
public static void Run([TimerTrigger("0 */5 * * * *")]TimerInfo myTimer, TraceWriter log)
{
    if (myTimer.IsPastDue)
    {
        log.Info("Timer is running late!");
    }
    log.Info($"C# Timer trigger function executed at: {DateTime.Now}");
}
 ```

## <a name="configuration"></a>Параметр Configuration

В следующей таблице описываются свойства конфигурации привязки, которые задаются в файле *function.json* и атрибуте `TimerTrigger`.

|свойство function.json | Свойство атрибута |ОПИСАНИЕ|
|---------|---------|----------------------|
|**type** | Недоступно | Этому свойству необходимо присвоить значение "timerTrigger". Это свойство задается автоматически при создании триггера на портале Azure.|
|**direction** | Недоступно | Для этого свойства необходимо задать значение "in". Это свойство задается автоматически при создании триггера на портале Azure. |
|**name** | Недоступно | Имя переменной, представляющей объект таймера в коде функции. | 
|**schedule**|**ScheduleExpression**|Значение [выражения CRON](#cron-expressions) или [TimeSpan](#timespan). `TimeSpan` можно использовать только для приложения-функции, которая работает в плане службы приложений. Вы можете поместить выражение расписания в параметр приложения и присвоить этому свойству имя параметра приложения, заключенное в знаки **%**, например: "%NameOfAppSettingWithScheduleExpression%". |
|**runOnStartup**|**RunOnStartup**|Если настроено значение `true`, функция вызывается при запуске среды выполнения. Например, среда выполнения запускается, когда приложение-функция выходит из спящего режима (в который она перешла из-за отсутствия активности), когда приложение-функция перезапускается из-за изменения функции или когда приложение-функция масштабируется. Поэтому для **runOnStartup** редко устанавливается значение `true`, так как он будет выполнять код в очень непредсказуемое время. Если вам нужно запустить функцию за пределами расписания таймера, можно создать вторую функцию с другим типом триггера и совместно использовать код между ними. Например, для запуска в развертывании можно [настроить развертывание](https://github.com/projectkudu/kudu/wiki/Customizing-deployments) для вызова второй функции, путем создания HTTP-запроса после его завершения.|
|**useMonitor**|**UseMonitor**|Установите значение `true` или`false`, чтобы указать, следует ли отслеживать расписание. При мониторинге расписания его экземпляры сохраняются, чтобы обеспечить его корректную обработку даже при перезапуске экземпляров приложения-функции. Если значение явно не задано, то по умолчанию используется `true` для расписаний с интервалом повторения более чем 1 минута. Для расписаний, выполняющихся чаще одного раза в минуту, значением по умолчанию является `false`.

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Использование

При вызове функции триггера с таймером [объект таймера](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/TimerInfo.cs) передается в функцию. Далее представлен JSON в качестве примера объекта таймера. 

```json
{
    "Schedule":{
    },
    "ScheduleStatus": {
        "Last":"2016-10-04T10:15:00+00:00",
        "LastUpdated":"2016-10-04T10:16:00+00:00",
        "Next":"2016-10-04T10:20:00+00:00"
    },
    "IsPastDue":false
}
```

Значение свойства `IsPastDue` — `true`, когда текущая функция вызывается позже запланированного. Например перезапуск приложения-функции может привести к тому, что вызов будет пропущен.

## <a name="cron-expressions"></a>Выражения CRON 

Выражение CRON для триггера таймера службы "Функции Azure" содержит шесть полей. 

`{second} {minute} {hour} {day} {month} {day-of-week}`

Каждое поле может принимать значение одного из следующих типов:

|type  |Пример  |Когда активируется  |
|---------|---------|---------|
|Определенное значение |<nobr>"0 5 * * * *"</nobr>|в hh:05:00, где hh — каждый час (один раз в час)|
|Все значения (`*`)|<nobr>"0 * 5 * * *"</nobr>|в 5:mm:00 каждый день, где mm — каждая минута часа (60 раз в день)|
|Диапазон (оператор `-`)|<nobr>"5-7 * * * * *"</nobr>|в hh:mm:05, hh:mm:06 и hh:mm:07, где hh:mm — каждая минута каждого часа (3 раза в минуту)|  
|Набор значений (оператор `,`)|<nobr>"5,8,10 * * * * *"</nobr>|в hh:mm:05, hh:mm:08 и hh:mm:10, где hh:mm — каждая минута каждого часа (3 раза в минуту)|
|Значение интервала (оператор `/`)|<nobr>"0 */5 * * * *"</nobr>|в hh:05:00, hh:10:00, hh:15:00 и так далее до hh:55:00, где hh — каждый час (12 раз в час)|

### <a name="cron-examples"></a>Примеры CRON

Ниже приведены примеры выражений CRON, которые можно использовать для триггера таймера в службе "Функции Azure".

|Пример|Когда активируется  |
|---------|---------|
|"0 */5 * * * *"|через каждые пять минут|
|"0 0 * * * *"|через каждый час|
|"0 0 */2 * * *"|через каждые 2 часа|
|"0 0 9-17 * * *"|один раз в час с 9:00 до 17:00|
|"0 30 9 * * *"|каждый день в 9:30|
|"0 30 9 * * 1-5"|каждый рабочий день в 9:30|

>[!NOTE]   
>Примеры выражений CRON можно найти в Интернете, но во многих из них пропущено поле `{second}`. При копировании такого выражения добавьте отсутствующее поле `{second}`. Обычно вам необходимо, чтобы значение этого поля было 0, а не звездочка.

### <a name="cron-time-zones"></a>Часовые пояса CRON

Числа в выражении CRON представляют собой время и дату, а не временной интервал. Например, значение 5 в поле `hour` означает 5:00, а не каждые 5 часов.

Часовой пояс по умолчанию, используемый с выражениями CRON — время в формате UTC. Если нужно использовать другой часовой пояс в выражении CRON, создайте параметр приложения с именем `WEBSITE_TIME_ZONE` для приложения-функции. В качестве значения задайте имя нужного часового пояса, как показано в статье [Microsoft Time Zone Index](https://technet.microsoft.com/library/cc749073) (Индексы часовых поясов Майкрософт). 

Например, *восточное поясное время* — UTC-05:00. Если требуется, чтобы триггер с таймером активировался в 10:00 по восточному поясному времени каждый день, можно использовать следующее выражение CRON, в котором учитывается часовой пояс UTC:

```json
"schedule": "0 0 15 * * *",
``` 

Или создайте параметр приложения с именем `WEBSITE_TIME_ZONE` для приложения-функции и задайте **Восточное время США** в качестве значения.  Затем используется следующее выражение CRON: 

```json
"schedule": "0 0 10 * * *",
``` 

## <a name="timespan"></a>Интервал времени

 `TimeSpan` можно использовать только для приложения-функции, которая работает в плане службы приложений.

В отличие от выражения CRON значение `TimeSpan` задает интервал времени между каждым вызовом функции. Если функция будет выполняться дольше заданного временного интервала, она немедленно будет активирована таймером.

Когда значения выражения `hh` в виде стоки меньше 24, `TimeSpan` будет использовать формат `hh:mm:ss`. Когда первые две цифры больше или равны 24, будет использован следующий формат `dd:hh:mm`. Ниже приведены некоторые примеры:

|Пример |Когда активируется  |
|---------|---------|
|"01:00:00" | каждый час        |
|"00:01:00"|каждую минуту         |
|"24:00:00" | каждые 24 дня        |

## <a name="scale-out"></a>Горизонтальное масштабирование

Если приложение-функция будет развернуто на несколько экземпляров, то только один из экземпляров активируемой по таймеру функции выполняется для всех экземпляров.

## <a name="function-apps-sharing-storage"></a>Совместное использование хранилища приложениями-функциями

Если вы предоставляете общий доступ к учетной записи хранения для нескольких приложений-функций, убедитесь, что у всех них разные `id` в файле *host.json*. Вы можете опустить свойство `id` или вручную задать для `id` всех приложений-функций разные значения. Для триггера таймера используется блокировка хранилища. Это гарантирует наличие только одного экземпляра таймера, когда приложение-функция масштабируется до нескольких экземпляров. Если у двух приложений-функций один `id` и для каждого используется триггер таймера, запустится только один таймер.

## <a name="retry-behavior"></a>Поведение при повторе

В отличие от триггера очереди триггер таймера не осуществляет повторную попытку после того, как произошла ошибка выполнения функции. Когда функция завершается ошибкой, она будет снова вызвана только по расписанию.

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Перейдите к краткому руководству по использованию триггера таймера](functions-create-scheduled-function.md)

> [!div class="nextstepaction"]
> [Основные понятия триггеров и привязок в Функциях Azure](functions-triggers-bindings.md)

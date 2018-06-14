---
title: Диагностика в устойчивых функциях — Azure
description: Сведения о том, как диагностировать неполадки в расширении устойчивых функций для Функций Azure.
services: functions
author: cgillum
manager: cfowler
editor: ''
tags: ''
keywords: ''
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 04/30/2018
ms.author: azfuncdf
ms.openlocfilehash: 4829ea88e0b6507159c192c111acf8ec7e5088e2
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/07/2018
ms.locfileid: "33764021"
---
# <a name="diagnostics-in-durable-functions-azure-functions"></a>Диагностика в устойчивых функциях (Функции Azure)

Существует несколько способов диагностики неполадок в [устойчивых функциях](durable-functions-overview.md). Некоторые из этих вариантов такие же, как и для обычных функций, а некоторые уникальны для устойчивых функций.

## <a name="application-insights"></a>Application Insights

[Application Insights](../application-insights/app-insights-overview.md) является рекомендуемым средством для выполнения диагностики и мониторинга в Функциях Azure. То же относится и к устойчивым функциям. Общие сведения об использовании Application Insights в приложении-функции см. в статье [Мониторинг Функций Azure](functions-monitoring.md).

Расширение устойчивых функций в службе "Функции Azure" также генерирует *события отслеживания* для выполнения трассировки на протяжении всего процесса оркестрации. Их можно найти и запросить с помощью средства [аналитики Application Insights](../application-insights/app-insights-analytics.md) на портале Azure.

### <a name="tracking-data"></a>Данные отслеживания

Каждое событие жизненного цикла экземпляра оркестрации инициирует запись события отслеживания в коллекцию **трассировки** в Application Insights. Это событие содержит полезные данные **customDimensions** с несколькими полями.  Все имена полей указаны с префиксом `prop__`.

* **hubName.** Имя центра задач, в котором выполняется оркестрация.
* **appName.** Имя приложения-функции. Это полезно, если у вас есть несколько приложений-функций, использующих один и тот же экземпляр Application Insights.
* **slotName.** [Слот развертывания](https://blogs.msdn.microsoft.com/appserviceteam/2017/06/13/deployment-slots-preview-for-azure-functions/), в котором выполняется текущее приложение-функция. Это полезно, если вы используете слоты развертывания для управления версиями оркестраций.
* **functionName.** Имя оркестратора или функции действия.
* **functionType.** Тип функции, например функция **оркестратора** или **действия**.
* **InstanceId.** Уникальный идентификатор экземпляра оркестрации.
* **state.** Состояние выполнения жизненного цикла экземпляра. Допустимые значения:
    * **Scheduled.** Выполнение функции было запланировано, но еще не началось.
    * **Started.** Функция запущена, но еще не находится в состоянии ожидания и не была завершена.
    * **Awaited.** Оркестратор запланировал некоторые действия и ожидает их завершения.
    * **Listening.** Оркестратор прослушивает уведомления о внешних событиях.
    * **Completed.** Функция успешно выполнена.
    * **Failed.** Выполнение функции завершилось ошибкой.
* **reason.** Дополнительные данные, связанные с событием отслеживания. Например, если экземпляр ожидает уведомления о внешних событиях, это поле указывает имя события, которое он ожидает. Если выполнение функции завершилось сбоем, оно будет содержать сведения об ошибке.
* **isReplay.** Логическое значение, указывающее, следует ли повторно выполнять событие отслеживания.
* **extensionVersion.** Версия расширения устойчивых задач. Эти данные очень важны в рамках отчетности о возможных ошибках в расширении. Долго выполняющиеся экземпляры могут сообщать о нескольких версиях, если возникает обновление при их выполнении. 
* **sequenceNumber**: порядковый номер выполнения события. В сочетании с меткой времени позволяет упорядочить события по времени выполнения. *Обратите внимание на то, что это число будет сброшено до нуля при перезапуске узла, когда экземпляр работает. Поэтому важно всегда сначала сортировать по метке времени, а затем по значению sequenceNumber.*

Уровень детализации данных отслеживания, переданных в Application Insights, можно настроить в разделе `logger` файла `host.json`.

```json
{
    "logger": {
        "categoryFilter": {
            "categoryLevels": {
                "Host.Triggers.DurableTask": "Information"
            }
        }
    }
}
```

По умолчанию передаются все события отслеживания. Объем данных можно уменьшить, задав для `Host.Triggers.DurableTask` значение `"Warning"` или `"Error"`. В этом случае события отслеживания будут передаваться в исключительных ситуациях.

> [!NOTE]
> По умолчанию телеметрия Application Insights осуществляется средой выполнения Функций Azure, чтобы избежать частой передачи данных. Это может привести к потере сведений об отслеживании, если много событий жизненного цикла происходят за короткий период времени. Сведения о настройке этого поведения см. в разделе [Настройка выборки](functions-monitoring.md#configure-sampling).

### <a name="single-instance-query"></a>Одноэкземплярный запрос

Указанный ниже запрос содержит данные отслеживания журнала для одного экземпляра оркестрации функции [последовательности Hello](durable-functions-sequence.md). Он написан с помощью [языка запросов Application Insights (AIQL)](https://docs.loganalytics.io/docs/Language-Reference). Этот экземпляр фильтрует выполнение воспроизведения, поэтому показан только *логический* путь выполнения. События могут быть упорядочены путем сортировки по `timestamp` и `sequenceNumber`, как показано в следующем запросе. 

```AIQL
let targetInstanceId = "ddd1aaa685034059b545eb004b15d4eb";
let start = datetime(2018-03-25T09:20:00);
traces
| where timestamp > start and timestamp < start + 30m
| where customDimensions.Category == "Host.Triggers.DurableTask"
| extend functionName = customDimensions["prop__functionName"]
| extend instanceId = customDimensions["prop__instanceId"]
| extend state = customDimensions["prop__state"]
| extend isReplay = tobool(tolower(customDimensions["prop__isReplay"]))
| extend sequenceNumber = tolong(customDimensions["prop__sequenceNumber"]) 
| where isReplay == false
| where instanceId == targetInstanceId
| sort by timestamp asc, sequenceNumber asc
| project timestamp, functionName, state, instanceId, sequenceNumber, appName = cloud_RoleName
```

Результатом является список событий отслеживания, который отображает путь выполнения оркестрации, включая любые функции действий, упорядоченные по времени выполнения в возрастающем порядке.

![Запрос Application Insights](media/durable-functions-diagnostics/app-insights-single-instance-ordered-query.png)


### <a name="instance-summary-query"></a>Сводный запрос экземпляров

Следующий запрос отображает состояние всех экземпляров оркестрации, выполненных в указанном диапазоне времени.

```AIQL
let start = datetime(2017-09-30T04:30:00);
traces
| where timestamp > start and timestamp < start + 1h
| where customDimensions.Category == "Host.Triggers.DurableTask"
| extend functionName = tostring(customDimensions["prop__functionName"])
| extend instanceId = tostring(customDimensions["prop__instanceId"])
| extend state = tostring(customDimensions["prop__state"])
| extend isReplay = tobool(tolower(customDimensions["prop__isReplay"]))
| extend output = tostring(customDimensions["prop__output"])
| where isReplay == false
| summarize arg_max(timestamp, *) by instanceId
| project timestamp, instanceId, functionName, state, output, appName = cloud_RoleName
| order by timestamp asc
```
Результатом является список идентификаторов экземпляров и их текущее состояние выполнения.

![Запрос Application Insights](media/durable-functions-diagnostics/app-insights-single-summary-query.png)

## <a name="logging"></a>Ведение журналов

Очень важно помнить о поведении воспроизведения оркестратора при записи журналов непосредственно из функции оркестратора. Например, рассмотрим следующую функцию оркестратора:

#### <a name="c"></a>C#

```cs
public static async Task Run(
    DurableOrchestrationContext ctx,
    TraceWriter log)
{
    log.Info("Calling F1.");
    await ctx.CallActivityAsync("F1");
    log.Info("Calling F2.");
    await ctx.CallActivityAsync("F2");
    log.Info("Calling F3");
    await ctx.CallActivityAsync("F3");
    log.Info("Done!");
}
```

#### <a name="javascript-functions-v2-only"></a>JavaScript (только для службы "Функции" версии 2)

```javascript
const df = require("durable-functions");

module.exports = df(function*(context){
    context.log("Calling F1.");
    yield context.df.callActivityAsync("F1");
    context.log("Calling F2.");
    yield context.df.callActivityAsync("F2");
    context.log("Calling F3.");
    yield context.df.callActivityAsync("F3");
    context.log("Done!");
});
```

Итоговые данные журнала должны выглядеть следующим образом:

```txt
Calling F1.
Calling F1.
Calling F2.
Calling F1.
Calling F2.
Calling F3.
Calling F1.
Calling F2.
Calling F3.
Done!
```

> [!NOTE]
> Помните, что хотя в журналах указаны вызовы F1, F2 и F3, эти функции *фактически* вызываются, только когда они встречаются впервые. Последующие вызовы, которые происходят во время воспроизведения, пропускаются, и выходные данные воспроизводятся в логике оркестратора.

Если вы хотите протоколировать только выполнение без воспроизведения, можно написать логическое выражение, чтобы вносить запись, только когда `IsReplaying` имеет значение `false`. Рассмотрим указанный выше пример, но на этот раз с проверкой воспроизведения.

```cs
public static async Task Run(
    DurableOrchestrationContext ctx,
    TraceWriter log)
{
    if (!ctx.IsReplaying) log.Info("Calling F1.");
    await ctx.CallActivityAsync("F1");
    if (!ctx.IsReplaying) log.Info("Calling F2.");
    await ctx.CallActivityAsync("F2");
    if (!ctx.IsReplaying) log.Info("Calling F3");
    await ctx.CallActivityAsync("F3");
    log.Info("Done!");
}
```
После этого изменения выходные данные журнала выглядят следующим образом:

```txt
Calling F1.
Calling F2.
Calling F3.
Done!
```

> [!NOTE]
> Свойство `IsReplaying` еще не доступно в JavaScript.

## <a name="custom-status"></a>Настраиваемое значение состояния

Эта возможность позволяет задать настраиваемое значение состояния для функции оркестратора. Такое значение состояния можно задать с помощью API HTTP-запросов состояния или API `DurableOrchestrationClient.GetStatusAsync`. Настраиваемое состояние оркестрации предоставляет дополнительные возможности мониторинга для функций оркестратора. Например, в код функции оркестратора можно включить вызовы `DurableOrchestrationContext.SetCustomStatus`, чтобы обновлять ход выполнения длительной операции. Клиент, например веб-страница или другая внешняя система, может периодически отправлять запрос в API HTTP-запросов состояния, чтобы получить расширенные сведения о ходе выполнения. Пример использования `DurableOrchestrationContext.SetCustomStatus` приведен ниже.

```csharp
public static async Task SetStatusTest([OrchestrationTrigger] DurableOrchestrationContext ctx)
{
    // ...do work...

    // update the status of the orchestration with some arbitrary data
    var customStatus = new { completionPercentage = 90.0, status = "Updating database records" };
    ctx.SetCustomStatus(customStatus);

    // ...do more work...
}
```

Когда выполняется оркестрация, внешние клиенты могут получить данные о таком настраиваемом значении состояния:

```http
GET /admin/extensions/DurableTaskExtension/instances/instance123

```

Они получат следующий ответ: 

```http
{
  "runtimeStatus": "Running",
  "input": null,
  "customStatus": { "completionPercentage": 90.0, "status": "Updating database records" },
  "output": null,
  "createdTime": "2017-10-06T18:30:24Z",
  "lastUpdatedTime": "2017-10-06T19:40:30Z"
}
```

> [!WARNING]
>  Полезные данные настраиваемого значения состояния не должны превышать 16 КБ JSON-текста в кодировке UTF-16, так как они должны поместиться в столбец хранилища таблиц Azure. Для полезных данных большего размера можно использовать внешнее хранилище.

## <a name="debugging"></a>Отладка

Функции Azure поддерживают сценарии отладки кода функции напрямую. Та же поддержка реализуется в устойчивых функциях, работающих в Azure или локально. Однако следует помнить о некоторых особенностях поведения при выполнении отладки.

* **Replay.** Функции оркестратора обычно воспроизводятся при получении новых входных данных. Это означает, что одно *логическое* выполнение функции оркестратора может привести к достижению той же точки останова несколько раз, особенно в случаях, если это установлено ранее в коде функции.
* **Await.** Каждый раз, когда возникает действие `await`, оно возвращает элемент управления в диспетчер платформы устойчивых задач. Если действие `await` возникает впервые, связанные задачи *никогда не* возобновляются. Так как задача никогда не возобновляется, *обход* действия await (F10 в Visual Studio) не является возможным. Вы можете обойти это действие, только когда задание воспроизводится.
* **Время ожидания обмена сообщениями.** Устойчивые функции внутри используют сообщения очереди для управления выполнением функций оркестратора и функций действий. В среде со множеством виртуальных машин прерывание отладки на длительное время может привести к тому, что другая виртуальная машина получит сообщение, из-за чего произойдет дублирующее выполнение. Это поведение также существует для обычных функций триггера очереди, но его очень важно отметить в этом контексте, так как очереди относятся к тонкостям реализации.

> [!TIP]
> При установке точек останова, если вы хотите прервать только выполнение без воспроизведения, можно задать условную точку останова, которая прерывается, только когда `IsReplaying` имеет значение `false`.

## <a name="storage"></a>Служба хранилища

По умолчанию устойчивые функции хранят состояние в службе хранилища Azure. Это означает, что вы можете проверить состояние вашей оркестрации с помощью средств, таких как [обозреватель службы хранилища Microsoft Azure](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer).

![Снимок экрана обозревателя службы хранилища](media/durable-functions-diagnostics/storage-explorer.png)

Это полезно для отладки, так как вы можете увидеть точное состояние оркестрации. Сообщения в очередях можно также проверить, чтобы узнать, какое действие находится в состоянии ожидания (или в некоторых случаях могло зависнуть).

> [!WARNING]
> Хотя просматривать журнал выполнения в службе таблиц более удобно, избегайте использования зависимостей в таблице. Они могут измениться при развитии расширения устойчивых функций.

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Таймеры в устойчивых функциях (Функции Azure)](durable-functions-timers.md)
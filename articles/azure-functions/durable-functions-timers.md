---
title: Таймеры в устойчивых функциях — Azure
description: Сведения о том, как реализовать устойчивые таймеры в расширении устойчивых функций для Функций Azure.
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
ms.openlocfilehash: 4fd86b70965a7be84c72e265af798292819cbe96
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/07/2018
ms.locfileid: "33762274"
---
# <a name="timers-in-durable-functions-azure-functions"></a>Таймеры в устойчивых функциях (Функции Azure)

[Устойчивые функции](durable-functions-overview.md) предоставляют *устойчивые таймеры*, которые используются в функциях оркестраторов для реализации задержек или настройки времени ожидания в асинхронных действиях. Устойчивые таймеры следует использовать в функциях оркестраторов вместо `Thread.Sleep` (C#) или `Task.Delay` (JavaScript).

Чтобы создать устойчивый таймер, в [DurableOrchestrationContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html) нужно вызвать метод [CreateTimer](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CreateTimer_). Метод возвращает задачу, которая возобновляется в указанные дату и время.

## <a name="timer-limitations"></a>Ограничения таймера

При создании таймера, срок действия которого истекает в 16:30, базовая платформа устойчивых задач помещает в очередь сообщение, которое становится видимым только в 16:30. При запуске в плане потребления Функций Azure ставшее видимым сообщения таймера обеспечит активацию приложения-функции на соответствующей виртуальной машине.

> [!NOTE]
> * Из-за ограничений службы хранилища Azure устойчивые таймеры можно устанавливать не более чем на 7 дней. Мы работаем над [заявкой на исправление функции, касающейся продления работы таймеров более чем на 7 дней](https://github.com/Azure/azure-functions-durable-extension/issues/14).
> * При вычислении относительного срока действия устойчивого таймера всегда используйте [CurrentUtcDateTime](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CurrentUtcDateTime) вместо `DateTime.UtcNow`, как показано в примерах ниже.

## <a name="usage-for-delay"></a>Использование для задержки

В следующем примере показано, как использовать устойчивые таймеры для задержки выполнения. Пример выдает уведомление о выставлении счета каждый день в течение 10 дней.

#### <a name="c"></a>C#

```csharp
[FunctionName("BillingIssuer")]
public static async Task Run(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    for (int i = 0; i < 10; i++)
    {
        DateTime deadline = context.CurrentUtcDateTime.Add(TimeSpan.FromDays(1));
        await context.CreateTimer(deadline, CancellationToken.None);
        await context.CallActivityAsync("SendBillingEvent");
    }
}
```

#### <a name="javascript"></a>JavaScript

```js
const df = require("durable-functions");
const moment = require("moment-js");

module.exports = df(function*(context) {
    for (let i = 0; i < 10; i++) {
        const dayOfMonth = context.df.currentUtcDateTime.getDate();
        const deadline = moment.utc(context.df.currentUtcDateTime).add(1, 'd');
        yield context.df.createTimer(deadline.toDate());
        yield context.df.callActivityAsync("SendBillingEvent");
    }
});
```

> [!WARNING]
> Избегайте бесконечных циклов в функциях оркестраторов. Сведения о том, как безопасно и эффективно реализовать сценарии с бесконечным циклом, см. в описании [внешних оркестраций](durable-functions-eternal-orchestrations.md). 

## <a name="usage-for-timeout"></a>Использование для установки времени ожидания

В этом примере показано, как использовать устойчивые таймеры для реализации времени ожидания.

#### <a name="c"></a>C#

```csharp
[FunctionName("TryGetQuote")]
public static async Task<bool> Run(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    TimeSpan timeout = TimeSpan.FromSeconds(30);
    DateTime deadline = context.CurrentUtcDateTime.Add(timeout);

    using (var cts = new CancellationTokenSource())
    {
        Task activityTask = context.CallActivityAsync("GetQuote");
        Task timeoutTask = context.CreateTimer(deadline, cts.Token);

        Task winner = await Task.WhenAny(activityTask, timeoutTask);
        if (winner == activityTask)
        {
            // success case
            cts.Cancel();
            return true;
        }
        else
        {
            // timeout case
            return false;
        }
    }
}
```

#### <a name="javascript"></a>JavaScript

```js
const df = require("durable-functions");
const moment = require("moment-js");

module.exports = df(function*(context) {
    const deadline = moment.utc(context.df.currentUtcDateTime).add(30, 's');

    const activityTask = context.df.callActivityAsync("GetQuote");
    const timeoutTask = context.df.createTimer(deadline);

    const winner = yield context.df.Task.any([activityTask, timeoutTask]);
    if (winner === activityTask) {
        // success case
        timeoutTask.cancel();
        return true;
    }
    else
    {
        // timeout case
        return false;
    }
});
```

> [!WARNING]
> Используйте `CancellationTokenSource` для отмены устойчивого таймера (C#) или вызовите `cancel()` при возврате `TimerTask` (JavaScript), если ваш код не будет ожидать его завершения. Платформа устойчивых задач не изменит состояние оркестрации на "Завершено", пока не будут завершены или отменены все незавершенные задачи.

Этот механизм фактически не прекращает текущее выполнение функции действия. Вместо этого он просто позволяет функции оркестратора игнорировать результат и двигаться дальше. Если в вашем приложении-функции используется план потребления, вам все равно будет выставлен счет за время и память, потребленные прерванной функцией действия. По умолчанию для функций, выполняемых в плане потребления, устанавливается время ожидания в пять минут. При превышении этого ограничения узел Функций Azure перезапускается для остановки выполнения всех процессов и предотвращения неконтролируемого выставления счетов. [Время ожидания функции можно настроить](functions-host-json.md#functiontimeout).

Более подробный пример реализации времени ожидания в функциях оркестраторов см. в пошаговом руководстве [Human interaction in Durable Functions — Phone verification sample](durable-functions-phone-verification.md) (Участие пользователя в устойчивых функциях: пример проверки по телефону).

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Сведения о том, как вызывать и обрабатывать внешние события](durable-functions-external-events.md)


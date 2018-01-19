---
title: "Таймеры в устойчивых функциях — Azure"
description: "Сведения о том, как реализовать устойчивые таймеры в расширении устойчивых функций для Функций Azure."
services: functions
author: cgillum
manager: cfowler
editor: 
tags: 
keywords: 
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 09/29/2017
ms.author: azfuncdf
ms.openlocfilehash: e29e472860890e3f44af79c42c31ff524acb9276
ms.sourcegitcommit: 9a8b9a24d67ba7b779fa34e67d7f2b45c941785e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/08/2018
---
# <a name="timers-in-durable-functions-azure-functions"></a>Таймеры в устойчивых функциях (Функции Azure)

[Устойчивые функции](durable-functions-overview.md) предоставляют *устойчивые таймеры*, которые используются в функциях оркестраторов для реализации задержек или настройки времени ожидания в асинхронных действиях. Устойчивые таймеры следует использовать в функциях оркестраторов вместо `Thread.Sleep` или `Task.Delay`.

Чтобы создать устойчивый таймер, в [DurableOrchestrationContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html) нужно вызвать метод [CreateTimer](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CreateTimer_). Метод возвращает задачу, которая возобновляется в указанные дату и время.

## <a name="timer-limitations"></a>Ограничения таймера

При создании таймера, срок действия которого истекает в 16:30, базовая платформа устойчивых задач помещает в очередь сообщение, которое становится видимым только в 16:30. При запуске в плане потребления Функций Azure ставшее видимым сообщения таймера обеспечит активацию приложения-функции на соответствующей виртуальной машине.

> [!WARNING]
> * Из-за ограничений службы хранилища Azure устойчивые таймеры можно устанавливать не более чем на 7 дней. Мы работаем над [заявкой на исправление функции, касающейся продления работы таймеров более чем на 7 дней](https://github.com/Azure/azure-functions-durable-extension/issues/14).
> * При вычислении относительного срока действия устойчивого таймера всегда используйте [CurrentUtcDateTime](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CurrentUtcDateTime) вместо `DateTime.UtcNow`, как показано в примерах ниже.

## <a name="usage-for-delay"></a>Использование для задержки

В следующем примере показано, как использовать устойчивые таймеры для задержки выполнения. Пример выдает уведомление о выставлении счета каждый день в течение 10 дней.

```csharp
[FunctionName("BillingIssuer")]
public static async Task Run(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    for (int i = 0; i < 10; i++)
    {
        DateTime deadline = context.CurrentUtcDateTime.Add(TimeSpan.FromDays(1));
        await context.CreateTimer(deadline, CancellationToken.None);
        await context.CallFunctionAsync("SendBillingEvent");
    }
}
```

> [!WARNING]
> Избегайте бесконечных циклов в функциях оркестраторов. Сведения о том, как безопасно и эффективно реализовать сценарии с бесконечным циклом, см. в описании [внешних оркестраций](durable-functions-eternal-orchestrations.md). 

## <a name="usage-for-timeout"></a>Использование для установки времени ожидания

В этом примере показано, как использовать устойчивые таймеры для реализации времени ожидания.

```csharp
[FunctionName("TryGetQuote")]
public static async Task<bool> Run(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    TimeSpan timeout = TimeSpan.FromSeconds(30);
    DateTime deadline = context.CurrentUtcDateTime.Add(timeout);

    using (var cts = new CancellationTokenSource())
    {
        Task activityTask = context.CallFunctionAsync("GetQuote");
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

> [!WARNING]
> Используйте `CancellationTokenSource` для отмены устойчивого таймера, если ваш код не будет ожидать его завершения. Платформа устойчивых задач не изменит состояние оркестрации на "Завершено", пока не будут завершены или отменены все незавершенные задачи.

Этот механизм фактически не прекращает текущее выполнение функции действия. Вместо этого он просто позволяет функции оркестратора игнорировать результат и двигаться дальше. Если в вашем приложении-функции используется план потребления, вам все равно будет выставлен счет за время и память, потребленные прерванной функцией действия. По умолчанию для функций, выполняемых в плане потребления, устанавливается время ожидания в пять минут. При превышении этого ограничения узел Функций Azure перезапускается для остановки выполнения всех процессов и предотвращения неконтролируемого выставления счетов. [Время ожидания функции можно настроить](functions-host-json.md#functiontimeout).

Более подробный пример реализации времени ожидания в функциях оркестраторов см. в пошаговом руководстве [Human interaction in Durable Functions — Phone verification sample](durable-functions-phone-verification.md) (Участие пользователя в устойчивых функциях: пример проверки по телефону).

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Сведения о том, как вызывать и обрабатывать внешние события](durable-functions-external-events.md)


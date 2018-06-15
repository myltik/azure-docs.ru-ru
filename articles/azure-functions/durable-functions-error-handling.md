---
title: Обработка ошибок в устойчивых функциях — Azure
description: Сведения о том, как обрабатывать ошибки в расширении устойчивых функций для Функций Azure.
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
ms.openlocfilehash: 944fab5ccc55bc9a697e870208338bd0e697672d
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/07/2018
ms.locfileid: "33763311"
---
# <a name="handling-errors-in-durable-functions-azure-functions"></a>Обработка ошибок в устойчивых функциях (Функции Azure)

Оркестрации устойчивых функций реализованы в коде и могут использовать возможности языка программирования по обработке ошибок. С учетом этого при внедрении в оркестрации обработки и компенсации ошибок не требуется изучать новые концепции. Однако следует помнить о некоторых особенностях поведения.

## <a name="errors-in-activity-functions"></a>Ошибки в функциях действий

Любое исключение, возникающее в функции действия, маршалируется обратно в функцию оркестратора и выдается как `FunctionFailedException`. Вы можете написать код для обработки и компенсации ошибок, соответствующий вашим потребностям в функции оркестратора.

Например, рассмотрим следующую функцию оркестратора, которая перемещает средства с одного счета на другой:

```csharp
#r "Microsoft.Azure.WebJobs.Extensions.DurableTask"

public static async Task Run(DurableOrchestrationContext context)
{
    var transferDetails = ctx.GetInput<TransferOperation>();

    await context.CallActivityAsync("DebitAccount",
        new
        { 
            Account = transferDetails.SourceAccount,
            Amount = transferDetails.Amount
        });

    try
    {
        await context.CallActivityAsync("CreditAccount",         
            new
            { 
                Account = transferDetails.DestinationAccount,
                Amount = transferDetails.Amount
            });
    }
    catch (Exception)
    {
        // Refund the source account.
        // Another try/catch could be used here based on the needs of the application.
        await context.CallActivityAsync("CreditAccount",         
            new
            { 
                Account = transferDetails.SourceAccount,
                Amount = transferDetails.Amount
            });
    }
}
```

Если вызов функции **CreditAccount** завершается с ошибкой для целевого счета, функция оркестратора компенсирует это, кредитуя денежные средства обратно на исходный счет.

## <a name="automatic-retry-on-failure"></a>Автоматическое повторение попыток при сбое

При вызове функций действий или функций суборкестрации можно указать автоматическую политику повтора. В следующем примере предпринимается попытка вызова функции до 3 раз с ожиданием в течение 5 секунд между попытками:

```csharp
public static async Task Run(DurableOrchestrationContext context)
{
    var retryOptions = new RetryOptions(
        firstRetryInterval: TimeSpan.FromSeconds(5),
        maxNumberOfAttempts: 3);

    await ctx.CallActivityWithRetryAsync("FlakyFunction", retryOptions, null);
    
    // ...
}
```

API `CallActivityWithRetryAsync` принимает параметр `RetryOptions`. В вызовах суборкестрации с использованием API `CallSubOrchestratorWithRetryAsync` можно использовать эти же политики повтора.

Существует несколько параметров настройки политики автоматического повтора. К ним относятся следующие:

* **Max number of attempts** (Максимальное число попыток): максимальное число повторных попыток.
* **First retry interval** (Интервал до первого повтора): время ожидания перед первой повторной попыткой.
* **Backoff coefficient** (Коэффициент отсрочки): коэффициент, позволяющий определить степень увеличения отсрочки. По умолчанию равен 1.
* **Max retry interval** (Максимальный интервал повтора): максимальное время ожидания между повторными попытками.
* **Retry timeout** (Время ожидания повтора): максимальное время, отведенное на выполнение повторных попыток. Поведение по умолчанию — бесконечное повторение.
* **Custom** (Настраиваемый): можно указать определенный пользователем обратный вызов, который определяет, следует ли повторять попытку вызова функции.

## <a name="function-timeouts"></a>Время ожидания функций

Вы можете прекратить вызов функции внутри функции оркестратора, если его завершение занимает слишком много времени. Правильный способ сделать это в настоящее время — создать [устойчивый таймер](durable-functions-timers.md), используя `context.CreateTimer` в сочетании с `Task.WhenAny`, как показано в следующем примере:

```csharp
public static async Task<bool> Run(DurableOrchestrationContext context)
{
    TimeSpan timeout = TimeSpan.FromSeconds(30);
    DateTime deadline = context.CurrentUtcDateTime.Add(timeout);

    using (var cts = new CancellationTokenSource())
    {
        Task activityTask = context.CallActivityAsync("FlakyFunction");
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

> [!NOTE]
> Этот механизм фактически не прекращает текущее выполнение функции действия. Вместо этого он просто позволяет функции оркестратора игнорировать результат и двигаться дальше. Дополнительные сведения см. в документации по [таймерам](durable-functions-timers.md#usage-for-timeout).

## <a name="unhandled-exceptions"></a>Необработанные исключения

Если функция оркестратора завершается сбоем с необработанным исключением, сведения об этом исключении регистрируются в журнале, и экземпляр завершает работу с состоянием `Failed`.

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Дополнительные сведения для диагностики неполадок](durable-functions-diagnostics.md)

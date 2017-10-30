---
title: "Обработка внешних событий в устойчивых функциях в Azure"
description: "Сведения о том, как обрабатывать внешние события в расширении устойчивых функций для Функций Azure."
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
ms.openlocfilehash: 23c99031ae3146a83867d10bd97d4eee8878188a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="handling-external-events-in-durable-functions-azure-functions"></a>Обработка внешних событий в устойчивых функциях (Функции Azure)

Функции оркестратора могут ожидать передачи внешних событий. Эта возможность [устойчивых функций](durable-functions-overview.md) часто используется для обработки действий человека или других внешних триггеров.

## <a name="wait-for-events"></a>Ожидание событий

Метод [WaitForExternalEvent](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_WaitForExternalEvent_) позволяет функции оркестратора асинхронно ожидать передачи внешнего события. Вызывающий объект объявляет *имя* события и *форму данных*, которую ожидает получить.

```csharp
[FunctionName("BudgetApproval")]
public static async Task Run(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    bool approved = await context.WaitForExternalEvent<bool>("Approval");
    if (approved)
    {
        // approval granted - do the approved action
    }
    else
    {
        // approval denied - send a notification
    }
}
```

В предыдущем примере выполняется ожидание передачи одиночного события и предпринимается действие при его получении.

Можно одновременно прослушивать несколько событий, как показано в следующем примере, в котором ожидается одно из трех возможных уведомлений о событиях.

```csharp
[FunctionName("Select")]
public static async Task Run(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    var event1 = context.WaitForExternalEvent<float>("Event1");
    var event2 = context.WaitForExternalEvent<bool>("Event2");
    var event3 = context.WaitForExternalEvent<int>("Event3");

    var winner = await Task.WhenAny(event1, event2, event3);
    if (winner == event1)
    {
        // ...
    }
    else if (winner == event2)
    {
        // ...
    }
    else if (winner == event3)
    {
        // ...
    }
}
```

В предыдущем примере ожидается *любое* из нескольких событий. Также возможно реализовать ожидание *всех* событий.

```csharp
[FunctionName("NewBuildingPermit")]
public static async Task Run(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    string applicationId = context.GetInput<string>();

    var gate1 = context.WaitForExternalEvent("CityPlanningApproval");
    var gate2 = context.WaitForExternalEvent("FireDeptApproval");
    var gate3 = context.WaitForExternalEvent("BuildingDeptApproval");

    // all three departments must grant approval before a permit can be issued
    await Task.WhenAll(gate1, gate2, gate3);

    await context.CallActivityAsync("IssueBuildingPermit", applicationId);
}
```

Метод [WaitForExternalEvent](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_WaitForExternalEvent_) бесконечно ожидает входные данные.  Приложение-функцию можно безопасно выгрузить во время ожидания. Когда событие поступит в этот экземпляр оркестрации, он автоматически активируется и немедленно обработает событие.

> [!NOTE]
> Во время ожидания функцией оркестратора задачи от `WaitForExternalEvent` плата не взимается, независимо от того, сколько длится ожидание.

Если полезную нагрузку события нельзя преобразовать в ожидаемый тип `T`, выдается исключение.

## <a name="send-events"></a>Отправка событий

Метод [RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_) класса [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) отправляет события, которые ожидает `WaitForExternalEvent`.  Метод `RaiseEventAsync` принимает *eventName* и *eventData* в качестве параметров. Данные событий должны быть JSON-сериализуемыми.

Ниже приведен пример функции, активируемой с помощью очереди, которая отправляет событие "Approval" в экземпляр функции оркестратора. Идентификатор экземпляра оркестрации поступает из текста сообщения очереди.

```csharp
[FunctionName("ApprovalQueueProcessor")]
public static async Task Run(
    [QueueTrigger("approval-queue")] string instanceId,
    [OrchestrationClient] DurableOrchestrationClient client)
{
    await client.RaiseEventAsync(instanceId, "Approval", true);
}
```

Внутри системы `RaiseEventAsync` помещает в очередь сообщение, которое получает ожидающая функция оркестратора.

> [!WARNING]
> Если экземпляра оркестрации с указанным *идентификатором экземпляра* нет или если экземпляр не ожидает указанное *имя события*, сообщение о событии отклоняется. Дополнительные сведения об этом поведении см. в [описании проблемы на GitHub](https://github.com/Azure/azure-functions-durable-extension/issues/29).

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Сведения о настройке внешних оркестраций](durable-functions-eternal-orchestrations.md)

> [!div class="nextstepaction"]
> [Запуск примера с ожиданием внешних событий](durable-functions-counter.md)

> [!div class="nextstepaction"]
> [Запуск примера с ожиданием действий человека](durable-functions-phone-verification.md)


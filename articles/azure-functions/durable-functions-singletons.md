---
title: "Одноэлементные экземпляры в устойчивых функциях — Azure"
description: "Инструкции по использованию одноэлементных экземпляров в расширении устойчивых функций для Функций Azure."
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
ms.openlocfilehash: e82cc53d53a6d0296aaab2c3a76ad4e2f6c12c54
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="singleton-orchestrators-in-durable-functions-azure-functions"></a>Одноэлементные экземпляры в устойчивых функциях (Функции Azure)

Для фоновых заданий или оркестраций в субъектном стиле часто необходимо обеспечить, чтобы одновременно выполнялся только один экземпляр определенного оркестратора. Это можно реализовать в [устойчивых функциях](durable-functions-overview.md), назначив создаваемому оркестратору определенный идентификатор экземпляра.

## <a name="singleton-example"></a>Пример одноэлементного экземпляра

В следующем примере C# показана функция HTTP-триггера, которая создает одноэлементный экземпляр оркестрации фонового задания. При этом используется известный идентификатор экземпляра, чтобы обеспечить наличие только одного экземпляра.

```cs
[FunctionName("EnsureSingletonTrigger")]
public static async Task<HttpResponseMessage> Ensure(
    [HttpTrigger(AuthorizationLevel.Function, methods: "post")] HttpRequestMessage req,
    [OrchestrationClient] DurableOrchestrationClient starter,
    TraceWriter log)
{
    // Ensure only one instance is ever running at a time
    const string OrchestratorName = "MySingletonOrchestrator";
    const string InstanceId = "MySingletonInstanceId";

    var existingInstance = await starter.GetStatusAsync(InstanceId);
    if (existingInstance == null)
    {
        log.Info($"Creating singleton instance with ID = {InstanceId}...");
        await starter.StartNewAsync(OrchestratorName, InstanceId, input: null);
    }

    return starter.CreateCheckStatusResponse(req, InstanceId);
}
```

По умолчанию идентификаторы экземпляров — это случайным образом сгенерированные GUID. Но обратите внимание, что в нашем примере функция-триггер использует заранее определенную переменную `InstanceId` со значением `MySingletonInstanceId`, чтобы предварительно назначить идентификатор экземпляра функции оркестратора. Так триггер может проверить, запущен ли уже известный экземпляр, вызвав [GetStatusAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_GetStatusAsync_).

На самом деле подробности реализации функции оркестратора не имеют значения. Это может быть обычная функция оркестратора, которая начинает и завершает работу, или же выполняющаяся бесконечно (т. е. [вечная оркестрация](durable-functions-eternal-orchestrations.md)). Важно то, что в любой момент времени выполняется только один экземпляр.

> [!NOTE]
> Если одноэлементный экземпляр оркестрации будет остановлен, подвергнется сбою или завершит работу, его невозможно будет создать повторно с тем же идентификатором. В таких случаях следует подготовиться к повторному его созданию с новым идентификатором экземпляра.

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Сведения о вызове вложенных оркестраций](durable-functions-sub-orchestrations.md)

> [!div class="nextstepaction"]
> [Запуск примера одноэлементного экземпляра](durable-functions-counter.md)

---
title: Одноэлементные экземпляры в устойчивых функциях — Azure
description: Инструкции по использованию одноэлементных экземпляров в расширении устойчивых функций для Функций Azure.
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
ms.date: 09/29/2017
ms.author: azfuncdf
ms.openlocfilehash: ea8b5db946d6b35ea4583d9170ec36e5f95e16cd
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/17/2018
ms.locfileid: "29972557"
---
# <a name="singleton-orchestrators-in-durable-functions-azure-functions"></a>Одноэлементные экземпляры в устойчивых функциях (Функции Azure)

Для фоновых заданий или оркестраций в субъектном стиле часто необходимо обеспечить, чтобы одновременно выполнялся только один экземпляр определенного оркестратора. Это можно реализовать в [устойчивых функциях](durable-functions-overview.md), назначив создаваемому оркестратору определенный идентификатор экземпляра.

## <a name="singleton-example"></a>Пример одноэлементного экземпляра

В следующем примере C# показана функция HTTP-триггера, которая создает одноэлементный экземпляр оркестрации фонового задания. Код обеспечивает наличие только одного экземпляра с указанным идентификатором.

```cs
[FunctionName("HttpStartSingle")]
public static async Task<HttpResponseMessage> RunSingle(
    [HttpTrigger(AuthorizationLevel.Function, methods: "post", Route = "orchestrators/{functionName}/{instanceId}")] HttpRequestMessage req,
    [OrchestrationClient] DurableOrchestrationClient starter,
    string functionName,
    string instanceId,
    TraceWriter log)
{
    // Check if an instance with the specified ID already exists.
    var existingInstance = await starter.GetStatusAsync(instanceId);
    if (existingInstance == null)
    {
        // An instance with the specified ID doesn't exist, create one.
        dynamic eventData = await req.Content.ReadAsAsync<object>();
        await starter.StartNewAsync(functionName, instanceId, eventData);
        log.Info($"Started orchestration with ID = '{instanceId}'.");
        return starter.CreateCheckStatusResponse(req, instanceId);
    }
    else
    {
        // An instance with the specified ID exists, don't create one.
        return req.CreateErrorResponse(
            HttpStatusCode.Conflict,
            $"An instance with ID '{instanceId}' already exists.");
    }
}
```

По умолчанию идентификаторы экземпляров — это случайным образом сгенерированные GUID. Но в этом случае идентификатор экземпляра передается в данных маршрута с URL-адреса. Этот код вызывает [GetStatusAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_GetStatusAsync_), чтобы проверить, запущен ли экземпляр с указанным идентификатором. Если нет, такой экземпляр создается.

На самом деле подробности реализации функции оркестратора не имеют значения. Это может быть обычная функция оркестратора, которая начинает и завершает работу, или же выполняющаяся бесконечно (т. е. [вечная оркестрация](durable-functions-eternal-orchestrations.md)). Важно то, что в любой момент времени выполняется только один экземпляр.

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Сведения о вызове вложенных оркестраций](durable-functions-sub-orchestrations.md)

---
title: "Диагностика надежных служб с отслеживанием состояния Azure Service Fabric | Документация Майкрософт"
description: "Диагностические функции для надежных служб с отслеживанием состояния в Azure Service Fabric"
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: 
ms.assetid: ae0e8f99-69ab-4d45-896d-1fa80ed45659
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/15/2017
ms.author: dekapur
ms.openlocfilehash: edcaaaf8f1619082b33195aedf1fb1abf32e85b1
ms.sourcegitcommit: 804db51744e24dca10f06a89fe950ddad8b6a22d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2017
---
# <a name="diagnostic-functionality-for-stateful-reliable-services"></a>Диагностические функции для надежных служб с отслеживанием состояния
Класс StatefulServiceBase служб Reliable Services с отслеживанием состояния Azure Service Fabric генерирует события [EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx), которые можно использовать для отладки. Они позволяют исследовать работу среды выполнения и помогают устранять неполадки.

## <a name="eventsource-events"></a>События EventSource
Имя EventSource для класса StatefulServiceBase надежных служб с отслеживанием состояния — Microsoft-ServiceFabric-Services. При [отладке службы в Visual Studio](service-fabric-debugging-your-application.md). события из этого источника отображаются в окне [Diagnostics Events](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md#view-service-fabric-system-events-in-visual-studio) (События диагностики).

Для сбора и просмотра событий EventSource вы можете использовать такие средства и технологии, как [PerfView](http://www.microsoft.com/download/details.aspx?id=28567), [система диагностики Azure](../cloud-services/cloud-services-dotnet-diagnostics.md) и [библиотека Microsoft TraceEvent](http://www.nuget.org/packages/Microsoft.Diagnostics.Tracing.TraceEvent).

## <a name="events"></a>События
| Имя события | Идентификатор события | Уровень | Описание события |
| --- | --- | --- | --- |
| StatefulRunAsyncInvocation |1 |Информация |Генерируется, когда запускается задача RunAsync службы. |
| StatefulRunAsyncCancellation |2 |Информация |Генерируется, когда отменяется задача RunAsync службы. |
| StatefulRunAsyncCompletion |3 |Информация |Генерируется, когда завершается задача RunAsync службы. |
| StatefulRunAsyncSlowCancellation |4. |Предупреждение |Генерируется, когда отмена задачи RunAsync службы выполняется слишком долго. |
| StatefulRunAsyncFailure |5 |Ошибка |Генерируется, когда задача RunAsync службы вызывает исключение. |

## <a name="interpret-events"></a>Интерпретация событий
События StatefulRunAsyncInvocation, StatefulRunAsyncCompletion и StatefulRunAsyncCancellation полезны для разработчика служб тем, что позволяют понять жизненный цикл службы и правильно определить время запуска, отмены или завершения службы. Эти сведения могут быть полезными при отладке службы и анализе ее жизненного цикла.

Разработчикам служб следует обратить особое внимание на события StatefulRunAsyncSlowCancellation и StatefulRunAsyncFailure, так как они свидетельствуют о наличии проблем со службой.

Событие StatefulRunAsyncFailure генерируется каждый раз, когда задача RunAsync() службы вызывает исключение. Обычно исключение указывает на наличие ошибки или дефекта в работе службы. Кроме того, исключение приводит к отказу службы и, как следствие, ее перемещению на другой узел. Эта операция может быть ресурсоемкой, из-за чего входящие запросы будут обработаны с задержкой. Разработчикам службы следует определить причину исключения и по возможности нейтрализовать ее.

Событие StatefulRunAsyncSlowCancellation генерируется всякий раз, когда выполнение запроса на отмену задачи RunAsync занимает больше четырех секунд. Если отмена выполняется слишком долго, служба не может быстро перезапуститься на другом узле. Кроме того, снижается ее общая доступность.

## <a name="next-steps"></a>Дополнительная информация
[Поставщики EventSource в PerfView](https://blogs.msdn.microsoft.com/vancem/2012/07/09/introduction-tutorial-logging-etw-events-in-c-system-diagnostics-tracing-eventsource/)

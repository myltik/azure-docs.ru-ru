---
title: "Диагностика Reliable Services с отслеживанием состояния | Документация Майкрософт"
description: "Диагностические функции для надежных служб с отслеживанием состояния"
services: service-fabric
documentationcenter: .net
author: AlanWarwick
manager: timlt
editor: 
ms.assetid: ae0e8f99-69ab-4d45-896d-1fa80ed45659
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/04/2017
ms.author: alanwar
translationtype: Human Translation
ms.sourcegitcommit: 3ed67788fbbe0c4fb820cfd1525d8c9ee5154446
ms.openlocfilehash: 5da5707900b35be69018ba4e9c8efb05d8a74a9d


---
# <a name="diagnostic-functionality-for-stateful-reliable-services"></a>Диагностические функции для надежных служб с отслеживанием состояния
Класс StatefulServiceBase служб Reliable Services с отслеживанием состояния генерирует события [EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx), которые можно использовать для отладки. Они позволяют исследовать работу среды выполнения и помогают устранять неполадки.

## <a name="eventsource-events"></a>События EventSource
Именем EventSource для класса StatefulServiceBase надежных служб с отслеживанием состояния является Microsoft-ServiceFabric-Services. При [отладке службы в Visual Studio](service-fabric-debugging-your-application.md). события из этого источника отображаются в окне [Diagnostics Events](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md#view-service-fabric-system-events-in-visual-studio) (События диагностики).

Для сбора и просмотра событий EventSource вы можете использовать такие средства и технологии, как [PerfView](http://www.microsoft.com/download/details.aspx?id=28567), [система диагностики Microsoft Azure](../cloud-services/cloud-services-dotnet-diagnostics.md) и [библиотека Microsoft TraceEvent](http://www.nuget.org/packages/Microsoft.Diagnostics.Tracing.TraceEvent).

## <a name="events"></a>События
| Имя события | Идентификатор события | Уровень | Описание события |
| --- | --- | --- | --- |
| StatefulRunAsyncInvocation |1 |Информация |Генерируется, когда запускается задача RunAsync службы. |
| StatefulRunAsyncCancellation |2 |Информация |Генерируется, когда отменяется задача RunAsync службы. |
| StatefulRunAsyncCompletion |3 |Информация |Генерируется, когда завершается выполнение задачи RunAsync службы. |
| StatefulRunAsyncSlowCancellation |4. |Предупреждение |Генерируется, когда отмена задачи RunAsync службы выполняется слишком долго. |
| StatefulRunAsyncFailure |5 |Ошибка |Генерируется, когда задача RunAsync службы вызывает исключение. |

## <a name="interpret-events"></a>Интерпретация событий
События StatefulRunAsyncInvocation, StatefulRunAsyncCompletion и StatefulRunAsyncCancellation полезны для разработчика служб тем, что позволяют понять жизненный цикл службы и правильно определить время запуска, отмены или завершения службы. Это может быть полезно при отладке неполадок службы или для понимания ее жизненного цикла.

Разработчикам служб следует обратить особое внимание на события StatefulRunAsyncSlowCancellation и StatefulRunAsyncFailure, так как они свидетельствуют о наличии проблем со службой.

Событие StatefulRunAsyncFailure генерируется каждый раз, когда задача RunAsync() службы вызывает исключение. Обычно исключение указывает на наличие ошибки или дефекта в работе службы. Кроме того, исключение приводит к отказу службы и, как следствие, ее перемещению на другой узел. Это может быть ресурсоемкой операцией, из-за чего входящие запросы будут обработаны с задержкой. Разработчикам службы следует определить причину исключения и по возможности нейтрализовать ее.

Событие StatefulRunAsyncSlowCancellation генерируется всякий раз, когда выполнение запроса на отмену задачи RunAsync занимает больше четырех секунд. Если отмена занимает слишком много времени, служба не может быстро перезапуститься на другом узле. Кроме того, снижается ее общая доступность.

## <a name="next-steps"></a>Дальнейшие действия
* [Поставщики EventSource в PerfView](https://blogs.msdn.microsoft.com/vancem/2012/07/09/introduction-tutorial-logging-etw-events-in-c-system-diagnostics-tracing-eventsource/)



<!--HONumber=Jan17_HO1-->



<properties
   pageTitle="Надежные службы с отслеживанием состояния | Microsoft Azure"
   description="Диагностические функции для надежных служб с отслеживанием состояния"
   services="service-fabric"
   documentationCenter=".net"
   authors="AlanWarwick"
   manager="timlt"
   editor=""/>

<tags
   ms.service="Service-Fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="05/17/2016"
   ms.author="alanwar"/>

# Диагностические функции для надежных служб с отслеживанием состояния
Класс StatefulServiceBase надежных служб с отслеживанием состояния генерирует события [EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx), которые можно использовать для отладки. Они позволяют исследовать работу среды выполнения и помогают устранять неполадки.

## События EventSource
Именем EventSource для класса StatefulServiceBase надежных служб с отслеживанием состояния является Microsoft-ServiceFabric-Services. События из этого источника отображаются в окне [События диагностики](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md#view-service-fabric-system-events-in-visual-studio) при [отладке службы в Visual Studio](service-fabric-debugging-your-application.md).

Для сбора и просмотра событий EventSource вы можете использовать такие инструменты и технологии, как [PerfView](http://www.microsoft.com/download/details.aspx?id=28567), [система диагностики Microsoft Azure](../cloud-services/cloud-services-dotnet-diagnostics.md) и [библиотека Microsoft TraceEvent](http://www.nuget.org/packages/Microsoft.Diagnostics.Tracing.TraceEvent).

## События

|Имя события|Идентификатор события|Уровень|Описание события|
|----------|--------|-----|-----------------|
|StatefulRunAsyncInvocation|1|Информация|Генерируется, когда запускается задача RunAsync службы.|
|StatefulRunAsyncCancellation|2|Информация|Генерируется, когда отменяется задача RunAsync службы.|
|StatefulRunAsyncCompletion|3|Информация|Генерируется, когда завершается выполнение задачи RunAsync службы.|
|StatefulRunAsyncSlowCancellation|4\.|Предупреждение|Генерируется, когда отмена задачи RunAsync службы выполняется слишком долго.|
|StatefulRunAsyncFailure|5|Ошибка|Генерируется, когда задача RunAsync службы вызывает исключение.|

## Интерпретация событий

События StatefulRunAsyncInvocation, StatefulRunAsyncCompletion и StatefulRunAsyncCancellation полезны для разработчика служб тем, что позволяют понять жизненный цикл службы и правильно определить время запуска, отмены или завершения службы. Это может быть полезно при отладке неполадок службы или для понимания ее жизненного цикла.

Разработчикам служб следует обратить особое внимание на события StatefulRunAsyncSlowCancellation и StatefulRunAsyncFailure, так как они свидетельствуют о наличии проблем со службой.

Событие StatefulRunAsyncFailure генерируется каждый раз, когда задача RunAsync() службы вызывает исключение. Обычно исключение указывает на наличие ошибки или дефекта в работе службы. Кроме того, исключение приводит к отказу службы и, как следствие, ее перемещению на другой узел. Это может быть ресурсоемкой операцией, из-за чего входящие запросы будут обработаны с задержкой. Разработчикам службы следует определить причину исключения и по возможности нейтрализовать ее.

Событие StatefulRunAsyncSlowCancellation генерируется всякий раз, когда выполнение запроса на отмену задачи RunAsync занимает больше четырех секунд. Если отмена занимает слишком много времени, служба не может быстро перезапуститься на другом узле. Кроме того, снижается ее общая доступность.

<!---HONumber=AcomDC_0518_2016-->
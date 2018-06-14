---
title: Мониторинг на уровне приложений службы Azure Service Fabric | Документы Майкрософт
description: Сведения о журналах и событиях на уровне приложений и служб, используемых для мониторинга и диагностики кластеров Azure Service Fabric.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/20/2018
ms.author: dekapur
ms.openlocfilehash: b8118d83e2be452c6aa5bbc8b7a3c220d26903a1
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/16/2018
ms.locfileid: "34204337"
---
# <a name="application-and-service-level-logging"></a>Ведение журналов на уровне приложений и служб

В основе большинства других аспектов мониторинга служб лежит инструментирование кода. Инструментирование — это единственный способ выяснить, что какой-то компонент работает неправильно, и диагностировать проблему для ее дальнейшего исправления. Технически есть возможность подключить отладчик к рабочей службе, но обычно так никто не делает. Поэтому вам нужны подробные данные инструментирования.

Некоторые решения выполняют инструментирование кода автоматически. Они могут вполне успешно работать, но почти всегда требуется инструментирование вручную. В итоге у вас должно быть достаточно информации для экспертной отладки приложения. В этом документе описываются различные подходы к инструментированию кода и преимущества этих подходов для разных ситуаций.

Примеры использования этих подходов см. в статье [Добавление ведения журнала в приложение Service Fabric](service-fabric-how-to-diagnostics-log.md).

## <a name="eventsource"></a>EventSource

Если вы создаете решение Service Fabric из шаблона в Visual Studio, в нем создается класс **ServiceEventSource** или **ActorEventSource**, производный от **EventSource**. При этом создается шаблон, в который вы можете добавлять события для конкретного приложения или службы. Имя **EventSource** **должно** быть уникальным, поэтому потребуется изменить стандартное имя MyCompany-&lt;решение&gt;-&lt;проект&gt;. Наличие нескольких определений **EventSource** с одинаковым именем приведет к возникновению проблемы во время выполнения. У каждого определенного события должен быть уникальный идентификатор. Если идентификатор не является уникальным, происходит сбой во время выполнения. Некоторые организации предварительно назначают для идентификаторов диапазоны значений, чтобы избежать конфликтов между командами разработчиков. Дополнительные сведения см. в [блоге Вэнса (Vance)](https://blogs.msdn.microsoft.com/vancem/2012/07/09/introduction-tutorial-logging-etw-events-in-c-system-diagnostics-tracing-eventsource/) или в [документации MSDN](https://msdn.microsoft.com/library/dn774985(v=pandp.20).aspx).

## <a name="aspnet-core-logging"></a>Ведение журнала ASP.NET Core

Очень важно тщательно спланировать методы инструментирования кода. Правильный план инструментирования позволит избежать потенциальной дестабилизации базы кода, которая повлечет за собой повторное инструментрирование кода. Чтобы снизить риск, вы можете применить библиотеку инструментирования, например [Microsoft.Extensions.Logging](https://www.nuget.org/packages/Microsoft.Extensions.Logging/), которая входит в Microsoft ASP.NET Core. ASP.NET Core предоставляет интерфейс [ILogger](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.logging.ilogger), который можно подключить к любому поставщику, с минимальными изменениями существующего кода. Код ASP.NET Core можно использовать на платформах Windows, Linux и в полной версии .NET Framework, то есть код инструментирования будет полностью стандартизирован.

## <a name="application-insights-sdk"></a>Пакет SDK для Application Insights

Azure Application Insights поставляется с широкими возможностями интеграции с Azure Service Fabric. Пользователи могут добавлять пакеты NuGet ИИ Service Fabric и получать данные и журналы, созданные и собранные готовыми к просмотру на портале Azure. Кроме того пользователям рекомендуется добавлять свои собственные данные телеметрии для диагностики и отладки своих приложений, а также получения сведения о самых используемых службах и частей приложений. Класс [TelemetryClient](https://docs.microsoft.com/dotnet/api/microsoft.applicationinsights.telemetryclient?view=azure-dotnet) в пакете SDK предоставляет много способов отслеживать данные телеметрии в приложении. Дополнительные сведения об инструментировании и добавлении Application Insights в приложение см. руководстве по [наблюдению и диагностике приложений .NET](service-fabric-tutorial-monitoring-aspnet.md)


## <a name="next-steps"></a>Дополнительная информация

После выбора поставщика ведения журнала для инструментирования приложений и служб вам потребуется агрегировать журналы и события перед отправкой в любую платформу аналитики. См. дополнительные сведения об [Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md), [EventFlow](service-fabric-diagnostics-event-aggregation-eventflow.md) и [WAD](service-fabric-diagnostics-event-aggregation-wad.md), чтобы ознакомиться с некоторыми рекомендуемыми параметрами.

---
title: Анализ событий Azure Service Fabric с помощью Log Analytics | Документация Майкрософт
description: Ознакомьтесь со сведениями о визуализации и анализе событий с использованием Log Analytics для мониторинга и диагностики кластеров Azure Service Fabric.
services: service-fabric
documentationcenter: .net
author: srrengar
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/16/2018
ms.author: dekapur; srrengar
ms.openlocfilehash: 8efbc1d400f1d32e6aee2c1e2d78847bea786940
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2018
---
# <a name="event-analysis-and-visualization-with-log-analytics"></a>Анализ и визуализация событий с помощью Log Analytics

Log Analytics (также известен как Operations Management Suite (OMS)) — это набор служб управления, которые помогают в мониторинге и диагностике приложений и служб, размещенных в облаке. В этой статье описано, как выполнять запросы в Log Analytics для получения полезных сведений и устранения неполадок, которые могут произойти в кластере. Рассматриваются следующие распространенные вопросы:

* Как устранить неполадки событий работоспособности?
* Как узнать, что узел вышел из строя?
* Как узнать, запущены или остановлены ли службы приложения?

## <a name="log-analytics-workspace"></a>Рабочая область Log Analytics

Служба Log Analytics собирает данные из управляемых ресурсов, включая таблицу или агент службы хранилища Azure, и сохраняет их в центральном репозитории. Эти данные далее могут использоваться для анализа, оповещения, визуализации или последующего экспорта. Log Analytics поддерживает события, данные производительности, а также любые иные пользовательские данные. Просмотрите сведения о [действиях по настройке расширения диагностики для статистической обработки событий](service-fabric-diagnostics-event-aggregation-wad.md) и [действиях по созданию рабочей области Log Analytics для чтения данных о событиях в хранилище](service-fabric-diagnostics-oms-setup.md), чтобы настроить передачу данных в Log Analytics.

После получения данных службой Log Analytics можно применять ряд *решений для управления* из состава набора Azure для отслеживания входящих данных. Эти решения оптимизированы для нескольких сценариев. К ним относятся *Аналитика Service Fabric* и *Контейнеры* — два наиболее важных решения для диагностики и мониторинга кластеров Service Fabric. В этой статье описывается использование решения "Аналитика Service Fabric", созданного с помощью рабочей области.

## <a name="access-the-service-fabric-analytics-solution"></a>Получение доступа к решению "Аналитика Service Fabric"

1. Перейдите в группу ресурсов, в которой вы создали решение "Аналитика Service Fabric". Выберите ресурс **ServiceFabric\<имя_рабочей_области_OMS\>** и перейдите к странице обзора.

2. В верхней части страницы обзора щелкните ссылку вверху, чтобы перейти на портал OMS.

    ![Ссылка на портал OMS](media/service-fabric-diagnostics-event-analysis-oms/oms-portal-link.png)

3. Вы вошли на портал OMS и можете просмотреть включенные решения. Щелкните диаграмму под названием Service Fabric (первое изображение ниже), чтобы перейти к решению Service Fabric (второе изображение ниже).

    ![Решение OMS Service Fabric](media/service-fabric-diagnostics-event-analysis-oms/oms-workspace-all-solutions.png)

    ![Решение OMS Service Fabric](media/service-fabric-diagnostics-event-analysis-oms/service-fabric-analytics-new.png)

На рисунке выше показана домашняя страница решения "Аналитика Service Fabric". На этом снимке экрана представлена информация, касающаяся работы вашего кластера. Если включить диагностику во время создания кластера, можно просмотреть такие события: 

* [Операционный канал](service-fabric-diagnostics-event-generation-operational.md). Операции высокого уровня, которые выполняет платформа Service Fabric (коллекция системных служб).
* [События модели программирования на основе Reliable Actors](service-fabric-reliable-actors-diagnostics.md).
* [События модели программирования на основе Reliable Services](service-fabric-reliable-services-diagnostics.md).

>[!NOTE]
>Помимо операционного канала можно собирать дополнительные системные события, [обновив файл конфигурации расширения диагностики](service-fabric-diagnostics-event-aggregation-wad.md#log-collection-configurations).

### <a name="view-operational-events-including-actions-on-nodes"></a>Просмотр операционных событий, включая действия на узлах

1. На странице аналитики Service Fabric на портале OMS щелкните диаграмму для операционного канала.

    ![Операционный канал решения OMS Service Fabric](media/service-fabric-diagnostics-event-analysis-oms/service-fabric-analytics-new-operational.png)

2. Щелкните таблицу, чтобы просмотреть список событий. Здесь вы увидите все собранные системные события. Эти данные взяты из таблицы WADServiceFabricSystemEventsTable в учетной записи хранения Azure, а события служб Reliable Services и субъектов Reliable Actors, которые показаны далее, также взяты из этих соответствующих таблиц.
    
    ![Запрос OMS к операционному каналу](media/service-fabric-diagnostics-event-analysis-oms/oms-query-operational-channel.png)

Кроме того, чтобы найти необходимые данные, можно щелкнуть значок лупы в левой части экрана и воспользоваться языком запросов Kusto. Например, чтобы найти все действия, выполняемые в узлах кластера, можно использовать приведенный ниже запрос. Идентификаторы событий, используемые ниже, можно найти в [справочнике по событиям операционного канала](service-fabric-diagnostics-event-generation-operational.md).

```kusto
ServiceFabricOperationalEvent
| where EventId < 25627 and EventId > 25619 
```

Вы можете запрашивать много дополнительных полей (например, конкретные узлы (Компьютер) и системную службу (TaskName)).

### <a name="view-service-fabric-reliable-service-and-actor-events"></a>Просмотр событий служб Reliable Services и субъектов Reliable Actors в Service Fabric

1. На странице аналитики Service Fabric на портале OMS щелкните диаграмму для Reliable Services.

    ![Reliable Services в решении OMS Service Fabric](media/service-fabric-diagnostics-event-analysis-oms/service-fabric-analytics-reliable-services.png)

2. Щелкните таблицу, чтобы просмотреть список событий. Здесь можно просмотреть события из служб Reliable Services. Когда служба RunAsync запускается и завершается, могут отображаться различные события. Обычно это происходит при развертывании и обновлении. 

    ![Запрос OMS к Reliable Services](media/service-fabric-diagnostics-event-analysis-oms/oms-query-reliable-services.png)

Аналогичным образом можно просмотреть события субъектов Reliable Actors. Чтобы настроить более подробные события для субъектов Reliable Actors, необходимо изменить `scheduledTransferKeywordFilter` в конфигурации для расширения диагностики (см. ниже). Сведения об этих значениях можно найти в [справочнике по событиям субъектов Reliable Actors](service-fabric-reliable-actors-diagnostics.md#keywords).

```json
"EtwEventSourceProviderConfiguration": [
                {
                    "provider": "Microsoft-ServiceFabric-Actors",
                    "scheduledTransferKeywordFilter": "1",
                    "scheduledTransferPeriod": "PT5M",
                    "DefaultEvents": {
                    "eventDestination": "ServiceFabricReliableActorEventTable"
                    }
                },
```

Язык запросов Kusto предоставляет широкие возможности. Вы можете выполнить другой полезный запрос, чтобы узнать, какие узлы создают больше событий. Запрос на снимке экрана, приведенном ниже, показывает событие Reliable Services, агрегированное с конкретной службой и узлом.

![События запросов OMS на каждом узле](media/service-fabric-diagnostics-event-analysis-oms/oms-query-events-per-node.png)

## <a name="next-steps"></a>Дополнительная информация

* Чтобы включить мониторинг инфраструктуры (т. е. счетчики производительности), перейдите к изучению [этой статьи о добавлении агента OMS](service-fabric-diagnostics-oms-agent.md). Агент собирает счетчики производительности и добавляет их в имеющуюся рабочую область.
* Для локальных кластеров OMS предлагает шлюз (прокси-сервер переадресации HTTP), который можно использовать для отправки данных в OMS. Дополнительные сведения см. в разделе [Подключения компьютеров к OMS с помощью шлюза OMS без доступа к Интернету](../log-analytics/log-analytics-oms-gateway.md).
* Настройте OMS, чтобы установить [автоматические оповещения](../log-analytics/log-analytics-alerts.md), которые помогают выполнять обнаружение и диагностику.
* Ознакомьтесь с функциями [поиска по журналам и запросов к журналам](../log-analytics/log-analytics-log-searches.md), которые являются частью решения Log Analytics.
* Более подробные сведения о службе Log Analytics и ее возможностях см. в статье [Что такое Operations Management Suite (OMS)?](../operations-management-suite/operations-management-suite-overview.md)

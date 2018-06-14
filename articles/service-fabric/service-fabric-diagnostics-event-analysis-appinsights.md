---
title: Анализ событий Azure Service Fabric с помощью Application Insights | Документы Майкрософт
description: Ознакомьтесь со сведениями о визуализации и анализе событий с использованием Application Insights для мониторинга и диагностики кластеров Azure Service Fabric.
services: service-fabric
documentationcenter: .net
author: srrengar
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/04/2018
ms.author: srrengar
ms.openlocfilehash: 29adf362fdacdb793af071fa6d7bd59214536374
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/16/2018
ms.locfileid: "34207759"
---
# <a name="event-analysis-and-visualization-with-application-insights"></a>Анализ и визуализация событий с помощью Application Insights

Azure Application Insights — это расширяемая платформа для отслеживания и диагностики приложений. Она включает в себя эффективное средство аналитики и запросов, настраиваемую панель мониторинга, визуализации и дополнительные параметры, включая автоматическое оповещение. Это рекомендуемая платформа для отслеживания и диагностики приложений и служб Service Fabric. Эта статья поможет найти ответ на следующие распространенные вопросы:

* как узнать, что происходит в приложениях и службах, и как собрать данные телеметрии;
* как устранить неполадки в приложениях, особенно если это несколько взаимосвязанных служб;
* как получить метрики выполнения служб, такие как время загрузки страниц и данные об HTTP-запросах.

Эта статья предназначена для того, чтобы продемонстрировать процессы анализа и устранения неполадок в Application Insights. Если вас интересует, как установить и настроить службу Application Insights для интеграции с Service Fabric, ознакомьтесь с этим [руководством](service-fabric-tutorial-monitoring-aspnet.md).

## <a name="monitoring-in-app-insights"></a>Мониторинг в Application Insights

Application Insights имеет широкие возможности для использования Service Fabric. На странице "Обзор" в Application Insights предоставляются основные сведения о службе, например время отклика ответа и число обработанных запросов. Щелкнув кнопку "Поиск" вверху, вы увидите список последних запросов в приложении. Кроме того, вы увидите здесь неудачные запросы и сможете продиагностировать возникшие ошибки.

![Страница "Обзор" в Application Insights](media/service-fabric-diagnostics-event-analysis-appinsights/ai-overview.png)

На предыдущем рисунке вы видите справа панель со списком элементов двух основных типов: запросы и события. Запросы поступают в виде вызовов API-интерфейса приложения, в нашем примере это HTTP-запросы. Событиями называются любые пользовательские события, которые можно добавить в любое место кода в качестве данных телеметрии. Дополнительные сведения об инструментировании приложений вы найдете в статье [API Application Insights для пользовательских событий и метрик](../application-insights/app-insights-api-custom-events-metrics.md). Щелкнув запрос, вы увидите представленные на следующем рисунке подробности о нем, включая сведения о Service Fabric, которые собираются в пакете NuGet Service Fabric для Application Insights. Эти данные полезны для устранения неполадок и анализа состояния приложения. Все они доступны для поиска в Application Insights.

![Сведения о запросе Application Insights](media/service-fabric-diagnostics-event-analysis-appinsights/ai-request-details.png)

Application Insights имеет специальное представление для запросов по поступающим данным. Щелкните "Обозреватель метрик" в верхней части страницы "Обзор", чтобы перейти на портал Application Insights. Здесь вы можете с помощью языка запросов Kusto получить данные об описанных выше пользовательских событиях, запросах, исключениях, счетчиках производительности и других метриках. В следующем примере показаны все запросы за последний час.

![Сведения о запросе Application Insights](media/service-fabric-diagnostics-event-analysis-appinsights/ai-metrics-explorer.png)

Для дальнейшего изучения возможностей портала Application Insights, перейдите на [портал документации Application Insights](../application-insights/app-insights-dashboards.md).

### <a name="configuring-ai-with-wad"></a>Настройка Application Insights с помощью WAD

>[!NOTE]
>В настоящее время это распространяется только на кластеры Windows.

Существуют два основных способа отправки данных из WAD в Azure Application Insights, для обеспечения которой необходимо добавить приемник Application Insights в конфигурацию WAD, как описано в [этой статье](../monitoring-and-diagnostics/azure-diagnostics-configure-application-insights.md).

#### <a name="add-an-ai-instrumentation-key-when-creating-a-cluster-in-azure-portal"></a>Добавление ключа инструментирования Application Insights при создании кластера на портале Azure

![Добавление AIKey](media/service-fabric-diagnostics-event-analysis-appinsights/azure-enable-diagnostics.png)

Если при создании кластера диагностика включена, появляется дополнительное поле для ввода ключа инструментирования Application Insights. Если вставить ключ инструментирования Application Insights, приемник Application Insights автоматически настроится в шаблоне Resource Manager, который используется для развертывания кластера.

#### <a name="add-the-ai-sink-to-the-resource-manager-template"></a>Добавление приемника Application Insights в шаблон Resource Manager

В разделе WadCfg шаблона Resource Manager добавьте приемник, внеся два указанных ниже изменения.

1. Добавьте конфигурацию приемника сразу после объявления `DiagnosticMonitorConfiguration`:

    ```json
    "SinksConfig": {
        "Sink": [
            {
                "name": "applicationInsights",
                "ApplicationInsights": "***ADD INSTRUMENTATION KEY HERE***"
            }
        ]
    }

    ```

2. Включите приемник в `DiagnosticMonitorConfiguration`, добавив следующую строку в `DiagnosticMonitorConfiguration` `WadCfg` (сразу перед объявлением `EtwProviders`):

    ```json
    "sinks": "applicationInsights"
    ```

В обоих приведенных выше фрагментах кода для указания приемника использовано имя applicationInsights. Это не является обязательным. Если имя приемника включено в элемент sinks, именем может быть любая строка.

В настоящее время журналы кластера отображаются в средстве просмотра журналов Application Insights как **трассировки**. Так как большинство трассировок, поступающих от платформы, имеют уровень "Информационный", вы можете изменить конфигурацию приемника так, чтобы отправлялись журналы только типов "Критический" или "Ошибка". Для этого в приемник можно добавить каналы, как показано в [этой статье](../monitoring-and-diagnostics/azure-diagnostics-configure-application-insights.md).

>[!NOTE]
>Если вы укажете на портале или в шаблоне Resource Manager неправильный ключ инструментирования Application Insights, придется вручную заменить его, затем обновить или повторно развернуть кластер.

### <a name="configuring-ai-with-eventflow"></a>Настройка Application Insights с помощью EventFlow

Если для статистической обработки событий используется EventFlow, импортируйте пакет NuGet `Microsoft.Diagnostics.EventFlow.Output.ApplicationInsights`. В разделе *outputs* файла *eventFlowConfig.json* должен присутствовать следующий код:

```json
"outputs": [
    {
        "type": "ApplicationInsights",
        "instrumentationKey": "***ADD INSTRUMENTATION KEY HERE***"
    }
]
```

Обязательно внесите необходимые изменения в фильтры, а также включите все остальные входные данные (с соответствующими пакетами NuGet).

## <a name="aisdk"></a>AI.SDK

В качестве решений для статистической обработки мы рекомендуем использовать EventFlow и WAD, так как они лучше поддерживают модульный подход к диагностике и мониторингу. Например, чтобы изменить выходные данные EventFlow, не придется менять инструментирование, достаточно лишь внести небольшое изменение в файл конфигурации. Однако если вы решите инвестировать в использование Application Insights и, скорее всего, не будете переходить на другую платформу, рассмотрите возможность применения нового пакета SDK для Application Insights с целью статистической обработки событий и отправки их в Application Insights. Таким образом, вам больше не придется настраивать EventFlow для отправки данных в Application Insights. Вместо этого вы установите пакет NuGet Service Fabric для Application Insights. Подробные сведения о пакете можно найти [здесь](https://github.com/Microsoft/ApplicationInsights-ServiceFabric).

На странице [Поддержка микрослужб и контейнеров в Application Insights](https://azure.microsoft.com/blog/app-insights-microservices/) описываются некоторые новые возможности, над которыми ведется работа (в настоящее время они доступны в виде бета-версии) и которые расширяют набор встроенных параметров мониторинга в Application Insights. Они включают в себя отслеживание зависимостей (используется при построении карты AppMap для всех служб и приложений в кластере и взаимосвязей между ними), а также улучшенное сопоставление трассировок, поступающих из служб (помогает выявлять проблемы в рабочем процессе приложения или службы).

Если вы ведете разработку на .NET, скорее всего, будете использовать какие-либо модели программирования Service Fabric и хотите применять Application Insights в качестве платформы для визуализации и анализа данных событий и журналов, мы рекомендуем схему SDK для Application Insights в качестве рабочего процесса мониторинга и диагностики. Чтобы приступить к использованию Application Insights для сбора и отображения журналов, ознакомьтесь с [этой](../application-insights/app-insights-asp-net-more.md) и [этой](../application-insights/app-insights-asp-net-trace-logs.md) статьями.

## <a name="navigating-the-ai-resource-in-azure-portal"></a>Навигация по ресурсу Application Insights на портале Azure

После настройки Application Insights для вывода событий и журналов данные должны начать отображаться в ресурсе Application Insights через несколько минут. Перейдите к ресурсу Application Insights. Должна открыться панель мониторинга ресурса Application Insights. На панели задач Application Insights щелкните **Поиск**, чтобы просмотреть последние полученные трассировки и при необходимости отфильтровать их.

*Обозреватель метрик* — это полезное средство для создания пользовательских панелей мониторинга на основе метрик, которые могут предоставляться приложениями, службами и кластером. Сведения о настройке диаграмм на основе собираемых данных см. в статье [Исследование метрик в Application Insights](../application-insights/app-insights-metrics-explorer.md).

Если щелкнуть **Аналитика**, откроется портал аналитики Application Insights, на котором доступно больше возможностей по запросу событий и трассировок. Дополнительные сведения см. в статье [Аналитика в Application Insights](../application-insights/app-insights-analytics.md).

## <a name="next-steps"></a>Дополнительная информация

* [Настройте оповещения в Application Insights](../application-insights/app-insights-alerts.md), чтобы узнавать об изменениях в производительности или характере использования.
* [Интеллектуальное обнаружение в Application Insights](../application-insights/app-insights-proactive-diagnostics.md) осуществляет упреждающий анализ данных телеметрии, отправляемых в Application Insights, и предупреждает о потенциальных проблемах с производительностью.

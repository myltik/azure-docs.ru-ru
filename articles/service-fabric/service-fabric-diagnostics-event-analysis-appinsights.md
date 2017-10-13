---
title: "Анализ событий Azure Service Fabric с помощью Application Insights | Документы Майкрософт"
description: "Ознакомьтесь со сведениями о визуализации и анализе событий с использованием Application Insights для мониторинга и диагностики кластеров Azure Service Fabric."
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/26/2017
ms.author: dekapur
ms.openlocfilehash: 4085a607b800f4f4f155cdc266bc203b0858fd7c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="event-analysis-and-visualization-with-application-insights"></a>Анализ и визуализация событий с помощью Application Insights

Azure Application Insights — это расширяемая платформа для отслеживания и диагностики приложений. Она включает в себя эффективное средство аналитики и запросов, настраиваемую панель мониторинга, визуализации и дополнительные параметры, включая автоматическое оповещение. Это рекомендуемая платформа для отслеживания и диагностики приложений и служб Service Fabric.

## <a name="setting-up-application-insights"></a>Настройка Application Insights

### <a name="creating-an-ai-resource"></a>Создание ресурса Application Insights

Чтобы создать ресурс Application Insights, перейдите в Azure Marketplace и выполните поиск по запросу "Application Insights". Требуемое решение должно быть первым в списке результатов (в категории "Интернет и мобильные устройства"). Нажмите кнопку **Создать** справа (убедитесь в том, что путь соответствует приведенной ниже иллюстрации).

![Новый ресурс Application Insights](media/service-fabric-diagnostics-event-analysis-appinsights/create-new-ai-resource.png)

Для правильной подготовки ресурсов необходимо указать некоторые сведения. В поле *Тип приложения* выберите значение "Веб-приложение ASP.NET", если вы будете использовать какую-либо из моделей программирования Service Fabric или публиковать приложение .NET в кластере. Выберите значение "Общее", если вы будете развертывать гостевые исполняемые файлы и контейнеры. Как правило, по умолчанию следует использовать значение "Веб-приложение ASP.NET", чтобы параметры оставались доступными в будущем. Имя может быть любым на ваш выбор, а группу ресурсов и подписку можно изменить после развертывания ресурса. Рекомендуется, чтоб ресурс Application Insights входил в ту же группу ресурсов , что и кластер. Если вам требуются дополнительные сведения, ознакомьтесь с разделом [Создание ресурса Application Insights](../application-insights/app-insights-create-new-resource.md).

Для настройки Application Insights с помощью средства статистической обработки событий требуется ключ инструментирования Application Insights. После настройки ресурса Application Insights (занимает несколько минут после проверки развертывания) перейдите к нему и найдите раздел **Свойства** на левой панели навигации. Откроется новая колонка с *ключом инструментирования*. Если необходимо изменить подписку или группу ресурсов, это также можно сделать здесь.

### <a name="configuring-ai-with-wad"></a>Настройка Application Insights с помощью WAD

Существуют два основных способа отправки данных из WAD в Azure Application Insights, для обеспечения которой необходимо добавить приемник Application Insights в конфигурацию WAD, как описано в [этой статье](../monitoring-and-diagnostics/azure-diagnostics-configure-application-insights.md).

#### <a name="add-an-ai-instrumentation-key-when-creating-a-cluster-in-azure-portal"></a>Добавление ключа инструментирования Application Insights при создании кластера на портале Azure

![Добавление AIKey](media/service-fabric-diagnostics-event-analysis-appinsights/azure-enable-diagnostics.png)

Если при создании кластера диагностика включена, появляется дополнительное поле для ввода ключа инструментирования Application Insights. Если вставить в него ключ инструментирования Application Insights, приемник Application Insights будет автоматически настроен в шаблоне Resource Manager, используемом для развертывания кластера.

#### <a name="add-the-ai-sink-to-the-resource-manager-template"></a>Добавление приемника Application Insights в шаблон Resource Manager

В разделе WadCfg шаблона Resource Manager добавьте приемник, внеся два указанных ниже изменения.

1. Добавьте конфигурацию приемника.

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

2. Включите приемник в DiagnosticMonitorConfiguration, добавив в подраздел DiagnosticMonitorConfiguration раздела WadCfg следующую строку:

    ```json
    "sinks": "applicationInsights"
    ```

В обоих приведенных выше фрагментах кода имя applicationInsights использовалось для указания приемника. Это не является обязательным. Если имя приемника включено в элемент sinks, именем может быть любая строка.

В настоящее время журналы кластера отображаются в средстве просмотра журналов Application Insights как трассировки. Так как большинство трассировок, поступающих с платформы, имеют уровень "Информационный", можно также изменить конфигурацию приемника так, чтобы отправлялись журналы только типа "Критический" или "Ошибка". Для этого в приемник можно добавить каналы, как показано в [этой статье](../monitoring-and-diagnostics/azure-diagnostics-configure-application-insights.md).

>[!NOTE]
>При использовании неправильного ключа инструментирования Application Insights на портале или в шаблоне Resource Manager его необходимо поменять вручную, а затем обновить или повторно развернуть кластер. 

### <a name="configuring-ai-with-eventflow"></a>Настройка Application Insights с помощью EventFlow

Если для статистической обработки событий используется EventFlow, импортируйте пакет NuGet `Microsoft.Diagnostics.EventFlow.Output.ApplicationInsights`. В раздел *outputs* файла *eventFlowConfig.json* необходимо включить следующий код:

```json
"outputs": [
    {
        "type": "ApplicationInsights",
        // (replace the following value with your AI resource's instrumentation key)
        "instrumentationKey": "00000000-0000-0000-0000-000000000000"
    }
]
```

Обязательно внесите необходимые изменения в фильтры, а также включите все остальные входные данные (с соответствующими пакетами NuGet).

## <a name="aisdk"></a>AI.SDK

Как правило, в качестве решений для статистической обработки рекомендуется использовать EventFlow и WAD, так как они обеспечивают более модульный подход к диагностике и мониторингу, то есть если вы хотите изменить выходные данные EventFlow, изменять инструментирование не требуется. Достаточно внести простое изменение в файл конфигурации. Однако если вы решите инвестировать в использование Application Insights и, скорее всего, не будете переходить на другую платформу, рассмотрите возможность применения нового пакета SDK для Application Insights с целью статистической обработки событий и отправки их в Application Insights. Таким образом, вам больше не придется настраивать EventFlow для отправки данных в Application Insights. Вместо этого вы установите пакет NuGet Service Fabric для Application Insights. Подробные сведения о пакете можно найти [здесь](https://github.com/Microsoft/ApplicationInsights-ServiceFabric).

На странице [Поддержка микрослужб и контейнеров в Application Insights](https://azure.microsoft.com/app-insights-microservices/) описываются некоторые новые возможности, над которыми ведется работа (в настоящее время они доступны в виде бета-версии) и которые расширяют набор встроенных параметров мониторинга в Application Insights. Они включают в себя отслеживание зависимостей (используется при построении карты AppMap для всех служб и приложений в кластере и взаимосвязей между ними), а также улучшенное сопоставление трассировок, поступающих из служб (помогает выявлять проблемы в рабочем процессе приложения или службы).

Если вы ведете разработку на .NET, скорее всего, будете использовать какие-либо модели программирования Service Fabric и хотите применять Application Insights в качестве платформы для визуализации и анализа данных событий и журналов, мы рекомендуем схему SDK для Application Insights в качестве рабочего процесса мониторинга и диагностики. Чтобы приступить к использованию Application Insights для сбора и отображения журналов, ознакомьтесь с [этой](../application-insights/app-insights-asp-net-more.md) и [этой](../application-insights/app-insights-asp-net-trace-logs.md) статьями.

## <a name="navigating-the-ai-resource-in-azure-portal"></a>Навигация по ресурсу Application Insights на портале Azure

После настройки Application Insights для вывода событий и журналов данные должны начать отображаться в ресурсе Application Insights через несколько минут. Перейдите к ресурсу Application Insights. Должна открыться панель мониторинга ресурса Application Insights. На панели задач Application Insights щелкните **Поиск**, чтобы просмотреть последние полученные трассировки и при необходимости отфильтровать их.

*Обозреватель метрик* — это полезное средство для создания пользовательских панелей мониторинга на основе метрик, которые могут предоставляться приложениями, службами и кластером. Сведения о настройке диаграмм на основе собираемых данных см. в статье [Исследование метрик в Application Insights](../application-insights/app-insights-metrics-explorer.md).

Если щелкнуть **Аналитика**, откроется портал аналитики Application Insights, на котором доступно больше возможностей по запросу событий и трассировок. Дополнительные сведения см. в статье [Аналитика в Application Insights](../application-insights/app-insights-analytics.md).

## <a name="next-steps"></a>Дальнейшие действия

* [Настройте оповещения в Application Insights](../application-insights/app-insights-alerts.md), чтобы узнавать об изменениях в производительности или характере использования.
* [Интеллектуальное обнаружение в Application Insights](../application-insights/app-insights-proactive-diagnostics.md) осуществляет упреждающий анализ данных телеметрии, отправляемых в Application Insights, и предупреждает о потенциальных проблемах с производительностью.
---
title: "Поддержка нескольких ролей, микрослужб и контейнеров в Azure Application Insights | Документация Майкрософт"
description: "Мониторинг приложений, которые состоят из нескольких компонентов или ролей, для получения данных производительности и использования."
services: application-insights
documentationcenter: 
author: alancameronwills
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 05/15/2017
ms.author: cfreeman
ms.translationtype: Human Translation
ms.sourcegitcommit: c308183ffe6a01f4d4bf6f5817945629cbcedc92
ms.openlocfilehash: dad17277452081427a01d077128a3a137b2190f5
ms.contentlocale: ru-ru
ms.lasthandoff: 05/17/2017


---
# <a name="monitor-multi-role-applications-with-application-insights-preview"></a>Мониторинг приложений с несколькими ролями с помощью Application Insights (предварительная версия)

Вы можете отслеживать приложения, которые состоят из нескольких компонентов, ролей или служб, с помощью [Azure Application Insights](app-insights-overview.md). Работоспособность ролей и связи между ними отображаются на единой схеме сопоставления приложений. Вы можете отслеживать отдельные операции для нескольких ролей за счет автоматической корреляции HTTP. Диагностика контейнеров может быть интегрирована и сопоставлена с телеметрией приложения. Используйте единый ресурс Application Insights для всех ролей в вашем приложении. 

![Схема сопоставления приложений с несколькими ролями](./media/app-insights-monitor-multi-role-apps/app-map.png)

Мы используем "роль" в широком смысле слова для обозначения любого компонента приложения или службы, которая создана в виде отдельного проекта. Например, обычное бизнес-приложение может состоять из нескольких ролей, которые обмениваются данными через REST API. Роли могут размещаться в контейнерах, например Docker или Service Fabric, в облаке или локальном узле. 

### <a name="sharing-a-single-application-insights-resource"></a>Совместное использование одного ресурса Application Insights 

Ключевой метод здесь — отправлять телеметрию каждой роли в приложении в тот же ресурс Application Insights, но использовать свойство `cloud_RoleName`, чтобы отличать роли, когда это необходимо. 

В некоторых случаях это может быть неуместным, и вы можете использовать отдельные ресурсы для разных групп ролей. Например, вам может потребоваться использовать разные ресурсы для управления и выставления счетов. Использование отдельных ресурсов означает, что вы не видите все отображаемые роли на единой схеме сопоставления приложений и не можете запрашивать по ролям в [аналитике](app-insights-analytics.md). Кроме того, отдельные ресурсы необходимо настроить.

Исходя из этого, в оставшейся части этого документа рассматривается отправка данных из нескольких ролей в один ресурс Application Insights.

## <a name="configure-multi-role-applications"></a>Настройка приложений с несколькими ролями

Чтобы получить схему сопоставления приложений с несколькими ролями, вам потребуется выполнить следующее:

* **установить последнюю предварительную версию** пакета Application Insights в каждой роли приложения; 
* **настроить совместное использование единого ресурса Application Insights** для всех ролей в вашем приложении;
* **включить схему сопоставления приложений с несколькими ролями** в колонке "Предварительный просмотр".

Настройте каждую роль своего приложения, используя соответствующий метод для его типа ([ASP.NET](app-insights-asp-net.md), [Java](app-insights-java-get-started.md), [Node.js](app-insights-nodejs.md)).

### <a name="1-install-the-latest-pre-release-package"></a>1. Установка последней предварительной версии пакета

Установите пакет Appication Insights или обновите его для каждой роли в проекте. Если вы используете Visual Studio:

1. Щелкните проект правой кнопкой мыши и выберите **Управление пакетами Nuget**. 
2. Выберите параметр **Включить предварительные выпуски**.
3. Если пакеты Application Insights отображаются в списке обновлений, выберите их. 

    В противном случае найдите и установите соответствующий пакет:
    
    * Microsoft.ApplicationInsights.WindowsServer;
    * Microsoft.ApplicationInsights.ServiceFabric — для ролей, выполняющихся в качестве гостевых исполняемых файлов, и контейнеров Docker, работающих в приложениях Service Fabric;
    * Microsoft.ApplicationInsights.ServiceFabric.Native — для надежных служб в приложениях ServiceFabric;
    * Microsoft.ApplicationInsights.Kubernetes — для ролей, выполняемых в Docker на Kubernetes.

### <a name="2-share-a-single-application-insights-resource"></a>2) Настройка совместного использования одного ресурса Application Insights

* В Visual Studio щелкните проект правой кнопкой мыши и выберите **Настроить Application Insights** или **Application Insights > Настроить**. Для первого проекта используйте мастер для создания ресурса Application Insights, а для последующих проектов выбирайте тот же ресурс.
* Если вы не видите меню Application Insights, выполните настройку вручную:

   1. На [портале Azure](https://portal,azure.com) откройте ресурс Application Insights, который вы уже создали для другой роли.
   2. В колонке "Обзор" щелкните раскрывающуюся вкладку "Основные компоненты" и скопируйте **ключ инструментирования**.
   3. В своем проекте откройте файл ApplicationInsights.config и вставьте: `<InstrumentationKey>your copied key</InstrumentationKey>`.

![Скопируйте ключ инструментирования в CONFIG-файл.](./media/app-insights-monitor-multi-role-apps/copy-instrumentation-key.png)


### <a name="3-enable-multi-role-application-map"></a>3. Включение схемы сопоставления приложений с несколькими ролями

На портале Azure откройте ресурс для своего приложения. Включите *схему сопоставления приложений с несколькими ролями* в колонке "Предварительный просмотр".

### <a name="4-enable-docker-metrics-optional"></a>4. Включение метрик Docker (необязательно) 

Если роль выполняется в Docker, который размещен на виртуальной машине Azure под управлением Windows, вы можете собрать дополнительные метрики из контейнера. Вставьте в файл конфигурации [системы диагностики Azure](../monitoring-and-diagnostics/azure-diagnostics.md) следующий код:

```
"DiagnosticMonitorConfiguration": {
        ...
        "sinks": "applicationInsights",
        "DockerSources": {
                "Stats": {
                    "enabled": true,
                    "sampleRate": "PT1M"
                }
            },
        ...
    }
    ...   
    "SinksConfig": {
        "Sink": [{
            "name": "applicationInsights",
            "ApplicationInsights": "<your instrumentation key here>"
        }]
    }
    ...
}

```

## <a name="use-cloudrolename-to-separate-roles"></a>Использование свойства cloud_RoleName для разделения ролей

Свойство `cloud_RoleName` присоединяется ко всей телеметрии. Оно идентифицирует роль или службу, которая создает телеметрию. (Оно отличается от свойства cloud_RoleInstance, которое разделяет идентичные роли, которые выполняются параллельно в нескольких процессах сервера или на нескольких машинах.)

На портале вы можете фильтровать или сегментировать данные телеметрии с помощью этого свойства. В этом примере колонка "Сбои" отфильтрована для показа только информации из интерфейсной веб-службы, пропуская сбои из серверной части API CRM:

![Диаграмма метрик, сегментированная по имени облачной роли](./media/app-insights-monitor-multi-role-apps/cloud-role-name.png)

## <a name="trace-operations-between-roles"></a>Отслеживание операций между ролями

Вы можете отслеживать вызовы от одной службы к другой, сделанные во время обработки отдельной операции.


![Отображение данных телеметрии для операции](./media/app-insights-monitor-multi-role-apps/show-telemetry-for-operation.png)

Перейдите к связанному списку телеметрии для этой операции на интерфейсном веб-сервере и в API серверной части:

![Поиск по ролям](./media/app-insights-monitor-multi-role-apps/search-across-components.png)


## <a name="next-steps"></a>Дальнейшие действия

* [Отделение телеметрии стадий разработки, тестирования и эксплуатации](app-insights-separate-resources.md)


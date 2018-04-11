---
title: Поддержка нескольких компонентов, микрослужб и контейнеров в Azure Application Insights | Документы Майкрософт
description: Мониторинг приложений, которые состоят из нескольких компонентов или ролей, для получения данных производительности и использования.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 05/17/2017
ms.author: mbullwin
ms.openlocfilehash: 9b03aff140eec5b355383447f0a815220d6408e3
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/03/2018
---
# <a name="monitor-multi-component-applications-with-application-insights-preview"></a>Мониторинг приложений с несколькими компонентами с помощью Application Insights (предварительная версия)

Вы можете отслеживать приложения, которые состоят из нескольких серверных компонентов, ролей или служб, с помощью [Azure Application Insights](app-insights-overview.md). Работоспособность компонентов и связи между ними отображаются на единой схеме сопоставления приложений. Вы можете отслеживать отдельные операции для нескольких компонентов за счет автоматической корреляции HTTP. Диагностика контейнеров может быть интегрирована и сопоставлена с телеметрией приложения. Используйте единый ресурс Application Insights для всех компонентов в вашем приложении. 

![Схема сопоставления многокомпонентных приложений](./media/app-insights-monitor-multi-role-apps/app-map.png)

Под компонентом здесь подразумевается любая функциональная часть большого приложения. Например, типичное бизнес-приложение может состоять из клиентского кода, выполняемого в веб-браузерах и взаимодействующего с одной или несколькими службами веб-приложения, которые, в свою очередь, используют серверные службы. Серверные компоненты могут размещаться в локальной среде или в облаке, представлять собой веб-роли и рабочие роли Azure или выполняться в контейнерах, таких как Docker или Service Fabric. 

### <a name="sharing-a-single-application-insights-resource"></a>Совместное использование одного ресурса Application Insights 

Ключевой метод здесь — отправлять телеметрию каждого компонента в приложении в тот же ресурс Application Insights, но использовать свойство `cloud_RoleName`, чтобы различать компоненты, когда это необходимо. Пакет SDK для Application Insights добавляет свойство `cloud_RoleName` в данные телеметрии, выдаваемые компонентами. Например, пакет SDK добавит имя веб-сайта или роли службы в свойство `cloud_RoleName`. Это значение можно переопределить с помощью telemetryinitializer. В схеме сопоставления приложений свойство `cloud_RoleName` используется для идентификации компонентов в схеме.

Дополнительные сведения о том, как переопределить свойство `cloud_RoleName`, см. в разделе [Добавление свойств: ITelemetryInitializer](app-insights-api-filtering-sampling.md#add-properties-itelemetryinitializer).  

В некоторых случаях это может быть неуместным, и вы можете использовать отдельные ресурсы для разных групп компонентов. Например, вам может потребоваться использовать разные ресурсы для управления и выставления счетов. Использование отдельных ресурсов означает, что вы не видите все отображаемые компоненты на единой схеме сопоставления приложений и не можете запрашивать по компонентам в [аналитике](app-insights-analytics.md). Кроме того, отдельные ресурсы необходимо настроить.

Исходя из этого, в оставшейся части этого документа рассматривается отправка данных из нескольких компонентов в один ресурс Application Insights.

## <a name="configure-multi-component-applications"></a>Настройка многокомпонентных приложений

Чтобы получить схему сопоставления приложений с несколькими компонентами, вам потребуется выполнить следующее:

* **установить последнюю предварительную версию** пакета Application Insights в каждом компоненте приложения; 
* **использовать единый ресурс Application Insights** для всех компонентов в вашем приложении;
* **включить схему составных приложений** в колонке "Предварительный просмотр".

Настройте каждый компонент своего приложения, используя соответствующий метод для его типа ([ASP.NET](app-insights-asp-net.md), [Java](app-insights-java-get-started.md), [Node.js](app-insights-nodejs.md), [JavaScript](app-insights-javascript.md)).

### <a name="1-install-the-latest-pre-release-package"></a>1. Установка последней предварительной версии пакета

Установите пакеты Application Insights или обновите их для каждого серверного компонента в проекте. Если вы используете Visual Studio:

1. Щелкните проект правой кнопкой мыши и выберите **Управление пакетами Nuget**. 
2. Выберите параметр **Включить предварительные выпуски**.
3. Если пакеты Application Insights отображаются в списке обновлений, выберите их. 

    В противном случае найдите и установите соответствующий пакет:
    
    * Microsoft.ApplicationInsights.WindowsServer;
    * Microsoft.ApplicationInsights.ServiceFabric — для компонентов, выполняющихся в качестве гостевых исполняемых файлов, и контейнеров Docker, работающих в приложениях Service Fabric;
    * Microsoft.ApplicationInsights.ServiceFabric.Native — для надежных служб в приложениях ServiceFabric;
    * Microsoft.ApplicationInsights.Kubernetes — для компонентов, выполняемых в Docker на Kubernetes.

### <a name="2-share-a-single-application-insights-resource"></a>2. Настройка совместного использования одного ресурса Application Insights

* В Visual Studio щелкните проект правой кнопкой мыши и выберите **Настроить Application Insights** или **Application Insights > Настроить**. Для первого проекта используйте мастер для создания ресурса Application Insights, а для последующих проектов выбирайте тот же ресурс.
* Если вы не видите меню Application Insights, выполните настройку вручную:

   1. На [портале Azure](https://portal,azure.com) откройте ресурс Application Insights, который вы уже создали для другого компонента.
   2. В колонке "Обзор" щелкните раскрывающуюся вкладку "Основные компоненты" и скопируйте **ключ инструментирования**.
   3. В своем проекте откройте файл ApplicationInsights.config и вставьте: `<InstrumentationKey>your copied key</InstrumentationKey>`.

![Скопируйте ключ инструментирования в CONFIG-файл.](./media/app-insights-monitor-multi-role-apps/copy-instrumentation-key.png)


### <a name="3-enable-composite-application-map"></a>3. Включение схемы составных приложений

На портале Azure откройте ресурс для своего приложения. Под подзаголовком "Настройка" щелкните "Previews" (Предварительный просмотр), чтобы открыть колонку "Previews" (Предварительный просмотр). Включите параметр *Схема сопоставления составных приложений* в колонке "Previews" (Предварительный просмотр).

### <a name="4-enable-docker-metrics-optional"></a>4. Включение метрик Docker (необязательно) 

Если компонент выполняется в Docker, который размещен в виртуальной машине Azure с ОС Windows, вы можете собрать дополнительные метрики из контейнера. Вставьте в файл конфигурации [системы диагностики Azure](../monitoring-and-diagnostics/azure-diagnostics.md) следующий код:

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

## <a name="use-cloudrolename-to-separate-components"></a>Использование свойства cloud_RoleName для разделения компонентов

Свойство `cloud_RoleName` присоединяется ко всей телеметрии. Оно идентифицирует компонент (роль или службу), который создает телеметрию. (Оно отличается от свойства cloud_RoleInstance, которое разделяет идентичные роли, которые выполняются параллельно в нескольких процессах сервера или на нескольких машинах.)

На портале вы можете фильтровать или сегментировать данные телеметрии с помощью этого свойства. В этом примере колонка "Сбои" отфильтрована для показа только информации из интерфейсной веб-службы, пропуская сбои из серверной части API CRM:

![Диаграмма метрик, сегментированная по имени облачной роли](./media/app-insights-monitor-multi-role-apps/cloud-role-name.png)

## <a name="trace-operations-between-components"></a>Отслеживание операций между компонентами

Вы можете отслеживать вызовы от одного компонента к другому, сделанные во время обработки отдельной операции.


![Отображение данных телеметрии для операции](./media/app-insights-monitor-multi-role-apps/show-telemetry-for-operation.png)

Перейдите к связанному списку телеметрии для этой операции на интерфейсном веб-сервере и в API серверной части:

![Поиск по компонентам](./media/app-insights-monitor-multi-role-apps/search-across-components.png)


## <a name="next-steps"></a>Дополнительная информация

* [Отделение телеметрии стадий разработки, тестирования и эксплуатации](app-insights-separate-resources.md)

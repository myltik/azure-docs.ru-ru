---
title: Действия веб-перехватчика для оповещений журнала в оповещениях Azure | Документация Майкрософт
description: В этой статье описывается, как правило генерации оповещений журнала, в котором используется Log Analytics или Application Insights, передает данные в виде веб-перехватчика HTTP, и приводятся сведения о возможных вариантах настройки.
author: msvijayn
manager: kmadnani1
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: ''
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/01/2018
ms.author: vinagara
ms.openlocfilehash: 28c8e6ab6a23a46bdea31c71b08b9c6a28d1be33
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/08/2018
---
# <a name="webhook-actions-for-log-alert-rules"></a>Действия веб-перехватчика для правил оповещений журнала
При [создании оповещения в Azure](monitor-alerts-unified-usage.md) можно [настроить конфигурацию с помощью групп действий](monitoring-action-groups.md) для выполнения одного или нескольких действий.  В этой статье описываются различные доступные действия веб-перехватчика и сведения о том, как настроить пользовательский веб-перехватчик на основе JSON.


## <a name="webhook-actions"></a>Действия webhook

Действия webhook позволяют вызывать внешний процесс с помощью одного запроса HTTP POST.  Вызываемая служба должна поддерживать веб-перехватчики и определить использование получаемых полезных данных.   Примеры использования webhook в ответ на оповещение включают в себя отправку сообщения в [Slack](http://slack.com) или создание инцидента в [PagerDuty](http://pagerduty.com/).  

Для выполнения действий веб-перехватчика требуются свойства, приведенные в следующей таблице:

| Свойство | ОПИСАНИЕ |
|:--- |:--- |
| URL-адрес Webhook |URL-адрес действия webhook. |
| Настраиваемые полезные данные JSON |Настраиваемые полезные данные для отправки с помощью веб-перехватчика, если этот параметр выбран при создании оповещения. Подробные сведения см. в статье об [управлении оповещений с помощью оповещений Azure](monitor-alerts-unified-usage.md). |

> [!NOTE]
> Кнопка "Проверить веб-перехватчик", наряду с параметром *Включить настраиваемые полезные данные JSON для веб-перехватчика* журнала оповещений, запускает фиктивный вызов для проверки URL-адреса веб-перехватчика. Он не содержит фактические данные и представитель схемы JSON, используемой для журнала оповещений. 

Действия webhook включают URL-адрес и полезные данные в формате JSON, которые являются данными, отправляемыми во внешнюю службу.  По умолчанию в полезные данные включаются значения из следующей таблицы. Их можно заменить другими полезными данными по собственному усмотрению.  В этом случае можно использовать переменные из таблицы для каждого параметра, чтобы включить их значение в пользовательские полезные данные.


| Параметр | Переменная | ОПИСАНИЕ |
|:--- |:--- |:--- |
| AlertRuleName |#alertrulename |Имя правила генерации оповещений. |
| Уровень серьезности |#severity |Уровень серьезности, установленный для срабатывающего оповещения журнала. |
| AlertThresholdOperator |#thresholdoperator |Оператор порога для правила генерации оповещений.  *Больше* или *Меньше*. |
| AlertThresholdValue |#thresholdvalue |Значение порога для правила генерации оповещений. |
| LinkToSearchResults |#linktosearchresults |Ссылки на поиск журналов Log Analytics, возвращающая записи из запроса, создавшего оповещение. |
| ResultCount |#searchresultcount |Число записей в результатах поиска. |
| Время окончания интервала поиска |#searchintervalendtimeutc |Время окончания для запроса в формате UTC. |
| Интервал поиска |#searchinterval |Временное окно для правила генерации оповещений. |
| Время начала интервала поиска |#searchintervalstarttimeutc |Время начала для запроса в формате UTC. 
| SearchQuery |#searchquery |Запрос поиска журналов, используемый правилом генерации оповещений. |
| SearchResults |"IncludeSearchResults": true|Записи, возвращаемые запросом в виде JSON-таблицы, ограничены первой 1000 записей, если в определение настраиваемого веб-перехватчика JSON добавлен параметр "IncludeSearchResults":true в качестве свойства верхнего уровня. |
| WorkspaceID |#workspaceid |Идентификатор рабочей области Log Analytics. |
| Идентификатор приложения |#applicationid |Идентификатор вашего приложения Application Insight. |
| Идентификатор подписки |#subscriptionid |Идентификатор подписки Azure, используемой с Application Insights. 


Например, можно указать следующие пользовательские полезные данные, содержащие один параметр — *text*.  Служба, вызываемая этим действием webhook, будет ожидать этот параметр.

```json

    {
        "text":"#alertrulename fired with #searchresultcount over threshold of #thresholdvalue."
    }
```
При отправке в webhook этот пример полезных данных разрешается в нечто следующее:

```json
    {
        "text":"My Alert Rule fired with 18 records over threshold of 10 ."
    }
```

Чтобы включить результаты поиска в пользовательские полезные данные, добавьте **IncudeSearchResults** как свойство верхнего уровня в полезные данные JSON. 

## <a name="sample-payloads"></a>Примеры полезных данных
В этом разделе показан пример полезных данных для веб-перехватчика оповещений журнала, включая случаи стандартных и настраиваемых полезных данных.

> [!NOTE]
> Для обеспечения обратной совместимости стандартные полезные данные веб-перехватчика для оповещений с помощью Azure Log Analytics выполняют ту же роль, что и [управление оповещениями Log Analytics](../log-analytics/log-analytics-alerts-creating.md). Но для оповещений журнала с использованием [Application Insights](../application-insights/app-insights-analytics.md) стандартные полезные данные веб-перехватчика основаны на схеме группы действий.

### <a name="standard-webhook-for-log-alerts"></a>Стандартный веб-перехватчик для оповещений журнала 
В обоих этих примерах заданы фиктивные полезные данные, которые содержат только два столбца и две строки.

#### <a name="log-alert-for-azure-log-analytics"></a>Оповещение журнала для Azure Log-Analytics
Ниже приведен пример полезных данных для стандартного действия веб-перехватчика *без настраиваемого параметра JSON* при использовании в оповещениях на основе Log Analytics.

```json
{
    "WorkspaceId":"12345a-1234b-123c-123d-12345678e",
    "AlertRuleName":"AcmeRule","SearchQuery":"search *",
    "SearchResult":
        {
        "tables":[
                    {"name":"PrimaryResult","columns":
                        [
                        {"name":"$table","type":"string"},
                        {"name":"Id","type":"string"},
                        {"name":"TimeGenerated","type":"datetime"}
                        ],
                    "rows":
                        [
                            ["Fabrikam","33446677a","2018-02-02T15:03:12.18Z"],
                            ["Contoso","33445566b","2018-02-02T15:16:53.932Z"]
                        ]
                    }
                ]
        },
    "SearchIntervalStartTimeUtc": "2018-03-26T08:10:40Z",
    "SearchIntervalEndtimeUtc": "2018-03-26T09:10:40Z",
    "AlertThresholdOperator": "Greater Than",
    "AlertThresholdValue": 0,
    "ResultCount": 2,
    "SearchIntervalInSeconds": 3600,
    "LinkToSearchResults": "https://workspaceID.portal.mms.microsoft.com/#Workspace/search/index?_timeInterval.intervalEnd=2018-03-26T09%3a10%3a40.0000000Z&_timeInterval.intervalDuration=3600&q=Usage",
    "Description": null,
    "Severity": "Warning"
 }
 ```   

#### <a name="log-alert-for-azure-application-insights"></a>Оповещение журнала для Azure Application Insights
Ниже приведен пример полезных данных для стандартного веб-перехватчика *без настраиваемого параметра JSON* при использовании в оповещениях журнала на основе Application Insights.
    
```json
{
    "schemaId":"Microsoft.Insights/LogAlert","data":
    { 
    "SubscriptionId":"12345a-1234b-123c-123d-12345678e",
    "AlertRuleName":"AcmeRule","SearchQuery":"search *",
    "SearchResult":
        {
        "tables":[
                    {"name":"PrimaryResult","columns":
                        [
                        {"name":"$table","type":"string"},
                        {"name":"Id","type":"string"},
                        {"name":"TimeGenerated","type":"datetime"}
                        ],
                    "rows":
                        [
                            ["Fabrikam","33446677a","2018-02-02T15:03:12.18Z"],
                            ["Contoso","33445566b","2018-02-02T15:16:53.932Z"]
                        ]
                    }
                ]
        },
    "SearchIntervalStartTimeUtc": "2018-03-26T08:10:40Z",
    "SearchIntervalEndtimeUtc": "2018-03-26T09:10:40Z",
    "AlertThresholdOperator": "Greater Than",
    "AlertThresholdValue": 0,
    "ResultCount": 2,
    "SearchIntervalInSeconds": 3600,
    "LinkToSearchResults": "https://analytics.applicationinsights.io/subscriptions/12345a-1234b-123c-123d-12345678e/?query=search+*+&timeInterval.intervalEnd=2018-03-26T09%3a10%3a40.0000000Z&_timeInterval.intervalDuration=3600&q=Usage",
    "Description": null,
    "Severity": "Error",
    "ApplicationId": "123123f0-01d3-12ab-123f-abc1ab01c0a1"
    }
}
```

#### <a name="log-alert-with-custom-json-payload"></a>Оповещение журнала с настраиваемыми полезными данными JSON
Например, чтобы создать пользовательские полезные данные, включающие только имя оповещения и результаты поиска, выполните этот код: 

```json
    {
       "alertname":"#alertrulename",
       "IncludeSearchResults":true
    }
```

Ниже приведен пример полезных данных для настраиваемого действия веб-перехватчика для любого оповещения журнала.
    
```json
    {
    "alertname":"AcmeRule","IncludeSearchResults":true,
    "SearchResult":
        {
        "tables":[
                    {"name":"PrimaryResult","columns":
                        [
                        {"name":"$table","type":"string"},
                        {"name":"Id","type":"string"},
                        {"name":"TimeGenerated","type":"datetime"}
                        ],
                    "rows":
                        [
                            ["Fabrikam","33446677a","2018-02-02T15:03:12.18Z"],
                            ["Contoso","33445566b","2018-02-02T15:16:53.932Z"]
                        ]
                    }
                ]
        }
    }
```


## <a name="next-steps"></a>Дополнительная информация
- Узнайте больше об [оповещениях журналов в службе оповещений Azure](monitor-alerts-unified-log.md).
- Создание [групп действий в Azure](monitoring-action-groups.md) и управление ими
- Дополнительные сведения об [Application Insights](../application-insights/app-insights-analytics.md)
- Дополнительные сведения о [Log Analytics](../log-analytics/log-analytics-overview.md). 
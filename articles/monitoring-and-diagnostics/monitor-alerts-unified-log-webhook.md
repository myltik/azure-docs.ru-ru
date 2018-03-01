---
title: "Действия веб-перехватчика для оповещений журнала в оповещениях Azure (предварительная версия) | Документация Майкрософт"
description: "В этой статье описывается, как правило оповещения журнала, в котором используется Log Analytics или Application Insights, передает данные в виде веб-перехватчика HTTP, и приводятся сведения о возможных вариантах настройки."
author: msvijayn
manager: kmadnani1
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 49905638-f9f2-427b-8489-a0bcc7d8b9fe
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/05/2018
ms.author: vinagara
ms.openlocfilehash: 4af1bb61888810011ce64fde7931cabfefe76ab6
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/09/2018
---
# <a name="webhook-actions-for-log-alert-rules"></a>Действия веб-перехватчика для правил оповещений журнала
При [создании оповещения в Azure (предварительная версия)](monitor-alerts-unified-usage.md) можно [настроить конфигурацию с помощью групп действий](monitoring-action-groups.md) для выполнения одного или нескольких действий.  В этой статье описываются различные доступные действия веб-перехватчика и сведения о том, как настроить пользовательский веб-перехватчик на основе JSON.


## <a name="webhook-actions"></a>Действия webhook

Действия webhook позволяют вызывать внешний процесс с помощью одного запроса HTTP POST.  Вызываемая служба должна поддерживать действия webhook и определить использование получаемых полезных данных.  Можно также вызвать REST API без поддержки действий webhook, если запрос имеет формат, понятный этому API.  Примеры использования webhook в ответ на оповещение включают в себя отправку сообщения в [Slack](http://slack.com) или создание инцидента в [PagerDuty](http://pagerduty.com/).  Полное пошаговое руководство по созданию правила генерации оповещений с webhook для вызова Slack см. в статье [Действия webhook в оповещениях Log Analytics](../log-analytics/log-analytics-alerts-webhooks.md).

Для выполнения действий webhook требуются свойства, приведенные в таблице ниже.

| Свойство | ОПИСАНИЕ |
|:--- |:--- |
| URL-адрес Webhook |URL-адрес действия webhook. |
| Настраиваемые полезные данные JSON |Настраиваемые полезные данные для отправки с webhook.  Если параметр выбран во время создания оповещения. Подробные сведения см. в статье [Создание и просмотр оповещений, а также и управление ими с помощью Azure Monitor ("Оповещения (предварительная версия)")](monitor-alerts-unified-usage.md). |

> [!NOTE]
> Кнопка "Проверить веб-перехватчик", наряду с параметром *Включить настраиваемые полезные данные JSON для веб-перехватчика* журнала оповещений, запускает фиктивный вызов для проверки URL-адреса веб-перехватчика. Он не содержит фактических данных или представителя схемы JSON, используемой для журнала оповещений. В то время как можно протестировать любой веб-перехватчик с настраиваемым JSON-представителем, все веб-перехватчики, настроенные в группе действий, будут отправлены с настраиваемыми полезными данными JSON.

Действия webhook включают URL-адрес и полезные данные в формате JSON, которые являются данными, отправляемыми во внешнюю службу.  По умолчанию полезные данные включают в себя значения из приведенной ниже таблицы.  Такие полезные данные можно заменить на собственные.  В этом случае можно использовать переменные из таблицы для каждого параметра, чтобы включить их значение в пользовательские полезные данные.


| Параметр | Переменная | ОПИСАНИЕ |
|:--- |:--- |:--- |
| AlertRuleName |#alertrulename |Имя правила генерации оповещений. |
| AlertThresholdOperator |#thresholdoperator |Оператор порога для правила генерации оповещений.  *Больше* или *Меньше*. |
| AlertThresholdValue |#thresholdvalue |Значение порога для правила генерации оповещений. |
| LinkToSearchResults |#linktosearchresults |Ссылки на поиск журналов Log Analytics, возвращающая записи из запроса, создавшего оповещение. |
| ResultCount |#searchresultcount |Число записей в результатах поиска. |
| SearchIntervalEndtimeUtc |#searchintervalendtimeutc |Время окончания для запроса в формате UTC. |
| SearchIntervalInSeconds |#searchinterval |Временное окно для правила генерации оповещений. |
| SearchIntervalStartTimeUtc |#searchintervalstarttimeutc |Время начала для запроса в формате UTC. |
| SearchQuery |#searchquery |Запрос поиска журналов, используемый правилом генерации оповещений. |
| SearchResults |"IncludeSearchResults":true|Записи, возвращаемые запросом в виде JSON-таблицы, ограничены первой 1000 записей или строк, если в определение настраиваемого веб-перехватчика JSON добавлен параметр "IncludeSearchResults":true в качестве свойства верхнего уровня |
| WorkspaceID |#workspaceid |Идентификатор рабочей области Log Analytics. |
| Уровень серьезности |#severity |Уровень серьезности, установленный для срабатывающего оповещения журнала. |

Например, можно указать следующие пользовательские полезные данные, содержащие один параметр — *text*.  Служба, вызываемая этим действием webhook, будет ожидать этот параметр.

    {
        "text":"#alertrulename fired with #searchresultcount over threshold of #thresholdvalue."
    }

При отправке в webhook этот пример полезных данных разрешается в нечто следующее:

    {
        "text":"My Alert Rule fired with 18 records over threshold of 10 ."
    }

Чтобы включить результаты поиска в пользовательские полезные данные, добавьте **IncudeSearchResults** как свойство верхнего уровня в полезные данные JSON.


С полным примером можно ознакомиться в статье о [создании действия веб-перехватчика оповещений в Log Analytics для отправки сообщения в Slack](../log-analytics/log-analytics-alerts-webhooks.md).

## <a name="sample-payload"></a>Пример полезных данных
В этом разделе показан пример полезных данных для веб-перехватчика оповещений журнала, включая случаи стандартных и настраиваемых полезных данных.

> [!NOTE]
> Для обеспечения обратной совместимости стандартные полезные данные веб-перехватчика для оповещений с помощью Azure Log Analytics работают аналогично [управлению оповещениями OMS](../log-analytics/log-analytics-solution-alert-management.md). Однако для оповещений журнала с использованием [Application Insights](../application-insights/app-insights-analytics.md) стандартные полезные данные веб-перехватчика основаны на схеме группы действий.

### <a name="standard-webhook-for-log-alerts"></a>Стандартный веб-перехватчик для оповещений журнала
В обоих этих примерах мы задали фиктивные полезные данные, в которых только два столбца и две строки.

#### <a name="log-alert-for-azure-log-analytics"></a>Оповещение журнала для Azure Log-Analytics
Ниже приведен пример полезных данных для стандартного действия веб-перехватчика без настраиваемых данных JSON при использовании в оповещениях журнала на основе Log Analytics.

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
                        {"name":"TimeGenerated","type":"datetime"},
                        ],
                    "rows":
                        [
                            ["Fabrikam","33446677a","2018-02-02T15:03:12.18Z"],
                            ["Contoso","33445566b","2018-02-02T15:16:53.932Z"]
                        ]
                    }
                ]
        }
    "SearchIntervalStartTimeUtc": "2018-03-26T08:10:40Z",
    "SearchIntervalEndtimeUtc": "2018-03-26T09:10:40Z",
    "AlertThresholdOperator": "Greater Than",
    "AlertThresholdValue": 0,
    "ResultCount": 2,
    "SearchIntervalInSeconds": 3600,
    "LinkToSearchResults": "https://workspaceID.portal.mms.microsoft.com/#Workspace/search/index?_timeInterval.intervalEnd=2018-03-26T09%3a10%3a40.0000000Z&_timeInterval.intervalDuration=3600&q=Usage",
    "Description": null,
    "Severity": "Low"
    }



#### <a name="log-alert-for-azure-application-insights"></a>Оповещение журнала для Azure Application Insights
Ниже приведен пример полезных данных для стандартного действия веб-перехватчика без настраиваемых данных JSON при использовании в оповещениях журнала на основе Application Insights.


    {
    "schemaId":"LogAlert","data":
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
                        {"name":"TimeGenerated","type":"datetime"},
                        ],
                    "rows":
                        [
                            ["Fabrikam","33446677a","2018-02-02T15:03:12.18Z"],
                            ["Contoso","33445566b","2018-02-02T15:16:53.932Z"]
                        ]
                    }
                ]
        }
    "SearchIntervalStartTimeUtc": "2018-03-26T08:10:40Z",
    "SearchIntervalEndtimeUtc": "2018-03-26T09:10:40Z",
    "AlertThresholdOperator": "Greater Than",
    "AlertThresholdValue": 0,
    "ResultCount": 2,
    "SearchIntervalInSeconds": 3600,
    "LinkToSearchResults": "https://workspaceID.portal.mms.microsoft.com/#Workspace/search/index?_timeInterval.intervalEnd=2018-03-26T09%3a10%3a40.0000000Z&_timeInterval.intervalDuration=3600&q=Usage",
    "Description": null,
    "Severity": "Low"
    }
    }


#### <a name="log-alert-with-custom-json-payload"></a>Оповещение журнала с настраиваемыми полезными данными JSON
Например, чтобы создать пользовательские полезные данные, включающие только имя оповещения и результаты поиска, можно использовать следующее:

    {
       "alertname":"#alertrulename",
       "IncludeSearchResults":true
    }

Ниже приведен пример полезных данных для настраиваемого действия веб-перехватчика для любого оповещения журнала.


    {
    "AlertRuleName":"AcmeRule","IncludeSearchResults":true,
    "SearchResult":
        {
        "tables":[
                    {"name":"PrimaryResult","columns":
                        [
                        {"name":"$table","type":"string"},
                        {"name":"Id","type":"string"},
                        {"name":"TimeGenerated","type":"datetime"},
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




## <a name="next-steps"></a>Дополнительная информация
- Создание [групп действий в Azure](monitoring-action-groups.md) и управление ими
- Подробнее об [оповещениях журналов в интерфейсе Azure "Оповещения (предварительная версия)"](monitor-alerts-unified-log.md)

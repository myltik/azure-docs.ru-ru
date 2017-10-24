---
title: "Ответ на оповещение в OMS Log Analytics | Документация Майкрософт"
description: "Оповещения в Log Analytics идентифицируют важную информацию в репозитории OMS и могут заранее уведомлять вас о возникших проблемах или выполнять действия в попытке устранить их.  Эта статья описывает создание правила генерации оповещений и сведения о различных действиях, которые оно может выполнить."
services: log-analytics
documentationcenter: 
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: 6cfd2a46-b6a2-4f79-a67b-08ce488f9a91
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/06/2017
ms.author: bwren
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: d6d65480c53f905b393409dfdd9952618ab6cb64
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="add-actions-to-alert-rules-in-log-analytics"></a>Добавление действий в правила оповещений в Log Analytics
При [создании оповещения в Log Analytics](log-analytics-alerts.md) можно [настроить правила оповещений](log-analytics-alerts.md) для выполнения одного или нескольких действий.  В этой статье описываются различные доступные действия и сведения о том, как их настроить.

| Действие | Описание |
|:--|:--|
| [Электронная почта](#email-actions) | Отправка сообщения электронной почты с описанием оповещения одному или нескольким получателям. |
| [webhook](#webhook-actions) | Вызов внешнего процесса с помощью одного запроса HTTP POST. |
| [Runbook](#runbook-actions) | Запуск модуля Runbook в службе автоматизации Azure. |


## <a name="email-actions"></a>Действия электронной почты
Действия электронной почты отправляют сообщение электронной почты с описанием оповещения одному или несколькими получателям.  Вы можете указать тему сообщения, но его содержимое имеет стандартный формат, определяемый службой Log Analytics.  Оно содержит сводные данные, такие как имя оповещения, а также описание до 10 записей, возвращенных в результате поиска журналов.  Он также включает ссылку на поиск журналов в Log Analytics, которая возвращает полный набор записей на основе этого запроса.   Отправителем сообщения является *группа разработчиков Microsoft Operations Management Suite&lt;noreply@oms.microsoft.com&gt;*. 

Для выполнения действий электронной почты требуются свойства, приведенные в таблице ниже.

| Свойство | Описание |
|:--- |:--- |
| Субъект |Тема сообщения электронной почты.  Основной текст сообщения нельзя изменить. |
| Recipients |Адреса всех получателей электронной почты.  При указании нескольких адресов разделяйте их точкой с запятой (;). |


## <a name="webhook-actions"></a>Действия webhook

Действия webhook позволяют вызывать внешний процесс с помощью одного запроса HTTP POST.  Вызываемая служба должна поддерживать действия webhook и определить использование получаемых полезных данных.  Можно также вызвать REST API без поддержки действий webhook, если запрос имеет формат, понятный этому API.  Примеры использования webhook в ответ на оповещение включают в себя отправку сообщения в [Slack](http://slack.com) или создание инцидента в [PagerDuty](http://pagerduty.com/).  Полное пошаговое руководство по созданию правила генерации оповещений с webhook для вызова Slack см. в статье [Действия webhook в оповещениях Log Analytics](log-analytics-alerts-webhooks.md).

Для выполнения действий webhook требуются свойства, приведенные в таблице ниже.

| Свойство | Описание |
|:--- |:--- |
| URL-адрес Webhook |URL-адрес действия webhook. |
| Настраиваемые полезные данные JSON |Настраиваемые полезные данные для отправки с webhook.  Дополнительные сведения см. ниже. |


Действия webhook включают URL-адрес и полезные данные в формате JSON, которые являются данными, отправляемыми во внешнюю службу.  По умолчанию полезные данные включают в себя значения из приведенной ниже таблицы.  Такие полезные данные можно заменить на собственные.  В этом случае можно использовать переменные из таблицы для каждого параметра, чтобы включить их значение в пользовательские полезные данные.

>[!NOTE]
> Если ваша рабочая область переведена на [новый язык запросов Log Analytics](log-analytics-log-search-upgrade.md), полезные данные веб-перехватчика будут работать по-другому.  Подробные сведения о формате см. в документации [API REST Log Analytics Azure](https://aka.ms/loganalyticsapiresponse).  Пример см. в разделе [Пример полезной нагрузки](#sample-payload) ниже.

| Параметр | Переменная | Описание |
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
| SearchResults |См. ниже |Записи, возвращенные запросом в формате JSON.  Только первые 5000 записей. |
| WorkspaceID |#workspaceid |Идентификатор рабочей области OMS |

Например, можно указать следующие пользовательские полезные данные, содержащие один параметр — *text*.  Служба, вызываемая этим действием webhook, будет ожидать этот параметр.

    {
        "text":"#alertrulename fired with #searchresultcount over threshold of #thresholdvalue."
    }

При отправке в webhook этот пример полезных данных разрешается в нечто следующее:

    {
        "text":"My Alert Rule fired with 18 records over threshold of 10 ."
    }

Чтобы включить результаты поиска в пользовательские полезные данные, добавьте следующую строку как свойство верхнего уровня в полезных данных JSON.  

    "IncludeSearchResults":true

Например, чтобы создать пользовательские полезные данные, включающие только имя оповещения и результаты поиска, можно использовать следующее: 

    {
       "alertname":"#alertrulename",
       "IncludeSearchResults":true
    }


С полным примером создания правила генерации оповещений с помощью webhook для запуска внешней службы можно ознакомиться в статье [Действия webhook в оповещениях Log Analytics](log-analytics-alerts-webhooks.md).


## <a name="runbook-actions"></a>Действия Runbook
Действия Runbook запускают модуль Runbook в службе автоматизации Azure.  Чтобы использовать этот тип действия, необходимо иметь установленное и настроенное [решение автоматизации](log-analytics-add-solutions.md) в рабочей области OMS.  Можно выбрать один из модулей Runbook в учетной записи автоматизации, настроенной в решении автоматизации.

Для выполнения действий Runbook требуются свойства, приведенные в таблице ниже.

| Свойство | Описание |
|:--- |:---|
| Модуль Runbook | Модуль Runbook, который требуется запустить при создании оповещения. |
| Запуск на | Выберите **Azure** для запуска Runbook в облаке Azure.  Выберите **Гибридная рабочая роль** для запуска Runbook в агенте с установленной [гибридной рабочей ролью Runbook](../automation/automation-hybrid-runbook-worker.md ).  |

Действия Runbook запускают модуль Runbook с помощью [webhook](../automation/automation-webhooks.md).  При создании правила генерации оповещений автоматически создается новое действие webhook для модуля Runbook с именем **OMS Alert Remediation** , за которым следует GUID.  

Нельзя напрямую заполнять параметры Runbook. Однако в [параметре $WebhookData](../automation/automation-webhooks.md) будут содержаться подробные сведения о предупреждении, включая результаты поиска по журналам,из-за которого оно возникло.  Для доступа к свойствам предупреждения модулю Runbook потребуется определить **$WebhookData** в качестве параметра.  Данные предупреждения доступны в формате Json в свойстве **SearchResults** свойства **RequestBody** параметра **$WebhookData**.  Свойства этого параметра приведены в таблице ниже.

>[!NOTE]
> Если ваша рабочая область переведена на [новый язык запросов Log Analytics](log-analytics-log-search-upgrade.md), полезные данные runbook будут работать по-другому.  Подробные сведения о формате см. в документации [API REST Log Analytics Azure](https://aka.ms/loganalyticsapiresponse).  Пример см. в разделе [Пример полезной нагрузки](#sample-payload) ниже.

| Узел | Описание |
|:--- |:--- |
| id |Путь и GUID для поиска. |
| __metadata |Сведения об оповещении, включая количество записей и состояние результатов поиска. |
| value |Отдельная запись для каждой записи в результатах поиска.  Подробные сведения о записи будут соответствовать свойствам и значениям записи. |

Например, следующий модуль Runbook извлечет записи, возвращенные в результате поиска по журналам, и назначит разные свойства на основе типа каждой записи.  Обратите внимание, что сначала модуль Runbook преобразовывает формат **RequestBody** из Json, чтобы с этим параметром можно было работать в качестве объекта в PowerShell.

    param ( 
        [object]$WebhookData
    )

    $RequestBody = ConvertFrom-JSON -InputObject $WebhookData.RequestBody
    $Records     = $RequestBody.SearchResults.value

    foreach ($Record in $Records)
    {
        $Computer = $Record.Computer

        if ($Record.Type -eq 'Event')
        {
            $EventNo    = $Record.EventID
            $EventLevel = $Record.EventLevelName
            $EventData  = $Record.EventData
        }

        if ($Record.Type -eq 'Perf')
        {
            $Object    = $Record.ObjectName
            $Counter   = $Record.CounterName
            $Instance  = $Record.InstanceName
            $Value     = $Record.CounterValue
        }
    }


## <a name="sample-payload"></a>Пример полезных данных
В этом разделе показан пример полезных данных для действий веб-перехватчика и runbook в предыдущей и [обновленной рабочей области Log Analytics](log-analytics-log-search-upgrade.md).

### <a name="webhook-actions"></a>Действия webhook

#### <a name="legacy-workspace"></a>Рабочая область прежней версии.
Ниже приведен пример полезных данных для действия веб-перехватчика в рабочей области прежней версии.

    {
    "WorkspaceId": "workspaceID",
    "AlertRuleName": "WebhookAlert",
    "SearchQuery": "Type=Usage",
    "SearchResult": {
        "id": "subscriptions/subscriptionID/resourceGroups/ResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/workspace-workspaceID/search/SearchGUID|10.1.0.7|2017-09-27T10-30-38Z",
        "__metadata": {
        "resultType": "raw",
        "total": 1,
        "top": 2147483647,
        "RequestId": "SearchID|10.1.0.7|2017-09-27T10-30-38Z",
        "CoreSummaries": [
            {
            "Status": "Successful",
            "NumberOfDocuments": 135000000
            }
        ],
        "Status": "Successful",
        "NumberOfDocuments": 135000000,
        "StartTime": "2017-09-27T10:30:38.9453282Z",
        "LastUpdated": "2017-09-27T10:30:44.0907473Z",
        "ETag": "636421050440907473",
        "sort": [
            {
            "name": "TimeGenerated",
            "order": "desc"
            }
        ],
        "requestTime": 361
        },
        "value": [
        {
            "Computer": "-",
            "SourceSystem": "OMS",
            "TimeGenerated": "2017-09-26T13:59:59Z",
            "ResourceUri": "/subscriptions/df1ec963-d784-4d11-a779-1b3eeb9ecb78/resourcegroups/mms-eus/providers/microsoft.operationalinsights/workspaces/workspace-861bd466-5400-44be-9552-5ba40823c3aa",
            "DataType": "Operation",
            "StartTime": "2017-09-26T13:00:00Z",
            "EndTime": "2017-09-26T13:59:59Z",
            "Solution": "LogManagement",
            "BatchesWithinSla": 8,
            "BatchesOutsideSla": 0,
            "BatchesCapped": 0,
            "TotalBatches": 8,
            "AvgLatencyInSeconds": 0.0,
            "Quantity": 0.002502,
            "QuantityUnit": "MBytes",
            "IsBillable": false,
            "MeterId": "a4e29a95-5b4c-408b-80e3-113f9410566e",
            "LinkedMeterId": "00000000-0000-0000-0000-000000000000",
            "id": "954f7083-cd55-3f0a-72cb-3d78cd6444a3",
            "Type": "Usage",
            "MG": "00000000-0000-0000-0000-000000000000",
            "__metadata": {
            "Type": "Usage",
            "TimeGenerated": "2017-09-26T13:59:59Z"
            }
        }
        ]
    },
    "SearchIntervalStartTimeUtc": "2017-09-26T08:10:40Z",
    "SearchIntervalEndtimeUtc": "2017-09-26T09:10:40Z",
    "AlertThresholdOperator": "Greater Than",
    "AlertThresholdValue": 0,
    "ResultCount": 1,
    "SearchIntervalInSeconds": 3600,
    "LinkToSearchResults": "https://workspaceID.portal.mms.microsoft.com/#Workspace/search/index?_timeInterval.intervalEnd=2017-09-26T09%3a10%3a40.0000000Z&_timeInterval.intervalDuration=3600&q=Type%3DUsage",
    "Description": null,
    "Severity": "Low"
    }


#### <a name="upgraded-workspace"></a>Обновленная рабочая область.
Ниже приведен пример полезных данных для действия веб-перехватчика в обновленной рабочей области.

    {
    "WorkspaceId": "workspaceID",
    "AlertRuleName": "WebhookAlert",
    "SearchQuery": "Usage",
    "SearchResult": {
        "tables": [
        {
            "name": "PrimaryResult",
            "columns": [
            {
                "name": "TenantId",
                "type": "string"
            },
            {
                "name": "Computer",
                "type": "string"
            },
            {
                "name": "TimeGenerated",
                "type": "datetime"
            },
            {
                "name": "SourceSystem",
                "type": "string"
            },
            {
                "name": "StartTime",
                "type": "datetime"
            },
            {
                "name": "EndTime",
                "type": "datetime"
            },
            {
                "name": "ResourceUri",
                "type": "string"
            },
            {
                "name": "LinkedResourceUri",
                "type": "string"
            },
            {
                "name": "DataType",
                "type": "string"
            },
            {
                "name": "Solution",
                "type": "string"
            },
            {
                "name": "BatchesWithinSla",
                "type": "long"
            },
            {
                "name": "BatchesOutsideSla",
                "type": "long"
            },
            {
                "name": "BatchesCapped",
                "type": "long"
            },
            {
                "name": "TotalBatches",
                "type": "long"
            },
            {
                "name": "AvgLatencyInSeconds",
                "type": "real"
            },
            {
                "name": "Quantity",
                "type": "real"
            },
            {
                "name": "QuantityUnit",
                "type": "string"
            },
            {
                "name": "IsBillable",
                "type": "bool"
            },
            {
                "name": "MeterId",
                "type": "string"
            },
            {
                "name": "LinkedMeterId",
                "type": "string"
            },
            {
                "name": "Type",
                "type": "string"
            }
            ],
            "rows": [
            [
                "workspaceID",
                "-",
                "2017-09-26T13:59:59Z",
                "OMS",
                "2017-09-26T13:00:00Z",
                "2017-09-26T13:59:59Z",
                "/subscriptions/SubscriptionID/resourcegroups/ResourceGroupName/providers/microsoft.operationalinsights/workspaces/workspace-workspaceID",
                null,
                "Operation",
                "LogManagement",
                8,
                0,
                0,
                8,
                0,
                0.002502,
                "MBytes",
                false,
                "a4e29a95-5b4c-408b-80e3-113f9410566e",
                "00000000-0000-0000-0000-000000000000",
                "Usage"
            ]
            ]
        }
        ]
    },
    "SearchIntervalStartTimeUtc": "2017-09-26T08:10:40Z",
    "SearchIntervalEndtimeUtc": "2017-09-26T09:10:40Z",
    "AlertThresholdOperator": "Greater Than",
    "AlertThresholdValue": 0,
    "ResultCount": 1,
    "SearchIntervalInSeconds": 3600,
    "LinkToSearchResults": "https://workspaceID.portal.mms.microsoft.com/#Workspace/search/index?_timeInterval.intervalEnd=2017-09-26T09%3a10%3a40.0000000Z&_timeInterval.intervalDuration=3600&q=Usage",
    "Description": null,
    "Severity": "Low"
    }


### <a name="runbooks"></a>Модули Runbook

#### <a name="legacy-workspace"></a>Рабочая область прежней версии.
Ниже приведен пример полезных данных для действия runbook в рабочей области прежней версии.

    {
        "SearchResults": {
            "id": "subscriptions/subscriptionID/resourceGroups/ResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/workspace-workspaceID/search/searchGUID|10.1.0.7|TimeStamp",
            "__metadata": {
                "resultType": "raw",
                "total": 1,
                "top": 2147483647,
                "RequestId": "searchGUID|10.1.0.7|2017-09-27T10-51-43Z",
                "CoreSummaries": [{
                    "Status": "Successful",
                    "NumberOfDocuments": 135000000
                }],
                "Status": "Successful",
                "NumberOfDocuments": 135000000,
                "StartTime": "2017-09-27T10:51:43.3075124Z",
                "LastUpdated": "2017-09-27T10:51:51.1002092Z",
                "ETag": "636421063111002092",
                "sort": [{
                    "name": "TimeGenerated",
                    "order": "desc"
                }],
                "requestTime": 511
            },
            "value": [{
                "Computer": "-",
                "SourceSystem": "OMS",
                "TimeGenerated": "2017-09-26T13:59:59Z",
                "ResourceUri": "/subscriptions/AnotherSubscriptionID/resourcegroups/SampleResourceGroup/providers/microsoft.operationalinsights/workspaces/workspace-workspaceID",
                "DataType": "Operation",
                "StartTime": "2017-09-26T13:00:00Z",
                "EndTime": "2017-09-26T13:59:59Z",
                "Solution": "LogManagement",
                "BatchesWithinSla": 8,
                "BatchesOutsideSla": 0,
                "BatchesCapped": 0,
                "TotalBatches": 8,
                "AvgLatencyInSeconds": 0.0,
                "Quantity": 0.002502,
                "QuantityUnit": "MBytes",
                "IsBillable": false,
                "MeterId": "a4e29a95-5b4c-408b-80e3-113f9410566e",
                "LinkedMeterId": "00000000-0000-0000-0000-000000000000",
                "id": "954f7083-cd55-3f0a-72cb-3d78cd6444a3",
                "Type": "Usage",
                "MG": "00000000-0000-0000-0000-000000000000",
                "__metadata": {
                    "Type": "Usage",
                    "TimeGenerated": "2017-09-26T13:59:59Z"
                }
            }]
        }
    }

#### <a name="upgraded-workspace"></a>Обновленная рабочая область.
Ниже приведен пример полезных данных для действия runbook в обновленной рабочей области.

    {
    "WorkspaceId": "workspaceID",
    "AlertRuleName": "AutomationAlert",
    "SearchQuery": "Usage",
    "SearchResult": {
        "tables": [
        {
            "name": "PrimaryResult",
            "columns": [
            {
                "name": "TenantId",
                "type": "string"
            },
            {
                "name": "Computer",
                "type": "string"
            },
            {
                "name": "TimeGenerated",
                "type": "datetime"
            },
            {
                "name": "SourceSystem",
                "type": "string"
            },
            {
                "name": "StartTime",
                "type": "datetime"
            },
            {
                "name": "EndTime",
                "type": "datetime"
            },
            {
                "name": "ResourceUri",
                "type": "string"
            },
            {
                "name": "LinkedResourceUri",
                "type": "string"
            },
            {
                "name": "DataType",
                "type": "string"
            },
            {
                "name": "Solution",
                "type": "string"
            },
            {
                "name": "BatchesWithinSla",
                "type": "long"
            },
            {
                "name": "BatchesOutsideSla",
                "type": "long"
            },
            {
                "name": "BatchesCapped",
                "type": "long"
            },
            {
                "name": "TotalBatches",
                "type": "long"
            },
            {
                "name": "AvgLatencyInSeconds",
                "type": "real"
            },
            {
                "name": "Quantity",
                "type": "real"
            },
            {
                "name": "QuantityUnit",
                "type": "string"
            },
            {
                "name": "IsBillable",
                "type": "bool"
            },
            {
                "name": "MeterId",
                "type": "string"
            },
            {
                "name": "LinkedMeterId",
                "type": "string"
            },
            {
                "name": "Type",
                "type": "string"
            }
            ],
            "rows": [
            [
                "861bd466-5400-44be-9552-5ba40823c3aa",
                "-",
                "2017-09-26T13:59:59Z",
                "OMS",
                "2017-09-26T13:00:00Z",
                "2017-09-26T13:59:59Z",
            "/subscriptions/SubscriptionID/resourcegroups/ResourceGroupName/providers/microsoft.operationalinsights/workspaces/workspace-861bd466-5400-44be-9552-5ba40823c3aa",
                null,
                "Operation",
                "LogManagement",
                8,
                0,
                0,
                8,
                0,
                0.002502,
                "MBytes",
                false,
                "a4e29a95-5b4c-408b-80e3-113f9410566e",
                "00000000-0000-0000-0000-000000000000",
                "Usage"
            ]
        }
        ]
    },
    "SearchIntervalStartTimeUtc": "2017-09-26T08:10:40Z",
    "SearchIntervalEndtimeUtc": "2017-09-26T09:10:40Z",
    "AlertThresholdOperator": "Greater Than",
    "AlertThresholdValue": 0,
    "ResultCount": 1,
    "SearchIntervalInSeconds": 3600,
    "LinkToSearchResults": "https://workspaceID.portal.mms.microsoft.com/#Workspace/search/index?_timeInterval.intervalEnd=2017-09-26T09%3a10%3a40.0000000Z&_timeInterval.intervalDuration=3600&q=Usage",
    "Description": null,
    "Severity": "Critical"
    }



## <a name="next-steps"></a>Дальнейшие действия
- Вы можете выполнить пошаговое руководство по [настройке webook](log-analytics-alerts-webhooks.md) с использованием правила генерации оповещений.  
- Вы можете научиться писать [модули Runbook в службе автоматизации Azure](https://azure.microsoft.com/documentation/services/automation) для устранения проблем, обозначенных в оповещениях.
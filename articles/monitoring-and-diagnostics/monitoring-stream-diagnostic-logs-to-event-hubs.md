---
title: "Потоковая передача журналов диагностики Azure в концентраторы событий | Документация Майкрософт"
description: "Узнайте, как настроить потоковую передачу журналов диагностики Azure в концентраторы событий."
author: johnkemnetz
manager: orenr
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 42bc4845-c564-4568-b72d-0614591ebd80
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/09/2016
ms.author: johnkem
ms.translationtype: HT
ms.sourcegitcommit: cddb80997d29267db6873373e0a8609d54dd1576
ms.openlocfilehash: d974d64dbafe15707a6ce86144b98bad334f7f00
ms.contentlocale: ru-ru
ms.lasthandoff: 07/18/2017

---
# <a name="stream-azure-diagnostic-logs-to-event-hubs"></a>Потоковая передача журналов диагностики Azure в концентраторы событий
**[Журналы диагностики Azure](monitoring-overview-of-diagnostic-logs.md)** можно передавать в близком к реальному времени в любое приложение. Для этого достаточно настроить стандартный параметр "Экспорт в концентраторы событий" на портале или включить идентификатор правила служебной шины в параметрах диагностики с помощью командлетов Azure PowerShell или интерфейса командной строки Azure.

## <a name="what-you-can-do-with-diagnostics-logs-and-event-hubs"></a>Что можно делать с журналами диагностики и концентраторами событий
Мы приведем лишь несколько примеров использования потоковой передачи журналов диагностики.

* **Потоковая передача журналов в сторонние системы ведения журналов и сбора телеметрии**. Через некоторое время потоковая передача в концентраторы событий будет использоваться для передачи журналов диагностики в сторонние решения SIEM и решения для анализа журналов.
* **Проверка работоспособности службы путем потоковой передачи данных критического пути в PowerBI**. С помощью концентраторов событий, Stream Analytics и PowerBI можно в близком к реальному времени получать аналитическую информацию о службах Azure на основе диагностических данных. [В этой статье приводятся подробные инструкции по настройке концентраторов событий, обработке данных в Stream Analytics и выводу информации через Power BI](../stream-analytics/stream-analytics-power-bi-dashboard.md). Вот несколько советов по настройке журналов диагностики.
  
  * Концентраторы событий для категории журналов диагностики создаются автоматически, когда вы выбираете соответствующий вариант на портале или включаете его с помощью PowerShell. Поэтому концентраторы событий следует выбирать в пространстве имен, имя которого начинается с **insights-**.
  * Приведенный ниже пример SQL-запроса для Stream Analytics позволяет перенести все данные из журнала в таблицу Power BI:

    ```sql
    SELECT
    records.ArrayValue.[Properties you want to track]
    INTO
    [OutputSourceName – the PowerBI source]
    FROM
    [InputSourceName] AS e
    CROSS APPLY GetArrayElements(e.records) AS records
    ```

* **Создание пользовательской платформы для телеметрии и ведения журнала**. Если у вас уже есть пользовательская платформа для телеметрии и ведения журнала или вы только планируете ее создать, высокая масштабируемость публикации и подписки концентраторов событий позволит вам гибко настраивать прием журналов диагностики. [Ознакомьтесь с руководством Дэна Росановы (Dan Rosanova) по использованию концентраторов событий для глобальной платформы телеметрии](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/).

## <a name="enable-streaming-of-diagnostic-logs"></a>Включение потоковой передачи журналов диагностики
Потоковую передачу журналов диагностики можно включить программно, через портал или с помощью [REST API Azure Monitor](https://msdn.microsoft.com/library/azure/dn931943.aspx). В любом сценарии вам следует выбрать пространство имен концентраторов событий, в котором будут созданы концентраторы событий для каждой включенной категории журнала. **Категория журналов диагностики** — это тип журналов, которые может собирать ресурс. Категории журналов, которые нужно собирать для конкретного ресурса, можно выбрать на портале Azure в колонке "Диагностика".

![Категории журналов на портале](./media/monitoring-stream-diagnostic-logs-to-event-hubs/log-categories.png)

> [!WARNING]
> Для включения и потоковой передачи журналов диагностики из вычислительных ресурсов (например, виртуальных машин или Service Fabric) [используется другая последовательность действий](../event-hubs/event-hubs-streaming-azure-diags-data.md).
> 
> 

Пространство имен служебной шины или концентраторов событий и создающий журналы ресурс могут находиться в разных подписках. Достаточно, чтобы у пользователя, настраивающего этот режим, был необходимый доступ RBAC к обеим подпискам.

### <a name="via-powershell-cmdlets"></a>С помощью командлетов PowerShell
Для включения потоковой передачи с помощью [командлетов Azure PowerShell](insights-powershell-samples.md) применяется командлет `Set-AzureRmDiagnosticSetting` с такими параметрами:

```powershell
Set-AzureRmDiagnosticSetting -ResourceId [your resource Id] -ServiceBusRuleId [your Service Bus rule id] -Enabled $true
```

В параметре ServiceBusRuleID передается строка в таком формате: `{Service Bus resource ID}/authorizationrules/{key name}`. Например: `/subscriptions/{subscription ID}/resourceGroups/Default-ServiceBus-WestUS/providers/Microsoft.ServiceBus/namespaces/{Service Bus namespace}/authorizationrules/RootManageSharedAccessKey`.

### <a name="via-azure-cli"></a>С помощью интерфейса командной строки Azure
Для включения потоковой передачи через [интерфейс командной строки Azure](insights-cli-samples.md) применяется команда `insights diagnostic set` с такими параметрами:

```azurecli
azure insights diagnostic set --resourceId <resourceId> --serviceBusRuleId <serviceBusRuleId> --enabled true
```

Формат для ServiceBusRuleID будет таким же, как описано для командлета PowerShell.

### <a name="via-azure-portal"></a>С помощью портала Azure
Чтобы включить потоковую передачу на портале Azure, перейдите к параметрам диагностики ресурса и выберите **Экспорт в концентратор событий**.

![Экспорт в концентраторы событий на портале](./media/monitoring-stream-diagnostic-logs-to-event-hubs/portal-export.png)

Чтобы настроить его, выберите существующее пространство имен концентраторов событий. В выбранном пространстве имен будет создан концентратор событий (если вы еще не выполняли потоковую передачу журналов диагностики), или в него будет выполняться передача (если уже существуют ресурсы для потоковой передачи соответствующей категории журналов в это пространство имен). Разрешения, предоставляемые механизму потоковой передачи, определяются политикой. Сейчас для потоковой передачи журналов в концентратор событий нужны разрешения "Управление", "Отправка" и "Прослушивание". Политики общего доступа для пространства имен концентраторов событий можно создать или изменить на портале, на вкладке **Настройка** соответствующего пространства имен. Чтобы изменить эти параметры диагностики, клиент должен иметь разрешение **ListKey** для правила авторизации концентраторов событий.

## <a name="how-do-i-consume-the-log-data-from-event-hubs"></a>Как используются данные журнала из концентраторов событий?
Ниже приведен пример выходных данных из концентраторов событий.

```json
{
    "records": [
        {
            "time": "2016-07-15T18:00:22.6235064Z",
            "workflowId": "/SUBSCRIPTIONS/DF602C9C-7AA0-407D-A6FB-EB20C8BD1192/RESOURCEGROUPS/JOHNKEMTEST/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/JOHNKEMTESTLA",
            "resourceId": "/SUBSCRIPTIONS/DF602C9C-7AA0-407D-A6FB-EB20C8BD1192/RESOURCEGROUPS/JOHNKEMTEST/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/JOHNKEMTESTLA/RUNS/08587330013509921957/ACTIONS/SEND_EMAIL",
            "category": "WorkflowRuntime",
            "level": "Error",
            "operationName": "Microsoft.Logic/workflows/workflowActionCompleted",
            "properties": {
                "$schema": "2016-04-01-preview",
                "startTime": "2016-07-15T17:58:55.048482Z",
                "endTime": "2016-07-15T18:00:22.4109204Z",
                "status": "Failed",
                "code": "BadGateway",
                "resource": {
                    "subscriptionId": "df602c9c-7aa0-407d-a6fb-eb20c8bd1192",
                    "resourceGroupName": "JohnKemTest",
                    "workflowId": "243aac67fe904cf195d4a28297803785",
                    "workflowName": "JohnKemTestLA",
                    "runId": "08587330013509921957",
                    "location": "westus",
                    "actionName": "Send_email"
                },
                "correlation": {
                    "actionTrackingId": "29a9862f-969b-4c70-90c4-dfbdc814e413",
                    "clientTrackingId": "08587330013509921958"
                }
            }
        },
        {
            "time": "2016-07-15T18:01:15.7532989Z",
            "workflowId": "/SUBSCRIPTIONS/DF602C9C-7AA0-407D-A6FB-EB20C8BD1192/RESOURCEGROUPS/JOHNKEMTEST/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/JOHNKEMTESTLA",
            "resourceId": "/SUBSCRIPTIONS/DF602C9C-7AA0-407D-A6FB-EB20C8BD1192/RESOURCEGROUPS/JOHNKEMTEST/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/JOHNKEMTESTLA/RUNS/08587330012106702630/ACTIONS/SEND_EMAIL",
            "category": "WorkflowRuntime",
            "level": "Information",
            "operationName": "Microsoft.Logic/workflows/workflowActionStarted",
            "properties": {
                "$schema": "2016-04-01-preview",
                "startTime": "2016-07-15T18:01:15.5828115Z",
                "status": "Running",
                "resource": {
                    "subscriptionId": "df602c9c-7aa0-407d-a6fb-eb20c8bd1192",
                    "resourceGroupName": "JohnKemTest",
                    "workflowId": "243aac67fe904cf195d4a28297803785",
                    "workflowName": "JohnKemTestLA",
                    "runId": "08587330012106702630",
                    "location": "westus",
                    "actionName": "Send_email"
                },
                "correlation": {
                    "actionTrackingId": "042fb72c-7bd4-439e-89eb-3cf4409d429e",
                    "clientTrackingId": "08587330012106702632"
                }
            }
        }
    ]
}
```

| Имя элемента | Description (Описание) |
| --- | --- |
| records |Массив всех событий журнала, включенных в этот набор полезных данных. |
| time |Время возникновения события. |
| category |Категория журнала для этого события. |
| resourceId |Идентификатор ресурса, который создал это событие. |
| operationName |Имя операции. |
| level |необязательный параметр. Уровень ведения журнала событий. |
| properties |Свойства события. |

Список всех поставщиков ресурсов, которые поддерживают потоковую передачу в концентраторы событий, см. [здесь](monitoring-overview-of-diagnostic-logs.md).

## <a name="stream-data-from-compute-resources"></a>Потоковая передача данных от вычислительных ресурсов
Можно также передавать поток данных журналов диагностики от вычислительных ресурсов с помощью агента системы диагностики Azure. [В этой статье](../event-hubs/event-hubs-streaming-azure-diags-data.md) описано, как это настроить.

## <a name="next-steps"></a>Дальнейшие действия
* [Дополнительные сведения о журналах диагностики Azure](monitoring-overview-of-diagnostic-logs.md)
* [Приступая к работе с концентраторами событий](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)



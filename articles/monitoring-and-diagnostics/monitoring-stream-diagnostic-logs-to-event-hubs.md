---
title: "Поток журналы диагностики Azure в концентратор событий | Документы Microsoft"
description: "Дополнительные сведения о потоковой Azure журналам диагностики в концентратор событий."
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
ms.date: 12/22/2017
ms.author: johnkem
ms.openlocfilehash: bcb9fcb2371217e7082d96ddbba4a095e6d9a00f
ms.sourcegitcommit: a648f9d7a502bfbab4cd89c9e25aa03d1a0c412b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/22/2017
---
# <a name="stream-azure-diagnostic-logs-to-an-event-hub"></a>Журналы диагностики Azure потока в концентратор событий
**[Журналы Azure диагностики](monitoring-overview-of-diagnostic-logs.md)**  может передаваться почти в реальном времени для любого приложения, используя встроенный параметр «Экспорт для концентраторов событий» на портале или путем включения в параметрах диагностики через Azure идентификатор правила авторизации концентратора событий Командлеты PowerShell или Azure CLI.

## <a name="what-you-can-do-with-diagnostics-logs-and-event-hubs"></a>Что можно делать с журналами диагностики и концентраторами событий
Мы приведем лишь несколько примеров использования потоковой передачи журналов диагностики.

* **Поток журналы для ведения журнала и данных телеметрии систем сторонних разработчиков** — можно осуществлять потоковую передачу всех диагностических журналов к концентратору одно событие для канала данных журнала к средству аналитики сторонних SIEM или журнала.
* **Проверка работоспособности службы путем потоковой передачи данных критического пути в PowerBI.** С помощью концентраторов событий, Stream Analytics и PowerBI можно в близком к реальному времени преобразовать аналитическую информацию о службах Azure на основе диагностических данных. [В этой статье приводятся подробные инструкции по настройке концентраторов событий, обработке данных в Stream Analytics и выводу информации через Power BI](../stream-analytics/stream-analytics-power-bi-dashboard.md). Вот несколько советов по настройке журналов диагностики:
  
  * Концентраторы событий для категории журналов диагностики создаются автоматически, когда вы выбираете соответствующий вариант на портале или включаете его с помощью PowerShell. Поэтому концентраторы событий следует выбирать в пространстве имен, имя которого начинается с **insights-**.
  * Приведенный ниже пример SQL-кода для Stream Analytics позволяет перенести все данные из журнала в таблицу Power BI:

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
Потоковую передачу журналов диагностики можно включить программно, через портал или с помощью [REST API Azure Monitor](https://docs.microsoft.com/rest/api/monitor/servicediagnosticsettings). В любом случае создается параметр диагностики, в котором необходимо указать пространство имен концентраторов событий, а также категории журнала и метрики, которые требуется отправить в пространство имен. Концентратор событий создается в пространстве имен для каждой включенной категории журнала. Категория **журналов диагностики** — это тип журналов, которые может собирать ресурс.

> [!WARNING]
> Для включения и потоковой передачи журналов диагностики из вычислительных ресурсов (например, виртуальных машин или Service Fabric) [используется другая последовательность действий](../event-hubs/event-hubs-streaming-azure-diags-data.md).
> 
> 

Пространство имен концентраторы событий не быть в той же подписке, в качестве ресурса, создающие журналы при условии, что пользователь, который настраивает параметр имеет соответствующий доступ RBAC для обеих подписок.

## <a name="stream-diagnostic-logs-using-the-portal"></a>Потоковая передача журналов диагностики с помощью портала
1. На портале перейдите к Azure Monitor и щелкните **Параметры диагностики**.

    ![Раздел мониторинга Azure Monitor](media/monitoring-stream-diagnostic-logs-to-event-hubs/diagnostic-settings-blade.png)

2. При необходимости отфильтруйте список по группе или типу ресурса, а затем щелкните ресурс, для которого необходимо задать параметр диагностики.

3. Если параметров для выбранного ресурса не существует, вам будет предложено создать параметр. Щелкните Turn on diagnostics (Включить диагностику).

   ![Добавление параметра диагностики — имеющиеся параметры отсутствуют](media/monitoring-stream-diagnostic-logs-to-event-hubs/diagnostic-settings-none.png)

   Если в ресурсе имеются параметры, для него отобразится список настроенных параметров. Нажмите Add diagnostic setting (Добавить параметр диагностики).

   ![Добавление параметра диагностики — имеющиеся параметры](media/monitoring-stream-diagnostic-logs-to-event-hubs/diagnostic-settings-multiple.png)

3. Введите имя параметра и установите флажок **Stream to an event hub** (Потоковая передача в концентратор событий), затем выберите пространство имен концентраторов событий.
   
   ![Добавление параметра диагностики — имеющиеся параметры](media/monitoring-stream-diagnostic-logs-to-event-hubs/diagnostic-settings-configure.png)
    
   В выбранном пространстве имен будет создан концентратор событий (если вы еще не выполняли потоковую передачу журналов диагностики), или в него будет выполняться передача (если уже существуют ресурсы для потоковой передачи соответствующей категории журналов в это пространство имен). Разрешения, предоставляемые механизму потоковой передачи, определяются политикой. Сейчас для потоковой передачи журналов в концентратор событий нужны разрешения "Управление", "Отправка" и "Прослушивание". Политики общего доступа для пространства имен концентраторов событий можно создать или изменить на портале, на вкладке "Настройка" соответствующего пространства имен. Чтобы обновить один из этих параметров диагностики, клиент должен иметь разрешение ListKey для правила авторизации концентраторов событий. Также при необходимости можно указать имя концентратора событий. Если необходимо указывать имя концентратора событий, журналы будут направляться в этот концентратор событий, а не к концентратору событий только что созданный по категориям журнала.

4. Выберите команду **Сохранить**.

Через несколько секунд появляется новый параметр в списке параметров для данного ресурса и журналы диагностики передаются потоком этот концентратор событий, как только создается новый данные события.

### <a name="via-powershell-cmdlets"></a>С помощью командлетов PowerShell
Для включения потоковой передачи с помощью [командлетов Azure PowerShell](insights-powershell-samples.md) применяется командлет `Set-AzureRmDiagnosticSetting` с такими параметрами:

```powershell
Set-AzureRmDiagnosticSetting -ResourceId [your resource ID] -ServiceBusRuleId [your Service Bus rule ID] -Enabled $true
```

В параметре ServiceBusRuleID передается строка в таком формате: `{Service Bus resource ID}/authorizationrules/{key name}`. Например: `/subscriptions/{subscription ID}/resourceGroups/Default-ServiceBus-WestUS/providers/Microsoft.ServiceBus/namespaces/{Service Bus namespace}/authorizationrules/RootManageSharedAccessKey`. Нельзя выбрать имя концентратора определенного события с помощью PowerShell в настоящее время.

### <a name="via-azure-cli"></a>С помощью интерфейса командной строки Azure
Для включения потоковой передачи через [интерфейс командной строки Azure](insights-cli-samples.md) применяется команда `insights diagnostic set` с такими параметрами:

```azurecli
azure insights diagnostic set --resourceId <resourceID> --serviceBusRuleId <serviceBusRuleID> --enabled true
```

Формат для ServiceBusRuleID будет таким же, как описано для командлета PowerShell. Имя концентратора определенного события с помощью Azure CLI нельзя выбирать в настоящее время.

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

| Имя элемента | ОПИСАНИЕ |
| --- | --- |
| records |Массив всех событий журнала, включенных в этот набор полезных данных. |
| Twitter в режиме реального |Время возникновения события. |
| category |Категория журнала для этого события. |
| ResourceId |Идентификатор ресурса, который создал это событие. |
| operationName |Имя операции. |
| level |Необязательный элемент. Уровень ведения журнала событий. |
| properties |Свойства события. |

Список всех поставщиков ресурсов, которые поддерживают потоковую передачу в концентраторы событий, см. [здесь](monitoring-overview-of-diagnostic-logs.md).

## <a name="stream-data-from-compute-resources"></a>Потоковая передача данных от вычислительных ресурсов
Можно также передавать поток данных журналов диагностики от вычислительных ресурсов с помощью агента системы диагностики Azure. [В этой статье](../event-hubs/event-hubs-streaming-azure-diags-data.md) описано, как это настроить.

## <a name="next-steps"></a>Дальнейшие действия
* [Дополнительные сведения о журналах диагностики Azure](monitoring-overview-of-diagnostic-logs.md)
* [Приступая к работе с концентраторами событий](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)


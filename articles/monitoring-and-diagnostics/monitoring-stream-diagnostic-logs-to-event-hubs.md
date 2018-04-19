---
title: Потоковая передача журналов диагностики Azure в концентратор событий | Документация Майкрософт
description: Узнайте, как настроить потоковую передачу журналов диагностики Azure в концентратор событий.
author: johnkemnetz
manager: orenr
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 42bc4845-c564-4568-b72d-0614591ebd80
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/04/2018
ms.author: johnkem
ms.openlocfilehash: 1f5a97f5af47a3c5731d5c5d4d5e8cf17097ae60
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/06/2018
---
# <a name="stream-azure-diagnostic-logs-to-an-event-hub"></a>Потоковая передача журналов диагностики Azure в концентратор событий
**[Журналы диагностики Azure](monitoring-overview-of-diagnostic-logs.md)** можно передавать в режиме, близком к реальному времени, в любое приложение. Для этого достаточно использовать стандартный параметр Export to Event Hubs (Экспорт в службу "Концентраторы событий") на портале или включить идентификатор правила авторизации концентратора событий в параметрах диагностики с помощью командлетов Azure PowerShell или Azure CLI 2.0.

## <a name="what-you-can-do-with-diagnostics-logs-and-event-hubs"></a>Что можно делать с журналами диагностики и концентраторами событий
Мы приведем лишь несколько примеров использования потоковой передачи журналов диагностики.

* **Потоковая передача журналов в сторонние системы ведения журналов и сбора телеметрии.** Вы можете передать потоком все журналы диагностики в отдельный концентратор событий, чтобы направить данные журналов в канал SIEM стороннего производителя или средство аналитики журналов.
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

Пространство имен концентраторов событий и создающий журналы ресурс могут находиться в разных подписках. Достаточно, чтобы пользователь, настраивающий этот режим, имел необходимый доступ RBAC к обеим подпискам.

> [!NOTE]
> Отправка многомерных метрик с помощью параметров диагностики сейчас не поддерживается. Метрики с измерениями экспортируются как преобразованные в плоскую структуру одномерные метрики, агрегированные по значениям измерений.
>
> *Пример.* Метрику "Входящие сообщения" в концентраторе событий можно изучить и вывести в виде диаграммы на уровне очереди. Но при экспорте с помощью параметров диагностики метрика будет представлена в виде всех входящих сообщений для всех очередей концентратора событий.
>
>

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

   В выбранном пространстве имен будет создан концентратор событий (если вы еще не выполняли потоковую передачу журналов диагностики), или в него будет выполняться передача (если уже существуют ресурсы для потоковой передачи соответствующей категории журналов в это пространство имен). Разрешения, предоставляемые механизму потоковой передачи, определяются политикой. Сейчас для потоковой передачи журналов в концентратор событий нужны разрешения "Управление", "Отправка" и "Прослушивание". Политики общего доступа для пространства имен концентраторов событий можно создать или изменить на портале, на вкладке "Настройка" соответствующего пространства имен. Чтобы обновить один из этих параметров диагностики, клиент должен иметь разрешение ListKey для правила авторизации концентраторов событий. При необходимости можно также указать имя концентратора событий. Это позволит перенаправлять журналы в этот концентратор событий, а не в созданный для каждой категории журналов.

4. Выберите команду **Сохранить**.

Через несколько секунд новый параметр появится в списке параметров этого ресурса, и сразу же после создания данных о событии журналы диагностики будут отправлены в необходимый концентратор событий.

### <a name="via-powershell-cmdlets"></a>С помощью командлетов PowerShell

Для включения потоковой передачи с помощью [командлетов Azure PowerShell](insights-powershell-samples.md) применяется командлет `Set-AzureRmDiagnosticSetting` с такими параметрами:

```powershell
Set-AzureRmDiagnosticSetting -ResourceId [your resource ID] -EventHubAuthorizationRuleId [your Event Hub namespace auth rule ID] -Enabled $true
```

Идентификатор правила авторизации концентратора событий представляет собой строку в формате `{Event Hub namespace resource ID}/authorizationrules/{key name}`. Пример: `/subscriptions/{subscription ID}/resourceGroups/{resource group}/providers/Microsoft.EventHub/namespaces/{Event Hub namespace}/authorizationrules/RootManageSharedAccessKey`. Сейчас с помощью PowerShell нельзя выбирать концентратор событий с определенным именем.

### <a name="via-azure-cli-20"></a>С помощью Azure CLI 2.0

Чтобы включить потоковую передачу с помощью [Azure CLI 2.0](insights-cli-samples.md), используйте команду [az monitor diagnostic-settings create](/cli/azure/monitor/diagnostic-settings#az-monitor-diagnostic-settings-create).

```azurecli
az monitor diagnostic-settings create --name <diagnostic name> \
    --event-hub <event hub name> \
    --event-hub-rule <event hub rule ID> \
    --resource <target resource object ID> \
    --logs '[
    {
        "category": <category name>,
        "enabled": true
    }
    ]'
```

Вы можете расширить набор категорий в журнале диагностики, добавив словари в массив JSON, переданный в качестве параметра `--logs`.

Для аргумента `--event-hub-rule` используется то же формат, что и для идентификатора правила авторизации концентратора событий, как описано для командлета PowerShell.

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

## <a name="next-steps"></a>Дополнительная информация

* [Дополнительные сведения о журналах диагностики Azure](monitoring-overview-of-diagnostic-logs.md)
* [Приступая к работе с концентраторами событий](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)

<properties
	pageTitle="Потоковая передача журналов диагностики Azure в концентраторы событий | Microsoft Azure"
	description="Узнайте, как настроить потоковую передачу журналов диагностики Azure в концентраторы событий."
	authors="johnkemnetz"
	manager="rboucher"
	editor=""
	services="monitoring-and-diagnostics"
	documentationCenter="monitoring-and-diagnostics"/>

<tags
	ms.service="monitoring-and-diagnostics"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/08/2016"
	ms.author="johnkem"/>

# Потоковая передача журналов диагностики Azure в концентраторы событий

**[Журналы диагностики Azure](monitoring-overview-of-diagnostic-logs.md)** можно передавать в близком к реальному времени в любое приложение. Для этого достаточно настроить стандартный параметр "Экспорт в концентраторы событий" на портале или включить идентификатор правила служебной шины в параметрах диагностики с помощью командлетов Azure PowerShell или интерфейса командной строки Azure.

## Что можно делать с журналами диагностики и концентраторами событий
Мы приведем лишь несколько примеров использования потоковой передачи журналов диагностики.

- **Потоковая передача журналов в сторонние системы ведения журналов и сбора телеметрии**. Через некоторое время потоковая передача в концентраторы событий будет использоваться для передачи журналов диагностики в сторонние решения SIEM и решения для анализа журналов.

- **Проверка работоспособности службы путем потоковой передачи данных критического пути в PowerBI**. С помощью концентраторов событий, Stream Analytics и PowerBI можно в близком к реальному времени получать аналитическую информацию о службах Azure на основе диагностических данных. [В этой статье приводится подробный обзор настройки концентраторов событий, обработки данных в Stream Analytics и вывода информации через PowerBI](../stream-analytics/stream-analytics-power-bi-dashboard.md). Вот несколько советов по настройке журналов диагностики.
	- Концентраторы событий для категории журналов диагностики создаются автоматически, когда вы выбираете соответствующий вариант на портале или с помощью PowerShell, поэтому следует выбирать концентраторы событий в пространстве имен служебной шины, имена которых начинаются с "insights-".
	- Этот пример запроса Stream Analytics выполняет простой перенос всех данных из журнала в таблицу PowerBI:

```
SELECT
records.ArrayValue.[Properties you want to track]
INTO
[OutputSourceName – the PowerBI source]
FROM
[InputSourceName] AS e
CROSS APPLY GetArrayElements(e.records) AS records
```

- **Создание пользовательской платформы для телеметрии и ведения журнала**. Если у вас уже есть пользовательская платформа для телеметрии и ведения журнала или вы только планируете ее создать, высокая масштабируемость публикации и подписки концентраторов событий позволит вам гибко настраивать прием журналов диагностики. [Ознакомьтесь с руководством Дэна Росановы (Dan Rosanova) по использованию концентраторов событий для глобальной платформы телеметрии](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/).

## Включение потоковой передачи журналов диагностики
Потоковую передачу журналов диагностики можно включить программно, через портал или с помощью [REST API для аналитики](https://msdn.microsoft.com/library/azure/dn931943.aspx). В каждом из вариантов вам следует выбрать пространство имен служебной шины. Концентраторы событий будут созданы в этом пространстве имен для каждой включенной категории журнала. **Категория журналов диагностики** — это тип журналов, которые может собирать ресурс. Категории журналов, которые нужно собирать для конкретного ресурса, можно выбрать на портале Azure в колонке "Диагностика".

![Категории журналов на портале](./media/monitoring-stream-diagnostic-logs-to-event-hubs/log-categories.png)

> [AZURE.WARNING] Для включения и потоковой передачи журналов диагностики из вычислительных ресурсов (например, виртуальных машин или Service Fabric) [используется другая последовательность действий](../event-hubs/event-hubs-streaming-azure-diags-data.md).

### С помощью командлетов PowerShell
Для включения потоковой передачи с помощью [командлетов Azure PowerShell](insights-powershell-samples.md) применяется командлет `Set-AzureRmDiagnosticSetting` с такими параметрами:

```
Set-AzureRmDiagnosticSetting -ResourceId [your resource Id] -ServiceBusRuleId [your service bus rule id] -Enabled $true
```

В параметре ServiceBusRuleID передается строка в таком формате: `{service bus resource ID}/authorizationrules/{key name}`. Например: `/subscriptions/{subscription ID}/resourceGroups/Default-ServiceBus-WestUS/providers/Microsoft.ServiceBus/namespaces/{service bus namespace}/authorizationrules/RootManageSharedAccessKey`.


### С помощью интерфейса командной строки Azure
Для включения потоковой передачи через [интерфейс командной строки Azure](insights-cli-samples.md) применяется команда `insights diagnostic set` с такими параметрами:

```
azure insights diagnostic set --resourceId <resourceId> --serviceBusRuleId <serviceBusRuleId> --enabled true
```

Формат для ServiceBusRuleID будет таким же, как описано для командлета PowerShell.

###С помощью портала Azure
Чтобы включить потоковую передачу на портале Azure, перейдите в параметры диагностики ресурса и выберите "Экспорт в концентраторы событий".

![Экспорт в концентраторы событий на портале](./media/monitoring-stream-diagnostic-logs-to-event-hubs/portal-export.png)

Для настройки выберите существующее пространство имен служебной шины. В выбранном пространстве имен будут созданы концентраторы событий (если ранее вы не выполняли потоковую передачу журналов диагностики), или в него будет выполняться передача (если уже существуют ресурсы для потоковой передачи соответствующей категории журналов в это пространство имен). Разрешения, предоставляемые механизму потоковой передачи, определяются политикой. В настоящее время для потоковой передачи журналов в концентраторы событий нужны разрешения на управление, чтение и отправку. Политики общего доступа для пространства имен служебной шины можно создать или изменить на классическом портале на вкладке "Настройка" для пространства имен служебной шины. Чтобы изменить эти параметры диагностики, клиент должен иметь разрешение ListKey для правила авторизации служебной шины.

##Как используются данные журнала из концентраторов событий?
Ниже приведен пример выходных данных из концентраторов событий.

```
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
|--------------|--------------------------------------------------------|
|records | Массив всех событий журнала, включенных в этот набор полезных данных. |
|time | Время возникновения события. |
|category | Категория журнала для этого события. |
|resourceId | Идентификатор ресурса, который создал это событие. |
|operationName | Имя операции. |
|level | необязательный параметр. Уровень ведения журнала событий. |
|properties | Свойства события. |


Список всех поставщиков ресурсов, которые поддерживают потоковую передачу в концентратор событий, см. [здесь](monitoring-overview-of-diagnostic-logs.md).

##Дальнейшие действия
- [Дополнительные сведения о журналах диагностики Azure](monitoring-overview-of-diagnostic-logs.md)
- [Приступая к работе с концентраторами событий](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)

<!---HONumber=AcomDC_0817_2016-->
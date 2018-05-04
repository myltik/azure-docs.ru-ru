---
title: Мониторинг фабрик данных с помощью Azure Monitor | Документация Майкрософт
description: Сведения об использовании Azure Monitor для мониторинга конвейеров фабрики данных с помощью включения журналов диагностики с данными из фабрики данных Azure.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2018
ms.author: shlo
ms.openlocfilehash: 798af75625e0d2fed1220932c172683fe71f9aad
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2018
---
# <a name="monitor-data-factories-using-azure-monitor"></a>Мониторинг фабрик данных с помощью Azure Monitor  
Облачные приложения являются сложными и содержат множество подвижных частей. Мониторинг дает возможность отслеживать данные, чтобы обеспечить работоспособность приложения, а также позволяет предотвратить потенциальные проблемы или устранить неполадки. Кроме того, данные мониторинга можно использовать для получения подробных сведений о приложении. Эти знания могут помочь повысить его производительность и улучшить возможности обслуживания, а также автоматизировать действия, которые в противном случае выполнялись бы вручную.

Azure Monitor предоставляет метрики инфраструктуры базового уровня, а также журналы для большинства служб Microsoft Azure. Дополнительные сведения см. в статье [Обзор Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-azure-monitor). Журналы диагностики Azure генерируются ресурсом. Они содержат подробные и своевременные данные о работе этого ресурса. Фабрика данных выводит журналы диагностики в Azure Monitor. 

> [!NOTE]
> Эта статья относится к версии 2 фабрики данных, которая в настоящее время доступна в предварительной версии. Если вы используете общедоступную версию 1 службы фабрики данных, прочитайте статью [Мониторинг конвейеров фабрики данных Azure и управление ими с помощью приложения для мониторинга и управления](v1/data-factory-monitor-manage-pipelines.md).

## <a name="diagnostic-logs"></a>Журналы диагностики

* Сохранение журналов в **учетную запись хранения** для аудита или проверки вручную. В параметрах диагностики вы также можете задать время хранения (в днях).
* **Потоковая передача журналов в концентраторы событий** для обработки в сторонней службе или пользовательском аналитическом решении, например в PowerBI.
* Анализ журналов с помощью **Log Analytics**.

Вы можете использовать учетную запись хранения или пространство имен концентратора событий, не входящие в подписку, в которой создаются журналы. Пользователю, который настраивает этот параметр, должен быть предоставлен соответствующий уровень доступа RBAC к обеим подпискам.

## <a name="set-up-diagnostic-logs"></a>Настройка журналов диагностики

### <a name="diagnostic-settings"></a>параметрах диагностики
Журналы диагностики для всех ресурсов, кроме вычислительных, настраиваются с помощью параметров диагностики. Параметры диагностики для ресурса определяют следующие настройки.

* Куда будут отправляться журналы диагностики (учетная запись хранения, служба "Концентраторы событий" и (или) Log Analytics).
* Какие категории журналов будут отправляться.
* Как долго должны храниться журналы каждой категории в учетной записи хранения.
* Срок хранения 0 дней означает, что журналы хранятся неограниченно долго. В противном случае укажите количество дней в диапазоне от 1 до 2 147 483 647.
* Если политики хранения заданы, но хранение журналов в учетной записи хранения отключено (например, выбраны только концентраторы событий или Log Analytics), политики хранения не будут применены.
* Политики хранения применяются по дням, поэтому в конце дня (по времени в формате UTC) журналы, срок которых теперь превышает период хранения, будут удалены. Например, если настроена политика хранения в течение одного дня, то в начале текущего дня журналы за вчерашний день будет удалены.

### <a name="enable-diagnostic-logs-via-rest-apis"></a>Включение журналов диагностики с помощью REST API

Создайте или обновите параметры диагностики в REST API Azure Monitor.

**Запрос**
```
PUT
https://management.azure.com/{resource-id}/providers/microsoft.insights/diagnosticSettings/service?api-version={api-version}
```

**Заголовки**
* Замените `{api-version}` на `2016-09-01`.
* Замените `{resource-id}` идентификатором ресурса, параметры диагностики которого вы хотите изменить. Дополнительные сведения см. в статье [Управление ресурсами Azure через портал](../azure-resource-manager/resource-group-portal.md).
* В качестве заголовка `Content-Type` установите `application/json`.
* В качестве заголовка авторизации установите веб-маркер JSON, полученный из Azure Active Directory. Дополнительные сведения см. в статье [Сценарии аутентификации в Azure Active Directory](../active-directory/develop/active-directory-authentication-scenarios.md).

**Текст**
```json
{
    "properties": {
        "storageAccountId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>/providers/Microsoft.Storage/storageAccounts/<storageAccountName>",
        "serviceBusRuleId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>/providers/Microsoft.EventHub/namespaces/<eventHubName>/authorizationrules/RootManageSharedAccessKey",
        "workspaceId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<LogAnalyticsName>",
        "metrics": [
        ],
        "logs": [
                {
                    "category": "PipelineRuns",
                    "enabled": true,
                    "retentionPolicy": {
                        "enabled": false,
                        "days": 0
                    }
                },
                {
                    "category": "TriggerRuns",
                    "enabled": true,
                    "retentionPolicy": {
                        "enabled": false,
                        "days": 0
                    }
                },
                {
                    "category": "ActivityRuns",
                    "enabled": true,
                    "retentionPolicy": {
                        "enabled": false,
                        "days": 0
                    }
                }
            ]
    },
    "location": ""
} 
```

| Свойство | type | ОПИСАНИЕ |
| --- | --- | --- |
| storageAccountId |Строка | Идентификатор учетной записи хранения, в которую необходимо отправить журналы диагностики. |
| serviceBusRuleId |Строка | Идентификатор правила служебной шины для пространства имен служебной шины, в котором будут созданы концентраторы событий для потоковой передачи журналов диагностики. Идентификатор правила имеет формат: {идентификатор ресурса служебной шины}/authorizationrules/{имя ключа}.|
| workspaceId | Сложный тип | Массив интервалов времени метрики и политики их хранения. В настоящее время это свойство пусто. |
|Метрики| Значения параметров выполнения конвейера, которые должны быть переданы в вызываемый конвейер.| Объект JSON, сопоставляющий имена параметров со значениями аргументов. | 
| журналы| Сложный тип| Имя категории журнала диагностики для типа ресурса. Чтобы получить список категорий журналов диагностики ресурса, сначала выполните операцию получения параметров диагностики. |
| category| Строка| Массив категорий журналов и политики их хранения. |
| timeGrain | Строка | Уровень детализации метрик, сохраненных в формате длительности ISO 8601. Должно использоваться значение PT1M (одна минута).|
| Включено| Логическое | Указывает, включена ли коллекция этой метрики или категории журнала для выбранного ресурса.|
| retentionPolicy| Сложный тип| Описывает политику хранения метрики или категории журналов. Используется только для хранения параметра учетной записи.|
| days| int| Число дней для хранения метрик или журналов. Нулевое значение означает, что журналы хранятся неограниченно долго. Используется только для хранения параметра учетной записи. |

**Ответ**

200 ОК


```json
{
    "id": "/subscriptions/<subID>/resourcegroups/adf/providers/microsoft.datafactory/factories/shloadobetest2/providers/microsoft.insights/diagnosticSettings/service",
    "type": null,
    "name": "service",
    "location": null,
    "kind": null,
    "tags": null,
    "properties": {
        "storageAccountId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>//providers/Microsoft.Storage/storageAccounts/<storageAccountName>",
        "serviceBusRuleId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>//providers/Microsoft.EventHub/namespaces/<eventHubName>/authorizationrules/RootManageSharedAccessKey",
        "workspaceId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>//providers/Microsoft.OperationalInsights/workspaces/<LogAnalyticsName>",
        "eventHubAuthorizationRuleId": null,
        "eventHubName": null,
        "metrics": [],
        "logs": [
            {
                "category": "PipelineRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            },
            {
                "category": "TriggerRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            },
            {
                "category": "ActivityRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            }
        ]
    },
    "identity": null
}
```

Получение параметров диагностики в REST API Azure Monitor

**Запрос**
```
GET
https://management.azure.com/{resource-id}/providers/microsoft.insights/diagnosticSettings/service?api-version={api-version}
```

**Заголовки**
* Замените `{api-version}` на `2016-09-01`.
* Замените `{resource-id}` идентификатором ресурса, параметры диагностики которого вы хотите изменить. Дополнительные сведения см. в статье "Управление ресурсами Azure через портал".
* В качестве заголовка `Content-Type` установите `application/json`.
* В качестве заголовка авторизации установите веб-маркер JSON, полученный из Azure Active Directory. Дополнительные сведения см. в статье "Сценарии аутентификации в Azure Active Directory".

**Ответ**

200 ОК

```json
{
    "id": "/subscriptions/<subID>/resourcegroups/adf/providers/microsoft.datafactory/factories/shloadobetest2/providers/microsoft.insights/diagnosticSettings/service",
    "type": null,
    "name": "service",
    "location": null,
    "kind": null,
    "tags": null,
    "properties": {
        "storageAccountId": "/subscriptions/<subID>/resourceGroups/shloprivate/providers/Microsoft.Storage/storageAccounts/azmonlogs",
        "serviceBusRuleId": "/subscriptions/<subID>/resourceGroups/shloprivate/providers/Microsoft.EventHub/namespaces/shloeventhub/authorizationrules/RootManageSharedAccessKey",
        "workspaceId": "/subscriptions/<subID>/resourceGroups/ADF/providers/Microsoft.OperationalInsights/workspaces/mihaipie",
        "eventHubAuthorizationRuleId": null,
        "eventHubName": null,
        "metrics": [],
        "logs": [
            {
                "category": "PipelineRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            },
            {
                "category": "TriggerRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            },
            {
                "category": "ActivityRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            }
        ]
    },
    "identity": null
}
```
[Подробнее](https://msdn.microsoft.com/library/azure/dn931932.aspx)

## <a name="schema-of-logs--events"></a>Схема журналов и событий

### <a name="activity-run-logs-attributes"></a>Атрибуты журналов выполнения действий

```json
{  
   "Level": "",
   "correlationId":"",
   "time":"",
   "activityRunId":"",
   "pipelineRunId":"",
   "resourceId":"",
   "category":"ActivityRuns",
   "level":"Informational",
   "operationName":"",
   "pipelineName":"",
   "activityName":"",
   "start":"",
   "end":"",
   "properties:" 
       {
          "Input": "{
              "source": {
                "type": "BlobSource"
              },
              "sink": {
                "type": "BlobSink"
              }
           }",
          "Output": "{"dataRead":121,"dataWritten":121,"copyDuration":5,
               "throughput":0.0236328132,"errors":[]}",
          "Error": "{
              "errorCode": "null",
              "message": "null",
              "failureType": "null",
              "target": "CopyBlobtoBlob"
        }
    }
}
```

| Свойство | type | ОПИСАНИЕ | Пример |
| --- | --- | --- | --- |
| Уровень |Строка | Уровень журналов диагностики. Уровень 4 всегда относится к журналам выполнения действий. | `4`  |
| correlationId |Строка | Уникальный идентификатор для полного отслеживания конкретного запроса. | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| Twitter в режиме реального | Строка | Время возникновения события в формате UTC. | `YYYY-MM-DDTHH:MM:SS.00000Z` | `2017-06-28T21:00:27.3534352Z` |
|activityRunId| Строка| Идентификатор выполнения действия. | `3a171e1f-b36e-4b80-8a54-5625394f4354` |
|pipelineRunId| Строка| Идентификатор выполнения конвейера. | `9f6069d6-e522-4608-9f99-21807bfc3c70` |
|ResourceId| Строка | Идентификатор связанного ресурса фабрики данных. | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|category| Строка | Категория журналов диагностики. Для этого свойства задайте значение ActivityRuns. | `ActivityRuns` |
|level| Строка | Уровень журналов диагностики. Для этого свойства задайте значение Informational. | `Informational` |
|operationName| Строка |Имя действия и состояние. Если состояние — начать пульс, это `MyActivity -`. Если состояние — окончить пульс, это `MyActivity - Succeeded` с конечным состоянием. | `MyActivity - Succeeded` |
|pipelineName| Строка | Имя конвейера. | `MyPipeline` |
|activityName| Строка | Имя действия. | `MyActivity` |
|start| Строка | Время начала выполнения действия в формате UTC. | `2017-06-26T20:55:29.5007959Z`|
|end| Строка | Время окончания выполнения действия в формате UTC. Если действие не завершено (журнал диагностики для действия выполняется), устанавливается значение по умолчанию `1601-01-01T00:00:00Z`.  | `2017-06-26T20:55:29.5007959Z` |


### <a name="pipeline-run-logs-attributes"></a>Атрибуты журналов выполнения конвейеров

```json
{  
   "Level": "",
   "correlationId":"",
   "time":"",
   "runId":"",
   "resourceId":"",
   "category":"PipelineRuns",
   "level":"Informational",
   "operationName":"",
   "pipelineName":"",
   "start":"",
   "end":"",
   "status":"",
   "properties": 
    {
      "Parameters": {
        "<parameter1Name>": "<parameter1Value>"
      },
      "SystemParameters": {
        "ExecutionStart": "",
        "TriggerId": "",
        "SubscriptionId": ""
      }
    }
}
```

| Свойство | type | ОПИСАНИЕ | Пример |
| --- | --- | --- | --- |
| Уровень |Строка | Уровень журналов диагностики. Уровень 4 относится к журналам выполнения действий. | `4`  |
| correlationId |Строка | Уникальный идентификатор для полного отслеживания конкретного запроса. | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| Twitter в режиме реального | Строка | Время возникновения события в формате UTC. | `YYYY-MM-DDTHH:MM:SS.00000Z` | `2017-06-28T21:00:27.3534352Z` |
|runId| Строка| Идентификатор выполнения конвейера. | `9f6069d6-e522-4608-9f99-21807bfc3c70` |
|ResourceId| Строка | Идентификатор связанного ресурса фабрики данных. | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|category| Строка | Категория журналов диагностики. Для этого свойства задайте значение PipelineRuns. | `PipelineRuns` |
|level| Строка | Уровень журналов диагностики. Для этого свойства задайте значение Informational. | `Informational` |
|operationName| Строка |Имя конвейера и его состояние. Pipeline - Succeeded с конечным состоянием при завершении выполнения конвейера.| `MyPipeline - Succeeded` |
|pipelineName| Строка | Имя конвейера. | `MyPipeline` |
|start| Строка | Время начала выполнения действия в формате UTC. | `2017-06-26T20:55:29.5007959Z`|
|end| Строка | Время окончания выполнения действия в формате UTC. Если действие не завершено (журнал диагностики для действия выполняется), устанавливается значение по умолчанию `1601-01-01T00:00:00Z`.  | `2017-06-26T20:55:29.5007959Z` |
|status| Строка | Конечное состояние выполнения конвейера (Succeeded или Failed). | `Succeeded`|


### <a name="trigger-run-logs-attributes"></a>Атрибуты журналов запуска триггеров.

```json
{ 
   "Level": "",
   "correlationId":"",
   "time":"",
   "triggerId":"",
   "resourceId":"",
   "category":"TriggerRuns",
   "level":"Informational",
   "operationName":"",
   "triggerName":"",
   "triggerType":"",
   "triggerEvent":"",
   "start":"",
   "status":"",
   "properties":
   {
      "Parameters": {
        "TriggerTime": "",
       "ScheduleTime": ""
      },
      "SystemParameters": {}
    }
} 

```

| Свойство | type | ОПИСАНИЕ | Пример |
| --- | --- | --- | --- |
| Уровень |Строка | Уровень журналов диагностики. Задайте уровень 4 для журналов выполнения действий. | `4`  |
| correlationId |Строка | Уникальный идентификатор для полного отслеживания конкретного запроса. | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| Twitter в режиме реального | Строка | Время возникновения события в формате UTC. | `YYYY-MM-DDTHH:MM:SS.00000Z` | `2017-06-28T21:00:27.3534352Z` |
|triggerId| Строка| Идентификатор запуска триггера. | `08587023010602533858661257311` |
|ResourceId| Строка | Идентификатор связанного ресурса фабрики данных. | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|category| Строка | Категория журналов диагностики. Для этого свойства задайте значение PipelineRuns. | `PipelineRuns` |
|level| Строка | Уровень журналов диагностики. Для этого свойства задайте значение Informational. | `Informational` |
|operationName| Строка |Имя триггера с конечным состоянием в случае успешного запуска. MyTrigger - Succeeded при успешном запуске пульса.| `MyTrigger - Succeeded` |
|triggerName| Строка | Имя триггера. | `MyTrigger` |
|triggerType| Строка | Тип триггера (ручной триггер или запланированный триггер). | `ScheduleTrigger` |
|triggerEvent| Строка | Событие триггера. | `ScheduleTime - 2017-07-06T01:50:25Z` |
|start| Строка | Время запуска триггера в формате UTC. | `2017-06-26T20:55:29.5007959Z`|
|status| Строка | Конечное состояние запуска триггера (Succeeded или Failed). | `Succeeded`|

## <a name="metrics"></a>Метрики

Azure Monitor позволяет использовать данные телеметрии, чтобы получать сведения о производительности и работоспособности рабочих нагрузок в Azure. Наиболее важным типом данных телеметрии Azure являются метрики (также называемые счетчиками производительности), создаваемые большинством ресурсов Azure. Azure Monitor предоставляет несколько способов настройки и использования этих метрик для мониторинга и устранения неполадок.

ADFV2 выводит следующие метрики.

| **Метрика**           | **Отображаемое имя метрики**         | **Единица измерения** | **Тип статистической обработки** | **Описание**                                       |
|----------------------|---------------------------------|----------|----------------------|-------------------------------------------------------|
| PipelineSucceededRun | Метрики успешных выполнений конвейеров. | Count    | Всего                | Общее количество успешных выполнений конвейеров в минутном окне. |
| PipelineFailedRuns   | Метрики неудачных выполнений конвейеров.    | Count    | Всего                | Общее количество неудачных выполнений конвейеров в минутном окне.    |
| ActiviySucceededRuns | Метрики успешных выполнений действий. | Count    | Всего                | Общее количество успешных выполнений действий в минутном окне.  |
| ActivityFailedRuns   | Метрики неудачных выполнений действий.    | Count    | Всего                | Общее количество неудачных выполнений действий в минутном окне.     |
| TriggerSucceededRuns | Метрики успешных запусков триггеров.  | Count    | Всего                | Общее количество успешных запусков триггеров в минутном окне.   |
| TriggerFailedRuns    | Метрики неудачных запусков триггеров.     | Count    | Всего                | Общее количество успешных запусков триггеров в минутном окне.      |

Следуйте инструкциям из этой статьи: https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics 

## <a name="alerts"></a>Оповещения

Вы можете создавать оповещения на основе поддерживаемых метрик фабрики данных. Нажмите кнопку **Оповещения** кнопку на странице **Монитор** фабрики данных.

![Параметры оповещений](media/monitor-using-azure-monitor/alerts_image1.png)

Откроется страница **Оповещений**.

![Страница оповещений](media/monitor-using-azure-monitor/alerts_image2.png)

Можно также войти на портал Azure и щелкнуть **Монитор -&gt; Оповещения**, чтобы сразу открыть страницу **Оповещения**.

![Оповещения в меню портала](media/monitor-using-azure-monitor/alerts_image3.png)

### <a name="create-alerts"></a>Создание оповещений

1.  Нажмите кнопку **+Новое правило генерации оповещений**, чтобы создать оповещение.

    ![Новое правило генерации оповещений](media/monitor-using-azure-monitor/alerts_image4.png)

2.  Определите **условия оповещения**.

    > [!NOTE]
    > Выберите значение **Все** для параметра **фильтрации по типу ресурсов**.

    ![Условие оповещения, экран 1 из 3](media/monitor-using-azure-monitor/alerts_image5.png)

    ![Условие оповещения, экран 2 из 3](media/monitor-using-azure-monitor/alerts_image6.png)

    ![Условие оповещения, экран 3 из 3](media/monitor-using-azure-monitor/alerts_image7.png)

3.  Определите **сведения об оповещении**.

    ![Сведения об оповещении](media/monitor-using-azure-monitor/alerts_image8.png)

4.  Определите **группу действий**.

    ![Группа действий, экран 1 из 4](media/monitor-using-azure-monitor/alerts_image9.png)

    ![Группа действий, экран 2 из 4](media/monitor-using-azure-monitor/alerts_image10.png)

    ![Группа действий, экран 3 из 4](media/monitor-using-azure-monitor/alerts_image11.png)

    ![Группа действий, экран 4 из 4](media/monitor-using-azure-monitor/alerts_image12.png)

## <a name="next-steps"></a>Дополнительная информация
Дополнительные сведения о мониторинге и управлении конвейерами см. в статье [Отслеживание фабрики данных Azure](monitor-programmatically.md). 

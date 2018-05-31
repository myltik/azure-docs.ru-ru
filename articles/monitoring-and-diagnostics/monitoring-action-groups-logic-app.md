---
title: Активация сложных действий с использованием оповещений и групп действий Azure Monitor | Документация Майкрософт
description: Узнайте, как создать действие приложения логики для обработки оповещения Azure Monitor.
author: dkamstra
manager: chrad
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: ''
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/30/2018
ms.author: dukek
ms.openlocfilehash: 0020f1475d52d01897320062edbd3a66c8acf751
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/08/2018
ms.locfileid: "33887454"
---
# <a name="create-a-logic-app-action"></a>Создание действия приложения логики
## <a name="overview"></a>Обзор ##
Когда активируется оповещение Azure Monitor, оно вызывает [группу действий](monitoring-action-groups.md). Группы действий позволяют активировать одно или несколько действий для уведомления пользователей об оповещении и даже устранения его причины.

В этой статье показано, как настроить и активировать приложение логики, чтобы создать общение в Microsoft Teams при срабатывании оповещения.

Общий процесс приведен ниже:

-   Создание приложения логики для соответствующего типа оповещений.

-   Импорт схемы для соответствующего типа оповещений в приложение логики.

-   Определение поведения приложения логики.

-   Копирование конечной точки HTTP приложения логики в группу действий Azure.

Если требуется, чтобы приложение логики выполняло другое действие, процесс настройки аналогичен.

## <a name="create-an-activity-log-alert--administrative"></a>Создание оповещения журнала действий на портале администрирования

1.  Нажмите кнопку **Создать ресурс** в верхнем левом углу окна портала Azure.

2.  Найдите и выберите **приложение логики**. Нажмите кнопку **Создать** .

3.  Присвойте имя приложению логики, выберите группу ресурсов и т. д.

    ![Диалоговое окно "Создание приложения логики"](media/monitoring-action-groups/create-logic-app-dialog.png "Create logic app dialog")

4.  Нажмите кнопку "Создать", чтобы создать приложение логики. После создания приложения логики появится всплывающее окно. Чтобы открыть конструктор Logic Apps, нажмите кнопку "Launch Resource" (Запустить ресурс).

5.  Выберите триггер **При получении HTTP-запроса**.

    ![Триггеры приложения логики](media/monitoring-action-groups/logic-app-triggers.png "Logic App Triggers")

6.  Выберите **Изменить**, чтобы изменить триггер HTTP-запроса.

    ![Фигура триггера HTTP-запроса](media/monitoring-action-groups/http-request-trigger-shape.png "HTTP request trigger shape")

7.  Выберите **Использовать пример полезной нагрузки, чтобы создать схему**.

    ![Кнопка использования полезных данных](media/monitoring-action-groups/use-sample-payload-button.png "Use sample payload button")

8.  Скопируйте приведенный ниже пример схемы и вставьте его в диалоговое окно.

    ```json
        {
            "schemaId": "Microsoft.Insights/activityLogs",
            "data": {
                "status": "Activated",
                "context": {
                "activityLog": {
                    "authorization": {
                    "action": "microsoft.insights/activityLogAlerts/write",
                    "scope": "/subscriptions/…"
                    },
                    "channels": "Operation",
                    "claims": "…",
                    "caller": "logicappdemo@contoso.com",
                    "correlationId": "91ad2bac-1afa-4932-a2ce-2f8efd6765a3",
                    "description": "",
                    "eventSource": "Administrative",
                    "eventTimestamp": "2018-04-03T22:33:11.762469+00:00",
                    "eventDataId": "ec74c4a2-d7ae-48c3-a4d0-2684a1611ca0",
                    "level": "Informational",
                    "operationName": "microsoft.insights/activityLogAlerts/write",
                    "operationId": "61f59fc8-1442-4c74-9f5f-937392a9723c",
                    "resourceId": "/subscriptions/…",
                    "resourceGroupName": "LOGICAPP-DEMO",
                    "resourceProviderName": "microsoft.insights",
                    "status": "Succeeded",
                    "subStatus": "",
                    "subscriptionId": "…",
                    "submissionTimestamp": "2018-04-03T22:33:36.1068742+00:00",
                    "resourceType": "microsoft.insights/activityLogAlerts"
                }
                },
                "properties": {}
            }
        }
    ```

9. Отобразится конструктор приложений логики с напоминанием о том, что запрос, отправляемый в приложение логики, должен задавать в заголовке Content-Type значение application/json. Закройте это диалоговое окно. Оповещение Azure Monitor сделает это правильным образом.

    ![Заголовок Content-Type](media/monitoring-action-groups/content-type-header.png "Content-Type header")

10. Выберите **+ Новый шаг**, а затем щелкните **Добавить действие**.

    ![Добавление действия](media/monitoring-action-groups/add-action.png "Add action")

11. Найдите и выберите соединитель Microsoft Teams. Выберите действие **Microsoft Teams – Post message** (Microsoft Teams — публикация сообщения).

    ![Действия Microsoft Teams](media/monitoring-action-groups/microsoft-teams-actions.png "Microsoft Teams actions")

12. Настройте действие Microsoft Teams. Конструктор Logic Apps предложит аутентифицироваться с помощью учетной записи Office 365. Выберите значения параметров **Идентификатор команды** и **Идентификатор канала** для отправки сообщения.

13. Настройте **сообщение**, указав статический текст и ссылки на \<поля\> динамического содержимого. Скопируйте приведенный ниже текст и вставьте его в поле "Сообщение".

    ```text
      Activity Log Alert: <eventSource>
      operationName: <operationName>
      status: <status>
      resourceId: <resourceId>
    ```

    Найдите и замените \<поля\> тегами динамического содержимого с такими же именами.

    **[ПРИМЕЧАНИЕ].** Имеются два динамических поля с именем **status**. Добавьте оба эти поля в сообщение. Используйте одно в контейнере свойств activityLog и удалите другое. Если навести указатель мыши на поле **status**, отобразится подсказка с полным именем поля, как показано на снимке экрана.

    ![Действия команды — публикация сообщения](media/monitoring-action-groups/teams-action-post-message.png "Teams Action - Post message")

14. Сохраните приложение логики, нажав кнопку "Сохранить" в верхней части конструктора.

15. Щелкните фигуру HTTP-запроса, чтобы развернуть ее. Скопируйте URL-адрес HTTP-запроса POST.

    ![URL-адрес HTTP-запроса POST](media/monitoring-action-groups/http-post-url.png "HTTP POST URL")

16. Откройте существующую группу действий и добавьте в нее действие, которое ссылается на приложение логики. Если у вас нет группы действий, создайте ее, следуя указаниям в разделе <https://docs.microsoft.com/en-us/azure/monitoring-and-diagnostics/monitoring-action-groups>. Обязательно сохраните изменения.

    ![обновление группы действий](media/monitoring-action-groups/update-action-group.png "Update action group")

При следующем вызове группы действий оповещением будет вызвано ваше приложение логики.

## <a name="create-a-service-health-alert"></a>Создание оповещения о работоспособности служб

Записи о работоспособности служб являются частью журнала действий, поэтому процесс выполняется аналогичным образом со следующими изменениями.

1.  Шаги с 1 по 7 остаются без изменений.
2.  Используйте приведенный пример схемы для триггера HTTP на шаге 8.

    ```json
    {
        "schemaId": "Microsoft.Insights/activityLogs",
        "data": {
            "status": "Activated",
            "context": {
                "activityLog": {
                    "channels": "Admin",
                    "correlationId": "e416ed3c-8874-4ec8-bc6b-54e3c92a24d4",
                    "description": "…",
                    "eventSource": "ServiceHealth",
                    "eventTimestamp": "2018-04-03T22:44:43.7467716+00:00",
                    "eventDataId": "9ce152f5-d435-ee31-2dce-104228486a6d",
                    "level": "Informational",
                    "operationName": "Microsoft.ServiceHealth/incident/action",
                    "operationId": "e416ed3c-8874-4ec8-bc6b-54e3c92a24d4",
                    "properties": {
                        "title": "…",
                        "service": "…",
                        "region": "Global",
                        "communication": "…",
                        "incidentType": "Incident",
                        "trackingId": "…",
                        "impactStartTime": "2018-03-22T21:40:00.0000000Z",
                        "impactMitigationTime": "2018-03-22T21:41:00.0000000Z",
                        "impactedServices": "[{"ImpactedRegions"}]",
                        "defaultLanguageTitle": "…",
                        "defaultLanguageContent": "…",
                        "stage": "Active",
                        "communicationId": "11000001466525",
                        "version": "0.1.1"
                    },
                    "status": "Active",
                    "subscriptionId": "…",
                    "submissionTimestamp": "2018-04-03T22:44:50.8013523+00:00"
                }
            },
            "properties": {}
        }
    }
    ```

3.  Шаги 9–10 не отличаются.
4.  С шага 11 используйте процесс, описанный ниже.
5.  Нажмите кнопку **+ Новый шаг** и выберите **Добавить условие**. Задайте приведенные ниже условия, чтобы убедиться, что приложение логики выполняется, только когда входные данные соответствует этим значениям.
    - schemaId == Microsoft.Insights/activityLogs
    - eventSource == ServiceHealth
    - version == 0.1.1

    !["Условие полезных данных о работоспособности служб"](media/monitoring-action-groups/service-health-payload-condition.png "Service health payload condition")

6. В условие **Если истинно** добавьте действие Microsoft Teams, выполнив шаги 11–13 из предыдущего примера.

7. Определите сообщение, используя сочетание кода HTML и [динамического содержимого]. Скопируйте содержимое, приведенное ниже, и вставьте его в поле сообщения. Замените поля [incidentType], [trackingID], [title] и [communication] тегами динамического содержимого с такими же именами.

    ```html
    <p>
    <b>Alert Type:</b>&nbsp;<strong>[incidentType]</strong>&nbsp;
    <strong>Tracking ID:</strong>&nbsp;[trackingId]&nbsp;
    <strong>Title:</strong>&nbsp;[title]</p>
    <p>
    <a href="https://ms.portal.azure.com/#blade/Microsoft_Azure_Health/AzureHealthBrowseBlade/serviceIssues">For details log into the Azure Service Health dashboard</a>
    </p>
    <p>[communication]</p>
    ```

    !["Действие публикации для условия true оповещения о работоспособности служб"](media/monitoring-action-groups/service-health-true-condition-post-action.png "Service health true condition post action")

8. Для условия **Если ложно** введите имеющее смысл сообщение.

    ```html
    <p><strong>Service Health Alert</strong></p>
    <p><b>Unrecognized alert schema</b></p>
    <p><a href="https://ms.portal.azure.com/#blade/Microsoft_Azure_Health/AzureHealthBrowseBlade/serviceIssues">For details log into the Azure Service Health dashboard\</a></p>
    ```

    !["Действие публикации для условия false оповещения о работоспособности служб"](media/monitoring-action-groups/service-health-false-condition-post-action.png "Service Health false condition post action")

9.  Выполните действия 15–16 из предыдущего примера, чтобы сохранить приложение логики и обновить группу действий.

## <a name="metric-alert"></a>Оповещение метрики

1.  Шаги с 1 по 7 остаются такими же, как и в первом примере.
2.  Используйте приведенный пример схемы для триггера HTTP на шаге 8.

    ```json
    {
    "schemaId": "AzureMonitorMetricAlert",
    "data": {
        "version": "2.0",
        "status": "Activated",
        "context": {
        "timestamp": "2018-04-09T19:00:07.7461615Z",
        "id": "…",
        "name": "TEST-VM CPU Utilization",
        "description": "",
        "conditionType": "SingleResourceMultipleMetricCriteria",
        "condition": {
            "windowSize": "PT15M",
            "allOf": [
                {
                    "metricName": "Percentage CPU",
                    "dimensions": [
                    {
                        "name": "ResourceId",
                        "value": "d92fc5cb-06cf-4309-8c9a-538eea6a17a6"
                    }
                ],
                "operator": "GreaterThan",
                "threshold": "5",
                "timeAggregation": "PT15M",
                "metricValue": 1.0
            }
            ]
        },
        "subscriptionId": "…",
        "resourceGroupName": "TEST",
        "resourceName": "test-vm",
        "resourceType": "Microsoft.Compute/virtualMachines",
        "resourceId": "…",
        "portalLink": "…"
        },
        "properties": {}
    }
    }
    ```

3.  Шаги 9–10 не отличаются.
4.  С шага 11 используйте процесс, описанный ниже.
5.  Нажмите кнопку **+ Новый шаг** и выберите **Добавить условие**. Задайте приведенные ниже условия, чтобы убедиться, что приложение логики выполняется, только когда входные данные соответствует этим значениям.

    - schemaId == AzureMonitorMetricAlert
    - version == 2.0

    !["Условие для полезных данных оповещения метрики"](media/monitoring-action-groups/metric-alert-payload-condition.png "Metric alert payload condition")

6.  В условие **Если истинно** мы добавим фигуру **For each** и действие Microsoft Teams, а также определим сообщение, используя сочетание кода HTML и [динамического содержимого].

    !["Действие публикации для условия true оповещения метрики"](media/monitoring-action-groups/metric-alert-true-condition-post-action.png "Metric alert true condition post action")

7.  В фигуре **Если ложно** определите действие Microsoft Teams, которое сообщает, что оповещение метрики не соответствует ожиданиям приложения логики и включает в себя полезные данные JSON. Обратите внимание на то, как указываются ссылки на динамическое содержимое triggerBody в выражении json().

    !["Действие публикации для условия false оповещения метрики"](media/monitoring-action-groups/metric-alert-false-condition-post-action.png "Metric alert false condition post action")

8.  Выполните действия 15–16 из первого примера, чтобы сохранить приложение логики и обновить группу действий.

## <a name="next-steps"></a>Дополнительная информация ##
* Изучите [обзор оповещений журнала действий](monitoring-overview-alerts.md) и узнайте, как получать оповещения.  
* Узнайте, как [настроить оповещения при поступлении уведомлений о работоспособности службы](monitoring-activity-log-alerts-on-service-notifications.md).
* Узнайте больше о [группах действий](monitoring-action-groups.md).
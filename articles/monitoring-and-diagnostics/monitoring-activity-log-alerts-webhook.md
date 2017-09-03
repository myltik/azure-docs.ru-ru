---
title: "Общие сведения о схеме веб-перехватчика, используемой в оповещениях журнала действий | Документация Майкрософт"
description: "Дополнительные сведения о схеме JSON, отправляемой по URL-адресу веб-перехватчика при активации оповещения журнала действий."
author: johnkemnetz
manager: orenr
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/31/2017
ms.author: johnkem
ms.translationtype: HT
ms.sourcegitcommit: 25e4506cc2331ee016b8b365c2e1677424cf4992
ms.openlocfilehash: 75c71bcd16573d4f4dd3377c623aa9b414aa3906
ms.contentlocale: ru-ru
ms.lasthandoff: 08/24/2017

---
# <a name="webhooks-for-azure-activity-log-alerts"></a>Веб-перехватчики для оповещений журнала действий Azure
В определении группы действий можно настроить конечные точки веб-перехватчика для получения уведомлений об оповещениях журнала действий. С помощью веб-перехватчика можно направлять эти уведомления в другие системы для последующей обработки или выполнения настраиваемых действий. В этой статье показано, как выглядят полезные данные HTTP POST для webhook.

Дополнительные сведения об оповещениях журнала действий см. в статье [Создание оповещений журнала действий](monitoring-activity-log-alerts.md).

Сведения о группах действия см. в разделе [Создание групп действий и управление ими на портале Azure](monitoring-action-groups.md).

## <a name="authenticate-the-webhook"></a>Аутентификация веб-перехватчика
При необходимости веб-перехватчик может использовать авторизацию на основе токенов для аутентификации. URI веб-перехватчика сохраняется вместе с идентификатором токена, например `https://mysamplealert/webcallback?tokenid=sometokenid&someparameter=somevalue`.

## <a name="payload-schema"></a>Схема полезных данных
Полезные данные JSON, содержащихся в операции POST, могут быть различны в зависимости от поля data.context.activityLog.eventSource.

###<a name="common"></a>Common
```json
{
    "schemaId": "Microsoft.Insights/activityLogs",
    "data": {
        "status": "Activated",
        "context": {
            "activityLog": {
                "channels": "Operation",
                "correlationId": "6ac88262-43be-4adf-a11c-bd2179852898",
                "eventSource": "Administrative",
                "eventTimestamp": "2017-03-29T15:43:08.0019532+00:00",
                "eventDataId": "8195a56a-85de-4663-943e-1a2bf401ad94",
                "level": "Informational",
                "operationName": "Microsoft.Insights/actionGroups/write",
                "operationId": "6ac88262-43be-4adf-a11c-bd2179852898",
                "status": "Started",
                "subStatus": "",
                "subscriptionId": "52c65f65-0518-4d37-9719-7dbbfc68c57a",
                "submissionTimestamp": "2017-03-29T15:43:20.3863637+00:00",
                ...
            }
        },
        "properties": {}
    }
}
```
###<a name="administrative"></a>Administrative
```json
{
    "schemaId": "Microsoft.Insights/activityLogs",
    "data": {
        "status": "Activated",
        "context": {
            "activityLog": {
                "authorization": {
                    "action": "Microsoft.Insights/actionGroups/write",
                    "scope": "/subscriptions/52c65f65-0518-4d37-9719-7dbbfc68c57b/resourceGroups/CONTOSO-TEST/providers/Microsoft.Insights/actionGroups/IncidentActions"
                },
                "claims": "{...}",
                "caller": "me@contoso.com",
                "description": "",
                "httpRequest": "{...}",
                "resourceId": "/subscriptions/52c65f65-0518-4d37-9719-7dbbfc68c57b/resourceGroups/CONTOSO-TEST/providers/Microsoft.Insights/actionGroups/IncidentActions",
                "resourceGroupName": "CONTOSO-TEST",
                "resourceProviderName": "Microsoft.Insights",
                "resourceType": "Microsoft.Insights/actionGroups"
            }
        },
        "properties": {}
    }
}

```
###<a name="servicehealth"></a>ServiceHealth
```json
{
    "schemaId": "unknown",
    "data": {
        "status": "Activated",
        "context": {
            "activityLog": {
                "properties": {
                    "title": "...",
                    "service": "...",
                    "region": "...",
                    "communication": "...",
                    "incidentType": "Incident",
                    "trackingId": "...",
                    "groupId": "...",
                    "impactStartTime": "3/29/2017 3:43:21 PM",
                    "impactMitigationTime": "3/29/2017 3:43:21 PM",
                    "eventCreationTime": "3/29/2017 3:43:21 PM",
                    "impactedServices": "[{...}]",
                    "defaultLanguageTitle": "...",
                    "defaultLanguageContent": "...",
                    "stage": "Active",
                    "communicationId": "...",
                    "version": "0.1"
                }
            }
        },
        "properties": {}
    }
}
```

Сведения о конкретной схеме оповещений журнала действий для уведомлений о работоспособности службы см. в статье [Уведомления о работоспособности службы](monitoring-service-notifications.md).

Сведения о схеме для остальных оповещений журнала действий см. в статье [Мониторинг действий подписки с помощью журнала действий Azure](monitoring-overview-activity-logs.md).

| Имя элемента | Описание |
| --- | --- |
| status |Используется для оповещений на основе метрик. Всегда имеет значение activated для оповещений журнала действий. |
| context |Контекст события. |
| resourceProviderName |Поставщик ресурсов для затронутого ресурса. |
| conditionType |Всегда имеет значение Event. |
| name |Имя правила генерации оповещений. |
| id |Идентификатор ресурса для оповещения. |
| Описание |Описание оповещения, которое задается при его создании. |
| subscriptionId |Идентификатор подписки Azure. |
| Timestamp |Время создания события службой Azure, которая обработала запрос. |
| resourceId |Идентификатор ресурса для затронутого ресурса. |
| имя_группы_ресурсов |Имя группы ресурсов для затронутого ресурса. |
| properties |Набор пар `<Key, Value>` (например, `Dictionary<String, String>`), содержащий сведения о событии. |
| event |Элемент, содержащий метаданные о событии. |
| authorization |Свойства управления доступом на основе ролей для события. Обычно к ним относятся action, role и scope. |
| category |Категория события. Поддерживаются следующие значения: Administrative, Alert, Security, ServiceHealth, Recommendation. |
| caller |Адрес электронной почты пользователя, который выполнил операцию, утверждение имени субъекта-службы или имени участника-пользователя в зависимости от доступности. Может иметь значение NULL для определенных системных вызовов. |
| correlationId |Обычно GUID в строковом формате. События с correlationId относятся к одному крупному действию и обычно совместно используют correlationId. |
| eventDescription |Статическое описание события в текстовом виде. |
| eventDataId |Уникальный идентификатор события. |
| eventSource |Имя инфраструктуры или службы Azure, которая создала событие. |
| httpRequest |Обычно запрос содержит clientRequestId, clientIpAddress и method (метод HTTP, например PUT). |
| level |Одно из следующих значений: Critical, Error, Warning, Informational или Verbose. |
| operationId |Обычно события, относящиеся к одной операции, совместно используют один GUID. |
| operationName |Имя операции. |
| properties |Свойства события. |
| status |Строка. Состояние операции. Обычные значения: Started, In Progress, Succeeded, Failed, Active, Resolved. |
| subStatus |Обычно содержит код состояния HTTP для соответствующего вызова REST. Может также включать другие строки, описывающие подсостояние. Обычные значения подсостояния: OK (код состояния HTTP: 200), Created (код состояния HTTP: 201), Accepted (код состояния HTTP: 202), No Content (код состояния HTTP: 204), Bad Request (код состояния HTTP: 400), Not Found (код состояния HTTP: 404), Conflict (код состояния HTTP: 409), Internal Server Error (код состояния HTTP: 500), Service Unavailable (код состояния HTTP: 503), Gateway Timeout (код состояния HTTP: 504). |

## <a name="next-steps"></a>Дальнейшие действия
* [Мониторинг действий подписки с помощью журнала действий Azure](monitoring-overview-activity-logs.md).
* [Using Azure Automation to take action on Azure Alerts](http://go.microsoft.com/fwlink/?LinkId=627081) (Использование службы автоматизации Azure для выполнения действий по уведомлениям Azure).
* [Logic app that sends a text message when an alert fires](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-text-message-with-logic-app) (Приложение логики, которое отправляет текстовое сообщение при возникновении предупреждения). Это пример для оповещений на основе метрик, но его можно изменить для работы с оповещениями журнала действий.
* [Logic app that posts a message to a slack channel when an alert fires](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-slack-with-logic-app) (Приложение логики, которое отправляет сообщение в канал Slack при возникновении предупреждения). Это пример для оповещений на основе метрик, но его можно изменить для работы с оповещениями журнала действий.
* [Logic app that adds an item to a queue when an alert fires](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-queue-with-logic-app) (Приложение логики, добавляющее элемент в очередь при возникновении предупреждения). Это пример для оповещений на основе метрик, но его можно изменить для работы с оповещениями журнала действий.


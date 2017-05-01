# <a name="webhooks-for-azure-activity-log-alerts"></a>Объекты webhook для оповещений журнала действий Azure
В определении группы действий можно настроить конечные точки webhook для получения уведомлений об оповещениях журнала действий. Объекты webhook позволяют направлять эти уведомления в другие системы для последующей обработки или выполнения настраиваемых действий. В этой статье показано, как выглядят полезные данные HTTP POST для webhook.

Дополнительные сведения о настройке и схеме для оповещений журнала действий Azure приведены [на этой странице](monitoring-activity-log-alerts.md).

Дополнительные сведения о настройке и схеме для групп действий приведены [на этой странице](monitoring-action-groups.md).

## <a name="authenticating-the-webhook"></a>Проверка подлинности объекта webhook
Для аутентификации webhook может использоваться маркер. Универсальный код ресурса (URI) webhook сохраняется вместе с идентификатором маркера. Пример: `https://mysamplealert/webcallback?tokenid=sometokenid&someparameter=somevalue`.

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
                },
            }
        },
        "properties": {}
    }
}
```

Чтобы получить сведения о конкретной схеме для оповещений журнала активности о работоспособности службы, [щелкните здесь](monitoring-service-notifications.md). Чтобы получить сведения о схеме для всех прочих оповещениях журнала активности, [щелкните здесь](monitoring-overview-activity-logs.md).

| Имя элемента | Описание |
| --- | --- |
| status |Используется для оповещений на основе метрик. Всегда имеет значение activated для оповещений журнала действий. |
| context |Контекст события. |
| resourceProviderName |Поставщик ресурсов для затронутого ресурса. |
| conditionType |Всегда имеет значение Event. |
| name |Имя правила генерации оповещений. |
| id |Идентификатор ресурса для оповещения. |
| Описание |Описание оповещения, заданное во время создания оповещения. |
| subscriptionId |Идентификатор подписки Azure. |
| Timestamp |Время создания события службой Azure, которая обработала запрос. |
| resourceId |Идентификатор ресурса для затронутого ресурса. |
| имя_группы_ресурсов |Имя группы ресурсов для затронутого ресурса. |
| properties |Набор пар `<Key, Value>` (например, `Dictionary<String, String>`), содержащий сведения о событии. |
| event |Элемент, содержащий метаданные о событии. |
| authorization |Свойства RBAC события. Обычно к ним относятся action, role и scope. |
| category |Категория события. Поддерживаются следующие значения: Administrative, Alert, Security, ServiceHealth, Recommendation. |
| caller |Адрес электронной почты пользователя, который выполнил операцию, утверждение имени субъекта-службы или имени участника-пользователя в зависимости от доступности. Может иметь значение NULL для определенных системных вызовов. |
| correlationId |Обычно GUID в строковом формате. События с correlationId относятся к одному крупному действию и обычно совместно используют correlationId. |
| eventDescription |Статическое описание события в текстовом виде. |
| eventDataId |Уникальный идентификатор события. |
| eventSource |Имя инфраструктуры или службы Azure, которая создала событие. |
| httpRequest |Обычно запрос содержит clientRequestId, clientIpAddress и method (метод HTTP, например PUT). |
| уровень |Одно из следующих значений: Critical, Error, Warning, Informational или Verbose. |
| operationId |Обычно события, относящиеся к одной операции, совместно используют один GUID. |
| operationName |Имя операции. |
| properties |Свойства события. |
| status |Строка. Состояние операции. Обычные значения: Started, In Progress, Succeeded, Failed, Active, Resolved. |
| subStatus |Обычно содержит код состояния HTTP для соответствующего вызова REST. Может также включать другие строки, описывающие подсостояние. Обычные значения подсостояния: OK (код состояния HTTP: 200), Created (код состояния HTTP: 201), Accepted (код состояния HTTP: 202), No Content (код состояния HTTP: 204), Bad Request (код состояния HTTP: 400), Not Found (код состояния HTTP: 404), Conflict (код состояния HTTP: 409), Internal Server Error (код состояния HTTP: 500), Service Unavailable (код состояния HTTP: 503), Gateway Timeout (код состояния HTTP: 504). |

## <a name="next-steps"></a>Дальнейшие действия
* [См. дополнительные сведения о журнале действий](monitoring-overview-activity-logs.md)
* [Выполнение скриптов службы автоматизации Azure (Runbooks) на основе оповещений Azure](http://go.microsoft.com/fwlink/?LinkId=627081)
* [Использование приложения логики для отправки SMS с помощью Twilio из оповещения Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-text-message-with-logic-app). Это пример для оповещений на основе метрик, но его можно изменить для работы с оповещениями журнала действий.
* [Использование приложения логики для отправки сообщений Slack из оповещения Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-slack-with-logic-app). Это пример для оповещений на основе метрик, но его можно изменить для работы с оповещениями журнала действий.
* [Использование приложения логики для отправки сообщений в очередь Azure из оповещения Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-queue-with-logic-app). Это пример для оповещений на основе метрик, но его можно изменить для работы с оповещениями журнала действий.

---
title: "Схема событий журнала действий Azure | Документация Майкрософт"
description: "Общие сведения о схеме событий для данных, генерируемых в журнал действий"
author: johnkemnetz
manager: robb
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/20/2017
ms.author: johnkem
ms.translationtype: HT
ms.sourcegitcommit: bfd49ea68c597b109a2c6823b7a8115608fa26c3
ms.openlocfilehash: 5c96a1cfa56d1535549cb15d5a7bcf03bd11e723
ms.contentlocale: ru-ru
ms.lasthandoff: 07/25/2017

---
# <a name="azure-activity-log-event-schema"></a>Схема событий журнала действий Azure
**Журнал действий Azure** — это журнал с подробными сведениями о событиях на уровне подписки, которые произошли в Azure. В этой статье описывается схема событий по категориям данных.

## <a name="administrative"></a>Administrative
Эта категория содержит записи всех операций создания, обновления, удаления и других действий, которые выполняются через Resource Manager. В качестве примеров типов событий, которые относятся к этой категории, можно назвать "создание виртуальной машины" или "удаление группы безопасности сети". Каждое действие, выполняемое пользователем или приложением с помощью Resource Manager, моделируется как операция с определенным типом ресурсов. Если тип операции — "запись", "удаление" или "действие", то любые сведения об этой операции (о ее запуске, успешном выполнении или сбое) записываются в категорию "Административная". Категория "Административная" также включает в себя любые изменения в управлении доступом на основе ролей, которые происходят в подписке.

### <a name="sample-event"></a>Пример события
```json
{
  "authorization": {
    "action": "microsoft.support/supporttickets/write",
    "role": "Subscription Admin",
    "scope": "/subscriptions/s1/resourceGroups/MSSupportGroup/providers/microsoft.support/supporttickets/115012112305841"
  },
  "caller": "admin@contoso.com",
  "channels": "Operation",
  "claims": {
    "aud": "https://management.core.windows.net/",
    "iss": "https://sts.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db47/",
    "iat": "1421876371",
    "nbf": "1421876371",
    "exp": "1421880271",
    "ver": "1.0",
    "http://schemas.microsoft.com/identity/claims/tenantid": "1e8d8218-c5e7-4578-9acc-9abbd5d23315 ",
    "http://schemas.microsoft.com/claims/authnmethodsreferences": "pwd",
    "http://schemas.microsoft.com/identity/claims/objectidentifier": "2468adf0-8211-44e3-95xq-85137af64708",
    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "admin@contoso.com",
    "puid": "20030000801A118C",
    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier": "9vckmEGF7zDKk1YzIY8k0t1_EAPaXoeHyPRn6f413zM",
    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname": "John",
    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname": "Smith",
    "name": "John Smith",
    "groups": "cacfe77c-e058-4712-83qw-f9b08849fd60,7f71d11d-4c41-4b23-99d2-d32ce7aa621c,31522864-0578-4ea0-9gdc-e66cc564d18c",
    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name": " admin@contoso.com",
    "appid": "c44b4083-3bq0-49c1-b47d-974e53cbdf3c",
    "appidacr": "2",
    "http://schemas.microsoft.com/identity/claims/scope": "user_impersonation",
    "http://schemas.microsoft.com/claims/authnclassreference": "1"
  },
  "correlationId": "1e121103-0ba6-4300-ac9d-952bb5d0c80f",
  "description": "",
  "eventDataId": "44ade6b4-3813-45e6-ae27-7420a95fa2f8",
  "eventName": {
    "value": "EndRequest",
    "localizedValue": "End request"
  },
  "httpRequest": {
    "clientRequestId": "27003b25-91d3-418f-8eb1-29e537dcb249",
    "clientIpAddress": "192.168.35.115",
    "method": "PUT"
  },
  "id": "/subscriptions/s1/resourceGroups/MSSupportGroup/providers/microsoft.support/supporttickets/115012112305841/events/44ade6b4-3813-45e6-ae27-7420a95fa2f8/ticks/635574752669792776",
  "level": "Informational",
  "resourceGroupName": "MSSupportGroup",
  "resourceProviderName": {
    "value": "microsoft.support",
    "localizedValue": "microsoft.support"
  },
  "resourceUri": "/subscriptions/s1/resourceGroups/MSSupportGroup/providers/microsoft.support/supporttickets/115012112305841",
  "operationId": "1e121103-0ba6-4300-ac9d-952bb5d0c80f",
  "operationName": {
    "value": "microsoft.support/supporttickets/write",
    "localizedValue": "microsoft.support/supporttickets/write"
  },
  "properties": {
    "statusCode": "Created"
  },
  "status": {
    "value": "Succeeded",
    "localizedValue": "Succeeded"
  },
  "subStatus": {
    "value": "Created",
    "localizedValue": "Created (HTTP Status Code: 201)"
  },
  "eventTimestamp": "2015-01-21T22:14:26.9792776Z",
  "submissionTimestamp": "2015-01-21T22:14:39.9936304Z",
  "subscriptionId": "s1"
}
```

### <a name="property-descriptions"></a>Описания свойств
| Имя элемента | Description (Описание) |
| --- | --- |
| authorization |BLOB-объект со свойствами RBAC события. Обычно включает следующие свойства: action, role и scope. |
| caller |Адрес электронной почты пользователя, который выполнил операцию, утверждение имени субъекта-службы или имени участника-пользователя в зависимости от доступности. |
| каналов |Одно из следующих значений: Admin или Operation. |
| claims |Токен JWT, который используется Active Directory для аутентификации пользователя или приложения при выполнении этой операции в Resource Manager. |
| correlationId |Обычно GUID в строковом формате. События, которые совместно используют идентификатор correlationId, принадлежат к одному общему действию. |
| Description (Описание) |Статическое описание события в текстовом виде. |
| eventDataId |Уникальный идентификатор события. |
| httpRequest |Большой двоичный объект, описывающий HTTP-запрос. Обычно включает clientRequestId, clientIpAddress и method (метод HTTP, например PUT). |
| уровень |Уровень события. Одно из следующих значений: "Critical", "Error", "Warning", "Informational" или "Verbose" |
| имя_группы_ресурсов |Имя группы ресурсов для затронутого ресурса. |
| resourceProviderName |Имя поставщика ресурса для затронутого ресурса. |
| resourceId |Идентификатор ресурса для затронутого ресурса. |
| operationId |События, относящиеся к одной операции, совместно используют один GUID. |
| operationName |Имя операции. |
| properties |Набор пар `<Key, Value>` (например, Dictionary) c подробным описанием события. |
| status |Строка, описывающая состояние операции. Обычные значения: Started, In Progress, Succeeded, Failed, Active, Resolved. |
| subStatus |Обычно содержит код состояния HTTP для соответствующего вызова REST, но может содержать и другие строковые описания подсостояния, например: OK (код состояния HTTP: 200); Created (код состояния HTTP: 201); Accepted (код состояния HTTP: 202); No Content (код состояния HTTP: 204); Bad Request (код состояния HTTP: 400); Not Found (код состояния HTTP: 404); Conflict (код состояния HTTP: 409); Internal Server Error (код состояния HTTP: 500); Service Unavailable (код состояния HTTP: 503); Gateway Timeout (код состояния HTTP: 504). |
| eventTimestamp |Метка времени, когда служба Azure создала событие при обработке соответствующего этому событию запроса. |
| submissionTimestamp |Метка времени, когда событие стало доступно для запросов. |
| subscriptionId |Идентификатор подписки Azure. |

## <a name="service-health"></a>Работоспособность службы
Эта категория содержит записи всех инцидентов, связанных с работоспособностью службы, которые произошли в Azure. В качестве примера типа события из этой категории можно назвать следующее: "В работе SQL Azure в восточной части США наблюдаются простои". Существует шесть разновидностей событий работоспособности службы: "Требуется действие", "Помощь при восстановлении", "Инцидент", "Обслуживание", "Сведения" и "Безопасность". Они отображаются, только если в подписке есть ресурс, на который повлияет данное событие.

### <a name="sample-event"></a>Пример события
```json
{
  "channels": "Admin",
  "correlationId": "c550176b-8f52-4380-bdc5-36c1b59d3a44",
  "description": "Active: Network Infrastructure - UK South",
  "eventDataId": "c5bc4514-6642-2be3-453e-c6a67841b073",
  "eventName": {
      "value": null
  },
  "category": {
      "value": "ServiceHealth",
      "localizedValue": "Service Health"
  },
  "eventTimestamp": "2017-07-20T23:30:14.8022297Z",
  "id": "/subscriptions/mySubscriptionID/events/c5bc4514-6642-2be3-453e-c6a67841b073/ticks/636361902148022297",
  "level": "Warning",
  "operationName": {
      "value": "Microsoft.ServiceHealth/incident/action",
      "localizedValue": "Microsoft.ServiceHealth/incident/action"
  },
  "resourceProviderName": {
      "value": null
  },
  "resourceType": {
      "value": null,
      "localizedValue": ""
  },
  "resourceId": "/subscriptions/mySubscriptionID",
  "status": {
      "value": "Active",
      "localizedValue": "Active"
  },
  "subStatus": {
      "value": null
  },
  "submissionTimestamp": "2017-07-20T23:30:34.7431946Z",
  "subscriptionId": "mySubscriptionID",
  "properties": {
    "title": "Network Infrastructure - UK South",
    "service": "Service Fabric",
    "region": "UK South",
    "communication": "Starting at approximately 21:41 UTC on 20 Jul 2017, a subset of customers in UK South may experience degraded performance, connectivity drops or timeouts when accessing their Azure resources hosted in this region. Engineers are investigating underlying Network Infrastructure issues in this region. Impacted services may include, but are not limited to App Services, Automation, Service Bus, Log Analytics, Key Vault, SQL Database, Service Fabric, Event Hubs, Stream Analytics, Azure Data Movement, API Management, and Azure Search. Multiple engineering teams are engaged in multiple workflows to mitigate the impact. The next update will be provided in 60 minutes, or as events warrant.",
    "incidentType": "Incident",
    "trackingId": "NA0F-BJG",
    "impactStartTime": "2017-07-20T21:41:00.0000000Z",
    "impactedServices": "[{\"ImpactedRegions\":[{\"RegionName\":\"UK South\"}],\"ServiceName\":\"Service Fabric\"}]",
    "defaultLanguageTitle": "Network Infrastructure - UK South",
    "defaultLanguageContent": "Starting at approximately 21:41 UTC on 20 Jul 2017, a subset of customers in UK South may experience degraded performance, connectivity drops or timeouts when accessing their Azure resources hosted in this region. Engineers are investigating underlying Network Infrastructure issues in this region. Impacted services may include, but are not limited to App Services, Automation, Service Bus, Log Analytics, Key Vault, SQL Database, Service Fabric, Event Hubs, Stream Analytics, Azure Data Movement, API Management, and Azure Search. Multiple engineering teams are engaged in multiple workflows to mitigate the impact. The next update will be provided in 60 minutes, or as events warrant.",
    "stage": "Active",
    "communicationId": "636361902146035247",
    "version": "0.1.1"
  }
}
```

### <a name="property-descriptions"></a>Описания свойств
Имя элемента | Описание
-------- | -----------
каналов | Одно из следующих значений: Admin или Operation.
correlationId | Обычно GUID в строковом формате. События, относящиеся к одному действию uber, обычно имеют общее значение correlationId.
Описание | Описание события.
eventDataId | Уникальный идентификатор события.
eventName | Заголовок события.
level | Уровень события. Одно из следующих значений: "Critical", "Error", "Warning", "Informational" или "Verbose"
resourceProviderName | Имя поставщика ресурсов для затронутого ресурса. Если неизвестно, то значение будет null.
тип_ресурса| Тип затронутого ресурса. Если неизвестно, то значение будет null.
subStatus | Обычно для событий работоспособности службы имеет значение null.
eventTimestamp | Метка времени, когда событие журнала было создано и отправлено в журнал действий.
submissionTimestamp |   Метка времени, когда событие стало доступным в журнале действий.
subscriptionId | Подписка Azure, в которой это событие было занесено в журнал.
status | Строка, описывающая состояние операции. Распространенные значения: Active (Активно), Resolved (Разрешено).
operationName | Имя операции. Обычно имеет значение Microsoft.ServiceHealth/incident/action.
category | ServiceHealth
resourceId | Идентификатор ресурса для затронутого ресурса (если он известен). В противном случае указывается идентификатор подписки.
Properties.title | Локализованное название этого сообщения. По умолчанию используется английский язык.
Properties.communication | Локализованные сведения сообщения с разметкой HTML. По умолчанию используется английский язык.
Properties.incidentType | Возможные значения: AssistedRecovery, ActionRequired, Information, Incident, Maintenance, Security.
Properties.trackingId | Идентифицирует инцидент, с которым связано это событие. Используйте его для сопоставления событий, связанных с инцидентом.
Properties.impactedServices | Экранированный большой двоичный объект в формате JSON, описывающий службы и регионы, на которые влияет инцидент. Список служб Services, каждая из которых содержит ServiceName, и список регионов ImpactedRegions, каждый из которых содержит RegionName.
Properties.defaultLanguageTitle | Сообщение на английском языке.
Properties.defaultLanguageContent | Сообщение на английском языке с разметкой HTML или в виде обычного текста.
Properties.stage | Возможные значения для AssistedRecovery, ActionRequired, Information, Incident, Security: Active, Resolved. Возможные значения для Maintenance: Active, Planned, InProgress, Canceled, Rescheduled, Resolved, Complete.
Properties.communicationId | Сообщение, с которым связано это событие.

## <a name="alert"></a>Предупреждение
Эта категория содержит записи всех активаций оповещений Azure. В качестве примера типа события из этой категории можно назвать следующее: "Процент использования ЦП на виртуальной машине myVM за последние 5 минут превышал 80 %". Различные системы Azure работают по принципу оповещений, то есть вы можете определить какое-то правило, и система будет направлять вам уведомление, когда условия этого правила выполняются. Каждый раз, когда "активируется" поддерживаемый тип оповещения Azure или выполняются условия для создания уведомления, в эту категорию журнала активности также передается запись об активации.

### <a name="sample-event"></a>Пример события

```json
{
  "caller": "Microsoft.Insights/alertRules",
  "channels": "Admin, Operation",
  "claims": {
    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/spn": "Microsoft.Insights/alertRules"
  },
  "correlationId": "/subscriptions/mySubscriptionID/resourceGroups/myResourceGroup/providers/microsoft.insights/alertrules/myalert/incidents/L3N1YnNjcmlwdGlvbnMvZGY2MDJjOWMtN2FhMC00MDdkLWE2ZmItZWIyMGM4YmQxMTkyL3Jlc291cmNlR3JvdXBzL0NzbUV2ZW50RE9HRk9PRC1XZXN0VVMvcHJvdmlkZXJzL21pY3Jvc29mdC5pbnNpZ2h0cy9hbGVydHJ1bGVzL215YWxlcnQwNjM2MzYyMjU4NTM1MjIxOTIw",
  "description": "'Disk read LessThan 100000 ([Count]) in the last 5 minutes' has been resolved for CloudService: myResourceGroup/Production/Event.BackgroundJobsWorker.razzle (myResourceGroup)",
  "eventDataId": "149d4baf-53dc-4cf4-9e29-17de37405cd9",
  "eventName": {
    "value": "Alert",
    "localizedValue": "Alert"
  },
  "category": {
    "value": "Alert",
    "localizedValue": "Alert"
  },
  "id": "/subscriptions/mySubscriptionID/resourceGroups/myResourceGroup/providers/Microsoft.ClassicCompute/domainNames/myResourceGroup/slots/Production/roles/Event.BackgroundJobsWorker.razzle/events/149d4baf-53dc-4cf4-9e29-17de37405cd9/ticks/636362258535221920",
  "level": "Informational",
  "resourceGroupName": "myResourceGroup",
  "resourceProviderName": {
    "value": "Microsoft.ClassicCompute",
    "localizedValue": "Microsoft.ClassicCompute"
  },
  "resourceId": "/subscriptions/mySubscriptionID/resourceGroups/myResourceGroup/providers/Microsoft.ClassicCompute/domainNames/myResourceGroup/slots/Production/roles/Event.BackgroundJobsWorker.razzle",
  "resourceType": {
    "value": "Microsoft.ClassicCompute/domainNames/slots/roles",
    "localizedValue": "Microsoft.ClassicCompute/domainNames/slots/roles"
  },
  "operationId": "/subscriptions/mySubscriptionID/resourceGroups/myResourceGroup/providers/microsoft.insights/alertrules/myalert/incidents/L3N1YnNjcmlwdGlvbnMvZGY2MDJjOWMtN2FhMC00MDdkLWE2ZmItZWIyMGM4YmQxMTkyL3Jlc291cmNlR3JvdXBzL0NzbUV2ZW50RE9HRk9PRC1XZXN0VVMvcHJvdmlkZXJzL21pY3Jvc29mdC5pbnNpZ2h0cy9hbGVydHJ1bGVzL215YWxlcnQwNjM2MzYyMjU4NTM1MjIxOTIw",
  "operationName": {
    "value": "Microsoft.Insights/AlertRules/Resolved/Action",
    "localizedValue": "Microsoft.Insights/AlertRules/Resolved/Action"
  },
  "properties": {
    "RuleUri": "/subscriptions/mySubscriptionID/resourceGroups/myResourceGroup/providers/microsoft.insights/alertrules/myalert",
    "RuleName": "myalert",
    "RuleDescription": "",
    "Threshold": "100000",
    "WindowSizeInMinutes": "5",
    "Aggregation": "Average",
    "Operator": "LessThan",
    "MetricName": "Disk read",
    "MetricUnit": "Count"
  },
  "status": {
    "value": "Resolved",
    "localizedValue": "Resolved"
  },
  "subStatus": {
    "value": null
  },
  "eventTimestamp": "2017-07-21T09:24:13.522192Z",
  "submissionTimestamp": "2017-07-21T09:24:15.6578651Z",
  "subscriptionId": "mySubscriptionID"
}
```

### <a name="property-descriptions"></a>Описания свойств
| Имя элемента | Описание |
| --- | --- |
| caller | Всегда имеет значение Microsoft.Insights/alertRules. |
| каналов | Всегда имеет значение "Admin, Operation". |
| claims | Большой двоичный объект JSON с именем субъекта-службы (SPN) или типом ресурса обработчика предупреждений. |
| correlationId | Глобальный уникальный идентификатор (GUID) в строковом формате. |
| Описание |Статическое текстовое описание события оповещения. |
| eventDataId |Уникальный идентификатор события оповещения. |
| level |Уровень события. Одно из следующих значений: "Critical", "Error", "Warning", "Informational" или "Verbose" |
| имя_группы_ресурсов |Имя группы ресурсов для затронутого ресурса, если это оповещение метрики. Для других типов оповещений это имя группы ресурсов, содержащей само оповещение. |
| resourceProviderName |Имя поставщика ресурсов для затронутого ресурса, если это оповещение метрики. Для других типов оповещений это имя поставщика ресурсов для самого оповещения. |
| resourceId | Имя идентификатора ресурса для затронутого ресурса, если это оповещение метрики. Для других типов оповещений это идентификатор ресурса самого ресурса оповещения. |
| operationId |События, относящиеся к одной операции, совместно используют один GUID. |
| operationName |Имя операции. |
| properties |Набор пар `<Key, Value>` (например, Dictionary) c подробным описанием события. |
| status |Строка, описывающая состояние операции. Обычные значения: Started, In Progress, Succeeded, Failed, Active, Resolved. |
| subStatus | Обычно для оповещений имеет значение null. |
| eventTimestamp |Метка времени, когда служба Azure создала событие при обработке соответствующего этому событию запроса. |
| submissionTimestamp |Метка времени, когда событие стало доступно для запросов. |
| subscriptionId |Идентификатор подписки Azure. |

### <a name="properties-field-per-alert-type"></a>Поле свойств по типу оповещения
Поле свойств будет содержать разные значения в зависимости от источника события оповещения. Два распространенных поставщика событий оповещений — оповещения журнала действий и оповещения метрик.

#### <a name="properties-for-activity-log-alerts"></a>Свойства оповещений журнала действий
| Имя элемента | Описание |
| --- | --- |
| properties.subscriptionId | Идентификатор подписки из события журнала действий, которое вызвало активацию данного правила оповещения журнала действий. |
| properties.eventDataId | Идентификатор данных события из события журнала действий, которое вызвало активацию данного правила оповещения журнала действий. |
| properties.resourceGroup | Идентификатор группы ресурсов из события журнала действий, которое вызвало активацию данного правила оповещения журнала действий. |
| properties.resourceId | Идентификатор ресурса из события журнала действий, которое вызвало активацию данного правила оповещения журнала действий. |
| properties.eventTimestamp | Метка времени события из события журнала действий, которое вызвало активацию данного правила оповещения журнала действий. |
| properties.operationName | Имя операции из события журнала действий, которое вызвало активацию данного правила оповещения журнала действий. |
| properties.status | Состояние из события журнала действий, которое вызвало активацию данного правила оповещения журнала действий.|

#### <a name="properties-for-metric-alerts"></a>Свойства оповещений метрик
| Имя элемента | Описание |
| --- | --- |
| properties.RuleUri | Идентификатор ресурса самого правила оповещения метрики. |
| properties.RuleName | Имя правила оповещения метрики. |
| properties.RuleDescription | Описание правила оповещения метрики (как указано в правиле оповещения). |
| properties.Threshold | Пороговое значение, используемое для оценки правила оповещения метрики. |
| properties.WindowSizeInMinutes | Размер окна, используемый для оценки правила оповещения метрики. |
| properties.Aggregation | Тип агрегата, определенный в правиле оповещения метрики. |
| properties.Operator | Условный оператор, используемый для оценки правила оповещения метрики. |
| properties.MetricName | Имя метрики, используемой для оценки правила оповещения метрики. |
| properties.MetricUnit | Единица измерения метрики, используемая для оценки правила оповещения метрики. |

## <a name="autoscale"></a>Autoscale
Эта категория содержит записи всех событий, связанных с операциями системы автоматического масштабирования, в основе которой лежат параметры автомасштабирования, определенные в подписке. В качестве примера типа события из этой категории можно назвать следующее: "Не удалось выполнить автоматическое увеличение масштаба". С помощью функции автомасштабирования можно автоматически увеличивать или уменьшать число экземпляров в поддерживаемом типе ресурсов на основе времени суток и (или) загружать данные (метрики), используя параметр автомасштабирования. Когда выполняются условия для увеличения или уменьшения масштаба, в эту категорию записываются соответствующие события (запуск, успешное выполнение или сбой).

### <a name="sample-event"></a>Пример события
```json
{
  "caller": "Microsoft.Insights/autoscaleSettings",
  "channels": "Admin, Operation",
  "claims": {
    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/spn": "Microsoft.Insights/autoscaleSettings"
  },
  "correlationId": "fc6a7ff5-ff68-4bb7-81b4-3629212d03d0",
  "description": "The autoscale engine attempting to scale resource '/subscriptions/mySubscriptionID/resourceGroups/myResourceGroup/providers/Microsoft.ClassicCompute/domainNames/myResourceGroup/slots/Production/roles/myResource' from 3 instances count to 2 instances count.",
  "eventDataId": "a5b92075-1de9-42f1-b52e-6f3e4945a7c7",
  "eventName": {
    "value": "AutoscaleAction",
    "localizedValue": "AutoscaleAction"
  },
  "category": {
    "value": "Autoscale",
    "localizedValue": "Autoscale"
  },
  "id": "/subscriptions/mySubscriptionID/resourceGroups/myResourceGroup/providers/microsoft.insights/autoscalesettings/myResourceGroup-Production-myResource-myResourceGroup/events/a5b92075-1de9-42f1-b52e-6f3e4945a7c7/ticks/636361956518681572",
  "level": "Informational",
  "resourceGroupName": "myResourceGroup",
  "resourceProviderName": {
    "value": "microsoft.insights",
    "localizedValue": "microsoft.insights"
  },
  "resourceId": "/subscriptions/mySubscriptionID/resourceGroups/myResourceGroup/providers/microsoft.insights/autoscalesettings/myResourceGroup-Production-myResource-myResourceGroup",
  "resourceType": {
    "value": "microsoft.insights/autoscalesettings",
    "localizedValue": "microsoft.insights/autoscalesettings"
  },
  "operationId": "fc6a7ff5-ff68-4bb7-81b4-3629212d03d0",
  "operationName": {
    "value": "Microsoft.Insights/AutoscaleSettings/Scaledown/Action",
    "localizedValue": "Microsoft.Insights/AutoscaleSettings/Scaledown/Action"
  },
  "properties": {
    "Description": "The autoscale engine attempting to scale resource '/subscriptions/mySubscriptionID/resourceGroups/myResourceGroup/providers/Microsoft.ClassicCompute/domainNames/myResourceGroup/slots/Production/roles/myResource' from 3 instances count to 2 instances count.",
    "ResourceName": "/subscriptions/mySubscriptionID/resourceGroups/myResourceGroup/providers/Microsoft.ClassicCompute/domainNames/myResourceGroup/slots/Production/roles/myResource",
    "OldInstancesCount": "3",
    "NewInstancesCount": "2",
    "LastScaleActionTime": "Fri, 21 Jul 2017 01:00:51 GMT"
  },
  "status": {
    "value": "Succeeded",
    "localizedValue": "Succeeded"
  },
  "subStatus": {
    "value": null
  },
  "eventTimestamp": "2017-07-21T01:00:51.8681572Z",
  "submissionTimestamp": "2017-07-21T01:00:52.3008754Z",
  "subscriptionId": "mySubscriptionID"
}

```

### <a name="property-descriptions"></a>Описания свойств
| Имя элемента | Описание |
| --- | --- |
| caller | Всегда имеет значение Microsoft.Insights/autoscaleSettings. |
| каналов | Всегда имеет значение "Admin, Operation". |
| claims | Большой двоичный объект JSON с именем субъекта-службы (SPN) или типом ресурса системы автомасштабирования. |
| correlationId | Глобальный уникальный идентификатор (GUID) в строковом формате. |
| Описание |Статическое текстовое описание события автомасштабирования. |
| eventDataId |Уникальный идентификатор события автомасштабирования. |
| level |Уровень события. Одно из следующих значений: "Critical", "Error", "Warning", "Informational" или "Verbose" |
| имя_группы_ресурсов |Имя группы ресурсов для параметра автомасштабирования. |
| resourceProviderName |Имя поставщика ресурсов для параметра автомасштабирования. |
| resourceId |Идентификатор ресурса параметра автомасштабирования. |
| operationId |События, относящиеся к одной операции, совместно используют один GUID. |
| operationName |Имя операции. |
| properties |Набор пар `<Key, Value>` (например, Dictionary) c подробным описанием события. |
| properties.Description | Подробное описание действий, выполненных системой автоматического масштабирования. |
| properties.ResourceName | Идентификатор ресурса для затронутого ресурса (ресурса, в отношении которого выполнялось действие масштабирования). |
| properties.OldInstancesCount | Число экземпляров до выполнения действия автомасштабирования. |
| properties.NewInstancesCount | Число экземпляров после выполнения действия автомасштабирования. |
| properties.LastScaleActionTime | Метка времени, когда произошло действие автоматического масштабирования. |
| status |Строка, описывающая состояние операции. Обычные значения: Started, In Progress, Succeeded, Failed, Active, Resolved. |
| subStatus | Обычно для автоматического масштабирования имеет значение null. |
| eventTimestamp |Метка времени, когда служба Azure создала событие при обработке соответствующего этому событию запроса. |
| submissionTimestamp |Метка времени, когда событие стало доступно для запросов. |
| subscriptionId |Идентификатор подписки Azure. |

## <a name="next-steps"></a>Дальнейшие действия
* [Дополнительные сведения о журнале действий (прежнее название — журналы аудита)](monitoring-overview-activity-logs.md)
* [Потоковая передача журнала действий Azure в концентраторы событий](monitoring-stream-activity-logs-event-hubs.md)


.<properties
	pageTitle="Общие сведения о журнале действий Azure | Microsoft Azure"
	description="Узнайте, что такое журнал действий Azure и как его использовать для просмотра событий, происходящих в рамках подписки Azure."
	authors="johnkemnetz"
	manager="rboucher"
	editor=""
	services="monitoring-and-diagnostics"
	documentationCenter="monitoring-and-diagnostics"/>

.<tags
	ms.service="monitoring-and-diagnostics"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/17/2016"
	ms.author="johnkem"/>

# Общие сведения о журнале действий Azure
**Журнал действий Azure** — это журнал с информацией об операциях, которые выполнялись с ресурсами в подписке. Журнал действий раньше назывался журналом аудита или операционным журналамом, так как он содержит связанные с подписками события на уровне управления. С помощью журнала действий можно определить кто, что и когда делал в контексте любой операции записи (PUT, POST, DELETE), выполненной с ресурсами в вашей подписке; кроме того, можно узнать о состоянии этих операций и других важных свойствах. Журнал действий не включает операции чтения (GET).

Журнал действий отличается от [журналов диагностики](./monitoring-overview-of-diagnostic-logs.md), которые включают все журналы, генерируемые ресурсом. Эти журналы содержат данные об операциях самих ресурсов, а не об операциях с ресурсами.

События из журнала действий можно получить с помощью портала Azure, интерфейса командной строки, командлетов PowerShell или REST API Insights.

## Что можно делать с журналом действий
Ниже описано несколько доступных операций с журналом действий.

- Запросы и просмотры журнала на **портале Azure**.
- Обращение к журналу с помощью REST API, командлетов PowerShell или интерфейса командной строки.
- [Создание оповещений электронной почты или веб-перехватчика, активируемых событием журнала действий.](./insights-auditlog-to-webhook-email.md)
- Сохранение журнала в **учетную запись хранения** для архивации или проверки вручную. В **профилях журнала** вы также можете задать время хранения (в днях).
- Анализ журнала в PowerBI с помощью [**пакета содержимого PowerBI**](https://powerbi.microsoft.com/ru-RU/documentation/powerbi-content-pack-azure-audit-logs/).
- [Потоковая передача журнала в **концентратор событий**](./monitoring-stream-activity-logs-event-hubs.md) для обработки в сторонней службе или пользовательском аналитическом решении, например, PowerBI.

## Экспорт журнала действий с помощью профилей журнала
**Профиль журнала** определяет, как экспортируется журнал действий. С помощью профиля журнала вы можете настроить следующие параметры.

- Куда будет отправлен журнал действий (учетная запись хранения или концентраторы событий).
- Какие будут отправлены категории событий (запись, удаление, действие).
- Какие будут экспортированы регионы (расположения).
- Как долго будет храниться в учетной записи хранения журнал действий (если указать здесь 0 дней, журналы будут храниться неограниченно долго). Если политики хранения заданы, но хранение журналов в учетной записи отключено (например, выбраны только варианты концентраторов событий или OMS), политики хранения не будут применены.

Эти параметры можно настроить в параметре "Экспорт" в колонке "Журнал действий" на портале или программно [с помощью REST API](https://msdn.microsoft.com/library/azure/dn931927.aspx), командлетов PowerShell или интерфейса командной строки. Для каждой подписки может существовать только один профиль журнала.

### Настройка профилей журнала на портале Azure
С помощью параметра "Экспорт" на портале Azure можно выполнить потоковую передачу журнала действий в концентратор событий или сохранить журнал в учетную запись хранения.

1. Перейдите к колонке **Журнал действий** с помощью меню в левой части портала.

    .![Переход к журналу действий на портале](./media/monitoring-overview-activity-logs/activity-logs-portal-navigate.png)
2. Нажмите кнопку **Экспорт** в верхней части колонки.

    .![Кнопка экспорта на портале](./media/monitoring-overview-activity-logs/activity-logs-portal-export.png)
3. Откроется колонка, в которой вы можете выбрать следующие элементы: регионы, связанные с экспортируемыми событиями; учетную запись хранения для хранения событий (а также количество дней, в течение которых эти события будут храниться); пространство имен служебной шины, в котором будет создан концентратор событий для потоковой передачи этих событий.

    .![Колонка экспорта журнала действий](./media/monitoring-overview-activity-logs/activity-logs-portal-export-blade.png)
4. Нажмите кнопку **Сохранить**, чтобы сохранить эти параметры. Параметры будут немедленно применены к подписке

### Настройка профилей журнала с помощью командлетов Azure PowerShell
#### Получение существующего профиля журнала
```
Get-AzureRmLogProfile
```

#### Добавление профиля журнала
```
Add-AzureRmLogProfile -Name my_log_profile -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey -Locations global,westus,eastus -RetentionInDays 90 -Categories Write,Delete,Action
```

| Свойство | Обязательно | Description (Описание) |
|------------------|----------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Name (Имя) | Да | Имя профиля журнала. |
| StorageAccountId | Нет | Идентификатор ресурса для учетной записи хранения, в которую будет сохранен журнал действий. |
| serviceBusRuleId | Нет | Идентификатор правила служебной шины для пространства имен служебной шины, в котором вы будут созданы концентраторы событий. Это строка в таком формате: `{service bus resource ID}/authorizationrules/{key name}`. |
| Расположения | Да | Разделенный запятыми список регионов, для которых будут собираться события журнала действий. |
| RetentionInDays | Да | Количество дней, в течение которых будут храниться события. Нулевое значение означает, что журналы будут храниться неограниченно долго. |
| Категории | Нет | Разделенный запятыми список категорий событий, которые будут собираться. Возможные значения: Write, Delete или Action. |

#### Удаление профиля журнала
```
Remove-AzureRmLogProfile -name my_log_profile
```

### Настройка профилей журнала с помощью кроссплатформенного интерфейса командной строки
#### Получение существующего профиля журнала
```
azure insights logprofile list
```
```
azure insights logprofile get --name my_log_profile
```
Свойство `name` должно содержать имя профиля журнала.

#### Добавление профиля журнала
``` 
azure insights logprofile add --name my_log_profile --storageId /subscriptions/s1/resourceGroups/insights-integration/providers/Microsoft.Storage/storageAccounts/my_storage --serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey --locations global,westus,eastus,northeurope --retentionInDays 90 –categories Write,Delete,Action
```

| Свойство | Обязательно | Описание |
|------------------|----------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| name | Да | Имя профиля журнала. |
| storageId | Нет | Идентификатор ресурса для учетной записи хранения, в которую будет сохранен журнал действий. |
| serviceBusRuleId | Нет | Идентификатор правила служебной шины для пространства имен служебной шины, в котором вы будут созданы концентраторы событий. Это строка в таком формате: `{service bus resource ID}/authorizationrules/{key name}`. |
| locations | Да | Разделенный запятыми список регионов, для которых будут собираться события журнала действий. |
| retentionInDays | Да | Количество дней, в течение которых будут храниться события. Нулевое значение означает, что журналы хранятся неограниченно долго. |
| categories | Нет | Разделенный запятыми список категорий событий, которые будут собираться. Возможные значения: Write, Delete или Action. |

#### Удаление профиля журнала
```
azure insights logprofile delete --name my_log_profile
```

## Схема событий
Для каждого события в журнале действий создается большой двоичный объект JSON следующего вида.

```
{
  "value": [ {
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
    "eventSource": {
      "value": "Microsoft.Resources",
      "localizedValue": "Microsoft Resources"
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
  } ],
"nextLink": "https://management.azure.com/########-####-####-####-############$skiptoken=######"
}
```

| Имя элемента | Описание |
|----------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| authorization | BLOB-объект со свойствами RBAC события. Обычно включает следующие свойства: action, role и scope. |
| caller | Адрес электронной почты пользователя, который выполнил операцию, утверждение имени субъекта-службы или имени участника-пользователя в зависимости от доступности. |
| каналов | Одно из следующих значений: Admin или Operation. |
| correlationId | Обычно GUID в строковом формате. События, которые совместно используют идентификатор correlationId, принадлежат к одному общему действию. |
| description | Статическое описание события в текстовом виде. |
| eventDataId | Уникальный идентификатор события. |
| eventSource | Имя инфраструктуры или службы Azure, которая создала событие. |
| httpRequest | Большой двоичный объект, описывающий HTTP-запрос. Обычно включает "clientRequestId", "clientIpAddress" и "method" (метод HTTP, например PUT). |
| level | Уровень события. Одно из следующих значений: "Critical", "Error", "Warning", "Informational" или "Verbose" |
| имя\_группы\_ресурсов | Имя группы ресурсов для затронутого ресурса. |
| resourceProviderName | Имя поставщика ресурса для затронутого ресурса. |
| resourceUri | Идентификатор ресурса для затронутого ресурса. |
| operationId | События, относящиеся к одной операции, совместно используют один GUID. |
| operationName | Имя операции. |
| properties | Набор пар `<Key, Value>` (например, Dictionary) c подробным описанием события. |
| status | Строка, описывающая состояние операции. Обычные значения: Started, In Progress, Succeeded, Failed, Active, Resolved. |
| subStatus | Обычно содержит код состояния HTTP для соответствующего вызова REST, но может содержать и другие строковые описания подсостояния, например: OK (код состояния HTTP: 200); Created (код состояния HTTP: 201); Accepted (код состояния HTTP: 202); No Content (код состояния HTTP: 204); Bad Request (код состояния HTTP: 400); Not Found (код состояния HTTP: 404); Conflict (код состояния HTTP: 409); Internal Server Error (код состояния HTTP: 500); Service Unavailable (код состояния HTTP: 503); Gateway Timeout (код состояния HTTP: 504). |
| eventTimestamp | Метка времени, когда служба Azure создала событие при обработке соответствующего этому событию запроса. |
| submissionTimestamp | Метка времени, когда событие стало доступно для запросов. |
| subscriptionId | Идентификатор подписки Azure. |
| nextLink | Маркер продолжения для получения следующего набора результатов, если результаты разделены на несколько ответов. Обычно это происходит, если передается более 200 записей. |

## Дальнейшие действия
- [Дополнительные сведения о журнале действий (прежнее название — журналы аудита)](../resource-group-audit.md)
- [Потоковая передача журнала действий Azure в концентраторы событий](./monitoring-stream-activity-logs-event-hubs.md)

<!---HONumber=AcomDC_0817_2016-->
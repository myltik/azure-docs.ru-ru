---
title: "Общие сведения о журнале действий Azure | Документация Майкрософт"
description: "Узнайте, что такое журнал действий Azure и как его использовать для просмотра событий, происходящих в рамках подписки Azure."
author: johnkemnetz
manager: rboucher
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: c274782f-039d-4c28-9ddb-f89ce21052c7
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 2/2/2017
ms.author: johnkem
translationtype: Human Translation
ms.sourcegitcommit: 97edd5eaa3cfa4a122556583dff28c4a9b6f5adc
ms.openlocfilehash: 18035fe2a30707f701098cef4b1391b1d5ab2012


---
# <a name="overview-of-the-azure-activity-log"></a>Общие сведения о журнале действий Azure
**Журнал действий Azure** — это журнал с информацией об операциях, которые выполнялись с ресурсами в подписке. Журнал действий раньше назывался журналом аудита или операционным журналамом, так как он содержит связанные с подписками события на уровне управления. С помощью журнала изменений можно ответить на вопросы "что? кто? когда?" о любой операции записи (PUT, POST, DELETE) с ресурсами в вашей подписке. Вы также можете отслеживать состояние операции и другие ее свойства. Журнал действий не содержит операции чтения (GET) или операции с ресурсами, которые используют классическую модель или модель RDFE.

Журнал действий отличается от [журналов диагностики](monitoring-overview-of-diagnostic-logs.md), которые включают все журналы, генерируемые ресурсом. Эти журналы содержат данные об операциях самих ресурсов, а не об операциях с ресурсами.

События из журнала изменений можно получить с помощью портала Azure, интерфейса командной строки, командлетов PowerShell или REST API Azure Monitor.

Просмотрите [ознакомительное видео о журнале изменений](https://channel9.msdn.com/Blogs/Seth-Juarez/Logs-John-Kemnetz).  

> [!WARNING]
> Журнал действий Azure используется главным образом для действий, которые происходят в Azure Resource Manager, а не которые используют классическую модель или модель RDFE. Обратите внимание, что для некоторых классических типов ресурсов в Azure Resource Manager имеется прокси-поставщик ресурсов например, Microsoft.ClassicCompute). Если пользователь взаимодействует с ресурсом классического типа через Azure Resource Manager с помощью этих прокси-поставщиков ресурсов, то операции будут отображены в журнале действий. Если пользователь взаимодействует с ресурсом классического типа на классическом портале или иным образом вне прокси Azure Resource Manager, то действия пользователя записываются только в журнал операций, доступный исключительно на классическом портале.
>
>

## <a name="what-you-can-do-with-the-activity-log"></a>Что можно делать с журналом действий
Ниже описано несколько доступных операций с журналом действий.

* Запросы и просмотры журнала на **портале Azure**.
* Обращение к журналу с помощью REST API, командлетов PowerShell или интерфейса командной строки.
* [Создание оповещений электронной почты или веб-перехватчика, активируемых событием журнала действий.](insights-auditlog-to-webhook-email.md)
* [Сохранение журнала в **учетную запись хранения** для архивации или проверки вручную](monitoring-archive-activity-log.md). В **профилях журнала**вы также можете задать время хранения (в днях).
* Анализ журнала в PowerBI с помощью [**пакета содержимого PowerBI**](https://powerbi.microsoft.com/en-us/documentation/powerbi-content-pack-azure-audit-logs/).
* [Потоковая передача журнала в **концентратор событий**](monitoring-stream-activity-logs-event-hubs.md) для обработки в сторонней службе или пользовательском аналитическом решении, например PowerBI.

Учетная запись хранения или пространство имен концентратора событий не обязательно должны находиться в той самой подписке, в которой создаются журналы, если у пользователя, настраивающего параметр, имеется соответствующий доступ RBAC к обеим подпискам.

## <a name="export-the-activity-log-with-log-profiles"></a>Экспорт журнала действий с помощью профилей журнала
**Профиль журнала** определяет, как экспортируется журнал действий. С помощью профиля журнала вы можете настроить следующие параметры.

* Куда будет отправлен журнал действий (учетная запись хранения или концентраторы событий).
* Какие категории событий будут отправляться (запись, удаление, действие). *Обратите внимание, что значение "категории" в контексте профиля журнала отличается от значения свойства category в событии журнала действий. "Категория" в профиле журнала обозначает тип операции (запись, удаление, действие), а свойство category в событии журнала действий представляет источник или тип события (Administration, ServiceHealth, Alert и т. д.).*
* Какие будут экспортированы регионы (расположения).
* Как долго будет храниться в учетной записи хранения журнал действий (если указать здесь&0; дней, журналы будут храниться неограниченно долго). В противном случае укажите количество дней в диапазоне от 1 до 2 147 483 647. Если политики хранения заданы, но хранение журналов в учетной записи хранения отключено (например, выбраны только варианты концентраторов событий или OMS), политики хранения не будут применены. Политики хранения применяются по дням, поэтому в конце дня (по времени в формате UTC) журналы, срок которых теперь превышает период хранения, будут удалены. Например, если настроена политика хранения в течение одного дня, то в начале текущего дня журналы за вчерашний день будет удалены.

Эти параметры можно настроить с помощью параметра "Экспорт" в колонке журнала изменений на портале. Их также можно настроить программно [с помощью REST API Azure Monitor](https://msdn.microsoft.com/library/azure/dn931927.aspx), командлетов PowerShell или интерфейса командной строки. Для каждой подписки может существовать только один профиль журнала.

### <a name="configure-log-profiles-using-the-azure-portal"></a>Настройка профилей журнала на портале Azure
С помощью параметра "Экспорт" на портале Azure можно выполнить потоковую передачу журнала действий в концентратор событий или сохранить журнал в учетную запись хранения.

1. Перейдите к колонке **Журнал действий** с помощью меню в левой части портала.
   
    ![Переход к журналу действий на портале](./media/monitoring-overview-activity-logs/activity-logs-portal-navigate.png)
2. Нажмите кнопку **Экспорт** в верхней части колонки.
   
    ![Кнопка экспорта на портале](./media/monitoring-overview-activity-logs/activity-logs-portal-export.png)
3. В появившейся колонке вы можете выбрать следующие параметры:  
   
   * регионы, события которых нужно экспортировать;
   * учетная запись хранения, в которой вы хотите сохранить события;
   * сколько дней следует хранить эти события в хранилище (если выбрать значение "0 дней", журналы будут храниться бессрочно);
   * пространство имен служебной шины, в котором следует создать концентратор событий для потоковой передачи этих событий.
     
     ![Колонка экспорта журнала действий](./media/monitoring-overview-activity-logs/activity-logs-portal-export-blade.png)
4. Нажмите кнопку **Сохранить** , чтобы сохранить эти параметры. Параметры будут немедленно применены к подписке

### <a name="configure-log-profiles-using-the-azure-powershell-cmdlets"></a>Настройка профилей журнала с помощью командлетов Azure PowerShell
#### <a name="get-existing-log-profile"></a>Получение существующего профиля журнала
```
Get-AzureRmLogProfile
```

#### <a name="add-a-log-profile"></a>Добавление профиля журнала
```
Add-AzureRmLogProfile -Name my_log_profile -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey -Locations global,westus,eastus -RetentionInDays 90 -Categories Write,Delete,Action
```

| Свойство | Обязательно | Description (Описание) |
| --- | --- | --- |
| Name (Имя) |Да |Имя профиля журнала. |
| StorageAccountId |Нет |Идентификатор ресурса для учетной записи хранения, в которую будет сохранен журнал действий. |
| serviceBusRuleId |Нет |Идентификатор правила служебной шины для пространства имен служебной шины, в котором вы будут созданы концентраторы событий. Это строка в таком формате: `{service bus resource ID}/authorizationrules/{key name}`. |
| Расположения |Да |Разделенный запятыми список регионов, для которых будут собираться события журнала действий. |
| RetentionInDays |Да |Количество дней, в течение которых будут храниться события: от 1 до 2 147 483 647. Нулевое значение означает, что журналы хранятся неограниченно долго, то есть всегда. |
| Категории |Нет |Разделенный запятыми список категорий событий, которые будут собираться. Возможные значения: Write, Delete или Action. |

#### <a name="remove-a-log-profile"></a>Удаление профиля журнала
```
Remove-AzureRmLogProfile -name my_log_profile
```

### <a name="configure-log-profiles-using-the-azure-cross-platform-cli"></a>Настройка профилей журнала с помощью кроссплатформенного интерфейса командной строки
#### <a name="get-existing-log-profile"></a>Получение существующего профиля журнала
```
azure insights logprofile list
```
```
azure insights logprofile get --name my_log_profile
```
Свойство `name` должно содержать имя профиля журнала.

#### <a name="add-a-log-profile"></a>Добавление профиля журнала
```
azure insights logprofile add --name my_log_profile --storageId /subscriptions/s1/resourceGroups/insights-integration/providers/Microsoft.Storage/storageAccounts/my_storage --serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey --locations global,westus,eastus,northeurope --retentionInDays 90 –categories Write,Delete,Action
```

| Свойство | Обязательно | Description (Описание) |
| --- | --- | --- |
| name |Да |Имя профиля журнала. |
| storageId |Нет |Идентификатор ресурса для учетной записи хранения, в которую будет сохранен журнал действий. |
| serviceBusRuleId |Нет |Идентификатор правила служебной шины для пространства имен служебной шины, в котором вы будут созданы концентраторы событий. Это строка в таком формате: `{service bus resource ID}/authorizationrules/{key name}`. |
| locations |Да |Разделенный запятыми список регионов, для которых будут собираться события журнала действий. |
| RetentionInDays |Да |Количество дней, в течение которых будут храниться события: от 1 до 2 147 483 647. Нулевое значение означает, что журналы хранятся неограниченно долго, то есть всегда. |
| Категории |Нет |Разделенный запятыми список категорий событий, которые будут собираться. Возможные значения: Write, Delete или Action. |

#### <a name="remove-a-log-profile"></a>Удаление профиля журнала
```
azure insights logprofile delete --name my_log_profile
```

## <a name="event-schema"></a>Схема событий
Для каждого события в журнале изменений создается большой двоичный объект JSON, как в этом примере:

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

| Имя элемента | Description (Описание) |
| --- | --- |
| authorization |BLOB-объект со свойствами RBAC события. Обычно включает следующие свойства: action, role и scope. |
| caller |Адрес электронной почты пользователя, который выполнил операцию, утверждение имени субъекта-службы или имени участника-пользователя в зависимости от доступности. |
| каналов |Одно из следующих значений: Admin или Operation. |
| correlationId |Обычно GUID в строковом формате. События, которые совместно используют идентификатор correlationId, принадлежат к одному общему действию. |
| Description (Описание) |Статическое описание события в текстовом виде. |
| eventDataId |Уникальный идентификатор события. |
| eventSource |Имя инфраструктуры или службы Azure, которая создала событие. |
| httpRequest |Большой двоичный объект, описывающий HTTP-запрос. Обычно включает clientRequestId, clientIpAddress и method (метод HTTP, например PUT). |
| уровень |Уровень события. Одно из следующих значений: "Critical", "Error", "Warning", "Informational" или "Verbose" |
| имя_группы_ресурсов |Имя группы ресурсов для затронутого ресурса. |
| resourceProviderName |Имя поставщика ресурса для затронутого ресурса. |
| resourceUri |Идентификатор ресурса для затронутого ресурса. |
| operationId |События, относящиеся к одной операции, совместно используют один GUID. |
| operationName |Имя операции. |
| properties |Набор пар `<Key, Value>` (например, Dictionary) c подробным описанием события. |
| status |Строка, описывающая состояние операции. Обычные значения: Started, In Progress, Succeeded, Failed, Active, Resolved. |
| subStatus |Обычно содержит код состояния HTTP для соответствующего вызова REST, но может содержать и другие строковые описания подсостояния, например: OK (код состояния HTTP: 200); Created (код состояния HTTP: 201); Accepted (код состояния HTTP: 202); No Content (код состояния HTTP: 204); Bad Request (код состояния HTTP: 400); Not Found (код состояния HTTP: 404); Conflict (код состояния HTTP: 409); Internal Server Error (код состояния HTTP: 500); Service Unavailable (код состояния HTTP: 503); Gateway Timeout (код состояния HTTP: 504). |
| eventTimestamp |Метка времени, когда служба Azure создала событие при обработке соответствующего этому событию запроса. |
| submissionTimestamp |Метка времени, когда событие стало доступно для запросов. |
| subscriptionId |Идентификатор подписки Azure. |
| nextLink |Маркер продолжения для получения следующего набора результатов, если результаты разделены на несколько ответов. Обычно маркер необходим при наличии более чем 200 записей. |

## <a name="next-steps"></a>Дальнейшие действия
* [Дополнительные сведения о журнале действий (прежнее название — журналы аудита)](../azure-resource-manager/resource-group-audit.md)
* [Потоковая передача журнала действий Azure в концентраторы событий](monitoring-stream-activity-logs-event-hubs.md)




<!--HONumber=Feb17_HO1-->



---
title: Архивация журнала действий Azure | Документация Майкрософт
description: Узнайте, как настроить архивацию журнала действий Azure для долгосрочного хранения в учетной записи хранения.
author: johnkemnetz
manager: orenr
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: d37d3fda-8ef1-477c-a360-a855b418de84
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/09/2016
ms.author: johnkem
ms.openlocfilehash: 6020272d79ace55041da94ee45165e557e92b80f
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/06/2018
---
# <a name="archive-the-azure-activity-log"></a>Архивация журнала действий Azure
В этой статье описано, как настроить архивацию [**журнала действий Azure**](monitoring-overview-activity-logs.md) в учетной записи хранения с помощью портала Azure, командлетов PowerShell или кроссплатформенного интерфейса командной строки. Архивацию целесообразно применять, если вам нужно хранить данные журнала действий дольше 90 дней (с полным контролем над политикой хранения) для аудита, статического анализа или резервного копирования. Если вам требуется хранить события в течение не более 90 дней, не нужно настраивать архивацию в учетную запись хранения, так как события журнала действий можно хранить в течение этого периода на платформе Azure.

## <a name="prerequisites"></a>Предварительные требования
Прежде чем начать, необходимо [создать учетную запись хранения](../storage/common/storage-create-storage-account.md#create-a-storage-account) для архивации данных журнала действий. Настоятельно рекомендуется не использовать имеющуюся учетную запись хранения, в которой содержатся другие данные (не данные мониторинга), чтобы лучше контролировать доступ к данным мониторинга. Но если вы также архивируете в учетную запись хранения журналы диагностики и метрики, целесообразно использовать эту учетную запись и для хранения данных журнала действий. Так все данные мониторинга будут храниться в одном расположении. Используйте учетную запись хранения общего назначения, а не учетную запись хранения больших двоичных объектов. Учетная запись хранения не обязательно должна находиться в той самой подписке, в которой создаются журналы, если у пользователя, настраивающего параметр, имеется соответствующий доступ RBAC к обеим подпискам.

## <a name="log-profile"></a>Профиль журнала
Чтобы настроить архивацию журнала действий с помощью одного из описанных здесь методов, необходимо настроить **профиль журнала** для подписки. Профиль журнала определяет тип событий, которые хранятся или передаются потоком, и выходных данных — учетная запись хранения и/или концентратор событий. Он также определяет политику хранения событий (число дней), содержащихся в учетной записи хранения. Если для политики хранения задано значение 0, события хранятся неограниченное время. Или вы можете присвоить любое значение от 1 до 2 147 483 647. Политики хранения применяются по дням, поэтому в конце дня (по времени в формате UTC) журналы, срок которых теперь превышает период хранения, будут удалены. Например, если настроена политика хранения в течение одного дня, то в начале текущего дня журналы за вчерашний день будет удалены. Дополнительные сведения о профилях журнала см. [здесь](monitoring-overview-activity-logs.md#export-the-activity-log-with-a-log-profile). 

## <a name="archive-the-activity-log-using-the-portal"></a>Архивация журнала действий с помощью портала
1. На портале щелкните ссылку **Журнал действий** на панели навигации слева. Если вы не видите эту ссылку, сначала щелкните ссылку **Все службы**.
   
    ![Переход к колонке журнала действий](media/monitoring-archive-activity-log/act-log-portal-navigate.png)
2. В верхней части колонки щелкните **Экспорт**.
   
    ![Нажатие кнопки "Экспорт"](media/monitoring-archive-activity-log/act-log-portal-export-button.png)
3. В открывшейся колонке установите флажок **Export to a storage account** (Экспортировать в учетную запись хранения) и выберите учетную запись хранения.
   
    ![Настройка учетной записи хранения](media/monitoring-archive-activity-log/act-log-portal-export-blade.png)
4. С помощью ползунка или текстового поля задайте число дней, в течение которых необходимо хранить события журнала действий в учетной записи хранения. Если требуется, чтобы данные хранились в учетной записи хранения бесконечно, задайте значение 0.
5. Выберите команду **Сохранить**.

## <a name="archive-the-activity-log-via-powershell"></a>Архивация журнала действий с помощью PowerShell

   ```powershell
   # Settings needed for the new log profile
   $logProfileName = "default"
   $locations = (Get-AzureRmLocation).Location
   $locations += "global"
   $subscriptionId = "<your Azure subscription Id>"
   $resourceGroupName = "<resource group name your storage account belongs to>"
   $storageAccountName = "<your storage account name>"

   # Build the storage account Id from the settings above
   $storageAccountId = "/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Storage/storageAccounts/$storageAccountName"

   Add-AzureRmLogProfile -Name $logProfileName -Location $locations -StorageAccountId $storageAccountId
   ```

| Свойство | Обязательно | ОПИСАНИЕ |
| --- | --- | --- |
| StorageAccountId |Yes |Идентификатор ресурса для учетной записи хранения, в которую будут сохранены журналы действий. |
| Расположения |Yes |Разделенный запятыми список регионов, для которых будут собираться события журнала действий. Вы можете просмотреть список всех регионов для своей подписки с помощью `(Get-AzureRmLocation).Location`. |
| RetentionInDays |Нет  |Количество дней, в течение которых будут храниться события: от 1 до 2 147 483 647. Нулевое значение означает, что журналы хранятся неограниченно долго, то есть всегда. |
| Категории |Нет  |Разделенный запятыми список категорий событий, которые будут собираться. Возможные значения: Write, Delete или Action.  Если значение не указано, предполагается наличие всех возможных значений. |

## <a name="archive-the-activity-log-via-cli"></a>Архивация журнала действий с помощью интерфейса командной строки

   ```azurecli-interactive
   az monitor log-profiles create --name "default" --location null --locations "global" "eastus" "westus" --categories "Delete" "Write" "Action"  --enabled false --days 0 --storage-account-id "/subscriptions/<YOUR SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.Storage/storageAccounts/<STORAGE ACCOUNT NAME>"
   ```

| Свойство | Обязательно | ОПИСАНИЕ |
| --- | --- | --- |
| name |Yes |Имя профиля журнала. |
| storage-account-id |Yes |Идентификатор ресурса для учетной записи хранения, в которую будут сохранены журналы действий. |
| Расположения |Yes |Разделенный пробелами список регионов, для которых будут собираться события журнала действий. Вы можете просмотреть список всех регионов для своей подписки с помощью `az account list-locations --query [].name`. |
| days |Yes |Количество дней, в течение которых будут храниться события: от 1 до 2 147 483 647. Нулевое значение означает, что журналы будут храниться неограниченно долго, то есть всегда.  Если значение равно нулю, для включенного параметра нужно установить значение true. |
|Включено | Yes |Значение True или False.  Позволяет включить или отключить политику хранения.  Если установлено значение True, параметр дней должен иметь значение больше 0.
| Категории |Yes |Разделенный пробелами список категорий событий, которые будут собираться. Возможные значения: Write, Delete или Action. |

## <a name="storage-schema-of-the-activity-log"></a>Схема хранения журнала действий
После настройки архивации в учетной записи хранения будет создан контейнер хранилища, как только возникнет событие журнала. Большие двоичные объекты, содержащиеся в контейнере, имеют одинаковый формат в журналах действий и диагностики. Вот как выглядит структура большого двоичного объекта:

> insights-operational-logs/name=default/resourceId=/SUBSCRIPTIONS/{ИД подписки}/y={год в четырехзначном формате}/m={месяц в двухзначном формате}/d={день в двухзначном формате}/h={время в двухзначном 24-часовом формате}/m=00/PT1H.json
> 
> 

Например, большой двоичный объект может иметь такое имя:

> insights-operational-logs/name=default/resourceId=/SUBSCRIPTIONS/s1id1234-5679-0123-4567-890123456789/y=2016/m=08/d=22/h=18/m=00/PT1H.json
> 
> 

Каждый большой двоичный объект PT1H.json содержит большой двоичный объект JSON с событиями, произошедшими в течение часа, указанного в URL-адресе этого объекта (например, h=12). В течение заданного часа события добавляются в файл PT1H.json по мере возникновения. Значение минуты (m=00) всегда равно 00, так как события журнала действий разбиваются на отдельные большие двоичные объекты каждый час.

В файле PT1H.json каждое событие сохраняется в массиве records в следующем формате:

```
{
    "records": [
        {
            "time": "2015-01-21T22:14:26.9792776Z",
            "resourceId": "/subscriptions/s1/resourceGroups/MSSupportGroup/providers/microsoft.support/supporttickets/115012112305841",
            "operationName": "microsoft.support/supporttickets/write",
            "category": "Write",
            "resultType": "Success",
            "resultSignature": "Succeeded.Created",
            "durationMs": 2826,
            "callerIpAddress": "111.111.111.11",
            "correlationId": "c776f9f4-36e5-4e0e-809b-c9b3c3fb62a8",
            "identity": {
                "authorization": {
                    "scope": "/subscriptions/s1/resourceGroups/MSSupportGroup/providers/microsoft.support/supporttickets/115012112305841",
                    "action": "microsoft.support/supporttickets/write",
                    "evidence": {
                        "role": "Subscription Admin"
                    }
                },
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
                }
            },
            "level": "Information",
            "location": "global",
            "properties": {
                "statusCode": "Created",
                "serviceRequestId": "50d5cddb-8ca0-47ad-9b80-6cde2207f97c"
            }
        }
    ]
}
```


| Имя элемента | ОПИСАНИЕ |
| --- | --- |
| Twitter в режиме реального |Метка времени, когда служба Azure создала событие при обработке соответствующего этому событию запроса. |
| ResourceId |Идентификатор ресурса для затронутого ресурса. |
| operationName |Имя операции. |
| category |Категория действия, например "Запись", "Чтение", "Действие" |
| resultType |Тип результата, например "Успешно", "Ошибка", "Запуск" |
| resultSignature |Зависит от типа ресурса. |
| durationMs |Время выполнения операции в миллисекундах |
| callerIpAddress |IP-адрес пользователя, который выполнил операцию, утверждение имени субъекта-службы или имени участника-пользователя в зависимости от доступности. |
| correlationId |Обычно GUID в строковом формате. События, которые совместно используют идентификатор correlationId, принадлежат к одному общему действию. |
| identity |Большой двоичный объект JSON, описывающий авторизацию и утверждения. |
| authorization |BLOB-объект со свойствами RBAC события. Обычно включает следующие свойства: action, role и scope. |
| level |Уровень события. Одно из следующих значений: "Critical", "Error", "Warning", "Informational" или "Verbose" |
| location |Регион, к которому принадлежит расположение (или глобальное местоположение). |
| properties |Набор пар `<Key, Value>` (например, Dictionary) c подробным описанием события. |

> [!NOTE]
> Свойства и их использование зависят от ресурса.
> 
> 

## <a name="next-steps"></a>Дополнительная информация
* [Скачивание больших двоичных объектов для анализа](../storage/blobs/storage-quickstart-blobs-dotnet.md)
* [Потоковая передача журнала действий в концентраторы событий](monitoring-stream-activity-logs-event-hubs.md)
* [Дополнительные сведения о журнале действий](monitoring-overview-activity-logs.md)


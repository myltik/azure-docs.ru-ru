<properties
	pageTitle="Архивация журнала действий Azure | Microsoft Azure"
	description="Узнайте, как настроить архивацию журнала действий Azure для долгосрочного хранения в учетной записи хранения."
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
	ms.date="08/23/2016"
	ms.author="johnkem"/>

# Архивация журнала действий Azure
В этой статье описано, как настроить архивацию [**журнала действий Azure**](monitoring-overview-activity-logs.md) в учетной записи хранения с помощью портала Azure, командлетов PowerShell или кроссплатформенного интерфейса командной строки. Архивацию целесообразно применять, если вам нужно хранить данные журнала действий дольше 90 дней (с полным контролем над политикой хранения) для аудита, статического анализа или резервного копирования. Если вам требуется хранить события в течение не более 90 дней, не нужно настраивать архивацию в учетную запись хранения, так как события журнала действий можно хранить в течение этого периода на платформе Azure.

## Предварительные требования
Прежде чем начать, необходимо [создать учетную запись хранения](../storage/storage-create-storage-account.md#create-a-storage-account) для архивации данных журнала действий. Настоятельно рекомендуется не использовать имеющуюся учетную запись хранения, в которой содержатся другие данные (не данные мониторинга), чтобы лучше контролировать доступ к данным мониторинга. Но если вы также архивируете в учетную запись хранения журналы диагностики и метрики, целесообразно использовать эту учетную запись и для хранения данных журнала действий. Так все данные мониторинга будут храниться в одном расположении. Используйте учетную запись хранения общего назначения, а не учетную запись хранения больших двоичных объектов.

## Профиль журнала
Чтобы настроить архивацию журнала действий с помощью одного из описанных здесь методов, необходимо настроить **профиль журнала** для подписки. Профиль журнала определяет тип событий, которые хранятся или передаются потоком, и выходных данных — учетная запись хранения и/или концентратор событий. Он также определяет политику хранения событий (число дней), содержащихся в учетной записи хранения. Если для политики хранения задано значение 0, события хранятся неограниченное время. Или вы можете присвоить любое значение от 1 до 2 147 483 647. Дополнительные сведения о профилях журнала см. [здесь](monitoring-overview-activity-logs.md#export-the-activity-log-with-log-profiles).

## Архивация журнала действий с помощью портала
1. На портале щелкните ссылку **Журнал действий** на панели навигации слева. Если вы не видите эту ссылку, сначала щелкните ссылку **Другие службы**.

    ![Переход к колонке журнала действий](media/monitoring-archive-activity-log/act-log-portal-navigate.png)
2. В верхней части колонки щелкните **Экспорт**.

    ![Нажатие кнопки "Экспорт"](media/monitoring-archive-activity-log/act-log-portal-export-button.png)
3. В открывшейся колонке установите флажок **Export to a storage account** (Экспортировать в учетную запись хранения) и выберите учетную запись хранения.

    ![Настройка учетной записи хранения](media/monitoring-archive-activity-log/act-log-portal-export-blade.png)
4. С помощью ползунка или текстового поля задайте число дней, в течение которых необходимо хранить события журнала действий в учетной записи хранения. Если требуется, чтобы данные хранились в учетной записи хранения бесконечно, задайте значение 0.
5. Щелкните **Сохранить**.

## Архивация журнала действий с помощью PowerShell
```
Add-AzureRmLogProfile -Name my_log_profile -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -Locations global,westus,eastus -RetentionInDays 180 -Categories Write,Delete,Action
```

| Свойство | Обязательно | Description (Описание) |
|------------------|----------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| StorageAccountId | Нет | Идентификатор ресурса для учетной записи хранения, в которую будут сохранены журналы действий. |
| Расположения | Да | Разделенный запятыми список регионов, для которых будут собираться события журнала действий. Список всех регионов можно получить, [посетив эту страницу](https://azure.microsoft.com/regions) или используя [REST API управления Azure](https://msdn.microsoft.com/library/azure/gg441293.aspx). |
| RetentionInDays | Да | Количество дней, в течение которых будут храниться события: от 1 до 2 147 483 647. Нулевое значение означает, что журналы хранятся неограниченно долго, то есть всегда. |
| Категории | Да | Разделенный запятыми список категорий событий, которые будут собираться. Возможные значения: Write, Delete или Action. |
## Архивация журнала действий с помощью интерфейса командной строки
```
azure insights logprofile add --name my_log_profile --storageId /subscriptions/s1/resourceGroups/insights-integration/providers/Microsoft.Storage/storageAccounts/my_storage --locations global,westus,eastus,northeurope --retentionInDays 180 –categories Write,Delete,Action
```

| Свойство | Обязательно | Описание |
|-----------------|----------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| name | Да | Имя профиля журнала. |
| storageId | Нет | Идентификатор ресурса для учетной записи хранения, в которую будут сохранены журналы действий. |
| locations | Да | Разделенный запятыми список регионов, для которых будут собираться события журнала действий. Список всех регионов можно получить, [посетив эту страницу](https://azure.microsoft.com/regions) или используя [REST API управления Azure](https://msdn.microsoft.com/library/azure/gg441293.aspx). |
| retentionInDays | Да | Количество дней, в течение которых будут храниться события: от 1 до 2 147 483 647. Нулевое значение означает, что журналы будут храниться неограниченно долго, то есть всегда. |
| categories | Да | Разделенный запятыми список категорий событий, которые будут собираться. Возможные значения: Write, Delete или Action. |

## Схема хранения журнала действий
После настройки архивации в учетной записи хранения будет создан контейнер хранилища, как только возникнет событие журнала. Большие двоичные объекты, содержащиеся в контейнере, имеют одинаковый формат в журналах действий и диагностики. Вот как выглядит структура большого двоичного объекта:

> insights-operational-logs/name=default/resourceId=/SUBSCRIPTIONS/{ИД подписки}/y={год в четырехзначном формате}/m={месяц в двухзначном формате}/d={день в двухзначном формате}/h={время в двухзначном 24-часовом формате}/m=00/PT1H.json

Например, большой двоичный объект может иметь такое имя:

> insights-operational-logs/name=default/resourceId=/SUBSCRIPTIONS/s1id1234-5679-0123-4567-890123456789/y=2016/m=08/d=22/h=18/m=00/PT1H.json

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


| Имя элемента | Описание |
|-----------------|----------------------------------------------------------------------------------------------------------------|
| Twitter в режиме реального | Метка времени, когда служба Azure создала событие при обработке соответствующего этому событию запроса. |
| resourceId | Идентификатор ресурса для затронутого ресурса. |
| operationName | Имя операции. |
| category | Категория действия, например Write, Read, Action. |
| resultType | Тип результата, например Success, Failure, Start. |
| resultSignature | Зависит от типа ресурса. |
| durationMs | Время выполнения операции в миллисекундах |
| callerIpAddress | IP-адрес пользователя, который выполнил операцию, утверждение имени субъекта-службы или имени участника-пользователя в зависимости от доступности. |
| correlationId | Обычно GUID в строковом формате. События, которые совместно используют идентификатор correlationId, принадлежат к одному общему действию. |
| identity | Большой двоичный объект JSON, описывающий авторизацию и утверждения. |
| authorization | BLOB-объект со свойствами RBAC события. Обычно включает следующие свойства: action, role и scope. |
| level | Уровень события. Одно из следующих значений: "Critical", "Error", "Warning", "Informational" или "Verbose" |
| location | Регион, к которому принадлежит расположение (или глобальное местоположение). |
| properties | Набор пар `<Key, Value>` (например, Dictionary) c подробным описанием события. |

> [AZURE.NOTE] Свойства и их использование зависят от ресурса.

## Дальнейшие действия
- [Скачивание больших двоичных объектов для анализа](../storage/storage-dotnet-how-to-use-blobs.md#download-blobs)
- [Потоковая передача журнала действий в концентраторы событий](monitoring-stream-activity-logs-event-hubs.md)
- [Дополнительные сведения о журнале действий](monitoring-overview-activity-logs.md)

<!---HONumber=AcomDC_0907_2016-->
<properties
	pageTitle="Настройка объекта webhook для оповещений журнала действий Azure | Microsoft Azure"
	description="Сведения об использовании оповещений журнала действий для вызова объектов webhook. "
	authors="kamathashwin"
	manager=""
	editor=""
	services="monitoring-and-diagnostics"
	documentationCenter="monitoring-and-diagnostics"/>

<tags
	ms.service="monitoring-and-diagnostics"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/15/2016"
	ms.author="ashwink"/>

# Настройка объектов webhook для оповещений журнала действий Azure

Объекты webhook позволяют направлять уведомления об оповещениях Azure в другие системы для постобработки или выполнения настраиваемых действий. Объект webhook можно использовать, чтобы направить оповещение к службам, которые отправляют SMS, ведут журналы об ошибках, уведомляют членов команды в чате или службах обмена сообщениями либо выполняют другие действия. В этой статье описывается, как настроить объект webhook для оповещений журнала действий Azure и как выглядят полезные данные запроса HTTP POST к webhook. Дополнительные сведения о настройке и схема для оповещений о метриках Azure приведены [здесь](./insights-webhooks-alerts.md). Можно также настроить отправку сообщения электронной почты при активации оповещения журнала действий.

>[AZURE.NOTE] Сейчас эта функция находится на стадии предварительной версии, поэтому возможны проблемы с производительностью и качеством ее работы.

Оповещение журнала действий можно настроить с помощью [командлетов Azure PowerShell ](./insights-powershell-samples.md#create-alert-rules), [кроссплатформенного интерфейса командной строки](./insights-cli-samples.md#work-with-alerts) или [REST API Insights](https://msdn.microsoft.com/library/azure/dn933805.aspx).

## Проверка подлинности объекта webhook
Объект webhook может проходить проверку подлинности с помощью любого из этих методов:

1. **Авторизация на основе маркера.** Универсальный код ресурса (URI) объекта webhook сохраняется вместе с идентификатором маркера, например `https://mysamplealert/webcallback?tokenid=sometokenid&someparameter=somevalue`.
2.	**Базовая авторизация.** Универсальный код ресурса (URI) объекта webhook сохраняется вместе с именем пользователя и паролем, например `https://userid:password@mysamplealert/webcallback?someparamater=somevalue&foo=bar`.

## Схема полезных данных
Операция POST содержит следующие полезные данные и схему JSON для всех оповещений, связанных с журналом действий. Эта схема аналогична той, которая используется оповещениями, связанными с метриками.

```
{
        "status": "Activated",
        "context": {
                "resourceProviderName": "Microsoft.Web",
                "event": {
                        "$type": "Microsoft.WindowsAzure.Management.Monitoring.Automation.Notifications.GenericNotifications.Datacontracts.InstanceEventContext, Microsoft.WindowsAzure.Management.Mon.Automation",
                        "authorization": {
                                "action": "Microsoft.Web/sites/start/action",
                                "scope": "/subscriptions/s1/resourcegroups/rg1/providers/Microsoft.Web/sites/leoalerttest"
                        },
                        "eventDataId": "327caaca-08d7-41b1-86d8-27d0a7adb92d",
                        "category": "Administrative",
                        "caller": "myname@mycompany.com",
                        "httpRequest": {
                                "clientRequestId": "f58cead8-c9ed-43af-8710-55e64def208d",
                                "clientIpAddress": "104.43.166.155",
                                "method": "POST"
                        },
                        "status": "Succeeded",
                        "subStatus": "OK",
                        "level": "Informational",
                        "correlationId": "4a40beaa-6a63-4d92-85c4-923a25abb590",
                        "eventDescription": "",
                        "operationName": "Microsoft.Web/sites/start/action",
                        "operationId": "4a40beaa-6a63-4d92-85c4-923a25abb590",
                        "properties": {
                                "$type": "Microsoft.WindowsAzure.Management.Common.Storage.CasePreservedDictionary, Microsoft.WindowsAzure.Management.Common.Storage",
                                "statusCode": "OK",
                                "serviceRequestId": "f7716681-496a-4f5c-8d14-d564bcf54714"
                        }
                },
                "timestamp": "Friday, March 11, 2016 9:13:23 PM",
                "id": "/subscriptions/s1/resourceGroups/rg1/providers/microsoft.insights/alertrules/alertonevent2",
                "name": "alertonevent2",
                "description": "test alert on event start",
                "conditionType": "Event",
                "subscriptionId": "s1",
                "resourceId": "/subscriptions/s1/resourcegroups/rg1/providers/Microsoft.Web/sites/leoalerttest",
                "resourceGroupName": "rg1"
        },
        "properties": {
                "key1": "value1",
                "key2": "value2"
        }
}
```

|Имя элемента|	Описание|
|---|---|
|status |Используется для оповещений на основе метрик. Всегда имеет значение activated для оповещений журнала действий.|
|context|Контекст события.|
|resourceProviderName|Поставщик ресурсов для затронутого ресурса.|
|conditionType |Всегда имеет значение Event.|
|name |Имя правила генерации оповещений.|
|id |Идентификатор ресурса для оповещения.|
|description|	Описание оповещения, заданное во время создания оповещения.|
|subscriptionId |Идентификатор подписки Azure.|
|Timestamp|	Время создания события службой Azure, которая обработала запрос.|
|resourceId |Идентификатор ресурса для затронутого ресурса.|
|имя\_группы\_ресурсов|Имя группы ресурсов для затронутого ресурса.|
|properties |Набор пар `<Key, Value>` (например, `Dictionary<String, String>`), содержащий сведения о событии.|
|event|Элемент, содержащий метаданные о событии.|
|authorization|Свойства RBAC события. Обычно к ним относятся action, role и scope.|
|category | Категория события. Поддерживаются следующие значения: Administrative, Alert, Security, ServiceHealth, Recommendation.|
|caller|Адрес электронной почты пользователя, который выполнил операцию, утверждение имени субъекта-службы или имени участника-пользователя в зависимости от доступности. Может иметь значение NULL для определенных системных вызовов.|
|correlationId|	Обычно GUID в строковом формате. События с correlationId относятся к одному крупному действию и обычно совместно используют correlationId.|
|eventDescription |Статическое описание события в текстовом виде.|
|eventDataId|Уникальный идентификатор события.|
|eventSource |Имя инфраструктуры или службы Azure, которая создала событие.|
|httpRequest|	Обычно включает "clientRequestId", "clientIpAddress" и "method" (метод HTTP, например PUT).|
|level|Одно из следующих значений: Critical, Error, Warning, Informational или Verbose.|
|operationId|Обычно события, относящиеся к одной операции, совместно используют один GUID.|
|operationName|Имя операции.|
|properties |Свойства события.|
|status|Строка. Состояние операции. Обычные значения: Started, In Progress, Succeeded, Failed, Active, Resolved.|
|subStatus|	Обычно содержит код состояния HTTP для соответствующего вызова REST. Может также включать другие строки, описывающие подсостояние. Обычные значения подсостояния: OK (код состояния HTTP: 200), Created (код состояния HTTP: 201), Accepted (код состояния HTTP: 202), No Content (код состояния HTTP: 204), Bad Request (код состояния HTTP: 400), Not Found (код состояния HTTP: 404), Conflict (код состояния HTTP: 409), Internal Server Error (код состояния HTTP: 500), Service Unavailable (код состояния HTTP: 503), Gateway Timeout (код состояния HTTP: 504).|

## Дальнейшие действия
- [Дополнительные сведения о журнале действий](./monitoring-overview-activity-logs.md)
- [Выполнение скриптов службы автоматизации Azure (Runbooks) на основе оповещений Azure](http://go.microsoft.com/fwlink/?LinkId=627081)
- [Использование приложения логики для отправки SMS с помощью Twilio из оповещения Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-text-message-with-logic-app). Это пример для оповещений на основе метрик, но его можно изменить для работы с оповещениями журнала действий.
- [Использование приложения логики для отправки сообщений Slack из оповещения Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-slack-with-logic-app). Это пример для оповещений на основе метрик, но его можно изменить для работы с оповещениями журнала действий.
- [Использование приложения логики для отправки сообщений в очередь Azure из оповещения Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-queue-with-logic-app). Это пример для оповещений на основе метрик, но его можно изменить для работы с оповещениями журнала действий.

<!---HONumber=AcomDC_0921_2016-->
<properties
	pageTitle="Azure Insights: использование журналов аудита для отправки электронной почты и уведомлений об оповещениях веб-перехватчика в Azure Insights | Microsoft Azure"
	description="Узнайте, как использовать записи журнала аудита для вызова URL-адресов веб-сайтов или отправки уведомлений по электронной почте в Azure Insights. "
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
	ms.date="03/30/2016"
	ms.author="ashwink"/>

# Использование журналов аудита для отправки электронной почты и уведомлений об оповещениях веб-перехватчика в Azure Insights

В данной статье показана схема полезных данных, когда событие журнала аудита запускает веб-перехватчик, описывающий, как отправлять сообщения электронной почты с помощью того же события.

>[AZURE.NOTE] Эта функция в настоящее время находится на стадии предварительной версии. В ближайшие месяцы инфраструктура и производительность оповещений о событиях будут улучшены. В этой предварительной версии данную функцию можно использовать только с помощью Azure PowerShell и интерфейса командной строки. Доступ к ней с помощью портала Azure будет реализован позже.

## Объекты Webhook
Веб-перехватчики позволяют направлять уведомления об оповещениях Azure в другие системы для постобработки или отображения настраиваемых уведомлений. Например, оповещения могут направляться в службы, которые способны обрабатывать входящие веб-запросы для отправки SMS, ведения журналов, уведомления пользователей в чате или через службы обмена мгновенными сообщениями. Универсальный код ресурса (URI) веб-перехватчика должен быть допустимой конечной точкой HTTP или HTTPS.

## Email
Сообщения электронной почты могут отправляться на любой допустимый адрес. Также будут уведомляться администраторы и соадминистраторы подписки, где работает это правило.

### Пример правила электронной почты
Необходимо настроить правила электронной почты и веб-перехватчика, а затем настроить их для запуска при возникновении события журнала аудита. Пример использования PowerShell можно просмотреть в [примерах быстрого старта PowerShell для Azure Insights](insights-powershell-samples.md#alert-on-audit-log-event).


## Аутентификация
Существует две формы универсального кода ресурса (URI) для проверки подлинности:

1. Проверка подлинности на основе токенов, заключающаяся в сохранении URI веб-перехватчика с идентификатором токена в качестве параметра запроса. Например, https://mysamplealert/webcallback?tokenid=sometokenid&someparameter=somevalue
2. Обычная проверка подлинности с использованием имени пользователя и пароля. Например, https://userid:password@mysamplealert/webcallback?someparamater=somevalue&parameter=value

## Схема полезных данных веб-перехватчика уведомлений для событий журнала аудита
При появлении нового события оповещение для событий журнала аудита запускает настроенный веб-перехватчик с метаданными событий в полезных данных веб-перехватчика. В следующем примере показана схема полезных данных веб-перехватчика:

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
|status |Всегда задавайте значение "activated"|
|context|Контекст события|
|resourceProviderName|Поставщик ресурсов для затронутого ресурса|
|conditionType |"Event"|
|name |Имя правила оповещения|
|id |Идентификатор ресурса для оповещения|
|description|	Описание, заданное создателем оповещения|
|subscriptionId |GUID подписки Azure|
|Timestamp|	Метка времени для момента создания события службой Azure, обработавшей соответствующий событию запрос|
|resourceId |Уникальный универсальный код ресурса (URI) идентификатора ресурса|
|имя\_группы\_ресурсов|Имя группы ресурсов для затронутого ресурса|
|properties |Набор пар <ключ, значение> (например, Dictionary<String, String>), содержащий сведения о событии|
|event|Элемент с метаданными о событии|
|authorization|Захватывает свойства RBAC события. Обычно к ним относятся "action", "role" и "scope".|
|category | Категория события. Поддерживаются следующие значения: "Administrative", "Alert", "Security", "ServiceHealth", "Recommendation".|
|caller|Адрес электронной почты пользователя, который выполнил операцию, утверждение ими субъекта-службы или имени участника-пользователя в зависимости от доступности. Может иметь значение NULL для определенных системных вызовов.|
|correlationId|	Обычно GUID в строковом формате. События с correlationId относятся к одному крупному действию и обычно совместно используют один correlationId.|
|eventDescription |Статический текст, описывающий событие|
|eventDataId|Уникальный идентификатор события|
|eventSource |Имя инфраструктуры или службы Azure, которая создала событие|
|httpRequest|	Обычно включает "clientRequestId", "clientIpAddress" и "method" (метод HTTP, например PUT).|
|level|Одно из следующих значений: "Critical", "Error", "Warning", "Informational" или "Verbose"|
|operationId|Обычно события, относящиеся к одной операции, совместно используют один GUID|
|operationName|Имя операции|
|properties |Элемент внутри элемента события, содержащий свойства события.|
|status|Строка, описывающая состояние операции. Обычные значения: "Started", "In Progress", "Succeeded", "Failed", "Active", "Resolved".|
|subStatus|	Обычно содержит код состояния HTTP для соответствующего вызова REST. Может также включать другие строки, описывающие подсостояние. Обычные значения подсостояния: "OK" (код состояния HTTP: 200), "Created" (код состояния HTTP: 201), "Accepted" (код состояния HTTP: 202), "No Content" (код состояния HTTP: 204), "Bad Request" (код состояния HTTP: 400), "Not Found" (код состояния HTTP: 404), "Conflict" (код состояния HTTP: 409), "Internal Server Error" (код состояния HTTP: 500), "Service Unavailable" (код состояния HTTP: 503), "Gateway Timeout" (код состояния HTTP: 504)|

<!---HONumber=AcomDC_0810_2016-->
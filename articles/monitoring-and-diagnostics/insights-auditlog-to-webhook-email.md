---
title: "Вызов веб-перехватчика с помощью оповещений журнала действий Azure | Документация Майкрософт"
description: "Узнайте, как перенаправлять события журнала действий в другие службы для выполнения пользовательских действий. Например, можно отправлять SMS, вести журнал ошибок или уведомлять команду через службу чата или обмена сообщениями."
author: johnkemnetz
manager: orenr
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 64d333d1-7f37-4a00-9d16-dda6e69a113b
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: johnkem
ms.openlocfilehash: 9872c30d123f0a7443e28dc58ee0d4e16572a390
ms.sourcegitcommit: 95500c068100d9c9415e8368bdffb1f1fd53714e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/14/2018
---
# <a name="call-a-webhook-on-an-azure-activity-log-alert"></a>Вызов веб-перехватчика для оповещений журнала действий Azure
С помощью веб-перехватчиков можно направлять уведомления об оповещениях Azure в другие системы для постобработки или выполнения настраиваемых действий. Веб-перехватчик можно использовать, чтобы направить оповещение к службам, которые отправляют SMS-сообщения, ведут журналы об ошибках, уведомляют членов команды в чате или службах обмена сообщениями либо выполняют другие действия. Кроме того, можно настроить отправку сообщения электронной почты при активации оповещения журнала действий.

В этой статье описывается, как настроить веб-перехватчик, вызываемый при активации оповещения журнала действий Azure. В ней также показывается, как выглядят полезные данные HTTP POST для webhook. Сведения о настройке и схеме оповещений метрик Azure см. в статье [Настройка объектов webhook для оповещений на основе метрик Azure](insights-webhooks-alerts.md). 

> [!NOTE]
> Сейчас функция, поддерживающая вызов веб-перехватчика для оповещения журнала действий Azure доступна в предварительной версии.
>
>

Оповещение журнала действий можно настроить с помощью [командлетов Azure PowerShell](insights-powershell-samples.md#create-metric-alerts), [кроссплатформенного интерфейса командной строки](insights-cli-samples.md#work-with-alerts) или [REST API Azure Monitor](https://msdn.microsoft.com/library/azure/dn933805.aspx). Сейчас невозможно использовать портал Azure для настройки предупреждения журнала действий.

## <a name="authenticate-the-webhook"></a>Аутентификация веб-перехватчика
Веб-перехватчик может проходить проверку подлинности с помощью любого из этих методов:

* **Проверка подлинности на основе маркеров**. Универсальный код ресурса (URI) веб-перехватчика сохраняется вместе с идентификатором маркера. Например: `https://mysamplealert/webcallback?tokenid=sometokenid&someparameter=somevalue`
* **Обычная авторизация**. URI веб-перехватчика сохраняется вместе с именем пользователя и паролем. Например: `https://userid:password@mysamplealert/webcallback?someparamater=somevalue&foo=bar`

## <a name="payload-schema"></a>Схема полезных данных
Операция POST содержит следующие полезные данные и схему JSON для всех оповещений, связанных с журналом действий. Эта схема аналогична той, которая используется оповещениями, связанными с метриками.

```json
{
    "WebhookName": "Alert1515526229589",
    "RequestBody": {
        "schemaId": "Microsoft.Insights/activityLogs",
        "data": {
            "status": "Activated",
            "context": {
                "activityLog": {
                    "authorization": {
                        "action": "Microsoft.Compute/virtualMachines/deallocate/action",
                        "scope": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ContosoVM/providers/Microsoft.Compute/virtualMachines/ContosoVM1"
                    },
                    "channels": "Operation",
                    "claims": {
                        "aud": "https://management.core.windows.net/",
                        "iss": "https://sts.windows.net/00000000-0000-0000-0000-000000000000/",
                        "iat": "1234567890",
                        "nbf": "1234567890",
                        "exp": "1234567890",
                        "aio": "Y2NgYBD8ZLlhu27JU6WZsXemMIvVAAA=",
                        "appid": "00000000-0000-0000-0000-000000000000",
                        "appidacr": "2",
                        "e_exp": "262800",
                        "http://schemas.microsoft.com/identity/claims/identityprovider": "https://sts.windows.net/00000000-0000-0000-0000-000000000000/",
                        "http://schemas.microsoft.com/identity/claims/objectidentifier": "00000000-0000-0000-0000-000000000000",
                        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier": "00000000-0000-0000-0000-000000000000",
                        "http://schemas.microsoft.com/identity/claims/tenantid": "00000000-0000-0000-0000-000000000000",
                        "uti": "XnCk46TrDkOQXwo49Y8fAA",
                        "ver": "1.0"
                    },
                    "caller": "00000000-0000-0000-0000-000000000000",
                    "correlationId": "00000000-0000-0000-0000-000000000000",
                    "description": "",
                    "eventSource": "Administrative",
                    "eventTimestamp": "2018-01-09T20:11:25.8410967+00:00",
                    "eventDataId": "00000000-0000-0000-0000-000000000000",
                    "level": "Informational",
                    "operationName": "Microsoft.Compute/virtualMachines/deallocate/action",
                    "operationId": "00000000-0000-0000-0000-000000000000",
                    "resourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ContosoVM/providers/Microsoft.Compute/virtualMachines/ContosoVM1",
                    "resourceGroupName": "ContosoVM",
                    "resourceProviderName": "Microsoft.Compute",
                    "status": "Succeeded",
                    "subStatus": "",
                    "subscriptionId": "00000000-0000-0000-0000-000000000000",
                    "submissionTimestamp": "2018-01-09T20:11:40.2986126+00:00",
                    "resourceType": "Microsoft.Compute/virtualMachines"
                }
            },
            "properties": {}
        }
    },
    "RequestHeader": {
        "Expect": "100-continue",
        "Host": "s1events.azure-automation.net",
        "User-Agent": "IcMBroadcaster/1.0",
        "X-CorrelationContext": "RkkKACgAAAACAAAAEADlBbM7x86VTrHdQ2JlmlxoAQAQALwazYvJ/INPskb8S5QzgDk=",
        "x-ms-request-id": "00000000-0000-0000-0000-000000000000"
    }
}
```

| Имя элемента | ОПИСАНИЕ |
| --- | --- |
| status |Используется для оповещений на основе метрик. Всегда имеет значение Activated для оповещений журнала действий.|
| context |Контекст события. |
| activityLog | Свойства журнала для события.|
| authorization |Свойства управления доступом на основе ролей для события. Обычно к ним относятся **action**, **role** и **scope**. |
| action | Действие, зафиксированное в оповещении. |
| scope | Область оповещения (ресурс).|
| каналов | Операция. |
| claims | Коллекция данных, которые относятся к утверждениям. |
| caller |GUID или имя пользователя, который выполнил операцию, утверждение имени участника-пользователя или имени субъекта-службы в зависимости от доступности. Может иметь значение NULL для определенных системных вызовов. |
| correlationId |Обычно GUID в строковом формате. События с **correlationId** относятся к одному крупному действию. Обычно им присвоено одинаковое значение **correlationId**. |
| description |Описание оповещения, которое задается при его создании. |
| eventSource |Имя инфраструктуры или службы Azure, которая создала событие. |
| eventTimestamp |Время, когда произошло событие. |
| eventDataId |Уникальный идентификатор события. |
| level |Одно из следующих значений: Critical, Error, Warning, Informational или Verbose. |
| operationName |Имя операции. |
| operationId |Как правило, GUID, общий для событий. Идентификатор GUID обычно соответствует одной операции. |
| ResourceId |Идентификатор ресурса затронутого ресурса. |
| имя_группы_ресурсов |Имя группы ресурсов для затронутого ресурса. |
| resourceProviderName |Поставщик ресурса затронутого ресурса. |
| status |Строковое значение, указывающее состояние операции. Обычные значения: Started, In Progress, Succeeded, Failed, Active, Resolved. |
| subStatus |Обычно содержит код состояния HTTP для соответствующего вызова REST. Может также включать другие строки, описывающие подсостояние. Обычные значения подсостояния: OK (код состояния HTTP: 200), Created (код состояния HTTP: 201), Accepted (код состояния HTTP: 202), No Content (код состояния HTTP: 204), Bad Request (код состояния HTTP: 400), Not Found (код состояния HTTP: 404), Conflict (код состояния HTTP: 409), Internal Server Error (код состояния HTTP: 500), Service Unavailable (код состояния HTTP: 503), Gateway Timeout (код состояния HTTP: 504). |
| subscriptionId |Идентификатор подписки Azure. |
| submissionTimestamp |Время создания события службой Azure, которая обработала запрос. |
| тип_ресурса | Тип ресурса, в котором было создано событие.|
| properties |Набор пар "ключ — значение", содержащих подробные сведения о событии. Например, `Dictionary<String, String>`. |

## <a name="next-steps"></a>Дополнительная информация
* Узнайте больше о [журнале действий](monitoring-overview-activity-logs.md).
* Узнайте больше о [выполнении скриптов службы автоматизации Azure для оповещений Azure](http://go.microsoft.com/fwlink/?LinkId=627081).
* Узнайте, как [использовать приложение логики для отправки сообщения SMS с помощью Twilio из оповещения Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-text-message-with-logic-app). Это пример для оповещений на основе метрик, но его можно изменить для работы с оповещениями журнала действий.
* Узнайте, как [использовать приложение логики для отправки сообщения Slack из оповещения Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-slack-with-logic-app). Это пример для оповещений на основе метрик, но его можно изменить для работы с оповещениями журнала действий.
* Узнайте, как [использовать приложение логики для отправки сообщения в очередь Azure из оповещения Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-queue-with-logic-app). Это пример для оповещений на основе метрик, но его можно изменить для работы с оповещениями журнала действий.

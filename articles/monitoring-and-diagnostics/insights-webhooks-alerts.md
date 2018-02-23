---
title: "Настройка webhook для оповещений на основе метрик Azure | Документация Майкрософт"
description: "Ниже описывается, как выполнить перенаправление оповещений Azure в другие системы (не Azure)."
author: johnkemnetz
manager: carmonm
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 8b3ae540-1d19-4f3d-a635-376042f8a5bb
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/03/2017
ms.author: johnkem
ms.openlocfilehash: 049803e7701c68559103d9b1fa5dfacf820d0548
ms.sourcegitcommit: 95500c068100d9c9415e8368bdffb1f1fd53714e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/14/2018
---
# <a name="configure-a-webhook-on-an-azure-metric-alert"></a>Настройка объектов webhook для оповещений на основе метрик Azure
С помощью веб-перехватчика можно направлять уведомления об оповещениях Azure в другие системы для постобработки или выполнения настраиваемых действий. Веб-перехватчик можно использовать, чтобы направить оповещение к службам, которые отправляют SMS-сообщения, ведут журналы об ошибках, уведомляют членов команды в чате или службах обмена сообщениями либо выполняют другие действия. 

В этой статье описываются способы настройки веб-перехватчика для оповещения метрики Azure. В ней также показывается, как выглядят полезные данные HTTP POST для веб-перехватчика. Дополнительные сведения о настройке и схеме для оповещений журнала действий Azure см. в статье [Вызов веб-перехватчика для оповещений журнала действий Azure](insights-auditlog-to-webhook-email.md).

Оповещения Azure с помощью запроса HTTP POST помещают содержимое оповещения в формате JSON в универсальный код ресурса (URI) веб-перехватчика, указанный при создании оповещения. Схема определена далее в этой статье. Этот URI должен быть допустимой конечной точкой HTTP или HTTPS. При активации оповещений Azure размещает одну запись для каждого запроса.

## <a name="configure-webhooks-via-the-azure-portal"></a>Настройка веб-перехватчика на портале Azure
Чтобы добавить или обновить универсальный код ресурса (URI) веб-перехватчика на [портале Azure](https://portal.azure.com/), перейдите в раздел **создания и обновления оповещений**.

![Добавление панели правил генерации оповещений](./media/insights-webhooks-alerts/Alertwebhook.png)

Вы также можете настроить оповещение так, чтобы при его активации в URI веб-перехватчика добавлялись соответствующие сведения. Для этого можно использовать [командлеты Azure PowerShell](insights-powershell-samples.md#create-metric-alerts), [кроссплатформенный интерфейс командной строки](insights-cli-samples.md#work-with-alerts) или [REST API Azure Monitor](https://msdn.microsoft.com/library/azure/dn933805.aspx).

## <a name="authenticate-the-webhook"></a>Аутентификация веб-перехватчика
Для веб-перехватчика может использоваться аутентификация на основе маркеров. Универсальный код ресурса (URI) веб-перехватчика сохраняется вместе с идентификатором маркера. Например: `https://mysamplealert/webcallback?tokenid=sometokenid&someparameter=somevalue`

## <a name="payload-schema"></a>Схема полезных данных
Операция POST содержит следующие полезные данные и схему JSON для всех оповещений, связанных с метриками.

```JSON
{
    "WebhookName": "Alert1515515157799",
    "RequestBody": {
        "status": "Activated",
        "context": {
            "timestamp": "2015-08-14T22:26:41.9975398Z",
            "id": "/subscriptions/s1/resourceGroups/useast/providers/microsoft.insights/alertrules/ruleName1",
            "name": "ruleName1",
            "description": "some description",
            "conditionType": "Metric",
            "condition": {
                "metricName": "Requests",
                "metricUnit": "Count",
                "metricValue": "10",
                "threshold": "10",
                "windowSize": "15",
                "timeAggregation": "Average",
                "operator": "GreaterThanOrEqual"
            },
            "subscriptionId": "s1",
            "resourceGroupName": "useast",
            "resourceName": "mysite1",
            "resourceType": "microsoft.foo/sites",
            "resourceId": "/subscriptions/s1/resourceGroups/useast/providers/microsoft.foo/sites/mysite1",
            "resourceRegion": "centralus",
            "portalLink": "https://portal.azure.com/#resource/subscriptions/s1/resourceGroups/useast/providers/microsoft.foo/sites/mysite1"
        },
        "properties": {
            "key1": "value1",
            "key2": "value2"
        }
    }
}
```


| Поле | Обязательно | Фиксированный набор значений | Заметки |
|:--- |:--- |:--- |:--- |
| status |Да |Activated, Resolved |Состояние оповещения на основе заданных условий. |
| context |Да | |Контекст оповещения. |
| timestamp |Да | |Время активации оповещения |
| id |Да | |Каждое правило оповещения имеет уникальный идентификатор. |
| name |Да | |Имя оповещения. |
| description |Да | |Описание оповещения. |
| conditionType |Да |Metric, Event |Поддерживаются два типа оповещений: метрики и события. Оповещения метрики основаны на условии метрики. Оповещения событий основаны на событиях в журнале действий. Таким образом, это значение позволяет узнать тип оповещения — метрики или события. |
| condition |Да | |Специальные поля для проверки с учетом параметра **conditionType**. |
| metricName |Для оповещений на основе метрик | |Имя метрики, определяющей, что контролирует метрика. |
| metricUnit |Для оповещений на основе метрик |Bytes, BytesPerSecond, Count, CountPerSecond, Percent, Seconds |Допустимая метрикой единица Список допустимых значений см. См. [допустимые значения](https://msdn.microsoft.com/library/microsoft.azure.insights.models.unit.aspx). |
| metricValue |Для оповещений на основе метрик | |Фактическое значение метрики, которая вызвала оповещение. |
| threshold |Для оповещений на основе метрик | |Пороговое значение, при котором активируется оповещение. |
| windowSize |Для оповещений на основе метрик | |Период времени, используемый для отслеживания активности оповещения по пороговому значению. Значение должно составлять от 5 минут до 24 часов. Значение должно быть в формате длительности ISO 8601. |
| timeAggregation |Для оповещений на основе метрик |Average, Last, Maximum, Minimum, None, Total |Порядок объединения данных, собранных за определенный период. Значение по умолчанию — Average (Среднее). См. [допустимые значения](https://msdn.microsoft.com/library/microsoft.azure.insights.models.aggregationtype.aspx). |
| operator |Для оповещений на основе метрик | |Оператор, используемый для сравнения текущих данных метрик с заданным пороговым значением. |
| subscriptionId |Да | |Идентификатор подписки Azure. |
| имя_группы_ресурсов |Да | |Имя группы ресурсов для затронутого ресурса. |
| resourceName |Да | |Имя затронутого ресурса. |
| тип_ресурса |Да | |Тип ресурса затронутого ресурса. |
| ResourceId |Да | |Идентификатор ресурса затронутого ресурса. |
| resourceRegion |Да | |Регион или расположение затронутого ресурса. |
| portalLink |Да | |Прямая ссылка на страницу сводки по ресурсу на портале. |
| properties |Нет |Необязательно |Набор пар "ключ — значение", содержащих подробные сведения о событии. Например, `Dictionary<String, String>`. Поле свойства не является обязательным. В настраиваемом пользовательском интерфейсе или в рабочем процессе на основе приложения логики пользователи могут вводить пары "ключ — значение" для передачи в виде полезных сведений. Еще один способ передачи пользовательских свойств в веб-перехватчик — через сам универсальный код ресурса (URI) веб-перехватчика (в виде параметров запросов). |

> [!NOTE]
> Значение в поле **свойства** можно задать только с помощью [REST API Azure Monitor](https://msdn.microsoft.com/library/azure/dn933805.aspx).
>
>

## <a name="next-steps"></a>Дополнительная информация
* Дополнительные сведения об оповещениях Azure и веб-перехватчиках см. в видео, посвященном [интеграции оповещений Azure с PagerDuty](http://go.microsoft.com/fwlink/?LinkId=627080).
* Узнайте больше о [выполнении скриптов службы автоматизации Azure для оповещений Azure](http://go.microsoft.com/fwlink/?LinkId=627081).
* Узнайте, как [использовать приложение логики для отправки сообщения SMS с помощью Twilio из оповещения Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-text-message-with-logic-app).
* Узнайте, как [использовать приложение логики для отправки сообщения Slack из оповещения Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-slack-with-logic-app).
* Узнайте, как [использовать приложение логики для отправки сообщения в очередь Azure из оповещения Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-queue-with-logic-app).

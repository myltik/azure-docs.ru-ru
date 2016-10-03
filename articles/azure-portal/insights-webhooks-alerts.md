<properties
	pageTitle="Настройка объектов webhook для оповещений на основе метрик Azure | Microsoft Azure"
	description="Перенаправление оповещений Azure в другие системы (не Azure)"
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

# Настройка объектов webhook для оповещений на основе метрик Azure

Объекты webhook позволяют направлять уведомления об оповещениях Azure в другие системы для постобработки или выполнения настраиваемых действий. Объект webhook можно использовать, чтобы направить оповещение к службам, которые отправляют SMS, ведут журналы об ошибках, уведомляют членов команды в чате или службах обмена сообщениями либо выполняют другие действия. В этой статье описывается, как настроить объект webhook для оповещений на основе метрик Azure и как выглядят полезные данные запроса HTTP POST к webhook. Дополнительные сведения о настройке и схема для оповещений журнала действий Azure приведены [здесь](./insights-auditlog-to-webhook-email.md).

Оповещения Azure (с помощью запроса HTTP POST) помещают содержимое оповещения в формате JSON (схема приведена ниже) в универсальный код ресурса (URI) объекта webhook, указанный при создании оповещения. Этот URI должен быть допустимой конечной точкой HTTP или HTTPS. При активации оповещений Azure размещает одну запись для каждого запроса.

## Настройка веб-перехватчиков через портал

В окне создания и обновления оповещений на [портале Azure](https://portal.azure.com/) можно добавить или обновить универсальный кода ресурса (URI) объекта webhook.

![Добавить правило оповещения](./media/insights-webhooks-alerts/Alertwebhook.png)

Вы также можете настроить оповещение таким образом, чтобы при его активации в URI объекта webhook добавлялись соответствующие сведения. Для этого можно использовать [командлеты Azure PowerShell](./insights-powershell-samples.md#create-alert-rules), [кроссплатформенный интерфейс командной строки](./insights-cli-samples.md#work-with-alerts) или [REST API Insights](https://msdn.microsoft.com/library/azure/dn933805.aspx).

## Проверка подлинности объекта webhook

Объект webhook может проходить проверку подлинности с помощью любого из этих методов:

1. **Авторизация на основе маркера.** Универсальный код ресурса (URI) объекта webhook сохраняется вместе с идентификатором маркера, например `https://mysamplealert/webcallback?tokenid=sometokenid&someparameter=somevalue`.
2.	**Базовая авторизация.** Универсальный код ресурса (URI) объекта webhook сохраняется вместе с именем пользователя и паролем, например `https://userid:password@mysamplealert/webcallback?someparamater=somevalue&foo=bar`.

## Схема полезных данных

Операция POST содержит следующие полезные данные и схему JSON для всех оповещений, связанных с метриками.

```
{
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
```


| Поле | Обязательно | Фиксированный набор значений | Примечания |
| :-------------| :-------------   | :-------------   | :-------------   |
|status|Да|Activated, Resolved|Состояние оповещения на основе заданных условий.|
|context| Да | | Контекст оповещения.|
|Timestamp| Да | | Время активации оповещения|
|id | Да | | Каждое правило оповещения имеет уникальный идентификатор.|
|name |Да | | Имя оповещения.|
|description |Да | |Описание оповещения.|
|conditionType |Да |Metric, Event |Поддерживаются два типа оповещений: на основе метрик и на основе событий в журнале действий. Таким образом, это значение позволяет узнать тип оповещения.|
|condition |Да | | Специальные поля для проверки с учетом параметра conditionType.|
|metricName |Для оповещений на основе метрик | |Имя метрики, определяющей, что контролирует метрика.|
|metricUnit |Для оповещений на основе метрик |Bytes, BytesPerSecond, Count, CountPerSecond, Percent, Seconds|	 Допустимая метрикой единица Список допустимых значений см. [здесь](https://msdn.microsoft.com/library/microsoft.azure.insights.models.unit.aspx).|
|metricValue |Для оповещений на основе метрик | |Фактическое значение метрики, которая вызвала оповещение.|
|threshold |Для оповещений на основе метрик | |Пороговое значение, при котором активируется оповещение.|
|windowSize |Для оповещений на основе метрик | |Период времени, используемый для отслеживания активности оповещения по пороговому значению. Это должен быть период от 5 минут до 1 дня. Формат длительности — ISO 8601.|
|timeAggregation |Для оповещений на основе метрик |Average, Last, Maximum, Minimum, None, Total |	Порядок объединения данных, собранных за определенный период. Значение по умолчанию — Average (Среднее). Список допустимых значений см. [здесь](https://msdn.microsoft.com/library/microsoft.azure.insights.models.aggregationtype.aspx).|
|operator |Для оповещений на основе метрик | |Оператор, используемый для сравнения текущих данных метрик с заданным пороговым значением.|
|subscriptionId |Да | |Идентификатор подписки Azure.|
|имя\_группы\_ресурсов |Да | |Имя группы ресурсов для затронутого ресурса.|
|resourceName |Да | |Имя затронутого ресурса.|
|тип\_ресурса |Да | |Тип затронутого ресурса.|
|resourceId |Да | |Идентификатор ресурса для затронутого ресурса.|
|resourceRegion |Да | |Регион или расположение затронутого ресурса.|
|portalLink |Да | |Прямая ссылка на страницу сводки по ресурсу на портале.|
|properties |Нет |Необязательно |Набор пар `<Key, Value>` (например, `Dictionary<String, String>`), содержащий сведения о событии. Поле свойства не является обязательным. В настраиваемом пользовательском интерфейсе или в рабочем процессе на основе приложения логики пользователи могут вводить пары "ключ — значение" для передачи в виде полезных сведений. Еще один способ передачи пользовательских свойств в веб-перехватчик — через сам универсальный код ресурса (URI) веб-перехватчика (в виде параметров запросов).|


>[AZURE.NOTE] Поле свойств можно задать с помощью [REST API Insights](https://msdn.microsoft.com/library/azure/dn933805.aspx).

## Дальнейшие действия

- Дополнительные сведения об оповещениях Azure и объектах webhook см. в видео [Integrate Azure Alerts with PagerDuty](http://go.microsoft.com/fwlink/?LinkId=627080) (Интеграция оповещений Azure с PagerDuty)
- [Выполнение скриптов службы автоматизации Azure (Runbooks) на основе оповещений Azure](http://go.microsoft.com/fwlink/?LinkId=627081)
- [Использование приложения логики для отправки SMS с помощью Twilio из оповещения Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-text-message-with-logic-app)
- [Использование приложения логики для отправки сообщений Slack из оповещения Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-slack-with-logic-app)
- [Использование приложения логики для отправки сообщений в очередь Azure из оповещения Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-queue-with-logic-app)

<!---HONumber=AcomDC_0921_2016-->
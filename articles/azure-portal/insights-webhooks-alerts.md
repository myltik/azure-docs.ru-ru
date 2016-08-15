<properties
	pageTitle="Настройка оповещений Azure для отправки в другие системы | Microsoft Azure"
	description="Перенаправление оповещений Azure в другие системы (не Azure)"
	authors="kamathashwin"
	manager=""
	editor=""
	services="monitoring"
	documentationCenter="monitoring"/>

<tags
	ms.service="monitoring"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/16/2016"
	ms.author="ashwink"/>

# Настройка веб-перехватчиков для оповещений

Веб-перехватчики позволяют направлять оповещения Azure в другие системы для постобработки или отображения настраиваемых уведомлений. Например, оповещения Azure могут отправляться в службы, которые способны обрабатывать входящие веб-запросы для отправки SMS, ведения журналов, уведомления членов команды в чате или через службы обмена мгновенными сообщениями и т. д.

Универсальный код ресурса (URI) веб-перехватчика должен быть действительной конечной точкой HTTP или HTTPS. Служба оповещений Azure выполнит операцию POST в указанном веб-перехватчике, передав полезные данные и схему JSON.

## Настройка веб-перехватчиков через портал

В окне «Создание/обновление оповещений» на [портале Azure](https://portal.azure.com/) можно добавить или обновить URL веб-перехватчика.

![Добавить правило оповещения](./media/insights-webhooks-alerts/Alertwebhook.png)


## Аутентификация

Доступны два типа проверки подлинности.

1. **Проверка подлинности на основе маркеров** — в этом случае универсальный код ресурса (URI) веб-перехватчика сохраняется с идентификатором маркера, например *https://mysamplealert/webcallback?tokenid=sometokenid&someparameter=somevalue*.
2.	**Обычная проверка подлинности** по имени пользователя и паролю: в этом случае универсальный код ресурса (URI) веб-перехватчика сохраняется как *https://userid:password@mysamplealert/webcallback?someparamater=somevalue&foo=bar*.

## Схема полезных данных

Операция POST будет содержать следующие полезные данные и схему JSON для всех оповещений, связанных с метриками.

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
            "portalLink": “https://portal.azure.com/#resource/subscriptions/s1/resourceGroups/useast/providers/microsoft.foo/sites/mysite1”                                
},
"properties": {
              "key1": "value1",
              "key2": "value2"
              }
}
```

>[AZURE.NOTE] В следующем обновлении мы добавим поддержку оповещений о событиях (“conditionType” : “Event”).


| Поле | Обязательное? | Фиксированный набор значений? | Примечания |
| :-------------| :-------------   | :-------------   | :-------------   |
|status|Да|Activated, Resolved|Позволяет определить вид оповещения. Azure автоматически отправляет оповещения об активации и разрешении для заданного условия.|
|context| Да | | Контекст оповещения|
|Timestamp| Да | | Время активации оповещения Оповещение активируется при считывании показателя из хранилища диагностических данных.|
|id | Да | | Каждое правило оповещения имеет уникальный идентификатор.|
|name|Да | |
|description |Да | |Описание оповещения|
|conditionType |Да |Metric, Event |Поддерживаются два типа оповещений: оповещения на основе метрик и оповещения на основе событий. В дальнейшем мы добавим поддержку оповещений для событий, поэтому используйте это значение, чтобы проверить, основано ли оповещение на метрике или событии|
|condition |Да | |Здесь задаются специальные поля для проверки с учетом параметра conditionType|
|metricName |Для оповещений на основе метрик | |Имя метрики, определяющей, что контролирует метрика.|
|metricUnit |Для оповещений на основе метрик |Bytes, BytesPerSecond, Count, CountPerSecond, Percent, Seconds|	 Допустимая метрикой единица Допустимые значения: https://msdn.microsoft.com/library/microsoft.azure.insights.models.unit.aspx|
|metricValue |Для оповещений на основе метрик | |Фактическое значение метрики, которая вызвала оповещение|
|threshold |Для оповещений на основе метрик | |Пороговое значение, активирующее оповещение|
|windowSize |Для оповещений на основе метрик | |Период времени, используемый для отслеживания активности оповещения по пороговому значению. Это должен быть период от 5 минут до 1 дня. Формат длительности — ISO 8601.|
|timeAggregation |Для оповещений на основе метрик |Average, Last, Maximum, Minimum, None, Total |	Порядок объединения данных, собранных за определенный период. Значение по умолчанию — Average (Среднее). Допустимые значения: https://msdn.microsoft.com/library/microsoft.azure.insights.models.aggregationtype.aspx|
|operator |Для оповещений на основе метрик | |Оператор, используемый для сравнения данных с пороговым значением.|
|subscriptionId |Да | |GUID подписки Azure|
|имя\_группы\_ресурсов |Да | |Имя группы затронутых ресурсов|
|resourceName |Да | |Имя затронутого ресурса|
|тип\_ресурса |Да | |Тип затронутого ресурса|
|resourceId |Да | |Уникальный универсальный код ресурса (URI) идентификатора ресурса|
|resourceRegion |Да | |Расположение региона или области местонахождения затронутого ресурса|
|portalLink |Да | |Прямая ссылка на сводную страницу ресурса на портале Azure|
|properties |Нет |Необязательно |Набор пар <ключ, значение> (например, Dictionary<String, String>), содержащий сведения о событии. Поле свойства не является обязательным. В настраиваемом пользовательском интерфейсе или в рабочем процессе на основе приложения логики пользователи могут вводить ключ/значения для передачи в виде полезных сведений. Еще один способ передачи пользовательских свойств в веб-перехватчик — через сам универсальный код ресурса (URI) веб-перехватчика (в виде параметров запросов).|


>[AZURE.NOTE] На портале поле свойств использовать нельзя. В будущей версии пакета SDK для Insights свойства можно будет настраивать с помощью API Alert.

## Дальнейшие действия

Дополнительные сведения об оповещениях Azure и веб-перехватчиках см. в видео [Интеграция оповещений Azure с PagerDuty](http://go.microsoft.com/fwlink/?LinkId=627080).

Дополнительные сведения о программном создании веб-перехватчика см. в статье [Создание оповещений с веб-перехватчиками с помощью пакета SDK Azure для Insights (C#)](https://code.msdn.microsoft.com/Create-Azure-Alerts-with-b938077a).

Настроив веб-перехватчики и оповещения, ознакомьтесь с другими способами запуска сценария автоматизации.

[Выполнение сценариев службы автоматизации Azure (Runbooks)](http://go.microsoft.com/fwlink/?LinkId=627081)

Используйте оповещения Azure для отправки сообщений в другие службы. Для начала воспользуйтесь следующими примерами шаблонов:

[Использование приложения логики для отправки SMS через API Twilio](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-text-message-with-logic-app)

[Использование приложения логики для отправки сообщений Slack](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-slack-with-logic-app)

[Использование приложения логики для отправки сообщений в очередь Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-queue-with-logic-app)

<!---HONumber=AcomDC_0803_2016-->
---
title: "Отправка событий в среду Azure Time Series Insights | Документация Майкрософт"
description: "Это руководство содержит сведения о передаче событий в среду Time Series Insights"
keywords: 
services: time-series-insights
documentationcenter: 
author: venkatgct
manager: almineev
editor: cgronlun
ms.assetid: 
ms.service: time-series-insights
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 04/21/2017
ms.author: venkatja
ms.translationtype: Human Translation
ms.sourcegitcommit: 6efa2cca46c2d8e4c00150ff964f8af02397ef99
ms.openlocfilehash: 9f2d3b57a42efb7b04566278d3267b3cdbed713a
ms.contentlocale: ru-ru
ms.lasthandoff: 07/01/2017

---
<a id="send-events-to-a-time-series-insights-environment-via-event-hub" class="xliff"></a>

# Отправка событий в среду Time Series Insights через концентратор событий

Это руководство содержит сведения о том, как создать и настроить концентратор событий и запустить пример приложения для передачи событий. Если у вас уже есть концентратор событий, содержащий события в формате JSON, пропустите это руководство и просмотрите свою среду в [Time Series Insights](https://insights.timeseries.azure.com).

<a id="configure-an-event-hub" class="xliff"></a>

## Настройка концентратора событий
1. Чтобы создать концентратор событий, следуйте инструкциям из [документации](https://docs.microsoft.com/azure/event-hubs/event-hubs-create) по концентратору событий.

2. Создайте группу потребителей, которая используется исключительно источником событий Time Series Insights.

  > [!IMPORTANT]
  > Убедитесь, что эта группа потребителей не используется другой службой (например, заданием Stream Analytics или другой средой Time Series Insights). Если группа потребителей используется другими службами, это негативно скажется на операции чтения в этой среде и в других службах. Использование $Default в качестве группы потребителей может потенциально привести к ее повторному использованию другими читателями.

  ![Выбор группы потребителей концентратора событий](media/send-events/consumer-group.png)

3. В концентраторе событий создайте политику MySendPolicy, используемую для отправки событий в примере C#.

  ![Выбор "Политики общего доступа" и кнопка "Добавить"](media/send-events/shared-access-policy.png)  

  ![Добавление новой политики общего доступа](media/send-events/shared-access-policy-2.png)  

<a id="create-time-series-insights-event-source" class="xliff"></a>

## Создание источника событий Time Series Insights
1. Если вы еще не создали источник событий, следуйте инструкциям по его созданию, указанным [здесь](time-series-insights-add-event-source.md).

2. В качестве имени свойства метки времени укажите deviceTimestamp. Это свойство будет использоваться в качестве фактической метки времени в примере C#. Имя свойства метки времени чувствительно к регистру, поэтому при отправке JSON в концентратор событий значения должны иметь формат __yyyy-MM-ddTHH:mm:ss.FFFFFFFK__. Если свойство не существует в событии, используется время размещения в очереди для концентратора событий.

  ![Создание источника событий](media/send-events/event-source-1.png)

<a id="sample-code-to-push-events" class="xliff"></a>

## Пример кода для принудительной отправки событий
1. Перейдите к политике концентратора событий MySendPolicy и скопируйте строку подключения с ключом политики.

  ![Копирование строки подключения MySendPolicy](media/send-events/sample-code-connection-string.png)

2. Выполните следующий код, который будет отправлять 600 событий для каждого из трех устройств. Обновите `eventHubConnectionString` с помощью строки подключений.

```csharp
using System;
using System.Collections.Generic;
using System.Globalization;
using System.IO;
using Microsoft.ServiceBus.Messaging;

namespace Microsoft.Rdx.DataGenerator
{
    internal class Program
    {
        private static void Main(string[] args)
        {
            var from = new DateTime(2017, 4, 20, 15, 0, 0, DateTimeKind.Utc);
            Random r = new Random();
            const int numberOfEvents = 600;

            var deviceIds = new[] { "device1", "device2", "device3" };

            var events = new List<string>();
            for (int i = 0; i < numberOfEvents; ++i)
            {
                for (int device = 0; device < deviceIds.Length; ++device)
                {
                    // Generate event and serialize as JSON object:
                    // { "deviceTimestamp": "utc timestamp", "deviceId": "guid", "value": 123.456 }
                    events.Add(
                        String.Format(
                            CultureInfo.InvariantCulture,
                            @"{{ ""deviceTimestamp"": ""{0}"", ""deviceId"": ""{1}"", ""value"": {2} }}",
                            (from + TimeSpan.FromSeconds(i * 30)).ToString("o"),
                            deviceIds[device],
                            r.NextDouble()));
                }
            }

            // Create event hub connection.
            var eventHubConnectionString = @"Endpoint=sb://...";
            var eventHubClient = EventHubClient.CreateFromConnectionString(eventHubConnectionString);

            using (var ms = new MemoryStream())
            using (var sw = new StreamWriter(ms))
            {
                // Wrap events into JSON array:
                sw.Write("[");
                for (int i = 0; i < events.Count; ++i)
                {
                    if (i > 0)
                    {
                        sw.Write(',');
                    }
                    sw.Write(events[i]);
                }
                sw.Write("]");

                sw.Flush();
                ms.Position = 0;

                // Send JSON to event hub.
                EventData eventData = new EventData(ms);
                eventHubClient.Send(eventData);
            }
        }
    }
}

```
<a id="supported-json-shapes" class="xliff"></a>

## Поддерживаемые формы JSON
<a id="sample-1" class="xliff"></a>

### Пример 1

<a id="input" class="xliff"></a>

#### Входные данные

Простой объект JSON.

```json
{
    "id":"device1",
    "timestamp":"2016-01-08T01:08:00Z"
}
```
<a id="output---1-event" class="xliff"></a>

#### Выходные данные, одно событие

|id|Timestamp|
|--------|---------------|
|device1|2016-01-08T01:08:00Z|

<a id="sample-2" class="xliff"></a>

### Пример 2

<a id="input" class="xliff"></a>

#### Входные данные
Массив JSON с двумя объектами JSON. Каждый объект JSON будет преобразован в событие.
```json
[
    {
        "id":"device1",
        "timestamp":"2016-01-08T01:08:00Z"
    },
    {
        "id":"device2",
        "timestamp":"2016-01-17T01:17:00Z"
    }
]
```
<a id="output---2-events" class="xliff"></a>

#### Выходные данные, два события

|id|Timestamp|
|--------|---------------|
|device1|2016-01-08T01:08:00Z|
|device2|2016-01-08T01:17:00Z|
<a id="sample-3" class="xliff"></a>

### Пример 3

<a id="input" class="xliff"></a>

#### Входные данные

Объект JSON с вложенным массивом JSON, содержащий два объекта JSON.
```json
{
    "location":"WestUs",
    "events":[
        {
            "id":"device1",
            "timestamp":"2016-01-08T01:08:00Z"
        },
        {
            "id":"device2",
            "timestamp":"2016-01-17T01:17:00Z"
        }
    ]
}

```
<a id="output---2-events" class="xliff"></a>

#### Выходные данные, два события
Обратите внимание, что свойство location копируется для каждого события.

|location|events.id|events.timestamp|
|--------|---------------|----------------------|
|WestUs|device1|2016-01-08T01:08:00Z|
|WestUs|device2|2016-01-08T01:17:00Z|

<a id="sample-4" class="xliff"></a>

### Пример 4

<a id="input" class="xliff"></a>

#### Входные данные

Объект JSON с вложенным массивом JSON, содержащий два объекта JSON. Эти входные данные указывают на то, что глобальные свойства могут быть представлены сложным объектом JSON.

```json
{
    "location":"WestUs",
    "manufacturer":{
        "name":"manufacturer1",
        "location":"EastUs"
    },
    "events":[
        {
            "id":"device1",
            "timestamp":"2016-01-08T01:08:00Z",
            "data":{
                "type":"pressure",
                "units":"psi",
                "value":108.09
            }
        },
        {
            "id":"device2",
            "timestamp":"2016-01-17T01:17:00Z",
            "data":{
                "type":"vibration",
                "units":"abs G",
                "value":217.09
            }
        }
    ]
}
```
<a id="output---2-events" class="xliff"></a>

#### Выходные данные, два события

|location|manufacturer.name|manufacturer.location|events.id|events.timestamp|events.data.type|events.data.units|events.data.value|
|---|---|---|---|---|---|---|---|
|WestUs|manufacturer1|EastUs|device1|2016-01-08T01:08:00Z|pressure|psi|108.09|
|WestUs|manufacturer1|EastUs|device2|2016-01-08T01:17:00Z|vibration|abs G|217.09|

<a id="next-steps" class="xliff"></a>

## Дальнейшие действия

* Просмотрите свою среду на [портале Time Series Insights](https://insights.timeseries.azure.com).


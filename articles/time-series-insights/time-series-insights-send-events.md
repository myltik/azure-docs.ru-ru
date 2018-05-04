---
title: Как отправлять события в среду службы "Аналитика временных рядов Azure" | Документация Майкрософт
description: Это руководство содержит сведения о том, как создать и настроить концентратор событий и запустить пример приложения для передачи событий и их отображения в службе "Аналитика временных рядов Azure".
services: time-series-insights
ms.service: time-series-insights
author: venkatgct
ms.author: venkatja
manager: jhubbard
editor: MarkMcGeeAtAquent
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.devlang: csharp
ms.workload: big-data
ms.topic: article
ms.date: 04/09/2018
ms.openlocfilehash: b418d1114cf6b906dcdee46bbf7e094cbc4a0521
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2018
---
# <a name="send-events-to-a-time-series-insights-environment-using-event-hub"></a>Отправка событий в среду Time Series Insights через концентратор событий
Это статья содержит сведения о том, как создать и настроить концентратор событий и запустить пример приложения для передачи событий. Если у вас уже есть концентратор событий, содержащий события в формате JSON, пропустите это руководство и просмотрите свою среду в службе [Аналитика временных рядов](https://insights.timeseries.azure.com).

## <a name="configure-an-event-hub"></a>Настройка концентратора событий
1. Чтобы создать концентратор событий, следуйте инструкциям из [документации](../event-hubs/event-hubs-create.md) по концентратору событий.

2. Выполните поиск **концентратора событий** в строке поиска. Выберите **Концентраторы событий** в возвращенном списке.

3. Выберите концентратор событий, щелкнув его имя.

4. В разделе **Сущности** в центре окна настройки снова выберите **Концентраторы событий**.

5. Выберите имя концентратора событий, чтобы настроить его.

  ![Выбор группы потребителей концентратора событий](media/send-events/consumer-group.png)

6. В разделе **Сущности** выберите **Группы потребителей**.
 
7. Создайте группу потребителей, которая используется исключительно источником событий Time Series Insights.

   > [!IMPORTANT]
   > Убедитесь, что эта группа потребителей не используется другой службой (например, заданием Stream Analytics или другой средой Time Series Insights). Если группа потребителей используется другими службами, это негативно скажется на операции чтения в этой среде и в других службах. Использование $Default в качестве группы потребителей может потенциально привести к ее повторному использованию другими читателями.

8. В разделе **Параметры** выберите **Share access policies** (Политики общего доступа).

9. В концентраторе событий создайте политику **MySendPolicy**, используемую для отправки событий в примере CSharp.

  ![Выбор "Политики общего доступа" и кнопка "Добавить"](media/send-events/shared-access-policy.png)  

  ![Добавление новой политики общего доступа](media/send-events/shared-access-policy-2.png)  

## <a name="add-time-series-insights-reference-data-set"></a>Добавление эталонного набора данных аналитики временных рядов 
Использование эталонных данных в TSI позволяет согласовать данные телеметрии с контекстом.  Этот контекст добавляет значимость к данным и упрощает фильтрацию и выполнение статических вычислений.  TSI объединяет эталонные данные во время входа и не может ретроактивно объединять эти данные.  Поэтому важно добавить эталонные данные, прежде чем добавлять источник событий с данными.  Такие данные, как расположение или тип датчика, являются полезными измерениями, которые можно соединить с идентификатором устройства, тега или датчика, чтобы упростить срез и фильтрацию.  

> [!IMPORTANT]
> Наличие настроенного набора эталонных данных критически важно при отправке данных журнала.

Убедитесь, что у вас есть эталонные данные при массовой отправке данных журнала в TSI.  Помните, что TSI немедленно начнет чтение из соединенного источника события, если в нем есть данные.  Рекомендуется соединять источник событий к TSI, только когда у вас будут эталонные данные, особенно в том случае, если в источнике событий содержатся данные. Кроме того, можно отложить отправку данных в источник событий, пока эталонный набор данных не появится в нужном расположении.

Для управления эталонными данными в обозревателе TSI имеется пользовательский веб-интерфейс, а также программный API на основе C#. В обозревателе TSI есть визуальный пользовательский интерфейс, позволяющий отправлять файлы или вставлять имеющиеся эталонные наборы данных в формате файлов JSON или CSV. При необходимости с помощью API можно создать клиентское приложение.

Дополнительные сведения об управлении эталонными данными в службе "Аналитика временных рядов" см. в [статье об эталонных данных](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-add-reference-data-set).

## <a name="create-time-series-insights-event-source"></a>Создание источника событий Time Series Insights
1. Если вы еще не создали источник событий, создайте его, [следуя инструкциям](time-series-insights-how-to-add-an-event-source-eventhub.md).

2. В качестве имени свойства метки времени укажите **deviceTimestamp**. Это свойство будет использоваться в качестве фактической метки времени в примере C#. Имя свойства метки времени чувствительно к регистру, поэтому при отправке в концентратор событий в формате JSON значения должны иметь формат __yyyy-MM-ddTHH:mm:ss.FFFFFFFK__. Если свойство не существует в событии, используется время размещения в очереди для концентратора событий.

  ![Создание источника событий](media/send-events/event-source-1.png)

## <a name="sample-code-to-push-events"></a>Пример кода для принудительной отправки событий
1. Перейдите к политике концентратора событий с именем **MySendPolicy**. Скопируйте **строку подключения** с ключом политики.

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
## <a name="supported-json-shapes"></a>Поддерживаемые формы JSON
### <a name="sample-1"></a>Пример 1

#### <a name="input"></a>Входные данные

Простой объект JSON.

```json
{
    "id":"device1",
    "timestamp":"2016-01-08T01:08:00Z"
}
```
#### <a name="output---one-event"></a>Вывод. Одно событие

|id|timestamp|
|--------|---------------|
|device1|2016-01-08T01:08:00Z|

### <a name="sample-2"></a>Пример 2

#### <a name="input"></a>Входные данные
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
#### <a name="output---two-events"></a>Вывод. Два события

|id|timestamp|
|--------|---------------|
|device1|2016-01-08T01:08:00Z|
|device2|2016-01-08T01:17:00Z|

### <a name="sample-3"></a>Пример 3

#### <a name="input"></a>Входные данные

Объект JSON с вложенным массивом JSON, содержащий два объекта JSON:
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
#### <a name="output---two-events"></a>Вывод. Два события
Обратите внимание, что свойство location копируется для каждого события.

|location|events.id|events.timestamp|
|--------|---------------|----------------------|
|WestUs|device1|2016-01-08T01:08:00Z|
|WestUs|device2|2016-01-08T01:17:00Z|

### <a name="sample-4"></a>Пример 4

#### <a name="input"></a>Входные данные

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
#### <a name="output---two-events"></a>Вывод. Два события

|location|manufacturer.name|manufacturer.location|events.id|events.timestamp|events.data.type|events.data.units|events.data.value|
|---|---|---|---|---|---|---|---|
|WestUs|manufacturer1|EastUs|device1|2016-01-08T01:08:00Z|pressure|psi|108.09|
|WestUs|manufacturer1|EastUs|device2|2016-01-08T01:17:00Z|vibration|abs G|217.09|

### <a name="json-shaping-strategies"></a>Стратегии формирования JSON
Используем следующий пример события в качестве начальной точки, а затем рассмотрим связанные с ним проблемы и стратегии по их устранению.

#### <a name="payload-1"></a>Полезные данные 1:
```json
[{
            "messageId": "LINE_DATA",
            "deviceId": "FXXX",
            "timestamp": 1522355650620,
            "series": [{
                        "chId": 3,
                        "value": -3750.0
                  }, {
                        "chId": 13,
                        "value": 0.58015072345733643
                  }, {
                        "chId": 11,
                        "value": 800.0
                  }, {
                        "chId": 21,
                        "value": 0.0
                  }, {
                        "chId": 14,
                        "value": -999.0
                  }, {
                        "chId": 37,
                        "value": 2.445906400680542
                  }, {
                        "chId": 39,
                        "value": 0.0
                  }, {
                        "chId": 40,
                        "value": 1.0
                  }, {
                        "chId": 1,
                        "value": 1.0172575712203979
                  }
            ],
            "EventProcessedUtcTime": "2018-03-29T20:36:21.3245900Z",
            "PartitionId": 2,
            "EventEnqueuedUtcTime": "2018-03-29T20:34:11.0830000Z",
            "IoTHub": {
                  "MessageId": "<17xxx2xx-36x0-4875-9x1x-x428x41x1x68>",
                  "CorrelationId": "<x253x5xx-7xxx-4xx3-91x4-xxx3bx2xx0x3>",
                  "ConnectionDeviceId": "AAAA-ZZ-001",
                  "ConnectionDeviceGenerationId": "<123456789012345678>",
                  "EnqueuedTime": "2018-03-29T20:34:10.7990000Z",
                  "StreamId": null
            }
      }
]
 ```

Если отправить этот массив событий в качестве полезных данных в TSI, для каждого измеренного значения будет сохранено одно событие. Последствием может быть избыток событий, что может быть не идеальным решением. Обратите внимание, что вы можете использовать эталонные данные в TSI, чтобы добавлять значимые имена в качестве свойств.  Например, можно создать эталонный набор данных со свойством ключа = chId:  

chId  Measure               Unit 24    Engine Oil Pressure   PSI 25    CALC Pump Rate        bbl/min

Дополнительные сведения об управлении эталонными данными в службе "Аналитика временных рядов" см. в [статье об эталонных данных](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-add-reference-data-set).

Другой проблемой с первыми полезными данными является метка времени в миллисекундах. TSI принимает только метки времени в формате ISO. Единственное решение — оставить поведение метки времени по умолчанию в TSI, то есть использовать поставленную в очередь метку времени.

В качестве альтернативы указанных выше полезных данных давайте рассмотрим другой пример.  

#### <a name="payload-2"></a>Полезные данные 2:
```json
{
      "line": "Line01",
      "station": "Station 11",
      "gatewayid": "AAAA-ZZ-001",
      "deviceid": "F12XX",
      "timestamp": "2018-03-29T20:34:15.0000000Z",
      "STATE Engine State": 1,
      "unit": "NONE"
}, {
      "line": "Line01",
      "station": "Station 11",
      "gatewayid": "AAAA-ZZ-001",
      "deviceid": "MPC_AAAA-ZZ-001",
      "timestamp": "2018-03-29T20:34:15.0000000Z",
      "Well Head Px 1": -494162.8515625,
      "unit": "psi"
}, {
      "line": "Line01",
      "station": "Station 11",
      "gatewayid": "AAAA-ZZ-001",
      "deviceid": "F12XX",
      "timestamp": "2018-03-29T20:34:15.0000000Z",
      "CALC Pump Rate": 0,
      "unit": "bbl/min"
}, {
      "line": "Line01",
      "station": "Station 11",
      "gatewayid": "AAAA-ZZ-001",
      "deviceid": "F12XX",
      "timestamp": "2018-03-29T20:34:15.0000000Z",
      "Engine Fuel Pressure": 0,
      "unit": "psi"
}, {
      "line": "Line01",
      "station": "Station 11",
      "gatewayid": "AAAA-ZZ-001",
      "deviceid": "F12XX",
      "timestamp": "2018-03-29T20:34:15.0000000Z",
      "Engine Oil Pressure": 0.58015072345733643,
      "unit": "psi"
}
```

Как и в полезных данных 1, TSI будет хранить каждое измеренное значение в качестве уникального события.  Существенным различием является то, что TSI будет считывать *метку времени*, как здесь, в формате ISO.  

Если вам нужно уменьшить количество отправленных событий, можно отправить информацию следующим образом.  

#### <a name="payload-3"></a>Полезные данные 3:
```json
{
      "line": "Line01",
      "station": "Station 11",
      "gatewayid": "AAAA-ZZ-001",
      "deviceid": "F12XX",
      "timestamp": "2018-03-29T20:34:15.0000000Z",
      "CALC Pump Rate": 0,
      "CALC Pump Rate.unit": "bbl/min"
      "Engine Oil Pressure": 0.58015072345733643,
      "Engine Oil Pressure.unit": "psi"
      "Engine Fuel Pressure": 0,
      "Engine Fuel Pressure.unit": "psi"
}
```
Одно окончательное предложение приведено ниже.

#### <a name="payload-4"></a>Полезные данные 4:
```json
{
              "line": "Line01",
              "station": "Station 11",
              "gatewayid": "AAAA-ZZ-001",
              "deviceid": "F12XX",
              "timestamp": "2018-03-29T20:34:15.0000000Z",
              "CALC Pump Rate": {
                           "value": 0,
                           "unit": "bbl/min"
              },
              "Engine Oil Pressure": {
                           "value": 0.58015072345733643,
                           "unit": "psi"
              },
              "Engine Fuel Pressure": {
                           "value": 0,
                           "unit": "psi"
              }
}
```

В этом примере показаны выходные данные после преобразования в плоскую структуру JSON:

```json
{
      "line": "Line01",
      "station": "Station 11",,
      "gatewayid": "AAAA-ZZ-001",
      "deviceid": "F12XX",
      "timestamp": "2018-03-29T20:34:15.0000000Z",
      "CALC Pump Rate.value": 0,
      "CALC Pump Rate.unit": "bbl/min"
      "Engine Oil Pressure.value": 0.58015072345733643,
      "Engine Oil Pressure.unit": "psi"
      "Engine Fuel Pressure.value": 0,
      "Engine Fuel Pressure.unit": "psi"
}
```

У вас есть возможность определить различные свойства для каждого канала в собственном объекте JSON, сохраняя при этом низкое количество событий. Плоская структура занимает больше места, что нужно учитывать. Емкость TSI основана на событиях и размере, в зависимости о того, какой предел будет достигнут первым.

## <a name="next-steps"></a>Дополнительная информация
> [!div class="nextstepaction"]
> [Просмотрите свою среду в обозревателе службы "Аналитика временных рядов"](https://insights.timeseries.azure.com).

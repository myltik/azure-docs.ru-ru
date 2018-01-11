---
title: "Как создать расписание триггеры в фабрике данных Azure | Документы Microsoft"
description: "Узнайте, как создать триггер в фабрике данных Azure, выполняется по расписанию конвейера."
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: 
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/11/2017
ms.author: shlo
ms.openlocfilehash: 6b92e8402d372e29e264dc70b128124973d66bb9
ms.sourcegitcommit: 1d423a8954731b0f318240f2fa0262934ff04bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/05/2018
---
# <a name="how-to-create-a-trigger-that-runs-a-pipeline-on-a-schedule"></a>Как создать триггер, который выполняет конвейера по расписанию
В этой статье описаны запланированный триггер и шаги для создания, запуска и наблюдения за триггер. Для триггеров других типов, в разделе [конвейера выполнения и триггеры](concepts-pipeline-execution-triggers.md).

> [!NOTE]
> Эта статья относится к версии 2 фабрики данных, которая в настоящее время доступна в предварительной версии. Если вы используете общедоступную версию 1 службы фабрики данных, см. статью о [начале работы со службой фабрики данных версии 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

### <a name="schedule-trigger-json-definition"></a>Запланированный триггер определения JSON
При создании запланированный триггер, можно указать расписание и шаблон повторений, с помощью JSON, как показано в примере в этом разделе.

Чтобы начнем конвейера выполнить триггер расписание, включайте ссылку конвейера конвейера, определенного в определении триггера. Конвейеры и триггеры имеют связь "многие ко многим". Несколько триггеров могут запускать один конвейер. Один триггер может запускать несколько конвейеров.

```json
{
  "properties": {
    "type": "ScheduleTrigger",
    "typeProperties": {
      "recurrence": {
        "frequency": <<Minute, Hour, Day, Week, Month>>,
        "interval": <<int>>,             // optional, how often to fire (default to 1)
        "startTime": <<datetime>>,
        "endTime": <<datetime - optional>>,
        "timeZone": "UTC"
        "schedule": {                    // optional (advanced scheduling specifics)
          "hours": [<<0-23>>],
          "weekDays": : [<<Monday-Sunday>>],
          "minutes": [<<0-59>>],
          "monthDays": [<<1-31>>],
          "monthlyOccurences": [
               {
                    "day": <<Monday-Sunday>>,
                    "occurrence": <<1-5>>
               }
           ]
        }
      }
    },
   "pipelines": [
            {
                "pipelineReference": {
                    "type": "PipelineReference",
                    "referenceName": "<Name of your pipeline>"
                },
                "parameters": {
                    "<parameter 1 Name>": {
                        "type": "Expression",
                        "value": "<parameter 1 Value>"
                    },
                    "<parameter 2 Name>" : "<parameter 2 Value>"
                }
           }
      ]
  }
}
```

> [!IMPORTANT]
>  Свойство **Параметры** является обязательным для **конвейеров**. Даже если ваш конвейер не принимает никаких параметров, включите для них пустой файл JSON, так как это свойство должно присутствовать.


### <a name="overview-schedule-trigger-schema"></a>Обзор: Расписание триггер схемы
Таблица ниже содержит обзор основных элементов, связанных с периодичностью выполнения и расписанием триггера.

Свойство JSON |     ОПИСАНИЕ
------------- | -------------
startTime | startTime имеет формат "дата и время". В простых расписаниях startTime указывает время первого запуска. В сложных расписаниях триггер не запускается раньше, чем startTime.
endTime | Указывает дату и время окончания для триггера. Триггер не будет выполняться позднее этого времени. Значение элемента endTime не может относиться к прошлому. Это необязательное свойство.
timeZone | В настоящее время поддерживается только формат UTC.
recurrence | Объект recurrence указывает правила повторения для триггера. Объект recurrence поддерживает следующие элементы: frequency, interval, endTime, count и schedule. Если задан объект recurrence, нужно обязательно указать элемент frequency. Остальные элементы объекта recurrence не являются обязательными.
frequency | Представляет единицу частоты, с которой выполняется триггер. Поддерживаются такие значения: `minute`, `hour`, `day`, `week` и `month`.
interval | interval содержит положительное целое число. Это число обозначает количество единиц частоты, через которые выполняется триггер. Например, если interval имеет значение 3, а для элемента frequency выбран вариант week (неделя), триггер выполняется один раз каждые 3 недели.
schedule | Триггер с указанной частотой выполняется по расписанию. Значение schedule содержит изменения с учетом минут, часов, дней недели, чисел месяца и количества недель.


### <a name="overview-schedule-trigger-schema-defaults-limits-and-examples"></a>Обзор: Планирование триггер настройки схемы по умолчанию, ограничения и примеры

Имя JSON | Тип значения | Обязательный? | Значение по умолчанию | Допустимые значения | Пример
--------- | ---------- | --------- | ------------- | ------------ | -------
startTime | Строка | Yes | Нет | Дата и время по спецификации ISO-8601 | ```"startTime" : "2013-01-09T09:30:00-08:00"```
recurrence | Объект. | Yes | Нет | Объект recurrence | ```"recurrence" : { "frequency" : "monthly", "interval" : 1 }```
interval | Number | Нет  | 1 | От 1 до 1000 | ```"interval":10```
endTime | Строка | Yes | Нет | Значение даты-времени, представляющее время в будущем | `"endTime" : "2013-02-09T09:30:00-08:00"`
schedule | Объект. | Нет  | None | Объект schedule | `"schedule" : { "minute" : [30], "hour" : [8,17] }`

### <a name="deep-dive-starttime"></a>Подробно: startTime
В приведенной ниже таблице показано, как параметр startTime определяет время запуска триггера.

Значение startTime | Повторение без расписания | Повторение с расписанием
--------------- | --------------------------- | ------------------------
Время начала в прошлом | Вычисляется время первого выполнения, относящееся к будущему времени, после указанного времени начала.<p>Последующие выполнения вычисляются с учетом времени предыдущего выполнения.</p><p>Пример представлен после этой таблицы.</p> | Триггер выполняется _не раньше_ указанного времени начала. Первое выполнение производится по расписанию, которое отсчитывается от времени начала. <p>Последующие выполнения производятся по расписанию повторов.</p>
Время начала в будущем или в настоящем | Выполняется первый раз в указанное время начала. <p>Последующие выполнения производятся с учетом времени предыдущего выполнения.</p> | Триггер выполняется _не раньше_ указанного времени начала. Первое выполнение производится по расписанию, которое отсчитывается от времени начала.<p>Последующие выполнения производятся по расписанию повторов.</p>

Рассмотрим, как работает триггер, для которого время startTime установлено в прошлом, указан параметр recurrence и отсутствует параметр schedule. Допустим, текущее время `2017-04-08 13:00`, startTime имеет значение `2017-04-07 14:00`, а recurrence определяет повторы через каждые два дня (то есть frequency имеет значение day, а interval равен 2). Обратите внимание, что время startTime находится в прошлом, то есть наступает раньше текущего времени.

В этих условиях первое выполнение происходит `2017-04-09 at 14:00`. От времени начала ядро планировщика отсчитывает время повторных выполнений. Выполнения, которые приходятся на прошлое, игнорируются. Ядро берет очередной случай выполнения, который приходится на будущее. В данном случае startTime имеет значение `2017-04-07 at 2:00pm`, поэтому следующее выполнение состоится через 2 дня от этого времени начала, то есть `2017-04-09 at 2:00pm`.

Время первого выполнения останется тем же даже при значениях startTime `2017-04-05 14:00` или `2017-04-01 14:00`. Все последующие выполнения после первого вычисляются по расписанию schedule. В этом примере они состоятся `2017-04-11 at 2:00pm`, затем `2017-04-13 at 2:00pm`, `2017-04-15 at 2:00pm` и т. д.

И, наконец, если у триггера есть расписание schedule, в котором не указаны часы и (или) минуты, используются значения часов и (или) минут первого выполнения.

### <a name="deep-dive-schedule"></a>Подробный обзор: schedule
С одной стороны, параметр schedule может ограничивать число выполнений триггера. Например, если триггеру назначена ежемесячная частота и параметр schedule, который запускает триггер только в 31-й день месяца, этот триггер будет выполняться только в те месяцы, в которых есть 31 день.

Обратите внимание, что параметр schedule может увеличивать число выполнений триггера. Например, если триггеру назначена ежемесячная частота и параметр schedule, который запускает триггер в 1-й и 2-й день месяца, этот триггер будет выполняться 1-го и 2-го числа каждого месяца, а не один раз в месяц.

Если в параметре schedule задано несколько элементов, они применяются в порядке от большего к меньшему: номер недели, число месяца, день недели, час и минута.

В следующей таблице элементы параметра schedule описаны подробно.


Имя JSON | ОПИСАНИЕ | Допустимые значения
--------- | ----------- | ------------
minutes | Минуты часа, в которые будет выполняться триггер. | <ul><li>Целое число </li><li>массив целых чисел</li></ul>
hours | Часы дня, в которые будет выполняться триггер. | <ul><li>Целое число </li><li>массив целых чисел</li></ul>
weekDays | Дни недели, в которые будет выполняться триггер. Указываются только при выборе еженедельной частоты. | <ul><li>Понедельник, вторник, среда, четверг, пятница, суббота, воскресенье</li><li>Массив любого значения (максимальное количество элементов в массиве — 7)</li></p>Без учета регистра</p>
monthlyOccurrences | Определяет, в какие числа месяца будет выполняться триггер. Указываются только при выборе ежемесячной частоты. | Массив объектов monthlyOccurence: `{ "day": day,  "occurrence": occurence }`. <p> Элемент day определяет день недели, в который будет выполняться триггер, например `{Sunday}` означает каждое воскресенье месяца. Обязательный элемент.<p>Элемент occurence определяет конкретное повторение дня в течение месяца, например `{Sunday, -1}` означает последнее воскресенье месяца. Необязательный элемент.
monthDays | День месяца, в который будет выполняться триггер. Указываются только при выборе ежемесячной частоты. | <ul><li>Любое значение <= -1 и >= -31</li><li>Любое значение >= 1 и <= 31</li><li>Массив значений</li>


## <a name="examples-recurrence-schedules"></a>Примеры: расписания повторений
Этот раздел содержит примеры расписания повторений с применением объекта schedule и его элементов.

Во всех представленных примерах для параметра interval предполагается значение 1. Кроме того, в каждом случае следует предполагать частоту, соответствующую параметру schedule. Например, нельзя использовать ежедневную частоту и параметр monthDays в расписании. Эти ограничения указаны в таблице в предыдущем разделе.

Пример | ОПИСАНИЕ
------- | -----------
`{"hours":[5]}` | Запускается каждый день в 05:00.
`{"minutes":[15], "hours":[5]}` | Запускается каждый день в 05:15.
`{"minutes":[15], "hours":[5,17]}` | Запускается каждый день в 05:15 и 17:15.
`{"minutes":[15,45], "hours":[5,17]}` | Запускается каждый день в 05:15, 05:45, 17:15 и 17:45.
`{"minutes":[0,15,30,45]}` | Запускается каждые 15 минут.
`{hours":[0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23]}` | Запускается каждый час. Этот триггер запускается каждый час. Минута выполнения определяется параметром startTime, если он указан, или временем создания. Например, если триггер запущен или создан (в зависимости от ситуации) в 12:25, он будет запускаться в 00:25, 01:25, 02:25, ..., 23:25. Это расписание работает так же, как триггер с частотой hour (час), значением 1 для параметра interval и без параметра schedule. Отличие в том, что такое расписание позволяет создать другие триггеры с другими значениями частоты и интервала. Например, если бы параметр frequency имел значение month, расписание выполнялось бы раз в месяц, а не каждый день, как в данном случае, когда параметр frequency имеет значение day.
`{"minutes":[0]}` | Выполняется с наступлением каждого часа. Этот триггер также выполняется каждый час, но ровно в момент его наступления (например, в 00:00, 01:00, 02:00 и т. д.). Такая настройка эквивалентна триггеру, в котором параметр frequency имеет значение hour, параметр startTime указан с нулевыми минутами, а расписание не задано (параметр frequency имеет значение day); если же параметр frequency имеет значение week или month, расписание будет выполняться только один раз в неделю или в месяц соответственно.
`{"minutes":[15]}` | Выполняется через 15 минут после наступления каждого часа. Задание выполняется каждый час, начиная с 00:15, 01:15, 02:15 и т. д. и заканчивая 22:15 и 23:15.
`{"hours":[17], "weekDays":["saturday"]}` | Выполняется в 17:00 каждую субботу.
`{"hours":[17], "weekDays":["monday", "wednesday", "friday"]}` | Выполняется в 17:00 каждый понедельник, среду и пятницу.
`{"minutes":[15,45], "hours":[17], "weekDays":["monday", "wednesday", "friday"]}` | Выполняется в 17:15 и 17:45 каждый понедельник, среду и пятницу.
`{"minutes":[0,15,30,45], "weekDays":["monday", "tuesday", "wednesday", "thursday", "friday"]}` | Задание выполняется каждые 15 минут в каждый рабочий день.
`{"minutes":[0,15,30,45], "hours": [9, 10, 11, 12, 13, 14, 15, 16] "weekDays":["monday", "tuesday", "wednesday", "thursday", "friday"]}` | Выполняется каждые 15 минут в период с 09:00 до 16:45 каждый рабочий день.
`{"weekDays":["tuesday", "thursday"]}` | Выполняется по вторникам и четвергам в указанное время начала.
`{"minutes":[0], "hours":[6], "monthDays":[28]}` | Выполняется в 06:00 28-го числа каждого месяца (при ежемесячной частоте).
`{"minutes":[0], "hours":[6], "monthDays":[-1]}` | Выполняется в 06:00 в последний день каждого месяца. Если вы хотите, чтобы триггер запускался в последний день месяца, используйте -1 вместо значения 28, 29, 30 или 31.
`{"minutes":[0], "hours":[6], "monthDays":[1,-1]}` | Выполняется в 06:00 в первый и последний день каждого месяца.
`{monthDays":[1,14]}` | Выполняется в первый и 14-й день каждого месяца в указанное время начала.
`{"minutes":[0], "hours":[5], "monthlyOccurrences":[{"day":"friday", "occurrence":1}]}` | Выполняется в первую пятницу каждого месяца в 05:00.
`{"monthlyOccurrences":[{"day":"friday", "occurrence":1}]}` | Выполняется в первую пятницу каждого месяца в указанное время начала.
`{"monthlyOccurrences":[{"day":"friday", "occurrence":-3}]}` | Выполняется в третью пятницу с конца месяца, каждый месяц в указанное время начала.
`{"minutes":[15], "hours":[5], "monthlyOccurrences":[{"day":"friday", "occurrence":1},{"day":"friday", "occurrence":-1}]}` | Выполняется в первую и последнюю пятницу каждого месяца в 05:15.
`{"monthlyOccurrences":[{"day":"friday", "occurrence":1},{"day":"friday", "occurrence":-1}]}` | Выполняется в первую и последнюю пятницу каждого месяца в указанное время начала.
`{"monthlyOccurrences":[{"day":"friday", "occurrence":5}]}` | Выполняется в пятую пятницу каждого месяца в указанное время начала. Если пятой пятницы в месяце нет, конвейер не выполняется, поскольку по расписанию оно должно выполняться только по пятым пятницам.  Если вы хотите, чтобы триггер выполнялся в последнюю пятницу месяца, используйте вместо значения 5 значение -1.
`{"minutes":[0,15,30,45], "monthlyOccurrences":[{"day":"friday", "occurrence":-1}]}` | Выполняется каждые 15 минут в последнюю пятницу месяца.
`{"minutes":[15,45], "hours":[5,17], "monthlyOccurrences":[{"day":"wednesday", "occurrence":3}]}` | Выполняется в 05:15, 05:45, 17:15 и 17:45 третьей среды каждого месяца.


## <a name="use-azure-powershell"></a>Использование Azure PowerShell
В этом разделе показано, как использовать Azure PowerShell для создания, запуска и наблюдения за триггер планировщика. Чтобы увидеть работу этого образца следует пройти [краткое руководство: создание фабрики данных с помощью Azure PowerShell](quickstart-create-data-factory-powershell.md). Затем добавьте следующий код в методе main, который создает и запускает запланированный триггер, который выполняется каждые 15 минут. Триггер связан с конвейером (**Adfv2QuickStartPipeline**), создаваемых в рамках краткого руководства.

1. Создайте JSON-файл с именем MyTrigger.json в папке C:\ADFv2QuickStartPSH\ со следующим содержимым:

    > [!IMPORTANT]
    > Задать **startTime** текущее время в формате UTC и **endTime** на один час после текущего времени в формате UTC, время до сохранения файла JSON.

    ```json   
    {
        "properties": {
            "name": "MyTrigger",
            "type": "ScheduleTrigger",
            "typeProperties": {
                "recurrence": {
                    "frequency": "Minute",
                    "interval": 15,
                    "startTime": "2017-12-08T00:00:00",
                    "endTime": "2017-12-08T01:00:00"
                }
            },
            "pipelines": [{
                    "pipelineReference": {
                        "type": "PipelineReference",
                        "referenceName": "Adfv2QuickStartPipeline"
                    },
                    "parameters": {
                        "inputPath": "adftutorial/input",
                        "outputPath": "adftutorial/output"
                    }
                }
            ]
        }
    }
    ```

    Фрагмент JSON:
    - **Тип** триггер имеет значение **ScheduleTrigger**.
    - **Частоты** равно **минуту** и **интервал** равно **15**. Таким образом триггер выполняется конвейера каждые 15 минут между значениями времени начала и окончания.
    - **EndTime** составляет один час после **startTime**, поэтому выполняется триггер конвейера 15 минут, 30 минут и 45 минут позже времени начала. Не забудьте обновить свойство startTime текущее время в формате UTC и endTime в один час после времени начала.  
    - Триггер, связанные с **Adfv2QuickStartPipeline** конвейера. Чтобы связать несколько конвейеров с триггера, добавить дополнительные **pipelineReference** разделы.
    - Конвейер в кратком руководстве принимает два **параметры**. Таким образом можно передавать значения для этих параметров из триггера.
2. Создание триггера с помощью **AzureRmDataFactoryV2Trigger набор** командлета.

    ```powershell
    Set-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger" -DefinitionFile "C:\ADFv2QuickStartPSH\MyTrigger.json"
    ```
3. Убедитесь, что состояние триггера **остановлена** с помощью **Get AzureRmDataFactoryV2Trigger** командлета.

    ```powershell
    Get-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"
    ```
4. Запуск триггера с помощью **AzureRmDataFactoryV2Trigger начала** командлета:

    ```powershell
    Start-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"
    ```
5. Убедитесь, что состояние триггера **Started** с помощью **Get AzureRmDataFactoryV2Trigger** командлета.

    ```powershell
    Get-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"
    ```
6.  Получить триггер запускается с помощью PowerShell с помощью **Get AzureRmDataFactoryV2TriggerRun** командлета. Чтобы получить сведения о запускается триггер, выполните следующую команду периодически: обновление **TriggerRunStartedAfter** и **TriggerRunStartedBefore** значения в соответствии со значениями в определении триггера .

    ```powershell
    Get-AzureRmDataFactoryV2TriggerRun -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -TriggerName "MyTrigger" -TriggerRunStartedAfter "2017-12-08T00:00:00" -TriggerRunStartedBefore "2017-12-08T01:00:00"
    ```

    Мониторинге триггер запускается/конвейера выполняется на портале Azure см. в разделе [запускается монитор конвейера](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline)

## <a name="use-net-sdk"></a>Использование пакета SDK для .NET
В этом разделе показано, как использовать пакет SDK .NET для создания, запуска и наблюдения за триггера. Если вы хотите просмотреть этот код работает, сначала выполните [краткое руководство по созданию фабрики данных, с помощью пакета SDK .NET](quickstart-create-data-factory-dot-net.md). Затем добавьте следующий код в методе main, который создает и запускает запланированный триггер, который выполняется каждые 15 минут. Триггер связан с конвейером (**Adfv2QuickStartPipeline**), создаваемых в рамках краткого руководства.

```csharp
            //create the trigger
            Console.WriteLine("Creating the trigger");

            // set the start time to the current UTC time
            DateTime startTime = DateTime.UtcNow;

            // specify values for the inputPath and outputPath parameters
            Dictionary<string, object> pipelineParameters = new Dictionary<string, object>();
            pipelineParameters.Add("inputPath", "adftutorial/input");
            pipelineParameters.Add("outputPath", "adftutorial/output");

            // create a schedule trigger
            string triggerName = "MyTrigger";
            ScheduleTrigger myTrigger = new ScheduleTrigger()
            {
                Pipelines = new List<TriggerPipelineReference>()
                {
                    // associate the Adfv2QuickStartPipeline pipeline with the trigger
                    new TriggerPipelineReference()
                    {
                        PipelineReference = new PipelineReference(pipelineName),
                        Parameters = pipelineParameters,
                    }
                },
                Recurrence = new ScheduleTriggerRecurrence()
                {
                    // set the start time to current UTC time and the end time to be one hour after.
                    StartTime = startTime,
                    TimeZone = "UTC",
                    EndTime = startTime.AddHours(1),
                    Frequency = RecurrenceFrequency.Minute,
                    Interval = 15,
                }
            };

            // now, create the trigger by invoking CreateOrUpdate method.
            TriggerResource triggerResource = new TriggerResource()
            {
                Properties = myTrigger
            };
            client.Triggers.CreateOrUpdate(resourceGroup, dataFactoryName, triggerName, triggerResource);

            //start the trigger
            Console.WriteLine("Starting the trigger");
            client.Triggers.Start(resourceGroup, dataFactoryName, triggerName);

```

Для мониторинга выполнения триггера, добавьте следующий код перед удалением последней `Console.WriteLine` инструкции:

```csharp
            // Check the trigger runs every 15 minutes
            Console.WriteLine("Trigger runs. You see the output every 15 minutes");

            for (int i = 0; i < 3; i++)
            {
                System.Threading.Thread.Sleep(TimeSpan.FromMinutes(15));
                List<TriggerRun> triggerRuns = client.Triggers.ListRuns(resourceGroup, dataFactoryName, triggerName, DateTime.UtcNow.AddMinutes(-15 * (i + 1)), DateTime.UtcNow.AddMinutes(2)).ToList();
                Console.WriteLine("{0} trigger runs found", triggerRuns.Count);

                foreach (TriggerRun run in triggerRuns)
                {
                    foreach (KeyValuePair<string, string> triggeredPipeline in run.TriggeredPipelines)
                    {
                        PipelineRun triggeredPipelineRun = client.PipelineRuns.Get(resourceGroup, dataFactoryName, triggeredPipeline.Value);
                        Console.WriteLine("Pipeline run ID: {0}, Status: {1}", triggeredPipelineRun.RunId, triggeredPipelineRun.Status);
                        List<ActivityRun> runs = client.ActivityRuns.ListByPipelineRun(resourceGroup, dataFactoryName, triggeredPipelineRun.RunId, run.TriggerRunTimestamp.Value, run.TriggerRunTimestamp.Value.AddMinutes(20)).ToList();
                    }
                }
            }
```

Мониторинге триггер запускается/конвейера выполняется на портале Azure см. в разделе [запускается монитор конвейера](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline)

## <a name="use-python-sdk"></a>С помощью пакета SDK для Python
В этом разделе показано, как использовать пакет SDK для Python для создания, запуска и наблюдения за триггера. Если вы хотите просмотреть этот код работает, сначала выполните [краткое руководство по созданию фабрики данных, с помощью пакета SDK для Python](quickstart-create-data-factory-python.md). Затем добавьте следующий блок кода после «мониторинг выполнения конвейера» блок кода в сценарий python. Этот код создает запланированный триггер, который выполняется каждые 15 минут между указанными начала и окончания. Обновите start_time в текущее время в формате UTC и end_time на один час после текущего времени UTC.

```python
    # Create a trigger
    tr_name = 'mytrigger'
    scheduler_recurrence = ScheduleTriggerRecurrence(frequency='Minute', interval='15',start_time='2017-12-12T04:00:00', end_time='2017-12-12T05:00:00', time_zone='UTC')
    pipeline_parameters = {'inputPath':'adftutorial/input', 'outputPath':'adftutorial/output'}
    pipelines_to_run = []
    pipeline_reference = PipelineReference('copyPipeline')
    pipelines_to_run.append(TriggerPipelineReference(pipeline_reference, pipeline_parameters))
    tr_properties = ScheduleTrigger(description='My scheduler trigger', pipelines = pipelines_to_run, recurrence=scheduler_recurrence)    
    adf_client.triggers.create_or_update(rg_name, df_name, tr_name, tr_properties)

    # start the trigger
    adf_client.triggers.start(rg_name, df_name, tr_name)
```
Мониторинге триггер запускается/конвейера выполняется на портале Azure см. в разделе [запускается монитор конвейера](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline)

## <a name="use-resource-manager-template"></a>Использование шаблона Resource Manager
Шаблон диспетчера ресурсов Azure можно использовать для создания триггера. Пошаговые инструкции см. в разделе [создания фабрики данных Azure с помощью шаблона диспетчера ресурсов](quickstart-create-data-factory-resource-manager-template.md).  

## <a name="pass-the-trigger-start-time-to-a-pipeline"></a>Время начала триггер передается в конвейер
В версии 1 фабрика данных Azure поддерживала чтение или запись секционированных данных с использованием системных переменных SliceStart/SliceEnd/WindowStart/WindowEnd. В версии 2 это достигается с помощью параметра конвейера и времени начала или запланированного времени запуска триггера в качестве значения параметра. В следующем примере триггера запланированного времени передается как значение параметра scheduledRunTime конвейера.

```json
"parameters": {
    "scheduledRunTime": "@trigger().scheduledTime"
}
```    
Дополнительные сведения см. в разделе [как для чтения или записи секционированных данных](how-to-read-write-partitioned-data.md).

## <a name="next-steps"></a>Дальнейшие действия
Подробные сведения о триггерах см. в разделе [конвейера выполнения и триггеры](concepts-pipeline-execution-triggers.md#triggers).

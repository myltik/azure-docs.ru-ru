---
title: "Функции и системные переменные фабрики данных | Документация Майкрософт"
description: "Содержит список функций и системных переменных фабрики данных Azure."
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: monicar
services: data-factory
ms.assetid: b6b3c2ae-b0e8-4e28-90d8-daf20421660d
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2017
ms.author: shlo
ms.translationtype: Human Translation
ms.sourcegitcommit: e72275ffc91559a30720a2b125fbd3d7703484f0
ms.openlocfilehash: 95ffafb276009f0acfa9cd96b9d4e575bd6a9d28
ms.contentlocale: ru-ru
ms.lasthandoff: 05/05/2017


---
# <a name="azure-data-factory---functions-and-system-variables"></a>Фабрика данных Azure — функции и системные переменные
В этой статье содержатся сведения о функциях и переменных, поддерживаемых фабрикой данных Azure.

## <a name="data-factory-system-variables"></a>Системные переменные фабрики данных
| Имя переменной | Описание | Область объекта | Область JSON и примеры использования |
| --- | --- | --- | --- |
| WindowStart |Начало интервала времени для текущего окна запуска действия. |действие |<ol><li>Укажите запросы на выбор данных. Ознакомьтесь со статьями о соединителях, упомянутых в статье [Действия перемещения данных](data-factory-data-movement-activities.md).</li> |
| WindowEnd |Конец интервала времени для текущего окна запуска действия. |действие |то же, что и выше |
| SliceStart |Начало интервала времени для формируемого среза данных. |действие<br/>набор данных |<ol><li>Укажите динамические пути к папкам и имена файлов при работе с [большими двоичными объектами Azure](data-factory-azure-blob-connector.md) и [наборами данных файловой системы](data-factory-onprem-file-system-connector.md).</li><li>Укажите входные зависимости с помощью функций фабрики данных в коллекции входных данных действия.</li></ol> |
| SliceEnd |Конец интервала времени для текущего формируемого среза данных. |действие<br/>dataset |То же, что и для SliceStart. |

> [!NOTE]
> В настоящее время для фабрики данных требуется, чтобы расписание, указанное в действии, в точности соответствовало расписанию, указанному в разделе доступности выходного набора данных. Следовательно, WindowStart, WindowEnd, SliceStart и SliceEnd всегда сопоставляются с одним и тем же периодом времени и отдельным выходным срезом.
> 

### <a name="example-for-using-a-system-variable"></a>Пример использования системной переменной
В следующем примере год, месяц, день и время **SliceStart** извлекаются в отдельные переменные, используемые в свойствах **folderPath** и **fileName**.

```json
"folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
"fileName": "{Hour}.csv",
"partitionedBy":
 [
    { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
    { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } },
    { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } },
    { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } }
],
```

## <a name="data-factory-functions"></a>Функции фабрики данных
Функции фабрики данных можно использовать с системными переменными для следующих целей:

1. Указание запросов выбора данных (см. статьи о соединителях, на которые ссылается статья [Действия перемещения данных](data-factory-data-movement-activities.md)).
   
   Синтаксис, необходимый для вызова функции фабрики данных, — это **$$<function>** (используется для запросов выбора данных и других свойств действий и наборов данных).  
2. Указание входных зависимостей с помощью функций фабрики данных в коллекции входных данных действия.
   
    Префикс $$ не требуется при указании выражений входных зависимостей.     

В следующем примере свойству **sqlReaderQuery** в JSON-файле присваивается значение, возвращаемое функцией `Text.Format`. Кроме того, в этом примере используется системная переменная с именем **WindowStart**, которая отображает время начала для окна запуска действия.

```json
{
    "Type": "SqlSource",
    "sqlReaderQuery": "$$Text.Format('SELECT * FROM MyTable WHERE StartTime = \\'{0:yyyyMMdd-HH}\\'', WindowStart)"
}
```

В разделе [Строки настраиваемых форматов даты и времени](https://msdn.microsoft.com/library/8kb3ddd4.aspx) описаны различные варианты форматирования, которые можно использовать (пример: гг и гггг). 

### <a name="functions"></a>Функции
В приведенных ниже таблицах перечислены все функции фабрики данных Azure.

| Категория | Функция | Параметры | Описание |
| --- | --- | --- | --- |
| Время |AddHours(X,Y) |X: DateTime  <br/><br/>Y: int |Добавляет Y часов к заданному времени X. <br/><br/>Пример: 05.09.2013 12:00:00 + 2 часа = 05.09.2013 14:00:00. |
| Время |AddMinutes(X,Y) |X: DateTime  <br/><br/>Y: int |Добавляет Y минут к Х.<br/><br/>Пример: 05.09.2013 12:00:00 + 15 минут = 05.09.2013 12:15:00. |
| Время |StartOfHour(X) |X: DateTime  |Получает то время, когда начинался час, отображаемый компонентом часа в параметре X. <br/><br/>Пример: параметр StartOfHour для 15.09.2013 17:10:23 имеет значение 15.09.2013 17:00:00. |
| Дата |AddDays(X,Y) |X: DateTime <br/><br/>Y: int |Добавляет Y дней к Х. <br/><br/>Пример: 15.09.2013 12:00:00 + 2 дня = 17.09.2013 12:00:00.<br/><br/>Можно также вычитать дни, указав в качестве Y отрицательное число.<br/><br/>Пример: 15.09.2013 12:00:00 – 2 дня = 13.09.2013 12:00:00. |
| Дата |AddMonths(X,Y) |X: DateTime <br/><br/>Y: int |Добавляет Y месяцев к Х.<br/><br/>Пример: 15.09.2013 12:00:00 + 1 месяц = 17.10.2013 12:00:00.<br/><br/>Можно также вычитать месяцы, указав в качестве Y отрицательное число.<br/><br/>Пример: 15.09.2013 12:00:00 – 1 месяц = 15.08.2013 12:00:00.|
| Дата |AddQuarters(X,Y) |X: DateTime  <br/><br/>Y: int |Добавляет Y * 3 месяцев к Х.<br/><br/>Пример: 15.09.2013 12:00:00 + 1 квартал = 15.12.2013 12:00:00. |
| Дата |AddWeeks(X,Y) |X: DateTime <br/><br/>Y: int |Добавляет Y (одна единица которого равна 7 дням) к X<br/><br/>Пример: 15.09.2013 12:00:00 + 1 неделя = 22.09.2013 12:00:00.<br/><br/>Можно также вычитать недели, указав в качестве Y отрицательное число.<br/><br/>Пример: 15.09.2013 12:00:00 – 1 неделя = 07.09.2013 12:00:00. |
| Дата |AddYears(X,Y) |X: DateTime <br/><br/>Y: int |Добавляет Y лет к Х.<br/><br/>Пример: 15.09.2013 12:00:00 + 1 год = 15.09.2014 12:00:00.<br/><br/>Можно также вычитать года, указав в качестве Y отрицательное число.<br/><br/>Пример: 15.09.2013 12:00:00 – 1 год = 15.09.2012 12:00:00. |
| Дата |Day(X) |X: DateTime  |Возвращает значение дня для X.<br/><br/>Пример: для 15.09.2013 12:00:00 значение Day равно 9. |
| Дата |DayOfWeek(X) |X: DateTime  |Возвращает значение дня недели для X.<br/><br/>Пример: для 15.09.2013 12:00:00 DayOfWeek равно Sunday. |
| Дата |DayOfYear(X) |X: DateTime  |Получает день года, представленный значением года параметра X.<br/><br/>Примеры:<br/>01.12.2015: 335 день 2015 года;<br/>31.12.2015: 365 день 2015 года;<br/>31.12.2016: 366 день 2016 года (високосного). |
| Дата |DaysInMonth(X) |X: DateTime  |Получает количество дней в месяце, представленном значением месяца параметра X.<br/><br/>Пример: для 15.09.2013 значение DaysInMonth равно 30, так как в сентябре 30 дней. |
| Дата |EndOfDay(X) |X: DateTime  |Получает значение даты и времени, которое соответствует окончанию суток (значения дня) в параметре X.<br/><br/>Пример: EndOfDay для 15.09.2013 17:10:23 имеет значение 15.09.2013 23:59:59. |
| Дата |EndOfMonth(X) |X: DateTime  |Получает значение конца месяца, представленного значением месяца параметра X. <br/><br/>Пример: для 15.09.2013 05:10:23 EndOfMonth имеет значение 30.09.2013 23:59:59 (дата и время конца сентября). |
| Дата |StartOfDay(X) |X: DateTime  |Получает начало суток, представленных значением дня в параметре X.<br/><br/>Пример: StartOfDay для 15.09.2013 17:10:23 имеет значение 15.09.2013 00:00:00. |
| DateTime |From(X) |X: String |Разбор строки X в значение даты и времени. |
| DateTime |Ticks(X) |X: DateTime  |Получает свойство тактов времени параметра X. Один такт равен 100 наносекундам. Значение этого свойства соответствует количеству тактов, прошедших с полуночи (24:00:00) 1 января 0001 года. |
| текст |Format(X) |X: String (переменная) |Форматирует текст (используйте комбинацию `\\'` для экранирования символа `'`).|

> [!IMPORTANT]
> При использовании функции внутри другой функции нет необходимости использовать для внутренней функции префикс **$$** . Например, $$Text.Format('PartitionKey eq \\'my_pkey_filter_value\\' and RowKey ge \\'{0:yyyy-MM-dd HH:mm:ss}\\'', Time.AddHours(SliceStart, -6)). В этом примере обратите внимание, что префикс **$$** не используется для функции **Time.AddHours**. 

#### <a name="example"></a>Пример
В следующем примере входные и выходные параметры для действия Hive определяются с помощью функции Text.Format и системной переменной SliceStart. 

```json  
{
    "name": "HiveActivitySamplePipeline",
        "properties": {
    "activities": [
            {
            "name": "HiveActivitySample",
            "type": "HDInsightHive",
            "inputs": [
                    {
                    "name": "HiveSampleIn"
                    }
            ],
            "outputs": [
                    {
                    "name": "HiveSampleOut"
                }
            ],
            "linkedServiceName": "HDInsightLinkedService",
            "typeproperties": {
                    "scriptPath": "adfwalkthrough\\scripts\\samplehive.hql",
                    "scriptLinkedService": "StorageLinkedService",
                    "defines": {
                        "Input": "$$Text.Format('wasb://adfwalkthrough@<storageaccountname>.blob.core.windows.net/samplein/yearno={0:yyyy}/monthno={0:MM}/dayno={0:dd}/', SliceStart)",
                        "Output": "$$Text.Format('wasb://adfwalkthrough@<storageaccountname>.blob.core.windows.net/sampleout/yearno={0:yyyy}/monthno={0:MM}/dayno={0:dd}/', SliceStart)"
                    },
                    "scheduler": {
                        "frequency": "Hour",
                        "interval": 1
                }
            }
            }
    ]
    }
}
```

### <a name="example-2"></a>Пример 2

В следующем примере параметр DateTime для действия хранимой процедуры определяется с помощью функции Text.Format и системной переменной SliceStart. 

```json
{
    "name": "SprocActivitySamplePipeline",
    "properties": {
        "activities": [
            {
                "type": "SqlServerStoredProcedure",
                "typeProperties": {
                    "storedProcedureName": "sp_sample",
                    "storedProcedureParameters": {
                        "DateTime": "$$Text.Format('{0:yyyy-MM-dd HH:mm:ss}', SliceStart)"
                    }
                },
                "outputs": [
                    {
                        "name": "sprocsampleout"
                    }
                ],
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "SprocActivitySample"
            }
        ],
            "start": "2016-08-02T00:00:00Z",
            "end": "2016-08-02T05:00:00Z",
        "isPaused": false
    }
}
```

### <a name="example-3"></a>Пример 3
Для чтения данных за предыдущий день, а не день, указанный в SliceStart, используйте функцию AddDays, как показано в следующем примере. 

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-01-01T08:00:00",
        "end": "2017-01-01T11:00:00",
        "description": "hive activity",
        "activities": [
            {
                "name": "SampleHiveActivity",
                "inputs": [
                    {
                        "name": "MyAzureBlobInput",
                        "startTime": "Date.AddDays(SliceStart, -1)",
                        "endTime": "Date.AddDays(SliceEnd, -1)"
                    }
                ],
                "outputs": [
                    {
                        "name": "MyAzureBlobOutput"
                    }
                ],
                "linkedServiceName": "HDInsightLinkedService",
                "type": "HDInsightHive",
                "typeProperties": {
                    "scriptPath": "adftutorial\\hivequery.hql",
                    "scriptLinkedService": "StorageLinkedService",
                    "defines": {
                        "Year": "$$Text.Format('{0:yyyy}',WindowsStart)",
                        "Month": "$$Text.Format('{0:MM}',WindowStart)",
                        "Day": "$$Text.Format('{0:dd}',WindowStart)"
                    }
                },
                "scheduler": {
                    "frequency": "Day",
                    "interval": 1
                },
                "policy": {
                    "concurrency": 1,
                    "executionPriorityOrder": "OldestFirst",
                    "retry": 2,
                    "timeout": "01:00:00"
                }
            }
        ]
    }
}
```

В разделе [Строки настраиваемых форматов даты и времени](https://msdn.microsoft.com/library/8kb3ddd4.aspx) описаны различные варианты форматирования, которые можно использовать (пример: гг и гггг). 



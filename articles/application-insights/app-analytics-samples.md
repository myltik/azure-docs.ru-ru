<properties 
	pageTitle="Примеры запросов в аналитике Application Insights: аналитика" 
	description="Примеры запросов в аналитике Application Insights, мощном инструменте поиска Application Insights." 
	services="application-insights" 
    documentationCenter=""
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/21/2016" 
	ms.author="awills"/>


# Примеры аналитики для Application Insights

[Аналитика](app-analytics.md) — это мощная поисковая система в данных телеметрии [Application Insights](app-insights-overview.md). На этих страницах описан язык запросов аналитики приложений. Также существует [обзор языка](app-analytics-tour.md), с которого удобно начать работу.


[AZURE.INCLUDE [app-analytics-top-index](../../includes/app-analytics-top-index.md)]

## Рекомендации по запросам 

Существуют некоторые рекомендации и запреты, которые позволят ускорить выполнение запроса.

РЕКОМЕНДАЦИИ:

-	Сначала используйте фильтры времени. Аналитика Application Insights хорошо оптимизирована для использования фильтров времени.
-	Помещайте фильтры, которые должны отфильтровать наибольший объем данных, в начале запроса (сразу после фильтров времени).
-	Убедитесь, что большинство фильтров находятся в начале запроса (перед использованием "расширения"). 
-	При поиске полных маркеров ключевое слово "has" предпочтительнее "contains". "has" обладает большей производительностью, так как не ищет подстроки.
-	Используйте поиск в определенных столбцах вместо "*" (полнотекстового поиска по всем столбцам).
-	При использовании соединения нужно указывать только столбцы с обеих сторон соединения (это позволит снизить объем полезных данных, передаваемых с одного компьютера на другой).

ЗАПРЕТЫ:

-	Не пробуйте выполнять новые запросы, не указав в конце запроса "limit [небольшое число]" или "count". Выполнение запросов без ограничений на неизвестном наборе данных может привести к тому, что клиенту будет возвращено несколько ГБ результатов, что приведет к замедлению ответов и перегрузке кластера.
-	Если вы обнаружили, что вы применяете преобразования (JSON, строки и т. д.) к записям 1B+, измените запрос так, чтобы сократить объем данных, подвергаемых преобразованию.





<a name="activities"></a>
## Получение сеансов от событий начала и окончания

Предположим, что у нас есть журнал событий, в котором некоторые события отмечают начало или конец расширенного действия или сеанса.

|Имя|City|SessionId|Timestamp|
|---|---|---|---|
|Начало|Лондон|2817330|2015-12-09T10:12:02.32|
|Игра|Лондон|2817330|2015-12-09T10:12:52.45|
|Начало|Манчестер|4267667|2015-12-09T10:14:02.23|
|Остановить|Лондон|2817330|2015-12-09T10:23:43.18|
|Отмена|Манчестер|4267667|2015-12-09T10:27:26.29|
|Остановить|Манчестер|4267667|2015-12-09T10:28:31.72|

У каждого события есть идентификатор сеанса SessionId, поэтому задача состоит в том, чтобы сопоставить события начала и конца с одинаковыми идентификаторами.

```AIQL
let Events = MyLogTable | where ... ;

Events
| where Name == "Start"
| project Name, City, SessionId, StartTime=timestamp
| join (Events 
        | where Name="Stop"
        | project StopTime=timestamp, SessionId) 
    on activityId
| project City, SessionId, StartTime, StopTime, Duration = StopTime - StartTime
```

Используйте [`let`](app-analytics-syntax.md#let-statements) в качестве имени для проекции таблицы, которая максимально сокращена, перед выполнением соединения. [`Project`](app-analytics-queries.md#project-operator) используется для изменения имен временных меток, чтобы время начала и окончания отображались в результатах. Также выбираются другие столбцы, которые мы бы хотели видеть в результатах. [`join`](app-analytics-queries.md#join-operator) сопоставляет записи начала и окончания для одного и того же действия, создавая строку для каждого действия. Наконец, `project` снова добавляет столбец для отображения длительности действия.


|City|SessionId|StartTime|StopTime|Длительность|
|---|---|---|---|---|
|Лондон|2817330|2015-12-09T10:12:02.32|2015-12-09T10:23:43.18|00:11:40.46|
|Манчестер|4267667|2015-12-09T10:14:02.23|2015-12-09T10:28:31.72|00:14:29.49|

### Получение сеансов без идентификатора сеанса

Теперь предположим, что у событий начала и окончания нет идентификатора сеанса, который мы можем использовать. Но у нас есть IP-адрес клиента, на котором выполнялся сеанс. Если каждый клиент проводит только один сеанс одновременно, то можно сопоставить каждое событие начала следующему событию окончания с того же IP-адреса.

```AIQL
Events 
| where Name == "Start" 
| project City, ClientIp, StartTime = timestamp
| join  kind=inner
    (Events
    | where Name == "Stop" 
    | project StopTime = timestamp, ClientIp)
    on ClientIp
| extend duration = StopTime - StartTime 
    // Remove matches with earlier stops:
| where  duration > 0  
    // Pick out the earliest stop for each start and client:
| summarize argmin(duration, *) by bin(StartTime,1s), ClientIp
```

Соединение будет сопоставлять каждое событие начала всем событиям окончания с того же IP-адреса клиента. Поэтому сначала удалим соответствия с более ранним временем окончания.

Затем выполним группировку по времени начала и IP-адресу, чтобы получить группу для каждого сеанса. Мы должны указать функцию `bin` для параметра StartTime. Если этого не сделать, аналитика автоматически использует группы длительностью 1 час, что приведет к сопоставлению некоторых событий начала с неправильными событиями окончания.

`argmin` выбирает строку с наименьшей длительностью в каждой группе, а параметр `*` передается через все другие столбцы, хотя он добавляет префикс "min\_" к имени каждого столбца.


![](./media/app-analytics-samples/040.png)

Затем можно добавить код для подсчета длительности в группах удобного размера. Мы используем небольшие предпочтения для линейчатой диаграммы, поэтому разделим показатели на `1s` для преобразования временных интервалов в числа.


      // Count the frequency of each duration:
    | summarize count() by duration=bin(min_duration/1s, 10) 
      // Cut off the long tail:
    | where duration < 300
      // Prepare for display in a bar chart:
    | sort by duration asc 


![](./media/app-analytics-samples/050.png)


### Реальный пример

```AIQL

Logs  
| filter ActivityId == "ActivityId with Blablabla" 
| summarize max(Timestamp), min(Timestamp)  
| extend Duration = max_Timestamp - min_Timestamp 
 
wabitrace  
| filter Timestamp >= datetime(2015-01-12 11:00:00Z)  
| filter Timestamp < datetime(2015-01-12 13:00:00Z)  
| filter EventText like "NotifyHadoopApplicationJobPerformanceCounters"  	 
| extend Tenant = extract("tenantName=([^,]+),", 1, EventText) 
| extend Environment = extract("environmentName=([^,]+),", 1, EventText)  
| extend UnitOfWorkId = extract("unitOfWorkId=([^,]+),", 1, EventText)  
| extend TotalLaunchedMaps = extract("totalLaunchedMaps=([^,]+),", 1, EventText, typeof(real))  
| extend MapsSeconds = extract("mapsMilliseconds=([^,]+),", 1, EventText, typeof(real)) / 1000 
| extend TotalMapsSeconds = MapsSeconds  / TotalLaunchedMaps 
| filter Tenant == 'DevDiv' and Environment == 'RollupDev2'  
| filter TotalLaunchedMaps > 0 
| summarize sum(TotalMapsSeconds) by UnitOfWorkId  
| extend JobMapsSeconds = sum_TotalMapsSeconds * 1 
| project UnitOfWorkId, JobMapsSeconds 
| join ( 
wabitrace  
| filter Timestamp >= datetime(2015-01-12 11:00:00Z)  
| filter Timestamp < datetime(2015-01-12 13:00:00Z)  
| filter EventText like "NotifyHadoopApplicationJobPerformanceCounters"  
| extend Tenant = extract("tenantName=([^,]+),", 1, EventText) 
| extend Environment = extract("environmentName=([^,]+),", 1, EventText)  
| extend UnitOfWorkId = extract("unitOfWorkId=([^,]+),", 1, EventText)   
| extend TotalLaunchedReducers = extract("totalLaunchedReducers=([^,]+),", 1, EventText, typeof(real)) 
| extend ReducesSeconds = extract("reducesMilliseconds=([^,]+)", 1, EventText, typeof(real)) / 1000 
| extend TotalReducesSeconds = ReducesSeconds / TotalLaunchedReducers 
| filter Tenant == 'DevDiv' and Environment == 'RollupDev2'  
| filter TotalLaunchedReducers > 0 
| summarize sum(TotalReducesSeconds) by UnitOfWorkId  
| extend JobReducesSeconds = sum_TotalReducesSeconds * 1 
| project UnitOfWorkId, JobReducesSeconds ) 
on UnitOfWorkId 
| join ( 
wabitrace  
| filter Timestamp >= datetime(2015-01-12 11:00:00Z)  
| filter Timestamp < datetime(2015-01-12 13:00:00Z)  
| filter EventText like "NotifyHadoopApplicationJobPerformanceCounters"  
| extend Tenant = extract("tenantName=([^,]+),", 1, EventText) 
| extend Environment = extract("environmentName=([^,]+),", 1, EventText)  
| extend JobName = extract("jobName=([^,]+),", 1, EventText)  
| extend StepName = extract("stepName=([^,]+),", 1, EventText)  
| extend UnitOfWorkId = extract("unitOfWorkId=([^,]+),", 1, EventText)  
| extend LaunchTime = extract("launchTime=([^,]+),", 1, EventText, typeof(datetime))  
| extend FinishTime = extract("finishTime=([^,]+),", 1, EventText, typeof(datetime)) 
| extend TotalLaunchedMaps = extract("totalLaunchedMaps=([^,]+),", 1, EventText, typeof(real))  
| extend TotalLaunchedReducers = extract("totalLaunchedReducers=([^,]+),", 1, EventText, typeof(real)) 
| extend MapsSeconds = extract("mapsMilliseconds=([^,]+),", 1, EventText, typeof(real)) / 1000 
| extend ReducesSeconds = extract("reducesMilliseconds=([^,]+)", 1, EventText, typeof(real)) / 1000 
| extend TotalMapsSeconds = MapsSeconds  / TotalLaunchedMaps  
| extend TotalReducesSeconds = (ReducesSeconds / TotalLaunchedReducers / ReducesSeconds) * ReducesSeconds  
| extend CalculatedDuration = (TotalMapsSeconds + TotalReducesSeconds) * time(1s) 
| filter Tenant == 'DevDiv' and Environment == 'RollupDev2') 
on UnitOfWorkId 
| extend MapsFactor = TotalMapsSeconds / JobMapsSeconds 
| extend ReducesFactor = TotalReducesSeconds / JobReducesSeconds 
| extend CurrentLoad = 1536 + (768 * TotalLaunchedMaps) + (1536 * TotalLaunchedMaps) 
| extend NormalizedLoad = 1536 + (768 * TotalLaunchedMaps * MapsFactor) + (1536 * TotalLaunchedMaps * ReducesFactor) 
| summarize sum(CurrentLoad), sum(NormalizedLoad) by  JobName  
| extend SaveFactor = sum_NormalizedLoad / sum_CurrentLoad 
```

<a name="concurrent-activities"><a/>
## Диаграммы для параллельных сеансов

Предположим, что у нас есть таблица действий с временами их начала и окончания. Мы бы хотели просмотреть диаграмму, которая показывает, сколько сеансов выполняется параллельно в любой момент времени.

Ниже приведен пример входных данных, которые мы будем называть `X`:

|SessionId | StartTime | StopTime |
|---|---|---|
| a | 10:01:03 | 10:10:08 |
| b | 10:01:29 | 10:03:10 |
| c | 10:03:02 | 10:05:20 |

Мы хотим получить диаграмму с группированием по 1 минуте, поэтому хотим создать что-то такое, что можно подсчитывать с интервалом 1 минута для каждого выполняющегося действия.

Вот промежуточный результат:

     X | extend samples = range(bin(StartTime, 1m), Stop, 1m)

`range` создает массив значений в заданных интервалах времени:

|SessionId | StartTime | StopTime | примеры|
|---|---|---|---|
| a | 10:03:33 | 10:06:31 | [10:01:00,10:02:00,...10:06:00]|
| b | 10:02:29 | 10:03:45 | [10:02:00,10:03:00]|
| c | 10:03:12 | 10:04:30 | [10:03:00,10:04:00]|

Но вместо хранения этих массивов мы [развернем их](app-analytics-queries.md#mvexpand-operator):

    X | mvexpand samples = range(bin(StartTime, 1m), StopTime , 1m)


|SessionId | StartTime | StopTime | примеры|
|---|---|---|---|
| a | 10:03:33 | 10:06:31 | 10:01:00|
| a | 10:03:33 | 10:06:31 | 10:02:00|
| b | 10:02:29 | 10:03:45 | 10:02:00|
| a | 10:03:33 | 10:06:31 | 10:03:00|
| b | 10:02:29 | 10:03:45 | 10:03:00|
| c | 10:03:12 | 10:04:30 | 10:03:00|
| a | 10:03:33 | 10:06:31 | 10:04:00|
| c | 10:03:12 | 10:04:30 | 10:04:00|
|...||||

Теперь можно сгруппировать их по времени выборки, подсчитав количество вхождений для каждого действия:

```
X
| mvexpand samples = range(bin(StartTime, 1m), StopTime , 1m)
| summarize count(SessionId) by bin(todatetime(samples),1m)
```

* Нам нужно использовать функцию todatetime(), так как `mvexpand` возвращает столбец динамического типа.
* Нам нужно использовать функцию bin(), так как для числовых значений и дат при обработке статистических данных всегда применяется функция группировки с интервалом по умолчанию, если интервал не указан. 


| count\_SessionId | примеры|
|---|---|
| 1 | 10:01:00|
| 2 | 10:02:00|
| 3 | 10:03:00|
| 2 | 10:04:00|
| 1 | 10:05:00|

Это можно отобразить в виде линейчатой или временной диаграммы.


## Разновидности оператора соединения

Точная разновидность оператора соединения определяется с помощью ключевого слова kind. На данный момент аналитика поддерживает шесть видов оператора соединения: внутреннее соединение с дедупликацией с левой стороны (по умолчанию), стандартное внутреннее соединение, левое внешнее, правое внешнее, полное внешнее и левое антисоединение.
 
Конфигурация соединения по умолчанию (без указания типа). Для объяснения операции соединения используем две таблицы выборки:
 
Таблица X


|Ключ |Значение1 
|---|---
|a |1 
|b |2 
|b |3 
|c |4\. 

Таблица Y

|Ключ |Значение2 
|---|---
|b |10 
|c |20 
|c |30 
|d |40 

 
Соединение по умолчанию выполняет внутреннее соединение после удаления дубликатов в левой части по ключу соединения (первая запись при дедупликации сохраняется). Рассмотрим эту инструкцию:

    X | join Y on Key 

эффективная левая сторона соединения (таблица X после удаления дубликатов) будет следующей:

|Ключ |Значение1 
|---|---
|a |1 
|b |2 
|c |4\. 

а в результате объединения мы получим следующее:

|Ключ |Значение1 |Ключ |Значение2 
|---|---|---|---
|b |2 |b |10 
|c |4\. |c |20 
|c |4\. |c |30 

(Обратите внимание, что ключи a и d не отображаются в выходных данных, поскольку соответствующие ключи отсутствуют и справа, и слева.)
 
(Исторически это была первая реализация соединения, поддерживаемая первоначальной версией аналитики. Она удобна в типичных сценариях анализов журналов и трассировки, в которых мы хотим связать два события (каждое из которых соответствует некоему критерию фильтрации) с одним идентификатором корреляции и вернуть все появления феномена, который мы ищем, игнорируя множественные вхождения анализируемых записей трассировки.)
 
### Внутреннее соединение (kind=inner) 

Это стандартное внутреннее соединение SQL. Выходная запись образуется каждый раз, когда у записи с левой стороны есть тот же ключ соединения, что и у записи с правой стороны.
 
    X | join kind=inner Y on Key 

дает следующий результат:

|Ключ |Значение1 |Ключ |Значение2 
|---|---|---|---
|b |2 |b |10 
|b |3 |b |10 
|c |4\. |c |20 
|c |4\. |c |30 

Обратите внимание, что (b,10) с правой стороны был присоединен дважды: с (b,2) и (b,3) в левой части. (c,4) в левой части также был присоединен дважды: с (c,20) и (c,30) справа.

### Левое внешнее соединение (kind= leftouter) 

Результат левого внешнего соединения для таблиц X и Y всегда содержит все записи из левой таблицы (X), даже если условие соединения не соответствует ни одной из записей из правой таблицы (Y).
 
Исходные данные:

    X | join kind=leftouter Y on Key 

Результат:

|Ключ |Значение1 |Ключ |Значение2 
|---|---|---|---
|a |1 |null |null 
|b |2 |b |10 
|b |3 |b |10 
|c |4\. |c |20 
|c |4\. |c |30 
 
### Правое внешнее объединение (kind=rightouter) 

Напоминает левое внешнее соединение, но обработка таблиц осуществляется в обратном порядке.
 
Исходные данные:

    X | join kind=rightouter Y on Key 

Результат:


|Ключ |Значение1 |Ключ |Значение2 
|---|---|---|---
|b |2 |b |10 
|b |3 |b |10 
|c |4\. |c |20 
|c |4\. |c |30 
|null |null |d |40 
 
### Полное внешнее соединение (kind=fullouter) 

По сути, полное внешнее соединение объединяет результаты, полученные после применения левого и правого внешних соединений. В тех случаях, когда записи в соединяемых таблицах не совпадают, результирующий набор будет иметь значения NULL для каждого столбца таблицы, в котором нет совпадающей строки. Для тех записей, для которых соответствия нет, в результирующий набор будет добавлена одна строка (содержащая поля, заполненные из обеих таблиц).
 
Исходные данные:

    X | join kind=fullouter Y on Key 

Результат:

|Ключ |Значение1 |Ключ |Значение2 
|---|---|---|---
|a |1 |null |null 
|b |2 |b |10 
|b |3 |b |10 
|c |4\. |c |20 
|c |4\. |c |30 
|null |null |d |40 
 
### Левое антиобъединение (kind=leftanti) 

Левое антиобъединение возвращает все записи с левой стороны, которые не соответствуют ни одной записи с правой стороны.
 
    X | join kind=leftanti Y on Key 
 
Результат:

|Ключ |Значение1 
|---|---
|a |1 
 
Антисоединение моделирует запрос NOT IN.



## Соединение с сопоставлением встроенного словаря

```AIQL

let TeamFoundationJobResult = range i from 1 to 1 step 1 
  | extend recordsJson = "[ 
    { 'key': -1, 'value': 'None'}, 
    { 'key': 0, 'value': 'Succeeded'}, 
    { 'key': 1, 'value': 'PartiallySucceeded'}, 
    { 'key': 2, 'value': 'Failed'}, 
    { 'key': 3, 'value': 'Stopped'}, 
    { 'key': 4, 'value': 'Killed'}, 
    { 'key': 5, 'value': 'Blocked'}, 
    { 'key': 6, 'value': 'ExtensionNotFound'}, 
    { 'key': 7, 'value': 'Inactive'}, 
    { 'key': 8, 'value': 'Disabled'}, 
    { 'key': 9, 'value': 'JobInitializationError'} 
  ]" 
  | mvexpand record = parsejson(replace("'", """, recordsJson)) 
  | project Result = toint(record.key), ResultString = tostring(record.value); 
JobHistory 
  | where PreciseTimeStamp > ago(1h)  
  | where Service  <> "AX" 
  | where Plugin has "Analytics"  
  | sort by PreciseTimeStamp desc 
  | join kind=leftouter TeamFoundationJobResult on Result 
  | extend ExecutionTimeSpan = totimespan(ExecutionTime) 
  | project JobName, StartTime, ExecutionTimeSpan, ResultString, ResultMessage  
```





[AZURE.INCLUDE [app-analytics-footer](../../includes/app-analytics-footer.md)]

<!---HONumber=AcomDC_0323_2016-->
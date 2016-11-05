---
title: Обзор аналитики в Application Insights | Microsoft Docs
description: Короткие примеры всех основных запросов в аналитике, мощном инструменте поиска Application Insights.
services: application-insights
documentationcenter: ''
author: alancameronwills
manager: douge

ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 10/03/2016
ms.author: awills

---
# <a name="a-tour-of-analytics-in-application-insights"></a>Знакомство с аналитикой в Application Insights
[Аналитика](app-insights-analytics.md) — это мощный инструмент поиска [Application Insights](app-insights-overview.md). На этих страницах описан язык запросов аналитики приложений.

* **[Просмотрите видео с вводной информацией](https://applicationanalytics-media.azureedge.net/home_page_video.mp4)**.
* **[Протестируйте аналитику на смоделированных данных](https://analytics.applicationinsights.io/demo)**, если ваше приложение еще не отправляет данные в Application Insights.

Давайте рассмотрим некоторые основные запросы, которые помогут вам начать работу с системой.

## <a name="connect-to-your-application-insights-data"></a>Подключение к данным Application Insights
Откройте аналитику в [колонке "Обзор"](app-insights-dashboards.md) приложения в Application Insights.

![На сайте portal.azure.com откройте ресурс Application Insights и щелкните "Аналитика".](./media/app-insights-analytics-tour/001.png)

## <a name="[take](app-insights-analytics-reference.md#take-operator):-show-me-n-rows"></a>Оператор [take](app-insights-analytics-reference.md#take-operator): отображение n строк
Точки данных, регистрирующие операции пользователя (обычно HTTP-запросы, полученные веб-приложением), хранятся в таблице `requests`. Каждая строка представляет точку данных телеметрии, полученную из пакета SDK Application Insights в приложении.

Начнем с изучения нескольких образцов строк таблицы:

![results](./media/app-insights-analytics-tour/010.png)

> [!NOTE]
> Поместите курсор в любой части инструкции, а затем нажмите кнопку "Выполнить". Инструкцию можно разделить на несколько строк, но она не должна содержать пустых строк. Пустыми строками удобно разделить несколько запросов в одном окне.
> 
> 

Выберите столбцы и измените их положение:

![Выберите столбец в правом верхнем углу результатов.](./media/app-insights-analytics-tour/030.png)

Разверните любой элемент, чтобы просмотреть сведения о нем:

![Выберите "Таблица", а затем "Настроить столбцы".](./media/app-insights-analytics-tour/040.png)

> [!NOTE]
> Чтобы изменить порядок результатов, доступных в веб-браузере, щелкните заголовок столбца. Но имейте в виду, что для большого результирующего набора в браузер загружается ограниченное количество строк. Не забывайте, что такая сортировка не всегда показывает фактические наибольшие или наименьшие элементы. Для этого следует использовать оператор `top` или `sort`. 
> 
> 

## <a name="[top](app-insights-analytics-reference.md#top-operator)-and-[sort](app-insights-analytics-reference.md#sort-operator)"></a>Операторы [top](app-insights-analytics-reference.md#top-operator) и [sort](app-insights-analytics-reference.md#sort-operator)
`take` позволяет получить быструю выборку из результата, но отображает строки из таблицы в произвольном порядке. Чтобы упорядочить строки, используйте `top` (для выборки) или `sort` (для всей таблицы).

Показать первые n строк, упорядоченных по одному из столбцов:

```AIQL

    requests | top 10 by timestamp desc 
```

* *Синтаксис*. У большинства операторов есть параметры в виде ключевых слов, такие как `by`.
* `desc` — по убыванию, `asc` — по возрастанию.

![](./media/app-insights-analytics-tour/260.png)

`top...` является более производительным способом выполнения `sort ... | take...`. Можно было бы написать следующее:

```AIQL

    requests | sort by timestamp desc | take 10
```

Результат будет таким же, но запрос станет выполняться немного медленнее. (Также можно написать `order`, это псевдоним `sort`).

Заголовки столбцов в табличном представлении также можно использовать для сортировки результатов на экране. Но, разумеется, если вы использовали `take` или `top` для получения только части таблицы, отсортировать можно только полученные записи.

## <a name="[project](app-insights-analytics-reference.md#project-operator):-select,-rename-and-compute-columns"></a>Оператор [Project](app-insights-analytics-reference.md#project-operator): выбор, переименование и вычисление столбцов
С помощью оператора [`project`](app-insights-analytics-reference.md#project-operator) можно выбрать только нужные столбцы.

```AIQL

    requests | top 10 by timestamp desc
             | project timestamp, name, resultCode
```

![](./media/app-insights-analytics-tour/240.png)

Также можно переименовать столбцы и определить новые:

```AIQL

    requests 
      | top 10 by timestamp desc 
      | project  
            name, 
            response = resultCode,
            timestamp, 
            ['time of day'] = floor(timestamp % 1d, 1s)
```

![result](./media/app-insights-analytics-tour/270.png)

* [Имена столбцов](app-insights-analytics-reference.md#names) могут содержать пробелы или символы, если будут заключены в квадратные скобки: `['...']` или `["..."]`.
* `%` — обычный оператор остатка от деления. 
* `1d` (т. е. цифра 1, а затем "d") — это литерал интервала времени, который означает один день. Вот еще несколько литералов интервала времени: `12h`, `30m`, `10s`, `0.01s`.
* `floor` (псевдоним `bin`) округляет значение до ближайшего числа, кратного указанному базовому значению. Например, `floor(aTime, 1s)` округляет время до ближайшей секунды.

[Выражения](app-insights-analytics-reference.md#scalars) могут включать в себя все обычные операторы (`+`, `-` и т. д.) и ряд полезных функций.

## <a name="[extend](app-insights-analytics-reference.md#extend-operator):-compute-columns"></a>Оператор [Extend](app-insights-analytics-reference.md#extend-operator): вычисление столбцов
Для добавления новых столбцов к существующим можно использовать оператор [`extend`](app-insights-analytics-reference.md#extend-operator).

```AIQL

    requests 
      | top 10 by timestamp desc
      | extend timeOfDay = floor(timestamp % 1d, 1s)
```

Если вы хотите сохранить все существующие столбцы, можете воспользоваться [`extend`](app-insights-analytics-reference.md#extend-operator). Это менее подробный вариант, чем [`project`](app-insights-analytics-reference.md#project-operator).

## <a name="[summarize](app-insights-analytics-reference.md#summarize-operator):-aggregate-groups-of-rows"></a>Оператор [Summarize](app-insights-analytics-reference.md#summarize-operator): агрегирование групп строк
`Summarize` применяет указанную *функцию агрегирования* для групп строк. 

Например, время, требуемое веб-приложению для ответа на запрос, указано в поле `duration`. Давайте рассмотрим среднее время ответа для всех запросов:

![](./media/app-insights-analytics-tour/410.png)

Или можно разделить результат по запросам с разными именами:

![](./media/app-insights-analytics-tour/420.png)

Оператор `Summarize` собирает точки данных в потоке в группы, для которых предложение `by` вычисляется одинаково. Каждое значение в выражении `by` , то есть каждое имя операции в приведенном выше примере, соответствует строке в таблице результатов. 

Или можно сгруппировать результаты по времени дня:

![](./media/app-insights-analytics-tour/430.png)

Обратите внимание на то, как используется функция `bin` (также называемая `floor`). Если бы мы использовали `by timestamp`, то каждая входная строка попала бы в небольшую отдельную группу. Для любого непрерывного скаляра, такого как значения времени или числовые значения, нужно разбить единый диапазон на удобное количество дискретных значений. Проще всего это сделать с помощью функции `bin`, которая представляет собой привычную функцию округления в меньшую сторону `floor`.

Ту же методику можно использовать и для сокращения диапазонов строк:

![](./media/app-insights-analytics-tour/440.png)

Обратите внимание, что с помощью `name=` можно задать имя столбца результатов в агрегатных выражениях или предложении By.

## <a name="counting-sampled-data"></a>Подсчет данных выборки
`sum(itemCount)` . Во многих случаях itemCount==1, поэтому функция просто подсчитывает количество строк в группе. Но если выполняется [выборка](app-insights-sampling.md), то лишь часть исходных событий сохраняется в виде точек данных в Application Insights, поэтому для каждой отображаемой точки данных существуют события `itemCount`. 

Например, если выборка отбрасывает 75 % исходных событий, то в сохраняемых записях itemCount==4. Это значит, что для каждой сохраняемой записи было четыре исходные записи. 

В результате адаптивной выборки itemCount увеличивается в периоды, когда приложение интенсивно используется.

Таким образом, суммирование по itemCount позволяет правильно оценить исходное число событий.

![](./media/app-insights-analytics-tour/510.png)

Существует также функция агрегирования `count()` (и операция подсчета), используемая, когда действительно нужно подсчитать количество строк в группе.

Существует ряд [статистических функций](app-insights-analytics-reference.md#aggregations).

## <a name="charting-the-results"></a>Отображение результатов
```AIQL

    exceptions 
       | summarize count()  
         by bin(timestamp, 1d)
```

По умолчанию результаты отображаются в виде таблицы:

![](./media/app-insights-analytics-tour/225.png)

Мы можем добиться лучшего результата по сравнению с таким табличным представлением. Давайте посмотрим на результаты в диаграмме с вертикальными столбцами:

![Щелкните "Диаграмма", затем выберите "Вертикальная линейчатая диаграмма" и назначьте оси x и y.](./media/app-insights-analytics-tour/230.png)

Обратите внимание, что хотя мы не сортировали результаты по времени (как видно в окне таблицы), дата и время на диаграмме всегда отображаются в правильном порядке.

## <a name="[where](app-insights-analytics-reference.md#where-operator):-filtering-on-a-condition"></a>Оператор [where](app-insights-analytics-reference.md#where-operator): фильтрация по условию
Если вы настроили мониторинг Application Insights для [клиентской](app-insights-javascript.md) и серверной сторон приложения, некоторые данные телеметрии в базу данных поступают из браузеров.

Давайте посмотрим только на исключения, отправленные из браузеров:

```AIQL

    exceptions 
      | where device_Id == "browser" 
      |  summarize count() 
       by device_BrowserVersion, outerExceptionMessage 
```

![](./media/app-insights-analytics-tour/250.png)

Оператор `where` принимает логическое выражение. Ниже приведены некоторые ключевые моменты:

* `and`, `or`: логические операторы;
* `==`, `<>`: равно и не равно;
* `=~`, `!=`: равенство и неравенство строк без учета регистра. Существует множество других операторов сравнения строк.

Ознакомьтесь со всей информацией о [скалярных выражениях](app-insights-analytics-reference.md#scalars).

### <a name="filtering-events"></a>Фильтрация событий
Поиск неудачных запросов:

```AIQL

    requests 
      | where isnotempty(resultCode) and toint(resultCode) >= 400
```

`responseCode` имеет строковый тип, поэтому мы должны [привести его тип](app-insights-analytics-reference.md#casts) для числового сравнения.

Суммирование различных ответов:

```AIQL

    requests
      | where isnotempty(resultCode) and toint(resultCode) >= 400
      | summarize count() 
      by resultCode
```

## <a name="timecharts"></a>Временные диаграммы
Показать количество событий, возникающих каждый день:

```AIQL

    requests
      | summarize event_count=count()
        by bin(timestamp, 1d)
```

Выберите вариант отображения диаграммы:

![timechart](./media/app-insights-analytics-tour/080.png)

Ось x для линейчатых диаграмм должна иметь тип DateTime. 

## <a name="multiple-series"></a>Множественные серии
Используйте несколько значений в предложении `summarize by` , чтобы создать отдельную строку для каждого сочетания значений.

```AIQL

    requests 
      | summarize event_count=count()   
        by bin(timestamp, 1d), client_StateOrProvince
```

![](./media/app-insights-analytics-tour/090.png)

Чтобы просмотреть несколько строк на диаграмме, щелкните **Split by** (Разбить по) и выберите столбец.

![](./media/app-insights-analytics-tour/100.png)

## <a name="daily-average-cycle"></a>Ежедневный средний цикл
Как изменяется использование в течение среднего дня?

Количество запросов в зависимости от времени по модулю в один день, сгруппированное по часам:

```AIQL

    requests
      | extend hour = floor(timestamp % 1d , 1h) 
          + datetime("2016-01-01")
      | summarize event_count=count() by hour
```

![Линейчатая диаграмма часов для среднего дня](./media/app-insights-analytics-tour/120.png)

> [!NOTE]
> Обратите внимание, что в настоящее время необходимо преобразовать интервалы времени в datetime для отображения на диаграмме.
> 
> 

## <a name="compare-multiple-daily-series"></a>Сравнение серий для нескольких дней
Как использование изменяется в течение дня в различных состояниях?

```AIQL
    requests
     | extend hour= floor( timestamp % 1d , 1h)
           + datetime("2001-01-01")
     | summarize event_count=count() 
       by hour, client_StateOrProvince
```

Разбиение диаграммы по состояниям:

![Разбиение по client_StateOrProvince](./media/app-insights-analytics-tour/130.png)

## <a name="plot-a-distribution"></a>Построение графика распределения
Какое количество сеансов различной длительности существует?

```AIQL

    requests 
      | where isnotnull(session_Id) and isnotempty(session_Id) 
      | summarize min(timestamp), max(timestamp) 
      by session_Id 
      | extend sessionDuration = max_timestamp - min_timestamp 
      | where sessionDuration > 1s and sessionDuration < 3m 
      | summarize count() by floor(sessionDuration, 3s) 
      | project d = sessionDuration + datetime("2016-01-01"), count_
```

Последняя строка необходима для преобразования в datetime — в настоящее время ось x графика может быть только в формате datetime.

Предложение `where` исключает одноразовые сеансы (sessionDuration==0) и задает длину оси x.

![](./media/app-insights-analytics-tour/290.png)

## <a name="[percentiles](app-insights-analytics-reference.md#percentiles)"></a>[Процентили](app-insights-analytics-reference.md#percentiles)
Какие диапазоны длительности покрывают различные процентные доли сеансов?

Используйте приведенный выше запрос, но вместо последней строки укажите следующую:

```AIQL

    requests 
      | where isnotnull(session_Id) and isnotempty(session_Id) 
      | summarize min(timestamp), max(timestamp) 
      by session_Id 
      | extend sesh = max_timestamp - min_timestamp 
      | where sesh > 1s
      | summarize count() by floor(sesh, 3s) 
      | summarize percentiles(sesh, 5, 20, 50, 80, 95)
```

Мы также удалили верхний предел в предложении where, чтобы получить правильные данные, включая все сеансы с несколькими запросами:

![result](./media/app-insights-analytics-tour/180.png)

Из полученных результатов мы видим следующее:

* 5 % сеансов имеют продолжительность менее 3 минут 34 секунд; 
* 50 % сеансов имеют продолжительность менее 36 минут;
* 5 % сеансов имеют продолжительность более 7 дней.

Для получения отдельных результатов для каждой страны нужно просто отделить столбец client_CountryOrRegion с помощью обоих операторов суммирования:

```AIQL

    requests 
      | where isnotnull(session_Id) and isnotempty(session_Id) 
      | summarize min(timestamp), max(timestamp) 
      by session_Id, client_CountryOrRegion
      | extend sesh = max_timestamp - min_timestamp 
      | where sesh > 1s
      | summarize count() by floor(sesh, 3s), client_CountryOrRegion
      | summarize percentiles(sesh, 5, 20, 50, 80, 95)
      by client_CountryOrRegion
```

![](./media/app-insights-analytics-tour/190.png)

## <a name="[join](app-insights-analytics-reference.md#join)"></a>[Join](app-insights-analytics-reference.md#join)
Имеется доступ к нескольким таблицам, включая запросы и исключения.

Чтобы найти исключения, связанные с запросом, который завершился неудачно, можно соединить таблицы по `session_Id`.

```AIQL

    requests 
      | where toint(responseCode) >= 500 
      | join (exceptions) on operation_Id 
      | take 30
```


Рекомендуется использовать `project` только для выбора необходимых столбцов перед выполнением соединения.
В тех же предложениях мы переименуем столбец timestamp.

## <a name="[let](app-insights-analytics-reference.md#let-clause):-assign-a-result-to-a-variable"></a>Оператор [let](app-insights-analytics-reference.md#let-clause): присвоение результата переменной
С помощью оператора [let](app-insights-analytics-reference.md#let-statements) можно разделить части предыдущего выражения. Результаты не изменились:

```AIQL

    let bad_requests = 
      requests
        | where  toint(resultCode) >= 500  ;
    bad_requests
      | join (exceptions) on session_Id 
      | take 30
```

> Совет. Не добавляйте пустые строки между частями выражения в клиенте аналитики. Обязательно выполните все части.
> 
> 

## <a name="accessing-nested-objects"></a>Доступ к вложенным объектам
Доступ к вложенным объектам осуществляется легко. Например, в потоке исключений структурированные объекты выглядят следующим образом:

![result](./media/app-insights-analytics-tour/520.png)

Этот поток можно выровнять, выбрав необходимые свойства.

```AIQL

    exceptions | take 10
      | extend method1 = tostring(details[0].parsedStack[1].method)
```

Обратите внимание, что необходимо использовать [приведение](app-insights-analytics-reference.md#casts) к соответствующему типу.

## <a name="custom-properties-and-measurements"></a>Пользовательские свойства и измерения
Если ваше приложение прикрепляет к событиям [пользовательские измерения (свойства) или пользовательские показатели](app-insights-api-custom-events-metrics.md#properties), они будут отображаться в объектах `customDimensions` и `customMeasurements`.

Например, если приложение включает в себя:

```C#

    var dimensions = new Dictionary<string, string> 
                     {{"p1", "v1"},{"p2", "v2"}};
    var measurements = new Dictionary<string, double>
                     {{"m1", 42.0}, {"m2", 43.2}};
    telemetryClient.TrackEvent("myEvent", dimensions, measurements);
```

Чтобы извлечь эти значения в аналитике, необходимо выполнить следующий сценарий:

```AIQL

    customEvents
      | extend p1 = customDimensions.p1, 
      m1 = todouble(customMeasurements.m1) // cast to expected type

``` 

## <a name="tables"></a>Таблицы
Поток данных телеметрии, полученных из приложения, доступен в нескольких таблицах. Схема свойств, доступных для каждой таблицы, отображается в левой части окна.

### <a name="requests-table"></a>Таблица запросов
Подсчитайте число HTTP-запросов к веб-приложению и сегментируйте их по имени страницы.

![Число запросов, сегментированных по имени](./media/app-insights-analytics-tour/analytics-count-requests.png)

Найдите запросы, чаще всего завершавшиеся сбоем.

![Число запросов, сегментированных по имени](./media/app-insights-analytics-tour/analytics-failed-requests.png)

### <a name="custom-events-table"></a>Таблица настраиваемых событий
Если вы используете [TrackEvent()](app-insights-api-custom-events-metrics.md#track-event) для отправки своих собственных событий, то их можно считать из этой таблицы. 

Рассмотрим пример, в котором код вашего приложения содержит следующие строки.

```C#

    telemetry.TrackEvent("Query", 
       new Dictionary<string,string> {{"query", sqlCmd}},
       new Dictionary<string,double> {
           {"retry", retryCount},
           {"querytime", totalTime}})
```

Отобразите частоту этих событий.

![Частота отображения пользовательских событий](./media/app-insights-analytics-tour/analytics-custom-events-rate.png)

Извлеките показатели и измерения из событий.

![Частота отображения пользовательских событий](./media/app-insights-analytics-tour/analytics-custom-events-dimensions.png)

### <a name="custom-metrics-table"></a>Таблица настраиваемых метрик
Если вы используете [TrackMetric()](app-insights-api-custom-events-metrics.md#track-metric) для отправки своих собственных значений метрик, то вы найдете полученные результаты в потоке **customMetrics**. Например:  

![Настраиваемые метрики в аналитике Application Insights](./media/app-insights-analytics-tour/analytics-custom-metrics.png)

> [!NOTE]
> В [обозревателе метрик](app-insights-metrics-explorer.md) все пользовательские показатели, прикрепленные к любому типу телеметрии, отображаются в колонке метрик вместе с метриками, отправленными с помощью `TrackMetric()`. Однако в аналитике пользовательские показатели по-прежнему прикреплены к типу телеметрии, в котором они были выполнены (события, запросы и т. п.), а метрики, отправляемые TrackMetric, отображаются в своем собственном потоке.
> 
> 

### <a name="performance-counters-table"></a>Таблица счетчиков производительности
[Счетчики производительности](app-insights-web-monitor-performance.md#system-performance-counters) показывают базовые системные метрики для вашего приложения, такие как использование ресурсов ЦП, памяти и сети. Можно настроить пакет SDK для отправки дополнительных счетчиков, включая собственные пользовательские счетчики.

Схема **PerformanceCounters** предоставляет `category`, имя `counter` и имя `instance` каждого счетчика производительности. Имена экземпляров счетчиков применимы только к некоторым счетчикам производительности и обычно указывают имя процесса, к которому относится счетчик. В данных телеметрии для каждого приложения вы увидите только счетчики для этого приложения. Например, вот как можно увидеть, какие счетчики доступны. 

![Счетчики производительности в аналитике Application Insights](./media/app-insights-analytics-tour/analytics-performance-counters.png)

Вот как можно получить диаграмму доступной памяти за последний период. 

![Временная диаграмма памяти в аналитике Application Insights](./media/app-insights-analytics-tour/analytics-available-memory.png)

Как и другие данные телеметрии, данные **performanceCounters** также содержат столбец `cloud_RoleInstance`, указывающий удостоверение хост-компьютера, на котором выполняется приложение. Например, вот как можно сравнить производительность приложения на разных компьютерах. 

![Производительность, сегментированная по экземпляру роли в аналитике Application Insights](./media/app-insights-analytics-tour/analytics-metrics-role-instance.png)

### <a name="exceptions-table"></a>Таблица исключений
В этой таблице доступны [исключения, выданные вашим приложением](app-insights-asp-net-exceptions.md). 

Чтобы найти HTTP-запрос, обрабатываемый приложением при порождении исключения, выполните соединение по operation_Id.

![Соединение исключений с запросами по operation_Id](./media/app-insights-analytics-tour/analytics-exception-request.png)

### <a name="browser-timings-table"></a>Таблица временных параметров браузера
`browserTimings` показывает данные о загрузке страниц, собранных в браузерах пользователей.

Для просмотра этих метрик [настройте приложение для использования телеметрии на стороне клиента](app-insights-javascript.md). 

Схема включает в себя [метрики, указывающее длительность различных этапов загрузки страниц](app-insights-javascript.md#page-load-performance). (Они не указывает продолжительность чтения страниц пользователями.)  

Отобразите популярность различных страниц и время загрузки каждой из них.

![Время загрузки страниц в аналитике](./media/app-insights-analytics-tour/analytics-page-load.png)

### <a name="availbility-results-table"></a>Таблица результатов доступности
`availabilityResults` показывает результаты ваших [веб-тестов](app-insights-monitor-web-app-availability.md). Каждое выполнение тестов в каком-либо расположении тестирования регистрируется отдельно. 

![Время загрузки страниц в аналитике](./media/app-insights-analytics-tour/analytics-availability.png)

### <a name="dependencies-table"></a>Таблица зависимостей
Содержит результаты вызовов вашего приложения, отправляемых к базам данных и интерфейсам REST API, а также других вызовов TrackDependency().

### <a name="traces-table"></a>Таблица трассировок
Содержит данные телеметрии, отправленные приложением с помощью TrackTrace() или [других платформ ведения журнала](app-insights-asp-net-trace-logs.md).

## <a name="try-it!"></a>Тестирование
* **[Протестируйте аналитику на смоделированных данных](https://analytics.applicationinsights.io/demo)**, если ваше приложение еще не отправляет данные в Application Insights.

[!INCLUDE [app-insights-analytics-footer](../../includes/app-insights-analytics-footer.md)]

<!--HONumber=Oct16_HO2-->



---
title: Обзор аналитики в Azure Application Insights | Документация Майкрософт
description: Короткие примеры всех основных запросов в аналитике, мощном инструменте поиска Application Insights.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: bddf4a6d-ea8d-4607-8531-1fe197cc57ad
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 05/06/2017
ms.author: mbullwin
ms.openlocfilehash: 9727e3b715334837b959f22dd526caba221be62c
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/18/2018
---
# <a name="a-tour-of-analytics-in-application-insights"></a>Знакомство с аналитикой в Application Insights
[Аналитика](app-insights-analytics.md) — это мощный инструмент поиска [Application Insights](app-insights-overview.md). На этих страницах описан язык запросов Log Analytics.

* **[Просмотрите видео с вводной информацией](https://applicationanalytics-media.azureedge.net/home_page_video.mp4)**.
* **[Протестируйте аналитику на смоделированных данных](https://analytics.applicationinsights.io/demo)**, если ваше приложение еще не отправляет данные в Application Insights.
* **[Памятка для пользователей SQL](https://aka.ms/sql-analytics)** содержит сопоставление наиболее распространенных идиом.

Давайте рассмотрим некоторые основные запросы, которые помогут вам начать работу с системой.

## <a name="connect-to-your-application-insights-data"></a>Подключение к данным Application Insights
Откройте аналитику в [колонке "Обзор"](app-insights-dashboards.md) приложения в Application Insights.

![На сайте portal.azure.com откройте ресурс Application Insights и щелкните "Аналитика".](./media/app-insights-analytics-tour/001.png)

## <a name="takehttpsdocsloganalyticsioquerylanguagequerylanguagetakeoperatorhtml-show-me-n-rows"></a>Оператор [take](https://docs.loganalytics.io/queryLanguage/query_language_takeoperator.html): отображение n строк
Точки данных, регистрирующие операции пользователя (обычно HTTP-запросы, полученные веб-приложением), хранятся в таблице `requests`. Каждая строка представляет точку данных телеметрии, полученную из пакета SDK Application Insights в приложении.

Начнем с изучения нескольких образцов строк таблицы:

![results](./media/app-insights-analytics-tour/010.png)

> [!NOTE]
> Поместите курсор в любой части инструкции, а затем нажмите кнопку "Выполнить". Инструкцию можно разделить на несколько строк, но она не должна содержать пустых строк. Пустыми строками удобно разделить несколько запросов в одном окне.
>
>

Выберите нужные столбцы, перетащите их, чтобы cгруппировать, и примените фильтр:

![Выберите столбец в правом верхнем углу результатов.](./media/app-insights-analytics-tour/030.png)

Разверните любой элемент, чтобы просмотреть сведения о нем:

![Выберите "Таблица", а затем "Настроить столбцы".](./media/app-insights-analytics-tour/040.png)

> [!NOTE]
> Чтобы изменить порядок результатов, доступных в веб-браузере, щелкните заголовок столбца. Но имейте в виду, что для большого результирующего набора в браузер загружается ограниченное количество строк. При такой сортировке просто обрабатывается полученный результирующий набор и не всегда отображаются фактические наибольшие или наименьшие элементы. Чтобы безошибочно отсортировать элементы, используйте операторы `top` или `sort`.
>
>

## <a name="query-across-applications"></a>Запрос в приложениях
Если нужно объединить данные из нескольких приложений Application Insights, используйте ключевое слово **приложение**, чтобы указать приложение и имя таблицы.  Этот запрос объединяет запросы двух разных приложений, использующих команду **объединения**.


```AIQL

    union app('fabrikamstage').requests, app('fabrikamprod').requests
    
```

## <a name="tophttpsdocsloganalyticsioquerylanguagequerylanguagetopoperatorhtml-and-sorthttpsdocsloganalyticsioquerylanguagequerylanguagesortoperatorhtml"></a>Операторы [top](https://docs.loganalytics.io/queryLanguage/query_language_topoperator.html) и [sort](https://docs.loganalytics.io/queryLanguage/query_language_sortoperator.html)
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

Заголовки столбцов в табличном представлении также можно использовать для сортировки результатов на экране. Но, разумеется, если вы использовали `take` или `top`, чтобы получить только часть таблицы, щелкнув заголовок столбца, можно отсортировать только полученные записи.

## <a name="wherehttpsdocsloganalyticsioquerylanguagequerylanguagewhereoperatorhtml-filtering-on-a-condition"></a>Оператор [where](https://docs.loganalytics.io/queryLanguage/query_language_whereoperator.html): фильтрация по условию

Давайте отобразим только запросы, вернувшие конкретный код результата.

```AIQL

    requests
    | where resultCode  == "404"
    | take 10
```

![](./media/app-insights-analytics-tour/250.png)

Оператор `where` принимает логическое выражение. Ниже приведены некоторые ключевые моменты:

* `and`, `or`: логические операторы;
* `==`, `<>`, `!=`: равно и не равно;
* `=~`, `!~`: равенство и неравенство строк без учета регистра. Существует множество других операторов сравнения строк.

<!---Read all about [scalar expressions]().--->

### <a name="find-unsuccessful-requests"></a>Поиск неудачных запросов

Преобразуйте строковое значение в целое число, чтобы использовать сравнение "больше чем":

```AIQL

    requests
    | where isnotempty(resultCode) and toint(resultCode) >= 400
```
<!---
`resultCode` has type string, so we must cast it app-insights-analytics-reference.md#casts for a numeric comparison.
--->

## <a name="time"></a>Время

По умолчанию отображаются запросы только за последние 24 часа. Этот интервал можно изменить.

![](./media/app-insights-analytics-tour/change-time-range.png)

Для этого в предложении where в запросе следует указать `timestamp`. Например: 

```AIQL

    // What were the slowest requests over the past 3 days?
    requests
    | where timestamp > ago(3d)  // Override the time range
    | top 5 by duration
```

Функция диапазона времени эквивалентна предложению where, добавленному после каждого упоминания какой-либо из исходных таблиц.

`ago(3d)` означает "три дня назад". Другие единицы времени включают в себя часы (`2h`, `2.5h`), минуты (`25m`) и секунды (`10s`).

Другие примеры.

```AIQL

    // Last calendar week:
    requests
    | where timestamp > startofweek(now()-7d)
        and timestamp < startofweek(now())
    | top 5 by duration

    // First hour of every day in past seven days:
    requests
    | where timestamp > ago(7d) and timestamp % 1d < 1h
    | top 5 by duration

    // Specific dates:
    requests
    | where timestamp > datetime(2016-11-19) and timestamp < datetime(2016-11-21)
    | top 5 by duration

```

[Справочник по значениям даты и времени](https://docs.loganalytics.io/docs/Language-Reference/Data-types/datetime).


## <a name="projecthttpsdocsloganalyticsioquerylanguagequerylanguageprojectoperatorhtml-select-rename-and-compute-columns"></a>Оператор [Project](https://docs.loganalytics.io/queryLanguage/query_language_projectoperator.html): выбор, переименование и вычисление столбцов
С помощью оператора [`project`](https://docs.loganalytics.io/queryLanguage/query_language_projectoperator.html) можно выбрать только нужные столбцы.

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

* Имена столбцов могут содержать пробелы или символы, если они заключены в квадратные скобки: `['...']` или `["..."]`.
* `%` — обычный оператор остатка от деления.
* `1d` (т. е. цифра 1, а затем "d") — это литерал интервала времени, который означает один день. Вот еще несколько литералов интервала времени: `12h`, `30m`, `10s`, `0.01s`.
* `floor` (псевдоним `bin`) округляет значение до ближайшего числа, кратного указанному базовому значению. Например, `floor(aTime, 1s)` округляет время до ближайшей секунды.

Выражения могут включать в себя все обычные операторы (`+`, `-` и т. д.) и ряд полезных функций.

## <a name="extend"></a>Extend
Для добавления новых столбцов к существующим можно использовать оператор [`extend`](https://docs.loganalytics.io/queryLanguage/query_language_extendoperator.html).

```AIQL

    requests
    | top 10 by timestamp desc
    | extend timeOfDay = floor(timestamp % 1d, 1s)
```

Если вы хотите сохранить все существующие столбцы, можете воспользоваться [`extend`](https://docs.loganalytics.io/queryLanguage/query_language_extendoperator.html). Это менее подробный вариант, чем [`project`](https://docs.loganalytics.io/queryLanguage/query_language_projectoperator.html).

### <a name="convert-to-local-time"></a>Преобразование в местное время

Метки времени всегда указываются в формате UTC. Поэтому, если вы находитесь на тихоокеанском побережье США зимой, то запрос может быть следующим:

```AIQL

    requests
    | top 10 by timestamp desc
    | extend localTime = timestamp - 8h
```


## <a name="summarizehttpsdocsloganalyticsioquerylanguagequerylanguagesummarizeoperatorhtml-aggregate-groups-of-rows"></a>Оператор [Summarize](https://docs.loganalytics.io/queryLanguage/query_language_summarizeoperator.html): агрегирование групп строк
`Summarize` применяет указанную *функцию агрегирования* для групп строк.

Например, время, требуемое веб-приложению для ответа на запрос, указано в поле `duration`. Давайте рассмотрим среднее время ответа для всех запросов:

![](./media/app-insights-analytics-tour/410.png)

Или можно разделить результат по запросам с разными именами:

![](./media/app-insights-analytics-tour/420.png)

Оператор `Summarize` собирает точки данных в потоке в группы, для которых предложение `by` вычисляется одинаково. Каждое значение в выражении `by`, то есть каждое уникальное имя операции в приведенном выше примере, соответствует строке в таблице результатов.

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

Существует ряд [статистических функций](https://docs.loganalytics.io/learn/tutorials/aggregations.html).

## <a name="charting-the-results"></a>Отображение результатов
```AIQL

    exceptions
       | summarize count=sum(itemCount)  
         by bin(timestamp, 1h)
```

По умолчанию результаты отображаются в виде таблицы:

![](./media/app-insights-analytics-tour/225.png)

Мы можем добиться лучшего результата по сравнению с таким табличным представлением. Давайте посмотрим на результаты в диаграмме с вертикальными столбцами:

![Щелкните "Диаграмма", затем выберите "Вертикальная линейчатая диаграмма" и назначьте оси x и y.](./media/app-insights-analytics-tour/230.png)

Обратите внимание, что хотя мы не сортировали результаты по времени (как видно в окне таблицы), дата и время на диаграмме всегда отображаются в правильном порядке.


## <a name="timecharts"></a>Временные диаграммы
Отображение количества событий, возникающих каждый час:

```AIQL

    requests
      | summarize event_count=sum(itemCount)
        by bin(timestamp, 1h)
```

Выберите вариант отображения диаграммы:

![timechart](./media/app-insights-analytics-tour/080.png)

## <a name="multiple-series"></a>Множественные серии
Из нескольких выражений в `summarize` создается несколько столбцов.

Из нескольких выражений в предложении `by` создаются несколько строк, по одной для каждого сочетания значений.

```AIQL

    requests
    | summarize count_=sum(itemCount), avg(duration)
      by bin(timestamp, 1h), client_StateOrProvince, client_City
    | order by timestamp asc, client_StateOrProvince, client_City
```

![Запросы к таблицам по часу и расположению](./media/app-insights-analytics-tour/090.png)

### <a name="segment-a-chart-by-dimensions"></a>Разделение диаграммы по показателям
При представлении на диаграмме данных таблицы, в которой есть столбцы строкового и числового типа, строки можно использовать для разделения числовых данных на отдельные ряды точек. Если есть несколько столбцов строкового типа, можно выбрать один из этих столбцов для использования в качестве дискриминатора.

![Разделение диаграммы аналитики](./media/app-insights-analytics-tour/100.png)

#### <a name="bounce-rate"></a>Частота возвращения

Преобразуйте логическое значение в строку для использования в качестве дискриминатора.

```AIQL

    // Bounce rate: sessions with only one page view
    requests
    | where notempty(session_Id)
    | where tostring(operation_SyntheticSource) == "" // real users
    | summarize pagesInSession=sum(itemCount), sessionEnd=max(timestamp)
               by session_Id
    | extend isbounce= pagesInSession == 1
    | summarize count()
               by tostring(isbounce), bin (sessionEnd, 1h)
    | render timechart
```

### <a name="display-multiple-metrics"></a>Отображение нескольких метрик
При представлении на диаграмме данных таблицы, содержащей несколько столбцов числового типа, помимо метки времени можно отобразить любое сочетание метрик.

![Разделение диаграммы аналитики](./media/app-insights-analytics-tour/110.png)

Чтобы выбрать несколько столбцов числового типа, щелкните **Don't Split** (Не разделять). Если отображаются несколько столбцов числового типа, невозможно разделить данные по столбцу строкового типа.

## <a name="daily-average-cycle"></a>Ежедневный средний цикл
Как изменяется использование в течение среднего дня?

Количество запросов в зависимости от времени по модулю в один день, сгруппированное по часам:

```AIQL

    requests
    | where timestamp > ago(30d)  // Override "Last 24h"
    | where tostring(operation_SyntheticSource) == "" // real users
    | extend hour = bin(timestamp % 1d , 1h)
          + datetime("2016-01-01") // Allow render on line chart
    | summarize event_count=sum(itemCount) by hour
```

![Линейчатая диаграмма часов для среднего дня](./media/app-insights-analytics-tour/120.png)

> [!NOTE]
> Обратите внимание, что в настоящее время необходимо преобразовать интервалы времени в значения datetime для отображения на графике.
>
>

## <a name="compare-multiple-daily-series"></a>Сравнение серий для нескольких дней
Как изменяется использование в течение дня в разных странах?

```AIQL

     requests  
     | where timestamp > ago(30d)  // Override "Last 24h"
     | where tostring(operation_SyntheticSource) == "" // real users
     | extend hour= floor( timestamp % 1d , 1h)
           + datetime("2001-01-01")
     | summarize event_count=sum(itemCount)
       by hour, client_CountryOrRegion
     | render timechart
```

![Разделение по client_CountryOrRegion](./media/app-insights-analytics-tour/130.png)

## <a name="plot-a-distribution"></a>Построение графика распределения
Какое количество сеансов различной длительности существует?

```AIQL

    requests
    | where timestamp > ago(30d) // override "Last 24h"
    | where isnotnull(session_Id) and isnotempty(session_Id)
    | summarize min(timestamp), max(timestamp)
      by session_Id
    | extend sessionDuration = max_timestamp - min_timestamp
    | where sessionDuration > 1s and sessionDuration < 3m
    | summarize count() by floor(sessionDuration, 3s)
    | project d = sessionDuration + datetime("2016-01-01"), count_
```

Последняя строка необходима для преобразования в данные даты и времени. Сейчас ось x графика отображается в виде скаляра, только если на ней представлены данные даты и времени.

Предложение `where` исключает одноразовые сеансы (sessionDuration==0) и задает длину оси x.

![](./media/app-insights-analytics-tour/290.png)

## <a name="percentileshttpsdocsloganalyticsioquerylanguagequerylanguagepercentilesaggfunctionhtml"></a>[Процентили](https://docs.loganalytics.io/queryLanguage/query_language_percentiles_aggfunction.html)
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

* 5 % сеансов имеют продолжительность менее 3 минут 34 секунд;
* 50 % сеансов имеют продолжительность менее 36 минут;
* 5 % сеансов имеют продолжительность более 7 дней.

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

## <a name="join"></a>Объединение
Имеется доступ к нескольким таблицам, включая запросы и исключения.

Чтобы найти исключения, связанные с запросом, который завершился неудачно, можно соединить таблицы по `session_Id`.

```AIQL

    requests
    | where toint(resultCode) >= 500
    | join (exceptions) on operation_Id
    | take 30
```


Рекомендуется использовать `project` только для выбора необходимых столбцов перед выполнением соединения.
В тех же предложениях мы переименуем столбец timestamp.

## <a name="lethttpsdocsloganalyticsioquerylanguagequerylanguageletstatementhtml-assign-a-result-to-a-variable"></a>Оператор [let](https://docs.loganalytics.io/queryLanguage/query_language_letstatement.html): присвоение результата переменной

С помощью оператора `let` можно разделить части предыдущего выражения. Результаты не изменились:

```AIQL

    let bad_requests =
      requests
        | where  toint(resultCode) >= 500  ;
    bad_requests
    | join (exceptions) on session_Id
    | take 30
```

> [!Tip] 
> Не добавляйте пустые строки между частями запроса в клиенте аналитики. Обязательно выполните все части.
>

Используйте `toscalar` для преобразования одной ячейки таблицы в значение:

```AIQL
let topCities =  toscalar (
   requests
   | summarize count() by client_City 
   | top n by count_ 
   | summarize makeset(client_City));
requests
| where client_City in (topCities(3)) 
| summarize count() by client_City;
```


### <a name="functions"></a>Functions

Используйте *Let* для определения функции.

```AIQL

    let usdate = (t:datetime)
    {
      strcat(getmonth(t), "/", dayofmonth(t),"/", getyear(t), " ",
      bin((t-1h)%12h+1h,1s), iff(t%24h<12h, "AM", "PM"))
    };
    requests  
    | extend PST = usdate(timestamp-8h)
```

## <a name="accessing-nested-objects"></a>Доступ к вложенным объектам
Доступ к вложенным объектам осуществляется легко. Например, в потоке исключений структурированные объекты выглядят следующим образом:

![result](./media/app-insights-analytics-tour/520.png)

Этот поток можно выровнять, выбрав необходимые свойства.

```AIQL

    exceptions | take 10
    | extend method1 = tostring(details[0].parsedStack[1].method)
```

Обратите внимание на то, что необходимо привести результат к соответствующему типу.


## <a name="custom-properties-and-measurements"></a>Пользовательские свойства и измерения
Если ваше приложение прикрепляет к событиям [пользовательские измерения (свойства) или пользовательские показатели](app-insights-api-custom-events-metrics.md#properties), они будут отображаться в объектах `customDimensions` и `customMeasurements`.

Например, если приложение включает в себя:

```csharp

    var dimensions = new Dictionary<string, string>
                     {{"p1", "v1"},{"p2.d2", "v2"}};
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

Чтобы проверить, относится ли пользовательское измерение к определенному типу:

```AIQL

    customEvents
    | extend p1 = customDimensions.p1,
      iff(notnull(todouble(customMeasurements.m1)), ...
```

### <a name="special-characters"></a>Специальные символы

Для доступа к идентификаторам, имена которых содержат специальные символы или ключевые слова, необходимо использовать `['` и `']` или `["` и `"]`.

```AIQL

    customEvents
    | extend p2d2 = customDimensions.['p2.d2'], ...
```

[Справочник по правилам именования идентификаторов](https://docs.loganalytics.io/docs/Learn/References/Naming-principles)

## <a name="dashboards"></a>Панели мониторинга
Результаты можно закрепить на панели мониторинга, чтобы все важные диаграммы и таблицы были под рукой.

* [Панель мониторинга Azure в общем доступе](app-insights-dashboards.md#share-dashboards). Щелкните значок булавки. Для этого необходимо иметь панель мониторинга, открытую для совместного использования. На портале Azure откройте или создайте панель мониторинга, а затем щелкните "Общий доступ".
* [Панель мониторинга Power BI](app-insights-export-power-bi.md). Щелкните "Экспорт" > "Запрос Power BI". Преимущество этого способа состоит в том, что можно отобразить запрос вместе с другими результатами из множества источников.

## <a name="combine-with-imported-data"></a>Объединение с импортированными данными

Аналитические отчеты отлично выглядят на панели мониторинга, но иногда необходимо преобразовать данные в более удобную форму. Например предположим, что аутентифицированные пользователи идентифицируются в данных телеметрии по псевдониму. А вы хотите показать в результатах их настоящие имена. В этом случае вам необходим CSV-файл для сопоставления настоящих имен и псевдонимов.

Можно импортировать файл данных и использовать его как стандартную таблицу (включая запросы, исключения и т. д.). Можно выполнять запросы к такой таблице отдельно либо соединить ее с другими таблицами. Например, если у вас есть таблица usermap и она содержит столбцы `realName` и `userId`, то с ее помощью можно преобразовать поле `user_AuthenticatedId` в телеметрии запросов.

```AIQL

    requests
    | where notempty(user_AuthenticatedId)
    | project userId = user_AuthenticatedId
      // get the realName field from the usermap table:
    | join kind=leftouter ( usermap ) on userId
      // count transactions by name:
    | summarize count() by realName
```

Для импорта таблицы в колонке "Схема" следуйте инструкциям в разделе **Другие источники данных**, чтобы добавить новый источник данных, передав образец данных. Затем это определение можно использовать для передачи таблиц.

В настоящее время доступна предварительная версия функции импорта, поэтому в разделе "Другие источники данных" сначала будет отображаться ссылка "Свяжитесь с нами". Используйте ее для регистрации в программе по ознакомлению с предварительной версией, и позже на месте ссылки появится кнопка "Добавить новый источник данных".


## <a name="tables"></a>Таблицы
Поток данных телеметрии, полученных из приложения, доступен в нескольких таблицах. Схема свойств, доступных для каждой таблицы, отображается в левой части окна.

### <a name="requests-table"></a>Таблица запросов
Подсчитайте число HTTP-запросов к веб-приложению и сегментируйте их по имени страницы.

![Число запросов, сегментированных по имени](./media/app-insights-analytics-tour/analytics-count-requests.png)

Найдите запросы, чаще всего завершавшиеся сбоем.

![Число запросов, сегментированных по имени](./media/app-insights-analytics-tour/analytics-failed-requests.png)

### <a name="custom-events-table"></a>Таблица настраиваемых событий
Если вы используете [TrackEvent()](app-insights-api-custom-events-metrics.md#trackevent) для отправки своих собственных событий, то их можно считать из этой таблицы.

Рассмотрим пример, в котором код вашего приложения содержит следующие строки.

```csharp

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
Если вы используете [TrackMetric()](app-insights-api-custom-events-metrics.md#trackmetric) для отправки своих собственных значений метрик, то вы найдете полученные результаты в потоке **customMetrics**. Например:   

![Настраиваемые метрики в аналитике Application Insights](./media/app-insights-analytics-tour/analytics-custom-metrics.png)

> [!NOTE]
> В [обозревателе метрик](app-insights-metrics-explorer.md) все пользовательские показатели, прикрепленные к любому типу телеметрии, отображаются в колонке метрик вместе с метриками, отправленными с помощью `TrackMetric()`. Однако в аналитике пользовательские показатели по-прежнему прикреплены к типу телеметрии, в котором они были выполнены (события, запросы и т. п.), а метрики, отправляемые TrackMetric, отображаются в своем собственном потоке.
>
>

### <a name="performance-counters-table"></a>Таблица счетчиков производительности
[Счетчики производительности](app-insights-performance-counters.md) показывают базовые системные метрики для вашего приложения, такие как использование ресурсов ЦП, памяти и сети. Можно настроить пакет SDK для отправки дополнительных счетчиков, включая собственные пользовательские счетчики.

Схема **PerformanceCounters** предоставляет `category`, имя `counter` и имя `instance` каждого счетчика производительности. Имена экземпляров счетчиков применимы только к некоторым счетчикам производительности и обычно указывают имя процесса, к которому относится счетчик. В данных телеметрии для каждого приложения вы увидите только счетчики для этого приложения. Например, вот как можно увидеть, какие счетчики доступны.

![Счетчики производительности в аналитике Application Insights](./media/app-insights-analytics-tour/analytics-performance-counters.png)

Вот как можно получить диаграмму доступной памяти за выбранный период.

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

### <a name="availability-results-table"></a>Таблица результатов тестов доступности
`availabilityResults` показывает результаты ваших [веб-тестов](app-insights-monitor-web-app-availability.md). Каждое выполнение тестов в каком-либо расположении тестирования регистрируется отдельно.

![Время загрузки страниц в аналитике](./media/app-insights-analytics-tour/analytics-availability.png)

### <a name="dependencies-table"></a>Таблица зависимостей
Содержит результаты вызовов вашего приложения, отправляемых к базам данных и интерфейсам REST API, а также других вызовов TrackDependency(). Кроме того, содержит вызовы AJAX, выполненные из браузера.

Вызовы AJAX из браузера:

```AIQL

    dependencies | where client_Type == "Browser"
    | take 10
```

Вызовы зависимостей от сервера:

```AIQL

    dependencies | where client_Type == "PC"
    | take 10
```

Для результатов зависимостей на стороне сервера всегда отображается `success==False`, если не установлен агент Application Insights. Тем не менее прочие данные правильны.

### <a name="traces-table"></a>Таблица трассировок
Содержит данные телеметрии, отправленные приложением с помощью TrackTrace() или [других платформ ведения журнала](app-insights-asp-net-trace-logs.md).

## <a name="video"></a>Видео 

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/123/player] 

Расширенные запросы:

> [!VIDEO https://channel9.msdn.com/Events/Build/2016/P591/player]


## <a name="next-steps"></a>Дополнительная информация
* [Справочные материалы по аналитике](app-insights-analytics-reference.md)
* [Памятка для пользователей SQL](https://aka.ms/sql-analytics) содержит сопоставление наиболее распространенных идиом.

[!INCLUDE [app-insights-analytics-footer](../../includes/app-insights-analytics-footer.md)]

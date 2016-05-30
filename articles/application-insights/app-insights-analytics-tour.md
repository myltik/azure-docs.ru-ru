<properties 
	pageTitle="Обзор аналитики в Application Insights" 
	description="Короткие примеры всех основных запросов в аналитике, мощном инструменте поиска Application Insights." 
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
	ms.date="05/04/2016" 
	ms.author="awills"/>


 
# Знакомство с аналитикой в Application Insights


[Аналитика](app-insights-analytics.md) — это эффективный инструмент поиска [Application Insights](app-insights-overview.md). На этих страницах описан язык запросов аналитики приложений.


[AZURE.INCLUDE [app-insights-analytics-top-index](../../includes/app-insights-analytics-top-index.md)]
 
Давайте рассмотрим некоторые основные запросы, которые помогут вам начать работу с системой.

## Подключение к данным Application Insights

Откройте аналитику в [колонке "Обзор"](app-insights-dashboards.md) приложения в Application Insights.

![На сайте portal.azure.com откройте ресурс Application Insights и щелкните "Аналитика".](./media/app-insights-analytics-tour/001.png)

	
## Оператор [take](app-insights-analytics-reference.md#take-operator): отображение n строк

Точки данных, регистрирующие операции пользователя (обычно HTTP-запросы, полученные веб-приложением), хранятся в таблице `requests`. Каждая строка представляет точку данных телеметрии, полученную из пакета SDK Application Insights в приложении.

Начнем с изучения нескольких образцов строк таблицы:

![results](./media/app-insights-analytics-tour/010.png)

> [AZURE.NOTE] Поместите курсор в любой части инструкции, а затем нажмите кнопку "Выполнить". Инструкцию можно разделить на несколько строк, но она не должна содержать пустых строк. Пустыми строками удобно разделить несколько запросов в одном окне.


Выберите столбцы и измените их положение:

![Выберите столбец в правом верхнем углу результатов.](./media/app-insights-analytics-tour/030.png)


Разверните любой элемент, чтобы просмотреть сведения о нем:
 
![Выберите "Таблица", а затем "Настроить столбцы".](./media/app-insights-analytics-tour/040.png)

> [AZURE.NOTE] Чтобы изменить порядок результатов, доступных в веб-браузере, щелкните заголовок столбца. Но имейте в виду, что для большого результирующего набора в браузер загружается ограниченное количество строк. Не забывайте, что такая сортировка не всегда показывает фактические наибольшие или наименьшие элементы. Для этого следует использовать оператор `top` или `sort`.

## Операторы [top](app-insights-analytics-reference.md#top-operator) и [sort](app-insights-analytics-reference.md#sort-operator)

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

Результат будет таким же, но запрос станет выполняться немного медленнее. (Также можно написать `order`, это псевдоним `sort`.)

Заголовки столбцов в табличном представлении также можно использовать для сортировки результатов на экране. Но, разумеется, если вы использовали `take` или `top` для получения только части таблицы, отсортировать можно только полученные записи.


## Оператор [Project](app-insights-analytics-reference.md#project-operator): выбор, переименование и вычисление столбцов

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
    | project timestamp, 
               timeOfDay = floor(timestamp % 1d, 1s), 
               name, 
               response = resultCode
```

![result](./media/app-insights-analytics-tour/270.png)

В скалярном выражении:

* `%` — обычный оператор остатка от деления. 
* `1d` (т. е. цифра 1, а затем "d") — это литерал интервала времени, который означает один день. Вот еще несколько литералов интервала времени: `12h`, `30m`, `10s`, `0.01s`.
* `floor` (псевдоним `bin`) округляет значение до ближайшего числа, кратного указанному базовому значению. Поэтому `floor(aTime, 1s)` округляет время до ближайшей секунды.

[Выражения](app-insights-analytics-reference.md#scalars) могут включать в себя все обычные операторы (`+`, `-` и т. д.) и ряд полезных функций.

    

## Оператор [Extend](app-insights-analytics-reference.md#extend-operator): вычисление столбцов

Для добавления новых столбцов к имеющимся можно использовать оператор [`extend`](app-insights-analytics-reference.md#extend-operator).

```AIQL

    requests 
    | top 10 by timestamp desc
    | extend timeOfDay = floor(timestamp % 1d, 1s)
```

Если вы хотите сохранить все имеющиеся столбцы, можете воспользоваться [`extend`](app-insights-analytics-reference.md#extend-operator). Это менее подробный вариант, чем [`project`](app-insights-analytics-reference.md#project-operator).


## Доступ к вложенным объектам

Доступ к вложенным объектам осуществляется легко. Например, в потоке исключений структурированные объекты выглядят следующим образом:

![result](./media/app-insights-analytics-tour/520.png)

Этот поток можно выровнять, выбрав необходимые свойства.

```AIQL

    exceptions | take 10
    | extend method1 = details[0].parsedStack[1].method
```

## Пользовательские свойства и измерения

Если ваше приложение прикрепляет к событиям [пользовательские измерения или свойства](app-insights-api-custom-events-metrics.md#properties), то они будут отображаться в объектах `customDimensions` и `customMeasurements`.


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
      m1 = todouble(customMeasurements.m1) // cast numerics

``` 

> [AZURE.NOTE] В [обозревателе метрик](app-insights-metrics-explorer.md) все пользовательские измерения, присоединенные к любому типу телеметрии, отображаются в колонке метрик вместе с метриками, отправленными с помощью `TrackMetric()`. Но в аналитике пользовательские измерения по-прежнему прикреплены к типу телеметрии, при котором они были выполнены, а метрики отображаются в их собственном потоке `metrics`.


## Оператор [Summarize](app-insights-analytics-reference.md#summarize-operator): агрегирование групп строк

`Summarize` применяет указанную *агрегатную функцию* для групп строк.

Например, время, требуемое вашему веб-приложению для ответа на запрос, указано в поле `duration`. Давайте рассмотрим среднее время ответа для всех запросов:

![](./media/app-insights-analytics-tour/410.png)

Или можно разделить результат по запросам с разными именами:


![](./media/app-insights-analytics-tour/420.png)

`Summarize` собирает точки данных в потоке в группы, для которых предложение `by` вычисляется одинаково. Каждое значение в выражении `by`, то есть каждое имя операции в приведенном выше примере, соответствует строке в таблице результатов.

Или можно сгруппировать результаты по времени дня:

![](./media/app-insights-analytics-tour/430.png)

Обратите внимание на то, как используется функция `bin` (также называемая `floor`). Если бы мы использовали `by timestamp`, каждая входная строка попала бы в небольшую отдельную группу. Для любого непрерывного скалярного значения, такого как время или числа, нужно разбить единый диапазон на удобное количество дискретных значений. Проще всего это можно сделать с помощью `bin`, которая представляет собой привычную функцию округления в меньшую сторону `floor`.

Ту же методику можно использовать и для сокращения диапазонов строк:


![](./media/app-insights-analytics-tour/440.png)

Обратите внимание, что `name=` можно использовать для задания имени столбца результатов в агрегатных выражениях или предложении By.

## Подсчет данных выборки

Для подсчета событий рекомендуется использовать агрегатную функцию `sum(itemCount)`. Во многих случаях itemCount==1, поэтому функция просто подсчитывает количество строк в группе. Однако когда [выборка](app-insights-sampling.md) осуществляется в операции, лишь часть исходных событий сохраняется в виде точки данных в Application Insights, поэтому для каждой отображаемой точки данных существуют события `itemCount`. Таким образом, суммирование по itemCount позволяет правильно оценить исходное число событий.


![](./media/app-insights-analytics-tour/510.png)

Существует также агрегат `count()` (и операция подсчета), используемый в случаях, когда действительно нужно подсчитать количество строк в группе.


Доступен целый [спектр агрегатных функций](app-insights-analytics-reference.md#aggregations).


## Отображение результатов



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


## Оператор [Where](app-insights-analytics-reference.md#where-operator): фильтрация по условию

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

Прочтите всю информацию о [скалярных выражениях](app-insights-analytics-reference.md#scalars).

### Фильтрация событий

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

## Временные диаграммы

Показать количество событий, возникающих каждый день:

```AIQL

    requests
      | summarize event_count=count()
        by bin(timestamp, 1d)
```

Выберите вариант отображения диаграммы:

![timechart](./media/app-insights-analytics-tour/080.png)

Ось x для линейчатых диаграмм должна иметь тип DateTime.

## Множественные серии 

Используйте несколько значений в предложении `summarize by`, чтобы создать отдельную строку для каждого сочетания значений.

```AIQL

    requests 
      | summarize event_count=count()   
        by bin(timestamp, 1d), client_StateOrProvince
```

![](./media/app-insights-analytics-tour/090.png)

Чтобы просмотреть несколько строк на диаграмме, щелкните **Split by** (Разбить по) и выберите столбец.

![](./media/app-insights-analytics-tour/100.png)



## Ежедневный средний цикл

Как изменяется использование в течение среднего дня?

Количество запросов в зависимости от времени по модулю в один день, сгруппированное по часам:

```AIQL

    requests
    | extend hour = floor(timestamp % 1d , 1h) 
          + datetime("2016-01-01")
    | summarize event_count=count() by hour
```

![Линейчатая диаграмма часов для среднего дня](./media/app-insights-analytics-tour/120.png)

>[AZURE.NOTE] Обратите внимание, что в настоящее время необходимо преобразовать интервалы времени в datetime для отображения на диаграмме.


## Сравнение серий для нескольких дней

Как использование изменяется в течение дня в различных состояниях?

```AIQL
    requests
     | extend hour= floor( timestamp % 1d , 1h)
           + datetime("2001-01-01")
     | summarize event_count=count() 
       by hour, client_StateOrProvince
```

Разбиение диаграммы по состояниям:

![Разбиение по client\_StateOrProvince](./media/app-insights-analytics-tour/130.png)


## Построение графика распределения

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

Предложение `where` исключает одноразовые сеансы (sessionDuration == 0) и задает длину оси x.


![](./media/app-insights-analytics-tour/290.png)



## [Процентили](app-insights-analytics-reference.md#percentiles)

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
* 50 % сеансов имеют продолжительность менее 36 минут;
* 5 % сеансов имеют продолжительность более 7 дней.

Для получения отдельных результатов для каждой страны нужно просто отделить столбец client\_CountryOrRegion с помощью обоих операторов суммирования:

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


## [Join](app-insights-analytics-reference.md#join)

Имеется доступ к нескольким таблицам, включая запросы и исключения.

Чтобы найти исключения, связанные с запросом, который завершился неудачно, можно соединить таблицы по `session_Id`.

```AIQL

    requests 
    | where toint(responseCode) >= 500 
    | join (exceptions) on operation_Id 
    | take 30
```


Рекомендуется использовать `project` для выбора только необходимых столбцов перед выполнением соединения. В тех же предложениях мы переименуем столбец timestamp.



## Оператор [Let](app-insights-analytics-reference.md#let-clause): присвоение результата переменной

С помощью оператора [let](./app-insights-analytics-syntax.md#let-statements) можно разделить части предыдущего выражения. Результаты не изменились:

```AIQL

    let bad_requests = 
      requests
        | where  toint(resultCode) >= 500  ;
    bad_requests
    | join (exceptions) on session_Id 
    | take 30
```

> Совет. Не добавляйте пустые строки между частями выражения в клиенте аналитики. Обязательно выполните все части.


[AZURE.INCLUDE [app-insights-analytics-footer](../../includes/app-insights-analytics-footer.md)]

<!---HONumber=AcomDC_0518_2016-->
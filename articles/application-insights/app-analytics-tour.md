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
	ms.date="03/24/2016" 
	ms.author="awills"/>


 
# Знакомство с аналитикой в Application Insights

[Аналитика](app-analytics.md) позволяет выполнять расширенные запросы к данным телеметрии приложения, собранным службой [Application Insights](app-insights-overview.md). На этих страницах описан ее язык запросов.


[AZURE.INCLUDE [app-analytics-top-index](../../includes/app-analytics-top-index.md)]
 
Давайте рассмотрим некоторые основные запросы, которые помогут вам начать работу с системой.

## Подключение к данным Application Insights

Откройте аналитику в [колонке "Обзор"](app-insights-dashboards.md) приложения в Application Insights.

![На сайте portal.azure.com откройте ресурс Application Insights и щелкните "Аналитика".](./media/app-analytics/001.png)

## [Count](app-analytics-aggregations.md#count): подсчет строк

Метрики, такие как счетчики производительности, хранятся в таблице метрик. Каждая строка представляет точку данных телеметрии, полученную из пакета SDK Application Insights в приложении. Чтобы узнать, каков размер таблицы, мы передаем ее содержимое в оператор, который просто подсчитывает строки:

```AIQL
	
    requests | count
```

> [AZURE.NOTE] Поместите курсор в любой части инструкции, а затем нажмите кнопку "Выполнить". Инструкцию можно разделить на несколько строк, но она не должна содержать пустых строк. Чтобы разместить несколько запросов в одном окне, разделяйте их пустыми строками.

Ниже приведен результат:


![](./media/app-analytics-tour/010.png)

	
[`Count`](app-analytics-aggregations.md#count) является одним из многих [операторов запроса](app-analytics-queries.md), с помощью которых можно организовать конвейер, фильтрацию, изменение формы и объединение данных в несколько этапов.
	
## [Take](app-analytics-aggregations.md#take): показать n строк


Давайте просмотрим некоторые данные — что содержится в выборке из 5 строк?

```AIQL

	requests | take 5
```

И вот мы получаем:

![results](./media/app-analytics-tour/020.png)

Выберите столбцы и измените их положение:

![Выберите столбец в правом верхнем углу результатов.](./media/app-analytics-tour/030.png)


Разверните любой элемент, чтобы просмотреть сведения о нем:
 
![Выберите "Таблица", а затем "Настроить столбцы".](./media/app-analytics-tour/040.png)

> [AZURE.NOTE] Чтобы изменить порядок результатов, доступных в веб-браузере, щелкните заголовок столбца. Но имейте в виду, что для большого результирующего набора в браузер загружается ограниченное количество строк. Не забывайте, что такая сортировка не всегда показывает фактические наибольшие или наименьшие элементы. Для этого следует использовать оператор `top` или `sort`.

## [Top](app-analytics-aggregations.md#top) и [sort](app-analytics-aggregations.md#sort)

`take` позволяет получить быструю выборку из результата, но отображает строки из таблицы в произвольном порядке. Чтобы упорядочить строки, используйте `top` (для выборки) или `sort` (для всей таблицы).

Показать первые n строк, упорядоченных по одному из столбцов:

```AIQL

	requests | top 10 by timestamp desc 
```

* *Синтаксис*. У большинства операторов есть параметры в виде ключевых слов, такие как `by`.
* `desc` — по убыванию, `asc` — по возрастанию.

![](./media/app-analytics-tour/260.png)

`top...` является более производительным способом выполнения `sort ... | take...`. Можно было бы написать следующее:

```AIQL

	requests | sort by timestamp desc | take 10
```

Результат будет таким же, но запрос станет выполняться немного медленнее. (Также можно написать `order`, это псевдоним `sort`.)

Заголовки столбцов в табличном представлении также можно использовать для сортировки результатов на экране. Но, разумеется, если вы использовали `take` или `top` для получения только части таблицы, отсортировать можно только полученные записи.


## [Project](app-analytics-aggregations.md#project): выбор, переименование и вычисление столбцов

Воспользуйтесь [`project`](app-analytics-aggregations.md#project), чтобы выбрать только нужные столбцы:

```AIQL

    requests | top 10 by timestamp desc
             | project timestamp, name, resultCode
```

![](./media/app-analytics-tour/240.png)


Также можно переименовать столбцы и определить новые:

```AIQL

    requests 
    | top 10 by timestamp desc 
    | project timestamp, 
               timeOfDay = floor(timestamp % 1d, 1s), 
               name, 
               response = resultCode
```

![result](./media/app-analytics-tour/270.png)

В скалярном выражении:

* `%` — обычный оператор остатка от деления. 
* `1d` (т. е. цифра 1, а затем "d") — это литерал интервала времени, который означает один день. Вот еще несколько литералов интервала времени: `12h`, `30m`, `10s`, `0.01s`.
* `floor` (псевдоним `bin`) округляет значение до ближайшего числа, кратного указанному базовому значению. Поэтому `floor(aTime, 1s)` округляет время до ближайшей секунды.

[Выражения](app-analytics-scalars.md) могут включать в себя все обычные операторы (`+`, `-`…) и ряд полезных функций.

## [Extend](app-analytics-aggregations.md#extend): вычисление столбцов

Если требуется лишь добавить новые столбцы к существующим, используйте [`extend`](app-analytics-aggregations.md#extend):

```AIQL

    requests 
    | top 10 by timestamp desc
    | extend timeOfDay = floor(timestamp % 1d, 1s)
```

Если вы хотите сохранить все существующие столбцы, можете воспользоваться [`extend`](app-analytics-aggregations.md#extend). Это менее подробный вариант, чем [`project`](app-analytics-aggregations.md#project).

## [Summarize](app-analytics-aggregations.md#summarize): агрегирование групп строк

Просмотрев выборку из таблицы, мы видим поля, содержащие различные данные телеметрии. Например, `exception | take 20` быстро показывает, что исключения записываются в поле с именем `outerExceptionType`.

Но вместо перебора отдельных экземпляров давайте запросим общее количество обнаруженных исключений каждого типа:

```AIQL

	exceptions 
    | summarize count() by outerExceptionType
```

![](./media/app-analytics-tour/210.png)

`Summarize` группирует строки, имеющие одинаковые значения в полях, указанных в предложении `by`, возвращая результат для каждой группы в одной строке. Поэтому в данном случае мы получим по одной строке для каждого типа исключения. Статистическая функция `count()` подсчитывает строки в каждой группе, предоставляя результат в столбце.


Существует широкий спектр [статистических функций](app-analytics-aggregations.md), и в одном операторе суммирования можно использовать некоторые из них для получения нескольких вычисляемых столбцов.

Например, давайте создадим список HTTP-запросов, для которых возникают эти исключения. Еще раз взглянув на выборку из таблицы исключений, вы заметите, что пути HTTP-запросов записываются в столбец с именем `operation_Name`.

```AIQL

    exceptions 
    | summarize count(), makeset(operation_Name)
      by outerExceptionType	      
```

![](./media/app-analytics-tour/220.png)

Статистическая функция `makeset()` создает набор из всех указанных значений в каждой группе. Как и в этом примере, существует только одна операция, которая вызывает каждое исключение.


Результат суммирования имеет:

* каждый столбец, указанный в `by`;
* столбец для каждого статистического выражения;
* строку для каждого сочетания значений `by`.


## Суммирование по скалярным значениям


В предложении by можно использовать скалярные (числовые, временные или интервальные) значения. Но числа обычно заполняют непрерывный диапазон. Чтобы сгруппировать точки данных, необходимо назначить их группам дискретных значений. Для этого удобна функция `bin`.

```AIQL

    exceptions 
       | summarize count()  
         by bin(timestamp, 1d)
```

![](./media/app-analytics-tour/225.png)

`bin` уменьшает все временные отметки до интервала в 1 день. Это псевдоним `floor`, функции, знакомой по большинству языков. Она просто уменьшает каждое значение до ближайшего кратного указанного остатка, чтобы `summarize` могла назначить строки группам разумного размера. (Без нее мы бы получили строку результата для каждой отдельной доли секунды, при этом данные не были бы суммированы.)

Мы можем добиться лучшего результата по сравнению с указанным здесь табличным представлением. Давайте посмотрим на результаты в диаграмме с вертикальными столбцами:

![Щелкните "Диаграмма", затем выберите "Вертикальная линейчатая диаграмма" и назначьте оси x и y.](./media/app-analytics-tour/230.png)

Обратите внимание, что хотя мы не сортировали результаты по времени (как видно в окне таблицы), дата и время на диаграмме всегда отображаются в правильном порядке.


## [Where](app-analytics-aggregations.md#where): фильтрация по условию

Если вы настроили мониторинг Application Insights для [клиентской](app-insights-javascript.md) и серверной сторон приложения, некоторые данные телеметрии в базу данных поступают из браузеров.

Давайте посмотрим только на исключения, отправленные из браузеров:

```AIQL

    exceptions 
    | where device_Id == "browser" 
    |  summarize count() 
       by device_BrowserVersion, outerExceptionMessage 
```

![](./media/app-analytics-tour/250.png)

Оператор `where` принимает логическое выражение. Ниже приведены некоторые ключевые моменты:

 * `and`, `or`: логические операторы
 * `==`, `<>`: равно и не равно
 * `=~`, `!=`: равенство и неравенство строк без учета регистра. Существует множество других операторов сравнения строк.

Прочтите всю информацию о [скалярных выражениях](app-analytics-scalars.md).

### Фильтрация событий

Поиск неудачных запросов:

```AIQL

    requests 
    | where isnotempty(resultCode) and toint(resultCode) >= 400
```

`responseCode` имеет строковый тип, поэтому мы должны [привести его тип](app-analytics-scalars.md#casts) для числового сравнения.

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

![timechart](./media/app-analytics-tour/080.png)

Ось x для линейчатых диаграмм должна иметь тип DateTime.

## Множественные серии 

Используйте несколько значений в предложении `summarize by`, чтобы создать отдельную строку для каждого сочетания значений.

```AIQL

    requests 
      | summarize event_count=count()   
        by bin(timestamp, 1d), client_StateOrProvince
```

![](./media/app-analytics-tour/090.png)

Чтобы просмотреть несколько строк на диаграмме, щелкните **Разбить по** и выберите столбец.

![](./media/app-analytics-tour/100.png)



## Ежедневный средний цикл

Как изменяется использование в течение среднего дня?

Количество запросов в зависимости от времени по модулю в один день, сгруппированное по часам:

```AIQL

    requests
    | extend hour = floor(timestamp % 1d , 1h) 
          + datetime("2016-01-01")
    | summarize event_count=count() by hour
```

![Линейчатая диаграмма часов для среднего дня](./media/app-analytics-tour/120.png)

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

![Разбиение по client\_StateOrProvince](./media/app-analytics-tour/130.png)


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


![](./media/app-analytics-tour/290.png)



## [Процентили](app-analytics-aggregations.md#percentiles)

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

![result](./media/app-analytics-tour/180.png)

Из полученных результатов мы видим следующее:

* 5 % сеансов имеют продолжительность менее 3 минут 34 секунд; 
* 50 % сеансов имеют продолжительность менее 36 минут;
* 5 % сеансов имеют продолжительность более 7 дней.

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

![](./media/app-analytics-tour/190.png)


## [Join](app-analytics-aggregations.md#join)

Имеется доступ к нескольким таблицам, включая запросы и исключения.

Чтобы найти исключения, связанные с запросом, который завершился неудачно, можно объединить таблицы по `session_Id`:

```AIQL

    requests 
    | where toint(responseCode) >= 500 
    | join (exceptions) on operation_Id 
    | take 30
```


Рекомендуется использовать `project` для выбора только необходимых столбцов перед выполнением соединения. В тех же предложениях мы переименуем столбец timestamp.



## [Let](app-analytics-aggregations.md#let): присвоение результата переменной

Используйте [let](./app-analytics-syntax.md#let-statements) для разделения частей предыдущего выражения. Результаты не изменились:

```AIQL

    let bad_requests = 
      requests
        | where  toint(resultCode) >= 500  ;
    bad_requests
    | join (exceptions) on session_Id 
    | take 30
```

> Совет. Не добавляйте пустые строки между частями выражения в клиенте аналитики. Обязательно выполните все части.


[AZURE.INCLUDE [app-analytics-footer](../../includes/app-analytics-footer.md)]

<!---HONumber=AcomDC_0330_2016-->
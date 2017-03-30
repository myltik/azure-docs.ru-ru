---
title: "Справочник по аналитике в Azure Application Insights | Документация Майкрософт"
description: "Справочные материалы по регулярным выражениям в аналитике — мощном инструменте поиска Application Insights. "
services: application-insights
documentationcenter: 
author: alancameronwills
manager: carmonm
ms.assetid: eea324de-d5e5-4064-9933-beb3a97b350b
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 03/09/2017
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: b850264ef2b89ad1679ae1e956a58cc849e63c84
ms.lasthandoff: 03/25/2017


---
# <a name="reference-for-analytics"></a>Справочные материалы по аналитике
[Аналитика](app-insights-analytics.md) — это мощный инструмент поиска [Application Insights](app-insights-overview.md). На этих страницах описан язык запросов аналитики приложений.

Дополнительные источники информации:

* Множество справочных материалов предоставляется в аналитике при вводе. Просто начните ввод запроса, чтобы отобразить возможные варианты.
* На [странице руководства](app-insights-analytics-tour.md) содержится пошаговое описание возможностей языка.
* [Памятка для пользователей SQL](https://aka.ms/sql-analytics) содержит сопоставление наиболее распространенных идиом.
* [Протестируйте аналитику на смоделированных данных](https://analytics.applicationinsights.io/demo), если ваше приложение еще не отправляет данные в Application Insights.
 

## <a name="index"></a>Индекс
**Let** [let](#let-clause) | [materialize](#materialize) 

**Запросы и операторы** [as](#as-operator) | [count](#count-operator) | [datatable](#datatable-operator) | [distinct](#distinct-operator) | [evaluate](#evaluate-operator) | [extend](#extend-operator) | [find](#find-operator) | [getschema](#getschema-operator) | [join](#join-operator) | [limit](#limit-operator) | [make-series](#make-series-operator) | [mvexpand](#mvexpand-operator) | [parse](#parse-operator) | [project](#project-operator) | [project-away](#project-away-operator) | [range](#range-operator) | [reduce](#reduce-operator) | [render directive](#render-directive) | [restrict clause](#restrict-clause) | [sample](#sample-operator) | [sample-distinct](#sample-distinct-operator) | [sort](#sort-operator) | [summarize](#summarize-operator) | [table](#table-operator) | [take](#take-operator) | [top](#top-operator) | [top-nested](#top-nested-operator) | [union](#union-operator) | [where](#where-operator) 

**Статистические функции** [any](#any) | [argmax](#argmax) | [argmin](#argmin) | [avg](#avg) | [buildschema](#buildschema) | [count](#count) | [countif](#countif) | [dcount](#dcount) | [dcountif](#dcountif) | [makelist](#makelist) | [makeset](#makeset) | [max](#max) | [min](#min) | [percentile](#percentile) | [percentiles](#percentiles) | [percentilesw](#percentilesw) | [percentilew](#percentilew) | [stdev](#stdev) | [sum](#sum) | [variance](#variance)

**Скаляры** [Логические литералы](#boolean-literals) | [Логические операторы](#boolean-operators) | [Приведения](#casts) | [Скалярные сравнения](#scalar-comparisons) | [gettype](#gettype) | [hash](#hash) | [iff](#iff) | [isnotnull](#isnotnull) | [isnull](#isnull) | [notnull](#notnull) | [toscalar](#toscalar)

**Числа** [Арифметические операторы](#arithmetic-operators) | [Числовые литералы](#numeric-literals) | [abs](#abs) | [bin](#bin) | [exp](#exp) | [floor](#floor) | [gamma](#gamma) | [log](#log) | [rand](#rand) | [sqrt](#sqrt) | [todouble](#todouble) | [toint](#toint) | [tolong](#tolong)

**Дата и время** [Выражения даты и времени](#date-and-time-expressions) | [Литералы даты и времени](#date-and-time-literals) | [ago](#ago) | [datepart](#datepart) | [dayofmonth](#dayofmonth) | [dayofweek](#dayofweek) | [dayofyear](#dayofyear) | [endofday](#endofday) | [endofmonth](#endofmonth) | [endofweek](#endofweek) | [endofyear](#endofyear) | [getmonth](#getmonth) | [getyear](#getyear) | [now](#now) | [startofday](#startofday) | [startofmonth](#startofmonth) | [startofweek](#startofweek) | [startofyear](#startofyear) | [todatetime](#todatetime) | [totimespan](#totimespan) | [weekofyear](#weekofyear)

**Строка** [GUID](#guids) | [Маскируемые строковые литералы](#obfuscated-string-literals) | [Строковые литералы](#string-literals) | [Сравнение строк](#string-comparisons) | [countof](#countof) | [extract](#extract) | [in, !in](#in) | [isempty](#isempty) | [isnotempty](#isnotempty) | [notempty](#notempty)| [parseurl](#parseurl) | [replace](#replace) | [split](#split) | [strcat](#strcat) | [strlen](#strlen) | [substring](#substring) | [tolower](#tolower) | [toupper](#toupper)

**Массивы, объекты и динамические типы** [Литералы массива и объекта](#array-and-object-literals) | [Функции динамического объекта](#dynamic-object-functions) | [Динамические объекты в предложениях let](#dynamic-objects-in-let-clauses) | [Выражения пути JSON](#json-path-expressions) | [Имена](#names) | [arraylength](#arraylength) | [extractjson](#extractjson) | [in, !in](#in) | [parsejson](#parsejson) | [range](#range) | [todynamic](#todynamic) | [treepath](#treepath)

## <a name="let"></a>Let
### <a name="let-clause"></a>Предложение let
**Табличное предложение let — присвоение имени таблице**

    let recentReqs = requests | where timestamp > ago(3d); 
    recentReqs | count

**Скалярное предложение let — присвоение имени значению**

    let interval = 3d; 
    requests | where timestamp > ago(interval)

**Лямбда-предложение let — присвоение имени функции**

    let Recent = 
       (interval:timespan) { requests | where timestamp > ago(interval) };
    Recent(3h) | count

    let us_date = (t:datetime)
    {
      strcat(getmonth(t), "/", dayofmonth(t),"/", getyear(t), " ", 
      bin((t-1h)%12h+1h,1s), iff(t%24h<12h, "AM", "PM"))
    };
    requests 
    | summarize count() by bin(timestamp, 1h) 
    | project count_, pacificTime=us_date(timestamp-8h)

Предложение let связывает [имя](#names) с табличным результатом, скалярным значением или функцией. Предложение представлено префиксом для запроса, а сам запрос — это область привязки. (Let нельзя использовать для присвоения имен элементам, которые будут использоваться позже в ходе сеанса).

**Синтаксис**

    let name = scalar_constant_expression ; query

    let name = query ; query

    let name = (parameterName : type [, ...]) { plain_query }; query

    let name = (parameterName : type [, ...]) { scalar_expression }; query

* *type:* `bool`, `int`, `long`, `double`, `string`, `timespan`, `datetime`, `guid`, [`dynamic`](#dynamic-type)
* *plain_query*: запрос без префикса в виде предложения let.

**Примеры**

    let rows = (n:long) { range steps from 1 to n step 1 };
    rows(10) | ...

Преобразование результата таблицы в скалярное выражение и его использование в запросе:

```
let topCities =  toscalar ( // convert single column to value
   requests
   | summarize count() by client_City 
   | top 4 by count_ 
   | summarize makeset(client_City)) ;
requests
| where client_City in (topCities) 
| summarize count() by client_City;
```

### <a name="materialize"></a>materialize

Используйте индекс materialize(), чтобы улучшить производительность, если на последующих стадиях результаты предложения let используются несколько раз. Этот индекс оценивает и кэширует результат табличного предложения let во время выполнения запроса. Это позволяет убедиться, что запрос не будет выполняться несколько раз.

**Синтаксис**

    materialize(expression)

**Аргументы**

* `expresion` — табличное выражение, вычисляемое и кэшируемое во время выполнения запроса.

**Советы**

* Используйте индекс materialize при наличии операторов join или union, операнды которых имеют взаимные вложенные запросы, выполняемые один раз (см. пример ниже).
* Этот индекс также можно применять в сценариях с использованием разветвлений join или union.
* Индекс materialize можно использовать только в инструкциях let, задав имя кэшированного набора результатов.
* Предельный размер кэша индекса materialize составляет 5 ГБ. Это ограничение применяется на каждом узле кластера и является взаимным для всех запросов.

**Пример самосоединения**


```AIQL
let totalPagesPerDay = pageViews
| summarize by name, Day = startofday(timestamp)
| summarize count() by Day;
let materializedScope = pageViews
| summarize by name, Day = startofday(timestamp);
let cachedResult = materialize(materializedScope);
cachedResult
| project name, Day1 = Day
| join kind = inner
(
    cachedResult
    | project name, Day2 = Day
)
on name
| where Day2 > Day1
| summarize count() by Day1, Day2
| join kind = inner
    totalPagesPerDay
on $left.Day1 == $right.Day
| project Day1, Day2, Percentage = count_*100.0/count_1
```

Некэшированная версия использует результат `scope` дважды:

```AIQL
let totalPagesPerDay = pageViews
| summarize by name, Day = startofday(timestamp)
| summarize count() by Day;
let scope = pageViews
| summarize by name, Day = startofday(timestamp);
scope      // First use of this table.
| project name, Day1 = Day
| join kind = inner
(
    scope  // Second use can cause evaluation twice.
    | project name, Day2 = Day
)
on name
| where Day2 > Day1
| summarize count() by Day1, Day2
| join kind = inner
    totalPagesPerDay
on $left.Day1 == $right.Day
| project Day1, Day2, Percentage = count_*100.0/count_1
```

## <a name="queries-and-operators"></a>Запросы и операторы
Запрос на данные телеметрии выполняется с помощью ссылки на исходный поток, за которой следует конвейер фильтров. Например:

```AIQL
requests // The request table starts this pipeline.
| where client_City == "London" // filter the records
   and timestamp > ago(3d)
| count 
```

Каждый фильтр, для которого в качестве префикса используется вертикальная черта `|` , — это экземпляр класса *operator*с определенными параметрами. Входные данные для оператора представлены таблицей, которая является результатом предыдущего конвейера. В большинстве случаев все параметры — это [скалярные выражения](#scalars) для столбцов входных данных. В некоторых случаях параметры являются именами входных столбцов, но иногда параметр может быть второй таблицей. Результат запроса всегда является таблицей — даже если она включает только один столбец и одну строку.

Запросы могут содержать одиночные разрывы строк, но завершаются пустой строкой. Они могут содержать комментарии между знаком `//` и окончанием строки.

Перед запросом можно указать одно или несколько [предложений let](#let-clause), чтобы определить используемые в нем скалярные значения, таблицы или функции.

```AIQL

    let interval = 3d ;
    let city = "London" ;
    let req = (city:string) {
      requests
      | where client_City == city and timestamp > ago(interval) };
    req(city) | count
```

> `T` обозначает предыдущий конвейер или исходную таблицу.
> 
> 

### <a name="as-operator"></a>Оператор as

Временно привязывает имя ко входному табличному выражению.

**Синтаксис**

    T | as name

**Аргументы**

* *name:* — временное имя таблицы.

**Примечания**

* Если вы хотите использовать это имя в другой части выражения, используйте оператор [let](#let-clause), а не *as*.
* Используйте оператор *as*, чтобы указать имя таблицы в том виде, в котором оно отображается в результатах запроса [union](#union-operator), [find](#find-operator) или [search](#search-operator).

**Пример**

```AIQL
range x from 1 to 10 step 1 | as T1
| union withsource=TableName (requests | take 10 | as T2)
```

### <a name="count-operator"></a>Оператор count
Оператор `count` возвращает количество записей (строк) во входном наборе записей.

**Синтаксис**

    T | count

**Аргументы**

* *T*— табличные данные, записи которых будут подсчитаны.

**Результаты**

Эта функция возвращает таблицу с одной записью и столбцом типа `long`. Значение единственной ячейки — количество записей в *T*. 

**Пример**

```AIQL
requests | count
```

### <a name="datatable-operator"></a>Оператор DataTable

Определяет встроенную таблицу. Схема и значения определяются в самом запросе.

Обратите внимание, что для этого оператора ввод конвейера не предусмотрен.

**Синтаксис**

    datatable ( ColumnName1 : ColumnType1 , ...) [ScalarValue1, ...]

* *ColumnName* — имя столбца.
* *ColumnType* — [тип данных](#scalars). 
* *ScalarValue* — значение соответствующего типа. Число значений должно быть кратно числу столбцов. 

**Результаты**

Таблица, содержащая указанные значения.

**Пример**

```AIQL
datatable (Date:datetime, Event:string)
    [datetime(1910-06-11), "Born",
     datetime(1930-01-01), "Enters Ecole Navale",
     datetime(1953-01-01), "Published first book",
     datetime(1997-06-25), "Died"]
| where strlen(Event) > 4
```

### <a name="distinct-operator"></a>Оператор distinct

Возвращает таблицу, которая содержит набор строк с разными комбинациями значений. При необходимости выполняет проекцию в подмножество столбцов перед операцией.

**Синтаксис**

    T | distinct *              // All columns
    T | distinct Column1, ...   // Columns to project

**Пример**

```AIQL
datatable (Supplier: string, Fruit: string, Price:int) 
["Contoso", "Grapes", 22,
"Fabrikam", "Apples", 14,
"Contoso", "Apples", 15,
"Fabrikam", "Grapes", 22]
| distinct Fruit, Price 
```


|Фрукт|Цена|
|---|---|
|Виноград|22|
|Яблоки|14|
|Яблоки|15|


### <a name="evaluate-operator"></a>Оператор evaluate
Оператор `evaluate` — это механизм расширения, позволяющий добавлять в запросы специализированные алгоритмы.

Оператор `evaluate` должен стоять в конце конвейера запроса (за исключением оператора `render`). Он не должен находиться в тексте функции.

[evaluate autocluster](#evaluate-autocluster) | [evaluate basket](#evaluate-basket) | [evaluate diffpatterns](#evaluate-diffpatterns) | [evaluate extractcolumns](#evaluate-extractcolumns)

#### <a name="evaluate-autocluster"></a>evaluate autocluster
     T | evaluate autocluster()

AutoCluster находит распространенные шаблоны дискретных атрибутов (измерения) в данных и сокращает результаты исходного запроса (будь то 100 или 100 тысяч строк) до небольшого числа шаблонов. Эта функция предназначена для анализа сбоев (например, исключений, поломок), но потенциально может работать с любым отфильтрованным набором данных. 

**Синтаксис**

    T | evaluate autocluster( arguments )

**Результаты**

AutoCluster возвращает набор шаблонов (как правило, небольшого размера), которые содержат фрагменты данных с одинаковыми значениями для нескольких дискретных атрибутов. В результатах каждый шаблон соответствует строке. 

Первые два столбца — это число и процент строк из исходного запроса, которые охватывает шаблон. Остальные столбцы берутся из исходного запроса. Их значения соответствуют конкретным значениям в столбцах или представлены в виде звездочки ("*"), которая заменяет значения переменных. 

Обратите внимание, что шаблоны связаны между собой. Они могут перекрываться и обычно не охватывают все исходные строки. Некоторые строки могут не охватываться ни одним из шаблонов.

**Советы**

* Используйте аргументы `where` и `project` во входном канале, чтобы получить только требуемые данные.
* Если вы хотите узнать дополнительные сведения о конкретной строке, добавьте ее значения в фильтр `where` .

**Аргументы (необязательные)**

* `output=all | values | minimal` 
  
    Формат результатов. В результатах всегда содержатся столбцы Count и Percent. 
  
  * `all` выводит все столбцы из входных данных.
  * `values` фильтрует столбцы в результатах, которые содержат только "*".
  * `minimal` также фильтрует идентичные столбцы для всех строк исходного запроса. 
* `min_percent=`*double* (значение по умолчанию — 1)
  
    Минимальный процент охвата созданных строк.
  
    Пример: `T | evaluate autocluster("min_percent=5.5")`
* `num_seeds=` *int* (значение по умолчанию — 25) 
  
    Число начальных значений определяет число начальных локальных точек поиска для алгоритма. В некоторых случаях в зависимости от структуры данных при увеличении числа начальных значений увеличивается число (или повышается качество) результатов. Это достигается за счет увеличения области поиска при меньшей скорости выполнения запросов. Аргумент num_seeds сокращает количество результатов в обоих направлениях. Поэтому если указать значение меньше 5, повышение производительности будет незначительным, а если указать значение больше 50, вы вряд ли получите дополнительные шаблоны.
  
    Пример: `T | evaluate autocluster("num_seeds=50")`
* `size_weight=` *0<double<1*+ (значение по умолчанию — 0,5)
  
    Позволяет управлять балансом между универсальностью (большой охват) и информативностью (много общих значений). При увеличении значения size_weight количество шаблонов, как правило, уменьшается, а каждый шаблон охватывает больший процент данных. Если уменьшить значение size_weight, обычно получаются более конкретные шаблоны с большим количеством общих значений и меньшим процентом охвата. Поэтому для аргумента используется скрытая формула, представляющая собой взвешенное среднее геометрическое значение между нормализованной универсальной оценкой и информативной оценкой, для которых в качестве весовых коэффициентов применяются size_weight и 1-size_weight. 
  
    Пример: `T | evaluate autocluster("size_weight=0.8")`
* `weight_column=` *column_name*
  
    Рассматривает каждую строку входных данных в соответствии с указанным весовым коэффициентом (по умолчанию весовой коэффициент каждой строки — 1). Как правило, при использовании столбца с весовым коэффициентом следует учитывать выборку, группирование или агрегирование данных, внедренных в каждой строке.
  
    Пример: `T | evaluate autocluster("weight_column=sample_Count")` 

#### <a name="evaluate-basket"></a>evaluate basket
     T | evaluate basket()

Basket находит все распространенные шаблоны дискретных атрибутов (измерения) в данных и возвращает все распространенные шаблоны, которые превысили пороговое значение частоты в исходном запросе. Basket гарантировано обнаруживает все часто встречающиеся шаблоны в данных. Однако эта функция не гарантирует, что задание будет выполнено за полиномиальное время. Время выполнения запроса линейно по отношению к числу строк. Но в некоторых случаях оно может быть экспоненциальным по отношению к числу столбцов (измерения). В основе Basket лежит алгоритм Apriori, который изначально предназначался для интеллектуального анализа данных путем анализа корзины. 

**Результаты**

Все шаблоны, которые содержатся в большей части событий, чем указано (по умолчанию — 0,05).

**Аргументы (необязательные)**

* `threshold=` *0,015<double<1* (значение по умолчанию — 0,05) 
  
    Задает минимальный коэффициент строк, определяющий распространенность шаблонов (шаблоны с меньшим коэффициентом не будут возвращаться).
  
    Пример: `T | evaluate basket("threshold=0.02")`
* `weight_column=` *column_name*
  
    Рассматривает каждую строку входных данных в соответствии с указанным весовым коэффициентом (по умолчанию весовой коэффициент каждой строки — 1). Как правило, при использовании столбца с весовым коэффициентом следует учитывать выборку, группирование или агрегирование данных, внедренных в каждой строке.
  
    Пример: T | evaluate basket("weight_column=sample_Count")
* `max_dims=` *1<int* (значение по умолчанию — 5)
  
    Задает максимальное количество некоррелированных измерений на корзину. По умолчанию они ограничены, чтобы обеспечить сокращение времени выполнения запроса.
* `output=minimize` | `all` 
  
    Формат результатов. В результатах всегда содержатся столбцы Count и Percent.
  
  * `minimize` фильтрует столбцы в результатах, которые содержат только "*".
  * `all` выводит все столбцы из входных данных.

#### <a name="evaluate-diffpatterns"></a>evaluate diffpatterns
     requests | evaluate diffpatterns("split=success")

Diffpatterns сравнивает два набора данных с одинаковой структурой и находит шаблоны дискретных атрибутов (измерения), характеризующие различия между двумя наборами данных. Функция Diffpatterns предназначена для анализа сбоев (например, путем сравнения сбоев и успешных результатов за заданный период времени), но потенциально может найти различия между двумя наборами данных с одинаковой структурой. 

**Синтаксис**

`T | evaluate diffpatterns("split=` *BinaryColumn* `" [, arguments] )`

**Результаты**

Diffpatterns возвращает набор шаблонов (как правило, небольшого размера), который содержит разные фрагменты данных в двух наборах (т. е. шаблон, охватывающий большое количество строк в первом наборе данных и малое количество строк во втором наборе). В результатах каждый шаблон соответствует строке.

Первые четыре столбца соответствуют количеству и проценту строк из исходного запроса, которые охватывает шаблон в каждом наборе. В пятом столбце представлена разница (в абсолютных процентах) между двумя наборами. Остальные столбцы берутся из исходного запроса. Их значения соответствуют конкретным значениям в столбцах или представлены в виде звездочки ("*"), которая заменяет значения переменных. 

Обратите внимание, что шаблоны не являются уникальными. Они могут перекрываться и обычно не охватывают все исходные строки. Некоторые строки могут не охватываться ни одним из шаблонов.

**Советы**

* Используйте аргументы where и project во входном канале, чтобы получить только требуемые данные.
* Если вы хотите узнать дополнительные сведения о конкретной строке, добавьте ее значения в фильтр where.

**Аргументы**

* `split=` *column name* (обязательный аргумент)
  
    Столбец должен содержать два значения. При необходимости создайте такой столбец:
  
    `requests | extend fault = toint(resultCode) >= 500` <br/>
    `| evaluate diffpatterns("split=fault")`
* `target=` *string*
  
    Указывает алгоритму, что искать нужно только шаблоны с большим процентным соотношением в конечном наборе данных. Конечный набор данных должен быть одним из двух значений столбца разделения.
  
    `requests | evaluate diffpatterns("split=success", "target=false")`
* `threshold=` *0,015<double<1* (значение по умолчанию — 0,05) 
  
    Задает минимальную разницу для шаблонов (соотношение) между двумя наборами.
  
    `requests | evaluate diffpatterns("split=success", "threshold=0.04")`
* `output=minimize | all`
  
    Формат результатов. В результатах всегда содержатся столбцы Count и Percent. 
  
  * `minimize` фильтрует столбцы в результатах, которые содержат только "*".
  * `all` выводит все столбцы из входных данных.
* `weight_column=` *column_name*
  
    Рассматривает каждую строку входных данных в соответствии с указанным весовым коэффициентом (по умолчанию весовой коэффициент каждой строки — 1). Как правило, при использовании столбца с весовым коэффициентом следует учитывать выборку, группирование или агрегирование данных, внедренных в каждой строке.
  
    `requests | evaluate autocluster("weight_column=itemCount")`

#### <a name="evaluate-extractcolumns"></a>evaluate extractcolumns
     exceptions | take 1000 | evaluate extractcolumns("details=json") 

Extractcolumns используется для добавления в таблицу нескольких простых столбцов, которые динамически извлекаются из структурированных (полуструктурированных) столбцов на основе их типа. В настоящее время поддерживаются только столбцы JSON (как с динамической, так и со строковой сериализацией).

* `max_columns=` *int* (значение по умолчанию — 10) 
  
    Количество новых добавленных столбцов является динамическим, т. е. может быть добавлено очень много столбцов (на самом деле количество столбцов соответствует количеству отдельных ключей во всех записях JSON). Поэтому нам необходимо ограничить его. Новые столбцы сортируются по убыванию на основе их частоты. Количество столбцов, добавляемых в таблицу, соответствует значению max_columns.
  
    `T | evaluate extractcolumns("json_column_name=json", "max_columns=30")`
* `min_percent=` *double* (значение по умолчанию — 10,0) 
  
    Еще один способ ограничения числа новых столбцов — игнорирование столбцов, частота которых меньше значения min_percent.
  
    `T | evaluate extractcolumns("json_column_name=json", "min_percent=60")`
* `add_prefix=` *bool* (значение по умолчанию — true) 
  
    Если указано значение true, имя комплексного столбца будет добавлено в качестве префикса к именам извлеченных столбцов.
* `prefix_delimiter=` *string* (значение по умолчанию — "_") 
  
    Если для add_prefix указано значение true, этот параметр определяет разделитель, который будет использоваться для сцепления имен новых столбцов.
  
    `T | evaluate extractcolumns("json_column_name=json",` <br/>
    `"add_prefix=true", "prefix_delimiter=@")`
* `keep_original=` *bool* (значение по умолчанию — false) 
  
    Если указано значение true, в выходной таблице будут храниться исходные столбцы (JSON).
* `output=query | table` 
  
    Формат результатов. 
  
  * `table` — выводится полученная таблица без указанных входных столбцов, а также новых столбцов, извлеченных из входных столбцов.
  * `query` — выводится строка, представляющая запрос, который позволяет получить результаты в виде таблицы. 

### <a name="extend-operator"></a>Оператор extend
     T | extend duration = stopTime - startTime

Добавляет в таблицу один или несколько вычисляемых столбцов. 

**Синтаксис**

    T | extend ColumnName = Expression [, ...]

**Аргументы**

* *T* — входная таблица.
* *ColumnName* — имена столбцов, которые будут добавлены. В [именах](#names) учитывается регистр и могут содержаться буквы и цифры, а также символы подчеркивания (_). Используйте `['...']` или `["..."]`, чтобы заключать в кавычки ключевые слова или имена с другими знаками.
* *Expression* — вычисления для имеющихся столбцов.

**Результаты**

Копия входной таблицы с указанными дополнительными столбцами.

**Советы**

* Используйте [`project`](#project-operator) вместо этого оператора, если необходимо удалить или переименовать некоторые столбцы.
* Не используйте оператор `extend` , если вы просто хотите использовать в длинном выражении более короткое имя. `...| extend x = anonymous_user_id_from_client | ... func(x) ...` 
  
    Собственные столбцы таблицы уже проиндексированы. Так как новое имя определяет дополнительный столбец, который не проиндексирован, скорее всего, запрос будет выполняться медленнее.

**Пример**

```AIQL
traces
| extend
    Age = now() - timestamp
```

### <a name="find-operator"></a>Оператор find

    find in (Table1, Table2, Table3) where id=="a string"
    find in (Table1, Table2, Table3) where id=="a string" project column1, column2
    find in (Table1, Table2, Table3) where * has "a string"
    find in (Table1, Table2, Table3) where appName in ("string 1", "string 2", "string 3")

Поиск строк, соответствующих предикату в наборе таблиц.

**Синтаксис**

    find in (Table1, ...) 
    where Predicate 
    [project Column1, ...]

**Аргументы**

* *Table1*: имя таблицы или запроса. Это может быть таблица, определенная с помощью LET, но не функция. Оператор выполняется быстрее, если указано имя таблицы, а не запрос.
* *Predicate*: логическое выражение, вычисляемое для каждой строки в указанных таблицах.
 * В сравнении строк вместо имени столбца можно использовать "*".
* *Column1*: параметр `project` позволяет указать, какие столбцы всегда должны отображаться в выходных данных. 

**Результат**

По умолчанию выходная таблица содержит:

* `source_`: обозначение исходной таблицы для каждой строки; Если вы хотите указать имя, отображающееся в этом столбце, используйте оператор [as](#as-operator) в конце каждого табличного выражения.
* столбцы, явно указанные в предикате;
* непустые столбцы, являющиеся общими для всех входных таблиц;
* `pack_`: контейнер свойств, содержащий данные из других столбцов.

Обратите внимание, что этот формат может измениться при изменении входных данных или предиката. Чтобы указать фиксированный набор столбцов, используйте `project`.

**Пример**

Получение всех запросов и исключений, кроме тех, что выдают тесты доступности и роботы.

```AIQL

    find in (requests, exceptions) where isempty(operation_SyntheticSource)
```

Поиск всех запросов и исключений из Соединенного Королевства (UK), кроме тех, что выдают тесты доступности и роботы.

```AIQL

    let requk = requests
    | where client_CountryOrRegion == "United Kingdom";
    let exuk = exceptions
    | where client_CountryOrRegion == "United Kingdom";
    find in (requk, exuk) where isempty(operation_SyntheticSource)
```

Поиск самых последних данных телеметрии, в которых любое поле содержит слово "test".

```AIQL

    find in (traces, requests, pageViews, dependencies, customEvents, availabilityResults, exceptions) 
    where * has 'test' 
    | top 100 by timestamp desc
```

**Советы по повышению производительности**

* Добавьте в предикат `where` условия на основе времени.
* Используйте предложения `let`, а не создавайте встроенные запросы.

### <a name="getschema-operator"></a>Оператор getschema

   T | getschema
   
Возвращает таблицу с именами столбцов и типами входной таблицы.

```AIQL
requests
| project appId, appName, customDimensions, duration, iKey, itemCount, success, timestamp 
| getschema 
```

![Результаты выполнения getschema](./media/app-insights-analytics-reference/getschema.png)

### <a name="join-operator"></a>Оператор join
    Table1 | join (Table2) on CommonColumn

Объединяет строки в двух таблицах, сопоставляя значения из указанного столбца.

**Синтаксис**

    Table1 | join [kind=Kind] (Table2) on CommonColumn [, ...]

**Аргументы**

* *Table1* — левая часть объединенных данных.
* *Table2* — правая часть объединенных данных. Может быть выражением в виде вложенного запроса для вывода таблицы.
* *CommonColumn* — столбец с одинаковым именем в двух таблицах.
* *Kind* — определяет способ сопоставления строк в двух таблицах.

**Результаты**

Таблица со следующими элементами.

* Столбец для каждого столбца в каждой из двух таблиц, в том числе соответствующие ключи. В случае конфликта имен столбцы в правой части будут автоматически переименованы.
* Строка для каждого соответствия во входных таблицах. Соответствие — это строки из обеих таблиц, у которых совпадают значения всех полей `on` . 
* `Kind` не указан или `= innerunique`
  
    Каждому значению ключа `on` соответствует только одна строка из левой части. Выходные данные содержат по одной строке для каждого соответствия этой строки со строками из правой части.
* `kind=inner`
  
     Строка в выходных данных для каждой комбинации соответствующих строк из левой и правой частей.
* `kind=leftouter` (или `kind=rightouter`, или `kind=fullouter`)
  
     Кроме внутренних соответствий есть также строка для каждой строки в левой и (или) правой части, даже при отсутствии соответствия. В этом случае ячейки выходных данных без соответствий содержат значения NULL.
* `kind=leftanti`
  
     Возвращает все записи из левой части, для которых нет соответствий в правой. Итоговая таблица содержит только столбцы из левой части. 
* `kind=leftsemi` (или `leftantisemi`)

    Возвращает строку из таблицы слева, если в таблице справа имеется (или отсутствует) соответствующая строка. Результат не включает в себя данные таблицы справа.


**Советы**

Размер таблицы результатов не должен превышать 64 МБ.

Обеспечить наилучшую производительность можно так.

* Перед оператором `join` используйте `where` и `project`, чтобы сократить количество строк и столбцов во входных таблицах. 
* Если одна таблица меньше другой, используйте ее в качестве левой (перенаправленной) части объединенных данных.
* У объединяемых по соответствию столбцов должно быть одно имя. При необходимости переименуйте столбец в одной из таблиц с помощью оператора project.



**Пример**

Получите расширенные сведения о действиях из журнала, в котором некоторые записи указывают на время начала и конца действия. 

```AIQL
    let Events = MyLogTable | where type=="Event" ;
    Events
    | where Name == "Start"
    | project Name, City, ActivityId, StartTime=timestamp
    | join (Events
           | where Name == "Stop"
           | project StopTime=timestamp, ActivityId)
        on ActivityId
    | project City, ActivityId, StartTime, StopTime, Duration, StopTime, StartTime

```


### <a name="limit-operator"></a>Оператор limit
     T | limit 5

Возвращает заданное количество строк из входной таблицы. Указать, какие записи будут возвращены, нельзя. (Чтобы вернуть определенные записи, используйте оператор [`top`](#top-operator).)

**Псевдоним** `take`

**Синтаксис**

    T | limit NumberOfRows


**Советы**

`Take` — простой и эффективный способ просмотреть пример результатов во время работы в интерактивном режиме. Учтите, что этот оператор не используется для создания конкретных строк либо их создания в указанном порядке.

Есть неявное ограничение на количество строк, возвращаемых клиенту, даже если не используется оператор `take`. Чтобы снять это ограничение, используйте параметр `notruncation` клиентского запроса.

### <a name="make-series-operator"></a>Оператор make-series

Выполняет агрегирование. В отличие от оператора [summarize](#summarize-operator) для каждой группы формируется одна выходная строка. В результирующих столбцах значения в каждой группе сгруппированы в массивы. 

**Синтаксис**

    T | 
    make-series [Column =] Aggregation default = DefaultValue [, ...] 
    on AxisColumn in range(start, stop, step) 
    by [Column =] GroupExpression [, ...]


**Аргументы**

* *Column* — необязательное имя итогового столбца. По умолчанию это имя, получаемое из выражения.
* *DefaultValue*. Если строка с определенными значениями AxisColumn и GroupExpression отсутствует, тогда в результатах соответствующий элемент массива будет иметь значение DefaultValue. 
* *Aggregation* — числовое выражение, использующее [статистическую функцию](#aggregations). 
* *AxisColumn* — столбец, в котором упорядочивается ряд. Он может рассматриваться как временная шкала, но принимаются все числовые типы.
*start, stop, step*. Определяет список значений AxisColumn для каждой строки. Каждый столбец результатов агрегирования имеет массив с одинаковой длиной. 
* *GroupExpression* — выражение для столбцов, предоставляющее набор уникальных значений. Для каждого значения GroupExpression в выходных данных отображается одна строка. Обычно это имя столбца, который уже содержит ограниченный набор значений. 

**Подсказка**

Массивы результатов отображаются на диаграмме аналитики так же, как и соответствующая операция summarize.

**Пример**

```AIQL
requests
| make-series sum(itemCount) default=0, avg(duration) default=0
  on timestamp in range (ago(7d), now(), 1d)
  by client_City
```

![Результаты выполнения make-series](./media/app-insights-analytics-reference/make-series.png)

### <a name="mvexpand-operator"></a>Оператор mvexpand
    T | mvexpand listColumn 

Разворачивает список из ячейки с динамической типизацией (JSON) так, чтобы у каждой записи была отдельная строка. Все остальные ячейки в развернутой строке дублируются. 

(Ознакомьтесь также с оператором [`summarize makelist`](#summarize-operator), который выполняет противоположную функцию.)

**Пример**

Предположим, что у вас такая входная таблица:

| A:int | B:string | D:dynamic |
| --- | --- | --- |
| 1 |"hello" |{"key":"value"} |
| 2 |"world" |[0,1,"k","v"] |

    mvexpand D

Результат будет таким:

| A:int | B:string | D:dynamic |
| --- | --- | --- |
| 1 |"hello" |{"key":"value"} |
| 2 |"world" |0 |
| 2 |"world" |1 |
| 2 |"world" |"k" |
| 2 |"world" |"v" |

**Синтаксис**

    T | mvexpand  [bagexpansion=(bag | array)] ColumnName [limit Rowlimit]

    T | mvexpand  [bagexpansion=(bag | array)] [Name =] ArrayExpression [to typeof(Typename)] [limit Rowlimit]

**Аргументы**

* *ColumnName* — в выходных данных массивы из этого столбца будут развернуты в несколько строк. 
* *ArrayExpression* — выражение, которое возвращает массив. Если используется этот аргумент, добавляется новый столбец, а существующий сохраняется.
* *Name* — имя нового столбца.
* *Typename* — приводит развернутое выражение к определенному типу.
* *RowLimit* — максимальное количество строк, созданных из каждой исходной строки. Значение по умолчанию — 128.

**Результаты**

Несколько строк для каждого значения в массиве в именованном столбце или выражении массива.

Тип развернутого столбца всегда динамический. Чтобы выполнить обычное или статистическое вычисление значений, используйте приведение, например `todatetime()` или `toint()`.

Поддерживаются два режима развертывания контейнера свойств.

* `bagexpansion=bag`— контейнеры свойств развертываются в контейнеры свойств с одной записью. Это значение по умолчанию.
* `bagexpansion=array` — контейнеры свойств развертываются в структуры массива с двумя элементами `[`*key*`,`*value*`]`. Это позволяет единообразно обращаться к ключам и значениям (а также выполнять, например, подсчет разных значений в именах свойств).

**Примеры**

    exceptions | take 1
    | mvexpand details[0]

Разбивает запись исключения на строки для каждого элемента в поле подробных сведений.

### <a name="parse-operator"></a>Оператор parse
    T | parse "I got 2 socks for my birthday when I was 63 years old"
    with * "got" counter:long " " present "for" * "was" year:long *


    T | parse kind=relaxed
          "I got no socks for my birthday when I was 63 years old"
    with * "got" counter:long " " present "for" * "was" year:long *

    T |  parse kind=regex "I got socks for my 63rd birthday"
    with "(I|She) got " present " for .*?" year:long *

Извлекает значения из строки. Можно использовать сопоставление простых или регулярных выражений.

**Синтаксис**

    T | parse [kind=regex|relaxed] SourceText
        with [Match | Column [: Type [*]] ]  ...

**Аргументы**

* `T`— входная таблица.
* `kind`: 
  * `simple` (по умолчанию) — строки `Match` являются простыми строками.
  * `relaxed` — если текст не анализируется как тип столбца, для столбца задается значение NULL и анализ продолжается. 
  * `regex` — строки `Match` являются регулярными выражениями.
* `Text`— это столбец или другое выражение, результатом вычисления или преобразования которого является строка.
* *Match* — сопоставление следующей части строки, а затем ее удаление.
* *Column* — назначение этому столбцу следующей части строки. Если столбец не существует, он будет создан.
* *Type* — анализ следующей части строки согласно указанному типу, например int, date, double. 

**Результаты**

Входную таблицу, развернутую в соответствии со списком столбцов.

Элементы в предложении `with` , в свою очередь, сопоставляются с исходной строкой. Каждый элемент обрабатывает часть исходного текста. 

* При использовании строкового литерала или регулярного выражения курсор перемещается в рамках соответствия.
* При анализе регулярного выражения может использоваться оператор минимизации "?", чтобы как можно быстрее перейти к следующему соответствию.
* Имя столбца с типом анализирует текст согласно указанному типу. Если kind=relaxed не указано, то неудачно выполненный анализ делает недействительным сопоставление всего шаблона.
* Имя столбца без типа или с типом "строка" копирует минимальное число знаков для получения следующего соответствия.
* Знак *пропускает минимальное число знаков для получения следующего соответствия. Знак* можно использовать в начале и в конце шаблона или после типа, который отличается от типа "строка", или между соответствием строк.

Все элементы в шаблоне анализа должны точно соответствовать. В противном случае результаты не будут получены. Но если указано kind=relaxed, ошибка анализа типизированной переменной не приведет к остановке анализа.

**Примеры**

*Простой:*

```AIQL

// Test without reading a table:
 range x from 1 to 1 step 1 
 | parse "I got 2 socks for my birthday when I was 63 years old" 
    with 
     *   // skip until next match
     "got" 
     counter: long // read a number
     " " // separate fields
     present // copy string up to next match
     "for" 
     *  // skip until next match
     "was" 
     year:long // parse number
     *  // skip rest of string
```

| x | Счетчик | Подарок | Год |
| --- | --- | --- | --- |
| 1 |2 |Носки |63 |

*Упрощенный:*

Если входные данные содержат точное совпадение для каждого типизированного столбца, то упрощенный анализ предоставляет такие же результаты, как и простой анализ. Но если один из типизированных столбцов не точно проанализирован, упрощенный анализ продолжает обрабатывать остальную часть шаблона, в то время как простой анализ останавливается, не создавая какого-либо результата.

```AIQL

// Test without reading a table:
 range x from 1 to 1 step 1 
 | parse kind="relaxed"
        "I got several socks for my birthday when I was 63 years old" 
    with 
     *   // skip until next match
     "got" 
     counter: long // read a number
     " " // separate fields
     present // copy string up to next match
     "for" 
     *  // skip until next match
     "was" 
     year:long // parse number
     *  // skip rest of string
```


| x | Подарок | Год |
| --- | --- | --- |
| 1 |Носки |63 |

*Регулярное выражение:*

```AIQL

// Run a test without reading a table:
range x from 1 to 1 step 1 
// Test string:
| extend s = "Event: NotifySliceRelease (resourceName=Scheduler, totalSlices=27, sliceNumber=16, lockTime=02/17/2016 07:31, releaseTime=02/17/2016 08:41:00, previousLockTime=02/17/2016 06:20:00 ) }" 
// Parse it:
| parse kind=regex s 
  with ".*?=" resource 
       ", total.*?sliceNumber=" slice:long *
       "lockTime=" lock
       ",.*?releaseTime=" release 
       ",.*?previousLockTime=" previous:date 
       @".*\)" *
| project-away x, s
```

| resource | Срез | lock | release | Предыдущее время |
| --- | --- | --- | --- | --- |
| Планировщик |16 |02/17/2016 07:31:00 |02/17/2016 08:41 |2016-02-17T06:20:00Z |

### <a name="project-operator"></a>Оператор project
    T | project cost=price*quantity, price

Выберите столбцы для включения, переименования или удаления, а затем вставьте новые вычисляемые столбцы. Порядок столбцов в результате определяется порядком аргументов. В результат будут включены только указанные в аргументах столбцы. Другие входные данные удаляются.  (См. также сведения об `extend`.)

**Синтаксис**

    T | project ColumnName [= Expression] [, ...]

**Аргументы**

* *T* — входная таблица.
* *ColumnName* — имя столбца, который будет отображен в выходных данных. Если аргумент *Expression*отсутствует, столбец с таким именем должен присутствовать во входных данных. В [именах](#names) учитывается регистр и могут содержаться буквы и цифры, а также символы подчеркивания (_). Используйте `['...']` или `["..."]`, чтобы заключать в кавычки ключевые слова или имена с другими знаками.
* *Expression* — необязательное скалярное выражение, указывающее на входные столбцы. 
  
    Можно получить новый вычисляемый столбец с именем существующего столбца во входных данных.

**Результаты**

Таблица со столбцами, именованными как аргументы, а также количеством строк, соответствующим входной таблице.

**Пример**

В следующем примере показано несколько типов операций, которые можно выполнить с помощью оператора `project` . Входная таблица `T` содержит три столбца типа `int`: `A`, `B` и `C`. 

```AIQL
T
| project
    X=C,               // Rename column C to X
    A=2*B,             // Calculate a new column A from the old B
    C=strcat("-",tostring(C)), // Calculate a new column C from the old C
    B=2*B,              // Calculate a new column B from the old B
    ['where'] = client_City // rename, using a keyword as a column name
```

### <a name="project-away-operator"></a>Оператор project-away
    T | project-away column1, column2, ...

Исключение указанных столбцов. Результат содержит все входные столбцы, кроме указанных.

### <a name="range-operator"></a>Оператор range
    range LastWeek from ago(7d) to now() step 1d

Создает таблицу значений с одним столбцом. Обратите внимание: входные данные конвейера отсутствуют. 

| LastWeek |
| --- |
| 2015-12-05 09:10:04.627 |
| 2015-12-06 09:10:04.627 |
| ... |
| 2015-12-12 09:10:04.627 |

**Синтаксис**

    range ColumnName from Start to Stop step Step

**Аргументы**

* *ColumnName* — имя одного столбца в выходной таблице.
* *Start* — наименьшее значение в выходных данных.
* *Stop* — наибольшее значение, созданное в выходных данных (или ограничение на максимальное значение, если в связи с применением аргумента *step* это значение пропускается).
* *Step* — разница между двумя последовательными значениями. 

Аргументы должны иметь числовые значения, значения даты или временного диапазона. Они не могут ссылаться на столбцы таблицы. (Если вы хотите вычислить диапазон на основе входной таблицы, используйте [*функцию* range](#range), например с [оператором mvexpand](#mvexpand-operator).) 

**Результаты**

Таблица с одним столбцом с именем *ColumnName* и следующими значениями: *Start*, *Start* + *Step* и так далее до *Stop* включительно.

**Пример**  

```AIQL
range Steps from 1 to 8 step 3
```

Таблица с одним столбцом с именем `Steps`, типом `long` и значениями `1`, `4` и `7`.

**Пример**

    range LastWeek from bin(ago(7d),1d) to now() step 1d

Таблица со значениями в полночь за последние семь дней. Функция bin (floor) позволяет сократить время до начала каждого дня.

**Пример**  

```AIQL
range timestamp from ago(4h) to now() step 1m
| join kind=fullouter
  (traces
      | where timestamp > ago(4h)
      | summarize Count=count() by bin(timestamp, 1m)
  ) on timestamp
| project Count=iff(isnull(Count), 0, Count), timestamp
| render timechart  
```

В этом примере оператор `range` создает небольшую ad-hoc-таблицу измерения и на ее основе заполняет нулями все поля, для которых нет значений в исходных данных.

### <a name="reduce-operator"></a>Оператор reduce
    exceptions | reduce by outerMessage

Пытается сгруппировать похожие записи. Для каждой группы оператор выводит шаблон (`Pattern`), который считает наилучшим описанием этой группы, а также количество записей в ней (`Count`).

![](./media/app-insights-analytics-reference/reduce.png)

**Синтаксис**

    T | reduce by  ColumnName [ with threshold=Threshold ]

**Аргументы**

* *ColumnName* — анализируемый столбец. Он должен быть строкового типа.
* <seg>
  *Threshold* — пороговое значение в диапазоне {0..1}.</seg> Значение по умолчанию — 0,001. Для объемных входных данных пороговое значение должно быть небольшим. 

**Результаты**

Два столбца: `Pattern` и `Count`. Во многих случаях модель будет представлена полным значением из столбца. В некоторых случаях оператор может определить общие термины, заменив части переменных символом *.

Например, результат выполнения `reduce by city` может быть таким: 

| Модель | Count |
| --- | --- |
| Сан * |5182 |
| Сант * |2846 |
| Москва |3726 |
| \* -ор- \* |2730 |
| Париж |27 163 |

### <a name="render-directive"></a>Директива render
    T | render [ table | timechart  | barchart | piechart ]

Render указывает уровню представления данных способ отображения таблицы. Этот элемент должен быть последним в канале. Он является удобной альтернативой использованию элементов управления, позволяя сохранять запрос с помощью конкретного метода представления.

### <a name="restrict-clause"></a>Предложение restrict
Задает набор имен таблиц, доступных для операторов, которые следуют. Например:

    let e1 = requests | project name, client_City;
    let e2 =  requests | project name, success;
    // Exclude predefined tables from the union:
    restrict access to (e1, e2);
    union * |  take 10 

### <a name="sample-operator"></a>Оператор sample

Возвращает равномерно распределенные случайные строки из входной таблицы.


**Синтаксис**

    T | sample NumerOfRows

* *NumberOfRows* — возвращаемое число строк в выборке.

**Подсказка**

Используйте `Take`, когда вам не требуется получить равномерно распределенную выборку.


### <a name="sample-distinct-operator"></a>Оператор sample-distinct

Возвращает один столбец, который содержит указанное число разных значений запрошенного столбца. Сейчас не возвращает распределенную выборку.

**Синтаксис**

    T | sample-distinct NumberOfValues of ColumnName

* *NumberOfValues* — требуемая длина таблицы.
* *ColumnName* — требуемый столбец.

**Советы**

Выборку заполнения удобно получить, поместив оператор sample-distinct в инструкцию let, а затем выполнив фильтрацию с помощью оператора in (см. пример).
 
Если вам нужны максимальные значения, а не просто выборка, можно использовать оператор top-hitters.

Если вам нужны строки данных, а не просто значения конкретного столбца, см. описание [оператора sample](#sample-operator).

**Пример**

Выполняет выборку заполнения и дальнейшие вычисления, учитывая то, что одна строка не может превысить ограничения запроса.

```AIQL
let sampleops = toscalar(requests | sample-distinct 10 of OperationName);
requests | where OperationName in (sampleops) | summarize total=count() by OperationName
```
### <a name="search-operator"></a>Оператор search

Выполняет поиск строк в нескольких таблицах и столбцах.

**Синтаксис**

    search [kind=case_sensitive] [in (TableName, ...)] SearchToken

    T | search [kind=case_sensitive] SearchToken

    search [kind=case_sensitive] [in (TableName, ...)] SearchPredicate

    T | search [kind=case_sensitive] SearchPredicate

Проверяет вхождения определенной строки токена в любом столбце любой таблицы.
 
* *TableName*. Имя таблицы, определенной глобально (запросы, исключения и т. д.) или с помощью [предложения let](#let-clause). При необходимости можно использовать подстановочные знаки, например r*.
* *SearchToken*. Строка токена (слово целиком). При необходимости можно использовать конечные подстановочные знаки. Amster* совпадает с Amsterdam, но Amster нет.
* *SearchPredicate*. Логическое выражение в столбцах таблиц. В качестве подстановочного знака в именах столбцов можно использовать звездочку ("*").

**Примеры**

```AIQL
search "Amster*"  //All columns, all tables

search name has "home"  // one column

search * has "home"     // all columns

search in (requests, exceptions) "Amster*"  // two tables

requests | search "Amster*"

requests | search name has "home"

```




### <a name="sort-operator"></a>Оператор sort
    T | sort by country asc, price desc

Сортирует по порядку строки входной таблицы (по одному или нескольким столбцам).

**Псевдоним** `order`

**Синтаксис**

    T  | sort by Column [ asc | desc ] [ , ... ]

**Аргументы**

* *T* — входная таблица для сортировки.
* *Column* — столбец значений аргумента *T*, по которому выполняется сортировка. Значения должны быть представлены числовыми или строковыми значениями, значениями времени или даты.
* `asc` — сортировка по возрастанию. `desc` — сортировка по убыванию. Используется по умолчанию.

**Пример**

```AIQL
Traces
| where ActivityId == "479671d99b7b"
| sort by Timestamp asc
```
Все строки в таблице трассировки с определенным значением `ActivityId`, отсортированные по меткам времени.

### <a name="summarize-operator"></a>Оператор summarize
Создает таблицу, которая объединяет содержимое входной таблицы.

    requests
    | summarize count(), avg(duration), makeset(client_City) 
      by client_CountryOrRegion

В таблице отображено количество, средняя продолжительность запроса и группа городов каждой страны. Для каждой страны в выходных данных есть своя строка. В выходных столбцах отображено количество, средняя продолжительность, города и страна. Все остальные входные столбцы игнорируются.

    T | summarize count() by price_range=bin(price, 10.0)

В таблице отображено количество элементов с ценами в каждом интервале [0,10.0], [10.0,20.0] и т. д. Пример включает один столбец для подсчета и один столбец для диапазон цен. Все остальные входные столбцы игнорируются.

**Синтаксис**

    T | summarize
         [  [ Column = ] Aggregation [ , ... ] ]
         [ by
            [ Column = ] GroupExpression [ , ... ] ]

**Аргументы**

* *Column* — необязательное имя итогового столбца. По умолчанию это имя, получаемое из выражения. В [именах](#names) учитывается регистр и могут содержаться буквы и цифры, а также символы подчеркивания (_). Используйте `['...']` или `["..."]`, чтобы заключать в кавычки ключевые слова или имена с другими знаками.
* *Aggregation* — вызов статистической функции, например `count()` или `avg()`, с именами столбцов в качестве аргументов. Дополнительные сведения о статистических функциях см. [здесь](#aggregations).
* *GroupExpression* — выражение для столбцов, предоставляющее набор уникальных значений. Обычно это имя столбца, который уже содержит ограниченный набор значений, либо функция `bin()` с числовым или временным столбцом в качестве аргумента. 

Если указать числовое или временное выражение без `bin()`, аналитика автоматически применит его с интервалом `1h` для временных значений и интервалом `1.0` для числовых.

Если выражение *GroupExpression* не указано, вся таблица будет суммирована в одну выходную строку.

**Результаты**

Входные строки объединяются в группы с одинаковыми значениями выражений `by` . Затем указанные агрегатные функции выполняют вычисления и создают строку для каждой группы. Результат содержит столбцы `by` и хотя бы один столбец для каждого вычисленного статистического выражения. (Некоторые агрегатные функции возвращают несколько столбцов).

Результат содержит столько строк, сколько существует уникальных комбинаций значений `by` . Чтобы суммировать диапазоны числовых значений, можно ограничить диапазоны дискретными значениями с помощью `bin()` .

> [!NOTE]
> Для агрегатных выражений и выражений группирования допускаются произвольные выражения, но эффективнее использовать простые имена столбцов или функцию `bin()` для числовых столбцов.

### <a name="table-operator"></a>Оператор table

    table('pageViews')

Таблица, указанная в строке аргумента.

**Синтаксис**

    table(tableName)

**Аргументы**

* *tableName* — строка. Имя таблицы (статическое или результат предложения let).

**Примеры**

    table('requests');


    let size = (tableName: string) {
        table(tableName) | summarize sum(itemCount)
    };
    size('pageViews');



### <a name="take-operator"></a>Оператор take
Псевдоним оператора [limit](#limit-operator)

### <a name="top-operator"></a>Оператор top
    T | top 5 by Name desc nulls first

Возвращает первые записи ( *N* ), отсортированные по указанным столбцам.

**Синтаксис**

    T | top NumberOfRows by Sort_expression [ asc | desc ] [nulls first|nulls last] [, ... ]

**Аргументы**

* *NumberOfRows* — возвращаемое количество строк *T*.
* *Sort_expression* — выражение, по которому сортируются строки. Обычно это просто имя столбца. Можно указать несколько аргументов sort_expression.
* `asc` или `desc` (по умолчанию) могут указывать направление выбора элементов в диапазоне: снизу вверх или сверху вниз.
* `nulls first` или `nulls last` управляет отображением значений NULL. `First` — значение по умолчанию для `asc`, а `last` — для `desc`.

**Советы**

Выражение `top 5 by name` внешне эквивалентно `sort by name | take 5`. Однако первое работает быстрее и, в отличие от `take` , всегда возвращает отсортированные результаты.

### <a name="top-nested-operator"></a>Вложенный оператор верхнего уровня
    requests
    | top-nested 5 of name by count()
    , top-nested 3 of performanceBucket by count()
    , top-nested 3 of client_CountryOrRegion by count()
    | render barchart

Формирует иерархические результаты, где каждый следующий уровень детализирует предыдущий. Это позволяет, например, выделить пять основных запросов, для каждого из них определить три основных категории производительности и для каждой из этих категорий установить, из каких трех стран поступает большинство запросов.

**Синтаксис**

   T | N вложенных СТОЛБЦОВ верхнего уровня путем АГРЕГАЦИИ [, ...]

**Аргументы**

* N:int — число строк для возвращения или передачи на следующий уровень. В запросе с тремя уровнями, где N — 5, 3 и 3, общее количество строк составит 45.
* СТОЛБЕЦ — столбец для группирования данных путем агрегации. 
* АГРЕГАТ — [статистическая функция](#aggregations) для применения к каждой группе строк. От результатов агрегации будет зависеть выбор верхних групп в представлении.

### <a name="union-operator"></a>Оператор union
     Table1 | union Table2, Table3

Принимает несколько таблиц и возвращает строки из них. 

**Синтаксис**

    T | union [ kind= inner | outer ] [ withsource = ColumnName ] Table2 [ , ...]  

    union [ kind= inner | outer ] [ withsource = ColumnName ] Table1, Table2 [ , ...]  

**Аргументы**

* *Таблица1*, *таблица2*…
  * Имя таблицы, например `requests`, или таблицы, определенной в [предложении let](#let-clause).
  * Выражение запроса, например `(requests | where success=="True")`
  * Набор таблиц, указанных с помощью подстановочного знака. Например, `e*` формирует объединение всех таблиц, определенных в предыдущих предложениях let, имена которых начинаются с "e", вместе с таблицей exceptions.
* `kind`: 
  * `inner` — результат содержит подмножество столбцов, которые являются общими для всех входных таблиц.
  * `outer` — результат содержит все столбцы, которые присутствуют в любых входных таблицах. Для ячеек, которые не определены во входной таблице, будет задано значение `null`.
* `withsource=`*ColumnName* — если аргумент указан, выходные данные будут содержать столбец с именем *ColumnName*. В нем для каждой строки будет указано, из какой исходной таблицы она взята. Если вы хотите указать имя, отображающееся в этом столбце, используйте оператор [as](#as-operator) в конце каждого табличного выражения.

**Результаты**

Таблица с таким количеством строк, как во всех входных таблицах, и таким количеством столбцов, сколько уникальных имен столбцов во входных данных.

Какое-либо упорядочение строк не гарантируется.


**Пример**

Количество различных пользователей, которые за последние 12 часов создали событие `exceptions` или `traces`. В результате в столбце SourceTable будет указано одно из значений: exceptions или traces:

```AIQL
    
    union withsource=SourceTable kind=outer exceptions, traces
    | where timestamp > ago(12h)
    | summarize dcount(user_Id) by SourceTable
```

Это более эффективный способ получить тот же результат, так как перед объединением каждая таблица отфильтровывается.

```AIQL
    exceptions
    | where timestamp > ago(24h) | as exceptions
    | union withsource=SourceTable kind=outer (requests | where timestamp > ago(12h) | as traces)
    | summarize dcount(user_Id) by SourceTable 
```

Используйте оператор [as](#as-operator), чтобы указать имя, которое будет отображаться в исходном столбце.

#### <a name="forcing-an-order-of-results"></a>Принудительное задание порядка результатов

Объединение не гарантирует какого-либо определенного упорядочения в строках результатов.
Чтобы получать результаты в одном и том же порядке при каждом выполнении запроса, добавьте столбец тегов в каждую входную таблицу.

    let r1 = (traces | count | extend tag = 'r1');
    let r2 = (requests | count| extend tag = 'r2');
    let r3 = (pageViews | count | extend tag = 'r3');
    r1 | union r2,r3 | sort by tag

#### <a name="see-also"></a>Дополнительные материалы

Рассмотрите [оператор join](#join-operator) в качестве альтернативы.

### <a name="where-operator"></a>Оператор where
     requests | where resultCode=="200"

Отфильтровывает таблицу для подмножества строк, которые удовлетворяют предикату.

**Псевдоним** `filter`

**Синтаксис**

    T | where Predicate
    T | where * has Term

**Аргументы**

* *T* — табличные входные данные, записи которых будут отфильтрованы.
* *Predicate* — [выражение](#boolean) `boolean` для столбцов таблицы *T*. Вычисляется отдельно для каждой строки в *T*.
* *Term* — строка, которая должна совпадать с целым словом в столбце.

**Результаты**

Строки из таблицы *T*, для которых *Predicate* имеет значение `true`.

**Советы**

Обеспечить максимальную производительность можно так.

* **Используйте простые сравнения** между именами столбцов и константами. (Под константой здесь понимается значение, постоянное для всех строк таблицы. Соответственно, допустимо использовать функции `now()` и `ago()`, а также скалярные значения, назначенные с помощью [предложения `let`](#let-clause).)
  
    Например, лучше использовать `where Timestamp >= ago(1d)`, чем `where floor(Timestamp, 1d) == ago(1d)`.
* **Вводите сначала более простые элементы**. Если у вас есть несколько предложений, объединенных с помощью `and`, то сначала вводите предложения, которые включают только один столбец. Пример правильного порядка предложений: `Timestamp > ago(1d) and OpId == EventId`.

**Пример**

```AIQL
traces
| where Timestamp > ago(1h)
    and Source == "Kuskus"
    and ActivityId == SubActivityIt 
```

Записи не старше 1 часа из источника Kuskus, у которых есть по два столбца с одним значением. 

Обратите внимание: сравнение двух столбцов вводится в последнюю очередь, так как эта операция не предполагает использование индекса и запуска проверки.



## <a name="aggregations"></a>Агрегаты
Агрегаты — это функции, которые используются для объединения значений в группы, созданные в [операции суммирования](#summarize-operator). Например, в этом запросе dcount() является агрегатной функцией:

    requests | summarize dcount(name) by success

### <a name="any"></a>любой
    any(Expression)

Случайным образом выбирает одну строку группы и возвращает значение указанного выражения.

Это полезно, например, когда в каком-то столбце есть большое количество подобных значений (например, в столбце "текст ошибки") и вы хотите сделать выборку из этого столбца по уникальному значению составного ключа группы. 

**Пример**  

```

traces 
| where timestamp > now(-15min)  
| summarize count(), any(message) by operation_Name 
| top 10 by count_level desc 
```

<a name="argmin"></a>
<a name="argmax"></a>

### <a name="argmin-argmax"></a>argmin, argmax
    argmin(ExprToMinimize, * | ExprToReturn  [ , ... ] )
    argmax(ExprToMaximize, * | ExprToReturn  [ , ... ] ) 

Находит в группе строку, для которой выражение *ExprToMaximize* будет минимальным (или максимальным) и возвращает значение *ExprToReturn* (можно указать `*`, чтобы вернуть всю строку).

**Совет**. Переданные столбцы переименовываются автоматически. Чтобы убедиться, что вы используете правильные имена, проверьте результаты с помощью `take 5`, прежде чем передавать их другому оператору.

**Примеры**

Показать время возникновения самого продолжительного запроса для каждого имени запроса:

    requests | summarize argmax(duration, timestamp) by name

Показать все подробности выполнения самого длинного запроса, а не только метку времени:

    requests | summarize argmax(duration, *) by name


Найти наименьшее значение каждой метрики с ее меткой времени и другими данными:

    metrics 
    | summarize minValue=argmin(value, *) 
      by name


![](./media/app-insights-analytics-reference/argmin.png)

### <a name="avg"></a>avg
    avg(Expression)

Вычисляет среднее значение выражения *Expression* для группы.

### <a name="buildschema"></a>buildschema
    buildschema(DynamicExpression)

Возвращает минимальную схему, которая допускает все значения *DynamicExpression*. 

Указанный в параметре столбец должен иметь тип `dynamic` — массив или контейнер свойств. 

**Пример**

    exceptions | summarize buildschema(details)

Результат:

    { "indexer":
     {"id":"string",
       "parsedStack":
       { "indexer": 
         {  "level":"int",
            "assembly":"string",
            "fileName":"string",
            "method":"string",
            "line":"int"
         }},
      "outerId":"string",
      "message":"string",
      "type":"string",
      "rawStack":"string"
    }}

Обратите внимание, что `indexer` используется для обозначения тех случаев, когда следует применить числовой индекс. Некоторые допустимые пути для этой схемы будут следующими (предполагается, что эти индексы в этом примере находятся в диапазоне):

    details[0].parsedStack[2].level
    details[0].message
    arraylength(details)
    arraylength(details[0].parsedStack)

**Пример**

Предположим, что входной столбец имеет три динамических значения:

|  |
| --- |
| `{"x":1, "y":3.5}` |
| `{"x":"somevalue", "z":[1, 2, 3]}` |
| `{"y":{"w":"zzz"}, "t":["aa", "bb"], "z":["foo"]}` |

Итоговая схема будет следующей:

    {
      "x":["int", "string"],
      "y":["double", {"w": "string"}],
      "z":{"indexer": ["int", "string"]},
      "t":{"indexer": "string"}
    }

Схема указывает следующее:

* Корневой объект представляет собой контейнер с четырьмя свойствами x, y, z и t.
* Свойство x, которое может быть типа int или string.
* Свойство y, которое может либо типа double, либо другой контейнер со свойством w типа string.
* Ключевое слово ``indexer`` указывает, что z и t являются массивами.
* Каждый элемент в массиве z имеет тип int или string.
* t представляет собой массив строк.
* Каждое свойство является необязательным, и любой массив может быть пустым.

##### <a name="schema-model"></a>Модель схемы
Синтаксис возвращаемой схемы следующий:

    Container ::= '{' Named-type* '}';
    Named-type ::= (name | '"indexer"') ':' Type;
    Type ::= Primitive-type | Union-type | Container;
    Union-type ::= '[' Type* ']';
    Primitive-type ::= "int" | "string" | ...;

Они эквивалентны подмножеству аннотаций типов TypeScript, закодированному как динамическое значение. В Typescript пример схемы будет следующим:

    var someobject:
    {
      x?: (number | string),
      y?: (number | { w?: string}),
      z?: { [n:number] : (int | string)},
      t?: { [n:number]: string }
    }


### <a name="count"></a>count
    count([ Predicate ])

Возвращает число строк, для которых предикат *Predicate* принимает значение `true`. Если предикат *Predicate* не указан, возвращается общее число записей в группе. 

**Совет для повышения производительности**. Используйте `summarize count(filter)` вместо `where filter | summarize count()`.

> [!NOTE]
> Не используйте count() для поиска числа запросов, исключений или других произошедших событий. При обработке [выборки](app-insights-sampling.md) количество точек данных, хранящихся в Application Insights, окажется меньше, чем количество фактических событий. Вместо этого используйте `summarize sum(itemCount)...`. Свойство itemCount отражает число исходных событий, представленных каждой сохраненной точкой данных.
> 
> 

### <a name="countif"></a>countif
    countif(Predicate)

Возвращает число строк, для которых предикат *Predicate* принимает значение `true`.

**Совет для повышения производительности**. Используйте `summarize countif(filter)` вместо `where filter | summarize count()`.

> [!NOTE]
> Не используйте countif() для поиска количества запросов, исключений или других произошедших событий. При обработке [выборки](app-insights-sampling.md) количество точек данных окажется меньше, чем количество фактических событий. Вместо этого используйте `summarize sum(itemCount)...`. Свойство itemCount отражает число исходных событий, представленных каждой сохраненной точкой данных.
> 
> 

### <a name="dcount"></a>dcount
    dcount( Expression [ ,  Accuracy ])

Возвращает приблизительное количество различных значений выражения *Expr* для группы. (Чтобы получить список уникальных значений, используйте [`makeset`](#makeset).)

Приоритет между скоростью и точностью контролирует необязательный параметр *Accuracy*.

* `0` — наименее точное и самое быстрое вычисление.
* `1` — вычисление по умолчанию, при котором точность и время расчета сбалансированы; вероятность ошибки составляет около 0,8 %.
* `2` — наиболее точное и самое медленное вычисление; вероятность ошибки составляет около 0,4 %.

**Пример**

    pageViews 
    | summarize cities=dcount(client_City) 
      by client_CountryOrRegion

![](./media/app-insights-analytics-reference/dcount.png)

### <a name="dcountif"></a>dcountif
    dcountif( Expression, Predicate [ ,  Accuracy ])

Возвращает приблизительное количество уникальных значений для *Expr* строк в группе, для которой предикат *Predicate* имеет значение true. (Чтобы получить список уникальных значений, используйте [`makeset`](#makeset).)

Приоритет между скоростью и точностью контролирует необязательный параметр *Accuracy*.

* `0` — наименее точное и самое быстрое вычисление.
* `1` — вычисление по умолчанию, при котором точность и время расчета сбалансированы; вероятность ошибки составляет около 0,8 %.
* `2` — наиболее точное и самое медленное вычисление; вероятность ошибки составляет около 0,4 %.

**Пример**

    pageViews 
    | summarize cities=dcountif(client_City, client_City startswith "St") 
      by client_CountryOrRegion


### <a name="makelist"></a>makelist
    makelist(Expr [ ,  MaxListSize ] )

Возвращает массив `dynamic` (JSON) со всеми значениями *Expr* в группе. 

* *MaxListSize* — необязательное целочисленное ограничение для максимального количества возвращаемых элементов (по умолчанию — *128*).

### <a name="makeset"></a>makeset
    makeset(Expression [ , MaxSetSize ] )

Возвращает массив `dynamic` (JSON) с набором различных значений, которые выражение *Expr* принимает в группе. (Совет. Для простого подсчета уникальных значений используйте [`dcount`](#dcount).)

* *MaxSetSize* — необязательное целочисленное ограничение для максимального количества возвращаемых элементов (по умолчанию — *128*).

**Пример**

    pageViews 
    | summarize cities=makeset(client_City) 
      by client_CountryOrRegion

![](./media/app-insights-analytics-reference/makeset.png)

Сведения о противоположной функции см. в разделе [Оператор `mvexpand`](#mvexpand-operator).

### <a name="max-min"></a>max, min
    max(Expr)

Вычисляет максимальное значение выражения *Expr*.

    min(Expr)

Вычисляет максимальное значение выражения *Expr*.

**Совет**. Эта функция возвращает только минимальный или максимальный результат, например наибольшую или наименьшую цену. Если вам нужны и другие столбцы этой строки, например имя поставщика с наименьшей ценой, используйте [argmin или argmax](#argmin-argmax).

<a name="percentile"></a>
<a name="percentiles"></a>
<a name="percentilew"></a>
<a name="percentilesw"></a>

### <a name="percentile-percentiles-percentilew-percentilesw"></a>percentile, percentiles, percentilew, percentilesw
    percentile(Expression, Percentile)

Возвращает оценку выражения *Expression* для группы по указанному процентилю. Точность зависит от плотности заполнения области процентиля.

    percentiles(Expression, Percentile1 [ , Percentile2 ...] )

То же, что и `percentile()`, но вычисляет количество значений процентилей (это быстрее, чем отдельно вычислять каждый процентиль).

    percentilew(Expression, WeightExpression, Percentile)

Взвешенный процентиль. Используется для предварительно статистически обработанных данных.  `WeightExpression` представляет собой целое число, указывающее, сколько исходных строк представляется каждой сводной строкой.

    percentilesw(Expression, WeightExpression, Percentile1, [, Percentile2 ...])

То же, что и `percentilew()`, но вычисляет количество значений процентилей.

**Примеры**

Значение `duration` , ниже которого находится 95 % значений, а выше — 5 % значений выборки, вычисленное для каждого имени запроса.

    request 
    | summarize percentile(duration, 95)
      by name

Чтобы выполнить вычисления для всей таблицы, пропустите "by...".

Одновременно вычислите несколько процентилей для других имен запросов:

    requests 
    | summarize 
        percentiles(duration, 5, 20, 50, 80, 95) 
      by name

![](./media/app-insights-analytics-reference/percentiles.png)

Результаты показывают, что для запроса /Events/Index 5 % запросов были выполнены за 2,44 с, половина запросов — за 3,52 с, а 5 % — более чем за 6,85 с.

Вычисление множественной статистики:

    requests 
    | summarize 
        count(), 
        avg(Duration),
        percentiles(Duration, 5, 50, 95)
      by name

#### <a name="weighted-percentiles"></a>Взвешенные процентили
Используйте функции взвешенных процентилей в случаях, когда данные были предварительно статистически обработаны. 

Предположим, приложение выполняет тысячи операций в секунду, и вы хотите знать их значение задержки. Простым решением было бы создать запрос Application Insights или пользовательское событие для каждой операции. Это создаст большой объем трафика, несмотря на то, что для его уменьшения будет задействована адаптивная выборка. Но можно реализовать и лучшее решение: написать в приложении код для статистической обработки данных перед их отправкой в Application Insights. Агрегатные сводки будут отправляться через равные интервалы, снижая скорость передачи данных, возможно, до нескольких точек в минуту.

Код принимает поток измерений задержки в миллисекундах. Например:

     { 15, 12, 2, 21, 2, 5, 35, 7, 12, 22, 1, 15, 18, 12, 26, 7 }

Выполняется подсчет измерений в следующих ячейках: `{ 10, 20, 30, 40, 50, 100 }`

Периодически выполняются вызовы TrackEvent, по одному для каждого контейнера, при этом в каждом вызове применяются пользовательские измерения: 

    foreach (var latency in bins.Keys)
    { telemetry.TrackEvent("latency", null, 
         new Dictionary<string, double>
         ({"latency", latency}, {"opCount", bins[latency]}}); }

В аналитике каждая такая группа событий отображается следующим образом:

| `opCount` | `latency` | значение |
| --- | --- | --- |
| 8 |10 |= 8 операций в ячейке с задержкой 10 мс |
| 6 |20 |= 6 операций в ячейке с задержкой 20 мс |
| 3 |30 |= 3 операции в ячейке с задержкой 30 мс |
| 1 |40 |= 1 операция в ячейке с задержкой 40 мс |

Чтобы получить точное представление об исходном распределении показателей задержки событий, используется `percentilesw`.

    customEvents | summarize percentilesw(latency, opCount, 20, 50, 80)

Результаты будут такими же, как при использовании обычных `percentiles` на исходном наборе измерений.

> [!NOTE]
> Взвешенные процентили не применяются к [данным выборки](app-insights-sampling.md), где каждая строка выборки представляет собой случайную выборку исходных строк, а не ячейку. Функции обычных процентилей подходят для данных выборки.
> 
> 

#### <a name="estimation-error-in-percentiles"></a>Ошибка оценки в процентилях
Статистическая оценка процентилей предоставляет приблизительное значение с помощью [T-Digest](https://github.com/tdunning/t-digest/blob/master/docs/t-digest-paper/histo.pdf). 

Некоторые важные моменты: 

* Границы ошибки оценки изменяются со значением запрошенного процентиля. Наивысшая точность наблюдается на концах шкалы [0..100], процентили 0 и 100 — это точное минимальное и максимальное значения распределения. Точность постепенно снижается к середине шкалы. Она худшая на медиане и ограничена сверху 1 %. 
* Границы ошибки наблюдаются на диапазоне, а не на значении. Предположим, что функция percentile(X, 50) вернула значение Xm. Оценка гарантирует, что не менее 49 % и не более 51 % значений X меньше, чем Xm. Теоретического ограничения на разницу между Xm и X нет.

### <a name="stdev"></a>stdev
     stdev(Expr)

Возвращает стандартное отклонение выражения *Expr* для группы.

### <a name="variance"></a>variance
    variance(Expr)

Возвращает отклонение выражения *Expr* для группы.

### <a name="sum"></a>sum
    sum(Expr)

Возвращает сумму выражения *Expr* для группы.                      

## <a name="scalars"></a>Скаляры
[Приведения](#casts) | [Операции сравнения](#scalar-comparisons)
<br/>
[gettype](#gettype) | [hash](#hash) | [iff](#iff) |  [isnull](#isnull) | [isnotnull](#isnotnull) | [notnull](#notnull) | [toscalar](#toscalar)

Ниже приведены поддерживаемые типы.

| Тип | Дополнительные имена | Эквивалентный тип .NET |
| --- | --- | --- |
| `bool` |`boolean` |`System.Boolean` |
| `datetime` |`date` |`System.DateTime` |
| `dynamic` | |`System.Object` |
| `guid` |`uuid`, `uniqueid` |`System.Guid` |
| `int` | |`System.Int32` |
| `long` | |`System.Int64` |
| `double` |`real` |`System.Double` |
| `string` | |`System.String` |
| `timespan` |`time` |`System.TimeSpan` |

### <a name="casts"></a>Приведение типов
Типы можно приводить друг к другу. Как правило, если преобразование имеет смысл, оно будет работать:

    todouble(10), todouble("10.6")
    toint(10.6) == 11
    floor(10.6) == 10
    toint("200")
    todatetime("2016-04-28 13:02")
    totimespan("1.5d"), totimespan("1.12:00:00")
    toguid("00000000-0000-0000-0000-000000000000")
    tostring(42.5)
    todynamic("{a:10, b:20}")

Проверьте, можно ли преобразовать строку в конкретный тип:

    iff(notnull(todouble(customDimensions.myValue)),
       ..., ...)

### <a name="scalar-comparisons"></a>Скалярные сравнения
|  |  |
| --- | --- |
| `<` |Меньше |
| `<=` |Меньше или равно |
| `>` |Больше |
| `>=` |Больше или равно |
| `<>` |Не равно |
| `!=` |Не равно |
| `in` |Правый операнд является массивом (динамическим), а левый операнд равен одному из его элементов. |
| `!in` |Правый операнд является массивом (динамическим), а левый операнд не равен ни одному из его элементов. |

### <a name="gettype"></a>gettype
**Возвращает**

Строка, представляющая базовый тип хранилища для единственного аргумента. Это особенно удобно для значений типа `dynamic`. Для них `gettype()` покажет, как закодировано значение.

**Примеры**

|  |  |
| --- | --- |
| `gettype("a")` |`"string" ` |
| `gettype(111)` |`"long" ` |
| `gettype(1==1)` |`"int8"` |
| `gettype(now())` |`"datetime" ` |
| `gettype(1s)` |`"timespan" ` |
| `gettype(parsejson('1'))` |`"int" ` |
| `gettype(parsejson(' "abc" '))` |`"string" ` |
| `gettype(parsejson(' {"abc":1} '))` |`"dictionary"` |
| `gettype(parsejson(' [1, 2, 3] '))` |`"array"` |
| `gettype(123.45)` |`"real" ` |
| `gettype(guid(12e8b78d-55b4-46ae-b068-26d7a0080254))` |`"guid"` |
| `gettype(parsejson(''))` |`"null"` |
| `gettype(1.2)==real` |`true` |

### <a name="hash"></a>hash
**Синтаксис**

    hash(source [, mod])

**Аргументы**

* *source*— исходный скаляр, для которого вычисляется хэш.
* *mod*— значение модуля (остатка от деления), которое будет применено к полученному в результате хэшу.

**Результаты**

Значение xxhash (тип long) для заданного скаляра, указанное значение модуля (если указано).

**Примеры**

```
hash("World")                   // 1846988464401551951
hash("World", 100)              // 51 (1846988464401551951 % 100)
hash(datetime("2015-01-01"))    // 1380966698541616202
```
### <a name="iff"></a>iff
Функция `iff()` вычисляет первый аргумент (предикат) и возвращает значение второго или третьего аргументов в зависимости от значения предиката (`true` или `false`). Второй и третий аргументы должны быть одного типа.

**Синтаксис**

    iff(predicate, ifTrue, ifFalse)


**Аргументы**

* *predicate* — выражение, которое возвращает значение типа `boolean`.
* *ifTrue* — выражение, которое вычисляется и значение которого возвращается из функции, если *predicate* принимает значение `true`.
* *ifFalse* — выражение, которое вычисляется и значение которого возвращается из функции, если *predicate* принимает значение `false`.

**Результаты**

Эта функция возвращает значение *ifTrue*, если *predicate* принимает значение `true`. В противном случае она возвращает значение *ifFalse*.

**Пример**

```
iff(floor(timestamp, 1d)==floor(now(), 1d), "today", "anotherday")
```

<a name="isnull"/></a>
<a name="isnotnull"/></a>
<a name="notnull"/></a>

### <a name="isnull-isnotnull-notnull"></a>isnull, isnotnull, notnull
    isnull(parsejson("")) == true

Принимает один аргумент и сообщает, равен ли он null.

**Синтаксис**

    isnull([value])


    isnotnull([value])


    notnull([value])  // alias for isnotnull

**Результаты**

Значение true или false в зависимости от того, равен ли аргумент null.

| x | isnull(x) |
| --- | --- |
| "" |нет |
| "x" |нет |
| parsejson("") |Да |
| parsejson("[]") |нет |
| parsejson("{}") |нет |

**Пример**

    T | where isnotnull(PossiblyNull) | count

Обратите внимание, что того же эффекта можно добиться другими способами:

    T | summarize count(PossiblyNull)

### <a name="toscalar"></a>toscalar
Оценивает запрос или выражение и возвращает результат в виде одного значения. Эта функция пригодится для промежуточных вычислений, например, при расчете общего числа событий и использовании полученного значения как базового уровня.

**Синтаксис**

    toscalar(query)
    toscalar(scalar)

**Результаты**

Вычисленный аргумент. Если аргументом является таблица, возвращается первый столбец первой строки. (Рекомендуется упорядочить таблицу таким образом, чтобы у аргумента был только один столбец и одна строка.)

**Пример**

```AIQL

    // Get the count of requests 5 days ago:
    let baseline = toscalar(requests  
        | where floor(timestamp, 1d) == floor(ago(5d),1d) | count);
    // List the counts relative to that baseline:
    requests | summarize daycount = count() by floor(timestamp, 1d)  
    | extend relative = daycount - baseline
```




### <a name="boolean-literals"></a>Логические литералы
    true == 1
    false == 0
    gettype(true) == "int8"
    typeof(bool) == typeof(int8)

### <a name="boolean-operators"></a>Логические операторы
    and 
    or 

### <a name="convert-to-boolean"></a>Преобразование в логическое значение

Если у вас есть строка `aStringBoolean`, которая содержит значение true или false, ее можно преобразовать в логическое значение:

    booleanResult = aStringBoolean =~ "true"



## <a name="numbers"></a>Числа
[abs](#abs) | [bin](#bin) | [exp](#exp) | [floor](#floor) | [gamma](#gamma) |[log](#log) | [rand](#rand) | [range](#range) | [sqrt](#sqrt) 
| [todouble](#todouble) | [toint](#toint) | [tolong](#tolong)

### <a name="numeric-literals"></a>Числовые литералы
|  |  |
| --- | --- |
| `42` |`long` |
| `42.0` |`real` |

### <a name="arithmetic-operators"></a>Арифметические операторы
|  |  |
| --- | --- |
| + |Добавить |
| - |Вычитание |
| * |Умножение |
| / |Деление |
| % |Остаток от деления |
| `<` |Меньше |
| `<=` |Меньше или равно |
| `>` |Больше |
| `>=` |Больше или равно |
| `<>` |Не равно |
| `!=` |Не равно |

### <a name="abs"></a>abs
**Синтаксис**

    abs(x)

**Аргументы**

* x — целое число, действительное или интервал времени

**Результаты**

    iff(x>0, x, -x)

<a name="bin"></a><a name="floor"></a>

### <a name="bin-floor"></a>bin, floor
Округляет значения до целого, кратного указанному размеру группы. Часто используется в запросе [`summarize by`](#summarize-operator) . При наличии разрозненного набора значений они будут сгруппированы в меньший набор определенных значений.

Псевдоним `floor`.

**Синтаксис**

     bin(value, roundTo)
     floor(value, roundTo)

**Аргументы**

* *value* — число, дата или временной диапазон. 
* *roundTo* — размер ячейки. Число, дата или временной диапазон, на который делится *value*. 

**Результаты**

Ближайшее число, кратное *roundTo* и меньшее *value*.  

    (toint(value/roundTo)) * roundTo

**Примеры**

| Expression | Результат |
| --- | --- |
| `bin(4.5, 1)` |`4.0` |
| `bin(time(16d), 7d)` |`14d` |
| `bin(datetime(1953-04-15 22:25:07), 1d)` |`datetime(1953-04-15)` |

Следующее выражение вычисляет гистограмму длительности с размером сегмента в 1 секунду:

```AIQL

    T | summarize Hits=count() by bin(Duration, 1s)
```

### <a name="exp"></a>exp
    exp(v)   // e raised to the power v
    exp2(v)  // 2 raised to the power v
    exp10(v) // 10 raised to the power v


### <a name="floor"></a>floor
Псевдоним для [`bin()`](#bin).

### <a name="gamma"></a>gamma
[Функция gamma](https://en.wikipedia.org/wiki/Gamma_function)

**Синтаксис**

    gamma(x)

**Аргументы**

* *x* — вещественное число.

Для положительных целых чисел `gamma(x) == (x-1)!`. Например: `gamma(5) == 4 * 3 * 2 * 1`.

Ознакомьтесь также со сведениями о функции [loggamma](#loggamma).

### <a name="log"></a>log
    log(v)    // Natural logarithm of v
    log2(v)   // Logarithm base 2 of v
    log10(v)  // Logarithm base 10 of v


`v` должно быть вещественным числом > 0. В противном случае возвращается значение null.

### <a name="loggamma"></a>loggamma
Натуральный логарифм абсолютного значения [функции gamma](#gamma).

**Синтаксис**

    loggamma(x)

**Аргументы**

* *x* — вещественное число.

### <a name="rand"></a>rand
Генератор случайных чисел.

* `rand()` — вещественное число от 0,0 до 1,0.
* `rand(n)` — целое число от 0 до n-1.

### <a name="sqrt"></a>sqrt
Функция квадратного корня.  

**Синтаксис**

    sqrt(x)

**Аргументы**

* *x* — вещественное число >= 0.

**Результаты**

* Положительное число, такое как `sqrt(x) * sqrt(x) == x`
* `null`, если аргумент является отрицательным числом или не может быть преобразован в значение `real`. 

### <a name="toint"></a>toint
    toint(100)        // cast from long
    toint(20.7) == 20 // nearest int below double
    toint(20.4) == 20 // nearest int below double
    toint("  123  ")  // parse string
    toint(a[0])       // cast from dynamic
    toint(b.c)        // cast from dynamic


### <a name="todouble"></a>todouble
    todouble(20) == 20.0 // conversion from long or int
    todouble(" 12.34 ")  // parse string
    todouble(a[0])       // cast from dynamic
    todouble(b.c)        // cast from dynamic


### <a name="tolong"></a>tolong
    tolong(20.7) == 20 // conversion from double
    tolong(20.4) == 20 // conversion from double
    tolong("  123  ")  // parse string
    tolong(a[0])       // cast from dynamic
    tolong(b.c)        // cast from dynamic


## <a name="date-and-time"></a>Дата и время
[ago](#ago) | [dayofmonth](#dayofmonth) | [dayofweek](#dayofweek) |  [dayofyear](#dayofyear) |[datepart](#datepart) | [endofday](#endofday) | [endofmonth](#endofmonth) | [endofweek](#endofweek) | [endofyear](#endofyear) | [getmonth](#getmonth)|  [getyear](#getyear) | [now](#now) | [startofday](#startofday) | [startofmonth](#startofmonth) | [startofweek](#startofweek) | [startofyear](#startofyear) | [todatetime](#todatetime) | [totimespan](#totimespan) | [weekofyear](#weekofyear)

### <a name="date-and-time-literals"></a>Литералы даты и времени
|  |  |
| --- | --- |
| **datetime** | |
| `datetime("2015-12-31 23:59:59.9")`<br/>`datetime("2015-12-31")` |Время всегда выражается в формате UTC. Если дата не указана, то имеется в виду время для сегодняшнего дня. |
| `now()` |Текущее время. |
| `now(`-*timespan*`)` |`now()-`*timespan* |
| `ago(`*timespan*`)` |`now()-`*timespan* |
| **timespan** | |
| `2d` |2 дня |
| `1.5h` |1,5 часа |
| `30m` |30 минут |
| `10s` |10 с |
| `0.1s` |0,1 с |
| `100ms` |100 мс |
| `10microsecond` | |
| `1tick` |100 нс |
| `time("15 seconds")` | |
| `time("2")` |2 дня |
| `time("0.12:34:56.7")` |`0d+12h+34m+56.7s` |

### <a name="date-and-time-expressions"></a>Выражения для даты и времени
| Expression | Результат |
| --- | --- |
| `datetime("2015-01-02") - datetime("2015-01-01")` |`1d` |
| `datetime("2015-01-01") + 1d` |`datetime("2015-01-02")` |
| `datetime("2015-01-01") - 1d` |`datetime("2014-12-31")` |
| `2h * 24` |`2d` |
| `2d` / `2h` |`24` |
| `datetime("2015-04-15T22:33") % 1d` |`timespan("22:33")` |
| `bin(datetime("2015-04-15T22:33"), 1d)` |`datetime("2015-04-15T00:00")` |
|  | |
| `<` |Меньше |
| `<=` |Меньше или равно |
| `>` |Больше |
| `>=` |Больше или равно |
| `<>` |Не равно |
| `!=` |Не равно |

### <a name="ago"></a>ago
Вычитает заданный интервал времени из текущего времени UTC. Как и `now()`, эту функцию можно использовать в выражении несколько раз, и значение времени UTC будет каждый раз одинаковым.

**Синтаксис**

    ago(a_timespan)

**Аргументы**

* *a_timespan* — интервал для вычитания из текущего времени UTC (`now()`).

**Результаты**

    now() - a_timespan

**Пример**

Все строки за последний час:

```AIQL

    T | where timestamp > ago(1h)
```

### <a name="datepart"></a>datepart
    datepart("Day", datetime(2015-12-14)) == 14

Извлекает указанную часть даты как целое число.

**Синтаксис**

    datepart(part, datetime)

**Аргументы**

* `part:String` — {"Year", "Month", "Day", "Hour", "Minute", "Second", "Millisecond", "Microsecond", "Nanosecond"}.
* `datetime`

**Результаты**

Длинное число, представляющее указанную часть.

### <a name="dayofmonth"></a>dayofmonth
    dayofmonth(datetime("2016-05-15")) == 15 

Порядковый номер дня в месяце.

**Синтаксис**

    dayofmonth(a_date)

**Аргументы**

* `a_date` — значение в формате `datetime`.

### <a name="dayofweek"></a>dayofweek
    dayofweek(datetime("2015-12-14")) == 1d  // Monday

Целое число дней после предыдущего воскресенья в формате `timespan`.

**Синтаксис**

    dayofweek(a_date)

**Аргументы**

* `a_date` — значение в формате `datetime`.

**Результаты**

Интервал `timespan` от полуночи предыдущего воскресенья с округлением до целого числа (количество дней).

**Примеры**

```AIQL
dayofweek(1947-11-29 10:00:05)  // time(6.00:00:00), indicating Saturday
dayofweek(1970-05-11)           // time(1.00:00:00), indicating Monday
```

### <a name="dayofyear"></a>dayofyear
    dayofyear(datetime("2016-05-31")) == 152 
    dayofyear(datetime("2016-01-01")) == 1 

Порядковый номер дня в году.

**Синтаксис**

    dayofyear(a_date)

**Аргументы**

* `a_date` — значение в формате `datetime`.

<a name="endofday"></a><a name="endofweek"></a><a name="endofmonth"></a><a name="endofyear"></a>

### <a name="endofday-endofweek-endofmonth-endofyear"></a>endofday, endofweek, endofmonth, endofyear
    dt = datetime("2016-05-23 12:34")

    endofday(dt) == 2016-05-23T23:59:59.999
    endofweek(dt) == 2016-05-28T23:59:59.999 // Saturday
    endofmonth(dt) == 2016-05-31T23:59:59.999 
    endofyear(dt) == 2016-12-31T23:59:59.999 


### <a name="getmonth"></a>getmonth
Возвращает номер месяца (1-12) для аргумента типа datetime.

**Пример**

    ... | extend month = getmonth(datetime(2015-10-12))

    --> month == 10

### <a name="getyear"></a>getyear
Возвращает год для аргумента типа datetime.

**Пример**

    ... | extend year = getyear(datetime(2015-10-12))

    --> year == 2015

### <a name="now"></a>now
    now()
    now(-2d)

Текущее время UTC, при необходимости с учетом указанного смещения. Эта функция может использоваться в выражении несколько раз, и значение времени будет одинаковым для всех случаев.

**Синтаксис**

    now([offset])

**Аргументы**

* *offset* — смещение `timespan`, добавляемое к текущему времени UTC. По умолчанию: 0.

**Результаты**

Текущее время UTC в формате `datetime`.

    now() + offset

**Пример**

Определяет интервал с момента события, определенного предикатом.

```AIQL
T | where ... | extend Elapsed=now() - timestamp
```

<a name="startofday"></a><a name="startofweek"></a><a name="startofmonth"></a><a name="startofyear"></a>

### <a name="startofday-startofweek-startofmonth-startofyear"></a>startofday, startofweek, startofmonth, startofyear
    date=datetime("2016-05-23 12:34:56")

    startofday(date) == datetime("2016-05-23")
    startofweek(date) == datetime("2016-05-22") // Sunday
    startofmonth(date) == datetime("2016-05-01")
    startofyear(date) == datetime("2016-01-01")



### <a name="todatetime"></a>todatetime
Псевдоним `datetime()`.

     todatetime("2016-03-28")
     todatetime("03/28/2016")
     todatetime("2016-03-28 14:34:00")
     todatetime("03/28/2016 2:34pm")
     todatetime("2016-03-28T14:34.5Z")
     todatetime(a[0]) 
     todatetime(b.c) 

Проверьте, является ли строка допустимой датой:

     iff(notnull(todatetime(customDimensions.myDate)),
         ..., ...)


### <a name="totimespan"></a>totimespan
Псевдоним `timespan()`.

    totimespan("21d")
    totimespan("21h")
    totimespan(request.duration)

### <a name="weekofyear"></a>weekofyear
    weekofyear(datetime("2016-05-14")) == 21
    weekofyear(datetime("2016-01-03")) == 1
    weekofyear(datetime("2016-12-31")) == 53

Целочисленный результат представляет номер недели по стандарту ISO 8601. Первым днем недели является воскресенье, а первой неделей года — неделя, на которую приходится первый четверг года. (Таким образом, последние дни года могут включать несколько дней первой недели следующего года, а первые дни года — несколько дней 52-й или 53-й недели года.)

## <a name="string"></a>Строка
[countof](#countof) | [extract](#extract) | [extractjson](#extractjson)  | [isempty](#isempty) | [isnotempty](#isnotempty) | [notempty](#notempty) | [parseurl](#parseurl) | [replace](#replace) | [split](#split) | [strcat](#strcat) | [strlen](#strlen) | [substring](#substring) | [tolower](#tolower) | [tostring](#tostring) | [toupper](#toupper)

### <a name="string-literals"></a>Строковые литералы
Используются такие же правила, как в JavaScript.

Строки могут быть заключены в одинарные или двойные кавычки. 

Обратная косая черта (`\`) используется для экранирования знаков, включая `\t` (табуляция), `\n` (новая строка) и кавычки.

* `'this is a "string" literal in single \' quotes'`
* `"this is a 'string' literal in double \" quotes"`
* `@"C:\backslash\not\escaped\with @ prefix"`

### <a name="obfuscated-string-literals"></a>Скрываемые строковые литералы
Скрываемые строковые литералы — это строки, которые аналитика скрывает при выводе строки (например, при трассировке). Для этого все скрываемые знаки заменяются знаком звездочки (`*`).

Чтобы создать скрываемый строковый литерал, укажите перед строкой знак `h` или H. Например:

```
h'hello'
h@'world' 
h"hello"
```

### <a name="string-comparisons"></a>Сравнение строк
| operator | Описание | С учетом регистра | Пример |
| --- | --- | --- | --- |
| `==` |Равно |Да |`"aBc" == "aBc"` |
| `<>` `!=` |Не равно |Да |`"abc" <> "ABC"` |
| `=~` |Равно |Нет |`"abc" =~ "ABC"` <br/>`boolAsString =~ "true"` |
| `!~` |Не равно |Нет |`"aBc" !~ "xyz"` |
| `has` |Правая часть представляет собой все слово в левой части |Нет |`"North America" has "america"` |
| `!has` |Правая часть не является всем словом в левой части |Нет |`"North America" !has "amer"` |
| `hasprefix` |Правая часть является префиксом слова в левой части. |Нет |`"North America" hasprefix "ame"` |
| `!hasprefix` |Правая часть не является префиксом какого-либо слова в левой части. |Нет |`"North America" !hasprefix "mer"` |
| `hassuffix` |Правая часть является суффиксом слова в левой части. |Нет |`"North America" hassuffix "rth"` |
| `!hassuffix` |Правая часть не является суффиксом какого-либо слова в левой части. |Нет |`"North America" !hassuffix "mer"` |
| `contains` |Правая часть является подстрокой левой части. |Нет |`"FabriKam" contains "BRik"` |
| `!contains` |Правая часть не входит в левую часть |Нет |`"Fabrikam" !contains "xyz"` |
| `containscs` |Правая часть является подстрокой левой части. |Да |`"FabriKam" contains "Kam"` |
| `!containscs` |Правая часть не входит в левую часть |Да |`"Fabrikam" !contains "Kam"` |
| `startswith` |Правая часть является начальной подстрокой левой части. |Нет |`"Fabrikam" startswith "fab"` |
| `!startswith` |Правая часть не является начальной подстрокой левой части. |Нет |`"Fabrikam" !startswith "abr"` |
| `endswith` |Правая часть является конечной подстрокой левой части. |Нет |`"Fabrikam" endswith "kam"` |
| `!endswith` |Правая часть не является конечной подстрокой левой части. |Нет |`"Fabrikam" !endswith "ka"` |
| `matches regex` |Левая часть содержит соответствие для правой части |Да |`"Fabrikam" matches regex "b.*k"` |
| [`in`](#in) |Равно любому из элементов |Да |`"abc" in ("123", "345", "abc")` |
| [`!in`](#in) |Не равно ни одному из элементов |Да |`"bc" !in ("123", "345", "abc")` |

При проверке на наличие полной лексической единицы (т. е. знака или буквенно-цифрового слова, окруженного не буквенно-цифровыми знаками, в начале или в конце поля) используйте `has` или `in`. `has` выполняется быстрее, чем `contains`, `startswith` или `endswith`. Первый из этих запросов выполняется быстрее:

    EventLog | where continent has "North" | count;
    EventLog | where continent contains "nor" | count





### <a name="countof"></a>countof
    countof("The cat sat on the mat", "at") == 3
    countof("The cat sat on the mat", @"\b.at\b", "regex") == 3

Подсчитывает вхождения подстроки в строку. Совпадения для обычных строк могут перекрывать друг друга, для регулярных выражений — не могут.

**Синтаксис**

    countof(text, search [, kind])

**Аргументы**

* *text* — строка.
* *search* — текстовая строка или регулярное выражение, которое проверяется на вхождение в *text*.
* *kind* — `"normal"|"regex"`. Значение по умолчанию — `normal`. 

**Результаты**

Количество совпадений для строки поиска в контейнере. Совпадения для обычных строк могут перекрывать друг друга, для регулярных выражений — не могут.

**Примеры**

|  |  |
| --- | --- |
| `countof("aaa", "a")` |3 |
| `countof("aaaa", "aa")` |3 (не 2!) |
| `countof("ababa", "ab", "normal")` |2 |
| `countof("ababa", "aba")` |2 |
| `countof("ababa", "aba", "regex")` |1 |
| `countof("abcabc", "a.c", "regex")` |2 |

### <a name="extract"></a>extract
    extract("x=([0-9.]+)", 1, "hello x=45.6|wo") == "45.6"

Определяет соответствие [регулярного выражения](#regular-expressions) из текстовой строки. При необходимости извлеченная подстрока преобразуется к указанному типу.

**Синтаксис**

    extract(regex, captureGroup, text [, typeLiteral])

**Аргументы**

* *regex* — [регулярное выражение](#regular-expressions).
* *captureGroup* — положительная константа `int`, указывающая извлекаемую группу записи. 0 означает полное совпадение, 1 — значение, соответствующее первой '('круглой скобке')' в регулярном выражении, 2 или более — последующим круглым скобкам.
* *text* — `string` .
* *typeLiteral* — необязательный литерал типа (например, `typeof(long)`). Если указан, то извлеченная подстрока преобразуется в этот тип. 

**Результаты**

Если *regex* находит соответствие в *text*, то возвращается подстрока, которая сравнивалась с указанной в группе записи *captureGroup*, при необходимости преобразованная в тип *typeLiteral*.

Если соответствия нет или не удается выполнить преобразование типа, возвращается `null`. 

**Примеры**

В примере строки `Trace` производится поиск определения `Duration`. Соответствие преобразуется в `real`, а затем умножается на константу времени (`1s`), чтобы для `Duration` был задан тип `timespan`. В этом примере он равен 123,45 секунды:

```AIQL
...
| extend Trace="A=1, B=2, Duration=123.45, ..."
| extend Duration = extract("Duration=([0-9.]+)", 1, Trace, typeof(real)) * time(1s) 
```

Этот пример аналогичен `substring(Text, 2, 4)`.

```AIQL
extract("^.{2,2}(.{4,4})", 1, Text)
```

<a name="notempty"></a>
<a name="isnotempty"></a>
<a name="isempty"></a>



### <a name="isempty-isnotempty-notempty"></a>isempty, isnotempty, notempty
    isempty("") == true

True, если аргумент является пустой строкой или имеет значение null.
Ознакомьтесь также со сведениями о функции [isnull](#isnull).

**Синтаксис**

    isempty([value])


    isnotempty([value])


    notempty([value]) // alias of isnotempty

**Результаты**

Указывает, является ли аргумент пустой строкой или имеет значение null.

| x | isempty(x) |
| --- | --- |
| "" |Да |
| "x" |нет |
| parsejson("") |Да |
| parsejson("[]") |нет |
| parsejson("{}") |нет |

**Пример**

    T | where isempty(fieldName) | count


### <a name="parseurl"></a>parseurl
Разделение URL-адреса на части.

**Синтаксис**

    parseurl(urlstring)

**Аргументы**

* *urlstring:* URL-адрес.

**Результаты**

Объект, содержащий элементы в виде строк.

**Пример**

    parseurl("http://user:pass@contoso.com/icecream/buy.aspx?a=1&b=2#tag")

    {
    "Scheme" : "http",
    "Host" : "contoso.com",
    "Port" : "80",
    "Path" : "/icecream/buy.aspx",
    "Username" : "user",
    "Password" : "pass",
    "Query Parameters" : {"a":"1","b":"2"},
    "Fragment" : "tag"
    }

### <a name="replace"></a>replace
Заменяет все найденные совпадения регулярного выражения другой строкой.

**Синтаксис**

    replace(regex, rewrite, text)

**Аргументы**

* *regex* — [регулярное выражение](https://github.com/google/re2/wiki/Syntax) для поиска в *text*. Оно может содержать группы захвата в '('круглых скобках')'. 
* *rewrite* — регулярное выражение, результат которого заменит все соответствия, найденные *matchingRegex*. Используйте `\0` для указания полного соответствия, `\1` для первой группы записи, `\2` и так далее — для следующих групп записи.
* *text* — строка.

**Результаты**

*text* после замены всех вхождений *regex* результатами вычислений *rewrite*. Совпадения не перекрываются.

**Пример**

Выражение:

```AIQL
range x from 1 to 5 step 1
| extend str=strcat('Number is ', tostring(x))
| extend replaced=replace(@'is (\d+)', @'was: \1', str)
```

Получены следующие результаты:

| x | str | заменена |
| --- | --- | --- |
| 1 |Число равно: 1,000000 |Число было равно: 1,000000 |
| 2 |Число равно: 2,000000 |Число было равно: 2,000000 |
| 3 |Число равно: 3,000000 |Число было равно: 3,000000 |
| 4. |Число равно: 4,000000 |Число было равно: 4,000000 |
| 5 |Число равно: 5,000000 |Число было равно: 5,000000 |

### <a name="split"></a>split
    split("aaa_bbb_ccc", "_") == ["aaa","bbb","ccc"]

Разбивает указанную строку в соответствии с заданным разделителем и возвращает массив строк, содержащий подстроки. При необходимости может быть возвращена конкретная подстрока, если она существует.

**Синтаксис**

    split(source, delimiter [, requestedIndex])

**Аргументы**

* *source*— исходная строка, которая будет разделена в соответствии с указанным разделителем.
* *delimiter*— разделитель, который будет использоваться для разделения исходной строки.
* *requestedIndex* — необязательный индекс типа `int`, начинающийся с нуля. Если указан, возвращаемый массив строк будет содержать запрошенную подстроку, если она существует. 

**Результаты**

Массив строк, содержащий подстроки заданной исходной строки, разделенные указанным разделителем.

**Примеры**

```
split("aa_bb", "_")           // ["aa","bb"]
split("aaa_bbb_ccc", "_", 1)  // ["bbb"]
split("", "_")                // [""]
split("a__b")                 // ["a","","b"]
split("aabbcc", "bb")         // ["aa","cc"]
```




### <a name="strcat"></a>strcat
    strcat("hello", " ", "world")

Объединяет от 1 до 16 строк, переданных в качестве аргументов.

### <a name="strlen"></a>strlen
    strlen("hello") == 5

Длина строки.

### <a name="substring"></a>substring
    substring("abcdefg", 1, 2) == "bc"

Извлекает подстроку из заданной исходной строки, начиная с указанного индекса. При необходимости можно указать длину запрашиваемой подстроки.

**Синтаксис**

    substring(source, startingIndex [, length])

**Аргументы**

* *source* — исходная строка, из которой будет извлечена подстрока.
* *startingIndex* — отсчитываемая от нуля позиция первого знака запрашиваемой подстроки.
* *length* — необязательный параметр, который указывает требуемое количество знаков в подстроке. 

**Результаты**

Подстрока для указанной строки. Подстрока начинается с позиции startingIndex (отсчитывается начиная с нуля) и продолжается до конца строки или до требуемого количества символов, если оно указано.

**Примеры**

```
substring("123456", 1)        // 23456
substring("123456", 2, 2)     // 34
substring("ABCD", 0, 2)       // AB
```

### <a name="tolower"></a>tolower
    tolower("HELLO") == "hello"

Преобразует строку в нижний регистр.

### <a name="toupper"></a>toupper
    toupper("hello") == "HELLO"

Преобразует строку в верхний регистр.

### <a name="guids"></a>Идентификаторы GUID
    guid(00000000-1111-2222-3333-055567f333de)


## <a name="arrays-objects-and-dynamic"></a>Массивы, объекты и динамические типы
[Литералы](#dynamic-literals) | [Приведения](#casting-dynamic-objects) | [Операторы](#operators) | [Предложения let](#dynamic-objects-in-let-clauses)
<br/>
[arraylength](#arraylength) | [extractjson](#extractjson) | [parsejson](#parsejson) | [range](#range) | [treepath](#treepath) | [todynamic](#todynamic) | [zip](#zip)

Ниже приведен результат запроса на исключение Application Insights. Значение `details` является массивом.

![](./media/app-insights-analytics-reference/310.png)

**Индексация** — массивы и объекты, которые можно индексировать так же, как в JavaScript.

    exceptions | take 1
    | extend 
        line = details[0].parsedStack[0].line,
        stackdepth = arraylength(details[0].parsedStack)

* Но используйте `arraylength` и другие функции аналитики (не ".length"!).

**Приведение типов** В некоторых случаях бывает необходимо привести тип элемента, который можно извлечь из объекта, поскольку его тип может варьироваться. Например, `summarize...to` необходим конкретный тип:

    exceptions 
    | summarize count() 
      by toint(details[0].parsedStack[0].line)

    exceptions
    | summarize count()
      by tostring(details[0].parsedStack[0].assembly)

**Литералы** Используются для создания явного массива или объекта контейнера свойств, его записи в виде строки JSON и приведения типов:

    todynamic('[{"x":"1", "y":"32"}, {"x":"6", "y":"44"}]')


**mvexpand:** чтобы извлечь свойства объекта в отдельные строки, используйте mvexpand:

    exceptions | take 1
    | mvexpand details[0].parsedStack[0]


![](./media/app-insights-analytics-reference/410.png)

**treepath** используется для поиска всех путей в сложном объекте.

    exceptions | take 1 | project timestamp, details
    | extend path = treepath(details)
    | mvexpand path


![](./media/app-insights-analytics-reference/420.png)

**buildschema** применяется для поиска минимальной схемы, которая учитывает все значения выражения в таблице.

    exceptions | summarize buildschema(details)

Результат:

    { "indexer":
     {"id":"string",
       "parsedStack":
       { "indexer":
         {  "level":"int",
            "assembly":"string",
            "fileName":"string",
            "method":"string",
            "line":"int"
         }},
      "outerId":"string",
      "message":"string",
      "type":"string",
      "rawStack":"string"
    }}

Обратите внимание, что `indexer` используется для обозначения тех случаев, когда следует применить числовой индекс. Некоторые допустимые пути для этой схемы будут следующими (предполагается, что эти индексы в этом примере находятся в диапазоне):

    details[0].parsedStack[2].level
    details[0].message
    arraylength(details)
    arraylength(details[0].parsedStack)



### <a name="array-and-object-literals"></a>Литералы массива и объекта
Чтобы создать динамический литерал, используйте `parsejson` (псевдоним `todynamic`) со строкой JSON в качестве аргумента:

* `parsejson('[43, 21, 65]')` — массив чисел;
* `parsejson('{"name":"Alan", "age":21, "address":{"street":432,"postcode":"JLK32P"}}')`
* `parsejson('21')` — одно значение динамического типа, содержащее число;
* `parsejson('"21"')` — одно значение динамического типа, содержащее строку.

Обратите внимание, что, в отличие от JavaScript, в JSON необходимо заключать строки в двойные кавычки (`"`). Поэтому обычно проще заключать закодированные JSON литералы в одинарные кавычки (`'`).

В этом примере создается динамическое значение, а затем используются его поля:

```

T
| extend person = parsejson('{"name":"Alan", "age":21, "address":{"street":432,"postcode":"JLK32P"}}')
| extend n = person.name, add = person.address.street
```


### <a name="dynamic-object-functions"></a>Функции динамического объекта
|  |  |
| --- | --- |
| [*значение* `in` *массив*](#in) |*Массив* содержит *значение*. |
| [*значение* `!in` *массив*](#in) |*Массив* не содержит *значение*. |
| [`arraylength(`array`)`](#arraylength) |Возвращает NULL, если это не массив |
| [`extractjson(`путь,объект`)`](#extractjson) |Использует путь path для перехода к объекту object. |
| [`parsejson(`source`)`](#parsejson) |Преобразует строку формата JSON в динамический объект. |
| [`range(`начало,конец,шаг`)`](#range) |Массив значений |
| [`mvexpand` listColumn](#mvexpand-operator) |Реплицирует строку для каждого значения в списке в указанной ячейке. |
| [`summarize buildschema(`столбец`)`](#buildschema) |Определяет схему типа по содержимому столбца. |
| [`summarize makelist(`столбец`)` ](#makelist) |Выполняет сведение групп строк и помещает значения столбца в массив. |
| [`summarize makeset(`столбец`)`](#makeset) |Выполняет сведение групп строк и помещает значения столбца в массив без дублирования значений. |

### <a name="dynamic-objects-in-let-clauses"></a>Динамические объекты в предложениях let
В [предложениях let](#let-clause) динамические значения хранятся в виде строк, поэтому оба предложения эквивалентны и требуют применения `parsejson` (или `todynamic`) перед использованием.

    let list1 = '{"a" : "somevalue"}';
    let list2 = parsejson('{"a" : "somevalue"}');

    T | project parsejson(list1).a, parsejson(list2).a


### <a name="in"></a>больше
    value in (listExpression)
    value !in (listExpression)

Определяет, имеется ли в списке элемент, равный значению. Регистр знаков учитывается, если это значение — строка.

**Аргументы**

* `value`: скалярное выражение.
* `listExpression`...: список скалярных выражений или выражение, которое возвращает список. 

Вложенный массив сведен в один список (например, `where x in (dynamic([1,[2,3]]))` становится `where x in (1,2,3)`).  

**Примеры**

```AIQL
    requests | where client_City in ("London", "Paris", "Rome")
```

```AIQL
let cities = dynamic(['Dublin','Redmond','Amsterdam']);
requests | where client_City in (cities) 
|  summarize count() by client_City
```

Вычисленный список:

```AIQL
let topCities =  toscalar ( // convert single column to value
   requests
   | summarize count() by client_City 
   | top 4 by count_ 
   | summarize makeset(client_City)) ;
requests
| where client_City in (topCities) 
| summarize count() by client_City;
```

Использование вызова функции как выражения списка:

```AIQL
let topCities =  (n:int) {toscalar (
   requests
   | summarize count() by client_City 
   | top n by count_ 
   | summarize makeset(client_City)) };
requests
| where client_City in (topCities(3)) 
| summarize count() by client_City;
```
 

### <a name="arraylength"></a>arraylength
Количество элементов в динамическом массиве.

**Синтаксис**

    arraylength(array)

**Аргументы**

* *array* — значение типа `dynamic`.

**Результаты**

Количество элементов в массиве *array* или `null`, если *array* не является массивом.

**Примеры**

```
arraylength(parsejson('[1, 2, 3, "four"]')) == 4
arraylength(parsejson('[8]')) == 1
arraylength(parsejson('[{}]')) == 1
arraylength(parsejson('[]')) == 0
arraylength(parsejson('{}')) == null
arraylength(parsejson('21')) == null
```



### <a name="extractjson"></a>extractjson
    extractjson("$.hosts[1].AvailableMB", EventText, typeof(int))

Возвращает указанный элемент текста JSON с помощью выражения пути. При необходимости преобразует извлеченную строку к указанному типу.

**Синтаксис**

```

    string extractjson(jsonPath, dataSource) 
    resulttype extractjson(jsonPath, dataSource, typeof(resulttype))
```


**Результаты**

Эта функция выполняет запрос JsonPath к источнику данных dataSource, который содержит допустимую строку JSON. При необходимости это значение преобразуется в другой тип, в зависимости от третьего аргумента.

**Пример**

Обозначения с [квадратными скобками] и точками эквивалентны:

    ... | extend AvailableMB = extractjson("$.hosts[1].AvailableMB", EventText, typeof(int)) | ...

    ... | extend AvailableMD = extractjson("$['hosts'][1]['AvailableMB']", EventText, typeof(int)) | ...



**Советы по улучшению производительности**

* Применяйте предложения where перед использованием `extractjson()`
* По возможности предпочтительнее использовать сопоставление с регулярным выражением и [extract](#extract) . Он выполняется гораздо быстрее и эффективнее, если JSON создается из шаблона.
* Используйте `parsejson()` , если требуется извлечь из JSON несколько значений.
* При возможности проведите анализ JSON при обработке, объявив динамический тип столбца.

### <a name="json-path-expressions"></a>Выражения пути JSON
|  |  |
| --- | --- |
| `$` |Корневой объект |
| `@` |Текущий объект |
| `[0]` |Индекс массива |
| `.` или `[0]` |Дочерний |

*(В настоящее время подстановочные знаки, рекурсии, объединения и срезы не реализованы.)*

### <a name="parsejson"></a>parsejson
Интерпретирует `string` как [значение JSON](http://json.org/) и возвращает значение типа `dynamic`. Этот метод предпочтительнее, чем `extractjson()` , если необходимо извлечь более одного элемента из составного объекта JSON.

**Синтаксис**

    parsejson(json)

**Аргументы**

* *json* — документ JSON.

**Результаты**

Объект типа `dynamic` , определенный в *json*.

**Пример**

В следующем примере `context_custom_metrics` представляет собой `string`, который выглядит следующим образом. 

```
{"duration":{"value":118.0,"count":5.0,"min":100.0,"max":150.0,"stdDev":0.0,"sampledValue":118.0,"sum":118.0}}
```

Тогда следующий фрагмент извлечет значение слота `duration` из объекта, а затем из него извлечет два слота `duration.value` и  `duration.min` (`118.0` и `110.0`, соответственно).

```AIQL
T
| ...
| extend d=parsejson(context_custom_metrics) 
| extend duration_value=d.duration.value, duration_min=d["duration"]["min"]
```



### <a name="range"></a>range
Функция `range()` (не путайте с оператором `range`) создает динамический массив, содержащий последовательность равномерно распределенных значений.

**Синтаксис**

    range(start, stop, step)

**Аргументы**

* *start* — значение первого элемента результирующего массива. 
* *stop* — значение последнего элемента результирующего массива или наименьшее значение, которое больше последнего элемента результирующего массива и находится в определенном количестве (целое число) шагов *step* от первого элемента *start*.
* *step* — разница между двумя последовательными элементами массива.

**Примеры**

В следующем примере возвращается `[1, 4, 7]`.

```AIQL
range(1, 8, 3)
```

Следующий пример возвращает массив, содержащий все дни 2015 года:

```AIQL

    range(datetime(2015-01-01), datetime(2015-12-31), 1d)
```

### <a name="todynamic"></a>todynamic
    todynamic('{"a":"a1", "b":["b1", "b2"]}')

Преобразует строку в динамическое значение.

### <a name="treepath"></a>treepath
    treepath(dynamic_object)

Перечисляет все выражения пути, которые определяют листья в динамическом объекте. 

**Результаты**

Массив выражений пути.

**Примеры**

    treepath(parsejson('{"a":"b", "c":123}')) 
    =>       ["['a']","['c']"]
    treepath(parsejson('{"prop1":[1,2,3,4], "prop2":"value2"}'))
    =>       ["['prop1']","['prop1'][0]","['prop2']"]
    treepath(parsejson('{"listProperty":[100,200,300,"abcde",{"x":"y"}]}'))
    =>       ["['listProperty']","['listProperty'][0]","['listProperty'][0]['x']"]

Обратите внимание, что "[0]" определяет наличие массива, но не обозначает индекс, используемый указанным расположением.

### <a name="zip"></a>zip
    zip(list1, list2, ...)

Объединяет набор списков в один список кортежей.

* `list1...`: список значений.

**Примеры**

    zip(parsejson('[1,3,5]'), parsejson('[2,4,6]'))
    => [ [1,2], [3,4], [5,6] ]


    zip(parsejson('[1,3,5]'), parsejson('[2,4]'))
    => [ [1,2], [3,4], [5,null] ]


### <a name="names"></a>именах
Имя может содержать до 1024 знаков. В нем учитывается регистр и допускаются буквы, цифры и знаки подчеркивания (`_`). 

Чтобы включить в имя другие символы или использовать в нем ключевое слово, заключите имя в кавычки ['...'] или ["..."]. Например:

```AIQL

    requests | 
    summarize  ["distinct urls"] = dcount(name) // non-alphanumerics
    by  ['where'] = client_City, // using a keyword as a name
        ['outcome!'] = success // non-alphanumerics
```


|  |  |
| --- | --- |
| ['путь\\файл\n\'x\''] |Используйте \ для экранирования символов |
| ["d-e.=/f#\n"] | |
| [@'path\file'] |Экранирования нет: \ является литералом. |
| [@"\now & then\"] | |
| [where] |Использование ключевого слова языка в качестве имени. |

[!INCLUDE [app-insights-analytics-footer](../../includes/app-insights-analytics-footer.md)]



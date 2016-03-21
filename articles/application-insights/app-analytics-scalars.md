<properties 
	pageTitle="Скалярные выражения в аналитике Application Insights" 
	description="Числа, строки, динамические выражения и типы в аналитике Application Insights, мощном средстве поиска для Application Insights." 
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
	ms.date="03/05/2016" 
	ms.author="awills"/>


 
# Скалярные выражения в аналитике Application Insights


[Аналитика Application Insights](app-analytics.md) — это мощная поисковая система для вашей телеметрии [Application Insights](app-insights-overview.md). На этих страницах описан AIQL, язык запросов аналитики Application Insights.

[AZURE.INCLUDE [app-analytics-top-index](../../includes/app-analytics-top-index.md)]

---

[ago](#ago) | [arraylength](#arraylength) | [bin](#bin) [countof](#countof) | [dayofweek](#dayofweek) | [extract](#extract) | [extractjson](#extractjson) | [floor](#floor) <br/>[getmonth](#getmonth) | [gettype](#gettype) [getyear](#getyear) | [hash](#hash) | [iff](#iff) | [isempty](#isempty) | [isnotempty](#isnotempty) | [isnull](#isnull) | [isnotnull](#isnotnull) <br/> [now](#now) | [notempty](#notempty) | [notnull](#notnull) | [parsejson](#parsejson)| [rand](#rand) | [range](#range) | [replace](#replace) | [split](#split) | [sqrt](#sqrt) <br/>[startofmonth](#startofmonth) | [startofyear](#startofyear) | [strcat](#strcat) | [strlen](#strlen) | [substring](#substring) | [tolower](#tolower) | [toupper](#toupper) | [treepath](#treepath)

---



Термин "скалярный" означает такие значения, как числовые или строковые, которые могут занимать одну ячейку в таблице AIQL. Скалярные выражения строятся из скалярных функций и операторов и возвращают скалярное значение. `sqrt(score)/100 > target+2` — скалярное выражение.

К "скалярным" также относятся массивы и составные объекты, которые также могут храниться в одной ячейке базы данных.

Скалярные выражения отличаются от [запросов](app-analytics-queries.md), результаты которых представляют собой таблицы.

## Скалярные значения

[casts](#casts) | [comparisons](#scalar-comparisons) <br/> [gettype](#gettype) | [hash](#hash) | [iff](#iff)| [isnull](#isnull) | [isnotnull](#isnotnull) | [notnull](#notnull)

Ниже приведены поддерживаемые типы.

| Тип | Дополнительные имена | Эквивалентный тип .NET |
| --------- | -------------------- | -------------------- |
| `bool` | `boolean` | `System.Boolean` |
| `datetime`| `date` | `System.DateTime` |
| `dynamic` | | `System.Object` |
| `guid` | `uuid`, `uniqueid` | `System.Guid` |
| `int` | | `System.Int32` |
| `long` | | `System.Int64` |
| `double` | `real` | `System.Double` |
| `string` | | `System.String` |
| `timespan`| `time` | `System.TimeSpan` |

### Приведение типов

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

### Скалярные сравнения

||
---|---
`<` |Меньше
`<=`|Меньше или равно
`>` |Больше
`>=`|Больше или равно
`<>`|Не равно
`!=`|Не равно 
`in`| Правый операнд является массивом (динамическим), а левый операнд равен одному из его элементов.
`!in`| Правый операнд является массивом (динамическим), а левый операнд не равен ни одному из его элементов.




### gettype

**Возвращает**

Строка, представляющая базовый тип хранилища для единственного аргумента. Это особенно удобно для значений типа `dynamic`: в этом случае `gettype()` покажет, как закодировано значение.

**Примеры**

|||
---|---
`gettype("a")` |`"string" `
`gettype(111)` |`"long" `
`gettype(1==1)` |`"int8" (*) `
`gettype(now())` |`"datetime" `
`gettype(1s)` |`"timespan" `
`gettype(parsejson('1'))` |`"int" `
`gettype(parsejson(' "abc" '))` |`"string" `
`gettype(parsejson(' {"abc":1} '))` |`"dictionary"` 
`gettype(parsejson(' [1, 2, 3] '))` |`"array"` 
`gettype(123.45)` |`"real" `
`gettype(guid(12e8b78d-55b4-46ae-b068-26d7a0080254))` |`"guid"` 
`gettype(parsejson(''))` |`"null"`



### hash

**Синтаксис**

    hash(source [, mod])

**Аргументы**

* *source*: исходный скаляр, для которого вычисляется хэш.
* *mod*: значение модуля (остатка от деления), которое будет применено к результирующему хэшу.

**Возвращает**

Значение xxhash (тип long) для заданного скаляра, указанное значение модуля (если указано).

**Примеры**

```
hash("World")                   // 1846988464401551951
hash("World", 100)              // 51 (1846988464401551951 % 100)
hash(datetime("2015-01-01"))    // 1380966698541616202
```
### iff

Функция `iff()` вычисляет первый аргумент (предикат) и возвращает значение второго или третьего аргументов, в зависимости от того, является ли предикат `true` или `false`. Второй и третий аргументы должны быть одного типа.

**Синтаксис**

    iff(predicate, ifTrue, ifFalse)


**Аргументы**

* *predicate:* выражение, результатом которого является значение `boolean`.
* *ifTrue:* выражение, которое вычисляется и значение которого возвращается из функции, если *predicate* равен `true`.
* *ifFalse:* выражение, которое вычисляется и значение которого возвращается из функции, если *predicate* равен `false`.

**Возвращает**

Эта функция возвращает значение *ifTrue*, если *predicate* равен `true`, или значение *ifFalse* в противном случае.

**Пример**

```
iff(floor(timestamp, 1d)==floor(now(), 1d), "today", "anotherday")
```

<a name="isnull"/></a> <a name="isnotnull"/></a> <a name="notnull"/></a>
### isnull, isnotnull, notnull

    isnull(parsejson("")) == true

Принимает один аргумент и сообщает, равен ли он null.

**Синтаксис**


    isnull([value])


    isnotnull([value])


    notnull([value])  // alias for isnotnull

**Возвращает**

Значение true или false в зависимости от того, равен ли аргумент null.


|x|isnull(x)
|---|---
| "" | нет
|"x" | нет
|parsejson("")|Да
|parsejson("")|нет
|parsejson("{}")|нет

**Пример**

    T | where isnotnull(PossiblyNull) | count

Обратите внимание, что того же эффекта можно добиться другими способами:

    T | summarize count(PossiblyNull)




## Логический 

### Логические литералы

	true == 1
    false == 0
    gettype(true) == "int8"
    typeof(bool) == typeof(int8)

### Логические операторы

	and 
    or 

    

## Числа

[bin](#bin) | [floor](#floor) | [rand](#rand) | [range](#range) | [sqrt](#sqrt) | [todouble](#todouble) | [toint](#toint) | [tolong](#tolong)

### Числовые литералы

|||
|---|---
|`42`|`long`
|`42.0`|`real`

### Арифметические операторы

|| |
|---|-------------|
| + | Добавить |
| – | Вычитание || * | Умножение || / | Деление || % | Остаток от деления | || |`<` | Меньше |`<=`| Меньше или равно |`>` | Больше |`>=`| Больше или равно |`<>`| Не равно |`!=`| Не равно




### bin

Округляет значения до целого, кратного указанному размеру группы. Часто используется в запросе [`summarize by`](app-analytics-queries.md#summarize-operator). При наличии разрозненного набора значений они будут сгруппированы в меньший набор определенных значений.

Псевдоним `floor`.

**Синтаксис**

     bin(value, roundTo)

**Аргументы**

* *value:* число, дата или интервал времени. 
* *roundTo:* "размер группы". Число, дата или интервал времени, на который делится *value*. 

**Возвращает**

Ближайшее число, кратное *roundTo* и меньшее *value*.
 
    (toint((value/roundTo)-0.5)) * roundTo

**Примеры**

Выражение | Результат
---|---
`bin(4.5, 1)` | `4.0`
`bin(time(16d), 7d)` | `14d`
`bin(datetime(1953-04-15 22:25:07), 1d)`| `datetime(1953-04-15)`


Следующее выражение вычисляет гистограмму длительности с размером сегмента в 1 секунду:

```AIQL

    T | summarize Hits=count() by bin(Duration, 1s)
```

### floor

Псевдоним для [`bin()`](#bin).


### rand

Генератор случайных чисел.

* `rand()` — вещественное число от 0.0 до 1.0.
* `rand(n)` — целое число от 0 до n-1.




### sqrt

Функция квадратного корня.

**Синтаксис**

    sqrt(x)

**Аргументы**

* *x:* вещественное число > = 0.

**Возвращает**

* Положительное число, такое как `sqrt(x) * sqrt(x) == x`
* `null` если аргумент является отрицательным числом или не может быть преобразован в значение `real`. 




### toint

    toint(100)        // cast from long
    toint(20.7) == 21 // nearest int from double
    toint(20.4) == 20 // nearest int from double
    toint("  123  ")  // parse string
    toint(a[0])       // cast from dynamic
    toint(b.c)        // cast from dynamic

### tolong

    tolong(20.7) == 21 // conversion from double
    tolong(20.4) == 20 // conversion from double
    tolong("  123  ")  // parse string
    tolong(a[0])       // cast from dynamic
    tolong(b.c)        // cast from dynamic


### todouble

    todouble(20) == 20.0 // conversion from long or int
    todouble(" 12.34 ")  // parse string
    todouble(a[0])       // cast from dynamic
    todouble(b.c)        // cast from dynamic



## Дата и время


[ago](#ago) | [dayofweek](#dayofweek) | [getmonth](#getmonth)| [getyear](#getyear) | [now](#now) | [startofmonth](#startofmonth) | [startofyear](#startofyear) | [todatetime](#todatetime) | [totimespan](#totimespan)

### Литералы даты и времени

|||
---|---
**datetime**|
`datetime("2015-12-31 23:59:59.9")`<br/>`datetime("2015-12-31")`|Время всегда выражается в формате UTC. Если дата не указана, то имеется в виду время для сегодняшнего дня.
`now()`|Текущее время.
`now(`-*timespan*`)`|`now()-`*timespan*
`ago(`*timespan*`)`|`now()-`*timespan*
**интервал времени**|
`2d`|2 дня
`1.5h`|1,5 часа 
`30m`|30 минут
`10s`|10 с
`0.1s`|0,1 с
`100ms`| 100 мс
`10microsecond`|
`1tick`|100 нс
`time("15 seconds")`|
`time("2")`| 2 дня
`time("0.12:34:56.7")`|`0d+12h+34m+56.7s`

### Выражения для даты и времени

Выражение |Результат
---|---
`datetime("2015-01-02") - datetime("2015-01-01")`| `1d`
`datetime("2015-01-01") + 1d`| `datetime("2015-01-02")`
`datetime("2015-01-01") - 1d`| `datetime("2014-12-31")`
`2h * 24` | `2d`
`2d` / `2h` | `24`
`datetime("2015-04-15T22:33") % 1d` | `timespan("22:33")`
`bin(datetime("2015-04-15T22:33"), 1d)` | `datetime("2015-04-15T00:00")`
||
`<` |Меньше
`<=`|Меньше или равно
`>` |Больше
`>=`|Больше или равно
`<>`|Не равно
`!=`|Не равно 




### ago

Вычитает заданный интервал времени из текущего времени UTC. Как и `now()`, эта функция может использоваться в выражении несколько раз, и значение времени UTC будет одинаковым для всех случаев.

**Синтаксис**

    ago(a_timespan)

**Аргументы**

* *a\_timespan*: интервал для вычитания из текущего времени UTC (`now()`).

**Возвращает**

    now() - a_timespan

**Пример**

Все строки за последний час:

```AIQL

    T | where timestamp > ago(1h)
```



### dayofweek

    dayofweek(datetime("2015-12-14")) == 1d  // Monday

Целое число дней с предыдущего воскресенья в виде `timespan`.

**Синтаксис**

    dayofweek(a_date)

**Аргументы**

* `a_date`: A `datetime`.

**Возвращает**

`timespan` с момента полуночи в начале предыдущего воскресенья, округленное до целого числа дней.

**Примеры**

```AIQL
dayofweek(1947-11-29 10:00:05)  // time(6.00:00:00), indicating Saturday
dayofweek(1970-05-11)           // time(1.00:00:00), indicating Monday
```

### getmonth

Возвращает номер месяца (1-12) для аргумента типа datetime.

**Пример**

    ... | extend month = getmonth(datetime(2015-10-12))

    --> month == 10

### getyear

Возвращает год для аргумента типа datetime.

**Пример**

    ... | extend year = getyear(datetime(2015-10-12))

    --> year == 2015

### now

    now()
    now(-2d)

Текущее время UTC, при необходимости с учетом указанного смещения. Эта функция может использоваться в выражении несколько раз, и значение времени будет одинаковым для всех случаев.

**Синтаксис**

    now([offset])

**Аргументы**

* *offset:* смещение `timespan`, добавляемое к текущему времени UTC. По умолчанию: 0.

**Возвращает**

Текущее время UTC в виде `datetime`.

    now() + offset

**Пример**

Определяет интервал с момента события, определенного предикатом.

```AIQL
T | where ... | extend Elapsed=now() - timestamp
```

### startofmonth

    startofmonth(date)

Начало месяца, содержащего дату.

### startofyear

    startofyear(date)

Начало года, содержащего дату.


### todatetime

Псевдоним `datetime()`.

     todatetime("2016-03-28")
     todatetime("03/28/2016")
     todatetime("2016-03-28 14:34")
     todatetime("03/28/2016 2:34pm")
     todatetime("2016-03-28T14:34.5Z")
     todatetime(a[0])  // cast a dynamic type
     todatetime(b.c)   // cast a dynamic type

### totimespan

Псевдоним `timespan()`.

    totimespan("21d")
    totimespan("21h")
    totimespan(request.duration)


## Строка

[countof](#countof) | [extract](#extract) | [extractjson](#extractjson) | [isempty](#isempty) | [isnotempty](#isnotempty) | [notempty](#notempty) | [replace](#replace) | [split](#split) | [strcat](#strcat) | [strlen](#strlen) | [substring](#substring) | [tolower](#tolower) | [tostring](#tostring) | [toupper](#toupper)


### Строковые литералы

Используются такие же правила, как в JavaScript.

Строки могут быть заключены в одинарные или двойные кавычки.

Обратная косая черта (``) используется для экранирования символов, таких как `\t` (табуляция), `\n` (новая строка) и кавычек.

* `'this is a "string" literal in single \' quotes'`
* `"this is a 'string' literal in double " quotes"`
* `@"C:\backslash\not\escaped\with @ prefix"`

### Скрываемые строковые литералы

Скрываемые строковые литералы — это строки, которые аналитика Application Insights скрывает при выводе строки (например при трассировке). Для этого все скрываемые символы заменяются начальным символом (`*`).

Для образования скрываемого строкового литерала укажите символ `h` или "H". Например:

```
h'hello'
h@'world' 
h"hello"
```

### Сравнение строк

Оператор|Описание|С учетом регистра|Пример
---|---|---|---
`==`|Равно |Да| `"aBc" == "aBc"`
`<>`|Не равно|Да| `"abc" <> "ABC"`
`=~`|Равно |Нет| `"abc" =~ "ABC"`
`!~`|Не равно |Нет| `"aBc" !~ "xyz"`
`has`|Правая часть представляет собой все слово в левой части|Нет| `"North America" has "america"`
`!has`|Правая часть не является всем словом в левой части|Нет|`"North America" !has "amer"` 
`contains` | Правая часть является вхождением в левую часть|Нет| `"FabriKam" contains "BRik"`
`!contains`| Правая часть не входит в левую часть|Нет| `"Fabrikam" !contains "xyz"`
`containscs` | Правая часть является вхождением в левую часть|Да| `"FabriKam" contains "Kam"`
`!containscs`| Правая часть не входит в левую часть|Да| `"Fabrikam" !contains "Kam"`
`startswith`|Правая часть является начальным вхождением в левую часть|Нет|`"Fabrikam" startswith "fab"`
`matches regex`|Левая часть содержит соответствие для правой части|Да| `"Fabrikam" matches regex "b.*k"`


При проверке на наличие полного лексического слова (т. е. символа или буквенно-цифрового слова, окруженного не буквенно-цифровыми символами, не началом и не концом поля) используйте `has` или `in`. `has` выполняется быстрее, чем `contains` или `startswith`. Первый из этих запросов выполняется быстрее:

    EventLog | where continent has "North" | count;
	EventLog | where continent contains "nor" | count





### countof

    countof("The cat sat on the mat", "at") == 3
    countof("The cat sat on the mat", @"\b.at\b", "regex") == 3

Подсчитывает вхождения подстроки в строку. Совпадения для обычных строк могут перекрывать друг друга, для регулярных выражений — не могут.

**Синтаксис**

    countof(text, search [, kind])

**Аргументы**

* *text:* строка.
* *search:* текстовая строка или [регулярное выражение](app-analytics-reference.md#regular-expressions), которое проверяется на вхождение в *text*.
* *kind:* `"normal"|"regex"` по умолчанию `normal`. 

**Возвращает**

Количество совпадений для строки поиска в контейнере. Совпадения для обычных строк могут перекрывать друг друга, для регулярных выражений — не могут.

**Примеры**

|||
|---|---
|`countof("aaa", "a")`| 3 
|`countof("aaaa", "aa")`| 3 (не 2!)
|`countof("ababa", "ab", "normal")`| 2
|`countof("ababa", "aba")`| 2
|`countof("ababa", "aba", "regex")`| 1
|`countof("abcabc", "a.c", "regex")`| 2
    



### extract

    extract("x=([0-9.]+)", 1, "hello x=45.6|wo") == "45.6"

Определяет совпадение для [регулярного выражения](app-analytics-reference.md#regular-expressions) из текстовой строки. При необходимости извлеченная подстрока преобразуется к указанному типу.

**Синтаксис**

    extract(regex, captureGroup, text [, typeLiteral])

**Аргументы**

* *regex:* [регулярное выражение](app-analytics-reference.md#regular-expressions).
* *captureGroup:* положительная константа `int`, указывающая группу захвата для извлечения. 0 означает полное совпадение, 1 — значение, соответствующее первой '('круглой скобке')' в регулярном выражении, 2 или более — последующим круглым скобкам.
* *text:* `string` для поиска.
* *typeLiteral:* необязательный литерал типа (например `typeof(long)`). Если указан, то извлеченная подстрока преобразуется в этот тип. 

**Возвращает**

Если *regex* находит совпадение в *text*, то возвращается подстрока, которая сравнивалась с указанной группой захвата *captureGroup*, при необходимости преобразованная в тип *typeLiteral*.

Если совпадения нет или не удается выполнить преобразование типа, возвращается `null`.

**Примеры**

В примере строки `Trace` производится поиск определения `Duration`. Совпадение преобразуется в `real`, а затем умножается на константу времени (`1s`), так чтобы `Duration` получил тип `timespan`. В этом примере он равен 123,45 секунды:

```AIQL
...
| extend Trace="A=1, B=2, Duration=123.45, ..."
| extend Duration = extract("Duration=([0-9.]+)", 1, Trace, typeof(real)) * time(1s) 
```

Этот пример аналогичен `substring(Text, 2, 4)`:

```AIQL
extract("^.{2,2}(.{4,4})", 1, Text)
```

<a name="notempty"></a> <a name="isnotempty"></a> <a name="isempty"></a>
### isempty, isnotempty, notempty

    isempty("") == true

True, если аргумент является пустой строкой или имеет значение null. См. также [isnull](#isnull).


**Синтаксис**

    isempty([value])


    isnotempty([value])


    notempty([value]) // alias of isnotempty

**Возвращает**

Указывает, является ли аргумент пустой строкой или имеет значение null.

|x|isempty(x)
|---|---
| "" | Да
|"x" | нет
|parsejson("")|Да
|parsejson("")|нет
|parsejson("{}")|нет


**Пример**


    T | where isempty(fieldName) | count




### replace

Заменяет все найденные совпадения регулярного выражения другой строкой.

**Синтаксис**

    replace(regex, rewrite, text)

**Аргументы**

* *regex:* [регулярное выражение](https://github.com/google/re2/wiki/Syntax) для поиска в *text*. Оно может содержать группы захвата в '('круглых скобках')'. 
* *rewrite:* замена регулярного выражения для любого сравнения, производимого с *matchingRegex*. Используйте `\0` для указания полного совпадения, `\1` для первой группы захвата, `\2` и более — для следующих групп захвата.
* *text:* строка.

**Возвращает**

*text* после замены всех вхождений *regex* с вычислениями *rewrite*. Совпадения не перекрываются.

**Пример**

Выражение:

```AIQL
range x from 1 to 5 step 1
| extend str=strcat('Number is ', tostring(x))
| extend replaced=replace(@'is (\d+)', @'was: \1', str)
```

Получены следующие результаты:

| x | str | заменена|
|---|---|---|
| 1 | Число равно: 1,000000 | Число было равно: 1,000000|
| 2 | Число равно: 2,000000 | Число было равно: 2,000000|
| 3 | Число равно: 3,000000 | Число было равно: 3,000000|
| 4\. | Число равно: 4,000000 | Число было равно: 4,000000|
| 5 | Число равно: 5,000000 | Число было равно: 5,000000|
 



### split

    split("aaa_bbb_ccc", "_") == ["aaa","bbb","ccc"]

Разбивает указанную строку в соответствии с заданным разделителем и возвращает массив строк, содержащий подстроки. При необходимости может быть возвращена конкретная подстрока, если она существует.

**Синтаксис**

    split(source, delimiter [, requestedIndex])

**Аргументы**

* *source*: исходная строка, которая будет разделена в соответствии с указанным разделителем.
* *delimiter*: разделитель, который будет использоваться для разделения исходной строки.
* *requestedIndex*: необязательный отсчитываемый от нуля индекс `int`. Если указан, возвращаемый массив строк будет содержать запрошенную подстроку, если она существует. 

**Возвращает**

Массив строк, содержащий подстроки заданной исходной строки, разделенные указанным разделителем.

**Примеры**

```
split("aa_bb", "_")           // ["aa","bb"]
split("aaa_bbb_ccc", "_", 1)  // ["bbb"]
split("", "_")                // [""]
split("a__b")                 // ["a","","b"]
split("aabbcc", "bb")         // ["aa","cc"]
```




### strcat

    strcat("hello", " ", "world")

Объединяет от 1 до 16 строк, переданных в качестве аргументов.

### strlen

    strlen("hello") == 5

Длина строки.

### substring

    substring("abcdefg", 1, 2) == "bc"

Извлекает подстроку из заданной исходной строки, начиная с указанного индекса. При необходимости можно указать длину запрашиваемой подстроки.

**Синтаксис**

    substring(source, startingIndex [, length])

**Аргументы**

* *source:* исходная строка, из которой будет извлечена подстрока.
* *startingIndex:* отсчитываемая от нуля позиция первого символа запрашиваемой подстроки.
* *length:* необязательный параметр, который может использоваться для указания требуемого количества символов в подстроке. 

**Возвращает**

Подстрока для указанной строки. Подстрока начинается с позиции startingIndex (отсчитывается начиная с нуля) и продолжается до конца строки или до требуемого количества символов, если оно указано.

**Примеры**

```
substring("123456", 1)        // 23456
substring("123456", 2, 2)     // 34
substring("ABCD", 0, 2)       // AB
```

### tolower

    tolower("HELLO") == "hello"

Преобразует строку в нижний регистр.

### toupper

    toupper("hello") == "HELLO"

Преобразует строку в верхний регистр.



## Идентификаторы GUID

    guid(00000000-1111-2222-3333-055567f333de)


## Массивы и объекты — динамические типы

[literals](#dynamic-literals) | [casting](#casting-dynamic-objects) | [operators](#operators) | [let clauses](#dynamic-objects-in-let-clauses) <br/> [arraylength](#arraylength) | [extractjson](#extractjson) | [parsejson](#parsejson) | [range](#range) | [treepath](#treepath) | [todynamic](#todynamic)


Ниже приведен результат запроса на исключение Application Insights. Значение `details` является массивом.

![](./media/app-analytics-scalars/310.png)

**Индексирование:** индексируйте массивы и объекты, так же как JavaScript:

    exceptions | take 1
    | extend 
        line = details[0].parsedStack[0].line,
        stackdepth = arraylength(details[0].parsedStack)

* Но используйте `arraylength` и другие функции AIQL (не ".length"!)

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


![](./media/app-analytics-scalars/410.png)


**treepath:** используется для поиска всех путей в сложном объекте:

    exceptions | take 1 | project timestamp, details 
    | extend path = treepath(details) 
    | mvexpand path


![](./media/app-analytics-scalars/420.png)

**buildschema:** используется для поиска минимальной схемы, которая учитывает все значения выражения в таблице:

    exceptions | summarize buildschema(details)

Результат:

    { "`indexer`":
     {"id":"string",
       "parsedStack":
       { "`indexer`": 
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

Обратите внимание, что `indexer` используется для обозначения тех случаев, в которых необходимо использовать числовой индекс. Некоторые допустимые пути для этой схемы будут следующими (предполагается, что эти индексы в этом примере находятся в диапазоне):

    details[0].parsedStack[2].level
    details[0].message
    arraylength(details)
    arraylength(details[0].parsedStack)



### Литералы массива и объекта

Чтобы создать динамический литерал, используйте `parsejson` (псевдоним `todynamic`) со строкой JSON в качестве аргумента:

* `parsejson('[43, 21, 65]')` — массив чисел
* `parsejson('{"name":"Alan", "age":21, "address":{"street":432,"postcode":"JLK32P"}}')` 
* `parsejson('21')` — одно значение динамического типа, содержащее число
* `parsejson('"21"')` — одно значение динамического типа, содержащее строку

Обратите внимание, что в отличие от JavaScript, в JSON необходимо заключать строки в двойные кавычки (`"`). Поэтому обычно проще заключать закодированные JSON литералы в одинарные кавычки (`'`).

В этом примере создается динамическое значение, а затем используются его поля:

```

T
| extend person = parsejson('{"name":"Alan", "age":21, "address":{"street":432,"postcode":"JLK32P"}}')
| extend n = person.name, add = person.address.street
```


<a name="operators"></a>
### Операторы и функции для динамических типов

|||
|---|---|
| *value* `in` *array*| Возвращает true, если в массиве *array* есть элемент, который равен значению *value*<br/>`where City in ('London', 'Paris', 'Rome')`
| *value* `!in` *array*| Возвращает true, если в массиве *array* нет ни одного элемента, который равен значению *value*
|[`arraylength(`array`)`](#arraylength)| Возвращает NULL, если это не массив
|[`extractjson(`path,object`)`](#extractjson)|Использует путь path для перехода к объекту object.
|[`parsejson(`source`)`](#parsejson)| Преобразует строку формата JSON в динамический объект.
|[`range(`from,to,step`)`](#range)| Массив значений
|[`mvexpand` listColumn](app-analytics-queries.md#mvexpand-operator) | Реплицирует строку для каждого значения в списке в указанной ячейке.
|[`summarize buildschema(`column`)`](app-analytics-queries.md#summarize-operator) |Определяет схему типа по содержимому столбца.
|[`summarize makelist(`column`)` ](app-analytics-queries.md#summarize-operator)| Выполняет сведение групп строк и помещает значения столбца в массив.
|[`summarize makeset(`column`)`](app-analytics-queries.md#summarize-operator) | Выполняет сведение групп строк и помещает значения столбца в массив без дублирования значений.

### Динамические объекты в предложениях let


В [предложениях let](app-analytics-queries.md#let-clause) динамические значения хранятся в виде строк, поэтому эти два предложения эквивалентны. Оба предложения требуют `parsejson` (или `todynamic`) перед использованием:

    let list1 = '{"a" : "somevalue"}';
    let list2 = parsejson('{"a" : "somevalue"}');

    T | project parsejson(list1).a, parsejson(list2).a




### arraylength

Количество элементов в динамическом массиве.

**Синтаксис**

    arraylength(array)

**Аргументы**

* *array:* значение `dynamic`.

**Возвращает**

Число элементов в *array* или `null`, если *array* не является массивом.

**Примеры**

```
arraylength(parsejson('[1, 2, 3, "four"]')) == 4
arraylength(parsejson('[8]')) == 1
arraylength(parsejson('[{}]')) == 1
arraylength(parsejson('[]')) == 0
arraylength(parsejson('{}')) == null
arraylength(parsejson('21')) == null
```



### extractjson

    extractjson("$.hosts[1].AvailableMB", EventText, typeof(int))

Возвращает указанный элемент текста JSON с помощью выражения пути. При необходимости преобразует извлеченную строку к указанному типу.


**Синтаксис**

```

    string extractjson(jsonPath, dataSource)​​ 
    resulttype extractjson(jsonPath, dataSource, typeof(resulttype))​​
```


**Возвращает**

Эта функция выполняет запрос JsonPath к источнику данных dataSource, который содержит допустимую строку JSON. При необходимости это значение преобразуется в другой тип, в зависимости от третьего аргумента.



**Пример**

Обозначения с [квадратными скобками] и точками эквивалентны:

    ... | extend AvailableMB = extractjson("$.hosts[1].AvailableMB", EventText, typeof(int)) | ...

    ... | extend AvailableMD = extractjson("$['hosts'][1]['AvailableMB']", EventText, typeof(int)) | ...

#### Выражения пути JSON

|||
|---|---|
|`$`|Корневой объект|
|`@`|Текущий объект|
|`.` или `[ ]` | Дочерний|
|`[ ]`|Индекс массива|

*(В настоящее время подстановочные знаки, рекурсии, объединения и срезы не реализованы.)*


**Советы по улучшению производительности**

* Применяйте предложение where перед использованием `extractjson()`
* По возможности используйте сопоставление с регулярным выражением с [extract](#extract) вместо него. Он выполняется гораздо быстрее и эффективнее, если JSON создается из шаблона.
* Используйте `parsejson()`, если необходимо извлечь из JSON несколько значений.
* При возможности проведите анализ JSON при обработке, объявив динамический тип столбца.



### parsejson

Интерпретирует `string` как [значение JSON](http://json.org/) и возвращает значение как `dynamic`. Этот метод более предпочтителен, чем `extractjson()`, когда необходимо извлечь более одного элемента из составного объекта JSON.

**Синтаксис**

    parsejson(json)

**Аргументы**

* *json:* документ JSON.

**Возвращает**

Объект типа `dynamic`, определенный *json*.

**Пример**

В следующем примере, когда `context_custom_metrics` представляет собой `string`, который выглядит следующим образом:

```
{"duration":{"value":118.0,"count":5.0,"min":100.0,"max":150.0,"stdDev":0.0,"sampledValue":118.0,"sum":118.0}}
```

затем следующий фрагмент AIQL получает значение слота `duration` в объекте и из него получает два слота `duration.value` и `duration.min` (`118.0` и `110.0`, соответственно).

```AIQL
T
| ...
| extend d=parsejson(context_custom_metrics) 
| extend duration_value=d.duration.value, duration_min=d["duration"]["min"]
```



### range

Функция `range()` (не следует путать с оператором `range`) создает динамический массив, содержащий последовательность равномерно распределенных значений.

**Синтаксис**

    range(start, stop, step)

**Аргументы**

* *start:* значение первого элемента результирующего массива. 
* *stop:* значение последнего элемента результирующего массива или наименьшее значение, которое больше, чем последний элемент результирующего массива, и находится в целом количестве шагов *step* от первого элемента *start*.
* *step:* различие между двумя последовательными элементами массива.

**Примеры**

В следующем примере возвращается `[1, 4, 7]`:

```AIQL
range(1, 8, 3)
```

Следующий пример возвращает массив, содержащий все дни 2015 года:

```AIQL

    range(datetime(2015-01-01), datetime(2015-12-31), 1d)
```

### todynamic

    todynamic('{"a":"a1", "b":["b1", "b2"]}')

Преобразует строку в динамическое значение.

### treepath

    treepath(dynamic_object)

Перечисляет все выражения пути, которые определяют листья в динамическом объекте.

**Возвращает**

Массив выражений пути.

**Примеры**

    treepath(parsejson('{"a":"b", "c":123}')) 
    =>       ["['a']","['c']"]
    treepath(parsejson('{"prop1":[1,2,3,4], "prop2":"value2"}'))
    =>       ["['prop1']","['prop1'][0]","['prop2']"]
    treepath(parsejson('{"listProperty":[100,200,300,"abcde",{"x":"y"}]}'))
    =>       ["['listProperty']","['listProperty'][0]","['listProperty'][0]['x']"]

Обратите внимание, что "[0]" определяет наличие массива, но не обозначает индекс, используемый указанным расположением.



[AZURE.INCLUDE [app-analytics-footer](../../includes/app-analytics-footer.md)]

<!---HONumber=AcomDC_0309_2016-->
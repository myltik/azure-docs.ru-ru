<properties 
	pageTitle="Инструкции в аналитике приложений" 
	description="Запросы, выражения и инструкции let в аналитике приложений, мощной поисковой системе для Application Insights." 
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
	ms.date="03/01/2016" 
	ms.author="awills"/>


 
# Инструкции в аналитике приложений

[Аналитика приложений](app-analytics.md) — это мощная поисковая система для вашей телеметрии [Application Insights](app-insights-overview.md). На этих страницах описан CSL, язык запросов аналитики приложений.


[AZURE.INCLUDE [app-analytics-top-index](../../includes/app-analytics-top-index.md)]

## Модель данных

В CSL:

* *База данных* содержит ноль или более именованных *таблиц*.
* *Таблица* содержит:
 * Один или несколько именованных *столбцов*. Каждый столбец имеет *тип*.
 * Одну или несколько *строк*.
* Каждая строка содержит одну или несколько *ячеек*, по одной для каждого столбца таблицы.
* Ячейка может содержать значение типа соответствующего столбца или `null`.


## Имена сущностей

Каждый столбец, таблица или база данных имеет имя, уникальное в пределах контейнера.

Можно ссылаться на сущность либо по имени (если контекст является однозначным), либо определяя ее контейнер. Например, ссылаться на `MyColumn` также можно как на `MyTable.MyColumn` или `MyDatabase.MyTable.MyColumn`.

Имя может содержать до 1024 знаков. Оно зависит от регистра и может содержать буквы, цифры и символы подчеркивания (`_`).

Кроме того, имена могут заключаться в кавычки, чтобы они могли содержать другие знаки. Например:

|||
|---|---|
|`['path\\file\n\'x\'']` | Используйте ``, чтобы экранировать знаки.|
|`["d-e.=/f#\n"]` | |
|`[@'path\file']`| Экранирования нет: `` является литералом.|
|`[@"\now & then"]` | |
|`[where]` | Использование ключевого слова языка в качестве имени.|

Имена также могут быть дополнены именем базы данных (см. раздел [Cross-databse queries](#cross-database-queries)).

```
database("MyDb").Table
```


## Инструкции

В CSL существует четыре типа инструкций.

### Запросы данных
  
Запросы только на чтение данных, хранящихся в аналитике приложений. Например:

* `event` – возвращает все записи таблицы с именем "event".
* `event | count` – возвращает число записей в таблице "event".

    
## Инструкции let

#### Обзор
Перед инструкцией запроса данных можно указать одну или нескольких инструкций let. Это позволяет привязать имя (действительное имя сущности) к выражению. Имена, определенные с помощью инструкции let, затем можно использовать один или несколько раз в следующей инструкции запроса данных.

Инструкция let может привязать имя к выражениям следующих видов:
1. скалярные;
2. табличные данные. 

При привязке к табличным данным инструкцию let можно преобразовать во "view". Это позволит инструкции участвовать в операциях "union *".

#### Синтаксис

`let <name> = <expression>`

<expression> Может быть объявление лямбда нулем, одним или несколькими аргументами.

`() {...}`

`(<arg0>:<type0>, ...) {...}`

Примеры. Следующий пример привязывает имя "x" к скалярному литералу "1".

```
let x=1;
range y from x to x step x
```

Следующий пример привязывает ценность журналов за один час к имени "Window", а затем выполняет самосоединение с "Window".


```
let Window=Logs | where Timestamp > ago(1h);
Window | where  ... | join (Window | where …) on ActivityId| ...
```

В двух следующих примерах показано использование инструкции let с лямбда-выражением.


```
let Test = () { range x from 1 to 10 step 1 };
Test | count

let step=1;
let Test = (start:long, end:long) { range x from start to end step 1 };
Test(1, 10) | count
```

С помощью ключевого слова "view" можно преобразовать инструкции let в представление, которое участвует в операциях "union *".


```
let Test1 = view () { range x from start to end step 1 };
let Test2 = view () { range x from start to end step 1 };
union * | count
// Result: 20
```


## Инструкция restrict

#### Обзор
Можно ограничить доступ запросов к объектам, указав их список с помощью следующей инструкции.


```
restrict access to (<entity1, entity2, ...>);
```
 
Эта инструкция разрешает доступ только к сущностям (для инструкций let или табличных сущностей), которые разрешены инструкцией restrict.
 
Примеры:

```
// Restricting access to Test statement only
let Test = () { range x from 1 to 10 step 1 };
restrict access to (Test);
Test
 
// Assume that there is a table called Table1, Table2 in the database
let View1 = view () { Table1 | project Column1 };
let View2 = view () { Table2 | project Column1, Column2 };
restrict access to (View1, View2);
 
// When those statements appear before the command - the next works
let View1 = view () { Table1 | project Column1 };
let View2 = view () { Table2 | project Column1, Column2 };
restrict access to (View1, View2);
View1 |  count
 
// When those statements appear before the command - the next access is not allowed
let View1 = view () { Table1 | project Column1 };
let View2 = view () { Table2 | project Column1, Column2 };
restrict access to (View1, View2);
Table1 |  count
```


## Составление запроса

*Запрос* составляется по шаблону:

- *источник* | *фильтр* | *фильтр* …

Например:


```
Logs | where Timestamp > ago(1d) | count
```

* *Источник* — имя таблицы базы данных или ранее определенной таблицы результатов.
* Каждый *фильтра* вызывает оператор запроса, например `where` или `count`, с соответствующими параметрами. Параметрами могут быть *скалярные выражения*, вложенные *запросы данных* или имена столбцов.

Например:


```
Logs 
| where Timestamp > ago(1d) 
| join 
(
    Events 
    | where continent == 'Europe'
) on RequestId 
``` 

## Инструкции let

Инструкцию let можно использовать для определения функций — именованных выражений, принимающих ноль или более аргументов и возвращающих значения. Эти функции могут затем "вызываться" другими инструкциями.

### Именованные значения

Определение имен для представления скалярных значений.


```
let n = 10;  // number
let place = "Dallas";  // string
let cutoff = ago(62d); // datetime
Events 
| where timestamp > cutoff 
    and city == place 
| take n
```

Определение имен для представления результатов запроса.


```
let Cities = Events | summarize dcount(city) by country;
Cities | take 10
```

Это особенно удобно для самосоединения.


```
let Recent = Events | where timestamp > ago(7d);
Recent | where name contains "session_started" 
| project start = timestamp, session_id
| join (Recent 
        | where name contains "session_ended" 
        | project stop = timestamp, session_id)
    on session_id
| extend duration = stop - start 
```

## Именованные функции

Определение функций, возвращающих скалярные результаты.


```
let square = (n:long){n*n};
// yield 9 rows
Events | take square(3)    
```

Определение функций, возвращающих результаты запроса.


```
let TopEvents= (n:long, when:datetime){Events 
                | where timestamp > when | take n};
TopEvents(5, ago(7d)) 
```

Параметры именованных функций должны быть скалярными.





[AZURE.INCLUDE [app-analytics-footer](../../includes/app-analytics-footer.md)]

<!---HONumber=AcomDC_0309_2016-->
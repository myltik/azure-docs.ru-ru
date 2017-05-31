---
title: "Использование оконных функций U-SQL для заданий в службе Azure Data Lake Analytics | Документация Майкрософт"
description: "Узнайте, как использовать оконные функции U-SQL. "
services: data-lake-analytics
documentationcenter: 
author: saveenr
manager: saveenr
editor: cgronlun
ms.assetid: a5e14b32-d5eb-4f4b-9258-e257359f9988
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data`
ms.date: 12/05/2016
ms.author: edmaca
ms.translationtype: Human Translation
ms.sourcegitcommit: e72275ffc91559a30720a2b125fbd3d7703484f0
ms.openlocfilehash: 55d19a00198f1943a8196d31399c617397b4e5d2
ms.contentlocale: ru-ru
ms.lasthandoff: 05/05/2017


---
# <a name="using-u-sql-window-functions-for-azure-data-lake-analytics-jobs"></a>Использование оконных функций U-SQL для заданий в службе аналитики озера данных Azure
Оконные функции были добавлены в стандарт SQL ISO/ANSI в 2003 году. В языке U-SQL используется ряд оконных функций, определенных в стандарте SQL ANSI.

Оконные функции используются для выполнения вычислений в наборах строк, именуемых *окнами*. Окна определяются предложением OVER. Оконные функции очень эффективны в некоторых случаях.

Оконные функции разделены на несколько категорий: 

* [статистические функции для создания отчетов](#reporting-aggregation-functions), например SUM и AVG;
* [ранжирующие функции](#ranking-functions), например DENSE_RANK, ROW_NUMBER, RANK и NTILE;
* [аналитические функции](#analytic-functions), например интегральная функция распределения, процентили или обращение к данным из предыдущей строки того же результирующего набора без использования самосоединения.

## <a name="sample-datasets"></a>Типовые наборы данных
В этом руководстве используются два набора данных.

### <a name="the-querylog-sample-dataset"></a>Пример набора данных журнала запросов
  
Журнал запросов — это список поисковых запросов пользователей в поисковой системе. Каждый журнал запросов включает в себя следующую информацию.
  
* Query - What the user was searching for
* Latency - How fast the query came back to the user in milliseconds
* Vertical - What kind of content the user was interested in (Web links, Images, Videos)  
 
```
@querylog = 
    SELECT * FROM ( VALUES
        ("Banana"  , 300, "Image" ),
        ("Cherry"  , 300, "Image" ),
        ("Durian"  , 500, "Image" ),
        ("Apple"   , 100, "Web"   ),
        ("Fig"     , 200, "Web"   ),
        ("Papaya"  , 200, "Web"   ),
        ("Avocado" , 300, "Web"   ),
        ("Cherry"  , 400, "Web"   ),
        ("Durian"  , 500, "Web"   ) )
    AS T(Query,Latency,Vertical);
```

## <a name="the-employees-sample-dataset"></a>Пример набора данных о сотрудниках
  
Набор данных о сотрудниках содержит такие поля:
  
* EmpID — идентификатор сотрудника
* EmpName — имя сотрудника
* DeptName — название отдела 
* DeptID — идентификатор отдела
* Salary — заработная плата сотрудника

```
@employees = 
    SELECT * FROM ( VALUES
        (1, "Noah",   "Engineering", 100, 10000),
        (2, "Sophia", "Engineering", 100, 20000),
        (3, "Liam",   "Engineering", 100, 30000),
        (4, "Emma",   "HR",          200, 10000),
        (5, "Jacob",  "HR",          200, 10000),
        (6, "Olivia", "HR",          200, 10000),
        (7, "Mason",  "Executive",   300, 50000),
        (8, "Ava",    "Marketing",   400, 15000),
        (9, "Ethan",  "Marketing",   400, 10000) )
    AS T(EmpID, EmpName, DeptName, DeptID, Salary);
```  

## <a name="compare-window-functions-to-grouping"></a>Сравнение оконных функций с функцией группирования
Оконные функции и функция группирования концептуально связаны. Давайте разберемся в том, что их объединяет и отличает.

### <a name="use-aggregation-and-grouping"></a>Использование агрегирования и группирования
Приведенный ниже запрос рассчитывает совокупную заработную плату всех сотрудников с помощью агрегирования.

    @result = 
        SELECT 
            SUM(Salary) AS TotalSalary
        FROM @employees;

Результатом будет одна строка с одним столбцом. $165000 — это сумма зарплат (значение Salary) по всем строкам таблицы. 

| TotalSalary |
| --- |
| 165 000 |


Приведенная ниже инструкция вычисляет совокупную зарплату каждого отдела с помощью предложения GROUP BY.

    @result=
        SELECT DeptName, SUM(Salary) AS SalaryByDept
        FROM @employees
        GROUP BY DeptName;

Получаются такие результаты:

| DeptName | SalaryByDept |
| --- | --- |
| Engineering |60 000 |
| HR |30 000 |
| Executive |50 000 |
| Marketing |25 000 |

Сумма значений в столбце SalaryByDept равна 165 000 $, что соответствует сумме в сценарии выше.

В каждом из этих примеров количество выходных строк меньше количества входных.

* Если предложение GROUP BY не используется, агрегирование сворачивает все строки в одну. 
* Если предложение GROUP BY используется, вы получаете N выходных строк, где N — это количество уникальных значений в отображаемых данных.  В нашем случае вы получите 4 строки выходных данных.

### <a name="use-a-window-function"></a>Использование оконной функции
В следующем примере предложение OVER пустое, поэтому окно содержит все строки. Функция SUM в этом примере применяется к предложению OVER, перед которым она стоит.

Этот запрос можно читать так: "сумма зарплат по окну, содержащему все строки".

    @result=
        SELECT
            EmpName,
            SUM(Salary) OVER( ) AS SalaryAllDepts
        FROM @employees;

В отличие от функции GROUP BY, количество выходных строк равно количеству входных. 

| EmpName | TotalAllDepts |
| --- | --- |
| Noah |165 000 |
| Sophia |165 000 |
| Liam |165 000 |
| Emma |165 000 |
| Jacob |165 000 |
| Olivia |165 000 |
| Mason |165 000 |
| Ava |165 000 |
| Ethan |165 000 |

Значение 165 000 (сумма всех зарплат) помещается в каждую выходную строку. Итоговое значение берется из «окна» всех строк, поэтому оно включает все зарплаты. 

В следующем примере показано, как уточнить "окно", чтобы получить список всех сотрудников, названия соответствующих отделов и совокупную зарплату в каждом отделе. В предложение OVER добавляется предложение PARTITION BY.

    @result=
    SELECT
        EmpName, DeptName,
        SUM(Salary) OVER( PARTITION BY DeptName ) AS SalaryByDept
    FROM @employees;

Получаются такие результаты:

| EmpName | DeptName | SalaryByDep |
| --- | --- | --- |
| Noah |Engineering |60 000 |
| Sophia |Engineering |60 000 |
| Liam |Engineering |60 000 |
| Mason |Executive |50 000 |
| Emma |HR |30 000 |
| Jacob |HR |30 000 |
| Olivia |HR |30 000 |
| Ava |Marketing |25 000 |
| Ethan |Marketing |25000 |

Количество входных строк опять равно количеству выходных, но в каждой строке указан совокупный размер зарплаты всего отдела.

## <a name="reporting-aggregation-functions"></a>Статистические функции для создания отчетов
Оконные функции поддерживают также такие статистические функции:

* COUNT
* SUM
* MIN
* MAX
* AVG
* STDEV
* VAR

Синтаксис выглядит вот так:

    <AggregateFunction>( [DISTINCT] <expression>) [<OVER_clause>]

Примечание. 

* По умолчанию статистические функции, за исключением COUNT, игнорируют значения NULL.
* Если статистическая функция указана вместе с предложением OVER, предложение ORDER BY не допускается в предложении OVER.

### <a name="use-sum"></a>Использование функции SUM
В следующем примере в каждую входную строку добавляется совокупная зарплата соответствующего отдела.

    @result=
        SELECT 
            *,
            SUM(Salary) OVER( PARTITION BY DeptName ) AS TotalByDept
        FROM @employees;

Результат выглядит так:

| EmpID | EmpName | DeptName | DeptID | Salary | TotalByDept |
| --- | --- | --- | --- | --- | --- |
| 1 |Noah |Engineering |100 |10 000 |60 000 |
| 2 |Sophia |Engineering |100 |20 000 |60 000 |
| 3 |Liam |Engineering |100 |30 000 |60 000 |
| 7 |Mason |Executive |300 |50 000 |50 000 |
| 4 |Emma |HR |200 |10 000 |30 000 |
| 5 |Jacob |HR |200 |10 000 |30 000 |
| 6 |Olivia |HR |200 |10 000 |30 000 |
| 8 |Ava |Marketing |400 |15 000 |25 000 |
| 9 |Ethan |Marketing |400 |10 000 |25 000 |

### <a name="use-count"></a>Использование функции COUNT
В следующем примере в каждую строку добавляется дополнительное поле, в котором показывается общее количество сотрудников в каждом отделе.

    @result =
        SELECT *, 
            COUNT(*) OVER(PARTITION BY DeptName) AS CountByDept 
        FROM @employees;

Получаются такие результаты:

| EmpID | EmpName | DeptName | DeptID | Salary | CountByDept |
| --- | --- | --- | --- | --- | --- |
| 1 |Noah |Engineering |100 |10 000 |3 |
| 2 |Sophia |Engineering |100 |20 000 |3 |
| 3 |Liam |Engineering |100 |30 000 |3 |
| 7 |Mason |Executive |300 |50 000 |1 |
| 4 |Emma |HR |200 |10 000 |3 |
| 5 |Jacob |HR |200 |10 000 |3 |
| 6 |Olivia |HR |200 |10 000 |3 |
| 8 |Ava |Marketing |400 |15 000 |2 |
| 9 |Ethan |Marketing |400 |10 000 |2 |

### <a name="use-min-and-max"></a>Использование функций MIN и MAX
В следующем примере в каждую строку добавляется дополнительное поле, в котором показывается минимальная зарплата в отделе.

    @result =
        SELECT 
            *,
            MIN(Salary) OVER( PARTITION BY DeptName ) AS MinSalary
        FROM @employees;

Получаются такие результаты:

| EmpID | EmpName | DeptName | DeptID | Salary | MinSalary |
| --- | --- | --- | --- | --- | --- |
| 1 |Noah |Engineering |100 |10 000 |10 000 |
| 2 |Sophia |Engineering |100 |20 000 |10 000 |
| 3 |Liam |Engineering |100 |30 000 |10 000 |
| 7 |Mason |Executive |300 |50 000 |50 000 |
| 4 |Emma |HR |200 |10 000 |10 000 |
| 5 |Jacob |HR |200 |10 000 |10 000 |
| 6 |Olivia |HR |200 |10 000 |10 000 |
| 8 |Ava |Marketing |400 |15 000 |10 000 |
| 9 |Ethan |Marketing |400 |10 000 |10 000 |

## <a name="ranking-functions"></a>ранжирующие функции
Ранжирующие функции возвращают ранжирующее значение (LONG) для каждой строки в каждой секции, которые определены предложениями PARTITION BY и OVER. Порядок ранжирования определяется предложением ORDER BY в предложении OVER.

Поддерживаются такие ранжирующие функции:

* RANK;
* DENSE_RANK; 
* NTILE;
* ROW_NUMBER

**Синтаксис**

    [ RANK() | DENSE_RANK() | ROW_NUMBER() | NTILE(<numgroups>) ]
        OVER (
            [PARTITION BY <identifier, > …[n]]
            [ORDER BY <identifier, > …[n] [ASC|DESC]] 
    ) AS <alias>

* В ранжирующих функциях использовать предложение ORDER BY необязательно. Если предложение ORDER BY не указано, U-SQL назначает значения в том порядке, в котором считывает запись. В этом случае значения ROW_NUMBER, RANK и DENSE_RANK будут недетерминированными.
* Для функции NTILE требуется выражение, результатом которого является положительное целое число. Это число определяет количество групп, на которое необходимо разделить каждую секцию. Этот идентификатор используется только с ранжирующей функцией NTILE. 

Дополнительные сведения о предложении OVER см. в [справке по U-SQL](http://go.microsoft.com/fwlink/p/?LinkId=691348).

Функции ROW_NUMBER, RANK и DENSE_RANK назначают строкам в окне номера. Мы не будем разбирать эти функции по отдельности, а рассмотрим, как они работают с одинаковыми входными данными.

    @result =
    SELECT 
        *,
        ROW_NUMBER() OVER (PARTITION BY Vertical ORDER BY Latency) AS RowNumber,
        RANK() OVER (PARTITION BY Vertical ORDER BY Latency) AS Rank, 
        DENSE_RANK() OVER (PARTITION BY Vertical ORDER BY Latency) AS DenseRank 
    FROM @querylog;

Обратите внимание, что предложения OVER идентичны. Получаются такие результаты:

| Запрос | Задержка: INT | Vertical | RowNumber | RANK; | DenseRank |
| --- | --- | --- | --- | --- | --- |
| Banana |300 |Образ — |1 |1 |1 |
| Cherry |300 |Образ — |2 |1 |1 |
| Durian |500 |Образ — |3 |3 |2 |
| Apple |100 |Web |1 |1 |1 |
| Fig |200 |Web |2 |2 |2 |
| Papaya |200 |Web |3 |2 |2 |
| Fig |300 |Web |4 |4 |3 |
| Cherry |400 |Web |5 |5 |4 |
| Durian |500 |Web |6 |6 |5 |

### <a name="rownumber"></a>ROW_NUMBER
В каждом окне (для значений Image или Web в поле Vertical) строкам присваиваются последовательные номера (с шагом в единицу) с упорядочиванием по полю Latency.  

![Оконная функция ROW_NUMBER, язык U-SQL](./media/data-lake-analytics-use-windowing-functions/u-sql-windowing-function-row-number-result.png)

### <a name="rank"></a>RANK;
В отличие от ROW_NUMBER(), функция RANK() учитывает значение поля Latency, указанного в предложении ORDER BY для окна.

Так как первые два значения в поле Latency совпадают, RANK начинается с двух единиц (1, 1, 3). Затем идет тройка, так как значение Latency изменилось на 500. Ключевая особенность функции RANK в том, что одинаковым значениям присваиваются одинаковые числа, но значения числа RANK растут синхронно со значениями ROW_NUMBER. То же самое мы наблюдаем для строк со значением Web в поле Vertical (2, 2, 4).

![Оконная функция RANK, язык U-SQL](./media/data-lake-analytics-use-windowing-functions/u-sql-windowing-function-rank-result.png)

### <a name="denserank"></a>DENSE_RANK;
DENSE_RANK действует так же, как и RANK, за исключением того, что она не пропускает значения для синхронизации с ROW_NUMBER. DENSE_RANK просто переходит к следующему числу по порядку. В нашем примере это поведение отражено в последовательностях 1, 1, 2 и 2, 2, 3.

![Оконная функция DENSE_RANK, язык U-SQL](./media/data-lake-analytics-use-windowing-functions/u-sql-windowing-function-dense-rank-result.png)

### <a name="remarks"></a>Примечания
* Если предложение ORDER BY не указано, ранжирующая функция применяется к набору строк без упорядочения, что приводит к недетерминированным результатам.
* Чтобы строки, возвращаемые запросом с функцией ROW_NUMBER, каждый раз упорядочивались одинаково, должны выполняться следующие условия:
  
  * значения в секционированном столбце — уникальные;
  * значения в столбцах ORDER BY — уникальные;
  * комбинации значений в секционированном столбце и столбце ORDER BY — уникальные.

### <a name="ntile"></a>NTILE;
Функция NTILE распределяет строки упорядоченной секции в заданное количество групп. Группы нумеруются, начиная с единицы. 

Запрос из следующего примера разбивает набор строк в каждой секции (по полю Vertical) на 4 группы с сортировкой по полю Latency, и возвращает номер группы для каждой строки. 

Набор строк, имеющих значение Image в поле Vertical, содержит всего три строки, поэтому в нем будет три группы. 

Набор строк со значением Web содержит шесть строк.  Две дополнительные строки добавляются в первую и вторую группы. Поэтому группы 1 и 2 содержат по две строки, а группы 3 и 4 — только по одной.  

    @result =
        SELECT 
            *,
            NTILE(4) OVER(PARTITION BY Vertical ORDER BY Latency) AS Quartile   
        FROM @querylog;

Получаются такие результаты:

| Запрос | Задержка | Vertical | Quartile |
| --- | --- | --- | --- |
| Banana |300 |Образ — |1 |
| Cherry |300 |Образ — |2 |
| Durian |500 |Образ — |3 |
| Apple |100 |Web |1 |
| Fig |200 |Web |1 |
| Papaya |200 |Web |2 |
| Fig |300 |Web |2 |
| Cherry |400 |Web |3 |
| Durian |500 |Web |4 |

Функция NTILE принимает параметр (numgroups). Numgroups — это положительное выражение целого числа или длинной константы, определяющее количество групп, на которые необходимо разделить каждую секцию. 

* Если количество строк в секции делится на значение numgroups без остатка, все группы будут иметь одинаковый размер. 
* Если количество строк в секции не делится на значение numgroups без остатка, все группы будут иметь немного отличающиеся размеры. Сначала идут большие группы, а затем — маленькие. Порядок определяется предложением OVER. 

Например:

    100 rows divided into 4 groups: 
    [ 25, 25, 25, 25 ]

    102 rows divided into 4 groups: 
    [ 26, 26, 25, 25 ]

### <a name="top-n-records-per-partition-via-rank-denserank-or-rownumber"></a>Получение N первых записей из каждой секции с помощью функции RANK, DENSE_RANK или ROW_NUMBER
Пользователям часто нужно выбрать только несколько первых строк (TOP n) из каждой группы, а этого нельзя добиться с помощью традиционных предложений GROUP BY. 

Приведенный ниже пример продемонстрирован в начале раздела о функциях ранжирования. Он не показывает N первых записей в каждой секции.

    @result =
    SELECT 
        *,
        ROW_NUMBER() OVER (PARTITION BY Vertical ORDER BY Latency) AS RowNumber,
        RANK() OVER (PARTITION BY Vertical ORDER BY Latency) AS Rank,
        DENSE_RANK() OVER (PARTITION BY Vertical ORDER BY Latency) AS DenseRank
    FROM @querylog;

Получаются такие результаты:

| Запрос | Задержка | Vertical | RANK; | DenseRank | RowNumber |
| --- | --- | --- | --- | --- | --- |
| Banana |300 |Образ — |1 |1 |1 |
| Cherry |300 |Образ — |1 |1 |2 |
| Durian |500 |Образ — |3 |2 |3 |
| Apple |100 |Web |1 |1 |1 |
| Fig |200 |Web |2 |2 |2 |
| Papaya |200 |Web |2 |2 |3 |
| Fig |300 |Web |4 |3 |4 |
| Cherry |400 |Web |5 |4 |5 |
| Durian |500 |Web |6 |5 |6 |

### <a name="top-n-with-dense-rank"></a>Получение N первых записей с помощью функции DENSE_RANK
В следующем примере возвращаются три первые записи из каждой группы. Номера (ранги) строк в каждой секции присваиваются последовательно, без разрывов.

    @result =
    SELECT 
        *,
        DENSE_RANK() OVER (PARTITION BY Vertical ORDER BY Latency) AS DenseRank
    FROM @querylog;

    @result = 
        SELECT *
        FROM @result
        WHERE DenseRank <= 3;

Получаются такие результаты:

| Запрос | Задержка | Vertical | DenseRank |
| --- | --- | --- | --- |
| Banana |300 |Образ — |1 |
| Cherry |300 |Образ — |1 |
| Durian |500 |Образ — |2 |
| Apple |100 |Web |1 |
| Fig |200 |Web |2 |
| Papaya |200 |Web |2 |
| Fig |300 |Web |3 |

### <a name="top-n-with-rank"></a>Получение N первых записей с помощью функции RANK
    @result =
        SELECT 
            *,
            RANK() OVER (PARTITION BY Vertical ORDER BY Latency) AS Rank
        FROM @querylog;

    @result = 
        SELECT *
        FROM @result
        WHERE Rank <= 3;

Получаются такие результаты:    

| Запрос | Задержка | Vertical | RANK; |
| --- | --- | --- | --- |
| Banana |300 |Образ — |1 |
| Cherry |300 |Образ — |1 |
| Durian |500 |Образ — |3 |
| Apple |100 |Web |1 |
| Fig |200 |Web |2 |
| Papaya |200 |Web |2 |

### <a name="top-n-with-rownumber"></a>Получение N первых записей с помощью функции ROW_NUMBER
    @result =
        SELECT 
            *,
            ROW_NUMBER() OVER (PARTITION BY Vertical ORDER BY Latency) AS RowNumber
        FROM @querylog;

    @result = 
        SELECT *
        FROM @result
        WHERE RowNumber <= 3;

Получаются такие результаты:   

| Запрос | Задержка | Vertical | RowNumber |
| --- | --- | --- | --- |
| Banana |300 |Образ — |1 |
| Cherry |300 |Образ — |2 |
| Durian |500 |Образ — |3 |
| Apple |100 |Web |1 |
| Fig |200 |Web |2 |
| Papaya |200 |Web |3 |

### <a name="assign-globally-unique-row-number"></a>Назначение глобального уникального номера строки
Зачастую лучше каждой строке назначать глобальный уникальный номер. Обычно проще и эффективнее использовать ранжирующие функции, чем преобразователи данных.

    @result =
        SELECT 
            *,
            ROW_NUMBER() OVER () AS RowNumber
        FROM @querylog;

<!-- ################################################### -->
## <a name="analytic-functions"></a>аналитические функции
Аналитические функции используются для анализа распределения значений в окнах. Чаще всего аналитические функции используют для вычисления процентилей.

**Поддерживаемые аналитические функции для окон**

* CUME_DIST 
* PERCENT_RANK
* PERCENTILE_CONT
* PERCENTILE_DISC

### <a name="cumedist"></a>Функция CUME_DIST

Функция CUME_DIST вычисляет относительное положение указанного значения в группе значений. Она вычисляет процент запросов, задержка которых меньше или равна задержке текущего запроса в той же вертикали. 

Если упорядочение выполняется по возрастанию, для каждой строки R функция CUME_DIST возвращает число строк, значения в которых не превышают значение в строке R, деленное на общее число строк в секции. 

CUME_DIST возвращает числа в диапазоне 0 < x < = 1.

**Синтаксис**

    CUME_DIST() 
        OVER (
            [PARTITION BY <identifier, > …[n]]
            ORDER BY <identifier, > …[n] [ASC|DESC] 
    ) AS <alias>

В следующем примере функция CUME_DIST используется для вычисления процентиля задержки каждого запроса в вертикали. 

    @result=
        SELECT 
            *,
            CUME_DIST() OVER(PARTITION BY Vertical ORDER BY Latency) AS CumeDist
        FROM @querylog;

Получаются такие результаты:

| Запрос | Задержка | Vertical | CumeDist |
| --- | --- | --- | --- |
| Durian |500 |Образ — |1 |
| Banana |300 |Образ — |0,666666666666667 |
| Cherry |300 |Образ — |0,666666666666667 |
| Durian |500 |Web |1 |
| Cherry |400 |Web |0,833333333333333 |
| Fig |300 |Web |0,666666666666667 |
| Fig |200 |Web |0,5 |
| Papaya |200 |Web |0,5 |
| Apple |100 |Web |0,166666666666667 |

В секции есть шесть строк с ключом секции Web

* из них в шести строках значение не превышает 500, поэтому CUME_DIST возвращает 6/6=1;
* из них в пяти строках значение не превышает 400, поэтому CUME_DIST возвращает 5/6=0,83;
* из них в четырех строках значение не превышает 300, поэтому CUME_DIST возвращает 4/6=0,66;
* из них в трех строках значение не превышает 200, поэтому CUME_DIST возвращает 3/6=0,5. Существуют две строки с одинаковым значением задержки.
* из них только в одной строке значение не превышает 100, поэтому CUME_DIST возвращает 1/6=0,16. 

**Примечания об использовании**

* Равные значения всегда дают одно и то же значение интегральной функции распределения.
* Значения NULL рассматриваются как минимально возможные значения.
* Для вычисления CUME_DIST требуется предложение ORDER BY.
* Функция CUME_DIST подобна функции PERCENT_RANK.

Примечание. Предложение ORDER BY использовать нельзя, если после инструкции SELECT не указана инструкция OUTPUT.

### <a name="percentrank"></a>Функция PERCENT_RANK
Функция PERCENT_RANK вычисляет относительный ранг строки в группе строк. Функция используется для определения относительного положения значения в набор строк или секции. Диапазон значений, возвращаемых функцией PERCENT_RANK, больше 0 и меньше 1 или равен ей. В отличие от функции CUME_DIST, функция PERCENT_RANK всегда возвращает 0 для первой строки.

** Синтаксис:**

    PERCENT_RANK() 
        OVER (
            [PARTITION BY <identifier, > …[n]]
            ORDER BY <identifier, > …[n] [ASC|DESC] 
        ) AS <alias>

**Примечания**

* Для первой строки в любом наборе функция PERCENT_RANK возвращает 0.
* Значения NULL рассматриваются как минимально возможные значения.
* Для PERCENT_RANK требуется предложение ORDER BY.
* Функция CUME_DIST подобна функции PERCENT_RANK. 

В следующем примере функция PERCENT_RANK используется для вычисления процентиля задержки каждого запроса в вертикали. 

Предложение PARTITION BY указано для того, чтобы секционировать строки результирующего набора по вертикали. Предложение ORDER BY в предложении OVER упорядочивает строки в каждой секции. 

Значение, возвращаемое функцией PERCENT_RANK, обозначает выраженный в процентах ранг задержки запросов в вертикали. 

    @result=
        SELECT 
            *,
            PERCENT_RANK() OVER(PARTITION BY Vertical ORDER BY Latency) AS PercentRank
        FROM @querylog;

Получаются такие результаты:

| Запрос | Задержка: INT | Vertical | PercentRank |
| --- | --- | --- | --- |
| Banana |300 |Образ — |0 |
| Cherry |300 |Образ — |0 |
| Durian |500 |Образ — |1 |
| Apple |100 |Web |0 |
| Fig |200 |Web |0,2 |
| Papaya |200 |Web |0,2 |
| Fig |300 |Web |0,6 |
| Cherry |400 |Web |0,8 |
| Durian |500 |Web |1 |

### <a name="percentilecont--percentiledisc"></a>Функции PERCENTILE_CONT и PERCENTILE_DISC
Эти две функции вычисляют процентиль на основе непрерывного или дискретного распределения значений в столбце.

**Синтаксис**

    [PERCENTILE_CONT | PERCENTILE_DISC] ( numeric_literal ) 
        WITHIN GROUP ( ORDER BY <identifier> [ ASC | DESC ] )
        OVER ( [ PARTITION BY <identifier,>…[n] ] ) AS <alias>

**numeric_literal** — вычисляемый процентиль. Значение должно быть в диапазоне от 0,0 до 1,0.

    WITHIN GROUP (ORDER BY <identifier> [ ASC | DESC ])

Указывает список числовых значений, по которым выполняется сортировка и вычисление процентиля. Допускается только один идентификатор столбца. Выражение должно возвращать числовое значение. Другие типы данных не допускаются. По умолчанию значения сортируются по возрастанию.

    OVER ([ PARTITION BY <identifier,>…[n] ] )

Делит входной набор строк на секции (на основе ключей секций), к которым применяется функция вычисления процентиля. Дополнительные сведения см. в этой статье в разделе «Ранжирующие функции».
Примечание. Все значения NULL в наборе данных игнорируются.

**PERCENTILE_CONT** вычисляет процентиль на основе непрерывного распределения значений в столбце. Результат интерполируется и не может быть равен какому-либо конкретному значению в столбце. 

**PERCENTILE_DISC** вычисляет процентиль на основе дискретного распределения значений в столбце. Результат равен конкретному значению в столбце. Другими словами, функция PERCENTILE_DISC, в отличие от PERCENTILE_CONT, всегда возвращает фактическое (исходное входное) значение.

В приведенном ниже примере определяется медиана (процентиль равен 0,50) задержки в пределах каждой вертикали. На этом примере можно понять, как работают обе функции.

    @result = 
        SELECT 
            Vertical, 
            Query,
            PERCENTILE_CONT(0.5) 
                WITHIN GROUP (ORDER BY Latency)
                OVER ( PARTITION BY Vertical ) AS PercentileCont50,
            PERCENTILE_DISC(0.5) 
                WITHIN GROUP (ORDER BY Latency) 
                OVER ( PARTITION BY Vertical ) AS PercentileDisc50 

        FROM @querylog;

Получаются такие результаты:

| Запрос | Задержка: INT | Vertical | PercentileCont50 | PercentilDisc50 |
| --- | --- | --- | --- | --- |
| Banana |300 |Образ — |300 |300 |
| Cherry |300 |Образ — |300 |300 |
| Durian |500 |Образ — |300 |300 |
| Apple |100 |Web |250 |200 |
| Fig |200 |Web |250 |200 |
| Papaya |200 |Web |250 |200 |
| Fig |300 |Web |250 |200 |
| Cherry |400 |Web |250 |200 |
| Durian |500 |Web |250 |200 |

В случае с PERCENTILE_CONT значения могут интерполироваться, поэтому медиана для вертикали Web будет равна 250, даже если в этой вертикали нет запроса с задержкой 250. 

PERCENTILE_DISC не интерполирует значения, поэтому медиана для вертикали Web будет равна 200, что есть одним из фактических значений входных строк.

## <a name="see-also"></a>См. также
* [Разработка скриптов U-SQL с помощью средств озера данных для Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)
* [Использование интерактивных учебников по аналитике озера данных Azure](data-lake-analytics-use-interactive-tutorials.md)
* [Начало работы с языком U-SQL в службе аналитики озера данных Azure.](data-lake-analytics-u-sql-get-started.md)




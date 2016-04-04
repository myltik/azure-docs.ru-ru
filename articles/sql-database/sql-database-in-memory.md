<properties
	pageTitle="Приступая к работе с SQL In-Memory | Microsoft Azure"
	description="Технологии SQL In-Memory значительно повышают производительность транзакций и аналитических операций. Узнайте, как воспользоваться преимуществами этих технологий."
	services="sql-database"
	documentationCenter=""
	authors="jodebrui"
	manager="jhubbard"
	editor=""/>


<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/17/2016"
	ms.author="jodebrui"/>


# Приступая к работе с In-Memory (в режиме предварительной версии) в базе данных SQL

Компоненты In-Memory значительно повышают производительность транзакций и аналитических операций, когда это требуется.

В этой статье рассматриваются два демонстрационных примера: использование компонента In-Memory OLTP и компонента In-Memory Analytics. Каждая демонстрация содержит инструкции и код, необходимые для ее запуска. Вы можете выбрать один из таких вариантов:

- использовать код для тестирования вариантов, чтобы оценить разницу в производительности;
- прочитать код, чтобы понять сценарий и узнать, как создавать и использовать объекты In-Memory.

> [AZURE.VIDEO azure-sql-database-in-memory-technologies]

- [Quick Start 1: In-Memory OLTP Technologies for Faster Transact-SQL Performance](http://msdn.microsoft.com/library/mt694156.aspx) — еще одна статья, которая поможет вам приступить к работе.

#### In-Memory OLTP;

Компоненты In-Memory [OLTP](#install_oltp_manuallink) (оперативная обработка транзакций) перечислены ниже.

- Оптимизированные для памяти таблицы.
- Скомпилированные в собственном коде хранимые процедуры.


Кроме стандартного представления на жестком диске, оптимизированная для памяти таблица представлена в активной памяти. Бизнес-транзакции в таблице выполняются быстрее, так как они напрямую взаимодействуют с представлением в активной памяти.

Компонент In-Memory OLTP позволяет достичь 30-кратного увеличения пропускной способности транзакций (в зависимости от рабочей нагрузки).


Скомпилированные в собственном коде хранимые процедуры требуют меньше машинных инструкций во время выполнения, чем традиционные интерпретируемые хранимые процедуры. Мы увидели результат компиляции в собственном коде с интервалами, которые равны 1/100 интерпретируемой длительности.


#### In-Memory Analytics 

Компонентами In-Memory [Analytics](#install_analytics_manuallink) являются:

- Индексы columnstore


Индекс columnstore повышает производительность рабочих нагрузок запросов за счет необычного сжатия данных.

В других службах индексы columnstore обязательно оптимизированы для памяти. Однако в базе данных SQL Azure индекс columnstore может существовать на жестком диске вместе с обычной таблицей, которую он индексирует.


#### Real-Time Analytics

В [Real-Time Analytics](http://msdn.microsoft.com/library/dn817827.aspx) сочетание компонентов In-Memory OLTP и In-Memory Analytics дает следующие возможности:

- Анализ бизнес-данных на основе оперативных данных.


#### Доступность


Общая доступность:

- [Индексы columnstore](http://msdn.microsoft.com/library/dn817827.aspx), которые находятся *на диске*.


Предварительный просмотр:

- In-Memory OLTP;
- In-Memory Analytics с индексами columnstore, оптимизированными для памяти;
- Real-Time Operational Analytics.


Рекомендации по использованию компонентов In-Memory в предварительной версии описаны [далее в этой статье](#preview_considerations_for_in_memory).


> [AZURE.NOTE] Эти компоненты в режиме предварительной версии доступны только для баз данных SQL Azure уровня [*Премиум*](sql-database-service-tiers.md). В базах данных уровня служб «Стандартный» или «Базовый» эти компоненты отсутствуют.



<a id="install_oltp_manuallink" name="install_oltp_manuallink"></a>

&nbsp;

## О. Установка образца In-Memory OLTP

На [портале Azure](https://portal.azure.com/) вы можете быстро и просто создать образец базы данных AdventureWorksLT [V12]. Затем в этом разделе объясняется, как можно расширить базу данных AdventureWorksLT, используя следующие компоненты.

- Таблицы In-Memory.
- Скомпилированные в собственном коде хранимые процедуры.


#### Шаги установки

1. На [портале Azure](https://portal.azure.com/) создайте базу данных категории «Премиум» на сервере V12. Укажите **источник** для примера базы данных AdventureWorksLT [V12].
 - Подробные инструкции см. в статье [Создание первой базы данных Azure SQL](sql-database-get-started.md).

2. Подключитесь к базе данных с помощью SQL Server Management Studio [(SSMS.exe)](http://msdn.microsoft.com/library/mt238290.aspx).

3. Скопируйте в буфер обмена [скрипт Transact-SQL для In-Memory OLTP](http://raw.githubusercontent.com/Azure/azure-sql-database-samples/master/T-SQL/In-Memory/sql_in-memory_oltp_sample.sql).
 - Этот скрипт создаст необходимые объекты In-Memory в образце базы данных AdventureWorksLT, созданной на этапе 1.

4. Вставьте скрипт T-SQL в SSMS-файл, а затем выполните его.
 - Как и в блоке ниже, ключевым является предложение `MEMORY_OPTIMIZED = ON` инструкции CREATE TABLE:


```
CREATE TABLE [SalesLT].[SalesOrderHeader_inmem](
	[SalesOrderID] int IDENTITY NOT NULL PRIMARY KEY NONCLUSTERED ...,
	...
) WITH (MEMORY_OPTIMIZED = ON);
```


#### Ошибка 40536


При запуске скрипта T-SQL может появиться ошибка 40536. В таком случае выполните следующий скрипт T-SQL, чтобы проверить, поддерживает ли база данных компонент In-Memory:


```
SELECT DatabasePropertyEx(DB_Name(), 'IsXTPSupported');
```


Значение **0** указывает на то, что In-Memory не поддерживается, а значение 1 — что поддерживается. Чтобы диагностировать проблему, выполните следующие действия:

- Убедитесь, что база данных создана после того, как компонент In-Memory OLTP стал активным для предварительного просмотра.
- Убедитесь, что для базы данных выбран уровень служб «Премиум».


#### Сведения о созданных элементах, оптимизированных для памяти

**Таблицы** — пример содержит следующие оптимизированные для памяти таблицы:

- SalesLT.Product\_inmem
- SalesLT.SalesOrderHeader\_inmem
- SalesLT.SalesOrderDetail\_inmem
- Demo.DemoSalesOrderHeaderSeed
- Demo.DemoSalesOrderDetailSeed


В SSMS-файле оптимизированные для памяти таблицы можно проверить с помощью **обозревателя объектов** следующим образом.

- Щелкните правой кнопкой мыши **Таблицы** > **Фильтры** > **Параметры фильтров** > **Оптимизация для памяти** и задайте значение 1.


Или можно отправить запрос представлений каталога:


```
SELECT is_memory_optimized, name, type_desc, durability_desc
	FROM sys.tables
	WHERE is_memory_optimized = 1;
```


**Скомпилированная в собственном коде хранимая процедура** — процедуру SalesLT.usp\_InsertSalesOrder\_inmem тоже можно проверить с помощью запроса представления каталога:


```
SELECT uses_native_compilation, OBJECT_NAME(object_id), definition
	FROM sys.sql_modules
	WHERE uses_native_compilation = 1;
```


&nbsp;

## Запуск образца рабочей нагрузки OLTP

Единственное различие между двумя следующими *хранимыми процедурами* состоит в том, что первая процедура использует оптимизированные для памяти версии таблиц, а вторая — обычные таблицы на диске:

- SalesLT**.**usp\_InsertSalesOrder**\_inmem**
- SalesLT**.**usp\_InsertSalesOrder**\_ondisk**.


В этом разделе описано, как с помощью удобной служебной программы **ostress.exe** можно выполнить две хранимые процедуры в режиме нагрузочного теста. При этом вы можете сравнить время выполнения этих нагрузочных тестов.


При запуске программы ostress.exe рекомендуется передавать значения параметров:

- -n100 — для выполнения большого количества одновременных подключений;
- -r500 — для многократного (сотни раз) выполнения каждого цикла подключения.


Тем не менее проверку работоспособности можно выполнить и с помощью гораздо меньших значений, например -n10 и -r50.


### Скрипт для ostress.exe


В этом разделе приведен скрипт T-SQL, внедренный в командную строку ostress.exe. Этот скрипт использует элементы, созданные ранее с помощью установленного скрипта T-SQL.


Следующий скрипт вставляет образец заказа на продажу с пятью позициями строки в следующие оптимизированные для памяти *таблицы*:

- SalesLT.SalesOrderHeader\_inmem
- SalesLT.SalesOrderDetail\_inmem


```
DECLARE
	@i int = 0,
	@od SalesLT.SalesOrderDetailType_inmem,
	@SalesOrderID int,
	@DueDate datetime2 = sysdatetime(),
	@CustomerID int = rand() * 8000,
	@BillToAddressID int = rand() * 10000,
	@ShipToAddressID int = rand() * 10000;
	
INSERT INTO @od
	SELECT OrderQty, ProductID
	FROM Demo.DemoSalesOrderDetailSeed
	WHERE OrderID= cast((rand()*60) as int);
	
WHILE (@i < 20)
begin;
	EXECUTE SalesLT.usp_InsertSalesOrder_inmem @SalesOrderID OUTPUT,
		@DueDate, @CustomerID, @BillToAddressID, @ShipToAddressID, @od;
	SET @i = @i + 1;
end
```


Чтобы создать версию предыдущего скрипта T-SQL для ostress.exe на диске (\_ondisk), просто измените оба вхождения подстроки*\_inmem* на *\_ondisk*. Эти замены влияют на имена таблиц и хранимых процедур.


### Установка служебных программ RML и ostress


В идеале вам следует запланировать запуск ostress.exe на виртуальной машине Azure. Вам нужно создать [виртуальную машину Azure](https://azure.microsoft.com/documentation/services/virtual-machines/) в географическом регионе Azure, в котором находится база данных AdventureWorksLT. Но вы также можете запустить программу ostress.exe и на переносном компьютере.


На виртуальной машине (или в другом размещении) установите служебные программы (Replay Markup Language), которые включают ostress.exe.

- См. описание программы ostress.exe в статье [Пример базы данных для In-Memory OLTP](http://msdn.microsoft.com/library/mt465764.aspx).
 - Или см. статью [Образец базы данных для In-Memory OLTP](http://msdn.microsoft.com/library/mt465764.aspx).
 - Или см. [публикацию блога, посвященную установке ostress.exe](http://blogs.msdn.com/b/psssql/archive/2013/10/29/cumulative-update-2-to-the-rml-utilities-for-microsoft-sql-server-released.aspx).



<!--
dn511655.aspx is for SQL 2014,
[Extensions to AdventureWorks to Demonstrate In-Memory OLTP]
(http://msdn.microsoft.com/library/dn511655&#x28;v=sql.120&#x29;.aspx)

whereas for SQL 2016+
[Sample Database for In-Memory OLTP]
(http://msdn.microsoft.com/library/mt465764.aspx)
-->



### Сначала — запуск тестовой рабочей нагрузки \_inmem


Вы можете использовать окно *командной строки RML*, чтобы запустить командную строку ostress.exe. Параметры командной строки указывают программе ostress выполнять следующие действия:

- параллельно выполнять 100 подключений (-n100);
- заставлять каждое подключение запускать сценарий T-SQL 50 раз (-r50).


```
ostress.exe -n100 -r50 -S<servername>.database.windows.net -U<login> -P<password> -d<database> -q -Q"DECLARE @i int = 0, @od SalesLT.SalesOrderDetailType_inmem, @SalesOrderID int, @DueDate datetime2 = sysdatetime(), @CustomerID int = rand() * 8000, @BillToAddressID int = rand() * 10000, @ShipToAddressID int = rand()* 10000; INSERT INTO @od SELECT OrderQty, ProductID FROM Demo.DemoSalesOrderDetailSeed WHERE OrderID= cast((rand()*60) as int); WHILE (@i < 20) begin; EXECUTE SalesLT.usp_InsertSalesOrder_inmem @SalesOrderID OUTPUT, @DueDate, @CustomerID, @BillToAddressID, @ShipToAddressID, @od; set @i += 1; end"
```


Выполнить предыдущую команду ostress.exe можно так.


1. Чтобы удалить все данные, вставленные в ходе предыдущих запусков, сбросьте содержимое базы данных, выполнив следующую команду в SSMS.
```
EXECUTE Demo.usp_DemoReset;
```

2. Скопируйте текст предыдущей командной строки ostress.exe в буфер обмена.

3. Замените <placeholders> для параметров -S, -U, -P, и -d правильными фактическими значениями.

4. В окне командной строки RML запустите измененную командную строку.


#### Результат — это длительность выполнения теста


При завершении программа ostress.exe записывает значение длительности выполнения в качестве последней строки выходных данных в окне командной строки RML. Например, более короткий тестовый запуск длится около 1,5 минут:

`11/12/15 00:35:00.873 [0x000030A8] OSTRESS exiting normally, elapsed time: 00:01:31.867`


#### Сброс базы данных, изменение значения \_ondisk и повторный запуск


Получив результат выполнения \_inmem, выполните следующие действия для запуска \_ondisk:


1. Выполните сброс базы данных, запустив следующую команду в SSMS. Она удалит все данные, вставленные в ходе предыдущего запуска.
```
EXECUTE Demo.usp_DemoReset;
```

2. Измените командную строку ostress.exe, изменив все вхождения *\_inmem* на *\_ondisk*.

3. Перезапустите ostress.exe еще раз и запишите результат (длительность выполнения).

4. Еще раз выполните сброс базы данных, чтобы корректно удалить большой объем тестовых данных.


#### Ожидаемые результаты сравнения

Наши тесты с использованием In-Memory демонстрируют **9-кратное** улучшение производительности для упрощенных рабочих нагрузок. При этом программа ostress выполняется на виртуальной машине Azure, расположенной в том же регионе Azure, что и база данных.



<a id="install_analytics_manuallink" name="install_analytics_manuallink"></a>

&nbsp;


## B. Установка образца In-Memory Analytics


В этом разделе вы сравните результаты ввода-вывода и статистические данные при использовании индекса columnstore и обычного индекса.


Хотя индексы columnstore логически соответствуют обычным индексам, физически они отличаются. Индекс columnstore позволяет определенным образом организовать данные, приводя к их значительному сжатию. Это ощутимо повышает производительность.


Для анализа в режиме реального времени с использованием рабочей нагрузки OLTP зачастую лучше использовать НЕкластеризованный индекс columnstore. Подробные сведения см. в статье [Описание индексов columnstore](http://msdn.microsoft.com/library/gg492088.aspx).



### Подготовка тестирования аналитики с помощью columnstore


1. С помощью портала Azure создайте из образца новую базу данных AdventureWorksLT.
 - Используйте такое же имя.
 - Выберите любой уровень служб категории «Премиум».

2. Скопируйте [sql\_inmemory\_analytics\_sample](http://raw.githubusercontent.com/Azure/azure-sql-database-samples/master/T-SQL/In-Memory/sql_in-memory_analytics_sample.sql) в буфер обмена.
 - Этот скрипт создаст необходимые объекты In-Memory в образце базы данных AdventureWorksLT, созданной на этапе 1.
 - Скрипт создает таблицу измерений и две таблицы фактов. Таблицы фактов заполняются 3,5 млн строк.
 - Выполнение скрипта может занять до 15 минут.

3. Вставьте скрипт T-SQL в SSMS-файл, а затем выполните его.
 - Важным является ключевое слово **COLUMNSTORE** в инструкции **CREATE INDEX**, как и в:<br/>`CREATE NONCLUSTERED COLUMNSTORE INDEX ...;`

4. Задайте для базы данных AdventureWorksLT уровень совместимости 130:<br/>`ALTER DATABASE AdventureworksLT SET compatibility_level = 130;`
 - Уровень 130 не имеет прямого отношения к компонентам In-Memory. При этом уровень 130 обычно обеспечивает более высокую скорость обработки запросов, чем уровень 120.


#### Ключевые таблицы и индексы columnstore


- dbo.FactResellerSalesXL\_CCI — это таблица, включающая кластеризованный индекс **columnstore** с дополнительными возможностями сжатия на уровне *данных*.

- dbo.FactResellerSalesXL\_PageCompressed — это таблица с эквивалентным обычным кластеризованным индексом, который сжат только на уровне *страницы*.


#### Критические запросы для сравнения с индексом columnstore


[Здесь](http://raw.githubusercontent.com/Azure/azure-sql-database-samples/master/T-SQL/In-Memory/clustered_columnstore_sample_queries.sql) представлено несколько типов запросов T-SQL, которые можно выполнить для анализа повышения производительности. На этапе 2 в скрипте T-SQL описано два интересующих нас запроса. Эти запросы отличаются только одной строкой:


- `FROM FactResellerSalesXL_PageCompressed a`
- `FROM FactResellerSalesXL_CCI a`


Кластеризованный индекс columnstore включен в таблицу FactResellerSalesXL**\_CCI**.

Следующий фрагмент скрипта T-SQL отображает статистику для ввода-вывода, а также время выполнения запроса (TIME) для каждой таблицы.


```
/*********************************************************************
Step 2 -- Overview
-- Page Compressed BTree table v/s Columnstore table performance differences
-- Enable actual Query Plan in order to see Plan differences when Executing
*/
-- Ensure Database is in 130 compatibility mode
ALTER DATABASE AdventureworksLT SET compatibility_level = 130
GO

-- Execute a typical query that joins the Fact Table with dimension tables
-- Note this query will run on the Page Compressed table, Note down the time
SET STATISTICS IO ON
SET STATISTICS TIME ON
GO

SELECT c.Year
	,e.ProductCategoryKey
	,FirstName + ' ' + LastName AS FullName
	,count(SalesOrderNumber) AS NumSales
	,sum(SalesAmount) AS TotalSalesAmt
	,Avg(SalesAmount) AS AvgSalesAmt
	,count(DISTINCT SalesOrderNumber) AS NumOrders
	,count(DISTINCT a.CustomerKey) AS CountCustomers
FROM FactResellerSalesXL_PageCompressed a
INNER JOIN DimProduct b ON b.ProductKey = a.ProductKey
INNER JOIN DimCustomer d ON d.CustomerKey = a.CustomerKey
Inner JOIN DimProductSubCategory e on e.ProductSubcategoryKey = b.ProductSubcategoryKey
INNER JOIN DimDate c ON c.DateKey = a.OrderDateKey
WHERE e.ProductCategoryKey =2
	AND c.FullDateAlternateKey BETWEEN '1/1/2014' AND '1/1/2015'
GROUP BY e.ProductCategoryKey,c.Year,d.CustomerKey,d.FirstName,d.LastName
GO
SET STATISTICS IO OFF
SET STATISTICS TIME OFF
GO


-- This is the same Prior query on a table with a Clustered Columnstore index CCI 
-- The comparison numbers are even more dramatic the larger the table is, this is a 11 million row table only.
SET STATISTICS IO ON
SET STATISTICS TIME ON
GO
SELECT c.Year
	,e.ProductCategoryKey
	,FirstName + ' ' + LastName AS FullName
	,count(SalesOrderNumber) AS NumSales
	,sum(SalesAmount) AS TotalSalesAmt
	,Avg(SalesAmount) AS AvgSalesAmt
	,count(DISTINCT SalesOrderNumber) AS NumOrders
	,count(DISTINCT a.CustomerKey) AS CountCustomers
FROM FactResellerSalesXL_CCI a
INNER JOIN DimProduct b ON b.ProductKey = a.ProductKey
INNER JOIN DimCustomer d ON d.CustomerKey = a.CustomerKey
Inner JOIN DimProductSubCategory e on e.ProductSubcategoryKey = b.ProductSubcategoryKey
INNER JOIN DimDate c ON c.DateKey = a.OrderDateKey
WHERE e.ProductCategoryKey =2
	AND c.FullDateAlternateKey BETWEEN '1/1/2014' AND '1/1/2015'
GROUP BY e.ProductCategoryKey,c.Year,d.CustomerKey,d.FirstName,d.LastName
GO

SET STATISTICS IO OFF
SET STATISTICS TIME OFF
GO
```



<a id="preview_considerations_for_in_memory" name="preview_considerations_for_in_memory"></a>


## Рекомендации по использованию In-Memory OLTP в режиме предварительной версии


Компоненты In-Memory OLTP в базе данных SQL Azure [доступны для использования в режиме предварительной версии с 28 октября 2015 г.](https://azure.microsoft.com/updates/public-preview-in-memory-oltp-and-real-time-operational-analytics-for-azure-sql-database/)


Компонент In-Memory OLTP поддерживается в режиме предварительной версии (до выпуска в режиме общедоступной версии) только для следующих элементов.

- Базы данных уровня служб *Премиум*.

- Базы данных, созданные после того, как компоненты In-Memory OLTP стали активными.
 - Новая база данных не может поддерживать In-Memory OLTP, если она восстановлена из базы данных, созданной до того, как компоненты In-Memory OLTP стали активными.


Проверить, поддерживает ли ваша база данных компонент In-Memory, можно с помощью инструкции T-SQL SELECT. Значение **1** означает, что база данных поддерживает In-Memory OLTP:

```
SELECT DatabasePropertyEx(DB_NAME(), 'IsXTPSupported');
```


Если запрос возвращает **1**, это означает, что In-Memory OLTP поддерживается как базой данных, так и всеми ее копиями и восстановленными базами данных, созданными на основе этой базы данных.


#### Объекты, которые поддерживаются только на уровне служб «Премиум»


Если база данных содержит любые из следующих видов объектов или типов In-Memory OLTP, изменить уровень служб базы данных с «Премиум» на «Базовый» или «Стандартный» нельзя. Чтобы изменить уровень служб базы данных, сначала удалите эти объекты.

- Оптимизированные для памяти таблицы
- Типы оптимизированных для памяти таблиц.
- Скомпилированные в собственном коде модули


#### Другие связи


- Предварительную версию компонента In-Memory OLTP нельзя использовать с базами данных, входящими в пул эластичных баз данных. Мы планируем включить эту возможность в будущем.

- Использование In-Memory OLTP в хранилище данных SQL не поддерживается.
 - Индекс columnstore, доступный в рамках компонента In-Memory Analytics, поддерживается в хранилище данных SQL.

- При использовании с предварительной версией хранилище запросов не отслеживает запросы внутри скомпилированных в собственном коде модулей. Мы планируем включить эту возможность в будущем.

- Некоторые функции языка Transact-SQL не поддерживаются в In-Memory OLTP. Это относится как к Microsoft SQL Server, так и к базе данных SQL Azure. Дополнительные сведения см. в разделе
 - [Поддержка Transact-SQL для OLTP в памяти](http://msdn.microsoft.com/library/dn133180.aspx)
 - [Конструкции языка Transact-SQL не поддерживаются компонентом In-Memory OLTP](http://msdn.microsoft.com/library/dn246937.aspx)


## Дальнейшие действия


- Попробуйте [использовать компонент In-Memory OLTP в существующем приложении SQL Azure](sql-database-in-memory-oltp-migration.md).


## Дополнительные ресурсы

#### Подробные сведения

- [Дополнительные сведения о компоненте In-Memory OLTP, актуальные для Microsoft SQL Server и базы данных SQL Azure.](http://msdn.microsoft.com/library/dn133186.aspx)

- [Сведения о технологии Real-Time Operational в библиотеке MSDN.](http://msdn.microsoft.com/library/dn817827.aspx)

- Технический документ с [рекомендациями по распространенным шаблонам рабочих нагрузок и миграции](http://msdn.microsoft.com/library/dn673538.aspx) включает описание шаблонов рабочих нагрузок, для которых In-Memory OLTP обычно обеспечивает значительное повышение производительности.

#### Проектирование приложений

- [In-Memory OLTP (оптимизация в памяти)](http://msdn.microsoft.com/library/dn133186.aspx)

- [Используйте In-Memory OLTP в существующем приложении SQL Azure.](sql-database-in-memory-oltp-migration.md)

#### Средства

- [Предварительная версия SQL Server Data Tools (SSDT)](http://msdn.microsoft.com/library/mt204009.aspx) — последняя версия (выпускается ежемесячно).

- [Описание служебных программ RML (Replay Markup Language) для SQL Server.](http://support.microsoft.com/ru-RU/kb/944837)

- [Мониторинг хранилища In-Memory](sql-database-in-memory-oltp-monitoring.md) для компонента In-Memory OLTP.

<!---HONumber=AcomDC_0323_2016-->
<properties
	pageTitle="Начало работы с SQL In-Memory | Microsoft Azure"
	description="Технологии SQL In-Memory значительно повышают производительность транзакций и аналитических операций. Узнайте, как воспользоваться преимуществами этих технологий."
	services="sql-database"
	documentationCenter=""
	authors="jodebrui"
	manager="jeffreyg"
	editor=""/>


<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="10/28/2015"
	ms.author="jodebrui"/>


# Начало работы с In-Memory (предварительная версия)


Технологии SQL In-Memory значительно повышают производительность транзакций и аналитических операций. Благодаря In-Memory OLTP можно повысить пропускную способность транзакций в 30 раз, а с помощью In-Memory Analytics — увеличить производительность запросов в 100 раз (в зависимости от рабочей нагрузки).

Технология In-Memory Analytics является общедоступной. Технология In-Memory OLTP для баз данных SQL Azure (цен. категория «Премиум») доступна в предварительной версии.


## Приступая к работе

Попробуйте использовать In-Memory OLTP для выполнения транзакций.


- [Добавьте образцы In-Memory OLTP в базу данных AdventureWorksLT](#Enrich-an-AdventureWorksLT-database-with-In-Memory-OLTP-samples).
- [Используйте In-Memory OLTP в существующем приложении SQL Azure.](sql-database-in-memory-oltp-migration.md)
- [Отслеживайте хранилище In-Memory](sql-database-in-memory-oltp-monitoring.md).


Попробуйте использовать In-Memory Analytics для выполнения транзакций.

- [Установите In-Memory Analytics](#Install-the-In-Memory-Analytics-sample).
- Узнайте больше об [индексах Columnstore](https://msdn.microsoft.com/library/gg492088.aspx) в библиотеке MSDN.


## Добавление образцов In-Memory OLTP в базу данных AdventureWorksLT

Вы можете несколькими щелчками создать образец базы данных AdventureWorksLT [V12] на портале предварительной версии Azure. Далее объясняется, как обогатить базу данных AdventureWorksLT с помощью таблиц и хранимых процедур, скомпилированных в собственном коде, и приводятся примеры объектов In-Memory OLTP.


1. На [портале предварительной версии Azure](https://portal.azure.com/) создайте базу данных категории «Премиум» на сервере V12. Задайте источник в образце базы данных AdventureWorksLT [V12].
 - Более подробные инструкции для этого этапа см. [в этой статье](sql-database-get-started.md).

2. Подключитесь к базе данных с помощью [SQL Server Management Studio (SSMS.exe)](https://msdn.microsoft.com/library/mt238290.aspx) или другой подобной программы.

3. Скопируйте [сценарий In-Memory OLTP](https://raw.githubusercontent.com/Azure/azure-sql-database-samples/master/T-SQL/In-Memory/sql_in-memory_oltp_sample.sql) в буфер обмена.
 - Этот сценарий создаст необходимые объекты In-Memory в образце базы данных AdventureWorksLT, созданной на этапе 1.

4. Вставьте сценарий Transact-SQL в файл SSMS.exe и выполните сценарий.



&nbsp;

Образец содержит следующие таблицы, оптимизированные для памяти:

- SalesLT.Product\_inmem
- SalesLT.SalesOrderHeader\_inmem
- SalesLT.SalesOrderDetail\_inmem
- Demo.DemoSalesOrderHeaderSeed
- Demo.DemoSalesOrderDetailSeed

Проверьте оптимизированные для памяти таблицы с помощью обозревателя объектов или запросов представления каталога.

Пример:


```
		SELECT name, object_id, type, type_desc, is_memory_optimized, durability, durability_desc
		FROM sys.tables
	WHERE is_memory_optimized=1;
```


Скомпилированную в собственном коде хранимую процедуру SalesLT.usp\_InsertSalesOrder\_inmem тоже можно проверить с помощью обозревателя объектов или запросов в представлении каталога.

Пример:


```
		SELECT object_name(object_id), object_id, definition, uses_native_compilation
		FROM sys.sql_modules
	WHERE uses_native_compilation=1;
```


## Запуск пробной рабочей нагрузки

Используйте хранимые процедуры SalesLT.usp\_InsertSalesOrder\_inmem и SalesLT.usp\_InsertSalesOrder\_ondisk, чтобы сравнить производительность операций вставки в оптимизированных для памяти и дисковых таблицах.

Рекомендуем запускать рабочую нагрузку с использованием нескольких одновременных подключений клиентов из приложения, размещенного в том же регионе Azure, что и образец базы данных.

### Вставка образца заказа на продажу

Следующий сценарий вставляет образец заказа на продажу с пятью позициями строк в оптимизированных для памяти таблицах SalesLT.SalesOrderHeader\_inmem и SalesLT.SalesOrderDetail\_inmem:


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

EXECUTE SalesLT.usp_InsertSalesOrder_inmem @SalesOrderID OUTPUT, @DueDate,
			@CustomerID, @BillToAddressID, @ShipToAddressID, @od;
```


### Запуск пробной стрессовой рабочей нагрузки

Чтобы запустить пробную стрессовую рабочую нагрузку, создайте [виртуальную машину Azure](https://azure.microsoft.com/documentation/services/virtual-machines/) в регионе, в котором размещен образец базы данных. Для запуска рабочей нагрузки используйте программу командной строки ostress. Инструкции по [установке и запуску ostress](https://msdn.microsoft.com/library/dn511655&#x28;v=sql.120&#x29;.aspx) можно найти на сайте MSDN.

При запуске из командной строки RML следующая команда добавляет миллион заказов на продажу по пять позиций строки в каждом (в оптимизированных для памяти таблицах), используя 100 параллельных подключений.


```
		ostress.exe –n100 –r500 –S<servername>.database.windows.net -U<login> -P<password>
		 -d<database> -q -Q"DECLARE @i int = 0, @od SalesLT.SalesOrderDetailType_inmem,
		@SalesOrderID int, @DueDate datetime2 = sysdatetime(), @CustomerID int = rand() *
		8000, @BillToAddressID int = rand() * 10000, @ShipToAddressID int = rand()*  
		10000; INSERT INTO @od SELECT OrderQty, ProductID FROM
		Demo.DemoSalesOrderDetailSeed WHERE OrderID= cast((rand()*60) as int); while (@i <
		 20) begin; EXEC SalesLT.usp_InsertSalesOrder_inmem @SalesOrderID OUTPUT, @DueDate,
		 @CustomerID, @BillToAddressID, @ShipToAddressID, @od; set @i += 1 end"
```


Обязательно замените <servername> именем вашего сервера, <database> — именем базы данных, а <login> и <password> — данными для входа.

Чтобы сравнить производительность операции вставки в оптимизированных для памяти таблицах и обычных дисковых таблицах, вставьте один миллион заказов на продажу в дисковые таблицы с помощью следующей команды:


```
		ostress.exe –n100 –r500 –S<servername>.database.windows.net -U<login> -P<password>
		-d<database> -q -Q"DECLARE @i int = 0, @od SalesLT.SalesOrderDetailType_ondisk,
		@SalesOrderID int, @DueDate datetime2 = sysdatetime(), @CustomerID int = rand() *
		8000, @BillToAddressID int = rand() * 10000, @ShipToAddressID int = rand() *
		10000; INSERT INTO @od SELECT OrderQty, ProductID FROM
		Demo.DemoSalesOrderDetailSeed with (snapshot) WHERE OrderID= cast((rand()*60) as
		int); while (@i < 20) begin; EXEC SalesLT.usp_InsertSalesOrder_ondisk
		@SalesOrderID OUTPUT, @DueDate, @CustomerID, @BillToAddressID, @ShipToAddressID,
		@od; set @i += 1 end"
```


Согласно тестам улучшение производительности при выполнении этой операции с помощью таблиц, оптимизированных для памяти, примерно в 9 раз выше по сравнению с производительностью при использовании дисковых таблиц. При этом программа ostress выполнялась на виртуальной машине, расположенной в том же регионе Azure, что и база данных.

После каждого запуска тестирования не забудьте сбрасывать образец, чтобы не исчерпать свободное место в памяти. Выполните следующую инструкцию T-SQL в базе данных. Одна тестовая вставка одного миллиона заказов на продажу создает более 500 МБ данных в таблицах, оптимизированных для памяти.


```
EXECUTE Demo.usp_DemoReset;
```


## Установка образца In-Memory Analytics

**Сначала** создайте новую базу данных SQL Azure.

- Выберите любой выпуск категории «Премиум» (требуется для Columnstore).
- Убедитесь, что база данных создана из образца.

- Для простоты присвойте базе данных имя AdventureworksLT.



**Далее** подключитесь к базе данных SQL Azure через [SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx) (предварительная версия за сентябрь 2015 года или более поздняя версия).


- Выполните сценарий настройки [sql\_in memory\_analytics\_sample](https://raw.githubusercontent.com/Azure/azure-sql-database-samples/master/T-SQL/In-Memory/sql_in-memory_analytics_sample.sql).


- Создайте схему, необходимую для таблиц измерений и фактов. Для этого выполните сценарий. Сценарий создаст 2 таблицы фактов, каждая из которых будет содержать примерно 3,5 млн строк.


- FactResellerSales\_CCI с таблицей columnstore.


- FactResellerSalesXL\_PageCompressed — эквивалент таблицы сбалансированного дерева со сжатием страниц. **Примечание.** Выполнение этого сценария и создание данных может занять до 15 минут.


**Выполните** демонстрационные запросы в файле [clustered\_columnstore\_sample\_queries.sql](https://raw.githubusercontent.com/Azure/azure-sql-database-samples/master/T-SQL/In-Memory/clustered_columnstore_sample_queries.sql) и изучите этот компонент.

## Дополнительные сведения о технологии In-Memory OLTP

[In-Memory OLTP (оптимизация в памяти)](https://msdn.microsoft.com/library/dn133186.aspx)

[Технический документ по распространенным шаблонам рабочих нагрузок и вопросам миграции](https://msdn.microsoft.com/library/dn673538.aspx), в котором описываются шаблоны рабочих нагрузок, для которых In-Memory OLTP обычно обеспечивает значительное повышение производительности.

## Рекомендации по предварительной версии

In-Memory OLTP поддерживается **только** в базах данных категории «Премиум».

In-Memory OLTP поддерживается только в новых базах данных. Эта технология не поддерживается в базах данных, созданных на основе существующей базы данных с помощью функций копирования или восстановления. Однако после создания новой базы данных ее можно скопировать или восстановить, сохранив полный набор функций In-Memory OLTP.

Чтобы проверить, поддерживается ли In-Memory OLTP в той или иной базе данных, выполните следующий запрос:


```
SELECT DATABASEPROPERTYEX(DB_NAME(), 'IsXTPSupported');
```


Если запрос возвращает **1**, In-Memory OLTP поддерживается в этой базе данных, а также во всех ее копиях и восстановленных базах данных, созданных на основе этой базы данных.

Если база данных содержит любые из следующих видов объектов или типов, изменить уровень обслуживания базы данных на базовый или стандартный нельзя. Чтобы понизить уровень обслуживания базы данных, необходимо сначала удалить такие объекты:

- Оптимизированные для памяти таблицы
- Типы оптимизированных для памяти таблиц.
- Скомпилированные в собственном коде модули, использующие In-Memory OLTP с базами данных в пулах эластичных БД, не поддерживаются.

Использование In-Memory OLTP в хранилище данных SQL не поддерживается.

Хранилище запросов не отслеживает запросы внутри скомпилированных в собственном коде модулей.

Некоторые функции языка Transact-SQL не поддерживаются в In-Memory OLTP. Дополнительные сведения см. в статье [Поддержка Transact-SQL в In-Memory OLTP](https://msdn.microsoft.com/library/dn133180.aspx).

## Поддерживаемые средства

[SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx) (предварительная версия за сентябрь 2015 г. или более поздняя версия).

[SQL Server Data Tools (SSDT)](https://msdn.microsoft.com/library/mt204009.aspx) (предварительная версия за сентябрь 2015 г. или более поздняя версия).

## Дополнительные ресурсы

Дополнительные сведения о технологиях In-Memory OLTP и In-Memory Analytics: [In-Memory OLTP (оптимизация в памяти)](https://msdn.microsoft.com/library/dn133186.aspx)

[Дополнительные сведения об индексах Columnstore в библиотеке MSDN](https://msdn.microsoft.com/library/gg492088.aspx)

[Технический документ по распространенным шаблонам рабочих нагрузок и вопросам миграции](https://msdn.microsoft.com/library/dn673538.aspx), в котором описываются шаблоны рабочих нагрузок, для которых In-Memory OLTP обычно обеспечивает значительное повышение производительности.

## Дальнейшие действия

Попробуйте [использовать In-Memory OLTP в существующем приложении SQL Azure](sql-database-in-memory-oltp-migration.md).

[Отслеживайте хранилище In-Memory](sql-database-in-memory-oltp-monitoring.md) для In-Memory OLTP.

<!---HONumber=Nov15_HO1-->
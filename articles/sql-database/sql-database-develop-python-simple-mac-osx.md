<properties
	pageTitle="Подключение к базе данных SQL с помощью Python в ОС Mac"
	description="В статье представлен пример кода Python, который можно использовать для подключения к базе данных SQL Azure в ОС Mac. Пример использует драйвер pymssql."
	services="sql-database"
	documentationCenter=""
	authors="meet-bhagdev"
	manager="jeffreyg"
	editor=""/>


<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="python"
	ms.topic="article"
	ms.date="03/14/2016"
	ms.author="meetb"/>


# Подключение к базе данных SQL с помощью Python в ОС Mac


[AZURE.INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]

В этом разделе представлен пример кода, написанный на Python. Пример кода запускается на компьютере с ОС Mac. Этот пример подключается к базе данных SQL Azure с помощью драйвера **pymssql**. Кроме того, посмотрите наш видеоролик [Начало работы с Python на компьютере Mac](https://www.youtube.com/watch?v=OMpugPTwnTI) в качестве дополнения к этой документации.

## Шаг 1. Настройка среды разработки

[Предварительные требования для использования драйвера pymssql Python для SQL Server](https://msdn.microsoft.com/library/mt694094.aspx#Mac)

## Шаг 2. Создание базы данных SQL

Чтобы узнать, как создать образец базы данных, перейдите на страницу [Начало работы](sql-database-get-started.md). Очень важно соблюдать инструкции руководства во время создания **шаблона базы данных AdventureWorks**. Приведенные ниже примеры работают только со **схемой AdventureWorks**.

## Шаг 3. Получение сведений о подключении

[AZURE.INCLUDE [sql-database-include-connection-string-details-20-portalshots](../../includes/sql-database-include-connection-string-details-20-portalshots.md)]

## Шаг 4. Подключение

Функция [pymssql.connect](http://pymssql.org/en/latest/ref/pymssql.html) используется для подключения к базе данных SQL.

	import pymssql
	conn = pymssql.connect(server='yourserver.database.windows.net', user='yourusername@yourserver', password='yourpassword', database='AdventureWorks')


## Шаг 5. Выполнение запроса

Функция [cursor.execute](http://pymssql.org/en/latest/ref/pymssql.html#pymssql.Cursor.execute) может использоваться для извлечения результирующего набора из запроса к базе данных SQL. Эта функция фактически принимает любой запрос и возвращает результирующий набор, по которому может быть выполнена итерация с использованием [cursor.fetchone()](http://pymssql.org/en/latest/ref/pymssql.html#pymssql.Cursor.fetchone).


	import pymssql
	conn = pymssql.connect(server='yourserver.database.windows.net', user='yourusername@yourserver', password='yourpassword', database='AdventureWorks')
	cursor = conn.cursor()
	cursor.execute('SELECT c.CustomerID, c.CompanyName,COUNT(soh.SalesOrderID) AS OrderCount FROM SalesLT.Customer AS c LEFT OUTER JOIN SalesLT.SalesOrderHeader AS soh ON c.CustomerID = soh.CustomerID GROUP BY c.CustomerID, c.CompanyName ORDER BY OrderCount DESC;')
	row = cursor.fetchone()
	while row:
	    print str(row[0]) + " " + str(row[1]) + " " + str(row[2]) 	
	    row = cursor.fetchone()


## Шаг 6. Вставка строки

В приведенном примере показано, как выполнять инструкцию [INSERT](https://msdn.microsoft.com/library/ms174335.aspx), передавать параметры в режиме защиты от [внедрения кода SQL](https://technet.microsoft.com/library/ms161953(v=sql.105).aspx) и извлекать автоматически созданные значения [первичного ключа](https://msdn.microsoft.com/library/ms179610.aspx).



	import pymssql
	conn = pymssql.connect(server='yourserver.database.windows.net', user='yourusername@yourserver', password='yourpassword', database='AdventureWorks')
	cursor = conn.cursor()
	cursor.execute("INSERT SalesLT.Product (Name, ProductNumber, StandardCost, ListPrice, SellStartDate) OUTPUT INSERTED.ProductID VALUES ('SQL Server Express', 'SQLEXPRESS', 0, 0, CURRENT_TIMESTAMP)")
	row = cursor.fetchone()
	while row:
	    print "Inserted Product ID : " +str(row[0])
	    row = cursor.fetchone()


## Шаг 7. Откат транзакции


Этот пример кода демонстрирует использование транзакций, в которых можно:


— начать транзакцию;

— вставить строку данных;

— откатить транзакцию для отмены вставки.


	import pymssql
	conn = pymssql.connect(server='yourserver.database.windows.net', user='yourusername@yourserver', password='yourpassword', database='AdventureWorks')
	cursor = conn.cursor()
	cursor.execute("BEGIN TRANSACTION")
	cursor.execute("INSERT SalesLT.Product (Name, ProductNumber, StandardCost, ListPrice, SellStartDate) OUTPUT INSERTED.ProductID VALUES ('SQL Server Express New', 'SQLEXPRESS New', 0, 0, CURRENT_TIMESTAMP)")
	cnxn.rollback()


## Дальнейшие действия

Дополнительные сведения см. в [Центре разработчика Python](/develop/python/).

<!-----HONumber=AcomDC_0330_2016-->
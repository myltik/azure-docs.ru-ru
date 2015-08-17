<properties 
	pageTitle="Подключение к базе данных SQL с помощью Python с драйвером pymssql в ОС Ubuntu" 
	description="В статье представлен пример кода Python, который можно использовать для подключения к базе данных SQL Azure. Пример выполняется на клиентском компьютере Ubuntu Linux."
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
	ms.date="04/27/2015" 
	ms.author="mebha"/>


# Подключение к базе данных SQL с помощью Python в ОС Ubuntu Linux


[AZURE.INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]


Этот раздел содержит пример кода Python, выполняемого на клиентском компьютере Ubuntu Linux для подключения к базе данных SQL Azure.


## Требования


- [Python 2.7.6](https://www.python.org/download/releases/2.7.6/).


### Установка необходимых модулей


Откройте терминал и перейдите в каталог, в котором вы планируете создать сценарий Python. Введите следующие команды, чтобы установить **FreeTDS** и **pymssql**. Драйвер pymssql использует FreeTDS для подключения к базам данных SQL.

	sudo apt-get --assume-yes update
	sudo apt-get --assume-yes install freetds-dev freetds-bin
	sudo apt-get --assume-yes install python-dev python-pip
	sudo pip install pymssql


### Создание базы данных и получение строки подключения


Чтобы узнать, как создать базу данных и получить строку подключения, перейдите на страницу [Начало работы](sql-database-get-started.md). Очень важно соблюдать инструкции руководства во время создания **шаблона базы данных AdventureWorks**. Приведенные ниже примеры работают только со **схемой AdventureWorks**.


## Подключение к базе данных SQL


Функция [pymssql.connect](http://pymssql.org/en/latest/ref/pymssql.html) используется для подключения к базе данных SQL.

	import pymssql
	conn = pymssql.connect(server='yourserver.database.windows.net', user='yourusername@yourserver', password='yourpassword', database='AdventureWorks')


## Выполнение оператора SQL SELECT

Функция [cursor.execute](http://pymssql.org/en/latest/ref/pymssql.html#pymssql.Cursor.execute) может использоваться для извлечения результирующего набора из запроса к базе данных SQL. Эта функция фактически принимает любой запрос и возвращает результирующий набор, по которому может быть выполнена итерация с использованием [cursor.fetchone()](http://pymssql.org/en/latest/ref/pymssql.html#pymssql.Cursor.fetchone).


	import pymssql
	conn = pymssql.connect(server='yourserver.database.windows.net', user='yourusername@yourserver', password='yourpassword', database='AdventureWorks')
	cursor = conn.cursor()
	cursor.execute('SELECT c.CustomerID, c.CompanyName,COUNT(soh.SalesOrderID) AS OrderCount FROM SalesLT.Customer AS c LEFT OUTER JOIN SalesLT.SalesOrderHeader AS soh ON c.CustomerID = soh.CustomerID GROUP BY c.CustomerID, c.CompanyName ORDER BY OrderCount DESC;')
	row = cursor.fetchone()
	while row:
	    print str(row[0]) + " " + str(row[1]) + " " + str(row[2]) 	
	    row = cursor.fetchone()


## Вставка строки, передача параметров и получение созданного первичного ключа

Для получения автоматически созданных значений [первичного ключа](https://msdn.microsoft.com/library/ms179610.aspx) в базе данных SQL можно использовать свойство [IDENTITY](https://msdn.microsoft.com/library/ms186775.aspx) и объект [SEQUENECE](https://msdn.microsoft.com/library/ff878058.aspx).


	import pymssql
	conn = pymssql.connect(server='yourserver.database.windows.net', user='yourusername@yourserver', password='yourpassword', database='AdventureWorks')
	cursor = conn.cursor()
	cursor.execute("INSERT SalesLT.Product (Name, ProductNumber, StandardCost, ListPrice, SellStartDate) OUTPUT INSERTED.ProductID VALUES ('SQL Server Express', 'SQLEXPRESS', 0, 0, CURRENT_TIMESTAMP)")
	row = cursor.fetchone()
	while row:
	    print "Inserted Product ID : " +str(row[0])
	    row = cursor.fetchone()


## Транзакции


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


 

<!---HONumber=August15_HO6-->
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
	ms.date="07/16/2015" 
	ms.author="mebha"/>


# Подключение к базе данных SQL с помощью Python в ОС Mac


[AZURE.INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]


В этом разделе представлен пример кода, написанный на Python. Пример кода запускается на компьютере с ОС Mac. Этот пример подключается к базе данных SQL Azure с помощью драйвера **pymssql**.


## Требования


- [Python 2.7.6](https://www.python.org/download/releases/2.7.6/).
- [FreeTDS](https://github.com/brianb/FreeTDS)
- [pymssql](https://github.com/pymssql/pymssql)

### Установка необходимых модулей


Откройте терминал и установите:

**1) Homebrew**: выполните указанную ниже команду из терминала. Она запустит загрузку диспетчера пакетов Homebrew на компьютер.

    ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"

**2) FreeTDS**: выполните указанную ниже команду из терминала. Она запустит загрузку FreeTDS на компьютер. FreeTDS требуется для работы Pymmsql.

    brew install FreeTDS
  
**3) Pymmsql**: выполните указанную ниже команду из терминала. На компьютере будет установлен Pymmsql.

    sudo -H pip install pymssql

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

Для получения автоматически созданных [значений первичного ключа](https://msdn.microsoft.com/library/ms179610.aspx) в Базе данных SQL можно использовать свойство [IDENTITY](https://msdn.microsoft.com/library/ms186775.aspx) и объект [SEQUENCE](https://msdn.microsoft.com/library/ff878058.aspx).


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
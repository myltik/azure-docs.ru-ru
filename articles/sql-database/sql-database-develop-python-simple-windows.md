<properties 
	pageTitle="Подключение к базе данных SQL с помощью Python в ОС Windows" 
	description="В статье представлен пример кода Python, который можно использовать для подключения к базе данных SQL Azure с клиентского компьютера Windows. Пример использует драйвер pymssql."
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
	ms.date="07/20/2015" 
	ms.author="mebha"/>


# Подключение к базе данных SQL с помощью Python в ОС Windows


[AZURE.INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]


В этом разделе представлен пример кода, написанный на Python. Пример кода запускается на компьютере с ОС Windows. Этот пример подключается к базе данных SQL Azure с помощью драйвера **pymssql**.


## Требования


- [Python 2.7.6](https://www.python.org/download/releases/2.7.6/)


### Установка необходимых модулей


Установите драйвер [pymssql](http://www.lfd.uci.edu/~gohlke/pythonlibs/#pymssql).

Убедитесь, что выбран правильный файл WHL.

Например, если вы используете Python 2.7 на 64-разрядном компьютере, выберите файл pymssql‑2.1.1‑cp27‑none‑win\_amd64.whl. После загрузки файла WHL разместите его в папке C:/Python27.

Теперь установите драйвер pymssql с помощью pip из командной строки. Перейдите в каталог C:/Python27 и выполните следующую команду:
	
	pip install pymssql‑2.1.1‑cp27‑none‑win_amd64.whl

Инструкции по установке pip см. [здесь](http://stackoverflow.com/questions/4750806/how-to-install-pip-on-windows)


## Создание базы данных и получение строки подключения


Чтобы узнать, как создать базу данных и получить строку подключения, см. раздел [Начало работы](sql-database-get-started.md). Очень важно соблюдать инструкции руководства во время создания **шаблона базы данных AdventureWorks**. Приведенные ниже примеры работают только со **схемой AdventureWorks**.


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
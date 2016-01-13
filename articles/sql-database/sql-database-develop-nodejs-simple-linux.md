<properties
	pageTitle="Подключение к базе данных SQL с помощью Node.js Tedious в ОС Ubuntu Linux"
	description="В этой статье представлен пример кода Node.js, который можно использовать для подключения к базе данных SQL Azure. Для подключения пример использует драйвер Tedious."
	services="sql-database"
	documentationCenter=""
	authors="meet-bhagdev"
	manager="jeffreyg"
	editor=""/>


<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="nodejs"
	ms.topic="article"
	ms.date="12/17/2015"
	ms.author="meetb"/>


# Подключение к базе данных SQL с помощью Node.js Tedious в ОС Ubuntu Linux


> [AZURE.SELECTOR]
- [Python](sql-database-develop-python-simple-ubuntu-linux.md)
- [Node.js](sql-database-develop-nodejs-simple-linux.md)
- [Ruby](sql-database-develop-ruby-simple-linux.md)


В этом разделе представлен пример кода Node.js, работающий в ОС Ubuntu Linux. Подключение к базе данных SQL Azure осуществляется с помощью драйвера Tedious.


## Предварительные требования


Откройте терминал и установите **node** и **npm**, если они еще не установлены на компьютере.


	sudo apt-get install node
	sudo apt-get install npm


После настройки на компьютере **node** и **npm** перейдите в каталог, в котором вы планируете создать проект Node.js, и введите следующие команды.


	sudo npm init
	sudo npm install tedious


Команда **npm init** создает узел проекта. Чтобы сохранить значения по умолчанию во время создания проекта, удерживайте нажатой клавишу Enter, пока проект создается. Теперь в каталоге проекта появится файл **package.json**.


### База данных SQL

Чтобы узнать, как создать образец базы данных, перейдите на страницу [Начало работы](sql-database-get-started.md). Очень важно соблюдать инструкции руководства во время создания **шаблона базы данных AdventureWorks**. Приведенные ниже примеры работают только со **схемой AdventureWorks**.

## Шаг 1. Получение сведений о подключении

[AZURE.INCLUDE [sql-database-include-connection-string-details-20-portalshots](../../includes/sql-database-include-connection-string-details-20-portalshots.md)]

## Шаг 2. Подключение

Функция [new Connection](http://pekim.github.io/tedious/api-connection.html) используется для подключения к базе данных SQL.

	var Connection = require('tedious').Connection;
	var config = {
		userName: 'yourusername',
		password: 'yourpassword',
		server: 'yourserver.database.windows.net',
		// If you are on Microsoft Azure, you need this:
		options: {encrypt: true, database: 'AdventureWorks'}
	};
	var connection = new Connection(config);
	connection.on('connect', function(err) {
	// If no error, then good to proceed.
		console.log("Connected");
	});


## Шаг 3. Выполнение запроса


Все операторы SQL выполняются с помощью функции [new Request()](http://pekim.github.io/tedious/api-request.html). Если оператор возвращает строки, например оператор select, их можно будет извлечь с помощью функции [request.on()](http://pekim.github.io/tedious/api-request.html). Если строк нет, функция [request.on()](http://pekim.github.io/tedious/api-request.html) возвращает пустые списки.


	var Connection = require('tedious').Connection;
	var config = {
		userName: 'yourusername',
		password: 'yourpassword',
		server: 'yourserver.database.windows.net',
		// When you connect to Azure SQL Database, you need these next options.
		options: {encrypt: true, database: 'AdventureWorks'}
	};
	var connection = new Connection(config);
	connection.on('connect', function(err) {
		// If no error, then good to proceed.
		console.log("Connected");
		executeStatement();
	});

	var Request = require('tedious').Request;
	var TYPES = require('tedious').TYPES;

	function executeStatement() {
		request = new Request("SELECT c.CustomerID, c.CompanyName,COUNT(soh.SalesOrderID) AS OrderCount FROM SalesLT.Customer AS c LEFT OUTER JOIN SalesLT.SalesOrderHeader AS soh ON c.CustomerID = soh.CustomerID GROUP BY c.CustomerID, c.CompanyName ORDER BY OrderCount DESC;", function(err) {
	  	if (err) {
	   		console.log(err);}
		});
		var result = "";
		request.on('row', function(columns) {
		    columns.forEach(function(column) {
		      if (column.value === null) {
		        console.log('NULL');
		      } else {
		        result+= column.value + " ";
		      }
		    });
		    console.log(result);
		    result ="";
		});

		request.on('done', function(rowCount, more) {
		console.log(rowCount + ' rows returned');
		});
		connection.execSql(request);
	}


## Шаг 4. Вставка строки

В приведенном примере показано, как выполнять инструкцию [INSERT]\(https://msdn.microsoft.com/library/ms174335.aspx), передавать параметры в режиме защиты от внедрения кода SQL (https://technet.microsoft.com/library/ms161953(v=sql.105).aspx) и извлекать автоматически созданные значения [первичного ключа]\(https://msdn.microsoft.com/library/ms179610.aspx).


	var Connection = require('tedious').Connection;
	var config = {
		userName: 'yourusername',
		password: 'yourpassword',
		server: 'yourserver.database.windows.net',
		// If you are on Azure SQL Database, you need these next options.
		options: {encrypt: true, database: 'AdventureWorks'}
	};
	var connection = new Connection(config);
	connection.on('connect', function(err) {
		// If no error, then good to proceed.
		console.log("Connected");
		executeStatement1();
	});

	var Request = require('tedious').Request
	var TYPES = require('tedious').TYPES;

	function executeStatement1() {
		request = new Request("INSERT SalesLT.Product (Name, ProductNumber, StandardCost, ListPrice, SellStartDate) OUTPUT INSERTED.ProductID VALUES (@Name, @Number, @Cost, @Price, CURRENT_TIMESTAMP);", function(err) {
		 if (err) {
		 	console.log(err);}
		});
		request.addParameter('Name', TYPES.NVarChar,'SQL Server Express 2014');
		request.addParameter('Number', TYPES.NVarChar , 'SQLEXPRESS2014');
		request.addParameter('Cost', TYPES.Int, 11);
		request.addParameter('Price', TYPES.Int,11);
		request.on('row', function(columns) {
		    columns.forEach(function(column) {
		      if (column.value === null) {
		        console.log('NULL');
		      } else {
		        console.log("Product id of inserted item is " + column.value);
		      }
		    });
		});		
		connection.execSql(request);
	}


## Дальнейшие действия

Дополнительную информацию см. в [Центре разработчика Node.js](/develop/nodejs/).

<!----HONumber=AcomDC_1223_2015-->
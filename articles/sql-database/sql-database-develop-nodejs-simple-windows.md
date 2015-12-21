<properties
	pageTitle="Подключение к базе данных SQL с помощьюNode.js в операционной системе Windows"
	description="В этой статье представлен пример кода Node.js, который можно использовать для подключения к базе данных SQL Azure. Пример кода запускается на компьютере с клиентской версией Windows."
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
	ms.date="12/08/2015"
	ms.author="meetb"/>


# Подключение к базе данных SQL с помощьюNode.js в операционной системе Windows


[AZURE.INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]


В этой статье представлен пример кода Node.js, который можно использовать для подключения к базе данных SQL Azure. Программа Node.js запускается на клиентском компьютере Windows. Для управления соединением используется драйвер msnodesql.


## Предварительные требования


Перечисленное ниже программное обеспечение должно быть установлено на клиентском компьютере разработки.


-  Node.js — [версия 0.8.9 (32-разрядная версия)](http://blog.nodejs.org/2012/09/11/node-v0-8-9-stable/). Прокрутите список и нажмите кнопку загрузки 32-разрядной версии установщика Windows x86.
- [Python 2.7.6](https://www.python.org/download/releases/2.7.6/) — установщик для 86- или 64-разрядной версии.
- [Visual C++ 2010](https://app.vssps.visualstudio.com/profile/review?download=true&family=VisualStudioCExpress&release=VisualStudio2010&type=web&slcid=0x409&context=eyJwZSI6MSwicGMiOjEsImljIjoxLCJhbyI6MCwiYW0iOjEsIm9wIjpudWxsLCJhZCI6bnVsbCwiZmEiOjAsImF1IjpudWxsLCJjdiI6OTY4OTg2MzU1LCJmcyI6MCwic3UiOjAsImVyIjoxfQ2) — выпуск Express можно бесплатно загрузить с сайта Майкрософт.
- SQL Server Native Client 11.0 — доступен как Microsoft SQL Server 2012 Native Client в [пакете дополнительных компонентов SQL Server 2012](http://www.microsoft.com/download/details.aspx?id=29065).


### Установка необходимых модулей

После выполнения требований убедитесь, что используется Node.js версии 0.8.9. Это можно проверить, выполнив следующую команду в терминале командной строки: node -v. <br>В окне командной строки **cmd.exe** перейдите в каталог вашего проекта, например C:\\NodeJSSQLProject. Введите следующие команды в указанной последовательности.

	npm init
	npm install msnodesql

Затем перейдите в папку node\_modules\\msnodesql и запустите исполняемый файл **msnodesql-0.2.1-v0.8-ia32**. Следуйте инструкциям мастера установки, а после завершения работы нажмите кнопку «Готово». На этом этапе у вас должен быть установлен драйвер Node.js SQL Server. Выполните следующие действия для получения строки подключения, после чего вы сможете подключиться к Базе данных SQL Azure из приложения Node.js.


### База данных SQL

Чтобы узнать, как создать образец базы данных, перейдите на страницу [Начало работы](sql-database-get-started.md). Очень важно соблюдать инструкции руководства во время создания **шаблона базы данных AdventureWorks**. Приведенные ниже примеры работают только со **схемой AdventureWorks**.


## Шаг 1. Получение сведений о подключении

[AZURE.INCLUDE [sql-database-include-connection-string-details-20-portalshots](../../includes/sql-database-include-connection-string-details-20-portalshots.md)]

## Шаг 2. Подключение


- Скопируйте следующий код в файл JS, расположенный в каталоге проекта.


		var http = require('http');
		var sql = require('msnodesql');
		var http = require('http');
		var fs = require('fs');
		var useTrustedConnection = false;
		var conn_str = "Driver={SQL Server Native Client 11.0};Server=tcp:yourserver.database.windows.net;" +
		(useTrustedConnection == true ? "Trusted_Connection={Yes};" : "UID=yourusername;PWD=yourpassword;") +
		"Database={AdventureWorks};"
		sql.open(conn_str, function (err, conn) {
		    if (err) {
		        console.log("Error opening the connection!");
		        return;
		    }
		    else
		        console.log("Successfuly connected");
		});


- Запустите js-файл, выполнив следующую команду:


		node index.js


## Шаг 3. Выполнение запроса


	var http = require('http');
	var sql = require('msnodesql');
	var http = require('http');
	var fs = require('fs');
	var useTrustedConnection = false;
	var conn_str = "Driver={SQL Server Native Client 11.0};Server=tcp:yourserver.database.windows.net;" +
	(useTrustedConnection == true ? "Trusted_Connection={Yes};" : "UID=yourusername;PWD=yourpassword;") +
	"Database={AdventureWorks};"
	sql.open(conn_str, function (err, conn) {
	    if (err) {
	        console.log("Error opening the connection!");
	        return;
	    }
	    else
	        console.log("Successfuly connected");


	    conn.queryRaw("SELECT c.CustomerID, c.CompanyName,COUNT(soh.SalesOrderID) AS OrderCount FROM SalesLT.Customer AS c LEFT OUTER JOIN SalesLT.SalesOrderHeader AS soh ON c.CustomerID = soh.CustomerID GROUP BY c.CustomerID, c.CompanyName ORDER BY OrderCount DESC;", function (err, results) {
	        if (err) {
	            console.log("Error running query1!");
	            return;
	        }
	        for (var i = 0; i < results.rows.length; i++) {
	            console.log(results.rows[i]);
	        }
	    });
	});


## Шаг 4. Вставка строки


	var http = require('http');
	var sql = require('msnodesql');
	var http = require('http');
	var fs = require('fs');
	var useTrustedConnection = false;
	var conn_str = "Driver={SQL Server Native Client 11.0};Server=tcp:yourserver.database.windows.net;" +
	(useTrustedConnection == true ? "Trusted_Connection={Yes};" : "UID=yourusername;PWD=yourpassword;") +
	"Database={AdventureWorks};"
	sql.open(conn_str, function (err, conn) {
	    if (err) {
	        console.log("Error opening the connection!");
	        return;
	    }
	    else
	        console.log("Successfuly connected");


	    conn.queryRaw("INSERT SalesLT.Product (Name, ProductNumber, StandardCost, ListPrice, SellStartDate) OUTPUT INSERTED.ProductID VALUES ('SQL Server Express', 'SQLEXPRESS', 0, 0, CURRENT_TIMESTAMP)", function (err, results) {
	        if (err) {
	            console.log("Error running query!");
	            return;
	        }
	        for (var i = 0; i < results.rows.length; i++) {
	            console.log("Product ID Inserted : "+results.rows[i]);
	        }
	    });
	});


## Шаг 5. Откат транзакции


Метод **conn.beginTransactions** не будет работать в базе данных SQL Azure. Вместо этого выполните образец кода для выполнения транзакций в базе данных SQL.


	var http = require('http');
	var sql = require('msnodesql');
	var http = require('http');
	var fs = require('fs');
	var useTrustedConnection = false;
	var conn_str = "Driver={SQL Server Native Client 11.0};Server=tcp:yourserver.database.windows.net;" +
	(useTrustedConnection == true ? "Trusted_Connection={Yes};" : "UID=yourusername;PWD=yourpassword;") +
	"Database={AdventureWorks};"
	sql.open(conn_str, function (err, conn) {
	    if (err) {
	        console.log("Error opening the connection!");
	        return;
	    }
	    else
	        console.log("Successfuly connected");


	    conn.queryRaw("INSERT SalesLT.Product (Name, ProductNumber, StandardCost, ListPrice, SellStartDate) OUTPUT INSERTED.ProductID VALUES ('SQL Server Express New ', 'SQLEXPRESS New', 1, 1, CURRENT_TIMESTAMP)", function (err, results) {
	        if (err) {
	            console.log("Error running query!");
	            return;
	        }
	        for (var i = 0; i < results.rows.length; i++) {
	            console.log("Product ID Inserted : "+results.rows[i]);
	        }
	    });

	    conn.queryRaw("ROLLBACK TRANSACTION; ", function (err, results) {
            	if (err) {
        		console.log("Rollback failed");
        		return;
        	}
    	    });
	});


## Шаг 6. Хранимые процедуры

Для работы этого образца кода необходимо иметь или создать хранимую процедуру, которая не вводит параметры. Хранимую процедуру можно создать с помощью инструмента SQL Server Management Studio (SSMS.exe).


	var http = require('http');
	var sql = require('msnodesql');
	var http = require('http');
	var fs = require('fs');
	var useTrustedConnection = false;
	var conn_str = "Driver={SQL Server Native Client 11.0};Server=tcp:yourserver.database.windows.net;" +
	(useTrustedConnection == true ? "Trusted_Connection={Yes};" : "UID=yourusername;PWD=yourpassword;") +
	"Database={AdventureWorks};"
	sql.open(conn_str, function (err, conn) {
	    if (err) {
	        console.log("Error opening the connection!");
	        return;
	    }
	    else
	        console.log("Successfuly connected");

	    conn.query("exec NameOfStoredProcedure", function (err, results) {
	    	if (err) {
			console.log("Error running query8!");
			return;
		}
	    });
	});


## Дальнейшие действия

Дополнительную информацию см. в [Центре разработчика Node.js](/develop/nodejs/).

<!---HONumber=AcomDC_1210_2015-->
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
	ms.date="04/27/2015" 
	ms.author="mebha"/>


# Подключение к базе данных SQL с помощьюNode.js в операционной системе Windows


[AZURE.INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]


В этой статье представлен пример кода Node.js, который можно использовать для подключения к базе данных SQL Azure. Программа Node.js запускается на клиентском компьютере Windows. Для управления соединением используется драйвер msnodesql.


## Требования


Перечисленное ниже программное обеспечение должно быть установлено на клиентском компьютере разработки.


-  Node.js — [версия 0.8.9 (32-разрядная версия)](http://blog.nodejs.org/2012/09/11/node-v0-8-9-stable/). Прокрутите список и нажмите кнопку загрузки 32-разрядной версии установщика Windows x86.
- [Python 2.7.6](https://www.python.org/download/releases/2.7.6/) — установщик для 86- или 64-разрядной версии. 
- [Visual C++ 2010](https://app.vssps.visualstudio.com/profile/review?download=true&family=VisualStudioCExpress&release=VisualStudio2010&type=web&slcid=0x409&context=eyJwZSI6MSwicGMiOjEsImljIjoxLCJhbyI6MCwiYW0iOjEsIm9wIjpudWxsLCJhZCI6bnVsbCwiZmEiOjAsImF1IjpudWxsLCJjdiI6OTY4OTg2MzU1LCJmcyI6MCwic3UiOjAsImVyIjoxfQ2) — выпуск Express можно бесплатно загрузить с сайта Майкрософт.
- SQL Server Native Client 11.0 — доступен как Microsoft SQL Server 2012 Native Client в [пакете дополнительных компонентов SQL Server 2012](http://www.microsoft.com/download/details.aspx?id=29065).


### Установка необходимых модулей


В окне командной строки **cmd.exe** откройте каталог, в котором находится драйвер msnodesql. Введите следующие команды в указанной последовательности.


	npm install msnodesql
	npm install -g node-gyp


Теперь, когда узел gyp установлен, перейдите в каталог *YourProjectDirectory*, а затем — в папку **node_modules\msnodesql**. Затем выполните следующие команды в окне **cmd.exe**.


	node-gyp configure 
	node-gyp build


Затем перейдите в каталог **build\release**. Скопируйте файл **sqlserver.node** и вставьте его в каталог **msnodesql\lib**. Замените старый файл, если необходимо.


### Создание базы данных и получение строки подключения
 
Информацию о создании образца базы данных и получении строки подключения см. в разделе [Начало работы](sql-database-get-started.md). Очень важно соблюдать инструкции руководства во время создания **шаблона базы данных AdventureWorks**. Приведенные ниже примеры будут работать только со **схемой AdventureWorks**.


## Подключение к базе данных SQL


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


## Выполнение оператора SQL SELECT


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


## Вставка строки, передача параметров и получение созданного первичного ключа


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


## Транзакции


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


## Хранимые процедуры


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

<!---HONumber=58--> 
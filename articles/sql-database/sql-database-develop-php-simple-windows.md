<properties
	pageTitle="PHP в ОС Windows для подключения к базе данных SQL | Microsoft Azure"
	description="В этой статье представлен пример программы PHP, которая подключается к базе данных SQL Azure из клиентской ОС Windows, и показаны ссылки на программные компоненты, необходимые для клиента."
	services="sql-database"
	documentationCenter=""
	authors="meet-bhagdev"
	manager="jeffreyg"
	editor=""/>


<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="php"
	ms.topic="article"
	ms.date="06/10/2015"
	ms.author="mebha"/>


# Подключение к базе данных SQL с помощью приложения PHP в операционной системе Windows


[AZURE.INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]


В этом разделе объясняется, как подключиться к базе данных SQL Azure из клиентского приложения PHP, которое запущено в ОС Windows.


## Предварительные требования


Чтобы запустить пример кода PHP, приведенный в этом разделе, на клиентский компьютер необходимо установить следующее программное обеспечение.


- [Драйверы Microsoft для PHP для Microsoft SQL Server](http://www.microsoft.com/download/details.aspx?id=20098) (файл SQLSRV32.EXE содержит последнюю версию).
- [Microsoft SQL Server Native Client 11.0](http://www.microsoft.com/download/details.aspx?id=36434).
- [Драйвер ODBC Microsoft](https://www.microsoft.com/ru-ru/download/details.aspx?id=36434).
- IIS Express.
- [PHP 5.6 для IIS Express](http://www.microsoft.com/web/downloads/platform.aspx). Скачайте дистрибутив с помощью установщика платформы. Для скачивания установщика платформы используйте Internet Explorer.

Чтобы узнать, как установить и настроить перечисленные выше компоненты, посетите наш [блог группы](http://blogs.msdn.com/b/sqlphp/archive/2015/05/11/getting-started-with-php-and-microsoft-sql-server.aspx) и просмотрите [видео](https://www.youtube.com/watch?v=0oCjiRK_tUk).


## Создание базы данных и получение строки подключения


Чтобы узнать, как создать базу данных и получить строку подключения, см. раздел [Начало работы](sql-database-get-started.md). Очень важно соблюдать инструкции руководства во время создания **шаблона базы данных AdventureWorks**. Приведенные ниже примеры работают только со **схемой AdventureWorks**.


## Подключение к базе данных SQL


Эта функция **OpenConnection** вызывается перед выполнением всех последующих функций.


	function OpenConnection()
	{
		try
		{
			$serverName = "tcp:myserver.database.windows.net,1433";
			$connectionOptions = array("Database"=>"AdventureWorks",
				"Uid"=>"MyUser", "PWD"=>"MyPassword");
			$conn = sqlsrv_connect($serverName, $connectionOptions);
			if($conn == false)
				die(FormatErrors(sqlsrv_errors()));
		}
		catch(Exception $e)
		{
			echo("Error!");
		}
	}


## Выполнение запроса и получение результирующего набора

Функция [sqlsrv_query()](http://php.net/manual/en/function.sqlsrv-query.php) может использоваться для извлечения результирующего набора из запроса к базе данных SQL. Эта функция фактически принимает любой запрос и объект соединения и возвращает результирующий набор, по которому может быть выполнена итерация с использованием [sqlsrv_fetch_array()](http://php.net/manual/en/function.sqlsrv-fetch-array.php).

	function ReadData()
	{
		try
		{
			$conn = OpenConnection();
			$tsql = "SELECT [CompanyName] FROM SalesLT.Customer";
			$getProducts = sqlsrv_query($conn, $tsql);
			if ($getProducts == FALSE)
				die(FormatErrors(sqlsrv_errors()));
			$productCount = 0;
			while($row = sqlsrv_fetch_array($getProducts, SQLSRV_FETCH_ASSOC))
			{
				echo($row['CompanyName']);
				echo("<br/>");
				$productCount++;
			}
			sqlsrv_free_stmt($getProducts);
			sqlsrv_close($conn);
		}
		catch(Exception $e)
		{
			echo("Error!");
		}
	}
	

## Вставка строки, передача параметров и получение созданного первичного ключа


Для получения автоматически созданных значений [первичного ключа](https://msdn.microsoft.com/library/ms179610.aspx) в базе данных SQL можно использовать свойство [IDENTITY](https://msdn.microsoft.com/library/ms186775.aspx) и объект [SEQUENECE](https://msdn.microsoft.com/library/ff878058.aspx).


	function InsertData()
	{
		try
		{
			$conn = OpenConnection();

			$tsql = "INSERT SalesLT.Product (Name, ProductNumber, StandardCost, ListPrice, SellStartDate) OUTPUT 			INSERTED.ProductID VALUES ('SQL Server 1', 'SQL Server 2', 0, 0, getdate())";
			//Insert query
			$insertReview = sqlsrv_query($conn, $tsql);
			if($insertReview == FALSE)
				die(FormatErrors( sqlsrv_errors()));
			echo "Product Key inserted is :";	
			while($row = sqlsrv_fetch_array($insertReview, SQLSRV_FETCH_ASSOC))
			{   
				echo($row['ProductID']);
			}
			sqlsrv_free_stmt($insertReview);
			sqlsrv_close($conn);
		}
		catch(Exception $e)
		{
			echo("Error!");
		}
	}

## Транзакции


Этот пример кода демонстрирует использование транзакций, в которых можно:

— начать транзакцию;

— вставить строку данных, обновить другую строку данных;

— зафиксировать транзакцию, если запросы на вставку и обновление выполнены успешно, или откатить транзакцию, если один из них вызвал ошибку.


	function Transactions()
	{
		try
		{
			$conn = OpenConnection();

			if (sqlsrv_begin_transaction($conn) == FALSE)
				die(FormatErrors(sqlsrv_errors()));

			$tsql1 = "INSERT INTO SalesLT.SalesOrderDetail (SalesOrderID,OrderQty,ProductID,UnitPrice) 
			VALUES (71774, 22, 709, 33)";
			$stmt1 = sqlsrv_query($conn, $tsql1);
			
			/* Set up and execute the second query. */
			$tsql2 = "UPDATE SalesLT.SalesOrderDetail SET OrderQty = (OrderQty + 1) WHERE ProductID = 709";
			$stmt2 = sqlsrv_query( $conn, $tsql2);
			
			/* If both queries were successful, commit the transaction. */
			/* Otherwise, rollback the transaction. */
			if($stmt1 && $stmt2)
			{
			       sqlsrv_commit($conn);
			       echo("Transaction was commited");
			}
			else
			{
			    sqlsrv_rollback($conn);
			    echo "Transaction was rolled back.\n";
			}
			/* Free statement and connection resources. */
			sqlsrv_free_stmt( $stmt1);
			sqlsrv_free_stmt( $stmt2);
		}
		catch(Exception $e)
		{
			echo("Error!");
		}
	}


## Дополнительные материалы


Дополнительные сведения об установке и использовании PHP см. в статье [Доступ к базам данных SQL Server с помощью PHP](http://technet.microsoft.com/library/cc793139.aspx).

 

<!---HONumber=July15_HO2-->
<properties
	pageTitle="Логика повторов в PHP для подключения к базе данных SQL | Microsoft Azure"
	description="В этой статье представлен пример программы PHP, которая подключается к Базе данных SQL Azure из клиентской ОС Windows с обработкой временных сбоев, и показаны ссылки на программные компоненты, необходимые для клиента."
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
	ms.date="03/18/2016"
	ms.author="meetb"/>


# Подключение к Базе данных SQL с помощью приложения PHP в операционной системе Windows с обработкой временных сбоев


[AZURE.INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]


В этом разделе объясняется, как подключиться к базе данных SQL Azure из клиентского приложения PHP, которое запущено в ОС Windows.


[AZURE.INCLUDE [sql-database-develop-includes-prerequisites-php-windows](../../includes/sql-database-develop-includes-prerequisites-php-windows.md)]

### База данных SQL

Чтобы узнать, как создать образец базы данных, перейдите на страницу [Начало работы](sql-database-get-started.md). Очень важно соблюдать инструкции руководства во время создания **шаблона базы данных AdventureWorks**. Приведенные ниже примеры работают только со **схемой AdventureWorks**.


## Шаг 1. Получение сведений о подключении

[AZURE.INCLUDE [sql-database-include-connection-string-details-20-portalshots](../../includes/sql-database-include-connection-string-details-20-portalshots.md)]

## Шаг 2. Подключение и запрос

Демонстрационная программа разработана так, чтобы временная ошибка во время попытки подключения вызывала повторную попытку. Тем не менее, в случае временной ошибки во время выполнения команды запроса программа отменяет текущее подключение и создает новое, прежде чем повторять попытку запроса. Мы не выступаем на за, ни против использования этого метода. Демонстрационная программа иллюстрирует некоторые возможности разработки, доступные пользователю.

<br>Длина этого примера кода в основном связана с логикой перехвата исключений. Сокращенная версия файла Program.cs доступна [здесь](sql-database-develop-php-simple-windows.md). <br>Метод Main находится в файле Program.cs. Стек вызовов работает следующим образом.
* Метод Main вызывает ConnectAndQuery.
* ConnectAndQuery вызывает EstablishConnection.
* EstablishConnection вызывает IssueQueryCommand.

Функция [sqlsrv\_query()](http://php.net/manual/en/function.sqlsrv-query.php) может использоваться для извлечения результирующего набора из запроса к базе данных SQL. Эта функция фактически принимает любой запрос и объект соединения и возвращает результирующий набор, по которому может быть выполнена итерация с использованием [sqlsrv\_fetch\_array()](http://php.net/manual/en/function.sqlsrv-fetch-array.php).

	<?php
		// Variables to tune the retry logic.  
		$connectionTimeoutSeconds = 30;  // Default of 15 seconds is too short over the Internet, sometimes.
		$maxCountTriesConnectAndQuery = 3;  // You can adjust the various retry count values.
		$secondsBetweenRetries = 4;  // Simple retry strategy.
		$errNo = 0;
		$serverName = "tcp:yourdatabase.database.windows.net,1433";
		$connectionOptions = array("Database"=>"AdventureWorks",
		   "Uid"=>"yourusername", "PWD"=>"yourpassword", "LoginTimeout" => $connectionTimeoutSeconds);
		$conn;
		$errorArr = array();
		for ($cc = 1; $cc <= $maxCountTriesConnectAndQuery; $cc++)
		{
		    $errorArr = array();
		    $ctr = 0;
		    // [A.2] Connect, which proceeds to issue a query command.
		    $conn = sqlsrv_connect($serverName, $connectionOptions);  
		    if( $conn == true)
		    {
		        echo "Connection was established";
		        echo "<br>";

		        $tsql = "SELECT [CompanyName] FROM SalesLT.Customer";
		        $getProducts = sqlsrv_query($conn, $tsql);
		        if ($getProducts == FALSE)
		            die(FormatErrors(sqlsrv_errors()));
		        $productCount = 0;
		        $ctr = 0;
		        while($row = sqlsrv_fetch_array($getProducts, SQLSRV_FETCH_ASSOC))
		        {   
		            $ctr++;
		            echo($row['CompanyName']);
		            echo("<br/>");
		            $productCount++;
		            if($ctr>10)
		                break;
		        }
		        sqlsrv_free_stmt($getProducts);
		        break;
		    }
		    // Adds any the error codes from the SQL Exception to an array.
		    else {  
		        if( ($errors = sqlsrv_errors() ) != null) {
		            foreach( $errors as $error ) {
		                $errorArr[$ctr] = $error['code'];
		                $ctr = $ctr + 1;
		            }
		        }
		        $isTransientError = TRUE;
		        // [A.4] Check whether sqlExc.Number is on the whitelist of transients.
		        $isTransientError = IsTransientStatic($errorArr);
		        if ($isTransientError == TRUE)  // Is a static persistent error...
		        {
		            echo("Persistent error suffered, SqlException.Number==". $errorArr[0].". Program Will terminate.");
		            echo "<br>";
		            // [A.5] Either the connection attempt or the query command attempt suffered a persistent SqlException.
		            // Break the loop, let the hopeless program end.
		            exit(0);
		        }
		        // [A.6] The SqlException identified a transient error from an attempt to issue a query command.
		        // So let this method reloop and try again. However, we recommend that the new query
		        // attempt should start at the beginning and establish a new connection.
		        if ($cc >= $maxCountTriesConnectAndQuery)
		        {
		            echo "Transient errors suffered in too many retries - " . $cc ." Program will terminate.";
		            echo "<br>";
		            exit(0);
		        }
		        echo("Transient error encountered.  SqlException.Number==". $errorArr[0]. " . Program might retry by itself.");  
		        echo "<br>";
		        echo $cc . " Attempts so far. Might retry.";
		        echo "<br>";
		        // A very simple retry strategy, a brief pause before looping. This could be changed to exponential if you want.
		        sleep(1*$secondsBetweenRetries);
		    }
		    // [A.3] All has gone well, so let the program end.
		}
		function IsTransientStatic($errorArr) {
		    $arrayOfTransientErrorNumbers = array(4060, 10928, 10929, 40197, 40501, 40613);
		    $result = array_intersect($arrayOfTransientErrorNumber, $errorArr);
		    $count = count($result);
		    if($count >= 0) //change to > 0 later.
		        return TRUE;
		}
	?>

## Дальнейшие действия

Дополнительные сведения об установке и использовании PHP см. в статье [Доступ к базам данных SQL Server с помощью PHP](http://technet.microsoft.com/library/cc793139.aspx).

<!---HONumber=AcomDC_0323_2016-->
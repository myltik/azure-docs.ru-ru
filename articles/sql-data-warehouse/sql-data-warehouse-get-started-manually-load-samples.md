<properties
   pageTitle="Загрузка образца данных в хранилище данных SQL | Microsoft Azure"
   description="Загрузка образца данных в хранилище данных SQL"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lodipalm"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="01/04/2016"
   ms.author="lodipalm;barbkess"/>

#Загрузка образца данных в хранилище данных SQL

Следующим шагом после [создания экземпляра базы данных хранилища данных SQL][create a SQL Data Warehouse database instance] является создание и загрузка нескольких таблиц. Примеры сценариев Adventure Works, разработанные нами для хранилища данных SQL, можно использовать в целях создания и загрузки таблиц для вымышленной компании Adventure Works. Эти сценарии используют sqlcmd для выполнения SQL и bcp для загрузки данных. Если вы еще не установили эти средства, воспользуйтесь следующими ссылками для [установки bcp][] и [sqlcmd][].

Выполните эти простые действия для загрузки образца базы данных образца Adventure Works в хранилище данных SQL...

1. Скачайте [примеры сценариев Adventure Works для хранилища данных SQL][].

2. Извлеките файлы из скачанного ZIP-файла в каталог на локальном компьютере.

3. Измените извлеченный файл aw\_create.bat и задайте следующие переменные в верхней его части: Не забудьте, что между знаком = и параметром не должно быть пробелов. Ниже приведены примеры того, как могут выглядеть такие изменения.

    	server=mylogicalserver.database.windows.net
    	user=mydwuser
    	password=Mydwpassw0rd
    	database=mydwdatabase

4. Запустите измененный файл aw\_create.bat из командной строки Windows. Убедитесь, что вы находитесь в том каталоге, куда была сохранена измененная версия aw\_create.bat. Вот что делает этот сценарий:
	* удаляет все представления и таблицы Adventure Works, которые уже существуют в базе данных;
	* создает представления и таблицы Adventure Works;
	* загружает каждую таблицу Adventure Works с помощью bcp;
	* проверяет количество строк для каждой таблицы Adventure Works;
	* собирает статистику по каждому столбцу для каждой таблицы Adventure Works.


##Запрос демонстрационных данных

После загрузки некоторых демонстрационных данных в хранилище данных SQL можно быстро выполнить несколько запросов. Для выполнения запроса подключитесь к недавно созданной базе данных Adventure Works в хранилище данных SQL Azure с помощью Visual Studio и SSDT, как описано в документе о [подключении][].

Пример простого оператора select для получения всех сведений о сотрудниках:

	SELECT * FROM DimEmployee;

Пример более сложного запроса с использованием таких конструкций, как GROUP BY, для просмотра общей суммы всех продаж за каждый день:

	SELECT OrderDateKey, SUM(SalesAmount) AS TotalSales
	FROM FactInternetSales
	GROUP BY OrderDateKey
	ORDER BY OrderDateKey;

Пример оператора SELECT с выражением WHERE для фильтрации заказов, размещенных до определенной даты:

	SELECT OrderDateKey, SUM(SalesAmount) AS TotalSales
	FROM FactInternetSales
	WHERE OrderDateKey > '20020801'
	GROUP BY OrderDateKey
	ORDER BY OrderDateKey;

Хранилище данных SQL поддерживает почти все конструкции T-SQL, которые поддерживаются SQL Server. Все различия описаны в нашей документации по [переносу кода][].

## Дальнейшие действия
Теперь, когда имеется возможность выполнить некоторые запросы с демонстрационными данными, попробуйте осуществлять [разработку][], [загрузку][] или [перенос][] в хранилище данных SQL.

<!--Image references-->

<!--Article references-->
[перенос]: ./sql-data-warehouse-overview-migrate.md
[разработку]: ./sql-data-warehouse-overview-develop.md
[загрузку]: ./sql-data-warehouse-overview-load.md
[подключении]: ./sql-data-warehouse-get-started-connect.md
[переносу кода]: ./sql-data-warehouse-migrate-code.md
[create a SQL Data Warehouse database instance]: ./sql-data-warehouse-get-started-provision.md
[установки bcp]: ./sql-data-warehouse-load-with-bcp.md
[sqlcmd]: ./sql-data-warehouse-get-started-connect-query-sqlcmd.md

<!--Other Web references-->
[примеры сценариев Adventure Works для хранилища данных SQL]: https://migrhoststorage.blob.core.windows.net/sqldwsample/AdventureWorksSQLDW2012.zip

<!---HONumber=AcomDC_0107_2016-->
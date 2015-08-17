<properties
   pageTitle="Загрузка образца данных в хранилище данных SQL | Microsoft Azure"
   description="Загрузка достаточных данных в хранилище данных SQL"
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
   ms.date="08/05/2015"
   ms.author="lodipalm;barbkess"/>

#Загрузка образца данных в хранилище данных SQL

После установки экземпляра хранилища данных SQL можно легко загрузить в него некоторые образцы данных. Следующие сведения помогут вам создать набор данных с именем AdventureWorksPDW2012 в базе данных. Этот набор данных моделирует образец структуры хранилища данных для вымышленной компании с названием AdventureWorks. Обратите внимание, что для выполнения следующих действий должна быть установлена программа BCP. Если у вас в настоящее время не установлена программа BCP, установите [программы командной строки Microsoft для SQL Server][].

1. Для начала работы щелкните, чтобы загрузить наши [образцы сценариев данных][].

2. После загрузки файла извлеките содержимое файла AdventureWorksPDW2012.zip и откройте новую папку AdventureWorksPDW2012.

3. Измените файл aw\_create.bat и задайте следующие значения в верхней части файла: a. **Сервер**: полное имя сервера хранилища данных SQL находится на b. **Пользователь**: пользователь указанного выше сервера c. **Пароль**: пароль для указанного имени входа на сервер d. **База данных**: имя экземпляра хранилища данных SQL, куда необходимо загрузить данные

4. Запустите aw\_create.bat из каталога, в котором он находится. Это создаст схему и загрузит данные во все таблицы с помощью программы BCP.


## Подключение к образцу и отправка запроса

Как описано в документации по [подключению и запросам][], можно подключиться к базе данных с помощью Visual Studio и SSDT. Теперь, когда некоторые образцы данных загружены в хранилище данных SQL, можно быстро запустить несколько запросов, чтобы приступить к работе.

Можно запустить простой оператор select для получения всех сведений о сотрудниках:

	SELECT * FROM DimEmployee;

Мы также можем выполнить более сложный запрос с помощью таких конструкций, как GROUP BY, чтобы увидеть общую сумму всех продаж за каждый день:

	SELECT OrderDateKey, SUM(SalesAmount) AS TotalSales
	FROM FactInternetSales
	GROUP BY OrderDateKey
	ORDER BY OrderDateKey;

Можно даже использовать выражение WHERE для фильтрации заказов, размещенных до определенной даты:

	SELECT OrderDateKey, SUM(SalesAmount) AS TotalSales
	FROM FactInternetSales
	WHERE OrderDateKey > '20020801'
	GROUP BY OrderDateKey
	ORDER BY OrderDateKey;

На самом деле хранилище данных SQL поддерживает почти все конструкции T-SQL, которые поддерживает SQL Server. Некоторые различия можно найти в нашей документации [Перенос кода][].

## Дальнейшие действия
Теперь, когда вы немного разобрались с образцом данных, узнайте, как [разрабатывать][], [загружать][] или [переносить][].

<!--Image references-->

<!--Article references-->
[переносить]: https://azure.microsoft.com/ru-ru/documentation/articles/sql-data-warehouse-overview-migrate/
[разрабатывать]: https://azure.microsoft.com/ru-ru/documentation/articles/sql-data-warehouse-overview-develop/
[загружать]: https://azure.microsoft.com/ru-ru/documentation/articles/sql-data-warehouse-overview-load/
[подключению и запросам]: https://azure.microsoft.com/ru-ru/documentation/articles/sql-data-warehouse-get-started-connect-query/
[Перенос кода]: https://azure.microsoft.com/ru-ru/documentation/articles/sql-data-warehouse-migrate-code/

<!--MSDN references-->
[программы командной строки Microsoft для SQL Server]: http://www.microsoft.com/ru-ru/download/details.aspx?id=36433

<!--Other Web references-->
[образцы сценариев данных]: https://migrhoststorage.blob.core.windows.net/sqldwsample/AdventureWorksPDW2012.zip

<!---HONumber=August15_HO6-->
.<properties
   pageTitle="Загрузка образца данных в хранилище данных SQL | Microsoft Azure"
   description="Загрузка образца данных в хранилище данных SQL"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lodipalm"
   manager="barbkess"
   editor=""/>

.<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="08/16/2016"
   ms.author="lodipalm;barbkess;sonyama"/>

#Загрузка образца данных в хранилище данных SQL

Выполните эти простые действия для загрузки примера базы данных Adventure Works и выполнения запроса к ней. Эти сценарии сначала используют sqlcmd для выполнения инструкций SQL, что приведет к созданию таблиц и представлений. После создания таблицы сценарии используют bcp для загрузки данных. Если вы еще не установили sqlcmd и bcp, воспользуйтесь следующими ссылками для [установки bcp][] и [sqlcmd][].

##Загрузка примера данных

1. Скачайте ZIP-файл с [примерами сценариев Adventure Works для хранилища данных SQL][].

2. Извлеките файлы из скачанного ZIP-файла в каталог на локальном компьютере.

3. Измените извлеченный файл aw\_create.bat и задайте следующие переменные в верхней его части: Не забудьте, что между знаком = и параметром не должно быть пробелов. Ниже приведены примеры того, как могут выглядеть такие изменения.

    ```
    server=mylogicalserver.database.windows.net
    user=mydwuser
    password=Mydwpassw0rd
    database=mydwdatabase
    ```

4. Запустите измененный файл aw\_create.bat из командной строки Windows. Убедитесь, что вы находитесь в том каталоге, куда была сохранена измененная версия aw\_create.bat. Вот что делает этот сценарий:
	* удаляет все представления и таблицы Adventure Works, которые уже существуют в базе данных;
	* создает представления и таблицы Adventure Works;
	* загружает каждую таблицу Adventure Works с помощью bcp;
	* проверяет количество строк для каждой таблицы Adventure Works;
	* собирает статистику по каждому столбцу для каждой таблицы Adventure Works.


##Запрос части данных для примера

После загрузки некоторых демонстрационных данных в хранилище данных SQL можно быстро выполнить несколько запросов. Для выполнения запроса подключитесь к недавно созданной базе данных Adventure Works в хранилище данных SQL Azure с помощью Visual Studio и SSDT, как описано в документе о [выполнении запроса с помощью Visual Studio][].

Пример простого оператора select для получения всех сведений о сотрудниках:

```sql
SELECT * FROM DimEmployee;
```

Пример более сложного запроса с использованием таких конструкций, как GROUP BY, для просмотра общей суммы всех продаж за каждый день:

```sql
SELECT OrderDateKey, SUM(SalesAmount) AS TotalSales
FROM FactInternetSales
GROUP BY OrderDateKey
ORDER BY OrderDateKey;
```

Пример оператора SELECT с выражением WHERE для фильтрации заказов, размещенных до определенной даты:

```
SELECT OrderDateKey, SUM(SalesAmount) AS TotalSales
FROM FactInternetSales
WHERE OrderDateKey > '20020801'
GROUP BY OrderDateKey
ORDER BY OrderDateKey;
```

Хранилище данных SQL поддерживает почти все конструкции T-SQL, которые поддерживаются SQL Server. Все различия описаны в нашей документации по [переносу кода][].

## Дальнейшие действия
Теперь, когда имеется возможность выполнить некоторые запросы с демонстрационными данными, попробуйте осуществлять [разработку][], [загрузку][] или [перенос][] в хранилище данных SQL.

.<!--Image references-->

.<!--Article references-->
[перенос]: sql-data-warehouse-overview-migrate.md
[разработку]: sql-data-warehouse-overview-develop.md
[загрузку]: sql-data-warehouse-overview-load.md
[выполнении запроса с помощью Visual Studio]: sql-data-warehouse-query-visual-studio.md
[переносу кода]: sql-data-warehouse-migrate-code.md
[установки bcp]: sql-data-warehouse-load-with-bcp.md
[sqlcmd]: sql-data-warehouse-get-started-connect-sqlcmd.md

.<!--Other Web references-->
[примерами сценариев Adventure Works для хранилища данных SQL]: https://migrhoststorage.blob.core.windows.net/sqldwsample/AdventureWorksSQLDW2012.zip

<!---HONumber=AcomDC_0817_2016-->
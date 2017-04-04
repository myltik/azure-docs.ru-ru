---
title: "Загрузка образца данных в хранилище данных SQL | Документация Майкрософт"
description: "Загрузка образца данных в хранилище данных SQL"
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: jhubbard
editor: 
ms.assetid: e338ecf8-cfee-419b-b7b6-98108d381c62
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: loading
ms.date: 10/31/2016
ms.author: barbkess
translationtype: Human Translation
ms.sourcegitcommit: 424d8654a047a28ef6e32b73952cf98d28547f4f
ms.openlocfilehash: cc5ec6022cede019541d697905aa068b88d25ee4
ms.lasthandoff: 03/22/2017



---
# <a name="load-sample-data-into-sql-data-warehouse"></a>Загрузка образца данных в хранилище данных SQL
Выполните эти простые действия для загрузки примера базы данных Adventure Works и выполнения запроса к ней. Эти сценарии сначала используют sqlcmd для выполнения инструкций SQL, что приведет к созданию таблиц и представлений. После создания таблицы сценарии используют bcp для загрузки данных.  Если вы еще не установили sqlcmd и bcp, воспользуйтесь следующими ссылками для [установки bcp][install bcp] и [sqlcmd][install sqlcmd].

## <a name="load-sample-data"></a>Загрузка примера данных
1. Скачайте ZIP-файл с [примерами сценариев Adventure Works для хранилища данных SQL][Adventure Works Sample Scripts for SQL Data Warehouse].
2. Извлеките файлы из скачанного ZIP-файла в каталог на локальном компьютере.
3. Измените извлеченный файл aw_create.bat и задайте следующие переменные в верхней его части:  Не забудьте, что между знаком = и параметром не должно быть пробелов.  Ниже приведены примеры того, как могут выглядеть такие изменения.
   
    ```
    server=mylogicalserver.database.windows.net
    user=mydwuser
    password=Mydwpassw0rd
    database=mydwdatabase
    ```
4. Запустите измененный файл aw_create.bat из командной строки Windows.  Убедитесь, что вы находитесь в том каталоге, куда была сохранена измененная версия aw_create.bat.
   Вот что делает этот сценарий:
   
   * удаляет все представления и таблицы Adventure Works, которые уже существуют в базе данных;
   * создает представления и таблицы Adventure Works;
   * загружает каждую таблицу Adventure Works с помощью bcp;
   * проверяет количество строк для каждой таблицы Adventure Works;
   * собирает статистику по каждому столбцу для каждой таблицы Adventure Works.

## <a name="query-sample-data"></a>Запрос части данных для примера
После загрузки некоторых демонстрационных данных в хранилище данных SQL можно быстро выполнить несколько запросов.  Для выполнения запроса подключитесь к недавно созданной базе данных Adventure Works в хранилище данных SQL Azure с помощью Visual Studio и SSDT, как описано в документе о [выполнении запроса с помощью Visual Studio][query with Visual Studio].

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

Хранилище данных SQL поддерживает почти все конструкции T-SQL, которые поддерживаются SQL Server.  Все различия описаны в нашей документации по [переносу кода][migrate code].

## <a name="next-steps"></a>Дальнейшие действия
Теперь, когда имеется возможность выполнить некоторые запросы с демонстрационными данными, попробуйте осуществлять [разработку][develop], [загрузку][load] или [перенос][migrate] в хранилище данных SQL.

<!--Image references-->

<!--Article references-->
[migrate]: sql-data-warehouse-overview-migrate.md
[develop]: sql-data-warehouse-overview-develop.md
[load]: sql-data-warehouse-overview-load.md
[query with Visual Studio]: sql-data-warehouse-query-visual-studio.md
[migrate code]: sql-data-warehouse-migrate-code.md
[install bcp]: sql-data-warehouse-load-with-bcp.md
[install sqlcmd]: sql-data-warehouse-get-started-connect-sqlcmd.md

<!--Other Web references-->
[Adventure Works Sample Scripts for SQL Data Warehouse]: https://migrhoststorage.blob.core.windows.net/sqldwsample/AdventureWorksSQLDW2012.zip


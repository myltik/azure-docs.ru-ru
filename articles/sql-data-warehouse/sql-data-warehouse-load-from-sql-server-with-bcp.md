---
title: "Загрузка данных из SQL Server в хранилище данных SQL Azure (с использованием bcp) | Документация Майкрософт"
description: "Описывается использование программы bcp, чтобы экспортировать небольшие наборы данных из SQL Server, а потом импортировать их непосредственно в хранилище данных SQL."
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: jhubbard
editor: 
ms.assetid: f87d8d7c-8276-43c5-90e7-d4ccc0e3a224
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 10/31/2016
ms.author: barbkess
ms.custom: loading
translationtype: Human Translation
ms.sourcegitcommit: c0e2324a2b2e6294df6e502f2e7a0ae36ff94158
ms.openlocfilehash: 8dc7c2fb833c1c51ecef772ba1cbe5f0405fe494
ms.lasthandoff: 01/30/2017


---
# <a name="load-data-from-sql-server-into-azure-sql-data-warehouse-flat-files"></a>Загрузка данных из SQL Server в хранилище данных SQL Azure (неструктурированные файлы)
> [!div class="op_single_selector"]
> * [SSIS](sql-data-warehouse-load-from-sql-server-with-integration-services.md)
> * [PolyBase;](sql-data-warehouse-load-from-sql-server-with-polybase.md)
> * [bcp](sql-data-warehouse-load-from-sql-server-with-bcp.md)
> 
> 

Служебную программу командной строки bcp можно использовать, чтобы экспортировать небольшие наборы данных из SQL Server, а потом загрузить их непосредственно в хранилище данных SQL.

В этом руководстве описывается, как с помощью bcp выполнять следующие действия:

* экспортировать таблицу из SQL Server с помощью команды bcp out (или создавать простой пример файла);
* импортировать таблицу из неструктурированного файла в хранилище данных SQL;
* создавать статистику для загруженных данных.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Loading-data-into-Azure-SQL-Data-Warehouse-with-BCP/player]
> 
> 

## <a name="before-you-begin"></a>Перед началом работы
### <a name="prerequisites"></a>Предварительные требования
Для выполнения этих действий необходимо иметь следующее:

* База данных хранилища данных SQL
* установленная служебная программа командной строки bcp;
* установленная служебная программа командной строки sqlcmd.

Вы можете скачать служебные программы bcp и sqlcmd в [Центре загрузки Майкрософт][Microsoft Download Center].

### <a name="data-in-ascii-or-utf-16-format"></a>Данные в формате ASCII или UTF-16
Чтобы выполнить действия, описанные в этом руководстве, необходимо использовать данные в формате ASCII или UTF-16, так как bcp не поддерживает кодировку UTF-8. 

PolyBase поддерживает UTF-8, но еще не поддерживает UTF-16. Чтобы совместно использовать bcp и PolyBase, необходимо преобразовать данные, загруженные из SQL Server, в формат UTF-8. 

## <a name="1-create-a-destination-table"></a>1. Создание целевой таблицы
Определите в хранилище данных SQL таблицу, которая станет целевой таблицей для загрузки данных. Столбцы в таблице должны соответствовать данным в каждой строке файла данных.

Чтобы создать таблицу, откройте окно командной строки и используйте sqlcmd.exe для выполнения следующей команды:

```sql
sqlcmd.exe -S <server name> -d <database name> -U <username> -P <password> -I -Q "
    CREATE TABLE DimDate2
    (
        DateId INT NOT NULL,
        CalendarQuarter TINYINT NOT NULL,
        FiscalQuarter TINYINT NOT NULL
    )
    WITH
    (
        CLUSTERED COLUMNSTORE INDEX,
        DISTRIBUTION = ROUND_ROBIN
    );
"
```


## <a name="2-create-a-source-data-file"></a>2) Создание файла источника данных
Откройте блокнот и скопируйте следующие строки данных в новый текстовый файл, а затем сохраните этот файл в локальный временный каталог (C:\Temp\DimDate2.txt). Эти данные имеют формат ASCII.

```
20150301,1,3
20150501,2,4
20151001,4,2
20150201,1,3
20151201,4,2
20150801,3,1
20150601,2,4
20151101,4,2
20150401,2,4
20150701,3,1
20150901,3,1
20150101,1,3
```

Чтобы экспортировать данные из базы данных SQL Server, откройте окно командной строки и выполните команду ниже (необязательно). Замените значения TableName, ServerName, DatabaseName, Username и Password на собственные.

```sql
bcp <TableName> out C:\Temp\DimDate2_export.txt -S <ServerName> -d <DatabaseName> -U <Username> -P <Password> -q -c -t ','
```



## <a name="3-load-the-data"></a>3. Загрузка данных
Чтобы загрузить данные, откройте окно командной строки и выполните следующую команду, подставив собственные значения имени сервера, базы данных, пользователя и пароль.

```sql
bcp DimDate2 in C:\Temp\DimDate2.txt -S <ServerName> -d <DatabaseName> -U <Username> -P <password> -q -c -t  ','
```

Используйте команду ниже, чтобы убедиться, что данные загружены правильно.

```sql
sqlcmd.exe -S <server name> -d <database name> -U <username> -P <password> -I -Q "SELECT * FROM DimDate2 ORDER BY 1;"
```

Результат должен выглядеть следующим образом:

| DateId | CalendarQuarter | FiscalQuarter |
| --- | --- | --- |
| 20150101 |1 |3 |
| 20150201 |1 |3 |
| 20150301 |1 |3 |
| 20150401 |2 |4 |
| 20150501 |2 |4 |
| 20150601 |2 |4 |
| 20150701 |3 |1 |
| 20150801 |3 |1 |
| 20150801 |3 |1 |
| 20151001 |4 |2 |
| 20151101 |4 |2 |
| 20151201 |4 |2 |

## <a name="4-create-statistics"></a>4. Создание статистики
Хранилище данных SQL пока не поддерживает автоматическое создание или автоматическое обновление статистики. Чтобы добиться максимально высокой производительности запросов, крайне важно сформировать статистические данные для всех столбцов всех таблиц после первой загрузки или после любых значительных изменений в данных. Дополнительные сведения см. в статье об [управлении статистикой][Statistics]. 

Выполните следующую команду, чтобы создать статистику для загруженной таблицы.

```sql
sqlcmd.exe -S <server name> -d <database name> -U <username> -P <password> -I -Q "
    create statistics [DateId] on [DimDate2] ([DateId]);
    create statistics [CalendarQuarter] on [DimDate2] ([CalendarQuarter]);
    create statistics [FiscalQuarter] on [DimDate2] ([FiscalQuarter]);
"
```

## <a name="5-export-data-from-sql-data-warehouse"></a>5. Экспорт данных из хранилища данных SQL
Вы можете экспортировать данные, загруженные из хранилища данных SQL, обратно в хранилище.  Для этого используется та же команда.

Однако результаты ее выполнения отличаются. Так как данные в хранилище данных SQL распределены, при их экспорте каждый вычислительный узел записывает хранящиеся на нем данные в выходной файл. Порядок данных в выходном файле может отличаться от порядка данных во входном файле.

### <a name="export-a-table-and-compare-exported-results"></a>Экспорт таблицы и сравнение результатов экспорта
Чтобы просмотреть экспортированные данные, откройте окно командной строки и выполните следующую команду, используя свои собственные параметры. Параметр ServerName — это имя логического сервера SQL Azure.

```sql
bcp DimDate2 out C:\Temp\DimDate2_export.txt -S <Server Name> -d <Database Name> -U <Username> -P <password> -q -c -t ','
```
Убедитесь, что данные экспортированы, открыв новый файл. Данные в файле должны соответствовать приведенным ниже, но могут размещаться в другом порядке.

```
20150301,1,3
20150501,2,4
20151001,4,2
20150201,1,3
20151201,4,2
20150801,3,1
20150601,2,4
20151101,4,2
20150401,2,4
20150701,3,1
20150901,3,1
20150101,1,3
```

### <a name="export-the-results-of-a-query"></a>Экспорт результатов запроса
Используйте функцию **queryout** командной строки bcp, чтобы экспортировать результаты запроса, а не всю таблицу. 

## <a name="next-steps"></a>Дальнейшие действия
Общие сведения о загрузке см. в статье [Загрузка данных в хранилище данных SQL][Load data into SQL Data Warehouse].
Дополнительные советы по разработке см. в статье [Проектные решения и методики программирования для хранилища данных SQL][SQL Data Warehouse development overview].
Дополнительные сведения о создании таблицы в хранилище данных SQL см. в статьях, посвященных [таблицам в хранилище данных SQL][Table Overview] и [синтаксису инструкции CREATE TABLE][CREATE TABLE syntax].

<!--Image references-->

<!--Article references-->

[Load data into SQL Data Warehouse]: ./sql-data-warehouse-overview-load.md
[SQL Data Warehouse development overview]: ./sql-data-warehouse-overview-develop.md
[Table Overview]: ./sql-data-warehouse-tables-overview.md
[Statistics]: ./sql-data-warehouse-tables-statistics.md

<!--MSDN references-->
[bcp]: https://msdn.microsoft.com/library/ms162802.aspx
[CREATE TABLE syntax]: https://msdn.microsoft.com/library/mt203953.aspx

<!--Other Web references-->
[Microsoft Download Center]: https://www.microsoft.com/download/details.aspx?id=36433


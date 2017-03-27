---
title: "Загрузка данных из SQL Server в хранилище данных SQL Azure (PolyBase) | Документация Майкрософт"
description: "Экспорт данных из SQL Server в неструктурированные файлы, AZCopy, а также импорт данных в хранилище BLOB-объектов Azure и PolyBase для приема данных в хранилище данных SQL Azure с помощью программы bcp."
services: sql-data-warehouse
documentationcenter: NA
author: ckarst
manager: barbkess
editor: 
ms.assetid: 4d42786a-fb28-43c9-9c3b-72d19c0ecc11
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 10/31/2016
ms.author: cakarst;barbkess
ms.custom: loading
translationtype: Human Translation
ms.sourcegitcommit: 424d8654a047a28ef6e32b73952cf98d28547f4f
ms.openlocfilehash: 21b4cc704e271ac220fd606305f8f97c9b2593bb
ms.lasthandoff: 03/22/2017


---
# <a name="load-data-from-sql-server-into-azure-sql-data-warehouse-azcopy"></a>Загрузка данных из SQL Server в хранилище данных SQL Azure (AZCopy)
Для загрузки данных из SQL Server в хранилище BLOB-объектов Azure следует использовать служебные программы командной строки bcp и AZCopy. Затем для загрузки данных в хранилище данных SQL Azure используйте PolyBase или фабрику данных Azure. 

## <a name="prerequisites"></a>Предварительные требования
Для выполнения этих действий необходимо иметь следующее:

* База данных хранилища данных SQL
* Установленная служебная программа командной строки bcp
* Установленная служебная программа командной строки SQLCMD.

> [!NOTE]
> Вы можете скачать служебные программы bcp и sqlcmd в [Центре загрузки Майкрософт][Microsoft Download Center].
> 
> 

## <a name="import-data-into-sql-data-warehouse"></a>Импорт данных в хранилище данных SQL
Работая с этим учебником, вы создадите таблицу в хранилище данных SQL Azure и импортируете в нее данные.

### <a name="step-1-create-a-table-in-azure-sql-data-warehouse"></a>Шаг 1. Создание таблицы в хранилище данных SQL Azure
В командной строке выполните следующий запрос, чтобы создать таблицу для экземпляра с помощью sqlcmd:

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

> [!NOTE]
> Дополнительные сведения о создании таблицы в хранилище данных SQL и параметрах, доступных в предложении WITH, см. в статьях, посвященных [таблицам в хранилище данных SQL][Table Overview] и [синтаксису инструкции CREATE TABLE][CREATE TABLE syntax].
> 
> 

### <a name="step-2-create-a-source-data-file"></a>Шаг 2. Создание файла источника данных
Откройте блокнот и скопируйте следующие строки данных в новый текстовый файл, а затем сохраните этот файл в локальный временный каталог (C:\Temp\DimDate2.txt).

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

> [!NOTE]
> Важно помнить, что bcp.exe не поддерживает кодировку UTF-8. Используйте файлы в кодировке ASCII или UTF-16 для файлов при работе со средством bcp.exe.
> 
> 

### <a name="step-3-connect-and-import-the-data"></a>Шаг 3. Подключение и импорт данных
С помощью программы bcp подключитесь и импортируйте данные, для чего замените в следующей команде значения соответствующим образом.

```sql
bcp DimDate2 in C:\Temp\DimDate2.txt -S <Server Name> -d <Database Name> -U <Username> -P <password> -q -c -t  ','
```

Вы можете убедиться, что данные загружены, выполнив следующий запрос с помощью sqlcmd:

```sql
sqlcmd.exe -S <server name> -d <database name> -U <username> -P <password> -I -Q "SELECT * FROM DimDate2 ORDER BY 1;"
```

Вы получите следующие результаты:

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

### <a name="step-4-create-statistics-on-your-newly-loaded-data"></a>Шаг 4. Создание статистики для вновь загруженных данных
Хранилище данных SQL Azure пока не поддерживает автоматическое создание или автоматическое обновление статистики. Чтобы добиться максимально высокой производительности запросов, крайне важно сформировать статистические данные для всех столбцов всех таблиц после первой загрузки или после любых значительных изменений в данных. Подробные сведения о работе со статистикой см. в статье [Управление статистикой таблиц в хранилище данных SQL][Statistics]. Ниже приведен краткий пример создания статистики по табличным данным, загруженным в этом примере.

Выполните следующие операторы CREATE STATISTICS из командной строки sqlcmd:

```sql
sqlcmd.exe -S <server name> -d <database name> -U <username> -P <password> -I -Q "
    create statistics [DateId] on [DimDate2] ([DateId]);
    create statistics [CalendarQuarter] on [DimDate2] ([CalendarQuarter]);
    create statistics [FiscalQuarter] on [DimDate2] ([FiscalQuarter]);
"
```

## <a name="export-data-from-sql-data-warehouse"></a>Экспорт данных из хранилища данных SQL
Работая с этим учебником, вы создадите файл данных из таблицы, находящейся в хранилище данных SQL. Созданные данные будут экспортированы в новый файл данных DimDate2_export.txt.

### <a name="step-1-export-the-data"></a>Шаг 1. Экспорт данных
С помощью программы bcp подключитесь и экспортируйте данные, для чего замените в следующей команде значения соответствующим образом.

```sql
bcp DimDate2 out C:\Temp\DimDate2_export.txt -S <Server Name> -d <Database Name> -U <Username> -P <password> -q -c -t ','
```
Убедитесь, что данные экспортированы, открыв новый файл. Данные в файле должны совпадать с приведенным далее текстом:

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

> [!NOTE]
> Из-за особенностей распределенных систем порядок данных, взятых из разных баз данных хранилища данных SQL, может не совпадать. Другой вариант — вместо того, чтобы экспортировать всю таблицу, вы можете использовать в bcp функцию **queryout** , чтобы создать запрос на извлечение.
> 
> 

## <a name="next-steps"></a>Дальнейшие действия
Общие сведения о загрузке см. в статье [Загрузка данных в хранилище данных SQL][Load data into SQL Data Warehouse].
Дополнительные советы по разработке см. в статье [Проектные решения и методики программирования для хранилища данных SQL][SQL Data Warehouse development overview].

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


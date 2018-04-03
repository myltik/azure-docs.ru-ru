---
title: Использование программы bcp для загрузки данных в хранилище данных SQL | Документация Майкрософт
description: Узнайте, что такое программа bcp и как ее использовать в различных сценариях работы с хранилищем данных.
services: sql-data-warehouse
documentationcenter: NA
author: ckarst
manager: barbkess
editor: ''
ms.assetid: f9467d11-fcd6-4131-a65a-2022d2c32d24
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: loading
ms.date: 01/22/2018
ms.author: cakarst;barbkess
ms.openlocfilehash: 752a2f0d7b49f9e053788b26e3df389246c72a73
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/23/2018
---
# <a name="load-data-with-bcp"></a>Загрузка данных с помощью bcp

**[bcp](/sql/tools/bcp-utility.md)** — это программа командной строки для массовой загрузки, которая позволяет копировать данные между SQL Server, файлами данных и хранилищем данных SQL. С помощью программы bcp можно выполнять импорт большого количества строк в таблицы хранилища данных SQL или экспорт данных из таблиц SQL Server в файлы данных. За исключением случаев использования с параметром queryout, для работы с программой bcp не требуется знание языка Transact-SQL.

Программа bcp — это быстрый и простой способ перемещения небольших наборов данных в базу данных хранилища данных SQL и из нее. Точный объем данных, который рекомендуется загружать или извлекать с помощью bcp, зависит от сетевого подключения с Azure.  Небольшие таблицы измерений можно загружать и легко извлекать с помощью bcp. Тем не менее для загрузки и извлечения больших объемов данных рекомендуется использовать PolyBase, а не bcp. PolyBase предназначен для архитектуры вычислений с массовым параллелизмом хранилища данных SQL.

С помощью программы bcp можно выполнять следующие действия.

* Использовать программу командной строки для загрузки данных в хранилище данных SQL.
* Использовать программу командной строки для извлечения данных из хранилища данных SQL.

В этом руководстве рассматривается:

* Импорт данных в таблицу с помощью команды bcp in.
* Экспорт данных из таблицы с помощью команды bcp out.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Loading-data-into-Azure-SQL-Data-Warehouse-with-BCP/player]
> 
> 

## <a name="prerequisites"></a>предварительным требованиям
Для выполнения этих действий необходимо иметь следующее:

* База данных хранилища данных SQL
* Программы командной строки bcp и sqlcmd. Их можно скачать в [Центре загрузки Майкрософт](https://www.microsoft.com/download/details.aspx?id=36433). 

### <a name="data-in-ascii-or-utf-16-format"></a>Данные в формате ASCII или UTF-16
Чтобы выполнить действия, описанные в этом руководстве, необходимо использовать данные в формате ASCII или UTF-16, так как bcp не поддерживает кодировку UTF-8. 

PolyBase поддерживает UTF-8, но еще не поддерживает UTF-16. Чтобы использовать bcp для экспорта данных, а затем PolyBase для их загрузки, необходимо преобразовать данные в кодировку UTF-8 после экспорта из SQL Server. 

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

Дополнительные сведения о создании таблицы см. в статье [Общие сведения о проектировании таблиц в хранилище данных SQL Azure](sql-data-warehouse-tables-overview.md) или [синтаксисе инструкции CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse.md).
 

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
| 20150401 |2 |4. |
| 20150501 |2 |4. |
| 20150601 |2 |4. |
| 20150701 |3 |1 |
| 20150801 |3 |1 |
| 20150801 |3 |1 |
| 20151001 |4. |2 |
| 20151101 |4. |2 |
| 20151201 |4. |2 |

### <a name="step-4-create-statistics-on-your-newly-loaded-data"></a>Шаг 4. Создание статистики для вновь загруженных данных
Последний шаг после загрузки данных — создание или обновление статистики. Это нужно для повышения скорости обработки запросов. Дополнительные сведения см. в статье [Managing statistics on tables in SQL Data Warehouse](sql-data-warehouse-tables-statistics.md) (Управление статистикой таблиц в хранилище данных SQL). В следующем примере sqlcmd создает статистику для таблицы, содержащей недавно загруженные данные.


```sql
sqlcmd.exe -S <server name> -d <database name> -U <username> -P <password> -I -Q "
    create statistics [DateId] on [DimDate2] ([DateId]);
    create statistics [CalendarQuarter] on [DimDate2] ([CalendarQuarter]);
    create statistics [FiscalQuarter] on [DimDate2] ([FiscalQuarter]);
"
```

## <a name="export-data-from-sql-data-warehouse"></a>Экспорт данных из хранилища данных SQL
Работая с этим руководством, вы создадите файл данных из таблицы, находящейся в хранилище данных SQL. Таким образом вы экспортируете данные, импортированные в предыдущем разделе. Результаты направляются в файл с именем DimDate2_export.txt.

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

## <a name="next-steps"></a>Дополнительная информация
Чтобы спланировать процесс загрузки, ознакомьтесь с [рекомендациями по загрузке данных в хранилище данных SQL Azure](guidance-for-loading-data.md).  



<!--MSDN references-->



<!--Other Web references-->
[Microsoft Download Center]: https://www.microsoft.com/download/details.aspx?id=36433

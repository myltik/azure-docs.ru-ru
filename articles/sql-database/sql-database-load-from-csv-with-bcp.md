---
title: "Загрузка данных из CSV-файла в базу данных SQL Azure (с использованием bcp) | Документация Майкрософт"
description: "Для импорта небольших объемов данных в базу данных SQL Azure используйте программу bcp."
services: sql-database
documentationcenter: NA
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 875f9b8d-f1a1-4895-b717-f45570fb7f80
ms.service: sql-database
ms.custom: load & move data
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 01/10/2017
ms.author: carlrab
ms.openlocfilehash: 84bebab7763bb21f73880a6c8b367a62b0c137d3
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/18/2017
---
# <a name="load-data-from-csv-into-azure-sql-database-flat-files"></a>Загрузка данных из CSV-файла в базу данных SQL Azure (неструктурированные файлы)
Для импорта данных из CSV-файла в базу данных SQL Azure можно использовать программу командной строки bcp.

## <a name="before-you-begin"></a>Перед началом работы
### <a name="prerequisites"></a>Предварительные требования
Для выполнения задач из этой статьи необходимо следующее:

* Логический сервер и база данных SQL Azure
* установленная служебная программа командной строки bcp;
* установленная служебная программа командной строки sqlcmd.

Вы можете скачать служебные программы bcp и sqlcmd в [Центре загрузки Майкрософт][Microsoft Download Center].

### <a name="data-in-ascii-or-utf-16-format"></a>Данные в формате ASCII или UTF-16
Чтобы выполнить действия, описанные в этом руководстве, необходимо использовать данные в формате ASCII или UTF-16, так как bcp не поддерживает кодировку UTF-8. 

## <a name="1-create-a-destination-table"></a>1. Создание целевой таблицы
Определите таблицу в базе данных SQL как целевую таблицу. Столбцы в таблице должны соответствовать данным в каждой строке файла данных.

Чтобы создать таблицу, откройте окно командной строки и используйте sqlcmd.exe для выполнения следующей команды:

```sql
sqlcmd.exe -S <server name> -d <database name> -U <username> -P <password> -I -Q "
    CREATE TABLE DimDate2
    (
        DateId INT NOT NULL,
        CalendarQuarter TINYINT NOT NULL,
        FiscalQuarter TINYINT NOT NULL
    )
    ;
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

```bcp
bcp <TableName> out C:\Temp\DimDate2_export.txt -S <ServerName> -d <DatabaseName> -U <Username> -P <Password> -q -c -t , 
```

## <a name="3-load-the-data"></a>3. Загрузка данных
Чтобы загрузить данные, откройте окно командной строки и выполните следующую команду, подставив собственные значения имени сервера, базы данных, пользователя и пароль.

```bcp
bcp DimDate2 in C:\Temp\DimDate2.txt -S <ServerName> -d <DatabaseName> -U <Username> -P <password> -q -c -t  ,
```

Используйте команду ниже, чтобы убедиться, что данные загружены правильно.

```bcp
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

## <a name="next-steps"></a>Дальнейшие действия
Миграция базы данных SQL Server описана в статье [Миграция базы данных SQL Server в базу данных SQL в облаке](sql-database-cloud-migrate.md).

<!--MSDN references-->
[bcp]: https://msdn.microsoft.com/library/ms162802.aspx
[CREATE TABLE syntax]: https://msdn.microsoft.com/library/mt203953.aspx

<!--Other Web references-->
[Microsoft Download Center]: https://www.microsoft.com/download/details.aspx?id=36433

<properties
   pageTitle="Загрузка данных из CSV-файла в базу данных SQL Azure (с использованием bcp) | Microsoft Azure"
   description="Для импорта небольших объемов данных в базу данных SQL Azure используйте программу bcp."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="CarlRabeler"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/30/2016"
   ms.author="carlrab"/>


# Загрузка данных из CSV-файла в хранилище данных SQL Azure (неструктурированные файлы)

Для импорта данных из CSV-файла в базу данных SQL Azure можно использовать программу командной строки bcp.

## Перед началом работы

### Предварительные требования

Для выполнения этих действий необходимо иметь следующее:

- Логический сервер и база данных SQL Azure
- установленная служебная программа командной строки bcp;
- установленная служебная программа командной строки sqlcmd.

Вы можете загрузить служебные программы bcp и sqlcmd в [Центре загрузки Майкрософт][].

### Данные в формате ASCII или UTF-16

Чтобы выполнить действия, описанные в этом руководстве, необходимо использовать данные в формате ASCII или UTF-16, так как bcp не поддерживает кодировку UTF-8.

PolyBase поддерживает UTF-8, но еще не поддерживает UTF-16. Чтобы совместно использовать bcp и PolyBase, необходимо преобразовать данные, загруженные из SQL Server, в формат UTF-8.


## 1\. Создание целевой таблицы

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
    ;
"
```


## 2\. Создание файла источника данных

Откройте блокнот и скопируйте следующие строки данных в новый текстовый файл, а затем сохраните этот файл в локальный временный каталог (C:\\Temp\\DimDate2.txt). Эти данные имеют формат ASCII.

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

## 3\. Загрузка данных
Чтобы загрузить данные, откройте окно командной строки и выполните следующую команду, подставив собственные значения имени сервера, базы данных, пользователя и пароль.

```sql
bcp DimDate2 in C:\Temp\DimDate2.txt -S <ServerName> -d <DatabaseName> -U <Username> -P <password> -q -c -t  ','
```

Используйте команду ниже, чтобы убедиться, что данные загружены правильно.

```sql
sqlcmd.exe -S <server name> -d <database name> -U <username> -P <password> -I -Q "SELECT * FROM DimDate2 ORDER BY 1;"
```

Результат должен выглядеть следующим образом:

DateId |CalendarQuarter |FiscalQuarter
----------- |--------------- |-------------
20150101 |1 |3
20150201 |1 |3
20150301 |1 |3
20150401 |2 |4\.
20150501 |2 |4\.
20150601 |2 |4\.
20150701 |3 |1
20150801 |3 |1
20150801 |3 |1
20151001 |4\. |2
20151101 |4\. |2
20151201 |4\. |2


## Дальнейшие действия

Миграция базы данных SQL Server описана в статье [Миграция базы данных SQL Server в базу данных SQL в облаке](sql-database-cloud-migrate.md).

<!--MSDN references-->
[bcp]: https://msdn.microsoft.com/library/ms162802.aspx
[CREATE TABLE syntax]: https://msdn.microsoft.com/library/mt203953.aspx

<!--Other Web references-->
[Центре загрузки Майкрософт]: https://www.microsoft.com/download/details.aspx?id=36433

<!---HONumber=AcomDC_0803_2016-->
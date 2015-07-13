<properties
   pageTitle="Учебник по работе с PolyBase в хранилище данных SQL | Microsoft Azure"
   description="Узнайте, что такое средство PolyBase и как его использовать в сценариях работы с хранилищем данных."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="barbkess"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="05/09/2015"
   ms.author="sahajs;barbkess"/>


# Загрузка данных с помощью PolyBase
Технология PolyBase позволяет создавать запросы и объединять данные из разных источников с помощью команд Transact-SQL.

PolyBase позволяет запрашивать данные в хранилище больших двоичных объектов Azure и загружать их в базу данных хранилища данных SQL с помощью следующих действий.

- Создание главного ключа базы данных и учетных данных.
- Создание объектов PolyBase: внешний источник данных, формат внешнего файла и внешняя таблица. 
- Отправка запроса к данным в хранилище больших двоичных объектов Azure.
- Загрузка данных из хранилища больших двоичных объектов Azure в хранилище данных SQL.


## Предварительные требования
Для выполнения этих действий необходимо иметь следующее:

- Учетная запись хранения Azure.
- Данные, хранимые в хранилище больших двоичных объектов Azure в виде текстовых файлов с разделителями.

Сначала вы создадите объекты, которые нужны PolyBase для подключения к данным и создания запросов в хранилище больших двоичных объектов Azure.

## Создание главного ключа базы данных
Подключитесь к главной базе данных на сервере, чтобы создать главный ключ базы данных. Этот ключ используется для шифрования учетных данных на следующем этапе.

```
-- Creating master key
CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'S0me!nfo';
```

Раздел справки: [СОЗДАНИЕ ГЛАВНОГО КЛЮЧА (Transact-SQL)][].

## Создание учетных данных для определенной базы данных
Для доступа к хранилищу больших двоичных объектов Azure необходимо создать учетные данные для конкретной базы данных, которые будут хранить сведения для проверки подлинности учетной записи хранения Azure. Подключитесь к базе данных хранилища данных и создайте учетные данные базы данных для каждой учетной записи хранения Azure, которая будет использоваться. Укажите имя удостоверения и ключ учетной записи хранения Azure в качестве секрета. Имя удостоверения не влияет на процесс проверки подлинности в хранилище Azure.

```
-- Creating credential
CREATE DATABASE SCOPED CREDENTIAL ASBSecret WITH IDENTITY = 'joe', 
	Secret = 'myazurestoragekey==';
```

Раздел справки: [CREATE CREDENTIAL (Transact-SQL)][].

При смене ключей учетных записей хранения Azure потребуется сбросить учетные данные и снова создать их с помощью нового ключа в качестве секрета.

```
-- Dropping credential
DROP DATABASE SCOPED CREDENTIAL ASBSecret;
```

Раздел справки: [DROP CREDENTIAL (Transact-SQL)][].


## Создание внешнего источника данных
Внешний источник данных — это объект базы данных, который хранит расположение данных хранилища больших двоичных объектов Azure и сведения о доступе. Необходимо определить источник данных для каждого контейнера хранилища Azure.

```
-- Creating external data source (Azure Blob Storage) 
CREATE EXTERNAL DATA SOURCE azure_storage 
WITH (
	TYPE = HADOOP, 
       LOCATION ='wasbs://mycontainer@ test.blob.core.windows.net/path’,
      CREDENTIAL = ASBSecret
);
```

Раздел справки: [CREATE EXTERNAL DATA SOURCE (Transact-SQL)][].

## Создание формата внешнего файла
Формат внешнего файла является объектом базы данных, который указывает формат внешних данных. В данном случае у нас есть несжатые данные в текстовом файле, поля в котором разделены с помощью символа вертикальной черты (|).

```
-- Creating external file format (delimited text file)
CREATE EXTERNAL FILE FORMAT text_file_format 
WITH (
	FORMAT_TYPE = DELIMITEDTEXT, 
	FORMAT_OPTIONS (
		FIELD_TERMINATOR ='|', 
		USE_TYPE_DEFAULT = TRUE
	)
);
```

PolyBase может работать со сжатыми и несжатыми данными в виде текста с разделителями в формате Hive RCFILE и HIVE ORC.

Раздел справки: [CREATE EXTERNAL FILE FORMAT (Transact-SQL)][].

## Создание внешней таблицы

Определение внешней таблицы схоже с определением реляционной таблицы. Ключевая разница — расположение и формат данных. Определение внешней таблицы хранится в базе данных хранилища данных SQL. Данные хранятся в расположении, указанном источником данных.

Параметр LOCATION указывает путь к данным из корня источника данных. В этом примере данные располагаются по адресу wasbs://mycontainer@ test.blob.core.windows.net/path/Demo/.

```
-- Creating external table pointing to file stored in Azure Storage
CREATE EXTERNAL TABLE [ext].[CarSensor_Data] (
    [SensorKey] int NOT NULL, 
    [CustomerKey] int NOT NULL, 
    [GeographyKey] int NULL, 
    [Speed] float NOT NULL, 
    [YearMeasured] int NOT NULL
)
WITH (LOCATION='/Demo/',
      DATA_SOURCE = azure_storage,
      FILE_FORMAT = text_file_format,      
);
```

> [AZURE.NOTE]Обратите внимание, что в настоящее время нельзя создавать статистику по внешней таблице.

Все файлы для одной таблицы должны находиться в одной логической папке в большом двоичном объекте Azure. Рекомендуется разделять данные хранилища Azure на файлы объемом не более 1 ГБ, если это возможно, для параллельной обработки в хранилище данных SQL.

Раздел справки: [CREATE EXTERNAL TABLE (Transact-SQL)][].

Объекты, созданные недавно, хранятся в базе данных хранилища данных SQL. Их можно просмотреть в обозревателе объектов средств SQL Server Data Tools (SSDT).


## Отправка запроса для данных хранилища больших двоичных объектов Azure
Запросы к внешним таблицам используют имя таблицы так, как будто это реляционная таблица.

Это неструктурированный запрос, который объединяет данные о клиентах страховой компании из хранилища данных SQL с данными автомобильных датчиков из большого двоичного объекта хранилища Azure. Результат содержит водителей, которые водят быстрее других.

```
-- Join SQL Data Warehouse relational data with Azure storage data. 
SELECT 
    [Insured_Customers].[FirstName],
    [Insured_Customers].[LastName],
    [Insured_Customers].[YearlyIncome],
    [CarSensor_Data].[Speed]
FROM [dbo].[Insured_Customers] INNER JOIN [ext].[CarSensor_Data]
ON [Insured_Customers].[CustomerKey] = [CarSensor_Data].[CustomerKey]
WHERE [CarSensor_Data].[Speed] > 60 
ORDER BY [CarSensor_Data].[Speed] desc;
```

## Загрузка данных из хранилища больших двоичных объектов Azure
В этом примере данные загружаются из хранилища больших двоичных объектов Azure в базу данных хранилища данных SQL.

Прямое хранение данных сокращает время переноса данных для запроса. Хранение данных с индексом columnstore повышает производительность аналитических запросов до 10 раз.

В этом примере для загрузки данных используется инструкция CREATE TABLE AS SELECT. Новая таблица наследует столбцы с именами, указанные в запросе. Таблица наследует типы данных столбцов из определения внешней таблицы.

CREATE TABLE AS SELECT — это высокопроизводительная инструкция Transact-SQL, которая заменяет инструкцию INSERT...SELECT. Инструкция была изначально разработана для подсистемы MPP в Analytics Platform System и теперь используется в хранилище данных SQL.

```
-- Load data from Azure blob storage to SQL Data Warehouse 

CREATE TABLE [dbo].[Customer_Speed]
WITH (
	CLUSTERED COLUMNSTORE INDEX
	DISTRIBUTION = HASH([CarSensor_Data].[CustomerKey])
	)
AS SELECT * from [ext].[CarSensor_Data];
```

См. раздел [CREATE TABLE AS SELECT (Transact-SQL)][].


## Ограничения
При загрузке с помощью PolyBase поддерживается только кодировка UTF-8. При наличии других кодировок, например UTF-16, используйте программу bcp, службы SSIS или фабрику данных Azure для загрузки данных в базу данных хранилища данных SQL.

## Дальнейшие действия
Дополнительные советы по разработке см. в статье [Общие сведения о разработке][].

<!--Image references-->

<!--Article references-->
[Load data with bcp]: sql-data-warehouse-load-with-bcp.md
[Load with PolyBase]: sql-data-warehouse-load-with-polybase.md
[solution partners]: sql-data-warehouse-solution-partners.md
[Общие сведения о разработке]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->
[supported source/sink]: https://msdn.microsoft.com/library/dn894007.aspx
[copy activity]: https://msdn.microsoft.com/library/dn835035.aspx
[SQL Server destination adapter]: https://msdn.microsoft.com/library/ms141095.aspx
[SSIS]: https://msdn.microsoft.com/library/ms141026.aspx


<!-- External Links -->
[CREATE EXTERNAL DATA SOURCE (Transact-SQL)]: https://msdn.microsoft.com/library/dn935022(v=sql.130).aspx
[CREATE EXTERNAL FILE FORMAT (Transact-SQL)]: https://msdn.microsoft.com/library/dn935026(v=sql.130).aspx
[CREATE EXTERNAL TABLE (Transact-SQL)]: https://msdn.microsoft.com/library/dn935021(v=sql.130).aspx
[CREATE TABLE AS SELECT (Transact-SQL)]: https://msdn.microsoft.com/library/mt204041.aspx
[СОЗДАНИЕ ГЛАВНОГО КЛЮЧА (Transact-SQL)]: https://msdn.microsoft.com/ru-ru/library/ms174382.aspx
[CREATE CREDENTIAL (Transact-SQL)]: https://msdn.microsoft.com/ru-ru/library/ms189522.aspx
[DROP CREDENTIAL (Transact-SQL)]: https://msdn.microsoft.com/ru-ru/library/ms189450.aspx

<!---HONumber=July15_HO1-->
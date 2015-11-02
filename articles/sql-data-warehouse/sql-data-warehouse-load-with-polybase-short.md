<properties
   pageTitle="Учебник по загрузке данных в PolyBase | Microsoft Azure"
   description="Узнайте, как загружать данные в хранилище данных SQL с помощью PolyBase."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sahajs"
   manager="jhubbard"
   editor="sahajs"/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="10/20/2015"
   ms.author="sahajs;barbkess"/>


# Загрузка данных с помощью PolyBase
В этом учебнике показано, как загружать данные в хранилище данных SQL Azure с помощью PolyBase.


## Предварительные требования
Для выполнения этих действий необходимо иметь следующее.

- База данных хранилища данных SQL
- Учетная запись хранения Azure


## Шаг 1. Создание файла источника данных
Откройте Блокнот и скопируйте следующие строки данных в новый файл. Сохраните файл в локальный временный каталог C:\\Temp\\DimDate2.txt.

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


## Шаг 2. Перенос данных в хранилище больших двоичных объектов Azure

- Загрузите [последнюю версию AzCopy][].
- Откройте окно командной строки и перейдите к каталогу установки AzCopy на компьютере, где располагается исполняемый файл AzCopy.exe. По умолчанию установка выполняется в папку %ProgramFiles(x86)%\\Microsoft SDKs\\Azure\\AzCopy\\ на компьютере с 64-разрядной ОС Windows.
- Выполните следующую команду, чтобы передать файл: Укажите ключ учетной записи хранения Azure для параметра /DestKey.

```
.\AzCopy.exe /Source:C:\Temp\ /Dest:https://pbdemostorage.blob.core.windows.net/datacontainer/datedimension/ /DestKey:<azure_storage_account_key> /Pattern:DimDate2.txt
```

Дополнительные сведения об использовании AzCopy см. в статье [Приступая к работе со служебной программой командной строки AzCopy][].


## Шаг 3. Создание внешних таблиц

Теперь необходимо создать в хранилище данных SQL внешние таблицы, которые будут ссылаться на данные в хранилище больших двоичных объектов Azure. Для создания внешней таблицы выполните указанные ниже действия.

- [Создание главного ключа][]\: требуется для шифрования учетных данных, позволяющих получить доступ к базе данных.
- [Создание учетных данных для базы данных]\: требуется для указания сведений о проверке подлинности для учетной записи хранения Azure.
- [Создания внешнего источника данных]\: позволяет указать расположение хранилища больших двоичных объектов Azure.
- [Создание формата внешних файлов]\: позволяет указать формат данных.
- [Создание внешней таблицы]\: позволяет настроить ссылки на данные в хранилище Azure.



```

-- A: Create Master Key
-- Required to encrypt the credential secret in the next step.
CREATE MASTER KEY;


-- B: Create Database Scoped Credential
-- Provide the Azure storage account key. The identity name does not affect authentication to Azure storage.
CREATE DATABASE SCOPED CREDENTIAL AzureStorageCredential 
WITH IDENTITY = 'user', 
SECRET = '<azure_storage_account_key>';


-- C: Create External Data Source
-- Specify location and credential to access your Azure blob storage.
CREATE EXTERNAL DATA SOURCE AzureStorage 
WITH (	
		TYPE = Hadoop, 
		LOCATION = 'wasbs://datacontainer@pbdemostorage.blob.core.windows.net',
		CREDENTIAL = AzureStorageCredential
); 


-- D: Create External File format 
-- Specify the layout of data stored in Azure storage blobs. 
CREATE EXTERNAL FILE FORMAT TextFile 
WITH (FORMAT_TYPE = DelimitedText, 
FORMAT_OPTIONS (FIELD_TERMINATOR = ','));


-- E: Create External Table
-- To reference data stored in Azure blob storage.
CREATE EXTERNAL TABLE dbo.DimDate2External (
	DateId INT NOT NULL, 
	CalendarQuarter TINYINT NOT NULL, 
	FiscalQuarter TINYINT NOT NULL
)
WITH (
		LOCATION='datedimension/', 
		DATA_SOURCE=AzureStorage, 
		FILE_FORMAT=TextFile
);


-- Run a query on external table to confirm that the Azure Storage data can be referenced.
SELECT count(*) FROM dbo.DimDate2External;

```



## Шаг 4. Загрузка данных в хранилище данных SQL

- Для загрузки данных в новую таблицу выполните оператор CREATE TABLE AS SELECT. Новая таблица наследует столбцы с именами, указанные в запросе. Таблица наследует типы данных столбцов из определения внешней таблицы. 
- Для загрузки данных в существующую таблицу используйте оператор INSERT...SELECT.  


```

-- Load the data from Azure blob storage to SQL Data Warehouse

CREATE TABLE dbo.DimDate2
WITH 
(   
    CLUSTERED COLUMNSTORE INDEX,
		DISTRIBUTION = ROUND_ROBIN
)
AS 
SELECT * 
FROM   [dbo].[DimDate2External];

```
См. раздел [CREATE TABLE AS SELECT (Transact-SQL)][].


Дополнительные сведения о PolyBase см. в [учебнике по работе с PolyBase в хранилище данных SQL][].


<!--Article references-->
[учебнике по работе с PolyBase в хранилище данных SQL]: sql-data-warehouse-load-with-polybase.md


<!-- External Links -->
[последнюю версию AzCopy]: http://aka.ms/downloadazcopy
[Приступая к работе со служебной программой командной строки AzCopy]: https://azure.microsoft.com/documentation/articles/storage-use-azcopy/

[Создания внешнего источника данных]: https://msdn.microsoft.com/library/dn935022(v=sql.130).aspx
[Создание формата внешних файлов]: https://msdn.microsoft.com/library/dn935026(v=sql.130).aspx
[Создание внешней таблицы]: https://msdn.microsoft.com/library/dn935021(v=sql.130).aspx
[Создание главного ключа]: https://msdn.microsoft.com/ru-RU/library/ms174382.aspx
[Создание учетных данных для базы данных]: https://msdn.microsoft.com/ru-RU/library/mt270260.aspx
[CREATE TABLE AS SELECT (Transact-SQL)]: https://msdn.microsoft.com/library/mt204041.aspx

<!---HONumber=Oct15_HO4-->
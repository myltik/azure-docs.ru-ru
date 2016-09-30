<properties
   pageTitle="Учебник по работе с PolyBase в хранилище данных SQL | Microsoft Azure"
   description="Узнайте, что такое средство PolyBase и как его использовать в сценариях работы с хранилищем данных."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="ckarst"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/30/2016"
   ms.author="cakarst;barbkess;sonyama"/>


# Загрузка данных в хранилище данных SQL с помощью PolyBase

> [AZURE.SELECTOR]
- [Фабрика данных](sql-data-warehouse-get-started-load-with-azure-data-factory.md)
- [PolyBase;](sql-data-warehouse-get-started-load-with-polybase.md)
- [BCP](sql-data-warehouse-load-with-bcp.md)

В этом руководстве показано, как загрузить данные в хранилище данных SQL с помощью AzCopy и PolyBase. Изучив руководство, вы будете знать:

- как использовать AzCopy для копирования данных в хранилище больших двоичных объектов;
- как создавать объекты базы данных для определения данных;
- как выполнять запросы T-SQL для загрузки данных.

>[AZURE.VIDEO loading-data-with-polybase-in-azure-sql-data-warehouse]

## Предварительные требования

Для выполнения этих действий необходимо иметь следующее.

- База данных хранилища данных SQL.
- Учетная запись хранилища Azure типа Standard-LRS (локально избыточное хранилище уровня «Стандартный»), Standard-GRS (геоизбыточное хранилище уровня «Стандартный») или Standard-RAGRS (геоизбыточное хранилище с доступом для чтения уровня «Стандартный»).
- Служебная программа командной строки AzCopy. Загрузите и установите [последнюю версию AzCopy][], которая входит в состав средств хранилища Microsoft Azure.

    ![Средства хранилища Azure](./media/sql-data-warehouse-get-started-load-with-polybase/install-azcopy.png)


## Шаг 1. Добавление данных в хранилище больших двоичных объектов Azure

Чтобы загрузить данные, нам нужно сначала поместить демонстрационные данные в хранилище BLOB-объектов Azure. На этом шаге мы заполним демонстрационными данными большой двоичный объект хранилища Azure. Затем с помощью PolyBase мы загрузим эти данные в базу данных хранилища данных SQL.

### О. Подготовка примера текстового файла

Создайте пример текстового файла следующим образом.

1. Откройте Блокнот и скопируйте следующие строки данных в новый файл. Сохраните файл в локальный каталог с именем %temp%\\DimDate2.txt.

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

### B. Поиск адреса конечной точки службы BLOB-объектов

Найдите конечную точку службы BLOB-объектов следующим образом.

1. На портале Azure выберите **Обзор** > **Учетные записи хранения**.
2. Щелкните учетную запись хранения, которую хотите использовать.
3. В колонке учетной записи хранения выберите «BLOB-объекты».

    ![Выбор BLOB-объектов](./media/sql-data-warehouse-get-started-load-with-polybase/click-blobs.png)

1. Сохраните URL-адрес конечной точки службы BLOB-объектов.

    ![Конечная точка службы BLOB-объектов](./media/sql-data-warehouse-get-started-load-with-polybase/blob-service.png)

### C. Поиск ключа к хранилищу данных Azure

Найдите ключ к хранилищу данных Azure следующим образом.

1. На портале Azure выберите **Обзор** > **Учетные записи хранения**.
2. Щелкните учетную запись хранения, которую вы хотите использовать.
3. Выберите **Все параметры** > **Ключи доступа**.
4. Скопируйте один из ключей доступа в буфер обмена.

    ![Копирование ключа к хранилищу данных Azure](./media/sql-data-warehouse-get-started-load-with-polybase/access-key.png)

### D. Копирование примера файла в хранилище BLOB-объектов Azure

Скопируйте сохраненные данные в хранилище BLOB-объектов Azure следующим образом.

1. Откройте командную строку и перейдите в каталог, в котором установлена программа AzCopy. Эта команда выполняет переход в каталог установки, который по умолчанию используется на 64-разрядной версии Windows.

    ```
    cd /d "%ProgramFiles(x86)%\Microsoft SDKs\Azure\AzCopy"
    ```

1. Выполните следующую команду, чтобы передать файл: Укажите URL-адрес конечной точки службы BLOB-объектов вместо <blob service endpoint URL> и ключ учетной записи хранилища Azure вместо <azure\_storage\_account\_key>.

    ```
    .\AzCopy.exe /Source:C:\Temp\ /Dest:<blob service endpoint URL> /datacontainer/datedimension/ /DestKey:<azure_storage_account_key> /Pattern:DimDate2.txt
    ```

См. также статью [Приступая к работе со служебной программой командной строки AzCopy][].

### E. Просмотр содержимого контейнера хранилища BLOB-объектов

Проверьте загрузку файла в хранилище BLOB-объектов следующим образом.

1. Вернитесь к колонке службы BLOB-объектов.
2. В разделе «Контейнеры» дважды щелкните **datacontainer**.
3. Щелкните папку **datedimension**, где хранятся ваши данные, и найдите там загруженный файл **DimDate2.txt**.
4. Чтобы просмотреть его свойства, щелкните **DimDate2.txt**.
5. Обратите внимание, что в колонке свойств BLOB-объекта есть возможность загрузки и удаления файла.

    ![Просмотр хранилища BLOB-объектов Azure](./media/sql-data-warehouse-get-started-load-with-polybase/view-blob.png)


## Шаг 2. Создание внешней таблицы для демонстрационных данных

В этом разделе мы создадим внешнюю таблицу, которая определяет демонстрационные данные.

PolyBase использует внешние таблицы для доступа к данным в хранилище BLOB-объектов. Поскольку данные хранятся не в хранилище данных SQL, PolyBase выполняет проверку подлинности для доступа к внешним данным с помощью учетных данных, заданных для базы данных.

На этом шаге нашего примера мы выполним несколько инструкций Transact-SQL для создания внешней таблицы.

- [Создание главного ключа (Transact-SQL)][] для шифрования секрета учетных данных, которые заданы для базы данных.
- [Создание учетных данных для базы данных (Transact-SQL)][], которые нужны при проверке подлинности учетной записи хранения Azure.
- [Создание внешнего источника данных (Transact-SQL)][] для указания расположения хранилища BLOB-объектов Azure.
- [Создание формата внешнего файла (Transact-SQL)][] для указания формата данных.
- [Создание внешней таблицы (Transact-SQL)][], где определяется таблица и расположение нужных данных.

Выполните этот запрос к вашей базе данных хранилища данных SQL. В схеме dbo будет создана внешняя таблица с именем DimDate2External, которая указывает на файл с демонстрационными данными DimDate2.txt в хранилище BLOB-объектов Azure.


```sql
-- A: Create a master key.
-- Only necessary if one does not already exist.
-- Required to encrypt the credential secret in the next step.

CREATE MASTER KEY;


-- B: Create a database scoped credential
-- IDENTITY: Provide any string, it is not used for authentication to Azure storage.
-- SECRET: Provide your Azure storage account key.


CREATE DATABASE SCOPED CREDENTIAL AzureStorageCredential
WITH
    IDENTITY = 'user',
    SECRET = '<azure_storage_account_key>'
;


-- C: Create an external data source
-- TYPE: HADOOP - PolyBase uses Hadoop APIs to access data in Azure blob storage.
-- LOCATION: Provide Azure storage account name and blob container name.
-- CREDENTIAL: Provide the credential created in the previous step.

CREATE EXTERNAL DATA SOURCE AzureStorage
WITH (
    TYPE = HADOOP,
    LOCATION = 'wasbs://<blob_container_name>@<azure_storage_account_name>.blob.core.windows.net',
    CREDENTIAL = AzureStorageCredential
);


-- D: Create an external file format
-- FORMAT_TYPE: Type of file format in Azure storage (supported: DELIMITEDTEXT, RCFILE, ORC, PARQUET).
-- FORMAT_OPTIONS: Specify field terminator, string delimiter, date format etc. for delimited text files.
-- Specify DATA_COMPRESSION method if data is compressed.

CREATE EXTERNAL FILE FORMAT TextFile
WITH (
    FORMAT_TYPE = DelimitedText,
    FORMAT_OPTIONS (FIELD_TERMINATOR = ',')
);


-- E: Create the external table
-- Specify column names and data types. This needs to match the data in the sample file.
-- LOCATION: Specify path to file or directory that contains the data (relative to the blob container).
-- To point to all files under the blob container, use LOCATION='.'

CREATE EXTERNAL TABLE dbo.DimDate2External (
    DateId INT NOT NULL,
    CalendarQuarter TINYINT NOT NULL,
    FiscalQuarter TINYINT NOT NULL
)
WITH (
    LOCATION='/datedimension/',
    DATA_SOURCE=AzureStorage,
    FILE_FORMAT=TextFile
);


-- Run a query on the external table

SELECT count(*) FROM dbo.DimDate2External;

```


В обозревателе объектов SQL Server в Visual Studio можно просмотреть формат внешнего файла, внешний источник данных и таблицу DimDate2External.

![Просмотр внешней таблицы](./media/sql-data-warehouse-get-started-load-with-polybase/external-table.png)

## Шаг 3. Загрузка данных в хранилище данных SQL

После создания внешней таблицы вы можете загрузить данные в новую таблицу или вставить в уже существующую.

- Чтобы загрузить данные в новую таблицу, выполните инструкцию [CREATE TABLE AS SELECT (Transact-SQL)][]. В новую таблицу будут включены столбцы с именами, указанными в запросе. Типы данных столбцов будут соответствовать типам данных в определении внешней таблицы.
- Чтобы загрузить данные в существующую таблицу, выполните инструкцию [INSERT...SELECT (Transact-SQL)][].

```sql
-- Load the data from Azure blob storage to SQL Data Warehouse

CREATE TABLE dbo.DimDate2
WITH
(   
    CLUSTERED COLUMNSTORE INDEX,
    DISTRIBUTION = ROUND_ROBIN
)
AS
SELECT * FROM [dbo].[DimDate2External];
```

## Шаг 4. Создание статистики для только что загруженных данных

Хранилище данных SQL не создает и не обновляет статистику автоматически. Поэтому после первой загрузки нужно создать статистику для каждого столбца каждой таблицы, чтобы обеспечить высокую производительность. Также важно обновлять статистику после существенных изменений данных.

Этот пример создает статистику по отдельным столбцам для новой таблицы DimDate2.

```sql
CREATE STATISTICS [DateId] on [DimDate2] ([DateId]);
CREATE STATISTICS [CalendarQuarter] on [DimDate2] ([CalendarQuarter]);
CREATE STATISTICS [FiscalQuarter] on [DimDate2] ([FiscalQuarter]);
```

Дополнительные сведения см. в статье [Управление статистикой в хранилище данных SQL][].


## Дальнейшие действия
При разработке решений на основе PolyBase будет полезно изучить [Руководство по PolyBase][].

<!--Image references-->


<!--Article references-->
[PolyBase in SQL Data Warehouse Tutorial]: ./sql-data-warehouse-get-started-load-with-polybase.md
[Load data with bcp]: ./sql-data-warehouse-load-with-bcp.md
[Управление статистикой в хранилище данных SQL]: ./sql-data-warehouse-tables-statistics.md
[Руководство по PolyBase]: ./sql-data-warehouse-load-polybase-guide.md
[Приступая к работе со служебной программой командной строки AzCopy]: ../storage/storage-use-azcopy.md
[последнюю версию AzCopy]: ../storage/storage-use-azcopy.md

<!--External references-->
[supported source/sink]: https://msdn.microsoft.com/library/dn894007.aspx
[copy activity]: https://msdn.microsoft.com/library/dn835035.aspx
[SQL Server destination adapter]: https://msdn.microsoft.com/library/ms141095.aspx
[SSIS]: https://msdn.microsoft.com/library/ms141026.aspx


[Создание внешнего источника данных (Transact-SQL)]: https://msdn.microsoft.com/library/dn935022.aspx
[Создание формата внешнего файла (Transact-SQL)]: https://msdn.microsoft.com/library/dn935026.aspx
[Создание внешней таблицы (Transact-SQL)]: https://msdn.microsoft.com/library/dn935021.aspx

[DROP EXTERNAL DATA SOURCE (Transact-SQL)]: https://msdn.microsoft.com/library/mt146367.aspx
[DROP EXTERNAL FILE FORMAT (Transact-SQL)]: https://msdn.microsoft.com/library/mt146379.aspx
[DROP EXTERNAL TABLE (Transact-SQL)]: https://msdn.microsoft.com/library/mt130698.aspx

[CREATE TABLE AS SELECT (Transact-SQL)]: https://msdn.microsoft.com/library/mt204041.aspx
[INSERT...SELECT (Transact-SQL)]: https://msdn.microsoft.com/library/ms174335.aspx
[Создание главного ключа (Transact-SQL)]: https://msdn.microsoft.com/library/ms174382.aspx
[CREATE CREDENTIAL (Transact-SQL)]: https://msdn.microsoft.com/library/ms189522.aspx
[Создание учетных данных для базы данных (Transact-SQL)]: https://msdn.microsoft.com/library/mt270260.aspx
[DROP CREDENTIAL (Transact-SQL)]: https://msdn.microsoft.com/library/ms189450.aspx

<!---HONumber=AcomDC_0907_2016--->
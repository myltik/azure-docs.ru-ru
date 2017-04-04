---
title: "Загрузка данных из Azure Data Lake Store в хранилище данных SQL | Документация Майкрософт"
description: "Узнайте, как с помощью внешних таблиц PolyBase загружать данные из Azure Data Lake Store в хранилище данных SQL Azure."
services: sql-data-warehouse
documentationcenter: NA
author: ckarst
manager: barbkess
editor: 
ms.assetid: 
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: loading
ms.date: 01/25/2017
ms.author: cakarst;barbkess
translationtype: Human Translation
ms.sourcegitcommit: a087df444c5c88ee1dbcf8eb18abf883549a9024
ms.openlocfilehash: aca0e4cfdcfb3e3ed2e69ad8153b4c965b299806
ms.lasthandoff: 03/15/2017



---
# <a name="load-data-from-azure-data-lake-store-into-sql-data-warehouse"></a>Загрузка данных из Azure Data Lake Store в хранилище данных SQL
В этой статье описываются все шаги, которые необходимо выполнить для загрузки данных из Azure Data Lake Store (ADLS) в хранилище данных SQL с использованием PolyBase.
Вы можете выполнять специализированные запросы к данным, которые хранятся в ADLS, с помощью внешних таблиц, но мы рекомендуем импортировать данные в хранилище данных SQL.
Оценка времени: 10 минут, если уже выполнены все предварительные условия.
>
Из этого учебного курса вы узнаете следующее:

1. Создание объектов внешней базы данных для загрузки данных из Azure Data Lake Store.
2. Подключение к каталогу Azure Data Lake Store.
3. Загрузка данных в хранилище данных SQL Azure.

## <a name="before-you-begin"></a>Перед началом работы
Для работы с этим руководством необходимы указанные ниже компоненты.

* Приложение Azure Active Directory для проверки подлинности между службами. Создать его помогут инструкции из [этой статьи](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-authenticate-using-active-directory).

>[!NOTE] 
> Вам нужно получить из приложения Active Directory идентификатор клиента, ключ и значение конечной точки маркера OAuth2.0, чтобы подключаться к Azure Data Lake из хранилища данных SQL. Чтобы узнать, как получить эти значения, см. статью по приведенной выше ссылке.

* SQL Server Management Studio или SQL Server Data Tools. Загрузка и подключение SSMS описаны [здесь](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-query-ssms).

* Хранилище данных SQL Azure. Чтобы создать его, выполните инструкции из этой статьи: https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-get-started-provision

* Хранилище Azure Data Lake Store, в котором не используется шифрование. Чтобы создать его, выполните инструкции из этой статьи: https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal




## <a name="configure-the-data-source"></a>Настройка источника данных
PolyBase использует внешние объекты T-SQL для определения расположения и атрибутов внешних данных. Внешние объекты содержатся в хранилище данных SQL и ссылаются на данные, которые хранятся во внешнем расположении.


###  <a name="create-a-credential"></a>Создание учетных данных
Чтобы открыть Azure Data Lake Store, нужно создать главный ключ базы данных для шифрования секрета учетных данных, который мы будем использовать на следующем шаге.
Затем вы создадите учетные данные для базы данных, которые содержат учетные данные для субъекта-службы, настроенные в AAD. Если вы ранее пользовались PolyBase для подключения к большим двоичным объектам Microsoft Azure Storage Blob, обратите внимание на отличия в синтаксисе учетных данных.
Для подключения к Azure Data Lake Store необходимо **сначала** создать приложение Azure Active Directory, затем создать ключ доступа и предоставить этому приложению доступ к ресурсу Azure Data Lake. Соответствующие инструкции приведены [здесь](https://docs.microsoft.com/en-us/azure/data-lake-store/data-lake-store-authenticate-using-active-directory).

```sql
-- A: Create a Database Master Key.
-- Only necessary if one does not already exist.
-- Required to encrypt the credential secret in the next step.
-- For more information on Master Key: https://msdn.microsoft.com/en-us/library/ms174382.aspx?f=255&MSPPError=-2147217396

CREATE MASTER KEY;


-- B: Create a database scoped credential
-- IDENTITY: Pass the client id and OAuth 2.0 Token Endpoint taken from your Azure Active Directory Application
-- SECRET: Provide your AAD Application Service Principal key.
-- For more information on Create Database Scoped Credential: https://msdn.microsoft.com/en-us/library/mt270260.aspx

CREATE DATABASE SCOPED CREDENTIAL ADLCredential
WITH
    IDENTITY = '<client_id>@<OAuth_2.0_Token_EndPoint>',
    SECRET = '<key>'
;

```


### <a name="create-the-external-data-source"></a>Создание внешнего источника данных
Используйте команду [CREATE EXTERNAL DATA SOURCE][CREATE EXTERNAL DATA SOURCE], чтобы сохранить расположение и тип данных.
URI для ADL можно найти на портале Azure или по адресу www.portal.azure.com.

```sql
-- C: Create an external data source
-- TYPE: HADOOP - PolyBase uses Hadoop APIs to access data in Azure Data Lake Store.
-- LOCATION: Provide Azure Data Lake accountname and URI
-- CREDENTIAL: Provide the credential created in the previous step.

CREATE EXTERNAL DATA SOURCE AzureDataLakeStore
WITH (
    TYPE = HADOOP,
    LOCATION = 'adl://<AzureDataLake account_name>.azuredatalake.net',
    CREDENTIAL = ADLCredential
);
```



## <a name="configure-data-format"></a>Настройка формата данных
Чтобы импортировать данные из ADLS, укажите формат внешнего файла. Эта команда принимает параметры описания данных, зависящие от формата файла.
Ниже приведен пример распространенного формата — текстовый файл с разделителями "вертикальная черта".
В документации по T-SQL можно найти полный список форматов, доступных для команды [CREATE EXTERNAL FILE FORMAT][CREATE EXTERNAL FILE FORMAT]

```sql
-- D: Create an external file format
-- FIELD_TERMINATOR: Marks the end of each field (column) in a delimited text file
-- STRING_DELIMITER: Specifies the field terminator for data of type string in the text-delimited file.
-- DATE_FORMAT: Specifies a custom format for all date and time data that might appear in a delimited text file.
-- Use_Type_Default: Store all Missing values as NULL

CREATE EXTERNAL FILE FORMAT TextFileFormat
WITH
(   FORMAT_TYPE = DELIMITEDTEXT
,    FORMAT_OPTIONS    (   FIELD_TERMINATOR = '|'
                    ,    STRING_DELIMITER = ''
                    ,    DATE_FORMAT         = 'yyyy-MM-dd HH:mm:ss.fff'
                    ,    USE_TYPE_DEFAULT = FALSE
                    )
);
```

## <a name="create-the-external-tables"></a>Создание внешних таблиц
Указав источник данных и формат файла, можно перейти к созданию внешних таблиц. Внешние таблицы являются методом взаимодействия с внешними данными. PolyBase рекурсивно обходит каталоги и считывает все файлы во всех подкаталогах исходного расположения, заданного параметром location. В следующем примере показано, как создать объект. Эту инструкцию следует изменить так, чтобы она соответствовала имеющимся у вас данным в ADLS.

```sql
-- D: Create an External Table
-- LOCATION: Folder under the ADLS root folder.
-- DATA_SOURCE: Specifies which Data Source Object to use.
-- FILE_FORMAT: Specifies which File Format Object to use
-- REJECT_TYPE: Specifies how you want to deal with rejected rows. Either Value or percentage of the total
-- REJECT_VALUE: Sets the Reject value based on the reject type.

-- DimProduct
CREATE EXTERNAL TABLE [dbo].[DimProduct_external] (
    [ProductKey] [int] NOT NULL,
    [ProductLabel] [nvarchar](255) NULL,
    [ProductName] [nvarchar](500) NULL
)
WITH
(
    LOCATION='/DimProduct/'
,   DATA_SOURCE = AzureDataLakeStore
,   FILE_FORMAT = TextFileFormat
,   REJECT_TYPE = VALUE
,   REJECT_VALUE = 0
)
;

```

## <a name="external-table-considerations"></a>Рекомендации по внешним таблицам
Создание внешней таблицы не представляет сложностей, но следует учитывать некоторые особенности.

Загрузка данных с помощью PolyBase строго типизирована. Это означает, что каждая строка принимаемых данных должна соответствовать определению в схеме таблицы.
Каждая строка, которая не соответствует определению схемы, будет отклоняться при загрузке.

С помощью параметров Reject Type и Reject Value вы можете определить, сколько строк или какой процент данных должны попасть в итоговую таблицу.
Если в ходе загрузки будет достигнуто указанное ограничение, загрузка завершится ошибкой. Чаще всего строки отклоняются из-за несоответствия определению схемы.
Например, если для столбца в схеме ошибочно указан формат int (целое число), а сами данные в файле имеют строковый формат, будут отклонены все строки.

Параметр Location указывает самый вышестоящий каталог, из которого будут считываться данные.
В нашем примере будут импортированы все данные из подкаталогов в каталоге /DimProduct/PolyBase, если они существуют.

## <a name="load-the-data"></a>Загрузка данных
Чтобы загрузить данные из Azure Data Lake Store, используйте инструкцию [CREATE TABLE AS SELECT (Transact-SQL)][CREATE TABLE AS SELECT (Transact-SQL)]. Эта инструкция (CTAS) использует строго типизированную внешнюю таблицу, которую вы создали.

Компонент CTAS создает новую таблицу и заполняет ее результатам инструкции Select. CTAS определяет новую таблицу так, чтобы в ней содержались те же столбцы и типы данных, которые были выведены инструкцией Select. Если вы выбираете все столбцы из внешней таблицы, новая таблица получает точную копию всех столбцов и типов данных этой внешней таблицы.

В нашем примере мы создаем распределенную хэш-таблицу с именем DimProduct на основе внешней таблицы DimProduct_external.

```sql

CREATE TABLE [dbo].[DimProduct]
WITH (DISTRIBUTION = HASH([ProductKey]  ) )
AS
SELECT * FROM [dbo].[DimProduct_external]
OPTION (LABEL = 'CTAS : Load [dbo].[DimProduct]');
```


## <a name="optimize-columnstore-compression"></a>Оптимизация сжатия columnstore
По умолчанию в хранилище данных SQL таблицы хранятся в виде кластеризованных индексов columnstore. После завершения загрузки для некоторых строк данных может не выполняться сжатие в индекс columnstore.  Это может происходить по ряду причин. Чтобы узнать больше, ознакомьтесь с [управлением индексами columnstore][manage columnstore indexes].

Чтобы оптимизировать производительность запросов и сжатие columnstore после загрузки, перестройте таблицу, чтобы настроить принудительное сжатие всех строк таблиц индексом columnstore.

```sql

ALTER INDEX ALL ON [dbo].[DimProduct] REBUILD;

```

Дополнительные сведения об обслуживании индексов columnstore см. в статье, посвященной [управлению индексами columnstore][manage columnstore indexes].

## <a name="optimize-statistics"></a>Оптимизация статистики
Одностолбцовую статистику рекомендуется создавать сразу после загрузки. Существует несколько вариантов статистики. Например, при создании одностолбцовой статистики для каждого столбца перестройка всех статистических данных может занять длительное время. Если вам известно, что некоторые столбцы не будут входить в предикаты запросов, можно пропустить создание статистики для этих столбцов.

Если вам потребуется создать одностолбцовую статистику для каждого столбца в каждой таблице, можно использовать пример кода хранимой процедуры `prc_sqldw_create_stats` из статьи, посвященной [статистике][statistics].

Следующий пример кода — хорошая отправная точка для создания статистики. Он создает одностолбцовую статистику для каждого столбца в таблице измерения и для каждого соответствующего столбца в таблицах фактов. Одно- или многостолбцовую статистику в другие столбцы таблицы фактов можно добавить позже.


## <a name="achievement-unlocked"></a>Победа!
Данные успешно загружены в хранилище данных SQL Azure. Отличная работа!

##<a name="next-steps"></a>Дальнейшие действия
Загрузка данных является первым шагом к разработке решения для хранения данных на основе хранилища данных SQL. Ознакомьтесь с документацией для разработчиков, посвященной [таблицам](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-overview) и [T-SQL](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-develop-loops.md).


<!--Image references-->

<!--Article references-->
[Create a SQL Data Warehouse]: sql-data-warehouse-get-started-provision.md
[Load data into SQL Data Warehouse]: sql-data-warehouse-overview-load.md
[SQL Data Warehouse development overview]: sql-data-warehouse-overview-develop.md
[manage columnstore indexes]: sql-data-warehouse-tables-index.md
[Statistics]: sql-data-warehouse-tables-statistics.md
[CTAS]: sql-data-warehouse-develop-ctas.md
[label]: sql-data-warehouse-develop-label.md

<!--MSDN references-->
[CREATE EXTERNAL DATA SOURCE]: https://msdn.microsoft.com/library/dn935022.aspx
[CREATE EXTERNAL FILE FORMAT]: https://msdn.microsoft.com/library/dn935026.aspx
[CREATE TABLE AS SELECT (Transact-SQL)]: https://msdn.microsoft.com/library/mt204041.aspx
[sys.dm_pdw_exec_requests]: https://msdn.microsoft.com/library/mt203887.aspx
[REBUILD]: https://msdn.microsoft.com/library/ms188388.aspx

<!--Other Web references-->
[Microsoft Download Center]: http://www.microsoft.com/download/details.aspx?id=36433
[Load the full Contoso Retail Data Warehouse]: https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/contoso-data-warehouse/readme.md


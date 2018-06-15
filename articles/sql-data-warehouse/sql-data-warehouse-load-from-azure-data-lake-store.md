---
title: Руководство. Загрузка из Azure Data Lake Store в хранилище данных SQL Azure | Документация Майкрософт
description: Используйте внешние таблицы PolyBase для загрузки данных из Azure Data Lake Store в хранилище данных SQL Azure.
services: sql-data-warehouse
author: ckarst
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
ms.date: 04/17/2018
ms.author: cakarst
ms.reviewer: igorstan
ms.openlocfilehash: c6030d1951c22dddfe6df01225c63cf503a370ac
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2018
ms.locfileid: "32188384"
---
# <a name="load-data-from-azure-data-lake-store-to-sql-data-warehouse"></a>Загрузка данных из Azure Data Lake Store в хранилище данных SQL
Используйте внешние таблицы PolyBase для загрузки данных из Azure Data Lake Store в хранилище данных SQL Azure. Несмотря на то, что можно запускать специальные запросы данных, хранящихся в ADLS, мы рекомендуем импортировать данные в хранилище данных SQL для обеспечения максимальной производительности.

> [!div class="checklist"]
> * Создание объектов базы данных, требуемых для загрузки данных из Azure Data Lake Store.
> * Подключение к каталогу Azure Data Lake Store.
> * Загрузка данных в хранилище данных SQL Azure.

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/), прежде чем начинать работу.

## <a name="before-you-begin"></a>Перед началом работы
Перед началом работы с этим руководством скачайте и установите последнюю версию [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) (SSMS).

Для работы с этим руководством необходимы указанные ниже компоненты.

* Приложение Azure Active Directory для проверки подлинности между службами. Создать его помогут инструкции из [этой статьи](../data-lake-store/data-lake-store-authenticate-using-active-directory.md).

>[!NOTE] 
> Вам нужно получить из приложения Active Directory идентификатор клиента, ключ и значение конечной точки маркера OAuth2.0, чтобы подключаться к Azure Data Lake из хранилища данных SQL. Чтобы узнать, как получить эти значения, см. статью по приведенной выше ссылке. Для регистрации приложения Azure Active Directory используйте идентификатор приложения как идентификатор клиента.
> 

* Хранилище данных SQL Azure Ознакомьтесь со статьей [Краткое руководство. Создание хранилища данных SQL Azure на портале Azure и отправка запросов к этому хранилищу данных](create-data-warehouse-portal.md).

* Azure Data Lake Store Ознакомьтесь со статьей [Начало работы с Azure Data Lake Store с помощью портала Azure](../data-lake-store/data-lake-store-get-started-portal.md). 

##  <a name="create-a-credential"></a>Создание учетных данных
Чтобы открыть Azure Data Lake Store, нужно создать главный ключ базы данных для шифрования секрета учетных данных, который мы будем использовать на следующем шаге. Затем вы создадите учетные данные для базы данных, которые содержат учетные данные для субъекта-службы, настроенные в AAD. Если вы ранее пользовались PolyBase для подключения к большим двоичным объектам Microsoft Azure Storage Blob, обратите внимание на отличия в синтаксисе учетных данных.

Для подключения к Azure Data Lake Store необходимо **сначала** создать приложение Azure Active Directory, затем создать ключ доступа и предоставить этому приложению доступ к ресурсу Azure Data Lake. Инструкции см. в статье [Аутентификация между службами в Data Lake Store с помощью Azure Active Directory](../data-lake-store/data-lake-store-authenticate-using-active-directory.md).

```sql
-- A: Create a Database Master Key.
-- Only necessary if one does not already exist.
-- Required to encrypt the credential secret in the next step.
-- For more information on Master Key: https://msdn.microsoft.com/library/ms174382.aspx?f=255&MSPPError=-2147217396

CREATE MASTER KEY;


-- B: Create a database scoped credential
-- IDENTITY: Pass the client id and OAuth 2.0 Token Endpoint taken from your Azure Active Directory Application
-- SECRET: Provide your AAD Application Service Principal key.
-- For more information on Create Database Scoped Credential: https://msdn.microsoft.com/library/mt270260.aspx

CREATE DATABASE SCOPED CREDENTIAL ADLCredential
WITH
    IDENTITY = '<client_id>@<OAuth_2.0_Token_EndPoint>',
    SECRET = '<key>'
;

-- It should look something like this:
CREATE DATABASE SCOPED CREDENTIAL ADLCredential
WITH
    IDENTITY = '536540b4-4239-45fe-b9a3-629f97591c0c@https://login.microsoftonline.com/42f988bf-85f1-41af-91ab-2d2cd011da47/oauth2/token',
    SECRET = 'BjdIlmtKp4Fpyh9hIvr8HJlUida/seM5kQ3EpLAmeDI='
;
```

## <a name="create-the-external-data-source"></a>Создание внешнего источника данных
Используйте команду [CREATE EXTERNAL DATA SOURCE](/sql/t-sql/statements/create-external-data-source-transact-sql), чтобы сохранить расположение данных. 

```sql
-- C: Create an external data source
-- TYPE: HADOOP - PolyBase uses Hadoop APIs to access data in Azure Data Lake Store.
-- LOCATION: Provide Azure Data Lake accountname and URI
-- CREDENTIAL: Provide the credential created in the previous step.

CREATE EXTERNAL DATA SOURCE AzureDataLakeStore
WITH (
    TYPE = HADOOP,
    LOCATION = 'adl://<AzureDataLake account_name>.azuredatalakestore.net',
    CREDENTIAL = ADLCredential
);
```

## <a name="configure-data-format"></a>Настройка формата данных
Чтобы импортировать данные из ADLS, укажите формат внешнего файла. Этот объект определяет, каким образом файлы записываются в ADLS.
Полный список форматов, доступных для команды [CREATE EXTERNAL FILE FORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql), см. в документации по T-SQL.

```sql
-- D: Create an external file format
-- FIELD_TERMINATOR: Marks the end of each field (column) in a delimited text file
-- STRING_DELIMITER: Specifies the field terminator for data of type string in the text-delimited file.
-- DATE_FORMAT: Specifies a custom format for all date and time data that might appear in a delimited text file.
-- Use_Type_Default: Store missing values as default for datatype.

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
Указав источник данных и формат файла, можно перейти к созданию внешних таблиц. Внешние таблицы являются методом взаимодействия с внешними данными. В параметре расположения можно указать файл или каталог. Если указан каталог, загружаются все содержащиеся в нем файлы.

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

Внешние таблицы строго типизированы. Это означает, что каждая строка принимаемых данных должна соответствовать определению в схеме таблицы.
Строка, которая не соответствует определению схемы, будет отклоняться при загрузке.

С помощью параметров REJECT_TYPE и REJECT_VALUE вы можете определить, сколько строк или какой процент данных должны попасть в итоговую таблицу. Если в ходе загрузки будет достигнуто указанное ограничение, загрузка завершится ошибкой. Чаще всего строки отклоняются из-за несоответствия определению схемы. Например, если для столбца в схеме ошибочно указан формат int (целое число), а сами данные в файле имеют строковый формат, будут отклонены все строки.

 Хранилище Azure Data Lake использует управление доступом на основе ролей (RBAC) для управления доступом к данным. Это означает, что субъект-служба должна иметь разрешения на чтение для каталогов, указанных в параметре расположения, и для дочерних элементов конечного каталога и файлов. Это позволяет PolyBase выполнять аутентификацию и загрузку данных. 

## <a name="load-the-data"></a>Загрузка данных
Чтобы загрузить данные из Azure Data Lake Store, используйте инструкцию [CREATE TABLE AS SELECT (Transact-SQL)](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse). 

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
По умолчанию в хранилище данных SQL таблицы хранятся в виде кластеризованных индексов columnstore. После завершения загрузки для некоторых строк данных может не выполняться сжатие в индекс columnstore.  Это может происходить по ряду причин. Чтобы узнать больше, ознакомьтесь с [управлением индексами columnstore](sql-data-warehouse-tables-index.md).

Чтобы оптимизировать производительность запросов и сжатие columnstore после загрузки, перестройте таблицу, чтобы настроить принудительное сжатие всех строк таблиц индексом columnstore.

```sql

ALTER INDEX ALL ON [dbo].[DimProduct] REBUILD;

```

## <a name="optimize-statistics"></a>Оптимизация статистики
Одностолбцовую статистику рекомендуется создавать сразу после загрузки. Существует несколько вариантов статистики. Например, при создании одностолбцовой статистики для каждого столбца перестройка всех статистических данных может занять длительное время. Если вам известно, что некоторые столбцы не будут входить в предикаты запросов, можно пропустить создание статистики для этих столбцов.

Если вам потребуется создать одностолбцовую статистику для каждого столбца в каждой таблице, можно использовать пример кода хранимой процедуры `prc_sqldw_create_stats` из статьи, посвященной [статистике](sql-data-warehouse-tables-statistics.md).

Следующий пример кода — хорошая отправная точка для создания статистики. Он создает одностолбцовую статистику для каждого столбца в таблице измерения и для каждого соответствующего столбца в таблицах фактов. Одно- или многостолбцовую статистику в другие столбцы таблицы фактов можно добавить позже.

## <a name="achievement-unlocked"></a>Победа!
Данные успешно загружены в хранилище данных SQL Azure. Отличная работа!

## <a name="next-steps"></a>Дополнительная информация 
В этом руководстве вы создали внешние таблицы для определения структуры данных, хранящихся в Azure Data Lake Store, а затем использовали инструкцию PolyBase CREATE TABLE AS SELECT для загрузки данных в хранилище данных. 

Вы выполнили такие действия:
> [!div class="checklist"]
> * Создание объектов базы данных, необходимые для загрузки данных из Azure Data Lake Store.
> * Подключение к каталогу Azure Data Lake Store.
> * Загрузка данных в хранилище данных SQL Azure.
> 

Загрузка данных является первым шагом к разработке решения для хранения данных на основе хранилища данных SQL. Ознакомьтесь с нашими ресурсами разработки.

> [!div class="nextstepaction"]
>[Общие сведения о проектировании таблиц в хранилище данных SQL Azure](sql-data-warehouse-tables-overview.md)





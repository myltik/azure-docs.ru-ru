---
title: "Перемещение данных в хранилище данных SQL Azure и из него | Документация Майкрософт"
description: "Узнайте, как переместить данные в хранилище данных SQL Azure и из него с помощью фабрики данных Azure."
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: monicar
ms.assetid: d90fa9bd-4b79-458a-8d40-e896835cfd4a
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/23/2017
ms.author: jingwang
translationtype: Human Translation
ms.sourcegitcommit: aada5b96eb9ee999c5b1f60b6e7b9840fd650fbe
ms.openlocfilehash: 2831416bbb290905835396835db2eb6cb5e7b46f
ms.lasthandoff: 02/24/2017


---
# <a name="move-data-to-and-from-azure-sql-data-warehouse-using-azure-data-factory"></a>Перемещение данных в хранилище данных Azure SQL и из него с помощью фабрики данных Azure
В этой статье описано, как с помощью действия копирования в фабрике данных Azure перемещать данные из хранилища данных SQL Azure в другое хранилище данных и наоборот. Здесь мы развиваем темы, освещенные в статье о [действиях перемещения данных](data-factory-data-movement-activities.md) , которая кратко описывает, как перемещать данные с помощью действия копирования, и содержит список исходных и целевых хранилищ данных (источников и приемников).

> [!TIP]
> Для обеспечения лучшей производительности при загрузке данных в хранилище данных SQL Azure используйте PolyBase. Дополнительные сведения см. в разделе [Загрузка данных в хранилище данных SQL Azure с помощью PolyBase](data-factory-azure-sql-data-warehouse-connector.md#use-polybase-to-load-data-into-azure-sql-data-warehouse). Пошаговое руководство и пример использования см. в статье [Загрузка 1 ТБ в хранилище данных SQL Azure с помощью фабрики данных Azure [мастер копирования] менее чем за 15 минут](data-factory-load-sql-data-warehouse.md).
>

## <a name="copy-data-wizard"></a>Мастер копирования данных
Самый простой способ создать конвейер, копирующий данные в хранилище данных SQL Azure или из него, — использовать мастер копирования данных. В статье [Загрузка данных в хранилище данных SQL с помощью фабрики данных](../sql-data-warehouse/sql-data-warehouse-load-with-data-factory.md) приведены краткие пошаговые указания по созданию конвейера с помощью мастера копирования данных.

> [!TIP]
> При копировании данных из базы данных SQL Azure или SQL Server в хранилище данных SQL Azure, фабрика данных поддерживает автоматическое создание таблицы с использованием исходной схемы, если таблица не существует в целевом хранилище. Попробуйте в деле мастер копирования и ознакомьтесь с разделом [Автоматическое создание таблицы](#auto-table-creation).
>

Ниже приведены примеры с определениями JSON, которые можно использовать для создания конвейера с помощью [портала Azure](data-factory-copy-activity-tutorial-using-azure-portal.md), [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) или [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). В них показано, как копировать данные в хранилище данных SQL Azure и хранилище BLOB-объектов Azure и обратно. Тем не менее данные можно копировать **непосредственно** из любых источников в любой указанный [здесь](data-factory-data-movement-activities.md#supported-data-stores-and-formats) приемник. Это делается с помощью действия копирования в фабрике данных Azure.

## <a name="sample-copy-data-from-azure-sql-data-warehouse-to-azure-blob"></a>Пример: копирование данных из хранилища данных SQL Azure в BLOB-объект Azure
Образец определяет следующие сущности фабрики данных.

1. Связанная служба типа [AzureSqlDW](#linked-service-properties).
2. Связанная служба типа [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service).
3. Входной [набор данных](data-factory-create-datasets.md) типа [AzureSqlDWTable](#dataset-type-properties).
4. Выходной [набор данных](data-factory-create-datasets.md) типа [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
5. Конвейер [pipeline](data-factory-create-pipelines.md) с действием копирования, в котором используются [SqlDWSource](#copy-activity-type-properties) и [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties).

В этом примере данные, относящиеся к одному временному ряду (за каждый час, каждый день и т. д.), каждый час копируются из таблицы в хранилище данных SQL Azure в большой двоичный объект. Используемые в этих примерах свойства JSON описаны в разделах, следующих за примерами.

**Связанная служба хранилища данных SQL Azure**

```JSON
{
  "name": "AzureSqlDWLinkedService",
  "properties": {
    "type": "AzureSqlDW",
    "typeProperties": {
      "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
    }
  }
}
```
**Связанная служба хранилища BLOB-объектов Azure**

```JSON
{
  "name": "StorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```
**Входной набор данных хранилища данных SQL Azure**

В примере предполагается, что вы уже создали таблицу MyTable в хранилище данных Azure SQL и она содержит столбец с именем timestampcolumn для данных временных рядов.

Если параметру external присвоить значение true, фабрика данных воспримет этот набор данных как внешний и созданный не в результате какого-либо действия в этой службе.

```JSON
{
  "name": "AzureSqlDWInput",
  "properties": {
    "type": "AzureSqlDWTable",
    "linkedServiceName": "AzureSqlDWLinkedService",
    "typeProperties": {
      "tableName": "MyTable"
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    },
    "policy": {
      "externalData": {
        "retryInterval": "00:01:00",
        "retryTimeout": "00:10:00",
        "maximumRetry": 3
      }
    }
  }
}
```
**Выходной набор данных BLOB-объекта Azure**

Данные записываются в новый BLOB-объект каждый час (frequency: hour, interval: 1). Путь к папке BLOB-объекта вычисляется динамически на основе времени начала обрабатываемого среза. В пути к папке используется год, месяц, день и час времени начала.

```JSON
{
  "name": "AzureBlobOutput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
      "partitionedBy": [
        {
          "name": "Year",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "yyyy"
          }
        },
        {
          "name": "Month",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "MM"
          }
        },
        {
          "name": "Day",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "dd"
          }
        },
        {
          "name": "Hour",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "HH"
          }
        }
      ],
      "format": {
        "type": "TextFormat",
        "columnDelimiter": "\t",
        "rowDelimiter": "\n"
      }
    },
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```

**Конвейер с действием копирования**

Конвейер содержит действие копирования, которое использует входной и выходной наборы данных и выполняется каждый час. В определении JSON конвейера для типа **source** установлено значение **SqlDWSource**, а для типа **sink** — значение **BlobSink**. SQL-запрос, указанный для свойства **SqlReaderQuery** , выбирает для копирования данные за последний час.

```JSON
{  
    "name":"SamplePipeline",
    "properties":{  
    "start":"2014-06-01T18:00:00",
    "end":"2014-06-01T19:00:00",
    "description":"pipeline for copy activity",
    "activities":[  
      {
        "name": "AzureSQLDWtoBlob",
        "description": "copy activity",
        "type": "Copy",
        "inputs": [
          {
            "name": "AzureSqlDWInput"
          }
        ],
        "outputs": [
          {
            "name": "AzureBlobOutput"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "SqlDWSource",
            "sqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
          },
          "sink": {
            "type": "BlobSink"
          }
        },
       "scheduler": {
          "frequency": "Hour",
          "interval": 1
        },
        "policy": {
          "concurrency": 1,
          "executionPriorityOrder": "OldestFirst",
          "retry": 0,
          "timeout": "01:00:00"
        }
      }
     ]
   }
}
```
> [!NOTE]
> В приведенном примере для свойства SqlDWSource указано **sqlReaderQuery** . Действие копирования выполняет этот запрос для хранилища данных SQL Azure для получения данных.
>
> Кроме того, можно создать хранимую процедуру, указав **sqlReaderStoredProcedureName** и **storedProcedureParameters** (если хранимая процедура принимает параметры).
>
> Если не указать sqlReaderQuery или sqlReaderStoredProcedureName, то для построения запроса (select column1, column2 from mytable) к хранилищу данных SQL Azure будут использованы столбцы, определенные в разделе структуры набора данных JSON. Если у определения набора данных нет структуры, выбираются все столбцы из таблицы.
>
>

## <a name="sample-copy-data-from-azure-blob-to-azure-sql-data-warehouse"></a>Пример: копирование данных из BLOB-объекта Azure в хранилище данных SQL Azure
Образец определяет следующие сущности фабрики данных.

1. Связанная служба типа [AzureSqlDW](#linked-service-properties).
2. Связанная служба типа [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service).
3. Входной [набор данных](data-factory-create-datasets.md) типа [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
4. Выходной [набор данных](data-factory-create-datasets.md) типа [AzureSqlDWTable](#dataset-type-properties).
5. Конвейер [pipeline](data-factory-create-pipelines.md) с действием копирования, которое использует [BlobSource](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties) и [SqlDWSink](#copy-activity-type-properties).

В этом примере данные, относящиеся к одному временному ряду (за каждый час, каждый день и т. д.), каждый час копируются из большого двоичного объекта Azure в таблицу в хранилище данных SQL Azure. Используемые в этих примерах свойства JSON описаны в разделах, следующих за примерами.

**Связанная служба хранилища данных SQL Azure**

```JSON
{
  "name": "AzureSqlDWLinkedService",
  "properties": {
    "type": "AzureSqlDW",
    "typeProperties": {
      "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
    }
  }
}
```
**Связанная служба хранилища BLOB-объектов Azure**

```JSON
{
  "name": "StorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```
**Входной набор данных BLOB-объекта Azure**

Данные берутся из нового BLOB-объекта каждый час (frequency: hour, interval: 1). Путь к папке с BLOB-объектом и имя файла вычисляются динамически на основе времени начала обрабатываемого среза. В пути к папке используется год, месяц и день начала, а в имени файла — час начала. Когда для параметра external задано значение true, служба фабрики данных считает эту таблицу внешней и созданной не в результате какого-либо действия в фабрике данных.

```JSON
{
  "name": "AzureBlobInput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}",
      "fileName": "{Hour}.csv",
      "partitionedBy": [
        {
          "name": "Year",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "yyyy"
          }
        },
        {
          "name": "Month",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "MM"
          }
        },
        {
          "name": "Day",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "dd"
          }
        },
        {
          "name": "Hour",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "HH"
          }
        }
      ],
      "format": {
        "type": "TextFormat",
        "columnDelimiter": ",",
        "rowDelimiter": "\n"
      }
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    },
    "policy": {
      "externalData": {
        "retryInterval": "00:01:00",
        "retryTimeout": "00:10:00",
        "maximumRetry": 3
      }
    }
  }
}
```
**Выходной набор данных хранилища данных SQL Azure**

В этом примере данные копируются в таблицу с именем MyTable в хранилище данных SQL Azure. Таблицу в хранилище данных SQL Azure необходимо создать с таким количеством столбцов, которое должно быть в CSV-файле большого двоичного объекта. Новые строки добавляются в таблицу каждый час.

```JSON
{
  "name": "AzureSqlDWOutput",
  "properties": {
    "type": "AzureSqlDWTable",
    "linkedServiceName": "AzureSqlDWLinkedService",
    "typeProperties": {
      "tableName": "MyOutputTable"
    },
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```
**Конвейер с действием копирования**

Конвейер содержит действие копирования, которое использует входной и выходной наборы данных и выполняется каждый час. В определении JSON конвейера для типа **source** установлено значение **BlobSource**, а для типа **sink** — значение **SqlDWSink**.

```JSON
{  
    "name":"SamplePipeline",
    "properties":{  
    "start":"2014-06-01T18:00:00",
    "end":"2014-06-01T19:00:00",
    "description":"pipeline with copy activity",
    "activities":[  
      {
        "name": "AzureBlobtoSQLDW",
        "description": "Copy Activity",
        "type": "Copy",
        "inputs": [
          {
            "name": "AzureBlobInput"
          }
        ],
        "outputs": [
          {
            "name": "AzureSqlDWOutput"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "BlobSource",
            "blobColumnSeparators": ","
          },
          "sink": {
            "type": "SqlDWSink",
            "allowPolyBase": true
          }
        },
       "scheduler": {
          "frequency": "Hour",
          "interval": 1
        },
        "policy": {
          "concurrency": 1,
          "executionPriorityOrder": "OldestFirst",
          "retry": 0,
          "timeout": "01:00:00"
        }
      }
      ]
   }
}
```
Пошаговое руководство см. в статьях [Загрузка 1 ТБ в хранилище данных SQL Azure с помощью фабрики данных Azure [мастер копирования] менее чем за 15 минут](data-factory-load-sql-data-warehouse.md) и [Загрузка данных с помощью фабрики данных Azure](../sql-data-warehouse/sql-data-warehouse-get-started-load-with-azure-data-factory.md) в документации по хранилищу данных SQL Azure.

## <a name="linked-service-properties"></a>Свойства связанной службы
В таблице ниже приведено описание элементов JSON, которые относятся к связанной службе хранилища данных SQL Azure.

| Свойство | Описание | Обязательно |
| --- | --- | --- |
| type |Для свойства type необходимо задать значение **AzureSqlDW** |Да |
| connectionString |Укажите сведения, необходимые для подключения к экземпляру хранилища данных SQL Azure, для свойства connectionString. |Да |

> [!IMPORTANT]
> [Брандмауэр базы данных SQL Azure](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure) необходимо настроить для [разрешения службам Azure доступа к серверу](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure). Кроме того, когда вы с помощью шлюза фабрики данных копируете данные в хранилище данных SQL Azure из внешнего по отношению к Azure источника, в том числе из локальных источников данных, необходимо настроить соответствующий диапазон IP-адресов для компьютера, который отправляет данные в хранилище данных SQL Azure.
>
>

## <a name="dataset-type-properties"></a>Свойства типа "Набор данных"
Полный список разделов и свойств, используемых для определения наборов данных, см. в статье [Наборы данных](data-factory-create-datasets.md). Разделы структуры, доступности и политики JSON набора данных одинаковы для всех типов наборов данных (SQL Azure, большие двоичные объекты Azure, таблицы Azure и т. д.).

Раздел typeProperties во всех типах наборов данных разный. В нем содержатся сведения о расположении данных в хранилище данных. Раздел **typeProperties** набора данных типа **AzureSqlDWTable** содержит следующие свойства.

| Свойство | Описание | Обязательно |
| --- | --- | --- |
| tableName |Имя таблицы или представления в базе данных хранилища данных SQL Azure, на которое ссылается связанная служба. |Да |

## <a name="copy-activity-type-properties"></a>Свойства типа "Действие копирования"
Полный список разделов и свойств, используемых для определения действий, см. в статье [Создание конвейеров](data-factory-create-pipelines.md). Свойства (включая имя, описание, входные и выходные таблицы, политику и т. д.) доступны для всех типов действий.

> [!NOTE]
> Действие копирования принимает только один набор входных данных и возвращает только один набор выходных.
>
>

С другой стороны, свойства, доступные в разделе typeProperties действия, зависят от конкретного типа действия. Для действия копирования они различаются в зависимости от типов источников и приемников.

### <a name="sqldwsource"></a>SqlDWSource
Когда источник относится к типу **SqlDWSource**, в разделе **typeProperties** доступны следующие свойства.

| Свойство | Описание | Допустимые значения | Обязательно |
| --- | --- | --- | --- |
| SqlReaderQuery |Используйте пользовательский запрос для чтения данных. |Строка запроса SQL. Например, select * from MyTable. |Нет |
| sqlReaderStoredProcedureName |Имя хранимой процедуры, которая считывает данные из исходной таблицы. |Имя хранимой процедуры. |Нет |
| storedProcedureParameters |Параметры для хранимой процедуры. |Пары имен и значений. Имена и регистр параметров должны совпадать с именами и регистром параметров хранимой процедуры. |Нет |

Если для SqlDWSource указано **sqlReaderQuery** , то действие копирования выполняет этот запрос для хранилища данных SQL Azure с целью получения данных.

Кроме того, можно создать хранимую процедуру, указав **sqlReaderStoredProcedureName** и **storedProcedureParameters** (если хранимая процедура принимает параметры).

Если не указать sqlReaderQuery или sqlReaderStoredProcedureName, то для построения запроса к хранилищу данных SQL Azure будут использованы столбцы, определенные в разделе структуры набора данных JSON. Пример: `select column1, column2 from mytable`. Если у определения набора данных нет структуры, выбираются все столбцы из таблицы.

#### <a name="sqldwsource-example"></a>Пример SqlDWSource

```JSON
"source": {
    "type": "SqlDWSource",
    "sqlReaderStoredProcedureName": "CopyTestSrcStoredProcedureWithParameters",
    "storedProcedureParameters": {
        "stringData": { "value": "str3" },
        "identifier": { "value": "$$Text.Format('{0:yyyy}', SliceStart)", "type": "Int"}
    }
}
```
**Определение хранимой процедуры:**

```SQL
CREATE PROCEDURE CopyTestSrcStoredProcedureWithParameters
(
    @stringData varchar(20),
    @identifier int
)
AS
SET NOCOUNT ON;
BEGIN
     select *
     from dbo.UnitTestSrcTable
     where dbo.UnitTestSrcTable.stringData != stringData
    and dbo.UnitTestSrcTable.identifier != identifier
END
GO
```

### <a name="sqldwsink"></a>SqlDWSink
**SqlDWSink** поддерживает указанные ниже свойства.

| Свойство | Описание | Допустимые значения | Обязательно |
| --- | --- | --- | --- |
| writeBatchSize |Вставляет данные в таблицу SQL, когда размер буфера достигает значения writeBatchSize. |Целое число (количество строк) |Нет (значение по умолчанию — 10 000). |
| writeBatchTimeout |Время ожидания до выполнения операции пакетной вставки, пока не завершится срок ее действия. |Интервал времени<br/><br/> Пример:&amp;00;:30:00 (30 минут). |Нет |
| sqlWriterCleanupScript |Укажите запрос на выполнение действия копирования, позволяющий убедиться в том, что данные конкретного среза очищены. Дополнительные сведения см. в [разделе о повторяемости](#repeatability-during-copy). |Инструкция запроса. |Нет |
| allowPolyBase |Указывает, следует ли использовать PolyBase (если применимо) вместо механизма BULKINSERT для загрузки данных в хранилище данных SQL Azure. <br/><br/>В настоящее время в качестве исходного используется только набор данных **большого двоичного объекта Azure** в **формате** **TextFormat**. <br/><br/>Подробные сведения и ограничения приведены в разделе [Загрузка данных в хранилище данных SQL Azure с помощью PolyBase](#use-polybase-to-load-data-into-azure-sql-data-warehouse). |Да <br/>False (по умолчанию) |Нет |
| polyBaseSettings |Группа свойств, которые можно задать, если свойство **allowPolybase** имеет значение **true**. |&nbsp; |Нет |
| rejectValue |Указывает количество или процент строк, которые могут быть отклонены, прежде чем запрос завершится с ошибкой. <br/><br/>Дополнительные сведения о параметрах отклонения PolyBase см. в подразделе **Аргументы** раздела [CREATE EXTERNAL TABLE (Transact-SQL)](https://msdn.microsoft.com/library/dn935021.aspx). |0 (по умолчанию), 1, 2, ... |Нет |
| rejectType |Указывает тип значения, заданного для параметра rejectValue: литеральное значение или процент. |Value (по умолчанию), Percentage |Нет |
| rejectSampleValue |Определяет количество строк, которое PolyBase следует получить до повторного вычисления процента отклоненных строк. |1, 2, … |Да, если **rejectType** имеет значение **percentage**. |
| useTypeDefault |Указывает способ обработки отсутствующих значений в текстовых файлах с разделителями, когда PolyBase получает данные из текстового файла.<br/><br/>Дополнительные сведения об этом свойстве см. в подразделе "Аргументы" раздела [CREATE EXTERNAL FILE FORMAT (Transact-SQL)](https://msdn.microsoft.com/library/dn935026.aspx). |True, False (по умолчанию) |Нет |

#### <a name="sqldwsink-example"></a>Пример SqlDWSink

```JSON
"sink": {
    "type": "SqlDWSink",
    "writeBatchSize": 1000000,
    "writeBatchTimeout": "00:05:00"
}
```
## <a name="use-polybase-to-load-data-into-azure-sql-data-warehouse"></a>Загрузка данных в хранилище данных SQL Azure с помощью PolyBase.
Применение **PolyBase** — это эффективный способ скоростной загрузки большого объема данных в хранилище данных SQL Azure с высокой пропускной способностью. Используя PolyBase вместо стандартного механизма BULKINSERT, можно значительно увеличить пропускную способность. Подробное сравнение см. в разделе [Базовые показатели производительности](data-factory-copy-activity-performance.md#performance-reference).

* Если формат исходных данных соответствует требованиям PolyBase, то копирование можно выполнять напрямую из исходного хранилища данных в хранилище данных SQL Azure, используя PolyBase. Дополнительные сведения см. в разделе **[Прямое копирование с помощью PolyBase](#direct-copy-using-polybase)**. Пошаговое руководство и пример использования см. в статье [Загрузка 1 ТБ в хранилище данных SQL Azure с помощью фабрики данных Azure [мастер копирования] менее чем за 15 минут](data-factory-load-sql-data-warehouse.md).
* Если PolyBase не поддерживает формат исходных данных, вы можете использовать **[промежуточное копирование](#staged-copy-using-polybase)**. Этот вариант копирования также улучшает пропускную способность. В этом случае данные сначала автоматически преобразовываются в совместимый с PolyBase формат, сохраняются в хранилище BLOB-объектов Azure, а затем отправляются в хранилище данных SQL.

Задайте свойству `allowPolyBase` значение **true**, как показано в следующем примере, чтобы фабрика данных Azure использовала PolyBase для копирования данных в хранилище данных SQL Azure. Если присвоить allowPolyBase значение true, то можно указать определенные свойства PolyBase, используя группу свойств `polyBaseSettings`. В разделе [SqlDWSink](#SqlDWSink) представлены дополнительные сведения о свойствах, которые можно использовать с polyBaseSettings.

```JSON
"sink": {
    "type": "SqlDWSink",
    "allowPolyBase": true,
    "polyBaseSettings":
    {
        "rejectType": "percentage",
        "rejectValue": 10.0,
        "rejectSampleValue": 100,
        "useTypeDefault": true
    }
}
```
### <a name="direct-copy-using-polybase"></a>Прямое копирование с помощью PolyBase
Если исходные данные соответствуют критериям, описанным в этом разделе, можно выполнять копирование напрямую из исходного хранилища данных в хранилище данных SQL Azure, используя PolyBase. В противном случае можно использовать [промежуточное копирование с помощью PolyBase](#staged-copy-using-polybase).

Если требования не выполняются, фабрика данных Azure проверяет параметры и автоматически возвращается к механизму перемещения данных BULKINSERT.

1. **Связанная служба источника** относится к типу **AzureStorage**. Она не настроена для использования аутентификации SAS (подписанный URL-адрес). Дополнительные сведения см. в разделе [Связанная служба хранилища Azure](data-factory-azure-blob-connector.md#azure-storage-linked-service).  
2. Тип **входного набора данных** — **AzureBlob**, тип формата в свойствах `type` — **OrcFormat** или **TextFormat** со следующими конфигурациями:

   1. Параметр `rowDelimiter` должен иметь значение **\n**.
   2. Параметр `nullValue` должен быть **пустой строке** (""), или параметру `treatEmptyAsNull` присваивается значение **true**.
   3. Параметру `encodingName` присваивается значение **utf-8**, которое является значением **по умолчанию**.
   4. Параметры `escapeChar`, `quoteChar`, `firstRowAsHeader` и `skipLineCount` не указываются.
   5. Параметр `compression` может иметь значение **no compression**, **GZip** или **Deflate**.

    ```JSON
    "typeProperties": {
       "folderPath": "<blobpath>",
       "format": {
           "type": "TextFormat",     
           "columnDelimiter": "<any delimiter>",
           "rowDelimiter": "\n",       
           "nullValue": "",           
           "encodingName": "utf-8"    
       },
       "compression": {  
           "type": "GZip",  
           "level": "Optimal"  
       }  
    },
    ```

3. В разделе **BlobSource** для действия копирования в конвейере отсутствует параметр `skipHeaderLineCount`.
4. В разделе **SqlDWSink** для действия копирования в конвейере отсутствует параметр `sliceIdentifierColumnName`. PolyBase гарантирует, что за один цикл выполнения либо все данные будут обновлены, либо ничего не будет обновлено. Чтобы обеспечить **воспроизводимость**, используйте свойство `sqlWriterCleanupScript`).
5. В соответствующем действии копирования не используется `columnMapping`.

### <a name="staged-copy-using-polybase"></a>промежуточное копирование с помощью PolyBase
Если исходные данные не соответствуют критериям, описанным в предыдущем разделе, вы можете включить копирование данных через промежуточное хранилище BLOB-объектов Azure (хранилище класса Premium не поддерживается). В таком случае в фабрике данных Azure данные будут преобразованы и приведены в соответствие с требованиями к формату PolyBase, а затем переданы в хранилище данных SQL с помощью PolyBase. Подробные сведения о том, как работает копирование данных через промежуточное хранилище BLOB-объектов Azure, см. в разделе [Промежуточное копирование](data-factory-copy-activity-performance.md#staged-copy).

> [!NOTE]
> Если при копировании данных из локального хранилища данных в хранилище данных SQL Azure через промежуточное хранилище с помощью PolyBase используется шлюз управления данными версии ниже 2.4, на компьютере шлюза, который будет использоваться для преобразования исходных данных в правильный формат, необходимо установить среду выполнения Java (JRE). Чтобы избежать такой зависимости, мы рекомендуем обновить шлюз до последней версии.
>
>

Чтобы использовать эту функцию, создайте [связанную службу службы хранилища Azure](data-factory-azure-blob-connector.md#azure-storage-linked-service), относящуюся к учетной записи хранения Azure, которая содержит это промежуточное хранилище BLOB-объектов. Затем укажите свойства `enableStaging` и `stagingSettings` для действия копирования, как показано в следующем коде:

```JSON
"activities":[  
{
    "name": "Sample copy activity from SQL Server to SQL Data Warehouse via PolyBase",
    "type": "Copy",
    "inputs": [{ "name": "OnpremisesSQLServerInput" }],
    "outputs": [{ "name": "AzureSQLDWOutput" }],
    "typeProperties": {
        "source": {
            "type": "SqlSource",
        },
        "sink": {
            "type": "SqlDwSink",
            "allowPolyBase": true
        },
        "enableStaging": true,
        "stagingSettings": {
            "linkedServiceName": "MyStagingBlob"
        }
    }
}
]
```

## <a name="best-practices-when-using-polybase"></a>Рекомендации по использованию PolyBase
### <a name="required-database-permission"></a>Необходимые разрешения базы данных
Чтобы отправить данные в хранилище данных SQL с помощью PolyBase, пользователь, используемый для этого процесса, должен иметь [разрешение CONTROL](https://msdn.microsoft.com/library/ms191291.aspx) в целевой базе данных. Это разрешение можно получить, добавив этого пользователя как участника роли db_owner. Дополнительные сведения см. в [этом разделе](../sql-data-warehouse/sql-data-warehouse-overview-manage-security.md#authorization).

### <a name="row-size-and-data-type-limitation"></a>Ограничение размера строки и типа данных
Нагрузки PolyBase ограничиваются загрузкой строк меньше **1 МБ** и не позволяют загружать данные в переменные VARCHR(MAX), NVARCHAR(MAX) или VARBINARY(MAX). Дополнительные сведения см. [здесь](../sql-data-warehouse/sql-data-warehouse-service-capacity-limits.md#loads).

Если источник данных содержит строки больше 1 МБ, вы можете разделить исходные таблицы по вертикали на несколько небольших таблиц, для каждой из которых максимальный размер строки не будет превышать это ограничение. Затем эти небольшие таблицы можно загрузить с помощью PolyBase и объединить в хранилище данных SQL Azure.

### <a name="sql-data-warehouse-resource-class"></a>Класс ресурсов хранилища данных SQL
Чтобы получить наилучшую пропускную способность, мы рекомендуем присвоить пользователю, используемому для отправки данных в хранилище данных SQL через PolyBase, более высокий класс ресурсов. Узнайте, как это сделать, ознакомившись с [примером изменения класса ресурсов пользователя](../sql-data-warehouse/sql-data-warehouse-develop-concurrency.md#change-a-user-resource-class-example).

### <a name="tablename-in-azure-sql-data-warehouse"></a>Использование tableName в хранилище данных SQL Azure
В следующей таблице показаны правильные значения свойства **tableName** в наборе данных JSON для разных сочетаний имен таблиц и схем.

| Схема базы данных | Имя таблицы | Свойство tableName в JSON |
| --- | --- | --- |
| dbo |MyTable |MyTable или dbo.MyTable либо [dbo].[MyTable] |
| dbo1 |MyTable |dbo1.MyTable или [dbo1].[MyTable] |
| dbo |My.Table |[My.Table] или [dbo].[My.Table] |
| dbo1 |My.Table |[dbo1].[My.Table] |

Если вы увидите приведенное ниже сообщение об ошибке, это может указывать на неправильное значение свойства tableName. Правильные значения для свойства tableName в JSON см. в таблице выше.  

```
Type=System.Data.SqlClient.SqlException,Message=Invalid object name 'stg.Account_test'.,Source=.Net SqlClient Data Provider
```

### <a name="columns-with-default-values"></a>Столбцы со значениями по умолчанию
Сейчас для PolyBase в фабрике данных требуется такое же количество столбцов, как и в целевой таблице. Предположим, у вас есть таблица с четырьмя столбцами и один из них определен со значением по умолчанию. Входные данные по-прежнему должны содержать четыре столбца. Если входной набор данных будет содержать 3 столбца, мы получим ошибку, похожую на следующую.

```
All columns of the table must be specified in the INSERT BULK statement.
```
Значение NULL рассматривается как вариант значения по умолчанию. Если столбец допускает значения NULL, входные значения (в большом двоичном объекте) для этого столбца могут быть пустыми (но не могут отсутствовать во входном наборе данных). Для пустых значений PolyBase будет использовать значение NULL в хранилище данных SQL Azure.  

## <a name="auto-table-creation"></a>Автоматическое создание таблицы
При копировании данных из базы данных SQL Azure или SQL Server в хранилище данных SQL Azure, фабрика данных поддерживает автоматическое создание таблицы с использованием исходной схемы при применении мастера копирования для создания, если таблица не существует в целевом хранилище.

Фабрика данных создаст таблицу в месте назначения, используя то же имя, что и у источника, создаст типы данных для столбцов с приведенным ниже сопоставлением и использует распределение таблиц методом циклического перебора. Обратите внимание, что преобразование типов данных надлежащим образом произойдет, если нужно устранить несовместимость между исходными и целевыми хранилищами.

| Тип столбца исходной базы данных SQL | Тип столбца целевого хранилища данных SQL (ограничение размера) |
| --- | --- |
| int | int |
| BigInt | BigInt |
| SmallInt | SmallInt |
| TinyInt | TinyInt |
| Bit | Bit |
| Decimal | Decimal |
| Числовой | Decimal |
| Float | Float |
| Money | Money |
| Real | Real |
| SmallMoney | SmallMoney |
| Binary | Binary |
| Varbinary | VarBinary (до 8000) |
| Дата | Дата |
| DateTime | DateTime |
| DateTime2 | DateTime2 |
| Время | Время |
| DateTimeOffset | DateTimeOffset |
| SmallDateTime | SmallDateTime |
| текст | Varchar (до 8000) |
| NText | NVarChar (до 4000) |
| Образ — | VarBinary (до 8000) |
| UniqueIdentifier | UniqueIdentifier |
| Char | Char |
| NCHAR | NCHAR |
| VarChar | Varchar (до 8000) |
| NVarChar | NVarChar (до 4000) |
| Xml | Varchar (до 8000) |

[!INCLUDE [data-factory-type-repeatability-for-sql-sources](../../includes/data-factory-type-repeatability-for-sql-sources.md)]

[!INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

### <a name="type-mapping-for-azure-sql-data-warehouse"></a>Сопоставление типов для хранилища данных SQL Azure
Как упоминалось в статье о [действиях перемещения данных](data-factory-data-movement-activities.md), во время копирования типы источников автоматически преобразовываются в типы приемников. Такое преобразование выполняется в два этапа.

1. Преобразование собственных типов источников в тип .NET.
2. Преобразование типа .NET в собственный тип приемника.

Когда данные перемещаются в базы данных SQL Azure, SQL Server, Sybase и обратно, для преобразования типа SQL в тип .NET и наоборот используются следующие сопоставления.

Сопоставление аналогично [сопоставлению типов данных SQL Server для ADO.NET](https://msdn.microsoft.com/library/cc716729.aspx).

| Тип ядра СУБД SQL Server | Тип .NET Framework |
| --- | --- |
| bigint |Int64 |
| binary; |Byte[] |
| bit |Логический |
| char; |String, Char[] |
| дата |DateTime |
| DateTime |DateTime |
| datetime2; |DateTime |
| Datetimeoffset |Datetimeoffset |
| Decimal |Decimal |
| Атрибут FILESTREAM (varbinary(max)) |Byte[] |
| Float |Double |
| изображение |Byte[] |
| int |Int32 |
| money; |Decimal |
| nchar; |String, Char[] |
| ntext |String, Char[] |
| numeric |Decimal |
| nvarchar; |String, Char[] |
| real; |Single |
| rowversion |Byte[] |
| smalldatetime; |DateTime |
| smallint; |Int16 |
| smallmoney; |Decimal |
| sql_variant |Object * |
| text |String, Char[] |
| time |Интервал времени |
| Timestamp |Byte[] |
| tinyint; |Byte |
| uniqueidentifier |Guid |
| varbinary; |Byte[] |
| varchar. |String, Char[] |
| xml |xml |

[!INCLUDE [data-factory-type-conversion-sample](../../includes/data-factory-type-conversion-sample.md)]

[!INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

## <a name="performance-and-tuning"></a>Производительность и настройка
Ознакомьтесь со статьей [Руководство по настройке производительности действия копирования](data-factory-copy-activity-performance.md), в которой описываются ключевые факторы, влияющие на производительность перемещения данных (действие копирования) в фабрике данных Azure, и различные способы оптимизации этого процесса.


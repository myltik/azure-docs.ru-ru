<properties 
	pageTitle="Перемещение данных в хранилище данных SQL Azure и из него | Microsoft Azure" 
	description="Узнайте, как переместить данные в хранилище данных SQL Azure и из него с помощью фабрики данных Azure." 
	services="data-factory" 
	documentationCenter="" 
	authors="spelluru" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="data-factory" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/20/2016" 
	ms.author="spelluru"/>

# Перемещение данных в хранилище данных Azure SQL и из него с помощью фабрики данных Azure

В этой статье описано, как с помощью действия копирования в фабрике данных Azure перемещать данные из хранилища данных SQL Azure в другое хранилище данных и наоборот.

Можно указать, следует ли использовать PolyBase при загрузке данных в хранилище данных SQL Azure. Для обеспечения лучшей производительности при загрузке данных в хранилище данных SQL Azure рекомендуется использовать PolyBase. Дополнительные сведения см. в разделе [Загрузка данных в хранилище данных SQL Azure с помощью PolyBase](data-factory-azure-sql-data-warehouse-connector.md#use-polybase-to-load-data-into-azure-sql-data-warehouse).


## Мастер копирования данных
Самый простой способ создать конвейер, копирующий данные в хранилище данных SQL Azure или из него, — использовать мастер копирования данных. В статье [Руководство. Создание конвейера с действием копирования с помощью мастера копирования фабрики данных](data-factory-copy-data-wizard-tutorial.md) приведены краткие пошаговые указания по созданию конвейера с помощью мастера копирования данных.

Ниже приведены примеры с определениями JSON, которые можно использовать для создания конвейера с помощью [портала Azure](data-factory-copy-activity-tutorial-using-azure-portal.md), [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) или [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). В них показано, как копировать данные в хранилище данных SQL Azure и хранилище BLOB-объектов Azure и обратно. Однако данные можно **напрямую** копировать из любых источников в любой указанный [здесь](data-factory-data-movement-activities.md#supported-data-stores) приемник. Это делается с помощью действия копирования в фабрике данных Azure.


> [AZURE.NOTE] 
Общие сведения о службе фабрики данных Azure см. в разделе [Общие сведения о службе фабрики данных Azure, службе интеграции данных в облаке](data-factory-introduction.md).
> 
> В этой статье приведены примеры JSON, но в ней не содержится пошаговых инструкций по созданию фабрики данных. Краткое пошаговое руководство с инструкциями по использованию действия копирования в фабрике данных Azure см. в статье [Копирование данных из хранилища BLOB-объектов Azure в базу данных SQL с помощью фабрики данных](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).


## Пример: копирование данных из хранилища данных SQL Azure в BLOB-объект Azure

Образец определяет следующие сущности фабрики данных.

1. Связанная служба типа [AzureSqlDW](#azure-sql-data-warehouse-linked-service-properties).
2. Связанная служба типа [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties).
3. Входной набор данных [dataset](data-factory-create-datasets.md) типа [AzureSqlDWTable](#azure-sql-data-warehouse-dataset-type-properties).
4. Выходной [набор данных](data-factory-create-datasets.md) типа [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
4. Конвейер [pipeline](data-factory-create-pipelines.md) с действием копирования, в котором используются [SqlDWSource](#azure-sql-data-warehouse-copy-activity-type-properties) и [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties).

В этом примере данные, относящиеся к одному временному ряду (за каждый час, каждый день и т. д.), каждый час копируются из таблицы в хранилище данных SQL Azure в большой двоичный объект. Используемые в этих примерах свойства JSON описаны в разделах, следующих за примерами.

**Связанная служба хранилища данных SQL Azure**

	{
	  "name": "AzureSqlDWLinkedService",
	  "properties": {
	    "type": "AzureSqlDW",
	    "typeProperties": {
	      "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
	    }
	  }
	}

**Связанная служба хранилища BLOB-объектов Azure**

	{
	  "name": "StorageLinkedService",
	  "properties": {
	    "type": "AzureStorage",
	    "typeProperties": {
	      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
	    }
	  }
	}

**Входной набор данных хранилища данных SQL Azure**

В примере предполагается, что вы уже создали таблицу MyTable в хранилище данных Azure SQL и она содержит столбец с именем timestampcolumn для данных временных рядов.
 
Если параметру external присвоить значение true, фабрика данных воспримет этот набор данных как внешний и созданный не в результате какого-либо действия в этой службе.

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

**Выходной набор данных BLOB-объекта Azure**

Данные записываются в новый BLOB-объект каждый час (frequency: hour, interval: 1). Путь к папке BLOB-объекта вычисляется динамически на основе времени начала обрабатываемого среза. В пути к папке используется год, месяц, день и час времени начала.

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


**Конвейер с действием копирования**

Конвейер содержит действие копирования, которое использует входной и выходной наборы данных и выполняется каждый час. В определении JSON конвейера для типа **source** установлено значение **SqlDWSource**, а для типа **sink** — значение **BlobSink**. SQL-запрос, указанный для свойства **SqlReaderQuery**, выбирает для копирования данные за последний час.

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

> [AZURE.NOTE] В приведенном примере для свойства SqlDWSource указано **sqlReaderQuery**. Действие копирования выполняет этот запрос для хранилища данных SQL Azure для получения данных.
>  
> Кроме того, можно создать хранимую процедуру, указав **sqlReaderStoredProcedureName** и **storedProcedureParameters** (если хранимая процедура принимает параметры).
>  
> Если не указать sqlReaderQuery или sqlReaderStoredProcedureName, то для построения запроса (select column1, column2 from mytable) к хранилищу данных SQL Azure будут использованы столбцы, определенные в разделе структуры набора данных JSON. Если у определения набора данных нет структуры, выбираются все столбцы из таблицы.

## Пример: копирование данных из BLOB-объекта Azure в хранилище данных SQL Azure

Образец определяет следующие сущности фабрики данных.

1.	Связанная служба типа [AzureSqlDW](#azure-sql-data-warehouse-linked-service-properties).
2.	Связанная служба типа [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties).
3.	Входной [набор данных](data-factory-create-datasets.md) типа [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
4.	Выходной [набор данных](data-factory-create-datasets.md) типа [AzureSqlDWTable](#azure-sql-data-warehouse-dataset-type-properties).
4.	Конвейер [pipeline](data-factory-create-pipelines.md) с действием копирования, которое использует [BlobSource](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties) и [SqlDWSink](#azure-sql-data-warehouse-copy-activity-type-properties).


В этом примере данные, относящиеся к одному временному ряду (за каждый час, каждый день и т. д.), каждый час копируются из большого двоичного объекта Azure в таблицу в хранилище данных SQL Azure. Используемые в этих примерах свойства JSON описаны в разделах, следующих за примерами.

**Связанная служба хранилища данных SQL Azure**

	{
	  "name": "AzureSqlDWLinkedService",
	  "properties": {
	    "type": "AzureSqlDW",
	    "typeProperties": {
	      "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
	    }
	  }
	}

**Связанная служба хранилища BLOB-объектов Azure**

	{
	  "name": "StorageLinkedService",
	  "properties": {
	    "type": "AzureStorage",
	    "typeProperties": {
	      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
	    }
	  }
	}

**Входной набор данных BLOB-объекта Azure**

Данные берутся из нового BLOB-объекта каждый час (frequency: hour, interval: 1). Путь к папке с BLOB-объектом и имя файла вычисляются динамически на основе времени начала обрабатываемого среза. В пути к папке используется год, месяц и день начала, а в имени файла — час начала. Когда для параметра external задано значение true, служба фабрики данных считает эту таблицу внешней и созданной не в результате какого-либо действия в фабрике данных.

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

**Выходной набор данных хранилища данных SQL Azure**

В этом примере данные копируются в таблицу с именем MyTable в хранилище данных SQL Azure. Таблицу в хранилище данных SQL Azure необходимо создать с таким количеством столбцов, которое должно быть в CSV-файле большого двоичного объекта. Новые строки добавляются в таблицу каждый час.

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

**Конвейер с действием копирования**

Конвейер содержит действие копирования, которое использует входной и выходной наборы данных и выполняется каждый час. В определении JSON конвейера для типа **source** установлено значение **BlobSource**, а для типа **sink** — значение **SqlDWSink**.

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
	            "type": "SqlDWSink"
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

Пошаговое руководство см. в статье [Загрузка данных с помощью фабрики данных Azure](../sql-data-warehouse/sql-data-warehouse-get-started-load-with-azure-data-factory.md) в документации по хранилищу данных SQL Azure.

## Свойства связанной службы хранилища данных SQL Azure

В таблице ниже приведено описание элементов JSON, которые относятся к связанной службе хранилища данных SQL Azure.

Свойство | Описание | Обязательно
-------- | ----------- | --------
type | Для свойства type необходимо задать значение **AzureSqlDW**. | Да
**connectionString** | Укажите сведения, необходимые для подключения к экземпляру хранилища данных SQL Azure, для свойства connectionString. | Да

> [AZURE.IMPORTANT] [Брандмауэр базы данных SQL Azure](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure) необходимо настроить для [разрешения службам Azure доступа к серверу](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure). Кроме того, когда вы с помощью шлюза фабрики данных копируете данные в хранилище данных SQL Azure из внешнего по отношению к Azure источника, в том числе из локальных источников данных, необходимо настроить соответствующий диапазон IP-адресов для компьютера, который отправляет данные в хранилище данных SQL Azure.

## Свойства типа "Набор данных хранилища данных SQL Azure"

Полный список разделов и свойств, используемых для определения наборов данных, см. в статье [Наборы данных](data-factory-create-datasets.md). Разделы структуры, доступности и политики JSON набора данных одинаковы для всех типов наборов данных (SQL Azure, большие двоичные объекты Azure, таблицы Azure и т. д.).

Раздел typeProperties во всех типах наборов данных разный. В нем содержатся сведения о расположении данных в хранилище данных. Раздел **typeProperties** набора данных типа **AzureSqlDWTable** имеет следующие свойства.

| Свойство | Описание | Обязательно |
| -------- | ----------- | -------- |
| tableName | Имя таблицы в базе данных хранилища данных SQL Azure, на которое ссылается связанная служба. | Да |

## Свойства типа "Действие копирования хранилища данных SQL Azure"

Полный список разделов и свойств, используемых для определения действий, см. в статье [Создание конвейеров](data-factory-create-pipelines.md). Свойства (включая имя, описание, входные и выходные таблицы, политику и т. д.) доступны для всех типов действий.

> [AZURE.NOTE]
Действие копирования принимает только один набор входных данных и возвращает только один набор выходных.

С другой стороны, свойства, доступные в разделе typeProperties действия, зависят от конкретного типа действия. Для действия копирования они различаются в зависимости от типов источников и приемников.

### SqlDWSource

Когда источник относится к типу **SqlDWSource**, в разделе **typeProperties** доступны следующие свойства.

| Свойство | Описание | Допустимые значения | Обязательно |
| -------- | ----------- | -------------- | -------- |
| sqlReaderQuery | Используйте пользовательский запрос для чтения данных. | Строка запроса SQL. Например, select * from MyTable. | Нет |
| sqlReaderStoredProcedureName | Имя хранимой процедуры, которая считывает данные из исходной таблицы. | Имя хранимой процедуры. | Нет |
| storedProcedureParameters | Параметры для хранимой процедуры. | Пары имен и значений. Имена и регистр параметров должны совпадать с именами и регистром параметров хранимой процедуры. | Нет |

Если для SqlDWSource указано **sqlReaderQuery**, то действие копирования выполняет этот запрос для хранилища данных SQL Azure с целью получения данных.

Кроме того, можно создать хранимую процедуру, указав **sqlReaderStoredProcedureName** и **storedProcedureParameters** (если хранимая процедура принимает параметры).

Если не указать sqlReaderQuery или sqlReaderStoredProcedureName, то для построения запроса к хранилищу данных SQL Azure будут использованы столбцы, определенные в разделе структуры набора данных JSON. Пример: `select column1, column2 from mytable`. Если у определения набора данных нет структуры, выбираются все столбцы из таблицы.

#### Пример SqlDWSource

    "source": {
        "type": "SqlDWSource",
        "sqlReaderStoredProcedureName": "CopyTestSrcStoredProcedureWithParameters",
        "storedProcedureParameters": {
            "stringData": { "value": "str3" },
            "id": { "value": "$$Text.Format('{0:yyyy}', SliceStart)", "type": "Int"}
        }
    }

**Определение хранимой процедуры:**

	CREATE PROCEDURE CopyTestSrcStoredProcedureWithParameters
	(
		@stringData varchar(20),
		@id int
	)
	AS
	SET NOCOUNT ON;
	BEGIN
	     select *
	     from dbo.UnitTestSrcTable
	     where dbo.UnitTestSrcTable.stringData != stringData
	    and dbo.UnitTestSrcTable.id != id
	END
	GO
 

### SqlDWSink
**SqlDWSink** поддерживает указанные ниже свойства.

| Свойство | Описание | Допустимые значения | Обязательно |
| -------- | ----------- | -------------- | -------- |
| writeBatchSize | Вставляет данные в таблицу SQL, когда размер буфера достигает значения writeBatchSize. | Целое число (количество строк) | Нет (значение по умолчанию — 10 000). |
| writeBatchTimeout | Время ожидания до выполнения операции пакетной вставки, пока не завершится срок ее действия. | Интервал времени<br/><br/> Пример: 00:30:00 (30 минут). | Нет | 
| sqlWriterCleanupScript | Укажите запрос на выполнение действия копирования, позволяющий убедиться в том, что данные конкретного среза очищены. Дополнительные сведения см. в [разделе о повторяемости](#repeatability-during-copy). | Инструкция запроса. | Нет |
| allowPolyBase | Указывает, следует ли использовать PolyBase (если применимо) вместо механизма BULKINSERT для загрузки данных в хранилище данных SQL Azure. <br/><br/>В настоящее время только набор данных **большого двоичного объекта Azure** в **формате** **TextFormat** используется в качестве исходного набора данных. <br/><br/>Подробности и ограничения см. в разделе [Загрузка данных в хранилище данных SQL Azure с помощью PolyBase](#use-polybase-to-load-data-into-azure-sql-data-warehouse). | True, <br/>False (по умолчанию) | Нет |  
| polyBaseSettings | Группа свойств, которые можно задать, если свойство **allowPolybase** имеет значение **true**. | &nbsp; | Нет |  
| rejectValue | Указывает количество или процент строк, которые могут быть отклонены, прежде чем запрос завершится с ошибкой. <br/><br/>Дополнительные сведения о параметрах отклонения PolyBase см. в разделе **Аргументы** описания процедуры [CREATE EXTERNAL TABLE (Transact-SQL)](https://msdn.microsoft.com/library/dn935021.aspx). | 0 (по умолчанию), 1, 2, ... | Нет |  
| rejectType | Указывает тип значения, заданного для параметра rejectValue: литеральное значение или процент. | Value (по умолчанию), Percentage | Нет |   
| rejectSampleValue | Определяет количество строк, которое PolyBase следует получить до повторного вычисления процента отклоненных строк. | 1, 2, … | Да, если **rejectType** имеет значение **percentage** |  
| useTypeDefault | Указывает, как PolyBase обрабатывает отсутствующие значения, когда получает данные из текстового файла с разделителями.<br/><br/>Дополнительные сведения об этом свойстве см. в разделе "Аргументы" описания процедуры [CREATE EXTERNAL FILE FORMAT (Transact-SQL)](https://msdn.microsoft.com/library/dn935026.aspx). | True, False (по умолчанию) | Нет | 


#### Пример SqlDWSink


    "sink": {
        "type": "SqlDWSink",
        "writeBatchSize": 1000000,
        "writeBatchTimeout": "00:05:00"
    }

## Загрузка данных в хранилище данных SQL Azure с помощью PolyBase.
Применение **PolyBase** — это эффективный способ скоростной загрузки большого объема данных в хранилище данных SQL Azure с высокой пропускной способностью. Используя PolyBase вместо стандартного механизма BULKINSERT, можно значительно увеличить пропускную способность.

Задайте свойству **allowPolyBase** значение **true**, как показано в следующем примере, чтобы фабрика данных Azure использовала PolyBase для копирования данных в хранилище данных SQL Azure. Когда allowPolyBase имеет значение true, можно задать дополнительные свойства PolyBase с помощью группы свойств **polyBaseSettings**. Подробные сведения о свойствах, которые можно использовать в polyBaseSettings, см. в разделе [SqlDWSink](#SqlDWSink).


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

### Прямое копирование с помощью PolyBase
Если исходные данные соответствуют критериям, описанным в этом разделе, можно выполнять копирование напрямую из исходного хранилища данных в хранилище данных SQL Azure, используя PolyBase. В противном случае можно использовать [промежуточное копирование с помощью PolyBase](#staged-copy-using-polybase).

Если требования не выполняются, фабрика данных Azure проверяет параметры и автоматически возвращается к механизму перемещения данных BULKINSERT.

1.	**Связанная служба источника** имеет тип **Служба хранилища Azure**. Она не настроена использовать аутентификацию SAS (подписанный URL-адрес). Дополнительные сведения см. в разделе [Связанная служба хранилища Azure](data-factory-azure-blob-connector.md#azure-storage-linked-service).
2. Тип **входного набора данных** — **большой двоичный объект Azure**, тип формата в свойствах типа — **OrcFormat** или **TextFormat** со следующими конфигурациями:
	1. параметр **rowDelimiter** имеет значение **\\n**;
	2. параметр **nullValue** равен **пустой строке** ("");
	3. параметр **encodingName** имеет значение **utf-8**. Это значение используется **по умолчанию**, поэтому не указывайте другое значение;
	4. параметры **escapeChar** и **quoteChar** не указаны;
	5. параметр **Compression** имеет любое значение, кроме **BZIP2**.
	 
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
3.	В разделе **BlobSource** для действия копирования в конвейере отсутствует параметр **skipHeaderLineCount**.
4.	В разделе **SqlDWSink** для действия копирования в конвейере отсутствует параметр **sliceIdentifierColumnName**. PolyBase гарантирует, что за один цикл выполнения либо все данные будут обновлены, либо ничего не будет обновлено. Чтобы обеспечить **воспроизводимость**, используйте свойство **sqlWriterCleanupScript**.
5.	В соответствующем действии копирования не используется **columnMapping**.

### Промежуточное копирование с помощью PolyBase
Если исходные данные не соответствуют критериям, описанным в предыдущем разделе, можно включить копирование данных через промежуточное хранилище BLOB-объектов Azure. В таком случае в фабрике данных Azure данные будут преобразованы и приведены в соответствие с требованиями к формату PolyBase, а затем переданы в хранилище данных SQL с помощью PolyBase. Подробные сведения о том, как работает копирование данных через промежуточное хранилище BLOB-объектов Azure, см. в разделе [Промежуточное копирование](data-factory-copy-activity-performance.md#staged-copy).

> [AZURE.IMPORTANT] При копировании данных из локального хранилища данных в хранилище данных SQL Azure с помощью PolyBase и промежуточного хранения необходимо установить JRE 8 (среда выполнения Java) на компьютере шлюза, который будет использоваться для преобразования исходных данных в правильный формат. Для 64-разрядного шлюза требуется 64-разрядная версия JRE, а для 32-разрядного шлюза — 32-разрядная версия JRE. Скачайте соответствующую версию из [расположения загрузки Java](http://go.microsoft.com/fwlink/?LinkId=808605).

Чтобы использовать эту функцию, создайте [связанную службу службы хранилища Azure](data-factory-azure-blob-connector.md#azure-storage-linked-service), относящуюся к учетной записи хранения Azure, которая содержит это промежуточное хранилище BLOB-объектов. Затем укажите свойства **enableStaging** и **stagingSettings** для действия копирования, как показано в следующем коде.

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


### Рекомендации по использованию PolyBase

#### Ограничение размера строки
Polybase не поддерживает строки размером больше 32 КБ. Попытка загрузить таблицу со строками больше 32 КБ приведет к следующей ошибке:

	Type=System.Data.SqlClient.SqlException,Message=107093;Row size exceeds the defined Maximum DMS row size: [35328 bytes] is larger than the limit of [32768 bytes],Source=.Net SqlClient

Если источник данных содержит строки больше 32 КБ, вы можете разделить исходные таблицы по вертикали на несколько небольших таблиц, для каждой из которых максимальный размер строки не будет превышать это ограничение. Затем эти небольшие таблицы можно загрузить с помощью PolyBase и объединить в хранилище данных SQL Azure.

#### Использование tableName в хранилище данных SQL Azure
В следующей таблице показаны правильные значения свойства **tableName** в наборе данных JSON для разных сочетаний имен таблиц и схем.

| Схема базы данных | Имя таблицы | Свойство tableName в JSON |
| --------- | -----------| ----------------------- | 
| dbo | MyTable | MyTable или dbo.MyTable либо [dbo].[MyTable] |
| dbo1 | MyTable | dbo1.MyTable или [dbo1].[MyTable] |
| dbo | My.Table | [My.Table] или [dbo].[My.Table] |
| dbo1 | My.Table | [dbo1].[My.Table] |

Если вы увидите приведенное ниже сообщение об ошибке, это может указывать на неправильное значение свойства tableName. Правильные значения для свойства tableName в JSON см. в таблице выше.

	Type=System.Data.SqlClient.SqlException,Message=Invalid object name 'stg.Account_test'.,Source=.Net SqlClient Data Provider

#### Столбцы со значениями по умолчанию
Сейчас для PolyBase в фабрике данных требуется такое же количество столбцов, как и в целевой таблице. Предположим, у вас есть таблица с четырьмя столбцами и один из них определен со значением по умолчанию. Входные данные по-прежнему должны содержать четыре столбца. Если входной набор данных будет содержать 3 столбца, мы получим ошибку, похожую на следующую.

	All columns of the table must be specified in the INSERT BULK statement.

Значение NULL рассматривается как вариант значения по умолчанию. Если столбец допускает значения NULL, входные значения (в большом двоичном объекте) для этого столбца могут быть пустыми (но не могут отсутствовать во входном наборе данных). Для пустых значений PolyBase будет использовать значение NULL в хранилище данных SQL Azure.


[AZURE.INCLUDE [data-factory-type-repeatability-for-sql-sources](../../includes/data-factory-type-repeatability-for-sql-sources.md)]

[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

### Сопоставление типов для хранилища данных SQL Azure

Как упоминалось в статье о [действиях перемещения данных](data-factory-data-movement-activities.md), во время копирования типы источников автоматически преобразуются в типы приемников. Такое преобразование выполняется в два этапа.

1. Преобразование собственных типов источников в тип .NET.
2. Преобразование типа .NET в собственный тип приемника.

Когда данные перемещаются в базы данных SQL Azure, SQL Server, Sybase и обратно, для преобразования типа SQL в тип .NET и наоборот используются следующие сопоставления.

Сопоставление аналогично [сопоставлению типов данных SQL Server для ADO.NET](https://msdn.microsoft.com/library/cc716729.aspx).

| Тип ядра СУБД SQL Server | Тип .NET Framework |
| ------------------------------- | ------------------- |
| bigint | Int64 |
| binary; | Byte |
| bit | Логический |
| char; | String, Char |
| дата | DateTime |
| Datetime | DateTime |
| datetime2; | DateTime |
| Datetimeoffset | DateTimeOffset |
| Decimal | Decimal |
| Атрибут FILESTREAM (varbinary(max)) | Byte |
| Float | Double |
| изображение | Byte | 
| int | Int32 | 
| money; | Decimal |
| nchar; | String, Char |
| ntext | String, Char |
| numeric | Decimal |
| nvarchar; | String, Char |
| real; | Single |
| rowversion | Byte |
| smalldatetime; | DateTime |
| smallint; | Int16 |
| smallmoney; | Decimal | 
| sql\_variant | Object * |
| text | String, Char |
| Twitter в режиме реального | TimeSpan |
| Timestamp | Byte |
| tinyint; | Byte |
| uniqueidentifier | Guid |
| varbinary; | Byte |
| varchar. | String, Char |
| xml | Xml |



[AZURE.INCLUDE [data-factory-type-conversion-sample](../../includes/data-factory-type-conversion-sample.md)]

[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

## Производительность и настройка  
См. статью [Руководство по настройке производительности действия копирования](data-factory-copy-activity-performance.md), в которой описываются ключевые факторы, влияющие на производительность перемещения данных (действие копирования) в фабрике данных Azure, и различные способы оптимизации этого процесса.

<!---HONumber=AcomDC_0921_2016-->
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
	ms.date="04/14/2016" 
	ms.author="spelluru"/>

# Перемещение данных в хранилище данных Azure SQL и из него с помощью фабрики данных Azure

В этой статье рассказывается, как с помощью действия копирования в фабрике данных Azure можно перемещать данные из хранилища данных SQL Azure в другое хранилище данных SQL Azure и обратно. Эта статья основана на статье о [действиях перемещения данных](data-factory-data-movement-activities.md), в которой приведены общие сведения о перемещении данных с помощью действия копирования и поддерживаемых источников и приемников данных для хранилища данных SQL.

В следующих примерах показано, как копировать данные в хранилище данных SQL Azure и хранилище BLOB-объектов Azure и обратно. Однако данные можно **напрямую** копировать из любых источников в любые приемники, указанные в статье [Действия перемещения данных](data-factory-data-movement-activities.md#supported-data-stores), с помощью действия копирования в фабрике данных Azure.

> [AZURE.NOTE] 
Общие сведения о службе фабрики данных Azure см. в разделе [Введение в фабрику данных Azure](data-factory-introduction.md).
> 
> В этой статье приведены примеры JSON, но в ней не содержится пошаговых инструкций по созданию фабрики данных. Краткое пошаговое руководство с инструкциями по использованию действия копирования в фабрике данных Azure см. в разделе [Учебник. Копирование данных из больших двоичных объектов Azure в базу данных SQL Azure](data-factory-get-started.md).


## Пример: копирование данных из хранилища данных SQL Azure в BLOB-объект Azure

В примере ниже показано следующее.

1. Связанная служба типа [AzureSqlDW](#azure-sql-data-warehouse-linked-service-properties).
2. Связанная служба типа [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties). 
3. Входной набор данных [dataset](data-factory-create-datasets.md) типа [AzureSqlDWTable](#azure-sql-data-warehouse-dataset-type-properties). 
4. Выходной набор данных [dataset](data-factory-create-datasets.md) типа [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
4. Конвейер [pipeline](data-factory-create-pipelines.md) с действием копирования, в котором используются [SqlDWSource](#azure-sql-data-warehouse-copy-activity-type-properties) и [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties).

В этом примере данные, относящиеся к одному временному ряду, из таблицы в базе данных хранилища SQL Azure каждый час копируются в BLOB-объект. Используемые в этих примерах свойства JSON описаны в разделах, следующих за примерами.

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
 
Если для параметра external задать значение true и указать политику externalData, фабрика данных будет считать эту таблицу внешней по отношению к себе и не созданной в результате какого-либо действия в фабрике данных.

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
	            "format": "%M"
	          }
	        },
	        {
	          "name": "Day",
	          "value": {
	            "type": "DateTime",
	            "date": "SliceStart",
	            "format": "%d"
	          }
	        },
	        {
	          "name": "Hour",
	          "value": {
	            "type": "DateTime",
	            "date": "SliceStart",
	            "format": "%H"
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

Конвейер содержит действие копирования (Copy), которое использует входной и выходной наборы данных (см. выше) и выполняется каждый час. В определении JSON конвейера для типа **source** установлено значение **SqlDWSource**, а для типа **sink** — значение **BlobSink**. SQL-запрос, указанный для свойства **SqlReaderQuery**, выбирает для копирования данные за последний час.

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

> [AZURE.NOTE] В приведенном выше примере для свойства SqlDWSource указано **sqlReaderQuery**. Действие копирования выполняет этот запрос для хранилища данных SQL Azure для получения данных.
>  
> Кроме того, можно создать хранимую процедуру, указав **sqlReaderStoredProcedureName** и **storedProcedureParameters** (если хранимая процедура принимает параметры).
>  
> Если не указать sqlReaderQuery или sqlReaderStoredProcedureName, то для построения запроса (select column1, column2 from mytable) к хранилищу данных SQL Azure будут использованы столбцы, определенные в разделе структуры набора данных JSON. Если у определения набора данных нет структуры, выбираются все столбцы из таблицы.

## Пример: копирование данных из BLOB-объекта Azure в хранилище данных SQL Azure

В примере ниже показано следующее.

1.	Связанная служба типа [AzureSqlDW](#azure-sql-data-warehouse-linked-service-properties).
2.	Связанная служба типа [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties).
3.	Набор данных [dataset](data-factory-create-datasets.md) типа [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
4.	Набор данных [dataset](data-factory-create-datasets.md) типа [AzureSqlDWTable](#azure-sql-data-warehouse-dataset-type-properties).
4.	Конвейер [pipeline](data-factory-create-pipelines.md) с действием копирования, которое использует [BlobSource](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties) и [SqlDWSink](#azure-sql-data-warehouse-copy-activity-type-properties).


В этом примере данные, относящиеся к одному временному ряду, копируются из BLOB-объекта Azure в таблицу в базе данных хранилища данных SQL Azure каждый час. Используемые в этих примерах свойства JSON описаны в разделах, следующих за примерами.

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

Данные берутся из нового BLOB-объекта каждый час (frequency: hour, interval: 1). Путь к папке с BLOB-объектом и имя файла вычисляются динамически на основе времени начала обрабатываемого среза. В пути к папке используется год, месяц и день начала, а в имени файла — час начала. Когда для параметра external задано значение true, служба фабрики данных считает эту таблицу внешней по отношению к себе и не созданной в результате какого-либо действия в фабрике данных.

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
	            "format": "%M"
	          }
	        },
	        {
	          "name": "Day",
	          "value": {
	            "type": "DateTime",
	            "date": "SliceStart",
	            "format": "%d"
	          }
	        },
	        {
	          "name": "Hour",
	          "value": {
	            "type": "DateTime",
	            "date": "SliceStart",
	            "format": "%H"
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

В этом примере данные копируются в таблицу с именем MyTable в хранилище данных SQL Azure. Таблицу в хранилище данных Azure SQL нужно создать с таким количеством столбцов, которое должно содержаться в CSV-файле BLOB-объекта. Новые строки добавляются в таблицу каждый час.

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

Конвейер содержит действие копирования (Copy), которое использует входной и выходной наборы данных (см. выше) и выполняется каждый час. В определении JSON конвейера для типа **source** установлено значение **BlobSource**, а для типа **sink** — значение **SqlDWSink**.

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

Примечание. Вам необходимо настроить [брандмауэр базы данных SQL Azure](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure). Сервер базы данных необходимо настроить для [разрешения службам Azure доступа к серверу](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure). Кроме того, когда вы с помощью шлюза фабрики данных копируете данные в хранилище данных SQL Azure из внешнего по отношению к Azure источника, в том числе из локальных источников данных, необходимо настроить соответствующий диапазон IP-адресов для компьютера, который отправляет данные в хранилище данных SQL Azure.

## Свойства типа "Набор данных хранилища данных SQL Azure"

Полный список разделов и свойств, используемых для определения наборов данных, см. в статье [Создание наборов данных](data-factory-create-datasets.md). Разделы структуры, доступности и политики JSON набора данных одинаковы для всех типов наборов данных (SQL Azure, BLOB-объекты Azure, таблицы Azure и т. д.).

Раздел typeProperties во всех типах наборов данных разный. В нем содержатся сведения о расположении данных в хранилище данных. Раздел **typeProperties** набора данных типа **AzureSqlDWTable** имеет следующие свойства.

| Свойство | Описание | Обязательно |
| -------- | ----------- | -------- |
| tableName | Имя таблицы в базе данных хранилища данных SQL Azure, на которое ссылается связанная служба. | Да |

## Свойства типа "Действие копирования хранилища данных SQL Azure"

Полный список разделов и свойств, используемых для определения действий, см. в статье [Создание конвейеров](data-factory-create-pipelines.md). Такие свойства, как имя, описание, входные и выходные таблицы, различные политики и т. д., доступны для всех типов действий.

**Примечание**. Действие копирования принимает только один входной набор данных и возвращает только один выходной набор.

То, какие свойства будут доступны в разделе typeProperties, зависит от типа действия, а в случае с действием копирования — еще и от типов источников и приемников.

### SqlDWSource

В случае действия копирования, когда источник относится к типу **SqlDWSource**, в разделе **typeProperties** доступны указанные ниже свойства.

| Свойство | Описание | Допустимые значения | Обязательно |
| -------- | ----------- | -------------- | -------- |
| sqlReaderQuery | Используйте пользовательский запрос для чтения данных. | Строка запроса SQL. Например, select * from MyTable. | Нет |
| sqlReaderStoredProcedureName | Имя хранимой процедуры, которая считывает данные из исходной таблицы. | Имя хранимой процедуры. | Нет |
| storedProcedureParameters | Параметры для хранимой процедуры. | Пары имен и значений. Имена и регистр параметров должны совпадать с именами и регистром параметров хранимой процедуры. | Нет |

Если для SqlDWSource указано **sqlReaderQuery**, то действие копирования выполняет этот запрос для хранилища данных SQL Azure с целью получения данных.

Кроме того, можно создать хранимую процедуру, указав **sqlReaderStoredProcedureName** и **storedProcedureParameters** (если хранимая процедура принимает параметры).

Если не указать sqlReaderQuery или sqlReaderStoredProcedureName, то для построения запроса (select column1, column2 from mytable) к хранилищу данных SQL Azure будут использованы столбцы, определенные в разделе структуры набора данных JSON. Если у определения набора данных нет структуры, выбираются все столбцы из таблицы.

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
| writeBatchSize | Вставляет данные в таблицу SQL, когда размер буфера достигает значения writeBatchSize. | Целое число (единица — количество строк) | Нет (значение по умолчанию — 10 000) |
| writeBatchTimeout | Время ожидания до выполнения операции пакетной вставки, пока не завершится срок ее действия. | (Единица — интервал времени) Пример: 00:30:00 (30 минут). | Нет | 
| sqlWriterCleanupScript | Указанный пользователем запрос на выполнение действия копирования, позволяющий убедиться в том, что данные конкретного среза будут очищены. Дополнительные сведения см. ниже в разделе о повторяемости. | Инструкция запроса. | Нет |
| allowPolyBase | Указывает, следует ли использовать PolyBase (если применимо) вместо механизма BULKINSERT для загрузки данных в хранилище данных SQL Azure. <br/><br/>Обратите внимание, что в качестве источника данных пока поддерживаются только наборы данных **BLOB-объектов Azure**, для которых параметр **format** имеет значение **TextFormat**. Поддержка других типов источников будет добавлена в ближайшее время. <br/><br/>Подробности и ограничения см. в разделе [Загрузка данных в хранилище данных SQL Azure с помощью PolyBase](#use-polybase-to-load-data-into-azure-sql-data-warehouse). | True <br/>False (по умолчанию) | Нет |  
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
**PolyBase** — это эффективный способ скоростной загрузки большого объема данных из хранилища BLOB-объектов Azure в хранилище данных SQL Azure. Используя PolyBase вместо стандартного механизма BULKINSERT, можно значительно увеличить пропускную способность.

Если источником данных является не хранилище BLOB-объектов Azure, вы можете сначала скопировать данные из источника данных в хранилище BLOB-объектов Azure, а затем загрузить их в хранилище данных SQL Azure с помощью PolyBase. В таком случае вам нужно выполнить два действия копирования. Первое копирует данные из исходного хранилища данных в хранилище BLOB-объектов Azure, а второе — из хранилища BLOB-объектов Azure в хранилище данных SQL Azure с использованием PolyBase.

Задайте свойству **allowPolyBase** значение **true**, как показано в следующем примере, чтобы фабрика данных Azure использовала PolyBase для копирования данных из хранилища BLOB-объектов Azure в хранилище данных SQL Azure. Когда allowPolyBase имеет значение true, можно задать дополнительные свойства PolyBase с помощью группы свойств **polyBaseSettings**. Подробные сведения о свойствах, которые можно использовать в polyBaseSettings, см. в разделе [SqlDWSink](#SqlDWSink) выше.


    "sink": {
        "type": "SqlDWSink",
		"allowPolyBase": true,
		"polyBaseSettings":
		{
			"rejectType": "percentage",
			"rejectValue": 10,
			"rejectSampleValue": 100,
			"useTypeDefault": true 
		}

    }

Фабрика данных Azure, прежде чем использовать PolyBase для копирования данных в хранилище данных SQL Azure, проверяет соблюдение следующих условий. Если условия не выполняются, фабрика данных Azure автоматически возвращается к механизму BULKINSERT.

1.	**Связанная служба источника** имеет тип **Служба хранилища Azure**; она не настроена выполнять проверку подлинности SAS (подписанный URL-адрес). Дополнительные сведения см. в разделе [Связанная служба хранилища Azure](data-factory-azure-blob-connector.md#azure-storage-linked-service).  
2. **Входной набор данных** имеет тип **BLOB-объект Azure**; свойства типа набора данных удовлетворяют следующим критериям: 
	1. параметр **Type** имеет значение **TextFormat**; 
	2. параметр **rowDelimiter** имеет значение **\\n**; 
	3. параметр **nullValue** равен **пустой строке** (""); 
	4. параметр **encodingName** имеет значение **utf-8**. Это значение используется **по умолчанию**, поэтому не указывайте другое значение; 
	5. параметры **escapeChar** и **quoteChar** не указаны; 
	6. параметр **Compression** имеет любое значение, кроме **BZIP2**.
	 
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
4.	В разделе **SqlDWSink** для действия копирования в конвейере отсутствует параметр **sliceIdentifierColumnName**. PolyBase гарантирует, что за один цикл выполнения либо все данные будут обновлены, либо ничего не будет обновлено. Чтобы обеспечить **воспроизводимость** используйте свойство **SqlWriterCleanupScript**.
5.	В соответствующем действии копирования не используется **columnMapping**. 

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
Сейчас для PolyBase в фабрике данных требуется такое же количество столбцов, как и в целевой таблице. Предположим, у вас есть таблица с 4 столбцами, один из которых определен со значением по умолчанию. При этом входные данные все равно должны содержать 4 столбца. Если входной набор данных будет содержать 3 столбца, мы получим следующую ошибку:

	All columns of the table must be specified in the INSERT BULK statement.

Значение NULL рассматривается как вариант значения по умолчанию. Если столбец допускает значения NULL, входные значения (в большом двоичном объекте) для этого столбца могут быть пустыми (но не могут отсутствовать во входном наборе данных). Для пустых значений PolyBase будет использовать значение NULL в хранилище данных SQL Azure.

#### Применение двухэтапного копирования для использования PolyBase
В PolyBase есть ограничения на используемые типы хранилищ и форматы данных. Если в вашей ситуации эти требования не выполняются, следует использовать дополнительное действие копирования, чтобы скопировать данные в хранилище данных и (или) преобразовать их в формат. И хранилище, и формат должны поддерживаться PolyBase. Ниже приведены примеры возможных преобразований.

-	Преобразование исходных файлов в других кодировках в большой двоичный объект Azure с кодировкой UTF-8.
-	Сериализация данных из базы данных SQL Server или SQL Azure в большие двоичные объекты Azure в формате CSV.
-	Изменение порядка столбцов с помощью свойства columnMapping.

Некоторые советы по выполнению преобразований.

- Выбирайте подходящий разделитель при преобразовании табличных данных в CSV-файлы.

	Мы рекомендуем использовать в качестве разделителя столбцов данных редко используемые символы. Типичные разделители: запятая (,), тильда (~), вертикальная черта (|) и символ табуляции (\\t). Если они используются в данных, задайте в качестве разделителя непечатаемый символ, например \\u0001. Polybase принимает разделители из нескольких символов, что позволяет создавать более сложные разделители столбцов.	
- Формат объектов datetime

	Когда выполняется сериализация объектов datetime, действие копирования по умолчанию использует следующий формат: "yyyy-MM-dd HH:mm:ss.fffffff". PolyBase по умолчанию не поддерживает этот формат. Поддерживаемые форматы даты и времени можно см. здесь: [CREATE EXTERNAL FILE FORMAT (Transact-SQL)](https://msdn.microsoft.com/library/dn935026.aspx). Если формат даты и времени не поддерживаются в Polybase, появляется следующая ошибка:

		Query aborted-- the maximum reject threshold (0 rows) was reached while reading from an external source: 1 rows rejected out of total 1 rows processed.
		(/AccountDimension)Column ordinal: 97, Expected data type: DATETIME NOT NULL, Offending value: 2010-12-17 00:00:00.0000000  (Column Conversion Error), Error: Conversion failed when converting the NVARCHAR value '2010-12-17 00:00:00.0000000' to data type DATETIME.

	Чтобы устранить эту ошибку, задайте формат даты и времени, как показано ниже.
	
		"structure": [
    		{ "name" : "column", "type" : "int", "format": "yyyy-MM-dd HH:mm:ss" }
		]


[AZURE.INCLUDE [data-factory-type-repeatability-for-sql-sources](../../includes/data-factory-type-repeatability-for-sql-sources.md)]

[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

### Сопоставление типов для хранилища данных SQL Azure

Как упоминалось в статье о [действиях перемещения данных](data-factory-data-movement-activities.md), во время копирования типы источников автоматически преобразуются в типы приемников. Такое преобразование состоит из следующих двух этапов.

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
В статье [Руководство по настройке производительности действия копирования](data-factory-copy-activity-performance.md) описаны ключевые факторы, влияющие на производительность при перемещении данных (действие копирования) в фабрике данных Azure. Также указаны разные способы оптимизации этого процесса.

<!---HONumber=AcomDC_0420_2016-->
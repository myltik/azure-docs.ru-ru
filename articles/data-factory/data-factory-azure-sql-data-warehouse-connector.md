<properties 
	pageTitle="Соединитель хранилища данных SQL Azure. Перемещение данных в хранилище данных SQL Azure и из него" 
	description="Сведения о соединителе хранилища данных SQL Azure для службы фабрики данных, с помощью которого данные можно перемещать не только в хранилище SQL Azure, но и из него." 
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
	ms.date="07/29/2015" 
	ms.author="spelluru"/>

# Соединитель хранилища данных SQL Azure. Перемещение данных в хранилище данных SQL Azure и из него 

В этой статье рассказывается, как с помощью действия копирования в фабрике данных можно перемещать данные в хранилище данных SQL Azure из другого хранилища данных и обратно. Эта статья основана на статье о [действиях перемещения данных](data-factory-data-movement-activities.md), в которой приведены общие сведения о перемещении данных с помощью действия копирования и поддерживаемых сочетаниях хранилищ данных.

## Пример: копирование данных из хранилища данных SQL Azure в BLOB-объект Azure

В примере ниже показано следующее.

1. Связанная служба типа AzureSqlDW.
2. Связанная служба типа AzureStorage. 
3. Входной набор данных типа AzureSqlDWTable. 
4. Выходной набор данных типа AzureBlob.
4. Конвейер с действием копирования, в котором используются SqlDWSource и BlobSink.

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
	            "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
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

## Пример: копирование данных из BLOB-объекта Azure в хранилище данных SQL Azure

В примере ниже показано следующее.

1.	Связанная служба типа AzureSqlDWDatabase.
2.	Связанная служба типа AzureStorage.
3.	Входной набор данных типа AzureBlob.
4.	Выходной набор данных типа AzureSqlDWTable.
4.	Конвейер с действием копирования, которое использует BlobSource и SqlDWSink.

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

## Свойства связанной службы хранилища данных SQL Azure

В таблице ниже приведено описание элементов JSON, которые относятся к связанной службе хранилища данных SQL Azure.

Свойство | Описание | Обязательно
-------- | ----------- | --------
type | Для свойства type необходимо задать значение **AzureSqlDW**. | Да
**connectionString** | В свойстве connectionString указываются сведения, необходимые для подключения к экземпляру базы данных SQL Azure. | Да

Примечание. Вам нужно настроить [брандмауэр базы данных SQL Azure](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure), а на сервере базы данных — [разрешить службам Azure доступ к серверу](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure). Кроме того, когда вы с помощью шлюза фабрики данных копируете данные в хранилище данных SQL Azure из внешнего по отношению к Azure источника, в том числе из локальных источников данных, необходимо настроить соответствующий диапазон IP-адресов для компьютера, который отправляет данные в хранилище данных SQL Azure.

## Свойства типа «Набор данных хранилища данных SQL Azure»

Полный список разделов и свойств, используемых для определения наборов данных, см. в статье [Создание наборов данных](data-factory-create-datasets.md). Разделы структуры, доступности и политики JSON набора данных одинаковы для всех типов наборов данных (SQL Azure, BLOB-объекты Azure, таблицы Azure и т. д.).

Раздел typeProperties во всех типах наборов данных разный. В нем содержатся сведения о расположении данных в хранилище данных. Раздел **typeProperties** набора данных типа **AzureSqlDWTable** имеет следующие свойства.

| Свойство | Описание | Обязательно |
| -------- | ----------- | -------- |
| tableName | Имя таблицы в базе данных хранилища данных SQL Azure, на которое ссылается связанная служба. | Да |

## Свойства типа «Действие копирования хранилища данных SQL Azure»

Полный список разделов и свойств, доступных для определения действий, см. в статье [Создание конвейеров](data-factory-create-pipelines.md). Такие свойства, как имя, описание, входные и выходные таблицы, различные политики и т. д., доступны для всех типов действий.

**Примечание**. Действие копирования принимает только один набор входных данных и возвращает только один набор выходных.

То, какие свойства будут доступны в разделе typeProperties, зависит от типа действия, а в случае с действием копирования — еще и от типов источников и приемников.

В случае действия копирования, когда источник относится к типу **SqlDWSource**, в разделе **typeProperties** доступны следующие свойства.

| Свойство | Описание | Допустимые значения | Обязательно |
| -------- | ----------- | -------------- | -------- |
| sqlReaderQuery | Используйте пользовательский запрос для чтения данных. | Строка запроса SQL. Например, select * from MyTable. Если не указано другое, выполняется инструкция SQL select from MyTable. | Нет |

**SqlDWSink** поддерживает следующие свойства.

| Свойство | Описание | Допустимые значения | Обязательно |
| -------- | ----------- | -------------- | -------- |
| sqlWriterStoredProcedureName | Указанное пользователем имя хранимой процедуры для обновления и вставки данных в целевой таблице. | Имя хранимой процедуры. | Нет |
| sqlWriterTableType | Указанное пользователем имя типа таблицы для использования в упомянутой выше хранимой процедуре. Действие копирования делает перемещаемые данные доступными во временной таблице этого типа. Код хранимой процедуры затем можно использовать для объединения копируемых и существующих данных. | Имя типа таблицы. | Нет |
| writeBatchSize | Вставляет данные в таблицу SQL, когда размер буфера достигает значения writeBatchSize. | Целое число (единица — количество строк) | Нет (значение по умолчанию — 10 000) |
| writeBatchTimeout | Время ожидания до выполнения операции пакетной вставки, пока не завершится срок ее действия. | (Единица — интервал времени) Пример: 00:30:00 (30 минут). | Нет | 
| sqlWriterCleanupScript | Указанный пользователем запрос на выполнение действия копирования, позволяющий убедиться в том, что данные конкретного среза будут очищены. Дополнительные сведения см. ниже в разделе о повторяемости. | Инструкция запроса. | Нет |
| sliceIdentifierColumnName | Указываемое пользователем имя столбца, в которое действие копирования добавляет автоматически созданный идентификатор среза. Этот идентификатор используется для очистки данных конкретного среза при повторном запуске. Дополнительные сведения см. ниже в разделе о повторяемости. | Имя столбца с типом данных binary(32). | Нет |

[AZURE.INCLUDE [data-factory-type-repeatability-for-sql-sources](../../includes/data-factory-type-repeatability-for-sql-sources.md)]

[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

### Сопоставление типов для хранилища данных SQL Azure

Как упоминалось в статье о [действиях перемещения данных](data-factory-data-movement-activities.md), во время копирования типы источников автоматически преобразовываются в типы приемников. Такое преобразование выполняется в два этапа:

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

<!---HONumber=August15_HO6-->
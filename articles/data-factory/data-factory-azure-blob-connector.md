<properties 
	pageTitle="Соединитель BLOB-объектов Azure: перемещение данных в BLOB-объект Azure и из него" 
	description="Сведения о соединителе BLOB-объектов Azure для службы фабрики данных, с помощью которого можно перемещать данные в хранилище BLOB-объектов Azure и из него." 
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

# Соединитель BLOB-объектов Azure: перемещение данных в BLOB-объект Azure и из него
В этой статье рассказывается, как с помощью действия копирования в фабрике данных Azure можно перемещать данные в BLOB-объект Azure из другого хранилища данных и обратно. Эта статья основана на статье о [действиях перемещения данных](data-factory-data-movement-activities.md), в которой приведены общие сведения о перемещении данных с помощью действия копирования и поддерживаемых сочетаниях хранилищ данных.

## Пример копирования данных из BLOB-объекта Azure в SQL Azure
В примере ниже показано следующее.

1.	Связанная служба типа [AzureSqlDatabase](data-factory-azure-sql-connector.md).
2.	Связанная служба типа [AzureStorage](#LinkedService).
3.	Входной набор данных типа [AzureBlob](#Dataset).
4.	Выходной набор данных типа [AzureSqlTable](data-factory-azure-sql-connector.md).
4.	Конвейер с действием копирования, в котором используются [BlobSource](#CopyActivity) и [SqlSink](data-factory-azure-sql-connector.md).

В этом примере данные, относящиеся к одному временному ряду, каждый час копируются из BLOB-объекта Azure в таблицу в базе данных SQL Azure. Используемые в этих примерах свойства JSON описаны в разделах, следующих за примерами.

**Связанная служба SQL Azure**

	{
	  "name": "AzureSqlLinkedService",
	  "properties": {
	    "type": "AzureSqlDatabase",
	    "typeProperties": {
	      "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
	    }
	  }
	}

**Связанная служба хранилища Azure**

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

**Выходной набор данных SQL Azure**

В этом примере данные копируются в таблицу с именем MyOutputTable в базе данных SQL Azure. Таблицу в базе данных SQL Azure нужно создать с тем количеством столбцов, которое должно быть в CSV-файле BLOB-объекта. Новые строки добавляются в таблицу каждый час.

	{
	  "name": "AzureSqlOutput",
	  "properties": {
	    "type": "AzureSqlTable",
	    "linkedServiceName": "AzureSqlLinkedService",
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

Конвейер содержит действие копирования (Copy), которое использует входной и выходной наборы данных (см. выше) и выполняется каждый час. В определении JSON конвейера для типа **source** установлено значение **BlobSource**, а для типа **sink** — значение **SqlSink**.

	{  
	    "name":"SamplePipeline",
	    "properties":{  
	    "start":"2014-06-01T18:00:00",
	    "end":"2014-06-01T19:00:00",
	    "description":"pipeline with copy activity",
	    "activities":[  
	      {
	        "name": "AzureBlobtoSQL",
	        "description": "Copy Activity",
	        "type": "Copy",
	        "inputs": [
	          {
	            "name": "AzureBlobInput"
	          }
	        ],
	        "outputs": [
	          {
	            "name": "AzureSqlOutput"
	          }
	        ],
	        "typeProperties": {
	          "source": {
	            "type": "BlobSource",
	            "blobColumnSeparators": ","
	          },
	          "sink": {
	            "type": "SqlSink"
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

## Пример копирования данных из SQL Azure в BLOB-объект Azure
В примере ниже показано следующее.

1.	Связанная служба типа AzureSqlDatabase.
2.	Связанная служба типа AzureStorage.
3.	Входной набор данных типа AzureSqlTable.
4.	Выходной набор данных типа AzureBlob.
4.	Конвейер с действием копирования, в котором используются SqlSource и BlobSink.

В этом примере данные, относящиеся к одному временному ряду, каждый час копируются из таблицы в базе данных SQL Azure в BLOB-объект. Используемые в этих примерах свойства JSON описаны в разделах, следующих за примерами.

**Связанная служба SQL Azure**

	{
	  "name": "AzureSqlLinkedService",
	  "properties": {
	    "type": "AzureSqlDatabase",
	    "typeProperties": {
	      "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
	    }
	  }
	}

**Связанная служба хранилища Azure**

	{
	  "name": "StorageLinkedService",
	  "properties": {
	    "type": "AzureStorage",
	    "typeProperties": {
	      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
	    }
	  }
	}

**Входной набор данных SQL Azure**

В примере предполагается, что таблица MyTable уже создана в SQL Azure и содержит столбец с именем timestampcolumn для данных временных рядов.

Если для параметра external задать значение true и указать политику externalData, фабрика данных будет считать эту таблицу внешней по отношению к себе и не созданной в результате какого-либо действия в фабрике данных.

	{
	  "name": "AzureSqlInput",
	  "properties": {
	    "type": "AzureSqlTable",
	    "linkedServiceName": "AzureSqlLinkedService",
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

Конвейер содержит действие копирования (Copy), которое использует входной и выходной наборы данных (см. выше) и выполняется каждый час. В определении JSON конвейера для типа **source** установлено значение **SqlSource**, а для типа **sink** — значение **BlobSink**. SQL-запрос, указанный для свойства **SqlReaderQuery**, выбирает для копирования данные за последний час.


	{  
	    "name":"SamplePipeline",
	    "properties":{  
	    	"start":"2014-06-01T18:00:00",
	    	"end":"2014-06-01T19:00:00",
	    	"description":"pipeline for copy activity",
	    	"activities":[  
	      		{
	        		"name": "AzureSQLtoBlob",
		    	    "description": "copy activity",
		    	    "type": "Copy",
		    	    "inputs": [
		    	      {
		    	        "name": "AzureSQLInput"
		    	      }
		    	    ],
		    	    "outputs": [
		    	      {
		    	        "name": "AzureBlobOutput"
		    	      }
		    	    ],
		    	    "typeProperties": {
		    	    	"source": {
		            		"type": "SqlSource",
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

## <a name="LinkedService"></a> Свойства связанной службы хранилища Azure

С помощью связанной службы хранилища Azure учетную запись хранения Azure можно связать с фабрикой данных Azure. В таблице ниже приведено описание элементов JSON, которые относятся к связанной службе хранилища Azure.

| Свойство | Описание | Обязательно |
| -------- | ----------- | -------- |
| type | Для свойства type необходимо задать значение **AzureStorage**. | Да |
| connectionString | В свойстве connectionString указываются сведения, необходимые для подключения к службе хранилища Azure. ConnectionString для хранилища Azure можно получить на портале Azure. | Да |

## <a name="Dataset"></a> Свойства типа «Набор данных BLOB-объекта Azure»

Полный список разделов и свойств, используемых для определения наборов данных, см. в статье [Создание наборов данных](data-factory-create-datasets.md). Разделы структуры, доступности и политики JSON набора данных одинаковы для всех типов наборов данных (SQL Azure, BLOB-объекты Azure, таблицы Azure и т. д.).

Раздел **typeProperties** во всех типах наборов данных разный. В нем содержатся сведения о расположении данных в хранилище данных, формате и т. д. Раздел typeProperties набора данных с типом **AzureTable** имеет следующие свойства.

| Свойство | Описание | Обязательно |
| -------- | ----------- | -------- | 
| folderPath | Путь контейнеру и папке в хранилище BLOB-объектов. Пример: myblobcontainer\\myblobfolder\\ | Да |
| fileName | <p>Имя BLOB-объекта. Необязательное свойство. </p><p>Если указать имя файла, действие (включая копирование) будет выполняться в отношении определенного BLOB-объекта.</p><p>Если свойство fileName не указано, копирование будет включать все BLOB-объекты в свойстве folderPath для входного набора данных.</p><p>Если свойство fileName не указано для выходного набора данных, имя созданного файла будет иметь следующий формат: Data.<Guid>.txt (например, Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt).</p> | Нет |
| partitionedBy | Необязательное свойство. Его можно использовать, чтобы указать динамические путь к папке и имя файла для временного ряда данных. Например, путь к папке (значение folderPath) каждый час может быть другим. Подробные сведения и примеры см в разделе «Использование свойства partitionedBy» ниже. | Нет
| format | Поддерживаются два типа форматов: **TextFormat** и **AvroFormat**. Свойству type в разделе format необходимо присвоить одно из этих значений. Если для свойства Format указано значение TextFormat, для формата можно указать дополнительные свойства. Для получения дополнительных сведений см. раздел [Определение TextFormat](#specifying-textformat) ниже. | Нет

### Использование свойства partitionedBy
Как упоминалось выше, для временного ряда данных путь к папке и имя файла можно указывать динамически. Это делается в разделе **partitionedBy** с использованием макросов фабрики данных и системных переменных SliceStart и SliceEnd, которые указывают время начала и окончания среза данных.

В статьях [Создание наборов данных](data-factory-create-datasets.md) и [Планирование и исполнение](data-factory-scheduling-and-execution.md) приведены подробные сведения о наборах данных временных рядов, планировании и срезах.

#### Пример 1

	"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
	"partitionedBy": 
	[
	    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
	],

В примере выше {Slice} заменяется значением SliceStart (системная переменная фабрики данных) в формате YYYYMMDDHH. SliceStart указывает время начала среза. Значение folderPath отличается для каждого среза. Например: wikidatagateway/wikisampledataout/2014100103 или wikidatagateway/wikisampledataout/2014100104.

#### Пример 2

	"folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
	"fileName": "{Hour}.csv",
	"partitionedBy": 
	 [
	    { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
	    { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } }, 
	    { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } }, 
	    { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } } 
	],

В примере выше год, месяц, день и время SliceStart извлекаются в отдельные переменные, используемые в свойствах folderPath и fileName.

### Определение TextFormat

Если для свойства format задать значение **TextFormat**, в разделе **Format** вы сможете указать следующие **необязательные** свойства.

| Свойство | Описание | Обязательно |
| -------- | ----------- | -------- |
| columnDelimiter | Один или несколько символов, используемых в качестве разделителя столбцов в файле. Этот тег является необязательным. Значение по умолчанию — запятая (,). | Нет |
| rowDelimiter | Один или несколько символов, используемых в качестве необработанного разделителя в файле. Этот тег является необязательным. По умолчанию используется одно из следующих значений: [\\r\\n, \\r, \\n]. | Нет |
| escapeChar | <p>Специальный символ, используемый для экранирования разделителя столбцов в содержимом. Этот тег является необязательным. Значение по умолчанию отсутствует. Для этого свойства следует указывать не более одного символа.</p><p>Например, если в качестве разделителя столбцов вы используете запятую и хотите вставить в текст запятую (например, Hello, world), определите escape-символ $ и используйте в источнике строку Hello$, world.</p><p>Обратите внимание, что для таблицы нельзя одновременно задать escapeChar и quoteChar.</p> | Нет | 
| quoteChar | <p>Специальный символ, который используется для заключения строкового значения в кавычки. Разделители столбцов и строк внутри кавычек будут рассматриваться как часть строкового значения. Этот тег является необязательным. Значение по умолчанию отсутствует. Для этого свойства следует указывать не более одного символа.</p><p>Например, если в качестве разделителя столбцов вы используете запятую и хотите вставить в текст запятую (например, <Hello  world>), определите символ " в качестве кавычек и используйте в источнике строку "Hello, world". Это свойство применимо к входным и выходным таблицам.</p><p>Обратите внимание, что для таблицы нельзя одновременно задать escapeChar и quoteChar.</p> | Нет |
| nullValue | <p>Один или несколько символов, используемых для обозначения нулевого значения в файле BLOB-объекта. Этот тег является необязательным. Значение по умолчанию — \\N.</p><p>Например, значение NaN (на основе приведенного выше примера) в BLOB-объекте будет преобразовано в нулевое значение при копировании, например, на сервер SQL Server.</p> | Нет |
| encodingName | Имя кодировки. Список допустимых имен кодировок см. в статье [Свойство Encoding.EncodingName](https://msdn.microsoft.com/library/system.text.encoding.aspx) Например: windows-1250 или shift\_jis. По умолчанию используется значение UTF-8. | Нет | 

#### Примеры
В следующем примере показаны некоторые свойства формата для TextFormat.

	"typeProperties":
	{
	    "folderPath": "mycontainer/myfolder",
	    "fileName": "myblobname"
	    "format":
	    {
	        "type": "TextFormat",
	        "columnDelimiter": ",",
	        "rowDelimiter": ";",
	        "quoteChar": """,
	        "NullValue": "NaN"
	    }
	},

Чтобы использовать escapeChar вместо quoteChar, вместо строки с quoteChar укажите следующее:

	"escapeChar": "$",

### Определение AvroFormat
Если для свойства format выбрано значение AvroFormat, вам не нужно указывать какие-либо свойства в подразделе Format раздела typeProperties. Пример:

	"format":
	{
	    "type": "AvroFormat",
	}

Узнать, как использовать AvroFormat в таблице Hive можно из [учебника по Apache Hive](https://cwiki.apache.org/confluence/display/Hive/AvroSerDe).

## <a name="CopyActivity"></a> Свойства типа «Действие копирования BLOB-объекта Azure»  
Полный список разделов и свойств, используемых для определения действий, см. в статье [Создание конвейеров](data-factory-create-pipelines.md). Такие свойства, как имя, описание, входные и выходные таблицы, различные политики и т. д., доступны для всех типов действий.

То, какие свойства будут доступны в разделе typeProperties, зависит от типа действия, а в случае с действием копирования — еще и от типов источников и приемников.

Для **BlobSource** в разделе **typeProperties** могут быть указаны следующие свойства.

| Свойство | Описание | Допустимые значения | Обязательно |
| -------- | ----------- | -------------- | -------- | 
| treatEmptyAsNull | Указывает, следует ли интерпретировать null или пустую строку как значение null. | TRUE<br/>FALSE | Нет |
| skipHeaderLineCount | Укажите, сколько строк необходимо пропустить. Применяется, только когда для входного набора данных используется **TextFormat**. | Целое число от 0 до макс. | Нет | 


Для **BlobSink** в разделе **typeProperties** могут быть указаны следующие свойства.

| Свойство | Описание | Допустимые значения | Обязательно |
| -------- | ----------- | -------------- | -------- |
| blobWriterAddHeader | Указывает, нужно ли добавлять заголовок для определений столбцов. | TRUE<br/>FALSE (по умолчанию) | Нет |

[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

[AZURE.INCLUDE [data-factory-type-conversion-sample](../../includes/data-factory-type-conversion-sample.md)]

[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

<!---HONumber=August15_HO6-->
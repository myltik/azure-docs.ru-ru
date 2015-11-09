<properties
	pageTitle="Перемещение данных в озеро данных Azure и обратно| Фабрика данных Azure"
	description="Узнайте, как с помощью фабрики данных Azure перемещать данные в озеро данных Azure и обратно"
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
	ms.date="10/13/2015"
	ms.author="spelluru"/>

# Перемещение данных в озеро данных Azure и обратно с помощью фабрики данных Azure
Эта статья расскажет, как с помощью действия копирования в фабрике данных Azure перемещать информацию в озеро данных Azure из другого хранилища данных и обратно. Эта статья основана на статье о [действиях перемещения данных](data-factory-data-movement-activities.md), в которой приведены общие сведения о перемещении данных с помощью действия копирования и поддерживаемых сочетаниях хранилищ данных.

> [AZURE.NOTE]Чтобы создать конвейер с действием копирования для перемещения информации в озеро данных Azure и обратно, вам потребуется учетная запись хранилища озера данных Azure. Сведения о хранилище озера данных Azure см. в статье [Начало работы с хранилищем озера данных Azure](../data-lake-store/data-lake-store-get-started-portal.md).
>  
> В руководстве [Построение первого конвейера](data-factory-build-your-first-pipeline.md) подробно описаны процессы создания фабрики данных, связанных служб, наборов данных и конвейера. Для создания сущностей фабрики данных запустите предложенные фрагменты кода JSON в редакторе фабрики данных, в Visual Studio или в Azure PowerShell.

## Пример. Копирование данных из BLOB-объекта Azure в хранилище озера данных Azure
В примере ниже показано следующее.

1.	Связанная служба типа [AzureStorage](#azure-storage-linked-service-properties).
2.	Связанная служба типа [AzureDataLakeStore](#azure-data-lake-linked-service-properties).
3.	Входной [набор данных](data-factory-create-datasets.md) типа [AzureBlob](#azure-blob-dataset-type-properties).
4.	Выходной [набор данных](data-factory-create-datasets.md) типа [AzureDataLakeStore](#azure-data-lake-dataset-type-properties).
4.	[Конвейер](data-factory-create-pipelines.md) с действием копирования, который использует [BlobSource](#azure-blob-copy-activity-type-properties) и [AzureDataLakeStoreSink](#azure-data-lake-copy-activity-type-properties).

В этом примере данные из одного временного ряда каждый час копируются из хранилища BLOB-объектов Azure в хранилище озера данных Azure. Используемые в этих примерах свойства JSON описаны в разделах, следующих за примерами.


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

**Связанная служба озера данных Azure:**

	{
	    "name": "AzureDataLakeStoreLinkedService",
	    "properties": {
	        "type": "AzureDataLakeStore",
	        "typeProperties": {
	            "dataLakeUri": "https://<accountname>.azuredatalake.net/webhdfs/v1",
				"sessionId": "<session ID>",
	            "authorization": "<authorization URL>"
	        }
	    }
	}

### Создание связанной службы озера данных Azure с помощью редактора фабрики данных
Далее приводятся пошаговые инструкции по созданию связанной службы хранилища озера данных Azure с помощью редактора фабрики данных.

1. Щелкните **Новое хранилище данных** на панели команд и выберите **Хранилище озера данных Azure**.
2. В редакторе JSON введите URI озера данных как значение свойства **datalakeUri**.
3. Щелкните кнопку **Авторизовать** на панели команд. Появится всплывающее окно.

	![Кнопка «Авторизовать»](./media/data-factory-azure-data-lake-connector/authorize-button.png)
4. Войдите в систему с помощью своих учетных данных. После этого свойство **authorization** в файле JSON должно получить нужное значение.
5. Укажите значения необязательных параметров JSON, например **accountName**, **subscriptionID** и **resourceGroupName** или удалите эти свойства из JSON. Этот шаг можно пропустить.
6. Чтобы развернуть эту службу, нажмите кнопку **Развернуть** на панели команд.


**Входной набор данных BLOB-объекта Azure**

Данные берутся из нового BLOB-объекта каждый час (frequency: hour, interval: 1). Путь к папке с BLOB-объектом и имя файла вычисляются динамически на основе времени начала обрабатываемого среза. В пути к папке используется год, месяц и день начала, а в имени файла — час начала. Когда для параметра external задано значение true, служба фабрики данных считает эту таблицу внешней по отношению к себе и не созданной в результате какого-либо действия в фабрике данных.

	{
	  "name": "AzureBlobInput",
	  "properties": {
	    "type": "AzureBlob",
	    "linkedServiceName": "StorageLinkedService",
	    "typeProperties": {
	      "Path": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}",
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
	      ]
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


**Выходной набор данных озера данных Azure:**

Этот пример кода копирует данные в хранилище озера данных Azure. Новые данные копируются в озеро данных каждый час.

	{
		"name": "AzureDataLakeStoreOutput",
	  	"properties": {
			"type": "AzureDataLakeStore",
		    "linkedServiceName": " AzureDataLakeStoreLinkedService",
		    "typeProperties": {
				"folderPath": "datalake/output/"
		    },
	    	"availability": {
	      		"frequency": "Hour",
	      		"interval": 1
	    	}
	  	}
	}



**Конвейер с действием копирования**

Конвейер содержит действие копирования (Copy), которое использует входной и выходной наборы данных (см. выше) и выполняется каждый час. В определении JSON конвейера для параметра **source** задается тип **BlobSource**, а для **sink** — тип **AzureDataLakeStoreSink**.

	{  
	    "name":"SamplePipeline",
	    "properties":
		{  
	    	"start":"2014-06-01T18:00:00",
	    	"end":"2014-06-01T19:00:00",
	    	"description":"pipeline with copy activity",
	    	"activities":
			[  
	      		{
	        		"name": "AzureBlobtoDataLake",
		        	"description": "Copy Activity",
		        	"type": "Copy",
		        	"inputs": [
		          	{
			            "name": "AzureBlobInput"
		          	}
		        	],
		        	"outputs": [
		          	{
			            "name": "AzureDataLakeStoreOutput"
		          	}
		        	],
		        	"typeProperties": {
			        	"source": {
		            		"type": "BlobSource",
			    			"treatEmptyAsNull": true,
		            		"blobColumnSeparators": ","
		          		},
		          		"sink": {
		            		"type": "AzureDataLakeStoreSink"
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

## Пример. Копирование данных из хранилища озера данных Azure в BLOB-объект Azure
В примере ниже показано следующее.

1.	Связанная служба типа [AzureDataLakeStore](#azure-data-lake-linked-service-properties).
2.	Связанная служба типа [AzureStorage](#azure-storage-linked-service-properties).
3.	Входной [набор данных](data-factory-create-datasets.md) типа [AzureDataLakeStore](#azure-data-lake-dataset-type-properties).
4.	Выходной [набор данных](data-factory-create-datasets.md) типа [AzureBlob](#azure-blob-dataset-type-properties).
5.	[Конвейер](data-factory-create-pipelines.md) с действием копирования, который использует [AzureDataLakeStoreSource](#azure-data-lake-copy-activity-type-properties) и [BlobSink](#azure-blob-copy-activity-type-properties).

Этот пример кода каждый час копирует данные временного ряда из хранилища озера данных Azure в BLOB-объект Azure. Используемые в этих примерах свойства JSON описаны в разделах, следующих за примерами.

**Связанная служба хранилища озера данных Azure:**

	{
	    "name": "AzureDataLakeStoreLinkedService",
	    "properties": {
	        "type": "AzureDataLakeStore",
	        "typeProperties": {
	            "dataLakeUri": "https://<accountname>.azuredatalake.net/webhdfs/v1",
				"sessionId": "<session ID>",
	            "authorization": "<authorization URL>"
	        }
	    }
	}

> [AZURE.NOTE]Чтобы получить URL-адрес авторизации, см. шаги, приведенные в предыдущем примере.

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

**Входной набор данных озера данных Azure:**

Если для параметра **external** задать значение true и указать политику **externalData**, фабрика данных Azure будет считать эту таблицу внешней по отношению к себе, а не созданной в результате какого-либо действия в фабрике данных.

	{
		"name": "AzureDataLakeStoreInput",
	  	"properties":
		{
	    	"type": "AzureDataLakeStore",
	    	"linkedServiceName": "AzureDataLakeStoreLinkedService",
		    "typeProperties": {
				"folderPath": "datalake/input/",
            	"fileName": "SearchLog.tsv",
            	"format": {
                	"type": "TextFormat",
	                "rowDelimiter": "\n",
    	            "columnDelimiter": "\t"
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

Конвейер содержит действие копирования (Copy), которое использует входной и выходной наборы данных (см. выше) и выполняется каждый час. В определении JSON конвейера для параметра **source** задается тип **AzureDataLakeStoreSource**, а для параметра **sink** — тип **BlobSink**.


	{  
	    "name":"SamplePipeline",
	    "properties":{  
	    	"start":"2014-06-01T18:00:00",
	    	"end":"2014-06-01T19:00:00",
	    	"description":"pipeline for copy activity",
	    	"activities":[  
	      		{
	        		"name": "AzureDakeLaketoBlob",
		    	    "description": "copy activity",
		    	    "type": "Copy",
		    	    "inputs": [
		    	      {
		    	        "name": "AzureDataLakeStoreInput"
		    	      }
		    	    ],
		    	    "outputs": [
		    	      {
		    	        "name": "AzureBlobOutput"
		    	      }
		    	    ],
		    	    "typeProperties": {
		    	    	"source": {
		            		"type": "AzureDataLakeStoreSource",
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


## Свойства связанной службы хранилища озера данных Azure

С помощью связанной службы хранилища Azure учетную запись хранения Azure можно связать с фабрикой данных Azure. В таблице ниже приведено описание элементов JSON, которые относятся к связанной службе хранилища Azure.

| Свойство | Описание | Обязательно |
| -------- | ----------- | -------- |
| type | Для свойства type следует задать значение **AzureDataLakeStore** | Да |
| dataLakeUri | Указывает сведения об учетной записи хранилища озера данных Azure в следующем формате: https://<Azure Data Lake account name>.azuredatalake.net/webhdfs/v1 | Да |
| authorization | Нажмите кнопку **Авторизовать** в **редакторе фабрики данных** и введите учетные данные. URL-адрес авторизации будет создан автоматически и присвоен этому свойству. | Да |
| sessionId | Идентификатор сеанса OAuth из сеанса авторизации oauth. Каждый идентификатор сеанса является уникальным и используется только один раз. Он создается автоматически при использовании редактора данных фабрики. | Да |  
| accountName | Имя учетной записи озера данных | Нет |
| subscriptionId | Идентификатор подписки Azure. | Нет (если не указан, используется подписка фабрики данных). |
| имя\_группы\_ресурсов | Имя группы ресурсов Azure | Нет (если не указано, используется группа ресурсов фабрики данных). |


## Свойства типа «Набор данных озера данных Azure»

Полный список разделов и свойств JSON, используемых для определения наборов данных, вы найдете в статье [Наборы данных](data-factory-create-datasets.md). Разделы структуры, доступности и политики JSON набора данных одинаковы для всех типов наборов данных (SQL Azure, BLOB-объекты Azure, таблицы Azure и т. д.).

Раздел **typeProperties** различается для разных типов наборов данных. Он содержит сведения о расположении данных в хранилище данных, формате данных и т. д. Раздел typeProperties набора данных типа **AzureDataLakeStore** имеет следующие свойства.

| Свойство | Описание | Обязательно |
| -------- | ----------- | -------- |
| folderPath | Путь к контейнеру и папке в хранилище озера данных Azure. | Да |
| fileName | <p>Имя файла в хранилище озера данных Azure. Необязательное свойство. </p><p>Если вы укажете имя файла, любое действие (включая копирование) будет выполняться для конкретного файла.</p><p>Если свойство fileName не указано, будут скопированы все файлы из папки folderPath входного набора данных.</p><p>Если свойство fileName не указано для выходного набора данных, имя созданного файла будет иметь следующий формат: Data.<Guid>.txt (например, Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt).</p> | Нет |
| partitionedBy | Необязательное свойство. Его можно использовать, чтобы указать динамические путь к папке и имя файла для временного ряда данных. Например, путь к папке (значение folderPath) каждый час может быть другим. Подробные сведения и примеры см. ниже в разделе «Использование свойства partitionedBy». | Нет |

### Использование свойства partitionedBy
Как упоминалось выше, вы можете задать динамические значения свойств folderPath и fileName для данных временных рядов. Для этого используйте раздел **partitionedBy**, макросы фабрики данных и системные переменные SliceStart и SliceEnd, которые указывают время начала и окончания каждого среза данных.

Дополнительные сведения о наборах данных временных рядов, планировании и срезах см. в статьях [Наборы данных](data-factory-create-datasets.md) и [Планирование и исполнение](data-factory-scheduling-and-execution.md).

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

## Свойства типа «Действие копирования озера данных Azure»  
Полный список разделов и свойств, используемых для определения действий, см. в статье [Основные сведения о конвейерах и действиях](data-factory-create-pipelines.md). Такие свойства, как имя, описание, входные и выходные таблицы, различные политики и т. д., доступны для всех типов действий.

То, какие свойства будут доступны в разделе typeProperties, зависит от типа действия, а в случае с действием копирования — еще и от типов источников и приемников.

**AzureDataLakeStoreSource** поддерживает следующие свойства в разделе **typeProperties**.

| Свойство | Описание | Допустимые значения | Обязательно |
| -------- | ----------- | -------------- | -------- |
| recursive | Указывает, следует ли читать данные рекурсивно из вложенных папок или только из указанной папки. | True (значение по умолчанию), False | Нет |



**AzureDataLakeStoreSink** поддерживает следующие свойства в разделе **typeProperties**.

| Свойство | Описание | Допустимые значения | Обязательно |
| -------- | ----------- | -------------- | -------- |
| copyBehavior | Определяет поведение копирования. | <p>**PreserveHierarchy:** сохраняет иерархию файлов в целевой папке, т. е. относительный путь каждого целевого файла в целевой папке будет совпадать с относительным путем соответствующего исходного файла в исходной папке.</p><p>**FlattenHierarchy:** все файлы из исходной папки копируются на первый уровень целевой папки. Имена целевых файлов будут генерироваться автоматически. </p><p>**MergeFiles:** все файлы из исходной папки объединяются в один файл. Если указать имя файла или большого двоичного объекта, объединенный файл будет создан с этим именем; в противном случае имя файла будет сформировано автоматически.</p> | Нет |


[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

[AZURE.INCLUDE [data-factory-type-conversion-sample](../../includes/data-factory-type-conversion-sample.md)]

[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

<!---HONumber=Nov15_HO1-->
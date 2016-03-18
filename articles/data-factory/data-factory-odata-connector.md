<properties 
	pageTitle="Перемещение данных из источников OData | Фабрика данных Azure" 
	description="Узнайте, как перемещать данные из источников OData с помощью фабрики данных Azure." 
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
	ms.date="02/18/2016" 
	ms.author="spelluru"/>

# Перемещение данных из источника OData с помощью фабрики данных Azure
В этой статье описано использование действия копирования в фабрике данных Azure для перемещения данных из источника OData в другое хранилище данных. Эта статья основана на статье о [действиях перемещения данных](data-factory-data-movement-activities.md), в которой приведены общие сведения о перемещении данных с помощью действия копирования и поддерживаемых сочетаниях хранилищ данных.

## Пример копирования данных из источника OData в BLOB-объект Azure

Из этого примера вы узнаете, как копировать данные из источника OData в хранилище BLOB-объектов Azure. Однако данные можно **напрямую** копировать в любой указанный [здесь](data-factory-data-movement-activities.md#supported-data-stores) приемник. Для этого используется действие копирования в фабрике данных Azure.
 
Образец состоит из следующих сущностей фабрики данных.

1.	Связанная служба типа [OData](#odata-linked-service-properties).
2.	Связанная служба типа [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties).
3.	Входной [набор данных](data-factory-create-datasets.md) типа [ODataResource](#odata-dataset-type-properties).
4.	Выходной набор данных [dataset](data-factory-create-datasets.md) типа [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
4.	[Конвейер](data-factory-create-pipelines.md) с действием копирования, в котором используются [RelationalSource](#odata-copy-activity-type-properties) и [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties).

В примере данные из запросов к источнику OData каждый час копируются в BLOB-объект Azure. Используемые в этих примерах свойства JSON описаны в разделах, следующих за примерами.

**Связанная служба OData**. В этом примере используется обычная проверка подлинности. Сведения о различных типах проверки подлинности, которые можно использовать, см. в разделе [Свойства связанной службы OData](#odata-linked-service-properties).

    {
		"name": "ODataLinkedService",
       	"properties": 
        {
        	"type": "OData",
           	"typeProperties": 
            {
            	"url": "http://services.odata.org/OData/OData.svc",
               "authenticationType": "Anonymous"
           	}
       	}
    }


**Связанная служба хранения Azure**

	{
	  	"name": "AzureStorageLinkedService",
		"properties": {
	    	"type": "AzureStorage",
		    "typeProperties": {
		      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
	    	}
	  	}
	}

**Входной набор данных OData**

Если для параметра external задать значение true и указать политику externalData, фабрика данных будет считать эту таблицу внешней по отношению к себе и не созданной в результате какого-либо действия в фабрике данных.
	
    {
    	"name": "ODataDataset",
		"properties": 
		{
    		"type": "ODataResource",
    		"typeProperties": 
			{
     			"path": "Products" 
    		},
    		"linkedServiceName": "ODataLinkedService",
    		"structure": [],
    		"availability": {
        		"frequency": "Hour",
        		"interval": 1
    		},
			"external": true,
    		"policy": {
            	"retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
	            "maximumRetry": 3				
			}
		}
	}

Обратите внимание, что указывать **путь** в определении набора данных необязательно.


**Выходной набор данных BLOB-объекта Azure**

Данные записываются в новый BLOB-объект каждый час (frequency: hour, interval: 1). Путь к папке BLOB-объекта вычисляется динамически на основе времени начала обрабатываемого среза. В пути к папке используется год, месяц, день и час времени начала.

	{
	    "name": "AzureBlobODataDataSet",
	    "properties": {
	        "type": "AzureBlob",
	        "linkedServiceName": "AzureStorageLinkedService",
	        "typeProperties": {
	            "folderPath": "mycontainer/odata/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
	            "format": {
	                "type": "TextFormat",
	                "rowDelimiter": "\n",
	                "columnDelimiter": "\t"
	            },
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
	            ]
	        },
	        "availability": {
	            "frequency": "Hour",
	            "interval": 1
	        }
	    }
	}



**Конвейер с действием копирования**

Конвейер содержит действие копирования (Copy), которое использует входной и выходной наборы данных (см. выше) и выполняется каждый час. В определении JSON конвейера для типа **source** установлено значение **RelationalSource**, а для типа **sink** — значение **BlobSink**. SQL-запрос, заданный для свойства **query**, выбирает самые последние (новейшие) данные из источника OData.
	
	{
	    "name": "CopyODataToBlob",
	    "properties": {
	        "description": "pipeline for copy activity",
	        "activities": [
	            {
	                "type": "Copy",
	                "typeProperties": {
	                    "source": {
	                        "type": "RelationalSource",
	                        "query": "?$select=Name, Description&$top=5",
	                    },
	                    "sink": {
	                        "type": "BlobSink",
	                        "writeBatchSize": 0,
	                        "writeBatchTimeout": "00:00:00"
	                    }
	                },
	                "inputs": [
	                    {
	                        "name": "ODataDataSet"
	                    }
	                ],
	                "outputs": [
	                    {
	                        "name": "AzureBlobODataDataSet"
	                    }
	                ],
	                "policy": {
	                    "timeout": "01:00:00",
	                    "concurrency": 1
	                },
	                "scheduler": {
	                    "frequency": "Hour",
	                    "interval": 1
	                },
	                "name": "ODataToBlob"
	            }
	        ],
	        "start": "2016-02-01T18:00:00Z",
	        "end": "2016-02-03T19:00:00Z"
	    }
	}


Обратите внимание, что указывать **запрос** в определении конвейера необязательно. **URL-адрес**, который служба фабрики данных использует для извлечения данных имеет следующий вид: URL-адрес, указанный в связанной службе (обязательно) + путь, указанный в наборе данных (необязательно) + запрос в конвейере (необязательно).

## Свойства связанной службы OData

В приведенной далее таблице содержится описание элементов JSON, которые относятся к связанной службе OData.

| Свойство | Описание | Обязательно |
| -------- | ----------- | -------- | 
| type | Для свойства type необходимо задать значение **OData**. | Да |
| url| URL-адрес службы OData. | Да |
| authenticationType | Тип проверки подлинности, используемый для подключения к источнику OData. Возможными значениями являются: "Анонимная" и "Обычная". | Да | 
| Имя пользователя | При использовании обычной проверки подлинности укажите имя пользователя. | Да (только при использовании обычной проверки подлинности) | 
| пароль | Введите пароль для учетной записи пользователя, указанной для выбранного имени пользователя. | Да (только при использовании обычной проверки подлинности) | 

### Использовать обычную проверку подлинности

    {
        "name": "inputLinkedService",
       "properties": 
        {
            "type": "OData",
           	"typeProperties": 
            {
               "url": "http://services.odata.org/OData/OData.svc",
               "authenticationType": "Basic",
                "username": "username",
               "password": "password"
           }
       }
    }

### Использовать анонимную проверку подлинности
	
    {
		"name": "ODataLinkedService",
       	"properties": 
        {
            "type": "OData",
           "typeProperties": 
            {
               "url": "http://services.odata.org/OData/OData.svc",
               "authenticationType": "Anonymous"
           }
       }
    }



## Свойства типа "Набор данных OData"

Полный список разделов и свойств, используемых для определения наборов данных, см. в статье [Наборы данных](data-factory-create-datasets.md). Разделы структуры, доступности и политики JSON набора данных одинаковы для всех типов наборов данных (SQL Azure, BLOB-объекты Azure, таблицы Azure и т. д.).

Раздел **typeProperties** во всех типах наборов данных разный. В нем содержится информация о расположении данных в хранилище данных. Раздел typeProperties набора данных с типом **ODataResource** (который включает набор данных OData) имеет следующие свойства:

| Свойство | Описание | Обязательно |
| -------- | ----------- | -------- |
| path | Путь к ресурсу OData | Нет | 

## Свойства типа "Действие копирования OData"

Полный список разделов и свойств, используемых для определения действий, см. в статье [Создание конвейеров](data-factory-create-pipelines.md). Такие свойства, как имя, описание, входные и выходные таблицы, различные политики и т. д., доступны для всех типов действий.

То, какие свойства будут доступны в разделе typeProperties, зависит от типа действия, а в случае с действием копирования — еще и от типов источников и приемников.

В случае действия копирования, когда источник относится к типу **RelationalSource** (который содержит OData), в разделе typeProperties доступны указанные далее свойства:

| Свойство | Описание | Пример | Обязательно |
| -------- | ----------- | -------------- | -------- |
| запрос | Используйте пользовательский запрос для чтения данных. | "?$select=Name, Description&$top=5" | Нет | 

[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

### Сопоставление типов для OData

Как упоминалось в статье о [действиях перемещения данных](data-factory-data-movement-activities.md), во время копирования типы источников автоматически преобразовываются в типы приемников. Такое преобразование выполняется в 2 этапа:

1. Преобразование собственных типов источников в тип .NET.
2. Преобразование типа .NET в собственный тип приемника.

При перемещении данных из хранилищ данных OData типы данных OData сопоставляются с типами .NET.


[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

[AZURE.INCLUDE [data-factory-type-repeatability-for-relational-sources](../../includes/data-factory-type-repeatability-for-relational-sources.md)]

<!-----HONumber=AcomDC_0224_2016-->
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
	ms.date="02/25/2016"
	ms.author="spelluru"/>

# Перемещение данных в озеро данных Azure и обратно с помощью фабрики данных Azure
Эта статья расскажет, как с помощью действия копирования в фабрике данных Azure перемещать информацию в хранилище озера данных Azure из другого хранилища данных и обратно. Эта статья основана на статье о [действиях перемещения данных](data-factory-data-movement-activities.md), в которой приведены общие сведения о перемещении данных с помощью действия копирования и поддерживаемых сочетаниях хранилищ данных.

> [AZURE.NOTE]
Чтобы создать конвейер с действием копирования для перемещения информации в озеро данных Azure и обратно, вам потребуется учетная запись хранилища озера данных Azure. Сведения о хранилище озера данных Azure см. в статье [Начало работы с хранилищем озера данных Azure](../data-lake-store/data-lake-store-get-started-portal.md).
>  
> В руководстве [Построение первого конвейера](data-factory-build-your-first-pipeline.md) подробно описаны процессы создания фабрики данных, связанных служб, наборов данных и конвейера. Для создания сущностей фабрики данных запустите предложенные фрагменты кода JSON в редакторе фабрики данных, в Visual Studio или в Azure PowerShell.

В следующих примерах показано, как копировать данные в хранилище озера данных Azure и хранилище BLOB-объектов Azure и обратно. Однако данные можно **напрямую** копировать из любых источников в любой указанный [здесь](data-factory-data-movement-activities.md#supported-data-stores) приемник. Это делается с помощью действия копирования в фабрике данных Azure.


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
	            "dataLakeUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
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

	![Кнопка "Авторизовать"](./media/data-factory-azure-data-lake-connector/authorize-button.png)

4. Войдите в систему с помощью своих учетных данных. После этого свойство **authorization** в файле JSON должно получить нужное значение.
5. Укажите значения необязательных параметров JSON, например **accountName**, **subscriptionID** и **resourceGroupName** или удалите эти свойства из JSON. Этот шаг можно пропустить.
6. Чтобы развернуть эту службу, нажмите кнопку **Развернуть** на панели команд.

> [AZURE.IMPORTANT] У кода авторизации, созданного с помощью кнопки **Авторизовать**, срок действия через некоторое время истекает. Когда **срок действия маркера истечет**, вам потребуется **повторно авторизоваться** с помощью кнопки **Авторизовать** и повторно развернуть связанную службу. Подробности см. в разделе [Пример связанной службы хранилища озера данных Azure](#azure-data-lake-store-linked-service-properties).



**Входной набор данных BLOB-объекта Azure**

Данные берутся из нового BLOB-объекта каждый час (frequency: hour, interval: 1). Путь к папке с BLOB-объектом и имя файла вычисляются динамически на основе времени начала обрабатываемого среза. В пути к папке используется год, месяц и день начала, а в имени файла — час начала. Когда для параметра external задано значение true, служба фабрики данных считает эту таблицу внешней по отношению к себе и не созданной в результате какого-либо действия в фабрике данных.

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

Конвейер содержит действие копирования (Copy), которое использует входной и выходной наборы данных (см. выше) и выполняется каждый час. В определении JSON конвейера для параметра **source** задается тип **BlobSource**, а для **sink** — тип **AzureDataLakeStoreSink**.

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
	            "dataLakeUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
				"sessionId": "<session ID>",
	            "authorization": "<authorization URL>"
	        }
	    }
	}

> [AZURE.NOTE] Чтобы получить URL-адрес авторизации, см. шаги, приведенные в предыдущем примере.

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

Конвейер содержит действие копирования (Copy), которое использует входной и выходной наборы данных (см. выше) и выполняется каждый час. В определении JSON конвейера для параметра **source** задается тип **AzureDataLakeStoreSource**, а для параметра **sink** — тип **BlobSink**.


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
| :-------- | :----------- | :-------- |
| type | Для свойства type следует задать значение **AzureDataLakeStore** | Да |
| dataLakeUri | Указывает сведения об учетной записи хранилища озера данных Azure в следующем формате: https://<Azure Data Lake account name>.azuredatalake.net/webhdfs/v1 | Да |
| authorization | Нажмите кнопку **Авторизовать** в **редакторе фабрики данных** и введите учетные данные. URL-адрес авторизации будет создан автоматически и присвоен этому свойству. | Да |
| sessionId | Идентификатор сеанса OAuth из сеанса авторизации oauth. Каждый идентификатор сеанса является уникальным и используется только один раз. Он создается автоматически при использовании редактора данных фабрики. | Да |  
| accountName | Имя учетной записи озера данных | Нет |
| subscriptionId | Идентификатор подписки Azure. | Нет (если не указан, используется подписка фабрики данных). |
| имя\_группы\_ресурсов | Имя группы ресурсов Azure | Нет (если не указано, используется группа ресурсов фабрики данных). |

У кода авторизации, созданного с помощью кнопки **Авторизовать**, срок действия через некоторое время истекает. Сроки действия для различных типов учетных записей пользователей см. в следующей таблице. При истечении **срока действия маркера** проверки подлинности может появиться следующее сообщение об ошибке: "Ошибка операции с учетными данными: invalid\_grant - AADSTS70002: ошибка при проверке учетных данных". AADSTS70008: срок действия предоставленных прав доступа истек или они были отозваны. Идентификатор отслеживания: d18629e8-af88-43c5-88e3-d8419eb1fca1 Идентификатор корреляции: fac30a0c-6be6-4e02-8d69-a776d2ffefd7 Временная отметка: 2015-12-15 21-09-31Z".


| Тип пользователя | Срок действия |
| :-------- | :----------- | 
| Учетные записи пользователей, которые НЕ управляются Azure Active Directory (@hotmail.com, @live.com и т. д.) | 12 часов |
| Учетные записи пользователей, которые управляются Azure Active Directory (AAD) | | 14 дней после последнего запуска среза. <p>90 дней, если срез, основанный на связанной службе на основе OAuth, выполняется по крайней мере раз в 14 дней.</p> |

Чтобы избежать этой ошибки или исправить ее, вам потребуется повторно авторизоваться с помощью кнопки **Авторизовать** и повторно развернуть связанную службу, когда **срок действия маркера истечет**. Значения свойств **sessionId** и **authorization** также можно задавать программно с помощью кода, приведенного в следующем разделе.

### Программное создание значений свойств sessionId и authorization 

    if (linkedService.Properties.TypeProperties is AzureDataLakeStoreLinkedService ||
        linkedService.Properties.TypeProperties is AzureDataLakeAnalyticsLinkedService)
    {
        AuthorizationSessionGetResponse authorizationSession = this.Client.OAuth.Get(this.ResourceGroupName, this.DataFactoryName, linkedService.Properties.Type);

        WindowsFormsWebAuthenticationDialog authenticationDialog = new WindowsFormsWebAuthenticationDialog(null);
        string authorization = authenticationDialog.AuthenticateAAD(authorizationSession.AuthorizationSession.Endpoint, new Uri("urn:ietf:wg:oauth:2.0:oob"));

        AzureDataLakeStoreLinkedService azureDataLakeStoreProperties = linkedService.Properties.TypeProperties as AzureDataLakeStoreLinkedService;
        if (azureDataLakeStoreProperties != null)
        {
            azureDataLakeStoreProperties.SessionId = authorizationSession.AuthorizationSession.SessionId;
            azureDataLakeStoreProperties.Authorization = authorization;
        }

        AzureDataLakeAnalyticsLinkedService azureDataLakeAnalyticsProperties = linkedService.Properties.TypeProperties as AzureDataLakeAnalyticsLinkedService;
        if (azureDataLakeAnalyticsProperties != null)
        {
            azureDataLakeAnalyticsProperties.SessionId = authorizationSession.AuthorizationSession.SessionId;
            azureDataLakeAnalyticsProperties.Authorization = authorization;
        }
    }

Подробные сведения о классах фабрики данных, используемых в коде, см. в разделах [Класс AzureDataLakeStoreLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestorelinkedservice.aspx), [Класс AzureDataLakeAnalyticsLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakeanalyticslinkedservice.aspx) и [Класс AuthorizationSessionGetResponse](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.authorizationsessiongetresponse.aspx). Для использования класса WindowsFormsWebAuthenticationDialog необходимо добавить ссылку на Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms.dll.
 

## Свойства типа "Набор данных озера данных Azure"

Полный список разделов и свойств JSON, используемых для определения наборов данных, вы найдете в статье [Наборы данных](data-factory-create-datasets.md). Разделы структуры, доступности и политики JSON набора данных одинаковы для всех типов наборов данных (SQL Azure, BLOB-объекты Azure, таблицы Azure и т. д.).

Раздел **typeProperties** различается для разных типов наборов данных. Он содержит сведения о расположении данных в хранилище данных, формате данных и т. д. Раздел typeProperties набора данных типа **AzureDataLakeStore** имеет следующие свойства.

| Свойство | Описание | Обязательно |
| :-------- | :----------- | :-------- |
| folderPath | Путь к контейнеру и папке в хранилище озера данных Azure. | Да |
| fileName | <p>Имя файла в хранилище озера данных Azure. Свойство fileName является необязательным и в нем учитывается регистр знаков. </p><p>Если вы укажете имя файла, любое действие (включая копирование) будет выполняться для конкретного файла.</p><p>Если свойство fileName не указано, будут скопированы все файлы из папки folderPath входного набора данных.</p><p>Если свойство fileName не указано для выходного набора данных, имя созданного файла будет иметь следующий формат: Data.<Guid>.txt (например, Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt).</p> | Нет |
| partitionedBy | Необязательное свойство. Его можно использовать, чтобы указать динамические путь к папке и имя файла для временного ряда данных. Например, путь к папке (значение folderPath) каждый час может быть другим. Подробные сведения и примеры см. ниже в разделе "Использование свойства partitionedBy". | Нет |
| format | Поддерживаются два типа форматов: **TextFormat** и **AvroFormat**. Свойству type в разделе format необходимо присвоить одно из этих значений. Если для свойства Format указано значение TextFormat, для формата можно указать дополнительные свойства. Дополнительную информацию см. в разделе [Определение TextFormat](#specifying-textformat) ниже. | Нет |
| compression | Укажите тип и уровень сжатия данных. Поддерживаемые типы: **GZip**, **Deflate** и **BZip2**. Поддерживаемые уровни: **Optimal** и **Fastest**. Обратите внимание, что в данный момент параметры сжатия для данных в формате **AvroFormat** не поддерживаются. Дополнительные сведения см. в разделе [Поддержка сжатия](#compression-support). | Нет |

### Использование свойства partitionedBy
Как упоминалось выше, динамические значения folderPath и fileName для данных временных рядов можно задать в разделе **partitionedBy**, а также с помощью макросов фабрики данных и системных переменных SliceStart и SliceEnd, которые указывают время начала и окончания среза данных.

Дополнительные сведения о наборах данных временных рядов, планировании и срезах см. в статьях [Создание наборов данных](data-factory-create-datasets.md) и [Планирование и исполнение](data-factory-scheduling-and-execution.md).

#### Пример 1

	"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
	"partitionedBy":
	[
	    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
	],

В примере выше {Slice} заменяется значением SliceStart (системная переменная фабрики данных) в формате YYYYMMDDHH. SliceStart указывает время начала среза. Значение folderPath отличается для каждого среза. Например: wikidatagateway/wikisampledataout/2014100103 или wikidatagateway/wikisampledataout/2014100104.

#### Пример 2

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

Если для свойства format задано значение **TextFormat**, в разделе **Format** вы можете указать следующие **необязательные** свойства.

| Свойство | Описание | Обязательно |
| -------- | ----------- | -------- |
| columnDelimiter | Символ, используемый в качестве разделителя столбцов в файле. В настоящее время допускается только один символ. Этот тег является необязательным. Значение по умолчанию — запятая (,). | Нет |
| rowDelimiter | Символ, используемый в качестве необработанного разделителя в файле. В настоящее время допускается только один символ. Этот тег является необязательным. По умолчанию используется одно из следующих значений: [\\r\\n, \\r, \\n]. | Нет |
| escapeChar | <p>Специальный символ, используемый для экранирования разделителя столбцов в содержимом. Этот тег является необязательным. Значение по умолчанию отсутствует. Для этого свойства следует указывать не более одного символа.</p><p>Например, если в качестве разделителя столбцов используется запятая и в тексте встречается этот символ (например: Hello, world), назначьте $ escape-символом и используйте в исходном коде строку Hello$, world.</p><p>Обратите внимание, что для таблицы нельзя одновременно указывать свойства escapeChar и quoteChar.</p> | Нет | 
| quoteChar | <p>Специальный символ, который используется для заключения строкового значения в кавычки. Разделители столбцов и строк внутри кавычек будут рассматриваться как часть строкового значения. Этот тег является необязательным. Значение по умолчанию отсутствует. Для этого свойства следует указывать не более одного символа.</p><p>Например, если в качестве разделителя столбцов используется запятая (,) и нужно, чтобы этот символ встречался в тексте (например, <Hello  world>), назначьте в качестве символа кавычек прямые кавычки (") и используйте в исходном коде строку <"Hello, world">. Это свойство применимо ко входным и выходным таблицам.</p><p>Обратите внимание, что для таблицы невозможно одновременно указывать свойства escapeChar и quoteChar.</p> | Нет |
| nullValue | <p>Символы, используемые для обозначения нулевого значения в содержимом файла большого двоичного объекта. Этот тег является необязательным. Значение по умолчанию — \\N.</p><p>Например, значение NaN (на основе приведенного выше примера) в большом двоичном объекте будет преобразовано в нулевое значение при копировании, например на сервер SQL Server.</p> | Нет |
| encodingName | Имя кодировки. Список допустимых имен кодировок см. в описании свойства [Encoding.EncodingName](https://msdn.microsoft.com/library/system.text.encoding.aspx). Например: windows-1250 или shift\_jis. По умолчанию используется значение UTF-8. | Нет | 

#### Примеры
В следующем примере показаны некоторые свойства формата для TextFormat.

	"typeProperties":
	{
	    "folderPath": "mycontainer/myfolder",
	    "fileName": "myfilename"
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

Сведения об использовании формата Avro в таблице Hive см. в [учебнике по Apache Hive](https://cwiki.apache.org/confluence/display/Hive/AvroSerDe).


### Поддержка сжатия  
Обработка больших наборов данных может привести к возникновению узких мест ввода-вывода и сети. Поэтому использование сжатых данных в хранилищах может не только ускорить передачу данных по сети и освободить место на диске, но также обеспечить и значительное повышение производительности при обработке больших данных. В настоящее время сжатие поддерживается для файловых хранилищ данных, таких как хранилище больших двоичных объектов Azure или локальная файловая система.

Чтобы указать сжатие для набора данных, используйте свойство **compression** в наборе данных JSON, как показано в следующем примере.

	{  
		"name": "AzureDatalakeStoreDataSet",  
	  	"properties": {  
	    	"availability": {  
	    		"frequency": "Day",  
	    	  	"interval": 1  
	    	},  
	    	"type": "AzureDatalakeStore",  
	    	"linkedServiceName": "DataLakeStoreLinkedService",  
	    	"typeProperties": {  
	    		"fileName": "pagecounts.csv.gz",  
	    	  	"folderPath": "compression/file/",  
	    	  	"compression": {  
	    	    	"type": "GZip",  
	    	    	"level": "Optimal"  
	    	  	}  
    		}  
	  	}  
	}  
 
Обратите внимание, что раздел **compression** содержит два свойства:
  
- **Type:** кодек сжатия, который может иметь значение **GZIP**, **Deflate** или **BZIP2**.  
- **Level:** коэффициент сжатия, который может иметь значение **Optimal** или **Fastest**. 
	- **Fastest:** операция сжатия должна выполняться как можно быстрее, даже если итоговый файл сжимается не оптимально. 
	- **Optimal**: операция сжатия должна выполняться оптимально, даже если для ее завершения требуется больше времени. 
	
	Дополнительные сведения см. в разделе [Уровень сжатия](https://msdn.microsoft.com/library/system.io.compression.compressionlevel.aspx).

Предположим, что приведенный выше пример набора данных используется в качестве результата операции копирования; эта операция сжимает выходные данные с использованием кодека GZIP и оптимального коэффициента сжатия, а затем записывает сжатые данные в файл с именем pagecounts.csv.gz в хранилище озера данных Azure.

При задании свойства "compression" во входном наборе входных данных JSON конвейер может считывать сжатые данные из источника, а при задании этого свойства в результирующем наборе данных JSON операция копирования может записывать сжатые данные в место назначения. Ниже приведено несколько примеров сценариев:

- Считайте сжатые с помощью кодека GZIP данные из хранилища озера данных Azure, распакуйте их и запишите результирующие данные в базу данных SQL Azure. В этом случае вы определяете входной набор данных хранилища озера данных Azure с помощью свойства "compression" JSON. 
- Считайте данные из обычного текстового файла в локальной файловой системе, сожмите их в формате GZip и запишите сжатые данные в хранилище озера данных Azure. В этом случае вы определяете выходной набор озера данных Azure с помощью свойства "compression" JSON.  
- Считайте сжатые с помощью кодека GZIP данные из хранилища озера данных Azure, распакуйте их и сожмите с помощью BZIP2, а затем запишите результирующие данные в хранилище озера данных Azure. В этом случае вы определяете входной набор данных хранилища озера данных Azure, установив для типа сжатия значение GZIP, и результирующий набор данных, установив для типа сжатия значение BZIP2.   


## Свойства типа "Действие копирования озера данных Azure"  
Полный список разделов и свойств, используемых для определения действий, см. в статье [Создание конвейеров](data-factory-create-pipelines.md). Такие свойства, как имя, описание, входные и выходные таблицы, различные политики и т. д., доступны для всех типов действий.

То, какие свойства будут доступны в разделе typeProperties, зависит от типа действия, а в случае с действием копирования — еще и от типов источников и приемников.

**AzureDataLakeStoreSource** поддерживает следующие свойства в разделе **typeProperties**.

| Свойство | Описание | Допустимые значения | Обязательно |
| -------- | ----------- | -------------- | -------- |
| recursive | Указывает, следует ли читать данные рекурсивно из вложенных папок или только из указанной папки. | True (значение по умолчанию), False | Нет |



**AzureDataLakeStoreSink** поддерживает следующие свойства в разделе **typeProperties**.

| Свойство | Описание | Допустимые значения | Обязательно |
| -------- | ----------- | -------------- | -------- |
| copyBehavior | Определяет поведение копирования. | <p>**PreserveHierarchy:** сохраняет иерархию файлов в целевой папке, т. е. относительный путь каждого целевого файла в целевой папке будет совпадать с относительным путем соответствующего исходного файла в исходной папке.</p><p>**FlattenHierarchy:** все файлы из исходной папки копируются на первый уровень целевой папки. Имена целевых файлов будут генерироваться автоматически. </p><p>**MergeFiles:** все файлы из исходной папки объединяются в один файл. Если указано имя файла или большого двоичного объекта, именем объединенного файла будет указанное имя; в противном случае имя файла будет сформировано автоматически.</p> | Нет |


[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

[AZURE.INCLUDE [data-factory-type-conversion-sample](../../includes/data-factory-type-conversion-sample.md)]

[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

<!---HONumber=AcomDC_0302_2016-->
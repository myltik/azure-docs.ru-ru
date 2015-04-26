<properties title="Azure Data Factory - Create Predictive Pipelines using Data Factory and Azure Machine Learning" pageTitle="Фабрика данных. Создание прогнозирующих конвейеров с помощью фабрик данных и машинного обучения | Azure" description="Описывает создание создания прогнозирующего конвейеры, с помощью фабрики Azuer данных и Azure машинного обучения" metaKeywords=""  services="data-factory" solutions=""  documentationCenter="" authors="spelluru" manager="jhubbard" editor="monicar" />

<tags ms.service="data-factory" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="12/05/2014" ms.author="spelluru" />

# Создание прогнозирующих конвейеров с помощью фабрик данных Azure и машинного обучения Azure 
Можно запустить в эксплуатацию опубликованные модели [машинного обучения Azure][azure-machine-learning] в конвейеры фабрики данных. Такие конвейеры называются прогнозирующими конвейерами. Для создания прогнозирующего конвейера потребуется следующее.

-	Ключ API опубликованной модели рабочей области и URL-адрес количественной оценки (см. рисунок ниже)
-	Хранилище больших двоичных объектов Azure с входным CSV-файлом, который необходимо оценить.
-	Хранилище больших двоичных объектов Azure, в котором будут содержаться результаты количественной оценки CSV-файла.

	![Machine Learning Dashboard][machine-learning-dashboard]

	URL-адрес количественной оценки для AzureMLLinkedService можно получить, как показано на рисунке выше, но без "**help**":  https://ussouthcentral.services.azureml.net/workspaces/da9e350b758e44b2812a6218d507e216/services/8c91ff373a81416f8f8e0d96a1162681/jobs/

**Прогнозирующий конвейер** состоит из следующих частей:

-	Входные и выходные таблицы
-	Хранилища Azure и связанные службы Azure ML
-	Конвейер с действием количественной оценки Azure ML

## Пример



1. Создание связанной службы для хранилища Azure. Если входные и выходные файлы оценки находятся в разных учетных записях хранения, то потребуются две связанные службы. Ниже приведен пример JSON:

		{
		    "name": "StorageLinkedService",
		    "properties":
		    {
		        "type": "AzureStorageLinkedService",
		        "connectionString": "DefaultEndpointsProtocol=https;AccountName=[acctName];AccountKey=[acctKey]"
		    }
		}

2. Создание входных и выходных таблиц фабрики данных Azure. Обратите внимание, что в отличие от некоторых других таблиц фабрики данных обе эти таблицы должны содержать оба значения **folderPath** и **fileName**. Можно использовать секционирование для выполнения каждого пакета (каждого среза данных) для обработки или создания уникальных входных и выходных файлов. Скорее всего, потребуется включить некоторые вышестоящие действия для преобразования входных данных в формат CSV-файла и его размещения в учетной записи хранения для каждого среза. В этом случае не будут включены параметры waitOnExternal, как показано на примере ниже, и ScoringInputBlob будет выходной таблицей другого действия.

		{  
			"name":"ScoringInputBlob",
			"properties":
			{  
					"location":
					{  
						"type":"AzureBlobLocation",
						"folderPath":"azuremltesting/input",
						"fileName":"in.csv",
						"format":
						{ 
							"type":"TextFormat",
							"columnDelimiter":","
						},
						"linkedServiceName":"StorageLinkedService"
					},
					"availability":
					{  
						"frequency":"Day",
						"interval":1,
						"waitOnExternal":
						{
		                	"retryInterval": "00:01:00",
		                	"retryTimeout": "00:10:00",
		                	"maximumRetry": 3
		            	}
		      		}
		   		}
			}

3. В этом примере выходных данных используется секционирование для создания уникального выходного пути при выполнении каждого из срезов. Без этого действие перезапишет файл.

		{  
		   "name":"ScoringResultBlob",
		   "properties":
			{  
		        "location":
				{  
		            "type":"AzureBlobLocation",
		            "folderPath": "azuremltesting/scored/{folderpart}/",
		            "fileName": "{filepart}result.csv",
		            "partitionedBy": [ 
		                 { "name": "folderpart", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMdd" } },
		                 { "name": "filepart", "value": { "type": "DateTime", "date": "SliceStart", "format": "HHmmss" } } 
		             ], 
		            "format":{  
		              "type":"TextFormat",
		              "columnDelimiter":","
		            },
		            "linkedServiceName":"StorageLinkedService"
		        },
		        "availability":
				{  
		            "frequency":"Day",
		            "interval":15
		        }
		   }
		}


4. Создайте связанную службу типа **AzureMLLinkedService**, предоставляющую ключ API и URL-адрес количественной оценки модели.
		
		{
		    "name": "MyAzureMLLinkedService",
		    "properties":
		    {
		        "type": "AzureMLLinkedService",
		        "mlEndpoint":"https://[batch scoring endpoint]/jobs",
		        "apiKey":"[apikey]"
		    }
		}

5. И наконец, создайте конвейер, содержащий **AzureMLBatchScoringActivity**. Он получит расположение входного файла из входных таблиц, вызовет API количественной оценки AzureML и скопирует выходные данные количественной оценки в большой двоичный объект, переданный в таблице выходных данных. В отличие от некоторых других действий фабрики данных AzureMLBatchScoringActivity может иметь только одну входную и одну выходную таблицы.

		 {
		    "name": "PredictivePipeline",
		    "properties":
		    {
		        "description" : "use AzureML model",
		        "activities":
		        [
		         {  
		            "name":"MLActivity",
		            "type":"AzureMLBatchScoringActivity",
		            "description":"prediction analysis on batch input",
		            "inputs": [ { "name": "ScoringInputBlob" } ],
		            "outputs":[ { "name": "ScoringResultBlob" } ],
		            "linkedServiceName":"MyAzureMLLinkedService",
		            "policy":{  
		               "concurrency":3,
		               "executionPriorityOrder":"NewestFirst",
		               "retry":1,
		               "timeout":"02:00:00"
		            }
		         }
		        ]
		    }
		}

## См. также

Статья | Описание
------ | ---------------
[Введение в фабрику данных Azure][adf-introduction] | Эта статья содержит обзор службы фабрики данных Azure и поддерживаемых сценариев. 
[Начало работы с фабрикой данных Azure][adf-getstarted] | В этой статье содержится базовый учебник, в котором описаны пошаговые инструкции по созданию и отслеживанию образца фабрики данных.
[Включение конвейеров для работы с локальными данными][use-onpremises-datasources] | Эта статья содержит пошаговое руководство, которое показывает, как скопировать данные из локальной базы данных SQL Server в большой двоичный объект Azure.
[Использование сценариев Pig и Hive с фабрикой данных][use-pig-and-hive-with-data-factory] | В этой статье показано, как использовать действия HDInsight для выполнения сценариев Hive и Pig, чтобы обработать входные данные и получить выходные данные. 
[Учебник. Перемещение и обработка файлов журнала с помощью фабрики данных][adf-tutorial] | В этой статье содержится подробное пошаговое руководство, которое показывает, как реализовать практически реальный сценарий с использованием фабрики данных Azure для преобразования данных из файлов журнала в подробные данные.
[Использование пользовательских действий в фабрике данных][use-custom-activities] | В этой статье приводится подробное руководство с пошаговыми инструкциями по созданию пользовательских действий и использованию их в конвейере. 
[Устранение неполадок фабрики данных][troubleshoot] | В этой статье описывается устранение неполадок в работе фабрики данных Azure. Вы можете использовать руководство из этой статьи на учебной фабрике данных ADFTutorialDataFactory, намеренно создав ошибки (удалив таблицу в базе данных SQL Azure). 
[Справочник разработчика фабрики данных Azure][developer-reference] | Справочник разработчика содержит полную справку по командлетам, сценарию JSON, функциям и т. д. 

[adf-introduction]: ../data-factory-introduction
[adf-getstarted]: ../data-factory-get-started
[use-onpremises-datasources]: ../data-factory-use-onpremises-datasources
[use-pig-and-hive-with-data-factory]: ../data-factory-pig-hive-activities
[adf-tutorial]: ../data-factory-tutorial
[use-custom-activities]: ../data-factory-use-custom-activities
[troubleshoot]: ../data-factory-troubleshoot
[data-factory-introduction]: ../data-factory-introduction  
[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908

[azure-machine-learning]: http://azure.microsoft.com/ru-ru/services/machine-learning/
[machine-learning-dashboard]: ./media/data-factory-create-predictive-pipelines/AzureMLDashboard.png


<!--HONumber=35.2-->

<properties
	pageTitle="Загрузка данных с помощью фабрики данных Azure | Microsoft Azure"
	description="Сведения о том, как загружать данные с помощью фабрики данных Azure."
	services="sql-data-warehouse"
	documentationCenter="NA"
	authors="lodipalm"
	manager="barbkess"
	editor=""
	tags="azure-sql-data-warehouse"/>
<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="11/19/2015"
   ms.author="lodipalm"/>

# Загрузка данных с помощью фабрики данных Azure

> [AZURE.SELECTOR]
- [Data Factory](sql-data-warehouse-get-started-load-with-azure-data-factory.md)
- [PolyBase](sql-data-warehouse-get-started-load-with-polybase.md)
- [BCP](sql-data-warehouse-load-with-bcp.md)

 В этом учебнике показано, как создать в фабрике данных Azure конвейер, который будет перемещать данные из больших двоичных объектов службы хранилища Azure в хранилище данных SQL. С помощью описанных ниже шагов вы сможете:

+ настроить образец данных в большом двоичном объекте службы хранилища Azure;
+ подключить ресурсы к фабрике данных Azure;
+ создать конвейер для перемещения данных из больших двоичных объектов службы хранилища в хранилище данных SQL.

>[AZURE.VIDEO loading-azure-sql-data-warehouse-with-azure-data-factory]

## Ресурсы

Для работы с этим учебником нужны такие ресурсы:

   + **Большой двоичный объект службы хранилища Azure**. Он будет служить источником данных для конвейера. Вы можете использовать существующий большой двоичный объект или [подготовить новый](../storage/storage-create-storage-account/).

   + **Хранилище данных SQL**. В этом руководстве описано, как перемещать данные в хранилище данных SQL. Если вы еще не настроили экземпляр хранилища, узнайте, как это сделать, из [этой статьи](sql-data-warehouse-get-started-provision.md). Кроме того, ваш экземпляр нужно настроить с помощью нашего набора данных AdventureWorks DW. Если при подготовке хранилища данных вы не использовали демонстрационные данные, вы можете [загрузить их вручную](sql-data-warehouse-get-started-manually-load-samples.md).

   + **Фабрика данных Azure**. Фабрика данных Azure завершит загрузку. Если вам нужна дополнительная информация о настройке фабрики или создании конвейеров, см. [эту статью](../data-factory/data-factory-build-your-first-pipeline-using-editor/).

Когда все компоненты будут готовы, вы сможете начать подготовку данных и создание конвейера фабрики данных Azure.

## Образец данных

Помимо разных частей конвейера, нам потребуется также образец данных, с помощью которого можно практиковаться в загрузке данных в фабрике данных Azure.

1. Сначала [скачайте демонстрационные данные](https://migrhoststorage.blob.core.windows.net/adfsample/FactInternetSales.csv). Эти данные используются вместе с уже имеющимися в образце данными продаж еще за три года.

2. После скачивания данных вы можете переместить их в хранилище больших двоичных объектов, запустив сценарий, приведенный ниже, в AZCopy.

        AzCopy /Source:<Sample Data Location>  /Dest:https://<storage account>.blob.core.windows.net/<container name> /DestKey:<storage key> /Pattern:FactInternetSales.csv

	Дополнительные сведения об установке программы AZCopy и работе с ней см. в [документации по AZCopy](../storage/storage-use-azcopy/).

Теперь данные находятся там, где нужно. Поэтому в фабрике данных можно создать конвейер, который будет перемещать данные из учетной записи хранения в хранилище данных SQL.

## Использование фабрики данных Azure

Теперь, настроив все компоненты, мы можем начать настройку конвейера. Для этого нужно перейти к вашему экземпляру фабрики данных Azure на портале предварительной версии Azure. Чтобы это сделать, щелкните элемент [Портал Azure](portal.azure.com) и выберите фабрику данных в меню слева.

Чтобы после этого настроить конвейер фабрики данных Azure для передачи данных в хранилище данных, нужно выполнить три действия: связать службы, определить наборы данных и создать конвейер.

### Создание связанных служб

Сначала нужно связать учетную запись хранения Azure и хранилище данных SQL с фабрикой данных.

1. Начните регистрацию. Для этого щелкните раздел «Связанные службы» фабрики данных, а затем — элемент «Создать хранилище данных». Выберите имя для регистрации вашего хранилища Azure. Выберите типа «Хранилище Azure» и введите имя и ключ учетной записи.

2. Чтобы зарегистрировать хранилище данных SQL, нужно перейти к разделу «Разработка и развертывание», щелкнуть «Создать хранилище данных», а затем — «Хранилище данных SQL Azure». Затем необходимо будет заполнить приведенный ниже шаблон.

		{
		    "name": "<Linked Service Name>",
		    "properties": {
		        "description": "",
		        "type": "AzureSqlDW",
		        "typeProperties": {
		            "connectionString": "Data Source=tcp:<server name>.database.windows.net,1433;Initial Catalog=<server name>;Integrated Security=False;User ID=<user>@<servername>;Password=<password>;Connect Timeout=30;Encrypt=True"
		        }
		    }
		}

### Регистрация наборов данных

После создания связанных служб следует определить наборы данных. Это означает, что нужно задать структуру данных, перемещаемых из вашего хранилища в хранилище данных. Вы можете прочитать об этом подробнее.

1. Сначала перейдите к разделу фабрики данных «Разработка и развертывание».

2. Чтобы привязать хранилище к фабрике данных, щелкните элемент «Создать набор данных», а затем — «Хранилище BLOB-объектов Azure». Для определения данных в хранилище BLOB-объектов Azure вы можете использовать приведенный ниже сценарий.

		{
			"name": "<Dataset Name>",
			"properties": {
				"type": "AzureBlob",
				"linkedServiceName": "<linked storage name>",
				"typeProperties": {
					"folderPath": "<containter name>",
					"fileName": "FactInternetSales.csv",
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



3. Теперь мы настроим также набор данных, предназначенный для хранилища данных SQL. Начнем так же, то есть щелкнем элемент «Создать набор данных», а затем — «Хранилище данных SQL Azure».

		{
		  "name": "<dataset name>",
		  "properties": {
		    "type": "AzureSqlDWTable",
		    "linkedServiceName": "<linked data warehouse name>",
		    "typeProperties": {
		      "tableName": "FactInternetSales"
		    },
		    "availability": {
		      "frequency": "Hour",
		      "interval": 1
		    }
		  }
		}

		{
		  "name": "DWDataset",
		  "properties": {
			"type": "AzureSqlDWTable",
			"linkedServiceName": "AzureSqlDWLinkedService",
			"typeProperties": {
			  "tableName": "FactInternetSales"
			},
			"availability": {
			  "frequency": "Hour",
			  "interval": 1
			}
		  }
		}

### Настройка конвейера

Наконец, настроим и запустим конвейер в фабрике данных Azure. Эта операция будет выполнять фактическое перемещение данных. Полный список операций, которые можно выполнять в хранилище данных SQL и фабрике данных Azure, см. [здесь](../data-factory/data-factory-azure-sql-data-warehouse-connector/).

Теперь в разделе «Разработка и развертывание» щелкните элемент «Дополнительные команды», а затем — «Создать конвейер». Создав конвейер, вы сможете передавать данные в хранилище данных с помощью приведенного ниже кода.

	{
	"name": "<Pipeline Name>",
	"properties": {
		"description": "<Description>",
		"activities": [
			{
				"type": "Copy",
				"typeProperties": {
					"source": {
						"type": "BlobSource",
						"skipHeaderLineCount": 1
					},
					"sink": {
						"type": "SqlDWSink",
						"writeBatchSize": 0,
						"writeBatchTimeout": "00:00:10"
					}
				},
				"inputs": [
					{
						"name": "<Storage Dataset>"
					}
				],
				"outputs": [
					{
						"name": "<Data Warehouse Dataset>"
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
				"name": "Sample Copy",
				"description": "Copy Activity"
			}
		],
		"start": "<Date YYYY-MM-DD>",
		"end": "<Date YYYY-MM-DD>",
		"isPaused": false
	}
	}
	

<!---HONumber=AcomDC_1125_2015-->
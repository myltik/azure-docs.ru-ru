<properties 
	pageTitle="Потоковая активность Hadoop" 
	description="Узнайте, как с помощью действия потоковой передачи Hadoop в фабрике данных Azure выполнять программы потоковой передачи Hadoop в собственном кластере HDInsight или кластере по требованию." 
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

# Потоковая активность Hadoop
Можно использовать действие HDInsightStreamingActivity для вызова задания потоковой передачи Hadoop из конвейера фабрики данных Azure. В следующем фрагменте JSON показан синтаксис для использования действия HDInsightStreamingActivity в JSON-файле конвейера.

Действие потоковой передачи HDInsight в [конвейере](data-factory-create-pipelines.md) фабрики данных выполняет программы потоковой передачи Hadoop [самостоятельно](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) или в кластере HDInsight на базе Windows/Linux [по требованию](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service). Данная статья основана на материалах статьи о [действиях преобразования данных](data-factory-data-transformation-activities.md), в которой приведен общий обзор преобразования данных и список поддерживаемых действий преобразования.

## Образец JSON
Кластер HDInsight автоматически заполняется примерами программ (wc.exe и cat.exe) и данных (davinci.txt). По умолчанию именем контейнера, используемым кластером HDInsight, является имя самого кластера. Например, если кластеру задано имя myhdicluster, именем связанного контейнера больших двоичных объектов будет myhdicluster.

	{
	    "name": "HadoopStreamingPipeline",
	    "properties": {
	        "description": "Hadoop Streaming Demo",
	        "activities": [
	            {
	                "type": "HDInsightStreaming",
	                "typeProperties": {
	                    "mapper": "cat.exe",
	                    "reducer": "wc.exe",
	                    "input": "wasb://<nameofthecluster>@spestore.blob.core.windows.net/example/data/gutenberg/davinci.txt",
	                    "output": "wasb://<nameofthecluster>@spestore.blob.core.windows.net/example/data/StreamingOutput/wc.txt",
	                    "filePaths": [
	                        "<nameofthecluster>/example/apps/wc.exe",
	                        "<nameofthecluster>/example/apps/cat.exe"
	                    ],
	                    "fileLinkedService": "StorageLinkedService",
	                    "getDebugInfo": "Failure"
	                },
	                "outputs": [
	                    {
	                        "name": "StreamingOutputDataset"
	                    }
	                ],
	                "policy": {
	                    "timeout": "01:00:00",
	                    "concurrency": 1,
	                    "executionPriorityOrder": "NewestFirst",
	                    "retry": 1
	                },
	                "scheduler": {
	                    "frequency": "Day",
	                    "interval": 1
	                },
	                "name": "RunHadoopStreamingJob",
	                "description": "Run a Hadoop streaming job",
	                "linkedServiceName": "HDInsightLinkedService"
	            }
	        ],
	        "start": "2014-01-04T00:00:00Z",
	        "end": "2014-01-05T00:00:00Z"
	    }
	}

Обратите внимание на следующие моменты.

1. Присвойте параметру **linkedServiceName** имя связанной службы, которое указывает на ваш кластер HDInsight, где будет выполняться задание потоковой передачи MapReduce.
2. Задайте в качестве типа действия значение **HDInsightStreaming**.
3. Для свойства **mapper** укажите имя исполняемого файла mapper. В этом примере таким файлом является cat.exe.
4. Для свойства **reducer** укажите имя исполняемого файла reducer. В этом примере таким файлом является wc.exe.
5. Для свойства типа **input** укажите входной файл (включая местоположение) для свойства mapper. В примере wasb://adfsample@<учетная\_запись>.blob.core.windows.net/example/data/gutenberg/davinci.txt фрагмент adfsample — это контейнер больших двоичных объектов, example/data/Gutenberg — папка, а davinci.txt — большой двоичный объект.
6. Для свойства типа **output** укажите выходной файл (включая местоположение) для reducer. Результат задания потоковой передачи Hadoop записывается в расположение, заданное для этого свойства.
7. В разделе **filePaths** укажите пути для исполняемых файлов mapper и reducer. В примере adfsample/example/apps/wc.exe adfsample — это контейнер больших двоичных объектов, example/apps — это папка, а wc.exe — это исполняемый файл.
8. Для свойства **fileLinkedService** укажите связанную службу хранилища Azure, которая представляет хранилище Azure, где хранятся указанные в разделе filePaths файлы.
9. Для свойства **arguments** укажите аргументы для задания потоковой передачи.
10. Свойство **GetDebugInfo** является необязательным элементом. Если для него установлено значение Failure, журналы скачиваются только при сбое. Если для него установлено значение All, журналы скачиваются всегда, независимо от состояния выполнения.

> [AZURE.NOTE] Как показано в примере, вам нужно указать выходной набор данных для действия потоковой передачи Hadoop для свойства **outputs**. Это просто фиктивный набор данных, необходимый для соблюдения расписания конвейера. Указывать входной набор данных для действия для свойства **inputs** не требуется.

	
## Пример
Конвейер в этом пошаговом руководстве запускает в кластере Azure HDInsight программу потоковой передачи Map/Reduce для подсчета слов.

### Связанные службы

#### Связанная служба хранения Azure
Сначала необходимо создать связанную службу для связи службы хранилища Azure, используемой в кластере Azure HDInsight, с фабрикой данных Azure. При копировании и вставке следующего кода не забудьте заменить имя и ключ учетной записи на имя и ключ хранилища Azure.

	{
	    "name": "StorageLinkedService",
	    "properties": {
	        "type": "AzureStorage",
	        "typeProperties": {
	            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>"
	        }
	    }
	}

#### Связанная служба Azure HDInsight
Далее необходимо создать связанную службу для связи кластера Azure HDInsight с фабрикой данных Azure. При копировании и вставке следующего кода не забудьте заменить имя кластера HDInsight на имя вашего кластера HDInsight и изменить значения имени пользователя и пароля.
	
	{
	    "name": "HDInsightLinkedService",
	    "properties": {
	        "type": "HDInsight",
	        "typeProperties": {
	            "clusterUri": "https://<HDInsight cluster name>.azurehdinsight.net",
	            "userName": "admin",
	            "password": "**********",
	            "linkedServiceName": "StorageLinkedService"
	        }
	    }
	}

### Наборы данных

#### Выходной набор данных
Конвейер в этом примере не принимает никаких входных данных. Вам нужно указать выходной набор данных для действия потоковой передачи HDInsight. Это просто фиктивный набор данных, необходимый для соблюдения расписания конвейера.

	{
	    "name": "StreamingOutputDataset",
	    "properties": {
	        "published": false,
	        "type": "AzureBlob",
	        "linkedServiceName": "StorageLinkedService",
	        "typeProperties": {
	            "folderPath": "adftutorial/streamingdata/",
	            "format": {
	                "type": "TextFormat",
	                "columnDelimiter": ","
	            },
	        },
	        "availability": {
	            "frequency": "Day",
	            "interval": 1
	        }
	    }
	}

### Конвейер

В этом примере конвейер имеет только одно действие с типом **HDInsightStreaming**.

Кластер HDInsight автоматически заполняется примерами программ (wc.exe и cat.exe) и данных (davinci.txt). По умолчанию именем контейнера, используемым кластером HDInsight, является имя самого кластера. Например, если кластеру задано имя myhdicluster, именем связанного контейнера больших двоичных объектов будет myhdicluster.

	{
	    "name": "HadoopStreamingPipeline",
	    "properties": {
	        "description": "Hadoop Streaming Demo",
	        "activities": [
	            {
	                "type": "HDInsightStreaming",
	                "typeProperties": {
	                    "mapper": "cat.exe",
	                    "reducer": "wc.exe",
	                    "input": "wasb://<blobcontainer>@spestore.blob.core.windows.net/example/data/gutenberg/davinci.txt",
	                    "output": "wasb://<blobcontainer>@spestore.blob.core.windows.net/example/data/StreamingOutput/wc.txt",
	                    "filePaths": [
	                        "<blobcontainer>/example/apps/wc.exe",
	                        "<blobcontainer>/example/apps/cat.exe"
	                    ],
	                    "fileLinkedService": "StorageLinkedService"
	                },
	                "outputs": [
	                    {
	                        "name": "StreamingOutputDataset"
	                    }
	                ],
	                "policy": {
	                    "timeout": "01:00:00",
	                    "concurrency": 1,
	                    "executionPriorityOrder": "NewestFirst",
	                    "retry": 1
	                },
	                "scheduler": {
	                    "frequency": "Day",
	                    "interval": 1
	                },
	                "name": "RunHadoopStreamingJob",
	                "description": "Run a Hadoop streaming job",
	                "linkedServiceName": "HDInsightLinkedService"
	            }
	        ],
	        "start": "2014-01-04T00:00:00Z",
	        "end": "2014-01-05T00:00:00Z"
	    }
	}

## См. также
- [Действие Hive](data-factory-hive-activity.md)
- [Действие Pig](data-factory-pig-activity.md)
- [Действие MapReduce](data-factory-map-reduce.md)
- [Вызов программ Spark](data-factory-spark.md)
- [Вызов сценариев R](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample)

<!---HONumber=AcomDC_0921_2016-->
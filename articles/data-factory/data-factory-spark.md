<properties 
	pageTitle="Вызов программ Spark из фабрики данных Azure" 
	description="Узнайте, как вызывать программы Spark из фабрики данных Azure с помощью действия MapReduce." 
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
	ms.date="04/05/2016" 
	ms.author="spelluru"/>

# Вызов программ Spark из фабрики данных
## Введение
Для запуска программ Spark в кластере HDInsight Spark можно использовать действие MapReduce в конвейере фабрики данных. Перед чтением этой статьи просмотрите статью [Действие MapReduce](data-factory-map-reduce.md), в которой содержатся подробные сведения об использовании этого действия.

## Пример Spark в GitHub
В [примере "Spark — фабрика данных" в GitHub](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/Spark) показано, как использовать действие MapReduce для запуска программы Spark. Программа Spark просто копирует данные из одного контейнера BLOB-объектов Azure в другой.

## Сущности фабрики данных
В папке **Spark-ADF/src/ADFJsons** содержатся файлы для сущностей фабрики данных (связанные службы, наборы данных, конвейер).

В этом примере имеется две **связанные службы**: служба хранилища Azure и Azure HDInsight. Имя службы хранилища Azure и значения ключа необходимо указать в файле **StorageLinkedService.json**. Также необходимо указать значения параметров clusterUri (URI кластера), userName (имя пользователя) и password (пароль) в файле **HDInsightLinkedService.json**.

В этом примере существует два **набора данных**: **input.json** и **output.json**. Эти файлы расположены в папке **Datasets**. Они представляют входные и выходные наборы данных для действия MapReduce.

В примере в каталоге **ADFJsons/Pipeline** используется один **конвейер**. Это наиболее важная сущность, с которой нужно ознакомиться для того, чтобы понять, как вызывать программу Spark с помощью действия MapReduce.

	{
	    "name": "SparkSubmit",
	    "properties": {
	        "description": "Submit a spark job",
	        "activities": [
	            {
	                "type": "HDInsightMapReduce",
	                "typeProperties": {
	                    "className": "com.adf.spark.SparkJob",
	                    "jarFilePath": "libs/spark-adf-job-bin.jar",
	                    "jarLinkedService": "StorageLinkedService",
	                    "arguments": [
	                        "--jarFile",
	                        "libs/sparkdemoapp_2.10-1.0.jar",
	                        "--jars",
	                        "/usr/hdp/current/hadoop-client/hadoop-azure-2.7.1.2.3.3.0-3039.jar,/usr/hdp/current/hadoop-client/lib/azure-storage-2.2.0.jar",
	                        "--mainClass",
	                        "com.adf.spark.demo.Demo",
	                        "--master",
	                        "yarn-cluster",
	                        "--driverMemory",
	                        "2g",
	                        "--driverExtraClasspath",
	                        "/usr/lib/hdinsight-logging/*",
	                        "--executorCores",
	                        "1",
	                        "--executorMemory",
	                        "4g",
	                        "--sparkHome",
	                        "/usr/hdp/current/spark-client",
	                        "--connectionString",
	                        "DefaultEndpointsProtocol=https;AccountName=<YOUR_ACCOUNT>;AccountKey=<YOUR_KEY>",
	                        "input=wasb://input@<YOUR_ACCOUNT>.blob.core.windows.net/data",
	                        "output=wasb://output@<YOUR_ACCOUNT>.blob.core.windows.net/results"
	                    ]
	                },
	                "inputs": [
	                    {
	                        "name": "input"
	                    }
	                ],
	                "outputs": [
	                    {
	                        "name": "output"
	                    }
	                ],
	                "policy": {
	                    "executionPriorityOrder": "OldestFirst",
	                    "timeout": "01:00:00",
	                    "concurrency": 1,
	                    "retry": 1
	                },
	                "scheduler": {
	                    "frequency": "Day",
	                    "interval": 1
	                },
	                "name": "Spark Launcher",
	                "description": "Submits a Spark Job",
	                "linkedServiceName": "HDInsightLinkedService"
	            }
	        ],
	        "start": "2015-11-16T00:00:01Z",
	        "end": "2015-11-16T23:59:00Z",
	        "isPaused": false,
	        "pipelineMode": "Scheduled"
	    }
	}

Как вы видите, действие MapReduce настроено для вызова **spark-adf-job-bin.jar** в контейнере **libs** в службе хранилища Azure (указанной в файле StorageLinkedService.json). Исходный код для этой программы находится в папке Spark-ADF/src/main/java/com/adf/spark. Он вызывает spark-submit и запускает задания Spark.

Эта программа MapReduce (spark-adf-job-bin.jar), выполняемая в кластере HDInsight Spark, вызывает программу Spark **sparkdemoapp\_2.10 1.0.jar** и передает аргументы, полученные через действие MapReduce (показанное в приведенном выше коде JSON), в программу Spark. Программа **sparkdemoapp\_2.10 1.0.jar** содержит исходный код Scala, копирующий данные из одного контейнера BLOB-объектов Azure в другой. Этот jar-файл с демонстрационным приложением можно заменить любым другим jar-файлом, содержащим любое задание, которое вы пытаетесь запустить с помощью Spark.

Подведем итоги. **Действие MapReduce** вызывает программу MapReduce **spark-adf-job-bin.jar**, которая вызывает программу Spark **sparkdemoapp\_2.10-1.0.jar**. Чтобы запустить собственную программу Spark, замените sparkdemoapp\_2.10-1.0.jar на свою программу.

> [AZURE.NOTE] При таком вызове программ Spark с помощью действия MapReduce необходимо использовать собственный кластер Spark HDInsight. Использование кластера HDInsight по требованию не поддерживается.


## См. также
- [Действие Hive](data-factory-hive-activity.md)
- [Действие Pig](data-factory-pig-activity.md)
- [Действие MapReduce](data-factory-map-reduce.md)
- [Потоковая активность Hadoop](data-factory-hadoop-streaming-activity.md)
- [Вызов сценариев R](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample)

<!---HONumber=AcomDC_0420_2016-->
<properties 
	pageTitle="Действие Hive" 
	description="Узнайте, как с помощью действия Hive в фабрике данных Azure выполнять запросы Hive к кластеру HDInsight по требованию или собственному кластеру HDInsight." 
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

# Действие Hive

Действие Hive HDInsight в [конвейере](data-factory-create-pipelines.md) фабрики данных выполняет запросы к Hive [самостоятельно](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) или [по требованию](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) в кластере HDInsight под управлением Windows или Linux. Данная статья основана на материалах статьи о [действиях преобразования данных](data-factory-data-transformation-activities.md), в которой приведен общий обзор преобразования данных и список поддерживаемых действий преобразования.

## Синтаксис

	{
		"name": "Hive Activity",
	    "description": "description",
	    "type": "HDInsightHive",
	    "inputs": [
	      {
	        "name": "input tables"
	      }
	    ],
	    "outputs": [
	      {
	        "name": "output tables"
	      }
	    ],
	    "linkedServiceName": "MyHDInsightLinkedService",
	    "typeProperties": {
	      "script": "Hive script",
	      "scriptPath": "<pathtotheHivescriptfileinAzureblobstorage>",
	      "defines": {
	        "param1": "param1Value"
	      }
	    },
       "scheduler": {
          "frequency": "Day",
          "interval": 1
        }
	}
	
## Сведения о синтаксисе

Свойство | Описание | Обязательно
-------- | ----------- | --------
name | Имя действия. | Да
description | Текст, описывающий, для чего используется действие | Нет
type | HDInsightHive. | Да
inputs | Входные данные, используемые действием Hive | Нет
outputs | Выходные данные, создаваемые действием Hive | Да 
linkedServiceName (имя связанной службы) | Ссылка на кластер HDInsight, зарегистрированный в качестве связанной службы в фабрике данных. | Да 
script | Указывается встроенный сценарий Hive. | Нет
script path | Путь к файлу сценария Hive в хранилище BLOB-объектов Azure. Можно использовать либо свойство script, либо свойство scriptPath, но не оба сразу. В имени файла учитывается регистр знаков. | Нет 
defines | Параметры в виде пары "ключ-значение", ссылки на которые указываются в сценарии Hive с помощью элемента hiveconf. | Нет

## Пример

Рассмотрим пример с аналитикой игровых журналов. Предположим, вы хотите определить время, которое пользователи проводят за игрой, выпущенной вашей компанией.

Ниже приведен журнал игры с разделителями-запятыми (`,`), содержащий следующие поля: ProfileID, SessionStart, Duration, SrcIPAddress и GameType.

	1809,2014-05-04 12:04:25.3470000,14,221.117.223.75,CaptureFlag
	1703,2014-05-04 06:05:06.0090000,16,12.49.178.247,KingHill
	1703,2014-05-04 10:21:57.3290000,10,199.118.18.179,CaptureFlag
	1809,2014-05-04 05:24:22.2100000,23,192.84.66.141,KingHill
	.....

**Сценарий Hive** для обработки этих данных выглядит так:

	DROP TABLE IF EXISTS HiveSampleIn; 
	CREATE EXTERNAL TABLE HiveSampleIn 
	(
		ProfileID		string, 
	    SessionStart 	string, 
	    Duration 		int, 
	    SrcIPAddress 	string, 
	    GameType 		string
	) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION 'wasb://adfwalkthrough@<storageaccount>.blob.core.windows.net/samplein/'; 
	
	DROP TABLE IF EXISTS HiveSampleOut; 
	CREATE EXTERNAL TABLE HiveSampleOut 
	(	
		ProfileID 	string, 
	    Duration 	int
	) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION 'wasb://adfwalkthrough@<storageaccount>.blob.core.windows.net/sampleout/';
	
	INSERT OVERWRITE TABLE HiveSampleOut
	Select 
		ProfileID,
		SUM(Duration)
	FROM HiveSampleIn Group by ProfileID

Чтобы выполнить его в конвейере фабрики данных, необходимо сделать следующее.

1. Создайте связанную службу для регистрации [собственного вычислительного кластера HDInsight](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) или настройте [вычислительный кластер HDInsight по требованию](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service). Назовем эту связанную службу HDInsightLinkedService.
2. Создайте [связанную службу](data-factory-azure-blob-connector.md) для настройки подключения к хранилищу BLOB-объектов Azure, в котором хранятся данные. Назовем эту связанную службу StorageLinkedService.
3. Создайте [наборы данных](data-factory-create-datasets.md), указывающие на входные и выходные данные. Назовем входной набор данных HiveSampleIn, а выходной — HiveSampleOut.
4. Скопируйте запрос Hive в файл и сохраните его в хранилище BLOB-объектов Azure, настроенном на шаге 2. Если хранилище, в котором размещаются данные, отличается от хранилища, в котором размещаются этот файл запроса, создайте отдельную связанную службу хранилища Azure и добавьте ссылку на нее в действие. Используйте свойство **scriptPath**, чтобы указать путь к файлу запроса Hive, и **scriptLinkedService**, чтобы определить службу хранилища Azure, содержащую файл сценария.

	> [AZURE.NOTE] Также можно добавить сценарий Hive непосредственно в определение действия, используя свойство **script**. Мы не рекомендуем это делать, так как вам потребуется экранировать все специальные знаки в сценарии в документе JSON, что может вызвать проблемы при отладке. Мы рекомендуем следовать инструкциям, описанным в шаге 4.
5.	Создайте конвейер с действием HDInsightHive. Это действие обрабатывает и преобразует данные.

		{
		  "name": "HiveActivitySamplePipeline",
		  "properties": {
		    "activities": [
		      {
		        "name": "HiveActivitySample",
		        "type": "HDInsightHive",
		        "inputs": [
		          {
		            "name": "HiveSampleIn"
		          }
		        ],
		        "outputs": [
		          {
		            "name": "HiveSampleOut"
		          }
		        ],
		        "linkedServiceName": "HDInsightLinkedService",
		        "typeproperties": {
		          "scriptPath": "adfwalkthrough\\scripts\\samplehive.hql",
		          "scriptLinkedService": "StorageLinkedService"
		        },
       			"scheduler": {
          			"frequency": "Hour",
          			"interval": 1
        		}
		      }
		    ]
		  }
		}

6.	Разверните конвейер. Дополнительные сведения см. в разделе [Создание конвейеров](data-factory-create-pipelines.md).
7.	Отслеживайте состояние конвейера, используя функции мониторинга и управления фабрикой данных. Подробные сведения см. в статье [Мониторинг конвейеров фабрики данных и управление ими](data-factory-monitor-manage-pipelines.md).


## Настройка параметров для сценария Hive  
В этом примере журналы игры ежедневно добавляются в хранилище BLOB-объектов Azure и хранятся в папках, разбитых по дате и времени. Вам необходимо параметризовать сценарий Hive, чтобы адрес входной папки передавался во время выполнения динамически, а выходной набор данных сегментировался по дате и времени.

Чтобы использовать параметризованный сценарий Hive, выполните следующие действия.

- Задайте параметры в разделе **defines**.

		{
			"name": "HiveActivitySamplePipeline",
		  	"properties": {
		    "activities": [
		     	{
		        	"name": "HiveActivitySample",
		        	"type": "HDInsightHive",
			        "inputs": [
			          	{
				            "name": "HiveSampleIn"
				          }
		        	],
		        	"outputs": [
		          		{
				            "name": "HiveSampleOut"
				        }
		        	],
		        	"linkedServiceName": "HDInsightLinkedService",
		        	"typeproperties": {
		          		"scriptPath": "adfwalkthrough\\scripts\\samplehive.hql",
		          		"scriptLinkedService": "StorageLinkedService",
		          		"defines": {
		            		"Input": "$$Text.Format('wasb://adfwalkthrough@<storageaccountname>.blob.core.windows.net/samplein/yearno={0:yyyy}/monthno={0:%M}/dayno={0:%d}/', SliceStart)",
		            		"Output": "$$Text.Format('wasb://adfwalkthrough@<storageaccountname>.blob.core.windows.net/sampleout/yearno={0:yyyy}/monthno={0:%M}/dayno={0:%d}/', SliceStart)"
		          		},
       					"scheduler": {
          					"frequency": "Hour",
          					"interval": 1
        				}
		        	}
		      	}
		    ]
		  }
		}

- Добавьте ссылку на параметр в сценарий Hive с помощью элемента **${hiveconf:parameterName}**.

		DROP TABLE IF EXISTS HiveSampleIn; 
		CREATE EXTERNAL TABLE HiveSampleIn 
		(
			ProfileID 	string, 
		    SessionStart 	string, 
		    Duration 	int, 
		    SrcIPAddress 	string, 
		    GameType 	string
		) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:Input}'; 
		
		DROP TABLE IF EXISTS HiveSampleOut; 
		CREATE EXTERNAL TABLE HiveSampleOut 
		(
		    ProfileID 	string, 
		    Duration 	int
		) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:Output}';
		
		INSERT OVERWRITE TABLE HiveSampleOut
		Select 
			ProfileID,
			SUM(Duration)
		FROM HiveSampleIn Group by ProfileID


## См. также
- [Действие Pig](data-factory-pig-activity.md)
- [Действие MapReduce](data-factory-map-reduce.md)
- [Потоковая активность Hadoop](data-factory-hadoop-streaming-activity.md)
- [Вызов программ Spark](data-factory-spark.md)
- [Вызов сценариев R](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample)

<!---HONumber=AcomDC_0921_2016-->
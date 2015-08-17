<properties 
	pageTitle="Действие Pig" 
	description="Узнайте, как с помощью действия Pig в фабрике данных Azure выполнять запросы Pig к собственному кластеру HDInsight или к кластеру HDInsight по требованию." 
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
	ms.date="07/26/2015" 
	ms.author="spelluru"/>

# Действие Pig

Действие Pig HDInsight в [конвейере](data-factory-create-pipelines.md) фабрики данных выполняет запросы Pig в [вашем](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) кластере HDInsight или в кластере HDInsight [по требованию](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service). Эта статья основана на материалах статьи [Действия преобразования данных](data-factory-data-transformation-activities.md), в которой приведен общий обзор преобразования данных и поддерживаемые действия преобразования.

## Синтаксис

	{
		"name": "HiveActivitySamplePipeline",
	  	"properties": {
	    "activities": [
	    	{
	        	"name": "Pig Activity",
	        	"description": "description",
	        	"type": "HDInsightPig",
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
	          		"script": "Pig script",
	          		"scriptPath": "<pathtothePigscriptfileinAzureblobstorage>",
	          		"defines": {
	            		"param1": "param1Value"
	          		}
	        	},
       			"scheduler": {
          			"frequency": "Day",
          			"interval": 1
        		}
	      	}
	    ]
	  }
	}

## Сведения о синтаксисе

Свойство | Описание | Обязательно
-------- | ----------- | --------
name | Имя действия | Да
description | Текст, описывающий, для чего используется действие | Нет
type | HDinsightPig | Да
inputs | Входные данные, используемые действием Pig. | Нет
outputs | Выходные данные, используемые действием Pig. | Да
linkedServiceName (имя связанной службы) | Ссылка на кластер HDInsight, указанный в качестве связанной службы в фабрике данных. | Да
script | Указывается встроенный сценарий Pig. | Нет
script path | Путь к файлу сценария Pig в хранилище BLOB-объектов Azure. Можно использовать либо свойство script, либо свойство scriptPath, но не оба сразу. | Нет
defines | Параметры в виде пары «ключ — значение», ссылки на которые указываются в сценарии Pig. | Нет

## Пример

Рассмотрим пример с аналитикой игровых журналов. Предположим, вы хотите определить время, которое пользователи проводят за игрой, выпущенной вашей компанией.
 
Ниже приведен журнал игры в формате CSV, содержащий следующие поля: ProfileID, SessionStart, Duration, SrcIPAddress и GameType.

	1809,2014-05-04 12:04:25.3470000,14,221.117.223.75,CaptureFlag
	1703,2014-05-04 06:05:06.0090000,16,12.49.178.247,KingHill
	1703,2014-05-04 10:21:57.3290000,10,199.118.18.179,CaptureFlag
	1809,2014-05-04 05:24:22.2100000,23,192.84.66.141,KingHill
	.....

**Сценарий Pig** для обработки этих данных выглядит так:

	PigSampleIn = LOAD 'wasb://adfwalkthrough@anandsub14.blob.core.windows.net/samplein/' USING PigStorage(',') AS (ProfileID:chararray, SessionStart:chararray, Duration:int, SrcIPAddress:chararray, GameType:chararray);
	
	GroupProfile = Group PigSampleIn all;
	
	PigSampleOut = Foreach GroupProfile Generate PigSampleIn.ProfileID, SUM(PigSampleIn.Duration);
	
	Store PigSampleOut into 'wasb://adfwalkthrough@anandsub14.blob.core.windows.net/sampleoutpig/' USING PigStorage (',');

Чтобы выполнить его в конвейере фабрики данных, необходимо сделать следующее.

1. Создайте связанную службу для регистрации [собственного вычислительного кластера HDInsight](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) или настройте [вычислительный кластер HDInsight по требованию](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service). Назовем эту связанную службу HDInsightLinkedService.
2.	Создайте [связанную службу](data-factory-azure-storage-connector.md) для настройки подключения к хранилищу BLOB-объектов Azure, в котором хранятся данные. Назовем эту службу StorageLinkedService.
3.	Создайте [наборы данных](data-factory-create-datasets.md), указывающие на входные и выходные данные. Назовем входной набор данных PigSampleIn, а выходной — PigSampleOut.
4.	Скопируйте запрос Pig в файл и сохраните его в хранилище BLOB-объектов Azure, заданном на втором шаге. Если связанная служба, размещающая данные, отличается от службы, размещающей файл запроса, создайте отдельную службу хранилища Azure и добавьте ссылку на нее в настройку действия. Используйте свойство**scriptPath**, чтобы указать путь к файлу сценария Pig, и **scriptLinkedService**, чтобы задать хранилище Azure, в котором размещен файл сценария.
	
	> [AZURE.NOTE]Вы можете добавить сценарий Pig непосредственно в определение действия, используя свойство **script**. Однако мы не рекомендуем это делать, так как в этом случае потребуется экранировать все специальные символы в сценарии в документе JSON, что может вызвать проблемы при отладке. Мы рекомендуем следовать инструкциям, описанным в шаге 4.
5. Создайте следующий конвейер, в котором данные обрабатываются действием HDInsightPig.

		{
		  "name": "PigActivitySamplePipeline",
		  "properties": {
		    "activities": [
		      {
		        "name": "PigActivitySample",
		        "type": "HDInsightPig",
		        "inputs": [
		          {
		            "name": "PigSampleIn"
		          }
		        ],
		        "outputs": [
		          {
		            "name": "PigSampleOut"
		          }
		        ],
		        "linkedServiceName": "HDInsightLinkedService",
		        "typeproperties": {
		          "scriptPath": "adfwalkthrough\\scripts\\enrichlogs.pig",
		          "scriptLinkedService": "StorageLinkedService"
		        },
       			"scheduler": {
          			"frequency": "Day",
          			"interval": 1
        		}
		      }
		    ]
		  }
		} 
6. Разверните конвейер. Дополнительные сведения см. в разделе [Создание конвейеров](data-factory-create-pipelines.md). 
7. Отслеживайте состояние конвейера, используя функции мониторинга и управления фабрикой данных. Подробные сведения см. в статье [Мониторинг конвейеров фабрики данных и управление ими](data-factory-monitor-manage-pipelines.md).

## Указание параметров для сценариев Pig с помощью элемента defines

Рассмотрим пример, в котором журналы игры ежедневно добавляются в хранилище BLOB-объектов Azure и хранятся в папках, разбитых по дате и времени. Вам необходимо параметризовать сценарий Pig так, чтобы адрес входной папки передавался во время выполнения динамически, а выходной набор данных также сегментировался по дате и времени.
 
Чтобы использовать параметризованные сценарии Pig, сделайте вот что:

- Задайте параметры в разделе **defines**.

		{
			"name": "PigActivitySamplePipeline",
		  	"properties": {
		    "activities": [
		    	{
		        	"name": "PigActivitySample",
		        	"type": "HDInsightPig",
		        	"inputs": [
		          		{
		            		"name": "PigSampleIn"
		          		}
		        	],
		        	"outputs": [
		          		{
		            		"name": "PigSampleOut"
		          		}
		        	],
		        	"linkedServiceName": "HDInsightLinkedService",
		        	"typeproperties": {
		          		"scriptPath": "adfwalkthrough\\scripts\\samplepig.hql",
		          		"scriptLinkedService": "StorageLinkedService",
		          		"defines": {
		            		"Input": "$Text.Format('wasb: //adfwalkthrough@<storageaccountname>.blob.core.windows.net/samplein/yearno={0: yyyy}/monthno={0: %M}/dayno={0: %d}/',SliceStart)",
				            "Output": "Text.Format('wasb://adfwalkthrough@<storageaccountname>.blob.core.windows.net/sampleout/yearno={0:yyyy}/monthno={0:%M}/dayno={0:%d}/', SliceStart)"
		          		}
		        	},
       				"scheduler": {
          				"frequency": "Day",
	          			"interval": 1
    	    		}
		      	}
		    ]
		  }
		}  
	  
- В сценарии Pig сошлитесь на параметры с помощью **$parameterName**, как показано в следующем примере.

		PigSampleIn = LOAD '$Input' USING PigStorage(',') AS (ProfileID:chararray, SessionStart:chararray, Duration:int, SrcIPAddress:chararray, GameType:chararray);	
		GroupProfile = Group PigSampleIn all;		
		PigSampleOut = Foreach GroupProfile Generate PigSampleIn.ProfileID, SUM(PigSampleIn.Duration);		
		Store PigSampleOut into '$Output' USING PigStorage (','); 


 

<!---HONumber=August15_HO6-->
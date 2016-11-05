---
title: Действие Pig
description: Узнайте, как с помощью действия Pig в фабрике данных Azure выполнять запросы Pig к собственному кластеру HDInsight или к кластеру HDInsight по требованию.
services: data-factory
documentationcenter: ''
author: spelluru
manager: jhubbard
editor: monicar

ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/31/2016
ms.author: spelluru

---
# Действие Pig
Действие Pig HDInsight в [конвейере](data-factory-create-pipelines.md) фабрики данных выполняет запросы Pig Hive [самостоятельно](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) или [по требованию](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) в кластере HDInsight под управлением Windows или Linux. Данная статья основана на материалах статьи о [действиях преобразования данных](data-factory-data-transformation-activities.md), в которой приведен общий обзор преобразования данных и список поддерживаемых действий преобразования.

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
| Свойство | Описание | Обязательно |
| --- | --- | --- |
| name |Имя действия. |Да |
| description |Текст, описывающий, для чего используется действие |Нет |
| type |HDinsightPig |Да |
| inputs |Входные данные, используемые действием Pig. |Нет |
| outputs |Выходные данные, создаваемые действием Pig. |Да |
| linkedServiceName (имя связанной службы) |Ссылка на кластер HDInsight, зарегистрированный в качестве связанной службы в фабрике данных. |Да |
| script |Указывается встроенный сценарий Pig. |Нет |
| script path |Путь к файлу сценария Pig в хранилище BLOB-объектов Azure. Можно использовать либо свойство script, либо свойство scriptPath, но не оба сразу. В имени файла учитывается регистр знаков. |Нет |
| defines |Параметры в виде пары "ключ — значение", ссылки на которые указываются в сценарии Pig. |Нет |

## Пример
Рассмотрим пример с аналитикой игровых журналов. Предположим, вы хотите определить время, которое игроки проводят за игрой, выпущенной вашей компанией.

Ниже приведен журнал игры в формате CSV-файла. Он содержит следующие поля: ProfileID, SessionStart, Duration, SrcIPAddress и GameType.

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

Чтобы выполнить его в конвейере фабрики данных, сделайте следующее.

1. Создайте связанную службу для регистрации [собственного вычислительного кластера HDInsight](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) или настройте [вычислительный кластер HDInsight по требованию](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service). Назовем эту связанную службу **HDInsightLinkedService**.
2. Создайте [связанную службу](data-factory-azure-blob-connector.md) для настройки подключения к хранилищу BLOB-объектов Azure, в котором хранятся данные. Назовем эту связанную службу **StorageLinkedService**.
3. Создайте [наборы данных](data-factory-create-datasets.md), указывающие на входные и выходные данные. Назовем входной набор данных **PigSampleIn**, а выходной — **PigSampleOut**.
4. Скопируйте запрос Pig в файл, настроенный хранилищем BLOB-объектов Azure на шаге 2. Если хранилище BLOB-объектов Azure, размещающее данные, отличается от хранилища, размещающего файл запроса, то создайте отдельную связанную службу хранилища Azure. Добавьте ссылку на эту связанную службу в конфигурации действия. Используйте свойство **scriptPath** для указания пути к файлу сценария Pig и **scriptLinkedService**.
   
   > [!NOTE]
   > Также можно добавить сценарий Pig непосредственно в определение действия, используя свойство **script**. Однако мы не рекомендуем это делать, так как в этом случае потребуется экранировать все специальные знаки в сценарии, что может вызвать проблемы при отладке. Мы рекомендуем следовать инструкциям, описанным в шаге 4.
   > 
   > 
5. Создайте конвейер с действием HDInsightPig. Это действие обрабатывает входные данные, запуская сценарий Pig в кластере HDInsight.
   
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

## Указание параметров для сценария Pig
Рассмотрим следующий пример: журналы игры ежедневно добавляются в хранилище BLOB-объектов Azure и хранятся в папках, разбитых по дате и времени. Вам необходимо параметризовать сценарий Pig так, чтобы адрес входной папки передавался во время выполнения динамически, а выходной набор данных также сегментировался по дате и времени.

Чтобы использовать параметризованный сценарии Pig, выполните описанные ниже действия.

* Задайте параметры в разделе **defines**.
  
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
                            "Input": "$$Text.Format('wasb: //adfwalkthrough@<storageaccountname>.blob.core.windows.net/samplein/yearno={0: yyyy}/monthno={0: %M}/dayno={0: %d}/',SliceStart)",
                            "Output": "$$Text.Format('wasb://adfwalkthrough@<storageaccountname>.blob.core.windows.net/sampleout/yearno={0:yyyy}/monthno={0:%M}/dayno={0:%d}/', SliceStart)"
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
* В сценарии Pig сошлитесь на параметры с помощью **$parameterName**, как показано в следующем примере.
  
        PigSampleIn = LOAD '$Input' USING PigStorage(',') AS (ProfileID:chararray, SessionStart:chararray, Duration:int, SrcIPAddress:chararray, GameType:chararray);    
        GroupProfile = Group PigSampleIn all;        
        PigSampleOut = Foreach GroupProfile Generate PigSampleIn.ProfileID, SUM(PigSampleIn.Duration);        
        Store PigSampleOut into '$Output' USING PigStorage (','); 

## См. также
* [Действие Hive](data-factory-hive-activity.md)
* [Действие MapReduce](data-factory-map-reduce.md)
* [Потоковая активность Hadoop](data-factory-hadoop-streaming-activity.md)
* [Вызов программ Spark](data-factory-spark.md)
* [Вызов сценариев R](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample)

<!---HONumber=AcomDC_0831_2016-->
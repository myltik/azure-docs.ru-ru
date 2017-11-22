---
title: "Чтение или запись секционированных данных в фабрике данных Azure | Документация Майкрософт"
description: "Узнайте больше о том, как выполнять чтение и запись секционированных данных в фабрике данных Azure версии 2."
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: 
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/09/2017
ms.author: shlo
ms.openlocfilehash: 2066847feb3dcdf36ead8901a679d8cae7a6acde
ms.sourcegitcommit: e38120a5575ed35ebe7dccd4daf8d5673534626c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/13/2017
---
# <a name="how-to-read-or-write-partitioned-data-in-azure-data-factory-version-2"></a>Чтение или запись секционированных данных в фабрике данных Azure версии 2
В версии 1 фабрика данных Azure поддерживала чтение или запись секционированных данных с использованием системных переменных SliceStart/SliceEnd/WindowStart/WindowEnd. В версии 2 это достигается с помощью параметра конвейера и времени начала или запланированного времени запуска триггера в качестве значения параметра. 

## <a name="use-a-pipeline-parameter"></a>Использование параметра конвейера 
В версии 1 можно было использовать свойство partitionedBy и системную переменную SliceStart, как показано в следующем примере: 

```json
"folderPath": "adfcustomerprofilingsample/logs/marketingcampaigneffectiveness/yearno={Year}/monthno={Month}/dayno={Day}/",
"partitionedBy": [
    { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
    { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "%M" } },
    { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "%d" } }
],
```

Дополнительные сведения о свойстве partitonedBy см. в статье о [соединителе больших двоичных объектов Azure версии 1](v1/data-factory-azure-blob-connector.md#dataset-properties). 

В версии 2 это можно достичь, выполнив следующие действия: 

1. Определите **параметр конвейера** строкового типа. В следующем примере именем параметра конвейера является **scheduledRunTime**. 
2. Задайте для **folderPath** в определении набора данных значение параметра конвейера. 
3. Передайте запрограммированное значение параметра перед запуском конвейера. Можно также передавать данные времени начала или запланированного времени запуска триггера динамически во время выполнения. 

```json
"folderPath": {
      "value": "@concat(pipeline().parameters.blobContainer, '/logs/marketingcampaigneffectiveness/yearno=', formatDateTime(pipeline().parameters.scheduledRunTime, 'yyyy'), '/monthno=', formatDateTime(pipeline().parameters.scheduledRunTime, '%M'), '/dayno=', formatDateTime(pipeline().parameters.scheduledRunTime, '%d'), '/')",
      "type": "Expression"
},
```

## <a name="pass-in-value-from-a-trigger"></a>Передача значения из триггера
В следующем определении триггера запланированное время триггера передается в виде значения параметра конвейера **scheduledRunTime**: 

```json
{
    "name": "MyTrigger",
    "properties": {
       ...
        },
        "pipeline": {
            "pipelineReference": {
                "type": "PipelineReference",
                "referenceName": "MyPipeline"
            },
            "parameters": {
                "scheduledRunTime": "@trigger().scheduledTime"
            }
        }
    }
}
```

## <a name="example"></a>Пример

Ниже приведен пример определения набора данных (в котором используется параметр с именем `date`):

```json
{
  "type": "AzureBlob",
  "typeProperties": {
    "folderPath": {
      "value": "@concat(pipeline().parameters.blobContainer, '/logs/marketingcampaigneffectiveness/yearno=', formatDateTime(pipeline().parameters.scheduledRunTime, 'yyyy'), '/monthno=', formatDateTime(pipeline().parameters.scheduledRunTime, '%M'), '/dayno=', formatDateTime(pipeline().parameters.scheduledRunTime, '%d'), '/')",
      "type": "Expression"
    },
    "format": {
      "type": "TextFormat",
      "columnDelimiter": ","
    }
  },
  "structure": [
    { "name": "ProfileID", "type": "String" },
    { "name": "SessionStart", "type": "String" },
    { "name": "Duration", "type": "Int32" },
    { "name": "State", "type": "String" },
    { "name": "SrcIPAddress", "type": "String" },
    { "name": "GameType", "type": "String" },
    { "name": "Multiplayer", "type": "String" },
    { "name": "EndRank", "type": "String" },
    { "name": "WeaponsUsed", "type": "Int32" },
    { "name": "UsersInteractedWith", "type": "String" },
    { "name": "Impressions", "type": "String" }
  ],
  "linkedServiceName": {
    "referenceName": "churnStorageLinkedService",
    "type": "LinkedServiceReference"
  }
}
```

Определение конвейера: 

```json
{
    "properties": {
        "activities": [{
            "type": "HDInsightHive",
            "typeProperties": {
                "scriptPath": {
                    "value": "@concat(pipeline().parameters.blobContainer, '/scripts/', pipeline().parameters.partitionHiveScriptFile)",
                    "type": "Expression"
                },
                "scriptLinkedService": {
                    "referenceName": "churnStorageLinkedService",
                    "type": "LinkedServiceReference"
                },
                "defines": {
                    "RAWINPUT": {
                        "value": "@concat('wasb://', pipeline().parameters.blobContainer, '@', pipeline().parameters.blobStorageAccount, '.blob.core.windows.net/logs/', pipeline().parameters.inputRawLogsFolder, '/')",
                        "type": "Expression"
                    },
                    "PARTITIONEDOUTPUT": {
                        "value": "@concat('wasb://', pipeline().parameters.blobContainer, '@', pipeline().parameters.blobStorageAccount, '.blob.core.windows.net/logs/partitionedgameevents/')",
                        "type": "Expression"
                    },
                    "Year": {
                        "value": "@formatDateTime(pipeline().parameters.scheduledRunTime, 'yyyy')",
                        "type": "Expression"
                    },
                    "Month": {
                        "value": "@formatDateTime(pipeline().parameters.scheduledRunTime, '%M')",
                        "type": "Expression"
                    },
                    "Day": {
                        "value": "@formatDateTime(pipeline().parameters.scheduledRunTime, '%d')",
                        "type": "Expression"
                    }
                }
            },
            "linkedServiceName": {
                "referenceName": "HdiLinkedService",
                "type": "LinkedServiceReference"
            },
            "name": "HivePartitionGameLogs"
        }],
        "parameters": {
            "scheduledRunTime": {
                "type": "String"
            },
            "blobStorageAccount": {
                "type": "String"
            },
            "blobContainer": {
                "type": "String"
            },
            "inputRawLogsFolder": {
                "type": "String"
            },
            "partitionHiveScriptFile": {
                "type": "String"
            }
        }
    }
}
```

## <a name="next-steps"></a>Дальнейшие действия
См. пошаговое руководство по [созданию фабрики данных с конвейером](quickstart-create-data-factory-powershell.md). 
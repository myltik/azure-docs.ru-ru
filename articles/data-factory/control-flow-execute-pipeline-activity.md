---
title: "Действие выполнения конвейера в фабрике данных Azure | Документация Майкрософт"
description: "Узнайте, как использовать действие выполнения конвейера для вызова одного конвейера фабрики данных из другого."
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
ms.date: 09/05/2017
ms.author: shlo
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 81d631291c5b375cf7425b0089caf6a97ec3b598
ms.contentlocale: ru-ru
ms.lasthandoff: 09/25/2017

---
# <a name="execute-pipeline-activity-in-azure-data-factory"></a>Действие выполнения конвейера в фабрике данных Azure
Действие выполнения конвейера позволяет конвейеру фабрики данных вызвать другой конвейер.

> [!NOTE]
> Эта статья относится к версии 2 фабрики данных, которая сейчас доступна в предварительной версии. Если вы используете общедоступную версию 1 службы фабрики данных, см. статью [Introduction to Azure Data Factory](v1/data-factory-introduction.md) (Введение в фабрику данных Azure).

## <a name="syntax"></a>Синтаксис

```json
{
    "name": "MyPipeline",
    "properties": {
        "activities": [
            {
                "name": "ExecutePipelineActivity",
                "type": "ExecutePipeline",
                "typeProperties": {
                    "parameters": {                        
                        "mySourceDatasetFolderPath": {
                            "value": "@pipeline().parameters.mySourceDatasetFolderPath",
                            "type": "Expression"
                        }
                    },
                    "pipeline": {
                        "referenceName": "<InvokedPipelineName>",
                        "type": "PipelineReference"
                    },
                    "waitOnCompletion": true
                 }
            }
        ],
        "parameters": [
            {
                "mySourceDatasetFolderPath": {
                    "type": "String"
                }
            }
        ]
    }
}
```

## <a name="type-properties"></a>Свойства типа
Свойство | Описание | Допустимые значения | Обязательно
-------- | ----------- | -------------- | --------
name | Имя действия выполнения конвейера. | Строка | Да
type | Должно иметь значение **ExecutePipeline**. | Строка | Да
pipeline | Ссылка на зависимый конвейер, вызывающий этот конвейер. Объект ссылки конвейера имеет два свойства: **referenceName** и **type**. Свойство referenceName указывает имя конвейера в ссылке. Для свойства type необходимо задать значение PipelineReference. | PipelineReference | Да
parameters | Параметры для передачи в вызванный конвейер | Объект JSON, сопоставляющий имена параметров со значениями аргументов | Нет
waitOnCompletion | Определяет, будет ли при выполнении действия ожидаться завершение выполнения зависимого конвейера. | Значение по умолчанию — false. | Логический | Нет

## <a name="sample"></a>Образец
В этом сценарии есть два конвейера:

- **Главный конвейер.** Этот конвейер содержит одно действие выполнения конвейера, вызывающее вызванный конвейер. Главный конвейер принимает два параметра: `masterSourceBlobContainer`, `masterSinkBlobContainer`.
- **Вызванный конвейер**. Этот конвейер содержит одно действие копирования, копирующее данные из источника больших двоичных объектов Azure в приемник больших двоичных объектов Azure. Вызванный конвейер принимает два параметра: `sourceBlobContainer`, `sinkBlobContainer`.

### <a name="master-pipeline-definition"></a>Определение главного конвейера

```json
{
  "name": "masterPipeline",
  "properties": {
    "activities": [
      {
        "type": "ExecutePipeline",
        "typeProperties": {
          "pipeline": {
            "referenceName": "invokedPipeline",
            "type": "PipelineReference"
          },
          "parameters": {
            "sourceBlobContainer": {
              "value": "@pipeline().parameters.masterSourceBlobContainer",
              "type": "Expression"
            },
            "sinkBlobCountainer": {
              "value": "@pipeline().parameters.masterSinkBlobContainer",
              "type": "Expression"
            }
          },
          "waitOnCompletion": true
        },
        "name": "MyExecutePipelineActivity"
      }
    ],
    "datasets": [],
    "linkedServices": [],
    "parameters": {
      "masterSourceBlobContainer": {
        "type": "String"
      },
      "masterSinkBlobContainer": {
        "type": "String"
      }
    }
  }
}

```

### <a name="invoked-pipeline-definition"></a>Определение вызванного конвейера

```json
{
  "name": "invokedPipeline",
  "properties": {
    "activities": [
      {
        "type": "Copy",
        "typeProperties": {
          "source": {
            "type": "BlobSource"
          },
          "sink": {
            "type": "BlobSink"
          }
        },
        "name": "CopyBlobtoBlob",
        "inputs": [
          {
            "referenceName": "SourceBlobDataset",
            "type": "DatasetReference"
          }
        ],
        "outputs": [
          {
            "referenceName": "sinkBlobDataset",
            "type": "DatasetReference"
          }
        ]
      }
    ],
    "datasets": [
      {
        "name": "SourceBlobDataset",
        "properties": {
          "type": "AzureBlob",
          "typeProperties": {
            "folderPath": {
              "value": "@pipeline().parameters.sourceBlobContainer",
              "type": "Expression"
            },
            "fileName": "salesforce.txt"
          },
          "linkedServiceName": {
            "referenceName": "BlobStorageLinkedService",
            "type": "LinkedServiceReference"
          }
        }
      },
      {
        "name": "sinkBlobDataset",
        "properties": {
          "type": "AzureBlob",
          "typeProperties": {
            "folderPath": {
              "value": "@pipeline().parameters.sinkBlobContainer",
              "type": "Expression"
            }
          },
          "linkedServiceName": {
            "referenceName": "BlobStorageLinkedService",
            "type": "LinkedServiceReference"
          }
        }
      }
    ],
    "linkedServices": [
      {
        "name": "BlobStorageLinkedService",
        "properties": {
          "type": "AzureStorage",
          "typeProperties": {
            "connectionString": {
              "value": "DefaultEndpointsProtocol=https;AccountName=*****",
              "type": "SecureString"
            }
          }
        }
      }
    ],
    "parameters": {
      "sourceBlobContainer": {
        "type": "String"
      },
      "sinkBlobContainer": {
        "type": "String"
      }
    }
  }
}

```

### <a name="running-the-pipeline"></a>Запуск конвейера

Чтобы запустить главный конвейер в этом примере, для параметров masterSourceBlobContainer и masterSinkBlobContainer нужно передать следующие значения: 

```json
{
  "masterSourceBlobContainer": "executetest",
  "masterSinkBlobContainer": "executesink"
}
```

Главный конвейер переадресовывает эти значения в вызванный конвейер, как показано в следующем примере: 

```json
{
    "type": "ExecutePipeline",
    "typeProperties": {
      "pipeline": {
        "referenceName": "invokedPipeline",
        "type": "PipelineReference"
      },
      "parameters": {
        "sourceBlobContainer": {
          "value": "@pipeline().parameters.masterSourceBlobContainer",
          "type": "Expression"
        },
        "sinkBlobCountainer": {
          "value": "@pipeline().parameters.masterSinkBlobContainer",
          "type": "Expression"
        }
      },

      ....
}

```
## <a name="next-steps"></a>Дальнейшие действия
Ознакомьтесь с другими действиями потока управления, которые поддерживаются фабрикой данных: 

- [Действие ForEach](control-flow-for-each-activity.md)
- [Действие получения метаданных](control-flow-get-metadata-activity.md)
- [Действие поиска](control-flow-lookup-activity.md)
- [Веб-действие](control-flow-web-activity.md)

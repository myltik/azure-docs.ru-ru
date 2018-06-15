---
title: Преобразование данных с помощью действия потоковой передачи Hadoop в Azure | Документация Майкрософт
description: Узнайте, как с помощью действия потоковой передачи Hadoop в фабрике данных Azure преобразовывать данные, выполняя программы потоковой передачи Hadoop в собственном кластере HDInsight или кластере по требованию.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.assetid: 4c3ff8f2-2c00-434e-a416-06dfca2c41ec
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: e405f99388f699b3f1e88b9e84f52f9a4380228c
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34621978"
---
# <a name="transform-data-using-hadoop-streaming-activity-in-azure-data-factory"></a>Преобразование данных с помощью действия потоковой передачи Hadoop в фабрике данных Azure
> [!div class="op_single_selector" title1="Transformation Activities"]
> * [Действие Hive](data-factory-hive-activity.md) 
> * [Действие Pig](data-factory-pig-activity.md)
> * [Действие MapReduce](data-factory-map-reduce.md)
> * [Потоковая активность Hadoop](data-factory-hadoop-streaming-activity.md)
> * [Действие Spark](data-factory-spark.md)
> * [Действие выполнения пакета машинного обучения](data-factory-azure-ml-batch-execution-activity.md)
> * [Действие "Обновить ресурс" в службе машинного обучения](data-factory-azure-ml-update-resource-activity.md)
> * [Действие хранимой процедуры](data-factory-stored-proc-activity.md)
> * [Действие U-SQL в Data Lake Analytics](data-factory-usql-activity.md)
> * [Настраиваемое действие .NET](data-factory-use-custom-activities.md)

> [!NOTE]
> Статья относится к версии 1 фабрики данных, которая является общедоступной версией. Если вы используете версию 2 службы фабрики данных, которая находится на этапе предварительной версии, ознакомьтесь с [преобразованием данных с использованием действий потоковой передачи Hadoop в фабрике данных версии 2](../transform-data-using-hadoop-streaming.md).


Можно использовать действие HDInsightStreamingActivity для вызова задания потоковой передачи Hadoop из конвейера фабрики данных Azure. В следующем фрагменте JSON показан синтаксис для использования действия HDInsightStreamingActivity в JSON-файле конвейера. 

Действие потоковой передачи HDInsight в [конвейере](data-factory-create-pipelines.md) фабрики данных выполняет программы потоковой передачи Hadoop для [вашего собственного](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) кластера HDInsight или кластера HDInsight [по запросу](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) под управлением Windows или Linux. Данная статья основана на материалах статьи о [действиях преобразования данных](data-factory-data-transformation-activities.md) , в которой приведен общий обзор преобразования данных и список поддерживаемых действий преобразования.

> [!NOTE] 
> Если вы не знакомы с фабрикой данных Azure, сначала ознакомьтесь со статьей [Введение в фабрику данных Azure](data-factory-introduction.md) и руководством [Создание первого конвейера для преобразования данных с помощью кластера Hadoop](data-factory-build-your-first-pipeline.md). 

## <a name="json-sample"></a>Образец JSON
Кластер HDInsight автоматически заполняется примерами программ (wc.exe и cat.exe) и данных (davinci.txt). По умолчанию именем контейнера, используемым кластером HDInsight, является имя самого кластера. Например, если кластеру задано имя myhdicluster, именем связанного контейнера больших двоичных объектов будет myhdicluster. 

```JSON
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
                    "fileLinkedService": "AzureStorageLinkedService",
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
```

Обратите внимание на следующие моменты.

1. Присвойте параметру **linkedServiceName** имя связанной службы, которое указывает на ваш кластер HDInsight, где будет выполняться задание потоковой передачи MapReduce.
2. Задайте в качестве типа действия значение **HDInsightStreaming**.
3. Для свойства **mapper** укажите имя исполняемого файла mapper. В этом примере таким файлом является cat.exe.
4. Для свойства **reducer** укажите имя исполняемого файла reducer. В этом примере таким файлом является wc.exe.
5. Для свойства типа **input** укажите входной файл (включая местоположение) для свойства mapper. В примере "wasb://adfsample@<account name>.blob.core.windows.net/example/data/gutenberg/davinci.txt" adfsample — это контейнер BLOB-объектов, example/data/Gutenberg — это папка, а davinci.txt — это двоичный большой объект.
6. Для свойства типа **output** укажите выходной файл (включая местоположение) для reducer. Результат задания потоковой передачи Hadoop записывается в расположение, заданное для этого свойства.
7. В разделе **filePaths** укажите пути для исполняемых файлов mapper и reducer. В примере adfsample/example/apps/wc.exe adfsample — это контейнер больших двоичных объектов, example/apps — это папка, а wc.exe — это исполняемый файл.
8. Для свойства **fileLinkedService** укажите связанную службу хранилища Azure, которая представляет хранилище Azure, где хранятся указанные в разделе filePaths файлы.
9. Для свойства **arguments** укажите аргументы для задания потоковой передачи.
10. Свойство **GetDebugInfo** является необязательным элементом. Если для него установлено значение Failure, журналы скачиваются только при сбое. Если для него установлено значение Always, журналы скачиваются всегда, независимо от состояния выполнения.

> [!NOTE]
> Как показано в примере, вам нужно указать выходной набор данных для действия потоковой передачи Hadoop для свойства **outputs** . Это просто фиктивный набор данных, необходимый для соблюдения расписания конвейера. Указывать входной набор данных для действия для свойства **inputs** не требуется.  
> 
> 

## <a name="example"></a>Пример
Конвейер в этом пошаговом руководстве запускает в кластере Azure HDInsight программу потоковой передачи Map/Reduce для подсчета слов. 

### <a name="linked-services"></a>Связанные службы
#### <a name="azure-storage-linked-service"></a>Связанная служба хранения Azure
Сначала необходимо создать связанную службу для связи службы хранилища Azure, используемой в кластере Azure HDInsight, с фабрикой данных Azure. При копировании и вставке следующего кода не забудьте заменить имя и ключ учетной записи на имя и ключ хранилища Azure. 

```JSON
{
    "name": "StorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>"
        }
    }
}
```

#### <a name="azure-hdinsight-linked-service"></a>Связанная служба Azure HDInsight
Далее необходимо создать связанную службу для связи кластера Azure HDInsight с фабрикой данных Azure. При копировании и вставке следующего кода не забудьте заменить имя кластера HDInsight на имя вашего кластера HDInsight и изменить значения имени пользователя и пароля. 

```JSON
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
```

### <a name="datasets"></a>Наборы данных
#### <a name="output-dataset"></a>Выходной набор данных
Конвейер в этом примере не принимает никаких входных данных. Вам нужно указать выходной набор данных для действия потоковой передачи HDInsight. Это просто фиктивный набор данных, необходимый для соблюдения расписания конвейера. 

```JSON
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
```

### <a name="pipeline"></a>Конвейер
В этом примере конвейер имеет только одно действие с типом **HDInsightStreaming**. 

Кластер HDInsight автоматически заполняется примерами программ (wc.exe и cat.exe) и данных (davinci.txt). По умолчанию именем контейнера, используемым кластером HDInsight, является имя самого кластера. Например, если кластеру задано имя myhdicluster, именем связанного контейнера больших двоичных объектов будет myhdicluster.  

```JSON
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
        "start": "2017-01-03T00:00:00Z",
        "end": "2017-01-04T00:00:00Z"
    }
}
```
## <a name="see-also"></a>См. также
* [Действие Hive](data-factory-hive-activity.md)
* [Действие Pig](data-factory-pig-activity.md)
* [Действие MapReduce](data-factory-map-reduce.md)
* [Вызов программ Spark](data-factory-spark.md)
* [Вызов сценариев R](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample)


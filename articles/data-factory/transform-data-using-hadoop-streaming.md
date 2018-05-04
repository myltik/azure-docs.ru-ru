---
title: Преобразование данных с помощью действия потоковой передачи Hadoop в фабрике данных Azure | Документация Майкрософт
description: Эта статья содержит сведения о том, как с помощью действия потоковой передачи Hadoop в фабрике данных Azure преобразовывать данные, выполняя программы потоковой передачи Hadoop в кластере Hadoop.
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2018
ms.author: douglasl
ms.openlocfilehash: c0143b4f2d6858883ac11df18f7844802c667404
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2018
---
# <a name="transform-data-using-hadoop-streaming-activity-in-azure-data-factory"></a>Преобразование данных с помощью действия потоковой передачи Hadoop в фабрике данных Azure
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Версия 1 — общедоступная](v1/data-factory-hadoop-streaming-activity.md)
> * [Версия 2 — предварительная](transform-data-using-hadoop-streaming.md)

Действие потоковой передачи HDInsight в [конвейере](concepts-pipelines-activities.md) фабрики данных выполняет программы потоковой передачи Hadoop для [вашего](compute-linked-services.md#azure-hdinsight-linked-service) кластера HDInsight или кластера HDInsight [по запросу](compute-linked-services.md#azure-hdinsight-on-demand-linked-service). Данная статья основана на материалах статьи о [действиях преобразования данных](transform-data.md) , в которой приведен общий обзор преобразования данных и список поддерживаемых действий преобразования.

> [!NOTE]
> Эта статья относится к версии 2 фабрики данных, которая в настоящее время доступна в предварительной версии. Если используется служба фабрики данных версии 1, которая является общедоступной версией, см. статью [Преобразование данных с помощью действия потоковой передачи Hadoop в фабрике данных Azure](v1/data-factory-hadoop-streaming-activity.md).

Если вы не знакомы с фабрикой данных Azure, сначала ознакомьтесь со статьей [Введение в фабрику данных Azure](introduction.md) и руководством [Преобразование данных в облаке с помощью действия Spark в фабрике данных Azure](tutorial-transform-data-spark-powershell.md). 

## <a name="json-sample"></a>Образец JSON
```json
{
    "name": "Streaming Activity",
    "description": "Description",
    "type": "HDInsightStreaming",
    "linkedServiceName": {
        "referenceName": "MyHDInsightLinkedService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "mapper": "MyMapper.exe",
        "reducer": "MyReducer.exe",
        "combiner": "MyCombiner.exe",
        "fileLinkedService": {
            "referenceName": "MyAzureStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "filePaths": [
            "<containername>/example/apps/MyMapper.exe",
            "<containername>/example/apps/MyReducer.exe",
            "<containername>/example/apps/MyCombiner.exe"
        ],
        "input": "wasb://<containername>@<accountname>.blob.core.windows.net/example/input/MapperInput.txt",
        "output": "wasb://<containername>@<accountname>.blob.core.windows.net/example/output/ReducerOutput.txt",
        "commandEnvironment": [
            "CmdEnvVarName=CmdEnvVarValue"
        ],
        "getDebugInfo": "Failure",
        "arguments": [
            "SampleHadoopJobArgument1"
        ],
        "defines": {
            "param1": "param1Value"
        }
    }
}
```

## <a name="syntax-details"></a>Сведения о синтаксисе

| Свойство          | ОПИСАНИЕ                              | Обязательно |
| ----------------- | ---------------------------------------- | -------- |
| name              | Имя действия.                     | Yes      |
| description       | Текст, описывающий, для чего используется действие | Нет        |
| Тип              | Для действия потоковой передачи Hadoop используется тип действия HDInsightStreaming. | Yes      |
| linkedServiceName | Ссылка на кластер HDInsight, зарегистрированный в качестве связанной службы в фабрике данных. Дополнительные сведения об этой связанной службе см. в статье [Вычислительные среды, поддерживаемые фабрикой данных Azure](compute-linked-services.md). | Yes      |
| mapper            | Указывает имя исполняемого файла средства сопоставления. | Yes      |
| reducer           | Указывает имя исполняемого файла средства приведения. | Yes      |
| combiner          | Указывает имя исполняемого файла средства объединения. | Нет        |
| fileLinkedService | Ссылки на связанные службы хранилища Azure, используемые для хранения программ средств сопоставления, объединения и приведения, которые следует выполнить. Если не указать эту связанную службу, будет использоваться связанная служба хранилища Azure, определенная в связанной службе HDInsight. | Нет        |
| filePath          | Предоставляет массив путей к программам средств сопоставления, объединения и приведения, хранящийся в службе хранилища Azure, на которую ссылается свойство fileLinkedService. Путь следует вводить с учетом регистра. | Yes      |
| input             | Указывает путь WASB к входному файлу для средства сопоставления. | Yes      |
| output            | Указывает путь WASB к выходному файлу для средства приведения. | Yes      |
| getDebugInfo      | Указывает, когда файлы журнала копируются в службу хранилища Azure, используемую кластером HDInsight или определенную scriptLinkedService. Допустимые значения: None, Always или Failure. Значение по умолчанию: None. | Нет        |
| arguments         | Указывает массив аргументов для задания Hadoop. Аргументы передаются в качестве аргументов командной строки в каждую задачу. | Нет        |
| defines           | Параметры в виде пары "ключ — значение", ссылки на которые указываются в скрипте Hive. | Нет        | 

## <a name="next-steps"></a>Дополнительная информация
Ознакомьтесь со следующими ссылками, в которых описаны способы преобразования данных другими способами: 

* [Действие U-SQL](transform-data-using-data-lake-analytics.md)
* [Действие Hive](transform-data-using-hadoop-hive.md)
* [Действие Pig](transform-data-using-hadoop-pig.md)
* [Действие MapReduce](transform-data-using-hadoop-map-reduce.md)
* [Действие Spark](transform-data-using-spark.md)
* [Настраиваемое действие .NET](transform-data-using-dotnet-custom-activity.md)
* [Создание прогнозирующих конвейеров с помощью машинного обучения Azure и фабрики данных Azure](transform-data-using-machine-learning.md)
* [Действие хранимой процедуры](transform-data-using-stored-procedure.md)

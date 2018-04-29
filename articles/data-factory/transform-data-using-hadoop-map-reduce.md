---
title: Преобразование данных с помощью действия Hadoop MapReduce в фабрике данных Azure | Документация Майкрософт
description: Узнайте, как обрабатывать данные путем выполнения программ Hadoop MapReduce в кластере Azure HDInsight из фабрики данных Azure.
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
ms.openlocfilehash: ecaa9b612dcf7760987b8b8438808b0a961ef193
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2018
---
# <a name="transform-data-using-hadoop-mapreduce-activity-in-azure-data-factory"></a>Преобразование данных с помощью действия Hadoop MapReduce в фабрике данных Azure
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Версия 1 — общедоступная](v1/data-factory-map-reduce.md)
> * [Версия 2 — предварительная](transform-data-using-hadoop-map-reduce.md)


Действие MapReduce HDInsight в [конвейере](concepts-pipelines-activities.md) фабрики данных вызывает программу MapReduce для [вашего](compute-linked-services.md#azure-hdinsight-linked-service) кластера HDInsight или кластера HDInsight [по запросу](compute-linked-services.md#azure-hdinsight-on-demand-linked-service). Данная статья основана на материалах статьи о [действиях преобразования данных](transform-data.md) , в которой приведен общий обзор преобразования данных и список поддерживаемых действий преобразования.

> [!NOTE]
> Эта статья относится к версии 2 фабрики данных, которая в настоящее время доступна в предварительной версии. Если используется служба фабрики данных версии 1, которая является общедоступной версией, см. статью [Вызов программы MapReduce из фабрики данных](v1/data-factory-map-reduce.md).


Если вы не знакомы с фабрикой данных Azure, сначала ознакомьтесь со статьей [Введение в фабрику данных Azure](introduction.md) и руководством [Преобразование данных в облаке с помощью действия Spark в фабрике данных Azure](tutorial-transform-data-spark-powershell.md). 

Дополнительные сведения о выполнении скриптов Pig и Hive в кластере HDInsight из конвейера с помощью действий Pig и Hive в HDInsight см. в статьях о действии [Pig](transform-data-using-hadoop-pig.md) и [Hive](transform-data-using-hadoop-hive.md). 

## <a name="syntax"></a>Синтаксис

```json
{
    "name": "Map Reduce Activity",
    "description": "Description",
    "type": "HDInsightMapReduce",
    "linkedServiceName": {
        "referenceName": "MyHDInsightLinkedService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "className": "org.myorg.SampleClass",
        "jarLinkedService": {
            "referenceName": "MyAzureStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "jarFilePath": "MyAzureStorage/jars/sample.jar",
        "getDebugInfo": "Failure",
        "arguments": [
          "-SampleHadoopJobArgument1"
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
| Тип              | Для действия MapReduce Activity используется тип действия HDinsightMapReduce. | Yes      |
| linkedServiceName | Ссылка на кластер HDInsight, зарегистрированный в качестве связанной службы в фабрике данных. Дополнительные сведения об этой связанной службе см. в статье [Вычислительные среды, поддерживаемые фабрикой данных Azure](compute-linked-services.md). | Yes      |
| className         | Имя класса для выполнения.         | Yes      |
| jarLinkedService  | Ссылки на связанные службы хранилища Azure, используемые для хранения файлов Jar. Если не указать эту связанную службу, будет использоваться связанная служба хранилища Azure, определенная в связанной службе HDInsight. | Нет        |
| jarFilePath       | Укажите путь к файлам Jar, которые хранятся в службе хранилища Azure, на который ссылается jarLinkedService. В имени файла учитывается регистр знаков. | Yes      |
| jarlibs           | Массив строк пути к файлам библиотеки Jar, на которые ссылается задание, хранящееся в службе хранилища Azure, которая, в свою очередь, определена в свойстве jarLinkedService. В имени файла учитывается регистр знаков. | Нет        |
| getDebugInfo      | Указывает, когда файлы журнала копируются в службу хранилища Azure, используемую кластером HDInsight или определенную jarLinkedService. Допустимые значения: None, Always или Failure. Значение по умолчанию: None. | Нет        |
| arguments         | Указывает массив аргументов для задания Hadoop. Аргументы передаются в качестве аргументов командной строки в каждую задачу. | Нет        |
| defines           | Параметры в виде пары "ключ — значение", ссылки на которые указываются в скрипте Hive. | Нет        |



## <a name="example"></a>Пример
С помощью действия MapReduce можно выполнить JAR-файл MapReduce в кластере HDInsight. В приведенном ниже образце определения конвейера JSON действие HDInsight настроено на запуск JAR-файла Mahout.

```json   
{
    "name": "MapReduce Activity for Mahout",
    "description": "Custom MapReduce to generate Mahout result",
    "type": "HDInsightMapReduce",
    "linkedServiceName": {
        "referenceName": "MyHDInsightLinkedService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "className": "org.apache.mahout.cf.taste.hadoop.similarity.item.ItemSimilarityJob",
        "jarLinkedService": {
            "referenceName": "MyStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "jarFilePath": "adfsamples/Mahout/jars/mahout-examples-0.9.0.2.2.7.1-34.jar",
        "arguments": [
            "-s",
            "SIMILARITY_LOGLIKELIHOOD",
            "--input",
            "wasb://adfsamples@spestore.blob.core.windows.net/Mahout/input",
            "--output",
            "wasb://adfsamples@spestore.blob.core.windows.net/Mahout/output/",
            "--maxSimilaritiesPerItem",
            "500",
            "--tempDir",
            "wasb://adfsamples@spestore.blob.core.windows.net/Mahout/temp/mahout"
        ]
    }
}
```
Вы можете указать необходимые аргументы для программы MapReduce в разделе **arguments**. Во время выполнения вы увидите несколько дополнительных аргументов (например, mapreduce.job.tags) платформы MapReduce. Чтобы отличать свои аргументы от аргументов MapReduce, вы можете использовать параметр и значение в качестве аргументов, как показано в следующем примере (-s, --input, --output и т. д — параметры, за которыми сразу следуют их значения).

## <a name="next-steps"></a>Дополнительная информация
Ознакомьтесь со следующими ссылками, в которых описаны способы преобразования данных другими способами: 

* [Действие U-SQL](transform-data-using-data-lake-analytics.md)
* [Действие Hive](transform-data-using-hadoop-hive.md)
* [Действие Pig](transform-data-using-hadoop-pig.md)
* [Действие потоковой передачи Hadoop](transform-data-using-hadoop-streaming.md)
* [Действие Spark](transform-data-using-spark.md)
* [Настраиваемое действие .NET](transform-data-using-dotnet-custom-activity.md)
* [Создание прогнозирующих конвейеров с помощью машинного обучения Azure и фабрики данных Azure](transform-data-using-machine-learning.md)
* [Действие хранимой процедуры](transform-data-using-stored-procedure.md)

---
title: "Вызов программ Spark из фабрики данных Azure"
description: "Узнайте, как вызывать программы Spark из фабрики данных Azure с помощью действия MapReduce."
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: monicar
ms.assetid: fd98931c-cab5-4d66-97cb-4c947861255c
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/21/2017
ms.author: spelluru
translationtype: Human Translation
ms.sourcegitcommit: bb1ca3189e6c39b46eaa5151bf0c74dbf4a35228
ms.openlocfilehash: 8be5e1525a7c481de5cb02edd26da305af2d4798
ms.lasthandoff: 03/18/2017


---
# <a name="invoke-spark-programs-from-data-factory"></a>Вызов программ Spark из фабрики данных
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

## <a name="introduction"></a>Введение
Действие HDInsight Spark в [конвейере](data-factory-create-pipelines.md) фабрики данных выполняет программы Spark в [вашем](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) кластере HDInsight. Данная статья основана на материалах статьи о [действиях преобразования данных](data-factory-data-transformation-activities.md) , в которой приведен общий обзор преобразования данных и список поддерживаемых действий преобразования.

## <a name="hdinsight-linked-service"></a>Связанная служба HDInsight
Прежде чем использовать действие Spark в конвейере фабрики данных, создайте (собственную) связанную службу HDInsight. В следующем фрагменте JSON показано определение связанной службы HDInsight, указывающей на ваш кластер Azure HDInsight Spark.   

```json
{
    "name": "HDInsightLinkedService",
    "properties": {
        "type": "HDInsight",
        "typeProperties": {
            "clusterUri": "https://MyHdinsightSparkcluster.azurehdinsight.net/",
              "userName": "admin",
              "password": "password",
              "linkedServiceName": "MyHDInsightStoragelinkedService"
        }
    }
}
```

> [!NOTE]
> В настоящее время действие Spark не поддерживает кластеры Spark, использующие Data Lake Store в качестве основного хранилища или связанную службу HDInsight по запросу. 

Дополнительные сведения о связанной службе HDInsight и других связанных службах вычислений см. в статье [Вычислительные среды, поддерживаемые фабрикой данных Azure](data-factory-compute-linked-services.md). 

## <a name="spark-activity-json"></a>Определение JSON действия Spark
Ниже приведен пример определения JSON действия Spark.    

```json
{
    "name": "MySparkActivity",
    "description": "This activity invokes the Spark program",
    "type": "HDInsightSpark",
    "outputs": [
        {
            "name": "PlaceholderDataset"
        }
    ],
    "linkedServiceName": "HDInsightLinkedService",
    "typeProperties": {
        "rootPath": "mycontainer\\myfolder",
        "entryFilePath": "main.py",
        "arguments": [ "arg1", "arg2" ],
        "sparkConfig": {
              "spark.python.worker.memory": "512m"
        }
    }
}
```
В следующей таблице приведено описание свойств, используемых в определении JSON. 

| Свойство | Описание | Обязательно |
| -------- | ----------- | -------- |
| name | Имя действия в конвейере. | Да |
| description | Описание действия. | Нет |
| type | Для этого свойства необходимо задать значение HDInsightSpark. | Да |
| linkedServiceName (имя связанной службы) | Ссылка на связанную службу HDInsight, в которой выполняется программа Spark. | Да |
| rootPath | Контейнер BLOB-объектов Azure и папка, содержащая файл Spark. В имени файла учитывается регистр знаков. | Да |
| entryFilePath | Относительный путь к корневой папке пакета и кода Spark. | Да |
| className | Основной класс Java или Spark приложения. | Нет | 
| arguments | Список аргументов командной строки для программы Spark. | Нет | 
| proxyUser | Учетная запись пользователя для олицетворения, используемая для выполнения программы Spark. | Нет | 
| sparkConfig | Свойства конфигурации Spark. | Нет | 
| getDebugInfo | Указывает, когда файлы журнала Spark копируются в службу хранилища Azure, используемое кластером HDInsight или определенное sparkJobLinkedService. Допустимые значения: None, Always или Failure. Значение по умолчанию: None. | Нет | 
| sparkJobLinkedService | Связанная служба службы хранилища Azure, в которой хранятся файл задания Spark, зависимости и журналы.  Если значение этого свойства не указано, используется хранилище, связанное с кластером HDInsight. | Нет |

## <a name="folder-structure"></a>Структура папок
Действие Spark не поддерживает встроенный сценарий, в отличие от действий Pig и Hive. Задания Spark также обеспечивают большую гибкость, чем задания Pig и Hive. Для заданий Spark можно указать несколько зависимостей, например пакеты JAR (размещаются в CLASSPATH Java), файлы Python (размещаются в PYTHONPATH) и другие файлы.

Создайте следующую структуру папок в хранилище BLOB-объектов Azure, на которое ссылается связанная служба HDInsight. Затем передайте зависимые файлы в соответствующие вложенные папки в корневой папке, определенной значением **entryFilePath**. Например, передайте файлы Python во вложенную папку pyFiles, а JAR-файлы — во вложенную папку jars, расположенный в корневой папке. Во время выполнения служба фабрики данных ожидает в хранилище BLOB-объектов Azure следующую структуру папок.     

| Путь | Описание | Обязательно | Тип |
| ---- | ----------- | -------- | ---- | 
| .    | Путь к корневому каталогу задания Spark в хранилище связанной службы.    | Да | Папка |
| &lt;Определяется пользователем&gt; | Путь к файлу записи задания Spark. | Да | Файл | 
| ./jars | Все файлы в этой папке передаются и помещаются в папку CLASSPATH Java для кластера. | Нет | Папка |
| ./pyFiles | Все файлы в этой папке передаются и помещаются в папку PYTHONPATH для кластера. | Нет | Папка |
| ./files | Все файлы в этой папке передаются и помещаются в рабочий каталог исполнителя. | Нет | Папка |
| ./archives | Все файлы в этой папке не сжаты. | Нет | Папка |
| ./logs | Папка, в которой хранятся журналы из кластера Spark.| Нет | Папка |

Ниже приведен пример хранилища, содержащего два файла заданий Spark в хранилище BLOB-объектов Azure, на которое ссылается связанная служба HDInsight. 

```
SparkJob1
    main.jar
    files
        input1.txt
        input2.txt
    jars
        package1.jar
        package2.jar
    logs

SparkJob2
    main.py
    pyFiles
        scrip1.py
        script2.py
    logs    
```

> [!IMPORTANT]
> Полное пошаговое руководство по созданию конвейера с действием преобразования приведено в статье [Создание конвейера преобразования данных](data-factory-build-your-first-pipeline-using-editor.md). 



## <a name="see-also"></a>См. также
* [Действие Hive](data-factory-hive-activity.md)
* [Действие Pig](data-factory-pig-activity.md)
* [Действие MapReduce](data-factory-map-reduce.md)
* [Потоковая активность Hadoop](data-factory-hadoop-streaming-activity.md)
* [Вызов сценариев R](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample)



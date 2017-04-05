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
ms.sourcegitcommit: 432752c895fca3721e78fb6eb17b5a3e5c4ca495
ms.openlocfilehash: 3c7d109ec7fd92859cad4ded7422105e8094485c
ms.lasthandoff: 03/30/2017


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

> [!IMPORTANT]
> Если вы еще не знакомы с фабрикой данных Azure, перед прочтением этой статьи мы рекомендуем ознакомиться c руководством по [созданию первого конвейера](data-factory-build-your-first-pipeline.md). Общие сведения о службе фабрики данных Azure см. в [этой статье](data-factory-introduction.md). 

## <a name="apache-spark-cluster-in-azure-hdinsight"></a>Кластер Apache Spark в Azure HDInsight
Сначала создайте кластер Apache Spark в Azure HDInsight, следуя инструкциям в [этом руководстве](../hdinsight/hdinsight-apache-spark-jupyter-spark-sql.md). 

## <a name="create-data-factory"></a>Создание фабрики данных 
Ниже приведены стандартные действия для создания конвейера фабрики данных с действием Spark.  

1. Создадите фабрику данных.
2. Создайте связанную службу, связывающую кластер Apache Spark в Azure HDInsight с вашей фабрикой данных.
3. Затем определите выходной набор данных для действия, даже если выходные данные не выдаются. Чтобы создать набор данных большого двоичного объекта Azure, сделайте следующее:
    1. Создайте связанную службу, которая связывает учетную запись хранения Azure с фабрикой данных.
    2. Создайте набор данных, который ссылается на связанную службу хранилища Azure.  
3. Создайте конвейер с действием Spark, которое ссылается на связанную службу Apache HDInsight, созданную на шаге 2. Конфигурация действия выполняется на основе набора данных, созданного на предыдущем шаге в качестве выходного набора данных. На основе этого набора настраивается расписание (ежечасно, ежедневно и т. д.). Исходя из сказанного выше, вы должны определить выходной набор данных, даже если действие не выдает выходные данные.

Подробные инструкции по созданию фабрики данных см. в руководстве [по созданию первого конвейера](data-factory-build-your-first-pipeline.md). В рамках этого руководства используется действие Hive и кластер HDInsight Hadoop, но действия при использовании действия Spark и кластера HDInsight Spark аналогичные.   

В следующих разделах приведены сведения о сущностях фабрики данных, необходимых для использования кластера Apache Spark и действия Spark в фабрике данных.   

## <a name="hdinsight-linked-service"></a>Связанная служба HDInsight
Прежде чем использовать действие Spark в конвейере фабрики данных, создайте (собственную) связанную службу HDInsight. В следующем фрагменте JSON показано определение связанной службы HDInsight, указывающей на кластер Azure HDInsight Spark.   

```json
{
    "name": "HDInsightLinkedService",
    "properties": {
        "type": "HDInsight",
        "typeProperties": {
            "clusterUri": "https://<nameofyoursparkcluster>.azurehdinsight.net/",
              "userName": "admin",
              "password": "password",
              "linkedServiceName": "MyHDInsightStoragelinkedService"
        }
    }
}
```

> [!NOTE]
> Сейчас действие Spark не поддерживает кластеры Spark, использующие Azure Data Lake Store в качестве основного хранилища или связанной службы HDInsight по запросу. 

Дополнительные сведения о связанной службе HDInsight и других связанных службах вычислений см. в статье [Вычислительные среды, поддерживаемые фабрикой данных Azure](data-factory-compute-linked-services.md). 

## <a name="spark-activity-json"></a>Определение JSON действия Spark
Ниже приведен пример определения JSON конвейера с действием Spark.    

```json
{
    "name": "SparkPipeline",
    "properties": {
        "activities": [
            {
                "type": "HDInsightSpark",
                "typeProperties": {
                    "rootPath": "adfspark\\pyFiles",
                    "entryFilePath": "test.py",
                    "arguments": [ "arg1", "arg2" ],
                    "sparkConfig": {
                        "spark.python.worker.memory": "512m"
                    }
                    "getDebugInfo": "Always"
                },
                "outputs": [
                    {
                        "name": "OutputDataset"
                    }
                ],
                "name": "MySparkActivity",
                "description": "This activity invokes the Spark program",
                "linkedServiceName": "HDInsightLinkedService"
            }
        ],
        "start": "2017-02-01T00:00:00Z",
        "end": "2017-02-02T00:00:00Z"
    }
}
```

Обратите внимание на следующие моменты. 
- Свойству type присваивается значение HDInsightSpark. 
- Свойству rootPath присваивается значение adfspark\\pyFiles, где adfspark — контейнер большого двоичного объекта Azure, а pyFiles — папка с файлами в этом контейнере. В этом примере хранилище BLOB-объектов Azure связано с кластером Spark. Файл можно отправить в другое хранилище Azure. Чтобы сделать это, создайте связанную службу хранилища Azure, которая свяжет эту учетную запись хранения с фабрикой данных. Затем укажите имя связанной службы в качестве значения свойства sparkJobLinkedService. Сведения об этом свойстве и других свойствах, поддерживаемых действием Spark, см. в [этом разделе](#spark-activity-properties).  
- Свойству entryFilePath присваивается значение test.py, которое является файлом Python. 
- Значения параметров программы Spark передаются при помощи свойства arguments. В этом примере используются два аргумента: arg1 и arg2. 
- Свойству getDebugInfo присваивается значение Always, что означает, что файлы журналов создаются постоянно (успешные и неудачные события). 
- В разделе sparkConfig указан один параметр среды Python: worker.memory. 
- В разделе outputs содержится один выходной набор данных. Вы должны определить выходной набор данных, даже если программа Spark не выдает выходные данные. На основе этого набора настраивается расписание конвейера (ежечасно, ежедневно и т. д.).     

Свойства type (в разделе typeProperties) описаны далее в разделе [Свойства действия Spark](#spark-activity-properties) этой статьи. 

Как упоминалось ранее, вы должны определить выходной набор данных действия, так как на его основе настраивается расписание конвейера (ежечасно, ежедневно и т. д.). В этом примере используется набор данных большого двоичного объекта Azure. Чтобы создать набор данных большого двоичного объекта Azure, сначала необходимо создать связанную службу хранилища Azure. 

Ниже приведен пример определений связанной службы хранилища Azure и набор данных большого двоичного объекта Azure. 

**Связанная служба хранилища Azure:**
```json
{
    "name": "AzureStorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<storageaccountname>;AccountKey=<storageaccountkey>"
        }
    }
}
```
 

**Набор данных большого двоичного объекта Azure:** 
```json
{
    "name": "OutputDataset",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "fileName": "sparkoutput.txt",
            "folderPath": "spark/output/",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": "\t"
            }
        },
        "availability": {
            "frequency": "Day",
            "interval": 1
        }
    }
}
```

Этот набор данных является фиктивным. Фабрика данных использует параметры частоты и интервала и запускает конвейер каждый день в пределах между указанным временем начала и окончания. В примере определения конвейера время начала и окончания указаны с разницей в день, поэтому конвейер запускается только один раз. 

## <a name="spark-activity-properties"></a>Свойства действия Spark

В следующей таблице приведено описание свойств, используемых в определении JSON. 

| Свойство | Описание | Обязательно |
| -------- | ----------- | -------- |
| name | Имя действия в конвейере. | Да |
| description | Описание действия. | Нет |
| type | Для этого свойства необходимо задать значение HDInsightSpark. | Да |
| linkedServiceName (имя связанной службы) | Имя связанной службы HDInsight, в которой выполняется программа Spark. | Да |
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



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
ms.date: 02/06/2017
ms.author: spelluru
translationtype: Human Translation
ms.sourcegitcommit: b3895dbf56c58e8e10e6af84afc520e5aef6e01e
ms.openlocfilehash: c2eb07b83f3096f86772f9af63a48da79ce0fd2e


---
# <a name="invoke-spark-programs-from-data-factory"></a>Вызов программ Spark из фабрики данных
## <a name="introduction"></a>Введение
Для запуска программ Spark в кластере HDInsight Spark можно использовать действие MapReduce в конвейере фабрики данных. Перед чтением этой статьи просмотрите статью, посвященную [действию MapReduce](data-factory-map-reduce.md) , в которой содержатся подробные сведения об использовании этого действия. 

## <a name="spark-sample-on-github"></a>Пример Spark в GitHub
В [примере "Spark — фабрика данных" в GitHub](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/Spark) показано, как использовать действие MapReduce для запуска программы Spark. Программа Spark просто копирует данные из одного контейнера BLOB-объектов Azure в другой. 

## <a name="data-factory-entities"></a>Сущности фабрики данных
В папке **Spark-ADF/src/ADFJsons** содержатся файлы для сущностей фабрики данных (связанные службы, наборы данных, конвейер).  

В этом примере имеется две **связанные службы**: служба хранилища Azure и Azure HDInsight. Имя службы хранилища Azure и значения ключа необходимо указать в файле **StorageLinkedService.json**. Также необходимо указать значения параметров clusterUri, userName и password в файле **HDInsightLinkedService.json**.

В этом примере имеется два **набора данных**: **input.json** и **output.json**. Эти файлы расположены в папке **Datasets** .  Они представляют входные и выходные наборы данных для действия MapReduce.

Примеры конвейеров можно найти в папке **ADFJsons/Pipeline** . Просмотрите конвейер, чтобы понять, как вызывать программу Spark с помощью действия MapReduce. 

Действие MapReduce настроено для вызова **com.adf.sparklauncher.jar** в контейнере **adflibs** в службе хранилища Azure (указанной в файле StorageLinkedService.json). Исходный код для этой программы находится в папке Spark-ADF/src/main/java/com/adf/. Он вызывает spark-submit и запускает задания Spark. 

> [!IMPORTANT]
> Перед использованием примера прочтите файл [README.md](https://github.com/Azure/Azure-DataFactory/blob/master/Samples/Spark/README.md), чтобы ознакомиться с последней дополненной информацией. 
> 
> При таком вызове программ Spark с помощью действия MapReduce используйте собственный кластер Spark HDInsight. Использование кластера HDInsight по требованию не поддерживается.   
> 
> 

## <a name="see-also"></a>См. также
* [Действие Hive](data-factory-hive-activity.md)
* [Действие Pig](data-factory-pig-activity.md)
* [Действие MapReduce](data-factory-map-reduce.md)
* [Потоковая активность Hadoop](data-factory-hadoop-streaming-activity.md)
* [Вызов сценариев R](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample)




<!--HONumber=Nov16_HO3-->



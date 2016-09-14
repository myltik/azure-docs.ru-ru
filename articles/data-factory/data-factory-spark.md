<properties 
	pageTitle="Вызов программ Spark из фабрики данных Azure" 
	description="Узнайте, как вызывать программы Spark из фабрики данных Azure с помощью действия MapReduce." 
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
	ms.date="08/25/2016" 
	ms.author="spelluru"/>

# Вызов программ Spark из фабрики данных
## Введение
Для запуска программ Spark в кластере HDInsight Spark можно использовать действие MapReduce в конвейере фабрики данных. Перед чтением этой статьи просмотрите статью, посвященную [действию MapReduce](data-factory-map-reduce.md), в которой содержатся подробные сведения об использовании этого действия.

## Пример Spark в GitHub
В [примере "Spark — фабрика данных" в GitHub](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/Spark) показано, как использовать действие MapReduce для запуска программы Spark. Программа Spark просто копирует данные из одного контейнера BLOB-объектов Azure в другой.

## Сущности фабрики данных
В папке **Spark-ADF/src/ADFJsons** содержатся файлы для сущностей фабрики данных (связанные службы, наборы данных, конвейер).

В этом примере имеется две **связанные службы**: служба хранилища Azure и Azure HDInsight. Имя службы хранилища Azure и значения ключа необходимо указать в файле **StorageLinkedService.json**. Также необходимо указать значения параметров clusterUri (URI кластера), userName (имя пользователя) и password (пароль) в файле **HDInsightLinkedService.json**.

В этом примере существует два **набора данных**: **input.json** и **output.json**. Эти файлы расположены в папке **Datasets**. Они представляют входные и выходные наборы данных для действия MapReduce.

Примеры конвейеров можно найти в папке **ADFJsons/Pipeline**. Просмотрите конвейер, чтобы понять, как вызывать программу Spark с помощью действия MapReduce.

Действие MapReduce настроено для вызова **com.adf.sparklauncher.jar** в контейнере **adflibs** в службе хранилища Azure (указанной в файле StorageLinkedService.json). Исходный код для этой программы находится в папке Spark-ADF/src/main/java/com/adf/. Он вызывает spark-submit и запускает задания Spark.

> [AZURE.IMPORTANT] 
Перед использованием примера прочтите файл [README.TXT](https://github.com/Azure/Azure-DataFactory/blob/master/Samples/Spark/README.txt), чтобы ознакомиться с последней дополненной информацией.
>  
> При таком вызове программ Spark с помощью действия MapReduce используйте собственный кластер Spark HDInsight. Использование кластера HDInsight по требованию не поддерживается.


## См. также
- [Действие Hive](data-factory-hive-activity.md)
- [Действие Pig](data-factory-pig-activity.md)
- [Действие MapReduce](data-factory-map-reduce.md)
- [Потоковая активность Hadoop](data-factory-hadoop-streaming-activity.md)
- [Вызов сценариев R](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample)

<!---HONumber=AcomDC_0831_2016-->
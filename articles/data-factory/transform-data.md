---
title: Преобразование данных с помощью фабрики данных Azure | Документация Майкрософт
description: Узнайте, как преобразовать или обработать данные в фабрике данных Azure с помощью Hadoop, Машинного обучения Azure или Azure Data Lake Analytics.
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
ms.openlocfilehash: 4915e8aebf110b5ae799f409472b43b986a18b8d
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2018
---
# <a name="transform-data-in-azure-data-factory"></a>Преобразование данных в фабрике данных Azure
> [!div class="op_single_selector"]
> * [Hive](transform-data-using-hadoop-hive.md)  
> * [Pig](transform-data-using-hadoop-pig.md)  
> * [MapReduce](transform-data-using-hadoop-map-reduce.md)  
> * [Потоковая передача Hadoop](transform-data-using-hadoop-streaming.md)
> * [Spark](transform-data-using-spark.md)
> * [Машинное обучение](transform-data-using-machine-learning.md) 
> * [Хранимая процедура](transform-data-using-stored-procedure.md)
> * [Аналитика озера данных U-SQL](transform-data-using-data-lake-analytics.md)
> * [Пользовательские действия .NET](transform-data-using-dotnet-custom-activity.md)

## <a name="overview"></a>Обзор
В этой статье объясняются действия преобразования данных в фабрике данных Azure, с помощью которых можно обрабатывать необработанные данные и преобразовывать их в прогнозы и аналитику. Действие преобразования выполняется в вычислительной среде, например в кластере Azure HDInsight или пакетной службе Azure. Статья содержит ссылки на статьи с подробными сведениями о каждом действии преобразования.

Фабрика данных поддерживает указанные ниже действия преобразования, которые вы можете добавлять в [конвейеры](concepts-pipelines-activities.md) как по отдельности, так и в цепочке с другим действием.

> [!NOTE]
> Эта статья относится к версии 2 фабрики данных, которая в настоящее время доступна в предварительной версии. Если вы используете общедоступную версию 1 службы фабрики данных, см. статью о [действиях по преобразованию в фабрике данных версии 1](v1/data-factory-data-transformation-activities.md).
 

## <a name="hdinsight-hive-activity"></a>Действие Hive HDInsight
Действие Hive HDInsight в конвейере фабрики данных выполняет запросы Hive к вашему собственному кластеру HDInsight или кластеру HDInsight по запросу под управлением Windows или Linux. Дополнительные сведения об этом действии см. в статье [Преобразование данных с помощью действия Hadoop Hive в фабрике данных Azure](transform-data-using-hadoop-hive.md). 

## <a name="hdinsight-pig-activity"></a>Действие Pig HDInsight
Действие Pig HDInsight в конвейере фабрики данных выполняет запросы Pig к вашему собственному кластеру HDInsight или кластеру HDInsight по запросу под управлением Windows или Linux. Дополнительные сведения об этом действии см. в статье [Преобразование данных с помощью действия Hadoop Pig в фабрике данных Azure](transform-data-using-hadoop-pig.md). 

## <a name="hdinsight-mapreduce-activity"></a>Действие MapReduce HDInsight
Действие MapReduce HDInsight в конвейере фабрики данных выполняет программы MapReduce для вашего собственного кластера HDInsight или кластера HDInsight по запросу под управлением Windows или Linux. Дополнительные сведения об этом действии см. в статье [Преобразование данных с помощью действия MapReduce в фабрике данных Azure](transform-data-using-hadoop-map-reduce.md).

## <a name="hdinsight-streaming-activity"></a>Действие потоковой передачи HDInsight
Действие потоковой передачи HDInsight в конвейере фабрики данных выполняет программы потоковой передачи Hadoop для вашего собственного кластера HDInsight или кластера HDInsight по запросу под управлением Windows или Linux. Дополнительные сведения об этом действии см. в разделе [Потоковая активность Hadoop](transform-data-using-hadoop-streaming.md).

## <a name="hdinsight-spark-activity"></a>Действие HDInsight Spark
Действие HDInsight Spark в конвейере фабрики данных выполняет программы Spark в вашем кластере HDInsight. Дополнительные сведения см. в разделе [Вызов программ Spark из фабрики данных](transform-data-using-spark.md). 

## <a name="machine-learning-activities"></a>Действия машинного обучения
Фабрика данных Azure позволяет легко создавать конвейеры, в которых для прогнозной аналитики используется опубликованная веб-служба Машинного обучения Azure. С помощью [действия выполнения пакета](transform-data-using-machine-learning.md) в конвейере фабрики данных Azure можно вызывать веб-службу Машинного обучения Azure, чтобы создавать прогнозы по данным в пакете.

Со временем прогнозные модели в оценивающих экспериментах машинного обучения потребуют повторного обучения с помощью новых входных наборов данных. Когда повторное обучение будет завершено, вам потребуется обновить веб-службу оценки на основании обновленной модели машинного обучения. Чтобы обновить веб-службу с помощью заново обученной модели, можно использовать [действие обновления ресурса](update-machine-learning-models.md).  

Дополнительные сведения об этих действиях машинного обучения см. в разделе [Создание прогнозных конвейеров с помощью действий машинного обучения Azure](transform-data-using-machine-learning.md). 

## <a name="stored-procedure-activity"></a>Действие хранимой процедуры
C помощью действия хранимой процедуры SQL Server в конвейере фабрики данных можно вызвать хранимую процедуру из одного из следующих хранилищ данных: база данных SQL Azure, хранилище данных SQL Azure, база данных SQL Server вашего предприятия или виртуальная машина Azure. Дополнительные сведения см. в статье [Преобразование данных с помощью действия хранимой процедуры SQL Server в фабрике данных Azure](transform-data-using-stored-procedure.md).  

## <a name="data-lake-analytics-u-sql-activity"></a>Действие U-SQL Data Lake Analytics
Действие U-SQL Data Lake Analytics запускает скрипт U-SQL для кластера Azure Data Lake Analytics. Дополнительные сведения см. в статье [Преобразование данных с помощью сценариев U-SQL в Azure Data Lake Analytics](transform-data-using-data-lake-analytics.md). 

## <a name="net-custom-activity"></a>Настраиваемое действие .NET
Если вам нужно преобразовать данные способом, который не поддерживается фабрикой данных Azure, то можно создать настраиваемое действие с собственной логикой обработки данных и использовать это действие в конвейере. Можно настроить запуск настраиваемого действия .NET с помощью пакетной службы Azure или кластера HDInsight. Дополнительные сведения см. в разделе [Использование настраиваемых действий в конвейере фабрики данных Azure](transform-data-using-dotnet-custom-activity.md). 

Можно создать настраиваемое действие для выполнения сценариев R в кластере HDInsight, где установлена среда R. Ознакомьтесь с примером в репозитории GitHub [Run R Script using Azure Data Factory](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample) (Запуск сценария R с помощью фабрики данных Azure). 

## <a name="compute-environments"></a>Вычислительные среды
Вы создаете связанную службу для среды вычислений, а затем используете эту службу при определении действия преобразования. Фабрика данных поддерживает вычислительные среды двух типов. 

- **По требованию**: в этом случае вычислительная среда полностью управляется фабрикой данных. Среда автоматически создается службой фабрики данных перед отправкой задания для обработки данных и удаляется после его выполнения. Вы можете настраивать и изменять для вычислительной среды "по требованию" детализированные параметры выполнения задания, управления кластером и действий начальной загрузки. 
- **Собственная**— в этом случае вы регистрируете собственную вычислительную среду (например, кластер HDInsight) и используете ее в качестве связанной службы в фабрике данных. Вы будете управлять средой вычислений, а служба фабрики данных — использовать ее для выполнения действий. 

В статье [Связанные службы вычислений](compute-linked-services.md) описывается, какие службы вычислений поддерживает фабрика данных. 

## <a name="next-steps"></a>Дополнительная информация
Пример использования действия преобразования см. в руководстве [Преобразование данных в облаке с помощью действия Spark в фабрике данных Azure](tutorial-transform-data-spark-powershell.md).
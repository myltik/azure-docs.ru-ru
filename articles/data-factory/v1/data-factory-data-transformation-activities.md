---
title: 'Преобразование данных: обработка и преобразование данных | Документация Майкрософт'
description: Узнайте, как преобразовать или обработать данные в фабрике данных Azure с помощью Hadoop, Машинного обучения Azure или Azure Data Lake Analytics.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.assetid: 39786731-1e4b-40a4-81b7-d06e127427aa
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: aa51073395a1049cd78f13ba185119707a787b29
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34622301"
---
# <a name="transform-data-in-azure-data-factory"></a>Преобразование данных в фабрике данных Azure
> [!div class="op_single_selector"]
> * [Hive](data-factory-hive-activity.md)  
> * [Pig](data-factory-pig-activity.md)  
> * [MapReduce](data-factory-map-reduce.md)  
> * [Потоковая передача Hadoop](data-factory-hadoop-streaming-activity.md)
> * [Машинное обучение](data-factory-azure-ml-batch-execution-activity.md) 
> * [Хранимая процедура](data-factory-stored-proc-activity.md)
> * [Аналитика озера данных U-SQL](data-factory-usql-activity.md)
> * [Пользовательские действия .NET](data-factory-use-custom-activities.md)

## <a name="overview"></a>Обзор
> [!NOTE]
> Статья относится к версии 1 фабрики данных, которая является общедоступной версией. Если вы используете версию 2 службы фабрики данных, которая находится на этапе предварительной версии, см. статью о [действиях по преобразованию данных в фабрике данных версии 2](../transform-data.md).

В этой статье объясняются действия преобразования данных в фабрике данных Azure, с помощью которых можно обрабатывать необработанные данные и преобразовывать их в прогнозы и аналитику. Действие преобразования выполняется в вычислительной среде, например в кластере Azure HDInsight или пакетной службе Azure. Статья содержит ссылки на статьи с подробными сведениями о каждом действии преобразования.

Фабрика данных поддерживает указанные ниже действия преобразования, которые вы можете добавлять в [конвейеры](data-factory-create-pipelines.md) как по отдельности, так и в цепочке с другим действием.

> [!NOTE]
> Пошаговое руководство см. в статье [Учебник. Создание первого конвейера для обработки данных с помощью кластера Hadoop](data-factory-build-your-first-pipeline.md).  
> 
> 

## <a name="hdinsight-hive-activity"></a>Действие Hive HDInsight
Действие Hive HDInsight в конвейере фабрики данных выполняет запросы Hive к вашему собственному кластеру HDInsight или кластеру HDInsight по запросу под управлением Windows или Linux. Дополнительные сведения об этом действии см. в разделе [Действие Hive](data-factory-hive-activity.md). 

## <a name="hdinsight-pig-activity"></a>Действие Pig HDInsight
Действие Pig HDInsight в конвейере фабрики данных выполняет запросы Pig к вашему собственному кластеру HDInsight или кластеру HDInsight по запросу под управлением Windows или Linux. Дополнительные сведения об этом действии см. в разделе [Действие Pig](data-factory-pig-activity.md). 

## <a name="hdinsight-mapreduce-activity"></a>Действие MapReduce HDInsight
Действие MapReduce HDInsight в конвейере фабрики данных выполняет программы MapReduce для вашего собственного кластера HDInsight или кластера HDInsight по запросу под управлением Windows или Linux. Дополнительные сведения об этом действии см. в разделе [Действие MapReduce](data-factory-map-reduce.md).

## <a name="hdinsight-streaming-activity"></a>Действие потоковой передачи HDInsight
Действие потоковой передачи HDInsight в конвейере фабрики данных выполняет программы потоковой передачи Hadoop для вашего собственного кластера HDInsight или кластера HDInsight по запросу под управлением Windows или Linux. Дополнительные сведения об этом действии см. в разделе [Потоковая активность Hadoop](data-factory-hadoop-streaming-activity.md).

## <a name="hdinsight-spark-activity"></a>Действие HDInsight Spark
Действие HDInsight Spark в конвейере фабрики данных выполняет программы Spark в вашем кластере HDInsight. Дополнительные сведения см. в разделе [Вызов программ Spark из фабрики данных](data-factory-spark.md). 

## <a name="machine-learning-activities"></a>Действия машинного обучения
Фабрика данных Azure позволяет легко создавать конвейеры, в которых для прогнозной аналитики используется опубликованная веб-служба Машинного обучения Azure. С помощью [действия выполнения пакета](data-factory-azure-ml-batch-execution-activity.md#invoking-a-web-service-using-batch-execution-activity) в конвейере фабрики данных Azure можно вызывать веб-службу Машинного обучения Azure, чтобы создавать прогнозы по данным в пакете.

Со временем прогнозные модели в оценивающих экспериментах машинного обучения потребуют повторного обучения с помощью новых входных наборов данных. Когда повторное обучение будет завершено, вам потребуется обновить веб-службу оценки на основании обновленной модели машинного обучения. Чтобы обновить веб-службу с помощью заново обученной модели, можно использовать [действие обновления ресурса Машинного обучения Azure](data-factory-azure-ml-batch-execution-activity.md#updating-models-using-update-resource-activity) .  

Дополнительные сведения об этих действиях машинного обучения см. в разделе [Создание прогнозных конвейеров с помощью действий машинного обучения Azure](data-factory-azure-ml-batch-execution-activity.md). 

## <a name="stored-procedure-activity"></a>Действие хранимой процедуры
C помощью действия хранимой процедуры SQL Server в конвейере фабрики данных можно вызвать хранимую процедуру из одного из следующих хранилищ данных: база данных SQL Azure, хранилище данных SQL Azure, база данных SQL Server вашего предприятия или виртуальная машина Azure. Дополнительные сведения см. в разделе [Действие хранимой процедуры](data-factory-stored-proc-activity.md).  

## <a name="data-lake-analytics-u-sql-activity"></a>Действие U-SQL Data Lake Analytics
Действие U-SQL Data Lake Analytics запускает сценарий U-SQL для кластера Azure Data Lake Analytics. Дополнительные сведения см. в разделе [Запуск скрипта U-SQL в аналитике озера данных Azure из фабрики данных Azure](data-factory-usql-activity.md). 

## <a name="net-custom-activity"></a>Настраиваемое действие .NET
Если вам нужно преобразовать данные способом, который не поддерживается фабрикой данных Azure, то можно создать настраиваемое действие с собственной логикой обработки данных и использовать это действие в конвейере. Можно настроить запуск настраиваемого действия .NET с помощью пакетной службы Azure или кластера HDInsight. Дополнительные сведения см. в разделе [Использование настраиваемых действий в конвейере фабрики данных Azure](data-factory-use-custom-activities.md). 

Можно создать настраиваемое действие для выполнения сценариев R в кластере HDInsight, где установлена среда R. Ознакомьтесь с примером в репозитории GitHub [Run R Script using Azure Data Factory](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample) (Запуск сценария R с помощью фабрики данных Azure). 

## <a name="compute-environments"></a>Вычислительные среды
Вы создаете связанную службу для среды вычислений, а затем используете эту службу при определении действия преобразования. Фабрика данных поддерживает вычислительные среды двух типов. 

1. **По требованию**: в этом случае вычислительная среда полностью управляется фабрикой данных. Среда автоматически создается службой фабрики данных перед отправкой задания для обработки данных и удаляется после его выполнения. Вы можете настраивать и изменять для вычислительной среды "по требованию" детализированные параметры выполнения задания, управления кластером и действий начальной загрузки. 
2. **Собственная**— в этом случае вы регистрируете собственную вычислительную среду (например, кластер HDInsight) и используете ее в качестве связанной службы в фабрике данных. Вы будете управлять средой вычислений, а служба фабрики данных — использовать ее для выполнения действий. 

В статье [Связанные службы вычислений](data-factory-compute-linked-services.md) описывается, какие службы вычислений поддерживает фабрика данных. 

## <a name="summary"></a>Сводка
Фабрика данных Azure поддерживает приведенные ниже действия преобразования данных и вычислительных сред для них. Действия преобразования можно добавлять в конвейеры как по отдельности, так и в цепочке с другим действием.

| Действия по преобразованию данных | Вычислительная среда |
|:--- |:--- |
| [Hive](data-factory-hive-activity.md) |HDInsight [Hadoop] |
| [Pig](data-factory-pig-activity.md) |HDInsight [Hadoop] |
| [MapReduce](data-factory-map-reduce.md) |HDInsight [Hadoop] |
| [Потоковая передача Hadoop](data-factory-hadoop-streaming-activity.md) |HDInsight [Hadoop] |
| [Действия машинного обучения: выполнение пакета и обновление ресурса](data-factory-azure-ml-batch-execution-activity.md) |Azure |
| [Хранимая процедура](data-factory-stored-proc-activity.md) |Azure SQL, хранилище данных Azure SQL или SQL Server |
| [Аналитика озера данных U-SQL](data-factory-usql-activity.md) |Аналитика озера данных Azure |
| [DotNet](data-factory-use-custom-activities.md) |HDInsight [Hadoop] или пакетная служба Azure |


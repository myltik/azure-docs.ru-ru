---
title: "Обработка данных с датчиков автомобилей с использованием Apache Storm в HDInsight | Документация Майкрософт"
description: "Узнайте, как обрабатывать данные с датчиков автомобилей из концентраторов событий с использованием средств Apache Storm в HDInsight. Добавление модели данных из Azure Cosmos DB и сохранение выходных данных в хранилище."
services: hdinsight,documentdb,notification-hubs
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 78980635-8bef-4c33-96c3-fae50e932e31
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 05/03/2017
ms.author: larryfr
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 8e8ebc724e1c70e8fcd56312adef5da2342373ea
ms.contentlocale: ru-ru
ms.lasthandoff: 05/10/2017


---
# <a name="process-vehicle-sensor-data-from-azure-event-hubs-using-apache-storm-on-hdinsight"></a>Обработка данных с датчиков автомобилей из концентраторов событий Azure с использованием средств Apache Storm в HDInsight.

Узнайте, как обрабатывать данные с датчиков автомобилей из концентраторов событий Azure с использованием средств Apache Storm в HDInsight. В этом примере производится считывание данных с датчиков из концентраторов событий Azure, которые дополняются сведениями из Azure Cosmos DB. Данные хранятся в службе хранилища Azure с использованием файловой системы Hadoop (HDFS).

![HDInsight и схема архитектуры Интернета вещей (IoT)](./media/hdinsight-storm-iot-eventhub-documentdb/iot.png)

## <a name="overview"></a>Обзор

Датчики на автомобилях помогают прогнозировать возникновение неполадок оборудования на основе тенденций в массивах статистических данных, а также совершенствовать последующие версии датчиков, руководствуясь результатами анализа закономерностей в их использовании. Необходима возможность быстро и эффективно загружать данные со всех автомобилей в систему Hadoop до начала их обработки средствами MapReduce. Кроме того, целесообразно реализовать анализ путей критических отказов (температуры в двигателе, неполадок тормозной системы и т. д.) в режиме реального времени.

Концентраторы событий Azure позволяют справляться с большими объемами данных, поступающих с датчиков. С помощью средств Apache Storm можно реализовать загрузку и обработку данных перед их сохранением в HDFS.

## <a name="solution"></a>Решение

Данные телеметрии, связанные с температурой двигателя и окружающей среды, и также со скоростью автомобиля, фиксируются датчиками и отправляются в концентраторы событий вместе с идентификационным номером автомобиля (VIN-кодом) и меткой времени. Топология Storm, работающая на платформе Apache Storm в кластере HDInsight, считывает эту информацию оттуда, обрабатывает ее и сохраняет в системе HDFS.

Во время обработки VIN-код используется для извлечения сведений о модели автомобиля из базы Cosmos DB. Они добавляются в поток данных перед его сохранением.

В топологии Storm используются перечисленные ниже компоненты.

* **EventHubSpout** : считывает данные из концентраторов событий Azure.
* **TypeConversionBolt** преобразует строку JSON, полученную из концентраторов событий, в кортеж, содержащий следующие данные датчиков:
    * engineTemperature
    * температура окружающей среды;
    * Speed
    * VIN
    * Timestamp
* **DataReferencBolt** ищет сведения о модели автомобиля в Cosmos DB на основе VIN-кода.
* **WasbStoreBolt** : сохраняет данные в HDFS (хранилище Azure).

Ниже представлена схема этого решения.

![топология Storm](./media/hdinsight-storm-iot-eventhub-documentdb/iottopology.png)

## <a name="implementation"></a>Реализация

Полное автоматизированное решение на базе этого сценария доступно в репозитории [HDInsight-Storm-Examples](https://github.com/hdinsight/hdinsight-storm-examples) на сайте GitHub. Чтобы воспользоваться этим примером, следуйте инструкциям в файле [IoTExample README.MD](https://github.com/hdinsight/hdinsight-storm-examples/blob/master/IotExample/README.md).

## <a name="next-steps"></a>Дальнейшие действия

Другие примеры топологий Storm см. в статье [Примеры топологий и компонентов Storm для Apache Storm в HDInsight](hdinsight-storm-example-topology.md).



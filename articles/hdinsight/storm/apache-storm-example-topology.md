---
title: Пример топологий Apache Storm в HDInsight | Документация Майкрософт
description: Список примеров топологий Storm, созданных и протестированных с помощью Apache Storm в HDInsight, включая базовые топологии на C# и Java, а также работа с концентраторами событий.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: f9b1bdff-5928-4705-a76d-52fd200917cb
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 02/21/2018
ms.author: larryfr
ms.openlocfilehash: f633797147b2090680ebb5ea11f60271addc7f52
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2018
---
# <a name="example-storm-topologies-and-components-for-apache-storm-on-hdinsight"></a>Примеры топологий и компонентов Storm для Apache Storm в HDInsight

Ниже приведен список примеров для Apache Storm, разработанных и созданных корпорацией Майкрософт в HDInsight. Эти примеры охватывают целый ряд тем: от создания простейших топологий на C# и Java до использования служб Azure, таких как концентраторы событий, Cosmos DB, база данных SQL, HBase в HDInsight и служба хранилища Azure. Некоторые примеры также демонстрируют использование средств, не относящихся к Azure, и даже технологий, не связанных с корпорацией Майкрософт, таких как SignalR и Socket.IO.

| ОПИСАНИЕ | Что демонстрирует | Язык или платформа |
|:--- |:--- |:--- |
| [Запись в хранилище озера данных Azure из Apache Storm](apache-storm-write-data-lake-store.md) |Запись в хранилище озера данных Azure |Java |
| [Источник воронки и сита концентратора событий](https://github.com/apache/storm/tree/master/external/storm-eventhubs) |Источник воронки и сита концентратора событий |Java |
| [Создание топологии Apache Storm на языке Java][5797064f] |Maven |Java |
| [Разработка топологий для Apache Storm в HDInsight на C# с помощью средств Hadoop для Visual Studio][16fce2d1] |Средства HDInsight для Visual Studio |C#, Java |
| [Обработка событий из службы концентраторов событий Azure с помощью Storm в HDInsight (C#)][844d1d81] |Концентраторы событий |C# и Java |
| [Обработка событий из службы концентраторов событий Azure с помощью Storm в HDInsight (Java)](https://azure.microsoft.com/resources/samples/hdinsight-java-storm-eventhub/) |Концентраторы событий |Java |
| [Обработка данных с датчиков автомобилей из концентраторов событий Azure с использованием средств Apache Storm в HDInsight][246ee964] |Концентраторы событий, Cosmos DB, Azure Storage Blob (WASB) |C#, Java |
| [Real Time ETL Example][b4b68194] (Пример ETL в режиме реального времени) |Концентраторы событий, HBase |C# |
| [Template C# Storm topology project for working with Azure services from Storm on HDInsight][ce0c02a2] (Шаблон проекта топологии на C# и Storm для работы со службами Azure из Storm в HDInsight) |Концентраторы событий, Cosmos DB, база данных SQL, HBase, SignalR |C#, Java |
| [EventHubs scalability example (Java & SCP.Net Hybrid)][d6c540e3] (Пример масштабируемости концентраторов событий (Java и SCP.Net Hybrid)) |Скорость обработки сообщений, концентраторы событий, база данных SQL |C#, Java |
| [Использование Python со Storm в HDInsight](apache-storm-develop-python-topology.md) |Компоненты Python с топологией Flux |Python |
| [Использование Kafka со Storm в HDInsight](../hdinsight-apache-storm-with-kafka.md) | Apache Storm: чтение и запись в Apache Kafka | Java |

### <a name="next-steps"></a>Дальнейшие действия

* [Начало работы с примерами Storm Starter для аналитики больших данных в HDInsight под управлением Linux][2b8c3488]
* [Развертывание топологий Apache Storm в HDInsight под управлением Windows и управление ими][6eb0d3b8]

[2b8c3488]:apache-storm-tutorial-get-started-linux.md "Начало работы с примерами Storm Starter для аналитики больших данных в HDInsight под управлением Linux."
[6eb0d3b8]:apache-storm-deploy-monitor-topology.md "Развертывание топологий Apache Storm в HDInsight под управлением Windows и управление ими."
[16fce2d1]:apache-storm-develop-csharp-visual-studio-topology.md "Разработка топологий для Apache Storm в HDInsight на C# с помощью средств Hadoop для Visual Studio."
[5797064f]:apache-storm-develop-java-topology.md "Разработка топологий Storm на языке Java с использованием Maven путем создания простой топологии для подсчета статистики."
[844d1d81]:apache-storm-develop-csharp-event-hub-topology.md "Считывание данных из концентраторов событий Azure и запись их туда с использованием Storm в HDInsight."
[246ee964]: https://github.com/hdinsight/hdinsight-storm-examples/blob/master/IotExample/README.md "Обработка данных с датчиков автомобилей из концентраторов событий Azure с использованием средств Apache Storm в HDInsight."
[d6c540e3]: https://github.com/hdinsight/hdinsight-storm-examples/blob/master/EventCountExample "Несколько топологий, демонстрирующих пропускную способность при считывании данных из концентраторов событий Azure и их записи в базу данных SQL с использованием средств Apache Storm в HDInsight."
[b4b68194]: https://github.com/hdinsight/hdinsight-storm-examples/blob/master/RealTimeETLExample "Real Time ETL Example" (Пример ETL в режиме реального времени).
[ce0c02a2]: https://github.com/hdinsight/hdinsight-storm-examples/tree/master/templates/HDInsightStormExamples "Этот проект содержит шаблоны воронок, сит и топологий, обеспечивающих взаимодействие с различными службами Azure, такими как концентраторы событий, Cosmos DB и база данных SQL."


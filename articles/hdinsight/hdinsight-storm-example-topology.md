---
title: "Пример топологий Apache Storm в HDInsight | Документация Майкрософт"
description: "Список примеров топологий Storm, созданных и протестированных с помощью Apache Storm в HDInsight, включая базовые топологии на C# и Java, а также работа с концентраторами событий."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: f9b1bdff-5928-4705-a76d-52fd200917cb
ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/09/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 46bc5b3b70120cd631523fd2b27ad8b9a47e3c6d
ms.openlocfilehash: 21c25e4579a440b13c3201ee8f6c4e1db7a2aa46


---
# <a name="example-storm-toplogies-and-components-for-apache-storm-on-hdinsight"></a>Примеры топологий и компонентов Storm для Apache Storm в HDInsight

Ниже приведен список примеров для Apache Storm, разработанных и созданных корпорацией Майкрософт в HDInsight. Эти примеры охватывают целый ряд тем: от создания простейших топологий на C# и Java до использования служб Azure, таких как концентраторы событий, DocumentDB, Power BI, база данных SQL, HBase в HDInsight и служба хранилища Azure. Некоторые примеры также демонстрируют использование средств, не относящихся к Azure, и даже технологий, не связанных с корпорацией Майкрософт, таких как SignalR и Socket.IO.

| Описание | Что демонстрирует | Язык или платформа |
|:--- |:--- |:--- |
| [Запись в хранилище озера данных Azure из Apache Storm](hdinsight-storm-write-data-lake-store.md) |Запись в хранилище озера данных Azure |Java |
| [Источник воронки и сита концентратора событий](https://github.com/apache/storm/tree/master/external/storm-eventhubs) |Источник воронки и сита концентратора событий |Java |
| [Разработка топологий на основе Java для базовых приложений подсчета слов с помощью Apache Storm и Maven в HDInsight][5797064f] |Maven |Java |
| [Разработка топологий для Apache Storm в HDInsight на C# с помощью средств Hadoop для Visual Studio][16fce2d1] |Средства HDInsight для Visual Studio |C#, Java |
| [Создание нескольких потоков данных в топологии Storm на C#][ec5a4064] |Несколько потоков |C# |
| [Определение популярных тем в Twitter с помощью Apache Storm в HDInsight][3c86c7c8] |Trident |Java, Trident |
| [Обработка событий из службы концентраторов событий Azure с помощью Storm в HDInsight (C#)][844d1d81] |Концентраторы событий |C# и Java |
| [Обработка событий из службы концентраторов событий Azure с помощью Storm в HDInsight (Java)](hdinsight-storm-develop-java-event-hub-topology.md) |Концентраторы событий |Java |
| [Использование средств Power BI для визуализации данных из топологии Apache Storm][94d15238] |Power BI |C# |
| [Анализ полученных с датчиков данных с использованием Apache Storm, концентратора событий и базы данных HBase в службе HDInsight (Hadoop)][ab894747] |Концентраторы событий, HBase, Socket.IO, веб-панель мониторинга |C#, Java, JavaScript, HTML |
| [Обработка данных с датчиков автомобилей из концентраторов событий Azure с использованием средств Apache Storm в HDInsight][246ee964] |Концентраторы событий, DocumentDb, хранилище больших двоичных объектов Azure (WASB) |C#, Java |
| [Извлечение, преобразование и загрузка данных из концентраторов событий Azure в HBase с использованием Storm в HDInsight][b4b68194] |Концентраторы событий, HBase |C# |
| [Шаблон проекта топологии на C# и Storm для работы со службами Azure из Storm в HDInsight][ce0c02a2] |Концентраторы событий, DocumentDb, база данных SQL, HBase, SignalR |C#, Java |
| [Тестирование масштабируемости для считывания данных из концентраторов событий Azure с использованием Storm в HDInsight][d6c540e3] |Скорость обработки сообщений, концентраторы событий, база данных SQL |C#, Java |
| [Сопоставление событий с помощью Storm и HBase в HDInsight](hdinsight-storm-correlation-topology.md) |HBase |C# |
| [Использование Python со Storm в HDInsight](hdinsight-storm-develop-python-topology.md) |Компоненты Python с топологиями Storm на основе Java и Clojure |Python |

## <a name="next-steps"></a>Дальнейшие действия

* [Учебник по Apache Storm в HDInsight: начало работы с анализом больших объемов данных в HDInsight с помощью примеров Storm Starter][2b8c3488]
* [Развертывание топологий Apache Storm в HDInsight под управлением Windows и управление ими][6eb0d3b8]

[2b8c3488]: hdinsight-apache-storm-tutorial-get-started-linux.md "Создание среды Storm в кластере HDInsight и развертывание образцов топологий с использованием панели мониторинга Storm."
[6eb0d3b8]: hdinsight-storm-deploy-monitor-topology.md "Развертывание топологий и управление ими с использованием веб-панели мониторинга и пользовательского интерфейса Storm или средств HDInsight для Visual Studio."
[16fce2d1]: hdinsight-storm-develop-csharp-visual-studio-topology.md "Создание топологии Storm на языке C# с помощью средств HDInsight для Visual Studio."
[5797064f]: hdinsight-storm-develop-java-topology.md "Разработка топологий Storm на языке Java с использованием Maven путем создания простой топологии для подсчета статистики."
[94d15238]: hdinsight-storm-power-bi-topology.md "Демонстрация возможности записи данных в Power BI из топологии C# с последующим созданием на их основе диаграммы и панели мониторинга."
[ec5a4064]: https://github.com/Blackmist/csharp-storm-example "Демонстрация реализованной на языке C# простой топологии Storm для подсчета статистики. Этот пример также демонстрирует возможность создания нескольких потоков данных в топологии C#."
[844d1d81]: hdinsight-storm-develop-csharp-event-hub-topology.md "Считывание данных из концентраторов событий Azure и запись их туда с использованием Storm в HDInsight."
[ab894747]: hdinsight-storm-sensor-data-analysis.md "Использование средств Apache Storm в HDInsight для обработки данных с датчиков из концентраторов событий Azure, их визуализации с помощью D3.js и (необязательно) записи этих сведений в HBase."
[3c86c7c8]: hdinsight-storm-twitter-trending.md "Использование средств Trident для создания топологии Storm, которая определяет популярные темы в Twitter по хэш-тегам."
[246ee964]: hdinsight-storm-iot-eventhub-documentdb.md "Использование топологии Storm для считывания сообщений из концентраторов событий Azure, считывании документов из Azure DocumentDB для ссылок на данные и сохранение данных в хранилище Azure."
[d6c540e3]: https://github.com/hdinsight/hdinsight-storm-examples/blob/master/EventCountExample "Несколько топологий, демонстрирующих пропускную способность при считывании данных из концентраторов событий Azure и их записи в базу данных SQL с использованием средств Apache Storm в HDInsight."
[b4b68194]: https://github.com/hdinsight/hdinsight-storm-examples/blob/master/RealTimeETLExample "Считывание данных из концентраторов событий Azure, их обобщение и преобразование с последующим сохранением в базу HBase в HDInsight."
[ce0c02a2]: https://github.com/hdinsight/hdinsight-storm-examples/tree/master/templates/HDInsightStormExamples "Этот проект содержит шаблоны воронок, сит и топологий, обеспечивающих взаимодействие с различными службами Azure, такими как концентраторы событий, DocumentDB и база данных SQL."




<!--HONumber=Nov16_HO3-->



---
title: Log Analytics для Apache Kafka в Azure HDInsight | Документация Майкрософт
description: Сведения о том, как использовать Log Analytics для анализа журналов из кластера Apache Kafka в Azure HDInsight.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: ''
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 05/01/2018
ms.author: larryfr
ms.openlocfilehash: 9f366631ced4392831ad9ed97898a88b3290cd22
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/03/2018
ms.locfileid: "32772268"
---
# <a name="analyze-logs-for-apache-kafka-on-hdinsight"></a>Анализ журналов для Apache Kafka в HDInsight

Сведения о том, как использовать Log Analytics для анализа журналов, созданных в Apache Kafka в Azure HDInsight.

## <a name="enable-log-analytics-for-kafka"></a>Включение Log Analytics для Kafka

Инструкции по включению Log Analytics для HDInsight идентичны для всех кластеров HDInsight. Чтобы узнать, как создать и настроить необходимые службы, воспользуйтесь ссылками ниже.

1. Создание рабочей области Log Analytics. Дополнительные сведения см. в документе [Начало работы с рабочей областью Log Analytics](https://docs.microsoft.com/azure/log-analytics).

2. Создание Kafka в кластере HDInsight. Дополнительные сведения см. в документе [Приступая к работе с Apache Kafka (предварительная версия) в HDInsight](apache-kafka-get-started.md).

3. Настройка кластера Kafka для использования Log Analytics. Дополнительные сведения см. в статье об [использовании Log Analytics для мониторинга HDInsight](../hdinsight-hadoop-oms-log-analytics-tutorial.md).

    > [!NOTE]
    > Кроме того, можно настроить кластер для использования Log Analytics с помощью командлета `Enable-AzureRmHDInsightOperationsManagementSuite`. Для использования этого командлета требуется указанная ниже информация.
    >
    > * Имя кластера HDInsight.
    > * Идентификатор рабочей области для Log Analytics. Идентификатор рабочей области можно найти в рабочей области Log Analytics.
    > * Первичный ключ для подключения к Log Analytics. Чтобы определить первичный ключ, выберите свой экземпляр Log Analytics, а затем перейдите на __портал OMS__. На портале OMS выберите __Параметры__, __Подключенные источники__, а затем — __Серверы Linux__.


> [!IMPORTANT]
> Подготовка данных для Log Analytics может занять около 20 минут.

## <a name="query-logs"></a>Журналы запросов

1. На [портале Azure](https://portal.azure.com) выберите рабочую область Log Analytics.

2. Выберите __Поиск по журналам__. Здесь можно выполнять поиск данных, собранных из Kafka. Ниже приведены некоторые примеры поисковых запросов:

    * Использование дискового пространства: `Perf | where ObjectName == "Logical Disk" and CounterName == "Free Megabytes" and InstanceName == "_Total" and ((Computer startswith_cs "hn" and Computer contains_cs "-") or (Computer startswith_cs "wn" and Computer contains_cs "-")) | summarize AggregatedValue = avg(CounterValue) by Computer, bin(TimeGenerated, 1h)`

    * Загрузка ЦП: `Perf | where CounterName == "% Processor Time" and InstanceName == "_Total" and ((Computer startswith_cs "hn" and Computer contains_cs "-") or (Computer startswith_cs "wn" and Computer contains_cs "-")) | summarize AggregatedValue = avg(CounterValue) by Computer, bin(TimeGenerated, 1h)`

    * Входящих сообщений в секунду: `metrics_kafka_CL | where ClusterName_s == "your_kafka_cluster_name" and InstanceName_s == "kafka-BrokerTopicMetrics-MessagesInPerSec-Count" | summarize AggregatedValue = avg(kafka_BrokerTopicMetrics_MessagesInPerSec_Count_value_d) by HostName_s, bin(TimeGenerated, 1h)`

    * Входящих байт в секунду: `metrics_kafka_CL | where HostName_s == "wn0-kafka" and InstanceName_s == "kafka-BrokerTopicMetrics-BytesInPerSec-Count" | summarize AggregatedValue = avg(kafka_BrokerTopicMetrics_BytesInPerSec_Count_value_d) by bin(TimeGenerated, 1h)`

    * Исходящих байт в секунду: `metrics_kafka_CL | where ClusterName_s == "your_kafka_cluster_name" and InstanceName_s == "kafka-BrokerTopicMetrics-BytesOutPerSec-Count" | summarize AggregatedValue = avg(kafka_BrokerTopicMetrics_BytesOutPerSec_Count_value_d) by bin(TimeGenerated, 1h)`

    > [!IMPORTANT]
    > Замените значения запроса своими сведениями об определенном кластере. Например, для параметра `ClusterName_s` укажите имя кластера. `HostName_s` должно быть присвоено доменное имя рабочего узла в кластере.

    Кроме того, вы можете ввести `*` для поиска всех типов данных журнала. В настоящее время для запросов доступны следующие журналы:

    | Тип журнала | ОПИСАНИЕ |
    | ---- | ---- |
    | log\_kafkaserver\_CL | Брокер Kafka, server.log |
    | log\_kafkacontroller\_CL | Брокер Kafka, controller.log |
    | metrics\_kafka\_CL | Метрики Kafka JMX |

    ![Окно поиска сведений об использовании ЦП](./media/apache-kafka-log-analytics-operations-management/kafka-cpu-usage.png)
 
 ## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о Log Analytics см. в документе [Начало работы с рабочей областью Log Analytics](../../log-analytics/log-analytics-get-started.md).

Дополнительные сведения о работе с Kafka см. в следующих документах:

 * [Зеркальное отображение Kafka в кластерах HDInsight](apache-kafka-mirroring.md).
 * [Повышение уровня масштабируемости Kafka в HDInsight](apache-kafka-scalability.md).
 * [Использование потоковой передачи Spark с Kafka](../hdinsight-apache-spark-with-kafka.md).
 * [Использование структурированной потоковой передачи Spark с Kafka](../hdinsight-apache-kafka-spark-structured-streaming.md).

---
title: Интеграция Apache Spark с концентраторами событий Azure | Документация Майкрософт
description: Интеграция с Apache Spark для обеспечения структурированной потоковой передачи с помощью концентраторов событий
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.service: event-hubs
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/05/2018
ms.author: shvija
ms.openlocfilehash: 2dcf390b80c119ab21948b982c0812395e45bc01
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/12/2018
---
# <a name="integrating-apache-spark-with-azure-event-hubs"></a>Интеграция Apache Spark с концентраторами событий Azure

Концентраторы событий Azure легко интегрируются с [Apache Spark](https://spark.apache.org/), что упрощает создание _комплексных_ распределенных приложений потоковой передачи. Такая интеграция поддерживается для [Spark Core](http://spark.apache.org/docs/latest/rdd-programming-guide.html), [Spark Streaming](http://spark.apache.org/docs/latest/streaming-programming-guide.html) и [Structured Streaming](https://spark.apache.org/docs/latest/structured-streaming-programming-guide.html). Соединитель концентратора событий для Apache Spark можно найти на [GitHub](https://github.com/Azure/azure-event-hubs-spark). Эту библиотеку также можно использовать в проектах Maven из [центрального репозитория Maven](http://search.maven.org/#artifactdetails%7Ccom.microsoft.azure%7Cazure-eventhubs-spark_2.11%7C2.1.6%7C).

Из этой статьи вы узнаете, как создать непрерывно работающее приложение в [Azure Databricks](https://azure.microsoft.com/services/databricks/). В этой статье используется [Azure Databricks](https://azure.microsoft.com/services/databricks/), но кластеры Spark также доступны с [HDInsight](https://docs.microsoft.com/azure/hdinsight/spark/apache-spark-overview).

В примере ниже используется две записные книжки Scala: одна для потоковой передачи событий из концентратора событий, а другая для отправки событий в него.

## <a name="prerequisites"></a>предварительным требованиям

* Подписка Azure. Если у вас нет подписки, [создайте бесплатную учетную запись](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
* Экземпляр концентратора событий. Если у вас нет экземпляра, [создайте его](https://docs.microsoft.com/azure/event-hubs/event-hubs-create).
* Экземпляр [Azure Databricks](https://azure.microsoft.com/services/databricks/). Если у вас нет экземпляра, [создайте его](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal).
* [Создайте библиотеку, используя такие координаты maven](https://docs.databricks.com/user-guide/libraries.html#upload-a-maven-package-or-spark-package): `com.microsoft.azure:azure‐eventhubs‐spark_2.11:2.3.0`

Чтобы выполнять потоковую передачу событий из концентратора событий, используйте приведенный ниже фрагмент кода в своей записной книжке.

```scala
import org.apache.spark.eventhubs._

// To connect to an Event Hub, EntityPath is required as part of the connection string.
// Here, we assume that the connection string from the Azure portal does not have the EntityPath part.
val connectionString = ConnectionStringBuilder("{EVENT HUB CONNECTION STRING FROM AZURE PORTAL}")
  .setEventHubName("{EVENT HUB NAME}")
  .build 
val ehConf = EventHubsConf(connectionString)
  .setStartingPosition(EventPosition.fromEndOfStream)

// Create a stream that reads data from the specified Event Hub.
val reader = spark.readStream
  .format("eventhubs")
  .options(ehConf.toMap)
  .load()

// Select the body column and cast it to a string.
val eventhubs = reader
  .select("CAST (body AS STRING)")
  .as[String]
```
Приведенный ниже фрагмент кода ниже используется для отправки событий в концентратор событий с помощью API пакета Spark. Кроме того, для отправки событий в концентратор событий можно написать запрос потоковой передачи.

```scala
import org.apache.spark.eventhubs._
import org.apache.spark.sql.functions._

// To connect to an Event Hub, EntityPath is required as part of the connection string.
// Here, we assume that the connection string from the Azure portal does not have the EntityPath part.
val connectionString = ConnectionStringBuilder("{EVENT HUB CONNECTION STRING FROM AZURE PORTAL}")
  .setEventHubName("{EVENT HUB NAME}")
  .build

val eventHubsConf = EventHubsConf(connectionString)

// Create a column representing the partitionKey.
val partitionKeyColumn = (col("id") % 5).cast("string").as("partitionKey")
// Create random strings as the body of the message.
val bodyColumn = concat(lit("random nunmber: "), rand()).as("body")

// Write 200 rows to the specified Event Hub.
val df = spark.range(200).select(partitionKeyColumn, bodyColumn)
df.write
  .format("eventhubs")
  .options(eventHubsConf.toMap)
  .save() 

```

В этой статье мы кратко объяснили, как работает соединитель концентратора событий для создания отказоустойчивых решений потоковой передачи, работающих в режиме реального времени. Узнайте больше о Structured Streaming и интегрируемом соединителе концентратора событий в разделе дополнительной информации.

## <a name="next-steps"></a>Дополнительная информация

* [Structured Streaming + Azure Event Hubs Integration Guide](https://github.com/Azure/azure-event-hubs-spark/blob/master/docs/structured-streaming-eventhubs-integration.md) (Руководство по интеграции Structured Streaming со службой Azure "Концентраторы событий")
* [Spark Streaming + Event Hubs Integration Guide](https://github.com/Azure/azure-event-hubs-spark/blob/master/docs/spark-streaming-eventhubs-integration.md) (Руководство по интеграции Spark Streaming со службой "Концентраторы событий")

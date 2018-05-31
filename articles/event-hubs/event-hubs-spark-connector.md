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
ms.openlocfilehash: 09790852d28f2f68e854b37256ca1c8edba992e6
ms.sourcegitcommit: 6e43006c88d5e1b9461e65a73b8888340077e8a2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/01/2018
ms.locfileid: "32311300"
---
# <a name="integrating-apache-spark-with-azure-event-hubs"></a>Интеграция Apache Spark с концентраторами событий Azure

Концентраторы событий Azure плавно интегрируются с [Apache Spark](https://spark.apache.org/) для создания распределенных потоковых приложений. Такая интеграция поддерживается для [Spark Core](http://spark.apache.org/docs/latest/rdd-programming-guide.html), [Spark Streaming](http://spark.apache.org/docs/latest/streaming-programming-guide.html) и [Structured Streaming](https://spark.apache.org/docs/latest/structured-streaming-programming-guide.html). Соединитель службы "Концентраторы событий" для Apache Spark можно найти на [GitHub](https://github.com/Azure/azure-event-hubs-spark). Эту библиотеку также можно использовать в проектах Maven из [центрального репозитория Maven](http://search.maven.org/#artifactdetails%7Ccom.microsoft.azure%7Cazure-eventhubs-spark_2.11%7C2.1.6%7C).

В этой статье показано, как создать непрерывное приложение в [Azure Databricks](https://azure.microsoft.com/services/databricks/). Хотя в этой статье используется [Azure Databricks](https://azure.microsoft.com/services/databricks/), кластеры Spark также доступны с [HDInsight](../hdinsight/spark/apache-spark-overview.md).

В примере ниже используются две записные книжки Scala: одна для потоковой передачи событий из концентратора событий, а другая для отправки событий в него.

## <a name="prerequisites"></a>предварительным требованиям

* Подписка Azure. Если у вас нет подписки, [создайте бесплатную учетную запись](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
* Экземпляр концентратора событий. Если у вас нет экземпляра, [создайте его](event-hubs-create.md).
* Экземпляр [Azure Databricks](https://azure.microsoft.com/services/databricks/). Если у вас нет экземпляра, [создайте его](../azure-databricks/quickstart-create-databricks-workspace-portal.md).
* [Создайте библиотеку, используя такие координаты maven](https://docs.databricks.com/user-guide/libraries.html#upload-a-maven-package-or-spark-package): `com.microsoft.azure:azure‐eventhubs‐spark_2.11:2.3.1`

Потоковая передача событий из концентратора событий использует следующий код:

```scala
import org.apache.spark.eventhubs._

// To connect to an event hub, EntityPath is required as part of the connection string.
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
val eventhubs = reader.select($"body" cast "string")

eventhubs.writeStream
  .format("console")
  .outputMode("append")
  .start()
  .awaitTermination()
```
В этом примере кода события отправляются в концентратор событий с использованием API пакета Spark. Кроме того, для отправки событий в концентратор событий можно написать запрос потоковой передачи.

```scala
import org.apache.spark.eventhubs._
import org.apache.spark.sql.functions._

// To connect to an event hub, EntityPath is required as part of the connection string.
// Here, we assume that the connection string from the Azure portal does not have the EntityPath part.
val connectionString = ConnectionStringBuilder("{EVENT HUB CONNECTION STRING FROM AZURE PORTAL}")
  .setEventHubName("{EVENT HUB NAME}")
  .build
val ehConf = EventHubsConf(connectionString)

// Create random strings as the body of the message.
val bodyColumn = concat(lit("random nunmber: "), rand()).as("body")

// Write 200 rows to the specified event hub.
val df = spark.range(200).select(bodyColumn)
df.write
  .format("eventhubs")
  .options(eventHubsConf.toMap)
  .save() 
```

## <a name="next-steps"></a>Дополнительная информация

Теперь вы знаете, как настроить масштабируемый и отказоустойчивый поток с помощью соединителя концентраторов событий для Apache Spark. Дополнительные сведения об использовании концентраторов событий со структурированной потоковой передачей и потоковой передачей Spark см. по следующим ссылкам:

* [Structured Streaming + Azure Event Hubs Integration Guide](https://github.com/Azure/azure-event-hubs-spark/blob/master/docs/structured-streaming-eventhubs-integration.md) (Руководство по интеграции Structured Streaming со службой Azure "Концентраторы событий")
* [Spark Streaming + Event Hubs Integration Guide](https://github.com/Azure/azure-event-hubs-spark/blob/master/docs/spark-streaming-eventhubs-integration.md) (Руководство по интеграции Spark Streaming со службой "Концентраторы событий")

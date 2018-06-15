---
title: Лямбда-архитектура с использованием Azure Cosmos DB и HDInsight (Apache Spark) | Документация Майкрософт
description: В этой статье описывается, как реализовать лямбда-архитектуру, используя Azure Cosmos DB, HDInsight и Spark.
keywords: Лямбда-архитектура
services: cosmos-db
author: dennyglee
manager: kfile
editor: ''
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 01/19/2018
ms.author: denlee
ms.openlocfilehash: a8cdee6e8e7c338be278449a31426824f6e805d6
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34611863"
---
# <a name="azure-cosmos-db-implement-a-lambda-architecture-on-the-azure-platform"></a>Azure Cosmos DB: реализация лямбда-архитектуры на платформе Azure 

Лямбда-архитектура обеспечивает эффективную обработку больших наборов данных. Это предполагает использование уровней пакетной, потоковой обработки и обслуживания, что сводит к минимуму задержки, связанные со скоростью выполнения запросов на большие данные. 

Чтобы реализовать такую архитектуру в Azure, вы можете объединить следующие технологии и ускорить анализ больших данных в реальном времени:
* [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) — это первая в отрасли глобально распределенная многомодельная служба базы данных. 
* [Apache Spark для Azure HDInsight](https://azure.microsoft.com/services/hdinsight/apache-spark/) — платформа обработки, на которой работают крупномасштабные приложения анализа данных.
* [Канал изменений](change-feed.md) Azure Cosmos DB передает поток новых данных на пакетный уровень для обработки в HDInsight.
* [Соединитель Spark для Azure Cosmos DB](spark-connector.md).

В этой статье описываются компоненты лямбда-архитектуры, построенной на основе исходной многоуровневой структуры, и преимущества такой архитектуры после изменений, упрощающих работу.  

Обзор лямбда-архитектуры и ресурсов, доступных в ее образце, см. в следующем видео:

> [!VIDEO https:///channel9.msdn.com/Events/Connect/2017/T135/player]
>

## <a name="what-is-a-lambda-architecture"></a>Что такое лямбда-архитектура
Лямбда-архитектура — это универсальная масштабируемая отказоустойчивая архитектура обработки данных, предназначенная для пакетных сценариев и сценариев сокращения задержек, как описал [Натан Марз](https://twitter.com/nathanmarz) (Nathan Marz).

![Схема лямбда-архитектуры](./media/lambda-architecture/lambda-architecture-intro.png)

Источник: http://lambda-architecture.net/

Основные компоненты лямбда-архитектуры изображены на предыдущей схеме, согласно источнику [https://lambda-architecture.net](http://lambda-architecture.net/).

 1. Все **данные** отправляются *и* на *пакетный уровень*, и на *уровень ускорения*.
 2. **Пакетный уровень** предполагает набор основных данных (неизменяемый набор необработанных данных только для добавления) и предварительно вычисляет пакетные представления.
 3. **Уровень обслуживания** предусматривает представления пакетов для быстрой отправки запросов. 
 4. На **уровне ускорения** происходит компенсация времени обработки (на уровне обслуживания) и используются только последние данные.
 5. Вы можете объединить результаты пакетных представлений и представлений в режиме реального времени, чтобы получить ответы на все запросы, или проверить их по отдельности.

Дочитав статью, вы узнаете, как реализовать эту архитектуру, используя только следующее:

* коллекции Azure Cosmos DB;
* кластер HDInsight (Apache Spark 2.1);
* соединитель Spark [1.0](https://github.com/Azure/azure-cosmosdb-spark/tree/master/releases/azure-cosmosdb-spark_2.1.0_2.11-1.0.0).

## <a name="speed-layer"></a>Уровень ускорения

С точки зрения необходимых операций передача двух потоков данных и поддержка правильного состояния данных могут быть сложной задачей. Для упрощения работы используйте [поддержку канала изменений Azure Cosmos DB](change-feed.md), чтобы сохранять состояние на *пакетном уровне*, а также журнал изменений Azure Cosmos DB через *API канала изменений* для *уровня ускорения*.  
![Схема, на которой изображены новые данные, уровень ускорения и набор основных данных как часть лямбда-архитектуры](./media/lambda-architecture/lambda-architecture-change-feed.png)

На этих уровнях есть такие особенности:

 1. Все **данные** отправляются *только* в Azure Cosmos DB, что исключает проблемы многократного приведения.
 2. **Пакетный уровень** предполагает набор основных данных (неизменяемый набор необработанных данных только для добавления) и предварительно вычисляет пакетные представления.
 3. **Уровень обслуживания** рассматривается в следующем разделе.
 4. На **уровне ускорения**  HDInsight (Apache Spark) считывает канал изменений Azure Cosmos DB. Это позволяет сохранить данные, а также запрашивать и одновременно обрабатывать их.
 5. Вы можете объединить результаты пакетных представлений и представлений в режиме реального времени, чтобы получить ответы на все запросы, или проверить их по отдельности.
 
### <a name="code-example-spark-structured-streaming-to-an-azure-cosmos-db-change-feed"></a>Пример кода: структурированная потоковая передача Spark в канал изменений Azure Cosmos DB
При запуске упрощенного прототипа канала изменений Azure Cosmos DB на **уровне ускорения** его можно проверить с использованием данных Twitter как части примера [Stream Processing Changes using Azure Cosmos DB Change Feed and Apache Spark](https://github.com/Azure/azure-cosmosdb-spark/wiki/Stream-Processing-Changes-using-Azure-Cosmos-DB-Change-Feed-and-Apache-Spark) (Потоковая обработка изменений с использованием канала изменений Azure Cosmos DB и Apache Spark). Дополнительные сведения о том, как начать использовать выходные данные Twitter, см. в примере кода [Stream feed from Twitter to Cosmos DB](https://github.com/tknandu/TwitterCosmosDBFeed) (Потоковая передача по каналу из Twitter в Cosmos DB). В примере выше вам нужно загрузить данные Twitter в Azure Cosmos DB, чтобы можно было настроить кластер HDInsight (Apache Spark) для подключения к каналу изменений. Дополнительные сведения о том, как настроить такую конфигурацию, см. в разделе [Apache Spark to Azure Cosmos DB Connector Setup](https://github.com/Azure/azure-cosmosdb-spark/wiki/Spark-to-Cosmos-DB-Connector-Setup) (Настройка соединителя Apache Spark для Azure Cosmos DB).  

В следующем фрагменте кода показано, как настроить `spark-shell` для выполнения задания структурированной потоковой передачи и подключения к каналу изменений Azure Cosmos DB, где в реальном времени отображается поток данных Twitter, чтобы подсчитывать число интервалов выполнения.

```
// Import Libraries
import com.microsoft.azure.cosmosdb.spark._
import com.microsoft.azure.cosmosdb.spark.schema._
import com.microsoft.azure.cosmosdb.spark.config.Config
import org.codehaus.jackson.map.ObjectMapper
import com.microsoft.azure.cosmosdb.spark.streaming._
import java.time._


// Configure connection to Azure Cosmos DB Change Feed
val sourceConfigMap = Map(
"Endpoint" -> "[COSMOSDB ENDPOINT]",
"Masterkey" -> "[MASTER KEY]",
"Database" -> "[DATABASE]",
"Collection" -> "[COLLECTION]",
"ConnectionMode" -> "Gateway",
"ChangeFeedCheckpointLocation" -> "checkpointlocation",
"changefeedqueryname" -> "Streaming Query from Cosmos DB Change Feed Interval Count")

// Start reading change feed as a stream
var streamData = spark.readStream.format(classOf[CosmosDBSourceProvider].getName).options(sourceConfigMap).load()

// Start streaming query to console sink
val query = streamData.withColumn("countcol", streamData.col("id").substr(0, 0)).groupBy("countcol").count().writeStream.outputMode("complete").format("console").start()
```

Целые примеры кода см. по адресу [azure-cosmosdb-spark/lambda/samples](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples/lambda), в том числе:
* [Streaming Query from Cosmos DB Change Feed.scala](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Streaming%20Query%20from%20Cosmos%20DB%20Change%20Feed.scala);
* [Streaming Tags Query from Cosmos DB Change Feed.scala](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Streaming%20Tags%20Query%20from%20Cosmos%20DB%20Change%20Feed%20.scala).

В результате вы получите консоль `spark-shell`, которая постоянно выполняет задание структурированной потоковой передачи, подсчитывающее число интервалов получения данных Twitter из канала изменений Azure Cosmos DB. На рисунке ниже показаны выходные данные задания потоковой передачи и количество интервалов.

![Результат потоковой передачи с количеством интервалов получения данных Twitter из канала изменений Azure Cosmos DB](./media/lambda-architecture/lambda-architecture-speed-layer-twitter-count.png) 

Дополнительные сведения об этом канале см. в ресурсах:

* [Работа с поддержкой веб-канала изменений в Azure Cosmos DB](change-feed.md)
* [Введение в библиотеку обработчика канала изменений Azure Cosmos DB](https://azure.microsoft.com/blog/introducing-the-azure-cosmosdb-change-feed-processor-library/)
* [Stream Processing Changes: Azure CosmosDB change feed + Apache Spark](https://azure.microsoft.com/blog/stream-processing-changes-azure-cosmosdb-change-feed-apache-spark/) (Изменения в потоковой передаче. Канал изменений Azure Cosmos DB и Apache Spark)

## <a name="batch-and-serving-layers"></a>Пакетный уровень и уровень обслуживания
Новые данные загружаются в Azure Cosmos DB (где используется канал изменений для уровня ускорения), поэтому здесь находится **основной набор данных** (неизменяемый набор необработанных данных только для добавления). С этого этапа используйте HDInsight (Apache Spark), чтобы выполнять функции предварительных вычислений на **пакетном уровне** для **уровня обслуживания**, как показано на рисунке ниже:

![Схема, на которой выделены пакетный уровень и уровень обслуживания в лямбда-архитектуре](./media/lambda-architecture/lambda-architecture-batch-serve.png)

На этих уровнях есть такие особенности:

 1. Все **данные** отправляются в Azure Cosmos DB, чтобы исключить проблемы многократного приведения.
 2. **Пакетный уровень** предполагает набор основных данных (неизменяемый набор необработанных данных только для добавления), хранимый в Azure Cosmos DB. С помощью HDI Spark вы можете предварительно вычислить агрегаты, которые следует хранить в вычисляемых пакетных представлениях.
 3. **Уровень обслуживания** представляет собой базу данных Azure Cosmos DB с коллекциями для набора основных данных и вычисленных пакетных представлений.
 4. **Уровень ускорения** рассматривается дальше в этой статье.
 5. Вы можете объединить результаты пакетных представлений и представлений в режиме реального времени, чтобы получить ответы на все запросы, или же проверить их по отдельности.

### <a name="code-example-pre-computing-batch-views"></a>Пример кода: предварительное вычисление пакетных представлений
Чтобы продемонстрировать, как предварительно вычислять представления, используя ваш **набор основных данных**, из Apache Spark для Azure Cosmos DB, используйте следующие фрагменты кода из записных книжек [Lambda Architecture Rearchitected - Batch Layer](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20Layer.ipynb) (Измененная лямбда-архитектура. Пакетный уровень) и [Lambda Architecture Rearchitected - Batch to Serving Layer](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20to%20Serving%20Layer.ipynb) (Измененная лямбда-архитектура. Пакетный уровень — уровень обслуживания). В этом случае используйте данные Twitter, хранящиеся в Azure Cosmos DB.

Начнем с создания подключения к данным Twitter в Azure Cosmos DB, используя код PySpark ниже.

```
# Configuration to connect to Azure Cosmos DB
tweetsConfig = {
  "Endpoint" : "[Endpoint URL]",
  "Masterkey" : "[Master Key]",
  "Database" : "[Database]",
  "Collection" : "[Collection]", 
  "preferredRegions" : "[Preferred Regions]",
  "SamplingRatio" : "1.0",
  "schema_samplesize" : "200000",
  "query_custom" : "[Cosmos DB SQL Query]"
}

# Create DataFrame
tweets = spark.read.format("com.microsoft.azure.cosmosdb.spark").options(**tweetsConfig).load()

# Create Temp View (to run Spark SQL statements)
tweets.createOrReplaceTempView("tweets")
```

Далее запустим следующую инструкцию SQL, чтобы определить первые 10 хэштегов из набора твитов. SQL-запрос Spark мы запустим в записной книжке Jupyter без формирования линейчатой диаграммы, показанной сразу после этого фрагмента кода.

```
%%sql
select hashtags.text, count(distinct id) as tweets
from (
  select 
    explode(hashtags) as hashtags,
    id
  from tweets
) a
group by hashtags.text
order by tweets desc
limit 10
```

![График, на котором показано количество твитов на хэштег](./media/lambda-architecture/lambda-architecture-batch-hashtags-bar-chart.png)

Теперь, когда есть запрос, сохраним его в коллекции, используя соединитель Spark, чтобы поместить выходные данные в другую коллекцию.  В этом примере для демонстрации подключения используется Scala. Как в предыдущем примере, создайте подключение в конфигурации, так чтобы сохранять таблицу данных Apache Spark в другой коллекции Azure Cosmos DB.

```
val writeConfigMap = Map(
    "Endpoint" -> "[Endpoint URL]",
    "Masterkey" -> "[Master Key]",
    "Database" -> "[Database]",
    "Collection" -> "[New Collection]", 
    "preferredRegions" -> "[Preferred Regions]",
    "SamplingRatio" -> "1.0",
    "schema_samplesize" -> "200000"
)

// Configuration to write
val writeConfig = Config(writeConfigMap)

```

Определите `SaveMode` (указав действие с документами: `Overwrite` или `Append`) и создайте таблицу данных `tweets_bytags`, аналогичную SQL-запросу Spark в предыдущем примере.  После создания таблицы `tweets_bytags` ее можно сохранить, используя метод `write` и заранее указав `writeConfig`.

```
// Import SaveMode so you can Overwrite, Append, ErrorIfExists, Ignore
import org.apache.spark.sql.{Row, SaveMode, SparkSession}

// Create new DataFrame of tweets tags
val tweets_bytags = spark.sql("select hashtags.text as hashtags, count(distinct id) as tweets from ( select explode(hashtags) as hashtags, id from tweets ) a group by hashtags.text order by tweets desc")

// Save to Cosmos DB (using Append in this case)
tweets_bytags.write.mode(SaveMode.Overwrite).cosmosDB(writeConfig)
```

Последняя инструкция теперь сохранила таблицу данных Spark в новой коллекции Azure Cosmos DB. В лямбда-архитектуре это **пакетное представление** на **уровне обслуживания**.
 
#### <a name="resources"></a>Ресурсы

Целые примеры кода см. по адресу [azure-cosmosdb-spark/lambda/samples](vhttps://github.com/Azure/azure-cosmosdb-spark/tree/master/samples/lambda), в том числе:
* Lambda Architecture Rearchitected - Batch Layer (Измененная лямбда-архитектура. Пакетный уровень) [HTML](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20Layer.html) | [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20Layer.ipynb)
* Lambda Architecture Rearchitected - Batch to Serving Layer (Измененная лямбда-архитектура. Пакетный уровень — уровень обслуживания) [HTML](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20to%20Serving%20Layer.html) | [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20to%20Serving%20Layer.ipynb)

## <a name="speed-layer"></a>Уровень ускорения
Как указано выше, применение библиотеки канала изменений Azure Cosmos DB упрощает выполнение операций между уровнем пакетов и ускорения. В этой архитектуре используйте Apache Spark (с помощью HDInsight), чтобы выполнять *структурированную потоковую передачу* запросов к данным. Вы также можете временно сохранять результаты запросов структурированной потоковой передачи, чтобы к этим данным был доступ из других систем.

![Схема, на которой выделен уровень ускорения в лямбда-архитектуре](./media/lambda-architecture/lambda-architecture-speed.png)

Для этого создайте отдельную коллекцию Azure Cosmos DB, в которой будут сохраняться результаты запросов структурированной потоковой передачи.  Таким образом сведения будут доступны для других систем, а не только для Apache Spark. Кроме того, с помощью функции срока жизни (TTL) в Cosmos DB можно настроить автоматическое удаление документов по прошествии определенного времени.  Дополнительные сведения о TTL в Azure Cosmos DB см. в статье [Автоматическое завершение срока действия данных в коллекциях Azure Cosmos DB с использованием срока жизни](time-to-live.md).

```
// Import Libraries
import com.microsoft.azure.cosmosdb.spark._
import com.microsoft.azure.cosmosdb.spark.schema._
import com.microsoft.azure.cosmosdb.spark.config.Config
import org.codehaus.jackson.map.ObjectMapper
import com.microsoft.azure.cosmosdb.spark.streaming._
import java.time._


// Configure connection to Azure Cosmos DB Change Feed
val sourceCollectionName = "[SOURCE COLLECTION NAME]"
val sinkCollectionName = "[SINK COLLECTION NAME]"

val configMap = Map(
"Endpoint" -> "[COSMOSDB ENDPOINT]",
"Masterkey" -> "[COSMOSDB MASTER KEY]",
"Database" -> "[DATABASE NAME]",
"Collection" -> sourceCollectionName,
"ChangeFeedCheckpointLocation" -> "changefeedcheckpointlocation")

val sourceConfigMap = configMap.+(("changefeedqueryname", "Structured Stream replication streaming test"))

// Start to read the stream
var streamData = spark.readStream.format(classOf[CosmosDBSourceProvider].getName).options(sourceConfigMap).load()
val sinkConfigMap = configMap.-("collection").+(("collection", sinkCollectionName))

// Start the stream writer to new collection
val streamingQueryWriter = streamData.writeStream.format(classOf[CosmosDBSinkProvider].getName).outputMode("append").options(sinkConfigMap).option("checkpointLocation", "streamingcheckpointlocation")
var streamingQuery = streamingQueryWriter.start()

```

## <a name="lambda-architecture-rearchitected"></a>Измененная лямбда-архитектура
Как указано в разделах выше, вы можете упростить исходную лямбда-архитектуру с помощью следующих компонентов:
* Azure Cosmos DB
* библиотека канала изменений Azure Cosmos DB (позволяет избежать проблем с многократным приведением данных между пакетным уровнем и уровнем обслуживания);
* Apache Spark в HDInsight;
* соединитель Spark для Azure Cosmos DB.

![Схема измененной лямбда-архитектуры, в которой используется Azure Cosmos DB, Spark и API канала изменений Azure Cosmos DB](./media/lambda-architecture/lambda-architecture-re-architected.png)

При такой структуре требуется только две управляемые службы: Azure Cosmos DB и HDInsight. Вместе они обеспечивают работу на пакетном уровне, а также на уровнях обслуживания и ускорения в лямбда-архитектуре. Это упрощает выполнение не только операций, но и потоков данных. 
 1. Все данные отправляются в Azure Cosmos DB для обработки.
 2. Пакетный уровень предполагает набор основных данных (неизменяемый набор необработанных данных только для добавления) и предварительно вычисляет пакетные представления.
 3. Уровень обслуживания предусматривает представления пакетов данных для быстрой отправки запросов.
 4. На уровне ускорения происходит компенсация времени обработки (на уровне обслуживания) и используются только последние данные.
 5. Вы можете объединить результаты пакетного представления и представления в режиме реального времени, чтобы получить ответы на все запросы.

### <a name="resources"></a>Ресурсы

 * **Новые данные** — [канал потоковой передачи из Twitter в CosmosDB](https://github.com/tknandu/TwitterCosmosDBFeed), представляющий собой механизм для принудительной отправки новых данных в Azure Cosmos DB.
 * **Пакетный уровень** — *основной набор данных* (неизменяемых необработанных данных только для добавления) и возможность предварительно вычислять пакетные представления данных, передаваемых на **уровень обслуживания** .
    * Записная книжка **Lambda Architecture Rearchitected - Batch Layer** (Измененная лямбда-архитектура. Пакетный уровень) [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20Layer.ipynb) | [html](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20Layer.html) запрашивает *набор основных данных* для пакетных представлений.
 * **Уровень обслуживания.** Предполагает предварительно вычисляемые данные, которые отображаются в пакетных представлениях (например, агрегаты, определенные срезы и т. д.), для быстрого выполнения запросов.
    * Записная книжка **Lambda Architecture Rearchitected - Batch to Serving Layer** (Измененная лямбда-архитектура. Пакетный уровень — уровень обслуживания) [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20to%20Serving%20Layer.ipynb) | [html](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20to%20Serving%20Layer.html) обеспечивает отправку пакетных данных на уровень обслуживания, т. е. Spark запрашивает коллекцию твитов, обрабатывает ее и сохраняет в другой коллекции (в вычисляемом пакете).
* **Уровень ускорения.** Предусматривает использование Spark канала изменений Azure Cosmos DB для чтения и немедленного реагирования. Данные можно также сохранить в *вычисляемом RT*, чтобы другие системы могли запросить обработанные данные в режиме реального времени, а не обрабатывать их.
    * [Streaming Query from Cosmos DB Change Feed](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Streaming%20Query%20from%20Cosmos%20DB%20Change%20Feed.scala) (Потоковая передача запроса из канала изменений Cosmos DB) — сценарий Scala, предусматривающий выполнение запросов потоковой передачи из канала изменений Azure Cosmos DB, чтобы подсчитывать число интервалов в оболочке Spark.
    * [Streaming Tags Query from Cosmos DB Change Feed](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Streaming%20Tags%20Query%20from%20Cosmos%20DB%20Change%20Feed%20.scala) (Потоковая передача тегов из канала изменений Cosmos DB.scala) — сценарий Scala, предусматривающий выполнение запросов потоковой передачи из канала изменений Azure Cosmos DB, чтобы подсчитывать число тегов в оболочке Spark.
  
## <a name="next-steps"></a>Дополнительная информация
Скачайте соединитель Azure Cosmos DB для Spark из репозитория [azure-cosmosdb-spark](https://github.com/Azure/azure-cosmosdb-spark) на сайте GitHub и изучите дополнительные ресурсы в этом репозитории:
* [Лямбда-архитектура](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples/lambda)
* [Примеры распределенных агрегирований](https://github.com/Azure/azure-documentdb-spark/wiki/Aggregations-Examples)
* [Примеры скриптов и записных книжек](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples)
* [Демонстрационные материалы по структурированной потоковой передаче](https://github.com/Azure/azure-cosmosdb-spark/wiki/Structured-Stream-demos)
* [Демонстрационные материалы по каналу изменений](https://github.com/Azure/azure-cosmosdb-spark/wiki/Change-Feed-demos)
* [Stream Processing Changes using Azure Cosmos DB Change Feed and Apache Spark](https://github.com/Azure/azure-cosmosdb-spark/wiki/Stream-Processing-Changes-using-Azure-Cosmos-DB-Change-Feed-and-Apache-Spark) (Потоковая обработка изменений с использованием канала изменений Azure Cosmos DB и Apache Spark).

Дополнительные сведения см. также в [руководстве по SQL, таблицам и наборам данных Apache Spark](http://spark.apache.org/docs/latest/sql-programming-guide.html) и в статье [Начало работы. Создание кластера Apache Spark в Azure HDInsight и выполнение интерактивных запросов с помощью SQL Spark](../hdinsight/spark/apache-spark-jupyter-spark-sql.md).

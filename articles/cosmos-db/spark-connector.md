---
title: Подключение Apache Spark к Azure Cosmos DB | Документация Майкрософт
description: В этом руководстве приведены сведения о соединителе Azure Cosmos DB Spark, который позволяет подключать Apache Spark к Azure Cosmos DB для выполнения распределенного агрегирования и обработки данных в мультитенантной глобально распределенной системе баз данных Майкрософт, разработанной для использования в облачной среде.
keywords: apache spark
services: cosmos-db
author: dennyglee
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 01/08/2018
ms.author: denlee
ms.openlocfilehash: 3502a5af5de546de0b66521e29c37f5edfdbc6ea
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34612628"
---
# <a name="accelerate-real-time-big-data-analytics-with-the-spark-to-azure-cosmos-db-connector"></a>Ускорение аналитики больших данных в реальном времени с помощью соединителя Spark для Cosmos DB

Соединитель Spark для Azure Cosmos DB дает возможность использовать Azure Cosmos DB в качестве источника входных данных или приемника выходных данных для заданий Apache Spark. Подключение [Spark](http://spark.apache.org/) к [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) ускоряет решение проблем анализа данных, используя Azure Cosmos DB для быстрого сохранения и запроса данных. Соединитель Spark для Azure Cosmos DB эффективно использует собственные управляемые индексы Azure Cosmos DB. Эти индексы позволяют использовать обновляемые столбцы при выполнении анализа, применять фильтры предиката к быстро меняющимся глобально распределенным данным, начиная от Интернета вещей, обработки и анализа данных и до сценариев аналитики.

Дополнительные сведения см. в этом видео с главным администратором проектов Azure Cosmos DB Дэнни Ли (Denny Lee) 

> [!VIDEO https://channel9.msdn.com/Events/Connect/2017/T135/player] 

Дополнительные сведения о работе с API Spark GraphX и графами Gremlin в Azure Cosmos DB см.в статье [Azure Cosmos DB. Аналитика графов с помощью Spark и Apache TinkerPop Gremlin](spark-connector-graph.md).

## <a name="download"></a>Загрузка

Чтобы приступить к работе, скачайте соединитель Spark для Azure Cosmos DB из репозитория [azure-cosmosdb-spark](https://github.com/Azure/azure-cosmosdb-spark/) на сайте GitHub.

## <a name="connector-components"></a>Компоненты соединителя

Соединитель использует следующие компоненты:

* [Azure Cosmos DB](http://documentdb.com) позволяет клиентам подготавливать и эластично масштабировать пропускную способность и ресурсы хранилища в любом количестве географических регионов. Служба предлагает следующее:
   * Готовое решение [глобального распределения](distribute-data-globally.md) и горизонтального масштабирования.
   * Гарантия задержки, измеряемой единицами, на уровне 99-го процентиля.
   * [Множество точно определенных моделей согласованности](consistency-levels.md).
   * Гарантии высокой доступности с возможностями множественной адресации.
   * Все возможности прописаны в ведущих в отрасли универсальных [соглашениях об уровне обслуживания](https://azure.microsoft.com/support/legal/sla/cosmos-db) (SLA).

* [Apache Spark](http://spark.apache.org/) — высокопроизводительная подсистема обработки с открытым исходным кодом, призванная ускорить разработку, повысить удобство использования и реализовать сложную аналитику.

* [Apache Spark в Azure HDInsight](../hdinsight/spark/apache-spark-jupyter-spark-sql.md) — чтобы Apache Spark можно было развернуть в облаке для критически важных приложений, используя [Azure HDInsight](https://azure.microsoft.com/services/hdinsight/apache-spark/).

Официально поддерживаемые версии:

| Компонент | Version (версия) |
|---------|-------|
|Apache Spark|2.0.2, 2.1.0, 2.2.0|
| Scala| 2.10, 2.11|
| Пакет SDK SQL для Java в Azure Cosmos DB | 1.14.0, 1.15.0 |

Эта статья поможет вам выполнить некоторые простые примеры на языке Python (через pyDocumentDB) с помощью интерфейса Scala.

Существует два способа подключения Apache Spark и Azure Cosmos DB:
- при помощи pyDocumentDB с использованием [пакета SDK SQL для Python в Azure Cosmos DB](https://github.com/Azure/azure-documentdb-python);
- с помощью соединителя Spark для Azure Cosmos DB на основе Java, использующего [пакет SDK SQL для Java в Azure Cosmos DB](https://github.com/Azure/azure-documentdb-java).

## <a name="pydocumentdb-implementation"></a>Реализация pyDocumentDB
Текущий [пакет SDK для pyDocumentDB](https://github.com/Azure/azure-documentdb-python) дает возможность подключить Spark к Azure Cosmos DB, как показано на следующей схеме:

![Поток данных из Spark в Azure Cosmos DB через pyDocument DB](./media/spark-connector/spark-pydocumentdb.png)


### <a name="data-flow-of-the-pydocumentdb-implementation"></a>Путь потока данных в pyDocumentDB

Путь потока данных выглядит следующим образом:

1. Главный узел Spark подключается к узлу шлюза Azure Cosmos DB через pyDocumentDB. Пользователь указывает только подключения Spark и Azure Cosmos DB. Соединения в соответствующих главном узле и узле шлюза прозрачны для пользователя.
2. Узел шлюза делает запрос к Azure Cosmos DB. Далее запрос отправляется к разделам коллекции в узлах данных. Ответ на эти запросы возвращается в узел шлюза, а результирующий набор — в главный узел Spark.
3. Последующие запросы (например, к таблице данных Spark) отправляются в рабочие узлы Spark для обработки.

Взаимодействие между Spark и Azure Cosmos DB происходит в пределах главного узла Spark и узлов шлюза Azure Cosmos DB.  Скорость запросов зависит от транспортного уровня между двумя этими узлами.

### <a name="install-pydocumentdb"></a>Установка pyDocumentDB
pyDocumentDB можно установить на узел драйвера с помощью **pip** следующим образом:

```bash
pip install pyDocumentDB
```


### <a name="connect-spark-to-azure-cosmos-db-via-pydocumentdb"></a>Подключение Spark к Azure Cosmos DB через pyDocumentDB
Благодаря простоте обмена данными выполнение запроса из Spark в Azure Cosmos DB с помощью pyDocumentDB относительно простое.

В приведенном ниже фрагменте кода показано, как использовать pyDocumentDB в контексте Spark.

```python
# Import Necessary Libraries
import pydocumentdb
from pydocumentdb import document_client
from pydocumentdb import documents
import datetime

# Configuring the connection policy (allowing for endpoint discovery)
connectionPolicy = documents.ConnectionPolicy()
connectionPolicy.EnableEndpointDiscovery
connectionPolicy.PreferredLocations = ["Central US", "East US 2", "Southeast Asia", "Western Europe","Canada Central"]


# Set keys to connect to Azure Cosmos DB
masterKey = 'le1n99i1w5l7uvokJs3RT5ZAH8dc3ql7lx2CG0h0kK4lVWPkQnwpRLyAN0nwS1z4Cyd1lJgvGUfMWR3v8vkXKA=='
host = 'https://doctorwho.documents.azure.com:443/'
client = document_client.DocumentClient(host, {'masterKey': masterKey}, connectionPolicy)
```

Во фрагменте кода указано:

* Пакет SDK Python для Azure Cosmos DB (`pyDocumentDB`) содержит все необходимые параметры подключения. Например, параметр предпочтительных расположений выбирает реплику чтения и приоритет.
*  Импортируйте необходимые библиотеки, а затем настройте **главный ключ** и **узел** для создания *клиента* Azure Cosmos DB (**pydocumentdb.document_client**).


### <a name="execute-spark-queries-via-pydocumentdb"></a>Выполнение запросов Spark с помощью pyDocumentDB
В следующих примерах используется экземпляр Azure Cosmos DB, созданный в предыдущем фрагменте с использованием указанных ключей, доступных только для чтения. Следующий фрагмент кода подключается к коллекции **airports.codes** (в учетной записи DoctorWho, как указано ранее) и выполняет запрос для извлечения городов с аэропортами в штате Вашингтон.

```python
# Configure Database and Collections
databaseId = 'airports'
collectionId = 'codes'

# Configurations the Azure Cosmos DB client will use to connect to the database and collection
dbLink = 'dbs/' + databaseId
collLink = dbLink + '/colls/' + collectionId

# Set query parameter
querystr = "SELECT c.City FROM c WHERE c.State='WA'"

# Query documents
query = client.QueryDocuments(collLink, querystr, options=None, partition_key=None)

# Query for partitioned collections
# query = client.QueryDocuments(collLink, query, options= { 'enableCrossPartitionQuery': True }, partition_key=None)

# Push into list `elements`
elements = list(query)
```

После выполнения запроса с использованием команды **query** в результате отобразится значение **query_iterable. QueryIterable**, преобразованное в список Python. Список Python можно легко преобразовать в таблицу данных Spark, используя следующий код:

```python
# Create `df` Spark DataFrame from `elements` Python list
df = spark.createDataFrame(elements)
```

### <a name="why-use-the-pydocumentdb-to-connect-spark-to-azure-cosmos-db"></a>Зачем использовать pyDocumentDB для подключения Spark к Azure Cosmos DB?
Подключение Spark к Azure Cosmos DB с помощью pyDocumentDB в основном предназначено для следующих сценариев:

* Необходимо использовать Python.
* Необходимо вернуть относительно небольшой набор результатов из Azure Cosmos DB в Spark. Обратите внимание, что базовый набор данных в Azure Cosmos DB может быть достаточно большим. К источнику Azure Cosmos DB можно применить фильтры предиката.  

## <a name="spark-to-azure-cosmos-db-connector"></a>Соединитель Spark для Azure Cosmos DB

Соединитель Spark для Azure Cosmos DB использует [пакет SDK SQL для Java в Azure Cosmos DB](https://github.com/Azure/azure-documentdb-java) и перемещает данные между рабочими узлами Spark и Azure Cosmos DB, как показано на следующей схеме:

![Поток данных в соединителе Spark для Azure Cosmos DB](./media/spark-connector/spark-connector.png)

Путь потока данных выглядит следующим образом:

1. Главный узел Spark подключается к узлу шлюза Azure Cosmos DB, чтобы получить схему секционирования. Пользователь указывает только подключения Spark и Azure Cosmos DB. Соединения в соответствующих главном узле и узле шлюза прозрачны для пользователя.
2. Эта информация возвращается в главный узел Spark.  На этом этапе вы можете проанализировать запрос, чтобы определить разделы (и их расположения) в Azure Cosmos DB, к которым требуется получить доступ.
3. Эта информация передается в рабочие узлы Spark.
4. Таким образом, рабочие узлы Spark подключаются к разделам Azure Cosmos DB, чтобы извлечь необходимые данные и вернуть их в разделы рабочих узлов Spark.

Взаимодействие между Spark и Azure Cosmos DB происходит значительно быстрее, так как данные перемещаются между рабочими узлами Spark и узлами данных Azure Cosmos DB (разделами).

### <a name="build-the-spark-to-azure-cosmos-db-connector"></a>Создание соединителя Spark для Azure Cosmos DB
Сейчас в проекте соединителя используется maven. Чтобы создать соединитель без зависимостей, выполните следующую команду:
```
mvn clean package
```
Вы также можете скачать последние версии JAR-файла из [папки *releases*](https://github.com/Azure/azure-cosmosdb-spark/releases).

### <a name="include-the-azure-cosmos-db-spark-jar"></a>Включение JAR-файла Azure Cosmos DB Spark
Перед выполнением любого кода необходимо добавить JAR-файл Azure Cosmos DB Spark.  Если вы используете **spark-shell**, JAR-файл можно включить с помощью параметра **--jars**.  

```bash
spark-shell --master $master --jars /$location/azure-cosmosdb-spark_2.1.0_2.11-1.0.0-uber.jar
```

JAR-файл можно также выполнить без зависимостей, используя следующий код:

```bash
spark-shell --master $master --jars /$location/azure-cosmosdb-spark_2.1.0_2.11-1.0.0.jar,/$location/azure-documentdb-1.14.0.jar,/$location/azure-documentdb-rx-0.9.0-rc2.jar,/$location/json-20140107.jar,/$location/rxjava-1.3.0.jar,/$location/rxnetty-0.4.20.jar 
```

При использовании записной книжки (например, Azure HDInsight Jupyter) вы можете использовать команды **spark magic**:

```
%%configure
{ "name":"Spark-to-Cosmos_DB_Connector", 
  "jars": ["wasb:///example/jars/1.0.0/azure-cosmosdb-spark_2.1.0_2.11-1.0.0.jar", "wasb:///example/jars/1.0.0/azure-documentdb-1.14.0.jar", "wasb:///example/jars/1.0.0/azure-documentdb-rx-0.9.0-rc2.jar", "wasb:///example/jars/1.0.0/json-20140107.jar", "wasb:///example/jars/1.0.0/rxjava-1.3.0.jar", "wasb:///example/jars/1.0.0/rxnetty-0.4.20.jar"],
  "conf": {
    "spark.jars.excludes": "org.scala-lang:scala-reflect"
   }
}
```

Команда **jars** позволяет включить два JAR-файла, необходимые для **azure-cosmosdb-spark** (и пакета SDK SQL для Java в Azure Cosmos DB), и исключает **scala-reflect**, чтобы предотвратить конфликт с вызовами Livy (Jupyter Notebook > Livy > Spark).

### <a name="connect-spark-to-azure-cosmos-db-using-the-connector"></a>Подключение Spark и Azure Cosmos DB с помощью соединителя
Несмотря на то что обмен данными происходит сложнее, запрос из Spark в Azure Cosmos DB с помощью соединителя выполняется значительно быстрее.

В следующем фрагменте кода показано, как использовать соединитель в сеансе Spark; ознакомьтесь с примерами на Python в `azure-cosmosdb-spark` [в репозитории GitHub](https://github.com/Azure/azure-cosmosdb-spark).

```
// Import Necessary Libraries
import org.joda.time._
import org.joda.time.format._
import com.microsoft.azure.cosmosdb.spark.schema._
import com.microsoft.azure.cosmosdb.spark._
import com.microsoft.azure.cosmosdb.spark.config.Config

// Configure connection to your collection
val baseConfig = Config(Map("Endpoint" -> "https://doctorwho.documents.azure.com:443/",
"Masterkey" -> "le1n99i1w5l7uvokJs3RT5ZAH8dc3ql7lx2CG0h0kK4lVWPkQnwpRLyAN0nwS1z4Cyd1lJgvGUfMWR3v8vkXKA==",
"Database" -> "DepartureDelays",
"preferredRegions" -> "Central US;East US2;",
"Collection" -> "flights_pcoll",
"SamplingRatio" -> "1.0"))

// Create collection connection
val coll = spark.sqlContext.read.cosmosDB(baseConfig)
coll.createOrReplaceTempView("c")
```

Во фрагменте кода указано:

- **azure-cosmosdb-spark** содержит все необходимые параметры подключения, включая предпочтительное расположения. Например, можно выбрать реплику чтения и приоритет.
- Импортируйте необходимые библиотеки и настройте главный ключ и узел для создания клиента Azure Cosmos DB.

### <a name="execute-spark-queries-via-the-connector"></a>Выполнение запросов Spark с помощью соединителя

В следующем примере используется экземпляр Azure Cosmos DB, созданный в предыдущем фрагменте с использованием указанных ключей, доступных только для чтения. Следующий фрагмент кода подключается к коллекции DepartureDelays.flights_pcoll (в учетной записи DoctorWho, как указано ранее) и выполняет запрос для извлечения сведений о задержке рейсов, вылетающих из Сиэтла.

```
// Queries
var query = "SELECT c.date, c.delay, c.distance, c.origin, c.destination FROM c WHERE c.origin = 'SEA'"
val df = spark.sql(query)

// Run DF query (count)
df.count()

// Run DF query (show)
df.show()
```

### <a name="why-use-the-spark-to-azure-cosmos-db-connector-implementation"></a>Зачем использовать соединитель Spark для Azure Cosmos DB?

Подключение Spark к Azure Cosmos DB с помощью соединителя в основном предназначено для следующих сценариев:

* Вам нужно использовать Python и (или) Scala.
* Необходимо передать большой объем данных между Apache Spark и Azure Cosmos DB.

Чтобы получить представление о разнице в производительности запросов, см. сведения в статье [Query Test Runs](https://github.com/Azure/azure-cosmosdb-spark/wiki/Query-Test-Runs) (Тестовые выполнения запросов).

## <a name="distributed-aggregation-example"></a>Пример распределенного агрегирования
В этом разделе приведены некоторые примеры того, как можно выполнить распределенное агрегирование и аналитику с помощью Apache Spark и Azure Cosmos DB. В Azure Cosmos DB уже реализована поддержка агрегирования,как описано в записи блога [Planet scale aggregates with Azure Cosmos DB](https://azure.microsoft.com/blog/planet-scale-aggregates-with-azure-documentdb/) (Глобальное агрегирование с помощью Azure Cosmos DB). Вот как с помощью Apache Spark этот процесс можно выполнить на новом уровне.

Обратите внимание, что эти агрегирования относятся к [соединителю Spark для Azure Cosmos DB](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/Spark-to-CosmosDB_Connector.ipynb).

### <a name="connect-to-flights-sample-data"></a>Подключение к примеру данных о полетах
Эти примеры агрегаций получают доступ к данным о выполнении рейсов, которые хранятся в базе данных Azure Cosmos DB **DoctorWho**. Чтобы подключиться к ней, используйте следующий фрагмент кода:

```
// Import Spark to Azure Cosmos DB connector
import com.microsoft.azure.cosmosdb.spark.schema._
import com.microsoft.azure.cosmosdb.spark._
import com.microsoft.azure.cosmosdb.spark.config.Config

// Connect to Azure Cosmos DB Database
val readConfig2 = Config(Map("Endpoint" -> "https://doctorwho.documents.azure.com:443/",
"Masterkey" -> "le1n99i1w5l7uvokJs3RT5ZAH8dc3ql7lx2CG0h0kK4lVWPkQnwpRLyAN0nwS1z4Cyd1lJgvGUfMWR3v8vkXKA==",
"Database" -> "DepartureDelays",
"preferredRegions" -> "Central US;East US 2;",
"Collection" -> "flights_pcoll",
"SamplingRatio" -> "1.0"))

// Create collection connection
val coll = spark.sqlContext.read.cosmosDB(readConfig2)
coll.createOrReplaceTempView("c")
```

С помощью этого фрагмента кода мы также выполним базовый запрос, передающий отфильтрованный набор данных из Azure Cosmos DB в Spark (где впоследствии можно выполнить распределенные агрегирования). В этом случае мы запрашиваем сведения об авиарейсах, вылетающих из Сиэтла (SEA).

```
// Run, get row count, and time query
val originSEA = spark.sql("SELECT c.date, c.delay, c.distance, c.origin, c.destination FROM c WHERE c.origin = 'SEA'")
originSEA.createOrReplaceTempView("originSEA")
```

Следующие результаты были получены с помощью запросов от службы Jupyter Notebook.  Обратите внимание, что все фрагменты кода являются универсальными и не относятся к конкретной службе.

### <a name="running-limit-and-count-queries"></a>Выполнение запросов LIMIT и COUNT
Так же как и в случае с SQL или Spark SQL, начнем с запроса **LIMIT**:

![Запрос Spark LIMIT](./media/spark-connector/spark-sql-query.png)

Следующий запрос это простой и быстрый запрос **COUNT**:

![Запрос Spark COUNT](./media/spark-connector/spark-count-query.png)

### <a name="group-by-query"></a>Запрос GROUP BY
В следующем наборе можно легко выполнять запросы **GROUP BY** к базе данных Azure Cosmos DB:

```
select destination, sum(delay) as TotalDelays
from originSEA
group by destination
order by sum(delay) desc limit 10
```

![График запроса Spark GROUP BY](./media/spark-connector/group-by-query-graph.png)

### <a name="distinct-order-by-query"></a>Запросы DISTINCT и ORDER BY
Запросы **DISTINCT и ORDER BY** выглядят так:

![График запроса Spark GROUP BY](./media/spark-connector/order-by-query.png)

### <a name="continue-the-flight-data-analysis"></a>Дальнейший анализ данных о рейсах
Следующие примеры запросов можно использовать, чтобы продолжить анализ данных о рейсах:

#### <a name="top-5-delayed-destinations-cities-departing-from-seattle"></a>Первые 5 задержанных рейсов, вылетающих из Сиэтла
```
select destination, sum(delay)
from originSEA
where delay < 0
group by destination
order by sum(delay) limit 5
```
![График Spark для первых задержанных рейсов](./media/spark-connector/top-delays-graph.png)


#### <a name="calculate-median-delays-by-destination-cities-departing-from-seattle"></a>Вычисление медианы задержки вылетов из Сиэтла по городам назначения
```
select destination, percentile_approx(delay, 0.5) as median_delay
from originSEA
where delay < 0
group by destination
order by percentile_approx(delay, 0.5)
```

![График медианы задержки Spark](./media/spark-connector/median-delays-graph.png)

## <a name="next-steps"></a>Дополнительная информация

Скачайте соединитель Azure Cosmos DB для Spark из репозитория [azure-cosmosdb-spark](https://github.com/Azure/azure-cosmosdb-spark) на сайте GitHub и изучите дополнительные ресурсы в этом репозитории:

* [Aggregations Examples](https://github.com/Azure/azure-cosmosdb-spark/wiki/Aggregations-Examples) (Примеры агрегирований)
* [Sample Scripts and Notebooks](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples) (Примеры скриптов и записных книжек)

Дополнительные сведения см. также в [руководстве по SQL, таблицам и наборам данных Apache Spark](http://spark.apache.org/docs/latest/sql-programming-guide.html) и в статье [Начало работы. Создание кластера Apache Spark в Azure HDInsight и выполнение интерактивных запросов с помощью SQL Spark](../hdinsight/spark/apache-spark-jupyter-spark-sql.md).

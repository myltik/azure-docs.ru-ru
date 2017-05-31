---
title: "Подключение Apache Spark к Azure Cosmos DB | Документация Майкрософт"
description: "В этом руководстве приведены сведения о соединителе Azure Cosmos DB Spark, который позволяет подключать Apache Spark к Azure Cosmos DB для выполнения распределенного агрегирования и обработки данных в мультитенантной глобально распределенной системе баз данных Майкрософт, разработанной для использования в облачной среде."
keywords: apache spark
services: cosmosdb
documentationcenter: 
author: dennyglee
manager: jhubbard
editor: 
ms.assetid: c4f46007-2606-4273-ab16-29d0e15c0736
ms.service: cosmosdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/03/2017
ms.author: denlee
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 6a5db515bec785aa29b29a3096dc20a72056bd08
ms.contentlocale: ru-ru
ms.lasthandoff: 05/10/2017


---

# <a name="accelerate-real-time-big-data-analytics-with-the-spark-to-azure-cosmos-db-connector"></a>Ускорение аналитики больших данных в реальном времени с помощью соединителя Spark для Cosmos DB

Соединитель Spark для Cosmos DB позволяет использовать Azure Cosmos DB в качестве источника входных данных или приемника выходных данных для заданий Apache Spark. Подключение [Spark](http://spark.apache.org/) к [Azure Cosmos DB](https://azure.microsoft.com/services/documentdb/) ускоряет решение проблем анализа данных. Это также позволяет быстро сохранять и запрашивать данные с помощью Cosmos DB. Соединитель Spark для Azure Cosmos DB эффективно использует собственные управляемые индексы Cosmos DB, а также позволяет использовать обновляемые столбцы при выполнении анализа, применять фильтры предиката к быстро меняющимся глобально распределенным данным, начиная от Интернета вещей, обработки и анализа данных и до сценариев аналитики. 

## <a name="download"></a>Загрузить

Сначала скачайте соединитель Spark для Cosmos DB (предварительная версия) из репозитория [azure-documentdb-spark](https://github.com/Azure/azure-documentdb-spark/) на сайте GitHub.

## <a name="connector-components"></a>Компоненты соединителя

Соединитель использует следующие компоненты:

* [Azure Cosmos DB](http://documentdb.com) — мультитенантная [глобально распределенная система баз данных](documentdb-distribute-data-globally.md) от корпорации Майкрософт, разработанная для использования в облачной среде. Azure Cosmos DB позволяет клиентам подготавливать и эластично масштабировать пропускную способность и ресурсы хранилища в любом количестве географических регионов. DocumentDB гарантирует низкую задержку на уровне 99-го процентиля, высокий уровень доступности в 99,99 % и [несколько четко определенных моделей согласованности](documentdb-consistency-levels.md) для разработчиков.

* [Apache Spark](http://spark.apache.org/) — высокопроизводительная подсистема обработки с открытым исходным кодом, призванная ускорить разработку, повысить удобство использования и реализовать сложную аналитику. 

* [Apache Spark в Azure HDInsight](../hdinsight/hdinsight-apache-spark-jupyter-spark-sql.md) Apache Spark можно развернуть в облаке для выполнения критически важных развертываний с помощью [Azure HDInsight](https://azure.microsoft.com/services/hdinsight/apache-spark/).

Официально поддерживаемые версии:

| Компонент | Версия |
|---------|-------|
|Apache Spark|2.0+|
| Scala| 2.11|
| Пакет SDK для Java в Azure DocumentDB | 1.9.6 |
 
Эта статья поможет вам выполнить некоторые простые примеры на языке Python (через pyDocumentDB) с помощью интерфейса Scala.

Существует два способа подключения Apache Spark и Azure Cosmos DB:
- с помощью pyDocumentDB через [пакет SDK для Java в Azure DocumentDB](https://github.com/Azure/azure-documentdb-python);
- с помощью соединителя Spark для DocumentDB на основе Java, использующего [пакет SDK для Java в Azure DocumentDB](https://github.com/Azure/azure-documentdb-java).

## <a name="pydocumentdb-implementation"></a>Реализация pyDocumentDB 
Текущий [пакет SDK для pyDocumentDB](https://github.com/Azure/azure-documentdb-python) позволяет подключить Spark к Cosmos DB, как показано на следующей схеме:

![Поток данных из Spark в Cosmos DB через pyCosmos DB](./media/documentdb-spark-connector/azure-documentdb-spark-pydocumentdb.png)


### <a name="data-flow-of-the-pydocumentdb-implementation"></a>Путь потока данных в pyDocumentDB

Путь потока данных выглядит следующим образом:

1. Между главным узлом Spark и узлом шлюза Cosmos DB устанавливается подключение через pyCosmos DB.  Обратите внимание, что пользователь указывает только подключения между Spark и Cosmos DB. Тот факт, что подключение устанавливается между соответствующим главным узлом и узлом шлюза, очевиден.
2. К Cosmos DB (через узел шлюза) выполняется запрос, вследствие чего далее отправляется запрос к разделам коллекции в узлах данных. Ответ на эти запросы возвращается в узел шлюза, а результирующий набор — в главный узел Spark.
3. Все последующие запросы (например, к таблице данных Spark) отправляются в рабочие узлы Spark для обработки.

Важно, что взаимодействие между Spark и Cosmos DB происходит в пределах главного узла Spark и узла шлюза Cosmos DB.  Скорость запросов зависит от транспортного уровня между двумя этими узлами.

### <a name="installing-pydocumentdb"></a>Установка pyDocumentDB
pyDocumentDB можно установить на узел драйвера с помощью **pip** следующим образом:

```
pip install pyDocumentDB
```


### <a name="connecting-spark-to-cosmos-db-via-pycosmos-db"></a>Подключение Spark к Cosmos DB через pyCosmos DB 
Благодаря простоте обмена данными выполнение запроса из Spark в Cosmos DB с помощью pyCosmos DB относительно простое.

В приведенном ниже фрагменте кода показано, как использовать pyDocumentDB в контексте Spark.

```
# Import Necessary Libraries
import pydocumentdb
from pydocumentdb import document_client
from pydocumentdb import documents
import datetime

# Configuring the connection policy (allowing for endpoint discovery)
connectionPolicy = documents.ConnectionPolicy()
connectionPolicy.EnableEndpointDiscovery 
connectionPolicy.PreferredLocations = ["Central US", "East US 2", "Southeast Asia", "Western Europe","Canada Central"]


# Set keys to connect to Cosmos DB 
masterKey = 'le1n99i1w5l7uvokJs3RT5ZAH8dc3ql7lx2CG0h0kK4lVWPkQnwpRLyAN0nwS1z4Cyd1lJgvGUfMWR3v8vkXKA==' 
host = 'https://doctorwho.documents.azure.com:443/'
client = document_client.DocumentClient(host, {'masterKey': masterKey}, connectionPolicy)
```

Во фрагменте кода указано:

* Пакет SDK для DocumentDB Python содержит все необходимые параметры подключения, в том числе предпочтительные расположения (выбор порядка чтения реплик).
*  Импортируйте необходимые библиотеки, а затем настройте **главный ключ** и **узел** для создания *клиента* Cosmos DB (**pydocumentdb.document_client**).


### <a name="executing-spark-queries-via-pydocumentdb"></a>Выполнение запросов Spark с помощью pyDocumentDB
В следующих примерах используется экземпляр Cosmos DB, созданный в предыдущем фрагменте с использованием указанных ключей, доступных только для чтения.  Следующий фрагмент кода подключается к коллекции **airports.codes** (в учетной записи DoctorWho, как указано ранее) и выполняет запрос для извлечения городов с аэропортами в штате Вашингтон. 

```
# Configure Database and Collections
databaseId = 'airports'
collectionId = 'codes'

# Configurations the Cosmos DB client will use to connect to the database and collection
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

```
# Create `df` Spark DataFrame from `elements` Python list
df = spark.createDataFrame(elements)
```

### <a name="why-use-the-pydocumentdb-to-connect-spark-to-documentdb"></a>Зачем использовать pyDocumentDB для подключения Spark к DocumentDB?
Подключение Spark к Cosmos DB с помощью pyCosmos DB в основном предназначено для следующих сценариев:

* Необходимо использовать Python.
* В Spark возвращается относительно небольшой результирующий набор из DocumentDB.  Обратите внимание, что базовый набор данных в DocumentDB может быть довольно большим. В этом случае к источнику DocumentDB можно применить фильтры предиката.  

## <a name="spark-to-cosmos-db-connector"></a>Соединитель Spark для Cosmos DB

Соединитель Spark для Cosmos DB использует [пакет SDK для Java в Azure Cosmos DB](https://github.com/Azure/azure-documentdb-java) и перемещает данные между рабочими узлами Spark и Cosmos DB, как показано на следующей схеме:

![Поток данных в соединителе Spark для DocumentDB](./media/documentdb-spark-connector/azure-documentdb-spark-connector.png)

Путь потока данных выглядит следующим образом:

1. Чтобы получить схему секционирования, между главным узлом Spark и узлом шлюза Cosmos DB устанавливается соединение.  Обратите внимание, что пользователь указывает только подключение между Spark и Cosmos DB. Тот факт, что подключение устанавливается между соответствующим главным узлом и узлом шлюза, очевиден.
2. Эта информация возвращается в главный узел Spark.  На этом этапе вы можете проанализировать запрос, чтобы определить разделы (и их расположение) в Cosmos DB, к которым требуется получить доступ.
3. Эта информация передается в рабочие узлы Spark.
4. Таким образом, рабочие узлы Spark могут подключаться непосредственно к разделам Cosmos DB для извлечения необходимых данных и переносить эти данные обратно в разделы рабочих узлов Spark.

Важно, что взаимодействие между Spark и Cosmos DB происходит значительно быстрее, так как данные перемещаются между рабочими узлами Spark и узлами данных Cosmos DB (разделами).

### <a name="building-the-spark-to-cosmos-db-connector"></a>Создание соединителя Spark для DocumentDB
Сейчас в проекте соединителя используется maven. Чтобы создать соединитель без зависимостей, выполните следующую команду:
```
mvn clean package
```
Вы также можете скачать последние версии JAR-файла в папку *releases*.

### <a name="including-the-azure-documentdb-spark-jar"></a>Включение JAR-файла Azure DocumentDB Spark
Перед выполнением кода сначала необходимо включить JAR-файл Azure DocumentDB Spark.  Если вы используете **spark-shell**, JAR-файл можно включить с помощью параметра **--jars**.  

```
spark-shell --master $master --jars /$location/azure-documentdb-spark-0.0.1-jar-with-dependencies.jar
```

Команду jar можно также выполнить без зависимостей:

```
spark-shell --master $master --jars /$location/azure-documentdb-spark-0.0.1.jar,/$location/azure-documentdb-1.9.6.jar
```

При использовании записной книжки (например, Azure HDInsight Jupyter) вы можете использовать команды **spark magic**:

```
%%configure
{ "jars": ["wasb:///example/jars/azure-documentdb-1.9.6.jar","wasb:///example/jars/azure-documentdb-spark-0.0.1.jar"],
  "conf": {
    "spark.jars.excludes": "org.scala-lang:scala-reflect"
   }
}
```

Команда **jars** позволяет включить два JAR-файла, необходимые для **azure-documentdb-spark**(и пакета SDK для Java в Azure DocumentDB), и исключает **scala-reflect** для предотвращения конфликта с вызовами Livy (Jupyter Notebook > Livy > Spark).

### <a name="connecting-spark-to-cosmos-db-using-the-connector"></a>Подключение Spark и Cosmos DB с помощью соединителя
Хотя обмен данными происходит сложнее, запрос из Spark в Cosmos DB с помощью соединителя выполняется значительно быстрее.

В следующем фрагменте кода показано, как использовать соединитель в контексте Spark.

```
// Import Necessary Libraries
import org.joda.time._
import org.joda.time.format._
import com.microsoft.azure.documentdb.spark.schema._
import com.microsoft.azure.documentdb.spark._
import com.microsoft.azure.documentdb.spark.config.Config

// Configure connection to your collection
val readConfig2 = Config(Map("Endpoint" -> "https://doctorwho.documents.azure.com:443/",
"Masterkey" -> "le1n99i1w5l7uvokJs3RT5ZAH8dc3ql7lx2CG0h0kK4lVWPkQnwpRLyAN0nwS1z4Cyd1lJgvGUfMWR3v8vkXKA==",
"Database" -> "DepartureDelays",
"preferredRegions" -> "Central US;East US2;",
"Collection" -> "flights_pcoll", 
"SamplingRatio" -> "1.0"))
 
// Create collection connection 
val coll = spark.sqlContext.read.DocumentDB(readConfig2)
coll.createOrReplaceTempView("c")
```

Во фрагменте кода указано:

- **azure-documentdb-spark** содержит все необходимые параметры подключения, в том числе предпочтительные расположения (выбор порядка чтения реплик).
- Импортируйте необходимые библиотеки и настройте главный ключ и узел для создания клиента Cosmos DB.

### <a name="executing-spark-queries-via-the-connector"></a>Выполнение запросов Spark с помощью соединителя

В следующих примерах используется экземпляр Cosmos DB, созданный в предыдущем фрагменте с использованием указанных ключей, доступных только для чтения. Следующий фрагмент кода подключается к коллекции DepartureDelays.flights_pcoll (в учетной записи DoctorWho, как указано ранее) и выполняет запрос для извлечения сведений о задержке рейсов, вылетающих из Сиэтла.

```
// Queries
var query = "SELECT c.date, c.delay, c.distance, c.origin, c.destination FROM c WHERE c.origin = 'SEA'"
val df = spark.sql(query)

// Run DF query (count)
df.count()

// Run DF query (show)
df.show()
```

### <a name="why-use-the-spark-to-cosmos-db-connector-implementation"></a>Зачем использовать соединитель Spark для Cosmos DB?

Подключение к Cosmos DB с помощью соединителя Spark в основном предназначено для следующих сценариев:

* Необходимо использовать Scala (и обновить его для включения оболочки Python, как указано на странице [Issue 3: Add Python wrapper and examples](https://github.com/Azure/azure-documentdb-spark/issues/3) (Проблема 3. Добавление оболочки Python с примерами)).
* Необходимо передать большой объем данных между Apache Spark и Cosmos DB.

Чтобы получить представление о разнице в производительности запросов, см. сведения в статье [Query Test Runs](https://github.com/Azure/azure-documentdb-spark/wiki/Query-Test-Runs) (Тестовые выполнения запросов).

## <a name="distributed-aggregation-example"></a>Пример распределенного агрегирования
В этом разделе приведены некоторые примеры того, как можно выполнить распределенное агрегирование и аналитику с помощью Apache Spark и Azure Cosmos DB.  Обратите внимание, что в Azure Cosmos DB уже реализована поддержка агрегирования, как описано в статье блога [Planet scale aggregates with Azure Cosmos DB](https://azure.microsoft.com/blog/planet-scale-aggregates-with-azure-documentdb/) (Глобальное агрегирование с помощью Azure Cosmos DB). Таким образом с помощью Apache Spark этот процесс можно выполнить на новом уровне.

Обратите внимание, что эти агрегирования относятся к [соединителю Spark для Cosmos DB](https://github.com/Azure/azure-documentdb-spark/blob/master/samples/notebooks/Spark-to-Cosmos DB_Connector.ipynb).

### <a name="connecting-to-flights-sample-data"></a>Подключение к примеру данных о полетах
В этих примерах мы обратимся к данным о выполнении рейсов, которые хранятся в базе данных Cosmos DB **DoctorWho**.  Чтобы подключиться к ней, используйте следующий фрагмент кода:

```
// Import Spark to Cosmos DB connector
import com.microsoft.azure.documentdb.spark.schema._
import com.microsoft.azure.documentdb.spark._
import com.microsoft.azure.documentdb.spark.config.Config

// Connect to Cosmos DB Database
val readConfig2 = Config(Map("Endpoint" -> "https://doctorwho.documents.azure.com:443/",
"Masterkey" -> "le1n99i1w5l7uvokJs3RT5ZAH8dc3ql7lx2CG0h0kK4lVWPkQnwpRLyAN0nwS1z4Cyd1lJgvGUfMWR3v8vkXKA==",
"Database" -> "DepartureDelays",
"preferredRegions" -> "Central US;East US 2;",
"Collection" -> "flights_pcoll", 
"SamplingRatio" -> "1.0"))

// Create collection connection 
val coll = spark.sqlContext.read.DocumentDB(readConfig2)
coll.createOrReplaceTempView("c")
```

С помощью этого фрагмента кода мы также выполним базовый запрос, который передаст отфильтрованный набор данных из Cosmos DB в Spark (где впоследствии можно выполнить распределенные агрегирования).  В этом случае мы запрашиваем сведения об авиарейсах, вылетающих из Сиэтла (SEA).

```
// Run, get row count, and time query
val originSEA = spark.sql("SELECT c.date, c.delay, c.distance, c.origin, c.destination FROM c WHERE c.origin = 'SEA'")
originSEA.createOrReplaceTempView("originSEA")
```

Следующие результаты были получены с помощью запросов от службы Jupyter Notebook.  Обратите внимание, что все фрагменты кода являются универсальными и не относятся к конкретной службе.

### <a name="running-limit-and-count-queries"></a>Выполнение запросов LIMIT и COUNT
Так же как и в случае с SQL или Spark SQL, начнем с запроса **LIMIT**:

![Запрос Spark LIMIT](./media/documentdb-spark-connector/azure-documentdb-spark-sql-query.png)

Следующий запрос является простым и быстрым запросом **COUNT**:

![Запрос Spark COUNT](./media/documentdb-spark-connector/azure-documentdb-spark-count-query.png)

### <a name="group-by-query"></a>Запрос GROUP BY
В этом наборе можно легко выполнять запросы **GROUP BY** к базе данных Cosmos DB:

```
select destination, sum(delay) as TotalDelays 
from originSEA 
group by destination 
order by sum(delay) desc limit 10
```

![График запроса Spark GROUP BY](./media/documentdb-spark-connector/azure-documentdb-group-by-query-graph.png)

### <a name="distinct-order-by-query"></a>Запросы DISTINCT и ORDER BY
Запросы **DISTINCT и ORDER BY** выглядят так:

![График запроса Spark GROUP BY](./media/documentdb-spark-connector/azure-documentdb-order-by-query.png)

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
![График Spark для первых задержанных рейсов](./media/documentdb-spark-connector/azure-documentdb-top-delays-graph.png)


#### <a name="calculate-median-delays-by-destination-cities-departing-from-seattle"></a>Вычисление медианы задержки вылетов из Сиэтла по городам назначения
```
select destination, percentile_approx(delay, 0.5) as median_delay 
from originSEA
where delay < 0 
group by destination 
order by percentile_approx(delay, 0.5)
```

![График медианы задержки Spark](./media/documentdb-spark-connector/azure-documentdb-median-delays-graph.png)

## <a name="next-steps"></a>Дальнейшие действия

Скачайте соединитель Cosmos DB для Spark из репозитория [azure-documentdb-spark](https://github.com/Azure/azure-documentdb-spark) на сайте GitHub и изучите дополнительные ресурсы в этом репозитории:

* [Aggregations Examples](https://github.com/Azure/azure-documentdb-spark/wiki/Aggregations-Examples) (Примеры агрегирований)
* [Sample Scripts and Notebooks](https://github.com/Azure/azure-documentdb-spark/tree/master/samples) (Примеры скриптов и записных книжек)

Дополнительные сведения см. также в [руководстве по SQL, таблицам и наборам данных Apache Spark](http://spark.apache.org/docs/latest/sql-programming-guide.html) и в статье [Начало работы. Создание кластера Apache Spark в Azure HDInsight и выполнение интерактивных запросов с помощью SQL Spark](../hdinsight/hdinsight-apache-spark-jupyter-spark-sql.md).




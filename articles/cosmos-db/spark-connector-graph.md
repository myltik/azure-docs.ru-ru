---
title: Azure Cosmos DB. Аналитика графов с помощью Spark и Apache TinkerPop Gremlin | Документация Майкрософт
description: Эта статья содержит инструкции по настройке и выполнению аналитики графов и параллельного вычисления в Azure Cosmos DB с помощью Spark и TinkerPop SparkGraphComputer.
services: cosmosdb
documentationcenter: ''
author: SnehaGunda
manager: kfile
ms.assetid: 89ea62bb-c620-46d5-baa0-eefd9888557c
ms.service: cosmos-db
ms.custom: quick start connect
ms.workload: ''
ms.tgt_pltfrm: na
ms.devlang: gremlin
ms.topic: article
ms.date: 09/08/2017
ms.author: sngun
ms.openlocfilehash: f44c7d432ac9c07daf9b99dd922f1dcd5de5d58e
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/06/2018
---
# <a name="azure-cosmos-db-perform-graph-analytics-by-using-spark-and-apache-tinkerpop-gremlin"></a>Azure Cosmos DB. Аналитика графов с помощью Spark и Apache TinkerPop Gremlin

[Azure Cosmos DB](introduction.md) — это глобально распределенная многомодельная служба базы данных Майкрософт. Вы можете создавать и запрашивать документы, пары "ключ — значение" и базы данных графов, используя преимущества возможностей глобального распределения и горизонтального масштабирования Azure Cosmos DB. Azure Cosmos DB поддерживает рабочие нагрузки графов оперативной обработки транзакций (OLTP), использующие [Gremlin Apache TinkerPop](graph-introduction.md).

[Spark](http://spark.apache.org/) — это проект Apache Software Foundation, в котором основное внимание уделяется обработке данных оперативной аналитической обработки (OLAP) общего назначения. Spark предоставляет гибридную модель распределенных вычислений в памяти и на дисках, аналогичную модели MapReduce в Hadoop. Apache Spark можно развернуть в облаке с помощью [Azure HDInsight](https://azure.microsoft.com/services/hdinsight/apache-spark/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

Используя одновременно Azure Cosmos DB и Spark, вы можете выполнять рабочие нагрузки OLTP и OLAP с помощью Gremlin. В этом кратком руководстве показано, как выполнять запросы Gremlin в Azure Cosmos DB в кластере Azure HDInsight Spark.

## <a name="prerequisites"></a>предварительным требованиям

Для выполнения этого примера вам потребуется:
* Кластер Azure HDInsight Spark 2.0.
* Пакет JDK 1.8+ (если у вас нет пакета JDK, выполните `apt-get install default-jdk`).
* Maven (если у вас нет Maven, выполните `apt-get install maven`).
* Подписка Azure ([!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]).

Дополнительные сведения о том, как настроить кластер Azure HDInsight Spark, см. в статье [Создание кластеров Hadoop в HDInsight](../hdinsight/hdinsight-hadoop-provision-linux-clusters.md).

## <a name="create-an-azure-cosmos-db-database-account"></a>Создание учетной записи базы данных Azure Cosmos DB

Сначала создайте учетную запись базы данных с помощью API Graph. Для этого сделайте следующее:

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount-graph.md)]

## <a name="add-a-collection"></a>Добавление коллекции

[!INCLUDE [cosmos-db-create-collection](../../includes/cosmos-db-create-graph.md)]

## <a name="get-apache-tinkerpop"></a>Получение Apache TinkerPop

Получите Apache TinkerPop следующим образом:

1. Выполните удаленное подключение к главному узлу кластера HDInsight `ssh tinkerpop3-cosmosdb-demo-ssh.azurehdinsight.net`.

2. Клонируйте исходный код TinkerPop3, создайте его локально и установите в кэш Maven.

    ```bash
    git clone https://github.com/apache/tinkerpop.git
    cd tinkerpop
    mvn clean install
    ```

3. Установите подключаемый модуль Spark-Gremlin. 

    a. За установку подключаемого модуля отвечает Grape. Укажите сведения о репозитории для Grape для скачивания подключаемого модуля и его зависимостей. 

      Создайте файл конфигурации Grape, если его нет в каталоге `~/.groovy/grapeConfig.xml`. Используйте следующие параметры:

    ```xml
    <ivysettings>
    <settings defaultResolver="downloadGrapes"/>
    <resolvers>
        <chain name="downloadGrapes">
        <filesystem name="cachedGrapes">
            <ivy pattern="${user.home}/.groovy/grapes/[organisation]/[module]/ivy-[revision].xml"/>
            <artifact pattern="${user.home}/.groovy/grapes/[organisation]/[module]/[type]s/[artifact]-[revision].[ext]"/>
        </filesystem>
        <ibiblio name="codehaus" root="http://repository.codehaus.org/" m2compatible="true"/>
        <ibiblio name="central" root="http://central.maven.org/maven2/" m2compatible="true"/>
        <ibiblio name="jitpack" root="https://jitpack.io" m2compatible="true"/>
        <ibiblio name="java.net2" root="http://download.java.net/maven/2/" m2compatible="true"/>
        <ibiblio name="apache-snapshots" root="http://repository.apache.org/snapshots/" m2compatible="true"/>
        <ibiblio name="local" root="file:${user.home}/.m2/repository/" m2compatible="true"/>
        </chain>
    </resolvers>
    </ivysettings>
    ``` 

    Б. Запустите консоль Gremlin `bin/gremlin.sh`.
        
    c. Установите подключаемый модуль Spark-Gremlin версии 3.3.0-SNAPSHOT, созданный на предыдущем шаге.

    ```bash
    $ bin/gremlin.sh

            \,,,/
            (o o)
    -----oOOo-(3)-oOOo-----
    plugin activated: tinkerpop.server
    plugin activated: tinkerpop.utilities
    plugin activated: tinkerpop.tinkergraph
    gremlin> :install org.apache.tinkerpop spark-gremlin 3.3.0-SNAPSHOT
    ==>loaded: [org.apache.tinkerpop, spark-gremlin, 3.3.0-SNAPSHOT] - restart the console to use [tinkerpop.spark]
    gremlin> :q
    $ bin/gremlin.sh

            \,,,/
            (o o)
    -----oOOo-(3)-oOOo-----
    plugin activated: tinkerpop.server
    plugin activated: tinkerpop.utilities
    plugin activated: tinkerpop.tinkergraph
    gremlin> :plugin use tinkerpop.spark
    ==>tinkerpop.spark activated
    ```

4. С помощью `:plugin list` активируйте `Hadoop-Gremlin`. Отключите этот подключаемый модуль, так как он может мешать выполнению подключаемого модуля Spark-Gremlin `:plugin unuse tinkerpop.hadoop`.

## <a name="prepare-tinkerpop3-dependencies"></a>Подготовка зависимых компонентов TinkerPop3

При создании TinkerPop3 на предыдущем шаге также были извлечены все зависимые компоненты с форматом JAR Spark и Hadoop в целевом каталоге. Используйте JAR-файлы, предварительно установленные с помощью HDI, и при необходимости извлеките только дополнительные зависимые компоненты.

1. Перейдите в целевой каталог консоли Gremlin: `tinkerpop/gremlin-console/target/apache-tinkerpop-gremlin-console-3.3.0-SNAPSHOT-standalone`. 

2. Переместите все JAR-файлы из каталога `ext/` в каталог `lib/`: `find ext/ -name '*.jar' -exec mv {} lib/ \;`.

3. Удалите все библиотеки формата JAR в `lib/`, которых нет в списке ниже.

    ```bash
    # TinkerPop3
    gremlin-console-3.3.0-SNAPSHOT.jar
    gremlin-core-3.3.0-SNAPSHOT.jar       
    gremlin-groovy-3.3.0-SNAPSHOT.jar     
    gremlin-shaded-3.3.0-SNAPSHOT.jar     
    hadoop-gremlin-3.3.0-SNAPSHOT.jar     
    spark-gremlin-3.3.0-SNAPSHOT.jar      
    tinkergraph-gremlin-3.3.0-SNAPSHOT.jar

    # Gremlin depedencies
    asm-3.2.jar                                
    avro-1.7.4.jar                             
    caffeine-2.3.1.jar                         
    cglib-2.2.1-v20090111.jar                  
    gbench-0.4.3-groovy-2.4.jar                
    gprof-0.3.1-groovy-2.4.jar                 
    groovy-2.4.9-indy.jar                      
    groovy-2.4.9.jar                           
    groovy-console-2.4.9.jar                   
    groovy-groovysh-2.4.9-indy.jar             
    groovy-json-2.4.9-indy.jar                 
    groovy-jsr223-2.4.9-indy.jar               
    groovy-sql-2.4.9-indy.jar                  
    groovy-swing-2.4.9.jar                     
    groovy-templates-2.4.9.jar                 
    groovy-xml-2.4.9.jar                       
    hadoop-yarn-server-nodemanager-2.7.2.jar   
    hppc-0.7.1.jar                             
    javatuples-1.2.jar                         
    jaxb-impl-2.2.3-1.jar                      
    jbcrypt-0.4.jar                            
    jcabi-log-0.14.jar                         
    jcabi-manifests-1.1.jar                    
    jersey-core-1.9.jar                        
    jersey-guice-1.9.jar                       
    jersey-json-1.9.jar                        
    jettison-1.1.jar                           
    scalatest_2.11-2.2.6.jar                   
    servlet-api-2.5.jar                        
    snakeyaml-1.15.jar                         
    unused-1.0.0.jar                           
    xml-apis-1.3.04.jar                        
    ```

## <a name="get-the-azure-cosmos-db-spark-connector"></a>Получение соединителя Spark для Azure Cosmos DB

1. Получите соединитель Spark для Azure Cosmos DB `azure-cosmosdb-spark-0.0.3-SNAPSHOT.jar` и пакет SDK для Java для Cosmos DB `azure-documentdb-1.12.0.jar` из [соединителя Spark для Azure Cosmos DB в GitHub](https://github.com/Azure/azure-cosmosdb-spark/tree/master/releases/azure-cosmosdb-spark-0.0.3_2.0.2_2.11).

2. Кроме того, вы можете создать его локально. Так как последняя версия Spark-Gremlin создана с помощью Spark 1.6.1 и не совместима со Spark версии 2.0.2, которая сейчас используется в соединителе Spark для Azure Cosmos DB, вы можете создать новейший код TinkerPop3 и установить JAR-файлы вручную. Выполните следующее:

    a. Клонируйте соединитель Spark для Azure Cosmos DB.

    Б. Создайте TinkerPop3 (сделано ранее). Установите все JAR-файлы TinkerPop 3.3.0-SNAPSHOT локально.

    ```bash
    mvn install:install-file -Dfile="gremlin-core-3.3.0-SNAPSHOT.jar" -DgroupId=org.apache.tinkerpop -DartifactId=gremlin-core -Dversion=3.3.0-SNAPSHOT -Dpackaging=jar
    mvn install:install-file -Dfile="gremlin-groovy-3.3.0-SNAPSHOT.jar" -DgroupId=org.apache.tinkerpop -DartifactId=gremlin-groovy -Dversion=3.3.0-SNAPSHOT -Dpackaging=jar`
    mvn install:install-file -Dfile="gremlin-shaded-3.3.0-SNAPSHOT.jar" -DgroupId=org.apache.tinkerpop -DartifactId=gremlin-shaded -Dversion=3.3.0-SNAPSHOT -Dpackaging=jar`
    mvn install:install-file -Dfile="hadoop-gremlin-3.3.0-SNAPSHOT.jar" -DgroupId=org.apache.tinkerpop -DartifactId=hadoop-gremlin -Dversion=3.3.0-SNAPSHOT -Dpackaging=jar`
    mvn install:install-file -Dfile="spark-gremlin-3.3.0-SNAPSHOT.jar" -DgroupId=org.apache.tinkerpop -DartifactId=spark-gremlin -Dversion=3.3.0-SNAPSHOT -Dpackaging=jar`
    mvn install:install-file -Dfile="tinkergraph-gremlin-3.3.0-SNAPSHOT.jar" -DgroupId=org.apache.tinkerpop -DartifactId=tinkergraph-gremlin -Dversion=3.3.0-SNAPSHOT -Dpackaging=jar`
    ```

    c. Обновите `tinkerpop.version` `azure-documentdb-spark/pom.xml` до версии `3.3.0-SNAPSHOT`.
    
    d. Выполните сборку с помощью Maven. Необходимые JAR-файлы помещаются в каталоги `target` и `target/alternateLocation`.

    ```bash
    git clone https://github.com/Azure/azure-cosmosdb-spark.git
    cd azure-documentdb-spark
    mvn clean package
    ```

3. Скопируйте упомянутые выше JAR-файлы в локальный каталог ~/azure-documentdb-spark:

    ```bash
    $ azure-documentdb-spark:
    mkdir ~/azure-documentdb-spark
    cp target/azure-documentdb-spark-0.0.3-SNAPSHOT.jar ~/azure-documentdb-spark
    cp target/alternateLocation/azure-documentdb-1.10.0.jar ~/azure-documentdb-spark
    ```

## <a name="distribute-the-dependencies-to-the-spark-worker-nodes"></a>Распространение зависимых компонентов по рабочим узлам Spark 

1. Так как преобразование данных графа зависит от TinkerPop3, вам необходимо распределить связанные зависимости по всем рабочим узлам Spark.

2. Скопируйте зависимости Gremlin, перечисленные выше, JAR-файл соединителя Spark для CosmosDB и пакет SDK для Java для CosmosDB в рабочие узлы следующим образом:

    a. Скопируйте все JAR-файлы в каталог `~/azure-documentdb-spark`.

    ```bash
    $ /home/sshuser/tinkerpop/gremlin-console/target/apache-tinkerpop-gremlin-console-3.3.0-SNAPSHOT-standalone:
    cp lib/* ~/azure-documentdb-spark
    ```

    Б. Получите список всех рабочих узлов Spark, которые можно найти на панели мониторинга Ambari, в списке `Spark2 Clients` в разделе `Spark2`.

    c. Скопируйте этот каталог для каждого узла.

    ```bash
    scp -r ~/azure-documentdb-spark sshuser@wn0-cosmos:/home/sshuser
    scp -r ~/azure-documentdb-spark sshuser@wn1-cosmos:/home/sshuser
    ...
    ```
    
## <a name="set-up-the-environment-variables"></a>Настройка переменных среды

1. Найдите версию HDP кластера Spark. Это имя каталога в разделе `/usr/hdp/` (например, 2.5.4.2-7).

2. Задайте значение hdp.version для всех узлов. На панели мониторинга Ambari перейдите к **разделу YARN** > **Configs (Конфигурации)** > **Advanced** (Дополнительные), а затем сделайте следующее: 
 
    a. В разделе `Custom yarn-site` добавьте новое свойство `hdp.version` со значением версии HDP на главном узле. 
     
    Б. Сохраните конфигурации. Могут появиться предупреждения, которые можно проигнорировать. 
     
    c. Перезапустите службы YARN и Oozie в соответствии с сообщением в значках с уведомлением.

3. Задайте следующие переменные среды на главном узле (замените значения соответствующим образом):

    ```bash
    export HADOOP_GREMLIN_LIBS=/home/sshuser/tinkerpop/gremlin-console/target/apache-tinkerpop-gremlin-console-3.3.0-SNAPSHOT-standalone/ext/spark-gremlin/lib
    export CLASSPATH=$CLASSPATH:$HADOOP_CONF_DIR:/usr/hdp/current/spark2-client/jars/*:/home/sshuser/azure-documentdb-spark/*
    export HDP_VERSION=2.5.4.2-7
    export HADOOP_HOME=${HADOOP_HOME:-/usr/hdp/current/hadoop-client}
    ```

## <a name="prepare-the-graph-configuration"></a>Подготовка конфигурации графа

1. Создайте файл конфигурации с параметрами подключения Azure Cosmos DB и параметрами Spark и поместите его в каталог `tinkerpop/gremlin-console/target/apache-tinkerpop-gremlin-console-3.3.0-SNAPSHOT-standalone/conf/hadoop/gremlin-spark.properties`.

    ```
    gremlin.graph=org.apache.tinkerpop.gremlin.hadoop.structure.HadoopGraph
    gremlin.hadoop.jarsInDistributedCache=true
    gremlin.hadoop.defaultGraphComputer=org.apache.tinkerpop.gremlin.spark.process.computer.SparkGraphComputer

    gremlin.hadoop.graphReader=com.microsoft.azure.documentdb.spark.gremlin.DocumentDBInputRDD
    gremlin.hadoop.graphWriter=com.microsoft.azure.documentdb.spark.gremlin.DocumentDBOutputRDD

    ####################################
    # SparkGraphComputer Configuration #
    ####################################
    spark.master=yarn
    spark.executor.memory=3g
    spark.executor.instances=6
    spark.serializer=org.apache.spark.serializer.KryoSerializer
    spark.kryo.registrator=org.apache.tinkerpop.gremlin.spark.structure.io.gryo.GryoRegistrator
    gremlin.spark.persistContext=true

    # Classpath for the driver and executors
    spark.driver.extraClassPath=/usr/hdp/current/spark2-client/jars/*:/home/sshuser/azure-documentdb-spark/*
    spark.executor.extraClassPath=/usr/hdp/current/spark2-client/jars/*:/home/sshuser/azure-documentdb-spark/*
    
    ######################################
    # DocumentDB Spark connector         #
    ######################################
    spark.documentdb.connectionMode=Gateway
    spark.documentdb.schema_samplingratio=1.0
    spark.documentdb.Endpoint=https://FILLIN.documents.azure.com:443/
    spark.documentdb.Masterkey=FILLIN
    spark.documentdb.Database=FILLIN
    spark.documentdb.Collection=FILLIN
    spark.documentdb.preferredRegions=FILLIN
    ```

2. Обновите значения `spark.driver.extraClassPath` и `spark.executor.extraClassPath`, чтобы включить каталог JAR-файлов, распределенных на предыдущем шаге (в этом случае `/home/sshuser/azure-documentdb-spark/*`).

3. Укажите следующие сведения для Azure Cosmos DB:

    ```
    spark.documentdb.Endpoint=https://FILLIN.documents.azure.com:443/
    spark.documentdb.Masterkey=FILLIN
    spark.documentdb.Database=FILLIN
    spark.documentdb.Collection=FILLIN
    # Optional
    #spark.documentdb.preferredRegions=West\ US;West\ US\ 2
    ```
   
## <a name="load-the-tinkerpop-graph-and-save-it-to-azure-cosmos-db"></a>Загрузка графа TinkerPop и его сохранение в Azure Cosmos DB
Чтобы продемонстрировать, как можно сохранить граф в Azure Cosmos DB, в этом примере используется предопределенный современный граф TinkerPop. Граф сохранен в формате Kryo и добавлен в репозиторий TinkerPop.

1. Так как вы работаете с Gremlin в режиме YARN, необходимо сделать данные графа доступными в файловой системе Hadoop. Используйте команды ниже, чтобы создать каталог, а затем скопируйте в него локальный файл графа. 

    ```bash
    $ tinkerpop:
    hadoop fs -mkdir /graphData
    hadoop fs -copyFromLocal ~/tinkerpop/data/tinkerpop-modern.kryo /graphData/tinkerpop-modern.kryo
    ```

2. Временно обновите файл `gremlin-spark.properties`, чтобы использовать `GryoInputFormat` для считывания графа. Кроме того, укажите `inputLocation` как созданный каталог следующим образом:

    ```
    gremlin.hadoop.graphReader=org.apache.tinkerpop.gremlin.hadoop.structure.io.gryo.GryoInputFormat
    gremlin.hadoop.inputLocation=/graphData/tinkerpop-modern.kryo
    ```

3. Запустите консоль Gremlin и создайте следующие шаги вычисления, чтобы сохранить данные в настроенную коллекцию Azure Cosmos DB:  

    a. Создайте граф `graph = GraphFactory.open("conf/hadoop/gremlin-spark.properties")`.

    Б. Используйте SparkGraphComputer для записи: `graph.compute(SparkGraphComputer.class).result(GraphComputer.ResultGraph.NEW).persist(GraphComputer.Persist.EDGES).program(TraversalVertexProgram.build().traversal(graph.traversal().withComputer(Computer.compute(SparkGraphComputer.class)),"gremlin-groovy","g.V()").create(graph)).submit().get()`.

    ```bash
    gremlin> graph = GraphFactory.open("conf/hadoop/gremlin-spark.properties")
    ==>hadoopgraph[gryoinputformat->documentdboutputrdd]
    gremlin> hg = graph.
                compute(SparkGraphComputer.class).
                result(GraphComputer.ResultGraph.NEW).
                persist(GraphComputer.Persist.EDGES).
                program(TraversalVertexProgram.build().
                    traversal(graph.traversal().withComputer(Computer.compute(SparkGraphComputer.class)), "gremlin-groovy", "g.V()").
                    create(graph)).
                submit().
                get() 
    ==>result[hadoopgraph[documentdbinputrdd->documentdboutputrdd],memory[size:1]]
    ```

4. С помощью обозревателя данных можно проверить, сохранены ли данные в Azure Cosmos DB.

## <a name="load-the-graph-from-azure-cosmos-db-and-run-gremlin-queries"></a>Загрузка графа из Azure Cosmos DB и выполнение запросов Gremlin

1. Для загрузки графа измените `gremlin-spark.properties`, чтобы задать параметру `graphReader` значение `DocumentDBInputRDD`:

    ```
    gremlin.hadoop.graphReader=com.microsoft.azure.documentdb.spark.gremlin.DocumentDBInputRDD
    ```

2. Загрузите граф, просмотрите данные и выполните запросы Gremlin следующим образом:

    a. Запустите консоль Gremlin `bin/gremlin.sh`.

    Б. Создайте граф с помощью конфигурации `graph = GraphFactory.open('conf/hadoop/gremlin-spark.properties')`.

    c. Создайте обход графа со SparkGraphComputer `g = graph.traversal().withComputer(SparkGraphComputer)`.

    d. Выполните следующие запросы графа Gremlin:

    ```bash
    gremlin> graph = GraphFactory.open("conf/hadoop/gremlin-spark.properties")
    ==>hadoopgraph[documentdbinputrdd->documentdboutputrdd]
    gremlin> g = graph.traversal().withComputer(SparkGraphComputer)
    ==>graphtraversalsource[hadoopgraph[documentdbinputrdd->documentdboutputrdd], sparkgraphcomputer]
    gremlin> g.V().count()
    ==>6
    gremlin> g.E().count()
    ==>6
    gremlin> g.V(1).out().values('name')
    ==>josh
    ==>vadas
    ==>lop
    gremlin> g.V().hasLabel('person').coalesce(values('nickname'), values('name'))
    ==>josh
    ==>peter
    ==>vadas
    ==>marko
    gremlin> g.V().hasLabel('person').
            choose(values('name')).
                option('marko', values('age')).
                option('josh', values('name')).
                option('vadas', valueMap()).
                option('peter', label())
    ==>josh
    ==>person
    ==>[name:[vadas],age:[27]]
    ==>29
    ```

> [!NOTE]
> Чтобы увидеть более подробные сведения журнала, задайте более подробный уровень ведения журнала в каталоге `conf/log4j-console.properties`.
>

## <a name="next-steps"></a>Дополнительная информация

Из этого краткого руководства вы узнали, как работать с графами с помощью Azure Cosmos DB и Spark.

> [!div class="nextstepaction"]

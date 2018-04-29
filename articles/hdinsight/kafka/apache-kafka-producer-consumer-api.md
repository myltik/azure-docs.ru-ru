---
title: Использование API производителя и потребителя Apache Kafka (Azure HDInsight) | Документация Майкрософт
description: Узнайте, как использовать API производителя и потребителя Apache Kafka для Kafka в HDInsight. Эти API-интерфейсы позволяют разрабатывать приложения, в которых используется Apache Kafka для чтения и записи сообщений.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: cgronlun
editor: cgronlun
tags: azure-portal
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/10/2018
ms.author: larryfr
ms.openlocfilehash: 01592401c4c88adeed49b11df4e7963e27b1bcee
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2018
---
# <a name="apache-kafka-producer-and-consumer-apis"></a>Интерфейсы API производителя и потребителя Apache Kafka

Узнайте, как создать приложение, в котором используется API производителя и потребителя Kafka для Kafka в HDInsight.

Документацию по интерфейсам API см. в разделах [об API производителя](https://kafka.apache.org/documentation/#producerapi) и [API потребителя](https://kafka.apache.org/documentation/#consumerapi).

## <a name="set-up-your-development-environment"></a>Настройка среды разработки

В среде разработки необходимо установить следующие компоненты:

* [Java JDK 8](http://www.oracle.com/technetwork/java/javase/downloads/index.html) или эквивалент, например OpenJDK.

* [Apache Maven](http://maven.apache.org/)

* Клиент SSH и команда `scp`. Дополнительные сведения см. в статье [Подключение к HDInsight (Hadoop) с помощью SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="set-up-your-deployment-environment"></a>Настройка окружения развертывания

Для выполнения этого примера требуется Kafka в HDInsight версии 3.6. Чтобы узнать, как создавать Kafka в кластере HDInsight, см. статью [Приступая к работе с Apache Kafka в HDInsight](apache-kafka-get-started.md).

## <a name="build-and-deploy-the-example"></a>Создание и развертывание примера

1. Скачайте примеры по адресу [https://github.com/Azure-Samples/hdinsight-kafka-java-get-started](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started).

2. Откройте каталог `Producer-Consumer` и выполните следующую команду.

    ```
    mvn clean package
    ```

    Эта команда создает каталог с именем `target`, который содержит файл с именем `kafka-producer-consumer-1.0-SNAPSHOT.jar`.

3. С помощью следующих команд скопируйте файл `kafka-producer-consumer-1.0-SNAPSHOT.jar` в свой кластер HDInsight.
   
    ```bash
    scp ./target/kafka-producer-consumer-1.0-SNAPSHOT.jar SSHUSER@CLUSTERNAME-ssh.azurehdinsight.net:kafka-producer-consumer.jar
    ```
   
    Замените **SSHUSER** именем пользователя SSH для кластера, а **CLUSTERNAME** — именем кластера. При появлении запроса введите пароль пользователя SSH.

## <a id="run"></a>Выполнение примера

1. Чтобы открыть SSH-подключение к кластеру, выполните следующую команду:

    ```bash
    ssh SSHUSER@CLUSTERNAME-ssh.azurehdinsight.net
    ```

    Замените **SSHUSER** именем пользователя SSH для кластера, а **CLUSTERNAME** — именем кластера. При появлении запроса введите пароль для учетной записи пользователя SSH. Дополнительные сведения об использовании `scp` с HDInsight см. в статье [Подключение к HDInsight (Hadoop) с помощью SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. Чтобы создать разделы Kafka, применяемые в этом примере, используйте следующие команды:

    ```bash
    sudo apt -y install jq

    CLUSTERNAME='your cluster name'

    export KAFKAZKHOSTS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2`

    export KAFKABROKERS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`

    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic test --zookeeper $KAFKAZKHOSTS
    ```

    Замените строку __your cluster name__ именем кластера HDInsight. При появлении запроса введите пароль от учетной записи кластера HDInsight.

    > [!NOTE]
    > Если имя для входа в кластер отличается от значения по умолчанию `admin`, замените значение `admin` в предыдущих командах на имя для входа в кластер.

3. Чтобы запустить производитель и сохранить данные в разделе, выполните следующую команду:

    ```bash
    java -jar kafka-producer-consumer.jar producer $KAFKABROKERS
    ```

4. Когда процесс производителя завершится, выполните следующую команду для считывания данных из раздела:
   
    ```bash
    java -jar kafka-producer-consumer.jar consumer $KAFKABROKERS
    ```
   
    Отобразятся считанные записи, а также их количество.

5. Нажмите клавиши __Ctrl+C__, чтобы закрыть потребитель.

### <a name="multiple-consumers"></a>Несколько потребителей

При считывании записей объект-получатель Kafka использует группу объектов-получателей. Использование одной группы с несколькими потребителями приведет к считыванию записей с балансировкой нагрузки из раздела. Каждый потребитель в группе получает часть записей.

Приложение-потребитель принимает параметр, который используется как идентификатор группы. Например, следующая команда предназначена для запуска нового потребителя с идентификатором группы `mygroup`.
   
```bash
java -jar kafka-producer-consumer.jar consumer $KAFKABROKERS mygroup
```

Чтобы увидеть этот процесс в действии, выполните следующие шаги:

1. Повторите шаги 1 и 2 из раздела [Выполнение примера](#run), чтобы открыть новый сеанс SSH.

2. В обоих сеансах SSH введите следующую команду:

    ```bash
    java -jar kafka-producer-consumer.jar consumer $KAFKABROKERS mygroup
    ```
    
    > [!IMPORTANT]
    > Обе команды следует запустить одновременно, чтобы они выполнялись параллельно.

    Обратите внимание, что количество считанных сообщений будет отличаться от количества в примере из предыдущего раздела, где работал только один потребитель. Теперь считываемые сообщения распределяются между экземплярами.

Потребление по клиентам, входящих в одну группу, обрабатывается по секциям раздела. Созданный ранее раздел `test` содержит восемь секций. Если открыть восемь сеансов SSH и запустить в них потребителей, каждый потребитель будет считывать записи из одной секции этого раздела.

> [!IMPORTANT]
> Число экземпляров потребителя, входящих в группу потребителей, должно соответствовать числу секций. В этом примере одна группа объектов-получателей может содержать не больше восьми объектов-получателей, так как это число секций в разделе. Но у вас может быть несколько групп объектов-получателей, в каждую из которых входит не более восьми из них.

Записи хранятся в Kafka в том порядке, в котором они поступают в секцию. Чтобы обеспечить упорядоченную доставку записей *в секцию*, нужно создать группу потребителей. При этом число входящих в нее экземпляров потребителя должно соответствовать числу секций. Чтобы обеспечить упорядоченную доставку записей *в раздел*, нужно создать группу потребителей, которая содержит только один экземпляр потребителя.

## <a name="next-steps"></a>Дополнительная информация

Из этого документа вы узнали, как использовать API производителя и потребителя Kafka для Kafka в HDInsight. Дополнительные сведения о работе с Kafka см. в следующих материалах.

* [Анализ журналов Kafka](apache-kafka-log-analytics-operations-management.md)
* [Репликация разделов Apache Kafka с помощью Kafka в HDInsight (предварительная версия) и MirrorMaker](apache-kafka-mirroring.md)
* [API потоков Kafka в HDInsight](apache-kafka-streams-api.md)
* [Пример потоковой передачи Apache Spark (DStream) с использованием Kafka (предварительная версия) в HDInsight](../hdinsight-apache-spark-with-kafka.md)
* [Использование структурированной потоковой передачи Spark с Kafka (предварительная версия) в HDInsight](../hdinsight-apache-kafka-spark-structured-streaming.md)
* [Использование структурированной потоковой передачи Apache Spark из Kafka HDInsight в Azure Cosmos DB](../apache-kafka-spark-structured-streaming-cosmosdb.md)
* [Совместное использование Apache Kafka (предварительная версия) и Storm в HDInsight](../hdinsight-apache-storm-with-kafka.md)
* [Подключение к Kafka через виртуальную сеть Azure](apache-kafka-connect-vpn-gateway.md)
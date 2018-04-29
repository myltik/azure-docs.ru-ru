---
title: Использование API Потоков Apache Kafka в Azure HDInsight | Документация Майкрософт
description: Узнайте, как использовать API Потоков Apache Kafka в HDInsight. Этот API позволяет выполнять потоковую обработку между разделами в Kafka.
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
ms.openlocfilehash: 36d67cdb99871f3948db1f6497b1a4638df4f3f1
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2018
---
# <a name="apache-kafka-streams-api"></a>API Потоков Apache Kafka

Узнайте, как создать приложение, использующее API Потоков Kafka и запустить его с Kafka в HDInsight.

При работе с Apache Kafka потоковая обработка часто выполняется с помощью Apache Spark или Storm. В Kafka версии 0.10.0 (в HDInsight 3.5 и 3.6) появился API Потоков Kafka. Этот API позволяет преобразовать потоки данных между входными и выходными разделами с помощью приложения, выполняемого в Kafka. В некоторых случаях это может быть альтернативой созданию решения потоковой передачи Spark или Storm. Дополнительные сведения о Потоках Kafka см. в [вводной документации ](https://kafka.apache.org/10/documentation/streams/) на сайте Apache.org.

## <a name="set-up-your-development-environment"></a>Настройка среды разработки

В среде разработки необходимо установить следующие компоненты:

* [Java JDK 8](http://www.oracle.com/technetwork/java/javase/downloads/index.html) или эквивалент, например OpenJDK.

* [Apache Maven](http://maven.apache.org/)

* Клиент SSH и команда `scp`. Дополнительные сведения см. в статье [Подключение к HDInsight (Hadoop) с помощью SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="set-up-your-deployment-environment"></a>Настройка окружения развертывания

Для выполнения этого примера требуется Kafka в HDInsight версии 3.6. Чтобы узнать, как создавать Kafka в кластере HDInsight, см. статью [Приступая к работе с Apache Kafka в HDInsight](apache-kafka-get-started.md).

## <a name="build-and-deploy-the-example"></a>Создание и развертывание примера

Чтобы создать и развернуть проект для Kafka в кластере HDInsight, выполните следующие действия.

1. Скачайте примеры по адресу [https://github.com/Azure-Samples/hdinsight-kafka-java-get-started](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started).

2. Измените каталоги на каталог `Streaming`, а затем используйте следующую команду, чтобы создать пакет JAR:

    ```bash
    mvn clean package
    ```

    Эта команда создает каталог с именем `target`, который содержит файл с именем `kafka-streaming-1.0-SNAPSHOT.jar`.

3. Используя следующую команду, скопируйте файл `kafka-streaming-1.0-SNAPSHOT.jar` в свой кластер HDInsight.
   
    ```bash
    scp ./target/kafka-streaming-1.0-SNAPSHOT.jar SSHUSER@CLUSTERNAME-ssh.azurehdinsight.net:kafka-streaming.jar
    ```
   
    Замените **SSHUSER** именем пользователя SSH для кластера, а **CLUSTERNAME** — именем кластера. При появлении запроса введите пароль для учетной записи пользователя SSH. Дополнительные сведения об использовании `scp` с HDInsight см. в статье [Подключение к HDInsight (Hadoop) с помощью SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="run-the-example"></a>Выполнение примера

1. Чтобы открыть SSH-подключение к кластеру, выполните следующую команду:

    ```bash
    ssh SSHUSER@CLUSTERNAME-ssh.azurehdinsight.net
    ```

    Замените **SSHUSER** именем пользователя SSH для кластера, а **CLUSTERNAME** — именем кластера. При появлении запроса введите пароль для учетной записи пользователя SSH. Дополнительные сведения об использовании `scp` с HDInsight см. в статье [Подключение к HDInsight (Hadoop) с помощью SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

4. Чтобы создать разделы Kafka, применяемые в этом примере, используйте следующие команды:

    ```bash
    sudo apt -y install jq

    CLUSTERNAME='your cluster name'

    export KAFKAZKHOSTS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2`

    export KAFKABROKERS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`

    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic test --zookeeper $KAFKAZKHOSTS

    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic wordcounts --zookeeper $KAFKAZKHOSTS
    ```

    Замените строку __your cluster name__ именем кластера HDInsight. При появлении запроса введите пароль от учетной записи кластера HDInsight.

    > [!NOTE]
    > Если имя для входа в кластер отличается от значения по умолчанию `admin`, замените значение `admin` в предыдущих командах на имя для входа в кластер.

5. Чтобы выполнить этот пример, необходимо сделать следующее:

    * Запустите решение Потоков, содержащееся в `kafka-streaming.jar`.
    * Запустите отправитель, который записывает данные в раздел `test`.
    * Запустите объект-получатель, чтобы просмотреть выходные данные, записанные в разделе `wordcounts`.

    > [!NOTE]
    > Убедитесь, что в файле конфигурации брокера Kafka для свойства `auto.create.topics.enable` задано значение `true`. Это свойство можно просмотреть и изменить в файле расширенной конфигурации брокера Kafka с помощью веб-интерфейса Ambari. В противном случае необходимо вручную создать промежуточный раздел `RekeyedIntermediateTopic` перед запуском этого примера, используя следующую команду:
    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic RekeyedIntermediateTopic  --zookeeper $KAFKAZKHOSTS
    ```
    
    Эти операции можно выполнить, открыв три сеанса SSH. Однако затем необходимо задать значения `$KAFKABROKERS` и `$KAFKAZKHOSTS`, выполнив шаг 4 из этого раздела в каждом сеансе SSH. Более простое решение — использовать программу `tmux`, которая может разделить текущее отображение SSH на несколько секций. Чтобы запустить потоковую передачу, объект-получатель и отправитель с помощью `tmux`, используйте следующую команду:

    ```bash
    tmux new-session '/usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --bootstrap-server $KAFKABROKERS --topic wordcounts --formatter kafka.tools.DefaultMessageFormatter --property print.key=true --property key.deserializer=org.apache.kafka.common.serialization.StringDeserializer --property value.deserializer=org.apache.kafka.common.serialization.LongDeserializer' \; split-window -h 'java -jar kafka-streaming.jar $KAFKABROKERS $KAFKAZKHOSTS' \; split-window -v '/usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list $KAFKABROKERS --topic test' \; attach
    ```

    Эта команда разбивает отображения SSH на три секции:

    * В левой секции выполняется объект-получатель консоли, читающий сообщения в разделе `wordcounts`: `/usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --bootstrap-server $KAFKABROKERS --topic wordcounts --formatter kafka.tools.DefaultMessageFormatter --property print.key=true --property key.deserializer=org.apache.kafka.common.serialization.StringDeserializer --property value.deserializer=org.apache.kafka.common.serialization.LongDeserializer`

        > [!NOTE]
        > В соответствии с параметрами `--property` объект-получатель консоли печатает ключ (машинное слово) и число (значение). Кроме того, этот параметр настраивает десериализатор, используемый при считывании этих значений из Kafka.

    * Вверху справа запускается решение API Потоков: `java -jar kafka-streaming.jar $KAFKABROKERS $KAFKAZKHOSTS`

    * В правой нижней секции выполняется отправитель консоли и ожидает ввода сообщения для отправки в раздел `test`: `/usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list $KAFKABROKERS --topic test`
 
6. После того, как команда `tmux` разделит отображение, курсор будет находиться в нижней правой секции. Начните вводить предложения. После каждого предложения панель слева обновляется и отображает количество уникальных слов. Результат будет аналогичен приведенному ниже:
   
        dwarfs  13635
        ago     13664
        snow    13636
        dwarfs  13636
        ago     13665
        a       13803
        ago     13666
        a       13804
        ago     13667
        ago     13668
        jumped  13640
        jumped  13641
   
    > [!NOTE]
    > Число увеличивается каждый раз, когда в сообщении встречается слово.

7. Нажмите клавиши __Ctrl+C__, чтобы закрыть отправитель. Снова нажмите клавиши __Ctrl+C__, чтобы выйти из приложения и объекта-получателя.

## <a name="next-steps"></a>Дополнительная информация

В этом документе вы узнали, как использовать API Потоков Kafka с Kafka в HDInsight. Дополнительные сведения о работе с Kafka см. в следующих материалах.

* [Анализ журналов Kafka](apache-kafka-log-analytics-operations-management.md)
* [Репликация разделов Apache Kafka с помощью Kafka в HDInsight (предварительная версия) и MirrorMaker](apache-kafka-mirroring.md)
* [Apache Kafka Producer and Consumer APIs](apache-kafka-producer-consumer-api.md) (API-интерфейсы отправителя и объекта-получателя Apache Kafka)
* [Пример потоковой передачи Apache Spark (DStream) с использованием Kafka (предварительная версия) в HDInsight](../hdinsight-apache-spark-with-kafka.md)
* [Использование структурированной потоковой передачи Spark с Kafka (предварительная версия) в HDInsight](../hdinsight-apache-kafka-spark-structured-streaming.md)
* [Использование структурированной потоковой передачи Apache Spark из Kafka HDInsight в Azure Cosmos DB](../apache-kafka-spark-structured-streaming-cosmosdb.md)
* [Совместное использование Apache Kafka (предварительная версия) и Storm в HDInsight](../hdinsight-apache-storm-with-kafka.md)
* [Подключение к Kafka через виртуальную сеть Azure](apache-kafka-connect-vpn-gateway.md)

---
title: Руководство. Использование API производителя и потребителя Apache Kafka (Azure HDInsight) | Документация Майкрософт
description: Узнайте, как использовать API производителя и потребителя Apache Kafka для Kafka в HDInsight. В этом руководстве вы узнаете, как использовать эти API с Kafka в HDInsight из приложения Java.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: cgronlun
editor: cgronlun
tags: azure-portal
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: tutorial
ms.date: 04/16/2018
ms.author: larryfr
ms.openlocfilehash: b602f8bfe316e9c11dbff18273f37c99407c3da6
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/07/2018
ms.locfileid: "33771156"
---
# <a name="tutorial-use-the-apache-kafka-producer-and-consumer-apis"></a>Руководство. Использование API производителя и потребителя Apache Kafka

Здесь содержатся сведения об использовании API производителя и потребителя Kafka в HDInsight.

API производителя Kafka позволяет приложениям отправлять потоки данных в кластер Kafka. API потребителя Kafka позволяет приложениям считывать потоки данных из кластера.

Из этого руководства вы узнаете, как выполнять такие задачи:

> [!div class="checklist"]
> * Настройка среды разработки
> * Настройка окружения развертывания
> * Изучение кода
> * Создание и развертывание приложения
> * Запуск приложения в кластере

Дополнительные сведения об интерфейсах API см. в документации [по API производителя](https://kafka.apache.org/documentation/#producerapi) и [API потребителя](https://kafka.apache.org/documentation/#consumerapi).

## <a name="set-up-your-development-environment"></a>Настройка среды разработки

В среде разработки необходимо установить следующие компоненты:

* [Java JDK 8](http://www.oracle.com/technetwork/java/javase/downloads/index.html) или эквивалент, например OpenJDK.

* [Apache Maven](http://maven.apache.org/)

* Клиент SSH и команда `scp`. Дополнительные сведения см. в статье [Подключение к HDInsight (Hadoop) с помощью SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

* Текстовый редактор или Java IDE.

Во время установки Java и JDK на компьютере, где ведется разработка, могут быть установлены следующие переменные среды. Однако следует убедиться, что они существуют и что они содержат правильные значения для вашей системы.

* `JAVA_HOME`. Эта переменная должна указывать на каталог, в который установлен JDK.
* `PATH`. Эта переменная должна содержать следующие пути:
  
    * `JAVA_HOME` или эквивалентный путь.
    * `JAVA_HOME\bin` или эквивалентный путь.
    * Каталог, в который установлено ПО Maven.

## <a name="set-up-your-deployment-environment"></a>Настройка окружения развертывания

Для выполнения этого руководства требуется Kafka в HDInsight версии 3.6. Чтобы узнать, как создавать Kafka в кластере HDInsight, см. статью [Приступая к работе с Apache Kafka в HDInsight](apache-kafka-get-started.md).

## <a name="understand-the-code"></a>Изучение кода

Пример приложения расположен в подкаталоге `Producer-Consumer` по адресу [https://github.com/Azure-Samples/hdinsight-kafka-java-get-started](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started). Приложение состоит в основном из четырех файлов:

* файл `pom.xml` определяет зависимости проекта, версию Java и методы упаковки;
* файл `Producer.java` отправляет 1 миллион случайных предложений в Kafka, используя API производителя;
* файл `Consumer.java` использует API потребителя для чтения данных из Kafka и передачи их в STDOUT;
* интерфейс командной строки `Run.java` используется для выполнения кода потребителя и производителя.

### <a name="pomxml"></a>Pom.xml

В файле `pom.xml` важны следующие элементы:

* Зависимости. Этот проект использует API потребителя и производителя Kafka, предоставленные в пакете `kafka-clients`. Приведенный ниже код XML определяет эту зависимость:

    ```xml
    <!-- Kafka client for producer/consumer operations -->
    <dependency>
      <groupId>org.apache.kafka</groupId>
      <artifactId>kafka-clients</artifactId>
      <version>${kafka.version}</version>
    </dependency>
    ```

    > [!NOTE]
    > Запись `${kafka.version}` объявлена в разделе `<properties>..</properties>` файла `pom.xml`. Она настроена для версии Kafka кластера HDInsight.

* Подключаемые модули. Подключаемые модули Maven предоставляют различные возможности. В этом проекте используются следующие подключаемые модули:

    * С помощью модуля `maven-compiler-plugin` можно задать для проекта Java версии 8. Это версия Java, используемая в HDInsight 3.6.
    * Модуль `maven-shade-plugin` используется для создания файла типа uber jar, содержащего это приложение, а также любые зависимости. Он также используется для установки точки входа приложения, с помощью которой вы сможете напрямую запускать JAR-файл, не указывая основной класс.

### <a name="producerjava"></a>Producer.java

Производитель взаимодействует с узлами брокера Kafka (рабочие узлы) для хранения данных в разделе Kafka. Следующий фрагмент кода взят из файла `Producer.java`:

```java
package com.microsoft.example;

import org.apache.kafka.clients.producer.KafkaProducer;
import org.apache.kafka.clients.producer.ProducerRecord;
import java.util.Properties;
import java.util.Random;
import java.io.IOException;

public class Producer
{
    public static void produce(String brokers) throws IOException
    {

        // Set properties used to configure the producer
        Properties properties = new Properties();
        // Set the brokers (bootstrap servers)
        properties.setProperty("bootstrap.servers", brokers);
        // Set how to serialize key/value pairs
        properties.setProperty("key.serializer","org.apache.kafka.common.serialization.StringSerializer");
        properties.setProperty("value.serializer","org.apache.kafka.common.serialization.StringSerializer");
        KafkaProducer<String, String> producer = new KafkaProducer<>(properties);

        // So we can generate random sentences
        Random random = new Random();
        String[] sentences = new String[] {
                "the cow jumped over the moon",
                "an apple a day keeps the doctor away",
                "four score and seven years ago",
                "snow white and the seven dwarfs",
                "i am at two with nature"
        };

        String progressAnimation = "|/-\\";
        // Produce a bunch of records
        for(int i = 0; i < 1000000; i++) {
            // Pick a sentence at random
            String sentence = sentences[random.nextInt(sentences.length)];
            // Send the sentence to the test topic
            producer.send(new ProducerRecord<String, String>("test", sentence));
            String progressBar = "\r" + progressAnimation.charAt(i % progressAnimation.length()) + " " + i;
            System.out.write(progressBar.getBytes());
        }
    }
}
```

Этот код подключается к узлам брокера Kafka (рабочие узлы), а затем отправляет 1 000 000 приложений в Kafka, используя API производителя.

### <a name="consumerjava"></a>Consumer.java

Потребитель взаимодействует с узлами брокера Kafka (рабочие узлы) и считывает записи в цикле. Следующий фрагмент кода взят из файла `Consumer.java`:

```java
package com.microsoft.example;

import org.apache.kafka.clients.consumer.KafkaConsumer;
import org.apache.kafka.clients.consumer.ConsumerRecords;
import org.apache.kafka.clients.consumer.ConsumerRecord;
import java.util.Properties;
import java.util.Arrays;

public class Consumer {
    public static void consume(String brokers, String groupId) {
        // Create a consumer
        KafkaConsumer<String, String> consumer;
        // Configure the consumer
        Properties properties = new Properties();
        // Point it to the brokers
        properties.setProperty("bootstrap.servers", brokers);
        // Set the consumer group (all consumers must belong to a group).
        properties.setProperty("group.id", groupId);
        // Set how to serialize key/value pairs
        properties.setProperty("key.deserializer","org.apache.kafka.common.serialization.StringDeserializer");
        properties.setProperty("value.deserializer","org.apache.kafka.common.serialization.StringDeserializer");
        // When a group is first created, it has no offset stored to start reading from. This tells it to start
        // with the earliest record in the stream.
        properties.setProperty("auto.offset.reset","earliest");
        consumer = new KafkaConsumer<>(properties);

        // Subscribe to the 'test' topic
        consumer.subscribe(Arrays.asList("test"));

        // Loop until ctrl + c
        int count = 0;
        while(true) {
            // Poll for records
            ConsumerRecords<String, String> records = consumer.poll(200);
            // Did we get any?
            if (records.count() == 0) {
                // timeout/nothing to read
            } else {
                // Yes, loop over records
                for(ConsumerRecord<String, String> record: records) {
                    // Display record and count
                    count += 1;
                    System.out.println( count + ": " + record.value());
                }
            }
        }
    }
}
```

В этом коде потребитель настроен для чтения с самого начала раздела (`auto.offset.reset` имеет значение `earliest`).

### <a name="runjava"></a>Run.java

Файл `Run.java` предоставляет интерфейс командной строки, который выполняет код производителя или потребителя. Нужно указать сведения об узле брокера Kafka в качестве параметра. При необходимости можно указать значение идентификатора группы, которое используется процессом потребителя. При создании нескольких экземпляров потребителя с одним и тем же идентификатором группы между ними будет распределяться нагрузка по операциям чтения из раздела.

```java
package com.microsoft.example;

import java.io.IOException;
import java.util.UUID;

// Handle starting producer or consumer
public class Run {
    public static void main(String[] args) throws IOException {
        if(args.length < 2) {
            usage();
        }

        // Get the brokers
        String brokers = args[1];
        switch(args[0].toLowerCase()) {
            case "producer":
                Producer.produce(brokers);
                break;
            case "consumer":
                // Either a groupId was passed in, or we need a random one
                String groupId;
                if(args.length == 3) {
                    groupId = args[2];
                } else {
                    groupId = UUID.randomUUID().toString();
                }
                Consumer.consume(brokers, groupId);
                break;
            default:
                usage();
        }
        System.exit(0);
    }
    // Display usage
    public static void usage() {
        System.out.println("Usage:");
        System.out.println("kafka-example.jar <producer|consumer> brokerhosts [groupid]");
        System.exit(1);
    }
}
```

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

2. Чтобы создать разделы Kafka, применяемые в этом примере, выполните следующие действия:

    1. Чтобы сохранить имя кластера в переменной и установить служебную программу синтаксического анализа JSON (`jq`), используйте следующие команды. При появлении запроса введите имя кластера Kafka:
    
        ```bash
        sudo apt -y install jq
        read -p 'Enter your Kafka cluster name:' CLUSTERNAME
        ```
    
    2. Чтобы получить узлы Zookeeper и брокера Kafka, используйте следующие команды. При появлении запроса введите пароль для учетной записи администратора, чтобы войти на кластер.
    
        ```bash
        export KAFKAZKHOSTS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2`; \
        export KAFKABROKERS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`; \
        ```

    3. Чтобы создать раздел `test`, используйте следующую команду:

        ```bash
        /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic test --zookeeper $KAFKAZKHOSTS
        ```

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

Чтобы увидеть этот процесс в действии, выполните следующую команду:

```bash
tmux new-session 'java -jar kafka-producer-consumer.jar consumer $KAFKABROKERS mygroup' \; split-w
indow -h 'java -jar kafka-producer-consumer.jar consumer $KAFKABROKERS mygroup' \; attach
```

Эта команда использует `tmux`, чтобы разделить терминал на два столбца. Потребитель запускается в каждом столбце с тем же значением идентификатора группы. Когда потребители закончат чтение, обратите внимание, что каждый из них считал только часть записей. Дважды нажмите клавиши CTLR+C, чтобы выйти из `tmux`.

Потребление по клиентам, входящих в одну группу, обрабатывается по секциям раздела. Созданный ранее раздел `test` содержит восемь секций. Если запущено восемь потребителей, каждый из них считывает записи из одной секции раздела.

> [!IMPORTANT]
> Число экземпляров потребителя, входящих в группу потребителей, должно соответствовать числу секций. В этом примере одна группа объектов-получателей может содержать не больше восьми объектов-получателей, так как это число секций в разделе. Но у вас может быть несколько групп объектов-получателей, в каждую из которых входит не более восьми из них.

Записи хранятся в Kafka в том порядке, в котором они поступают в секцию. Чтобы обеспечить упорядоченную доставку записей *в секцию*, нужно создать группу потребителей. При этом число входящих в нее экземпляров потребителя должно соответствовать числу секций. Чтобы обеспечить упорядоченную доставку записей *в раздел*, нужно создать группу потребителей, которая содержит только один экземпляр потребителя.

## <a name="next-steps"></a>Дополнительная информация

Из этого документа вы узнали, как использовать API производителя и потребителя Kafka для Kafka в HDInsight. Дополнительные сведения о работе с Kafka см. в следующих материалах.

* [Анализ журналов Kafka](apache-kafka-log-analytics-operations-management.md)
* [Репликация разделов Apache Kafka с помощью Kafka в HDInsight (предварительная версия) и MirrorMaker](apache-kafka-mirroring.md)
* [API потоков Kafka в HDInsight](apache-kafka-streams-api.md)
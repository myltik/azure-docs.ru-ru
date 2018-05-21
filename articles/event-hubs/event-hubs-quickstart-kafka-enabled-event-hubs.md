---
title: Потоковая передача данных в концентраторы событий Azure для экосистемы Kafka | Документация Майкрософт
description: Потоковая передача данных в концентраторы событий с помощью протокола Kafka и API-интерфейсов.
services: event-hubs
documentationcenter: ''
author: basilhariri
manager: timlt
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: quickstart
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/03/2018
ms.author: bahariri
ms.openlocfilehash: cabbb7ed6157a6c68530ab6b5f405aa67b31a1b2
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/10/2018
---
# <a name="stream-into-event-hubs-for-kafka-ecosystem"></a>Потоковая передача данных в концентраторы событий для экосистемы Kafka

> [!NOTE]
> Этот пример можно найти на сайте [GitHub](https://github.com/Azure/azure-event-hubs).

В этом кратком руководстве показано, как выполнять потоковую передачу данных в концентраторы событий с поддержкой Kafka без необходимости менять клиенты протоколов или запускать собственные кластеры. Вы узнаете, как обеспечить взаимодействие отправителей и объектов-получателей с концентраторами событий с поддержкой Kafka, изменив конфигурацию в приложениях. Концентраторы событий Azure для экосистемы Kafka поддерживают [Apache Kafka версии 1.0.](https://kafka.apache.org/10/documentation.html)

## <a name="prerequisites"></a>предварительным требованиям

В рамках этого краткого руководства вам потребуются:

* Подписка Azure. Если у вас еще нет подписки Azure, создайте [бесплатную учетную запись](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio), прежде чем начать работу.
* [Комплект разработчика Java (JDK) 1.7+](http://www.oracle.com/technetwork/java/javase/downloads/index.html).
* [Скачайте](http://maven.apache.org/download.cgi) и [установите](http://maven.apache.org/install.html) двоичный архив Maven.
* [Git.](https://www.git-scm.com/)
* [Пространство имен концентраторов событий с поддержкой Kafka](event-hubs-create.md).

## <a name="send-and-receive-messages-with-kafka-in-event-hubs"></a>Отправка и получение сообщений с использованием Kafka в концентраторах событий

1. Клонируйте [репозиторий концентраторов событий Azure](https://github.com/Azure/azure-event-hubs).

2. Перейдите на страницу `azure-event-hubs/samples/kafka/quickstart/producer`.

3. Обновите сведения о конфигурации для отправителя в файле src/main/resources/producer.config, как показано ниже.

    ```xml
    bootstrap.servers={YOUR.EVENTHUBS.FQDN}:9093
    security.protocol=SASL_SSL
    sasl.mechanism=PLAIN
    sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
    ```
4. Выполните код отправителя и потоковую передачу данных в концентраторы событий с поддержкой Kafka.
   
    ```java
    mvn clean package
    mvn exec:java -Dexec.mainClass="TestProducer"                                    
    ```
5. Перейдите в папку azure-event-hubs/samples/kafka/quickstart/consumer.

6. Обновите сведения о конфигурации для объекта-получателя в файле src/main/resources/consumer.config, как показано ниже.
   
    ```xml
    bootstrap.servers={YOUR.EVENTHUBS.FQDN}:9093
    security.protocol=SASL_SSL
    sasl.mechanism=PLAIN
    sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
    ```

7. Используя клиенты Kafka, выполните код объекта-получателя и обработку из концентраторов событий с поддержкой Kafka.

    ```java
    mvn clean package
    mvn exec:java -Dexec.mainClass="TestConsumer"                                    
    ```

Если на кластере Kafka концентраторов событий есть события в очереди, полученные от отправителя, начните их принимать на объекте-получателе.

## <a name="next-steps"></a>Дополнительная информация

* [Подробнее о концентраторах событий для экосистемы Kafka](event-hubs-for-kafka-ecosystem-overview.md)
* [Сведения о концентраторах событий](event-hubs-what-is-event-hubs.md)
* Использование [MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330) для потоковой передачи событий из локальных концентраторов событий с поддержкой Kafka в облачные.](event-hubs-kafka-mirror-maker-tutorial.md)

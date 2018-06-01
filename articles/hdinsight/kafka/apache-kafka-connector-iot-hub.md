---
title: Использование Apache Kafka в HDInsight с Центром Интернета вещей | Документация Майкрософт
description: Узнайте, как использовать Apache Kafka в HDInsight с Центром Интернета вещей. Проект Центра Интернета вещей Kafka Connect предоставляет соединитель источника и приемника для Kafka. Соединитель источника считывает данные из Центра Интернета вещей, а соединитель приемника записывает данные в Центр Интернета вещей.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: cgronlun
editor: cgronlun
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 05/15/2018
ms.author: larryfr
ms.openlocfilehash: 33fdb5b099efc40fec94a860b21cda75ced44fe9
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/17/2018
ms.locfileid: "34267305"
---
# <a name="use-apache-kafka-on-hdinsight-with-azure-iot-hub"></a>Использование Apache Kafka в HDInsight с Центром Интернета вещей

Узнайте, как использовать соединитель [Центра Интернета вещей Kafka Connect](https://github.com/Azure/toketi-kafka-connect-iothub) для перемещения данных между Apache Kafka в HDInsight и Центром Интернета вещей. В этом документе показано, как запускать соединитель Центра Интернета вещей из граничного узла кластера.

API Kafka Connect позволяет реализовать соединители, которые будут постоянно извлекать данные в Kafka или отправлять их из Kafka в другую систему. [Центр Интернета вещей Kafka Connect](https://github.com/Azure/toketi-kafka-connect-iothub) является соединителем, извлекающим данные из Центра Интернета вещей в Kafka. Он также может отправлять данные из Kafka в Центр Интернета вещей. 

При извлечении из Центра Интернета вещей используется соединитель __источника__. При отправке в Центр Интернета вещей используется соединитель __приемника__. Соединитель Центра Интернета вещей может выступать как соединителем источника, так и соединителем приемника.

На следующей схеме показан поток данных между Центром Интернета вещей и Kafka в HDInsight при использовании соединителя.

![Изображение, на котором показан поток данных из Центра Интернета вещей в Kafka через соединитель](./media/apache-kafka-connector-iot-hub/iot-hub-kafka-connector-hdinsight.png)

Дополнительные сведения о API подключения см. по адресу: [https://kafka.apache.org/documentation/#connect](https://kafka.apache.org/documentation/#connect).

## <a name="prerequisites"></a>предварительным требованиям

* Подписка Azure. Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

* Kafka в кластере HDInsight. Дополнительные сведения см. в документе [Приступая к работе с Apache Kafka в HDInsight](apache-kafka-get-started.md).

* Граничный узел в кластере Kafka. Дополнительные сведения см. в статье [Использование пустых граничных узлов в кластерах Hadoop в HDInsight](../hdinsight-apps-use-edge-node.md).

* Центр интернета вещей Azure. В этом руководстве рекомендуется использовать документ [Подключение онлайн-симулятора Raspberry Pi к Центру Интернета вещей Azure (Node.js)](https://docs.microsoft.com/azure/iot-hub/iot-hub-raspberry-pi-web-simulator-get-started).

* Клиент SSH. В инструкциях в данном документе для подключения к кластеру используется протокол SSH. Дополнительные сведения см. в статье [Подключение к HDInsight (Hadoop) с помощью SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="install-the-connector"></a>Установка соединителя

1. Скачайте последний выпуск Kafka Connect для Центра Интернета вещей по адресу [https://github.com/Azure/toketi-kafka-connect-iothub/releases/](https://github.com/Azure/toketi-kafka-connect-iothub/releases).

2. Чтобы скачать JAR-файл на граничный узел Kafka в кластере HDInsight, используйте следующую команду:

    > [!NOTE]
    > Замените `sshuser` на имя учетной записи пользователя SSH для кластера HDInsight. Замените `new-edgenode` именем граничного узла. Замените `clustername` именем кластера. Дополнительные сведения о конечной точке SSH для граничного узла см. в разделе [Доступ к граничному узлу](../hdinsight-apps-use-edge-node.md#access-an-edge-node).

    ```bash
    scp kafka-connect-iothub-assembly*.jar sshuser@new-edgenode.clustername-ssh.azurehdinsight.net:
    ```

3. Когда файл будет скопирован, подключитесь к граничному узлу с помощью SSH:

    ```bash
    ssh sshuser@new-edgenode.clustername-ssh.azurehdinsight.net
    ```

    Дополнительные сведения см. в статье [Подключение к HDInsight (Hadoop) с помощью SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

4. Чтобы установить соединитель в каталог `libs` Kafka, используйте следующую команду:

    ```bash
    sudo mv kafka-connect-iothub-assembly*.jar /usr/hdp/current/kafka-broker/libs/
    ```

> [!TIP]
> Если в этом документе возникли проблемы с остальными шагами при использовании предварительно созданного JAR-файла, попробуйте создать пакет из источника.
>
> Чтобы создать соединитель, необходима среда разработки Scala с [инструментом сборки Scala](http://www.scala-sbt.org/).
>
> 1. Скачайте источник для соединителя из [https://github.com/Azure/toketi-kafka-connect-iothub/](https://github.com/Azure/toketi-kafka-connect-iothub/) в свою среду разработки.
>
> 2. Из командной строки в каталоге проекта используйте следующую команду для выполнения сборки и упаковки проекта:
>
>    ```bash
>    sbt assembly
>    ```
>
>    Эта команда создает файл с именем `kafka-connect-iothub-assembly_2.11-0.6.jar` в каталоге `target/scala-2.11` проекта.

## <a name="configure-kafka"></a>Настройка Kafka

В SSH-подключении к граничному узлу используйте следующие шаги, чтобы настроить в Kafka выполнение соединителя в автономном режиме:

1. Сохраните имя кластера в переменной. Использование переменной упрощает копирование и вставку других команд в этот раздел:

    ```bash
    read -p "Enter the Kafka on HDInsight cluster name: " CLUSTERNAME
    ```

2. Установите служебную программу `jq`. Эта программа упрощает обработку документов JSON, возвращаемых из запросов Ambari:

    ```bash
    sudo apt -y install jq
    ```

3. Получите адреса брокеров Kafka. В вашем кластере может быть много брокеров, но вам нужно всего лишь ссылаться на один или два. Чтобы получить адрес двух узлов брокера, используйте следующую команду:

    ```bash
    export KAFKABROKERS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`
    echo $KAFKABROKERS
    ```

    При появлении запроса введите пароль для учетной записи администратора, чтобы войти на кластер. Возвращаемое значение аналогично приведенному ниже тексту.

    `wn0-kafka.w5ijyohcxt5uvdhhuaz5ra4u5f.ex.internal.cloudapp.net:9092,wn1-kafka.w5ijyohcxt5uvdhhuaz5ra4u5f.ex.internal.cloudapp.net:9092`

4. Получите адреса узлов Zookeeper. В кластере имеется несколько узлов Zookeeper, но необходимо ссылаться только на один или два. Чтобы получить адреса двух узлов Zookeeper, используйте следующую команду:

    ```bash
    export KAFKAZKHOSTS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2`
    ```

5. При запуске соединителя в автономном режиме файл `/usr/hdp/current/kafka-broker/config/connect-standalone.properties` используется для обмена данными с брокерами Kafka. Чтобы изменить файл `connect-standalone.properties`, используйте следующую команду:

    ```bash
    sudo nano /usr/hdp/current/kafka-broker/config/connect-standalone.properties
    ```

    * Чтобы настроить изолированную конфигурацию на граничном узле для поиска брокеров Kafka, найдите строку, которая начинается с `bootstrap.servers=`. Замените `localhost:9092` значением с узлами брокеров из предыдущего шага.

    * Замените строки `key.converter=` и `value.converter=` следующими значениями:

        ```text
        key.converter=org.apache.kafka.connect.storage.StringConverter
        value.converter=org.apache.kafka.connect.storage.StringConverter
        ```

        > [!IMPORTANT]
        > Это изменение можно проверить с помощью отправителя консоли, входящего в состав Kafka. Для различных отправителей и потребителей вам могут понадобиться различные преобразователи. Дополнительные сведения об использовании других значений преобразования см. по адресу [https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md).

    * Добавьте строку в конце файла, которая содержит следующий текст:

        ```text
        consumer.max.poll.records=10
        ```

        > [!TIP]
        > Это изменение предназначено для предотвращения времени ожидания в соединителе приемника, ограничивая его 10 записями за раз. Дополнительные сведения можно найти по адресу [https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md).

6. Чтобы сохранить файл, нажмите клавиши __CTRL+X__, затем — __Y__ и __ВВОД__.

7. Чтобы создать разделы для соединителя, используйте следующие команды:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic iotin --zookeeper $KAFKAZKHOSTS

    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic iotout --zookeeper $KAFKAZKHOSTS
    ```

    Чтобы проверить, что разделы `iotin` и `iotout` существуют, используйте следующую команду:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --list --zookeeper $KAFKAZKHOSTS
    ```

    Раздел `iotin` используется для получения сообщений из Центра Интернета вещей. Раздел `iotout` используется для отправки сообщений в Центр Интернета вещей.

## <a name="get-iot-hub-connection-information"></a>Получение сведений о подключении Центра Интернета вещей

Чтобы извлечь информацию о Центре Интернета вещей, используемую соединителем, выполните следующие действия:

1. Получите конечную точку, совместимую с центрами событий, и ее имя для Центра Интернета вещей. Чтобы получить эту информацию, используйте один из указанных ниже способов:

    * __На [портале Azure](https://portal.azure.com/)__ выполните указанные ниже действия.

        1. Перейдите к Центру Интернета вещей и выберите __Конечные точки__.
        2. В разделе __Встроенные конечные точки__ выберите __События__.
        3. В разделе __Свойства__ скопируйте значения следующих полей:

            * __Имя, совместимое с центрами событий__
            * __Конечная точка, совместимая с центрами событий__
            * __Секции__

        > [!IMPORTANT]
        > Значение конечной точки на портале может содержать лишний текст, который не требуется в этом примере. Извлеките текст, который соответствует этому шаблону: `sb://<randomnamespace>.servicebus.windows.net/`.

    * __В [интерфейсе командной строки Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli)__ введите следующую команду:

        ```azure-cli
        az iot hub show --name myhubname --query "{EventHubCompatibleName:properties.eventHubEndpoints.events.path,EventHubCompatibleEndpoint:properties.eventHubEndpoints.events.endpoint,Partitions:properties.eventHubEndpoints.events.partitionCount}"
        ```

        Замените переменную `myhubname` именем Центра Интернета вещей. В ответ вы получите примерно такой текст:

        ```text
        "EventHubCompatibleEndpoint": "sb://ihsuprodbnres006dednamespace.servicebus.windows.net/",
        "EventHubCompatibleName": "iothub-ehub-myhub08-207673-d44b2a856e",
        "Partitions": 2
        ```

2. Получите __политику общего доступа__ и __ключ__. Для этого примера используйте __служебный__ ключ. Чтобы получить эту информацию, используйте один из указанных ниже способов:

    * __На [портале Azure](https://portal.azure.com/)__ выполните указанные ниже действия.

        1. Выберите __Политика общего доступа__, а затем выберите __службу__.
        2. Скопируйте значение __первичного ключа__.
        3. Скопируйте значение поля __Строка подключения — первичный ключ__.

    * __В [интерфейсе командной строки Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli)__ введите следующую команду:

        1. Чтобы получить значение первичного ключа, используйте следующую команду:

            ```azure-cli
            az iot hub policy show --hub-name myhubname --name service --query "primaryKey"
            ```

            Замените переменную `myhubname` именем Центра Интернета вещей. Ответ — это первичный ключ для политики `service` этого центра.

        2. Чтобы получить строку подключения для политики `service`, используйте следующую команду:

            ```azure-cli
            az iot hub show-connection-string --name myhubname --policy-name service --query "connectionString"
            ```

            Замените переменную `myhubname` именем Центра Интернета вещей. Ответом является строка подключения для политики `service`.

## <a name="configure-the-source-connection"></a>Настройка подключения к источнику

Чтобы настроить источник для работы с Центром Интернета вещей, выполните следующие действия из SSH-подключения к граничному узлу:

1. Создайте копию файла `connect-iot-source.properties` в каталоге `/usr/hdp/current/kafka-broker/config/`. Скачайте файл из проекта toketi-kafka-connect-iothub, используя следующую команду:

    ```bash
    sudo wget -P /usr/hdp/current/kafka-broker/config/ https://raw.githubusercontent.com/Azure/toketi-kafka-connect-iothub/master/connect-iothub-source.properties
    ```

2. Чтобы изменить файл `connect-iot-source.properties` и добавить информацию о Центре Интернета вещей, используйте следующую команду:

    ```bash
    sudo nano /usr/hdp/current/kafka-broker/config/connect-iothub-source.properties
    ```

    В редакторе найдите и измените следующие записи:

    * `Kafka.Topic=PLACEHOLDER`. Замените `PLACEHOLDER` на `iotin`. Сообщения, полученные из Центра Интернета вещей, размещаются в разделе `iotin`.
    * `IotHub.EventHubCompatibleName=PLACEHOLDER`. Замените `PLACEHOLDER` именем, совместимым с центрами событий.
    * `IotHub.EventHubCompatibleEndpoint=PLACEHOLDER`. Замените `PLACEHOLDER` конечной точкой, совместимой с центрами событий.
    * `IotHub.Partitions=PLACEHOLDER`. Замените `PLACEHOLDER` на количество секций из предыдущего шага.
    * `IotHub.AccessKeyName=PLACEHOLDER`. Замените `PLACEHOLDER` на `service`.
    * `IotHub.AccessKeyValue=PLACEHOLDER`. Замените `PLACEHOLDER` первичным ключом политики `service`.
    * `IotHub.StartType=PLACEHOLDER`. Замените `PLACEHOLDER` датой в формате UTC. Это время и дата, когда соединитель начинает проверку на наличие сообщений. Формат даты — `yyyy-mm-ddThh:mm:ssZ`.
    * `BatchSize=100`. Замените `100` на `5`. В таком случае соединитель считывает сообщения в Kafka, когда в Центре Интернета вещей появилось пять новых сообщений.

    Пример конфигурации см. по адресу [https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Source.md](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Source.md).

3. Чтобы сохранить изменения, нажмите клавиши __CTRL+X__, затем — __Y__ и __ВВОД__.

Дополнительные сведения о настройке источника соединителя см. по адресу [https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Source.md](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Source.md).

## <a name="configure-the-sink-connection"></a>Настройка подключения приемника

Чтобы настроить подключение приемника для работы с Центром Интернета вещей, выполните следующие действия из SSH-подключения к граничному узлу:

1. Создайте копию файла `connect-iothub-sink.properties` в каталоге `/usr/hdp/current/kafka-broker/config/`. Скачайте файл из проекта toketi-kafka-connect-iothub, используя следующую команду:

    ```bash
    sudo wget -P /usr/hdp/current/kafka-broker/config/ https://raw.githubusercontent.com/Azure/toketi-kafka-connect-iothub/master/connect-iothub-sink.properties
    ```

2. Чтобы изменить файл `connect-iothub-sink.properties` и добавить информацию о Центре Интернета вещей, используйте следующую команду:

    ```bash
    sudo nano /usr/hdp/current/kafka-broker/config/connect-iothub-sink.properties
    ```

    В редакторе найдите и измените следующие записи:

    * `topics=PLACEHOLDER`. Замените `PLACEHOLDER` на `iotout`. Сообщение, записанные в раздел `iotout`, переадресовываются в Центр Интернета вещей.
    * `IotHub.ConnectionString=PLACEHOLDER`. Замените `PLACEHOLDER` строкой подключения политики `service`.

    Пример конфигурации см. по адресу [https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md).

3. Чтобы сохранить изменения, нажмите клавиши __CTRL+X__, затем — __Y__ и __ВВОД__.

Дополнительные сведения о настройке приемника соединителя см. по адресу [https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md).

## <a name="start-the-source-connector"></a>Запуск соединителя источника

Чтобы запустить соединитель источника, используйте следующую команду из SSH-подключения к пограничному узлу:

```bash
/usr/hdp/current/kafka-broker/bin/connect-standalone.sh /usr/hdp/current/kafka-broker/config/connect-standalone.properties /usr/hdp/current/kafka-broker/config/connect-iothub-source.properties
```

После запуска соединителя отправьте сообщения в Центр Интернета вещей со своих устройств. Когда соединитель считывает сообщения из Центра Интернета вещей и сохраняет их в разделе Kafka, он регистрирует информацию на консоли:

```text
[2017-08-29 20:15:46,112] INFO Polling for data - Obtained 5 SourceRecords from IotHub (com.microsoft.azure.iot.kafka.co
nnect.IotHubSourceTask:39)
[2017-08-29 20:15:54,106] INFO Finished WorkerSourceTask{id=AzureIotHubConnector-0} commitOffsets successfully in 4 ms (
org.apache.kafka.connect.runtime.WorkerSourceTask:356)
```

> [!NOTE]
> При запуске соединителя может появиться несколько предупреждений. Эти предупреждения не вызывают проблем с получением сообщений из Центра Интернета вещей.

Нажмите клавиши __CTRL+C__, чтобы остановить соединитель.

## <a name="start-the-sink-connector"></a>Запуск соединителя приемника

Чтобы запустить соединитель приемника в автономном режиме, используйте следующую команду из SSH-подключения к граничному узлу:

```bash
/usr/hdp/current/kafka-broker/bin/connect-standalone.sh /usr/hdp/current/kafka-broker/config/connect-standalone.properties /usr/hdp/current/kafka-broker/config/connect-iothub-sink.properties
```

После запуска соединителя отобразится похожая информация:

```text
[2017-08-30 17:49:16,150] INFO Started tasks to send 1 messages to devices. (com.microsoft.azure.iot.kafka.connect.sink.
IotHubSinkTask:47)
[2017-08-30 17:49:16,150] INFO WorkerSinkTask{id=AzureIotHubSinkConnector-0} Committing offsets (org.apache.kafka.connec
t.runtime.WorkerSinkTask:262)
```

> [!NOTE]
> При запуске соединителя может появиться несколько предупреждений. Эти предупреждения можно игнорировать.

Для отправки сообщений через соединитель, выполните следующие действия:

1. Запустите другой сеанс SSH к кластеру Kafka:

    ```bash
    ssh sshuser@new-edgenode.clustername-ssh.azurehdinsight.net
    ```
2. Для отправки сообщений в раздел `iotout` используйте следующую команду:

    > [!WARNING]
    > Так как это новое подключение SSH, переменная `$KAFKABROKERS` не содержит никакой информации. Чтобы задать его, используйте один из следующих методов:
    >
    > * Используйте первые три шага в разделе [Настройка Kafka](#configure-kafka).
    > * Используйте `echo $KAFKABROKERS` из предыдущего SSH-подключения, чтобы получить значения, а затем замените `$KAFKABROKERS` в следующей команде фактическими значениями.

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list $KAFKABROKERS --topic iotout
    ```

    Эта команда не возвращает нормальные строки Bash. Вместо этого он отправляет ввод с клавиатуры в раздел `iotout`.

3. Чтобы отправить сообщение на устройство, вставьте документ JSON в сеанс SSH для `kafka-console-producer`.

    > [!IMPORTANT]
    > Для записи `"deviceId"` необходимо задать идентификатор устройства. В следующем примере устройство называется `fakepi`:

    ```text
    {"messageId":"msg1","message":"Turn On","deviceId":"fakepi"}
    ```

    Схема для этого документа JSON более подробно описана в репозитории [https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md).

    Если вы используете имитированное устройство Raspberry Pi и оно работает, на устройстве регистрируется следующее сообщение:

    ```text
    Receive message: Turn On
    ```

    Повторно отправьте документ JSON, но измените значение записи `"message"`. Новое значение записывается на устройстве.

Дополнительные сведения об использовании соединителя приемника см. по адресу [https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md).

## <a name="next-steps"></a>Дополнительная информация

Из этого документа вы узнали, как использовать API Kafka Connect для запуска соединителя Kafka Центра Интернета вещей в HDInsight. Другие материалы, посвященные работе с Kafka, доступны по следующим ссылкам:

* [Совместное использование Apache Spark и Kafka (предварительная версия) в HDInsight](../hdinsight-apache-spark-with-kafka.md)
* [Совместное использование Apache Kafka (предварительная версия) и Storm в HDInsight](../hdinsight-apache-storm-with-kafka.md)

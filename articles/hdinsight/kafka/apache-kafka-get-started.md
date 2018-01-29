---
title: "Приступая к работе с Apache Kafka в Azure HDInsight | Документация Майкрософт"
description: "Узнайте, как создать кластер Apache Kafka в Azure HDInsight. Узнайте, как создать разделы, подписки и потребителей."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 43585abf-bec1-4322-adde-6db21de98d7f
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: 
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/18/2018
ms.author: larryfr
ms.openlocfilehash: 6191d81d6b55f5ffe943f800be542d7ea4614eaf
ms.sourcegitcommit: 817c3db817348ad088711494e97fc84c9b32f19d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/20/2018
---
# <a name="start-with-apache-kafka-on-hdinsight"></a>Приступая к работе с Apache Kafka в HDInsight

Узнайте, как создать кластер [Apache Kafka](https://kafka.apache.org) и использовать его в Azure HDInsight. Kafka — это распределенная платформа потоковой передачи с открытым кодом, которая доступна в HDInsight. Она часто используется как брокер сообщений, предоставляя такие же функциональные возможности, как и очередь сообщений типа "публикация-подписка". Kafka часто используется с Apache Spark и Apache Storm.

> [!NOTE]
> Сейчас в HDInsight доступны две версии Kafka: 0.9.0 (HDInsight 3.4) и 0.10.0 (HDInsight 3.5 и 3.6). В этой статье предполагается, что вы используете Kafka с версией HDInsight 3.6.

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="create-a-kafka-cluster"></a>Создание кластера Kafka

Чтобы создать Kafka в кластере HDInsight, сделайте следующее.

1. На [портале Azure](https://portal.azure.com) последовательно выберите **+Создать ресурс**, **Данные и аналитика** и **HDInsight**.
   
    ![Создание кластера HDInsight](./media/apache-kafka-get-started/create-hdinsight.png)

2. В колонке **Основные сведения** задайте следующие параметры:

    * **Имя кластера.** Имя кластера HDInsight.
    * **Подписка.** Выберите нужную подписку.
    * **Имя пользователя для входа в кластер** и **Пароль для входа в кластер**. Имя для входа в кластер по протоколу HTTPS. Эти учетные данные используются для доступа к таким службам, как веб-интерфейс Ambari или REST API.
    * **Secure Shell (SSH) username** (Имя пользователя Secure Shell (SSH)). Имя для доступа к кластеру по протоколу SSH. По умолчанию пароль совпадает с паролем для входа в кластер.
    * **Группа ресурсов.** Группа ресурсов, в которой будет создан кластер.
    * **Расположение.** Регион Azure, в котором будет создан кластер.

        > [!IMPORTANT]
        > Для обеспечения высокой доступности данных рекомендуется выбрать расположение (регион), содержащий __три домена сбоя__. Дополнительные сведения см. в разделе [Высокая доступность данных](#data-high-availability).
   
 ![Выберите подписку.](./media/apache-kafka-get-started/hdinsight-basic-configuration.png)

3. Выберите **Тип кластера** и в колонке **Конфигурация кластера** задайте следующие значения:
   
    * **Тип кластера**: Kafka.
    * **Версия**: Kafka 0.10.0 (HDI 3.6).

    Затем нажмите кнопку **Выбрать**, чтобы сохранить эти параметры.
     
 ![Выбор типа кластера](./media/apache-kafka-get-started/set-hdinsight-cluster-type.png)

4. Выбрав тип кластера, нажмите кнопку __Выбрать__, чтобы установить выбранный тип. Затем нажмите кнопку __Далее__, чтобы завершить настройку основных параметров.

5. В колонке **Хранилище** выберите или создайте учетную запись хранения. Для действий, описанных в этом документе, в других полях этой колонки оставьте значения по умолчанию. Нажмите кнопку __Далее__, чтобы сохранить конфигурацию хранилища.

    ![Настройка параметров учетной записи хранения для HDInsight](./media/apache-kafka-get-started/set-hdinsight-storage-account.png)

6. Чтобы продолжить, в колонке __Приложения (необязательно)__ щелкните __Далее__. Для этого примера приложения не требуются.

7. Чтобы продолжить, в колонке __Размер кластера__ щелкните __Далее__.

    > [!WARNING]
    > Чтобы обеспечить доступность Kafka в HDInsight, кластер должен содержать не менее трех рабочих узлов. Дополнительные сведения см. в разделе [Высокая доступность данных](#data-high-availability).

    ![Определение размер кластера Kafka](./media/apache-kafka-get-started/kafka-cluster-size.png)

    > [!IMPORTANT]
    > **Число дисков для каждой записи рабочего узла** определяет степень масштабируемости Kafka в HDInsight. Kafka в HDInsight использует локальный диск для виртуальных машин в кластере. Kafka обрабатывает большое количество операций ввода-вывода, поэтому используются [управляемые диски Azure](../../virtual-machines/windows/managed-disks-overview.md), чтобы обеспечить высокую пропускную способность и предоставить дополнительное хранилище для каждого узла. Управляемый диск может быть двух типов: __Стандартный__ (HDD) или __Премиум__ (SSD). Диски категории "Премиум" используются с виртуальными машинами серий DS и GS. Для всех остальных виртуальных машин используются стандартные управляемые диски.

8. Чтобы продолжить, в колонке __Дополнительные параметры__ щелкните __Далее__.

9. В колонке **Сводка** проверьте конфигурацию для кластера. Используйте ссылки __Изменить__, чтобы изменить неправильные параметры. Наконец, нажмите кнопку "Создать" для создания кластера.
   
    ![Сводка по конфигурации кластера](./media/apache-kafka-get-started/hdinsight-configuration-summary.png)
   
    > [!NOTE]
    > Операция создания кластера может занять до 20 минут.

## <a name="connect-to-the-cluster"></a>Подключение к кластеру

> [!IMPORTANT]
> При выполнении следующих шагов необходимо использовать клиент SSH. Дополнительные сведения см. в статье [Подключение к HDInsight (Hadoop) с помощью SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

На своем клиенте используйте SSH, чтобы подключиться к кластеру:

```ssh SSHUSER@CLUSTERNAME-ssh.azurehdinsight.net```

Замените **SSHUSER** именем пользователя SSH, указанным при создании кластера. Замените **CLUSTERNAME** именем кластера.

При появлении запроса введите пароль, который используется для учетной записи SSH.

См. дополнительные сведения об [использовании SSH в HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a id="getkafkainfo"></a>Получение сведений об узлах Zookeeper и брокера

Для работы с Kafka вам нужно предоставить данные о двух узлах: *Zookeeper* и *брокера*. Эти узлы используются Kafka API и многими другими служебными программами, поставляемыми с платформой Kafka.

Ниже описано, как создать переменные среды, которые содержат сведения об узле. Эти переменные используются в нашем примере далее.

1. Используя SSH-подключение к кластеру, выполните следующую команду, чтобы установить служебную программу `jq`. Эта служебная программа используется для синтаксического анализа документов JSON. С ее помощью также удобно получать сведения об узле брокера.
   
    ```bash
    sudo apt -y install jq
    ```

2. Используйте следующие команды, чтобы набрать переменные среды на основе информации, полученной из Ambari.

    ```bash
    CLUSTERNAME='your cluster name'
    export KAFKAZKHOSTS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2`

    export KAFKABROKERS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`

    echo '$KAFKAZKHOSTS='$KAFKAZKHOSTS
    echo '$KAFKABROKERS='$KAFKABROKERS
    ```

    > [!IMPORTANT]
    > В качестве значения параметра `CLUSTERNAME=` задайте имя кластера Kafka. При появлении запроса введите пароль для учетной записи администратора, чтобы войти на кластер.

    Ниже приведен пример содержимого `$KAFKAZKHOSTS`:
   
    `zk0-kafka.eahjefxxp1netdbyklgqj5y1ud.ex.internal.cloudapp.net:2181,zk2-kafka.eahjefxxp1netdbyklgqj5y1ud.ex.internal.cloudapp.net:2181`
   
    Ниже приведен пример содержимого `$KAFKABROKERS`:
   
    `wn1-kafka.eahjefxxp1netdbyklgqj5y1ud.cx.internal.cloudapp.net:9092,wn0-kafka.eahjefxxp1netdbyklgqj5y1ud.cx.internal.cloudapp.net:9092`

    > [!NOTE]
    > Команда `cut` используется для сокращения списка узлов для двух записей узлов. При создании объект-получателя или производителя Kafka не нужно предоставлять полный список узлов.
   
    > [!WARNING]
    > Учтите, что данные, возвращаемые этим сеансом, могут быть неточными. Масштабирование кластера обычно сопровождается добавлением или удалением брокеров. Если происходит сбой, и узел заменяется, имя узла также может измениться.
    >
    > Чтобы получить допустимые значения, всегда следует извлекать сведения об узлах Zookeeper и брокера непосредственно перед их использованием.

## <a name="create-a-topic"></a>Создание раздела

Kafka хранит потоки данных в категориях, называемых *разделами*. Чтобы создать такой раздел, запустите скрипт, который поставляется с Kafka, используя SSH-подключение к головному узлу кластера:

```bash
/usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic test --zookeeper $KAFKAZKHOSTS
```

Эта команда создает подключение к Zookeeper, используя хранящиеся в `$KAFKAZKHOSTS` сведения об узле, а затем создает раздел Kafka с именем **test**. Чтобы убедиться, что раздел создан, используйте следующий скрипт, который отображает список разделов.

```bash
/usr/hdp/current/kafka-broker/bin/kafka-topics.sh --list --zookeeper $KAFKAZKHOSTS
```

В выходных данных этой команды перечислены разделы Kafka, среди которых должен быть и новый раздел **test**.

## <a name="produce-and-consume-records"></a>Создание и использование записей

Kafka хранит *записи* в разделах. Записи создаются *производителями*, а используются *потребителями*. Производители создают записи для *брокеров* Kafka. Каждый рабочий узел в кластере HDInsight — это брокер Kafka.

Ниже описано, как сначала сохранить записи в созданный ранее тестовый раздел, а затем считать их с помощью потребителя.

1. Запустите сеанс SSH и выполните скрипт, предоставленный службой Kafka для сохранения записей в раздел:
   
    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list $KAFKABROKERS --topic test
    ```
   
    Эта команда не возвращает запрос после запуска. Вместо этого введите несколько текстовых сообщений, а затем нажмите клавиши **Ctrl+C**, чтобы остановить отправку данных в раздел. Каждая строка отправляется как отдельная запись.

2. Используйте скрипт, поставляемый с платформой Kafka, чтобы считать записи из раздела:
   
    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --bootstrap-server $KAFKABROKERS --topic test --from-beginning
    ```
   
    Эта команда извлекает записи из раздела, а затем отображает их. Параметр `--from-beginning` указывает потребителю считывать данные с самого начала потока, поэтому будут извлечены все записи.

    > [!NOTE]
    > Если вы используете более раннюю версию Kafka, возможно, понадобится изменить `--bootstrap-server $KAFKABROKERS` на `--zookeeper $KAFKAZKHOSTS`.

3. Нажмите клавиши __Ctrl+C__, чтобы остановить потребитель.

Вы также можете создать отправители и получатели программными средствами. Пример использования этого API см. в документе [Apache Kafka Producer and Consumer APIs](apache-kafka-producer-consumer-api.md) (API-интерфейсы отправителя и получателя Apache Kafka).

## <a name="data-high-availability"></a>Высокая доступность данных

В каждом регионе Azure (расположении) предоставляются _домены сбоя_. Домен сбоя — это логическое объединение базового оборудования в центре обработки данных Azure. Все домены сбоя используют общий источник питания и сетевой коммутатор. Виртуальные машины и управляемые диски, на которых реализуются узлы в кластере HDInsight, распределяются по этим доменам сбоя. Такая архитектура ограничивает потенциальное влияние сбоев физического оборудования.

Дополнительные сведения о количестве доменов сбоя в регионе см. в разделе [Использование управляемых дисков для виртуальных машин в группе доступности](../../virtual-machines/windows/manage-availability.md#use-managed-disks-for-vms-in-an-availability-set).

> [!IMPORTANT]
> Мы рекомендуем использовать регион Azure с тремя доменами сбоя и коэффициент репликации 3.

Если необходимо указать регион с двумя доменами сбоя, используйте коэффициент репликации 4, чтобы равномерно распределить реплики на этих доменах.

### <a name="kafka-and-fault-domains"></a>Kafka и домены сбоя

В Kafka нет сведений о доменах сбоя. При создании реплик секций для разделов реплики могут быть неправильно распределены с точки зрения высокой доступности. Для обеспечения высокой доступности используйте [средство перераспределения секций Kafka](https://github.com/hdinsight/hdinsight-kafka-tools). Это средство следует запускать из сеанса SSH на главном узле кластера Kafka.

Чтобы обеспечить максимально высокий уровень доступности данных Kafka, следует перераспределять реплики секций для раздела в следующих случаях:

* при создании раздела или секции;

* при масштабировании кластера.

## <a name="delete-the-cluster"></a>Удаление кластера

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="troubleshoot"></a>Устранение неполадок

Если при создании кластеров HDInsight возникли проблемы, см. раздел [Создание кластеров](../hdinsight-administer-use-portal-linux.md#create-clusters).

## <a name="next-steps"></a>Дополнительная информация

В этой статье описаны основные принципы работы с Apache Kafka в HDInsight. Дополнительные сведения о работе с Kafka см. в следующих материалах.

* [Анализ журналов Kafka](apache-kafka-log-analytics-operations-management.md)
* [Репликация разделов Apache Kafka с помощью Kafka в HDInsight (предварительная версия) и MirrorMaker](apache-kafka-mirroring.md)
* [Apache Kafka Producer and Consumer APIs](apache-kafka-producer-consumer-api.md) (API-интерфейсы отправителя и получателя Apache Kafka)
* [Apache Kafka streams API](apache-kafka-streams-api.md) (API потоков Apache Kafka)
* [Пример потоковой передачи Apache Spark (DStream) с использованием Kafka (предварительная версия) в HDInsight](../hdinsight-apache-spark-with-kafka.md)
* [Использование структурированной потоковой передачи Spark с Kafka (предварительная версия) в HDInsight](../hdinsight-apache-kafka-spark-structured-streaming.md)
* [Use Spark Structured Streaming with Kafka and Azure Cosmos DB](../apache-kafka-spark-structured-streaming-cosmosdb.md) (Использование структурированной потоковой передачи Spark с Kafka и Azure Cosmos DB)
* [Совместное использование Apache Kafka (предварительная версия) и Storm в HDInsight](../hdinsight-apache-storm-with-kafka.md)
* [Подключение к Kafka через виртуальную сеть Azure](apache-kafka-connect-vpn-gateway.md)

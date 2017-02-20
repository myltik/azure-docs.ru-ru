---
title: "Приступая к работе с Apache Kafka в HDInsight | Документация Майкрософт"
description: "Базовые сведения о создании и работе с Kafka в HDInsight."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 43585abf-bec1-4322-adde-6db21de98d7f
ms.service: hdinsight
ms.devlang: 
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/14/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: f592dc23938c436e803c7a0d8f7fd2dd5b4185c8
ms.openlocfilehash: 3b645725b88b33e7283ce2bf89383b285d75cddc

---
# <a name="get-started-with-apache-kafka-preview-on-hdinsight"></a>Приступая к работе с Apache Kafka (предварительная версия) в HDInsight

[Apache Kafka](https://kafka.apache.org) — это распределенная платформа потоковой передачи с открытым кодом, которая доступна в HDInsight. Она часто используется как брокер сообщений, предоставляя такие же функциональные возможности, как и очередь сообщений типа "публикация-подписка". Из этой статьи вы узнаете, как создать Kafka в кластере HDInsight, а затем отправить и получить данные из приложения Java.

> [!NOTE]
> Сейчас в HDInsight доступны две версии Kafka: 0.9.0 (HDInsight 3.4) и 0.10.0 (HDInsight 3.5). В этой статье предполагается, что вы используете Kafka с версией HDInsight 3.5.

## <a name="prerequisite"></a>Предварительные требования

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Необходимые условия для успешного выполнения инструкций этого руководства для Apache Kafka:

* **Подписка Azure**. Ознакомьтесь с [бесплатной пробной версией Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

* **Знакомство с SSH и SCP**. Дополнительные сведения об использовании SSH и SCP с HDInsight см. в следующих ресурсах:
  
   * **Для клиентов Linux, Unix, OS X и Windows 10**: см. сведения в статье [Использование SSH с HDInsight (Hadoop) на платформе Windows, Linux, Unix или OS X](hdinsight-hadoop-linux-use-ssh-unix.md).
   
   * **Для клиентов Windows**: см. статью [Использование SSH с Hadoop на основе Linux в HDInsight из Windows](hdinsight-hadoop-linux-use-ssh-windows.md).

* [Java JDK 8](http://www.oracle.com/technetwork/java/javase/downloads/index.html) или эквивалент, например OpenJDK.

* [Apache Maven](http://maven.apache.org/) 

### <a name="access-control-requirements"></a>Требования к контролю доступа

[!INCLUDE [access-control](../../includes/hdinsight-access-control-requirements.md)]

## <a name="create-a-kafka-cluster"></a>Создание кластера Kafka

Чтобы создать Kafka в кластере HDInsight, сделайте следующее.

1. На [портале Azure](https://portal.azure.com) последовательно выберите элементы **+Создать**, **Аналитика** и **HDInsight**.
   
    ![Создание кластера HDInsight](./media/hdinsight-apache-kafka-get-started/create-hdinsight.png)

2. В колонке **Основные сведения** задайте следующие параметры.

    * **Имя кластера.** Имя кластера HDInsight.
    * **Подписка.** Выберите нужную подписку.
    * **Имя пользователя для входа в кластер** и **Пароль для входа в кластер**. Имя для входа в кластер по протоколу HTTPS. Эти учетные данные используются для доступа к таким службам, как веб-интерфейс Ambari или REST API.
    * **Secure Shell (SSH) username** (Имя пользователя Secure Shell (SSH)). Имя для доступа к кластеру по протоколу SSH. По умолчанию пароль совпадает с паролем для входа в кластер.
    * **Группа ресурсов.** Группа ресурсов, в которой будет создан кластер.
    * **Расположение.** Регион Azure, в котором будет создан кластер.
   
    ![Выберите подписку.](./media/hdinsight-apache-kafka-get-started/hdinsight-basic-configuration.png)

3. Выберите **тип кластера** и в колонке **Конфигурация кластера** задайте следующие значения:
   
    * **Тип кластера**: Kafka.

    * **Версия**: Kafka 0.10.0 (HDI 3.5).

    * **Ценовая категория кластера**: "Стандартный".
     
    Затем нажмите кнопку **Выбрать**, чтобы сохранить эти параметры.
     
    ![Выбор типа кластера](./media/hdinsight-apache-kafka-get-started/set-hdinsight-cluster-type.png)

    > [!NOTE]
    > Если подписка Azure не имеет доступа к предварительной версии Kafka, отображаются инструкции о том, как получить доступ к этой версии. Они аналогичны приведенным на рисунке ниже.
    >
    > ![Сообщение о предварительной версии: If you would like to deploy a managed Apache Kafka cluster on HDInsight, email us to request preview access (Если вы хотите развернуть управляемый кластер Apache Kafka в HDInsight, отправьте нам запрос на доступ к предварительной версии по электронной почте)](./media/hdinsight-apache-kafka-get-started/no-kafka-preview.png)

4. Выбрав тип кластера, нажмите кнопку __Выбрать__, чтобы установить выбранный тип. Затем нажмите кнопку __Далее__, чтобы завершить настройку основных параметров.

5. В колонке **Хранилище** выберите или создайте учетную запись хранения. Для действий, описанных в этом документе, в других полях этой колонки оставьте значения по умолчанию. Нажмите кнопку __Далее__, чтобы сохранить конфигурацию хранилища.

    ![Настройка параметров учетной записи хранения для HDInsight](./media/hdinsight-apache-kafka-get-started/set-hdinsight-storage-account.png)

6. В колонке **Сводка** просмотрите конфигурацию для кластера. Используйте ссылки __Изменить__, чтобы изменить неправильные параметры. Наконец, нажмите кнопку "Создать" для создания кластера.
   
    ![Сводка по конфигурации кластера](./media/hdinsight-apache-kafka-get-started/hdinsight-configuration-summary.png)
   
    > [!NOTE]
    > Операция создания кластера может занять до 20 минут.

## <a name="connect-to-the-cluster"></a>Подключение к кластеру

На своем клиенте используйте SSH, чтобы подключиться к кластеру. Если вы используете Linux, Unix, MacOS или Bash в Windows 10, используйте следующую команду:

```ssh SSHUSER@CLUSTERNAME-ssh.azurehdinsight.net```

Замените **SSHUSER** именем пользователя SSH, указанным при создании кластера. Замените **CLUSTERNAME** именем кластера.

При появлении запроса введите пароль, который используется для учетной записи SSH.

> [!NOTE]
> Если установлена версия Windows, которая не включает команды SSH, см. сведения в статье [Использование SSH с HDInsight (Hadoop) в PuTTY на базе Windows](hdinsight-hadoop-linux-use-ssh-windows.md). В этой статье описывается использование SSH-клиента PuTTY для Windows.

Дополнительные сведения об использовании SSH с HDInsight см. в следующих статьях.

* [Использование SSH с HDInsight (Hadoop) на платформе Windows, Linux, Unix или OS X](hdinsight-hadoop-linux-use-ssh-unix.md)

* [Использование SSH с Hadoop на основе Linux в HDInsight из Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

##<a name="a-idgetkafkainfoaget-the-zookeeper-and-broker-host-information"></a><a id="getkafkainfo"></a>Получение сведений об узлах Zookeeper и брокера

Для работы с Kafka вам нужно предоставить данные о двух узлах: *Zookeeper* и *брокера*. Эти узлы используются Kafka API и многими другими служебными программами, поставляемыми с платформой Kafka.

Ниже описано, как создать переменные среды, которые содержат сведения об узле. Эти переменные используются в нашем примере далее.

1. Используя SSH-подключение к кластеру, выполните следующую команду, чтобы установить служебную программу `jq`. Эта служебная программа используется для синтаксического анализа документов JSON. С ее помощью также удобно получать сведения об узле брокера.
   
    ```bash
    sudo apt -y install jq
    ```

2. Используйте следующие команды, чтобы определить переменные среды на основе информации, полученной из Ambari. Замените __KAFKANAME__ именем кластера Kafka. Замените __PASSWORD__ паролем администратора, который использовался при создании кластера.

    ```bash
    export KAFKAZKHOSTS=`curl --silent -u admin:'PASSWORD' -G http://headnodehost:8080/api/v1/clusters/KAFKANAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")'`

    export KAFKABROKERS=`curl --silent -u admin:'PASSWORD' -G http://headnodehost:8080/api/v1/clusters/KAFKANAME/services/HDFS/components/DATANODE | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")'`

    echo '$KAFKAZKHOSTS='$KAFKAZKHOSTS
    echo '$KAFKABROKERS='$KAFKABROKERS
    ```

    Ниже приведен пример содержимого `$KAFKAZKHOSTS`:
   
    `zk0-kafka.eahjefxxp1netdbyklgqj5y1ud.ex.internal.cloudapp.net:2181,zk2-kafka.eahjefxxp1netdbyklgqj5y1ud.ex.internal.cloudapp.net:2181,zk3-kafka.eahjefxxp1netdbyklgqj5y1ud.ex.internal.cloudapp.net:2181`
   
    Ниже приведен пример содержимого `$KAFKABROKERS`:
   
    `wn1-kafka.eahjefxxp1netdbyklgqj5y1ud.cx.internal.cloudapp.net:9092,wn0-kafka.eahjefxxp1netdbyklgqj5y1ud.cx.internal.cloudapp.net:9092`
   
    > [!WARNING]
    > Учтите, что данные, возвращаемые этим сеансом, могут быть неточными. Масштабирование кластера обычно сопровождается добавлением или удалением брокеров. Если происходит сбой, и узел заменяется, имя узла также может измениться. 
    > 
    > Чтобы получить допустимые значения, всегда следует извлекать сведения об узлах Zookeeper и брокера непосредственно перед их использованием.

## <a name="create-a-topic"></a>Создание раздела

Kafka хранит потоки данных в категориях, называемых *разделами*. Чтобы создать такой раздел, запустите скрипт, который поставляется с Kafka, используя SSH-подключение к головному узлу кластера:

```bash
/usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 2 --partitions 8 --topic test --zookeeper $KAFKAZKHOSTS
```

Эта команда создает подключение к Zookeeper, используя хранящиеся в `$KAFKAZKHOSTS` сведения об узле, а затем создает раздел Kafka с именем **test**. Чтобы убедиться, что раздел создан, используйте следующий скрипт, который отображает список разделов.

```bash
/usr/hdp/current/kafka-broker/bin/kafka-topics.sh --list --zookeeper $KAFKAZKHOSTS
```

В выходных данных этой команды перечислены разделы Kafka, среди которых должен быть и новый раздел **test**.

## <a name="produce-and-consume-records"></a>Создание и использование записей

Kafka хранит *записи* в разделах. Записи создаются *производителями*, а используются *потребителями*. Производители извлекают записи из *брокеров* Kafka. Каждый рабочий узел в кластере HDInsight — это брокер Kafka.

Ниже описано, как сначала сохранить записи в созданный ранее тестовый раздел, а затем считать их с помощью потребителя.

1. Запустите сеанс SSH и выполните скрипт, предоставленный службой Kafka для сохранения записей в раздел:
   
    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list $KAFKABROKERS --topic test
    ```
   
    Эта команда не возвращает запрос после запуска. Вместо этого введите несколько текстовых сообщений, а затем нажмите клавиши **Ctrl+C**, чтобы остановить отправку данных в раздел. Каждая строка отправляется как отдельная запись.

2. Используйте скрипт, поставляемый с платформой Kafka, чтобы считать записи из раздела:
   
    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --zookeeper $KAFKAZKHOSTS --topic test --from-beginning
    ```
   
    Записи будут извлечены из раздела, а затем отображены. Параметр `--from-beginning` указывает потребителю считывать данные с самого начала потока, поэтому будут извлечены все записи.

3. Нажмите клавиши __Ctrl+C__, чтобы остановить потребитель.

## <a name="producer-and-consumer-api"></a>API производителя и потребителя

Вы также можете создавать и использовать записи программным способом — с помощью [API-интерфейсов Kafka](http://kafka.apache.org/documentation#api). Ниже описано, как скачать и создать производитель и потребитель на основе Java.

1. Скачайте примеры на странице [https://github.com/Azure-Samples/hdinsight-kafka-java-get-started](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started). В примере с производителем и потребителем используется проект в каталоге `Producer-Consumer`. В примере ниже используются следующие классы.
   
    * **Run** — запускает потребителя или производителя.

    * **Producer** — сохраняет 1 000 000 записей в раздел.

    * **Consumer** — считывает записи из раздела.

2. Из командной строки в среде разработки измените путь к каталогам на расположение каталога `Producer-Consumer` с примером. Затем используйте следующую команду, чтобы создать пакет JAR:
   
    ```
    mvn clean package
    ```
   
    Эта команда создает каталог с именем `target`, который содержит файл с именем `kafka-producer-consumer-1.0-SNAPSHOT.jar`.

3. С помощью следующих команд скопируйте файл `kafka-producer-consumer-1.0-SNAPSHOT.jar` в свой кластер HDInsight.
   
    ```bash
    scp ./target/kafka-producer-consumer-1.0-SNAPSHOT.jar SSHUSER@CLUSTERNAME-ssh.azurehdinsight.net:kafka-producer-consumer.jar
    ```
   
    Замените **SSHUSER** именем пользователя SSH для кластера, а **CLUSTERNAME** — именем кластера. При появлении запроса введите пароль пользователя SSH.

4. Когда команда `scp` завершит копирование файла, подключитесь к кластеру по протоколу SSH. Затем используйте следующую команду, чтобы сохранить записи в созданный ранее тестовый раздел.
   
    ```bash
    ./kafka-producer-consumer.jar producer $KAFKABROKERS
    ```
   
    Эта команда запустит производителя и начнет операцию записи. Также появится счетчик, отображающий число сохраненных записей.

    > [!NOTE]
    > Если возникнет ошибка отказа в доступе, используйте следующую команду, чтобы сделать файл исполняемым: ```chmod +x kafka-producer-consumer.jar```.

5. Когда процесс завершится, используйте следующую команду для считывания записей из раздела:
   
    ```bash
    ./kafka-producer-consumer.jar consumer $KAFKABROKERS
    ```
   
    Отобразятся считанные записи, а также их количество. Вы можете увидеть, что в разделе сохранено больше 1 000 000 записей, так как ранее мы уже отправили несколько записей с помощью скрипта.

6. Нажмите клавиши __Ctrl+C__, чтобы закрыть потребитель.

### <a name="multiple-consumers"></a>Несколько потребителей

Особенностью платформы Kafka является то, что при считывании записей потребители включаются в одну группу потребителей (определяется идентификатором группы). Использование одной группы с несколькими потребителями приведет к считыванию записей с балансировкой нагрузки из раздела. Каждый потребитель в группе получает часть записей. Увидеть это в действии можно так:

1. Откройте еще один сеанс SSH в кластере (сеансов должно быть два). В каждом сеансе используйте следующую команду, чтобы запустить потребитель с идентификатором группы потребителей:
   
    ```bash
    ./kafka-producer-consumer.jar consumer $KAFKABROKERS mygroup
    ```

    > [!NOTE]
    > В новом сеансе SSH необходимо выполнить команды, приведенные в разделе [Получение сведений об узлах Zookeeper и брокера](#getkafkainfo), чтобы определить `$KAFKABROKERS`.

2. Вы увидите, как каждый сеанс отображает число записей, получаемых из раздела. Общее число, полученное в двух сеансах, должно совпадать с ранее полученным числом от одного потребителя.

Потребление по клиентам, входящих в одну группу, обрабатывается по секциям раздела. Созданный ранее раздел `test` содержит восемь секций. Если открыть восемь сеансов SSH и запустить в них потребителей, каждый потребитель будет считывать записи из одной секции этого раздела.

> [!IMPORTANT]
> Число экземпляров потребителя, входящих в группу потребителей, должно соответствовать числу секций. В нашем примере одна группа потребителей может содержать не больше восьми потребителей, так как это — число секций в разделе. Но у вас может быть несколько групп потребителей, в каждую из которых входит не более восьми потребителей.

Записи хранятся в Kafka в том порядке, в котором они поступают в секцию. Чтобы обеспечить упорядоченную доставку записей *в секцию*, нужно создать группу потребителей. При этом число входящих в нее экземпляров потребителя должно соответствовать числу секций. Чтобы обеспечить упорядоченную доставку записей *в раздел*, нужно создать группу потребителей, которая содержит только один экземпляр потребителя.

## <a name="streaming-api"></a>API для потоковой передачи

API для потоковой передачи реализован в версии Kafka 0.10.0. В более ранних выпусках для обработки потоков используются Apache Spark или Storm.

1. При необходимости скачайте примеры на странице [https://github.com/Azure-Samples/hdinsight-kafka-java-get-started](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started) и сохраните их в своей среде разработки. В этом примере потоковой передачи используется проект в каталоге `streaming`.
   
    Этот проект содержит только один класс `Stream`, который считывает данные из ранее созданного раздела `test`. Он подсчитывает считанные слова, передавая каждое слово и их число в раздел с именем `wordcounts`. (Раздел `wordcounts` в нашем примере создается позже.)

2. Из командной строки в среде разработки измените путь к каталогам на расположение каталога `Streaming`. Затем используйте следующую команду, чтобы создать пакет JAR.
   
    ```
    mvn clean package
    ```
   
    Эта команда создает каталог с именем `target`, который содержит файл с именем `kafka-streaming-1.0-SNAPSHOT.jar`.

3. С помощью следующих команд скопируйте файл `kafka-streaming-1.0-SNAPSHOT.jar` в свой кластер HDInsight.
   
    ```bash
    scp ./target/kafka-streaming-1.0-SNAPSHOT.jar SSHUSER@CLUSTERNAME-ssh.azurehdinsight.net:kafka-streaming.jar
    ```
   
    Замените **SSHUSER** именем пользователя SSH для кластера, а **CLUSTERNAME** — именем кластера. При появлении запроса введите пароль пользователя SSH.

4. Когда команда `scp` завершит копирование файла, подключитесь к кластеру по протоколу SSH. Затем используйте следующую команду, чтобы создать раздел `wordcounts`.

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 2 --partitions 8 --topic wordcounts --zookeeper $KAFKAZKHOSTS
    ```

5. Теперь запустите процесс потоковой передачи с помощью приведенной ниже команды.
   
    ```bash
    ./kafka-streaming.jar $KAFKABROKERS $KAFKAZKHOSTS 2>/dev/null &
    ```
   
    Потоковая передача будет запущена в фоновом режиме.

6. Используйте следующую команду для отправки сообщений в раздел `test`. В рамках потоковой передачи начнется их обработка:
   
    ```bash
    ./kafka-producer-consumer.jar producer $KAFKABROKERS &>/dev/null &
    ```

7. Используйте приведенную ниже команду для просмотра выходных данных, записанных в раздел `wordcounts` процессом потоковой передачи.
   
    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --zookeeper $KAFKAZKHOSTS --topic wordcounts --from-beginning --formatter kafka.tools.DefaultMessageFormatter --property print.key=true --property key.deserializer=org.apache.kafka.common.serialization.StringDeserializer --property value.deserializer=org.apache.kafka.common.serialization.LongDeserializer
    ```
   
    > [!NOTE]
    > Чтобы просмотреть данные, необходимо указать потребителю вывести ключ (который содержит значение) и десериализатор. Имя ключа — это слово, а значение ключа содержит число.
   
    Результат будет аналогичен приведенному ниже:
   
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
        a       13805
        snow    13637
   
    > [!NOTE]
    > Число увеличивается каждый раз, когда в сообщении встречается слово.

7. Нажмите клавиши __Ctrl+C__, чтобы закрыть потребитель, а затем с помощью команды `fg` выведите фоновую задачу потоковой передачи на передний план. Для завершения работы также нажмите клавиши __Ctrl+C__.

## <a name="delete-the-cluster"></a>Удаление кластера

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="next-steps"></a>Дальнейшие действия

В этой статье описаны основные принципы работы с Apache Kafka в HDInsight. Дополнительные сведения о работе с Kafka см. в следующих материалах.

* [Документация по Apache Kafka](http://kafka.apache.org/documentation.html) на сайте kafka.apache.org
* [Use MirrorMaker to create a replica of a Kafka on HDInsight cluster (preview)](hdinsight-apache-kafka-mirroring.md) (Создание реплики Kafka в кластере HDInsight с помощью MirrorMaker (предварительная версия))
* [Совместное использование Apache Kafka (предварительная версия) и Storm в HDInsight](hdinsight-apache-storm-with-kafka.md)
* [Совместное использование Apache Spark и Kafka (предварительная версия) в HDInsight](hdinsight-apache-spark-with-kafka.md)




<!--HONumber=Feb17_HO3-->



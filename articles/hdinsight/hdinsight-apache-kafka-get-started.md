---
title: "Приступая к работе с Apache Kafka в HDInsight | Документация Майкрософт"
description: "Базовые сведения о создании и работе с Kafka в HDInsight."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: paulettm
editor: cgronlun
ms.assetid: 43585abf-bec1-4322-adde-6db21de98d7f
ms.service: hdinsight
ms.devlang: 
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/09/2016
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 2d744e753224e4ce98680d3228914fd89e87eba4
ms.openlocfilehash: 70d910f61fa414a0c58a22057133ef3b899dac05

---
# <a name="get-started-with-apache-kafka-preview-on-hdinsight"></a>Приступая к работе с Apache Kafka (предварительная версия) в HDInsight

[Apache Kafka](https://kafka.apache.org) — это распределенная платформа потоковой передачи с открытым кодом, которая доступна в HDInsight. Она часто используется как брокер сообщений, предоставляя такие же функциональные возможности, как и очередь сообщений типа "публикация-подписка". Из этой статьи вы узнаете, как создать Kafka в кластере HDInsight, а затем отправить и получить данные из приложения Java.

> [!NOTE]
> Сейчас в HDInsight доступны две версии Kafka: 0.9.0 (HDInsight 3.4) и 0.10.0 (HDInsight 3.5). В этой статье предполагается, что вы используете Kafka с версией HDInsight 3.5.

## <a name="prerequisite"></a>Предварительные требования

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Необходимое условие для успешного выполнения инструкций этого учебника:

* **Подписка Azure**. Ознакомьтесь с [бесплатной пробной версией Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

* **Знакомство с SSH и SCP**. Дополнительные сведения об использовании SSH и SCP с HDInsight см. в следующих ресурсах:
  
   * **Для клиентов Linux, Unix или OS X**: ознакомьтесь с разделом [Использование SSH с Hadoop на основе Linux в HDInsight из Linux, Unix или OS X](hdinsight-hadoop-linux-use-ssh-unix.md).
   
   * **Для клиентов Windows**: ознакомьтесь с разделом [Использование SSH с Hadoop на основе Linux в HDInsight из Windows](hdinsight-hadoop-linux-use-ssh-windows.md).

* [Java JDK 8](http://www.oracle.com/technetwork/java/javase/downloads/index.html) или эквивалент, например OpenJDK.

* [Apache Maven](http://maven.apache.org/) 

### <a name="access-control-requirements"></a>Требования к контролю доступа

[!INCLUDE [access-control](../../includes/hdinsight-access-control-requirements.md)]

## <a name="create-a-kafka-cluster"></a>Создание кластера Kafka

Чтобы создать Kafka в кластере HDInsight, сделайте следующее.

1. На [портале Azure](https://portal.azure.com) последовательно выберите элементы **+Создать**, **Аналитика** и **HDInsight**.
   
    ![Создание кластера HDInsight](./media/hdinsight-apache-kafka-get-started/create-hdinsight.png)

2. В колонке **Новый кластер HDInsight** укажите **имя кластера** и выберите **подписку** для использования с этим кластером.
   
    ![Выберите подписку.](./media/hdinsight-apache-kafka-get-started/new-hdinsight-cluster-blade.png)

3. **Выберите тип кластера** и укажите следующие значения в колонке **Конфигурация типа кластера**.
   
    * **Тип кластера**: Kafka.

    * **Версия**: Kafka 0.10.0 (HDI 3.5).

    * **Ценовая категория кластера**: "Стандартный".
     
    Затем нажмите кнопку **Выбрать**, чтобы сохранить эти параметры.
     
    ![Выбор типа кластера](./media/hdinsight-apache-kafka-get-started/cluster-type.png)

4. В колонке **Учетные данные кластера** настройте имя для входа в кластер и учетные данные пользователя SSH.  Нажмите кнопку **Выбрать**, чтобы сохранить эти параметры.
   
    > [!NOTE]
    > Имя для входа в кластер используется при обращении к кластеру через Интернет по протоколу HTTPS. Сведения о пользователе SSH позволяют подключаться к кластеру и выполнять команды в интерактивном режиме.
   
    ![Настройка имени для входа в кластер](./media/hdinsight-apache-kafka-get-started/cluster-credentials.png)
   
    Дополнительные сведения об использовании SSH с HDInsight см. в следующих статьях.
   
    * [Использование SSH с Hadoop на основе Linux в HDInsight из Linux, Unix или OS X](hdinsight-hadoop-linux-use-ssh-unix.md)
   
    * [Использование SSH с Hadoop на основе Linux в HDInsight из Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

5. В колонке **Источник данных** настройте основное хранилище данных для кластера. Чтобы создать такое хранилище, в колонке **Источник данных** укажите следующие сведения.
   
    * Щелкните **Создать** и укажите имя учетной записи хранения.
    
    * Выберите близкое к вам географическое **расположение**. Это расположение используется для создания и учетной записи хранения, и кластера HDInsight.
     
   Затем нажмите кнопку **Выбрать**, чтобы сохранить эти параметры.
     
    ![Настройка хранилища](./media/hdinsight-apache-kafka-get-started/configure-storage.png)

6. В колонке **Цены** укажите **число рабочих узлов** — 2. Использование двух рабочих узлов снижает расходы на кластер; кроме того, для нашего примера больше улов не требуется. Нажмите кнопку **Выбрать**, чтобы сохранить эти параметры.
   
    ![Цены](./media/hdinsight-apache-kafka-get-started/pricing.png)
   
    > [!NOTE]
    > Указанные на портале цены могут отличаться от цен на снимке экрана.

7. Создайте **группу ресурсов**, указав имя в соответствующем поле. Также установите флажок **Закрепить на панели мониторинга**. Завершив настройку, нажмите кнопку **Создать** для создания кластера.
   
    ![Поле "Группа ресурсов"](./media/hdinsight-apache-kafka-get-started/resource-group.png)
   
    > [!NOTE]
    > Операция создания кластера может занять до 20 минут.

## <a name="connect-to-the-cluster"></a>Подключение к кластеру

На своем клиенте используйте SSH, чтобы подключиться к кластеру. Если вы используете Linux, Unix, MacOS или Bash в Windows 10, используйте следующую команду:

    ssh SSHUSER@CLUSTERNAME-ssh.azurehdinsight.net

Замените **SSHUSER** именем пользователя SSH, указанным при создании кластера. Замените **CLUSTERNAME** именем кластера.

При появлении запроса введите пароль, который используется для учетной записи SSH.

Дополнительные сведения об использовании SSH с HDInsight см. в следующих статьях.

* [Использование SSH с Hadoop под управлением Linux в HDInsight в Linux, Unix или OS X](hdinsight-hadoop-linux-use-ssh-unix.md)

* [Использование SSH с Hadoop под управлением Linux в HDInsight в Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

##<a name="a-idgetkafkainfoaget-the-zookeeper-and-broker-host-information"></a><a id="getkafkainfo"></a>Получение сведений об узлах Zookeeper и брокера

Для работы с Kafka вам нужно предоставить данные о двух узлах: *Zookeeper* и *брокера*. Эти узлы используются Kafka API и многими другими служебными программами, поставляемыми с платформой Kafka.

Ниже описано, как создать переменные среды, которые содержат сведения об узле. Эти переменные используются в нашем примере далее.

1. Используя SSH-подключение к кластеру, выполните следующую команду, чтобы установить служебную программу `jq`. Эта служебная программа используется для синтаксического анализа документов JSON. С ее помощью также удобно получать сведения об узле брокера.
   
        sudo apt -y install jq

2. Используйте следующие команды, чтобы определить переменные среды на основе информации, полученной из Ambari. Замените __KAFKANAME__ именем кластера Kafka. Замените __PASSWORD__ паролем администратора, который использовался при создании кластера.

        export KAFKAZKHOSTS=`curl --silent -u admin:PASSWORD -G http://headnodehost:8080/api/v1/clusters/KAFKANAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")'`

        export KAFKABROKERS=`curl --silent -u admin:PASSWORD -G http://headnodehost:8080/api/v1/clusters/KAFKANAME/services/HDFS/components/DATANODE | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")'`

        echo '$KAFKAZKHOSTS='$KAFKAZKHOSTS
        echo '$KAFKABROKERS='$KAFKABROKERS

    Ниже приведен пример содержимого `$KAFKAZKHOSTS`:
   
        zk0-kafka.eahjefxxp1netdbyklgqj5y1ud.ex.internal.cloudapp.net:2181,zk2-kafka.eahjefxxp1netdbyklgqj5y1ud.ex.internal.cloudapp.net:2181,zk3-kafka.eahjefxxp1netdbyklgqj5y1ud.ex.internal.cloudapp.net:2181
   
    Ниже приведен пример содержимого `$KAFKABROKERS`:
   
        wn1-kafka.eahjefxxp1netdbyklgqj5y1ud.cx.internal.cloudapp.net:9092,wn0-kafka.eahjefxxp1netdbyklgqj5y1ud.cx.internal.cloudapp.net:9092
   
    > [!WARNING]
    > Учтите, что данные, возвращаемые этим сеансом, могут быть неточными. Масштабирование кластера обычно сопровождается добавлением или удалением брокеров. Если происходит сбой, и узел заменяется, имя узла также может измениться. 
    > 
    > Чтобы получить допустимые значения, всегда следует извлекать сведения об узлах Zookeeper и брокера непосредственно перед их использованием.

## <a name="create-a-topic"></a>Создание раздела

Kafka хранит потоки данных в категориях, называемых *разделами*. Чтобы создать такой раздел, запустите скрипт, который поставляется с Kafka, используя SSH-подключение к головному узлу кластера:

    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 2 --partitions 8 --topic test --zookeeper $KAFKAZKHOSTS

Эта команда создает подключение к Zookeeper, используя хранящиеся в `$KAFKAZKHOSTS` сведения об узле, а затем создает раздел Kafka с именем **test**. Чтобы убедиться, что раздел создан, используйте следующий скрипт, который отображает список разделов.

    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --list --zookeeper $KAFKAZKHOSTS

В выходных данных этой команды перечислены разделы Kafka, среди которых должен быть и новый раздел **test**.

## <a name="produce-and-consume-records"></a>Создание и использование записей

Kafka хранит *записи* в разделах. Записи создаются *производителями*, а используются *потребителями*. Производители извлекают записи из *брокеров* Kafka. Каждый рабочий узел в кластере HDInsight — это брокер Kafka.

Ниже описано, как сначала сохранить записи в созданный ранее тестовый раздел, а затем считать их с помощью потребителя.

1. Запустите сеанс SSH и выполните скрипт, предоставленный службой Kafka для сохранения записей в раздел:
   
        /usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list $KAFKABROKERS --topic test
   
    Эта команда не возвращает запрос после запуска. Вместо этого введите несколько текстовых сообщений, а затем нажмите клавиши **Ctrl+C**, чтобы остановить отправку данных в раздел. Каждая строка отправляется как отдельная запись.

2. Используйте скрипт, поставляемый с платформой Kafka, чтобы считать записи из раздела:
   
        /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --zookeeper $KAFKAZKHOSTS --topic test --from-beginning
   
    Записи будут извлечены из раздела, а затем отображены. Параметр `--from-beginning` указывает потребителю считывать данные с самого начала потока, поэтому будут извлечены все записи.

3. Нажмите клавиши __Ctrl+C__, чтобы остановить потребитель.

## <a name="producer-and-consumer-api"></a>API производителя и потребителя

Вы также можете создавать и использовать записи программным способом — с помощью [API-интерфейсов Kafka](http://kafka.apache.org/documentation#api). Ниже описано, как скачать и создать производитель и потребитель на основе Java.

1. Скачайте примеры на странице [https://github.com/Azure-Samples/hdinsight-kafka-java-get-started](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started). В примере с производителем и потребителем используется проект в каталоге `Producer-Consumer`. Обязательно просмотрите код, чтобы понять, как работает этот механизм. Код содержит следующие классы:
   
    * **Run** — запуск потребителя или производителя на основе аргументов командной строки.

    * **Producer** — сохраняет 1 000 000 записей в раздел.

    * **Consumer** — считывает записи из раздела.

2. Из командной строки в среде разработки измените путь к каталогам на расположение каталога `Producer-Consumer` с примером. Затем используйте следующую команду, чтобы создать пакет JAR:
   
        mvn clean package
   
    Эта команда создает каталог с именем `target`, который содержит файл с именем `kafka-producer-consumer-1.0-SNAPSHOT.jar`.

3. С помощью следующих команд скопируйте файл `kafka-producer-consumer-1.0-SNAPSHOT.jar` в свой кластер HDInsight.
   
        scp ./target/kafka-producer-consumer-1.0-SNAPSHOT.jar SSHUSER@CLUSTERNAME-ssh.azurehdinsight.net:kafka-producer-consumer.jar
   
    Замените **SSHUSER** именем пользователя SSH для кластера, а **CLUSTERNAME** — именем кластера. При появлении запроса введите пароль пользователя SSH.

4. Когда команда `scp` завершит копирование файла, подключитесь к кластеру по протоколу SSH. Затем используйте следующую команду, чтобы сохранить записи в созданный ранее тестовый раздел.
   
        ./kafka-producer-consumer.jar producer $KAFKABROKERS
   
    Запущенный производитель начнет операцию записи. Также появится счетчик, отображающий число сохраненных записей.

    > [!NOTE]
    > Если возникнет ошибка отказа в доступе, используйте следующую команду, чтобы сделать файл исполняемым: ```chmod +x kafka-producer-consumer.jar```.

5. Когда процесс завершится, используйте следующую команду для считывания записей из раздела:
   
        ./kafka-producer-consumer.jar consumer $KAFKABROKERS
   
    Отобразятся считанные записи, а также их количество. Вы можете увидеть, что в раздел сохранено больше 1 000 000 записей, так как ранее мы уже отправили несколько записей с помощью скрипта.

6. Нажмите клавиши __Ctrl+C__, чтобы закрыть потребитель.

### <a name="multiple-consumers"></a>Несколько потребителей

Особенностью платформы Kafka является то, что при считывании записей потребители включаются в одну группу потребителей (определяется идентификатором группы). При считывании записей входящими в такую группу потребителями выполняется балансировка нагрузки, чтобы каждый потребитель получил свою порцию данных. Увидеть это в действии можно так.

1. Откройте еще один сеанс SSH в кластере (сеансов должно быть два). В каждом сеансе используйте следующую команду, чтобы запустить потребитель с идентификатором группы потребителей:
   
        ./kafka-producer-consumer.jar consumer $KAFKABROKERS mygroup

    > [!NOTE]
    > В новом сеансе SSH необходимо выполнить команды, приведенные в разделе [Получение сведений об узлах Zookeeper и брокера](#getkafkainfo), чтобы определить `$KAFKABROKERS`.

2. Вы увидите, как каждый сеанс отображает число записей, получаемых из раздела. Общее число, полученное в двух сеансах, должно совпадать с ранее полученным числом от одного потребителя.

Потребление по клиентам, входящих в одну группу, обрабатывается по секциям раздела. Созданный ранее раздел `test` содержит восемь секций. Если открыть восемь сеансов SSH и запустить в них потребители, каждый потребитель будет считывать записи из одной секции этого раздела.

> [!IMPORTANT]
> Число экземпляров потребителя, входящих в группу потребителей, должно соответствовать числу секций. В нашем примере одна группа потребителей может содержать не больше восьми потребителей, так как это — число секций в разделе. Но у вас может быть несколько групп потребителей, в каждую из которых входит не более восьми потребителей.

Записи хранятся в Kafka в том порядке, в котором они поступают в секцию. Чтобы обеспечить упорядоченную доставку записей *в секцию*, нужно создать группу потребителей. При этом число входящих в нее экземпляров потребителя должно соответствовать числу секций. Чтобы обеспечить упорядоченную доставку записей *в раздел*, нужно создать группу потребителей, которая содержит только один экземпляр потребителя.

## <a name="streaming-api"></a>API для потоковой передачи

API для потоковой передачи реализован в версии Kafka 0.10.0. В более ранних выпусках для обработки потоков используются Apache Spark или Storm.

1. При необходимости скачайте примеры на странице [https://github.com/Azure-Samples/hdinsight-kafka-java-get-started](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started). В этом примере потоковой передачи используется проект в каталоге `streaming`. Обязательно просмотрите код, чтобы понять, как работает этот механизм. 
   
    Этот проект содержит только один класс `Stream`, который считывает данные из ранее созданного раздела `test`. Он подсчитывает считанные слова, передавая каждое слово и их число в раздел с именем `wordcounts`. (Раздел `wordcounts` в нашем примере создается позже.)

2. Из командной строки в среде разработки измените путь к каталогам на расположение каталога `Streaming`. Затем используйте следующую команду, чтобы создать пакет JAR:
   
        mvn clean package
   
    Эта команда создает каталог с именем `target`, который содержит файл с именем `kafka-streaming-1.0-SNAPSHOT.jar`.

3. С помощью следующих команд скопируйте файл `kafka-streaming-1.0-SNAPSHOT.jar` в свой кластер HDInsight.
   
        scp ./target/kafka-streaming-1.0-SNAPSHOT.jar SSHUSER@CLUSTERNAME-ssh.azurehdinsight.net:kafka-streaming.jar
   
    Замените **SSHUSER** именем пользователя SSH для кластера, а **CLUSTERNAME** — именем кластера. При появлении запроса введите пароль пользователя SSH.

4. Когда команда `scp` завершит копирование файла, подключитесь к кластеру по протоколу SSH. Затем используйте следующую команду, чтобы запустить потоковую передачу:
   
        ./kafka-streaming.jar $KAFKABROKERS $KAFKAZKHOSTS 2>/dev/null &
   
    Потоковая передача будет запущена в фоновом режиме.

5. Используйте следующую команду для отправки сообщений в раздел `test`. В рамках потоковой передачи начнется их обработка:
   
        ./kafka-producer-consumer.jar producer $KAFKABROKERS &>/dev/null &

6. Используйте следующую команду для просмотра выходных данных, записанных в раздел `wordcounts`:
   
        /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --zookeeper $KAFKAZKHOSTS --topic wordcounts --from-beginning --formatter kafka.tools.DefaultMessageFormatter --property print.key=true --property key.deserializer=org.apache.kafka.common.serialization.StringDeserializer --property value.deserializer=org.apache.kafka.common.serialization.LongDeserializer
   
    > [!NOTE]
    > Мы должны указать потребителю вывести ключ (который содержит значение слова) и десериализатор, с помощью которого можно просматривать данные, представленные ключом и значением.
   
    Выход аналогичен приведенному ниже:
   
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
   
    Учтите, что число увеличивается каждый раз, когда в сообщении встречается слово.

7. Нажмите клавиши __Ctrl+C__, чтобы закрыть потребитель, а затем с помощью команды `fg` выведите фоновую задачу потоковой передачи на передний план. Для завершения работы также нажмите клавиши __Ctrl+C__.

## <a name="next-steps"></a>Дальнейшие действия

В этой статье описаны основные принципы работы с Apache Kafka в HDInsight. Дополнительные сведения о работе с Kafka см. в следующих материалах.

* [Документация по Apache Kafka](http://kafka.apache.org/documentation.html) на сайте kafka.apache.org
* [Use MirrorMaker to create a replica of a Kafka on HDInsight cluster (preview)](hdinsight-apache-kafka-mirroring.md) (Создание реплики Kafka в кластере HDInsight с помощью MirrorMaker (предварительная версия))
* [Совместное использование Apache Kafka (предварительная версия) и Storm в HDInsight](hdinsight-apache-storm-with-kafka.md)
* [Совместное использование Apache Spark и Kafka (предварительная версия) в HDInsight](hdinsight-apache-spark-with-kafka.md)




<!--HONumber=Nov16_HO3-->



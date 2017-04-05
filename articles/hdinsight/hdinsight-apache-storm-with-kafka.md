---
title: "Совместное использование Apache Kafka и Storm в HDInsight | Документация Майкрософт"
description: "Платформа Apache Kafka устанавливается вместе с Apache Storm в службе HDInsight. Из этой статьи вы узнаете, как записывать и считывать данные в Kafka с помощью компонентов KafkaBolt и KafkaSpout, поставляемых в составе решения Storm. Также вы узнаете, как использовать платформу Flux для определения и отправки топологий Storm."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: paulettm
editor: cgronlun
ms.assetid: e4941329-1580-4cd8-b82e-a2258802c1a7
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 03/20/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: dcda5e27cbcadff054c8085b72a1b6fb1c07b889
ms.lasthandoff: 03/25/2017

---
# <a name="use-apache-kafka-preview-with-storm-on-hdinsight"></a>Совместное использование Apache Kafka (предварительная версия) и Storm в HDInsight

Apache Kafka — это решение для обмена сообщениями по схеме "публикация — подписка", доступное в HDInsight. Apache Storm является распределенной системой, которая может использоваться для анализа данных в реальном времени. В этом документе показано, как использовать Storm в HDInsight для чтения и обработки данных из Kafka в HDInsight. В примере, приведенном в этой статье, используется топология Storm на языке Java, в основу которой положены воронка и сито Kafka, доступные в Apache Storm.

> [!NOTE]
> С помощью описанных здесь шагов будет создана группа ресурсов Azure, которая содержит кластеры Storm в HDInsight и Kafka в HDInsight. Оба этих кластера находятся в виртуальной сети Azure, что позволяет кластеру Storm напрямую обмениваться данными с кластером Kafka.
> 
> Выполнив инструкции, не забудьте удалить кластеры, чтобы избежать ненужных расходов.

## <a name="prerequisites"></a>Предварительные требования

* Пакет [Java JDK](http://www.oracle.com/technetwork/java/javase/downloads/index.html) 1.8 или более поздней версии либо его эквивалент, например [OpenJDK](http://openjdk.java.net/).
  
    > [!NOTE]
    > В приведенных здесь инструкциях используется кластер HDInsight 3.5, использующий Java 8.

* [Maven 3.x](http://maven.apache.org/) — пакет управления сборкой для приложений Java.

* Текстовый редактор или Java IDE.

* SSH-клиент (требуются команды `ssh` и `scp`). Дополнительные сведения см. в разделе [Подключение к HDInsight (Hadoop) с помощью SSH](hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="create-the-clusters"></a>Создание кластеров

Apache Kafka в HDInsight не предоставляет доступ к брокерам Kafka через общедоступный сегмент Интернета. Все объекты, обращающиеся к Kafka, должны находиться в той же виртуальной сети Azure, что и узлы в кластере Kafka. В этом примере кластеры Storm и Kafka расположены в виртуальной сети Azure. На следующей схеме показано, как взаимодействуют кластеры.

![Схема кластеров Storm и Kafka в виртуальной сети Azure](./media/hdinsight-apache-storm-with-kafka/storm-kafka-vnet.png)

> [!NOTE]
> Хотя само решение Kafka ограничено связью в пределах виртуальной сети, другие службы в кластере, например SSH и Ambari, доступны через Интернет. Дополнительные сведения об общих портах, доступных в HDInsight, см. в статье [Порты и универсальные коды ресурсов (URI), используемые кластерами HDInsight](hdinsight-hadoop-port-settings-for-services.md).


Виртуальную сеть Azure, кластеры Kafka и Storm можно создать вручную, но чтобы упростить задачу, воспользуйтесь шаблоном Azure Resource Manager. Выполните следующие действия, чтобы развернуть виртуальную сеть Azure, а также кластеры Kafka и Storm в подписке Azure.

1. Нажмите эту кнопку, чтобы войти в Azure и открыть шаблон на портале Azure.
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-kafka-storm-cluster-in-vnet.json" target="_blank"><img src="./media/hdinsight-apache-storm-with-kafka/deploy-to-azure.png" alt="Deploy to Azure"></a>
   
    Шаблон Azure Resource Manager находится по адресу **https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-kafka-storm-cluster-in-vnet.json**.

2. Используйте следующие инструкции для заполнения колонки **Настраиваемое развертывание**.
   
    ![Настраиваемое развертывание в HDInsight](./media/hdinsight-apache-storm-with-kafka/parameters.png)

    * **Группа ресурсов.** Создайте новую группу ресурсов или выберите существующую. Эта группа содержит кластер HDInsight.
   
    * **Расположение.** Выберите близкое к вам географическое расположение. Оно должно соответствовать расположению в разделе __Параметры__.

    * **Base Cluster Name** (Базовое имя кластера). Это значение будет использоваться в качестве базового имени для кластеров Storm и Kafka. Например, если ввести **hdi**, будет создан кластер Storm **storm-hdi** и кластер Kafka **kafka-hdi**.
   
    * **Cluster Login User Name** (Имя пользователя для входа в кластер). Имя администратора для кластеров Storm и Kafka.
   
    * **Cluster Login User Password** (Пароль пользователя для входа в кластер). Имя администратора для кластеров Storm и Kafka.
    
    * **Имя пользователя SSH.** Создаваемый пользователь SSH для кластеров Storm и Kafka.
    
    * **Пароль SSH.** Пароль пользователя SSH для кластеров Storm и Kafka.
    
    * **Расположение.** Регион, в котором создаются кластеры.

3. Прочтите **условия использования** и установите флажок **Я принимаю указанные выше условия**.

4. Установите флажок **Закрепить на панели мониторинга** и нажмите кнопку **Приобрести**. Процесс создания кластеров занимает около 20 минут.

Когда указанные ресурсы будут созданы, отобразится колонка группы ресурсов, которая содержит кластеры и веб-панель мониторинга.

![Колонка группы ресурсов для виртуальной сети и кластеров](./media/hdinsight-apache-storm-with-kafka/groupblade.png)

> [!IMPORTANT]
> Обратите внимание, что кластерам HDInsight присвоены имена **storm-BASENAME** и **kafka-BASENAME**, где BASENAME — имя, указанное в шаблоне. Эти имена будут использоваться позже при подключении к кластерам.

## <a name="get-the-code"></a>Получение кода

Код для примера, описанного в этом документе, доступен по адресу [https://github.com/Azure-Samples/hdinsight-storm-java-kafka](https://github.com/Azure-Samples/hdinsight-storm-java-kafka).

## <a name="understanding-the-code"></a>Основные сведения о коде

Этот проект содержит две топологии:

* **KafkaWriter.** Эта топология, определяемая файлом **writer.yaml**, записывает случайные предложения в Kafka с помощью сита KafkaBolt, входящего в состав Apache Storm.
  
    Эта топология генерирует случайные предложения с помощью настраиваемого компонента **SentenceSpout**.

* **KafkaReader.** Эта топология, определяемая файлом **reader.yaml**, считывает данные из Kafka с помощью воронки KafkaSpout, входящей в состав Apache Storm, а затем записывает данные в журнал stdout.
  
    Эта топология записывает в журнал данные, считанные из Kafka, с помощью настраиваемого компонента **PrinterBolt**.

### <a name="flux"></a>Flux

Топологии определяются с помощью [Flux](https://storm.apache.org/releases/1.0.1/flux.html). Решение Flux, которое впервые было представлено в версии Storm 0.10.x, позволяет отделить конфигурацию топологии от кода. Для топологий, которые используют платформу Flux, топология определяется в YAML-файле. YAML-файл можно включить в топологию или указать при отправке топологии на сервер Storm. Flux также поддерживает подстановку переменных во время выполнения, которая используется в этом примере.

В обеих топологиях следует указывать следующие переменные среды:

* **KAFKATOPIC**: имя раздела Kafka, в котором топология выполняет чтение и запись.

* **KAFKABROKERS**: узлы, на которых работают брокеры Kafka. Сито KafkaBolt использует сведения о брокерах при записи в Kafka.

* **KAFKAZKHOSTS**: узлы, на которых работает Zookeeper.

В этом документе показано, как задать эти переменные среды.

## <a name="create-a-kafka-topic"></a>Создание раздела Kafka

1. Подключитесь к кластеру Kafka с помощью SSH. Замените `USERNAME` именем пользователя SSH, которое использовалось при создании кластера. Замените `BASENAME` базовым именем, которое использовалось при создании кластера.
   
        ssh USERNAME@kafka-BASENAME-ssh.azurehdinsight.net
   
    При появлении запроса введите пароль, который использовался при создании кластеров.
   
    См. дополнительные сведения об [использовании SSH в HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

2. Во время установления SSH-подключения к кластеру Kafka используйте следующие команды, чтобы задать переменные для имени для входа по протоколу HTTP и имени кластера. Эти значения используются в других шагах в этом разделе.

  ```bash
  ADMIN='admin' #replace with the name of the admin account for the cluster
  PASSWORD='password' #replace with the password for the admin account
  ```

3. Используйте приведенные ниже команды, чтобы установить служебную программу `jq`, получить имя кластера и задать переменную `KAFKAZKHOSTS`.

  ```bash
  sudo apt -y install jq
  CLUSTERNAME=`curl -u $ADMIN:$PASSWORD -G "http://headnodehost:8080/api/v1/clusters" | jq -r '.items[].Clusters.cluster_name'`
  KAFKAZKHOSTS=`curl -u $ADMIN:$PASSWORD -G "http://headnodehost:8080/api/v1/clusters/$CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER" | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")'`
  ```

    Используйте следующую команду, чтобы получить имя кластера.

  ```bash
  echo $CLUSTERNAME
  ```

    После выполнения этой команды вы должны увидеть результат, аналогичный приведенному ниже.

  ```bash
  kafka-myhdi
  ```

    Выполните следующую команду, чтобы проверить правильность настройки `KAFKAZKHOSTS`.

  ```bash
  echo $KAFKAZKHOSTS
  ```

    После выполнения этой команды вы должны увидеть результат, аналогичный приведенному ниже.

  ```bash
  zk0-kafka.eahjefxxp1netdbyklgqj5y1ud.ex.internal.cloudapp.net:2181,zk2-kafka.eahjefxxp1netdbyklgqj5y1ud.ex.internal.cloudapp.net:2181,zk3-kafka.eahjefxxp1netdbyklgqj5y1ud.ex.internal.cloudapp.net:2181
  ```

    Сохраните имя кластера Kafka и данные узла Zookeeper, так как эти значения используются при запуске топологии в кластере Storm.

    > [!NOTE]
    > Предыдущая команда использует адрес __http://headnodehost:8080/__ с прямым подключением к Ambari. Если вам необходимо получить эту информацию извне кластера через Интернет, используйте адрес __https://kafka-<базовое_имя>.azurehdinsight.net__.

4. Создайте раздел в Kafka с помощью следующей команды.

  ```bash
  /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 2 --partitions 8 --topic stormtest --zookeeper $KAFKAZKHOSTS
  ```

    Эта команда подключается к Zookeeper с помощью сведений об узле, хранящихся в `$KAFKAZKHOSTS`, а затем создает раздел Kafka с именем **stormtest**. Чтобы убедиться, что раздел создан, используйте следующую команду, которая выводит список разделов.

  ```bash
  /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --list --zookeeper $KAFKAZKHOSTS
  ```

    В выходных данных этой команды перечислены разделы Kafka, среди которых должен быть и новый раздел **stormtest**.

Оставьте SSH-подключение к кластеру Kafka активным. С его помощью вы сможете убедиться, что топология Storm записывает сообщения в раздел.

## <a name="download-and-compile-the-project"></a>Скачивание и компиляция проекта

1. В среде разработки скачайте проект по адресу [https://github.com/Azure-Samples/hdinsight-storm-java-kafka](https://github.com/Azure-Samples/hdinsight-storm-java-kafka), откройте окно командной строки и перейдите в папку со скачанным проектом.

    Потратьте несколько минут, чтобы просмотреть код и понять, как работает проект.

2. Из каталога **hdinsight-storm-java-kafka** выполните следующую команду, чтобы скомпилировать проект и создать пакет для развертывания.

  ```bash
  mvn clean package
  ```

    Процесс упаковки создаст файл с именем `KafkaTopology-1.0-SNAPSHOT.jar` в каталоге `target`.

3. Воспользуйтесь приведенными ниже командами, чтобы скопировать пакет в кластер Storm в HDInsight. Замените **USERNAME** именем пользователя SSH для кластера. Замените **BASENAME** базовым именем, которое использовалось при создании кластера.

  ```bash
  scp ./target/KafkaTopology-1.0-SNAPSHOT.jar USERNAME@storm-BASENAME-ssh.azurehdinsight.net:KafkaTopology-1.0-SNAPSHOT.jar
  ```

    При появлении запроса введите пароль, который использовался при создании кластеров.

4. С помощью следующей команды скопируйте файл `set-env-variables.sh` в каталог `scripts` проекта в кластере Storm.

  ```bash
  scp ./scripts/set-env-variables.sh USERNAME@storm-BASENAME-ssh.azurehdinsight.net:set-env-variables.sh
  ```

    Этот сценарий используется, чтобы задать переменные среды, используемые топологиями Storm для взаимодействия с кластером Kafka.

## <a name="start-the-writer"></a>Запуск модуля записи

1. Используйте указанную ниже команду для подключения к кластеру Storm с помощью SSH. Замените **USERNAME** именем пользователя SSH, которое использовалось при создании кластера. Замените **BASENAME** базовым именем, которое использовалось при создании кластера.

  ```bash
  ssh USERNAME@storm-BASENAME-ssh.azurehdinsight.net
  ```

    При появлении запроса введите пароль, который использовался при создании кластеров.
   
    См. дополнительные сведения об [использовании SSH в HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

2. Чтобы запустить сценарий `set-env-variables.sh`, выполните следующие команды из SSH-подключения к кластеру Storm:

  ```bash
  chmod +x set-env-variables.sh
  . ./set-env-variables.sh KAFKACLUSTERNAME PASSWORD
  ```

    Замените __KAFKACLUSTERNAME__ именем кластера Kafka. Замените __PASSWORD__ паролем для входа администратора кластера Kafka.

    Сценарий подключается к кластеру Kafka и получает список брокеров Kafka и узлов Zookeeper. Затем данные сохраняются в переменных среды, используемых в топологиях Storm.

    После выполнения сценария вы должны увидеть результат, аналогичный приведенному ниже.

        Checking for jq: install ok installed
        Exporting variables:
        $KAFKATOPIC=stormtest
        $KAFKABROKERS=wn0-storm.4rf4ncirvydube02fuj0gpxp4e.ex.internal.cloudapp.net:9092,wn1-storm.4rf4ncirvydube02fuj0gpxp4e.ex.internal.cloudapp.net:9092
        $KAFKAZKHOSTS=zk1-storm.4rf4ncirvydube02fuj0gpxp4e.ex.internal.cloudapp.net:2181,zk3-storm.4rf4ncirvydube02fuj0gpxp4e.ex.internal.cloudapp.net:2181,zk5-storm.4rf4ncirvydube02fuj0gpxp4e.ex.internal.cloudapp.net:2181

3. Чтобы запустить топологию модуля записи, выполните следующие команды из SSH-подключения к кластеру Storm.

        storm jar KafkaTopology-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --remote -R /writer.yaml -e

    Параметры, используемые в этой команде:

    * `org.apache.storm.flux.Flux`. Для настройки и запуска этой топологии следует использовать Flux.

    * `--remote`. Отправьте топологию в Nimbus. Топология распределяется между рабочими узлами в кластере.

    * `-R /writer.yaml`. Используйте файл `writer.yaml` для настройки топологии. `-R` указывает, что ресурс включен в JAR-файл. Он включается в корень JAR-файла, поэтому путь к нему выглядит так: `/writer.yaml`.

    * `-e`. Используйте подстановку переменных среды. Flux выбирает значения $KAFKABROKERS и $KAFKATOPIC, заданные ранее, и использует их в файле reader.yaml вместо записей `${ENV-KAFKABROKER}` и `${ENV-KAFKATOPIC}`.

5. После запуска топологии перейдите к SSH-подключению к кластеру Kafka и используйте следующую команду для просмотра сообщений в разделе **stormtest**.

  ```bash
  /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --zookeeper $KAFKAZKHOSTS --from-beginning --topic stormtest
  ```

    Эта команда использует сценарий, входящий в состав Kafka, для мониторинга раздела. Через некоторое время она начнет возвращать случайные предложения, которые были записаны в раздел. Вы должны увидеть результат, аналогичный приведенному ниже.

        i am at two with nature             
        an apple a day keeps the doctor away
        snow white and the seven dwarfs     
        the cow jumped over the moon        
        an apple a day keeps the doctor away
        an apple a day keeps the doctor away
        the cow jumped over the moon        
        an apple a day keeps the doctor away
        an apple a day keeps the doctor away
        four score and seven years ago      
        snow white and the seven dwarfs     
        snow white and the seven dwarfs     
        i am at two with nature             
        an apple a day keeps the doctor away

    Для остановки сценария нажмите сочетание клавиш CTRL+C.

## <a name="start-the-reader"></a>Запуск модуля чтения

1. Чтобы запустить топологию модуля чтения, выполните следующие команды из SSH-подключения к кластеру Storm.

  ```bash
  storm jar KafkaTopology-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --remote -R /reader.yaml -e
  ```

2. После запуска топологии откройте пользовательский интерфейс Storm. Этот веб-интерфейс расположен по адресу https://storm-BASENAME.azurehdinsight.net/stormui. Замените __BASENAME__ базовым именем, которое использовалось при создании кластера. 

    При появлении запроса введите имя администратора для входа (по умолчанию `admin`) и пароль, который использовался при создании кластера. Появится примерно такая веб-страница:

    ![Пользовательский интерфейс Storm](./media/hdinsight-apache-storm-with-kafka/stormui.png)

3. В пользовательском интерфейсе Storm выберите ссылку __kafka-reader__ в разделе __Topology Summary__ (Сводка топологии), чтобы отобразить сведения о топологии __kafka-reader__.

    ![Раздел Topology Summary в веб-интерфейсе Storm](./media/hdinsight-apache-storm-with-kafka/topology-summary.png)

4. Выберите ссылку __logger-bolt__ в разделе __Bolts (All time)__ (Сита (за все время)), чтобы отобразить сведения об экземплярах компонента logger-bolt.

    ![Ссылка logger-bolt в разделе Bolts](./media/hdinsight-apache-storm-with-kafka/bolts.png)

5. В разделе__Executors__ (Исполнители) выберите ссылку в столбце __Port__ (Порт), чтобы отобразить сведения журнала об этом экземпляре компонента.

    ![Ссылка Executors](./media/hdinsight-apache-storm-with-kafka/executors.png)

    Журнал содержит данные, считанные из раздела Kafka. Данные журнала подобны приведенному ниже тексту.

        2016-11-04 17:47:14.907 c.m.e.LoggerBolt [INFO] Received data: four score and seven years ago
        2016-11-04 17:47:14.907 STDIO [INFO] the cow jumped over the moon
        2016-11-04 17:47:14.908 c.m.e.LoggerBolt [INFO] Received data: the cow jumped over the moon
        2016-11-04 17:47:14.911 STDIO [INFO] snow white and the seven dwarfs
        2016-11-04 17:47:14.911 c.m.e.LoggerBolt [INFO] Received data: snow white and the seven dwarfs
        2016-11-04 17:47:14.932 STDIO [INFO] snow white and the seven dwarfs
        2016-11-04 17:47:14.932 c.m.e.LoggerBolt [INFO] Received data: snow white and the seven dwarfs
        2016-11-04 17:47:14.969 STDIO [INFO] an apple a day keeps the doctor away
        2016-11-04 17:47:14.970 c.m.e.LoggerBolt [INFO] Received data: an apple a day keeps the doctor away

## <a name="stop-the-topologies"></a>Остановка топологий

Чтобы остановить топологии Storm, выполните следующие команды из SSH-подключения к кластеру Storm.

  ```bash
  storm kill kafka-writer
  storm kill kafka-reader
  ```

## <a name="delete-the-cluster"></a>Удаление кластера

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Выполнив описанные здесь инструкции, вы создадите два кластера в одной группе ресурсов Azure. Следовательно, вы можете удалить эту группу ресурсов на портале Azure. При этом будут удалены все ресурсы, созданные по инструкциям в этом документе, виртуальная сеть Azure и учетная запись хранения, которые используются в кластерах.

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные примеры топологий, которые можно использовать со Storm в HDInsight, см. в статье [Примеры топологий и компонентов Storm для Apache Storm в HDInsight](hdinsight-storm-example-topology.md).

Сведения о развертывании и мониторинге топологий в HDInsight под управлением Linux см. в статье [Развертывание топологий Apache Storm в HDInsight под управлением Linux и управление ими](hdinsight-storm-deploy-monitor-topology-linux.md).



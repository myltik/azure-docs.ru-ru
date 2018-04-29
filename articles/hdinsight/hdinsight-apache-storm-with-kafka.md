---
title: Использование Apache Kafka со Storm в HDInsight — Azure | Документы Майкрософт
description: Платформа Apache Kafka устанавливается вместе с Apache Storm в службе HDInsight. Из этой статьи вы узнаете, как записывать и считывать данные в Kafka с помощью компонентов KafkaBolt и KafkaSpout, поставляемых в составе решения Storm. Также вы узнаете, как использовать платформу Flux для определения и отправки топологий Storm.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: e4941329-1580-4cd8-b82e-a2258802c1a7
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: java
ms.topic: conceptual
ms.date: 03/08/2018
ms.author: larryfr
ms.openlocfilehash: be62705ce0217235b75ec5ad220ad6f32dfd3c10
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2018
---
# <a name="use-apache-kafka-with-storm-on-hdinsight"></a>Использование Apache Kafka со Storm в HDInsight

Сведения об использовании Apache Storm для чтения и записи Apache Kafka. В этом примере также показано, как сохранять данные из топологии Storm в HDFS-совместимой файловой системе, используемой HDInsight.

> [!NOTE]
> С помощью описанных здесь шагов будет создана группа ресурсов Azure, которая содержит кластеры Storm в HDInsight и Kafka в HDInsight. Оба этих кластера находятся в виртуальной сети Azure, что позволяет кластеру Storm напрямую обмениваться данными с кластером Kafka.
> 
> Выполнив инструкции, не забудьте удалить кластеры, чтобы избежать ненужных расходов.

## <a name="get-the-code"></a>Получение кода

Код для примера, используемого в этом документе, см. по адресу [https://github.com/Azure-Samples/hdinsight-storm-java-kafka](https://github.com/Azure-Samples/hdinsight-storm-java-kafka).

Чтобы скомпилировать этот проект, требуется следующая конфигурация среды разработки:

* Пакет [Java JDK](http://www.oracle.com/technetwork/pt/java/javase/downloads/jdk8-downloads-2133151.html) 1.8 или более поздней версии. Для HDInsight 3.5 или более поздней версии требуется Java 8.

* [Maven 3.x](https://maven.apache.org/download.cgi)

* SSH-клиент (требуются команды `ssh` и `scp`). Дополнительные сведения см. в разделе [Подключение к HDInsight (Hadoop) с помощью SSH](hdinsight-hadoop-linux-use-ssh-unix.md).

* Текстовый редактор или интегрированная среда разработки.

Во время установки Java и JDK на компьютере, где ведется разработка, могут быть установлены следующие переменные среды. Однако следует убедиться, что они существуют и что они содержат правильные значения для вашей системы.

* `JAVA_HOME`. Эта переменная должна указывать на каталог, в который установлен JDK.
* `PATH`. Эта переменная должна содержать следующие пути:
  
    * `JAVA_HOME` или эквивалентный путь.
    * `JAVA_HOME\bin` или эквивалентный путь.
    * Каталог, в который установлено ПО Maven.

## <a name="create-the-clusters"></a>Создание кластеров

Apache Kafka в HDInsight не предоставляет доступ к брокерам Kafka через общедоступный сегмент Интернета. Все объекты, обращающиеся к Kafka, должны находиться в той же виртуальной сети Azure, что и узлы в кластере Kafka. В этом примере кластеры Storm и Kafka расположены в виртуальной сети Azure. На следующей схеме показано, как взаимодействуют кластеры.

![Схема кластеров Storm и Kafka в виртуальной сети Azure](./media/hdinsight-apache-storm-with-kafka/storm-kafka-vnet.png)

> [!NOTE]
> Другие службы в кластере, например SSH и Ambari, могут быть доступны через Интернет. Дополнительные сведения об общих портах, доступных в HDInsight, см. в статье [Порты и универсальные коды ресурсов (URI), используемые кластерами HDInsight](hdinsight-hadoop-port-settings-for-services.md).

Виртуальную сеть Azure, кластеры Kafka и Storm можно создать вручную, но чтобы упростить задачу, воспользуйтесь шаблоном Azure Resource Manager. Выполните следующие действия, чтобы развернуть виртуальную сеть Azure, а также кластеры Kafka и Storm в подписке Azure.

1. Нажмите эту кнопку, чтобы войти в Azure и открыть шаблон на портале Azure.
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fhdinsight-storm-java-kafka%2Fmaster%2Fcreate-kafka-storm-clusters-in-vnet.json" target="_blank"><img src="./media/hdinsight-apache-storm-with-kafka/deploy-to-azure.png" alt="Deploy to Azure"></a>
   
    Шаблон Azure Resource Manager доступен по адресу **https://github.com/Azure-Samples/hdinsight-storm-java-kafka/blob/master/create-kafka-storm-clusters-in-vnet.json**. Он создает перечисленные ниже ресурсы.
    
    * Группа ресурсов Azure
    * Виртуальная сеть Azure
    * Учетная запись хранения Azure
    * Kafka в HDInsight версии 3.6 (три рабочих узла)
    * Storm в HDInsight версии 3.6 (три рабочих узла)

  > [!WARNING]
  > Чтобы обеспечить доступность Kafka в HDInsight, кластер должен содержать не менее трех рабочих узлов. Этот шаблон создает кластер Kafka, содержащий три рабочих узла.

2. Используйте приведенные ниже инструкции для заполнения раздела **Настраиваемое развертывание**.
   
    ![Настраиваемое развертывание в HDInsight](./media/hdinsight-apache-storm-with-kafka/parameters.png)

    * **Группа ресурсов.** Создайте новую группу ресурсов или выберите существующую. Эта группа содержит кластер HDInsight.
   
    * **Расположение.** Выберите близкое к вам географическое расположение.

    * **Base Cluster Name** (Базовое имя кластера). Это значение будет использоваться в качестве базового имени для кластеров Storm и Kafka. Например, если ввести **hdi**, будет создан кластер Storm **storm-hdi** и кластер Kafka **kafka-hdi**.
   
    * **Cluster Login User Name** (Имя пользователя для входа в кластер). Имя администратора для кластеров Storm и Kafka.
   
    * **Cluster Login User Password** (Пароль пользователя для входа в кластер). Имя администратора для кластеров Storm и Kafka.
    
    * **Имя пользователя SSH.** Создаваемый пользователь SSH для кластеров Storm и Kafka.
    
    * **Пароль SSH.** Пароль пользователя SSH для кластеров Storm и Kafka.

3. Прочтите **условия использования** и установите флажок **Я принимаю указанные выше условия**.

4. Установите флажок **Закрепить на панели мониторинга** и нажмите кнопку **Приобрести**. Процесс создания кластеров занимает около 20 минут.

После создания ресурсов отобразится раздел группы ресурсов.

![Раздел группы ресурсов для виртуальной сети и кластеров](./media/hdinsight-apache-storm-with-kafka/groupblade.png)

> [!IMPORTANT]
> Обратите внимание, что кластерам HDInsight присвоены имена **storm-BASENAME** и **kafka-BASENAME**, где BASENAME — имя, указанное в шаблоне. Эти имена будут использоваться позже при подключении к кластерам.

## <a name="understanding-the-code"></a>Основные сведения о коде

Этот проект содержит две топологии:

* **KafkaWriter.** Эта топология, определяемая файлом **writer.yaml**, записывает случайные предложения в Kafka с помощью сита KafkaBolt, входящего в состав Apache Storm.

    Эта топология генерирует случайные предложения с помощью настраиваемого компонента **SentenceSpout**.

* **KafkaReader.** Эта топология, определяемая файлом **reader.yaml**, считывает данные из Kafka с помощью воронки KafkaSpout, входящей в состав Apache Storm, а затем записывает данные в журнал stdout.

    Эта топология записывает данные в хранилище по умолчанию для кластера Storm с помощью компонента Storm HdfsBolt.
### <a name="flux"></a>Flux

Топологии определяются с помощью [Flux](https://storm.apache.org/releases/1.1.2/flux.html). Решение Flux, которое впервые было представлено в версии Storm 0.10.x, позволяет отделить конфигурацию топологии от кода. Для топологий, которые используют платформу Flux, топология определяется в YAML-файле. YAML-файл можно включить в топологию или указать его как отдельный файл при отправке топологии. Flux также поддерживает подстановку переменных во время выполнения, которая используется в этом примере.

Во время выполнения этих топологий задаются следующие параметры:

* `${kafka.topic}`: имя раздела Kafka, в котором топология выполняет чтение и запись.

* `${kafka.broker.hosts}`: узлы, на которых работают брокеры Kafka. Сито KafkaBolt использует сведения о брокерах при записи в Kafka.

* `${kafka.zookeeper.hosts}`: узлы, на которых работает Zookeeper.

Дополнительные сведения о топологиях Flux см. по адресу [https://storm.apache.org/releases/1.1.2/flux.html](https://storm.apache.org/releases/1.1.2/flux.html).

## <a name="download-and-compile-the-project"></a>Скачивание и компиляция проекта

1. В среде разработки скачайте проект по адресу [https://github.com/Azure-Samples/hdinsight-storm-java-kafka](https://github.com/Azure-Samples/hdinsight-storm-java-kafka), откройте окно командной строки и перейдите в папку со скачанным проектом.

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

## <a name="configure-the-topology"></a>Настройка топологии

1. Используйте один из следующих методов для обнаружения узлов брокера Kafka для **Kafka** в кластере HDInsight.

    ```powershell
    $creds = Get-Credential -UserName "admin" -Message "Enter the HDInsight login"
    $clusterName = Read-Host -Prompt "Enter the Kafka cluster name"
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/KAFKA/components/KAFKA_BROKER" `
        -Credential $creds
    $respObj = ConvertFrom-Json $resp.Content
    $brokerHosts = $respObj.host_components.HostRoles.host_name[0..1]
    ($brokerHosts -join ":9092,") + ":9092"
    ```

    > [!IMPORTANT]
    > В следующем примере Bash предполагается, что `$CLUSTERNAME` содержит имя кластера __Kafka__. Также предполагается, что установлен обработчик [jq](https://stedolan.github.io/jq/) 1.5 или более поздней версии. При появлении запроса введите пароль, чтобы войти на кластер.

    ```bash
    curl -su admin -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER" | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2
    ```

    Возвращаемое значение аналогично приведенному ниже тексту.

        wn0-kafka.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:9092,wn1-kafka.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:9092

    > [!IMPORTANT]
    > Хотя в кластер может содержать более двух узлов брокера, вам необязательно указывать полный список всех узлов на клиентах. Достаточно указать один или два.

2. Используйте один из следующих методов для обнаружения узлов Zookeeper для __Kafka__ в кластере HDInsight.

    ```powershell
    $creds = Get-Credential -UserName "admin" -Message "Enter the HDInsight login"
    $clusterName = Read-Host -Prompt "Enter the Kafka cluster name"
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/ZOOKEEPER/components/ZOOKEEPER_SERVER" `
        -Credential $creds
    $respObj = ConvertFrom-Json $resp.Content
    $zookeeperHosts = $respObj.host_components.HostRoles.host_name[0..1]
    ($zookeeperHosts -join ":2181,") + ":2181"
    ```

    > [!IMPORTANT]
    > В следующем примере Bash предполагается, что `$CLUSTERNAME` содержит имя кластера __Kafka__. Также предполагается, что [jq](https://stedolan.github.io/jq/) установлен. При появлении запроса введите пароль, чтобы войти на кластер.

    ```bash
    curl -su admin -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER" | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2
    ```

    Возвращаемое значение аналогично приведенному ниже тексту.

        zk0-kafka.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:2181,zk2-kafka.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:2181

    > [!IMPORTANT]
    > Хотя может быть более двух узлов Zookeeper, вам необязательно указывать полный список всех узлов на клиентах. Достаточно указать один или два.

    Сохраните это значение, так как оно будет использовано позже.

3. Отредактируйте файл `dev.properties` в корневой папке проекта. Добавьте данные узлов брокера и Zookeeper для кластера __Kafka__ в соответствующие строки в этом файле. В следующем примере настройка выполняется с помощью образцов значений, которые использовались на предыдущих шагах:

        kafka.zookeeper.hosts: zk0-kafka.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:2181,zk2-kafka.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:2181
        kafka.broker.hosts: wn0-kafka.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:9092,wn1-kafka.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:9092
        kafka.topic: stormtopic

4. Сохраните файл `dev.properties` и выполните следующую команду, чтобы передать его на кластер **Storm**.

     ```bash
    scp dev.properties USERNAME@storm-BASENAME-ssh.azurehdinsight.net:dev.properties
    ```

    Замените **USERNAME** именем пользователя SSH для кластера. Замените **BASENAME** базовым именем, которое использовалось при создании кластера.

## <a name="start-the-writer"></a>Запуск модуля записи

> [!IMPORTANT]
> В этом разделе предполагается, что для создания кластеров Storm и Kafka вы использовали ссылку на шаблон Azure Resource Manager из данного документа. Этот шаблон обеспечивает автоматическое создание разделов для кластера Kafka.
>
> По умолчанию Kafka в HDInsight не допускает автоматического создания разделов, поэтому, если вы использовали другой метод создания кластера Kafka, необходимо вручную создать раздел. Сведения о ручном создании разделов см. в документе [Приступая к работе с Apache Kafka в HDInsight](./kafka/apache-kafka-get-started.md).

1. Используйте указанную ниже команду для подключения к кластеру **Storm** с помощью SSH. Замените **USERNAME** именем пользователя SSH, которое использовалось при создании кластера. Замените **BASENAME** базовым именем, которое использовалось при создании кластера.

  ```bash
  ssh USERNAME@storm-BASENAME-ssh.azurehdinsight.net
  ```

    При появлении запроса введите пароль, который использовался при создании кластеров.
   
    См. дополнительные сведения об [использовании SSH в HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

2. Чтобы запустить топологию модуля записи, выполните следующие команды из SSH-подключения к кластеру Storm.

    ```bash
    storm jar KafkaTopology-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --remote -R /writer.yaml --filter dev.properties
    ```

    Параметры, используемые в этой команде:

    * `org.apache.storm.flux.Flux`. Для настройки и запуска этой топологии следует использовать Flux.

    * `--remote`. Отправьте топологию в Nimbus. Топология распределяется между рабочими узлами в кластере.

    * `-R /writer.yaml`. Используйте файл `writer.yaml` для настройки топологии. `-R` указывает, что ресурс включен в JAR-файл. Он включается в корень JAR-файла, поэтому путь к нему выглядит так: `/writer.yaml`.

    * `--filter`: заполните записи в топологии `writer.yaml`, используя значения из файла `dev.properties`. Например, значение записи `kafka.topic` в файле используется для замены записи `${kafka.topic}` в определении топологии.

5. После запуска топологии выполните следующую команду, чтобы убедиться, что она записывает данные в раздел Kafka:

    > [!IMPORTANT]
    > Замените `$KAFKAZKHOSTS` данными узла Zookeeper для кластера __Kafka__.

  ```bash
  /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --zookeeper $KAFKAZKHOSTS --from-beginning --topic stormtopic
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

> [!NOTE]
> При просмотре данных модуля чтения в пользовательском интерфейсе Storm, может отображаться раздел __Topology spouts lag error__ (Ошибка, связанная с задержкой при отображении элементов spout топологии). В нашем примере вы можете пропустить эту ошибку.

1. Чтобы запустить топологию модуля чтения, выполните следующие команды из SSH-подключения к кластеру Storm.

  ```bash
  storm jar KafkaTopology-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --remote -R /reader.yaml --filter dev.properties
  ```

2. После запуска топологии откройте пользовательский интерфейс Storm. Этот пользовательский веб-интерфейс находится по адресу `https://storm-BASENAME.azurehdinsight.net/stormui`. Замените __BASENAME__ базовым именем, которое использовалось при создании кластера. 

    При появлении запроса введите имя администратора для входа (по умолчанию `admin`) и пароль, который использовался при создании кластера. Появится примерно такая веб-страница:

    ![Пользовательский интерфейс Storm](./media/hdinsight-apache-storm-with-kafka/stormui.png)

3. В пользовательском интерфейсе Storm выберите ссылку __kafka-reader__ в разделе __Topology Summary__ (Сводка топологии), чтобы отобразить сведения о топологии __kafka-reader__.

    ![Раздел Topology Summary в веб-интерфейсе Storm](./media/hdinsight-apache-storm-with-kafka/topology-summary.png)

4. Чтобы отобразить сведения об экземплярах компонента logger-bolt, выберите ссылку __logger-bolt__ в разделе __Bolts (All time)__ (Сита [за все время]).

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

Выполнив описанные здесь инструкции, вы создадите два кластера в одной группе ресурсов Azure. Следовательно, вы можете удалить эту группу ресурсов на портале Azure. При этом будут удалены все ресурсы, созданные по инструкциям в этом документе.

## <a name="next-steps"></a>Дополнительная информация

Дополнительные примеры топологий, которые можно использовать со Storm в HDInsight, см. в статье [Примеры топологий и компонентов Storm для Apache Storm в HDInsight](storm/apache-storm-example-topology.md).

Сведения о развертывании и мониторинге топологий в HDInsight под управлением Linux см. в статье [Развертывание топологий Apache Storm в HDInsight под управлением Linux и управление ими](storm/apache-storm-deploy-monitor-topology-linux.md).

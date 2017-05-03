---
title: "Зеркальное отображение Apache Kafka в кластере HDInsight | Документация Майкрософт"
description: "Узнайте, как использовать функцию зеркального отображения Kafka, чтобы сохранить реплику Kafka в кластере HDInsight, создав зеркальную копию разделов во вторичном кластере."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 015d276e-f678-4f2b-9572-75553c56625b
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/13/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 8c4e33a63f39d22c336efd9d77def098bd4fa0df
ms.openlocfilehash: c7517f61944b9fdb02a3589d7c9cd83355dae6d8
ms.lasthandoff: 04/20/2017

---
# <a name="use-mirrormaker-to-create-a-replica-of-a-kafka-on-hdinsight-cluster-preview"></a>Создание реплики Kafka в кластере HDInsight с помощью MirrorMaker (предварительная версия)

Apache Kafka включает функцию зеркального отображения, которая позволяет реплицировать разделы из одного кластера Kafka в другой. Например, можно реплицировать записи между кластерами Kafka в разных регионах Azure.

Зеркальное отображение можно запустить как непрерывный процесс или периодически использовать для переноса данных из одного кластера в другой.

> [!WARNING]
> Зеркальное отображение нельзя рассматривать как средство обеспечения отказоустойчивости. Из-за разного смещения элементов в разделах исходного и целевого кластерах они не могут использоваться как взаимозаменяемые.
> 
> Если вас интересует отказоустойчивость, настройте репликацию для разделов в пределах кластера. Дополнительные сведения см. в статье по [началу работы с Kafka в HDInsight](hdinsight-apache-kafka-get-started.md).

## <a name="prerequisites"></a>Предварительные требования

* Виртуальные сети Azure. Исходный и целевой кластеры Kafka должны обмениваться данными напрямую. HDInsight не предоставляет API-интерфейсы Kafka в общедоступном сегменте Интернета, поэтому исходный и целевой кластеры должны находиться в одной виртуальной сети Azure.

* Два кластера Kafka. В этом документе используется шаблон Azure Resource Manager для создания в виртуальной сети Azure двух кластеров Kafka с помощью HDInsight.

## <a name="how-does-mirroring-work"></a>Как работает зеркальное отображение?

Зеркальное отображение выполняется с помощью средства MirrorMaker (часть Apache Kafka). Это средство использует записи из разделов в исходном кластере, а затем создает локальную копию в целевом. MirrorMaker использует один (или несколько) *получателей*, которые считывают исходный кластер, и *производитель*, который записывает данные в локальный (целевой) кластер.

На схеме ниже показан процесс зеркального отображения.

![Схема процесса зеркального отображения](./media/hdinsight-apache-kafka-mirroring/kafka-mirroring.png)

Исходный и целевой кластеры могут содержать разное количество узлов и разделов. Смещение в разделах также может отличаться. Зеркальное отображение сохраняет значение ключа, который используется для секционирования, поэтому порядок записей сохраняется вместе с этими значениями.

### <a name="mirroring-between-networks"></a>Зеркальное отображение между сетями

Чтобы реализовать зеркальное отображение между кластерами Kafka в разных сетях, следует учитывать следующее.

* **Шлюзы** — сети должны взаимодействовать на уровне TCPIP.

* **Разрешение имен** — кластеры Kafka в каждой сети должны подключаться друг к другу с использованием имен узлов. Возможно, потребуется установить в каждой сети сервер службы доменных имен (DNS), который настроен для пересылки запросов в другие сети. 
  
    При создании виртуальной сети Azure вместо использования автоматического DNS, предоставленного сетью, необходимо указать пользовательские DNS-сервер и IP-адрес для сервера. Когда виртуальная сеть будет готова, создайте виртуальную машину Azure, использующую этот IP-адрес, а затем установите и настройте на ней программное обеспечение DNS.
  
    > [!WARNING]
    > Создайте и настройте пользовательский DNS-сервер, прежде чем устанавливать HDInsight в виртуальной сети. HDInsight не нужно дополнительно настраивать для использования DNS-сервера, настроенного для виртуальной сети.

Дополнительные сведения о подключении двух виртуальных сетей Azure см. в статье [Настройка подключения между виртуальными сетями в развертывании Resource Manager с помощью PowerShell](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md).

## <a name="create-kafka-clusters"></a>Создание кластеров Kafka

Apache Kafka в HDInsight не предоставляет доступ к службе Kafka через общедоступный сегмент Интернета. Все объекты, обращающиеся к Kafka, должны находиться в той же виртуальной сети Azure, что и узлы в кластере Kafka. В этом примере исходный и целевой кластеры Kafka расположены в виртуальной сети Azure. На следующей схеме показано, как взаимодействуют кластеры.

![Схема исходного и целевого кластеров Kafka в виртуальной сети Azure](./media/hdinsight-apache-kafka-mirroring/spark-kafka-vnet.png)

> [!NOTE]
> Хотя само решение Kafka ограничено связью в пределах виртуальной сети, другие службы в кластере, например SSH и Ambari, доступны через Интернет. Дополнительные сведения об общих портах, доступных в HDInsight, см. в статье [Порты и универсальные коды ресурсов (URI), используемые кластерами HDInsight](hdinsight-hadoop-port-settings-for-services.md).

Виртуальную сеть Azure и кластеры Kafka можно создать вручную, но лучше использовать шаблон Azure Resource Manager. Выполните следующие действия, чтобы развернуть виртуальную сеть Azure и два кластера Kafka в подписке Azure.

1. Нажмите эту кнопку, чтобы войти в Azure и открыть шаблон на портале Azure.
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-kafka-mirror-cluster-in-vnet.json" target="_blank"><img src="./media/hdinsight-apache-kafka-mirroring/deploy-to-azure.png" alt="Deploy to Azure"></a>
   
    Шаблон Azure Resource Manager находится по адресу **https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-kafka-mirror-cluster-in-vnet.json**.

2. Используйте следующие сведения, чтобы заполнить колонку **Настраиваемое развертывание**.
    
    ![Настраиваемое развертывание в HDInsight](./media/hdinsight-apache-kafka-mirroring/parameters.png)
    
    * **Группа ресурсов.** Создайте новую группу ресурсов или выберите существующую. Эта группа содержит кластер HDInsight.

    * **Расположение.** Выберите близкое к вам географическое расположение. Оно должно соответствовать расположению в разделе __Параметры__.
     
    * **Базовое имя кластера.** Это значение будет использоваться в качестве базового имени для кластеров Kafka. Например, введя значение **hdi**, вы создадите кластеры с именами **source-hdi** и **dest-hdi**.

    * **Имя пользователя для входа в кластер.** Имя администратора исходного и целевого кластеров Kafka.

    * **Пароль для входа в кластер.** Пароль администратора исходного и целевого кластеров Kafka.

    * **Имя пользователя SSH.** Создаваемый пользователь SSH для исходного и целевого кластеров Kafka.

    * **Пароль SSH.** Пароль пользователя SSH для исходного и целевого кластеров Kafka.

    * **Расположение.** Регион, в котором создаются кластеры.

3. Прочтите **условия использования** и установите флажок **Я принимаю указанные выше условия**.

4. Установите флажок **Закрепить на панели мониторинга** и нажмите кнопку **Приобрести**. Процесс создания кластеров занимает около 20 минут.

Когда указанные ресурсы будут созданы, отобразится колонка группы ресурсов, которая содержит кластеры и веб-панель мониторинга.

![Колонка группы ресурсов для виртуальной сети и кластеров](./media/hdinsight-apache-kafka-mirroring/groupblade.png)

> [!IMPORTANT]
> Обратите внимание, что кластерам HDInsight присвоены имена **source-BASENAME** и **dest-BASENAME**, где BASENAME — имя, указанное в шаблоне. Эти имена будут использоваться позже при подключении к кластерам.

## <a name="create-topics"></a>Создание разделов

1. Подключитесь к **исходному** кластеру с помощью SSH.
   
        ssh sshuser@source-BASENAME-ssh.azurehdinsight.net
   
    Замените **sshuser** именем пользователя SSH, которое использовалось при создании кластера. Замените **BASENAME** базовым именем, которое использовалось при создании кластера.
   
    См. дополнительные сведения об [использовании SSH в HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

2. Выполните следующую команду, чтобы найти узлы Zookeeper, задать переменную `SOURCE_ZKHOSTS`, а затем создать несколько разделов с именем `testtopic`:
   
    ```bash
    SOURCE_ZKHOSTS=`grep -R zk /etc/hadoop/conf/yarn-site.xml | grep 2181 | grep -oPm1 "(?<=<value>)[^<]+"`
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 2 --partitions 8 --topic testtopic --zookeeper $SOURCE_ZKHOSTS
    ```

3. С помощью этой команды проверьте, создан ли раздел:
   
    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --list --zookeeper $SOURCE_ZKHOSTS
    ```
   
    Ответ содержит `testtopic`.

4. Чтобы просмотреть сведения об узле Zookeeper для этого (**исходного**) кластера, выполните следующую команду:
   
    ```bash
    echo $SOURCE_ZKHOSTS
    ```
   
 Эта команда возвращает следующую информацию:
   
       zk0-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:2181,zk1-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:2181,zk6-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:2181
   
 Сохраните эти сведения. Они будут использоваться в следующем разделе.

## <a name="configure-mirroring"></a>Настройка зеркального отображения

1. Подключитесь к **целевому** кластеру с помощью другого сеанса SSH:
   
        ssh sshuser@dest-BASENAME-ssh.azurehdinsight.net
   
    Замените **sshuser** именем пользователя SSH, которое использовалось при создании кластера. Замените **BASENAME** базовым именем, которое использовалось при создании кластера.
   
    См. дополнительные сведения об [использовании SSH в HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

2. Выполните следующую команду, чтобы создать файл `consumer.properties`, который настраивает обмен данными с **исходным** кластером:
   
    ```bash
    nano consumer.properties
    ```
   
    Добавьте в файл `consumer.properties` следующее содержимое:
   
        zookeeper.connect=SOURCE_ZKHOSTS
        group.id=mirrorgroup
   
    Замените **SOURCE_ZKHOSTS** сведениями об узлах Zookeeper для **исходного** кластера.
   
    Этот файл включает сведения о получателе, которые используются при считывании данных из исходного кластера Kafka. Дополнительные сведения о конфигурации получателя см. в [этом разделе](https://kafka.apache.org/documentation#consumerconfigs) на сайте kafka.apache.org.
   
    Нажмите клавиши **Ctrl+X**, затем — **Y** и ВВОД, чтобы сохранить файл.

3. Прежде чем настраивать производитель, который обменивается данными с целевым кластером, найдите узлы брокера для **целевого** кластера. Чтобы получить эти сведения, выполните следующие команды:
   
    ```bash
    sudo apt -y install jq
    DEST_BROKERHOSTS=`sudo bash -c 'ls /var/lib/ambari-agent/data/command-[0-9]*.json' | tail -n 1 | xargs sudo cat | jq -r '["\(.clusterHostInfo.kafka_broker_hosts[]):9092"] | join(",")'`
    echo $DEST_BROKERHOSTS
    ```
   
    Эта команда возвращает следующую информацию:
   
        wn0-dest.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092,wn1-dest.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092

4. Выполните следующую команду, чтобы создать файл `producer.properties`, который настраивает обмен данными с **целевым** кластером:

    ```bash
    nano producer.properties
    ```

    Добавьте в файл `producer.properties` следующее содержимое:
   
        bootstrap.servers=DEST_BROKERS
        compression.type=none
   
    Замените **DEST_BROKERS** сведениями о брокере из предыдущего шага. 
   
    Дополнительные сведения о конфигурации производителя см. в [этом разделе](https://kafka.apache.org/documentation#producerconfigs) на сайте kafka.apache.org.

## <a name="start-mirrormaker"></a>Запуск MirrorMaker

1. Чтобы запустить процесс MirrorMaker, выполните следующие команды в рамках SSH-подключения к **целевому** кластеру:
   
    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-run-class.sh kafka.tools.MirrorMaker --consumer.config consumer.properties --producer.config producer.properties --whitelist testtopic --num.streams 4
    ```
   
    В этом примере используются следующие параметры.
   
    * **--consumer.config** — указывает файл, который содержит свойства получателя. Эти свойства используются для создания получателя, который считывает данные из *исходного* кластера Kafka.

    * **--producer.config** — указывает файл, который содержит свойства производителя. Эти свойства используются для создания производителя, который записывает данные в *целевой* кластер Kafka.

    * **--whitelist** — список разделов, которые MirrorMaker реплицирует из исходного кластера в целевой.
    
    * **--num.streams** — число создаваемых потоков получателя.
     
    При запуске MirrorMaker возвращает следующую информацию:
        
    ```
    {metadata.broker.list=wn1-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092,wn0-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092, request.timeout.ms=30000, client.id=mirror-group-3, security.protocol=PLAINTEXT}{metadata.broker.list=wn1-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092,wn0-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092, request.timeout.ms=30000, client.id=mirror-group-0, security.protocol=PLAINTEXT}
    metadata.broker.list=wn1-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092,wn0-kafka.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092, request.timeout.ms=30000, client.id=mirror-group-2, security.protocol=PLAINTEXT}
    metadata.broker.list=wn1-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092,wn0-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092, request.timeout.ms=30000, client.id=mirror-group-1, security.protocol=PLAINTEXT}
    ```

2. Чтобы запустить производитель и отправить сообщения в раздел, выполните следующую команду из SSH-подключения к **исходному** кластеру:
    
    ```bash
    sudo apt -y install jq
    SOURCE_BROKERHOSTS=`sudo bash -c 'ls /var/lib/ambari-agent/data/command-[0-9]*.json' | tail -n 1 | xargs sudo cat | jq -r '["\(.clusterHostInfo.kafka_broker_hosts[]):9092"] | join(",")'`
    /usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list $SOURCE_BROKERHOSTS --topic testtopic
    ```

 При переходе в пустую строку с курсором введите несколько текстовых сообщений. Они отправляются в раздел в **исходном** кластере. По завершении нажмите клавиши **Ctrl+C**, чтобы завершить процесс производителя.

3. Чтобы завершить процесс MirrorMaker, нажмите клавиши **Ctrl+C** в рамках SSH-подключения к **целевому** кластеру. Затем используйте следующие команды, чтобы убедиться, что раздел `testtopic` создан и данные в нем реплицированы в это зеркало:
    
    ```bash
    DEST_ZKHOSTS=`grep -R zk /etc/hadoop/conf/yarn-site.xml | grep 2181 | grep -oPm1 "(?<=<value>)[^<]+"`
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --list --zookeeper $DEST_ZKHOSTS
    /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --zookeeper $DEST_ZKHOSTS --topic testtopic --from-beginning
    ```
    
  Теперь в списке разделов есть раздел `testtopic`, который создается, когда MirrorMaster зеркально отражает раздел из исходного кластера в место назначения. Сообщения, полученные из раздела, совпадают с введенными в исходном кластере.

## <a name="delete-the-cluster"></a>Удаление кластера

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Выполнив описанные здесь инструкции, вы создадите два кластера в одной группе ресурсов Azure. Следовательно, вы можете удалить эту группу ресурсов на портале Azure. При этом будут удалены все созданные в рамках этого руководства и используемые в кластерах ресурсы, виртуальная сеть Azure и учетная запись хранения.

## <a name="next-steps"></a>Дальнейшие действия

В этом документе вы узнали, как создать реплику кластер Kafka с помощью MirrorMaker. Другие материалы, посвященные работе с Kafka, доступны по следующим ссылкам:

* [Документация по Apache Kafka MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330) на сайте cwiki.apache.org.
* [Get started with Apache Kafka on HDInsight (preview)](hdinsight-apache-kafka-get-started.md) (Приступая к работе с Apache Kafka в HDInsight (предварительная версия))
* [Совместное использование Apache Spark и Kafka (предварительная версия) в HDInsight](hdinsight-apache-spark-with-kafka.md)
* [Совместное использование Apache Kafka (предварительная версия) и Storm в HDInsight](hdinsight-apache-storm-with-kafka.md)
* [Подключение к Kafka через виртуальную сеть Azure](hdinsight-apache-kafka-connect-vpn-gateway.md)


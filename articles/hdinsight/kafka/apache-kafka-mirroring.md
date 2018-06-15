---
title: Зеркальное отображение разделов Apache Kafka в Azure HDInsight | Документация Майкрософт
description: Узнайте, как использовать функцию зеркального отображения Apache Kafka, чтобы сохранить реплику Kafka в кластере HDInsight, создав зеркальную копию разделов во вторичном кластере.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 015d276e-f678-4f2b-9572-75553c56625b
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 05/01/2018
ms.author: larryfr
ms.openlocfilehash: 9fbf4364e22c0b25d224ee0961f7e7ee13ddcef8
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/03/2018
ms.locfileid: "32778875"
---
# <a name="use-mirrormaker-to-replicate-apache-kafka-topics-with-kafka-on-hdinsight"></a>Репликация разделов Apache Kafka с помощью Kafka в HDInsight и MirrorMaker

Узнайте, как реплицировать разделы во вторичный кластер с помощью функции зеркального отображения Apache Kafka. Зеркальное отображение можно запустить как непрерывный процесс или периодически использовать для переноса данных из одного кластера в другой.

В этом примере зеркальное отображение используется для репликации разделов между двумя кластерами HDInsight. Оба кластера расположены в одной виртуальной сети Azure в том же регионе.

> [!WARNING]
> Зеркальное отображение нельзя рассматривать как средство обеспечения отказоустойчивости. Из-за разного смещения элементов в разделах исходного и целевого кластерах они не могут использоваться как взаимозаменяемые.
>
> Если вас интересует отказоустойчивость, настройте репликацию для разделов в пределах кластера. Дополнительные сведения см. в статье по [началу работы с Kafka в HDInsight](apache-kafka-get-started.md).

## <a name="how-kafka-mirroring-works"></a>Как работает зеркальное отображение Kafka

Зеркальное отображение выполняется с помощью средства MirrorMaker (часть Apache Kafka). Это средство использует записи из разделов в исходном кластере, а затем создает локальную копию в целевом. MirrorMaker использует один (или несколько) *получателей*, которые считывают исходный кластер, и *производитель*, который записывает данные в локальный (целевой) кластер.

На схеме ниже показан процесс зеркального отображения.

![Схема процесса зеркального отображения](./media/apache-kafka-mirroring/kafka-mirroring.png)

Apache Kafka в HDInsight не предоставляет доступ к службе Kafka через общедоступный сегмент Интернета. Производители или потребители Kafka должны находиться в той же виртуальной сети Azure, что и узлы в кластере Kafka. В этом примере исходный и целевой кластеры Kafka расположены в виртуальной сети Azure. На следующей схеме показано, как взаимодействуют кластеры.

![Схема исходного и целевого кластеров Kafka в виртуальной сети Azure](./media/apache-kafka-mirroring/spark-kafka-vnet.png)

Исходный и целевой кластеры могут содержать разное количество узлов и разделов. Смещение в разделах также может отличаться. Зеркальное отображение сохраняет значение ключа, который используется для секционирования, поэтому порядок записей сохраняется вместе с этими значениями.

### <a name="mirroring-across-network-boundaries"></a>Зеркальное отображение в пределах сети

Чтобы реализовать зеркальное отображение между кластерами Kafka в разных сетях, следует учитывать следующее.

* **Шлюзы** — сети должны взаимодействовать на уровне TCPIP.

* **Разрешение имен** — кластеры Kafka в каждой сети должны подключаться друг к другу с использованием имен узлов. Возможно, потребуется установить в каждой сети сервер службы доменных имен (DNS), который настроен для пересылки запросов в другие сети.

    При создании виртуальной сети Azure вместо использования автоматического DNS, предоставленного сетью, необходимо указать пользовательские DNS-сервер и IP-адрес для сервера. Когда виртуальная сеть будет готова, создайте виртуальную машину Azure, использующую этот IP-адрес, а затем установите и настройте на ней программное обеспечение DNS.

    > [!WARNING]
    > Создайте и настройте пользовательский DNS-сервер, прежде чем устанавливать HDInsight в виртуальной сети. HDInsight не нужно дополнительно настраивать для использования DNS-сервера, настроенного для виртуальной сети.

Дополнительные сведения о подключении двух виртуальных сетей Azure см. в статье [Настройка подключения между виртуальными сетями в развертывании Resource Manager с помощью PowerShell](../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md).

## <a name="create-kafka-clusters"></a>Создание кластеров Kafka

Виртуальную сеть Azure и кластеры Kafka можно создать вручную, но лучше использовать шаблон Azure Resource Manager. Выполните следующие действия, чтобы развернуть виртуальную сеть Azure и два кластера Kafka в подписке Azure.

1. Нажмите эту кнопку, чтобы войти в Azure и открыть шаблон на портале Azure.
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-kafka-mirror-cluster-in-vnet-v2.1.json" target="_blank"><img src="./media/apache-kafka-mirroring/deploy-to-azure.png" alt="Deploy to Azure"></a>
   
    Шаблон Azure Resource Manager доступен по адресу **https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-kafka-mirror-cluster-in-vnet-v2.1.json**.

    > [!WARNING]
    > Чтобы обеспечить доступность Kafka в HDInsight, кластер должен содержать не менее трех рабочих узлов. Этот шаблон создает кластер Kafka, содержащий три рабочих узла.

2. Используйте следующие сведения, чтобы заполнить колонку **Настраиваемое развертывание**.
    
    ![Настраиваемое развертывание в HDInsight](./media/apache-kafka-mirroring/parameters.png)
    
    * **Группа ресурсов.** Создайте новую группу ресурсов или выберите существующую. Эта группа содержит кластер HDInsight.

    * **Расположение.** Выберите близкое к вам географическое расположение.
     
    * **Базовое имя кластера.** Это значение будет использоваться в качестве базового имени для кластеров Kafka. Например, введя значение **hdi**, вы создадите кластеры с именами **source-hdi** и **dest-hdi**.

    * **Имя пользователя для входа в кластер.** Имя администратора исходного и целевого кластеров Kafka.

    * **Пароль для входа в кластер.** Пароль администратора исходного и целевого кластеров Kafka.

    * **Имя пользователя SSH.** Создаваемый пользователь SSH для исходного и целевого кластеров Kafka.

    * **Пароль SSH.** Пароль пользователя SSH для исходного и целевого кластеров Kafka.

3. Прочтите **условия использования** и установите флажок **Я принимаю указанные выше условия**.

4. Установите флажок **Закрепить на панели мониторинга** и нажмите кнопку **Приобрести**. Процесс создания кластеров занимает около 20 минут.

> [!IMPORTANT]
> Кластерам HDInsight присвоены имена **source-BASENAME** и **dest-BASENAME**, где BASENAME — имя, указанное в шаблоне. Эти имена будут использоваться позже при подключении к кластерам.

## <a name="create-topics"></a>Создание разделов

1. Подключитесь к **исходному** кластеру с помощью SSH.

    ```bash
    ssh sshuser@source-BASENAME-ssh.azurehdinsight.net
    ```

    Замените **sshuser** именем пользователя SSH, которое использовалось при создании кластера. Замените **BASENAME** базовым именем, которое использовалось при создании кластера.

    См. дополнительные сведения об [использовании SSH в HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. Чтобы найти узлы Zookeeper для исходного кластера, используйте следующие команды:

    ```bash
    # Install jq if it is not installed
    sudo apt -y install jq
    # get the zookeeper hosts for the source cluster
    export SOURCE_ZKHOSTS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2`
    ```

    Замените `$CLUSTERNAME` именем исходного кластера. При появлении запроса введите пароль для учетной записи администратора, чтобы войти на кластер.

3. Создайте раздел с именем `testtopic` с помощью следующей команды:

    ```bash
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

    `zk0-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:2181,zk1-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:2181`

    Сохраните эти сведения. Они будут использоваться в следующем разделе.

## <a name="configure-mirroring"></a>Настройка зеркального отображения

1. Подключитесь к **целевому** кластеру с помощью другого сеанса SSH:

    ```bash
    ssh sshuser@dest-BASENAME-ssh.azurehdinsight.net
    ```

    Замените **sshuser** именем пользователя SSH, которое использовалось при создании кластера. Замените **BASENAME** базовым именем, которое использовалось при создании кластера.

    См. дополнительные сведения об [использовании SSH в HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. Файл `consumer.properties` используется для настройки обмена данными с **исходным** кластером. Чтобы создать файл, используйте следующую команду:

    ```bash
    nano consumer.properties
    ```

    Добавьте в файл `consumer.properties` следующее содержимое:

    ```yaml
    zookeeper.connect=SOURCE_ZKHOSTS
    group.id=mirrorgroup
    ```

    Замените **SOURCE_ZKHOSTS** сведениями об узлах Zookeeper для **исходного** кластера.

    Этот файл включает сведения о получателе, которые используются при считывании данных из исходного кластера Kafka. Дополнительные сведения о конфигурации получателя см. в [этом разделе](https://kafka.apache.org/documentation#consumerconfigs) на сайте kafka.apache.org.

    Чтобы сохранить файл, нажмите клавиши **CTRL+X**, затем — **Y** и **ВВОД**.

3. Прежде чем настраивать производитель, который обменивается данными с целевым кластером, найдите узлы брокера для **целевого** кластера. Чтобы получить эти сведения, выполните следующие команды:

    ```bash
    sudo apt -y install jq
    DEST_BROKERHOSTS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`
    echo $DEST_BROKERHOSTS
    ```

    Замените `$CLUSTERNAME` именем целевого кластера. При появлении запроса введите пароль для учетной записи администратора, чтобы войти на кластер.

    Команда `echo` возвращает примерно такие сведения:

        wn0-dest.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092,wn1-dest.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092

4. Файл `producer.properties` используется для обмена данными с __целевым__ кластером. Чтобы создать файл, используйте следующую команду:

    ```bash
    nano producer.properties
    ```

    Добавьте в файл `producer.properties` следующее содержимое:

    ```yaml
    bootstrap.servers=DEST_BROKERS
    compression.type=none
    ```

    Замените **DEST_BROKERS** сведениями о брокере из предыдущего шага.

    Дополнительные сведения о конфигурации производителя см. в [этом разделе](https://kafka.apache.org/documentation#producerconfigs) на сайте kafka.apache.org.

5. Чтобы найти узлы Zookeeper для целевого кластера, используйте следующие команды:

    ```bash
    # Install jq if it is not installed
    sudo apt -y install jq
    # get the zookeeper hosts for the source cluster
    export DEST_ZKHOSTS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2`
    ```

    Замените `$CLUSTERNAME` именем целевого кластера. При появлении запроса введите пароль для учетной записи администратора, чтобы войти на кластер.

7. В конфигурации по умолчанию для Kafka в HDInsight не предусматривается автоматическое создание разделов. Прежде чем начать процесс зеркального отображения, используйте один из следующих параметров:

    * **Create the topics on the destination cluster** (Создание разделов на целевом кластере). Этот параметр также позволяет задать число секций и коэффициент репликации.

        Вы можете создать разделы заранее, выполнив следующую команду:

        ```bash
        /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 2 --partitions 8 --topic testtopic --zookeeper $DEST_ZKHOSTS
        ```

        Замените `testtopic` именем создаваемого раздела.

    * **Configure the cluster for automatic topic creation** (Настройка кластера для автоматического создания разделов). Этот параметр позволяет средству MirrorMaker автоматически создавать разделы. Но это средство может создать разделы с не таким числом секций и коэффициентом репликации, как в исходном разделе.

        Чтобы настроить автоматическое создание разделов в целевом кластере, выполните следующие действия:

        1. На [портале Azure](https://portal.azure.com) выберите целевой кластер Kafka.
        2. На странице сведений о кластере выберите __Панель мониторинга кластера__. Затем выберите __Панель мониторинга кластера HDInsight__. Когда появится запрос, пройдите проверку подлинности, используя учетные данные (администратора) входа для кластера.
        3. В списке в левой части страницы выберите службу __Kafka__.
        4. Выберите __Configs__ (Конфигурации) в середине страницы.
        5. В поле __Фильтр__ введите значение параметра `auto.create`. Будет отфильтрован список свойств и отобразится параметр `auto.create.topics.enable`.
        6. Измените значение параметра `auto.create.topics.enable` на true и выберите __Сохранить__. Добавьте заметку и выберите __Сохранить__ еще раз.
        7. Выберите службу __Kafka__ и щелкните __Перезапустить__, а затем выберите __Restart all affected__ (Перезапустить все затронутые). Когда появится запрос, выберите __Conform Restart All__ (Подтвердить перезапуск всех).

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

    ```json
    {metadata.broker.list=wn1-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092,wn0-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092, request.timeout.ms=30000, client.id=mirror-group-3, security.protocol=PLAINTEXT}{metadata.broker.list=wn1-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092,wn0-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092, request.timeout.ms=30000, client.id=mirror-group-0, security.protocol=PLAINTEXT}
    metadata.broker.list=wn1-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092,wn0-kafka.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092, request.timeout.ms=30000, client.id=mirror-group-2, security.protocol=PLAINTEXT}
    metadata.broker.list=wn1-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092,wn0-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092, request.timeout.ms=30000, client.id=mirror-group-1, security.protocol=PLAINTEXT}
    ```

2. Чтобы запустить производитель и отправить сообщения в раздел, выполните следующую команду из SSH-подключения к **исходному** кластеру:

    ```bash
    SOURCE_BROKERHOSTS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`
    /usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list $SOURCE_BROKERHOSTS --topic testtopic
    ```

    Замените `$CLUSTERNAME` именем исходного кластера. При появлении запроса введите пароль для учетной записи администратора, чтобы войти на кластер.

     При переходе в пустую строку с курсором введите несколько текстовых сообщений. Сообщения отправляются в раздел в **исходном** кластере. По завершении нажмите клавиши **Ctrl+C**, чтобы завершить процесс производителя.

3. Чтобы завершить процесс MirrorMaker, нажмите клавиши **Ctrl+C** в рамках SSH-подключения к **целевому** кластеру. Завершение этого процесса может занять несколько секунд. Чтобы проверить состояние репликации сообщений в целевое назначение, используйте следующую команду:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --zookeeper $DEST_ZKHOSTS --topic testtopic --from-beginning
    ```

    Замените `$CLUSTERNAME` именем целевого кластера. При появлении запроса введите пароль для учетной записи администратора, чтобы войти на кластер.

    Теперь в списке разделов есть раздел `testtopic`, который создается, когда MirrorMaster зеркально отражает раздел из исходного кластера в место назначения. Сообщения, полученные из раздела, совпадают с введенными в исходном кластере.

## <a name="delete-the-cluster"></a>Удаление кластера

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

Выполнив описанные здесь инструкции, вы создадите два кластера в одной группе ресурсов Azure. Следовательно, вы можете удалить эту группу ресурсов на портале Azure. При этом будут удалены все созданные в рамках этого руководства и используемые в кластерах ресурсы, виртуальная сеть Azure и учетная запись хранения.

## <a name="next-steps"></a>Дальнейшие действия

В этом документе вы узнали, как создать реплику кластер Kafka с помощью MirrorMaker. Другие материалы, посвященные работе с Kafka, доступны по следующим ссылкам:

* [Документация по Apache Kafka MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330) на сайте cwiki.apache.org.
* [Get started with Apache Kafka on HDInsight (preview)](apache-kafka-get-started.md) (Приступая к работе с Apache Kafka в HDInsight (предварительная версия))
* [Совместное использование Apache Spark и Kafka (предварительная версия) в HDInsight](../hdinsight-apache-spark-with-kafka.md)
* [Совместное использование Apache Kafka (предварительная версия) и Storm в HDInsight](../hdinsight-apache-storm-with-kafka.md)
* [Подключение к Kafka через виртуальную сеть Azure](apache-kafka-connect-vpn-gateway.md)

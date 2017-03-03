---
title: "Совместное использование Apache Spark и Kafka в Azure HDInsight | Документация Майкрософт"
description: "Узнайте, как использовать Spark в HDInsight для чтения и записи данных в Kafka в кластере HDInsight. В этом примере используется Scala в блокноте Jupyter для записи случайных данных в Kafka в кластере HDInsight, которые затем считываются с помощью потоковой передачи Spark."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: dd8f53c1-bdee-4921-b683-3be4c46c2039
ms.service: hdinsight
ms.devlang: 
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/13/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 50a9c3929a4d3194c3786a3d4f6cdd1b73fb5867
ms.openlocfilehash: 0cb9d65e33bf6f6b67e2a74b7e4634aba3f2359b
ms.lasthandoff: 02/14/2017

---
# <a name="use-apache-spark-with-kafka-preview-on-hdinsight"></a>Использование Apache Spark и Kafka (предварительная версия) в HDInsight

Apache Spark можно использовать для двунаправленного потокового обмена данными с Apache Kafka. В этом документе описано, как выполнять потоковый обмен данными с Kafka, используя блокнот Jupyter из Spark в HDInsight.

> [!NOTE]
> Вы узнаете, как создать группу ресурсов Azure, которая содержит кластеры Spark и Kafka в HDInsight. Оба этих кластера находятся в виртуальной сети Azure, что позволяет кластеру Spark напрямую обмениваться данными с кластером Kafka.
> 
> Выполнив инструкции, не забудьте удалить кластеры, чтобы избежать ненужных расходов.

## <a name="prerequisites"></a>Предварительные требования

* Подписка Azure

* Клиент SSH (вам потребуются команды `ssh` и `scp`). Дополнительные сведения об использовании SSH с HDInsight см. в следующих статьях.

    * [Использование SSH с Hadoop на основе Linux в HDInsight из Linux, Unix или OS X.](hdinsight-hadoop-linux-use-ssh-unix.md)

    * [Использование SSH с HDInsight под управлением Linux в Windows.](hdinsight-hadoop-linux-use-ssh-windows.md)

* [cURL](https://curl.haxx.se/) — кроссплатформенная служебная программа, используемая для создания HTTP-запросов.

* [jq](https://stedolan.github.io/jq/) — кроссплатформенная служебная программа, используемая для синтаксического анализа JSON-документов.

## <a name="create-the-clusters"></a>Создание кластеров

Apache Kafka в HDInsight не предоставляет доступ к брокерам Kafka через общедоступный сегмент Интернета. Все объекты, обращающиеся к Kafka, должны находиться в той же виртуальной сети Azure, что и узлы в кластере Kafka. В этом примере кластеры Kafka и Spark расположены в виртуальной сети Azure. На следующей схеме показано, как взаимодействуют кластеры.

![Схема кластеров Spark и Kafka в виртуальной сети Azure](./media/hdinsight-apache-spark-with-kafka/spark-kafka-vnet.png)

> [!NOTE]
> Хотя само решение Kafka ограничено связью в пределах виртуальной сети, другие службы в кластере, например SSH и Ambari, доступны через Интернет. Дополнительные сведения об общих портах, доступных в HDInsight, см. в статье [Порты и универсальные коды ресурсов (URI), используемые кластерами HDInsight](hdinsight-hadoop-port-settings-for-services.md).

Хотя виртуальную сеть Azure, а также кластеры Kafka и Spark можно создать вручную, проще использовать шаблон Azure Resource Manager. Выполните следующие действия, чтобы развернуть виртуальную сеть Azure, а также кластеры Kafka и Spark в подписке Azure.

1. Нажмите эту кнопку, чтобы войти в Azure и открыть шаблон на портале Azure.
    
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-kafka-spark-cluster-in-vnet.json" target="_blank"><img src="./media/hdinsight-apache-spark-with-kafka/deploy-to-azure.png" alt="Deploy to Azure"></a>
    
    Шаблон Azure Resource Manager находится по адресу **https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-kafka-spark-cluster-in-vnet.json**.

2. Используйте следующие сведения, чтобы заполнить колонку **Настраиваемое развертывание**.
   
    ![Настраиваемое развертывание в HDInsight](./media/hdinsight-apache-spark-with-kafka/parameters.png)
   
    * **Группа ресурсов.** Создайте новую группу ресурсов или выберите существующую. Эта группа содержит кластер HDInsight.

    * **Расположение.** Выберите близкое к вам географическое расположение. Оно должно соответствовать расположению в разделе __Параметры__.

    * **Базовое имя кластера**. Это значение будет использоваться в качестве базового имени для кластеров Spark и Kafka. Например, если ввести **hdi**, будет создан кластер Spark с именем spark-hdi__ и кластер Kafka с именем **kafka-hdi**.

    * **Cluster Login User Name** (Имя пользователя для входа в кластер). Имя администратора для кластеров Spark и Kafka.

    * **Cluster Login User Password** (Пароль пользователя для входа в кластер). Имя администратора для кластеров Spark и Kafka.

    * **Имя пользователя SSH.** Создаваемый пользователь SSH для кластеров Spark и Kafka.

    * **Пароль SSH.** Пароль пользователя SSH для кластеров Spark и Kafka.

    * **Расположение.** Регион, в котором создаются кластеры.

3. Прочтите **условия использования** и установите флажок **Я принимаю указанные выше условия**.

4. Установите флажок **Закрепить на панели мониторинга** и нажмите кнопку **Приобрести**. Процесс создания кластеров занимает около 20 минут.

Когда указанные ресурсы будут созданы, отобразится колонка группы ресурсов, которая содержит кластеры и веб-панель мониторинга.

![Колонка группы ресурсов для виртуальной сети и кластеров](./media/hdinsight-apache-spark-with-kafka/groupblade.png)

> [!IMPORTANT]
> Обратите внимание, что кластерам HDInsight присвоены имена **spark-BASENAME** и **kafka-BASENAME**, где BASENAME — имя, указанное в шаблоне. Эти имена будут использоваться позже при подключении к кластерам.

## <a name="get-the-code"></a>Получение кода

Код для примера, описанного в этом документе, доступен по адресу [https://github.com/Azure-Samples/hdinsight-spark-scala-kafka](https://github.com/Azure-Samples/hdinsight-spark-scala-kafka).

## <a name="understand-the-code"></a>Изучение кода

В этом примере используется приложение Scala в блокноте Jupyter. Этот код в блокноте использует следующие данные.

* __Брокеры Kafka.__ Процесс брокера выполняется в каждом рабочем узле в кластере Kafka. Список брокеров требуется компоненту производителя, который записывает данные в Kafka.

* __Узлы Zookeeper.__ Узлы Zookeeper для кластера Kafka используются при использовании (чтении) данных из Kafka.

* __Имя раздела.__ Имя раздела для записи и чтения данных. В этом примере используется раздел с именем `sparktest`.

Дополнительную информацию о том, как получить брокер Kafka и узел Zookeeper, см. в разделе [Сведения об узле Kafka](#kafkahosts).

Пример кода в блокноте выполняет следующие задачи.

* Создает получатель, который считывает данные из раздела Kafka с именем `sparktest`, подсчитывает каждое слово в данных и сохраняет результат во временную таблицу с именем `wordcounts`.

* Создает производитель, который записывает случайные предложения в раздел Kafka с именем `sparktest`.

* Выбирает данные из таблицы `wordcounts` для отображения счетчиков.

Каждая ячейка в проекте содержит комментарии или текстовый раздел, в котором объясняется, что делает код.

##<a name="a-idkafkahostsakafka-host-information"></a><a id="kafkahosts"></a>Сведения об узле Kafka

При создании приложения, которое работает с Kafka в HDInsight, сначала нужно получить сведения о брокере Kafka и узле Zookeeper для кластера Kafka. Эти сведения используются клиентскими приложениями для обмена данными с Kafka.

> [!NOTE]
> Брокер Kafka и узлы Zookeeper недоступны напрямую через Интернет. Любое приложение, использующее Kafka, должно выполняться либо в самом кластере Kafka, либо в пределах виртуальной сети Azure, в которой этот кластер находится. В нашем примере приложение выполняется в Spark в кластере HDInsight в той же виртуальной сети.

Чтобы получить сведения о брокере и узлах Zookeeper, в среде разработки используйте следующие команды. Замените __PASSWORD__ паролем администратора, который использовался при создании кластера. Замените __BASENAME__ базовым именем, которое использовалось при создании кластера.

* Вот как получить сведения о __брокере Kafka__:

        curl -u admin:PASSWORD -G "https://kafka-BASENAME.azurehdinsight.net/api/v1/clusters/kafka-BASENAME/services/KAFKA/components/KAFKA_BROKER" | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")'

    > [!IMPORTANT]
    > При использовании этой команды в Windows PowerShell может возникнуть ошибка использования кавычек в оболочке. В таком случае используйте следующую команду: `curl -u admin:PASSWORD -G "https://kafka-BASENAME.azurehdinsight.net/api/v1/clusters/kafka-BASENAME/services/KAFKA/components/KAFKA_BROKER" | jq -r '["""\(.host_components[].HostRoles.host_name):9092"""] | join(""",""")'`

* Вот как получить сведения об __узле Zookeeper__:

        curl -u admin:PASSWORD -G "https://kafka-BASENAME.azurehdinsight.net/api/v1/clusters/kafka-BASENAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER" | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")'
    
    > [!IMPORTANT]
    > При использовании этой команды в Windows PowerShell может возникнуть ошибка использования кавычек в оболочке. В таком случае используйте следующую команду: `curl -u admin:PASSWORD -G "https://kafka-BASENAME.azurehdinsight.net/api/v1/clusters/kafka-BASENAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER" | jq -r '["""\(.host_components[].HostRoles.host_name):2181"""] | join(""",""")'`

Обе команды возвращают информацию приблизительно следующего содержания.

* __Для брокеров Kafka__: `wn0-kafka.4rf4ncirvydube02fuj0gpxp4e.ex.internal.cloudapp.net:9092,wn1-kafka.4rf4ncirvydube02fuj0gpxp4e.ex.internal.cloudapp.net:9092`

* __Для узлов Zookeeper__: `zk0-kafka.4rf4ncirvydube02fuj0gpxp4e.ex.internal.cloudapp.net:2181,zk1-kafka.4rf4ncirvydube02fuj0gpxp4e.ex.internal.cloudapp.net:2181,zk2-kafka.4rf4ncirvydube02fuj0gpxp4e.ex.internal.cloudapp.net:2181`

> [!IMPORTANT]
> Сохраните эти сведения, так как они вам понадобятся для дальнейшего использования.

## <a name="use-the-jupyter-notebook"></a>Запустите блокнот Jupyter.

Чтобы использовать блокнот Jupyter, необходимо загрузить его на сервер Jupyter Notebook в кластере Spark. Вот как это сделать.

1. В веб-браузере используйте следующий URL-адрес для подключения к серверу Jupyter Notebook в кластере Spark. Замените __BASENAME__ базовым именем, которое использовалось при создании кластера.

        https://spark-BASENAME.azurehdinsight.net/jupyter

    При появлении запроса введите имя администратора кластера и пароль, которые использовались при создании кластера.

2. В правой верхней части страницы нажмите кнопку __Отправить__ для загрузки файла `KafkaStreaming.ipynb`. Выберите файл в диалоговом окне браузера файла и щелкните __Открыть__. 

    ![Использование кнопки "Отправить" для выбора и загрузки блокнота](./media/hdinsight-apache-spark-with-kafka/upload-button.png)

    ![Выбор файла KafkaStreaming.ipynb](./media/hdinsight-apache-spark-with-kafka/select-notebook.png)

3. Найдите запись __KafkaStreaming.ipynb__ в списке блокнотов, а затем нажмите расположенную рядом кнопку __Отправка__.

    ![Использование кнопки "Отправка" рядом с записью KafkaStreaming.ipynb для отправки на сервер Jupyter Notebook](./media/hdinsight-apache-spark-with-kafka/upload-notebook.png)

4. Загрузив файл, выберите запись __KafkaStreaming.ipynb__, чтобы открыть блокнот. Чтобы выполнить этот пример, следуйте инструкциям в блокноте.

## <a name="delete-the-cluster"></a>Удаление кластера

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Выполнив описанные здесь инструкции, вы создадите два кластера в одной группе ресурсов Azure. Следовательно, вы можете удалить эту группу ресурсов на портале Azure. При этом будут удалены все созданные в рамках этого руководства и используемые в кластерах ресурсы, виртуальная сеть Azure и учетная запись хранения.

## <a name="next-steps"></a>Дальнейшие действия

В этом документе описано, как использовать Spark для чтения и записи данных в Kafka. Другие материалы, посвященные работе с Kafka, доступны по следующим ссылкам:

* [Get started with Apache Kafka on HDInsight (preview)](hdinsight-apache-kafka-get-started.md) (Приступая к работе с Apache Kafka в HDInsight (предварительная версия))
* [Use MirrorMaker to create a replica of a Kafka on HDInsight cluster (preview)](hdinsight-apache-kafka-mirroring.md) (Создание реплики Kafka в кластере HDInsight с помощью MirrorMaker (предварительная версия))
* [Совместное использование Apache Kafka (предварительная версия) и Storm в HDInsight](hdinsight-apache-storm-with-kafka.md)



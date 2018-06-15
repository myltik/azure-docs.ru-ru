---
title: Потоковая передача Apache Spark и Kafka в Azure HDInsight | Документация Майкрософт
description: Узнайте об использовании Apache Spark для двунаправленного потокового обмена данными с Apache Kafka с помощью DStreams. В этом примере описано, как выполнить потоковую передачу данных, используя записную книжку Jupyter из Spark в HDInsight.
keywords: пример kafka, kafka zookeeper, потоковая передача spark kafka, пример потоковой передачи spark kafka
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: dd8f53c1-bdee-4921-b683-3be4c46c2039
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: ''
ms.topic: conceptual
ms.date: 02/23/2018
ms.author: larryfr
ms.openlocfilehash: a9463b5983b5f41683a5cfe416ca125bf2810062
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2018
ms.locfileid: "31402385"
---
# <a name="apache-spark-streaming-dstream-example-with-kafka-on-hdinsight"></a>Пример потоковой передачи Apache Spark (DStream) с использованием Kafka в HDInsight

Узнайте об использовании Apache Spark для двунаправленного потокового обмена данными с Apache Kafka в HDInsight с помощью DStreams. В этом примере используется записная книжка Jupyter, которая выполняется на кластере Spark.

> [!NOTE]
> Вы узнаете, как создать группу ресурсов Azure, которая содержит кластеры Spark и Kafka в HDInsight. Оба этих кластера находятся в виртуальной сети Azure, что позволяет кластеру Spark напрямую обмениваться данными с кластером Kafka.
>
> Выполнив инструкции, не забудьте удалить кластеры, чтобы избежать ненужных расходов.

> [!IMPORTANT]
> В этом примере используется DStreams (старая технология потоковой передачи Spark). Пример, использующий новые функции потоковой передачи Spark, см. в статье [Использование структурированной потоковой передачи Spark с Kafka в HDInsight](hdinsight-apache-kafka-spark-structured-streaming.md).

## <a name="create-the-clusters"></a>Создание кластеров

Apache Kafka в HDInsight не предоставляет доступ к брокерам Kafka через общедоступный сегмент Интернета. Все объекты, обращающиеся к Kafka, должны находиться в той же виртуальной сети Azure, что и узлы в кластере Kafka. В этом примере кластеры Kafka и Spark расположены в виртуальной сети Azure. На следующей схеме показано, как взаимодействуют кластеры.

![Схема кластеров Spark и Kafka в виртуальной сети Azure](./media/hdinsight-apache-spark-with-kafka/spark-kafka-vnet.png)

> [!NOTE]
> Хотя само решение Kafka ограничено связью в пределах виртуальной сети, другие службы в кластере, например SSH и Ambari, доступны через Интернет. Дополнительные сведения об общих портах, доступных в HDInsight, см. в статье [Порты и универсальные коды ресурсов (URI), используемые кластерами HDInsight](hdinsight-hadoop-port-settings-for-services.md).

Хотя виртуальную сеть Azure, а также кластеры Kafka и Spark можно создать вручную, проще использовать шаблон Azure Resource Manager. Выполните следующие действия, чтобы развернуть виртуальную сеть Azure, а также кластеры Kafka и Spark в подписке Azure.

1. Нажмите эту кнопку, чтобы войти в Azure и открыть шаблон на портале Azure.
    
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-kafka-spark-cluster-in-vnet-v4.1.json" target="_blank"><img src="./media/hdinsight-apache-spark-with-kafka/deploy-to-azure.png" alt="Deploy to Azure"></a>
    
    Шаблон Azure Resource Manager доступен по адресу **https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-kafka-spark-cluster-in-vnet-v4.1.json**.

    > [!WARNING]
    > Чтобы обеспечить доступность Kafka в HDInsight, кластер должен содержать не менее трех рабочих узлов. Этот шаблон создает кластер Kafka, содержащий три рабочих узла.

    Этот шаблон создает кластер HDInsight 3.6 для Kafka и Spark.

2. Используйте следующие сведения, чтобы заполнить раздел **Настраиваемое развертывание**:
   
    ![Настраиваемое развертывание в HDInsight](./media/hdinsight-apache-spark-with-kafka/parameters.png)
   
    * **Группа ресурсов.** Создайте новую группу ресурсов или выберите существующую. Эта группа содержит кластер HDInsight.

    * **Расположение.** Выберите близкое к вам географическое расположение.

    * **Базовое имя кластера**. Это значение будет использоваться в качестве базового имени для кластеров Spark и Kafka. Например, если ввести **hdi**, будет создан кластер Spark с именем __spark-hdi__ и кластер Kafka с именем **kafka-hdi**.

    * **Cluster Login User Name** (Имя пользователя для входа в кластер). Имя администратора для кластеров Spark и Kafka.

    * **Cluster Login User Password** (Пароль пользователя для входа в кластер). Имя администратора для кластеров Spark и Kafka.

    * **Имя пользователя SSH.** Создаваемый пользователь SSH для кластеров Spark и Kafka.

    * **Пароль SSH.** Пароль пользователя SSH для кластеров Spark и Kafka.

3. Прочтите **условия использования** и установите флажок **Я принимаю указанные выше условия**.

4. Установите флажок **Закрепить на панели мониторинга** и нажмите кнопку **Приобрести**. Процесс создания кластеров занимает около 20 минут.

Когда ресурсы будут созданы, отобразится страница со сводными сведениями.

![Сводные сведения о группе ресурсов для виртуальной сети и кластеров](./media/hdinsight-apache-spark-with-kafka/groupblade.png)

> [!IMPORTANT]
> Обратите внимание, что кластерам HDInsight присвоены имена **spark-BASENAME** и **kafka-BASENAME**, где BASENAME — имя, указанное в шаблоне. Эти имена будут использоваться позже при подключении к кластерам.

## <a name="use-the-notebooks"></a>Использование записных книжек

Код для примера, описанного в этом документе: [https://github.com/Azure-Samples/hdinsight-spark-scala-kafka](https://github.com/Azure-Samples/hdinsight-spark-scala-kafka).

Следуйте указаниям в файле `README.md`, чтобы выполнить этот пример.

## <a name="delete-the-cluster"></a>Удаление кластера

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Выполнив описанные здесь инструкции, вы создадите два кластера в одной группе ресурсов Azure. Следовательно, вы можете удалить эту группу ресурсов на портале Azure. При этом будут удалены все созданные в рамках этого руководства и используемые в кластерах ресурсы, виртуальная сеть Azure и учетная запись хранения.

## <a name="next-steps"></a>Дополнительная информация

В этом примере описано, как использовать Spark для чтения и записи данных в Kafka. Другие материалы, посвященные работе с Kafka, доступны по следующим ссылкам:

* [Get started with Apache Kafka on HDInsight (preview)](kafka/apache-kafka-get-started.md) (Приступая к работе с Apache Kafka в HDInsight (предварительная версия))
* [Use MirrorMaker to create a replica of a Kafka on HDInsight cluster (preview)](kafka/apache-kafka-mirroring.md) (Создание реплики Kafka в кластере HDInsight с помощью MirrorMaker (предварительная версия))
* [Совместное использование Apache Kafka (предварительная версия) и Storm в HDInsight](hdinsight-apache-storm-with-kafka.md)


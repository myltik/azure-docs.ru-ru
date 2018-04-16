---
title: Структурированная потоковая передача Apache Spark с Kafka в Azure HDInsight | Документы Майкрософт
description: Узнайте об использовании потоковой передачи Apache Spark (DStream) для двунаправленного обмена данными с Apache Kafka. В этом примере показано, как выполнить потоковую передачу данных, используя записную книжку Jupyter из Spark в HDInsight.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: cgronlun
editor: cgronlun
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: ''
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 04/04/2018
ms.author: larryfr
ms.openlocfilehash: 49c13bbea537d7de60ecf509bc28675191c0b34d
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/06/2018
---
# <a name="use-spark-structured-streaming-with-kafka-on-hdinsight"></a>Использование структурированной потоковой передачи Spark с Kafka в HDInsight

Узнайте, как использовать структурированную потоковую передачу Spark для чтения данных из Apache Kafka в Azure HDInsight.

Структурированная потоковая передача Spark — это механизм обработки потока, встроенный в Spark SQL. Он позволяет выражать потоковые вычисления так же, как пакетные вычисления статических данных. Дополнительные сведения о структурированной потоковой передаче см. в разделе [Руководство по программированию структурированной потоковой передачи [альфа-версия]](http://spark.apache.org/docs/2.2.0/structured-streaming-programming-guide.html) на Apache.org.

> [!IMPORTANT]
> В этом примере используется Spark 2.2 в HDInsight 3.6.
>
> Вы узнаете, как создать группу ресурсов Azure, которая содержит кластеры Spark и Kafka в HDInsight. Оба этих кластера находятся в виртуальной сети Azure, что позволяет кластеру Spark напрямую обмениваться данными с кластером Kafka.
>
> Выполнив инструкции, не забудьте удалить кластеры, чтобы избежать ненужных расходов.

## <a name="create-the-clusters"></a>Создание кластеров

Apache Kafka в HDInsight не предоставляет доступ к брокерам Kafka через общедоступный сегмент Интернета. Все ресурсы, которые использует Kafka, должны находиться в одной виртуальной сети Azure. В рамках этого руководства кластеры Kafka и Spark расположены в одной и той же виртуальной сети Azure. 

На следующей схеме показано, как происходит обмен данными между Spark и Kafka:

![Схема кластеров Spark и Kafka в виртуальной сети Azure](./media/hdinsight-apache-spark-with-kafka/spark-kafka-vnet.png)

> [!NOTE]
> Служба Kafka ограничена обменом данными в пределах виртуальной сети. Другие службы в кластере, например SSH и Ambari, могут быть доступны через Интернет. Дополнительные сведения об общих портах, доступных в HDInsight, см. в статье [Порты и универсальные коды ресурсов (URI), используемые кластерами HDInsight](hdinsight-hadoop-port-settings-for-services.md).

Для удобства в приведенных ниже действиях для создания кластеров Kafka и Spark в виртуальной сети используется шаблон Azure Resource Manager.

1. Нажмите эту кнопку, чтобы войти в Azure и открыть шаблон на портале Azure.
    
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fhdinsight-spark-kafka-structured-streaming%2Fmaster%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-apache-spark-with-kafka/deploy-to-azure.png" alt="Deploy to Azure"></a>
    
    Шаблон Azure Resource Manager доступен по адресу **https://raw.githubusercontent.com/Azure-Samples/hdinsight-spark-kafka-structured-streaming/master/azuredeploy.json**.

    Этот шаблон создает следующие ресурсы:

    * Kafka в кластере HDInsight 3.6;
    * Spark 2.2.0 в кластере HDInsight 3.6;
    * виртуальную сеть Azure, содержащую кластеры HDInsight.

    > [!IMPORTANT]
    > Записная книжка структурированной потоковой передачи, используемая в этом примере, требует Spark в HDInsight 3.6. Если используется более ранняя версия Spark в HDInsight, возникнут ошибки при использовании этой записной книжки.

2. Заполните раздел **Настроенный шаблон**, используя следующие сведения:

    | Параметр | Значение |
    | --- | --- |
    | Подписка | Ваша подписка Azure. |
    | Группа ресурсов | Группа ресурсов, в которой содержатся ресурсы. |
    | Расположение | Регион Azure, в котором создаются ресурсы. |
    | Spark Cluster Name (Имя кластера Spark) | Имя кластера Spark. |
    | Kafka Cluster Name (Имя кластера Kafka) | Имя кластера Kafka. |
    | Имя пользователя для входа в кластер | Имя администратора кластеров. |
    | Пароль для входа в кластер | Пароль администратора кластеров. |
    | Имя пользователя SSH | Пользователь SSH, который создается для кластеров. |
    | Пароль SSH | Пароль пользователя SSH. |
   
    ![Снимок экрана с настроенным шаблоном](./media/hdinsight-apache-kafka-spark-structured-streaming/spark-kafka-template.png)

4. Установите флажок **Закрепить на панели мониторинга** и нажмите кнопку **Приобрести**. 

> [!NOTE]
> Создание кластеров может занять до 20 минут.

## <a name="get-the-notebook"></a>Получение записной книжки

Код для примера, описанного в этом документе: [https://github.com/Azure-Samples/hdinsight-spark-kafka-structured-streaming](https://github.com/Azure-Samples/hdinsight-spark-kafka-structured-streaming).

## <a name="upload-the-notebooks"></a>Отправка записных книжек

Чтобы отправить записные книжки из проекта в кластер Spark в HDInsight, выполните следующие действия:

1. В веб-браузере подключитесь к записной книжке Jupyter в вашем кластере Spark. В следующем URL-адресе замените `CLUSTERNAME` именем вашего кластера __Spark__:

        https://CLUSTERNAME.azurehdinsight.net/jupyter

    При появлении запроса введите имя администратора кластера и пароль, которые использовались при создании кластера.

2. В правой верхней части страницы нажмите кнопку __Отправить__, чтобы отправить файл __spark-structured-streaming-kafka.ipynb__ в кластер. Нажмите __Открыть__ для запуска отправки.

    ![Использование кнопки "Отправить" для выбора и загрузки блокнота](./media/hdinsight-apache-kafka-spark-structured-streaming/upload-button.png)

    ![Выбор файла KafkaStreaming.ipynb](./media/hdinsight-apache-kafka-spark-structured-streaming/select-notebook.png)

3. Найдите запись __spark-structured-streaming-kafka.ipynb__ в списке записных книжек, а затем нажмите расположенную рядом кнопку __Отправить__.

    ![Чтобы отправить записную книжку, нажмите кнопку отправки рядом с записью KafkaStreaming.ipynb.](./media/hdinsight-apache-kafka-spark-structured-streaming/upload-notebook.png)


## <a name="use-the-notebook"></a>Использование записной книжки

После отправки файлов выберите запись __spark-structured-streaming-kafka.ipynb__, чтобы открыть записную книжку. Чтобы узнать, как использовать структурированную потоковую передачу Spark с Kafka в HDInsight, следуйте инструкциям в записной книжке.

## <a name="clean-up-resources"></a>Очистка ресурсов

Чтобы очистить ресурсы, созданные при работе с этим руководством, удалите группу ресурсов. При этом будет удален связанный кластер HDInsight и другие ресурсы, связанные с этой группой ресурсов.

Чтобы удалить группу ресурсов с помощью портала Azure, сделайте следующее:

1. На портале Azure разверните меню слева, чтобы открыть меню служб, а затем выберите __Группы ресурсов__, чтобы просмотреть список групп ресурсов.
2. Найдите группу ресурсов, которую нужно удалить, и щелкните правой кнопкой мыши кнопку __Дополнительно__ (…) справа от списка.
3. Выберите __Удалить группу ресурсов__ и подтвердите выбор.

> [!WARNING]
> Начисление оплаты начинается после создания кластера HDInsight и прекращается только после его удаления. Кластеры оплачиваются поминутно, поэтому всегда следует удалять кластер, когда он больше не нужен.
> 
> При удалении кластера Kafka в HDInsight удаляются все данные, хранящиеся в Kafka.

## <a name="next-steps"></a>Дополнительная информация

Теперь, когда вы узнали, как использовать структурированную потоковую передачу Spark, перейдите к следующим документам для углубленного изучения работы со Spark и Kafka.

* [Как использовать потоковую передачу Spark (DStream) с Kafka](hdinsight-apache-spark-with-kafka.md).
* [Начало работы с записной книжкой Jupyter и Spark в HDInsight](spark/apache-spark-jupyter-spark-sql.md).
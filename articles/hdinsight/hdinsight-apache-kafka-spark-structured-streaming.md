---
title: Руководство. Структурированная потоковая передача Apache Spark с Kafka в Azure HDInsight | Документация Майкрософт
description: Узнайте об использовании потоковой передачи Apache Spark для двунаправленного обмена данными с Apache Kafka. В этом руководстве показано, как выполнить потоковую передачу данных, используя записную книжку Jupyter из Spark в HDInsight.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: cgronlun
editor: cgronlun
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: ''
ms.topic: tutorial
ms.date: 05/08/2018
ms.author: larryfr
ms.openlocfilehash: 8c7c1b37102e94f00ac6077958952eb52b342668
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/10/2018
ms.locfileid: "33939260"
---
# <a name="tutorial-use-spark-structured-streaming-with-kafka-on-hdinsight"></a>Руководство. Использование структурированной потоковой передачи Spark с Kafka в HDInsight

В этом руководстве демонстрируется, как использовать структурированную потоковую передачу Spark, чтобы считывать и записывать данные с использованием Apache Kafka в Azure HDInsight.

Структурированная потоковая передача Spark — это механизм обработки потока, встроенный в Spark SQL. Он позволяет выражать потоковые вычисления так же, как пакетные вычисления статических данных. 

Из этого руководства вы узнаете, как выполнять такие задачи:

> [!div class="checklist"]
> * Структурированная потоковая передача с помощью Kafka
> * Создание кластеров Kafka и Spark.
> * Передача записной книжки в Spark.
> * Использование записной книжки
> * Очистка ресурсов

Выполнив инструкции, не забудьте удалить кластеры, чтобы избежать ненужных расходов.

## <a name="prerequisites"></a>предварительным требованиям

* Опыт работы с записными книжками Jupyter с Spark в HDInsight. Дополнительные сведения см. в статье [Выполнение интерактивных запросов в кластерах Spark в HDInsight](spark/apache-spark-load-data-run-query.md).

* Опыт работы с языком [Scala](https://www.scala-lang.org/). Код, используемый в этом руководстве, написан на языке Scala.

* Опыт создания разделов Kafka. Дополнительные сведения см. в документе [Приступая к работе с Apache Kafka в HDInsight](kafka/apache-kafka-get-started.md).

> [!IMPORTANT]
> Чтобы выполнить действия, описанные в этом документе, необходимо иметь группу ресурсов Azure, которая содержит кластеры Spark и Kafka в HDInsight. Оба этих кластера находятся в виртуальной сети Azure, что позволяет кластеру Spark напрямую обмениваться данными с кластером Kafka.
> 
> Для удобства в этом документе есть ссылка на шаблон, с помощью которого можно создать все необходимые ресурсы Azure. 
>
> Дополнительные сведения об использовании HDInsight в виртуальной сети см. в статье [Расширение возможностей HDInsight с помощью виртуальной сети Azure](hdinsight-extend-hadoop-virtual-network.md).

## <a name="structured-streaming-with-kafka"></a>Структурированная потоковая передача с помощью Kafka

Структурированная потоковая передача Spark — это механизм обработки потока, встроенный в ядро Spark SQL. При использовании структурированной потоковой передачи можно писать запросы потоковой передачи так же, как и пакетные запросы.

В следующих фрагментах кода демонстрируется чтение данных из Kafka и их сохранение в файле. Первый фрагмент кода — это пакетная операция, а второй — операция потоковой передачи.

```scala
// Read a batch from Kafka
val kafkaDF = spark.read.format("kafka")
                .option("kafka.bootstrap.servers", kafkaBrokers)
                .option("subscribe", kafkaTopic)
                .option("startingOffsets", "earliest")
                .load()
// Select data and write to file
kafkaDF.select(from_json(col("value").cast("string"), schema) as "trip")
                .write
                .format("parquet")
                .option("path","/example/batchtripdata")
                .option("checkpointLocation", "/batchcheckpoint")
                .save()
```

```scala
// Stream from Kafka
val kafkaStreamDF = spark.readStream.format("kafka")
                .option("kafka.bootstrap.servers", kafkaBrokers)
                .option("subscribe", kafkaTopic)
                .option("startingOffsets", "earliest")
                .load()
// Select data from the stream and write to file
kafkaStreamDF.select(from_json(col("value").cast("string"), schema) as "trip")
                .writeStream
                .format("parquet")
                .option("path","/example/streamingtripdata")
                .option("checkpointLocation", "/streamcheckpoint")
                .start.awaitTermination(30000)
```

В обоих фрагментах кода данные считываются из Kafka и записываются в файл. Ниже показаны различия в примерах.

| Пакетная служба Azure | Потоковая передача |
| --- | --- |
| `read` | `readStream` |
| `write` | `writeStream` |
| `save` | `start` |

Операция потоковой передачи также использует `awaitTermination(30000)`, что останавливает потоковую передачу спустя 30 000 мс. 

Чтобы использовать структурированную потоковую передачу с помощью Kafka, ваш проект должен иметь зависимость в пакете `org.apache.spark : spark-sql-kafka-0-10_2.11`. Версия этого пакета должна соответствовать версии Spark в HDInsight. Для Spark версии 2.2.0 (доступна в HDInsight 3.6) можно найти сведения о зависимости для разных типов проекта по адресу [https://search.maven.org/#artifactdetails%7Corg.apache.spark%7Cspark-sql-kafka-0-10_2.11%7C2.2.0%7Cjar](https://search.maven.org/#artifactdetails%7Corg.apache.spark%7Cspark-sql-kafka-0-10_2.11%7C2.2.0%7Cjar).

Для записной книжки Jupyter, предоставленной в этом руководстве, следующие ячейки передают зависимость пакета:

```
%%configure -f
{
    "conf": {
        "spark.jars.packages": "org.apache.spark:spark-sql-kafka-0-10_2.11:2.2.0",
        "spark.jars.excludes": "org.scala-lang:scala-reflect,org.apache.spark:spark-tags_2.11"
    }
}
```

## <a name="create-the-clusters"></a>Создание кластеров

Apache Kafka в HDInsight не предоставляет доступ к брокерам Kafka через общедоступный сегмент Интернета. Все ресурсы, которые использует Kafka, должны находиться в одной виртуальной сети Azure. В рамках этого руководства кластеры Kafka и Spark расположены в одной и той же виртуальной сети Azure. 

На следующей схеме показано, как происходит обмен данными между Spark и Kafka:

![Схема кластеров Spark и Kafka в виртуальной сети Azure](./media/hdinsight-apache-spark-with-kafka/spark-kafka-vnet.png)

> [!NOTE]
> Служба Kafka ограничена обменом данными в пределах виртуальной сети. Другие службы в кластере, например SSH и Ambari, могут быть доступны через Интернет. Дополнительные сведения об общих портах, доступных в HDInsight, см. в статье [Порты и универсальные коды ресурсов (URI), используемые кластерами HDInsight](hdinsight-hadoop-port-settings-for-services.md).

Чтобы создать виртуальную сеть Azure, а затем создать кластеры Kafka и Spark в ее пределах, выполните такие действия:

1. Нажмите эту кнопку, чтобы войти в Azure и открыть шаблон на портале Azure.
    
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fhdinsight-spark-kafka-structured-streaming%2Fmaster%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-apache-spark-with-kafka/deploy-to-azure.png" alt="Deploy to Azure"></a>
    
    Шаблон Azure Resource Manager доступен по адресу **https://raw.githubusercontent.com/Azure-Samples/hdinsight-spark-kafka-structured-streaming/master/azuredeploy.json**.

    Этот шаблон создает следующие ресурсы:

    * Kafka в кластере HDInsight 3.6;
    * Spark 2.2.0 в кластере HDInsight 3.6;
    * виртуальную сеть Azure, содержащую кластеры HDInsight.

    > [!IMPORTANT]
    > Записная книжка структурированной потоковой передачи, используемая в этом руководстве, требует Spark 2.2.0 в HDInsight 3.6. Если используется более ранняя версия Spark в HDInsight, возникнут ошибки при использовании этой записной книжки.

2. Заполните раздел **Настроенный шаблон**, используя следующие сведения:

    | Параметр | Значение |
    | --- | --- |
    | Подписка | Ваша подписка Azure. |
    | Группа ресурсов | Группа ресурсов, в которой содержатся ресурсы. |
    | Расположение | Регион Azure, в котором создаются ресурсы. |
    | Spark Cluster Name (Имя кластера Spark) | Имя кластера Spark. Первые шесть символов должны отличаться от имени кластера Kafka. |
    | Kafka Cluster Name (Имя кластера Kafka) | Имя кластера Kafka. Первые шесть символов должны отличаться от имени кластера Spark. |
    | Имя пользователя для входа в кластер | Имя администратора кластеров. |
    | Пароль для входа в кластер | Пароль администратора кластеров. |
    | Имя пользователя SSH | Пользователь SSH, который создается для кластеров. |
    | Пароль SSH | Пароль пользователя SSH. |
   
    ![Снимок экрана с настроенным шаблоном](./media/hdinsight-apache-kafka-spark-structured-streaming/spark-kafka-template.png)

3. Прочтите **условия использования** и установите флажок **Я принимаю указанные выше условия**.

4. Установите флажок **Закрепить на панели мониторинга** и нажмите кнопку **Приобрести**. 

> [!NOTE]
> Создание кластеров может занять до 20 минут.

## <a name="upload-the-notebook"></a>Передача записной книжки

Чтобы отправить записные книжки из проекта в кластер Spark в HDInsight, выполните следующие действия:

1. Скачайте проект по этому адресу [https://github.com/Azure-Samples/hdinsight-spark-kafka-structured-streaming](https://github.com/Azure-Samples/hdinsight-spark-kafka-structured-streaming).

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

Из этого руководства вы узнали, как использовать структурированную потоковую передачу Spark для записи и чтения данных из Kafka в HDInsight. Воспользуйтесь следующей ссылкой, чтобы узнать, как совместно использовать Storm и Kafka.

> [!div class="nextstepaction"]
> [Руководство. Использование Apache Storm с Kafka в HDInsight](hdinsight-apache-storm-with-kafka.md)
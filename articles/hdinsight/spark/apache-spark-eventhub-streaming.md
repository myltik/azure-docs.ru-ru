---
title: Руководство. Обработка данных из концентраторов событий Azure с помощью Apache Spark в Azure HDInsight | Документация Майкрософт
description: Подключите Apache Spark в Azure HDInsight к концентраторам событий Azure и выполните потоковою передачу данных.
services: hdinsight
documentationcenter: ''
author: mumian
manager: cgronlun
editor: cgronlun
tags: azure-portal
ms.service: hdinsight
ms.custom: hdinsightactive,mvc
ms.devlang: na
ms.topic: conceptual
ms.date: 05/07/2018
ms.author: jgao
ms.openlocfilehash: 9b59f5d58234aaf8f8385f722d6659548e066933
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/07/2018
ms.locfileid: "33781413"
---
# <a name="tutorial-process-tweets-using-azure-event-hubs-and-spark-in-hdinsight"></a>Руководство. Обработка твитов с помощью концентраторов событий Azure и Spark в HDInsight

В этом руководстве вы научитесь создавать приложение потоковой передачи Apache Spark для отправки твитов в концентратор событий Azure, а также приложение для чтения твитов из концентратора событий. Подробное описание потоковой передачи Spark см. в [этом разделе](http://spark.apache.org/docs/latest/streaming-programming-guide.html#overview). HDInsight предоставляет аналогичные функции потоковой передачи для кластера Spark в Azure.

Из этого руководства вы узнаете, как выполнять такие задачи:
> [!div class="checklist"]
> * отправка сообщений в концентратор событий Azure;
> * чтение сообщений из концентратора событий Azure.

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/), прежде чем начинать работу.

## <a name="prerequisites"></a>предварительным требованиям

* **Выполните инструкции, приведенные в статье [Выполнение интерактивных запросов в кластерах Spark в HDInsight](./apache-spark-load-data-run-query.md)**.

## <a name="create-a-twitter-application"></a>Создание приложения Twitter

Для получения потока твитов создайте приложение в Twitter. Следуйте инструкциям по созданию приложения Twitter и запишите значения, необходимые для выполнения заданий из этого руководства.

1. Перейдите на [страницу управления приложениями Twitter](https://apps.twitter.com/).
2. Выберите **Create New App** (Создать приложение).
3. Укажите следующие значения.

    - "Имя": укажите имя приложения. В этом руководстве используется имя **HDISparkStreamApp0423**. Имя должно быть уникальным.
    - "Описание": укажите краткое описание приложения. В этом руководстве используется следующее описание: **Простое приложение потоковой передачи HDInsight Spark**.
    - "Веб-сайт": укажите веб-сайт приложения. Необязательно указывать действительный веб-сайт.  В этом руководстве используется значение **http://www.contoso.com**.
    - URL-адрес обратного вызова. Это поле можно оставить пустым.

4. Выберите **Yes, I have read and agree to the Twitter Developer Agreement** (Я подтверждаю ознакомление и согласие с соглашением с разработчиком Twitter), а затем выберите **Create your Twitter application** (Создать приложение Twitter).
5. Откройте вкладку **Ключи и токены доступа** .
6. В конце страницы выберите **Create my access token** (Создать маркер доступа).
7. Запишите следующие значения со страницы.  Они понадобятся вам позже при работе с этим руководством.

    - **Ключ пользователя (ключ API)**.    
    - **Секрет пользователя (секрет API)**.  
    - **Маркер доступа**
    - **Секрет маркера доступа**.   

## <a name="create-an-azure-event-hub"></a>Создание концентратора событий Azure

Используйте этот концентратор событий для хранения твитов.

1. Войдите на [портал Azure](https://ms.portal.azure.com).
2. В верхней левой части экрана выберите **Create a resource** (Создать ресурс).
3. Выберите **Интернет вещей**, а затем — **Концентраторы событий**.

    ![Создание концентратора событий для примера приложения потоковой передачи Spark](./media/apache-spark-eventhub-streaming/hdinsight-create-event-hub-for-spark-streaming.png "Создание концентратора событий для примера приложения потоковой передачи Spark")
4. Для пространства имен нового концентратора событий введите следующие значения.

    - **Имя**. Введите имя для концентратора событий.  В этом руководстве используется имя **myeventhubns20180403**.
    - **Ценовая категория**. Выберите **Стандартный**.
    - **Группа ресурсов**. У вас есть возможность создать новую группу ресурсов для кластера Spark или же выбрать имеющуюся. 
    - **Расположение**. Для сокращения задержек и затрат выберите то же **расположение**, в котором находится кластер Apache Spark в HDInsight.

    ![Указание имени концентратора событий для примера приложения потоковой передачи Spark](./media/apache-spark-eventhub-streaming/hdinsight-provide-event-hub-name-for-spark-streaming.png "Указание имени концентратора событий для примера приложения потоковой передачи Spark")
5. Выберите **Создать**, чтобы создать пространство имен.

6. Откройте пространство имен концентратора событий, выполнив следующие инструкции:

    1. На портале щелкните **Все службы**.
    2. В текстовом поле "Фильтр" введите **концентраторы событий**.
    3. Дважды щелкните созданное пространство имен.
    4. Выберите **+ Концентратор событий**.

6. В списке пространств имен концентраторов событий выберите созданное пространство имен.      
5. Чтобы создать концентратор событий, выберите **Концентраторы событий**, а затем — **+ Концентратор событий**.
  

6. Введите следующие значения.

    - "Имя". Задайте имя для этого концентратора событий.
    - "Количество разделов": 10.
    - "Хранение сообщений": 1. 
   
    ![Указание сведений о концентраторе событий для примера приложения потоковой передачи Spark](./media/apache-spark-eventhub-streaming/hdinsight-provide-event-hub-details-for-spark-streaming-example.png "Указание сведений о концентраторе событий для примера приложения потоковой передачи Spark")

7. Нажмите кнопку **Создать**.
8. Выберите **Политики общего доступа** для пространства имен (обратите внимание на то, что это не политики общего доступа концентратора событий), а затем выберите **RootManageSharedAccessKey**.
    
     ![Определение политик концентратора событий для примера приложения потоковой передачи Spark](./media/apache-spark-eventhub-streaming/hdinsight-set-event-hub-policies-for-spark-streaming-example.png "Определение политик концентратора событий для примера приложения потоковой передачи Spark")

9. Сохраните значения **Первичный ключ** и **Строка подключения — первичный ключ**. Они понадобятся позже для работы с этим руководством.

     ![Просмотр ключей политик концентратора событий для примера приложения потоковой передачи Spark](./media/apache-spark-eventhub-streaming/hdinsight-view-event-hub-policy-keys.png "Просмотр ключей политик концентратора событий для примера приложения потоковой передачи Spark")


## <a name="send-tweets-to-the-event-hub"></a>Отправка твитов в концентратор событий

Создайте записную книжку Jupyter с именем **SendTweetsToEventHub**. 

1. Добавьте внешние библиотеки Maven, выполнив следующий код:

    ```
    %%configure
    {"conf":{"spark.jars.packages":"com.microsoft.azure:azure-eventhubs-spark_2.11:2.2.0,org.twitter4j:twitter4j-core:4.0.6"}}
    ```

2. Отправьте твиты в концентратор событий, выполнив следующий код:

    ```
    import java.util._
    import scala.collection.JavaConverters._
    import java.util.concurrent._
    
    import org.apache.spark._
    import org.apache.spark.streaming._
    import org.apache.spark.eventhubs.ConnectionStringBuilder

    // Event hub configurations
    // Replace values below with yours        
    val eventHubName = "<Event hub name>"
    val eventHubNSConnStr = "<Event hub namespace connection string>"
    val connStr = ConnectionStringBuilder(eventHubNSConnStr).setEventHubName(eventHubName).build 
    
    import com.microsoft.azure.eventhubs._
    val pool = Executors.newFixedThreadPool(1)
    val eventHubClient = EventHubClient.create(connStr.toString(), pool)
    
    def sendEvent(message: String) = {
          val messageData = EventData.create(message.getBytes("UTF-8"))
          eventHubClient.get().send(messageData)
          println("Sent event: " + message + "\n")
    }
    
    import twitter4j._
    import twitter4j.TwitterFactory
    import twitter4j.Twitter
    import twitter4j.conf.ConfigurationBuilder

    // Twitter application configurations
    // Replace values below with yours   
    val twitterConsumerKey = "<CONSUMER KEY>"
    val twitterConsumerSecret = "<CONSUMER SECRET>"
    val twitterOauthAccessToken = "<ACCESS TOKEN>"
    val twitterOauthTokenSecret = "<TOKEN SECRET>"
    
    val cb = new ConfigurationBuilder()
    cb.setDebugEnabled(true).setOAuthConsumerKey(twitterConsumerKey).setOAuthConsumerSecret(twitterConsumerSecret).setOAuthAccessToken(twitterOauthAccessToken).setOAuthAccessTokenSecret(twitterOauthTokenSecret)
    
    val twitterFactory = new TwitterFactory(cb.build())
    val twitter = twitterFactory.getInstance()

    // Getting tweets with keyword "Azure" and sending them to the Event Hub in realtime!
    
    val query = new Query(" #Azure ")
    query.setCount(100)
    query.lang("en")
    var finished = false
    while (!finished) {
      val result = twitter.search(query)
      val statuses = result.getTweets()
      var lowestStatusId = Long.MaxValue
      for (status <- statuses.asScala) {
        if(!status.isRetweet()){
          sendEvent(status.getText())
        }
        lowestStatusId = Math.min(status.getId(), lowestStatusId)
        Thread.sleep(2000)
      }
      query.setMaxId(lowestStatusId - 1)
    }
    
    // Closing connection to the Event Hub
    eventHubClient.get().close()
    ```

3. Откройте концентратор событий на портале Azure.  На странице **обзора** вы увидите несколько диаграмм, отображающих сообщения, отправленные в концентратор событий.

## <a name="read-tweets-from-the-event-hub"></a>Чтение твитов из концентратора событий

Создайте другую записную книжку Jupyter с именем **ReadTweetsFromEventHub**. 

1. Добавьте внешнюю библиотеку Maven, выполнив следующий код:

    ```
    %%configure -f
    {"conf":{"spark.jars.packages":"com.microsoft.azure:azure-eventhubs-spark_2.11:2.2.0"}}
    ```
2. Считайте твиты из концентратора событий, выполнив следующий код:

    ```
    import org.apache.spark.eventhubs._
    // Event hub configurations
    // Replace values below with yours        
    val eventHubName = "<Event hub name>"
    val eventHubNSConnStr = "<Event hub namespace connection string>"
    val connStr = ConnectionStringBuilder(eventHubNSConnStr).setEventHubName(eventHubName).build 
    
    val customEventhubParameters = EventHubsConf(connectionString).setMaxEventsPerTrigger(5)
    val incomingStream = spark.readStream.format("eventhubs").options(customEventhubParameters.toMap).load()
    //incomingStream.printSchema    
    
    import org.apache.spark.sql.types._
    import org.apache.spark.sql.functions._
    
    // Event Hub message format is JSON and contains "body" field
    // Body is binary, so you cast it to string to see the actual content of the message
    val messages = incomingStream.withColumn("Offset", $"offset".cast(LongType)).withColumn("Time (readable)", $"enqueuedTime".cast(TimestampType)).withColumn("Timestamp", $"enqueuedTime".cast(LongType)).withColumn("Body", $"body".cast(StringType)).select("Offset", "Time (readable)", "Timestamp", "Body")
    
    messages.printSchema
    
    messages.writeStream.outputMode("append").format("console").option("truncate", false).start().awaitTermination()
    ```

## <a name="clean-up-resources"></a>Очистка ресурсов

В случае с HDInsight ваши данные хранятся в службе хранилища Azure или Azure Data Lake Store, что позволяет безопасно удалить неиспользуемый кластер. Плата за кластеры HDInsight взимается, даже когда они не используются. Поскольку стоимость кластера во много раз превышает стоимость хранилища, экономически целесообразно удалять неиспользуемые кластеры. Если вы планируете сразу приступить к следующему руководству, можно оставить кластер.

Откройте кластер на портале Azure и выберите **Удалить**.

![Удаление кластера HDInsight](./media/apache-spark-load-data-run-query/hdinsight-azure-portal-delete-cluster.png "Удаление кластера HDInsight")

Кроме того, можно выбрать имя группы ресурсов, чтобы открыть страницу группы ресурсов, а затем нажать кнопку **Удалить группу ресурсов**. Вместе с группой ресурсов вы также удаляете кластер HDInsight Spark и учетную запись хранения по умолчанию.

## <a name="next-steps"></a>Дополнительная информация

Из этого руководства вы узнали, как выполнить следующие задачи:

* чтение сообщений из концентратора событий.
Перейдите к следующей статье, чтобы создать приложение машинного обучения. 

> [!div class="nextstepaction"]
> [Создание приложений машинного обучения Apache Spark в Azure HDInsight](./apache-spark-ipython-notebook-machine-learning.md)



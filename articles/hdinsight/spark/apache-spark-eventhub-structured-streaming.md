---
title: "Использование структурированной потоковой передачи Apache Spark с концентраторами событий в Azure HDInsight | Документация Майкрософт"
description: "Сведения о разработке примера приложения потоковой передачи Apache Spark для отправки потока данных в концентратор событий Azure и последующего получения этих событий в кластере HDInsight Spark с помощью приложения Scala."
keywords: "потоковая передача apache spark, потоковая передача spark, пример приложения spark, пример приложения потоковой передачи apache spark, пример концентратора событий azure, пример приложения spark"
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2017
ms.author: nitinme
ms.openlocfilehash: a542295e91a641289fa4261920a08eddbad6a217
ms.sourcegitcommit: be0d1aaed5c0bbd9224e2011165c5515bfa8306c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/01/2017
---
# <a name="apache-spark-structured-streaming-on-hdinsight-to-process-events-from-event-hubs"></a>Структурированная потоковая передача Apache Spark в HDInsight для обработки событий из концентраторов событий

Из этой статьи вы узнаете, как обрабатывать телеметрию в реальном времени с использованием структурированной потоковой передачи Spark. Для этого необходимо выполнить перечисленные ниже пошаговые действия:

1. На локальной рабочей станции скомпилируйте и запустите пример приложения "Создатель событий", создающего события для отправки концентраторам событий.
2. Для определения и запуска простого приложения структурированной потоковой передачи Spark используйте [оболочку Spark](apache-spark-shell.md).

## <a name="prerequisites"></a>Предварительные требования

* Подписка Azure. Ознакомьтесь с [бесплатной пробной версией Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

* Кластер Apache Spark в HDInsight. Инструкции см. в статье [Начало работы. Создание кластера Apache Spark в HDInsight на платформе Linux и выполнение интерактивных запросов с помощью SQL Spark](apache-spark-jupyter-spark-sql.md).

* Пространство имен концентраторов событий Azure. Дополнительные сведения см. в разделе [Создание концентратора событий Azure](apache-spark-eventhub-streaming.md#create-an-azure-event-hub).

* Комплект разработчика Oracle Java. Его можно установить [отсюда](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).

* Apache Maven. Скачать эту версию можно [здесь](https://maven.apache.org/download.cgi). Инструкции по установке Maven доступны [здесь](https://maven.apache.org/install.html).

## <a name="build-configure-and-run-the-event-producer"></a>Создание, настройка и запуск приложения "Создатель событий"
В этой задаче вы клонируете пример приложения, которое создает случайные события и отправляет их в настроенный концентратор событий. Пример этого приложения доступен на сайте GitHub по адресу [https://github.com/hdinsight/eventhubs-sample-event-producer](https://github.com/hdinsight/eventhubs-sample-event-producer).

1. Убедитесь, что у вас установлены инструменты git. Их можно загрузить со [станицы загрузок GIT](http://www.git-scm.com/downloads). 
2. Откройте командную строку или окно оболочки терминала и выполните следующую команду из выбранного каталога, чтобы клонировать проект.
        
        git clone https://github.com/hdinsight/eventhubs-sample-event-producer.git eventhubs-client-sample

3. При этом создается папка с именем eventhubs-client-sample. Перейдите в эту папку из командной строки или оболочки.
4. Запустите Maven для создания приложения с помощью следующей команды:

          mvn package

5. Из командной строки или оболочки перейдите в создаваемый целевой каталог, в котором содержится файл ``com-microsoft-azure-eventhubs-client-example-0.2.0.jar``.
6. Затем необходимо создать командную строку, чтобы запустить приложение "Создатель событий" для концентратора событий. Для этого замените значения в командной строке на следующие:

        java -cp com-microsoft-azure-eventhubs-client-example-0.2.0.jar com.microsoft.azure.eventhubs.client.example.EventhubsClientDriver --eventhubs-namespace "<namespace>" --eventhubs-name "hub1" --policy-name "RootManageSharedAccessKey" --policy-key "<policyKey>" --message-length 32 --thread-count 1 --message-count -1

7. Например, готовая команда будет выглядеть следующим образом:

        java -cp com-microsoft-azure-eventhubs-client-example-0.2.0.jar com.microsoft.azure.eventhubs.client.example.EventhubsClientDriver --eventhubs-namespace "sparkstreaming" --eventhubs-name "hub1" --policy-name "RootManageSharedAccessKey" --policy-key "2P1Q17Wd1rdLP1OZQYn6dD2S13Bb3nF3h2XZD9hvyyU=" --message-length 32 --thread-count 1 --message-count -1

8. Команда выполнится и если ваши настройки верны, через несколько секунд появятся выходные данные, связанные с событиями, отправляемыми в концентратор событий. Они будут аналогичны примеру, приведенному ниже:

        Map('policyKey -> 2P1Q17Wd1rdLP1OZQYn6dD2S13Bb3nF3h2XZD9hvyyU, 'eventhubsName -> hub1, 'policyName -> RootManageSharedAccessKey, 'eventhubsNamespace -> sparkstreaming, 'messageCount -> -1, 'messageLength -> 32, 'threadCount -> 1)
        Events per thread: -1 (-1 for unlimited)
        10 > Sun Jun 18 11:32:58 PDT 2017 > 1024 > 1024 > Sending event: ZZ93958saG5BUKbvUI9wHVmpuA2TrebS
        10 > Sun Jun 18 11:33:46 PDT 2017 > 2048 > 2048 > Sending event: RQorGRbTPp6U2wYzRSnZUlWEltRvTZ7R
        10 > Sun Jun 18 11:34:33 PDT 2017 > 3072 > 3072 > Sending event: 36Eoy2r8ptqibdlfCYSGgXe6ct4AyOX3
        10 > Sun Jun 18 11:35:19 PDT 2017 > 4096 > 4096 > Sending event: bPZma9V0CqOn6Hj9bhrrJT0bX2rbPSn3
        10 > Sun Jun 18 11:36:06 PDT 2017 > 5120 > 5120 > Sending event: H2TVD77HNTVyGsVcj76g0daVnYxN4Sqs

9. Продолжая выполнять действия, оставьте приложение "Создатель событий" запущенным.

## <a name="run-spark-shell-on-your-hdinsight-cluster"></a>Запуск оболочки Spark в кластере HDInsight
В этой задаче вы подключитесь к головному узлу кластера HDInsight по протоколу SSH, запустите консоль Spark и приложение потоковой передачи Spark, которое получает и обрабатывает события из концентраторов событий. 

К этому моменту ваш кластер HDInsight должен быть готов. В противном случае необходимо подождать, пока подготовка не завершится. После подготовки сделайте следующее:

1. Подключитесь к кластеру HDInsight по протоколу SSH. Указания см. в статье [Подключение к HDInsight (Hadoop) с помощью SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

5. Создаваемым приложениям требуется пакет концентраторов событий потоковой передачи Spark. Чтобы запустить оболочку Spark, которая должна автоматически получить эту зависимость из [центрального репозитория Maven](https://search.maven.org), убедитесь, что поддержка замены пакетов координируется с Maven следующим образом:

        spark-shell --packages "com.microsoft.azure:spark-streaming-eventhubs_2.11:2.1.0"

6. После загрузки оболочки Spark вы должны увидеть следующее:

        Welcome to
            ____              __
            / __/__  ___ _____/ /__
            _\ \/ _ \/ _ `/ __/  '_/
        /___/ .__/\_,_/_/ /_/\_\   version 2.1.0.2.6.0.10-29
            /_/
                
        Using Scala version 2.11.8 (OpenJDK 64-Bit Server VM, Java 1.8.0_131)
        Type in expressions to have them evaluated.
        Type :help for more information.

        scala> 

7. Скопируйте следующий фрагмент кода в текстовый редактор и измените его так, чтобы в нем были заданы ключ политики и пространство имен, подходящие для концентратора событий.

        val eventhubParameters = Map[String, String] (
            "eventhubs.policyname" -> "RootManageSharedAccessKey",
            "eventhubs.policykey" -> "<policyKey>",
            "eventhubs.namespace" -> "<namespace>",
            "eventhubs.name" -> "hub1",
            "eventhubs.partition.count" -> "2",
            "eventhubs.consumergroup" -> "$Default",
            "eventhubs.progressTrackingDir" -> "/eventhubs/progress",
            "eventhubs.sql.containsProperties" -> "true"
            )

8. Вставьте измененный фрагмент в ожидающую командную строку scala> и нажмите кнопку возврата. Должен отобразиться примерно такой результат:

        scala> val eventhubParameters = Map[String, String] (
            |       "eventhubs.policyname" -> "RootManageSharedAccessKey",
            |       "eventhubs.policykey" -> "2P1Q17Wd1rdLP1OZQYn6dD2S13Bb3nF3h2XZD9hvyyU",
            |       "eventhubs.namespace" -> "sparkstreaming",
            |       "eventhubs.name" -> "hub1",
            |       "eventhubs.partition.count" -> "2",
            |       "eventhubs.consumergroup" -> "$Default",
            |       "eventhubs.progressTrackingDir" -> "/eventhubs/progress",
            |       "eventhubs.sql.containsProperties" -> "true"
            |     )
        eventhubParameters: scala.collection.immutable.Map[String,String] = Map(eventhubs.sql.containsProperties -> true, eventhubs.name -> hub1, eventhubs.consumergroup -> $Default, eventhubs.partition.count -> 2, eventhubs.progressTrackingDir -> /eventhubs/progress, eventhubs.policykey -> 2P1Q17Wd1rdLP1OZQYn6dD2S13Bb3nF3h2XZD9hvyyU, eventhubs.namespace -> hdiz-docs-eventhubs, eventhubs.policyname -> RootManageSharedAccessKey)

9. Затем начните создавать структурированную потоковою передачу Spark, указав источник запроса. Вставьте следующий код в оболочку Spark и нажмите клавишу возврата.

        val inputStream = spark.readStream.
        format("eventhubs").
        options(eventhubParameters).
        load()

10. Должен отобразиться примерно такой результат:

        inputStream: org.apache.spark.sql.DataFrame = [body: binary, offset: bigint ... 5 more fields]

11. Затем создайте запрос, записывающий выходные данные в консоль. Для этого вставьте следующий код в окно оболочки Spark и нажмите кнопку возврата.

        val streamingQuery1 = inputStream.writeStream.
        outputMode("append").
        format("console").start().awaitTermination()

12. Вы должны увидеть, что некоторые пакеты начинаются с вывода, аналогичного следующему.

        -------------------------------------------
        Batch: 0
        -------------------------------------------
        [Stage 0:>                                                          (0 + 2) / 2]

13. За этим следуют выходные результаты обработки каждого микропакета событий. 

        -------------------------------------------
        Batch: 0
        -------------------------------------------
        17/06/18 18:57:39 WARN TaskSetManager: Stage 1 contains a task of very large size (419 KB). The maximum recommended task size is 100 KB.
        +--------------------+------+---------+------------+---------+------------+----------+
        |                body|offset|seqNumber|enqueuedTime|publisher|partitionKey|properties|
        +--------------------+------+---------+------------+---------+------------+----------+
        |[7B 22 74 65 6D 7...|     0|        0|  1497734887|     null|        null|     Map()|
        |[7B 22 74 65 6D 7...|   112|        1|  1497734887|     null|        null|     Map()|
        |[7B 22 74 65 6D 7...|   224|        2|  1497734887|     null|        null|     Map()|
        |[7B 22 74 65 6D 7...|   336|        3|  1497734887|     null|        null|     Map()|
        |[7B 22 74 65 6D 7...|   448|        4|  1497734887|     null|        null|     Map()|
        |[7B 22 74 65 6D 7...|   560|        5|  1497734887|     null|        null|     Map()|
        |[7B 22 74 65 6D 7...|   672|        6|  1497734887|     null|        null|     Map()|
        |[7B 22 74 65 6D 7...|   784|        7|  1497734888|     null|        null|     Map()|
        |[7B 22 74 65 6D 7...|   896|        8|  1497734888|     null|        null|     Map()|
        |[7B 22 74 65 6D 7...|  1008|        9|  1497734888|     null|        null|     Map()|
        |[7B 22 74 65 6D 7...|  1120|       10|  1497734888|     null|        null|     Map()|
        |[7B 22 74 65 6D 7...|  1232|       11|  1497734888|     null|        null|     Map()|
        |[7B 22 74 65 6D 7...|  1344|       12|  1497734888|     null|        null|     Map()|
        |[7B 22 74 65 6D 7...|  1456|       13|  1497734888|     null|        null|     Map()|
        |[7B 22 74 65 6D 7...|  1568|       14|  1497734888|     null|        null|     Map()|
        |[7B 22 74 65 6D 7...|  1680|       15|  1497734888|     null|        null|     Map()|
        |[7B 22 74 65 6D 7...|  1792|       16|  1497734888|     null|        null|     Map()|
        |[7B 22 74 65 6D 7...|  1904|       17|  1497734888|     null|        null|     Map()|
        |[7B 22 74 65 6D 7...|  2016|       18|  1497734889|     null|        null|     Map()|
        |[7B 22 74 65 6D 7...|  2128|       19|  1497734889|     null|        null|     Map()|
        +--------------------+------+---------+------------+---------+------------+----------+
        only showing top 20 rows

14. По мере поступления новых событий из приложения "Создатель событий" они обрабатываются запросом структурированный потоковой передачи.
15. Не забудьте удалить кластер HDInsight после выполнения этого примера.



## <a name="see-also"></a>См. также

* [Общие сведения о потоковой передаче Spark](apache-spark-streaming-overview.md)














---
title: "Создание приложений машинного обучения Apache Spark в Azure HDInsight | Документация Майкрософт"
description: "Пошаговые инструкции по созданию приложения машинного обучения Apache Spark в кластерах HDInsight Spark с помощью записной книжки Jupyter"
services: hdinsight
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: f584ca5e-abee-4b7c-ae58-2e45dfc56bf4
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2017
ms.author: jgao
ms.openlocfilehash: 22a3d220966fef77e131fbeb3ea46a1f81a9ada5
ms.sourcegitcommit: 562a537ed9b96c9116c504738414e5d8c0fd53b1
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/12/2018
---
# <a name="build-apache-spark-machine-learning-applications-on-azure-hdinsight"></a>Создание приложений машинного обучения Apache Spark в Azure HDInsight

Узнайте о том, как создать приложение машинного обучения Apache Spark с помощью кластера Spark в HDInsight. В этой статье показано, как использовать записную книжку Jupyter с кластером для создания и тестирования приложения. Приложение использует данные из примера файла HVAC.csv, который по умолчанию доступен на всех кластерах.

**Предварительные требования:**

Необходимо следующее:

* Кластер Apache Spark в HDInsight. Инструкции см. в статье [Начало работы. Создание кластера Apache Spark в HDInsight на платформе Linux и выполнение интерактивных запросов с помощью SQL Spark](apache-spark-jupyter-spark-sql.md). 

## <a name="data"></a>Общие сведения о наборе данных
Прежде чем приступать к созданию приложения, рассмотрим структуру данных, для которых создается приложение, и тип анализа, который мы будем использовать в отношении данных. 

В этой статье мы используем пример файла данных **HVAC.csv** , доступный в учетной записи хранения Azure, которую вы связали с кластером HDInsight. В этой учетной записи хранения файл находится в папке **\HdiSamples\HdiSamples\SensorSampleData\hvac**. Скачайте и откройте CSV-файл, чтобы получить моментальный снимок данных.  

![Моментальный снимок данных, используемых для примера машинного обучения Spark](./media/apache-spark-ipython-notebook-machine-learning/spark-machine-learning-understand-data.png "Моментальный снимок данных, используемых для примера машинного обучения Spark")

Это данные о целевой температуре и фактической температуре здания, в котором установлена система кондиционирования воздуха. Предположим, что в столбце **System** указан идентификатор системы, а в столбце **SystemAge** — срок эксплуатации системы кондиционирования в годах.

Эти сведения используются для прогнозирования того, будет ли температура здания выше или ниже относительно целевой температуры на основе идентификатора системы и ее возраста.

## <a name="app"></a>Создание приложения машинного обучения Spark с помощью Spark MLlib
В этом приложении мы используем конвейер машинного обучения Spark для выполнения классификации документов. В конвейере мы разбиваем документ на слова, преобразуем слова в вектор числовой функции и, наконец, создаем модель прогнозирования с помощью характеристического вектора и меток. Выполните следующие действия, чтобы создать приложение.

1. На начальной панели [портала Azure](https://portal.azure.com/)щелкните элемент кластера Spark (если он закреплен на начальной панели). Кроме того, вы можете перейти к кластеру, последовательно щелкнув **Просмотреть все** > **Кластеры HDInsight**.   
2. В колонке кластера Spark щелкните **Панель мониторинга кластера**, а затем выберите **Записная книжка Jupyter**. При появлении запроса введите учетные данные администратора для кластера.
   
   > [!NOTE]
   > Также можно открыть Jupyter Notebook для своего кластера, открыв следующий URL-адрес в браузере. Замените **CLUSTERNAME** именем кластера:
   > 
   > `https://CLUSTERNAME.azurehdinsight.net/jupyter`
   > 
   > 
3. Создайте новую записную книжку. Щелкните **Создать**, а затем выберите **PySpark**.
   
    ![Создание записной книжки Jupyter для примера машинного обучения Spark](./media/apache-spark-ipython-notebook-machine-learning/spark-machine-learning-create-notebook.png "Создание записной книжки Jupyter для примера машинного обучения Spark")
4. Будет создана и открыта записная книжка с именем Untitled.pynb. Щелкните имя записной книжки в верхней части страницы сверху и введите понятное имя.
   
    ![Указание имени записной книжки для примера машинного обучения Spark](./media/apache-spark-ipython-notebook-machine-learning/spark-machine-learning-notebook-name.png "Указание имени записной книжки для примера машинного обучения Spark")
5. Так как записная книжка была создана с помощью ядра PySpark, задавать контексты явно необязательно. Контексты Spark и Hive будут созданы автоматически при выполнении первой ячейки кода. Можно начать с импорта различных типов, необходимых для этого сценария. Вставьте следующий фрагмент кода в пустую ячейку и нажмите клавиши **SHIFT + ВВОД**. 
   
        from pyspark.ml import Pipeline
        from pyspark.ml.classification import LogisticRegression
        from pyspark.ml.feature import HashingTF, Tokenizer
        from pyspark.sql import Row
   
        import os
        import sys
        from pyspark.sql.types import *
   
        from pyspark.mllib.classification import LogisticRegressionWithSGD
        from pyspark.mllib.regression import LabeledPoint
        from numpy import array
6. Теперь необходимо загрузить данные (hvac.csv), проанализировать их и использовать эти данные для обучения модели. Для этого необходимо определить функцию, которая проверяет, превышает ли значение фактической температуры здания значение целевой температуры. Если фактическая температура больше, то здание горячее (значение **1.0**). Если фактическая температура меньше, то здание холодное (значение **0.0**). 
   
    Вставьте следующий фрагмент кода в пустую ячейку и нажмите клавиши **SHIFT + ВВОД**.

        # List the structure of data for better understanding. Because the data will be
        # loaded as an array, this structure makes it easy to understand what each element
        # in the array corresponds to

        # 0 Date
        # 1 Time
        # 2 TargetTemp
        # 3 ActualTemp
        # 4 System
        # 5 SystemAge
        # 6 BuildingID

        LabeledDocument = Row("BuildingID", "SystemInfo", "label")

        # Define a function that parses the raw CSV file and returns an object of type LabeledDocument

        def parseDocument(line):
            values = [str(x) for x in line.split(',')]
            if (values[3] > values[2]):
                hot = 1.0
            else:
                hot = 0.0        

            textValue = str(values[4]) + " " + str(values[5])

            return LabeledDocument((values[6]), textValue, hot)

        # Load the raw HVAC.csv file, parse it using the function
        data = sc.textFile("wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

        documents = data.filter(lambda s: "Date" not in s).map(parseDocument)
        training = documents.toDF()


1. Настройте конвейер машинного обучения Spark, который состоит из трех частей: логического анализатора, hashingTF и lr. Дополнительные сведения о возможностях конвейера и о том, как он работает, см. в статье <a href="http://spark.apache.org/docs/latest/ml-guide.html#how-it-works" target="_blank">Конвейер машинного обучения Spark</a>.
   
    Вставьте следующий фрагмент кода в пустую ячейку и нажмите клавиши **SHIFT + ВВОД**.
   
        tokenizer = Tokenizer(inputCol="SystemInfo", outputCol="words")
        hashingTF = HashingTF(inputCol=tokenizer.getOutputCol(), outputCol="features")
        lr = LogisticRegression(maxIter=10, regParam=0.01)
        pipeline = Pipeline(stages=[tokenizer, hashingTF, lr])
2. Впишите конвейер в документ для обучения. Вставьте следующий фрагмент кода в пустую ячейку и нажмите клавиши **SHIFT + ВВОД**.
   
        model = pipeline.fit(training)
3. Проверьте, чтобы в документе для обучения имелась контрольная точка хода выполнения для приложения. Вставьте следующий фрагмент кода в пустую ячейку и нажмите клавиши **SHIFT + ВВОД**.
   
        training.show()
   
    Результат должен быть аналогичен приведенному ниже:
   
        +----------+----------+-----+
        |BuildingID|SystemInfo|label|
        +----------+----------+-----+
        |         4|     13 20|  0.0|
        |        17|      3 20|  0.0|
        |        18|     17 20|  1.0|
        |        15|      2 23|  0.0|
        |         3|      16 9|  1.0|
        |         4|     13 28|  0.0|
        |         2|     12 24|  0.0|
        |        16|     20 26|  1.0|
        |         9|      16 9|  1.0|
        |        12|       6 5|  0.0|
        |        15|     10 17|  1.0|
        |         7|      2 11|  0.0|
        |        15|      14 2|  1.0|
        |         6|       3 2|  0.0|
        |        20|     19 22|  0.0|
        |         8|     19 11|  0.0|
        |         6|      15 7|  0.0|
        |        13|      12 5|  0.0|
        |         4|      8 22|  0.0|
        |         7|      17 5|  0.0|
        +----------+----------+-----+

    Вернитесь и проверьте выходные данные со сведениями в необработанном CSV-файле. Например, в первой строке CSV-файла содержатся следующие данные:

    ![Моментальный снимок выходных данных для примера машинного обучения Spark](./media/apache-spark-ipython-notebook-machine-learning/spark-machine-learning-output-data.png "Моментальный снимок выходных данных для примера машинного обучения Spark")

    Обратите внимание на то, насколько фактическая температура меньше целевой, что свидетельствует о том, что здание холодное. В выходных данных обучения видно, что значение в первой строке столбца **label** составляет **0,0**. Это означает, что в здании не тепло.

1. Подготовьте набор данных, в отношении которого необходимо выполнить обученную модель. Для этого мы передадим идентификатор системы и ее возраст (значения в столбце **SystemInfo** в выходных данных обучения). После этого модель предскажет, станет ли в здании с этими идентификатором системы и возрастом теплее (значение 1,0) или холоднее (значение 0,0).
   
   Вставьте следующий фрагмент кода в пустую ячейку и нажмите клавиши **SHIFT + ВВОД**.
   
       # SystemInfo here is a combination of system ID followed by system age
       Document = Row("id", "SystemInfo")
       test = sc.parallelize([(1L, "20 25"),
                     (2L, "4 15"),
                     (3L, "16 9"),
                     (4L, "9 22"),
                     (5L, "17 10"),
                     (6L, "7 22")]) \
           .map(lambda x: Document(*x)).toDF() 
2. Наконец, создайте прогнозы на основе тестовых данных. Вставьте следующий фрагмент кода в пустую ячейку и нажмите клавиши **SHIFT + ВВОД**.
   
        # Make predictions on test documents and print columns of interest
        prediction = model.transform(test)
        selected = prediction.select("SystemInfo", "prediction", "probability")
        for row in selected.collect():
            print row
3. Должен отобразиться результат, аналогичный приведенному ниже:
   
       Row(SystemInfo=u'20 25', prediction=1.0, probability=DenseVector([0.4999, 0.5001]))
       Row(SystemInfo=u'4 15', prediction=0.0, probability=DenseVector([0.5016, 0.4984]))
       Row(SystemInfo=u'16 9', prediction=1.0, probability=DenseVector([0.4785, 0.5215]))
       Row(SystemInfo=u'9 22', prediction=1.0, probability=DenseVector([0.4549, 0.5451]))
       Row(SystemInfo=u'17 10', prediction=1.0, probability=DenseVector([0.4925, 0.5075]))
       Row(SystemInfo=u'7 22', prediction=0.0, probability=DenseVector([0.5015, 0.4985]))
   
   В первой строке прогноза видно, что при использовании системы кондиционирования с идентификатором 20 и возрастом 25 лет здание будет горячим (**прогноз = 1.0**). Первое значение параметра DenseVector (0,49999) соответствует прогнозу 0,0, а второе значение (0,5001) — прогнозу 1,0. В выходных данных видно, что, даже если второе значение лишь незначительно выше, модель показывает **прогноз = 1.0**.
4. Завершив работу с приложением, следует закрыть записную книжку, чтобы освободить ресурсы. Для этого в записной книжке в меню **Файл** выберите пункт **Close and Halt** (Закрыть и остановить). Это завершит работу записной книжки и закроет ее.

## <a name="anaconda"></a>Использование библиотеки scikit-learn Anaconda для машинного обучения Spark
Кластеры Apache Spark в HDInsight включают библиотеки Anaconda. Они также включают библиотеку **scikit-learn** для машинного обучения. Кроме того, библиотека включает различные наборы данных, которые можно использовать для создания примеров приложений прямо в записной книжке Jupyter. Примеры использования библиотеки scikit-learn см. на странице: [http://scikit-learn.org/stable/auto_examples/index.html](http://scikit-learn.org/stable/auto_examples/index.html).

## <a name="seealso"></a>Дополнительные материалы
* [Обзор: Apache Spark в Azure HDInsight](apache-spark-overview.md)

### <a name="scenarios"></a>Сценарии
* [Использование Spark со средствами бизнес-аналитики. Выполнение интерактивного анализа данных с использованием Spark в HDInsight с помощью средств бизнес-аналитики](apache-spark-use-bi-tools.md)
* [Использование Spark с машинным обучением. Использование Spark в HDInsight для прогнозирования результатов контроля качества пищевых продуктов](apache-spark-machine-learning-mllib-ipython.md)
* [Потоковая передача Spark. Использование Spark в HDInsight для сборки приложений потоковой передачи данных в режиме реального времени](apache-spark-eventhub-streaming.md)
* [Анализ журнала веб-сайта с использованием Spark в HDInsight](apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Создание и запуск приложений
* [Создание автономного приложения с использованием Scala](apache-spark-create-standalone-application.md)
* [Удаленный запуск заданий с помощью Livy в кластере Spark](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Средства и расширения
* [Использование подключаемого модуля средств HDInsight для IntelliJ IDEA для создания и отправки приложений Spark Scala](apache-spark-intellij-tool-plugin.md)
* [Удаленная отладка приложений Spark в кластере HDInsight Spark Linux с помощью подключаемого модуля средств HDInsight для IntelliJ IDEA](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Использование записных книжек Zeppelin с кластером Spark в HDInsight](apache-spark-zeppelin-notebook.md)
* [Ядра, доступные для записной книжки Jupyter в кластере Spark в HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Использование внешних пакетов с записными книжками Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)
* [Установка записной книжки Jupyter на компьютере и ее подключение к кластеру Apache Spark в Azure HDInsight (предварительная версия)](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Управление ресурсами
* [Управление ресурсами кластера Apache Spark в Azure HDInsight](apache-spark-resource-manager.md)
* [Отслеживание и отладка заданий в кластере Apache Spark в HDInsight на платформе Linux](apache-spark-job-debugging.md)

[hdinsight-versions]: hdinsight-component-versioning.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md

[hdinsight-weblogs-sample]:../hadoop/apache-hive-analyze-website-log.md
[hdinsight-sensor-data-sample]:../hadoop/apache-hive-analyze-sensor-data.md

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-create-storageaccount]:../../storage/common/storage-create-storage-account.md

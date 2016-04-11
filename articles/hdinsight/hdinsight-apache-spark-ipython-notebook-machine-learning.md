<properties 
	pageTitle="Использование Apache Spark для создания приложений машинного обучения в HDInsight | Microsoft Azure" 
	description="Пошаговые инструкции по использованию записных книжек Apache Spark для создания приложений машинного обучения" 
	services="hdinsight" 
	documentationCenter="" 
	authors="nitinme" 
	manager="paulettm" 
	editor="cgronlun"
	tags="azure-portal"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/17/2016" 
	ms.author="nitinme"/>


# Создание приложений машинного обучения с помощью Apache Spark в Azure HDInsight (Linux)

Узнайте о том, как создать приложение машинного обучения приложение с помощью кластера Apache Spark в HDInsight. В этой статье показано, как использовать записную книжку Jupyter с кластером для создания и тестирования приложения. Приложение использует данные из примера файла HVAC.csv, который по умолчанию доступен на всех кластерах.

**Предварительные требования:**

Необходимо следующее:

- Подписка Azure. См. [Бесплатная пробная версия Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- Кластер Apache Spark в HDInsight на платформе Linux. Инструкции см. в разделе [Создание кластеров Apache Spark в Azure HDInsight](hdinsight-apache-spark-jupyter-spark-sql.md). 

##<a name="data"></a>Отображение данных

Прежде чем приступать к созданию приложения, рассмотрим структуру данных и тип анализа, который мы будем использовать в отношении данных.

В этой статье мы используем пример файла с данными **HVAC.csv**, который по умолчанию доступен на всех кластерах HDInsight по следующему пути: **\\HdiSamples\\HdiSamples\\SensorSampleData\\hvac**. Скачайте и откройте CSV-файл, чтобы получить моментальный снимок данных.

![Моментальный снимок данных системы кондиционирования](./media/hdinsight-apache-spark-ipython-notebook-machine-learning/hdispark.ml.show.data.png "Моментальный снимок данных системы кондиционирования")

Это данные о целевой температуре и фактической температуре здания, в котором установлена система кондиционирования воздуха. Предположим, что в столбце **System** указан идентификатор системы, а в столбце **SystemAge** — срок эксплуатации системы кондиционирования в годах.

Эти сведения используются для прогнозирования того, будет ли температура здания выше или ниже относительно целевой температуры на основе идентификатора системы и ее возраста.

##<a name="app"></a>Создание приложения машинного обучения с помощью Spark MLlib

В этом приложении мы используем конвейер машинного обучения Spark для выполнения классификации документов. В конвейере мы разбиваем документ на слова, преобразуем слова в вектор числовой функции и, наконец, создаем модель прогнозирования с помощью характеристического вектора и меток. Выполните следующие действия, чтобы создать приложение.

1. На начальной панели [портала Azure](https://portal.azure.com/) щелкните элемент кластера Spark (если он закреплен на начальной панели). Кроме того, вы можете перейти к кластеру, выбрав пункты **Просмотреть все** и **Кластеры HDInsight**.   

2. В колонке кластера Spark щелкните **Быстрые ссылки**, затем в колонке **Панель мониторинга кластера** выберите **Jupyter Notebook**. При появлении запроса введите учетные данные администратора для кластера.

	> [AZURE.NOTE] Также можно открыть Jupyter Notebook для своего кластера, открыв следующий URL-адрес в браузере. Замените __CLUSTERNAME__ именем кластера.
	>
	> `https://CLUSTERNAME.azurehdinsight.net/jupyter`

2. Создайте новую записную книжку. Щелкните **Создать**, а затем выберите **PySpark**.

	![Создание новой записной книжки Jupyter](./media/hdinsight-apache-spark-ipython-notebook-machine-learning/hdispark.note.jupyter.createnotebook.png "Создание новой записной книжки Jupyter")

3. Будет создана и открыта записная книжка с именем Untitled.pynb. Щелкните имя записной книжки в верхней части страницы сверху и введите понятное имя.

	![Указание имени для записной книжки](./media/hdinsight-apache-spark-ipython-notebook-machine-learning/hdispark.note.jupyter.notebook.name.png "Указание имени для записной книжки")

3. Так как записная книжка была создана с помощью ядра PySpark, задавать контексты явно необязательно. Контексты Spark, SQL и Hive будут созданы автоматически при выполнении первой ячейки кода. Можно начать с импорта различных типов, необходимых для этого сценария. Вставьте следующий фрагмент кода в пустую ячейку и нажмите клавиши **SHIFT + ВВОД**.

		from pyspark.ml import Pipeline
		from pyspark.ml.classification import LogisticRegression
		from pyspark.ml.feature import HashingTF, Tokenizer
		from pyspark.sql import Row, SQLContext
		
		import os
		import sys
		from pyspark.sql.types import *
		
		from pyspark.mllib.classification import LogisticRegressionWithSGD
		from pyspark.mllib.regression import LabeledPoint
		from numpy import array
		
		
	 
4. Теперь необходимо загрузить данные (hvac.csv), проанализировать их и использовать эти данные для обучения модели. Для этого необходимо определить функцию, которая проверяет, превышает ли значение фактической температуры здания значение целевой температуры. Если фактическая температура больше, то здание горячее (значение **1.0**). Если фактическая температура меньше, то здание холодное (значение **0.0**).

	Вставьте следующий фрагмент кода в пустую ячейку и нажмите клавиши **SHIFT + ВВОД**.

		
		# List the structure of data for better understanding. Becuase the data will be
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


5. Настройте конвейер машинного обучения Spark, который состоит из трех частей: логического анализатора, hashingTF и lr. Дополнительные сведения о возможностях конвейера и о том, как он работает, см. в статье <a href="http://spark.apache.org/docs/latest/ml-guide.html#how-it-works" target="_blank">Конвейер машинного обучения Spark</a>.

	Вставьте следующий фрагмент кода в пустую ячейку и нажмите клавиши **SHIFT + ВВОД**.

		tokenizer = Tokenizer(inputCol="SystemInfo", outputCol="words")
		hashingTF = HashingTF(inputCol=tokenizer.getOutputCol(), outputCol="features")
		lr = LogisticRegression(maxIter=10, regParam=0.01)
		pipeline = Pipeline(stages=[tokenizer, hashingTF, lr])

6. Впишите конвейер в документ для обучения. Вставьте следующий фрагмент кода в пустую ячейку и нажмите клавиши **SHIFT + ВВОД**.

		model = pipeline.fit(training)

7. Проверьте, чтобы в документе для обучения имелась контрольная точка хода выполнения для приложения. Вставьте следующий фрагмент кода в пустую ячейку и нажмите **SHIFT + ВВОД**.

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

	![Моментальный снимок данных системы кондиционирования](./media/hdinsight-apache-spark-ipython-notebook-machine-learning/hdispark.ml.show.data.first.row.png "Моментальный снимок данных системы кондиционирования")

	Обратите внимание на то, насколько фактическая температура меньше целевой, что свидетельствует о том, что здание холодное. В выходных данных обучения видно, что значение в первой строке столбца **label** составляет **0.0**. Это означает, что здание не горячее.

8.  Подготовьте набор данных, в отношении которого необходимо выполнить обученную модель. Для этого мы передадим идентификатор системы и ее возраст (значения в столбце **SystemInfo** в выходных данных обучения). После этого модель предскажет, станет ли здание с данными идентификатором системы и возрастом еще горячее (значение 1.0) или холоднее (значение 0.0).

	Вставьте следующий фрагмент кода в пустую ячейку и нажмите сочетание клавиш **SHIFT+ВВОД**.
		
		# SystemInfo here is a combination of system ID followed by system age
		Document = Row("id", "SystemInfo")
		test = sc.parallelize([(1L, "20 25"),
                      (2L, "4 15"),
                      (3L, "16 9"),
                      (4L, "9 22"),
                      (5L, "17 10"),
                      (6L, "7 22")]) \
    		.map(lambda x: Document(*x)).toDF() 

9. Наконец, создайте прогнозы на основе тестовых данных. Вставьте следующий фрагмент кода в пустую ячейку и нажмите **SHIFT + ВВОД**.

		# Make predictions on test documents and print columns of interest
		prediction = model.transform(test)
		selected = prediction.select("SystemInfo", "prediction", "probability")
		for row in selected.collect():
		    print row

10. Должен отобразиться результат, аналогичный приведенному ниже:

		Row(SystemInfo=u'20 25', prediction=1.0, probability=DenseVector([0.4999, 0.5001]))
		Row(SystemInfo=u'4 15', prediction=0.0, probability=DenseVector([0.5016, 0.4984]))
		Row(SystemInfo=u'16 9', prediction=1.0, probability=DenseVector([0.4785, 0.5215]))
		Row(SystemInfo=u'9 22', prediction=1.0, probability=DenseVector([0.4549, 0.5451]))
		Row(SystemInfo=u'17 10', prediction=1.0, probability=DenseVector([0.4925, 0.5075]))
		Row(SystemInfo=u'7 22', prediction=0.0, probability=DenseVector([0.5015, 0.4985]))

	В первой строке прогноза видно, что при использовании системы кондиционирования с идентификатором 20 и возрастом 25 лет здание будет горячим (**прогноз = 1.0**). Первое значение параметра DenseVector (0.49999) соответствует прогнозу 0,0, а второе значение (0.5001) — прогнозу 1.0. В выходных данных видно, что, даже если второе значение лишь незначительно выше, модель показывает **прогноз = 1.0**.

11. Завершив работу с приложением, следует закрыть записную книжку, чтобы освободить ресурсы. Для этого в записной книжке в меню **Файл** выберите пункт **Закрыть и остановить**. Это завершит работу записной книжки и закроет ее.
	  	   

##<a name="anaconda"></a>Использование библиотеки scikit-learn Anaconda для машинного обучения

Кластеры Apache Spark в HDInsight включают библиотеки Anaconda. Они также включают библиотеку **scikit-learn** для машинного обучения. Кроме того, библиотека включает различные наборы данных, которые можно использовать для создания примеров приложений прямо в записной книжке Jupyter. Примеры использования библиотеки scikit-learn см. на странице: [http://scikit-learn.org/stable/auto\_examples/index.html](http://scikit-learn.org/stable/auto_examples/index.html).

##<a name="seealso"></a>См. также:

* [Обзор: Apache Spark в Azure HDInsight](hdinsight-apache-spark-overview.md)

### Сценарии

* [Использование Spark со средствами бизнес-аналитики. Выполнение интерактивного анализа данных с использованием Spark в HDInsight с помощью средств бизнес-аналитики](hdinsight-apache-spark-use-bi-tools.md)

* [Использование Spark с машинным обучением. Использование Spark в HDInsight для прогнозирования результатов контроля качества пищевых продуктов](hdinsight-apache-spark-machine-learning-mllib-ipython.md)

* [Потоковая передача Spark. Использование Spark в HDInsight для сборки приложений потоковой передачи данных в режиме реального времени](hdinsight-apache-spark-eventhub-streaming.md)

* [Анализ журнала веб-сайта с использованием Spark в HDInsight](hdinsight-apache-spark-custom-library-website-log-analysis.md)

### Создание и запуск приложений

* [Создание автономного приложения с использованием Scala](hdinsight-apache-spark-create-standalone-application.md)

* [Удаленный запуск заданий с помощью Livy в кластере Spark](hdinsight-apache-spark-livy-rest-interface.md)

### Средства и расширения

* [Использование подключаемого модуля средств HDInsight для IntelliJ IDEA для создания и отправки приложений Spark Scala](hdinsight-apache-spark-intellij-tool-plugin.md)

* [Использование записных книжек Zeppelin с кластером Spark в HDInsight](hdinsight-apache-spark-use-zeppelin-notebook.md)

* [Ядра, доступные для записной книжки Jupyter в кластере Spark в HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md)

### Управление ресурсами

* [Управление ресурсами кластера Apache Spark в Azure HDInsight](hdinsight-apache-spark-resource-manager.md)


[hdinsight-versions]: hdinsight-component-versioning.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md

[hdinsight-weblogs-sample]: hdinsight-hive-analyze-website-log.md
[hdinsight-sensor-data-sample]: hdinsight-hive-analyze-sensor-data.md

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-management-portal]: https://manage.windowsazure.com/
[azure-create-storageaccount]: storage-create-storage-account.md

<!---HONumber=AcomDC_0330_2016-->
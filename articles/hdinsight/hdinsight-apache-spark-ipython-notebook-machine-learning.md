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
	ms.date="09/30/2015" 
	ms.author="nitinme"/>


# Создание приложений машинного обучения с помощью Apache Spark в Azure HDInsight

Узнайте о том, как создать приложение машинного обучения приложение с помощью кластера Apache Spark в HDInsight. В этой статье показано, как использовать записную книжку Jupyter с кластером для создания и тестирования приложения. Приложение использует данные из примера файла HVAC.csv, который по умолчанию доступен на всех кластерах.

**Предварительные требования:**

Необходимо следующее:

- Подписка Azure. См. [Бесплатная пробная версия Azure](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- Кластер Apache Spark. Инструкции см. в разделе [Подготовка кластеров Apache Spark в Azure HDInsight](hdinsight-apache-spark-provision-clusters.md). 

##<a name="data"></a>Отображение данных

Прежде чем приступать к созданию приложения, рассмотрим структуру данных и тип анализа, который мы будем использовать в отношении данных.

В этой статье мы используем пример файла с данными **HVAC.csv**, который по умолчанию доступен на всех кластерах HDInsight по следующему пути: **\\HdiSamples\\SensorSampleData\\hvac**. Скачайте и откройте CSV-файл, чтобы получить моментальный снимок данных.

![Моментальный снимок данных системы кондиционирования](./media/hdinsight-apache-spark-ipython-notebook-machine-learning/HDI.Spark.ML.Show.Data.png "Моментальный снимок данных системы кондиционирования")

Это данные о целевой температуре и фактической температуре здания, в котором установлена система кондиционирования воздуха. Предположим, что в столбце **System** указан идентификатор системы, а в столбце **SystemAge** — срок эксплуатации системы кондиционирования в годах.

Эти сведения используются для прогнозирования того, будет ли температура здания выше или ниже относительно целевой температуры на основе идентификатора системы и ее возраста.

##<a name="app"></a>Создание приложения машинного обучения с помощью Spark MLlib

1. На начальной панели [портала предварительной версии Azure](https://portal.azure.com/) щелкните элемент кластера Spark (если он закреплен на начальной панели). Можно также перейти к кластеру в разделе **Просмотреть все** > **Кластеры HDInsight**.   

2. В колонке кластера Spark щелкните **Быстрые ссылки**, затем в колонке **Панель мониторинга кластера** выберите **Jupyter Notebook**. При появлении запроса введите учетные данные администратора для кластера.

	> [AZURE.NOTE]Также можно открыть Jupyter Notebook для своего кластера, открыв следующий URL-адрес в браузере. Замените __CLUSTERNAME__ именем кластера.
	>
	> `https://CLUSTERNAME.azurehdinsight.net/jupyter`

2. Создайте новую записную книжку. Щелкните **Создать**, а затем выберите **Python 2**.

	![Создание новой записной книжки Jupyter](./media/hdinsight-apache-spark-ipython-notebook-machine-learning/HDI.Spark.Note.Jupyter.CreateNotebook.png "Создание новой записной книжки Jupyter")

3. Будет создана и открыта записная книжка с именем Untitled.pynb. Щелкните имя записной книжки в верхней части страницы сверху и введите понятное имя.

	![Указание имени для записной книжки](./media/hdinsight-apache-spark-ipython-notebook-machine-learning/HDI.Spark.Note.Jupyter.Notebook.Name.png "Указание имени для записной книжки")

3. Приступайте к созданию приложения машинного обучения. В этом приложении мы используем конвейер машинного обучения Spark для выполнения классификации документов. В конвейере мы разбиваем документ на слова, преобразуем слова в вектор числовой функции и, наконец, создаем модель прогнозирования с помощью характеристического вектора и меток.

	Чтобы приступить к созданию приложения, сначала импортируйте необходимые модули и назначьте ресурсы для приложения. Вставьте следующий фрагмент кода в пустую ячейку в новой записной книжке и нажмите сочетание клавиш **SHIFT + ВВОД**.


		from pyspark.ml import Pipeline
		from pyspark.ml.classification import LogisticRegression
		from pyspark.ml.feature import HashingTF, Tokenizer
		from pyspark.sql import Row, SQLContext
		
		import os
		import sys
		from pyspark import SparkConf
		from pyspark import SparkContext
		from pyspark.sql import SQLContext
		from pyspark.sql.types import *
		
		from pyspark.mllib.classification import LogisticRegressionWithSGD
		from pyspark.mllib.regression import LabeledPoint
		from numpy import array
		
		# Assign resources to the application
		conf = SparkConf()
		conf.setMaster('spark://headnodehost:7077')
		conf.setAppName('pysparkregression')
		conf.set("spark.cores.max", "4")
		conf.set("spark.executor.memory", "4g")
		
		sc = SparkContext(conf=conf)
		sqlContext = SQLContext(sc)

	При каждом запуске задания в Jupyter в заголовке окна веб-браузера будет отображаться состояние **(Busy)** (Занято), а также заголовок записной книжки. Кроме того, рядом с надписью **Python 2** в верхнем правом углу окна отобразится закрашенный кружок. После завершения задания этот значок изменится на кружок без заливки.

	 ![Состояние задания записной книжки Jupyter](./media/hdinsight-apache-spark-ipython-notebook-machine-learning/HDI.Spark.Jupyter.Job.Status.png "Состояние задания записной книжки Jupyter")
 
4. Теперь необходимо загрузить данные (hvac.csv), проанализировать их и использовать эти данные для обучения модели. Для этого необходимо определить функцию, которая проверяет, превышает ли значение фактической температуры здания значение целевой температуры. Если фактическая температура больше, то здание горячее (значение **1.0**). Если фактическая температура меньше, то здание холодное (значение **0.0**).

	Вставьте следующий фрагмент кода в пустую ячейку и нажмите **SHIFT + ВВОД**.

		
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
		data = sc.textFile("wasb:///HdiSamples/SensorSampleData/hvac/HVAC.csv")

		documents = data.filter(lambda s: "Date" not in s).map(parseDocument)
		training = documents.toDF()


5. Настройте конвейер машинного обучения Spark, который состоит из трех частей: логического анализатора, hashingTF и lr. Дополнительные сведения о возможностях конвейера и о том, как он работает, см. в статье <a href="http://spark.apache.org/docs/latest/ml-guide.html#how-it-works" target="_blank">Конвейер машинного обучения Spark</a>.

	Вставьте следующий фрагмент кода в пустую ячейку и нажмите сочетание клавиш **SHIFT+ВВОД**.

		tokenizer = Tokenizer(inputCol="SystemInfo", outputCol="words")
		hashingTF = HashingTF(inputCol=tokenizer.getOutputCol(), outputCol="features")
		lr = LogisticRegression(maxIter=10, regParam=0.01)
		pipeline = Pipeline(stages=[tokenizer, hashingTF, lr])

6. Впишите конвейер в документ для обучения. Вставьте следующий фрагмент кода в пустую ячейку и нажмите сочетание клавиш **SHIFT+ВВОД**.

		model = pipeline.fit(training)

7. Проверьте, чтобы в документе для обучения имелась контрольная точка хода выполнения для приложения. Вставьте следующий фрагмент кода в пустую ячейку и нажмите **SHIFT + ВВОД**.

		training.show()

	Результат должен быть аналогичен приведенному ниже:

		BuildingID SystemInfo label
		4          13 20      0.0  
		17         3 20       0.0  
		18         17 20      1.0  
		15         2 23       0.0  
		3          16 9       1.0  
		4          13 28      0.0  
		2          12 24      0.0  
		16         20 26      1.0  
		9          16 9       1.0  
		12         6 5        0.0  
		15         10 17      1.0  
		7          2 11       0.0  
		15         14 2       1.0  
		6          3 2        0.0  
		20         19 22      0.0  
		8          19 11      0.0  
		6          15 7       0.0  
		13         12 5       0.0  
		4          8 22       0.0  
		7          17 5       0.0

	Вернитесь и проверьте выходные данные со сведениями в необработанном CSV-файле. Например, в первой строке CSV-файла содержатся следующие данные:

	![Моментальный снимок данных системы кондиционирования](./media/hdinsight-apache-spark-ipython-notebook-machine-learning/HDI.Spark.ML.Show.Data.First.Row.png "Моментальный снимок данных системы кондиционирования")

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

	В первой строке прогноза видно, что при использовании системы кондиционирования с идентификатором 20 и возрастом 25 лет здание будет горячим (**prediction=1.0**). Первое значение параметра DenseVector (0.49999) соответствует прогнозу 0,0, а второе значение (0.5001) — прогнозу 1.0. В выходных данных видно, что, даже если второе значение лишь незначительно выше, модель показывает **prediction=1.0**.

11. Теперь можно выйти из записной книжки, перезапустив ядро. В меню в верхней части страницы щелкните элемент **Ядро**, выберите команду **Перезапустить** и снова нажмите кнопку **Перезапустить** в окне подтверждения действия.

	![Перезапуск ядра Jupyter](./media/hdinsight-apache-spark-ipython-notebook-machine-learning/HDI.Spark.Jupyter.Restart.Kernel.png "Перезапуск ядра Jupyter")
	  	   

##<a name="anaconda"></a>Использование библиотеки scikit-learn Anaconda для машинного обучения

Кластеры Apache Spark в HDInsight включают библиотеки Anaconda. Они также включают библиотеку **scikit-learn** для машинного обучения. Кроме того, библиотека включает различные наборы данных, которые можно использовать для создания примеров приложений прямо в записной книжке Jupyter. Примеры использования библиотеки scikit-learn см. на странице: [http://scikit-learn.org/stable/auto\_examples/index.html](http://scikit-learn.org/stable/auto_examples/index.html).

##<a name="seealso"></a>См. также:

* [Обзор: Apache Spark в Azure HDInsight](hdinsight-apache-spark-overview.md)
* [Подготовка Spark в кластере HDInsight](hdinsight-apache-spark-provision-clusters.md)
* [Выполнение интерактивного анализа данных с использованием Spark в HDInsight с помощью средств бизнес-аналитики](hdinsight-apache-spark-use-bi-tools.md)
* [Использование Spark в HDInsight для создания приложений потоковой передачи данных в режиме реального времени](hdinsight-apache-spark-csharp-apache-zeppelin-eventhub-streaming.md)
* [Управление ресурсами кластера Apache Spark в Azure HDInsight](hdinsight-apache-spark-resource-manager.md)


[hdinsight-versions]: ../hdinsight-component-versioning/

[hdinsight-upload-data]: ../hdinsight-upload-data/

[hdinsight-storage]: ../hdinsight-use-blob-storage/


[hdinsight-weblogs-sample]: ../hdinsight-hive-analyze-website-log/
[hdinsight-sensor-data-sample]: ../hdinsight-hive-analyze-sensor-data/

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-management-portal]: https://manage.windowsazure.com/
[azure-create-storageaccount]: ../storage-create-storage-account/

<!---HONumber=Oct15_HO2-->
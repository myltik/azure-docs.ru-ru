<properties
	pageTitle="Расширенное исследование и моделирование данных с помощью Spark | Microsoft Azure"
	description="ПОДЛЕЖИТ УТОЧНЕНИЮ."
	services="machine-learning"
	documentationCenter=""
	authors="bradsev,deguhath,gokuma"
	manager="paulettm"
	editor="cgronlun"  />

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/26/2016"
	ms.author="deguhath;bradsev" />

# Расширенное исследование и моделирование данных с помощью Spark 

[AZURE.INCLUDE [machine-learning-spark-modeling](../../includes/machine-learning-spark-modeling.md)]

В этом пошаговом руководстве рассматривается исследование и моделирование данных с применением двоичной классификации и регрессии, при этом используются перекрестная проверка и оптимизация гиперпараметров для обучения моделей на примере набора данных о поездках в такси по Нью-Йорку и тарифах за 2013 г. с помощью HDInsight Spark. Здесь также подробно описаны этапы [анализа и обработки данных](http://aka.ms/datascienceprocess) с использованием кластера HDInsight Spark по обработке данных и больших двоичных объектов Azure для хранения данных и моделей. В ходе этой процедуры данные из большого двоичного объекта службы хранилища Azure сначала исследуются и визуализируются, а затем подготавливаются для создания прогнозных моделей. Чтобы создать код решения и составить соответствующие графики, использовался язык Python. Эти модели создаются с помощью набора средств Spark MLlib для выполнения задач двоичной классификации и регрессии.

- Задача **двоичной классификации**: спрогнозировать, будут ли выплачены чаевые за поездку. 
- Задача **регрессии**: спрогнозировать сумму чаевых в зависимости от других признаков. 

Здесь также содержатся примеры кода, демонстрирующие процесс обучения, анализа и сохранения каждого типа модели. В этой статье частично рассматриваются те же базовые условия, что и в статье [Исследование и моделирование данных с помощью Spark](machine-learning-data-science-spark-data-exploration-modeling.md), но он называется "расширенным", потому что здесь также используется перекрестная проверка в сочетании с перебором гиперпараметров для обучения оптимально точных моделей классификации и регрессии.

**Перекрестная проверка** — это метод, который оценивает, насколько хорошо модель, обученная на известном наборе данных, будет обобщаться для прогнозирования характеристик наборов данных, на которых она не прошла обучение. Суть этого метода в том, что модель обучается на наборе известных данных, а затем точность ее прогнозов тестируется на независимом наборе данных. Здесь используется распространенная реализация, разделяющая набор данных на K сверток и затем обучающая модель путем циклического перебора по всем сверткам, кроме одной.

**Оптимизация гиперпараметров** — это задача выбора набора гиперпараметров для алгоритма обучения, обычно с целью оптимизации меры производительности алгоритма на независимом наборе данных. **Гиперпараметры** — это значения, которые должны быть указаны за пределами процедуры обучения модели. Предположения относительно этих значений могут повлиять на гибкость и точность моделей. Деревья принятия решений содержат гиперпараметры, такие как требуемая глубина и число листьев в дереве. Для методов опорных векторов (SVM) необходимо задать условие штрафа за неправильную классификацию.

Распространенный способ выполнения оптимизации гиперпараметров, который используется здесь, — поиск в сетке или **перебор параметров**. Он заключается в выполнении тщательного поиска по значениям указанного подмножества пространства гиперпараметров для алгоритма обучения. Перекрестная проверка может предоставить метрику производительности, чтобы отсортировать оптимальные результаты, выданные алгоритмом поиска в сетке. Использование перекрестной проверки с перебором гиперпараметров помогает ограничить такие проблемы, как лжевзаимосвязи модели с обучающими данными, чтобы модель сохраняла возможность применения к общему набору данных, из которого извлекаются данные для обучения.

Мы использовали такие модели, как логистическая и линейная регрессия, случайные леса и увеличивающиеся деревья принятия решений.

- [Линейная регрессия с применением SGD](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.regression.LinearRegressionWithSGD) — модель линейной регрессии, в которой используется метод стохастического градиента для оптимизации и масштабирование признаков для прогнозирования суммы чаевых. 
- [Логистическая регрессия с применением алгоритма LBFGS](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.classification.LogisticRegressionWithLBFGS) (или логит-регрессия) — регрессионная модель, которая используется для классификации данных, если зависимая переменная является категориальной. Алгоритм LBFGS — это широко используемый в машинном обучении квазиньютоновский алгоритм оптимизации, который представляет собой модифицированный метод Бройдена — Флетчера — Гольдфарба — Шанно (BFGS) с ограниченным использованием компьютерной памяти.
- [Случайные леса](http://spark.apache.org/docs/latest/mllib-ensembles.html#Random-Forests) — это совокупности деревьев принятия решений. Такие комитеты используются во избежание переобучения. Этот алгоритм применяется для задач классификации и регрессии. С его помощью можно обрабатывать категориальные признаки, расширять модель для выполнения задач мультиклассовой классификации, а также определять нелинейные зависимости и взаимодействия признаков. Этот метод не требует масштабирования признаков. Случайные леса — одна из самых эффективных моделей машинного обучения для задач классификации и регрессии.
- [Деревья с градиентным повышением](http://spark.apache.org/docs/latest/ml-classification-regression.html#gradient-boosted-trees-gbts) — это совокупности деревьев принятия решений. Этот метод предусматривает итеративное обучение деревьев принятия решений, что позволяет свести потери к минимуму. Он применяется для задач классификации и регрессии. С его помощью можно обрабатывать категориальные признаки, а также определять нелинейные зависимости и взаимодействия признаков. Этот метод не требует масштабирования признаков. Кроме того, его можно использовать для создания модели мультиклассовой классификации.

Примеры моделирования с использованием перекрестной проверки и перебора гиперпараметров показаны для задачи двоичной классификации. Более простые примеры (без перебора параметров) представлены в основном разделе о задачах регрессии. Однако в приложении также представлена проверка с использованием эластичной сети для линейной регрессии и перекрестной проверки с перебором параметров для регрессии случайного леса. **Эластичная сеть** — это метод регуляризованной регрессии для подгонки моделей линейной регрессии, которые линейно объединяют метрики L1 и L2 в качестве штрафов методов [lasso](https://en.wikipedia.org/wiki/Lasso%20%28statistics%29) и [ridge](https://en.wikipedia.org/wiki/Tikhonov_regularization).



>[AZURE.NOTE] Несмотря на то, что набор средств Spark MLlib предназначен для работы с большими наборами данных, для удобства мы будем использовать относительно небольшую выборку (приблизительно 30 МБ, 170 тыс. строк, примерно 0,1 % исходного набора данных о поездках в такси по Нью-Йорку), чтобы продемонстрировать возможности моделирования. Описанные в этой статье задачи можно эффективно выполнить в кластере HDInsight с 2 рабочими узлами (примерно за 10 мин). Тот же код (с незначительными изменениями) можно использовать для обработки больших наборов данных. Необходимо лишь внести соответствующие изменения для кэширования данных в памяти или изменения размера кластера.


## Предварительные требования

Чтобы приступить к выполнению шагов, описанных в этом руководстве, потребуется учетная запись Azure и кластер HDInsight Spark версии Spark 1.5.2 (HDI 3.3). Дополнительные сведения о требованиях, необходимых для выполнения задач, описание данных о поездках в такси по Нью-Йорку за 2013 г. и инструкции по выполнению кода из записной книжки Jupyter в кластере Spark см. в статье [Overview of Data Science using Spark on Azure HDInsight](machine-learning-data-science-spark-overview.md) (Обзор анализа и обработки данных с помощью Spark в Azure HDInsight). Записная книжка **machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb** с примерами кода, которые используются в этом разделе, доступна на портале [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/Spark/Python).


[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]


## Настройка места хранения, библиотек и контекста Spark

Spark может считывать данные и записывать их в хранилище BLOB-объектов Azure (также известное как WASB). Таким образом, данные из хранилища можно обрабатывать с помощью Spark и сохранять полученные данные в этом же хранилище.

Чтобы сохранить модели или файлы в хранилище BLOB-объектов, необходимо указать путь соответствующим образом. Контейнер по умолчанию, присоединенный к кластеру Spark, можно указать с помощью пути, который начинается с wasb///. Другие расположения начинаются с wasb://.

### Настройка путей каталога к месту хранения в хранилище BLOB-объектов

В следующем примере кода указывается расположение данных, которые необходимо считать, и путь для каталога хранилища модели, где будут сохранены выходные данные модели.

	# SET PATHS TO FILE LOCATIONS: DATA AND MODEL STORAGE

	# LOCATION OF TRAINING DATA
	taxi_train_file_loc = "wasb://mllibwalkthroughs@cdspsparksamples.blob.core.windows.net/Data/NYCTaxi/JoinedTaxiTripFare.Point1Pct.Train.tsv";

	
	# SET THE MODEL STORAGE DIRECTORY PATH 
	# NOTE THAT THE FINAL BACKSLASH IN THE PATH IS NEEDED.
	modelDir = "wasb:///user/remoteuser/NYCTaxi/Models/";

	# PRINT START TIME
	import datetime
	datetime.datetime.now()

**ВЫХОДНЫЕ ДАННЫЕ**

datetime.datetime(2016, 4, 18, 17, 36, 27, 832799)


### Импорт библиотек и настройка контекста Spark 

Чтобы импортировать библиотеки и настроить контекст Spark, используйте следующий код:

	# LOAD PYSPARK LIBRARIES
	import pyspark
	from pyspark import SparkConf
	from pyspark import SparkContext
	from pyspark.sql import SQLContext
	%matplotlib inline
	import matplotlib
	import matplotlib.pyplot as plt
	#matplotlib.style.use('ggplot')
	from pyspark.sql import Row
	from pyspark.sql.functions import UserDefinedFunction
	from pyspark.sql.types import *
	import atexit
	from numpy import array
	import numpy as np
	import datetime
	
	# SET SPARK CONTEXT
	sc = SparkContext(conf=SparkConf().setMaster('yarn-client'))
	sqlContext = SQLContext(sc)
	atexit.register(lambda: sc.stop())
	
	sc.defaultParallelism

**ВЫХОДНЫЕ ДАННЫЕ**

4\.

## Прием данных: 

В этом разделе содержится код, с помощью которого можно выполнить задачи, необходимые для приема выборки данных, которые нужно смоделировать. С помощью этого кода можно считать файл в формате TSV (выборку с примерно 0,1 % исходного набора данных поездок по Нью-Йорку), отформатировать и очистить данные, создать и кэшировать фрейм данных в памяти, а затем зарегистрировать его как временную таблицу в контексте SQL.

Чтобы начать анализ и обработку необходимых данных, требуется переместить их из внешних источников или систем, в которых они находятся, в среду исследования и моделирования данных. В нашем пошаговом руководстве — это среда Spark. Этот раздел содержит код, с помощью которого можно выполнить следующие задачи:

- прием выборки данных, которые нужно моделировать;
- считывание входного набора данных (TSV-файл);
- форматирование и очистка данных;
- создание и кэширование объектов (устойчивых распределенных наборов данных или фреймов данных) в памяти;
- регистрация данных в качестве временной таблицы в контексте SQL.

Ниже приведен код для приема данных.


	# RECORD START TIME
	timestart = datetime.datetime.now()
	
	# IMPORT FILE FROM PUBLIC BLOB
	taxi_train_file = sc.textFile(taxi_train_file_loc)
	
	# GET SCHEMA OF THE FILE FROM HEADER
	schema_string = taxi_train_file.first()
	fields = [StructField(field_name, StringType(), True) for field_name in schema_string.split('\t')]
	fields[7].dataType = IntegerType() #Pickup hour
	fields[8].dataType = IntegerType() # Pickup week
	fields[9].dataType = IntegerType() # Weekday
	fields[10].dataType = IntegerType() # Passenger count
	fields[11].dataType = FloatType() # Trip time in secs
	fields[12].dataType = FloatType() # Trip distance
	fields[19].dataType = FloatType() # Fare amount
	fields[20].dataType = FloatType() # Surcharge
	fields[21].dataType = FloatType() # Mta_tax
	fields[22].dataType = FloatType() # Tip amount
	fields[23].dataType = FloatType() # Tolls amount
	fields[24].dataType = FloatType() # Total amount
	fields[25].dataType = IntegerType() # Tipped or not
	fields[26].dataType = IntegerType() # Tip class
	taxi_schema = StructType(fields)
	
	# PARSE FIELDS AND CONVERT DATA TYPE FOR SOME FIELDS
	taxi_header = taxi_train_file.filter(lambda l: "medallion" in l)
	taxi_temp = taxi_train_file.subtract(taxi_header).map(lambda k: k.split("\t"))\
	        .map(lambda p: (p[0],p[1],p[2],p[3],p[4],p[5],p[6],int(p[7]),int(p[8]),int(p[9]),int(p[10]),
	                        float(p[11]),float(p[12]),p[13],p[14],p[15],p[16],p[17],p[18],float(p[19]),
	                        float(p[20]),float(p[21]),float(p[22]),float(p[23]),float(p[24]),int(p[25]),int(p[26])))
	
	    
	# CREATE A CLEANED DATA-FRAME BY DROPPING SOME UN-NECESSARY COLUMNS & FILTERING FOR UNDESIRED VALUES OR OUTLIERS
	taxi_df_train_cleaned = taxi_train_df.drop('medallion').drop('hack_license').drop('store_and_fwd_flag').drop('pickup_datetime')\
	    .drop('dropoff_datetime').drop('pickup_longitude').drop('pickup_latitude').drop('dropoff_latitude')\
	    .drop('dropoff_longitude').drop('tip_class').drop('total_amount').drop('tolls_amount').drop('mta_tax')\
	    .drop('direct_distance').drop('surcharge')\
	    .filter("passenger_count > 0 and passenger_count < 8 AND payment_type in ('CSH', 'CRD') AND tip_amount >= 0 AND tip_amount < 30 AND fare_amount >= 1 AND fare_amount < 150 AND trip_distance > 0 AND trip_distance < 100 AND trip_time_in_secs > 30 AND trip_time_in_secs < 7200" )
	
	# CACHE DATA-FRAME IN MEMORY & MATERIALIZE DF IN MEMORY
	taxi_df_train_cleaned.cache()
	taxi_df_train_cleaned.count()
	
	# REGISTER DATA-FRAME AS A TEMP-TABLE IN SQL-CONTEXT
	taxi_df_train_cleaned.registerTempTable("taxi_train")
	
	# PRINT HOW MUCH TIME IT TOOK TO RUN THE CELL
	timeend = datetime.datetime.now()
	timedelta = round((timeend-timestart).total_seconds(), 2) 
	print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 


**ВЫХОДНЫЕ ДАННЫЕ**

Время на выполнение кода выше: 17,73 с.


## Исследование и визуализация данных 

После помещения данных в Spark необходимо исследовать и визуализировать их, чтобы получить более глубокое представление. В этом разделе мы исследуем данные о поездках в такси с помощью SQL-запросов и составим графики целевых переменных и потенциальных признаков для визуального контроля. В частности, мы составим график частоты поездок в такси, частоты оставления чаевых и зависимости суммы чаевых от тарифа и типа платежа.

### Построение гистограммы количества пассажиров в выборке данных о поездках на такси

В коде ниже используется SQL-запрос для извлечения и преобразования выборки данных во фрейм данных Pandas.

	# PLOT FREQUENCY OF PASSENGER COUNTS IN TAXI TRIPS

	# SQL SQUERY
	sqlStatement = """
	    SELECT passenger_count, COUNT(*) as trip_counts 
	    FROM taxi_train 
	    WHERE passenger_count > 0 and passenger_count < 7
	    GROUP BY passenger_count 
	"""
	sqlResults = sqlContext.sql(sqlStatement)
	
	#CONVERT TO PANDAS DATA-FRAMES FOR PLOTTING IN PYTHON
	resultsPDDF = sqlResults.toPandas()
	
	# PLOT PASSENGER NUMBER VS. TRIP COUNTS
	x_labels = resultsPDDF['passenger_count'].values
	fig = resultsPDDF[['trip_counts']].plot(kind='bar', facecolor='lightblue')
	fig.set_xticklabels(x_labels)
	fig.set_title('Counts of trips by passenger count')
	fig.set_xlabel('Passenger counts')
	fig.set_ylabel('Trip counts')
	plt.show()

**ВЫХОДНЫЕ ДАННЫЕ**

![Частота поездок в зависимости от количества пассажиров](./media/machine-learning-data-science-spark-advanced-data-exploration-modeling/frequency-of-trips-by-passenger-count.png)


### Построение гистограммы суммы чаевых и ее зависимости от количества пассажиров и тарифа.

В коде ниже используется SQL-запрос для извлечения и преобразования выборки данных во фрейм данных Pandas.

	# RECORD START TIME
	timestart = datetime.datetime.now()
	
	# SQL SQUERY
	sqlStatement = """
	    SELECT fare_amount, passenger_count, tip_amount, tipped
	    FROM taxi_train 
	    WHERE passenger_count > 0 AND passenger_count < 7
	    AND fare_amount > 0 AND fare_amount < 200
	    AND payment_type in ('CSH', 'CRD')
	    AND tip_amount > 0 AND tip_amount < 25
	"""
	sqlResults = sqlContext.sql(sqlStatement)
	
	# CONVERT TO PANDAS DATA-FRAME FOR PLOTTING IN PYTHON
	resultsPDDF= sqlResults.toPandas()
	
	# TIP BY PAYMENT TYPE AND PASSENGER COUNT
	ax1 = resultsPDDF[['tip_amount']].plot(kind='hist', bins=25, facecolor='lightblue')
	ax1.set_title('Tip amount distribution')
	ax1.set_xlabel('Tip Amount ($)')
	ax1.set_ylabel('Counts')
	plt.suptitle('')
	plt.show()
	
	# TIP BY PASSENGER COUNT
	ax2 = resultsPDDF.boxplot(column=['tip_amount'], by=['passenger_count'])
	ax2.set_title('Tip amount ($) by Passenger count')
	ax2.set_xlabel('Passenger count')
	ax2.set_ylabel('Tip Amount ($)')
	plt.suptitle('')
	plt.show()
	
	# TIP AMOUNT BY FARE AMOUNT, POINTS ARE SCALED BY PASSENGER COUNT
	ax = resultsPDDF.plot(kind='scatter', x= 'fare_amount', y = 'tip_amount', c='blue', alpha = 0.10, s=5*(resultsPDDF.passenger_count))
	ax.set_title('Tip amount by Fare amount ($)')
	ax.set_xlabel('Fare Amount')
	ax.set_ylabel('Tip Amount')
	plt.axis([-2, 120, -2, 30])
	plt.show()
	
	# PRINT HOW MUCH TIME IT TOOK TO RUN THE CELL
	timeend = datetime.datetime.now()
	timedelta = round((timeend-timestart).total_seconds(), 2) 
	print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**Выходные данные:**

![Распределение суммы чаевых](./media/machine-learning-data-science-spark-advanced-data-exploration-modeling/tip-amount-distribution.png)

![Сумма чаевых в зависимости от количества пассажиров](./media/machine-learning-data-science-spark-advanced-data-exploration-modeling/tip-amount-by-passenger-count.png)

![Сумма чаевых в зависимости от тарифа](./media/machine-learning-data-science-spark-advanced-data-exploration-modeling/tip-amount-by-fare-amount.png)

Время на выполнение кода выше: 10,42 с.


## Проектирование признаков, преобразование и подготовка данных к моделированию

В этом разделе приведен пример кода для выполнения процедур по подготовке данных, которые будут использоваться в моделях машинного обучения. Здесь также показано, как выполнять следующие задачи:

- Создание нового признака путем группирования часов в периоды трафика
- Индексация и прямое кодирование категориальных признаков
- Создание объектов с помеченной вершиной в качестве ввода для функций машинного обучения
- создание случайной вложенной выборки данных и ее разделение на наборы для обучения и тестирования;
- масштабирование признаков;
- кэширование объектов в памяти.


### Создание нового признака путем группирования часов в периоды трафика

В следующем коде показано, как создать признак путем группирования часов в периоды трафика, а затем кэшировать итоговый фрейм данных в памяти. Если устойчивые распределенные наборы данных и фреймы данных используются постоянно, на их кэширование необходимо меньше времени. В этом пошаговом руководстве процесс кэширования имеющихся данных выполняется в несколько этапов.

	# CREATE FOUR BUCKETS FOR TRAFFIC TIMES
	sqlStatement = """
	    SELECT *,
	    CASE
	     WHEN (pickup_hour <= 6 OR pickup_hour >= 20) THEN "Night" 
	     WHEN (pickup_hour >= 7 AND pickup_hour <= 10) THEN "AMRush" 
	     WHEN (pickup_hour >= 11 AND pickup_hour <= 15) THEN "Afternoon"
	     WHEN (pickup_hour >= 16 AND pickup_hour <= 19) THEN "PMRush"
	    END as TrafficTimeBins
	    FROM taxi_train 
	"""
	taxi_df_train_with_newFeatures = sqlContext.sql(sqlStatement)
	
	# CACHE DATA-FRAME IN MEMORY & MATERIALIZE DF IN MEMORY
	taxi_df_train_with_newFeatures.cache()
	taxi_df_train_with_newFeatures.count()

**ВЫХОДНЫЕ ДАННЫЕ**

126 050.


### Индексация и прямое кодирование категориальных признаков

В этом разделе показано, как индексировать и кодировать категориальные признаки в качестве ввода для функций моделирования. Перед использованием в функциях моделирования и прогнозирования MLlib категориальные входные данные сначала необходимо проиндексировать или закодировать.

В зависимости от модели этот процесс происходит по-разному. Например, для логистической и линейной моделей регрессии требуется прямое кодирование, при котором признак с 3 категориями можно разделить на 3 столбца, в каждом из которых в зависимости от категории наблюдения содержатся значения 0 или 1. Для этого можно использовать функцию [OneHotEncoder](http://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.OneHotEncoder.html#sklearn.preprocessing.OneHotEncoder) в MLlib. Этот кодировщик сопоставляет столбец с индексами меток со столбцом двоичных векторов как минимум с одним отдельным значением. Благодаря этой кодировке алгоритмы, для которых необходимы признаки с числовыми значениями (например, логистическая регрессия), можно применять к категориальным признакам.

Ниже приведен пример кода, с помощью которого можно проиндексировать и закодировать категориальные признаки.


	# RECORD START TIME
	timestart = datetime.datetime.now()
	
	# LOAD PYSPARK LIBRARIES
	from pyspark.ml.feature import OneHotEncoder, StringIndexer, VectorAssembler, OneHotEncoder, VectorIndexer
	
	# INDEX AND ENCODE VENDOR_ID
	stringIndexer = StringIndexer(inputCol="vendor_id", outputCol="vendorIndex")
	model = stringIndexer.fit(taxi_df_train_with_newFeatures) # Input data-frame is the cleaned one from above
	indexed = model.transform(taxi_df_train_with_newFeatures)
	encoder = OneHotEncoder(dropLast=False, inputCol="vendorIndex", outputCol="vendorVec")
	encoded1 = encoder.transform(indexed)
	
	# INDEX AND ENCODE RATE_CODE
	stringIndexer = StringIndexer(inputCol="rate_code", outputCol="rateIndex")
	model = stringIndexer.fit(encoded1)
	indexed = model.transform(encoded1)
	encoder = OneHotEncoder(dropLast=False, inputCol="rateIndex", outputCol="rateVec")
	encoded2 = encoder.transform(indexed)
	
	# INDEX AND ENCODE PAYMENT_TYPE
	stringIndexer = StringIndexer(inputCol="payment_type", outputCol="paymentIndex")
	model = stringIndexer.fit(encoded2)
	indexed = model.transform(encoded2)
	encoder = OneHotEncoder(dropLast=False, inputCol="paymentIndex", outputCol="paymentVec")
	encoded3 = encoder.transform(indexed)
	
	# INDEX AND TRAFFIC TIME BINS
	stringIndexer = StringIndexer(inputCol="TrafficTimeBins", outputCol="TrafficTimeBinsIndex")
	model = stringIndexer.fit(encoded3)
	indexed = model.transform(encoded3)
	encoder = OneHotEncoder(dropLast=False, inputCol="TrafficTimeBinsIndex", outputCol="TrafficTimeBinsVec")
	encodedFinal = encoder.transform(indexed)
	
	# PRINT ELAPSED TIME
	timeend = datetime.datetime.now()
	timedelta = round((timeend-timestart).total_seconds(), 2) 
	print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 


**ВЫХОДНЫЕ ДАННЫЕ**

Время на выполнение кода выше: 1,22 с.


### Создание объектов с помеченной вершиной в качестве ввода для функций машинного обучения

В этом разделе содержится код, с помощью которого можно индексировать категориальные текстовые данные в тип данных с помеченной вершиной, а затем закодировать их. После этого данные можно использовать для обучения и проверки модели логистической регрессии MLlib и других моделей классификации. Объекты с помеченной вершиной отформатированы в устойчивые распределенные наборы данных, которые можно использовать в качестве входных для большинства алгоритмов машинного обучения в MLlib. Объект [с помеченной вершиной](https://spark.apache.org/docs/latest/mllib-data-types.html#labeled-point) — это разреженный или плотный локальный вектор, связанный с меткой или ответом.


	# FUNCTIONS FOR BINARY CLASSIFICATION

	# LOAD PYSPARK LIBRARIES
	from pyspark.mllib.regression import LabeledPoint
	from numpy import array

	# INDEXING CATEGORICAL TEXT FEATURES FOR INPUT INTO TREE-BASED MODELS
	def parseRowIndexingBinary(line):
	    features = np.array([line.paymentIndex, line.vendorIndex, line.rateIndex, line.pickup_hour, line.weekday,
	                         line.passenger_count, line.trip_time_in_secs, line.trip_distance, line.fare_amount])
	    labPt = LabeledPoint(line.tipped, features)
	    return  labPt
	
	# ONE-HOT ENCODING OF CATEGORICAL TEXT FEATURES FOR INPUT INTO LOGISTIC RERESSION MODELS
	def parseRowOneHotBinary(line):
	    features = np.concatenate((np.array([line.pickup_hour, line.weekday, line.passenger_count,
	                                        line.trip_time_in_secs, line.trip_distance, line.fare_amount]), 
	                               line.vendorVec.toArray(), line.rateVec.toArray(), line.paymentVec.toArray()), axis=0)
	    labPt = LabeledPoint(line.tipped, features)
	    return  labPt


	# FUNCTIONS FOR REGRESSION WITH TIP AMOUNT AS TARGET VARIABLE

	# ONE-HOT ENCODING OF CATEGORICAL TEXT FEATURES FOR INPUT INTO TREE-BASED MODELS
	def parseRowIndexingRegression(line):
	    features = np.array([line.paymentIndex, line.vendorIndex, line.rateIndex, line.TrafficTimeBinsIndex, 
	                         line.pickup_hour, line.weekday, line.passenger_count, line.trip_time_in_secs, 
	                         line.trip_distance, line.fare_amount])
	    labPt = LabeledPoint(line.tip_amount, features)
	    return  labPt
	
	# INDEXING CATEGORICAL TEXT FEATURES FOR INPUT INTO LINEAR REGRESSION MODELS
	def parseRowOneHotRegression(line):
	    features = np.concatenate((np.array([line.pickup_hour, line.weekday, line.passenger_count,
	                                        line.trip_time_in_secs, line.trip_distance, line.fare_amount]), 
	                                        line.vendorVec.toArray(), line.rateVec.toArray(), 
	                                        line.paymentVec.toArray(), line.TrafficTimeBinsVec.toArray()), axis=0)
	    labPt = LabeledPoint(line.tip_amount, features)
	    return  labPt



### Создание случайной вложенной выборки данных и ее разделение на наборы для обучения и тестирования

С помощью кода в этом разделе создается случайная выборка данных (25 % всех данных). Несмотря на то, что из-за небольшого размера используемого набора данных, этот шаг выполнять необязательно, мы все равно покажем, как создавать выборку. Это поможет вам в случае возникновения проблем. Если выборки большие, этот процесс может значительно сэкономить время обучения моделей. Затем мы разобьем выборку данных на наборы для обучения (75 %) и тестирования (25 %), которые будут использоваться для двоичной классификации и регрессии.


	# RECORD START TIME
	timestart = datetime.datetime.now()
	
	# SPECIFY SAMPLING AND SPLITTING FRACTIONS
	from pyspark.sql.functions import rand
	
	samplingFraction = 0.25;
	trainingFraction = 0.75; testingFraction = (1-trainingFraction);
	seed = 1234;
	encodedFinalSampled = encodedFinal.sample(False, samplingFraction, seed=seed)
	
	# SPLIT SAMPLED DATA-FRAME INTO TRAIN/TEST, WITH A RANDOM COLUMN ADDED FOR DOING CV (SHOWN LATER)
	# INCLUDE RAND COLUMN FOR CREATING CROSS-VALIDATION FOLDS
	dfTmpRand = encodedFinalSampled.select("*", rand(0).alias("rand"));
	trainData, testData = dfTmpRand.randomSplit([trainingFraction, testingFraction], seed=seed);
	
	# CACHE TRAIN AND TEST DATA
	trainData.cache()
	testData.cache()
	
	# FOR BINARY CLASSIFICATION TRAINING AND TESTING
	indexedTRAINbinary = trainData.map(parseRowIndexingBinary)
	indexedTESTbinary = testData.map(parseRowIndexingBinary)
	oneHotTRAINbinary = trainData.map(parseRowOneHotBinary)
	oneHotTESTbinary = testData.map(parseRowOneHotBinary)
	
	# FOR REGRESSION TRAINING AND TESTING
	indexedTRAINreg = trainData.map(parseRowIndexingRegression)
	indexedTESTreg = testData.map(parseRowIndexingRegression)
	oneHotTRAINreg = trainData.map(parseRowOneHotRegression)
	oneHotTESTreg = testData.map(parseRowOneHotRegression)
	
	# PRINT ELAPSED TIME
	timeend = datetime.datetime.now()
	timedelta = round((timeend-timestart).total_seconds(), 2) 
	print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**ВЫХОДНЫЕ ДАННЫЕ**

Время на выполнение кода выше: 0,4 с.


### Масштабирование признаков

Масштабирование признаков (или нормализация данных) гарантирует, что для признаков с широко распределенными значениями не задано высокое значение веса в целевой функции. В коде для масштабирования признаков используется функция [StandardScaler](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.feature.StandardScaler), которая позволяет масштабировать признаки в зависимости от изменений единицы. MLlib предоставляет функцию StandardScaler для выполнения линейной регрессии с применением метода стохастического градиента. Этот алгоритм широко используется для обучения других моделей машинного обучения (например, регуляризованной регрессии или метода опорных векторов).


>[AZURE.NOTE] Алгоритм LinearRegressionWithSGD чувствителен к масштабированию признаков.


Ниже приведен код для масштабирования переменных, пригодный для использования с регуляризованным линейным алгоритмом с применением стохастического градиента.

	# RECORD START TIME
	timestart = datetime.datetime.now()
	
	# LOAD PYSPARK LIBRARIES
	from pyspark.mllib.regression import LabeledPoint
	from pyspark.mllib.linalg import Vectors
	from pyspark.mllib.feature import StandardScaler, StandardScalerModel
	from pyspark.mllib.util import MLUtils
	
	# SCALE VARIABLES FOR REGULARIZED LINEAR SGD ALGORITHM
	label = oneHotTRAINreg.map(lambda x: x.label)
	features = oneHotTRAINreg.map(lambda x: x.features)
	scaler = StandardScaler(withMean=False, withStd=True).fit(features)
	dataTMP = label.zip(scaler.transform(features.map(lambda x: Vectors.dense(x.toArray()))))
	oneHotTRAINregScaled = dataTMP.map(lambda x: LabeledPoint(x[0], x[1]))
	
	label = oneHotTESTreg.map(lambda x: x.label)
	features = oneHotTESTreg.map(lambda x: x.features)
	scaler = StandardScaler(withMean=False, withStd=True).fit(features)
	dataTMP = label.zip(scaler.transform(features.map(lambda x: Vectors.dense(x.toArray()))))
	oneHotTESTregScaled = dataTMP.map(lambda x: LabeledPoint(x[0], x[1]))
	
	# PRINT ELAPSED TIME
	timeend = datetime.datetime.now()
	timedelta = round((timeend-timestart).total_seconds(), 2) 
	print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**ВЫХОДНЫЕ ДАННЫЕ**

Время на выполнение кода выше: 7,33 с.

### Кэширование объектов в памяти

Время на обучение и тестирование алгоритмов машинного обучения можно сократить, кэшировав объекты входного фрейма данных, которые использовались в качестве масштабируемых признаков, а также признаков для классификации и регрессии.

	# RECORD START TIME
	timestart = datetime.datetime.now()
	
	# FOR BINARY CLASSIFICATION TRAINING AND TESTING
	indexedTRAINbinary.cache()
	indexedTESTbinary.cache()
	oneHotTRAINbinary.cache()
	oneHotTESTbinary.cache()
	
	# FOR REGRESSION TRAINING AND TESTING
	indexedTRAINreg.cache()
	indexedTESTreg.cache()
	oneHotTRAINreg.cache()
	oneHotTESTreg.cache()
	
	# SCALED FEATURES
	oneHotTRAINregScaled.cache()
	oneHotTESTregScaled.cache()
	
	# PRINT ELAPSED TIME
	timeend = datetime.datetime.now()
	timedelta = round((timeend-timestart).total_seconds(), 2) 
	print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**Выходные данные:**

Время на выполнение кода выше: 0,11 с.


## Прогнозирование вероятности выплаты чаевых за поездку с помощью моделей бинарной классификации

В этом разделе показано, как с помощью трех моделей для выполнения задач двоичной классификации спрогнозировать, будут ли оставлены чаевые за поездку в такси. Мы используем следующие модели:

- Логистическая регрессия 
- Случайный лес
- увеличивающиеся деревья принятия решений.

Процесс создания модели состоит из следующих этапов:

1. **Обучение модели** с использованием данных с одним набором параметров.
2. **Оценка модели** на основе набора тестовых данных с метриками.
3. **Сохранение модели** в большом двоичном объекте для последующего использования.

Мы покажем, как выполнить перекрестную проверку с перебором параметров двумя способами:

1. С использованием **универсального** пользовательского кода, который можно применять к любому алгоритму в MLlib и к любым наборам параметров в алгоритме. 
1. С использованием **функции конвейера pySpark CrossValidator**. Обратите внимание, что, несмотря на подтвержденное нашим опытом удобство, функция CrossValidator имеет несколько ограничений для Spark 1.5.0: 

	- Модели конвейера не могут быть сохранены для последующей обработки.
	- Не может использоваться для каждого параметра в модели.
	- Не может использоваться для каждого алгоритма MLlib.


### Универсальная перекрестная проверка и перебор гиперпараметров, используемые с алгоритмом логистической регрессии для двоичной классификации

Приведенный в этом разделе код предназначен для обучения, анализа и сохранения модели логистической регрессии с применением [LBFGS](https://en.wikipedia.org/wiki/Broyden%E2%80%93Fletcher%E2%80%93Goldfarb%E2%80%93Shanno_algorithm), с помощью которой можно спрогнозировать, будут ли выплачены чаевые за поездку в такси по Нью-Йорку, на основе набора данных и тарифов. Модель обучена с использованием перекрестной проверки и перебора гиперпараметров, реализованных в пользовательском коде, который можно применить к любому из алгоритмов обучения в MLlib.


>[AZURE.NOTE] Выполнение такого пользовательского кода перекрестной проверки может занять несколько минут.


	# LOGISTIC REGRESSION CLASSIFICATION WITH CV AND HYPERPARAMETER SWEEPING

	# RECORD START TIME
	timestart = datetime.datetime.now()
	
	from pyspark.mllib.classification import LogisticRegressionWithLBFGS 
	from pyspark.mllib.evaluation import BinaryClassificationMetrics
	
	# CREATE PARAMETER GRID FOR LOGISTIC REGRESSION PARAMETER SWEEP
	from sklearn.grid_search import ParameterGrid
	grid = [{'regParam': [0.01, 0.1], 'iterations': [5, 10], 'regType': ["l1", "l2"], 'tolerance': [1e-3, 1e-4]}]
	paramGrid = list(ParameterGrid(grid))
	numModels = len(paramGrid)
	
	# SET NUM FOLDS AND NUM PARAMETER SETS TO SWEEP ON
	nFolds = 3;
	h = 1.0 / nFolds;
	metricSum = np.zeros(numModels);
	
	# BEGIN CV WITH PARAMETER SWEEP
	for i in range(nFolds):
	    # Create training and x-validation sets
	    validateLB = i * h
	    validateUB = (i + 1) * h
	    condition = (trainData["rand"] >= validateLB) & (trainData["rand"] < validateUB)
	    validation = trainData.filter(condition)
	    # Create labeled points from data-frames
	    if i > 0:
	        trainCVLabPt.unpersist()
	        validationLabPt.unpersist()
	    trainCV = trainData.filter(~condition)
	    trainCVLabPt = trainCV.map(parseRowOneHotBinary)
	    trainCVLabPt.cache()
	    validationLabPt = validation.map(parseRowOneHotBinary)
	    validationLabPt.cache()
	    # For parameter sets compute metrics from x-validation
	    for j in range(numModels):
	        regt = paramGrid[j]['regType']
	        regp = paramGrid[j]['regParam']
	        iters = paramGrid[j]['iterations']
	        tol = paramGrid[j]['tolerance']
	        # Train logistic regression model with hypermarameter set
	        model = LogisticRegressionWithLBFGS.train(trainCVLabPt, regType=regt, iterations=iters,  
	                                                  regParam=regp, tolerance = tol, intercept=True)
	        predictionAndLabels = validationLabPt.map(lambda lp: (float(model.predict(lp.features)), lp.label))
	        # Use ROC-AUC as accuracy metrics
	        validMetrics = BinaryClassificationMetrics(predictionAndLabels)
	        metric = validMetrics.areaUnderROC
	        metricSum[j] += metric
	
	avgAcc = metricSum / nFolds;
	bestParam = paramGrid[np.argmax(avgAcc)];
	
	# UNPERSIST OBJECTS
	trainCVLabPt.unpersist()
	validationLabPt.unpersist()
	    
	# TRAIN ON FULL TRAIING SET USING BEST PARAMETERS FROM CV/PARAMETER SWEEP
	logitBest = LogisticRegressionWithLBFGS.train(oneHotTRAINbinary, regType=bestParam['regType'], 
	                                              iterations=bestParam['iterations'], 
	                                              regParam=bestParam['regParam'], tolerance = bestParam['tolerance'], 
	                                              intercept=True)
	
	timeend = datetime.datetime.now()
	timedelta = round((timeend-timestart).total_seconds(), 2) 
	print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 


**ВЫХОДНЫЕ ДАННЫЕ**

Время на выполнение кода выше: 160,47 с.


**Оценка модели двоичной классификации со стандартными метриками и построение кривой ROC**

Код в этом разделе показывает, как оценить модель логистической регрессии по тестовому набору данных, включая построение кривой ROC.


	# RECORD START TIME
	timestart = datetime.datetime.now()
	
	# LOAD PYSPARK LIBRARIES
	from sklearn.metrics import roc_curve,auc
	from pyspark.mllib.evaluation import BinaryClassificationMetrics
	from pyspark.mllib.evaluation import MulticlassMetrics
	
	# PREDICT ON TEST DATA WITH BEST/FINAL MODEL
	predictionAndLabels = oneHotTESTbinary.map(lambda lp: (float(logitBest.predict(lp.features)), lp.label))
	
	# INSTANTIATE METRICS OBJECT
	metrics = BinaryClassificationMetrics(predictionAndLabels)

	# AREA UNDER PRECISION-RECALL CURVE
	print("Area under PR = %s" % metrics.areaUnderPR)

	# AREA UNDER ROC CURVE
	print("Area under ROC = %s" % metrics.areaUnderROC)
	metrics = MulticlassMetrics(predictionAndLabels)

	# OVERALL STATISTICS
	precision = metrics.precision()
	recall = metrics.recall()
	f1Score = metrics.fMeasure()
	print("Summary Stats")
	print("Precision = %s" % precision)
	print("Recall = %s" % recall)
	print("F1 Score = %s" % f1Score)
	
	
	# CREATE A PANDAS DATA-FRAME AND PLOT ROC-CURVE, FROM PREDICTED PROBS AND LABELS                                     
	logitBest.clearThreshold() # This clears threshold for classification (0.5) and outputs probabilities
	predictionAndLabels = oneHotTESTbinary.map(lambda lp: (float(logitBest.predict(lp.features)), lp.label))
	predictionAndLabelsDF = predictionAndLabels.toDF()
	test_predictions = predictionAndLabelsDF.toPandas()
	predictions_pddf = test_predictions.rename(columns={'_1': 'probability', '_2': 'label'})
	
	prob = predictions_pddf["probability"] 
	fpr, tpr, thresholds = roc_curve(predictions_pddf['label'], prob, pos_label=1);
	roc_auc = auc(fpr, tpr)
	
	# PLOT ROC CURVE
	plt.figure(figsize=(5,5))
	plt.plot(fpr, tpr, label='ROC curve (area = %0.2f)' % roc_auc)
	plt.plot([0, 1], [0, 1], 'k--')
	plt.xlim([0.0, 1.0])
	plt.ylim([0.0, 1.05])
	plt.xlabel('False Positive Rate')
	plt.ylabel('True Positive Rate')
	plt.title('ROC Curve')
	plt.legend(loc="lower right")
	plt.show()
	
	# PRINT ELAPSED TIME
	timeend = datetime.datetime.now()
	timedelta = round((timeend-timestart).total_seconds(), 2) 
	print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**ВЫХОДНЫЕ ДАННЫЕ**

Area under PR = 0.985319161941

Area under ROC = 0.983511076103

Summary Stats

Precision = 0.984187223276

Recall = 0.984187223276

F1 Score = 0.984187223276

![Кривая ROC логистической регрессии для универсального подхода](./media/machine-learning-data-science-spark-advanced-data-exploration-modeling/logistic-regression-roc-curve.png)

Время на выполнение кода выше: 5,02 с.


**Сохранение модели в большом двоичном объекте для последующего использования**

Код в этом разделе показывает, как сохранить модель логистической регрессии для использования.

	# RECORD START TIME
	timestart = datetime.datetime.now()
	
	# LOAD PYSPARK LIBRARIES
	from pyspark.mllib.classification import LogisticRegressionModel
	
	# PERSIST MODEL
	datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
	logisticregressionfilename = "LogisticRegressionWithLBFGS_" + datestamp;
	dirfilename = modelDir + logisticregressionfilename;
	
	logitBest.save(sc, dirfilename);
	
	# PRINT ELAPSED TIME
	timeend = datetime.datetime.now()
	timedelta = round((timeend-timestart).total_seconds(), 2) 
	print "Time taken to execute above cell: " + str(timedelta) + " seconds";


**ВЫХОДНЫЕ ДАННЫЕ**

Время на выполнение кода выше: 9,96 с.


### Использование функции конвейера MLlib CrossValidator с моделью LogisticRegression (эластичная регрессия)

Приведенный в этом разделе код предназначен для обучения, анализа и сохранения модели логистической регрессии с применением [LBFGS](https://en.wikipedia.org/wiki/Broyden%E2%80%93Fletcher%E2%80%93Goldfarb%E2%80%93Shanno_algorithm), с помощью которой можно спрогнозировать, будут ли выплачены чаевые за поездку в такси по Нью-Йорку, на основе набора данных и тарифов. Модель обучена с использованием перекрестной проверки и перебора гиперпараметров, реализованных с помощью функции конвейера MLlib CrossValidator для перекрестной проверки с перебором гиперпараметров.


>[AZURE.NOTE] Выполнение такого кода перекрестной проверки MLlib может занять несколько минут.


	# RECORD START TIME
	timestart = datetime.datetime.now()
	
	# LOAD PYSPARK LIBRARIES
	from pyspark.ml.classification import LogisticRegression
	from pyspark.ml import Pipeline
	from pyspark.ml.evaluation import BinaryClassificationEvaluator
	from pyspark.ml.tuning import CrossValidator, ParamGridBuilder
	from sklearn.metrics import roc_curve,auc
	
	# DEFINE ALGORITHM / MODEL
	lr = LogisticRegression()
	
	# DEFINE GRID PARAMETERS
	paramGrid = ParamGridBuilder().addGrid(lr.regParam, (0.01, 0.1))\
	                              .addGrid(lr.maxIter, (5, 10))\
	                              .addGrid(lr.tol, (1e-4, 1e-5))\
	                              .addGrid(lr.elasticNetParam, (0.25,0.75))\
	                              .build()
	
	# DEFINE CV WITH PARAMETER SWEEP
	cv = CrossValidator(estimator= lr,
	                    estimatorParamMaps=paramGrid,
	                    evaluator=BinaryClassificationEvaluator(),
	                    numFolds=3)
	
	# CONVERT TO DATA-FRAME: THIS WILL NOT RUN ON RDDs
	trainDataFrame = sqlContext.createDataFrame(oneHotTRAINbinary, ["features", "label"])
	
	# TRAIN WITH CROSS-VALIDATION
	cv_model = cv.fit(trainDataFrame)
	

	## PREDICT AND EVALUATE ON TEST DATA-SET

	# USE TEST DATASET FOR PREDICTION
	testDataFrame = sqlContext.createDataFrame(oneHotTESTbinary, ["features", "label"])
	test_predictions = cv_model.transform(testDataFrame)
	
	# CONVERT RTO PANDAS DATA-FRAME FOR CALCULATING AND PLOTTING ROC CURVE
	predictions_pddf = test_predictions.toPandas()
	predictions_pddf.dtypes
	prob = [x[1] for x in predictions_pddf["probability"]]
	fpr, tpr, thresholds = roc_curve(predictions_pddf['label'], prob, pos_label=1);
	roc_auc = auc(fpr, tpr)
	
	# PLOT ROC CURVE
	plt.figure(figsize=(5,5))
	plt.plot(fpr, tpr, label='ROC curve (area = %0.2f)' % roc_auc)
	plt.plot([0, 1], [0, 1], 'k--')
	plt.xlim([0.0, 1.0])
	plt.ylim([0.0, 1.05])
	plt.xlabel('False Positive Rate')
	plt.ylabel('True Positive Rate')
	plt.title('ROC Curve')
	plt.legend(loc="lower right")
	plt.show()
	
	# PRINT ELAPSED TIME
	timeend = datetime.datetime.now()
	timedelta = round((timeend-timestart).total_seconds(), 2) 
	print "Time taken to execute above cell: " + str(timedelta) + " seconds";

**ВЫХОДНЫЕ ДАННЫЕ**

![Кривая ROC логистической регрессии с использованием MLlib CrossValidator](./media/machine-learning-data-science-spark-advanced-data-exploration-modeling/mllib-crossvalidator-roc-curve.png)

Время на выполнение кода выше: 118,25 с.


### Классификация случайного леса

Приведенный в этом разделе код предназначен для обучения, анализа и сохранения регрессии случайного леса, с помощью которой можно спрогнозировать, будут ли выплачены чаевые за поездку в такси по Нью-Йорку, на основе набора данных и тарифов.


	# RECORD START TIME
	timestart = datetime.datetime.now()
	
	# LOAD PYSPARK LIBRARIES
	from pyspark.mllib.tree import RandomForest, RandomForestModel
	from pyspark.mllib.util import MLUtils
	from pyspark.mllib.evaluation import BinaryClassificationMetrics
	from pyspark.mllib.evaluation import MulticlassMetrics
	
	# SPECIFY NUMBER OF CATEGORIES FOR CATEGORICAL FEATURES. FEATURE #0 HAS 2 CATEGORIES, FEATURE #2 HAS 2 CATEGORIES, AND SO ON
	categoricalFeaturesInfo={0:2, 1:2, 2:6, 3:4}
	
	# TRAIN RANDOMFOREST MODEL
	rfModel = RandomForest.trainClassifier(indexedTRAINbinary, numClasses=2, 
	                                       categoricalFeaturesInfo=categoricalFeaturesInfo,
	                                       numTrees=25, featureSubsetStrategy="auto",
	                                       impurity='gini', maxDepth=5, maxBins=32)
	## UN-COMMENT IF YOU WANT TO PRING TREES
	#print('Learned classification forest model:')
	#print(rfModel.toDebugString())
	
	# PREDICT ON TEST DATA AND EVALUATE
	predictions = rfModel.predict(indexedTESTbinary.map(lambda x: x.features))
	predictionAndLabels = indexedTESTbinary.map(lambda lp: lp.label).zip(predictions)
	
	# AREA UNDER ROC CURVE
	metrics = BinaryClassificationMetrics(predictionAndLabels)
	print("Area under ROC = %s" % metrics.areaUnderROC)
	
	# PERSIST MODEL IN BLOB
	datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
	rfclassificationfilename = "RandomForestClassification_" + datestamp;
	dirfilename = modelDir + rfclassificationfilename;
	
	rfModel.save(sc, dirfilename);
	
	# PRINT ELAPSED TIME
	timeend = datetime.datetime.now()
	timedelta = round((timeend-timestart).total_seconds(), 2) 
	print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 


**ВЫХОДНЫЕ ДАННЫЕ**

Area under ROC = 0.985240932843

Время на выполнение кода выше: 22,9 с.


### Классификация с применением модели увеличивающихся деревьев принятия решений

Приведенный в этом разделе код предназначен для обучения, анализа и сохранения модели увеличивающихся деревьев принятия решений, с помощью которой можно спрогнозировать, будут ли выплачены чаевые за поездку в такси по Нью-Йорку на основе набора данных и тарифов.


	# RECORD START TIME
	timestart = datetime.datetime.now()
	
	# LOAD PYSPARK LIBRARIES
	from pyspark.mllib.tree import GradientBoostedTrees, GradientBoostedTreesModel
	
	# SPECIFY NUMBER OF CATEGORIES FOR CATEGORICAL FEATURES. FEATURE #0 HAS 2 CATEGORIES, FEATURE #2 HAS 2 CATEGORIES, AND SO ON
	categoricalFeaturesInfo={0:2, 1:2, 2:6, 3:4}
	
	gbtModel = GradientBoostedTrees.trainClassifier(indexedTRAINbinary, categoricalFeaturesInfo=categoricalFeaturesInfo,
	                                                numIterations=10)
	## UNCOMMENT IF YOU WANT TO PRINT TREE DETAILS
	#print('Learned classification GBT model:')
	#print(bgtModel.toDebugString())
	
	# PREDICT ON TEST DATA AND EVALUATE
	predictions = gbtModel.predict(indexedTESTbinary.map(lambda x: x.features))
	predictionAndLabels = indexedTESTbinary.map(lambda lp: lp.label).zip(predictions)
	
	# Area under ROC curve
	metrics = BinaryClassificationMetrics(predictionAndLabels)
	print("Area under ROC = %s" % metrics.areaUnderROC)
	
	# PERSIST MODEL IN A BLOB
	datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
	btclassificationfilename = "GradientBoostingTreeClassification_" + datestamp;
	dirfilename = modelDir + btclassificationfilename;
	
	gbtModel.save(sc, dirfilename)
	
	# PRINT ELAPSED TIME
	timeend = datetime.datetime.now()
	timedelta = round((timeend-timestart).total_seconds(), 2) 
	print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**ВЫХОДНЫЕ ДАННЫЕ**

Area under ROC = 0.985240932843

Время на выполнение кода выше: 22,41 с.


## Прогнозирование суммы чаевых с помощью моделей регрессии (без использования перекрестной проверки)

В этом разделе показано, как с помощью трех моделей регрессии спрогнозировать сумму чаевых в зависимости от других признаков. Мы используем следующие модели:

- регуляризованная линейная регрессия;
- Случайный лес
- увеличивающиеся деревья принятия решений.

Эти модели описаны в начале статьи. Процесс создания модели состоит из следующих этапов:

1. **Обучение модели** с использованием данных с одним набором параметров.
2. **Оценка модели** на основе набора тестовых данных с метриками.
3. **Сохранение модели** в большом двоичном объекте для последующего использования.   


>ПРИМЕЧАНИЕ AZURE. Перекрестная проверка не используется с тремя моделями регрессии в этом разделе. Однако пример использования перекрестной проверки с эластичной сетью для линейной регрессии показан в приложении к этому разделу.


>ПРИМЕЧАНИЕ AZURE. На практике в моделях LinearRegressionWithSGD часто возникают проблемы с конвергенцией. Чтобы получить допустимую модель, необходимо осторожно изменить или оптимизировать параметры. Проблему конвергенции можно решить, выполнив масштабирование переменных. Регрессия эластичной сети, показанная в приложении к этому разделу, также может помочь улучшить конвергенцию.


### Линейная регрессия с применением метода стохастического градиента

С помощью кода, приведенного в этом разделе, на основе масштабируемых признаков можно обучить модель линейной регрессии, в которой для оптимизации используется метод стохастического градиента, а также оценить, проанализировать и сохранить ее в хранилище BLOB-объектов Azure.


	# LINEAR REGRESSION WITH SGD 

	# RECORD START TIME
	timestart = datetime.datetime.now()
	
	# LOAD PYSPARK LIBRARIES
	from pyspark.mllib.regression import LabeledPoint, LinearRegressionWithSGD, LinearRegressionModel
	from pyspark.mllib.evaluation import RegressionMetrics
	from scipy import stats
	
	# USE SCALED FEATURES TO TRAIN MODEL
	linearModel = LinearRegressionWithSGD.train(oneHotTRAINregScaled, iterations=100, step = 0.1, regType='l2', regParam=0.1, intercept = True)
	
	# SCORE ON SCALED TEST DATA-SET & EVALUATE
	predictionAndLabels = oneHotTESTregScaled.map(lambda lp: (float(linearModel.predict(lp.features)), lp.label))
	testMetrics = RegressionMetrics(predictionAndLabels)
	
	print("RMSE = %s" % testMetrics.rootMeanSquaredError)
	print("R-sqr = %s" % testMetrics.r2)
	
	# SAVE MODEL IN BLOB
	datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
	linearregressionfilename = "LinearRegressionWithSGD_" + datestamp;
	dirfilename = modelDir + linearregressionfilename;
	
	linearModel.save(sc, dirfilename)
	
	# PRINT ELAPSED TIME
	timeend = datetime.datetime.now()
	timedelta = round((timeend-timestart).total_seconds(), 2) 
	print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**ВЫХОДНЫЕ ДАННЫЕ**

RMSE = 1.29395294535

R-sqr = 0.588405443258

Время на выполнение кода выше: 36,14 с.


### Регрессия с использованием модели случайного леса

Приведенный в этом разделе код предназначен для обучения, анализа и сохранения модели случайного леса, с помощью которой можно спрогнозировать суму чаевых за поездку в такси по Нью-Йорку.


>[AZURE.NOTE] Перекрестная проверка с перебором параметров с использованием пользовательского кода показана в приложении.


	#PREDICT TIP AMOUNTS USING RANDOM FOREST

	# RECORD START TIME
	timestart= datetime.datetime.now()
	
	# LOAD PYSPARK LIBRARIES
	from pyspark.mllib.tree import RandomForest, RandomForestModel
	from pyspark.mllib.util import MLUtils
	from pyspark.mllib.evaluation import RegressionMetrics
	
	
	# TRAIN MODEL
	categoricalFeaturesInfo={0:2, 1:2, 2:6, 3:4}
	rfModel = RandomForest.trainRegressor(indexedTRAINreg, categoricalFeaturesInfo=categoricalFeaturesInfo,
	                                    numTrees=25, featureSubsetStrategy="auto",
	                                    impurity='variance', maxDepth=10, maxBins=32)
	# UN-COMMENT IF YOU WANT TO PRING TREES
	#print('Learned classification forest model:')
	#print(rfModel.toDebugString())
	
	# PREDICT AND EVALUATE ON TEST DATA-SET
	predictions = rfModel.predict(indexedTESTreg.map(lambda x: x.features))
	predictionAndLabels = oneHotTESTreg.map(lambda lp: lp.label).zip(predictions)
	
	testMetrics = RegressionMetrics(predictionAndLabels)
	print("RMSE = %s" % testMetrics.rootMeanSquaredError)
	print("R-sqr = %s" % testMetrics.r2)
	
	# SAVE MODEL IN BLOB
	datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
	rfregressionfilename = "RandomForestRegression_" + datestamp;
	dirfilename = modelDir + rfregressionfilename;
	
	rfModel.save(sc, dirfilename);
	
	# PRINT ELAPSED TIME
	timeend = datetime.datetime.now()
	timedelta = round((timeend-timestart).total_seconds(), 2) 
	print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**ВЫХОДНЫЕ ДАННЫЕ**

RMSE = 0.962262172157

R-sqr = 0.69142848223

Время на выполнение кода выше: 29,3 с.


### Регрессия с применением увеличивающихся деревьев принятия решений

Приведенный в этом разделе код предназначен для обучения, анализа и сохранения модели увеличивающихся деревьев принятия решений, с помощью которой можно спрогнозировать суму чаевых за поездку в такси по Нью-Йорку.


	#PREDICT TIP AMOUNTS USING GRADIENT BOOSTING TREES

	# RECORD START TIME
	timestart= datetime.datetime.now()
	
	# LOAD PYSPARK LIBRARIES
	from pyspark.mllib.tree import GradientBoostedTrees, GradientBoostedTreesModel
	from pyspark.mllib.util import MLUtils
	
	# TRAIN MODEL
	categoricalFeaturesInfo={0:2, 1:2, 2:6, 3:4}
	gbtModel = GradientBoostedTrees.trainRegressor(indexedTRAINreg, categoricalFeaturesInfo=categoricalFeaturesInfo, 
	                                                numIterations=10, maxBins=32, maxDepth = 4, learningRate=0.1)
	
	# EVALUATE A TEST DATA-SET
	predictions = gbtModel.predict(indexedTESTreg.map(lambda x: x.features))
	predictionAndLabels = indexedTESTreg.map(lambda lp: lp.label).zip(predictions)
	
	testMetrics = RegressionMetrics(predictionAndLabels)
	print("RMSE = %s" % testMetrics.rootMeanSquaredError)
	print("R-sqr = %s" % testMetrics.r2)
	
	# PLOT SCATTER-PLOT BETWEEN ACTUAL AND PREDICTED TIP VALUES
	test_predictions= sqlContext.createDataFrame(predictionAndLabels)
	test_predictions_pddf = test_predictions.toPandas()
	
	ax = test_predictions_pddf.plot(kind='scatter', figsize = (6,6), x='_1', y='_2', color='blue', alpha = 0.25, label='Actual vs. predicted');
	fit = np.polyfit(test_predictions_pddf['_1'], test_predictions_pddf['_2'], deg=1)
	ax.set_title('Actual vs. Predicted Tip Amounts ($)')
	ax.set_xlabel("Actual")
	ax.set_ylabel("Predicted")
	ax.plot(test_predictions_pddf['_1'], fit[0] * test_predictions_pddf['_1'] + fit[1], color='magenta')
	plt.axis([-1, 20, -1, 20])
	plt.show(ax)
	
	# SAVE MODEL IN BLOB
	datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
	btregressionfilename = "GradientBoostingTreeRegression_" + datestamp;
	dirfilename = modelDir + btregressionfilename;
	
	gbtModel.save(sc, dirfilename)
	
	# PRINT ELAPSED TIME
	timeend = datetime.datetime.now()
	timedelta = round((timeend-timestart).total_seconds(), 2) 
	print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 


**ВЫХОДНЫЕ ДАННЫЕ**

Среднеквадратичное отклонение — 0,962160568829.

R-квадрат — 0,717354800581.

![Actual-vs-predicted-tip-amounts](./media/machine-learning-data-science-spark-advanced-data-exploration-modeling/actual-vs-predicted-tips.png)


## Приложение. Дополнительные задачи регрессии с использованием перекрестной проверки с перебором параметров

В этом приложении содержится код, показывающий, как выполнить перекрестную проверку с использованием эластичной сети для линейной регрессии, а также как выполнить перекрестную проверку с перебором параметров с использованием пользовательского кода для регрессии случайного леса.


### Перекрестная проверка с использованием эластичной сети для линейной регрессии

Код в этом разделе показывает, как выполнить перекрестную проверку с использованием эластичной сети для линейной регрессии и как оценить модель по тестовым данным.

	###  CV USING ELASTIC NET FOR LINEAR REGRESSION

	# RECORD START TIME
	timestart = datetime.datetime.now()
	
	# LOAD PYSPARK LIBRARIES
	from pyspark.ml.regression import LinearRegression
	from pyspark.ml import Pipeline
	from pyspark.ml.evaluation import RegressionEvaluator
	from pyspark.ml.tuning import CrossValidator, ParamGridBuilder
	
	# Define algo / model
	lr = LinearRegression()
	
	# Define grid parameters
	paramGrid = ParamGridBuilder().addGrid(lr.regParam, (0.01, 0.1))\
	                              .addGrid(lr.maxIter, (5, 10))\
	                              .addGrid(lr.tol, (1e-4, 1e-5))\
	                              .addGrid(lr.elasticNetParam, (0.25,0.75))\
	                              .build() 
	
	# Define pipeline, in this case, simply the modeling (without any transformations etc.)
	pipeline = Pipeline(stages=[lr])
	
	# Define CV with parameter sweep
	cv = CrossValidator(estimator= lr,
	                    estimatorParamMaps=paramGrid,
	                    evaluator=RegressionEvaluator(),
	                    numFolds=3)
	
	# Convert to data-frame, as CrossValidator won't run on RDDs
	trainDataFrame = sqlContext.createDataFrame(oneHotTRAINreg, ["features", "label"])
	
	# Train with cross-validation
	cv_model = cv.fit(trainDataFrame)
	
	timeend = datetime.datetime.now()
	timeend-timestart
	

	# EVALUATE MODEL ON TEST SET
	testDataFrame = sqlContext.createDataFrame(oneHotTESTreg, ["features", "label"])
	
	# MAKE PREDICTIONS ON TEST DOCUMENTS 
	#THE cvModel USES THE BEST MODEL FOUND (lrModel).
	test_predictions = cv_model.transform(testDataFrame)
	predictions_pddf = test_predictions.toPandas()
	
	corstats = stats.linregress(predictions_pddf['label'],predictions_pddf['prediction'])
	r2 = (corstats[2]*corstats[2])
	print("R-sqr = %s" % r2)
	
	# PRINT ELAPSED TIME
	timeend = datetime.datetime.now()
	timedelta = round((timeend-timestart).total_seconds(), 2) 
	print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 


**ВЫХОДНЫЕ ДАННЫЕ**

R-sqr = 0.594830601664

Время на выполнение кода выше: 129,51 с.


### Перекрестная проверка с перебором параметров с использованием пользовательского кода для регрессии случайного леса

Код в этом разделе показывает, как выполнить перекрестную проверку с перебором параметров с использованием пользовательского кода для регрессии случайного леса и как оценить модель по тестовым данным.


	# RECORD START TIME
	timestart= datetime.datetime.now()

	# LOAD PYSPARK LIBRARIES
	# GET ACCURARY FOR HYPERPARAMETERS BASED ON CROSS-VALIDATION IN TRAINING DATA-SET
	from pyspark.mllib.tree import RandomForest, RandomForestModel
	from pyspark.mllib.util import MLUtils
	from pyspark.mllib.evaluation import RegressionMetrics
	from sklearn.grid_search import ParameterGrid
	
	## CREATE PARAMETER GRID
	grid = [{'maxDepth': [5,10], 'numTrees': [25,50]}]
	paramGrid = list(ParameterGrid(grid))
	
	## SPECIFY LEVELS OF CATEGORICAL VARIBLES
	categoricalFeaturesInfo={0:2, 1:2, 2:6, 3:4}
	
	# SPECIFY NUMFOLDS AND ARRAY TO HOLD METRICS
	nFolds = 3;
	numModels = len(paramGrid)
	h = 1.0 / nFolds;
	metricSum = np.zeros(numModels);
	
	for i in range(nFolds):
	    # Create training and x-validation sets
	    validateLB = i * h
	    validateUB = (i + 1) * h
	    condition = (trainData["rand"] >= validateLB) & (trainData["rand"] < validateUB)
	    validation = trainData.filter(condition)
	    # Create labeled points from data-frames
	    if i > 0:
	        trainCVLabPt.unpersist()
	        validationLabPt.unpersist()
	    trainCV = trainData.filter(~condition)
	    trainCVLabPt = trainCV.map(parseRowIndexingRegression)
	    trainCVLabPt.cache()
	    validationLabPt = validation.map(parseRowIndexingRegression)
	    validationLabPt.cache()
	    # For parameter sets compute metrics from x-validation
	    for j in range(numModels):
	        maxD = paramGrid[j]['maxDepth']
	        numT = paramGrid[j]['numTrees']
	        # Train logistic regression model with hypermarameter set
	        rfModel = RandomForest.trainRegressor(trainCVLabPt, categoricalFeaturesInfo=categoricalFeaturesInfo,
	                                    numTrees=numT, featureSubsetStrategy="auto",
	                                    impurity='variance', maxDepth=maxD, maxBins=32)
	        predictions = rfModel.predict(validationLabPt.map(lambda x: x.features))
	        predictionAndLabels = validationLabPt.map(lambda lp: lp.label).zip(predictions)
	        # Use ROC-AUC as accuracy metrics
	        validMetrics = RegressionMetrics(predictionAndLabels)
	        metric = validMetrics.rootMeanSquaredError
	        metricSum[j] += metric
	
	avgAcc = metricSum/nFolds;
	bestParam = paramGrid[np.argmin(avgAcc)];
	
	# UNPERSIST OBJECTS
	trainCVLabPt.unpersist()
	validationLabPt.unpersist()
	        
	## TRAIN FINAL MODL WIHT BEST PARAMETERS
	rfModel = RandomForest.trainRegressor(indexedTRAINreg, categoricalFeaturesInfo=categoricalFeaturesInfo,
	                                    numTrees=bestParam['numTrees'], featureSubsetStrategy="auto",
	                                    impurity='variance', maxDepth=bestParam['maxDepth'], maxBins=32)

	# EVALUATE MODEL ON TEST DATA
	predictions = rfModel.predict(indexedTESTreg.map(lambda x: x.features))
	predictionAndLabels = indexedTESTreg.map(lambda lp: lp.label).zip(predictions)
	
	#PRINT TEST METRICS
	testMetrics = RegressionMetrics(predictionAndLabels)
	print("RMSE = %s" % testMetrics.rootMeanSquaredError)
	print("R-sqr = %s" % testMetrics.r2)
	
	# PRINT ELAPSED TIME
	timeend = datetime.datetime.now()
	timedelta = round((timeend-timestart).total_seconds(), 2) 
	print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 


**ВЫХОДНЫЕ ДАННЫЕ**

RMSE = 0.990182456723

R-sqr = 0.609523627251

Время на выполнение кода выше: 72,5 с.


### Очистка объектов из памяти и вывод расположений моделей

Удалить объекты, кэшированные в памяти, можно с помощью метода `unpersist()`.

	# UNPERSIST OBJECTS CACHED IN MEMORY

	# REMOVE ORIGINAL DFs
	taxi_df_train_cleaned.unpersist()
	taxi_df_train_with_newFeatures.unpersist()
	trainData.unpersist()
	trainData.unpersist()
	
	# FOR BINARY CLASSIFICATION TRAINING AND TESTING
	indexedTRAINbinary.unpersist()
	indexedTESTbinary.unpersist()
	oneHotTRAINbinary.unpersist()
	oneHotTESTbinary.unpersist()
	
	# FOR REGRESSION TRAINING AND TESTING
	indexedTRAINreg.unpersist()
	indexedTESTreg.unpersist()
	oneHotTRAINreg.unpersist()
	oneHotTESTreg.unpersist()
	
	# SCALED FEATURES
	oneHotTRAINregScaled.unpersist()
	oneHotTESTregScaled.unpersist()

	# PRINT MODEL FILE LOCATIONS FOR CONSUMPTION
	print "logisticRegFileLoc = modelDir + "" + logisticregressionfilename + """;
	print "linearRegFileLoc = modelDir + "" + linearregressionfilename + """;
	print "randomForestClassificationFileLoc = modelDir + "" + rfclassificationfilename + """;
	print "randomForestRegFileLoc = modelDir + "" + rfregressionfilename + """;
	print "BoostedTreeClassificationFileLoc = modelDir + "" + btclassificationfilename + """;
	print "BoostedTreeRegressionFileLoc = modelDir + "" + btregressionfilename + """;


**ВЫХОДНЫЕ ДАННЫЕ**

PythonRDD[119] at RDD at PythonRDD.scala:43

logisticRegFileLoc = modelDir + "LogisticRegressionWithLBFGS\_2016-04-1817\_40\_35.796789"

linearRegFileLoc = modelDir + "LinearRegressionWithSGD\_2016-04-1817\_44\_00.993832"

randomForestClassificationFileLoc = modelDir + "RandomForestClassification\_2016-04-1817\_42\_58.899412"

randomForestRegFileLoc = modelDir + "RandomForestRegression\_2016-04-1817\_44\_27.204734"

BoostedTreeClassificationFileLoc = modelDir + "GradientBoostingTreeClassification\_2016-04-1817\_43\_16.354770"

BoostedTreeRegressionFileLoc = modelDir + "GradientBoostingTreeRegression\_2016-04-1817\_44\_46.206262"

## Что дальше?

После создания моделей регрессии и классификации с помощью Spark MlLib необходимо ознакомиться с процессом оценки и анализа этих моделей.

**Использование модели**. Дополнительные сведения об оценке и анализе моделей классификации и регрессии, созданных в этой статье, см. в разделе [Score Spark-built machine learning models](machine-learning-data-science-spark-model-consumption.md) (Оценка моделей машинного обучения, созданных с помощью Spark).

<!---HONumber=AcomDC_0427_2016-->
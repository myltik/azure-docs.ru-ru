<properties
	pageTitle="Обработка и анализ данных с использованием Scala и Spark в Azure | Microsoft Azure"
	description="Сведения о том, как использовать Scala, чтобы выполнять контролируемые задачи машинного обучения, применяя масштабируемую библиотеку машинного обучения Spark (MLlib) и пакеты SparkML в кластере Spark Azure HDInsight."  
	services="machine-learning"
	documentationCenter=""
	authors="bradsev,deguhath"
	manager="paulettm"
	editor="cgronlun" />

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/01/2016"
	ms.author="bradsev;"/>


# Обработка и анализ данных с использованием Scala и Spark в Azure

В этой статье показано, как применять Scala, чтобы выполнять контролируемые задачи машинного обучения, используя масштабируемую библиотеку машинного обучения Spark (MLlib) и пакеты SparkML в кластере Spark Azure HDInsight. Здесь подробно расписаны задачи, которые образуют [процесс обработки и анализа данных](http://aka.ms/datascienceprocess): прием и исследование данных, визуализация, проектирование признаков, моделирование и использование моделей. Мы использовали такие модели, как логистическая и линейная регрессия, случайные леса и увеличивающиеся деревья принятия решений. Основное внимание в этой статье уделяется двум распространенным контролируемым задачам машинного обучения:

- регрессия: прогнозирование суммы чаевых (в долларах);
- двоичная классификация: прогнозирование вероятности оплаты чаевых (1 или 0) за поездку в такси.

Моделирование предусматривает обучение и оценку на основе наборов тестовых данных с учетом соответствующих метрик точности. В этой статье мы также рассказываем, как сохранить эти модели в хранилище BLOB-объектов Azure (WASB), а также сохранять и оценивать производительность прогнозирования. В более расширенных разделах рассматриваются способы оптимизации моделей с помощью перекрестной проверки и перебора гиперпараметров. В качестве данных используется пример с числом поездок и тарифами нью-йоркского такси за 2013 год.

[Scala](http://www.scala-lang.org/) — это язык для виртуальных машин Java, который обеспечивает интеграцию объектно-ориентированного подхода и функциональных качеств языка. Это масштабируемый язык, который отлично подходит для распределенной обработки в облаке и работает на кластерах Spark в Azure.

[Spark](http://spark.apache.org/) — это платформа параллельной обработки с открытым исходным кодом, которая поддерживает обработку в памяти, повышая производительность приложений для анализа больших данных. Подсистема обработки Spark призвана ускорить разработку, повысить удобство использования и реализовать сложную аналитику. Возможности распределенного вычисления в памяти Spark отлично подходят для итеративных алгоритмов в машинном обучении и графовых вычислениях. Пакет [spark.ml](http://spark.apache.org/docs/latest/ml-guide.html) включает в себя единый набор API высокого уровня, созданных поверх таблиц данных, позволяющих пользователям создавать и настраивать действующие конвейеры машинного обучения. [MLlib](http://spark.apache.org/mllib/) — это масштабируемая библиотека машинного обучения Spark, которая предоставляет возможности моделирования для этой распределенной среды.

[HDInsight Spark](../hdinsight/hdinsight-apache-spark-overview.md) представляет собой версию платформы Spark с открытым исходным кодом, размещенную в Azure. Он также поддерживает в кластере Spark **записные книжки Jupyter на языке Scala**, которые могут выполнять интерактивные запросы Spark SQL для преобразования, фильтрации и визуализации данных, хранящихся в больших двоичных объектах Azure. Здесь фрагменты кода Scala, которые предоставляют решения и формируют соответствующие графики с целью визуализации данных, выполняются в записных книжках Jupyter, установленных в кластерах Spark. Этапы моделирования, описанные в этих разделах, содержат код, который демонстрирует способ обучения, анализа, сохранения и использования каждого типа модели.

Действия по настройке и код, указанные в этом пошаговом руководстве, предназначены для HDInsight 3.4 Spark 1.6. Однако код здесь и в [записных книжках Jupyter на языке Scala](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/Scala/Exploration%20Modeling%20and%20Scoring%20using%20Scala.ipynb) в этом пошаговом руководстве является универсальным и должен работать в любом кластере Spark. Действия по настройке кластера и управлению им могут немного отличаться от приведенных здесь, если вы не используете HDInsight Spark.

> [AZURE.NOTE] О том, как использовать Python вместо Scala для выполнения задач по полным обработке и анализу данных, см. в статье [Общие сведения об обработке и анализе данных с помощью платформы Spark в Azure HDInsight](machine-learning-data-science-spark-overview.md).


## Предварительные требования

1\. Для изучения этих разделов необходимо иметь подписку Azure. Если у вас ее нет, см. раздел [о получении бесплатной пробной версии Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

2\. Для выполнения инструкций этого руководства требуется кластер HDInsight 3.4 Spark 1.6. Создайте его, выполнив инструкции в статье [Начало работы: создание кластера Apache Spark в Azure HDInsight](../hdinsight/hdinsight-apache-spark-jupyter-spark-sql.md). Тип и версию кластера можно указать с помощью меню **Выберите тип кластера**.

![](./media/machine-learning-data-science-process-scala-walkthrough/spark-cluster-on-portal.png)


>[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]


Описание данных о поездках в такси по Нью-Йорку и инструкции о том, как выполнить код из записной книжки Jupyter в кластере Spark, см. в соответствующих разделах статьи [Общие сведения об обработке и анализе данных с помощью платформы Spark в Azure HDInsight](machine-learning-data-science-spark-overview.md).


## Выполнение кода Scala из записной книжки Jupyter в кластере Spark 

Записную книжку Jupyter можно запустить с портала Azure. Найдите кластер Spark на панели мониторинга и щелкните ее, чтобы войти на страницу управления кластером. Последовательно выберите **Cluster Dashboards** (Панели мониторинга кластера) > **Записная книжка Jupyter**, чтобы открыть записную книжку, связанную с кластером Spark.

![](./media/machine-learning-data-science-process-scala-walkthrough/spark-jupyter-on-portal.png)

Чтобы получить доступ к записным книжкам Jupyter, также можно перейти по ссылке ***https://CLUSTERNAME.azurehdinsight.net/jupyter***. Просто замените фрагмент CLUSTERNAME в URL-адресе на имя собственного кластера. Для доступа к записным книжкам необходим пароль к учетной записи администратора.

![](./media/machine-learning-data-science-process-scala-walkthrough/spark-jupyter-notebook.png)

Выберите Scala, чтобы открыть каталог, содержащий несколько примеров предварительно упакованных записных книжек, в которых используется API PySpark. Записная книжка **Exploration Modeling and Scoring using Scala.ipynb** с примерами кода для этой группы разделов о Spark доступна на сайте [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/Spark/Scala).


Записную книжку можно отправить непосредственно из GitHub на сервер записных книжек Jupyter в кластере Spark. На домашней странице записной книжки Jupyter в правой части экрана нажмите кнопку **Отправить**. Откроется обозреватель файлов. Здесь вы можете вставить URL-адрес GitHub (необработанное содержимое) для записной книжки Scala и щелкнуть **Открыть**. Записная книжка Scala доступна по следующей URL-ссылке:

[Exploration-Modeling-and-Scoring-using-Scala.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/Scala/Exploration-Modeling-and-Scoring-using-Scala.ipynb)


## Настройка: предустановленные контексты, а также волшебные команды и библиотеки Spark

### Предустановленный контекст Spark и Hive

	# SET START TIME
	import java.util.Calendar
	val beginningTime = Calendar.getInstance().getTime()


В ядрах Spark, предоставляемых с записными книжками Jupyter, есть предустановленные контексты, поэтому вам не требуется явно настраивать контексты Spark или Hive перед началом работы с разрабатываемым приложением. Они доступны по умолчанию, а именно:

- sc — для контекста Spark;
- sqlContext — для контекста Hive.


### Волшебные команды Spark

Ядро Spark предусматривает несколько волшебных команд. Это специальные команды, которые можно вызывать, используя "%%". В этих примерах кода используются две подобные команды.

- **%%local** указывает, что код в последующих строках будет выполнен локально. Это должен быть допустимый код Scala.
- **%%sql -o <имя\_переменной>** выполняет запрос Hive к sqlContext. Если передан параметр -o, результат запроса сохраняется в контексте %%local Scala в качестве таблицы данных Spark.

Дополнительные сведения о ядрах для записных книжек Jupyter и предварительно определенных волшебных командах с оператором %% (например, %%local), которые в них предусмотрены, см. в статье [Ядра, доступные для использования с записными книжками Jupyter с кластерами HDInsight Spark на платформе Linux в HDInsight](../hdinsight/hdinsight-apache-spark-jupyter-notebook-kernels.md).


### Импорт библиотек

Импортируйте Spark, MLlib и другие необходимые библиотеки, используя следующий код:

	# IMPORT SPARK AND JAVA LIBRARIES 
	import org.apache.spark.sql.SQLContext
	import org.apache.spark.sql.functions._
	import java.text.SimpleDateFormat
	import java.util.Calendar
	import sqlContext.implicits._
	import org.apache.spark.sql.Row
	
	# SPARK SQL FUNCTIONS
	import org.apache.spark.sql.types.{StructType, StructField, StringType, IntegerType, FloatType, DoubleType}
	import org.apache.spark.sql.functions.rand
	
	# SPARK ML FUNCTIONS
	import org.apache.spark.ml.Pipeline
	import org.apache.spark.ml.feature.{StringIndexer, VectorAssembler, OneHotEncoder, VectorIndexer, Binarizer}
	import org.apache.spark.ml.tuning.{ParamGridBuilder, TrainValidationSplit, CrossValidator}
	import org.apache.spark.ml.regression.{LinearRegression, LinearRegressionModel, RandomForestRegressor, RandomForestRegressionModel, GBTRegressor, GBTRegressionModel}
	import org.apache.spark.ml.classification.{LogisticRegression, LogisticRegressionModel, RandomForestClassifier, RandomForestClassificationModel, GBTClassifier, GBTClassificationModel}
	import org.apache.spark.ml.evaluation.{BinaryClassificationEvaluator, RegressionEvaluator, MulticlassClassificationEvaluator}
	
	# SPARK MLLIB FUNCTIONS
	import org.apache.spark.mllib.linalg.{Vector, Vectors}
	import org.apache.spark.mllib.util.MLUtils
	import org.apache.spark.mllib.classification.{LogisticRegressionWithLBFGS, LogisticRegressionModel}
	import org.apache.spark.mllib.regression.{LabeledPoint, LinearRegressionWithSGD, LinearRegressionModel}
	import org.apache.spark.mllib.tree.{GradientBoostedTrees, RandomForest}
	import org.apache.spark.mllib.tree.configuration.BoostingStrategy
	import org.apache.spark.mllib.tree.model.{GradientBoostedTreesModel, RandomForestModel, Predict}
	import org.apache.spark.mllib.evaluation.{BinaryClassificationMetrics, MulticlassMetrics, RegressionMetrics}
	
	# SPECIFY SQL CONTEXT
	val sqlContext = new SQLContext(sc)


## Прием данных
 
Чтобы начать анализ и обработку данных, требуется переместить их из внешних источников или систем, в которых они находятся, в среду исследования и моделирования данных. В этом пошаговом руководстве выполняется считывание объединенной 0,1-процентной выборки файла данных поездок и тарифов такси (хранящегося в виде TSV-файла). В качестве среды исследования и моделирования данных используется среда Spark. Этот раздел содержит код, с помощью которого можно выполнить следующие задачи:

- установка пути к каталогам для хранения данных и моделей;
- считывание входного набора данных (TSV-файл);
- определение схемы для данных и очистку данных;
- создание очищенных таблиц данных, их кэширование в памяти;
- регистрация данных в качестве временной таблицы в контексте SQL;
- выполнение запроса к таблице и импорт результатов в таблицу данных.


### Настройка путей каталога к месту хранения в хранилище BLOB-объектов

Spark может считывать данные и записывать их в хранилище BLOB-объектов Azure (также известное как WASB). Таким образом, данные из хранилища можно обрабатывать с помощью Spark и сохранять полученные данные в этом же хранилище.

Чтобы сохранить модели или файлы в хранилище BLOB-объектов, необходимо указать путь соответствующим образом. Контейнер по умолчанию, присоединенный к кластеру Spark, можно указать с помощью пути, который начинается с "wasb:///". Другие расположения начинаются с wasb://.

В следующем примере кода указывается расположение входных данных для чтения и путь к большому двоичному объекту Azure, присоединенному к кластеру Spark, в котором будет сохранена модель.

	# SET PATHS TO DATA AND MODEL FILE LOCATIONS: 
	# INGEST DATA DATA AND SPECIFY HEADERS FOR COLUMNS
	val taxi_train_file = sc.textFile("wasb://mllibwalkthroughs@cdspsparksamples.blob.core.windows.net/Data/NYCTaxi/JoinedTaxiTripFare.Point1Pct.Train.tsv")
	val header = taxi_train_file.first;
	
	# SET THE MODEL STORAGE DIRECTORY PATH 
	# NOTE THAT THE FINAL BACKSLASH IN THE PATH IS NEEDED.
	val modelDir = "wasb:///user/remoteuser/NYCTaxi/Models/";


### Импорт данных, создание RDD и определение таблицы данных согласно схеме 

	# RECORD START TIME
	val starttime = Calendar.getInstance().getTime()
	
	# DEFINE SCHEMA BASED ON HEADER OF THE FILE
	val sqlContext = new SQLContext(sc)
	val taxi_schema = StructType(
	    Array(
	        StructField("medallion", StringType, true), 
	        StructField("hack_license", StringType, true),
	        StructField("vendor_id", StringType, true), 
	        StructField("rate_code", DoubleType, true),
	        StructField("store_and_fwd_flag", StringType, true),
	        StructField("pickup_datetime", StringType, true),
	        StructField("dropoff_datetime", StringType, true),
	        StructField("pickup_hour", DoubleType, true),
	        StructField("pickup_week", DoubleType, true),
	        StructField("weekday", DoubleType, true),
	        StructField("passenger_count", DoubleType, true),
	        StructField("trip_time_in_secs", DoubleType, true),
	        StructField("trip_distance", DoubleType, true),
	        StructField("pickup_longitude", DoubleType, true),
	        StructField("pickup_latitude", DoubleType, true),
	        StructField("dropoff_longitude", DoubleType, true),
	        StructField("dropoff_latitude", DoubleType, true),
	        StructField("direct_distance", StringType, true),
	        StructField("payment_type", StringType, true),
	        StructField("fare_amount", DoubleType, true),
	        StructField("surcharge", DoubleType, true),
	        StructField("mta_tax", DoubleType, true),
	        StructField("tip_amount", DoubleType, true),
	        StructField("tolls_amount", DoubleType, true),
	        StructField("total_amount", DoubleType, true),
	        StructField("tipped", DoubleType, true),
	        StructField("tip_class", DoubleType, true)
	        )
	    )
	
	# CAST VARIABLES ACCORDING TO SCHEMA
	val taxi_temp = (taxi_train_file.map(_.split("\t"))
	                        .filter((r) => r(0) != "medallion")
	                        .map(p => Row(p(0), p(1), p(2),
	                            p(3).toDouble, p(4), p(5), p(6), p(7).toDouble, p(8).toDouble, p(9).toDouble, p(10).toDouble,
	                            p(11).toDouble, p(12).toDouble, p(13).toDouble, p(14).toDouble, p(15).toDouble, p(16).toDouble,
	                            p(17), p(18), p(19).toDouble, p(20).toDouble, p(21).toDouble, p(22).toDouble,
	                            p(23).toDouble, p(24).toDouble, p(25).toDouble, p(26).toDouble)))
	
	
	# CREATE INITIAL DATA-FRAME, DROP COLUMNS, AND CREATE CLEANED DATA-FRAME BY FILTERING FOR UNDESIRED VALUES OR OUTLIERS
	val taxi_train_df = sqlContext.createDataFrame(taxi_temp, taxi_schema)
	
	val taxi_df_train_cleaned = (taxi_train_df.drop(taxi_train_df.col("medallion"))
	        .drop(taxi_train_df.col("hack_license")).drop(taxi_train_df.col("store_and_fwd_flag"))
	        .drop(taxi_train_df.col("pickup_datetime")).drop(taxi_train_df.col("dropoff_datetime"))
	        .drop(taxi_train_df.col("pickup_longitude")).drop(taxi_train_df.col("pickup_latitude"))
	        .drop(taxi_train_df.col("dropoff_longitude")).drop(taxi_train_df.col("dropoff_latitude"))
	        .drop(taxi_train_df.col("surcharge")).drop(taxi_train_df.col("mta_tax"))
	        .drop(taxi_train_df.col("direct_distance")).drop(taxi_train_df.col("tolls_amount"))
	        .drop(taxi_train_df.col("total_amount")).drop(taxi_train_df.col("tip_class"))
	        .filter("passenger_count > 0 and passenger_count < 8 AND payment_type in ('CSH', 'CRD') AND tip_amount >= 0 AND tip_amount < 30 AND fare_amount >= 1 AND fare_amount < 150 AND trip_distance > 0 AND trip_distance < 100 AND trip_time_in_secs > 30 AND trip_time_in_secs < 7200"));
	
	# CACHE AND MATERIALIZE CLEANED DATA-FRAME IN MEMORY
	taxi_df_train_cleaned.cache()
	taxi_df_train_cleaned.count()
	
	# REGISTER DATA-FRAME AS A TEMP-TABLE IN SQL-CONTEXT
	taxi_df_train_cleaned.registerTempTable("taxi_train")
	
	# GET TIME TO RUN THE CELL
	val endtime = Calendar.getInstance().getTime()
	val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
	println("Time taken to run the above cell: " + elapsedtime + " seconds.");


**ВЫХОДНЫЕ ДАННЫЕ:**

время, затраченное на выполнение кода выше, — 8 секунд.


### Выполнение запроса к таблице и импорт результатов в таблицу данных 

Запросите данные о поездках, пассажирах и чаевых из таблицы, отфильтруйте поврежденные и несвойственные данные, а затем выведите несколько строк.

	# QUERY THE DATA
	val sqlStatement = """
	    SELECT fare_amount, passenger_count, tip_amount, tipped
	    FROM taxi_train 
	    WHERE passenger_count > 0 AND passenger_count < 7
	    AND fare_amount > 0 AND fare_amount < 200
	    AND payment_type in ('CSH', 'CRD')
	    AND tip_amount > 0 AND tip_amount < 25
	"""
	val sqlResultsDF = sqlContext.sql(sqlStatement)
	
	# SHOW ONLY THE TOP THREE ROWS
	sqlResultsDF.show(3)

**ВЫХОДНЫЕ ДАННЫЕ:**

fare\_amount|passenger\_count|tip\_amount|tipped
-----------|---------------|----------|------
 13,5| 1\.0| 2,9| 1\.0
 16,0| 2,0| 3\.4| 1\.0
 10,5| 2,0| 1\.0| 1\.0


## Исследование и визуализация данных 

После помещения данных в Spark необходимо исследовать и визуализировать их, чтобы получить более глубокое представление. В этом разделе мы изучим данные такси, используя запросы SQL, а затем импортируем результаты в таблицу данных, чтобы графически показать целевые переменные и потенциальные признаки, а также проверить их визуально с помощью функции автоматической визуализации Jupyter.

### Использование волшебных команд local и SQL для графического представления данных

По умолчанию выходные данные фрагмента кода, выполняемого из записной книжки Jupyter, доступны в контексте сеанса, сохраненного на рабочих узлах. Если для каждого вычисления нужно сохранить данные о поездке на рабочих узлах и все данные, необходимые для вычислений, доступны локально на узле сервера Jupyter (который является головным), можно использовать волшебную команду %%local, чтобы выполнить фрагмент кода на сервере Jupyter.

- **SQL magic (`%%sql`)**. Ядро HDInsight Spark позволяет легко выполнять встроенные запросы HiveQL к sqlContext. Аргумент (-o VARIABLE\_NAME) сохраняет выходные данные запроса SQL в формате Pandas DataFrame на сервере Jupyter. Это означает, что они будут доступны в локальном режиме.
- Волшебная команда **`%%local`** используется для локального выполнения кода на сервере Jupyter, который представляет собой головной узел кластера HDInsight. Как правило, команда `%%local` используется в комбинации с волшебной командой `%%sql` с параметром -o. Параметр -o сохраняет выходные данные запроса SQL локально, после чего волшебная команда %%local активирует следующий набор фрагментов кода, который выполняется локально с выходными данными запросов SQL, сохраненными на локальном компьютере.

### Выполнение запроса на данные с использованием SQL
Этот запрос извлекает данные о поездках на основе сведений суммы к оплате, количества пассажиров и суммы чаевых.

	# SQL QUERY
	%%sql -q -o sqlResults
	SELECT fare_amount, passenger_count, tip_amount, tipped FROM taxi_train WHERE passenger_count > 0 AND passenger_count < 7 AND fare_amount > 0 AND fare_amount < 200 AND payment_type in ('CSH', 'CRD') AND tip_amount > 0 AND tip_amount < 25

В коде ниже волшебная команда %%local создает локальную таблицу данных sqlResults, которую можно использовать для построения графического представления с применением matplotlib.

> [AZURE.TIP] В этом пошаговом руководстве волшебная команда local используется несколько раз. Если используется большой объем данных, сделайте выборку, чтобы создать таблицу данных, которую можно сохранить в локальной памяти.

### Графическое представление данных

Данные можно графически показать с помощью кода Python, если таблица данных находится в локальном контексте в качестве таблицы данных pandas.

	# RUN THE CODE LOCALLY ON THE JUPYTER SERVER
	%%local
	
	# USE THE JUPYTER AUTO-PLOTTING FEATURE TO CREATE INTERACTIVE FIGURES. 
	# CLICK ON THE TYPE OF PLOT TO BE GENERATED (E.G. LINE, AREA, BAR ETC.)
	sqlResults


 Ядро Spark автоматически визуализирует выходные данные запросов SQL (HiveQL) после выполнения кода. Вы можете выбрать тип визуализации среди нескольких различных типов:

- Таблица
- круговая диаграмма;
- график;
- Область
- линейчатая диаграмма.

Ниже приведен код для графического представления данных:

	# RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
	%%local
	import matplotlib.pyplot as plt
	%matplotlib inline
	
	# TIP BY PAYMENT TYPE AND PASSENGER COUNT
	ax1 = sqlResults[['tip_amount']].plot(kind='hist', bins=25, facecolor='lightblue')
	ax1.set_title('Tip amount distribution')
	ax1.set_xlabel('Tip Amount ($)')
	ax1.set_ylabel('Counts')
	plt.suptitle('')
	plt.show()
	
	# TIP BY PASSENGER COUNT
	ax2 = sqlResults.boxplot(column=['tip_amount'], by=['passenger_count'])
	ax2.set_title('Tip amount by Passenger count')
	ax2.set_xlabel('Passenger count')
	ax2.set_ylabel('Tip Amount ($)')
	plt.suptitle('')
	plt.show()
	
	# TIP AMOUNT BY FARE AMOUNT, POINTS ARE SCALED BY PASSENGER COUNT
	ax = sqlResults.plot(kind='scatter', x= 'fare_amount', y = 'tip_amount', c='blue', alpha = 0.10, s=5*(sqlResults.passenger_count))
	ax.set_title('Tip amount by Fare amount')
	ax.set_xlabel('Fare Amount ($)')
	ax.set_ylabel('Tip Amount ($)')
	plt.axis([-2, 80, -2, 20])
	plt.show()


**ВЫХОДНЫЕ ДАННЫЕ:**

![Гистограмма суммы чаевых](./media/machine-learning-data-science-process-scala-walkthrough/plot-tip-amount-histogram.png)

![Сумма чаевых в зависимости от количества пассажиров](./media/machine-learning-data-science-process-scala-walkthrough/plot-tip-amount-by-passenger-count.png)

![Сумма чаевых в зависимости от тарифа](./media/machine-learning-data-science-process-scala-walkthrough/plot-tip-amount-by-fare-amount.png)



## Создание признаков и подготовка данных для функций моделирования 

Чтобы можно было использовать функции древовидного моделирования из машинного обучения Spark и MLlib, необходимо подготовить целевые объекты и признаки, применяя различные методы, такие как группирование, индексирование, прямое кодирование и векторизация. В этом разделе подробно рассматриваются следующие процедуры:

- создание признака путем **группирования** часов в периоды трафика;
- **индексирование и прямое кодирование** категориальных признаков;
- **выборка и разбиение набора данных** на обучающую и проверочную часть;
- **указание переменной обучения и признаков**, а также создание индексированных или прямо закодированных обучающих и проверочных RDD или таблиц входящих данных с помеченной вершиной;
- автоматическая **категоризация и векторизация целевых объектов и признаков** для использования в качестве входных данных моделей машинного обучения.


### Создание нового признака путем группирования часов в периоды трафика 

В следующем коде показано, как создать признак путем группирования часов в периоды трафика, а затем кэшировать итоговый фрейм данных в памяти. Если устойчивые распределенные наборы данных и фреймы данных используются постоянно, на их кэширование необходимо меньше времени. В этом пошаговом руководстве процесс кэширования имеющихся данных выполняется в несколько этапов.

	# CREATE FOUR BUCKETS FOR TRAFFIC TIMES
	val sqlStatement = """
	    SELECT *,
	    CASE
	     WHEN (pickup_hour <= 6 OR pickup_hour >= 20) THEN "Night" 
	     WHEN (pickup_hour >= 7 AND pickup_hour <= 10) THEN "AMRush" 
	     WHEN (pickup_hour >= 11 AND pickup_hour <= 15) THEN "Afternoon"
	     WHEN (pickup_hour >= 16 AND pickup_hour <= 19) THEN "PMRush"
	    END as TrafficTimeBins
	    FROM taxi_train 
	"""
	val taxi_df_train_with_newFeatures = sqlContext.sql(sqlStatement)
	
	# CACHE DATA-FRAME IN MEMORY & MATERIALIZE DF IN MEMORY 
	taxi_df_train_with_newFeatures.cache()
	taxi_df_train_with_newFeatures.count()


### Индексирование и прямое кодирование категориальных признаков 

В этом разделе показано, как индексировать и кодировать категориальные признаки в качестве ввода для функций моделирования. Перед использованием в функциях моделирования и прогнозирования MLlib категориальные входные данные сначала необходимо проиндексировать или закодировать.

В зависимости от модели этот процесс происходит по-разному. Например, для логистической и линейной моделей регрессии требуется прямое кодирование, при котором признак с 3 категориями можно разделить на 3 столбца, в каждом из которых в зависимости от категории наблюдения содержатся значения 0 или 1. Для этого можно использовать функцию [OneHotEncoder](http://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.OneHotEncoder.html#sklearn.preprocessing.OneHotEncoder) в MLlib. Этот кодировщик сопоставляет столбец с индексами меток со столбцом двоичных векторов как минимум с одним отдельным значением. Благодаря этой кодировке алгоритмы, для которых необходимы признаки с числовыми значениями (например, логистическая регрессия), можно применять к категориальным признакам.

Здесь мы преобразуем только четыре переменных, чтобы показать примеры, которые представляют собой символьные строки. Другие переменные, например дня недели, представленные числовыми значениями, можно индексировать в качестве категориальных переменных.

Для индексирования мы использовали функцию `StringIndexer()`, а для прямого кодирования — `OneHotEncoder()` из MLlib. Ниже приведен пример кода, с помощью которого можно проиндексировать и закодировать категориальные признаки.


	# HERE WE CREATE INDEXES, AND ONE-HOT ENCODED VECTORS FOR SEVERAL CATEGORICAL FEATURES

	# RECORD START TIME
	val starttime = Calendar.getInstance().getTime()
	
	# INDEX AND ENCODE VENDOR_ID
	val stringIndexer = new StringIndexer().setInputCol("vendor_id").setOutputCol("vendorIndex").fit(taxi_df_train_with_newFeatures)
	val indexed = stringIndexer.transform(taxi_df_train_with_newFeatures)
	val encoder = new OneHotEncoder().setInputCol("vendorIndex").setOutputCol("vendorVec")
	val encoded1 = encoder.transform(indexed)
	
	# INDEX AND ENCODE RATE_CODE
	val stringIndexer = new StringIndexer().setInputCol("rate_code").setOutputCol("rateIndex").fit(encoded1)
	val indexed = stringIndexer.transform(encoded1)
	val encoder = new OneHotEncoder().setInputCol("rateIndex").setOutputCol("rateVec")
	val encoded2 = encoder.transform(indexed)
	
	# INDEX AND ENCODE PAYMENT_TYPE
	val stringIndexer = new StringIndexer().setInputCol("payment_type").setOutputCol("paymentIndex").fit(encoded2)
	val indexed = stringIndexer.transform(encoded2)
	val encoder = new OneHotEncoder().setInputCol("paymentIndex").setOutputCol("paymentVec")
	val encoded3 = encoder.transform(indexed)
	
	# INDEX AND TRAFFIC TIME BINS
	val stringIndexer = new StringIndexer().setInputCol("TrafficTimeBins").setOutputCol("TrafficTimeBinsIndex").fit(encoded3)
	val indexed = stringIndexer.transform(encoded3)
	val encoder = new OneHotEncoder().setInputCol("TrafficTimeBinsIndex").setOutputCol("TrafficTimeBinsVec")
	val encodedFinal = encoder.transform(indexed)
	
	# GET TIME TO RUN THE CELL 
	val endtime = Calendar.getInstance().getTime()
	val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
	println("Time taken to run the above cell: " + elapsedtime + " seconds.");


**ВЫХОДНЫЕ ДАННЫЕ:**

время, затраченное на выполнение кода выше, — 4 секунды.



### Выборка и разбиение набора данных на обучающую и проверочную часть

С помощью кода в этом разделе создается случайная выборка данных (25 % всех данных). Несмотря на то, что из-за небольшого размера используемого набора данных, этот шаг выполнять необязательно, мы все равно покажем, как создавать выборку. Это поможет вам в случае возникновения проблем. Если выборки большие, этот процесс может значительно сэкономить время обучения моделей. Затем мы разобьем выборку данных на наборы для обучения (75 %) и тестирования (25 %), которые будут использоваться для двоичной классификации и регрессии.

Мы добавили случайное число (от 0 до 1), чтобы получить строку (в столбце rand), которую можно использовать для выбора сверток перекрестной проверки во время обучения.


	# RECORD START TIME
	val starttime = Calendar.getInstance().getTime()
	
	# SPECIFY SAMPLING AND SPLITTING FRACTIONS
	val samplingFraction = 0.25;
	val trainingFraction = 0.75; 
	val testingFraction = (1-trainingFraction);
	val seed = 1234;
	val encodedFinalSampledTmp = encodedFinal.sample(withReplacement = false, fraction = samplingFraction, seed = seed)
	val sampledDFcount = encodedFinalSampledTmp.count().toInt
	
	val generateRandomDouble = udf(() => {
	    scala.util.Random.nextDouble
	})
	
	# ADD RANDOM NUMBER FOR CV
	val encodedFinalSampled = encodedFinalSampledTmp.withColumn("rand", generateRandomDouble());
	
	# SPLIT SAMPLED DATA-FRAME INTO TRAIN/TEST, WITH A RANDOM COLUMN ADDED FOR DOING CV (SHOWN LATER)
	# INCLUDE RAND COLUMN FOR CREATING CROSS-VALIDATION FOLDS
	val splits = encodedFinalSampled.randomSplit(Array(trainingFraction, testingFraction), seed = seed)
	val trainData = splits(0)
	val testData = splits(1)
	
	# GET TIME TO RUN THE CELL 
	val endtime = Calendar.getInstance().getTime()
	val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
	println("Time taken to run the above cell: " + elapsedtime + " seconds.");


**ВЫХОДНЫЕ ДАННЫЕ:**

время, затраченное на выполнение кода выше, — 2 секунды.


### Указание переменной обучения и признаков, а также создание индексированных или прямо закодированных обучающих и проверочных RDD или таблиц входящих данных с помеченной вершиной 

В этом разделе содержится код, с помощью которого можно индексировать категориальные текстовые данные в тип данных с помеченной вершиной, а затем закодировать их. После этого данные можно использовать для обучения и проверки модели логистической регрессии MLlib и других моделей классификации. Объекты с помеченной вершиной отформатированы в устойчивые распределенные наборы данных, которые можно использовать в качестве входных для большинства алгоритмов машинного обучения в MLlib. Объект [с помеченной вершиной](https://spark.apache.org/docs/latest/mllib-data-types.html#labeled-point) — это разреженный или плотный локальный вектор, связанный с меткой или ответом.

В этом коде мы указали целевую (зависимую) переменную обучения и признаки, которые следует использовать для обучения, а также создали индексированные и прямо закодированные обучающие и проверочные RDD и таблицы входящих данных с помеченной вершиной.


	# RECORD START TIME
	val starttime = Calendar.getInstance().getTime()
	
	# MAP NAMES OF FEATURES AND TARGETS FOR CLASSIFICATION AND REGRESSION PROBLEMS.
	val featuresIndOneHot = List("paymentVec", "vendorVec", "rateVec", "TrafficTimeBinsVec", "pickup_hour", "weekday", "passenger_count", "trip_time_in_secs", "trip_distance", "fare_amount").map(encodedFinalSampled.columns.indexOf(_))
	val featuresIndIndex = List("paymentIndex", "vendorIndex", "rateIndex", "TrafficTimeBinsIndex", "pickup_hour", "weekday", "passenger_count", "trip_time_in_secs", "trip_distance", "fare_amount").map(encodedFinalSampled.columns.indexOf(_))
	
	# SPECIFY THE TARGET FOR CLASSIFICATION ('tipped') AND REGRESSION ('tip_amount') PROBLEMS
	val targetIndBinary = List("tipped").map(encodedFinalSampled.columns.indexOf(_))
	val targetIndRegression = List("tip_amount").map(encodedFinalSampled.columns.indexOf(_))
	
	# INDEXED LAELEDPOINT RDD OBJECTS
	val indexedTRAINbinary = trainData.rdd.map(r => LabeledPoint(r.getDouble(targetIndBinary(0).toInt), Vectors.dense(featuresIndIndex.map(r.getDouble(_)).toArray)))
	val indexedTESTbinary = testData.rdd.map(r => LabeledPoint(r.getDouble(targetIndBinary(0).toInt), Vectors.dense(featuresIndIndex.map(r.getDouble(_)).toArray)))
	val indexedTRAINreg = trainData.rdd.map(r => LabeledPoint(r.getDouble(targetIndRegression(0).toInt), Vectors.dense(featuresIndIndex.map(r.getDouble(_)).toArray)))
	val indexedTESTreg = testData.rdd.map(r => LabeledPoint(r.getDouble(targetIndRegression(0).toInt), Vectors.dense(featuresIndIndex.map(r.getDouble(_)).toArray)))
	
	# Indexed DFs that can be used for training using Spark ML functions
	val indexedTRAINbinaryDF = indexedTRAINbinary.toDF()
	val indexedTESTbinaryDF = indexedTESTbinary.toDF()
	val indexedTRAINregDF = indexedTRAINreg.toDF()
	val indexedTESTregDF = indexedTESTreg.toDF()
	
	# One-hot encoded (vectorized) DFs that can be used for training using Spark ML functions
	val assemblerOneHot = new VectorAssembler().setInputCols(Array("paymentVec", "vendorVec", "rateVec", "TrafficTimeBinsVec", "pickup_hour", "weekday", "passenger_count", "trip_time_in_secs", "trip_distance", "fare_amount")).setOutputCol("features")
	val OneHotTRAIN = assemblerOneHot.transform(trainData) 
	val OneHotTEST = assemblerOneHot.transform(testData)
	
	# GET TIME TO RUN THE CELL
	val endtime = Calendar.getInstance().getTime()
	val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
	println("Time taken to run the above cell: " + elapsedtime + " seconds.");


**ВЫХОДНЫЕ ДАННЫЕ:**

время, затраченное на выполнение кода выше, — 4 секунды.


### Автоматическая категоризация и векторизация целевых объектов и признаков для использования в качестве входных данных моделей машинного обучения

Целевой объект и признаки, которые нужно использовать в функциях древовидного моделирования в Spark ML, необходимо правильно категоризировать. Код выполняет две задачи:

1. Создает двоичный целевой объект для классификации, присваивая значение 0 или 1 каждой точке данных со значением между 0 и 1, используя пороговое значение 0,5.
2. Автоматически категоризирует признаки. Если количество уникальных числовых значений для любого из признаков меньше 32, этот признак категоризирован.

Ниже приведен код для этих двух задач.

	# CATEGORIZE FEATURES AND BINARIZE TARGET FOR BINARY CLASSIFICATION PROBLEM

	# TRAIN DATA
	val indexer = new VectorIndexer().setInputCol("features").setOutputCol("featuresCat").setMaxCategories(32)
	val indexerModel = indexer.fit(indexedTRAINbinaryDF)
	val indexedTrainwithCatFeat = indexerModel.transform(indexedTRAINbinaryDF)
	val binarizer: Binarizer = new Binarizer().setInputCol("label").setOutputCol("labelBin").setThreshold(0.5)
	val indexedTRAINwithCatFeatBinTarget = binarizer.transform(indexedTrainwithCatFeat)
	
	# TEST DATA
	val indexerModel = indexer.fit(indexedTESTbinaryDF)
	val indexedTrainwithCatFeat = indexerModel.transform(indexedTESTbinaryDF)
	val binarizer: Binarizer = new Binarizer().setInputCol("label").setOutputCol("labelBin").setThreshold(0.5)
	val indexedTESTwithCatFeatBinTarget = binarizer.transform(indexedTrainwithCatFeat)
	
	# CATEGORIZE FEATURES FOR REGRESSION PROBLEM
	# CREATE PROPERLY INDEXED AND CATEGORIED DFS FOR TREE-BASED MODELS

	# TRAIN DATA
	val indexer = new VectorIndexer().setInputCol("features").setOutputCol("featuresCat").setMaxCategories(32)
	val indexerModel = indexer.fit(indexedTRAINregDF)
	val indexedTRAINwithCatFeat = indexerModel.transform(indexedTRAINregDF)
	
	# TEST DATA
	val indexerModel = indexer.fit(indexedTESTbinaryDF)
	val indexedTESTwithCatFeat = indexerModel.transform(indexedTESTregDF)


## Двоичная классификация: прогнозирование вероятности выплаты чаевых за поездку

В этом разделе мы создадим модели двоичной классификации трех типов, чтобы составить прогноз того, будут ли выплачены чаевые за поездку:

- **модель логистической регрессии**, использующую функцию SparkML `LogisticRession()`;
- **модель классификации случайного леса**, использующую функцию машинного обучения Spark `RandomForestClassifier()`;
- **модель классификации по методу увеличивающихся деревьев**, использующую функцию MLlib `GradientBoostedTrees()`.

### Создание модели логистической регрессии

В этом подразделе мы создадим модель логистической регрессии, использующую функцию SparkML `LogisticRession()`. Создание кода сборки модели в этом разделе предполагает такую последовательность шагов:

1. **Обучение модели** с использованием данных с одним набором параметров.
2. **Оценка модели** на основе набора тестовых данных с метриками.
3. **Сохранение модели** в большом двоичном объекте для последующего использования.
4. **Оценка модели** на основе тестовых данных.
5. **Графическое представление результатов** — кривые ROC.

Ниже приведен код для выполнения этих двух процедур.

	# CREATE LOGISTIC REGRESSION MODEL 
	val lr = new LogisticRegression().setLabelCol("tipped").setFeaturesCol("features").setMaxIter(10).setRegParam(0.3).setElasticNetParam(0.8)
	val lrModel = lr.fit(OneHotTRAIN)
	
	# PREDICT ON TEST DATA SET
	val predictions = lrModel.transform(OneHotTEST)
	
	# SELECT BinaryClassificationEvaluator() TO COMPUTE TEST ERROR
	val evaluator = new BinaryClassificationEvaluator().setLabelCol("tipped").setRawPredictionCol("probability").setMetricName("areaUnderROC")
	val ROC = evaluator.evaluate(predictions)
	println("ROC on test data = " + ROC)
	
	# SAVE THE MODEL
	val datestamp = Calendar.getInstance().getTime().toString.replaceAll(" ", ".").replaceAll(":", "_");
	val modelName = "LogisticRegression__"
	val filename = modelDir.concat(modelName).concat(datestamp)
	lrModel.save(filename);

Теперь загрузите, оцените и сохраните результаты.

	# RECORD START TIME 
	val starttime = Calendar.getInstance().getTime()

	# LOAD SAVED MODEL AND SCORE THE TEST DATA SET
	val savedModel = org.apache.spark.ml.classification.LogisticRegressionModel.load(filename)
	println(s"Coefficients: ${savedModel.coefficients} Intercept: ${savedModel.intercept}")
	
	# SCORE THE MODEL ON THE TEST DATA
	val predictions = savedModel.transform(OneHotTEST).select("tipped","probability","rawPrediction")
	predictions.registerTempTable("testResults")
	
	# SELECT BinaryClassificationEvaluator() TO COMPUTE TEST ERROR
	val evaluator = new BinaryClassificationEvaluator().setLabelCol("tipped").setRawPredictionCol("probability").setMetricName("areaUnderROC")
	val ROC = evaluator.evaluate(predictions)
	
	# GET TIME TO RUN THE CELL
	val endtime = Calendar.getInstance().getTime()
	val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
	println("Time taken to run the above cell: " + elapsedtime + " seconds.");
	
	# PRINT THE ROC RESULTS
	println("ROC on test data = " + ROC)


**ВЫХОДНЫЕ ДАННЫЕ:**

ROC для тестовых данных — 0,9827381497557599.


Примените код Python к таблицам данных pandas, чтобы построить кривую ROC.


	# QUERY RESULTS
	%%sql -q -o sqlResults
	SELECT tipped, probability from testResults


	# RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
	%%local
	%matplotlib inline
	from sklearn.metrics import roc_curve,auc
	
	sqlResults['probFloat'] = sqlResults.apply(lambda row: row['probability'].values()[0][1], axis=1)
	predictions_pddf = sqlResults[["tipped","probFloat"]]
	
	# ROC CURVE
	# predictions_pddf = sqlResults.rename(columns={'_1': 'probability', 'tipped': 'label'})
	prob = predictions_pddf["probFloat"] 
	fpr, tpr, thresholds = roc_curve(predictions_pddf['tipped'], prob, pos_label=1);
	roc_auc = auc(fpr, tpr)
	
	#PLOT
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


**ВЫХОДНЫЕ ДАННЫЕ:**

![Кривая ROC вероятности выплаты чаевых](./media/machine-learning-data-science-process-scala-walkthrough/plot-roc-curve-tip-or-not.png)


### Создание модели классификации случайного леса

Теперь мы создадим модель классификации случайного леса, использующую функцию машинного обучения Spark `RandomForestClassifier()`, и оценим модель на основе тестовых данных.


	# RECORD START TIME 
	val starttime = Calendar.getInstance().getTime()
	
	# CREATE THE RANDOM FOREST CLASSIFIER MODEL
	val rf = new RandomForestClassifier().setLabelCol("labelBin").setFeaturesCol("featuresCat").setNumTrees(10).setSeed(1234)
	
	# FIT THE MODEL
	val rfModel = rf.fit(indexedTRAINwithCatFeatBinTarget)
	val predictions = rfModel.transform(indexedTESTwithCatFeatBinTarget)
	
	# EVALUATE THE MODEL
	val evaluator = new MulticlassClassificationEvaluator().setLabelCol("label").setPredictionCol("prediction").setMetricName("f1")
	val Test_f1Score = evaluator.evaluate(predictions)
	println("F1 score on test data: " + Test_f1Score);
	
	# GET TIME TO RUN THE CELL
	val endtime = Calendar.getInstance().getTime()
	val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
	println("Time taken to run the above cell: " + elapsedtime + " seconds.");
	
	# CALCULATE BINARY CLASSIFICATION EVALUATION METRICSS
	val evaluator = new BinaryClassificationEvaluator().setLabelCol("label").setRawPredictionCol("probability").setMetricName("areaUnderROC")
	val ROC = evaluator.evaluate(predictions)
	println("ROC on test data = " + ROC)


**ВЫХОДНЫЕ ДАННЫЕ:**

ROC для тестовых данных — 0,9847103571552683.


### Создание модели классификации по методу увеличивающихся деревьев

В этом подразделе мы создадим модель классификации по методу увеличивающихся деревьев, использующую функцию MLlib `GradientBoostedTrees()`, и оценим модель на основе тестовых данных.


	# TRAIN A GBT CLASSIFICATION MODEL USING MLIB AND LABELEDPOINT

	# RECORD START TIME
	val starttime = Calendar.getInstance().getTime()
	
	# DEFINE THE GBT CLASSIFICATION MODEL
	val boostingStrategy = BoostingStrategy.defaultParams("Classification")
	boostingStrategy.numIterations = 20
	boostingStrategy.treeStrategy.numClasses = 2
	boostingStrategy.treeStrategy.maxDepth = 5
	boostingStrategy.treeStrategy.categoricalFeaturesInfo = Map[Int, Int]((0,2),(1,2),(2,6),(3,4))
	
	# TRAIN THE MODEL
	val gbtModel = GradientBoostedTrees.train(indexedTRAINbinary, boostingStrategy)
	
	# SAVE THE MODEL IN BLOB STORAGE
	val datestamp = Calendar.getInstance().getTime().toString.replaceAll(" ", ".").replaceAll(":", "_");
	val modelName = "GBT_Classification__"
	val filename = modelDir.concat(modelName).concat(datestamp)
	gbtModel.save(sc, filename);
	
	# EVALUATE THE MODEL ON TEST INSTANCES AND THE COMPUTE TEST ERROR
	val labelAndPreds = indexedTESTbinary.map { point =>
	  val prediction = gbtModel.predict(point.features)
	  (point.label, prediction)
	}
	val testErr = labelAndPreds.filter(r => r._1 != r._2).count.toDouble / indexedTRAINbinary.count()
	//println("Learned classification GBT model:\n" + gbtModel.toDebugString)
	println("Test Error = " + testErr)
	
	# USE BINARY AND MULTICLASS METRICS TO EVALUATE THE MODEL ON THE TEST DATA
	val metrics = new MulticlassMetrics(labelAndPreds)
	println(s"Precision: ${metrics.precision}")
	println(s"Recall: ${metrics.recall}")
	println(s"F1 Score: ${metrics.fMeasure}")
	
	val metrics = new BinaryClassificationMetrics(labelAndPreds)
	println(s"Area under PR curve: ${metrics.areaUnderPR}")
	println(s"Area under ROC curve: ${metrics.areaUnderROC}")
	
	# GET TIME TO RUN THE CELL
	val endtime = Calendar.getInstance().getTime()
	val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
	println("Time taken to run the above cell: " + elapsedtime + " seconds.");
	
	# PRINT THE ROC METRIC
	println(s"Area under ROC curve: ${metrics.areaUnderROC}")


**ВЫХОДНЫЕ ДАННЫЕ:**

площадь под ROC — 0,9846895479241554.


## Регрессия: прогнозирование суммы чаевых 

В этом разделе мы создадим модели регрессии двух типов, чтобы спрогнозировать сумму чаевых:

- **модель регуляризованной линейной регрессии**, использующую функцию машинного обучения Spark `LinearRegression()`, сохраним модель и оценим ее на основе тестовых данных;
- **модель регрессии по методу увеличивающихся деревьев**, использующую функцию машинного обучения Spark `GBTRegressor() `.


### Создание регуляризованной линейной регрессии

	# RECORD START TIME
	val starttime = Calendar.getInstance().getTime()
	
	# CREATE REGULARIZED LINEAR REGRESSION MODEL USING SPARK ML FUNCTION AND DATA-FRAME
	val lr = new LinearRegression().setLabelCol("tip_amount").setFeaturesCol("features").setMaxIter(10).setRegParam(0.3).setElasticNetParam(0.8)
	
	# FIT THE MODEL USING DATA-FRAME
	val lrModel = lr.fit(OneHotTRAIN)
	println(s"Coefficients: ${lrModel.coefficients} Intercept: ${lrModel.intercept}")
	
	# SUMMARIZE THE MODEL OVER THE TRAIINNG SET AND PRINT OUT SOME METRICS
	val trainingSummary = lrModel.summary
	println(s"numIterations: ${trainingSummary.totalIterations}")
	println(s"objectiveHistory: ${trainingSummary.objectiveHistory.toList}")
	trainingSummary.residuals.show()
	println(s"RMSE: ${trainingSummary.rootMeanSquaredError}")
	println(s"r2: ${trainingSummary.r2}")
	
	# SAVE THE MODEL IN BLOB STORAGE
	val datestamp = Calendar.getInstance().getTime().toString.replaceAll(" ", ".").replaceAll(":", "_");
	val modelName = "LinearRegression__"
	val filename = modelDir.concat(modelName).concat(datestamp)
	lrModel.save(filename);
	
	# PRINT COEFFICIENTS
	println(s"Coefficients: ${lrModel.coefficients} Intercept: ${lrModel.intercept}")
	
	# SCORE THE MODEL ON TEST DATA
	val predictions = lrModel.transform(OneHotTEST)
	
	# EVALUATE THE MODEL ON TEST DATA
	val evaluator = new RegressionEvaluator().setLabelCol("tip_amount").setPredictionCol("prediction").setMetricName("r2")
	val r2 = evaluator.evaluate(predictions)
	println("R-sqr on test data = " + r2)
	
	# GET TIME TO RUN THE CELL
	val endtime = Calendar.getInstance().getTime()
	val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
	println("Time taken to run the above cell: " + elapsedtime + " seconds.");


**ВЫХОДНЫЕ ДАННЫЕ:**

время, затраченное на выполнение кода выше, — 13 секунд.


	# LOAD A SAVED LINEARREGRESSION MODEL FROM BLOB STORAGE AND SCORE A TEST DATA SET. 

	# RECORD START TIME
	val starttime = Calendar.getInstance().getTime()
	
	# LOAD A SAVED LINEARREGRESSION MODEL FROM BLOB STORAGE
	val savedModel = org.apache.spark.ml.regression.LinearRegressionModel.load(filename)
	println(s"Coefficients: ${savedModel.coefficients} Intercept: ${savedModel.intercept}")
	
	# SCORE THE MODEL ON TEST DATA
	val predictions = savedModel.transform(OneHotTEST).select("tip_amount","prediction")
	predictions.registerTempTable("testResults")
	
	# EVALUATE THE MODEL ON TEST DATA
	val evaluator = new RegressionEvaluator().setLabelCol("tip_amount").setPredictionCol("prediction").setMetricName("r2")
	val r2 = evaluator.evaluate(predictions)
	println("R-sqr on test data = " + r2)
	
	# GET TIME TO RUN THE CELL
	val endtime = Calendar.getInstance().getTime()
	val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
	println("Time taken to run the above cell: " + elapsedtime + " seconds.");
	
	# PRINT RESULTS
	println("R-sqr on test data = " + r2)


**ВЫХОДНЫЕ ДАННЫЕ:**

R-sqr для тестовых данных — 0,5960320470835743.


Далее запросите результаты теста в качестве таблицы данных и визуализируйте данные, используя "autoviz" Jupyter и "matplotlib" Python.


	# SQL QUERY
	%%sql -q -o sqlResults
	select * from testResults

	# RUN THE CODE LOCALLY ON THE JUPYTER SERVER
	%%local

	# USE THE JUPYTER AUTO-PLOTTING FEATURE TO CREATE INTERACTIVE FIGURES. 
	# CLICK ON THE TYPE OF PLOT TO BE GENERATED (E.G. LINE, AREA, BAR ETC.)
	sqlResults

Этот код создает локальную таблицу данных из выходных данных запроса и формирует графическое представление данных. Волшебная команда `%%local` создает локальную таблицу данных `sqlResults`, с помощью которой можно сформировать графическое представление данных с использованием matplotlib.

>[AZURE.NOTE] В этом пошаговом руководстве волшебная команда Spark используется несколько раз. Если объем данных большой, сделайте выборку, чтобы создать фрейм данных, который можно разместить в локальной памяти.

Создайте график, используя "matplotlib" Python.

	# RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
	%%local
	sqlResults
	%matplotlib inline
	import numpy as np
	
	# PLOT THE RESULTS
	ax = sqlResults.plot(kind='scatter', figsize = (6,6), x='tip_amount', y='prediction', color='blue', alpha = 0.25, label='Actual vs. predicted');
	fit = np.polyfit(sqlResults['tip_amount'], sqlResults['prediction'], deg=1)
	ax.set_title('Actual vs. Predicted Tip Amounts ($)')
	ax.set_xlabel("Actual")
	ax.set_ylabel("Predicted")
	#ax.plot(sqlResults['tip_amount'], fit[0] * sqlResults['prediction'] + fit[1], color='magenta')
	plt.axis([-1, 15, -1, 8])
	plt.show(ax)

**ВЫХОДНЫЕ ДАННЫЕ:**

![Сумма чаевых: соотношение фактической и прогнозируемой суммы](./media/machine-learning-data-science-process-scala-walkthrough/plot-actual-vs-predicted-tip-amount.png)


### Создание модели регрессии по методу увеличивающихся деревьев

В этом подразделе мы создадим модель регрессии по методу увеличивающихся деревьев, использующую функцию Spark `ML GBTRegressor()`, и оценим модель на основе тестовых данных.

[Увеличивающиеся деревья принятия решений](http://spark.apache.org/docs/latest/ml-classification-regression.html#gradient-boosted-trees-gbts) — это метод, заключающийся в использовании комитета деревьев принятия решений. Этот метод предусматривает итеративное обучение деревьев принятия решений, что позволяет свести потери к минимуму. Он применяется для задач классификации и регрессии. С его помощью можно обрабатывать категориальные признаки, а также определять нелинейные зависимости и взаимодействия признаков. Этот метод не требует масштабирования признаков. Кроме того, его можно использовать для создания модели мультиклассовой классификации.


	# RECORD START TIME
	val starttime = Calendar.getInstance().getTime()
	
	# TRAIN A GBT REGRESSION MODEL
	val gbt = new GBTRegressor().setLabelCol("label").setFeaturesCol("featuresCat").setMaxIter(10)
	val gbtModel = gbt.fit(indexedTRAINwithCatFeat)
	
	# MAKE PREDICTIONS
	val predictions = gbtModel.transform(indexedTESTwithCatFeat)
	
	# COMPUTE TEST SET R2
	val evaluator = new RegressionEvaluator().setLabelCol("label").setPredictionCol("prediction").setMetricName("r2")
	val Test_R2 = evaluator.evaluate(predictions)
	
	
	# GET TIME TO RUN THE CELL
	val endtime = Calendar.getInstance().getTime()
	val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
	println("Time taken to run the above cell: " + elapsedtime + " seconds.");
	
	# PRINT RESULTS
	println("Test R-sqr is: " + Test_R2);


**ВЫХОДНЫЕ ДАННЫЕ:**

Test R-sqr — 0,7655383534596654.



## Служебные программы расширенного моделирования для оптимизации

В этом разделе мы покажем, как использовать служебные программы машинного обучения, которые часто применяют для оптимизации моделей. В частности, мы рассмотрим три разных способа оптимизации модели машинного обучения с использованием перебора параметров и перекрестной проверки:

1\. Разбиение данных на наборы данных для обучения и для проверки, оптимизация модели с использованием перебора гиперпараметров набора данных для обучения и оценки проверочного набора (линейная регрессия).

2\. Оптимизация модели с помощью перекрестной проверки и перебора гиперпараметров с использованием функции машинного обучения Spark "CrossValidator" (двоичная классификация).

3\. Оптимизация модели с помощью настраиваемого кода перекрестной проверки и перебора гиперпараметров, чтобы она использовала любые функции машинного обучения и наборы параметров (линейная регрессия).


**Перекрестная проверка** — это метод, который позволяет оценить, насколько хорошо модель, обученная на известном наборе данных, будет обобщаться для прогнозирования признаков наборов данных, на которых она не прошла обучение. Суть этого метода в том, что модель обучается на наборе известных данных, а затем точность ее прогнозов тестируется на независимом наборе данных. Здесь используется распространенная реализация, разделяющая набор данных на K сверток и затем обучающая модель путем циклического перебора по всем сверткам, кроме одной.

**Оптимизация гиперпараметров** — это задача выбора набора гиперпараметров для алгоритма обучения обычно с целью оптимизации меры производительности алгоритма на независимом наборе данных. **Гиперпараметры** — это значения, которые должны быть указаны вне процедуры обучения модели. Предположения относительно этих значений могут повлиять на гибкость и точность моделей. Деревья принятия решений содержат гиперпараметры, такие как требуемая глубина и число листьев в дереве. Для методов опорных векторов (SVM) необходимо задать условие штрафа за неправильную классификацию.

Распространенный способ оптимизации гиперпараметров, который используется здесь, — поиск в сетке или **перебор параметров**. Он заключается в выполнении тщательного поиска по значениям указанного подмножества пространства гиперпараметров для алгоритма обучения. Перекрестная проверка может предоставить метрику производительности, чтобы отсортировать оптимальные результаты, выданные алгоритмом поиска в сетке. Использование перекрестной проверки с перебором гиперпараметров помогает ограничить такие проблемы, как лжевзаимосвязи модели с обучающими данными, чтобы модель сохраняла возможность применения к общему набору данных, из которого извлекаются данные для обучения.

### Оптимизация модели линейной регрессии с помощью перебора гиперпараметров

Разбейте данные на набор данных для обучения и набор для проверки, оптимизируйте модель, используя перебор гиперпараметров набора обучающих данных и оценку набора проверки (линейная регрессия).

	# RECORD START TIME
	val starttime = Calendar.getInstance().getTime()
	
	# RENAME tip_amount AS LABEL
	val OneHotTRAINLabeled = OneHotTRAIN.select("tip_amount","features").withColumnRenamed(existingName="tip_amount",newName="label") 
	val OneHotTESTLabeled = OneHotTEST.select("tip_amount","features").withColumnRenamed(existingName="tip_amount",newName="label")
	OneHotTRAINLabeled.cache()
	OneHotTESTLabeled.cache()
	
	# DEFINE THE ESTIMATOR FUNCTION: THE LinearRegression() FUNCTION
	val lr = new LinearRegression().setLabelCol("label").setFeaturesCol("features").setMaxIter(10)
	
	# DEFINE THE PARAMETER GRID
	val paramGrid = new ParamGridBuilder().addGrid(lr.regParam, Array(0.1, 0.01, 0.001)).addGrid(lr.fitIntercept).addGrid(lr.elasticNetParam, Array(0.1, 0.5, 0.9)).build()
	
	# DEFINE PIPELINE WITH TRAIN-TEST VALIDATION SPLIT, WITH 75% IN THE TRAIING SET, SPECIFY ESTIMATOR, EVALUATOR, AND PARAMETER GRID
	val trainPct = 0.75
	val trainValidationSplit = new TrainValidationSplit().setEstimator(lr).setEvaluator(new RegressionEvaluator).setEstimatorParamMaps(paramGrid).setTrainRatio(trainPct)
	
	# RUN THE TRAIN VALIDATION SPLIT AND CHOOSE THE BEST SET OF PARAMETERS
	val model = trainValidationSplit.fit(OneHotTRAINLabeled)
	
	# MAKE PREDICTIONS ON THE TEST DATA USING THE MODEL WITH THE COMBINATION OF PARAMETERS THAT PERFORM THE BEST
	val testResults = model.transform(OneHotTESTLabeled).select("label", "prediction")
	
	# COMPUTE TEST SET R2
	val evaluator = new RegressionEvaluator().setLabelCol("label").setPredictionCol("prediction").setMetricName("r2")
	val Test_R2 = evaluator.evaluate(testResults)
	
	# GET TIME TO RUN THE CELL
	val endtime = Calendar.getInstance().getTime()
	val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
	println("Time taken to run the above cell: " + elapsedtime + " seconds.");
	
	println("Test R-sqr is: " + Test_R2);


**ВЫХОДНЫЕ ДАННЫЕ:**

Test R-sqr — 0,6226484708501209


### Оптимизация модели двоичной классификации с использованием перекрестной проверки и перебора гиперпараметров

В этом подразделе мы покажем, как оптимизировать модель двоичной классификации, используя перекрестную проверку и перебор гиперпараметров. Для этого используется функция машинного обучения Spark "CrossValidator".

	# RECORD START TIME
	val starttime = Calendar.getInstance().getTime()
	
	# CREATE DATA-FRAMES WITH PROPERLY LABELED COLUMNS FOR USE WITH TRAIN/TEST SPLIT
	val indexedTRAINwithCatFeatBinTargetRF = indexedTRAINwithCatFeatBinTarget.select("labelBin","featuresCat").withColumnRenamed(existingName="labelBin",newName="label").withColumnRenamed(existingName="featuresCat",newName="features")
	val indexedTESTwithCatFeatBinTargetRF = indexedTESTwithCatFeatBinTarget.select("labelBin","featuresCat").withColumnRenamed(existingName="labelBin",newName="label").withColumnRenamed(existingName="featuresCat",newName="features")
	indexedTRAINwithCatFeatBinTargetRF.cache()
	indexedTESTwithCatFeatBinTargetRF.cache()
	
	# DEFINE THE ESTIMATOR FUNCTION
	val rf = new RandomForestClassifier().setLabelCol("label").setFeaturesCol("features").setImpurity("gini").setSeed(1234).setFeatureSubsetStrategy("auto").setMaxBins(32)
	
	# DEFINE THE PARAMETER GRID
	val paramGrid = new ParamGridBuilder().addGrid(rf.maxDepth, Array(4,8)).addGrid(rf.numTrees, Array(5,10)).addGrid(rf.minInstancesPerNode, Array(100,300)).build()
	
	# SPECIFY THE NUMBER OF FOLDS
	val numFolds = 3
	
	# DEFINE THE TRAIN-TEST VALIDATION SPLIT WITH 75% IN THE TRAIING SET
	val CrossValidator = new CrossValidator().setEstimator(rf).setEvaluator(new BinaryClassificationEvaluator).setEstimatorParamMaps(paramGrid).setNumFolds(numFolds)
	
	# RUN THE TRAIN VALIDATION SPLIT AND CHOOSE THE BEST SET OF PARAMETERS
	val model = CrossValidator.fit(indexedTRAINwithCatFeatBinTargetRF)
	
	# MAKE PREDICTIONS ON THE TEST DATA USING THE MODEL WITH THE COMBINATION OF PARAMETERS THAT PERFORM THE BEST
	val testResults = model.transform(indexedTESTwithCatFeatBinTargetRF).select("label", "prediction")
	
	# COMPUTE TEST F1 SCORE
	val evaluator = new MulticlassClassificationEvaluator().setLabelCol("label").setPredictionCol("prediction").setMetricName("f1")
	val Test_f1Score = evaluator.evaluate(testResults)
	
	# GET TIME TO RUN THE CELL
	val endtime = Calendar.getInstance().getTime()
	val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
	println("Time taken to run the above cell: " + elapsedtime + " seconds.");


**ВЫХОДНЫЕ ДАННЫЕ:**

время, затраченное на выполнение кода выше, — 33 секунды.


### Оптимизация модели линейной регрессии с использованием пользовательского кода перекрестной проверки и перебора параметров

В этом подразделе мы оптимизируем модель с помощью пользовательского кода и определим оптимальные параметры модели, используя критерий высокой точности. Затем мы создадим окончательную модель, оценим ее на основе тестовых данных и сохраним в хранилище BLOB-объектов. Наконец, мы загрузим модель, оценим тестовые данные и точность.

	# RECORD START TIME
	val starttime = Calendar.getInstance().getTime()
	
	# DEFINE PARAMETER GRID AND THE NUMBER OF FOLDS
	val paramGrid = new ParamGridBuilder().addGrid(rf.maxDepth, Array(5,10)).addGrid(rf.numTrees, Array(10,25,50)).build()
	
	val nFolds = 3
	val numModels = paramGrid.size
	val numParamsinGrid = 2
	
	# SPECIFY THE NUMBER OF CATEGORIES FOR THE CATEGORIAL VARIBLES
	val categoricalFeaturesInfo = Map[Int, Int]((0,2),(1,2),(2,6),(3,4))
	
	var maxDepth = -1
	var numTrees = -1
	var param = ""
	var paramval = -1
	var validateLB = -1.0
	var validateUB = -1.0
	val h = 1.0 / nFolds;
	val RMSE  = Array.fill(numModels)(0.0)
	
	# CREATE K FOLDS
	val splits = MLUtils.kFold(indexedTRAINbinary, numFolds = nFolds, seed=1234)
	
	
	# LOOP THROUGH K-FOLDS AND THE PARAMETER GRID TO GET AND IDENTIFY THE BEST PARAMETER SET BY ACCURACY
	for (i <- 0 to (nFolds-1)) {
	    validateLB = i * h
	    validateUB = (i + 1) * h
	    val validationCV = trainData.filter($"rand" >= validateLB  && $"rand" < validateUB)
	    val trainCV = trainData.filter($"rand" < validateLB  || $"rand" >= validateUB)
	    val validationLabPt = validationCV.rdd.map(r => LabeledPoint(r.getDouble(targetIndRegression(0).toInt), Vectors.dense(featuresIndIndex.map(r.getDouble(_)).toArray)));
	    val trainCVLabPt = trainCV.rdd.map(r => LabeledPoint(r.getDouble(targetIndRegression(0).toInt), Vectors.dense(featuresIndIndex.map(r.getDouble(_)).toArray)));
	    validationLabPt.cache()
	    trainCVLabPt.cache()
	
	    for (nParamSets <- 0 to (numModels-1)) {
	        for (nParams <- 0 to (numParamsinGrid-1)) {
	            param = paramGrid(nParamSets).toSeq(nParams).param.toString.split("__")(1)
	            paramval = paramGrid(nParamSets).toSeq(nParams).value.toString.toInt
	            if (param == "maxDepth") {maxDepth = paramval}
	            if (param == "numTrees") {numTrees = paramval}
	        }
	        val rfModel = RandomForest.trainRegressor(trainCVLabPt, categoricalFeaturesInfo=categoricalFeaturesInfo,
	                                                  numTrees=numTrees, maxDepth=maxDepth,
	                                                  featureSubsetStrategy="auto",impurity="variance", maxBins=32)
	        val labelAndPreds = validationLabPt.map { point =>
	                                                 val prediction = rfModel.predict(point.features)
	                                                 ( prediction, point.label )
	                                                }
	        val validMetrics = new RegressionMetrics(labelAndPreds)
	        val rmse = validMetrics.rootMeanSquaredError
	        RMSE(nParamSets) += rmse
	    }
	    validationLabPt.unpersist();
	    trainCVLabPt.unpersist();
	}
	val minRMSEindex = RMSE.indexOf(RMSE.min)
	
	# GET BEST PARAMETERS FROM CV AND PARAMETER SWEEP 
	var best_maxDepth = -1
	var best_numTrees = -1
	for (nParams <- 0 to (numParamsinGrid-1)) {
	    param = paramGrid(minRMSEindex).toSeq(nParams).param.toString.split("__")(1)
	    paramval = paramGrid(minRMSEindex).toSeq(nParams).value.toString.toInt
	    if (param == "maxDepth") {best_maxDepth = paramval}
	    if (param == "numTrees") {best_numTrees = paramval}
	}
	
	# CREATE BEST MODEL WITH BEST PARAMETERS AND FULL TRAIING DATASET
	val best_rfModel = RandomForest.trainRegressor(indexedTRAINreg, categoricalFeaturesInfo=categoricalFeaturesInfo,
	                                                  numTrees=best_numTrees, maxDepth=best_maxDepth,
	                                                  featureSubsetStrategy="auto",impurity="variance", maxBins=32)
	
	# SAVE BEST RF MODEL IN BLOB STORAGE
	val datestamp = Calendar.getInstance().getTime().toString.replaceAll(" ", ".").replaceAll(":", "_");
	val modelName = "BestCV_RF_Regression__"
	val filename = modelDir.concat(modelName).concat(datestamp)
	best_rfModel.save(sc, filename);
	
	# PREDICT ON TRAINING SET WITH BEST MODEL AND EVALUATE
	val labelAndPreds = indexedTESTreg.map { point =>
	                                        val prediction = best_rfModel.predict(point.features)
	                                        ( prediction, point.label )
	                                       }
	
	val test_rmse = new RegressionMetrics(labelAndPreds).rootMeanSquaredError
	val test_rsqr = new RegressionMetrics(labelAndPreds).r2
	
	# GET TIME TO RUN THE CELL
	val endtime = Calendar.getInstance().getTime()
	val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
	println("Time taken to run the above cell: " + elapsedtime + " seconds.");


	# LOAD THE MODEL
	val savedRFModel = RandomForestModel.load(sc, filename)
	
	val labelAndPreds = indexedTESTreg.map { point =>
	                                        val prediction = savedRFModel.predict(point.features)
	                                        ( prediction, point.label )
	                                       }
	# TEST THE MODEL
	val test_rmse = new RegressionMetrics(labelAndPreds).rootMeanSquaredError
	val test_rsqr = new RegressionMetrics(labelAndPreds).r2


**ВЫХОДНЫЕ ДАННЫЕ:**

время, затраченное на выполнение кода выше, — 61 секунда.


## Автоматическое использование моделей машинного обучения на основе Spark с кодом Scala

Процедура использования кода Scala для автоматической загрузки и оценки новых наборов данных с помощью встроенных моделей машинного обучения Spark с последующим сохранением в большом двоичном объекте Azure описана в статье [Оценка моделей машинного обучения, созданных с помощью Spark](machine-learning-data-science-spark-model-consumption.md). Чтобы включить автоматическое использование, пользователи могут выполнить инструкции, указанные в этой статье, и просто заменить код Python кодом Scala, указанным выше.

## Что дальше?

Обзор разделов с пошаговыми руководствами по задачам, которые входят в процесс обработки и анализа данных в Azure, см. в статье [Процесс обработки и анализа данных группы](http://aka.ms/datascienceprocess).

Описание других пошаговых руководств, в каждом их которых показаны шаги процесса обработки и анализа данных группы для **конкретных сценариев**,а также описание того, как объединять облачные и локальные инструменты и службы в рабочий процесс или конвейер, чтобы создать интеллектуальное приложение, см. в разделе [Пошаговые руководства по процессу обработки и анализа данных группы](data-science-process-walkthroughs.md).

<!---HONumber=AcomDC_0803_2016-->
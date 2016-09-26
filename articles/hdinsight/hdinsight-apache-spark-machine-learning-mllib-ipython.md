<properties 
	pageTitle="Использование Apache Spark для создания приложений машинного обучения в HDInsight | Microsoft Azure" 
	description="Пошаговые инструкции по использованию записных книжек Apache Spark для создания приложений машинного обучения" 
	services="hdinsight" 
	documentationCenter="" 
	authors="nitinme" 
	manager="jhubbard" 
	editor="cgronlun"
	tags="azure-portal"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/25/2016" 
	ms.author="nitinme"/>


# Машинное обучение. Прогнозный анализ на основе данных контроля качества пищевых продуктов с использованием MLlib и кластера Apache Spark в HDInsight на платформе Linux

> [AZURE.TIP] Кроме того, это руководство доступно в виде записной книжки Jupyter в кластере Spark (на платформе Linux), созданном в HDInsight. Фрагменты кода Python можно выполнять непосредственно в записной книжке. Чтобы выполнить действия в руководстве из записной книжки, создайте кластер Spark, запустите записную книжку Jupyter (`https://CLUSTERNAME.azurehdinsight.net/jupyter`), а затем откройте записную книжку **Машинное обучение Spark. Прогнозный анализ на основе данных контроля качества пищевых продуктов с использованием MLlib.ipynb** в папке **Python**.


В этой статье показано, как использовать встроенные библиотеки машинного обучения Spark **MLlib** для выполнения простого прогнозного анализа на основе открытого набора данных. Библиотека MLlib — это основной компонент Spark, содержащий несколько служебных программ, которые подходят для задач машинного обучения, в частности:

* Классификация

* Регрессия

* Кластеризация

* тематического моделирования;

* сингулярного разложения и анализа по методу главных компонент;

* проверки гипотез и статистической выборки.

В этой статье представлен простой подход к *классификации* — с использованием метода логистической регрессии.

## Сведения о классификации и логистической регрессии

*Классификация* — это распространенная задача машинного обучения, которая представляет собой процесс сортировки входных данных по категориям. Алгоритм классификации определяет принцип назначения «меток» предоставленным входным данных. Например, можно создать алгоритм машинного обучения, который принимает в качестве входных данных информацию об акциях и делит акции на две категории: акции, которые следует продать, и акции, которые стоит оставить.

Логистическая регрессия — один из алгоритмов классификации. API Spark для логистической регрессии подходит для задач *двоичной классификации* или разделения входных данных на две группы. Дополнительные сведения о логистической регрессии см. в статье [Википедии](https://en.wikipedia.org/wiki/Logistic_regression).

В общих словах, в основе логистической регрессии лежит некая *логистическая функция*, используемая для прогнозирования вероятности принадлежности вектора входных данных к одной или другой группе.

## Задачи, которые позволяет решить эта статья

В этом руководстве описывается использование Spark для выполнения прогнозного анализа на основе данных контроля качества пищевых продуктов (**Food\_Inspections1.csv**), полученных на [информационном портале Чикаго](https://data.cityofchicago.org/). Этот набор данных содержит сведения о проверках качества пищевых продуктов, проведенных в Чикаго, в частности информацию о каждом проверенном предприятии общественного питания, обнаруженных нарушениях (если таковые были) и результатах проверки. CSV-файл данных уже доступен в учетной записи хранения, связанной с кластером: **/HdiSamples/HdiSamples/FoodInspectionData/Food\_Inspections1.csv**.

В следующих разделах описаны шаги по разработке модели, которая позволит узнать, что нужно, чтобы пройти контроль качества пищевых продуктов.

## Приступая к созданию приложения машинного обучения с использованием Spark MLlib

1. На начальной панели [портала Azure](https://portal.azure.com/) щелкните элемент кластера Spark (если он закреплен на начальной панели). Кроме того, вы можете перейти к кластеру, выбрав пункты **Просмотреть все** и **Кластеры HDInsight**.

2. В колонке кластера Spark щелкните **Быстрые ссылки**, затем в колонке **Панель мониторинга кластера** выберите **Jupyter Notebook**. При появлении запроса введите учетные данные администратора для кластера.

	> [AZURE.NOTE] Также можно открыть Jupyter Notebook для своего кластера, открыв следующий URL-адрес в браузере. Замените __CLUSTERNAME__ именем кластера.
	>
	> `https://CLUSTERNAME.azurehdinsight.net/jupyter`

2. Создайте новую записную книжку. Щелкните **Создать**, а затем выберите **PySpark**.

	![Создание новой записной книжки Jupyter](./media/hdinsight-apache-spark-machine-learning-mllib-ipython/hdispark.note.jupyter.createnotebook.png "Создание новой записной книжки Jupyter")

3. Будет создана и открыта записная книжка с именем Untitled.pynb. Щелкните имя записной книжки в верхней части страницы сверху и введите понятное имя.

	![Указание имени для записной книжки](./media/hdinsight-apache-spark-machine-learning-mllib-ipython/hdispark.note.jupyter.notebook.name.png "Указание имени для записной книжки")

3. Так как записная книжка была создана с помощью ядра PySpark, задавать контексты явно необязательно. Контексты Spark и Hive будут созданы автоматически при выполнении первой ячейки кода. Вы можете приступить к созданию своего приложения машинного обучения, импортировав типы, необходимые для этого сценария. Для этого наведите курсор на ячейку и нажмите клавиши **SHIFT+ВВОД**.


		from pyspark.ml import Pipeline
		from pyspark.ml.classification import LogisticRegression
		from pyspark.ml.feature import HashingTF, Tokenizer
		from pyspark.sql import Row
		from pyspark.sql.functions import UserDefinedFunction
		from pyspark.sql.types import *

## Создание входной таблицы данных

Для преобразования структурированных данных можно использовать `sqlContext`. Сначала нужно загрузить пример данных (**Food\_Inspections1.csv**) в *таблицу данных* SQL Spark.

1. Так как изначально данные предоставлены в формате CSV, нужно извлечь каждую строку файла в память как неструктурированный текст с использованием контекста Spark, а затем проанализировать отдельно каждую строку с помощью библиотеки Python в формате CSV.


		def csvParse(s):
		    import csv
		    from StringIO import StringIO
		    sio = StringIO(s)
		    value = csv.reader(sio).next()
		    sio.close()
		    return value
		
		inspections = sc.textFile('wasbs:///HdiSamples/HdiSamples/FoodInspectionData/Food_Inspections1.csv')\
		                .map(csvParse)


2. Теперь у нас есть CSV-файл в качестве устойчивого распределенного набора данных (RDD). Извлечем одну строку из RDD, чтобы разобраться со схемой данных.


		inspections.take(1)


	Вы должны увидеть подобные выходные данные:

	    # -----------------
		# THIS IS AN OUTPUT
		# -----------------

		[['413707',
	      'LUNA PARK INC',
	      'LUNA PARK  DAY CARE',
	      '2049789',
	      "Children's Services Facility",
	      'Risk 1 (High)',
	      '3250 W FOSTER AVE ',
	      'CHICAGO',
	      'IL',
	      '60625',
	      '09/21/2010',
	      'License-Task Force',
	      'Fail',
	      '24. DISH WASHING FACILITIES: PROPERLY DESIGNED, CONSTRUCTED, MAINTAINED, INSTALLED, LOCATED AND OPERATED - Comments: All dishwashing machines must be of a type that complies with all requirements of the plumbing section of the Municipal Code of Chicago and Rules and Regulation of the Board of Health. OBSEVERD THE 3 COMPARTMENT SINK BACKING UP INTO THE 1ST AND 2ND COMPARTMENT WITH CLEAR WATER AND SLOWLY DRAINING OUT. INST NEED HAVE IT REPAIR. CITATION ISSUED, SERIOUS VIOLATION 7-38-030 H000062369-10 COURT DATE 10-28-10 TIME 1 P.M. ROOM 107 400 W. SURPERIOR. | 36. LIGHTING: REQUIRED MINIMUM FOOT-CANDLES OF LIGHT PROVIDED, FIXTURES SHIELDED - Comments: Shielding to protect against broken glass falling into food shall be provided for all artificial lighting sources in preparation, service, and display facilities. LIGHT SHIELD ARE MISSING UNDER HOOD OF  COOKING EQUIPMENT AND NEED TO REPLACE LIGHT UNDER UNIT. 4 LIGHTS ARE OUT IN THE REAR CHILDREN AREA,IN THE KINDERGARDEN CLASS ROOM. 2 LIGHT ARE OUT EAST REAR, LIGHT FRONT WEST ROOM. NEED TO REPLACE ALL LIGHT THAT ARE NOT WORKING. | 35. WALLS, CEILINGS, ATTACHED EQUIPMENT CONSTRUCTED PER CODE: GOOD REPAIR, SURFACES CLEAN AND DUST-LESS CLEANING METHODS - Comments: The walls and ceilings shall be in good repair and easily cleaned. MISSING CEILING TILES WITH STAINS IN WEST,EAST, IN FRONT AREA WEST, AND BY THE 15MOS AREA. NEED TO BE REPLACED. | 32. FOOD AND NON-FOOD CONTACT SURFACES PROPERLY DESIGNED, CONSTRUCTED AND MAINTAINED - Comments: All food and non-food contact equipment and utensils shall be smooth, easily cleanable, and durable, and shall be in good repair. SPLASH GUARDED ARE NEEDED BY THE EXPOSED HAND SINK IN THE KITCHEN AREA | 34. FLOORS: CONSTRUCTED PER CODE, CLEANED, GOOD REPAIR, COVING INSTALLED, DUST-LESS CLEANING METHODS USED - Comments: The floors shall be constructed per code, be smooth and easily cleaned, and be kept clean and in good repair. INST NEED TO ELEVATE ALL FOOD ITEMS 6INCH OFF THE FLOOR 6 INCH AWAY FORM WALL.  ',
	      '41.97583445690982',
	      '-87.7107455232781',
	      '(41.97583445690982, -87.7107455232781)']]


3. Приведенные выше выходные данные позволяют нам составить представление о схеме входного файла. Помимо прочего, в файле содержатся название, тип и адрес каждого учреждения, а также соответствующие данные проверки и места проведения проверок. Давайте выберем несколько подходящих столбцов для прогнозного анализа и сгруппируем результаты в таблицу данных, которой затем воспользуемся для создания временной таблицы.


		schema = StructType([
        StructField("id", IntegerType(), False), 
        StructField("name", StringType(), False), 
        StructField("results", StringType(), False), 
        StructField("violations", StringType(), True)])

		df = sqlContext.createDataFrame(inspections.map(lambda l: (int(l[0]), l[1], l[12], l[13])) , schema)
		df.registerTempTable('CountResults')

4. Теперь у нас есть *таблица данных*, `df`, на основе которой можно выполнять анализ. У нас также есть временная таблица **CountResults**. Она состоит из 4 столбцов: **id**, **name**, **results** и **violations**.
	
	Выполните следующую команду, чтобы получить небольшой пример данных:

		df.show(5)

	Вы должны увидеть подобные выходные данные:

	    # -----------------
		# THIS IS AN OUTPUT
		# -----------------

		+------+--------------------+-------+--------------------+
	    |    id|                name|results|          violations|
	    +------+--------------------+-------+--------------------+
	    |413707|       LUNA PARK INC|   Fail|24. DISH WASHING ...|
	    |391234|       CAFE SELMARIE|   Fail|2. FACILITIES TO ...|
	    |413751|          MANCHU WOK|   Pass|33. FOOD AND NON-...|
	    |413708|BENCHMARK HOSPITA...|   Pass|                    |
	    |413722|           JJ BURGER|   Pass|                    |
	    +------+--------------------+-------+--------------------+

## Используемые данные

1. Давайте подробно рассмотрим содержимое набора данных. Например, что представляют собой различные значения в столбце **results**?


		df.select('results').distinct().show()

	
	Вы должны увидеть подобные выходные данные:

	    # -----------------
		# THIS IS AN OUTPUT
		# -----------------
	
		+--------------------+
	    |             results|
	    +--------------------+
	    |                Fail|
	    |Business Not Located|
	    |                Pass|
	    |  Pass w/ Conditions|
	    |     Out of Business|
	    +--------------------+
    
2. Простой способ понять, каким образом распределены результаты, — применить визуализацию. Во временной таблице **CountResults** уже есть данные. Чтобы лучше понять, как распределяются результаты, можно выполнить следующий SQL-запрос к таблице.

		%%sql -o countResultsdf
		SELECT results, COUNT(results) AS cnt FROM CountResults GROUP BY results

	Волшебное слово `%%sql`, за которым следует `-o countResultsdf`, гарантирует, что вывод запроса сохраняется локально на сервере Jupyter (обычно это головной узел кластера). Выходные данные сохраняются в кадре данных [Pandas](http://pandas.pydata.org/) с именем **countResultsdf**.
	
	Вы должны увидеть подобные выходные данные:
	
	![Результат SQL-запроса](./media/hdinsight-apache-spark-machine-learning-mllib-ipython/query.output.png "Результат SQL-запроса")

	Дополнительные сведения о волшебном слове `%%sql`, а также других волшебных словах, доступных в ядре PySpark, см. в статье [Ядра, доступные для использования записными книжками Jupyter с кластерами Spark в HDInsight (Linux)](hdinsight-apache-spark-jupyter-notebook-kernels.md#why-should-i-use-the-new-kernels).

3. Также можно создать диаграмму с помощью Matplotlib, библиотеки, используемой для визуализации данных. Так как диаграмма должна создаваться из локально сохраненного кадра данных **countResultsdf**, фрагмент кода должен начинаться с волшебного слова `%%local`. Это гарантирует, что код будет выполняться локально на сервере Jupyter.

		%%local
		%matplotlib inline
		import matplotlib.pyplot as plt
		
		
		labels = countResultsdf['results']
		sizes = countResultsdf['cnt']
		colors = ['turquoise', 'seagreen', 'mediumslateblue', 'palegreen', 'coral']
		plt.pie(sizes, labels=labels, autopct='%1.1f%%', colors=colors)
		plt.axis('equal')

	Вы должны увидеть подобные выходные данные:

	![Вывод результатов](./media/hdinsight-apache-spark-machine-learning-mllib-ipython/output_13_1.png)


4. Проверка может дать один из 5 следующих результатов:
	
	* Business not located;
	* Fail;
	* Pass;
	* Pss w/ conditions;
	* Out of Business.

	Наша задача — разработать модель, которая может спрогнозировать результат контроля качества пищевых продуктов, исходя из нарушений. Так как логистическая регрессия — это метод двоичной классификации, целесообразно разделить данные на две категории: **Fail** и **Pass**. Результат Pass w/ Conditions также считается проходным, поэтому при обучении модели мы будет рассматривать его как эквивалентный Pass. Данные с другими результатами (Business Not Located, Out of Business) бесполезны, поэтому мы удалим их из обучающего набора. Эти никак не должно повлиять на анализ, так как они составляют очень небольшой процент от общего результата.

5. Далее нам нужно преобразовать существующую таблицу данных (`df`) в новую таблицу данных, где каждая проверка представлена в виде пары «метка-нарушение». В нашем случае метка `0.0` представляет неудачу, `1.0` — успех, а `-1.0` — другие результаты, помимо этих двух. Отфильтруем все второстепенные результаты при преобразовании в новую таблицу данных.


		def labelForResults(s):
		    if s == 'Fail':
		        return 0.0
		    elif s == 'Pass w/ Conditions' or s == 'Pass':
		        return 1.0
		    else:
		        return -1.0
		label = UserDefinedFunction(labelForResults, DoubleType())
		labeledData = df.select(label(df.results).alias('label'), df.violations).where('label >= 0')


	А сейчас, давайте извлечем одну строку из помеченных данных, чтобы увидеть, как они выглядят.


		labeledData.take(1)


	Вы должны увидеть подобные выходные данные:
	
	    # -----------------
		# THIS IS AN OUTPUT
		# -----------------
	
		[Row(label=0.0, violations=u"41. PREMISES MAINTAINED FREE OF LITTER, UNNECESSARY ARTICLES, CLEANING  EQUIPMENT PROPERLY STORED - Comments: All parts of the food establishment and all parts of the property used in connection with the operation of the establishment shall be kept neat and clean and should not produce any offensive odors.  REMOVE MATTRESS FROM SMALL DUMPSTER. | 35. WALLS, CEILINGS, ATTACHED EQUIPMENT CONSTRUCTED PER CODE: GOOD REPAIR, SURFACES CLEAN AND DUST-LESS CLEANING METHODS - Comments: The walls and ceilings shall be in good repair and easily cleaned.  REPAIR MISALIGNED DOORS AND DOOR NEAR ELEVATOR.  DETAIL CLEAN BLACK MOLD LIKE SUBSTANCE FROM WALLS BY BOTH DISH MACHINES.  REPAIR OR REMOVE BASEBOARD UNDER DISH MACHINE (LEFT REAR KITCHEN). SEAL ALL GAPS.  REPLACE MILK CRATES USED IN WALK IN COOLERS AND STORAGE AREAS WITH PROPER SHELVING AT LEAST 6' OFF THE FLOOR.  | 38. VENTILATION: ROOMS AND EQUIPMENT VENTED AS REQUIRED: PLUMBING: INSTALLED AND MAINTAINED - Comments: The flow of air discharged from kitchen fans shall always be through a duct to a point above the roofline.  REPAIR BROKEN VENTILATION IN MEN'S AND WOMEN'S WASHROOMS NEXT TO DINING AREA. | 32. FOOD AND NON-FOOD CONTACT SURFACES PROPERLY DESIGNED, CONSTRUCTED AND MAINTAINED - Comments: All food and non-food contact equipment and utensils shall be smooth, easily cleanable, and durable, and shall be in good repair.  REPAIR DAMAGED PLUG ON LEFT SIDE OF 2 COMPARTMENT SINK.  REPAIR SELF CLOSER ON BOTTOM LEFT DOOR OF 4 DOOR PREP UNIT NEXT TO OFFICE.")]


## Создание модели логистической регрессии на основе входной таблицы данных

Наша последняя задача — преобразовать помеченные данные в такой формат, в котором их сможет проанализировать модель логистической регрессии. Входные данные для алгоритма логистической регрессии должны представлять собой набор *пар «метка-вектор признаков»*, где «вектор признаков» — это совокупность чисел, представляющая собой своего рода входную точку. Таким образом, нам нужен способ преобразования столбца violations, содержащего полуструктурированные данные и множество текстовых комментариев в свободной форме, в массив действительных чисел, которые может распознать компьютер.

Стандартный метод обработки естественного языка в машинном обучении — назначить каждому отдельному слову индекс, а затем передать вектор в алгоритм машинного обучения, при этом значение каждого индекса должно содержать относительную частоту использования слова в текстовой строке.

MLlib предоставляет простой способ выполнения этой операции. Сначала мы пометим каждую строку нарушений с помощью маркеров, чтобы определить отдельные слова в каждой строке, а затем используем `HashingTF` чтобы преобразовать каждый набор маркеров в вектор признаков, который затем будет передан в алгоритм логистической регрессии для создания модели. Используем оператор pipeline, чтобы выполнить все эти действия последовательно.


	tokenizer = Tokenizer(inputCol="violations", outputCol="words")
	hashingTF = HashingTF(inputCol=tokenizer.getOutputCol(), outputCol="features")
	lr = LogisticRegression(maxIter=10, regParam=0.01)
	pipeline = Pipeline(stages=[tokenizer, hashingTF, lr])
	
	model = pipeline.fit(labeledData)


## Анализ модели с использованием отдельного тестового набора данных

Созданную ранее модель можно использовать, чтобы *спрогнозировать* результаты новой проверки на основе обнаруженных нарушений. Мы обучили эту модель по набору данных **Food\_Inspections1.csv**. Теперь используем другой набор данных, **Food\_Inspections2.csv**, чтобы *оценить* эффективность этой модели на основе новых данных. Другой набор данных (**Food\_Inspections2.csv**) должен быть помещен в контейнер хранилища по умолчанию, связанный с кластером.

1. Приведенный ниже фрагмент кода создает новую таблицу данных, **predictionsDf**, содержащую сформированный моделью прогноз. Фрагмент кода также создает временную таблицу **Predictions** на основе кадра данных.


		testData = sc.textFile('wasbs:///HdiSamples/HdiSamples/FoodInspectionData/Food_Inspections2.csv')\
	             .map(csvParse) \
	             .map(lambda l: (int(l[0]), l[1], l[12], l[13]))
		testDf = sqlContext.createDataFrame(testData, schema).where("results = 'Fail' OR results = 'Pass' OR results = 'Pass w/ Conditions'")
		predictionsDf = model.transform(testDf)
		predictionsDf.registerTempTable('Predictions')
		predictionsDf.columns


	Вы должны увидеть подобные выходные данные:
	
	    # -----------------
		# THIS IS AN OUTPUT
		# -----------------
		
		['id',
	     'name',
	     'results',
	     'violations',
	     'words',
	     'features',
	     'rawPrediction',
	     'probability',
	     'prediction']

2. Чтобы просмотреть пример прогноза, выполните этот фрагмент кода:

		predictionsDf.take(1)

	Появится прогноз для первой записи в тестовом наборе данных.

3. Метод `model.transform()` таким же образом преобразует новые данные и определит, как их классифицировать. Можно выполнить некоторые статистические расчеты, чтобы увидеть, насколько точны полученные прогнозы:


		numSuccesses = predictionsDf.where("""(prediction = 0 AND results = 'Fail') OR 
		                                      (prediction = 1 AND (results = 'Pass' OR 
		                                                           results = 'Pass w/ Conditions'))""").count()
		numInspections = predictionsDf.count()
		
		print "There were", numInspections, "inspections and there were", numSuccesses, "successful predictions"
		print "This is a", str((float(numSuccesses) / float(numInspections)) * 100) + "%", "success rate"

	Данные результата выглядят следующим образом:
	
	    # -----------------
		# THIS IS AN OUTPUT
		# -----------------
	
		There were 9315 inspections and there were 8087 successful predictions
	    This is a 86.8169618894% success rate


	Используя логистическую регрессию Spark, мы создали точную модель, которая прогнозирует вероятность прохождения контроля качества пищевых продуктов для определенного предприятия на основе описаний нарушений на английском языке.

## Создание визуального представления прогноза

Теперь мы можем построить окончательную визуализацию, чтобы наглядно увидеть результаты теста.

1. Начнем с извлечения различных прогнозов и результатов из временной таблицы **Predictions**, созданной ранее. Следующие запросы разделяют выходные данные на *true\_positive*, *false\_positive*, *true\_negative* и *false\_negative*. В приведенных ниже запросах мы отключим визуализацию с помощью `-q`, а также сохраним выходные данные (с помощью `-o`) как кадры данных, которые затем можно будет использовать с волшебным словом `%%local`.

		%%sql -q -o true_positive
		SELECT count(*) AS cnt FROM Predictions WHERE prediction = 0 AND results = 'Fail'

		%%sql -q -o false_positive
		SELECT count(*) AS cnt FROM Predictions WHERE prediction = 0 AND (results = 'Pass' OR results = 'Pass w/ Conditions')

		%%sql -q -o true_negative
		SELECT count(*) AS cnt FROM Predictions WHERE prediction = 1 AND results = 'Fail'

		%%sql -q -o false_negative
		SELECT count(*) AS cnt FROM Predictions WHERE prediction = 1 AND (results = 'Pass' OR results = 'Pass w/ Conditions') 

2. Наконец, используйте следующий фрагмент кода для создания диаграммы с помощью **Matplotlib**.

		%%local
		%matplotlib inline
		import matplotlib.pyplot as plt
		
		labels = ['True positive', 'False positive', 'True negative', 'False negative']
		sizes = [true_positive['cnt'], false_positive['cnt'], false_negative['cnt'], true_negative['cnt']]
		colors = ['turquoise', 'seagreen', 'mediumslateblue', 'palegreen', 'coral']
		plt.pie(sizes, labels=labels, autopct='%1.1f%%', colors=colors)
		plt.axis('equal')
	
	Вы должны увидеть следующие выходные данные.
	
	![Вывод прогноза](./media/hdinsight-apache-spark-machine-learning-mllib-ipython/output_26_1.png)


	На этой диаграмме «положительный» результат представляет собой непройденную проверку, а отрицательный — пройденную.

## Завершение работы записной книжки

Завершив работу с приложением, следует закрыть записную книжку, чтобы освободить ресурсы. Для этого в записной книжке в меню **Файл** выберите пункт **Закрыть и остановить**. Это завершит работу записной книжки и закроет ее.


## <a name="seealso"></a>См. также:


* [Обзор: Apache Spark в Azure HDInsight](hdinsight-apache-spark-overview.md)

### Сценарии

* [Использование Spark со средствами бизнес-аналитики. Выполнение интерактивного анализа данных с использованием Spark в HDInsight с помощью средств бизнес-аналитики](hdinsight-apache-spark-use-bi-tools.md)

* [Использование Spark с машинным обучением. Использование Spark в HDInsight для анализа температуры в здании на основе данных системы кондиционирования](hdinsight-apache-spark-ipython-notebook-machine-learning.md)

* [Потоковая передача Spark. Использование Spark в HDInsight для сборки приложений потоковой передачи данных в режиме реального времени](hdinsight-apache-spark-eventhub-streaming.md)

* [Анализ журнала веб-сайта с использованием Spark в HDInsight](hdinsight-apache-spark-custom-library-website-log-analysis.md)

### Создание и запуск приложений

* [Создание автономного приложения с использованием Scala](hdinsight-apache-spark-create-standalone-application.md)

* [Удаленный запуск заданий с помощью Livy в кластере Spark](hdinsight-apache-spark-livy-rest-interface.md)

### Средства и расширения

* [Использование подключаемого модуля средств HDInsight для IntelliJ IDEA для создания и отправки приложений Spark Scala](hdinsight-apache-spark-intellij-tool-plugin.md)

* [Удаленная отладка приложений Spark в кластере HDInsight Spark Linux с помощью подключаемого модуля средств HDInsight для IntelliJ IDEA](hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)

* [Использование записных книжек Zeppelin с кластером Spark в HDInsight](hdinsight-apache-spark-use-zeppelin-notebook.md)

* [Ядра, доступные для записной книжки Jupyter в кластере Spark в HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md)

* [Использование внешних пакетов с записными книжками Jupyter](hdinsight-apache-spark-jupyter-notebook-use-external-packages.md)

* [Установка записной книжки Jupyter на компьютере и ее подключение к кластеру Apache Spark в Azure HDInsight (предварительная версия)](hdinsight-apache-spark-jupyter-notebook-install-locally.md)

### Управление ресурсами

* [Управление ресурсами кластера Apache Spark в Azure HDInsight](hdinsight-apache-spark-resource-manager.md)

* [Отслеживание и отладка заданий в кластере Apache Spark в HDInsight на платформе Linux](hdinsight-apache-spark-job-debugging.md)

<!---HONumber=AcomDC_0914_2016-->
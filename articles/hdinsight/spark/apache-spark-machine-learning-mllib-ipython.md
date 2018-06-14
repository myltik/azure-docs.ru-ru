---
title: Пример машинного обучения с использованием Spark MLlib в HDInsight — Azure | Документы Майкрософт
description: Узнайте, как использовать Spark MLlib для создания приложения машинного обучения, которое анализирует набор данных с помощью классификации посредством логистической регрессии.
keywords: машинное обучение spark, пример машинного обучения spark
services: hdinsight
documentationcenter: ''
author: mumian
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: c0fd4baa-946d-4e03-ad2c-a03491bd90c8
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.devlang: na
ms.topic: conceptual
ms.date: 05/18/2018
ms.author: jgao
ms.openlocfilehash: 1fc89f2181a5b9fb6b6c5a26d974b016fa1926a6
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
ms.locfileid: "34361341"
---
# <a name="use-spark-mllib-to-build-a-machine-learning-application-and-analyze-a-dataset"></a>Использование Spark MLlib для создания приложения машинного обучения и анализа набора данных

Узнайте, как использовать Spark [MLlib](https://spark.apache.org/mllib/) для создания приложения машинного обучения с целью проведения простого прогнозного анализа на основе открытого набора данных. В этом примере используется *классификация* посредством логистической регрессии на основе встроенных библиотек машинного обучения Spark. 

> [!TIP]
> Этот пример также доступен в виде записной книжки Jupyter в кластере Spark (на платформе Linux), созданном в HDInsight. Фрагменты кода Python можно выполнять непосредственно в записной книжке. Для прохождения учебника в записной книжке создайте кластер Spark и запустите записную книжку Jupyter (`https://CLUSTERNAME.azurehdinsight.net/jupyter`). Затем запустите записную книжку **Машинное обучение Spark. Прогнозный анализ на основе данных контроля качества пищевых продуктов с использованием MLlib.ipynb** в папке **Python**.
>
>

MLlib — это основная библиотека Spark, содержащая множество служебных программ, которые подходят для задач машинного обучения, в частности:

* классификация;
* Регрессия
* Кластеризация
* тематического моделирования;
* сингулярного разложения и анализа по методу главных компонент;
* проверки гипотез и статистической выборки.

## <a name="understand-classification-and-logistic-regression"></a>Общие сведения о классификации и логистической регрессии
*Классификация* — это распространенная задача машинного обучения, которая представляет собой процесс сортировки входных данных по категориям. Алгоритм классификации определяет принцип назначения «меток» предоставленным входным данных. Например, можно создать алгоритм машинного обучения, который принимает в качестве входных данных информацию об акциях и делит акции на две категории: акции, которые следует продать, и акции, которые стоит оставить.

Логистическая регрессия — один из алгоритмов классификации. API Spark для логистической регрессии подходит для задач *двоичной классификации* или разделения входных данных на две группы. Дополнительные сведения о логистической регрессии см. в статье [Википедии](https://en.wikipedia.org/wiki/Logistic_regression).

В общих словах, в основе логистической регрессии лежит некая *логистическая функция* , используемая для прогнозирования вероятности принадлежности вектора входных данных к одной или другой группе.  

## <a name="predictive-analysis-example-on-food-inspection-data"></a>Пример прогнозного анализа на основе данных контроля качества пищевых продуктов
В этом примере показано использование Spark для прогнозного анализа на основе данных контроля качества пищевых продуктов (**Food_Inspections1.csv**), полученных на [информационном портале Чикаго](https://data.cityofchicago.org/). Этот набор данных содержит сведения о проверках качества пищевых продуктов, проведенных в Чикаго, в частности информацию о каждом предприятии общественного питания, обнаруженных нарушениях (если таковые были) и результатах проверки. CSV-файл данных уже доступен в учетной записи хранения, связанной с кластером **/HdiSamples/HdiSamples/FoodInspectionData/Food_Inspections1.csv**.

В следующих разделах описаны шаги по разработке модели, которая позволит узнать, что нужно, чтобы пройти контроль качества пищевых продуктов.

## <a name="create-a-spark-mllib-machine-learning-app"></a>Создание приложения машинного обучения Spark MLlib

1. Создайте записную книжку Jupyter, используя ядро PySpark. Инструкции см. в разделе по [созданию записной книжки Jupyter](./apache-spark-jupyter-spark-sql.md#create-a-jupyter-notebook).

2. Импортируйте типы, необходимые для этого приложения. Скопируйте указанный ниже фрагмент кода, вставьте его в пустую ячейку и нажмите клавиши **SHIFT+ВВОД**.

    ```PySpark
    from pyspark.ml import Pipeline
    from pyspark.ml.classification import LogisticRegression
    from pyspark.ml.feature import HashingTF, Tokenizer
    from pyspark.sql import Row
    from pyspark.sql.functions import UserDefinedFunction
    from pyspark.sql.types import *
    ```
    Ядро PySpark позволяет не задавать контексты явным образом. Контексты Spark и Hive будут созданы автоматически при выполнении первой ячейки кода. 

## <a name="construct-the-input-dataframe"></a>Создание входного кадра данных

Необработанные данные предоставляются в формате CSV, что позволяет применить контекст Spark для передачи файла в память в виде неструктурированного текста, а затем выполнить синтаксический анализ каждой его строки поочередно с помощью библиотеки CSV для Python.

1. Запустите следующие строки, чтобы импортировать входные данные и создать на их основе устойчивый распределенный набор данных (RDD).

    ```PySpark
    def csvParse(s):
        import csv
        from StringIO import StringIO
        sio = StringIO(s)
        value = csv.reader(sio).next()
        sio.close()
        return value
    
    inspections = sc.textFile('wasb:///HdiSamples/HdiSamples/FoodInspectionData/Food_Inspections1.csv')\
                    .map(csvParse)
    ```

2. Выполните следующий код, чтобы получить из набора RDD одну строку, которая позволит изучить схему данных.

    ```PySpark
    inspections.take(1)
    ```

    Результаты:

    ```
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
    ```

    Эти выходные данные позволяют составить представление о схеме данных во входном файле. Помимо прочего, в файле содержатся название, тип и адрес каждого учреждения, а также данные проверки и места проведения проверок. 

3. Выполните следующий код, чтобы создать кадр данных под (*df*) и временную таблицу (*CountResults*) с несколькими столбцами, которые помогут выполнить прогностический анализ. Для преобразования структурированных данных используйте `sqlContext`. 

    ```PySpark
    schema = StructType([
    StructField("id", IntegerType(), False),
    StructField("name", StringType(), False),
    StructField("results", StringType(), False),
    StructField("violations", StringType(), True)])
    
    df = spark.createDataFrame(inspections.map(lambda l: (int(l[0]), l[1], l[12], l[13])) , schema)
    df.registerTempTable('CountResults')
    ```

    В этом кадре данных нас интересуют 4 столбца: **id**, **name**, **results** и **violations**.

4. Выполните следующий код, чтобы получить небольшую выборку данных.

    ```PySpark
    df.show(5)
    ```

    Результаты:

    ```
    +------+--------------------+-------+--------------------+
    |    id|                name|results|          violations|
    +------+--------------------+-------+--------------------+
    |413707|       LUNA PARK INC|   Fail|24. DISH WASHING ...|
    |391234|       CAFE SELMARIE|   Fail|2. FACILITIES TO ...|
    |413751|          MANCHU WOK|   Pass|33. FOOD AND NON-...|
    |413708|BENCHMARK HOSPITA...|   Pass|                    |
    |413722|           JJ BURGER|   Pass|                    |
    +------+--------------------+-------+--------------------+
    ```

## <a name="understand-the-data"></a>Используемые данные

Давайте подробно рассмотрим содержимое нашего набора данных. 

1. Выполните следующий код, чтобы увидеть конкретные значения из столбца **результаты**:

    ```PySpark
    df.select('results').distinct().show()
    ```

    Результаты:

    ```
    +--------------------+
    |             results|
    +--------------------+
    |                Fail|
    |Business Not Located|
    |                Pass|
    |  Pass w/ Conditions|
    |     Out of Business|
    +--------------------+
    ```

2. Выполните следующий код, чтобы создать визуализацию на основе этих результатов.

    ```PySpark
    %%sql -o countResultsdf
    SELECT results, COUNT(results) AS cnt FROM CountResults GROUP BY results
    ```

    Волшебное слово `%%sql`, за которым следует `-o countResultsdf`, гарантирует, что вывод запроса сохраняется локально на сервере Jupyter (обычно это головной узел кластера). Выходные данные сохраняются в кадре данных [Pandas](http://pandas.pydata.org/) с именем **countResultsdf**. Дополнительные сведения о магической команде `%%sql`, а также других магических командах, доступных в ядре PySpark, приведены в статье [Ядра для записных книжек Jupyter с кластерами Apache Spark в HDInsight](apache-spark-jupyter-notebook-kernels.md#parameters-supported-with-the-sql-magic).

    Результаты:

    ![Результат SQL-запроса](./media/apache-spark-machine-learning-mllib-ipython/spark-machine-learning-query-output.png "Результат SQL-запроса")


3. Также вы можете создать диаграмму с помощью библиотеки визуализации данных [Matplotlib](https://en.wikipedia.org/wiki/Matplotlib). Так как диаграмма должна создаваться из локально сохраненного кадра данных **countResultsdf**, фрагмент кода должен начинаться с волшебного слова `%%local`. Это гарантирует, что код будет выполняться локально на сервере Jupyter.

    ```PySpark
    %%local
    %matplotlib inline
    import matplotlib.pyplot as plt

    labels = countResultsdf['results']
    sizes = countResultsdf['cnt']
    colors = ['turquoise', 'seagreen', 'mediumslateblue', 'palegreen', 'coral']
    plt.pie(sizes, labels=labels, autopct='%1.1f%%', colors=colors)
    plt.axis('equal')
    ```

    Результаты:

    ![Выходные данные приложения машинного обучения Spark — круговая диаграмма с пятью отдельными результатами проверки](./media/apache-spark-machine-learning-mllib-ipython/spark-machine-learning-result-output-1.png "Результаты машинного обучения Spark")

    Проверка может вернуть один из следующих пяти результатов:

    - Business not located;
    - Fail;
    - Pass;
    - Pass w/ conditions;
    - Out of Business.

    Чтобы спрогнозировать результат проверки пищевых продуктов, вам нужно разработать модель для анализа нарушений. Логистическая регрессия является методом двоичной классификации, а значит данные целесообразно разделить на две категории: **Fail** и **Pass**.

    - Pass;
        - Pass;
        - Pass w/ conditions;
    - Fail;
        - Fail;
    - Игнорировать
        - Business not located;
        - Out of Business.

    Данные с другими результатами (Business not located или Out of Business) для нас бесполезны, и в любом случае их доля в общей выборке результатов крайне невелика.

4. Выполните следующий код, чтобы преобразовать существующий кадр данных (`df`) в новый кадр данных, где каждая проверка будет представлена в виде пары "метка — нарушение". В нашем случае метка `0.0` обозначает неудачу, `1.0` — успех, а `-1.0` — все остальные результаты. 

    ```PySpark
    def labelForResults(s):
        if s == 'Fail':
            return 0.0
        elif s == 'Pass w/ Conditions' or s == 'Pass':
            return 1.0
        else:
            return -1.0
    label = UserDefinedFunction(labelForResults, DoubleType())
    labeledData = df.select(label(df.results).alias('label'), df.violations).where('label >= 0')
    ```

5. Выполните следующий код, чтобы отобразить одну строку данных с меткой:

    ```PySpark
    labeledData.take(1)
    ```

    Результаты:

    ```
    [Row(label=0.0, violations=u"41. PREMISES MAINTAINED FREE OF LITTER, UNNECESSARY ARTICLES, CLEANING  EQUIPMENT PROPERLY STORED - Comments: All parts of the food establishment and all parts of the property used in connection with the operation of the establishment shall be kept neat and clean and should not produce any offensive odors.  REMOVE MATTRESS FROM SMALL DUMPSTER. | 35. WALLS, CEILINGS, ATTACHED EQUIPMENT CONSTRUCTED PER CODE: GOOD REPAIR, SURFACES CLEAN AND DUST-LESS CLEANING METHODS - Comments: The walls and ceilings shall be in good repair and easily cleaned.  REPAIR MISALIGNED DOORS AND DOOR NEAR ELEVATOR.  DETAIL CLEAN BLACK MOLD LIKE SUBSTANCE FROM WALLS BY BOTH DISH MACHINES.  REPAIR OR REMOVE BASEBOARD UNDER DISH MACHINE (LEFT REAR KITCHEN). SEAL ALL GAPS.  REPLACE MILK CRATES USED IN WALK IN COOLERS AND STORAGE AREAS WITH PROPER SHELVING AT LEAST 6' OFF THE FLOOR.  | 38. VENTILATION: ROOMS AND EQUIPMENT VENTED AS REQUIRED: PLUMBING: INSTALLED AND MAINTAINED - Comments: The flow of air discharged from kitchen fans shall always be through a duct to a point above the roofline.  REPAIR BROKEN VENTILATION IN MEN'S AND WOMEN'S WASHROOMS NEXT TO DINING AREA. | 32. FOOD AND NON-FOOD CONTACT SURFACES PROPERLY DESIGNED, CONSTRUCTED AND MAINTAINED - Comments: All food and non-food contact equipment and utensils shall be smooth, easily cleanable, and durable, and shall be in good repair.  REPAIR DAMAGED PLUG ON LEFT SIDE OF 2 COMPARTMENT SINK.  REPAIR SELF CLOSER ON BOTTOM LEFT DOOR OF 4 DOOR PREP UNIT NEXT TO OFFICE.")]
    ```

## <a name="create-a-logistic-regression-model-from-the-input-dataframe"></a>Создание модели логистической регрессии на основе входной таблицы данных

И последняя задача — преобразуйте данные с метками в такой формат, который удобен для анализа методом логистической регрессии. Входные данные для алгоритма логистической регрессии должны представлять собой *набор пар "метка — вектор признаков"*, где "вектор признаков" содержит числа, характеризующие точку входных данных. Таким образом, нам нужно преобразовать столбец violations, содержащий полуструктурированные данные и множество текстовых комментариев в свободной форме, в массив действительных чисел, которые может распознать компьютер.

Стандартный метод обработки естественного языка в машинном обучении — назначить каждому отдельному слову индекс, а затем передать вектор в алгоритм машинного обучения, при этом значение каждого индекса должно содержать относительную частоту использования слова в текстовой строке.

MLlib предоставляет простой способ выполнения этой операции. Сначала пометим каждую строку нарушений с помощью маркеров, чтобы определить отдельные слова в каждой строке. Затем используем `HashingTF`, чтобы преобразовать каждый набор маркеров в вектор признаков, который затем может быть передан в алгоритм логистической регрессии для создания модели. Для последовательного выполнения всех этих действий воспользуемся оператором pipeline.

```PySpark
tokenizer = Tokenizer(inputCol="violations", outputCol="words")
hashingTF = HashingTF(inputCol=tokenizer.getOutputCol(), outputCol="features")
lr = LogisticRegression(maxIter=10, regParam=0.01)
pipeline = Pipeline(stages=[tokenizer, hashingTF, lr])

model = pipeline.fit(labeledData)
```

## <a name="evaluate-the-model-using-another-dataset"></a>Оценка модели по другому набору данных

Созданную ранее модель можно использовать, чтобы *спрогнозировать* результаты новой проверки на основе обнаруженных нарушений. Мы обучили эту модель по набору данных **Food_Inspections1.csv**. Вы можете применить другой набор данных, например **Food_Inspections2.csv**, чтобы *оценить* эффективность полученной модели на основе новых данных. Этот новый набор данных (**Food_Inspections2.csv**) следует поместить в контейнер хранилища по умолчанию, связанный с кластером.

1. Выполните приведенный ниже фрагмент кода, чтобы создать кадр данных **predictionsDf** с прогнозами нашей модели. Фрагмент кода также создает временную таблицу **Predictions** на основе кадра данных.

    ```PySpark
    testData = sc.textFile('wasb:///HdiSamples/HdiSamples/FoodInspectionData/Food_Inspections2.csv')\
                .map(csvParse) \
                .map(lambda l: (int(l[0]), l[1], l[12], l[13]))
    testDf = spark.createDataFrame(testData, schema).where("results = 'Fail' OR results = 'Pass' OR results = 'Pass w/ Conditions'")
    predictionsDf = model.transform(testDf)
    predictionsDf.registerTempTable('Predictions')
    predictionsDf.columns
    ```

    Вы должны увидеть подобные выходные данные:

    ```
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
    ```

1. Чтобы просмотреть пример прогноза, выполните этот фрагмент кода:

    ```PySpark
    predictionsDf.take(1)
    ```

   Вы получите прогноз для первой записи в тестовом наборе данных.
1. Метод `model.transform()` таким же образом преобразовывает новые данные и определяет, как их классифицировать. Можно выполнить статистические расчеты, чтобы оценить точность полученных прогнозов:

    ```PySpark
    numSuccesses = predictionsDf.where("""(prediction = 0 AND results = 'Fail') OR
                                            (prediction = 1 AND (results = 'Pass' OR
                                                                results = 'Pass w/ Conditions'))""").count()
    numInspections = predictionsDf.count()

    print "There were", numInspections, "inspections and there were", numSuccesses, "successful predictions"
    print "This is a", str((float(numSuccesses) / float(numInspections)) * 100) + "%", "success rate"
    ```

    Данные результата выглядят следующим образом:

    ```
    # -----------------
    # THIS IS AN OUTPUT
    # -----------------

    There were 9315 inspections and there were 8087 successful predictions
    This is a 86.8169618894% success rate
    ```

    Используя логистическую регрессию Spark, вы создали точную модель, которая прогнозирует вероятность прохождения контроля качества пищевых продуктов для определенного предприятия на основе описаний нарушений на английском языке.

## <a name="create-a-visual-representation-of-the-prediction"></a>Создание визуального представления прогноза
Теперь создайте итоговую визуализацию, которая позволит оценить результаты теста.

1. Начнем с извлечения разных прогнозов и результатов из временной таблицы **Predictions**, созданной ранее. Следующие запросы разделяют выходные данные на *true_positive*, *false_positive*, *true_negative* и *false_negative*. В приведенных ниже запросах мы отключим визуализацию с помощью `-q`, а также сохраним выходные данные (с помощью `-o`) как кадры данных, которые затем можно будет использовать с волшебным словом `%%local`.

    ```PySpark
    %%sql -q -o true_positive
    SELECT count(*) AS cnt FROM Predictions WHERE prediction = 0 AND results = 'Fail'
    ```

    ```PySpark
    %%sql -q -o false_positive
    SELECT count(*) AS cnt FROM Predictions WHERE prediction = 0 AND (results = 'Pass' OR results = 'Pass w/ Conditions')
    ```

    ```PySpark
    %%sql -q -o true_negative
    SELECT count(*) AS cnt FROM Predictions WHERE prediction = 1 AND results = 'Fail'
    ```

    ```PySpark
    %%sql -q -o false_negative
    SELECT count(*) AS cnt FROM Predictions WHERE prediction = 1 AND (results = 'Pass' OR results = 'Pass w/ Conditions')
    ```

1. Наконец, используйте следующий фрагмент кода для создания диаграммы с помощью **Matplotlib**.

    ```PySpark
    %%local
    %matplotlib inline
    import matplotlib.pyplot as plt

    labels = ['True positive', 'False positive', 'True negative', 'False negative']
    sizes = [true_positive['cnt'], false_positive['cnt'], false_negative['cnt'], true_negative['cnt']]
    colors = ['turquoise', 'seagreen', 'mediumslateblue', 'palegreen', 'coral']
    plt.pie(sizes, labels=labels, autopct='%1.1f%%', colors=colors)
    plt.axis('equal')
    ```

    Вы должны увидеть следующий результат.

    ![Выходные данные приложения машинного обучения Spark — круговая диаграмма с процентными значениями для проверок качества пищевых продуктов, которые не были пройдены.](./media/apache-spark-machine-learning-mllib-ipython/spark-machine-learning-result-output-2.png "Результаты машинного обучения Spark")

    На этой диаграмме «положительный» результат представляет собой непройденную проверку, а отрицательный — пройденную.

## <a name="shut-down-the-notebook"></a>Завершение работы записной книжки
Завершив работу с приложением, следует закрыть записную книжку, чтобы освободить ресурсы. Для этого в записной книжке в меню **Файл** выберите пункт **Close and Halt** (Закрыть и остановить). Записная книжка завершит работу и закроется.

## <a name="seealso"></a>Дополнительные материалы
* [Обзор: Apache Spark в Azure HDInsight](apache-spark-overview.md)

### <a name="scenarios"></a>Сценарии
* [Использование Spark со средствами бизнес-аналитики. Выполнение интерактивного анализа данных с использованием Spark в HDInsight с помощью средств бизнес-аналитики](apache-spark-use-bi-tools.md)
* [Использование Spark с машинным обучением. Использование Spark в HDInsight для анализа температуры в здании на основе данных системы кондиционирования](apache-spark-ipython-notebook-machine-learning.md)
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

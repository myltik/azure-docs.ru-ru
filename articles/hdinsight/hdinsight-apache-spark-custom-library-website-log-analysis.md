<properties 
	pageTitle="Использование пользовательских библиотек с кластером HDInsight Spark для анализа журналов веб-сайтов | Microsoft Azure" 
	description="Использование пользовательских библиотек с кластером HDInsight Spark для анализа журналов веб-сайтов" 
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

# Анализ журналов веб-сайтов с помощью пользовательской библиотеки и кластера Apache Spark в HDInsight на платформе Linux

Данная записная книжка показывает, как анализировать данные журналов с помощью настраиваемой библиотеки с кластером Spark в HDInsight. В качестве пользовательской библиотеки используется библиотека Python с именем **iislogparser.py**.

> [AZURE.TIP] Кроме того, это руководство доступно в виде записной книжки Jupyter в кластере Spark (на платформе Linux), созданном в HDInsight. Фрагменты кода Python можно выполнять непосредственно в записной книжке. Чтобы выполнить действия в учебнике из записной книжки, создайте кластер Spark, запустите записную книжку Jupyter (`https://CLUSTERNAME.azurehdinsight.net/jupyter`), а затем записную книжку **Анализ журналов в Spark с помощью настраиваемой библиотеки IPYNB** в папке **PySpark**.

**Предварительные требования:**

Необходимо следующее:

- Подписка Azure. См. [Бесплатная пробная версия Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- Кластер Apache Spark в HDInsight на платформе Linux. Инструкции см. в разделе [Создание кластеров Apache Spark в Azure HDInsight](hdinsight-apache-spark-jupyter-spark-sql.md).

## Сохранение необработанных данных в формате RDD

В этом разделе мы используем записную книжку [Jupyter](https://jupyter.org), связанную с кластером Apache Spark в HDInsight, для выполнения заданий, которые обрабатывают необработанные демонстрационные данные и сохраняют их как таблицу Hive. В качестве демонстрационных данных выступает CSV-файл (hvac.csv), доступный на всех кластерах по умолчанию.

После сохранения данных в виде таблицы Hive можно переходить к следующему разделу и подключится к таблице Hive с помощью средств бизнес-аналитики, таких как Power BI и Tableau.

1. На начальной панели [портала Azure](https://portal.azure.com/) щелкните элемент кластера Spark (если он закреплен на начальной панели). Кроме того, вы можете перейти к кластеру, выбрав пункты **Просмотреть все** и **Кластеры HDInsight**.

2. В колонке кластера Spark щелкните **Быстрые ссылки**, затем в колонке **Панель мониторинга кластера** выберите **Jupyter Notebook**. При появлении запроса введите учетные данные администратора для кластера.

	> [AZURE.NOTE] Также можно открыть Jupyter Notebook для своего кластера, открыв следующий URL-адрес в браузере. Замените __CLUSTERNAME__ именем кластера.
	>
	> `https://CLUSTERNAME.azurehdinsight.net/jupyter`

2. Создайте новую записную книжку. Щелкните **Создать**, а затем выберите **PySpark**.

	![Создание новой записной книжки Jupyter](./media/hdinsight-apache-spark-custom-library-website-log-analysis/hdispark.note.jupyter.createnotebook.png "Создание новой записной книжки Jupyter")

3. Будет создана и открыта записная книжка с именем Untitled.pynb. Щелкните имя записной книжки в верхней части страницы сверху и введите понятное имя.

	![Указание имени для записной книжки](./media/hdinsight-apache-spark-custom-library-website-log-analysis/hdispark.note.jupyter.notebook.name.png "Указание имени для записной книжки")

4. Так как записная книжка была создана с помощью ядра PySpark, задавать контексты явно необязательно. Контексты Spark и Hive будут созданы автоматически при выполнении первой ячейки кода. Можно начать с импорта различных типов, необходимых для этого сценария. Вставьте следующий фрагмент кода в пустую ячейку и нажмите клавиши **SHIFT + ВВОД**.


		from pyspark.sql import Row
		from pyspark.sql.types import *


5. Создайте RDD, используя пример данных журнала, уже доступных в кластере. Доступ к данным в связанной с кластером учетной записи хранения по умолчанию вы можете получить с помощью файла **\\HdiSamples\\HdiSamples\\WebsiteLogSampleData\\SampleLog\\909f2b.log**.


		logs = sc.textFile('wasbs:///HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/909f2b.log')


6. Извлеките пример набора журналов и убедитесь в том, что описанный выше шаг успешно выполнен.

		logs.take(5)

	Должен отобразиться результат, аналогичный приведенному ниже:

		# -----------------
		# THIS IS AN OUTPUT
		# -----------------

		[u'#Software: Microsoft Internet Information Services 8.0',
		 u'#Fields: date time s-sitename cs-method cs-uri-stem cs-uri-query s-port cs-username c-ip cs(User-Agent) cs(Cookie) cs(Referer) cs-host sc-status sc-substatus sc-win32-status sc-bytes cs-bytes time-taken',
		 u'2014-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step2.png X-ARR-LOG-ID=2ec4b8ad-3cf0-4442-93ab-837317ece6a1 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 53175 871 46',
		 u'2014-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step3.png X-ARR-LOG-ID=9eace870-2f49-4efd-b204-0d170da46b4a 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 51237 871 32',
		 u'2014-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step4.png X-ARR-LOG-ID=4bea5b3d-8ac9-46c9-9b8c-ec3e9500cbea 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 72177 871 47']

## Анализ данных журнала с помощью пользовательской библиотеки Python

7. В приведенном выше примере выходных данных первые несколько строк содержат сведения о заголовке, а все последующие строки соответствуют схеме, описанной в этом заголовке. Анализ таких журналов может быть сложным, поэтому мы используем настраиваемую библиотеку Python (**iislogparser.py**), которая делает эту задачу намного проще. По умолчанию эта библиотека входит в состав кластера Spark в HDInsight и расположена в **/HdiSamples/HdiSamples/WebsiteLogSampleData/iislogparser.py**.

	Однако в `PYTHONPATH` эта библиотека не входит, поэтому использовать ее с помощью такого оператора импорта, как `import iislogparser`, нельзя. Чтобы использовать эту библиотеку, необходимо распространить ее на все рабочие узлы. Выполните следующий фрагмент кода.


		sc.addPyFile('wasbs:///HdiSamples/HdiSamples/WebsiteLogSampleData/iislogparser.py')


9. `iislogparser` предоставляет функцию `parse_log_line`, которая возвращает `None`, если строка журнала является строкой заголовка, или экземпляр класса `LogLine` при обнаружении строки журнала. Класс `LogLine` позволяет извлечь только строки журнала из RDD:

		def parse_line(l):
		    import iislogparser
		    return iislogparser.parse_log_line(l)
		logLines = logs.map(parse_line).filter(lambda p: p is not None).cache()


10. Выведите на экран несколько извлеченных строк журнала и убедитесь в том, что это действие выполнено успешно.

		logLines.take(2)

	Должен быть получен результат, аналогичный приведенному ниже:

		# -----------------
		# THIS IS AN OUTPUT
		# -----------------

		[2014-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step2.png X-ARR-LOG-ID=2ec4b8ad-3cf0-4442-93ab-837317ece6a1 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 53175 871 46,
 		2014-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step3.png X-ARR-LOG-ID=9eace870-2f49-4efd-b204-0d170da46b4a 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 51237 871 32]


11. В свою очередь, класс `LogLine` включает несколько полезных методов, например метод `is_error()`, который возвращается, если запись журнала содержит код ошибки. С его помощью вы можете вычислить количество ошибок в извлеченных строках журналов, а затем записать все ошибки в отдельный файл.

		errors = logLines.filter(lambda p: p.is_error())
		numLines = logLines.count()
		numErrors = errors.count()
		print 'There are', numErrors, 'errors and', numLines, 'log entries'
		errors.map(lambda p: str(p)).saveAsTextFile('wasbs:///HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/909f2b-2.log')

	Вы должны увидеть подобные выходные данные:

		# -----------------
		# THIS IS AN OUTPUT
		# -----------------

		There are 30 errors and 646 log entries

12. Кроме того, для визуализации данных вы можете использовать **Matplotlib**. Например, если вы хотите установить причину длительного выполнения некоторых запросов, найдите файлы с наибольшим средним временем обработки. Код в представленном ниже фрагменте выдает первые 25 ресурсов с максимальным временем обработки запросов.

		def avgTimeTakenByKey(rdd):
		    return rdd.combineByKey(lambda line: (line.time_taken, 1),
		                            lambda x, line: (x[0] + line.time_taken, x[1] + 1),
		                            lambda x, y: (x[0] + y[0], x[1] + y[1]))\
		              .map(lambda x: (x[0], float(x[1][0]) / float(x[1][1])))
		    
		avgTimeTakenByKey(logLines.map(lambda p: (p.cs_uri_stem, p))).top(25, lambda x: x[1])

	Вы должны увидеть подобные выходные данные:

		# -----------------
		# THIS IS AN OUTPUT
		# -----------------

		[(u'/blogposts/mvc4/step13.png', 197.5),
		 (u'/blogposts/mvc2/step10.jpg', 179.5),
		 (u'/blogposts/extractusercontrol/step5.png', 170.0),
		 (u'/blogposts/mvc4/step8.png', 159.0),
		 (u'/blogposts/mvcrouting/step22.jpg', 155.0),
		 (u'/blogposts/mvcrouting/step3.jpg', 152.0),
		 (u'/blogposts/linqsproc1/step16.jpg', 138.75),
		 (u'/blogposts/linqsproc1/step26.jpg', 137.33333333333334),
		 (u'/blogposts/vs2008javascript/step10.jpg', 127.0),
		 (u'/blogposts/nested/step2.jpg', 126.0),
		 (u'/blogposts/adminpack/step1.png', 124.0),
		 (u'/BlogPosts/datalistpaging/step2.png', 118.0),
		 (u'/blogposts/mvc4/step35.png', 117.0),
		 (u'/blogposts/mvcrouting/step2.jpg', 116.5),
		 (u'/blogposts/aboutme/basketball.jpg', 109.0),
		 (u'/blogposts/anonymoustypes/step11.jpg', 109.0),
		 (u'/blogposts/mvc4/step12.png', 106.0),
		 (u'/blogposts/linq8/step0.jpg', 105.5),
		 (u'/blogposts/mvc2/step18.jpg', 104.0),
		 (u'/blogposts/mvc2/step11.jpg', 104.0),
		 (u'/blogposts/mvcrouting/step1.jpg', 104.0),
		 (u'/blogposts/extractusercontrol/step1.png', 103.0),
		 (u'/blogposts/sqlvideos/sqlvideos.jpg', 102.0),
		 (u'/blogposts/mvcrouting/step21.jpg', 101.0),
		 (u'/blogposts/mvc4/step1.png', 98.0)]


13. Кроме того, эти сведения вы можете представить в виде графика. Чтобы создать диаграмму, для начала создадим временную таблицу **AverageTime**. Таблица группирует журналы по времени и показывает все необычные пики задержек за определенный период времени.

		avgTimeTakenByMinute = avgTimeTakenByKey(logLines.map(lambda p: (p.datetime.minute, p))).sortByKey()
		schema = StructType([StructField('Minutes', IntegerType(), True),
		                     StructField('Time', FloatType(), True)])
		                     
		avgTimeTakenByMinuteDF = sqlContext.createDataFrame(avgTimeTakenByMinute, schema)
		avgTimeTakenByMinuteDF.registerTempTable('AverageTime')

14. После этого можно выполнить следующий запрос SQL, чтобы получить все записи таблицы **AverageTime**.

		%%sql -o averagetime
		SELECT * FROM AverageTime

	Волшебное слово `%%sql`, за которым следует `-o averagetime`, гарантирует, что вывод запроса сохраняется локально на сервере Jupyter (обычно это головной узел кластера). Выходные данные сохраняются в кадре данных [Pandas](http://pandas.pydata.org/) с именем **averagetime**.

	Вы должны увидеть подобные выходные данные:

	![Результат SQL-запроса](./media/hdinsight-apache-spark-custom-library-website-log-analysis/sql.output.png "Результат SQL-запроса")

	Дополнительные сведения о волшебном слове `%%sql`, а также других волшебных словах, доступных в ядре PySpark, приведены в разделе [Ядра, доступные в записных книжках Jupyter с кластерами Spark в HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md#why-should-i-use-the-new-kernels).

15. Теперь можно создать диаграмму с помощью Matplotlib, библиотеки, используемой для визуализации данных. Так как диаграмма должна создаваться из локально сохраненного кадра данных **averagetime**, фрагмент кода должен начинаться с волшебного слова `%%local`. Это гарантирует, что код будет выполняться локально на сервере Jupyter.

		%%local
		%matplotlib inline
		import matplotlib.pyplot as plt
		
		plt.plot(averagetime['Minutes'], averagetime['Time'], marker='o', linestyle='--')
		plt.xlabel('Time (min)')
		plt.ylabel('Average time taken for request (ms)')

	Вы должны увидеть подобные выходные данные:

	![Выходные данные Matplotlib](./media/hdinsight-apache-spark-custom-library-website-log-analysis/hdi-apache-spark-web-log-analysis-plot.png "Выходные данные Matplotlib")

16. Завершив работу с приложением, следует закрыть записную книжку, чтобы освободить ресурсы. Для этого в записной книжке в меню **Файл** выберите пункт **Закрыть и остановить**. Это завершит работу записной книжки и закроет ее.
	

## <a name="seealso"></a>См. также:


* [Обзор: Apache Spark в Azure HDInsight](hdinsight-apache-spark-overview.md)

### Сценарии

* [Использование Spark со средствами бизнес-аналитики. Выполнение интерактивного анализа данных с использованием Spark в HDInsight с помощью средств бизнес-аналитики](hdinsight-apache-spark-use-bi-tools.md)

* [Использование Spark с машинным обучением. Использование Spark в HDInsight для анализа температуры в здании на основе данных системы кондиционирования](hdinsight-apache-spark-ipython-notebook-machine-learning.md)

* [Использование Spark с машинным обучением. Использование Spark в HDInsight для прогнозирования результатов контроля качества пищевых продуктов](hdinsight-apache-spark-machine-learning-mllib-ipython.md)

* [Потоковая передача Spark. Использование Spark в HDInsight для сборки приложений потоковой передачи данных в режиме реального времени](hdinsight-apache-spark-eventhub-streaming.md)

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
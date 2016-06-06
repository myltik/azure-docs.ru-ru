<properties 
	pageTitle="Использование внешних пакетов с записными книжками Jupyter в кластерах Apache Spark в HDInsight | Azure"
	description="Пошаговые инструкции по настройке записных книжек Jupyter с кластерами Spark HDInsight для использования внешних пакетов Spark." 
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
	ms.date="05/25/2016" 
	ms.author="nitinme"/>


# Использование внешних пакетов с записными книжками Jupyter в кластерах Apache Spark в Azure HDInsight (предварительная версия)

Узнайте, как настроить записную книжку Jupyter в кластере Apache Spark в HDInsight (Linux) для использования внешних, предоставленных сообществом пакетов, которые не включены в готовую версию кластера.

Полный список доступных пакетов можно найти в [репозитории Maven](http://search.maven.org/). Его также можно получить из других источников. Например, полный список предоставленных сообществом пакетов можно найти в разделе [Пакеты Spark](http://spark-packages.org/).

В этой статье вы узнаете, как использовать пакет [spark-csv](http://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar) с записной книжкой Jupyter.

**Предварительные требования:**

Необходимо следующее:

- Подписка Azure. См. [Бесплатная пробная версия Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- Кластер Apache Spark в HDInsight на платформе Linux. Инструкции см. в разделе [Создание кластеров Apache Spark в Azure HDInsight](hdinsight-apache-spark-jupyter-spark-sql.md).

## Использование внешних пакетов с записными книжками Jupyter 

1. На начальной панели [портала Azure](https://portal.azure.com/) щелкните элемент кластера Spark (если он закреплен на начальной панели). Кроме того, вы можете перейти к кластеру, выбрав пункты **Просмотреть все** и **Кластеры HDInsight**.   

2. В колонке кластера Spark щелкните **Быстрые ссылки**, затем в колонке **Панель мониторинга кластера** выберите **Jupyter Notebook**. При появлении запроса введите учетные данные администратора для кластера.

	> [AZURE.NOTE] Также можно открыть Jupyter Notebook для своего кластера, открыв следующий URL-адрес в браузере. Замените __CLUSTERNAME__ именем кластера.
	>
	> `https://CLUSTERNAME.azurehdinsight.net/jupyter`

2. Создайте новую записную книжку. Щелкните **Создать**, а затем выберите **Spark**.

	![Создание новой записной книжки Jupyter](./media/hdinsight-apache-spark-jupyter-notebook-use-external-packages/hdispark.note.jupyter.createnotebook.png "Создание новой записной книжки Jupyter")

3. Будет создана и открыта записная книжка с именем Untitled.pynb. Щелкните имя записной книжки в верхней части страницы сверху и введите понятное имя.

	![Указание имени для записной книжки](./media/hdinsight-apache-spark-jupyter-notebook-use-external-packages/hdispark.note.jupyter.notebook.name.png "Указание имени для записной книжки")

4. Используйте волшебную команду `%%configure`, чтобы настроить записную книжку для использования внешнего пакета. В записных книжках, которые используют внешние пакеты, следует вызывать волшебную команду `%%configure` в первой ячейке кода. Она настраивает ядро для использования пакета до начала сеанса.

		%%configure
		{ "packages":["com.databricks:spark-csv_2.10:1.4.0"] }


	>[AZURE.IMPORTANT] Если вы забыли настроить ядро в первой ячейке, можно использовать `%%configure` с параметром `-f`, но при этом сеанс будет перезапущен и все изменения будут утеряны.

5. В приведенном выше фрагменте `packages` ожидает получить список координат Maven в центральном репозитории Maven. В этом фрагменте кода `com.databricks:spark-csv_2.10:1.4.0` — координата пакета **spark-csv** в Maven. Вот как сформировать координаты для пакета.

	а. Найдите пакет в репозитории Maven. В этом учебнике мы используем [spark-csv](http://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar).
	
	b. В репозитории найдите значения для параметров **GroupId**, **ArtifactId** и **Version**.

	![Использование внешних пакетов с записной книжкой Jupyter](./media/hdinsight-apache-spark-jupyter-notebook-use-external-packages/use-external-packages-with-jupyter.png "Использование внешних пакетов с записной книжкой Jupyter")

	c. Объедините три значения, разделив их двоеточием (**:**).

		com.databricks:spark-csv_2.10:1.4.0

6. Запустите код ячейки с помощью волшебной команды `%%configure`. После выполнения этой команды соответствующий сеанс Livy будет использовать указанный вами пакет. В последующих ячейках записной книжки теперь можно использовать этот пакет, как показано ниже.

		val df = sqlContext.read.format("com.databricks.spark.csv").
        option("header", "true").
        option("inferSchema", "true").
        load("wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

7. Затем можно запустить фрагменты кода, как показано ниже, чтобы просмотреть данные из таблицы данных, созданной на предыдущем шаге.

		df.show()

		df.select("Time").count()


## <a name="seealso"></a>См. также:


* [Обзор: Apache Spark в Azure HDInsight](hdinsight-apache-spark-overview.md)

### Сценарии

* [Использование Spark со средствами бизнес-аналитики. Выполнение интерактивного анализа данных с использованием Spark в HDInsight с помощью средств бизнес-аналитики](hdinsight-apache-spark-use-bi-tools.md)

* [Использование Spark с машинным обучением. Использование Spark в HDInsight для анализа температуры в здании на основе данных системы кондиционирования](hdinsight-apache-spark-ipython-notebook-machine-learning.md)

* [Использование Spark с машинным обучением. Использование Spark в HDInsight для прогнозирования результатов контроля качества пищевых продуктов](hdinsight-apache-spark-machine-learning-mllib-ipython.md)

* [Потоковая передача Spark. Использование Spark в HDInsight для сборки приложений потоковой передачи данных в режиме реального времени](hdinsight-apache-spark-eventhub-streaming.md)

* [Анализ журнала веб-сайта с использованием Spark в HDInsight](hdinsight-apache-spark-custom-library-website-log-analysis.md)

### Создание и запуск приложений

* [Создание автономного приложения с использованием Scala](hdinsight-apache-spark-create-standalone-application.md)

* [Удаленный запуск заданий с помощью Livy в кластере Spark](hdinsight-apache-spark-livy-rest-interface.md)

### Средства и расширения

* [Использование подключаемого модуля средств HDInsight для IntelliJ IDEA для создания и отправки приложений Spark Scala](hdinsight-apache-spark-intellij-tool-plugin.md)

* [Использование записных книжек Zeppelin с кластером Spark в HDInsight](hdinsight-apache-spark-use-zeppelin-notebook.md)

### Управление ресурсами

* [Управление ресурсами кластера Apache Spark в Azure HDInsight](hdinsight-apache-spark-resource-manager.md)

<!---HONumber=AcomDC_0525_2016-->
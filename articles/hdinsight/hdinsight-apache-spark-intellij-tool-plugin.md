 <properties
	pageTitle="Создание приложений Spark Scala с помощью подключаемого модуля HDInsight для IntelliJ IDEA | Microsoft Azure"
	description="Узнайте, как создать автономное приложение Spark для работы в кластерах HDInsight Spark"
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
	ms.date="05/18/2016"
	ms.author="nitinme"/>


# Использование подключаемого модуля средств HDInsight для IntelliJ IDEA для создания приложений Spark Scala для кластера HDInsight Spark на платформе Linux (предварительная версия)

В этой статье приводятся пошаговые инструкции по разработке приложений Spark, написанных на языке Scala, и его отправка в кластер HDInsight Spark с помощью подключаемого модуля HDInsight для IntelliJ IDEA. Подключаемый модуль можно использовать различным образом:

* Для разработки и отправки приложений Scala Spark в кластер HDInsight Spark.
* Для доступа к ресурсам кластера Azure HDInsight Spark.
* Для разработки и локального запуска приложений Scala Spark.

Чтобы приступить к работе, посмотрите видео [здесь](https://mix.office.com/watch/1nqkqjt5xonza).

>[AZURE.IMPORTANT] Это средство позволяет создавать и отправлять приложения только в кластер HDInsight Spark на Linux.


**Предварительные требования**

* Подписка Azure. См. [Бесплатная пробная версия Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* Кластер Apache Spark в HDInsight на платформе Linux. Инструкции см. в разделе [Создание кластеров Apache Spark в Azure HDInsight](hdinsight-apache-spark-jupyter-spark-sql.md).
* Комплект разработчика Oracle Java. Его можно установить [отсюда](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).
* IntelliJ IDEA. В этой статье используется версия 15.0.1. Его можно установить [отсюда](https://www.jetbrains.com/idea/download/).

## Установка подключаемого модуля средств HDInsight для IntelliJ IDEA

1. На экране приветствия IntelliJ IDEA щелкните **Configure** (Настройка), а затем **Plugins** (Подключаемые модули).

2. На следующем экране щелкните **Browse Repositories** (Поиск репозиториев) в левом нижнем углу. В открывшемся диалоговом окне **Browse Repositories** (Поиск репозиториев) найдите **HDInsight**, выберите **Microsoft Azure HDInsight Tools for IntelliJ (Preview)** (Средства Microsoft Azure HDInsight для IntelliJ — предварительная версия) и щелкните **Install** (Установить). Этот подключаемый модуль имеет зависимость от подключаемого модуля Scala, поэтому, если подключаемый модуль Scala не установлен, вам будет предложено установить его.

	![Установка подключаемого модуля HDInsight](./media/hdinsight-apache-spark-intellij-tool-plugin/install-hdinsight-plugin.png)

3. В появившемся запросе нажмите кнопку **Restart IntelliJ IDEA** (Перезапустить IntelliJ IDEA), чтобы перезапустить интегрированную среду разработки.


## Запуск приложения Spark Scala в кластере HDInsight Spark

1. Запустите IntelliJ IDEA и создайте проект. В диалоговом окне нового проекта установите параметры, как на снимке экрана ниже, а затем нажмите кнопку **Next** («Далее»).

	![Создание приложения Spark Scala](./media/hdinsight-apache-spark-intellij-tool-plugin/create-hdi-scala-app.png)

	* На левой панели выберите **HDInsight**.
	* На правой панели выберите **Spark on HDInsight (Scala)** (Spark в HDInsight (Scala)).
	* Нажмите кнопку **Далее**.

2. В следующем окне укажите сведения о проекте.

	* Введите имя и расположение проекта.
	* Для **пакета SDK для Project** требуется версия Java выше седьмой.
	* Для **пакета SDK для Scala** щелкните **Создать**, а затем **Загрузить**, после чего выберите версию Scala. **Не используйте версию 2.11.x**. В этом примере используется версия **2.10.6**.

		![Создание приложения Spark Scala](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-scala-version.png)

	* Для **пакета SDK для Spark** скачайте и используйте пакет SDK [отсюда](http://go.microsoft.com/fwlink/?LinkID=723585&clcid=0x409). Можно также использовать [репозиторий Spark Maven](http://mvnrepository.com/search?q=spark), однако убедитесь, что у вас установлен нужный репозиторий Maven для разработки приложений Spark. (Например, при использовании потоковой передачи Spark необходимо убедиться, что установлена часть Spark Streaming. Кроме того, убедитесь, что используется репозиторий, помеченный как Scala 2.10, — не используйте репозиторий, помеченный как Scala 2.11).

		![Создание приложения Spark Scala](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-scala-project-details.png)

	* Нажмите кнопку **Готово**

3. Проект Spark автоматически создаст артефакт. Вы можете создать собственный артефакт, выполнив следующую процедуру:

	1. В меню **File** («Файл») выберите пункт **Project Structure** («Структура проекта»).
	2. В диалоговом окне **Project Structure** («Структура проекта») выберите **Artifacts** («Артефакты») и щелкните знак плюса. Во всплывающем диалоговом окне щелкните **JAR**, а затем **Empty** (Пустой).

		![Создание JAR-файла](./media/hdinsight-apache-spark-intellij-tool-plugin/create-jar-1.png)

	3. Введите имя JAR-файла (например **MyClusterApp**). На панели "Доступные элементы" щелкните правой кнопкой мыши **"MyClusterApp" compile output** (Результат компиляции MyClusterApp) и выберите пункт **Put into Output Root** (Поместить в корень выходных данных).

		![Создание JAR-файла](./media/hdinsight-apache-spark-intellij-tool-plugin/create-jar-2.png)

	4. Нажмите кнопку **Apply** (Применить), а затем — **ОК**.

4. Добавьте исходный код своего приложения.

	1. В **обозревателе проектов** щелкните правой кнопкой мыши **src**, наведите указатель мыши на пункт **Создать** и щелкните **Класс Scala**.

		![Добавить исходный код](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-spark-scala-code.png)

	2. В диалоговом окне **Create New Scala Class** (Создание класса Scala) введите имя, в поле **Вид** выберите **Объект** и нажмите кнопку **ОК**.

		![Добавить исходный код](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-spark-scala-code-object.png)

	3. Вставьте в файл **MyClusterApp.scala** следующий код: Этот код считывает данные из файла HVAC.csv (доступного для всех кластеров HDInsight Spark), извлекает строки, содержащие только одну цифру в седьмом столбце CSV-файла, и записывает результат в **/HVACOut** в контейнер хранилища по умолчанию для кластера.


			import org.apache.spark.SparkConf
			import org.apache.spark.SparkContext

			object MyClusterApp{
			  def main (arg: Array[String]): Unit = {
			    val conf = new SparkConf().setAppName("MyClusterApp")
			    val sc = new SparkContext(conf)

			    val rdd = sc.textFile("wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

			    //find the rows which have only one digit in the 7th column in the CSV
				val rdd1 =  rdd.filter(s => s.split(",")(6).length() == 1)

			    rdd1.saveAsTextFile("wasb:///HVACOut")
			  }

			}

5. Запустите приложение в кластере HDInsight Spark.

	1. В **обозревателе проектов** щелкните имя проекта правой кнопкой мыши и выберите пункт **Submit Spark Application to HDInsight** (Отправить приложение Spark в HDInsight).

		![Отправить приложение Spark](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-submit-spark-app-1.png)

	2. Вам будет предложено ввести учетные данные подписки Azure. В диалоговом окне **Spark Submission** (Отправка в Spark) введите следующие значения:

		![Отправить приложение Spark](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-submit-spark-app-2.png)

		* В поле **Spark clusters (Linux only)** (Кластеры Spark (только для Linux)) выберите кластер HDInsight Spark, в котором вы хотите запустить приложение.

		* Необходимо выбрать артефакт из проекта IntelliJ или с жесткого диска.

		* В текстовом поле **Main class name** (Имя главного класса) щелкните многоточие (![многоточие](./media/hdinsight-apache-spark-intellij-tool-plugin/ellipsis.png)), выберите главный класс в исходном коде приложения и нажмите кнопку **ОК**.

			![Отправить приложение Spark](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-submit-spark-app-3.png)

		* Поскольку для кода приложения в этом примере не требуются аргументы командной строки, справочные JAR или файлы, остальные текстовые поля можно не заполнять.

		* Нажмите кнопку **Submit** (Отправить).

	3. На вкладке **Spark Submission** (Отправка в Spark) в нижней части окна начнет отображаться ход выполнения. Также можно остановить приложение, щелкнув красную кнопку в окне отправки в Spark.

        ![Результат приложения Spark](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-spark-app-result.png)

    В следующем разделе вы узнаете, как получить доступ к выходным данным задачи, используя подключаемый модуль HDInsight для IntelliJ IDEA.


## Доступ к кластерам HDInsight Spark и управление ими с помощью подключаемого модуля HDInsight для IntelliJ

С помощью подключаемого модуля HDInsight можно выполнять различные операции.

### Доступ к контейнеру хранилища для кластера

1. В меню **Вид** наведите указатель мыши на пункт **Окна инструментов** и щелкните **Обозреватель HDInsight**. При появлении запроса введите учетные данные для доступа к подписке Azure.

2. Разверните корневой узел **HDInsight**, чтобы увидеть список доступных кластеров HDInsight Spark.

3. Разверните имя кластера, чтобы увидеть учетную запись хранилища и контейнер хранилища по умолчанию для кластера.

	![Доступ к хранилищу кластера](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-spark-access-storage.png)

4. Щелкните имя связанного с кластером контейнера хранилища. На правой панели появится папка с именем **HVACOut**. Дважды щелкнув эту папку, вы увидите файлы **part-**. Откройте один из этих файлов для просмотра выходных данных приложения.

### Доступ к серверу журнала Spark

1. В **обозревателе HDInsight** щелкните имя кластера Spark правой кнопкой мыши и выберите пункт **Open Spark History UI** (Открыть пользовательский интерфейс журнала Spark). При появлении запроса введите учетные данные администратора для кластера. Вы должны были указать их при подготовке кластера.

2. На панели мониторинга сервера журнала Spark вы сможете найти приложение, выполнение которого только что было завершено по его имени. В приведенном выше коде имя приложения было указано с помощью `val conf = new SparkConf().setAppName("MyClusterApp")`. Таким образом, имя приложения Spark выглядело как **MyClusterApp**.

### Запуск портала Ambari

В **обозревателе HDInsight** щелкните имя кластера Spark правой кнопкой мыши и выберите пункт **Open Cluster Management Portal (Ambari)** (Открыть портал управления кластерами (Ambari)). При появлении запроса введите учетные данные администратора для кластера. Вы должны были указать их при подготовке кластера.

### Управление подписками Azure

По умолчанию подключаемый модуль HDInsight содержит список кластеров Spark из всех ваших подписок Azure. При необходимости можно указать подписки, кластеры которых вас интересуют. В **обозревателе HDInsight** щелкните правой кнопкой мыши корневой узел **HDInsight** и выберите пункт **Управление подписками**. В диалоговом окне снимите флажки напротив подписок, доступ к которым вам не требуется, и нажмите кнопку **Закрыть**. Если вы хотите выйти из своей подписки Azure, нажмите кнопку **Выйти**.


## Запуск приложения Spark Scala на локальном компьютере

Подключаемый модуль HDInsight Tools для IntelliJ IDEA позволяет запускать приложения Spark Scala на локальной рабочей станции. Как правило, такие приложения не требуют доступа к ресурсам кластера, таким как контейнер хранилища, и могут запускаться и тестироваться локально.

### Предварительные требования

При запуске локального приложения Spark Scala на компьютере под управлением Windows может возникнуть исключение, описанное в [SPARK 2356](https://issues.apache.org/jira/browse/SPARK-2356) и связанное с отсутствием в Windows файла WinUtils.exe. Чтобы обойти эту ошибку, [скачайте этот исполняемый файл отсюда](http://public-repo-1.hortonworks.com/hdp-win-alpha/winutils.exe), например, в папку **C:\\WinUtils\\bin**. После этого добавьте переменную среды **HADOOP\_HOME** и присвойте ей значение **C\\WinUtils**.

### Запуск локального приложения Spark Scala	 

1. Запустите IntelliJ IDEA и создайте проект. В диалоговом окне нового проекта установите параметры, как на снимке экрана ниже, а затем нажмите кнопку **Next** («Далее»).

	![Создание приложения Spark Scala](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-spark-app-local-run.png)

	* На левой панели выберите **HDInsight**.
	* На правой панели выберите **Spark on HDInsight Local Run Sample (Scala)** (Пример локального запуска Spark в HDInsight (Scala)).
	* Нажмите кнопку **Далее**.

2. Выполните действия, описанные в разделе "Запуск приложения Spark Scala в кластере HDInsight Spark", чтобы настроить необходимые параметры.

3. Запустите приложение локально на рабочей станции. Щелкните правой кнопкой мыши приложение Scala и выберите пункт **Run LogQuery** (Запустить LogQuery). В нижней части вкладки **Запуск** вы увидите выходные данные следующего вида:

	![Результат локального запуска приложения Spark](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-spark-app-local-run-result.png)

## Преобразования существующих приложений IntelliJ IDEA для использования подключаемого модуля HDInsight

Существующие приложения Spark Scala, созданные в IntelliJ IDEA, можно преобразовать и сделать совместимыми с подключаемым модулем средствами HDInsight. В результате вы сможете использовать средство для отправки приложений в кластер HDInsight Spark. Для этого выполните следующие действия.

1. Для существующего приложения Spark Scala, созданного с помощью IntelliJ IDEA, откройте соответствующий файл IML.
2. На корневом уровне вы увидите элемент **модуля** в следующем виде:

		<module org.jetbrains.idea.maven.project.MavenProjectsManager.isMavenModule="true" type="JAVA_MODULE" version="4">

3. Измените элемент, добавив `UniqueKey="HDInsightTool"`, чтобы элемент **модуля** выглядел следующим образом:

		<module org.jetbrains.idea.maven.project.MavenProjectsManager.isMavenModule="true" type="JAVA_MODULE" version="4" UniqueKey="HDInsightTool">

4. Сохраните изменения. Ваше приложение станет совместимым с подключаемым модулем средства HDInsight. Это можно проверить, щелкнув имя проекта в обозревателе проектов правой кнопкой мыши. Во всплывающем меню должна появиться возможность **отправить приложение Spark в HDInsight**.


## Устранение неполадок

### Ошибка "Используйте больший размер кучи" в локальной среде

В Spark 1.6 при использовании 32-разрядного пакета SDK для Java в локальной среде выполнения могут возникать следующие ошибки:

    Exception in thread "main" java.lang.IllegalArgumentException: System memory 259522560 must be at least 4.718592E8. Please use a larger heap size.
    	at org.apache.spark.memory.UnifiedMemoryManager$.getMaxMemory(UnifiedMemoryManager.scala:193)
    	at org.apache.spark.memory.UnifiedMemoryManager$.apply(UnifiedMemoryManager.scala:175)
    	at org.apache.spark.SparkEnv$.create(SparkEnv.scala:354)
    	at org.apache.spark.SparkEnv$.createDriverEnv(SparkEnv.scala:193)
    	at org.apache.spark.SparkContext.createSparkEnv(SparkContext.scala:288)
    	at org.apache.spark.SparkContext.<init>(SparkContext.scala:457)
    	at LogQuery$.main(LogQuery.scala:53)
    	at LogQuery.main(LogQuery.scala)
    	at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
    	at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:57)
    	at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
    	at java.lang.reflect.Method.invoke(Method.java:606)
    	at com.intellij.rt.execution.application.AppMain.main(AppMain.java:144)

Это связано с тем, что размер кучи недостаточно велик для работы кластера Spark, так как он требует не меньше 471 МБ (дополнительные сведения см. в статье о [SPARK-12081](https://issues.apache.org/jira/browse/SPARK-12081)). Простое решение – это использовать 64-разрядный пакет SDK для Java. Кроме того, можно изменить параметры виртуальной машины Java в IntelliJ, добавив следующие параметры:

    -Xms128m -Xmx512m -XX:MaxPermSize=300m -ea

![Результат локального запуска приложения Spark](./media/hdinsight-apache-spark-intellij-tool-plugin/change-heap-size.png)

## Отзывы и известные проблемы

В настоящее время просмотр выходных данных Spark напрямую не поддерживается, и мы работаем над этим.

С любыми отзывами и предложениями (а также в случае возникновения проблем при работе с этим средством) обращайтесь по адресу электронной почты hdivstool@microsoft.com.

## <a name="seealso"></a>См. также:


* [Обзор: Apache Spark в Azure HDInsight](hdinsight-apache-spark-overview.md)

### Сценарии

* [Использование Spark со средствами бизнес-аналитики. Выполнение интерактивного анализа данных с использованием Spark в HDInsight с помощью средств бизнес-аналитики](hdinsight-apache-spark-use-bi-tools.md)

* [Использование Spark с машинным обучением. Использование Spark в HDInsight для анализа температуры в здании на основе данных системы кондиционирования](hdinsight-apache-spark-ipython-notebook-machine-learning.md)

* [Использование Spark с машинным обучением. Использование Spark в HDInsight для прогнозирования результатов контроля качества пищевых продуктов](hdinsight-apache-spark-machine-learning-mllib-ipython.md)

* [Потоковая передача Spark. Использование Spark в HDInsight для сборки приложений потоковой передачи данных в режиме реального времени](hdinsight-apache-spark-eventhub-streaming.md)

* [Анализ журнала веб-сайта с использованием Spark в HDInsight](hdinsight-apache-spark-custom-library-website-log-analysis.md)

### Создание и запуск приложений

* [Удаленный запуск заданий с помощью Livy в кластере Spark](hdinsight-apache-spark-livy-rest-interface.md)

### Средства и расширения

* [Использование записных книжек Zeppelin с кластером Spark в HDInsight](hdinsight-apache-spark-use-zeppelin-notebook.md)

* [Ядра, доступные для записной книжки Jupyter в кластере Spark в HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md)

### Управление ресурсами

* [Управление ресурсами кластера Apache Spark в Azure HDInsight](hdinsight-apache-spark-resource-manager.md)

<!---HONumber=AcomDC_0601_2016-->
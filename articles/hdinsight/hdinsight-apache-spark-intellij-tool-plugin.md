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
	ms.date="02/05/2016" 
	ms.author="nitinme"/>


# Использование подключаемого модуля средств HDInsight для IntelliJ IDEA для создания и отправки приложений Spark Scala (Linux)

В этой статье приводятся пошаговые инструкции по разработке приложений Spark, написанных на языке Scala, и его отправка в кластер HDInsight Spark с помощью подключаемого модуля HDInsight для IntelliJ IDEA. Подключаемый модуль можно использовать различным образом:

* Для разработки и отправки приложений Scala Spark в кластер HDInsight Spark.
* Для доступа к ресурсам кластера Azure HDInsight Spark.
* Для разработки и локального запуска приложений Scala Spark.

>[AZURE.IMPORTANT] Это средство позволяет создавать и отправлять приложения только в кластер HDInsight Spark на Linux.


**Предварительные требования**

* Подписка Azure. См. [Бесплатная пробная версия Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* Кластер Apache Spark в HDInsight на платформе Linux. Инструкции см. в разделе [Создание кластеров Apache Spark в Azure HDInsight](hdinsight-apache-spark-jupyter-spark-sql.md).
* Комплект разработчика Oracle Java. Его можно установить [отсюда](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).
* IntelliJ IDEA. В этой статье используется версия 15.0.1. Его можно установить [отсюда](https://www.jetbrains.com/idea/download/). 


## Установка подключаемого модуля Scala для IntelliJ IDEA

Если во время установки IntelliJ IDEA вы не получили запрос на включение подключаемого модуля Scala, запустите IntelliJ IDEA и выполните следующие действия, чтобы установить его:

1. Запустите IntelliJ IDEA и на экране приветствия щелкните **Configure** («Настройка»), а затем — **Plugins** («Подключаемые модули»).

	![Включение подключаемого модуля Scala](./media/hdinsight-apache-spark-intellij-tool-plugin/enable-scala-plugin.png)

2. На следующем экране в левом нижнем углу щелкните **Install JetBrains plugin** («Установить подключаемый модуль JetBrains»). В открывшемся диалоговом окне **Browse JetBrains Plugins** («Обзор подключаемых модулей JetBrains») найдите модуль Scala и нажмите кнопку **Install** («Установить»).

	![Установка подключаемого модуля Scala](./media/hdinsight-apache-spark-intellij-tool-plugin/install-scala-plugin.png)

3. После успешной установки подключаемого модуля вам будет предложено перезапустить интегрированную среду разработки. Этот шаг можно пропустить.

## Установка подключаемого модуля средств HDInsight для IntelliJ IDEA

1. Если вы вернулись на экран приветствия IntelliJ IDEA, еще раз щелкните **Настройка**, а затем **Подключаемые модули**.

2. На следующем экране щелкните **Поиск репозиториев** в левом нижнем углу. В открывшемся диалоговом окне **Поиск репозиториев** найдите **HDInsight**, выберите **Средства Microsoft Azure HDInsight для IntelliJ** и щелкните **Установить**.

3. В появившемся запросе нажмите кнопку **Перезапустить IntelliJ IDEA**, чтобы перезапустить интегрированную среду разработки.

## Запуск приложения Spark Scala в кластере HDInsight Spark

1. Запустите IntelliJ IDEA и создайте проект. В диалоговом окне нового проекта установите параметры, как на снимке экрана ниже, а затем нажмите кнопку **Next** («Далее»).

	![Создание приложения Spark Scala](./media/hdinsight-apache-spark-intellij-tool-plugin/create-hdi-scala-app.png)

	* На левой панели выберите **HDInsight**.
	* На правой панели выберите **Spark в HDInsight (Scala)**.
	* Нажмите кнопку **Далее**.

2. В следующем окне укажите сведения о проекте.

	* Введите имя и расположение проекта.
	* Для **пакета SDK для Project** требуется версия Java выше седьмой.
	* Для **пакета SDK для Scala** щелкните **Создать**, а затем **Загрузить**, после чего выберите версию Scala. **Не используйте версию 2.11.x**. В этом примере используется версия **2.10.6**.
	
		![Создание приложения Spark Scala](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-scala-version.png)

	* Для **пакета SDK для Spark** скачайте и используйте пакет SDK [отсюда](http://go.microsoft.com/fwlink/?LinkID=723585&clcid=0x409).

		![Создание приложения Spark Scala](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-scala-project-details.png)

	* Нажмите кнопку **Готово**

3. Определите структуру проекта для создания артефакта (запакованный JAR-файл), который в конечном итоге будет содержать выполняемый в кластере код.

	1. В меню **File** («Файл») выберите пункт **Project Structure** («Структура проекта»).
	2. В диалоговом окне **Project Structure** («Структура проекта») выберите **Artifacts** («Артефакты») и щелкните знак плюса. Во всплывающем диалоговом окне щелкните **JAR**, а затем **Пустой**.

		![Создание JAR-файла](./media/hdinsight-apache-spark-intellij-tool-plugin/create-jar-1.png)

	3. Введите имя JAR-файла (например **MyClusterApp**). На панели "Доступные элементы" щелкните правой кнопкой мыши **Результат компиляции MyClusterApp** и выберите пункт **Поместить в корень выходных данных**.

		![Создание JAR-файла](./media/hdinsight-apache-spark-intellij-tool-plugin/create-jar-2.png)
	
	4. Нажмите кнопку **Применить**, а затем **ОК**.

4. Добавьте исходный код своего приложения.

	1. В **обозревателе проектов** щелкните правой кнопкой мыши **src**, наведите указатель мыши на пункт **Создать** и щелкните **Класс Scala**.

		![Добавить исходный код](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-spark-scala-code.png)

	2. В диалоговом окне **Создание класса Scala** введите имя, в поле **Вид** выберите **объект** и нажмите кнопку **ОК**.

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

	1. В **обозревателе проектов** щелкните имя проекта правой кнопкой мыши и выберите пункт **Отправить приложение Spark в HDInsight**.

		![Отправить приложение Spark](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-submit-spark-app-1.png)

	2. Вам будет предложено ввести учетные данные подписки Azure. В диалоговом окне **Отправка в Spark** введите следующие значения:

		![Отправить приложение Spark](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-submit-spark-app-2.png)

		* Для **кластеров Spark (только для Linux)** выберите кластер HDInsight Spark, в котором вы хотите запустить приложение.

		* В раскрывающемся списке **Артефакты построения** должно присутствовать имя JAR-файла, которое вы указали при выполнении предыдущих шагов.

		* В текстовом поле **Имя основного класса** щелкните многоточие (![многоточие](./media/hdinsight-apache-spark-intellij-tool-plugin/ellipsis.png)), выберите основной класс в исходном коде приложения и нажмите кнопку **ОК**.

			![Отправить приложение Spark](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-submit-spark-app-3.png)
	
		* Поскольку для кода приложения в этом примере не требуются аргументы командной строки, справочные JAR или файлы, остальные текстовые поля можно не заполнять.
	
		* Нажмите кнопку **Submit** (Отправить).

	3. На вкладке **Отправка в Spark** в нижней части окна начнет отображаться ход выполнения.

	В следующем разделе вы узнаете, как получить доступ к выходным данным задачи, используя подключаемый модуль HDInsight для IntelliJ IDEA.


## Доступ к кластерам HDInsight Spark и управление ими с помощью подключаемого модуля HDInsight для IntelliJ

С помощью подключаемого модуля HDInsight можно выполнять различные операции.

### Доступ к контейнеру хранилища для кластера

1. В меню **Вид** наведите указатель мыши на пункт **Окна инструментов** и щелкните **Обозреватель HDInsight**. При появлении запроса введите учетные данные для доступа к подписке Azure.

2. Разверните корневой узел **HDInsight**, чтобы увидеть список доступных кластеров HDInsight Spark.

3. Разверните имя кластера, чтобы увидеть учетную запись хранилища и контейнер хранилища по умолчанию для кластера.

	![Доступ к хранилищу кластера](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-spark-access-storage.png)

4. Щелкните имя связанного с кластером контейнера хранилища. На правой панели появится папка с именем **HVACOut**. Дважды щелкнув эту папку, вы увидите файлы **part-***. Откройте один из этих файлов для просмотра выходных данных приложения.

### Доступ к серверу журнала Spark

1. В **обозревателе HDInsight** щелкните имя кластера Spark правой кнопкой мыши и выберите пункт**Открыть пользовательский интерфейс журнал Spark**. При появлении запроса введите учетные данные администратора для кластера. Вы должны были указать их при подготовке кластера.

2. На панели мониторинга сервера журнала Spark вы сможете найти приложение, выполнение которого только что было завершено по его имени. В приведенном выше коде имя приложения было указано с помощью `val conf = new SparkConf().setAppName("MyClusterApp")`. Таким образом, имя приложения Spark выглядело как **MyClusterApp**.

### Запуск портала Ambari

В **обозревателе HDInsight** щелкните имя кластера Spark правой кнопкой мыши и выберите пункт **Открыть портал управления кластерами (Ambari)**. При появлении запроса введите учетные данные администратора для кластера. Вы должны были указать их при подготовке кластера.

### Управление подписками Azure

По умолчанию подключаемый модуль HDInsight содержит список кластеров Spark из всех ваших подписок Azure. При необходимости можно указать подписки, кластеры которых вас интересуют. В **обозревателе HDInsight** щелкните правой кнопкой мыши корневой узел **HDInsight** и выберите пункт **Управление подписками**. В диалоговом окне снимите флажки напротив подписок, доступ к которым вам не требуется, и нажмите кнопку **Закрыть**. Если вы хотите выйти из своей подписки Azure, нажмите кнопку **Выйти**.


## Запуск приложения Spark Scala на локальном компьютере

Подключаемый модуль HDInsight Tools для IntelliJ IDEA позволяет запускать приложения Spark Scala на локальной рабочей станции. Как правило, такие приложения не требуют доступа к ресурсам кластера, таким как контейнер хранилища, и могут запускаться и тестироваться локально.

### Предварительные требования

При запуске локального приложения Spark Scala на компьютере под управлением Windows может возникнуть исключение, описанное в [SPARK 2356](https://issues.apache.org/jira/browse/SPARK-2356) и связанное с отсутствием в Windows файла WinUtils.exe. Чтобы обойти эту ошибку, [загрузите исполняемый файл отсюда](http://public-repo-1.hortonworks.com/hdp-win-alpha/winutils.exe) в папку вида **C:\\WinUtils\\bin**. После этого добавьте переменную среды **HADOOP\_HOME** и присвойте ей значение **C\\WinUtils**.

### Запуск локального приложения Spark Scala	 

1. Запустите IntelliJ IDEA и создайте проект. В диалоговом окне нового проекта установите параметры, как на снимке экрана ниже, а затем нажмите кнопку **Next** («Далее»).

	![Создание приложения Spark Scala](./media/hdinsight-apache-spark-intellij-tool-plugin/create-hdi-scala-app.png)

	* На левой панели выберите **HDInsight**.
	* На правой панели выберите **Spark в HDInsight (Scala)**.
	* Нажмите кнопку **Далее**.

2. В следующем окне укажите сведения о проекте.

	* Введите имя и расположение проекта.
	* Для **пакета SDK для Project** требуется версия Java выше седьмой.
	* Для **пакета SDK для Scala** щелкните **Создать**, а затем **Загрузить**, после чего выберите версию Scala. **Не используйте версию 2.11.x**. В этом примере используется версия **2.10.6**.
	
		![Создание приложения Spark Scala](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-scala-version.png)

	* Для **пакета SDK для Spark** скачайте и используйте пакет SDK [отсюда](http://go.microsoft.com/fwlink/?LinkID=723585&clcid=0x409).
	* Нажмите кнопку **Готово**

		![Создание приложения Spark Scala](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-scala-local-project-details.png)

3. Определите структуру проекта для создания артефакта (запакованный JAR-файл), который в конечном итоге будет содержать выполняемый в кластере код.

	1. В меню **File** («Файл») выберите пункт **Project Structure** («Структура проекта»).
	2. В диалоговом окне **Project Structure** («Структура проекта») выберите **Artifacts** («Артефакты») и щелкните знак плюса. Во всплывающем диалоговом окне щелкните **JAR**, а затем **Пустой**.

		![Создание JAR-файла](./media/hdinsight-apache-spark-intellij-tool-plugin/create-jar-1.png)

	3. Введите имя JAR-файла (например **MyLocalApp**). На панели "Доступные элементы" щелкните правой кнопкой мыши **Результат компиляции MyLocalApp** и выберите пункт **Поместить в корень выходных данных**.

		![Создание JAR-файла](./media/hdinsight-apache-spark-intellij-tool-plugin/create-local-jar-2.png)
	
	4. Нажмите кнопку **Применить**, а затем **ОК**.

4. Добавьте исходный код своего приложения.

	1. В **обозревателе проектов** щелкните правой кнопкой мыши **src**, наведите указатель мыши на пункт **Создать** и щелкните **Класс Scala**.

		![Добавить исходный код](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-spark-local-scala-code.png)

	2. В диалоговом окне **Создание класса Scala** введите имя, в поле **Вид** выберите **объект** и нажмите кнопку **ОК**.

		![Добавить исходный код](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-spark-local-scala-code-object.png)

	3. Вставьте в файл **MyLocalApp.scala** следующий код: Этот код считывает пример входного текста на компьютере и показывает, сколько строк в этом текстовом файле содержит символы "a" и "b".


			import org.apache.spark.SparkContext
			import org.apache.spark.SparkContext._
			import org.apache.spark.SparkConf
			
			object MyLocalApp {
			  def main(args: Array[String]) {
			    val logFile = "C:/users/nitinme/Desktop/commands.txt" // Should be some file on your system
			    val conf = new SparkConf().setAppName("MyLocalApp").setMaster("local[2]")
			    val sc = new SparkContext(conf)
			    val logData = sc.textFile(logFile, 2).cache()
			    val numAs = logData.filter(line => line.contains("a")).count()
			    val numBs = logData.filter(line => line.contains("b")).count()
			    println("Lines with a: %s, Lines with b: %s".format(numAs, numBs))
			  }
			}

5. Запустите приложение локально на рабочей станции. В меню **Запуск** щелкните элемент **Запуск MyLocalApp**. В нижней части вкладки **Запуск** вы увидите выходные данные следующего вида:

		...
		...
		Lines with a: 4, Lines with b: 4
		...
		...
		16/02/01 15:04:05 INFO SparkContext: Successfully stopped SparkContext
		16/02/01 15:04:05 INFO ShutdownHookManager: Shutdown hook called
		16/02/01 15:04:05 INFO ShutdownHookManager: Deleting directory C:\Users\nitinme\AppData\Local\Temp\spark-618dee33-45a3-4bce-a8fc-bf85663133b3
		
		Process finished with exit code 0


## Преобразования существующих приложений IntelliJ IDEA для использования подключаемого модуля HDInsight

Существующие приложения Spark Scala, созданные в IntelliJ IDEA, можно преобразовать и сделать совместимыми с подключаемым модулем средствами HDInsight. В результате вы сможете использовать средство для отправки приложений в кластер HDInsight Spark. Для этого выполните следующие действия.

1. Для существующего приложения Spark Scala, созданного с помощью IntelliJ IDEA, откройте соответствующий файл IML.
2. На корневом уровне вы увидите **модуль** элемента следующим образом:

		<module org.jetbrains.idea.maven.project.MavenProjectsManager.isMavenModule="true" type="JAVA_MODULE" version="4">

3. Измените элемент, добавив `UniqueKey="HDInsightTool"`, чтобы элемент **модуля** выглядел следующим образом:

		<module org.jetbrains.idea.maven.project.MavenProjectsManager.isMavenModule="true" type="JAVA_MODULE" version="4" UniqueKey="HDInsightTool">

4. Сохраните изменения. Ваше приложение станет совместимым с подключаемым модулем средства HDInsight. Это можно проверить, щелкнув имя проекта в обозревателе проектов правой кнопкой мыши. Во всплывающем меню должна появиться возможность **отправить приложение Spark в HDInsight**.

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

<!---HONumber=AcomDC_0302_2016-->
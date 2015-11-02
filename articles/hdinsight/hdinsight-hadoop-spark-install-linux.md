<properties 
	pageTitle="Использование действия сценария для установки Apache Spark в кластере HDInsight (Hadoop) под управлением Linux | Microsoft Azure" 
	description="Узнайте, как установить Spark в кластере HDInsight на основе Linux с помощью действий сценария. Действия сценария позволяют настроить кластер во время создания, изменяя его конфигурацию или устанавливая службы и служебные программы." 
	services="hdinsight" 
	documentationCenter="" 
	authors="Blackmist" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/19/2015" 
	ms.author="larryfr"/>

# Установка и использование Spark в кластерах HDInsight Hadoop

В этом документе описано, как установить Spark с помощью действия сценария. Действие сценария позволяет выполнять сценарии для настройки кластера только во время его создания. Дополнительные сведения см. в разделе [Настройка кластера HDInsight с помощью действия скрипта][hdinsight-cluster-customize]. После установки Spark вы также узнаете, как осуществлять запрос Spark на кластерах HDInsight.

> [AZURE.NOTE]HDInsight также предоставляет Spark как тип кластера. Это означает, что теперь можно непосредственно подготовить кластер Spark без изменения кластера Hadoop. Однако на данный момент это ограничено только кластерами на основе Windows . С помощью типа кластера Spark вы получаете кластер HDInsight версии 3.2 с версией Spark 1.3.1 под управлением Windows. Дополнительные сведения см. в разделе [Приступая к работе с Apache Spark в HDInsight](hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql.md).

## <a name="whatis"></a>Что такое Spark

<a href="http://spark.apache.org/docs/latest/index.html" target="_blank">Apache Spark</a> — это платформа параллельной обработки с открытым исходным кодом, которая поддерживает обработку в памяти, чтобы повысить производительность приложений для анализа данных большого размера. Возможности вычисления в памяти Spark отлично подходят для итеративных алгоритмов в машинном обучении и графовых вычислениях.

Spark также может использоваться для выполнения обычной обработки данных на диске. Spark оптимизирует традиционную платформу MapReduce, позволяя избежать операций записи на диск на промежуточных этапах. К тому же, Spark совместима с распределенной файловой системой Hadoop (HDFS) и хранилищем больших двоичных объектов Azure, поэтому существующие данные могут легко обрабатываться с помощью этой платформы.

Этот раздел содержит инструкции по настройке кластера HDInsight для установки Spark.

## <a name="whatis"></a>Какую версию Spark можно установить

В этом разделе мы используем настраиваемый сценарий "Действия сценария" для установки Spark в кластере HDInsight. Этот сценарий устанавливает Spark 1.5.1.

Вы можете изменить этот сценарий или создать собственный, чтобы установить другие версии Spark.

## Что делает сценарий

Этот сценарий устанавливает Spark версии 1.5.1 в `/usr/hdp/current/spark`.

> [AZURE.WARNING]Вы можете обнаружить, что в вашем кластере HDInsight по умолчанию установлены некоторые двоичные файлы Spark 1.3.1. Их не следует использовать, так как в последующем обновлении они будут удалены из образа кластера HDInsight.

## <a name="install"></a>Установка Spark с помощью действий сценария

Пример сценария для установки Spark на кластере HDInsight доступен по ссылке на хранилище больших двоичных объектов Azure (только для чтения): [https://hdiconfigactions.blob.core.windows.net/linuxsparkconfigactionv02/spark-installer-v02.sh](https://hdiconfigactions.blob.core.windows.net/linuxsparkconfigactionv02/spark-installer-v02.sh). Этот раздел содержит инструкции по использованию примера сценария при создании кластера с помощью портала Azure.

> [AZURE.NOTE]Для создания кластера с помощью этого сценария также можно использовать Azure PowerShell или пакет SDK HDInsight для .NET. Дополнительную информацию об использовании этих методов см. в статье [Настройка кластеров HDInsight с помощью действий сценария](hdinsight-hadoop-customize-cluster-linux.md).

1. Приступите к созданию кластера с помощью действий, описанных в разделе [Создание кластеров HDInsight под управлением Linux](hdinsight-provision-linux-clusters.md#portal), но не завершайте создание.

2. В колонке **Необязательная конфигурация** выберите **Действия сценария** и введите следующие сведения.

	* __ИМЯ__: введите понятное имя для действия сценария.
	* __УНИВЕРСАЛЬНЫЙ КОД РЕСУРСА (URI) СКРИПТА__: https://hdiconfigactions.blob.core.windows.net/linuxsparkconfigactionv02/spark-installer-v02.sh.
	* __ГОЛОВНОЙ__: установите флажок.
	* __РАБОЧИЙ__: снимите флажок.
	* __ZOOKEEPER__: снимите флажок.
	* __ПАРАМЕТРЫ__: оставьте это поле пустым.
    
    > [AZURE.NOTE]Пример сценария Spark устанавливает компоненты только на головных узлах, поэтому флажки для других типов узлов можно снять.

3. В нижней части раздела **Действия сценария** нажмите кнопку **Выбрать**, чтобы сохранить конфигурацию. Наконец, нажмите кнопку **Выбрать** в нижней части колонки **Необязательная конфигурация**, чтобы сохранить дополнительные настройки.

4. Продолжите подготовку кластера, как описано в разделе [Создание кластеров HDInsight под управлением Linux](hdinsight-provision-linux-clusters.md#portal).

## <a name="usespark"></a>Как использовать Spark в HDInsight

Spark предоставляет интерфейсы API в Scala, Python и Java. Также можно использовать интерактивную оболочку Spark для выполнения запросов Spark. После завершения создания кластера используйте следующую команду для подключения к кластеру HDInsight:

	ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
	
Дополнительные сведения об использовании SSH с HDInsight см. в следующих статьях:

* [Использование SSH с Hadoop под управлением Linux в HDInsight в Linux, Unix или OS X](hdinsight-hadoop-linux-use-ssh-unix.md)

* [Использование SSH с Hadoop под управлением Linux в HDInsight в Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

После установления соединения используйте следующие разделы для конкретных шагов по использованию Spark:

- [использование оболочки Spark для выполнения интерактивных запросов](#sparkshell);
- [использование оболочки Spark для выполнения запросов Spark SQL](#sparksql); 
- [использование отдельной программы Scala](#standalone).

###<a name="sparkshell"></a>Использование оболочки Spark для выполнения интерактивных запросов

1. Выполните следующую команду, чтобы запустить оболочку Spark:

		 /usr/hdp/current/spark/bin/spark-shell --master yarn

	После завершения выполнения команды должна появиться строка запроса Scala:

		 scala>

5. Введите в эту строку запрос Spark, указанный ниже. Этот запрос подсчитывает количество повторений каждого слова в файле davinci.txt, который доступен в расположении /example/data/gutenberg/ связанного с кластером хранилища больших двоичных объектов Azure.

		val file = sc.textFile("/example/data/gutenberg/davinci.txt")
		val counts = file.flatMap(line => line.split(" ")).map(word => (word, 1)).reduceByKey(_ + _)
		counts.toArray().foreach(println)

6. Результат должен выглядеть так:

		(felt,,1)
		(axle-tree,1)
		(deals,9)
		(virtuous,4)

7. Введите :q, чтобы выйти из строки Scala.

		:q

###<a name="sparksql"></a>Использование оболочки Spark для выполнения запросов Spark SQL

Spark SQL позволяет использовать Spark для выполнения реляционных запросов на языке SQL, HiveQL или Scala. В этом разделе мы рассмотрим использование Spark для выполнения запроса Hive на примере таблицы Hive. Таблица Hive, используемая в данном разделе (**hivesampletable**), доступна по умолчанию при создании кластера.

1. Выполните следующую команду, чтобы запустить оболочку Spark:

		 /usr/hdp/current/spark/bin/spark-shell --master yarn

	После завершения выполнения команды должна появиться строка запроса Scala:

		 scala>

2. Задайте контекст Hive в командной строке Scala. Это необходимо для работы с запросами Hive с помощью Spark.

		val hiveContext = new org.apache.spark.sql.hive.HiveContext(sc)

	> [AZURE.NOTE]`sc` в этом выражении — это контекст Spark по умолчанию, который задается при запуске оболочки Spark.

5. Выполните запрос Hive, используя контекст Hive, и выведите выходные данные на консоль. Запрос получает данные конкретных устройств и ограничивает количество полученных записей до 20.

		hiveContext.sql("""SELECT * FROM hivesampletable WHERE devicemake LIKE "HTC%" LIMIT 20""").collect().foreach(println)

6. Вы должны увидеть подобные выходные данные:

		[820,11:35:17,ru-RU,Android,HTC,Inspire 4G,Louisiana,UnitedStates, 2.7383836,0,1]
		[1055,17:24:08,ru-RU,Android,HTC,Incredible,Ohio,United States,18.0894738,0,0]
		[1067,03:42:29,ru-RU,Windows Phone,HTC,HD7,District Of Columbia,United States,null,0,0]

7. Введите :q, чтобы выйти из строки Scala.

		:q

### <a name="standalone"></a>Использование отдельной программы Scala

В этом разделе вы напишете код приложения Scala, которое подсчитывает количество строк, содержащих буквы a и b, в примере файла данных (/example/data/gutenberg/davinci.txt).

1. Чтобы установить средство сборки Scala, используйте следующие команды.

		echo "deb http://dl.bintray.com/sbt/debian /" | sudo tee -a /etc/apt/sources.list.d/sbt.list
		sudo apt-get update
		sudo apt-get install sbt
		
	При появлении запроса выберите __Y__ для продолжения.

2. Создайте структуру каталогов для проекта Scala:

		mkdir -p SimpleScalaApp/src/main/scala
		
3. Создайте новый файл с именем __simple.sbt__, который содержит сведения о конфигурации для этого проекта.

		cd SimpleScalaApp
		nano simple.sbt
		
	Добавьте в этот файл следующее содержимое:

		name := "SimpleApp"
	
		version := "1.0"
	
		scalaVersion := "2.10.4"
	
		libraryDependencies += "org.apache.spark" %% "spark-core" % "1.2.0"


	> [AZURE.NOTE]Убедитесь, что между записями сохранились пустые строки.
	
	Нажмите сочетание клавиш __CTRL + X__, затем клавишу __Y__ и клавишу __ВВОД__ для сохранения файла.

4. Используйте следующую команду, чтобы создать новый файл с именем __SimpleApp.scala__ в каталоге __SimpleScalaApp/src/main/scala__:

		nano src/main/scala/SimpleApp.scala

	Добавьте в этот файл следующее содержимое:

		/* SimpleApp.scala */
		import org.apache.spark.SparkContext
		import org.apache.spark.SparkContext._
		import org.apache.spark.SparkConf
		
		object SimpleApp {
		  def main(args: Array[String]) {
		    val logFile = "/example/data/gutenberg/davinci.txt"			//Location of the sample data file on Azure Blob storage
		    val conf = new SparkConf().setAppName("SimpleApplication")
		    val sc = new SparkContext(conf)
		    val logData = sc.textFile(logFile, 2).cache()
		    val numAs = logData.filter(line => line.contains("a")).count()
		    val numBs = logData.filter(line => line.contains("b")).count()
		    println("Lines with a: %s, Lines with b: %s".format(numAs, numBs))
		  }
		}

	Нажмите сочетание клавиш __CTRL + X__, затем клавишу __Y__ и клавишу __ВВОД__ для сохранения файла.

5. В каталоге __SimpleScalaApp__ выполните следующую команду для сборки приложения и сохраните его в JAR-файле:

		sbt package

	После компиляции приложения в каталоге \_\_SimpleScalaApp/target/scala-2.10** появится файл **simpleapp\_2.10-1.0.jar**.

6. Введите следующую команду для запуска программы SimpleApp.scala:


		/usr/hdp/current/spark/bin/spark-submit --class "SimpleApp" --master yarn target/scala-2.10/simpleapp_2.10-1.0.jar

4. После завершения работы программы вывод отображается на консоли.

		Lines with a: 21374, Lines with b: 11430

## Дальнейшие действия

- [Установка и использование Hue в кластерах HDInsight](hdinsight-hadoop-hue-linux.md). Hue — это веб-интерфейс, который позволяет легко создавать, запускать и сохранять задания Pig и Hive, а также просматривать содержимое хранилища по умолчанию для вашего кластера HDInsight.

- Статья [Установка R в кластерах HDInsight][hdinsight-install-r] содержит указания по применению настройки кластера для установки и использования R в кластерах HDInsight Hadoop. R — это открытый язык программирования и свободная программная среда для статистических вычислений. Он предоставляет сотни встраиваемых статистических функций и собственный язык программирования, который сочетает аспекты функционального и объектно-ориентированного программирования. Этот проект также обеспечивает обширные графические возможности.

- [Установка Giraph в кластерах HDInsight](hdinsight-hadoop-giraph-install-linux.md). Используйте настройки кластера для установки Giraph в кластерах HDInsight Hadoop. Giraph позволяет выполнять обработку графов с использованием Hadoop и может использоваться с Azure HDInsight.

- [Установка Solr в кластерах HDInsight](hdinsight-hadoop-solr-install-linux.md). Используйте настройки кластера для установки Solr в кластерах HDInsight Hadoop. Solr позволяет вести расширенный поиск по хранимым данным.

- [Установка Hue в кластерах HDInsight](hdinsight-hadoop-hue-linux.md). Установить Hue в кластерах HDInsight Hadoop можно при помощи настройки кластера. Hue — это набор веб-приложений, используемых для взаимодействия с кластером Hadoop.



[hdinsight-provision]: hdinsight-provision-clusters-linux.md
[hdinsight-install-r]: hdinsight-hadoop-r-scripts-linux.md
[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster-linux.md
[powershell-install-configure]: ../install-configure-powershell.md
 

<!---HONumber=Oct15_HO4-->
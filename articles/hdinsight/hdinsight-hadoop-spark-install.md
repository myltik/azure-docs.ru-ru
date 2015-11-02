<properties
	pageTitle="Использование действия сценария для установки Spark в кластере Hadoop | Microsoft Azure"
	description="Узнайте, как настроить кластер HDInsight для Spark помощью действия сценария."
	services="hdinsight"
	documentationCenter=""
	authors="nitinme"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/15/2015"
	ms.author="nitinme"/>

# Установка и использование Spark в кластерах HDInsight Hadoop с помощью действия сценария

> [AZURE.IMPORTANT]Теперь эта статья считается устаревшей. Теперь HDInsight предоставляет Spark как тип кластера первого класса для кластеров на основе Windows. Это означает, что теперь можно создать кластер Spark без изменения кластера Hadoop с помощью действия сценария. С помощью типа кластера Spark вы получаете кластер HDInsight версии 3.2 с версией Spark 1.3.1. Для установки различных версий Spark можно использовать действие сценария. В HDInsight есть пример действия сценария.

Научитесь устанавливать Spark на кластер HDInsight под управлением Windows с помощью действия сценария и выполнять запросы Spark на кластерах HDInsight.


**Связанные статьи**: [Установка Spark в кластерах HDInsight под управлением Linux](hdinsight-hadoop-spark-install-linux.md).

- [Создание кластеров Hadoop в HDInsight](hdinsight-provision-clusters.md). Общие сведения о создании кластеров HDInsight.

- [Приступая к работе с Apache Spark в HDInsight](hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql.md). Создание кластера типа Spark в операционной системе Windows.

- [Настройка кластера HDInsight с помощью действия сценария][hdinsight-cluster-customize]. Общая информация о настройке кластеров HDInsight с помощью действия сценария.

- [Разработка скриптов действия сценария для HDInsight](hdinsight-hadoop-script-actions.md).

## Что такое Spark?

<a href="http://spark.apache.org/docs/latest/index.html" target="_blank">Apache Spark</a> — это платформа параллельной обработки с открытым исходным кодом, которая поддерживает обработку в памяти, чтобы повысить производительность приложений для анализа данных большого размера. Возможности вычисления в памяти Spark отлично подходят для итеративных алгоритмов в машинном обучении и графовых вычислениях.

Spark также может использоваться для выполнения обычной обработки данных на диске. Spark оптимизирует традиционную платформу MapReduce, позволяя избежать операций записи на диск на промежуточных этапах. К тому же, Spark совместима с распределенной файловой системой Hadoop (HDFS) и хранилищем больших двоичных объектов Azure, поэтому существующие данные могут легко обрабатываться с помощью этой платформы.

Этот раздел содержит инструкции по настройке кластера HDInsight для установки Spark.

## Установка Spark с помощью портала предварительной версии Azure

Пример сценария для установки Spark в кластере HDInsight доступен в большом двоичном объекте службы хранилища Azure (доступ только для чтения) по адресу [https://hdiconfigactions.blob.core.windows.net/sparkconfigactionv03/spark-installer-v03.ps1](https://hdiconfigactions.blob.core.windows.net/sparkconfigactionv03/spark-installer-v03.ps1). Этот сценарий может установить Spark 1.2.0 или Spark 1.0.2 в зависимости от версии создаваемого кластера HDInsight.

- Если вы используете этот сценарий при создании кластера **HDInsight 3.2**, он устанавливает **Spark 1.2.0**.
- Если вы используете этот сценарий при создании кластера **HDInsight 3.1**, он устанавливает **Spark 1.0.2**.

Вы можете изменить этот сценарий или создать собственный, чтобы установить другие версии Spark.

> [AZURE.NOTE]Пример сценария работает только с кластером HDInsight версии 3.1 и 3.2. Дополнительную информацию о версиях кластера HDInsight см. в статье [Новые возможности версий кластеров Hadoop, предоставляемых HDInsight](hdinsight-component-versioning.md).

1. Начните создание кластера с помощью параметра **НАСТРАИВАЕМОЕ СОЗДАНИЕ**, как описано в разделе [Создание кластеров Hadoop в HDInsight](hdinsight-provision-clusters.md#portal). Выберите необходимую версию кластера в соответствии со следующими требованиями.

	- Если вы хотите установить **Spark 1.2.0**, создайте кластер HDInsight 3.2.
	- Если вы хотите установить **Spark 1.0.2**, создайте кластер HDInsight 3.1.


2. На странице **Действия сценариев** мастера щелкните **Добавить действие сценария** для предоставления информации о данном действии сценария, как показано ниже:

	![Использование действия сценария для настройки кластера](./media/hdinsight-hadoop-spark-install/HDI.CustomProvision.Page6.png "Использование действия сценария для настройки кластера")

	<table border='1'>
	<tr><th>Свойство</th><th>Значение</th></tr>
	<tr><td>Имя</td>
		<td>Укажите имя для действия сценария. Например, <b>Установить Spark</b>.</td></tr>
	<tr><td>URI-адрес сценария</td>
		<td>Укажите универсальный идентификатор ресурса (URI) для сценария, который вызывается для настройки кластера. Например, <i>https://hdiconfigactions.blob.core.windows.net/sparkconfigactionv03/spark-installer-v03.ps1</i>.</td></tr>
	<tr><td>Тип узла</td>
		<td>Укажите узлы, на которых выполняется сценарий настройки. Можно выбрать одно из трех значений: <b>Все узлы</b>, <b>Только головные узлы</b> или <b>Только рабочие узлы</b>.
	<tr><td>Параметры</td>
		<td>Укажите параметры, если они требуются для сценария. Скрипт для установки Spark не требует никаких параметров, поэтому можно оставить это поле пустым.</td></tr>
</table>Можно добавить несколько действий сценария для установки нескольких компонентов в кластере. После добавления скриптов щелкните флажок, чтобы начать создание кластера.

Сценарий можно также использовать для установки Spark в HDInsight с помощью Azure PowerShell или пакета SDK для HDInsight .NET. Инструкции по выполнению этих процедур приведены ниже в этом разделе.

## Использование Spark в HDInsight
Spark предоставляет интерфейсы API в Scala, Python и Java. Также можно использовать интерактивную оболочку Spark для выполнения запросов Spark. Этот раздел содержит указания по использованию различных подходов для работы с платформой Spark:

- [Использование оболочки Spark для выполнения интерактивных запросов](#sparkshell)
- [Использование оболочки Spark для выполнения запросов Spark SQL](#sparksql)
- [Использование автономной программы Scala](#standalone)

###<a name="sparkshell"></a>Использование оболочки Spark для выполнения интерактивных запросов
Выполните следующие действия для запуска запросов Spark из интерактивной оболочки Spark. В этом разделе мы выполним запрос Spark на примере файла данных (/ example/data/gutenberg/davinci.txt), который по умолчанию присутствует в кластерах HDInsight.

1. На портале Azure включите удаленный рабочий стол для созданного кластера с установленной системой Spark, а затем подключитесь к кластеру. Указания см. в разделе <a href="http://azure.microsoft.com/documentation/articles/hdinsight-administer-use-management-portal/#rdp" target="_blank">Подключение к кластерам HDInsight с помощью RDP</a>.

2. В сеансе по протоколу удаленного рабочего стола (RDP) с рабочего стола откройте командную строку Hadoop (с помощью ярлыка на рабочем столе) и перейдите к расположению, в котором установлена Spark, например **C:\\apps\\dist\\spark-1.2.0**.


3. Выполните следующую команду, чтобы запустить оболочку Spark:

		 .\bin\spark-shell --master yarn

	После завершения выполнения команды должна появиться строка запроса Scala:

		 scala>

5. Введите в эту строку запрос Spark, указанный ниже. Этот запрос подсчитывает количество повторений каждого слова в файле davinci.txt, который доступен в расположении /example/data/gutenberg/ связанного с кластером хранилища больших двоичных объектов Azure.

		val file = sc.textFile("/example/data/gutenberg/davinci.txt")
		val counts = file.flatMap(line => line.split(" ")).map(word => (word, 1)).reduceByKey(_ + _)
		counts.toArray().foreach(println)

6. Результат должен выглядеть так:

	![Выходные данные, отображаемые после выполнения интерактивной оболочки Scala в кластере HDInsight](./media/hdinsight-hadoop-spark-install/hdi-scala-interactive.png)


7. Введите :q, чтобы выйти из строки Scala.

		:q

###<a name="sparksql"></a>Использование оболочки Spark для выполнения запросов Spark SQL

Spark SQL позволяет использовать Spark для выполнения реляционных запросов на языке SQL, HiveQL или Scala. В этом разделе мы рассмотрим использование Spark для выполнения запроса Hive на примере таблицы Hive. Таблица Hive, используемая в данном разделе (**hivesampletable**), доступна по умолчанию при создании кластера.

>[AZURE.NOTE]Пример ниже был создан с использованием **Spark 1.2.0**, который устанавливается, если запустить действие сценария при создании кластера HDInsight 3.2.

1. На портале Azure включите удаленный рабочий стол для созданного кластера с установленной системой Spark, а затем подключитесь к кластеру. Указания см. в разделе <a href="http://azure.microsoft.com/documentation/articles/hdinsight-administer-use-management-portal/#rdp" target="_blank">Подключение к кластерам HDInsight с помощью RDP</a>.

2. В сеансе RDP с рабочего стола откройте командную строку Hadoop (с помощью ярлыка на рабочем столе) и перейдите к расположению, в котором установлена Spark, например **C:\\apps\\dist\\spark-1.2.0**.


3. Выполните следующую команду, чтобы запустить оболочку Spark:

		 .\bin\spark-shell --master yarn

	После завершения выполнения команды должна появиться строка запроса Scala:

		 scala>

4. Задайте контекст Hive в командной строке Scala. Это необходимо для работы с запросами Hive с помощью Spark.

		val hiveContext = new org.apache.spark.sql.hive.HiveContext(sc)

	Обратите внимание, что **sc** — контекст Spark по умолчанию, который задается при запуске оболочки Spark.

5. Выполните запрос Hive, используя контекст Hive, и выведите выходные данные на консоль. Запрос получает данные конкретных устройств и ограничивает количество полученных записей до 20.

		hiveContext.sql("""SELECT * FROM hivesampletable WHERE devicemake LIKE "HTC%" LIMIT 20""").collect().foreach(println)

6. Вы должны увидеть подобные выходные данные:

	![Выходные данные, отображаемые после выполнения Spark SQL в кластере HDInsight](./media/hdinsight-hadoop-spark-install/hdi-spark-sql.png)

7. Введите :q, чтобы выйти из строки Scala.

		:q

### <a name="standalone"></a>Использование автономной программы Scala

В этом разделе мы напишем код приложения Scala, которое подсчитывает количество строк, содержащих буквы a и b, в примере файла данных (/example/data/gutenberg/davinci.txt), который по умолчанию присутствует в кластерах HDInsight. Для написания и использования отдельной программы Scala с кластером, настроенным при помощи установки Spark, необходимо выполнить следующие действия:

- Напишите программу Scala.
- Создайте программу Scala для получения JAR-файла.
- Запустите задание на кластере.

#### Напишите программу Scala.
В этом разделе показано, как написать программу Scala, которая подсчитывает количество строк, содержащих буквы a и b, в примере файла данных.

1. Откройте текстовый редактор и вставьте следующий код:


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

2. Сохраните файл с именем **SimpleApp.scala**.

#### Построение программы Scala
В этом разделе мы используем <a href="http://www.scala-sbt.org/0.13/docs/index.html" target="_blank">Simple Build Tool</a> (SBT) для создания программы Scala. Для работы SBT требуется Java 1.6 или более поздней версии, поэтому перед тем как продолжить, убедитесь, что у вас установлена соответствующая версия Java.

1. Установите SBT из http://www.scala-sbt.org/0.13/tutorial/Installing-sbt-on-Windows.html.
2. Создайте папку **SimpleScalaApp**, а в этой папке — файл с именем **simple.sbt**. Это файл конфигурации, содержащий информацию о версии Scala, зависимостях библиотеки и т. д. Вставьте следующий код в файл simple.sbt и сохраните его:


		name := "SimpleApp"

		version := "1.0"

		scalaVersion := "2.10.4"

		libraryDependencies += "org.apache.spark" %% "spark-core" % "1.2.0"



	>[AZURE.NOTE]Убедитесь, что в файле сохранились пустые строки.


3. В папке **SimpleScalaApp** создайте структуру каталогов **\\src\\main\\scala** и вставьте программу Scala (**SimpleApp.scala**), созданную ранее в папке \\src\\main\\scala.
4. Откройте командную строку, перейдите в каталог SimpleScalaApp и введите следующую команду:


		sbt package


	После компиляции приложения появится файл **simpleapp\_2.10-1.0.jar**, созданный в каталоге **\\target\\scala-2.10** корневой папки SimpleScalaApp.


#### Запустите задание на кластере.
В этом разделе содержатся указания по удаленному подключению к кластеру со Spark и последующему копированию конечной папки проекта SimpleScalaApp. Вы можете использовать команду **spark-submit** для отправки задания в кластер.

1. Установите удаленное подключение к кластеру с установленной Spark. На компьютере, который использовался для написания и создания программы SimpleApp.scala, скопируйте папку **SimpleScalaApp\\target** в расположение в кластере.
2. В сеансе RDP с рабочего стола откройте командную строку Hadoop и перейдите к расположению, в которое была скопирована папка **target**.
3. Введите следующую команду для запуска программы SimpleApp.scala:


		C:\apps\dist\spark-1.2.0\bin\spark-submit --class "SimpleApp" --master local target/scala-2.10/simpleapp_2.10-1.0.jar

4. После завершения работы программы вывод отображается на консоли.


		Lines with a: 21374, Lines with b: 11430

## Установка Spark с помощью Azure PowerShell

В этом разделе мы используем командлет **<a href = "http://msdn.microsoft.com/library/dn858088.aspx" target="_blank">Add-AzureHDInsightScriptAction</a>**, чтобы вызвать сценарии с помощью действия сценария для настройки кластера. Прежде чем продолжить, убедитесь, что вы установили и настроили Azure PowerShell. Информацию о настройке рабочей станции для запуска командлетов Azure PowerShell для HDInsight см. в статье [Как установить и настроить Azure PowerShell][powershell-install-configure].

Выполните следующие действия:

1. Откройте окно Azure PowerShell и объявите следующие переменные:

		# Provide values for these variables
		$subscriptionName = "<SubscriptionName>"		# Name of the Azure subscription
		$clusterName = "<HDInsightClusterName>"			# HDInsight cluster name
		$storageAccountName = "<StorageAccountName>"	# Azure Storage account that hosts the default container
		$storageAccountKey = "<StorageAccountKey>"      # Key for the Storage account
		$containerName = $clusterName
		$location = "<MicrosoftDataCenter>"				# Location of the HDInsight cluster. It must be in the same data center as the Storage account.
		$clusterNodes = <ClusterSizeInNumbers>			# Number of nodes in the HDInsight cluster
		$version = "<HDInsightClusterVersion>"          # For example, "3.2"

2. Укажите такие значения настройки, как узлы в кластере и хранилище для использования по умолчанию.

		# Specify the configuration options
		Select-AzureSubscription $subscriptionName
		$config = New-AzureHDInsightClusterConfig -ClusterSizeInNodes $clusterNodes
		$config.DefaultStorageAccount.StorageAccountName="$storageAccountName.blob.core.windows.net"
		$config.DefaultStorageAccount.StorageAccountKey=$storageAccountKey
		$config.DefaultStorageAccount.StorageContainerName=$containerName

3. Используйте командлет **Add-AzureHDInsightScriptAction**, чтобы добавить действие сценария в конфигурацию кластера. Позже, а именно при создании кластера, действие сценария начнет выполняться.

		# Add a script action to the cluster configuration
		$config = Add-AzureHDInsightScriptAction -Config $config -Name "Install Spark" -ClusterRoleCollection HeadNode -Uri https://hdiconfigactions.blob.core.windows.net/sparkconfigactionv03/spark-installer-v03.ps1

	Командлет **Add-AzureHDInsightScriptAction** принимает следующие параметры:

	<table style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse;">
<tr>
<th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:90px; padding-left:5px; padding-right:5px;">Параметр</th>
<th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:550px; padding-left:5px; padding-right:5px;">Определение</th></tr>
<tr>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Настройка</td>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px; padding-right:5px;">Объект конфигурации, к которому добавляются сведения о действии скрипта.</td></tr>
<tr>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Имя</td>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Имя действия скрипта</td></tr>
<tr>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">ClusterRoleCollection</td>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Указывает узлы, на которых выполняется скрипт настройки. Допустимые значения: HeadNode (для установки на головном узле) или DataNode (для установки на всех узлах данных). Можно использовать как одно, так и оба значения.</td></tr>
<tr>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">URI</td>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Задает универсальный код ресурса для выполняемого сценария.</td></tr>
<tr>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Параметры</td>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Параметры, необходимые для скрипта. В приведенном образце скрипта не требуется задавать параметры, поэтому этот параметр не указан в приведенном выше фрагменте кода.
</td></tr>
</table>

4. Наконец, приступите к созданию настроенного кластера с установленным Spark.

		# Start creating a cluster with Spark installed
		New-AzureHDInsightCluster -Config $config -Name $clusterName -Location $location -Version $version

При появлении запроса введите учетные данные для кластера. Создание кластера может занять несколько минут.

## Установка Spark с помощью PowerShell

Обратитесь к статье [Настройка кластеров HDInsight с помощью действия сценария](hdinsight-hadoop-customize-cluster.md#call_scripts_using_powershell).

## Установка Spark с помощью пакета SDK для .NET

Обратитесь к статье [Настройка кластеров HDInsight с помощью действия сценария](hdinsight-hadoop-customize-cluster.md#call_scripts_using_azure_powershell).


## См. также

- [Установка Spark в кластерах HDInsight под управлением Linux](hdinsight-hadoop-spark-install-linux.md). Установка Spark в кластерах HDInsight под управлением Linux.
- [Создание кластеров Hadoop в HDInsight](hdinsight-provision-clusters.md). Создание кластеров HDInsight.
- [Приступая к работе с Apache Spark в HDInsight](hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql.md). Начало работы с Spark в HDInsight.
- [Настройка кластера HDInsight с помощью действия сценария][hdinsight-cluster-customize]. Настройка кластеров HDInsight с помощью действия сценария.
- [Разработка скриптов действия сценария для HDInsight](hdinsight-hadoop-script-actions.md). Разработка скриптов действия сценария.
- Статья [Установка R в кластерах HDInsight][hdinsight-install-r] содержит указания по применению настройки кластера для установки и использования R в кластерах HDInsight Hadoop. R — это открытый язык программирования и свободная программная среда для статистических вычислений. Он предоставляет сотни встраиваемых статистических функций и собственный язык программирования, который сочетает аспекты функционального и объектно-ориентированного программирования. Этот проект также обеспечивает обширные графические возможности.
- [Установка Giraph в кластерах HDInsight](hdinsight-hadoop-giraph-install.md). Используйте настройки кластера для установки Giraph в кластерах HDInsight Hadoop. Giraph позволяет выполнять обработку графов с использованием Hadoop и может использоваться с Azure HDInsight.
- [Установка Solr в кластерах HDInsight](hdinsight-hadoop-solr-install.md). Используйте настройки кластера для установки Solr в кластерах HDInsight Hadoop. Solr позволяет вести расширенный поиск по хранимым данным.

[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-install-r]: hdinsight-hadoop-r-scripts.md
[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster.md
[powershell-install-configure]: ../install-configure-powershell.md

<!---HONumber=Oct15_HO4-->
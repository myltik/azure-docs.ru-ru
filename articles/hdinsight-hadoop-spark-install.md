<properties 
	pageTitle="Использование Script Action в HDInsight для установки Spark на кластере Hadoop| Azure" 
	description="Дополнительные сведения о настройке кластера HDInsight для установки Spark. Вы воспользуетесь параметром конфигурации действия скрипта (Script Action), чтобы использовать скрипт для установки Spark" 
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
	ms.date="12/19/2014" 
	ms.author="nitinme"/>

# Установка и использование Spark 1.0 на кластерах HDInsight Hadoop

Spark можно установить в любой тип кластера Hadoop в HDInsight с помощью настройки кластера **Действие скрипта**. Действие скрипта позволяет выполнять скрипты для настройки кластера только во время его создания. Дополнительные сведения см. в разделе [Настройка кластера HDInsight с помощью действия скрипта][hdinsight-cluster-customize].

В этом разделе описано, как установить версию Spark 1.0 с помощью Script Action. После установки Spark вы также узнаете, как осуществлять запрос Spark на кластерах HDInsight.


## Содержание

- [Что такое Spark?](#whatis)
- [Как установить Spark?](#install)
- [Использование Spark в HDInsight](#usespark)
- [Установка Spark в кластерах HDInsight Hadoop с помощью PowerShell](#usingPS)
- [Установка Spark на кластерах HDInsight Hadoop с помощью пакета SDK для .NET](#usingSDK) 


## <a name="whatis"></a>Что такое Spark?

<a href="http://spark.apache.org/docs/latest/index.html" target="_blank">Apache Spark</a> - это платформа параллельной обработки с открытым исходным кодом, которая поддерживает обработку в памяти, чтобы повысить производительность приложений для анализа больших объемов данных. Возможности вычисления в памяти Spark отлично подходят для итеративных алгоритмов в компьютерном обучении и графовых вычислениях.

Эта платформа также может использоваться для выполнения обычной обработки данных на диске. Spark превосходит традиционную платформу MapReduce, так как избегает операций записи на диск на промежуточных этапах. К тому же, Spark совместима с HDFS и WASB, поэтому существующие данные легко могут обрабатываться с помощью этой платформы. 

Этот раздел содержит инструкции по настройке кластера HDInsight для установки Spark.   

## <a name="install"></a>Как установить Spark?

Пример скрипта для установки Spark на кластере HDInsight доступен в BLOB-объекте хранилища Azure (доступ только для чтения): [https://hdiconfigactions.blob.core.windows.net/sparkconfigactionv02/spark-installer-v02.ps1](https://hdiconfigactions.blob.core.windows.net/sparkconfigactionv02/spark-installer-v02.ps1). Этот раздел содержит инструкции по использованию примера скрипта при подготовке кластера к работе с помощью портала управления Azure. 

> [AZURE.NOTE] Пример скрипта работает только с кластером HDInsight версии 3.1.  Дополнительные сведения о версиях кластера HDInsight см. в разделе [Версии кластера HDInsight](http://azure.microsoft.com/documentation/articles/hdinsight-component-versioning/).

1. Начните подготовку кластера к работе с помощью параметра **Настраиваемое создание**, как описано в разделе [Подготовка кластера с использованием пользовательских параметров](http://azure.microsoft.com/documentation/articles/hdinsight-provision-clusters/#portal). 
2. На странице **Действия скриптов** мастера нажмите кнопку **Добавить действие скрипта** для предоставления сведений о данном действии скрипта, как показано ниже:

	![Use Script Action to customize a cluster](./media/hdinsight-hadoop-customize-cluster/HDI.CustomProvision.Page6.png "Use Script Action to customize a cluster")
	
	<table border='1'>
		<tr><th>Свойство</th><th>Значение</th></tr>
		<tr><td>Имя</td>
			<td>Укажите имя для действия скрипта. Например, <b>Установить Spark</b>.</td></tr>
		<tr><td>URI скрипта</td>
			<td>Укажите URI для скрипта, вызываемого для настройки кластера. Например, <i>https://hdiconfigactions.blob.core.windows.net/sparkconfigactionv02/spark-installer-v02.ps1</i></td></tr>
		<tr><td>Тип узла</td>
			<td>Указывает узлы, на которых выполняется скрипт настройки. Вы можете <b>Все узлы</b>, <b>Только головные узлы</b> или <b>Только рабочие узлы</b>.
		<tr><td>Параметры</td>
			<td>Укажите необходимые для скрипта параметры. Скрипт для установки Spark  не требует никаких параметров, поэтому можно оставить это поле пустым.</td></tr>
	</table>	

	Можно добавить несколько действий скрипта для установки нескольких компонентов в кластере. После добавления скриптов щелкните значок с галочкой, чтобы начать подготовку кластера.

Скрипт также позволяет установить Spark на HDInsight с помощью PowerShell или пакета SDK HDInsight .NET. Инструкции по выполнению этих процедур приведены ниже в этом разделе.

## <a name="usespark"></a>Использование Spark в HDInsight
Spark предоставляет интерфейсы API в Scala, Python и Java. Также можно использовать интерактивную оболочку Spark для выполнения запросов Spark. Этот раздел содержит общие сведения о том, как использовать два вышеупомянутых подхода для работы с платформой Spark:

- [С помощью  оболочки Spark](#sparkshell)
- [С помощью отдельной программы Scala](#standalone)

###<a name="sparkshell"></a>С помощью оболочки Spark
Выполните следующие действия для запуска запросов Spark из интерактивной оболочки Spark. В этом разделе мы выполним запрос Spark для образца файла данных (/ example/data/gutenberg/davinci.txt), который по умолчанию присутствует в кластерах HDInsight.

1. На портале управления Azure запустите протокол удаленного рабочего стола для созданного кластера с установленной системой Spark, а затем подключитесь к кластеру. Инструкции см. в разделе <a href="http://azure.microsoft.com/documentation/articles/hdinsight-administer-use-management-portal/#rdp" target="_blank">Подключение к кластерам HDInsight с использованием RDP</a>.

2. В сеансе RDP с рабочего стола откройте командную строку Hadoop и перейдите к расположению, в котором установлена Spark, например, **C:\apps\dist\spark-1.0.2**.


3. Выполните следующую команду, чтобы активировать оболочку Spark.

		 .\bin\spark-shell --master yarn

	После завершения выполнения команды должна появиться строка запроса Scala.

		 scala>

5. Введите в эту строку запрос Spark, указанный ниже. Этот запрос подсчитывает встречаемость каждого слова в файле davinci.txt, который доступен в расположении /example/data/gutenberg/ связанного с кластером хранилища BLOB-объектов (WASB).

		val file = sc.textFile("/example/data/gutenberg/davinci.txt")
		val counts = file.flatMap(line => line.split(" ")).map(word => (word, 1)).reduceByKey(_ + _)
		counts.toArray().foreach(println)

6. Результат должен выглядеть так:

	![Output from running Scala interactive shell in an HDInsight cluster][img-hdi-scala-interactive]
		

7. Введите :q, чтобы выйти из строки Scala.

		:q

### <a name="standalone"></a>С помощью отдельной программы Scala

В этом разделе мы создаем приложение Scala, которое подсчитывает количество строк, содержащих буквы 'a' and 'b', в образце файла данных (/ example/data/gutenberg/davinci.txt), который по умолчанию присутствует в кластерах HDInsight. Для написания и использования отдельной программы Scala с кластером, настроенным при помощи установки Spark, необходимо выполнить следующие действия:

- Напишите программу Scala.
- Выполните построение программы, чтобы получить JAR-файл.
- Запустите задание на кластере.

#### Написание программы Scala
В этом разделе показано, как написать программу Scala, которая подсчитывает количество строк, содержащих  'a' and 'b' в образце файла данных. 

1. Откройте текстовый редактор и вставьте следующий код:


		/* SimpleApp.scala */
		import org.apache.spark.SparkContext
		import org.apache.spark.SparkContext._
		import org.apache.spark.SparkConf
		
		object SimpleApp {
		  def main(args: Array[String]) {
		    val logFile = "/example/data/gutenberg/davinci.txt"			//location of sample data file on WASB
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
В этом разделе описано, как можно использовать <a href="http://www.scala-sbt.org/0.13/docs/index.html" target="_blank">простой инструмент построения</a> (Simple Build Tool, или SBT) для построения программы scala. Для работы SBT требуется Java 1.6 или более поздней версии, поэтому, перед тем как продолжить, убедитесь, что у вас установлена соответствующая версия Java.

1. Установите SBT из страницы http://www.scala-sbt.org/0.13/tutorial/Installing-sbt-on-Windows.html.
2. Создайте папку **SimpleScalaApp** и в этой папке - файл с именем **simple.sbt**. Это файл настройки, содержащий сведения о версии Scala, зависимостях библиотеки и т. д. Вставьте следующую информацию в файл simple.sbt и сохраните его.


		name := "SimpleApp"
	
		version := "1.0"
	
		scalaVersion := "2.10.4"
	
		libraryDependencies += "org.apache.spark" %% "spark-core" % "1.0.2"



	>[AZURE.NOTE] Убедитесь, что в файле сохранились пустые строки.

	
3. В папке **SimpleScalaApp** создайте структуру каталогов **\src\main\scala** и вставьте программу Scala (**SimpleApp.scala**), созданную ранее в папке \src\main\scala.
4. Откройте командную строку, перейдите в каталог SimpleScalaApp и введите следующую команду:


		sbt package


	После компиляции приложения появится файл **simpleapp_2.10-1.0.jar**, созданный в каталоге **\target\scala-2.10** корневой папки SimpleScalaApp.


#### Запуск задания на кластере
В этом разделе содержатся инструкции по удаленному подключению к кластеру, установленному с помощью Spark, и последующему копированию конечной папки проекта SimpleScalaApp. Далее последует информация об использовании команды **spark-submit** для отправки задания на кластер.

1. Установите удаленное подключение к кластеру с установленной Spark. С компьютера, который использовался для написания и построения программы SimpleApp.scala, скопируйте папку **SimpleScalaApp\target** и вставьте в расположение на кластере.
2. В сеансе RDP с рабочего стола откройте командную строку Hadoop и перейдите к расположению, в которое была скопирована папка **target**.
3. Введите следующую команду для запуска программы SimpleApp.scala:


		C:\apps\dist\spark-1.0.2\bin\spark-submit --class "SimpleApp" --master local target/scala-2.10/simpleapp_2.10-1.0.jar

4. После завершения работы программы вывод отображается на консоли.


		Lines with a: 21374, Lines with b: 11430

## <a name="usingPS"></a>Установка Spark в кластерах HDInsight Hadoop с помощью PowerShell

В этом разделе мы используем командлет **<a href = "http://msdn.microsoft.com/library/dn858088.aspx" target="_blank">Add-AzureHDInsightScriptAction</a>**, чтобы вызвать скрипты с помощью действия скрипта для настройки кластера. Прежде чем продолжить, убедитесь, что вы установили и настроили PowerShell. Сведения о настройке рабочей станции для запуска командлетов HDInsight Powershell см. в разделе [Установка и настройка Azure PowerShell][powershell-install-configure].

Выполните следующие действия:

1. Откройте окно Azure PowerShell и объявите следующие переменные:

		# PROVIDE VALUES FOR THESE VARIABLES
		$subscriptionName = "<SubscriptionName>"		# Name of the Azure subscription
		$clusterName = "<HDInsightClusterName>"			# The HDInsight cluster name
		$storageAccountName = "<StorageAccountName>"	# Azure storage account that hosts the default container
		$storageAccountKey = "<StorageAccountKey>"      # Key for the storage account
		$containerName = $clusterName
		$location = "<MicrosoftDataCenter>"				# The location of the HDInsight cluster. It must in the same data center as the storage account.
		$clusterNodes = <ClusterSizeInNumbers>			# The number of nodes in the HDInsight cluster.
		$version = "<HDInsightClusterVersion>"          # For example "3.1"
	
2. Укажите такие значения настройки, как узлы в кластере и хранилище для использования по умолчанию.

		# SPECIFY THE CONFIGURATION OPTIONS
		Select-AzureSubscription $subscriptionName
		$config = New-AzureHDInsightClusterConfig -ClusterSizeInNodes $clusterNodes
		$config.DefaultStorageAccount.StorageAccountName="$storageAccountName.blob.core.windows.net"
		$config.DefaultStorageAccount.StorageAccountKey=$storageAccountKey
		$config.DefaultStorageAccount.StorageContainerName=$containerName
	
3. Используйте командлет **Add-AzureHDInsightScriptAction**, чтобы добавить Script Action в настройки кластера. Позже, а именно при создании кластера, Script Action начнет выполняться. 

		# ADD SCRIPT ACTION TO CLUSTER CONFIGURATION
		$config = Add-AzureHDInsightScriptAction -Config $config -Name "Install Spark" -ClusterRoleCollection HeadNode -Uri https://hdiconfigactions.blob.core.windows.net/sparkconfigactionv02/spark-installer-v02.ps1

	Командлет **Add-AzureHDInsightScriptAction** принимает следующие параметры:

	<table style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse;">
	<tr>
	<th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:90px; padding-left:5px; padding-right:5px;">Параметр</th>
	<th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:550px; padding-left:5px; padding-right:5px;">Определение</th></tr>
	<tr>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Конфигурация</td>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px; padding-right:5px;">Объект конфигурации, к которому добавляется информация о действии скрипта</td></tr>
	<tr>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Имя</td>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Имя действия скрипта</td></tr>
	<tr>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">ClusterRoleCollection</td>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Указывает узлы, на которых выполняется скрипт настройки. Допустимые значения: HeadNode (для установки на головном узле) или DataNode (для установки на всех узлах данных). Можно использовать как одно, так и оба значения.</td></tr>
	<tr>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">URI</td>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Задает URI для выполняемого скрипта</td></tr>
	<tr>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Параметры</td>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Параметры, необходимые для скрипта. В приведенном образце скрипта не требуется задавать параметры, поэтому этот параметр не указан в приведенном выше фрагменте кода.
	</td></tr>
	</table>
	
4. Наконец, начните подготовку настроенного кластера с установленной Spark.  
	
		# START PROVISIONING A CLUSTER WITH SPARK INSTALLED
		New-AzureHDInsightCluster -Config $config -Name $clusterName -Location $location -Version $version 

При появлении запроса введите учетные данные для кластера. Создание кластера может занять несколько минут.


## <a name="usingSDK"></a>Установите Spark на кластерах HDInsight Hadoop с помощью пакета SDK для .NET

Пакет SDK для HDInsight .NET предоставляет клиентские библиотеки .NET, которые упрощают работу с кластерами HDInsight из приложения .NET. Этот раздел содержит инструкции по использованию Script Action из пакета SDK для подготовки кластера с установленной Spark. Необходимо выполнить следующие процедуры:

- Установка пакета SDK для HDInsight .NET
- Создание самозаверяющего сертификата
- Создание консольного приложения
- Выполнение приложения


**Установка пакета SDK для HDInsight .NET**

Последнюю опубликованную сборку пакета SDK можно установить с сайта [NuGet](http://nuget.codeplex.com/wikipage?title=Getting%20Started). Инструкции будут показаны в следующей процедуре.

**Создание самозаверяющего сертификата**

Создание самозаверяющего сертификата, установка его на рабочую станцию и загрузка на вашу подписку Azure. Инструкции см. в разделе [Создание самозаверяющего сертификата](http://go.microsoft.com/fwlink/?LinkId=511138). 


**Создание приложения Visual Studio**

1. Откройте Visual Studio 2013.

2. В меню "Файл" щелкните **Создать**, затем щелкните **Проект**.

3. В окне "Новый проект" введите или выберите следующие значения:
	
	<table style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse;">
	<tr>
	<th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:90px; padding-left:5px; padding-right:5px;">Свойство</th>
	<th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:90px; padding-left:5px; padding-right:5px;">Значение</th></tr>
	<tr>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Категория</td>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px; padding-right:5px;">Templates/Visual C#/Windows</td></tr>
	<tr>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Шаблон</td>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Консоль Приложение</td></tr>
	<tr>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Имя</td>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">CreateSparkCluster</td></tr>
	</table>

4. Нажмите кнопку **ОК**, чтобы создать проект.

5. В меню **Инструменты** нажмите **Диспетчер пакетов Nuget**, а затем - **Консоль диспетчера пакетов**.

6. Для установки пакета выполните следующие команды в консоли:

		Install-Package Microsoft.WindowsAzure.Management.HDInsight

	Эта команда добавляет библиотеки .NET и ссылки на них в текущий проект Visual Studio.

7. Двойным щелчком откройте **Program.cs** в обозревателе решений.

8. Добавьте в начало файла следующие инструкции:

		using System.Security.Cryptography.X509Certificates;
		using Microsoft.WindowsAzure.Management.HDInsight;
		using Microsoft.WindowsAzure.Management.HDInsight.ClusterProvisioning;
		using Microsoft.WindowsAzure.Management.HDInsight.Framework.Logging;
	
9. В функции Main() скопируйте и вставьте следующий код и задайте значения для переменных:
		
        var clusterName = args[0];

        // PROVIDE VALUES FOR THE VARIABLES
        string thumbprint = "<CertificateThumbprint>";  
        string subscriptionId = "<AzureSubscriptionID>";
        string location = "<MicrosoftDataCenterLocation>";
        string storageaccountname = "<AzureStorageAccountName>.blob.core.windows.net";
        string storageaccountkey = "<AzureStorageAccountKey>";
        string username = "<HDInsightUsername>";
        string password = "<HDInsightUserPassword>";
        int clustersize = <NumberOfNodesInTheCluster>;

        // PROVIDE THE CERTIFICATE THUMBPRINT TO RETRIEVE THE CERTIFICATE FROM THE CERTIFICATE STORE 
        X509Store store = new X509Store();
        store.Open(OpenFlags.ReadOnly);
        X509Certificate2 cert = store.Certificates.Cast<X509Certificate2>().First(item => item.Thumbprint == thumbprint);

        // CREATE AN HDINSIGHT CLIENT OBJECT
        HDInsightCertificateCredential creds = new HDInsightCertificateCredential(new Guid(subscriptionId), cert);
        var client = HDInsightClient.Connect(creds);
		client.IgnoreSslErrors = true;
        
        // PROVIDE THE CLUSTER INFORMATION
		var clusterInfo = new ClusterCreateParameters()
        {
            Name = clusterName,
            Location = location,
            DefaultStorageAccountName = storageaccountname,
            DefaultStorageAccountKey = storageaccountkey,
            DefaultStorageContainer = clusterName,
            UserName = username,
            Password = password,
            ClusterSizeInNodes = clustersize,
            Version = "3.1"
        };        

10. Добавьте следующий код в функцию Main(), чтобы использовать класс [ScriptAction](http://msdn.microsoft.com/library/microsoft.windowsazure.management.hdinsight.clusterprovisioning.data.scriptaction.aspx) для вызова пользовательского скрипта с целью установки Spark.

		// ADD THE SCRIPT ACTION TO INSTALL SPARK
        clusterInfo.ConfigActions.Add(new ScriptAction(
          "Install Spark", // Name of the config action
          new ClusterNodeType[] { ClusterNodeType.HeadNode }, // List of nodes to install Spark on
          new Uri("https://hdiconfigactions.blob.core.windows.net/sparkconfigactionv02/spark-installer-v02.ps1"), // Location of the script to install Spark
		  null //because the script used does not require any parameters.
        ));

11. Наконец, создайте кластер.

		client.CreateCluster(clusterInfo);

11. Сохраните изменения в приложение и выполните построение решения. 

**Запуск приложения**

Откройте консоль PowerShell, перейдите к расположению, где был сохранен проект Visual Studio, затем перейдите в каталог \bin\debug в проекте, после чего выполните следующую команду:

	.\CreateSparkCluster <cluster-name>

Укажите имя кластера и нажмите клавишу ВВОД, чтобы подготовить кластер с установленным Spark.


## Дополнительные материалы##
- [Установка R на кластерах HDInsight][hdinsight-install-r] - инструкции по применению настройки кластера для установки и использования R на кластерах HDInsight Hadoop. Проект R - это открытый язык программирования и свободная программная среда для статистической обработки данных. Он предоставляет сотни встраиваемых статистических функций и собственный язык программирования, который сочетает аспекты функционального и объектно-ориентированного программирования. Этот проект также обеспечивает обширные графические возможности.
- [Установка Giraph в кластерах HDInsight](hdinsight-hadoop-giraph-install.md). Используйте настройки кластера для установки Giraph в кластерах HDInsight Hadoop. Giraph позволяет выполнять обработку графов с использованием Hadoop, а также может использоваться с Azure HDInsight.
- [Установка Solr в кластерах HDInsight](hdinsight-hadoop-solr-install.md). Используйте настройки кластера для установки Solr в кластерах HDInsight Hadoop. Solr позволяет вести расширенный поиск по хранимым данным.





[hdinsight-provision]: ../hdinsight-provision-clusters/
[hdinsight-install-r]: ../hdinsight-hadoop-r-scripts/
[hdinsight-cluster-customize]: ../hdinsight-hadoop-customize-cluster
[powershell-install-configure]: ../install-configure-powershell/


<!--HONumber=42-->

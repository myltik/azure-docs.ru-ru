<properties 
	pageTitle="Использование Script Action в HDInsight для установки Giraph на кластере Hadoop| Azure" 
	description="Дополнительные сведения о настройке кластера HDInsight для установки Giraph. Вы воспользуетесь параметром конфигурации действия скрипта (Script Action), чтобы использовать скрипт для установки Giraph" 
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

# Установка и использование Giraph на кластерах HDInsight Hadoop

Giraph можно установить в любой тип кластера Hadoop в HDInsight с помощью настройки кластера **Действие скрипта**. Действие скрипта позволяет выполнять скрипты для настройки кластера только во время его создания. Дополнительные сведения см. в разделе [Настройка кластера HDInsight с помощью действия скрипта][hdinsight-cluster-customize].

В этом разделе описано, как установить Giraph с помощью действия скрипта. После установки Giraph вы также научитесь использовать Giraph для наиболее типичных приложений для обработки крупномасштабных графов.


## Содержание

- [Что такое Giraph?](#whatis)
- [Как установить Giraph?](#install)
- [Использование Giraph в HDInsight](#usegiraph)
- [Установка Giraph в кластерах HDInsight Hadoop с помощью PowerShell](#usingPS)
- [Установка Giraph на кластерах HDInsight Hadoop с помощью пакета SDK для .NET](#usingSDK) 


## <a name="whatis"></a>Что такое Giraph?

<a href="http://giraph.apache.org/" target="_blank">Apache Giraph</a> позволяет выполнять обработку графов с использованием Hadoop, а также может использоваться с Azure HDInsight. Графы моделируют взаимоотношения между объектами, таки как подключения между маршрутизаторами в большой сети, подобной Интернету, или взаимоотношения между людьми в социальных сетях (иногда называется социальным графом.) Обработка графов дает возможность делать выводы о взаимоотношениях объектов в графе, таких как:

- определение потенциальных друзей на основе текущих взаимоотношений
- определение кратчайшего маршрута между двумя компьютерами в сети
- вычисление ранга страницы веб-страниц

   
## <a name="install"></a>Как установить Giraph?

Пример скрипта для установки Giraph на кластере HDInsight доступен в BLOB-объекте хранилища Azure (доступ только для чтения): [https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1](https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1). Этот раздел содержит инструкции по использованию примера скрипта при подготовке кластера к работе с помощью портала управления Azure. 

> [AZURE.NOTE] Образец скрипта работает только с кластером HDInsight версии 3.1.  Дополнительные сведения о версиях кластера HDInsight см. в разделе [Версии кластера HDInsight](http://azure.microsoft.com/ru-ru/documentation/articles/hdinsight-component-versioning/).

1. Начните подготовку кластера к работе с помощью параметра **Настраиваемое создание**, как описано в разделе [Подготовка кластера с использованием пользовательских параметров](http://azure.microsoft.com/ru-ru/documentation/articles/hdinsight-provision-clusters/#portal). 
2. На странице **Действия скриптов** мастера нажмите кнопку **Добавить действие скрипта** для предоставления сведений о данном действии скрипта, как показано ниже:

	![Use Script Action to customize a cluster](./media/hdinsight-hadoop-giraph-install/hdi-script-action-giraph.png "Use Script Action to customize a cluster")
	
	<table border='1'>
		<tr><th>Свойство</th><th>Значение</th></tr>
		<tr><td>Имя</td>
			<td>Укажите имя для действия скрипта. Например, <b>Установить Giraph</b>.</td></tr>
		<tr><td>URI скрипта</td>
			<td>Укажите URI для скрипта, вызываемого для настройки кластера. Например: <i>https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1</i></td></tr>
		<tr><td>Тип узла</td>
			<td>Указывает узлы, на которых выполняется скрипт настройки. Вы можете <b>Все узлы</b>, <b>Только головные узлы</b> или <b>Только рабочие узлы</b>.
		<tr><td>Параметры</td>
			<td>Укажите необходимые для скрипта параметры. Скрипт для установки Giraph не требует никаких параметров, поэтому можно оставить это поле пустым.</td></tr>
	</table>	

	Можно добавить несколько действий скрипта для установки нескольких компонентов в кластере. После добавления скриптов щелкните значок с галочкой, чтобы начать подготовку кластера.

Скрипт также позволяет установить Giraph на HDInsight с помощью PowerShell или пакета SDK HDInsight .NET. Инструкции по выполнению этих процедур приведены ниже в этом разделе.

## <a name="usegiraph"></a>Использование Giraph в HDInsight

Мы используем SimpleShortestPathsComputation, чтобы продемонстрировать базовую реализацию <a href = "http://people.apache.org/~edwardyoon/documents/pregel.pdf">Pregel</a> для поиска кратчайшего пути между объектами в графе. Выполните следующие действия, чтобы отправить данные и JAR-файл примера, запустить задание с использованием примера SimpleShortestPathsComputation, а затем просмотреть результаты.

1. **Отправьте пример файла данных в WASB**. На локальной рабочей станции, создайте новый файл с именем **tiny_graph.txt**. В нем должны присутствовать следующие строки.

		[0,0,[[1,1],[3,3]]]
		[1,0,[[0,1],[2,2],[3,1]]]
		[2,0,[[1,2],[4,4]]]
		[3,0,[[0,3],[1,1],[4,4]]]
		[4,0,[[3,4],[2,4]]]

	Отправьте файл tiny_graph.txt в основное хранилище для кластера HDInsight. Инструкции по отправке данных см. в разделе [Отправка данных для заданий Hadoop в HDInsight](../hdinsight-upload-data/)

	Эти данные описывают взаимоотношения между объектами в направленном графе с использованием формата [source\_id, source\_value,[[dest\_id], [edge\_value],...]]. Каждая строка представляет взаимоотношение между объектом **source\_id** и одним или несколькими объектами **dest\_id**. Значение **edge\_value** (или вес) можно представить себе как силу или расстояние соединения между **source_id** и **dest\_id**.

	В начерченном виде и с использованием значения (или веса) в качестве расстояния между объектами указанные выше данные могут выглядеть следующим образом.

	![tiny_graph.txt drawn as circles with lines of varying distance between](.\media\hdinsight-hadoop-giraph-install\giraph-graph.png)

	

4. Запустите пример **SimpleShortstPathsComputation**. Воспользуйтесь следующими командлетами PowerShell для запуска этого примера, используя файл tiny_graph.txt в качестве входных данных. Для выполнения необходимо установить и настроить [Azure PowerShell][powershell-install-configure].

		$clusterName = "clustername"
		# Giraph examples JAR
		$jarFile = "wasb:///example/jars/giraph-examples.jar"
		# Arguments for this job
		$jobArguments = "org.apache.giraph.examples.SimpleShortestPathsComputation",
		                "-ca", "mapred.job.tracker=headnodehost:9010",
		                "-vif", "org.apache.giraph.io.formats.JsonLongDoubleFloatDoubleVertexInputFormat",
		                "-vip", "wasb:///example/data/tiny_graph.txt",
		                "-vof", "org.apache.giraph.io.formats.IdWithValueTextOutputFormat",
		                "-op",  "wasb:///example/output/shortestpaths",
		                "-w", "2"
		# Create the definition
		$jobDefinition = New-AzureHDInsightMapReduceJobDefinition
		  -JarFile $jarFile
		  -ClassName "org.apache.giraph.GiraphRunner"
		  -Arguments $jobArguments
		
		# Run the job, write output to the PowerShell window
		$job = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $jobDefinition
		Write-Host "Wait for the job to complete ..." -ForegroundColor Green
		Wait-AzureHDInsightJob -Job $job
		Write-Host "STDERR"
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $job.JobId -StandardError
		Write-Host "Display the standard output ..." -ForegroundColor Green
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $job.JobId -StandardOutput

	В приведенном выше примере замените **clustername** именем кластера HDInsight, где установлен Giraph.

5. **Просмотрите результаты**.  После завершения задания результаты будут храниться в двух выходных файлах в папке __wasb:///example/out/shotestpaths__. Эти файлы называются __part-m-00001__ и __part-m-00002__. Выполните следующие действия, чтобы скачать и просмотреть выходные данные.

		$subscriptionName = "<SubscriptionName>"       # Azure subscription name
		$storageAccountName = "<StorageAccountName>"   # Azure storage account name
		$containerName = "<ContainerName>"             # Blob storage container name

		# Select the current subscription
		Select-AzureSubscription $subscriptionName
		
		# Create the storage account context object
		$storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
		$storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey

		# Download the job output to the workstation
		Get-AzureStorageBlobContent -Container $containerName -Blob example/output/shortestpaths/part-m-00001 -Context $storageContext -Force
		Get-AzureStorageBlobContent -Container $containerName -Blob example/output/shortestpaths/part-m-00002 -Context $storageContext -Force

	В результате создается структура каталога __example/output/shortestpaths__ в текущем каталоге, а выходные файлы скачиваются в это расположение.

	Используйте командлет __Cat__, чтобы отобразить содержимое файлов: 

		Cat example/output/shortestpaths/part*

	Результат должен выглядеть аналогично следующему:


		0	1.0
		4	5.0
		2	2.0
		1	0.0
		3	1.0

	Пример SimpleShortestPathComputation жестко запрограммирован для запуска с объекта ID 1 и поиска кратчайшего пути к другим объектам. Таким образом, выходные данные должны считываться как `destination_id distance`, где distance (расстояние) представляет собой значение (или вес) переходов на пути между объектом ID 1 и целевым объектом ID.
	
	При визуализации можно проверить результаты, проходя кратчайшие пути между ID 1 и всеми другими объектами. Обратите внимание, что кратчайший путь между ID 1 и ID 4 - это 5. Это общее расстояние между <span style="color:orange">ID 1 и 3</span>, а затем <span style="color:red">ID 3 и 4</span>.

	![Drawing of objects as circles with shortest paths drawn between](.\media\hdinsight-hadoop-giraph-install\giraph-graph-out.png) 


## <a name="usingPS"></a>Установка Giraph в кластерах HDInsight Hadoop с помощью PowerShell

В этом разделе мы используем командлет **<a href = "http://msdn.microsoft.com/ru-ru/library/dn858088.aspx" target="_blank">Add-AzureHDInsightScriptAction</a>**, чтобы вызвать скрипты с помощью действия скрипта для настройки кластера. Прежде чем продолжить, убедитесь, что вы установили и настроили PowerShell. Сведения о настройке рабочей станции для запуска командлетов HDInsight Powershell см. в разделе [Установка и настройка Azure PowerShell][powershell-install-configure].

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
		$config = Add-AzureHDInsightScriptAction -Config $config -Name "Install Giraph" -ClusterRoleCollection HeadNode -Uri https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1

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
	
4. Наконец, начните подготовку настроенного кластера с установленной системой Giraph.  
	
		# START PROVISIONING A CLUSTER WITH GIRAPH INSTALLED
		New-AzureHDInsightCluster -Config $config -Name $clusterName -Location $location -Version $version 

При появлении запроса введите учетные данные для кластера. Создание кластера может занять несколько минут.


## <a name="usingSDK"></a>Установка Giraph на кластерах HDInsight Hadoop с помощью пакета SDK для .NET

Пакет SDK для HDInsight .NET предоставляет клиентские библиотеки .NET, которые упрощают работу с кластерами HDInsight из приложения .NET. Этот раздел содержит инструкции по использованию действия скрипта из пакета SDK для подготовки кластера с установленной Giraph. Необходимо выполнить следующие процедуры:

- Установка пакета SDK для HDInsight .NET
- Создание самозаверяющего сертификата
- Создание консольного приложения
- Выполнение приложения


**Установка пакета SDK для HDInsight .NET**

Последнюю опубликованную сборку пакета SDK можно установить с сайта [NuGet](http://nuget.codeplex.0com/wikipage?title=Getting%20Started). Инструкции будут показаны в следующей процедуре.

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
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Консольное приложение</td></tr>
	<tr>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Имя</td>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">CreateGiraphCluster</td></tr>
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

10. Добавьте следующий код в функцию Main(), чтобы использовать класс [ScriptAction](http://msdn.microsoft.com/ru-ru/library/microsoft.windowsazure.management.hdinsight.clusterprovisioning.data.scriptaction.aspx) для вызова пользовательского скрипта с целью установки Giraph.

		// ADD THE SCRIPT ACTION TO INSTALL GIRAPH
        clusterInfo.ConfigActions.Add(new ScriptAction(
          "Install Giraph", // Name of the config action
          new ClusterNodeType[] { ClusterNodeType.HeadNode }, // List of nodes to install Giraph on
          new Uri("https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1"), // Location of the script to install Giraph
		  null //because the script used does not require any parameters.
        ));

11. Наконец, создайте кластер.

		client.CreateCluster(clusterInfo);

11. Сохраните изменения в приложение и выполните построение решения. 

**Запуск приложения**

Откройте консоль PowerShell, перейдите к расположению, где был сохранен проект Visual Studio, затем перейдите в каталог \bin\debug в проекте, после чего выполните следующую команду:

	.\CreateGiraphCluster <cluster-name>

Укажите имя кластера и нажмите клавишу ВВОД, чтобы подготовить кластер с установленной системой Giraph.


## Дополнительные материалы##
- [Установка и использование Spark на кластерах HDInsight][hdinsight-install-spark] - инструкции по применению настройки кластера для установки и использования Spark на кластерах HDInsight Hadoop. Spark - это платформа параллельной обработки с открытым исходным кодом, которая поддерживает обработку в памяти, чтобы повысить производительность приложений для анализа больших объемов данных.
- [Установка R на кластерах HDInsight][hdinsight-install-r] - инструкции по применению настройки кластера для установки и использования R на кластерах HDInsight Hadoop. Проект R - это открытый язык программирования и свободная программная среда для статистической обработки данных. Он предоставляет сотни встраиваемых статистических функций и собственный язык программирования, который сочетает аспекты функционального и объектно-ориентированного программирования. Этот проект также обеспечивает обширные графические возможности.
- [Установка Solr в кластерах HDInsight](../hdinsight-hadoop-solr-install). Используйте настройки кластера для установки Solr в кластерах HDInsight Hadoop. Solr позволяет вести расширенный поиск по хранимым данным.



[tools]: https://github.com/Blackmist/hdinsight-tools
[aps]: http://azure.microsoft.com/ru-ru/documentation/articles/install-configure-powershell/

[hdinsight-provision]: ../hdinsight-provision-clusters/
[hdinsight-install-r]: ../hdinsight-hadoop-r-scripts/
[hdinsight-install-spark]: ../hdinsight-hadoop-spark-install/
[hdinsight-cluster-customize]: ../hdinsight-hadoop-customize-cluster<!--HONumber=42-->

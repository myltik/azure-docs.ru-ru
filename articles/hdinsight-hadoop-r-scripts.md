<properties 
	pageTitle="Использование R в HDInsight для настройки кластеров| Azure" 
	description="Узнайте, как установить и использовать R для настройки кластеров Hadoop." 
	services="hdinsight" 
	documentationCenter="" 
	authors="bradsev" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/19/2014" 
	ms.author="bradsev"/>

# Установка и использование R на кластерах HDInsight Hadoop

R можно установить в любой тип кластера Hadoop в HDInsight с помощью настройки кластера **Действие скрипта**. Благодаря R ученые и аналитики имеют возможность задействовать мощную платформу программирования MapReduce/YARN для обработки больших объемов данных, которые находятся в кластерах Hadoop, развернутых в HDInsight.

Действие скрипта позволяет выполнять скрипты для настройки кластера только во время его создания. Дополнительные сведения см. в разделе [Настройка кластера HDInsight с помощью действия скрипта][hdinsight-cluster-customize].


## Содержание

- [Что такое R](#whatIs)
- [Как установить R?](#install)
- [Запуск скриптов R в HDInsight](#useR) 
- [Установка R в кластерах HDInsight Hadoop с помощью PowerShell](#usingPS)
- [Установка R на кластерах HDInsight Hadoop с помощью пакета SDK для .NET](#usingSDK)
- [Дополнительные материалы](#seeAlso)


## <a name="whatIs"></a>Что такое R?

Пример: <a href="http://www.r-project.org/" target="_blank">Проект R -</a> это открытый язык программирования и свободная программная среда для статистической обработки данных. R предоставляет сотни встраиваемых статистических функций и собственный язык программирования, который сочетает аспекты функционального и объектно-ориентированного программирования. Этот проект также обеспечивает обширные графические возможности. Большинство профессиональных статистиков и ученых, работающих в целом ряде областей, отдает предпочтение программной среде R. 

В службе HDInsight скрипты R могут выполняться на кластерах Hadoop, которые во время создания были настроены с помощью параметра действия скриптов для установки среды R. R совместим с хранилищем больших двоичных объектов Azure (WASB). Это дает возможность обрабатывать находящиеся там данные, используя R в HDInsight.  

## <a name="install"></a>Как установить R?

Пример скрипта для установки R на кластере HDInsight доступен в BLOB-объекте хранилища Azure (доступ только для чтения): [https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1](https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1). Этот раздел содержит инструкции по использованию примера скрипта при подготовке кластера к работе с помощью портала управления Azure.

> [AZURE.NOTE] Образец скрипта работает только с кластером HDInsight версии 3.1. Дополнительные сведения о версиях кластера HDInsight см. в разделе [Версии кластера HDInsight](http://azure.microsoft.com/documentation/articles/hdinsight-component-versioning/).

1. Начните подготовку кластера к работе с помощью параметра **Настраиваемое создание**, как описано в разделе [Подготовка кластера с использованием пользовательских параметров](http://azure.microsoft.com/documentation/articles/hdinsight-provision-clusters/#portal). 
2. На странице **Действия скриптов** мастера нажмите кнопку **Добавить действие скрипта** для предоставления сведений о данном действии скрипта, как показано ниже:

	![Use Script Action to customize a cluster](./media/hdinsight-hadoop-r-scripts/hdi-r-script-action.png "Use Script Action to customize a cluster")
	
	<table border='1'>
		<tr><th>Свойство</th><th>Значение</th></tr>
		<tr><td>Имя</td>
			<td>Укажите имя для действия скрипта. Например, <b>Установить R</b>.</td></tr>
		<tr><td>URI скрипта</td>
			<td>Укажите URI для скрипта, вызываемого для настройки кластера. Например: <i>https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1</i></td></tr>
		<tr><td>Тип узла</td>
			<td>Указывает узлы, на которых выполняется скрипт настройки. Вы можете <b>Все узлы</b>, <b>Только головные узлы</b> или <b>Только рабочие узлы</b>.
		<tr><td>Параметры</td>
			<td>Укажите необходимые для скрипта параметры. Скрипт для установки R не требует никаких параметров, поэтому можно оставить это поле пустым.</td></tr>
	</table>	

	Можно добавить несколько действий скрипта для установки нескольких компонентов в кластере. После добавления скриптов щелкните значок с галочкой, чтобы начать подготовку кластера.

Скрипт также позволяет установить R на HDInsight с помощью PowerShell или пакета SDK HDInsight .NET. Инструкции по выполнению этих процедур приведены ниже в этом разделе.

## <a name="useR"></a>Выполнение скриптов R в HDInsight
В этом разделе содержится описание действий для выполнения скрипта R в кластере Hadoop на базе HDInsight.

1. **Установите соединение между удаленным рабочим столом и кластером**: На портале управления Azure запустите протокол удаленного рабочего стола для созданного кластера с установленным R, а затем подключитесь к кластеру. Инструкции см. в разделе <a href="http://azure.microsoft.com/documentation/articles/hdinsight-administer-use-management-portal/#rdp" target="_blank">Подключение к кластерам HDInsight с использованием RDP</a>.

2. **Откройте консоль R**: Установочный файл R размещает на рабочем столе головного узла ссылку на консоль R. Щелкните ее, чтобы открыть консоль R.

3. **Запустите скрипт R**: Скрипт R возможно запустить непосредственно из консоли R. Для этого необходимо вставить его в консоль, выбрать его и нажать клавишу **ВВОД**. Ниже приведен пример простого скрипта, который генерирует числа от 1 до 100 и умножает их на 2.

		library(rmr2)
		library(rhdfs)
		ints = to.dfs(1:100)
		calc = mapreduce(input = ints, map = function(k, v) cbind(v, 2*v))
		from.dfs(calc)

Первые две строки вызывают библиотеки RHadoop, установленные с R. Последняя строка выводит результаты на консоль. Результат должен выглядеть так:

	[1,]  1 2
	[2,]  2 4
	.
	.
	.
	[98,]  98 196
	[99,]  99 198
	[100,] 100 200

## <a name="usingPS"></a>Установка R в кластерах HDInsight Hadoop с помощью PowerShell

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
	
3. Используйте командлет **Add-AzureHDInsightScriptAction** для запуска примера скрипта и установки R. 

		# INVOKE THE SCRIPT USING THE SCRIPT ACTION
		$config = Add-AzureHDInsightScriptAction -Config $config -Name "Install R"  -ClusterRoleCollection HeadNode,DataNode -Uri https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1


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
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Параметры</td>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Параметры, необходимые для скрипта. 
	</td></tr>
	<tr>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">URI</td>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Задает URI для выполняемого скрипта</td></tr>
	</table>
	
4. Наконец, подготовьте настроенный кластер к установке R.  
	
		# PROVISION A CLUSTER WITH R INSTALLED
		New-AzureHDInsightCluster -Config $config -Name $clusterName -Location $location -Version $version 

При появлении запроса введите учетные данные для кластера. Создание кластера может занять несколько минут.


## <a name="usingSDK"></a>Установка R в HDInsight с помощью пакета SDK для .NET

Пакет SDK для HDInsight .NET предоставляет клиентские библиотеки .NET, которые упрощают работу с кластерами HDInsight из приложения .NET. 

Для подготовки кластера HDInsight с использованием пакета SDK необходимы следующие процедуры:

- [Установка пакета SDK .NET для HDInsight](#installSDK)
- [Создание самозаверяющего сертификата](#createCert)
- [Создание приложения .NET в Visual Studio](#createApp)
- [Запуск приложения](#runApp)

В последующих разделах приведены инструкции по выполнению этих процедур.

### <a name="installSDK"></a>Установка пакета SDK для HDInsight .NET

Последнюю опубликованную сборку пакета SDK можно установить с сайта [NuGet](http://nuget.codeplex.com/wikipage?title=Getting%20Started). Инструкции будут показаны в следующей процедуре.

### <a name="createCert"></a>Создание самозаверяющего сертификата

Создание самозаверяющего сертификата, установка его на рабочую станцию и загрузка на вашу подписку Azure. Инструкции см. в разделе [Создание самозаверяющего сертификата](http://go.microsoft.com/fwlink/?LinkId=511138). 


### <a name="createApp"></a>Создание приложения .NET в Visual Studio

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
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">CreateRCluster</td></tr>
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

		// ADD THE SCRIPT ACTION TO INSTALL R

        clusterInfo.ConfigActions.Add(new ScriptAction(
            "Install R",
            new ClusterNodeType[] { ClusterNodeType.HeadNode, ClusterNodeType.DataNode },
            new Uri("https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1"), null
            ));

11. Наконец, создайте кластер.

		client.CreateCluster(clusterInfo);

11. Сохраните изменения в приложение и выполните построение решения. 

### <a name="runApp"></a>Выполнение приложения

Откройте консоль PowerShell, перейдите к расположению, где был сохранен проект, затем перейдите в каталог \bin\debug в проекте, после чего выполните следующую команду:

	.\CreateRCluster <cluster-name>

Укажите имя кластера и нажмите клавишу ВВОД, чтобы подготовить кластер с установленным R.

## <a name="seeAlso"></a>Дополнительные материалы

- [Установка и использование Spark на кластерах HDInsight][hdinsight-install-spark] - инструкции по применению настройки кластера для установки и использования Spark на кластерах HDInsight Hadoop. Spark - это платформа параллельной обработки с открытым исходным кодом, которая поддерживает обработку в памяти, чтобы повысить производительность приложений для анализа больших объемов данных.
- [Установка Giraph в кластерах HDInsight](hdinsight-hadoop-giraph-install.md). Используйте настройки кластера для установки Giraph в кластерах HDInsight Hadoop. Giraph позволяет выполнять обработку графов с использованием Hadoop, а также может использоваться с Azure HDInsight.
- [Установка Solr в кластерах HDInsight](hdinsight-hadoop-solr-install.md). Используйте настройки кластера для установки Solr в кластерах HDInsight Hadoop. Solr позволяет вести расширенный поиск по хранимым данным.


[hdinsight-provision]: ../hdinsight-provision-clusters/
[hdinsight-cluster-customize]: ../hdinsight-hadoop-customize-cluster
[hdinsight-install-spark]: ../hdinsight-hadoop-spark-install/



<!--HONumber=42-->

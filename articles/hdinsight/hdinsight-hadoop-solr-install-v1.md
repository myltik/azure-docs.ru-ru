<properties 
	pageTitle="Использование действия сценария для установки Solr в кластере Hadoop | Microsoft Azure" 
	description="Узнайте, как настроить кластер HDInsight для установки Solr. Вам следует воспользоваться параметром конфигурации действия скрипта (Script Action), чтобы использовать скрипт для установки Solr" 
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
	ms.date="08/07/2015" 
	ms.author="nitinme"/>

# Установка и использование Solr на кластерах HDInsight Hadoop

Solr можно установить в кластере Hadoop в Azure HDInsight любого типа с помощью настройки кластера **Действие сценария**. Действие сценария позволяет выполнять сценарии для настройки кластера только во время его создания. Дополнительную информацию см. в статье [Настройка кластера HDInsight с помощью действия сценария][hdinsight-cluster-customize].

[AZURE.INCLUDE [hdinsight-azure-portal](../../includes/hdinsight-azure-portal.md)]

* [Установка Solr в кластерах HDInsight](hdinsight-hadoop-solr-install.md)

В этом разделе описано, как установить Solr с помощью действия сценария. Solr представляет собой многофункциональную платформу поиска и предоставляет возможности поиска корпоративного уровня на основе данных, управляемых Hadoop. После установки Solr в кластере HDInsight вы также узнаете, как искать данные с помощью Solr.

> [AZURE.NOTE]Пример скрипта, используемый в данном разделе, создает кластер Solr с определенной конфигурацией. Если вы хотите настроить кластер Solr на использование других коллекций, сегментов, схем, реплик и т. п., необходимо соответствующим образом изменить скрипт и двоичные файлы Solr.


## <a name="whatis"></a>Что такое Solr

<a href="http://lucene.apache.org/solr/features.html" target="_blank">Apache Solr</a> — это корпоративная платформа поиска, предоставляющая многофункциональные инструменты полнотекстового поиска данных. Если Hadoop обеспечивает хранение огромных объемов данных и управление ими, то Apache Solr предоставляет возможности поиска для быстрого извлечения этих данных. Этот раздел содержит инструкции по настройке кластера HDInsight для установки Solr.

## <a name="install"></a>Как установить Solr

Пример сценария для установки Solr в кластере HDInsight доступен в большом двоичном объекте службы хранилища Azure (доступ только для чтения): [https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1](https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1). Этот раздел содержит инструкции по использованию примера скрипта при подготовке кластера с помощью портала Azure.


> [AZURE.NOTE]Пример скрипта работает только с кластером HDInsight версии 3.1. Дополнительную информацию о версиях кластера HDInsight см. в статье [Новые возможности версий кластеров Hadoop, предоставляемых HDInsight](hdinsight-component-versioning.md).


1. Начните подготовку кластера с помощью параметра **НАСТРАИВАЕМОЕ СОЗДАНИЕ**, как описано в статье [Подготовка кластера с использованием пользовательских параметров](hdinsight-provision-clusters.md#portal). 
2. На странице **Действия сценариев** мастера щелкните **Добавить действие сценария** для предоставления информации о данном действии сценария, как показано ниже:

	![Использование действия сценария для настройки кластера](./media/hdinsight-hadoop-solr-install-v1/hdi-script-action-solr.png "Использование действия сценария для настройки кластера")
	
	<table border='1'>
	<tr><th>Свойство</th><th>Значение</th></tr>
	<tr><td>Имя</td>
		<td>Укажите имя для действия сценария. Например, <b>Установить Solr</b>.</td></tr>
	<tr><td>URI-адрес сценария</td>
		<td>Укажите URI для сценария, который вызывается для настройки кластера. Например, <i>https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1</i>.</td></tr>
	<tr><td>Тип узла</td>
		<td>Укажите узлы, на которых выполняется сценарий настройки. Можно выбрать одно из трех значений: <b>Все узлы</b>, <b>Только головные узлы</b> или <b>Только рабочие узлы</b>.
	<tr><td>Параметры</td>
		<td>Укажите параметры, если они требуются для сценария. Сценарий для установки Solr не требует никаких параметров, поэтому можно оставить это поле пустым.</td></tr>
</table>Можно добавить несколько действий сценария для установки нескольких компонентов в кластере. После добавления скриптов щелкните значок с галочкой, чтобы начать подготовку кластера.

Сценарий также позволяет установить Solr в HDInsight с помощью Azure PowerShell или пакета SDK для HDInsight .NET. Инструкции по выполнению этих процедур приведены ниже в этом разделе.

## <a name="usesolr"></a>Как использовать Solr в HDInsight

Необходимо начать с индексирования системой Solr нескольких файлов данных. Затем можно использовать Solr для выполнения запросов поиска в индексированных данных. Выполните следующие действия, чтобы использовать Solr в кластере HDInsight.

1. **Используйте протокол удаленного рабочего стола (RDP) для удаленного подключения к кластеру HDInsight с установленной Solr**. На портале Azure включите удаленный рабочий стол для созданного кластера с установленной Solr, а затем подключитесь к кластеру. Указания см. в разделе <a href="http://azure.microsoft.com/documentation/articles/hdinsight-administer-use-management-portal/#rdp" target="_blank">Подключение к кластерам HDInsight с помощью RDP</a>.

2. **Выполните индексирование Solr, передав файлы данных**. При индексировании системы Solr вы помещаете в нее документы, по которым может потребоваться выполнить поиск. Чтобы выполнить индексирование Solr, подключитесь к кластеру по протоколу удаленного рабочего стола, перейдите на рабочий стол, откройте командную строку Hadoop и перейдите к **C:\\apps\\dist\\solr-4.7.2\\example\\exampledocs**. Выполните следующую команду:
	
		java -jar post.jar solr.xml monitor.xml

	В консоли будут выведены такие выходные данные:

		POSTing file solr.xml
		POSTing file monitor.xml
		2 files indexed.
		COMMITting Solr index changes to http://localhost:8983/solr/update..
		Time spent: 0:00:01.624

	Служебная программа post.jar индексирует Solr с использованием двух примеров документов — **solr.xml** и **monitor.xml**. Служебная программа post.jar и примеры документов входят в состав установки Solr.

3. **Воспользуйтесь панелью мониторинга Solr для поиска по индексированным документам**. В сеансе связи с кластером HDInsight по протоколу удаленного рабочего стола откройте Internet Explorer и запустите панель мониторинга Solr по адресу ****http://headnodehost:8983/solr/#/**. В левой области в раскрывающемся списке **Базовый селектор** выберите **collection1**, а затем щелкните **Запрос**. Например, чтобы выбрать и возвратить все документы в Solr, укажите следующие значения:
	1. В текстовом поле **q** введите ***:***. Это позволяет возвратить все документы, индексированные в Solr. Если требуется найти в документах конкретную строку, ее также можно ввести в этом поле.
	2. В текстовом поле **wt** выберите формат выходных данных. По умолчанию используется **JSON**. Щелкните **Выполнить запрос**.

		![Использование действия сценария для настройки кластера](./media/hdinsight-hadoop-solr-install-v1/hdi-solr-dashboard-query.png "Выполнение запроса на панели мониторинга Solr")
	
	В выходных данных возвращаются два документа, которые использовались при индексировании Solr. Результат выглядит следующим образом:

			"response": {
			    "numFound": 2,
			    "start": 0,
			    "maxScore": 1,
			    "docs": [
			      {
			        "id": "SOLR1000",
			        "name": "Solr, the Enterprise Search Server",
			        "manu": "Apache Software Foundation",
			        "cat": [
			          "software",
			          "search"
			        ],
			        "features": [
			          "Advanced Full-Text Search Capabilities using Lucene",
			          "Optimized for High Volume Web Traffic",
			          "Standards Based Open Interfaces - XML and HTTP",
			          "Comprehensive HTML Administration Interfaces",
			          "Scalability - Efficient Replication to other Solr Search Servers",
			          "Flexible and Adaptable with XML configuration and Schema",
			          "Good unicode support: héllo (hello with an accent over the e)"
			        ],
			        "price": 0,
			        "price_c": "0,USD",
			        "popularity": 10,
			        "inStock": true,
			        "incubationdate_dt": "2006-01-17T00:00:00Z",
			        "_version_": 1486960636996878300
			      },
			      {
			        "id": "3007WFP",
			        "name": "Dell Widescreen UltraSharp 3007WFP",
			        "manu": "Dell, Inc.",
			        "manu_id_s": "dell",
			        "cat": [
			          "electronics and computer1"
			        ],
			        "features": [
			          "30" TFT active matrix LCD, 2560 x 1600, .25mm dot pitch, 700:1 contrast"
			        ],
			        "includes": "USB cable",
			        "weight": 401.6,
			        "price": 2199,
			        "price_c": "2199,USD",
			        "popularity": 6,
			        "inStock": true,
			        "store": "43.17614,-90.57341",
			        "_version_": 1486960637584081000
			      }
			    ]
			  }
   

4. **Рекомендуется создать резервную копию индексированных данных из Solr в хранилище больших двоичных объектов Azure, связанном с кластером HDInsight**. Опыт показывает, что стоит выполнять резервное копирование индексированных данных с узлов кластера Solr в хранилище больших двоичных объектов Azure. Для этого выполните следующие действия:

	1. В сеансе связи по протоколу RDP откройте Internet Explorer и выберите следующий URL-адрес:

			http://localhost:8983/solr/replication?command=backup

		Вы должны получить примерно следующий ответ:

			<?xml version="1.0" encoding="UTF-8"?>
			<response>
			  <lst name="responseHeader">
			    <int name="status">0</int>
			    <int name="QTime">9</int>
			  </lst>
			  <str name="status">OK</str>
			</response>

	2. В удаленном сеансе перейдите к {SOLR\_HOME}\\{Collection}\\data. Для кластера, созданного с помощью примера сценария, это должна быть папка **C:\\apps\\dist\\solr-4.7.2\\example\\solr\\collection1\\data**. В этом расположении вы увидите папку моментальных снимков с именем, похожим на **snapshot.*метка\_времени***.
	
	3. Запакуйте эту папку моментальных снимков и отправьте ее в хранилище больших двоичных объектов Azure. В командной строке Hadoop перейдите к расположению папки моментальных снимков, используя следующую команду:

			  hadoop fs -CopyFromLocal snapshot._timestamp_.zip /example/data

		Эта команда копирует моментальный снимок в папку /example/data/ в контейнере учетной записи хранения по умолчанию, связанной с этим кластером.

## <a name="usingPS"></a>Установка Solr в кластерах HDInsight Hadoop с помощью Azure PowerShell

В этом разделе мы используем командлет **<a href = "http://msdn.microsoft.com/library/dn858088.aspx" target="_blank">Add-AzureHDInsightScriptAction</a>**, чтобы вызвать сценарии с помощью действия сценария для настройки кластера. Прежде чем продолжить, убедитесь, что вы установили и настроили Azure PowerShell. Информацию о настройке рабочей станции для запуска командлетов HDInsight Windows PowerShell см. в статье [Как установить и настроить Azure PowerShell][powershell-install-configure].

Выполните следующие действия:

1. Откройте окно Azure PowerShell и объявите следующие переменные:

		# PROVIDE VALUES FOR THESE VARIABLES
		$subscriptionName = "<SubscriptionName>"		# Name of the Azure subscription
		$clusterName = "<HDInsightClusterName>"			# HDInsight cluster name
		$storageAccountName = "<StorageAccountName>"	# Azure Storage account that hosts the default container
		$storageAccountKey = "<StorageAccountKey>"      # Key for the Storage account
		$containerName = $clusterName
		$location = "<MicrosoftDataCenter>"				# Location of the HDInsight cluster. It must be in the same data center as the Storage account.
		$clusterNodes = <ClusterSizeInNumbers>			# Number of nodes in the HDInsight cluster
		$version = "<HDInsightClusterVersion>"          # For example, "3.1"
	
2. Укажите такие значения настройки, как узлы в кластере и хранилище для использования по умолчанию.

		# Specify the configuration options
		Select-AzureSubscription $subscriptionName
		$config = New-AzureHDInsightClusterConfig -ClusterSizeInNodes $clusterNodes
		$config.DefaultStorageAccount.StorageAccountName="$storageAccountName.blob.core.windows.net"
		$config.DefaultStorageAccount.StorageAccountKey=$storageAccountKey
		$config.DefaultStorageAccount.StorageContainerName=$containerName
	
3. Используйте командлет **Add-AzureHDInsightScriptAction**, чтобы добавить действие сценария в конфигурацию кластера. Позже, а именно при создании кластера, действие скрипта начнет выполняться.

		# Add the script action to the cluster configuration
		$config = Add-AzureHDInsightScriptAction -Config $config -Name "Install Solr" -ClusterRoleCollection HeadNode,DataNode -Uri https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1

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
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Узлы, на которых выполняется скрипт настройки. Допустимые значения: HeadNode (для установки на головном узле) или DataNode (для установки на всех узлах данных). Можно использовать как одно, так и оба значения.</td></tr>
<tr>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">URI</td>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">URI для выполняемого скрипта.</td></tr>
<tr>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Параметры</td>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Параметры, необходимые для скрипта. В приведенном образце скрипта не требуется задавать параметры, поэтому этот параметр не указан в приведенном выше фрагменте кода.
</td></tr>
</table>
	
4. Наконец, начните подготовку настроенного кластера с установленной Solr.
	
		# Start provisioning a cluster with Solr installed
		New-AzureHDInsightCluster -Config $config -Name $clusterName -Location $location -Version $version 

При появлении запроса введите учетные данные для кластера. Создание кластера может занять несколько минут.


## <a name="usingSDK"></a>Установка Solr в кластерах HDInsight Hadoop с помощью пакета SDK для .NET

Пакет SDK для HDInsight .NET предоставляет клиентские библиотеки .NET, которые упрощают работу с кластерами HDInsight из приложения .NET Framework. Этот раздел содержит инструкции по использованию действия скрипта из пакета SDK для подготовки кластера с установленной Solr. Необходимо выполнить следующие процедуры:

- Установка пакета SDK для HDInsight .NET
- Создание самозаверяющего сертификата
- Создание консольного приложение
- Выполнение приложения


**Установка пакета SDK для HDInsight .NET**

Последнюю опубликованную сборку пакета SDK можно установить с сайта [NuGet](http://nuget.codeplex.com/wikipage?title=Getting%20Started). Инструкции будут показаны в следующей процедуре.

**Создание самозаверяющего сертификата**

Создание самозаверяющего сертификата, установка его на рабочую станцию и загрузка на вашу подписку Azure. Более подробные инструкции см. в разделе [Создание самозаверяющего сертификата](http://go.microsoft.com/fwlink/?LinkId=511138).


**Создание приложения Visual Studio**

1. Откройте Visual Studio 2013.

2. В меню **Файл** выберите команду **Создать**, а затем — **Проект**.

3. В окне **Новый проект** введите или выберите следующие значения.
	
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
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">CreateSolrCluster</td></tr>
</table>

4. Нажмите кнопку **ОК**, чтобы создать проект.

5. В меню **Средства** щелкните **Диспетчер пакетов Nuget**, а затем щелкните **Консоль диспетчера пакетов**.

6. Для установки пакета выполните следующую команду в консоли:

		Install-Package Microsoft.WindowsAzure.Management.HDInsight

	Эта команда добавляет библиотеки .NET и ссылки на них в текущий проект Visual Studio.

	
7. В обозревателе решений дважды щелкните **Program.cs**, чтобы открыть файл.

8. Добавьте в начало файла следующие инструкции using:

		using System.Security.Cryptography.X509Certificates;
		using Microsoft.WindowsAzure.Management.HDInsight;
		using Microsoft.WindowsAzure.Management.HDInsight.ClusterProvisioning;
		using Microsoft.WindowsAzure.Management.HDInsight.Framework.Logging;
	
9. В функции Main() скопируйте и вставьте следующий код и задайте значения для переменных:
		
        var clusterName = args[0];

        // Provide values for the variables
        string thumbprint = "<CertificateThumbprint>";  
        string subscriptionId = "<AzureSubscriptionID>";
        string location = "<MicrosoftDataCenterLocation>";
        string storageaccountname = "<AzureStorageAccountName>.blob.core.windows.net";
        string storageaccountkey = "<AzureStorageAccountKey>";
        string username = "<HDInsightUsername>";
        string password = "<HDInsightUserPassword>";
        int clustersize = <NumberOfNodesInTheCluster>;

        // Provide the certificate thumbprint to retrieve the certificate from the certificate store 
        X509Store store = new X509Store();
        store.Open(OpenFlags.ReadOnly);
        X509Certificate2 cert = store.Certificates.Cast<X509Certificate2>().First(item => item.Thumbprint == thumbprint);

        // Create an HDInsight client object
        HDInsightCertificateCredential creds = new HDInsightCertificateCredential(new Guid(subscriptionId), cert);
        var client = HDInsightClient.Connect(creds);
		client.IgnoreSslErrors = true;
        
        // Provide the cluster information
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

10. Добавьте следующий код в функцию Main(), чтобы использовать класс [ScriptAction](http://msdn.microsoft.com/library/microsoft.windowsazure.management.hdinsight.clusterprovisioning.data.scriptaction.aspx) для вызова пользовательского сценария для установки Solr.

		// Add the script action to install Solr
        clusterInfo.ConfigActions.Add(new ScriptAction(
          "Install Solr", // Name of the config action
          new ClusterNodeType[] { ClusterNodeType.HeadNode, ClusterNodeType.DataNode }, // List of nodes to install Solr on
          new Uri("https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1"), // Location of the script to install Solr
		  null //Because the script used does not require any parameters
        ));

11. Наконец, создайте кластер.

		client.CreateCluster(clusterInfo);

11. Сохраните изменения в приложение и выполните построение решения.

**Запуск приложения**

Откройте консоль Windows PowerShell или Azure PowerShell, перейдите к расположению, где был сохранен проект Visual Studio, а затем перейдите к каталогу \\bin\\debug в проекте и выполните следующую команду:

	.\CreateSolrCluster <cluster-name>

Укажите имя кластера и нажмите клавишу ВВОД, чтобы подготовить кластер с установленной системой Solr.


## Дополнительные материалы##
- [Установка и использование Spark в кластерах HDInsight][hdinsight-install-spark]. Используйте настройки кластера для установки Spark в кластерах HDInsight Hadoop. Spark — это платформа параллельной обработки с открытым исходным кодом, которая поддерживает обработку в памяти, чтобы повысить производительность приложений для анализа данных большого объема.
- [Установка R в кластерах HDInsight][hdinsight-install-r]. Используйте настройки кластера для установки R в кластерах HDInsight Hadoop. R — это открытый язык программирования и свободная программная среда для статистических вычислений. Он предоставляет сотни встраиваемых статистических функций и собственный язык программирования, который сочетает аспекты функционального и объектно-ориентированного программирования. Этот проект также обеспечивает обширные графические возможности.
- [Установка Giraph в кластерах HDInsight](hdinsight-hadoop-giraph-install.md). Используйте настройки кластера для установки Giraph в кластерах HDInsight Hadoop. Giraph позволяет выполнять обработку графов с использованием Hadoop и может использоваться с Azure HDInsight.



[powershell-install-configure]: ../install-configure-powershell.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-install-r]: hdinsight-hadoop-r-scripts.md
[hdinsight-install-spark]: hdinsight-hadoop-spark-install.md
[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster.md
 

<!---HONumber=August15_HO8-->
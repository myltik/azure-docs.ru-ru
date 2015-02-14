<properties 
	pageTitle="Настройка кластеров HDInsight с помощью действия скрипта| Azure" 
	description="Дополнительные сведения о настройке кластеров Hadoop с помощью действия скрипта." 
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
	ms.date="01/15/2015" 
	ms.author="nitinme"/> 

# Настройка кластеров HDInsight с помощью действия скрипта

Вы можете настраивать кластер Azure HDInsight, чтобы устанавливать на нем дополнительное программное обеспечение или изменять конфигурацию приложений. В HDInsight есть параметр конфигурации **Script Action**, вызывающий пользовательские скрипты, с помощью которых можно настроить кластер. Эти скрипты можно использовать для настройки кластера в процессе развертывания.  

Кластеры HDInsight можно настраивать множеством других способов, например включая дополнительные учетные записи хранения, изменяя файлы конфигурации Hadoop (core-site.xml, hive-site.xml и т. д.) или добавляя общие библиотеки (например, Hive, Oozie) в стандартные расположения в кластере. Настройку можно осуществлять с помощью HDInsight PowerShell, пакета SDK .NET или портала управления Azure. Дополнительные сведения см. в разделе [Подготовка кластеров Hadoop в HDInsight с помощью пользовательских параметров][hdinsight-provision-cluster].



> [AZURE.NOTE] Использовать Script Action для настройки кластера можно только в кластере HDInsight версии 3.1. Дополнительные сведения о версиях кластера HDInsight см. в разделе [Версии кластера HDInsight](http://azure.microsoft.com/ru-ru/documentation/articles/hdinsight-component-versioning/).



## Содержание

- [Как используется скрипт во время создания кластера?](#lifecycle)
- [Как написать скрипт для настройки кластера?](#writescript)
- [Как использовать действие скрипта для настройки кластера?](#howto)
- [Примеры настройки кластера](#example)
- [Поддержка программного обеспечения с открытым исходным кодом, используемого в кластере HDInsight](#support)


## <a name="lifecycle"></a>Как используется скрипт во время создания кластера?

С помощью действия скрипта кластер HDInsight можно настроить только в процессе создания. Выделяются следующие стадии создания кластера HDInsight:

![HDInsight cluster customization and stages during cluster provisioning][img-hdi-cluster-states] 

Скрипт вызывается после завершения стадии *HDInsightConfiguration* и до начала стадии *ClusterOperational*. В каждом кластере можно использовать несколько скриптов, которые вызываются в том порядке, в котором они указаны.

> [AZURE.NOTE] Настройка кластеров HDInsight доступна в рамках стандартных подписок на Azure HDInsight без дополнительной платы.

### Как работает скрипт?

Вы можете запускать скрипт на головном узле, рабочих узлах или на всех вместе. При выполнении скрипта кластер переходит на стадию *ClusterCustomization*. На этой стадии скрипт выполняется под учетной записью администратора системы, параллельно на всех указанных узлах в кластере и предоставляет права администратора на узлах в полном объеме. 

> [AZURE.NOTE] Так как у вас есть права администратора на узлах кластера на стадии *Cluster customization*, вы можете использовать скрипт для выполнения таких операций, как остановка и запуск служб, в том числе служб, связанных с Hadoop. Таким образом, перед завершением работы скрипта необходимо запустить службы Ambari и другие службы, связанные с Hadoop. Эти службы нужны для определения работоспособности и состояния кластера при его создании. При изменении любых настроек в кластере, затрагивающих эти службы, необходимо использовать указанные вспомогательные функции. Дополнительные сведения о вспомогательных функциях см. в разделе [Разработка действия скрипта с помощью HDInsight][hdinsight-write-script].

Выходные данные, а также журналы ошибок скрипта, хранятся в учетной записи хранения, заданной по умолчанию для кластера. Журналы хранятся в таблице с именем *u<\cluster-name-fragment><\time-stamp>setuplog*. Это сводные журналы о запусках скрипта на всех узлах (на головном и рабочих) в кластере.

## <a name="writescript"></a>Как написать скрипт для настройки кластера?

Сведения о том, как написать скрипт для настройки кластера, см. в разделе [Разработка действия скрипта с помощью HDInsight][hdinsight-write-script]. 

## <a name="howto"></a>Как использовать действие скрипта для настройки кластера?

Действие скрипта можно использовать из портала управления Azure, командлетов PowerShell или пакета SDK HDInsight .NET для настройки кластера. 

**На портале управления**

1. Начните подготовку кластера к работе с помощью параметра **Настраиваемое создание**, как описано в разделе [Подготовка кластера с использованием пользовательских параметров](http://azure.microsoft.com/ru-ru/documentation/articles/hdinsight-provision-clusters/#portal). 
2. На странице "Действия скриптов" мастера нажмите кнопку **Добавить действие скрипта** для предоставления сведений о данном действии скрипта, как показано ниже:

	![Use Script Action to customize a cluster](./media/hdinsight-hadoop-customize-cluster/HDI.CustomProvision.Page6.png "Use Script Action to customize a cluster")
	
	<table border='1'>
		<tr><th>Свойство</th><th>Значение</th></tr>
		<tr><td>Имя</td>
			<td>Укажите имя для действия скрипта.</td></tr>
		<tr><td>URI скрипта</td>
			<td>Укажите URI для скрипта, вызываемого для настройки кластера.</td></tr>
		<tr><td>Тип узла</td>
			<td>Указывает узлы, на которых выполняется скрипт настройки. Вы можете <b>Все узлы</b>, <b>Только головные узлы</b> или <b>Только рабочие узлы</b>.
		<tr><td>Параметры</td>
			<td>Укажите необходимые для скрипта параметры.</td></tr>
	</table>

	Можно добавить несколько действий скрипта для установки нескольких компонентов в кластере. После добавления скриптов щелкните значок с галочкой, чтобы начать подготовку кластера. 
  
**Использование командлетов PowerShell**

Используйте команды HDInsight PowerShell для выполнения одного или нескольких действий скрипта. Можно использовать командлет **<a href = "http://msdn.microsoft.com/ru-ru/library/dn858088.aspx" target="_blank">Add-AzureHDInsightScriptAction</a>** для вызова пользовательских скриптов. Чтобы использовать эти командлеты, требуется установить и настроить Azure PowerShell. Сведения о настройке рабочей станции для запуска командлетов HDInsight Powershell см. в разделе [Установка и настройка Azure PowerShell][powershell-install-configure].

Используйте следующую команду PowerShell для единичного выполнения Script Action при развертывании кластера HDInsight:

	$config = New-AzureHDInsightClusterConfig -ClusterSizeInNodes 4

	$config = Add-AzureHDInsightScriptAction -Config $config -Name MyScriptActionName -Uri http://uri.to/scriptaction.ps1 -Parameters MyScriptActionParameter -ClusterRoleCollection HeadNode,DataNode

	New-AzureHDInsightCluster -Config $config

Используйте следующую команду PowerShell для множественного выполнения Script Action при развертывании кластера HDInsight:

	$config = New-AzureHDInsightClusterConfig -ClusterSizeInNodes 4

	$config = Add-AzureHDInsightScriptAction -Config $config -Name MyScriptActionName1 -Uri http://uri.to/scriptaction1.ps1 -Parameters MyScriptAction1Parameters -ClusterRoleCollection HeadNode,DataNode | Add-AzureHDInsightScriptAction -Config $config -Name MyScriptActionName2 -Uri http://uri.to/scriptaction2.ps1 -Parameters MyScriptAction2Parameters -ClusterRoleCollection HeadNode

	New-AzureHDInsightCluster -Config $config

**Использование пакета SDK HDInsight .NET**

Пакет SDK HDInsight .NET предоставляет класс <a href="http://msdn.microsoft.com/ru-ru/library/microsoft.windowsazure.management.hdinsight.clusterprovisioning.data.scriptaction.aspx" target="_blank">ScriptAction</a> для вызова пользовательских скриптов. Порядок использования пакета SDKHDInsight .NET:

1. Создайте приложение Visual Studio и затем установите пакет SDK с сайта Nuget. В меню **Инструменты** нажмите **Диспетчер пакетов Nuget**, а затем - **Консоль диспетчера пакетов**. Для установки пакета выполните следующие команды в консоли:

		Install-Package Microsoft.WindowsAzure.Management.HDInsight

2. Создайте кластер с помощью пакета SDK. Инструкции см. в разделе [Подготовка кластера HDInsight с помощью пакета SDK .NET](http://azure.microsoft.com/ru-ru/documentation/articles/hdinsight-provision-clusters/#sdk).

3. Используйте класс **ScriptAction** для вызова пользовательского скрипта, как показано ниже:

		
		var clusterInfo = new ClusterCreateParameters()
		{
			// PROVIDE THE CLUSTER INFORMATION LIKE
			// NAME, STORAGE ACCOUNT, CREDENTIALS,
			// CLUSTER SIZE, and VERSION		    
			...
			...
		};

		// ADD THE SCRIPT ACTION TO INSTALL SPARK
		clusterInfo.ConfigActions.Add(new ScriptAction(
	  		"MyScriptActionName", // Name of the config action
	  		new ClusterNodeType[] { ClusterNodeType.HeadNode }, // List of nodes to install component on
	  		new Uri("http://uri.to/scriptaction.ps1"), // Location of the script to install the component
	  		"MyScriptActionParameter" //Parameters, if any, required by the script.
		));


## <a name="example"></a>Примеры настройки кластера

В HDInsight приведены примеры скриптов для установки следующих компонентов в кластере HDInsight.

- **Установка Spark**. См. раздел [Установка Spark в кластерах HDInsight][hdinsight-install-spark].
- **Установка R**. См. раздел [Установка R в кластерах HDInsight][hdinsight-install-r].
- **Установка Solr**. [Установка и использование Solr в кластерах HDInsight](../hdinsight-hadoop-solr-install)
- **Установка Giraph**. [Установка и использование Giraph в кластерах HDInsight](../hdinsight-hadoop-giraph-install)

## <a name="support"></a>Поддержка программного обеспечения с открытым исходным кодом, используемого в кластере HDInsight
Служба HDInsight Microsoft Azure - это гибкая платформа, которая позволяет создавать большие приложения для работы с данными большого размера в облаке, используя сформированную вокруг Hadoop экосистему технологий с открытым исходным кодом. Microsoft Azure предоставляет общий уровень поддержки для технологий с открытым исходным кодом, как описано в разделе <a href="http://azure.microsoft.com/ru-ru/support/faq/" target="_blank">о сфере обеспечения поддержки на сайте вопросов и ответов по поддержке Azure</a>. Кроме того, служба HDInsight предоставляет дополнительный уровень поддержки для некоторых описанных ниже компонентов.

В службе HDInsight доступно два типа компонентов с открытым исходным кодом.

- **Встроенные компоненты**. Эти компоненты предустановлены в кластерах HDInsight и предоставляют его базовые основные функциональные возможности. Например, этой категории относится диспетчер ресурсов Yarn, язык запросов Hive и библиотека Mahout. Полный список компонентов кластера приведен <a href="http://azure.microsoft.com/ru-ru/documentation/articles/hdinsight-component-versioning/" target="_blank">здесь</a>.
- **настраиваемые компоненты**. Как пользователь кластера, вы можете установить или использовать в рабочей нагрузке любой компонент, полученный из сообщества или созданный своими силами.

Встроенные компоненты полностью поддерживаются, и служба поддержки корпорации Майкрософт поможет выявить и устранить проблемы, связанные с этими компонентами.

Настраиваемые компоненты получают ограниченную коммерчески оправданную поддержку, способствующую дальнейшей диагностике проблемы. В результате проблема может быть устранена, либо вас могут попросить воспользоваться доступными каналами по технологиям с открытым исходным кодом, чтобы связаться с экспертами в данной области. Например, существует множество сайтов сообщества, которые можно использовать, таких как <a href ="https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=hdinsight" target="_blank">форум MSDN для HDInsight</a>, <a href="http://stackoverflow.com" target="_blank">http://stackoverflow.com</a>. Кроме того, по адресу <a href="http://apache.org" target="_blank">http://apache.org</a>можно найти сайты по проектам Apache, например <a href="http://hadoop.apache.org/" target="_blank">Hadoop</a>, <a href="http://spark.apache.org/" target="_blank">Spark</a>.

Служба HDInsight позволяет использовать настраиваемые компоненты несколькими разными способами. Уровень поддержки не зависит от того, как компонент используется или устанавливается в кластере. Ниже приведен список самых распространенных способов использования настраиваемых компонентов в кластерах HDInsight.

1. Отправка задания. В кластер можно отправлять задания Hadoop или другие типы заданий, выполняющие или использующие настраиваемые компоненты.
2. Настройка кластера. Во время создания кластера можно указать дополнительные параметры и настраиваемые компоненты, которые будут установлены на узлах кластера.
3. Примеры/ Для популярных настраиваемых компонентов корпорация Майкрософт и другие компании могут предоставлять примеры использования таких компонентов в кластерах HDInsight. Эти примеры представляются без поддержки.


## Дополнительные материалы##
В разделе [Подготовка кластеров Hadoop в HDInsight с помощью настраиваемых параметров][hdinsight-provision-cluster] приведены инструкции по подготовке кластера HDInsight с помощью других настраиваемых параметров.

[hdinsight-install-spark]: ../hdinsight-hadoop-spark-install/
[hdinsight-install-r]: ../hdinsight-hadoop-r-scripts/
[hdinsight-write-script]: ../hdinsight-hadoop-script-actions/
[hdinsight-provision-cluster]: ../hdinsight-provision-clusters/
[powershell-install-configure]: ../install-configure-powershell/


[img-hdi-cluster-states]: ./media/hdinsight-hadoop-customize-cluster/HDI-Cluster-state.png "Stages during cluster provisioning"
<!--HONumber=42-->

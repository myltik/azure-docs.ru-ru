<properties 
	pageTitle="Настройка кластеров HDInsight с помощью действий скрипта | Microsoft Azure" 
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
	ms.date="03/03/2015" 
	ms.author="nitinme"/>

# Настройка кластеров HDInsight с помощью действия сценария

Вы можете настраивать кластер Azure HDInsight, чтобы устанавливать на нем дополнительное программное обеспечение или изменять конфигурацию приложений. В HDInsight есть параметр конфигурации **Действие сценария**, вызывающий пользовательские сценарии, с помощью которых можно настроить кластер. Эти сценарии можно использовать для настройки кластера *в процессе развертывания*.

Кластеры HDInsight можно настраивать множеством других способов, например включая дополнительные учетные записи хранения Azure, изменяя файлы конфигурации Hadoop (core-site.xml, hive-site.xml и т. д.) или добавляя общие библиотеки (например, Hive, Oozie) в стандартные расположения в кластере. Эти настройки можно внести с помощью Azure PowerShell, пакета SDK для Azure для HDInsight .NET или на портале Azure. Дополнительную информацию см. в статье [Подготовка кластеров Hadoop в HDInsight с использованием настраиваемых параметров][hdinsight-provision-cluster].



> [AZURE.NOTE]Использовать Script Action для настройки кластера можно только в кластере HDInsight версии 3.1. Дополнительную информацию о версиях кластера HDInsight см. в статье [Новые возможности версий кластеров Hadoop, предоставляемых HDInsight](hdinsight-component-versioning.md).


## <a name="lifecycle"></a>Как используется сценарий во время создания кластера

С помощью действия сценария кластер HDInsight можно настроить только в процессе создания. Выделяются следующие стадии создания кластера HDInsight:

![Настройка кластера HDInsight и этапы подготовки кластера][img-hdi-cluster-states]

Сценарий вызывается после завершения этапа **HDInsightConfiguration** и до начала этапа **ClusterOperational**. В каждом кластере можно использовать несколько действий сценариев, которые вызываются в том порядке, в котором они указаны.

> [AZURE.NOTE]Настройка кластеров HDInsight доступна в рамках стандартных подписок на Azure HDInsight без дополнительной платы.

### Как работает сценарий

Вы можете запускать сценарий на головном узле, рабочих узлах или на всех вместе. При выполнении сценария кластер переходит к этапу **ClusterCustomization**. На этой стадии скрипт выполняется под учетной записью администратора системы, параллельно на всех указанных узлах в кластере и предоставляет права администратора на узлах в полном объеме.

> [AZURE.NOTE]Так как у вас есть права администратора в узлах кластера на этапе **ClusterCustomization**, вы можете использовать сценарий для выполнения таких операций, как остановка и запуск служб, в том числе служб, связанных с Hadoop. Таким образом, перед завершением работы сценария необходимо запустить службы Ambari и другие службы, связанные с Hadoop. Эти службы нужны для определения работоспособности и состояния кластера при его создании. При изменении любых настроек в кластере, затрагивающих эти службы, необходимо использовать указанные вспомогательные функции. Дополнительную информацию о вспомогательных функциях см. в статье [Разработка действия сценария с помощью HDInsight][hdinsight-write-script].

Выходные данные и журналы ошибок сценария хранятся в учетной записи хранения, заданной по умолчанию для кластера. Журналы хранятся в таблице с именем **u<\\cluster-name-fragment><\\time-stamp>setuplog**. Это сводные журналы сценария, выполняемого на всех узлах (на головном и рабочих) в кластере.

## <a name="writescript"></a>Как написать сценарий для настройки кластера

Информацию о том, как написать сценарий для настройки кластера, см. в разделе [Разработка действия сценария с помощью HDInsight][hdinsight-write-script].

## <a name="howto"></a>Как использовать действие сценария для настройки кластера

Настроить кластер с помощью действия сценария можно путем использования портала Azure, командлетов Azure PowerShell или пакета SDK для HDInsight .NET.

**Использование портала Azure**

1. Начните подготовку кластера с помощью параметра **НАСТРАИВАЕМОЕ СОЗДАНИЕ**, как описано в разделе [Подготовка кластера с использованием пользовательских параметров](hdinsight-provision-clusters.md#portal). 
2. На странице **Действия сценариев** мастера щелкните **Добавить действие сценария** для предоставления информации о данном действии сценария, как показано ниже:

	![Использование действия сценария для настройки кластера](./media/hdinsight-hadoop-customize-cluster/HDI.CustomProvision.Page6.png "Использование действия сценария для настройки кластера")
	
	<table border='1'>
	<tr><th>Свойство</th><th>Значение</th></tr>
	<tr><td>Имя</td>
		<td>Укажите имя для действия сценария.</td></tr>
	<tr><td>URI-адрес сценария</td>
		<td>Укажите URI для сценария, который вызывается для настройки кластера.</td></tr>
	<tr><td>Тип узла</td>
		<td>Укажите узлы, на которых выполняется сценарий настройки. Вы можете выбрать одно из трех значений: <b>Все узлы</b>, <b>Только головные узлы</b> или <b>Только рабочие узлы</b>.
	<tr><td>Параметры</td>
		<td>Укажите параметры, если они требуются для сценария.</td></tr>
</table>Можно добавить несколько действий сценария для установки нескольких компонентов в кластере. После добавления скриптов щелкните значок с галочкой, чтобы начать подготовку кластера.
  
**Использование других командлетов Azure PowerShell**

Используйте команды Azure PowerShell для HDInsight, чтобы выполнить одно или несколько действий сценария. Вы можете использовать командлет **<a href = "http://msdn.microsoft.com/library/dn858088.aspx" target="_blank">Add-AzureHDInsightScriptAction</a>** для вызова пользовательских сценариев. Чтобы использовать эти командлеты, требуется установить и настроить Azure PowerShell. Информацию о настройке рабочей станции для запуска командлетов Azure PowerShell для HDInsight см. в статье [Как установить и настроить Azure PowerShell][powershell-install-configure].

Используйте следующую команду Azure PowerShell для выполнения одного действия сценария при развертывании кластера HDInsight:

	$config = New-AzureHDInsightClusterConfig –ClusterSizeInNodes 4

	$config = Add-AzureHDInsightScriptAction -Config $config –Name MyScriptActionName –Uri http://uri.to/scriptaction.ps1 –Parameters MyScriptActionParameter -ClusterRoleCollection HeadNode,DataNode

	New-AzureHDInsightCluster -Config $config

Используйте следующую команду Azure PowerShell для выполнения нескольких действий сценария при развертывании кластера HDInsight:

	$config = New-AzureHDInsightClusterConfig –ClusterSizeInNodes 4

	$config = Add-AzureHDInsightScriptAction -Config $config –Name MyScriptActionName1 –Uri http://uri.to/scriptaction1.ps1 –Parameters MyScriptAction1Parameters -ClusterRoleCollection HeadNode,DataNode | Add-AzureHDInsightScriptAction -Config $config –Name MyScriptActionName2 –Uri http://uri.to/scriptaction2.ps1 -Parameters MyScriptAction2Parameters -ClusterRoleCollection HeadNode

	New-AzureHDInsightCluster -Config $config

**Использование пакета SDK для HDInsight .NET**

Пакет SDK для HDInsight .NET позволяет использовать класс <a href="http://msdn.microsoft.com/library/microsoft.windowsazure.management.hdinsight.clusterprovisioning.data.scriptaction.aspx" target="_blank">ScriptAction</a> для вызова пользовательских сценариев. Порядок использования пакета SDKHDInsight .NET:

1. Создайте приложение Visual Studio, а затем установите пакет SDK с сайта Nuget. В меню **Средства** щелкните **Диспетчер пакетов Nuget**, а затем щелкните **Консоль диспетчера пакетов**. Для установки пакета выполните следующую команду в консоли:

		Install-Package Microsoft.WindowsAzure.Management.HDInsight

2. Создайте кластер с помощью пакета SDK. Указания см. в разделе [Подготовка кластера HDInsight с помощью пакета SDK для .NET](hdinsight-provision-clusters.md#sdk).

3. Используйте класс **ScriptAction** для вызова пользовательского сценария, как показано ниже:

		
		var clusterInfo = new ClusterCreateParameters()
		{
			// Provide the cluster information, like
			// name, Storage account, credentials,
			// cluster size, and version		    
			...
			...
		};

		// Add the script action to install Spark
		clusterInfo.ConfigActions.Add(new ScriptAction(
	  		"MyScriptActionName", // Name of the config action
	  		new ClusterNodeType[] { ClusterNodeType.HeadNode }, // List of nodes to install the component on
	  		new Uri("http://uri.to/scriptaction.ps1"), // Location of the script to install the component
	  		"MyScriptActionParameter" //Parameters, if any, required by the script
		));


## <a name="example"></a>Примеры настройки кластера

В HDInsight приведены примеры сценариев для установки следующих компонентов в кластере HDInsight.

- **Установка Spark** — см. статью [Установка и использование Spark на кластерах HDInsight][hdinsight-install-spark].
- **Установка R** — см. статью [Установка и использование R на кластерах HDInsight][hdinsight-install-r].
- **Установка Solr** — см. статью [Установка и использование Solr на кластерах HDInsight](hdinsight-hadoop-solr-install.md).
- **Установка Giraph** — см. статью [Установка и использование Giraph на кластерах HDInsight](hdinsight-hadoop-giraph-install.md).

## <a name="support"></a>Поддержка программного обеспечения с открытым исходным кодом, используемого в кластере HDInsight
Служба Microsoft Azure HDInsight — это гибкая платформа, которая позволяет создавать приложения для работы с данными большого размера в облаке, используя сформированную вокруг Hadoop экосистему технологий с открытым исходным кодом. Microsoft Azure предоставляет общий уровень поддержки для технологий с открытым исходным кодом, как описано в разделе **Объем поддержки** на <a href="http://azure.microsoft.com/support/faq/" target="_blank">веб-сайте часто задаваемых вопросов о поддержке Azure</a>. Служба HDInsight предоставляет дополнительный уровень поддержки для некоторых описанных ниже компонентов.

В службе HDInsight доступно два типа компонентов с открытым исходным кодом.

- **Встроенные компоненты**. Эти компоненты предварительно установлены в кластерах HDInsight и предоставляют его базовые функциональные возможности. Например, к этой категории относится диспетчер ресурсов YARN, язык запросов Hive (HiveQL) и библиотека Mahout. Полный список компонентов кластера доступен в статье <a href="http://azure.microsoft.com/documentation/articles/hdinsight-component-versioning/" target="_blank">Новые возможности версий кластеров Hadoop, предоставляемых HDInsight</a>.
- **Настраиваемые компоненты**. Как пользователь кластера вы можете установить или использовать в рабочей нагрузке любой компонент, полученный из сообщества или созданный самостоятельно.

Встроенные компоненты полностью поддерживаются, и служба поддержки корпорации Майкрософт поможет выявить и устранить проблемы, связанные с этими компонентами.

Настраиваемые компоненты получают ограниченную коммерчески оправданную поддержку, способствующую дальнейшей диагностике проблемы. В результате проблема может быть устранена, либо вас могут попросить воспользоваться доступными каналами по технологиям с открытым исходным кодом, чтобы связаться с экспертами в данной области. Например, существует множество сайтов сообществ, которые можно использовать, такие как: <a href ="https://social.msdn.microsoft.com/Forums/azure/home?forum=hdinsight" target="_blank">Форум MSDN по HDInsight</a> и <a href="http://stackoverflow.com" target="_blank">Stack Overflow</a>. Кроме того, у проектов Apache есть соответствующие сайты по-адресу <a href="http://apache.org" target="_blank">Apache.org</a>, например для <a href="http://hadoop.apache.org/" target="_blank">Hadoop</a> и <a href="http://spark.apache.org/" target="_blank">Spark</a>.

Служба HDInsight позволяет использовать настраиваемые компоненты несколькими разными способами. Уровень поддержки не зависит от того, как компонент используется или устанавливается в кластере. Ниже приведен список самых распространенных способов использования настраиваемых компонентов в кластерах HDInsight.

1. Отправка задания. В кластер можно отправлять задания Hadoop или другие типы заданий, выполняющие или использующие настраиваемые компоненты.
2. Настройка кластера. Во время создания кластера можно указать дополнительные параметры и настраиваемые компоненты, которые будут установлены в узлах кластера.
3. Примеры. Для популярных настраиваемых компонентов корпорация Майкрософт и другие компании могут предоставлять примеры использования таких компонентов в кластерах HDInsight. Эти примеры представляются без поддержки.


## Дополнительные материалы##
В статье [Подготовка кластеров Hadoop в HDInsight с использованием настраиваемых параметров][hdinsight-provision-cluster] приведены указания по подготовке кластера HDInsight с использованием других настраиваемых параметров.

[hdinsight-install-spark]: hdinsight-hadoop-spark-install.md
[hdinsight-install-r]: hdinsight-hadoop-r-scripts.md
[hdinsight-write-script]: hdinsight-hadoop-script-actions.md
[hdinsight-provision-cluster]: hdinsight-provision-clusters.md
[powershell-install-configure]: ../install-configure-powershell.md


[img-hdi-cluster-states]: ./media/hdinsight-hadoop-customize-cluster/HDI-Cluster-state.png "Этапы подготовки кластера"
 

<!---HONumber=62-->
<properties
	pageTitle="Настройка кластеров HDInsight с помощью действий скрипта | Microsoft Azure"
	description="Дополнительные сведения о настройке кластеров HDInsight с помощью действия скрипта."
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
	ms.date="10/02/2015"
	ms.author="nitinme"/>

# Настройка кластеров HDInsight с помощью действия скрипта (Windows)

В HDInsight есть параметр конфигурации **Действие сценария**, вызывающий пользовательские сценарии, с помощью которых можно настроить кластер в процессе создания. Эти сценарии можно использовать, чтобы устанавливать на нем дополнительное программное обеспечение или изменять конфигурацию приложений.

[AZURE.INCLUDE [hdinsight-azure-preview-portal](../../includes/hdinsight-azure-preview-portal.md)]

* [Настройка кластеров HDInsight с помощью действия сценария](hdinsight-hadoop-customize-cluster-v1.md)

> [AZURE.NOTE]Информация, приведенная в этой статье, относится только к кластерам HDInsight под управлением Windows. Версию данной статьи для кластеров под управлением Linux см. в разделе [Настройка кластеров HDInsight с помощью действия сценария (Linux)](hdinsight-hadoop-customize-cluster-linux.md).

Кластеры HDInsight можно настраивать множеством других способов, например включая дополнительные учетные записи хранения Azure, изменяя файлы конфигурации Hadoop (core-site.xml, hive-site.xml и т. д.) или добавляя общие библиотеки (например, Hive, Oozie) в стандартные расположения в кластере. Эти настройки можно выполнить с помощью Azure PowerShell, пакета SDK для Azure для HDInsight .NET или портала предварительной версии Azure. Дополнительные сведения см. в разделе [Создание кластеров Hadoop в HDInsight][hdinsight-provision-cluster].

## Действие сценария в процессе создания кластера

Действие сценария используется только в том случае, когда кластеры находятся в процессе создания. На следующей схеме показано, когда выполняется действие сценария в процессе создания.

![Настройка кластера HDInsight и этапы создания кластера][img-hdi-cluster-states]

При выполнении сценария кластер переходит к этапу **ClusterCustomization**. На этой стадии скрипт выполняется под учетной записью администратора системы, параллельно на всех указанных узлах в кластере и предоставляет права администратора на узлах в полном объеме.

> [AZURE.NOTE]Так как у вас есть права администратора в узлах кластера на этапе **ClusterCustomization**, вы можете использовать сценарий для выполнения таких операций, как остановка и запуск служб, в том числе служб, связанных с Hadoop. Таким образом, перед завершением работы сценария необходимо запустить службы Ambari и другие службы, связанные с Hadoop. Эти службы нужны для определения работоспособности и состояния кластера при его создании. При изменении любых настроек в кластере, затрагивающих эти службы, необходимо использовать указанные вспомогательные функции. Подробнее о вспомогательных функциях см. в статье [Разработка скриптов действия сценария для HDInsight][hdinsight-write-script].

Выходные данные и журналы ошибок сценария хранятся в учетной записи хранения, заданной по умолчанию для кластера. Журналы хранятся в таблице с именем **u<\\cluster-name-fragment><\\time-stamp>setuplog**. Это сводные журналы сценария, выполняемого на всех узлах (на головном и рабочих) в кластере.

В каждом кластере можно использовать несколько действий сценариев, которые вызываются в том порядке, в котором они указаны. Сценарий может выполняться на головном узле, на рабочем узле или на обоих.

HDInsight предоставляет несколько скриптов для установки следующих компонентов в кластерах HDInsight:

Имя | Скрипт
----- | -----
**Установка Spark** | https://hdiconfigactions.blob.core.windows.net/sparkconfigactionv03/spark-installer-v03.ps1. См. статью [Установка и использование Spark в кластерах HDInsight][hdinsight-install-spark].
**Установка R** | https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1. См. статью [Установка и использование R в кластерах HDInsight][hdinsight-install-r].
**Установка Solr** | https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1. См. статью [Установка и использование Solr в кластерах HDInsight](hdinsight-hadoop-solr-install.md).
**Установка Giraph** | https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1. См. статью [Установка и использование Giraph в кластерах HDInsight](hdinsight-hadoop-giraph-install.md).



## Вызов сценариев с помощью портала предварительной версии Azure

**С помощью портала предварительной версии Azure**

1. Начните создание кластера, как описано в разделе [Создание кластеров Hadoop в HDInsight](hdinsight-provision-clusters.md#portal).
2. В разделе «Необязательная конфигурация» в колонке **Действия сценария** нажмите **Добавить действие сценария**, чтобы указать сведения об этом действии сценария:

	![Использование действия сценария для настройки кластера](./media/hdinsight-hadoop-customize-cluster/HDI.CreateCluster.8.png "Использование действия сценария для настройки кластера")

	<table border='1'>
	<tr><th>Свойство</th><th>Значение</th></tr>
	<tr><td>Имя</td>
		<td>Укажите имя для действия сценария.</td></tr>
	<tr><td>URI-адрес сценария</td>
		<td>Укажите URI для сценария, который вызывается для настройки кластера.</td></tr>
	<tr><td>Головной/рабочий</td>
		<td>Укажите узлы (**Головной** или **Рабочий**), на которых выполняется сценарий настройки.</b>
	<tr><td>Параметры</td>
		<td>Укажите параметры, если они требуются для сценария.</td></tr>
</table>Нажмите клавишу ВВОД, чтобы добавить несколько действий сценария для установки нескольких компонентов в кластере.

3. Щелкните **Выбрать**, чтобы сохранить конфигурацию действия сценария и продолжить создание кластера.

## Вызов сценариев с помощью Azure PowerShell

Этот сценарий PowerShell показывает, как установить Spark в кластере HDInsight под управлением Windows. Чтобы установить другое программное обеспечение, необходимо заменить файл скрипта в сценарии:

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

## Вызов сценариев с помощью пакета SDK для .NET 

В следующем примере показано, как установить Spark на кластер HDInsight под управлением Windows. Чтобы установить другое программное обеспечение, необходимо заменить файл скрипта в коде.

**Создание кластера HDInsight с использованием Spark**

1. Создайте в Visual Studio консольное приложение C#.
2. Введите следующую команду в окне консоли диспетчера пакетов NuGet.

		Install-Package Microsoft.Azure.Management.HDInsight -Pre
		Install-Package Microsoft.Azure.Common.Authentication -Pre

2. Используйте следующие инструкции using в файле Program.cs:

		using System;
		using System.Security;
		using Microsoft.Azure.Management.HDInsight;
		using Microsoft.Azure.Management.HDInsight.Models;
		
		using Microsoft.Azure;
		using Microsoft.Azure.Common.Authentication;
		using Microsoft.Azure.Common.Authentication.Factories;
		using Microsoft.Azure.Common.Authentication.Models;

3. Замените существующий код в файле класса следующим:

        private static HDInsightManagementClient _hdiManagementClient;

        private static Guid SubscriptionId = new Guid("<YourAzureSubscriptionID>");
        private const string ResourceGroupName = "<ExistingAzureResourceGroupName>";
        private const string NewClusterName = "<NewAzureHDInsightClusterName>";
        private const int NewClusterNumNodes = <NumberOfClusterNodes>;
        private const string NewClusterLocation = "East US";
        private const string NewClusterVersion = "3.2";
        private const string ExistingStorageName = "<ExistingAzureStorageAccountName>";
        private const string ExistingStorageKey = "<ExistingAzureStorageAccountKey>";
        private const string ExistingContainer = "<ExistingAzureBlobStorageContainer>";
        private const HDInsightClusterType NewClusterType = HDInsightClusterType.Hadoop;
        private const OSType NewClusterOSType = OSType.Windows;
        private const string NewClusterUsername = "<HttpUserName>";
        private const string NewClusterPassword = "<HttpUserPassword>";

        static void Main(string[] args)
        {
            System.Console.WriteLine("Running");

            var tokenCreds = GetTokenCloudCredentials();
            var subCloudCredentials = GetSubscriptionCloudCredentials(tokenCreds, SubscriptionId);

            _hdiManagementClient = new HDInsightManagementClient(subCloudCredentials);

            CreateCluster();

        }

        private static void CreateCluster()
        {
            var parameters = new ClusterCreateParameters
            {
                ClusterSizeInNodes = NewClusterNumNodes,
                Location = NewClusterLocation,
                ClusterType = NewClusterType,
                OSType = NewClusterOSType,
                Version = NewClusterVersion,

                DefaultStorageAccountName = ExistingStorageName,
                DefaultStorageAccountKey = ExistingStorageKey,
                DefaultStorageContainer = ExistingContainer,

                UserName = NewClusterUsername,
                Password = NewClusterPassword,
            };

            ScriptAction sparkScriptAction = new ScriptAction("Install Spark",
                new Uri("https://hdiconfigactions.blob.core.windows.net/sparkconfigactionv03/spark-installer-v03.ps1"), "");

            parameters.ScriptActions.Add(ClusterNodeType.HeadNode, new System.Collections.Generic.List<ScriptAction> { sparkScriptAction });
            parameters.ScriptActions.Add(ClusterNodeType.WorkerNode, new System.Collections.Generic.List<ScriptAction> { sparkScriptAction });

            _hdiManagementClient.Clusters.Create(ResourceGroupName, NewClusterName, parameters);
        }


        public static SubscriptionCloudCredentials GetTokenCloudCredentials(string username = null, SecureString password = null)
        {
            var authFactory = new AuthenticationFactory();

            var account = new AzureAccount { Type = AzureAccount.AccountType.User };

            if (username != null && password != null)
                account.Id = username;

            var env = AzureEnvironment.PublicEnvironments[EnvironmentName.AzureCloud];

            var accessToken =
                authFactory.Authenticate(account, env, AuthenticationFactory.CommonAdTenant, password, ShowDialog.Auto)
                    .AccessToken;

            return new TokenCloudCredentials(accessToken);
        }

        public static SubscriptionCloudCredentials GetSubscriptionCloudCredentials(SubscriptionCloudCredentials creds, Guid subId)
        {
            return new TokenCloudCredentials(subId.ToString(), ((TokenCloudCredentials)creds).Token);
        }

4. Нажмите клавишу **F5** для запуска приложения.


## Поддержка программного обеспечения с открытым исходным кодом, используемого в кластере HDInsight
Служба Microsoft Azure HDInsight — это гибкая платформа, которая позволяет создавать приложения для работы с данными большого размера в облаке, используя сформированную вокруг Hadoop экосистему технологий с открытым исходным кодом. Microsoft Azure предоставляет общий уровень поддержки для технологий с открытым исходным кодом, как описано в разделе **Объем поддержки** на <a href="http://azure.microsoft.com/support/faq/" target="_blank">веб-сайте часто задаваемых вопросов о поддержке Azure</a>. Служба HDInsight предоставляет дополнительный уровень поддержки для некоторых описанных ниже компонентов.

В службе HDInsight доступно два типа компонентов с открытым исходным кодом.

- **Встроенные компоненты**. Эти компоненты предварительно установлены в кластерах HDInsight и предоставляют его базовые функциональные возможности. Например, к этой категории относится диспетчер ресурсов YARN, язык запросов Hive (HiveQL) и библиотека Mahout. Полный список компонентов кластера доступен в статье <a href="http://azure.microsoft.com/documentation/articles/hdinsight-component-versioning/" target="_blank">Новые возможности версий кластеров Hadoop, предоставляемых HDInsight</a>.
- **Настраиваемые компоненты**. Как пользователь кластера вы можете установить или использовать в рабочей нагрузке любой компонент, полученный из сообщества или созданный самостоятельно.

Встроенные компоненты полностью поддерживаются, и служба поддержки корпорации Майкрософт поможет выявить и устранить проблемы, связанные с этими компонентами.

> [AZURE.WARNING]Компоненты, предоставляемые вместе с кластером HDInsight, поддерживаются в полном объеме. Служба поддержки Майкрософт поможет вам выявить и устранить проблемы, связанные с этими компонентами.
>
> Настраиваемые компоненты получают ограниченную коммерчески оправданную поддержку, способствующую дальнейшей диагностике проблемы. В результате проблема может быть устранена, либо вас могут попросить воспользоваться доступными каналами по технологиям с открытым исходным кодом, чтобы связаться с экспертами в данной области. Можно воспользоваться сайтами сообществ, такими как [форум MSDN по HDInsight](https://social.msdn.microsoft.com/Forums/azure/ru-RU/home?forum=hdinsight) и [http://stackoverflow.com](http://stackoverflow.com). Кроме того, у проектов Apache есть собственные сайты на [http://apache.org](http://apache.org), например у [Hadoop](http://hadoop.apache.org/) и [Spark](http://spark.apache.org/).

Служба HDInsight позволяет использовать настраиваемые компоненты несколькими разными способами. Уровень поддержки не зависит от того, как компонент используется или устанавливается в кластере. Ниже приведен список самых распространенных способов использования настраиваемых компонентов в кластерах HDInsight.

1. Отправка задания. В кластер можно отправлять задания Hadoop или другие типы заданий, выполняющие или использующие настраиваемые компоненты.
2. Настройка кластера. Во время создания кластера можно указать дополнительные параметры и настраиваемые компоненты, которые будут установлены в узлах кластера.
3. Примеры. Для популярных настраиваемых компонентов корпорация Майкрософт и другие компании могут предоставлять примеры использования таких компонентов в кластерах HDInsight. Эти примеры представляются без поддержки.

## Разработка скрипта действия сценария

См. раздел [Разработка скриптов действия сценария для HDInsight][hdinsight-write-script].


## См. также

- В статье [Создание кластеров Hadoop в HDInsight][hdinsight-provision-cluster] приведены указания по созданию кластера HDInsight с использованием других настраиваемых параметров.
- [Разработка скриптов действия сценария для HDInsight][hdinsight-write-script]
- [Установка и использование Spark в кластерах HDInsight][hdinsight-install-spark]
- [Установка и использование R в кластерах HDInsight][hdinsight-install-r]
- [Установка и использование Solr в кластерах HDInsight](hdinsight-hadoop-solr-install.md)
- [Установка и использование Giraph в кластерах HDInsight](hdinsight-hadoop-giraph-install.md)

[hdinsight-install-spark]: hdinsight-hadoop-spark-install.md
[hdinsight-install-r]: hdinsight-hadoop-r-scripts.md
[hdinsight-write-script]: hdinsight-hadoop-script-actions.md
[hdinsight-provision-cluster]: hdinsight-provision-clusters.md
[powershell-install-configure]: ../install-configure-powershell.md


[img-hdi-cluster-states]: ./media/hdinsight-hadoop-customize-cluster/HDI-Cluster-state.png "Этапы создания кластера"

<!---HONumber=Oct15_HO3-->
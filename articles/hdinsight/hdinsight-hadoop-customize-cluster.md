---
title: Настройка кластеров HDInsight с помощью действий скрипта — Azure | Документы Майкрософт
description: Дополнительные сведения о настройке кластеров HDInsight с помощью действия скрипта.
services: hdinsight
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 3a63e216-4163-40c1-aa04-6b42fd0162ad
ms.service: hdinsight
ms.devlang: na
ms.topic: conceptual
ms.date: 10/05/2016
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: 15fa3e7738810ada48f471a685f79a82445ad70c
ms.sourcegitcommit: 6cf20e87414dedd0d4f0ae644696151e728633b6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/06/2018
ms.locfileid: "34808865"
---
# <a name="customize-windows-based-hdinsight-clusters-using-script-action"></a>Настройка кластеров HDInsight под управлением Windows с помощью действия сценария
**действий сценария** можно вызывать [пользовательские сценарии](hdinsight-hadoop-script-actions.md) во время создания кластера для установки в нем дополнительного программного обеспечения.

Информация, приведенная в этой статье, относится только к кластерам HDInsight под управлением Windows. О кластерах под управлением Linux читайте в статье [Настройка кластеров HDInsight под управлением Linux с помощью действия сценария](hdinsight-hadoop-customize-cluster-linux.md).

> [!IMPORTANT]
> Linux — это единственная операционная система, используемая для работы с HDInsight 3.4 или более поздних версий. Дополнительные сведения см. в разделе [Приближается дата прекращения сопровождения HDI версии 3.3](hdinsight-component-versioning.md#hdinsight-windows-retirement).

Кластеры HDInsight можно настраивать множеством других способов, например включая дополнительные учетные записи хранения Azure, изменяя файлы конфигурации Hadoop (core-site.xml, hive-site.xml и т. д.) или добавляя общие библиотеки (например, Hive, Oozie) в стандартные расположения в кластере. Эти настройки можно выполнить с помощью Azure PowerShell, пакета SDK для Azure для HDInsight .NET или на портале Azure. Дополнительные сведения см. в статье [Создание кластеров Hadoop в HDInsight][hdinsight-provision-cluster].

[!INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell-cli-and-dotnet-sdk.md)]

## <a name="script-action-in-the-cluster-creation-process"></a>Действие сценария в процессе создания кластера
Действие сценария используется только в том случае, если кластеры находятся в процессе создания. На следующей схеме показано, когда выполняется действие сценария в процессе создания.

![Настройка кластера HDInsight и этапы создания кластера][img-hdi-cluster-states]

При выполнении сценария кластер переходит к этапу **ClusterCustomization** . На этой стадии скрипт выполняется под учетной записью администратора системы, параллельно на всех указанных узлах в кластере и предоставляет права администратора на узлах в полном объеме.

> [!NOTE]
> Так как у вас есть права администратора в узлах кластера на этапе **ClusterCustomization**, вы можете использовать скрипт для выполнения таких операций, как остановка и запуск служб, в том числе служб, связанных с Hadoop. Таким образом, перед завершением работы сценария необходимо запустить службы Ambari и другие службы, связанные с Hadoop. Эти службы нужны для определения работоспособности и состояния кластера при его создании. При изменении любых настроек в кластере, затрагивающих эти службы, необходимо использовать указанные вспомогательные функции. Подробнее о вспомогательных функциях см. в статье [Разработка скриптов действия сценария для HDInsight][hdinsight-write-script].
>
>

Выходные данные и журналы ошибок сценария хранятся в учетной записи хранения, заданной по умолчанию для кластера. Журналы хранятся в таблице с именем **u<фрагмент-имени-кластера><\метка-времени>setuplog**. Это сводные журналы сценария, выполняемого на всех узлах (на головном и рабочих) в кластере.

В каждом кластере можно использовать несколько действий сценариев, которые вызываются в том порядке, в котором они указаны. Сценарий может выполняться на головном узле, на рабочем узле или на обоих.

HDInsight предоставляет несколько скриптов для установки следующих компонентов в кластерах HDInsight:

| ИМЯ | Скрипт |
| --- | --- |
| **Установка Spark** |https://hdiconfigactions.blob.core.windows.net/sparkconfigactionv03/spark-installer-v03.ps1. Ознакомьтесь со статьей [Установка и использование Spark в кластерах HDInsight Hadoop с помощью действия сценария][hdinsight-install-spark]. |
| **Установка R** |https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1. См. статью [Установка и использование R на кластерах HDInsight Hadoop][hdinsight-install-r]. |
| **Установка Solr** |https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1. Ознакомьтесь со статьей [Установка и использование Solr в кластерах HDInsight](hdinsight-hadoop-solr-install.md). |
| - **Установка Giraph** |https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1. Ознакомьтесь со статьей [Установка и использование Giraph в кластерах HDInsight](hdinsight-hadoop-giraph-install.md). |
| **Предварительная загрузка библиотек Hive** |https://hdiconfigactions.blob.core.windows.net/setupcustomhivelibsv01/setup-customhivelibs-v01.ps1. Ознакомьтесь со статьей [Добавление библиотек Hive в кластеры HDInsight](hdinsight-hadoop-add-hive-libraries.md) |

## <a name="call-scripts-using-the-azure-portal"></a>Вызов сценариев с помощью портала Azure
**На портале Azure**

1. Начните создание кластера, как описано в разделе [Создание кластеров Hadoop под управлением Windows в HDInsight](hdinsight-hadoop-provision-linux-clusters.md).
2. В разделе "Необязательная настройка" в колонке **Действия скрипта** щелкните **Добавить действие скрипта**, чтобы указать сведения об этом действии скрипта.

    ![Использование действия сценария для настройки кластера](./media/hdinsight-hadoop-customize-cluster/HDI.CreateCluster.8.png "Использование действия сценария для настройки кластера")

    <table border='1'>
        <tr><th>Свойство</th><th>Значение</th></tr>
        <tr><td>ИМЯ</td>
            <td>Укажите имя для действия сценария.</td></tr>
        <tr><td>URI-адрес сценария</td>
            <td>Укажите URI для сценария, который вызывается для настройки кластера. s</td></tr>
        <tr><td>Головной/рабочий</td>
            <td>Укажите узлы (**Головной** или **Рабочий**), на которых выполняется скрипт настройки</b>.
        <tr><td>Параметры</td>
            <td>Укажите параметры, если они требуются для сценария.</td></tr>
    </table>

    Нажмите клавишу ВВОД, чтобы добавить несколько действий сценария для установки нескольких компонентов в кластере.
3. Щелкните **Выбрать** , чтобы сохранить конфигурацию действия сценария и продолжить создание кластера.

## <a name="call-scripts-using-azure-powershell"></a>Вызов сценариев с помощью Azure PowerShell
Этот сценарий PowerShell показывает, как установить Spark в кластере HDInsight под управлением Windows.  

    # Provide values for these variables
    $subscriptionID = "<Azure Suscription ID>" # After "Connect-AzureRmAccount", use "Get-AzureRmSubscription" to list IDs.

    $nameToken = "<Enter A Name Token>"  # The token is use to create Azure service names.
    $namePrefix = $nameToken.ToLower() + (Get-Date -Format "MMdd")

    $resourceGroupName = $namePrefix + "rg"
    $location = "EAST US 2" # used for creating resource group, storage account, and HDInsight cluster.

    $hdinsightClusterName = $namePrefix + "spark"
    $httpUserName = "admin"
    $httpPassword = "<Enter a Password>"

    $defaultStorageAccountName = "$namePrefix" + "store"
    $defaultBlobContainerName = $hdinsightClusterName

    #############################################################
    # Connect to Azure
    #############################################################

    Try{
        Get-AzureRmSubscription
    }
    Catch{
        Connect-AzureRmAccount
    }
    Select-AzureRmSubscription -SubscriptionId $subscriptionID

    #############################################################
    # Prepare the dependent components
    #############################################################

    # Create resource group
    New-AzureRmResourceGroup -Name $resourceGroupName -Location $location

    # Create storage account
    New-AzureRmStorageAccount `
        -ResourceGroupName $resourceGroupName `
        -Name $defaultStorageAccountName `
        -Location $location `
        -Type Standard_GRS
    $defaultStorageAccountKey = (Get-AzureRmStorageAccountKey `
                                    -ResourceGroupName $resourceGroupName `
                                    -Name $defaultStorageAccountName)[0].Value
    $defaultStorageAccountContext = New-AzureStorageContext `
                                    -StorageAccountName $defaultStorageAccountName `
                                    -StorageAccountKey $storageAccountKey  
    New-AzureStorageContainer `
        -Name $defaultBlobContainerName `
        -Context $defaultStorageAccountContext

    #############################################################
    # Create cluster with ApacheSpark
    #############################################################

    # Specify the configuration options
    $config = New-AzureRmHDInsightClusterConfig `
                -DefaultStorageAccountName "$defaultStorageAccountName.blob.core.windows.net" `
                -DefaultStorageAccountKey $defaultStorageAccountKey


    # Add a script action to the cluster configuration
    $config = Add-AzureRmHDInsightScriptAction `
                -Config $config `
                -Name "Install Spark" `
                -NodeType HeadNode `
                -Uri https://hdiconfigactions.blob.core.windows.net/sparkconfigactionv03/spark-installer-v03.ps1 `

    # Start creating a cluster with Spark installed
    New-AzureRmHDInsightCluster `
            -ResourceGroupName $resourceGroupName `
            -ClusterName $hdinsightClusterName `
            -Location $location `
            -ClusterSizeInNodes 2 `
            -ClusterType Hadoop `
            -OSType Windows `
            -DefaultStorageContainer $defaultBlobContainerName `
            -Config $config


Чтобы установить другое программное обеспечение, необходимо заменить файл скрипта в сценарии:

При появлении запроса введите учетные данные для кластера. Создание кластера может занять несколько минут.

## <a name="call-scripts-using-net-sdk"></a>Вызов сценариев с помощью пакета SDK для .NET
В следующем примере показано, как установить Spark на кластер HDInsight под управлением Windows. Чтобы установить другое программное обеспечение, необходимо заменить файл скрипта в коде.

**Создание кластера HDInsight с использованием Spark**

1. Создайте в Visual Studio консольное приложение C#.
2. Введите следующую команду в окне консоли диспетчера пакетов NuGet.

        Install-Package Microsoft.Rest.ClientRuntime.Azure.Authentication -Pre
        Install-Package Microsoft.Azure.Management.ResourceManager -Pre
        Install-Package Microsoft.Azure.Management.HDInsight
3. Используйте следующие инструкции using в файле Program.cs:

        using System;
        using System.Security;
        using Microsoft.Azure;
        using Microsoft.Azure.Management.HDInsight;
        using Microsoft.Azure.Management.HDInsight.Models;
        using Microsoft.Azure.Management.ResourceManager;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;
        using Microsoft.Rest;
        using Microsoft.Rest.Azure.Authentication;
4. Замените существующий код в файле класса следующим:

        private static HDInsightManagementClient _hdiManagementClient;

        // Replace with your AAD tenant ID if necessary
        private const string TenantId = UserTokenProvider.CommonTenantId;
        private const string SubscriptionId = "<Your Azure Subscription ID>";
        // This is the GUID for the PowerShell client. Used for interactive logins in this example.
        private const string ClientId = "1950a258-227b-4e31-a9cf-717495945fc2";
        private const string ResourceGroupName = "<ExistingAzureResourceGroupName>";
        private const string NewClusterName = "<NewAzureHDInsightClusterName>";
        private const int NewClusterNumWorkerNodes = 2;
        private const string NewClusterLocation = "East US";
        private const string NewClusterVersion = "3.2";
        private const string ExistingStorageName = "<ExistingAzureStorageAccountName>";
        private const string ExistingStorageKey = "<ExistingAzureStorageAccountKey>";
        private const string ExistingContainer = "<ExistingAzureBlobStorageContainer>";
        private const string NewClusterType = "Hadoop";
        private const OSType NewClusterOSType = OSType.Windows;
        private const string NewClusterUsername = "<HttpUserName>";
        private const string NewClusterPassword = "<HttpUserPassword>";

        static void Main(string[] args)
        {
            System.Console.WriteLine("Running");

            // Authenticate and get a token
            var authToken = Authenticate(TenantId, ClientId, SubscriptionId);
            // Flag subscription for HDInsight, if it isn't already.
            EnableHDInsight(authToken);
            // Get an HDInsight management client
            _hdiManagementClient = new HDInsightManagementClient(authToken);

            CreateCluster();
        }

        private static void CreateCluster()
        {
            var parameters = new ClusterCreateParameters
            {
                ClusterSizeInNodes = NewClusterNumWorkerNodes,
                Location = NewClusterLocation,
                ClusterType = NewClusterType,
                OSType = NewClusterOSType,
                Version = NewClusterVersion,
                DefaultStorageInfo = new AzureStorageInfo(ExistingStorageName, ExistingStorageKey, ExistingContainer),
                UserName = NewClusterUsername,
                Password = NewClusterPassword,
            };

            ScriptAction sparkScriptAction = new ScriptAction("Install Spark",
                new Uri("https://hdiconfigactions.blob.core.windows.net/sparkconfigactionv03/spark-installer-v03.ps1"), "");

            parameters.ScriptActions.Add(ClusterNodeType.HeadNode, new System.Collections.Generic.List<ScriptAction> { sparkScriptAction });
            parameters.ScriptActions.Add(ClusterNodeType.WorkerNode, new System.Collections.Generic.List<ScriptAction> { sparkScriptAction });

            _hdiManagementClient.Clusters.Create(ResourceGroupName, NewClusterName, parameters);
        }

        /// <summary>
        /// Authenticate to an Azure subscription and retrieve an authentication token
        /// </summary>
        /// <param name="TenantId">The AAD tenant ID</param>
        /// <param name="ClientId">The AAD client ID</param>
        /// <param name="SubscriptionId">The Azure subscription ID</param>
        /// <returns></returns>
        static TokenCloudCredentials Authenticate(string TenantId, string ClientId, string SubscriptionId)
        {
            var authContext = new AuthenticationContext("https://login.microsoftonline.com/" + TenantId);
            var tokenAuthResult = authContext.AcquireToken("https://management.core.windows.net/",
                ClientId,
                new Uri("urn:ietf:wg:oauth:2.0:oob"),
                PromptBehavior.Always,
                UserIdentifier.AnyUser);
            return new TokenCloudCredentials(SubscriptionId, tokenAuthResult.AccessToken);
        }
        /// <summary>
        /// Marks your subscription as one that can use HDInsight, if it has not already been marked as such.
        /// </summary>
        /// <remarks>This is essentially a one-time action; if you have already done something with HDInsight
        /// on your subscription, then this isn't needed at all and will do nothing.</remarks>
        /// <param name="authToken">An authentication token for your Azure subscription</param>
        static void EnableHDInsight(TokenCloudCredentials authToken)
        {
            // Create a client for the Resource manager and set the subscription ID
            var resourceManagementClient = new ResourceManagementClient(new TokenCredentials(authToken.Token));
            resourceManagementClient.SubscriptionId = SubscriptionId;
            // Register the HDInsight provider
            var rpResult = resourceManagementClient.Providers.Register("Microsoft.HDInsight");
        }
5. Нажмите клавишу **F5** для запуска приложения.

## <a name="support-for-open-source-software-used-on-hdinsight-clusters"></a>Поддержка программного обеспечения с открытым исходным кодом, используемого в кластере HDInsight
Служба Microsoft Azure HDInsight — это гибкая платформа, которая позволяет создавать приложения для работы с данными большого размера в облаке, используя сформированную вокруг Hadoop экосистему технологий с открытым исходным кодом. Microsoft Azure предоставляет общий уровень поддержки для технологий с открытым исходным кодом, как описано в статье **Объем поддержки** на <a href="http://azure.microsoft.com/support/faq/" target="_blank">веб-сайте часто задаваемых вопросов о поддержке Azure</a>. Служба HDInsight предоставляет дополнительный уровень поддержки для некоторых описанных ниже компонентов.

В службе HDInsight доступно два типа компонентов с открытым исходным кодом.

* **Встроенные компоненты.** Эти компоненты предварительно установлены в кластерах HDInsight и предоставляют его базовые функциональные возможности. Например, к этой категории относится диспетчер ресурсов YARN, язык запросов Hive (HiveQL) и библиотека Mahout. Полный список компонентов кластера доступен в статье [Что представляют собой различные компоненты Hadoop, доступные в HDInsight?](hdinsight-component-versioning.md)</a>
* **Настраиваемые компоненты.** Как пользователь кластера вы можете установить или использовать в рабочей нагрузке любой компонент, полученный из сообщества или созданный самостоятельно.

Встроенные компоненты полностью поддерживаются, и служба поддержки корпорации Майкрософт поможет выявить и устранить проблемы, связанные с этими компонентами.

> [!WARNING]
> Компоненты, предоставляемые вместе с кластером HDInsight, поддерживаются в полном объеме. Служба поддержки Майкрософт поможет вам выявить и устранить проблемы, связанные с этими компонентами.
>
> Настраиваемые компоненты получают ограниченную коммерчески оправданную поддержку, способствующую дальнейшей диагностике проблемы. В результате проблема может быть устранена, либо вас могут попросить воспользоваться доступными каналами по технологиям с открытым исходным кодом, чтобы связаться с экспертами в данной области. Можно использовать ряд сайтов сообществ, например [форум MSDN по HDInsight](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=hdinsight) или [http://stackoverflow.com](http://stackoverflow.com). Кроме того, проекты Apache имеют соответствующие сайты (например, [Hadoop](http://hadoop.apache.org/), [Spark](http://spark.apache.org/)) на сайте [http://apache.org](http://apache.org).
>
>

Служба HDInsight позволяет использовать настраиваемые компоненты несколькими разными способами. Уровень поддержки не зависит от того, как компонент используется или устанавливается в кластере. Ниже приведен список самых распространенных способов использования настраиваемых компонентов в кластерах HDInsight.

1. Отправка задания. В кластер можно отправлять задания Hadoop или другие типы заданий, выполняющие или использующие настраиваемые компоненты.
2. Настройка кластера. Во время создания кластера можно указать дополнительные параметры и настраиваемые компоненты, которые будут установлены в узлах кластера.
3. Примеры. Для популярных настраиваемых компонентов корпорация Майкрософт и другие компании могут предоставлять примеры использования таких компонентов в кластерах HDInsight. Эти примеры представляются без поддержки.

## <a name="develop-script-action-scripts"></a>Разработка скрипта действия сценария
Ознакомьтесь со статьей [Разработка скриптов действия сценария для HDInsight][hdinsight-write-script].

## <a name="see-also"></a>См. также
* В статье [Создание кластеров Hadoop под управлением Windows в HDInsight][hdinsight-provision-cluster] приведены указания по созданию кластера HDInsight с использованием других настраиваемых параметров.
* [Разработка скриптов действия сценария для HDInsight][hdinsight-write-script]
* [Установка и использование Spark в кластерах HDInsight Hadoop с помощью действия сценария][hdinsight-install-spark]
* [Установка и использование Solr в кластерах HDInsight](hdinsight-hadoop-solr-install.md).
* [Установка и использование Giraph в кластерах HDInsight](hdinsight-hadoop-giraph-install.md).

[hdinsight-install-spark]: hdinsight-hadoop-spark-install.md
[hdinsight-write-script]: hdinsight-hadoop-script-actions.md
[hdinsight-provision-cluster]: hdinsight-hadoop-provision-linux-clusters.md
[powershell-install-configure]: /powershell/azureps-cmdlets-docs


[img-hdi-cluster-states]: ./media/hdinsight-hadoop-customize-cluster/HDI-Cluster-state.png "Этапы создания кластера"

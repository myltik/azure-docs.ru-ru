---
title: "Создание кластеров Azure HDInsight (Hadoop) с помощью .NET | Документация Майкрософт"
description: "Узнайте, как создавать кластеры Hadoop, HBase, Storm или Spark на платформе Linux в HDInsight с помощью пакета SDK для HDInsight .NET."
services: hdinsight
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 9c74e3dc-837f-4c90-bbb1-489bc7124a3d
ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 12/19/2016
ms.author: jgao
translationtype: Human Translation
ms.sourcegitcommit: bb700c7de96712666bc4be1f8e430a2e94761f69
ms.openlocfilehash: cd5ab90167501d5876381e0575042e19a23e702c


---
# <a name="create-linux-based-clusters-in-hdinsight-using-the-net-sdk"></a>Создание кластеров под управлением Linux в HDInsight с помощью пакета SDK для .NET
[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Пакет SDK для HDInsight .NET предоставляет клиентские библиотеки .NET, которые упрощают работу с кластерами HDInsight из приложения .NET Framework. В этой статье показано, как создать кластер HDInsight под управлением Linux с помощью пакета SDK для .NET.

> [!IMPORTANT]
> Действия, описанные в этом документе, позволяют создать кластер с одним узлом рабочей роли. Если вы планируете использовать более 32 рабочих узлов (при создании кластера или в ходе масштабирования после создания кластера), для головного узла потребуется минимум 8-ядерный процессор и 14 ГБ ОЗУ.
> 
> Дополнительные сведения о размерах узлов и их стоимости см. на странице с [ценами на HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).
> 
> 

## <a name="prerequisites"></a>Предварительные требования
[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

* **Подписка Azure**. Ознакомьтесь с [бесплатной пробной версией Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* **Учетная запись хранения Azure.** Ознакомьтесь со статьей [Создание учетной записи хранения](../storage/storage-create-storage-account.md#create-a-storage-account).
* **Visual Studio 2013 или 2015**.

### <a name="access-control-requirements"></a>Требования к контролю доступа
[!INCLUDE [access-control](../../includes/hdinsight-access-control-requirements.md)]

## <a name="create-clusters"></a>Создание кластеров
1. Откройте Visual Studio 2013 или 2015.
2. Создайте новый проект Visual Studio со следующими параметрами.
   
   | Свойство | Значение |
   | --- | --- |
   | Шаблон |Templates/Visual C#/Windows/Console Application |
   | Имя |CreateHDICluster |
3. В меню **Средства** щелкните **Диспетчер пакетов Nuget**, а затем щелкните **Консоль диспетчера пакетов**.
4. Чтобы установить пакеты, выполните в консоли следующую команду:
   
        Install-Package Microsoft.Rest.ClientRuntime.Azure.Authentication -Pre
        Install-Package Microsoft.Azure.Management.ResourceManager -Pre
        Install-Package Microsoft.Azure.Management.HDInsight
   
    Эти команды добавляют библиотеки .NET и ссылки на них в текущий проект Visual Studio.
5. В обозревателе решений дважды щелкните файл **Program.cs** , чтобы открыть его, вставьте указанный ниже код и укажите значения для переменных.
   
        using System;
        using Microsoft.Rest;
        using Microsoft.Rest.Azure.Authentication;
        using Microsoft.Azure;
        using Microsoft.Azure.Management.HDInsight;
        using Microsoft.Azure.Management.HDInsight.Models;
        using Microsoft.Azure.Management.ResourceManager;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;
   
        namespace CreateHDInsightCluster
        {
            class Program
            {
                private static HDInsightManagementClient _hdiManagementClient;
   
                private const string SubscriptionId = "<Your Azure Subscription ID>";
                // Replace with your AAD tenant ID if necessary
                private const string TenantId = UserTokenProvider.CommonTenantId; 
                // This is the GUID for the PowerShell client. Used for interactive logins in this example.
                private const string ClientId = "1950a258-227b-4e31-a9cf-717495945fc2";
   
                private const string ExistingResourceGroupName = "<Enter Resource Group Name>";
                private const string ExistingStorageName = "<Enter Default Storage Account Name>.blob.core.windows.net";
                private const string ExistingStorageKey = "<Enter Default Storage Account Key>";
                private const string ExistingBlobContainer = "<Enter Default Bob Container Name>";

                private const string NewClusterName = "<Enter HDInsight Cluster Name>";
                private const int NewClusterNumNodes = 2;
                private const string NewClusterLocation = "EAST US 2";     // Must be the same as the default Storage account
                private const OSType NewClusterOSType = OSType.Linux;
                private const string NewClusterType = "Hadoop";
                private const string NewClusterVersion = "3.4";
                private const string NewClusterUsername = "admin";
                private const string NewClusterPassword = "<Enter HTTP User Password>";
                private const string NewClusterSshUserName = "sshuser";

                // You can use eitehr password or public key.  See https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-windows or https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix
                private const string NewClusterSshPassword = "<Enter SSH User Password>";
                private const string NewClusterSshPublicKey = @"---- BEGIN SSH2 PUBLIC KEY ----
                    Comment: ""rsa-key-20150731""
                    AAAAB3NzaC1yc2EAAAABJQAAAQEA4QiCRLqT7fnmUA5OhYWZNlZo6lLaY1c+IRsp
                    gmPCsJVGQLu6O1wqcxRqiKk7keYq8bP5s30v6bIljsLZYTnyReNUa5LtFw7eauGr
                    yVt3Pve6ejfWELhbVpi0iq8uJNFA9VvRkz8IP1JmjC5jsdnJhzQZtgkIrdn3w0e6
                    WVfu15kKyY8YAiynVbdV51EB0SZaSLdMZkZQ81xi4DDtCZD7qvdtWEFwLa+EHdkd
                    pzO36Mtev5XvseLQqzXzZ6aVBdlXoppGHXkoGHAMNOtEWRXpAUtEccjpATsaZhQR
                    zZdZlzHduhM10ofS4YOYBADt9JohporbQVHM5w6qUhIgyiPo7w==
                    ---- END SSH2 PUBLIC KEY ----"; //replace the public key with your own
   
                static void Main(string[] args)
                {
                    System.Console.WriteLine("Creating a cluster.  The process takes 10 to 20 minutes ...");
   
                    // Authenticate and get a token
                    var authToken = GetTokenCloudCredentials(TenantId, ClientId, SubscriptionId);
                    // Flag subscription for HDInsight, if it isn't already.
                    EnableHDInsight(authToken);
                    // Get an HDInsight management client
                    _hdiManagementClient = new HDInsightManagementClient(authToken);
   
                    // Set parameters for the new cluster
                    var parameters = new ClusterCreateParameters
                    {
                        ClusterSizeInNodes = NewClusterNumNodes,
                        UserName = NewClusterUsername,
                        ClusterType = NewClusterType,
                        OSType = NewClusterOSType,
                        Version = NewClusterVersion,
   
                        // Use an Azure storage account as the default storage
                        DefaultStorageInfo = new AzureStorageInfo(ExistingStorageName, ExistingStorageKey, ExistingBlobContainer),

                        Password = NewClusterPassword,
                        Location = NewClusterLocation,
   
                        SshUserName = NewClusterSshUserName,
                        SshPassword = NewClusterSshPassword,
                        //SshPublicKey = NewClusterSshPublicKey
                    };
                    // Create the cluster
                    _hdiManagementClient.Clusters.Create(ExistingResourceGroupName, NewClusterName, parameters);
   
                    System.Console.WriteLine("The cluster has been created. Press ENTER to continue ...");
                    System.Console.ReadLine();
                }
   
                /// <summary>
                /// Authenticate to an Azure subscription and retrieve an authentication token
                /// </summary>
                static TokenCloudCredentials GetTokenCloudCredentials(string TenantId, string ClientId, string SubscriptionId)
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
            }
        }
6. Замените значения членов класса.
7. Нажмите клавишу **F5** для запуска приложения. Должно открыться окно консоли, в котором отображается состояние приложения. Появится запрос на ввод учетных данных учетной записи Azure. На подготовку кластера HDInsight может уйти несколько минут (обычно около 15).

## <a name="use-bootstrap"></a>Использование начальной загрузки

С помощью начальной загрузки можно настроить дополнительные параметры во время создания кластеров.  Подробные сведения см. в статье [Настройка кластеров HDInsight с помощью начальной загрузки](hdinsight-hadoop-customize-cluster-bootstrap.md).

Измените пример в разделе [Создание кластеров](#create-clusters) для настройки параметра Hive.

    static void Main(string[] args)
    {
        System.Console.WriteLine("Creating a cluster.  The process takes 10 to 20 minutes ...");

        // Authenticate and get a token
        var authToken = GetTokenCloudCredentials(TenantId, ClientId, SubscriptionId);
        // Flag subscription for HDInsight, if it isn't already.
        EnableHDInsight(authToken);
        // Get an HDInsight management client
        _hdiManagementClient = new HDInsightManagementClient(authToken);

        // Set parameters for the new cluster
        var extendedParameters = new ClusterCreateParametersExtended
        {
            Location = NewClusterLocation,
            Properties = new ClusterCreateProperties
            {
                ClusterDefinition = new ClusterDefinition
                {
                    ClusterType = NewClusterType.ToString()
                },
                ClusterVersion = NewClusterVersion,
                OperatingSystemType = NewClusterOSType
            }
        };

        var coreConfigs = new Dictionary<string, string>
        {
            {"fs.defaultFS", string.Format("wasbs://{0}@{1}", ExistingBlobContainer, ExistingStorageName)},
            {
                string.Format("fs.azure.account.key.{0}", ExistingStorageName),
                ExistingStorageKey
            }
        };

        // bootstrap
        var hiveConfigs = new Dictionary<string, string>
        {
            { "hive.metastore.client.socket.timeout", "90"}
        };

        var gatewayConfigs = new Dictionary<string, string>
        {
            {"restAuthCredential.isEnabled", "true"},
            {"restAuthCredential.username", NewClusterUsername},
            {"restAuthCredential.password", NewClusterPassword}
        };

        var configurations = new Dictionary<string, Dictionary<string, string>>
        {
            {"core-site", coreConfigs},
            {"gateway", gatewayConfigs},
            {"hive-site", hiveConfigs}
        };

        var serializedConfig = JsonConvert.SerializeObject(configurations);
        extendedParameters.Properties.ClusterDefinition.Configurations = serializedConfig;

        var sshPublicKeys = new List<SshPublicKey>();
        var sshPublicKey = new SshPublicKey
        {
            CertificateData =
                string.Format("ssh-rsa {0}", NewClusterSshPublicKey)
        };
        sshPublicKeys.Add(sshPublicKey);

        var headNode = new Role
        {
            Name = "headnode",
            TargetInstanceCount = 2,
            HardwareProfile = new HardwareProfile
            {
                VmSize = "Large"
            },
            OsProfile = new OsProfile
            {
                LinuxOperatingSystemProfile = new LinuxOperatingSystemProfile
                {
                    UserName = NewClusterSshUserName,
                    Password = NewClusterSshPassword //,
                    // When use a SSH pulbic key, make sure to remove comments, headers and trailers, and concatenate the key into one line 
                    //SshProfile = new SshProfile
                    //{
                    //    SshPublicKeys = sshPublicKeys
                    //}
                }
            }
        };

        var workerNode = new Role
        {
            Name = "workernode",
            TargetInstanceCount = NewClusterNumNodes,
            HardwareProfile = new HardwareProfile
            {
                VmSize = "Large"
            },
            OsProfile = new OsProfile
            {
                LinuxOperatingSystemProfile = new LinuxOperatingSystemProfile
                {
                    UserName = NewClusterSshUserName,
                    Password = NewClusterSshPassword //,
                    //SshProfile = new SshProfile
                    //{
                    //    SshPublicKeys = sshPublicKeys
                    //}
                }
            }
        };

        extendedParameters.Properties.ComputeProfile = new ComputeProfile();
        extendedParameters.Properties.ComputeProfile.Roles.Add(headNode);
        extendedParameters.Properties.ComputeProfile.Roles.Add(workerNode);

        _hdiManagementClient.Clusters.Create(ExistingResourceGroupName, NewClusterName, extendedParameters);

        System.Console.WriteLine("The cluster has been created. Press ENTER to continue ...");
        System.Console.ReadLine();
    }


## <a name="use-script-action"></a>Использование действия сценария

С помощью действия сценария можно настроить дополнительные параметры при создании кластеров.  Дополнительные сведения см. в статье [Настройка кластеров HDInsight под управлением Linux с помощью действия сценария](hdinsight-hadoop-customize-cluster-linux.md).

Измените пример в разделе [Создание кластеров](#create-clusters) для вызова действия сценария для установки R.

    static void Main(string[] args)
    {
        System.Console.WriteLine("Creating a cluster.  The process takes 10 to 20 minutes ...");

        // Authenticate and get a token
        var authToken = GetTokenCloudCredentials(TenantId, ClientId, SubscriptionId);
        // Flag subscription for HDInsight, if it isn't already.
        EnableHDInsight(authToken);
        // Get an HDInsight management client
        _hdiManagementClient = new HDInsightManagementClient(authToken);

        // Set parameters for the new cluster
        var parameters = new ClusterCreateParameters
        {
            ClusterSizeInNodes = NewClusterNumNodes,
            Location = NewClusterLocation,
            ClusterType = NewClusterType,
            OSType = NewClusterOSType,
            Version = NewClusterVersion,

            DefaultStorageInfo = new AzureStorageInfo(ExistingStorageName, ExistingStorageKey, ExistingBlobContainer),

            UserName = NewClusterUsername,
            Password = NewClusterPassword,
            SshUserName = NewClusterSshUserName,
            SshPublicKey = NewClusterSshPublicKey
        };

        ScriptAction rScriptAction = new ScriptAction("Install R",
            new Uri("https://hdiconfigactions.blob.core.windows.net/linuxrconfigactionv01/r-installer-v01.sh"), "");

        parameters.ScriptActions.Add(ClusterNodeType.HeadNode,new System.Collections.Generic.List<ScriptAction> { rScriptAction});
        parameters.ScriptActions.Add(ClusterNodeType.WorkerNode, new System.Collections.Generic.List<ScriptAction> { rScriptAction });

        _hdiManagementClient.Clusters.Create(ExistingResourceGroupName, NewClusterName, parameters);

        System.Console.WriteLine("The cluster has been created. Press ENTER to continue ...");
        System.Console.ReadLine();
    }

## <a name="next-steps"></a>Дальнейшие действия
Теперь, когда вы успешно создали кластер HDInsight, обратитесь к следующим статьям, чтобы научиться работать с кластером. 

### <a name="hadoop-clusters"></a>Кластеры Hadoop
* [Использование Hive с HDInsight](hdinsight-use-hive.md)
* [Использование Pig с HDInsight](hdinsight-use-pig.md)
* [Использование MapReduce с HDInsight](hdinsight-use-mapreduce.md)

### <a name="hbase-clusters"></a>Кластеры HBase
* [Начало работы с HBase в HDInsight](hdinsight-hbase-tutorial-get-started-linux.md)
* [Разработка приложений Java для HBase в HDInsight](hdinsight-hbase-build-java-maven-linux.md)

### <a name="storm-clusters"></a>Кластеры Storm
* [Разработка приложений Java для Storm в HDInsight](hdinsight-storm-develop-java-topology.md)
* [Использование компонентов Python в Storm в HDInsight](hdinsight-storm-develop-python-topology.md)
* [Развертывание и мониторинг топологий с помощью Storm в HDInsight](hdinsight-storm-deploy-monitor-topology-linux.md)

### <a name="spark-clusters"></a>Кластеры Spark
* [Создание автономного приложения с использованием Scala](hdinsight-apache-spark-create-standalone-application.md)
* [Удаленный запуск заданий с помощью Livy в кластере Spark](hdinsight-apache-spark-livy-rest-interface.md)
* [Использование Spark со средствами бизнес-аналитики. Выполнение интерактивного анализа данных с использованием Spark в HDInsight с помощью средств бизнес-аналитики](hdinsight-apache-spark-use-bi-tools.md)
* [Использование Spark с машинным обучением. Использование Spark в HDInsight для прогнозирования результатов контроля качества пищевых продуктов](hdinsight-apache-spark-machine-learning-mllib-ipython.md)
* [Потоковая передача Spark. Использование Spark в HDInsight для сборки приложений потоковой передачи данных в режиме реального времени](hdinsight-apache-spark-eventhub-streaming.md)

### <a name="run-jobs"></a>Выполнение заданий
* [Выполнение заданий Hive в HDInsight с помощью пакета SDK для .NET](hdinsight-hadoop-use-hive-dotnet-sdk.md)
* [Выполнение заданий Pig в HDInsight с помощью пакета SDK для .NET](hdinsight-hadoop-use-pig-dotnet-sdk.md)
* [Выполнение заданий Sqoop в HDInsight с помощью пакета SDK для .NET](hdinsight-hadoop-use-sqoop-dotnet-sdk.md)
* [Запуск заданий Oozie в HDInsight](hdinsight-use-oozie.md)




<!--HONumber=Jan17_HO4-->



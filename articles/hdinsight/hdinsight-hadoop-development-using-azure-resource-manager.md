---
title: "Переход к средствам Azure Resource Manager для HDInsight | Документация Майкрософт"
description: "Процесс перехода к средствам разработки на основе Azure Resource Manager для кластеров HDInsight"
services: hdinsight
editor: cgronlun
manager: jhubbard
author: nitinme
documentationcenter: 
ms.assetid: 05efedb5-6456-4552-87ff-156d77fbe2e1
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/28/2017
ms.author: nitinme
ms.translationtype: Human Translation
ms.sourcegitcommit: bb700c7de96712666bc4be1f8e430a2e94761f69
ms.openlocfilehash: 3606df64b619b62f8b9e5aec2abc4efc994c37e3
ms.contentlocale: ru-ru
ms.lasthandoff: 01/24/2017

---
# <a name="migrating-to-azure-resource-manager-based-development-tools-for-hdinsight-clusters"></a>Переход к средствам разработки на основе Azure Resource Manager для кластеров HDInsight

Средства для HDInsight на основе диспетчера служб Azure (ASM) устарели. Если для работы с кластерами HDInsight вы используете Azure PowerShell, интерфейс командной строки Azure или пакет SDK для HDInsight .NET, рекомендуем перейти на версии PowerShell, интерфейса командной строки и пакета SDK для .NET, основанные на диспетчере ресурсов Azure (ARM). В этой статье рассказывается, как перейти на средства, основанные на ARM. Кроме того, в этой статье указаны различия (если таковые имеются) между методами работы с HDInsight, основанными на ASM и ARM.

> [!IMPORTANT]
> Поддержка PowerShell, интерфейса командной строки и пакета SDK для .NET на основе ASM будет прекращена **1 января 2017 года**.
> 
> 

## <a name="migrating-azure-cli-to-azure-resource-manager"></a>Переход с Azure CLI на диспетчер ресурсов Azure
Теперь интерфейс командной строки Azure по умолчанию работает в режиме диспетчера ресурсов Azure (ARM), если только предыдущая установка не была обновлена — в этом случае для переключения в режим ARM нужно использовать команду `azure config mode arm` .

Интерфейс командной строки Azure предоставляет такие же команды для работы с HDInsight с использованием функции управления службами Azure (ASM), как при использовании ARM, однако некоторые параметры и переключатели могут иметь различные имена, а при использовании ARM доступно множество новых параметров. Например, теперь указать виртуальную сеть Azure, в которой можно создать кластер, либо сведения о метахранилище Hive/Oozie можно с помощью команды `azure hdinsight cluster create` .

Вот основные команды для работы с HDInsight с использованием диспетчера ресурсов Azure:

* `azure hdinsight cluster create` — создает кластер HDInsight;
* `azure hdinsight cluster delete` — удаляет кластер HDInsight;
* `azure hdinsight cluster show` — отображает информацию о существующем кластере;
* `azure hdinsight cluster list` — выдает список кластеров HDInsight для подписки Azure.

Проверить, какие параметры и переключатели доступны для каждой команды, позволяет переключатель `-h` .

### <a name="new-commands"></a>Новые команды
В диспетчере ресурсов Azure появились следующие команды:

* `azure hdinsight cluster resize` — динамически изменяет количество рабочих узлов в кластере;
* `azure hdinsight cluster enable-http-access` — обеспечивает HTTPs-доступ к кластеру (по умолчанию включен);
* `azure hdinsight cluster disable-http-access` — отключает HTTPs-доступ к кластеру;
* `azure hdinsight script-action` — предоставляет команды для создания действий сценариев в кластере и управления этими действиями;
* `azure hdinsight config` — предоставляет команды для создания файла конфигурации, который можно использовать с командой `hdinsight cluster create` для предоставления сведений о конфигурации.

### <a name="deprecated-commands"></a>Устаревшие команды
Если для отправки заданий в кластер HDInsight используются команды `azure hdinsight job`, они не будут доступны для команд ARM. Если задания из сценариев необходимо отправлять в HDInsight программными средствами, используйте API REST, предоставляемый в HDInsight. Дополнительные сведения об отправке заданий с использованием API REST см. в следующих документах:

* [Выполнение заданий MapReduce с помощью cURL с использованием Hadoop в HDInsight](hdinsight-hadoop-use-mapreduce-curl.md)
* [Выполнение запросов Hive с помощью cURL с использованием Hadoop в HDInsight](hdinsight-hadoop-use-hive-curl.md)
* [Выполнение запросов Pig с помощью cURL с использованием Hadoop в HDInsight](hdinsight-hadoop-use-pig-curl.md)

Сведения о других способах интерактивного запуска MapReduce, Hive и Pig см. в статьях [Использование MapReduce в Hadoop в HDInsight](hdinsight-use-mapreduce.md), [Использование Hive и HiveQL с Hadoop в HDInsight для анализа примера файла Apache log4j](hdinsight-use-hive.md) и [Использование Pig с Hadoop в HDInsight](hdinsight-use-pig.md).

### <a name="examples"></a>Примеры
**Создание кластера**

* Старая команда (ASM) — `azure hdinsight cluster create myhdicluster --location northeurope --osType linux --storageAccountName mystorage --storageAccountKey <storagekey> --storageContainer mycontainer --userName admin --password mypassword --sshUserName sshuser --sshPassword mypassword`
* Новая команда (ASM) — `azure hdinsight cluster create myhdicluster -g myresourcegroup --location northeurope --osType linux --clusterType hadoop --defaultStorageAccountName mystorage --defaultStorageAccountKey <storagekey> --defaultStorageContainer mycontainer --userName admin -password mypassword --sshUserName sshuser --sshPassword mypassword`

**Удаление кластера**

* Старая команда (ASM) — `azure hdinsight cluster delete myhdicluster`
* Новая команда (ASM) — `azure hdinsight cluster delete mycluster -g myresourcegroup`

**Получение списка кластеров**

* Старая команда (ASM) — `azure hdinsight cluster list`
* Новая команда (ASM) — `azure hdinsight cluster list`

> [!NOTE]
> Для команды списка при добавлении `-g` к группе ресурсов возвращаются только кластеры, входящие в указанную группу ресурсов.
> 
> 

**Отображение данных кластера**

* Старая команда (ASM) — `azure hdinsight cluster show myhdicluster`
* Новая команда (ASM) — `azure hdinsight cluster show myhdicluster -g myresourcegroup`

## <a name="migrating-azure-powershell-to-azure-resource-manager"></a>Переход с Azure PowerShell на диспетчер ресурсов Azure
Общие сведения об Azure PowerShell в режиме Azure Resource Manager (ARM) см. в [этой статье](../powershell-azure-resource-manager.md).

Командлеты ARM для Azure PowerShell могут устанавливаться параллельно с командлетами ASM. Командлеты двух режимов можно различать по именам.  В режиме ARM имена командлетов содержат *AzureRmHDInsight*, а в режиме ASM — *AzureHDInsight*.  Например, *New-AzureRmHDInsightCluster* или *New-AzureHDInsightCluster*. Параметры и переключатели могут иметь новые имена, а при использовании ARM появляется доступ к множеству новых параметров.  Например, для некоторых командлетов требуется новый переключатель *-ResourceGroupName*. 

Перед использованием командлетов HDInsight необходимо подключиться к учетной записи Azure и создать новую группу ресурсов:

* Login-AzureRmAccount или [Select-AzureRmProfile](https://msdn.microsoft.com/library/mt619310.aspx). См. статью о [проверке подлинности субъекта-службы в Azure Resource Manager](../azure-resource-manager/resource-group-authenticate-service-principal.md).
* [New-AzureRmResourceGroup](https://msdn.microsoft.com/library/mt603739.aspx)

### <a name="renamed-cmdlets"></a>Переименованные командлеты
Получение списка командлетов HDInsight ASM в консоли Windows PowerShell:

    help *azurermhdinsight*

В следующей таблице перечислены командлеты ASM и их имена в режиме ARM:

| Командлеты ASM | Командлеты ARM |
| --- | --- |
| Add-AzureHDInsightConfigValues |[Add-AzureRmHDInsightConfigValues](https://msdn.microsoft.com/library/mt603530.aspx) |
| Add-AzureHDInsightMetastore |[Add-AzureRmHDInsightMetastore](https://msdn.microsoft.com/library/mt603670.aspx) |
| Add-AzureHDInsightScriptAction |[Add-AzureRmHDInsightScriptAction](https://msdn.microsoft.com/library/mt603527.aspx) |
| Add-AzureHDInsightStorage |[Add-AzureRmHDInsightStorage](https://msdn.microsoft.com/library/mt619445.aspx) |
| Get-AzureHDInsightCluster |[Get-AzureRmHDInsightCluster](https://msdn.microsoft.com/library/mt619371.aspx) |
| Get-AzureHDInsightJob |[Get-AzureRmHDInsightJob](https://msdn.microsoft.com/library/mt603590.aspx) |
| Get-AzureHDInsightJobOutput |[Get-AzureRmHDInsightJobOutput](https://msdn.microsoft.com/library/mt603793.aspx) |
| Get-AzureHDInsightProperties |[Get-AzureRmHDInsightProperties](https://msdn.microsoft.com/library/mt603546.aspx) |
| Grant-AzureHDInsightHttpServicesAccess |[Grant-AzureRmHDInsightHttpServicesAccess](https://msdn.microsoft.com/library/mt619407.aspx) |
| Grant-AzureHdinsightRdpAccess |[Grant-AzureRmHDInsightRdpServicesAccess](https://msdn.microsoft.com/library/mt603717.aspx) |
| Invoke-AzureHDInsightHiveJob |[Invoke-AzureRmHDInsightHiveJob](https://msdn.microsoft.com/library/mt603593.aspx) |
| New-AzureHDInsightCluster |[New-AzureRmHDInsightCluster](https://msdn.microsoft.com/library/mt619331.aspx) |
| New-AzureHDInsightClusterConfig |[New-AzureRmHDInsightClusterConfig](https://msdn.microsoft.com/library/mt603700.aspx) |
| New-AzureHDInsightHiveJobDefinition |[New-AzureRmHDInsightHiveJobDefinition](https://msdn.microsoft.com/library/mt619448.aspx) |
| New-AzureHDInsightMapReduceJobDefinition |[New-AzureRmHDInsightMapReduceJobDefinition](https://msdn.microsoft.com/library/mt603626.aspx) |
| New-AzureHDInsightPigJobDefinition |[New-AzureRmHDInsightPigJobDefinition](https://msdn.microsoft.com/library/mt603671.aspx) |
| New-AzureHDInsightSqoopJobDefinition |[New-AzureRmHDInsightSqoopJobDefinition](https://msdn.microsoft.com/library/mt608551.aspx) |
| New-AzureHDInsightStreamingMapReduceJobDefinition |[New-AzureRmHDInsightStreamingMapReduceJobDefinition](https://msdn.microsoft.com/library/mt603626.aspx) |
| Remove-AzureHDInsightCluster |[Remove-AzureRmHDInsightCluster](https://msdn.microsoft.com/library/mt619431.aspx) |
| Revoke-AzureHDInsightHttpServicesAccess |[Revoke-AzureRmHDInsightHttpServicesAccess](https://msdn.microsoft.com/library/mt619375.aspx) |
| Revoke-AzureHdinsightRdpAccess |[Revoke-AzureRmHDInsightRdpServicesAccess](https://msdn.microsoft.com/library/mt603523.aspx) |
| Set-AzureHDInsightClusterSize |[Set-AzureRmHDInsightClusterSize](https://msdn.microsoft.com/library/mt603513.aspx) |
| Set-AzureHDInsightDefaultStorage |[Set-AzureRmHDInsightDefaultStorage](https://msdn.microsoft.com/library/mt603486.aspx) |
| Start-AzureHDInsightJob |[Start-AzureRmHDInsightJob](https://msdn.microsoft.com/library/mt603798.aspx) |
| Stop-AzureHDInsightJob |[Stop-AzureRmHDInsightJob](https://msdn.microsoft.com/library/mt619424.aspx) |
| Use-AzureHDInsightCluster |[Use-AzureRmHDInsightCluster](https://msdn.microsoft.com/library/mt619442.aspx) |
| Wait-AzureHDInsightJob |[Wait-AzureRmHDInsightJob](https://msdn.microsoft.com/library/mt603834.aspx) |

### <a name="new-cmdlets"></a>Новые командлеты
Ниже перечислены новые командлеты, доступные только в режиме ARM. 

**Командлеты, связанные с действиями сценариев:**

* **Get AzureRmHDInsightPersistedScriptAction**: возвращает список сохраняемых действий сценария для кластера, упорядоченный в хронологическом порядке, или получает сведения об указанном сохраняемом действии сценария. 
* **Get AzureRmHDInsightScriptActionHistory**: возвращает журнал действий сценария для кластера, перечисленных в обратном хронологическом порядке, или получает сведения о действии сценария, выполненном ранее. 
* **Remove-AzureRmHDInsightPersistedScriptAction**: удаляет из кластера HDInsight сохраняемое действие сценария.
* **Set-AzureRmHDInsightPersistedScriptAction**: назначает выполненное ранее действие сценария сохраняемым действием сценария.
* **Submit-AzureRmHDInsightScriptAction**: отправляет новое действие сценария в кластер Azure HDInsight. 

Дополнительные сведения об использовании см. в статье [Настройка кластеров HDInsight под управлением Linux с помощью действия сценария](hdinsight-hadoop-customize-cluster-linux.md).

**Командлеты, связанные с удостоверениями кластеров:**

* **Add-AzureRmHDInsightClusterIdentity**: добавляет идентификатор кластера в объект конфигурации кластера, чтобы кластер HDInsight мог получать доступ к Azure Data Lake Store. См. статью [Создание кластера HDInsight с хранилищем озера данных с помощью Azure PowerShell](../data-lake-store/data-lake-store-hdinsight-hadoop-use-powershell.md).

### <a name="examples"></a>Примеры
**Создать кластер**

Старая команда (ASM): 

    New-AzureHDInsightCluster `
        -Name $clusterName `
        -Location $location `
        -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" `
        -DefaultStorageAccountKey $storageAccountKey `
        -DefaultStorageContainerName $containerName `
        -ClusterSizeInNodes 2 `
        -ClusterType Hadoop `
        -OSType Linux `
        -Version "3.2" `
        -Credential $httpCredential `
        -SshCredential $sshCredential

Новая команда (ASM):

    New-AzureRmHDInsightCluster `
        -ClusterName $clusterName `
        -ResourceGroupName $resourceGroupName `
        -Location $location `
        -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" `
        -DefaultStorageAccountKey $storageAccountKey `
        -DefaultStorageContainer $containerName  `
        -ClusterSizeInNodes 2 `
        -ClusterType Hadoop `
        -OSType Linux `
        -Version "3.2" `
        -HttpCredential $httpcredentials `
        -SshCredential $sshCredentials


**Удалить кластер**

Старая команда (ASM):

    Remove-AzureHDInsightCluster -name $clusterName 

Новая команда (ASM):

    Remove-AzureRmHDInsightCluster -ResourceGroupName $resourceGroupName -ClusterName $clusterName 

**Список кластеров**

Старая команда (ASM):

    Get-AzureHDInsightCluster

Новая команда (ASM):

    Get-AzureRmHDInsightCluster 

**Отображение кластеров**

Старая команда (ASM):

    Get-AzureHDInsightCluster -Name $clusterName

Новая команда (ASM):

    Get-AzureRmHDInsightCluster -ResourceGroupName $resourceGroupName -clusterName $clusterName


#### <a name="other-samples"></a>Другие примеры
* [Создание кластеров Hadoop в HDInsight](hdinsight-hadoop-create-linux-clusters-azure-powershell.md)
* [Отправка заданий Hive](hdinsight-hadoop-use-hive-powershell.md)
* [Отправка заданий Pig](hdinsight-hadoop-use-pig-powershell.md)
* [Отправка заданий Sqoop](hdinsight-hadoop-use-sqoop-powershell.md)

## <a name="migrating-to-the-arm-based-hdinsight-net-sdk"></a>Переход на пакет SDK для HDInsight .NET на основе ARM
[Пакет SDK для HDInsight .NET (ASM)](https://msdn.microsoft.com/library/azure/mt416619.aspx) на основе управления службами Azure устарел. Рекомендуется использовать [пакет SDK для HDInsight .NET (ARM)](https://msdn.microsoft.com/library/azure/mt271028.aspx)на основе управления ресурсами Azure. Следующие пакеты HDInsight на основе ASM устарели:

* `Microsoft.WindowsAzure.Management.HDInsight`
* `Microsoft.Hadoop.Client`

Этот раздел содержит ссылки на дополнительные сведения о выполнении определенных задач с использованием пакета SDK на основе ARM.

| Как использовать пакет SDK для HDInsight на основе ARM | Ссылки |
| --- | --- |
| Создание кластеров HDInsight с помощью пакета SDK для .NET |См. статью [Создание кластеров под управлением Linux в HDInsight с помощью пакета SDK для .NET](hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md). |
| Настройка кластера с помощью действия сценария и пакета SDK для .NET |См. статью [Настройка кластеров HDInsight под управлением Linux с помощью действия сценария](hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md#use-script-action). |
| Интерактивная проверка подлинности приложений с Azure Active Directory c использованием пакета SDK для .NET |См. статью [Выполнение запросов Hive с помощью пакета SDK HDInsight для .NET](hdinsight-hadoop-use-hive-dotnet-sdk.md). Во фрагменте кода, представленном в этой статье, используется метод интерактивной проверки подлинности. |
| Неинтерактивная проверка подлинности приложений с Azure Active Directory c использованием пакета SDK для .NET |См. статью [Создание приложений .NET HDInsight с неинтерактивной проверкой подлинности](hdinsight-create-non-interactive-authentication-dotnet-applications.md). |
| Отправка задания Hive с помощью пакета SDK для .NET |См. раздел [Отправка запросов Hive с помощью пакета SDK HDInsight для .NET](hdinsight-hadoop-use-hive-dotnet-sdk.md). |
| Отправка задания Pig с помощью пакета SDK для .NET |См. статью [Выполнение заданий Pig с помощью пакета SDK для .NET для Hadoop в HDInsight](hdinsight-hadoop-use-pig-dotnet-sdk.md). |
| Отправка задания Sqoop с помощью пакета SDK для .NET |См. статью [Выполнение заданий Sqoop с помощью пакета SDK для .NET для Hadoop в HDInsight](hdinsight-hadoop-use-sqoop-dotnet-sdk.md). |
| Получение списка кластеров HDInsight с помощью пакета SDK для .NET |См. раздел [Получение списка кластеров](hdinsight-administer-use-dotnet-sdk.md#list-clusters). |
| Масштабирование кластеров HDInsight с помощью пакета SDK для .NET |См. раздел [Масштабирование кластеров](hdinsight-administer-use-dotnet-sdk.md#scale-clusters). |
| Предоставление и отмена доступа к кластерам HDInsight с помощью пакета SDK для .NET |См. раздел [Предоставление и отмена доступа](hdinsight-administer-use-dotnet-sdk.md#grantrevoke-access). |
| Обновление учетных данных пользователя HTTP для кластеров HDInsight с помощью пакета SDK для .NET |См. раздел [Обновление учетных данных пользователя HTTP](hdinsight-administer-use-dotnet-sdk.md#update-http-user-credentials). |
| Поиск учетной записи хранения по умолчанию для кластеров HDInsight с помощью пакета SDK для .NET |См. раздел [Поиск учетной записи хранения по умолчанию](hdinsight-administer-use-dotnet-sdk.md#find-the-default-storage-account). |
| Удаление кластеров HDInsight с помощью пакета SDK для .NET |См. раздел [Удаление кластеров](hdinsight-administer-use-dotnet-sdk.md#delete-clusters). |

### <a name="examples"></a>Примеры
Ниже представлены некоторые примеры выполнения операций с использованием пакета SDK на основе ASM и фрагмент аналогичного кода для пакета SDK на основе ARM.

**Создание CRUD-клиента кластера**

* Старая команда (ASM)
  
        //Certificate auth
        //This logs the application in using a subscription administration certificate, which is not offered in Azure Resource Manager (ARM)
  
        const string subid = "454467d4-60ca-4dfd-a556-216eeeeeeee1";
        var cred = new HDInsightCertificateCredential(new Guid(subid), new X509Certificate2(@"path\to\certificate.cer"));
        var client = HDInsightClient.Connect(cred);
* Новая команда (ARM) (основная авторизация службы)
  
        //Service principal auth
        //This will log the application in as itself, rather than on behalf of a specific user.
        //For details, including how to set up the application, see:
        //   https://azure.microsoft.com/en-us/documentation/articles/hdinsight-create-non-interactive-authentication-dotnet-applications/
  
        var authFactory = new AuthenticationFactory();
  
        var account = new AzureAccount { Type = AzureAccount.AccountType.ServicePrincipal, Id = clientId };
  
        var env = AzureEnvironment.PublicEnvironments[EnvironmentName.AzureCloud];
  
        var accessToken = authFactory.Authenticate(account, env, tenantId, secretKey, ShowDialog.Never).AccessToken;
  
        var creds = new TokenCloudCredentials(subId.ToString(), accessToken);
  
        _hdiManagementClient = new HDInsightManagementClient(creds);
* Новая команда (ARM) (авторизация пользователя)
  
        //User auth
        //This will log the application in on behalf of the user.
        //The end-user will see a login popup.
  
        var authFactory = new AuthenticationFactory();
  
        var account = new AzureAccount { Type = AzureAccount.AccountType.User, Id = username };
  
        var env = AzureEnvironment.PublicEnvironments[EnvironmentName.AzureCloud];
  
        var accessToken = authFactory.Authenticate(account, env, AuthenticationFactory.CommonAdTenant, password, ShowDialog.Auto).AccessToken;
  
        var creds = new TokenCloudCredentials(subId.ToString(), accessToken);
  
        _hdiManagementClient = new HDInsightManagementClient(creds);

**Создание кластера**

* Старая команда (ASM)
  
        var clusterInfo = new ClusterCreateParameters
                    {
                        Name = dnsName,
                        DefaultStorageAccountKey = key,
                        DefaultStorageContainer = defaultStorageContainer,
                        DefaultStorageAccountName = storageAccountDnsName,
                        ClusterSizeInNodes = 1,
                        ClusterType = type,
                        Location = "West US",
                        UserName = "admin",
                        Password = "*******",
                        Version = version,
                        HeadNodeSize = NodeVMSize.Large,
                    };
        clusterInfo.CoreConfiguration.Add(new KeyValuePair<string, string>("config1", "value1"));
        client.CreateCluster(clusterInfo);
* Новая команда (ASM)
  
        var clusterCreateParameters = new ClusterCreateParameters
            {
                Location = "West US",
                ClusterType = "Hadoop",
                Version = "3.1",
                OSType = OSType.Linux,
                DefaultStorageAccountName = "mystorage.blob.core.windows.net",
                DefaultStorageAccountKey =
                    "O9EQvp3A3AjXq/W27rst1GQfLllhp0gUeiUUn2D8zX2lU3taiXSSfqkZlcPv+nQcYUxYw==",
                UserName = "hadoopuser",
                Password = "*******",
                HeadNodeSize = "ExtraLarge",
                RdpUsername = "hdirp",
                RdpPassword = ""*******",
                RdpAccessExpiry = new DateTime(2025, 3, 1),
                ClusterSizeInNodes = 5
            };
        var coreConfigs = new Dictionary<string, string> {{"config1", "value1"}};
        clusterCreateParameters.Configurations.Add(ConfigurationKey.CoreSite, coreConfigs);

**Включение доступа по протоколу HTTP**

* Старая команда (ASM)
  
        client.EnableHttp(dnsName, "West US", "admin", "*******");
* Новая команда (ASM)
  
        var httpParams = new HttpSettingsParameters
        {
               HttpUserEnabled = true,
               HttpUsername = "admin",
               HttpPassword = "*******",
        };
        client.Clusters.ConfigureHttpSettings(resourceGroup, dnsname, httpParams);

**Удаление кластера**

* Старая команда (ASM)
  
        client.DeleteCluster(dnsName);
* Новая команда (ASM)
  
        client.Clusters.Delete(resourceGroup, dnsname);



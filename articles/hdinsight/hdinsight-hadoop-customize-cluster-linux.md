<properties
	pageTitle="Настройка кластеров HDInsight с помощью действий скрипта | Microsoft Azure"
	description="Узнайте, как добавить настраиваемые компоненты в кластеры HDInsight Hadoop на основе Linux с помощью действий сценария. Действия сценариев представляют собой сценарии Bash, которые выполняются во время создания кластера и могут использоваться для настройки конфигурации кластера или добавления дополнительных служб и служебных программ, таких как Hue, Solr или R."
	services="hdinsight"
	documentationCenter=""
	authors="Blackmist"
	manager="paulettm"
	editor="cgronlun"
	tags="azure-portal"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="11/16/2015"
	ms.author="larryfr"/>

# Настройка кластеров HDInsight с помощью действия скрипта (Linux)

В HDInsight есть параметр конфигурации **Действие сценария**, вызывающий пользовательские сценарии, с помощью которых можно настроить кластер в процессе создания. Эти сценарии можно использовать, чтобы устанавливать на нем дополнительное программное обеспечение или изменять конфигурацию приложений.

> [AZURE.NOTE]Информация, приведенная в этой статье, относится только к кластерам HDInsight под управлением Linux. Версию данной статьи для кластеров под управлением Windows см. в разделе [Настройка кластеров HDInsight с помощью действия сценария (Windows)](hdinsight-hadoop-customize-cluster.md).

## Действие сценария в процессе создания кластера

Действие сценария используется только в том случае, когда кластеры находятся в процессе создания. На следующей схеме показано, когда выполняется действие сценария в процессе создания.

![Настройка кластера HDInsight и этапы создания кластера][img-hdi-cluster-states]

Сценарий выполняется во время настройки HDInsight. На этом этапе сценарий выполняется параллельно на всех указанных узлах в кластере с привилегиями корневой учетной записи.

> [AZURE.NOTE]Так как у вас есть привилегии корневой учетной записи на узлах кластера, вы можете использовать сценарий для выполнения таких операций, как остановка и запуск служб, в том числе служб, связанных с Hadoop. Если вы останавливаете службы, перед завершением работы сценария необходимо запустить службу Ambari и другие службы, связанные с Hadoop. Эти службы нужны для определения работоспособности и состояния кластера при его создании.

В каждом кластере можно использовать несколько действий сценариев, которые вызываются в том порядке, в котором они указаны. Сценарий может выполняться на головных узлах, рабочих узлах или узлах обоих типов.

> [AZURE.IMPORTANT]Действия скрипта должны быть завершены в течение 60 минут. В противном случае возникнет ошибка времени ожидания. Во время подготовки узла данный сценарий выполняется одновременно с другими процессами установки и настройки. Конкуренция за ресурсы, такие как ЦП или пропускная способность сети, может привести к затягиванию выполнения сценария по сравнению со временем его выполнения в среде разработки.
> 
> Чтобы свести время выполнения сценария к минимуму, избегайте таких задач, как скачивание и компиляция приложений из исходного кода. Вместо этого выполните предварительную компиляцию приложения и сохраните двоичный файл в хранилище больших двоичных объектов Azure, чтобы его можно быстро скачать в кластер.


## Пример действий сценария

Скрипты действия сценария можно использовать с помощью портала предварительной версии Azure, Azure PowerShell или пакета SDK для HDInsight .NET. В этой статье показано, как использовать действия сценария на портале. Практическое использование действия сценария с помощью PowerShell и пакета SDK для .NET можно увидеть на примерах, приведенных в следующей таблице.

HDInsight предоставляет несколько скриптов для установки следующих компонентов в кластерах HDInsight:

Имя | Скрипт
----- | -----
**Установка Hue** | https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv01/install-hue-uber-v01.sh. См. статью [Установка и использование Hue в кластерах HDInsight](hdinsight-hadoop-hue-linux.md).
**Установка Spark** | https://hdiconfigactions.blob.core.windows.net/linuxsparkconfigactionv02/spark-installer-v02.sh. См. статью [Установка и использование Spark в кластерах HDInsight](hdinsight-hadoop-spark-install-linux.md).
**Установка R** | https://hdiconfigactions.blob.core.windows.net/linuxrconfigactionv01/r-installer-v01.sh. См. статью [Установка и использование R в кластерах HDInsight](hdinsight-hadoop-r-scripts-linux.md).
**Установка Solr** | https://hdiconfigactions.blob.core.windows.net/linuxsolrconfigactionv01/solr-installer-v01.sh. См. статью [Установка и использование Solr в кластерах HDInsight](hdinsight-hadoop-solr-install-linux.md).
**Установка Giraph** | https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh. См. статью [Установка и использование Giraph в кластерах HDInsight](hdinsight-hadoop-giraph-install-linux.md).

## Использование действия сценария с помощью портала предварительной версии Azure

1. Начните создание кластера, как описано в разделе [Создание кластеров Hadoop в HDInsight](hdinsight-provision-clusters.md#portal).

2. В разделе __Необязательная конфигурация__ в колонке **Действия сценария** щелкните **Добавить действие сценария**, чтобы указать сведения об этом действии сценария:

	![Использование действия сценария для настройки кластера](./media/hdinsight-hadoop-customize-cluster-linux/HDI.CreateCluster.8.png "Использование действия сценария для настройки кластера")

	| Свойство | Значение |
	| -------- | ----- |
	| Имя | Укажите имя для действия сценария. |
	| URI-адрес сценария | Укажите URI для сценария, который вызывается для настройки кластера. |
	| Головной/рабочий | Укажите узлы (**Head**, **Worker** или **ZooKeeper**), на которых выполняется сценарий настройки. |
	| Параметры | Укажите параметры, если они требуются для сценария. |

	Нажмите клавишу ВВОД, чтобы добавить несколько действий сценария для установки нескольких компонентов в кластере.

3. Щелкните **Выбрать**, чтобы сохранить конфигурацию действия сценария и продолжить создание кластера.

## Использование действия сценария на основе шаблонов диспетчера ресурсов Azure

В этом разделе для создания кластера HDInsight используются шаблоны диспетчера ресурсов Azure, а для установки пользовательских компонентов в кластере (в данном примере — R) — действие сценария. Ниже приведен пример шаблона ARM для создания кластера с помощью действия сценария.

### Перед началом работы

* Информацию о настройке рабочей станции для запуска командлетов HDInsight PowerShell см. в статье [Как установить и настроить Azure PowerShell](../powershell-install-configure.md).
* Инструкции по созданию шаблонов ARM см. в разделе [Создание шаблонов диспетчера ресурсов Azure](resource-group-authoring-templates.md).
* Информацию об использовании Azure PowerShell с диспетчером ресурсов см. в статье [Использование Azure PowerShell с диспетчером ресурсов Azure](powershell-azure-resource-manager).

### Создание кластеров с помощью действия сценария

1. Скопируйте следующий шаблон в папку на своем компьютере. Он устанавливает R на головной и рабочие узлы в кластере. Чтобы убедиться, что шаблон JSON является допустимым, вставьте его содержимое в [JSONLint](http://jsonlint.com/), интерактивное средство проверки JSON.

			{
		    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
		    "contentVersion": "1.0.0.0",
		    "parameters": {
		        "clusterLocation": {
		            "type": "string",
		            "defaultValue": "West US",
		            "allowedValues": [ "West US" ]
		        },
		        "clusterName": {
		            "type": "string"
		        },
		        "clusterUserName": {
		            "type": "string",
		            "defaultValue": "admin"
		        },
		        "clusterUserPassword": {
		            "type": "securestring"
		        },
		        "sshUserName": {
		            "type": "string",
		            "defaultValue": "username"
		        },
		        "sshPassword": {
		            "type": "securestring"
		        },
		        "clusterStorageAccountName": {
		            "type": "string"
		        },
		        "clusterStorageAccountResourceGroup": {
		            "type": "string"
		        },
		        "clusterStorageType": {
		            "type": "string",
		            "defaultValue": "Standard_LRS",
		            "allowedValues": [
		                "Standard_LRS",
		                "Standard_GRS",
		                "Standard_ZRS"
		            ]
		        },
		        "clusterStorageAccountContainer": {
		            "type": "string"
		        },
		        "clusterHeadNodeCount": {
		            "type": "int",
		            "defaultValue": 1
		        },
		        "clusterWorkerNodeCount": {
		            "type": "int",
		            "defaultValue": 2
		        }
		    },
		    "variables": {
		    },
		    "resources": [
		        {
		            "name": "[parameters('clusterStorageAccountName')]",
		            "type": "Microsoft.Storage/storageAccounts",
		            "location": "[parameters('clusterLocation')]",
		            "apiVersion": "2015-05-01-preview",
		            "dependsOn": [ ],
		            "tags": { },
		            "properties": {
		                "accountType": "[parameters('clusterStorageType')]"
		            }
		        },
		        {
		            "name": "[parameters('clusterName')]",
		            "type": "Microsoft.HDInsight/clusters",
		            "location": "[parameters('clusterLocation')]",
		            "apiVersion": "2015-03-01-preview",
		            "dependsOn": [
		                "[concat('Microsoft.Storage/storageAccounts/', parameters('clusterStorageAccountName'))]"
		            ],
		            "tags": { },
		            "properties": {
		                "clusterVersion": "3.2",
		                "osType": "Linux",
		                "clusterDefinition": {
		                    "kind": "hadoop",
		                    "configurations": {
		                        "gateway": {
		                            "restAuthCredential.isEnabled": true,
		                            "restAuthCredential.username": "[parameters('clusterUserName')]",
		                            "restAuthCredential.password": "[parameters('clusterUserPassword')]"
		                        }
		                    }
		                },
		                "storageProfile": {
		                    "storageaccounts": [
		                        {
		                            "name": "[concat(parameters('clusterStorageAccountName'),'.blob.core.windows.net')]",
		                            "isDefault": true,
		                            "container": "[parameters('clusterStorageAccountContainer')]",
		                            "key": "[listKeys(resourceId(parameters('clusterStorageAccountResourceGroup'), 'Microsoft.Storage/storageAccounts', parameters('clusterStorageAccountName')), providers('Microsoft.Storage', 'storageAccounts').apiVersions[0]).key1]"
		                        }
		                    ]
		                },
		                "computeProfile": {
		                    "roles": [
		                        {
		                            "name": "headnode",
		                            "targetInstanceCount": "[parameters('clusterHeadNodeCount')]",
		                            "hardwareProfile": {
		                                "vmSize": "Large"
		                            },
		                            "osProfile": {
		                                "linuxOperatingSystemProfile": {
		                                    "username": "[parameters('sshUserName')]",
		                                    "password": "[parameters('sshPassword')]"
		                                }
		                            },
		                            "scriptActions": [
		                                {
		                                    "name": "installR",
		                                    "uri": "https://hdiconfigactions.blob.core.windows.net/linuxrconfigactionv01/r-installer-v01.sh",
		                                    "parameters": ""
		                                }
		                            ]
		                        },
		                        {
		                            "name": "workernode",
		                            "targetInstanceCount": "[parameters('clusterWorkerNodeCount')]",
		                            "hardwareProfile": {
		                                "vmSize": "Large"
		                            },
		                            "osProfile": {
		                                "linuxOperatingSystemProfile": {
		                                    "username": "[parameters('sshUserName')]",
		                                    "password": "[parameters('sshPassword')]"
		                                }
		                            },
		                            "scriptActions": [
		                                {
		                                    "name": "installR",
		                                    "uri": "https://hdiconfigactions.blob.core.windows.net/linuxrconfigactionv01/r-installer-v01.sh",
		                                    "parameters": ""
		                                }
		                            ]
		                        }
		                    ]
		                }
		            }
		        }
		    ],
		    "outputs": {
		        "cluster":{
		            "type" : "object",
		            "value" : "[reference(resourceId('Microsoft.HDInsight/clusters',parameters('clusterName')))]"
		        }
		    }
		}

2. Запустите Azure PowerShell и войдите в учетную запись Azure. После предоставления учетных данных команда возвращает информацию о вашей учетной записи.

		Add-AzureRmAccount

		Id                             Type       ...
		--                             ----
		someone@example.com            User       ...

3. Если у вас несколько подписок, укажите идентификатор подписки, которую вы хотите использовать для развертывания.

		Select-AzureRmSubscription -SubscriptionID <YourSubscriptionId>

    > [AZURE.NOTE]`Get-AzureRmSubscription` можно использовать для получения связанного с вашей учетной записью списка всех подписок, в котором указан идентификатор каждой подписки.

5. Создайте группу ресурсов, если у вас нет существующей группы ресурсов. Введите имя группы ресурсов и расположение, необходимое для решения. Появится сводка по новой группе ресурсов.

		New-AzureRmResourceGroup -Name myresourcegroup -Location "West US"

		ResourceGroupName : myresourcegroup
		Location          : westus
		ProvisioningState : Succeeded
		Tags              :
		Permissions       :
		            		Actions  NotActions
		            		=======  ==========
		            		*
		ResourceId        : /subscriptions/######/resourceGroups/ExampleResourceGroup


6. Чтобы создать новое развертывание для группы ресурсов, выполните команду **New-AzureRmResourceGroupDeployment** и укажите необходимые параметры. Параметры будут включать в себя имя развертывания, имя группы ресурсов, а также путь к созданному шаблону или его URL-адрес. Если для вашего шаблона требуются какие-либо параметры, необходимо также передать их. В данном случае для действия сценария по установке R в кластере никакие параметры не требуются.


		New-AzureRmResourceGroupDeployment -Name mydeployment -ResourceGroupName myresourcegroup -TemplateFile <PathOrLinkToTemplate>


	Вам будет предложено задать значения параметров, определенных в шаблоне.

7. После развертывания группы ресурсов вы увидите сводку по развертыванию.

		  DeploymentName    : mydeployment
		  ResourceGroupName : myresourcegroup
		  ProvisioningState : Succeeded
		  Timestamp         : 8/17/2015 7:00:27 PM
		  Mode              : Incremental
		  ...

8. Если развертывание завершается сбоем, вы можете использовать следующие командлеты для получения информации об ошибках.

		Get-AzureRmResourceGroupDeployment -ResourceGroupName myresourcegroup -ProvisioningState Failed

## Использование действия сценария из Azure PowerShell

В этом разделе мы используем командлет [Add-AzureRmHDInsightScriptAction](https://msdn.microsoft.com/library/mt603527.aspx), чтобы вызвать сценарии с помощью действия сценария для настройки кластера. Прежде чем продолжить, убедитесь, что вы установили и настроили Azure PowerShell. Информацию о настройке рабочей станции для запуска командлетов HDInsight PowerShell см. в статье [Установка и настройка Azure PowerShell](../powershell-install-configure.md).

Выполните следующие действия:

1. Откройте консоль Azure PowerShell и объявите следующие переменные:

		# PROVIDE VALUES FOR THESE VARIABLES
		$subscriptionId = "<SubscriptionId>"		# ID of the Azure subscription
		$clusterName = "<HDInsightClusterName>"			# HDInsight cluster name
		$storageAccountName = "<StorageAccountName>"	# Azure storage account that hosts the default container
		$storageAccountKey = "<StorageAccountKey>"      # Key for the storage account
		$containerName = $clusterName
		$location = "<MicrosoftDataCenter>"				# Location of the HDInsight cluster. It must be in the same data center as the storage account.
		$clusterNodes = <ClusterSizeInNumbers>			# The number of nodes in the HDInsight cluster.
        $resourceGroupName = "<ResourceGroupName>"      # The resource group that the HDInsight cluster will be created in

2. Укажите значения конфигурации (например, узлы в кластере) и хранилище для использования по умолчанию.

		# SPECIFY THE CONFIGURATION OPTIONS
		Select-AzureRmSubscription -SubscriptionId $subscriptionId
		$config = New-AzureRmHDInsightClusterConfig
		$config.DefaultStorageAccountName="$storageAccountName.blob.core.windows.net"
		$config.DefaultStorageAccountKey=$storageAccountKey

3. Используйте командлет **Add-AzureRmHDInsightScriptAction** для вызова сценария. В следующем примере используется сценарий, устанавливающий R в кластере:

		# INVOKE THE SCRIPT USING THE SCRIPT ACTION FOR HEADNODE AND WORKERNODE
		$config = Add-AzureRmHDInsightScriptAction -Config $config -Name "Install R"  -NodeType HeadNode -Uri https://hdiconfigactions.blob.core.windows.net/linuxrconfigactionv01/r-installer-v01.sh
        $config = Add-AzureRmHDInsightScriptAction -Config $config -Name "Install R"  -NodeType WorkerNode -Uri https://hdiconfigactions.blob.core.windows.net/linuxrconfigactionv01/r-installer-v01.sh

	Командлет **Add-AzureRmHDInsightScriptAction** принимает следующие параметры.

	| Параметр | Определение |
	| --------- | ---------- |
	| Настройка | Объект конфигурации, к которому добавляются сведения о действии скрипта. |
	| Имя | Имя действия скрипта |
	| NodeType | Указывает узел, на котором выполняется сценарий настройки. Допустимые значения: **HeadNode** (для установки на головном узле), **WorkerNode** (для установки на всех узлах данных) или **Zookeeper** (для установки на узле Zookeeper). |
	| Параметры | Параметры, необходимые для скрипта. |
	| URI | Задает универсальный код ресурса для выполняемого сценария. |

4. Наконец, создайте кластер:
        
        New-AzureRmHDInsightCluster -config $config -clustername $clusterName -DefaultStorageContainer $containerName -Location $location -ResourceGroupName $resourceGroupName -ClusterSizeInNodes $clusterNodes

При появлении запроса введите учетные данные для кластера. Создание кластера может занять несколько минут.

## Использование действия сценария из пакета SDK HDInsight для .NET

Пакет SDK HDInsight для .NET предоставляет клиентские библиотеки, которые упрощают работу с кластерами HDInsight из приложения .NET. В следующей процедуре показано, как использовать сценарий для настройки кластера из пакета SDK HDInsight для .NET.

> [AZURE.IMPORTANT]Прежде всего необходимо создать самозаверяющий сертификат, установить его на рабочую станцию и загрузить его в вашу подписку Azure. Более подробные инструкции см. в разделе [Создание самозаверяющего сертификата](http://go.microsoft.com/fwlink/?LinkId=511138).


### Создание проекта Visual Studio


1. Создайте в Visual Studio консольное приложение C#.
2. Введите следующую команду в окне **консоли диспетчера пакетов** NuGet.

		Install-Package Microsoft.Azure.Common.Authentication -pre
		Install-Package Microsoft.Azure.Management.HDInsight -Pre

	Эти команды добавляют библиотеки .NET и ссылки на них в текущий проект Visual Studio.

3. Откройте файл **Program.cs** и добавьте следующие операторы using:

		using System;
		using System.Security;
		using Microsoft.Azure;
		using Microsoft.Azure.Common.Authentication;
		using Microsoft.Azure.Common.Authentication.Factories;
		using Microsoft.Azure.Common.Authentication.Models;
		using Microsoft.Azure.Management.HDInsight;
		using Microsoft.Azure.Management.HDInsight.Models;

4. Замените код класса на следующий:

        private static HDInsightManagementClient _hdiManagementClient;

        private static Guid SubscriptionId = new Guid("<AZURE SUBSCRIPTION ID>");
        private const string ResourceGroupName = "<AZURE RESOURCEGROUP NAME>";

        private const string NewClusterName = "<HDINSIGHT CLUSTER NAME>";
        private const int NewClusterNumNodes = <NUMBER OF NODES>;
        private const string NewClusterLocation = "<LOCATION>";  // Must match the Azure Storage account location
        private const string NewClusterVersion = "3.2";
        private const HDInsightClusterType NewClusterType = HDInsightClusterType.Hadoop;
        private const OSType NewClusterOSType = OSType.Windows;

        private const string ExistingStorageName = "<STORAGE ACCOUNT NAME>.blob.core.windows.net";
        private const string ExistingStorageKey = "<STORAGE ACCOUNT KEY>";
        private const string ExistingContainer = "<DEFAULT CONTAINER NAME>"; 

        private const string NewClusterUsername = "admin";
        private const string NewClusterPassword = "<HTTP USER PASSWORD>";

        private const string NewClusterSshUserName = "sshuser";
        private const string NewClusterSshPublicKey = @"---- BEGIN SSH2 PUBLIC KEY ----
			Comment: ""rsa-key-20150731""
			AAAAB3NzaC1yc2EAAAABJQAAAQEA4QiCRLqT7fnmUA5OhYWZNlZo6lLaY1c+IRsp
			gmPCsJVGQLu6O1wqcxRqiKk7keYq8bP5s30v6bIljsLZYTnyReNUa5LtFw7eauGr
			yVt3Pve6ejfWELhbVpi0iq8uJNFA9VvRkz8IP1JmjC5jsdnJhzQZtgkIrdn3w0e6
			WVfu15kKyY8YAiynVbdV51EB0SZaSLdMZkZQ81xi4DDtCZD7qvdtWEFwLa+EHdkd
			pzO36Mtev5XvseLQqzXzZ6aVBdlXoppGHXkoGHAMNOtEWRXpAUtEccjpATsaZhQR
			zZdZlzHduhM10ofS4YOYBADt9JohporbQVHM5w6qUhIgyiPo7w==
			---- END SSH2 PUBLIC KEY ----"; //replace the public key with your own

        private static void Main(string[] args)
        {
            var tokenCreds = GetTokenCloudCredentials();
            var subCloudCredentials = GetSubscriptionCloudCredentials(tokenCreds, SubscriptionId);

            _hdiManagementClient = new HDInsightManagementClient(subCloudCredentials);

            CreateCluster();
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
                SshUserName = NewClusterSshUserName,
        		SshPublicKey = NewClusterSshPublicKey
            };

            ScriptAction rScriptAction = new ScriptAction("Install R",
                new Uri("https://hdiconfigactions.blob.core.windows.net/linuxrconfigactionv01/r-installer-v01.sh"), "");

            parameters.ScriptActions.Add(ClusterNodeType.HeadNode,new System.Collections.Generic.List<ScriptAction> { rScriptAction});
            parameters.ScriptActions.Add(ClusterNodeType.WorkerNode, new System.Collections.Generic.List<ScriptAction> { rScriptAction });

            _hdiManagementClient.Clusters.Create(ResourceGroupName, NewClusterName, parameters);
        }
		
6. Замените значения членов класса.

7. Нажмите клавишу **F5** для запуска приложения. Должно открыться окно консоли, в котором отображается состояние приложения. Появится запрос на ввод учетных данных учетной записи Azure. На подготовку кластера HDInsight может уйти несколько минут.


## Устранение неполадок

Вы можете использовать веб-интерфейс Ambari для просмотра сведений, регистрируемых сценариями во время создания кластера. Однако в случае сбоя создания кластера из-за ошибки в сценарии журналы также доступны в связанной с кластером учетной записи хранения по умолчанию. Этот раздел содержит сведения о том, как получить журналы обоими этими способами.

### Использование веб-интерфейса Ambari

1. Откройте браузер и перейдите по адресу https://CLUSTERNAME.azurehdinsight.net. Параметр CLUSTERNAME нужно заменить именем кластера HDInsight.

	При появлении запроса введите имя (admin) и пароль учетной записи администратора кластера. Возможно, вам потребуется повторно ввести учетные данные администратора в веб-форме.

2. На панели вверху страницы выберите запись __ops__. Появится список текущих и предыдущих операций, выполняемых в кластере с помощью Ambari.

	![Веб-панель Ambari с выбранной записью ops](./media/hdinsight-hadoop-customize-cluster-linux/ambari-nav.png)

3. Найдите записи, для которых в столбце __Операции__ указано __run\_customscriptaction__. Такие записи создаются при выполнении действий сценария.

	![Снимок экрана операций](./media/hdinsight-hadoop-customize-cluster-linux/ambariscriptaction.png)

	Выберите эту запись и перейдите по ссылкам для просмотра данных STDOUT и STDERR, сгенерированных при выполнении сценария в кластере.

### Доступ к журналам из учетной записи хранения по умолчанию

В случае сбоя создания кластера из-за ошибки в действии сценария журналы действий сценария по-прежнему доступны в связанной с кластером учетной записи хранения по умолчанию.

* Журналы хранилища находятся в `\STORAGE_ACOCUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\CLUSTER_NAME\DATE`. 

	![Снимок экрана операций](./media/hdinsight-hadoop-customize-cluster-linux/script_action_logs_in_storage.png)

	В этом расположении журналы упорядочены по узлам headnode, workdernode и zookeeper. Примерами могут служить: * **головной узел** – `<uniqueidentifier>AmbariDb-hn0-<generated_value>.cloudapp.net` * **рабочий узел** – `<uniqueidentifier>AmbariDb-wn0-<generated_value>.cloudapp.net` * **узел Zookeeper** – `<uniqueidentifier>AmbariDb-zk0-<generated_value>.cloudapp.net`

* Все stdout и stderr соответствующего узла передаются в учетную запись хранилища. Для каждого действия сценария есть файл **output-*.txt** и **errors-*.txt**. Выходной TXT-файл содержит сведения об универсальном коде ресурса (URI) сценария, который был запущен на узле. Например:

		'Start downloading script locally: ', u'https://hdiconfigactions.blob.core.windows.net/linuxrconfigactionv01/r-installer-v01.sh'

* Возможно, повторно создан кластер действия сценария с тем же именем. В этом случае соответствующие журналы можно отличить по имени папки DATE. Например, структура папок для кластера (mycluster), созданного в другие дни, будет иметь вид:
	* `\STORAGE_ACOCUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\mycluster\2015-10-04`
	* `\STORAGE_ACOCUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\mycluster\2015-10-05`

* При создании кластера действие сценария с таким же именем в тот же день можно использовать уникальный префикс для идентификации соответствующих файлов журнала.

* При создании кластера в конце дня файлы журналов могут охватить два дня. В таких случаях для одного кластера будут отображаться две разные папки даты.

* Передача файлов журнала в контейнер по умолчанию может занять до 5 минут, особенно для больших кластеров. Таким образом, если вы хотите получить доступ к журналам, не следует немедленно удалять кластер при сбое действия сценария.


## Поддержка программного обеспечения с открытым исходным кодом, используемого в кластере HDInsight

Служба Microsoft Azure HDInsight — это гибкая платформа, которая позволяет создавать приложения для работы с данными большого размера в облаке, используя сформированную вокруг Hadoop экосистему технологий с открытым исходным кодом. Microsoft Azure предоставляет общий уровень поддержки для технологий с открытым исходным кодом, как описано в статье **Объем поддержки** на [веб-сайте часто задаваемых вопросов о поддержке Azure](http://azure.microsoft.com/support/faq/). Служба HDInsight предоставляет дополнительный уровень поддержки для некоторых описанных ниже компонентов.

В службе HDInsight доступно два типа компонентов с открытым исходным кодом.

- **Встроенные компоненты**. Эти компоненты предварительно установлены в кластерах HDInsight и предоставляют его базовые функциональные возможности. Например, к этой категории относится диспетчер ресурсов YARN, язык запросов Hive (HiveQL) и библиотека Mahout. Полный список компонентов кластера доступен в статье [Новые возможности версий кластеров Hadoop, предоставляемых в HDInsight](hdinsight-component-versioning.md).

- **Настраиваемые компоненты**. Как пользователь кластера вы можете установить или использовать в рабочей нагрузке любой компонент, полученный из сообщества или созданный самостоятельно.

> [AZURE.WARNING]Компоненты, предоставляемые вместе с кластером HDInsight, поддерживаются в полном объеме. Служба поддержки Майкрософт поможет вам выявить и устранить проблемы, связанные с этими компонентами.
>
> Настраиваемые компоненты получают ограниченную коммерчески оправданную поддержку, способствующую дальнейшей диагностике проблемы. В результате проблема может быть устранена, либо вас могут попросить воспользоваться доступными каналами по технологиям с открытым исходным кодом, чтобы связаться с экспертами в данной области. Можно использовать ряд сайтов сообществ, например [форум MSDN по HDInsight](https://social.msdn.microsoft.com/Forums/azure/ru-RU/home?forum=hdinsight) и [http://stackoverflow.com](http://stackoverflow.com). Кроме того, для проектов Apache есть соответствующие сайты по адресу [http://apache.org](http://apache.org), например для [Hadoop](http://hadoop.apache.org/) и [Spark](http://spark.apache.org/).

Служба HDInsight позволяет использовать настраиваемые компоненты несколькими разными способами. Уровень поддержки не зависит от того, как компонент используется или устанавливается в кластере. Ниже приведен список самых распространенных способов использования настраиваемых компонентов в кластерах HDInsight.

1. Отправка задания. В кластер можно отправлять задания Hadoop или другие типы заданий, выполняющие или использующие настраиваемые компоненты.

2. Настройка кластера. Во время создания кластера можно указать дополнительные параметры и настраиваемые компоненты, которые будут установлены в узлах кластера.

3. Примеры. Для популярных настраиваемых компонентов корпорация Майкрософт и другие компании могут предоставлять примеры использования таких компонентов в кластерах HDInsight. Эти примеры представляются без поддержки.

## Дальнейшие действия

Ниже приведены ресурсы с информацией о создании и настройке кластера с помощью сценариев и соответствующими примерами.

- [Разработка скриптов действия сценария для HDInsight](hdinsight-hadoop-script-actions-linux.md)
- [Установка и использование Spark в кластерах HDInsight](hdinsight-hadoop-spark-install-linux.md)
- [Установка и использование R в кластерах HDInsight](hdinsight-hadoop-r-scripts-linux.md)
- [Установка и использование Solr в кластерах HDInsight](hdinsight-hadoop-solr-install-linux.md)
- [Установка и использование Giraph в кластерах HDInsight](hdinsight-hadoop-giraph-install-linux.md)



[img-hdi-cluster-states]: ./media/hdinsight-hadoop-customize-cluster-linux/HDI-Cluster-state.png "Этапы создания кластера"

<!---HONumber=Nov15_HO4-->
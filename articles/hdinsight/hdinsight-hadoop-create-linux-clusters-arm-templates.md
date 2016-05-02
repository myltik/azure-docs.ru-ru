<properties
   pageTitle="Создание кластеров Hadoop под управлением Linux в HDInsight с помощью шаблонов ARM | Microsoft Azure"
   	description="Сведения о создании кластеров для Azure HDInsight с помощью шаблонов ARM Azure."
   services="hdinsight"
   documentationCenter=""
   tags="azure-portal"
   authors="mumian"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="03/08/2016"
   ms.author="jgao"/>

# Создание кластеров Hadoop под управлением Linux в HDInsight с помощью шаблонов ARM

[AZURE.INCLUDE [selector](../../includes/hdinsight-selector-create-clusters.md)]

Узнайте, как создавать кластеры HDInsight с помощью шаблонов диспетчера ресурсов Azure (ARM). Дополнительные сведения см. в статье [Развертывание приложения с помощью шаблона диспетчера ресурсов Azure](../resource-group-template-deploy.md). Сведения о других инструментах и функциях создания кластера приведены на вкладке в верхней части этой страницы или в разделе [Способы создания кластера](hdinsight-provision-clusters.md#cluster-creation-methods).

##Предварительные требования:

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Прежде чем следовать указаниям в этой статье, необходимо подготовить следующее:

- [Подписка Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- [Azure PowerShell](hdinsight-administer-use-powershell.md#install-azure-powershell-10-and-greater) и (или) [Azure CLI](../xplat-cli-install.md).

    [AZURE.INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-powershell-and-cli.md)]

## Шаблоны ARM

Шаблон ARM упрощает создание кластеров HDInsight, их зависимых ресурсов (например учетной записи хранения по умолчанию) и других ресурсов (таких как база данных SQL Azure для использования Apache Sqoop) для приложения и позволяет сделать это с помощью одной скоординированной операции. В шаблоне определяются ресурсы, необходимые для работы приложения, и указываются параметры развертывания в качестве входных значений для различных сред. Шаблон состоит из JSON и выражений, на основе которых можно создавать значения для развертывания.

Шаблон ARM для создания кластера HDInsight и зависимой учетной записи хранения Azure можно найти в [Приложении A](#appx-a-arm-template). Используйте кроссплатформенный [VSCode](https://code.visualstudio.com/#alt-downloads) с [расширением ARM](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools) или текстовый редактор, чтобы сохранить шаблон в файл на своей рабочей станции. Далее вы узнаете, как вызвать шаблон разными способами.

Дополнительные сведения о шаблоне ARM см. в следующих статьях:

- [Шаблоны диспетчера ресурсов Azure](../resource-group-authoring-templates.md)
- [Развертывание приложения с использованием шаблона диспетчера ресурсов Azure](../resource-group-template-deploy.md)


## Развертывание с помощью PowerShell

В следующей процедуре создается кластер HDInsight под управлением Linux.

**Развертывание кластера с помощью шаблона ARM**

1. Сохраните JSON-файл из [приложения А](#appx-a-arm-template) на своей рабочей станции. В сценарии PowerShell имя файла — *C:\\HDITutorials-ARM\\hdinsight-arm-template.json*.
2. При необходимости настройте параметры и переменные.
3. Запустите шаблон с помощью следующего сценария PowerShell:

        ####################################
        # Set these variables
        ####################################
        #region - used for creating Azure service names
        $nameToken = "<Enter an Alias>" 
        $templateFile = "C:\HDITutorials-ARM\hdinsight-arm-template.json"
        #endregion

        ####################################
        # Service names and varialbes
        ####################################
        #region - service names
        $namePrefix = $nameToken.ToLower() + (Get-Date -Format "MMdd")

        $resourceGroupName = $namePrefix + "rg"
        $hdinsightClusterName = $namePrefix + "hdi"
        $defaultStorageAccountName = $namePrefix + "store"
        $defaultBlobContainerName = $hdinsightClusterName

        $location = "East US 2"

        $armDeploymentName = $namePrefix
        #endregion

        ####################################
        # Connect to Azure
        ####################################
        #region - Connect to Azure subscription
        Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
        try{Get-AzureRmContext}
        catch{Login-AzureRmAccount}
        #endregion

        # Create a resource group
        New-AzureRmResourceGroup -Name $resourceGroupName -Location $Location

        # Create cluster and the dependent storage accounge
        $parameters = @{clusterName="$hdinsightClusterName";clusterStorageAccountName="$defaultStorageAccountName"}

        New-AzureRmResourceGroupDeployment `
            -Name $armDeploymentName `
            -ResourceGroupName $resourceGroupName `
            -TemplateFile $templateFile `
            -TemplateParameterObject $parameters

        # List cluster
        Get-AzureRmHDInsightCluster -ResourceGroupName $resourceGroupName -ClusterName $hdinsightClusterName 

	Сценарий PowerShell настраивает только имя кластера. Имя учетной записи хранения встроено в шаблон. Вам будет предложено ввести пароль пользователя кластера (по умолчанию имя пользователя — *admin*) и пароль пользователя SSH (имя пользователя SSH по умолчанию — *sshuser*).
	
Дополнительные сведения см. в разделе [Развертывание с помощью PowerShell](../resource-group-template-deploy.md#deploy-with-powershell).

## Развертывание с помощью интерфейса командной строки Azure

Следующий пример создает кластер и его учетную запись хранения и контейнер, вызывая шаблон ARM:

	azure login
	azure config mode arm
    azure group create -n hdi1229rg -l "East US"
    azure group deployment create --resource-group "hdi1229rg" --name "hdi1229" --template-file "C:\HDITutorials-ARM\hdinsight-arm-template.json"
    
Вам будет предложено ввести имя кластера, пароль пользователя кластера (по умолчанию имя пользователя — *admin*) и пароль пользователя SSH (имя пользователя SSH по умолчанию — *sshuser*). Предоставление встроенных параметров.

    azure group deployment create --resource-group "hdi1229rg" --name "hdi1229" --template-file "c:\Tutorials\HDInsightARM\create-linux-based-hadoop-cluster-in-hdinsight.json" --parameters '{"clusterName":{"value":"hdi1229"},"clusterLoginPassword":{"value":"Pass@word1"},"sshPassword":{"value":"Pass@word1"}}'

## Развертывание с помощью REST API

См. раздел [Развертывание с помощью REST API](../resource-group-template-deploy.md#deploy-with-the-rest-api).

## Развертывание с помощью Visual Studio

С помощью Visual Studio можно создать проект группы ресурсов и развернуть его в Azure, используя пользовательский интерфейс. Можно выбрать тип ресурсов, добавляемых в проект. Эти ресурсы будут автоматически добавляться в шаблон диспетчера ресурсов. Проект также предоставляет сценарий PowerShell для развертывания шаблона.

Обзорные сведения об использовании групп ресурсов в Visual Studio см. в статье [Создание и развертывание групп ресурсов Azure с помощью Visual Studio](../vs-azure-tools-resource-groups-deployment-projects-create-deploy.md).

##Дальнейшие действия
В этой статье вы ознакомились с несколькими способами создания кластера HDInsight. Для получения дополнительных сведений ознакомьтесь со следующими статьями:

- Пример развертывания ресурсов с помощью клиентской библиотеки .NET см. в статье [Развертывание ресурсов с использованием библиотек .NET и шаблона](../virtual-machines/virtual-machines-windows-csharp-template.md).
- Подробный пример развертывания приложения см. в статье [Предсказуемые подготовка и развертывание микрослужб в Azure](../app-service-web/app-service-deploy-complex-application-predictably.md).
- Инструкции по развертыванию своего решения в различных средах см. в статье [Среды разработки и тестирования в Microsoft Azure](../solution-dev-test-environments.md).
- Дополнительные сведения о разделах в шаблоне Azure Resource Manager см. в статье [Создание шаблонов](../resource-group-authoring-templates.md).
- Список функций, которые можно использовать в шаблоне Azure Resource Manager, см. в статье [Функции шаблонов](../resource-group-template-functions.md).

##Приложение А. Шаблон ARM

Следующий шаблон диспетчера ресурсов Azure создает кластер Hadoop под управлением Linux с зависимой учетной записью хранения Azure.

    {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "clusterName": {
        "type": "string",
        "metadata": {
            "description": "The name of the HDInsight cluster to create."
        }
        },
        "clusterLoginUserName": {
        "type": "string",
        "defaultValue": "admin",
        "metadata": {
            "description": "These credentials can be used to submit jobs to the cluster and to log into cluster dashboards."
        }
        },
        "clusterLoginPassword": {
        "type": "securestring",
        "metadata": {
            "description": "The password for the cluster login."
        }
        },
        "sshUserName": {
        "type": "string",
        "defaultValue": "sshuser",
        "metadata": {
            "description": "These credentials can be used to remotely access the cluster and the edge node virtual machine."
        }
        },
        "sshPassword": {
        "type": "securestring",
        "metadata": {
            "description": "The password for the ssh user."
        }
        },
        "location": {
        "type": "string",
        "defaultValue": "East US",
        "allowedValues": [
            "East US",
            "East US 2",
            "North Central US",
            "South Central US",
            "West US",
            "North Europe",
            "West Europe",
            "East Asia",
            "Southeast Asia",
            "Japan East",
            "Japan West",
            "Australia East",
            "Australia Southeast"
        ],
        "metadata": {
            "description": "The location where all azure resources will be deployed."
        }
        },
        "clusterType": {
        "type": "string",
        "defaultValue": "hadoop",
        "allowedValues": [
            "hadoop",
            "hbase",
            "storm",
            "spark"
        ],
        "metadata": {
            "description": "The type of the HDInsight cluster to create."
        }
        },  
        "clusterWorkerNodeCount": {
        "type": "int",
        "defaultValue": 2,
        "metadata": {
            "description": "The number of nodes in the HDInsight cluster."
        }
        }      
    },
    "variables": {
        "defaultApiVersion": "2015-05-01-preview",
        "clusterApiVersion": "2015-03-01-preview",
        "clusterStorageAccountName": "[concat(parameters('clusterName'),'store')]"      
    },
    "resources": [
        {
        "name": "[variables('clusterStorageAccountName')]",
        "type": "Microsoft.Storage/storageAccounts",
        "location": "[parameters('location')]",
        "apiVersion": "[variables('defaultApiVersion')]",
        "dependsOn": [],
        "tags": {},
        "properties": {
            "accountType": "Standard_LRS"
        }
        },
        {
        "name": "[parameters('clusterName')]",
        "type": "Microsoft.HDInsight/clusters",
        "location": "[parameters('location')]",
        "apiVersion": "[variables('clusterApiVersion')]",
        "dependsOn": [
            "[concat('Microsoft.Storage/storageAccounts/',variables('clusterStorageAccountName'))]"
        ],
        "tags": {},
        "properties": {
            "clusterVersion": "3.2",
            "osType": "Linux",
            "clusterDefinition": {
            "kind": "[parameters('clusterType')]",
            "configurations": {
                "gateway": {
                "restAuthCredential.isEnabled": true,
                "restAuthCredential.username": "[parameters('clusterLoginUserName')]",
                "restAuthCredential.password": "[parameters('clusterLoginPassword')]"
                }
            }
            },
            "storageProfile": {
            "storageaccounts": [
                {
                "name": "[concat(variables('clusterStorageAccountName'),'.blob.core.windows.net')]",
                "isDefault": true,
                "container": "[parameters('clusterName')]",
                "key": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('clusterStorageAccountName')), variables('defaultApiVersion')).key1]"
                }
            ]
            },
            "computeProfile": {
            "roles": [
                {
                "name": "headnode",
                "targetInstanceCount": "1",
                "hardwareProfile": {
                    "vmSize": "Large"
                },
                "osProfile": {
                    "linuxOperatingSystemProfile": {
                    "username": "[parameters('sshUserName')]",
                    "password": "[parameters('sshPassword')]"
                    }
                }
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
                }
                }
            ]
            }
        }
        }
    ],
    "outputs": {
        "cluster": {
        "type": "object",
        "value": "[reference(resourceId('Microsoft.HDInsight/clusters',parameters('clusterName')))]"
        }
    }
    }

<!---HONumber=AcomDC_0420_2016-->
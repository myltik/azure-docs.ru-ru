---
title: "Создание кластеров Hadoop под управлением Windows в HDInsight с помощью шаблонов Azure Resource Manager | Документация Майкрософт"
description: "Узнайте о создании кластеров для Azure HDInsight с помощью шаблонов Azure Resource Manager."
services: hdinsight
documentationcenter: 
tags: azure-portal
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: a4975787-699a-4cdc-b764-d8c9301e13ef
ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/19/2016
ms.author: jgao
translationtype: Human Translation
ms.sourcegitcommit: 72ca562c53f813599f19069cfac7ef3ac1957968
ms.openlocfilehash: 14cd1d8a9e278fa54168efb3c66cf6d693b57054


---
# <a name="create-windows-based-hadoop-clusters-in-hdinsight-using-azure-resource-manager-templates"></a>Создание кластеров Hadoop под управлением Windows в HDInsight с помощью шаблонов Azure Resource Manager
[!INCLUDE [selector](../../includes/hdinsight-selector-create-clusters.md)]

Узнайте, как создавать кластеры HDInsight с помощью шаблонов Azure Resource Manager. Узнайте подробнее [о развертывании приложения с помощью шаблона диспетчера ресурсов Azure](../resource-group-template-deploy.md). Сведения о других инструментах и функциях создания кластера приведены на вкладке в верхней части этой страницы или в разделе [Способы создания кластера](hdinsight-provision-clusters.md#cluster-creation-methods).

## <a name="prerequisites"></a>Предварительные требования:
[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Прежде чем следовать указаниям в этой статье, необходимо подготовить следующее:

* [Подписка Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* Azure PowerShell или Azure CLI.

    [!INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-powershell-and-cli.md)]

### <a name="access-control-requirements"></a>Требования к контролю доступа
[!INCLUDE [access-control](../../includes/hdinsight-access-control-requirements.md)]

## <a name="resource-manager-templates"></a>Шаблоны диспетчера ресурсов
Шаблон Resource Manager упрощает создание кластеров HDInsight, их зависимых ресурсов (например учетной записи хранения по умолчанию) и других ресурсов (таких как база данных SQL Azure для использования Apache Sqoop) для приложения и позволяет сделать это с помощью одной скоординированной операции. В шаблоне определяются ресурсы, необходимые для работы приложения, и указываются параметры развертывания в качестве входных значений для различных сред. Шаблон состоит из JSON и выражений, на основе которых можно создавать значения для развертывания.

Шаблон Resource Manager для создания кластера HDInsight и зависимой учетной записи хранения Azure можно найти в [Приложении A](#appx-a-arm-template). С помощью текстового редактора сохраните шаблон в файл на своей рабочей станции. Далее вы узнаете, как вызвать шаблон с помощью различных средств.

Дополнительные сведения о шаблоне Resource Manager см. в перечисленных ниже статьях.

* [Шаблоны диспетчера ресурсов Azure](../resource-group-authoring-templates.md)
* [Развертывание приложения с использованием шаблона диспетчера ресурсов Azure](../resource-group-template-deploy.md)

## <a name="deploy-with-powershell"></a>Развертывание с помощью PowerShell
В следующей процедуре создается кластер HDInsight.

**Развертывание кластера с помощью шаблона Resource Manager**

1. Сохраните JSON-файл из [приложения А](#appx-a-arm-template) на своей рабочей станции.
2. При необходимости установите параметры.
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

    Скрипт PowerShell настраивает только имя кластера и имя учетной записи хранения.  Вы можете задать другие значения в шаблоне диспетчера ресурсов.

Дополнительные сведения см. в статье [Развертывание ресурсов с использованием шаблонов Resource Manager и Azure PowerShell](../azure-resource-manager/resource-group-template-deploy.md#deploy).

## <a name="deploy-with-azure-cli"></a>Развертывание с помощью интерфейса командной строки Azure
Следующий пример создает кластер и его учетную запись хранения и контейнер, вызывая шаблон Resource Manager:

    azure login
    azure config mode arm
    azure group create -n hdi1229rg -l "East US 2"
    azure group deployment create "hdi1229rg" "hdi1229" --template-file "C:\HDITutorials-ARM\hdinsight-arm-template.json" -p "{\"clusterName\":{\"value\":\"hdi1229win\"},\"clusterStorageAccountName\":{\"value\":\"hdi1229store\"},\"location\":{\"value\":\"East US 2\"},\"clusterLoginPassword\":{\"value\":\"Pass@word1\"}}"





## <a name="deploy-with-rest-api"></a>Развертывание с помощью REST API
См. статью [Развертывание ресурсов с использованием шаблонов и REST API Resource Manager](../resource-group-template-deploy-rest.md).

## <a name="deploy-with-visual-studio"></a>Развертывание с помощью Visual Studio
С помощью Visual Studio можно создать проект группы ресурсов и развернуть его в Azure, используя пользовательский интерфейс. Можно выбрать тип ресурсов, добавляемых в проект. Эти ресурсы будут автоматически добавляться в шаблон диспетчера ресурсов. Проект также предоставляет сценарий PowerShell для развертывания шаблона.

Обзорные сведения об использовании групп ресурсов в Visual Studio см. в статье [Создание и развертывание групп ресурсов Azure с помощью Visual Studio](../vs-azure-tools-resource-groups-deployment-projects-create-deploy.md).

## <a name="next-steps"></a>Дальнейшие действия
В этой статье вы ознакомились с несколькими способами создания кластера HDInsight. Для получения дополнительных сведений ознакомьтесь со следующими статьями:

* Пример развертывания ресурсов с помощью клиентской библиотеки .NET см. в статье [Развертывание виртуальной машины Azure с помощью C# и шаблона Resource Manager](../virtual-machines/virtual-machines-windows-csharp-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Подробный пример развертывания приложения см. в статье [Предсказуемые подготовка и развертывание микрослужб в Azure](../app-service-web/app-service-deploy-complex-application-predictably.md).
* Инструкции по развертыванию своего решения в различных средах см. в статье [Среды разработки и тестирования в Microsoft Azure](../solution-dev-test-environments.md).
* Дополнительную информацию о разделах в шаблоне Azure Resource Manager см. в статье [Создание шаблонов диспетчера ресурсов Azure](../resource-group-authoring-templates.md).
* Список функций, которые можно использовать в шаблоне Azure Resource Manager, см. в статье [Функции шаблонов диспетчера ресурсов Azure](../resource-group-template-functions.md).

## <a name="appx-a-resource-manager-template"></a>Приложение A: шаблон Resource Manager
Следующий шаблон диспетчера ресурсов Azure создает кластер Hadoop под управлением Windows с зависимой учетной записью хранения Azure.

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
        "type": "string",
        "defaultValue": "East US 2",
        "allowedValues": [
            "Central US",
            "North Europe",
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
            "Brizil South",
            "Australia East",
            "Australia Southeast",
            "Central India"
        ],
        "metadata": {
            "description": "The location where all azure resources will be deployed."
        }
        },
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
        "clusterStorageAccountName": {
        "type": "string",
        "metadata": {
            "description": "The name of the storage account to be created and be used as the cluster's storage."
        }
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
        "clusterWorkerNodeCount": {
        "type": "int",
        "defaultValue": 4,
        "metadata": {
            "description": "The number of nodes in the HDInsight cluster."
        }
        }
    },
        "variables": {},
        "resources": [
            {
            "name": "[parameters('clusterStorageAccountName')]",
            "type": "Microsoft.Storage/storageAccounts",
            "location": "[parameters('location')]",
            "apiVersion": "2015-05-01-preview",
            "dependsOn": [],
            "tags": {},
            "properties": {
                "accountType": "[parameters('clusterStorageType')]"
            }
            },
            {
            "name": "[parameters('clusterName')]",
            "type": "Microsoft.HDInsight/clusters",
            "location": "[parameters('location')]",
            "apiVersion": "2015-03-01-preview",
            "dependsOn": [
                "[concat('Microsoft.Storage/storageAccounts/',parameters('clusterStorageAccountName'))]"
            ],
            "tags": {},
            "properties": {
                "clusterVersion": "3.2",
                "osType": "Windows",
                "clusterDefinition": {
                "kind": "hadoop",
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
                    "name": "[concat(parameters('clusterStorageAccountName'),'.blob.core.windows.net')]",
                    "isDefault": true,
                    "container": "[parameters('clusterName')]",
                    "key": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('clusterStorageAccountName')), '2015-05-01-preview').key1]"
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
                    }
                    },
                    {
                    "name": "workernode",
                    "targetInstanceCount": "[parameters('clusterWorkerNodeCount')]",
                    "hardwareProfile": {
                        "vmSize": "Large"
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



<!--HONumber=Dec16_HO1-->



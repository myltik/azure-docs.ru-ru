---
title: "Создание кластеров Azure HDInsight (Hadoop) под управлением Windows с помощью PowerShell | Документация Майкрософт"
description: "Узнайте, как создавать кластеры Hadoop под управлением Windows в HDInsight с помощью Azure PowerShell."
services: hdinsight
documentationcenter: 
tags: azure-portal
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: a365e67d-55bc-476e-a126-68f0962ec5aa
ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/17/2017
ms.author: jgao
translationtype: Human Translation
ms.sourcegitcommit: bb700c7de96712666bc4be1f8e430a2e94761f69
ms.openlocfilehash: 9870b61eb00cee169973ae7c09b0c4886cd2db53


---
# <a name="create-windows-based-hadoop-clusters-in-hdinsight-using-azure-powershell"></a>Создание кластеров Hadoop под управлением Windows в HDInsight с помощью Azure PowerShell

[!INCLUDE [selector](../../includes/hdinsight-selector-create-clusters.md)]

Узнайте, как создать кластер Hadoop под управлением Windows в HDInsight с помощью Azure PowerShell. 

Эта статья посвящена только кластерам HDInsight под управлением Windows. Сведения о создании кластеров под управлением Linux см. в статье [Создание кластеров под управлением Linux в HDInsight с помощью Azure PowerShell](hdinsight-hadoop-create-linux-clusters-azure-powershell.md).

> [!IMPORTANT]
> Linux — единственная операционная система, используемая для работы с HDInsight 3.4 или более поздней версии. См. дополнительные сведения о [нерекомендуемых версиях HDInsight в Windows](hdinsight-component-versioning.md#hdi-version-32-and-33-nearing-deprecation-date).

## <a name="prerequisites"></a>Предварительные требования:
[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Прежде чем следовать указаниям в этой статье, необходимо подготовить следующее:

* Подписка Azure. Ознакомьтесь с [бесплатной пробной версией Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* Azure PowerShell.

[!INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

### <a name="access-control-requirements"></a>Требования к контролю доступа
[!INCLUDE [access-control](../../includes/hdinsight-access-control-requirements.md)]

## <a name="create-clusters"></a>Создание кластеров
Azure PowerShell — это полнофункциональная среда сценариев, которую можно использовать для контроля и автоматизации развертывания и управления вашей рабочей нагрузкой в Azure. В этом разделе содержатся указания по созданию кластера HDInsight с помощью Azure PowerShell. Сведения о настройке рабочей станции для запуска командлетов HDInsight Windows PowerShell см. в статье [Установка и настройка Azure PowerShell](/powershell/azureps-cmdlets-docs). Дополнительные сведения об использовании Azure PowerShell с HDInsight см. в статье [Управление кластерами Hadoop в HDInsight с помощью Azure PowerShell](hdinsight-administer-use-powershell.md). Список командлетов HDInsight PowerShell см. в [справочной документации по командлетам PowerShell для HDInsight](https://msdn.microsoft.com/library/azure/dn858087.aspx).

Для создания кластера HDInsight с помощью Azure PowerShell необходимы следующие процедуры:

    ####################################
    # Set these variables
    ####################################
    #region - used for creating Azure service names
    $nameToken = "<Enter an Alias>"
    #endregion

    #region - cluster user accounts
    $httpUserName = "admin"  #HDInsight cluster username
    $httpPassword = "<Enter a Password>"
    #endregion

    ###########################################
    # Service names and varialbes
    ###########################################
    #region - service names
    $namePrefix = $nameToken.ToLower() + (Get-Date -Format "MMdd")

    $resourceGroupName = $namePrefix + "rg"
    $hdinsightClusterName = $namePrefix + "hdi"
    $defaultStorageAccountName = $namePrefix + "store"
    $defaultBlobContainerName = $hdinsightClusterName

    $location = "East US 2"
    $clusterSizeInNodes = 1
    #endregion

    # Treat all errors as terminating
    $ErrorActionPreference = "Stop"

    ###########################################
    # Connect to Azure
    ###########################################
    #region - Connect to Azure subscription
    Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
    try{Get-AzureRmContext}
    catch{Login-AzureRmAccount}
    #endregion

    ###########################################
    # Create the resource group
    ###########################################
    New-AzureRmResourceGroup -Name $resourceGroupName -Location $location

    ###########################################
    # Preapre default storage account and container
    ###########################################
    New-AzureRmStorageAccount `
        -ResourceGroupName $resourceGroupName `
        -Name $defaultStorageAccountName `
        -Type Standard_GRS `
        -Location $location

    $defaultStorageAccountKey = (Get-AzureRmStorageAccountKey `
                                    -ResourceGroupName $resourceGroupName `
                                    -Name $defaultStorageAccountName)[0].Value
    $defaultStorageContext = New-AzureStorageContext `
                                    -StorageAccountName $defaultStorageAccountName `
                                    -StorageAccountKey $defaultStorageAccountKey
    New-AzureStorageContainer `
        -Name $hdinsightClusterName -Context $defaultStorageContext

    ###########################################
    # Create the cluster
    ###########################################

    $httpPW = ConvertTo-SecureString -String $httpPassword -AsPlainText -Force
    $httpCredential = New-Object System.Management.Automation.PSCredential($httpUserName,$httpPW)

    New-AzureRmHDInsightCluster `
        -ResourceGroupName $resourceGroupName `
        -ClusterName $hdinsightClusterName `
        -Location $location `
        -ClusterSizeInNodes $clusterSizeInNodes `
        -ClusterType Hadoop `
        -OSType Windows `
        -Version "3.2" `
        -HttpCredential $httpCredential `
        -DefaultStorageAccountName "$defaultStorageAccountName.blob.core.windows.net" `
        -DefaultStorageAccountKey $defaultStorageAccountKey `
        -DefaultStorageContainer $hdinsightClusterName

    ####################################
    # Verify the cluster
    ####################################
    Get-AzureRmHDInsightCluster -ClusterName $hdinsightClusterName

## <a name="create-clusters-using-resource-manager-template"></a>Создание кластеров с помощью шаблона Resource Manager
Azure PowerShell можно использовать для развертывания шаблона Azure Resource Manager, который создает кластер HDInsight.  См. раздел [Развертывание с помощью PowerShell](hdinsight-hadoop-create-windows-clusters-arm-templates.md#deploy-with-powershell).

## <a name="customize-clusters"></a>Настройка кластеров
* Ознакомьтесь с разделом [Настройка кластеров HDInsight с помощью службы начальной загрузки](hdinsight-hadoop-customize-cluster-bootstrap.md#use-azure-powershell).
* Ознакомьтесь с разделом [Настройка кластеров HDInsight под управлением Windows с помощью действия сценария](hdinsight-hadoop-customize-cluster.md#call-scripts-using-azure-powershell).

## <a name="next-steps"></a>Дальнейшие действия
В этой статье вы ознакомились с несколькими способами создания кластера HDInsight. Для получения дополнительных сведений ознакомьтесь со следующими статьями:

* [Приступая к работе с Azure HDInsight](hdinsight-hadoop-linux-tutorial-get-started.md) — узнайте, как начать работу с кластером HDInsight.
* [Отправка заданий Hadoop программными средствами](hdinsight-submit-hadoop-jobs-programmatically.md) — узнайте, как программными средствами отправлять задания в HDInsight.
* [Управление кластерами Hadoop в HDInsight с использованием PowerShell](hdinsight-administer-use-powershell.md) — узнайте, как работать с HDInsight, используя Azure PowerShell.
* [Документация по пакету SDK для Azure HDInsight][hdinsight-sdk-documentation] — узнайте больше о пакете SDK для HDInsight.

[hdinsight-sdk-documentation]: http://msdn.microsoft.com/library/dn479185.aspx
[azure-preview-portal]: https://manage.windowsazure.com
[connectionmanager]: http://msdn.microsoft.com/library/mt146773(v=sql.120).aspx
[ssispack]: http://msdn.microsoft.com/library/mt146770(v=sql.120).aspx
[ssisclustercreate]: http://msdn.microsoft.com/library/mt146774(v=sql.120).aspx
[ssisclusterdelete]: http://msdn.microsoft.com/library/mt146778(v=sql.120).aspx



<!--HONumber=Jan17_HO4-->



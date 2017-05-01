---
title: "Создание кластеров Azure HDInsight (Hadoop) с помощью PowerShell | Документация Майкрософт"
description: "Узнайте, как создавать кластеры Hadoop, HBase, Storm или Spark на платформе Linux для HDInsight с помощью Azure PowerShell."
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 4208deca-d64a-45e1-8948-2673d5d7678c
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/06/2017
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: 0c4554d6289fb0050998765485d965d1fbc6ab3e
ms.openlocfilehash: a9bd1938c6b0aad2c08d063fd892fea2f8aad56e
ms.lasthandoff: 04/13/2017


---
# <a name="create-linux-based-clusters-in-hdinsight-using-azure-powershell"></a>Создание кластеров под управлением Linux в HDInsight с помощью Azure PowerShell

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Azure PowerShell — это полнофункциональная среда сценариев, которую можно использовать для контроля и автоматизации развертывания и управления вашей рабочей нагрузкой в Microsoft Azure. В этой статье содержится информация о том, как создать кластер HDInsight под управлением Linux с помощью Azure PowerShell, а также приведен пример скрипта.

> [!NOTE]
> Оболочка Azure PowerShell доступна только для клиентов Windows. Если вы используете клиент Linux, Unix или Mac OS X, сведения об использовании интерфейса командной строки Azure для создания кластера см. в статье [Создание кластеров под управлением Linux в HDInsight с помощью Azure CLI](hdinsight-hadoop-create-linux-clusters-azure-cli.md).

## <a name="prerequisites"></a>Предварительные требования
Прежде чем следовать инструкциям в этой статье, необходимо подготовить следующее.

* Подписка Azure. Ознакомьтесь с [бесплатной пробной версией Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps)

    > [!IMPORTANT]
    > Поддержка Azure PowerShell для управления ресурсами HDInsight с помощью диспетчера служб Azure (ASM) объявлена **устаревшей** и будет прекращена с 1 января 2017 г. В описанных в этом документе инструкциях используются новые командлеты HDInsight, которые работают с Azure Resource Manager.
    >
    > Чтобы установить последнюю версию Azure PowerShell, выполните действия из статьи [Установка и настройка Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps). Если у вас есть сценарии, в которые нужно добавить новые командлеты, работающие с Azure Resource Manager, см. статью [Переход к средствам разработки на основе Azure Resource Manager для кластеров HDInsight](hdinsight-hadoop-development-using-azure-resource-manager.md).

### <a name="access-control-requirements"></a>Требования к контролю доступа

[!INCLUDE [access-control](../../includes/hdinsight-access-control-requirements.md)]

## <a name="create-cluster"></a>Создание кластера

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Для создания кластера HDInsight с помощью Azure PowerShell необходимо выполнить следующие процедуры:

* создание группы ресурсов Azure;
* Создание учетной записи хранения Azure
* Создание контейнера BLOB-объектов Azure
* Создание кластера HDInsight

Чтобы вы могли создавать кластеры на платформе Linux, укажите два главных параметра, которые определяют тип ОС и предоставляют сведения о пользователе SSH.

* Укажите для параметра **-OSType** значение **Linux**.
* Для использования SSH в удаленных сеансах кластеров можно указать пароль пользователя SSH или открытый ключ SSH. Если указать и пароль пользователя SSH, и открытый ключ SSH, ключ будет игнорироваться. Если вам нужно использовать ключ SSH для удаленных сеансов, оставьте поле пустым при запросе пароля SSH. См. дополнительные сведения об [использовании SSH в HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

Следующий сценарий демонстрирует создание нового кластера.

    $token ="<SpecifyAnUniqueString>"
    $subscriptionID = "<SubscriptionName>"        # Provide your Subscription Name

    $resourceGroupName = $token + "rg"      # Provide a Resource Group name
    $clusterName = $token
    $defaultStorageAccountName = $token + "store"   # Provide a Storage account name
    $defaultStorageContainerName = $token + "container"
    $location = "East US 2"     # Change the location if needed
    $clusterNodes = 1           # The number of nodes in the HDInsight cluster

    # Sign in to Azure
    Login-AzureRmAccount

    # Select the subscription to use if you have multiple subscriptions
    Select-AzureRmSubscription -SubscriptionId $subscriptionID

    # Create an Azure Resource Group
    New-AzureRmResourceGroup -Name $resourceGroupName -Location $location

    # Create an Azure Storage account and container used as the default storage
    New-AzureRmStorageAccount `
        -ResourceGroupName $resourceGroupName `
        -StorageAccountName $defaultStorageAccountName `
        -Location $location `
        -Type Standard_LRS
    $defaultStorageAccountKey = (Get-AzureRmStorageAccountKey -Name $defaultStorageAccountName -ResourceGroupName $resourceGroupName)[0].Value
    $destContext = New-AzureStorageContext -StorageAccountName $defaultStorageAccountName -StorageAccountKey $defaultStorageAccountKey
    New-AzureStorageContainer -Name $defaultStorageContainerName -Context $destContext

    # Create an HDInsight cluster
    $credentials = Get-Credential -Message "Enter Cluster user credentials" -UserName "admin"
    $sshCredentials = Get-Credential -Message "Enter SSH user credentials"

    # The location of the HDInsight cluster must be in the same data center as the Storage account.
    $location = Get-AzureRmStorageAccount -ResourceGroupName $resourceGroupName -StorageAccountName $defaultStorageAccountName | %{$_.Location}

    New-AzureRmHDInsightCluster `
        -ClusterName $clusterName `
        -ResourceGroupName $resourceGroupName `
        -HttpCredential $credentials `
        -Location $location `
        -DefaultStorageAccountName "$defaultStorageAccountName.blob.core.windows.net" `
        -DefaultStorageAccountKey $defaultStorageAccountKey `
        -DefaultStorageContainer $defaultStorageContainerName  `
        -ClusterSizeInNodes $clusterNodes `
        -ClusterType Hadoop `
        -OSType Linux `
        -Version "3.4" `
        -SshCredential $sshCredentials

Значения, указываемые для параметра **$clusterCredentials** , используются для создания учетной записи Hadoop в кластере. Эта учетная запись будет использоваться для подключения к кластеру.

Значения, указываемые для параметра **$sshCredentials** , используются для создания пользователя SSH в кластере. Используйте эту учетную запись для запуска удаленного сеанса SSH в кластере и выполнения заданий.

> [!IMPORTANT]
> В этом скрипте необходимо указать количество узлов рабочей роли, которые будут находиться в кластере. Если вы планируете использовать более 32 узлов рабочей роли (при создании кластера или в ходе масштабирования после создания кластера), для головного узла потребуется минимум 8-ядерный процессор и 14 ГБ ОЗУ.
>
> Дополнительные сведения о размерах узлов и их стоимости см. на странице с [ценами на HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

Операция создания кластера может занять до 20 минут.

## <a name="create-cluster-configuration-object"></a>Создание кластера: объект конфигурации

Можно также создать объект конфигурации HDInsight с помощью командлета `New-AzureRmHDInsightClusterConfig`. Затем можно изменить этот объект конфигурации, чтобы включить дополнительные параметры конфигурации для кластера. Наконец, используйте параметр `-Config` командлета `New-AzureRmHDInsightCluster`, чтобы использовать эту конфигурацию.

Приведенный ниже сценарий создает объект конфигурации для настройки R Server для типа кластера HDInsight. Конфигурация включает граничный узел, RStudio и дополнительную учетную запись хранения.

    # Create another storage account used as additional storage account
    $additionalStorageAccountName = $token + "store2"
    New-AzureRmStorageAccount -ResourceGroupName $resourceGroupName `
        -StorageAccountName $additionalStorageAccountName `
        -Location $location `
        -Type Standard_LRS
    $additionalStorageAccountKey = (Get-AzureRmStorageAccountKey -Name $additionalStorageAccountName -ResourceGroupName $resourceGroupName)[0].Value

    # Create a new configuration for RServer cluster type
    # Use -EdgeNodeSize to set the size of the edge node for RServer clusters
    # if you want a specific size. Otherwise, the default size is used.
    $config = New-AzureRmHDInsightClusterConfig `
        -ClusterType "RServer" `
        -EdgeNodeSize "Standard_D12_v2"

    # Add RStudio to the configuration
    $rserverConfig = @{"RStudio"="true"}
    $config = $config | Add-AzureRmHDInsightConfigValues `
        -RServer $rserverConfig

    # Add an additional storage account
    Add-AzureRmHDInsightStorage -Config $config -StorageAccountName "$additionalStorageAccountName.blob.core.windows.net" -StorageAccountKey $additionalStorageAccountKey

    # Create a new HDInsight cluster
    New-AzureRmHDInsightCluster `
        -ClusterName $clusterName `
        -ResourceGroupName $resourceGroupName `
        -HttpCredential $credentials `
        -Location $location `
        -DefaultStorageAccountName "$defaultStorageAccountName.blob.core.windows.net" `
        -DefaultStorageAccountKey $defaultStorageAccountKey `
        -DefaultStorageContainer $defaultStorageContainerName  `
        -ClusterSizeInNodes $clusterNodes `
        -OSType Linux `
        -Version "3.5" `
        -SshCredential $sshCredentials `
        -Config $config

> [!WARNING]
> Использование учетной записи хранения, расположение которой отличается от расположения кластера HDInsight, не поддерживается. При использовании этого примера создайте дополнительную учетную запись хранения в том же расположении, что и сервер.

## <a name="customize-clusters"></a>Настройка кластеров

* Ознакомьтесь с разделом [Настройка кластеров HDInsight с помощью службы начальной загрузки](hdinsight-hadoop-customize-cluster-bootstrap.md#use-azure-powershell).
* См. статью [Настройка кластеров HDInsight под управлением Windows с помощью действия сценария](hdinsight-hadoop-customize-cluster-linux.md).

## <a name="delete-the-cluster"></a>Удаление кластера

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="next-steps"></a>Дальнейшие действия

Теперь, когда вы успешно создали кластер HDInsight, обратитесь к следующим ресурсам, чтобы научиться с ним работать.

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



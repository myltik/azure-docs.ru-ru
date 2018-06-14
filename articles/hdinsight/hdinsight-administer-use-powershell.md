---
title: Управление кластерами Hadoop в HDInsight с помощью PowerShell — Azure | Документы Майкрософт
description: Узнайте, как осуществлять управление кластерами Hadoop в HDInsight с использованием Azure PowerShell.
services: hdinsight
editor: cgronlun
manager: jhubbard
tags: azure-portal
author: mumian
documentationcenter: ''
ms.assetid: bfdfa754-18e5-4ef9-b0d6-2dbdcebc0283
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 05/14/2018
ms.author: jgao
ms.openlocfilehash: 656ad3673835bb30499931d20fe715e85418b1c3
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/16/2018
ms.locfileid: "34200832"
---
# <a name="manage-hadoop-clusters-in-hdinsight-by-using-azure-powershell"></a>Управление кластерами Hadoop в HDInsight с помощью Azure PowerShell
[!INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

Azure PowerShell можно использовать для контроля и автоматизации развертывания рабочих нагрузок, а также управления ими в Azure. В этой статье вы узнаете, как управлять кластерами Hadoop в Azure HDInsight с помощью Azure PowerShell. Список командлетов HDInsight PowerShell см. в [справочнике по командлетам HDInsight][hdinsight-powershell-reference].

**Предварительные требования**

Перед началом работы с этой статьей необходимо иметь следующее:

* **Подписка Azure**. См. страницу [бесплатной пробной версии Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

## <a name="install-azure-powershell"></a>Установите Azure PowerShell
[!INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

Если вы установили Azure PowerShell версии 0.9x, то перед установкой новой версии ее необходимо удалить.

Проверка версии установленной оболочки PowerShell:

```powershell
Get-Module *azure*
```

Для удаления старой версии запустите "Программы и компоненты" в панели управления.

## <a name="create-clusters"></a>Создание кластеров
Ознакомьтесь с разделом [Создание кластеров под управлением Linux в HDInsight с помощью Azure PowerShell](hdinsight-hadoop-create-linux-clusters-azure-powershell.md)

## <a name="list-clusters"></a>Получение списка кластеров
Чтобы получить список всех кластеров в текущей подписке, используйте следующую команду:

```powershell
Get-AzureRmHDInsightCluster
```

## <a name="show-cluster"></a>Отображение кластеров
Чтобы отобразить сведения о конкретном кластере в текущей подписке, используйте следующую команду:

```powershell
Get-AzureRmHDInsightCluster -ClusterName <Cluster Name>
```

## <a name="delete-clusters"></a>Удаление кластеров
Используйте следующую команду для удаления кластера:

```powershell
Remove-AzureRmHDInsightCluster -ClusterName <Cluster Name>
```

Можно также удалить кластер, удалив группу ресурсов, которая содержит этот кластер. Удаление группы ресурсов приведет к удалению всех ресурсов в группе, включая учетную запись хранения по умолчанию.

```powershell
Remove-AzureRmResourceGroup -Name <Resource Group Name>
```

## <a name="scale-clusters"></a>Масштабирование кластеров
Масштабирование кластера позволяет изменить количество рабочих узлов в кластере, который работает под управлением Azure HDInsight. При этом не требуется повторно создавать кластер.

> [!NOTE]
> Поддерживаются только кластеры HDInsight версии 3.1.3 или более поздней. Если вы не знаете версию кластера, см. страницу «Свойства».  См. раздел [Отображение кластеров](hdinsight-administer-use-portal-linux.md#list-and-show-clusters).
>
>

Ниже представлены возможности, связанные с изменением количества узлов данных в кластере каждого типа, поддерживаемого в HDInsight.

* Hadoop

    Вы можете легко увеличить количество рабочих узлов в работающем кластере Hadoop. Это не помешает обработке заданий в состоянии ожидания и выполнения. В ходе выполнения операции можно также отправлять новые задания. Сбои операции масштабирования обрабатываются корректно, поэтому кластер всегда пребывает в функциональном состоянии.

    Если уменьшить масштаб кластера Hadoop, сократив количество узлов данных, некоторые службы в нем будут перезапущены. Перезапуск служб приведет к сбою всех выполняющихся и ожидающих заданий при завершении операции масштабирования. Однако после завершения операции вы можете повторно отправить задания.
* hbase

    Вы можете с легкостью добавлять и удалять узлы данных в работающем кластере HBase. Балансировка региональных серверов выполняется автоматически в течение нескольких минут после завершения операции масштабирования. Но их также можно сбалансировать вручную, выполнив вход на головной узел кластера и выполнив приведенные ниже команды в окне командной строки.

    ```bash
    >pushd %HBASE_HOME%\bin
    >hbase shell
    >balancer
    ```

* Storm

    Вы можете с легкостью добавлять и удалять узлы данных в работающем кластере Storm. Но после успешного завершения операции масштабирования потребуется повторная балансировка топологии.

    Повторную балансировку можно выполнить двумя способами:

  * с помощью веб-интерфейса Storm;
  * с помощью программы командной строки.

    Дополнительные сведения см. в [документации по Apache Storm](http://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html).

    В кластере HDInsight доступен веб-интерфейс Storm.

    ![HDInsight, storm, масштабирование, перераспределение](./media/hdinsight-administer-use-management-portal/hdinsight.portal.scale.cluster.png)

    Ниже приведен пример использования команды CLI для повторной балансировки топологии Storm:

    ```cli
    ## Reconfigure the topology "mytopology" to use 5 worker processes,
    ## the spout "blue-spout" to use 3 executors, and
    ## the bolt "yellow-bolt" to use 10 executors
    $ storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10
    ```

Чтобы изменить размер кластера Hadoop с помощью Azure PowerShell, выполните следующую команду с клиентского компьютера:

```powershell
Set-AzureRmHDInsightClusterSize -ClusterName <Cluster Name> -TargetInstanceCount <NewSize>
```


## <a name="grantrevoke-access"></a>Предоставление и отмена доступа
В кластерах HDInsight имеются следующие веб-службы HTTP (все эти службы имеют конечные точки RESTful):

* ODBC
* JDBC
* Ambari
* Oozie
* Templeton

По умолчанию эти службы предоставляются для доступа. Вы можете отменить или предоставить доступ. Для отмены:

```powershell
Revoke-AzureRmHDInsightHttpServicesAccess -ClusterName <Cluster Name>
```

Для предоставления:

```powershell
$clusterName = "<HDInsight Cluster Name>"

# Credential option 1
$hadoopUserName = "admin"
$hadoopUserPassword = "<Enter the Password>"
$hadoopUserPW = ConvertTo-SecureString -String $hadoopUserPassword -AsPlainText -Force
$credential = New-Object System.Management.Automation.PSCredential($hadoopUserName,$hadoopUserPW)

# Credential option 2
#$credential = Get-Credential -Message "Enter the HTTP username and password:" -UserName "admin"

Grant-AzureRmHDInsightHttpServicesAccess -ClusterName $clusterName -HttpCredential $credential
```

> [!NOTE]
> Предоставляя или отменяя доступ, вы сбрасываете имя пользователя кластера и его пароль.
>
>

Предоставить и отменить доступ можно также на портале. Ознакомьтесь с разделом [Администрирование HDInsight с помощью портала Azure][hdinsight-admin-portal].

## <a name="update-http-user-credentials"></a>Обновление учетных данных пользователя HTTP
Процедура та же, что и при [предоставлении или отмене доступа по протоколу HTTP](#grant/revoke-access). Если кластеру был предоставлен доступ по протоколу HTTP, то сначала его необходимо отменить.  После этого предоставьте доступ с новыми учетными данными пользователя HTTP.

## <a name="find-the-default-storage-account"></a>Поиск учетной записи хранения по умолчанию
В следующем сценарии PowerShell показано получение имени учетной записи хранения по умолчанию и связанной информации.

```powershell
#Connect-AzureRmAccount
$clusterName = "<HDInsight Cluster Name>"

$clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
$storageInfo = $clusterInfo.DefaultStorageAccount.split('.')
$defaultStoreageType = $storageInfo[1]
$defaultStorageName = $storageInfo[0]

echo "Default Storage account name: $defaultStorageName"
echo "Default Storage account type: $defaultStoreageType"

if ($defaultStoreageType -eq "blob")
{
    $defaultBlobContainerName = $cluster.DefaultStorageContainer
    $defaultStorageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $defaultStorageAccountName)[0].Value
    $defaultStorageAccountContext = New-AzureStorageContext -StorageAccountName $defaultStorageAccountName -StorageAccountKey $defaultStorageAccountKey

    echo "Default Blob container name: $defaultBlobContainerName"
    echo "Default Storage account key: $defaultStorageAccountKey"
}
```


## <a name="find-the-resource-group"></a>Поиск группы ресурсов
В режиме Resource Manager каждый кластер HDInsight относится к группе ресурсов Azure.  Поиск группы ресурсов:

```powershell
$clusterName = "<HDInsight Cluster Name>"

$cluster = Get-AzureRmHDInsightCluster -ClusterName $clusterName
$resourceGroupName = $cluster.ResourceGroup
```


## <a name="submit-jobs"></a>Отправка заданий
**Отправка заданий MapReduce**

См. статью [Выполнение примеров Hadoop MapReduce в HDInsight на базе Windows](hdinsight-run-samples.md).

**Отправка заданий Hive**

См. статью [Выполнение запросов Hive с помощью PowerShell](hadoop/apache-hadoop-use-hive-powershell.md).

**Отправка заданий Pig**

См. статью [Выполнение заданий Pig с помощью PowerShell](hadoop/apache-hadoop-use-pig-powershell.md).

**Отправка заданий Sqoop**

См. статью [Использование Sqoop с Hadoop в HDInsight](hadoop/hdinsight-use-sqoop.md).

**Отправка заданий Oozie**

См. статью [Использование Oozie с Hadoop для определения и выполнения рабочего процесса в HDInsight](hdinsight-use-oozie.md).

## <a name="upload-data-to-azure-blob-storage"></a>Отправка данных в хранилище BLOB-объектов Azure
Ознакомьтесь со статьей [Отправка данных в HDInsight][hdinsight-upload-data].

## <a name="see-also"></a>См. также
* [Справочная документация по командлетам HDInsight][hdinsight-powershell-reference]
* [Администрирование HDInsight с помощью портала Azure][hdinsight-admin-portal]
* [Администрирование HDInsight с помощью интерфейса командной строки][hdinsight-admin-cli]
* [Создание кластеров Hadoop в HDInsight][hdinsight-provision]
* [Отправка данных в HDInsight][hdinsight-upload-data]
* [Отправка заданий Hadoop в HDInsight][hdinsight-submit-jobs]
* [Руководство по Hadoop. Начало работы с Hadoop в HDInsight на платформе Linux][hdinsight-get-started]

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[hdinsight-get-started]:hadoop/apache-hadoop-linux-tutorial-get-started.md
[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-provision-custom-options]: hdinsight-hadoop-provision-linux-clusters.md#configuration
[hdinsight-submit-jobs]:hadoop/submit-apache-hadoop-jobs-programmatically.md

[hdinsight-admin-cli]: hdinsight-administer-use-command-line.md
[hdinsight-admin-portal]: hdinsight-administer-use-management-portal.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md
[hdinsight-use-hive]:hadoop/hdinsight-use-hive.md
[hdinsight-use-mapreduce]:hadoop/hdinsight-use-mapreduce.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-flight]: hdinsight-analyze-flight-delay-data.md

[hdinsight-powershell-reference]: https://msdn.microsoft.com/library/dn858087.aspx

[powershell-install-configure]: /powershell/azureps-cmdlets-docs

[image-hdi-ps-provision]: ./media/hdinsight-administer-use-powershell/HDI.PS.Provision.png

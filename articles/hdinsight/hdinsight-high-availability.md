---
title: "Доступность кластеров Hadoop в HDInsight | Документация Майкрософт"
description: "HDInsight развертывает высокодоступные и надежные кластеры с дополнительным головным узлом."
services: hdinsight
tags: azure-portal
editor: cgronlun
manager: jhubbard
author: mumian
documentationcenter: 
ms.assetid: ccab792d-60d6-4287-96c2-479e5d0cf358
ms.service: hdinsight
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 02/06/2017
ms.author: jgao
translationtype: Human Translation
ms.sourcegitcommit: a2b32f23381ed1f9912edf6432f029e51bdf1be4
ms.openlocfilehash: dd28e295df7acead773f9076d790e0e96b66adb9
ms.lasthandoff: 02/07/2017


---
# <a name="availability-and-reliability-of-windows-based-hadoop-clusters-in-hdinsight"></a>Доступность и надежность кластеров Hadoop под управлением Windows в HDInsight
> [!IMPORTANT]
> Действия, описанные в этом документе, относятся только к кластерам HDInsight под управлением Windows. Linux — единственная операционная система, используемая для работы с HDInsight 3.4 или более поздней версии. См. дополнительные сведения о [нерекомендуемых версиях HDInsight в Windows](hdinsight-component-versioning.md#hdi-version-32-and-33-nearing-deprecation-date). Если вы используете кластер под управлением Linux, см. статью [Доступность и надежность кластеров Hadoop в HDInsight](hdinsight-high-availability-linux.md).
>
>

HDInsight позволяет клиентам развертывать различные типы кластеров для рабочих нагрузок анализа различных данных. Предлагаемые сегодня типы кластеров включают кластеры Hadoop для рабочих нагрузок запросов и анализа, кластеры HBase для рабочих нагрузок NoSQL и кластеры Storm для рабочих нагрузок обработки событий в реальном времени. В пределах данного типа кластера существуют разные роли для различных узлов. Например:

* Кластеры Hadoop для HDInsight развертываются с двумя ролями:

  * головной узел (два узла);
  * узел данных (по крайней мере 1 узел).
* Кластеры HBase для HDInsight развертываются с тремя ролями:

  * головные серверы (2 узла);
  * региональные серверы (по крайней мере 1 узел);
  * узлы Master/Zookeeper (3 узла).
* Кластеры Storm для HDInsight развертываются с тремя ролями:

  * узлы Nimbus (2 узла);
  * наблюдающие серверы (по крайней мере 1 узел);
  * узлы Zookeeper (3 узла).

Стандартные реализации кластеров Hadoop обычно имеют один головной узел. HDInsight удаляет эту единственную точку сбоя с добавлением дополнительного головного узла, головного сервера или узла Nimbus, что позволяет повысить доступность и надежность службы, необходимой для управления рабочими нагрузками. Эти головные узлы, головные серверы и узлы Nimbus разработаны для успешного управления сбоями рабочих узлов, однако любые отключения главных служб, работающих на головном узле, приводят к прекращению работы кластера.

Узлы [ZooKeeper](http://zookeeper.apache.org/) (ZK) были добавлены и используются для выбора лидера головных узлов, а также для гарантии, что рабочие узлы и шлюзы (GW) будут иметь информацию о необходимости перехода к вторичному головному узлу при отказе (Головной узел&1;), когда активный головной узел (Головной узел&0;) становится неактивным.

![Схема высоконадежных головных узлов в реализации HDInsight Hadoop.](./media/hdinsight-high-availability/hadoop.high.availability.architecture.diagram.png)

## <a name="check-active-head-node-service-status"></a>Проверка состояния активной службы головного узла
Чтобы проверить, какой из головных узлов является активным, а также проверить состояние работающих на нем служб, необходимо подключиться к кластеру Hadoop по протоколу удаленного рабочего стола (RDP). Инструкции по использованию протокола RDP описаны в статье [Управление кластерами Hadoop в HDInsight с помощью портала Azure](hdinsight-administer-use-management-portal.md#connect-to-clusters-using-rdp). После подключения к кластеру дважды щелкните значок **доступности службы Hadoop**, расположенный на рабочем столе, чтобы получить информацию о том, какой головной узел (Namenode, Jobtracker, Templeton, Oozieservice, Metastore и службы Hiveserver2) работает. В случае HDI 3.0 это будут Namenode, Resource Manager, History Server, Templeton, Oozieservice, Metastore и службы Hiveserver2.

![](./media/hdinsight-high-availability/Hadoop.Service.Availability.Status.png)

На снимке экрана активным головным узлом является *headnode0*.

## <a name="access-log-files-on-the-secondary-head-node"></a>Доступ к файлам журнала на вторичном головном узле
Чтобы получить доступ к журналам заданий на вторичном головном узле, когда он становится активным, можно просмотреть пользовательский интерфейс JobTracker, как и в случае с первичным активным узлом. Чтобы получить доступ к JobTracker, необходимо подключиться к кластеру Hadoop по протоколу удаленного рабочего стола (RDP), как описано в предыдущем разделе. Подключившись к кластеру, дважды щелкните значок **состояния Hadoop NameNode**, расположенный на рабочем столе. Затем щелкните **Журналы NameNode**, чтобы получить доступ к каталогу журналов на вторичном головном узле.

![](./media/hdinsight-high-availability/Hadoop.Head.Node.Log.Files.png)

## <a name="configure-head-node-size"></a>Настройка размера головного узла
По умолчанию головные узлы размещаются как большие виртуальные машины. Этот размер вполне подходит для управления большинством заданий Hadoop, выполняемых в кластере. Однако существуют сценарии, которые могут потребовать использования очень крупных виртуальных машин в качестве головных узлов. В качестве пример можно указать ситуацию, когда кластеру необходимо управлять большим количеством малых заданий Oozie.

Очень крупные виртуальные машины можно настраивать с помощью командлетов Azure PowerShell или пакета SDK для HDInsight.

Создание и подготовка кластера с использованием Azure PowerShell описаны в разделе [Администрирование HDInsight с помощью PowerShell](hdinsight-administer-use-powershell.md). Настройка очень крупных головных узлов требует добавления параметра `-HeadNodeVMSize ExtraLarge` в командлет `New-AzureRmHDInsightcluster`, используемый в этом коде.

    # Create a new HDInsight cluster in Azure PowerShell
    # Configured with an ExtraLarge head-node VM
    New-AzureRmHDInsightCluster `
                -ResourceGroupName $resourceGroupName `
                -ClusterName $clusterName `
                -Location $location `
                -HeadNodeVMSize ExtraLarge `
                -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" `
                -DefaultStorageAccountKey $storageAccountKey `
                -DefaultStorageContainerName $containerName  `
                -ClusterSizeInNodes $clusterNodes

При использовании пакета SDK процедура аналогична. Создание и подготовка кластера с использованием SDK описаны в разделе [Использование пакета SDК для HDInsight .NET](hdinsight-provision-clusters.md). Настройка очень крупных головных узлов требует добавления параметра `HeadNodeSize = NodeVMSize.ExtraLarge` в метод `ClusterCreateParameters()`, используемый в этом коде.

    # Create a new HDInsight cluster with the HDInsight SDK
    # Configured with an ExtraLarge head-node VM
    ClusterCreateParameters clusterInfo = new ClusterCreateParameters()
    {
        Name = clustername,
        Location = location,
        HeadNodeSize = NodeVMSize.ExtraLarge,
        DefaultStorageAccountName = storageaccountname,
        DefaultStorageAccountKey = storageaccountkey,
        DefaultStorageContainer = containername,
        UserName = username,
        Password = password,
        ClusterSizeInNodes = clustersize
    };


## <a name="next-steps"></a>Дальнейшие действия
* [Apache ZooKeeper](http://zookeeper.apache.org/)
* [Подключение к кластерам HDInsight с использованием RDP](hdinsight-administer-use-management-portal.md#connect-to-clusters-using-rdp)
* [Использование пакета SDК для HDInsight .NET](hdinsight-provision-clusters.md)


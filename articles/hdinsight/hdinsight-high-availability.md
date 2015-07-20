<properties 
	pageTitle="Доступность кластеров Hadoop в HDInsight | Microsoft Azure" 
	description="HDInsight развертывает высокодоступные и надежные кластеры с дополнительным головным узлом." 
	services="hdinsight" 
	editor="cgronlun" 
	manager="paulettm" 
	authors="bradsev" 
	documentationCenter=""/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="05/19/2014" 
	ms.author="bradsev"/>


#Доступность и надежность кластеров Hadoop в HDInsight


К кластерам Hadoop, развернутым в Azure HDInsight, добавлен второй головной узел для повышения доступности и надежности службы, необходимой для управления рабочими нагрузками. Стандартные реализации кластеров Hadoop обычно имеют один головной узел. Эти кластеры разработаны для успешного управления сбоями рабочих узлов, однако любые отключения главных служб, работающих на головном узле, приводят к прекращению работы кластера.

![Схема высоконадежных головных узлов в реализации HDInsight Hadoop.](http://i.imgur.com/jrUmrH4.png)

HDInsight устраняет эту единственную точку отказа благодаря добавлению вторичного головного узла (Головной узел 1). Узлы [ZooKeeper](http://zookeeper.apache.org/) (ZK), которые были добавлены и используются для выбора лидера головных узлов, а также для гарантии, что рабочие узлы и шлюзы (GW) будут иметь информацию о необходимости перехода к вторичному головному узлу при отказе (Головной узел 1), когда активный головной узел (Головной узел 0) становится неактивным.


## Как проверить состояние службы на активном головном узле ##
Чтобы проверить, какой из головных узлов является активным, а также проверить состояние работающих на нем служб, необходимо подключиться к кластеру Hadoop по протоколу удаленного рабочего стола (RDP). Возможность удаленного использования кластера в Azure по умолчанию отключена, поэтому ее необходимо сначала включить. Указания о том, как это сделать на портале, см. в разделе [Подключение к кластерам HDInsight с использованием RDP](hdinsight-administer-use-management-portal.md#rdp). После удаленного подключения к кластеру дважды щелкните значок **Состояние доступности службы Hadoop**, расположенный на рабочем столе, чтобы получить информацию о том, какой головной узел (Namenode, Jobtracker, Templeton, Oozieservice, Metastore и службы Hiveserver2) работает. В случае HDI 3.0 это будут Namenode, Resource Manager, History Server, Templeton, Oozieservice, Metastore и службы Hiveserver2.

![](http://i.imgur.com/MYTkCHW.png)


## Как обращаться к файлам журнала на вторичном головном узле \\

Чтобы получить доступ к журналам заданий на вторичном головном узле, когда он становится активным, можно просмотреть пользовательский интерфейс JobTracker, как и в случае с первичным активным узлом. Чтобы получить доступ к JobTracker, необходимо подключиться к кластеру Hadoop по протоколу удаленного рабочего стола (RDP), как описано в предыдущем разделе. После удаленного подключения к кластеру дважды щелкните значок **Hadoop NameNode**, расположенный на рабочем столе, а затем щелкните **Журналы NameNode**, чтобы получить доступ к каталогу журналов на вторичном головном узле.

![](http://i.imgur.com/eL6jzgB.png)


## Как настроить размер головного узла ##
По умолчанию головные узлы размещаются как большие виртуальные машины. Этот размер вполне подходит для управления большинством заданий Hadoop, выполняемых в кластере. Однако существуют сценарии, которые могут потребовать использования очень крупных виртуальных машин в качестве головных узлов. В качестве пример можно указать ситуацию, когда кластеру необходимо управлять большим количеством малых заданий Oozie.

Очень крупные виртуальные машины можно настраивать с помощью командлетов Azure PowerShell или пакета SDK для HDInsight.

Создание и подготовка кластера с использованием Azure PowerShell описаны в разделе [Администрирование HDInsight с помощью PowerShell](hdinsight-administer-use-powershell.md). Настройка очень крупных головных узлов требует добавления параметра `-HeadNodeVMSize ExtraLarge` в командлет `New-AzureHDInsightcluster`, используемый в этом коде.

    # Create a new HDInsight cluster in Azure PowerShell
	# Configured with an ExtraLarge head-node VM
    New-AzureHDInsightCluster -Name $clusterName -Location $location -HeadNodeVMSize ExtraLarge -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" -DefaultStorageAccountKey $storageAccountKey -DefaultStorageContainerName $containerName  -ClusterSizeInNodes $clusterNodes

При использовании пакета SDK процедура аналогична. Создание и подготовка кластера с использованием SDK описаны в разделе [Использование пакета SDК для HDInsight .NET](hdinsight-provision-clusters.md#sdk). Настройка очень крупных головных узлов требует добавления параметра `HeadNodeSize = NodeVMSize.ExtraLarge` в метод `ClusterCreateParameters()`, используемый в этом коде.

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


**Справочные материалы**

- [ZooKeeper](http://zookeeper.apache.org/)
- [Подключение к кластерам HDInsight с использованием RDP](hdinsight-administer-use-management-portal.md#rdp)
- [Использование пакета SDК для HDInsight .NET](hdinsight-provision-clusters.md#sdk) 







 

<!---HONumber=July15_HO2-->
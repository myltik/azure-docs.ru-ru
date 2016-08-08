<properties
	pageTitle="Доступность кластеров Hadoop в HDInsight | Microsoft Azure"
	description="HDInsight развертывает высокодоступные и надежные кластеры с дополнительным головным узлом."
	services="hdinsight"
	tags="azure-portal"
	editor="cgronlun"
	manager="paulettm"
	authors="mumian"
	documentationCenter=""/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="multiple"
	ms.topic="article"
	ms.date="07/25/2016"
	ms.author="jgao"/>


#Доступность и надежность кластеров Hadoop под управлением Windows в HDInsight


>[AZURE.NOTE] Действия, описанные в этом документе, относятся только к кластерам HDInsight под управлением Windows. Если вы используете кластер под управлением Linux, см. статью [Доступность и надежность кластеров Hadoop под управлением Linux в HDInsight](hdinsight-high-availability-linux.md).

HDInsight позволяет клиентам развертывать различные типы кластеров для рабочих нагрузок анализа различных данных. Предлагаемые сегодня типы кластеров включают кластеры Hadoop для рабочих нагрузок запросов и анализа, кластеры HBase для рабочих нагрузок NoSQL и кластеры Storm для рабочих нагрузок обработки событий в реальном времени. В пределах данного типа кластера существуют разные роли для различных узлов. Например:



- Кластеры Hadoop для HDInsight развертываются с двумя ролями:
	- головной узел (два узла);
	- узел данных (по крайней мере 1 узел).

- Кластеры HBase для HDInsight развертываются с тремя ролями:
	- головные серверы (2 узла);
	- региональные серверы (по крайней мере 1 узел);
	- узлы Master/Zookeeper (3 узла).

- Кластеры Storm для HDInsight развертываются с тремя ролями:
	- узлы Nimbus (2 узла);
	- наблюдающие серверы (по крайней мере 1 узел);
	- узлы Zookeeper (3 узла).

Стандартные реализации кластеров Hadoop обычно имеют один головной узел. HDInsight удаляет эту единственную точку сбоя с добавлением дополнительного головного узла, головного сервера или узла Nimbus, что позволяет повысить доступность и надежность службы, необходимой для управления рабочими нагрузками. Эти головные узлы, головные серверы и узлы Nimbus разработаны для успешного управления сбоями рабочих узлов, однако любые отключения главных служб, работающих на головном узле, приводят к прекращению работы кластера.


Узлы [ZooKeeper](http://zookeeper.apache.org/) (ZK), которые были добавлены и используются для выбора лидера головных узлов, а также для гарантии, что рабочие узлы и шлюзы (GW) будут иметь информацию о необходимости перехода к вторичному головному узлу при отказе (Головной узел 1), когда активный головной узел (Головной узел 0) становится неактивным.

![Схема высоконадежных головных узлов в реализации HDInsight Hadoop.](./media/hdinsight-high-availability/hadoop.high.availability.architecture.diagram.png)




## Проверка состояния активной службы головного узла
Чтобы проверить, какой из головных узлов является активным, а также проверить состояние работающих на нем служб, необходимо подключиться к кластеру Hadoop по протоколу удаленного рабочего стола (RDP). Для получения инструкций о RDP см. статью [Управление кластерами Hadoop в HDInsight с использованием портала Azure](hdinsight-administer-use-management-portal.md#connect-to-hdinsight-clusters-by-using-rdp). После удаленного подключения к кластеру дважды щелкните значок **Доступность службы Hadoop**, расположенный на рабочем столе, чтобы получить информацию о том, какой головной узел (Namenode, Jobtracker, Templeton, Oozieservice, Metastore и службы Hiveserver2) работает. В случае HDI 3.0 это будут Namenode, Resource Manager, History Server, Templeton, Oozieservice, Metastore и службы Hiveserver2.

![](./media/hdinsight-high-availability/Hadoop.Service.Availability.Status.png)

На снимке экрана активным головным узлом является *headnode0*.

## Доступ к файлам журнала на вторичном головном узле

Чтобы получить доступ к журналам заданий на вторичном головном узле, когда он становится активным, можно просмотреть пользовательский интерфейс JobTracker, как и в случае с первичным активным узлом. Чтобы получить доступ к JobTracker, необходимо подключиться к кластеру Hadoop по протоколу удаленного рабочего стола (RDP), как описано в предыдущем разделе. После удаленного подключения к кластеру дважды щелкните значок **Состояние Hadoop NameNode**, расположенный на рабочем столе, а затем щелкните **Журналы NameNode**, чтобы получить доступ к каталогу журналов на вторичном головном узле.

![](./media/hdinsight-high-availability/Hadoop.Head.Node.Log.Files.png)


## Настройка размера головного узла
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


## Дальнейшие действия

- [Apache ZooKeeper](http://zookeeper.apache.org/)
- [Подключение к кластерам HDInsight с использованием RDP](hdinsight-administer-use-management-portal.md#rdp)
- [Использование пакета SDК для HDInsight .NET](hdinsight-provision-clusters.md#sdk)

<!---HONumber=AcomDC_0727_2016-->
<properties 
	pageTitle="Доступность Hadoop кластеров в HDInsight - Azure" 
	description="HDInsight развертывает кластеры с высокой степенью доступности и надежности." 
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
	ms.date="11/10/2014" 
	ms.author="bradsev"/>


#Доступность и надежность кластеров Hadoop в HDInsight

## Введение ##
К кластерам Hadoop, развернутым в HDInsight, добавлен второй головной узел для повышения доступности и надежности службы, необходимой для управления рабочими нагрузками. Стандартные реализации кластеров Hadoop обыкновенно имеют один головной узел. Эти кластеры разработаны для успешного управления сбоями рабочих узлов, однако любые отключения главных служб, работающих на головном узле приводят к прекращению работы кластера. 

![](http://i.imgur.com/jrUmrH4.png)

HDInsight устраняет эту единственную точку отказа благодаря добавлению второго головного узла (Головной узел 1). [ZooKeeper][zookeeper] - узлы (ZK), которые были добавлены и используются для выбора лидера головных узлов, а также для гарантии, что рабочие узлы и шлюзы (GW) будут иметь информацию о необходимости перехода ко второму головному узлу при отказе (Головной узел1), когда активный головной узел (Головной узел 0) становится неактивным.


## Проверка состояния службы на активном головном узле ##
Чтобы проверить, какой из головных узлов является активным,а также проверить состояние работающих на нем служб, необходимо подключиться к кластеру Hadoop с использованием протокола удаленного рабочего стола (RDP). Возможность перехода к удаленному кластеру в Azure о умолчанию выключена, поэтому ее необходимо сначала включить. Инструкции о том, как это сделать на портале, см. в разделе [Подключение к кластерам HDInsight с использованием RDP](../hdinsight-administer-use-management-portal/#rdp)
После подключения к удаленному кластеру дважды щелкните значок **Состояние доступности службы Hadoop**, расположенный на рабочем столе, чтобы получить информацию о том, какой головной узел (Namenode, Jobtracker, Templeton, Oozieservice, Metastore и службы Hiveserver2) работает. В случае HDI 3.0 это будут Namenode, Resource Manager, History Server, Templeton, Oozieservice, Metastore и Hiveserver2.

![](http://i.imgur.com/MYTkCHW.png)


## Доступ к файлам журнала на вторичном головном узле ##

Чтобы получить доступ к журналам заданий на вторичном головном узле, когда он становится активным, можно просмотреть пользовательский интерфейс JobTracker, как и в случае с первичным активным узлом. Чтобы получить доступ к Job Tracker, необходимо подключиться к кластеру Hadoop с использованием протокола удаленного рабочего стола (RDP), как описано в предыдущем разделе. После подключения к удаленному кластеру дважды щелкните значок **Hadoop Name Node**, расположенный на рабочем столе, а затем щелкните **Журналы NameNode**, чтобы получить доступ к каталогу журналов вторичного головного узла.

![](http://i.imgur.com/eL6jzgB.png)


## Настройка размеров головного узла ##
По умолчанию головные узлы размещаются как большие ВМ. Этот размер вполне подходит для управления большинством заданий Hadoop, выполняемых в кластере. Однако существуют сценарии, которые могут потребовать использования очень крупных ВМ в качестве головных узлов. В качестве пример можно указать ситуацию, когда кластеру необходимо управлять большим количеством малых заданий Oozie. 

Очень крупные ВМ могут настраиваться с помощью командлетов Azure PowerShell или с использованием пакета SDK для HDInsight.

Создание и подготовка кластера с помощью PowerShell описывается в разделе [Администрирование HDInsight с использованием PowerShell](../hdinsight-administer-use-powershell/). Настройка очень крупных головных узлов требует добавления параметра `-HeadNodeVMSize ExtraLarge` в командлет  `New-AzureHDInsightcluster`, используемый в этом коде.

    # Create a new HDInsight cluster in Azure PowerShell
	# Configured with an ExtraLarge Headnode VM
    New-AzureHDInsightCluster -Name $clusterName -Location $location -HeadNodeVMSize ExtraLarge -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" -DefaultStorageAccountKey $storageAccountKey -DefaultStorageContainerName $containerName  -ClusterSizeInNodes $clusterNodes

При использовании пакета SDK процедура аналогична. Создание и подготовка кластера с использованием SDK описывается в разделе [Использование пакета SDК для HDInsight](../hdinsight-provision-clusters/#sdk). Настройка очень крупных головных узлов требует добавления параметра `HeadNodeSize = NodeVMSize.ExtraLarge` в метод `ClusterCreateParameters()`, используемый в этом коде.

    # Create a new HDInsight cluster with the HDInsight SDK
	# Configured with an ExtraLarge Headnode VM
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


**Ссылки**	

- [ZooKeeper][zookeeper]
- [Подключение к кластерам HDInsight с использованием RDP](../hdinsight-administer-use-management-portal/#rdp)
- [Использование пакета SDK HDInsight .NET](../hdinsight-provision-clusters/#sdk) 


[zookeeper]: http://zookeeper.apache.org/ 






<!--HONumber=42-->

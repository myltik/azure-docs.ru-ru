<properties urlDisplayName="HDInsight High Availability" pageTitle="Доступность Hadoop кластеров в HDInsight | Azure" metaKeywords="hdinsight, hadoop, hdinsight hadoop, hadoop azure" description="HDInsight развертывает кластеры с высокой степенью доступности и надежности." services="HDInsight" umbracoNaviHide="0" disqusComments="1" editor="cgronlun" manager="paulettm" title="Доступность Hadoop кластеров в HDInsight" authors="bradsev" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="bradsev" />

# Доступность и надежность кластеров Hadoop в HDInsight

## Введение

К кластерам Hadoop, развернутым в HDInsight, добавлен второй головной узел для повышения доступности и надежности службы, необходимой для управления рабочими нагрузками. Стандартные реализации кластеров Hadoop обыкновенно имеют один головной узел. Эти кластеры разработаны для успешного управления сбоями рабочих узлов, однако любые отключения главных служб, работающих на головном узле приводят к прекращению работы кластера.

![][0]

HDInsight устраняет эту единственную точку отказа благодаря добавлению второго головного узла (Головной узел 1). [ZooKeeper][ZooKeeper] — узлы (ZK), которые были добавлены и используются для выбора лидера головных узлов, а также для гарантии, что рабочие узлы и шлюзы (GW) будут иметь информацию о необходимости перехода ко второму головному узлу при отказе (Головной узел1), когда активный головной узел (Головной узел 0) становится неактивным.

## Проверка состояния службы на активном головном узле

Чтобы проверить, какой из головных узлов является активным,а также проверить состояние работающих на нем служб, необходимо подключиться к кластеру Hadoop с использованием протокола удаленного рабочего стола (RDP). Возможность перехода к удаленному кластеру в Azure о умолчанию выключена, поэтому ее необходимо сначала включить. Инструкции, как это сделать на портале см. в разделе [Подключение к кластерам HDInsight с помощью протокола RDP][Подключение к кластерам HDInsight с помощью протокола RDP]
После подключения к удаленном кластеру щелкните дважды по значку **Состояние доступности службы Hadoop**, расположенному на рабочем столе, чтобы получить состояние о том, какой головной узел (Namenode, Jobtracker, Templeton, Oozieservice, Metastore и службы Hiveserver2) работает или для HDI 3.0 это будут Namenode, Resource Manager, History Server, Templeton, Oozieservice, Metastore и Hiveserver2.

![][1]

## Доступ к файлам журнала на вторичном головном узле

Чтобы получить доступ к журналам заданий на вторичном головном узле, когда он становится активным, можно просмотреть пользовательский интерфейс JobTracker, как и в случае с первичным активным узлом. Чтобы получить доступ к Job Tracker, необходимо подключиться к кластеру Hadoop с использование протокола удаленного рабочего стола (RDP), как описано в предыдущем разделе. После подключения к удаленному кластеру дважды щелкните мышкой по значку **Hadoop Name Node**, расположенному на рабочем столе, а затем щелкните **Журналы NameNode** для получения доступа к каталогу журналов вторичного головного узла.

![][2]

## Настройка размеров головного узла

По умолчанию головные узлы размещаются как большие ВМ. Этот размер вполне подходит для управления большинством заданий Hadoop, выполняемых в кластере. Однако существуют сценарии, которые могут потребовать использования очень крупных ВМ в качестве головных узлов. В качестве пример можно указать ситуацию, когда кластеру необходимо управлять большим количеством малых заданий Oozie.

Очень крупные ВМ могут настраиваться с помощью командлетов Azure PowerShell или с использованием пакета SDK для HDInsight.

Создание и подготовка кластера с использованием PowerShell см. в разделе [Администрирование HDInsight с помощью PowerShell][Администрирование HDInsight с помощью PowerShell]. Настройка очень крупных головных узлов требует добавления параметра `-HeadNodeVMSize ExtraLarge` в командлет `New-AzureHDInsightcluster`, используемый в этом коде.

    # Create a new HDInsight cluster in Azure PowerShell
    # Configured with an ExtraLarge Headnode VM
    New-AzureHDInsightCluster -Name $clusterName -Location $location -HeadNodeVMSize ExtraLarge -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" -DefaultStorageAccountKey $storageAccountKey -DefaultStorageContainerName $containerName  -ClusterSizeInNodes $clusterNodes

При использовании пакета SDK процедура аналогична. Создание и подготовка кластера с использованием SDK см. в разделе [Использование пакета SDК для HDInsight][Использование пакета SDК для HDInsight]. Настройка очень крупных головных узлов требует добавления параметра `HeadNodeSize = NodeVMSize.ExtraLarge` в метод `ClusterCreateParameters()`, используемый в этом коде.

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

-   [ZooKeeper][ZooKeeper]
-   [Подключение к кластерам HDInsight с использованием RDP][Подключение к кластерам HDInsight с помощью протокола RDP]
-   [Использование пакета HDInsight .NET SDK][Использование пакета SDК для HDInsight]

  [0]: http://i.imgur.com/jrUmrH4.png
  [ZooKeeper]: http://zookeeper.apache.org/
  [Подключение к кластерам HDInsight с помощью протокола RDP]: ../hdinsight-administer-use-management-portal/#rdp
  [1]: http://i.imgur.com/MYTkCHW.png
  [2]: http://i.imgur.com/eL6jzgB.png
  [Администрирование HDInsight с помощью PowerShell]: ../hdinsight-administer-use-powershell/
  [Использование пакета SDК для HDInsight]: ../hdinsight-provision-clusters/#sdk

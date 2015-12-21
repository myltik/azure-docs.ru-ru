<properties
	pageTitle="Расширение возможностей HDInsight с помощью виртуальной сети Azure | Microsoft Azure"  
	description="Узнайте, как использовать виртуальную сеть Azure для подключения HDInsight к другим облачным ресурсам или к ресурсам в центре обработки данных."
	services="hdinsight"
	documentationCenter=""
	authors="Blackmist"
	manager="paulettm"
	editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="11/18/2015"
   ms.author="larryfr"/>


#Расширение возможностей HDInsight с помощью виртуальной сети Azure

Виртуальная сеть Azure позволяет расширить решения Hadoop для добавления локальных ресурсов, таких как SQL Server, или создания защищенных частных сетей между ресурсами в облаке.

> [AZURE.NOTE]HDInsight не поддерживает виртуальные сети Azure на основе территории. При работе с HDInsight следует использовать виртуальные сети на основе расположения.


##<a id="whatis"></a>Что такое виртуальная сеть Azure

[Azure Virtual Network](http://azure.microsoft.com/documentation/services/virtual-network/) позволяет создать безопасную, стабильную сеть, в которой будут находиться все ресурсы, необходимые для успешного функционирования вашего решения. Виртуальная сеть позволит вам:

* Соединить облачные ресурсы с частной сетью (только для облачных решений).

	![Диаграмма конфигурации только для облачных решений](media/hdinsight-extend-hadoop-virtual-network/cloud-only.png)

	При использовании виртуальной сети для привязки служб Azure к Azure HDInsight можно реализовать следующие сценарии.

	* **Вызов служб или заданий HDInsight** с веб-сайтов Azure или из служб, работающих в виртуальных машинах Azure.

	* **Непосредственная передача данных** между HDInsight и базой данных SQL Azure, SQL Server или другими решениями для хранения данных в виртуальной машине.

	* **Объединение нескольких серверов HDInsight** в единое решение. Пример — использование сервера Storm в HDInsight для получения входящих данных и последующего сохранения обработанных данных на сервере HBase HDInsight. Необработанные данные также можно сохранять на сервере Hadoop в HDInsight для их последующего анализа с помощью MapReduce.

* Соединить ваши облачные ресурсы с сетью локального центра обработки данных (типа «сеть — сеть» или «точка — сеть») при помощи виртуальной частной сети (VPN).

	Конфигурация «сеть — сеть» позволяет соединить несколько ресурсов вашего центра обработки данных с виртуальной сетью Azure при помощи программного обеспечения VPN или службы маршрутизации и удаленного доступа.

	![Диаграмма конфигурации соединения "сервер-сервер"](media/hdinsight-extend-hadoop-virtual-network/site-to-site.png)

	Конфигурация «точка — сеть» позволит соединить конкретный ресурс с виртуальной сетью Azure при помощи программного обеспечения VPN.

	![Диаграмма конфигурации соединения "клиент-сервер"](media/hdinsight-extend-hadoop-virtual-network/point-to-site.png)

	Использование виртуальной сети для связи облака и центра обработки данных позволяет реализовать подобные сценарии в конфигурации только облака. Но при этом помимо работы с ресурсами в облаке вы также получаете возможность работы с ресурсами в центре обработки данных.

	* **Непосредственная передача данных** между HDInsight и центром обработки данных. Например, использование Sqoop для передачи данных на SQL Server либо из него или чтение данных, созданных бизнес-приложением.

	* **Вызов служб или заданий HDInsight** из бизнес-приложения. Например, использование API HBase Java для хранения и извлечения данных из кластера HDInsight HBase.

Для получения более подробной информации о характеристиках, преимуществах и возможностях виртуальной сети Azure см. раздел [Обзор характеристик виртуальной сети Azure](../virtual-network/virtual-networks-overview.md).

> [AZURE.NOTE]Виртуальную сеть Azure необходимо создавать до подготовки кластера HDInsight. Более подробную информацию можно найти в разделе [Настройка задач виртуальной сети](http://azure.microsoft.com/documentation/services/virtual-network/).
>
> Azure HDInsight поддерживает только виртуальные сети на основе расположения и на данный момент не работает с виртуальными сетями на основе территориальной группы.
>
> Настоятельно рекомендуется назначать одну подсеть для одного кластера.
>
> Для кластеров на базе Windows требуется виртуальная сеть версии 1 (классическая), а для кластеров на базе Linux — версии 2 (диспетчер ресурсов Azure). Если у вас несоответствующий тип сети, она будет бесполезна при создании кластера.
>
> Если имеются ресурсы в виртуальной сети, которую не может использовать создаваемый кластер, то вы можете создать новую виртуальную сеть, подходящую для кластера, и подключить ее к несовместимой виртуальной сети. Затем можно создать кластер в сети требуемой версии, и ему будут доступны ресурсы в другой сети, так как эти две сети соединены. Подробнее о подключении классических и новых виртуальных сетей см. в разделе [Подключение классических виртуальных сетей к новым виртуальным сетям](../virtual-network/virtual-networks-arm-asm-s2s.md).

Дополнительные сведения о подготовке кластера HDInsight в виртуальной сети см. в разделе [Подготовка кластеров Hadoop в HDInsight](hdinsight-provision-clusters.md).

##<a id="tasks"></a>Задачи и информация

В этом разделе приведены распространенные задачи, а также информация, которая может понадобиться при использовании HDInsight в виртуальной сети.

###Определение полного доменного имени

Кластеру HDInsight будет назначено определенное полное доменное имя для виртуального сетевого интерфейса. Это адрес, который необходимо использовать при подключении к кластеру из других ресурсов в виртуальной сети. Чтобы определить полное доменное имя, используйте следующий URL-адрес для запроса службы управления Ambari:

	https://<clustername>.azurehdinsight.net/ambari/api/v1/clusters/<clustername>.azurehdinsight.net/services/<servicename>/components/<componentname>

> [AZURE.NOTE]Дополнительные сведения об использовании Ambari в HDInsight см. в разделе [Мониторинг кластеров Hadoop в HDInsight с помощью API Ambari](hdinsight-monitor-use-ambari-api.md).

Необходимо указать имя кластера, службу и компонент, работающий в кластере, например диспетчер ресурсов YARN.

> [AZURE.NOTE]Возвращенные данные представляют собой документ JavaScript Object Notation (JSON), содержащий большой объем сведений о компоненте. Чтобы извлечь только полное доменное имя, следует получить значение `host_components[0].HostRoles.host_name` с помощью средства синтаксического анализа JSON.

Например, чтобы получить полное доменное имя из кластера Hadoop HDInsight, можно использовать один из следующих способов извлечения данных для диспетчера ресурсов YARN.

* [Azure PowerShell](../powershell-install-configure.md)

		$ClusterDnsName = <clustername>
		$Username = <cluster admin username>
		$Password = <cluster admin password>
		$DnsSuffix = ".azurehdinsight.net"
		$ClusterFQDN = $ClusterDnsName + $DnsSuffix

		$webclient = new-object System.Net.WebClient
		$webclient.Credentials = new-object System.Net.NetworkCredential($Username, $Password)

		$Url = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/services/yarn/		components/resourcemanager"
		$Response = $webclient.DownloadString($Url)
		$JsonObject = $Response | ConvertFrom-Json
		$FQDN = $JsonObject.host_components[0].HostRoles.host_name
		Write-host $FQDN

* [cURL](http://curl.haxx.se/) и [jq](http://stedolan.github.io/jq/)

		curl -G -u <username>:<password> https://<clustername>.azurehdinsight.net/ambari/api/v1/clusters/<clustername>.azurehdinsight.net/services/yarn/components/resourcemanager | jq .host_components[0].HostRoles.host_name

###Подключение к HBase

Чтобы удаленно подключаться к HBase с помощью API Java, необходимо определить адреса кворума ZooKeeper для кластера HBase и указать это в приложении.

Чтобы получить адрес кворума ZooKeeper, используйте один из следующих способов для выполнения запроса к службе управления Ambari.

* [Azure PowerShell](../powershell-install-configure.md)

		$ClusterDnsName = <clustername>
		$Username = <cluster admin username>
		$Password = <cluster admin password>
		$DnsSuffix = ".azurehdinsight.net"
		$ClusterFQDN = $ClusterDnsName + $DnsSuffix

		$webclient = new-object System.Net.WebClient
		$webclient.Credentials = new-object System.Net.NetworkCredential($Username, $Password)

		$Url = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/configurations?type=hbase-site&tag=default&fields=items/properties/hbase.zookeeper.quorum"
        $Response = $webclient.DownloadString($Url)
        $JsonObject = $Response | ConvertFrom-Json
        Write-host $JsonObject.items[0].properties.'hbase.zookeeper.quorum'

* [cURL](http://curl.haxx.se/) и [jq](http://stedolan.github.io/jq/)

		curl -G -u <username>:<password> "https://<clustername>.azurehdinsight.net/ambari/api/v1/clusters/<clustername>.azurehdinsight.net/configurations?type=hbase-site&tag=default&fields=items/properties/hbase.zookeeper.quorum" | jq .items[0].properties[]

> [AZURE.NOTE]Дополнительные сведения об использовании Ambari в HDInsight см. в разделе [Мониторинг кластеров Hadoop в HDInsight с помощью API Ambari](hdinsight-monitor-use-ambari-api.md).

После получения информации о кворуме используйте ее в клиентском приложении.

Например, для приложения Java, которое использует API HBase, в проект необходимо добавить файл **hbase site.xml** и указать в нем сведения о кворуме, как показано ниже.

```
<configuration>
  <property>
    <name>hbase.cluster.distributed</name>
    <value>true</value>
  </property>
  <property>
    <name>hbase.zookeeper.quorum</name>
    <value>zookeeper0.address,zookeeper1.address,zookeeper2.address</value>
  </property>
  <property>
    <name>hbase.zookeeper.property.clientPort</name>
    <value>2181</value>
  </property>
</configuration>
```

###Проверка сетевого подключения

Некоторые службы, такие как SQL Server, могут ограничивать количество входящих сетевых подключений. Это будет препятствовать нормальной работе HDInsight с этими службами.

Если у вас возникли проблемы при доступе к службе из HDInsight, см. документацию по службе, чтобы убедиться, чтобы вы включили доступ к сети. Вы также можете проверить доступ к сети, создав виртуальную машину Azure в той же виртуальной сети и с помощью клиентских служебных программ проверив, может ли эта виртуальная машина подключаться к службе через виртуальную сеть.

##<a id="nextsteps"></a>Дальнейшие действия

В следующих примерах демонстрируется, как использовать HDInsight в виртуальной сети Azure.

* [Анализ данных датчиков с помощью Storm и HBase в HDInsight](hdinsight-storm-sensor-data-analysis.md). Показывается, как настроить кластер Storm и HBase в виртуальной сети, а также как удаленно записывать данные в HBase из Storm.

* [Подготовка кластеров Hadoop в HDInsight](hdinsight-hadoop-provision-linux-clusters.md). Сведения о подготовке кластеров Hadoop, в том числе сведения об использовании виртуальной сети Azure.

* [Использование Sqoop с Hadoop в HDInsight](hdinsight-use-sqoop-mac-linux.md). Сведения об использовании Sqoop для передачи данных с помощью SQL Server по виртуальной сети.

Дополнительные сведения о виртуальных сетях Azure см. в статье [Общие сведения о виртуальных сетях Azure](../virtual-network/virtual-networks-overview.md).

<!---HONumber=AcomDC_1210_2015-->
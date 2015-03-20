<properties
   pageTitle="Расширение возможностей HDInsight с помощью виртуальной сети Azure | Виртуальная сеть" metaKeywords="virtual network, vnet, azure, hdinsight" description="Узнайте, как использовать виртуальную сеть Azure для подключения HDInsight к другим облачным ресурсам или к ресурсам в центре обработки данных."
   services="hdinsight"
   documentationCenter=""
   authors="blackmist"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang=""
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="02/2/2015"
   ms.author="larryfr"/>


#Расширение возможностей HDInsight с помощью виртуальной сети Azure

Виртуальная сеть Azure позволяет расширить решения Hadoop для добавления локальных ресурсов, таких как SQL Server, и создания безопасных частных сетей между ресурсами в облаке.

> [AZURE.NOTE] Сейчас HDInsight не поддерживает виртуальные сети Azure на основе территории. При использовании HDInsight следует использовать виртуальные сети на основе расположения.

##<a id="whatis"></a>Что такое виртуальная сеть Azure?

[Виртуальная сеть Azure](/documentation/services/virtual-network/) позволяет создать безопасную, стабильную сеть, в которой будут находиться все ресурсы, необходимые для успешного функционирования вашего решения. Виртуальная сеть позволит вам:

* Соединить облачные ресурсы с частной сетью (только для облачных решений)

	![diagram of cloud-only configuration](.\media\hdinsight-provision-clusters\cloud-only.png)

	При использовании виртуальной сети для привязки служб Azure к HDInsight можно выполнять следующие сценарии.

	* **Вызов служб или заданий HDInsight** с веб-сайтов Azure или из служб, работающих в виртуальных машинах Azure.

	* **Непосредственная передача данных** между HDInsight и базой данных SQL, SQL Server или другими решениями для хранения данных в виртуальной машине.

	* **Объединение нескольких серверов HDInsight** в единое решение. Например, использование сервера Storm в HDInsight для получения входящих данных и последующего сохранения обработанных данных на сервер HBase HDInsight. Необработанные данные также можно сохранять на сервер Hadoop в HDInsight для их последующего анализа с помощью MapReduce.

* Соединить ваши облачные ресурсы с внутренней сетью обработки данных (типа "сервер-сервер" или "клиент-сервер) при помощи Виртуальной частной сети (VPN).

	Конфигурация "сервер-сервер" позволяет соединять несколько ресурсов вашего центра обработки данных с виртуальной сетью Azure при помощи программного обеспечения VPN или маршрутизации и службы удаленного доступа.

	![diagram of site-to-site configuration](.\media\hdinsight-provision-clusters\site-to-site.png)

	Конфигурация "клиент-сервер" позволит соединить определенные ресурсы с виртуальной сетью Azure при помощи программного обеспечения VPN

	![diagram of point-to-site configuration](.\media\hdinsight-provision-clusters\point-to-site.png)

	При использовании виртуальной сети для привязки облака и центра обработки данных подобные сценарии применяются только к облачным конфигурациям, но вы можете не ограничиваться работой с ресурсами в облаке и работать с ресурсами в центре обработки данных.

	* **Непосредственная передача данных** между HDInsight и центром обработки данных. Например, использование Sqoop для передачи данных на SQL Server либо из него или считывания данных, созданных бизнес-приложением.

	* **Вызов служб или заданий HDInsight** из бизнес-приложения. Например, использование API Java для HBase для хранения и извлечения данных из кластера HDInsight HBase.

Для получения более подробной информации о характеристиках, преимуществах и возможностях виртуальной сети Azure см. раздел [Общие сведения о виртуальных сетях Azure](http://msdn.microsoft.com/library/azure/jj156007.aspx).

> [WACOM.NOTE] Виртуальную сеть Azure необходимо создавать до подготовки кластера HDInsight. Более подробную информацию можно найти в разделе [Задачи конфигурации виртуальной сети](http://msdn.microsoft.com/library/azure/jj156206.aspx).
>
> Azure HDInsight поддерживает только географически привязанные виртуальные сети и на данный момент не поддерживает совместные групповые виртуальные сети.
>
> Настоятельно рекомендуется назначать одну подсеть для одного кластера.

Более подробная информация о кластере HDInsight в виртуальной сети см. в разделе [Подготовка кластеров Hadoop в HDInsight](/documentation/articles/hdinsight-provision-clusters/).

##<a id="tasks"></a>Задачи и информация

В этом разделе приведены распространенные задачи, а также информация, которая может понадобиться при использовании HDInsight в виртуальной сети.

###Определение полного доменного имени

Кластеру HDInsight будет назначено определенное полное доменное имя для виртуального сетевого интерфейса. Это адрес, который нужно использовать при подключении к кластеру из других ресурсов в виртуальной сети. Чтобы определить полное доменное имя, используйте следующий URL-адрес для запроса службы управления Ambari.

	https://<clustername>.azurehdinsight.net/ambari/api/v1/clusters/<clustername>.azurehdinsight.net/services/<servicename>/components/<componentname>

> [AZURE.NOTE] Дополнительную информацию об использовании Ambari с HDInsight см. в статье [Мониторинг кластеров Hadoop в HDInsight с помощью Ambari API](/documentation/articles/hdinsight-monitor-use-ambari-api/).

Необходимо указать имя кластера, службу и компонент, работающий в кластере, например диспетчер ресурсов YARN.

> [AZURE.NOTE] Возвращенные данные представляют собой документ в формате JSON, в котором содержится много информации о компоненте. Чтобы извлечь только полное доменное имя, необходимо использовать средство синтаксического анализа JSON для получения значения `host_components[0].HostRoles.host_name`.

Например, чтобы получить полное доменное имя из кластера Hadoop HDInsight, можно использовать один из следующих методов извлечения данных для диспетчера ресурсов YARN.

* [Azure PowerShell](/documentation/articles/install-configure-powershell/)

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

* [Curl](http://curl.haxx.se/) и [jq](http://stedolan.github.io/jq/)

		curl -G -u <username>:<password> https://<clustername>.azurehdinsight.net/ambari/api/v1/clusters/<clustername>.azurehdinsight.net/services/yarn/components/resourcemanager | jq .host_components[0].HostRoles.host_name

###Подключение к HBase

Для удаленного подключения к HBase с помощью API Java необходимо определить адрес кворума Zookeeper для кластера HBase и указать его в приложении.

Чтобы получить адрес кворума zookeeper, используйте один из следующих методов для запроса службы управления Ambari.

* [Azure PowerShell](/documentation/articles/install-configure-powershell/)

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

* [Curl](http://curl.haxx.se/) и [jq](http://stedolan.github.io/jq/)

		curl -G -u <username>:<password> "https://<clustername>.azurehdinsight.net/ambari/api/v1/clusters/<clustername>.azurehdinsight.net/configurations?type=hbase-site&tag=default&fields=items/properties/hbase.zookeeper.quorum" | jq .items[0].properties[]

> [AZURE.NOTE] Дополнительную информацию об использовании Ambari с HDInsight см. в статье [Мониторинг кластеров Hadoop в HDInsight с помощью Ambari API](/documentation/articles/hdinsight-monitor-use-ambari-api/).

После получения информации о кворуме используйте ее в клиентском приложении.

Например, для приложения Java, которое использует API для HBase, в проект необходимо добавить файл **hbase site.xml** и указать в нем информацию о кворуме, как показано ниже.

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

Если у вас возникли проблемы при доступе к службе из HDInsight, см. документацию по службе, чтобы убедиться, чтобы вы включили доступ к сети. Вы также можете проверить доступ к сети путем создания в ней виртуальной машины Azure и использовать клиентские служебные программы, чтобы убедиться, что виртуальная машина может подключаться к службе через виртуальную сеть.

##<a id="nextsteps"></a>Дальнейшие действия

В следующих примерах показано, как использовать HDInsight в виртуальной сети Azure.

* [Анализ данных датчиков с помощью Storm и HBase в HDInsight](/documentation/articles/hdinsight-storm-sensor-data-analysis/). В этой статье описано, как настроить кластер Storm и HBase в виртуальной сети и как удаленно записывать данные в HBase из Storm.

* [Подготовка кластеров HBase в виртуальной сети Azure](/documentation/articles/hdinsight-hbase-provision-vnet/) . В этой статье содержится информация о подготовке кластеров HBase в виртуальной сети Azure.

* [Подготовка кластеров Hadoop в HDInsight](/documentation/articles/hdinsight-provision-clusters/). В этой статье содержится информация о подготовке кластеров Hadoop, включая информацию об использовании виртуальной сети Azure.

* [Использование Sqoop с Hadoop в HDInsight](/documentation/articles/hdinsight-use-sqoop/) . В этой статье содержится информация об использовании Sqoop для передачи данных с помощью SQL Server по виртуальной сети.

Дополнительную информацию о виртуальных сетях Azure см. в статье [Общие сведения о виртуальных сетях Azure](http://msdn.microsoft.com/library/azure/jj156007.aspx).

<!--HONumber=47-->

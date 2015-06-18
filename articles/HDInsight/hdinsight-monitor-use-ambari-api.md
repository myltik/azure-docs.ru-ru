<properties 
	pageTitle="Отслеживание кластеров Hadoop в HDInsight с помощью интерфейса Ambari API для платформы Azure" 
	description="Набор интерфейсов API для Apache Ambari используется для подготовки, отслеживания кластеров Apache Hadoop и управления ими. Сложность организации Hadoop компенсируется интуитивным дизайном средств управления пользователя и интерфейсов API." 
	services="hdinsight" 
	documentationCenter="" 
	authors="mumian" 
	editor="cgronlun" 
	manager="paulettm"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/31/2015" 
	ms.author="jgao"/>

# Отслеживание кластеров Hadoop в HDInsight с помощью интерфейса Ambari API
 
Узнайте, как наблюдать за кластерами HDInsight версии 3.1 и 2.1 с помощью интерфейсов Ambari API.



## <a id="whatisambari"></a>Что такое Ambari?

[Apache Ambari][ambari-home] используется для подготовки, мониторинга кластеров Apache Hadoop и управления ими. Он включает в себя набор удобных средств и надежных интерфейсов API, который упрощают работу с кластерами Hadoop. Дополнительную информацию об интерфейсах API см. в [справочнике по Ambari API][ambari-api-reference].


В настоящее время HDInsight поддерживает только функцию мониторинга Ambari. Ambari API 1.0 поддерживается кластерами HDInsight версии 3.0 и 2.1. В этой статье описывается доступ к интерфейсам Ambari API в кластерах HDInsight версии 3.1 и 2.1. Основное отличие между двумя версиями заключается в замене некоторых компонентов, что позволило обеспечить новые возможности (таких как «История задач сервера»).


##<a id="prerequisites"></a>Предварительные требования

Перед началом работы с этим учебником необходимо иметь следующее:

- **Рабочая станция**, на которой установлена и настроена среда Azure PowerShell. Инструкции см. в разделе [Установка и настройка Azure PowerShell][powershell-install]. Для выполнения сценариев Azure PowerShell необходимо запустить Azure PowerShell с правами администратора и задать политику выполнения *RemoteSigned*. Дополнительную информацию см. в статье [Выполнение сценариев Windows PowerShell][powershell-script].

- [cURL][curl] (необязательно). Для установки см. раздел [выпусков и скачиваемых файлов cURL][curl-download].

	>[AZURE.NOTE]При использовании команды cURL в Windows для значений параметров указывайте двойные кавычки вместо одинарных.

- **Кластер Azure HDInsight**. Указания по подготовке кластеров см. в разделе [Приступая к работе с HDInsight][hdinsight-get-started] или [Подготовка кластеров HDInsight][hdinsight-provision]. Для выполнения учебника необходимы следующие данные:

	<table border="1">
<tr><th>Свойство кластера</th><th>Имя переменной Azure PowerShell</th><th>Значение</th><th>Описание</th></tr>
<tr><td>Имя кластера HDInsight.</td><td>$clusterName</td><td></td><td>Это имя вашего кластера HDInsight.</td></tr>
<tr><td>Имя пользователя кластера</td><td>$clusterUsername</td><td></td><td>Имя пользователя кластера, указанное при подготовке.</td></tr>
<tr><td>Пароль кластера</td><td>$clusterPassword</td><td></td><td>Пароль пользователя кластера.</td></tr>
</table>> [AZURE.NOTE]Введите значения в таблицу. Это будет полезно для изучения данного учебника.



##<a id="jumpstart"></a>Приступая к работе

Существует несколько способов для использования Ambari для отслеживания кластеров HDInsight.

**Использование Azure PowerShell**

Ниже приведен сценарий Azure PowerShell для получения данных модуля отслеживания заданий MapReduce *в кластере HDInsight 3.1*. Основное отличие заключается в том, что мы получаем эти данные от службы YARN (а не от MapReduce).

	$clusterName = "<HDInsightClusterName>"
	$clusterUsername = "<HDInsightClusterUsername>"
	$clusterPassword = "<HDInsightClusterPassword>"
	
	$ambariUri = "https://$clusterName.azurehdinsight.net:443/ambari"
	$uriJobTracker = "$ambariUri/api/v1/clusters/$clusterName.azurehdinsight.net/services/yarn/components/resourcemanager"
	
	$passwd = ConvertTo-SecureString $clusterPassword -AsPlainText -Force
	$creds = New-Object System.Management.Automation.PSCredential ($clusterUsername, $passwd)
	
	$response = Invoke-RestMethod -Method Get -Uri $uriJobTracker -Credential $creds -OutVariable $OozieServerStatus 
	
	$response.metrics.'yarn.queueMetrics'

Ниже приведен сценарий Azure PowerShell для получения данных модуля отслеживания заданий MapReduce *в кластере HDInsight 2.1*.

	$clusterName = "<HDInsightClusterName>"
	$clusterUsername = "<HDInsightClusterUsername>"
	$clusterPassword = "<HDInsightClusterPassword>"
	
	$ambariUri = "https://$clusterName.azurehdinsight.net:443/ambari"
	$uriJobTracker = "$ambariUri/api/v1/clusters/$clusterName.azurehdinsight.net/services/mapreduce/components/jobtracker"
	
	$passwd = ConvertTo-SecureString $clusterPassword -AsPlainText -Force
	$creds = New-Object System.Management.Automation.PSCredential ($clusterUsername, $passwd)
	
	$response = Invoke-RestMethod -Method Get -Uri $uriJobTracker -Credential $creds -OutVariable $OozieServerStatus 
	
	$response.metrics.'mapred.JobTracker'

Результаты:

![Вывод Jobtracker][img-jobtracker-output]

**Использование cURL**

Ниже приведен пример получения информации о кластере с помощью cURL:

	curl -u <username>:<password> -k https://<ClusterName>.azurehdinsight.net:443/ambari/api/v1/clusters/<ClusterName>.azurehdinsight.net

Результаты:
	
	{"href":"https://hdi0211v2.azurehdinsight.net/ambari/api/v1/clusters/hdi0211v2.azurehdinsight.net/",
	 "Clusters":{"cluster_name":"hdi0211v2.azurehdinsight.net","version":"2.1.3.0.432823"},
	 "services"[
	   {"href":"https://hdi0211v2.azurehdinsight.net/ambari/api/v1/clusters/hdi0211v2.azurehdinsight.net/services/hdfs",
	    "ServiceInfo":{"cluster_name":"hdi0211v2.azurehdinsight.net","service_name":"hdfs"}},
	   {"href":"https://hdi0211v2.azurehdinsight.net/ambari/api/v1/clusters/hdi0211v2.azurehdinsight.net/services/mapreduce",
	    "ServiceInfo":{"cluster_name":"hdi0211v2.azurehdinsight.net","service_name":"mapreduce"}}],
	 "hosts":[
	   {"href":"https://hdi0211v2.azurehdinsight.net/ambari/api/v1/clusters/hdi0211v2.azurehdinsight.net/hosts/headnode0",
	    "Hosts":{"cluster_name":"hdi0211v2.azurehdinsight.net",
	             "host_name":"headnode0"}},
	   {"href":"https://hdi0211v2.azurehdinsight.net/ambari/api/v1/clusters/hdi0211v2.azurehdinsight.net/hosts/workernode0",
	    "Hosts":{"cluster_name":"hdi0211v2.azurehdinsight.net",
	             "host_name":"headnode0.{ClusterDNS}.azurehdinsight.net"}}]}

**Примечание к выпуску от 10.8.2014**:

При использовании конечной точки Ambari https://{clusterDns}.azurehdinsight.net/ambari/api/v1/clusters/{clusterDns}.azurehdinsight.net/services/{servicename}/components/{componentname} поле *host_name* в настоящее время возвращает полное доменное имя (FQDN) узла вместо имени узла. До выпуска от 10.8.2014 этот пример просто возвращал «headnode0». После выпуска от 10.8.2014 поле возвращает полное доменное имя headnode0.{ClusterDNS}.azurehdinsight.net, как это показано в примере выше. Это изменение было продиктовано необходимостью использовать сценарии, в которых кластеры различного типа, например кластеры HBase и Hadoop, можно было бы размещать в одной виртуальной сети (VNET). Такая необходимость может возникнуть, например, при использовании HBase в качестве вспомогательной платформы для Hadoop.

##<a id="monitor"></a>Интерфейсы Ambari API для мониторинга

В следующей таблице перечислены некоторые наиболее распространенные вызовы Ambari API для мониторинга. Дополнительную информацию об API см. в [справочнике по Ambari API][ambari-api-reference].

<table border="1">
<tr><th>Отслеживание вызова API</th><th>URI</th><th>Описание</th></tr>
<tr><td>Получение кластеров</td><td><tt>/api/v1/clusters</tt></td><td></td></tr>
<tr><td>Получение сведений о кластере.</td><td><tt>/api/v1/clusters/&lt;ClusterName>.azurehdinsight.net</tt></td><td>кластеры, службы, узлы</td></tr>
<tr><td>Получение служб</td><td><tt>/api/v1/clusters/&lt;ClusterName>.azurehdinsight.net/services</tt></td><td>Службы включают в себя: hdfs, mapreduce</td></tr>
<tr><td>Получение сведений о службах.</td><td><tt>/api/v1/clusters/&lt;ClusterName>.azurehdinsight.net/services/&lt;ServiceName></tt></td><td></td></tr>
<tr><td>Получение компонентов службы</td><td><tt>/api/v1/clusters/&lt;ClusterName>.azurehdinsight.net/services/&lt;ServiceName>/components</tt></td><td>HDFS: namenode, datanode<br/>MapReduce: jobtracker; tasktracker</td></tr>
<tr><td>Получение сведений о компонентах.</td><td><tt>/api/v1/clusters/&lt;ClusterName>.azurehdinsight.net/services/&lt;ServiceName>/components/&lt;ComponentName></tt></td><td>ServiceComponentInfo, host-components, metrics</td></tr>
<tr><td>Получение узлов</td><td><tt>/api/v1/clusters/&lt;ClusterName>.azurehdinsight.net/hosts</tt></td><td>headnode0, workernode0</td></tr>
<tr><td>Получение сведений об узлах.</td><td><tt>/api/v1/clusters/&lt;ClusterName>.azurehdinsight.net/hosts/&lt;HostName> 
</td><td></td></tr>
<tr><td>Получение компонентов узлов</td><td><tt>/api/v1/clusters/&lt;ClusterName>.azurehdinsight.net/hosts/&lt;HostName>/host_components </tt></td><td>namenode, resourcemanager</td></tr>
<tr><td>Получение сведений о компонентах узла.</td><td><tt>/api/v1/clusters/&lt;ClusterName>.azurehdinsight.net/hosts/&lt;HostName>/host_components/&lt;ComponentName> </tt></td><td>HostRoles, component, host, metrics</td></tr>
<tr><td>Получение конфигураций</td><td><tt>/api/v1/clusters/&lt;ClusterName>.azurehdinsight.net/configurations </tt></td><td>Типы настройки: core-site, hdfs-site, mapred-site, hive-site</td></tr>
<tr><td>Получение сведений о конфигурации.</td><td><tt>/api/v1/clusters/&lt;ClusterName>.azurehdinsight.net/configurations?type=&lt;ConfigType>&amp;tag=&lt;VersionName> </tt></td><td>Типы настройки: core-site, hdfs-site, mapred-site, hive-site</td></tr>
</table>


##<a id="nextsteps"></a>Дальнейшие действия 

Теперь вы узнали, как использовать Ambari API для мониторинга. Дополнительные сведения см. на следующих ресурсах:

- [Управление кластерами HDInsight с помощью портала управления][hdinsight-admin-portal]
- [Управление кластерами HDInsight с помощью Azure PowerShell][hdinsight-admin-powershell]
- [Управление кластерами HDInsight с помощью интерфейса командной строки][hdinsight-admin-cli]
- [Документация по HDInsight][hdinsight-documentation]
- [Приступая к работе с HDInsight][hdinsight-get-started]



[ambari-home]: http://ambari.apache.org/
[ambari-api-reference]: https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md

[curl]: http://curl.haxx.se
[curl-download]: http://curl.haxx.se/download.html

[microsoft-hadoop-SDK]: http://hadoopsdk.codeplex.com/wikipage?title=Ambari%20Monitoring%20Client

[Powershell-install]: ../install-configure-powershell.md
[Powershell-script]: http://technet.microsoft.com/library/ee176949.aspx

[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-admin-portal]: hdinsight-administer-use-management-portal.md
[hdinsight-admin-cli]: hdinsight-administer-use-command-line.md
[hdinsight-documentation]: /documentation/services/hdinsight/
[hdinsight-get-started]: ../hdinsight-get-started.md
[hdinsight-provision]: hdinsight-provision-clusters.md

[img-jobtracker-output]: ./media/hdinsight-monitor-use-ambari-api/hdi.ambari.monitor.jobtracker.output.png


<!--HONumber=54--> 
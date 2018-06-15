---
title: Мониторинг кластеров Hadoop в HDInsight с помощью API-интерфейса Ambari — Azure | Документы Майкрософт
description: Набор интерфейсов API для Apache Ambari используется для создания, отслеживания кластеров Apache Hadoop и управления ими. Сложность организации Hadoop компенсируется интуитивным дизайном средств управления пользователя и интерфейсов API.
services: hdinsight
documentationcenter: ''
tags: azure-portal
author: mumian
editor: cgronlun
manager: jhubbard
ms.assetid: 052135b3-d497-4acc-92ff-71cee49356ff
ms.service: hdinsight
ms.devlang: na
ms.topic: conceptual
ms.date: 04/07/2017
ms.author: jgao
ROBOTS: NOINDEX
ms.openlocfilehash: 3c8b1af3ad151a7a901150352202ab0b85aa6ec8
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2018
ms.locfileid: "31402042"
---
# <a name="monitor-hadoop-clusters-in-hdinsight-using-the-ambari-api"></a>Отслеживание кластеров Hadoop в HDInsight с помощью интерфейса Ambari API
Узнайте, как отслеживать кластеры HDInsight с помощью интерфейсов Ambari API.

> [!NOTE]
> Информация в этой статье касается преимущественно кластеров HDInsight на платформе Windows, которые предоставляют версию интерфейса API REST Ambari только для чтения. Сведения о кластерах под управлением Linux см. в статье [Управление кластерами HDInsight с помощью веб-интерфейса Ambari](hdinsight-hadoop-manage-ambari.md).
> 
> 

## <a name="what-is-ambari"></a>Что такое Ambari?
[Apache Ambari][ambari-home] используется для подготовки, мониторинга кластеров Apache Hadoop и управления ими. Он включает в себя набор удобных средств и надежных интерфейсов API, который упрощают работу с кластерами Hadoop. Дополнительные сведения об интерфейсах API см. в [справочнике по Ambari API][ambari-api-reference]. 

В настоящее время HDInsight поддерживает только функцию мониторинга Ambari. Ambari API 1.0 поддерживается кластерами HDInsight версии 3.0 и 2.1. В этой статье описывается доступ к интерфейсам Ambari API в кластерах HDInsight версии 3.1 и 2.1. Основное отличие между двумя версиями заключается в замене некоторых компонентов, что позволило обеспечить новые возможности (таких как "История задач сервера"). 

**Предварительные требования**

Перед началом работы с этим руководством необходимо иметь следующее:

* **Рабочая станция с Azure PowerShell**.
* [cURL][curl] (необязательно). Чтобы установить cURL, перейдите на страницу [выпусков и скачиваемых файлов][curl-download].
  
  > [!NOTE]
  > При использовании команды cURL в Windows для значений параметров указывайте двойные кавычки вместо одинарных.
  > 
  > 
* **Кластер Azure HDInsight**. Указания по подготовке кластеров см. в статье [Руководство по Hadoop. Начало работы с Hadoop в HDInsight на платформе Linux][hdinsight-get-started] или [Создание кластеров Hadoop под управлением Windows в HDInsight][hdinsight-provision]. Для прохождения учебника необходимы следующие данные:
  
  | Свойство кластера | Имя переменной Azure PowerShell | Значение | ОПИСАНИЕ |
  | --- | --- | --- | --- |
  |   Имя кластера HDInsight. |$clusterName | |Это имя вашего кластера HDInsight. |
  |   Имя пользователя кластера |$clusterUsername | |Имя пользователя кластера, указанное при создании кластера. |
  |   Пароль кластера |$clusterPassword | |Пароль пользователя кластера. |

[!INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]


## <a name="jump-start"></a>Приступая к работе
Существует несколько способов для использования Ambari для отслеживания кластеров HDInsight.

**Использование Azure PowerShell**

Ниже приведен сценарий Azure PowerShell для получения данных модуля отслеживания заданий MapReduce *в кластере HDInsight 3.5*.  Основное отличие заключается в том, что мы получаем эти данные от службы YARN (а не от MapReduce).

    $clusterName = "<HDInsightClusterName>"
    $clusterUsername = "<HDInsightClusterUsername>"
    $clusterPassword = "<HDInsightClusterPassword>"

    $ambariUri = "https://$clusterName.azurehdinsight.net:443"
    $uriJobTracker = "$ambariUri/api/v1/clusters/$clusterName/services/YARN/components/RESOURCEMANAGER"

    $passwd = ConvertTo-SecureString $clusterPassword -AsPlainText -Force
    $creds = New-Object System.Management.Automation.PSCredential ($clusterUsername, $passwd)

    $response = Invoke-RestMethod -Method Get -Uri $uriJobTracker -Credential $creds -OutVariable $OozieServerStatus

    $response.metrics.'yarn.queueMetrics'

Ниже приведен сценарий PowerShell для получения данных модуля отслеживания заданий MapReduce *в кластере HDInsight 2.1*.

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

Ниже приведен пример получения сведений о кластере с помощью cURL:

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

При использовании конечной точки Ambari https://{clusterDns}.azurehdinsight.net/ambari/api/v1/clusters/{clusterDns}.azurehdinsight.net/services/{servicename}/components/{componentname} поле *host_name* в настоящее время возвращает полное доменное имя (FQDN) узла вместо имени узла. До выпуска от 08.10.2014 этот пример просто возвращал значение**headnode0**. Начиная с выпуска от 08.10.2014 он возвращает полное доменное имя**headnode0.{ClusterDNS}.azurehdinsight.net**, как показано выше. Это изменение было продиктовано необходимостью использовать сценарии, в которых кластеры различного типа, например кластеры HBase и Hadoop, можно было бы размещать в одной виртуальной сети (VNET). Такая необходимость может возникнуть, например, при использовании HBase в качестве вспомогательной платформы для Hadoop.

## <a name="ambari-monitoring-apis"></a>Интерфейсы Ambari API для мониторинга
В следующей таблице перечислены некоторые наиболее распространенные вызовы Ambari API для мониторинга. Дополнительные сведения об API см. в [справочнике по Ambari API][ambari-api-reference].

| Отслеживание вызова API | URI | ОПИСАНИЕ |
| --- | --- | --- |
| Получение кластеров |`/api/v1/clusters` | |
| Получение сведений о кластере. |`/api/v1/clusters/<ClusterName>.azurehdinsight.net` |кластеры, службы, узлы |
| Получение служб |`/api/v1/clusters/<ClusterName>.azurehdinsight.net/services` |Службы включают в себя: hdfs, mapreduce |
| Получение сведений о службах. |`/api/v1/clusters/<ClusterName>.azurehdinsight.net/services/<ServiceName>` | |
| Получение компонентов службы |`/api/v1/clusters/<ClusterName>.azurehdinsight.net/services/<ServiceName>/components` |HDFS: namenode, datanodeMapReduce: jobtracker; tasktracker |
| Получение сведений о компонентах. |`/api/v1/clusters/<ClusterName>.azurehdinsight.net/services/<ServiceName>/components/<ComponentName>` |ServiceComponentInfo, host-components, metrics |
| Получение узлов |`/api/v1/clusters/<ClusterName>.azurehdinsight.net/hosts` |headnode0, workernode0 |
| Получение сведений об узлах. |`/api/v1/clusters/<ClusterName>.azurehdinsight.net/hosts/<HostName>` | |
| Получение компонентов узлов |`/api/v1/clusters/<ClusterName>.azurehdinsight.net/hosts/<HostName>/host_components` |namenode, resourcemanager |
| Получение сведений о компонентах узла. |`/api/v1/clusters/<ClusterName>.azurehdinsight.net/hosts/<HostName>/host_components/<ComponentName>` |HostRoles, component, host, metrics |
| Получение конфигураций |`/api/v1/clusters/<ClusterName>.azurehdinsight.net/configurations` |Типы настройки: core-site, hdfs-site, mapred-site, hive-site |
| Получение сведений о конфигурации. |`/api/v1/clusters/<ClusterName>.azurehdinsight.net/configurations?type=<ConfigType>&tag=<VersionName>` |Типы настройки: core-site, hdfs-site, mapred-site, hive-site |

## <a name="next-steps"></a>Дальнейшие действия
Теперь вы узнали, как использовать Ambari API для мониторинга. Дополнительные сведения см. на следующих ресурсах:

* [Управление кластерами Hadoop в HDInsight с помощью портала Azure][hdinsight-admin-portal]
* [Управление кластерами Hadoop в HDInsight с помощью Azure PowerShell][hdinsight-admin-powershell]
* [Управление кластерами Hadoop в HDInsight с помощью интерфейса командной строки (CLI) Azure][hdinsight-admin-cli]
* [Документация по HDInsight][hdinsight-documentation]
* [Руководство по Hadoop. Начало работы с Hadoop в HDInsight на платформе Linux][hdinsight-get-started]

[ambari-home]: http://ambari.apache.org/
[ambari-api-reference]: https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md

[curl]: http://curl.haxx.se
[curl-download]: http://curl.haxx.se/download.html

[microsoft-hadoop-SDK]: http://hadoopsdk.codeplex.com/wikipage?title=Ambari%20Monitoring%20Client

[powershell-install]: /powershell/azureps-cmdlets-docs
[powershell-script]: http://technet.microsoft.com/library/ee176949.aspx

[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-admin-portal]: hdinsight-administer-use-management-portal.md
[hdinsight-admin-cli]: hdinsight-administer-use-command-line.md
[hdinsight-documentation]: https://docs.microsoft.com/azure/hdinsight/
[hdinsight-get-started]:hadoop/apache-hadoop-linux-tutorial-get-started.md
[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md

[img-jobtracker-output]: ./media/hdinsight-monitor-use-ambari-api/hdi.ambari.monitor.jobtracker.output.png

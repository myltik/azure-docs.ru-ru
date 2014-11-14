<properties urlDisplayName="Monitor Hadoop clusters  in HDInsight using the Ambari API" pageTitle="Отслеживание кластеров Hadoop в HDInsight с помощью интерфейса Ambari API для платформы Azure" metaKeywords="" description="Набор интерфейсов API для Apache Ambari используется для подготовки, отслеживания кластеров Apache Hadoop и управления ими. Сложность организации Hadoop компенсируется интуитивным дизайном средств управления пользователя и интерфейсов API." services="hdinsight" documentationCenter="" title="Отслеживание кластеров Hadoop в HDInsight с помощью интерфейса Ambari API" umbracoNaviHide="0" disqusComments="1" authors="jgao" editor="cgronlun" manager="paulettm" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="jgao" />

# Отслеживание кластеров Hadoop в HDInsight с помощью интерфейса Ambari API

Вы узнаете, как отслеживать кластеры HDInsight версии 3.1 и 2.1 с помощью набора интерфейсов Ambari API.

**Предполагаемое время выполнения:**15 минут

## Содержание

-   [Что такое Ambari?][Что такое Ambari?]
-   [Предварительные требования][Предварительные требования]
-   [Приступая к работе][Приступая к работе]
-   [Интерфейсы Ambari API для мониторинга][Интерфейсы Ambari API для мониторинга]
-   [Дальнейшие действия][Дальнейшие действия]

## <span id="whatisambari"></span></a>Что такое Ambari?

[Проект Apache Ambari][Проект Apache Ambari] используется для подготовки, отслеживания кластеров Apache Hadoop и управления ими. Он включает в себя набор удобных средств и надежных интерфейсов API, который упрощают работу с кластерами Hadoop. Дополнительные сведения об интерфейсах API см. в [справочнике по Ambari API][справочнике по Ambari API].

В настоящее время HDInsight поддерживает только функцию мониторинга Ambari. Ambari API 1.0 поддерживается кластерами HDInsight версии 2.1 и 3.0. В этой статье описывается только создание доступа для Ambari API к кластерам HDInsight версии 3.1 и 2.1. Основное отличие между двумя версиями заключается в замене некоторых компонентов, что позволило обеспечить новые возможности (таких как «История задач сервера»).

## <span id="prerequisites"></span></a>Предварительные требования

Перед началом работы с этим учебником необходимо иметь следующее:

-   **Рабочая станция**, на которой установлена и настроена среда Azure PowerShell. Инструкции см. в разделе [Установка и настройка Azure PowerShell][Установка и настройка Azure PowerShell]. Для выполнения скриптов PowerShell необходимо запустить Azure PowerShell с правами администратора и задать политику выполнения *RemoteSigned*. См. раздел [Выполнение скриптов Windows PowerShell][Выполнение скриптов Windows PowerShell].

    [Curl][Curl] использовать необязательно. Это средство можно установить [здесь][здесь].

    > [WACOM.NOTE] При использовании команды Curl в Windows указывайте двойные кавычки вместо одинарных кавычек для значений параметров.

-   **Кластер Azure HDInsight**. Инструкции по подготовке кластеров см. в разделе [Приступая к работе с HDInsight][Приступая к работе с HDInsight] или [Подготовка кластеров HDInsight][Подготовка кластеров HDInsight]. Для выполнения учебника необходимы следующие данные:

    <table>
    <colgroup>
    <col width="25%" />
    <col width="25%" />
    <col width="25%" />
    <col width="25%" />
    </colgroup>
    <thead>
    <tr class="header">
    <th align="left">Свойство кластера</th>
    <th align="left">Имя переменной PowerShell</th>
    <th align="left">Значение</th>
    <th align="left">Описание</th>
    </tr>
    </thead>
    <tbody>
    <tr class="odd">
    <td align="left">Имя кластера HDInsight.</td>
    <td align="left">$clusterName</td>
    <td align="left"></td>
    <td align="left">Это имя вашего кластера HDInsight.</td>
    </tr>
    <tr class="even">
    <td align="left">Имя пользователя кластера</td>
    <td align="left">$clusterUsername</td>
    <td align="left"></td>
    <td align="left">Имя пользователя кластера, указанное при подготовке.</td>
    </tr>
    <tr class="odd">
    <td align="left">Пароль кластера</td>
    <td align="left">$clusterPassword</td>
    <td align="left"></td>
    <td align="left">Пароль пользователя кластера.</td>
    </tr>
    </tbody>
    </table>

    > [WACOM.NOTE] Введите значения в таблицы. Это будет полезно при прохождении данного учебника.

## <span id="jumpstart"></span></a>Приступая к работе

Существует несколько способов для использования Ambari для отслеживания кластеров HDInsight.

**Использование Azure PowerShell**

Ниже приведен сценарий PowerShell для получения сведений о контролере заданий MapReduce *в кластере версии 3.1.* Основное отличие заключается в том, что мы получаем подробные данные от службы YARN (а не от Map Reduce).

    $clusterName = "<HDInsightClusterName>"
    $clusterUsername = "<HDInsightClusterUsername>"
    $clusterPassword = "<HDInsightClusterPassword>"

    $ambariUri = "https://$clusterName.azurehdinsight.net:443/ambari"
    $uriJobTracker = "$ambariUri/api/v1/clusters/$clusterName.azurehdinsight.net/services/yarn/components/resourcemanager"

    $passwd = ConvertTo-SecureString $clusterPassword -AsPlainText -Force
    $creds = New-Object System.Management.Automation.PSCredential ($clusterUsername, $passwd)

    $response = Invoke-RestMethod -Method Get -Uri $uriJobTracker -Credential $creds -OutVariable $OozieServerStatus 

    $response.metrics.'yarn.queueMetrics'

Ниже приведен сценарий PowerShell для получения сведений о контроллере заданий MapReduce *в кластере версии 2.1*:

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

![Вывод Jobtracker][Вывод Jobtracker]

**Использование Curl**

Ниже приведен пример получения сведений о кластере с помощью Curl:

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

Примечание для версии от 10.8.2014:
При использовании конечной точки Ambari, "[https://{clusterDns}.azurehdinsight.net/ambari/api/v1/clusters/{clusterDns}.azurehdinsight.net/services/{servicename}/components/{componentname}][https://{clusterDns}.azurehdinsight.net/ambari/api/v1/clusters/{clusterDns}.azurehdinsight.net/services/{servicename}/components/{componentname}]", поле *host\_name* теперь возвращает полное доменное имя (FQDN) узла вместо имени хоста. До версии от 10.8.2014 этот пример возвращал "просто значение **headnode0**". После 10.8.2014 поле возвращает вам полное доменное имя "**headnode0.{ClusterDNS}.azurehdinsight.net**” , как это показано в примере выше. Это изменение было продиктовано необходимостью использовать сценарии, в которых кластеры различного типа; например, кластеры HBase и Hadoop можно было бы размещать в одной виртуальной сети (VNET). Такая необходимость может возникнуть, например, при использовании HBase в качестве вспомогательной платформы для Hadoop.

## <span id="monitor"></span></a>Интерфейсы Ambari API для мониторинга

В следующей таблице перечислены некоторые наиболее распространенные вызовы Ambari API для мониторинга. Дополнительные сведения об интерфейсах API см. в [справочнике по Ambari API][справочнике по Ambari API].

<table border="1">
<tr>
<th>
Отслеживание вызова API

</th>
<th>
URI

</th>
<th>
Описание

</th>
</tr>
<tr>
<td>
Получение кластеров

</td>
<td>
`/api/v1/clusters`

</td>
<td>
</td>
</tr>
<tr>
<td>
Получение сведений о кластере.

</td>
<td>
`/api/v1/clusters/<ClusterName>.azurehdinsight.net`

</td>
<td>
кластеры, службы, узлы

</td>
</tr>
<tr>
<td>
Получение служб

</td>
<td>
`/api/v1/clusters/<ClusterName>.azurehdinsight.net/services`

</td>
<td>
Службы: hdfs, mapreduce

</td>
</tr>
<tr>
<td>
Получение сведений о службах.

</td>
<td>
`/api/v1/clusters/<ClusterName>.azurehdinsight.net/services/<ServiceName>`

</td>
<td>
</td>
</tr>
<tr>
<td>
Получение компонентов службы

</td>
<td>
`/api/v1/clusters/<ClusterName>.azurehdinsight.net/services/<ServiceName>/components`

</td>
<td>
HDFS: namenode, datanode
MapReduce: jobtracker; tasktracker

</td>
</tr>
<tr>
<td>
Получение сведений о компонентах.

</td>
<td>
`/api/v1/clusters/<ClusterName>.azurehdinsight.net/services/<ServiceName>/components/<ComponentName>`

</td>
<td>
ServiceComponentInfo, host-components, metrics

</td>
</tr>
<tr>
<td>
Получение узлов

</td>
<td>
`/api/v1/clusters/<ClusterName>.azurehdinsight.net/hosts`

</td>
<td>
headnode0, workernode0

</td>
</tr>
<tr>
<td>
Получение сведений об узлах.

</td>
<td>
`/api/v1/clusters/<ClusterName>.azurehdinsight.net/hosts/<HostName> `

<td>
</td>
</tr>
<tr>
<td>
Получение компонентов узлов

</td>
<td>
`/api/v1/clusters/<ClusterName>.azurehdinsight.net/hosts/<HostName>/host_components`

</td>
<td>
namenode, resourcemanager

</td>
</tr>
<tr>
<td>
Получение сведений о компонентах узла.

</td>
<td>
`/api/v1/clusters/<ClusterName>.azurehdinsight.net/hosts/<HostName>/host_components/<ComponentName>`

</td>
<td>
HostRoles, component, host, metrics

</td>
</tr>
<tr>
<td>
Получение конфигураций

</td>
<td>
`/api/v1/clusters/<ClusterName>.azurehdinsight.net/configurations `

</td>
<td>
Типы конфигураций: core-site, hdfs-site, mapred-site, hive-site

</td>
</tr>
<tr>
<td>
Получение сведений о конфигурации.

</td>
<td>
`/api/v1/clusters/<ClusterName>.azurehdinsight.net/configurations?type=<ConfigType>&tag=<VersionName> `

</td>
<td>
Типы конфигураций: core-site, hdfs-site, mapred-site, hive-site

</td>
</tr>
</table>
## <span id="nextsteps"></span></a> Дальнейшие действия

Теперь вы узнали, как использовать Ambari API для мониторинга. Дополнительные сведения см. на следующих ресурсах:

-   [Администрирование кластеров HDInsight с помощью портала управления][Администрирование кластеров HDInsight с помощью портала управления]
-   [Администрирование кластеров HDInsight с помощью Azure PowerShell][Администрирование кластеров HDInsight с помощью Azure PowerShell]
-   [Администрирование кластеров HDInsight с помощью интерфейса командной строки][Администрирование кластеров HDInsight с помощью интерфейса командной строки]
-   [Документация по HDInsight][Документация по HDInsight]
-   [Приступая к работе с HDInsight][Приступая к работе с HDInsight]

  [Что такое Ambari?]: #whatisambari
  [Предварительные требования]: #prerequisites
  [Приступая к работе]: #jumpstart
  [Интерфейсы Ambari API для мониторинга]: #monitor
  [Дальнейшие действия]: #nextsteps
  [Проект Apache Ambari]: http://ambari.apache.org/
  [справочнике по Ambari API]: https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md
  [Установка и настройка Azure PowerShell]: ../install-configure-powershell/
  [Выполнение скриптов Windows PowerShell]: http://technet.microsoft.com/ru-ru/library/ee176949.aspx
  [Curl]: http://curl.haxx.se
  [здесь]: http://curl.haxx.se/download.html
  [Приступая к работе с HDInsight]: ../hdinsight-get-started/
  [Подготовка кластеров HDInsight]: ../hdinsight-provision-clusters/
  [Вывод Jobtracker]: ./media/hdinsight-monitor-use-ambari-api/hdi.ambari.monitor.jobtracker.output.png
  [https://{clusterDns}.azurehdinsight.net/ambari/api/v1/clusters/{clusterDns}.azurehdinsight.net/services/{servicename}/components/{componentname}]: https://{clusterDns}.azurehdinsight.net/ambari/api/v1/clusters/{clusterDns}.azurehdinsight.net/services/{servicename}/components/{componentname}
  [Администрирование кластеров HDInsight с помощью портала управления]: ../hdinsight-administer-use-management-portal/
  [Администрирование кластеров HDInsight с помощью Azure PowerShell]: ../hdinsight-administer-use-powershell/
  [Администрирование кластеров HDInsight с помощью интерфейса командной строки]: ../hdinsight-administer-use-command-line/
  [Документация по HDInsight]: /ru-ru/documentation/services/hdinsight/

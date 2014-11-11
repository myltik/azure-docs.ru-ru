<properties linkid="manage-services-hdinsight-use-Ambari" urlDisplayName="Monitor Hadoop clusters  in HDInsight using the Ambari API" pageTitle="Monitor Hadoop clusters in HDInsight using the Ambari API | Azure" metaKeywords="" description="Use the Apache Ambari APIs for provisioning, managing, and monitoring Hadoop clusters. Ambari's intuitive operator tools and APIs hide the complexity of Hadoop." services="hdinsight" documentationCenter="" title="Monitor Hadoop clusters in HDInsight using the Ambari API" umbracoNaviHide="0" disqusComments="1" authors="jgao" editor="cgronlun" manager="paulettm" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="jgao" />

# Отслеживание кластеров Hadoop в HDInsight с помощью интерфейса Ambari API

Узнайте, как отслеживать кластеры HDInsight версии 2.1 с помощью Ambari API.

**Предполагаемое время выполнения:** 15 минут

## Содержание

-   [Что такое Ambari?][Что такое Ambari?]
-   [Предварительные требования][Предварительные требования]
-   [Приступая к работе][Приступая к работе]
-   [Интерфейсы Ambari API для мониторинга][Интерфейсы Ambari API для мониторинга]
-   [Дальнейшие действия][Дальнейшие действия]

## <span id="whatisambari"></span></a>Что такое Ambari?

[Проект Apache Ambari][Проект Apache Ambari] используется для подготовки, отслеживания кластеров Apache Hadoop и управления ими. Он включает в себя набор удобных средств и надежных интерфейсов API, который упрощают работу с кластерами Hadoop. Дополнительные сведения об интерфейсах API см. в [справочнике по Ambari API][справочнике по Ambari API].

В настоящее время HDInsight поддерживает только функцию мониторинга Ambari. Ambari API 1.0 поддерживается кластерами HDInsight версии 2.1 и 3.0. В этой статье описывается только использование Ambari API в кластерах HDInsight версии 2.1.

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

Ниже приведен скрипт PowerShell для получения сведений о MapReduce jobtracker.

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
                 "host_name":"workernode0"}}]}

## <span id="monitor"></span></a>Интерфейсы Ambari API для мониторинга

В следующей таблице перечислены некоторые наиболее распространенные вызовы Ambari API для мониторинга. Дополнительные сведения об интерфейсах API см. в [справочнике по Ambari API][справочнике по Ambari API].

<table>
<colgroup>
<col width="33%" />
<col width="33%" />
<col width="33%" />
</colgroup>
<thead>
<tr class="header">
<th align="left">Отслеживание вызова API</th>
<th align="left">URI</th>
<th align="left">Описание</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left">Получение кластеров</td>
<td align="left">/api/v1/clusters</td>
<td align="left"></td>
</tr>
<tr class="even">
<td align="left">Получение сведений о кластере.</td>
<td align="left">/api/v1/clusters/&lt;ClusterName&gt;.azurehdinsight.net</td>
<td align="left">кластеры, службы, узлы</td>
</tr>
<tr class="odd">
<td align="left">Получение служб</td>
<td align="left">/api/v1/clusters/&lt;ClusterName&gt;.azurehdinsight.net/services</td>
<td align="left">Службы: hdfs, mapreduce</td>
</tr>
<tr class="even">
<td align="left">Получение сведений о службах.</td>
<td align="left">/api/v1/clusters/&lt;ClusterName&gt;.azurehdinsight.net/services/&lt;ServiceName&gt;</td>
<td align="left"></td>
</tr>
<tr class="odd">
<td align="left">Получение компонентов службы</td>
<td align="left">/api/v1/clusters/&lt;ClusterName&gt;.azurehdinsight.net/services/&lt;ServiceName&gt;/components</td>
<td align="left">HDFS: namenode, datanode<br />MapReduce: jobtracker; tasktracker</td>
</tr>
<tr class="even">
<td align="left">Получение сведений о компонентах.</td>
<td align="left">/api/v1/clusters/&lt;ClusterName&gt;.azurehdinsight.net/services/&lt;ServiceName&gt;/components/&lt;ComponentName&gt;</td>
<td align="left">ServiceComponentInfo, host-components, metrics</td>
</tr>
<tr class="odd">
<td align="left">Получение узлов</td>
<td align="left">/api/v1/clusters/&lt;ClusterName&gt;.azurehdinsight.net/hosts</td>
<td align="left">headnode0, workernode0</td>
</tr>
<tr class="even">
<td align="left">Получение сведений об узлах.</td>
<td align="left">/api/v1/clusters/&lt;ClusterName&gt;.azurehdinsight.net/hosts/&lt;HostName&gt;</td>
<td align="left"></td>
</tr>
<tr class="odd">
<td align="left">Получение компонентов узлов</td>
<td align="left">/api/v1/clusters/&lt;ClusterName&gt;.azurehdinsight.net/hosts/&lt;HostName&gt;/host_components</td>
<td align="left">namenode, resourcemanager</td>
</tr>
<tr class="even">
<td align="left">Получение сведений о компонентах узла.</td>
<td align="left">/api/v1/clusters/&lt;ClusterName&gt;.azurehdinsight.net/hosts/&lt;HostName&gt;/host_components/&lt;ComponentName&gt;</td>
<td align="left">HostRoles, component, host, metrics</td>
</tr>
<tr class="odd">
<td align="left">Получение конфигураций</td>
<td align="left">/api/v1/clusters/&lt;ClusterName&gt;.azurehdinsight.net/configurations</td>
<td align="left">Типы конфигураций: core-site, hdfs-site, mapred-site, hive-site</td>
</tr>
<tr class="even">
<td align="left">Получение сведений о конфигурации.</td>
<td align="left">/api/v1/clusters/&lt;ClusterName&gt;.azurehdinsight.net/configurations?type=&lt;ConfigType&gt;&amp;tag=&lt;VersionName&gt;</td>
<td align="left">Типы конфигураций: core-site, hdfs-site, mapred-site, hive-site</td>
</tr>
</tbody>
</table>

## <span id="nextsteps"></span></a> Дальнейшие действия

Теперь вы узнали, как использовать Ambari API для мониторинга. Дополнительные сведения см. на следующих ресурсах:

-   [Администрирование кластеров HDInsight с помощью портала управления][Администрирование кластеров HDInsight с помощью портала управления]
-   [Администрирование кластеров HDInsight с помощью Azure PowerShell][Администрирование кластеров HDInsight с помощью Azure PowerShell]
-   [Администрирование кластеров HDInsight с помощью интерфейса командной строки][Администрирование кластеров HDInsight с помощью интерфейса командной строки]
-   [Документация по HDInsight][Документация по HDInsight]
-   [Приступая к работе с HDInsight][Приступая к работе с HDInsight]

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
  [Администрирование кластеров HDInsight с помощью портала управления]: ../hdinsight-administer-use-management-portal/
  [Администрирование кластеров HDInsight с помощью Azure PowerShell]: ../hdinsight-administer-use-powershell/
  [Администрирование кластеров HDInsight с помощью интерфейса командной строки]: ../hdinsight-administer-use-command-line/
  [Документация по HDInsight]: /ru-ru/documentation/services/hdinsight/

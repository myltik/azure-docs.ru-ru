---
title: "Создание кластеров HBase в виртуальной сети | Документация Майкрософт"
description: "Приступите к работе с HBase в Azure HDInsight. Узнайте, как создать кластеры HDInsight HBase в виртуальной сети Azure."
keywords: 
services: hdinsight,virtual-network
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: 8de8e446-f818-4e61-8fad-e9d38421e80d
ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/18/2016
ms.author: jgao
translationtype: Human Translation
ms.sourcegitcommit: 5919c477502767a32c535ace4ae4e9dffae4f44b
ms.openlocfilehash: 41e6338b8f8fce150e77a277c163bf71d42fb0c7


---
# <a name="create-hbase-clusters-in-azure-virtual-network"></a>Создание кластеров HBase в виртуальной сети Azure
Из этой статьи вы узнаете, как создавать кластеры Azure HDInsight HBase в [виртуальной сети Azure][1].

Благодаря интеграции виртуальной сети кластеры HBase могут быть развернуты в той же виртуальной сети, что и приложения. Это позволяет приложениям взаимодействовать с HBase непосредственно. К преимуществам относятся:

* прямое подключение веб-приложения к узлам кластера HBase, который обеспечивает обмен данными с помощью интерфейсов API удаленного вызова процедур (RPC) Java для HBase;
* повышение производительности без необходимости организации пропуска трафика через множество шлюзов и подсистемы балансировки нагрузки;
* возможность обработки конфиденциальной информации более безопасным способом, без необходимости организации общедоступной конечной точки.

### <a name="prerequisites"></a>Предварительные требования
Перед началом работы с этим учебником необходимо иметь следующее:

* **Подписка Azure**. Ознакомьтесь с [бесплатной пробной версией Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* **Рабочая станция с Azure PowerShell.**. Обратитесь к разделу [Установка и использование Azure PowerShell](https://azure.microsoft.com/documentation/videos/install-and-use-azure-powershell/).

## <a name="create-hbase-cluster-into-virtual-network"></a>Создание кластера HBase в виртуальной сети
В этом разделе мы создадим кластер HBase под управлением Linux с зависимой учетной записью службы хранилища Azure в виртуальной сети Azure. Для этих целей мы будем использовать [шаблона Azure Resource Manager](../resource-group-template-deploy.md). Сведения о других способах создания кластеров и их параметрах см. в статье [Создание кластеров Hadoop под управлением Linux в HDInsight](hdinsight-hadoop-provision-linux-clusters.md). Дополнительные сведения о создании в HDInsight кластеров Hadoop с помощью шаблонов ARM см. в статье [Создание кластеров Hadoop под управлением Windows в HDInsight с помощью шаблонов Azure Resource Manager](hdinsight-hadoop-create-windows-clusters-arm-templates.md).

> [!NOTE]
> Некоторые свойства жестко закодированы в шаблоне. Например:
>
> * **Расположение**: восточный регион США 2.
> * Версия кластера: 3.4.
> * **Число рабочих узлов кластера**: 4.
> * **Учетная запись хранения по умолчанию**: &lt;имя_кластера>store.
> * **Имя виртуальной сети**: &lt;имя_кластера>-vnet.
> * **Адресное пространство виртуальной сети**: 10.0.0.0/16.
> * **Имя подсети**: по умолчанию.
> * **Диапазон адресов подсети**: 10.0.0.0/24.
>
> Заполнитель &lt;имя_кластера> будет заменен именем кластера, которое вы укажете при использовании шаблона.
>
>

1. Щелкните следующее изображение, чтобы открыть шаблон на портале Azure. Шаблон хранится в общедоступном контейнере больших двоичных объектов.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-hbase-cluster-in-vnet.json" target="_blank"><img src="https://acom.azurecomcdn.net/80C57D/cdn/mediahandler/docarticles/dpsmedia-prod/azure.microsoft.com/en-us/documentation/articles/hdinsight-hbase-tutorial-get-started-linux/20160201111850/deploy-to-azure.png" alt="Deploy to Azure"></a>
2. В колонке **Настраиваемое развертывание** укажите следующее:

   * **Подписка**. Выберите подписку Azure, которая использовалась для создания кластера HDInsight, зависимой учетной записи хранения и виртуальной сети Azure.
   * **Группа ресурсов**.Щелкните **Создать** и укажите имя новой группы ресурсов.
   * **Расположение**. Выберите расположение группы ресурсов.
   * **Имя кластера**: введите имя создаваемого кластера Hadoop.
   * **Имя для входа и пароль кластера**: имя для входа по умолчанию — **admin**.
   * **Имя пользователя SSH и пароль**: по умолчанию используется имя **sshuser**.  Это имя можно изменить.
   * **Я принимаю указанные выше условия**. Установите этот флажок.
3. Щелкните **Приобрести**. Процесс создания кластера занимает около 20 минут. Когда кластер будет создан, щелкните его колонку на портале, чтобы открыть его.

После завершения работы с этим руководством кластер можно удалить. В случае с HDInsight ваши данные хранятся в службе хранилища Azure, что позволяет безопасно удалить неиспользуемый кластер. Плата за кластеры HDInsight взимается, даже когда они не используются. Поскольку стоимость кластера во много раз превышает стоимость хранилища, экономически целесообразно удалять неиспользуемые кластеры. Инструкции по удалению кластера см. в статье [Управление кластерами Hadoop в HDInsight с помощью портала Azure](hdinsight-administer-use-management-portal.md#delete-clusters).

Чтобы начать работу с новым HBase кластером, можно использовать процедуры, которые представлены в разделе [Приступая к работе с HBase с Hadoop в HDInsight](hdinsight-hbase-tutorial-get-started.md).

## <a name="connect-to-the-hbase-cluster-using-hbase-java-rpc-apis"></a>Подключитесь к кластеру HBase с помощью API-интерфейсов удаленного вызова процедур Java HBase
1. Создайте виртуальную машину IaaS в той же виртуальной сети Azure и той же подсети. Инструкции по созданию виртуальной машины IaaS см. в статье [Создание первой виртуальной машины Windows на портале Azure](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). При выполнении действий, описанных в этом документе, необходимо использовать следующую конфигурацию сети.

   * **Виртуальная сеть**: &lt;имя_кластера>-vnet.
   * **Подсеть**: по умолчанию.

   > [!IMPORTANT]
   > Замените &lt;имя_кластера> именем, использованным при создании кластера HDInsight на предыдущих шагах.
   >
   >

   Благодаря этим значениям виртуальная машина будет настроена для использования той же виртуальной сети и подсети, что и кластер HDInsight. Это позволит им напрямую взаимодействовать друг с другом.
2. При использовании Java-приложения для удаленного подключения к HBase необходимо использовать полное доменное имя (FQDN). Чтобы определить это, вам необходимо получить DNS-суффикс кластера HBase. Для этого используйте один из следующих методов.

   * С помощью веб-браузера сделайте вызов Ambari.

     Перейдите по адресу: https://&lt;имя_кластера>.azurehdinsight.net/api/v1/clusters/&lt;имя_кластера>/hosts?minimal_response=true. Он вернет файл JSON с DNS-суффиксами.
   * Используйте веб-сайт Ambari.

     1. Перейдите по адресу: https://&lt;имя_кластера>.azurehdinsight.net.
     2. В верхнем меню щелкните **Hosts** (Узлы).
   * Используйте Curl, чтобы выполнить вызовы REST:

         curl -u <username>:<password> -k https://<clustername>.azurehdinsight.net/ambari/api/v1/clusters/<clustername>.azurehdinsight.net/services/hbase/components/hbrest

     В возвращенных данных JSON найдите запись «host_name». В ней указаны полные доменные имена узлов в кластере. Например: 

         ...
         "host_name": "wordkernode0.<clustername>.b1.cloudapp.net
         ...

     Часть доменного имени, начинающаяся с имени кластера, является DNS-суффиксом. Например, mycluster.b1.cloudapp.net.
   * Использование Azure PowerShell

     Используйте следующий скрипт Azure PowerShell для регистрации функции **Get-ClusterDetail** , которую можно использовать для возвращения DNS-суффикса.

         function Get-ClusterDetail(
             [String]
             [Parameter( Position=0, Mandatory=$true )]
             $ClusterDnsName,
             [String]
             [Parameter( Position=1, Mandatory=$true )]
             $Username,
             [String]
             [Parameter( Position=2, Mandatory=$true )]
             $Password,
             [String]
             [Parameter( Position=3, Mandatory=$true )]
             $PropertyName
             )
         {
         <#
             .SYNOPSIS
              Displays information to facilitate an HDInsight cluster-to-cluster scenario within the same virtual network.
             .Description
              This command shows the following 4 properties of an HDInsight cluster:
              1. ZookeeperQuorum (supports only HBase type cluster)
                 Shows the value of HBase property "hbase.zookeeper.quorum".
              2. ZookeeperClientPort (supports only HBase type cluster)
                 Shows the value of HBase property "hbase.zookeeper.property.clientPort".
              3. HBaseRestServers (supports only HBase type cluster)
                 Shows a list of host FQDNs that run the HBase REST server.
              4. FQDNSuffix (supports all cluster types)
                 Shows the FQDN suffix of hosts in the cluster.
             .EXAMPLE
              Get-ClusterDetail -ClusterDnsName {clusterDnsName} -Username {username} -Password {password} -PropertyName ZookeeperQuorum
              This command shows the value of HBase property "hbase.zookeeper.quorum".
             .EXAMPLE
              Get-ClusterDetail -ClusterDnsName {clusterDnsName} -Username {username} -Password {password} -PropertyName ZookeeperClientPort
              This command shows the value of HBase property "hbase.zookeeper.property.clientPort".
             .EXAMPLE
              Get-ClusterDetail -ClusterDnsName {clusterDnsName} -Username {username} -Password {password} -PropertyName HBaseRestServers
              This command shows a list of host FQDNs that run the HBase REST server.
             .EXAMPLE
              Get-ClusterDetail -ClusterDnsName {clusterDnsName} -Username {username} -Password {password} -PropertyName FQDNSuffix
              This command shows the FQDN suffix of hosts in the cluster.
         #>

             $DnsSuffix = ".azurehdinsight.net"

             $ClusterFQDN = $ClusterDnsName + $DnsSuffix
             $webclient = new-object System.Net.WebClient
             $webclient.Credentials = new-object System.Net.NetworkCredential($Username, $Password)

             if($PropertyName -eq "ZookeeperQuorum")
             {
                 $Url = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/configurations?type=hbase-site&tag=default&fields=items/properties/hbase.zookeeper.quorum"
                 $Response = $webclient.DownloadString($Url)
                 $JsonObject = $Response | ConvertFrom-Json
                 Write-host $JsonObject.items[0].properties.'hbase.zookeeper.quorum'
             }
             if($PropertyName -eq "ZookeeperClientPort")
             {
                 $Url = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/configurations?type=hbase-site&tag=default&fields=items/properties/hbase.zookeeper.property.clientPort"
                 $Response = $webclient.DownloadString($Url)
                 $JsonObject = $Response | ConvertFrom-Json
                 Write-host $JsonObject.items[0].properties.'hbase.zookeeper.property.clientPort'
             }
             if($PropertyName -eq "HBaseRestServers")
             {
                 $Url1 = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/configurations?type=hbase-site&tag=default&fields=items/properties/hbase.rest.port"
                 $Response1 = $webclient.DownloadString($Url1)
                 $JsonObject1 = $Response1 | ConvertFrom-Json
                 $PortNumber = $JsonObject1.items[0].properties.'hbase.rest.port'

                 $Url2 = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/services/hbase/components/hbrest"
                 $Response2 = $webclient.DownloadString($Url2)
                 $JsonObject2 = $Response2 | ConvertFrom-Json
                 foreach ($host_component in $JsonObject2.host_components)
                 {
                     $ConnectionString = $host_component.HostRoles.host_name + ":" + $PortNumber
                     Write-host $ConnectionString
                 }
             }
             if($PropertyName -eq "FQDNSuffix")
             {
                 $Url = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/services/YARN/components/RESOURCEMANAGER"
                 $Response = $webclient.DownloadString($Url)
                 $JsonObject = $Response | ConvertFrom-Json
                 $FQDN = $JsonObject.host_components[0].HostRoles.host_name
                 $pos = $FQDN.IndexOf(".")
                 $Suffix = $FQDN.Substring($pos + 1)
                 Write-host $Suffix
             }
         }

     После запуска сценария Azure PowerShell используйте следующую команду, чтобы вернуть DNS-суффикс с помощью функции **Get-ClusterDetail** . При использовании этой команды укажите имя кластера HDInsight HBase, имя и пароль администратора.

         Get-ClusterDetail -ClusterDnsName <yourclustername> -PropertyName FQDNSuffix -Username <clusteradmin> -Password <clusteradminpassword>

     В результате будет возвращен DNS-суффикс. Например, **yourclustername.b4.internal.cloudapp.net**.
   * Использование удаленного рабочего стола

     Чтобы подключиться к кластеру HBase (вы будете подключены к головному узлу) и выполнить команду **ipconfig** в командной строке для получения DNS-суффикса, можно также использовать удаленный рабочий стол. Инструкции по включению протокола удаленного рабочего стола и подключению к кластеру с его помощью см. в разделе [Управление кластерами Hadoop в HDInsight с использованием портала Azure][hdinsight-admin-portal].

     ![hdinsight.hbase.dns.surffix][img-dns-surffix]

<!--
3.    Change the primary DNS suffix configuration of the virtual machine. This enables the virtual machine to automatically resolve the host name of the HBase cluster without explicit specification of the suffix. For example, the *workernode0* host name will be correctly resolved to workernode0 of the HBase cluster.

    To make the configuration change:

    1. RDP into the virtual machine.
    2. Open **Local Group Policy Editor**. The executable is gpedit.msc.
    3. Expand **Computer Configuration**, expand **Administrative Templates**, expand **Network**, and then click **DNS Client**.
    - Set **Primary DNS Suffix** to the value obtained in step 2:

        ![hdinsight.hbase.primary.dns.suffix][img-primary-dns-suffix]
    4. Click **OK**.
    5. Reboot the virtual machine.
-->

Чтобы проверить обмен данными между виртуальной машиной и кластером HBase, используйте следующую команду `ping headnode0.<dns suffix>` на виртуальной машине. Например, ping headnode0.mycluster.b1.cloudapp.net.

Чтобы использовать эту информацию в Java-приложении, для создания приложения можно придерживаться шагов, представленных в разделе [Использование Maven для создания Java-приложений, использующих HBase с HDInsight (Hadoop)](hdinsight-hbase-build-java-maven.md) . Чтобы приложение подключилось к удаленному серверу HBase, измените файл **hbase-site.xml** в этом примере, чтобы использовать полное доменное имя для ZooKeeper. Например:

    <property>
        <name>hbase.zookeeper.quorum</name>
        <value>zookeeper0.<dns suffix>,zookeeper1.<dns suffix>,zookeeper2.<dns suffix></value>
    </property>

> [!NOTE]
> Чтобы получить дополнительную информацию о разрешении имен в виртуальных сетях Azure, а также об использовании своего​ собственного DNS-сервера, ознакомьтесь с разделом [Разрешение имен (DNS)](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).
>
>

## <a name="next-steps"></a>Дальнейшие действия
В этом учебнике вы узнали, как создать кластер HBase. Дополнительные сведения см. на следующих ресурсах:

* [Приступая к работе с HDInsight](hdinsight-hadoop-linux-tutorial-get-started.md)
* [Настройка репликации HBase в HDInsight](hdinsight-hbase-geo-replication.md)
* [Создание кластеров Hadoop в HDInsight](hdinsight-provision-clusters.md)
* [Приступая к работе с HBase с Hadoop в HDInsight](hdinsight-hbase-tutorial-get-started.md)
* [Анализ мнений пользователей Twitter с использованием HBase в HDInsight](hdinsight-hbase-analyze-twitter-sentiment.md)
* [Обзор виртуальной сети][vnet-overview]

[1]: http://azure.microsoft.com/services/virtual-network/
[2]: http://technet.microsoft.com/library/ee176961.aspx
[3]: http://technet.microsoft.com/library/hh847889.aspx

[hbase-get-started]: hdinsight-hbase-tutorial-get-started.md
[hbase-twitter-sentiment]: hdinsight-hbase-analyze-twitter-sentiment.md
[vnet-overview]: ../virtual-network/virtual-networks-overview.md
[vm-create]: ../virtual-machines/virtual-machines-windows-hero-tutorial.md

[azure-portal]: https://portal.azure.com
[azure-create-storageaccount]: ../storage-create-storage-account.md
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-admin-portal]: hdinsight-administer-use-management-portal.md#connect-to-clusters-using-rdp

[hdinsight-powershell-reference]: https://msdn.microsoft.com/library/dn858087.aspx


[twitter-streaming-api]: https://dev.twitter.com/docs/streaming-apis
[twitter-statuses-filter]: https://dev.twitter.com/docs/api/1.1/post/statuses/filter


[powershell-install]: powershell-install-configure.md


[hdinsight-customize-cluster]: hdinsight-hadoop-customize-cluster.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md
[hdinsight-storage-powershell]: ../hdinsight-hadoop-use-blob-storage.md#powershell
[hdinsight-analyze-flight-delay-data]: hdinsight-analyze-flight-delay-data.md
[hdinsight-storage]: ../hdinsight-hadoop-use-blob-storage.md
[hdinsight-use-sqoop]: hdinsight-use-sqoop.md
[hdinsight-power-query]: hdinsight-connect-excel-power-query.md
[hdinsight-hive-odbc]: hdinsight-connect-excel-hive-ODBC-driver.md
[hdinsight-hbase-replication-dns]: hdinsight-hbase-geo-replication-configure-DNS.md

[img-dns-surffix]: ./media/hdinsight-hbase-provision-vnet/DNSSuffix.png
[img-primary-dns-suffix]: ./media/hdinsight-hbase-provision-vnet/PrimaryDNSSuffix.png
[img-provision-cluster-page1]: ./media/hdinsight-hbase-provision-vnet/hbasewizard1.png "Подготовка сведений для нового кластера HBase"
[img-provision-cluster-page5]: ./media/hdinsight-hbase-provision-vnet/hbasewizard5.png "Использование действия сценария для настройки кластера HBase"

[azure-preview-portal]: https://portal.azure.com



<!--HONumber=Nov16_HO3-->



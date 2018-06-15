---
title: Создание кластеров HBase в виртуальной сети Azure | Документация Майкрософт
description: Приступите к работе с HBase в Azure HDInsight. Узнайте, как создать кластеры HDInsight HBase в виртуальной сети Azure.
keywords: ''
services: hdinsight,virtual-network
documentationcenter: ''
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: 8de8e446-f818-4e61-8fad-e9d38421e80d
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 02/22/2018
ms.author: jgao
ms.openlocfilehash: edcfa47eee0f085bad415be0d9b112bbc33c3eca
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/18/2018
ms.locfileid: "31521611"
---
# <a name="create-hbase-clusters-on-hdinsight-in-azure-virtual-network"></a>Создание кластеров HBase в HDInsight в виртуальной сети Azure
Узнайте, как создавать кластеры Azure HDInsight HBase в [виртуальной сети Azure][1].

Благодаря интеграции виртуальной сети кластеры HBase могут быть развернуты в той же виртуальной сети, что и приложения. Это позволяет приложениям взаимодействовать с HBase непосредственно. К преимуществам относятся:

* прямое подключение веб-приложения к узлам кластера HBase, который обеспечивает обмен данными с помощью интерфейсов API удаленного вызова процедур (RPC) Java для HBase;
* повышение производительности без необходимости организации пропуска трафика через множество шлюзов и подсистемы балансировки нагрузки;
* возможность обработки конфиденциальной информации более безопасным способом, без необходимости организации общедоступной конечной точки.

### <a name="prerequisites"></a>предварительным требованиям
Перед началом работы с этим руководством необходимо иметь следующее:

* **Подписка Azure**. См. страницу [бесплатной пробной версии Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* **Рабочая станция с Azure PowerShell**. Обратитесь к разделу [Установка и использование Azure PowerShell](https://azure.microsoft.com/documentation/videos/install-and-use-azure-powershell/).

## <a name="create-hbase-cluster-into-virtual-network"></a>Создание кластера HBase в виртуальной сети
В этом разделе мы создадим кластер HBase под управлением Linux с зависимой учетной записью службы хранилища Azure в виртуальной сети Azure c помощью [шаблона Azure Resource Manager](../../azure-resource-manager/resource-group-template-deploy.md). Сведения о других способах создания кластеров и их параметрах см. в статье [Создание кластеров Hadoop под управлением Linux в HDInsight](../hdinsight-hadoop-provision-linux-clusters.md). Дополнительные сведения о создании в HDInsight кластеров Hadoop с помощью шаблонов ARM см. в статье [Создание кластеров Hadoop под управлением Windows в HDInsight с помощью шаблонов Azure Resource Manager](../hdinsight-hadoop-create-linux-clusters-arm-templates.md).

> [!NOTE]
> Некоторые свойства жестко заданы в шаблоне. Например: 
>
> * **Расположение**: восточный регион США 2.
> * **Версия кластера**: 3.6
> * **Число рабочих узлов кластера**: 2.
> * **Учетная запись хранения по умолчанию**: уникальная строка
> * **Имя виртуальной сети**: &lt;имя_кластера>-vnet.
> * **Адресное пространство виртуальной сети**: 10.0.0.0/16.
> * **Имя подсети**: subnet1
> * **Диапазон адресов подсети**: 10.0.0.0/24.
>
> Заполнитель &lt;имя_кластера> будет заменен именем кластера, которое вы укажете при использовании шаблона.
>
>

1. Щелкните следующее изображение, чтобы открыть шаблон на портале Azure. Шаблон находится в [шаблонах быстрого запуска Azure](https://azure.microsoft.com/resources/templates/101-hdinsight-hbase-linux-vnet/).

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-hbase-linux-vnet%2Fazuredeploy.json" target="_blank"><img src="./media/apache-hbase-provision-vnet/deploy-to-azure.png" alt="Deploy to Azure"></a>
2. В колонке **Настраиваемое развертывание** укажите следующие свойства.

   * **Подписка**. Выберите подписку Azure, которая использовалась для создания кластера HDInsight, зависимой учетной записи хранения и виртуальной сети Azure.
   * **Группа ресурсов**.Щелкните **Создать** и укажите имя новой группы ресурсов.
   * **Расположение**. Выберите расположение группы ресурсов.
   * **Имя_кластера**. Введите имя создаваемого кластера Hadoop.
   * **Имя для входа и пароль кластера**: имя для входа по умолчанию — **admin**.
   * **Имя пользователя SSH и пароль**: по умолчанию используется имя **sshuser**.  Это имя можно изменить.
   * **Я принимаю указанные выше условия**. Установите этот флажок.
3. Щелкните **Приобрести**. Процесс создания кластера занимает около 20 минут. Когда кластер будет создан, щелкните его колонку на портале, чтобы открыть его.

После завершения работы с этим руководством кластер можно удалить. В случае с HDInsight ваши данные хранятся в службе хранилища Azure, что позволяет безопасно удалить неиспользуемый кластер. Плата за кластеры HDInsight взимается, даже когда они не используются. Поскольку стоимость кластера во много раз превышает стоимость хранилища, экономически целесообразно удалять неиспользуемые кластеры. Инструкции по удалению кластера см. в статье [Управление кластерами Hadoop в HDInsight с помощью портала Azure](../hdinsight-administer-use-management-portal.md#delete-clusters).

Чтобы начать работу с новым HBase кластером, можно использовать процедуры, которые представлены в разделе [Приступая к работе с HBase с Hadoop в HDInsight](./apache-hbase-tutorial-get-started-linux.md).

## <a name="connect-to-the-hbase-cluster-using-hbase-java-rpc-apis"></a>Подключитесь к кластеру HBase с помощью API-интерфейсов удаленного вызова процедур Java HBase
1. Создайте виртуальную машину IaaS в той же виртуальной сети Azure и той же подсети. Инструкции по созданию виртуальной машины IaaS см. в статье [Создание первой виртуальной машины Windows на портале Azure](../../virtual-machines/windows/quick-create-portal.md). При выполнении действий, описанных в этом документе, необходимо использовать следующие значения для конфигурации сети.

   * **Виртуальная сеть**: &lt;имя_кластера>-vnet.
   * **Подсеть**: subnet1

   > [!IMPORTANT]
   > Замените &lt;имя_кластера> именем, использованным при создании кластера HDInsight на предыдущих шагах.
   >
   >

   Благодаря этим значениям виртуальная машина будет размещена в той же виртуальной сети и подсети, что и кластер HDInsight. Эта конфигурация позволит им напрямую взаимодействовать друг с другом. Существует возможность создания кластера HDInsight с пустым граничным узлом. Граничный узел можно использовать для управления кластером.  Подробные сведения см. в статье [Использование пустых граничных узлов в HDInsight](../hdinsight-apps-use-edge-node.md).

2. При использовании Java-приложения для удаленного подключения к HBase необходимо использовать полное доменное имя (FQDN). Чтобы определить это, вам необходимо получить DNS-суффикс кластера HBase. Для этого используйте один из следующих методов.

   * С помощью веб-браузера сделайте вызов Ambari.

     Перейдите по адресу: https://&lt;имя_кластера>.azurehdinsight.net/api/v1/clusters/&lt;имя_кластера>/hosts?minimal_response=true. Он вернет файл JSON с DNS-суффиксами.
   * Используйте веб-сайт Ambari.

     1. Перейдите по адресу: https://&lt;имя_кластера>.azurehdinsight.net.
     2. В верхнем меню щелкните **Hosts** (Узлы).
   * Используйте Curl, чтобы выполнить вызовы REST:

    ```bash
        curl -u <username>:<password> -k https://<clustername>.azurehdinsight.net/ambari/api/v1/clusters/<clustername>.azurehdinsight.net/services/hbase/components/hbrest
    ```

     В возвращенных данных JSON найдите запись «host_name». Она содержит полные доменные имена узлов в кластере. Например: 

         ...
         "host_name": "wordkernode0.<clustername>.b1.cloudapp.net
         ...

     Часть доменного имени, начинающаяся с имени кластера, является DNS-суффиксом. Например, mycluster.b1.cloudapp.net.
   * Использование Azure PowerShell

     Используйте следующий скрипт Azure PowerShell для регистрации функции **Get-ClusterDetail** , которую можно использовать для возвращения DNS-суффикса.

    ```powershell
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
    ```

     После запуска сценария Azure PowerShell используйте следующую команду, чтобы вернуть DNS-суффикс с помощью функции **Get-ClusterDetail** . При использовании этой команды укажите имя кластера HDInsight HBase, имя и пароль администратора.

    ```powershell
        Get-ClusterDetail -ClusterDnsName <yourclustername> -PropertyName FQDNSuffix -Username <clusteradmin> -Password <clusteradminpassword>
    ```

     Эта команда возвращает DNS-суффикс. Например, **yourclustername.b4.internal.cloudapp.net**.


<!--
3.    Change the primary DNS suffix configuration of the virtual machine. This enables the virtual machine to automatically resolve the host name of the HBase cluster without explicit specification of the suffix. For example, the *workernode0* host name will be correctly resolved to workernode0 of the HBase cluster.

    To make the configuration change:

    1. RDP into the virtual machine.
    2. Open **Local Group Policy Editor**. The executable is gpedit.msc.
    3. Expand **Computer Configuration**, expand **Administrative Templates**, expand **Network**, and then click **DNS Client**.
    - Set **Primary DNS Suffix** to the value obtained in step 2:

        ![hdinsight.hbase.primary.dns.suffix](./media/apache-hbase-provision-vnet/PrimaryDNSSuffix.png)
    4. Click **OK**.
    5. Reboot the virtual machine.
-->

Чтобы проверить обмен данными между виртуальной машиной и кластером HBase, используйте следующую команду `ping headnode0.<dns suffix>` на виртуальной машине. Например, ping headnode0.mycluster.b1.cloudapp.net.

Чтобы использовать эту информацию в Java-приложении, для создания приложения можно придерживаться шагов, представленных в разделе [Использование Maven для создания Java-приложений, использующих HBase с HDInsight (Hadoop)](./apache-hbase-build-java-maven-linux.md) . Чтобы приложение подключилось к удаленному серверу HBase, измените файл **hbase-site.xml** в этом примере, чтобы использовать полное доменное имя для ZooKeeper. Например: 

    <property>
        <name>hbase.zookeeper.quorum</name>
        <value>zookeeper0.<dns suffix>,zookeeper1.<dns suffix>,zookeeper2.<dns suffix></value>
    </property>

> [!NOTE]
> Чтобы получить дополнительную информацию о разрешении имен в виртуальных сетях Azure, а также об использовании своего​ собственного DNS-сервера, ознакомьтесь со статьей [Разрешение имен для ВМ и экземпляров ролей](../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).
>
>

## <a name="next-steps"></a>Дополнительная информация
Из этого руководства вы узнали, как создать кластер HBase. Дополнительные сведения см. на следующих ресурсах:

* [Приступая к работе с HDInsight](../hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Использование пустых граничных узлов в HDInsight](../hdinsight-apps-use-edge-node.md)
* [Настройка репликации HBase в HDInsight](apache-hbase-replication.md)
* [Создание кластеров Hadoop в HDInsight](../hdinsight-hadoop-provision-linux-clusters.md)
* [Приступая к работе с HBase с Hadoop в HDInsight](./apache-hbase-tutorial-get-started-linux.md)
* [Обзор виртуальной сети](../../virtual-network/virtual-networks-overview.md)

[1]: http://azure.microsoft.com/services/virtual-network/
[2]: http://technet.microsoft.com/library/ee176961.aspx
[3]: http://technet.microsoft.com/library/hh847889.aspx


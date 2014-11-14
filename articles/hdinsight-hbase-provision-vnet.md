<properties urlDisplayName="Provision HBase clusters on Azure Virtual Network" pageTitle="Подготовка кластеров HBase в виртуальной сети Azure | Azure" metaKeywords="" description="Узнайте, как создать кластеры HDInsight в виртуальной сети Azure." metaCanonical="" services="hdinsight" documentationCenter="" title="Подготовка кластеров HBase в виртуальной сети Azure" authors="jgao" solutions="big-data" manager="paulettm" editor="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="08/21/2014" ms.author="jgao" />

# Подготовка кластеров HBase в виртуальной сети Azure

Узнайте, как создать кластеры HDInsight в виртуальной сети Azure.

Благодаря интеграции виртуальной сети кластеры HBase могут быть развернуты в той же виртуальной сети, что и приложения. Это позволяет приложениям взаимодействовать с HBase непосредственно. К преимуществам относятся:

-   прямое подключение веб-приложения к узлам кластера HBase, который обеспечивает обмен данными с помощью API-интерфейсов HBase Java RPC;
-   повышение производительности без необходимости организации пропуска трафика через множество шлюзов и балансировки нагрузки;
-   обработка конфиденциальной информации более безопасным способом, без необходимости организации общедоступной конечной точки.

## Содержание

-   [Предварительные требования][Предварительные требования]
-   [Подготовка кластеров HBase в виртуальной сети Azure][Подготовка кластеров HBase в виртуальной сети Azure]
-   [Подключайтесь к подготовленному в виртуальной сети кластеру HBase с помощью интерфейсов API Java RPC для HBase.][Подключайтесь к подготовленному в виртуальной сети кластеру HBase с помощью интерфейсов API Java RPC для HBase.]
-   [Подготовка кластера HBase с помощью PowerShell][Подготовка кластера HBase с помощью PowerShell]
-   [Дальнейшие действия][Дальнейшие действия]

## <span id="prerequisites"></span></a>Предварительные требования

Перед началом работы с этим учебником необходимо иметь следующее:

-   **Подписка Azure.**. Azure — это платформа на основе подписок. Дополнительные сведения о получении подписки см. в разделах [Варианты приобретения][Варианты приобретения], [Предложения для участников][Предложения для участников] или [Бесплатное пробное использование][Бесплатное пробное использование].

-   **Рабочая станция, на которой установлена и настроена среда Azure PowerShell**. Инструкции см. в разделе [Установка и настройка Azure PowerShell][Установка и настройка Azure PowerShell]. Для выполнения скриптов PowerShell необходимо запустить Azure PowerShell с правами администратора и задать политику выполнения *RemoteSigned*. См. раздел [Выполнение скриптов Windows PowerShell][Выполнение скриптов Windows PowerShell].

    Перед выполнением скриптов PowerShell убедитесь, что вы подключены к подписке Azure, с помощью следующего командлета:

        Add-AzureAccount

    При наличии нескольких подписок Azure используется следующий командлет для установки текущей подписки:

        Select-AzureSubscription <AzureSubscriptionName>

## <span id="hbaseprovision"></span></a>Подготовка кластера HBase в виртуальной сети Azure.

**Чтобы создать виртуальную сеть с помощью портала управления:**

1.  Выполните вход на [портал управления Azure][портал управления Azure].
2.  В нижней части страницы щелкните **НОВЫЙ**, щелкните **СЕТЕВЫЕ СЛУЖБЫ**, щелкните **ВИРТУАЛЬНАЯ СЕТЬ** и затем **БЫСТРОЕ СОЗДАНИЕ**.
3.  Введите или выберите следующие значения:

    -   **Имя**: Название виртуальной сети.
    -   **Адресное пространство**: Выберите адресное пространство виртуальной сети таким образом, чтобы оно было достаточно большим для обеспечения адресов для всех узлов в кластере. В противном случае подготовка завершится с ошибкой.
    -   **Максимальное количество виртуальных машин**: Выберите максимальное количество виртуальных машин.
    -   **Location**: Расположение должно совпадать с расположением кластера HBase, который вы будете создавать.
    -   **Сервер DNS**: В этой статье используется внутренний сервер DNS, предоставленный Azure, поэтому вы можете выбрать **Нет**. Также поддерживаются более продвинутые сетевые конфигурации с другими серверами DNS. Подробное руководство см. по адресу [][]<http://msdn.microsoft.com/library/azure/jj156088.aspx></a>.

4.  Щелкните **Создать виртуальную сеть**. В списке появится имя новой виртуальной сети. Подождите, пока в столбце «Состояние» не появится значение **Создано**.
5.  В основной области щелкните только что созданную виртуальную сеть.
6.  В верхней части страницы щелкните **ПАНЕЛЬ МОНИТОРИНГА**.
7.  В разделе **Сводка** узнайте и запишите **ИДЕНТИФИКАТОР ВИРТУАЛЬНОЙ СЕТИ**. Он вам понадобится в процессе подготовки кластера HBase.
8.  В верхней части страницы щелкните **КОНФИГУРАЦИЯ**.
9.  В нижней части страницы указано имя подсети по умолчанию **Subnet-1**. При желании вы можете переименовать подсеть или добавить новую подсеть для кластера HBase. Запишите имя подсети, оно вам понадобится в процессе подготовки кластера.
10. Проверьте **CIDR(КОЛИЧЕСТВО АДРЕСОВ)** для подсети, которая будет использоваться в кластере. Количество адресов должен быть больше, чем количество рабочих узлов плюс семь (шлюзов: 2, головной узел: 2, узел Zookeeper: 3). Например, если вам нужен кластер HBase на 10 узлов, количество адресов для подсети должно быть больше 17 (10 + 7). В противном случае подготовка завершится с ошибкой.

    > [WACOM.NOTE] Настоятельно рекомендуется назначать одну подсеть для одного кластера.

11. Нажмите **Сохранить** в нижней части страницы в том случае, если вы изменяли значения для подсети.

> [WACOM.NOTE] Кластеры HDInsight используют хранилища BLOB-объектов Azure для хранения данных. Дополнительные сведения см. в разделе [Использование хранилища BLOB-объектов Azure с Hadoop в HDInsight][Использование хранилища BLOB-объектов Azure с Hadoop в HDInsight]. Вам понадобится учетная запись хранения и контейнер для хранения BLOB-объектов. Расположение учетной записи хранения должно соответствовать расположению виртуальной сети и кластера.

**Чтобы создать учетную запись хранения Azure Storage и контейнер для хранения BLOB-объектов:**

1.  Выполните вход на [портал управления Azure][портал управления Azure].
2.  Щелкните **СОЗДАТЬ** в левом нижнем углу, выберите **СЛУЖБЫ ДАННЫХ**, **ХРАНИЛИЩЕ**, а затем щелкните **БЫСТРО СОЗДАТЬ**.

3.  Введите или выберите следующие значения:

    -   **URL-адрес**: имя учетной записи хранения
    -   **Location**: расположение учетной записи хранения. Убедитесь, что оно совпадает с расположением виртуальной сети. Территориальные группы не поддерживаются.
    -   **РЕПЛИКАЦИЯ**: В целях тестирования используйте **Локально избыточная** для снижения затрат.

4.  Щелкните **СОЗДАТЬ УЧЕТНУЮ ЗАПИСЬ ХРАНЕНИЯ**. Вы увидите новую учетную запись хранения в списке хранилищ.
5.  Дождитесь, пока **СОСТОЯНИЕ** новой учетной записи хранения не изменится на **В сети**.
6.  Щелкните новую учетную запись хранения в списке, чтобы выбрать ее.
7.  Щелкните **УПРАВЛЕНИЕ КЛЮЧАМИ ДОСТУПА** в нижней части страницы.
8.  Запишите **ИМЯ УЧЕТНОЙ ЗАПИСИ ХРАНЕНИЯ** и **ПЕРВИЧНЫЙ КЛЮЧ ДОСТУПА**(или **ВТОРИЧНЫЙ КЛЮЧ ДОСТУПА**. Будет работать любой из ключей). Они потребуются позже в данном руководстве.
9.  В верхней части страницы щелкните **КОНТЕЙНЕР**.
10. В нижней части страницы щелкните **ДОБАВИТЬ**.
11. Введите имя контейнера. Этот контейнер будет использоваться в качестве контейнера по умолчанию для кластера HBase. По умолчанию имя контейнера по умолчанию соответствует имени кластера. Значение поля **ДОСТУП** оставьте **Частный**.
12. Чтобы создать контейнер, поставьте галочку.

**Подготовка кластера HBase с помощью портала Azure:**

> [WACOM.NOTE] Для получения информации о подготовке кластера HBase с помощью PowerShell см. раздел [Подготовка кластера HBase с помощью PowerShell][Подготовка кластера HBase с помощью PowerShell].

1.  Выполните вход на [портал управления Azure][портал управления Azure].
2.  Нажмите кнопку **СОЗДАТЬ** в нижнем левом углу, укажите **СЛУЖБЫ ДАННЫХ**, укажите **HDINSIGHT**, а затем нажмите кнопку **НАСТРАИВАЕМОЕ СОЗДАНИЕ**.
3.  Введите **ИМЯ КЛАСТЕРА** и выберите пункт **ВЕРСИЯ HDINSIGHT**, чтобы использовать его для кластера.

    ![поля для указания имени кластера и его версии][поля для указания имени кластера и его версии]

4.  Выберите количество **УЗЛЫ ДАННЫХ**, чтобы создать кластер и регион или виртуальную сеть Azure (**РЕГИОН/ВИРТУАЛЬНАЯ СЕТЬ**), чтобы использовать для этого кластера.

    ![поля для указания количества узлов и региона][поля для указания количества узлов и региона]

5.  Введите **ИМЯ ПОЛЬЗОВАТЕЛЯ** и **ПАРОЛЬ** администратора, чтобы использовать их для этого кластера.

    ![поля для указания имени и пароля администратора][поля для указания имени и пароля администратора]

6.  Выберите, какую следует использовать учетную запись хранения: новую или существующую. Если новую, введите **ИМЯ УЧЕТНОЙ ЗАПИСИ** и **КОНТЕЙНЕР ПО УМОЛЧАНИЮ**, чтобы ее создать. Наконец, установите флажок, чтобы создать кластер.

    ![выбор учетной записи хранения][выбор учетной записи хранения]

Чтобы начать работу с новым HBase кластером, можно использовать процедуры, которые представлены в разделе [Приступая к работе с HBase с Hadoop в HDInsight][Приступая к работе с HBase с Hadoop в HDInsight].

## <span id="connect"></span></a>Подключайтесь к подготовленному в виртуальной сети кластеру HBase с помощью интерфейсов API Java RPC для HBase.

1.  Подготовка виртуальной машины IaaS в той же виртуальной сети Azure и той же подсети. Поэтому виртуальная машина и кластер HBase будут использовать один и тот же внутренний сервер DNS для разрешения имен узлов. Чтобы сделать это, необходимо вызвать выбор вариантов из коллекции и выбрать виртуальную сеть вместо центра обработки данных. Инструкции см. в разделе [Создание виртуальной машины под управлением Windows Server][Создание виртуальной машины под управлением Windows Server]. Достаточно стандартного образа Windows Server 2012 с виртуальной машиной небольшого размера.

2.  При использовании Java-приложения для удаленного подключения к HBase необходимо использовать полное доменное имя (FQDN). Чтобы это определить, мы должны получить DNS-суффикс для подключения HBase кластера. Для этого используется программа Curl, предназначенная для формирования запроса Ambari, или удаленный рабочий стол для подключения к кластеру.

    -   **Curl** — используйте следующую команду:

            curl -u <username>:<password> -k https://<clustername>.azurehdinsight.net/ambari/api/v1/clusters/<clustername>.azurehdinsight.net/services/hbase/components/hbrest

        В возвращенных данных JSON найдите запись "host\_name" (имя хоста). Она будет содержать полное доменное имя (FQDN) для узлов кластера. Например:

            ...
            "host_name": "wordkernode0.<clustername>.b1.cloudapp.net
            ...

        Часть доменного имени, начинающаяся с имени кластера, является DNS-суффиксом. Например, mycluster.b1.cloudapp.net.

    -   **PowerShell** — используйте следующий скрипт PowerShell для регистрации функции **Get-ClusterDetail**, которая может быть использована для возвращения DNS-суффикса.

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
                 Displays information to facilitate HDInsight cluster to cluster scinario within same virtual network.
                .Description
                 This command shows following 4 properties of an HDInsight cluster.
                 1. ZookeeperQuorum (only support HBase type cluster)
                    Shows the value of hbase property "hbase.zookeeper.quorum".
                 2. ZookeeperClientPort (only support HBase type cluster)
                    Shows the value of hbase property "hbase.zookeeper.property.clientPort".
                 3. HBaseRestServers (only support HBase type cluster)
                    Shows a list of host FQDNs which run HBase rest server.
                 4. FQDNSuffix (support all type cluster)
                    Shows FQDN suffix of hosts in the cluster.
                .EXAMPLE
                 Get-ClusterDetail -ClusterDnsName {clusterDnsName} -Username {username} -Password {password} -PropertyName ZookeeperQuorum
                 This command shows the value of hbase property "hbase.zookeeper.quorum".
                .EXAMPLE
                 Get-ClusterDetail -ClusterDnsName {clusterDnsName} -Username {username} -Password {password} -PropertyName ZookeeperClientPort
                 This command shows the value of hbase property "hbase.zookeeper.property.clientPort".
                .EXAMPLE
                 Get-ClusterDetail -ClusterDnsName {clusterDnsName} -Username {username} -Password {password} -PropertyName HBaseRestServers
                 This command shows a list of host FQDNs which run HBase rest server.
                .EXAMPLE
                 Get-ClusterDetail -ClusterDnsName {clusterDnsName} -Username {username} -Password {password} -PropertyName FQDNSuffix
                 This command shows FQDN suffix of hosts in the cluster.
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
                    $Url = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/services/yarn/components/resourcemanager"
                    $Response = $webclient.DownloadString($Url)
                    $JsonObject = $Response | ConvertFrom-Json
                    $FQDN = $JsonObject.host_components[0].HostRoles.host_name
                    $pos = $FQDN.IndexOf(".")
                    $Suffix = $FQDN.Substring($pos + 1)
                    Write-host $Suffix
                }
            }

        После запуска сценария PowerShell используйте следующую команду, чтобы вернуть DNS-суффикс с помощью функции Get-ClusterDetail. При использовании этой команды укажите имя кластера HDInsight HBase, имя и пароль администратора.

            Get-ClusterDetail -ClusterDnsName <yourclustername> -PropertyName FQDNSuffix -Username <clusteradmin> -Password <clusteradminpassword>

        В результате будет возвращен DNS-суффикс. Например, **yourclustername.b4.internal.cloudapp.net**.

    > [WACOM.NOTE] Для подключения к кластеру HBase (вы будете подключены к headnode) и запуска команды **ipconfig** из командной строки с целью получения DNS-суффикса также может использоваться удаленный рабочий стол. Для получения инструкций по включению удаленного рабочего стола и подключению к кластеру, см. раздел [Управление кластерами Hadoop в HDInsight с помощью портала управления Azure][Управление кластерами Hadoop в HDInsight с помощью портала управления Azure].
    >
    > ![hdinsight.hbase.dns.surffix][hdinsight.hbase.dns.surffix]

<!--  3.  Change the Primary DNS Suffix configuration of the virtual machine. This enables virtual machine to automatically resolve the host name of the HBase cluster without explicit specification of the suffix. For example, the *workernode0* host name will be correctly resolved to the workernode0 of the HBase cluster.       To make the configuration change:      1. RDP into the virtual machine.      2. Open **Local Group Policy Editor**. The executable is gpedit.msc.     3. Expand **Computer Configuration**, expand **Administrative Templates**, expand **Network**, and then click **DNS Client**.      - Set **Primary DNS Suffix** to the value obtained in the step 2:           ![hdinsight.hbase.primary.dns.suffix][img-primary-dns-suffix]     4. Click **OK**.      5. Reboot the virtual machine. -->

Для обеспечения обмена данными между виртуальной машиной и кластером HBase используйте следующую команду `ping headnode0.<dns suffix>`, запущенную на виртуальной машине. Например, ping headnode0.mycluster.b1.cloudapp.net

Чтобы использовать эту информацию в Java-приложении, для создания приложения можно придерживаться шагов, представленных в разделе [Использование Maven для создания Java-приложений, использующих HBase с HDInsight (Hadoop)][Использование Maven для создания Java-приложений, использующих HBase с HDInsight (Hadoop)]. Для того чтобы приложение подключилось к удаленному серверу HBase, измените в этом примере файл **hbase-site.xml**, чтобы использовать полное доменное имя для ZooKeeper. Например:

    <property>
        <name>hbase.zookeeper.quorum</name>
        <value>zookeeper0.<dns suffix>,zookeeper1.<dns suffix>,zookeeper2.<dns suffix></value>
    </property>

> [WACOM.NOTE] Для получения дополнительной информации о разрешении имен в виртуальных сетях Azure, а также об использовании своего ​​собственного сервера DNS см. раздел [Разрешение имен (DNS)][Разрешение имен (DNS)].

## <span id="powershell"></span></a>Подготовка кластера HBase с помощью Azure PowerShell:\*\*

1.  Откройте интегрированную среду сценариев PowerShell.
2.  Скопируйте следующую команду и вставьте ее в окно запроса.

        $hbaseClusterName = "<HBaseClusterName>"
        $hadoopUserName = "<HBaseClusterUsername>"
        $hadoopUserPassword = "<HBaseClusterUserPassword>"
        $location = "<HBaseClusterLocation>"  #i.e. "West US"
        $clusterSize = <HBaseClusterSize>  
        $vnetID = "<AzureVirtualNetworkID>"
        $subNetName = "<AzureVirtualNetworkSubNetName>"
        $storageAccountName = "<AzureStorageAccountName>" # Do not use the full name here
        $storageAccountKey = "<AzureStorageAccountKey>"
        $storageContainerName = "<AzureBlobStorageContainer>"

        $password = ConvertTo-SecureString $hadoopUserPassword -AsPlainText -Force
        $creds = New-Object System.Management.Automation.PSCredential ($hadoopUserName, $password) 

        New-AzureHDInsightCluster -Name $hbaseClusterName `
                                  -ClusterType HBase `
                                  -Version 3.1 `
                                  -Location $location `
                                  -ClusterSizeInNodes $clusterSize `
                                  -Credential $creds `
                                  -VirtualNetworkId $vnetID `
                                  -SubnetName $subNetName `
                                  -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" `
                                  -DefaultStorageAccountKey $storageAccountKey `
                                  -DefaultStorageContainerName $storageContainerName

3.  Щелкните **Запустить сценарий** или нажмите клавишу **F5**.
4.  Вы можете либо проверить кластер через портал управления, либо запустить в нижней панели следующий командлет PowerShell:

    Get-AzureHDInsightCluster

## <span id="nextsteps"></span></a> Дальнейшие действия

В этом учебнике мы изучили способ подготовки кластера HBase. Дополнительные сведения см. на следующих ресурсах:

-   [Приступая к работе с HDInsight][Приступая к работе с HDInsight]
-   [Подготовка кластеров Hadoop в HDInsight][Подготовка кластеров Hadoop в HDInsight]
-   [Начало работы с использованием HBase с Hadoop в HDInsight][Приступая к работе с HBase с Hadoop в HDInsight]
-   [Анализ мнений пользователей Twitter с использованием HBase в HDInsight][Анализ мнений пользователей Twitter с использованием HBase в HDInsight]
-   [Обзор виртуальной сети][Обзор виртуальной сети].

  [Предварительные требования]: #prerequisites
  [Подготовка кластеров HBase в виртуальной сети Azure]: #hbaseprovision
  [Подключайтесь к подготовленному в виртуальной сети кластеру HBase с помощью интерфейсов API Java RPC для HBase.]: #connect
  [Подготовка кластера HBase с помощью PowerShell]: #powershell
  [Дальнейшие действия]: #nextsteps
  [Варианты приобретения]: http://azure.microsoft.com/ru-ru/pricing/purchase-options/
  [Предложения для участников]: http://azure.microsoft.com/ru-ru/pricing/member-offers/
  [Бесплатное пробное использование]: http://azure.microsoft.com/ru-ru/pricing/free-trial/
  [Установка и настройка Azure PowerShell]: ../install-configure-powershell
  [Выполнение скриптов Windows PowerShell]: http://technet.microsoft.com/ru-ru/library/ee176949.aspx
  [портал управления Azure]: https://manage.windowsazure.com
  []: http://msdn.microsoft.com/library/azure/jj156088.aspx
  [Использование хранилища BLOB-объектов Azure с Hadoop в HDInsight]: ../hdinsight-use-blob-storage/
  [поля для указания имени кластера и его версии]: ./media/hdinsight-hbase-provision-vnet/hbasewizard1.png
  [поля для указания количества узлов и региона]: ./media/hdinsight-hbase-provision-vnet/hbasewizard2.png
  [поля для указания имени и пароля администратора]: ./media/hdinsight-hbase-provision-vnet/hbasewizard3.png
  [выбор учетной записи хранения]: ./media/hdinsight-hbase-provision-vnet/hbasewizard4.png
  [Приступая к работе с HBase с Hadoop в HDInsight]: ../hdinsight-hbase-get-started/
  [Создание виртуальной машины под управлением Windows Server]: ../virtual-machines-windows-tutorial/
  [Управление кластерами Hadoop в HDInsight с помощью портала управления Azure]: ../hdinsight-administer-use-management-portal/#rdp
  [hdinsight.hbase.dns.surffix]: ./media/hdinsight-hbase-provision-vnet/DNSSuffix.png
  [Использование Maven для создания Java-приложений, использующих HBase с HDInsight (Hadoop)]: azure.microsoft.com/ru-ru/documentation/articles/hdinsight-hbase-build-java-maven/
  [Разрешение имен (DNS)]: http://msdn.microsoft.com/ru-ru/library/azure/jj156088.aspx
  [Приступая к работе с HDInsight]: ../hdinsight-get-started/
  [Подготовка кластеров Hadoop в HDInsight]: ../hdinsight-provision-clusters/
  [Анализ мнений пользователей Twitter с использованием HBase в HDInsight]: ../hdinsight-hbase-twitter-sentiment/
  [Обзор виртуальной сети]: http://msdn.microsoft.com/library/azure/jj156007.aspx

<properties
	pageTitle="Подготовка кластеров HBase в виртуальной сети | Microsoft Azure"
	description="Приступите к работе с HBase в Azure HDInsight. Узнайте, как создать кластеры HDInsight HBase в виртуальной сети Azure."
	keywords=""
	services="hdinsight,virtual-network"
	documentationCenter=""
	authors="mumian"
	manager="paulettm"
	editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="08/12/2015"
   ms.author="jgao"/>

# Подготовка кластеров HBase в виртуальной сети Azure 

Узнайте, как создать кластеры Azure HDInsight HBase в [виртуальной сети Azure][1].

[AZURE.INCLUDE [hdinsight-azure-preview-portal](../../includes/hdinsight-azure-preview-portal.md)]

* [Подготовка кластеров HBase в виртуальной сети Azure](hdinsight-hbase-provision-vnet-v1.md)

Благодаря интеграции виртуальной сети кластеры HBase могут быть развернуты в той же виртуальной сети, что и приложения. Это позволяет приложениям взаимодействовать с HBase непосредственно. К преимуществам относятся:

- прямое подключение веб-приложения к узлам кластера HBase, который обеспечивает обмен данными с помощью интерфейсов API удаленного вызова процедур (RPC) Java для HBase;
- повышение производительности без необходимости организации пропуска трафика через множество шлюзов и подсистемы балансировки нагрузки;
- возможность обработки конфиденциальной информации более безопасным способом, без необходимости организации общедоступной конечной точки.

##Предварительные требования
Перед началом работы с этим учебником необходимо иметь следующее:

- **Подписка Azure.**. См. [Бесплатная пробная версия Azure](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

- **Рабочая станция с Azure PowerShell.**. Обратитесь к разделу [Установка и использование Azure PowerShell](http://azure.microsoft.com/documentation/videos/install-and-use-azure-powershell/). Инструкции см. в разделе [Установка и настройка Azure PowerShell](../install-configure-powershell.md). Для выполнения сценариев Azure PowerShell необходимо запустить Azure PowerShell с правами администратора и задать политику выполнения *RemoteSigned*. См. раздел [Использование командлета Set-ExecutionPolicy][2].

	Перед выполнением сценариев Azure PowerShell убедитесь, что вы подключены к подписке Azure, с помощью следующего командлета:

		Add-AzureAccount

	При наличии нескольких подписок Azure используется следующий командлет для установки текущей подписки:

		Select-AzureSubscription <AzureSubscriptionName>


## Подготовка кластера HBase в виртуальной сети Azure

Как правило, приложения состоят из большого количества компонентов. В этом руководстве рассматривается следующее:

- виртуальная сеть Azure;
- учетная запись хранения Azure;
- кластер Azure HDInsight в HBase;
- виртуальная машина Azure, используемая в качестве DNS-сервера (необязательно).

Диспетчер ресурсов Azure позволяет вам работать с ресурсами в приложении в виде группы. Вы можете развертывать, обновлять или удалять все ресурсы для приложения в рамках одной скоординированной операции. Для развертывания вы используете шаблон, который можно использовать для разных сред, в том числе для тестовой, промежуточной и рабочей. Вы можете уточнить счета для своей организации, просмотрев сведенные затраты для всей группы.

**Создание группы ресурсов**

1. Выполните вход на [портал предварительной версии Azure](https://portal.azure.com).
2. В меню **Создать** выберите **Управление**, а затем — **Группа ресурсов**.
3. Введите или выберите следующие значения:

	- **Имя группы ресурсов** — введите имя для группы ресурсов.
	- **Подписки** — выберите подписку Azure, которая используется для этой группы ресурсов.
	- **Расположение группы ресурсов** — выберите центр обработки данных Azure. Это расположение не должно соответствовать расположению кластера HDInsight.

4. Щелкните **Создать**.

Перед подготовкой кластера HBase у вас должна быть виртуальная сеть Azure.

**Создание виртуальной сети с помощью портала управления Azure**

1. Войдите на [портал предварительной версии](https://portal.azure.com).
2. В меню **Создать** выберите **Сети**, а затем — **Виртуальная сеть**.
3. В разделе **Выбор модели развертывания** щелкните **Классическая** и нажмите кнопку **Создать**.

	> [AZURE.NOTE]Вам не удастся использовать виртуальную сеть Azure v1 (классическую) с HDInsight. Необходимо использовать виртуальную сеть v2 (диспетчер ресурсов Azure), чтобы ее можно было выбрать при создании кластера HDInsight на портале предварительной версии Azure или использовать при создании кластера в Azure CLI или Azure PowerShell.
> 
> Если у вас есть ресурсы в сети v1 и вам необходимо, чтобы эти ресурсы могли получать непосредственный доступ к HDInsight через виртуальную сеть, то сведения о том, как подключить виртуальную сеть v1 к виртуальной сети v2, можно найти в статье [Подключение классических виртуальных сетей к новым виртуальным сетям](../virtual-network/virtual-networks-arm-asm-s2s.md). После установления подключения можно создать кластер HDInsight в виртуальной сети v2.

4. Введите или выберите следующие значения:

	- **Имя**. Имя вашей виртуальной сети.
	- **Адресное пространство**. Выбирайте адресное пространство виртуальной сети таким образом, чтобы оно было достаточно большим для обеспечения адресов для всех узлов в кластере. В противном случае подготовка завершится с ошибкой. Для работы с этим руководством можно использовать значения по умолчанию. Нажмите кнопку **Сохранить**, чтобы сохранить изменения.
	- **Группа ресурсов** — выберите созданную ранее группу ресурсов.
	- **Подписка** — выберите подписку Azure, которую вы хотите использовать для этой виртуальной сети.
	- **Расположение** — расположение должно совпадать с расположением кластера HBase, который вы создадите.

5. Щелкните **Создать**.

По умолчанию виртуальная сеть использует внутренний сервер доменных имен (DNS), предоставленный Azure. Более продвинутые сетевые конфигурации с другими DNS-серверами также поддерживаются. Подробные указания см. в статье [Разрешение имен (DNS)](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).

**Добавление виртуальной машины DNS-сервера в виртуальную сеть (необязательно)**

DNS-сервер является необязательным, но в некоторых случаях он необходим. Процедура подробно описана в разделе [Настройка DNS между двумя виртуальными сетями Azure][hdinsight-hbase-replication-dns]. По сути, вам необходимо выполнить следующее:

1. добавить виртуальную машину Azure в виртуальную сеть;
2. задать статический IP-адрес для виртуальной машины;
3. добавить роль DNS-сервера для виртуальной машины;
4. назначить DNS-сервер в виртуальную сеть.

**Подготовка кластера HBase с помощью портала Azure**

> [AZURE.NOTE]Для получения информации о подготовке кластера HBase с помощью Azure PowerShell см. раздел [Подготовка кластера HBase с помощью Azure PowerShell](#powershell).


**Создание кластера HDInsight**

1. Войдите на [портал предварительной версии Azure](https://portal.azure.com).
2. Последовательно выберите **СОЗДАТЬ**, **Анализ данных**, а затем — **HDInsight**.

    ![Создание нового кластера на портале предварительной версии Azure](./media/hdinsight-provision-clusters/HDI.CreateCluster.1.png "Создание нового кластера на портале предварительной версии Azure")

3. Введите или выберите следующие значения:

  - **Имя кластера** — введите имя кластера. Если имя кластера доступно, рядом с ним появится зеленый флажок.
  - **Тип кластера** — выберите **HBase**.
  - **Операционная система кластера** — выберите **Windows Server 2012 R2 Datacenter**.
  - **Подписка** — выберите подписку Azure, которая будет использоваться для подготовки этого кластера.
  - **Группа ресурсов** — выберите созданную ранее группу ресурсов.
  - **Учетные данные** — настройте имя пользователя и пароль для пользователя Hadoop (пользователя HTTP). При включении удаленного рабочего стола для кластера необходимо настроить имя пользователя удаленного рабочего стола и пароль, а также указать дату окончания срока действия учетной записи. Щелкните **Выбрать** внизу страницы, чтобы сохранить изменения.
  - **Источник данных** — укажите существующую или создайте новую учетную запись службы хранения Azure, которая будет использована в качестве файловой системы по умолчанию для кластера. По умолчанию имя контейнера по умолчанию является именам кластера. Расположение учетной записи хранения также определяет расположение кластера.
  - **Ценовая категория для узлов** — для целей обучения или тестирования выберите узел региона 1, чтобы уменьшить стоимость.

  	- **Метод выбора** — выберите значение **Из всех подписок**, чтобы активировать поиск учетных записей хранения во всех своих подписках. Задайте для этого параметра значение **Ключ доступа**, если вы хотите ввести **имя хранилища** и **ключ доступа** существующей учетной записи хранения.
  	- **Выбрать учетную запись хранения/создать новую** — щелкните **Выбрать учетную запись хранения**, чтобы найти и выбрать существующую учетную запись хранения, которую нужно связать с кластером. Чтобы создать новую учетную запись хранения, выберите **Создать**. В появившееся поле введите имя учетной записи хранения. Если имя доступно, появится зеленый флажок.
    - **Выбрать контейнер по умолчанию** — позволяет ввести имя контейнера по умолчанию и использовать его для кластера. Вы можете ввести любое имя, однако мы рекомендуем использовать такое же имя, как у кластера, чтобы легко распознавать, какой контейнер используется для конкретного кластера.
  	- **Расположение** — географический регион, к которому будет относиться существующая или новая учетная запись хранения. Это расположение будет определять расположение кластера. Учетная запись хранения, используемая по умолчанию, должна находиться в том же центре обработки данных Azure, что и кластер.

  - **Ценовые категории для узлов** — задайте количество рабочих узлов, необходимых для кластера. Оценочная стоимость кластера отобразится в колонке.
	- **Необязательная конфигурация** — в обучающих целях вам необходимо настроить только **виртуальную сеть**. Выберите созданную вами виртуальную сеть. Убедитесь, что подсеть также выбрана.

4. Щелкните **Создать**.


Чтобы начать работу с новым HBase кластером, можно использовать процедуры, которые представлены в разделе [Приступая к работе с HBase с Hadoop в HDInsight](../hdinsight-hbase-get-started.md).

##Подключение к подготовленному в виртуальной сети кластеру HBase с помощью интерфейсов API удаленного вызова процедур (RPC) Java для HBase

1.	Подготовьте виртуальную машину IaaS в той же виртуальной сети Azure и той же подсети. Поэтому виртуальная машина и кластер HBase будут использовать один и тот же внутренний сервер DNS для разрешения имен узлов. Чтобы сделать это, необходимо выбрать параметр **Из коллекции** и выбрать виртуальную сеть вместо центра обработки данных. Инструкции см. в разделе [Создание виртуальной машины под управлением Windows Server](../virtual-machines-windows-tutorial.md). Достаточно стандартного образа Windows Server 2012 с виртуальной машиной небольшого размера.

2.	При использовании Java-приложения для удаленного подключения к HBase необходимо использовать полное доменное имя (FQDN). Чтобы определить это, вам необходимо получить DNS-суффикс кластера HBase. Для этого воспользуйтесь Curl, чтобы создать запрос к Ambari, или удаленным рабочим столом, чтобы подключиться к кластеру.

	* **Curl** — используйте следующую команду:

			curl -u <username>:<password> -k https://<clustername>.azurehdinsight.net/ambari/api/v1/clusters/<clustername>.azurehdinsight.net/services/hbase/components/hbrest

		В возвращенных данных JSON найдите запись «host\_name». В ней указаны полные доменные имена узлов в кластере. Например:

			...
			"host_name": "wordkernode0.<clustername>.b1.cloudapp.net
			...

		Часть доменного имени, начинающаяся с имени кластера, является DNS-суффиксом. Например, mycluster.b1.cloudapp.net.

	* **Azure PowerShell** — используйте следующий сценарий Azure PowerShell для регистрации функции **Get-ClusterDetail**, которая может быть использована для возвращения DNS-суффикса:

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
					$Url = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/services/yarn/components/resourcemanager"
					$Response = $webclient.DownloadString($Url)
					$JsonObject = $Response | ConvertFrom-Json
					$FQDN = $JsonObject.host_components[0].HostRoles.host_name
					$pos = $FQDN.IndexOf(".")
					$Suffix = $FQDN.Substring($pos + 1)
					Write-host $Suffix
				}
			}

		После запуска сценария Azure PowerShell используйте следующую команду, чтобы вернуть DNS-суффикс с помощью функции **Get-ClusterDetail**. При использовании этой команды укажите имя кластера HDInsight HBase, имя и пароль администратора.

			Get-ClusterDetail -ClusterDnsName <yourclustername> -PropertyName FQDNSuffix -Username <clusteradmin> -Password <clusteradminpassword>

		В результате будет возвращен DNS-суффикс. Например, **yourclustername.b4.internal.cloudapp.net**.

	> [AZURE.NOTE]Для подключения к кластеру HBase (вы будете подключены к головному узлу) и выполнения команды **ipconfig** в командной строке для получения DNS-суффикса также можно использовать удаленный рабочий стол. Для получения указаний по включению протокола удаленного рабочего стола и подключению к кластеру с его помощью см. раздел [Управление кластерами Hadoop в HDInsight с использованием портала Azure][hdinsight-admin-portal].
	>
	> ![hdinsight.hbase.dns.surffix][img-dns-surffix]


<!--
3.	Change the primary DNS suffix configuration of the virtual machine. This enables the virtual machine to automatically resolve the host name of the HBase cluster without explicit specification of the suffix. For example, the *workernode0* host name will be correctly resolved to workernode0 of the HBase cluster.

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

Чтобы использовать эту информацию в Java-приложении, для создания приложения можно придерживаться шагов, представленных в разделе [Использование Maven для создания Java-приложений, использующих HBase с HDInsight (Hadoop)](hdinsight-hbase-build-java-maven.md). Чтобы приложение подключилось к удаленному серверу HBase, измените файл **hbase-site.xml** в этом примере, чтобы использовать полное доменное имя для ZooKeeper. Например:

	<property>
    	<name>hbase.zookeeper.quorum</name>
    	<value>zookeeper0.<dns suffix>,zookeeper1.<dns suffix>,zookeeper2.<dns suffix></value>
	</property>

> [AZURE.NOTE]Для получения дополнительной информации о разрешении имен в виртуальных сетях Azure, а также об использовании своего​ собственного DNS-сервера см. раздел [Разрешение имен (DNS)](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).

##Подготовка кластера HBase с помощью Azure PowerShell

**Подготовка кластера HBase с помощью Azure PowerShell**

1. Откройте интегрированную среду сценариев Azure PowerShell.
2. Скопируйте следующую команду и вставьте ее в окно запроса:

		$hbaseClusterName = "<HBaseClusterName>"
		$hadoopUserName = "<HBaseClusterUsername>"
		$hadoopUserPassword = "<HBaseClusterUserPassword>"
		$location = "<HBaseClusterLocation>"  #i.e. "West US"
		$clusterSize = <HBaseClusterSize>  
		$resourceGroup = "<AzureResourceGroupName>"
		$vnetID = "<AzureVirtualNetworkID>"
		$subNetName = "<AzureVirtualNetworkSubNetName>"
		$storageAccountName = "<AzureStorageAccountName>" # Do not use the full name here
		$storageAccountKey = "<AzureStorageAccountKey>"
		$storageContainerName = "<AzureBlobStorageContainer>"

		$password = ConvertTo-SecureString $hadoopUserPassword -AsPlainText -Force
		$creds = New-Object System.Management.Automation.PSCredential ($hadoopUserName, $password)

		New-AzureHDInsightCluster -ResourceGroupName $resourceGroup `
		                          -ClusterName $hbaseClusterName `
				                    	-ClusterType HBase `
				                    	-Location $location `
				                    	-ClusterSizeInNodes $clusterSize `
		                          -HttpCredential $creds `
				                    	-VirtualNetworkId $vnetID `
				                    	-SubnetName $subNetName `
				                    	-DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" `
				                    	-DefaultStorageAccountKey $storageAccountKey `
		                          -DefaultStorageContainer $storageContainerName


3. Щелкните **Запустить сценарий** или нажмите клавишу **F5**.
4. Вы можете либо проверить кластер через портал Azure, либо выполнить в нижней панели следующий командлет Azure PowerShell:

	Get-AzureHDInsightCluster

##Дальнейшие действия

В этом учебнике вы узнали, как подготовить к работе кластер HBase. Дополнительные сведения см. на следующих ресурсах:

- [Приступая к работе с HDInsight](../hdinsight-get-started.md)
- [Настройка репликации HBase в HDInsight](hdinsight-hbase-geo-replication.md)
- [Подготовка кластеров Hadoop в HDInsight](hdinsight-provision-clusters.md)
- [Приступая к работе с HBase с Hadoop в HDInsight](../hdinsight-hbase-get-started.md)
- [Анализ мнений пользователей Twitter с использованием HBase в HDInsight](../hdinsight-hbase-twitter-sentiment.md)
- [Обзор виртуальной сети][vnet-overview]


[1]: http://azure.microsoft.com/services/virtual-network/
[2]: http://technet.microsoft.com/library/ee176961.aspx
[3]: http://technet.microsoft.com/library/hh847889.aspx

[hbase-get-started]: ../hdinsight-hbase-get-started.md
[hbase-twitter-sentiment]: ../hdinsight-hbase-twitter-sentiment.md
[vnet-overview]: ../virtual-network/virtual-networks-overview.md
[vm-create]: ../virtual-machines-windows-tutorial.md

[azure-portal]: https://portal.azure.com
[azure-create-storageaccount]: ../storage-create-storage-account.md
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-admin-portal]: hdinsight-administer-use-management-portal.md#rdp

[hdinsight-powershell-reference]: https://msdn.microsoft.com/library/dn858087.aspx


[twitter-streaming-api]: https://dev.twitter.com/docs/streaming-apis
[twitter-statuses-filter]: https://dev.twitter.com/docs/api/1.1/post/statuses/filter


[powershell-install]: ../install-configure-powershell.md


[hdinsight-customize-cluster]: hdinsight-hadoop-customize-cluster.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-get-started]: ../hdinsight-get-started.md
[hdinsight-storage-powershell]: ../hdinsight-use-blob-storage.md#powershell
[hdinsight-analyze-flight-delay-data]: hdinsight-analyze-flight-delay-data.md
[hdinsight-storage]: ../hdinsight-use-blob-storage.md
[hdinsight-use-sqoop]: hdinsight-use-sqoop.md
[hdinsight-power-query]: hdinsight-connect-excel-power-query.md
[hdinsight-hive-odbc]: hdinsight-connect-excel-hive-ODBC-driver.md
[hdinsight-hbase-replication-dns]: hdinsight-hbase-geo-replication-configure-DNS.md

[img-dns-surffix]: ./media/hdinsight-hbase-provision-vnet/DNSSuffix.png
[img-primary-dns-suffix]: ./media/hdinsight-hbase-provision-vnet/PrimaryDNSSuffix.png
[img-provision-cluster-page1]: ./media/hdinsight-hbase-provision-vnet/hbasewizard1.png "Подготовка сведений для нового кластера HBase"
[img-provision-cluster-page5]: ./media/hdinsight-hbase-provision-vnet/hbasewizard5.png "Использование действия сценария для настройки кластера HBase"

[azure-preview-portal]: https://portal.azure.com

<!---HONumber=Oct15_HO3-->
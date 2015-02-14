<properties 
	pageTitle="Подготовка кластеров HBase в виртуальной сети Azure | Azure" 
	description="Узнайте, как создать кластеры HDInsight в виртуальной сети Azure." 
	services="hdinsight" 
	documentationCenter="" 
	authors="mumian" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/19/2014" 
	ms.author="jgao"/>

# Подготовка кластеров HBase в виртуальной сети Azure

Узнайте, как создать кластеры HDInsight в [виртуальной сети Azure][1]. 

Благодаря интеграции виртуальной сети кластеры HBase могут быть развернуты в той же виртуальной сети, что и приложения. Это позволяет приложениям взаимодействовать с HBase непосредственно. К преимуществам относятся:

- прямое подключение веб-приложения к узлам кластера HBase, который обеспечивает обмен данными с помощью API-интерфейсов HBase Java RPC;
- повышение производительности без необходимости организации пропуска трафика через множество шлюзов и балансировки нагрузки; 
- обработка конфиденциальной информации более безопасным способом, без необходимости организации общедоступной конечной точки.


##Содержание

- [Необходимые условия](#prerequisites)
- [Подготовка кластеров HBase в виртуальной сети Azure](#hbaseprovision)
- [Подключение к подготовленному в виртуальной сети кластеру HBase с помощью интерфейсов API Java RPC для HBase](#connect)
- [Подготовка кластера HBase с помощью PowerShell](#powershell)
- [Дальнейшие действия](#nextsteps)

##<a id="prerequisites"></a>Предварительные требования
Перед началом работы с этим учебником необходимо иметь следующее:

- **Подписка Azure**. Azure - это платформа на основе подписок. Дополнительные сведения о получении подписки см. в разделе [Варианты приобретения][azure-purchase-options], [Предложения для участников][azure-member-offers] или [Бесплатное пробное использование][azure-free-trial].

- **Рабочая станция, на которой установлена и настроена среда Azure PowerShell**. Указания см. в разделе [Установка и настройка Azure PowerShell][powershell-install]. Для выполнения скриптов PowerShell необходимо запустить Azure PowerShell с правами администратора и задать политику выполнения *RemoteSigned*. См. раздел [Использование командлета Set-ExecutionPolicy][2].

	Перед выполнением скриптов PowerShell убедитесь, что вы подключены к подписке Azure, с помощью следующего командлета:

		Add-AzureAccount

	При наличии нескольких подписок Azure используется следующий командлет для установки текущей подписки:

		Select-AzureSubscription <AzureSubscriptionName>


##<a id="hbaseprovision"></a>Подготовка кластера HBase в виртуальной сети Azure. 

**Создание виртуальной сети с помощью портала управления::**

1. Войдите на [портал управления Azure][azure-portal].
2. В нижнем левом углу щелкните **СОЗДАТЬ**, затем последовательно щелкните **СЕТЕВЫЕ СЛУЖБЫ**, **ВИРТУАЛЬНАЯ СЕТЬ** и **БЫСТРОЕ СОЗДАНИЕ**.
3. Введите или выберите следующие значения:

	- **Имя**. Название виртуальной сети.
	- **Адресное пространство**. Выберите адресное пространство виртуальной сети таким образом, чтобы оно было достаточно большим для обеспечения адресов для всех узлов в кластере. В противном случае подготовка завершится с ошибкой. Для прохождения этого учебника можно выбрать любой из трех вариантов. 
	- **Максимальное количество виртуальных машин**. Выберите максимальное количество виртуальных машин. Это значение определяет количество возможных узлов (ВМ), которые могут быть созданы в адресном пространстве. Для прохождения этого учебника достаточно **4096 [CIDR: /20]**. 
	- **Расположение**. Расположение должно совпадать с расположением кластера HBase, который вы будете создавать.
	- **Сервер DNS**. В этой статье используется внутренний сервер DNS, предоставленный Azure, поэтому вы можете выбрать **Нет**. Также поддерживаются более продвинутые сетевые конфигурации с другими серверами DNS. Подробное руководство см. в разделе [Разрешение имен (DNS)](http://msdn.microsoft.com/library/azure/jj156088.aspx).
4. Щелкните **СОЗДАТЬ ВИРТУАЛЬНУЮ СЕТЬ**. В списке появится имя новой виртуальной сети. Подождите, пока в столбце "Состояние" не появится значение **Создано**.
5. В основной области щелкните только что созданную виртуальную сеть.
6. В верхней части страницы щелкните **ПАНЕЛЬ МОНИТОРИНГА**.
7. В разделе **Сводка** узнайте и запишите **ИДЕНТИФИКАТОР ВИРТУАЛЬНОЙ СЕТИ**. Он вам понадобится в процессе подготовки кластера HBase.
8. В верхней части страницы щелкните **НАСТРОИТЬ**.
9. В нижней части страницы указано имя подсети по умолчанию **Subnet-1**. При желании вы можете переименовать подсеть или добавить новую подсеть для кластера HBase. Запишите имя подсети, оно вам понадобится в процессе подготовки кластера.
10. Проверьте значение **CIDR (ЧИСЛО АДРЕСОВ)** для подсети, которая будет использоваться в кластере. Количество адресов должен быть больше, чем количество рабочих узлов плюс семь (шлюзов: 2, головной узел: 2, узел Zookeeper: 3). Например, если вам нужен кластер HBase на 10 узлов, количество адресов для подсети должно быть больше 17 (10 + 7). В противном случае подготовка завершится с ошибкой.

	> [AZURE.NOTE] Настоятельно рекомендуется назначать одну подсеть для одного кластера. 

11. Нажмите кнопку **Сохранить** в нижней части страницы в том случае, если вы изменяли значения для подсети.



> [AZURE.NOTE] Кластеры HDInsight используют хранилища BLOB-объектов Azure для хранения данных. Дополнительные сведения см. в разделе [Использование хранилища BLOB-объектов Azure с Hadoop в HDInsight][hdinsight-storage]. Вам понадобится учетная запись хранения и контейнер для хранения BLOB-объектов. Расположение учетной записи хранения должно соответствовать расположению виртуальной сети и кластера.

**Создание учетной записи хранения Azure и контейнера хранилища BLOB-объектов:**

1. Войдите на [портал управления Azure][azure-portal].
2. Щелкните **СОЗДАТЬ** в левом нижнем углу, выберите **СЛУЖБЫ ДАННЫХ**, **ХРАНИЛИЩЕ**, а затем щелкните **БЫСТРО СОЗДАТЬ**.

3. Введите или выберите следующие значения:

	- **URL-АДРЕС**. Имя учетной записи хранения.
	- **РАСПОЛОЖЕНИЕ**: Расположение учетной записи хранения. Убедитесь, что оно совпадает с расположением виртуальной сети. Территориальные группы не поддерживаются.
	- **РЕПЛИКАЦИЯ**. В рамках тестирования используйте **Локально избыточная** для снижения затрат.

4. Щелкните **СОЗДАТЬ УЧЕТНУЮ ЗАПИСЬ ХРАНЕНИЯ**.  Вы увидите новую учетную запись хранения в списке хранилищ. 
5. Дождитесь, пока **СОСТОЯНИЕ** новой учетной записи хранения не изменится на **В сети**.
6. Щелкните новую учетную запись хранения в списке, чтобы выбрать ее.
7. Щелкните **УПРАВЛЕНИЕ КЛЮЧАМИ ДОСТУПА** в нижней части страницы.
8. Запишите значение параметров **ИМЯ УЧЕТНОЙ ЗАПИСИ ХРАНЕНИЯ** и **ПЕРВИЧНЫЙ КЛЮЧ ДОСТУПА** (или **ВТОРИЧНЫЙ КЛЮЧ ДОСТУПА**.  Будет работать любой из ключей).  Они потребуются позже в данном руководстве.
9. В верхней части страницы щелкните **КОНТЕЙНЕР**.
10. В нижней части страницы щелкните **ДОБАВИТЬ**.
11. Введите имя контейнера.  Этот контейнер будет использоваться в качестве контейнера по умолчанию для кластера HBase. По умолчанию имя контейнера по умолчанию соответствует имени кластера. Для поля **ДОСТУП** оставьте значение **Частный**.  
12. Чтобы создать контейнер, поставьте галочку.

**Подготовка кластера HBase с помощью портала Azure:**

> [AZURE.NOTE] Для получения информации о подготовке кластера HBase с помощью PowerShell см. раздел [Подготовка кластера HBase с помощью PowerShell](#powershell).

1. Войдите на [портал управления Azure][azure-portal].

2. Щелкните **СОЗДАТЬ** в левом нижнем углу, выберите **СЛУЖБЫ ДАННЫХ**, **HDINSIGHT**, а затем щелкните **НАСТРАИВАЕМОЕ СОЗДАНИЕ**.

3. Введите "ИМЯ КЛАСТЕРА", укажите для параметра "ТИП КЛАСТЕРА" значение HBase и выберите версию HDINSIGHT.

	![Provide details for the HBase cluster][img-provision-cluster-page1]

	Щелкните стрелку вправо.

4. Введите или выберите следующие значения:

	- **УЗЛЫ ДАННЫХ**. Введите число узлов данных для кластера HBase. 
	- **РЕГИОН/ВИРТУАЛЬНАЯ СЕТЬ**. Выберите созданную вами виртуальную сеть Azure.
	- **ПОДСЕТИ ВИРТУАЛЬНОЙ сети**. Выберите подсеть. Имя по умолчанию - **Subnet-1**.

	Щелкните стрелку вправо.

5. Введите **ИМЯ ПОЛЬЗОВАТЕЛЯ** и **ПАРОЛЬ** пользователя Hadoop для этого кластера и щелкните правой кнопкой мыши.
6. Укажите, следует ли по умолчанию использовать для кластера новую или существующую учетную запись хранения, а затем щелкните правой кнопкой мыши.

7. На странице **Действия скриптов** щелкните **Добавить действие скрипта** для указания сведений о пользовательском скрипте, который необходимо выполнить для настройки кластера во время его создания. Например, действие скрипта можно использовать для настройки кластера на установку <a href="http://spark.apache.org/docs/latest/index.html" target="_blank">Apache Spark</a>. Дополнительные сведения см. в разделе [Настройка кластеров HDInsight с помощью действия скрипта][hdinsight-customize-cluster]. 
	
	![Configure Script Action to customize an HDInsight HBase cluster][img-provision-cluster-page5]

	<table border='1'>
		<tr><th>Свойство</th><th>Значение</th></tr>
		<tr><td>Имя</td>
			<td>Укажите имя для действия скрипта.</td></tr>
		<tr><td>URI скрипта</td>
			<td>Укажите URI для скрипта, вызываемого для настройки кластера.</td></tr>
		<tr><td>Тип узла</td>
			<td>Указывает узлы, на которых выполняется скрипт настройки. Вы можете <b>Все узлы</b>, <b>Только головные узлы</b> или <b>Только рабочие узлы</b>.
		<tr><td>Параметры</td>
			<td>Укажите необходимые для скрипта параметры.</td></tr>
	</table>

	Можно добавить несколько действий скрипта для установки нескольких компонентов в кластере. После добавления скриптов щелкните значок с галочкой, чтобы начать подготовку кластера.


Чтобы начать работу с новым кластером HBase, можно использовать процедуры, которые представлены в разделе [Приступая к работе с HBase с Hadoop в HDInsight][hbase-get-started].

##<a id="connect"></a>Подключайтесь к подготовленному в виртуальной сети кластеру HBase с помощью интерфейсов API Java RPC для HBase.

1.	Подготовка виртуальной машины IaaS в той же виртуальной сети Azure и той же подсети. Поэтому виртуальная машина и кластер HBase будут использовать один и тот же внутренний сервер DNS для разрешения имен узлов. Чтобы сделать это, необходимо вызвать выбор вариантов из коллекции и выбрать виртуальную сеть вместо центра обработки данных. Инструкции см. в разделе [Создание виртуальной машины под управлением Windows Server][vm-create]. Достаточно стандартного образа Windows Server 2012 с виртуальной машиной небольшого размера.
	
2.	При использовании Java-приложения для удаленного подключения к HBase необходимо использовать полное доменное имя (FQDN). Чтобы это определить, мы должны получить DNS-суффикс для подключения HBase кластера. Для этого используется программа Curl, предназначенная для формирования запроса Ambari, или удаленный рабочий стол для подключения к кластеру.

	* **Curl** - используйте следующую команду:

			curl -u <username>:<password> -k https://<clustername>.azurehdinsight.net/ambari/api/v1/clusters/<clustername>.azurehdinsight.net/services/hbase/components/hbrest

		В возвращенных данных JSON найдите запись "host_name" (имя узла). Она будет содержать полное доменное имя (FQDN) для узлов кластера. Например:

			...
			"host_name": "wordkernode0.<clustername>.b1.cloudapp.net
			...

		Часть доменного имени, начинающаяся с имени кластера, является DNS-суффиксом. Например, mycluster.b1.cloudapp.net.

	* **PowerShell** - используйте следующий скрипт PowerShell для регистрации функции **Get-ClusterDetail**, которая может быть использована для возвращения DNS-суффикса.

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

		После запуска скрипта PowerShell используйте следующую команду, чтобы вернуть DNS-суффикс с помощью функции Get-ClusterDetail. При использовании этой команды укажите имя кластера HDInsight HBase, имя и пароль администратора.

			Get-ClusterDetail -ClusterDnsName <yourclustername> -PropertyName FQDNSuffix -Username <clusteradmin> -Password <clusteradminpassword>

		В результате будет возвращен DNS-суффикс. Например, **yourclustername.b4.internal.cloudapp.net**.

	> [AZURE.NOTE] Для подключения к кластеру HBase (вы будете подключены к головному узлу) и запуска команды **ipconfig** из командной строки с целью получения DNS-суффикса также можно использовать удаленный рабочий стол. Для получения инструкций по включению удаленного рабочего стола и подключению к кластеру см. раздел [Управление кластерами Hadoop в HDInsight с помощью портала управления Azure][hdinsight-admin-portal].
	>
	> ![hdinsight.hbase.dns.surffix][img-dns-surffix]


<!-- 
3.	Измените настройку "Основной DNS-суффикс" виртуальной машины. Это позволяет виртуальной машине автоматически разрешить имя узла кластера HBase без явного задания суффикса. Например, имя узла *workernode0* будет корректно разрешено в workernode0 кластера HBase. 

	Для изменения настройки:

	1. подключитесь с помощью удаленного рабочего стола к виртуальной машине. 
	2. Откройте **Редактор локальной групповой политики**. Он запускается командой gpedit.msc.
	3. Разверните узлы **Конфигурация компьютера**, **Административные шаблоны** и **Сеть**, а затем щелкните **DNS-клиент**. 
	- Установите для параметра **Основной DNS-суффикс** значение, полученное на втором шаге: 

		![hdinsight.hbase.primary.dns.suffix][img-primary-dns-suffix]
	4. Нажмите кнопку **ОК**. 
	5. Перезапустите виртуальную машину.
-->

Для обеспечения обмена данными между виртуальной машиной и кластером HBase запустите на виртуальной машине следующую команду `ping headnode0.<dns suffix>`. Например, ping headnode0.mycluster.b1.cloudapp.net

Чтобы использовать эту информацию в Java-приложении, при создании приложения можно придерживаться шагов, представленных в разделе [Использование Maven для создания Java-приложений, использующих HBase с HDInsight (Hadoop)](azure.microsoft.com/ru-ru/documentation/articles/hdinsight-hbase-build-java-maven/). Для подключения приложения к удаленному серверу HBase измените в этом примере файл **hbase-site.xml**, чтобы использовать полное доменное имя для ZooKeeper. Например:

	<property>
    	<name>hbase.zookeeper.quorum</name>
    	<value>zookeeper0.<dns suffix>,zookeeper1.<dns suffix>,zookeeper2.<dns suffix></value>
	</property>

> [AZURE.NOTE] Для получения дополнительной информации о разрешении имен в виртуальных сетях Azure, а также об использовании своего​собственного сервера DNS см. раздел [Разрешение имен (DNS)](http://msdn.microsoft.com/ru-ru/library/azure/jj156088.aspx).

##<a id="powershell"></a>Подготовка кластера HBase с помощью Azure PowerShell

**Порядок подготовки кластера HBase с помощью Azure PowerShell**

1. Откройте интегрированную среду сценариев PowerShell.
2. Скопируйте следующую команду и вставьте ее в окно запроса.

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


3. Щелкните **Запустить скрипт** или нажмите клавишу **F5**.
4. Вы можете либо проверить кластер через портал управления, либо запустить в нижней панели следующий командлет PowerShell:

	Get-AzureHDInsightCluster 

##<a id="nextsteps"></a>Дальнейшие действия

В этом учебнике мы изучили способ подготовки кластера HBase. Дополнительные сведения см. на следующих ресурсах:

- [Приступая к работе с HDInsight][hdinsight-get-started]
- [Подготовка кластеров Hadoop в HDInsight][hdinsight-provision] 
- [Приступая к работе с HBase с Hadoop в HDInsight][hbase-get-started]
- [Анализ мнений пользователей Twitter с использованием HBase в HDInsight][hbase-twitter-sentiment]
- [Обзор виртуальной сети][vnet-overview].


[1]: http://azure.microsoft.com/ru-ru/services/virtual-network/
[2]: http://technet.microsoft.com/ru-ru/library/ee176961.aspx
[3]: http://technet.microsoft.com/ru-ru/library/hh847889.aspx

[hbase-get-started]: ../hdinsight-hbase-get-started/
[hbase-twitter-sentiment]: ../hdinsight-hbase-twitter-sentiment/
[vnet-overview]: http://msdn.microsoft.com/library/azure/jj156007.aspx
[vm-create]: ../virtual-machines-windows-tutorial/

[azure-portal]: https://manage.windowsazure.com
[azure-create-storageaccount]: ../storage-create-storage-account/ 
[azure-purchase-options]: http://azure.microsoft.com/ru-ru/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/ru-ru/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/ru-ru/pricing/free-trial/

[hdinsight-admin-powershell]: ../hdinsight-administer-use-powershell/
[hdinsight-admin-portal]: ../hdinsight-administer-use-management-portal/#rdp

[hdinsight-powershell-reference]: http://msdn.microsoft.com/ru-ru/library/windowsazure/dn479228.aspx


[twitter-streaming-api]: https://dev.twitter.com/docs/streaming-apis
[twitter-statuses-filter]: https://dev.twitter.com/docs/api/1.1/post/statuses/filter


[powershell-install]: ../install-configure-powershell


[hdinsight-customize-cluster]: ../hdinsight-hadoop-customize-cluster/
[hdinsight-provision]: ../hdinsight-provision-clusters/
[hdinsight-get-started]: ../hdinsight-get-started/
[hdinsight-storage-powershell]: ../hdinsight-use-blob-storage/#powershell
[hdinsight-analyze-flight-delay-data]: ../hdinsight-analyze-flight-delay-data/
[hdinsight-storage]: ../hdinsight-use-blob-storage/
[hdinsight-use-sqoop]: ../hdinsight-use-sqoop/
[hdinsight-power-query]: ../hdinsight-connect-excel-power-query/
[hdinsight-hive-odbc]: ../hdinsight-connect-excel-hive-ODBC-driver/

[img-dns-surffix]: ./media/hdinsight-hbase-provision-vnet/DNSSuffix.png
[img-primary-dns-suffix]: ./media/hdinsight-hbase-provision-vnet/PrimaryDNSSuffix.png
[img-provision-cluster-page1]: ./media/hdinsight-hbase-provision-vnet/hbasewizard1.png "Provision details for the new HBase cluster"
[img-provision-cluster-page5]: ./media/hdinsight-hbase-provision-vnet/hbasewizard5.png "Use Script Action to customize an HBase cluster"<!--HONumber=42-->

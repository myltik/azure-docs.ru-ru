<properties 
	pageTitle="Настройка прослушивателя внутренней подсистемы балансировки нагрузки для групп доступности AlwaysOn | Microsoft Azure"
	description="В этом руководстве используются ресурсы, созданные в классической модели развертывания, а также создается прослушиватель группы доступности AlwaysOn в Azure с помощью внутренней подсистемы балансировки нагрузки (ILB)."
	services="virtual-machines"
	documentationCenter="na"
	authors="rothja"
	manager="jeffreyg"
	editor="monicar" 
	tags="azure-service-management"/>
<tags 
	ms.service="virtual-machines"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.workload="infrastructure-services"
	ms.date="11/06/2015"
	ms.author="jroth" />

# Настройка прослушивателя внутренней подсистемы балансировки нагрузки для группы доступности AlwaysOn в Azure

> [AZURE.SELECTOR]
- [Internal Listener](virtual-machines-sql-server-configure-ilb-alwayson-availability-group-listener.md)
- [External Listener](virtual-machines-sql-server-configure-public-alwayson-availability-group-listener.md)

## Обзор

В этом разделе показано, как настроить прослушиватель для группы доступности AlwaysOn с помощью **внутренней подсистемы балансировки нагрузки (ILB)**.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Модель диспетчера ресурсов.
 

В группе доступности могут быть реплики, доступные только локально или только в Azure. В гибридных конфигурациях возможны оба способа доступа одновременно. Реплики в Azure могут находиться в одном или нескольких регионах (при использовании нескольких виртуальных сетей). В приведенных ниже указаниях предполагается, что вы уже [настроили группу доступности](virtual-machines-sql-server-alwayson-availability-groups-gui.md), но еще не настроили прослушиватель.

## Рекомендации и ограничения для внутренних прослушивателей
Обратите внимание на следующие рекомендации к прослушивателю группы доступности в Azure, использующему внутренний балансировщик нагрузки (ILB).

- Прослушиватель группы доступности поддерживается в Windows Server 2008 R2, Windows Server 2012 и Windows Server 2012 R2.

- Для одной облачной службы поддерживается только один внутренний прослушиватель группы доступности, так как он настраивается только на использование ILB, а в облачной службе только один ILB. Однако можно создать несколько внешних прослушивателей. Дополнительные сведения см. в статье [Настройка внешнего прослушивателя для групп доступности AlwaysOn в Azure](virtual-machines-sql-server-configure-public-alwayson-availability-group-listener.md).

- Создание внутреннего прослушивателя в той же облачной службе, что и внешний прослушиватель, использующий общедоступный виртуальный IP-адрес облачной службы, не поддерживается.

## Определение доступности прослушивателя

[AZURE.INCLUDE [ag-listener-accessibility](../../includes/virtual-machines-ag-listener-determine-accessibility.md)]

В этой статье рассматривается создание прослушивателя, использующего **внутренний балансировщик нагрузки (ILB)**. Если вам требуется общедоступный (внешний) прослушиватель, см. другую версию этой статьи, где приведены пошаговые инструкции по настройке [внешнего прослушивателя](virtual-machines-sql-server-configure-public-alwayson-availability-group-listener.md).

## Создание конечных точек балансировки нагрузки в ВМ со службой Direct Server Return

Для создания конечных точек с ILB сначала необходимо создать внутреннюю подсистему балансировки нагрузки. Это делается в приведенном ниже сценарии.

[AZURE.INCLUDE [load-balanced-endpoints](../../includes/virtual-machines-ag-listener-load-balanced-endpoints.md)]

1. Для **балансировщика нагрузки** вам потребуется назначить статический IP-адрес. Во-первых, изучите текущую конфигурацию виртуальной сети, выполнив следующую команду:

		(Get-AzureVNetConfig).XMLConfiguration

1. Обратите внимание на имя **Subnet** для подсети, содержащей виртуальные машины с репликами. Его значение будет присвоено параметру **$SubnetName** в сценарии.

1. Кроме того, обратите внимание на имя **VirtualNetworkSite** и начальный префикс подсети **AddressPrefix**, которая содержит виртуальные машины с репликами. Определите доступные IP-адреса. Для этого передайте оба значения в команду **Test-AzureStaticVNetIP** и изучите значение **AvailableAddresses**. Например, если виртуальная сеть называется *MyVNet*, а диапазон адресов подсети начинается с *172.16.0.128*, следующая команда выведет список доступных адресов:

		(Test-AzureStaticVNetIP -VNetName "MyVNet"-IPAddress 172.16.0.128).AvailableAddresses

1. Выберите один из доступных адресов и используйте его в качестве значения параметра **$ILBStaticIP** в приведенном ниже сценарии.

3. Скопируйте приведенный ниже сценарий PowerShell в текстовый редактор и задайте значения переменных в соответствии с вашими условиями (обратите внимание, что для некоторых параметров указаны значения по умолчанию). Вы не сможете добавить подсистему балансировки нагрузки к существующим развернутым службам, использующим территориальные группы. Дополнительные сведения о требованиях ILB см. в статье [Внутренняя подсистема балансировки нагрузки](../load-balancer/load-balancer-internal-overview.md). Если ваша группа доступности охватывает несколько регионов Azure, приведенный сценарий нужно будет запустить в каждом центре данных для всех облачных служб и узлов, которые находятся в этом центре.

		# Define variables
		$ServiceName = "<MyCloudService>" # the name of the cloud service that contains the availability group nodes
		$AGNodes = "<VM1>","<VM2>","<VM3>" # all availability group nodes containing replicas in the same cloud service, separated by commas
		$SubnetName = "<MySubnetName>" # subnet name that the replicas use in the VNet
		$ILBStaticIP = "<MyILBStaticIPAddress>" # static IP address for the ILB in the subnet
		$ILBName = "AGListenerLB" # customize the ILB name or use this default value
		
		# Create the ILB
		Add-AzureInternalLoadBalancer -InternalLoadBalancerName $ILBName -SubnetName $SubnetName -ServiceName $ServiceName -StaticVNetIPAddress $ILBStaticIP
		
		# Configure a load balanced endpoint for each node in $AGNodes using ILB
		ForEach ($node in $AGNodes)
		{
			Get-AzureVM -ServiceName $ServiceName -Name $node | Add-AzureEndpoint -Name "ListenerEndpoint" -LBSetName "ListenerEndpointLB" -Protocol tcp -LocalPort 1433 -PublicPort 1433 -ProbePort 59999 -ProbeProtocol tcp -ProbeIntervalInSeconds 10 -InternalLoadBalancerName $ILBName -DirectServerReturn $true | Update-AzureVM 
		}

1. Присвоив значения переменным, скопируйте сценарий из текстового редактора в текущий сеанс Azure PowerShell и выполните его. Если в командной строке отображается >>, нажмите клавишу ВВОД еще раз, чтобы начать выполнение сценария. Примечание.

>[AZURE.NOTE]В данный момент портал управления Azure не поддерживает внутреннюю балансировку нагрузки, поэтому система внутренней балансировки нагрузки и конечные точки не будут видны пользователю портала. Но команда **Get-AzureEndpoint** вернет внутренний IP-адрес, если на портале запущен балансировщик нагрузки. В противном случае будет возвращено значение null.

## Проверка наличия пакета KB2854082

[AZURE.INCLUDE [kb2854082](../../includes/virtual-machines-ag-listener-kb2854082.md)]

## Открытие портов брандмауэра в узлах групп доступности.

[AZURE.INCLUDE [брандмауэр](../../includes/virtual-machines-ag-listener-open-firewall.md)]

## Создание прослушивателя группы доступности

[AZURE.INCLUDE [брандмауэр](../../includes/virtual-machines-ag-listener-create-listener.md)]

1. Чтобы использовать внутреннюю балансировку нагрузки, вам потребуется указать IP-адрес ранее созданной внутренней подсистемы балансировки нагрузки. Этот IP-адрес можно получить при помощи PowerShell следующим образом.

		# Define variables
		$ServiceName="<MyServiceName>" # the name of the cloud service that contains the AG nodes
		(Get-AzureInternalLoadBalancer -ServiceName $ServiceName).IPAddress

1. Войдите на одну из виртуальных машин и скопируйте приведенный ниже сценарий PowerShell в текстовый редактор. Затем присвойте переменным записанные ранее значения.

		# Define variables
		$ClusterNetworkName = "<MyClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
		$IPResourceName = "<IPResourceName>" # the IP Address resource name 
		$ILBIP = “<X.X.X.X>” # the IP Address of the Internal Load Balancer (ILB)
		
		Import-Module FailoverClusters
		
		# If you are using Windows Server 2012 or higher, use the Get-Cluster Resource command. If you are using Windows Server 2008 R2, use the cluster res command. Both commands are commented out. Choose the one applicable to your environment and remove the # at the beginning of the line to convert the comment to an executable line of code. 
		
		# Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"="59999";"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
		# cluster res $IPResourceName /priv enabledhcp=0 address=$ILBIP probeport=59999  subnetmask=255.255.255.255

1. Присвоив значения переменным, откройте окно Windows PowerShell с повышенными правами. Затем скопируйте сценарий из текстового редактора, вставьте его в текущий сеанс Azure PowerShell и выполните сценарий. Если в командной строке отображается >>, нажмите клавишу ВВОД еще раз, чтобы начать выполнение сценария.

2. Повторите эти действия на каждой виртуальной машине. Этот сценарий настраивает ресурс IP-адреса путем установки IP-адреса облачной службы и прочих параметров, таких как порт зонда. После подключения ресурс IP-адреса сможет отвечать на запросы, отправляемые на порт зонда из созданной ранее конечной точки балансировки нагрузки.

## Подключение прослушивателя

[AZURE.INCLUDE [Bring-Listener-Online](../../includes/virtual-machines-ag-listener-bring-online.md)]

## Дальнейшие действия

[AZURE.INCLUDE [Follow-up](../../includes/virtual-machines-ag-listener-follow-up.md)]

## Проверьте прослушиватель группы доступности (в пределах одной виртуальной сети)

[AZURE.INCLUDE [Test-Listener-Within-VNET](../../includes/virtual-machines-ag-listener-test.md)]

## Дальнейшие действия

[AZURE.INCLUDE [Listener-Next-Steps](../../includes/virtual-machines-ag-listener-next-steps.md)]

<!---HONumber=Nov15_HO4-->
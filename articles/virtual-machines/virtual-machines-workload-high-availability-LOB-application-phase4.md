<properties 
	pageTitle="Бизнес-приложение, этап 4 | Microsoft Azure" 
	description="На четвертом этапе развертывания бизнес-приложения в Azure создайте веб-серверы и нагрузите бизнес-приложение." 
	documentationCenter=""
	services="virtual-machines" 
	authors="JoeDavies-MSFT" 
	manager="timlt" 
	editor=""
	tags="azure-resource-manager"/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="Windows" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/20/2015" 
	ms.author="josephd"/>

# Четвертый этап развертывания бизнес-приложений: настройка веб-серверов

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]Классическая модель развертывания.
 

На этом этапе развертывания высокодоступного бизнес-приложения на базе служб инфраструктуры Azure вы создадите веб-серверы, на которых будет обрабатываться нагрузка вашего бизнес-приложения.

Этот этап необходимо выполнить, прежде чем переходить к [этапу 5](virtual-machines-workload-high-availability-LOB-application-phase5.md). Все этапы перечислены в статье [Развертывание высокодоступных бизнес-приложений в Azure](virtual-machines-workload-high-availability-LOB-application-overview.md).

## Создание виртуальных машин с веб-серверами в среде Azure

Существует две виртуальные машины с веб-серверами, на которых можно развертывать приложения ASP.NET или более старые программы, для размещения которых можно использовать Internet Information Services 8 (IIS) в Windows Server 2012 R2.

> [AZURE.NOTE]Эта статья содержит команды для предварительной версии Azure PowerShell 1.0. Для выполнения этих команд в Azure PowerShell 0.9.8 или предыдущих версиях замените все случаи «-AzureRM» на «-Azure» и добавьте команду **Switch-AzureMode AzureResourceManager** до выполнения всех остальных команд. Дополнительные сведения см. в статье [Предварительная версия Azure PowerShell 1.0](https://azure.microsoft.com/blog/azps-1-0-pre/).

Сначала нужно настроить внутреннюю балансировку нагрузки, чтобы среда Azure равномерно распределяла трафик бизнес-приложения между двумя веб-серверами. Для этого необходимо задать экземпляр подсистемы внутренней балансировки нагрузки, состоящий из имени и собственного IP-адреса из адресного пространства подсети, которое было назначено вашей виртуальной сети Azure.

Укажите вместо переменных нужные значения и выполните следующий набор команд.

	# Set up key variables
	$rgName="<resource group name>"
	$locName="<Azure location of your resource group>"
	$vnetName="<Table V – Item 1 – Value column>"
	$privIP="<available IP address on the subnet>"
	$vnet=Get-AzureRMVirtualNetwork -Name $vnetName -ResourceGroupName $rgName

	$frontendIP=New-AzureRMLoadBalancerFrontendIpConfig -Name WebServers-LBFE -PrivateIPAddress $privIP -SubnetId $vnet.Subnets[1].Id
	$beAddressPool=New-AzureRMLoadBalancerBackendAddressPoolConfig -Name WebServers-LBBE

	# This example assumes unsecured (HTTP-based) web traffic to the web servers.
	$healthProbe=New-AzureRMLoadBalancerProbeConfig -Name WebServersProbe -Protocol "TCP" -Port 80 -IntervalInSeconds 15 -ProbeCount 2
	$lbrule=New-AzureRMLoadBalancerRuleConfig -Name "WebTraffic" -FrontendIpConfiguration $frontendIP -BackendAddressPool $beAddressPool -Probe $healthProbe -Protocol "TCP" -FrontendPort 80 -BackendPort 80
	New-AzureRMLoadBalancer -ResourceGroupName $rgName -Name "WebServersInAzure" -Location $locName -LoadBalancingRule $lbrule -BackendAddressPool $beAddressPool -Probe $healthProbe -FrontendIpConfiguration $frontendIP

Добавьте адрес DNS-сервера в свою внутреннюю инфраструктуру DNS, которая сопоставляет полное доменное имя бизнес-приложения (например, lobapp.corp.contoso.com) с IP-адресом, который назначен подсистеме внутренней балансировки нагрузки (значение $privIP в приведенном выше наборе команд Azure PowerShell).

С помощью приведенного ниже набора команд PowerShell создайте виртуальные машины для двух веб-серверов. Обратите внимание на то, что в наборе команд PowerShell используются значения из перечисленных ниже таблиц.

- Таблица M (для виртуальных машин)
- Таблица V (для параметров виртуальной сети)
- Таблица S (для подсети)
- Таблица ST (для учетных записей хранения)
- Таблица A (для групп доступности)

Напоминаем, что вы задали значения для таблицы M на [втором](virtual-machines-workload-high-availability-LOB-application-phase2.md), а для таблиц V, S, ST и A — [на первом этапе](virtual-machines-workload-high-availability-LOB-application-phase1.md).

Указав все необходимые значения, выполните полученные команды в среде Azure PowerShell.

	# Set up key variables
	$rgName="<resource group name>"
	$locName="<Azure location of your resource group>"
	$webLB=Get-AzureRMLoadBalancer -ResourceGroupName $rgName -Name "WebServersInAzure"	
	
	# Use the standard storage account
	$saName="<Table ST – Item 2 – Storage account name column>"

	$vnetName="<Table V – Item 1 – Value column>"
	$beSubnetName="<Table S - Item 2 - Name column>"
	$avName="<Table A – Item 3 – Availability set name column>"
	$vnet=Get-AzureRMVirtualNetwork -Name $vnetName -ResourceGroupName $rgName
	$backendSubnet=Get-AzureRMVirtualNetworkSubnetConfig -Name $beSubnetName -VirtualNetwork $vnet
	
	# Create the first web server virtual machine
	$vmName="<Table M – Item 6 - Virtual machine name column>"
	$vmSize="<Table M – Item 6 - Minimum size column>"
	$nic=New-AzureRMNetworkInterface -Name ($vmName + "-NIC") -ResourceGroupName $rgName -Location $locName -Subnet $backendSubnet -LoadBalancerBackendAddressPool $webLB.BackendAddressPools[0]
	$avSet=Get-AzureRMAvailabilitySet -Name $avName –ResourceGroupName $rgName 
	$vm=New-AzureRMVMConfig -VMName $vmName -VMSize $vmSize -AvailabilitySetId $avset.Id
	$cred=Get-Credential -Message "Type the name and password of the local administrator account for the first web server." 
	$vm=Set-AzureRMVMOperatingSystem -VM $vm -Windows -ComputerName $vmName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
	$vm=Set-AzureRMVMSourceImage -VM $vm -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
	$vm=Add-AzureRMVMNetworkInterface -VM $vm -Id $nic.Id
	$storageAcc=Get-AzureRMStorageAccount -ResourceGroupName $rgName -Name $saName
	$osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/" + $vmName + "-OSDisk.vhd"
	$vm=Set-AzureRMVMOSDisk -VM $vm -Name "OSDisk" -VhdUri $osDiskUri -CreateOption fromImage
	New-AzureRMVM -ResourceGroupName $rgName -Location $locName -VM $vm
	
	# Create the second web server virtual machine
	$vmName="<Table M – Item 7 - Virtual machine name column>"
	$vmSize="<Table M – Item 7 - Minimum size column>"
	$nic=New-AzureRMNetworkInterface -Name ($vmName + "-NIC") -ResourceGroupName $rgName -Location $locName -Subnet $backendSubnet -LoadBalancerBackendAddressPool $webLB.BackendAddressPools[0]
	$vm=New-AzureRMVMConfig -VMName $vmName -VMSize $vmSize -AvailabilitySetId $avset.Id
	$cred=Get-Credential -Message "Type the name and password of the local administrator account for the second second SQL Server computer." 
	$vm=Set-AzureRMVMOperatingSystem -VM $vm -Windows -ComputerName $vmName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
	$vm=Set-AzureRMVMSourceImage -VM $vm -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
	$vm=Add-AzureRMVMNetworkInterface -VM $vm -Id $nic.Id
	$storageAcc=Get-AzureRMStorageAccount -ResourceGroupName $rgName -Name $saName
	$osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/" + $vmName + "-OSDisk.vhd"
	$vm=Set-AzureRMVMOSDisk -VM $vm -Name "OSDisk" -VhdUri $osDiskUri -CreateOption fromImage
	New-AzureRMVM -ResourceGroupName $rgName -Location $locName -VM $vm

> [AZURE.NOTE]Так как эти виртуальные машины предназначены для приложения интрасети, им не назначается общедоступный IP-адрес, у них нет метки DNS-имени домена и они не доступны в Интернете. Однако это также означает, что к ним невозможно подключиться с помощью портала предварительной версии Azure. Кнопка **Подключить** будет недоступна при просмотре свойств виртуальной машины.

Используя предпочитаемый клиент удаленного рабочего стола, создайте подключение к удаленному рабочему столу каждой виртуальной машины с веб-сервером. Для подключения используйте DNS интрасети контроллера домена или имя компьютера, а также учетные данные локальной учетной записи администратора.

Присоедините все виртуальные машины с веб-серверами к соответствующему домену AD DS, выполнив на каждой машине следующие команды Windows PowerShell.

	$domName="<Active Directory domain name to join, such as corp.contoso.com>"
	Add-Computer -DomainName $domName
	Restart-Computer

Обратите внимание, что после ввода команды **Add-Computer** необходимо ввести данные для входа в учетную запись домена.

Когда виртуальные машины перезапустятся, повторно подключитесь к ним, используя учетную запись с правами локального администратора.

На каждом веб-сервере установите и настройте службы IIS.

1. Запустите диспетчер сервера и нажмите кнопку **Добавить роли и компоненты**.
2. На странице «Перед началом работы» нажмите кнопку **Далее**.
3. На странице «Выбор типа установки» нажмите кнопку **Далее**.
4. На странице «Выбор целевого сервера» нажмите кнопку **Далее**.
5. На странице «Роли сервера» в списке **Роли** щелкните **Веб-сервер (IIS)**.
6. При появлении запроса щелкните **Добавить компоненты**, а затем нажмите кнопку **Далее**.
7. На странице «Выбор компонентов» нажмите кнопку **Далее**.
8. На странице «Веб-сервер (IIS)» нажмите кнопку **Далее**.
9. На странице выбора служб ролей установите или снимите флажки для служб, которые необходимы для бизнес-приложения, а затем нажмите кнопку **Далее**. 10. На странице «Подтверждение выбранных элементов для установки» щелкните **Установить**.

## Развертывание бизнес-приложения на виртуальных машинах с веб-серверами

На компьютере в локальной сети сделайте вот что:

1.	Добавьте файлы вашего бизнес-приложения на оба веб-сервера.
2.	Создайте базы данных вашего бизнес-приложения в кластере SQL Server.
3.	Проверьте доступ к бизнес-приложению и его работоспособность.

На этой схеме показана конфигурация, которая должна у вас получиться после успешного выполнения этого этапа.

![](./media/virtual-machines-workload-high-availability-LOB-application-phase4/workload-lobapp-phase4.png)

## Дальнейшее действие

Дальнейшие действия по настройке этой рабочей нагрузки см. в статье [Этап 5. Создание группы доступности и добавление баз данных приложений](virtual-machines-workload-high-availability-LOB-application-phase5.md).

## Дополнительные ресурсы

[Развертывание высокодоступных бизнес-приложений в Azure](virtual-machines-workload-high-availability-LOB-application-overview.md)

[Архитектурный проект бизнес-приложений](http://msdn.microsoft.com/dn630664)

[Настройка веб-бизнес-приложения в гибридном облаке для тестирования](../virtual-network/virtual-networks-setup-lobapp-hybrid-cloud-testing.md)

[Руководство по реализации служб инфраструктуры Azure](virtual-machines-infrastructure-services-implementation-guidelines.md)

[Рабочая нагрузка служб инфраструктуры Azure: ферма SharePoint Server 2013](virtual-machines-workload-intranet-sharepoint-farm.md)

<!---HONumber=Oct15_HO4-->
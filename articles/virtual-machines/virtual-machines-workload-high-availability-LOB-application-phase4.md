<properties 
	pageTitle="Четвертый этап развертывания бизнес-приложений: настройка веб-серверов" 
	description="На этом этапе развертывания высокодоступного бизнес-приложения на базе служб инфраструктуры Azure создаются веб-серверы, на которых обрабатывается нагрузка вашего бизнес-приложения." 
	documentationCenter=""
	services="virtual-machines" 
	authors="JoeDavies-MSFT" 
	manager="timlt" 
	editor=""
	tags="azure-resource-manager"/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/11/2015" 
	ms.author="josephd"/>

# Четвертый этап развертывания бизнес-приложений: настройка веб-серверов

На этом этапе развертывания высокодоступного бизнес-приложения на базе служб инфраструктуры Azure вы создадите веб-серверы, на которых будет обрабатываться нагрузка вашего бизнес-приложения.

Этот этап необходимо выполнить, прежде чем переходить к [этапу 5](virtual-machines-workload-high-availability-LOB-application-phase5.md). Все этапы перечислены в статье [Развертывание высокодоступных бизнес-приложений в Azure](virtual-machines-workload-high-availability-LOB-application-overview.md).

## Создание виртуальных машин с веб-серверами в среде Azure

Существует две виртуальные машины с веб-серверами, на которых можно развертывать приложения ASP.NET или более старые программы, для размещения которых можно использовать Internet Information Services 8 (IIS) в Windows Server 2012 R2.

Сначала нужно настроить внутреннюю балансировку нагрузки, чтобы среда Azure равномерно распределяла трафик бизнес-приложения между двумя веб-серверами. Для этого необходимо задать экземпляр подсистемы внутренней балансировки нагрузки, состоящий из имени и собственного IP-адреса из адресного пространства подсети, которое было назначено вашей виртуальной сети Azure. Чтобы определить доступность IP-адреса, выбранного для подсистемы внутренней балансировки нагрузки, в командной строке Azure PowerShell выполните следующие команды. Укажите вместо переменных нужные значения и удалите символы < and >.

	Switch-AzureMode AzureServiceManagement
	$vnet="<Table V – Item 1 – Value column>"
	$testIP="<a chosen IP address from the subnet address space, Table S - Item 2 – Subnet address space column>"
	Test-AzureStaticVNetIP –VNetName $vnet –IPAddress $testIP

IP-адрес можно использовать, если в поле **IsAvailable** в данных, возвращаемых командой Test-AzureStaticVNetIP, указано значение **True**.

Вернитесь в режим диспетчера ресурсов PowerShell, используя следующую команду.

	Switch-AzureMode AzureResourceManager

Затем укажите вместо переменных нужные значения и выполните следующий набор команд.

	$rgName="<resource group name>"
	$locName="<Azure location of your resource group>"
	$vnetName="<Table V – Item 1 – Value column>"
	$privIP="<available IP address on the subnet>"
	$vnet=Get-AzureVirtualNetwork -Name $vnetName -ResourceGroupName $rgName

	$frontendIP=New-AzureLoadBalancerFrontendIpConfig -Name WebServers-LBFE -PrivateIPAddress $privIP -SubnetId $vnet.Subnets[1].Id
	$beAddressPool=New-AzureLoadBalancerBackendAddressPoolConfig -Name WebServers-LBBE

	# This example assumes unsecured (HTTP-based) web traffic to the web servers.
	$healthProbe=New-AzureLoadBalancerProbeConfig -Name WebServersProbe -Protocol "TCP" -Port 80 -IntervalInSeconds 15 -ProbeCount 2
	$lbrule=New-AzureLoadBalancerRuleConfig -Name "WebTraffic" -FrontendIpConfiguration $frontendIP -BackendAddressPool $beAddressPool -Probe $healthProbe -Protocol "TCP" -FrontendPort 80 -BackendPort 80
	New-AzureLoadBalancer -ResourceGroupName $rgName -Name "WebServersInAzure" -Location $locName -LoadBalancingRule $lbrule -BackendAddressPool $beAddressPool -Probe $healthProbe -FrontendIpConfiguration $frontendIP

Добавьте адрес DNS-сервера в свою внутреннюю инфраструктуру DNS, которая сопоставляет полное доменное имя бизнес-приложения (например, lobapp.corp.contoso.com) с IP-адресом, который назначен подсистеме внутренней балансировки нагрузки (значение $privIP в приведенном выше наборе команд Azure PowerShell).

С помощью приведенного ниже набора команд PowerShell создайте виртуальные машины для двух веб-серверов. Обратите внимание на то, что в наборе команд PowerShell используются значения из перечисленных ниже таблиц.

- Таблица M (для виртуальных машин)
- Таблица V (для параметров виртуальной сети)
- Таблица S (для подсети)
- Таблица ST (для учетных записей хранения)
- Таблица A (для групп доступности)

Значения для таблицы M были заданы на [этапе 2](virtual-machines-workload-high-availability-LOB-application-phase2.md), а для таблиц V, S, ST и A — на [этапе 1](virtual-machines-workload-high-availability-LOB-application-phase1.md).

Указав все необходимые значения, выполните полученные команды в среде Azure PowerShell.

	# Set up subscription and key variables
	$subscr="<name of the Azure subscription>"
	Set-AzureSubscription -SubscriptionName $subscr
	Switch-AzureMode AzureResourceManager
	$rgName="<resource group name>"
	$locName="<Azure location of your resource group>"
	$webLB=Get-AzureLoadBalancer -ResourceGroupName $rgName -Name "WebServersInAzure"	
	
	# Use the standard storage account
	$saName="<Table ST – Item 2 – Storage account name column>"

	$vnetName="<Table V – Item 1 – Value column>"
	$beSubnetName="<Table S - Item 2 - Name column>"
	$avName="<Table A – Item 3 – Availability set name column>"
	$vnet=Get-AzurevirtualNetwork -Name $vnetName -ResourceGroupName $rgName
	$backendSubnet=Get-AzureVirtualNetworkSubnetConfig -Name $beSubnetName -VirtualNetwork $vnet
	
	# Create the first web server virtual machine
	$vmName="<Table M – Item 6 - Virtual machine name column>"
	$vmSize="<Table M – Item 6 - Minimum size column>"
	$nic=New-AzureNetworkInterface -Name ($vmName + "-NIC") -ResourceGroupName $rgName -Location $locName -Subnet $backendSubnet -LoadBalancerBackendAddressPool $webLB.BackendAddressPools[0]
	$avSet=Get-AzureAvailabilitySet -Name $avName –ResourceGroupName $rgName 
	$vm=New-AzureVMConfig -VMName $vmName -VMSize $vmSize -AvailabilitySetId $avset.Id
	$cred=Get-Credential -Message "Type the name and password of the local administrator account for the first web server." 
	$vm=Set-AzureVMOperatingSystem -VM $vm -Windows -ComputerName $vmName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
	$vm=Set-AzureVMSourceImage -VM $vm -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
	$vm=Add-AzureVMNetworkInterface -VM $vm -Id $nic.Id
	$storageAcc=Get-AzureStorageAccount -ResourceGroupName $rgName -Name $saName
	$osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/" + $vmName + "-OSDisk.vhd"
	$vm=Set-AzureVMOSDisk -VM $vm -Name "OSDisk" -VhdUri $osDiskUri -CreateOption fromImage
	New-AzureVM -ResourceGroupName $rgName -Location $locName -VM $vm
	
	# Create the second web server virtual machine
	$vmName="<Table M – Item 7 - Virtual machine name column>"
	$vmSize="<Table M – Item 7 - Minimum size column>"
	$nic=New-AzureNetworkInterface -Name ($vmName + "-NIC") -ResourceGroupName $rgName -Location $locName -Subnet $backendSubnet -LoadBalancerBackendAddressPool $webLB.BackendAddressPools[0]
	$vm=New-AzureVMConfig -VMName $vmName -VMSize $vmSize -AvailabilitySetId $avset.Id
	$cred=Get-Credential -Message "Type the name and password of the local administrator account for the second second SQL Server computer." 
	$vm=Set-AzureVMOperatingSystem -VM $vm -Windows -ComputerName $vmName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
	$vm=Set-AzureVMSourceImage -VM $vm -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
	$vm=Add-AzureVMNetworkInterface -VM $vm -Id $nic.Id
	$storageAcc=Get-AzureStorageAccount -ResourceGroupName $rgName -Name $saName
	$osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/" + $vmName + "-OSDisk.vhd"
	$vm=Set-AzureVMOSDisk -VM $vm -Name "OSDisk" -VhdUri $osDiskUri -CreateOption fromImage
	New-AzureVM -ResourceGroupName $rgName -Location $locName -VM $vm

Используя предпочитаемый клиент удаленного рабочего стола, создайте подключение к удаленному рабочему столу каждой виртуальной машины с веб-сервером. Для подключения используйте DNS интрасети контроллера домена или имя компьютера, а также учетные данные локальной учетной записи администратора.

Присоедините все виртуальные машины с веб-серверами к соответствующему домену AD DS, выполнив на каждой машине следующие команды Windows PowerShell.

	$domName="<Active Directory domain name to join, such as corp.contoso.com>"
	Add-Computer -DomainName $domName
	Restart-Computer

Обратите внимание, что после ввода команды **Add-Computer** необходимо указать данные для входа в учетную запись домена.

Когда виртуальные машины перезапустятся, повторно подключитесь к ним, используя учетную запись с правами локального администратора.

На каждом веб-сервере установите и настройте службы IIS.

1. Запустите диспетчер сервера и нажмите кнопку **Добавить роли и компоненты**.
2. На странице «Перед началом работы» щелкните **Далее**.
3. На странице «Выбор типа установки» щелкните **Далее**.
4. На странице «Выбор целевого сервера» щелкните **Далее**.
5. На странице «Роли сервера» в списке **Роли** щелкните **Веб-сервер (IIS)**.
6. При появлении запроса нажмите кнопку **Добавить компоненты**, а затем щелкните **Далее**.
7. На странице «Выбор компонентов» щелкните **Далее**.
8. На странице «Веб-сервер (IIS)» щелкните **Далее**.
9. На странице «Выбор служб ролей» установите или снимите флажки для служб, которые необходимы для бизнес-приложения, а затем щелкните **Далее**. 10. На странице «Подтверждение выбранных элементов для установки» щелкните **Установить**.

## Развертывание бизнес-приложения на виртуальных машинах с веб-серверами

На компьютере в локальной сети сделайте вот что:

1.	Добавьте файлы вашего бизнес-приложения на оба веб-сервера.
2.	Создайте базы данных вашего бизнес-приложения в кластере SQL Server.
3.	Проверьте доступ к бизнес-приложению и его работоспособность.

На этой схеме показана конфигурация, которая должна у вас получиться после успешного выполнения этого этапа.

![](./media/virtual-machines-workload-high-availability-LOB-application-phase4/workload-lobapp-phase4.png)

## Дальнейшее действие

Дальнейшие действия по настройке этой среды см. в статье [Этап 5. Создание группы доступности и добавление баз данных приложения](virtual-machines-workload-high-availability-LOB-application-phase5.md).

## Дополнительные ресурсы

[Развертывание высокодоступных бизнес-приложений в Azure](virtual-machines-workload-high-availability-LOB-application-overview.md)

[Архитектурный проект бизнес-приложений](http://msdn.microsoft.com/dn630664)

[Настройка веб-бизнес-приложения в гибридном облаке для тестирования](../virtual-network/virtual-networks-setup-lobapp-hybrid-cloud-testing.md)

[Руководство по реализации служб инфраструктуры Azure](virtual-machines-infrastructure-services-implementation-guidelines.md)

[Рабочая нагрузка служб инфраструктуры Azure: ферма SharePoint Server 2013](virtual-machines-workload-intranet-sharepoint-farm.md)

<!---HONumber=August15_HO8-->
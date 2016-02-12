<properties
	pageTitle="Создание и настройка виртуальной машины | Microsoft Azure"
	description="Создание и настройка виртуальной машины Azure с использованием Powershell и модели развертывания на основе диспетчера ресурсов."
	services="virtual-machines"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/08/2015"
	ms.author="cynthn"/>

# Создание и настройка виртуальной машины Windows с помощью диспетчера ресурсов и Azure PowerShell

> [AZURE.SELECTOR]
- [Portal - Windows](virtual-machines-windows-tutorial.md)
- [PowerShell](virtual-machines-ps-create-preconfigure-windows-resource-manager-vms.md)
- [PowerShell - Template](virtual-machines-create-windows-powershell-resource-manager-template.md)
- [Portal - Linux](virtual-machines-linux-tutorial-portal-rm.md)
- [CLI](virtual-machines-linux-tutorial.md)

<br>



[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)] [classic deployment model](virtual-machines-ps-create-preconfigure-windows-vms.md).

Ниже показано, как составить набор команд Azure PowerShell для создания и настройки виртуальной машины Azure. Используя процесс на основе стандартных блоков, вы можете быстро подготовить набор команд для создания виртуальной машины под управлением Windows или расширения уже развернутых. Также можно создать несколько наборов команд, позволяющих быстро настраивать ИТ-среды для разработки, тестирования и профессионального использования.

Материал в этих шагах для создания наборов команд Azure PowerShell представлен таким образом, что достаточно лишь заполнить пробелы. Этот подход может оказаться полезным, если вы не знакомы с PowerShell или хотите знать, какие именно значения следует задать для обеспечения работоспособности конфигурации. Опытные пользователи PowerShell могут подставить в команды собственные значения переменных (строки, начинающиеся со знака "$").

## Шаг 1. Установка Azure PowerShell

[AZURE.INCLUDE [powershell-preview](../../includes/powershell-preview-inline-include.md)]

## Шаг 2. Настройка подписки

Запустите командную строку Azure PowerShell.

Войдите в свою учетную запись.

	Login-AzureRmAccount

Получите имя своей подписки, выполнив указанную ниже команду.

	Get-AzureSubscription | Sort SubscriptionName | Select SubscriptionName

Настройте свою подписку Azure. Замените все содержимое внутри кавычек, включая символы < and >, на правильные имена.

	$subscr="<subscription name>"
	Select-AzureSubscription -SubscriptionName $subscr –Current


## Шаг 3. Создание ресурсов

В этом разделе будет показано, как создать каждый ресурс для новой виртуальной машины.

### Группа ресурсов


Для виртуальных машин, созданных с помощью модели развертывания диспетчера ресурсов, требуются группы ресурсов. Если нужно, создайте новую группу ресурсов для новой виртуальной машины. Замените все содержимое внутри кавычек, включая символы < and >, на правильные имена.

	$rgName="<resource group name>"
	$locName="<location name, such as West US>"
	New-AzureRmResourceGroup -Name $rgName -Location $locName

Чтобы вывести список существующих групп ресурсов, используйте эту команду:

	Get-AzureRmResourceGroup | Sort ResourceGroupName | Select ResourceGroupName

Чтобы получить список расположений Azure, в которых можно создать виртуальные машины на основе диспетчера ресурсов, выполните следующий код:

	$loc=Get-AzureRmLocation | where { $_.Name –eq "Microsoft.Compute/virtualMachines" }
	$loc.Locations

### Учетная запись хранения


Для виртуальных машин, созданных с помощью модели развертывания диспетчера ресурсов, требуется учетная запись хранения на основе диспетчера ресурсов. При необходимости создайте для новой виртуальной машины учетную запись хранения, используя следующие команды.

	$rgName="<resource group name>"
	$locName="<location name, such as West US>"
	$saName="<storage account name>"
	$saType="<storage account type, specify one: Standard_LRS, Standard_GRS, Standard_RAGRS, or Premium_LRS>"
	New-AzureRmStorageAccount -Name $saName -ResourceGroupName $rgName –Type $saType -Location $locName

Необходимо выбрать для нее глобально уникальное имя, содержащее только строчные буквы и цифры. Чтобы вывести список существующих учетных записей хранения, используйте эту команду.

	Get-AzureRmStorageAccount | Sort Name | Select Name

Чтобы убедиться, что выбранное имя учетной записи хранения является глобально уникальным, выполните команду **Test-AzureName**.

	Test-AzureName -Storage <Proposed storage account name>

Если в результате выполнения команды Test-AzureName отображается значение False, предложенное имя является уникальным.


### Метка общедоступного доменного имени


Виртуальные машины, созданные с помощью модели развертывания диспетчера ресурсов, могут использовать метку общедоступного доменного имени. Она может содержать только буквы, цифры и дефисы. Первый и последний символ должны быть буквами или цифрами.

Чтобы проверить уникальность выбранного доменного имени, используйте следующие команды.

	$domName="<domain name label to test>"
	$loc="<short name of an Azure location, for example, for West US, the short name is westus>"
	Test-AzureRmDnsAvailability -DomainQualifiedName $domName -Location $loc

Если DNSNameAvailability возвращает значение True, предложенное имя является глобально уникальным.

### группа доступности;


При необходимости создайте для новой виртуальной машины группу доступности, используя следующие команды.

	$avName="<availability set name>"
	$rgName="<resource group name>"
	$locName="<location name, such as West US>"
	New-AzureRmAvailabilitySet –Name $avName –ResourceGroupName $rgName -Location $locName

Чтобы просмотреть список существующих групп доступности, используйте эту команду.

	Get-AzureRmAvailabilitySet –ResourceGroupName $rgName | Sort Name | Select Name

### Правила преобразования сетевых адресов

Виртуальные машины на основе диспетчера ресурсов можно настроить с помощью правил преобразования сетевых адресов (NAT) для входящих подключений, чтобы разрешить входящий трафик из Интернета и направить его в набор балансировки нагрузки. В обоих случаях необходимо указать экземпляр подсистемы балансировки нагрузки и другие параметры. Дополнительные сведения см. в статье [Создание балансировщика нагрузки с помощью диспетчера ресурсов Azure](../load-balancer/load-balancer-arm-powershell.md).

Для виртуальных машин, созданных с помощью модели развертывания диспетчера ресурсов, требуется виртуальная сеть диспетчера ресурсов. При необходимости создайте для новой виртуальной машины виртуальную сеть на базе диспетчера ресурсов с минимум одной подсетью. Ниже приводится пример новой виртуальной сети **TestNet** с двумя подсетями: **frontendSubnet** и **backendSubnet**.

	$rgName="LOBServers"
	$locName="West US"
	$frontendSubnet=New-AzureRmVirtualNetworkSubnetConfig -Name frontendSubnet -AddressPrefix 10.0.1.0/24
	$backendSubnet=New-AzureRmVirtualNetworkSubnetConfig -Name backendSubnet -AddressPrefix 10.0.2.0/24
	New-AzureRmVirtualNetwork -Name TestNet -ResourceGroupName $rgName -Location $locName -AddressPrefix 10.0.0.0/16 -Subnet $frontendSubnet,$backendSubnet

Чтобы вывести список существующих виртуальных сетей, используйте следующие команды:

	$rgName="<resource group name>"
	Get-AzureRmVirtualNetwork -ResourceGroupName $rgName | Sort Name | Select Name

## Шаг 4. Создание своего набора команд

Откройте новый экземпляр любого текстового редактора или интегрированную среду сценариев PowerShell и скопируйте следующие строки в свой набор команд. Укажите имя группы ресурсов, расположение в Azure и учетную запись хранения для новой виртуальной машины. Замените все содержимое внутри кавычек, включая символы < and >, на правильные имена.

	$rgName="<resource group name>"
	$locName="<Azure location, such as West US>"
	$saName="<storage account name>"

Необходимо указать имя виртуальной сети на базе диспетчера ресурсов и индекс подсети в виртуальной сети. Чтобы вывести список существующих подсетей в виртуальной сети, используйте следующие команды.

	$rgName="<resource group name>"
	$vnetName="<virtual network name>"
	Get-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $rgName | Select Subnets

Индекс подсети — это порядковый номер подсети в параметрах команды; подсети нумеруются последовательно слева направо начиная с 0.

В нашем примере:

	PS C:\> Get-AzureRmVirtualNetwork -Name TestNet -ResourceGroupName LOBServers | Select Subnets

	Subnets
	-------
	{frontendSubnet, backendSubnet}

Индекс подсети frontendSubnet —0, а подсети backendSubnet — 1.

Скопируйте эти строки в набор команд и укажите имя существующей виртуальной сети и индекс подсети для виртуальной машины.

	$vnetName="<name of an existing virtual network>"
	$subnetIndex=<index of the subnet on which to create the NIC for the virtual machine>
	$vnet=Get-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $rgName

Затем создайте сетевую карту. Скопируйте данные из одного из следующих вариантов в набор команд и введите имя необходимые сведения.

### Вариант 1. Укажите имя сетевой карты и назначьте общедоступный IP-адрес.

Скопируйте следующие строки в набор команд и укажите имя сетевой карты.

	$nicName="<name of the NIC of the VM>"
	$pip = New-AzureRmPublicIpAddress -Name $nicName -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic
	$nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[$subnetIndex].Id -PublicIpAddressId $pip.Id

### Вариант 2. Укажите имя сетевой карты и DNS-имя.

Скопируйте следующие строки в набор команд и укажите имя сетевой карты, а также глобально уникальное доменное имя.

	$nicName="<name of the NIC of the VM>"
	$domName="<domain name label>"
	$pip = New-AzureRmPublicIpAddress -Name $nicName -ResourceGroupName $rgName -DomainNameLabel $domName -Location $locName -AllocationMethod Dynamic
	$nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[$subnetIndex].Id -PublicIpAddressId $pip.Id

### Вариант 3. Укажите имя сетевой карты и назначьте статический частный IP-адрес.

Скопируйте следующие строки в набор команд и укажите имя сетевой карты.

	$nicName="<name of the NIC of the VM>"
	$staticIP="<available static IP address on the subnet>"
	$pip = New-AzureRmPublicIpAddress -Name $nicName -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic
	$nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[$subnetIndex].Id -PublicIpAddressId $pip.Id -PrivateIpAddress $staticIP

### Вариант 4. Укажите имя сетевой карты и экземпляр подсистемы балансировки нагрузки для правила преобразования сетевых адресов (NAT) для входящих подключений.

Чтобы создать сетевую карту и добавить ее в экземпляр подсистемы балансировки нагрузки для правила преобразования сетевых адресов (NAT) для входящих подключений, необходимы следующие данные:

- имя ранее созданного экземпляра подсистемы балансировки нагрузки, включающего правило преобразования сетевых адресов (NAT) для входящих подключений для трафика, который переадресовывается на виртуальную машину;
- номер индекса пула адресов серверной части экземпляра подсистемы балансировки нагрузки, который нужно назначить сетевой карте;
- номер индекса правила преобразования сетевых адресов (NAT) для входящих подключений, которое нужно назначить сетевой карте.

Информацию о том, как создать экземпляр балансировщика нагрузки с помощью правил преобразования сетевых адресов (NAT) для входящих подключений, см. в статье [Создание балансировщика нагрузки с помощью диспетчера ресурсов Azure](../load-balancer/load-balancer-arm-powershell.md).

Скопируйте эти строки в набор команд и укажите необходимые имена и номера индекса.

	$nicName="<name of the NIC of the VM>"
	$lbName="<name of the load balancer instance>"
	$bePoolIndex=<index of the back end pool, starting at 0>
	$natRuleIndex=<index of the inbound NAT rule, starting at 0>
	$lb=Get-AzureRmLoadBalancer -Name $lbName -ResourceGroupName $rgName
	$nic=New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $rgName -Location $locName -Subnet $vnet.Subnets[$subnetIndex].Id -LoadBalancerBackendAddressPool $lb.BackendAddressPools[$bePoolIndex] -LoadBalancerInboundNatRule $lb.InboundNatRules[$natRuleIndex]

Строка $nicName должна быть уникальной для группы ресурсов. В строку рекомендуется включить имя виртуальной машины, например LOB07-NIC.

### Вариант 5. Укажите имя сетевой карты и экземпляр подсистемы балансировки нагрузки для набора балансировки нагрузки.

Чтобы создать сетевую карту и добавить ее в экземпляр подсистемы балансировки нагрузки для набора балансировки нагрузки, необходимы следующие данные:

- имя ранее созданного экземпляра подсистемы балансировки нагрузки, включающего правило для трафика с балансировкой нагрузки;
- номер индекса пула адресов серверной части экземпляра подсистемы балансировки нагрузки, который нужно назначить сетевой карте.

Информацию о том, как создать экземпляр балансировщика нагрузки с правилами для трафика с балансировкой нагрузки, см. в статье [Создание балансировщика нагрузки с помощью диспетчера ресурсов Azure](../load-balancer/load-balancer-arm-powershell.md).

Скопируйте эти строки в набор команд и укажите необходимые имена и номера индекса.

	$nicName="<name of the NIC of the VM>"
	$lbName="<name of the load balancer instance>"
	$bePoolIndex=<index of the back end pool, starting at 0>
	$lb=Get-AzureRmLoadBalancer -Name $lbName -ResourceGroupName $rgName
	$nic=New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $rgName -Location $locName -Subnet $vnet.Subnets[$subnetIndex].Id -LoadBalancerBackendAddressPool $lb.BackendAddressPools[$bePoolIndex]

Затем создайте локальный объект виртуальной машины и при необходимости добавьте его в группу доступности. Скопируйте код первого или второго варианта в набор команд и введите имя и размер виртуальной машины, а также имя группы доступности.

Вариант 1. Укажите имя и размер виртуальной машины.

	$vmName="<VM name>"
	$vmSize="<VM size string>"
	$vm=New-AzureRmVMConfig -VMName $vmName -VMSize $vmSize

Чтобы определить возможные значения для строки размера виртуальной машины (вариант 1), используйте следующие команды.

	$locName="<Azure location of your resource group>"
	Get-AzureRmVMSize -Location $locName | Select Name

Вариант 2. Укажите имя и размер виртуальной машины и добавьте ее в группу доступности.

	$vmName="<VM name>"
	$vmSize="<VM size string>"
	$avName="<availability set name>"
	$avSet=Get-AzureRmAvailabilitySet –Name $avName –ResourceGroupName $rgName
	$vm=New-AzureRmVMConfig -VMName $vmName -VMSize $vmSize -AvailabilitySetId $avset.Id

Чтобы определить возможные значения для строки размера виртуальной машины (вариант 2), используйте следующие команды.

	$rgName="<resource group name>"
	$avName="<availability set name>"
	Get-AzureRmVMSize -ResourceGroupName $rgName -AvailabilitySetName $avName | Select Name

> [AZURE.NOTE] В настоящее время диспетчер ресурсов позволяет добавлять виртуальные машины в группу доступности только во время их создания.

Чтобы добавить дополнительный диск с данными в виртуальную машину, скопируйте следующие строки в набор команд и задайте параметры диска.

	$diskSize=<size of the disk in GB>
	$diskLabel="<the label on the disk>"
	$diskName="<name identifier for the disk in Azure storage, such as 21050529-DISK02>"
	$storageAcc=Get-AzureRmStorageAccount -ResourceGroupName $rgName -Name $saName
	$vhdURI=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/" + $vmName + $diskName  + ".vhd"
	Add-AzureRmVMDataDisk -VM $vm -Name $diskLabel -DiskSizeInGB $diskSize -VhdUri $vhdURI  -CreateOption empty

Далее определите издателя, предложение и SKU образа виртуальной машины. Ниже приведена таблица часто используемых образов с операционной системой Windows.

|Имя издателя | Название предложения | Номер SKU
|:---------------------------------|:-------------------------------------------|:---------------------------------|
|MicrosoftWindowsServer | WindowsServer | 2008-R2-SP1 |
|MicrosoftWindowsServer | WindowsServer | 2012-Datacenter |
|MicrosoftWindowsServer | WindowsServer | 2012-R2-Datacenter |
|MicrosoftDynamicsNAV | DynamicsNAV | 2015 |
|MicrosoftSharePoint | MicrosoftSharePointServer | 2013 |
|MicrosoftSQLServer | SQL2014 WS2012R2 | Enterprise-Optimized-for-DW |
|MicrosoftSQLServer | SQL2014 WS2012R2 | Enterprise-Optimized-for-OLTP |
|MicrosoftWindowsServerEssentials | WindowsServerEssentials | WindowsServerEssentials |
|MicrosoftWindowsServerHPCPack | WindowsServerHPCPack | 2012R2 |

Если вашего образа виртуальной машины нет в списке, определите издателя, предложение и номер SKU, следуя приведенным [здесь](resource-groups-vm-searching.md#powershell) инструкциям.

Скопируйте эти команды в набор команд и укажите имя издателя, название предложения и номер SKU.

	$pubName="<Image publisher name>"
	$offerName="<Image offer name>"
	$skuName="<Image SKU name>"
	$cred=Get-Credential -Message "Type the name and password of the local administrator account."
	$vm=Set-AzureRmVMOperatingSystem -VM $vm -Windows -ComputerName $vmName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
	$vm=Set-AzureRmVMSourceImage -VM $vm -PublisherName $pubName -Offer $offerName -Skus $skuName -Version "latest"
	$vm=Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id

Затем скопируйте эти команды в набор команд и введите идентификатор (имя) диска операционной системы для виртуальной машины.

	$diskName="<name identifier for the disk in Azure storage, such as OSDisk>"
	$storageAcc=Get-AzureRmStorageAccount -ResourceGroupName $rgName -Name $saName
	$osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/" + $diskName  + ".vhd"
	$vm=Set-AzureRmVMOSDisk -VM $vm -Name $diskName -VhdUri $osDiskUri -CreateOption fromImage
	New-AzureRmVM -ResourceGroupName $rgName -Location $locName -VM $vm

## Шаг 5. Запуск набора команд

Просмотрите в текстовом редакторе или среде PowerShell ISE набор команд Azure PowerShell, созданный при выполнении шага 4. Убедитесь, что все переменные указаны и имеют правильные значения. Также убедитесь, что удалены все символы < and >.

Если вы используете текстовый редактор, скопируйте набор команд в буфер обмена и щелкните его правой кнопкой мыши в командном окне Azure PowerShell. При этом набор команд отправляется как последовательность команд PowerShell и создается виртуальная машина Azure. Или выполните набор команд в среде Azure PowerShell ISE.

Для того чтобы использовать данные сведения для создания других виртуальных машин, сохраните этот набор команд как файл сценария PowerShell (PS1).

## Пример

Мне требуется набор команд PowerShell, чтобы создать дополнительную виртуальную машину для обработки коммерческого веб-трафика. ВМ должна:

- находиться в существующей группе ресурсов LOBServers;
- использовать образ Windows Server 2012 R2 Datacenter;
- иметь имя LOB07 и входить в существующую группу доступности WEB\_AS;
- иметь сетевой адаптер с общедоступным IP-адресом в подсети FrontEnd (индекс подсети — 0) существующей виртуальной сети AZDatacenter;
- иметь дополнительный диск данных объемом 200 ГБ.

Вот набор команд Azure PowerShell для создания такой виртуальной машины:

	# Set values for existing resource group and storage account names
	$rgName="LOBServers"
	$locName="West US"
	$saName="contosolobserverssa"

	# Set the existing virtual network and subnet index
	$vnetName="AZDatacenter"
	$subnetIndex=0
	$vnet=Get-AzureRMVirtualNetwork -Name $vnetName -ResourceGroupName $rgName

	# Create the NIC
	$nicName="LOB07-NIC"
	$domName="contoso-vm-lob07"
	$pip=New-AzureRmPublicIpAddress -Name $nicName -ResourceGroupName $rgName -DomainNameLabel $domName -Location $locName -AllocationMethod Dynamic
	$nic=New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[$subnetIndex].Id -PublicIpAddressId $pip.Id

	# Specify the name, size, and existing availability set
	$vmName="LOB07"
	$vmSize="Standard_A3"
	$avName="WEB_AS"
	$avSet=Get-AzureRmAvailabilitySet –Name $avName –ResourceGroupName $rgName
	$vm=New-AzureRmVMConfig -VMName $vmName -VMSize $vmSize -AvailabilitySetId $avset.Id

	# Add a 200 GB additional data disk
	$diskSize=200
	$diskLabel="APPStorage"
	$diskName="21050529-DISK02"
	$storageAcc=Get-AzureRmStorageAccount -ResourceGroupName $rgName -Name $saName
	$vhdURI=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/" + $vmName + $diskName  + ".vhd"
	Add-AzureRmVMDataDisk -VM $vm -Name $diskLabel -DiskSizeInGB $diskSize -VhdUri $vhdURI -CreateOption empty

	# Specify the image and local administrator account, and then add the NIC
	$pubName="MicrosoftWindowsServer"
	$offerName="WindowsServer"
	$skuName="2012-R2-Datacenter"
	$cred=Get-Credential -Message "Type the name and password of the local administrator account."
	$vm=Set-AzureRmVMOperatingSystem -VM $vm -Windows -ComputerName $vmName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
	$vm=Set-AzureRmVMSourceImage -VM $vm -PublisherName $pubName -Offer $offerName -Skus $skuName -Version "latest"
	$vm=Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id

	# Specify the OS disk name and create the VM
	$diskName="OSDisk"
	$storageAcc=Get-AzureRmStorageAccount -ResourceGroupName $rgName -Name $saName
	$osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/" + $vmName + $diskName  + ".vhd"
	$vm=Set-AzureRmVMOSDisk -VM $vm -Name $diskName -VhdUri $osDiskUri -CreateOption fromImage
	New-AzureRmVM -ResourceGroupName $rgName -Location $locName -VM $vm

## Дополнительные ресурсы

[Поставщики вычислительных и сетевых ресурсов, а также ресурсов хранения Azure в диспетчере ресурсов Azure](virtual-machines-azurerm-versus-azuresm.md)

[Общие сведения о диспетчере ресурсов Azure](../resource-group-overview.md)

[Развертывание виртуальных машин Azure и управление ими с использованием шаблонов диспетчера ресурсов и PowerShell](virtual-machines-deploy-rmtemplates-powershell.md)

[Создание виртуальной машины Windows с помощью шаблона диспетчера ресурсов и PowerShell](virtual-machines-create-windows-powershell-resource-manager-template.md)

[Установка и настройка Azure PowerShell](powershell-install-configure.md)

<!---HONumber=AcomDC_0204_2016-->
<properties
	pageTitle="Создание и предварительная настройка виртуальной машины под управлением Windows с помощью диспетчера ресурсов и Azure PowerShell"
	description="Узнайте, как использовать Azure PowerShell для создания и предварительной настройки виртуальных машин под управлением Windows в Azure."
	services="virtual-machines"
	documentationCenter=""
	authors="JoeDavies-MSFT"
	manager="timlt"
	editor=""/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/09/2015"
	ms.author="josephd"/>

# Создание и предварительная настройка виртуальной машины под управлением Windows с помощью диспетчера ресурсов и Azure PowerShell

Ниже показано, как подготовить набор команд в режиме диспетчера ресурсов Azure PowerShell для создания и предварительной настройки виртуальной машины Azure под управлением Windows. Используя процесс на основе стандартных блоков, вы можете быстро подготовить набор команд для создания виртуальной машины под управлением Windows или расширения уже развернутых. Также можно создать несколько наборов команд, позволяющих быстро настраивать ИТ-среды для разработки, тестирования и профессионального использования.

Материал в этих шагах для создания наборов команд Azure PowerShell представлен таким образом, что достаточно лишь заполнить пробелы. Этот подход может оказаться полезным, если вы не знакомы с PowerShell или хотите знать, какие именно значения следует задать для обеспечения работоспособности конфигурации. Опытные пользователи PowerShell могут подставить в команды собственные значения переменных (строки, начинающиеся со знака "$").

[AZURE.INCLUDE [resource-manager-pointer-to-service-management](../../includes/resource-manager-pointer-to-service-management.md)]

- [Использование Azure PowerShell для создания и предварительной настройки виртуальных машин под управлением Windows](virtual-machines-ps-create-preconfigure-windows-vms.md)

## Шаг 1. Установка Azure PowerShell

У Azure PowerShell должна быть версия не ниже 0.9.0. Если вы еще не установили и не настроили Azure PowerShell, см. соответствующие инструкции [здесь](../powershell-install-configure.md).

Чтобы узнать, какая версия Azure PowerShell установлена, используйте эту команду Azure PowerShell:

	Get-Module azure | format-table version

Ниже приведен пример.

	Version
	-------
	0.9.0

Если установлена версия ниже 0.9.0, удалите Azure PowerShell с помощью компонента «Программы и компоненты» панели управления, а затем установите последнюю версию. Дополнительные сведения см. в статье [Установка и настройка Azure PowerShell](../powershell-install-configure.md).

## Шаг 2. Настройка подписки

Запустите командную строку Azure PowerShell.

Задайте подписку Azure, выполнив в командной строке Azure PowerShell следующие команды. Замените все содержимое внутри кавычек, включая символы < and >, на правильные имена.

	$subscr="<subscription name>"
	Select-AzureSubscription -SubscriptionName $subscr –Current

Правильное имя команды можно увидеть в результатах выполнения этой команды.

	Get-AzureSubscription | Sort SubscriptionName | Select SubscriptionName

Перейдите в режим диспетчера ресурсов Azure PowerShell.

	Switch-AzureMode AzureResourceManager 

## Шаг 3. Создание необходимых ресурсов

Для виртуальных машин на базе диспетчера ресурсов потребуется группа ресурсов. При необходимости создайте группу ресурсов для новой виртуальной машины, используя следующие команды. Замените все содержимое внутри кавычек, включая символы < and >, на правильные имена.

	$rgName="<resource group name>"
	$locName="<location name, such as West US>"
	New-AzureResourceGroup -Name $rgName -Location $locName

Чтобы вывести список существующих групп ресурсов и выбрать уникальное имя для новой группы, используйте эту команду.

	Get-AzureResourceGroup | Sort ResourceGroupName | Select ResourceGroupName

Следующие команды помогут получить список расположений Azure, в которых можно создать виртуальные машины на основе диспетчера ресурсов.

	$loc=Get-AzureLocation | where { $_.Name –eq "Microsoft.Compute/virtualMachines" }
	$loc.Locations

Для виртуальных машин на основе диспетчера ресурсов требуется учетная запись хранения на основе диспетчера ресурсов. При необходимости создайте для новой виртуальной машины учетную запись хранения, используя следующие команды.

	$rgName="<resource group name>"
	$locName="<location name, such as West US>"
	$saName="<storage account name>"
	$saType="<storage account type, specify one: Standard_LRS, Standard_GRS, Standard_RAGRS, or Premium_LRS>"
	New-AzureStorageAccount -Name $saName -ResourceGroupName $rgName –Type $saType -Location $locName

Необходимо выбрать для нее глобально уникальное имя, содержащее только строчные буквы и цифры. Чтобы вывести список существующих учетных записей хранения, используйте эту команду.

	Get-AzureStorageAccount | Sort Name | Select Name

Чтобы убедиться, что выбранное имя учетной записи хранения глобально уникально, запустите команду **Test-AzureName** в режиме управления службами Azure PowerShell. Используйте следующие команды.

	Switch-AzureMode AzureServiceManagement
	Test-AzureName -Storage <Proposed storage account name>

Если в результате выполнения команды Test-AzureName отображается значение False, предложенное имя является уникальным. После выбора уникального имени вернитесь в режим диспетчера ресурсов Azure PowerShell, используя эту команду.

	Switch-AzureMode AzureResourceManager 

Виртуальные машины на основе диспетчера ресурсов могут использовать общедоступные доменные имена, содержащие только буквы, цифры и дефисы. Первый и последний символ в поле должны быть буквами или цифрами.

Чтобы проверить уникальность выбранного доменного имени, используйте следующие команды.

	$domName="<domain name label to test>"
	$loc="<short name of an Azure location, for example, for West US, the short name is westus>"
	Get-AzureCheckDnsAvailability -DomainQualifiedName $domName -Location $loc 
	
Если DNSNameAvailability возвращает значение True, предложенное имя является глобально уникальным.

Виртуальные машины на базе диспетчера ресурсов можно поместить в группу доступности на базе диспетчера ресурсов. При необходимости создайте для новой виртуальной машины группу доступности, используя следующие команды.

	$avName="<availability set name>"
	$rgName="<resource group name>"
	$locName="<location name, such as West US>"
	New-AzureAvailabilitySet –Name $avName –ResourceGroupName $rgName -Location $locName

Чтобы просмотреть список существующих групп доступности, используйте эту команду.

	Get-AzureAvailabilitySet –ResourceGroupName $rgName | Sort Name | Select Name

Для виртуальных машин на базе диспетчера ресурсов требуется виртуальная сеть на базе диспетчера ресурсов. При необходимости создайте для новой виртуальной машины виртуальную сеть на базе диспетчера ресурсов с минимум одной подсетью. Ниже приведен пример новой виртуальной сети с двумя подсетями: frontendSubnet и backendSubnet.

	$rgName="LOBServers"
	$locName="West US"
	$frontendSubnet=New-AzureVirtualNetworkSubnetConfig -Name frontendSubnet -AddressPrefix 10.0.1.0/24
	$backendSubnet=New-AzureVirtualNetworkSubnetConfig -Name backendSubnet -AddressPrefix 10.0.2.0/24
	New-AzurevirtualNetwork -Name TestNet -ResourceGroupName $rgName -Location $locName -AddressPrefix 10.0.0.0/16 -Subnet $frontendSubnet,$backendSubnet

Чтобы вывести список существующих виртуальных сетей, используйте следующие команды:

	$rgName="<resource group name>"
	Get-AzureVirtualNetwork -ResourceGroupName $rgName | Sort Name | Select Name

## Шаг 4. Создание своего набора команд

Откройте новый экземпляр любого текстового редактора или интегрированную среду сценариев PowerShell и скопируйте следующие строки в свой набор команд. Укажите имя группы ресурсов, расположение в Azure и учетную запись хранения для новой виртуальной машины. Замените все содержимое внутри кавычек, включая символы < and >, на правильные имена.

	Switch-AzureMode AzureResourceManager
	$rgName="<resource group name>"
	$locName="<Azure location, such as West US>"
	$saName="<storage account name>"

Необходимо указать имя виртуальной сети на базе диспетчера ресурсов и индекс подсети в виртуальной сети. Чтобы вывести список существующих подсетей в виртуальной сети, используйте следующие команды.

	$rgName="<resource group name>"
	$vnetName="<virtual network name>"
	Get-AzureVirtualNetwork -Name $vnetName -ResourceGroupName $rgName | Select Subnets 

Индекс подсети — это порядковый номер подсети в параметрах команды; подсети нумеруются последовательно слева направо начиная с 0.

В нашем примере:

	PS C:> Get-AzureVirtualNetwork -Name TestNet -ResourceGroupName LOBServers | Select Subnets
	
	Subnets
	-------
	{frontendSubnet, backendSubnet}

Индекс подсети frontendSubnet —0, а подсети backendSubnet — 1.

Скопируйте эти строки в набор команд и укажите имя существующей виртуальной сети и индекс подсети для виртуальной машины.

	$vnetName="<name of an existing virtual network>"
	$subnetIndex=<index of the subnet on which to create the NIC for the virtual machine>
	$vnet=Get-AzurevirtualNetwork -Name $vnetName -ResourceGroupName $rgName

Далее создайте сетевой адаптер (NIC), запросите общедоступный IP-адрес и при необходимости назначьте ему DNS-имя. Скопируйте код из первого или второго варианта в набор команд и введите имя сетевого адаптера и DNS-имя.

Вариант 1. Укажите имя сетевого адаптера.

Скопируйте эти строки в набор команд и укажите имя сетевого адаптера.

	$nicName="<name of the NIC of the VM>"
	$pip = New-AzurePublicIpAddress -Name $nicName -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic
	$nic = New-AzureNetworkInterface -Name $nicName -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[$subnetIndex].Id -PublicIpAddressId $pip.Id

Вариант 2. Укажите имя сетевого адаптера и DNS-имя.

Скопируйте эти строки в набор команд и укажите имя сетевого адаптера, а также глобально уникальное доменное имя. При создании виртуальной машины в режиме управления службами Azure PowerShell Azure выполняет эти действия автоматически.

	$nicName="<name of the NIC of the VM>"
	$domName="<domain name label>"
	$pip = New-AzurePublicIpAddress -Name $nicName -ResourceGroupName $rgName -DomainNameLabel $domName -Location $locName -AllocationMethod Dynamic
	$nic = New-AzureNetworkInterface -Name $nicName -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[$subnetIndex].Id -PublicIpAddressId $pip.Id

Затем создайте локальный объект виртуальной машины и при необходимости добавьте его в группу доступности. Скопируйте код первого или второго варианта в набор команд и введите имя и размер виртуальной машины, а также имя группы доступности.

Вариант 1. Укажите имя и размер виртуальной машины.

	$vmName="<VM name>"
	$vmSize="<VM size string>"
	$vm=New-AzureVMConfig -VMName $vmName -VMSize $vmSize

Чтобы определить возможные значения для строки размера виртуальной машины (вариант 1), используйте следующие команды.

	$locName="<Azure location of your resource group>"
	Get-AzureVMSize -Location $locName | Select Name

Вариант 2. Укажите имя и размер виртуальной машины и добавьте ее в группу доступности.

	$vmName="<VM name>"
	$vmSize="<VM size string>"
	$avName="<availability set name>"
	$avSet=Get-AzureAvailabilitySet –Name $avName –ResourceGroupName $rgName 
	$vm=New-AzureVMConfig -VMName $vmName -VMSize $vmSize -AvailabilitySetId $avset.Id

Чтобы определить возможные значения для строки размера виртуальной машины (вариант 2), используйте следующие команды.

	$rgName="<resource group name>"
	$avName="<availability set name>"
	Get-AzureVMSize -ResourceGroupName $rgName -AvailabilitySetName $avName | Select Name

> [AZURE.NOTE]В настоящее время диспетчер ресурсов позволяет добавлять виртуальные машины в группу доступности только во время их создания.

Чтобы добавить дополнительный диск с данными в виртуальную машину, скопируйте следующие строки в набор команд и задайте параметры диска.

	$diskSize=<size of the disk in GB>
	$diskLabel="<the label on the disk>"
	$diskName="<name identifier for the disk in Azure storage, such as 21050529-DISK02>"
	$storageAcc=Get-AzureStorageAccount -ResourceGroupName $rgName -Name $saName
	$vhdURI=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/" + $vmName + $diskName  + ".vhd"
	Add-AzureVMDataDisk -VM $vm -Name $diskLabel -DiskSizeInGB $diskSize -VhdUri $vhdURI  -CreateOption empty

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
	$vm=Set-AzureVMOperatingSystem -VM $vm -Windows -ComputerName $vmName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
	$vm=Set-AzureVMSourceImage -VM $vm -PublisherName $pubName -Offer $offerName -Skus $skuName -Version "latest"
	$vm=Add-AzureVMNetworkInterface -VM $vm -Id $nic.Id

Затем скопируйте эти команды в набор команд и введите идентификатор (имя) диска операционной системы для виртуальной машины.

	$diskName="<name identifier for the disk in Azure storage, such as OSDisk>"
	$storageAcc=Get-AzureStorageAccount -ResourceGroupName $rgName -Name $saName
	$osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/" + $diskName  + ".vhd"
	$vm=Set-AzureVMOSDisk -VM $vm -Name $diskName -VhdUri $osDiskUri -CreateOption fromImage
	New-AzureVM -ResourceGroupName $rgName -Location $locName -VM $vm

## Шаг 5. Запуск набора команд

Просмотрите в текстовом редакторе созданный набор команд Azure PowerShell, состоящий из нескольких блоков команд из шага 4. Убедитесь, что указаны все необходимые переменные и что они имеют правильные значения. Также убедитесь, что удалены все символы < and >.

Если вы используете текстовый редактор, скопируйте набор команд в буфер обмена и щелкните правой кнопкой мыши в командном окне Azure PowerShell. При этом набор команд выполняется в виде последовательности команд PowerShell, и создается виртуальная машина Azure. Или выполните набор команд в среде Azure PowerShell ISE.

Если вы планируете создавать такие же или аналогичные виртуальные машины снова, можете сохранить набор команд как файл сценария PowerShell (*.ps1).

## Пример

Мне требуется набор команд PowerShell, чтобы создать дополнительную виртуальную машину для обработки коммерческого веб-трафика. ВМ должна:

- входить в существующую группу ресурсов LOBServers;
- использовать образ Windows Server 2012 R2 Datacenter;
- иметь имя LOB07 и входить в существующую группу доступности WEB_AS;
- иметь сетевой адаптер с общедоступным IP-адресом в подсети FrontEnd (индекс подсети — 0) существующей виртуальной сети AZDatacenter;
- иметь дополнительный диск данных объемом 200 ГБ. 

Вот набор команд Azure PowerShell для создания такой виртуальной машины (в соответствии с процессом, описанным для шага 4).

	# Switch to the Resource Manager mode	
	Switch-AzureMode AzureResourceManager
	
	# Set values for existing resource group and storage account names
	$rgName="LOBServers"
	$locName="West US"
	$saName="contosoLOBServersSA"
	
	# Set the existing virtual network and subnet index
	$vnetName="AZDatacenter"
	$subnetIndex=0
	$vnet=Get-AzurevirtualNetwork -Name $vnetName -ResourceGroupName $rgName
	
	# Create the NIC
	$nicName="AzureInterface"
	$domName="contoso-vm-lob07"
	$pip=New-AzurePublicIpAddress -Name $nicName -ResourceGroupName $rgName -DomainNameLabel $domName -Location $locName -AllocationMethod Dynamic
	$nic=New-AzureNetworkInterface -Name $nicName -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[$subnetIndex].Id -PublicIpAddressId $pip.Id
	
	# Specify the name, size, and existing availability set
	$vmName="LOB07"
	$vmSize="Standard_A3"
	$avName="WEB_AS"
	$avSet=Get-AzureAvailabilitySet –Name $avName –ResourceGroupName $rgName
	$vm=New-AzureVMConfig -VMName $vmName -VMSize $vmSize -AvailabilitySetId $avset.Id
	
	# Add a 200 GB additional data disk
	$diskSize=200
	$diskLabel="APPStorage"
	$diskName="21050529-DISK02"
	$storageAcc=Get-AzureStorageAccount -ResourceGroupName $rgName -Name $saName
	$vhdURI=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/" + $vmName + $diskName  + ".vhd"
	Add-AzureVMDataDisk -VM $vm -Name $diskLabel -DiskSizeInGB $diskSize -VhdUri $vhdURI -CreateOption empty
	
	# Specify the image and local administrator account, and then add the NIC
	$pubName="MicrosoftWindowsServer"
	$offerName="WindowsServer"
	$skuName="2012-R2-Datacenter"
	$cred=Get-Credential -Message "Type the name and password of the local administrator account." 
	$vm=Set-AzureVMOperatingSystem -VM $vm -Windows -ComputerName $vmName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
	$vm=Set-AzureVMSourceImage -VM $vm -PublisherName $pubName -Offer $offerName -Skus $skuName -Version "latest"
	$vm=Add-AzureVMNetworkInterface -VM $vm -Id $nic.Id
	
	# Specify the OS disk name and create the VM
	$diskName="OSDisk"
	$storageAcc=Get-AzureStorageAccount -ResourceGroupName $rgName -Name $saName
	$osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/" + $vmName + $diskName  + ".vhd"
	$vm=Set-AzureVMOSDisk -VM $vm -Name $diskName -VhdUri $osDiskUri -CreateOption fromImage
	New-AzureVM -ResourceGroupName $rgName -Location $locName -VM $vm

## Дополнительные ресурсы

[Поставщики вычислительных и сетевых ресурсов, а также ресурсов хранения Azure в диспетчере ресурсов Azure](virtual-machines-azurerm-versus-azuresm.md)

[Общие сведения о диспетчере ресурсов Azure](../resource-group-overview.md)

[Развертывание виртуальных машин Azure и управление ими с использованием шаблонов диспетчера ресурсов и PowerShell](virtual-machines-deploy-rmtemplates-powershell.md)

[Создание виртуальной машины Windows с помощью шаблона диспетчера ресурсов и PowerShell](virtual-machines-create-windows-powershell-resource-manager-template-simple)

[Установка и настройка Azure PowerShell](../install-configure-powershell.md)
 

<!---HONumber=62-->
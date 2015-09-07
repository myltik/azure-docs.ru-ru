<properties
	pageTitle="Создание виртуальной машины Windows с помощью диспетчера ресурсов Azure и Azure PowerShell"
	description="Используйте режим управления ресурсами в Azure PowerShell, чтобы с легкостью создавать виртуальные машины Windows."
	services="virtual-machines"
	documentationCenter=""
	authors="davidmu1"
	manager="timlt"
	editor=""/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/25/2015"
	ms.author="davidmu"/>

# Создание виртуальной машины Windows с помощью диспетчера ресурсов Azure и Azure PowerShell

В этом разделе описывается способ создания виртуальной машины Microsoft Azure и управления ею с помощью диспетчера ресурсов Azure и PowerShell.

[AZURE.INCLUDE [resource-manager-pointer-to-service-management](../../includes/resource-manager-pointer-to-service-management.md)]

- [Создание виртуальной машины Windows с помощью PowerShell и управления службами Azure](virtual-machines-create-windows-powershell-service-manager.md)

## Создание виртуальной машины Windows

Если вы уже установили Azure PowerShell, необходимо использовать Azure PowerShell 0.9.0 или более поздней версии. Установленную версию Azure PowerShell можно узнать в командной строке Azure PowerShell с помощью такой команды:

	Get-Module azure | format-table version

Если вы еще не сделали этого или вам нужно обновить версию установленной Azure PowerShell, следуйте указаниям в разделе [Как установить и настроить Azure PowerShell](install-configure-powershell.md), чтобы установить Azure PowerShell на локальном компьютере. Затем откройте командную строку Azure PowerShell.

Сначала необходимо войти в систему Azure с помощью следующей команды.

	Add-AzureAccount

Укажите адрес электронной почты и пароль своей учетной записи Azure в диалоговом окне входа в Microsoft Azure.

Далее, если у вас есть несколько подписок Azure, необходимо задать лишь одну из них. Чтобы просмотреть список текущих подписок, выполните следующую команду.

	Get-AzureSubscription | sort SubscriptionName | Select SubscriptionName

Теперь замените все содержимое в кавычках, в том числе символы < and >, правильным именем подписки и выполните следующие команды.

	$subscrName="<subscription name>"
	Select-AzureSubscription -SubscriptionName $subscrName –Current

Далее необходимо создать учетную запись хранения. Вам необходимо выбрать уникальное имя, в котором будут содержаться только строчные буквы и цифры. Можно проверить уникальность имени учетной записи хранения с помощью этой команды.

	Test-AzureName -Storage <Proposed storage account name>

Если команда возвращает значение «False»,имя является уникальным.

Вам нужно будет указать расположение центра обработки данных Azure. Чтобы получить список центров обработки данных Azure, выполните следующую команду.

	Get-AzureLocation | sort Name | Select Name

Затем вам необходимо переключить режим Azure PowerShell на режим диспетчера ресурсов. Выполните такую команду:

	Switch-AzureMode AzureResourceManager

Теперь скопируйте следующий блок команд PowerShell в текстовый редактор. Введите выбранную учетную запись хранения и расположение, заменив все содержимое в кавычках, в том числе символы < and >.

	$stName="<chosen storage account name>"
	$locName="<chosen Azure location name>"
	$rgName="TestRG"
	New-AzureResourceGroup -Name $rgName -Location $locName
	$storageAcc=New-AzureStorageAccount -ResourceGroupName $rgName -Name $stName -Type "Standard_GRS" -Location $locName
	$singleSubnet=New-AzureVirtualNetworkSubnetConfig -Name singleSubnet -AddressPrefix 10.0.0.0/24
	$vnet=New-AzurevirtualNetwork -Name TestNet -ResourceGroupName $rgName -Location $locName -AddressPrefix 10.0.0.0/16 -Subnet $singleSubnet
	$pip = New-AzurePublicIpAddress -Name TestPIP -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic
	$nic = New-AzureNetworkInterface -Name TestNIC -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id
	$cred = Get-Credential -Message "Type the name and password of the local administrator account."
	$vm = New-AzureVMConfig -VMName WindowsVM -VMSize "Standard_A1"
	$vm = Set-AzureVMOperatingSystem -VM $vm -Windows -ComputerName MyWindowsVM -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
	$vm = Set-AzureVMSourceImage -VM $vm -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
	$vm = Add-AzureVMNetworkInterface -VM $vm -Id $nic.Id
	$osDiskUri = $storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/WindowsVMosDisk.vhd"
	$vm = Set-AzureVMOSDisk -VM $vm -Name "windowsvmosdisk" -VhdUri $osDiskUri -CreateOption fromImage
	New-AzureVM -ResourceGroupName $rgName -Location $locName -VM $vm

Наконец, скопируйте набор команд в буфер обмена и щелкните правой кнопкой мыши открытую командную строку Azure PowerShell. Это позволит вызвать набор команд в виде последовательности команд PowerShell. Затем вы увидите запрос на ввод имени и пароля учетной записи локального администратора, после чего будет создана виртуальная машина Azure.

Должно отобразиться примерно следующее:

	PS C:\> $stName="contosost"
	PS C:\> $locName="West US"
	PS C:\> $rgName="TestRG"
	PS C:\> New-AzureResourceGroup -Name $rgName -Location $locName
	VERBOSE: 12:45:15 PM - Created resource group 'TestRG' in location 'westus'


	ResourceGroupName : TestRG
	Location          : westus
	ProvisioningState : Succeeded
	Tags              :
	Permissions       :
	                    Actions  NotActions
	                    =======  ==========
	                    *

	ResourceId        : /subscriptions/fd92919d-eeca-4f5b-840a-e45c6770d92e/resourceGroups/TestRG


	PS C:\> $storageAcc=New-AzureStorageAccount -ResourceGroupName $rgName -Name $stName -Type "Standard_GRS" -Location $locName
	PS C:\> $singleSubnet=New-AzureVirtualNetworkSubnetConfig -Name singleSubnet -AddressPrefix 10.0.0.0/24
	PS C:\> $vnet=New-AzurevirtualNetwork -Name TestNet3 -ResourceGroupName $rgName -Location $locName -AddressPrefix 10.0.0.0/16 -Subnet $singleSubnet
	PS C:\> $pip = New-AzurePublicIpAddress -Name TestNIC -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic
	PS C:\> $nic = New-AzureNetworkInterface -Name TestNIC -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id
	PS C:\> $cred = Get-Credential -Message "Type the name and password of the local administrator account."
	PS C:\> $vm = New-AzureVMConfig -VMName WindowsVM -VMSize "Standard_A1"
	PS C:\> $vm = Set-AzureVMOperatingSystem -VM $vm -Windows -ComputerName MyWindowsVM -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
	PS C:\> $vm = Set-AzureVMSourceImage -VM $vm -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
	PS C:\> $vm = Add-AzureVMNetworkInterface -VM $vm -Id $nic.Id
	PS C:\> $osDiskUri = $storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/MyWindowsVMosDisk.vhd"
	PS C:\> $vm = Set-AzureVMOSDisk -VM $vm -Name "windowsvmosdisk" -VhdUri $osDiskUri -CreateOption fromImage
	PS C:\> New-AzureVM -ResourceGroupName $rgName -Location $locName -VM $vm


	EndTime             : 4/28/2015 1:00:05 PM -07:00
	Error               :
	Output              :
	StartTime           : 4/28/2015 12:52:52 PM -07:00
	Status              : Succeeded
	TrackingOperationId : 45035a90-ea12-4e1e-87e7-2a5e9ed12c93
	RequestId           : 98c7b4fb-b26e-4a58-b17a-b0983d896aae
	StatusCode          : OK

## Дополнительные ресурсы

[Поставщики вычислительных и сетевых ресурсов, а также ресурсов хранения Azure в диспетчере ресурсов Azure](virtual-machines-azurerm-versus-azuresm.md)

[Обзор диспетчера ресурсов Azure](resource-group-overview.md)

[Создание виртуальной машины Windows с помощью шаблона диспетчера ресурсов и PowerShell](virtual-machines-create-windows-powershell-resource-manager-template-simple.md)

[Создание виртуальной машины Windows с помощью PowerShell и управления службами Azure](virtual-machines-create-windows-powershell-service-manager.md)

[Документация по виртуальным машинам](http://azure.microsoft.com/documentation/services/virtual-machines/)

[Установка и настройка Azure PowerShell](install-configure-powershell.md)

<!---HONumber=August15_HO9-->
<properties 
	pageTitle="Создание виртуальной машины Windows с помощью PowerShell и диспетчера ресурсов Azure" 
	description="Используйте Azure PowerShell для быстрого создания новой виртуальной машины Windows." 
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
	ms.date="04/29/2015" 
	ms.author="josephd"/>

# Создание виртуальной машины Windows с помощью PowerShell и диспетчера ресурсов Azure

Если вы уже установили Azure PowerShell, необходимо использовать Azure PowerShell 0.8.0 или более поздней версии. Установленную версию Azure PowerShell можно узнать в командной строке Azure PowerShell с помощью такой команды:

	Get-Module azure | format-table version

Если вы еще не сделали этого, следуйте указаниям в разделе [Как установить и настроить Azure PowerShell](install-configure-powershell.md), чтобы установить Azure PowerShell на локальном компьютере. Затем откройте командную строку Azure PowerShell.

Сначала необходимо войти в систему Azure с помощью следующей команды.

	Add-AzureAccount

Укажите адрес электронной почты и пароль своей учетной записи Azure в диалоговом окне входа в Microsoft Azure.

Далее, если у вас есть несколько подписок Azure, необходимо задать лишь одну из них. Чтобы просмотреть список текущих подписок, выполните следующую команду.

	Get-AzureSubscription | sort SubscriptionName | Select SubscriptionName

Теперь замените все содержимое в кавычках, в том числе символы < and >, правильным именем подписки и выполните следующие команды.

	$subscrName="<subscription name>"
	Select-AzureSubscription -SubscriptionName $subscrName –Current

Далее необходимо указать учетную запись хранения. Вы можете отобразить текущий список учетных записей, выполнив следующую команду.

	Get-AzureStorageAccount | sort Label | Select Label

Если у вас еще нет учетной записи хранения, создайте ее. Вам необходимо выбрать уникальное имя, в котором будут содержаться только строчные буквы и цифры. Можно проверить уникальность имени учетной записи хранения с помощью этой команды.

	Test-AzureName -Storage <Proposed storage account name>

Если команда возвращает значение «False»,имя является уникальным. При создании учетной записи хранения вам нужно будет указать расположение центра обработки данных Azure. Чтобы получить список центров обработки данных Azure, выполните следующую команду.

	Get-AzureLocation | sort Name | Select Name

Теперь создайте и настройте учетную запись хранения с помощью этих команд. Укажите имя учетной записи хранения и замените все содержимое в кавычках, в том числе символы < and >.

	$stAccount="<chosen storage account name>"
	$locName="<Azure location>"
	New-AzureStorageAccount -StorageAccountName $stAccount -Location $locName
	Set-AzureStorageAccount -StorageAccountName $stAccount
	Set-AzureSubscription -SubscriptionName $subscrName -CurrentStorageAccountName $stAccount

Далее необходимо указать облачную службу. Если у вас нет облачной службы, ее необходимо создать. Вам необходимо выбрать уникальное имя, в котором будут содержаться только буквы, цифры и дефисы. Первый и последний символ в поле должны быть буквами или цифрами.

Например, службу можно назвать TestCS-\*UniqueSequence\*, где *UniqueSequence* — это сокращенное название вашей организации. К примеру, если ваша организация называется Tailspin Toys, можно назвать облачную службу TestCS-Tailspin.

Уникальность имени можно проверить, выполнив следующую команду Azure PowerShell.

	Test-AzureName -Service <Proposed cloud service name>

Если команда возвращает значение «False»,имя является уникальным. Создайте облачную службу с помощью этой команды.

	$csName="<cloud service name>"
	$locName="<Azure location>"
	New-AzureService -Service $csName -Location $locName

Теперь скопируйте следующий блок команд PowerShell в текстовый редактор, например Блокнот.

	$vmName="<machine name>"
	$csName="<cloud service name>"
	$locName="<Azure location>"
	$vm=New-AzureVMConfig -Name $vmName -InstanceSize Medium -ImageName "a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-R2-201503.01-en.us-127GB.vhd"
	$cred=Get-Credential -Message "Type the name and password of the local administrator account."
	$vm | Add-AzureProvisioningConfig -Windows -AdminUsername $cred.GetNetworkCredential().Username -Password $cred.GetNetworkCredential().Password
	New-AzureVM –ServiceName $csName –Location $locName -VMs $vm

Введите в нем имя виртуальной машины, имя облачной службы и расположение.

Наконец, скопируйте набор команд в буфер обмена и щелкните правой кнопкой мыши открытую командную строку Azure PowerShell. Это позволит вызвать набор команд в виде последовательности команд PowerShell. Затем вы увидите запрос на ввод имени и пароля учетной записи локального администратора, после чего будет создана виртуальная машина Azure. Вот пример того, как выглядит набор выполняющихся команд.

	PS C:\> $vmName="PSTest"
	PS C:\> $csName=" TestCS-Tailspin"
	PS C:\> $locName="West US"
	PS C:\> $vm=New-AzureVMConfig -Name $vmName -InstanceSize Medium -ImageName "a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-R2-201503.01-en.us-127GB.vhd"
	PS C:\> $cred=Get-Credential -Message "Type the name and password of the local administrator account."
	PS C:\> $vm | Add-AzureProvisioningConfig -Windows -AdminUsername $cred.GetNetworkCredential().Username -Password $cred.
	GetNetworkCredential().Password

	
	AvailabilitySetName               :
	ConfigurationSets                 : PSTest,Microsoft.WindowsAzure.Commands.ServiceManagement.Model.NetworkConfigurationSet}
	DataVirtualHardDisks              : {}
	Label                             : PSTest
	OSVirtualHardDisk                 : Microsoft.WindowsAzure.Commands.ServiceManagement.Model.OSVirtualHardDisk
	RoleName                          : PSTest
	RoleSize                          : Medium
	RoleType                          : PersistentVMRole
	WinRMCertificate                  :
	X509Certificates                  : {}
	NoExportPrivateKey                : False
	NoRDPEndpoint                     : False
	NoSSHEndpoint                     : False
	DefaultWinRmCertificateThumbprint :
	ProvisionGuestAgent               : True
	ResourceExtensionReferences       : {BGInfo}
	DataVirtualHardDisksToBeDeleted   :
	VMImageInput                      :
	
	PS C:\> New-AzureVM -ServiceName $csName -Location $locName -VMs $vm
	VERBOSE: 3:01:46 PM - Begin Operation: New-AzureVM - Create Deployment with VM PSTest
	VERBOSE: 3:02:49 PM - Completed Operation: New-AzureVM - Create Deployment with VM PSTest
	
	OperationDescription                    OperationId                            OperationStatus
	--------------------                    -----------                            --------------
	New-AzureVM                             8072cbd1-4abe-9278-9de2-8826b56e9221   Succeeded
	

## Дополнительные ресурсы

[Создание виртуальной машины Windows с помощью диспетчера ресурсов Azure и PowerShell](virtual-machines-create-windows-powershell-rm.md)

[Создание виртуальной машины Windows с помощью шаблона диспетчера ресурсов и PowerShell](virtual-machines-create-windows-powershell-rm-template-simple.md)

[Документация по виртуальным машинам](http://azure.microsoft.com/documentation/services/virtual-machines/)

[Как установить и настроить Azure PowerShell](install-configure-powershell.md)

[Использование Azure PowerShell для создания и предварительной настройки виртуальных машин под управлением Windows](virtual-machines-ps-create-preconfigure-windows-vms.md)


<!--HONumber=52-->

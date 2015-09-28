<properties
	pageTitle="Создание виртуальной машины Windows и управление ею с помощью компонента управления службами и Azure PowerShell"
	description="Используйте Azure PowerShell для быстрого создания виртуальной машины под управлением Windows в компонента управления службами и применения функций управления."
	services="virtual-machines"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/09/2015"
	ms.author="cynthn"/>

# Создание виртуальной машины Windows и управление ею с помощью компонента управления службами и Azure PowerShell

В этой статье показано, как создать виртуальную машину Windows и управлять ею с помощью управления службами и Azure PowerShell.

[AZURE.INCLUDE [service-management-pointer-to-resource-manager](../../includes/service-management-pointer-to-resource-manager.md)]

- [Развертывание виртуальных машин и управление ими с использованием шаблонов диспетчера ресурсов Azure и PowerShell](virtual-machines-deploy-rmtemplates-powershell.md)

## Настройка Azure PowerShell

Если вы уже установили Azure PowerShell, необходимо использовать Azure PowerShell 0.8.0 или более поздней версии. Установленную версию Azure PowerShell можно узнать в командной строке Azure PowerShell с помощью такой команды:

	Get-Module azure | format-table version

Если вы еще не сделали этого, следуйте указаниям в разделе [Как установить и настроить Azure PowerShell](../install-configure-powershell.md), чтобы установить Azure PowerShell на локальном компьютере. После этого откройте командную строку Azure PowerShell.

Сначала необходимо войти в Azure с помощью этой команды:

	Add-AzureAccount

Укажите адрес электронной почты и пароль своей учетной записи Azure в диалоговом окне входа в Microsoft Azure.

Далее, если у вас есть несколько подписок Azure, необходимо задать лишь одну из них. Чтобы просмотреть список текущих подписок, выполните следующую команду:

	Get-AzureSubscription | sort SubscriptionName | Select SubscriptionName

Теперь замените все содержимое в кавычках, в том числе символы < and >, правильным именем подписки и выполните следующие команды:

	$subscrName="<subscription name>"
	Select-AzureSubscription -SubscriptionName $subscrName –Current

## Создание виртуальной машины

Сначала необходимо указать учетную запись хранения. Вы можете отобразить текущий список учетных записей, выполнив следующую команду:

	Get-AzureStorageAccount | sort Label | Select Label

Если у вас еще нет учетной записи хранения, создайте ее. Вам необходимо выбрать уникальное имя, в котором будут содержаться только строчные буквы и цифры. Проверить уникальность имени учетной записи хранения можно с помощью этой команды:

	Test-AzureName -Storage <Proposed Storage account name>

Если команда возвращает значение «False»,имя является уникальным.

При создании учетной записи хранения вам нужно будет указать расположение центра обработки данных Azure. Чтобы получить список центров обработки данных Azure, выполните следующую команду:

	Get-AzureLocation | sort Name | Select Name

Теперь создайте и настройте учетную запись хранения с помощью следующих команд. Укажите имя учетной записи хранения и замените все содержимое в кавычках, в том числе символы < and >.

	$stAccount="<chosen Storage account name>"
	$locName="<Azure location>"
	New-AzureStorageAccount -StorageAccountName $stAccount -Location $locName
	Set-AzureStorageAccount -StorageAccountName $stAccount
	Set-AzureSubscription -SubscriptionName $subscrName -CurrentStorageAccountName $stAccount

Далее необходимо указать облачную службу. Если у вас нет облачной службы, ее необходимо создать. Вам необходимо выбрать уникальное имя, в котором будут содержаться только буквы, цифры и дефисы. Первый и последний символ в поле должны быть буквами или цифрами.

Например, службу можно назвать TestCS-*UniqueSequence*, где *UniqueSequence* — это сокращенное название вашей организации. К примеру, если ваша организация называется Tailspin Toys, можно назвать облачную службу TestCS-Tailspin.

Уникальность имени можно проверить, выполнив следующую команду Azure PowerShell.

	Test-AzureName -Service <Proposed cloud service name>

Если команда возвращает значение «False»,имя является уникальным. Создайте облачную службу с помощью следующих команд:

	$csName="<cloud service name>"
	$locName="<Azure location>"
	New-AzureService -Service $csName -Location $locName

Теперь скопируйте следующий блок команд Azure PowerShell в текстовый редактор, например Блокнот:

	$vmName="<machine name>"
	$csName="<cloud service name>"
	$locName="<Azure location>"
	$image=Get-AzureVMImage | where { $_.ImageFamily -eq "Windows Server 2012 R2 Datacenter" } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1
	$vm=New-AzureVMConfig -Name $vmName -InstanceSize Medium -ImageName $image
	$cred=Get-Credential -Message "Type the name and password of the local administrator account."
	$vm | Add-AzureProvisioningConfig -Windows -AdminUsername $cred.GetNetworkCredential().Username -Password $cred.GetNetworkCredential().Password
	New-AzureVM –ServiceName $csName –Location $locName -VMs $vm

Введите в нем имя виртуальной машины, имя облачной службы и расположение.

Наконец, скопируйте набор команд в буфер обмена и щелкните правой кнопкой мыши открытую командную строку Azure PowerShell. Это позволит вызвать набор команд в виде последовательности команд Azure PowerShell. Затем вы увидите запрос на ввод имени и пароля учетной записи локального администратора, после чего будет создана виртуальная машина Azure. Вот пример того, как выглядит набор выполняющихся команд.

	PS C:\> $vmName="PSTest"
	PS C:\> $csName=" TestCS-Tailspin"
	PS C:\> $locName="West US"
	PS C:\> $image=Get-AzureVMImage | where { $_.ImageFamily -eq "Windows Server 2012 R2 Datacenter" } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1
	VERBOSE: 3:01:17 PM - Begin Operation: Get-AzureVMImage
	VERBOSE: 3:01:22 PM - Completed Operation: Get-AzureVMImage
	VERBOSE: 3:01:22 PM - Begin Operation: Get-AzureVMImage
	VERBOSE: 3:01:23 PM - Completed Operation: Get-AzureVMImage
	PS C:\> $vm=New-AzureVMConfig -Name $vmName -InstanceSize Medium -ImageName $image
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

## Отображение информации о виртуальной машине
Вы будете выполнять эту основную задачу очень часто. Используйте ее, чтобы получить информацию о виртуальной машине, выполнить задачи в виртуальной машине или получить выходные данные, которые необходимо сохранить в переменной.

Чтобы получить информацию о виртуальной машине, выполните эту команду и замените все, что заключено в кавычки, включая символы < and >:

     Get-AzureVM -ServiceName "<cloud service name>" -Name "<virtual machine name>"

Чтобы сохранить выходные данные в переменной $vm, выполните:

    $vm = Get-AzureVM -ServiceName "<cloud service name>" -Name "<virtual machine name>"

## Вход в виртуальную машину под управлением Windows

Выполните следующие команды:

	$svcName="<cloud service name>"
	$vmName="<virtual machine name>"
	$localPath="<drive and folder location to store the downloaded RDP file, example: c:\temp >"
	$localFile=$localPath + "" + $vmname + ".rdp"
	Get-AzureRemoteDesktopFile -ServiceName $svcName -Name $vmName -LocalPath $localFile -Launch

>[AZURE.NOTE]Имя виртуальной машины и облачной службы можно получить из вывода команды **Get-AzureVM**.

## Остановка виртуальной машины

Выполните следующую команду:

    Stop-AzureVM -ServiceName "<cloud service name>" -Name "<virtual machine name>"

>[AZURE.IMPORTANT]Используйте параметр **StayProvisioned**, чтобы сохранить виртуальный IP-адрес (VIP) облачной службы, если эта виртуальная машина является последней в этой службе. При использовании этого параметра вам по-прежнему будут выставлять счета за использование виртуальной машины.

## Запуск виртуальной машины

Выполните следующую команду:

    Start-AzureVM -ServiceName "<cloud service name>" -Name "<virtual machine name>"

## Присоединение диска данных
Для этой задачи требуется несколько шагов. Сначала используйте командлет **Add-AzureDataDisk**, чтобы добавить диск в объект $vm. Затем используйте командлет Update-AzureVM, чтобы обновить конфигурацию виртуальной машины.

Вам необходимо решить, какой диск подключать — новый диск или диск с данными. При подключении нового диска команда создает VHD-файл и присоединяет его.

Чтобы подключить новый диск, выполните следующую команду:

    Add-AzureDataDisk -CreateNew -DiskSizeInGB <disk size> -DiskLabel "<label name>" -LUN <LUN number> -VM $vm | Update-AzureVM

Чтобы подключить существующий диск, выполните следующую команду:

    Add-AzureDataDisk -Import -DiskName "<existing disk name>" -LUN <LUN number> | Update-AzureVM

Чтобы подключить диски данных из существующего VHD-файла в хранилище больших двоичных объектов, выполните следующую команду:

    $diskLoc="https://mystorage.blob.core.windows.net/mycontainer/" + "<existing disk name>" + ".vhd"
	Add-AzureDataDisk -ImportFrom -MediaLocation  $diskLoc -DiskLabel "<label name>" -LUN <LUN number> | Update-AzureVM

## Дополнительные ресурсы

[Создание виртуальной машины Windows с помощью диспетчера ресурсов и Azure PowerShell](virtual-machines-create-windows-powershell-resource-manager.md)

[Создание виртуальной машины Windows с использованием шаблона диспетчера ресурсов и Azure PowerShell](virtual-machines-create-windows-powershell-resource-manager-template-simple.md)

[Документация по виртуальным машинам](http://azure.microsoft.com/documentation/services/virtual-machines/)

[Как установить и настроить Azure PowerShell](../install-configure-powershell.md)

[Использование Azure PowerShell для создания и предварительной настройки виртуальных машин под управлением Windows](virtual-machines-ps-create-preconfigure-windows-vms.md)

<!---HONumber=Sept15_HO3-->
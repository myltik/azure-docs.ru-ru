.<properties
	pageTitle="Создание образа виртуальной машины на основе виртуальной машины Azure | Microsoft Azure"
	description="Научитесь создавать универсальный образ виртуальной машины на основе существующей виртуальной машины Azure, созданной с помощью модели развертывания Resource Manager"
	services="virtual-machines-windows"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/04/2016"
	ms.author="cynthn"/>

# Создание образа виртуальной машины на основе существующей виртуальной машины Azure


В этой статье показано, как использовать Azure PowerShell для создания универсального образа существующей виртуальной машины Azure. Такой образ можно затем использовать для создания другой виртуальной машины. Данный образ виртуальной машины включает в себя диск операционной системы и другие диски данных, присоединенные к виртуальной машине. Образ не включает ресурсы виртуальной сети, поэтому их придется настроить заново при создании виртуальной машины с помощью этого образа. Этот процесс создает [универсальный образ Windows](https://technet.microsoft.com/library/hh824938.aspx).


## Предварительные требования

- Предполагается, что у вас уже есть виртуальная машина Azure с моделью развертывания Resource Manager, которую вы хотите использовать для создания образа. Необходимо указать имя виртуальной машины и имя группы ресурсов. Список групп ресурсов в подписке можно получить, выполнив командлет PowerShell `Get-AzureRmResourceGroup`. Список виртуальных машин в подписке можно получить, введя `Get-AzureRMVM`.

- У вас должна быть установлена среда Azure PowerShell версии 1.0 или выше. Если вы еще не установили PowerShell, см. шаги установки в статье [Установка и настройка Azure PowerShell](../powershell-install-configure.md).

- Убедитесь, что Sysprep поддерживает роли сервера, запущенные на компьютере. Дополнительные сведения см. в разделе [Sysprep Support for Server Roles](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles) (Поддержка ролей сервера в Sysprep).

## Подготовка исходной виртуальной машины 

В этом разделе вы узнаете, как обобщить виртуальную машину Windows, чтобы использовать ее для создания образа. Перед выполнением программы Sysprep выполните архивацию виртуальной машины.

> [AZURE.WARNING] После обобщения виртуальной машины вы не сможете войти в нее через протокол RDP, так как этот процесс удаляет все учетные записи пользователей. Эти изменения необратимы.

1. Выполните вход в виртуальную машину Windows. На [портале Azure](https://portal.azure.com) последовательно выберите **Обзор** > **Виртуальные машины** > имя нужной виртуальной машины Windows > **Подключить**.

2. Откройте окно командной строки с правами администратора.

3. Измените каталог на `%windir%\system32\sysprep` и запустите файл sysprep.exe.

4. В диалоговом окне **Программа подготовки системы** сделайте следующее:

	- В разделе **Действие по очистке системы** выберите **Запуск при первом включении компьютера (OOBE)** и убедитесь, что установлен флажок **Обобщить**. Дополнительную информацию об использовании Sysprep см. в разделе [Как использовать Sysprep: введение](http://technet.microsoft.com/library/bb457073.aspx).

	- В разделе **Параметры завершения работы** выберите **Завершение работы**.

	- Нажмите кнопку **ОК**.

	![Запуск Sysprep](./media/virtual-machines-windows-capture-image/SysprepGeneral.png)

   Sysprep завершит работу виртуальной машины. Ее состояние изменится на **Остановлена** на портале Azure.


## Вход в Azure PowerShell

1. Откройте Azure PowerShell и войдите в свою учетную запись Azure.

		Login-AzureRmAccount

	Откроется всплывающее окно для ввода данных учетной записи Azure.

2. Получите идентификаторы доступных вам подписок.

		Get-AzureRmSubscription

3. Укажите соответствующую подписку с помощью идентификатора.

		Select-AzureRmSubscription -SubscriptionId "<subscriptionID>"


## Отмена распределения для виртуальной машины и установка состояния "универсальный"		

1. Отмените распределение ресурсов для виртуальной машины.

		Stop-AzureRmVM -ResourceGroupName <resourceGroup> -Name <vmName>

	Вы увидите, что *состояние* виртуальной машины на портале Azure изменился с **Остановлено** на **Остановлено (освобождено)**.

2. Теперь задайте для виртуальной машины состояние **Generalized** (Универсальная).

		Set-AzureRmVm -ResourceGroupName <resourceGroup> -Name <vmName> -Generalized

3. Проверьте состояние виртуальной машины. В разделе **OSState/generalized** для этой виртуальной машины параметр **DisplayStatus** должен иметь значение **VM generalized**.
		
		$vm = Get-AzureRmVM -ResourceGroupName <resourceGroup> -Name <vmName> -status
		$vm.Statuses

		
## Создание образа 

1. С помощью этой команды скопируйте образ виртуальной машины в целевой контейнер хранилища. Образ будет создан в той же учетной записи хранения, что и исходная виртуальная машина. Переменная `-Path` сохраняет локальную копию шаблона JSON. Переменная `-DestinationContainerName` содержит имя контейнера для хранения образов. Если такой контейнер не существует, он будет создан автоматически.

		Save-AzureRmVMImage -ResourceGroupName YourResourceGroup -Name YourWindowsVM -DestinationContainerName YourImagesContainer -VHDNamePrefix YourTemplatePrefix -Path Yourlocalfilepath\Filename.json

	URL-адрес образа можно получить из шаблона файла JSON. Перейдите к разделу **resources** > **storageProfile** > **osDisk** > **image** > **uri**, чтобы получить полный путь к образу. URL-адрес образа выглядит так: `https://<storageAccountName>.blob.core.windows.net/system/Microsoft.Compute/Images/<imagesContainer>/<templatePrefix-osDisk>.xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx.vhd`.
	
	Также вы также можете проверить URI на портале. Образ копируется в контейнер **system** в вашей учетной записи хранения.

2. Создайте переменную для пути к образу.

		$imageURI = "<https://<storageAccountName>.blob.core.windows.net/system/Microsoft.Compute/Images/<imagesContainer>/<templatePrefix-osDisk>.xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx.vhd>"


## Создать виртуальную сеть

Создайте виртуальную сеть и подсеть [виртуальной сети](../virtual-network/virtual-networks-overview.md).
		

1. Замените значения переменных собственными данными. Укажите префикс адреса для подсети в формате CIDR. Создайте переменные и подсеть.

    	$rgName = "<resourceGroup>"
		$location = "<location>"
        $subnetName = "<subNetName>"
        $singleSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix <0.0.0.0/0>
        
2. Замените значение **$vnetName** именем виртуальной сети. Укажите префикс адреса для виртуальной сети в формате CIDR. Создайте переменную и виртуальную сеть с подсетью.

        $vnetName = "<vnetName>"
        $vnet = New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $locName -AddressPrefix <0.0.0.0/0> -Subnet $singleSubnet
        
            
## Создание общедоступного IP-адреса и сетевого интерфейса

Чтобы обеспечить обмен данными с виртуальной машиной в виртуальной сети, требуются [общедоступный IP-адрес](../virtual-network/virtual-network-ip-addresses-overview-arm.md) и сетевой интерфейс.

1. Замените значение **$ipName** именем общедоступного IP-адреса. Создайте переменную и общедоступный IP-адрес.

        $ipName = "<ipName>"
        $pip = New-AzureRmPublicIpAddress -Name $ipName -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic
        
2. Замените значение **$nicName** именем сетевого интерфейса. Создайте переменную и сетевой интерфейс.

        $nicName = "<nicName>"
        $nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id


## Создание виртуальной машины

Следующий сценарий PowerShell предназначен для настройки конфигураций виртуальных машин и использования переданного образа виртуальной машины в качестве источника новой установки.

>[AZURE.NOTE] Виртуальная машина должна находиться в той же учетной записи хранения, что и исходный виртуальный жесткий диск.

.</br>

	
	
	
	#Create variables
	# Enter a new user name and password to use as the local administrator account for the remotely accessing the VM
	$cred = Get-Credential
	
	# Name of the storage account 
	$storageAccName = "<storageAccountName>"
	
	# Name of the virtual machine
	$vmName = "<vmName>"
	
	# Size of the virtual machine. See the VM sizes documentation for more information: https://azure.microsoft.com/documentation/articles/virtual-machines-windows-sizes/
	$vmSize = "<vmSize>"
	
	# Computer name for the VM
	$computerName = "<computerName>"
	
	# Name of the disk that holds the OS
	$osDiskName = "<osDiskName>"
	
	# Assign a SKU name
	# Valid values for -SkuName are: **Standard_LRS** - locally redundant storage, **Standard_ZRS** - zone redundant storage, **Standard_GRS** - geo redundant storage, **Standard_RAGRS** - read access geo redundant storage, **Premium_LRS** - premium locally redundant storage. 
	$skuName = "<skuName>"
	
	# Create a new storage account for the VM
	New-AzureRmStorageAccount -ResourceGroupName $rgName -Name $storageAccName -Location $location -SkuName $skuName -Kind "Storage"

	#Get the storage account where the uploaded image is stored
	$storageAcc = Get-AzureRmStorageAccount -ResourceGroupName $rgName -AccountName $storageAccName

	#Set the VM name and size
	#Use "Get-Help New-AzureRmVMConfig" to know the available options for -VMsize
	$vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize $vmSize

	#Set the Windows operating system configuration and add the NIC
	$vm = Set-AzureRmVMOperatingSystem -VM $vmConfig -Windows -ComputerName $computerName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate

	$vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id

	#Create the OS disk URI
	$osDiskUri = '{0}vhds/{1}-{2}.vhd' -f $storageAcc.PrimaryEndpoints.Blob.ToString(), $vmName.ToLower(), $osDiskName

	#Configure the OS disk to be created from the image (-CreateOption fromImage), and give the URL of the uploaded image VHD for the -SourceImageUri parameter
	#You set this variable when you uploaded the VHD
	$vm = Set-AzureRmVMOSDisk -VM $vm -Name $osDiskName -VhdUri $osDiskUri -CreateOption fromImage -SourceImageUri $imageURI -Windows

	#Create the new VM
	New-AzureRmVM -ResourceGroupName $rgName -Location $location -VM $vm



После завершения процесса новая виртуальная машина должна отображаться на [портале Azure](https://portal.azure.com) (выберите **Обзор** > **Виртуальные машины**). Ее можно также отобразить, выполнив следующие команды PowerShell.

	$vmList = Get-AzureRmVM -ResourceGroupName $rgName
	$vmList.Name


## Дальнейшие действия

Сведения об управлении созданной виртуальной машиной с помощью Azure PowerShell см. в статье [Управление виртуальными машинами Azure с помощью Resource Manager и PowerShell](virtual-machines-windows-ps-manage.md).

<!---HONumber=AcomDC_0831_2016-->
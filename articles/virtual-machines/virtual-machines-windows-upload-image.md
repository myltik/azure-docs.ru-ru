.<properties
	pageTitle="Отправка Windows VHD для диспетчера ресурсов | Microsoft Azure"
	description="Узнайте, как отправить образ виртуальной машины Windows для использования в модели развертывания Resource Manager."
	services="virtual-machines-windows"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor="tysonn"
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/02/2016"
	ms.author="cynthn"/>

# Отправка образа виртуальной машины Windows в Azure для развертываний Resource Manager


В этой статье показано, как создать и передать образ виртуального жесткого диска (VHD-файл) Windows, чтобы иметь возможность быстро создавать виртуальные машины. Дополнительные сведения о дисках и виртуальных жестких дисках в Azure см. в статье [О дисках и виртуальных жестких дисках для виртуальных машин Azure](virtual-machines-linux-about-disks-vhds.md).


## Предварительные требования

В этой статье предполагается, что вы:

- Располагаете **подпиской Azure**. Если у вас еще нет подписки Azure, вы можете [создать учетную запись Azure бесплатно](/pricing/free-trial/?WT.mc_id=A261C142F) или [активировать преимущества для подписчиков MSDN](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F).

- Установили **Azure PowerShell версии 1.4 или выше** — если это программное обеспечение еще не установлено, см. статью [Установка и настройка Azure PowerShell](../powershell-install-configure.md).

- Располагаете **виртуальной машиной под управлением ОС Windows** — существует множество средств для создания виртуальных машин в локальной среде. Например, см. документацию по [установке роли Hyper-V и настройке виртуальной машины](http://technet.microsoft.com/library/hh846766.aspx). Сведения о операционных системах Windows, поддерживаемых в Azure, см. в статье [Microsoft server software support for Microsoft Azure virtual machines](https://support.microsoft.com/kb/2721672) (Поддержка серверного ПО Майкрософт для виртуальных машин Microsoft Azure).

- Убедитесь, что роли сервера, запущенные на виртуальной машине, совместимы с Sysprep. Дополнительные сведения см. в разделе [Sysprep Support for Server Roles](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles) (Поддержка ролей сервера в Sysprep). Перед выполнением программы Sysprep выполните архивацию виртуальной машины.


## Проверка соответствия формата файла виртуальной машины

В Azure можно использовать только [виртуальные машины 1-го поколения](http://blogs.technet.com/b/ausoemteam/archive/2015/04/21/deciding-when-to-use-generation-1-or-generation-2-virtual-machines-with-hyper-v.aspx), использующие формат файла VHD. Размер VHD-файла должен быть фиксированным и составлять целое число мегабайтов, т. е. число, кратное 8. Максимально допустимый размер виртуального жесткого диска составляет 1023 ГБ.

- Если у вас есть образ виртуальной Машины Windows в формате VHDX, преобразуйте его в VHD с помощью одного из следующих средств.

	- Hyper-V: откройте Hyper-V и выберите локальный компьютер в левой части окна. Затем в меню, которое располагается над ним, выберите **Действие** > **Изменить диск...**. Перемещайтесь по экранам, нажимая кнопку **Далее** и вводя следующие параметры: *Path for your VHDX file* (Путь к VHDX-файлу) > **Преобразовать** > **VHD** > **Фиксированный размер** > *Path for the new VHD file* (Путь к новому VHD-файлу). Нажмите кнопку **Готово**, чтобы закрыть окно.

	- [Командлет PowerShell Convert-VHD](http://technet.microsoft.com/library/hh848454.aspx): дополнительные сведения см. в публикации [Converting Hyper-V .vhdx to .vhd file formats](https://blogs.technet.microsoft.com/cbernier/2013/08/29/converting-hyper-v-vhdx-to-vhd-file-formats-for-use-in-windows-azure/) (Преобразование файлов Hyper-V в формате VHDX в формат VHD).

- Если у вас есть образ виртуальной машины Windows в [формате VMDK](https://en.wikipedia.org/wiki/VMDK), преобразуйте его в VHD с помощью [Microsoft Virtual Machine Converter](https://www.microsoft.com/download/details.aspx?id=42497). Дополнительные сведения см. в публикации [How to Convert a VMware VMDK to Hyper-V VHD](http://blogs.msdn.com/b/timomta/archive/2015/06/11/how-to-convert-a-vmware-vmdk-to-hyper-v-vhd.aspx) (Преобразование VMDK VMWare в VHD Hyper-V).


## Подготовка VHD к отправке

В этом разделе содержатся сведения об обобщении виртуальной машины Windows. При выполнении этой процедуры помимо прочего Sysprep удаляет все сведения о вашей учетной записи. Дополнительные сведения о программе Sysprep см. в статье [Использование программы Sysprep: введение](http://technet.microsoft.com/library/bb457073.aspx).

1. Выполните вход на виртуальную машину Windows.

2. Откройте окно командной строки с правами администратора. Измените каталог на **%windir%\\system32\\sysprep** и запустите файл `sysprep.exe`.

3. В диалоговом окне **Программа подготовки системы** сделайте следующее:

	1. В разделе **Действие по очистке системы** выберите **Переход в окно приветствия системы (OOBE)** и убедитесь, что установлен флажок **Подготовка к использованию**.

	2. В разделе **Параметры завершения работы** выберите **Завершение работы**.

	3. Нажмите кнопку **ОК**.

	.![Запустите Sysprep](./media/virtual-machines-windows-upload-image/sysprepgeneral.png)

.</br>


## Вход в Azure

1. Откройте Azure PowerShell и войдите в свою учетную запись Azure.

		Login-AzureRmAccount

	Откроется всплывающее окно для ввода данных учетной записи Azure.

2. Получите идентификаторы доступных вам подписок.

		Get-AzureRmSubscription

3. Укажите соответствующую подписку с помощью идентификатора.

		Select-AzureRmSubscription -SubscriptionId "<subscriptionID>"

	
## Получение учетной записи хранения

Вам необходима учетная запись хранения Azure, в которой хранится переданный образ виртуальной машины. Можно использовать существующую учетную запись хранения или создать новую.

Отобразите список доступных учетных записей хранения.

		Get-AzureRmStorageAccount

Если вы хотите использовать существующую учетную запись хранения, перейдите к разделу [Отправка образа виртуальной машины](#upload-the-vm-image-to-your-storage-account).

Если вы хотите создать новую учетную запись хранения, выполните следующие действия.

1. Убедитесь, что для этой учетной записи хранения существует группа ресурсов. Найдите все группы ресурсов в своей подписке, выполнив следующие действия.

		Get-AzureRmResourceGroup

2. Чтобы создать группу ресурсов, используйте следующую команду.

		New-AzureRmResourceGroup -Name <resourceGroupName> -Location <location>

3. Создайте учетную запись хранения в этой группе ресурсов с помощью командлета [New-AzureRmStorageAccount](https://msdn.microsoft.com/library/mt607148.aspx).

		New-AzureRmStorageAccount -ResourceGroupName <resourceGroupName> -Name <storageAccountName> -Location "<location>" -SkuName "<skuName>" -Kind "Storage"
			
Допустимые значения -SkuName перечислены ниже.

- **Standard\_LRS** — локально избыточное хранилище.
- **Standard\_ZRS** — хранилище, избыточное в пределах зоны.
- **Standard\_GRS** — геоизбыточное хранилище.
- **Standard\_RAGRS** — геоизбыточное хранилище с доступом только для чтения.
- **Premium\_LRS** — локально избыточное хранилище уровня "Премиум".



## Отправка образа виртуальной машины в учетную запись хранения

Используйте командлет [Add-AzureRmVhd](https://msdn.microsoft.com/library/mt603554.aspx), чтобы передать образ в контейнер в учетной записи хранения.

		$rgName = "<resourceGroupName>"
		$urlOfUploadedImageVhd = "<storageAccount>/<blobContainer>/<targetVHDName>.vhd"
		Add-AzureRmVhd -ResourceGroupName $rgName -Destination $urlOfUploadedImageVhd -LocalFilePath <localPathOfVHDFile>

Описание

- **storageAccount** — имя учетной записи хранения для образа.

- **BlobContainer** — контейнер больших двоичных объектов, где будет храниться образ. Если контейнер больших двоичных объектов с таким именем не будет найден, то он будет создан автоматически.

- **targetVHDName** — имя, присваиваемое переданному VHD-файлу.

- **localPathOfVHDFile** — это путь и имя VHD-файла на локальном компьютере.


В случае успешного выполнения отобразится ответ, который выглядит следующим образом.

		C:\> Add-AzureRmVhd -ResourceGroupName testUpldRG -Destination https://testupldstore2.blob.core.windows.net/testblobs/WinServer12.vhd -LocalFilePath "C:\temp\WinServer12.vhd"
		MD5 hash is being calculated for the file C:\temp\WinServer12.vhd.
		MD5 hash calculation is completed.
		Elapsed time for the operation: 00:03:35
		Creating new page blob of size 53687091712...
		Elapsed time for upload: 01:12:49

		LocalFilePath           DestinationUri
		-------------           --------------
		C:\temp\WinServer12.vhd https://testupldstore2.blob.core.windows.net/testblobs/WinServer12.vhd

В зависимости от сетевого подключения и размера VHD-файла выполнение этой команды может занять некоторое время.




## Создать виртуальную сеть

Создайте виртуальную сеть и подсеть [виртуальной сети](../virtual-network/virtual-networks-overview.md).

1. Замените значения переменных собственными данными. Укажите префикс адреса для подсети в формате CIDR. Создайте переменные и подсеть.

    	$rgName = "<resourceGroup>"
		$location = "<location>"
        $subnetName = "<subNetName>"
        $singleSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix <0.0.0.0/0>
        
2. Замените значение **$vnetName** именем виртуальной сети. Укажите префикс адреса для виртуальной сети в формате CIDR. Создайте переменную и виртуальную сеть с подсетью.

        $vnetName = "<vnetName>"
        $vnet = New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $location -AddressPrefix <0.0.0.0/0> -Subnet $singleSubnet
        
            
## Создание общедоступного IP-адреса и сетевого интерфейса

Чтобы обеспечить обмен данными с виртуальной машиной в виртуальной сети, требуются [общедоступный IP-адрес](../virtual-network/virtual-network-ip-addresses-overview-arm.md) и сетевой интерфейс.

1. Замените значение **$ipName** именем общедоступного IP-адреса. Создайте переменную и общедоступный IP-адрес.

        $ipName = "<ipName>"
        $pip = New-AzureRmPublicIpAddress -Name $ipName -ResourceGroupName $rgName -Location $location -AllocationMethod Dynamic
        
2. Замените значение **$nicName** именем сетевого интерфейса. Создайте переменную и сетевой интерфейс.

        $nicName = "<nicName>"
        $nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $rgName -Location $location -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id

		

## Создание виртуальной машины

Следующий сценарий PowerShell предназначен для настройки конфигураций виртуальных машин и использования переданного образа виртуальной машины в качестве источника новой установки.

>[AZURE.NOTE] Виртуальная машина должна находиться в той же учетной записи хранения, что и переданный VHD-файл.

.</br>

	
	
	#Create variables
	# Enter a new user name and password to use as the local administrator account for the remotely accessing the VM
	$cred = Get-Credential
	
	# Name of the storage account where the VHD file is and where the OS disk will be created
	$storageAccName = "<storageAccountName>"
	
	# Name of the virtual machine
	$vmName = "<vmName>"
	
	# Size of the virtual machine. See the VM sizes documentation for more information: https://azure.microsoft.com/documentation/articles/virtual-machines-windows-sizes/
	$vmSize = "<vmSize>"
	
	# Computer name for the VM
	$computerName = "<computerName>"
	
	# Name of the disk that holds the OS
	$osDiskName = "<osDiskName>"

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
	$vm = Set-AzureRmVMOSDisk -VM $vm -Name $osDiskName -VhdUri $osDiskUri -CreateOption fromImage -SourceImageUri $urlOfUploadedImageVhd -Windows

	#Create the new VM
	New-AzureRmVM -ResourceGroupName $rgName -Location $location -VM $vm



После завершения процесса новая виртуальная машина должна отображаться на [портале Azure](https://portal.azure.com) (выберите **Обзор** > **Виртуальные машины**). Ее можно также отобразить, выполнив следующие команды PowerShell.

	$vmList = Get-AzureRmVM -ResourceGroupName $rgName
	$vmList.Name


## Дальнейшие действия

Сведения об управлении виртуальной машиной с помощью Azure PowerShell см. в статье [Управление виртуальными машинами Azure с помощью Azure Resource Manager и PowerShell](virtual-machines-windows-ps-manage.md).

<!---HONumber=AcomDC_0831_2016-->
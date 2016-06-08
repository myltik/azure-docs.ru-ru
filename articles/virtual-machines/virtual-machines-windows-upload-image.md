<properties
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
	ms.date="05/06/2016"
	ms.author="cynthn"/>

# Отправка образа виртуальной машины Windows в Azure для развертываний Resource Manager


В этой статье показано, как отправить виртуальный жесткий диск (VHD) с операционной системой Windows, чтобы использовать его для создания виртуальных машин Windows с помощью модели развертывания Resource Manager Azure. Дополнительные сведения о дисках и виртуальных жестких дисках в Azure см. в статье [О дисках и виртуальных жестких дисках для виртуальных машин Azure](virtual-machines-linux-about-disks-vhds.md).



## Предварительные требования

В этой статье предполагается, что вы:

- Располагаете **подпиской Azure** — если у вас еще нет подписки Azure, вы можете [создать учетную запись Azure бесплатно](/pricing/free-trial/?WT.mc_id=A261C142F) и [активировать преимущества для подписчиков MSDN](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F).

- Установили **Azure PowerShell версии 1.0 или выше** — если это программное обеспечение еще не установлено, см. статью [Установка и настройка Azure PowerShell](../powershell-install-configure.md).

- Располагаете **виртуальной машиной под управлением ОС Windows** — существует множество средств для создания виртуальных машин в локальной среде. Например, см. документацию по [установке роли Hyper-V и настройке виртуальной машины](http://technet.microsoft.com/library/hh846766.aspx). Дополнительные сведения о поддерживаемых Azure операционных системах Windows см. в статье, посвященной [поддержке серверного ПО Майкрософт для виртуальных машин Microsoft Azure](https://support.microsoft.com/kb/2721672).


## Убедитесь, что на виртуальной машине используется правильный формат файлов.

Azure может принимать только образы [виртуальных машин первого поколения](http://blogs.technet.com/b/ausoemteam/archive/2015/04/21/deciding-when-to-use-generation-1-or-generation-2-virtual-machines-with-hyper-v.aspx), сохраненные в формате VHD-файлов. Размер VHD должен быть фиксированным и составлять целое число мегабайт, т. е. число, которое делится на 8. Максимально допустимый размер виртуального жесткого диска составляет 1023 ГБ.

- Если у вас есть образ виртуальной Машины Windows в формате VHDX, преобразуйте его в VHD с помощью одного из следующих средств.

	- Hyper-V: откройте Hyper-V и выберите локальный компьютер в левой части окна. Затем в меню, которое располагается над ним, выберите **Действие** > **Изменить диск...**. Перемещайтесь по экранам, нажимая кнопку **Далее** и вводя следующие параметры: *Путь к VHDX-файлу* > **Преобразовать** > **VHD** > **Фиксированный размер** > *Путь к новому VHD-файлу*. Нажмите кнопку **Готово**, чтобы закрыть окно.

	- [Командлет PowerShell Convert-VHD](http://technet.microsoft.com/library/hh848454.aspx): дополнительные сведения см. в публикации [Converting Hyper-V .vhdx to .vhd file formats](https://blogs.technet.microsoft.com/cbernier/2013/08/29/converting-hyper-v-vhdx-to-vhd-file-formats-for-use-in-windows-azure/) (Преобразование форматов файлов Hyper-V VHDX в VHD).

- Если образ виртуальной машины Windows имеет [ формат VMDK-файла](https://en.wikipedia.org/wiki/VMDK), преобразуйте его в формат VHD с помощью [Microsoft Virtual Machine Converter](https://www.microsoft.com/download/details.aspx?id=42497). Дополнительные сведения см. в публикации [How to Convert a VMware VMDK to Hyper-V VHD](http://blogs.msdn.com/b/timomta/archive/2015/06/11/how-to-convert-a-vmware-vmdk-to-hyper-v-vhd.aspx) (Преобразование VMWare VMDK в Hyper-V VHD).


## Подготовка VHD к отправке

В этом разделе содержатся сведения об обобщении виртуальной машины Windows. При выполнении этой процедуры помимо прочего будут удалены все сведения о вашей учетной записи. Обычно это необходимо сделать, если вы хотите использовать этот образ виртуальной машины для быстрого развертывания аналогичных виртуальных машин. Дополнительные сведения о программе Sysprep см. в статье [Использование программы Sysprep: введение](http://technet.microsoft.com/library/bb457073.aspx).

1. Выполните вход на виртуальную машину Windows.

2. Откройте окно командной строки с правами администратора. Измените каталог на **%windir%\\system32\\sysprep** и запустите файл `sysprep.exe`.

3. В диалоговом окне **Программа подготовки системы** выполните указанные далее действия.

	1. В разделе **Действие по очистке системы** выберите **Запуск при первом включении компьютера (OOBE)** и убедитесь, что установлен флажок **Обобщить**.

	2. В разделе **Параметры завершения работы** выберите **Завершение работы**.

	3. Нажмите кнопку **ОК**.

	![Запустите Sysprep](./media/virtual-machines-windows-upload-image/sysprepgeneral.png)

</br> <a id="createstorage"></a>
## Создание или поиск учетной записи хранения Azure

Для отправки образа виртуальной машины вам потребуется учетная запись хранения Azure. Можно использовать существующую учетную запись хранения или создать новую. Для этого можно использовать портал Azure или PowerShell.

### Создание или поиск учетной записи хранения Azure с помощью портала Azure

1. Войдите на [портал](https://portal.azure.com).

2. Щелкните **Обзор** > **Учетные записи хранения**.

3. Проверьте, существует ли учетная запись хранения, которую нужно использовать для отправки образа. Обратите внимание на имя этой учетной записи хранения. Если вы используете существующую учетную запись хранения, перейдите к разделу [Отправка образа виртуальной машины](#uploadvm).

4. Если вы хотите создать учетную запись хранения, нажмите кнопку **Добавить** и введите следующие сведения.

	1. Введите **имя** учетной записи хранения. Оно должно содержать от 3 до 24 строчных букв и цифр. Это имя становится частью URL-адреса, который будет использоваться для доступа к BLOB-объектам, файлам и другим ресурсам из учетной записи хранения.
	
	2. Выберите *Resource Manager* в качестве **модели развертывания**.

	3. Выберите соответствующие значения параметров **Вид учетной записи**, **Производительность** и **Репликация**. Чтобы узнать больше об этих значениях, можно навести указатель мыши на информационные значки.

	4. Выберите *+Создать* в области **Группа ресурсов** или существующую группу. Введите имя новой группы ресурсов, если требуется ее создать.

	5. Выберите **Расположение** учетной записи хранения и нажмите **Создать**. Учетная запись появится на панели **Учетные записи хранения**.

		![Введите сведения об учетной записи хранения](./media/virtual-machines-windows-upload-image/portal_create_storage_account.png)

	6. На этом и следующих шагах создается контейнер BLOB-объектов в учетной записи хранения. Это необязательная задача, так как команда PowerShell для отправки образа также позволяет создать контейнер BLOB-объектов для образа. Если вы не хотите создавать его самостоятельно, перейдите к разделу [Отправка образа виртуальной машины](#uploadvm). Или щелкните **BLOB-объекты** на плитке **Службы**.

		![Служба больших двоичных объектов](./media/virtual-machines-windows-upload-image/portal_create_blob.png)

	7. На открывшейся панели BLOB-объектов щелкните **+Контейнер**, чтобы создать контейнер хранилища BLOB-объектов. Введите имя контейнера и укажите тип доступа.

		![Создайте BLOB-объект](./media/virtual-machines-windows-upload-image/portal_create_container.png)

  		> [AZURE.NOTE] По умолчанию доступ к контейнеру предоставляется только владельцу учетной записи. Чтобы разрешить общий доступ на чтение для BLOB-объектов в контейнере, но не для свойств и метаданных контейнера, используйте параметр **BLOB-объект**. Чтобы разрешить полный общий доступ на чтение для контейнера и BLOB-объектов, используйте параметр **Контейнер**.

	8. На панели **Служба BLOB-объектов** появится новый контейнер BLOB-объектов. Запишите URL-адрес этого контейнера, так как он потребуется команде PowerShell для отправки образа. В зависимости от длины URL-адреса и разрешения экрана URL-адрес может быть частично скрыт. В этом случае разверните панель, щелкнув значок **Развернуть** в правом верхнем углу.


### Создание или поиск учетной записи хранения Azure с помощью PowerShell

1. Откройте Azure PowerShell 1.0.x и войдите в свою учетную запись Azure.

		Login-AzureRmAccount

	Эта команда откроет всплывающее окно для ввода учетных данных Azure.

2. Если идентификатор подписки, выбранный по умолчанию, отличается от идентификатора нужной подписки, воспользуйтесь одной из следующих команд, чтобы задать соответствующую подписку.

		Set-AzureRmContext -SubscriptionId "xxxx-xxxx-xxxx-xxxx"

	или

		Select-AzureRmSubscription -SubscriptionId "xxxx-xxxx-xxxx-xxxx"

	Можно найти подписки учетной записи Azure с помощью команды `Get-AzureRmSubscription`.

3. Найдите учетные записи хранения, доступные в рамках этой подписки.

		Get-AzureRmStorageAccount

	Если вы хотите использовать существующую учетную запись хранения, перейдите к разделу [Отправка образа виртуальной машины](#uploadvm).

4. Если вы хотите создать новую учетную запись хранения для хранения этого образа, выполните следующие действия.

	1. Убедитесь, что для этой учетной записи хранения существует группа ресурсов. Найдите все группы ресурсов в своей подписке, выполнив следующие действия.

			Get-AzureRmResourceGroup

	2. Если вы хотите создать группу ресурсов, используйте следующую команду:

			New-AzureRmResourceGroup -Name YourResourceGroup -Location "West US"

	3. Создайте учетную запись хранения в этой группе ресурсов с помощью следующей команды:

			New-AzureRmStorageAccount -ResourceGroupName YourResourceGroup -Name YourStorageAccountName -Location "West US" -Type "Standard_GRS"


</br> <a id="uploadvm"></a>

## Отправка образа виртуальной машины в учетную запись хранения

Следующие действия в Azure PowerShell предназначены для отправки образа виртуальной машины в учетную запись хранения. Образ будет загружен в контейнер хранилища BLOB-объектов в этой учетной записи. Можно использовать существующий контейнер или создать новый.

1. Выполните вход в Azure PowerShell 1.0.x с помощью `Login-AzureRmAccount`. Убедитесь, что используется правильная подписка. Для этого выполните командлет `Set-AzureRmContext -SubscriptionId "xxxx-xxxx-xxxx-xxxx"`, который упоминался в предыдущем разделе.

2. Добавьте обобщенный VHD Azure к учетной записи хранения с помощью командлета [Add-AzureRmVhd](https://msdn.microsoft.com/library/mt603554.aspx):

		Add-AzureRmVhd -ResourceGroupName YourResourceGroup -Destination "<StorageAccountURL>/<BlobContainer>/<TargetVHDName>.vhd" -LocalFilePath <LocalPathOfVHDFile>

	Описание
	- **StorageAccountURL** — это URL-адрес учетной записи хранения. Как правило, он имеет следующий формат: `https://YourStorageAccountName.blob.core.windows.net`. Обратите внимание, что вместо *YourStorageAccountName* необходимо использовать имя существующей или новой учетной записи хранения.
	- **BlobContainer** — это контейнер BLOB-объектов, где будут храниться образы. Если командлету не удается найти контейнер больших двоичных объектов с таким именем, он создаст новый контейнер.
	- **TargetVHDName** — это имя, под которым должен быть сохранен образ.
	- **PathToVHDFile** — это полный путь и имя VHD-файла на локальном компьютере.

	Успешное выполнение `Add-AzureRmVhd` будет выглядеть следующим образом:

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

</br>
## Развертывание новой виртуальной машины из отправленного образа

Теперь отправленный образ можно использовать для создания виртуальной машины Windows. Далее показано, как использовать Azure PowerShell и образ виртуальной машины, отправленный в приведенных выше шагах, для создания виртуальной машины в новой виртуальной сети.

>[AZURE.NOTE] Образ виртуальной машины должен находиться в той же учетной записи хранения, что и фактическая создаваемая виртуальная машина.

### Создание сетевых ресурсов

Воспользуйтесь следующим примером сценария PowerShell для настройки виртуальной сети и сетевого адаптера для новой виртуальной машины. Используйте значения переменных, представленных символом **$**, в соответствии с требованиями вашего приложения.

	$pip = New-AzureRmPublicIpAddress -Name $pipName -ResourceGroupName $rgName -Location $location -AllocationMethod Dynamic

	$subnetconfig = New-AzureRmVirtualNetworkSubnetConfig -Name $subnet1Name -AddressPrefix $vnetSubnetAddressPrefix

	$vnet = New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $location -AddressPrefix $vnetAddressPrefix -Subnet $subnetconfig

	$nic = New-AzureRmNetworkInterface -Name $nicname -ResourceGroupName $rgName -Location $location -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id

### Создание виртуальной машины

Следующий сценарий PowerShell предназначен для настройки конфигураций виртуальных машин и использования отправленного образа виртуальной машины в качестве источника новой установки. </br>

	#Enter a new user name and password in the pop-up window for the following
	$cred = Get-Credential

	#Get the storage account where the uploaded image is stored
	$storageAcc = Get-AzureRmStorageAccount -ResourceGroupName $rgName -AccountName $storageAccName

	#Set the VM name and size
	#Use "Get-Help New-AzureRmVMConfig" to know the available options for -VMsize
	$vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize "Standard_A4"

	#Set the Windows operating system configuration and add the NIC
	$vm = Set-AzureRmVMOperatingSystem -VM $vmConfig -Windows -ComputerName $computerName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate

	$vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id

	#Create the OS disk URI
	$osDiskUri = '{0}vhds/{1}{2}.vhd' -f $storageAcc.PrimaryEndpoints.Blob.ToString(), $vmName.ToLower(), $osDiskName

	#Configure the OS disk to be created from the image (-CreateOption fromImage), and give the URL of the uploaded image VHD for the -SourceImageUri parameter
	#You can find this URL in the result of the Add-AzureRmVhd cmdlet above
	$vm = Set-AzureRmVMOSDisk -VM $vm -Name $osDiskName -VhdUri $osDiskUri -CreateOption fromImage -SourceImageUri $urlOfUploadedImageVhd -Windows

	#Create the new VM
	New-AzureRmVM -ResourceGroupName $rgName -Location $location -VM $vm

Например, рабочий процесс может выглядеть примерно следующим образом.

		C:\> $pipName = "testpip6"
		C:\> $pip = New-AzureRmPublicIpAddress -Name $pipName -ResourceGroupName $rgName -Location $location -AllocationMethod Dynamic
		C:\> $subnet1Name = "testsub6"
		C:\> $nicname = "testnic6"
		C:\> $vnetName = "testvnet6"
		C:\> $subnetconfig = New-AzureRmVirtualNetworkSubnetConfig -Name $subnet1Name -AddressPrefix $vnetSubnetAddressPrefix
		C:\> $vnet = New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $location -AddressPrefix $vnetAddressPrefix -Subnet $subnetconfig
		C:\> $nic = New-AzureRmNetworkInterface -Name $nicname -ResourceGroupName $rgName -Location $location -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id
		C:\> $vmName = "testupldvm6"
		C:\> $vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize "Standard_A4"
		C:\> $computerName = "testupldcomp6"
		C:\> $vm = Set-AzureRmVMOperatingSystem -VM $vmConfig -Windows -ComputerName $computerName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
		C:\> $vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id
		C:\> $osDiskName = "testupos6"
		C:\> $osDiskUri = '{0}vhds/{1}{2}.vhd' -f $storageAcc.PrimaryEndpoints.Blob.ToString(), $vmName.ToLower(), $osDiskName
		C:\> $urlOfUploadedImageVhd = "https://testupldstore2.blob.core.windows.net/testblobs/WinServer12.vhd"
		C:\> $vm = Set-AzureRmVMOSDisk -VM $vm -Name $osDiskName -VhdUri $osDiskUri -CreateOption fromImage -SourceImageUri $urlOfUploadedImageVhd -Windows
		C:\> $result = New-AzureRmVM -ResourceGroupName $rgName -Location $location -VM $vm
		C:\> $result
		RequestId IsSuccessStatusCode StatusCode ReasonPhrase
		--------- ------------------- ---------- ------------
		                         True         OK OK

Созданная виртуальная машина должна отображаться на [портале Azure](https://portal.azure.com) (выберите **Обзор** > **Виртуальные машины**). Ее можно также увидеть, выполнив следующие команды PowerShell:

	$vmList = Get-AzureRmVM -ResourceGroupName $rgName
	$vmList.Name


## Дальнейшие действия

Сведения об управлении виртуальной машиной с помощью Azure PowerShell см. в статье [Управление виртуальными машинами Azure с помощью Azure Resource Manager и PowerShell](virtual-machines-windows-ps-manage.md).

<!---HONumber=AcomDC_0525_2016-->
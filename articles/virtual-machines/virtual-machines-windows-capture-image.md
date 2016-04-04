<properties
	pageTitle="Запись виртуальной машины в диспетчере ресурсов Windows | Microsoft Azure"
	description="Узнайте, как записать образ виртуальной машины Azure под управлением Windows, созданной с помощью модели развертывания диспетчера ресурсов Azure."
	services="virtual-machines-windows"
	documentationCenter=""
	authors="dsk-2015"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/29/2016"
	ms.author="dkshir"/>


# Запись образа виртуальной машины Windows в модели развертывания диспетчера ресурсов

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)] [classic deployment model](virtual-machines-windows-classic-capture-image.md).


В этой статье содержатся сведения о применении Azure PowerShell для записи виртуальной машины Azure под управлением Windows, чтобы использовать ее для создания других виртуальных машин. Данный образ виртуальной машины включает в себя диск операционной системы и прочие диски данных, присоединенные к виртуальной машине. В нем не указаны ресурсы виртуальной сети, которые необходимы для создания виртуальной машины Windows, поэтому их нужно настроить перед созданием другой виртуальной машины, которая использует образ. Этот образ будет также [обобщенным образом Windows](https://technet.microsoft.com/library/hh824938.aspx).


## Предварительные требования

Эти инструкции предполагают, что вы уже создали виртуальную машину Azure в модели развертывания диспетчера ресурсов и настроили операционную систему, в частности, подключили диски с данными, установили приложения и т. п. Если это еще не сделано, прочите статью [Создание виртуальной машины Windows с помощью диспетчера ресурсов и PowerShell](virtual-machines-windows-ps-create.md). Виртуальную машину Windows можно легко создать с помощью [портала Azure](https://portal.azure.com). Прочтите статью [Создание виртуальной машины Windows на портале Azure](virtual-machines-windows-hero-tutorial.md).


## Подготовка виртуальной машины для записи образа

В этом разделе содержатся сведения об обобщении виртуальной машины Windows. При выполнении этой процедуры помимо прочего будут также удалены все сведения о вашей учетной записи. Обычно это необходимо сделать, если вы хотите использовать этот образ виртуальной машины для быстрого развертывания аналогичных виртуальных машин.

1. Выполните вход в виртуальную машину Windows. На [портале Azure](https://portal.azure.com) последовательно выберите **Обзор** > **Виртуальные машины** > имя вашей виртуальной машины Windows > **Подключить**.

2. Откройте окно командной строки с правами администратора.

3. Измените каталог на `%windir%\system32\sysprep` и запустите файл sysprep.exe.

4. В диалоговом окне **Средство SysPrep** выполните следующие действия:

	- В разделе **Действие по очистке системы** выберите **Запуск при первом включении компьютера (OOBE)** и убедитесь, что установлен флажок **Обобщить**. Дополнительную информацию об использовании Sysprep см. в разделе [Как использовать Sysprep: введение](http://technet.microsoft.com/library/bb457073.aspx).

	- В разделе **Параметры завершения работы** выберите **Завершение работы**.

	- Нажмите кнопку **ОК**.

	![Запуск Sysprep](./media/virtual-machines-windows-capture-image/SysprepGeneral.png)

5.	Sysprep завершит работу виртуальной машины. Ее состояние изменится на **Остановлена** на портале Azure.


</br>
## Запись образа виртуальной машины

Обобщенную виртуальную машину Windows можно записать с помощью Azure PowerShell или нового средства обозревателя диспетчера ресурсов Azure (ARM). В этом разделе будут рассматриваться оба способа.

### с использованием PowerShell.

В этой статье предполагается, что у вас установлен Azure PowerShell версии 1.0.x. Рекомендуется использовать эту версию, так как новые возможности диспетчера ресурсов не будут добавлены в более старые версии PowerShell. Ознакомьтесь с [Azure PowerShell 1.0](https://azure.microsoft.com/blog/azps-1-0/), чтобы узнать о различиях версий.

1. Откройте Azure PowerShell 1.0.x и войдите в свою учетную запись Azure.

		Login-AzureRmAccount

	Эта команда откроет всплывающее окно для ввода учетных данных Azure.

2. Если идентификатор подписки, выбранный по умолчанию, отличается от идентификатора нужной подписки, воспользуйтесь одним из следующих вариантов, чтобы задать соответствующую подписку.

		Set-AzureRmContext -SubscriptionId "xxxx-xxxx-xxxx-xxxx"

	или

		Select-AzureRmSubscription -SubscriptionId "xxxx-xxxx-xxxx-xxxx"

	Можно найти подписки учетной записи Azure с помощью команды `Get-AzureRmSubscription`.

3. Теперь необходимо освободить ресурсы, используемые этой виртуальной машиной.

		Stop-AzureRmVM -ResourceGroupName YourResourceGroup -Name YourWindowsVM

	Вы увидите, что состояние виртуальной машины на портале изменилось с **Остановлена** на **Остановлена (освобождена)**.

	>[AZURE.TIP] Узнать состояние виртуальной машины в PowerShell можно с помощью:</br> `$vm = Get-AzureRmVM -ResourceGroupName YourResourceGroup -Name YourWindowsVM -status`</br> `$vm.Statuses`</br> Поле **DisplayStatus** соответствует полю **Состояние** на портале Azure.

4. Далее необходимо задать состояние виртуальной машины _Обобщена_. Обратите внимание, что это необходимо сделать потому, что Azure не поддерживает способ, приведенный выше (`sysprep`).

		Set-AzureRmVm -ResourceGroupName YourResourceGroup -Name YourWindowsVM -Generalized

	>[AZURE.NOTE] Заданное выше обобщенное состояние не будет отображаться на портале. Однако его можно проверить с помощью команды Get-AzureRmVM, как показано в приведенном выше совете.

5. С помощью этой команды запишите образ виртуальной машины в целевой контейнер хранилища.

		Save-AzureRmVMImage -ResourceGroupName YourResourceGroup -VMName YourWindowsVM -DestinationContainerName YourImagesContainer -VHDNamePrefix YourTemplatePrefix -Path Yourlocalfilepath\Filename.json

	Переменная `-Path` является необязательной и может использоваться для локального сохранения шаблона JSON. Переменная `-DestinationContainerName` является именем контейнера для хранения изображений. URL-адрес сохраненного образа будет выглядеть следующим образом: `https://YourStorageAccountName.blob.core.windows.net/system/Microsoft.Compute/Images/YourImagesContainer/YourTemplatePrefix-osDisk.xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx.vhd`. Он будет создан в той же учетной записи, что и исходная виртуальная машина.

	>[AZURE.NOTE] Чтобы найти расположение образа, откройте локальный шаблон JSON-файла. Последовательно выберите **Ресурсы** > **Профиль хранилища** > **Диск ОС** > **Образ** > **URI**, чтобы получить полный путь к образу. На данный момент простой способ проверки этих образов на портале отсутствует, поскольку _системный_ контейнер в учетной записи хранения скрыт. Поэтому несмотря на то, что переменная `-Path` является необязательной, вы наверняка захотите использовать ее для локального сохранения шаблона и определения URL-адреса образа. Кроме того, его можно узнать с помощью инструмента **Обозреватель хранилищ Azure**, действие которого объясняется в следующем разделе.


### Использование обозревателя ресурсов Azure (предварительная версия)

[Обозреватель ресурсов Azure (предварительная версия)](https://azure.microsoft.com/blog/azure-resource-explorer-a-new-tool-to-discover-the-azure-api/) является новым средством, разработанным для управления ресурсами Azure, созданными в модели развертывания диспетчера ресурсов. С помощью этого средства можно легко выполнять следующие задачи:

- обнаруживать API управления ресурсами Azure;
- получать документацию по API и
- осуществлять вызовы API непосредственно в подписках Azure.

Чтобы узнать, что еще можно сделать с помощью этого мощного средства, просмотрите видеоролик [об обозревателе ресурсов Azure с Дэвидом Эббо (David Ebbo)](https://channel9.msdn.com/Shows/Azure-Friday/Azure-Resource-Manager-Explorer-with-David-Ebbo).

Обозреватель ресурсов можно использовать для записи виртуальной машины. Этот способ является альтернативой методу PowerShell.

1. Откройте [веб-сайт обозревателя ресурсов](https://resources.azure.com/) и войдите в свою учетную запись Azure.

2. В верхней правой части средства выберите **Чтение и запись**, чтобы разрешить выполнение операций _PUT_ и _POST_. По умолчанию задано значение **Только чтение**, то есть можно выполнять только операции _GET_.

	![Обозреватель ресурсов — чтение и запись](./media/virtual-machines-windows-capture-image/ArmExplorerReadWrite.png)

3. Затем найдите виртуальную машину Windows. Введите имя в _поле поиска_ в верхней части средства или в меню в левой части последовательно выберите **Подписки** > ваша подписка Azure > **Группы ресурсов** > ваша группа ресурсов > **Поставщики** > **Microsoft.Compute** > **Виртуальные машины** > ваша виртуальная машина Windows. Щелкнув виртуальную машину в области навигации слева, вы увидите ее шаблон в правой части средства.

4. В верхней правой части страницы шаблона отображаются вкладки для различных операций, доступных для данной виртуальной машины. Перейдите на вкладку **Действия (POST или DELETE)**.

	![Меню "Действие" обозревателя ресурсов](./media/virtual-machines-windows-capture-image/ArmExplorerActionMenu.png)

5. Вы увидите список всех действий, которые можно выполнять с виртуальной машиной.

	![Элементы меню "Действие" обозревателя ресурсов](./media/virtual-machines-windows-capture-image/ArmExplorerActionItems.png)

6. Отмените выделение ресурсов для виртуальной машины, нажав кнопку действия **Освобождение**. Состояние виртуальной машины изменится с **Остановлена** на **Остановлена (освобождена)**.

7. Пометьте виртуальную машину как обобщенную, нажав кнопку действия **Обобщить**. Проверьте изменение состояния, щелкнув меню **Представление экземпляра** под именем виртуальной машины в левой части и перейдя в раздел **Состояния** в правой части.

8. Под кнопкой действия **Запись** можно задать значения для записи образа. Внесенные значения могут выглядеть следующим образом.

	![Запись в обозревателе ресурсов](./media/virtual-machines-windows-capture-image/ArmExplorerCaptureAction.png)

	Нажмите кнопку действия **Запись**, чтобы записать образ виртуальной машины. Будет создан виртуальный жесткий диск для образа, а также файл шаблона JSON, который на данный момент недоступен ни через обозреватель ресурсов, ни через [портал](https://portal.azure.com).

9. Чтобы получить VHD и шаблон нового образа, скачайте и установите средство Azure для управления ресурсами хранилища — [обозреватель хранилищ Azure](http://storageexplorer.com/). Обозреватель хранилища Azure будет установлен локально на компьютере.

	- Откройте обозреватель хранилищ и войдите в свою подписку Azure. Вы должны увидеть все учетные записи хранения, доступные для вашей подписки.

	- В левой части вы увидите учетную запись хранения виртуальной машины, которая была записана в процессе выполнения вышеописанных действий. Дважды щелкните меню **Система** под ней. В правой части вы увидите содержимое папки **System**.

		![Система обозревателя хранилищ](./media/virtual-machines-windows-capture-image/StorageExplorer1.png)

	- Дважды щелкните **Microsoft.Compute** и **Images**, после чего будут отображены все папки образов. Дважды щелкните имя папки, введенное для переменной **destinationContainerName** во время записи образа из обозревателя ресурсов. Вы увидите VHD и файл шаблона JSON.

	- Здесь можно найти URL-адрес или загрузить VHD или шаблон, щелкнув его правой кнопкой мыши.

		![Шаблон обозревателя хранилищ](./media/virtual-machines-windows-capture-image/StorageExplorer2.png)


## Развертывание новой виртуальной машины из записанного образа

Теперь записанный образ можно использовать для создания виртуальной машины Windows. Далее показано, как использовать Azure PowerShell и образ виртуальной машины, записанный в процессе выполнения вышеуказанных действий, для создания виртуальной машины в новой виртуальной сети.

>[AZURE.NOTE] Образ виртуальной машины должен находиться в той же учетной записи хранения, что и фактическая создаваемая виртуальная машина.

### Создание сетевых ресурсов

Воспользуйтесь следующим примером сценария PowerShell для настройки виртуальной сети и сетевого адаптера для новой виртуальной машины. Используйте значения для переменных, представленных символом **$**.

	$pip = New-AzureRmPublicIpAddress -Name $pipName -ResourceGroupName $rgName -Location $location -AllocationMethod Dynamic

	$subnetconfig = New-AzureRmVirtualNetworkSubnetConfig -Name $subnet1Name -AddressPrefix $vnetSubnetAddressPrefix

	$vnet = New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $location -AddressPrefix $vnetAddressPrefix -Subnet $subnetconfig

	$nic = New-AzureRmNetworkInterface -Name $nicname -ResourceGroupName $rgName -Location $location -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id

### Создание виртуальной машины

Следующий сценарий PowerShell предназначен для настройки конфигураций виртуальных машин и использования записанного образа виртуальной машины в качестве источника новой установки. </br>

	#Enter a new username and password in the pop-up for the following
	$cred = Get-Credential

	#Get the storage account where the captured image is stored
	$storageAcc = Get-AzureRmStorageAccount -ResourceGroupName $rgName -AccountName $storageAccName

	#Set the VM name and size
	$vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize "Standard_A2"

	#Set the Windows operating system configuration and add the NIC
	$vm = Set-AzureRmVMOperatingSystem -VM $vmConfig -Windows -ComputerName $computerName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate

	$vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id

	#Create the OS disk URI
	$osDiskUri = '{0}vhds/{1}{2}.vhd' -f $storageAcc.PrimaryEndpoints.Blob.ToString(), $vmName.ToLower(), $osDiskName

	#Configure the OS disk to be created from image (-CreateOption fromImage) and give the URL of the captured image VHD for the -SourceImageUri parameter.
	#We found this URL in the local JSON template in the previous sections.
	$vm = Set-AzureRmVMOSDisk -VM $vm -Name $osDiskName -VhdUri $osDiskUri -CreateOption fromImage -SourceImageUri $urlOfCapturedImageVhd -Windows

	#Create the new VM
	New-AzureRmVM -ResourceGroupName $rgName -Location $location -VM $vm

Созданная виртуальная машина должна отображаться на [портале Azure](https://portal.azure.com) (выберите **Обзор** > **Виртуальные машины**). Или выполните следующие команды PowerShell:

	$vmList = Get-AzureRmVM -ResourceGroupName $rgName
	$vmList.Name


## Дальнейшие действия

Узнайте, как управлять созданной виртуальной машиной с помощью Azure PowerShell, просмотрев сведения в статье [Управление виртуальными машинами с помощью диспетчера ресурсов Azure и PowerShell](virtual-machines-windows-ps-manage.md).

<!---HONumber=AcomDC_0323_2016-->
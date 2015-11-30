<properties
	pageTitle="Создание виртуальной машины под управлением Linux с помощью Azure Powershell | Microsoft Azure"
	description="Узнайте, как создать виртуальную машину под управлением Linux и выполнить ее предварительную настройку с помощью Azure PowerShell."
	services="virtual-machines"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="11/11/2015"
	ms.author="cynthn"/>

# Создание и предварительная настройка виртуальной машины под управлением Linux с помощью Azure PowerShell

> [AZURE.SELECTOR]
- [Azure CLI](virtual-machines-linux-tutorial.md)
- [PowerShell](virtual-machines-ps-create-preconfigure-linux-vms.md)

<br>

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Модель диспетчера ресурсов.
 
Материал в этих шагах по созданию виртуальной машины Linux для создания наборов команд Azure PowerShell представлен таким образом, что достаточно лишь заполнить пробелы. Этот подход может оказаться полезным, если вы не знакомы с Azure PowerShell или хотите знать, какие именно значения следует задать для обеспечения работоспособности конфигурации.

Для формирования набора команд вы скопируете соответствующие наборы приведенных ниже блоков команд в текстовый файл или интегрированную среду сценариев PowerShell, а затем заполните значения переменных и удалите знаки < and >. Чтобы иметь представление о конечном результате, см. два [примера](#examples), приведенных в конце этой статьи.

Дополнительные сведения о виртуальных машинах под управлением Windows см. в разделе [Использование Azure PowerShell для создания виртуальных машин Windows](virtual-machines-ps-create-preconfigure-windows-vms.md).

## Установка Azure PowerShell

Если это еще не сделано, [установите и настройте Azure PowerShell](../install-configure-powershell.md). После этого откройте командную строку Azure PowerShell.

## Указание подписки и учетной записи хранения

Укажите подписку Azure и учетную запись хранения, выполнив следующие команды в командной строке Azure PowerShell.

Правильное имя подписки можно получить из свойства **SubscriptionName** в выходных данных команды **Get-AzureSubscription**.

Правильное имя учетной записи хранения можно получить из свойства **Label** в выходных данных команды **Get-AzureStorageAccount** после выполнения команды Select-AzureSubscription.

Замените все содержимое внутри кавычек, включая символы < and >, на правильные имена.

	$subscr="<subscription name>"
	$staccount="<storage account name>"
	Select-AzureSubscription -SubscriptionName $subscr –Current
	Set-AzureSubscription -SubscriptionName $subscr -CurrentStorageAccountName $staccount


## Поиск образа для использования

Далее необходимо определить значение ImageFamily для образа, который вы хотите использовать. Список доступных значений ImageFamily можно получить с помощью следующей команды.

	Get-AzureVMImage | select ImageFamily -Unique

Ниже приведено несколько примеров значений ImageFamily для компьютеров под управлением Linux:

- Ubuntu Server 12.10
- CoreOS Alpha
- SUSE Linux Enterprise Server 12

Откройте новый экземпляр любого текстового редактора или интегрированную среду сценариев PowerShell (ISE). Скопируйте следующий текст в новый текстовый файл или среду PowerShell ISE, замещая значение ImageFamily.

	$family="<ImageFamily value>"
	$image=Get-AzureVMImage | where { $_.ImageFamily -eq $family } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1

## Указание имени, размера и, при необходимости, группы доступности

Начните создавать свой набор команд, выбрав один из этих двух блоков команд (обязательно).

**Вариант 1.** Укажите имя и размер виртуальной машины.

	$vmname="<machine name>"
	$vmsize="<Specify one: Small, Medium, Large, ExtraLarge, A5, A6, A7, A8, A9>"
	$vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image

**Вариант 2.** Укажите имя, размер, а также имя группы доступности.

	$vmname="<machine name>"
	$vmsize="<Specify one: Small, Medium, Large, ExtraLarge, A5, A6, A7, A8, A9>"
	$availset="<set name>"
	$vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image -AvailabilitySetName $availset

Сведения о значениях InstanceSize для виртуальных машин серии D, DS или G см. в разделе [Размеры виртуальных машин и облачных служб для Azure](https://msdn.microsoft.com/library/azure/dn197896.aspx).


## Настройка параметров безопасности доступа пользователя

**Вариант 1.** Укажите исходное имя пользователя Linux и пароль (обязательно). Выберите надежный пароль. Чтобы проверить его надежность, см. раздел [Проверка надежности пароля. Использование надежных паролей](https://www.microsoft.com/security/pc-security/password-checker.aspx).

	$cred=Get-Credential -Message "Type the name and password of the initial Linux account."
	$vm1 | Add-AzureProvisioningConfig -Linux -LinuxUser $cred.GetNetworkCredential().Username -Password $cred.GetNetworkCredential().Password

**Вариант 2.** Укажите набор пар ключей SSH, уже развернутых в подписке.

	$vm1 | Add-AzureProvisioningConfig -Linux -SSHKeyPairs "<SSH key pairs>"

Дополнительную информацию см. в разделе [Использование SSH с Linux в Azure](virtual-machines-linux-use-ssh-key.md).

**Вариант 3.** Укажите список открытых ключей SSH, уже развернутых в подписке.

	$vm1 | Add-AzureProvisioningConfig -Linux - SSHPublicKeys "<SSH public keys>"

Дополнительные параметры предварительной настройки для виртуальных машин под управлением Linux см. в описании синтаксиса для набора параметров **Linux** в [Add-AzureProvisioningConfig](https://msdn.microsoft.com/library/azure/dn495299.aspx).


## Необязательно: назначение статического выделенного IP-адреса

При необходимости назначьте виртуальной машине определенный IP-адрес, известный как статический выделенный IP-адрес.

	$vm1 | Set-AzureStaticVNetIP -IPAddress <IP address>

Доступность определенного IP-адреса можно проверить следующей командой:

	Test-AzureStaticVNetIP –VNetName <VNet name> –IPAddress <IP address>

## Необязательно: назначение виртуальной машины конкретной подсети 

Назначьте виртуальную машину определенной подсети в виртуальной сети Azure.

	$vm1 | Set-AzureSubnet -SubnetNames "<name of the subnet>"

	
## Необязательно: добавление диска данных
	
Добавьте следующий код в команду, чтобы добавить диск данных в виртуальную машину.

	$disksize=<size of the disk in GB>
	$disklabel="<the label on the disk>"
	$lun=<Logical Unit Number (LUN) of the disk>
	$hcaching="<Specify one: ReadOnly, ReadWrite, None>"
	$vm1 | Add-AzureDataDisk -CreateNew -DiskSizeInGB $disksize -DiskLabel $disklabel -LUN $lun -HostCaching $hcaching

## Необязательно: добавление виртуальной машины в существующий набор балансировки нагрузки 

Добавьте следующий код в набор команд, чтобы добавить виртуальную машину в существующий набор балансировки нагрузки для внешнего трафика.

	$prot="<Specify one: tcp, udp>"
	$localport=<port number of the internal port>
	$pubport=<port number of the external port>
	$endpointname="<name of the endpoint>"
	$lbsetname="<name of the existing load-balanced set>"
	$probeprotocol="<Specify one: tcp, http>"
	$probeport=<TCP or HTTP port number of probe traffic>
	$probepath="<URL path for probe traffic>"
	$vm1 | Add-AzureEndpoint -Name $endpointname -Protocol $prot -LocalPort $localport -PublicPort $pubport -LBSetName $lbsetname -ProbeProtocol $probeprotocol -ProbePort $probeport -ProbePath $probepath

## Выбор способа запуска процесса создания виртуальной машины 

Добавьте в свой набор команд один из следующих блоков команд, чтобы начать процесс создания виртуальной машины.

**Вариант 1.** Создайте виртуальную машину в существующей облачной службе.

	New-AzureVM –ServiceName "<short name of the cloud service>" -VMs $vm1

Короткое имя облачной службы — это то имя, которое отображается в списке облачных служб Azure на портале Azure или в списке групп ресурсов на портале предварительной версии Azure.

**Вариант 2.** Создайте виртуальную машину в существующей облачной службе и виртуальной сети.

	$svcname="<short name of the cloud service>"
	$vnetname="<name of the virtual network>"
	New-AzureVM –ServiceName $svcname -VMs $vm1 -VNetName $vnetname

## Запуск набора команд

Просмотрите набор команд Azure PowerShell, созданный в текстовом редакторе или интегрированной среде сценариев PowerShell, и убедитесь, что указаны все переменные, и они имеют правильные значения. Также убедитесь, что удалены все знаки < and >.

Скопируйте набор команд в буфер обмена и щелкните правой кнопкой мыши открытую командную строку Azure PowerShell. При этом набор команд выполняется в виде последовательности команд PowerShell, и создается виртуальная машина Azure.

После создания виртуальной машины ознакомьтесь с разделом [Как войти в виртуальную машину под управлением Linux](virtual-machines-linux-how-to-log-on.md).

Если вы хотите повторно использовать набор команд, можно выполнить следующее:

- Сохраните этот набор команд как файл сценария PowerShell (PS1).
- Сохраните этот набор команд как Runbook службы автоматизации Azure в разделе **Автоматизация** портала Azure.

## <a id="examples"></a>Примеры

Ниже приведено два примера применения описанных ранее способов создания наборов команд Azure PowerShell, создающих виртуальные машины под управлением Linux в Azure.

### Пример 1

Мне требуется набор команд PowerShell для создания исходной виртуальной машины Linux для сервера MySQL, который:

- использует образ Ubuntu Server 12.10;
- имеет имя AZMYSQL1;
- имеет дополнительный диск данных объемом 500 ГБ;
- имеет статический IP-адрес 192.168.244.4;
- находится в подсети BackEnd виртуальной сети AZDatacenter;
- находится в облачной службе Azure-TailspinToys.

Вот соответствующий набор команд Azure PowerShell для создания такой виртуальной машины, в котором для удобства чтения между блоками вставлены пустые строки.

	$family="Ubuntu Server 12.10"
	$image=Get-AzureVMImage | where { $_.ImageFamily -eq $family } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1

	$vmname="AZMYSQL1"
	$vmsize="Large"
	$vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image

	$cred=Get-Credential -Message "Type the name and password of the initial Linux account."
	$vm1 | Add-AzureProvisioningConfig -Linux -LinuxUser $cred.GetNetworkCredential().Username -Password $cred.GetNetworkCredential().Password

	$vm1 | Set-AzureSubnet -SubnetNames "BackEnd"

	$vm1 | Set-AzureStaticVNetIP -IPAddress 192.168.244.4

	$disksize=500
	$disklabel="MySQLData"
	$lun=0
	$hcaching="None"
	$vm1 | Add-AzureDataDisk -CreateNew -DiskSizeInGB $disksize -DiskLabel $disklabel -LUN $lun -HostCaching $hcaching

	$svcname="Azure-TailspinToys"
	$vnetname="AZDatacenter"
	New-AzureVM –ServiceName $svcname -VMs $vm1 -VNetName $vnetname

### Пример 2

Мне требуется набор команд PowerShell для создания виртуальной машины Linux для сервера Apache, который:

- использует образ SUSE Linux Enterprise Server 12;
- имеет имя LOB1;
- имеет дополнительный диск данных объемом 50 ГБ;
- является участником подсистемы балансировки нагрузки LOBServers, заданной для стандартного веб-трафика;
- находится в подсети FrontEnd виртуальной сети AZDatacenter;
- находится в облачной службе Azure-TailspinToys.

Вот соответствующий набор команд Azure PowerShell для создания такой виртуальной машины.

	$family="SUSE Linux Enterprise Server 12"
	$image=Get-AzureVMImage | where { $_.ImageFamily -eq $family } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1

	$vmname="LOB1"
	$vmsize="Medium"
	$vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image

	$cred=Get-Credential -Message "Type the name and password of the initial Linux account."
	$vm1 | Add-AzureProvisioningConfig -Linux -LinuxUser $cred.GetNetworkCredential().Username -Password $cred.GetNetworkCredential().Password

	$vm1 | Set-AzureSubnet -SubnetNames "FrontEnd"

	$disksize=50
	$disklabel="LOBApp"
	$lun=0
	$hcaching="ReadWrite"
	$vm1 | Add-AzureDataDisk -CreateNew -DiskSizeInGB $disksize -DiskLabel $disklabel -LUN $lun -HostCaching $hcaching

	$prot="tcp"
	$localport=80
	$pubport=80
	$endpointname="LOB1"
	$lbsetname="LOBServers"
	$probeprotocol="tcp"
	$probeport=80
	$probepath="/"
	$vm1 | Add-AzureEndpoint -Name $endpointname -Protocol $prot -LocalPort $localport -PublicPort $pubport -LBSetName $lbsetname -ProbeProtocol $probeprotocol -ProbePort $probeport -ProbePath $probepath

	$svcname="Azure-TailspinToys"
	$vnetname="AZDatacenter"
	New-AzureVM –ServiceName $svcname -VMs $vm1 -VNetName $vnetname

## Дополнительные ресурсы

[Документация по виртуальным машинам](http://azure.microsoft.com/documentation/services/virtual-machines/)

[Виртуальные машины Azure. Вопросы и ответы](http://msdn.microsoft.com/library/azure/dn683781.aspx)

[Общие сведения о виртуальных машинах Azure](http://msdn.microsoft.com/library/azure/jj156143.aspx)

[Установка и настройка Azure PowerShell](../install-configure-powershell.md)

[Вход в виртуальную машину под управлением ОС Linux](virtual-machines-linux-how-to-log-on.md)

[Использование Azure PowerShell для создания и предварительной настройки виртуальных машин под управлением Windows](virtual-machines-ps-create-preconfigure-windows-vms.md)

<!---HONumber=Nov15_HO4-->
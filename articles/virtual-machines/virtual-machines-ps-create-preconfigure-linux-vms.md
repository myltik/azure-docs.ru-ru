<properties 
	pageTitle="Использование Azure PowerShell для создания и предварительной настройки виртуальных машин под управлением Linux" 
	description="Узнайте, как использовать Azure PowerShell для создания и предварительной настройки виртуальных машин под управлением Linux в Azure." 
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
	ms.date="06/11/2015" 
	ms.author="josephd"/>

# Использование Azure PowerShell для создания и предварительной настройки виртуальных машин под управлением Linux

> [AZURE.SELECTOR]
- [Azure Portal](virtual-machines-linux-tutorial.md)
- [PowerShell](virtual-machines-ps-create-preconfigure-linux-vms.md)

Ниже показано, как настроить набор команд Azure PowerShell для создания и предварительной настройки виртуальной машины Azure под управлением Linux, используя подход на базе стандартных блоков. Этот процесс можно использовать для быстрого создания набора команд для новой виртуальной машины под управлением Linux и расширения существующего развертывания либо создания нескольких наборов команд, позволяющих быстро создать настраиваемую среду для разработки/тестирования или для ИТ-специалистов.

Материал в этих шагах для создания наборов команд Azure PowerShell представлен таким образом, что достаточно лишь заполнить пробелы. Этот подход может оказаться полезным, если вы не знакомы с PowerShell или хотите знать, какие именно значения следует задать для обеспечения работоспособности конфигурации. Опытные пользователи PowerShell могут подставить в команды собственные значения для переменных (строки, начинающиеся со знака "$").

Дополнительные сведения о настройке виртуальных машин под управлением Windows см. в разделе [Использование Azure PowerShell для создания и предварительной настройки виртуальных машин под управлением Windows](virtual-machines-ps-create-preconfigure-windows-vms.md).

## Шаг 1. Установка Azure PowerShell

Если вы еще не сделали этого, следуйте указаниям в разделе [Как установить и настроить Azure PowerShell](../install-configure-powershell.md), чтобы установить Azure PowerShell на локальном компьютере. После этого откройте командную строку Azure PowerShell.

## Шаг 2. Указание подписки и учетной записи хранения

Укажите подписку Azure и учетную запись хранения, выполнив следующие команды в командной строке Azure PowerShell. Замените все содержимое внутри кавычек, включая символы < and >, на правильные имена.

	$subscr="<subscription name>"
	$staccount="<storage account name>"
	Select-AzureSubscription -SubscriptionName $subscr –Current
	Set-AzureSubscription -SubscriptionName $subscr -CurrentStorageAccountName $staccount

Правильное имя подписки можно получить из свойства SubscriptionName в выходных данных команды **Get-AzureSubscription**. Правильное имя учетной записи хранения можно получить из свойства Label в выходных данных команды **Get-AzureStorageAccount** после выполнения команды **Select-AzureSubscription**. Эти команды также можно сохранить в текстовом файле для последующего использования.

## Шаг 3. Определение ImageFamily

Затем необходимо определить значение ImageFamily для определенного образа, соответствующего создаваемой виртуальной машине Azure. Список доступных значений ImageFamily можно получить с помощью данной команды.

	Get-AzureVMImage | select ImageFamily -Unique

Ниже приведено несколько примеров значений ImageFamily для компьютеров под управлением Linux:

- Ubuntu Server 12.10
- CoreOS Alpha
- SUSE Linux Enterprise Server 12

Откройте новый экземпляр любого текстового редактора или интегрированную среду сценариев PowerShell (ISE). Скопируйте следующий текст в новый текстовый файл или среду PowerShell ISE, замещая значение ImageFamily.
 
	$family="<ImageFamily value>"
	$image=Get-AzureVMImage | where { $_.ImageFamily -eq $family } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1

## Шаг 4. Создание своего набора команд

Сформируйте остальную часть набора команд. Для этого скопируйте соответствующий набор приведенных ниже блоков в новый текстовый файл или среду PowerShell ISE, а затем подставьте значения переменных и удалите символы < and >. Чтобы иметь представление о конечном результате, см. два [примера](#examples), приведенных в конце этой статьи.

Начните создавать свой набор команд, выбрав один из этих двух блоков команд (обязательно).

Вариант 1. Укажите имя и размер виртуальной машины.

	$vmname="<machine name>"
	$vmsize="<Specify one: Small, Medium, Large, ExtraLarge, A5, A6, A7, A8, A9>"
	$vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image

Вариант 2. Укажите имя, размер, а также имя группы доступности.

	$vmname="<machine name>"
	$vmsize="<Specify one: Small, Medium, Large, ExtraLarge, A5, A6, A7, A8, A9>"
	$availset="<set name>"
	$vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image -AvailabilitySetName $availset

Сведения о значениях InstanceSize для виртуальных машин серии D, DS или G см. в разделе [Размеры виртуальных машин и облачных служб для Azure](https://msdn.microsoft.com/library/azure/dn197896.aspx).

Укажите исходное имя пользователя Linux и пароль (обязательно). Выберите надежный пароль. Чтобы проверить его надежность, см. раздел [Проверка надежности пароля. Использование надежных паролей](https://www.microsoft.com/security/pc-security/password-checker.aspx).

	$cred=Get-Credential -Message "Type the name and password of the initial Linux account."	
	$vm1 | Add-AzureProvisioningConfig -Linux -LinuxUser $cred.GetNetworkCredential().Username -Password $cred.GetNetworkCredential().Password

Кроме того, можно указать набор пар ключей SSH, уже развернутых в подписке.

	$vm1 | Add-AzureProvisioningConfig -Linux -SSHKeyPairs "<SSH key pairs>"

Дополнительную информацию см. в разделе [Использование SSH с Linux в Azure](virtual-machines-linux-use-ssh-key.md).

Кроме того, можно указать список открытых ключей SSH, уже развернутых в подписке.

	$vm1 | Add-AzureProvisioningConfig -Linux - SSHPublicKeys "<SSH public keys>"

Дополнительные параметры предварительной настройки для виртуальных машин под управлением Linux см. в описании синтаксиса для набора параметров **Linux** в [Add-AzureProvisioningConfig](https://msdn.microsoft.com/library/azure/dn495299.aspx).

При необходимости назначьте виртуальной машине определенный IP-адрес, известный как статический выделенный IP-адрес.

	$vm1 | Set-AzureStaticVNetIP -IPAddress <IP address>

Доступность определенного IP-адреса можно проверить следующим образом:

	Test-AzureStaticVNetIP –VNetName <VNet name> –IPAddress <IP address>

При необходимости назначьте виртуальную машину определенной подсети в виртуальной сети Azure.

	$vm1 | Set-AzureSubnet -SubnetNames "<name of the subnet>"

При необходимости добавьте в виртуальную машину один диск данных.

	$disksize=<size of the disk in GB>
	$disklabel="<the label on the disk>"
	$lun=<Logical Unit Number (LUN) of the disk>
	$hcaching="<Specify one: ReadOnly, ReadWrite, None>"
	$vm1 | Add-AzureDataDisk -CreateNew -DiskSizeInGB $disksize -DiskLabel $disklabel -LUN $lun -HostCaching $hcaching

При необходимости добавьте в виртуальную машину существующий сбалансированный по нагрузке набор для внешнего трафика.

	$prot="<Specify one: tcp, udp>"
	$localport=<port number of the internal port>
	$pubport=<port number of the external port>
	$endpointname="<name of the endpoint>"
	$lbsetname="<name of the existing load-balanced set>"
	$probeprotocol="<Specify one: tcp, udp>"
	$probeport=<TCP or UDP port number of probe traffic>
	$probepath="<URL path for probe traffic>"
	$vm1 | Add-AzureEndpoint -Name $endpointname -Protocol $prot -LocalPort $localport -PublicPort $pubport -LBSetName $lbsetname -ProbeProtocol $probeprotocol -ProbePort $probeport -ProbePath $probepath

Наконец, начните процесс создания виртуальной машины, выбрав один из следующих блоков команд (обязательно).

Вариант 1. Создайте виртуальную машину в существующей облачной службе.

	New-AzureVM –ServiceName "<short name of the cloud service>" -VMs $vm1

Короткое имя облачной службы — это то имя, которое отображается в списке облачных служб на портале управления Azure или в списке групп ресурсов на портале предварительной версии Azure.

Вариант 2. Создайте виртуальную машину в существующей облачной службе и виртуальной сети.

	$svcname="<short name of the cloud service>"
	$vnetname="<name of the virtual network>"
	New-AzureVM –ServiceName $svcname -VMs $vm1 -VNetName $vnetname

## Шаг 5. Запуск набора команд

Просмотрите в текстовом редакторе или среде PowerShell ISE созданный набор команд Azure PowerShell, состоящий из нескольких блоков команд из шага 4. Убедитесь, что указаны все необходимые переменные и что они имеют правильные значения. Также убедитесь, что удалены все символы < and >.

Если вы используете текстовый редактор, скопируйте набор команд в буфер обмена и щелкните правой кнопкой мыши открытую командную строку Azure PowerShell. При этом набор команд выполняется в виде последовательности команд PowerShell, и создается виртуальная машина Azure. Или выполните набор команд в среде PowerShell ISE.

При создании виртуальной машины с неправильной подпиской, учетной записью хранения, облачной службой, группой доступности, виртуальной сетью или подсетью удалите виртуальную машину, исправьте синтаксис блока команд и запустите исправленный набор команд.

После создания виртуальной машины см. раздел [Как войти в виртуальную машину под управлением Linux](virtual-machines-linux-how-to-log-on.md).

Если вы собираетесь снова создать эту или подобную виртуальную машину, можно предпринять следующее:

- Сохраните этот набор команд как файл сценария PowerShell (PS1).
- Сохраните этот набор команд как Runbook автоматизации Azure в разделе **Автоматизация** портала управления Azure. 

## <a id="examples"></a>Примеры

Ниже приведено два примера применения описанных выше способов для создания наборов команд Azure PowerShell, создающих виртуальные машины под управлением Linux в Azure.

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

[Как войти в виртуальную машину под управлением Linux](virtual-machines-linux-how-to-log-on.md)

[Использование Azure PowerShell для создания и предварительной настройки виртуальных машин под управлением Windows](virtual-machines-ps-create-preconfigure-windows-vms.md)

 

<!---HONumber=58_postMigration-->
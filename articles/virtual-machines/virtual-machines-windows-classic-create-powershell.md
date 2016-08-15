<properties
	pageTitle="Создание виртуальной машины Windows с помощью PowerShell | Microsoft Azure"
	description="Создание виртуальных машин Windows с использованием Azure PowerShell и классической модели развертывания."
	services="virtual-machines-windows"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/05/2016"
	ms.author="cynthn"/>

# Создание виртуальной машины Windows с использованием PowerShell и классической модели развертывания 

> [AZURE.SELECTOR]
- [Классический портал Azure — Windows](virtual-machines-windows-classic-tutorial.md)
- [PowerShell — Windows](virtual-machines-windows-classic-create-powershell.md)

<br>


[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)] Узнайте, как [выполнить эти действия с помощью модели Resource Manager](virtual-machines-windows-ps-create.md).


Ниже показано, как настроить набор команд Azure PowerShell для создания и предварительной настройки виртуальной машины Azure под управлением Windows, используя подход на базе стандартных блоков. Этот процесс можно использовать для быстрого создания набора команд для новой виртуальной машины под управлением Windows и расширения существующего развертывания либо создания нескольких наборов команд, позволяющих быстро создать настраиваемую среду для разработки/тестирования или для ИТ-специалистов.

Материал в этих шагах для создания наборов команд Azure PowerShell представлен таким образом, что достаточно лишь заполнить пробелы. Этот подход может оказаться полезным, если вы не знакомы с PowerShell или хотите знать, какие именно значения следует задать для обеспечения работоспособности конфигурации. Опытные пользователи PowerShell могут подставить в команды собственные значения для переменных (строки, начинающиеся со знака "$").

Если вы еще не сделали этого, следуйте указаниям в разделе [Как установить и настроить Azure PowerShell](../powershell-install-configure.md), чтобы установить Azure PowerShell на локальном компьютере. Затем откройте командную строку Windows PowerShell.

## Шаг 1. Добавление учетной записи

1. В командной строке PowerShell введите **Add-AzureAccount** и нажмите клавишу **ВВОД**.
2. Введите адрес электронной почты, связанный с подпиской Azure, и нажмите кнопку **Продолжить**.
3. Введите пароль к учетной записи.
4. Щелкните **Войти**.

## Шаг 2. Указание подписки и учетной записи хранения

Укажите подписку Azure и учетную запись хранения, выполнив следующие команды в командной строке Windows PowerShell. Замените все содержимое внутри кавычек, включая знаки < и >, правильными именами.

	$subscr="<subscription name>"
	$staccount="<storage account name>"
	Select-AzureSubscription -SubscriptionName $subscr –Current
	Set-AzureSubscription -SubscriptionName $subscr -CurrentStorageAccountName $staccount

Правильное имя подписки можно получить из свойства SubscriptionName в выходных данных команды **Get-AzureSubscription**. Правильное имя учетной записи хранения можно получить из свойства Label в выходных данных команды **Get-AzureStorageAccount** после выполнения команды **Select-AzureSubscription**.

## Шаг 3. Определение ImageFamily

Затем необходимо определить значение ImageFamily или Label для определенного образа, соответствующего создаваемой виртуальной машине Azure. Список доступных значений ImageFamily можно получить с помощью данной команды.

	Get-AzureVMImage | select ImageFamily -Unique

Ниже приведено несколько примеров значений ImageFamily для компьютеров под управлением Windows:

- Центр обработки данных Windows Server 2012 R2;
- Windows Server 2008 R2 с пакетом обновления 1
- Windows Server 2016 Technical Preview 4
- SQL Server 2012 SP1 Enterprise на базе Windows Server 2012

Если вы нашли образ, который искали, откройте новый экземпляр любого текстового редактора или интегрированную среду сценариев PowerShell (ISE). Скопируйте следующий текст в новый текстовый файл или среду PowerShell ISE, замещая значение ImageFamily.

	$family="<ImageFamily value>"
	$image=Get-AzureVMImage | where { $_.ImageFamily -eq $family } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1

В некоторых случаях имя образа находится в свойстве Label, а не в значении ImageFamily. Если вы не нашли нужный образ с помощью свойства ImageFamily, отобразите список образов по свойству Label с помощью данной команды.

	Get-AzureVMImage | select Label -Unique

Если вы нашли нужный образ с помощью этой команды, откройте новый экземпляр любого текстового редактора или среду PowerShell ISE. Скопируйте следующий текст в новый текстовый файл или среду PowerShell ISE, замещая значение Label.

	$label="<Label value>"
	$image = Get-AzureVMImage | where { $_.Label -eq $label } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1

## Шаг 4. Создание своего набора команд

Сформируйте остальную часть набора команд. Для этого скопируйте соответствующий набор приведенных ниже блоков в новый текстовый файл или среду ISE, а затем подставьте значения переменных и удалите знаки < и >. Чтобы иметь представление о конечном результате, см. два [примера](#examples), приведенных в конце этой статьи.

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

>[AZURE.NOTE] Если у вас имеется соглашение Enterprise в рамках Software Assurance и вы хотите воспользоваться [преимуществами гибридного использования](https://azure.microsoft.com/pricing/hybrid-use-benefit/) Windows Server, то добавьте параметр **- LicenseType** в командлет **New-AzureVMConfig**, чтобы передать значение **Windows\_Server** для типичного варианта использования. Убедитесь, что используете переданный вами образ. Чтобы применить преимущества гибридного использования, стандартный образ из коллекции не подходит.

При необходимости для автономного компьютера Windows укажите учетную запись локального администратора и пароль.

	$cred=Get-Credential -Message "Type the name and password of the local administrator account."
	$vm1 | Add-AzureProvisioningConfig -Windows -AdminUsername $cred.Username -Password $cred.GetNetworkCredential().Password

 Выберите надежный пароль. Чтобы проверить его надежность, см. раздел [Проверка надежности пароля. Использование надежных паролей](https://www.microsoft.com/security/pc-security/password-checker.aspx).

Если потребуется добавить компьютер с Windows в существующий домен Active Directory, укажите учетную запись локального администратора и пароль, домен, а также имя и пароль учетной записи домена.

	$cred1=Get-Credential –Message "Type the name and password of the local administrator account."
	$cred2=Get-Credential –Message "Now type the name (not including the domain) and password of an account that has permission to add the machine to the domain."
	$domaindns="<FQDN of the domain that the machine is joining>"
	$domacctdomain="<domain of the account that has permission to add the machine to the domain>"
	$vm1 | Add-AzureProvisioningConfig -AdminUsername $cred1.Username -Password $cred1.GetNetworkCredential().Password -WindowsDomain -Domain $domacctdomain -DomainUserName $cred2.Username -DomainPassword $cred2.GetNetworkCredential().Password -JoinDomain $domaindns

Дополнительные параметры предварительной настройки для виртуальных машин под управлением Windows см. в описании синтаксиса для набора параметров **Windows** и **WindowsDomain** в [Add-AzureProvisioningConfig](https://msdn.microsoft.com/library/azure/dn495299.aspx).

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

Для контроллера домена Active Directory установите в $hcaching значение "None".

При необходимости добавьте в виртуальную машину существующий сбалансированный по нагрузке набор для внешнего трафика.

	$protocol="<Specify one: tcp, udp>"
	$localport=<port number of the internal port>
	$pubport=<port number of the external port>
	$endpointname="<name of the endpoint>"
	$lbsetname="<name of the existing load-balanced set>"
	$probeprotocol="<Specify one: tcp, http>"
	$probeport=<TCP or HTTP port number of probe traffic>
	$probepath="<URL path for probe traffic>"
	$vm1 | Add-AzureEndpoint -Name $endpointname -Protocol $protocol -LocalPort $localport -PublicPort $pubport -LBSetName $lbsetname -ProbeProtocol $probeprotocol -ProbePort $probeport -ProbePath $probepath

И в завершение выберите один из обязательных блоков команд для создания виртуальной машины.

Вариант 1. Создайте виртуальную машину в существующей облачной службе.

	New-AzureVM –ServiceName "<short name of the cloud service>" -VMs $vm1

Короткое имя облачной службы — это то имя, которое отображается в списке облачных служб на классическом портале Azure или в списке групп ресурсов на портале Azure.

Вариант 2. Создайте виртуальную машину в существующей облачной службе и виртуальной сети.

	$svcname="<short name of the cloud service>"
	$vnetname="<name of the virtual network>"
	New-AzureVM –ServiceName $svcname -VMs $vm1 -VNetName $vnetname

## Шаг 5. Запуск набора команд

Просмотрите в текстовом редакторе или среде PowerShell ISE созданный набор команд Azure PowerShell, состоящий из нескольких блоков команд из шага 4. Убедитесь, что указаны все необходимые переменные и что они имеют правильные значения. Убедитесь также, что удалены все знаки < и >.

Если вы используете текстовый редактор, скопируйте набор команд в буфер обмена и щелкните правой кнопкой мыши открытую командную строку Windows PowerShell. При этом набор команд выполняется в виде последовательности команд PowerShell, и создается виртуальная машина Azure. Или выполните набор команд в среде PowerShell ISE.

Если вы собираетесь снова создать эту или подобную виртуальную машину, можно предпринять следующее:

- Сохраните этот набор команд как файл сценария PowerShell (PS1-файл).
- Сохраните этот набор команд как Runbook службы автоматизации Azure в разделе **Автоматизация** классического портала Azure.

## <a id="examples"></a>Примеры

Ниже приведено два примера применения описанных выше способов для создания наборов команд Azure PowerShell, создающих виртуальные машины под управлением Windows в Azure.

### Пример 1

Мне требуется набор команд PowerShell для создания исходной виртуальной машины Linux для контроллера домена Active Directory, который:

- использует образ Windows Server 2012 R2 Datacenter;
- имеет имя AZDC1;
- является автономным компьютером;
- имеет дополнительный диск данных объемом 20 ГБ;
- имеет статический IP-адрес 192.168.244.4;
- находится в подсети BackEnd виртуальной сети AZDatacenter;
- находится в облачной службе Azure-TailspinToys.

Вот соответствующий набор команд Azure PowerShell для создания такой виртуальной машины, в котором для удобства чтения между блоками вставлены пустые строки.

	$family="Windows Server 2012 R2 Datacenter"
	$image=Get-AzureVMImage | where { $_.ImageFamily -eq $family } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1
	$vmname="AZDC1"
	$vmsize="Medium"
	$vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image

	$cred=Get-Credential -Message "Type the name and password of the local administrator account."
	$vm1 | Add-AzureProvisioningConfig -Windows -AdminUsername $cred.Username -Password $cred.GetNetworkCredential().Password

	$vm1 | Set-AzureSubnet -SubnetNames "BackEnd"

	$vm1 | Set-AzureStaticVNetIP -IPAddress 192.168.244.4

	$disksize=20
	$disklabel="DCData"
	$lun=0
	$hcaching="None"
	$vm1 | Add-AzureDataDisk -CreateNew -DiskSizeInGB $disksize -DiskLabel $disklabel -LUN $lun -HostCaching $hcaching

	$svcname="Azure-TailspinToys"
	$vnetname="AZDatacenter"
	New-AzureVM –ServiceName $svcname -VMs $vm1 -VNetName $vnetname

### Пример 2

Мне требуется набор команд PowerShell для создания виртуальной машины Linux для бизнес-сервера, который:

- использует образ Windows Server 2012 R2 Datacenter;
- имеет имя LOB1;
- является членом домена corp.contoso.com;
- имеет дополнительный диск данных объемом 200 ГБ;
- находится в подсети FrontEnd виртуальной сети AZDatacenter;
- находится в облачной службе Azure-TailspinToys.

Вот соответствующий набор команд Azure PowerShell для создания такой виртуальной машины.

	$family="Windows Server 2012 R2 Datacenter"
	$image=Get-AzureVMImage | where { $_.ImageFamily -eq $family } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1
	$vmname="LOB1"
	$vmsize="Large"
	$vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image

	$cred1=Get-Credential –Message "Type the name and password of the local administrator account."
	$cred2=Get-Credential –Message "Now type the name (not including the domain) and password of an account that has permission to add the machine to the domain."
	$domaindns="corp.contoso.com"
	$domacctdomain="CORP"
	$vm1 | Add-AzureProvisioningConfig -AdminUsername $cred1.Username -Password $cred1.GetNetworkCredential().Password -WindowsDomain -Domain $domacctdomain -DomainUserName $cred2.Username -DomainPassword $cred2.GetNetworkCredential().Password -JoinDomain $domaindns

	$vm1 | Set-AzureSubnet -SubnetNames "FrontEnd"

	$disksize=200
	$disklabel="LOBData"
	$lun=0
	$hcaching="ReadWrite"
	$vm1 | Add-AzureDataDisk -CreateNew -DiskSizeInGB $disksize -DiskLabel $disklabel -LUN $lun -HostCaching $hcaching

	$svcname="Azure-TailspinToys"
	$vnetname="AZDatacenter"
	New-AzureVM –ServiceName $svcname -VMs $vm1 -VNetName $vnetname


## Дальнейшие действия

Если требуется диск ОС больше 127 ГБ, вы можете [расширить диск ОС](virtual-machines-windows-expand-os-disk.md).

<!---HONumber=AcomDC_0803_2016-->
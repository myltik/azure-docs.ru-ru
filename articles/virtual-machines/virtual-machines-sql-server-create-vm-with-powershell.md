<properties 
	pageTitle="Создание виртуальной машины SQL Server в PowerShell | Microsoft Azure"
	description="Содержит описание действий и сценарии PowerShell для создания виртуальной машины Azure на основе образа из коллекции образов виртуальных машин SQL Server."
	services="virtual-machines"
	documentationCenter="na"
	authors="rothja"
	manager="jeffreyg"
	editor="monicar" 
	tags="azure-service-management"
	 />
<tags 
	ms.service="virtual-machines"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.workload="infrastructure-services"
	ms.date="08/26/2015"
	ms.author="jroth" />

# Создание виртуальной машины SQL Server в Azure (PowerShell)

> [AZURE.SELECTOR]
- [Azure portal](virtual-machines-provision-sql-server.md)
- [PowerShell](virtual-machines-sql-server-create-vm-with-powershell.md)

## Обзор

В данной статье описаны шаги по созданию виртуальной машины SQL Server в Azure с помощью командлетов PowerShell.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Модель диспетчера ресурсов.


## Установка и настройка PowerShell

1. Если у вас нет учетной записи Azure, используйте [бесплатную пробную версию Azure](https://azure.microsoft.com/ru-RU/pricing/free-trial/). 
 
2. [Установите последнюю версию командлетов Azure PowerShell](../powershell-install-configure.md/#how-to-install-azure-powershell).

3. [Подключитесь к PowerShell с помощью своей подписки Azure](../powershell-install-configure.md/#how-to-connect-to-your-subscription).

## Определение целевого региона Azure

Ваша виртуальная машина SQL Server будет размещаться в облачной службе, которая находится в определенном регионе Azure. Следующие шаги помогут определить регион, учетную запись хранения и облачную службу, которые будут использоваться в оставшейся части руководства.

1. Определите центр обработки данных, на котором должна размещаться виртуальная машина SQL Server. Следующие команды PowerShell выведут доступные регионы с общим списком в конце.

		Get-AzureLocation
		(Get-AzureLocation).Name

2.  После определения региона создайте переменную с именем **$dcLocation** и значением, соответствующим выбранному региону.

		$dcLocation = "<region name>"

## Указание подписки и учетной записи хранения

1. Определите подписку Azure, которая будет использоваться для новой виртуальной машины.

		(Get-AzureSubscription).SubscriptionName

1. Укажите целевую подписку Azure в переменной **$subscr**. Затем установите ее как текущую подписку Azure.

		$subscr="<subscription name>"
		Select-AzureSubscription -SubscriptionName $subscr –Current

1. После этого проверьте наличие существующих учетных записей хранения. Следующий сценарий выводит все учетные записи хранения, существующие в выбранном регионе:

		(Get-AzureStorageAccount | where { $_.GeoPrimaryLocation -eq $dcLocation }).StorageAccountName

	>[AZURE.NOTE]Если требуется создать новую учетную запись хранения, сначала создайте имя учетной записи в строчном регистре командой New-AzureStorageAccount, как в следующем примере: **New-AzureStorageAccount -StorageAccountName "<storage account name>" -Location $dcLocation**.

1. Присвойте имя целевой учетной записи хранения переменной **$staccount**. Затем установите текущую учетную запись хранения и подписку с помощью **Set-AzureSubscription**.

		$staccount="<storage account name>"
		Set-AzureSubscription -SubscriptionName $subscr -CurrentStorageAccountName $staccount

## Выбор образа виртуальной машины SQL Server

1. Получите список доступных образов виртуальных машин SQL Server из коллекции. Свойство **ImageFamily** для этих образов начинается с «SQL». Следующий запрос отображает доступное для вас семейство образов с предустановленным сервером SQL Server.

		Get-AzureVMImage | where { $_.ImageFamily -like "SQL*" } | select ImageFamily -Unique | Sort-Object -Property ImageFamily

1. В найденном семействе образов может быть несколько опубликованных образов. Используйте следующий сценарий, чтобы найти имя последнего опубликованного образа виртуальной машины для выбранного семейства образов (например, **SQL Server 2014 Enterprise с пакетом обновления 1 на Windows Server 2012 R2**):

		$family="<ImageFamily value>"
		$image=Get-AzureVMImage | where { $_.ImageFamily -eq $family } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1

		echo "Selected SQL Server image name:"
		echo "   $image"

## Создание виртуальной машины

Наконец создайте виртуальную машину с помощью PowerShell:

1. Создайте облачную службу для размещения новой виртуальной машины. Обратите внимание, что также можно использовать существующую облачную службу. Создайте новую переменную **$svcname** с коротким именем облачной службы.

		$svcname = "<cloud service name>"
		New-AzureService -ServiceName $svcname -Label $svcname -Location $dcLocation

2. Укажите имя и размер виртуальной машины. Дополнительные сведения о размерах виртуальных машин см. в статье [Размеры виртуальных машин в Azure](virtual-machines-size-specs.md).

		$vmname="<machine name>"
		$vmsize="<Specify a valid machine size>" # see the link to virtual machine sizes
		$vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image

3. Укажите имя и пароль учетной записи локального администратора.

		$cred=Get-Credential -Message "Type the name and password of the local administrator account."
		$vm1 | Add-AzureProvisioningConfig -Windows -AdminUsername $cred.GetNetworkCredential().Username -Password $cred.GetNetworkCredential().Password

4. Выполните следующий сценарий, чтобы создать виртуальную машину.

		New-AzureVM –ServiceName $svcname -VMs $vm1

>[AZURE.NOTE]Дополнительные сведения и параметры конфигурации см. в подразделе **Создание своего набора команд** раздела [Использование Azure PowerShell для создания и предварительной настройки виртуальных машин Windows](virtual-machines-ps-create-preconfigure-windows-vms.md).

## Пример сценария PowerShell

В следующем примере приведен полный сценарий, создающий виртуальную машину **SQL Server 2014 Enterprise с пакетом обновления 1 (SP1) на Windows Server 2012 R2**. При использовании этого сценария необходимо настроить исходные переменные на основе предыдущих шагов, описанных в этом разделе.

	# Customize these variables based on your settings and requirements:
	$dcLocation = "East US"
	$subscr="mysubscription"
	$staccount="mystorageaccount"
	$family="SQL Server 2014 SP1 Enterprise on Windows Server 2012 R2"
	$svcname = "mycloudservice"
	$vmname="myvirtualmachine"
	$vmsize="A5" 
	
	# Set the current subscription and storage account
	# Comment out the New-AzureStorageAccount line if the account already exists
	Select-AzureSubscription -SubscriptionName $subscr –Current
	New-AzureStorageAccount -StorageAccountName $staccount -Location $dcLocation
	Set-AzureSubscription -SubscriptionName $subscr -CurrentStorageAccountName $staccount
	
	# Select the most recent VM image in this image family:
	$image=Get-AzureVMImage | where { $_.ImageFamily -eq $family } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1
	
	# Create the new cloud service; comment out this line if cloud service exists already:
	New-AzureService -ServiceName $svcname -Label $svcname -Location $dcLocation
	
	# Create the VM config:
	$vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image
	
	# Set administrator credentials:
	$cred=Get-Credential -Message "Type the name and password of the local administrator account."
	$vm1 | Add-AzureProvisioningConfig -Windows -AdminUsername $cred.GetNetworkCredential().Username -Password $cred.GetNetworkCredential().Password
	
	# Create the SQL Server VM:
	New-AzureVM –ServiceName $svcname -VMs $vm1
	 

## Подключение к удаленному рабочему столу

1. Для завершения настройки создайте файлы .RDP для запуска этих виртуальных машин в каталоге документов текущего пользователя:

		$documentspath = [environment]::getfolderpath("mydocuments")
		Get-AzureRemoteDesktopFile -ServiceName $svcname -Name $vmname -LocalPath "$documentspath\vm1.rdp"

1. Запустите файл .RDP из каталога документов. Войдите в систему с именем пользователя и паролем администратора, которые были указаны ранее (например, если имя пользователя — VMAdmin, укажите "\\VMAdmin" в качестве пользователя и введите пароль).

		.\vm1.rdp

## Завершение настройки виртуальной машины SQL Server для удаленного доступа

Войдя в виртуальную машину с помощью удаленного рабочего стола, настройте SQL Server в соответствии с указаниями в подразделе [Действия по настройке подключения к SQL Server на виртуальной машине Azure](virtual-machines-sql-server-connectivity.md#steps-for-configuring-sql-server-connectivity-in-an-azure-vm).

## Дальнейшие действия

Дополнительные указания по подготовке виртуальных машин с помощью PowerShell можно найти в [документации по виртуальным машинам](virtual-machines-ps-create-preconfigure-windows-vms.md). Дополнительные сценарии для SQL Server и хранилища Premium см. в статье [Использование хранилища Azure Premium Storage с SQL Server на виртуальных машинах](virtual-machines-sql-server-use-premium-storage.md).

Во многих случаях следующим этапом является миграция баз данных на новую виртуальную машину SQL. Руководство по миграции баз данных см. в статье [Миграция базы данных в SQL Server на виртуальной машине Azure](virtual-machines-migrate-onpremises-database.md).

Если вам также интересно выполнение этих действий на портале Azure, см. статью [Подготовка виртуальной машины SQL Server в Azure](virtual-machines-provision-sql-server.md).

В дополнение к этим ресурсам рекомендуем ознакомиться с [другими статьями, связанными с запуском SQL Server на виртуальных машинах Azure](virtual-machines-sql-server-infrastructure-services.md).

<!---HONumber=AcomDC_1125_2015-->
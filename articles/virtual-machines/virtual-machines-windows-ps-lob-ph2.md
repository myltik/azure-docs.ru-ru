<properties 
	pageTitle="Бизнес-приложение, этап 2 | Microsoft Azure" 
	description="На втором этапе развертывания бизнес-приложения в Azure создайте и настройте два контроллера доменов реплик Active Directory." 
	documentationCenter=""
	services="virtual-machines-windows" 
	authors="JoeDavies-MSFT" 
	manager="timlt" 
	editor=""
	tags="azure-resource-manager"/>

<tags 
	ms.service="virtual-machines-windows" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="Windows" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/21/2016" 
	ms.author="josephd"/>

# Второй этап развертывания бизнес-приложений: настройка контроллеров домена

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]Классическая модель развертывания.
 

На этом этапе развертывания высокодоступного бизнес-приложения для интрасети на базе служб инфраструктуры Azure настраиваются два контроллера домена реплик в виртуальной сети Azure. Они используются для проверки подлинности клиентских веб-запросов к веб-ресурсам в виртуальной сети Azure вместо отправки данных для проверки в локальную сеть.

Этот этап необходимо выполнить, прежде чем переходить к [этапу 3](virtual-machines-windows-ps-lob-ph3.md). Все этапы перечислены в статье [Развертывание высокодоступных бизнес-приложений в Azure](virtual-machines-windows-lob-overview.md).

## Создание виртуальных машин контроллеров домена в среде Azure

Сначала необходимо заполнить столбец **Имя виртуальной машины** таблицы M и скорректировать размеры виртуальных машин в столбце **Минимальный размер**.

Элемент | Имя виртуальной машины | Образ из коллекции | Минимальный размер 
--- | --- | --- | --- 
1\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_ (первый контроллер домена, например DC1) | Центр обработки данных Windows Server 2012 R2 | Standard\_D2
2\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_ (второй контроллер домена, например DC2) | Центр обработки данных Windows Server 2012 R2 | Standard\_D2
3\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_ (главный сервер базы данных, например SQL1) | Microsoft SQL Server 2014 Enterprise — Windows Server 2012 R2 | 	Standard\_DS4
4\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_ (дополнительный сервер базы данных, например SQL2) | Microsoft SQL Server 2014 Enterprise — Windows Server 2012 R2 | 	Standard\_DS4
5\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_ (узел большинства кластера, например MN1) | Центр обработки данных Windows Server 2012 R2 | Standard\_D1
6\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_ (первый веб-сервер, например WEB1) | Центр обработки данных Windows Server 2012 R2 | Standard\_D3
7\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_ (второй веб-сервер, например WEB2) | Центр обработки данных Windows Server 2012 R2 | Standard\_D3

**Таблица М. Виртуальные машины для высокодоступных бизнес-приложений в Azure**

Полный список размеров виртуальных машин см. статье [Размеры виртуальных машин](virtual-machines-linux-sizes.md).

С помощью приведенного ниже набора команд Azure PowerShell создайте виртуальные машины для двух контроллеров домена. Укажите значения переменных, удалив знаки < and >. Обратите внимание на то, что в блоке команд PowerShell используются значения из перечисленных ниже таблиц.

- Таблица M (для виртуальных машин)
- Таблица V (для параметров виртуальной сети)
- Таблица S (для подсети)
- Таблица ST (для учетных записей хранения)
- Таблица A (для групп доступности)

Значения в таблицах V, S, ST и A были заданы на [первом этапе (настройка Azure)](virtual-machines-windows-ps-lob-ph1.md).

> [AZURE.NOTE] Следующая команда задает использование Azure PowerShell 1.0 и более поздней версии. Дополнительные сведения см. в статье [Azure PowerShell 1.0](https://azure.microsoft.com/blog/azps-1-0/).

Указав все необходимые значения, выполните полученные команды в среде Azure PowerShell.

	# Set up key variables
	$rgName="<resource group name>"
	$locName="<Azure location of your resource group>"
	$saName="<Table ST – Item 2 – Storage account name column>"
	$vnetName="<Table V – Item 1 – Value column>"
	$avName="<Table A – Item 1 – Availability set name column>"
	
	# Create the first domain controller
	$vmName="<Table M – Item 1 - Virtual machine name column>"
	$vmSize="<Table M – Item 1 - Minimum size column>"
	$staticIP="<Table V – Item 6 - Value column>"
	$vnet=Get-AzureRMVirtualNetwork -Name $vnetName -ResourceGroupName $rgName
	$nic=New-AzureRMNetworkInterface -Name ($vmName +"-NIC") -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[1].Id -PrivateIpAddress $staticIP
	$avSet=Get-AzureRMAvailabilitySet –Name $avName –ResourceGroupName $rgName 
	$vm=New-AzureRMVMConfig -VMName $vmName -VMSize $vmSize -AvailabilitySetId $avset.Id
	
	$diskSize=<size of the extra disk for AD DS data in GB>
	$storageAcc=Get-AzureRMStorageAccount -ResourceGroupName $rgName -Name $saName
	$vhdURI=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/" + $vmName + "-ADDSDisk.vhd"
	Add-AzureRMVMDataDisk -VM $vm -Name "ADDSData" -DiskSizeInGB $diskSize -VhdUri $vhdURI  -CreateOption empty
	
	$cred=Get-Credential -Message "Type the name and password of the local administrator account for the first domain controller." 
	$vm=Set-AzureRMVMOperatingSystem -VM $vm -Windows -ComputerName $vmName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
	$vm=Set-AzureRMVMSourceImage -VM $vm -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
	$vm=Add-AzureRMVMNetworkInterface -VM $vm -Id $nic.Id
	$storageAcc=Get-AzureRMStorageAccount -ResourceGroupName $rgName -Name $saName
	$osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/" + $vmName + "-OSDisk.vhd"
	$vm=Set-AzureRMVMOSDisk -VM $vm -Name "OSDisk" -VhdUri $osDiskUri -CreateOption fromImage
	New-AzureRMVM -ResourceGroupName $rgName -Location $locName -VM $vm
	
	# Create the second domain controller
	$vmName="<Table M – Item 2 - Virtual machine name column>"
	$vmSize="<Table M – Item 2 - Minimum size column>"
	$staticIP="<Table V – Item 7 - Value column>"
	$vnet=Get-AzureRMVirtualNetwork -Name $vnetName -ResourceGroupName $rgName
	$nic=New-AzureRMNetworkInterface -Name ($vmName +"-NIC") -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[1].Id -PrivateIpAddress $staticIP
	$avSet=Get-AzureRMAvailabilitySet –Name $avName –ResourceGroupName $rgName 
	$vm=New-AzureRMVMConfig -VMName $vmName -VMSize $vmSize -AvailabilitySetId $avset.Id
	
	$diskSize=<size of the extra disk for AD DS data in GB>
	$storageAcc=Get-AzureRMStorageAccount -ResourceGroupName $rgName -Name $saName
	$vhdURI=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/" + $vmName + "-ADDSDisk.vhd"
	Add-AzureRMVMDataDisk -VM $vm -Name "ADDSData" -DiskSizeInGB $diskSize -VhdUri $vhdURI  -CreateOption empty
	
	$cred=Get-Credential -Message "Type the name and password of the local administrator account for the second domain controller." 
	$vm=Set-AzureRMVMOperatingSystem -VM $vm -Windows -ComputerName $vmName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
	$vm=Set-AzureRMVMSourceImage -VM $vm -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
	$vm=Add-AzureRMVMNetworkInterface -VM $vm -Id $nic.Id
	$storageAcc=Get-AzureRMStorageAccount -ResourceGroupName $rgName -Name $saName
	$osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/" + $vmName + "-OSDisk.vhd"
	$vm=Set-AzureRMVMOSDisk -VM $vm -Name "OSDisk" -VhdUri $osDiskUri -CreateOption fromImage
	New-AzureRMVM -ResourceGroupName $rgName -Location $locName -VM $vm

> [AZURE.NOTE] Так как эти виртуальные машины предназначены для приложения интрасети, им не назначается общедоступный IP-адрес, у них нет метки DNS-имени домена и они не доступны в Интернете. Однако это также означает, что к ним невозможно подключиться с помощью портала Azure. Кнопка **Подключить** будет недоступна при просмотре свойств виртуальной машины. Используйте помощник "Подключение к удаленному рабочему столу" или другое аналогичное средство, чтобы подключиться к виртуальной машине с помощью частного IP-адреса или DNS-имени интрасети.

## Настройка первого контроллера домена

Используя предпочитаемый клиент удаленного рабочего стола, создайте подключение к удаленному рабочему столу виртуальной машины, относящейся к первому контроллеру домена. Для подключения используйте DNS интрасети контроллера домена или имя компьютера, а также учетные данные локальной учетной записи администратора.

Теперь добавьте на первый контроллер домена дополнительный диск данных.

### <a id="datadisk"></a>Инициализация пустого диска

1.	В левой области диспетчера серверов щелкните **Файловые службы и службы хранилища**, а затем выберите **Диски**.
2.	В области содержимого в группе **Диски** щелкните **диск 2** (при этом для параметра **Раздел** должно быть задано значение **Неизвестный**).
3.	Щелкните **Задачи**, а затем **Новый том**.
4.	На странице «Перед началом работы» мастера создания томов щелкните **Далее**.
5.	На странице "Выбор сервера и диска" щелкните **Диск 2**, а затем щелкните **Далее**. При появлении запроса нажмите кнопку OK.
6.	На странице «Выбор размера тома» щелкните **Далее**.
7.	На странице «Назначение букве диска или папке» щелкните **Далее**.
8.	На странице «Выбор параметров файловой системы» щелкните **Далее**.
9.	На странице «Подтверждение выбора» щелкните **Создать**.
10.	После завершения нажмите кнопку **Закрыть**.

Теперь проверьте, доступны ли с контроллера домена ресурсы в сети вашей организации.

### <a id="testconn"></a>Проверка подключения

1.	На рабочем столе откройте консоль Windows PowerShell.
2.	Используйте команду **ping**, чтобы проверить связь с именами и IP-адресами ресурсов в сети организации.

Эта процедура позволяет убедиться в том, что DNS-имена разрешаются правильно (виртуальная машина правильно настроена для работы с локальными DNS-серверами), а пакеты данных можно отправлять в распределенную виртуальную сеть и из нее. Если базовая проверка возвращает ошибку, обратитесь в ИТ-отдел для устранения неполадок с именами DNS и доставкой пакетов.

Затем в командной строке Windows PowerShell на первом контроллере домена выполните следующие команды:

	$domname="<DNS domain name of the domain for which this computer will be a domain controller>"
	Install-WindowsFeature AD-Domain-Services -IncludeManagementTools
	Install-ADDSDomainController -InstallDns –DomainName $domname  -DatabasePath "F:\NTDS" -SysvolPath "F:\SYSVOL" -LogPath "F:\Logs"

Вам будет предложено ввести данные учетной записи администратора домена. Компьютер перезапустится.

## Настройка второго контроллера домена

Используя предпочитаемый клиент удаленного рабочего стола, создайте подключение к удаленному рабочему столу виртуальной машины, относящейся ко второму контроллеру домена. Для подключения используйте DNS интрасети контроллера домена или имя компьютера, а также учетные данные локальной учетной записи администратора.

Теперь добавьте на второй контроллер домена дополнительный диск данных. См. раздел [Инициализация пустого диска](#datadisk).

Затем в командной строке Windows PowerShell на втором контроллере домена выполните следующие команды.

	$domname="<DNS domain name of the domain for which this computer will be a domain controller>"
	Install-WindowsFeature AD-Domain-Services -IncludeManagementTools
	Install-ADDSDomainController -InstallDns –DomainName $domname  -DatabasePath "F:\NTDS" -SysvolPath "F:\SYSVOL" -LogPath "F:\Logs"

Вам будет предложено ввести данные учетной записи администратора домена. Компьютер перезапустится.

Теперь измените параметры DNS-серверов в своей виртуальной сети: виртуальные машины в среде Azure должны использовать в качестве DNS-серверов IP-адреса двух новых контроллеров домена. Обратите внимание, что для этой процедуры используются значения из таблицы V с параметрами виртуальной сети, а также таблицы M с перечнем виртуальных машин.

1.	На левой панели портала Azure щелкните **Виртуальные сети** и выберите имя своей виртуальной сети (таблица V, элемент 1, столбец «Значение»).
2.	На панели **Параметры** щелкните **DNS-серверы**.
3.	На панели **DNS-серверы** введите следующую команду.
	- Для **основного DNS-сервера** в таблице V в столбце значений см. элемент 6.
	- Для **дополнительного DNS-сервера** в таблице V в столбце значений см. элемент 7.
4.	В левой панели на портале Azure щелкните **Виртуальные машины**.
5.	На панели **Виртуальные машины** щелкните имя первого контроллера домена (в таблице M в столбце имен виртуальных машин см. элемент 1).
6.	На панели виртуальной машины щелкните **Перезапустить**.
7.	После запуска первого контроллера домена на панели **Виртуальные машины** щелкните имя второго контроллера домена (в таблице M в столбце имен виртуальных машин см. элемент 2).
8.	На панели виртуальной машины щелкните **Перезапустить**. Дождитесь запуска второго контроллера домена.

Перезапуск контроллеров необходим для того, чтобы они перестали использовать локальные DNS-серверы. Оба контроллера сами являются DNS-серверами. Поэтому при повышении их уровня до контроллеров домена локальные DNS-серверы были автоматически назначены им в качестве DNS-серверов пересылки.

Теперь необходимо создать сайт репликации Active Directory, чтобы все серверы в виртуальной сети Azure использовали локальные контроллеры домена. Войдите на основной контроллер домена с учетной записью администратора домена и выполните следующие команды из консоли Windows PowerShell с правами администратора.

	$vnet="<Table V – Item 1 – Value column>"
	$vnetSpace="<Table V – Item 5 – Value column>"
	New-ADReplicationSite -Name $vnet 
	New-ADReplicationSubnet –Name $vnetSpace –Site $vnet

Добавьте учетную запись пользователя для управления виртуальными машинами SQL Server.

	New-ADUser -SamAccountName sqlservice -AccountPassword (read-host "Set user password" -assecurestring) -name "sqlservice" -enabled $true -PasswordNeverExpires $true -ChangePasswordAtLogon $false

На этой схеме показана конфигурация после успешного выполнения этого этапа (используются заполнители имен компьютеров):

![](./media/virtual-machines-windows-ps-lob-ph2/workload-lobapp-phase2.png)

## Дальнейшие действия

- Чтобы продолжить настройку этой рабочей нагрузки, см. [этап 3](virtual-machines-windows-ps-lob-ph3.md).

<!---HONumber=AcomDC_0323_2016-->
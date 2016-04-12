<properties
	pageTitle="Ферма SharePoint Server 2013, этап 3 | Microsoft Azure"
	description="На третьем этапе развертывания SharePoint Server 2013 в Azure создайте компьютеры и кластер SQL Server и включите группы доступности."
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
	ms.date="12/11/2015"
	ms.author="josephd"/>

# Ферма SharePoint в интрасети, этап 3: настройка инфраструктуры SQL Server

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]Классическая модель развертывания.

На этом этапе развертывания фермы SharePoint 2013 в интрасети с группами доступности AlwaysOn для SQL Server на базе служб инфраструктуры Azure вы создаете и настраиваете два компьютера SQL Server и компьютер узла большинства кластера, после чего они объединяются в кластер Windows Server.

Этот этап необходимо выполнить, прежде чем переходить к [этапу 4](virtual-machines-windows-ps-sp-intranet-ph4.md). Описания всех этапов см. в разделе [Развертывание среды SharePoint с группами доступности AlwaysOn для SQL Server на платформе Azure](virtual-machines-windows-sp-intranet-overview.md). virtual-machines-windows-sp-intranet-overview.md>[AZURE.NOTE] В этой статье используется образ SQL Server из коллекции образов Azure. За использование лицензии SQL Server взимается соответствующая плата. Можно также создать виртуальные машины в Azure и установить собственные лицензии SQL Server. В этом случае вам потребуется наличие Software Assurance и возможности перемещать лицензии, чтобы использовать лицензию SQL Server на виртуальной машине, в том числе на виртуальной машине Azure. Дополнительные сведения об установке SQL Server на виртуальную машину см. в статье [Установка SQL Server](https://msdn.microsoft.com/library/bb500469.aspx).

## Создание виртуальных машин кластера SQL Server в среде Azure

Для серверов SQL Server создаются две виртуальные машины. Один сервер SQL Server содержит первичную реплику базы данных группы доступности. Второй сервер содержит вторичную резервную реплику. Резервная копия необходима для обеспечения высокой доступности. Еще одна виртуальная машина содержит узел большинства кластера.

С помощью приведенного ниже набора команд PowerShell создайте виртуальные машины для трех серверов. Укажите значения переменных, удалив знаки < and >. Обратите внимание на то, что в наборе команд PowerShell используются значения из перечисленных ниже таблиц.

- Таблица M (для виртуальных машин)
- Таблица V (для параметров виртуальной сети)
- Таблица S (для подсети)
- Таблица ST (для учетных записей хранения)
- Таблица A (для групп доступности)

Значения в таблице M были заданы на [втором (настройка контроллеров домена)](virtual-machines-windows-ps-sp-intranet-ph2.md), а в таблицах V, S, ST и A — на [первом этапе (настройка Azure)](virtual-machines-windows-ps-sp-intranet-ph1.md).

> [AZURE.NOTE] Следующая команда задает использование Azure PowerShell 1.0 и более поздней версии. Дополнительные сведения см. в статье [Azure PowerShell 1.0](https://azure.microsoft.com/blog/azps-1-0/).

Указав все необходимые значения, выполните полученные команды в среде Azure PowerShell.


	# Set up key variables
	$rgName="<your resource group name>"
	$locName="<Azure location of your resource group>"
	$saName="<Table ST – Item 1 – Storage account name column>"
	$vnetName="<Table V – Item 1 – Value column>"
	$avName="<Table A – Item 2 – Availability set name column>"
	
	# Create the first SQL Server virtual machine
	$vmName="<Table M – Item 3 - Virtual machine name column>"
	$vmSize="<Table M – Item 3 - Minimum size column>"
	$vnet=Get-AzureRMVirtualNetwork -Name $vnetName -ResourceGroupName $rgName
	$nic=New-AzureRMNetworkInterface -Name ($vmName +"-NIC") -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[1].Id
	$avSet=Get-AzureRMAvailabilitySet –Name $avName –ResourceGroupName $rgName 
	$vm=New-AzureRMVMConfig -VMName $vmName -VMSize $vmSize -AvailabilitySetId $avset.Id
	
	$diskSize=<size of the extra disk for SQL data in GB>
	$diskLabel="<the label on the disk>"
	$storageAcc=Get-AzureRMStorageAccount -ResourceGroupName $rgName -Name $saName
	$vhdURI=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/" + $vmName + "-SQLDataDisk.vhd"
	Add-AzureRMVMDataDisk -VM $vm -Name $diskLabel -DiskSizeInGB $diskSize -VhdUri $vhdURI  -CreateOption empty
	
	$cred=Get-Credential -Message "Type the name and password of the local administrator account for the first SQL Server computer." 
	$vm=Set-AzureRMVMOperatingSystem -VM $vm -Windows -ComputerName $vmName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
	$vm=Set-AzureRMVMSourceImage -VM $vm -PublisherName MicrosoftSQLServer -Offer SQL2014-WS2012R2 -Skus Enterprise -Version "latest"
	$vm=Add-AzureRMVMNetworkInterface -VM $vm -Id $nic.Id
	$storageAcc=Get-AzureRMStorageAccount -ResourceGroupName $rgName -Name $saName
	$osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/" + $vmName + "-OSDisk.vhd"
	$vm=Set-AzureRMVMOSDisk -VM $vm -Name "OSDisk" -VhdUri $osDiskUri -CreateOption fromImage
	New-AzureRMVM -ResourceGroupName $rgName -Location $locName -VM $vm
	
	# Create the second SQL Server virtual machine
	$vmName="<Table M – Item 4 - Virtual machine name column>"
	$vmSize="<Table M – Item 4 - Minimum size column>"
	$nic=New-AzureRMNetworkInterface -Name ($vmName +"-NIC") -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[1].Id
	$vm=New-AzureRMVMConfig -VMName $vmName -VMSize $vmSize -AvailabilitySetId $avset.Id
	
	$diskSize=<size of the extra disk for SQL data in GB>
	$diskLabel="<the label on the disk>"
	$vhdURI=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/" + $vmName + "-ADDSDisk.vhd"
	Add-AzureRMVMDataDisk -VM $vm -Name $diskLabel -DiskSizeInGB $diskSize -VhdUri $vhdURI  -CreateOption empty
	
	$cred=Get-Credential -Message "Type the name and password of the local administrator account for the second SQL Server computer." 
	$vm=Set-AzureRMVMOperatingSystem -VM $vm -Windows -ComputerName $vmName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
	$vm=Set-AzureRMVMSourceImage -VM $vm -PublisherName MicrosoftSQLServer -Offer SQL2014-WS2012R2 -Skus Enterprise -Version "latest"
	$vm=Add-AzureRMVMNetworkInterface -VM $vm -Id $nic.Id
	$storageAcc=Get-AzureRMStorageAccount -ResourceGroupName $rgName -Name $saName
	$osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/" + $vmName + "-OSDisk.vhd"
	$vm=Set-AzureRMVMOSDisk -VM $vm -Name "OSDisk" -VhdUri $osDiskUri -CreateOption fromImage
	New-AzureRMVM -ResourceGroupName $rgName -Location $locName -VM $vm
	
	# Create the cluster majority node server
	$vmName="<Table M – Item 5 - Virtual machine name column>"
	$vmSize="<Table M – Item 5 - Minimum size column>"
	$nic=New-AzureRMNetworkInterface -Name ($vmName +"-NIC") -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[1].Id
	$vm=New-AzureRMVMConfig -VMName $vmName -VMSize $vmSize -AvailabilitySetId $avset.Id
	$cred=Get-Credential -Message "Type the name and password of the local administrator account for the cluster majority node server." 
	$vm=Set-AzureRMVMOperatingSystem -VM $vm -Windows -ComputerName $vmName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
	$vm=Set-AzureRMVMSourceImage -VM $vm -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
	$vm=Add-AzureRMVMNetworkInterface -VM $vm -Id $nic.Id
	$storageAcc=Get-AzureRMStorageAccount -ResourceGroupName $rgName -Name $saName
	$osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/" + $vmName + "-OSDisk.vhd"
	$vm=Set-AzureRMVMOSDisk -VM $vm -Name "OSDisk" -VhdUri $osDiskUri -CreateOption fromImage
	New-AzureRMVM -ResourceGroupName $rgName -Location $locName -VM $vm

> [AZURE.NOTE] Так как эти виртуальные машины предназначены для приложения интрасети, им не назначается общедоступный IP-адрес, у них нет метки DNS-имени домена и они не доступны в Интернете. Однако это также означает, что к ним невозможно подключиться с помощью портала Azure. Кнопка **Подключить** будет недоступна при просмотре свойств виртуальной машины. Используйте помощник "Подключение к удаленному рабочему столу" или другое аналогичное средство, чтобы подключиться к виртуальной машине с помощью частного IP-адреса или DNS-имени интрасети.

## Настройка компьютеров с SQL Server

Используйте предпочитаемый клиент удаленного рабочего стола и создайте отдельное подключение для каждой виртуальной машины с SQL Server. Для подключения используйте DNS интрасети контроллера домена или имя компьютера, а также учетные данные локальной учетной записи администратора.

Объедините виртуальные машины с сервером SQL Server в соответствующий домен AD DS, выполнив на каждой машине следующие команды Windows PowerShell.

	$domName="<AD DS domain name to join, such as corp.contoso.com>"
	Add-Computer -DomainName $domName
	Restart-Computer

Обратите внимание, что после ввода команды Add-Computer необходимо ввести данные для входа в учетную запись домена.

Когда виртуальные машины перезапустятся, повторно подключитесь к ним, используя учетную запись с правами локального администратора.

Для каждого сервера SQL Server выполните указанные ниже действия.

1. Соответствующие инструкции см. в разделе [Вход на виртуальную машину с использованием подключения к удаленному рабочему столу](virtual-machines-windows-ps-sp-intranet-ph2.md#logon).

2. Выполните процедуру [инициализации пустого диска](virtual-machines-windows-ps-sp-intranet-ph2.md#datadisk) дважды (по одному разу для каждого сервера SQL Server), чтобы добавить дополнительный диск данных.

3. Выполните в командной строке Windows PowerShell следующие команды:

		md f:\Data
		md f:\Log
		md f:\Backup

4. Теперь с помощью процедуры [проверки подключения](virtual-machines-windows-ps-sp-intranet-ph2.md#testconn) выясните, доступны ли ресурсы в сети вашей организации. Эта процедура позволяет убедиться в том, что DNS-имена разрешаются правильно (виртуальная машина правильно настроена для работы с DNS-серверами в виртуальной сети), а пакеты данных можно отправлять в распределенную виртуальную сеть и из нее.

Дважды выполните описанную ниже процедуру (по одному разу для каждого сервера SQL Server), чтобы настроить их для работы с диском F:, а также задать учетные записи и разрешения.

1.	На начальном экране введите **SQL Studio** и выберите **SQL Server 2014 Management Studio**.
2.	В окне **Подключение к серверу** щелкните **Подключить**.
3.	В левой области щелкните правой кнопкой мыши верхний узел (экземпляр по умолчанию, название которого соответствует имени машины) и выберите **Свойства**.
4.	В окне **Свойства сервера** щелкните **Параметры базы данных**.
5.	В разделе **Места хранения, используемые базой данных по умолчанию** задайте указанные ниже значения.
- В поле **Данные** укажите путь **f:\\Data**.
- В поле **Журнал** укажите путь **f:\\Log**.
- В поле **Резервная копия** укажите путь **f:\\Backup**.
- Только новые базы данных будут использовать эти расположения.
6.	Нажмите кнопку **ОК**, чтобы закрыть окно.
7.	В левой области разверните папку **Безопасность**.
8.	Щелкните правой кнопкой мыши элемент **Имена входа** и выберите **Новое имя входа**.
9.	В поле **Имя входа** введите *домен*\\sp\_farm\_db (замените *домен* именем домена, в котором была создана учетная запись sp\_farm\_db).
10.	В разделе **выбора страницы** щелкните **Роли сервера**, выберите **sysadmin** и нажмите кнопку **ОК**.
11.	Закройте SQL Server 2014 Management Studio.

Дважды выполните описанную ниже процедуру (по одному разу для каждого сервера SQL Server), чтобы разрешить подключения к удаленному рабочему столу с использованием учетной записи sp\_farm\_db.

1.	На начальном экране щелкните правой кнопкой мыши элемент **Этот компьютер** и выберите **Свойства**.
2.	В окне **Система** щелкните **Настройка удаленного доступа**.
3.	В разделе **Удаленный рабочий стол** щелкните **Выбрать пользователей** и нажмите кнопку **Добавить**.
4.	В поле **Введите имена объектов для выбора** введите [домен]**\\sp\_farm\_db**, а затем трижды нажмите кнопку **ОК**.

Серверу SQL Server необходим порт, по которому клиенты обращаются к серверу баз данных. Кроме того, необходимы порты для подключения к среде SQL Server Management Studio и управления группой высокой доступности. Теперь дважды выполните следующую команду в консоли Windows PowerShell с правами администратора (по одному разу для каждого сервера SQL Server), чтобы добавить правило брандмауэра Windows, которое разрешает входящий трафик на сервер SQL:

	New-NetFirewallRule -DisplayName "SQL Server ports 1433, 1434, and 5022" -Direction Inbound –Protocol TCP –LocalPort 1433,1434,5022 -Action Allow

Выйдите из учетной записи локального администратора на виртуальных машинах серверов SQL Server.

Дополнительные сведения см. в статье [Рекомендации по оптимизации производительности SQL Server в виртуальных машинах Azure](virtual-machines-windows-classic-sql-perf.md). Для повышения скорости ввода-вывода также можно отключить геоизбыточное хранилище (GRS) для учетной записи хранения фермы SharePoint и использовать пространство в хранилищах.

## Настройка сервера узла большинства кластера

Руководствуясь инструкциями в статье [Вход на виртуальную машину с использованием подключения к удаленному рабочему столу](virtual-machines-windows-ps-sp-intranet-ph2.md#logon), войдите на узел большинства кластера с учетной записью домена.

На узле большинства кластера с помощью процедуры [проверки подключения](virtual-machines-windows-ps-sp-intranet-ph2.md#testconn) убедитесь в наличии доступа к ресурсам в сети организации.

## Создание кластера Windows Server

Группы доступности AlwaysOn для SQL Server используют функцию отказоустойчивой кластеризации Windows Server (WSFC). С ее помощью несколько машин в кластере могут объединиться в группу. Когда на одной машине возникает сбой, ее место занимает другая. Таким образом, сначала необходимо активировать функцию отказоустойчивой кластеризации на всех машинах-участниках:

- сервере-источнике SQL Server;
- сервере-получателе SQL Server;
- узле большинства кластера.

Для создания отказоустойчивого кластера необходимо как минимум три виртуальных машины. На двух машинах работает SQL Server. Вторая виртуальная машина с SQL Server является синхронной вторичной репликой, которая позволяет исключить потерю данных в случае сбоя основной виртуальной машины. Размещать SQL Server на третьей машине не нужно. Узел большинства кластера обеспечивает кворум в WSFC. Поскольку кластер WSFC отслеживает работоспособность системы на основе кворума, для его постоянной работы необходимо постоянное большинство. Если в кластере только две машины и одна из них перестает работать, большинство отсутствует. Дополнительные сведения см. в статье [Режим кворума и конфигурация голосования WSFC (SQL Server)](http://msdn.microsoft.com/library/hh270280.aspx).

Для обоих компьютеров с сервером SQL Server и узла большинства кластера выполните в командной строке Windows PowerShell следующую команду с правами администратора:

	Install-WindowsFeature Failover-Clustering -IncludeManagementTools

В связи с тем, что работа DHCP в среде Azure в настоящее время не полностью соответствует стандарту RFC, при попытке создать отказоустойчивый кластер Windows Server (WSFC) может возникнуть ошибка. Дополнительные сведения можно найти в разделе «Поведение кластера WSFC в сети Azure» статьи «Высокий уровень доступности и аварийное восстановление для SQL Server в виртуальных машинах Azure». Однако существует обходное решение. Чтобы создать кластер, выполните перечисленные ниже действия.

1.	Войдите на виртуальную машину с сервером-источником SQL Server с учетной записью **sp\_install**.
2.	На начальном экране введите **Отказоустойчивость** и щелкните **Диспетчер отказоустойчивости кластеров**.
3.	В левой области щелкните правой кнопкой элемент **Диспетчер отказоустойчивости кластеров** и выберите команду **Создать кластер**.
4.	На странице «Перед началом работы» нажмите кнопку **Далее**.
5.	На странице «Выбор серверов» введите имя виртуальной машины с сервером-источником SQL Server, нажмите кнопку **Добавить**, а затем — **Далее**.
6.	На странице «Предупреждение проверки» выберите вариант **Нет. Для этого кластера не требуется поддержка Microsoft и, соответственно, не требуется выполнять проверочные тесты. После нажатия кнопки «Далее» продолжить создание кластера**, а затем нажмите кнопку **Далее**.
7.	На странице «Точка доступа для администрирования кластера» в поле **Имя кластера** введите имя кластера и нажмите кнопку **Далее**.
8.	На странице подтверждения нажмите кнопку **Далее**, чтобы начать создание кластера.
9.	На странице сводки нажмите кнопку **Готово**.
10.	В левой области щелкните новый кластер. В разделе **Ресурсы ядра кластера** области содержимого откройте имя кластера серверов. Для ресурса **IP-адрес** будет указано состояние **Сбой**. Его невозможно подключить к сети, так как кластеру назначен тот же IP-адрес, что и самой виртуальной машине. В результате адреса дублируются.
11.	Щелкните правой кнопкой мыши ресурс **IP-адреса** в состоянии сбоя и выберите **Свойства**.
12.	В диалоговом окне **Свойства IP-адреса** щелкните **Статический IP-адрес**.
13.	Введите свободный IP-адрес из диапазона адресов, соответствующего подсети, в которой расположен сервер SQL Server, и нажмите кнопку **ОК**.
14.	Щелкните правой кнопкой мыши ресурс IP-адреса в состоянии сбоя и выберите команду **Подключить**. Подождите, пока оба ресурса будут подключены. Когда ресурс имени кластера подключится, он добавит на контроллер домена новую учетную запись компьютера Active Directory (AD). Эта учетная запись впоследствии будет использоваться для запуска кластерной службы группы доступности.
15.	Теперь, когда учетная запись AD создана, отключите имя кластера. Щелкните его правой кнопкой мыши в разделе **Ресурсы ядра кластера** и выберите команду **Отключить**.
16.	Чтобы удалить IP-адрес кластера, щелкните правой кнопкой мыши элемент **IP-адрес**, выберите команду **Удалить** и нажмите кнопку **Да**. Ресурс кластера больше нельзя будет подключить, так как он зависит от ресурса IP-адреса. Однако группе доступности для работы не нужны ни имя кластера, ни IP-адрес, поэтому имя можно оставить в отключенном состоянии.
17.	Чтобы добавить в кластер остальные узлы, щелкните правой кнопкой мыши его имя на левой панели и выберите команду **Добавить узел**.
18.	На странице «Перед началом работы» нажмите кнопку **Далее**.
19.	На странице «Выбор серверов» добавьте в кластер сервер-получатель SQL Server и узел большинства (для каждого из них введите имя и нажмите кнопку **Добавить**). Добавив два компьютера, нажмите кнопку **Далее**. Если добавить машину не удается и появляется сообщение об ошибке «Служба удаленного реестра не запущена», выполните указанные ниже действия. Войдите на машину, откройте оснастку «Службы» (файл services.msc) и включите удаленный реестр. Дополнительные сведения см. в статье [Не удается подключиться к службе удаленного реестра](http://technet.microsoft.com/library/bb266998.aspx).
20.	На странице «Предупреждение проверки» выберите вариант **Нет. Для этого кластера не требуется поддержка Microsoft и, соответственно, не требуется выполнять проверочные тесты. После нажатия кнопки «Далее» продолжить создание кластера**, а затем нажмите кнопку **Далее**.
21.	На странице подтверждения нажмите кнопку **Далее**.
22.	На странице сводки нажмите кнопку **Готово**.
23.	В левой области щелкните **Узлы**. Вы должны увидеть в списке все три компьютера.

## Включение групп доступности AlwaysOn

Теперь необходимо включить группы доступности AlwaysOn с помощью диспетчера конфигурации SQL Server. Обратите внимание на то, что группа доступности SQL Server отличается от группы доступности Azure. Группа доступности SQL Server содержит высокодоступные базы данных с поддержкой восстановления. Группа доступности Azure распределяет виртуальные машины по разным доменам сбоя. Дополнительную информацию о доменах сбоя см. в статье [Управление доступностью виртуальных машин](virtual-machines-windows-manage-availability.md).

Чтобы включить группы доступности AlwaysOn на сервере SQL Server, выполните указанные ниже действия.

1.	Войдите на сервер-источник SQL Server с учетной записью **sp\_farm\_db** или другой учетной записью, у которой есть на этом сервере роль sysadmin.
2.	На начальном экране введите **Конфигурация SQL Server** и выберите **Диспетчер конфигурации SQL Server**.
3.	В левой области выберите **Службы SQL Server**.
4.	В области содержимого дважды щелкните **SQL Server (MSSQLSERVER)**.
5.	В разделе **Свойства SQL Server (MSSQLSERVER)** откройте вкладку **Высокий уровень доступности AlwaysOn**, выберите **Включить группы доступности AlwaysOn**, нажмите кнопку **Применить**, а затем — кнопку **ОК**. Не закрывайте окно свойств.
6.	Откройте вкладку с настройками управления доступностью виртуальных машин и в поле **Имя учетной записи** введите [домен]**\\sqlservice**. Введите пароль учетной записи sqlservice в поля **Пароль** и **Подтверждение пароля**, а затем нажмите кнопку **ОК**.
7.	В окне с сообщением нажмите кнопку **Да**, чтобы перезапустить службу SQL Server.
8.	Войдите на сервер-получатель SQL Server и повторите эту процедуру.

На следующей схеме показана конфигурация после успешного выполнения этого этапа (используются заполнители имен компьютеров):

![](./media/virtual-machines-windows-ps-sp-intranet-ph3/workload-spsqlao_03.png)

## Дальнейшие действия

- Чтобы продолжить настройку этой рабочей нагрузки, см. [этап 4](virtual-machines-windows-ps-sp-intranet-ph4.md).

<!---HONumber=AcomDC_0323_2016-->
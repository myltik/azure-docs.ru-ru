<properties 
	pageTitle="Бизнес-приложение, этап 3 | Microsoft Azure" 
	description="На третьем этапе развертывания бизнес-приложения в Azure создайте компьютеры и кластер SQL Server и включите группы доступности." 
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
	ms.date="04/01/2016" 
	ms.author="josephd"/>

# Третий этап развертывания бизнес-приложений: настройка инфраструктуры SQL Server

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]Классическая модель развертывания.

На этом этапе развертывания высокодоступного бизнес-приложения на базе служб инфраструктуры Azure настраиваются два компьютера: компьютер с SQL Server и компьютер с узлом большинства кластера. Затем они объединяются в кластер Windows Server.

Этот этап необходимо выполнить, прежде чем переходить к [этапу 4](virtual-machines-windows-ps-lob-ph4.md). Все этапы перечислены в статье [Развертывание высокодоступных бизнес-приложений в Azure.](virtual-machines-windows-lob-overview.md)

> [AZURE.NOTE] В этой статье используется образ SQL Server из коллекции образов Azure. За использование лицензии SQL Server взимается соответствующая плата. Можно также создать виртуальные машины в Azure и установить собственные лицензии SQL Server. В этом случае вам потребуется наличие Software Assurance и возможности перемещать лицензии, чтобы использовать лицензию SQL Server на виртуальной машине, в том числе на виртуальной машине Azure. Дополнительные сведения об установке SQL Server на виртуальную машину см. в статье [Установка SQL Server](https://msdn.microsoft.com/library/bb500469.aspx).

## Создание виртуальных машин кластера SQL Server в среде Azure

Имеются две виртуальные машины, на которых работает SQL Server. На одной виртуальной машине содержится первичная реплика базы данных группы доступности, а на второй — вторичная \(резервная\). Резервная реплика служит для обеспечения высокой доступности. Еще одна виртуальная машина содержит узел большинства кластера.

С помощью приведенного ниже набора команд PowerShell создайте виртуальные машины для трех серверов. Укажите значения переменных, удалив знаки < and >. Обратите внимание на то, что в наборе команд PowerShell используются значения из перечисленных ниже таблиц.

- Таблица M \(для виртуальных машин\)
- Таблица V \(для параметров виртуальной сети\)
- Таблица S \(для подсети\)
- Таблица ST \(для учетных записей хранения\)
- Таблица A \(для групп доступности\)

Напоминаем, что вы задали значения для таблицы M на [втором](virtual-machines-windows-ps-lob-ph2.md), а для таблиц V, S, ST и A — [на первом этапе](virtual-machines-windows-ps-lob-ph1.md).

> [AZURE.NOTE] Следующая команда задает использование Azure PowerShell 1.0 и более поздней версии. Дополнительные сведения см. в статье [Azure PowerShell 1.0](https://azure.microsoft.com/blog/azps-1-0/).

Указав все необходимые значения, выполните полученные команды в среде Azure PowerShell.

	# Set up key variables
	$rgName="<your resource group name>"
	$locName="<Azure location of your resource group>"
	# Change to the premium storage account
	$saName="<Table ST – Item 1 – Storage account name column>"
	$vnetName="<Table V – Item 1 – Value column>"
	$avName="<Table A – Item 2 – Availability set name column>"
	
	# Create the first SQL server
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
	
	# Change to the standard storage account
	$saName="<Table ST – Item 2 – Storage account name column>"
	
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

## Настройка компьютеров с сервером SQL Server

Используйте предпочитаемый клиент удаленного рабочего стола и создайте отдельное подключение для каждой виртуальной машины с SQL Server. Для подключения используйте DNS интрасети контроллера домена или имя компьютера, а также учетные данные локальной учетной записи администратора.

Объедините виртуальные машины с сервером SQL Server в соответствующий домен AD DS, выполнив на каждой машине следующие команды Windows PowerShell.

	$domName="<AD DS domain name to join, such as corp.contoso.com>"
	Add-Computer -DomainName $domName
	Restart-Computer

Обратите внимание, что после ввода команды Add-Computer необходимо ввести данные для входа в учетную запись домена.

Когда виртуальные машины перезапустятся, повторно подключитесь к ним, используя учетную запись с правами локального администратора.


Чтобы добавить дополнительный диск данных и создать папки на новом томе, выполните приведенную ниже процедуру на каждой виртуальной машине с SQL Server \(т. е. дважды\).

### Инициализация пустого диска и создание папок

1. В левой области диспетчера серверов щелкните **Файловые службы и службы хранилища**, а затем щелкните **Диски**.
2. В области содержимого в группе **Диски** щелкните **диск 2** \(при этом для параметра **Раздел** должно быть задано значение **Неизвестный**\).
3. Щелкните **Задачи**, а затем **Новый том**.
4. На странице «Перед началом работы» мастера создания томов щелкните **Далее**.
5. На странице «Выбор сервера и диска» щелкните **Диск 2**, а затем щелкните **Далее**. При появлении запроса нажмите кнопку **OK**.
6. На странице «Выбор размера тома» щелкните **Далее**.
7. На странице «Назначение букве диска или папке» щелкните **Далее**.
8. На странице «Выбор параметров файловой системы» щелкните **Далее**.
9. На странице «Подтверждение выбора» щелкните **Создать**.
10. После завершения нажмите кнопку **Закрыть**.
11. В командной строке Windows PowerShell выполните такие команды:
	- md f:\\Data
	- md f:\\Log
	- md f:\\Backup

Дважды выполните описанную ниже процедуру \(по одному разу для каждой виртуальной машины с SQL Server\). Таким образом вы укажете учетные записи и разрешения, а также настроите запись новых баз данных на диск F:\\.

1. На начальном экране введите **SQL Studio** и выберите **SQL Server 2014 Management Studio**.
2. В окне **Подключение к серверу** щелкните **Подключить**.
3. В левой области щелкните правой кнопкой мыши верхний узел \(экземпляр по умолчанию, название которого соответствует имени машины\) и выберите **Свойства**.
4.	В окне **Свойства сервера** щелкните **Параметры базы данных**.
5.	В разделе **Места хранения, используемые базой данных по умолчанию** задайте указанные ниже значения. 
	- В поле **Данные** укажите путь **f:\\Data**.
	- В поле **Журнал** укажите путь **f:\\Log**.
	- В поле **Резервная копия** укажите путь **f:\\Backup**.
	- Только новые базы данных будут использовать эти расположения.
6.	Нажмите кнопку **ОК**, чтобы закрыть окно.
7.	В левой области разверните папку **Безопасность**.
8.	Щелкните правой кнопкой мыши элемент **Имена входа** и выберите **Новое имя входа**.
9.	В поле **Имя для входа** введите *домен*\\sqladmin, заменив *домен* именем домена, в котором на [втором этапе](virtual-machines-windows-ps-lob-ph2.md) была создана учетная запись sqladmin. 
10.	В разделе **выбора страницы** щелкните **Роли сервера**, выберите **sysadmin** и нажмите кнопку **ОК**.
11.	Закройте SQL Server 2014 Management Studio.

Дважды выполните описанную ниже процедуру \(по одному разу для каждого сервера SQL Server\), чтобы разрешить подключения к удаленному рабочему столу с использованием учетной записи sqladmin.

1.	На начальном экране щелкните правой кнопкой мыши элемент **Этот компьютер** и выберите **Свойства**.
2.	В окне **Система** щелкните **Настройка удаленного доступа**.
3.	В разделе **Удаленный рабочий стол** щелкните **Выбрать пользователей** и нажмите кнопку **Добавить**.
4.	В поле **Введите имена объектов для выбора** введите \[домен\]\*\*\\sqladmin\*\* и трижды нажмите кнопку **ОК**.

Службе SQL Server необходим порт, по которому клиенты обращаются к серверу баз данных. Кроме того, необходимы порты для подключения к среде SQL Server Management Studio и управления группой высокой доступности. Теперь в консоли Windows PowerShell дважды выполните следующую команду с правами администратора \(по одному разу для каждого сервера SQL Server\). Она добавит в брандмауэр правило, которое разрешает входящий трафик этого типа.

	New-NetFirewallRule -DisplayName "SQL Server ports 1433, 1434, and 5022" -Direction Inbound –Protocol TCP –LocalPort 1433,1434,5022 -Action Allow

Выйдите из учетной записи локального администратора на виртуальных машинах с SQL Server.

Дополнительные сведения см. в статье [Рекомендации по оптимизации производительности SQL Server в виртуальных машинах Azure](virtual-machines-windows-sql-performance.md). Для повышения скорости ввода-вывода также можно отключить геоизбыточное хранилище \(GRS\) для учетной записи хранения бизнес-приложения и использовать пространство в хранилищах.

## Настройка сервера узла большинства кластера

Используя предпочитаемый клиент удаленного рабочего стола, создайте подключение к удаленному рабочему столу виртуальной машины, относящейся к узлу большинства кластера. Для подключения используйте DNS интрасети контроллера домена или имя компьютера, а также учетные данные локальной учетной записи администратора.

Присоедините узел большинства кластера к соответствующему домену AD DS, выполнив в среде Windows PowerShell следующие команды.

	$domName="<AD DS domain name to join, such as corp.contoso.com>"
	Add-Computer -DomainName $domName
	Restart-Computer

Обратите внимание, что при выполнении команды **Add-Computer** необходимо указать данные для входа в учетную запись домена.

Когда сервер узла перезагрузится, повторно подключитесь к нему, используя учетную запись с правами локального администратора.

## Создание кластера отказоустойчивой кластеризации Windows Server

Группы доступности AlwaysOn для SQL Server используют функцию отказоустойчивой кластеризации Windows Server \(WSFC\). С ее помощью несколько машин в кластере могут объединиться в группу. Когда на одной машине возникает сбой, ее место занимает другая. Таким образом, сначала необходимо активировать функцию отказоустойчивой кластеризации на всех машинах-участниках:

- основной виртуальной машине, на которой работает SQL Server;
- дополнительной виртуальной машине, на которой работает SQL Server;
- узле большинства кластера.

Для создания отказоустойчивого кластера необходимы как минимум три виртуальные машины. На двух машинах работает SQL Server, причем вторая виртуальная машина является синхронной вторичной репликой, которая позволяет исключить потерю данных в случае сбоя основной виртуальной машины. Размещать SQL Server на третьей виртуальной машине не нужно. Узел большинства кластера обеспечивает кворум в схеме WSFC. Поскольку кластер WSFC отслеживает работоспособность системы на основе кворума, для его постоянной работы необходимо постоянное большинство. Если в кластере только две машины и одна из них перестает работать, большинство отсутствует. Дополнительные сведения см. в статье [Режим кворума и конфигурация голосования WSFC \(SQL Server\)](http://msdn.microsoft.com/library/hh270280.aspx).

Для обоих виртуальных машин с SQL Server и узла большинства кластера выполните в командной строке Windows PowerShell следующую команду с правами администратора.

	Install-WindowsFeature Failover-Clustering -IncludeManagementTools

В связи с тем, что работа DHCP в среде Azure в настоящее время не полностью соответствует стандарту RFC, при попытке создать отказоустойчивый кластер Windows Server \(WSFC\) может возникнуть ошибка. Дополнительные сведения можно найти в разделе «Поведение кластера WSFC в сети Azure» статьи «Высокий уровень доступности и аварийное восстановление для SQL Server в виртуальных машинах Azure». Однако существует обходное решение. Чтобы создать кластер, выполните перечисленные ниже действия.

1.	Войдите на основную виртуальную машину с SQL Server с помощью учетной записи sqladmin, созданной на [втором этапе](virtual-machines-windows-ps-lob-ph2.md).
2.	На начальном экране введите **Отказоустойчивость** и щелкните **Диспетчер отказоустойчивости кластеров**.
3.	В левой области щелкните правой кнопкой элемент **Диспетчер отказоустойчивости кластеров** и выберите команду **Создать кластер**.
4.	На странице **Перед началом работы** нажмите кнопку **Далее**.
5.	На странице **Выбор серверов** введите имя виртуальной машины с сервером-источником SQL Server, щелкните **Добавить**, а затем **Далее**.
6.	На странице **Предупреждение проверки** выберите вариант **Нет. Для этого кластера не требуется поддержка Майкрософт, поэтому проверочные тесты не нужны. После нажатия кнопки «Далее» продолжить создание кластера**, а затем нажмите кнопку **Далее**.
7.	На странице **Точка доступа для администрирования кластера** в поле **Имя кластера** введите имя кластера и нажмите кнопку **Далее**.
8.	На странице **Подтверждение** нажмите кнопку **Далее**, чтобы начать создание кластера. 
9.	На странице **Сводка** нажмите кнопку **Готово**.
10.	В левой области щелкните новый кластер. В разделе **Ресурсы ядра кластера** области содержимого откройте имя кластера серверов. Для ресурса **IP-адрес** будет указано состояние **Сбой**. Его невозможно подключить к сети, так как кластеру назначен тот же IP-адрес, что и самой виртуальной машине. В результате адреса дублируются. 
11.	Щелкните правой кнопкой мыши ресурс **IP-адреса** в состоянии сбоя и выберите **Свойства**.
12.	В диалоговом окне **Свойства IP-адреса** щелкните **Статический IP-адрес**.
13.	Введите свободный IP-адрес из диапазона адресов, соответствующего подсети, в которой расположен сервер SQL Server, и нажмите кнопку **ОК**.
14.	Щелкните правой кнопкой мыши ресурс IP-адреса в состоянии сбоя и выберите команду **Подключить**. Подождите, пока оба ресурса будут подключены. Когда ресурс имени кластера подключится, он добавит на контроллер домена новую учетную запись компьютера Active Directory \(AD\). Эта учетная запись впоследствии будет использоваться для запуска кластерной службы группы доступности.
15.	Теперь, когда учетная запись AD создана, отключите имя кластера. Щелкните его правой кнопкой мыши в разделе **Ресурсы ядра кластера** и выберите команду **Отключить**.
16.	Чтобы удалить IP-адрес кластера, щелкните правой кнопкой мыши элемент **IP-адрес**, выберите команду **Удалить** и нажмите кнопку **Да**. Ресурс кластера больше нельзя будет подключить, так как он зависит от ресурса IP-адреса. Однако группе доступности для работы не нужны ни имя кластера, ни IP-адрес, поэтому имя можно оставить в отключенном состоянии.
17.	Чтобы добавить в кластер остальные узлы, щелкните правой кнопкой мыши его имя на левой панели и выберите команду **Добавить узел**.
18.	На странице **Перед началом работы** нажмите кнопку **Далее**. 
19.	На странице **Выбор серверов** добавьте в кластер сервер-получатель SQL Server и узел большинства кластера \(для каждого из них введите имя и нажмите кнопку **Добавить**\). Добавив два компьютера, нажмите кнопку **Далее**. Если добавить машину не удается и появляется сообщение об ошибке «Служба удаленного реестра не запущена», выполните указанные ниже действия. Войдите на машину, откройте оснастку «Службы» \(файл services.msc\) и включите удаленный реестр. Дополнительные сведения см. в статье [Не удается подключиться к службе удаленного реестра](http://technet.microsoft.com/library/bb266998.aspx). 
20.	На странице **Предупреждение проверки** выберите вариант **Нет. Для этого кластера не требуется поддержка Майкрософт, поэтому проверочные тесты не нужны. После нажатия кнопки «Далее» продолжить создание кластера**, а затем нажмите кнопку **Далее**. 
21.	На странице **Подтверждение** нажмите кнопку **Далее**.
22.	На странице **Сводка** нажмите кнопку **Готово**.
23.	В левой области щелкните **Узлы**. Вы должны увидеть в списке все три компьютера.

## Включение групп доступности AlwaysOn

Теперь необходимо включить группы доступности AlwaysOn с помощью диспетчера конфигурации SQL Server. Обратите внимание на то, что группа доступности SQL Server отличается от группы доступности Azure. Группа доступности SQL Server содержит высокодоступные базы данных с поддержкой восстановления. Группа доступности Azure распределяет виртуальные машины по разным доменам сбоя. Дополнительную информацию о доменах сбоя см. в статье [Управление доступностью виртуальных машин](virtual-machines-windows-manage-availability.md).

Чтобы включить группы доступности AlwaysOn на сервере SQL Server, выполните указанные ниже действия.

1.	Войдите на основную виртуальную машину с SQL Server с помощью учетной записи sqladmin, созданной на [втором этапе](virtual-machines-windows-ps-lob-ph2.md).
2.	На начальном экране введите **Конфигурация SQL Server** и выберите **Диспетчер конфигурации SQL Server**.
3.	В левой области выберите **Службы SQL Server**.
4.	В области содержимого дважды щелкните **SQL Server \(MSSQLSERVER\)**.
5.	В разделе **Свойства SQL Server \(MSSQLSERVER\)** откройте вкладку **Высокий уровень доступности AlwaysOn**, выберите **Включить группы доступности AlwaysOn**, нажмите кнопку **Применить**, а затем — кнопку **ОК**. Не закрывайте окно свойств. 
6.	Откройте вкладку с настройками управления доступностью виртуальных машин и в поле **Имя учетной записи** введите \[домен\]\*\*\\sqlservice\*\*. Введите пароль учетной записи sqlservice в поля **Пароль** и **Подтверждение пароля**, а затем нажмите кнопку **ОК**.
7.	В окне с сообщением нажмите кнопку **Да**, чтобы перезапустить службу SQL Server.
8.	Войдите на дополнительную виртуальную машину с сервером SQL Server, используя учетную запись sqladmin, и повторите шаги 2–7. 

На этой схеме показана конфигурация после успешного выполнения этого этапа \(используются заполнители имен компьютеров\):

![](./media/virtual-machines-windows-ps-lob-ph3/workload-lobapp-phase3.png)

## Дальнейшие действия

- Чтобы продолжить настройку этой рабочей нагрузки, см. [этап 4](virtual-machines-windows-ps-lob-ph4.md).

<!---HONumber=AcomDC_0413_2016-->
<properties
	pageTitle="Ферма SharePoint в интрасети, этап 3: настройка инфраструктуры SQL Server"
	description="На третьем этапе развертывания фермы SharePoint 2013 в интрасети с группами доступности AlwaysOn для SQL Server на базе служб инфраструктуры Azure создаются компьютеры кластера SQL Server и сам кластер."
	documentationCenter=""
	services="virtual-machines"
	authors="JoeDavies-MSFT"
	manager="timlt"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows-sharepoint"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/21/2015"
	ms.author="josephd"/>

# Ферма SharePoint в интрасети, этап 3: настройка инфраструктуры SQL Server

На этом этапе развертывания фермы SharePoint 2013 в интрасети с группами доступности AlwaysOn для SQL Server на базе служб инфраструктуры Azure в управлении службами создаются и настраиваются два компьютера SQL Server и компьютер узла большинства кластера, после чего они объединяются в кластер Windows Server.

Этот этап необходимо выполнить, прежде чем переходить к [этапу 4](virtual-machines-workload-intranet-sharepoint-phase4.md). Описания всех этапов см. в разделе [Развертывание среды SharePoint с группами доступности AlwaysOn для SQL Server на платформе Azure](virtual-machines-workload-intranet-sharepoint-overview.md).

## Создание виртуальных машин кластера SQL Server в среде Azure

Для серверов SQL Server создаются две виртуальные машины. Один сервер SQL Server содержит первичную реплику базы данных группы доступности. Второй сервер содержит вторичную резервную реплику. Резервная копия необходима для обеспечения высокой доступности. Еще одна виртуальная машина содержит узел большинства кластера.

С помощью приведенного ниже набора команд PowerShell создайте виртуальные машины для трех серверов. Укажите значения переменных, удалив знаки < and >. Обратите внимание на то, что в наборе команд PowerShell используются значения из перечисленных ниже таблиц.

- Таблица M (для виртуальных машин).
- Таблица V (для параметров виртуальной сети).
- Таблица S (для подсети).
- Таблица A (для групп доступности).
- Таблица C (для облачных служб).

Значения в таблице M были заданы на [этапе 2 «Настройка контроллеров домена»](virtual-machines-workload-intranet-sharepoint-phase2.md), а в таблицах V, S, A и C — на [этапе 1 «Настройка Azure»](virtual-machines-workload-intranet-sharepoint-phase1.md).

Указав все необходимые значения, выполните полученные команды в среде Azure PowerShell.

	# Create the first SQL server
	$vmName="<Table M – Item 3 - Virtual machine name column>"
	$vmSize="<Table M – Item 3 - Minimum size column, specify one: Small, Medium, Large, ExtraLarge, A5, A6, A7, A8, A9>"
	$availSet="<Table A – Item 2 – Availability set name column>"

	$image= Get-AzureVMImage | where { $_.ImageFamily -eq "SQL Server 2014 RTM Standard on Windows Server 2012 R2" } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1
	$vm1=New-AzureVMConfig -Name $vmName -InstanceSize $vmSize -ImageName $image -AvailabilitySetName $availSet

	$cred1=Get-Credential –Message "Type the name and password of the local administrator account for the first SQL Server computer."
	$cred2=Get-Credential –Message "Now type the name and password of an account that has permissions to add this virtual machine to the domain."
	$ADDomainName="<name of the AD domain that the server is joining (example CORP)>"
	$domainDNS="<FQDN of the AD domain that the server is joining (example corp.contoso.com)>"
	$vm1 | Add-AzureProvisioningConfig -AdminUsername $cred1.GetNetworkCredential().Username -Password $cred1.GetNetworkCredential().Password -WindowsDomain -Domain $ADDomainName -DomainUserName $cred2.GetNetworkCredential().Username -DomainPassword $cred2.GetNetworkCredential().Password -JoinDomain $domainDNS

	$diskSize=<size of the additional data disk in GB>
	$diskLabel="<the label on the disk>"
	$lun=<Logical Unit Number (LUN) of the disk>
	$vm1 | Add-AzureDataDisk -CreateNew -DiskSizeInGB $diskSize -DiskLabel $diskLabel -LUN $lun -HostCaching None

	$subnetName="<Table 6 – Item 1 – Subnet name column>"
	$vm1 | Set-AzureSubnet -SubnetNames $subnetName

	$serviceName="<Table C – Item 2 – Cloud service name column>"
	$vnetName="<Table V – Item 1 – Value column>"
	New-AzureVM –ServiceName $serviceName -VMs $vm1 -VNetName $vnetName

	# Create the second SQL server
	$vmName="<Table M – Item 4 - Virtual machine name column>"
	$vmSize="<Table M – Item 4 - Minimum size column, specify one: Small, Medium, Large, ExtraLarge, A5, A6, A7, A8, A9>"
	$vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image -AvailabilitySetName $availSet

	$cred1=Get-Credential –Message "Type the name and password of the local administrator account for the second SQL Server computer."
	$vm1 | Add-AzureProvisioningConfig -AdminUsername $cred1.GetNetworkCredential().Username -Password $cred1.GetNetworkCredential().Password -WindowsDomain -Domain $ADDomainName -DomainUserName $cred2.GetNetworkCredential().Username -DomainPassword $cred2.GetNetworkCredential().Password -JoinDomain $domainDNS

	$diskSize=<size of the additional data disk in GB>
	$diskLabel="<the label on the disk>"
	$lun=<Logical Unit Number (LUN) of the disk>
	$vm1 | Add-AzureDataDisk -CreateNew -DiskSizeInGB $diskSize -DiskLabel $diskLabel -LUN $lun -HostCaching None

	$vm1 | Set-AzureSubnet -SubnetNames $subnetName

	New-AzureVM –ServiceName $serviceName -VMs $vm1 -VNetName $vnetName

	# Create the cluster majority node server
	$vmName="<Table M – Item 5 - Virtual machine name column>"
	$vmSize="<Table M – Item 5 - Minimum size column, specify one: Small, Medium, Large, ExtraLarge, A5, A6, A7, A8, A9>"
	$image= Get-AzureVMImage | where { $_.ImageFamily -eq "Windows Server 2012 R2 Datacenter" } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1
	$vm1=New-AzureVMConfig -Name $vmName -InstanceSize $vmSize -ImageName $image -AvailabilitySetName $availSet

	$cred1=Get-Credential –Message "Type the name and password of the local administrator account for the cluster majority node server."
	$vm1 | Add-AzureProvisioningConfig -AdminUsername $cred1.GetNetworkCredential().Username -Password $cred1.GetNetworkCredential().Password -WindowsDomain -Domain $ADDomainName -DomainUserName $cred2.GetNetworkCredential().Username -DomainPassword $cred2.GetNetworkCredential().Password -JoinDomain $domainDNS

	$vm1 | Set-AzureSubnet -SubnetNames $subnetName

	New-AzureVM –ServiceName $serviceName -VMs $vm1 -VNetName $vnetName

## Настройка компьютеров с SQL Server

Для каждого сервера SQL Server выполните указанные ниже действия.

1. Соответствующие инструкции см. в разделе [Вход на виртуальную машину с использованием подключения к удаленному рабочему столу](virtual-machines-workload-intranet-sharepoint-phase2.md#logon).

2. Выполните процедуру [инициализации пустого диска](virtual-machines-workload-intranet-sharepoint-phase2.md#datadisk) дважды (по одному разу для каждого сервера SQL Server), чтобы добавить дополнительный диск данных.

3. Выполните в командной строке Windows PowerShell следующие команды:

		md f:\Data
		md f:\Log
		md f:\Backup

4. Теперь с помощью процедуры [проверки подключения](virtual-machines-workload-intranet-sharepoint-phase2.md#testconn) выясните, доступны ли ресурсы в сети вашей организации. Эта процедура позволяет убедиться в том, что DNS-имена разрешаются правильно (виртуальная машина правильно настроена для работы с DNS-серверами в виртуальной сети), а пакеты данных можно отправлять в распределенную виртуальную сеть и из нее.

Дважды выполните описанную ниже процедуру (по одному разу для каждого сервера SQL Server), чтобы настроить их для работы с диском F:, а также задать учетные записи и разрешения.


1.	На начальном экране введите **SQL Studio** и выберите **SQL Server 2014 Management Studio**.
2.	В окне **Подключение к серверу** щелкните **Подключить**.
3.	В левой области щелкните правой кнопкой мыши верхний узел (экземпляр по умолчанию, название которого соответствует имени машины) и выберите **Свойства**.
4.	В окне **Свойства сервера** щелкните **Параметры базы данных**.
5.	В разделе **Места хранения, используемые базой данных по умолчанию** задайте указанные ниже значения.
- В поле **Данные** укажите путь **f:\Data**.
- В поле **Журнал** укажите путь **f:\Log**.
- В поле **Резервная копия** укажите путь **f:\Backup**.
- Только новые базы данных будут использовать эти расположения.
6.	Нажмите кнопку **ОК**, чтобы закрыть окно.
7.	В левой области разверните папку **Безопасность**.
8.	Щелкните правой кнопкой мыши элемент **Имена входа** и выберите **Новое имя входа**.
9.	В поле **Имя входа** введите *домен*\sp_farm_db (замените *домен* именем домена, в котором была создана учетная запись sp_farm_db).
10.	В разделе **выбора страницы** щелкните **Роли сервера**, выберите **sysadmin** и нажмите кнопку **ОК**.
11.	Закройте SQL Server 2014 Management Studio.

Дважды выполните описанную ниже процедуру (по одному разу для каждого сервера SQL Server), чтобы разрешить подключения к удаленному рабочему столу с использованием учетной записи sp_farm_db.

1.	На начальном экране щелкните правой кнопкой мыши элемент **Этот компьютер** и выберите **Свойства**.
2.	В окне **Система** щелкните **Настройка удаленного доступа**.
3.	В разделе **Удаленный рабочий стол** щелкните **Выбрать пользователей** и нажмите кнопку **Добавить**.
4.	В поле **Введите имена объектов для выбора** введите [домен]**\sp_farm_db** и трижды нажмите кнопку **ОК**.

Серверу SQL Server необходим порт, по которому клиенты обращаются к серверу баз данных. Кроме того, необходимы порты для подключения к среде SQL Server Management Studio и управления группой высокой доступности. Теперь дважды выполните следующую команду в консоли Windows PowerShell с правами администратора (по одному разу для каждого сервера SQL Server), чтобы добавить правило брандмауэра Windows, которое разрешает входящий трафик на сервер SQL:

	New-NetFirewallRule -DisplayName "SQL Server ports 1433, 4234, and 5022" -Direction Inbound –Protocol TCP –LocalPort 1433,1434,5022 -Action Allow

Выйдите из учетной записи локального администратора на виртуальных машинах серверов SQL Server.

Дополнительные сведения см. в статье [Рекомендации по оптимизации производительности SQL Server в виртуальных машинах Azure](https://msdn.microsoft.com/library/azure/dn133149.aspx). Для повышения скорости ввода-вывода также можно отключить геоизбыточное хранилище (GRS) для учетной записи хранения фермы SharePoint и использовать пространство в хранилищах.

## Настройка сервера узла большинства кластера

Руководствуясь инструкциями в статье [Вход на виртуальную машину с использованием подключения к удаленному рабочему столу](virtual-machines-workload-intranet-sharepoint-phase2.md#logon), войдите на узел большинства кластера с учетной записью домена.

На узле большинства кластера с помощью процедуры [проверки подключения](virtual-machines-workload-intranet-sharepoint-phase2.md#testconn) убедитесь в наличии доступа к ресурсам в сети организации.

## Создание кластера Windows Server

Группы доступности AlwaysOn для SQL Server используют функцию отказоустойчивой кластеризации Windows Server (WSFC). С ее помощью несколько машин в кластере могут объединиться в группу. Когда на одной машине возникает сбой, ее место занимает другая. Таким образом, сначала необходимо активировать функцию отказоустойчивой кластеризации на всех машинах-участниках:

- сервере-источнике SQL Server;
- сервере-получателе SQL Server;
- узле большинства кластера.

Для создания отказоустойчивого кластера необходимо как минимум три виртуальных машины. На двух машинах работает SQL Server. Вторая виртуальная машина с SQL Server является синхронной вторичной репликой, которая позволяет исключить потерю данных в случае сбоя основной виртуальной машины. Размещать SQL Server на третьей машине не нужно. Узел большинства кластера выступает в качестве ресурса-свидетеля кворума в схеме WSFC. Поскольку кластер WSFC отслеживает работоспособность системы на основе кворума, для его постоянной работы необходимо постоянное большинство. Если в кластере только две машины и одна из них перестает работать, большинство отсутствует. Дополнительные сведения см. в статье [Режим кворума и конфигурация голосования WSFC (SQL Server)](http://msdn.microsoft.com/library/hh270280.aspx).

Для обоих компьютеров с сервером SQL Server и узла большинства кластера выполните в командной строке Windows PowerShell следующую команду с правами администратора:

	Install-WindowsFeature Failover-Clustering -IncludeManagementTools

В связи с тем, что работа DHCP в среде Azure в настоящее время не полностью соответствует стандарту RFC, при попытке создать отказоустойчивый кластер Windows Server (WSFC) может возникнуть ошибка. Дополнительные сведения можно найти в разделе «Поведение кластера WSFC в сети Azure» статьи «Высокий уровень доступности и аварийное восстановление для SQL Server в виртуальных машинах Azure». Однако существует обходное решение. Чтобы создать кластер, выполните перечисленные ниже действия.

1.	Войдите на виртуальную машину с сервером-источником SQL Server с учетной записью **sp_install**.
2.	На начальном экране введите **Отказоустойчивость** и щелкните **Диспетчер отказоустойчивости кластеров**.
3.	В левой области щелкните правой кнопкой элемент **Диспетчер отказоустойчивости кластеров** и выберите команду **Создать кластер**.
4.	На странице «Перед началом работы» нажмите кнопку **Далее**.
5.	На странице «Выбор серверов» введите имя виртуальной машины с сервером-источником SQL Server, нажмите кнопку **Добавить**, а затем — **Далее**.
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
17.	Чтобы добавить в кластер остальные узлы, щелкните правой кнопкой мыши его имя в левой области и выберите команду **Добавить узел**.
18.	На странице «Перед началом работы» нажмите кнопку **Далее**.
19.	На странице «Выбор серверов» добавьте в кластер сервер-получатель SQL Server и узел большинства (для каждого из них введите имя и нажмите кнопку **Добавить**). Добавив два компьютера, нажмите кнопку **Далее**. Если добавить машину не удается и появляется сообщение об ошибке «Служба удаленного реестра не запущена», выполните указанные ниже действия. Войдите на машину, откройте оснастку «Службы» (файл services.msc) и включите удаленный реестр. Дополнительные сведения см. в статье [Не удается подключиться к службе удаленного реестра](http://technet.microsoft.com/library/bb266998.aspx).
20.	На странице «Предупреждение проверки» выберите вариант **Нет. Для этого кластера не требуется поддержка Microsoft и, соответственно, не требуется выполнять проверочные тесты. После нажатия кнопки «Далее» продолжить создание кластера**, а затем нажмите кнопку **Далее**.
21.	На странице подтверждения нажмите кнопку **Далее**.
22.	На странице сводки нажмите кнопку **Готово**.
23.	В левой области щелкните **Узлы**. Вы должны увидеть в списке все три компьютера.

## Включение групп доступности AlwaysOn

Теперь необходимо включить группы доступности AlwaysOn с помощью диспетчера конфигурации SQL Server. Обратите внимание на то, что группа доступности SQL Server отличается от группы доступности Azure. Группа доступности SQL Server содержит высокодоступные базы данных с поддержкой восстановления. Группа доступности Azure распределяет виртуальные машины по разным доменам сбоя. Дополнительную информацию о доменах сбоя см. в статье [Управление доступностью виртуальных машин](virtual-machines-manage-availability.md).

Чтобы включить группы доступности AlwaysOn на сервере SQL Server, выполните указанные ниже действия.

1.	Войдите на сервер-источник SQL Server с учетной записью **sp_farm_db** или другой учетной записью, у которой есть на этом сервере роль sysadmin.
2.	На начальном экране введите **Конфигурация SQL Server** и выберите **Диспетчер конфигурации SQL Server**.
3.	В левой области выберите **Службы SQL Server**.
4.	В области содержимого дважды щелкните **SQL Server (MSSQLSERVER)**.
5.	В разделе **Свойства SQL Server (MSSQLSERVER)** откройте вкладку **Высокий уровень доступности AlwaysOn**, выберите **Включить группы доступности AlwaysOn**, нажмите кнопку **Применить**, а затем — кнопку **ОК**. Не закрывайте окно свойств.
6.	Откройте вкладку с настройками управления доступностью виртуальных машин и введите [Домен]**\sqlservice** в поле **Имя учетной записи**. Введите пароль учетной записи sqlservice в поля **Пароль** и **Подтверждение пароля**, а затем нажмите кнопку **ОК**.
7.	В окне с сообщением нажмите кнопку **Да**, чтобы перезапустить службу SQL Server.
8.	Войдите на сервер-получатель SQL Server и повторите эту процедуру.

На следующей схеме показана конфигурация после успешного выполнения этого этапа (используются заполнители имен компьютеров):

![](./media/virtual-machines-workload-intranet-sharepoint-phase3/workload-spsqlao_03.png)

## Дальнейшие действия

Дальнейшие действия по настройке этой среды см. в разделе [Этап 4. Настройка серверов SharePoint](virtual-machines-workload-intranet-sharepoint-phase4.md).

## Дополнительные ресурсы

[Развертывание среды SharePoint с группами доступности AlwaysOn для SQL Server на платформе Azure](virtual-machines-workload-intranet-sharepoint-overview.md)

[Фермы SharePoint, размещенные в службах инфраструктуры Azure](virtual-machines-sharepoint-infrastructure-services.md)

[Среда SharePoint с группами доступности AlwaysOn для SQL Server](http://go.microsoft.com/fwlink/?LinkId=394788)

[Архитектуры Microsoft Azure для SharePoint 2013](https://technet.microsoft.com/library/dn635309.aspx)

[Руководство по реализации служб инфраструктуры Azure](virtual-machines-infrastructure-services-implementation-guidelines.md)

<!---HONumber=July15_HO4-->
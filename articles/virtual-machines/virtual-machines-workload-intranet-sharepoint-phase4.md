<properties
	pageTitle="Ферма SharePoint в интрасети, этап 4: настройка серверов SharePoint"
	description="На четвертом этапе развертывания фермы SharePoint 2013 в интрасети создаются виртуальные машины с серверами SharePoint и новая ферма SharePoint."
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
	ms.date="07/22/2015"
	ms.author="josephd"/>

# Ферма SharePoint в интрасети, этап 4: настройка серверов SharePoint

На этом этапе развертывания фермы SharePoint 2013 в интрасети с группами доступности AlwaysOn для SQL Server на базе служб инфраструктуры Azure создаются уровень приложений и веб-уровень фермы и сама ферма с помощью мастера настройки SharePoint.

Этот этап необходимо выполнить, прежде чем переходить к [этапу 5](virtual-machines-workload-intranet-sharepoint-phase5.md). Описания всех этапов см. в разделе [Развертывание среды SharePoint с группами доступности AlwaysOn для SQL Server на платформе Azure](virtual-machines-workload-intranet-sharepoint-overview.md).

## Создание виртуальных машин серверов SharePoint в среде Azure

Для серверов SharePoint создаются четыре виртуальные машины. Две из них являются интерфейсными веб-серверами, а две используются для администрирования и размещения приложений SharePoint. Наличие двух серверов SharePoint на каждом уровне обеспечивает высокий уровень доступности.

С помощью приведенного ниже набора команд Azure PowerShell создайте виртуальные машины для четырех серверов SharePoint. Укажите значения переменных, удалив знаки < and >. Обратите внимание на то, что в наборе команд Azure PowerShell используются значения из перечисленных ниже таблиц.

- Таблица M (для виртуальных машин)
- Таблица V (для параметров виртуальной сети)
- Таблица S (для подсети)
- Таблица A (для групп доступности)
- Таблица C (для облачных служб)

Значения в таблице M были заданы на [этапе 2: настройка контроллеров домена](virtual-machines-workload-intranet-sharepoint-phase2.md), а в таблицах V, S, A и C — на [этапе 1: настройка Azure](virtual-machines-workload-intranet-sharepoint-phase1.md).

Указав все необходимые значения, выполните полученные команды в среде Azure PowerShell.

	# Create the first SharePoint application server
	$vmName="<Table M – Item 6 - Virtual machine name column>"
	$vmSize="<Table M – Item 6 - Minimum size column, specify one: Small, Medium, Large, ExtraLarge, A5, A6, A7, A8, A9>"
	$availSet="<Table A – Item 3 – Availability set name column>"
	$image= Get-AzureVMImage | where { $_.Label -eq "SharePoint Server 2013 Trial" } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1
	$vm1=New-AzureVMConfig -Name $vmName -InstanceSize $vmSize -ImageName $image -AvailabilitySetName $availSet

	$cred1=Get-Credential –Message "Type the name and password of the local administrator account for the first SharePoint application server."
	$cred2=Get-Credential –Message "Now type the name and password of an account that has permissions to add this virtual machine to the domain."
	$ADDomainName="<name of the AD domain that the server is joining (example CORP)>"
	$domainDNS="<FQDN of the AD domain that the server is joining (example corp.contoso.com)>"
	$vm1 | Add-AzureProvisioningConfig -AdminUsername $cred1.GetNetworkCredential().Username -Password $cred1.GetNetworkCredential().Password -WindowsDomain -Domain $ADDomainName -DomainUserName $cred2.GetNetworkCredential().Username -DomainPassword $cred2.GetNetworkCredential().Password -JoinDomain $domainDNS

	$subnetName="<Table 6 – Item 1 – Subnet name column>"
	$vm1 | Set-AzureSubnet -SubnetNames $subnetName

	$serviceName="<Table C – Item 3 – Cloud service name column>"
	$vnetName="<Table V – Item 1 – Value column>"
	New-AzureVM –ServiceName $serviceName -VMs $vm1 -VNetName $vnetName

	# Create the second SharePoint application server
	$vmName="<Table M – Item 7 - Virtual machine name column>"
	$vmSize="<Table M – Item 7 - Minimum size column, specify one: Small, Medium, Large, ExtraLarge, A5, A6, A7, A8, A9>"
	$vm1=New-AzureVMConfig -Name $vmName -InstanceSize $vmSize -ImageName $image -AvailabilitySetName $availSet

	$cred1=Get-Credential –Message "Type the name and password of the local administrator account for the second SharePoint application server."
	$vm1 | Add-AzureProvisioningConfig -AdminUsername $cred1.GetNetworkCredential().Username -Password $cred1.GetNetworkCredential().Password -WindowsDomain -Domain $ADDomainName -DomainUserName $cred2.GetNetworkCredential().Username -DomainPassword $cred2.GetNetworkCredential().Password -JoinDomain $domainDNS

	$vm1 | Set-AzureSubnet -SubnetNames $subnetName

	New-AzureVM –ServiceName $serviceName -VMs $vm1 -VNetName $vnetName

	# Create the first SharePoint web server
	$vmName="<Table M – Item 8 - Virtual machine name column>"
	$vmSize="<Table M – Item 8 - Minimum size column, specify one: Small, Medium, Large, ExtraLarge, A5, A6, A7, A8, A9>"
	$availSet="<Table A – Item 4 – Availability set name column>"
	$vm1=New-AzureVMConfig -Name $vmName -InstanceSize $vmSize -ImageName $image -AvailabilitySetName $availSet

	$cred1=Get-Credential –Message "Type the name and password of the local administrator account for the first SharePoint web server."
	$vm1 | Add-AzureProvisioningConfig -AdminUsername $cred1.GetNetworkCredential().Username -Password $cred1.GetNetworkCredential().Password -WindowsDomain -Domain $ADDomainName -DomainUserName $cred2.GetNetworkCredential().Username -DomainPassword $cred2.GetNetworkCredential().Password -JoinDomain $domainDNS

	$vm1 | Set-AzureSubnet -SubnetNames $subnetName

	New-AzureVM –ServiceName $serviceName -VMs $vm1 -VNetName $vnetName

	# Create the second SharePoint web server
	$vmName="<Table M – Item 9 - Virtual machine name column>"
	$vmSize="<Table M – Item 9 - Minimum size column, specify one: Small, Medium, Large, ExtraLarge, A5, A6, A7, A8, A9>"
	$vm1=New-AzureVMConfig -Name $vmName -InstanceSize $vmSize -ImageName $image -AvailabilitySetName $availSet

	$cred1=Get-Credential –Message "Type the name and password of the local administrator account for the second SharePoint web server."
	$vm1 | Add-AzureProvisioningConfig -AdminUsername $cred1.GetNetworkCredential().Username -Password $cred1.GetNetworkCredential().Password -WindowsDomain -Domain $ADDomainName -DomainUserName $cred2.GetNetworkCredential().Username -DomainPassword $cred2.GetNetworkCredential().Password -JoinDomain $domainDNS

	$vm1 | Set-AzureSubnet -SubnetNames $subnetName

	New-AzureVM –ServiceName $serviceName -VMs $vm1 -VNetName $vnetName

С помощью процедуры, описанной в разделе [Вход на виртуальную машину с использованием подключения к удаленному рабочему столу](virtual-machines-workload-intranet-sharepoint-phase2.md#logon), войдите на каждый из четырех серверов SharePoint с учетной записью [домен]\\sp\_farm\_db. Вы создали эти учетные данные на [этапе 2: настройка контроллеров домена](virtual-machines-workload-intranet-sharepoint-phase2.md).

На каждом из четырех серверов SharePoint с помощью процедуры [проверки подключения](virtual-machines-workload-intranet-sharepoint-phase2.md#testconn) убедитесь в наличии доступа к ресурсам в сети организации.

## Настройка фермы SharePoint

Чтобы настроить первый сервер SharePoint в ферме, выполните указанные ниже действия:

1.	На рабочем столе первого сервера приложений SharePoint дважды щелкните **мастер настройки продуктов SharePoint 2013**. Когда вам будет предложено разрешить программе вносить изменения на компьютере, нажмите кнопку **Да**.
2.	На странице **Продукты SharePoint** нажмите кнопку **Далее**.
3.	Откроется диалоговое окно **Мастер настройки продуктов SharePoint** с предупреждением о том, что службы (например, IIS) будут перезапущены или сброшены. Щелкните **Да**.
4.	На странице **Подключение к ферме серверов** выберите **Создать новую ферму серверов** и нажмите кнопку **Далее**.
5.	Откроется страница **Указание параметров базы данных конфигурации**.
 - В поле **Сервер базы данных** введите имя сервера-источника базы данных.
 - В поле **Имя пользователя** введите [домен]**\\sp\_farm\_db** (эта учетная запись была создана на [этапе 2: настройка контроллеров домена](virtual-machines-workload-intranet-sharepoint-phase2.md)). Обратите внимание, что у учетной записи sp\_farm\_db есть привилегии sysadmin на сервере базы данных.
 - В поле **Пароль** введите пароль к учетной записи sp\_farm\_db.
6.	Нажмите кнопку **Далее**.
7.	На странице **Задание параметров безопасности фермы** дважды введите парольную фразу. Запишите эту парольную фразу и сохраните ее в надежном месте. Нажмите кнопку **Далее**.
8.	На странице **Настройка веб-приложения центра администрирования SharePoint** щелкните **Далее**.
9.	Откроется страница **Выполнение мастера настройки продуктов SharePoint**. Нажмите кнопку **Далее**.
10.	Откроется страница **Настройка продуктов SharePoint**. Подождите, пока не завершится настройка (она занимает примерно 8 минут).
11.	Завершив настройку фермы, нажмите кнопку **Готово**. Будет запущен новый веб-сайт администрирования.
12.	Чтобы приступить к настройке фермы SharePoint, щелкните **Запуск мастера**.

Выполните описанную ниже процедуру на втором сервере приложений SharePoint и на двух интерфейсных веб-серверах.

1.	На рабочем столе дважды щелкните **мастер настройки продуктов SharePoint 2013**. Когда вам будет предложено разрешить программе вносить изменения на компьютере, нажмите кнопку **Да**.
2.	На странице **Продукты SharePoint** нажмите кнопку **Далее**.
3.	Откроется диалоговое окно **Мастер настройки продуктов SharePoint** с предупреждением о том, что службы (например, IIS) будут перезапущены или сброшены. Щелкните **Да**.
4.	На странице **Подключение к ферме серверов** выберите **Подключиться к существующей ферме серверов** и нажмите кнопку **Далее**.
5.	На странице **Указание параметров базы данных конфигурации** введите имя сервера-источника базы данных в поле **Сервер базы данных** и щелкните **Загрузить имена баз данных**.
6.	В списке имен баз данных выберите **SharePoint\_Config** и нажмите кнопку **Далее**.
7.	На странице **Задание параметров безопасности фермы** введите парольную фразу, которую задали в ходе предыдущей процедуры. Нажмите кнопку **Далее**.
8.	Откроется страница **Выполнение мастера настройки продуктов SharePoint**. Нажмите кнопку **Далее**.
9.	На странице **Настройка успешно завершена** нажмите кнопку **Готово**.

При создании фермы SharePoint настраивает набор серверных учетных данных на виртуальной машине сервера-источника SQL Server. В версии SQL Server 2012 введена концепция пользователей с паролями для доступа к автономным базам данных. В самой базе хранятся все ее метаданные и сведения о пользователях, а определенному в ней пользователю не требуется соответствующее имя для входа. Информация из этой базы данных реплицируется группой доступности и остается доступной после отработки отказа. Дополнительную информацию см. в статье [Автономные базы данных](http://go.microsoft.com/fwlink/p/?LinkId=262794).

Однако по умолчанию базы данных SharePoint не являются автономными. В связи с этим необходимо вручную настроить на сервере-получателе базы данных тот же набор учетных записей фермы SharePoint, что и на сервере-источнике базы данных. Синхронизацию можно выполнить в SQL Server Management Studio, подключившись одновременно к обоим серверам.

После завершения этой начальной настройки можно задать и другие параметры фермы SharePoint. Дополнительную информацию см. в статье [Планирование для SharePoint 2013 в службах инфраструктуры Azure](http://msdn.microsoft.com/library/dn275958.aspx).

## Настройка внутренней балансировки нагрузки

Внутренняя балансировка нагрузки настраивается для того, чтобы равномерно распределить клиентский трафик, поступающий на ферму SharePoint, между двумя интерфейсными веб-серверами. Для этого необходимо задать экземпляр подсистемы внутренней балансировки нагрузки, состоящий из имени и собственного IP-адреса из адресного пространства подсети. Определить, доступен ли IP-адрес, выбранный для внутренней подсистемы балансировки нагрузки, можно с помощью следующих команд Azure PowerShell:

	$vnet="<Table V – Item 1 – Value column>"
	$testIP="<a chosen IP address from the subnet address space, Table S - Item 1 – Subnet address space column>"
	Test-AzureStaticVNetIP –VNetName $vnet –IPAddress $testIP

Если в поле **IsAvailable** в данных, возвращаемых командой **Test-AzureStaticVNetIP**, стоит значение **True**, IP-адрес можно использовать.

В консоли Azure PowerShell на локальном компьютере выполните следующий набор команд:

	$serviceName="<Table C – Item 3 – Cloud service name column>"
	$ilb="<name of your internal load balancer instance>"
	$subnet="<Table S – Item 1 – Subnet name column>"
	$IP="<an available IP address for your ILB instance>"
	Add-AzureInternalLoadBalancer –ServiceName $serviceName -InternalLoadBalancerName $ilb –SubnetName $subnet –StaticVNetIPAddress $IP

	$prot="tcp"
	$locport=80
	$pubport=80
	# This example assumes unsecured HTTP traffic to the SharePoint farm.

	$epname="SPWeb1"
	$vmname="<Table M – Item 8 – Virtual machine name column>"
	Get-AzureVM –ServiceName $serviceName –Name $vmname | Add-AzureEndpoint -Name $epname -LBSetName $ilb -Protocol $prot -LocalPort $locport -PublicPort $pubport –DefaultProbe -InternalLoadBalancerName $ilb | Update-AzureVM

	$epname="SPWeb2"
	$vmname="<Table M – Item 9 – Virtual machine name column>"
	Get-AzureVM –ServiceName $serviceName –Name $vmname | Add-AzureEndpoint -Name $epname -LBSetName $ilb -Protocol $prot -LocalPort $locport -PublicPort $pubport –DefaultProbe -InternalLoadBalancerName $ilb | Update-AzureVM

Затем добавьте в инфраструктуру DNS своей организации запись DNS-адреса для сопоставления полного доменного имени фермы SharePoint (например, sp.corp.contoso.com) с IP-адресом, который назначен подсистеме внутренней балансировки нагрузки (значение **$IP** в блоке команд Azure PowerShell выше).

После успешного выполнения этого этапа у вас должна быть следующая конфигурация:

![](./media/virtual-machines-workload-intranet-sharepoint-phase4/workload-spsqlao_04.png)

## Дальнейшие действия

Дальнейшие действия по настройке этой среды см. в разделе [Этап 5: создание группы доступности и добавление баз данных SharePoint](virtual-machines-workload-intranet-sharepoint-phase5.md).

## Дополнительные ресурсы

[Развертывание среды SharePoint с группами доступности AlwaysOn для SQL Server на платформе Azure](virtual-machines-workload-intranet-sharepoint-overview.md)

[Фермы SharePoint, размещенные в службах инфраструктуры Azure](virtual-machines-sharepoint-infrastructure-services.md)

[Инфографика SharePoint с SQL Server AlwaysOn](http://go.microsoft.com/fwlink/?LinkId=394788)

[Архитектуры Microsoft Azure для SharePoint 2013](https://technet.microsoft.com/library/dn635309.aspx)

[Руководство по реализации служб инфраструктуры Azure](virtual-machines-infrastructure-services-implementation-guidelines.md)

<!---HONumber=August15_HO6-->
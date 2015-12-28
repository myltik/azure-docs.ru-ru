<properties
	pageTitle="Ферма SharePoint Server 2013, этап 4 | Microsoft Azure"
	description="На четвертом этапе развертывания фермы SharePoint Server 2013 в Azure создайте виртуальные машины SharePoint Server и новую ферму SharePoint."
	documentationCenter=""
	services="virtual-machines"
	authors="JoeDavies-MSFT"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="Windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="12/11/2015"
	ms.author="josephd"/>

# Ферма SharePoint в интрасети, этап 4: настройка серверов SharePoint

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]Классическая модель развертывания.

На этом этапе развертывания фермы SharePoint 2013 в интрасети с группами доступности AlwaysOn для SQL Server на базе служб инфраструктуры Azure создаются уровень приложений и веб-уровень фермы и сама ферма с помощью мастера настройки SharePoint.

Этот этап необходимо выполнить, прежде чем переходить к [этапу 5](virtual-machines-workload-intranet-sharepoint-phase5.md). Описания всех этапов см. в разделе [Развертывание среды SharePoint с группами доступности AlwaysOn для SQL Server на платформе Azure](virtual-machines-workload-intranet-sharepoint-overview.md).

## Создание виртуальных машин серверов SharePoint в среде Azure

Для серверов SharePoint создаются четыре виртуальные машины. Две из них являются интерфейсными веб-серверами, а две используются для администрирования и размещения приложений SharePoint. Наличие двух серверов SharePoint на каждом уровне обеспечивает высокий уровень доступности.

Сначала нужно настроить внутреннюю балансировку нагрузки, чтобы среда Azure равномерно распределяла трафик клиентов между двумя интерфейсными веб-серверами. Для этого необходимо задать экземпляр подсистемы внутренней балансировки нагрузки, состоящий из имени и собственного IP-адреса из адресного пространства подсети, которое было назначено вашей виртуальной сети Azure.

> [AZURE.NOTE]Следующая команда задает использование Azure PowerShell 1.0 и более поздней версии. Дополнительные сведения см. в статье [Azure PowerShell 1.0](https://azure.microsoft.com/blog/azps-1-0/).

Укажите значения переменных, удалив знаки < and >. Обратите внимание на то, что в приведенных наборах команд Azure PowerShell используются значения из следующих таблиц.

- Таблица M (для виртуальных машин)
- Таблица V (для параметров виртуальной сети)
- Таблица S (для подсети)
- Таблица ST (для учетных записей хранения)
- Таблица A (для групп доступности)

Значения в таблице M были заданы на [втором (настройка контроллеров домена)](virtual-machines-workload-intranet-sharepoint-phase2.md), а в таблицах V, S, ST и A — на [первом этапе (настройка Azure)](virtual-machines-workload-intranet-sharepoint-phase1.md).

Указав все необходимые значения, выполните полученные команды в среде Azure PowerShell.

	# Set up key variables
	$rgName="<resource group name>"
	$locName="<Azure location of your resource group>"
	$vnetName="<Table V – Item 1 – Value column>"
	$privIP="<available IP address on the subnet>"
	$vnet=Get-AzureRMVirtualNetwork -Name $vnetName -ResourceGroupName $rgName

	$frontendIP=New-AzureRMLoadBalancerFrontendIpConfig -Name SharePointWebServers-LBFE -PrivateIPAddress $privIP -SubnetId $vnet.Subnets[1].Id
	$beAddressPool=New-AzureRMLoadBalancerBackendAddressPoolConfig -Name SharePointWebServers-LBBE

	# This example assumes unsecured (HTTP-based) web traffic to the web servers.
	$healthProbe=New-AzureRMLoadBalancerProbeConfig -Name WebServersProbe -Protocol "TCP" -Port 80 -IntervalInSeconds 15 -ProbeCount 2
	$lbrule=New-AzureRMLoadBalancerRuleConfig -Name "WebTraffic" -FrontendIpConfiguration $frontendIP -BackendAddressPool $beAddressPool -Probe $healthProbe -Protocol "TCP" -FrontendPort 80 -BackendPort 80
	New-AzureRMLoadBalancer -ResourceGroupName $rgName -Name "SharePointWebServersInAzure" -Location $locName -LoadBalancingRule $lbrule -BackendAddressPool $beAddressPool -Probe $healthProbe -FrontendIpConfiguration $frontendIP

Затем добавьте во внутреннюю инфраструктуру DNS своей организации запись DNS-адреса для сопоставления полного доменного имени фермы SharePoint (например, spfarm.corp.contoso.com) с IP-адресом, который назначен внутреннему балансировщику нагрузки (значение $privIP в блоке команд Azure PowerShell выше).

С помощью приведенного ниже набора команд Azure PowerShell создайте виртуальные машины для четырех серверов SharePoint. Указав все необходимые значения, выполните полученные команды в среде Azure PowerShell.

	# Set up key variables
	$rgName="<resource group name>"
	$locName="<Azure location of your resource group>"
	$saName="<Table ST – Item 2 – Storage account name column>"
	$vnetName="<Table V – Item 1 – Value column>"
	$avName="<Table A – Item 3 – Availability set name column>"
	
	# Create the first application server
	$vmName="<Table M – Item 6 - Virtual machine name column>"
	$vmSize="<Table M – Item 6 - Minimum size column>"
	$vnet=Get-AzureRMVirtualNetwork -Name $vnetName -ResourceGroupName $rgName
	$nic=New-AzureRMNetworkInterface -Name ($vmName +"-NIC") -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[1].Id
	$avSet=Get-AzureRMAvailabilitySet –Name $avName –ResourceGroupName $rgName 
	$vm=New-AzureRMVMConfig -VMName $vmName -VMSize $vmSize -AvailabilitySetId $avset.Id
	$cred=Get-Credential -Message "Type the name and password of the local administrator account for the first application server." 
	$vm=Set-AzureRMVMOperatingSystem -VM $vm -Windows -ComputerName $vmName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
	$vm=Set-AzureRMVMSourceImage -VM $vm -PublisherName MicrosoftSharePoint -Offer MicrosoftSharePointServer -Skus 2013 -Version "latest"
	$vm=Add-AzureRMVMNetworkInterface -VM $vm -Id $nic.Id
	$storageAcc=Get-AzureRMStorageAccount -ResourceGroupName $rgName -Name $saName
	$osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/" + $vmName + "-OSDisk.vhd"
	$vm=Set-AzureRMVMOSDisk -VM $vm -Name "OSDisk" -VhdUri $osDiskUri -CreateOption fromImage
	New-AzureRMVM -ResourceGroupName $rgName -Location $locName -VM $vm

	# Create the second application server
	$vmName="<Table M – Item 7 - Virtual machine name column>"
	$vmSize="<Table M – Item 7 - Minimum size column>"
	$vnet=Get-AzureRMVirtualNetwork -Name $vnetName -ResourceGroupName $rgName
	$nic=New-AzureRMNetworkInterface -Name ($vmName +"-NIC") -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[1].Id
	$avSet=Get-AzureRMAvailabilitySet –Name $avName –ResourceGroupName $rgName 
	$vm=New-AzureRMVMConfig -VMName $vmName -VMSize $vmSize -AvailabilitySetId $avset.Id
	$cred=Get-Credential -Message "Type the name and password of the local administrator account for the second application server." 
	$vm=Set-AzureRMVMOperatingSystem -VM $vm -Windows -ComputerName $vmName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
	$vm=Set-AzureRMVMSourceImage -VM $vm -PublisherName MicrosoftSharePoint -Offer MicrosoftSharePointServer -Skus 2013 -Version "latest"
	$vm=Add-AzureRMVMNetworkInterface -VM $vm -Id $nic.Id
	$storageAcc=Get-AzureRMStorageAccount -ResourceGroupName $rgName -Name $saName
	$osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/" + $vmName + "-OSDisk.vhd"
	$vm=Set-AzureRMVMOSDisk -VM $vm -Name "OSDisk" -VhdUri $osDiskUri -CreateOption fromImage
	New-AzureRMVM -ResourceGroupName $rgName -Location $locName -VM $vm

	# Change the availability set
	$avName="<Table A – Item 4 – Availability set name column>"

	# Set up key variables
	$beSubnetName="<Table S - Item 2 - Name column>"
	$webLB=Get-AzureRMLoadBalancer -ResourceGroupName $rgName -Name "SharePointWebServersInAzure"	
	$vnet=Get-AzureRMVirtualNetwork -Name $vnetName -ResourceGroupName $rgName
	$backendSubnet=Get-AzureRMVirtualNetworkSubnetConfig -Name $beSubnetName -VirtualNetwork $vnet
	
	# Create the first front end web server virtual machine
	$vmName="<Table M – Item 8 - Virtual machine name column>"
	$vmSize="<Table M – Item 8 - Minimum size column>"
	$nic=New-AzureRMNetworkInterface -Name ($vmName + "-NIC") -ResourceGroupName $rgName -Location $locName -Subnet $backendSubnet -LoadBalancerBackendAddressPool $webLB.BackendAddressPools[0]
	$avSet=Get-AzureRMAvailabilitySet -Name $avName –ResourceGroupName $rgName 
	$vm=New-AzureRMVMConfig -VMName $vmName -VMSize $vmSize -AvailabilitySetId $avset.Id
	$cred=Get-Credential -Message "Type the name and password of the local administrator account for the first front end web server." 
	$vm=Set-AzureRMVMOperatingSystem -VM $vm -Windows -ComputerName $vmName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
	$vm=Set-AzureRMVMSourceImage -VM $vm -PublisherName MicrosoftSharePoint -Offer MicrosoftSharePointServer -Skus 2013 -Version "latest"
	$vm=Add-AzureRMVMNetworkInterface -VM $vm -Id $nic.Id
	$storageAcc=Get-AzureRMStorageAccount -ResourceGroupName $rgName -Name $saName
	$osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/" + $vmName + "-OSDisk.vhd"
	$vm=Set-AzureRMVMOSDisk -VM $vm -Name "OSDisk" -VhdUri $osDiskUri -CreateOption fromImage
	New-AzureRMVM -ResourceGroupName $rgName -Location $locName -VM $vm
	
	# Create the second front end web server virtual machine
	$vmName="<Table M – Item 9 - Virtual machine name column>"
	$vmSize="<Table M – Item 9 - Minimum size column>"
	$nic=New-AzureRMNetworkInterface -Name ($vmName + "-NIC") -ResourceGroupName $rgName -Location $locName -Subnet $backendSubnet -LoadBalancerBackendAddressPool $webLB.BackendAddressPools[0]
	$vm=New-AzureRMVMConfig -VMName $vmName -VMSize $vmSize -AvailabilitySetId $avset.Id
	$cred=Get-Credential -Message "Type the name and password of the local administrator account for the second front end web server." 
	$vm=Set-AzureRMVMOperatingSystem -VM $vm -Windows -ComputerName $vmName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
	$vm=Set-AzureRMVMSourceImage -VM $vm -PublisherName MicrosoftSharePoint -Offer MicrosoftSharePointServer -Skus 2013 -Version "latest"
	$vm=Add-AzureRMVMNetworkInterface -VM $vm -Id $nic.Id
	$storageAcc=Get-AzureRMStorageAccount -ResourceGroupName $rgName -Name $saName
	$osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/" + $vmName + "-OSDisk.vhd"
	$vm=Set-AzureRMVMOSDisk -VM $vm -Name "OSDisk" -VhdUri $osDiskUri -CreateOption fromImage
	New-AzureRMVM -ResourceGroupName $rgName -Location $locName -VM $vm

> [AZURE.NOTE]Так как эти виртуальные машины предназначены для приложения интрасети, им не назначается общедоступный IP-адрес, у них нет метки DNS-имени домена и они не доступны в Интернете. Однако это также означает, что к ним невозможно подключиться с помощью портала Azure. Кнопка **Подключить** будет недоступна при просмотре свойств виртуальной машины.

Используя предпочитаемый клиент удаленного рабочего стола, создайте подключение к удаленному рабочему столу каждой виртуальной машины. Для подключения используйте DNS интрасети контроллера домена или имя компьютера, а также учетные данные локальной учетной записи администратора.

Присоедините все виртуальные машины к соответствующему домену AD DS, выполнив на каждой машине следующие команды в командной строке Windows PowerShell.

	$domName="<Active Directory domain name to join, such as corp.contoso.com>"
	Add-Computer -DomainName $domName
	Restart-Computer

Обратите внимание, что после ввода команды **Add-Computer** необходимо ввести данные для входа в учетную запись домена.

После их перезапуска, следуя процедуре, описанной в разделе [Вход на виртуальную машину с помощью подключения удаленного рабочего стола](virtual-machines-workload-intranet-sharepoint-phase2.md#logon), войдите на каждый из четырех серверов SharePoint с учетной записью [домен]\\sp\_farm\_db. Вы создали эти учетные данные на [втором этапе (настройка контроллеров домена)](virtual-machines-workload-intranet-sharepoint-phase2.md).

На каждом из четырех серверов SharePoint с помощью процедуры [проверки подключения](virtual-machines-workload-intranet-sharepoint-phase2.md#testconn) убедитесь в наличии доступа к ресурсам в сети организации.

> [AZURE.NOTE]Серверы SharePoint создаются на основе образа пробной версии SharePoint Server 2013. Необходимо перенастроить экземпляр, указав ключ розничной или корпоративной лицензии на выпуск Standard или Enterprise сервера SharePoint Server 2013.

## Настройка фермы SharePoint

Чтобы настроить первый сервер SharePoint в ферме, выполните указанные ниже действия:

1.	На рабочем столе первого сервера приложений SharePoint дважды щелкните **мастер настройки продуктов SharePoint 2013**. Когда вам будет предложено разрешить программе вносить изменения на компьютере, нажмите кнопку **Да**.
2.	На странице **Продукты SharePoint** нажмите кнопку **Далее**.
3.	Откроется диалоговое окно **Мастер настройки продуктов SharePoint** с предупреждением о том, что службы (например, IIS) будут перезапущены или сброшены. Щелкните **Да**.
4.	На странице **Подключение к ферме серверов** выберите **Создать новую ферму серверов** и нажмите кнопку **Далее**.
5.	Откроется страница **Указание параметров базы данных конфигурации**.
 - В поле **Сервер базы данных** введите имя сервера базы данных-источника.
 - В поле **Имя пользователя** введите [домен]**\\sp\_farm\_db** (эта учетная запись была создана на [втором этапе (настройка контроллеров домена))](virtual-machines-workload-intranet-sharepoint-phase2.md). Обратите внимание, что у учетной записи sp\_farm\_db есть привилегии sysadmin на сервере базы данных.
 - В поле **Пароль** введите пароль к учетной записи sp\_farm\_db.
6.	Нажмите кнопку **Далее**.
7.	На странице **Задание параметров безопасности фермы** дважды введите парольную фразу. Запишите эту парольную фразу и сохраните ее в надежном месте. Нажмите кнопку **Далее**.
8.	На странице **Настроить веб-приложение центра администрирования SharePoint** нажмите кнопку **Далее**.
9.	Откроется страница **Выполнение мастера настройки продуктов SharePoint**. Нажмите кнопку **Далее**.
10.	Откроется страница **Настройка продуктов SharePoint**. Подождите, пока не завершится настройка (она занимает примерно 8 минут).
11.	Завершив настройку фермы, нажмите кнопку **Готово**. Будет запущен новый веб-сайт администрирования.
12.	Чтобы приступить к настройке фермы SharePoint, щелкните **Запуск мастера**.

Выполните описанную ниже процедуру на втором сервере приложений SharePoint и на двух интерфейсных веб-серверах.

1.	На рабочем столе дважды щелкните **мастер настройки продуктов SharePoint 2013**. Когда вам будет предложено разрешить программе вносить изменения на компьютере, нажмите кнопку **Да**.
2.	На странице **Продукты SharePoint** нажмите кнопку **Далее**.
3.	Откроется диалоговое окно **Мастер настройки продуктов SharePoint** с предупреждением о том, что службы (например, IIS) будут перезапущены или сброшены. Щелкните **Да**.
4.	На странице **Подключение к ферме серверов** выберите **Подключиться к существующей ферме серверов** и нажмите кнопку **Далее**.
5.	На странице **Указание параметров базы данных конфигурации** введите имя сервера базы данных-источника в поле **Сервер базы данных** и щелкните **Загрузить имена баз данных**.
6.	В списке имен баз данных выберите **SharePoint\_Config** и нажмите кнопку **Далее**.
7.	На странице **Задание параметров безопасности фермы** введите парольную фразу, которую задали в ходе предыдущей процедуры. Нажмите кнопку **Далее**.
8.	Откроется страница **Выполнение мастера настройки продуктов SharePoint**. Нажмите кнопку **Далее**.
9.	На странице **Настройка успешно завершена** нажмите кнопку **Готово**.

При создании фермы SharePoint настраивает набор серверных учетных данных на виртуальной машине сервера-источника SQL Server. В версии SQL Server 2012 введена концепция пользователей с паролями для доступа к автономным базам данных. В самой базе хранятся все ее метаданные и сведения о пользователях, а определенному в ней пользователю не требуется соответствующее имя для входа. Информация из этой базы данных реплицируется группой доступности и остается доступной после отработки отказа. Дополнительные сведения см. в статье [Автономные базы данных](http://go.microsoft.com/fwlink/p/?LinkId=262794).

Однако по умолчанию базы данных SharePoint не являются автономными. В связи с этим необходимо вручную настроить на сервере-получателе базы данных тот же набор учетных записей фермы SharePoint, что и на сервере-источнике базы данных. Синхронизацию можно выполнить в SQL Server Management Studio, подключившись одновременно к обоим серверам.

После завершения этой начальной настройки можно задать и другие параметры фермы SharePoint. Дополнительные сведения см. в статье [Планирование для SharePoint 2013 в службах инфраструктуры Azure](http://msdn.microsoft.com/library/dn275958.aspx).

После успешного выполнения этого этапа у вас должна быть следующая конфигурация.

![](./media/virtual-machines-workload-intranet-sharepoint-phase4/workload-spsqlao_04.png)

## Дальнейшие действия

- Чтобы продолжить настройку этой рабочей нагрузки, см. [этап 5](virtual-machines-workload-intranet-sharepoint-phase5.md).

<!---HONumber=AcomDC_1217_2015-->
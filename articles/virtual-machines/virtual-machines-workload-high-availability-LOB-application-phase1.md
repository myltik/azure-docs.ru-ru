<properties 
	pageTitle="Бизнес-приложение, этап 1 | Microsoft Azure"
	description="На первом этапе развертывания бизнес-приложения в Azure создайте виртуальную сеть и другие элементы инфраструктуры Azure."
	documentationCenter=""
	services="virtual-machines"
	authors="JoeDavies-MSFT"
	manager="timlt"
	editor=""/>

<tags 
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/11/2015"
	ms.author="josephd"/>

# Первый этап развертывания бизнес-приложений: настройка Azure

На этом этапе развертывания высокодоступного бизнес-приложения в интрасети на базе служб инфраструктуры Azure вы создадите инфраструктуру сети и хранения Azure. Его необходимо выполнить, прежде чем переходить к [этапу 2](virtual-machines-workload-high-availability-LOB-application-phase2.md). Все этапы перечислены в статье [Развертывание высокодоступных бизнес-приложений в Azure](virtual-machines-workload-high-availability-LOB-application-overview.md).

В среде Azure необходимо подготовить такие основные сетевые компоненты:

- распределенная виртуальная сеть с одной подсетью для размещения виртуальных машин Azure;
- две учетные записи хранения Azure для хранения образов виртуальных жестких дисков (VHD) и дополнительных дисков данных;
- три группы доступности.

## Перед началом работы

Прежде чем приступить к настройке компонентов Azure, заполните перечисленные ниже таблицы. Чтобы упростить себе настройку Azure, распечатайте этот раздел и запишите всю необходимую информацию либо скопируйте его в отдельный документ и заполните.

Параметры виртуальной сети заполняются в таблице V.

Элемент | Элемент конфигурации | Описание | Значение 
--- | --- | --- | --- 
1\. | Имя виртуальной сети | Имя, назначаемое виртуальной сети Azure (пример: SPFarmNet). | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_
2\. | Расположение виртуальной сети | Центр обработки данных Azure, в котором будет находиться виртуальная сеть. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_
3\. | Имя локальной сети | Имя, назначаемое сети вашей организации. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_
4\. | IP-адрес VPN-устройства | Общедоступный IPv4-адрес интерфейса VPN-устройства в Интернете. Уточните этот адрес у специалистов своего ИТ-отдела. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_
5\. | Адресное пространство виртуальной сети | Адресное пространство (один префикс частного адреса) виртуальной сети. Уточните это адресное пространство у специалистов своего ИТ-отдела. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_
6\. | Первый DNS-сервер для виртуальной сети | Четвертый возможный IP-адрес адресного пространства второй подсети виртуальной сети (см. таблицу S). Уточните этот адрес у специалистов своего ИТ-отдела. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_
7\. | Второй DNS-сервер для виртуальной сети | Пятый возможный IP-адрес адресного пространства второй подсети виртуальной сети (см. таблицу S). Уточните этот адрес у специалистов своего ИТ-отдела. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_
8\. | Общий ключ IPsec | Строка из 128 случайных букв и цифр для проверки подлинности с обеих сторон VPN-подключения типа «сеть — сеть». Значение ключа можно получить у специалистов вашего ИТ-отдела или отдела безопасности данных. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_


**Таблица V: конфигурация распределенной виртуальной сети**

Заполните таблицу S параметрами подсети в рамках этого решения.

- В первой подсети определите для подсети шлюза Azure 29-разрядное адресное пространство (с длиной префикса 29).
- Для второй подсети укажите понятное имя, одно пространство IP-адресов в рамках адресного пространства виртуальной сети, а также описание. 

Уточните эти адресные пространства (на основе адресного пространства виртуальной сети) у специалистов своего ИТ-отдела. Оба адресных пространства должны быть указаны в формате CIDR (формат префикса сети). Пример: 10.24.64.0/20.

Элемент | Имя подсети | Адресное пространство подсети | Назначение 
--- | --- | --- | --- 
1\. | Подсеть шлюза | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_ | Подсеть, используемая виртуальными машинами шлюза Azure.
2\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_ | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_ | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_

**Таблица S: подсети виртуальной сети**

> [AZURE.NOTE]Для простоты в этой готовой архитектуре используется одна подсеть. Чтобы эмулировать изоляцию подсетей с помощью набора перекрывающихся фильтров трафика, воспользуйтесь [группами безопасности сети](virtual-networks-nsg.md) Azure.

Заполните таблицу D для двух локальных DNS-серверов, которые планируете использовать на этапе начальной настройки контроллеров домена виртуальной сети. Задайте для каждого из них понятное имя и один IP-адрес. Понятное имя не обязано совпадать с именем узла или компьютера DNS-сервера. В списке предлагаются две записи, однако их число можно увеличить. Составьте его вместе со специалистами своего ИТ-отдела.

Элемент | Понятное имя DNS-сервера | IP-адрес DNS-сервера 
--- | --- | ---
1\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_ | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_
2\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_ | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_ 

**Таблица D: локальные DNS-серверы**

Для маршрутизации пакетов из виртуальной сети Azure в сеть организации через VPN-подключение типа «сеть — сеть» настройте локальную сеть в виртуальной сети. Она должна содержать список адресных пространств (в формате CIDR) для всех расположений, доступных из сети вашей организации. Список адресных пространств, определяющих вашу локальную сеть, должен быть уникальным. Он не должен пересекаться с адресными пространствами других виртуальных и локальных сетей.

Параметры адресных пространств локальной сети задаются в таблице L. Обратите внимание, что она содержит место для трех записей, хотя в реальности их, скорее всего, потребуется больше. Составьте этот список адресных пространств вместе со специалистами своего ИТ-отдела.

Элемент | Адресное пространство локальной сети 
--- | ---
1\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_
2\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_
3\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_

**Таблица L: префиксы адресов локальной сети**

Далее вам потребуется установить Azure PowerShell версии 0.9.5 или выше. Чтобы узнать версию Azure PowerShell, выполните следующую команду.

	Get-Module azure | format-table version

Если вы хотите установить последнюю версию Azure PowerShell, сначала удалите текущую версию через **Панель управления — Программы и компоненты**. Затем установите Azure PowerShell на локальный компьютер, следуя указаниям в статье [Установка и настройка Azure PowerShell](../install-configure-powershell.md). Откройте командную строку Azure PowerShell.

Сначала выберите правильную подписку Azure с помощью следующих команд: Замените все содержимое внутри кавычек, включая символы < and >, на правильные имена.

	$subscr="<Subscription name>"
	Select-AzureSubscription -SubscriptionName $subscr –Current

Уточнить имя подписки можно в свойстве **SubscriptionName**, которое возвращает команда **Get-AzureSubscription**.

Перейдите в режим диспетчера ресурсов Azure PowerShell, выполнив следующую команду.

	Switch-AzureMode AzureResourceManager 

Затем создайте группу ресурсов для своего бизнес-приложения.

Чтобы вывести список существующих групп ресурсов и выбрать уникальное имя для новой группы, используйте эту команду.

	Get-AzureResourceGroup | Sort ResourceGroupName | Select ResourceGroupName

Следующие команды помогут получить список расположений Azure, в которых можно создать виртуальные машины на основе диспетчера ресурсов.

	$loc=Get-AzureLocation | where { $_.Name –eq "Microsoft.Compute/virtualMachines" }
	$loc.Locations

Создайте группу ресурсов, выполнив следующие команды.

	$rgName="<resource group name>"
	$locName="<an Azure location, such as West US>"
	New-AzureResourceGroup -Name $rgName -Location $locName

Для виртуальных машин на основе диспетчера ресурсов требуется учетная запись хранения на основе диспетчера ресурсов.

Элемент | Имя учетной записи хранения | Назначение 
--- | --- | ---
1\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_ | Учетная запись хранилища класса Premium, используемая виртуальными машинами SQL Server.
2\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_ | Учетная запись хранилища класса Standard, используемая всеми другими виртуальными машинами в рабочей нагрузке. 

**Таблица ST. Учетные записи хранения**

Эти имена понадобятся при создании виртуальных машин на этапах 2, 3 и 4.

Для каждой учетной записи хранения вам нужно выбрать глобально уникальное имя, содержащее только строчные буквы и цифры. Чтобы вывести список существующих учетных записей хранения, используйте эту команду.

	Get-AzureStorageAccount | Sort Name | Select Name

Чтобы убедиться, что выбранное имя учетной записи хранения глобально уникально, запустите команду **Test-AzureName** в режиме управления службами Azure PowerShell. Используйте следующие команды.

	Switch-AzureMode AzureServiceManagement
	Test-AzureName -Storage <Proposed storage account name>

Предложенное имя уникально, если в результате выполнения команды Test-AzureName отображается значение **False**. Выбрав уникальные имена для обеих учетных записей, обновите таблицу ST и вернитесь в режим диспетчера ресурсов Azure PowerShell, используя следующую команду.

	Switch-AzureMode AzureResourceManager 

Чтобы создать первую учетную запись хранения, выполните следующие команды.

	$rgName="<your new resource group name>"
	$locName="<the location of your new resource group>"
	$saName="<Table ST – Item 1 - Storage account name column>"
	New-AzureStorageAccount -Name $saName -ResourceGroupName $rgName –Type Premium_LRS -Location $locName

Чтобы создать вторую учетную запись хранения, выполните следующие команды.

	$rgName="<your new resource group name>"
	$locName="<the location of your new resource group>"
	$saName="<Table ST – Item 2 - Storage account name column>"
	New-AzureStorageAccount -Name $saName -ResourceGroupName $rgName –Type Standard_LRS -Location $locName

Создайте виртуальную сеть Azure, в которой будет размещено ваше бизнес-приложение.

	$rgName="<name of your new resource group>"
	$locName="<Azure location of the new resource group>"
	$vnetName="<Table V – Item 1 – Value column>"
	$vnetAddrPrefix="<Table V – Item 5 – Value column>"
	$lobSubnetName="<Table S – Item 2 – Subnet name column>"
	$lobSubnetPrefix="<Table S – Item 2 – Subnet address space column>"
	$gwSubnetPrefix="<Table S – Item 1 – Subnet address space column>"
	$dnsServers=@( "<Table D – Item 1 – DNS server IP address column>", "<Table D – Item 2 – DNS server IP address column>" )
	$gwSubnet=New-AzureVirtualNetworkSubnetConfig -Name "GatewaySubnet" -AddressPrefix $gwSubnetPrefix
	$lobSubnet=New-AzureVirtualNetworkSubnetConfig -Name $lobSubnetName -AddressPrefix $lobSubnetPrefix
	New-AzurevirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $locName -AddressPrefix $vnetAddrPrefix -Subnet $gwSubnet,$lobSubnet -DNSServer $dnsServers

Создайте шлюзы для VPN-подключения типа «сеть — сеть», используя следующие команды.

	$vnetName="<Table V – Item 1 – Value column>"
	$vnet=Get-AzureVirtualNetwork -Name $vnetName -ResourceGroupName $rgName
	
	# Attach a virtual network gateway to a public IP address and the gateway subnet
	$publicGatewayVipName="LOBAppPublicIPAddress"
	$vnetGatewayIpConfigName="LOBAppPublicIPConfig"
	New-AzurePublicIpAddress -Name $vnetGatewayIpConfigName -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic
	$publicGatewayVip=Get-AzurePublicIpAddress -Name $vnetGatewayIpConfigName -ResourceGroupName $rgName
	$vnetGatewayIpConfig=New-AzureVirtualNetworkGatewayIpConfig -Name $vnetGatewayIpConfigName -PublicIpAddressId $publicGatewayVip.Id -SubnetId $vnet.Subnets[0].Id

	# Create the Azure gateway
	$vnetGatewayName="LOBAppAzureGateway"
	$vnetGateway=New-AzureVirtualNetworkGateway -Name $vnetGatewayName -ResourceGroupName $rgName -Location $locName -GatewayType Vpn -VpnType RouteBased -IpConfigurations $vnetGatewayIpConfig
	
	# Create the gateway for the local network
	$localGatewayName="LOBAppLocalNetGateway"
	$localGatewayIP="<Table V – Item 4 – Value column>"
	$localNetworkPrefix=@( <comma-separated, double-quote enclosed list of the local network address prefixes from Table L, example: "10.1.0.0/24", "10.2.0.0/24"> )
	$localGateway=New-AzureLocalNetworkGateway -Name $localGatewayName -ResourceGroupName $rgName -Location $locName -GatewayIpAddress $localGatewayIP -AddressPrefix $localNetworkPrefix
	
	# Define the Azure virtual network VPN connection
	$vnetConnectionName="LOBAppS2SConnection"
	$vnetConnectionKey="<Table V – Item 8 – Value column>"
	$vnetConnection=New-AzureVirtualNetworkGatewayConnection -Name $vnetConnectionName -ResourceGroupName $rgName -Location $locName -ConnectionType IPsec -SharedKey $vnetConnectionKey -VirtualNetworkGateway1 $vnetGateway -LocalNetworkGateway2 $localGateway

Настройте локальное VPN-устройство для подключения к VPN-шлюзу Azure. Дополнительные сведения см. в статье [Настройка VPN-устройства](../virtual-networks/vpn-gateway-configure-vpn-gateway-mp.md#configure-your-vpn-device).

Чтобы настроить локальное VPN-устройство, вам потребуются:

- общедоступный IPv4-адрес VPN-шлюза Azure для виртуальной сети (можно узнать, выполнив команду **Get-AzurePublicIpAddress -Name $publicGatewayVipName -ResourceGroupName $rgName**);
- общий ключ IPsec для VPN-подключения типа «сеть — сеть» (в таблице V в столбце значений см. позицию 8).

Затем убедитесь в том, что адресное пространство виртуальной сети доступно из вашей локальной сети. Для этого на VPN-устройство обычно добавляется маршрут, соответствующий адресному пространству вашей виртуальной сети, который затем объявляется остальным элементам инфраструктуры маршрутизации корпоративной сети. За дополнительными инструкциями обратитесь к специалистам своего ИТ-отдела.

Задайте имена трех групп доступности. Заполните таблицу A.

Элемент | Назначение | Имя группы доступности 
--- | --- | --- 
1\. | Контроллеры домена | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_
2\. | Серверы SQL Server | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_
3\. | Веб-серверы | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_

**Таблица A: имена групп доступности**

Эти имена понадобятся при создании виртуальных машин на этапах 2, 3 и 4.

Создайте перечисленные наборы доступности, используя следующие команды Azure PowerShell.

	$rgName="<your new resource group name>"
	$locName="<the Azure location for your new resource group>"
	$avName="<Table A – Item 1 – Availability set name column>"
	New-AzureAvailabilitySet –Name $avName –ResourceGroupName $rgName -Location $locName
	$avName="<Table A – Item 2 – Availability set name column>"
	New-AzureAvailabilitySet –Name $avName –ResourceGroupName $rgName -Location $locName
	$avName="<Table A – Item 3 – Availability set name column>"
	New-AzureAvailabilitySet –Name $avName –ResourceGroupName $rgName -Location $locName

После успешного выполнения этого этапа у вас должна быть следующая конфигурация:

![](./media/virtual-machines-workload-high-availability-LOB-application-phase1/workload-lobapp-phase1.png)

## Дальнейшие действия

Дальнейшие действия по настройке этой рабочей нагрузки см. в разделе [Этап 2. Настройка контроллеров домена](virtual-machines-workload-high-availability-LOB-application-phase2.md).

## Дополнительные ресурсы

[Развертывание высокодоступных бизнес-приложений в Azure](virtual-machines-workload-high-availability-LOB-application-overview.md)

[Архитектурный проект бизнес-приложений](http://msdn.microsoft.com/dn630664)

[Настройка веб-бизнес-приложения в гибридном облаке для тестирования](../virtual-network/virtual-networks-setup-lobapp-hybrid-cloud-testing.md)

[Руководство по реализации служб инфраструктуры Azure](virtual-machines-infrastructure-services-implementation-guidelines.md)

[Рабочая нагрузка служб инфраструктуры Azure: ферма SharePoint Server 2013](virtual-machines-workload-intranet-sharepoint-farm.md)

<!---HONumber=August15_HO9-->
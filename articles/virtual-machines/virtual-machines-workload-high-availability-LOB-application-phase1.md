<properties 
	pageTitle="Бизнес-приложение, этап 1 | Microsoft Azure" 
	description="На первом этапе развертывания бизнес-приложения в Azure создайте виртуальную сеть и другие элементы инфраструктуры Azure." 
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
	ms.date="01/21/2016" 
	ms.author="josephd"/>

# Первый этап развертывания бизнес-приложений: настройка Azure
 
[AZURE.INCLUDE [learn-about-deployment-models-rm-include](../../includes/learn-about-deployment-models-rm-include.md)]Классическая модель развертывания.
 
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
8\. | Общий ключ IPsec | Строка из 32 случайных букв и цифр для проверки подлинности с обеих сторон VPN-подключения типа «сеть — сеть». Значение ключа можно получить у специалистов вашего ИТ-отдела или отдела безопасности данных. Кроме того, см. статью [Создание случайной строки для общего ключа IPsec](http://social.technet.microsoft.com/wiki/contents/articles/32330.create-a-random-string-for-an-ipsec-preshared-key.aspx).| \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_

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

> [AZURE.NOTE] Для простоты в этой готовой архитектуре используется одна подсеть. Чтобы эмулировать изоляцию подсетей с помощью набора перекрывающихся фильтров трафика, воспользуйтесь [группами безопасности сети](virtual-networks-nsg.md) Azure.

Заполните таблицу D для двух локальных DNS-серверов, которые планируете использовать на этапе начальной настройки контроллеров домена виртуальной сети. Задайте для каждого из них понятное имя и один IP-адрес. Понятное имя не обязано совпадать с именем узла или компьютера DNS-сервера. В списке предлагаются две записи, однако их число можно увеличить. Составьте его вместе со специалистами своего ИТ-отдела.

Элемент | IP-адрес DNS-сервера 
--- | ---
1\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_
2\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_ 

**Таблица D: локальные DNS-серверы**

Для маршрутизации пакетов из виртуальной сети Azure в сеть организации через VPN-подключение типа «сеть — сеть» настройте локальную сеть в виртуальной сети. Она должна содержать список адресных пространств (в формате CIDR) для всех расположений, доступных из сети вашей организации. Список адресных пространств, определяющих вашу локальную сеть, должен быть уникальным. Он не должен пересекаться с адресными пространствами других виртуальных и локальных сетей.

Параметры адресных пространств локальной сети задаются в таблице L. Обратите внимание, что она содержит место для трех записей, хотя в реальности их, скорее всего, потребуется больше. Составьте этот список адресных пространств вместе со специалистами своего ИТ-отдела.

Элемент | Адресное пространство локальной сети 
--- | ---
1\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_
2\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_
3\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_

**Таблица L: префиксы адресов локальной сети**

Запустите командную строку Azure PowerShell.

> [AZURE.NOTE] Следующая команда задает использование Azure PowerShell 1.0 и более поздней версии. Дополнительные сведения см. в статье [Azure PowerShell 1.0](https://azure.microsoft.com/blog/azps-1-0/).

Сначала запустите командную строку Azure PowerShell и войдите в свою учетную запись.

	Login-AzureRMAccount

Получите имя своей подписки, выполнив указанную ниже команду.

	Get-AzureRMSubscription | Sort SubscriptionName | Select SubscriptionName

Настройте свою подписку Azure. Замените все содержимое внутри кавычек, включая символы < and >, на правильные имена.

	$subscr="<subscription name>"
	Get-AzureRmSubscription –SubscriptionName $subscr | Select-AzureRmSubscription

Затем создайте группу ресурсов для своего бизнес-приложения. Чтобы вывести список существующих групп ресурсов и выбрать уникальное имя для новой группы, используйте эту команду.

	Get-AzureRMResourceGroup | Sort ResourceGroupName | Select ResourceGroupName

Создайте группу ресурсов, выполнив следующие команды.

	$rgName="<resource group name>"
	$locName="<an Azure location, such as West US>"
	New-AzureRMResourceGroup -Name $rgName -Location $locName

Для виртуальных машин на основе диспетчера ресурсов требуется учетная запись хранения на основе диспетчера ресурсов.

Элемент | Имя учетной записи хранения | Назначение 
--- | --- | ---
1\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_ | Учетная запись хранилища класса Premium, используемая виртуальными машинами SQL Server.
2\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_ | Учетная запись хранилища класса Standard, используемая всеми другими виртуальными машинами в рабочей нагрузке. 

**Таблица ST. Учетные записи хранения**

Эти имена понадобятся при создании виртуальных машин на этапах 2, 3 и 4.

Для каждой учетной записи хранения вам нужно выбрать глобально уникальное имя, содержащее только строчные буквы и цифры. Чтобы вывести список существующих учетных записей хранения, используйте эту команду.

	Get-AzureRMStorageAccount | Sort StorageAccountName | Select StorageAccountName

Чтобы создать первую учетную запись хранения, выполните следующие команды.

	$rgName="<your new resource group name>"
	$locName="<the location of your new resource group>"
	$saName="<Table ST – Item 1 - Storage account name column>"
	New-AzureRMStorageAccount -Name $saName -ResourceGroupName $rgName –Type Premium_LRS -Location $locName

Чтобы создать вторую учетную запись хранения, выполните следующие команды.

	$saName="<Table ST – Item 2 - Storage account name column>"
	New-AzureRMStorageAccount -Name $saName -ResourceGroupName $rgName –Type Standard_LRS -Location $locName

Создайте виртуальную сеть Azure, в которой будет размещено ваше бизнес-приложение.

	$rgName="<name of your new resource group>"
	$locName="<Azure location of the new resource group>"
	$vnetName="<Table V – Item 1 – Value column>"
	$vnetAddrPrefix="<Table V – Item 5 – Value column>"
	$lobSubnetName="<Table S – Item 2 – Subnet name column>"
	$lobSubnetPrefix="<Table S – Item 2 – Subnet address space column>"
	$gwSubnetPrefix="<Table S – Item 1 – Subnet address space column>"
	$dnsServers=@( "<Table D – Item 1 – DNS server IP address column>", "<Table D – Item 2 – DNS server IP address column>" )
	$gwSubnet=New-AzureRMVirtualNetworkSubnetConfig -Name "GatewaySubnet" -AddressPrefix $gwSubnetPrefix
	$lobSubnet=New-AzureRMVirtualNetworkSubnetConfig -Name $lobSubnetName -AddressPrefix $lobSubnetPrefix
	New-AzureRMVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $locName -AddressPrefix $vnetAddrPrefix -Subnet $gwSubnet,$lobSubnet -DNSServer $dnsServers

Создайте шлюзы для VPN-подключения типа «сеть — сеть», используя следующие команды.

	$vnetName="<Table V – Item 1 – Value column>"
	$vnet=Get-AzureRMVirtualNetwork -Name $vnetName -ResourceGroupName $rgName
	
	# Attach a virtual network gateway to a public IP address and the gateway subnet
	$publicGatewayVipName="LOBAppPublicIPAddress"
	$vnetGatewayIpConfigName="LOBAppPublicIPConfig"
	New-AzureRMPublicIpAddress -Name $vnetGatewayIpConfigName -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic
	$publicGatewayVip=Get-AzureRMPublicIpAddress -Name $vnetGatewayIpConfigName -ResourceGroupName $rgName
	$vnetGatewayIpConfig=New-AzureRMVirtualNetworkGatewayIpConfig -Name $vnetGatewayIpConfigName -PublicIpAddressId $publicGatewayVip.Id -SubnetId $vnet.Subnets[0].Id

	# Create the Azure gateway
	$vnetGatewayName="LOBAppAzureGateway"
	$vnetGateway=New-AzureRMVirtualNetworkGateway -Name $vnetGatewayName -ResourceGroupName $rgName -Location $locName -GatewayType Vpn -VpnType RouteBased -IpConfigurations $vnetGatewayIpConfig
	
	# Create the gateway for the local network
	$localGatewayName="LOBAppLocalNetGateway"
	$localGatewayIP="<Table V – Item 4 – Value column>"
	$localNetworkPrefix=@( <comma-separated, double-quote enclosed list of the local network address prefixes from Table L, example: "10.1.0.0/24", "10.2.0.0/24"> )
	$localGateway=New-AzureRMLocalNetworkGateway -Name $localGatewayName -ResourceGroupName $rgName -Location $locName -GatewayIpAddress $localGatewayIP -AddressPrefix $localNetworkPrefix
	
	# Define the Azure virtual network VPN connection
	$vnetConnectionName="LOBAppS2SConnection"
	$vnetConnectionKey="<Table V – Item 8 – Value column>"
	$vnetConnection=New-AzureRMVirtualNetworkGatewayConnection -Name $vnetConnectionName -ResourceGroupName $rgName -Location $locName -ConnectionType IPsec -SharedKey $vnetConnectionKey -VirtualNetworkGateway1 $vnetGateway -LocalNetworkGateway2 $localGateway

После этого настройте локальное VPN-устройство для подключения к VPN-шлюзу Azure. Дополнительные сведения см. в статье [Настройка VPN-устройства](../virtual-networks/vpn-gateway-configure-vpn-gateway-mp.md#configure-your-vpn-device).

Чтобы настроить локальное VPN-устройство, вам потребуются:

- общедоступный IPv4-адрес VPN-шлюза Azure для виртуальной сети (его можно узнать, выполнив команду **Get-AzureRMPublicIpAddress -Name $publicGatewayVipName -ResourceGroupName $rgName**);
- общий ключ IPsec для VPN-подключения типа «сеть-сеть» (таблица V, элемент 8, столбец «Значение»).

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
	New-AzureRMAvailabilitySet –Name $avName –ResourceGroupName $rgName -Location $locName
	$avName="<Table A – Item 2 – Availability set name column>"
	New-AzureRMAvailabilitySet –Name $avName –ResourceGroupName $rgName -Location $locName
	$avName="<Table A – Item 3 – Availability set name column>"
	New-AzureRMAvailabilitySet –Name $avName –ResourceGroupName $rgName -Location $locName

После успешного выполнения этого этапа у вас должна быть следующая конфигурация:

![](./media/virtual-machines-workload-high-availability-LOB-application-phase1/workload-lobapp-phase1.png)

## Дальнейшие действия

- Чтобы продолжить настройку этой рабочей нагрузки, см. [этап 2](virtual-machines-workload-high-availability-LOB-application-phase2.md).

<!---HONumber=AcomDC_0128_2016-->
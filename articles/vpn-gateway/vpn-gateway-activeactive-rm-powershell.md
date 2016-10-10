<properties
   pageTitle="Настройка VPN-подключений типа ";сеть — сеть"; в режиме ";активный — активный"; для VPN-шлюзов Azure с помощью Azure Resource Manager и PowerShell | Microsoft Azure"
   description="В этой статье описана поэтапная настройка подключений в режиме ";активный — активный"; для VPN-шлюзов Azure с помощью Azure Resource Manager и PowerShell."
   services="vpn-gateway"
   documentationCenter="na"
   authors="yushwang"
   manager="rossort"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/26/2016"
   ms.author="yushwang"/>

# Настройка VPN-подключений типа "сеть — сеть" в режиме "активный — активный" для VPN-шлюзов Azure с помощью Azure Resource Manager и PowerShell

В этой статье содержится пошаговое описание процесса, который позволит создать подключения между локальными и виртуальными сетями в режиме "активный — активный" с помощью модели развертывания Resource Manager и PowerShell.


**О моделях развертывания Azure**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## Высокодоступное подключение между локальными сетями

Чтобы добиться высокого уровня доступности для подключений между локальными и виртуальными сетями, следует развернуть несколько VPN-шлюзов и установить несколько параллельных подключений между сетями и Azure. Сведения о вариантах подключения и топологии подключений см. в статье [Настройка высокодоступных подключений: распределенных и между виртуальными сетями](./vpn-gateway-highlyavailable.md).

Эта статья содержит инструкции по настройке VPN-подключения в режиме "активный — активный" между локальными сетями и между двумя виртуальными сетями.

- [Часть 1. Создание и настройка VPN-шлюза Azure в режиме "активный — активный"](#aagateway)

- [Часть 2. Создание подключения между локальными сетями в режиме "активный — активный"](#aacrossprem)

- [Часть 3. Создание подключения между виртуальными сетями в режиме "активный — активный"](#aav2v)

- [Часть 4. Обновление имеющегося шлюза между режимами "активный — активный" и "активный — резервный"](#aaupdate)

Вы можете комбинировать эти блоки для создания более сложной, высокодоступной топологии сети в соответствии со своими задачами.

>[AZURE.IMPORTANT] Обратите внимание, что режим "активный — активный" работает только для категории SKU HighPerformance.


## <a name ="aagateway"></a>Часть 1. Создание и настройка VPN-шлюзов в режиме "активный — активный"

Здесь приведены действия по настройке VPN-шлюза Azure в режиме "активный — активный". Между шлюзами в режиме "активный — активный" и "активный — резервный" существуют такие основные различия:

- Необходимо создать две конфигурации IP-адресов шлюза с двумя общедоступными IP-адресами.
- Требуется задать параметр EnableActiveActiveFeature.
- Номер SKU шлюза должен принадлежать к категории HighPerformance.

Другие свойства такие же, как у шлюзов не в режиме "активный — активный".

### Перед началом работы

- Убедитесь в том, что у вас уже есть подписка Azure. Если у вас нет подписки Azure, вы можете [активировать преимущества для подписчиков MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) или [зарегистрировать бесплатную учетную запись](https://azure.microsoft.com/pricing/free-trial/).
	
- Вам потребуется установить командлеты PowerShell диспетчера ресурсов Azure. Дополнительную информацию об установке командлетов PowerShell см. в статье [Установка и настройка Azure PowerShell](../powershell-install-configure.md).

### Шаг 1. Создание и настройка VNet1

#### 1\. Объявление переменных

В этом упражнении мы начнем с объявления переменных. В примере ниже объявлены переменные со значениями для этого упражнения. Обязательно замените значения своими при настройке для рабочей среды. Эти переменные можно использовать для ознакомления с этим типом конфигурации. Измените переменные, а затем скопируйте и вставьте код в консоль PowerShell.

	$Sub1          = "Ross"
	$RG1           = "TestAARG1"
	$Location1     = "West US"
	$VNetName1     = "TestVNet1"
	$FESubName1    = "FrontEnd"
	$BESubName1    = "Backend"
	$GWSubName1    = "GatewaySubnet"
	$VNetPrefix11  = "10.11.0.0/16"
	$VNetPrefix12  = "10.12.0.0/16"
	$FESubPrefix1  = "10.11.0.0/24"
	$BESubPrefix1  = "10.12.0.0/24"
	$GWSubPrefix1  = "10.12.255.0/27"
	$VNet1ASN      = 65010
	$DNS1          = "8.8.8.8"
	$GWName1       = "VNet1GW"
	$GW1IPName1    = "VNet1GWIP1"
	$GW1IPName2    = "VNet1GWIP2"
	$GW1IPconf1    = "gw1ipconf1"
	$GW1IPconf2    = "gw1ipconf2"
	$Connection12  = "VNet1toVNet2"
	$Connection151 = "VNet1toSite5_1"
	$Connection152 = "VNet1toSite5_2"

#### 2) Подключение к подписке Azure и создание группы ресурсов

Для работы с командлетами диспетчера ресурсов необходимо перейти в режим PowerShell. Дополнительную информацию см. в разделе [Использование Windows PowerShell с диспетчером ресурсов](../powershell-azure-resource-manager.md).

Откройте консоль PowerShell и подключитесь к своей учетной записи. Для подключения используйте следующий пример.

	Login-AzureRmAccount
	Select-AzureRmSubscription -SubscriptionName $Sub1
	New-AzureRmResourceGroup -Name $RG1 -Location $Location1

#### 3\. Создание TestVNet1

В примере ниже создается виртуальная сеть с именем TestVNet1 и три подсети: GatewaySubnet, FrontEnd и Backend. При замене значений важно, чтобы вы назвали подсеть шлюза именем GatewaySubnet. Если вы используете другое имя, шлюз не будет создан.

	$fesub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName1 -AddressPrefix $FESubPrefix1
	$besub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName1 -AddressPrefix $BESubPrefix1
	$gwsub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName1 -AddressPrefix $GWSubPrefix1

	New-AzureRmVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1 -Location $Location1 -AddressPrefix $VNetPrefix11,$VNetPrefix12 -Subnet $fesub1,$besub1,$gwsub1

### Шаг 2. Создание VPN-шлюза для TestVNet1 в режиме "активный — активный"

#### 1\. Создание общедоступных IP-адресов и конфигураций IP-адресов шлюза

Запросите выделение двух общедоступных IP-адресов для шлюза, который будет создан для виртуальной сети. Также следует определить настройки подсети и IP-адресов.

	$gw1pip1    = New-AzureRmPublicIpAddress -Name $GW1IPName1 -ResourceGroupName $RG1 -Location $Location1 -AllocationMethod Dynamic
	$gw1pip2    = New-AzureRmPublicIpAddress -Name $GW1IPName2 -ResourceGroupName $RG1 -Location $Location1 -AllocationMethod Dynamic

	$vnet1      = Get-AzureRmVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1
	$subnet1    = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet1
	$gw1ipconf1 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GW1IPconf1 -Subnet $subnet1 -PublicIpAddress $gw1pip1
	$gw1ipconf2 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GW1IPconf2 -Subnet $subnet1 -PublicIpAddress $gw1pip2

#### 2) Создание VPN-шлюза с конфигурацией "активный — активный"

Создайте шлюз для виртуальной сети TestVNet1. Обратите внимание, что есть две записи GatewayIpConfig и задан параметр EnableActiveActiveFeature. Для режима "активный — активный" требуется VPN-шлюз на основе маршрутов категории SKU HighPerformance. Создание шлюза может занять некоторое время (30 минут или более).

	New-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1 -Location $Location1 -IpConfigurations $gw1ipconf1,$gw1ipconf2 -GatewayType Vpn -VpnType RouteBased -GatewaySku HighPerformance -Asn $VNet1ASN -EnableActiveActiveFeature -Debug

#### 3\. Получение общедоступных IP-адресов шлюза и IP-адреса узла BGP

После создания шлюза необходимо получить IP-адрес для узла Azure BGP на VPN-шлюзе Azure. Этот адрес нужен, чтобы VPN-шлюз Azure мог выполнять роль узла BGP для локальных VPN-устройств.

	$gw1pip1 = Get-AzureRmPublicIpAddress -Name $GW1IPName1 -ResourceGroupName $RG1
	$gw1pip2 = Get-AzureRmPublicIpAddress -Name $GW1IPName2 -ResourceGroupName $RG1
	$vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1

Чтобы отобразить два общедоступных IP-адреса, выделенных для VPN-шлюза, и их соответствующие IP-адреса узла BGP для каждого экземпляра шлюза, используйте следующие командлеты:

	PS D:> $gw1pip1.IpAddress
	40.112.190.5

	PS D:> $gw1pip1.IpAddress
	138.91.156.129

	PS D:> $vnet1gw.BgpSettingsText
	{
	  "Asn": 65010,
	  "BgpPeeringAddress": "10.12.255.4,10.12.255.5",
	  "PeerWeight": 0
	}

Порядок общедоступных IP-адресов для экземпляров шлюза и соответствующих адресов пиринга BGP одинаковый. В приведенном примере виртуальная машина шлюза с общедоступным IP-адресом 40.112.190.5 использует адрес 10.12.255.4 в качестве адреса пиринга BGP, а шлюз с адресом 138.91.156.129 использует адрес 10.12.255.5. Эти сведения необходимы при настройке локальных VPN-устройств, которые подключаются к шлюзу в режиме "активный — активный". На схеме ниже показан шлюз вместе со всеми адресами.

![Шлюз в режиме "активный — активный"](./media/vpn-gateway-activeactive-rm-powershell/active-active-gw.png)

С помощью созданного шлюза можно установить подключение в режиме "активный — активный" между локальными или виртуальными сетями. В следующем разделе описано, как это сделать.


## <a name ="aacrossprem"></a>Часть 2. Создание подключения между локальными сетями в режиме "активный — активный"

Чтобы установить подключение между локальными сетями, нужно создать локальный сетевой шлюз, который будет представлять локальное VPN-устройство, а также подключение между VPN-шлюзом Azure и шлюзом локальной сети. В этом примере VPN-шлюз Azure находится в режиме "активный — активный". Таким образом, несмотря на то, что есть только одно локальное VPN-устройство (шлюз локальной сети) и один ресурс подключения, оба экземпляра VPN-шлюза Azure установят VPN-туннели типа "сеть — сеть" с локального устройства.

Прежде чем продолжить, убедитесь, что вы выполнили инструкции из [первой части](#aagateway) этой статьи.

### Шаг 1. Создание и настройка локального сетевого шлюза

#### 1\. Объявление переменных

В этом упражнении мы продолжим создание конфигурации, которая представлена на схеме. Не забудьте заменить значения теми, которые вы хотите использовать для конфигурации.

	$RG5           = "TestAARG5"
	$Location5     = "West US"
	$LNGName51     = "Site5_1"
	$LNGPrefix51   = "10.52.255.253/32"
	$LNGIP51       = "131.107.72.22"
	$LNGASN5       = 65050
	$BGPPeerIP51   = "10.52.255.253"

Несколько важных замечаний о параметрах локального сетевого шлюза.

- Локальный сетевой шлюз может находиться в том же расположении, что и VPN-шлюз, или в любом другом. Это же справедливо в отношении групп ресурсов. В этом примере они представлены в разных группах ресурсов, но в одном и том же расположении Azure.

- Если, как показано выше, есть только одно локальное VPN-устройство, подключение в режиме "активный — активный" может работать как с протоколом BGP, так и без него. В этом примере для подключения между локальными сетями используется BGP.

- Если включен BGP, то префикс, который необходимо объявить для шлюза локальной сети — это IP-адрес узла BGP на VPN-устройстве. В нашем примере это префикс /32 для адреса 10.52.255.253/32.

- Не забывайте, что для локальных сетей и виртуальной сети Azure должны быть указаны разные номера ASN BGP. Если они совпадают, а локальное VPN-устройство уже использует свой ASN для связи с другими соседями BGP, необходимо изменить ASN вашей виртуальной сети.

#### 2) Создание локального сетевого шлюза для сети Site5
	
Прежде чем продолжить, убедитесь, что вы все еще подключены к подписке 1. Создайте группу ресурсов, если она еще не создана.

	New-AzureRmResourceGroup       -Name $RG5 -Location $Location5
	New-AzureRmLocalNetworkGateway -Name $LNGName51 -ResourceGroupName $RG5 -Location $Location5 -GatewayIpAddress $LNGIP51 -AddressPrefix $LNGPrefix51 -Asn $LNGASN5 -BgpPeeringAddress $BGPPeerIP51

### Шаг 2. Подключение шлюза виртуальной сети к локальному сетевому шлюзу

#### 1\. Получение обоих шлюзов

	$vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
	$lng5gw1 = Get-AzureRmLocalNetworkGateway   -Name $LNGName51 -ResourceGroupName $RG5

#### 2) Создание подключения между TestVNet1 и Site5

На этом шаге будет создано подключение между TestVNet1 и Site5\_1, где параметру EnableBGP задано значение $True.

	New-AzureRmVirtualNetworkGatewayConnection -Name $Connection151 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng5gw1 -Location $Location1 -ConnectionType IPsec -SharedKey 'AzureA1b2C3' -EnableBGP True

#### 3\. Параметры VPN и BGP для локального VPN-устройства

В приведенном ниже примере перечислены параметры, которые следует ввести в разделе конфигурации BGP на локальном VPN-устройстве для нашего тестового задания.

	- Site5 ASN            : 65050
	- Site5 BGP IP         : 10.52.255.253
	- Prefixes to announce : (for example) 10.51.0.0/16 and 10.52.0.0/16
	- Azure VNet ASN       : 65010
	- Azure VNet BGP IP 1  : 10.12.255.4 for tunnel to 40.112.190.5
	- Azure VNet BGP IP 2  : 10.12.255.5 for tunnel to 138.91.156.129
	- Static routes        : Destination 10.12.255.4/32, nexthop the VPN tunnel interface to 40.112.190.5
	                         Destination 10.12.255.5/32, nexthop the VPN tunnel interface to 138.91.156.129
	- eBGP Multihop        : Ensure the "multihop" option for eBGP is enabled on your device if needed

Подключение будет установлено через несколько минут. Сразу после создания подключения IPsec начнется сессия пиринга BGP. В этом примере настроено только одно локальное VPN-устройство, как показано на схеме ниже.

![Подключение между локальными сетями в режиме "активный — активный"](./media/vpn-gateway-activeactive-rm-powershell/active-active.png)

### Шаг 3. Подключение двух локальных VPN-устройств к VPN-шлюзу в режиме "активный — активный"

При наличии двух VPN-устройств в одной локальной сети можно добиться двойной избыточности, подключив VPN-шлюз Azure ко второму VPN-устройству.

#### 1\. Создание второго шлюза локальной сети для сети Site5

Обратите внимание, что IP-адрес шлюза, префикс адреса и адрес пиринга BGP для второго шлюза локальной сети не должны перекрываться с предыдущим шлюзом локальной сети для одной локальной сети.

	$LNGName52     = "Site5_2"
	$LNGPrefix52   = "10.52.255.254/32"
	$LNGIP52       = "131.107.72.23"
	$BGPPeerIP52   = "10.52.255.254"

	New-AzureRmLocalNetworkGateway -Name $LNGName52 -ResourceGroupName $RG5 -Location $Location5 -GatewayIpAddress $LNGIP52 -AddressPrefix $LNGPrefix52 -Asn $LNGASN5 -BgpPeeringAddress $BGPPeerIP52
 
#### 2) Подключение шлюза виртуальной сети ко второму шлюзу локальной сети

Создайте подключение между TestVNet1 и Site5\_2, где параметру EnableBGP задано значение $True.

	$lng5gw2 = Get-AzureRmLocalNetworkGateway   -Name $LNGName52 -ResourceGroupName $RG5

	New-AzureRmVirtualNetworkGatewayConnection -Name $Connection152 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng5gw2 -Location $Location1 -ConnectionType IPsec -SharedKey 'AzureA1b2C3' -EnableBGP True

#### 3\. Параметры VPN и BGP для второго локального VPN-устройства

Аналогичным образом ниже перечислены параметры, которые нужно ввести на втором VPN-устройстве.

	- Site5 ASN            : 65050
	- Site5 BGP IP         : 10.52.255.254
	- Prefixes to announce : (for example) 10.51.0.0/16 and 10.52.0.0/16
	- Azure VNet ASN       : 65010
	- Azure VNet BGP IP 1  : 10.12.255.4 for tunnel to 40.112.190.5
	- Azure VNet BGP IP 2  : 10.12.255.5 for tunnel to 138.91.156.129
	- Static routes        : Destination 10.12.255.4/32, nexthop the VPN tunnel interface to 40.112.190.5
	                         Destination 10.12.255.5/32, nexthop the VPN tunnel interface to 138.91.156.129
	- eBGP Multihop        : Ensure the "multihop" option for eBGP is enabled on your device if needed

После того, как подключение (туннели) установится, VPN-устройства с двойной избыточностью и туннели подключатся к локальной сети и Azure.

![Подключение между локальными сетями с двойной избыточностью](./media/vpn-gateway-activeactive-rm-powershell/dual-redundancy.png)


## <a name ="aav2v"></a>Часть 3. Создание подключения между виртуальными сетями в режиме "активный — активный"

В этом разделе описано, как создать подключение между виртуальными сетями в режиме "активный — активный" с использованием BGP.

Приведенные ниже инструкции продолжают действия, описанные выше. Чтобы создать и настроить сеть TestVNet1 и VPN-шлюз с использованием BGP, сначала следует выполнить инструкции из [первой части](#aagateway).

### Шаг 1. Создание TestVNet2 и VPN-шлюза

Важно убедиться в том, что пространство IP-адресов для новой виртуальной сети TestVNet2 не пересекается с диапазонами любой из ваших виртуальных сетей.

В этом примере виртуальные сети относятся к одной подписке. Вы можете создавать подключения и между виртуальными сетями из разных подписок. Дополнительные сведения об этом в статье [Настройка подключения между виртуальными сетями в развертывании Resource Manager с помощью PowerShell](./vpn-gateway-vnet-vnet-rm-ps.md). Чтобы использовать для подключения протокол BGP, обязательно укажите параметр -EnableBgp True при создании подключения.

#### 1\. Объявление переменных

Не забудьте заменить значения теми, которые вы хотите использовать для конфигурации.

	$RG2           = "TestAARG2"
	$Location2     = "East US"
	$VNetName2     = "TestVNet2"
	$FESubName2    = "FrontEnd"
	$BESubName2    = "Backend"
	$GWSubName2    = "GatewaySubnet"
	$VNetPrefix21  = "10.21.0.0/16"
	$VNetPrefix22  = "10.22.0.0/16"
	$FESubPrefix2  = "10.21.0.0/24"
	$BESubPrefix2  = "10.22.0.0/24"
	$GWSubPrefix2  = "10.22.255.0/27"
	$VNet2ASN      = 65020
	$DNS2          = "8.8.8.8"
	$GWName2       = "VNet2GW"
	$GW2IPName1    = "VNet2GWIP1"
	$GW2IPconf1    = "gw2ipconf1"
	$GW2IPName2    = "VNet2GWIP2"
	$GW2IPconf2    = "gw2ipconf2"
	$Connection21  = "VNet2toVNet1"
	$Connection12  = "VNet1toVNet2"

#### 2) Создание сети TestVNet2 в новой группе ресурсов

	New-AzureRmResourceGroup -Name $RG2 -Location $Location2

	$fesub2 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName2 -AddressPrefix $FESubPrefix2
	$besub2 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName2 -AddressPrefix $BESubPrefix2
	$gwsub2 = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName2 -AddressPrefix $GWSubPrefix2

	New-AzureRmVirtualNetwork -Name $VNetName2 -ResourceGroupName $RG2 -Location $Location2 -AddressPrefix $VNetPrefix21,$VNetPrefix22 -Subnet $fesub2,$besub2,$gwsub2

#### 3\. Создание VPN-шлюза в режиме "активный — активный" для TestVNet2

Запросите выделение двух общедоступных IP-адресов для шлюза, который будет создан для виртуальной сети. Также следует определить настройки подсети и IP-адресов.

	$gw2pip1    = New-AzureRmPublicIpAddress -Name $GW2IPName1 -ResourceGroupName $RG2 -Location $Location2 -AllocationMethod Dynamic
	$gw2pip2    = New-AzureRmPublicIpAddress -Name $GW2IPName2 -ResourceGroupName $RG2 -Location $Location2 -AllocationMethod Dynamic

	$vnet2      = Get-AzureRmVirtualNetwork -Name $VNetName2 -ResourceGroupName $RG2
	$subnet2    = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet2
	$gw2ipconf1 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GW2IPconf1 -Subnet $subnet2 -PublicIpAddress $gw2pip1
	$gw2ipconf2 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GW2IPconf2 -Subnet $subnet2 -PublicIpAddress $gw2pip2

Создайте VPN-шлюз с номером AS и включенным параметром EnableActiveActiveFeature. Обратите внимание, что нужно переназначить номер ASN по умолчанию для ваших VPN-шлюзов Azure. Номера ASN для подключенных виртуальных сетей должны быть разными, чтобы работал протокол BGP и транзитная маршрутизация.

	New-AzureRmVirtualNetworkGateway -Name $GWName2 -ResourceGroupName $RG2 -Location $Location2 -IpConfigurations $gw2ipconf1,$gw2ipconf2 -GatewayType Vpn -VpnType RouteBased -GatewaySku HighPerformance -Asn $VNet2ASN -EnableActiveActiveFeature

### Шаг 2. Подключение шлюзов TestVNet1 и TestVNet2

В этом примере оба шлюза находятся в одной подписке. Этот шаг можно выполнить в одном сеансе PowerShell.

#### 1\. Получение обоих шлюзов

Обязательно войдите и подключитесь к подписке 1.

	$vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1
	$vnet2gw = Get-AzureRmVirtualNetworkGateway -Name $GWName2 -ResourceGroupName $RG2
	
#### 2) Создание двух подключений

На этом шаге вы создадите подключение от TestVNet1 к TestVNet2, а также подключение от TestVNet2 к TestVNet1.

	New-AzureRmVirtualNetworkGatewayConnection -Name $Connection12 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -VirtualNetworkGateway2 $vnet2gw -Location $Location1 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3' -EnableBgp $True

	New-AzureRmVirtualNetworkGatewayConnection -Name $Connection21 -ResourceGroupName $RG2 -VirtualNetworkGateway1 $vnet2gw -VirtualNetworkGateway2 $vnet1gw -Location $Location2 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3' -EnableBgp $True

>[AZURE.IMPORTANT] Не забудьте включить BGP для ОБОИХ подключений.

Подключение будет установлено через несколько минут после выполнения этих шагов, а после создания подключения между виртуальными сетями с двойной избыточностью запустится сеанс пиринга BGP.

![Подключение между виртуальными сетями в режиме "активный — активный"](./media/vpn-gateway-activeactive-rm-powershell/vnet-to-vnet.png)

## <a name ="aaupdate"></a>Часть 4. Обновление имеющегося шлюза между режимами "активный — активный" и "активный — резервный"

В последнем разделе описано, как перевести имеющийся VPN-шлюз Azure из режима "активный — резервный" в режим "активный — активный" и наоборот.

>[AZURE.IMPORTANT] Обратите внимание, что режим "активный — активный" работает только для категории SKU HighPerformance.

### Настройка режима "активный — активный" для шлюза в режиме "активный — резервный"

#### 1\. Параметры шлюза

В следующем примере шлюз в режиме "активный — резервный" переходит в режим "активный — активный". Необходимо создать другой общедоступный IP-адрес, а затем добавить вторую конфигурацию IP-адреса для шлюза. Ниже показаны используемые параметры.

	$GWName     = "TestVNetAA1GW"
	$VNetName   = "TestVNetAA1"
	$RG         = "TestVPNActiveActive01"
	$GWIPName2  = "gwpip2"
	$GWIPconf2  = "gw1ipconf2"

	$vnet       = Get-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $RG
	$subnet     = Get-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
	$gw         = Get-AzureRmVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG
	$location   = $gw.Location

#### 2) Создание общедоступного IP-адреса и добавление второй конфигурации IP-адреса для шлюза

	$gwpip2     = New-AzureRmPublicIpAddress -Name $GWIPName2 -ResourceGroupName $RG -Location $location -AllocationMethod Dynamic
	Add-AzureRmVirtualNetworkGatewayIpConfig -VirtualNetworkGateway $gw -Name $GWIPconf2 -Subnet $subnet -PublicIpAddress $gwpip2 

#### 3\. Включение режима "активный — активный" и обновление шлюза

Необходимо задать объект шлюза в PowerShell, чтобы активировать фактическое обновление.

	Set-AzureRmVirtualNetworkGateway -VirtualNetworkGateway $gw -EnableActiveActiveFeature

Для обновления может потребоваться 30–45 минут.

### Настройка режима "активный — резервный" для шлюза в режиме "активный — активный"

#### 1\. Параметры шлюза

Используйте приведенные выше параметры, чтобы получить имя IP-конфигурации, которую нужно удалить.

	$GWName     = "TestVNetAA1GW"
	$RG         = "TestVPNActiveActive01"

	$gw         = Get-AzureRmVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG
	$ipconfname = $gw.IpConfigurations[1].Name

#### 2) Удаление конфигурации IP-адреса для шлюза и отключение режима "активный — активный"

Аналогичным образом нужно задать объект шлюза в PowerShell, чтобы активировать фактическое обновление.

	Remove-AzureRmVirtualNetworkGatewayIpConfig -Name $ipconfname -VirtualNetworkGateway $gw
	Set-AzureRmVirtualNetworkGateway -VirtualNetworkGateway $gw -DisableActiveActiveFeature

Для обновления может потребоваться 30–45 минут.


## Дальнейшие действия

Установив подключение, можно добавить виртуальные машины в виртуальные сети. Инструкции см. в статье [Создание виртуальной машины под управлением Windows на портале Azure](../virtual-machines/virtual-machines-windows-hero-tutorial.md).

<!---HONumber=AcomDC_0928_2016-->
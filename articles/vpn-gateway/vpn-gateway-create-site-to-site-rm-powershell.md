<properties
   pageTitle="Создание виртуальной сети с VPN-подключением типа ";сеть — сеть"; с помощью Azure Resource Manager и PowerShell | Microsoft Azure"
   description="В этой статье поэтапно описывается создание виртуальной сети с помощью модели диспетчера ресурсов и ее подключение к локальной сети с помощью подключения типа ";сеть — сеть"; через VPN-шлюз."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/02/2016"
   ms.author="cherylmc"/>

# Создание виртуальной сети с VPN-подключением типа "сеть — сеть" с помощью PowerShell и Azure Resource Manager

> [AZURE.SELECTOR]
- [Портал Azure](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
- [Классический портал Azure](vpn-gateway-site-to-site-create.md)
- [PowerShell — Resource Manager](vpn-gateway-create-site-to-site-rm-powershell.md)

В этой статье мы расскажем, как создать виртуальную сеть, соединенную с локальной сетью через VPN-подключение типа "сеть — сеть", с использованием модели развертывания Azure Resource Manager. Подключения типа "сеть — сеть" можно использовать для распределенных и гибридных конфигураций.


**О моделях развертывания Azure**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## Схема подключения 

![Схема "сеть — сеть"](./media/vpn-gateway-create-site-to-site-rm-powershell/site2site.png "сеть — сеть")

**Модели развертывания и средства для подключений типа "сеть — сеть"**

[AZURE.INCLUDE [vpn-gateway-table-site-to-site](../../includes/vpn-gateway-table-site-to-site-include.md)]

Если вы хотите объединить виртуальные сети, не создавая соединение с локальным расположением, см. статью [Настройка подключения между виртуальными сетями](vpn-gateway-vnet-vnet-rm-ps.md). Если вам нужен другой тип конфигурации соединения, см. статью [Топологии подключения через VPN-шлюз Azure](vpn-gateway-topology.md).


## Перед началом работы

Перед началом настройки убедитесь, что у вас есть следующее.

- Совместимое VPN-устройство и пользователь, который может настроить его. См. статью [О VPN-устройствах](vpn-gateway-about-vpn-devices.md). Если вы не умеете настраивать VPN-устройство или не знаете диапазоны IP-адресов в своей локальной сети, вам следует найти того, кто сможет предоставить вам нужную информацию.

- Внешний общедоступный IP-адрес для VPN-устройства. Этот IP-адрес не может располагаться вне преобразования сетевых адресов (NAT).
	
- Подписка Azure. Если у вас нет подписки Azure, вы можете [активировать преимущества для подписчиков MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) или [зарегистрировать бесплатную учетную запись](https://azure.microsoft.com/pricing/free-trial/).
	
- Вам потребуется установить последнюю версию командлетов PowerShell диспетчера ресурсов Azure. Дополнительную информацию об установке командлетов PowerShell см. в статье [Установка и настройка Azure PowerShell](../powershell-install-configure.md).


## 1\. Подключение к подписке 

Для работы с командлетами диспетчера ресурсов необходимо перейти в режим PowerShell. Дополнительную информацию см. в разделе [Использование Windows PowerShell с диспетчером ресурсов](../powershell-azure-resource-manager.md).

Откройте консоль PowerShell и подключитесь к своей учетной записи. Для подключения используйте следующий пример.

	Login-AzureRmAccount

Просмотрите подписки учетной записи.

	Get-AzureRmSubscription 

Укажите подписку, которую нужно использовать.

	Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"

## 2\. Создание виртуальной сети и подсети шлюза

На примерах ниже показана подсеть шлюза размера /28. Хотя можно создать подсеть шлюза размера /29, мы не рекомендуем это делать. Рекомендуется создать подсеть шлюза размера /27 или больше (/26, и /25 и т. д.), чтобы удовлетворить требования к дополнительным компонентам.

Если у вас уже есть виртуальная сеть с подсетью шлюза c длиной префикса 29 бит (/29) или больше, сразу переходите к разделу [Добавление локального сетевого шлюза](#localnet).


[AZURE.INCLUDE [vpn-gateway-no-nsg](../../includes/vpn-gateway-no-nsg-include.md)]

### Создание виртуальной сети и подсети шлюза

Используйте приведенный ниже пример, чтобы создать виртуальную сеть и подсеть шлюза. Подставьте собственные значения.

Сначала создайте группу ресурсов.
	
	New-AzureRmResourceGroup -Name testrg -Location 'West US'

Затем создайте виртуальную сеть. Убедитесь, что указанные адресные пространства не перекрываются ни с одним из адресных пространств, которые используются в локальной сети.

В следующем примере создается виртуальная сеть с именем *testvnet* и две подсети: *GatewaySubnet* и *Subnet1*. Важно, чтобы одна из подсетей имела имя *GatewaySubnet*. Если вы используете другое имя, подключение не будет настроено.

	$subnet1 = New-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.0.0/28
	$subnet2 = New-AzureRmVirtualNetworkSubnetConfig -Name 'Subnet1' -AddressPrefix '10.0.1.0/28'
	New-AzureRmVirtualNetwork -Name testvnet -ResourceGroupName testrg -Location 'West US' -AddressPrefix 10.0.0.0/16 -Subnet $subnet1, $subnet2

### <a name="gatewaysubnet"></a>Добавление подсети шлюза в уже созданную виртуальную сеть

Этот шаг требуется только в том случае, если вам нужно добавить подсеть шлюза в созданную ранее виртуальную сеть.

Подсеть шлюза можно создать, используя приведенный ниже пример. Обязательно присвойте подсети шлюза имя "GatewaySubnet". Если будет присвоено другое имя, вы создадите подсеть, но Azure не будет воспринимать ее как подсеть шлюза.

	$vnet = Get-AzureRmVirtualNetwork -ResourceGroupName testrg -Name testvnet
	Add-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.3.0/28 -VirtualNetwork $vnet

Теперь нужно настроить конфигурацию.

	Set-AzureRmVirtualNetwork -VirtualNetwork $vnet

## 3\. <a name="localnet"></a>Добавление локального сетевого шлюза

В виртуальной сети термин "шлюз локальной сети" обычно означает локальное расположение. Вы присвоите этому расположению имя, с помощью которого служба Azure сможет ссылаться на него, а также укажете префикс адресного пространства для шлюза локальной сети.

С помощью указанного префикса IP-адреса служба Azure сможет определить, какой трафик отправлять в локальное расположение. Это значит, что вам нужно будет указать каждый префикс адреса, который необходимо связать со шлюзом локальной сети. При изменении локальной сети вы с легкостью можете обновить эти префиксы.

При использовании примеров PowerShell обратите внимание на следующее.
	
- Параметр *GatewayIPAddress* — это IP-адрес локального VPN-устройства. Ваше VPN-устройство не может располагаться вне преобразования сетевых адресов (NAT).
- Параметр *AddressPrefix* — локальное адресное пространство.

Чтобы добавить шлюз локальной сети с одним префиксом адреса, используйте этот пример:

	New-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg -Location 'West US' -GatewayIpAddress '23.99.221.164' -AddressPrefix '10.5.51.0/24'

Чтобы добавить шлюз локальной сети с несколькими префиксами адреса, используйте этот пример:

	New-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg -Location 'West US' -GatewayIpAddress '23.99.221.164' -AddressPrefix @('10.0.0.0/24','20.0.0.0/24')

### Изменение префиксов IP-адресов для локального сетевого шлюза

Иногда префиксы адресов шлюза локальной сети могут изменяться. Действия по изменению префиксов IP-адресов будут разными в зависимости от того, создано ли подключение через VPN-шлюз. См. раздел [Изменение префиксов IP-адресов для локального сетевого шлюза](#modify) в этой статье.


## 4\. Запрос общедоступного IP-адреса для VPN-шлюза

Затем вам понадобится запросить общедоступный IP-адрес, который нужно выделить для шлюза VPN виртуальной сети Azure. Он отличается от IP-адреса, назначенного VPN-устройству. Этот адрес присваивается VPN-шлюзу Azure отдельно. Вы не можете указать необходимый IP-адрес, так как он динамически выделяется для шлюза. Этот IP-адрес будет использоваться при настройке локального VPN-устройства для подключения к шлюзу.

Используйте следующий пример PowerShell. Для этого адреса нужно использовать метод динамического распределения (Dynamic).

	$gwpip= New-AzureRmPublicIpAddress -Name gwpip -ResourceGroupName testrg -Location 'West US' -AllocationMethod Dynamic

>[AZURE.NOTE] VPN-шлюз Azure для модели развертывания, в которой используется диспетчер ресурсов, в настоящее время поддерживает только общедоступные IP-адреса, которые выделяются динамически. Однако это не значит, что IP-адрес изменится. IP-адрес VPN-шлюза Azure изменяется только после его удаления и повторного создания. Общедоступный IP-адрес шлюза не изменяется после изменения размера, сброса или обновления VPN-шлюза Azure, а также вследствие других внутренних работ по его обслуживанию.

## 5\. Создание конфигурации IP-адресации шлюза

Конфигурация шлюза определяет используемые подсеть и общедоступный IP-адрес. Используйте следующий пример, чтобы создать конфигурацию шлюза.

	$vnet = Get-AzureRmVirtualNetwork -Name testvnet -ResourceGroupName testrg
	$subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
	$gwipconfig = New-AzureRmVirtualNetworkGatewayIpConfig -Name gwipconfig1 -SubnetId $subnet.Id -PublicIpAddressId $gwpip.Id 

## 6\. Создание шлюза виртуальной сети

На этом шаге вы создадите шлюз виртуальной сети. Обратите внимание, что создание шлюза может занять много времени — 20 минут или больше.

Используйте следующие значения:

- Для конфигурации "сеть — сеть" укажите для параметра *-GatewayType* значение *Vpn*. Тип шлюза всегда зависит от реализуемой конфигурации. Например, для других конфигураций шлюза может потребоваться тип шлюза ExpressRoute.

- Параметр *-VpnType* может иметь значение *RouteBased* (в некоторых документах такой шлюз называется шлюзом с динамической маршрутизацией) или *PolicyBased* (в некоторых документах — шлюз со статической маршрутизацией). Дополнительные сведения о типах VPN-шлюзов см. в статье [Шлюзы VPN](vpn-gateway-about-vpngateways.md#vpntype).
- Параметр *-GatewaySku* может иметь значение *Basic*, *Standard* или *HighPerformance*.

		New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg -Location 'West US' -IpConfigurations $gwipconfig -GatewayType Vpn -VpnType RouteBased -GatewaySku Standard

## 7\. Настройка устройства VPN

На этом этапе вам понадобится общедоступный IP-адрес шлюза виртуальной сети для настройки локального VPN-устройства. Обратитесь к производителю устройства, чтобы получить конкретные сведения о конфигурации. Дополнительные сведения см. также в статье [О VPN-устройствах для подключений виртуальной сети типа «сеть — сеть»](vpn-gateway-about-vpn-devices.md).

Чтобы найти общедоступный IP-адрес шлюза виртуальной сети, используйте следующий пример.

	Get-AzureRmPublicIpAddress -Name gwpip -ResourceGroupName testrg

## 8\. Создание VPN-подключения

Далее вам нужно создать VPN-подключение типа "сеть — сеть" между шлюзом виртуальной сети и VPN-устройством. Обязательно подставьте собственные значения. Общий ключ должен соответствовать значению, использованному в конфигурации VPN-устройства. Обратите внимание, что для подключения типа "сеть — сеть" параметр `-ConnectionType` имеет значение *IPsec*.

	$gateway1 = Get-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
	$local = Get-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg

	New-AzureRmVirtualNetworkGatewayConnection -Name localtovon -ResourceGroupName testrg -Location 'West US' -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'

Через некоторое время будет установлено подключение.

## <a name="toverify"></a>Проверка VPN-подключения

Существует несколько разных способов для проверки VPN-подключения. Далее мы расскажем, как выполнить основные проверки с помощью портала Azure и PowerShell.

[AZURE.INCLUDE [vpn-gateway-verify-connection-rm](../../includes/vpn-gateway-verify-connection-rm-include.md)]

## <a name="modify"></a>Изменение префиксов IP-адресов для локального сетевого шлюза

Если вам нужно изменить префиксы для шлюза локальной сети, выполните следующие действия. Мы приводим два набора инструкций. Выбор тех или других инструкций зависит от того, создано ли у вас подключение через шлюз.

[AZURE.INCLUDE [vpn-gateway-modify-ip-prefix-rm](../../includes/vpn-gateway-modify-ip-prefix-rm-include.md)]

## <a name="modifygwipaddress"></a>Изменение IP-адреса шлюза для локального сетевого шлюза

[AZURE.INCLUDE [vpn-gateway-modify-lng-gateway-ip-rm](../../includes/vpn-gateway-modify-lng-gateway-ip-rm-include.md)]

## Дальнейшие действия

- В виртуальные сети можно добавлять виртуальные машины. Инструкции см. в статье [Создание виртуальной машины под управлением Windows на портале Azure](../virtual-machines/virtual-machines-windows-hero-tutorial.md).

- Сведения о BGP см. в статьях [Обзор использования BGP с VPN-шлюзами Azure](vpn-gateway-bgp-overview.md) и [Настройка BGP на VPN-шлюзах Azure с помощью Azure Resource Manager и PowerShell](vpn-gateway-bgp-resource-manager-ps.md).

<!---HONumber=AcomDC_0810_2016-->
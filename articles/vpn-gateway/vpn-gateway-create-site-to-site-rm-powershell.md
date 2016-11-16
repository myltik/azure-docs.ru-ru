---
title: "Создание виртуальной сети с VPN-подключением типа &quot;сеть — сеть&quot; с помощью Azure Resource Manager и PowerShell | Документация Майкрософт"
description: "В этой статье поэтапно описывается создание виртуальной сети с помощью модели развертывания Resource Manager и ее подключение к локальной сети с помощью подключения типа &quot;сеть — сеть&quot; через VPN-шлюз."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: carmonm
editor: 
tags: azure-resource-manager
ms.assetid: fcc2fda5-4493-4c15-9436-84d35adbda8e
ms.service: vpn-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/14/2016
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: cdc41f9068de3e1ea796e1a3172a99dd185d9f9c


---
# <a name="create-a-vnet-with-a-sitetosite-connection-using-powershell"></a>Создание виртуальной сети с подключением типа "сеть — сеть" с помощью PowerShell
> [!div class="op_single_selector"]
> * [Resource Manager — портал Azure](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
> * [Resource Manager — PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md)
> * [Классическая модель — классический портал](vpn-gateway-site-to-site-create.md)
> 
> 

В этой статье мы расскажем, как создать виртуальную сеть, соединенную с локальной сетью через подключение с помощью VPN-шлюза типа "сеть — сеть", используя модель развертывания с помощью Azure Resource Manager. Подключения типа "сеть — сеть" можно использовать для распределенных и гибридных конфигураций.

![Схема "сеть — сеть"](./media/vpn-gateway-create-site-to-site-rm-powershell/s2srmps.png "site-to-site") 

### <a name="deployment-models-and-methods-for-sitetosite-connections"></a>Модели развертывания и способы настройки подключений типа "сеть — сеть"
[!INCLUDE [deployment models](../../includes/vpn-gateway-deployment-models-include.md)]

Следующая таблица показывает модели развертывания, доступные в настоящее время, и методы для конфигурации "сеть — сеть". Когда появится статья с руководством по конфигурации, мы разместим прямую ссылку на нее в этой таблице. 

[!INCLUDE [site-to-site table](../../includes/vpn-gateway-table-site-to-site-include.md)]

#### <a name="additional-configurations"></a>Дополнительные конфигурации
Если вы хотите объединить виртуальные сети, не создавая соединение с локальным расположением, см. статью о [настройке подключения между виртуальными сетями](vpn-gateway-vnet-vnet-rm-ps.md). Если вы хотите добавить подключение "сеть — сеть" к виртуальной сети, в которой уже есть подключение, см. статью о [добавлении подключения "сеть — сеть" к виртуальной сети с существующим подключением через VPN-шлюз](vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md).

## <a name="before-you-begin"></a>Перед началом работы
Перед началом настройки убедитесь, что у вас есть следующее.

* Совместимое VPN-устройство и пользователь, который может настроить его. См. статью о [VPN-устройствах](vpn-gateway-about-vpn-devices.md). Если вы не умеете настраивать VPN-устройство или не знаете диапазоны IP-адресов в своей локальной сети, вам следует найти того, кто сможет предоставить вам нужную информацию.
* Внешний общедоступный IP-адрес для VPN-устройства. Этот IP-адрес не может располагаться вне преобразования сетевых адресов (NAT).
* Подписка Azure. Если у вас нет подписки Azure, вы можете активировать [преимущества для подписчиков MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) или зарегистрировать [бесплатную учетную запись](https://azure.microsoft.com/pricing/free-trial/).
* Последняя версия командлетов PowerShell для Azure Resource Manager. Дополнительные сведения об установке командлетов PowerShell см. в статье [Как установить и настроить Azure PowerShell](../powershell-install-configure.md).

## <a name="a-namelogina1-connect-to-your-subscription"></a><a name="Login"></a>1. Подключение к подписке
Для работы с командлетами диспетчера ресурсов необходимо перейти в режим PowerShell. Дополнительные сведения см. в статье [Использование Azure PowerShell с диспетчером ресурсов Azure](../powershell-azure-resource-manager.md).

Откройте консоль PowerShell и подключитесь к своей учетной записи. Для подключения используйте следующий пример.

    Login-AzureRmAccount

Просмотрите подписки учетной записи.

    Get-AzureRmSubscription 

Укажите подписку, которую нужно использовать.

    Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"

## <a name="a-namevneta2-create-a-virtual-network-and-a-gateway-subnet"></a><a name="VNet"></a>2. Создание виртуальной сети и подсети шлюза
В примерах используется подсеть шлюза с маской подсети /28. Хотя можно создать подсеть шлюза размером /29, рекомендуется создать подсеть большего размера, включающую несколько адресов, выбрав по крайней мере значение /28 или /27. Таким образом, у вас будет достаточно адресов, чтобы добавить дополнительные конфигурации в будущем.

Если у вас уже есть виртуальная сеть с подсетью шлюза c длиной префикса 29 бит (/29) или больше, сразу переходите к разделу [Добавление локального сетевого шлюза](#localnet).

[!INCLUDE [vpn-gateway-no-nsg](../../includes/vpn-gateway-no-nsg-include.md)]

### <a name="to-create-a-virtual-network-and-a-gateway-subnet"></a>Создание виртуальной сети и подсети шлюза
Используйте пример ниже, чтобы создать виртуальную сеть и подсеть шлюза. Подставьте собственные значения. 

Сначала создайте группу ресурсов.

    New-AzureRmResourceGroup -Name testrg -Location 'West US'

Затем создайте виртуальную сеть. Убедитесь, что указанные адресные пространства не перекрываются ни с одним из адресных пространств, которые используются в локальной сети.

В примере ниже создается виртуальная сеть с именем *testvnet* и две подсети: *GatewaySubnet* и *Subnet1*. Важно, чтобы одна из подсетей имела имя *GatewaySubnet*. Если вы используете другое имя, подключение не будет настроено. 

Задайте переменные.

    $subnet1 = New-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.0.0/28
    $subnet2 = New-AzureRmVirtualNetworkSubnetConfig -Name 'Subnet1' -AddressPrefix '10.0.1.0/28'

Создайте виртуальную сеть.

    New-AzureRmVirtualNetwork -Name testvnet -ResourceGroupName testrg `
    -Location 'West US' -AddressPrefix 10.0.0.0/16 -Subnet $subnet1, $subnet2

### <a name="a-namegatewaysubnetato-add-a-gateway-subnet-to-a-virtual-network-you-have-already-created"></a><a name="gatewaysubnet"></a>Добавление подсети шлюза в уже созданную виртуальную сеть
Этот шаг требуется только в том случае, если вам нужно добавить подсеть шлюза в созданную ранее виртуальную сеть.

Подсеть шлюза можно создать с помощью примера, указанного ниже. Обязательно присвойте подсети шлюза имя "GatewaySubnet". Если будет присвоено другое имя, вы создадите подсеть, но Azure не будет рассматривать ее как подсеть шлюза.

Задайте переменные.

    $vnet = Get-AzureRmVirtualNetwork -ResourceGroupName testrg -Name testvnet

Создайте подсеть шлюза.

    Add-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.3.0/28 -VirtualNetwork $vnet

Теперь нужно настроить конфигурацию. 

    Set-AzureRmVirtualNetwork -VirtualNetwork $vnet

## <a name="3-a-namelocalnetaadd-your-local-network-gateway"></a>3) <a name="localnet"></a>Добавление локального сетевого шлюза
В виртуальной сети термин "шлюз локальной сети" обычно означает локальное расположение. Присвойте этому расположению имя (чтобы определить расположение в среде Azure), а также укажите префикс адресного пространства для шлюза локальной сети. 

С помощью указанного префикса IP-адреса служба Azure может определить, какой трафик отправлять в локальное расположение. Это значит, что вам нужно будет указать каждый префикс адреса, который необходимо связать со шлюзом локальной сети. При изменении локальной сети вы с легкостью можете обновить эти префиксы. 

При использовании примеров PowerShell обратите внимание на следующее.

* Параметр *GatewayIPAddress* — это IP-адрес локального VPN-устройства. Ваше VPN-устройство не может располагаться вне преобразования сетевых адресов (NAT). 
* Параметр *AddressPrefix* — локальное адресное пространство.

Чтобы добавить шлюз локальной сети с одним префиксом адреса, используйте этот пример:

    New-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg `
    -Location 'West US' -GatewayIpAddress '23.99.221.164' -AddressPrefix '10.5.51.0/24'

Чтобы добавить шлюз локальной сети с несколькими префиксами адреса, используйте этот пример:

    New-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg `
    -Location 'West US' -GatewayIpAddress '23.99.221.164' -AddressPrefix @('10.0.0.0/24','20.0.0.0/24')

### <a name="to-modify-ip-address-prefixes-for-your-local-network-gateway"></a>Изменение префиксов IP-адресов для локального сетевого шлюза
Иногда префиксы адресов шлюза локальной сети могут изменяться. Действия по изменению префиксов IP-адресов зависят от того, создано ли подключение через VPN-шлюз. См. раздел [Изменение префиксов IP-адресов для локального сетевого шлюза](#modify) этой статьи.

## <a name="a-namepublicipa4-request-a-public-ip-address-for-the-vpn-gateway"></a><a name="PublicIP"></a>4. Запрос общедоступного IP-адреса для VPN-шлюза
Затем вам нужно запросить общедоступный IP-адрес, который будет выделен для VPN-шлюза виртуальной сети Azure. Он отличается от IP-адреса, назначенного VPN-устройству. Этот адрес присваивается VPN-шлюзу Azure отдельно. Указать необходимый вам IP-адрес нельзя. Он выделяется для шлюза динамически. Этот IP-адрес будет использоваться при настройке локального VPN-устройства для подключения к шлюзу.

VPN-шлюз Azure для модели развертывания, в которой используется диспетчер ресурсов, в настоящее время поддерживает только общедоступные IP-адреса, которые выделяются динамически. Однако это не значит, что IP-адрес изменится. IP-адрес VPN-шлюза Azure изменяется только после его удаления и повторного создания. Общедоступный IP-адрес шлюза остается прежним после изменения размера, сброса или обновления VPN-шлюза Azure, а также после других процедур по его обслуживанию.

Используйте следующий пример PowerShell.

    $gwpip= New-AzureRmPublicIpAddress -Name gwpip -ResourceGroupName testrg -Location 'West US' -AllocationMethod Dynamic

## <a name="a-namegatewayipconfiga5-create-the-gateway-ip-addressing-configuration"></a><a name="GatewayIPConfig"></a>5. Создание конфигурации IP-адресации шлюза
Конфигурация шлюза определяет используемые подсеть и общедоступный IP-адрес. Используйте следующий пример, чтобы создать конфигурацию шлюза.

    $vnet = Get-AzureRmVirtualNetwork -Name testvnet -ResourceGroupName testrg
    $subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
    $gwipconfig = New-AzureRmVirtualNetworkGatewayIpConfig -Name gwipconfig1 -SubnetId $subnet.Id -PublicIpAddressId $gwpip.Id 

## <a name="a-namecreategatewaya6-create-the-virtual-network-gateway"></a><a name="CreateGateway"></a>6. Создание шлюза виртуальной сети
На этом этапе вы создадите шлюз виртуальной сети. Обратите внимание, что создание шлюза может занять некоторое время — 45 минут или больше. 

Используйте следующие значения:

* Для конфигурации "сеть — сеть" задайте для параметра *-GatewayType* значение *Vpn*. Тип шлюза всегда зависит от реализуемой конфигурации. Например, для других конфигураций шлюза может потребоваться тип шлюза ExpressRoute. 
* У параметра *-VpnType* может быть значение *RouteBased* (в некоторых документах такой шлюз называется шлюзом с динамической маршрутизацией) или *PolicyBased* (в некоторых документах — шлюз со статической маршрутизацией). Дополнительные сведения о типах VPN-шлюзов см. в статье [Основные сведения о VPN-шлюзах Azure](vpn-gateway-about-vpngateways.md#vpntype).
* У параметра *-GatewaySku* может быть значение *Basic*, *Standard* или *HighPerformance*.     
  
        New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg `
        -Location 'West US' -IpConfigurations $gwipconfig -GatewayType Vpn `
        -VpnType RouteBased -GatewaySku Standard

## <a name="a-nameconfigurevpndevicea7-configure-your-vpn-device"></a><a name="ConfigureVPNDevice"></a>7. Настройка устройства VPN
На этом этапе вам понадобится общедоступный IP-адрес шлюза виртуальной сети для настройки локального VPN-устройства. Обратитесь к производителю устройства, чтобы получить конкретные сведения о конфигурации. Дополнительные сведения также см. в статье о [VPN-устройствах](vpn-gateway-about-vpn-devices.md).

Чтобы найти общедоступный IP-адрес шлюза виртуальной сети, используйте следующий пример.

    Get-AzureRmPublicIpAddress -Name gwpip -ResourceGroupName testrg

## <a name="a-namecreateconnectiona8-create-the-vpn-connection"></a><a name="CreateConnection"></a>8. Создание VPN-подключения
Далее вам нужно создать VPN-подключение типа "сеть — сеть" между шлюзом виртуальной сети и VPN-устройством. Обязательно подставьте собственные значения. Общий ключ должен соответствовать значению, использованному в конфигурации VPN-устройства. Обратите внимание, что для подключения типа "сеть — сеть" параметр `-ConnectionType` имеет значение *IPsec*. 

Задайте переменные.

    $gateway1 = Get-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
    $local = Get-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg

Создайте подключение.

    New-AzureRmVirtualNetworkGatewayConnection -Name localtovon -ResourceGroupName testrg `
    -Location 'West US' -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local `
    -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'

Через некоторое время будет установлено подключение. 

## <a name="a-nametoverifyato-verify-a-vpn-connection"></a><a name="toverify"></a>Проверка VPN-подключения
Существует несколько разных способов для проверки VPN-подключения.

[!INCLUDE [vpn-gateway-verify-connection-rm](../../includes/vpn-gateway-verify-connection-rm-include.md)]

## <a name="a-namemodifyato-modify-ip-address-prefixes-for-a-local-network-gateway"></a><a name="modify"></a>Изменение префиксов IP-адресов для локального сетевого шлюза
Если вам нужно изменить префиксы для шлюза локальной сети, сделайте следующее. Мы приводим два набора инструкций. Выбор тех или других инструкций зависит от того, создано ли у вас подключение через шлюз. 

[!INCLUDE [vpn-gateway-modify-ip-prefix-rm](../../includes/vpn-gateway-modify-ip-prefix-rm-include.md)]

## <a name="a-namemodifygwipaddressato-modify-the-gateway-ip-address-for-a-local-network-gateway"></a><a name="modifygwipaddress"></a>Изменение IP-адреса шлюза для локального сетевого шлюза
[!INCLUDE [vpn-gateway-modify-lng-gateway-ip-rm](../../includes/vpn-gateway-modify-lng-gateway-ip-rm-include.md)]

## <a name="next-steps"></a>Дальнейшие действия
* В виртуальные сети можно добавлять виртуальные машины. Инструкции см. в статье о [создании виртуальной машины](../virtual-machines/virtual-machines-windows-hero-tutorial.md).
* Сведения о BGP см. в статьях [Обзор использования BGP с VPN-шлюзами Azure](vpn-gateway-bgp-overview.md) и [Настройка BGP на VPN-шлюзах Azure с помощью Azure Resource Manager и PowerShell](vpn-gateway-bgp-resource-manager-ps.md).




<!--HONumber=Nov16_HO2-->



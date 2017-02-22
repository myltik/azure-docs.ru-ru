---
title: "Параметры VPN-шлюза для распределенных подключений Azure | Документация Майкрософт"
description: "Узнайте о параметрах VPN-шлюза для шлюзов виртуальной сети Azure."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager,azure-service-management
ms.assetid: ae665bc5-0089-45d0-a0d5-bc0ab4e79899
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/13/2017
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: b902d2e79633959a6f76ddd45b1193177b0e8465
ms.openlocfilehash: 1ac5a78c8d9419e4c641bf66f8dac7aa8cbcd179


---
# <a name="about-vpn-gateway-configuration-settings"></a>Сведения о параметрах конфигурации VPN-шлюза
VPN-шлюз — это разновидность шлюза виртуальной сети, который передает зашифрованный трафик между виртуальной сетью и локальным расположением через общедоступное подключение. VPN-шлюз можно также использовать для передачи трафика между виртуальными сетями в магистрали Azure.

Подключение VPN-шлюза зависит от конфигурации нескольких ресурсов, каждый из которых содержит настраиваемые параметры. В разделах этой статьи рассматриваются ресурсы и параметры, относящиеся к VPN-шлюзу для виртуальной сети, созданной на основе модели развертывания с помощью Resource Manager. Описания и схемы топологий для каждого варианта подключения можно найти в статье [Основные сведения о VPN-шлюзах Azure](vpn-gateway-about-vpngateways.md).  

## <a name="a-namegwtypeagateway-types"></a><a name="gwtype"></a>Типы шлюзов
У каждой виртуальной сети может быть только один шлюз виртуальной сети каждого типа. При создании шлюза виртуальной сети необходимо убедиться, что в конфигурации указан правильный тип шлюза.

Для -GatewayType доступны приведенные ниже значения. 

* Vpn
* ExpressRoute

Для VPN-шлюза требуется `-GatewayType` *Vpn*.  

Пример:

    New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg `
    -Location 'West US' -IpConfigurations $gwipconfig -GatewayType Vpn `
    -VpnType RouteBased


## <a name="a-namegwskuagateway-skus"></a><a name="gwsku"></a>SKU шлюзов
[!INCLUDE [vpn-gateway-gwsku-include](../../includes/vpn-gateway-gwsku-include.md)]

### <a name="configuring-the-gateway-sku"></a>Настройка SKU шлюза
####<a name="specifying-the-gateway-sku-in-the-azure-portal"></a>Указание SKU шлюза на портале Azure

Если для создания шлюза виртуальной сети Resource Manager используется портал Azure, выбрать SKU шлюза можно в раскрывающемся списке. Представленные параметры соответствуют выбранным типу шлюза и типу VPN.

Например, если выбрать тип шлюза "VPN" и тип VPN "на базе политик", будет отображаться только SKU "Basic", так как это единственный SKU, доступный для VPN на базе политик. Если выбрать "На основе маршрутов", можно выбрать SKU категории Basic, Standard и HighPerformance. 

####<a name="specifying-the-gateway-sku-using-powershell"></a>Указание SKU шлюза с помощью PowerShell

В следующем примере PowerShell используется `-GatewaySku` со значением *Standard*.

    New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg `
    -Location 'West US' -IpConfigurations $gwipconfig -GatewaySku Standard `
    -GatewayType Vpn -VpnType RouteBased

####<a name="changing-a-gateway-sku"></a>Изменение SKU шлюза

Для обновления своего SKU шлюза до более производительной версии (с Basic/Standard до HighPerformance) можно воспользоваться командлетом PowerShell `Resize-AzureRmVirtualNetworkGateway`. С помощью этого командлета также можно перейти на более низкий уровень SKU.

В следующем примере PowerShell показано изменение размера шлюза до SKU со значением HighPerformance.

    $gw = Get-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
    Resize-AzureRmVirtualNetworkGateway -VirtualNetworkGateway $gw -GatewaySku HighPerformance

### <a name="estimated-aggregate-throughput-by-gateway-sku-and-type"></a>Расчетная суммарная пропускная способность в зависимости от SKU и типа шлюза
[!INCLUDE [vpn-gateway-table-gwtype-aggthroughput](../../includes/vpn-gateway-table-gwtype-aggtput-include.md)]

## <a name="a-nameconnectiontypeaconnection-types"></a><a name="connectiontype"></a>Типы подключения
В модели развертывания с помощью Resource Manager каждой конфигурации необходим определенный тип подключения шлюза виртуальной сети. Для `-ConnectionType` в PowerShell можно указать такие значения (развертывание Resource Manager):

* IPsec
* Vnet2Vnet
* ExpressRoute
* VPNClient

В следующем примере PowerShell создается подключение типа "сеть — сеть", для которого требуется тип *IPsec*.

    New-AzureRmVirtualNetworkGatewayConnection -Name localtovon -ResourceGroupName testrg `
    -Location 'West US' -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local `
    -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'


## <a name="a-namevpntypeavpn-types"></a><a name="vpntype"></a>Типы VPN
При создании шлюза виртуальной сети для конфигурации VPN-шлюза необходимо указать тип VPN. Выбор типа VPN зависит от топологии подключений, которую вы хотите создать. Например, для подключения типа "точка — сеть" требуется тип VPN на основе маршрутов. Тип VPN может также зависеть от оборудования, которое будет использоваться. Для конфигураций "сеть — сеть" требуется VPN-устройство. Некоторые VPN-устройства поддерживают только определенный тип VPN.

Выбранный тип VPN должен удовлетворять всем требованиям к подключению решения, которое вы хотите создать. Например, если вы хотите создать подключение VPN-шлюза типа "сеть — сеть" и подключение VPN-шлюза типа "точка — сеть" для одной и той же виртуальной сети, то вам следует использовать тип VPN *RouteBased* , так как он необходим для подключения типа "точка — сеть". Необходимо также проверить, поддерживает ли ваше VPN-устройство VPN-подключение на основе маршрутов. 

После создания шлюза виртуальной сети изменить тип VPN невозможно. Для этого потребуется удалить шлюз виртуальной сети и создать новый. Существует два типа VPN:

[!INCLUDE [vpn-gateway-vpntype](../../includes/vpn-gateway-vpntype-include.md)]

В следующем примере PowerShell используется `-VpnType` со значением *RouteBased*. При создании шлюза необходимо убедиться, что в конфигурации указано правильное значение -VpnType. 

    New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg `
    -Location 'West US' -IpConfigurations $gwipconfig `
    -GatewayType Vpn -VpnType RouteBased

## <a name="a-namerequirementsagateway-requirements"></a><a name="requirements"></a>Требования к шлюзу
[!INCLUDE [vpn-gateway-table-requirements](../../includes/vpn-gateway-table-requirements-include.md)]

## <a name="a-namegwsubagateway-subnet"></a><a name="gwsub"></a>Подсеть шлюза
Чтобы настроить шлюз виртуальной сети для виртуальной сети, сначала нужно создать подсеть шлюза. Подсеть шлюза содержит IP-адреса, которые используют службы шлюза виртуальной сети. Чтобы подсети шлюзов работали правильно, каждую подсеть нужно назвать *GatewaySubnet* . Это имя указывает Azure, что данную подсеть следует использовать для шлюза.

При создании подсети шлюза указывается количество IP-адресов, которое содержит подсеть. IP-адреса в подсети шлюза выделяются службе шлюза. В некоторых конфигурациях службам шлюза требуется выделить больше IP-адресов. Необходимо убедиться, что подсеть шлюза содержит достаточно IP-адресов с учетом будущего роста и возможных дополнительных конфигураций подключения. Хотя можно создать подсеть шлюза всего лишь с размером /29, мы рекомендуем создавать подсети с размером /28 или больше (/28, /27, /26 и т. д.). Просмотрите требования для конфигурации, которую требуется создать, и убедитесь, что подсеть шлюза соответствует этим требованиям.

В примере PowerShell для Resource Manager ниже показана подсеть шлюза GatewaySubnet. Здесь приведена нотация CIDR, указывающая размер /27, при котором можно использовать достаточно IP-адресов для большинства существующих конфигураций.

    Add-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.3.0/27

[!INCLUDE [vpn-gateway-no-nsg](../../includes/vpn-gateway-no-nsg-include.md)]

## <a name="a-namelngalocal-network-gateways"></a><a name="lng"></a>Локальные сетевые шлюзы
При создании конфигурации VPN-шлюза нередко шлюз локальной сети представляет ваше локальное расположение. В классической модели развертывания шлюз локальной сети называется "локальным сайтом". 

Для локального сетевого шлюза следует задать имя и общий IP-адрес локального VPN-устройства, а также указать префиксы адресов, принадлежащие к локальному расположению. Azure проверяет наличие сетевого трафика по префиксам адресов назначения, учитывает конфигурацию, указанную для локального сетевого шлюза, и соответствующим образом направляет пакеты. Можно также указать шлюзы локальной сети для конфигураций типа "виртуальная сеть — виртуальная сеть", использующих подключение к VPN-шлюзу.

Следующий пример PowerShell создает шлюз локальной сети.

    New-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg `
    -Location 'West US' -GatewayIpAddress '23.99.221.164' -AddressPrefix '10.5.51.0/24'

Иногда возникает необходимость изменить параметры локального сетевого шлюза. Например, при добавлении или изменении диапазона адресов, либо при изменении IP-адреса VPN-устройства. Для классической виртуальной сети эти параметры можно изменить на классическом портале, на странице "Локальные сети". В случае использования Resource Manager ознакомьтесь с разделом [Изменение параметров шлюза локальной сети с помощью PowerShell](vpn-gateway-modify-local-network-gateway.md).

## <a name="a-nameresourcesarest-apis-and-powershell-cmdlets"></a><a name="resources"></a>Интерфейсы REST API и командлеты PowerShell
Дополнительные технические материалы и специальные требования к синтаксису, действующие при использовании интерфейсов REST API и командлетов PowerShell для настройки конфигураций VPN-шлюзов, доступны на приведенных ниже страницах.

| **Классический** | **Диспетчер ресурсов** |
| --- | --- |
| [PowerShell](https://msdn.microsoft.com/library/mt270335.aspx) |[PowerShell](https://msdn.microsoft.com/library/mt163510.aspx) |
| [ИНТЕРФЕЙС REST API](https://msdn.microsoft.com/library/jj154113.aspx) |[ИНТЕРФЕЙС REST API](https://msdn.microsoft.com/library/mt163859.aspx) |

## <a name="next-steps"></a>Дальнейшие действия
Дополнительные сведения о доступных конфигурациях подключений см. в статье [Основные сведения о VPN-шлюзах Azure](vpn-gateway-about-vpngateways.md). 




<!--HONumber=Feb17_HO2-->



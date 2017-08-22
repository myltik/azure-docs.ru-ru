---
title: "Конфигурация VPN-устройств сторонних производителей для подключения к VPN-шлюзам Azure | Документация Майкрософт"
description: "В этой статье содержится обзор конфигураций VPN-устройств сторонних производителей, позволяющих подключаться к VPN-шлюзам Azure."
services: vpn-gateway
documentationcenter: na
author: yushwang
manager: rossort
editor: 
tags: 
ms.assetid: a8bfc955-de49-4172-95ac-5257e262d7ea
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/20/2017
ms.author: yushwang
ms.translationtype: HT
ms.sourcegitcommit: d941879aee6042b38b7f5569cd4e31cb78b4ad33
ms.openlocfilehash: 72dab85bb882b05d72cef26bef70437695b70416
ms.contentlocale: ru-ru
ms.lasthandoff: 07/10/2017


---
# <a name="overview-of-3rd-party-vpn-device-configurations"></a>Обзор конфигураций VPN-устройств сторонних производителей
В этой статье содержится обзор конфигураций локальных VPN-устройств, позволяющих подключаться к VPN-шлюзам Azure. Для подключения к различным локальным VPN-устройствам с одинаковыми параметрами будут использованы примеры виртуальной сети Azure и настройки VPN-шлюза.

## <a name="device-requirements"></a>Требования к устройствам
В VPN-шлюзах Azure используются стандартные наборы протоколов IPsec/IKE для VPN-туннелей S2S. Подробные параметры протокола IPsec/IKE и алгоритмы шифрования по умолчанию для VPN-шлюзов Azure см. в статье [VPN-устройства и параметры IPsec/IKE для подключений типа "сеть — сеть" через VPN-шлюз](vpn-gateway-about-vpn-devices.md). Дополнительно можно указать точную комбинацию алгоритмов шифрования и уровней стойкости ключа для определенного подключения, как описано в статье [Требования к шифрованию и VPN-шлюзы Azure](vpn-gateway-about-compliance-crypto.md).

## <a name ="singletunnel"></a>Одиночный VPN-туннель
Первая топология состоит из одного VPN-туннеля S2S между VPN-шлюзом Azure и локальным VPN-устройством. При необходимости можно также настроить BGP поверх VPN-туннеля.

![Одиночный туннель](./media/vpn-gateway-3rdparty-device-config-overview/singletunnel.png)

Подробное пошаговое руководство см. в статье [Создание подключения типа "сеть — сеть" на портале Azure](vpn-gateway-howto-site-to-site-resource-manager-portal.md). В следующих разделах перечислены параметры и представлен пример скрипта PowerShell для начала работы.

### <a name="network-and-vpn-gateway-information"></a>Сведения о сети и VPN-шлюзе
В этом разделе перечислены параметры, использующиеся в примерах, упомянутых выше.

| **Параметр**                | **Значение**                    |
| ---                          | ---                          |
| Префиксы адресов виртуальной сети        | 10.11.0.0/16;<br>10.12.0.0/16 |
| IP-адрес VPN-шлюза Azure         | IP-адрес VPN-шлюза Azure         |
| Префиксы адресов в локальной среде | 10.51.0.0/16<br>10.52.0.0/16 |
| IP-адрес VPN-устройства в локальной среде    | IP-адрес VPN-устройства в локальной среде    |
| *ASN для BGP виртуальной сети                | 65010                        |
| *IP-адрес узла BGP Azure           | 10.12.255.30                 |
| *ASN BGP в локальной среде         | 65050                        |
| *IP-адрес узла BGP в локальной среде     | 10.52.255.254                |

* (*) Необязательные параметры только для BGP

### <a name="sample-powershell-script"></a>Пример скрипта PowerShell
Подробные инструкции содержатся в статье [Создание виртуальной сети с VPN-подключением типа "сеть — сеть" с помощью PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md). В этом разделе приведен пример скрипта для начала работы.

```powershell
# Declare your variables

$Sub1          = "Replace_With_Your_Subcription_Name"
$RG1           = "TestRG1"
$Location1     = "East US 2"
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
$GWIPName1     = "VNet1GWIP"
$GWIPconfName1 = "gwipconf1"
$Connection15  = "VNet1toSite5"
$LNGName5      = "Site5"
$LNGPrefix50   = "10.52.255.254/32"
$LNGPrefix51   = "10.51.0.0/16"
$LNGPrefix52   = "10.52.0.0/16"
$LNGIP5        = "Your_VPN_Device_IP"
$LNGASN5       = 65050
$BGPPeerIP5    = "10.52.255.254"

# Connect to your subscription and create a new resource group

Login-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName $Sub1
New-AzureRmResourceGroup -Name $RG1 -Location $Location1

# Create virtual network

$fesub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName1 -AddressPrefix $FESubPrefix1 $besub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName1 -AddressPrefix $BESubPrefix1
$gwsub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName1 -AddressPrefix $GWSubPrefix1

New-AzureRmVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1 -Location $Location1 -AddressPrefix $VNetPrefix11,$VNetPrefix12 -Subnet $fesub1,$besub1,$gwsub1

# Create VPN gateway

$gwpip1    = New-AzureRmPublicIpAddress -Name $GWIPName1 -ResourceGroupName $RG1 -Location $Location1 -AllocationMethod Dynamic
$vnet1     = Get-AzureRmVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1
$subnet1   = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet1
$gwipconf1 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfName1 -Subnet $subnet1 -PublicIpAddress $gwpip1

New-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1 -Location $Location1 -IpConfigurations $gwipconf1 -GatewayType Vpn -VpnType RouteBased -GatewaySku VpnGw1 -Asn $VNet1ASN

# Create local network gateway

New-AzureRmLocalNetworkGateway -Name $LNGName5 -ResourceGroupName $RG1 -Location $Location1 -GatewayIpAddress $LNGIP5 -AddressPrefix $LNGPrefix51,$LNGPrefix52 -Asn $LNGASN5 -BgpPeeringAddress $BGPPeerIP5

# Create the S2S VPN connection

$vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
$lng5gw  = Get-AzureRmLocalNetworkGateway -Name $LNGName5 -ResourceGroupName $RG1

New-AzureRmVirtualNetworkGatewayConnection -Name $Connection15 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng5gw -Location $Location1 -ConnectionType IPsec -SharedKey 'AzureA1b2C3' -EnableBGP $False
```

### <a name ="policybased"></a> [Необязательно] Использование настраиваемой политики IPsec/IKE с параметром UsePolicyBasedTrafficSelectors
Если ваши VPN-устройства не поддерживают селекторы трафика "любой к любому" (конфигурация на основе маршрутов или на основе VTI), вам потребуется создать настраиваемую политику IPsec/IKE и настроить параметр UsePolicyBasedTrafficSelectors, как описано в [этой статье](vpn-gateway-connect-multiple-policybased-rm-ps.md).

> [!IMPORTANT]
> Вам необходимо создать политику IPsec/IKE для включения параметра "UsePolicyBasedTrafficSelectors" для подключения.

Пример сценария ниже создает политику IPsec/IKE со следующими параметрами и алгоритмами:
* IKEv2: AES256, SHA384, DHGroup24
* IPsec: AES256, SHA1, PFS24, время существования сопоставления безопасности 7200 секунд, размер 20480000 КБ (20 ГБ).

Затем политика применяется, и для подключения включается параметр UsePolicyBasedTrafficSelectors.

```powershell
$ipsecpolicy5 = New-AzureRmIpsecPolicy -IkeEncryption AES256 -IkeIntegrity SHA384 -DhGroup DHGroup24 -IpsecEncryption AES256 -IpsecIntegrity SHA1 -PfsGroup PFS24 -SALifeTimeSeconds 7200 -SADataSizeKilobytes 20480000

$vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
$lng5gw  = Get-AzureRmLocalNetworkGateway -Name $LNGName5 -ResourceGroupName $RG1

New-AzureRmVirtualNetworkGatewayConnection -Name $Connection15 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng5gw -Location $Location1 -ConnectionType IPsec -SharedKey 'AzureA1b2C3' -EnableBGP $False -IpsecPolicies $ipsecpolicy5 -UsePolicyBasedTrafficSelectors $True
```

### <a name ="bgp"></a>[Необязательно] Использование BGP в VPN-подключении типа "сеть — сеть"
В подключении можно при необходимости использовать BGP. См. статью [Настройка BGP на VPN-шлюзах Azure с помощью PowerShell](vpn-gateway-bgp-resource-manager-ps.md). Существует два отличия.

Префиксы адресов в локальной среде могут быть адресом одиночного хоста, IP-адресом узла BGP в локальной среде:

```powershell
New-AzureRmLocalNetworkGateway -Name $LNGName5 -ResourceGroupName $RG1 -Location $Location1 -GatewayIpAddress $LNGIP5 -AddressPrefix $LNGPrefix50 -Asn $LNGASN5 -BgpPeeringAddress $BGPPeerIP5
```

При создании подключения необходимо задать для параметра -EnableBGP значение $True:

```powershell
New-AzureRmVirtualNetworkGatewayConnection -Name $Connection15 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng5gw -Location $Location1 -ConnectionType IPsec -SharedKey 'AzureA1b2C3' -EnableBGP $True
```

## <a name="next-steps"></a>Дальнейшие действия
Инструкции по настройке распределенных подключений (локальных и между виртуальными сетями) с конфигурацией "активный — активный" см. в статье [Настройка VPN-подключений типа "сеть — сеть" в режиме "активный — активный" для VPN-шлюзов Azure с помощью Azure Resource Manager и PowerShell](vpn-gateway-activeactive-rm-powershell.md).



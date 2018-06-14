---
title: Создание VPN-подключений Azure S2S и управление ими с помощью PowerShell | Документация Майкрософт
description: Руководство по созданию VPN-подключений Azure S2S и управлению ими с помощью модуля Azure PowerShell
services: vpn-gateway
documentationcenter: na
author: yushwang
manager: rossort
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: vpn-gateway
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure
ms.date: 05/08/2018
ms.author: yushwang
ms.custom: mvc
ms.openlocfilehash: da077f013c558448be63dce9b215ded99362d22e
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/10/2018
ms.locfileid: "34012947"
---
# <a name="create-and-manage-s2s-vpn-connections-with-the-azure-powershell-module"></a>Создание VPN-подключений Azure S2S и управление ими с помощью модуля Azure PowerShell

VPN-подключения Azure S2S предоставляют безопасное распределенное подключение между локальными сетями клиента и Azure. Это пошаговое руководство по жизненным циклам VPN-подключений Azure S2S, таким как создание VPN-подключения Azure S2S и управление им. Вы узнаете, как выполнять следующие задачи:

> [!div class="checklist"]
> * Создание VPN-подключения S2S.
> * Обновление свойств подключения: общий ключ, BGP, политика IPsec/IKE.
> * Добавление дополнительных VPN-подключений.
> * Удаление VPN-подключения.

На следующей схеме показана топология для этого руководства:

![Схема по VPN-подключению типа "сеть — сеть"](./media/vpn-gateway-tutorial-vpnconnection-powershell/site-to-site-diagram.png)

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Чтобы установить и использовать PowerShell локально для работы с этим руководством, вам понадобится модуль Azure PowerShell 5.3 или более поздней версии. Чтобы узнать версию, выполните команду `Get-Module -ListAvailable AzureRM`. Если вам необходимо выполнить обновление, ознакомьтесь со статьей, посвященной [установке модуля Azure PowerShell](/powershell/azure/install-azurerm-ps). Если модуль PowerShell запущен локально, необходимо также выполнить командлет `Login-AzureRmAccount`, чтобы создать подключение к Azure.

## <a name="requirements"></a>Требования

Выполните первое руководство, [Create and Manage VPN gateway with the Azure PowerShell module](vpn-gateway-tutorial-create-gateway-powershell.md) (Создание VPN-шлюза и управление им с помощью модуля Azure PowerShell), чтобы создать следующие ресурсы:

1. группа ресурсов (TestRG1), виртуальная сеть (VNet1) и подсеть шлюза.
2. VPN-шлюз (VNet1GW).

Ниже перечислены значения параметров виртуальной сети. Запишите дополнительные значения шлюза локальной сети, представляющие локальную сеть. Измените значения на основе настроек среды и сети.

```azurepowershell-interactive
# Virtual network
$RG1         = "TestRG1"
$VNet1       = "VNet1"
$Location1   = "East US"
$VNet1Prefix = "10.1.0.0/16"
$VNet1ASN    = 65010
$Gw1         = "VNet1GW"

# On-premises network
$LNG1        = "VPNsite1"
$LNGprefix1  = "10.101.0.0/24"
$LNGprefix2  = "10.101.1.0/24"
$LNGIP1      = "YourDevicePublicIP"

# Optional - on-premises BGP properties
$LNGASN1     = 65011
$BGPPeerIP1  = "10.101.1.254"

# Connection
$Connection1 = "VNet1ToSite1"
```

Рабочий процесс для создания VPN-подключения Azure S2S прост:

1. Создайте шлюз локальной сети, чтобы представить локальную сеть.
2. Создайте подключение между VPN-шлюзом Azure и шлюзом локальной сети.

## <a name="create-a-local-network-gateway"></a>Создание локального сетевого шлюза

Шлюз локальной сети представляет локальную сеть. Вы можете указать свойства локальной сети в шлюзе локальной сети, включая:

* общедоступный IP-адрес вашего VPN-устройства;
* локальное адресное пространство;
* атрибуты BGP: IP-адрес партнера BGP и номер AS (необязательно).

Создайте шлюз локальной сети с помощью команды [New-AzureRmLocalNetworkGateway](/powershell/module/azurerm.resources/new-azurermlocalnetworkgateway).

```azurepowershell-interactive
New-AzureRmLocalNetworkGateway -Name $LNG1 -ResourceGroupName $RG1 `
  -Location 'East US' -GatewayIpAddress $LNGIP1 -AddressPrefix $LNGprefix1,$LNGprefix2
```

## <a name="create-a-s2s-vpn-connection"></a>Создание VPN-подключения Azure S2S

Далее вам нужно создать VPN-подключение типа "сеть — сеть" между шлюзом виртуальной сети и VPN-устройством с помощью [New-AzureRmVirtualNetworkGatewayConnection](/powershell/module/azurerm.resources/new-azurermvirtualnetworkgatewayconnection). Обратите внимание, что для VPN-подключения типа "сеть — сеть" параметр -ConnectionType имеет значение *IPsec*.

```azurepowershell-interactive
$vng1 = Get-AzureRmVirtualNetworkGateway -Name $GW1  -ResourceGroupName $RG1
$lng1 = Get-AzureRmLocalNetworkGateway   -Name $LNG1 -ResourceGroupName $RG1

New-AzureRmVirtualNetworkGatewayConnection -Name $Connection1 -ResourceGroupName $RG1 `
  -Location $Location1 -VirtualNetworkGateway1 $vng1 -LocalNetworkGateway2 $lng1 `
  -ConnectionType IPsec -SharedKey "Azure@!b2C3"
```

Добавьте дополнительное свойство **-EnableBGP $True**, чтобы включить использование протокола BGP. Он отключен по умолчанию.

## <a name="update-the-vpn-connection-pre-shared-key-bgp-and-ipsecike-policy"></a>Обновление свойств VPN-подключения: общий ключ, BGP и политики IPsec/IKE

### <a name="view-and-update-your-pre-shared-key"></a>Просмотр общего ключа и его обновление

VPN-подключение Azure S2S использует общий ключ (секрет) для проверки подлинности между локальным VPN-устройством и VPN-шлюзом Azure. Вы можете просмотреть и обновить общий ключ подключения с помощью [Get-AzureRmVirtualNetworkGatewayConnectionSharedKey](/powershell/module/azurerm.resources/get-azurermvirtualnetworkgatewayconnectionsharedkey) и [Set-AzureRmVirtualNetworkGatewayConnectionSharedKey](/powershell/module/azurerm.resources/set-azurermvirtualnetworkgatewayconnectionsharedkey).

> [!IMPORTANT]
> Общий ключ — это строка **печатных символов ASCII** длиной не более 128 символов.

Эта команда показывает общий ключ для подключения:

```azurepowershell-interactive
Get-AzureRmVirtualNetworkGatewayConnectionSharedKey `
  -Name $Connection1 -ResourceGroupName $RG1
```

В результате вы получите строку с **Azure@!b2C3** после строк, приведенных в примере выше. Используйте команду ниже, чтобы изменить значение общего ключа на **Azure@!_b2=C3**:

```azurepowershell-interactive
Set-AzureRmVirtualNetworkGatewayConnectionSharedKey `
  -Name $Connection1 -ResourceGroupName $RG1 `
  -Value "Azure@!_b2=C3"
```

### <a name="enable-bgp-on-vpn-connection"></a>Включение протокола BGP в VPN-подключении

VPN-шлюз Azure поддерживает протокол динамической маршрутизации BGP. Протокол BGP можно включить в каждом отдельном подключении, в зависимости от того, используется ли BGP в локальной сети или устройствах. Укажите следующие свойства перед включением BGP для подключения:

* ASN (номер автономной системы) VPN-подключения Azure;
* ASN шлюза локальной сети;
* IP-адреса для узла BGP шлюза локальной сети.

Если вы не настроили свойства BGP, используйте следующие команды для добавления этих свойств в VPN-шлюз и шлюз локальной сети: [Set-AzureRmVirtualNetworkGateway](/powershell/module/azurerm.resources/set-azurermvirtualnetworkgateway) и [Set-AzureRmLocalNetworkGateway](/powershell/module/azurerm.resources/set-azurermlocalnetworkgateway).

```azurepowershell-interactive
$vng1 = Get-AzureRmVirtualNetworkGateway -Name $GW1  -ResourceGroupName $RG1
Set-AzureRmVirtualNetworkGateway -VirtualNetworkGateway $vng1 -Asn $VNet1ASN

$lng1 = Get-AzureRmLocalNetworkGateway   -Name $LNG1 -ResourceGroupName $RG1
Set-AzureRmLocalNetworkGateway -LocalNetworkGateway $lng1 `
  -Asn $LNGASN1 -BgpPeeringAddress $BGPPeerIP1
```

Включение BGP с помощью [Set-AzureRmVirtualNetworkGatewayConnection](/powershell/module/azurerm.resources/set-azurermvirtualnetworkgatewayconnection).

```azurepowershell-interactive
$connection = Get-AzureRmVirtualNetworkGatewayConnection `
  -Name $Connection1 -ResourceGroupName $RG1

Set-AzureRmVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection `
  -EnableBGP $True
```

BGP можно отключить, изменив значение свойства -EnableBGP на **$False**. Дополнительные сведения о BGP в VPN-шлюзах Azure см. в статье [Настройка BGP на VPN-шлюзах Azure с помощью PowerShell](vpn-gateway-bgp-resource-manager-ps.md).

### <a name="apply-a-custom-ipsecike-policy-on-the-connection"></a>Применение настраиваемой политики IPsec/IKE для подключения

Вы можете применить дополнительную политику IPsec/IKE для указания точного сочетания алгоритмов шифрования IPsec/IKE и активации ключевых преимуществ в подключении, вместо того чтобы использовать [предложения по умолчанию](vpn-gateway-about-vpn-devices.md#ipsec). Следующий пример скрипта создает другую политику IPsec/IKE с такими параметрами и алгоритмами:

* IKEv2: AES256, SHA256, DHGroup14.
* IPsec: AES128, SHA1, PFS14; время существования SA — 14 400 секунд и 102 400 000 КБ.

```azurepowershell-interactive
$connection = Get-AzureRmVirtualNetworkGatewayConnection -Name $Connection1 `
                -ResourceGroupName $RG1
$newpolicy  = New-AzureRmIpsecPolicy `
                -IkeEncryption AES256 -IkeIntegrity SHA256 -DhGroup DHGroup14 `
                -IpsecEncryption AES128 -IpsecIntegrity SHA1 -PfsGroup PFS2048 `
                -SALifeTimeSeconds 14400 -SADataSizeKilobytes 102400000

Set-AzureRmVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection `
  -IpsecPolicies $newpolicy
```

Полный список алгоритмов и подробные инструкции см. в статье [Настройка политики IPsec/IKE для VPN-подключений типа "сеть — сеть" или "виртуальная сеть — виртуальная сеть"](vpn-gateway-ipsecikepolicy-rm-powershell.md).

## <a name="add-another-s2s-vpn-connection"></a>Добавление другого VPN-подключения S2S

Чтобы добавить дополнительное VPN-подключение S2S в тот же VPN-шлюз, создайте другой шлюз локальной сети и подключение между новым сетевым локальным шлюзом и VPN-шлюзом. Ниже приведен пример для этой статьи.

```azurepowershell-interactive
# On-premises network
$LNG2        = "VPNsite2"
$Location2   = "West US"
$LNGprefix21 = "10.102.0.0/24"
$LNGprefix22 = "10.102.1.0/24"
$LNGIP2      = "YourDevicePublicIP"
$Connection2 = "VNet1ToSite2"

New-AzureRmLocalNetworkGateway -Name $LNG2 -ResourceGroupName $RG1 `
  -Location $Location2 -GatewayIpAddress $LNGIP2 -AddressPrefix $LNGprefix21,$LNGprefix22

$vng1 = Get-AzureRmVirtualNetworkGateway -Name $GW1  -ResourceGroupName $RG1
$lng2 = Get-AzureRmLocalNetworkGateway   -Name $LNG2 -ResourceGroupName $RG1

New-AzureRmVirtualNetworkGatewayConnection -Name $Connection2 -ResourceGroupName $RG1 `
  -Location $Location1 -VirtualNetworkGateway1 $vng1 -LocalNetworkGateway2 $lng2 `
  -ConnectionType IPsec -SharedKey "AzureA1%b2_C3+"
```

Теперь у вас есть два VPN-подключения S2S к VPN-шлюзу Azure.

![Многосайтовое VPN-подключение](./media/vpn-gateway-tutorial-vpnconnection-powershell/multisite-connections.png)

## <a name="delete-a-s2s-vpn-connection"></a>Удаление VPN-подключения Azure S2S

Удалите VPN-подключение S2S с помощью [Remove-AzureRmVirtualNetworkGatewayConnection](/powershell/module/azurerm.resources/remove-azurermvirtualnetworkgatewayconnection).

```azurepowershell-interactive
Remove-AzureRmVirtualNetworkGatewayConnection -Name $Connection2 -ResourceGroupName $RG1
```

Удалите шлюз локальной сети, если он вам больше не требуется. Шлюз локальной сети нельзя удалить, если с ним связаны другие подключения.

```azurepowershell-interactive
Remove-AzureRmVirtualNetworkGatewayConnection -Name $LNG2 -ResourceGroupName $RG1
```

## <a name="next-steps"></a>Дополнительная информация

В этом руководстве описывалось создание VPN-подключений S2S и управление ими, в частности:

> [!div class="checklist"]
> * Создание VPN-подключения S2S.
> * Обновление свойств подключения: общий ключ, BGP, политика IPsec/IKE.
> * Добавление дополнительных VPN-подключений.
> * Удаление VPN-подключения.

Перейдите к следующим руководствам, чтобы узнать о подключениях типа "сеть — сеть", "виртуальная сеть — виртуальная сеть" и "точка — сеть".

> [!div class="nextstepaction"]
> * [Настройка подключения VPN-шлюза между виртуальными сетями на портале Azure](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
> * [Настройка подключения "точка — сеть" к виртуальной сети с использованием собственной аутентификации Azure на основе сертификата и портала Azure](vpn-gateway-howto-point-to-site-resource-manager-portal.md)

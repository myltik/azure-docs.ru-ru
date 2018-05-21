---
title: Создание VPN-шлюза Azure и управление им с помощью PowerShell | Документация Майкрософт
description: Руководство по созданию VPN-шлюза и управлению им с помощью модуля Azure PowerShell
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
ms.date: 04/24/2018
ms.author: yushwang
ms.custom: mvc
ms.openlocfilehash: 867a1c55c0f96b17f77049d7f24d47f41a90d9f9
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/10/2018
---
# <a name="create-and-manage-vpn-gateway-with-the-azure-powershell-module"></a>Создание VPN-шлюза и управление им с помощью модуля Azure PowerShell

VPN-шлюзы Azure предоставляют распределенное подключение между локальными сетями клиента и Azure. В этом руководстве приведены основные этапы развертывания VPN-шлюза Azure, такие как создание VPN-шлюза и управление им. Вы узнаете, как выполнять следующие задачи:

> [!div class="checklist"]
> * Создание VPN-шлюза
> * Изменение размера VPN-шлюза.
> * Сброс VPN-шлюза

На следующей схеме показаны виртуальная сеть и VPN-шлюз, созданные в рамках этого руководства.

![Виртуальная сеть и VPN-шлюз](./media/vpn-gateway-tutorial-create-gateway-powershell/vnet1-gateway.png)


[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Чтобы установить и использовать PowerShell локально для работы с этим руководством, вам понадобится модуль Azure PowerShell 5.3 или более поздней версии. Чтобы узнать версию, выполните команду `Get-Module -ListAvailable AzureRM`. Если вам необходимо выполнить обновление, ознакомьтесь со статьей, посвященной [установке модуля Azure PowerShell](/powershell/azure/install-azurerm-ps). Если модуль PowerShell запущен локально, необходимо также выполнить командлет `Login-AzureRmAccount`, чтобы создать подключение к Azure. 

## <a name="common-network-parameter-values"></a>Значения общих параметров сети

Измените следующие значения на основе настроек среды и сети.

```azurepowershell-interactive
$RG1         = "TestRG1"
$VNet1       = "VNet1"
$Location1   = "East US"
$FESubnet1   = "FrontEnd"
$BESubnet1   = "Backend"
$GwSubnet1   = "GatewaySubnet"
$VNet1Prefix = "10.1.0.0/16"
$FEPrefix1   = "10.1.0.0/24"
$BEPrefix1   = "10.1.1.0/24"
$GwPrefix1   = "10.1.255.0/27"
$VNet1ASN    = 65010
$DNS1        = "8.8.8.8"
$Gw1         = "VNet1GW"
$GwIP1       = "VNet1GWIP"
$GwIPConf1   = "gwipconf1"
```

## <a name="create-resource-group"></a>Создать группу ресурсов

Создайте группу ресурсов с помощью команды [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). Группа ресурсов Azure является логическим контейнером, в котором происходит развертывание ресурсов Azure и управление ими. Сначала необходимо создать группу ресурсов. В следующем примере группа ресурсов с именем *TestRG1* создается в регионе *восточная часть США*.

```azurepowershell-interactive
New-AzureRmResourceGroup -ResourceGroupName $RG1 -Location $Location1
```

## <a name="create-a-virtual-network"></a>Создать виртуальную сеть

VPN-шлюз Azure предоставляет распределенное подключение и функциональные возможности сервера VPN-подключения типа "точка — сеть" для виртуальной сети. Добавьте VPN-шлюз в имеющуюся виртуальную сеть или создайте виртуальную сеть и шлюз. В этом примере создается виртуальная сеть с тремя подсетями (внутренняя, серверная и подсеть шлюза) с помощью команд [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig) и [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork):

```azurepowershell-interactive
$fesub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubnet1 -AddressPrefix $FEPrefix1
$besub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubnet1 -AddressPrefix $BEPrefix1
$gwsub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubnet1 -AddressPrefix $GwPrefix1
$vnet   = New-AzureRmVirtualNetwork `
            -Name $VNet1 `
            -ResourceGroupName $RG1 `
            -Location $Location1 `
            -AddressPrefix $VNet1Prefix `
            -Subnet $fesub1,$besub1,$gwsub1
```

## <a name="request-a-public-ip-address-for-the-vpn-gateway"></a>Запрос общедоступного IP-адреса для VPN-шлюза

VPN-шлюзы Azure взаимодействуют с локальными VPN-устройствами через Интернет, чтобы выполнять согласование по протоколу IKE и устанавливать туннели IPsec. Создайте и назначьте общедоступный IP-адрес VPN-шлюза, как показано в приведенном ниже примере, используя команды [New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress) и [New-AzureRmVirtualNetworkGatewayIpConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworkgatewayipconfig):

> [!IMPORTANT]
> Сейчас для шлюза можно использовать только динамический общедоступный IP-адрес. Статический IP-адрес не поддерживается в VPN-шлюзах Azure.

```azurepowershell-interactive
$gwpip    = New-AzureRmPublicIpAddress -Name $GwIP1 -ResourceGroupName $RG1 `
              -Location $Location1 -AllocationMethod Dynamic
$subnet   = Get-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' `
              -VirtualNetwork $vnet
$gwipconf = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GwIPConf1 `
              -Subnet $subnet -PublicIpAddress $gwpip
```

## <a name="create-vpn-gateway"></a>Создание VPN-шлюза

Для создания VPN-шлюза требуется не менее 45 минут. Когда шлюз будет готов, можно создать подключение между вашей и другой виртуальной сетью. Также можно создать подключение между виртуальной сетью и локальным расположением. Создайте VPN-шлюз с помощью командлета [New-AzureRmVirtualNetworkGateway](/powershell/module/azurerm.network/New-AzureRmVirtualNetworkGateway).

```azurepowershell-interactive
New-AzureRmVirtualNetworkGateway -Name $Gw1 -ResourceGroupName $RG1 `
  -Location $Location1 -IpConfigurations $gwipconf -GatewayType Vpn `
  -VpnType RouteBased -GatewaySku VpnGw1
```

Ниже приведены значения ключевых параметров:
* GatewayType. Используйте **Vpn** для подключений типа "сеть — сеть" и "виртуальная сеть — виртуальная сеть".
* VpnType. Используйте **RouteBased** для взаимодействия с более широким диапазоном VPN-устройств и для получения доступа к дополнительным возможностям маршрутизации.
* GatewaySku. Значение по умолчанию — **VpnGw1**. Измените его на VpnGw2 или VpnGw3, если требуется более высокая пропускная способность или дополнительные подключения. Дополнительные сведения см. в разделе о [номерах SKU шлюзов](vpn-gateway-about-vpn-gateway-settings.md#gwsku).

Когда шлюз будет готов, можно создать подключение между вашей и другой виртуальной сетью или между виртуальной сетью и локальным расположением. Вы также можете настроить подключение P2S к вашей виртуальной сети с клиентского компьютера.

## <a name="resize-vpn-gateway"></a>Изменение размера VPN-шлюза

Номер SKU VPN-шлюза можно изменить после создания шлюза. Разные номера SKU шлюза поддерживают различные спецификации (например, пропускная способность, число подключений и т. д.). В следующем примере используется команда [Resize-AzureRmVirtualNetworkGateway](/powershell/module/azurerm.network/Resize-AzureRmVirtualNetworkGateway) для изменения размера шлюза с VpnGw1 на VpnGw2. Дополнительные сведения см. в разделе о [номерах SKU шлюзов](vpn-gateway-about-vpn-gateway-settings.md#gwsku).

```azurepowershell-interactive
$gw = Get-AzureRmVirtualNetworkGateway -Name $Gw1 -ResourceGroup $RG1
Resize-AzureRmVirtualNetworkGateway -GatewaySku VpnGw2 -VirtualNetworkGateway $gateway
```

Изменение размера VPN-шлюза также занимает около 30–45 минут. Эта операция **не** будет прерывать или удалять имеющиеся подключения и конфигурации.

## <a name="reset-vpn-gateway"></a>Сброс VPN-шлюза

В рамках выполнения шагов по устранению неполадок вы можете сбросить VPN-шлюз Azure, чтобы выполнить принудительный перезапуск конфигураций туннеля IPsec/IKE. Используйте команду [Reset-AzureRmVirtualNetworkGateway](/powershell/module/azurerm.network/Reset-AzureRmVirtualNetworkGateway), чтобы сбросить шлюз.

```azurepowershell-interactive
$gw = Get-AzureRmVirtualNetworkGateway -Name $Gw1 -ResourceGroup $RG1
Reset-AzureRmVirtualNetworkGateway -VirtualNetworkGateway $gateway
```

Дополнительные сведения см. в статье [Сброс VPN-шлюза](vpn-gateway-resetgw-classic.md).

## <a name="get-the-gateway-public-ip-address"></a>Получение общедоступного IP-адреса шлюза

Если вам известно имя общедоступного IP-адреса, используйте команду [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/Reset-AzureRmPublicIpAddress), чтобы просмотреть общедоступный IP-адрес, назначенный шлюзу.

```azurepowershell-interactive
$myGwIp = Get-AzureRmPublicIpAddress -Name $GwIP1 -ResourceGroup $RG1
$myGwIp.IpAddress
```

## <a name="delete-vpn-gateway"></a>Удаление VPN-шлюза

Для завершения настройки распределенного подключения и подключения типа "виртуальная сеть — виртуальная сеть" требуется несколько типов ресурсов, а также VPN-шлюз. Удалите подключения, связанные с VPN-шлюзом перед удалением самого шлюза. После удаления шлюза можно удалить назначенные для него общедоступные IP-адреса. Подробные сведения см. в статье [Delete a virtual network gateway using PowerShell](vpn-gateway-delete-vnet-gateway-powershell.md) (Удаление шлюза виртуальной сети с помощью PowerShell).

Если шлюз является частью прототипа развертывания или развертывания для подтверждения концепции, вы можете использовать команду [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup), чтобы удалить группу ресурсов, VPN-шлюз и все связанные ресурсы.

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name $RG1
```

## <a name="next-steps"></a>Дополнительная информация

В рамках этого руководства вы изучили основы создания VPN-шлюза и управления им. Вы также узнали, как выполнять следующие задачи:

> [!div class="checklist"]
> * Создание VPN-шлюза
> * Изменение размера VPN-шлюза.
> * Сброс VPN-шлюза

Перейдите к следующим руководствам, чтобы узнать о подключениях типа "сеть — сеть", "виртуальная сеть — виртуальная сеть" и "точка — сеть".

> [!div class="nextstepaction"]
> * [Создание VPN-подключений Azure S2S и управление ими с помощью модуля Azure PowerShell](vpn-gateway-tutorial-vpnconnection-powershell.md)
> * [Настройка подключения VPN-шлюза между виртуальными сетями на портале Azure](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
> * [Настройка подключения "точка — сеть" к виртуальной сети с использованием собственной аутентификации Azure на основе сертификата и портала Azure](vpn-gateway-howto-point-to-site-resource-manager-portal.md)

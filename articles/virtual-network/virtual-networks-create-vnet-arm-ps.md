---
title: "Создание виртуальной сети с помощью Azure PowerShell | Документация Майкрософт"
description: "Узнайте, как создать виртуальную сеть с помощью PowerShell."
services: virtual-network
documentationcenter: 
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: a31f4f12-54ee-4339-b968-1a8097ca77d3
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/03/2017
ms.author: jdial
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 63f2f6dde56c1b5c4b3ad2591700f43f6542874d
ms.openlocfilehash: d229d6add2c06952eaf662bdacc92a5163b1e945
ms.lasthandoff: 02/28/2017


---
# <a name="create-a-virtual-network-using-powershell"></a>Создание виртуальной сети с помощью PowerShell

[!INCLUDE [virtual-networks-create-vnet-intro](../../includes/virtual-networks-create-vnet-intro-include.md)]

Azure предоставляет две модели развертывания: с помощью Azure Resource Manager и классическую. Для создания ресурсов корпорация Майкрософт рекомендует использовать модель развертывания с помощью Resource Manager. Дополнительные сведения о различиях между двумя моделями см. в статье [Azure Resource Manager vs. classic deployment: Understand deployment models and the state of your resources](../azure-resource-manager/resource-manager-deployment-model.md) (Azure Resource Manager и классическое развертывание. Общие сведения о моделях развертывания и состоянии ресурсов).
 
В этой статье описывается создание виртуальной сети с помощью модели развертывания Resource Manager с использованием PowerShell. Виртуальную сеть также можно создать с помощью Resource Manager, используя другие инструменты, либо с помощью классической модели развертывания, выбрав другой вариант из следующего списка:

> [!div class="op_single_selector"]
- [Портал](virtual-networks-create-vnet-arm-pportal.md)
- [PowerShell](virtual-networks-create-vnet-arm-ps.md)
- [ИНТЕРФЕЙС КОМАНДНОЙ СТРОКИ](virtual-networks-create-vnet-arm-cli.md)
- [Шаблон](virtual-networks-create-vnet-arm-template-click.md)
- [Портал (классический)](virtual-networks-create-vnet-classic-pportal.md)
- [PowerShell (классическая модель)](virtual-networks-create-vnet-classic-netcfg-ps.md)
- [Интерфейс командной строки (классическая модель)](virtual-networks-create-vnet-classic-cli.md)

[!INCLUDE [virtual-networks-create-vnet-scenario-include](../../includes/virtual-networks-create-vnet-scenario-include.md)]

## <a name="create-a-virtual-network"></a>Создать виртуальную сеть

Чтобы создать виртуальную сеть с помощью PowerShell, сделайте следующее:

1. Установите и настройте Azure PowerShell, выполнив действия, описанные в [этой статье](/powershell/azureps-cmdlets-docs).

2. При необходимости создайте новую группу ресурсов, как показано ниже. В этом случае нужно создать группу ресурсов с именем *TestRG*. Дополнительные сведения о группах ресурсов см. в разделе "Группы ресурсов" [обзора Azure Resource Manager](../azure-resource-manager/resource-group-overview.md).

    ```powershell   
    New-AzureRmResourceGroup -Name TestRG -Location centralus
    ```

    Ожидаемые выходные данные:

        ResourceGroupName : TestRG
        Location          : centralus
        ProvisioningState : Succeeded
        Tags              :
        ResourceId        : /subscriptions/[Subscription Id]/resourceGroups/TestRG    
3. Создайте новую виртуальную сеть с именем *TestVNet*.

    ```powershell
    New-AzureRmVirtualNetwork -ResourceGroupName TestRG -Name TestVNet `
    -AddressPrefix 192.168.0.0/16 -Location centralus
    ```

    Ожидаемые выходные данные:

        Name                  : TestVNet
        ResourceGroupName     : TestRG
        Location              : centralus
        Id                    : /subscriptions/[Subscription Id]/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet
        Etag                   : W/"[Id]"
        ProvisioningState          : Succeeded
        Tags                       : 
        AddressSpace               : {
                                   "AddressPrefixes": [
                                     "192.168.0.0/16"
                                   ]
                                  }
        DhcpOptions                : {}
        Subnets                    : []
        VirtualNetworkPeerings     : []
4. Сохраните объект виртуальной сети в переменной.

    ```powershell
    $vnet = Get-AzureRmVirtualNetwork -ResourceGroupName TestRG -Name TestVNet
    ```

   > [!TIP]
   > Можно объединить шаги 3 и 4, выполнив команду `$vnet = New-AzureRmVirtualNetwork -ResourceGroupName TestRG -Name TestVNet -AddressPrefix 192.168.0.0/16 -Location centralus`.
   > 

5. Добавьте подсеть к переменной новой виртуальной сети.

    ```powershell
    Add-AzureRmVirtualNetworkSubnetConfig -Name FrontEnd `
    -VirtualNetwork $vnet -AddressPrefix 192.168.1.0/24
    ```

    Ожидаемые выходные данные:
   
        Name                  : TestVNet
        ResourceGroupName     : TestRG
        Location              : centralus
        Id                    : /subscriptions/[Subscription Id]/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet
        Etag                  : W/"[Id]"
        ProvisioningState     : Succeeded
        Tags                  :
        AddressSpace          : {
                                  "AddressPrefixes": [
                                    "192.168.0.0/16"
                                  ]
                                }
        DhcpOptions           : {}
        Subnets             : [
                                  {
                                    "Name": "FrontEnd",
                                    "AddressPrefix": "192.168.1.0/24"
                                  }
                                ]
        VirtualNetworkPeerings     : []

6. Повторите шаг 5 выше для каждой подсети, которую необходимо создать. Следующая команда создает подсеть *BackEnd* для этого сценария.

    ```powershell
    Add-AzureRmVirtualNetworkSubnetConfig -Name BackEnd `
    -VirtualNetwork $vnet -AddressPrefix 192.168.2.0/24
    ```

7. Несмотря на создание подсетей, в настоящее время они существуют только в локальной переменной, используемой для получения виртуальной сети, созданной на шаге 4. Чтобы сохранить изменения в Azure, выполните следующую команду:

    ```powershell
    Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
    ```
   
    Ожидаемые выходные данные:
   
        Name                  : TestVNet
        ResourceGroupName     : TestRG
        Location              : centralus
        Id                    : /subscriptions/[Subscription Id]/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet
        Etag                  : W/"[Id]"
        ProvisioningState     : Succeeded
        Tags                  :
        AddressSpace          : {
                                  "AddressPrefixes": [
                                    "192.168.0.0/16"
                                  ]
                                }
        DhcpOptions           : {
                                  "DnsServers": []
                                }
        Subnets               : [
                                  {
                                    "Name": "FrontEnd",
                                    "Etag": "W/\"[Id]\"",
                                    "Id": "/subscriptions/[Subscription Id]/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd",
                                    "AddressPrefix": "192.168.1.0/24",
                                    "IpConfigurations": [],
                                    "ProvisioningState": "Succeeded"
                                  },
                                  {
                                    "Name": "BackEnd",
                                    "Etag": "W/\"[Id]\"",
                                    "Id": "/subscriptions/[Subscription Id]/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/BackEnd",
                                    "AddressPrefix": "192.168.2.0/24",
                                    "IpConfigurations": [],
                                    "ProvisioningState": "Succeeded"
                                  }
                                ]
        VirtualNetworkPeerings : []

## <a name="next-steps"></a>Дальнейшие действия

Инструкции по подключению:

- Сведения о подключении виртуальной машины к виртуальной сети см. в статье о [создании виртуальной машины Windows](../virtual-machines/virtual-machines-windows-ps-create.md). Вместо создания виртуальной сети и подсети с помощью действий, описанных в этой статье, виртуальную машину можно подключить к имеющейся виртуальной сети и подсети.
- Сведения об установке подключения между виртуальными сетями см. в статье [Настройка подключения между виртуальными сетями на портале Azure](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md).
- Сведения о подключении виртуальной сети к локальной сети с использованием виртуальной частной сети типа "сеть — сеть" или канала ExpressRoute см. в [этой статье](../vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md) и в статье [Связывание виртуальной сети с каналом ExpressRoute](../expressroute/expressroute-howto-linkvnet-arm.md).


---
title: Контроль маршрутизации в виртуальной сети Azure с помощью PowerShell (классическая модель) | Документация Майкрософт
description: Сведения об управлении маршрутизацией в виртуальных сетях с помощью PowerShell | классическая модель
services: virtual-network
documentationcenter: na
author: genlin
manager: cshepard
editor: ''
tags: azure-service-management
ms.assetid: d8d07c16-cbe5-4536-acd6-870269346fe3
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/02/2016
ms.author: genli
ms.openlocfilehash: 930676a396ae316ec761ba5d03ad1a1d0fd7a425
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2018
ms.locfileid: "31792007"
---
# <a name="control-routing-and-use-virtual-appliances-classic-using-powershell"></a>Управление маршрутизацией и использование виртуальных модулей (классический режим) с помощью PowerShell

> [!div class="op_single_selector"]
> * [PowerShell](tutorial-create-route-table-powershell.md)
> * [интерфейс командной строки Azure](tutorial-create-route-table-cli.md)
> * [PowerShell (классическая модель)](virtual-network-create-udr-classic-ps.md)
> * [Интерфейс командной строки (классическая модель)](virtual-network-create-udr-classic-cli.md)

[!INCLUDE [virtual-network-create-udr-intro-include.md](../../includes/virtual-network-create-udr-intro-include.md)]

> [!IMPORTANT]
> Прежде чем приступить к работе с ресурсами Azure, обратите внимание на то, что в настоящее время в Azure существует две модели развертывания: классическая модель развертывания и модель развертывания с помощью Azure Resource Manager. Обязательно изучите [модели и инструменты развертывания](../azure-resource-manager/resource-manager-deployment-model.md) , прежде чем приступить к работе с какими бы то ни было ресурсами Azure. Для просмотра документации о различных средствах выберите соответствующий параметр в верхней части данной статьи. В этой статье рассматривается классическая модель развертывания.
> 

[!INCLUDE [virtual-network-create-udr-scenario-include.md](../../includes/virtual-network-create-udr-scenario-include.md)]

Для приведенных ниже примеров команд Azure PowerShell требуется уже созданная простая среда, основанная на приведенном выше сценарии. Для выполнения команд в том виде, в каком они представлены в данном документе, создайте среду, описанную в разделе о [создании виртуальной сети (классический режим) с помощью PowerShell](virtual-networks-create-vnet-classic-netcfg-ps.md).

[!INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## <a name="create-the-udr-for-the-front-end-subnet"></a>Создание определяемого пользователем маршрута для подсети переднего плана
Чтобы создать таблицу маршрутов и маршрут, необходимые для подсети переднего плана, на основании приведенного выше сценария, выполните следующие действия.

1. Чтобы создать таблицу маршрутов для интерфейсной подсети, выполните следующую команду:

    ```powershell
    New-AzureRouteTable -Name UDR-FrontEnd -Location uswest `
    -Label "Route table for front end subnet"
    ```

2. Чтобы создать маршрут в таблице маршрутов для отправки всего трафика, предназначенного для серверной подсети (192.168.2.0/24), в виртуальную машину **FW1** (192.168.0.4), выполните следующую команду:

    ```powershell
    Get-AzureRouteTable UDR-FrontEnd `
    |Set-AzureRoute -RouteName RouteToBackEnd -AddressPrefix 192.168.2.0/24 `
    -NextHopType VirtualAppliance `
    -NextHopIpAddress 192.168.0.4
    ```

3. Чтобы сопоставить таблицу маршрутов с подсетью **FrontEnd**, выполните следующую команду:

    ```powershell
    Set-AzureSubnetRouteTable -VirtualNetworkName TestVNet `
    -SubnetName FrontEnd `
    -RouteTableName UDR-FrontEnd
    ```

## <a name="create-the-udr-for-the-back-end-subnet"></a>Создание определяемого пользователем маршрута для серверной подсети
Чтобы создать таблицу маршрутов и маршрут, необходимые для серверной подсети, на основании сценария, выполните следующие действия:

1. Чтобы создать таблицу маршрутов для серверной подсети, выполните следующую команду:

    ```powershell
    New-AzureRouteTable -Name UDR-BackEnd `
    -Location uswest `
    -Label "Route table for back end subnet"
    ```

2. Чтобы создать маршрут в таблице маршрутов для отправки всего трафика, предназначенного для интерфейсной подсети (192.168.1.0/24), в виртуальную машину **FW1** (192.168.0.4), выполните следующую команду:

    ```powershell
    Get-AzureRouteTable UDR-BackEnd
    | Set-AzureRoute `
    -RouteName RouteToFrontEnd `
    -AddressPrefix 192.168.1.0/24 `
    -NextHopType VirtualAppliance `
    -NextHopIpAddress 192.168.0.4
    ```

3. Чтобы сопоставить таблицу маршрутов с подсетью **BackEnd**, выполните следующую команду:

    ```powershell
    Set-AzureSubnetRouteTable -VirtualNetworkName TestVNet `
    -SubnetName BackEnd `
    -RouteTableName UDR-BackEnd
    ```

## <a name="enable-ip-forwarding-on-the-fw1-vm"></a>Включение IP-пересылки на виртуальной машине FW1

Чтобы включить IP-пересылку на виртуальной машине FW1, выполните следующие действия:

1. Чтобы проверить состояние IP-пересылки, выполните следующую команду:

    ```powershell
    Get-AzureVM -Name FW1 -ServiceName TestRGFW `
    | Get-AzureIPForwarding
    ```

2. Чтобы включить IP-пересылку для виртуальной машины *FW1*, выполните следующую команду:

    ```powershell
    Get-AzureVM -Name FW1 -ServiceName TestRGFW `
    | Set-AzureIPForwarding -Enable
    ```

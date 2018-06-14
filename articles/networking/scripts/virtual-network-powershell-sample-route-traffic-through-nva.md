---
title: Пример скрипта Azure PowerShell. Маршрутизация трафика через виртуальный сетевой модуль | Документация Майкрософт
description: Пример скрипта Azure PowerShell для маршрутизации трафика через виртуальный сетевой модуль брандмауэра.
services: virtual-network
documentationcenter: virtual-network
author: georgewallace
manager: timlt
editor: tysonn
tags: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: powershell
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 05/16/2017
ms.author: gwallace
ms.openlocfilehash: 3b3aabed8c74dcb7561d7a40d09af9e3cf17ad8d
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/19/2018
ms.locfileid: "31599362"
---
# <a name="route-traffic-through-a-network-virtual-appliance"></a>Маршрутизация трафика через виртуальный сетевой модуль

В этом примере скрипта создается виртуальная сеть с интерфейсной и внутренней подсетями. Скрипт также создает виртуальную машину с IP-пересылкой, которая позволяет маршрутизировать трафик между двумя подсетями. После выполнения скрипта вы можете развернуть на виртуальной машине программное обеспечение сети, например брандмауэр.

При необходимости установите Azure PowerShell с помощью инструкции, приведенной в [руководстве Azure PowerShell](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/), а затем выполните команду `Connect-AzureRmAccount`, чтобы создать подключение к Azure.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Пример скрипта


[!code-powershell[main](../../../powershell_scripts/virtual-network/route-traffic-through-nva/route-traffic-through-nva.ps1 "Route traffic through a network virtual appliance")]

## <a name="clean-up-deployment"></a>Очистка развертывания 

Выполните следующую команду, чтобы удалить группу ресурсов, виртуальную машину и все связанные с ней ресурсы.

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup
```
## <a name="script-explanation"></a>Описание скрипта

Для создания группы ресурсов, виртуальной сети и групп безопасности сети этот скрипт использует следующие команды. Для каждой команды в таблице приведены ссылки на соответствующую документацию.

| Get-Help | Заметки |
|---|---|
| [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup)  | Создает группу ресурсов, в которой хранятся все ресурсы. |
| [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork) | Создает виртуальную сеть Azure и интерфейсную подсеть. |
| [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig) | Создает внутреннюю подсеть и подсеть DMZ. |
| [New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress) | Создает общедоступный IP-адрес для доступа к виртуальной машине из Интернета. |
| [New-AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface) | Создает виртуальный сетевой интерфейс и включает IP-пересылку для него. |
| [New-AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/new-azurermnetworksecuritygroup) | Создает группу безопасности сети (NSG). |
| [New-AzureRmNetworkSecurityRuleConfig](/powershell/module/azurerm.network/new-azurermnetworksecurityruleconfig) | Создает правила группы безопасности сети, которые разрешают входящий трафик по портам HTTP и HTTPS к виртуальной машине. |
| [Set-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/set-azurermvirtualnetworksubnetconfig)| Связывает группы безопасности сети и таблицы маршрутов с подсетями. |
| [New-AzureRmRouteTable](/powershell/module/azurerm.network/new-azurermroutetable)| Создает таблицу маршрутов для всех маршрутов. |
| [New-AzureRMRouteConfig](/powershell/module/azurerm.network/new-azurermrouteconfig)| Создает маршруты для маршрутизации трафика между подсетями и Интернетом через виртуальную машину. |
| [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) | Создает виртуальную машину и присоединяет к ней сетевой адаптер. Эта команда также указывает образ виртуальной машины и учетные данные администратора. |
| [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup)  | Удаляет группу ресурсов и все содержащиеся в ней ресурсы. |

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о Azure PowerShell см. в [документации по Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview).

Дополнительные примеры скриптов PowerShell для сетей см. в [обзорной документации по сетям Azure](../powershell-samples.md?toc=%2fazure%2fnetworking%2ftoc.json).

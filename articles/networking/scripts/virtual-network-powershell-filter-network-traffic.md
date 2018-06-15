---
title: Пример скрипта Azure PowerShell. Фильтрация сетевого трафика виртуальной машины | Документация Майкрософт
description: Пример скрипта Azure PowerShell для фильтрации входящего и исходящего сетевого трафика виртуальной машины.
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
ms.openlocfilehash: 0cac36b8dec530dc5b4fa00ccd6e775a39c2d80d
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/19/2018
ms.locfileid: "31597315"
---
# <a name="filter-inbound-and-outbound-vm-network-traffic"></a>Фильтрация входящего и исходящего сетевого трафика виртуальной машины

В этом примере скрипта создается виртуальная сеть с интерфейсной и внутренней подсетями. Входящий сетевой трафик в интерфейсной подсети ограничен портами HTTP и HTTPS, а исходящий интернет-трафик из внутренней подсети запрещен. После выполнения скрипта у вас будет одна виртуальная машина с двумя сетевыми адаптерами. Каждый сетевой адаптер подключен к другой подсети.

При необходимости установите Azure PowerShell с помощью инструкции, приведенной в [руководстве Azure PowerShell](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/), а затем выполните команду `Connect-AzureRmAccount`, чтобы создать подключение к Azure.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Пример скрипта


[!code-powershell[main](../../../powershell_scripts/virtual-network/filter-network-traffic/filter-network-traffic.ps1  "Filter VM network traffic")]

## <a name="clean-up-deployment"></a>Очистка развертывания 

Выполните следующую команду, чтобы удалить группу ресурсов, виртуальную машину и все связанные с ней ресурсы.

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="script-explanation"></a>Описание скрипта

Для создания группы ресурсов, виртуальной сети и групп безопасности сети этот скрипт использует следующие команды. Для каждой команды в таблице приведены ссылки на соответствующую документацию.

| Get-Help | Заметки |
|---|---|
| [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) | Создает группу ресурсов, в которой хранятся все ресурсы. |
| [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig) | Создает объект конфигурации подсети. |
| [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork) | Создает виртуальную сеть Azure и интерфейсную подсеть. |
| [New-AzureRmNetworkSecurityRuleConfig](/powershell/module/azurerm.network/new-azurermnetworksecurityruleconfig) | Создает правила безопасности, которые будут добавлены в группу безопасности сети. |
| [New-AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/new-azurermnetworksecuritygroup) |Создает правила групп безопасности сети, которые разрешают или блокируют определенные порты для конкретных подсетей. |
| [Set-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/set-azurermvirtualnetworksubnetconfig) | Связывает группы безопасности сети с подсетями. |
| [New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress) | Создает общедоступный IP-адрес для доступа к виртуальной машине из Интернета. |
| [New-AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface) | Создает виртуальные сетевые интерфейсы и присоединяет их к интерфейсной и внутренней подсети виртуальной сети. |
| [New-AzureRmVMConfig](/powershell/module/azurerm.compute/new-azurermvmconfig) | Создает конфигурацию виртуальной машины. Эта конфигурация включает в себя такие сведения, как имя виртуальной машины, операционную систему и учетные данные администратора. Данная конфигурации используется при создании виртуальной машины. |
| [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) | Создайте виртуальную машину. |
|[Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) | Удаляет группу ресурсов и все ресурсы, содержащиеся в ней. |

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о Azure PowerShell см. в [документации по Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview).

Дополнительные примеры скриптов PowerShell для сетей см. в [обзорной документации по сетям Azure](../powershell-samples.md?toc=%2fazure%2fnetworking%2ftoc.json).

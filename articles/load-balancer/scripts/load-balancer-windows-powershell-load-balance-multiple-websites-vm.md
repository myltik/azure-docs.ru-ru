---
title: Пример PowerShell. Распределение нагрузки на нескольких веб-сайтах с помощью Azure PowerShell | Документация Майкрософт
description: В этом примере скрипта Azure PowerShell показано, как распределить нагрузку между несколькими веб-сайтами на одной виртуальной машине
services: load-balancer
documentationcenter: load-balancer
author: KumudD
manager: jeconnoc
editor: tysonn
tags: ''
ms.assetid: ''
ms.service: load-balancer
ms.devlang: powershell
ms.topic: sample
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 04/20/2018
ms.author: kumud
ms.openlocfilehash: f0058c8c4c4b170b12d2e888073aa0c731e87725
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2018
ms.locfileid: "32181241"
---
# <a name="azure-powershell-script-example-load-balance-multiple-websites"></a>Пример скрипта Azure PowerShell. Распределение нагрузки на нескольких веб-сайтах

В этом примере скрипта Azure PowerShell создается виртуальная сеть с двумя виртуальными машинами, которые входят в группу доступности. Подсистема балансировки нагрузки направляет трафик с двух отдельных IP-адресов на две виртуальные машины. После выполнения сценария можно развернуть ПО веб-сервера на виртуальных машин и разместить несколько веб-сайтов, каждый из которых имеет собственный IP-адрес.

При необходимости установите Azure PowerShell с помощью инструкции, приведенной в [руководстве Azure PowerShell](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/), а затем выполните команду `Login-AzureRmAccount`, чтобы создать подключение к Azure.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Пример скрипта

[!code-powershell[main](../../../powershell_scripts/load-balancer/load-balance-multiple-web-sites-vm/load-balance-multiple-web-sites-vm.ps1  "Load balance multiple web sites")]

## <a name="clean-up-deployment"></a>Очистка развертывания 

Выполните следующую команду, чтобы удалить группу ресурсов, виртуальную машину и все связанные с ней ресурсы.

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="script-explanation"></a>Описание скрипта

Для создания группы ресурсов, виртуальной сети, подсистемы балансировки нагрузки и всех связанных ресурсов этот сценарий использует приведенные ниже команды. Для каждой команды в таблице приведены ссылки на соответствующую документацию.

| Get-Help | Заметки |
|---|---|
| [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) | Создает группу ресурсов, в которой хранятся все ресурсы. |
| [New-AzureRmAvailabilitySet](/powershell/module/azurerm.compute/new-azurermavailabilityset) | Создает группу доступности Azure для обеспечения высокой доступности. |
| [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig) | Создает конфигурацию подсети. Эта конфигурация используется в процессе создания виртуальной сети. |
| [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork) | Создает виртуальную сеть. |
| [New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress) | Создает общедоступный IP-адрес. |
| [New-AzureRmLoadBalancerFrontendIpConfig](/powershell/module/azurerm.network/new-azurermloadbalancerfrontendipconfig) | Создает конфигурацию внешних IP-адресов для подсистемы балансировки нагрузки. |
| [New-AzureRmLoadBalancerBackendAddressPoolConfig](/powershell/module/azurerm.network/new-azurermloadbalancerbackendaddresspoolconfig) | Создает конфигурацию внутреннего пула адресов для подсистемы балансировки нагрузки. |
| [New-AzureRmLoadBalancerProbeConfig](/powershell/module/azurerm.network/new-azurermloadbalancerprobeconfig) | Создает пробу балансировщика сетевой нагрузки. Она используется для отслеживания каждой виртуальной машины в наборе балансировщика сетевой нагрузки. Если любая виртуальная машина становится недоступной, к ней не направляется трафик. |
| [New-AzureRmLoadBalancerRuleConfig](/powershell/module/azurerm.network/new-azurermloadbalancerruleconfig) | Создает правило балансировщика сетевой нагрузки. В этом примере создается правило для порта 80. Так как трафик HTTP поступает в балансировщик сетевой нагрузки, он перенаправляется на порт 80 одной из виртуальных машин в наборе балансировщика сетевой нагрузки. |
| [New-AzureRmLoadBalancer](/powershell/module/azurerm.network/new-azurermloadbalancer) | Создает подсистему балансировки нагрузки. |
| [New-AzureRmNetworkInterfaceIpConfig](/powershell/module/azurerm.network/new-azurermnetworkinterfaceipconfig) | Определяет расширенные функции для интерфейса виртуальной сети. |
| [New-AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface) | Создает сетевой интерфейс. |
| [New-AzureRmVMConfig](/powershell/module/azurerm.compute/new-azurermvmconfig) | Создает конфигурацию виртуальной машины. Эта конфигурация включает в себя такие сведения, как имя виртуальной машины, операционную систему и учетные данные администратора. Данная конфигурации используется при создании виртуальной машины. |
| [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) | Создайте виртуальную машину. |
|[Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) | Удаляет группу ресурсов и все ресурсы, содержащиеся в ней. |

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о Azure PowerShell см. в [документации по Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview).

Дополнительные примеры скриптов PowerShell для сетей см. в [обзорной документации по сетям Azure](../powershell-samples.md?toc=%2fazure%2fnetworking%2ftoc.json).

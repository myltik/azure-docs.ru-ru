---
title: Примеры Azure PowerShell — масштабируемый набор, избыточный в пределах зоны | Документация Майкрософт
description: Примеры сценариев Azure PowerShell.
services: virtual-machine-scale-sets
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/27/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: b121684b3b9a5d03fe89e0892179140e9f5de80f
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2018
---
# <a name="create-a-zone-redundant-virtual-machine-scale-set-with-powershell"></a>Создание масштабируемого набора виртуальных машин, избыточного в пределах зоны, с помощью PowerShell
При помощи этого скрипта создается масштабируемый набор виртуальных машин под управлением Windows Server 2016 в нескольких зонах доступности. После выполнения сценария можно получить доступ к виртуальной машине по протоколу RDP.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Пример скрипта
[!code-powershell[main](../../../powershell_scripts/virtual-machine-scale-sets/create-zone-redundant-scale-set/create-zone-redundant-scale-set.ps1 "Create zone-redundant scale set")]

## <a name="clean-up-deployment"></a>Очистка развертывания
Выполните следующую команду, чтобы удалить группу ресурсов, масштабируемый набор и все связанные с ними ресурсы.

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="script-explanation"></a>Описание скрипта
Чтобы создать развертывание, скрипт использует следующие команды. Для каждого элемента в таблице приведены ссылки на документацию по команде.

| Get-Help | Заметки |
|---|---|
| [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) | Создает группу ресурсов, в которой хранятся все ресурсы. |
| [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig) | Создает объект конфигурации, который определяет подсеть виртуальной сети. |
| [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork) | Создает виртуальную сеть и подсеть. |
| [New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress) | Создает статический общедоступный IP-адрес. |
| [New-AzureRmLoadBalancerFrontendIpConfig](/powershell/module/azurerm.network/new-azurermloadbalancerfrontendipconfig) | Создает объект конфигурации, который определяет внешний интерфейс подсистемы балансировки нагрузки, в том числе общедоступный IP-адрес. |
| [New-AzureRmLoadBalancerBackendAddressPoolConfig](/powershell/module/azurerm.network/new-azurermloadbalancerbackendaddresspoolconfig) | Создает объект конфигурации, который определяет серверную часть подсистемы балансировки нагрузки. |
| [New-AzureRmLoadBalancer](/powershell/module/azurerm.network/new-azurermloadbalancer) | Создает подсистему балансировки нагрузки на основе объектов конфигурации внешней и серверной частей. |
| [Add-AzureRmLoadBalancerProbeConfig](/powershell/module/azurerm.network/new-azurermloadbalancerprobeconfig) | Создает зонд работоспособности подсистемы балансировки нагрузки, чтобы отслеживать трафик через TCP-порт 80. Если обнаружены два последовательных сбоя с интервалом 15 секунд, конечная точка считается нерабочей. |
| [Add-AzureRmLoadBalancerRuleConfig](/powershell/module/azurerm.network/new-azurermloadbalancerruleconfig) | Создает объект конфигурации, который определяет правила подсистемы балансировки нагрузки для распределения трафика через TCP-порт 80 в пулах внешней и серверной частей. |
| [Set-AzureRmLoadBalancer](/powershell/module/azurerm.network/set-azurermloadbalancer) | Обновляет подсистему балансировки нагрузки с помощью зонда работоспособности и правил балансировки нагрузки. |
| [New-AzureRmNetworkSecurityRuleConfig](/powershell/module/azurerm.network/new-azurermnetworksecurityruleconfig) | Создает объект конфигурации, который определяет правило группы безопасности сети, разрешающее трафик через TCP-порт 80. |
| [New-AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/new-azurermnetworksecuritygroup) | Создает группу и правило безопасности сети. |
| [Set-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/set-azurermvirtualnetworksubnetconfig) | Обновляет подсеть виртуальной сети, связанную с группой безопасности сети. Все экземпляры виртуальной машины, подключенные к подсети, наследуют правила группы безопасности сети. |
| [Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/set-azurermvirtualnetwork) | Обновляет виртуальную сеть с помощью подсети и конфигурации группы безопасности сети. |
| [New-AzureRmVmssIpConfig](/powershell/module/azurerm.compute/new-azurermvmssipconfig) | Создает объект конфигурации, который определяет информацию об IP-адресе масштабируемого набора виртуальных машин для подключения экземпляров виртуальных машин к внутреннему пулу подсистемы балансировки нагрузки и пулу входящих подключений NAT.
| [New-AzureRmVmssConfig](/powershell/module/azurerm.compute/new-azurermvmssconfig) | Создает объект конфигурации, который определяет количество экземпляров виртуальных машин в масштабируемом наборе, размер экземпляра виртуальной машины и режим обновления политики. |
| [Set-AzureRmVmssStorageProfile](/powershell/module/azurerm.compute/set-azurermvmssstorageprofile) | Создает объект конфигурации, определяющий образ виртуальной машины, который будут использовать экземпляры виртуальных машин. |
| [Set-AzureRmVmssOsProfile](/powershell/module/azurerm.compute/set-azurermvmssosprofile) | Создает профиль конфигурации, который определяет учетные данные пользователя и имя экземпляра виртуальной машины. |
| [Add-AzureRmVmssNetworkInterfaceConfiguration](/powershell/module/azurerm.compute/add-azurermvmssnetworkinterfaceconfiguration) | Создает объект конфигурации, который определяет сетевую карту виртуальной сети для каждого экземпляра виртуальной машины. |
| [New-AzureRmVmss](/powershell/module/azurerm.compute/new-azurermvmss) | Объединяет все объекты конфигурации для создания масштабируемого набора виртуальных машин. |
| [Get-AzureRmVmss](/powershell/module/azurerm.compute/get-azurermvmss) | Получает информацию о масштабируемом наборе виртуальных машин. |
| [Add-AzureRmVmssExtension](/powershell/module/azurerm.compute/add-azurermvmssextension) | Добавляет расширение виртуальной машины для настраиваемого скрипта установки базового веб-приложения. |
| [Update-AzureRmVmss](/powershell/module/azurerm.compute/update-azurermvmss) | Обновляет модель масштабируемого набора виртуальных машин для применения расширения виртуальной машины. |
| [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) | Позволяет получить сведения об общедоступном IP-адресе, который использует подсистема балансировки нагрузки. |
|[Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) | Удаляет группу ресурсов и все ресурсы, содержащиеся в ней. |


## <a name="next-steps"></a>Дополнительная информация
Дополнительные сведения о модуле Azure PowerShell см. в [документации по Azure PowerShell](/powershell/azure/overview).

См. дополнительные примеры сценариев PowerShell для [масштабируемого набора виртуальных машин Azure](../powershell-samples.md).
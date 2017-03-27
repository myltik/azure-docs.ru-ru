---
title: "Пример сценария Azure PowerShell для OMS | Документация Майкрософт"
description: "Пример сценария Azure PowerShell для OMS."
services: virtual-machines-linux
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/01/2017
ms.author: nepeters
translationtype: Human Translation
ms.sourcegitcommit: 97acd09d223e59fbf4109bc8a20a25a2ed8ea366
ms.openlocfilehash: 91b5b68152627bd4c31b87c317a4f7b1d97c9e76
ms.lasthandoff: 03/10/2017

---

# <a name="create-an-operations-management-suite-monitored-vm-with-powershell"></a>Создание виртуальной машины, отслеживаемой Operations Management Suite, с помощью PowerShell

Этот скрипт создает виртуальную машину Azure, устанавливает агент Operations Management Suite и регистрирует систему в рабочей области OMS. После выполнения скрипта виртуальная машина отобразится в консоли OMS.

Перед выполнением этого скрипта убедитесь, что установлено подключение к Azure, выполнив команду `Login-AzureRmAccount`. Кроме того, открытый ключ SSH `id_rsa.pub` должен храниться в каталоге .ssh вашего профиля пользователя. В скрипте требуется обновить идентификатор и ключ рабочей области OMS.

## <a name="sample-script"></a>Пример скрипта

[!code-powershell[main](../../../powershell_scripts/virtual-machine/create-vm-monitor-oms/create-vm-monitor-oms.ps1 "Создание виртуальной машины OMS")]

## <a name="clean-up-deployment"></a>Очистка развертывания 

После выполнения примера скрипта можно удалить группу ресурсов, виртуальную машину и все связанные с ней ресурсы, использовав следующую команду.

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="script-explanation"></a>Описание скрипта

Чтобы создать развертывание, скрипт использует следующие команды. Для каждого элемента в таблице приведены ссылки на документацию по команде.

| Команда | Примечания |
|---|---|
| [New-AzureRmResourceGroup](https://docs.microsoft.com/powershell/resourcemanager/azurerm.resources/v3.2.0/new-azurermresourcegroup) | Создает группу ресурсов, в которой хранятся все ресурсы. |
| [New-AzureRmVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/resourcemanager/azurerm.network/v2.1.0/new-azurermvirtualnetworksubnetconfig) | Создает конфигурацию подсети. Эта конфигурация используется в процессе создания виртуальной сети. |
| [New-AzureRmVirtualNetwork](https://docs.microsoft.com/powershell/resourcemanager/azurerm.network/v1.0.13/new-azurermvirtualnetwork) | Создает виртуальную сеть. |
| [New-AzureRmPublicIpAddress](https://docs.microsoft.com/powershell/resourcemanager/azurerm.network/v2.1.0/new-azurermpublicipaddress) | Создает общедоступный IP-адрес. |
| [New-AzureRmNetworkSecurityRuleConfig](https://docs.microsoft.com/powershell/resourcemanager/azurerm.network/v2.1.0/new-azurermnetworksecurityruleconfig) | Создает конфигурацию правил группы безопасности сети. Эта конфигурация используется для создания правила NSG при создании этой NSG. |
| [New-AzureRmNetworkSecurityGroup](https://docs.microsoft.com/powershell/resourcemanager/azurerm.network/v3.1.0/new-azurermnetworksecuritygroup) | Создает группу безопасности сети. |
| [Get-AzureRmVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/resourcemanager/azurerm.network/v1.0.13/get-azurermvirtualnetworksubnetconfig) | Возвращает сведения о подсети. Эта информация используется при создании сетевого интерфейса. |
| [New-AzureRmNetworkInterface](https://docs.microsoft.com/powershell/resourcemanager/azurerm.network/v3.4.0/new-azurermnetworkinterface) | Создает сетевой интерфейс. |
| [New-AzureRmVMConfig](https://docs.microsoft.com/powershell/resourcemanager/azurerm.compute/v1.3.4/new-azurermvmconfig) | Создает конфигурацию виртуальной машины. Эта конфигурация включает в себя такие сведения, как имя виртуальной машины, операционную систему и учетные данные администратора. Данная конфигурации используется при создании виртуальной машины. |
| [New-AzureRmVM](https://docs.microsoft.com/powershell/resourcemanager/azurerm.compute/v1.3.4/new-azurermvm) | Создайте виртуальную машину. |
| [Set-AzureRmVMExtension](https://docs.microsoft.com/powershell/resourcemanager/azurerm.compute/v2.2.0/set-azurermvmextension) | Добавляет в виртуальную машину расширение виртуальной машины. В этом случае расширение агента Operations Management Suite используется для установки агента OMS и регистрации виртуальной машины в рабочей области OMS. |
|[Remove-AzureRmResourceGroup](https://docs.microsoft.com/powershell/resourcemanager/azurerm.resources/v3.5.0/remove-azurermresourcegroup) | Удаляет группу ресурсов и все ресурсы, содержащиеся в ней. |

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о модуле Azure PowerShell см. в [документации по Azure PowerShell](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/).

Дополнительные примеры сценариев PowerShell для виртуальных машин представлены в [документации по виртуальным машинам Azure под управлением Linux](../virtual-machines-linux-powershell-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).


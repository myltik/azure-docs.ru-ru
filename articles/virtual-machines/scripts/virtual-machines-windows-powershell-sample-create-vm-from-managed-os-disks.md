---
title: Пример скрипта Azure PowerShell. Создание виртуальной машины путем подключения управляемого диска в качестве диска ОС | Документация Майкрософт
description: Пример скрипта Azure PowerShell. Создание виртуальной машины путем подключения управляемого диска в качестве диска ОС
services: virtual-machines-windows
documentationcenter: virtual-machines
author: ramankum
manager: kavithag
editor: ramankum
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 05/10/2017
ms.author: ramankum
ms.custom: mvc
ms.openlocfilehash: ec532811e94647c8a04b9faf9474f6749969f83e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
ms.locfileid: "23116672"
---
# <a name="create-a-virtual-machine-using-an-existing-managed-os-disk-with-powershell"></a>Создание виртуальной машины с помощью существующего управляемого диска ОС с использованием PowerShell

Этот скрипт создает виртуальную машину путем подключения существующего управляемого диска как диска ОС. Используйте этот скрипт в таких сценариях:
* создание виртуальной машины из существующего управляемого диска ОС, скопированного из управляемого диска в другой подписке;
* создание виртуальной машины из существующего управляемого диска, который был создан из специального файла VHD; 
* создание виртуальной машины из существующего управляемого диска ОС, который был создан из моментального снимка. 

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Пример скрипта

[!code-powershell[main](../../../powershell_scripts/virtual-machine/create-vm-from-snapshot/create-vm-from-snapshot.ps1 "Create VM from snapshot")]

## <a name="clean-up-deployment"></a>Очистка развертывания 

Выполните следующую команду, чтобы удалить группу ресурсов, виртуальную машину и все связанные с ней ресурсы.

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="script-explanation"></a>Описание скрипта

Этот скрипт использует следующие команды для получения свойств управляемого диска, его подключения к новой виртуальной машине и создания виртуальной машины. Для каждого элемента в таблице приведены ссылки на документацию по команде.

| Get-Help | Заметки |
|---|---|
| [Get-AzureRmDisk](/powershell/module/azurerm.compute/Get-AzureRmDisk) | Возвращает объект диска на основе имени и группы ресурсов диска. Свойство идентификатора возвращенного объекта диска используется для подключения диска к новой виртуальной машине. |
| [New-AzureRmVMConfig](/powershell/module/azurerm.compute/new-azurermvmconfig) | Создает конфигурацию виртуальной машины. Эта конфигурация включает в себя такие сведения, как имя виртуальной машины, операционную систему и учетные данные администратора. Данная конфигурации используется при создании виртуальной машины. |
| [Set-AzureRmVMOSDisk](/powershell/module/azurerm.compute/set-azurermvmosdisk) | Подключает управляемый диск, используя свойство идентификатора диска как диск ОС для новой виртуальной машины. |
| [New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress) | Создает общедоступный IP-адрес. |
| [New-AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface) | Создает сетевой интерфейс. |
| [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) | Создайте виртуальную машину. |
|[Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) | Удаляет группу ресурсов и все ресурсы, содержащиеся в ней. |

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о модуле Azure PowerShell см. в [документации по Azure PowerShell](/powershell/azure/overview).

Дополнительные примеры сценариев PowerShell для виртуальных машин представлены в [документации по виртуальным машинам Azure под управлением Windows](../windows/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
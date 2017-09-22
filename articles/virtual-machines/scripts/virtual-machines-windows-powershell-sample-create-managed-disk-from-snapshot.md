---
title: "Пример сценария Azure PowerShell. Создание управляемого диска из моментального снимка | Документация Майкрософт"
description: "Пример сценария Azure PowerShell для создания управляемого диска из моментального снимка."
services: virtual-machines-windows
documentationcenter: storage
author: ramankumarlive
manager: kavithag
editor: tysonn
tags: azure-service-management
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 06/05/2017
ms.author: ramankum
ms.translationtype: HT
ms.sourcegitcommit: 8f9234fe1f33625685b66e1d0e0024469f54f95c
ms.openlocfilehash: 0ead3d80ee509bb8043824f29a9954bd57e095d1
ms.contentlocale: ru-ru
ms.lasthandoff: 09/20/2017

---

# <a name="create-a-managed-disk-from-a-snapshot-with-powershell"></a>Создание управляемого диска из моментального снимка с помощью PowerShell

Этот сценарий создает управляемый диск на основе моментального снимка. Его можно использовать для восстановления виртуальной машины из моментальных снимков ОС и дисков данных. Можно создать управляемые диски данных и ОС на основе соответствующих моментальных снимков, а затем создать виртуальную машину, подключив эти управляемые диски. Кроме того, вы можете восстановить диски данных существующей виртуальной машины, подключив диски данных, созданные из моментальных снимков.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Пример скрипта

[!code-powershell[main](../../../powershell_scripts/virtual-machine/create-managed-disk-from-snapshot/create-managed-disk-from-snapshot.ps1 "Create managed disk from snapshot")]


## <a name="script-explanation"></a>Описание скрипта

Этот сценарий использует приведенные ниже команды для создания управляемого диска на основе моментального снимка. Для каждой команды в таблице приведены ссылки на соответствующую документацию.

| Команда | Примечания |
|---|---|
| [Get-AzureRmSnapshot](/powershell/module/azurerm.compute/Get-AzureRmSnapshot) | Возвращает свойства моментального снимка.  |
| [New-AzureRmDiskConfig](/powershell/module/azurerm.compute/New-AzureRmDiskConfig) | Создает конфигурацию диска, которая используется для создания диска. Она содержит идентификатор ресурса родительского моментального снимка, расположение, которое совпадает с расположением родительского моментального снимка, и тип хранилища.  |
| [New-AzureRmDisk](/powershell/module/azurerm.compute/New-AzureRmDisk) | Создает диск с помощью конфигурации диска, имени диска и имени группы ресурсов, которые передаются в качестве параметров. |


## <a name="next-steps"></a>Дальнейшие действия

[Создание виртуальной машины на основе управляемого диска](./virtual-machines-windows-powershell-sample-create-vm-from-managed-os-disks.md?toc=%2fpowershell%2fmodule%2ftoc.json)

Дополнительные сведения о модуле Azure PowerShell см. в [документации по Azure PowerShell](/powershell/azure/overview).

Дополнительные примеры сценариев PowerShell для виртуальных машин представлены в [документации по виртуальным машинам Azure под управлением Windows](../windows/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

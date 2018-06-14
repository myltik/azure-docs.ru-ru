---
title: Пример сценария Azure PowerShell. Копирование (перемещение) управляемых дисков в ту же или другую подписку | Документация Майкрософт
description: Пример сценария Azure PowerShell для копирования (перемещения) управляемых дисков в ту же или другую подписку.
services: virtual-machines-windows
documentationcenter: storage
author: ramankumarlive
manager: kavithag
editor: tysonn
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 06/06/2017
ms.author: ramankum
ms.openlocfilehash: a14b25236fc233ef7b98b29e62a1270c5e4d8f53
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
ms.locfileid: "23116462"
---
# <a name="copy-managed-disks-in-the-same-subscription-or-different-subscription-with-powershell"></a>Копирование управляемых дисков в ту же или другую подписку с помощью PowerShell

Этот сценарий создает копию управляемого диска в той же или в другой подписке. Новый диск создается в том же регионе, что и родительский управляемый диск.   

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Пример скрипта

[!code-powershell[main](../../../powershell_scripts/virtual-machine/copy-managed-disks-to-same-or-different-subscription/copy-managed-disks-to-same-or-different-subscription.ps1 "Copy managed disk")]


## <a name="script-explanation"></a>Описание скрипта

Этот сценарий использует приведенные ниже команды для создания управляемого диска в целевой подписке с помощью идентификатора исходного управляемого диска. Для каждой команды в таблице приведены ссылки на соответствующую документацию.

| Get-Help | Заметки |
|---|---|
| [New-AzureRmDiskConfig](/powershell/module/azurerm.compute/New-AzureRmDiskConfig) | Создает конфигурацию диска, которая используется для создания диска. Она содержит идентификатор ресурса родительского диска и расположение, которое совпадает с расположением родительского диска.  |
| [New-AzureRmDisk](/powershell/module/azurerm.compute/New-AzureRmDisk) | Создает диск с помощью конфигурации диска, имени диска и имени группы ресурсов, которые передаются в качестве параметров. |


## <a name="next-steps"></a>Дополнительная информация

[Создание виртуальной машины на основе управляемого диска](./virtual-machines-windows-powershell-sample-create-vm-from-managed-os-disks.md?toc=%2fpowershell%2fmodule%2ftoc.json)

Дополнительные сведения о модуле Azure PowerShell см. в [документации по Azure PowerShell](/powershell/azure/overview).

Дополнительные примеры сценариев PowerShell для виртуальных машин представлены в [документации по виртуальным машинам Azure под управлением Windows](../windows/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
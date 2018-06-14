---
title: Пример сценария Azure PowerShell. Копирование (перемещение) моментального снимка управляемого диска в ту же или другую подписку | Документация Майкрософт
description: Пример сценария Azure PowerShell для копирования (перемещения) моментального снимка управляемого диска в ту же или другую подписку.
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
ms.openlocfilehash: 064b5355da10fe683563fa078cfafc65080f7ea2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
ms.locfileid: "23116502"
---
# <a name="copy-snapshot-of-a-managed-disk-in-same-subscription-or-different-subscription-with-powershell"></a>Копирование моментального снимка управляемого диска в ту же или другую подписку с помощью PowerShell

Этот сценарий создает копию моментального снимка в той же или в другой подписке. Используйте этот сценарий, чтобы переместить моментальный снимок в другую подписку для хранения данных. Хранение моментальных снимков в другой подписке защитит вас от случайного удаления моментальных снимков в основной подписке. 

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Пример скрипта

[!code-powershell[main](../../../powershell_scripts/virtual-machine/copy-snapshot-to-same-or-different-subscription/copy-snapshot-to-same-or-different-subscription.ps1 "Copy snapshot")]


## <a name="script-explanation"></a>Описание скрипта

Этот сценарий использует приведенные ниже команды для создания моментального снимка в целевой подписке с помощью идентификатора исходного моментального снимка. Для каждой команды в таблице приведены ссылки на соответствующую документацию.

| Get-Help | Заметки |
|---|---|
| [New-AzureRmSnapshotConfig](/powershell/module/azurerm.compute/New-AzureRmSnapshotConfig) | Создает конфигурацию моментального снимка, используемую для создания моментального снимка. Она содержит идентификатор ресурса родительского моментального снимка и расположение, которое совпадает с расположением родительского моментального снимка.  |
| [New-AzureRmSnapshot](/powershell/module/azurerm.compute/New-AzureRmDisk) | Создает моментальный снимок с помощью конфигурации моментального снимка, имени моментального снимка и имени группы ресурсов, которые передаются в качестве параметров. |


## <a name="next-steps"></a>Дополнительная информация

[Создание виртуальной машины на основе моментального снимка](./virtual-machines-windows-powershell-sample-create-vm-from-snapshot.md?toc=%2fpowershell%2fmodule%2ftoc.json)

Дополнительные сведения о модуле Azure PowerShell см. в [документации по Azure PowerShell](/powershell/azure/overview).

Дополнительные примеры сценариев PowerShell для виртуальных машин представлены в [документации по виртуальным машинам Azure под управлением Windows](../windows/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
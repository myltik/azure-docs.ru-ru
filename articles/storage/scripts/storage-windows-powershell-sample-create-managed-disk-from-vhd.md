---
title: "Пример сценария Azure PowerShell. Создание управляемого диска из VHD-файла в учетной записи хранения в той же или другой подписке | Документация Майкрософт"
description: "Пример сценария Azure PowerShell для создания управляемого диска из VHD-файла в учетной записи хранения в той же или другой подписке."
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
ms.translationtype: Human Translation
ms.sourcegitcommit: 09f24fa2b55d298cfbbf3de71334de579fbf2ecd
ms.openlocfilehash: 079ab69da3c2cb6cc38f0a766ffeb47ed84c90a3
ms.contentlocale: ru-ru
ms.lasthandoff: 06/07/2017

---

# <a name="create-a-managed-disk-from-a-vhd-file-in-a-storage-account-in-same-or-different-subscription-with-powershell"></a>Создание управляемого диска из VHD-файла в учетной записи хранения в той же или другой подписке с помощью PowerShell

Этот сценарий создает управляемый диск на основе VHD-файла в учетной записи хранения в той же или другой подписке. Этот сценарий можно использовать, чтобы импортировать специализированный (не универсальный и не подготовленный командой Sysprep) VHD в управляемый диск ОС для создания виртуальной машины. Кроме того, с его помощью можно импортировать VHD данных в управляемый диск данных. 

Не создавайте несколько идентичных управляемых дисков из VHD-файла за небольшой промежуток времени. При создании управляемых дисков из VHD-файла создается моментальный снимок большого двоичного объекта VHD-файла, который затем используется для этой операции. За минуту может быть создан только один большой двоичный объект, что приводит к сбоям при создании дисков из-за регулирования. Во избежание этого регулирования создайте [управляемый моментальный снимок из VHD-файла](./../scripts/storage-windows-powershell-sample-create-snapshot-from-vhd.md?toc=%2fpowershell%2fmodule%2ftoc.json), а затем используйте его для создания нескольких управляемых дисков за короткий промежуток времени. 

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Пример скрипта

[!code-powershell[main](../../../powershell_scripts/storage/create-managed-disks-from-vhd-in-different-subscription/create-managed-disks-from-vhd-in-different-subscription.ps1 "Создание управляемого диска на основе VHD")]


## <a name="script-explanation"></a>Описание скрипта

Этот сценарий использует приведенные ниже команды для создания управляемого диска на основе VHD. Для каждой команды в таблице приведены ссылки на соответствующую документацию.

| Команда | Примечания |
|---|---|
| [New-AzureRmDiskConfig](/powershell/module/azurerm.compute/New-AzureRmDiskConfig) | Создает конфигурацию диска, которая используется для создания диска. Она содержит тип хранилища, расположение, идентификатор ресурса учетной записи хранения, в которой хранится родительский VHD, и универсальный код ресурса (URI) родительского VHD. |
| [New-AzureRmDisk](/powershell/module/azurerm.compute/New-AzureRmDisk) | Создает диск с помощью конфигурации диска, имени диска и имени группы ресурсов, которые передаются в качестве параметров. |

## <a name="next-steps"></a>Дальнейшие действия

[Создание виртуальной машины путем подключения управляемого диска как диска ОС](./../../virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm-from-managed-os-disks.md?toc=%2fpowershell%2fmodule%2ftoc.json)

Дополнительные сведения о модуле Azure PowerShell см. в [документации по Azure PowerShell](/powershell/azure/overview).

Дополнительные примеры сценариев PowerShell для виртуальных машин представлены в [документации по виртуальным машинам Azure под управлением Windows](../../virtual-machines/windows/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

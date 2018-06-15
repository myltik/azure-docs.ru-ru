---
title: Примеры PowerShell для виртуальной машины Azure | Документация Майкрософт
description: Примеры PowerShell для виртуальной машины Azure.
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 11/30/2017
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 27f926a9a5d30d28e2721ffa52695292f794a501
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/18/2018
ms.locfileid: "31522437"
---
# <a name="azure-virtual-machine-powershell-samples"></a>Примеры PowerShell для виртуальной машины Azure

Ниже приведена таблица с ссылками на примеры сценариев PowerShell, которые позволяют создавать виртуальные машины Windows и управлять ими.

| | |
|---|---|
|**Создание виртуальных машин**||
| [Быстрое создание виртуальной машины](./../scripts/virtual-machines-windows-powershell-sample-create-vm-quick.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Создает группу ресурсов, виртуальную машину и все связанные ресурсы почти без участия пользователя.|
| [Создание полностью настроенной виртуальной машины](./../scripts/virtual-machines-windows-powershell-sample-create-vm.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Создает группу ресурсов, виртуальную машину и все связанные ресурсы.|
| [Создание высокодоступной виртуальной машины](./../scripts/virtual-machines-windows-powershell-sample-create-nlb-vm.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Создает несколько виртуальных машин с высокодоступной конфигурацией с балансировкой нагрузки.|
| [Создание виртуальной машины с помощью NGINX](./../scripts/virtual-machines-windows-powershell-sample-create-vm-iis.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Создает виртуальную машину и использует расширение пользовательских скриптов Azure для установки IIS. |
| [Создание виртуальной машины с IIS с помощью DSC](./../scripts/virtual-machines-windows-powershell-sample-create-iis-using-dsc.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Создает виртуальную машину и использует расширение настройки требуемого состояния (DSC) для установки IIS. |
| [Sample script to upload a VHD to Azure and create a new VM](./../scripts/virtual-machines-windows-powershell-upload-generalized-script.md) (Пример скрипта для передачи VHD в Azure и создания виртуальной машины) | Передача локального файла VHD в Azure. Создание образа на основе VHD. Создание виртуальной машины на основе этого образа. |
| [Создание виртуальной машины с помощью существующего управляемого диска ОС с использованием CLI](./../scripts/virtual-machines-windows-powershell-sample-create-vm-from-managed-os-disks.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Создает виртуальную машину путем подключения имеющегося управляемого диска как диска ОС. |
| [Создание виртуальной машины из моментального снимка с помощью PowerShell](./../scripts/virtual-machines-windows-powershell-sample-create-vm-from-snapshot.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Создает виртуальную машину из моментального снимка, сначала создав из него управляемый диск, а затем подключив этот диск как диск ОС. |
|**Управление хранилищем**||
| [Create a managed disk from a VHD file in a storage account in same or different subscription with PowerShell](../scripts/virtual-machines-windows-powershell-sample-create-managed-disk-from-vhd.md?toc=%2fpowershell%2fmodule%2ftoc.json) (Создание управляемого диска на основе VHD-файла в учетной записи хранения в той же или другой подписке с помощью PowerShell) | Создание управляемого диска из специализированного VHD в качестве диска ОС или из VHD данных в качестве диска данных в той же или в другой подписке.  |
| [Create a managed disk from a snapshot with PowerShell](../scripts/virtual-machines-windows-powershell-sample-create-managed-disk-from-snapshot.md?toc=%2fpowershell%2fmodule%2ftoc.json) (Создание управляемого диска из моментального снимка с помощью PowerShell) | Создание управляемого диска из моментального снимка. |
| [Copy managed disks in the same subscription or different subscription with PowerShell](../scripts/virtual-machines-windows-powershell-sample-copy-managed-disks-to-same-or-different-subscription.md?toc=%2fcli%2fmodule%2ftoc.json) (Копирование управляемых дисков в ту же или другую подписку с помощью PowerShell) | Копирование управляемого диска в ту же или в другую подписку, но в том же регионе, что и родительский управляемый диск. 
| [Экспорт моментального снимка в виде VHD в учетную запись хранения](../scripts/virtual-machines-windows-powershell-sample-copy-snapshot-to-storage-account.md?toc=%2fpowershell%2fmodule%2ftoc.json) (Export/Copy managed snapshots as VHD to a storage account in different region with PowerShell) | Экспорт управляемого моментального снимка в качестве VHD в учетную запись хранения в другом регионе. |
| [Create a snapshot from a VHD to create multiple identical managed disks in small amount of time with PowerShell](../scripts/virtual-machines-windows-powershell-sample-create-snapshot-from-vhd.md?toc=%2fpowershell%2fmodule%2ftoc.json) (Создание моментального снимка на основе VHD для быстрого создания нескольких идентичных управляемых дисков с помощью PowerShell) | Создание моментального снимка на основе VHD для создания нескольких идентичных управляемых дисков из моментального снимка за короткий промежуток времени.  |
| [Copy snapshot of a managed disk to same or different subscription with CLI](../scripts/virtual-machines-windows-powershell-sample-copy-snapshot-to-same-or-different-subscription.md?toc=%2fpowershell%2fmodule%2ftoc.json) (Копирование моментального снимка управляемого диска в ту же или в другую подписку с помощью интерфейса командной строки) | Копирование моментального снимка в ту же или в другую подписку, но в том же регионе, что и родительский моментальный снимок. |
|**Защита виртуальных машин**||
| [Encrypt a Windows virtual machine with Azure PowerShell](./../scripts/virtual-machines-windows-powershell-sample-encrypt-vm.md?toc=%2fpowershell%2fazure%2ftoc.json) (Шифрование виртуальной машины Windows с помощью Azure PowerShell) | Создание Azure Key Vault, ключа шифрования и субъекта-службы. Шифрование виртуальной машины. |
|**Мониторинг виртуальных машин**||
| [Мониторинг виртуальной машины с помощью Operations Management Suite](./../scripts/virtual-machines-windows-powershell-sample-create-vm-oms.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Создает виртуальную машину, устанавливает агент Operations Management Suite и регистрирует виртуальную машину в рабочей области OMS.  |
| | |


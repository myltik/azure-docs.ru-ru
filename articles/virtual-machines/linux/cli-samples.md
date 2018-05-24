---
title: Примеры Azure CLI | Документация Майкрософт
description: Примеры Azure CLI
services: virtual-machines-linux
documentationcenter: virtual-machines
author: iainfoulds
manager: jeconnoc
editor: tysonn
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/08/2017
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 5df71f1b662defa9103df4b10a86f67bf5ee6e4b
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/16/2018
---
# <a name="azure-cli-samples-for-linux-virtual-machines"></a>Примеры Azure CLI для виртуальных машин Linux

В следующей таблице содержатся ссылки на скрипты Bash, созданные с помощью Azure CLI.

| | |
|---|---|
|**Создание виртуальных машин**||
| [Создание виртуальной машины](./../scripts/virtual-machines-linux-cli-sample-create-vm-quick-create.md?toc=%2fcli%2fazure%2ftoc.json) | Создает виртуальную машину Linux с минимальной конфигурацией. |
| [Создание полностью настроенной виртуальной машины](./../scripts/virtual-machines-linux-cli-sample-create-vm.md?toc=%2fcli%2fazure%2ftoc.json) | Создает группу ресурсов, виртуальную машину и все связанные ресурсы.|
| [Создание высокодоступной виртуальной машины](./../scripts/virtual-machines-linux-cli-sample-nlb.md?toc=%2fcli%2fazure%2ftoc.json) | Создает несколько виртуальных машин с высокодоступной конфигурацией с балансировкой нагрузки. |
| [Создание виртуальной машины с помощью NGINX](./../scripts/virtual-machines-linux-cli-sample-create-vm-nginx.md?toc=%2fcli%2fazure%2ftoc.json) | Создает виртуальную машину и использует расширение пользовательских скриптов Azure для установки NGINX. |
| [Создание виртуальной машины с помощью WordPress](./../scripts/virtual-machines-linux-cli-sample-create-vm-wordpress.md?toc=%2fcli%2fazure%2ftoc.json) | Создает виртуальную машину и использует расширение пользовательских скриптов Azure для установки WordPress. |
| [Создание виртуальной машины с помощью существующего управляемого диска ОС с использованием CLI](./../scripts/virtual-machines-linux-cli-sample-create-vm-from-managed-os-disks.md?toc=%2fcli%2fmodule%2ftoc.json) | Создает виртуальную машину путем подключения имеющегося управляемого диска как диска ОС. |
| [Создание виртуальной машины из моментального снимка с помощью PowerShell](./../scripts/virtual-machines-linux-cli-sample-create-vm-from-snapshot.md?toc=%2fcli%2fmodule%2ftoc.json) | Создает виртуальную машину из моментального снимка, сначала создав из него управляемый диск, а затем подключив этот диск как диск ОС. |
|**Управление хранилищем**||
| [Create a managed disk from a VHD file in a storage account in the same subscription with CLI](../scripts/virtual-machines-linux-cli-sample-create-managed-disk-from-vhd.md?toc=%2fcli%2fmodule%2ftoc.json) (Создание управляемого диска из файла VHD в учетной записи хранения в одной подписке с помощью интерфейса командной строки) | Создание управляемого диска из специализированного VHD в качестве диска ОС или из VHD данных в качестве диска данных.  |
| [Create a managed disk from a snapshot with CLI](../scripts/virtual-machines-linux-cli-sample-create-managed-disk-from-snapshot.md?toc=%2fcli%2fmodule%2ftoc.json) (Создание управляемого диска из моментального снимка с помощью интерфейса командной строки) | Создание управляемого диска из моментального снимка. |
| [Copy managed disks in the same subscription or different subscription with PowerShell](../scripts/virtual-machines-linux-cli-sample-copy-managed-disks-to-same-or-different-subscription.md?toc=%2fcli%2fmodule%2ftoc.json) (Копирование управляемых дисков в ту же или другую подписку с помощью PowerShell) | Копирование управляемого диска в ту же или в другую подписку, но в том же регионе, что и родительский управляемый диск. 
| [Экспорт моментального снимка в виде VHD в учетную запись хранения](../scripts/virtual-machines-linux-cli-sample-copy-snapshot-to-storage-account.md?toc=%2fcli%2fmodule%2ftoc.json) (Export/Copy managed snapshots as VHD to a storage account in different region with PowerShell) | Экспорт управляемого моментального снимка в качестве VHD в учетную запись хранения в другом регионе. |
| [Copy snapshot of a managed disk to same or different subscription with CLI](../scripts/virtual-machines-linux-cli-sample-copy-snapshot-to-same-or-different-subscription.md?toc=%2fcli%2fmodule%2ftoc.json) (Копирование моментального снимка управляемого диска в ту же или в другую подписку с помощью интерфейса командной строки) | Копирование моментального снимка в ту же или в другую подписку, но в том же регионе, что и родительский моментальный снимок. |
|**Сети виртуальных машин**||
| [Защита сетевого трафика между виртуальными машинами](./../scripts/virtual-machines-linux-cli-sample-create-vm-nsg.md?toc=%2fcli%2fazure%2ftoc.json) | Создает две виртуальные машины, все связанные ресурсы, а также внешние и внутренние группы безопасности сети (NSG). |
|**Защита виртуальных машин**||
| [Encrypt a Windows virtual machine with Azure PowerShell](./../scripts/virtual-machines-linux-cli-sample-encrypt-vm.md?toc=%2fcli%2fazure%2ftoc.json) (Шифрование виртуальной машины Windows с помощью Azure PowerShell) | Создание Azure Key Vault, ключа шифрования и субъекта-службы. Шифрование виртуальной машины. |
|**Мониторинг виртуальных машин**||
| [Мониторинг виртуальной машины с помощью Operations Management Suite](./../scripts/virtual-machines-linux-cli-sample-create-vm-oms.md?toc=%2fcli%2fazure%2ftoc.json) | Создает виртуальную машину, устанавливает агент Operations Management Suite и регистрирует виртуальную машину в рабочей области OMS.  |
|**Устранение неполадок в виртуальных машинах**||
| [Устранение неполадок диска операционной системы виртуальных машин](./../scripts/virtual-machines-linux-cli-sample-mount-os-disk.md?toc=%2fcli%2fazure%2ftoc.json) | Подключает диск операционной системы из одной виртуальной машины в качестве диска данных ко второй виртуальной машине. |
| | |

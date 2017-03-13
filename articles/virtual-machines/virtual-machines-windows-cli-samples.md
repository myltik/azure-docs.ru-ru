---
title: "Примеры Azure CLI для Windows | Документация Майкрософт"
description: "Примеры Azure CLI для Windows"
services: virtual-machines-windows
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 02/27/2017
ms.author: nepeters
translationtype: Human Translation
ms.sourcegitcommit: cde57732f16d9571af8cc4ae5df3c012579ce831
ms.openlocfilehash: 29e8a19d4c05ac3c01ecbe43650c6607234c0546
ms.lasthandoff: 03/01/2017


---
# <a name="azure-cli-samples-for-windows-virtual-machines"></a>Примеры Azure CLI для виртуальных машин Windows

В следующей таблице содержатся ссылки на скрипты Bash, созданные с помощью Azure CLI, которые развертывают виртуальные машины Windows.

| | |
|---|---|
|**Создание виртуальных машин**||
| [Создание виртуальной машины](./scripts/virtual-machines-windows-cli-sample-create-vm-quick-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Создает виртуальную машину Windows с минимальной конфигурацией. |
| [Создание полностью настроенной виртуальной машины](./scripts/virtual-machines-windows-cli-sample-create-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Создает группу ресурсов, виртуальную машину и все связанные ресурсы.|
| [Создание высокодоступной виртуальной машины](./scripts/virtual-machines-windows-cli-sample-nlb.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Создает несколько виртуальных машин с высокодоступной конфигурацией с балансировкой нагрузки. |
| [Создание виртуальной машины с помощью NGINX](./scripts/virtual-machines-windows-cli-sample-create-vm-iis.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Создает виртуальную машину и использует расширение пользовательских скриптов Azure для установки IIS. |
| [Создание виртуальной машины с IIS с помощью DSC](./scripts/virtual-machines-windows-cli-sample-create-iis-using-dsc.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Создает виртуальную машину и использует расширение настройки требуемого состояния (DSC) для установки IIS. |
|**Сети виртуальных машин**||
| [Защита сетевого трафика между виртуальными машинами](./scripts/virtual-machines-windows-cli-sample-create-vm-nsg.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Создает две виртуальные машины, все связанные ресурсы, а также внешние и внутренние группы безопасности сети (NSG). |
|**Мониторинг виртуальных машин**||
| [Мониторинг виртуальной машины с помощью Operations Management Suite](./scripts/virtual-machines-windows-cli-sample-create-vm-oms.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Создает виртуальную машину, устанавливает агент Operations Management Suite и регистрирует виртуальную машину в рабочей области OMS.  |
| | |


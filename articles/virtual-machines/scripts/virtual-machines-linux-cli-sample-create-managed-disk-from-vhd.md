---
title: "Пример сценария Azure CLI. Создание управляемого диска на основе VHD-файла в учетной записи хранения в той же подписке | Документы Майкрософт"
description: "Пример сценария Azure CLI. Создание управляемого диска на основе VHD-файла в учетной записи хранения в той же подписке"
services: virtual-machines-linux
documentationcenter: storage
author: ramankumarlive
manager: kavithag
editor: tysonn
tags: azure-service-management
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/19/2017
ms.author: ramankum
ms.custom: mvc
ms.openlocfilehash: ede9457f5843d0a8a04503779970a553c5ed4f96
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-managed-disk-from-a-vhd-file-in-a-storage-account-in-the-same-subscription-with-cli"></a>Создание управляемого диска на основе VHD-файла в учетной записи хранения в той же подписке с помощью интерфейса командной строки

Этот сценарий создает управляемый диск на основе VHD-файла в учетной записи хранения в той же подписке. Этот сценарий можно использовать для импорта специализированного (не универсального и не обработанного командой Sysprep) виртуального жесткого диска в управляемый диск операционной системы для создания виртуальной машины. Кроме того, с его помощью можно импортировать виртуальный жесткий диск данных в управляемый диск данных. 


[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Пример скрипта

[!code-azurecli[main](../../../cli_scripts/virtual-machine/create-managed-data-disks-from-vhd/create-managed-data-disks-from-vhd.sh "Create managed disk from VHD")]


## <a name="script-explanation"></a>Описание скрипта

Этот сценарий использует следующие команды для создания управляемого диска на основе VHD-файла. Для каждой команды в таблице приведены ссылки на соответствующую документацию.

| Get-Help | Заметки |
|---|---|
| [az disk create](https://docs.microsoft.com/cli/azure/disk#az_disk_create) | Создает управляемый диск на основе URI виртуального жесткого диска в учетной записи хранения в той же подписке. |

## <a name="next-steps"></a>Дополнительная информация

[Создание виртуальной машины путем подключения управляемого диска как диска операционной системы](./virtual-machines-linux-cli-sample-create-vm-from-managed-os-disks.md?toc=%2fcli%2fmodule%2ftoc.json)

Дополнительные сведения об Azure CLI см. в [документации по Azure CLI](https://docs.microsoft.com/cli/azure/overview).

Дополнительные примеры сценариев интерфейса командной строки для виртуальных машин и управляемых дисков см. в [документации по виртуальным машинам Azure под управлением Linux](../../app-service/app-service-cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

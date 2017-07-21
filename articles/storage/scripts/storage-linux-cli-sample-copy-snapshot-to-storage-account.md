---
title: "Пример сценария Azure CLI. Экспорт или копирование моментального снимка в виде VHD в учетную запись хранения в другом регионе | Документы Майкрософт"
description: "Пример сценария Azure CLI. Экспорт или копирование моментального снимка в виде VHD в учетную запись хранения в той же или другой подписке"
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
ms.translationtype: Human Translation
ms.sourcegitcommit: be747170a0d8a7a6defd790a3f8a122c4d397671
ms.openlocfilehash: 6785b444e0a89ec3ee0c2cebd8a5af123bb367f3
ms.contentlocale: ru-ru
ms.lasthandoff: 05/23/2017

---

# <a name="exportcopy-managed-snapshots-as-vhd-to-a-storage-account-in-different-region-with-cli"></a>Экспорт или копирование управляемых моментальных снимков в виде VHD-файлов в учетную запись хранения в другом регионе с помощью интерфейса командной строки

Этот сценарий экспортирует управляемый моментальный снимок в учетную запись хранения в другом регионе. Сначала он создает URI SAS для моментального снимка, а затем использует его для копирования в учетную запись хранения в другом регионе. Этот сценарий можно использовать для поддержания резервной копии управляемых дисков в другом регионе в целях аварийного восстановления. 


[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Пример скрипта

[!code-azurecli[main](../../../cli_scripts/storage/copy-snapshots-to-storage-account/copy-snapshots-to-storage-account.sh "Копирование моментального снимка")]


## <a name="script-explanation"></a>Описание скрипта

Этот сценарий выполняет следующие команды для создания URI SAS для управляемого моментального снимка и копирует моментальный снимок в учетную запись хранения, используя созданный URI SAS. Для каждой команды в таблице приведены ссылки на соответствующую документацию.

| Команда | Примечания |
|---|---|
| [az snapshot grant-access](https://docs.microsoft.com/cli/azure/snapshot#grant-access) | Создает SAS только для чтения, который используется для копирования базового VHD-файла в учетную запись хранения или загрузки в локальную среду.  |
| [az storage blob copy start](https://docs.microsoft.com/en-us/cli/azure/storage/blob/copy#start) | Асинхронно копирует большой двоичный объект из одной учетной записи хранения в другую. |

## <a name="next-steps"></a>Дальнейшие действия

[Создание управляемого диска на основе VHD](./../scripts/storage-linux-cli-sample-create-managed-disk-from-vhd.md?toc=%2fcli%2fmodule%2ftoc.json)

[Создание виртуальной машины на основе управляемого диска](./../../virtual-machines/scripts/virtual-machines-linux-cli-sample-create-vm-from-managed-os-disks.md?toc=%2fcli%2fmodule%2ftoc.json)

Дополнительные сведения об Azure CLI см. в [документации по Azure CLI](https://docs.microsoft.com/cli/azure/overview).

Дополнительные примеры сценариев интерфейса командной строки для виртуальных машин и управляемых дисков см. в [документации по виртуальным машинам Azure под управлением Linux](../../virtual-machines/linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).


---
title: "Пример сценария Azure CLI. Создание управляемого диска на основе моментального снимка | Документы Майкрософт"
description: "Пример сценария Azure CLI. Создание управляемого диска на основе моментального снимка"
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
ms.translationtype: HT
ms.sourcegitcommit: 190ca4b228434a7d1b30348011c39a979c22edbd
ms.openlocfilehash: 26bddc69c7ff4854c84832ec924ab46ea342f1f4
ms.contentlocale: ru-ru
ms.lasthandoff: 09/09/2017

---

# <a name="create-a-managed-disk-from-a-snapshot-with-cli"></a>Создание управляемого диска на основе моментального снимка с помощью интерфейса командной строки

Этот сценарий создает управляемый диск на основе моментального снимка. Его можно использовать для восстановления виртуальной машины из моментальных снимков ОС и дисков данных. Можно создать управляемые диски данных и ОС на основе соответствующих моментальных снимков, а затем создать виртуальную машину, подключив эти управляемые диски. Кроме того, вы можете восстановить диски данных существующей виртуальной машины, подключив диски данных, созданные из моментальных снимков.


[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Пример скрипта

[!code-azurecli[main](../../../cli_scripts/virtual-machine/create-managed-disks-from-snapshot/create-managed-disks-from-snapshot.sh "Создание управляемого диска из моментального снимка")]


## <a name="script-explanation"></a>Описание скрипта

Этот сценарий использует приведенные ниже команды для создания управляемого диска на основе моментального снимка. Для каждой команды в таблице приведены ссылки на соответствующую документацию.

| Команда | Примечания |
|---|---|
| [az snapshot show](https://docs.microsoft.com/cli/azure/snapshot#az_snapshot_show) | Получает все свойства моментального снимка, используя имя и свойства группы ресурсов моментального снимка. Свойство идентификатора используется для создания управляемого диска.  |
| [az disk create](https://docs.microsoft.com/cli/azure/disk#az_disk_create) | Создает управляемый диск с помощью идентификатора управляемого моментального снимка. |

## <a name="next-steps"></a>Дальнейшие действия

[Создание виртуальной машины путем подключения управляемого диска как диска операционной системы](./virtual-machines-linux-cli-sample-create-vm-from-managed-os-disks.md?toc=%2fcli%2fmodule%2ftoc.json)

Дополнительные сведения об Azure CLI см. в [документации по Azure CLI](https://docs.microsoft.com/cli/azure/overview).

Дополнительные примеры сценариев интерфейса командной строки для виртуальных машин и управляемых дисков см. в [документации по виртуальным машинам Azure под управлением Linux](../../app-service-web/app-service-cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).


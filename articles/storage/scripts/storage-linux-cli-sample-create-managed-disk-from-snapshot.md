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
ms.translationtype: Human Translation
ms.sourcegitcommit: be747170a0d8a7a6defd790a3f8a122c4d397671
ms.openlocfilehash: 5d0c06f0359916ddb2230c67013679bbe8e8aff7
ms.contentlocale: ru-ru
ms.lasthandoff: 05/23/2017

---

# <a name="create-a-managed-disk-from-a-snapshot-with-cli"></a>Создание управляемого диска на основе моментального снимка с помощью интерфейса командной строки

Этот сценарий создает управляемый диск на основе моментального снимка. Его можно использовать для восстановления виртуальной машины из моментальных снимков операционной системы и дисков данных. Создайте управляемые диски данных и операционной системы на основе соответствующих моментальных снимков, а затем создайте виртуальную машину путем подключения управляемых дисков. Кроме того, вы можете восстановить диски данных существующей виртуальной машины путем подключения дисков данных, созданных на основе моментальных снимков.


[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Пример скрипта

[!code-azurecli[main](../../../cli_scripts/storage/create-managed-disks-from-snapshot/create-managed-disks-from-snapshot.sh "Создание управляемого диска на основе моментального снимка")]


## <a name="script-explanation"></a>Описание скрипта

Этот сценарий использует следующие команды для создания управляемого диска на основе моментального снимка. Для каждой команды в таблице приведены ссылки на соответствующую документацию.

| Команда | Примечания |
|---|---|
| [az snapshot show](https://docs.microsoft.com/cli/azure/snapshot#show) | Получает все свойства моментального снимка, используя имя и свойства группы ресурсов моментального снимка. Свойство идентификатора используется для создания управляемого диска.  |
| [az disk create](https://docs.microsoft.com/cli/azure/disk#create) | Создает управляемый диск с помощью идентификатора управляемого моментального снимка. |

## <a name="next-steps"></a>Дальнейшие действия

[Создание виртуальной машины путем подключения управляемого диска как диска операционной системы](./../../virtual-machines/scripts/virtual-machines-linux-cli-sample-create-vm-from-managed-os-disks.md?toc=%2fcli%2fmodule%2ftoc.json)

Дополнительные сведения об Azure CLI см. в [документации по Azure CLI](https://docs.microsoft.com/cli/azure/overview).

Дополнительные примеры сценариев интерфейса командной строки для виртуальных машин и управляемых дисков см. в [документации по виртуальным машинам Azure под управлением Linux](../../virtual-machines/linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).


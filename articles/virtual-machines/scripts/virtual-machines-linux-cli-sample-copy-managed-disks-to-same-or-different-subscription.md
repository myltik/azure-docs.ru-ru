---
title: "Пример сценария Azure CLI. Копирование (перемещение) управляемых дисков в ту же или другую подписку | Microsoft Docs"
description: "Пример сценария Azure CLI. Копирование (перемещение) управляемых дисков в ту же или другую подписку"
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
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: dcf92babf84872ffbbba81127952f8422104c723
ms.contentlocale: ru-ru
ms.lasthandoff: 08/21/2017

---

# <a name="copy-managed-disks-to-same-or-different-subscription-with-cli"></a>Копирование управляемых дисков в ту же или другую подписку с помощью интерфейса командной строки

Этот сценарий копирует управляемый диск в ту же или другую подписку в одном регионе. 


[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Пример скрипта

[!code-azurecli[main](../../../cli_scripts/virtual-machine/copy-managed-disks-to-same-or-different-subscription/copy-managed-disks-to-same-or-different-subscription.sh "Копирование управляемого диска")]


## <a name="script-explanation"></a>Описание скрипта

Этот сценарий использует приведенные ниже команды для создания управляемого диска в целевой подписке с помощью идентификатора исходного управляемого диска. Для каждой команды в таблице приведены ссылки на соответствующую документацию.

| Команда | Примечания |
|---|---|
| [az disk show](https://docs.microsoft.com/cli/azure/disk#show) | Получает все свойства управляемого диска, используя имя и свойства группы ресурсов управляемого диска. Свойство идентификатора используется для копирования управляемого диска в другую подписку.  |
| [az disk create](https://docs.microsoft.com/cli/azure/disk#create) | Копирует управляемый диск путем создания нового управляемого диска в другой подписке, используя идентификатор и имя родительского управляемого диска.  |

## <a name="next-steps"></a>Дальнейшие действия

[Создание виртуальной машины на основе управляемого диска](./virtual-machines-linux-cli-sample-create-vm-from-managed-os-disks.md?toc=%2fpowershell%2fmodule%2ftoc.json)

Дополнительные сведения об Azure CLI см. в [документации по Azure CLI](https://docs.microsoft.com/cli/azure/overview).

Дополнительные примеры сценариев интерфейса командной строки для виртуальных машин и управляемых дисков см. в [документации по виртуальным машинам Azure под управлением Linux](../../app-service-web/app-service-cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).


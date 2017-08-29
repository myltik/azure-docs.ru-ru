---
title: "Пример сценария Azure CLI. Копирование (перемещение) моментального снимка управляемого диска в ту же или другую подписку с помощью CLI | Документы Майкрософт"
description: "Пример сценария Azure CLI. Копирование (перемещение) моментального снимка управляемого диска в ту же или другую подписку с помощью CLI"
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
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: 6cc0125c08ccb77d014b4642d702c556fffdc8bf
ms.contentlocale: ru-ru
ms.lasthandoff: 08/21/2017

---

# <a name="copy-snapshot-of-a-managed-disk-to-same-or-different-subscription-with-cli"></a>Копирование моментального снимка управляемого диска в ту же или другую подписку с помощью интерфейса командной строки

Этот сценарий копирует моментальный снимок управляемого диска в ту же или другую подписку. Этот сценарий можно использовать для перемещения моментального снимка в другую подписку в том же регионе, что и родительский снимок.


[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Пример скрипта

[!code-azurecli[main](../../../cli_scripts/virtual-machine/copy-snapshot-to-same-or-different-subscription/copy-snapshot-to-same-or-different-subscription.sh "Копирование моментального снимка")]


## <a name="script-explanation"></a>Описание скрипта

Этот сценарий использует приведенные ниже команды для создания моментального снимка в целевой подписке с помощью идентификатора исходного моментального снимка. Для каждой команды в таблице приведены ссылки на соответствующую документацию.

| Команда | Примечания |
|---|---|
| [az snapshot show](https://docs.microsoft.com/cli/azure/snapshot#show) | Получает все свойства моментального снимка, используя имя и свойства группы ресурсов моментального снимка. Свойство идентификатора используется для копирования моментального снимка в другую подписку.  |
| [az snapshot create](https://docs.microsoft.com/cli/azure/snapshot#create) | Копирует моментальный снимок путем создания моментального снимка в другой подписке с помощью идентификатора и имени родительского снимка.  |

## <a name="next-steps"></a>Дальнейшие действия

[Создание виртуальной машины на основе моментального снимка](./virtual-machines-linux-cli-sample-create-vm-from-snapshot.md?toc=%2fpowershell%2fmodule%2ftoc.json)

Дополнительные сведения об Azure CLI см. в [документации по Azure CLI](https://docs.microsoft.com/cli/azure/overview).

Дополнительные примеры сценариев интерфейса командной строки для виртуальных машин и управляемых дисков см. в [документации по виртуальным машинам Azure под управлением Linux](../../app-service-web/app-service-cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).


---
title: Пример скрипта Azure CLI. Остановка работы и удаление виртуальной машины в пользовательской лаборатории | Документация Майкрософт
description: Этот скрипт Azure CLI предназначен для остановки работы и удаления виртуальной машины в пользовательской лаборатории.
services: lab-services
author: spelluru
manager: ''
editor: ''
ms.assetid: ''
ms.service: lab-services
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/12/2018
ms.author: spelluru
ms.custom: mvc
ms.openlocfilehash: 0da99bb5e029f4d1c6be74d09ad11c6017121a7c
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/07/2018
---
# <a name="use-azure-cli-to-stop-and-delete-a-virtual-machine-in-a-custom-lab"></a>Остановка работы и удаление виртуальной машины в пользовательской лаборатории с помощью Azure CLI

Этот скрипт Azure CLI предназначен для остановки работы и удаления виртуальной машины (VM) в пользовательской лаборатории. 

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Пример скрипта

[!code-azurecli-interactive[main](../../../cli_scripts/devtest-lab/stop-delete-virtual-machine-in-lab/stop-delete-virtual-machine-in-lab.sh "Stop and delete a VM in a custom lab")]

## <a name="script-explanation"></a>Описание скрипта

Этот сценарий использует следующие команды:

| Get-Help | Заметки |
|---|---|
| [az lab vm stop](/cli/azure/lab/vm?view=azure-cli-latest#az-lab-vm-stop) | Остановка работы виртуальной машины в пользовательской лаборатории. Эта операция может занять некоторое время. |
| [az lab vm delete](/cli/azure/lab/vm?view=azure-cli-latest#az-lab-vm-delete) | Удаление виртуальной машины в пользовательской лаборатории. Эта операция может занять некоторое время. |


## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения об Azure CLI см. в [документации по Azure CLI](https://docs.microsoft.com/cli/azure).

Дополнительные примеры сценариев PowerShell для Служб лабораторий Azure приведены [здесь](../samples-cli.md).
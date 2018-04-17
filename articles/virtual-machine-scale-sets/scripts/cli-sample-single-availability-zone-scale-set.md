---
title: Примеры Azure CLI 2.0. Однозонный масштабируемый набор виртуальных машин | Документация Майкрософт
description: Примеры для Azure CLI 2.0
services: virtual-machine-scale-sets
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/27/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: f881d90628f5e4b5c4fc3fe56ecb1b3377d8ba42
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2018
---
# <a name="create-a-single-zone-virtual-machine-scale-set-with-powershell"></a>Создает однозонный масштабируемый набор виртуальных машин с помощью PowerShell
Этот скрипт создает масштабируемый набор виртуальных машин под управлением Ubuntu в одной зоне доступности. После выполнения сценария можно получить доступ к виртуальной машине по протоколу RDP.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Пример скрипта
[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine-scale-sets/create-single-availability-zone/create-single-availability-zone.sh "Create single-zone scale set")]

## <a name="clean-up-deployment"></a>Очистка развертывания
Выполните следующую команду, чтобы удалить группу ресурсов, масштабируемый набор и все связанные ресурсы.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Описание скрипта
Для создания группы ресурсов, масштабируемого набора виртуальных машин и всех связанных ресурсов этот скрипт использует следующие команды. Для каждой команды в таблице приведены ссылки на соответствующую документацию.

| Get-Help | Заметки |
|---|---|
| [az group create](/cli/azure/ad/group#az_ad_group_create) | Создает группу ресурсов, в которой хранятся все ресурсы. |
| [az vmss create](/cli/azure/vmss#az_vmss_create) | Создает масштабируемый набор виртуальных машин и подключает его к виртуальной сети, подсети и группе безопасности сети. Чтобы распределить трафик между несколькими экземплярами виртуальных машин, создается еще и подсистема балансировки нагрузки. Эта команда также указывает образ виртуальной машины, который будет использоваться, и учетные данные администратора.  |
| [az group delete](/cli/azure/ad/group#delete) | Удаляет группу ресурсов со всеми вложенными ресурсами. |

## <a name="next-steps"></a>Дополнительная информация
См. [документацию по Azure CLI 2.0](https://docs.microsoft.com/cli/azure/overview)

См. дополнительные примеры сценариев Azure CLI 2.0 для [масштабируемого набора виртуальных машин Azure](../cli-samples.md).
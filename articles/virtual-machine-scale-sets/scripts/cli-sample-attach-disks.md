---
title: Примеры для Azure CLI 2.0. Присоединение и использование дисков данных | Документация Майкрософт
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
ms.openlocfilehash: 88497484071d44e083ad44c45d2eab61f17fd95f
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2018
ms.locfileid: "30246378"
---
# <a name="attach-and-use-data-disks-with-a-virtual-machine-scale-set-with-the-azure-cli-20"></a>Присоединение и дисков данных к масштабируемому набору виртуальных машин и их использование с помощью Azure CLI 2.0
При помощи этого скрипта создается масштабируемый набор виртуальных машин, а также присоединяются и подготавливаются диски данных.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Пример скрипта
[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine-scale-sets/use-data-disks/use-data-disks.sh "Create a virtual machine scale set with data disks")]

## <a name="clean-up-deployment"></a>Очистка развертывания
Выполните приведенную ниже команду, чтобы удалить группу ресурсов, масштабируемый набор и все связанные с ним ресурсы.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Описание скрипта
Для создания группы ресурсов, масштабируемого набора виртуальных машин и всех связанных ресурсов в этом скрипте используются приведенные ниже команды. Для каждой команды в таблице приведены ссылки на соответствующую документацию.

| Get-Help | Заметки |
|---|---|
| [az group create](/cli/azure/ad/group#az_ad_group_create) | Создает группу ресурсов, в которой хранятся все ресурсы. |
| [az vmss create](/cli/azure/vmss#az_vmss_create) | Создание масштабируемого набора виртуальных машин и его подключение к виртуальной сети, подсети и группе безопасности сети. Чтобы распределить трафик между несколькими экземплярами виртуальных машин, создается еще и подсистема балансировки нагрузки. Эта команда также указывает образ виртуальной машины, который будет использоваться, и учетные данные администратора.  |
| [az vmss disk attach](/cli/azure/vmss/disk#az_vmss_disk_attach) | Создание дисков данных и их присоединение к масштабируемому набору виртуальных машин. |
| [az vmss extension set](/cli/azure/vmss/extension#az_vmss_extension_set) | Установка настраиваемого расширения скриптов Azure для выполнения сценария, который позволяет подготовить диски данных для каждого экземпляра виртуальной машины. |
| [az group delete](/cli/azure/ad/group#delete) | Удаляет группу ресурсов со всеми вложенными ресурсами. |

## <a name="next-steps"></a>Дополнительная информация
Дополнительные сведения см. в [документации по Azure CLI 2.0](https://docs.microsoft.com/cli/azure/overview)

Дополнительные примеры сценариев Azure CLI 2.0 для масштабируемого набора виртуальных машин Azure см. в [этой документации](../cli-samples.md).
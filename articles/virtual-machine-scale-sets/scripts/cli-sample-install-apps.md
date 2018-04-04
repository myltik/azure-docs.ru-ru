---
title: Примеры для Azure CLI 2.0. Установка приложений | Документация Майкрософт
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
ms.openlocfilehash: b1a8510efccf810bd4fbdd647d58d38cd87d019f
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2018
---
# <a name="install-applications-into-a-virtual-machine-scale-set-with-the-azure-cli-20"></a>Установка приложений в масштабируемый набор виртуальных машин с помощью Azure CLI 2.0
Этот скрипт создает виртуальную машину Azure под управлением Ubuntu и использует расширение пользовательских скриптов для установки базовых веб-приложений. После выполнения скрипта можно получить доступ к веб-приложению с помощью веб-браузера.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Пример скрипта
[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine-scale-sets/install-apps/install-apps.sh "Установка приложений в масштабируемый набор"")]

## <a name="clean-up-deployment"></a>Очистка развертывания
Выполните приведенную ниже команду, чтобы удалить группу ресурсов, масштабируемый набор и все связанные ресурсы.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Описание скрипта
Для создания группы ресурсов, масштабируемого набора виртуальных машин и всех связанных ресурсов в этом скрипте используются приведенные ниже команды. Для каждой команды в таблице приведены ссылки на соответствующую документацию.

| Get-Help | Заметки |
|---|---|
| [az group create](/cli/azure/ad/group#az_ad_group_create) | Создает группу ресурсов, в которой хранятся все ресурсы. |
| [az vmss create](/cli/azure/vmss#az_vmss_create) | Создает масштабируемый набор виртуальных машин и подключает его к виртуальной сети, подсети и группе безопасности сети. Чтобы распределить трафик между несколькими экземплярами виртуальных машин, создается еще и подсистема балансировки нагрузки. Эта команда также указывает образ виртуальной машины, который будет использоваться, и учетные данные администратора.  |
| [az vmss extension set](/cli/azure/vmss/extension#az_vmss_extension_set) | Устанавливает расширение пользовательских скриптов Azure для выполнения сценария, который позволяет подготовить диски данных для каждого экземпляра виртуальной машины. |
| [az network lb rule create](/cli/azure/network/lb/rule#az_network_lb_rule_create) | Создает правило подсистемы балансировки нагрузки для распределения трафика через TCP-порт 80 между экземплярами виртуальных машин в масштабируемом наборе. |
| [az network public-ip show](/cli/azure/network/public-ip#az_network_public_ip_show) | Позволяет получить сведения об общедоступном IP-адресе, который использует подсистема балансировки нагрузки. |
| [az group delete](/cli/azure/ad/group#delete) | Удаляет группу ресурсов со всеми вложенными ресурсами. |

## <a name="next-steps"></a>Дополнительная информация
Дополнительные сведения см. в [документации по Azure CLI 2.0](https://docs.microsoft.com/cli/azure/overview).

См. дополнительные примеры сценариев Azure CLI 2.0 для [масштабируемого набора виртуальных машин Azure](../cli-samples.md).
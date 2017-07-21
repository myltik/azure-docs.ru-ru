---
title: "Пример скрипта Azure CLI. Пиринг между двумя виртуальными сетями | Документация Майкрософт"
description: "Пример скрипта Azure CLI. Пиринг между двумя виртуальными сетями"
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: timlt
editor: tysonn
tags: 
ms.assetid: 
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: 
ms.workload: infrastructure
ms.date: 07/07/2017
ms.author: kumud
ms.translationtype: Human Translation
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.openlocfilehash: a2b8fda288072e2fb0087988bbd68d3e4d9031d8
ms.contentlocale: ru-ru
ms.lasthandoff: 07/08/2017

---

# <a name="peer-two-virtual-networks"></a>Пиринг между двумя виртуальными сетями

Этот скрипт создает и соединяет две виртуальные сети в одном регионе через сеть Azure. В результате выполнения скрипта будет создан пиринг между двумя виртуальными сетями.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]


## <a name="sample-script"></a>Пример скрипта

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-network/peer-two-virtual-networks/peer-two-virtual-networks.sh "Пиринг между двумя виртуальными сетями")]

## <a name="clean-up-deployment"></a>Очистка развертывания 

Выполните следующую команду, чтобы удалить группу ресурсов, виртуальную машину и все связанные с ней ресурсы.

```azurecli
az group delete --name myResourceGroup --yes
```

## <a name="script-explanation"></a>Описание скрипта

Для создания группы ресурсов, виртуальной машины и всех связанных ресурсов этот скрипт использует следующие команды. Для каждой команды в таблице приведены ссылки на соответствующую документацию.

| Команда | Примечания |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | Создает группу ресурсов, в которой хранятся все ресурсы. |
| [az network vnet create](https://docs.microsoft.com/cli/azure/network/vnet#create) | Создает виртуальную сеть и подсеть Azure. |
| [az network vnet peering create](https://docs.microsoft.com/cli/azure/network/vnet/peering#create) | Создает пиринг между двумя виртуальными сетями.  |
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#set) | Удаляет группу ресурсов со всеми вложенными ресурсами. |

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения об Azure CLI см. в [документации по Azure CLI](https://docs.microsoft.com/cli/azure/overview).

Дополнительные примеры сценариев Azure CLI для сетей см. в статье [Примеры Azure CLI](../cli-samples.md).


---
title: "Пример скрипта Azure CLI. Создание сети для многоуровневых приложений | Документация Майкрософт"
description: "Пример скрипта Azure CLI. Создание сети для многоуровневых приложений."
services: virtual-network
documentationcenter: virtual-network
author: jimdial
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
ms.author: jdial
ms.translationtype: Human Translation
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.openlocfilehash: de65d820f2d9eea49b58185c81d815675fd76740
ms.contentlocale: ru-ru
ms.lasthandoff: 07/08/2017


---

# <a name="create-a-network-for-multi-tier-applications"></a>Создание сети для многоуровневых приложений

В этом примере скрипта создается виртуальная сеть с интерфейсной и внутренней подсетями. Трафик к интерфейсной подсети принимается по протоколам HTTP и SSH, в то время как трафик к внутренней подсети принимается только от MySQL по порту 3306. После запуска скрипта у вас будет две виртуальные машины, по одной в каждой подсети, на которых вы можете развернуть веб-сервер и программное обеспечение MySQL.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]


## <a name="sample-script"></a>Пример скрипта


[!code-azurecli-interactive[main](../../../cli_scripts/virtual-network/virtual-network-multi-tier-application/virtual-network-multi-tier-application.sh  "Виртуальная сеть для многоуровневого приложения")]

## <a name="clean-up-deployment"></a>Очистка развертывания 

Выполните следующую команду, чтобы удалить группу ресурсов, виртуальную машину и все связанные с ней ресурсы.

```azurecli
az group delete --name MyResourceGroup --yes
```

## <a name="script-explanation"></a>Описание скрипта

Для создания группы ресурсов, виртуальной сети и групп безопасности сети этот скрипт использует следующие команды. Для каждой команды в таблице приведены ссылки на соответствующую документацию.

| Команда | Примечания |
|---|---|
| [az group create](/cli/azure/group#create) | Создает группу ресурсов, в которой хранятся все ресурсы. |
| [az network vnet create](/cli/azure/network/vnet#create) | Создает виртуальную сеть Azure и интерфейсную подсеть. |
| [az network subnet create](/cli/azure/network/vnet/subnet#create) | Создает внутреннюю подсеть. |
| [az network public-ip create](/cli/azure/network/public-ip#create) | Создает общедоступный IP-адрес для доступа к виртуальной машине из Интернета. |
| [az network nic create](/cli/azure/network/nic#create) | Создает виртуальные сетевые интерфейсы и присоединяет их к интерфейсной и внутренней подсети виртуальной сети. |
| [az network nsg create](/cli/azure/network/nsg#create) | Создает группы безопасности сети (NSG), которые связаны с интерфейсной и внутренней подсетями. |
| [az network nsg rule create](/cli/azure/network/nsg/rule#create) |Создает правила групп безопасности сети, которые разрешают или блокируют определенные порты для конкретных подсетей. |
| [az vm create](/cli/azure/vm#create) | Создает виртуальные машины и присоединяет сетевой адаптер к каждой из них. Эта команда также указывает образ виртуальной машины и учетные данные администратора. |
| [az group delete](/cli/azure/group#delete) | Удаляет группу ресурсов и все содержащиеся в ней ресурсы. |

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения об Azure CLI см. в [документации по Azure CLI](/cli/azure/overview).

Дополнительные примеры сценариев Azure CLI для сетей Azure см. в [обзоре документации по сетям Azure](../cli-samples.md).

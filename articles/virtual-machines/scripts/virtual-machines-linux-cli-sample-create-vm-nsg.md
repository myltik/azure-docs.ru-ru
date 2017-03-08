---
title: "Пример скрипта Azure CLI. Создание двух виртуальных машин с внутренней и внешней группой безопасности сети | Документация Майкрософт"
description: "Пример скрипта Azure CLI. Создание двух виртуальных машин с внутренней и внешней группой безопасности сети"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/27/2017
ms.author: nepeters
translationtype: Human Translation
ms.sourcegitcommit: a172d73732354d31d717d8e2f3a5c5c43cbbd6dc
ms.openlocfilehash: 864c59488ef68a2da04bb9bedacf24dff4f59a25
ms.lasthandoff: 03/01/2017

---

# <a name="secure-network-traffic-between-virtual-machines"></a>Защита сетевого трафика между виртуальными машинами

Этот скрипт создает две виртуальные машины и защищает их входящий трафик. Одна виртуальная машина доступна через Интернет. Ее группа безопасности сети настроена таким образом, чтобы разрешать трафик на портах 22 и 80. Вторая виртуальная машина недоступна через Интернет. Ее группа безопасности сети настроена таким образом, чтобы разрешать трафик только из первой виртуальной машины. 

Перед выполнением этого скрипта убедитесь, что установлено подключение к Azure, выполнив команду `az login`.

Этот пример работает в оболочке Bash. Сведения о параметрах выполнения скриптов Azure CLI в клиенте Windows см. в статье [Использование Azure CLI в Windows](../virtual-machines-windows-cli-options.md).

## <a name="sample-script"></a>Пример скрипта

[!code-azurecli[main](../../../cli_scripts/virtual-machine/create-vm-nsg/create-vm-nsg.sh "Создание виртуальной машины с использованием группы безопасности сети")]

## <a name="clean-up-deployment"></a>Очистка развертывания 

После выполнения примера скрипта можно удалить группу ресурсов, виртуальную машину и все связанные с ней ресурсы, использовав следующую команду.

```azurecli
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Описание скрипта

Для создания группы ресурсов, виртуальной машины и всех связанных ресурсов этот скрипт использует следующие команды. Для каждой команды в таблице приведены ссылки на соответствующую документацию.

| Команда | Примечания |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | Создает группу ресурсов, в которой хранятся все ресурсы. |
| [az network vnet create](https://docs.microsoft.com/cli/azure/network/vnet#create) | Создает виртуальную сеть и подсеть Azure. |
| [az network vnet subnet create](https://docs.microsoft.com/cli/azure/network/vnet/subnet#create) | Создает подсеть. |
| [az vm create](https://docs.microsoft.com/cli/azure/vm#create) | Создает виртуальную машину и подключает ее к сетевой карте, виртуальной сети, подсети и группе безопасности сети. Эта команда также указывает образ виртуальной машины, который будет использоваться, и учетные данные администратора.  |
| [az network nsg rule list](https://docs.microsoft.com/cli/azure/network/nsg/rule#list) | Возвращает сведения о правиле группы безопасности сети. В этом примере имя правила хранится в переменной для дальнейшего использования в скрипте. |
| [az network nsg rule update](https://docs.microsoft.com/cli/azure/network/nsg/rule#update) | Обновляет правило NSG. В этом примере внутреннее правило обновляется, чтобы разрешать передачу трафика только из подсети переднего плана. |
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#set) | Удаляет группу ресурсов со всеми вложенными ресурсами. |

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения об Azure CLI см. в [документации по Azure CLI](https://docs.microsoft.com/cli/azure/overview).

Дополнительные примеры скриптов интерфейса командной строки для виртуальных машин см. в [документации по виртуальным машинам Azure под управлением Linux](../virtual-machines-linux-cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).


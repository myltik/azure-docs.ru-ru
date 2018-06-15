---
title: Пример скрипта Azure CLI. Создание сети для многоуровневых приложений | Документация Майкрософт
description: Пример скрипта Azure CLI. Создание сети для многоуровневых приложений.
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: ''
tags: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 03/20/2018
ms.author: jdial
ms.openlocfilehash: e84c9555204a88622282c790d352ff7b052f560c
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/05/2018
ms.locfileid: "30840234"
---
# <a name="create-a-network-for-multi-tier-applications-script-sample"></a>Пример скрипта для создания сети для многоуровневых приложений

В этом примере скрипта создается виртуальная сеть с интерфейсной и внутренней подсетями. Трафик к интерфейсной подсети принимается по протоколам HTTP и SSH, в то время как трафик к внутренней подсети принимается только от MySQL по порту 3306. При выполнении скрипта создается две виртуальные машины (по одной в каждой подсети), на которых вы можете развернуть веб-сервер и программное обеспечение MySQL.

Вы можете выполнить скрипт из Azure [Cloud Shell](https://shell.azure.com/bash) или из локальной установки Azure CLI. Если вы используете CLI локально, для этого скрипта требуется версия 2.0.28 или выше. Выполните командлет `az --version`, чтобы узнать установленную версию. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0](/cli/azure/install-azure-cli). Если интерфейс командной строки запущен локально, необходимо также выполнить командлет `az login`, чтобы создать подключение к Azure.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]


## <a name="sample-script"></a>Пример скрипта


[!code-azurecli-interactive[main](../../../cli_scripts/virtual-network/virtual-network-multi-tier-application/virtual-network-multi-tier-application.sh  "Virtual network for multi-tier application")]

## <a name="clean-up-deployment"></a>Очистка развертывания 

Выполните следующую команду, чтобы удалить группу ресурсов, виртуальную машину и все связанные с ней ресурсы:

```azurecli
az group delete --name MyResourceGroup --yes
```

## <a name="script-explanation"></a>Описание скрипта

Для создания группы ресурсов, виртуальной сети и групп безопасности сети в этом скрипте используются приведенные ниже команды. Для каждой команды в следующей таблице приведены ссылки на соответствующую документацию:

| Get-Help | Заметки |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | Создает группу ресурсов, в которой хранятся все ресурсы. |
| [az network vnet create](/cli/azure/network/vnet#az_network_vnet_create) | Создает виртуальную сеть Azure и интерфейсную подсеть. |
| [az network subnet create](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create) | Создает внутреннюю подсеть. |
| [az network public-ip create](/cli/azure/network/public-ip#az_network_public_ip_create) | Создает общедоступный IP-адрес для доступа к виртуальной машине из Интернета. |
| [az network nic create](/cli/azure/network/nic#az_network_nic_create) | Создает виртуальные сетевые интерфейсы и присоединяет их к интерфейсной и внутренней подсети виртуальной сети. |
| [az network nsg create](/cli/azure/network/nsg#az_network_nsg_create) | Создает группы безопасности сети (NSG), которые связаны с интерфейсной и внутренней подсетями. |
| [az network nsg rule create](/cli/azure/network/nsg/rule#az_network_nsg_rule_create) |Создает правила групп безопасности сети, которые разрешают или блокируют определенные порты для конкретных подсетей. |
| [az vm create](/cli/azure/vm#az_vm_create) | Создает виртуальные машины и присоединяет сетевой адаптер к каждой из них. Эта команда также указывает образ виртуальной машины и учетные данные администратора. |
| [az group delete](/cli/azure/group#az_group_delete) | Удаляет группу ресурсов и все содержащиеся в ней ресурсы. |

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения об Azure CLI см. в [документации по Azure CLI](/cli/azure).

Дополнительные примеры скриптов см. на [странице с примерами CLI для виртуальных сетей](../cli-samples.md).

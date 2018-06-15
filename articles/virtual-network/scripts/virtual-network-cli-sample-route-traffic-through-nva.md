---
title: Пример скрипта Azure CLI. Маршрутизация трафика через виртуальный сетевой модуль | Документация Майкрософт
description: Пример скрипта Azure CLI. Маршрутизация трафика через виртуальный сетевой модуль брандмауэра.
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
ms.openlocfilehash: d0d410fd59574fe2884ae941ef3fd64600930f31
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/05/2018
ms.locfileid: "30840808"
---
# <a name="route-traffic-through-a-network-virtual-appliance-script-sample"></a>Пример скрипта для маршрутизации трафика через виртуальный сетевой модуль

В этом примере скрипта создается виртуальная сеть с интерфейсной и внутренней подсетями. Скрипт также создает виртуальную машину с IP-пересылкой, которая позволяет маршрутизировать трафик между двумя подсетями. После выполнения скрипта вы можете развернуть на виртуальной машине программное обеспечение сети, например брандмауэр.

Вы можете выполнить скрипт из Azure [Cloud Shell](https://shell.azure.com/bash) или из локальной установки Azure CLI. Если вы используете CLI локально, для этого скрипта требуется версия 2.0.28 или выше. Выполните командлет `az --version`, чтобы узнать установленную версию. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0](/cli/azure/install-azure-cli). Если интерфейс командной строки запущен локально, необходимо также выполнить командлет `az login`, чтобы создать подключение к Azure.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]


## <a name="sample-script"></a>Пример скрипта

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-network/route-traffic-through-nva/route-traffic-through-nva.sh "Route traffic through a network virtual appliance")]

## <a name="clean-up-deployment"></a>Очистка развертывания 

Выполните следующую команду, чтобы удалить группу ресурсов, виртуальную машину и все связанные с ней ресурсы:

```azurecli
az group delete --name MyResourceGroup --yes
```

## <a name="script-explanation"></a>Описание скрипта

Для создания группы ресурсов, виртуальной сети и групп безопасности сети этот скрипт использует следующие команды. Для каждой команды в следующей таблице приведены ссылки на соответствующую документацию:

| Get-Help | Заметки |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | Создает группу ресурсов, в которой хранятся все ресурсы. |
| [az network vnet create](/cli/azure/network/vnet#az_network_vnet_create) | Создает виртуальную сеть Azure и интерфейсную подсеть. |
| [az network subnet create](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create) | Создает внутреннюю подсеть и подсеть DMZ. |
| [az network public-ip create](/cli/azure/network/public-ip#az_network_public_ip_create) | Создает общедоступный IP-адрес для доступа к виртуальной машине из Интернета. |
| [az network nic create](/cli/azure/network/nic#az_network_nic_create) | Создает виртуальный сетевой интерфейс и включает IP-пересылку для него. |
| [az network nsg create](/cli/azure/network/nsg#az_network_nsg_create) | Создает группу безопасности сети (NSG). |
| [az network nsg rule create](/cli/azure/network/nsg/rule#az_network_nsg_rule_create) | Создает правила группы безопасности сети, которые разрешают входящий трафик по портам HTTP и HTTPS к виртуальной машине. |
| [az network vnet subnet update](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_update)| Связывает группы безопасности сети и таблицы маршрутов с подсетями. |
| [az network route-table create](/cli/azure/network/route-table#az_network_route_table_create)| Создает таблицу маршрутов для всех маршрутов. |
| [az network route-table route create](/cli/azure/network/route-table/route#az_network_route_table_route_create)| Создает маршруты для маршрутизации трафика между подсетями и Интернетом через виртуальную машину. |
| [az vm create](/cli/azure/vm#az_vm_create) | Создает виртуальную машину и присоединяет к ней сетевой адаптер. Эта команда также указывает образ виртуальной машины и учетные данные администратора. |
| [az group delete](/cli/azure/group#az_group_delete) | Удаляет группу ресурсов и все содержащиеся в ней ресурсы. |

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения об Azure CLI см. в [документации по Azure CLI](/cli/azure).

Дополнительные примеры скриптов см. на [странице с примерами CLI для виртуальных сетей](../cli-samples.md).

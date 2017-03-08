---
title: "Пример скрипта Azure CLI. Создание виртуальной машины Linux с помощью NLB | Документация Майкрософт"
description: "Пример скрипта Azure CLI. Создание виртуальной машины Linux с помощью NLB"
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
ms.sourcegitcommit: 07d91dfa905d22334bd051f9d5f3d936d38efc88
ms.openlocfilehash: 0253b5368acc370580dda645cf15b99a2acc437d
ms.lasthandoff: 02/28/2017

---

# <a name="create-a-highly-available-vm"></a>Создание высокодоступной виртуальной машины

Этот пример скрипта позволяет создать все необходимые компоненты для запуска нескольких виртуальных машин Ubuntu, настроенных в высокодоступной конфигурации с балансировкой нагрузки. Выполнив этот скрипт, вы получите три виртуальные машины, добавленные в группу доступности Azure, а также доступные в Azure Load Balancer. 

Перед выполнением этого скрипта убедитесь, что установлено подключение к Azure, выполнив команду `az login`.

Этот пример работает в оболочке Bash. Сведения о параметрах выполнения скриптов Azure CLI в клиенте Windows см. в статье [Использование Azure CLI в Windows](../virtual-machines-windows-cli-options.md).

## <a name="sample-script"></a>Пример скрипта

[!code-azurecli[main](../../../cli_scripts/virtual-machine/create-vm-nlb/create-vm-nlb.sh "Быстрое создание виртуальной машины")]

## <a name="clean-up-deployment"></a>Очистка развертывания 

После выполнения примера скрипта можно удалить группу ресурсов, виртуальную машину и все связанные с ней ресурсы, использовав следующую команду.

```azurecli
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Описание скрипта

Для создания группы ресурсов, виртуальной машины, группы доступности, балансировщика нагрузки и всех связанных ресурсов этот скрипт использует следующие команды. Для каждой команды в таблице приведены ссылки на соответствующую документацию.

| Команда | Примечания |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | Создает группу ресурсов, в которой хранятся все ресурсы. |
| [az network vnet create](https://docs.microsoft.com/cli/azure/network/vnet#create) | Создает виртуальную сеть и подсеть Azure. |
| [az network public-ip create](https://docs.microsoft.com/cli/azure/network/public-ip#create) | Создает общедоступный IP-адрес со статическим IP-адресом и связанным DNS-именем. |
| [az network lb create](https://docs.microsoft.com/cli/azure/network/lb#create) | Создает балансировщик сетевой нагрузки Azure. |
| [az network lb probe create](https://docs.microsoft.com/cli/azure/network/lb/probe#create) | Создает пробу балансировщика сетевой нагрузки. Она используется для отслеживания каждой виртуальной машины в наборе балансировщика сетевой нагрузки. Если любая виртуальная машина становится недоступной, к ней не направляется трафик. |
| [az network lb rule create](https://docs.microsoft.com/cli/azure/network/lb/rule#create) | Создает правило балансировщика сетевой нагрузки. В этом примере создается правило для порта 80. Так как трафик HTTP поступает в балансировщик сетевой нагрузки, он перенаправляется на порт 80 одной из виртуальных машин в наборе балансировщика сетевой нагрузки. |
| [az network lb inbound-nat-rule create](https://docs.microsoft.com/cli/azure/network/lb/inbound-nat-rule#create) | Создает правило преобразования сетевых адресов (NAT) балансировщика сетевой нагрузки.  Правила NAT сопоставляют порт балансировщика сетевой нагрузки с портом виртуальной машины. В этом примере создается правило NAT для SSH-трафика на каждой виртуальной машине в наборе балансировщика сетевой нагрузки.  |
| [az network nsg create](https://docs.microsoft.com/cli/azure/network/nsg#create) | Создает группу безопасности сети (NSG), которая выполняет роль периметра безопасности между Интернетом и виртуальной машиной. |
| [az network nsg rule create](https://docs.microsoft.com/cli/azure/network/nsg/rule#create) | Создает правило NSG, разрешающее входящий трафик. В этом примере открывается порт 22 для трафика SSH. |
| [az network nic create](https://docs.microsoft.com/cli/azure/network/nic#create) | Создает виртуальную сетевую карту и подключает ее к виртуальной сети, подсети и группе безопасности сети. |
| [az vm availability-set create](https://docs.microsoft.com/cli/azure/network/lb/rule#create) | Создает группу доступности. Группы доступности обеспечивают непрерывную работу приложения, распределяя виртуальные машины по физическим ресурсам. Таким образом, в случае сбоя он не затронет весь набор ресурсов. |
| [az vm create](https://docs.microsoft.com/cli/azure/vm/availability-set#create) | Создает виртуальную машину и подключает ее к сетевой карте, виртуальной сети, подсети и группе безопасности сети. Эта команда также указывает образ виртуальной машины, который будет использоваться, и учетные данные администратора.  |
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#set) | Удаляет группу ресурсов со всеми вложенными ресурсами. |

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения об Azure CLI см. в [документации по Azure CLI](https://docs.microsoft.com/cli/azure/overview).

Дополнительные примеры скриптов интерфейса командной строки для виртуальных машин см. в [документации по виртуальным машинам Azure под управлением Linux](../virtual-machines-linux-cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).


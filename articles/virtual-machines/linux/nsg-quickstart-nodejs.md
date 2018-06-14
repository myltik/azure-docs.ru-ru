---
title: Открытие портов для виртуальной машины Linux с помощью интерфейса командной строки Azure 1.0 | Документация Майкрософт
description: Узнайте, как открыть порт или создать конечную точку для виртуальной машины Linux, используя модель развертывания с помощью Azure Resource Manager и Azure CLI 1.0.
services: virtual-machines-linux
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 05/11/2017
ms.author: iainfou
ms.openlocfilehash: 21b408f4347bf59154407ac005a449f797c67e94
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
ms.locfileid: "34363491"
---
# <a name="opening-ports-and-endpoints-to-a-linux-vm-in-azure-using-the-azure-cli-10"></a>Открытие портов и конечных точек для виртуальной машины Linux в Azure с помощью интерфейса командной строки Azure 1.0
Чтобы открыть порт или создать конечную точку для виртуальной машины в Azure, создайте сетевой фильтр для подсети или сетевого интерфейса виртуальной машины. Эти фильтры, контролирующие входящий и исходящий трафик, добавляются в группу безопасности сети и присоединяются к ресурсу, который будет получать трафик. Давайте используем распространенный пример веб-трафика через порт 80. В этой статье показано, как открыть порт для виртуальной машины с помощью интерфейса командной строки Azure версии 1.0.


## <a name="cli-versions-to-complete-the-task"></a>Версии интерфейса командной строки для выполнения задачи
Вы можете выполнить задачу, используя одну из следующих версий интерфейса командной строки.

- [Azure CLI 1.0](#quick-commands) — интерфейс командной строки для классической модели развертывания и модели развертывания Resource Manager (в этой статье).
- [Azure CLI 2.0](nsg-quickstart.md) — интерфейс командной строки следующего поколения для модели развертывания с помощью Resource Manager.


## <a name="quick-commands"></a>Быстрые команды
Для создания группы безопасности сети и правил нужно установить [интерфейс командной строки Azure версии 1.0](../../cli-install-nodejs.md) и перевести его в режим Resource Manager.

```azurecli
azure config mode arm
```

В следующих примерах замените имена параметров собственными значениями. Примеры имен параметров: *myResourceGroup*, *mystorageaccount* и *myVM*.

Создайте группу безопасности сети, указав собственные имена и расположение. В следующем примере создается группа безопасности сети *myNetworkSecurityGroup* в расположении *eastus*.

```azurecli
azure network nsg create \
    --resource-group myResourceGroup \
    --location eastus \
    --name myNetworkSecurityGroup
```

Добавьте правило, разрешающее HTTP-трафик к вашему веб-серверу (или настройте правило под собственные нужды, например доступ по протоколу SSH или подключение к базе данных). В следующем примере создается правило с именем *myNetworkSecurityGroupRule*. Это правило разрешает TCP-трафик через порт 80:

```azurecli
azure network nsg rule create \
    --resource-group myResourceGroup \
    --nsg-name myNetworkSecurityGroup \
    --name myNetworkSecurityGroupRule \
    --protocol tcp \
    --direction inbound \
    --priority 1000 \
    --destination-port-range 80 \
    --access allow
```

Свяжите группу безопасности сети с сетевым интерфейсом виртуальной машины. В следующем примере существующий сетевой адаптер *myNic* связывается с группой безопасности сети *myNetworkSecurityGroup*:

```azurecli
azure network nic set \
    --resource-group myResourceGroup \
    --network-security-group-name myNetworkSecurityGroup \
    --name myNic
```

Кроме того, группу безопасности сети можно связать с подсетью виртуальной сети, а не только с сетевым интерфейсом на отдельной виртуальной машине. В следующем примере существующий сетевой адаптер *myNic* в виртуальной сети *myVnet* связывается с группой безопасности сети *myNetworkSecurityGroup*:

```azurecli
azure network vnet subnet set \
    --resource-group myResourceGroup \
    --network-security-group-name myNetworkSecurityGroup \
    --vnet-name myVnet --name mySubnet
```

## <a name="more-information-on-network-security-groups"></a>Дополнительная информация о группах безопасности сети
Приведенные здесь быстрые команды позволят настроить трафик, поступающий в виртуальную машину. Группы безопасности сети предоставляют множество полезных функций и всевозможные настройки для управления доступом к ресурсам. [Здесь](../../virtual-network/tutorial-filter-network-traffic-cli.md)вы можете больше прочитать о создании группы безопасности сети и правил ACL.

Группы безопасности сети и правила ACL можно также определять в шаблонах Azure Resource Manager. Узнайте больше о [создании групп безопасности сети с помощью шаблонов](../../virtual-network/template-samples.md).

Если необходимо использовать перенаправление портов, чтобы сопоставить уникальный внешний порт с внутренним портом на своей виртуальной машине, то воспользуйтесь балансировщиком нагрузки и правилами преобразования сетевых адресов (NAT). Например, может потребоваться открыть TCP-порт 8080 для доступа извне и направить трафик на TCP-порт 80 виртуальной машины. Вы можете узнать о [создании балансировщика нагрузки с выходом в Интернет](../../load-balancer/load-balancer-get-started-internet-arm-cli.md).

## <a name="next-steps"></a>Дополнительная информация
В этом примере создано простое правило, разрешающее трафик HTTP. Информацию о создании более детализированных сред можно найти в следующих статьях.

* [Общие сведения об Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md)
* [Группа безопасности сети](../../virtual-network/security-overview.md)
* [Поддержка диспетчера ресурсов Azure для подсистемы балансировки нагрузки](../../load-balancer/load-balancer-arm.md)


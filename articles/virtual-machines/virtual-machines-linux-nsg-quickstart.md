---
title: "Открытие портов для виртуальной машины Linux с помощью интерфейса командной строки Azure 2.0 | Документация Майкрософт"
description: "Узнайте, как открыть порт или создать конечную точку для виртуальной машины Linux, используя модель развертывания с помощью Azure Resource Manager и Azure CLI 2.0."
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
ms.assetid: eef9842b-495a-46cf-99a6-74e49807e74e
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/07/2017
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: 72b2d9142479f9ba0380c5bd2dd82734e370dee7
ms.openlocfilehash: 2e086311a509432454a7b5e188591605d27c6999
ms.lasthandoff: 03/08/2017


---
# <a name="open-ports-and-endpoints-to-a-linux-vm-with-the-azure-cli"></a>Открытие портов и конечных точек для виртуальной машины Linux с помощью интерфейса командной строки Azure
Чтобы открыть порт или создать конечную точку для виртуальной машины в Azure, создайте сетевой фильтр для подсети или сетевого интерфейса виртуальной машины. Эти фильтры, контролирующие входящий и исходящий трафик, добавляются в группу безопасности сети и присоединяются к ресурсу, который будет получать трафик. Давайте используем распространенный пример веб-трафика через порт 80. В этой статье показано, как открыть порт для виртуальной машины с помощью Azure CLI 2.0. Эти действия можно также выполнить с помощью [Azure CLI 1.0](virtual-machines-linux-nsg-quickstart-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).


## <a name="quick-commands"></a>Быстрые команды
Для создания группы безопасности сети и правил необходимо установить [Azure CLI 2.0](/cli/azure/install-az-cli2) и войти в учетную запись Azure с помощью команды [az login](/cli/azure/#login).

В следующих примерах замените имена параметров собственными значениями. Используемые имена параметров: `myResourceGroup`, `myNetworkSecurityGroup` и `myVnet`.

Создайте группу безопасности сети с помощью команды [az network nsg create](/cli/azure/network/nsg#create). В следующем примере создается группа безопасности сети с именем `myNetworkSecurityGroup` в расположении `westus`.

```azurecli
az network nsg create --resource-group myResourceGroup --location westus \
    --name myNetworkSecurityGroup
```

С помощью команды [az network nsg rule create](/cli/azure/network/nsg/rule#create) добавьте правило, разрешающее HTTP-трафик к вашему веб-серверу (или настройте правило под собственные нужды, например доступ по протоколу SSH или подключение к базе данных). В следующем примере мы создадим правило с именем `myNetworkSecurityGroupRule`. Это правило разрешает TCP-трафик через порт 80.

```azurecli
az network nsg rule create --resource-group myResourceGroup \
    --nsg-name myNetworkSecurityGroup --name myNetworkSecurityGroupRule \
    --protocol tcp --direction inbound --priority 1000 \
    --source-address-prefix '*' --source-port-range '*' \
    --destination-address-prefix '*' --destination-port-range 80 --access allow
```

Свяжите группу безопасности сети с сетевым интерфейсом виртуальной машины с помощью команды [az network nic update](/cli/azure/network/nic#update). В следующем примере мы свяжем существующий сетевой адаптера `myNic` с группой безопасности сети `myNetworkSecurityGroup`.

```azurecli
az network nic update --resource-group myResourceGroup --name myNic \
    --network-security-group myNetworkSecurityGroup
```

Кроме того, группу безопасности сети с помощью команды [az network vnet subnet update](/cli/azure/network/vnet/subnet#update) можно связать с подсетью виртуальной сети, а не только с сетевым интерфейсом на отдельной виртуальной машине. В следующем примере мы свяжем существующую подсеть `mySubnet` в виртуальной сети `myVnet` с группой безопасности сети `myNetworkSecurityGroup`.

```azurecli
az network vnet subnet update --resource-group myResourceGroup \
    --vnet-name myVnet --name mySubnet --network-security-group myNetworkSecurityGroup
```

## <a name="more-information-on-network-security-groups"></a>Дополнительная информация о группах безопасности сети
Приведенные здесь быстрые команды позволят настроить трафик, поступающий в виртуальную машину. Группы безопасности сети предоставляют множество полезных функций и всевозможные настройки для управления доступом к ресурсам. [Здесь](../virtual-network/virtual-networks-create-nsg-arm-cli.md)вы можете больше прочитать о создании группы безопасности сети и правил ACL.

Группы безопасности сети и правила ACL можно также определять в шаблонах Azure Resource Manager. Узнайте больше о [создании групп безопасности сети с помощью шаблонов](../virtual-network/virtual-networks-create-nsg-arm-template.md).

Если необходимо использовать перенаправление портов, чтобы сопоставить уникальный внешний порт с внутренним портом на своей виртуальной машине, то воспользуйтесь балансировщиком нагрузки и правилами преобразования сетевых адресов (NAT). Например, может потребоваться открыть TCP-порт 8080 для доступа извне и направить трафик на TCP-порт 80 виртуальной машины. Вы можете узнать о [создании балансировщика нагрузки с выходом в Интернет](../load-balancer/load-balancer-get-started-internet-arm-cli.md).

## <a name="next-steps"></a>Дальнейшие действия
В этом примере создано простое правило, разрешающее трафик HTTP. Информацию о создании более детализированных сред можно найти в следующих статьях.

* [Общие сведения об Azure Resource Manager](../azure-resource-manager/resource-group-overview.md)
* [Группа безопасности сети](../virtual-network/virtual-networks-nsg.md)
* [Поддержка диспетчера ресурсов Azure для подсистемы балансировки нагрузки](../load-balancer/load-balancer-arm.md)



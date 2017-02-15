---
title: "Открытие портов и конечных точек для виртуальной машины Linux в Azure | Документация Майкрософт"
description: "Узнайте, как открыть порт или создать конечную точку для виртуальной машины Linux, используя модель развертывания с помощью Azure Resource Manager и Azure CLI 1.0."
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 10/27/2016
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: 2ca514ec1bbfb5404070de904effd1522fcac892
ms.openlocfilehash: fd43b3a16607ccd383fbf185176d94af5aa97d89


---
# <a name="opening-ports-and-endpoints-to-a-linux-vm-in-azure"></a>Открытие портов и конечных точек для виртуальной машины Linux в Azure
Чтобы открыть порт или создать конечную точку для виртуальной машины в Azure, создайте сетевой фильтр для подсети или сетевого интерфейса виртуальной машины. Эти фильтры, контролирующие входящий и исходящий трафик, добавляются в группу безопасности сети и присоединяются к ресурсу, который будет получать трафик. Давайте используем распространенный пример веб-трафика через порт 80. В этой статье показано, как открыть порт для виртуальной машины с помощью интерфейса командной строки Azure версии 1.0.


## <a name="cli-versions-to-complete-the-task"></a>Версии интерфейса командной строки для выполнения задачи
Вы можете выполнить задачу, используя одну из следующих версий интерфейса командной строки.

- [Azure CLI 1.0](#quick-commands) — интерфейс командной строки для классической модели развертывания и модели развертывания Resource Manager (в этой статье).
- [Azure CLI 2.0 (предварительная версия)](virtual-machines-linux-nsg-quickstart.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) — интерфейс командной строки нового поколения для модели развертывания Resource Manager.


## <a name="quick-commands"></a>Быстрые команды
Для создания группы безопасности сети и правил нужно установить [интерфейс командной строки Azure версии 1.0](../xplat-cli-install.md) и перевести его в режим Resource Manager.

```azurecli
azure config mode arm
```

В следующих примерах замените имена параметров собственными значениями. Используемые имена параметров — `myResourceGroup`, `myNetworkSecurityGroup`, и `myVnet`.

Создайте группу безопасности сети, указав собственные имена и расположение. В следующем примере мы создадим группу безопасности сети с именем `myNetworkSecurityGroup` в расположении `WestUS`.

```azurecli
azure network nsg create --resource-group myResourceGroup --location westus \
    --name myNetworkSecurityGroup
```

Добавьте правило, разрешающее HTTP-трафик к вашему веб-серверу (или настройте правило под собственные нужды, например доступ по протоколу SSH или подключение к базе данных). В следующем примере мы создадим правило с именем `myNetworkSecurityGroupRule`. Это правило разрешает TCP-трафик через порт 80.

```azurecli
azure network nsg rule create --resource-group myResourceGroup \
    --nsg-name myNetworkSecurityGroup --name myNetworkSecurityGroupRule \
    --protocol tcp --direction inbound --priority 1000 \
    --destination-port-range 80 --access allow
```

Свяжите группу безопасности сети с сетевым интерфейсом виртуальной машины. В следующем примере мы свяжем существующий сетевой адаптера `myNic` с группой безопасности сети `myNetworkSecurityGroup`.

```azurecli
azure network nic set --resource-group myResourceGroup \
    --network-security-group-name myNetworkSecurityGroup --name myNic
```

Кроме того, группу безопасности сети можно связать с подсетью виртуальной сети, а не только с сетевым интерфейсом на отдельной виртуальной машине. В следующем примере мы свяжем существующую подсеть `mySubnet` в виртуальной сети `myVnet` с группой безопасности сети `myNetworkSecurityGroup`.

```azurecli
azure network vnet subnet set --resource-group myResourceGroup \
    --network-security-group-name myNetworkSecurityGroup \
    --vnet-name myVnet --name mySubnet
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




<!--HONumber=Dec16_HO2-->



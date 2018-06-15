---
title: Создание виртуальной машины Linux в Azure с несколькими сетевыми картами | Документация Майкрософт
description: Узнайте, как создать виртуальную машину Linux с несколькими сетевыми картами с помощью Azure CLI 2.0 или шаблонов Resource Manager.
services: virtual-machines-linux
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
ms.assetid: 5d2d04d0-fc62-45fa-88b1-61808a2bc691
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/26/2017
ms.author: iainfou
ms.openlocfilehash: 1968222940888c1e5399e257a9694d47adce2e45
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/18/2018
ms.locfileid: "31525188"
---
# <a name="how-to-create-a-linux-virtual-machine-in-azure-with-multiple-network-interface-cards"></a>Как создать виртуальную машину Linux в Azure с несколькими сетевыми картами
Можно создать виртуальную машину (ВМ) в Azure, к которой подключено несколько виртуальных сетевых интерфейсов (сетевых карт). Распространен сценарий, когда разные подсети используются для интерфейсных и внутренних подключений или когда для решения мониторинга либо архивации используется выделенная сеть. В этой статье описаны способы создания виртуальной машины с несколькими сетевыми картами, присоединенными к ней, и добавления или удаления сетевых адаптеров на существующей виртуальной машине. Различные [размеры виртуальных машин](sizes.md) поддерживают разное число сетевых карт, так что выбирайте соответствующий размер виртуальной машины.

В этой статье подробно описывается, как создать виртуальную машину с несколькими сетевыми картами с помощью Azure CLI 2.0. Эти действия можно также выполнить с помощью [Azure CLI 1.0](multiple-nics-nodejs.md).


## <a name="create-supporting-resources"></a>Создание вспомогательных ресурсов
Установите последнюю версию [Azure CLI 2.0](/cli/azure/install-az-cli2) и войдите в систему с учетной записью Azure, выполнив команду [az login](/cli/azure/reference-index#az_login).

В следующих примерах замените имена параметров собственными значениями. Примеры имен параметров: *myResourceGroup*, *mystorageaccount* и *myVM*.

Сначала создайте группу ресурсов с помощью команды [az group create](/cli/azure/group#az_group_create). В следующем примере создается группа ресурсов с именем *myResourceGroup* в расположении *eastus*.

```azurecli
az group create --name myResourceGroup --location eastus
```

Создайте виртуальную сеть с помощью команды [az network vnet create](/cli/azure/network/vnet#az_network_vnet_create). В следующем примере создаются виртуальная сеть *myVnet* и подсеть *mySubnetFrontEnd*.

```azurecli
az network vnet create \
    --resource-group myResourceGroup \
    --name myVnet \
    --address-prefix 192.168.0.0/16 \
    --subnet-name mySubnetFrontEnd \
    --subnet-prefix 192.168.1.0/24
```

Создайте подсеть для внутреннего трафика с помощью команды [az network vnet subnet create](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create). В следующем примере создается подсеть с именем *mySubnetBackEnd*:

```azurecli
az network vnet subnet create \
    --resource-group myResourceGroup \
    --vnet-name myVnet \
    --name mySubnetBackEnd \
    --address-prefix 192.168.2.0/24
```

Создайте группу безопасности сети с помощью команды [az network nsg create](/cli/azure/network/nsg#az_network_nsg_create). В следующем примере создается группа безопасности сети *myNetworkSecurityGroup*.

```azurecli
az network nsg create \
    --resource-group myResourceGroup \
    --name myNetworkSecurityGroup
```

## <a name="create-and-configure-multiple-nics"></a>Создание и настройка нескольких сетевых карт
Создайте две сетевых карты с помощью команды [az network nic create](/cli/azure/network/nic#az_network_nic_create). В следующем примере создаются две сетевые карты — *myNic1* и *myNic2*, — подключенные к группе безопасности сети. Каждая из этих сетевых карт подключена к отдельной подсети.

```azurecli
az network nic create \
    --resource-group myResourceGroup \
    --name myNic1 \
    --vnet-name myVnet \
    --subnet mySubnetFrontEnd \
    --network-security-group myNetworkSecurityGroup
az network nic create \
    --resource-group myResourceGroup \
    --name myNic2 \
    --vnet-name myVnet \
    --subnet mySubnetBackEnd \
    --network-security-group myNetworkSecurityGroup
```

## <a name="create-a-vm-and-attach-the-nics"></a>Создание виртуальной машины и подключение сетевых карт
При создании виртуальной машины укажите сетевые карты, созданные с помощью `--nics`. Необходимо выбрать соответствующий размер виртуальной машины. Для каждой виртуальной машины существуют ограничения на общее количество сетевых карт, которые можно в нее добавить. Прочитайте дополнительные сведения о [размерах виртуальных машин Linux](sizes.md). 

Создайте виртуальную машину с помощью команды [az vm create](/cli/azure/vm#az_vm_create). В следующем примере создается виртуальная машина с именем *myVM*.

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image UbuntuLTS \
    --size Standard_DS3_v2 \
    --admin-username azureuser \
    --generate-ssh-keys \
    --nics myNic1 myNic2
```

Добавьте таблицы маршрутизации для гостевой ОС, выполнив инструкции в статье о [настройке гостевой ОС для нескольких сетевых адаптеров](#configure-guest-os-for- multiple-nics).

## <a name="add-a-nic-to-a-vm"></a>Добавление сетевой карты к виртуальной машине
На предыдущих шагах вы создали виртуальную машину с несколькими сетевыми картами. С помощью Azure CLI 2.0. можно также добавить сетевые карты к существующей виртуальной машине. Различные [размеры виртуальных машин](sizes.md) поддерживают разное число сетевых карт, так что выбирайте соответствующий размер виртуальной машины. При необходимости вы можете [изменить размер виртуальной машины](change-vm-size.md).

Создайте другую сетевую карту с помощью команды [az network nic create](/cli/azure/network/nic#az_network_nic_create). В следующем примере создается сетевая карта *myNic3*, подключенная к внутренней подсети и группе безопасности сети, созданных на предыдущих шагах:

```azurecli
az network nic create \
    --resource-group myResourceGroup \
    --name myNic3 \
    --vnet-name myVnet \
    --subnet mySubnetBackEnd \
    --network-security-group myNetworkSecurityGroup
```

Чтобы добавить сетевую карту к существующей виртуальной машине, сначала отмените распределение виртуальной машины с помощью команды [az vm deallocate](/cli/azure/vm#az_vm_deallocate). В следующем примере отменяется распределение виртуальной машины *myVM*.


```azurecli
az vm deallocate --resource-group myResourceGroup --name myVM
```

Добавьте сетевую карту, выполнив команду [az vm nic add](/cli/azure/vm/nic#az_vm_nic_add). В следующем примере сетевая карта *myNic3* добавляется к виртуальной машине *myVM*:

```azurecli
az vm nic add \
    --resource-group myResourceGroup \
    --vm-name myVM \
    --nics myNic3
```

Запустите виртуальную машину, выполнив команду [az vm start](/cli/azure/vm#az_vm_start).

```azurecli
az vm start --resource-group myResourceGroup --name myVM
```

Добавьте таблицы маршрутизации для гостевой ОС, выполнив инструкции в статье о [настройке гостевой ОС для нескольких сетевых адаптеров](#configure-guest-os-for- multiple-nics).

## <a name="remove-a-nic-from-a-vm"></a>Удаление сетевой карты с виртуальной машины
Чтобы удалить сетевую карту с существующей виртуальной машины, сначала отмените распределение виртуальной машины с помощью команды [az vm deallocate](/cli/azure/vm#az_vm_deallocate). В следующем примере отменяется распределение виртуальной машины *myVM*.

```azurecli
az vm deallocate --resource-group myResourceGroup --name myVM
```

Удалите сетевую карту, выполнив команду [az vm nic remove](/cli/azure/vm/nic#az_vm_nic_remove). В следующем примере удаляется сетевая карта *myNic3* с виртуальной машины *myVM*:

```azurecli
az vm nic remove \
    --resource-group myResourceGroup \
    --vm-name myVM \
    --nics myNic3
```

Запустите виртуальную машину, выполнив команду [az vm start](/cli/azure/vm#az_vm_start).

```azurecli
az vm start --resource-group myResourceGroup --name myVM
```


## <a name="create-multiple-nics-using-resource-manager-templates"></a>Создание нескольких сетевых карт с помощью шаблонов Resource Manager
В шаблонах Azure Resource Manager используются декларативные JSON-файлы для определения среды. Дополнительные сведения см. в [обзоре Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md). Шаблоны Resource Manager дают возможность создать несколько экземпляров ресурса во время развертывания, в том числе создать несколько сетевых карт. Чтобы указать число создаваемых экземпляров, используется объект *copy* .

```json
"copy": {
    "name": "multiplenics"
    "count": "[parameters('count')]"
}
```

Вы можете прочитать дополнительные сведения о [создании нескольких экземпляров с помощью объекта *copy*](../../resource-group-create-multiple.md). 

Можно также использовать `copyIndex()`, чтобы добавить номер к имени ресурса, что позволяет создать `myNic1`, `myNic2` и т. д. Ниже показан пример добавления значения индекса.

```json
"name": "[concat('myNic', copyIndex())]", 
```

Вы можете ознакомиться с полным примером [создания нескольких сетевых карт с помощью шаблонов Resource Manager](../../virtual-network/template-samples.md).

Добавьте таблицы маршрутизации для гостевой ОС, выполнив инструкции в статье о [настройке гостевой ОС для нескольких сетевых адаптеров](#configure-guest-os-for- multiple-nics).

## <a name="configure-guest-os-for-multiple-nics"></a>Настройка гостевой ОС для нескольких сетевых карт
При добавлении нескольких сетевых интерфейсов к виртуальной машине Linux необходимо создать правила маршрутизации. Эти правила позволяют ей отправлять и принимать трафик конкретного сетевого интерфейса. В противном случае виртуальная машина не сможет должным образом обработать трафик, например, из сетевого интерфейса *eth1* из-за определенного маршрута по умолчанию.

Чтобы устранить эту проблемы, необходимо сначала добавить две таблицы маршрутизации в */etc/iproute2/rt_tables* следующим образом:

```bash
echo "200 eth0-rt" >> /etc/iproute2/rt_tables
echo "201 eth1-rt" >> /etc/iproute2/rt_tables
```

Чтобы изменения вступили в силу и применялись во время активации сетевого стека, измените файл */etc/sysconfig/network-scripts/ifcfg-eth0* и */etc/sysconfig/network-scripts/ifcfg-eth1*. Измените строку *NM_CONTROLLED=yes* на *NM_CONTROLLED=no*. Если не выполнить этот шаг, дополнительные правила и маршруты не применяются автоматически.
 
Теперь необходимо расширить таблицы маршрутизации. Предположим, что ми используем следующую конфигурацию:

*Маршрутизация*

```bash
default via 10.0.1.1 dev eth0 proto static metric 100
10.0.1.0/24 dev eth0 proto kernel scope link src 10.0.1.4 metric 100
10.0.1.0/24 dev eth1 proto kernel scope link src 10.0.1.5 metric 101
168.63.129.16 via 10.0.1.1 dev eth0 proto dhcp metric 100
169.254.169.254 via 10.0.1.1 dev eth0 proto dhcp metric 100
```

*Интерфейсы*

```bash
lo: inet 127.0.0.1/8 scope host lo
eth0: inet 10.0.1.4/24 brd 10.0.1.255 scope global eth0    
eth1: inet 10.0.1.5/24 brd 10.0.1.255 scope global eth1
```

Вы можете создать приведенные ниже файлы и добавить к ним правила и маршруты.

- */etc/sysconfig/network-scripts/rule-eth0*

    ```bash
    from 10.0.1.4/32 table eth0-rt
    to 10.0.1.4/32 table eth0-rt
    ```

- */etc/sysconfig/network-scripts/route-eth0*

    ```bash
    10.0.1.0/24 dev eth0 table eth0-rt
    default via 10.0.1.1 dev eth0 table eth0-rt
    ```

- */etc/sysconfig/network-scripts/rule-eth1*

    ```bash
    from 10.0.1.5/32 table eth1-rt
    to 10.0.1.5/32 table eth1-rt
    ```

- */etc/sysconfig/network-scripts/route-eth1*

    ```bash
    10.0.1.0/24 dev eth1 table eth1-rt
    default via 10.0.1.1 dev eth1 table eth1-rt
    ```

Чтобы применить изменения, перезапустите *сетевую* службу следующим образом:

```bash
systemctl restart network
```

Теперь вы должным образом настроили правила маршрутизации и при необходимости можете взаимодействовать с любым интерфейсом.


## <a name="next-steps"></a>Дополнительная информация
Ознакомьтесь с [размерами виртуальных машин Linux](sizes.md), когда будете создавать виртуальную машину с несколькими сетевыми картами. Обратите внимание на максимальное число сетевых карт, поддерживаемых каждым из размеров виртуальной машины. 

---
title: "Создание виртуальной машины Linux с несколькими сетевыми картами с помощью Azure CLI 2.0 | Документация Майкрософт"
description: "Узнайте, как создать виртуальную машину Linux с несколькими сетевыми картами с помощью Azure CLI 2.0 или шаблонов Resource Manager."
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
ms.assetid: 5d2d04d0-fc62-45fa-88b1-61808a2bc691
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/10/2017
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 48abd505b0c8c7b5ef6c6fbf58c45275a82b2da3
ms.lasthandoff: 04/03/2017


---
# <a name="create-a-linux-vm-with-multiple-nics"></a>Создание виртуальной машины Linux с несколькими сетевыми картами
Можно создать виртуальную машину (ВМ) в Azure, к которой подключено несколько виртуальных сетевых интерфейсов (сетевых карт). Распространен сценарий, когда разные подсети используются для интерфейсных и внутренних подключений, или когда для решения мониторинга или архивации используется выделенная сеть. Этой статье описываются быстрые команды для создания виртуальной машины с несколькими сетевыми картами. Чтобы получить дополнительные сведения, в том числе узнать, как создать нескольких сетевых карт в собственных сценариях Bash, узнайте больше о [развертывании виртуальных машин с несколькими сетевыми картами](../../virtual-network/virtual-network-deploy-multinic-arm-cli.md). Различные [размеры виртуальных машин](sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) поддерживают разное число сетевых карт, так что выбирайте соответствующий размер виртуальной машины.

В этой статье подробно описывается, как создать виртуальную машину с несколькими сетевыми картами с помощью Azure CLI 2.0. Эти действия можно также выполнить с помощью [Azure CLI 1.0](multiple-nics-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

> [!WARNING]
> Подключать несколько сетевых карт следует при создании виртуальной машины. Их нельзя добавить в существующую виртуальную машину. Вы можете [создать виртуальную машину на основе исходных виртуальных дисков](copy-vm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) и создать несколько сетевых карт при развертывании этой виртуальной машины.


## <a name="create-supporting-resources"></a>Создание вспомогательных ресурсов
Установите последнюю версию [Azure CLI 2.0](/cli/azure/install-az-cli2) и войдите в систему с учетной записью Azure, выполнив команду [az login](/cli/azure/#login).

В следующих примерах замените имена параметров собственными значениями. Используемые имена параметров — `myResourceGroup`, `mystorageaccount`, и `myVM`.

Сначала создайте группу ресурсов с помощью команды [az group create](/cli/azure/group#create). В следующем примере создается группа ресурсов с именем `myResourceGroup` в расположении `WestUS`:

```azurecli
az group create --name myResourceGroup --location westus
```

Создайте виртуальную сеть с помощью команды [az network vnet create](/cli/azure/network/vnet#create). В следующем примере создается виртуальная сеть `myVnet` и подсеть `mySubnetFrontEnd`.

```azurecli
az network vnet create --resource-group myResourceGroup --name myVnet \
  --address-prefix 192.168.0.0/16 --subnet-name mySubnetFrontEnd --subnet-prefix 192.168.1.0/24
```

Создайте подсеть для внутреннего трафика с помощью команды [az network vnet subnet create](/cli/azure/network/vnet/subnet#create). В следующем примере создается подсеть с именем `mySubnetBackEnd`

```azurecli
az network vnet subnet create --resource-group myResourceGroup --vnet-name myVnet \
    --name mySubnetBackEnd --address-prefix 192.168.2.0/24
```

## <a name="create-and-configure-multiple-nics"></a>Создание и настройка нескольких сетевых карт
Можно прочитать дополнительные сведения о [развертывание нескольких сетевых карт с помощью Azure CLI](../../virtual-network/virtual-network-deploy-multinic-arm-cli.md), включая создание сценария для циклического создания всех сетевых карт.

Обычно создается [группа безопасности сети](../../virtual-network/virtual-networks-nsg.md) или [подсистема балансировки нагрузки](../../load-balancer/load-balancer-overview.md) для управления трафиком и его распределения между виртуальными машинами. Создайте группу безопасности сети с помощью команды [az network nsg create](/cli/azure/network/nsg#create). В следующем примере создается группа безопасности сети с именем `myNetworkSecurityGroup`.

```azurecli
az network nsg create --resource-group myResourceGroup \
  --name myNetworkSecurityGroup
```

Создайте две сетевых карты с помощью команды [az network nic create](/cli/azure/network/nic#create). В следующем примере создаются две сетевые карты, `myNic1` и `myNic2`, подключенные к группе безопасности сети. Каждая из этих сетевых карт подключена к отдельной подсети.

```azurecli
az network nic create --resource-group myResourceGroup --name myNic1 \
  --vnet-name myVnet --subnet mySubnetFrontEnd \
  --network-security-group myNetworkSecurityGroup
az network nic create --resource-group myResourceGroup --name myNic2 \
  --vnet-name myVnet --subnet mySubnetBackEnd \
  --network-security-group myNetworkSecurityGroup
```

## <a name="create-a-vm-and-attach-the-nics"></a>Создание виртуальной машины и подключение сетевых карт
При создании виртуальной машины укажите сетевые карты, созданные с помощью `--nics`. Необходимо выбрать соответствующий размер виртуальной машины. Для каждой виртуальной машины существуют ограничения на общее количество сетевых карт, которые можно в нее добавить. Прочитайте дополнительные сведения о [размерах виртуальных машин Linux](sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 

Создайте виртуальную машину с помощью команды [az vm create](/cli/azure/vm#create). В следующем примере создается виртуальная машина `myVM`, использующая [Управляемые диски Azure](../../storage/storage-managed-disks-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image UbuntuLTS \
    --size Standard_DS2_v2 \
    --admin-username azureuser \
    --ssh-key-value ~/.ssh/id_rsa.pub \
    --nics myNic1 myNic2
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

Вы можете ознакомиться с полным примером [создания нескольких сетевых карт с помощью шаблонов Resource Manager](../../virtual-network/virtual-network-deploy-multinic-arm-template.md).

## <a name="next-steps"></a>Дальнейшие действия
Обязательно ознакомьтесь с [размерами виртуальных машин Linux](sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) , когда будете создавать виртуальную машину с несколькими сетевыми картами. Обратите внимание на максимальное число сетевых карт, поддерживаемых каждым из размеров виртуальной машины. 

Помните, что невозможно добавить дополнительные сетевые карты в существующую виртуальную машины. Все сетевые карты должны быть созданы при развертывании виртуальной машины. Будьте внимательны при планировании развертываний. С самого начала убедитесь в наличии всех необходимых сетевых подключений.



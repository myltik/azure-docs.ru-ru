---
title: "Создание виртуальной машины Linux с несколькими сетевыми картами | Документация Майкрософт"
description: "Узнайте, как создать виртуальную машину Linux с несколькими сетевыми картами с помощью интерфейса командной строки Azure или шаблонов Resource Manager."
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
ms.date: 10/27/2016
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: d4fa4187b25dcbb7cf3b75cb9186b5d245c89227
ms.openlocfilehash: 12da49e49782869153dcecbf6e4ca0ec24fa5960


---
# <a name="creating-a-linux-vm-with-multiple-nics"></a>Создание виртуальной машины Linux с несколькими сетевыми картами
Можно создать виртуальную машину (ВМ) в Azure, к которой подключено несколько виртуальных сетевых интерфейсов (сетевых карт). Распространен сценарий, когда разные подсети используются для интерфейсных и внутренних подключений, или когда для решения мониторинга или архивации используется выделенная сеть. Этой статье описываются быстрые команды для создания виртуальной машины с несколькими сетевыми картами. Чтобы получить дополнительные сведения, в том числе узнать, как создать нескольких сетевых карт в собственных сценариях Bash, узнайте больше о [развертывании виртуальных машин с несколькими сетевыми картами](../virtual-network/virtual-network-deploy-multinic-arm-cli.md). Различные [размеры виртуальных машин](virtual-machines-linux-sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) поддерживают разное число сетевых карт, так что выбирайте соответствующий размер виртуальной машины.

> [!WARNING]
> Подключать несколько сетевых карт следует при создании виртуальной машины. Их нельзя добавить в существующую виртуальную машину. Вы можете [создать виртуальную машины на основе исходных виртуальных дисков](virtual-machines-linux-copy-vm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) и создать несколько сетевых карт при развертывании этой виртуальной машины.
> 
> 

## <a name="quick-commands"></a>Быстрые команды
Войдите в [интерфейс командной строки Azure](../xplat-cli-install.md) и перейдите в режим Resource Manager.

```azurecli
azure config mode arm
```

В следующих примерах замените имена параметров собственными значениями. Используемые имена параметров — `myResourceGroup`, `mystorageaccount`, и `myVM`.

Сначала создайте группу ресурсов. В следующем примере создается группа ресурсов с именем `myResourceGroup` в расположении `WestUS`:

```azurecli
azure group create myResourceGroup -l WestUS
```

Создайте учетную запись хранения для размещения виртуальных машин. В следующем примере создается учетная запись хранения с именем `mystorageaccount`:

```azurecli
azure storage account create mystorageaccount -g myResourceGroup \
    -l WestUS --kind Storage --sku-name PLRS
```

Создайте виртуальную сеть для подключения к этим виртуальным машинам. В следующем примере создается виртуальная сеть с именем `myVnet` и префиксом адреса `192.168.0.0/16`.

```azurecli
azure network vnet create -g myResourceGroup -l WestUS \
    -n myVnet -a 192.168.0.0/16
```

Создайте две подсети виртуальной сети — для интерфейсного трафика и для внутреннего трафика. В следующем примере создаются две подсети с именами `mySubnetFrontEnd` и `mySubnetBackEnd`.

```azurecli
azure network vnet subnet create -g myResourceGroup -e myVnet \
    -n mySubnetFrontEnd -a 192.168.1.0/24
azure network vnet subnet create -g myResourceGroup -e myVnet \
    -n mySubnetBackEnd -a 192.168.2.0/24
```

Создайте две сетевые карты и подключите одну из них к интерфейсной подсети, а другую — к внутренней подсети. В следующем примере создаются две сетевые карты с именами `myNic1` и `myNic2` и подключаются к подсетям.

```azurecli
azure network nic create -g myResourceGroup -l WestUS \
    -n myNic1 -m myVnet -k mySubnetFrontEnd
azure network nic create -g myResourceGroup -l WestUS \
    -n myNic2 -m myVnet -k mySubnetBackEnd
```

Наконец, создайте виртуальную машину, подключив к ней две ранее созданные сетевые карты. В следующем примере создается виртуальная машина с именем `myVM`.

```azurecli
azure vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --location WestUS \
    --os-type linux \
    --nic-names myNic1,myNic2 \
    --vm-size Standard_DS2_v2 \
    --storage-account-name mystorageaccount \
    --image-urn UbuntuLTS \
    --admin-username ops \
    --ssh-publickey-file ~/.ssh/id_rsa.pub
```

## <a name="creating-multiple-nics-using-azure-cli"></a>Создание нескольких сетевых карт с помощью Azure CLI
Если ранее вы создали виртуальную машину с помощью Azure CLI, значит, вы ознакомлены с быстрыми командами. Процесс создания одной или нескольких сетевых карт аналогичен. Можно прочитать дополнительные сведения о [развертывание нескольких сетевых карт с помощью Azure CLI](../virtual-network/virtual-network-deploy-multinic-arm-cli.md), включая создание сценария для циклического создания всех сетевых карт.

В следующем примере создаются две сетевые карты с именами `myNic1` и `myNic2`, каждая из которых подключена к отдельной подсети.

```azurecli
azure network nic create --resource-group myResourceGroup --location WestUS \
    -n myNic1 --subnet-vnet-name myVnet --subnet-name mySubnetFrontEnd
azure network nic create --resource-group myResourceGroup --location WestUS \
    -n myNic2 --subnet-vnet-name myVnet --subnet-name mySubnetBackEnd
```

Обычно также создается [группа безопасности сети](../virtual-network/virtual-networks-nsg.md) или [балансировщик нагрузки](../load-balancer/load-balancer-overview.md) для управления трафиком и его распределения между виртуальными машинами. Опять же, эти команды используются для работы с несколькими сетевыми картами. В следующем примере создается группа безопасности сети с именем `myNetworkSecurityGroup`.

```azurecli
azure network nsg create --resource-group myResourceGroup --location WestUS \
    --name myNetworkSecurityGroup
```

Привяжите сетевые платы к группе безопасности сети с помощью `azure network nic set`. В следующем примере привязываются сетевые карты `myNic1` и `myNic2` к `myNetworkSecurityGroup`.

```azurecli
azure network nic set --resource-group myResourceGroup --name myNic1 \
    --network-security-group-name myNetworkSecurityGroup
azure network nic set --resource-group myResourceGroup --name myNic2 \
    --network-security-group-name myNetworkSecurityGroup
```

Теперь при создании виртуальной машины следует указать несколько сетевых карт. Вместо того, чтобы использовать `--nic-name` для указания одной сетевой карты, используйте `--nic-names` и укажите разделенный запятыми список сетевых карт. Необходимо выбрать соответствующий размер виртуальной машины. Для каждой виртуальной машины существуют ограничения на общее количество сетевых карт, которые можно в нее добавить. Прочитайте дополнительные сведения о [размерах виртуальных машин Linux](virtual-machines-linux-sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). В следующем примере показано, как указать несколько сетевых карт, а затем — размер виртуальной машины, который поддерживает использование нескольких сетевых карт (`Standard_DS2_v2`).

```azurecli
azure vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --location WestUS \
    --os-type linux \
    --nic-names myNic1,myNic2 \
    --vm-size Standard_DS2_v2 \
    --storage-account-name mystorageaccount \
    --image-urn UbuntuLTS \
    --admin-username ops \
    --ssh-publickey-file ~/.ssh/id_rsa.pub
```

## <a name="creating-multiple-nics-using-resource-manager-templates"></a>Создание нескольких сетевых карт с помощью шаблонов Resource Manager
В шаблонах Azure Resource Manager используются декларативные JSON-файлы для определения среды. Дополнительные сведения см. в [обзоре Azure Resource Manager](../azure-resource-manager/resource-group-overview.md). Шаблоны Resource Manager дают возможность создать несколько экземпляров ресурса во время развертывания, в том числе создать несколько сетевых карт. Чтобы указать число создаваемых экземпляров, используется объект *copy* .

```json
"copy": {
    "name": "multiplenics"
    "count": "[parameters('count')]"
}
```

Вы можете прочитать дополнительные сведения о [создании нескольких экземпляров с помощью объекта *copy*](../azure-resource-manager/resource-group-create-multiple.md). 

Можно также использовать `copyIndex()`, чтобы добавить номер к имени ресурса, что позволяет создать `myNic1`, `myNic2` и т. д. Ниже показан пример добавления значения индекса.

```json
"name": "[concat('myNic', copyIndex())]", 
```

Вы можете ознакомиться с полным примером [создания нескольких сетевых карт с помощью шаблонов Resource Manager](../virtual-network/virtual-network-deploy-multinic-arm-template.md).

## <a name="next-steps"></a>Дальнейшие действия
Обязательно ознакомьтесь с [размерами виртуальных машин Linux](virtual-machines-linux-sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) , когда будете создавать виртуальную машину с несколькими сетевыми картами. Обратите внимание на максимальное число сетевых карт, поддерживаемых каждым из размеров виртуальной машины. 

Помните, что невозможно добавить дополнительные сетевые карты в существующую виртуальную машины. Все сетевые карты должны быть созданы при развертывании виртуальной машины. Будьте внимательны при планировании развертываний. С самого начала убедитесь в наличии всех необходимых сетевых подключений.




<!--HONumber=Jan17_HO1-->



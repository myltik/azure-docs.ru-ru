---
title: Создание виртуальной машины Linux в Azure с несколькими сетевыми картами | Документация Майкрософт
description: Узнайте, как создать виртуальную машину Linux с несколькими сетевыми картами с помощью интерфейса командной строки Azure или шаблонов Resource Manager.
services: virtual-machines-linux
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/11/2017
ms.author: iainfou
ms.openlocfilehash: c87d4f07a01fa42ceb2582b974feb4e802b3e03f
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
ms.locfileid: "34365123"
---
# <a name="create-a-linux-virtual-machine-with-multiple-nics-using-the-azure-cli-10"></a>Создание виртуальной машины Linux с несколькими сетевыми картами с помощью Azure CLI 1.0
Можно создать виртуальную машину (ВМ) в Azure, к которой подключено несколько виртуальных сетевых интерфейсов (сетевых карт). Распространен сценарий, когда разные подсети используются для интерфейсных и внутренних подключений или когда для решения мониторинга либо архивации используется выделенная сеть. Этой статье описываются быстрые команды для создания виртуальной машины с несколькими сетевыми картами. Различные [размеры виртуальных машин](sizes.md) поддерживают разное число сетевых карт, так что выбирайте соответствующий размер виртуальной машины.

> [!WARNING]
> Подключать несколько сетевых карт следует при создании виртуальной машины. Их нельзя добавить в существующую виртуальную машину с помощью Azure CLI 1.0. С помощью [Azure CLI 2.0. можно добавить сетевые карты к существующей виртуальной машине](multiple-nics.md). Вы также можете [создать виртуальную машину на основе исходных виртуальных дисков](copy-vm.md), а затем создать несколько сетевых карт при развертывании этой виртуальной машины.


## <a name="cli-versions-to-complete-the-task"></a>Версии интерфейса командной строки для выполнения задачи
Вы можете выполнить задачу, используя одну из следующих версий интерфейса командной строки.

- [Azure CLI 1.0](#create-supporting-resources) — интерфейс командной строки для классической модели развертывания и модели развертывания Resource Manager (в этой статье).
- [Azure CLI 2.0](multiple-nics.md) — интерфейс командной строки следующего поколения для модели развертывания с помощью Resource Manager.


## <a name="create-supporting-resources"></a>Создание вспомогательных ресурсов
Войдите в [интерфейс командной строки Azure](../../cli-install-nodejs.md) и перейдите в режим Resource Manager.

```azurecli
azure config mode arm
```

В следующих примерах замените имена параметров собственными значениями. Примеры имен параметров: *myResourceGroup*, *mystorageaccount* и *myVM*.

Сначала создайте группу ресурсов. В следующем примере создается группа ресурсов с именем *myResourceGroup* в расположении *eastus*.

```azurecli
azure group create myResourceGroup --location eastus
```

Создайте учетную запись хранения для размещения виртуальных машин. В следующем примере создается учетная запись хранения с именем *mystorageaccount*:

```azurecli
azure storage account create mystorageaccount \
    --resource-group myResourceGroup \
    --location eastus \
    --kind Storage \
    --sku-name PLRS
```

Создайте виртуальную сеть для подключения к этим виртуальным машинам. В следующем примере создается виртуальная сеть с именем *myVnet* и префиксом адреса *192.168.0.0/16*:

```azurecli
azure network vnet create \
    --resource-group myResourceGroup \
    --location eastus \
    --name myVnet \
    --address-prefixes 192.168.0.0/16
```

Создайте две подсети виртуальной сети — для интерфейсного трафика и для внутреннего трафика. В следующем примере создаются две подсети с именами *mySubnetFrontEnd* и *mySubnetBackEnd*:

```azurecli
azure network vnet subnet create \
    --resource-group myResourceGroup \
    --location myVnet \
    --name mySubnetFrontEnd \
    --address-prefix 192.168.1.0/24
azure network vnet subnet create \
    --resource-group myResourceGroup \
    --location myVnet \
    --name mySubnetBackEnd \
    --address-prefix 192.168.2.0/24
```

## <a name="create-and-configure-multiple-nics"></a>Создание и настройка нескольких сетевых карт
Можно прочитать дополнительные сведения о [развертывание нескольких сетевых карт с помощью Azure CLI](../../virtual-machines/linux/multiple-nics.md), включая создание сценария для циклического создания всех сетевых карт.

В следующем примере создаются две сетевые карты с именами *myNic1* и *myNic2*, каждая из которых подключена к отдельной подсети:

```azurecli
azure network nic create \
    --resource-group myResourceGroup \
    --location eastus \
    --name myNic1 \
    --subnet-vnet-name myVnet \
    --subnet-name mySubnetFrontEnd
azure network nic create \
    --resource-group myResourceGroup \
    --location eastus \
    --name myNic2 \
    --subnet-vnet-name myVnet \
    --subnet-name mySubnetBackEnd
```

Обычно также создается [группа безопасности сети](../../virtual-network/security-overview.md) или [балансировщик нагрузки](../../load-balancer/load-balancer-overview.md) для управления трафиком и его распределения между виртуальными машинами. В следующем примере создается группа безопасности сети с именем *myNetworkSecurityGroup*.

```azurecli
azure network nsg create \
    --resource-group myResourceGroup \
    --location eastus \
    --name myNetworkSecurityGroup
```

Привяжите сетевые платы к группе безопасности сети с помощью `azure network nic set`. Следующий пример связывает *myNic1* и *myNic2* с *myNetworkSecurityGroup*:

```azurecli
azure network nic set \
    --resource-group myResourceGroup \
    --name myNic1 \
    --network-security-group-name myNetworkSecurityGroup
azure network nic set \
    --resource-group myResourceGroup \
    --name myNic2 \
    --network-security-group-name myNetworkSecurityGroup
```

## <a name="create-a-vm-and-attach-the-nics"></a>Создание виртуальной машины и подключение сетевых карт
Теперь при создании виртуальной машины следует указать несколько сетевых карт. Вместо того, чтобы использовать `--nic-name` для указания одной сетевой карты, используйте `--nic-names` и укажите разделенный запятыми список сетевых карт. Необходимо выбрать соответствующий размер виртуальной машины. Для каждой виртуальной машины существуют ограничения на общее количество сетевых карт, которые можно в нее добавить. Прочитайте дополнительные сведения о [размерах виртуальных машин Linux](sizes.md). В следующем примере показано, как определить несколько сетевых карт и размер виртуальной машины, который поддерживает использование нескольких сетевых карт (*Standard_DS2_v2*):

```azurecli
azure vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --location eastus \
    --os-type linux \
    --nic-names myNic1,myNic2 \
    --vm-size Standard_DS2_v2 \
    --storage-account-name mystorageaccount \
    --image-urn UbuntuLTS \
    --admin-username azureuser \
    --ssh-publickey-file ~/.ssh/id_rsa.pub
```

При добавлении нескольких сетевых интерфейсов к виртуальной машине Linux необходимо создать правила маршрутизации. Эти правила позволяют ей отправлять и принимать трафик конкретного сетевого интерфейса. В противном случае виртуальная машина не сможет должным образом обработать трафик, например, из сетевого интерфейса eth1 из-за определенного маршрута по умолчанию. Сведения по устранению этой проблемы с маршрутизацией см. в разделе [Настройка гостевой ОС для нескольких сетевых карт](multiple-nics.md#configure-guest-os-for-multiple-nics).

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

При добавлении нескольких сетевых интерфейсов к виртуальной машине Linux необходимо создать правила маршрутизации. Эти правила позволяют ей отправлять и принимать трафик конкретного сетевого интерфейса. В противном случае виртуальная машина не сможет должным образом обработать трафик, например, из сетевого интерфейса eth1 из-за определенного маршрута по умолчанию. Сведения по устранению этой проблемы с маршрутизацией см. в разделе [Настройка гостевой ОС для нескольких сетевых карт](multiple-nics.md#configure-guest-os-for-multiple-nics).

## <a name="next-steps"></a>Дополнительная информация
Обязательно ознакомьтесь с [размерами виртуальных машин Linux](sizes.md) , когда будете создавать виртуальную машину с несколькими сетевыми картами. Обратите внимание на максимальное число сетевых карт, поддерживаемых каждым из размеров виртуальной машины. 

Помните, что невозможно добавить дополнительные сетевые карты в существующую виртуальную машины. Все сетевые карты должны быть созданы при развертывании виртуальной машины. Будьте внимательны при планировании развертываний. С самого начала убедитесь в наличии всех необходимых сетевых подключений.


---
title: "Развертывание виртуальных машин Linux в существующей виртуальной сети с помощью Azure CLI 1.0 | Документация Майкрософт"
description: "Как развернуть виртуальную машину Linux в существующей виртуальной сети с помощью Azure CLI 1.0"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: iainfoulds
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.workload: infrastructure
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 05/11/2017
ms.author: iainfou
ms.translationtype: Human Translation
ms.sourcegitcommit: 97fa1d1d4dd81b055d5d3a10b6d812eaa9b86214
ms.openlocfilehash: 767a3f7cadba6b1e71e5a8f5995a9db090e419dd
ms.contentlocale: ru-ru
ms.lasthandoff: 05/11/2017


---

# <a name="how-to-deploy-a-linux-virtual-machine-into-an-existing-azure-virtual-network-with-the-azure-cli-10"></a>Как развернуть виртуальную машину Linux в существующей виртуальной сети Azure с помощью Azure CLI 1.0

В этой статье объясняется, как с помощью Azure CLI 1.0 развернуть виртуальную машину в существующей виртуальной сети. Для этого необходимы следующие компоненты:

- [учетная запись Azure](https://azure.microsoft.com/pricing/free-trial/);
- [файлы открытого и закрытого ключа SSH](mac-create-ssh-keys.md).


## <a name="cli-versions-to-complete-the-task"></a>Версии интерфейса командной строки для выполнения задачи
Вы можете выполнить задачу, используя одну из следующих версий интерфейса командной строки.

- [Azure CLI 1.0](#quick-commands) — интерфейс командной строки для классической модели развертывания и модели развертывания Resource Manager (в этой статье).
- [Azure CLI 2.0](deploy-linux-vm-into-existing-vnet-using-cli.md) — интерфейс командной строки следующего поколения для модели развертывания с помощью Resource Manager.


## <a name="quick-commands"></a>Быстрые команды

Если вам необходимо быстро выполнить задачу, в следующем разделе описаны нужные команды. Более подробные сведения и контекст для каждого этапа можно найти в остальной части документа [начиная отсюда](deploy-linux-vm-into-existing-vnet-using-cli.md#detailed-walkthrough).

Необходимые компоненты: группа ресурсов, виртуальная сеть, группа безопасности сети с входящим трафиком SSH, подсеть. Замените все примеры параметров своими параметрами.

### <a name="deploy-the-vm-into-the-virtual-network-infrastructure"></a>Развертывание виртуальной машины в инфраструктуре виртуальной сети

```azurecli
azure vm create myVM \
    -g myResourceGroup \
    -l eastus \
    -y linux \
    -Q Debian \
    -o mystorageaccount \
    -u myAdminUser \
    -M ~/.ssh/id_rsa.pub \
    -n myVM \
    -F myVNet \
    -j mySubnet \
    -N myVNic
```

## <a name="detailed-walkthrough"></a>Подробное пошаговое руководство

Ресурсы Azure, такие как виртуальные сети и группы безопасности сети, должны быть статическими. Они должны долго храниться и редко развертываться. После развертывания виртуальной сети ее можно повторно использовать при новых развертываниях. Инфраструктура при этом не пострадает. Представьте виртуальную сеть, как сетевой коммутатор в парадигме традиционного оборудования. Вам не нужно настраивать новый коммутатор при каждом развертывании. Если виртуальная сеть правильно настроена, вы можете развертывать в ней новые серверы снова и снова, внося некоторые изменения во время ее использования (если они потребуются).

## <a name="create-the-resource-group"></a>Создание группы ресурсов

Сначала создайте группу ресурсов, чтобы упорядочить все компоненты, которые будут созданы в этом пошаговом руководстве. Дополнительные сведения о группах ресурсов см. в статье [Общие сведения о диспетчере ресурсов Azure](../../azure-resource-manager/resource-group-overview.md).

```azurecli
azure group create myResourceGroup --location eastus
```

## <a name="create-the-vnet"></a>Создание виртуальной сети

Сначала нужно создать виртуальную сеть для запуска виртуальных машин. Для этого пошагового руководства виртуальная сеть содержит одну подсеть. Дополнительные сведения о виртуальных сетях Azure см. в статье [Создание виртуальной сети с помощью интерфейса командной строки Azure](../../virtual-network/virtual-networks-create-vnet-arm-cli.md).

```azurecli
azure network vnet create myVNet \
    --resource-group myResourceGroup \
    --address-prefixes 10.10.0.0/24 \
    --location eastus
```

## <a name="create-the-network-security-group"></a>Создание группы безопасности сети

Подсеть создается за существующей группой безопасности сети, поэтому, прежде чем создавать подсеть, создайте группу безопасности сети. На уровне сети группы безопасности сети Azure эквивалентны брандмауэру. Дополнительные сведения о группах безопасности сети см. в статье [Создание групп безопасности сети с помощью Azure CLI 2.0](../../virtual-network/virtual-networks-create-nsg-arm-cli.md).

```azurecli
azure network nsg create myNetworkSecurityGroup \
    --resource-group myResourceGroup \
    --location eastus
```

## <a name="add-an-inbound-ssh-allow-rule"></a>Добавление правила, разрешающего входящий трафик по протоколу SSH

Виртуальной машине требуется доступ к Интернету, поэтому нужно создать правило, разрешающее передачу входящего трафика в сети через порт 22 на виртуальную машину.

```azurecli
azure network nsg rule create inboundSSH \
    --resource-group myResourceGroup \
    --nsg-name myNSG \
    --access Allow \
    --protocol Tcp \
    --direction Inbound \
    --priority 100 \
    --source-address-prefix Internet \
    --source-port-range 22 \
    --destination-address-prefix 10.10.0.0/24 \
    --destination-port-range 22
```

## <a name="add-a-subnet-to-the-vnet"></a>Добавление подсети в виртуальную сеть

Виртуальные машины в виртуальной сети должны быть расположены в подсети. Каждая виртуальная сеть может содержать несколько подсетей. Создайте подсеть и свяжите ее с группой безопасности сети.

```azurecli
azure network vnet subnet create mySubNet \
    --resource-group myResourceGroup \
    --vnet-name myVNet \
    --address-prefix 10.10.0.0/26 \
    --network-security-group-name myNetworkSecurityGroup
```

Теперь подсеть добавлена в виртуальную сеть и связана с группой безопасности сети и ее правилом.


## <a name="add-a-vnic-to-the-subnet"></a>Добавление виртуальной сетевой карты в подсеть

Виртуальные сетевые карты (VNic) имеют большое значение, так как их можно повторно использовать, подключая к разным виртуальным машинам. Это позволяет использовать виртуальную сетевую карту как статический ресурс, тогда как виртуальные машины могут быть временными. Создайте виртуальную сетевую карту и свяжите ее с подсетью, созданной на предыдущем шаге.

```azurecli
azure network nic create myVNic \
    --resource-group myResourceGroup \
    --location eastus \
    ---subnet-vnet-name myVNet \
    --subnet-name mySubNet
```

## <a name="deploy-the-vm-into-the-vnet-and-nsg"></a>Развертывание виртуальной машины в виртуальной сети и группе безопасности сети

Теперь у вас есть виртуальная сеть, подсеть в виртуальной сети и группа безопасности сети для защиты подсети путем блокирования всего входящего трафика, кроме трафика, поступающего через порт 22 по протоколу SSH. Теперь виртуальную машину можно развернуть в этой существующей сетевой инфраструктуре.

С помощью Azure CLI и команды `azure vm create` виртуальную машину Linux можно развернуть в существующей группе ресурсов Azure, виртуальной сети, подсети и виртуальной сетевой карте. Дополнительные сведения о развертывании всех компонентов виртуальной машины с помощью интерфейса командной строки см. в статье [Создание полной среды Linux с помощью интерфейса командной строки Azure](create-cli-complete.md).

```azurecli
azure vm create myVM \
    --resource-group myResourceGroup \
    --location eastus \
    --os-type linux \
    --image-urn Debian \
    --storage-account-name mystorageaccount \
    --admin-username myAdminUser \
    --ssh-publickey-file ~/.ssh/id_rsa.pub \
    --vnet-name myVNet \
    --vnet-subnet-name mySubnet \
    --nic-name myVNic
```

Используя флаги интерфейса командной строки для вызова существующих ресурсов, дайте среде Azure инструкцию развернуть виртуальную машину в существующей сети. После развертывания виртуальную сеть и подсеть можно оставить в качестве статических или постоянных ресурсов в регионе Azure.  

## <a name="next-steps"></a>Дальнейшие действия

* [Развертывание виртуальных машин и управление ими с помощью шаблонов диспетчера ресурсов Azure и интерфейса командной строки Azure](../windows/cli-deploy-templates.md)
* [Создание полной среды Linux с помощью интерфейса командной строки Azure](create-cli-complete.md)
* [Создание виртуальной машины Linux с помощью шаблона Azure](create-ssh-secured-vm-from-template.md)


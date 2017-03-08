---
title: "Развертывание виртуальной машины Linux в существующей виртуальной сети с помощью Azure CLI 2.0 | Документация Майкрософт"
description: "Узнайте, как развернуть виртуальную машину Linux в существующей виртуальной сети с помощью Azure CLI 2.0"
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
ms.date: 01/31/2017
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: 67d4fee2fc59651903d4c02d1fce84c7b81e5da1
ms.openlocfilehash: c56ad780a1d67102d23c84a18c712ae48cec1eb6
ms.lasthandoff: 02/27/2017


---

# <a name="deploy-a-linux-vm-into-an-existing-virtual-network"></a>Развертывание виртуальной машины Linux в существующей виртуальной сети

В этой статье показано, как с помощью Azure CLI 2.0 развернуть виртуальную машину в существующей виртуальной сети. Для этого необходимы следующие компоненты:

- [учетная запись Azure](https://azure.microsoft.com/pricing/free-trial/);
- [файлы открытого и закрытого ключа SSH](virtual-machines-linux-mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Эти действия можно также выполнить с помощью [Azure CLI 1.0](virtual-machines-linux-deploy-linux-vm-into-existing-vnet-using-cli-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).


## <a name="quick-commands"></a>Быстрые команды
Если вам необходимо быстро выполнить задачу, в следующем разделе описаны нужные команды. Более подробные сведения и контекст для каждого этапа можно найти в остальной части документа [начиная отсюда](#detailed-walkthrough).

Для создания этой настраиваемой среды необходимо установить последнюю версию [Azure CLI 2.0](/cli/azure/install-az-cli2) и войти в учетную запись Azure с помощью команды [az login](/cli/azure/#login).

В следующих примерах замените имена параметров собственными значениями. Используемые имена параметров: `myResourceGroup`, `myVnet` и `myVM`.

**Предварительные требования:** группа ресурсов Azure, виртуальная сеть и подсеть, группа безопасности сети с разрешенными входящими подключениями SSH и виртуальная сетевая карта.

### <a name="deploy-the-vm-into-the-virtual-network-infrastructure"></a>Развертывание виртуальной машины в инфраструктуре виртуальной сети

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image Debian \
    --admin-username azureuser \
    --ssh-key-value ~/.ssh/id_rsa.pub \
    --nics myNic
```

## <a name="detailed-walkthrough"></a>Подробное пошаговое руководство

Рекомендуется, чтобы ресурсы Azure, такие как виртуальные сети и группы безопасности сети, были статическими, хранились в течение продолжительного времени и редко развертывались. После развертывания виртуальной сети ее можно повторно использовать при новых развертываниях. Инфраструктура при этом не пострадает. Воспринимайте виртуальную сеть в качестве традиционного аппаратного сетевого коммутатора. Вам не понадобится настраивать новый аппаратный коммутатор с каждым развертыванием. Если виртуальная сеть настроена правильно, то мы можем развертывать в ней новые виртуальные машины снова и снова, внося малочисленные необходимые изменения во время ее использования (если они нужны).

Для создания этой настраиваемой среды необходимо установить последнюю версию [Azure CLI 2.0](/cli/azure/install-az-cli2) и войти в учетную запись Azure с помощью команды [az login](/cli/azure/#login).

В следующих примерах замените имена параметров собственными значениями. Используемые имена параметров: `myResourceGroup`, `myVnet` и `myVM`.

## <a name="create-the-resource-group"></a>Создание группы ресурсов

Сначала мы создадим группу ресурсов Azure для организации всех компонентов, которые будут созданы в этом пошаговом руководстве. Дополнительные сведения о группах ресурсов см. в статье [Общие сведения об Azure Resource Manager](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Создайте группу ресурсов с помощью команды [az group create](/cli/azure/group#create). В следующем примере создается группа ресурсов с именем `myResourceGroup` в расположении `westus`:

```azurecli
az group create \
    --name myResourceGroup \
    --location westus
```

## <a name="create-the-virtual-network"></a>Создание виртуальной сети

Давайте создадим виртуальную сеть Azure для запуска виртуальных машин. Дополнительные сведения о виртуальных сетях см. в статье [Создание виртуальной сети с помощью интерфейса командной строки Azure](../virtual-network/virtual-networks-create-vnet-arm-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Создайте виртуальную сеть с помощью команды [az network vnet create](/cli/azure/network/vnet#create). В следующем примере создается виртуальная сеть `myVnet` и подсеть `mySubnet`.

```azurecli
az network vnet create \
    --resource-group myResourceGroup \
    --location westus \
    --name myVnet \
    --address-prefix 10.10.0.0/16 \
    --subnet-name mySubnet \
    --subnet-prefix 10.10.1.0/24
```

## <a name="create-the-network-security-group"></a>Создание группы безопасности сети

На уровне сети группы безопасности сети Azure эквивалентны брандмауэру. Дополнительные сведения о группах безопасности сети см. в статье [Создание групп безопасности сети с помощью интерфейса командной строки Azure](../virtual-network/virtual-networks-create-nsg-arm-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Создайте группу безопасности сети с помощью команды [az network nsg create](/cli/azure/network/nsg#create). В следующем примере создается группа безопасности сети с именем `myNetworkSecurityGroup`.

```azurecli
az network nsg create \
    --resource-group myResourceGroup \
    --location westus \
    --name myNetworkSecurityGroup
```

## <a name="add-an-inbound-ssh-allow-rule"></a>Добавление правила, разрешающего входящий трафик по протоколу SSH

Виртуальной машине Linux требуется доступ к Интернету, поэтому требуется правило, разрешающее передачу входящего трафика в сети через порт 22 на виртуальную машину Linux. Добавьте в группу безопасности сети правило входящего трафика с помощью команды [az network nsg rule create](/cli/azure/network/nsg/rule#create). В следующем примере создается правило `myNetworkSecurityGroupRuleSSH`.

```azurecli
az network nsg rule create \
    --resource-group myResourceGroup \
    --nsg-name myNetworkSecurityGroup \
    --name myNetworkSecurityGroupRuleSSH \
    --protocol tcp \
    --direction inbound \
    --priority 1000 \
    --source-address-prefix '*' \
    --source-port-range '*' \
    --destination-address-prefix '*' \
    --destination-port-range 22 \
    --access allow
```

## <a name="attach-the-subnet-to-the-network-security-group"></a>Подключение подсети к группе безопасности сети

Правила группы безопасности сети можно применять к подсети или определенному виртуальному сетевому интерфейсу. Давайте подключим группу безопасности сети к нашей подсети. Подключите подсеть к группе безопасности сети с помощью команды [az network vnet subnet update](/cli/azure/network/vnet/subnet#update).

```azurecli
az network vnet subnet update \
    --resource-group myResourceGroup \
    --vnet-name myVnet \
    --name mySubnet \
    --network-security-group myNetworkSecurityGroup
```

## <a name="add-a-virtual-network-interface-card-to-the-subnet"></a>Добавление виртуальной сетевой карты в подсеть

Виртуальные сетевые карты (VNic) имеют большое значение, так как их можно повторно использовать, подключая к разным виртуальным машинам. Это позволяет использовать виртуальную сетевую карту как статический ресурс, тогда как виртуальные машины могут быть временными. Создайте виртуальную сетевую карту и свяжите ее с подсетью, выполнив команду [az network nic create](/cli/azure/network/nic#create). В следующем примере создается виртуальная сетевая карта `myNic`.

```azurecli
az network nic create \
    --resource-group myResourceGroup \
    --location westus \
    --name myNic \
    --vnet-name myVnet \
    --subnet mySubnet
```

## <a name="deploy-the-vm-into-the-virtual-network-infrastructure"></a>Развертывание виртуальной машины в инфраструктуре виртуальной сети

Теперь у нас есть виртуальная сеть, подсеть и группа безопасности сети, выступающая в качестве брандмауэра для защиты подсети путем блокирования всего входящего трафика, кроме трафика, поступающего через порт 22 по протоколу SSH. Теперь виртуальную машину можно развернуть в этой существующей сетевой инфраструктуре.

Создайте виртуальную машину с помощью команды [az vm create](/cli/azure/vm#create). Дополнительные сведения о флагах, используемых в Azure CLI 2.0 для полного развертывания виртуальной машины, см. в статье [Создание полной среды Linux с помощью Azure CLI](virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

В следующем примере создается виртуальная машина с помощью управляемых дисков. Эти диски полностью контролируются платформой Azure и не требуют никакой подготовки или хранилища. Дополнительные сведения об управляемых дисках Azure см. в [этой статье](../storage/storage-managed-disks-overview.md). Если вы хотите использовать неуправляемые диски, см. дополнительное примечание ниже.

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image Debian \
    --admin-username azureuser \
    --ssh-key-value ~/.ssh/id_rsa.pub \
    --nics myNic
```

Если вы используете управляемые диски, пропустите этот шаг. Если вы хотите использовать неуправляемые диски, необходимо добавить следующие дополнительные параметры к команде, чтобы создать неуправляемые диски в учетной записи хранения с именем `mystorageaccount`: 

```azurecli
    --use-unmanaged-disk \
    --storage-account mystorageaccount
```

Используя флаги командной строки для вызова существующих ресурсов, мы укажем среде Azure развернуть виртуальную машину в существующей сети. Еще раз отметим, что после развертывания виртуальную сеть и подсеть можно оставить в качестве статических или постоянных ресурсов в регионе Azure. В этом примере мы не создали общедоступный IP-адрес и не назначили его виртуальной сетевой карте, поэтому данная виртуальная машина не является общедоступной через Интернет. Дополнительные сведения см. в разделе [Развертывание виртуальной машины со статическим общедоступным IP-адресом с использованием портала Azure CLI](../virtual-network/virtual-network-deploy-static-pip-arm-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="next-steps"></a>Дальнейшие действия
Дополнительные сведения о способах создания виртуальных машин в Azure доступны в следующих материалах:

* [Развертывание виртуальных машин и управление ими с помощью шаблонов диспетчера ресурсов Azure и интерфейса командной строки Azure](virtual-machines-linux-cli-deploy-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Создание полной среды Linux с помощью интерфейса командной строки Azure](virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Создание виртуальной машины Linux с помощью шаблона Azure](virtual-machines-linux-create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)


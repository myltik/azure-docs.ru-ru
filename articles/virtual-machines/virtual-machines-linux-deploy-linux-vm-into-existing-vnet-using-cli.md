---
title: "Развертывание виртуальной машины Linux в существующей виртуальной сети Azure с помощью интерфейса командной строки | Документация Майкрософт"
description: "Разверните виртуальную машину Linux в существующей виртуальной сети с помощью интерфейса командной строки."
services: virtual-machines-linux
documentationcenter: virtual-machines
author: vlivech
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.workload: infrastructure
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 11/30/2016
ms.author: v-livech
translationtype: Human Translation
ms.sourcegitcommit: 2fad20978f40150ef9f1cb44054da2ba66848bda
ms.openlocfilehash: 613ce9b27bc26643b2f46c490d7f550b370df998


---

# <a name="deploy-a-linux-vm-into-an-existing-vnet--nsg-using-the-cli"></a>Развертывание виртуальной машины Linux в существующей виртуальной сети и группе безопасности сети с помощью интерфейса командной строки

В этой статье показано, как развернуть виртуальную машину в существующей виртуальной сети, защищенный с помощью существующей группы безопасности сети, с использованием флагов интерфейса командной строки.  Для этого необходимы следующие компоненты:

- [учетная запись Azure](https://azure.microsoft.com/pricing/free-trial/);

- [файлы открытого и закрытого ключа SSH](virtual-machines-linux-mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="quick-commands"></a>Быстрые команды

Замените все примеры параметров своими параметрами.

### <a name="create-the-resource-group"></a>Создание группы ресурсов

```azurecli
azure group create myResourceGroup \
-l westus
```

### <a name="create-the-vnet"></a>Создание виртуальной сети

```azurecli
azure network vnet create myVNet \
-g myResourceGroup \
-a 10.10.0.0/24 \
-l westus
```

### <a name="create-the-nsg"></a>Создание группы безопасности сети

```azurecli
azure network nsg create myNSG \
-g myResourceGroup \
-l westus
```

### <a name="add-an-inbound-ssh-allow-rule"></a>Добавление правила, разрешающего входящий трафик по протоколу SSH

```azurecli
azure network nsg rule create inboundSSH \
-g myResourceGroup \
-a myNSG \
-c Allow \
-p Tcp \
-r Inbound \
-y 100 \
-f Internet \
-o 22 \
-e 10.10.0.0/24 \
-u 22
```

### <a name="add-a-subnet-to-the-vnet"></a>Добавление подсети в виртуальную сеть

```azurecli
azure network vnet subnet create mySubNet \
-g myResourceGroup \
-e myVNet \
-a 10.10.0.0/26 \
-o myNSG
```

### <a name="add-a-vnic-to-the-subnet"></a>Добавление виртуальной сетевой карты в подсеть

```azurecli
azure network nic create myVNic \
-g myResourceGroup \
-l westus \
-m myVNet \
-k mySubNet
```

### <a name="deploy-the-vm-into-the-vnet-nsg-and-connect-the-vnic"></a>Развертывание виртуальной машины в виртуальной сети и группе безопасности сети и подключение виртуальной сетевой карты

```azurecli
azure vm create myVM \
-g myResourceGroup \
-l westus \
-y linux \
-Q Debian \
-o myStorageAcct \
-u myAdminUser \
-M ~/.ssh/id_rsa.pub \
-n myVM \
-F myVNet \
-j mySubnet \
-N myVNic
```

## <a name="detailed-walkthrough"></a>Подробное пошаговое руководство

Мы рекомендуем, чтобы ресурсы Azure, такие как виртуальные сети и группы безопасности сети, были статическими, хранились в течение продолжительного времени и редко развертывались.  После развертывания виртуальной сети ее можно повторно использовать при новых развертываниях. Инфраструктура при этом не пострадает.  Воспринимайте виртуальную сеть в качестве традиционного аппаратного сетевого коммутатора. Вам не понадобится настраивать новый аппаратный коммутатор с каждым развертыванием.  Если виртуальная сеть правильно настроена, мы можем развертывать в ней новые серверы снова и снова, внося некоторые изменения во время ее использования (если они нужны).

## <a name="create-the-resource-group"></a>Создание группы ресурсов

Сначала мы создадим группу ресурсов для организации всех компонентов, которые будут созданы в этом пошаговом руководстве.  Дополнительные сведения о группах ресурсов см. в статье [Общие сведения об Azure Resource Manager](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

```azurecli
azure group create myResourceGroup \
--location westus
```

## <a name="create-the-vnet"></a>Создание виртуальной сети

Сначала нужно создать виртуальную сеть для запуска виртуальных машин.  Для этого пошагового руководства виртуальная сеть содержит одну подсеть.  Дополнительные сведения о виртуальных сетях Azure см. в статье [Создание виртуальной сети с помощью интерфейса командной строки Azure](../virtual-network/virtual-networks-create-vnet-arm-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

```azurecli
azure network vnet create myVNet \
--resource-group myResourceGroup \
--address-prefixes 10.10.0.0/24 \
--location westus
```

## <a name="create-the-nsg"></a>Создание группы безопасности сети

Подсеть создается за существующей группой безопасности сети, поэтому ее нужно создать перед подсетью.  На уровне сети группы безопасности сети Azure эквивалентны брандмауэру.  Дополнительные сведения о группах безопасности сети Azure см. в статье [Создание групп безопасности сети с помощью интерфейса командной строки Azure](../virtual-network/virtual-networks-create-nsg-arm-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

```azurecli
azure network nsg create myNSG \
--resource-group myResourceGroup \
--location westus
```

## <a name="add-an-inbound-ssh-allow-rule"></a>Добавление правила, разрешающего входящий трафик по протоколу SSH

Виртуальной машине Linux требуется доступ к Интернету, поэтому требуется правило, разрешающее передачу входящего трафика в сети через порт 22 на виртуальную машину Linux.

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

Виртуальные машины в виртуальной сети должны быть расположены в подсети.  Каждая виртуальная сеть может содержать несколько подсетей.  Создайте подсеть и свяжите ее с группой безопасности сети, чтобы добавить брандмауэр в подсеть.

```azurecli
azure network vnet subnet create mySubNet \
--resource-group myResourceGroup \
--vnet-name myVNet \
--address-prefix 10.10.0.0/26 \
--network-security-group-name myNSG
```

Теперь подсеть добавлена в виртуальную сеть и связана с группой безопасности сети и ее правилом.


## <a name="add-a-vnic-to-the-subnet"></a>Добавление виртуальной сетевой карты в подсеть

Виртуальные сетевые карты играют важную роль, так как их можно использовать повторно, подключая к разным виртуальным машинам. Таким образом виртуальная сетевая карта является статическим ресурсом, а виртуальные машины могут быть временными.  Создайте виртуальную сетевую карту и свяжите ее с подсетью, созданной на предыдущем шаге.

```azurecli
azure network nic create myVNic \
-g myResourceGroup \
-l westus \
-m myVNet \
-k mySubNet
```

## <a name="deploy-the-vm-into-the-vnet-and-nsg"></a>Развертывание виртуальной машины в виртуальной сети и группе безопасности сети

Теперь у нас есть виртуальная сеть, подсеть в этой виртуальной сети и группа безопасности сети, выступающая в качестве брандмауэра для защиты подсети путем блокирования всего входящего трафика, кроме трафика, поступающего через порт 22 по протоколу SSH.  Теперь виртуальную машину можно развернуть в этой существующей сетевой инфраструктуре.

С помощью Azure CLI и команды `azure vm create` виртуальную машину Linux можно развернуть в существующей группе ресурсов Azure, виртуальной сети, подсети и виртуальной сетевой карте.  Дополнительные сведения о развертывании всех компонентов виртуальной машины с помощью интерфейса командной строки см. в статье [Создание полной среды Linux с помощью интерфейса командной строки Azure](virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

```azurecli
azure vm create myVM \
--resource-group myResourceGroup \
--location westus \
--os-type linux \
--image-urn Debian \
--storage-account-name mystorageaccount \
--admin-username myAdminUser \
--ssh-publickey-file ~/.ssh/id_rsa.pub \
--vnet-name myVNet \
--vnet-subnet-name mySubnet \
--nic-name myVNic
```

Используя флаги командной строки для вызова существующих ресурсов, мы укажем среде Azure развернуть виртуальную машину в существующей сети.  После развертывания виртуальную сеть и подсеть можно оставить в качестве статических или постоянных ресурсов в регионе Azure для повторного развертывания.  

## <a name="next-steps"></a>Дальнейшие действия

* [Развертывание виртуальных машин и управление ими с помощью шаблонов диспетчера ресурсов Azure и интерфейса командной строки Azure](virtual-machines-linux-cli-deploy-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Создание полной среды Linux с помощью интерфейса командной строки Azure](virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Создание виртуальной машины Linux с помощью шаблона Azure](virtual-machines-linux-create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)



<!--HONumber=Nov16_HO5-->



---
title: "Развертывание виртуальных машин Linux в существующих сетях с помощью Azure CLI | Документация Майкрософт"
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
ms.date: 12/05/2016
ms.author: v-livech
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: d3f8f35ee3a5b294e77f3bad50edd3d1db221c9c
ms.lasthandoff: 04/03/2017


---

# <a name="deploy-a-linux-vm-into-an-existing-azure-virtual-network-using-the-cli"></a>Развертывание виртуальной машины Linux в существующей виртуальной сети Azure с помощью интерфейса командной строки

В этой статье показано, как развернуть виртуальную машину в существующей виртуальной сети с использованием флагов интерфейса командной строки.  Для этого необходимы следующие компоненты:

- [учетная запись Azure](https://azure.microsoft.com/pricing/free-trial/);
- [файлы открытого и закрытого ключа SSH](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).


## <a name="cli-versions-to-complete-the-task"></a>Версии интерфейса командной строки для выполнения задачи
Вы можете выполнить задачу, используя одну из следующих версий интерфейса командной строки.

- [Azure CLI 1.0](#quick-commands) — интерфейс командной строки для классической модели развертывания и модели развертывания Resource Manager (в этой статье).
- [Azure CLI 2.0](deploy-linux-vm-into-existing-vnet-using-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) — интерфейс командной строки следующего поколения для модели развертывания с помощью Resource Manager.


## <a name="quick-commands"></a>Быстрые команды

Если вам необходимо быстро выполнить задачу, в следующем разделе описаны нужные команды. Более подробные сведения и контекст для каждого этапа можно найти в остальной части документа [начиная отсюда](deploy-linux-vm-into-existing-vnet-using-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#detailed-walkthrough).

Необходимые компоненты: группа ресурсов, виртуальная сеть, группа безопасности сети с входящим трафиком SSH, подсеть. Замените все примеры параметров своими параметрами.

### <a name="deploy-the-vm-into-the-virtual-network-infrastructure"></a>Развертывание виртуальной машины в инфраструктуре виртуальной сети

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

Мы рекомендуем, чтобы ресурсы Azure, такие как виртуальные сети и группы безопасности сети, были статическими, хранились в течение продолжительного времени и редко развертывались.  После развертывания виртуальной сети ее можно повторно использовать при новых развертываниях. Инфраструктура при этом не пострадает.  Представьте виртуальную сеть, как сетевой коммутатор в парадигме традиционного оборудования. Вам не нужно настраивать новый коммутатор при каждом развертывании.  Если виртуальная сеть правильно настроена, мы можем развертывать в ней новые серверы снова и снова, внося некоторые изменения во время ее использования (если они нужны).

## <a name="create-the-resource-group"></a>Создание группы ресурсов

Сначала мы создадим группу ресурсов для организации всех компонентов, создаваемых в этом пошаговом руководстве.  Дополнительные сведения о группах ресурсов см. в статье [Общие сведения об Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

```azurecli
azure group create myResourceGroup \
--location westus
```

## <a name="create-the-vnet"></a>Создание виртуальной сети

Сначала нужно создать виртуальную сеть для запуска виртуальных машин.  Для этого пошагового руководства виртуальная сеть содержит одну подсеть.  Дополнительные сведения о виртуальных сетях Azure см. в статье [Создание виртуальной сети с помощью интерфейса командной строки Azure](../../virtual-network/virtual-networks-create-vnet-arm-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

```azurecli
azure network vnet create myVNet \
--resource-group myResourceGroup \
--address-prefixes 10.10.0.0/24 \
--location westus
```

## <a name="create-the-nsg"></a>Создание группы безопасности сети

Подсеть создается за существующей группой безопасности сети, поэтому ее нужно создать перед подсетью.  На уровне сети группы безопасности сети Azure эквивалентны брандмауэру.  Дополнительные сведения о группах безопасности сети Azure см. в статье [Создание групп безопасности сети с помощью интерфейса командной строки Azure](../../virtual-network/virtual-networks-create-nsg-arm-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

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

С помощью Azure CLI и команды `azure vm create` виртуальную машину Linux можно развернуть в существующей группе ресурсов Azure, виртуальной сети, подсети и виртуальной сетевой карте.  Дополнительные сведения о развертывании всех компонентов виртуальной машины с помощью интерфейса командной строки см. в статье [Создание полной среды Linux с помощью интерфейса командной строки Azure](create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

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

* [Развертывание виртуальных машин и управление ими с помощью шаблонов диспетчера ресурсов Azure и интерфейса командной строки Azure](../windows/cli-deploy-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Создание полной среды Linux с помощью интерфейса командной строки Azure](create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Создание виртуальной машины Linux с помощью шаблона Azure](create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)


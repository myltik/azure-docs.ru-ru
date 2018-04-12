---
title: Использование внутренней службы DNS для разрешения имен виртуальных машин в Azure | Документация Майкрософт
description: Использование внутренней службы DNS для разрешения имен виртуальных машин в Azure.
services: virtual-machines-linux
documentationcenter: ''
author: vlivech
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 12/05/2016
ms.author: v-livech
ms.openlocfilehash: 73ecbb5bfcc99eb4507cac24b0539a04c3d8f521
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/06/2018
---
# <a name="using-internal-dns-for-vm-name-resolution-on-azure"></a>Использование внутренней службы DNS для разрешения имен виртуальных машин в Azure

В этой статье показано, как задать статические внутренние имена DNS для виртуальных машин Linux с использованием виртуальных сетевых карт и имен меток DNS. Статические имена DNS используются для постоянных инфраструктурных служб, таких как сервер сборки Jenkins, который используется для этого поддержания документа, или сервер Git.

Для этого необходимы следующие компоненты:

* [учетная запись Azure](https://azure.microsoft.com/pricing/free-trial/);
* [файлы открытого и закрытого ключа SSH](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).


## <a name="cli-versions-to-complete-the-task"></a>Версии интерфейса командной строки для выполнения задачи
Вы можете выполнить задачу, используя одну из следующих версий интерфейса командной строки.

- [Azure CLI 1.0](#quick-commands) — интерфейс командной строки для классической модели развертывания и модели развертывания Resource Manager (в этой статье).
- [Azure CLI 2.0](static-dns-name-resolution-for-linux-on-azure.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) — интерфейс командной строки следующего поколения для модели развертывания с помощью Resource Manager.


## <a name="quick-commands"></a>Быстрые команды

Если вам необходимо быстро выполнить задачу, в следующем разделе описаны нужные команды. Более подробные сведения и контекст для каждого этапа можно найти в остальной части документа [начиная отсюда](#detailed-walkthrough).  

Предварительные требования: группа ресурсов, виртуальная сеть, группа безопасности сети с входящим трафиком SSH, подсеть.

### <a name="create-a-vnic-with-a-static-internal-dns-name"></a>Создание виртуального сетевого адаптера со статическим внутренним именем DNS

Флаг `-r` интерфейса командной строки используется для установки метки DNS, которая предоставляет статическое имя DNS виртуальному сетевому адаптеру.

```azurecli
azure network nic create jenkinsVNic \
-g myResourceGroup \
-l westus \
-m myVNet \
-k mySubNet \
-r jenkins
```

### <a name="deploy-the-vm-into-the-vnet-nsg-and-connect-the-vnic"></a>Развертывание виртуальной машины в виртуальной сети и группе безопасности сети и подключение виртуальной сетевой карты

`-N` подключает виртуальный сетевой адаптер к новой виртуальной машине при развертывании в Azure.

```azurecli
azure vm create jenkins \
-g myResourceGroup \
-l westus \
-y linux \
-Q Debian \
-o myStorageAcct \
-u myAdminUser \
-M ~/.ssh/id_rsa.pub \
-F myVNet \
-j mySubnet \
-N jenkinsVNic
```

## <a name="detailed-walkthrough"></a>Подробное пошаговое руководство

Для полной непрерывной интеграции и развертывания инфраструктуры в Azure определенные серверы должны быть статическими или долговременными серверами.  Мы рекомендуем, чтобы ресурсы Azure, такие как виртуальные сети и группы безопасности сети, были статическими, хранились в течение продолжительного времени и редко развертывались.  После развертывания виртуальной сети ее можно повторно использовать при новых развертываниях. Инфраструктура при этом не пострадает.  Добавление в эту статическую сеть сервера репозитория Git и сервера автоматизации Jenkins обеспечивает среды разработки или тестирования возможности непрерывной интеграции и развертывания инфраструктуры.  

Внутренние имена DNS можно сопоставлять только внутри виртуальной сети Azure.  Поскольку DNS-имена являются внутренними, они не сопоставляются с адресами в Интернете, обеспечивая дополнительную безопасность инфраструктуры.

_Замените все примеры своими именами._

## <a name="create-the-resource-group"></a>Создание группы ресурсов

Группа ресурсов необходима для организации всех компонентов, которые будут созданы в этом пошаговом руководстве.  Дополнительные сведения о группах ресурсов см. в статье [Общие сведения об Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

```azurecli
azure group create myResourceGroup \
--location westus
```

## <a name="create-the-vnet"></a>Создание виртуальной сети

Сначала нужно создать виртуальную сеть для запуска виртуальных машин.  Для этого пошагового руководства виртуальная сеть содержит одну подсеть.  Дополнительные сведения о виртуальных сетях Azure см. в разделе о [создании виртуальной сети](../../virtual-network/manage-virtual-network.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#create-a-virtual-network)

```azurecli
azure network vnet create myVNet \
--resource-group myResourceGroup \
--address-prefixes 10.10.0.0/24 \
--location westus
```

## <a name="create-the-nsg"></a>Создание группы безопасности сети

Подсеть создается за существующей группой безопасности сети, поэтому ее нужно создать перед подсетью.  На уровне сети группы безопасности сети Azure эквивалентны брандмауэру.  Дополнительные сведения о группах безопасности сети Azure см. в статье [Создание групп безопасности сети с помощью интерфейса командной строки Azure](../../virtual-network/tutorial-filter-network-traffic-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

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
--source-address-prefix * \
--source-port-range * \
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

## <a name="creating-static-dns-names"></a>Создание статических DNS-имен

Платформа Azure является очень гибкой, но чтобы использовать имена DNS для сопоставления имен виртуальных машин, необходимо с помощью меток DNS создавать их как виртуальные сетевые карты.  Виртуальные сетевые карты играют важную роль, так как их можно использовать повторно, подключая к разным виртуальным машинам. Таким образом виртуальная сетевая карта является статическим ресурсом, а виртуальные машины могут быть временными.  С помощью меток DNS на виртуальных сетевых картах можно обеспечить сопоставление простых имен из других виртуальных машин в виртуальной сети.  Использование сопоставляемых имен позволяет другим виртуальным машинам получить доступ к серверу автоматизации по DNS-имени `Jenkins` или серверу Git в качестве `gitrepo`.  Создайте виртуальную сетевую карту и свяжите ее с подсетью, созданной на предыдущем шаге.

```azurecli
azure network nic create jenkinsVNic \
-g myResourceGroup \
-l westus \
-m myVNet \
-k mySubNet \
-r jenkins
```

## <a name="deploy-the-vm-into-the-vnet-and-nsg"></a>Развертывание виртуальной машины в виртуальной сети и группе безопасности сети

Теперь у нас есть виртуальная сеть, подсеть в этой виртуальной сети и группа безопасности сети, выступающая в качестве брандмауэра для защиты подсети путем блокирования всего входящего трафика, кроме трафика, поступающего через порт 22 по протоколу SSH.  Теперь виртуальную машину можно развернуть в этой существующей сетевой инфраструктуре.

С помощью Azure CLI и команды `azure vm create` виртуальную машину Linux можно развернуть в существующей группе ресурсов Azure, виртуальной сети, подсети и виртуальной сетевой карте.  Дополнительные сведения о развертывании всех компонентов виртуальной машины с помощью интерфейса командной строки см. в статье [Создание полной среды Linux с помощью интерфейса командной строки Azure](create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

```azurecli
azure vm create jenkins \
--resource-group myResourceGroup myVM \
--location westus \
--os-type linux \
--image-urn Debian \
--storage-account-name mystorageaccount \
--admin-username myAdminUser \
--ssh-publickey-file ~/.ssh/id_rsa.pub \
--vnet-name myVNet \
--vnet-subnet-name mySubnet \
--nic-name jenkinsVNic
```

Используя флаги командной строки для вызова существующих ресурсов, мы укажем среде Azure развернуть виртуальную машину в существующей сети.  После развертывания виртуальную сеть и подсеть можно оставить в качестве статических или постоянных ресурсов в регионе Azure для повторного развертывания.  

## <a name="next-steps"></a>Дополнительная информация
* [Создание полной среды Linux с помощью интерфейса командной строки Azure](create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Создание виртуальной машины Linux с помощью шаблона Azure](create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

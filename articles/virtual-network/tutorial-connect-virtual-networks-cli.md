---
title: Подключение виртуальных сетей с помощью пиринговой связи (Azure CLI) | Документация Майкрософт
description: В этой статье вы узнаете, как подключить виртуальные сети между собой с помощью пиринга, используя Azure CLI.
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
Customer intent: I want to connect two virtual networks so that virtual machines in one virtual network can communicate with virtual machines in the other virtual network.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/13/2018
ms.author: jdial
ms.custom: ''
ms.openlocfilehash: 29ab957e97c6aa57be6192e6ee4d86fe642ae95d
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/03/2018
---
# <a name="connect-virtual-networks-with-virtual-network-peering-using-the-azure-cli"></a>Подключение виртуальных сетей с помощью пиринговой связи с использованием Azure CLI

Виртуальные сети можно подключить друг к другу с помощью пиринговой связи. После установки пиринговой связи ресурсы в обеих виртуальных сетях могут взаимодействовать друг с другом с такой же задержкой и пропускной способностью, как если бы эти ресурсы находились в одной виртуальной сети. В этой статье раскрываются следующие темы:

* создание двух виртуальных сетей;
* соединение двух виртуальных сетей с помощью пиринговой связи;
* развертывание виртуальной машины в каждой из виртуальных сетей;
* Взаимодействие между виртуальными машинами

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Если вы решили установить и использовать интерфейс командной строки локально, для работы с этой статьей вам понадобится Azure CLI 2.0.28 или более поздней версии. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0](/cli/azure/install-azure-cli). 

## <a name="create-virtual-networks"></a>Создание виртуальных сетей

Перед созданием виртуальной сети необходимо создать для нее группу ресурсов и другие компоненты, указанные в этой статье. Создайте группу ресурсов с помощью команды [az group create](/cli/azure/group#az_group_create). В следующем примере создается группа ресурсов с именем *myResourceGroup* в расположении *eastus*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

Создайте виртуальную сеть с помощью команды [az network vnet create](/cli/azure/network/vnet#az_network_vnet_create). В следующем примере создается виртуальная сеть с именем *myVirtualNetwork1* и префиксом адреса *10.0.0.0/16*.

```azurecli-interactive 
az network vnet create \
  --name myVirtualNetwork1 \
  --resource-group myResourceGroup \
  --address-prefixes 10.0.0.0/16 \
  --subnet-name Subnet1 \
  --subnet-prefix 10.0.0.0/24
```

Создайте виртуальную сеть с именем *myVirtualNetwork2* и префиксом адресов *10.1.0.0/16*:

```azurecli-interactive 
az network vnet create \
  --name myVirtualNetwork2 \
  --resource-group myResourceGroup \
  --address-prefixes 10.1.0.0/16 \
  --subnet-name Subnet1 \
  --subnet-prefix 10.1.0.0/24
```

## <a name="peer-virtual-networks"></a>Установка пиринговой связи между виртуальными сетями

Пиринговая связь настраивается по идентификаторам виртуальных сетей, а значит вам нужно получить идентификаторы обеих виртуальных сетей с помощью команды [az network vnet show](/cli/azure/network/vnet#az_network_vnet_show) и сохранить их в переменных.

```azurecli-interactive
# Get the id for myVirtualNetwork1.
vNet1Id=$(az network vnet show \
  --resource-group myResourceGroup \
  --name myVirtualNetwork1 \
  --query id --out tsv)

# Get the id for myVirtualNetwork2.
vNet2Id=$(az network vnet show \
  --resource-group myResourceGroup \
  --name myVirtualNetwork2 \
  --query id \
  --out tsv)
```

Создайте пиринговую связь сети *myVirtualNetwork1* с сетью *myVirtualNetwork2*, выполнив команду [az network vnet peering create](/cli/azure/network/vnet/peering#az_network_vnet_peering_create). Если не указать параметр `--allow-vnet-access`, пиринговая связь будет создана, но не позволит обмениваться данными.

```azurecli-interactive
az network vnet peering create \
  --name myVirtualNetwork1-myVirtualNetwork2 \
  --resource-group myResourceGroup \
  --vnet-name myVirtualNetwork1 \
  --remote-vnet-id $vNet2Id \
  --allow-vnet-access
```

В выходных данных указанной выше команды можно заметить, что параметр **peeringState** имеет значение *Initiated*. Состояние *Инициировано* не изменится, пока не будет создан пиринг сети *myVirtualNetwork2* с сетью *myVirtualNetwork1*. Установите пиринговую связь сети *myVirtualNetwork2* с сетью *myVirtualNetwork1*. 

```azurecli-interactive
az network vnet peering create \
  --name myVirtualNetwork2-myVirtualNetwork1 \
  --resource-group myResourceGroup \
  --vnet-name myVirtualNetwork2 \
  --remote-vnet-id $vNet1Id \
  --allow-vnet-access
```

В выходных данных этой команды параметр **peeringState** принимает значение *Подключено*. Одновременно с этим Azure установит значение *Подключено* и для первого пиринга *myVirtualNetwork1-myVirtualNetwork2*. Убедитесь, что для пиринга *myVirtualNetwork1-myVirtualNetwork2* установилось состояние *Подключено*, выполнив команду [az network vnet peering show](/cli/azure/network/vnet/peering#az_network_vnet_peering_show).

```azurecli-interactive
az network vnet peering show \
  --name myVirtualNetwork1-myVirtualNetwork2 \
  --resource-group myResourceGroup \
  --vnet-name myVirtualNetwork1 \
  --query peeringState
```

Ресурсы одной виртуальной сети не могут взаимодействовать с ресурсами другой виртуальной сети, пока **состояние пиринга** для обеих виртуальных сетей не примет значение *Подключен*. 

## <a name="create-virtual-machines"></a>Создание виртуальных машин

Создайте виртуальную машину в каждой виртуальной сети, чтобы на следующем шаге организовать взаимодействие между такими машинами.

### <a name="create-the-first-vm"></a>Создание первой виртуальной машины

Создайте виртуальную машину с помощью команды [az vm create](/cli/azure/vm#az_vm_create). В приведенном ниже примере в виртуальной сети *myVirtualNetwork1* создается виртуальная машина с именем *myVm1*. Также команда создает ключи SSH, если они не существуют в расположении ключей по умолчанию. Чтобы использовать определенный набор ключей, используйте параметр `--ssh-key-value`. Чтобы можно было перейти к следующему шагу, параметр `--no-wait` позволяет создать виртуальную машину в фоновом режиме.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVm1 \
  --image UbuntuLTS \
  --vnet-name myVirtualNetwork1 \
  --subnet Subnet1 \
  --generate-ssh-keys \
  --no-wait
```

### <a name="create-the-second-vm"></a>Создание второй виртуальной машины

Создайте виртуальную машину в виртуальной сети *myVirtualNetwork2*.

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name myVm2 \
  --image UbuntuLTS \
  --vnet-name myVirtualNetwork2 \
  --subnet Subnet1 \
  --generate-ssh-keys
```

Создание виртуальной машины занимает несколько минут. После создания виртуальной машины в Azure CLI отображаются примерно такие данные: 

```azurecli 
{
  "fqdns": "",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVm2",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.1.0.4",
  "publicIpAddress": "13.90.242.231",
  "resourceGroup": "myResourceGroup"
}
```

Запишите значение **publicIpAddress**. На следующем шаге этот адрес потребуется для доступа к виртуальной машине из Интернета.

## <a name="communicate-between-vms"></a>Взаимодействие между виртуальными машинами

Создайте сеанс SSH с виртуальной машиной *myVm2* при помощи приведенной ниже команды. Замените `<publicIpAddress>` общедоступным IP-адресом, который присвоен виртуальной машине. В предыдущем примере общедоступный IP-адрес — *13.90.242.231*.

```bash 
ssh <publicIpAddress>
```

Проверьте связь с виртуальной машиной в виртуальной сети *myVirtualNetwork1*.

```bash 
ping 10.0.0.4 -c 4
```

Вы получите четыре ответа. 

Закройте сеанс SSH с виртуальной машиной *myVm2*. 

## <a name="clean-up-resources"></a>Очистка ресурсов

Вы можете удалить ненужную группу ресурсов и все содержащиеся в ней ресурсы, выполнив команду [az group delete](/cli/azure/group#az_group_delete).

```azurecli-interactive 
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Дополнительная информация

Из этой статьи вы узнали, как с помощью пиринга соединить две виртуальные сети в одном регионе Azure. Пиринг можно использовать и для подключения виртуальных сетей из разных [поддерживаемых регионов](virtual-network-manage-peering.md#cross-region) или [разных подписок Azure](create-peering-different-subscriptions.md#cli), а также для создания [звездообразной топологии сети](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json#vnet-peering). Чтобы узнать больше о пиринге виртуальных сетей, ознакомьтесь с разделами [Пиринг между виртуальными сетями](virtual-network-peering-overview.md) и [Создание, изменение и удаление пиринга в виртуальной сети](virtual-network-manage-peering.md).

Теперь вы можете [подключить свой компьютер к виртуальной сети](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) через VPN и взаимодействовать с ресурсами в виртуальной сети или в виртуальных сетях, соединенных пиринговой связью. Примеры сценариев для многократного использования, с помощью которых можно выполнять многие задачи, описанные в статьях о виртуальных сетях, можно найти [здесь](cli-samples.md).
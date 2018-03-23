---
title: Подключение виртуальных сетей с помощью пиринговой связи (Azure CLI) | Документация Майкрософт
description: Сведения о том, как подключать виртуальные сети с помощью пиринговой связи.
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: ''
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/06/2018
ms.author: jdial
ms.custom: ''
ms.openlocfilehash: df56f2e3e13f80e7ce2c2b6c9cffeac3d03776e5
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/08/2018
---
# <a name="connect-virtual-networks-with-virtual-network-peering-using-the-azure-cli"></a>Подключение виртуальных сетей с помощью пиринговой связи с использованием Azure CLI

Виртуальные сети можно подключить друг к другу с помощью пиринговой связи. После установки пиринговой связи ресурсы в обеих виртуальных сетях могут взаимодействовать друг с другом с такой же задержкой и пропускной способностью, как если бы эти ресурсы находились в одной виртуальной сети. В этой статье описано, как создать две виртуальные сети и пиринговую связь между ними. Вы узнаете, как выполнять следующие задачи:

> [!div class="checklist"]
> * Создание двух виртуальных сетей
> * Создание пиринговой связи между виртуальными сетями
> * Тестирование пиринговой связи

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Если вы решили установить и использовать CLI локально, для выполнения инструкций в этом руководстве вам понадобится Azure CLI 2.0.4 или более поздней версии. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0](/cli/azure/install-azure-cli). 

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

Создайте виртуальную сеть с именем *myVirtualNetwork2* и префиксом адресов *10.1.0.0/16*. Этот префикс адреса не перекрывается с префиксом адреса виртуальной сети *myVirtualNetwork1*. Пиринговую связь невозможно установить между виртуальными сетями с перекрывающимися префиксами адресов.

```azurecli-interactive 
az network vnet create \
  --name myVirtualNetwork2 \
  --resource-group myResourceGroup \
  --address-prefixes 10.1.0.0/16 \
  --subnet-name Subnet1 \
  --subnet-prefix 10.1.0.0/24
```

## <a name="peer-virtual-networks"></a>Пиринговая связь между виртуальными сетями

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

Пиринговые связи устанавливаются между двумя виртуальными сетями, но не являются транзитивными. Например, если также требуется установить пиринговую связь сети *myVirtualNetwork2* с сетью *myVirtualNetwork3*, необходимо создать дополнительный пиринг между виртуальными сетями *myVirtualNetwork2* и *myVirtualNetwork3*. Несмотря на то, что *myVirtualNetwork1* уже имеет пиринговую связь с *myVirtualNetwork2*, ресурсы из *myVirtualNetwork1* получат доступ к ресурсам *myVirtualNetwork3* только в том случае, если будет создана пиринговая связь между *myVirtualNetwork1* и *myVirtualNetwork3*. 

Прежде чем настраивать пиринговую связь между рабочими виртуальными сетями, внимательно ознакомьтесь с [общими сведениями о пиринге](virtual-network-peering-overview.md), [сведениями об управлении пирингом](virtual-network-manage-peering.md) и [ограничениями виртуальных сетей](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits). Хотя в этой статье рассматривается пиринговая связь между двумя виртуальными сетями в одной подписке и одном расположении, вы можете настраивать ее и для виртуальных сетей в [разных регионах](#register) и [разных подписках Azure](create-peering-different-subscriptions.md#cli). Также с помощью пиринга можно создавать [звездообразные сетевые схемы](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json#vnet-peering).

## <a name="test-peering"></a>Тестирование пиринговой связи

Чтобы проверить, как пиринговая связь организует сетевое взаимодействие между виртуальными машинами в разных виртуальных сетях, разверните виртуальные машины в каждой подсети и организуйте обмен данными между этими виртуальными машинами. 

### <a name="create-virtual-machines"></a>Создание виртуальных машин

Создайте виртуальную машину с помощью команды [az vm create](/cli/azure/vm#az_vm_create). В приведенном ниже примере в виртуальной сети *myVirtualNetwork1* создается виртуальная машина с именем *myVm1*. Также команда создает ключи SSH, если они не существуют в расположении ключей по умолчанию. Чтобы использовать определенный набор ключей, используйте параметр `--ssh-key-value`. Параметр `--no-wait` позволяет создать виртуальную машину в фоновом режиме, чтобы можно было перейти к следующему шагу.

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

Azure автоматически назначает виртуальной машине частный IP-адрес 10.0.0.4, так как это первый доступный IP-адрес в подсети *Subnet1* виртуальной сети *myVirtualNetwork1*. 

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

Создание виртуальной машины занимает несколько минут. После создания виртуальной машины в Azure CLI отображаются сведения примерно следующего вида: 

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

В приведенном примере можно видеть, что параметр **privateIpAddress** имеет значение *10.1.0.4*. Azure DHCP автоматически назначает виртуальной машине адрес 10.1.0.4, так как это первый доступный адрес в подсети *Subnet1* виртуальной сети *myVirtualNetwork2*. Запишите значение **publicIpAddress**. Позже этот адрес будет использоваться для доступа к виртуальной машине из Интернета.

### <a name="test-virtual-machine-communication"></a>Тестирование обмена данными между виртуальными машинами

Используйте следующую команду для создания сеанса SSH с виртуальной машиной *myVm2*. Замените `<publicIpAddress>` общедоступным IP-адресом виртуальной машины. В предыдущем примере общедоступный IP-адрес — *13.90.242.231*.

```bash 
ssh <publicIpAddress>
```

Проверьте связь с виртуальной машиной в *myVirtualNetwork1*.

```bash 
ping 10.0.0.4 -c 4
```

Вы получите четыре ответа. Если для проверки связи вы укажете имя виртуальной машины (*myVm1*), а не IP-адрес, эта команда завершится ошибкой, так как *myVm1* не является известным именем узла. Разрешение имен Azure между виртуальными машинами по умолчанию работает только в пределах виртуальной сети, но не в разных виртуальных сетях. Чтобы разрешение имен выполнялось между разными виртуальными сетями, [разверните собственный DNS-сервер](virtual-networks-name-resolution-for-vms-and-role-instances.md) или используйте [частные домены Azure DNS](../dns/private-dns-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Закройте сеанс SSH с виртуальной машиной *myVm2*. 

## <a name="clean-up-resources"></a>Очистка ресурсов

Вы можете удалить ненужную группу ресурсов и все содержащиеся в ней ресурсы, выполнив команду [az group delete](/cli/azure/group#az_group_delete).

```azurecli-interactive 
az group delete --name myResourceGroup --yes
```

**<a name="register"></a>Регистрация для использования предварительной версии функции пиринга между виртуальными сетями в разных регионах**

Функция создания пиринговых связей между виртуальными сетями в одном регионе является общедоступной. Функция пиринга между виртуальными сетями в разных регионах сейчас находится на этапе предварительной версии. Доступные регионы перечислены в разделе [Обновления Azure](https://azure.microsoft.com/updates/?product=virtual-network). Чтобы создать пиринговую связь между виртуальными сетями в разных регионах, сначала нужно зарегистрироваться для использования предварительной версии, выполнив приведенные ниже действия (в подписке каждой виртуальной сети, для которой требуется пиринг).

1. Зарегистрируйтесь для использования предварительной версии, выполнив следующие команды:

  ```azurecli-interactive
  az feature register --name AllowGlobalVnetPeering --namespace Microsoft.Network
  az provider register --name Microsoft.Network
  ```

2. Убедитесь, что регистрация прошла успешно, выполнив следующую команду:

  ```azurecli-interactive
  az feature show --name AllowGlobalVnetPeering --namespace Microsoft.Network
  ```

  Если вы попытаетесь установить пиринговую связь между виртуальными сетями в разных регионах до того, как параметр **RegistrationState** в выходных данных предыдущей команды для обеих подписок получит значение **Зарегистрировано**, пиринговая связь не будет установлена.

## <a name="next-steps"></a>Дополнительная информация

Из этой статьи вы узнали, как соединить две виртуальные сети с помощью пиринга. Теперь вы можете [подключить свой компьютер к виртуальной сети](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) через VPN и взаимодействовать с ресурсами в виртуальной сети или в виртуальных сетях, соединенных пиринговой связью.

Перейдите к примерам скриптов для многократного использования, с помощью которых можно выполнять многие задачи, описанные в статьях о виртуальных сетях.

> [!div class="nextstepaction"]
> [Примеры скриптов для виртуальных сетей](../networking/cli-samples.md?toc=%2fazure%2fvirtual-network%2ftoc.json)

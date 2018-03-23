---
title: 'Маршрутизация сетевого трафика: Azure CLI | Документация Майкрософт'
description: Сведения о маршрутизации сетевого трафика с помощью таблицы маршрутов и Azure CLI.
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/02/2018
ms.author: jdial
ms.custom: ''
ms.openlocfilehash: 3c16c774fa1c8a5c8bf50b4f4f9d0bfb283318e3
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/08/2018
---
# <a name="route-network-traffic-with-a-route-table-using-the-azure-cli"></a>Маршрутизация сетевого трафика с помощью таблицы маршрутов и Azure CLI

По умолчанию Azure автоматически маршрутизирует трафик между всеми подсетями в виртуальной сети. Для переопределения маршрутизации Azure по умолчанию можно создать собственные маршруты. Возможность создания настраиваемых маршрутов полезна, если, например, требуется маршрутизировать трафик между подсетями через брандмауэр. В этой статье раскрываются следующие темы:

> [!div class="checklist"]
> * Создание таблицы маршрутов
> * Создание маршрута
> * Связывание таблицы маршрутов с подсетью виртуальной сети
> * Тестирование маршрутизации
> * Устранение неполадок маршрутизации

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Если вы решили установить и использовать CLI локально, для выполнения инструкций из этого руководства вам потребуется Azure CLI 2.0.28 или более поздней версии. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0](/cli/azure/install-azure-cli). 

## <a name="create-a-route-table"></a>Создание таблицы маршрутов

По умолчанию Azure маршрутизирует трафик между всеми подсетями в виртуальной сети. Дополнительные сведения о маршрутах Azure по умолчанию см. в разделе о [системных маршрутах](virtual-networks-udr-overview.md). Чтобы переопределить маршрутизацию Azure по умолчанию, создайте заполненную таблицу маршрутов и свяжите ее с подсетью виртуальной сети.

Перед созданием таблицы маршрутов создайте группу ресурсов с помощью команды [az group create](/cli/azure/group#az_group_create) для всех ресурсов, созданных в рамках этой статьи. 

```azurecli-interactive
# Create a resource group.
az group create \
  --name myResourceGroup \
  --location eastus
``` 

Создайте таблицу маршрутов с помощью команды [az network route-table create](/cli/azure/network/route#az_network_route_table_create). В следующем примере создается таблица маршрутов с именем *myRouteTablePublic*. 

```azurecli-interactive 
# Create a route table
az network route-table create \
  --resource-group myResourceGroup \
  --name myRouteTablePublic
```

## <a name="create-a-route"></a>Создание маршрута

Таблица маршрутов содержит несколько маршрутов или не содержит их вообще. Создайте маршрут в таблице маршрутов с помощью команды [az network route-table route create](/cli/azure/network/route-table/route#az_network_route_table_route_create). 

```azurecli-interactive
az network route-table route create \
  --name ToPrivateSubnet \
  --resource-group myResourceGroup \
  --route-table-name myRouteTablePublic \
  --address-prefix 10.0.1.0/24 \
  --next-hop-type VirtualAppliance \
  --next-hop-ip-address 10.0.2.4
``` 

Маршрут будет направлять весь трафик, предназначенный для префикса адреса 10.0.1.0/24, через сетевой виртуальный модуль с IP-адресом 10.0.2.4. Виртуальное устройство в сети и подсеть с указанным префиксом адреса создаются на последующих этапах. Маршрут переопределяет маршрут Azure по умолчанию, который перенаправляет трафик между подсетями напрямую. Каждый маршрут определяет тип следующего прыжка. Тип следующего прыжка указывает Azure, как маршрутизировать трафик. В этом примере тип следующего прыжка — *VirtualAppliance*. Дополнительные сведения обо всех доступных типах прыжков в Azure, а также их использовании см. в [этой статье](virtual-networks-udr-overview.md#custom-routes).

## <a name="associate-a-route-table-to-a-subnet"></a>Связывание таблицы маршрутов с подсетью

Чтобы связать таблицу маршрутов с подсетью, необходимо создать виртуальную сеть и подсеть. Создайте виртуальную сеть с одной подсетью при помощи команды [az network vnet create](/cli/azure/network/vnet#az_network_vnet_create).

```azurecli-interactive
az network vnet create \
  --name myVirtualNetwork \
  --resource-group myResourceGroup \
  --address-prefix 10.0.0.0/16 \
  --subnet-name Public \
  --subnet-prefix 10.0.0.0/24
```

Создайте две дополнительные подсети с помощью команды [az network vnet subnet create](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create).

```azurecli-interactive
# Create a private subnet.
az network vnet subnet create \
  --vnet-name myVirtualNetwork \
  --resource-group myResourceGroup \
  --name Private \
  --address-prefix 10.0.1.0/24

# Create a DMZ subnet.
az network vnet subnet create \
  --vnet-name myVirtualNetwork \
  --resource-group myResourceGroup \
  --name DMZ \
  --address-prefix 10.0.2.0/24
```

Таблицу маршрутов можно связать с несколькими подсетями или не связывать ни с одной. Подсеть может иметь не более одной таблицы маршрутов, связанной с ней. Исходящий трафик из подсети маршрутизируется на основе маршрутов Azure по умолчанию. Все настраиваемые маршруты, добавленные в таблицу маршрутов, связываются с подсетью. Свяжите таблицу маршрутов *myRouteTablePublic* с подсетью *Public* при помощи команды [az network vnet subnet update](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_update).

```azurecli-interactive
az network vnet subnet update \
  --vnet-name myVirtualNetwork \
  --name Public \
  --resource-group myResourceGroup \
  --route-table myRouteTablePublic
```

Перед развертыванием таблиц маршрутов для использования в рабочей среде рекомендуем внимательно ознакомиться со статьями о [маршрутизации в Azure](virtual-networks-udr-overview.md) и [ограничениями в Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

## <a name="test-routing"></a>Тестирование маршрутизации

Чтобы протестировать маршрутизацию, создайте виртуальную машину, которая будет служить в качестве сетевого виртуального модуля, через который проходит маршрут, созданный на предыдущем шаге. После создания сетевого виртуального модуля вы развернете виртуальную машину в подсетях *Public* (Открытая) и *Private* (Закрытая). Затем маршрутизируйте трафик из подсети *Public* в подсеть *Private* через сетевой виртуальный модуль.

### <a name="create-a-network-virtual-appliance"></a>Создание сетевого виртуального модуля

На предыдущем шаге вы создали маршрут, в котором указан сетевой виртуальный модуль как тип следующего прыжка. Виртуальная машина, на которой выполняется сетевое приложение, часто называется сетевым виртуальным модулем. В рабочей среде развертываемый сетевой виртуальный модуль чаще всего представляет собой предварительно настроенную виртуальную машину. Несколько сетевых виртуальных модулей доступны в [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?search=network%20virtual%20appliance&page=1). В этой статье создается базовая виртуальная машина. 

Создайте сетевой виртуальный модуль в подсети *сети периметра* с помощью команды [az vm create](/cli/azure/vm#az_vm_create). По умолчанию при создании виртуальной машины Azure создает и назначает этой машине общедоступный IP-адрес. Параметр `--public-ip-address ""` указывает Azure не создавать и не назначать виртуальной машине общедоступный IP-адрес, так как ей не требуется подключение из Интернета. Также команда создает ключи SSH, если они не существуют в расположении ключей по умолчанию. Чтобы использовать определенный набор ключей, используйте параметр `--ssh-key-value`.

```azure-cli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVmNva \
  --image UbuntuLTS \
  --public-ip-address "" \
  --subnet DMZ \
  --vnet-name myVirtualNetwork \
  --generate-ssh-keys
```

Создание виртуальной машины занимает несколько минут. Не переходите к следующему шагу до тех пор, пока Azure не завершит создание виртуальной машины и вернет выходные данные о ней. В рабочей среде развертываемый сетевой виртуальный модуль чаще всего представляет собой предварительно настроенную виртуальную машину. Несколько сетевых виртуальных модулей доступны в [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?search=network%20virtual%20appliance&page=1).

Необходимо включить IP-пересылку для каждого [сетевого интерфейса](virtual-network-network-interface.md) Azure, подключенного к виртуальной машине, которая перенаправляет трафик, предназначенный для любого IP-адреса, не назначенного сетевому интерфейсу. При создании сетевого виртуального модуля в подсети *сети периметра* платформа Azure автоматически создала сетевой интерфейс с именем *myVmNvaVMNic*, присоединила его к виртуальной машине и назначила частный IP-адрес *10.0.2.4* этому сетевому интерфейсу. Включите IP-пересылку для сетевого интерфейса с помощью команды [az network nic update](/cli/azure/network/nic#az_network_nic_update).

```azurecli-interactive
az network nic update \
  --name myVmNvaVMNic \
  --resource-group myResourceGroup \
  --ip-forwarding true
```

Внутри виртуальной машины операционная система или приложение, работающее на виртуальной машине, также должны иметь возможность перенаправлять сетевой трафик. При развертывании сетевого виртуального модуля в рабочей среде этот модуль обычно выполняет фильтрацию, запись в журнале или другую функцию перед пересылкой трафика. Однако в этой статье операционная система просто перенаправляет весь трафик, который она получает. Включите IP-пересылку в операционной системе виртуальной машины с помощью команды [az vm extension set](/cli/azure/vm/extension#az_vm_extension_set), которая разрешает IP-пересылку в операционной системе.

```azurecli-interactive
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVmNva \
  --name customScript \
  --publisher Microsoft.Azure.Extensions \
  --settings '{"commandToExecute":"sudo sysctl -w net.ipv4.ip_forward=1"}'
```
Выполнение команды может занять до минуты.

### <a name="create-virtual-machines"></a>Создание виртуальных машин

Создайте две виртуальные машины в виртуальной сети, чтобы убедиться, что трафик из подсети *Public* направляется в подсеть *Private* через сетевой виртуальный модуль на более позднем этапе. 

Создайте виртуальную машину в подсети *Public* с помощью командлета [az vm create](/cli/azure/vm#az_vm_create). Параметр `--no-wait` позволяет Azure выполнять команду в фоновом режиме, поэтому вы можете перейти к следующей команде. Для упрощения работы с этой статьей используется пароль. Ключи обычно используются в рабочей среде. Если вы используете ключи, необходимо также настроить перенаправление агента SSH. Дополнительные сведения см. в документации по своему клиенту SSH. В следующей команде замените значение `<replace-with-your-password>` паролем по своему выбору.

```azurecli-interactive
adminPassword="<replace-with-your-password>"

az vm create \
  --resource-group myResourceGroup \
  --name myVmWeb \
  --image UbuntuLTS \
  --vnet-name myVirtualNetwork \
  --subnet Public \
  --admin-username azureuser \
  --admin-password $adminPassword \
  --no-wait
```

Создайте виртуальную машину в *частной* подсети.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVmMgmt \
  --image UbuntuLTS \
  --vnet-name myVirtualNetwork \
  --subnet Private \
  --admin-username azureuser \
  --admin-password $adminPassword
```

Создание виртуальной машины занимает несколько минут. После создания виртуальной машины в Azure CLI отображаются сведения примерно следующего вида: 

```azurecli 
{
  "fqdns": "",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVmMgmt",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.1.4",
  "publicIpAddress": "13.90.242.231",
  "resourceGroup": "myResourceGroup"
}
```
Запишите значение **publicIpAddress**. Позже этот адрес будет использоваться для доступа к виртуальной машине из Интернета.

### <a name="route-traffic-through-a-network-virtual-appliance"></a>Маршрутизация трафика через виртуальный сетевой модуль

Используйте следующую команду для создания сеанса SSH с виртуальной машиной *myVmMgmt*. Замените *<publicIpAddress>* общедоступным IP-адресом виртуальной машины. В примере выше используется IP-адрес *13.90.242.231*.

```bash 
ssh azureuser@<publicIpAddress>
```

При запросе пароля введите пароль, выбранный при работе с разделом [Создание виртуальных машин](#create-virtual-machines).

С помощью следующей команды установите средство traceroute на виртуальную машину *myVmMgmt*:

```bash 
sudo apt-get install traceroute
```

Используя следующую команду, протестируйте маршрутизацию сетевого трафика к виртуальной машине *myVmWeb* от виртуальной машины *myVmMgmt*.

```bash
traceroute myvmweb
```

Ответ будет выглядеть примерно так:

```bash
traceroute to myvmweb (10.0.0.4), 30 hops max, 60 byte packets
1  10.0.0.4 (10.0.0.4)  1.404 ms  1.403 ms  1.398 ms
```

Вы увидите, что трафик направляется непосредственно с виртуальной машины *myVmMgmt* на виртуальную машину *myVmWeb*. Маршруты Azure по умолчанию выполняют маршрутизацию трафика напрямую между подсетями. 

Чтобы подключиться по SSH к виртуальной машине *myVmWeb* из виртуальной машины *myVmMgmt*, выполните следующую команду:

```bash 
ssh azureuser@myVmWeb
```

С помощью следующей команды установите средство traceroute на виртуальную машину *myVmWeb*:

```bash 
sudo apt-get install traceroute
```

Используя следующую команду, протестируйте маршрутизацию сетевого трафика к виртуальной машине *myVmMgmt* от виртуальной машины *myVmWeb*.

```bash
traceroute myvmmgmt
```

Ответ будет выглядеть примерно так:

```bash
traceroute to myvmmgmt (10.0.1.4), 30 hops max, 60 byte packets
1  10.0.2.4 (10.0.2.4)  0.781 ms  0.780 ms  0.775 ms
2  10.0.1.4 (10.0.0.4)  1.404 ms  1.403 ms  1.398 ms
```
Вы видите, что первому прыжку соответствует 10.0.2.4 — частный IP-адрес сетевого виртуального модуля. Второй прыжок — 10.0.1.4, частный IP-адрес виртуальной машины *myVmMgmt*. Маршрут, добавленный в таблицу маршрутов *myRouteTablePublic* и связанный с подсетью *Public*, заставил Azure маршрутизировать трафик через NVA, а не напрямую в подсеть *Private*.

Закройте сеансы SSH с обеими виртуальными машинами — *myVmWeb* и *myVmMgmt*.

## <a name="troubleshoot-routing"></a>Устранение неполадок маршрутизации

Как было показано на предыдущих шагах, Azure применяет маршруты по умолчанию, которые можно при необходимости переопределить собственными маршрутами. Иногда трафик может не маршрутизироваться ожидаемым образом. Команда [az network watcher show-next-hop](/cli/azure/network/watcher#az_network_watcher_show_next_hop) позволяет определить, как маршрутизируется трафик между двумя виртуальными машинами. Например, следующая команда проверяет маршрутизацию трафика от виртуальной машины *myVmWeb* (10.0.0.4) к виртуальной машине *myVmMgmt* (10.0.1.4):

```azurecli-interactive
# Enable network watcher for east region, if you don't already have a network watcher enabled for the region.
az network watcher configure --locations eastus --resource-group myResourceGroup --enabled true

```azurecli-interactive
az network watcher show-next-hop \
  --dest-ip 10.0.1.4 \
  --resource-group myResourceGroup \
  --source-ip 10.0.0.4 \
  --vm myVmWeb \
  --out table
```
После короткого ожидания отобразится результат следующего вида:

```azurecli
NextHopIpAddress    NextHopType       RouteTableId
------------------  ---------------- ---------------------------------------------------------------------------------------------------------------------------
10.0.2.4            VirtualAppliance  /subscriptions/<Subscription-Id>/resourceGroups/myResourceGroup/providers/Microsoft.Network/routeTables/myRouteTablePublic
```

Выходные данные сообщают о том, что IP-адрес следующего прыжка для трафика от *myVmWeb* к *myVmMgmt* — 10.0.2.4 (виртуальная машина *myVmNva*), тип следующего прыжка — *VirtualAppliance*, а таблица маршрутов, которая вызывает маршрутизацию, — *myRouteTablePublic*.

Эффективные маршруты для каждого сетевого интерфейса представляют собой комбинацию маршрутов Azure по умолчанию и любых определяемых вами маршрутов. Чтобы увидеть все маршруты, действующие для сетевого интерфейса на виртуальной машине, используйте команду [az network nic show-effective-route-table](/cli/azure/network/nic#az_network_nic_show_effective_route_table). Например, чтобы отобразить действующие маршруты для сетевого интерфейса *MyVmWebVMNic* на виртуальной машине *myVmWeb*, введите следующую команду:

```azurecli-interactive
az network nic show-effective-route-table \
  --name MyVmWebVMNic \
  --resource-group myResourceGroup
```

Будут возвращены все маршруты по умолчанию и маршрут, добавленный на предыдущем шаге.

## <a name="clean-up-resources"></a>Очистка ресурсов

Вы можете удалить ненужную группу ресурсов и все содержащиеся в ней ресурсы, выполнив команду [az group delete](/cli/azure/group#az_group_delete).

```azurecli-interactive 
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Дополнительная информация

В этой статье вы создали таблицу маршрутов и связали ее с подсетью. Вы создали сетевой виртуальный модуль, который направил трафик из общедоступной подсети в частную подсеть. Хотя в виртуальной сети можно развернуть множество ресурсов Azure, ресурсы для некоторых служб Azure PaaS невозможно развернуть в виртуальной сети. Тем не менее вы можете ограничить доступ к ресурсам некоторых служб Azure PaaS трафиком только из подсети виртуальной сети. Перейдите к следующему руководству, чтобы узнать, как ограничить сетевой доступ к ресурсам Azure PaaS.

> [!div class="nextstepaction"]
> [Ограничение сетевого доступа к ресурсам PaaS](virtual-network-service-endpoints-configure.md#azure-cli)

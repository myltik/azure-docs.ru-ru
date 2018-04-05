---
title: Создание виртуальной сети Azure с помощью Azure CLI | Документация Майкрософт
description: Быстро узнайте, как создать виртуальную сеть с помощью Azure CLI. Виртуальная сеть позволяет ресурсам Azure, таким как виртуальные машины, обмениваться данными в частном порядке и взаимодействовать через Интернет.
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
ms.date: 03/09/2018
ms.author: jdial
ms.custom: ''
ms.openlocfilehash: d07f06a1a70c859544c3b1ceb6146dc11e4d10aa
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/03/2018
---
# <a name="create-a-virtual-network-using-the-azure-cli"></a>Создание виртуальной сети с помощью интерфейса командной строки Azure

Виртуальная сеть позволяет ресурсам Azure, таким как виртуальные машины, обмениваться данными в частном порядке и взаимодействовать через Интернет. В этой статье вы узнаете, как создать виртуальную сеть. Создав виртуальную сеть, разверните в ней две виртуальные машины. Затем подключитесь к одной виртуальной машине из Интернета и установите частную связь с другой виртуальной машиной.

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Если вы решили установить и использовать интерфейс командной строки локально, для работы с этой статьей вам понадобится Azure CLI 2.0.28 или более поздней версии. Выполните командлет `az --version`, чтобы узнать установленную версию. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0](/cli/azure/install-azure-cli). 


## <a name="create-a-virtual-network"></a>Создать виртуальную сеть

Перед созданием виртуальной сети необходимо создать группу ресурсов, которая будет содержать эту виртуальную сеть. Создайте группу ресурсов с помощью команды [az group create](/cli/azure/group#az_group_create). В следующем примере создается группа ресурсов с именем *myResourceGroup* в расположении *eastus*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

Создайте виртуальную сеть с помощью команды [az network vnet create](/cli/azure/network/vnet#az_network_vnet_create). В следующем примере создается виртуальная сеть по умолчанию *myVirtualNetwork* с подсетью *default*.

```azurecli-interactive 
az network vnet create \
  --name myVirtualNetwork \
  --resource-group myResourceGroup \
  --subnet-name default
```

## <a name="create-virtual-machines"></a>Создание виртуальных машин

Создайте две виртуальные машины в виртуальной сети.

### <a name="create-the-first-vm"></a>Создание первой виртуальной машины

Создайте виртуальную машину с помощью команды [az vm create](/cli/azure/vm#az_vm_create). Также команда создает ключи SSH, если они не существуют в расположении ключей по умолчанию. Чтобы использовать определенный набор ключей, используйте параметр `--ssh-key-value`. Параметр `--no-wait` позволяет создать виртуальную машину в фоновом режиме, чтобы можно было перейти к следующему шагу. В следующем примере создается виртуальная машина с именем *myVm1*.

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name myVm1 \
  --image UbuntuLTS \
  --generate-ssh-keys \
  --no-wait
```

### <a name="create-the-second-vm"></a>Создание второй виртуальной машины

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name myVm2 \
  --image UbuntuLTS \
  --generate-ssh-keys
```

Создание виртуальной машины занимает несколько минут. После создания виртуальной машины Azure CLI возвращает выходные данные, как в примере ниже: 

```azurecli 
{
  "fqdns": "",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVm1",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.5",
  "publicIpAddress": "40.68.254.142",
  "resourceGroup": "myResourceGroup"
}
```

Запишите значение **publicIpAddress**. Этот адрес используется на следующем шаге, чтобы подключиться к виртуальной машине из Интернета.

## <a name="connect-to-a-vm-from-the-internet"></a>Подключение к виртуальной машине из Интернета

Введите следующую команду, заменив `<publicIpAddress>` общедоступным IP-адресом виртуальной машины *myVm2*:

```bash 
ssh <publicIpAddress>
```

## <a name="communicate-privately-between-vms"></a>Организация частной связи между виртуальными машинами

Чтобы проверить частную связь между виртуальными машинами *myVm2* и *myVm1*, введите следующую команду:

```bash
ping myVm1 -c 4
```

Вы получите четыре ответа с адреса *10.0.0.4*.

Завершите сеанс SSH с виртуальной машиной *myVm2*.

## <a name="clean-up-resources"></a>Очистка ресурсов

Чтобы удалить ненужную группу ресурсов и все содержащиеся в ней ресурсы, выполните команду [az group delete](/cli/azure/group#az_group_delete).

```azurecli-interactive 
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Дополнительная информация

Следуя инструкциям в этой статье, вы создали виртуальную сеть по умолчанию и две виртуальные машины. Затем вы подключились к одной виртуальной машине из Интернета и установили частную связь между двумя виртуальными машинами. Дополнительные сведения о настройках виртуальной сети см. в статье о [создании, изменении или удалении виртуальной сети](manage-virtual-network.md). 

По умолчанию Azure не накладывает ограничений на частную связь между виртуальными машинами, но разрешает только входящие сеансы SSH для виртуальных машин Linux из Интернета. Чтобы узнать, как разрешить или ограничить различные типы сетевого взаимодействия с виртуальными машинами в обоих направлениях, перейдите к следующему руководству.

> [!div class="nextstepaction"]
> [Фильтрация сетевого трафика](tutorial-filter-network-traffic-cli.md)

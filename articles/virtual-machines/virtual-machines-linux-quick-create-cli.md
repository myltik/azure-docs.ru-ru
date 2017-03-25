---
title: "Краткое руководство по Azure. Создание виртуальной машины с помощью интерфейса командной строки | Документация Майкрософт"
description: "Быстро научитесь создавать виртуальные машины с помощью Azure CLI."
services: virtual-machines-linux
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/10/2017
ms.author: nepeters
translationtype: Human Translation
ms.sourcegitcommit: c1cd1450d5921cf51f720017b746ff9498e85537
ms.openlocfilehash: 3ad45837a8c3f0e37e8571fb9b894df53ecb5f56
ms.lasthandoff: 03/14/2017

---

# <a name="create-a-linux-virtual-machine-with-the-azure-cli-20"></a>Создание виртуальной машины Linux с помощью Azure CLI 2.0

Azure CLI 2.0 используется для создания ресурсов Azure и управления ими из командной строки или с помощью сценариев. В этом руководстве описывается, как с помощью Azure CLI развернуть виртуальную машину под управлением Ubuntu 14.04 LTS.

Перед началом работы убедитесь, что вы установили Azure CLI. Дополнительные сведения см. в [руководстве по установке Azure CLI&2;.0](https://docs.microsoft.com/cli/azure/install-azure-cli). 

## <a name="log-in-to-azure"></a>Вход в Azure 

Войдите в подписку Azure с помощью команды [az login](/cli/azure/#login) и следуйте инструкциям на экране.

```azurecli
az login
```

## <a name="create-a-resource-group"></a>Создание группы ресурсов

Создайте группу ресурсов с помощью команды [az group create](/cli/azure/group#create). Группа ресурсов Azure является логическим контейнером, в котором происходит развертывание ресурсов Azure и управление ими. 

В следующем примере создается группа ресурсов с именем `myResourceGroup` в расположении `westeurope`.

```azurecli
az group create --name myResourceGroup --location westeurope
```

## <a name="create-virtual-machine"></a>Создание виртуальной машины

Создайте виртуальную машину с помощью команды [az vm create](/cli/azure/vm#create). 

В следующем примере создаются виртуальная машина `myVM` и ключи SSH, если они не существуют в расположении ключей по умолчанию. Чтобы использовать определенный набор ключей, используйте параметр `--ssh-key-value`.  

```azurecli
az vm create --resource-group myResourceGroup --name myVM --image UbuntuLTS --generate-ssh-keys
```

После создания виртуальной машины в Azure CLI отображается информация следующего вида. Запишите общедоступный IP-адрес. Этот адрес используется для доступа к виртуальной машине.

```azurecli
{
  "fqdns": "",
  "id": "/subscriptions/d5b9d4b7-6fc1-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "westeurope",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "52.174.34.95",
  "resourceGroup": "myResourceGroup"
}
```

## <a name="connect-to-virtual-machine"></a>Подключение к виртуальной машине

Используйте следующую команду для создания сеанса SSH с виртуальной машиной. Замените IP-адрес общедоступным IP-адресом виртуальной машины.

```bash 
ssh <Public IP Address>
```

## <a name="delete-virtual-machine"></a>Удаление виртуальной машины

Можно удалить ставшие ненужными группу ресурсов, виртуальную машину и все связанные с ней ресурсы, использовав следующую команду.

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Дальнейшие действия

Изучите [руководство по созданию высокодоступных виртуальных машин](./virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

[Изучите примеры развертывания виртуальных машин с помощью интерфейса командной строки](./virtual-machines-linux-cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

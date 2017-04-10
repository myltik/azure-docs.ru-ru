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
ms.topic: hero-article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/03/2017
ms.author: nepeters
translationtype: Human Translation
ms.sourcegitcommit: 303cb9950f46916fbdd58762acd1608c925c1328
ms.openlocfilehash: 67b4af9f3ef7fb078d6d125e0d3257ea85e288b0
ms.lasthandoff: 04/04/2017

---

# <a name="create-a-linux-virtual-machine-with-the-azure-cli"></a>Создание виртуальной машины Linux с помощью Azure CLI

Azure CLI используется для создания ресурсов Azure и управления ими из командной строки или с помощью скриптов. В этом руководстве описывается, как с помощью Azure CLI развернуть виртуальную машину под управлением Ubuntu 16.04 LTS. После развертывания сервера мы подключимся к виртуальной машине по протоколу SSH, чтобы установить nginx. 

Перед началом работы убедитесь, что вы установили Azure CLI. Дополнительные сведения см. в [руководстве по установке Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli). 

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

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

После создания виртуальной машины в Azure CLI отображается информация следующего вида. Запишите значение `publicIpAddress`. Этот адрес используется для доступа к виртуальной машине.

```azurecli
{
  "fqdns": "",
  "id": "/subscriptions/d5b9d4b7-6fc1-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "westeurope",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "40.68.254.142",
  "resourceGroup": "myResourceGroup"
}
```

## <a name="open-port-80-for-web-traffic"></a>Открытие порта 80 для веб-трафика 

По умолчанию виртуальные машины Linux, развернутые в Azure, поддерживают только SSH-подключения. Если эта виртуальная машина будет использоваться в качестве веб-сервера, необходимо открыть порт 80 через Интернет.  Чтобы открыть нужный порт, необходимо выполнить одну команду.  
 
 ```azurecli 
az vm open-port --port 80 --resource-group myResourceGroup --name myVM
```

## <a name="ssh-into-your-vm"></a>SSH-подключение к виртуальной машине

Используйте следующую команду для создания сеанса SSH с виртуальной машиной. Замените `<publicIpAddress>` общедоступным IP-адресом виртуальной машины.  В примере выше виртуальная машина имела следующий IP-адрес: `40.68.254.142`.

```bash 
ssh <publicIpAddress>
```

## <a name="install-nginx"></a>Установка nginx

Чтобы обновить источники пакетов и установить последнюю версию пакета nginx, используйте следующий bash-скрипт: 

```bash 
#!/bin/bash

# update package source
apt-get -y update

# install NGINX
apt-get -y install nginx
```

## <a name="view-the-ngix-welcome-page"></a>Просмотр страницы приветствия nginx

Установив nginx и открыв через Интернет порт 80 на виртуальной машине, вы можете просмотреть страницу приветствия nginx по умолчанию в любом браузере. Чтобы перейти на страницу по умолчанию, используйте значение `publicIpAddress`, записанное ранее. 

![Сайт nginx по умолчанию](./media/quick-create-cli/nginx.png) 


## <a name="delete-virtual-machine"></a>Удаление виртуальной машины

Можно удалить ставшие ненужными группу ресурсов, виртуальную машину и все связанные с ней ресурсы, использовав следующую команду.

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Дальнейшие действия

Изучите [руководство по созданию высокодоступных виртуальных машин](create-cli-complete.md).

[Изучите примеры развертывания виртуальных машин с помощью интерфейса командной строки](cli-samples.md).


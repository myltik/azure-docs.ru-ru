---
title: "Краткое руководство по Azure. Создание виртуальной машины Windows с помощью интерфейса командной строки | Документация Майкрософт"
description: "Быстро научитесь создавать виртуальные машины Windows с помощью Azure CLI."
services: virtual-machines-windows
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 04/03/2017
ms.author: nepeters
translationtype: Human Translation
ms.sourcegitcommit: 303cb9950f46916fbdd58762acd1608c925c1328
ms.openlocfilehash: d131a13d0d347a676c3cff0d668034ffc9373e4c
ms.lasthandoff: 04/04/2017

---

# <a name="create-a-windows-virtual-machine-with-the-azure-cli"></a>Создание виртуальной машины Windows с помощью Azure CLI

Azure CLI используется для создания ресурсов Azure и управления ими из командной строки или с помощью скриптов. В этом руководстве описывается, как с помощью Azure CLI развернуть виртуальную машину под управлением Windows Server 2016. После завершения развертывания мы подключимся к серверу и установим IIS.

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

В следующем примере создается виртуальная машина `myVM`. В этом примере используются имя администратора `azureuser` и его пароль ` myPassword12`. Измените эти значения в соответствии со своей средой. Эти значения нужны для создания подключения к виртуальной машине.

```azurecli
az vm create --resource-group myResourceGroup --name myVM --image win2016datacenter --admin-username azureuser --admin-password myPassword12
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

## <a name="open-port-80-for-web-traffic"></a>Открытие порта 80 для веб-трафика 

По умолчанию виртуальные машины Windows, развернутые в Azure, поддерживают только RDP-подключения. Если эта виртуальная машина будет использоваться в качестве веб-сервера, необходимо открыть порт 80 через Интернет.  Чтобы открыть нужный порт, необходимо выполнить одну команду.  
 
 ```azurecli 
az vm open-port --port 80 --resource-group myResourceGroup --name myVM
```


## <a name="connect-to-virtual-machine"></a>Подключение к виртуальной машине

Используйте следующую команду для создания сеанса удаленного рабочего стола с виртуальной машиной. Замените IP-адрес общедоступным IP-адресом виртуальной машины. При появлении запроса введите учетные данные, использованные при создании виртуальной машины.

```bash 
mstsc /v:<Public IP Address>
```

## <a name="install-iis-using-powershell"></a>Установка IIS с помощью PowerShell

После входа на виртуальную машину Azure вы можете установить IIS и включить локальное правило брандмауэра, разрезающее веб-трафик, с помощью одной строки кода PowerShell.  Откройте командную строку PowerShell и выполните следующую команду:

```powershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

## <a name="view-the-iis-welcome-page"></a>Просмотр страницы приветствия IIS

Установив IIS и открыв через Интернет порт 80 на виртуальной машине, вы можете просмотреть страницу приветствия IIS по умолчанию в любом браузере. Чтобы перейти на страницу по умолчанию, используйте значение `publicIpAddress`, записанное ранее. 

![Сайт IIS по умолчанию](./media/quick-create-powershell/default-iis-website.png) 
## <a name="delete-virtual-machine"></a>Удаление виртуальной машины

Можно удалить ставшие ненужными группу ресурсов, виртуальную машину и все связанные с ней ресурсы, использовав следующую команду.

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Дальнейшие действия

Ознакомьтесь с [руководством по установке роли и настройке брандмауэра](hero-role.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

[Изучите примеры развертывания виртуальных машин с помощью интерфейса командной строки](cli-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

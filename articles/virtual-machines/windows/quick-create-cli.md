---
title: Краткое руководство. Создание виртуальной машины Windows с помощью Azure CLI 2.0 | Документация Майкрософт
description: Из этого краткого руководства вы узнаете, как с помощью Azure CLI 2.0 создать виртуальную машину Windows.
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 04/24/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: b09a85686e19ae92c3e437bedff54bff8371784f
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2018
ms.locfileid: "32182363"
---
# <a name="quickstart-create-a-windows-virtual-machine-with-the-azure-cli-20"></a>Краткое руководство. Создание виртуальной машины Windows с помощью Azure CLI 2.0

Azure CLI 2.0 используется для создания ресурсов Azure и управления ими из командной строки или с помощью сценариев. В этом кратком руководстве показано, как с помощью Azure CLI 2.0 развернуть в Azure виртуальную машину Windows под управлением Windows Server 2016. Чтобы проверить работу виртуальной машины, вы подключитесь к ней по протоколу удаленного рабочего стола (RDP) и установите веб-сервер IIS.

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Если вы решили установить и использовать CLI локально, для выполнения инструкций из этого руководства вам потребуется Azure CLI 2.0.30 или более поздней версии. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Создание группы ресурсов

Создайте группу ресурсов с помощью команды [az group create](/cli/azure/group#az_group_create). Группа ресурсов Azure является логическим контейнером, в котором происходит развертывание ресурсов Azure и управление ими. В следующем примере создается группа ресурсов с именем *myResourceGroup* в расположении *eastus*.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="create-virtual-machine"></a>Создание виртуальной машины

Создайте виртуальную машину с помощью команды [az vm create](/cli/azure/vm#az_vm_create). В следующем примере создается виртуальная машина с именем *myVM*. В этом примере используются имя администратора *azureuser* и его пароль *myPassword12*. Измените эти значения в соответствии со своей средой. Эти значения используются при подключении к виртуальной машине.

```azurecli-interactive
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image win2016datacenter \
    --admin-username azureuser \
    --admin-password myPassword12
```

Создание виртуальной машины и вспомогательных ресурсов занимает несколько минут. В следующем примере выходных данных показано, что виртуальная машина успешно создана.

```azurecli-interactive
{
  "fqdns": "",
  "id": "/subscriptions/<guid>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "52.174.34.95",
  "resourceGroup": "myResourceGroup"
}
```

Запишите значение `publicIpAddress`, которое появится в выходных данных из виртуальной машины. Этот адрес будет использоваться для доступа к виртуальной машине на следующих шагах.

## <a name="open-port-80-for-web-traffic"></a>Открытие порта 80 для веб-трафика

По умолчанию при создании виртуальной машины Windows в Azure открыты только подключения по протоколу RDP. Откройте TCP-порт 80, который понадобится для веб-сервера IIS, с помощью команды [az vm open-port](/cli/azure/vm#az_vm_open_port).

```azurecli-interactive
az vm open-port --port 80 --resource-group myResourceGroup --name myVM
```

## <a name="connect-to-virtual-machine"></a>Подключение к виртуальной машине

На локальном компьютере выполните следующую команду, чтобы создать сеанс удаленного рабочего стола. Замените указанный IP-адрес общедоступным IP-адресом своей виртуальной машины. Когда появится запрос, введите учетные данные, указанные при создании виртуальной машины.

```powershell
mstsc /v:publicIpAddress
```

## <a name="install-web-server"></a>Установка веб-сервера

Чтобы проверить работу виртуальной машины, установите веб-сервер IIS. На виртуальной машине откройте командную строку PowerShell и выполните следующую команду:

```powershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

После этого закройте RDP-подключение к виртуальной машине.

## <a name="view-the-web-server-in-action"></a>Проверка работы веб-сервера

Установив IIS и открыв через Интернет порт 80 на виртуальной машине, откройте страницу приветствия IIS по умолчанию в любом браузере. Используйте общедоступный IP-адрес виртуальной машины, полученный на предыдущем шаге. В следующем примере показан сайт IIS по умолчанию:

![Сайт IIS по умолчанию](./media/quick-create-powershell/default-iis-website.png)

## <a name="clean-up-resources"></a>Очистка ресурсов

Когда группа ресурсов, виртуальная машина и все связанные с ней ресурсы станут ненужны, их можно удалить с помощью команды [az group delete](/cli/azure/group#az_group_delete).

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Дополнительная информация

При работе с этим кратким руководством вы развернули простую виртуальную машину, открыли сетевой порт для веб-трафика и установили простой веб-сервер. Дополнительные сведения о виртуальных машинах Azure см. в руководстве по работе с виртуальными машинами Windows.

> [!div class="nextstepaction"]
> [Создание виртуальных машин Windows и управление ими с помощью модуля Azure PowerShell](./tutorial-manage-vm.md)

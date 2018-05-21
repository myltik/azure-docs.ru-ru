---
title: Краткое руководство по созданию виртуальной машины Linux с помощью Azure CLI 2.0 | Документация Майкрософт
description: Из этого краткого руководства вы узнаете, как с помощью Azure CLI 2.0 создать виртуальную машину Linux.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: iainfoulds
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/24/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 1c45f8f010d69337d21fce327933990a573988a4
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2018
---
# <a name="quickstart-create-a-linux-virtual-machine-with-the-azure-cli-20"></a>Краткое руководство по созданию виртуальной машины Linux с помощью Azure CLI 2.0

Azure CLI 2.0 используется для создания ресурсов Azure и управления ими из командной строки или с помощью сценариев. В этом кратком руководстве показано, как с помощью Azure CLI 2.0 развернуть в Azure виртуальную машину Linux под управлением Ubuntu. Чтобы проверить работу виртуальной машины, вы подключитесь к ней по протоколу SSH и установите веб-сервер NGINX.

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Если вы решили установить и использовать CLI локально, для выполнения инструкций из этого руководства вам потребуется Azure CLI 2.0.30 или более поздней версии. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Создание группы ресурсов

Создайте группу ресурсов с помощью команды [az group create](/cli/azure/group#az_group_create). Группа ресурсов Azure является логическим контейнером, в котором происходит развертывание ресурсов Azure и управление ими. В следующем примере создается группа ресурсов с именем *myResourceGroup* в расположении *eastus*.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="create-virtual-machine"></a>Создание виртуальной машины

Создайте виртуальную машину с помощью команды [az vm create](/cli/azure/vm#az_vm_create).

В следующем примере создаются виртуальная машина с именем *myVM*, добавляется учетная запись пользователя с именем *azureuser* и создаются ключи SSH, если их еще нет в расположении для ключей по умолчанию (*~/.ssh*). Чтобы использовать определенный набор ключей, примените параметр `--ssh-key-value`:

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
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
  "publicIpAddress": "40.68.254.142",
  "resourceGroup": "myResourceGroup"
}
```

Запишите значение `publicIpAddress`, которое появится в выходных данных виртуальной машины. Этот адрес будет использоваться для доступа к виртуальной машине на следующих шагах.

## <a name="open-port-80-for-web-traffic"></a>Открытие порта 80 для веб-трафика

По умолчанию при создании виртуальной машины Linux в Azure открыты только SSH-подключения. Откройте TCP-порт 80, который понадобится для веб-сервера NGINX, с помощью команды [az vm open-port](/cli/azure/vm#az_vm_open_port).

```azurecli-interactive
az vm open-port --port 80 --resource-group myResourceGroup --name myVM
```

## <a name="connect-to-virtual-machine"></a>Подключение к виртуальной машине

Установите SSH-подключение к виртуальной машине в обычном режиме. Замените **publicIpAddress** общедоступным IP-адресом виртуальной машины, который вы записали из выходных данных виртуальной машины на предыдущем шаге.

```bash
ssh azureuser@publicIpAddress
```

## <a name="install-web-server"></a>Установка веб-сервера

Чтобы проверить работу виртуальной машины, установите веб-сервер NGINX. Чтобы обновить источники пакетов и установить последнюю версию пакета NGINX, выполните следующие команды в сеансе SSH:

```bash
# update packages
sudo apt-get -y update

# install NGINX
sudo apt-get -y install nginx
```

По завершении выполните команду `exit` в сеансе SSH.

## <a name="view-the-web-server-in-action"></a>Проверка работы веб-сервера

Установив NGINX и открыв через Интернет порт 80 на виртуальной машине, просмотрите страницу приветствия NGINX по умолчанию в любом браузере. Используйте общедоступный IP-адрес виртуальной машины, полученный на предыдущем шаге. В следующем примере показан сайт NGINX по умолчанию:

![Сайт NGINX по умолчанию](./media/quick-create-cli/nginx.png)

## <a name="clean-up-resources"></a>Очистка ресурсов

Вы можете удалить ставшие ненужными группу ресурсов, виртуальную машину и все связанные с ней ресурсы, выполнив команду [az group delete](/cli/azure/group#az_group_delete). Убедитесь, что вышли из сеанса SSH для виртуальной машины, а затем удалите ресурсы, как показано ниже:

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Дополнительная информация

При работе с этим кратким руководством вы развернули простую виртуальную машину, открыли сетевой порт для веб-трафика и установили базовый веб-сервер. Дополнительные сведения о виртуальных машинах Azure см. в руководстве для виртуальных машин Linux.


> [!div class="nextstepaction"]
> [Создание виртуальных машин Linux и управление ими с помощью Azure CLI](./tutorial-manage-vm.md)

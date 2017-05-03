---
title: "Виртуальные сети Azure и виртуальные машины Linux | Документы Майкрософт"
description: "Руководство по управлению виртуальными сетями Azure и виртуальными машинами Linux с помощью Azure CLI"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: davidmu1
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/18/2017
ms.author: davidmu
translationtype: Human Translation
ms.sourcegitcommit: 9eafbc2ffc3319cbca9d8933235f87964a98f588
ms.openlocfilehash: c1494009c126c4715caf7972a0f59cffdaba3a7f
ms.lasthandoff: 04/22/2017

---

# <a name="manage-azure-virtual-networks-and-linux-virtual-machines-with-the-azure-cli"></a>Управление виртуальными сетями Azure и виртуальными машинами Linux с помощью Azure CLI

Это руководство содержит сведения о создании нескольких виртуальных машин (ВМ) в виртуальной сети и настройках сетевого взаимодействия между ними. После завершения работы "интерфейсная" виртуальная машина будет доступна из Интернета через порт 22 для SSH-соединений и порт 80 для HTTP-соединений. "Серверная" виртуальная машина с базой данных MySQL будет изолирована и доступна только из интерфейсной виртуальной машины через порт 3306.

Для работы с этим руководством можно использовать последнюю версию [Azure CLI 2.0](/cli/azure/install-azure-cli).

## <a name="create-vm-and-vnet"></a>Создание виртуальной машины и виртуальной сети

Виртуальная сеть Azure — это представление сети в облаке. Это логическая изоляция облака Azure, выделенного для вашей подписки. В виртуальной сети находятся подсети, правила для их взаимодействия и подключения от виртуальных машин к подсетям. Azure CLI упрощает создание всех сетевых ресурсов, необходимых для обеспечения доступа к виртуальным машинам. 

Прежде чем создавать другие ресурсы Azure, нужно создать группу ресурсов с помощью команды "az group create". В следующем примере создается группа ресурсов с именем `myRGNetwork` в расположении `westus`:

```azurecli
az group create --name myRGNetwork --location westus
```

При создании виртуальной машины с помощью Azure CLI нужные ей сетевые ресурсы создаются автоматически. Создайте `myFrontendVM` и нужные вспомогательные сетевые ресурсы с помощью команды [az vm create](https://docs.microsoft.com/cli/azure/vm#create):

```azurecli
az vm create \
  --resource-group myRGNetwork \
  --name myFrontendVM \
  --image UbuntuLTS \
  --generate-ssh-keys
```

После создания виртуальной машины запишите общедоступный IP-адрес. Он будет использоваться в последующих шагах данного учебника:

```bash
{
  "fqdns": "",
  "id": "/subscriptions/{id}/resourceGroups/myRGNetwork/providers/Microsoft.Compute/virtualMachines/myFrontendVM",
  "location": "westus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "40.68.254.142",
  "resourceGroup": "myRGNetwork"
}
```

Были созданы следующие сетевые ресурсы:

- **myFrontendVMNSG** — группа безопасности сети, обеспечивающая безопасность входящего трафика для `myFrontendVM`.
- **myVMPublicIP** — общедоступный IP-адрес, предоставляющий доступ из Интернета к `myFrontendVM`.
- **myVMVMNic** — интерфейс виртуальной сети, обеспечивающий подключение к сети для `myFrontendVM`.
- **myVMVNET** — виртуальная сеть, к которой подключена `myFrontendVM`.

## <a name="install-web-server"></a>Установка веб-сервера

Создайте SSH-подключение к `myFrontendVM`. Замените IP-адрес в примере общедоступным IP-адресом виртуальной машины:

```bash
ssh 40.68.254.142
```

Введите следующие команды для установки NGINX:

```bash
sudo apt-get -y update && sudo apt-get -y install nginx
```

Закройте сеанс SSH:

```bash
exit
```

## <a name="manage-internet-traffic"></a>Управление интернет-трафиком

Группа безопасности сети (NSG) содержит перечень правил безопасности, которые разрешают или запрещают передачу сетевого трафика к ресурсам, подключенным к виртуальной сети. Группы безопасности сети можно сопоставить с подсетями или отдельными сетевыми картами, подключенными к виртуальным машинам. Открытие или закрытие доступа к виртуальным машинам осуществляется с помощью правил NSG. При создании `myFrontendVM` входящий порт 22 автоматически был открыт для SSH-взаимодействия.

Откройте порт 80 на `myFrontendVM` с помощью команды [az vm open-port](https://docs.microsoft.com/cli/azure/vm#open-port):

```azurecli
az vm open-port --port 80 --resource-group myRGNetwork --name myFrontendVM
```

Теперь перейдите к общедоступному IP-адресу виртуальной машины для просмотра сайта NGINX.

![Сайт nginx по умолчанию](./media/quick-create-cli/nginx.png)

## <a name="manage-internal-traffic"></a>Управление внутренним трафиком

С помощью группы безопасности сети также можно настроить взаимодействие внутри виртуальных машин. В этом разделе вы узнаете, как создать в сети дополнительную подсеть и назначить ей группу безопасности сети, чтобы разрешить подключение из `myFrontendVM` к `myBackendVM` через порт 3306. После этого подсеть назначается виртуальной машине при ее создании.

Добавьте новую группу безопасности сети `myBackendNSG` с помощью команды [az network nsg create](https://docs.microsoft.com/cli/azure/network/nsg#create). 

```azurecli
az network nsg create \
 --resource-group myRGNetwork \
 --name myBackendNSG
```

Настройте порт, чтобы разрешить `myFrontendVM` и `myBackendVM` взаимодействовать друг с другом в виртуальной сети. Добавьте правило группы безопасности сети, разрешающее трафик в `myBackendSubnet` только из `myVMSubnet`, с помощью команды `az network rule create`:

```azurecli
az network nsg rule create \
 --resource-group myRGNetwork \
 --nsg-name myBackendNSG \
 --name com-rule \
 --access Allow \
 --protocol Tcp \
 --direction Inbound \
 --priority 100 \
 --source-address-prefix 10.0.0.0/24 \
 --source-port-range "*" \
 --destination-address-prefix "*" \
 --destination-port-range 3306
```

## <a name="add-back-end-subnet"></a>Добавление внутренней подсети

Подсеть является дочерним ресурсом виртуальной сети, который помогает определить сегменты адресных пространств в пределах блока CIDR на основе префиксов IP-адресов. Сетевые карты можно добавлять в подсети и подключать к виртуальным машинам, обеспечивая сетевые подключения для различных рабочих нагрузок.

Добавьте `myBackEndSubnet` в `myFrontendVMVNet` с помощью команды [az network vnet subnet create](https://docs.microsoft.com/cli/azure/network/vnet/subnet#create):

```azurecli
az network vnet subnet create \
 --address-prefix 10.0.1.0/24 \
 --name myBackendSubnet \
 --resource-group myRGNetwork \
 --vnet-name myFrontendVMVNET \
 --network-security-group myBackendNSG
```

## <a name="create-back-end-vm"></a>Создание внутренней виртуальной машины

Создайте `myBackendVM`, используя `myBackendSubnet`, с помощью команды `az vm create`:

```azurecli
az vm create \
  --resource-group myRGNetwork \
  --name myBackendVM \
  --image UbuntuLTS \
  --generate-ssh-keys \
  --subnet myBackendSubnet \
  --vnet-name myFrontendVMVNET \
  --public-ip-address ""

```

## <a name="install-database"></a>Установка базы данных

В этом руководстве закрытый ключ копируется из виртуальной машины разработки в `myFrontendVM`. В рабочей среде рекомендуется создавать отдельные ключи для применения на виртуальных машинах, а не использовать параметр --generate-ssh-keys при создании виртуальных машин. 

Внутренняя виртуальная машина не предназначена для открытого доступа. В этом разделе вы узнаете, как использовать SSH для входа в `myFrontendVM`, а также для входа в `myBackendVM` из `myFrontendVM`.

Замените IP-адрес в примере общедоступным IP-адресом `myFrontendVM`:

```bash
scp ~/.ssh/id_rsa 40.68.254.142:~/.ssh/id_rsa
```

Создайте SSH-подключение к `myFrontendVM`. Замените IP-адрес в примере общедоступным IP-адресом `myFrontendVM`:

```bash
ssh 40.68.254.142
```

Подключитесь из `myFrontendVM` к `myBackendVM`:

```bash
ssh myBackendVM
```

Выполните следующую команду, чтобы установить MySQL:

```bash
sudo apt-get -y install mysql-server
```

Следуйте инструкциям по настройке MySQL.

Закройте сеансы SSH:

```bash
exit
```

Установка MySQL демонстрирует, как можно установить приложение в `myBackendVM`, в самом руководстве этот компонент не используется.

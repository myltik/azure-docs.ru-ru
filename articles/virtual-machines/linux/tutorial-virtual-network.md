---
title: "Виртуальные сети Azure и виртуальные машины Linux | Документы Майкрософт"
description: "Руководство по управлению виртуальными сетями Azure и виртуальными машинами Linux с помощью Azure CLI"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/10/2017
ms.author: nepeters
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: a16daa1f320516a771f32cf30fca6f823076aa96
ms.openlocfilehash: 10dda8b93e003c35c2a97a0aa7ca74d04249e52f
ms.contentlocale: ru-ru
ms.lasthandoff: 09/02/2017

---

# <a name="manage-azure-virtual-networks-and-linux-virtual-machines-with-the-azure-cli"></a>Управление виртуальными сетями Azure и виртуальными машинами Linux с помощью Azure CLI

Виртуальные машины Azure осуществляют внутреннее и внешнее взаимодействие через сеть Azure. В этом руководстве содержатся сведения о развертывании двух виртуальных машин и настройке для них сети Azure. Примеры, описанные в этом руководстве, предполагают, что на виртуальных машинах размещается веб-приложение с сервером базы данных, но здесь не описывается развертывание самого приложения. Из этого руководства вы узнаете, как выполнять такие задачи:

> [!div class="checklist"]
> * развернуть виртуальную сеть;
> * создавать подсеть в виртуальной сети;
> * присоединять виртуальную машину к подсети;
> * управлять общедоступными IP-адресами виртуальной машины;
> * защищать входящий интернет-трафик;
> * защищать трафик между виртуальными машинами.


[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Если вы решили установить и использовать интерфейс командной строки локально, то для работы с этим руководством вам понадобится Azure CLI 2.0.4 или более поздней версии. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0]( /cli/azure/install-azure-cli). 

## <a name="vm-networking-overview"></a>Обзор сети виртуальной машины

Виртуальные сети Azure позволяют устанавливать безопасные сетевые подключения между виртуальными машинами, в Интернете, а также между другими службами Azure, такими как база данных SQL Azure. Виртуальные сети разбиты на логические сегменты — подсети. Подсети позволяют контролировать поток сетевого трафика. Это своего рода периметр безопасности. При развертывании виртуальная машина обычно содержит виртуальный сетевой интерфейс, подключенный к подсети.

## <a name="deploy-virtual-network"></a>Развертывание виртуальной сети

В этом руководстве создается виртуальная сеть с двумя подсетями: интерфейсная подсеть для размещения веб-приложения и внутренняя подсеть для размещения сервера базы данных.

Прежде чем создать виртуальную сеть, выполните команду [az group create](/cli/azure/group#create), чтобы создать группу ресурсов. Следующий пример позволяет создать группу ресурсов *myRGNetwork* в расположении eastus.

```azurecli-interactive 
az group create --name myRGNetwork --location eastus
```

### <a name="create-virtual-network"></a>Создание виртуальной сети

Создайте виртуальную сеть с помощью команды [az network vnet create](/cli/azure/network/vnet#create). В этом примере создается сеть *mvVnet* с префиксом адреса *10.0.0.0/16*, а также подсеть *mySubnetFrontEnd* с префиксом *10.0.1.0/24*. Далее в этом руководстве мы подключим интерфейсную виртуальную машину к этой подсети. 

```azurecli-interactive 
az network vnet create \
  --resource-group myRGNetwork \
  --name myVnet \
  --address-prefix 10.0.0.0/16 \
  --subnet-name mySubnetFrontEnd \
  --subnet-prefix 10.0.1.0/24
```

### <a name="create-subnet"></a>Создание подсети

Добавьте новую подсеть к виртуальной сети с помощью команды [az network vnet subnet create](/cli/azure/network/vnet/subnet#create). В этом примере создается подсеть *mySubnetBackEnd* с префиксом адреса *10.0.2.0/24*. Она используется со всеми внутренними службами.

```azurecli-interactive 
az network vnet subnet create \
  --resource-group myRGNetwork \
  --vnet-name myVnet \
  --name mySubnetBackEnd \
  --address-prefix 10.0.2.0/24
```

На этом этапе мы создали сеть и разбили ее на две подсети — одна для интерфейсных служб, а вторая для внутренних служб. В следующем разделе мы создадим виртуальные машины и подключим их к этим подсетям.

## <a name="understand-public-ip-address"></a>Общие сведения об общедоступном IP-адресе

Общедоступный IP-адрес обеспечивает доступность ресурсов Azure в Интернете. В этом разделе руководства рассматривается настройка общедоступного IP-адреса для созданной виртуальной машины.

### <a name="allocation-method"></a>Способ выделения

Общедоступный IP-адрес может быть динамическим или статическим. По умолчанию выделяется динамический общедоступный IP-адрес. После освобождения виртуальной машины освобождаются также и динамические IP-адреса. Таким образом, IP-адреса изменяются во время каждой операции с освобождением виртуальной машины.

При использовании статического метода выделения IP-адрес остается назначенным виртуальной машине, даже если она в освобожденном состоянии. В этом случае вы не сможете указать IP-адрес. Он выделяется из пула доступных адресов.

### <a name="dynamic-allocation"></a>Динамическое выделение

При создании виртуальной машины с помощью команды [az vm create](/cli/azure/vm#create) по умолчанию выделяется динамический общедоступный IP-адрес. Следующий пример позволяет создать виртуальную машину с динамическим IP-адресом. 

```azurecli-interactive 
az vm create \
  --resource-group myRGNetwork \
  --name myFrontEndVM \
  --vnet-name myVnet \
  --subnet mySubnetFrontEnd \
  --nsg myNSGFrontEnd \
  --public-ip-address myFrontEndIP \
  --image UbuntuLTS \
  --generate-ssh-keys
```

### <a name="static-allocation"></a>Статическое выделение

При создании виртуальной машины с помощью команды [az vm create](/cli/azure/vm#create) добавьте аргумент `--public-ip-address-allocation static`, чтобы назначить статический общедоступный IP-адрес. Эта операция не описана в этом руководстве, но в следующем разделе показано, как изменить динамический IP-адрес на статический. 

### <a name="change-allocation-method"></a>Изменение метода выделения

Метод выделения IP-адресов можно изменить с помощью команды [az network public-ip update](/cli/azure/network/public-ip#update). В этом примере метод выделения IP-адреса интерфейсной виртуальной машины изменяется на статический.

Сначала отмените подготовку виртуальной машины.

```azurecli-interactive 
az vm deallocate --resource-group myRGNetwork --name myFrontEndVM
```

Обновите метод выделения, используя команду [az network public-ip update](/cli/azure/network/public-ip#update). В этом случае для параметра `--allocation-method` необходимо задать значение *static*.

```azurecli-interactive 
az network public-ip update --resource-group myRGNetwork --name myFrontEndIP --allocation-method static
```

Запустите виртуальную машину.

```azurecli-interactive 
az vm start --resource-group myRGNetwork --name myFrontEndVM --no-wait
```

### <a name="no-public-ip-address"></a>Создание виртуальной машины без общедоступного IP-адреса

Во многих случаях виртуальная машина не должна быть доступна через Интернет. Чтобы создать виртуальную машину без общедоступного IP-адреса, используйте аргумент `--public-ip-address ""` с пустыми двойными кавычками. Эта конфигурация рассматривается далее в этом руководстве.

## <a name="secure-network-traffic"></a>защищают сетевой трафик;

Группа безопасности сети (NSG) содержит перечень правил безопасности, которые разрешают или запрещают передачу сетевого трафика к ресурсам, подключенным к виртуальным сетям Azure. Группы безопасности сети (NSG) можно связать с подсетями или отдельными сетевыми интерфейсами. Группа безопасности сети, связанная с сетевым интерфейсом, применяется только к связанной виртуальной машине. Если группа безопасности сети связана с подсетью, эти правила применяются ко всем ресурсам в подсети. 

### <a name="network-security-group-rules"></a>Правила группы безопасности сети

Правила группы безопасности сети определяют сетевые порты, через которые разрешен или запрещен трафик. Эти правила также могут включать исходные и целевые диапазоны IP-адресов, что позволяет контролировать трафик между определенными системами и подсетями. Правилам также присваивается приоритет (от 1 до 4096), и они оцениваются в порядке приоритета. Правило с приоритетом 100 оценивается перед правилом с приоритетом 200.

Все группы NSG содержат набор правил по умолчанию. Эти правила нельзя удалить, но у них самый низкий приоритет, поэтому их можно переопределить, создав другие правила.

- **Виртуальная сеть.** Входящий и исходящий трафик виртуальной сети разрешен в обоих направлениях.
- **Интернет.** Исходящий трафик разрешен, но входящий трафик заблокирован.
- **Подсистема балансировки нагрузки.** Разрешает Azure Load Balancer инициировать проверку работоспособности виртуальных машин и экземпляров роли. Если набор балансировки нагрузки не используется, это правило можно переопределить.

### <a name="create-network-security-groups"></a>Создание групп безопасности сети

Группу безопасности сети можно создать вместе с виртуальной машиной, используя команду [az vm create](/cli/azure/vm#create). В этом случае группа безопасности сети связывается с сетевым интерфейсом виртуальных машин и автоматически создается правило NSG, разрешающее прохождение трафика через порт *22* из любого источника. Ранее в этом руководстве мы создали интерфейсную виртуальную машину, вместе с которой была автоматически создана группа безопасности сети переднего плана. Кроме того, было также создано правило NSG для порта 22. 

В некоторых случаях есть смысл заранее создать группу безопасности сети, например, если не нужно создавать правила SSH по умолчанию или если группу безопасности сети нужно присоединить к подсети. 

Создайте группу безопасности сети с помощью команды [az network nsg create](/cli/azure/network/nsg#create).

```azurecli-interactive 
az network nsg create --resource-group myRGNetwork --name myNSGBackEnd
```

В этом случае группа безопасности сети будет связана с подсетью, а не сетевым интерфейсом. В этой конфигурации все виртуальные машины, подключенные к подсети, наследуют правила NSG.

Обновите имеющуюся подсеть *mySubnetBackEnd*, включив в нее новую группу безопасности сети.

```azurecli-interactive 
az network vnet subnet update \
  --resource-group myRGNetwork \
  --vnet-name myVnet \
  --name mySubnetBackEnd \
  --network-security-group myNSGBackEnd
```

Теперь создайте виртуальную машину и присоедините ее к подсети *mySubnetBackEnd*. Обратите внимание, что в качестве значения аргумента `--nsg` используются пустые двойные кавычки. Вместе с виртуальной машиной не нужно создавать группу безопасности сети. Виртуальная машина будет подключена к внутренней подсети, которая защищена с помощью предварительно созданной внутренней группы безопасности сети. Эта группа безопасности сети применяется к виртуальной машине. Кроме того, обратите внимание, что в качестве значения аргумента `--public-ip-address` используются пустые двойные кавычки. Эта конфигурация создает виртуальную машину без общедоступного IP-адреса. 

```azurecli-interactive 
az vm create \
  --resource-group myRGNetwork \
  --name myBackEndVM \
  --vnet-name myVnet \
  --subnet mySubnetBackEnd \
  --public-ip-address "" \
  --nsg "" \
  --image UbuntuLTS \
  --generate-ssh-keys
```

### <a name="secure-incoming-traffic"></a>Защита входящего трафика

При создании интерфейсной виртуальной машины также было создано правило NSG, разрешающее входящий трафик через порт 22. Это правило разрешает SSH-подключения к виртуальной машине. В этом примере трафик также необходимо разрешить через порт *80*. Такая конфигурация обеспечивает доступ к веб-приложению на виртуальной машине.

Создайте правило для порта *80* с помощью команды [az network nsg rule create](/cli/azure/network/nsg/rule#create).

```azurecli-interactive 
az network nsg rule create \
  --resource-group myRGNetwork \
  --nsg-name myNSGFrontEnd \
  --name http \
  --access allow \
  --protocol Tcp \
  --direction Inbound \
  --priority 200 \
  --source-address-prefix "*" \
  --source-port-range "*" \
  --destination-address-prefix "*" \
  --destination-port-range 80
```

Теперь интерфейсная виртуальная машина доступна только через порты *22* и *80*. Весь остальной входящий трафик блокируется группой безопасности сети. Иногда есть смысл визуализировать конфигурации правил NSG. Получите конфигурацию правила NSG с помощью команды [az network rule list](/cli/azure/network/nsg/rule#list). 

```azurecli-interactive 
az network nsg rule list --resource-group myRGNetwork --nsg-name myNSGFrontEnd --output table
```

Выходные данные:

```azurecli-interactive 
Access    DestinationAddressPrefix      DestinationPortRange  Direction    Name                 Priority  Protocol    ProvisioningState    ResourceGroup    SourceAddressPrefix    SourcePortRange
--------  --------------------------  ----------------------  -----------  -----------------  ----------  ----------  -------------------  ---------------  ---------------------  -----------------
Allow     *                                               22  Inbound      default-allow-ssh        1000  Tcp         Succeeded            myRGNetwork      *                      *
Allow     *                                               80  Inbound      http                      200  Tcp         Succeeded            myRGNetwork      *                      *
```

### <a name="secure-vm-to-vm-traffic"></a>Защита трафика между виртуальными машинами

Правила группы безопасности сети также можно применить между виртуальными машинами. В этом примере интерфейсная виртуальная машина должна взаимодействовать с внутренней виртуальной машиной через порт *22* и *3306*. Эта конфигурация разрешает SSH-подключения из интерфейсной виртуальной машины, а также разрешает приложению, расположенному на этой виртуальной машине, взаимодействовать с внутренней базой данных MySQL. Весь остальной трафик между интерфейсной и внутренней виртуальными машинами должен блокироваться.

Создайте правило для порта 22 с помощью команды [az network nsg rule create](/cli/azure/network/nsg/rule#create). Обратите внимание, что аргумент `--source-address-prefix` имеет значение *10.0.1.0/24*. Такая конфигурация гарантирует, что только трафик из интерфейсной подсети сможет пройти через группу безопасности сети.

```azurecli-interactive 
az network nsg rule create \
  --resource-group myRGNetwork \
  --nsg-name myNSGBackEnd \
  --name SSH \
  --access Allow \
  --protocol Tcp \
  --direction Inbound \
  --priority 100 \
  --source-address-prefix 10.0.1.0/24 \
  --source-port-range "*" \
  --destination-address-prefix "*" \
  --destination-port-range "22"
```

Теперь добавьте правило, разрешающее трафик MySQL через порт 3306.

```azurecli-interactive 
az network nsg rule create \
  --resource-group myRGNetwork \
  --nsg-name myNSGBackEnd \
  --name MySQL \
  --access Allow \
  --protocol Tcp \
  --direction Inbound \
  --priority 200 \
  --source-address-prefix 10.0.1.0/24 \
  --source-port-range "*" \
  --destination-address-prefix "*" \
  --destination-port-range "3306"
```

И наконец, так как группы безопасности сети имеют правила по умолчанию, разрешающие весь трафик между виртуальными машинами в той же виртуальной сети, вы можете создать правило блокировки всего трафика во внутренних группах безопасности сети. Обратите внимание, что аргумент `--priority` имеет значение *300*, что меньше значения приоритета правил NSG и MySQL. Такая конфигурация гарантирует, что трафик SSH и MySQL по-прежнему сможет проходить через группу безопасности сети.

```azurecli-interactive 
az network nsg rule create \
  --resource-group myRGNetwork \
  --nsg-name myNSGBackEnd \
  --name denyAll \
  --access Deny \
  --protocol Tcp \
  --direction Inbound \
  --priority 300 \
  --source-address-prefix "*" \
  --source-port-range "*" \
  --destination-address-prefix "*" \
  --destination-port-range "*"
```

Теперь внутренняя виртуальная машина доступна только через порты *22* и *3306* из интерфейсной подсети. Весь остальной входящий трафик блокируется группой безопасности сети. Иногда есть смысл визуализировать конфигурации правил NSG. Получите конфигурацию правила NSG с помощью команды [az network rule list](/cli/azure/network/nsg/rule#list). 

```azurecli-interactive 
az network nsg rule list --resource-group myRGNetwork --nsg-name myNSGBackEnd --output table
```

Выходные данные:

```azurecli-interactive 
Access    DestinationAddressPrefix    DestinationPortRange    Direction    Name       Priority  Protocol    ProvisioningState    ResourceGroup    SourceAddressPrefix    SourcePortRange
--------  --------------------------  ----------------------  -----------  -------  ----------  ----------  -------------------  ---------------  ---------------------  -----------------
Allow     *                           22                      Inbound      SSH             100  Tcp         Succeeded            myRGNetwork      10.0.1.0/24            *
Allow     *                           3306                    Inbound      MySQL           200  Tcp         Succeeded            myRGNetwork      10.0.1.0/24            *
Deny      *                           *                       Inbound      denyAll         300  Tcp         Succeeded            myRGNetwork      *                      *
```

## <a name="next-steps"></a>Дальнейшие действия

В этом руководстве вы создали и защитили сети Azure с точки зрения виртуальных машин. Вы научились выполнять следующие задачи:

> [!div class="checklist"]
> * развертывать виртуальную сеть;
> * создавать подсеть в виртуальной сети;
> * присоединять виртуальную машину к подсети;
> * управлять общедоступными IP-адресами виртуальной машины;
> * защищать входящий интернет-трафик;
> * защищать трафик между виртуальными машинами.

Перейдите к следующему руководству, чтобы узнать о защите данных на виртуальных машинах с помощью службы архивации Azure. 

> [!div class="nextstepaction"]
> [Архивация виртуальных машин Linux в Azure](./tutorial-backup-vms.md)


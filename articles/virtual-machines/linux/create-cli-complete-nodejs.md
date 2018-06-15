---
title: Создание полной среды Linux с помощью Azure CLI 1.0 | Документация Майкрософт
description: Узнайте, как с помощью Azure CLI 1.0 создать "с нуля" хранилище, виртуальную машину Linux, виртуальную сеть и подсеть, балансировщик нагрузки, сетевую карту, общедоступный IP-адрес и группу безопасности сети.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: iainfoulds
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 4ba4060b-ce95-4747-a735-1d7c68597a1a
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/09/2017
ms.author: iainfou
ms.openlocfilehash: 4a43e138d3497e01fe9e0e5c55a4a66adac767c6
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/06/2018
ms.locfileid: "30910770"
---
# <a name="create-a-complete-linux-environment-with-the-azure-cli-10"></a>Создание полной среды Linux с помощью Azure CLI 1.0
В этой статье мы создаем простую сеть с балансировщиком нагрузки и парой виртуальных машин, подходящих для разработки и простых вычислений. Мы поэтапно выполняем полное развертывание, от первой до последней команды, в результате чего создаем две защищенные рабочие виртуальные машины Linux, к которым можно подключиться откуда угодно через Интернет. Затем вы сможете работать с более сложными сетями и средами.

Попутно вы ознакомитесь с иерархией зависимостей модели развертывания с помощью Resource Manager и узнаете, какие возможности она дает. Узнав, как устроена система, вы сможете воссоздать ее гораздо быстрее с помощью [шаблонов Azure Resource Manager](../../resource-group-authoring-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Кроме того, когда вы узнаете, как взаимодействуют части среды, создавать шаблоны для их автоматизации станет проще.

Наша среда содержит:

* две виртуальные машины в группе доступности;
* балансировщик нагрузки с правилом балансировки нагрузки для порта 80;
* правила группы безопасности сети для защиты виртуальных машин от нежелательного трафика.

Чтобы создать эту настраиваемую среду, потребуется последняя версия [Azure CLI 1.0](../../cli-install-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) в режиме Resource Manager (`azure config mode arm`). Кроме того, потребуется инструмент анализа JSON. В этом примере используются [jq](https://stedolan.github.io/jq/).


## <a name="cli-versions-to-complete-the-task"></a>Версии интерфейса командной строки для выполнения задачи
Вы можете выполнить задачу, используя одну из следующих версий интерфейса командной строки.

- [Azure CLI 1.0](#quick-commands) — интерфейс командной строки для классической модели развертывания и модели развертывания Resource Manager (в этой статье).
- [Azure CLI 2.0](create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) — интерфейс командной строки следующего поколения для модели развертывания с помощью Resource Manager.


## <a name="quick-commands"></a>Быстрые команды
Если вам необходимо быстро выполнить задачу, в следующем разделе описаны основные команды для отправки виртуальной машины в Azure. Дополнительные сведения и контекст для каждого этапа можно найти в остальной части документа, [начиная отсюда](#detailed-walkthrough).

Войдите в [Azure CLI 1.0](../../cli-install-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) и перейдите в режим Resource Manager.

```azurecli
azure config mode arm
```

В следующих примерах замените имена параметров собственными значениями. Используемые имена параметров: `myResourceGroup`, `mystorageaccount` и `myVM`.

Создайте группу ресурсов. В следующем примере создается группа ресурсов с именем `myResourceGroup` в расположении `westeurope`:

```azurecli
azure group create -n myResourceGroup -l westeurope
```

Проверьте группу ресурсов с помощью средства синтаксического анализа JSON.

```azurecli
azure group show myResourceGroup --json | jq '.'
```

Создайте учетную запись хранения. В следующем примере создается учетная запись хранения с именем `mystorageaccount` (Имя учетной записи хранения должно быть уникальным, поэтому введите собственное имя для учетной записи.)

```azurecli
azure storage account create -g myResourceGroup -l westeurope \
  --kind Storage --sku-name GRS mystorageaccount
```

Проверьте ее с помощью средства синтаксического анализа JSON.

```azurecli
azure storage account show -g myResourceGroup mystorageaccount --json | jq '.'
```

Создание виртуальной сети. В следующем примере создается виртуальная сеть с именем `myVnet`.

```azurecli
azure network vnet create -g myResourceGroup -l westeurope\
  -n myVnet -a 192.168.0.0/16
```

Создайте подсеть. В следующем примере создается подсеть с именем `mySubnet`

```azurecli
azure network vnet subnet create -g myResourceGroup \
  -e myVnet -n mySubnet -a 192.168.1.0/24
```

Проверьте виртуальную сеть и подсеть с помощью средства синтаксического анализа JSON.

```azurecli
azure network vnet show myResourceGroup myVnet --json | jq '.'
```

Создайте общедоступный IP-адрес. В следующем примере создается общедоступный IP-адрес `myPublicIP` с DNS-именем `mypublicdns`. (DNS-имя должно быть уникальным, поэтому укажите собственное уникальное имя.)

```azurecli
azure network public-ip create -g myResourceGroup -l westeurope \
  -n myPublicIP  -d mypublicdns -a static -i 4
```

Создайте балансировщик нагрузки. В следующем примере создается балансировщик нагрузки с именем `myLoadBalancer`.

```azurecli
azure network lb create -g myResourceGroup -l westeurope -n myLoadBalancer
```

Создайте интерфейсный пул IP-адресов для балансировщика нагрузки и привяжите к нему общедоступный IP-адрес. В следующем примере создается интерфейсный пул IP-адресов с именем `mySubnetPool`.

```azurecli
azure network lb frontend-ip create -g myResourceGroup -l myLoadBalancer \
  -i myPublicIP -n myFrontEndPool
```

Создайте внутренний пул IP-адресов для балансировщика нагрузки. В следующем примере создается внутренний пул IP-адресов с именем `myBackEndPool`.

```azurecli
azure network lb address-pool create -g myResourceGroup -l myLoadBalancer \
  -n myBackEndPool
```

Создайте правила преобразования сетевых адресов (NAT) для входящего трафика SSH для балансировщика нагрузки. В следующем примере создается два правила балансировщика нагрузки (`myLoadBalancerRuleSSH1` и `myLoadBalancerRuleSSH2`).

```azurecli
azure network lb inbound-nat-rule create -g myResourceGroup -l myLoadBalancer \
  -n myLoadBalancerRuleSSH1 -p tcp -f 4222 -b 22
azure network lb inbound-nat-rule create -g myResourceGroup -l myLoadBalancer \
  -n myLoadBalancerRuleSSH2 -p tcp -f 4223 -b 22
```

Создайте правила NAT для входящего веб-трафика для балансировщика нагрузки. В следующем примере создается правило балансировщика нагрузки с именем `myLoadBalancerRuleWeb`:

```azurecli
azure network lb rule create -g myResourceGroup -l myLoadBalancer \
  -n myLoadBalancerRuleWeb -p tcp -f 80 -b 80 \
  -t myFrontEndPool -o myBackEndPool
```

Создайте пробу работоспособности балансировщика нагрузки. В следующем примере создается TCP-проба с именем `myHealthProbe`.

```azurecli
azure network lb probe create -g myResourceGroup -l myLoadBalancer \
  -n myHealthProbe -p "tcp" -i 15 -c 4
```

Проверьте балансировщик нагрузки, пулы IP-адресов и правила NAT с помощью средства синтаксического анализа JSON.

```azurecli
azure network lb show -g myResourceGroup -n myLoadBalancer --json | jq '.'
```

Создайте первый сетевой адаптер (NIC). Замените `#####-###-###` собственным идентификатором подписки Azure. Идентификатор подписки указан в выходных данных **jq**, которые можно просмотреть при проверке создаваемых ресурсов. Кроме того, его можно просмотреть, выполнив команду `azure account list`.

В следующем примере создается сетевой адаптер с именем `myNic1`.

```azurecli
azure network nic create -g myResourceGroup -l westeurope \
  -n myNic1 -m myVnet -k mySubnet \
  -d "/subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool" \
  -e "/subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH1"
```

Создайте второй сетевой адаптер. В следующем примере создается сетевой адаптер с именем `myNic2`.

```azurecli
azure network nic create -g myResourceGroup -l westeurope \
  -n myNic2 -m myVnet -k mySubnet \
  -d "/subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool" \
  -e "/subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH2"
```

Проверьте эти сетевые адаптеры с помощью средства синтаксического анализа JSON.

```azurecli
azure network nic show myResourceGroup myNic1 --json | jq '.'
azure network nic show myResourceGroup myNic2 --json | jq '.'
```

Создайте группу безопасности сети. В следующем примере создается группа безопасности сети с именем `myNetworkSecurityGroup`.

```azurecli
azure network nsg create -g myResourceGroup -l westeurope \
  -n myNetworkSecurityGroup
```

Добавьте для группы безопасности сети два правила входящего трафика. В следующем примере создается два правила (`myNetworkSecurityGroupRuleSSH` и `myNetworkSecurityGroupRuleHTTP`).

```azurecli
azure network nsg rule create -p tcp -r inbound -y 1000 -u 22 -c allow \
  -g myResourceGroup -a myNetworkSecurityGroup -n myNetworkSecurityGroupRuleSSH
azure network nsg rule create -p tcp -r inbound -y 1001 -u 80 -c allow \
  -g myResourceGroup -a myNetworkSecurityGroup -n myNetworkSecurityGroupRuleHTTP
```

Проверьте группу безопасности сети и правила входящего трафика с помощью средства синтаксического анализа JSON.

```azurecli
azure network nsg show -g myResourceGroup -n myNetworkSecurityGroup --json | jq '.'
```

Привяжите два сетевых адаптера к группе безопасности сети.

```azurecli
azure network nic set -g myResourceGroup -o myNetworkSecurityGroup -n myNic1
azure network nic set -g myResourceGroup -o myNetworkSecurityGroup -n myNic2
```

Создайте группу доступности. В следующем примере создается группа доступности с именем `myAvailabilitySet`.

```azurecli
azure availset create -g myResourceGroup -l westeurope -n myAvailabilitySet
```

Создайте первую виртуальную машину Linux. В следующем примере создается виртуальная машина с именем `myVM1`.

```azurecli
azure vm create \
    --resource-group myResourceGroup \
    --name myVM1 \
    --location westeurope \
    --os-type linux \
    --availset-name myAvailabilitySet \
    --nic-name myNic1 \
    --vnet-name myVnet \
    --vnet-subnet-name mySubnet \
    --storage-account-name mystorageaccount \
    --image-urn canonical:UbuntuServer:16.04.0-LTS:latest \
    --ssh-publickey-file ~/.ssh/id_rsa.pub \
    --admin-username azureuser
```

Создайте вторую виртуальную машину Linux. В следующем примере создается виртуальная машина с именем `myVM2`.

```azurecli
azure vm create \
    --resource-group myResourceGroup \
    --name myVM2 \
    --location westeurope \
    --os-type linux \
    --availset-name myAvailabilitySet \
    --nic-name myNic2 \
    --vnet-name myVnet \
    --vnet-subnet-name mySubnet \
    --storage-account-name mystorageaccount \
    --image-urn canonical:UbuntuServer:16.04.0-LTS:latest \
    --ssh-publickey-file ~/.ssh/id_rsa.pub \
    --admin-username azureuser
```

С помощью средства синтаксического анализа JSON проверьте все созданные компоненты.

```azurecli
azure vm show -g myResourceGroup -n myVM1 --json | jq '.'
azure vm show -g myResourceGroup -n myVM2 --json | jq '.'
```

Экспортируйте новую среду в шаблон, чтобы быстро воссоздавать новые экземпляры.

```azurecli
azure group export myResourceGroup
```

## <a name="detailed-walkthrough"></a>Подробное пошаговое руководство
Подробные инструкции, приведенные ниже, поясняют действия каждой команды при создании вашей среды. Эти понятия помогут вам при создании пользовательских сред для разработки или эксплуатации.

Войдите в [Azure CLI 1.0](../../cli-install-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) и перейдите в режим Resource Manager.

```azurecli
azure config mode arm
```

В следующих примерах замените имена параметров собственными значениями. Используемые имена параметров: `myResourceGroup`, `mystorageaccount` и `myVM`.

## <a name="create-resource-groups-and-choose-deployment-locations"></a>Создание групп ресурсов и выбор расположений развертывания
Группы ресурсов Azure — это логические сущности развертывания, содержащие данные конфигурации и другие метаданные, которые делают возможным логическое управление развертыванием ресурсов. В следующем примере создается группа ресурсов с именем `myResourceGroup` в расположении `westeurope`:

```azurecli
azure group create --name myResourceGroup --location westeurope
```

Выходные данные:

```azurecli                        
info:    Executing command group create
+ Getting resource group myResourceGroup
+ Creating resource group myResourceGroup
info:    Created resource group myResourceGroup
data:    Id:                  /subscriptions/guid/resourceGroups/myResourceGroup
data:    Name:                myResourceGroup
data:    Location:            westeurope
data:    Provisioning State:  Succeeded
data:    Tags: null
data:
info:    group create command OK
```

## <a name="create-a-storage-account"></a>Создайте учетную запись хранения.
Необходимы учетные записи хранения для дисков виртуальной машины и других дисков данных, которые вы захотите добавить. Учетные записи хранения создаются практически сразу после создания групп ресурсов.

В этом случае мы используем команду `azure storage account create` и с ее помощью передаем расположение учетной записи, имя группы ресурсов, которая ее контролирует, и нужный вам тип поддержки хранилища. В следующем примере создается учетная запись хранения с именем `mystorageaccount`:

```azurecli
azure storage account create \  
  --location westeurope \
  --resource-group myResourceGroup \
  --kind Storage --sku-name GRS \
  mystorageaccount
```

Выходные данные:

```azurecli
info:    Executing command storage account create
+ Creating storage account
info:    storage account create command OK
```

Чтобы проверить нашу группу ресурсов с помощью команды `azure group show`, используйте средство [jq](https://stedolan.github.io/jq/) с параметром `--json` интерфейса командной строки Azure. (Можно использовать **jsawk** или любую предпочитаемую библиотеку языка для анализа JSON.)

```azurecli
azure group show myResourceGroup --json | jq '.'
```

Выходные данные:

```json
{
  "tags": {},
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup",
  "name": "myResourceGroup",
  "provisioningState": "Succeeded",
  "location": "westeurope",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "resources": [
    {
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
      "name": "mystorageaccount",
      "type": "storageAccounts",
      "location": "westeurope",
      "tags": null
    }
  ],
  "permissions": [
    {
      "actions": [
        "*"
      ],
      "notActions": []
    }
  ]
}
```

Чтобы исследовать учетную запись хранения с помощью интерфейса командной строки, нужно сначала задать имена и ключи учетных записей. Замените имя учетной записи хранения в следующем примере выбранным вами именем.

```bash
export AZURE_STORAGE_CONNECTION_STRING="$(azure storage account connectionstring show mystorageaccount --resource-group myResourceGroup --json | jq -r '.string')"
```

Это позволит с легкостью просматривать сведения о хранилище.

```azurecli
azure storage container list
```

Выходные данные:

```azurecli
info:    Executing command storage container list
+ Getting storage containers
data:    Name  Public-Access  Last-Modified
data:    ----  -------------  -----------------------------
data:    vhds  Off            Sun, 27 Sep 2015 19:03:54 GMT
info:    storage container list command OK
```

## <a name="create-a-virtual-network-and-subnet"></a>Создание виртуальной сети и подсети
Далее вам понадобится создать виртуальную сеть в Azure и подсеть, в которой можно создать виртуальные машины. В следующем примере создается виртуальная сеть с именем `myVnet` и префиксом адреса `192.168.0.0/16`.

```azurecli
azure network vnet create --resource-group myResourceGroup --location westeurope \
  --name myVnet --address-prefixes 192.168.0.0/16
```

Выходные данные:

```azurecli
info:    Executing command network vnet create
+ Looking up virtual network "myVnet"
+ Creating virtual network "myVnet"
+ Loading virtual network state
data:    Id                              : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet
data:    Name                            : myVnet
data:    Type                            : Microsoft.Network/virtualNetworks
data:    Location                        : westeurope
data:    ProvisioningState               : Succeeded
data:    Address prefixes:
data:      192.168.0.0/16
info:    network vnet create command OK
```

Опять же, давайте посмотрим, как создаются ресурсы с помощью параметра --json команды `azure group show` и инструмента `jq`. Теперь у нас есть ресурс `storageAccounts` и ресурс `virtualNetworks`.  

```azurecli
azure group show myResourceGroup --json | jq '.'
```

Выходные данные:

```json
{
  "tags": {},
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup",
  "name": "myResourceGroup",
  "provisioningState": "Succeeded",
  "location": "westeurope",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "resources": [
    {
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet",
      "name": "myVnet",
      "type": "virtualNetworks",
      "location": "westeurope",
      "tags": null
    },
    {
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
      "name": "mystorageaccount",
      "type": "storageAccounts",
      "location": "westeurope",
      "tags": null
    }
  ],
  "permissions": [
    {
      "actions": [
        "*"
      ],
      "notActions": []
    }
  ]
}
```

Теперь давайте создадим подсеть в виртуальной сети `myVnet`, в которой выполняется развертывание виртуальных машин. Мы используем команду `azure network vnet subnet create` с уже созданными ресурсами: группой ресурсов `myResourceGroup` и виртуальной сетью `myVnet`. В следующем примере мы добавим подсеть `mySubnet` с префиксом адреса `192.168.1.0/24`.

```azurecli
azure network vnet subnet create --resource-group myResourceGroup \
  --vnet-name myVnet --name mySubnet --address-prefix 192.168.1.0/24
```

Выходные данные:

```azurecli
info:    Executing command network vnet subnet create
+ Looking up the subnet "mySubnet"
+ Creating subnet "mySubnet"
+ Looking up the subnet "mySubnet"
data:    Id                              : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet
data:    Type                            : Microsoft.Network/virtualNetworks/subnets
data:    ProvisioningState               : Succeeded
data:    Name                            : mySubnet
data:    Address prefix                  : 192.168.1.0/24
data:
info:    network vnet subnet create command OK
```

Так как логически подсеть находится внутри виртуальной сети, информацию о подсети мы ищем с помощью немного другой команды. Мы используем команду `azure network vnet show`, но проверяем выходные данные JSON по-прежнему с помощью `jq`.

```azurecli
azure network vnet show myResourceGroup myVnet --json | jq '.'
```

Выходные данные:

```json
{
  "subnets": [
    {
      "ipConfigurations": [],
      "addressPrefix": "192.168.1.0/24",
      "provisioningState": "Succeeded",
      "name": "mySubnet",
      "etag": "W/\"974f3e2c-028e-4b35-832b-a4b16ad25eb6\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet"
    }
  ],
  "tags": {},
  "addressSpace": {
    "addressPrefixes": [
      "192.168.0.0/16"
    ]
  },
  "dhcpOptions": {
    "dnsServers": []
  },
  "provisioningState": "Succeeded",
  "etag": "W/\"974f3e2c-028e-4b35-832b-a4b16ad25eb6\"",
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet",
  "name": "myVnet",
  "location": "westeurope"
}
```

## <a name="create-a-public-ip-address"></a>Создание общедоступного IP-адреса
Теперь давайте создадим общедоступный IP-адрес (PIP), который назначается балансировщику нагрузки. Он позволит подключаться к виртуальным машинам из Интернета с помощью команды `azure network public-ip create` . Так как по умолчанию используется динамический адрес, мы создаем именованную запись DNS в домене **cloudapp.azure.com** с помощью параметра `--domain-name-label`. В следующем примере создается общедоступный IP-адрес `myPublicIP` с DNS-именем `mypublicdns`. DNS-имя должно быть уникальным, поэтому укажите собственное уникальное имя.

```azurecli
azure network public-ip create --resource-group myResourceGroup \
  --location westeurope --name myPublicIP --domain-name-label mypublicdns
```

Выходные данные:

```azurecli
info:    Executing command network public-ip create
+ Looking up the public ip "myPublicIP"
+ Creating public ip address "myPublicIP"
+ Looking up the public ip "myPublicIP"
data:    Id                              : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP
data:    Name                            : myPublicIP
data:    Type                            : Microsoft.Network/publicIPAddresses
data:    Location                        : westeurope
data:    Provisioning state              : Succeeded
data:    Allocation method               : Dynamic
data:    Idle timeout                    : 4
data:    Domain name label               : mypublicdns
data:    FQDN                            : mypublicdns.westeurope.cloudapp.azure.com
info:    network public-ip create command OK
```

Кроме того, IP-адрес — это ресурс верхнего уровня, поэтому его можно отобразить с помощью команды `azure group show`.

```azurecli
azure group show myResourceGroup --json | jq '.'
```

Выходные данные:

```json
{
"tags": {},
"id": "/subscriptions/guid/resourceGroups/myResourceGroup",
"name": "myResourceGroup",
"provisioningState": "Succeeded",
"location": "westeurope",
"properties": {
    "provisioningState": "Succeeded"
},
"resources": [
    {
    "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP",
    "name": "myPublicIP",
    "type": "publicIPAddresses",
    "location": "westeurope",
    "tags": null
    },
    {
    "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet",
    "name": "myVnet",
    "type": "virtualNetworks",
    "location": "westeurope",
    "tags": null
    },
    {
    "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
    "name": "mystorageaccount",
    "type": "storageAccounts",
    "location": "westeurope",
    "tags": null
    }
],
"permissions": [
    {
    "actions": [
        "*"
    ],
    "notActions": []
    }
]
}
```

Вы можете изучить дополнительные сведения о ресурсе, в том числе полное доменное имя (FQDN) поддомена, используя полную команду `azure network public-ip show`. Ресурс общедоступного IP-адреса выделен логически, но при этом конкретный адрес еще не назначен. Для получения IP-адреса вам потребуется балансировщик нагрузки, но он еще не создан.

```azurecli
azure network public-ip show myResourceGroup myPublicIP --json | jq '.'
```

Выходные данные:

```json
{
"tags": {},
"publicIpAllocationMethod": "Dynamic",
"dnsSettings": {
    "domainNameLabel": "mypublicdns",
    "fqdn": "mypublicdns.westeurope.cloudapp.azure.com"
},
"idleTimeoutInMinutes": 4,
"provisioningState": "Succeeded",
"etag": "W/\"c63154b3-1130-49b9-a887-877d74d5ebc5\"",
"id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP",
"name": "myPublicIP",
"location": "westeurope"
}
```

## <a name="create-a-load-balancer-and-ip-pools"></a>Создание балансировщика нагрузки и пулов IP-адресов
Создание балансировщика нагрузки дает возможность распределять трафик между несколькими виртуальными машинами. Это также обеспечивает избыточность для приложения, так как работает несколько виртуальных машин, которые отвечают на запросы пользователей в случае проведения обслуживания или высокой нагрузки. В следующем примере создается балансировщик нагрузки с именем `myLoadBalancer`.

```azurecli
azure network lb create --resource-group myResourceGroup --location westeurope \
  --name myLoadBalancer
```

Выходные данные:

```azurecli
info:    Executing command network lb create
+ Looking up the load balancer "myLoadBalancer"
+ Creating load balancer "myLoadBalancer"
data:    Id                              : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer
data:    Name                            : myLoadBalancer
data:    Type                            : Microsoft.Network/loadBalancers
data:    Location                        : westeurope
data:    Provisioning state              : Succeeded
info:    network lb create command OK
```

Наш балансировщик нагрузки выглядит пустым, поэтому давайте создадим несколько пулов IP-адресов. Необходимо создать два пула IP-адресов для балансировщика нагрузки — один интерфейсный, а второй внутренний. Интерфейсный пул IP-адресов является публично видимым. Это также расположение, которому присваивается общедоступный IP-адрес, созданный ранее. Затем мы используем внутренний пул как расположение для подключения наших виртуальных машин. Таким образом трафик сможет проходить через балансировщик нагрузки к виртуальным машинам.

Сначала создадим интерфейсный пул IP-адресов. В следующем примере создается интерфейсный пул с именем `myFrontEndPool`.

```azurecli
azure network lb frontend-ip create --resource-group myResourceGroup \
  --lb-name myLoadBalancer --public-ip-name myPublicIP \
  --name myFrontEndPool
```

Выходные данные:

```azurecli
info:    Executing command network lb frontend-ip create
+ Looking up the load balancer "myLoadBalancer"
+ Looking up the public ip "myPublicIP"
+ Updating load balancer "myLoadBalancer"
data:    Name                            : myFrontEndPool
data:    Provisioning state              : Succeeded
data:    Private IP allocation method    : Dynamic
data:    Public IP address id            : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP
info:    network lb mySubnet-ip create command OK
```

Обратите внимание на то, как мы использовали параметр `--public-ip-name` для передачи созданного ранее `myPublicIP`. Присвоение общедоступного IP-адреса балансировщику нагрузки позволяет получить доступ к нашим виртуальным машинам через Интернет.

Теперь создадим второй пул IP-адресов, на этот раз для внутреннего трафика. В следующем примере создается внутренний пул с именем `myBackEndPool`.

```azurecli
azure network lb address-pool create --resource-group myResourceGroup \
  --lb-name myLoadBalancer --name myBackEndPool
```

Выходные данные:

```azurecli
info:    Executing command network lb address-pool create
+ Looking up the load balancer "myLoadBalancer"
+ Updating load balancer "myLoadBalancer"
data:    Name                            : myBackEndPool
data:    Provisioning state              : Succeeded
info:    network lb address-pool create command OK
```

Можно узнать, как выглядит балансировщик нагрузки, введя команду `azure network lb show`, и проверить выходные данные JSON.

```azurecli
azure network lb show myResourceGroup myLoadBalancer --json | jq '.'
```

Выходные данные:

```json
{
  "etag": "W/\"29c38649-77d6-43ff-ab8f-977536b0047c\"",
  "provisioningState": "Succeeded",
  "resourceGuid": "f1446acb-09ba-44d9-b8b6-849d9983dc09",
  "outboundNatRules": [],
  "inboundNatPools": [],
  "inboundNatRules": [],
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer",
  "name": "myLoadBalancer",
  "type": "Microsoft.Network/loadBalancers",
  "location": "westeurope",
  "mySubnetIPConfigurations": [
    {
      "etag": "W/\"29c38649-77d6-43ff-ab8f-977536b0047c\"",
      "name": "myFrontEndPool",
      "provisioningState": "Succeeded",
      "publicIPAddress": {
        "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP"
      },
      "privateIPAllocationMethod": "Dynamic",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/mySubnetIPConfigurations/myFrontEndPool"
    }
  ],
  "backendAddressPools": [
    {
      "etag": "W/\"29c38649-77d6-43ff-ab8f-977536b0047c\"",
      "name": "myBackEndPool",
      "provisioningState": "Succeeded",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool"
    }
  ],
  "loadBalancingRules": [],
  "probes": []
}
```

## <a name="create-load-balancer-nat-rules"></a>Создание правил NAT балансировщика нагрузки
Чтобы трафик проходил через наш балансировщик нагрузки, необходимо создать правила NAT, которые задают действия для входящего или исходящего трафика. Можно указать используемый протокол, а затем сопоставить внешние порты с внутренними, если это необходимо. Давайте создадим в нашей среде несколько правил, которые пропускают трафик SSH через балансировщик нагрузки к виртуальным машинам. Мы настраиваем перенаправление TCP-портов 4222 и 4223 на TCP-порт 22 на наших виртуальных машинах (которые мы создадим позже). В следующем примере создается правило с именем `myLoadBalancerRuleSSH1`. Это правило сопоставляет TCP-порт 4222 с портом 22.

```azurecli
azure network lb inbound-nat-rule create --resource-group myResourceGroup \
  --lb-name myLoadBalancer --name myLoadBalancerRuleSSH1 \
  --protocol tcp --frontend-port 4222 --backend-port 22
```

Выходные данные:

```azurecli
info:    Executing command network lb inbound-nat-rule create
+ Looking up the load balancer "myLoadBalancer"
warn:    Using default enable floating ip: false
warn:    Using default idle timeout: 4
warn:    Using default mySubnet IP configuration "myFrontEndPool"
+ Updating load balancer "myLoadBalancer"
data:    Name                            : myLoadBalancerRuleSSH1
data:    Provisioning state              : Succeeded
data:    Protocol                        : Tcp
data:    mySubnet port                   : 4222
data:    Backend port                    : 22
data:    Enable floating IP              : false
data:    Idle timeout in minutes         : 4
data:    mySubnet IP configuration id    : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/mySubnetIPConfigurations/myFrontEndPool
info:    network lb inbound-nat-rule create command OK
```

Повторите эту процедуру для второго правила NAT для трафика SSH. В следующем примере создается правило с именем `myLoadBalancerRuleSSH2`. Это правило сопоставляет TCP-порт 4223 с портом 22.

```azurecli
azure network lb inbound-nat-rule create --resource-group myResourceGroup \
  --lb-name myLoadBalancer --name myLoadBalancerRuleSSH2 --protocol tcp \
  --frontend-port 4223 --backend-port 22
```

Дальше создадим правило NAT для TCP-порта 80 для веб-трафика, которое привязывает правило к нашим пулам IP-адресов. Привязав правило к пулу IP-адресов, а не к виртуальным машинам по отдельности, мы сможем добавлять виртуальные машины в пул IP-адресов и удалять их из него. Балансировщик нагрузки будет автоматически регулировать поток трафика. В следующем примере создается правило с именем `myLoadBalancerRuleWeb`. Это правило сопоставляет TCP-порт 80 с портом 80.

```azurecli
azure network lb rule create --resource-group myResourceGroup \
  --lb-name myLoadBalancer --name myLoadBalancerRuleWeb --protocol tcp \
  --frontend-port 80 --backend-port 80 --frontend-ip-name myFrontEndPool \
  --backend-address-pool-name myBackEndPool
```

Выходные данные:

```azurecli
info:    Executing command network lb rule create
+ Looking up the load balancer "myLoadBalancer"
warn:    Using default idle timeout: 4
warn:    Using default enable floating ip: false
warn:    Using default load distribution: Default
+ Updating load balancer "myLoadBalancer"
data:    Name                            : myLoadBalancerRuleWeb
data:    Provisioning state              : Succeeded
data:    Protocol                        : Tcp
data:    mySubnet port                   : 80
data:    Backend port                    : 80
data:    Enable floating IP              : false
data:    Load distribution               : Default
data:    Idle timeout in minutes         : 4
data:    mySubnet IP configuration id    : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/mySubnetIPConfigurations/myFrontEndPool
data:    Backend address pool id         : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool
info:    network lb rule create command OK
```

## <a name="create-a-load-balancer-health-probe"></a>Создание пробы работоспособности балансировщика нагрузки
Проба работоспособности периодически проверяет, соответствующим ли образом работают и отвечают на запросы виртуальные машины, которые расположены за балансировщиком нагрузки. Если это не так, они выводятся из эксплуатации, чтобы пользователи не могли их использовать. Можно определить пользовательские проверки для пробы работоспособности, указав интервалы и значения времени ожидания. Дополнительные сведения о пробах работоспособности см. в статье [Проверки балансировщика нагрузки](../../load-balancer/load-balancer-custom-probe-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). В следующем примере создается TCP-проба работоспособности с именем `myHealthProbe`.

```azurecli
azure network lb probe create --resource-group myResourceGroup \
  --lb-name myLoadBalancer --name myHealthProbe --protocol "tcp" \
  --interval 15 --count 4
```

Выходные данные:

```azurecli
info:    Executing command network lb probe create
warn:    Using default probe port: 80
+ Looking up the load balancer "myLoadBalancer"
+ Updating load balancer "myLoadBalancer"
data:    Name                            : myHealthProbe
data:    Provisioning state              : Succeeded
data:    Protocol                        : Tcp
data:    Port                            : 80
data:    Interval in seconds             : 15
data:    Number of probes                : 4
info:    network lb probe create command OK
```

Здесь мы указали интервал в 15 секунд для проверок работоспособности. Допускается пропуск до 4 проб (одна минута), прежде чем балансировщик нагрузки начнет считать, что узел перестал функционировать.

## <a name="verify-the-load-balancer"></a>Проверка балансировщика нагрузки
Настройка балансировщика нагрузки завершена. Вот какие действия были выполнены.

1. Вы создали балансировщик нагрузки.
2. Затем вы создали интерфейсный пул IP-адресов и назначили ему общедоступный IP-адрес.
3. Потом вы создали внутренний пул IP-адресов, к которому могут подключаться виртуальные машины.
4. Вы создали правила NAT, которые разрешают управлять виртуальными машинами через SSH-подключение, а также правило, открывающее TCP-порт 80 для нашего веб-приложения.
5. Вы добавили пробу работоспособности для периодической проверки виртуальных машин. Эта проба работоспособности гарантирует, что пользователи не попытаются обратиться к виртуальной машине, которая больше не работает или не обрабатывает содержимое.

Давайте посмотрим, как теперь выглядит балансировщик нагрузки.

```azurecli
azure network lb show --resource-group myResourceGroup \
  --name myLoadBalancer --json | jq '.'
```

Выходные данные:

```json
{
  "etag": "W/\"62a7c8e7-859c-48d3-8e76-5e078c5e4a02\"",
  "provisioningState": "Succeeded",
  "resourceGuid": "f1446acb-09ba-44d9-b8b6-849d9983dc09",
  "outboundNatRules": [],
  "inboundNatPools": [],
  "inboundNatRules": [
    {
      "etag": "W/\"62a7c8e7-859c-48d3-8e76-5e078c5e4a02\"",
      "name": "myLoadBalancerRuleSSH1",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH1",
      "mySubnetIPConfiguration": {
        "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/mySubnetIPConfigurations/myFrontEndPool"
      },
      "protocol": "Tcp",
      "mySubnetPort": 4222,
      "backendPort": 22,
      "idleTimeoutInMinutes": 4,
      "enableFloatingIP": false,
      "provisioningState": "Succeeded"
    },
    {
      "etag": "W/\"62a7c8e7-859c-48d3-8e76-5e078c5e4a02\"",
      "name": "myLoadBalancerRuleSSH2",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH2",
      "mySubnetIPConfiguration": {
        "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/mySubnetIPConfigurations/myFrontEndPool"
      },
      "protocol": "Tcp",
      "mySubnetPort": 4223,
      "backendPort": 22,
      "idleTimeoutInMinutes": 4,
      "enableFloatingIP": false,
      "provisioningState": "Succeeded"
    }
  ],
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer",
  "name": "myLoadBalancer",
  "type": "Microsoft.Network/loadBalancers",
  "location": "westeurope",
  "mySubnetIPConfigurations": [
    {
      "etag": "W/\"62a7c8e7-859c-48d3-8e76-5e078c5e4a02\"",
      "name": "myFrontEndPool",
      "provisioningState": "Succeeded",
      "publicIPAddress": {
        "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP"
      },
      "privateIPAllocationMethod": "Dynamic",
      "loadBalancingRules": [
        {
          "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/loadBalancingRules/myLoadBalancerRuleWeb"
        }
      ],
      "inboundNatRules": [
        {
          "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH1"
        },
        {
          "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH2"
        }
      ],
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/mySubnetIPConfigurations/myFrontEndPool"
    }
  ],
  "backendAddressPools": [
    {
      "etag": "W/\"62a7c8e7-859c-48d3-8e76-5e078c5e4a02\"",
      "name": "myBackEndPool",
      "provisioningState": "Succeeded",
      "loadBalancingRules": [
        {
          "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/loadBalancingRules/myLoadBalancerRuleWeb"
        }
      ],
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool"
    }
  ],
  "loadBalancingRules": [
    {
      "etag": "W/\"62a7c8e7-859c-48d3-8e76-5e078c5e4a02\"",
      "name": "myLoadBalancerRuleWeb",
      "provisioningState": "Succeeded",
      "enableFloatingIP": false,
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/loadBalancingRules/myLoadBalancerRuleWeb",
      "mySubnetIPConfiguration": {
        "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/mySubnetIPConfigurations/myFrontEndPool"
      },
      "backendAddressPool": {
        "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool"
      },
      "protocol": "Tcp",
      "loadDistribution": "Default",
      "mySubnetPort": 80,
      "backendPort": 80,
      "idleTimeoutInMinutes": 4
    }
  ],
  "probes": [
    {
      "etag": "W/\"62a7c8e7-859c-48d3-8e76-5e078c5e4a02\"",
      "name": "myHealthProbe",
      "provisioningState": "Succeeded",
      "numberOfProbes": 4,
      "intervalInSeconds": 15,
      "port": 80,
      "protocol": "Tcp",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/probes/myHealthProbe"
    }
  ]
}
```

## <a name="create-an-nic-to-use-with-the-linux-vm"></a>Создание сетевой карты для виртуальной машины Linux
Работой сетевых карт можно управлять программно, так как к ним можно применить правила. Кроме того, можно использовать несколько сетевых карт. В приведенной ниже команде `azure network nic create` вы привязываете сетевую карту к внутреннему пулу IP-адресов загрузки и связанному правилу NAT, которое разрешает трафик SSH.

Замените `#####-###-###` собственным идентификатором подписки Azure. Идентификатор подписки указан в выходных данных `jq`, которые можно просмотреть при проверке создаваемых ресурсов. Кроме того, его можно просмотреть, выполнив команду `azure account list`.

В следующем примере создается сетевой адаптер с именем `myNic1`.

```azurecli
azure network nic create --resource-group myResourceGroup --location westeurope \
  --subnet-vnet-name myVnet --subnet-name mySubnet --name myNic1 \
  --lb-address-pool-ids /subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool \
  --lb-inbound-nat-rule-ids /subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH1
```

Выходные данные:

```azurecli
info:    Executing command network nic create
+ Looking up the subnet "mySubnet"
+ Looking up the network interface "myNic1"
+ Creating network interface "myNic1"
data:    Id                              : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic1
data:    Name                            : myNic1
data:    Type                            : Microsoft.Network/networkInterfaces
data:    Location                        : westeurope
data:    Provisioning state              : Succeeded
data:    Enable IP forwarding            : false
data:    IP configurations:
data:      Name                          : Nic-IP-config
data:      Provisioning state            : Succeeded
data:      Private IP address            : 192.168.1.4
data:      Private IP allocation method  : Dynamic
data:      Subnet                        : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet
data:      Load balancer backend address pools:
data:        Id                          : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool
data:      Load balancer inbound NAT rules:
data:        Id                          : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH1
data:
info:    network nic create command OK
```

Чтобы отобразить сведения, необходимо просмотреть ресурс напрямую. Проверить ресурс можно с помощью команды `azure network nic show`:

```azurecli
azure network nic show myResourceGroup myNic1 --json | jq '.'
```

Выходные данные:

```json
{
  "etag": "W/\"fc1eaaa1-ee55-45bd-b847-5a08c7f4264a\"",
  "provisioningState": "Succeeded",
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic1",
  "name": "myNic1",
  "type": "Microsoft.Network/networkInterfaces",
  "location": "westeurope",
  "ipConfigurations": [
    {
      "etag": "W/\"fc1eaaa1-ee55-45bd-b847-5a08c7f4264a\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic1/ipConfigurations/Nic-IP-config",
      "loadBalancerBackendAddressPools": [
        {
          "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool"
        }
      ],
      "loadBalancerInboundNatRules": [
        {
          "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH1"
        }
      ],
      "privateIPAddress": "192.168.1.4",
      "privateIPAllocationMethod": "Dynamic",
      "subnet": {
        "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet"
      },
      "provisioningState": "Succeeded",
      "name": "Nic-IP-config"
    }
  ],
  "dnsSettings": {
    "appliedDnsServers": [],
    "dnsServers": []
  },
  "enableIPForwarding": false,
  "resourceGuid": "a20258b8-6361-45f6-b1b4-27ffed28798c"
}
```

Теперь мы создаем вторую сетевую карту и также привязываем ее к внутреннему пулу IP-адресов. В этот раз второе правило NAT разрешает трафик SSH. В следующем примере создается сетевой адаптер с именем `myNic2`.

```azurecli
azure network nic create --resource-group myResourceGroup --location westeurope \
  --subnet-vnet-name myVnet --subnet-name mySubnet --name myNic2 \
  --lb-address-pool-ids  /subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool \
  --lb-inbound-nat-rule-ids /subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH2
```

## <a name="create-a-network-security-group-and-rules"></a>Создание группы безопасности сети и правил
Теперь мы создадим группу безопасности сети и правила для входящего трафика, управляющие доступом к сетевому адаптеру. Группу безопасности сети можно применить к сетевому адаптеру или подсети. Правила, которые вы определяете, позволяют управлять исходящим и входящим потоком трафика виртуальной машины. В следующем примере создается группа безопасности сети с именем `myNetworkSecurityGroup`.

```azurecli
azure network nsg create --resource-group myResourceGroup --location westeurope \
  --name myNetworkSecurityGroup
```

Теперь добавим для этой группы безопасности сети правило для входящего трафика, чтобы разрешить входящие подключения через порт 22 (для поддержки SSH). В следующем примере создается правило с именем `myNetworkSecurityGroupRuleSSH`. Это правило разрешает TCP-трафик через порт 22.

```azurecli
azure network nsg rule create --resource-group myResourceGroup \
  --nsg-name myNetworkSecurityGroup --protocol tcp --direction inbound \
  --priority 1000 --destination-port-range 22 --access allow \
  --name myNetworkSecurityGroupRuleSSH
```

Теперь добавим для этой группы безопасности сети правило для входящего трафика, чтобы разрешить входящие подключения через порт 80 (для поддержки веб-трафика). В следующем примере создается правило с именем `myNetworkSecurityGroupRuleHTTP`. Это правило разрешает TCP-трафик через порт 80.

```azurecli
azure network nsg rule create --resource-group myResourceGroup \
  --nsg-name myNetworkSecurityGroup --protocol tcp --direction inbound \
  --priority 1001 --destination-port-range 80 --access allow \
  --name myNetworkSecurityGroupRuleHTTP
```

> [!NOTE]
> Правило для входящего трафика является фильтром входящих сетевых подключений. В этом примере мы привяжем группу безопасности сети к виртуальной сетевой карте виртуальной машины. Это значит, что любой запрос к порту 22 передается в сетевую карту виртуальной машины. Это правило для входящего сетевого подключения, а не конечной точки, как это было бы в классическом развертывании. Для открытия порта необходимо оставить для `--source-port-range` значение "\*" (значение по умолчанию), чтобы разрешить прием входящих запросов от **любого** запрашивающего порта. Обычно порты являются динамическими.
>
>

## <a name="bind-to-the-nic"></a>Привязка к сетевой карте
Привяжите группу безопасности сети к сетевому адаптеру. Сетевые адаптеры необходимо подключить к группе безопасности сети. Чтобы привязать оба сетевых адаптера, выполните следующие команды:

```azurecli
azure network nic set --resource-group myResourceGroup --name myNic1 \
  --network-security-group-name myNetworkSecurityGroup
```

```azurecli
azure network nic set --resource-group myResourceGroup --name myNic2 \
  --network-security-group-name myNetworkSecurityGroup
```

## <a name="create-an-availability-set"></a>"Создать группу доступности"
Группы доступности помогают распределить виртуальные машины между доменами сбоя и доменами обновления. Создадим группу доступности для виртуальных машин. В следующем примере создается группа доступности с именем `myAvailabilitySet`.

```azurecli
azure availset create --resource-group myResourceGroup --location westeurope
  --name myAvailabilitySet
```

Домены сбоя определяют группу виртуальных машин, совместно использующих общий источник питания и сетевой коммутатор. По умолчанию виртуальные машины, настроенные в группе доступности, разделяются между несколькими доменами сбоя, которых может быть не больше трех. Суть в том, что сбой оборудования в одном из этих доменов сбоя не влияет на все виртуальные машины, на которых выполняется ваше приложение. Azure автоматически распределяет виртуальные машины между доменами сбоя при помещении их в группу доступности.

Домены обновления — это группы виртуальных машин и базовое физическое оборудование, которое может быть перезагружено одновременно. Перезагрузка доменов обновления при запланированном обслуживании может выполняться не по порядку, но одновременно перезагружается только один домен обновления. Опять же, Azure автоматически распределяет виртуальные машины между доменами обновления при помещении их в группу доступности.

Узнайте больше об [управлении доступностью виртуальных машин](manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="create-the-linux-vms"></a>Создание виртуальной машины Linux
Вы создали ресурсы сети и хранилища для доступных через Интернет виртуальных машин. Теперь давайте создадим эти виртуальные машины и защитим их ключом SSH без пароля. В этом случае мы создадим виртуальную машину Ubuntu на базе последней версии LTS. Информацию об этом образе мы находим с помощью команды `azure vm image list`, как описано в статье о [поиске образов виртуальных машин Azure](../windows/cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Мы выбрали образ с помощью команды `azure vm image list westeurope canonical | grep LTS`. В этом случае мы используем `canonical:UbuntuServer:16.04.0-LTS:16.04.201608150`. Для последнего поля мы передаем `latest` , чтобы в будущем всегда получать последнюю сборку. (Используемая строка: `canonical:UbuntuServer:16.04.0-LTS:16.04.201608150`).

Следующий этап знаком каждому, кто уже хоть раз создал пару ключей SSH RSA (открытый и закрытый) в Linux или Mac с помощью команды **ssh-keygen -t rsa -b 2048**. Если в вашем каталоге `~/.ssh` нет пар ключей сертификата, то их можно создать:

* автоматически, с помощью параметра `azure vm create --generate-ssh-keys` ;
* вручную, следуя [инструкциям по самостоятельному созданию](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Кроме того, можно использовать метод `--admin-password` для проверки подлинности подключений по протоколу SSH после создания виртуальной машины. Обычно этот метод менее безопасен.

Мы создаем виртуальную машину, собирая вместе все наши ресурсы и информацию командой `azure vm create`.

```azurecli
azure vm create \
  --resource-group myResourceGroup \
  --name myVM1 \
  --location westeurope \
  --os-type linux \
  --availset-name myAvailabilitySet \
  --nic-name myNic1 \
  --vnet-name myVnet \
  --vnet-subnet-name mySubnet \
  --storage-account-name mystorageaccount \
  --image-urn canonical:UbuntuServer:16.04.0-LTS:latest \
  --ssh-publickey-file ~/.ssh/id_rsa.pub \
  --admin-username azureuser
```

Выходные данные:

```azurecli
info:    Executing command vm create
+ Looking up the VM "myVM1"
info:    Verifying the public key SSH file: /home/ahmet/.ssh/id_rsa.pub
info:    Using the VM Size "Standard_DS1"
info:    The [OS, Data] Disk or image configuration requires storage account
+ Looking up the storage account mystorageaccount
+ Looking up the availability set "myAvailabilitySet"
info:    Found an Availability set "myAvailabilitySet"
+ Looking up the NIC "myNic1"
info:    Found an existing NIC "myNic1"
info:    Found an IP configuration with virtual network subnet id "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet" in the NIC "myNic1"
info:    This is an NIC without publicIP configured
info:    The storage URI 'https://mystorageaccount.blob.core.windows.net/' will be used for boot diagnostics settings, and it can be overwritten by the parameter input of '--boot-diagnostics-storage-uri'.
info:    vm create command OK
```

К созданной виртуальной машине можно сразу же подключиться с помощью используемых по умолчанию ключей SSH. Обязательно укажите соответствующий порт, так как подключение проходит через балансировщик нагрузки. Для первой виртуальной машины мы устанавливаем правило NAT, которое перенаправляет на нее трафик с порта 4222.

```bash
ssh ops@mypublicdns.westeurope.cloudapp.azure.com -p 4222
```

Выходные данные:

```bash
The authenticity of host '[mypublicdns.westeurope.cloudapp.azure.com]:4222 ([xx.xx.xx.xx]:4222)' can't be established.
ECDSA key fingerprint is 94:2d:d0:ce:6b:fb:7f:ad:5b:3c:78:93:75:82:12:f9.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added '[mypublicdns.westeurope.cloudapp.azure.com]:4222,[xx.xx.xx.xx]:4222' (ECDSA) to the list of known hosts.
Welcome to Ubuntu 16.04.1 LTS (GNU/Linux 4.4.0-34-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  Get cloud support with Ubuntu Advantage Cloud Guest:
    http://www.ubuntu.com/business/services/cloud

0 packages can be updated.
0 updates are security updates.

ops@myVM1:~$
```

Точно так же создадим вторую виртуальную машину.

```azurecli
azure vm create \
  --resource-group myResourceGroup \
  --name myVM2 \
  --location westeurope \
  --os-type linux \
  --availset-name myAvailabilitySet \
  --nic-name myNic2 \
  --vnet-name myVnet \
  --vnet-subnet-name mySubnet \
  --storage-account-name mystorageaccount \
  --image-urn canonical:UbuntuServer:16.04.0-LTS:latest \
  --ssh-publickey-file ~/.ssh/id_rsa.pub \
  --admin-username azureuser
```

Теперь, чтобы проверить то, что вы создали, можно использовать команду `azure vm show myResourceGroup myVM1`. На данном этапе имеются работающие виртуальные машины Ubuntu, расположенные за балансировщиком нагрузки в Azure, входить на которые можно только с помощью пары ключей SSH (так как пароли отключены). Можно установить nginx или httpd, развернуть веб-приложение и посмотреть, как трафик проходит через балансировщик нагрузки на обе виртуальные машины.

```azurecli
azure vm show --resource-group myResourceGroup --name myVM1
```

Выходные данные:

```azurecli
info:    Executing command vm show
+ Looking up the VM "TestVM1"
+ Looking up the NIC "myNic1"
data:    Id                              :/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM1
data:    ProvisioningState               :Succeeded
data:    Name                            :myVM1
data:    Location                        :westeurope
data:    Type                            :Microsoft.Compute/virtualMachines
data:
data:    Hardware Profile:
data:      Size                          :Standard_DS1
data:
data:    Storage Profile:
data:      Image reference:
data:        Publisher                   :canonical
data:        Offer                       :UbuntuServer
data:        Sku                         :16.04.0-LTS
data:        Version                     :latest
data:
data:      OS Disk:
data:        OSType                      :Linux
data:        Name                        :clib45a8b650f4428a1-os-1471973896525
data:        Caching                     :ReadWrite
data:        CreateOption                :FromImage
data:        Vhd:
data:          Uri                       :https://mystorageaccount.blob.core.windows.net/vhds/clib45a8b650f4428a1-os-1471973896525.vhd
data:
data:    OS Profile:
data:      Computer Name                 :myVM1
data:      User Name                     :ops
data:      Linux Configuration:
data:        Disable Password Auth       :true
data:
data:    Network Profile:
data:      Network Interfaces:
data:        Network Interface #1:
data:          Primary                   :true
data:          MAC Address               :00-0D-3A-24-D4-AA
data:          Provisioning State        :Succeeded
data:          Name                      :LmyNic1
data:          Location                  :westeurope
data:
data:    AvailabilitySet:
data:      Id                            :/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Compute/availabilitySets/myAvailabilitySet
data:
data:    Diagnostics Profile:
data:      BootDiagnostics Enabled       :true
data:      BootDiagnostics StorageUri    :https://mystorageaccount.blob.core.windows.net/
data:
data:      Diagnostics Instance View:
info:    vm show command OK

```


## <a name="export-the-environment-as-a-template"></a>Экспорт среды как шаблона
Теперь, когда вы создали эту среду, предположим, что вам требуется создать дополнительную среду разработки с теми же параметрами или соответствующую рабочую среду. Resource Manager использует шаблоны JSON, которые определяют все параметры среды. Можно полностью создавать среды, ссылаясь на этот шаблон JSON. Вы можете [создавать шаблоны JSON вручную](../../resource-group-authoring-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) или экспортировать существующую среду для создания шаблона JSON.

```azurecli
azure group export --name myResourceGroup
```

С помощью этой команды в текущем рабочем каталоге создается файл `myResourceGroup.json`. При создании среды на основе этого шаблона запрашиваются все имена ресурсов, включая балансировщик нагрузки, сетевые интерфейсы или виртуальные машины. Эти имена можно указать в файле шаблона, добавив параметр `-p` или `--includeParameterDefaultValue` в команду `azure group export`, которая была показана ранее. Измените шаблон JSON, чтобы указать имена ресурсов, или [создайте файл parameters.json](../../resource-group-authoring-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) , задающий их.

Чтобы создать среду из шаблона, выполните следующие действия.

```azurecli
azure group deployment create --resource-group myNewResourceGroup \
  --template-file myResourceGroup.json
```

Вы можете прочитать [дополнительные сведения о развертывании из шаблонов](../../resource-group-template-deploy-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Узнайте, как поэтапно обновлять среды, использовать файл параметров и обращаться к шаблонам из единого места хранения.

## <a name="next-steps"></a>Дополнительная информация
Теперь вы готовы приступить к работе с несколькими сетевыми компонентами и виртуальными машинами. Используя этот пример среды, можно создать приложение на основе представленных здесь основных компонентов.

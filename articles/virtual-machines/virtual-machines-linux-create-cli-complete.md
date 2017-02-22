---
title: "Создание среды Linux с помощью Azure CLI 2.0 | Документация Майкрософт"
description: "Узнайте, как с помощью с помощью предварительной версии Azure CLI 2.0 создать &quot;с нуля&quot; хранилище, виртуальную машину Linux, виртуальную сеть и подсеть, балансировщик нагрузки, сетевую карту, общедоступный IP-адрес и группу безопасности сети."
services: virtual-machines-linux
documentationcenter: virtual-machines
author: iainfoulds
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 4ba4060b-ce95-4747-a735-1d7c68597a1a
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/8/2016
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: 39ce158ae52b978b74161cdadb4b886a7ddbf87a
ms.openlocfilehash: a00936df023ddbb13f5765f2e78900a68cccdb88


---
# <a name="create-a-complete-linux-environment-by-using-the-azure-cli-20-preview"></a>Создание полной среды Linux с помощью Azure CLI 2.0 (предварительная версия)
В этой статье мы создаем простую сеть с балансировщиком нагрузки и парой виртуальных машин, подходящих для разработки и простых вычислений. Мы поэтапно выполняем полное развертывание, от первой до последней команды, в результате чего создаем две защищенные рабочие виртуальные машины Linux, к которым можно подключиться откуда угодно через Интернет. Затем вы сможете работать с более сложными сетями и средами.

Попутно вы ознакомитесь с иерархией зависимостей модели развертывания с помощью Resource Manager и узнаете, какие возможности она дает. Узнав, как устроена система, вы сможете воссоздать ее гораздо быстрее с помощью [шаблонов Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Кроме того, когда вы узнаете, как взаимодействуют части среды, создавать шаблоны для их автоматизации станет проще.

Наша среда содержит:

* две виртуальные машины в группе доступности;
* балансировщик нагрузки с правилом балансировки нагрузки для порта 80;
* правила группы безопасности сети для защиты виртуальных машин от нежелательного трафика.

![Обзор базовой среды](./media/virtual-machines-linux-create-cli-complete/environment_overview.png)

## <a name="cli-versions-to-complete-the-task"></a>Версии интерфейса командной строки для выполнения задачи
Вы можете выполнить задачу, используя одну из следующих версий интерфейса командной строки.

- [Azure CLI 1.0](virtual-machines-linux-create-cli-complete-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) — интерфейс командной строки для классической модели развертывания и модели развертывания Resource Manager.
- [Azure CLI 2.0 (предварительная версия)](#quick-commands) — интерфейс командной строки нового поколения для модели развертывания Resource Manager (описывается в этой статье).

## <a name="quick-commands"></a>Быстрые команды
Если вам необходимо быстро выполнить задачу, в следующем разделе описаны основные команды для отправки виртуальной машины в Azure. Дополнительные сведения и контекст для каждого этапа можно найти в остальной части документа, [начиная отсюда](#detailed-walkthrough).

В следующих примерах замените имена параметров собственными значениями. Используемые имена параметров: `myResourceGroup`, `mystorageaccount` и `myVM`.

Для создания этой настраиваемой среды необходимо установить последнюю версию [Azure CLI 2.0 (предварительная версия)](/cli/azure/install-az-cli2) и войти в учетную запись Azure с помощью команды [az login](/cli/azure/#login).

Сначала создайте группу ресурсов с помощью команды [az group create](/cli/azure/group#create). В следующем примере создается группа ресурсов с именем `myResourceGroup` в расположении `westeurope`:

```azurecli
az group create --name myResourceGroup --location westeurope
```

Следующий шаг не является обязательным. По умолчанию виртуальная машина, созданная с помощью Azure CLI 2.0 (предварительная версия), использует Управляемые диски Azure. Дополнительные сведения об Управляемых дисках Azure см. в [обзоре Управляемых дисков Azure](../storage/storage-managed-disks-overview.md). Если вместо этого вы хотите использовать неуправляемые диски, необходимо создать учетную запись хранения, выполнив команду [az storage account create](/cli/azure/storage/account#create). В следующем примере создается учетная запись хранения с именем `mystorageaccount`. Имя учетной записи хранения должно быть уникальным, поэтому введите собственное уникальное имя.

```azurecli
az storage account create --resource-group myResourceGroup --location westeurope \
  --name mystorageaccount --kind Storage --sku Standard_LRS
```

Создайте виртуальную сеть с помощью команды [az network vnet create](/cli/azure/network/vnet#create). В следующем примере создается виртуальная сеть `myVnet` и подсеть `mySubnet`.

```azurecli
az network vnet create --resource-group myResourceGroup --location westeurope --name myVnet \
  --address-prefix 192.168.0.0/16 --subnet-name mySubnet --subnet-prefix 192.168.1.0/24
```

Создайте общедоступный IP-адрес с помощью команды [az network public-ip create](/cli/azure/network/public-ip#create). В следующем примере создается общедоступный IP-адрес `myPublicIP` с DNS-именем `mypublicdns`. DNS-имя должно быть уникальным, поэтому укажите собственное уникальное имя.

```azurecli
az network public-ip create --resource-group myResourceGroup --location westeurope \
  --name myPublicIP --dns-name mypublicdns --allocation-method static --idle-timeout 4
```

Создайте балансировщик нагрузки с помощью команды [az network lb create](/cli/azure/network/lb#create). В следующем примере выполняются следующие действия:

- создается балансировщик нагрузки с именем `myLoadBalancer`;
- связывается общедоступный IP-адрес `myPublicIP`;
- создается интерфейсный пул IP-адресов с именем `mySubnetPool`;
- создается внутренний пул IP-адресов с именем `myBackEndPool`.

```azurecli
az network lb create --resource-group myResourceGroup --location westeurope \
  --name myLoadBalancer --public-ip-address myPublicIP \
  --frontend-ip-name myFrontEndPool --backend-pool-name myBackEndPool
```

Создайте правила преобразования сетевых адресов (NAT) для входящего SSH-трафика для балансировщика нагрузки с помощью команды [az network lb inbound-nat-rule create](/cli/azure/network/lb/inbound-nat-rule#create). В следующем примере создается два правила балансировщика нагрузки (`myLoadBalancerRuleSSH1` и `myLoadBalancerRuleSSH2`).

```azurecli
az network lb inbound-nat-rule create --resource-group myResourceGroup \
  --lb-name myLoadBalancer --name myLoadBalancerRuleSSH1 --protocol tcp \
  --frontend-port 4222 --backend-port 22 --frontend-ip-name myFrontEndPool
az network lb inbound-nat-rule create --resource-group myResourceGroup \
  --lb-name myLoadBalancer --name myLoadBalancerRuleSSH2 --protocol tcp \
  --frontend-port 4223 --backend-port 22 --frontend-ip-name myFrontEndPool
```

Создайте проверку работоспособности балансировщика нагрузки с помощью команды [az network lb probe create](/cli/azure/network/lb/probe#create). В следующем примере создается TCP-проба с именем `myHealthProbe`.

```azurecli
az network lb probe create --resource-group myResourceGroup --lb-name myLoadBalancer \
  --name myHealthProbe --protocol tcp --port 80 --interval 15 --threshold 4
```

Создайте правила NAT для входящего веб-трафика для балансировщика нагрузки с помощью команды [az network lb rule create](/cli/azure/network/lb/rule#create). В следующем примере создается правило балансировщика нагрузки с именем `myLoadBalancerRuleWeb`. Это правило связывается с проверкой `myHealthProbe`.

```azurecli
az network lb rule create --resource-group myResourceGroup --lb-name myLoadBalancer \
  --name myLoadBalancerRuleWeb --protocol tcp --frontend-port 80 --backend-port 80 \
  --frontend-ip-name myFrontEndPool --backend-pool-name myBackEndPool \
  --probe-name myHealthProbe
```

Проверьте балансировщик нагрузки, пулы IP-адресов и правила NAT с помощью команды [az network lb show](/cli/azure/network/lb#show).

```azurecli
az network lb show --resource-group myResourceGroup --name myLoadBalancer
```

Создайте группу безопасности сети с помощью команды [az network nsg create](/cli/azure/network/nsg#create). В следующем примере создается группа безопасности сети с именем `myNetworkSecurityGroup`.

```azurecli
az network nsg create --resource-group myResourceGroup --location westeurope \
  --name myNetworkSecurityGroup
```

Добавьте в группу безопасности сети два правила входящего трафика с помощью команды [az network nsg rule create](/cli/azure/network/nsg/rule#create). В следующем примере создается два правила (`myNetworkSecurityGroupRuleSSH` и `myNetworkSecurityGroupRuleHTTP`).

```azurecli
az network nsg rule create --resource-group myResourceGroup \
  --nsg-name myNetworkSecurityGroup --name myNetworkSecurityGroupRuleSSH \
  --protocol tcp --direction inbound --priority 1000 --source-address-prefix '*' \
  --source-port-range '*' --destination-address-prefix '*' --destination-port-range 22 \
  --access allow
az network nsg rule create --resource-group myResourceGroup \
  --nsg-name myNetworkSecurityGroup --name myNetworkSecurityGroupRuleHTTP \
  --protocol tcp --direction inbound --priority 1001 --source-address-prefix '*' \
  --source-port-range '*' --destination-address-prefix '*' --destination-port-range 80 \
  --access allow
```

Создайте первый сетевой адаптер (NIC) с помощью команды [az network nic create](/cli/azure/network/nic#create). В следующем примере создается сетевой адаптер с именем `myNic1`, который привязывается к балансировщику нагрузки `myLoadBalancer`, соответствующим пулам и группе безопасности сети `myNetworkSecurityGroup`.

```azurecli
az network nic create --resource-group myResourceGroup --location westeurope --name myNic1 \
  --vnet-name myVnet --subnet mySubnet --network-security-group myNetworkSecurityGroup \
  --lb-name myLoadBalancer --lb-address-pools myBackEndPool \
  --lb-inbound-nat-rules myLoadBalancerRuleSSH1
```

Создайте второй сетевой адаптер с помощью команды **az network nic create**. В следующем примере создается сетевой адаптер с именем `myNic2`.

```azurecli
az network nic create --resource-group myResourceGroup --location westeurope --name myNic2 \
  --vnet-name myVnet --subnet mySubnet --network-security-group myNetworkSecurityGroup \
  --lb-name myLoadBalancer --lb-address-pools myBackEndPool \
  --lb-inbound-nat-rules myLoadBalancerRuleSSH2
```

Создайте группу доступности с помощью команды [az vm availability-set create](/cli/azure/vm/availability-set#create). В следующем примере создается группа доступности с именем `myAvailabilitySet`.

```azurecli
az vm availability-set create --resource-group myResourceGroup --location westeurope \
  --name myAvailabilitySet
```

Создайте первую виртуальную машину Linux с помощью команды [az vm create](/cli/azure/vm#create). В следующем примере создается виртуальная машина `myVM1`, использующая Управляемые диски Azure. Если вы хотите использовать неуправляемые диски, прочитайте дополнительное примечание ниже.

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM1 \
    --location westeurope \
    --availability-set myAvailabilitySet \
    --nics myNic1 \
    --vnet myVnet \
    --subnet-name mySubnet \
    --nsg myNetworkSecurityGroup \
    --image UbuntuLTS \
    --ssh-key-value ~/.ssh/id_rsa.pub \
    --admin-username azureuser
```

Если вы используете Управляемые диски Azure, пропустите этот шаг. Если вы хотите использовать неуправляемые диски и создали учетную запись хранения на предыдущих шагах, необходимо добавить следующие дополнительные параметры в приведенную команду. Добавьте указанные дополнительные параметры в команду, чтобы создать неуправляемые диски в учетной записи хранения `mystorageaccount`. 

```azurecli
  --use-unmanaged-disk \
  --storage-account mystorageaccount
```

Создайте вторую виртуальную машину Linux с помощью команды **az vm create**. В следующем примере создается виртуальная машина с именем `myVM2`.

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM2 \
    --location westeurope \
    --availability-set myAvailabilitySet \
    --nics myNic2 \
    --vnet myVnet \
    --subnet-name mySubnet \
    --nsg myNetworkSecurityGroup \
    --image UbuntuLTS \
    --ssh-key-value ~/.ssh/id_rsa.pub \
    --admin-username azureuser
```

Напомню, если вы не используете Управляемые диски Azure (используются по умолчанию), добавьте приведенные ниже дополнительные параметры в команду, чтобы создать неуправляемые диски в учетной записи хранения `mystorageaccount`.

```azurecli
  --use-unmanaged-disk \
  --storage-account mystorageaccount
``` 

Проверьте, правильно ли созданы виртуальные машины, выполнив команду [az vm show](/cli/azure/vm#show).

```azurecli
az vm show --resource-group myResourceGroup --name myVM1
az vm show --resource-group myResourceGroup --name myVM2
```

Экспортируйте новую среду в шаблон с помощью команды [az group export](/cli/azure/group#export). Это позволит быстро воссоздавать новые экземпляры:

```azurecli
az group export --name myResourceGroup > myResourceGroup.json
```

## <a name="detailed-walkthrough"></a>Подробное пошаговое руководство
Подробные инструкции, приведенные ниже, поясняют действия каждой команды при создании вашей среды. Эти понятия помогут вам при создании пользовательских сред для разработки или эксплуатации.

Обязательно установите последнюю версию [Azure CLI 2.0 (предварительная версия)](/cli/azure/install-az-cli2) и войдите в учетную запись Azure с помощью команды [az login](/cli/azure/#login).

В следующих примерах замените имена параметров собственными значениями. Используемые имена параметров: `myResourceGroup`, `mystorageaccount` и `myVM`.

## <a name="create-resource-groups-and-choose-deployment-locations"></a>Создание групп ресурсов и выбор расположений развертывания
Группы ресурсов Azure — это логические сущности развертывания, содержащие данные конфигурации и другие метаданные, которые делают возможным логическое управление развертыванием ресурсов. Создайте группу ресурсов с помощью команды [az group create](/cli/azure/group#create). В следующем примере создается группа ресурсов с именем `myResourceGroup` в расположении `westeurope`:

```azurecli
az group create --name myResourceGroup --location westeurope
```

По умолчанию результаты выводятся в формате JSON (нотация объектов JavaScript). Чтобы вывести результаты в виде списка или таблицы, используйте команду [az configure --output](/cli/azure/#configure). Параметр `--output` можно добавлять к любой команде — он один раз изменяет формат выходных данных. В следующем примере показаны выходные данные команды **az group create** в формате JSON:

```json                       
{
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup",
  "location": "westeurope",
  "name": "myResourceGroup",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null
}
```

## <a name="create-a-storage-account"></a>Создайте учетную запись хранения.
Следующий шаг не является обязательным. По умолчанию виртуальная машина, созданная с помощью Azure CLI 2.0 (предварительная версия), использует Управляемые диски Azure. Эти диски полностью контролируются платформой Azure и не требуют никакой подготовки или хранилища. Дополнительные сведения об Управляемых дисках Azure см. в [обзоре Управляемых дисков Azure](../storage/storage-managed-disks-overview.md). Перейдите к разделу [Создание виртуальной сети и подсети](#create-a-virtual-network-and-subnet), если вы хотите использовать Управляемые диски Azure. 

Если необходимо использовать неуправляемые диски, нужно создать учетную запись хранения для дисков виртуальной машины и дополнительных дисков данных, которые вы захотите добавить.

В этом случае мы используем команду [az storage account create](/cli/azure/storage/account#create) и с ее помощью передаем расположение учетной записи, имя группы ресурсов, которая ее контролирует, и необходимый тип поддержки хранилища. В следующем примере создается учетная запись хранения с именем `mystorageaccount`:

```azurecli
az storage account create --resource-group myResourceGroup --location westeurope \
  --name mystorageaccount --kind Storage --sku Standard_LRS
```

Выходные данные:

```json
{
  "accessTier": null,
  "creationTime": "2016-12-07T17:59:50.090092+00:00",
  "customDomain": null,
  "encryption": null,
  "id": "/subscriptions/guid/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
  "kind": "Storage",
  "lastGeoFailoverTime": null,
  "location": "westeurope",
  "name": "mystorageaccount",
  "primaryEndpoints": {
    "blob": "https://mystorageaccount.blob.core.windows.net/",
    "file": "https://mystorageaccount.file.core.windows.net/",
    "queue": "https://mystorageaccount.queue.core.windows.net/",
    "table": "https://mystorageaccount.table.core.windows.net/"
  },
  "primaryLocation": "westeurope",
  "provisioningState": "Succeeded",
  "resourceGroup": "myresourcegroup",
  "secondaryEndpoints": null,
  "secondaryLocation": null,
  "sku": {
    "name": "Standard_LRS",
    "tier": "Standard"
  },
  "statusOfPrimary": "Available",
  "statusOfSecondary": null,
  "tags": {},
  "type": "Microsoft.Storage/storageAccounts"
}
```

Чтобы исследовать учетную запись хранения с помощью интерфейса командной строки, нужно сначала задать имена и ключи учетных записей. Используйте команду [az storage account show-connection-string](/cli/azure/storage/account#show-connection-string). Замените имя учетной записи хранения в следующем примере выбранным вами именем.

```bash
export AZURE_STORAGE_CONNECTION_STRING="$(az storage account show-connection-string --resource-group myResourceGroup --name mystorageaccount --query connectionString)"
```

Затем вы можете просмотреть данные хранилища с помощью команды [az storage container list](/cli/azure/storage/container#list).

```azurecli
az storage container list
```

Выходные данные:

```azurecli
[
  {
    "metadata": null,
    "name": "vhds",
    "properties": {
      "etag": "\"0x8D41F912D472F94\"",
      "lastModified": "2016-12-08T17:39:35+00:00",
      "lease": {
        "duration": null,
        "state": null,
        "status": null
      },
      "leaseDuration": "infinite",
      "leaseState": "leased",
      "leaseStatus": "locked"
    }
  }
]
```

## <a name="create-a-virtual-network-and-subnet"></a>Создание виртуальной сети и подсети
Далее вам понадобится создать виртуальную сеть в Azure и подсеть, в которой можно создать виртуальные машины. В следующем примере с помощью команды [az network vnet create](/cli/azure/network/vnet#create) создаются виртуальная сеть с именем `myVnet` и префиксом адреса `192.168.0.0/16`, а также подсеть с именем `mySubnet` и префиксом адреса `192.168.1.0/24`.

```azurecli
az network vnet create --resource-group myResourceGroup --location westeurope \
  --name myVnet --address-prefix 192.168.0.0/16 \
  --subnet-name mySubnet --subnet-prefix 192.168.1.0/24
```

В выходных данных отображается подсеть, логически созданная внутри виртуальной сети.

```json
{
  "addressSpace": {
    "addressPrefixes": [
      "192.168.0.0/16"
    ]
  },
  "dhcpOptions": {
    "dnsServers": []
  },
  "etag": "W/\"e95496fc-f417-426e-a4d8-c9e4d27fc2ee\"",
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet",
  "location": "westeurope",
  "name": "myVnet",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "resourceGuid": "ed62fd03-e9de-430b-84df-8a3b87cacdbb",
  "subnets": [
    {
      "addressPrefix": "192.168.1.0/24",
      "etag": "W/\"e95496fc-f417-426e-a4d8-c9e4d27fc2ee\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet",
      "ipConfigurations": null,
      "name": "mySubnet",
      "networkSecurityGroup": null,
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "resourceNavigationLinks": null,
      "routeTable": null
    }
  ],
  "tags": {},
  "type": "Microsoft.Network/virtualNetworks",
  "virtualNetworkPeerings": null
}
```


## <a name="create-a-public-ip-address"></a>Создание общедоступного IP-адреса
Теперь давайте создадим общедоступный IP-адрес (PIP), который назначается балансировщику нагрузки. Он позволит подключаться к виртуальным машинам из Интернета с помощью команды [az network public-ip create](/cli/azure/network/public-ip#create). Так как по умолчанию используется динамический адрес, мы создаем именованную запись DNS в домене **cloudapp.azure.com** с помощью параметра `--domain-name-label`. В следующем примере создается общедоступный IP-адрес `myPublicIP` с DNS-именем `mypublicdns`. DNS-имя должно быть уникальным, поэтому укажите собственное уникальное имя.

```azurecli
az network public-ip create --resource-group myResourceGroup --location westeurope \
  --name myPublicIP --dns-name mypublicdns --allocation-method static --idle-timeout 4
```

Выходные данные:

```json
{
  "publicIp": {
    "dnsSettings": {
      "domainNameLabel": "mypublicdns",
      "fqdn": "mypublicdns.westeurope.cloudapp.azure.com",
      "reverseFqdn": ""
    },
    "idleTimeoutInMinutes": 4,
    "ipAddress": "52.174.48.109",
    "provisioningState": "Succeeded",
    "publicIPAllocationMethod": "Static",
    "resourceGuid": "78218510-ea54-42d7-b2c2-44773fc14af5"
  }
}
```

Ресурс общедоступного IP-адреса выделен логически, но при этом конкретный адрес еще не назначен. Для получения IP-адреса вам потребуется балансировщик нагрузки, но он еще не создан.

## <a name="create-a-load-balancer-and-ip-pools"></a>Создание балансировщика нагрузки и пулов IP-адресов
Создание балансировщика нагрузки дает возможность распределять трафик между несколькими виртуальными машинами. Это также обеспечивает избыточность для приложения, так как работает несколько виртуальных машин, которые отвечают на запросы пользователей в случае проведения обслуживания или высокой нагрузки. В следующем примере с помощью команды [az network lb create](/cli/azure/network/lb#create) создается балансировщик нагрузки с именем `myLoadBalancer` и интерфейсный пул IP-адресов с именем `myFrontEndPool`, а также подключается ресурс `myPublicIP`.

```azurecli
az network lb create --resource-group myResourceGroup --location westeurope \
  --name myLoadBalancer --public-ip-address myPublicIP --frontend-ip-name myFrontEndPool
```

Выходные данные:

```json
{
  "loadBalancer": {
    "backendAddressPools": [
      {
        "etag": "W/\"7a05df7a-5ee2-4581-b411-4b6f048ab291\"",
        "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myLoadBalancerbepool",
        "name": "myLoadBalancerbepool",
        "properties": {
          "provisioningState": "Succeeded"
        },
        "resourceGroup": "myResourceGroup"
      }
    ],
    "frontendIPConfigurations": [
      {
        "etag": "W/\"7a05df7a-5ee2-4581-b411-4b6f048ab291\"",
        "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/frontendIPConfigurations/myFrontEndPool",
        "name": "myFrontEndPool",
        "properties": {
          "privateIPAllocationMethod": "Dynamic",
          "provisioningState": "Succeeded",
          "publicIPAddress": {
            "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP",
            "resourceGroup": "myResourceGroup"
          }
        },
        "resourceGroup": "myResourceGroup"
      }
    ],
    "inboundNatPools": [],
    "inboundNatRules": [],
    "loadBalancingRules": [],
    "outboundNatRules": [],
    "probes": [],
    "provisioningState": "Succeeded",
    "resourceGuid": "f4879d84-5ae8-4e06-8b9b-1419baa875d9"
  }
}
```

Обратите внимание на то, как мы использовали параметр `--public-ip-address` для передачи созданного ранее `myPublicIP`. Присвоение общедоступного IP-адреса балансировщику нагрузки позволяет получить доступ к нашим виртуальным машинам через Интернет.

Мы используем внутренний пул как расположение, к которому будут подключаться наши виртуальные машины. Таким образом трафик сможет проходить через балансировщик нагрузки к виртуальным машинам. Давайте создадим пул IP-адресов для внутреннего трафика с помощью команды [az network lb address-pool create](/cli/azure/network/lb/address-pool#create). В следующем примере создается внутренний пул с именем `myBackEndPool`.

```azurecli
az network lb address-pool create --resource-group myResourceGroup \
  --lb-name myLoadBalancer --name myBackEndPool
```

Фрагмент выходных данных:

```json
  "backendAddressPools": [
    {
      "backendIpConfigurations": null,
      "etag": "W/\"a61814bc-ce4c-4fb2-9d6a-5b1949078345\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myLoadBalancerbepool",
      "loadBalancingRules": null,
      "name": "myLoadBalancerbepool",
      "outboundNatRule": null,
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup"
    },
    {
      "backendIpConfigurations": null,
      "etag": "W/\"a61814bc-ce4c-4fb2-9d6a-5b1949078345\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool",
      "loadBalancingRules": null,
      "name": "myBackEndPool",
      "outboundNatRule": null,
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup"
    }
  ],
  "etag": "W/\"a61814bc-ce4c-4fb2-9d6a-5b1949078345\"",
```


## <a name="create-load-balancer-nat-rules"></a>Создание правил NAT балансировщика нагрузки
Чтобы трафик проходил через наш балансировщик нагрузки, необходимо создать правила NAT, которые задают действия для входящего или исходящего трафика. Можно указать используемый протокол, а затем сопоставить внешние порты с внутренними, если это необходимо. Используя команду [az network lb inbound-nat-rule create](/cli/azure/network/lb/inbound-nat-rule#create), мы создадим в нашей среде несколько правил, которые пропускают SSH-трафик через балансировщик нагрузки к виртуальным машинам. Мы настраиваем перенаправление TCP-портов 4222 и 4223 на TCP-порт 22 на наших виртуальных машинах (которые мы создадим позже). В следующем примере создается правило с именем `myLoadBalancerRuleSSH1`. Это правило сопоставляет TCP-порт 4222 с портом 22.

```azurecli
az network lb inbound-nat-rule create --resource-group myResourceGroup \
  --lb-name myLoadBalancer --name myLoadBalancerRuleSSH1 --protocol tcp \
  --frontend-port 4222 --backend-port 22 --frontend-ip-name myFrontEndPool
```

Выходные данные:

```json
{
  "backendIpConfiguration": null,
  "backendPort": 22,
  "enableFloatingIp": false,
  "etag": "W/\"72843cf8-b5fb-4655-9ac8-9cbbbbf1205a\"",
  "frontendIpConfiguration": {
    "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/frontendIPConfigurations/myFrontEndPool",
    "resourceGroup": "myResourceGroup"
  },
  "frontendPort": 4222,
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH1",
  "idleTimeoutInMinutes": 4,
  "name": "myLoadBalancerRuleSSH1",
  "protocol": "Tcp",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup"
}
```

Повторите эту процедуру для второго правила NAT для трафика SSH. В следующем примере создается правило с именем `myLoadBalancerRuleSSH2`. Это правило сопоставляет TCP-порт 4223 с портом 22.

```azurecli
az network lb inbound-nat-rule create --resource-group myResourceGroup \
  --lb-name myLoadBalancer --name myLoadBalancerRuleSSH2 --protocol tcp \
  --frontend-port 4223 --backend-port 22 --frontend-ip-name myFrontEndPool
```

## <a name="create-a-load-balancer-health-probe"></a>Создание пробы работоспособности балансировщика нагрузки
Проба работоспособности периодически проверяет, соответствующим ли образом работают и отвечают на запросы виртуальные машины, которые расположены за балансировщиком нагрузки. Если это не так, они выводятся из эксплуатации, чтобы пользователи не могли их использовать. Можно определить пользовательские проверки для пробы работоспособности, указав интервалы и значения времени ожидания. Дополнительные сведения о пробах работоспособности см. в статье [Проверки балансировщика нагрузки](../load-balancer/load-balancer-custom-probe-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). В следующем примере с помощью команды [az network lb probe create](/cli/azure/network/lb/probe#create) создается TCP-проба работоспособности с именем `myHealthProbe`.

```azurecli
az network lb probe create --resource-group myResourceGroup --lb-name myLoadBalancer \
  --name myHealthProbe --protocol tcp --port 80 --interval 15 --threshold 4
```

Выходные данные:

```json
{
  "etag": "W/\"757018f6-b70a-4651-b717-48b511d82ba0\"",
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/probes/myHealthProbe",
  "intervalInSeconds": 15,
  "loadBalancingRules": null,
  "name": "myHealthProbe",
  "numberOfProbes": 4,
  "port": 80,
  "protocol": "Tcp",
  "provisioningState": "Succeeded",
  "requestPath": null,
  "resourceGroup": "myResourceGroup"
}
```

Здесь мы указали интервал в 15 секунд для проверок работоспособности. Допускается пропуск до&4; проб (одна минута), прежде чем балансировщик нагрузки начнет считать, что узел перестал функционировать.

Дальше создадим правило NAT для TCP-порта 80 для веб-трафика, которое привязывает правило к нашим пулам IP-адресов. Привязав правило к пулу IP-адресов, а не к виртуальным машинам по отдельности, мы сможем добавлять виртуальные машины в пул IP-адресов и удалять их из него. Балансировщик нагрузки будет автоматически регулировать поток трафика. В следующем примере с помощью команды [az network lb rule create](/cli/azure/network/lb/rule#create) создается правило с именем `myLoadBalancerRuleWeb`, сопоставляющее TCP-порт 80 с портом 80, и подключается проба работоспособности с именем `myHealthProbe`.

```azurecli
az network lb rule create --resource-group myResourceGroup --lb-name myLoadBalancer \
  --name myLoadBalancerRuleWeb --protocol tcp --frontend-port 80 --backend-port 80 \
  --frontend-ip-name myFrontEndPool --backend-pool-name myBackEndPool \
  --probe-name myHealthProbe
```

Выходные данные:

```json
{
  "backendAddressPool": {
    "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool",
    "resourceGroup": "myResourceGroup"
  },
  "backendPort": 80,
  "enableFloatingIp": false,
  "etag": "W/\"f0d77680-bf42-4d11-bfab-5d2c541bee56\"",
  "frontendIpConfiguration": {
    "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/frontendIPConfigurations/myFrontEndPool",
    "resourceGroup": "myResourceGroup"
  },
  "frontendPort": 80,
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/loadBalancingRules/myLoadBalancerRuleWeb",
  "idleTimeoutInMinutes": 4,
  "loadDistribution": "Default",
  "name": "myLoadBalancerRuleWeb",
  "probe": {
    "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/probes/myHealthProbe",
    "resourceGroup": "myResourceGroup"
  },
  "protocol": "Tcp",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup"
}
```

## <a name="verify-the-load-balancer"></a>Проверка балансировщика нагрузки
Настройка балансировщика нагрузки завершена. Вот какие действия были выполнены.

1. Вы создали балансировщик нагрузки.
2. Затем вы создали интерфейсный пул IP-адресов и назначили ему общедоступный IP-адрес.
3. Потом вы создали внутренний пул IP-адресов, к которому могут подключаться виртуальные машины.
4. Вы создали правила NAT, которые разрешают управлять виртуальными машинами через SSH-подключение, а также правило, открывающее TCP-порт 80 для нашего веб-приложения.
5. Вы добавили пробу работоспособности для периодической проверки виртуальных машин. Эта проба работоспособности гарантирует, что пользователи не попытаются обратиться к виртуальной машине, которая больше не работает или не обрабатывает содержимое.

Используя команду [az network lb show](/cli/azure/network/lb#show), давайте посмотрим, как теперь выглядит балансировщик нагрузки.

```azurecli
az network lb show --resource-group myResourceGroup --name myLoadBalancer
```

Выходные данные:

```json
{
  "backendAddressPools": [
    {
      "backendIpConfigurations": null,
      "etag": "W/\"a0e313a1-6de1-48be-aeb6-df57188d708c\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myLoadBalancerbepool",
      "loadBalancingRules": null,
      "name": "myLoadBalancerbepool",
      "outboundNatRule": null,
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup"
    },
    {
      "backendIpConfigurations": null,
      "etag": "W/\"a0e313a1-6de1-48be-aeb6-df57188d708c\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool",
      "loadBalancingRules": null,
      "name": "myBackEndPool",
      "outboundNatRule": null,
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup"
    }
  ],
  "etag": "W/\"a0e313a1-6de1-48be-aeb6-df57188d708c\"",
  "frontendIpConfigurations": [
    {
      "etag": "W/\"a0e313a1-6de1-48be-aeb6-df57188d708c\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/frontendIPConfigurations/LoadBalancerFrontEnd",
      "inboundNatPools": null,
      "inboundNatRules": null,
      "loadBalancingRules": null,
      "name": "LoadBalancerFrontEnd",
      "outboundNatRules": null,
      "privateIpAddress": null,
      "privateIpAllocationMethod": "Dynamic",
      "provisioningState": "Succeeded",
      "publicIpAddress": {
        "dnsSettings": null,
        "etag": null,
        "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/PublicIPmyLoadBalancer",
        "idleTimeoutInMinutes": null,
        "ipAddress": null,
        "ipConfiguration": null,
        "location": null,
        "name": null,
        "provisioningState": null,
        "publicIpAddressVersion": null,
        "publicIpAllocationMethod": null,
        "resourceGroup": "myResourceGroup",
        "resourceGuid": null,
        "tags": null,
        "type": null
      },
      "resourceGroup": "myResourceGroup",
      "subnet": null
    },
    {
      "etag": "W/\"a0e313a1-6de1-48be-aeb6-df57188d708c\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/frontendIPConfigurations/myFrontEndPool",
      "inboundNatPools": null,
      "inboundNatRules": null,
      "loadBalancingRules": null,
      "name": "myFrontEndPool",
      "outboundNatRules": null,
      "privateIpAddress": null,
      "privateIpAllocationMethod": "Dynamic",
      "provisioningState": "Succeeded",
      "publicIpAddress": {
        "dnsSettings": null,
        "etag": null,
        "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP",
        "idleTimeoutInMinutes": null,
        "ipAddress": null,
        "ipConfiguration": null,
        "location": null,
        "name": null,
        "provisioningState": null,
        "publicIpAddressVersion": null,
        "publicIpAllocationMethod": null,
        "resourceGroup": "myResourceGroup",
        "resourceGuid": null,
        "tags": null,
        "type": null
      },
      "resourceGroup": "myResourceGroup",
      "subnet": null
    }
  ],
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer",
  "inboundNatPools": [],
  "inboundNatRules": [],
  "loadBalancingRules": [],
  "location": "westeurope",
  "name": "myLoadBalancer",
  "outboundNatRules": [],
  "probes": [],
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "resourceGuid": "b5815801-b53d-4c22-aafc-2a9064f90f3c",
  "tags": {},
  "type": "Microsoft.Network/loadBalancers"
}
```

## <a name="create-a-network-security-group-and-rules"></a>Создание группы безопасности сети и правил
Теперь мы создадим группу безопасности сети и правила для входящего трафика, управляющие доступом к сетевому адаптеру. Группу безопасности сети можно применить к сетевому адаптеру или подсети. Правила, которые вы определяете, позволяют управлять исходящим и входящим потоком трафика виртуальной машины. В следующем примере с помощью команды [az network nsg create](/cli/azure/network/nsg#create) создается группа безопасности сети с именем `myNetworkSecurityGroup`.

```azurecli
az network nsg create --resource-group myResourceGroup --location westeurope \
  --name myNetworkSecurityGroup
```

Используя команду [az network nsg rule create](/cli/azure/network/nsg/rule#create), давайте добавим для этой группы безопасности правило для входящего трафика, чтобы разрешить входящие подключения через порт 22 (для поддержки SSH). В следующем примере создается правило с именем `myNetworkSecurityGroupRuleSSH`. Это правило разрешает TCP-трафик через порт 22.

```azurecli
az network nsg rule create --resource-group myResourceGroup \
  --nsg-name myNetworkSecurityGroup --name myNetworkSecurityGroupRuleSSH \
  --protocol tcp --direction inbound --priority 1000 \
  --source-address-prefix '*' --source-port-range '*' \
  --destination-address-prefix '*' --destination-port-range 22 --access allow
```

Теперь добавим для этой группы безопасности сети правило для входящего трафика, чтобы разрешить входящие подключения через порт 80 (для поддержки веб-трафика). В следующем примере создается правило с именем `myNetworkSecurityGroupRuleHTTP`. Это правило разрешает TCP-трафик через порт 80.

```azurecli
az network nsg rule create --resource-group myResourceGroup \
  --nsg-name myNetworkSecurityGroup --name myNetworkSecurityGroupRuleHTTP \
  --protocol tcp --direction inbound --priority 1001 \
  --source-address-prefix '*' --source-port-range '*' \
  --destination-address-prefix '*' --destination-port-range 80 --access allow
```

> [!NOTE]
> Правило для входящего трафика является фильтром входящих сетевых подключений. В этом примере мы привяжем группу безопасности сети к виртуальной сетевой карте виртуальной машины. Это значит, что любой запрос к порту 22 передается в сетевую карту виртуальной машины. Это правило для входящего сетевого подключения, а не конечной точки, как это было бы в классическом развертывании. Для открытия порта необходимо оставить для `--source-port-range` значение "\*" (значение по умолчанию), чтобы разрешить прием входящих запросов от **любого** запрашивающего порта. Обычно порты являются динамическими.

Чтобы проверить группу безопасности сети и правила, выполните команду [az network nsg show](/cli/azure/network/nsg#show).

```azurecli
az network nsg show --resource-group myResourceGroup --name myNetworkSecurityGroup
```

Выходные данные:

```json
{
  "defaultSecurityRules": [
    {
      "access": "Allow",
      "description": "Allow inbound traffic from all VMs in VNET",
      "destinationAddressPrefix": "VirtualNetwork",
      "destinationPortRange": "*",
      "direction": "Inbound",
      "etag": "W/\"2b656589-f332-4ba4-92f3-afb3be5c192c\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/defaultSecurityRules/AllowVnetInBound",
      "name": "AllowVnetInBound",
      "priority": 65000,
      "protocol": "*",
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "sourceAddressPrefix": "VirtualNetwork",
      "sourcePortRange": "*"
    },
    {
      "access": "Allow",
      "description": "Allow inbound traffic from azure load balancer",
      "destinationAddressPrefix": "*",
      "destinationPortRange": "*",
      "direction": "Inbound",
      "etag": "W/\"2b656589-f332-4ba4-92f3-afb3be5c192c\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/defaultSecurityRules/AllowAzureLoadBalancerInBound",
      "name": "AllowAzureLoadBalancerInBound",
      "priority": 65001,
      "protocol": "*",
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "sourceAddressPrefix": "AzureLoadBalancer",
      "sourcePortRange": "*"
    },
    {
      "access": "Deny",
      "description": "Deny all inbound traffic",
      "destinationAddressPrefix": "*",
      "destinationPortRange": "*",
      "direction": "Inbound",
      "etag": "W/\"2b656589-f332-4ba4-92f3-afb3be5c192c\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/defaultSecurityRules/DenyAllInBound",
      "name": "DenyAllInBound",
      "priority": 65500,
      "protocol": "*",
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "sourceAddressPrefix": "*",
      "sourcePortRange": "*"
    },
    {
      "access": "Allow",
      "description": "Allow outbound traffic from all VMs to all VMs in VNET",
      "destinationAddressPrefix": "VirtualNetwork",
      "destinationPortRange": "*",
      "direction": "Outbound",
      "etag": "W/\"2b656589-f332-4ba4-92f3-afb3be5c192c\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/defaultSecurityRules/AllowVnetOutBound",
      "name": "AllowVnetOutBound",
      "priority": 65000,
      "protocol": "*",
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "sourceAddressPrefix": "VirtualNetwork",
      "sourcePortRange": "*"
    },
    {
      "access": "Allow",
      "description": "Allow outbound traffic from all VMs to Internet",
      "destinationAddressPrefix": "Internet",
      "destinationPortRange": "*",
      "direction": "Outbound",
      "etag": "W/\"2b656589-f332-4ba4-92f3-afb3be5c192c\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/defaultSecurityRules/AllowInternetOutBound",
      "name": "AllowInternetOutBound",
      "priority": 65001,
      "protocol": "*",
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "sourceAddressPrefix": "*",
      "sourcePortRange": "*"
    },
    {
      "access": "Deny",
      "description": "Deny all outbound traffic",
      "destinationAddressPrefix": "*",
      "destinationPortRange": "*",
      "direction": "Outbound",
      "etag": "W/\"2b656589-f332-4ba4-92f3-afb3be5c192c\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/defaultSecurityRules/DenyAllOutBound",
      "name": "DenyAllOutBound",
      "priority": 65500,
      "protocol": "*",
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "sourceAddressPrefix": "*",
      "sourcePortRange": "*"
    }
  ],
  "etag": "W/\"2b656589-f332-4ba4-92f3-afb3be5c192c\"",
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup",
  "location": "westeurope",
  "name": "myNetworkSecurityGroup",
  "networkInterfaces": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "resourceGuid": "79c2c293-ee3e-4616-bf9c-4741ff1f708a",
  "securityRules": [
    {
      "access": "Allow",
      "description": null,
      "destinationAddressPrefix": "*",
      "destinationPortRange": "22",
      "direction": "Inbound",
      "etag": "W/\"2b656589-f332-4ba4-92f3-afb3be5c192c\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/securityRules/myNetworkSecurityGroupRuleSSH",
      "name": "myNetworkSecurityGroupRuleSSH",
      "priority": 1000,
      "protocol": "tcp",
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "sourceAddressPrefix": "*",
      "sourcePortRange": "*"
    },
    {
      "access": "Allow",
      "description": null,
      "destinationAddressPrefix": "*",
      "destinationPortRange": "80",
      "direction": "Inbound",
      "etag": "W/\"2b656589-f332-4ba4-92f3-afb3be5c192c\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/securityRules/myNetworkSecurityGroupRuleHTTP",
      "name": "myNetworkSecurityGroupRuleHTTP",
      "priority": 1001,
      "protocol": "tcp",
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "sourceAddressPrefix": "*",
      "sourcePortRange": "*"
    }
  ],
  "subnets": null,
  "tags": {},
  "type": "Microsoft.Network/networkSecurityGroups"
}
```

## <a name="create-an-nic-to-use-with-the-linux-vm"></a>Создание сетевой карты для виртуальной машины Linux
Работой сетевых карт можно управлять программно, так как к ним можно применить правила. Кроме того, можно использовать несколько сетевых карт. С помощью команды [az network nic create](https://docs.microsoft.com/en-us/cli/azure/network/nic#create) вы подключаете сетевую карту к внутреннему пулу IP-адресов и связываете ее с правилом NAT, которое разрешает использовать SSH-трафик и группу безопасности сети.

В следующем примере создается сетевой адаптер с именем `myNic1`.

```azurecli
az network nic create --resource-group myResourceGroup --location westeurope --name myNic1 \
  --vnet-name myVnet --subnet mySubnet --network-security-group myNetworkSecurityGroup \
  --lb-name myLoadBalancer --lb-address-pools myBackEndPool \
  --lb-inbound-nat-rules myLoadBalancerRuleSSH1
```

Выходные данные:

```json
{
  "newNIC": {
    "dnsSettings": {
      "appliedDnsServers": [],
      "dnsServers": []
    },
    "enableIPForwarding": false,
    "ipConfigurations": [
      {
        "etag": "W/\"a76b5c0d-14e1-4a99-afd4-5cd8ac0465ca\"",
        "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic1/ipConfigurations/ipconfig1",
        "name": "ipconfig1",
        "properties": {
          "loadBalancerBackendAddressPools": [
            {
              "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool",
              "resourceGroup": "myResourceGroup"
            }
          ],
          "loadBalancerInboundNatRules": [
            {
              "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH1",
              "resourceGroup": "myResourceGroup"
            }
          ],
          "primary": true,
          "privateIPAddress": "192.168.1.4",
          "privateIPAllocationMethod": "Dynamic",
          "provisioningState": "Succeeded",
          "subnet": {
            "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet",
            "resourceGroup": "myResourceGroup"
          }
        },
        "resourceGroup": "myResourceGroup"
      }
    ],
    "networkSecurityGroup": {
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup",
      "resourceGroup": "myResourceGroup"
    },
    "provisioningState": "Succeeded",
    "resourceGuid": "838977cb-cf4b-4c4a-9a32-0ddec7dc818b"
  }
}
```

Теперь мы создаем вторую сетевую карту и также привязываем ее к внутреннему пулу IP-адресов. В этот раз второе правило NAT разрешает трафик SSH. В следующем примере создается сетевой адаптер с именем `myNic2`.

```azurecli
az network nic create --resource-group myResourceGroup --location westeurope --name myNic2 \
  --vnet-name myVnet --subnet mySubnet --network-security-group myNetworkSecurityGroup \
  --lb-name myLoadBalancer --lb-address-pools myBackEndPool \
  --lb-inbound-nat-rules myLoadBalancerRuleSSH2
```


## <a name="create-an-availability-set"></a>"Создать группу доступности"
Группы доступности помогают распределить виртуальные машины между доменами сбоя и доменами обновления. Давайте с помощью команды[az vm availability-set create](/cli/azure/vm/availability-set#create) создадим группу доступности для виртуальных машин. В следующем примере создается группа доступности с именем `myAvailabilitySet`.

```azurecli
az vm availability-set create --resource-group myResourceGroup --location westeurope \
  --name myAvailabilitySet
```

Домены сбоя определяют группу виртуальных машин, совместно использующих общий источник питания и сетевой коммутатор. По умолчанию виртуальные машины, настроенные в группе доступности, разделяются между несколькими доменами сбоя, которых может быть не больше трех. Суть в том, что сбой оборудования в одном из этих доменов сбоя не влияет на все виртуальные машины, на которых выполняется ваше приложение. Azure автоматически распределяет виртуальные машины между доменами сбоя при помещении их в группу доступности.

Домены обновления — это группы виртуальных машин и базовое физическое оборудование, которое может быть перезагружено одновременно. Перезагрузка доменов обновления при запланированном обслуживании может выполняться не по порядку, но одновременно перезагружается только один домен обновления. Опять же, Azure автоматически распределяет виртуальные машины между доменами обновления при помещении их в группу доступности.

Узнайте больше об [управлении доступностью виртуальных машин](virtual-machines-linux-manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).


## <a name="create-the-linux-vms"></a>Создание виртуальной машины Linux
Вы создали ресурсы сети для доступных через Интернет виртуальных машин. Теперь давайте создадим эти виртуальные машины и защитим их ключом SSH без пароля. В этом случае мы создадим виртуальную машину Ubuntu на базе последней версии LTS. Чтобы найти этот образ, мы воспользуемся командой [az vm image list](/cli/azure/vm/image#list), как описано в статье о [поиске образов виртуальных машин Azure](virtual-machines-linux-cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Кроме того, мы укажем ключ SSH для аутентификации. Если у вас нет ключей SSH, [создайте их](virtual-machines-linux-mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Кроме того, можно использовать метод `--admin-password` для проверки подлинности подключений по протоколу SSH после создания виртуальной машины. Обычно этот метод менее безопасен.

С помощью команды [az vm create](/cli/azure/vm#create) мы создадим виртуальную машину, собрав воедино все наши ресурсы и информацию. В следующем примере создается виртуальная машина `myVM1`, использующая Управляемые диски Azure. Если вы хотите использовать неуправляемые диски, прочитайте дополнительное примечание ниже.

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM1 \
    --location westeurope \
    --availability-set myAvailabilitySet \
    --nics myNic1 \
    --vnet myVnet \
    --subnet-name mySubnet \
    --nsg myNetworkSecurityGroup \
    --image UbuntuLTS \
    --ssh-key-value ~/.ssh/id_rsa.pub \
    --admin-username azureuser
```

Если вы используете Управляемые диски Azure, пропустите этот шаг. Если вы хотите использовать неуправляемые диски и создали учетную запись хранения на предыдущих шагах, необходимо добавить следующие дополнительные параметры в приведенную команду. Добавьте указанные дополнительные параметры в команду, чтобы создать неуправляемые диски в учетной записи хранения `mystorageaccount`. 

```azurecli
  --use-unmanaged-disk \
  --storage-account mystorageaccount
```

Выходные данные:

```json
{
  "fqdn": "",
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM1",
  "macAddress": "",
  "privateIpAddress": "",
  "publicIpAddress": "",
  "resourceGroup": "myResourceGroup"
}
```

К созданной виртуальной машине можно сразу же подключиться с помощью используемых по умолчанию ключей SSH. Обязательно укажите соответствующий порт, так как подключение проходит через балансировщик нагрузки. Для первой виртуальной машины мы устанавливаем правило NAT, которое перенаправляет на нее трафик с порта 4222.

```bash
ssh ops@mypublicdns.westeurope.cloudapp.azure.com -p 4222 -i ~/.ssh/id_rsa.pub
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
az vm create \
    --resource-group myResourceGroup \
    --name myVM2 \
    --location westeurope \
    --availability-set myAvailabilitySet \
    --nics myNic2 \
    --vnet myVnet \
    --subnet-name mySubnet \
    --nsg myNetworkSecurityGroup \
    --image UbuntuLTS \
    --ssh-key-value ~/.ssh/id_rsa.pub \
    --admin-username azureuser
```

Напомню, если вы не используете Управляемые диски Azure (используются по умолчанию), добавьте приведенные ниже дополнительные параметры в команду, чтобы создать неуправляемые диски в учетной записи хранения `mystorageaccount`.

```azurecli
  --use-unmanaged-disk \
  --storage-account mystorageaccount
``` 

На данном этапе имеются работающие виртуальные машины Ubuntu, расположенные за балансировщиком нагрузки в Azure, входить на которые можно только с помощью пары ключей SSH (так как пароли отключены). Можно установить nginx или httpd, развернуть веб-приложение и посмотреть, как трафик проходит через балансировщик нагрузки на обе виртуальные машины.


## <a name="export-the-environment-as-a-template"></a>Экспорт среды как шаблона
Теперь, когда вы создали эту среду, предположим, что вам требуется создать дополнительную среду разработки с теми же параметрами или соответствующую рабочую среду. Resource Manager использует шаблоны JSON, которые определяют все параметры среды. Можно полностью создавать среды, ссылаясь на этот шаблон JSON. Вы можете [создавать шаблоны JSON вручную](../azure-resource-manager/resource-group-authoring-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) или экспортировать существующую среду для создания шаблона JSON автоматически. Чтобы экспортировать группу ресурсов, используйте команду [az group export](/cli/azure/group#export) следующим образом:

```azurecli
az group export --name myResourceGroup > myResourceGroup.json
```

С помощью этой команды в текущем рабочем каталоге создается файл `myResourceGroup.json`. При создании среды на основе этого шаблона запрашиваются все имена ресурсов, включая балансировщик нагрузки, сетевые интерфейсы или виртуальные машины. Эти имена можно указать в файле шаблона, добавив параметр `--include-parameter-default-value` в команду **az group export**, показанную ранее. Измените шаблон JSON, чтобы указать имена ресурсов, или [создайте файл parameters.json](../azure-resource-manager/resource-group-authoring-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) , задающий их.

Чтобы создать среду с помощью шаблона, используйте команду [az group deployment create](/cli/azure/group/deployment#create) следующим образом:

```azurecli
az group deployment create --resource-group myNewResourceGroup \
  --template-file myResourceGroup.json
```

Вы можете прочитать [дополнительные сведения о развертывании из шаблонов](../azure-resource-manager/resource-group-template-deploy-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Узнайте, как поэтапно обновлять среды, использовать файл параметров и обращаться к шаблонам из единого места хранения.

## <a name="next-steps"></a>Дальнейшие действия
Теперь вы готовы приступить к работе с несколькими сетевыми компонентами и виртуальными машинами. Используя этот пример среды, можно создать приложение на основе представленных здесь основных компонентов.



<!--HONumber=Feb17_HO2-->



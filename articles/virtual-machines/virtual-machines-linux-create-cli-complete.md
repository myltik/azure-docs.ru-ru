<properties
   pageTitle="Создание полной среды Linux с помощью интерфейса командной строки Azure | Microsoft Azure"
   description="Узнайте, как с помощью Azure CLI создать ";с нуля"; хранилище, виртуальную машину Linux, виртуальную сеть и подсеть, балансировщик нагрузки, сетевую карту, общедоступный IP-адрес и группу безопасности сети."
   services="virtual-machines-linux"
   documentationCenter="virtual-machines"
   authors="iainfoulds"
   manager="timlt"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure"
   ms.date="06/10/2016"
   ms.author="iainfou"/>

# Создание полной среды Linux с помощью интерфейса командной строки Azure

В этой статье мы создадим простую сеть с балансировщиком нагрузки и парой виртуальных машин, подходящих для разработки и простых вычислений. Мы поэтапно выполним полное развертывание, от первой до последней команды, в результате чего создадим две защищенные рабочие виртуальные машины Linux, к которым можно подключиться откуда угодно через Интернет. Затем вы сможете работать с более сложными сетями и средами.

Попутно вы ознакомитесь с иерархией зависимостей модели развертывания с помощью Resource Manager и узнаете, какие возможности она дает. Узнав, как устроена система, вы сможете воссоздать ее гораздо быстрее с помощью [шаблонов Azure Resource Manager](../resource-group-authoring-templates.md). Кроме того, когда вы узнаете, как взаимодействуют части среды, создавать шаблоны для их автоматизации станет проще.

Наша среда содержит:

- две виртуальные машины в группе доступности;
- балансировщик нагрузки с правилом балансировки нагрузки для порта 80;
- правила группы безопасности сети для защиты виртуальных машин от нежелательного трафика.

![Обзор базовой среды](./media/virtual-machines-linux-create-cli-complete/environment_overview.png)

Для создания этой настраиваемой среды потребуется последняя версия [Azure CLI](../xplat-cli-install.md) в режиме Resource Manager (`azure config mode arm`). Кроме того, потребуется инструмент анализа JSON. В этом примере используются [jq](https://stedolan.github.io/jq/).

## Быстрые команды
Для создания пользовательской среды можно использовать следующие быстрые команды. Чтобы узнать больше о том, какие действия при создании среды выполняет каждая команда, ознакомьтесь с [приведенным ниже подробным пошаговым руководством](#detailed-walkthrough).

Создайте группу ресурсов:

```bash
azure group create TestRG -l westeurope
```

Проверьте группу ресурсов с помощью средства синтаксического анализа JSON.

```bash
azure group show TestRG --json | jq '.'
```

Создайте учетную запись хранения.

```bash
azure storage account create -g TestRG -l westeurope --kind Storage --sku-name GRS computeteststore
```

Проверьте ее с помощью средства синтаксического анализа JSON.

```bash
azure storage account show -g TestRG computeteststore --json | jq '.'
```

Создайте виртуальную сеть:

```bash
azure network vnet create -g TestRG -n TestVNet -a 192.168.0.0/16 -l westeurope
```

Создайте подсеть.

```bash
azure network vnet subnet create -g TestRG -e TestVNet -n FrontEnd -a 192.168.1.0/24
```

Проверьте виртуальную сеть и подсеть с помощью средства синтаксического анализа JSON.


```bash
azure network vnet show TestRG TestVNet --json | jq '.'
```

Создайте общедоступный IP-адрес.

```bash
azure network public-ip create -g TestRG -n TestLBPIP -l westeurope -d testlb -a static -i 4
```

Создайте балансировщик нагрузки.

```bash
azure network lb create -g TestRG -n TestLB -l westeurope
```

Создайте интерфейсный пул IP-адресов для балансировщика нагрузки и привяжите к нему общедоступный IP-адрес.

```bash
azure network lb frontend-ip create -g TestRG -l TestLB -n TestFrontEndPool -i TestLBPIP
```

Создайте внутренний пул IP-адресов для балансировщика нагрузки.

```bash
azure network lb address-pool create -g TestRG -l TestLB -n TestBackEndPool
```

Создайте правила NAT для входящего трафика SSH для балансировщика нагрузки.

```bash
azure network lb inbound-nat-rule create -g TestRG -l TestLB -n VM1-SSH -p tcp -f 4222 -b 22
azure network lb inbound-nat-rule create -g TestRG -l TestLB -n VM2-SSH -p tcp -f 4223 -b 22
```

Создайте правила NAT для входящего трафика Интернета для балансировщика нагрузки.

```bash
azure network lb rule create -g TestRG -l TestLB -n WebRule -p tcp -f 80 -b 80 \
     -t TestFrontEndPool -o TestBackEndPool
```

Создайте пробу работоспособности балансировщика нагрузки.

```bash
azure network lb probe create -g TestRG -l TestLB -n HealthProbe -p "http" -f healthprobe.aspx -i 15 -c 4
```

Проверьте балансировщик нагрузки, пулы IP-адресов и правила NAT с помощью средства синтаксического анализа JSON.

```bash
azure network lb show -g TestRG -n TestLB --json | jq '.'
```

Создайте первую сетевую карту.

```bash
azure network nic create -g TestRG -n LB-NIC1 -l westeurope --subnet-vnet-name TestVNet --subnet-name FrontEnd \
    -d "/subscriptions/########-####-####-####-############/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/backendAddressPools/TestBackEndPool" \
    -e "/subscriptions/########-####-####-####-############/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/inboundNatRules/VM1-SSH"
```

Создайте вторую сетевую карту.

```bash
azure network nic create -g TestRG -n LB-NIC2 -l westeurope --subnet-vnet-name TestVNet --subnet-name FrontEnd \
    -d "/subscriptions/########-####-####-####-############/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/backendAddressPools/TestBackEndPool" \
    -e "/subscriptions/########-####-####-####-############/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/inboundNatRules/VM2-SSH"
```

Проверьте эти сетевые карты с помощью средства синтаксического анализа JSON.

```bash
azure network nic show TestRG LB-NIC1 --json | jq '.'
azure network nic show TestRG LB-NIC2 --json | jq '.'
```

Создайте группу безопасности сети.

```bash
azure network nsg create -g TestRG -n TestNSG -l westeurope
```

Добавьте правила входящего трафика для этой группы безопасности сети.

```bash
azure network nsg rule create --protocol tcp --direction inbound --priority 1000 \
    --destination-port-range 22 --access allow -g TestRG -a TestNSG -n SSHRule
azure network nsg rule create --protocol tcp --direction inbound --priority 1001 \
    --destination-port-range 80 --access allow -g TestRG -a TestNSG -n HTTPRule
```

Проверьте группу безопасности сети и правила входящего трафика с помощью средства синтаксического анализа JSON.

```bash
azure network nsg show -g TestRG -n TestNSG --json | jq '.'
```

Привяжите группу безопасности сети к сетевым картам.

```bash
azure network nic set -g TestRG -n LB-NIC1 -o TestNSG
azure network nic set -g TestRG -n LB-NIC2 -o TestNSG
```

Создайте группу доступности.

```bash
azure availset create -g TestRG -n TestAvailSet -l westeurope
```

Создайте первую виртуальную машину Linux.

```bash
azure vm create \
    --resource-group TestRG \
    --name TestVM1 \
    --location westeurope \
    --os-type linux \
    --availset-name TestAvailSet \
    --nic-name LB-NIC1 \
    --vnet-name TestVnet \
    --vnet-subnet-name FrontEnd \
    --storage-account-name computeteststore \
    --image-urn canonical:UbuntuServer:14.04.4-LTS:latest \
    --ssh-publickey-file ~/.ssh/id_rsa.pub \
    --admin-username ops
```

Создайте вторую виртуальную машину Linux.

```bash
azure vm create \
    --resource-group TestRG \
    --name TestVM2 \
    --location westeurope \
    --os-type linux \
    --availset-name TestAvailSet \
    --nic-name LB-NIC2 \
    --vnet-name TestVnet \
    --vnet-subnet-name FrontEnd \
    --storage-account-name computeteststore \
    --image-urn canonical:UbuntuServer:14.04.4-LTS:latest \
    --ssh-publickey-file ~/.ssh/id_rsa.pub \
    --admin-username ops
```

С помощью средства синтаксического анализа JSON проверьте все созданные компоненты.

```bash
azure vm show -g TestRG -n TestVM1 --json | jq '.'
azure vm show -g TestRG -n TestVM2 --json | jq '.'
```

Экспортируйте созданную среду в шаблон, чтобы можно было быстро воссоздавать новые экземпляры.

```bash
azure resource export TestRG
```

## Подробное пошаговое руководство
Подробные инструкции, приведенные ниже, поясняют действия каждой команды при создании вашей среды. Эти понятия помогут вам при создании пользовательских сред для разработки или эксплуатации.

## Создание групп ресурсов и выбор расположений развертывания

Группы ресурсов Azure — это логические сущности развертывания, содержащие данные конфигурации и другие метаданные, которые делают возможным логическое управление развертыванием ресурсов.

```bash
azure group create TestRG westeurope
```

Выходные данные:

```bash                        
info:    Executing command group create
+ Getting resource group TestRG
+ Creating resource group TestRG
info:    Created resource group TestRG
data:    Id:                  /subscriptions/<yoursub>/resourceGroups/TestRG
data:    Name:                TestRG
data:    Location:            westeurope
data:    Provisioning State:  Succeeded
data:    Tags: null
data:
info:    group create command OK
```

## Создайте учетную запись хранения.

Необходимы учетные записи хранения для дисков виртуальной машины и других дисков данных, которые вы захотите добавить. Учетные записи хранения создаются практически сразу после создания групп ресурсов.

В этом случае мы используем команду `azure storage account create` и с ее помощью передаем расположение учетной записи, имя группы ресурсов, которая ее контролирует, и нужный вам тип поддержки хранилища.

```bash
azure storage account create \  
--location westeurope \
--resource-group TestRG \
--kind Storage --sku-name GRS \
computeteststore
```

Выходные данные:

```bash
info:    Executing command storage account create
+ Creating storage account
info:    storage account create command OK
ahmet•~/workspace/keygen» azure group show testrg
info:    Executing command group show
+ Listing resource groups
+ Listing resources for the group
data:    Id:                  /subscriptions/<guid>/resourceGroups/TestRG
data:    Name:                TestRG
data:    Location:            westeurope
data:    Provisioning State:  Succeeded
data:    Tags: null
data:    Resources:
data:
data:      Id      : /subscriptions/<guid>/resourceGroups/TestRG/providers/Microsoft.Storage/storageAccounts/computeteststore
data:      Name    : computeteststore
data:      Type    : storageAccounts
data:      Location: westeurope
data:      Tags    :
data:
data:    Permissions:
data:      Actions: *
data:      NotActions:
data:
info:    group show command OK
```

Воспользуемся инструментом [jq](https://stedolan.github.io/jq/) с параметром `--json` интерфейса командной строки Azure (Azure CLI), чтобы проверить нашу группу ресурсов с помощью команды `azure group show`. (Можно использовать **jsawk** или любую предпочитаемую библиотеку языка для анализа JSON.)

```bash
azure group show TestRG --json | jq                                                                                      
```


Выходные данные:

```bash
{
  "tags": {},
  "id": "/subscriptions/<guid>/resourceGroups/TestRG",
  "name": "TestRG",
  "provisioningState": "Succeeded",
  "location": "westeurope",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "resources": [
    {
      "id": "/subscriptions/<guid>/resourceGroups/TestRG/providers/Microsoft.Storage/storageAccounts/computeteststore",
      "name": "computeteststore",
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

Чтобы исследовать учетную запись хранения с помощью интерфейса командной строки, нужно сначала задать имена и ключи учетных записей с помощью варианта следующей команды. Замените имя учетной записи хранения в следующем примере выбранным вами именем.

```
AZURE_STORAGE_CONNECTION_STRING="$(azure storage account connectionstring show computeteststore --resource-group testrg --json | jq -r '.string')"
```

Это позволит с легкостью просматривать сведения о хранилище.

```bash
azure storage container list
```

Выходные данные:

```bash
info:    Executing command storage container list
+ Getting storage containers
data:    Name  Public-Access  Last-Modified
data:    ----  -------------  -----------------------------
data:    vhds  Off            Sun, 27 Sep 2015 19:03:54 GMT
info:    storage container list command OK
```

## Создание виртуальной сети и подсети

Далее вам понадобится создать виртуальную сеть в Azure и подсеть, в которой можно установить виртуальные машины.

```bash
azure network vnet create -g TestRG -n TestVNet -a 192.168.0.0/16 -l westeurope
```

Выходные данные:

```bash
info:    Executing command network vnet create
+ Looking up virtual network "TestVNet"
+ Creating virtual network "TestVNet"
+ Loading virtual network state
data:    Id                              : /subscriptions/<guid>/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet
data:    Name                            : TestVNet
data:    Type                            : Microsoft.Network/virtualNetworks
data:    Location                        : westeurope
data:    ProvisioningState               : Succeeded
data:    Address prefixes:
data:      192.168.0.0/16
info:    network vnet create command OK
```

Опять же, давайте посмотрим, как создаются ресурсы с помощью параметра --json команды `azure group show` и инструмента **jq**. Теперь у нас есть ресурс `storageAccounts` и ресурс `virtualNetworks`.

```bash
azure group show TestRG --json | jq '.'
```

Выходные данные:

```bash
{
  "tags": {},
  "id": "/subscriptions/<guid>/resourceGroups/TestRG",
  "name": "TestRG",
  "provisioningState": "Succeeded",
  "location": "westeurope",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "resources": [
    {
      "id": "/subscriptions/<guid>/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet",
      "name": "TestVNet",
      "type": "virtualNetworks",
      "location": "westeurope",
      "tags": null
    },
    {
      "id": "/subscriptions/<guid>/resourceGroups/TestRG/providers/Microsoft.Storage/storageAccounts/computeteststore",
      "name": "computeteststore",
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

Теперь давайте создадим подсеть в виртуальной сети `TestVnet`, в которой будут развернуты виртуальные машины. Мы будем использовать команду `azure network vnet subnet create` с созданными ресурсами: группой ресурсов `TestRG` и виртуальной сетью `TestVNet`. Мы добавим имя подсети `FrontEnd` и префикс ее адреса `192.168.1.0/24`, как показано ниже.

```bash
azure network vnet subnet create -g TestRG -e TestVNet -n FrontEnd -a 192.168.1.0/24
```

Выходные данные:

```bash
info:    Executing command network vnet subnet create
+ Looking up the subnet "FrontEnd"
+ Creating subnet "FrontEnd"
+ Looking up the subnet "FrontEnd"
data:    Id                              : /subscriptions/<guid>/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd
data:    Type                            : Microsoft.Network/virtualNetworks/subnets
data:    ProvisioningState               : Succeeded
data:    Name                            : FrontEnd
data:    Address prefix                  : 192.168.1.0/24
data:
info:    network vnet subnet create command OK
```

Так как логически подсеть находится внутри виртуальной сети, информацию о подсети мы поищем с помощью немного другой команды. Мы воспользуемся командой `azure network vnet show`, но проверять выходные данные JSON будем по-прежнему с помощью **jq**.

```bash
azure network vnet show TestRG TestVNet --json | jq '.'
```

Выходные данные:

```bash
{
  "subnets": [
    {
      "ipConfigurations": [],
      "addressPrefix": "192.168.1.0/24",
      "provisioningState": "Succeeded",
      "name": "FrontEnd",
      "etag": "W/"974f3e2c-028e-4b35-832b-a4b16ad25eb6"",
      "id": "/subscriptions/<guid>/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd"
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
  "etag": "W/"974f3e2c-028e-4b35-832b-a4b16ad25eb6"",
  "id": "/subscriptions/<guid>/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet",
  "name": "TestVNet",
  "location": "westeurope"
}
```

## Создание общедоступного IP-адреса (PIP)

Теперь давайте создадим общедоступный IP-адрес (PIP), который будет назначен балансировщику нагрузки. Он позволит подключаться к виртуальным машинам из Интернета с помощью команды `azure network public-ip create`. Так как по умолчанию используется динамический адрес, мы создаем именованную запись DNS в домене **cloudapp.azure.com** при помощи параметра `-d testsubdomain`.

```bash
azure network public-ip create -d testsubdomain TestRG TestPIP westeurope
```

Выходные данные:

```bash
info:    Executing command network public-ip create
+ Looking up the public ip "TestPIP"
+ Creating public ip address "TestPIP"
+ Looking up the public ip "TestPIP"
data:    Id                              : /subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/publicIPAddresses/TestPIP
data:    Name                            : TestPIP
data:    Type                            : Microsoft.Network/publicIPAddresses
data:    Location                        : westeurope
data:    Provisioning state              : Succeeded
data:    Allocation method               : Dynamic
data:    Idle timeout                    : 4
data:    Domain name label               : testsubdomain
data:    FQDN                            : testsubdomain.westeurope.cloudapp.azure.com
info:    network public-ip create command OK
```

Кроме того, это ресурс верхнего уровня, поэтому его можно отобразить с помощью команды `azure group show`.

```bash
azure group show TestRG --json | jq '.'
```

Выходные данные:

```bash
{
"tags": {},
"id": "/subscriptions/guid/resourceGroups/TestRG",
"name": "TestRG",
"provisioningState": "Succeeded",
"location": "westeurope",
"properties": {
    "provisioningState": "Succeeded"
},
"resources": [
    {
    "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNIC",
    "name": "TestNIC",
    "type": "networkInterfaces",
    "location": "westeurope",
    "tags": null
    },
    {
    "id": "/subscriptions/guid/resourceGroups/testrg/providers/Microsoft.Network/publicIPAddresses/testpip",
    "name": "testpip",
    "type": "publicIPAddresses",
    "location": "westeurope",
    "tags": null
    },
    {
    "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet",
    "name": "TestVNet",
    "type": "virtualNetworks",
    "location": "westeurope",
    "tags": null
    },
    {
    "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Storage/storageAccounts/computeteststore",
    "name": "computeteststore",
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

Вы можете изучить дополнительные сведения о ресурсе, в том числе полное доменное имя (FQDN) поддомена, используя более полную команду `azure network public-ip show`. Обратите внимание, что ресурс общедоступного IP-адреса выделен логически, но при этом конкретный адрес еще не назначен. Для этого вам потребуется балансировщик нагрузки, но он еще не создан.

```bash
azure network public-ip show TestRG TestPIP --json | jq '.'
```

Выходные данные:

```bash
{
"tags": {},
"publicIpAllocationMethod": "Dynamic",
"dnsSettings": {
    "domainNameLabel": "testsubdomain",
    "fqdn": "testsubdomain.westeurope.cloudapp.azure.com"
},
"idleTimeoutInMinutes": 4,
"provisioningState": "Succeeded",
"etag": "W/"c63154b3-1130-49b9-a887-877d74d5ebc5"",
"id": "/subscriptions/guid/resourceGroups/testrg/providers/Microsoft.Network/publicIPAddresses/testpip",
"name": "testpip",
"location": "westeurope"
}
```

## Создание балансировщика нагрузки и пулов IP-адресов
Создание балансировщика нагрузки дает возможность распределять трафик между несколькими виртуальными машинами. Например, это можно использовать при выполнении веб-приложений. Это также обеспечивает избыточность для приложения, так как работает несколько виртуальных машин, которые отвечают на запросы пользователей в случае проведения обслуживания или высокой нагрузки.

Мы создадим балансировщик нагрузки следующим образом.

```bash
azure network lb create -g TestRG -n TestLB -l westeurope
```

```bash
azure network lb create -g TestRG -n TestLB -l westeurope
```

Выходные данные:

```bash
info:    Executing command network lb create
+ Looking up the load balancer "TestLB"
+ Creating load balancer "TestLB"
data:    Id                              : /subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB
data:    Name                            : TestLB
data:    Type                            : Microsoft.Network/loadBalancers
data:    Location                        : westeurope
data:    Provisioning state              : Succeeded
info:    network lb create command OK
```
Наш балансировщик нагрузки выглядит пустым, поэтому давайте создадим несколько пулов IP-адресов. Необходимо создать два пула IP-адресов для балансировщика нагрузки — один интерфейсный, а второй внутренний. Интерфейсный пул IP-адресов будет публично видимым. Это также расположение, которому мы присвоим общедоступный IP-адрес, который был создан ранее. Затем мы будем использовать внутренний пул как расположение для подключения наших виртуальных машин. Таким образом трафик сможет проходить через балансировщик нагрузки к виртуальным машинам.

Сначала давайте создадим интерфейсный пул IP-адресов.

```bash
azure network lb frontend-ip create -g TestRG -l TestLB -n TestFrontEndPool -i TestLBPIP
```

Выходные данные:

```bash
info:    Executing command network lb frontend-ip create
+ Looking up the load balancer "TestLB"
+ Looking up the public ip "TestLBPIP"
+ Updating load balancer "TestLB"
data:    Name                            : TestFrontEndPool
data:    Provisioning state              : Succeeded
data:    Private IP allocation method    : Dynamic
data:    Public IP address id            : /subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/publicIPAddresses/TestLBPIP
info:    network lb frontend-ip create command OK
```

Обратите внимание на то, как мы использовали параметр `--public-ip-name` для передачи созданного ранее TestLBPIP. Так мы присваиваем общедоступный IP-адрес балансировщику нагрузки, чтобы иметь доступ к нашим виртуальным машинам через Интернет.

Теперь давайте создадим второй пул IP-адресов, на этот раз для внутреннего трафика.

```bash
azure network lb address-pool create -g TestRG -l TestLB -n TestBackEndPool
```

Выходные данные:

```bash
info:    Executing command network lb address-pool create
+ Looking up the load balancer "TestLB"
+ Updating load balancer "TestLB"
data:    Name                            : TestBackEndPool
data:    Provisioning state              : Succeeded
info:    network lb address-pool create command OK
```

Можно узнать, как выглядит балансировщик нагрузки, введя команду `azure network lb show`, и проверить выходные данные JSON.

```bash
azure network lb show TestRG TestLB --json | jq '.'
```

Выходные данные:

```bash
{
  "etag": "W/"29c38649-77d6-43ff-ab8f-977536b0047c"",
  "provisioningState": "Succeeded",
  "resourceGuid": "f1446acb-09ba-44d9-b8b6-849d9983dc09",
  "outboundNatRules": [],
  "inboundNatPools": [],
  "inboundNatRules": [],
  "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB",
  "name": "TestLB",
  "type": "Microsoft.Network/loadBalancers",
  "location": "westeurope",
  "frontendIPConfigurations": [
    {
      "etag": "W/"29c38649-77d6-43ff-ab8f-977536b0047c"",
      "name": "TestFrontEndPool",
      "provisioningState": "Succeeded",
      "publicIPAddress": {
        "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/publicIPAddresses/TestLBPIP"
      },
      "privateIPAllocationMethod": "Dynamic",
      "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/frontendIPConfigurations/TestFrontEndPool"
    }
  ],
  "backendAddressPools": [
    {
      "etag": "W/"29c38649-77d6-43ff-ab8f-977536b0047c"",
      "name": "TestBackEndPool",
      "provisioningState": "Succeeded",
      "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/backendAddressPools/TestBackEndPool"
    }
  ],
  "loadBalancingRules": [],
  "probes": []
}
```

## Создание правил NAT балансировщика нагрузки
Чтобы трафик проходил через наш балансировщик нагрузки, необходимо создать правила NAT, которые задают действия для входящего или исходящего трафика. Можно указать используемый протокол, а затем сопоставить внешние порты с внутренними, если это необходимо. Давайте создадим в нашей среде несколько правил, которые пропускают трафик SSH через балансировщик нагрузки к виртуальным машинам. Мы настроим перенаправление TCP-портов 4222 и 4223 на TCP-порт 22 на наших виртуальных машинах (которые мы создадим позже).

```bash
azure network lb inbound-nat-rule create -g TestRG -l TestLB -n VM1-SSH -p tcp -f 4222 -b 22
```

Выходные данные:

```bash
info:    Executing command network lb inbound-nat-rule create
+ Looking up the load balancer "TestLB"
warn:    Using default enable floating ip: false
warn:    Using default idle timeout: 4
warn:    Using default frontend IP configuration "TestFrontEndPool"
+ Updating load balancer "TestLB"
data:    Name                            : VM1-SSH
data:    Provisioning state              : Succeeded
data:    Protocol                        : Tcp
data:    Frontend port                   : 4222
data:    Backend port                    : 22
data:    Enable floating IP              : false
data:    Idle timeout in minutes         : 4
data:    Frontend IP configuration id    : /subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/frontendIPConfigurations/TestFrontEndPool
info:    network lb inbound-nat-rule create command OK
```

Повторите эту процедуру для второго правила NAT для трафика SSH.

```bash
azure network lb inbound-nat-rule create -g TestRG -l TestLB -n VM2-SSH -p tcp -f 4223 -b 22
```

Давайте продолжим и создадим правило NAT для TCP-порта 80, которое привязывает правило к нашим пулам IP-адресов. Если сделать так, а не привязывать правило к виртуальным машинам по отдельности, то мы сможем просто добавлять виртуальные машины в пул IP-адресов или удалять их из него. После этого балансировщик нагрузки будет автоматически регулировать поток трафика.

```bash
azure network lb rule create -g TestRG -l TestLB -n WebRule -p tcp -f 80 -b 80 \
     -t TestFrontEndPool -o TestBackEndPool
```

Выходные данные:

```bash
info:    Executing command network lb rule create
+ Looking up the load balancer "TestLB"
warn:    Using default idle timeout: 4
warn:    Using default enable floating ip: false
warn:    Using default load distribution: Default
+ Updating load balancer "TestLB"
data:    Name                            : WebRule
data:    Provisioning state              : Succeeded
data:    Protocol                        : Tcp
data:    Frontend port                   : 80
data:    Backend port                    : 80
data:    Enable floating IP              : false
data:    Load distribution               : Default
data:    Idle timeout in minutes         : 4
data:    Frontend IP configuration id    : /subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/frontendIPConfigurations/TestFrontEndPool
data:    Backend address pool id         : /subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/backendAddressPools/TestBackEndPool
info:    network lb rule create command OK
```

## Создание пробы работоспособности балансировщика нагрузки

Проба работоспособности периодически проверяет, соответствующим ли образом работают и отвечают на запросы виртуальные машины, которые расположены за балансировщиком нагрузки. Если это не так, они выводятся из эксплуатации, чтобы пользователи не могли их использовать. Можно определить пользовательские проверки для пробы работоспособности, указав интервалы и значения времени ожидания. Дополнительную информацию о пробах работоспособности см. в статье [Проверки балансировщика нагрузки](../load-balancer/load-balancer-custom-probe-overview.md).

```bash
azure network lb probe create -g TestRG -l TestLB -n HealthProbe -p "http" -f healthprobe.aspx -i 15 -c 4
```

Выходные данные:

```bash
info:    Executing command network lb probe create
warn:    Using default probe port: 80
+ Looking up the load balancer "TestLB"
+ Updating load balancer "TestLB"
data:    Name                            : HealthProbe
data:    Provisioning state              : Succeeded
data:    Protocol                        : Http
data:    Port                            : 80
data:    Interval in seconds             : 15
data:    Number of probes                : 4
info:    network lb probe create command OK
```

Здесь мы указали интервал в 15 секунд для проверок работоспособности, и допускается пропуск до 4 проб (1 минута), прежде чем балансировщик нагрузки начнет считать, что узел перестал функционировать.

## Проверка балансировщика нагрузки
Настройка балансировщика нагрузки завершена. Вот какие действия были выполнены.

1. Сначала вы создали балансировщик нагрузки.
2. Затем вы создали интерфейсный пул IP-адресов и назначили ему общедоступный IP-адрес.
3. Далее вы создали внутренний пула IP-адресов, к которому могут подключаться виртуальные машины.
4. После чего вы создали правила NAT, которые разрешают передачу трафика SSH на виртуальные машины для управления, а также правило, открывающее TCP-порт 80 для нашего веб-приложения.
5. Наконец, вы добавили пробу работоспособности для периодической проверки виртуальных машин. Это гарантирует, что пользователь не попытается обратиться к виртуальной машине, которая больше не работает или не обрабатывает содержимое.

Давайте посмотрим, как теперь выглядит балансировщик нагрузки.

```bash
azure network lb show -g TestRG -n TestLB --json | jq '.'
```

Выходные данные:

```bash
{
  "etag": "W/"62a7c8e7-859c-48d3-8e76-5e078c5e4a02"",
  "provisioningState": "Succeeded",
  "resourceGuid": "f1446acb-09ba-44d9-b8b6-849d9983dc09",
  "outboundNatRules": [],
  "inboundNatPools": [],
  "inboundNatRules": [
    {
      "etag": "W/"62a7c8e7-859c-48d3-8e76-5e078c5e4a02"",
      "name": "VM1-SSH",
      "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/inboundNatRules/VM1-SSH",
      "frontendIPConfiguration": {
        "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/frontendIPConfigurations/TestFrontEndPool"
      },
      "protocol": "Tcp",
      "frontendPort": 4222,
      "backendPort": 22,
      "idleTimeoutInMinutes": 4,
      "enableFloatingIP": false,
      "provisioningState": "Succeeded"
    },
    {
      "etag": "W/"62a7c8e7-859c-48d3-8e76-5e078c5e4a02"",
      "name": "VM2-SSH",
      "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/inboundNatRules/VM2-SSH",
      "frontendIPConfiguration": {
        "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/frontendIPConfigurations/TestFrontEndPool"
      },
      "protocol": "Tcp",
      "frontendPort": 4223,
      "backendPort": 22,
      "idleTimeoutInMinutes": 4,
      "enableFloatingIP": false,
      "provisioningState": "Succeeded"
    }
  ],
  "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB",
  "name": "TestLB",
  "type": "Microsoft.Network/loadBalancers",
  "location": "westeurope",
  "frontendIPConfigurations": [
    {
      "etag": "W/"62a7c8e7-859c-48d3-8e76-5e078c5e4a02"",
      "name": "TestFrontEndPool",
      "provisioningState": "Succeeded",
      "publicIPAddress": {
        "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/publicIPAddresses/TestLBPIP"
      },
      "privateIPAllocationMethod": "Dynamic",
      "loadBalancingRules": [
        {
          "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/loadBalancingRules/WebRule"
        }
      ],
      "inboundNatRules": [
        {
          "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/inboundNatRules/VM1-SSH"
        },
        {
          "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/inboundNatRules/VM2-SSH"
        }
      ],
      "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/frontendIPConfigurations/TestFrontEndPool"
    }
  ],
  "backendAddressPools": [
    {
      "etag": "W/"62a7c8e7-859c-48d3-8e76-5e078c5e4a02"",
      "name": "TestBackEndPool",
      "provisioningState": "Succeeded",
      "loadBalancingRules": [
        {
          "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/loadBalancingRules/WebRule"
        }
      ],
      "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/backendAddressPools/TestBackEndPool"
    }
  ],
  "loadBalancingRules": [
    {
      "etag": "W/"62a7c8e7-859c-48d3-8e76-5e078c5e4a02"",
      "name": "WebRule",
      "provisioningState": "Succeeded",
      "enableFloatingIP": false,
      "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/loadBalancingRules/WebRule",
      "frontendIPConfiguration": {
        "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/frontendIPConfigurations/TestFrontEndPool"
      },
      "backendAddressPool": {
        "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/backendAddressPools/TestBackEndPool"
      },
      "protocol": "Tcp",
      "loadDistribution": "Default",
      "frontendPort": 80,
      "backendPort": 80,
      "idleTimeoutInMinutes": 4
    }
  ],
  "probes": [
    {
      "etag": "W/"62a7c8e7-859c-48d3-8e76-5e078c5e4a02"",
      "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/probes/HealthProbe",
      "protocol": "Http",
      "port": 80,
      "intervalInSeconds": 15,
      "numberOfProbes": 4,
      "requestPath": "healthprobe.aspx",
      "provisioningState": "Succeeded",
      "name": "HealthProbe"
    }
  ]
}
```

## Создание сетевой карты для виртуальной машины Linux

 Работой сетевых карт можно управлять программно, так как к ним можно применить правила. Кроме того, можно использовать несколько сетевых карт. Обратите внимание, что в приведенной ниже команде `azure network nic create` вы привязываете сетевую карту к внутреннему пулу IP-адресов загрузки и связанному правилу NAT, которое разрешает трафик SSH. Для этого вместо `<GUID>` необходимо указать идентификатор подписки Azure.

```bash
azure network nic create -g TestRG -n LB-NIC1 -l westeurope --subnet-vnet-name TestVNet --subnet-name FrontEnd \
     -d /subscriptions/<GUID>/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/backendAddressPools/TestBackEndPool \
     -e /subscriptions/<GUID>/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/inboundNatRules/VM1-SSH
```

Выходные данные:

```bash
info:    Executing command network nic create
+ Looking up the subnet "FrontEnd"
+ Looking up the network interface "LB-NIC1"
+ Creating network interface "LB-NIC1"
data:    Id                              : /subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/LB-NIC1
data:    Name                            : LB-NIC1
data:    Type                            : Microsoft.Network/networkInterfaces
data:    Location                        : westeurope
data:    Provisioning state              : Succeeded
data:    Enable IP forwarding            : false
data:    IP configurations:
data:      Name                          : Nic-IP-config
data:      Provisioning state            : Succeeded
data:      Private IP address            : 192.168.1.4
data:      Private IP allocation method  : Dynamic
data:      Subnet                        : /subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd
data:      Load balancer backend address pools:
data:        Id                          : /subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/backendAddressPools/TestBackEndPool
data:      Load balancer inbound NAT rules:
data:        Id                          : /subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/inboundNatRules/VM1-SSH
data:
info:    network nic create command OK
```

Чтобы отобразить сведения, необходимо просмотреть ресурс напрямую. Для этого воспользуйтесь командой `azure network nic show`.

```bash
azure network nic show TestRG LB-NIC1 --json | jq '.'
```

Выходные данные:

```bash
{
  "etag": "W/"fc1eaaa1-ee55-45bd-b847-5a08c7f4264a"",
  "provisioningState": "Succeeded",
  "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/LB-NIC1",
  "name": "LB-NIC1",
  "type": "Microsoft.Network/networkInterfaces",
  "location": "westeurope",
  "ipConfigurations": [
    {
      "etag": "W/"fc1eaaa1-ee55-45bd-b847-5a08c7f4264a"",
      "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/LB-NIC1/ipConfigurations/Nic-IP-config",
      "loadBalancerBackendAddressPools": [
        {
          "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/backendAddressPools/TestBackEndPool"
        }
      ],
      "loadBalancerInboundNatRules": [
        {
          "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/inboundNatRules/VM1-SSH"
        }
      ],
      "privateIPAddress": "192.168.1.4",
      "privateIPAllocationMethod": "Dynamic",
      "subnet": {
        "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd"
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

Теперь мы создадим вторую сетевую карту и тоже привяжем ее к внутреннему пулу IP-адресов. В этот раз второе правило NAT разрешает трафик SSH.

```bash
azure network nic create -g TestRG -n LB-NIC2 -l westeurope --subnet-vnet-name TestVNet --subnet-name FrontEnd \
    -d  /subscriptions/<GUID>/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/backendAddressPools/TestBackEndPool \
    -e /subscriptions/<GUID>/resourceGroups/TestRG/providers/Microsoft.Network/loadBalancers/TestLB/inboundNatRules/VM2-SSH
```

## Создание группы безопасности сети и правил

Теперь создадим группу безопасности сети и правила входящего трафика, управляющие доступом к сетевой карте.

```bash
azure network nsg create TestRG TestNSG westeurope
```

Давайте добавим для этой группы безопасности сети правило для входящего трафика, чтобы разрешить входящие подключения через порт 22 (для поддержки SSH).

```bash
azure network nsg rule create --protocol tcp --direction inbound --priority 1000 \
    --destination-port-range 22 --access allow TestRG TestNSG SSHRule
```

```bash
azure network nsg rule create --protocol tcp --direction inbound --priority 1001 \
    --destination-port-range 80 --access allow -g TestRG -a TestNSG -n HTTPRule
```

> [AZURE.NOTE] Правило для входящего трафика является фильтром входящих сетевых подключений. В этом примере мы привяжем группу безопасности сети к виртуальной сетевой карте виртуальной машины. Это значит, что любой запрос к порту 22 будет передаваться в сетевую карту виртуальной машины. Это правило для сетевого подключения, а не конечной точки, как это было бы в классическом развертывании. Это означает, что для открытия порта необходимо оставить для `--source-port-range` значение "*" (значение по умолчанию), чтобы разрешить прием входящих запросов от **любого** запрашивающего порта. Обычно порты являются динамическими.

## Привязка к сетевой карте

Привяжите группу безопасности сети к сетевым картам.

```bash
azure network nic set -g TestRG -n LB-NIC1 -o TestNSG
```

```bash
azure network nic set -g TestRG -n LB-NIC2 -o TestNSG
```

## "Создать группу доступности"
Группы доступности помогают распределить виртуальные машины между доменами сбоя и доменами обновления. Создадим группу доступности для виртуальных машин.

```bash
azure availset create -g TestRG -n TestAvailSet -l westeurope
```

Домены сбоя определяют группу виртуальных машин, совместно использующих общий источник питания и сетевой коммутатор. По умолчанию виртуальные машины, настроенные в группе доступности, разделяются между несколькими доменами сбоя, которых может быть не больше трех. Суть в том, что сбой оборудования в одном из этих доменов сбоя не повлияет на все виртуальные машины, на которых выполняется ваше приложение. Azure автоматически распределяет виртуальные машины между доменами сбоя при помещении их в группу доступности.

Домены обновления — это группы виртуальных машин и базовое физическое оборудование, которое может быть перезагружено одновременно. Перезагрузка доменов обновления при запланированном обслуживании может выполняться не по порядку, но одновременно будет перезагружаться только один домен обновления. Опять же, Azure автоматически распределяет виртуальные машины между доменами обновления при помещении их в группу доступности.

Узнайте больше об [управлении доступностью виртуальных машин](./virtual-machines-linux-manage-availability.md).

## Создание виртуальной машины Linux

Вы создали ресурсы сети и хранилища для доступных через Интернет виртуальных машин. Теперь давайте создадим эти виртуальные машины и защитим их ключом SSH без пароля. В этом случае мы создадим виртуальную машину Ubuntu на базе последней версии LTS. Информацию об этом образе мы найдем с помощью команды `azure vm image list`, как описано в статье о [поиске образов виртуальных машин Azure](virtual-machines-linux-cli-ps-findimage.md).

Мы выбрали образ с помощью команды `azure vm image list westeurope canonical | grep LTS`. В этом случае мы будем использовать `canonical:UbuntuServer:14.04.4-LTS:14.04.201604060`. Для последнего поля мы передадим `latest`, чтобы в будущем всегда получать последнюю сборку. (Используемая строка будет следующей: `canonical:UbuntuServer:14.04.4-LTS:14.04.201604060`).

Следующий этап знаком каждому, кто уже хоть раз создал пару ключей SSH RSA (открытый и закрытый) в Linux или Mac с помощью команды **ssh-keygen -t rsa -b 2048**. Если в вашем каталоге `~/.ssh` нет пар ключей сертификата, то их можно создать:

- автоматически, с помощью параметра `azure vm create --generate-ssh-keys`;
- вручную, следуя [инструкциям по самостоятельному созданию](virtual-machines-linux-ssh-from-linux.md).

Кроме того, можно использовать метод --admin-password для аутентификации подключений по протоколу SSH после создания виртуальной машины. Обычно этот метод менее безопасен.

Мы создаем виртуальную машину, собирая вместе все наши ресурсы и информацию командой `azure vm create`.

```bash
azure vm create \            
    --resource-group TestRG \
    --name TestVM1 \
    --location westeurope \
    --os-type linux \
    --availset-name TestAvailSet \
    --nic-name LB-NIC1 \
    --vnet-name TestVnet \
    --vnet-subnet-name FrontEnd \
    --storage-account-name computeteststore \
    --image-urn canonical:UbuntuServer:14.04.4-LTS:latest \
    --ssh-publickey-file ~/.ssh/id_rsa.pub \
    --admin-username ops
```

Выходные данные:

```bash
info:    Executing command vm create
+ Looking up the VM "TestVM1"
info:    Verifying the public key SSH file: /home/ifoulds/.ssh/id_rsa.pub
info:    Using the VM Size "Standard_A1"
info:    The [OS, Data] Disk or image configuration requires storage account
+ Looking up the storage account computeteststore
+ Looking up the availability set "TestAvailSet"
info:    Found an Availability set "TestAvailSet"
+ Looking up the NIC "LB-NIC1"
info:    Found an existing NIC "LB-NIC1"
info:    Found an IP configuration with virtual network subnet id "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd" in the NIC "LB-NIC1"
info:    This is an NIC without publicIP configured
info:    The storage URI 'https://computeteststore.blob.core.windows.net/' will be used for boot diagnostics settings, and it can be overwritten by the parameter input of '--boot-diagnostics-storage-uri'.
info:    vm create command OK
```

К созданной виртуальной машине можно сразу же подключиться с помощью используемых по умолчанию ключей SSH. Обязательно укажите соответствующий порт, так как подключение проходит через балансировщик нагрузки. (Для первой виртуальной машины мы устанавливаем правило NAT для перенаправления порта 4222 к ней.)

```bash
 ssh ops@testlb.westeurope.cloudapp.azure.com -p 4222
```

Выходные данные:

```bash
The authenticity of host '[testlb.westeurope.cloudapp.azure.com]:4222 ([xx.xx.xx.xx]:4222)' can't be established.
ECDSA key fingerprint is 94:2d:d0:ce:6b:fb:7f:ad:5b:3c:78:93:75:82:12:f9.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added '[testlb.westeurope.cloudapp.azure.com]:4222,[xx.xx.xx.xx]:4222' (ECDSA) to the list of known hosts.
Welcome to Ubuntu 14.04.4 LTS (GNU/Linux 3.19.0-58-generic x86_64)

 * Documentation:  https://help.ubuntu.com/

  System information as of Wed Apr 27 23:44:06 UTC 2016

  System load: 0.37              Memory usage: 5%   Processes:       81
  Usage of /:  37.3% of 1.94GB   Swap usage:   0%   Users logged in: 0

  Graph this data and manage this system at:
    https://landscape.canonical.com/

  Get cloud support with Ubuntu Advantage Cloud Guest:
    http://www.ubuntu.com/business/services/cloud

0 packages can be updated.
0 updates are security updates.



The programs included with the Ubuntu system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Ubuntu comes with ABSOLUTELY NO WARRANTY, to the extent permitted by
applicable law.

ops@TestVM1:~$
```

Точно так же создадим вторую виртуальную машину.

```bash
azure vm create \            
    --resource-group TestRG \
    --name TestVM2 \
    --location westeurope \
    --os-type linux \
    --availset-name TestAvailSet \
    --nic-name LB-NIC2 \
    --vnet-name TestVnet \
    --vnet-subnet-name FrontEnd \
    --storage-account-name computeteststore \
    --image-urn canonical:UbuntuServer:14.04.4-LTS:latest \
    --ssh-publickey-file ~/.ssh/id_rsa.pub \
    --admin-username ops
```

Теперь, чтобы проверить то, что вы создали, можно использовать команду `azure vm show testrg testvm`. На данном этапе имеются работающие виртуальные машины Ubuntu, расположенные за балансировщиком нагрузки в Azure, входить на которые можно только с помощью пары ключей SSH (так как пароли отключены). Можно установить nginx или httpd, развернуть веб-приложение и посмотреть, как трафик проходит через балансировщик нагрузки на обе виртуальные машины.

```bash
azure vm show TestRG TestVM1
```

Выходные данные:

```bash
info:    Executing command vm show
+ Looking up the VM "TestVM1"
+ Looking up the NIC "LB-NIC1"
data:    Id                              :/subscriptions/8fa5cd83-7fbb-431a-af16-4a20dede8802/resourceGroups/testrg/providers/Microsoft.Compute/virtualMachines/TestVM1
data:    ProvisioningState               :Succeeded
data:    Name                            :TestVM1
data:    Location                        :westeurope
data:    Type                            :Microsoft.Compute/virtualMachines
data:
data:    Hardware Profile:
data:      Size                          :Standard_A1
data:
data:    Storage Profile:
data:      Image reference:
data:        Publisher                   :canonical
data:        Offer                       :UbuntuServer
data:        Sku                         :14.04.4-LTS
data:        Version                     :latest
data:
data:      OS Disk:
data:        OSType                      :Linux
data:        Name                        :cli1cca1d20a1dcf56c-os-1461800591317
data:        Caching                     :ReadWrite
data:        CreateOption                :FromImage
data:        Vhd:
data:          Uri                       :https://computeteststore.blob.core.windows.net/vhds/cli1cca1d20a1dcf56c-os-1461800591317.vhd
data:
data:    OS Profile:
data:      Computer Name                 :TestVM1
data:      User Name                     :ops
data:      Linux Configuration:
data:        Disable Password Auth       :true
data:
data:    Network Profile:
data:      Network Interfaces:
data:        Network Interface #1:
data:          Primary                   :true
data:          MAC Address               :00-0D-3A-20-F8-8B
data:          Provisioning State        :Succeeded
data:          Name                      :LB-NIC1
data:          Location                  :westeurope
data:
data:    AvailabilitySet:
data:      Id                            :/subscriptions/guid/resourceGroups/testrg/providers/Microsoft.Compute/availabilitySets/TESTAVAILSET
data:
data:    Diagnostics Profile:
data:      BootDiagnostics Enabled       :true
data:      BootDiagnostics StorageUri    :https://computeteststore.blob.core.windows.net/
data:
data:      Diagnostics Instance View:
info:    vm show command OK
```


## Экспорт среды как шаблона
Теперь, когда вы создали эту среду, предположим, что вам требуется создать дополнительную среду разработки с теми же параметрами или соответствующую рабочую среду. Resource Manager использует шаблоны JSON, которые определяют все параметры среды. Это означает, что можно полностью создавать среды, ссылаясь на этот шаблон JSON. Вы можете [создавать шаблоны JSON вручную](../resource-group-authoring-templates.md) или просто экспортировать существующую среду для создания шаблона JSON.

```bash
azure group export TestRG
```

При этом создается файл `TestRG.json` в текущем рабочем каталоге. При создании новой среды на основе этого шаблона будут запрошены все имена ресурсов, включая балансировщик нагрузки, сетевые интерфейсы, виртуальные машины и т. д. Их можно указать в файле шаблона, добавив `-p` или `--includeParameterDefaultValue` в команду `azure group export`, которая была показана ранее. Измените шаблон JSON, чтобы указать имена ресурсов, или [создайте файл parameters.json](../resource-group-authoring-templates.md#parameters), задающий их.

Чтобы создать новую среду из шаблона, выполните следующие действия:

```bash
azure group deployment create -f TestRG.json -g NewRGFromTemplate
```

Вы можете прочитать [дополнительные сведения о развертывании из шаблонов](../resource-group-template-deploy-cli.md). Узнайте, как поэтапно обновлять среды, использовать файл параметров и обращаться к шаблонам из единого места хранения.

## Дальнейшие действия

Теперь вы готовы приступить к работе с несколькими сетевыми компонентами и виртуальными машинами. Используя этот пример среды, можно создать приложение на основе представленных здесь основных компонентов.

<!---HONumber=AcomDC_0727_2016-->
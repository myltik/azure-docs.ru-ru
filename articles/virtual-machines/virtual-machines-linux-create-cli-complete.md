<properties
   pageTitle="Создание виртуальной машины Linux с нуля с помощью Azure CLI | Microsoft Azure"
   description="Из этой статьи вы узнаете, как с помощью Azure CLI создать с нуля виртуальную машину Linux, хранилище, виртуальную сеть и подсеть, сетевой адаптер, общедоступный IP-адрес и группу безопасности сети."
   services="virtual-machines-linux"
   documentationCenter="virtual-machines"
   authors="vlivech"
   manager="squillace"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure"
   ms.date="04/04/2016"
   ms.author="v-livech"/>

# Создание виртуальной машины Linux с нуля с помощью Azure CLI

Для создания виртуальной машины Linux вам потребуется [Azure CLI](../xplat-cli-install.md) в режиме диспетчера ресурсов \(`azure config mode arm`\) и средство анализа JSON. Для выполнения действий в этом документе мы используем [jq](https://stedolan.github.io/jq/).

## Быстрые команды

```bash
# Create the Resource Group
chrisL@fedora$ azure group create TestRG westeurope

# Create the Storage Account
chrisL@fedora$ azure storage account create \  
--location westeurope \
--resource-group TestRG \
--type GRS \
computeteststore

# Verify the RG using the JSON parser
chrisL@fedora$ azure group show testrg --json | jq '.'

# Create the Virtual Network
chrisL@fedora$ azure network vnet create -g TestRG -n TestVNet -a 192.168.0.0/16 -l westeurope

# Verify the RG
chrisL@fedora$ azure group show testrg --json | jq '.'

# Create the Subnet
chrisL@fedora$ azure network vnet subnet create -g TestRG -e TestVNet -n FrontEnd -a 192.168.1.0/24

# Verify the VNet and Subnet
chrisL@fedora$ azure network vnet show testrg testvnet --json | jq '.'

# Create the NIC
chrisL@fedora$ azure network nic create -g TestRG -n TestNIC -l westeurope -a 192.168.1.101 -m TestVNet -k FrontEnd

# Verify the NIC
chrisL@fedora$ azure network nic show testrg testnic --json | jq '.'

# Create the NSG
chrisL@fedora$ azure network nsg create testrg testnsg westeurope

# Add an inbound rule for the NSG
chrisL@fedora$ azure network nsg rule create --protocol tcp --direction inbound --priority 1000  --destination-port-range 22 --access allow testrg testnsg testnsgrule

# Creat the Public facing NIC
chrisL@fedora$ azure network public-ip create -d testsubdomain testrg testpip westeurope

# Verify the NIC
chrisL@fedora$ azure network public-ip show testrg testpip --json | jq '.'

# Associate the Public IP to the NIC
chrisL@fedora$ azure network nic set --public-ip-name testpip testrg testnic

# Bind the NSG to the NIC
chrisL@fedora$ azure network nic set --network-security-group-name testnsg testrg testnic

# Create the Linux VM
chrisL@fedora$ azure vm create \            
    --resource-group testrg \
    --name testvm \
    --location westeurope \
    --os-type linux \
    --nic-name testnic \
    --vnet-name testvnet \
    --vnet-subnet-name FrontEnd \
    --storage-account-name computeteststore \
    --image-urn canonical:UbuntuServer:14.04.3-LTS:latest \
    --ssh-publickey-file ~/.ssh/id_rsa.pub \
    --admin-username ops

# Verify everything built
chrisL@fedora$ azure vm show testrg testvm

```

## Подробное пошаговое руководство

### Введение

В этой статье мы создаем развертывание с одной виртуальной машиной Linux в подсети виртуальной сети. Здесь полностью рассмотрен процесс базового развертывания, от первой до последней команды, в результате которого вы создадите защищенную работающую виртуальную машину Linux, к которой можно подключиться через Интернет.

Попутно вы ознакомитесь с иерархией зависимостей модели развертывания Resource Manager и узнаете, какие возможности она дает. Узнав, как создается система, вы сможете затем воссоздать ее гораздо быстрее с помощью прямых команд Azure CLI \(см. [здесь](virtual-machines-linux-quick-create-cli.md) информацию о примерно таком же развертывании, которое выполняется с помощью команды `azure vm quick-create`\). Или же вы можете научиться проектировать и автоматизировать развертывания сетей и приложений и обновлять их с помощью [шаблонов Azure Resource Manager](../resource-group-authoring-templates.md). Когда вы узнаете, как компонуются части развертывания, создавать шаблоны для их автоматизации станет проще.

Давайте создадим простую сеть с виртуальной машиной, с помощью которой можно выполнять разработку и простые вычисления. Мы все объясним по ходу дела. Освоив этот пример, вы сможете работать с более сложными сетями и развертываниями.

### Создание группы ресурсов и выбор расположений развертывания

Группы ресурсов Azure — это логические сущности развертывания, содержащие конфигурацию и другие метаданные, которые делают возможным логическое управление развертываниями ресурсов.

```
chrisL@fedora$ azure group create TestRG westeurope                        
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

### Создание учетной записи хранения

Вам понадобятся учетные записи хранения для дисков виртуальной машины и других дисков данных, которые вы захотите добавить \(а также для других случаев\). Короче говоря, вы всегда будете создавать учетные записи хранения практически сразу после создания групп ресурсов.

В этом случае мы используем команду `azure storage account create` и с ее помощью передаем расположение учетной записи, имя группы ресурсов, которая будет ее контролировать, и нужный вам тип поддержки хранилищ.

```
chrisL@fedora$ azure storage account create \  
--location westeurope \
--resource-group TestRG \
--type GRS \
computeteststore
info:    Executing command storage account create
+ Creating storage account
info:    storage account create command OK
rasquill•~/workspace/keygen» azure group show testrg
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

Чтобы проверить нашу группу ресурсов с помощью команды `azure group show`, мы воспользуемся средством [jq](https://stedolan.github.io/jq/) \(для анализа JSON можно использовать **jsawk** или любую другую языковую библиотеку\), а также параметром `--json` Azure CLI.

```
chrisL@fedora$ azure group show testrg --json | jq '.'                                                                                        
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

Чтобы изучить учетную запись хранения с помощью CLI, сначала нужно задать имена и ключи учетной записи. Для этого следует использовать следующую команду, заменив в ней имя учетной записи хранения, указанное в этой статье, на имя, которое вам нужно.

```
AZURE_STORAGE_CONNECTION_STRING="$(azure storage account connectionstring show computeteststore --resource-group testrg --json | jq -r '.string')"
```

Это позволит с легкостью просматривать сведения о хранилище.

```
chrisL@fedora$ azure storage container list
info:    Executing command storage container list
+ Getting storage containers
data:    Name  Public-Access  Last-Modified
data:    ----  -------------  -----------------------------
data:    vhds  Off            Sun, 27 Sep 2015 19:03:54 GMT
info:    storage container list command OK
```
### Создание виртуальной сети и подсети

Вам понадобится создать виртуальную сеть и подсеть Azure, в которой можно установить виртуальную машину.

```
chrisL@fedora$ azure network vnet create -g TestRG -n TestVNet -a 192.168.0.0/16 -l westeurope
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

Опять же, давайте посмотрим, как создаются ресурсы с помощью параметра --json команды `azure group show` и обработчика **jq**. Теперь у нас есть ресурс `storageAccounts` и ресурс `virtualNetworks`.

```
chrisL@fedora$ azure group show testrg --json | jq '.'
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

Давайте создадим подсеть в виртуальной сети `TestVnet`, в которой будет развернута виртуальная машина. Мы используем команду `azure network vnet subnet create` и ресурсы, которые уже создали: группу ресурсов `TestRG` и виртуальную сеть `TestVNet`. Кроме того, мы добавим имя подсети `FrontEnd` и префикс адреса подсети `192.168.1.0/24`, как показано ниже.

```
chrisL@fedora$ azure network vnet subnet create -g TestRG -e TestVNet -n FrontEnd -a 192.168.1.0/24
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

Так как логически подсеть находится внутри виртуальной сети, информацию о подсети мы поищем с помощью немного отличающейся команды `azure network vnet show`. При этом выходные данные JSON мы по-прежнему будем проверять с помощью **jq**.

```
chrisL@fedora$ azure network vnet show testrg testvnet --json | jq '.'
{
  "subnets": [
    {
      "ipConfigurations": [],
      "addressPrefix": "192.168.1.0/24",
      "provisioningState": "Succeeded",
      "name": "FrontEnd",
      "etag": "W/\"974f3e2c-028e-4b35-832b-a4b16ad25eb6\"",
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
  "etag": "W/\"974f3e2c-028e-4b35-832b-a4b16ad25eb6\"",
  "id": "/subscriptions/<guid>/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet",
  "name": "TestVNet",
  "location": "westeurope"
}
```

### Создание сетевого адаптера для использования с виртуальной машиной Linux

Даже с сетевыми адаптерами можно работать с помощью программных средств, так как вы можете применять правила их использования и иметь более двух адаптеров.

```
chrisL@fedora$ azure network nic create -g TestRG -n TestNIC -l westeurope -a 192.168.1.101 -m TestVNet -k FrontEnd
info:    Executing command network nic create
+ Looking up the network interface "TestNIC"
+ Looking up the subnet "FrontEnd"
+ Creating network interface "TestNIC"
+ Looking up the network interface "TestNIC"
data:    Id                              : /subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNIC
data:    Name                            : TestNIC
data:    Type                            : Microsoft.Network/networkInterfaces
data:    Location                        : westeurope
data:    Provisioning state              : Succeeded
data:    Enable IP forwarding            : false
data:    IP configurations:
data:      Name                          : NIC-config
data:      Provisioning state            : Succeeded
data:      Private IP address            : 192.168.1.101
data:      Private IP Allocation Method  : Static
data:      Subnet                        : /subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd
data:
info:    network nic create command OK
```

Ресурс сетевого адаптера связан с виртуальной машиной и группой безопасности сети, поэтому при просмотре группы ресурсов `TestRG` он отображается как ресурс верхнего уровня.

```
chrisL@fedora$ azure group show testrg --json | jq '.'
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

Чтобы отобразить сведения, нужно просмотреть ресурс напрямую с помощью команды `azure network nic show`.

```
chrisL@fedora$ azure network nic show testrg testnic --json | jq '.'
{
"ipConfigurations": [
    {
    "loadBalancerBackendAddressPools": [],
    "loadBalancerInboundNatRules": [],
    "privateIpAddress": "192.168.1.101",
    "privateIpAllocationMethod": "Static",
    "subnet": {
        "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd"
    },
    "provisioningState": "Succeeded",
    "name": "NIC-config",
    "etag": "W/\"4d29b1ca-0207-458c-b258-f298e6fc450f\"",
    "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNIC/ipConfigurations/NIC-config"
    }
],
"tags": {},
"dnsSettings": {
    "appliedDnsServers": [],
    "dnsServers": []
},
"enableIPForwarding": false,
"provisioningState": "Succeeded",
"etag": "W/\"4d29b1ca-0207-458c-b258-f298e6fc450f\"",
"id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNIC",
"name": "TestNIC",
"location": "westeurope"
}
```

### Создание группы безопасности сети и правил

Теперь создадим группу безопасности сети \(NSG\) и правила для входящего трафика, управляющие доступом к сетевой карте.

```
chrisL@fedora$ azure network nsg create testrg testnsg westeurope
```

Давайте добавим в этой группе NSG правило для входящего трафика, чтобы разрешить входящие подключения через порт 22 \(для поддержки SSH\).

```
chrisL@fedora$ azure network nsg rule create --protocol tcp --direction inbound --priority 1000  --destination-port-range 22 --access allow testrg testnsg testnsgrule
```

> [AZURE.NOTE] Правило для входящего трафика является фильтром входящих сетевых подключений. В этом примере мы привяжем сеть NSG к адаптеру виртуальной сети виртуальной машины. Вследствие этого любой запрос на порт 22 будет передаваться на сетевой адаптер нашей виртуальной машины. Это правило касается сетевого подключения \(а не конечной точки, как в классических развертываниях\), поэтому для открытия порта необходимо оставить значение "\*" \(значение по умолчанию\) параметра `--source-port-range`. Это позволит принимать входящие запросы от **любого** запрашивающего порта \(обычно это динамические порты\).

### Создание общедоступного IP-адреса \(PIP\)

Теперь давайте создадим общедоступный IP-адрес \(PIP\), позволяющий подключиться к виртуальной машине из Интернета с помощью команды `azure network public-ip create`. Так как по умолчанию используется динамический адрес, мы создаем именованную DNS-запись в домене **cloudapp.azure.com** при помощи параметра `-d testsubdomain`.

```
chrisL@fedora$ azure network public-ip create -d testsubdomain testrg testpip westeurope
info:    Executing command network public-ip create
+ Looking up the public ip "testpip"
+ Creating public ip address "testpip"
+ Looking up the public ip "testpip"
data:    Id                              : /subscriptions/guid/resourceGroups/testrg/providers/Microsoft.Network/publicIPAddresses/testpip
data:    Name                            : testpip
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

```
chrisL@fedora$ azure group show testrg --json | jq '.'
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

И, как всегда, вы можете узнать дополнительные сведения о ресурсе, в том числе полное доменное имя \(FQDN\) поддомена, используя более полную команду `azure network public-ip show`. Обратите внимание, что общедоступный IP-адрес выделен логически, но при этом конкретный адрес еще не назначен. Для этого вам потребуется виртуальная машина, но она еще не создана.

```
azure network public-ip show testrg testpip --json | jq '.'
{
"tags": {},
"publicIpAllocationMethod": "Dynamic",
"dnsSettings": {
    "domainNameLabel": "testsubdomain",
    "fqdn": "testsubdomain.westeurope.cloudapp.azure.com"
},
"idleTimeoutInMinutes": 4,
"provisioningState": "Succeeded",
"etag": "W/\"c63154b3-1130-49b9-a887-877d74d5ebc5\"",
"id": "/subscriptions/guid/resourceGroups/testrg/providers/Microsoft.Network/publicIPAddresses/testpip",
"name": "testpip",
"location": "westeurope"
}
```

### Связывание общедоступного IP-адреса и группы безопасности сети с сетевым адаптером

```
chrisL@fedora$ azure network nic set --public-ip-name testpip testrg testnic
```

Привяжите группу безопасности сети к сетевому адаптеру.

```
chrisL@fedora$ azure network nic set --network-security-group-name testnsg testrg testnic
```

### Создание виртуальной машины Linux

Вы создали ресурсы сети и хранилища, чтобы поддержать доступную через Интернет виртуальную машину. А теперь давайте создадим эту виртуальную машину и защитим ее ключом SSH \(без пароля\). В этом случае мы создадим виртуальную машину Ubuntu на базе последней версии LTS. Информацию об этом образе мы найдем с помощью команды `azure vm image list`, как описано в статье, посвященной [поиску образов виртуальных машин Azure](virtual-machines-linux-cli-ps-findimage.md). Мы выбрали образ при помощи команды `azure vm image list westeurope canonical | grep LTS`, и в этом случае применяем значение `canonical:UbuntuServer:14.04.3-LTS:14.04.201509080`, но для последнего поля передадим значение `latest`, чтобы в будущем всегда получать последнюю сборку \(мы используем строку `canonical:UbuntuServer:14.04.3-LTS:latest`\).

> [AZURE.NOTE] Следующий этап знаком каждому, кто уже хоть раз создал пару ключей SSH RSA \(общедоступный и закрытый\) в ОС Linux или Mac с помощью строки **ssh-keygen -t rsa -b 2048**. Если в каталоге `~/.ssh` нет пар ключей сертификата, вы можете их создать: <br /> 1. Автоматически с помощью параметра `azure vm create --generate-ssh-keys`. 2. Вручную, следуя [инструкциям по самостоятельному созданию](virtual-machines-linux-ssh-from-linux.md). <br /> Кроме того, с помощью параметров `azure vm create --admin-username --admin-password` вы можете проверять подлинность подключений SSH, используя имя пользователя и пароль \(это можно делать после создания виртуальной машины\).

Мы создаем виртуальную машину, используя все наши ресурсы и информацию вместе с командой `azure vm create`.

```
chrisL@fedora$ azure vm create \            
--resource-group testrg \
--name testvm \
--location westeurope \
--os-type linux \
--nic-name testnic \
--vnet-name testvnet \
--vnet-subnet-name FrontEnd \
--storage-account-name computeteststore \
--image-urn canonical:UbuntuServer:14.04.3-LTS:latest \
--ssh-publickey-file ~/.ssh/id_rsa.pub \
--admin-username ops
info:    Executing command vm create
+ Looking up the VM "testvm"
info:    Verifying the public key SSH file: /Users/user/.ssh/id_rsa.pub
info:    Using the VM Size "Standard_A1"
info:    The [OS, Data] Disk or image configuration requires storage account
+ Looking up the storage account computeteststore
+ Looking up the NIC "testnic"
info:    Found an existing NIC "testnic"
info:    Found an IP configuration with virtual network subnet id "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd" in the NIC "testnic"
info:    This NIC IP configuration has a public ip already configured "/subscriptions/guid/resourcegroups/testrg/providers/microsoft.network/publicipaddresses/testpip", any public ip parameters if provided, will be ignored.
+ Creating VM "testvm"
info:    vm create command OK
```

К созданной виртуальной машине можно сразу же подключиться с помощью используемых по умолчанию ключей SSH.

```
chrisL@fedora$ ssh ops@testsubdomain.westeurope.cloudapp.azure.com           
The authenticity of host 'testsubdomain.westeurope.cloudapp.azure.com (XX.XXX.XX.XXX)' can't be established.
RSA key fingerprint is b6:a4:7g:4b:cb:cd:76:87:63:2d:84:83:ac:12:2d:cd.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added 'testsubdomain.westeurope.cloudapp.azure.com,XX.XXX.XX.XXX' (RSA) to the list of known hosts.
Welcome to Ubuntu 14.04.3 LTS (GNU/Linux 3.19.0-28-generic x86_64)

* Documentation:  https://help.ubuntu.com/

System information as of Mon Sep 28 18:45:02 UTC 2015

System load: 0.64              Memory usage: 5%   Processes:       81
Usage of /:  45.3% of 1.94GB   Swap usage:   0%   Users logged in: 0

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

ops@testvm:~$
```

Теперь, чтобы проверить то, что вы создали, можете использовать команду `azure vm show testrg testvm`. В этом примере мы создали работающую виртуальную машину Ubuntu в Azure, входить в которую можно только с помощью пары ключей SSH. Пароли в ней отключены.

```
chrisL@fedora$ azure vm show testrg testvm
info:    Executing command vm show
+ Looking up the VM "testvm"
+ Looking up the NIC "testnic"
+ Looking up the public ip "testpip"
data:    Id                              :/subscriptions/guid/resourceGroups/testrg/providers/Microsoft.Compute/virtualMachines/testvm
data:    ProvisioningState               :Succeeded
data:    Name                            :testvm
data:    Location                        :westeurope
data:    FQDN                            :testsubdomain.westeurope.cloudapp.azure.com
data:    Type                            :Microsoft.Compute/virtualMachines
data:
data:    Hardware Profile:
data:      Size                          :Standard_A1
data:
data:    Storage Profile:
data:      Image reference:
data:        Publisher                   :canonical
data:        Offer                       :UbuntuServer
data:        Sku                         :14.04.3-LTS
data:        Version                     :latest
data:
data:      OS Disk:
data:        OSType                      :Linux
data:        Name                        :cli4eecdddc349d6015-os-1443465824206
data:        Caching                     :ReadWrite
data:        CreateOption                :FromImage
data:        Vhd:
data:          Uri                       :https://computeteststore.blob.core.windows.net/vhds/cli4eecdddc349d6015-os-1443465824206.vhd
data:
data:    OS Profile:
data:      Computer Name                 :testvm
data:      User Name                     :ops
data:      Linux Configuration:
data:        Disable Password Auth       :true
data:        SSH Public Keys:
data:          Public Key #1:
data:            Path                    :/home/ops/.ssh/authorized_keys
data:            Key                     :MIIBrTCCAZigAwIBAgIBATALBgkqhkiG9w0BAQUwADAiGA8yMDE1MDkyODE4MzM0
<snip>
data:
data:    Network Profile:
data:      Network Interfaces:
data:        Network Interface #1:
data:          Id                        :/subscriptions/guid/resourceGroups/testrg/providers/Microsoft.Network/networkInterfaces/testnic
data:          Primary                   :true
data:          MAC Address               :00-0D-3A-21-8E-AE
data:          Provisioning State        :Succeeded
data:          Name                      :testnic
data:          Location                  :westeurope
data:            Private IP alloc-method :Dynamic
data:            Private IP address      :192.168.1.101
data:            Public IP address       :40.115.48.189
data:            FQDN                    :testsubdomain.westeurope.cloudapp.azure.com
data:
data:    Diagnostics Instance View:
info:    vm show command OK
```

### Дальнейшие действия

Теперь вы готовы работать с несколькими сетевыми компонентами и виртуальными машинами.

<!---HONumber=AcomDC_0413_2016-->
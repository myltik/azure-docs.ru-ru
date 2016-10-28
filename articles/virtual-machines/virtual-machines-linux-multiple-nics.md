<properties
   pageTitle="Настройка нескольких сетевых карт на виртуальной машине Linux | Microsoft Azure"
   description="Узнайте, как создать виртуальную машину с несколькими сетевыми картами с помощью Azure CLI или шаблонов Resource Manager."
   services="virtual-machines-linux"
   documentationCenter=""
   authors="iainfoulds"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure"
   ms.date="08/02/2016"
   ms.author="iainfou"/>

# Создание виртуальной машины с несколькими сетевыми картами
Можно создать виртуальную машину (ВМ) в Azure, к которой подключено несколько виртуальных сетевых интерфейсов (сетевых карт). Распространен сценарий, когда разные подсети используются для интерфейсных и внутренних подключений, или когда для решения мониторинга или архивации используется выделенная сеть. Этой статье описываются быстрые команды для создания виртуальной машины с несколькими сетевыми картами. Чтобы получить дополнительные сведения, в том числе узнать, как создать нескольких сетевых карт в собственных сценариях Bash, узнайте больше о [развертывании виртуальных машин с несколькими сетевыми картами](../virtual-network/virtual-network-deploy-multinic-arm-cli.md). Различные [размеры виртуальных машин](virtual-machines-linux-sizes.md) поддерживают разное число сетевых карт, так что выбирайте соответствующий размер виртуальной машины.

>[AZURE.WARNING] Подключать несколько сетевых карт следует при создании виртуальной машины. Их нельзя добавить в существующую виртуальную машину. Вы можете [создать новую виртуальную машины на основе исходных виртуальных дисков](virtual-machines-linux-copy-vm.md) и создать несколько сетевых карт при развертывании это виртуальной машины.

## Быстрые команды
Войдите в [интерфейс командной строки Azure](../xplat-cli-install.md) (Azure CLI) и перейдите в режим Resource Manager (`azure config mode arm`).

Сначала создайте группу ресурсов.

```bash
azure group create TestRG --location WestUS
```

Создайте учетную запись хранения для размещения виртуальных машин.

```bash
azure storage account create teststorage --resource-group TestRG \
    --location WestUS --kind Storage --sku-name PLRS
```

Создайте виртуальную сеть для подключения к этим виртуальным машинам.

```bash
azure network vnet create --resource-group TestRG --location WestUS \
    --name TestVNet --address-prefixes 192.168.0.0/16 
```

Создайте две подсети виртуальной сети — для интерфейсного трафика и для внутреннего трафика.

```bash
azure network vnet subnet create --resource-group TestRG --vnet-name TestVNet \
    --name FrontEnd --address-prefix 192.168.1.0/24
azure network vnet subnet create --resource-group TestRG --vnet-name TestVNet \
    --name BackEnd --address-prefix 192.168.2.0/24
```

Создайте две сетевые карты и подключите одну из них к интерфейсной подсети, а другую — к внутренней подсети.

```bash
azure network nic create --resource-group TestRG --location WestUS \
    -n NIC1 --subnet-vnet-name TestVNet --subnet-name FrontEnd
azure network nic create --resource-group TestRG --location WestUS \
    -n NIC2 --subnet-vnet-name TestVNet --subnet-name BackEnd
```

Наконец, создайте виртуальную машину, подключив к ней две сетевые карты, созданные ранее.

```bash
azure vm create \            
    --resource-group TestRG \
    --name TestVM1 \
    --location WestUS \
    --os-type linux \
    --nic-names NIC1,NIC2 \
    --vm-size Standard_DS2_v2
    --storage-account-name teststorage \
    --image-urn UbuntuLTS \
    --admin-username ops \
    --ssh-publickey-file ~/.ssh/id_rsa.pub
```

## Создание нескольких сетевых карт с помощью Azure CLI
Если ранее вы создали виртуальную машину с помощью Azure CLI, значит, вы ознакомлены с быстрыми командами. Процесс создания одной или нескольких сетевых карт аналогичен. Можно прочитать дополнительные сведения о [развертывание нескольких сетевых карт с помощью Azure CLI](../virtual-network/virtual-network-deploy-multinic-arm-cli.md), включая создание сценария для циклического создания всех сетевых карт.

В следующем примере создаются две сетевые карты, каждая из которых подключена к отдельной подсети.

```bash
azure network nic create --resource-group TestRG --location WestUS \
    -n NIC1 --subnet-vnet-name TestVNet --subnet-name FrontEnd
azure network nic create --resource-group TestRG --location WestUS \
    -n NIC2 --subnet-vnet-name TestVNet --subnet-name BackEnd
```

Обычно также создается [группа безопасности сети](../virtual-network/virtual-networks-nsg.md) или [балансировщик нагрузки](../load-balancer/load-balancer-overview.md) для управления трафиком и его распределения между виртуальными машинами. Опять же, эти команды используются для работы с несколькими сетевыми картами. Создаваемые сетевые карты привязываются к группе безопасности сети или балансировщику нагрузки с помощью команды `azure network nic set`, как показано в следующем примере.

```bash
azure network nic set --resource-group TestRG --name NIC1 \
    --network-security-group-name TestNSG
```

Теперь при создании виртуальной машины следует указать несколько сетевых карт. Вместо того, чтобы использовать `--nic-name` для указания одной сетевой карты, используйте `--nic-names` и укажите разделенный запятыми список сетевых карт. Необходимо выбрать соответствующий размер виртуальной машины. Для каждой виртуальной машины существуют ограничения на общее количество сетевых карт, которые можно в нее добавить. Прочитайте дополнительные сведения о [размерах виртуальных машин Linux](virtual-machines-linux-sizes.md). В следующем примере показано, как указать несколько сетевых карт, а затем — размер виртуальной машины, который поддерживает использование нескольких сетевых карт (`Standard_DS2_v2`).

```bash
azure vm create \            
    --resource-group TestRG \
    --name TestVM1 \
    --location WestUS \
    --os-type linux \
    --nic-names NIC1,NIC2 \
    --vm-size Standard_DS2_v2
    --storage-account-name teststorage \
    --image-urn UbuntuLTS \
    --admin-username ops \
    --ssh-publickey-file ~/.ssh/id_rsa.pub
```

## Создание нескольких сетевых карт с помощью шаблонов Resource Manager
В шаблонах Azure Resource Manager используются декларативные JSON-файлы для определения среды. Вы можете прочитать [обзор Azure Resource Manager](../resource-group-overview.md), чтобы узнать больше об этом. Шаблоны Resource Manager дают возможность создать несколько экземпляров ресурса во время развертывания, в том числе создать несколько сетевых карт. Чтобы указать число создаваемых экземпляров, используется объект *copy*.

```bash
"copy": {
    "name": "multiplenics"
    "count": "[parameters('count')]"
}
```

Вы можете прочитать дополнительные сведения о [создании нескольких экземпляров с помощью объекта *copy*](../resource-group-create-multiple.md).

Можно также использовать `copyIndex()`, чтобы добавить номер к имени ресурса, что позволяет создать `NIC1`, `NIC2` и т. д. Ниже показан пример добавления значения индекса.

```bash
"name": "[concat('NIC-', copyIndex())]", 
```

Вы можете ознакомиться с полным примером [создания нескольких сетевых карт с помощью шаблонов Resource Manager](../virtual-network/virtual-network-deploy-multinic-arm-template.md).

## Дальнейшие действия
Обязательно ознакомьтесь с [размерами виртуальных машин Linux](virtual-machines-linux-sizes.md), когда будете создавать виртуальную машину с несколькими сетевыми картами. Обратите внимание на максимальное число сетевых карт, поддерживаемых каждым из размеров виртуальной машины.

Помните, что невозможно добавить дополнительные сетевые карты в существующую виртуальную машины. Все сетевые карты должны быть созданы при развертывании виртуальной машины. Будьте внимательны при планировании развертываний. С самого начала убедитесь в наличии всех необходимых сетевых подключений.

<!---HONumber=AcomDC_0817_2016-->
---
title: "Создание виртуальной машины со статическим общедоступным IP-адресом (Azure CLI 2.0) | Документация Майкрософт"
description: "Узнайте, как создать виртуальную машину со статическим общедоступным IP-адресом с помощью интерфейса командной строки Azure (CLI) версии 2.0."
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 55bc21b0-2a45-4943-a5e7-8d785d0d015c
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2016
ms.author: jdial
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a4c32694949880037f01bb2b6b9779d2cbb9809c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-vm-with-a-static-public-ip-address-using-the-azure-cli-20"></a>Создание виртуальной машины со статическим общедоступным IP-адресом с помощью Azure CLI 2.0

> [!div class="op_single_selector"]
> * [Портал Azure](virtual-network-deploy-static-pip-arm-portal.md)
> * [PowerShell](virtual-network-deploy-static-pip-arm-ps.md)
> * [Azure CLI 2.0](virtual-network-deploy-static-pip-arm-cli.md)
> * [Azure CLI 1.0](virtual-network-deploy-static-pip-cli-nodejs.md)
> * [Шаблон](virtual-network-deploy-static-pip-arm-template.md)
> * [PowerShell (классическая модель)](virtual-networks-reserved-public-ip.md)

[!INCLUDE [virtual-network-deploy-static-pip-intro-include.md](../../includes/virtual-network-deploy-static-pip-intro-include.md)]

В Azure предлагаются две модели развертывания для создания ресурсов и работы с ними: [модель диспетчера ресурсов и классическая модель](../resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json). В этой статье описывается использование модели развертывания c помощью Resource Manager. Для большинства новых развертываний мы рекомендуем использовать эту модель вместо классической.

[!INCLUDE [virtual-network-deploy-static-pip-scenario-include.md](../../includes/virtual-network-deploy-static-pip-scenario-include.md)]

## <a name = "create"></a>Создание виртуальной машины

Эту задачу можно выполнить с помощью Azure CLI 2.0 (в этой статье) или [Azure CLI 1.0](virtual-network-deploy-static-pip-cli-nodejs.md). Значения в прямых кавычках для переменных в последующих шагах позволяют создать ресурсы с параметрами из сценария. Подставьте соответствующие значения для своей среды.

1. Установите [Azure CLI 2.0](/cli/azure/install-az-cli2), если вы еще этого не сделали.
2. Создайте пару открытого и закрытого ключей SSH для виртуальных машин Linux, выполнив действия, описанные в [этой статье](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
3. Выполните вход из командной оболочки с помощью команды `az login`.
4. Создайте виртуальную машину, выполнив следующий скрипт на компьютере Mac или Linux. Общедоступный IP-адрес Azure, виртуальная сеть, сетевой интерфейс и виртуальная машина должны находиться в одном расположении. Хотя эти ресурсы и не должны находиться в одной группе ресурсов, в следующем скрипте они находятся в одной группе ресурсов.

```bash
RgName="IaaSStory"
Location="westus"

# Create a resource group.

az group create \
--name $RgName \
--location $Location

# Create a public IP address resource with a static IP address using the --allocation-method Static option.
# If you do not specify this option, the address is allocated dynamically. The address is assigned to the
# resource from a pool of IP adresses unique to each Azure region. The DnsName must be unique within the
# Azure location it's created in. Download and view the file from https://www.microsoft.com/en-us/download/details.aspx?id=41653#
# that lists the ranges for each region.

PipName="PIPWEB1"
DnsName="iaasstoryws1"
az network public-ip create \
--name $PipName \
--resource-group $RgName \
--location $Location \
--allocation-method Static \
--dns-name $DnsName

# Create a virtual network with one subnet

VnetName="TestVNet"
VnetPrefix="192.168.0.0/16"
SubnetName="FrontEnd"
SubnetPrefix="192.168.1.0/24"
az network vnet create \
--name $VnetName \
--resource-group $RgName \
--location $Location \
--address-prefix $VnetPrefix \
--subnet-name $SubnetName \
--subnet-prefix $SubnetPrefix

# Create a network interface connected to the VNet with a static private IP address and associate the public IP address
# resource to the NIC.

NicName="NICWEB1"
PrivateIpAddress="192.168.1.101"
az network nic create \
--name $NicName \
--resource-group $RgName \
--location $Location \
--subnet $SubnetName \
--vnet-name $VnetName \
--private-ip-address $PrivateIpAddress \
--public-ip-address $PipName

# Create a new VM with the NIC

VmName="WEB1"

# Replace the value for the VmSize variable with a value from the
# https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-sizes article.
VmSize="Standard_DS1"

# Replace the value for the OsImage variable with a value for *urn* from the output returned by entering
# the `az vm image list` command. 

OsImage="credativ:Debian:8:latest"
Username='adminuser'

# Replace the following value with the path to your public key file.
SshKeyValue="~/.ssh/id_rsa.pub"

az vm create \
--name $VmName \
--resource-group $RgName \
--image $OsImage \
--location $Location \
--size $VmSize \
--nics $NicName \
--admin-username $Username \
--ssh-key-value $SshKeyValue
# If creating a Windows VM, remove the previous line and you'll be prompted for the password you want to configure for the VM.
```

Помимо виртуальной машины скрипт также создает следующие ресурсы:
- Один управляемый диск уровня "Премиум" по умолчанию. Вы можете создать диск другого типа. Дополнительные сведения см. в статье [Создание виртуальной машины Linux с помощью предварительной версии Azure CLI 2.0 (az.py)](../virtual-machines/linux/quick-create-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
- Виртуальная сеть, подсеть, сетевая карта и общедоступный IP-адрес. Кроме того, можно использовать *имеющиеся* виртуальные сети, подсети, сетевые карты или общедоступные IP-адреса. Чтобы узнать, как использовать имеющиеся сетевые ресурсы, а не создавать дополнительные, введите `az vm create -h`.

## <a name = "validate"></a>Проверка создания виртуальной машины и общедоступного IP-адреса

1. Введите команду `az resource list --resouce-group IaaSStory --output table`, чтобы просмотреть список ресурсов, созданных с помощью скрипта. В результате должно отобразиться пять ресурсов: сетевой интерфейс, диск, общедоступный IP-адрес, виртуальная сеть и виртуальная машина.
2. Введите команду `az network public-ip show --name PIPWEB1 --resource-group IaaSStory --output table`. В возвращенных выходных данных обратите внимание на значение параметра **IpAddress**, а также на то, что параметр **PublicIpAllocationMethod** имеет значение *Static*.
3. Перед выполнением следующей команды удалите <> и замените *Username* именем, использованным для переменной **Username** в скрипте, а *ipAddress* — **IP-адресом** из предыдущего шага. Выполните следующую команду для подключения к виртуальной машине: `ssh -i ~/.ssh/azure_id_rsa <Username>@<ipAddress>`. 

## <a name= "clean-up"></a>Удаление виртуальной машины и связанных с ней ресурсов

Если вы не планируете использовать в рабочей среде ресурсы, созданные во время этого упражнения, рекомендуется удалить их. За виртуальную машину, общедоступный IP-адрес и диск взимается плата, пока они подготовлены. Чтобы удалить ресурсы, созданные во время этого упражнения, выполните следующие действия.

1. Выполните команду `az resource list --resource-group IaaSStory`, чтобы просмотреть ресурсы в группе ресурсов.
2. Убедитесь, что в ней содержатся только ресурсы, созданные с помощью сценария в этой статье. 
3. Чтобы удалить все ресурсы, созданные во время этого упражнения, выполните команду `az group delete -n IaaSStory`. Эта команда удаляет группу ресурсов и все содержащиеся в ней ресурсы.

## <a name="next-steps"></a>Дальнейшие действия

Виртуальная машина, созданная в этой статье, может принимать и передавать любой сетевой трафик. Вы можете определить правила, ограничивающие входящий и исходящий трафик в группе безопасности сети для сетевого интерфейса, подсети или для того и другого. Дополнительные сведения о группах безопасности сети см. в [этой статье](virtual-networks-nsg.md).
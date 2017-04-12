---
title: "Создание виртуальной машины с несколькими сетевыми картами (Azure CLI 2.0) | Документация Майкрософт"
description: "Узнайте, как создать виртуальную машину с несколькими сетевыми картами с помощью Azure CLI 2.0."
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 8e906a4b-8583-4a97-9416-ee34cfa09a98
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/02/2016
ms.author: jdial
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 19b1757dd694e756cfd2d0d6cd67e64f43ccab7f
ms.lasthandoff: 04/03/2017


---
# <a name="create-a-vm-with-multiple-nics-using-the-azure-cli-20"></a>Создание виртуальной машины с несколькими сетевыми картами с помощью Azure CLI 2.0

[!INCLUDE [virtual-network-deploy-multinic-arm-selectors-include.md](../../includes/virtual-network-deploy-multinic-arm-selectors-include.md)]

[!INCLUDE [virtual-network-deploy-multinic-intro-include.md](../../includes/virtual-network-deploy-multinic-intro-include.md)]

> [!NOTE]
> В Azure предлагаются две модели развертывания для создания ресурсов и работы с ними: [модель Resource Manager и классическая модель](../resource-manager-deployment-model.md).  В этой статье описывается использование модели развертывания c помощью Resource Manager. Для большинства новых развертываний мы рекомендуем использовать эту модель вместо [классической](virtual-network-deploy-multinic-classic-cli.md).
>

## <a name="create"></a>Создание виртуальной машины

Эту задачу можно выполнить с помощью Azure CLI 2.0 (в этой статье) или [Azure CLI 1.0](virtual-network-deploy-multinic-cli-nodejs.md). Значения в прямых кавычках для переменных в последующих шагах позволяют создать ресурсы с параметрами из сценария. Подставьте соответствующие значения для своей среды.

1. Установите [Azure CLI 2.0](/cli/azure/install-az-cli2), если вы еще этого не сделали.
2. Создайте пару открытого и закрытого ключей SSH для виртуальных машин Linux, выполнив действия, описанные в [этой статье](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
3. Выполните вход из командной оболочки с помощью команды `az login`.
4. Создайте виртуальную машину, выполнив следующий скрипт на компьютере Mac или Linux. Скрипт создает группу ресурсов, одну виртуальную сеть с двумя подсетями, две сетевые карты и виртуальную машину с двумя подключенными сетевыми картами. Одна из сетевых карт подключена к одной подсети. Этой сетевой карте назначается статический общедоступный и частный IP-адрес. Другая сетевая карта подключается к другой подсети, и ей назначается только статический частный IP-адрес. Сетевая карта, общедоступный IP-адрес, виртуальная сеть и виртуальная машина должны находиться в одном расположении и в одной подписке. Хотя эти ресурсы и не должны находиться в одной группе ресурсов, в следующем скрипте они находятся в одной группе ресурсов.

```bash
#!/bin/sh

RgName="Multi-NIC-VM"
Location="westus"

# Create a resource group.
az group create \
--name $RgName \
--location $Location

# The address is assigned to the resource from a pool of IP adresses unique to each Azure region. 
# Download and view the file from https://www.microsoft.com/en-us/download/details.aspx?id=41653 that lists
# the ranges for each region.

PipName="PIP-WEB"
az network public-ip create \
--name $PipName \
--resource-group $RgName \
--location $Location \
--allocation-method Static

# Create a virtual network with one subnet

VnetName="VNet1"
VnetPrefix="10.0.0.0/16"
VnetSubnet1Name="Front-End"
VnetSubnet1Prefix="10.0.0.0/24"
az network vnet create \
--name $VnetName \
--resource-group $RgName \
--location $Location \
--address-prefix $VnetPrefix \
--subnet-name $VnetSubnet1Name \
--subnet-prefix $VnetSubnet1Prefix

# Create a second subnet within the VNet

VnetSubnet2Name="Back-end"
VnetSubnet2Prefix="10.0.1.0/24"
az network vnet subnet create \
--vnet-name $VnetName \
--resource-group $RgName \
--name $VnetSubnet2Name \
--address-prefix $VnetSubnet2Prefix

# Create a network interface connected to one of the subnets. The NIC is assigned a single dynamic private and
# public IP address by default, but you can instead, assign static addresses, or no public IP address at all.
# You can also assign multiple private or public IP addresses to each NIC. To learn more about IP addressing
# options for NICs, enter the az network nic create -h command.

Nic1Name="NIC-FE"
PrivateIpAddress1="10.0.0.5"

az network nic create \
--name $Nic1Name \
--resource-group $RgName \
--location $Location \
--subnet $VnetSubnet1Name \
--vnet-name $VnetName \
--private-ip-address $PrivateIpAddress1 \
--public-ip-address $PipName

# Create a second network interface and connect it to the other subnet. Though multiple NICs attached to the same
# VM can be connected to different subnets, the subnets must all be within the same VNet. Add additional NICs as necessary.

Nic2Name="NIC-BE"
PrivateIpAddress2="10.0.1.5"

az network nic create \
--name $Nic2Name \
--resource-group $RgName \
--location $Location \
--subnet $VnetSubnet2Name \
--vnet-name $VnetName \
--private-ip-address $PrivateIpAddress2

# Create a VM and attach the two NICs.

VmName="WEB"

# Replace the value for the following **VmSize** variable with a value from the
# https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-sizes article. Not all VM sizes support
# more than one NIC, so be sure to select a VM size that supports the number of NICs you want to attach to the VM.
# You must create the VM with at least two NICs if you want to add more after VM creation. If you create a VM with
# only one NIC, you can't add additional NICs to the VM after VM creation, regardless of how many NICs the VM supports.
# The VM size specified in the following variable supports two NICs.

VmSize="Standard_DS2"

# Replace the value for the OsImage variable value with a value for *urn* from the output returned by entering the
# az vm image list command.

OsImage="credativ:Debian:8:latest"

Username="adminuser"

# Replace the following value with the path to your public key file.

SshKeyValue="~/.ssh/id_rsa.pub"

# Before executing the following command, add variable names of additional NICs you may have added to the script that
# you want to attach to the VM. If creating a Windows VM, remove the **ssh-key-value** line and you'll be prompted for
# the password you want to configure for the VM.

az vm create \
--name $VmName \
--resource-group $RgName \
--image $OsImage \
--location $Location \
--size $VmSize \
--nics $Nic1Name $Nic2Name \
--admin-username $Username \
--ssh-key-value $SshKeyValue
```

Помимо виртуальной машины с двумя сетевыми картами скрипт также создает следующие ресурсы:
- Один управляемый диск уровня "Премиум" по умолчанию. Вы можете создать диск другого типа. Дополнительные сведения см. в статье [Создание виртуальной машины Linux с помощью предварительной версии Azure CLI 2.0 (az.py)](../virtual-machines/linux/quick-create-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
- Виртуальная сеть с двумя подсетями и одним общедоступным IP-адресом. Кроме того, можно использовать *имеющиеся* виртуальные сети, подсети, сетевые карты или общедоступные IP-адреса. Чтобы узнать, как использовать имеющиеся сетевые ресурсы, а не создавать дополнительные, введите `az vm create -h`.

## <a name = "validate"></a>Проверка создания виртуальной машины и сетевых карт

1. Введите команду `az resource list --resouce-group Multi-NIC-VM --output table`, чтобы просмотреть список ресурсов, созданных с помощью скрипта. В результате должно отобразиться шесть ресурсов: две сетевые карты, один диск, общедоступный IP-адрес, одна виртуальная сеть и виртуальная машина.
2. Введите команду `az network public-ip show --name PIP-WEB --resource-group Multi-NIC-VM --output table`. В возвращенных выходных данных обратите внимание на значение параметра **IpAddress**, а также на то, что параметр **PublicIpAllocationMethod** имеет значение *Static*.
3. Перед выполнением следующей команды удалите <> и замените *Username* именем, использованным для переменной **Username** в скрипте, а *ipAddress* — **IP-адресом** из предыдущего шага. Выполните следующую команду для подключения к виртуальной машине: `ssh -i ~/.ssh/azure_id_rsa <Username>@<ipAddress>`. 
4. После подключения к виртуальной машине выполните команду `sudo ifconfig`, чтобы отобразить интерфейсы *eth0* и *eth1*. Каждой сетевой карте назначен статический частный IP-адрес, указанный в скрипте DHCP-серверами Azure. IP- и MAC-адреса, назначенные сетевым картам, не изменяются, пока виртуальная машина не будет удалена. Мы рекомендуем не изменять IP-адрес в операционной системе, так как таким образом можно отключить подключение к компьютеру. Общедоступные IP-адреса не отображаются в операционной системе, так как это сетевые адреса, преобразованные в IP-адрес и из него с помощью инфраструктуры Azure.

## <a name= "clean-up"></a>Удаление виртуальной машины и связанных с ней ресурсов

Если вы не планируете использовать в рабочей среде ресурсы, созданные во время этого упражнения, рекомендуется удалить их. За виртуальную машину, общедоступный IP-адрес и диск взимается плата, пока они подготовлены. Чтобы удалить ресурсы, созданные во время этого упражнения, выполните следующие действия.
1. Выполните команду `az resource list --resource-group Multi-NIC-VM`, чтобы просмотреть ресурсы в группе ресурсов.
2. Убедитесь, что в ней содержатся только ресурсы, созданные с помощью сценария в этой статье. 
3. Чтобы удалить все ресурсы, созданные во время этого упражнения, выполните команду `az group delete --name Multi-NIC-VM`. Эта команда удаляет группу ресурсов и все содержащиеся в ней ресурсы.

## <a name="next-steps"></a>Дальнейшие действия

Виртуальная машина, созданная в этой статье, может принимать и передавать любой сетевой трафик. Вы можете определить правила, ограничивающие входящий и исходящий трафик в группе безопасности сети для каждого сетевого интерфейса, каждой подсети или для того и другого. Дополнительные сведения о группах безопасности сети см. в [этой статье](virtual-networks-nsg.md).

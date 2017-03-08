---
title: "Виртуальная машина с несколькими сетевыми картами (Azure CLI 2.0) | Документация Майкрософт"
description: "Назначение виртуальной машине нескольких IP-адресов с помощью Azure CLI 2.0 | Resource Manager"
services: virtual-network
documentationcenter: na
author: anavinahar
manager: narayan
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/17/2016
ms.author: annahar
translationtype: Human Translation
ms.sourcegitcommit: d52694b2604510f1926142ace89cc7781ca83a1c
ms.openlocfilehash: 8b969b1367b3af752a89c8fea62a0685b514e8b5
ms.lasthandoff: 02/27/2017


---
# <a name="assign-multiple-ip-addresses-to-virtual-machines-using-the-azure-cli-20"></a>Назначение виртуальным машинам нескольких IP-адресов с помощью Azure CLI 2.0

[!INCLUDE [virtual-network-multiple-ip-addresses-intro.md](../../includes/virtual-network-multiple-ip-addresses-intro.md)]

В этой статье описывается создание виртуальной машины с помощью модели развертывания Azure Resource Manager с использованием Azure CLI 2.0. Для ресурсов, созданных с помощью классической модели развертывания, нельзя назначить несколько IP-адресов. Дополнительные сведения о моделях развертывания Azure см. в статье [Azure Resource Manager vs. classic deployment: Understand deployment models and the state of your resources](../resource-manager-deployment-model.md) (Развертывание с помощью Azure Resource Manager и классическое развертывание. Общие сведения о моделях развертывания и состоянии ресурсов).

[!INCLUDE [virtual-network-preview](../../includes/virtual-network-preview.md)]

[!INCLUDE [virtual-network-multiple-ip-addresses-template-scenario.md](../../includes/virtual-network-multiple-ip-addresses-scenario.md)]

## <a name="a-name--createacreate-a-vm-with-multiple-ip-addresses"></a><a name = "create"></a>Создание виртуальной машины с несколькими IP-адресами

Эту задачу можно выполнить с помощью Azure CLI 2.0 (в этой статье) или [Azure CLI 1.0](virtual-network-multiple-ip-addresses-cli-nodejs.md). Подставьте соответствующие значения для своей среды. Вы можете создать пример виртуальной машины с несколькими IP-адресами, как описано в нашем сценарии. Измените имена переменных в прямых кавычках и типы IP-адресов в соответствии с требованиями своей реализации. 

1. Установите [Azure CLI 2.0](/cli/azure/install-az-cli2), если вы еще этого не сделали.
2. Создайте пару открытого и закрытого ключей SSH для виртуальных машин Linux, выполнив действия, описанные в [этой статье](../virtual-machines/virtual-machines-linux-mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
3. Выполните вход из командной оболочки, применив команду `az login`, и выберите используемую подписку.
4. <a name="register"></a>Чтобы зарегистрироваться для получения предварительной версии, выполните следующие команды PowerShell (невозможно зарегистрироваться с помощью интерфейса командной строки):

    ```powershell
    Register-AzureRmProviderFeature  -FeatureName AllowMultipleIpConfigurationsPerNic    -ProviderNamespace Microsoft.Network
    Register-AzureRmProviderFeature  -FeatureName AllowLoadBalancingonSecondaryIpconfigs -ProviderNamespace Microsoft.Network
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Network
    ```

    Не пытайтесь выполнить остальные шаги, пока не увидите следующий результат при выполнении команды ```Get-AzureRmProviderFeature```:
    
    ```powershell
    FeatureName                            ProviderName      RegistrationState
    -----------                            ------------      -----------------
    AllowLoadBalancingOnSecondaryIpConfigs Microsoft.Network Registered       
    AllowMultipleIpConfigurationsPerNic    Microsoft.Network Registered       
    ```
        
    >[!NOTE] 
    >Регистрация может занять несколько минут.
5. Создайте виртуальную машину, выполнив следующий скрипт на компьютере Mac или Linux. Скрипт создает группу ресурсов, одну виртуальную сеть, одну сетевую карту с тремя конфигурациями IP-адреса и виртуальную машину с двумя подключенными сетевыми картами. Сетевая карта, общедоступный IP-адрес, виртуальная сеть и виртуальная машина должны находиться в одном расположении и в одной подписке. Хотя эти ресурсы и не должны находиться в одной группе ресурсов, в следующем скрипте они находятся в одной группе ресурсов.

    ```azurecli
    #!/bin/sh

    RgName="myResourceGroup"
    Location="westcentralus"
    az group create --name $RgName --location $Location

    # Create a public IP address resource with a static IP address using the `--allocation-method Static` option. If you
    # do not specify this option, the address is allocated dynamically. The address is assigned to the resource from a pool
    # of IP adresses unique to each Azure region. Download and view the file from
    # https://www.microsoft.com/en-us/download/details.aspx?id=41653 that lists the ranges for each region.

    PipName="myPublicIP"

    # This name must be unique within an Azure location.
    DnsName="myDNSName"

    az network public-ip create \
    --name $PipName \
    --resource-group $RgName \
    --location $Location \
    --dns-name $DnsName\
    --allocation-method Static

    # Create a virtual network with one subnet

    VnetName="myVnet"
    VnetPrefix="10.0.0.0/16"
    VnetSubnetName="mySubnet"
    VnetSubnetPrefix="10.0.0.0/24"

    az network vnet create \
    --name $VnetName \
    --resource-group $RgName \
    --location $Location \
    --address-prefix $VnetPrefix \
    --subnet-name $VnetSubnetName \
    --subnet-prefix $VnetSubnetPrefix

    # Create a network interface connected to the subnet and associate the public IP address to it. Azure will create the
    # first IP configuration with a dynamic private IP address and will associate the public IP address resource to it.

    NicName="MyNic1"

    az network nic create \
    --name $NicName \
    --resource-group $RgName \
    --location $Location \
    --subnet $VnetSubnet1Name \
    --vnet-name $VnetName \
    --public-ip-address $PipName

    # Create a second public IP address, a second IP configuration, and associate it to the NIC. This configuration has a
    # static public IP address and a static private IP address.

    az network public-ip create --resource-group $RgName --location $Location --name myPublicIP2 --dns-name mypublicdns2 --allocation-method Static
    az network nic ip-config create --resource-group $RgName --nic-name $NicName --name IPConfig-2 --private-ip-address 10.0.0.5 --public-ip-name myPublicIP2

    # Create a third IP configuration, and associate it to the NIC. This configuration has a dynamic private IP address and
    # no public IP address.

    azure network nic ip-config create --resource-group $RgName --nic-name $NicName --name IPConfig-3

    # Note: Though this article assigns all IP configurations to a single NIC, you can also assign multiple IP configurations
    # to any NIC in a VM. To learn how to create a VM with multiple NICs, read the Create a VM with multiple NICs 
    # article: https://docs.microsoft.com/azure/virtual-network/virtual-network-deploy-multinic-arm-cli.

    # Create a VM and attach the NIC.

    VmName="myVm"

    # Replace the value for the following **VmSize** variable with a value from the
    # https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-sizes article. The script fails if the VM size
    # is not supported in the location you select. Run the `azure vm sizes --location westcentralus` command to get a full list
    # of VMs in US West Central, for example.

    VmSize="Standard_DS1"

    # Replace the value for the OsImage variable value with a value for *urn* from the output returned by entering the
    # `az vm image list` command.

    OsImage="credativ:Debian:8:latest"

    Username="adminuser"

    # Replace the following value with the path to your public key file. If you're creating a Windows VM, remove the following
    # line and you'll be prompted for the password you want to configure for the VM.

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
    ```

    Помимо виртуальной машины с сетевой картой и тремя конфигурациями IP-адреса скрипт создает следующие ресурсы: — Один управляемый диск уровня "Премиум" по умолчанию. Вы можете создать диск другого типа. Дополнительные сведения см. в статье [Создание виртуальной машины Linux с помощью предварительной версии Azure CLI 2.0 (az.py)](../virtual-machines/virtual-machines-linux-quick-create-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
        — Виртуальная сеть с одной подсетью и двумя общедоступными IP-адресами. Кроме того, можно использовать *имеющиеся* виртуальные сети, подсети, сетевые карты или общедоступные IP-адреса. Чтобы узнать, как использовать имеющиеся сетевые ресурсы, а не создавать дополнительные, введите `az vm create -h`.

    > [!NOTE]
    > За общедоступные IP-адреса взимается номинальная плата. Дополнительные сведения о ценах на IP-адреса см. на странице [Цены на IP-адреса](https://azure.microsoft.com/pricing/details/ip-addresses). Число общедоступных IP-адресов, которые можно использовать в одной подписке, ограничено. Сведения об ограничениях см. в статье [Подписка Azure, границы, квоты и ограничения службы](../azure-subscription-service-limits.md#networking-limits).

7. Создав виртуальную машину, введите команду `az network nic show --name MyNic1 --resource-group myResourceGroup`, чтобы просмотреть конфигурацию сетевой карты. Введите команду `az network nic ip-config list --nic-name MyNic1 --resource-group myResourceGroup --output table` для просмотра списка конфигураций IP-адресов, связанных с сетевой картой.

8. Добавьте в операционную систему виртуальной машины частные IP-адреса, выполнив действия, соответствующие вашей операционной системе, как описано в разделе [Добавление IP-адресов в операционную систему виртуальной машины](#os-config) этой статьи.

## <a name="a-nameaddaadd-ip-addresses-to-a-vm"></a><a name="add"></a>Добавление IP-адресов в виртуальную машину

Выполнив следующие шаги, вы сможете назначить дополнительные частные или общедоступные IP-адреса существующим сетевым адаптерам. Примеры созданы на основе [сценария](#Scenario), описанного в этой статье.

1. Откройте командную оболочку и в рамках одного сеанса командной строки выполните все действия, описанные в этом разделе. Если Azure CLI еще не установлен и не настроен, выполните инструкции из статьи по [установке Azure CLI 2.0](/cli/azure/install-az-cli2?toc=%2fazure%2fvirtual-network%2ftoc.json), а затем войдите в учетную запись Azure, выполнив команду `az-login`.

2. Зарегистрируйтесь для использования общедоступной предварительной версии, выполнив [шаг 4](#register) в разделе **Создание виртуальной машины с несколькими IP-адресами** этой статьи.

3. Выполните действия, описанные в одном из следующих разделов, с учетом задач.

    **Добавление частного IP-адреса**
    
    Чтобы добавить к сетевой карте частный IP-адрес, создайте конфигурацию IP-адресов с помощью следующей команды. Если вам нужен динамический частный IP-адрес, удалите из этой команды элемент `-PrivateIpAddress 10.0.0.7`. Указываемый статический IP-адрес должен быть свободен в используемой подсети.

    ```azurecli
    az network nic ip-config create --resource-group myResourceGroup --nic-name myNic1 --private-ip-address 10.0.0.7 --name IPConfig-4
    ```
    
    Вы можете создать любое количество конфигураций, указывая для них уникальные имена и уникальные частные IP-адреса (если используются статические IP-адреса).

    **Добавление общедоступного IP-адреса**
    
    Чтобы добавить общедоступный IP-адрес, его нужно связать с новой или имеющейся IP-конфигурацией. Выполните шаги, описанные в одном из следующих разделов, в соответствии с задачами.

    > [!NOTE]
    > За общедоступные IP-адреса взимается номинальная плата. Дополнительные сведения о ценах на IP-адреса см. на странице [Цены на IP-адреса](https://azure.microsoft.com/pricing/details/ip-addresses). Число общедоступных IP-адресов, которые можно использовать в одной подписке, ограничено. Сведения об ограничениях см. в статье [Подписка Azure, границы, квоты и ограничения службы](../azure-subscription-service-limits.md#networking-limits).
    >

    **Связывание ресурса с новой IP-конфигурацией**
    
    Чтобы добавить общедоступный IP-адрес в новую IP-конфигурацию, необходимо добавить и частный IP-адрес, так как все IP-конфигурации должны иметь частный IP-адрес. В конфигурацию можно добавить имеющийся ресурс общедоступного IP-адреса или создать новый. Чтобы создать ресурс, выполните следующую команду:
    
    ```azurecli
      az network public-ip create --resource-group myResourceGroup --location westcentralus --name myPublicIP3 --dns-name mypublicdns3
    ```

     Чтобы создать новую IP-конфигурацию с частным динамическим IP-адресом и связанным ресурсом общедоступного IP-адреса *myPublicIP3*, выполните следующую команду:

    ```azurecli
    az network nic ip-config create --resource-group myResourceGroup --nic-name myNic1 --name IPConfig-5 --public-ip-address myPublicIP3
    ```

    **Связывание ресурса с имеющейся IP-конфигурацией**
    Ресурс общедоступного IP-адреса может быть связан только с такой IP-конфигурацией, у которой еще нет такого ресурса. Чтобы определить, связан ли с конкретной IP-конфигурацией какой-либо общедоступный IP-адрес, выполните следующую команду:

    ```azurecli
    az network nic ip-config list --resource-group myResourceGroup --nic-name myNic1 --query "[?provisioningState=='Succeeded'].{ Name: name, PublicIpAddressId: publicIpAddress.id }" --output table
    ```

    Возвращаемые выходные данные:

    ```azurecli
    Name        PublicIpAddressId
    --------    ------------------------------------------------------------------------------------------------------------
    ipconfig1   /subscriptions/[Id]/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP1
    IPConfig-2  /subscriptions/[Id]/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP2
    IPConfig-3  
    ```

    Столбец **PublicIpAddressId** для конфигурации *IpConfig-3* в выходных данных пуст. Это означает, что в настоящее время с этой конфигурацией не связан никакой общедоступный IP-адрес. Вы можете добавить к конфигурации IpConfig-3 имеющийся ресурс общедоступного IP-адреса или создать новый ресурс, выполнив следующую команду:

    ```azurecli
    az network public-ip create --resource-group  myResourceGroup --location westcentralus --name myPublicIP3 --dns-name mypublicdns3 --allocation-method Static
    ```
    
    Чтобы связать ресурс общедоступного IP-адреса с имеющейся IP-конфигурацией с именем *IPConfig-3*, выполните следующую команду:
    
    ```azurecli
    az network nic ip-config update --resource-group myResourceGroup --nic-name myNic1 --name IPConfig-3 --public-ip myPublicIP3
    ```

4. Просмотрите идентификаторы ресурсов частных и общедоступных IP-адресов, назначенных сетевой карте. Для этого введите следующую команду.

    ```azurecli
    az network nic ip-config list --resource-group myResourceGroup --nic-name myNic1 --query "[?provisioningState=='Succeeded'].{ Name: name, PrivateIpAddress: privateIpAddress, PrivateIpAllocationMethod: privateIpAllocationMethod, PublicIpAddressId: publicIpAddress.id }" --output table
    ```
    Возвращаемые выходные данные: 
    
    ```azurecli
    Name        PrivateIpAddress    PrivateIpAllocationMethod    PublicIpAddressId
    --------    ------------------  ---------------------------  ------------------------------------------------------------------------------------------------------------
    ipconfig1   10.0.0.4            Dynamic                      /subscriptions/[Id]/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP1
    IPConfig-2  10.0.0.5            Static                       /subscriptions/[Id]/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP2
    IPConfig-3  10.0.0.6            Dynamic                      /subscriptions/[Id]/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP3
    ```

5. Добавьте в операционную систему виртуальной машины частные IP-адреса, которые вы ранее назначили сетевой карте. Для этого выполните инструкции из раздела [Добавление IP-адресов в операционную систему виртуальной машины](#os-config) этой статьи. Не добавляйте в операционную систему общедоступные IP-адреса.

[!INCLUDE [virtual-network-multiple-ip-addresses-os-config.md](../../includes/virtual-network-multiple-ip-addresses-os-config.md)]


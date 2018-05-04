---
title: Настройка частных IP-адресов для виртуальных машин с помощью Azure PowerShell | Документация Майкрософт
description: Узнайте, как настроить частные IP-адреса для виртуальных машин с помощью PowerShell.
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: d5f18929-15e3-40a2-9ee3-8188bc248ed8
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/23/2016
ms.author: jdial
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: b0e8153f1d0cecd4efe66dc7cce64addd6ed62aa
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2018
---
# <a name="configure-private-ip-addresses-for-a-virtual-machine-using-powershell"></a>Настройка частных IP-адресов для виртуальной машины с помощью PowerShell

[!INCLUDE [virtual-networks-static-private-ip-selectors-arm-include](../../includes/virtual-networks-static-private-ip-selectors-arm-include.md)]

[!INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

Azure предоставляет две модели развертывания: с помощью Azure Resource Manager и классическую. Для создания ресурсов корпорация Майкрософт рекомендует использовать модель развертывания с помощью Resource Manager. Дополнительные сведения о различиях между двумя моделями см. в статье [Azure Resource Manager vs. classic deployment: Understand deployment models and the state of your resources](../azure-resource-manager/resource-manager-deployment-model.md) (Azure Resource Manager и классическое развертывание. Общие сведения о моделях развертывания и состоянии ресурсов). В этой статье описывается модель развертывания с использованием менеджера ресурсов. Кроме того, вы можете [управлять статическим частным IP-адресом в классической модели развертывания](virtual-networks-static-private-ip-classic-ps.md).

[!INCLUDE [virtual-networks-static-ip-scenario-include](../../includes/virtual-networks-static-ip-scenario-include.md)]

Для приведенных ниже примеров команд PowerShell требуется уже созданная простая среда, основанная на приведенном выше сценарии. Для выполнения команд в том виде, в каком они представлены в данном документе, сначала создайте тестовую среду, описанную в статье [Создание виртуальной сети с помощью PowerShell](quick-create-powershell.md).

## <a name="create-a-vm-with-a-static-private-ip-address"></a>Создание виртуальной машины со статическим частным IP-адресом
Чтобы создать виртуальную машину с именем *DNS01* в подсети *FrontEnd* виртуальной сети *TestVNet* со статическим частным IP-адресом *192.168.1.101*, выполните следующие действия.

1. Задайте переменные для учетной записи хранения, расположения, группы ресурсов и используемых учетных данных. Понадобится ввести имя пользователя и пароль для виртуальной машины. Учетная запись хранения и группа ресурсов уже должны существовать.

    ```powershell
    $stName  = "vnetstorage"
    $locName = "Central US"
    $rgName  = "TestRG"
    $cred    = Get-Credential -Message "Type the name and password of the local administrator account."
    ```

2. Получите виртуальную сеть и подсеть, в которой хотите создать виртуальную машину.

    ```powershell
    $vnet   = Get-AzureRmVirtualNetwork -ResourceGroupName TestRG -Name TestVNet
    $subnet = $vnet.Subnets[0].Id
    ```

3. При необходимости создайте общедоступный IP-адрес для доступа к виртуальной машине из Интернета.

    ```powershell
    $pip = New-AzureRmPublicIpAddress -Name TestPIP -ResourceGroupName $rgName `
    -Location $locName -AllocationMethod Dynamic
    ```

4. Создайте сетевую карту, используя статический частный IP-адрес, который хотите назначить виртуальной машине. Убедитесь, что IP-адрес находится в диапазоне подсети, в которую добавляется виртуальная машина. Это основной шаг для данной статьи, где частный IP-адрес задается как статический.

    ```powershell
    $nic = New-AzureRmNetworkInterface -Name TestNIC -ResourceGroupName $rgName `
    -Location $locName -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id `
    -PrivateIpAddress 192.168.1.101
    ```

5. Создайте виртуальную машину с сетевым адаптером:

    ```powershell
    $vm = New-AzureRmVMConfig -VMName DNS01 -VMSize "Standard_A1"
    $vm = Set-AzureRmVMOperatingSystem -VM $vm -Windows -ComputerName DNS01 `
    -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
    $vm = Set-AzureRmVMSourceImage -VM $vm -PublisherName MicrosoftWindowsServer `
    -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
    $vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id
    $osDiskUri = $storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/WindowsVMosDisk.vhd"
    $vm = Set-AzureRmVMOSDisk -VM $vm -Name "windowsvmosdisk" -VhdUri $osDiskUri `
    -CreateOption fromImage
    New-AzureRmVM -ResourceGroupName $rgName -Location $locName -VM $vm 
    ```

Не рекомендуем статически назначать виртуальной машине Azure частный IP-адрес в ее операционной системе за исключением ситуаций, когда это необходимо, например при [назначении нескольких IP-адресов виртуальной машине Windows](virtual-network-multiple-ip-addresses-powershell.md). Если вы будете вручную устанавливать частный IP-адрес в операционной системе, убедитесь, что он соответствует частному IP-адресу, назначенному [сетевому интерфейсу](virtual-network-network-interface-addresses.md#change-ip-address-settings) Azure. Иначе соединение с виртуальной машиной может быть потеряно. Ознакомьтесь с дополнительными сведениями о параметрах [частных IP-адресов](virtual-network-network-interface-addresses.md#private). Никогда не следует вручную назначать общедоступный IP-адрес для виртуальной машины Azure в ее операционной системе.

## <a name="retrieve-static-private-ip-address-information-for-a-network-interface"></a>Получение сведений о статическом частном IP-адресе сетевого интерфейса
Чтобы просмотреть сведения о статическом частном IP-адресе виртуальной машины, созданной с помощью приведенного выше скрипта, выполните следующую команду PowerShell и обратите внимание на значения *PrivateIpAddress* и *PrivateIpAllocationMethod*.

```powershell
Get-AzureRmNetworkInterface -Name TestNIC -ResourceGroupName TestRG
```

Ожидаемые выходные данные:

    Name                 : TestNIC
    ResourceGroupName    : TestRG
    Location             : centralus
    Id                   : /subscriptions/[Id]/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNIC
    Etag                 : W/"[Id]"
    ProvisioningState    : Succeeded
    Tags                 : 
    VirtualMachine       : {
                             "Id": "/subscriptions/[Id]/resourceGroups/TestRG/providers/Microsoft.Compute/virtualMachines/DNS01"
                           }
    IpConfigurations     : [
                             {
                               "Name": "ipconfig1",
                               "Etag": "W/\"[Id]\"",
                               "Id": "/subscriptions/[Id]/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNIC/ipConfigurations/ipconfig1",
                               "PrivateIpAddress": "192.168.1.101",
                               "PrivateIpAllocationMethod": "Static",
                               "Subnet": {
                                 "Id": "/subscriptions/[Id]/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd"
                               },
                               "PublicIpAddress": {
                                 "Id": "/subscriptions/[Id]/resourceGroups/TestRG/providers/Microsoft.Network/publicIPAddresses/TestPIP"
                               },
                               "LoadBalancerBackendAddressPools": [],
                               "LoadBalancerInboundNatRules": [],
                               "ProvisioningState": "Succeeded"
                             }
                           ]
    DnsSettings          : {
                             "DnsServers": [],
                             "AppliedDnsServers": [],
                             "InternalDnsNameLabel": null,
                             "InternalFqdn": null
                           }
    EnableIPForwarding   : False
    NetworkSecurityGroup : null
    Primary              : True

## <a name="remove-a-static-private-ip-address-from-a-network-interface"></a>Удаление статического частного IP-адреса из сетевого интерфейса
Чтобы удалить статический частный IP-адрес, добавленный на виртуальную машину в приведенном выше сценарии, выполните следующие команды PowerShell:

```powershell
$nic=Get-AzureRmNetworkInterface -Name TestNIC -ResourceGroupName TestRG
$nic.IpConfigurations[0].PrivateIpAllocationMethod = "Dynamic"
Set-AzureRmNetworkInterface -NetworkInterface $nic
```

Ожидаемые выходные данные:

    Name                 : TestNIC
    ResourceGroupName    : TestRG
    Location             : centralus
    Id                   : /subscriptions/[Id]/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNIC
    Etag                 : W/"[Id]"
    ProvisioningState    : Succeeded
    Tags                 : 
    VirtualMachine       : {
                             "Id": "/subscriptions/[Id]/resourceGroups/TestRG/providers/Microsoft.Compute/virtualMachines/WindowsVM"
                           }
    IpConfigurations     : [
                             {
                               "Name": "ipconfig1",
                               "Etag": "W/\"[Id]\"",
                               "Id": "/subscriptions/[Id]/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNIC/ipConfigurations/ipconfig1",
                               "PrivateIpAddress": "192.168.1.101",
                               "PrivateIpAllocationMethod": "Dynamic",
                               "Subnet": {
                                 "Id": "/subscriptions/[Id]/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd"
                               },
                               "PublicIpAddress": {
                                 "Id": "/subscriptions/[Id]/resourceGroups/TestRG/providers/Microsoft.Network/publicIPAddresses/TestPIP"
                               },
                               "LoadBalancerBackendAddressPools": [],
                               "LoadBalancerInboundNatRules": [],
                               "ProvisioningState": "Succeeded"
                             }
                           ]
    DnsSettings          : {
                             "DnsServers": [],
                             "AppliedDnsServers": [],
                             "InternalDnsNameLabel": null,
                             "InternalFqdn": null
                           }
    EnableIPForwarding   : False
    NetworkSecurityGroup : null
    Primary              : True

## <a name="add-a-static-private-ip-address-to-a-network-interface"></a>Добавление статического частного IP-адреса в сетевой интерфейс
Чтобы добавить статический частный IP-адрес для виртуальной машины, созданной с помощью приведенного ранее скрипта, выполните следующие команды:

```powershell
$nic=Get-AzureRmNetworkInterface -Name TestNIC -ResourceGroupName TestRG
$nic.IpConfigurations[0].PrivateIpAllocationMethod = "Static"
$nic.IpConfigurations[0].PrivateIpAddress = "192.168.1.101"
Set-AzureRmNetworkInterface -NetworkInterface $nic
```

Не рекомендуем статически назначать виртуальной машине Azure частный IP-адрес в ее операционной системе за исключением ситуаций, когда это необходимо, например при [назначении нескольких IP-адресов виртуальной машине Windows](virtual-network-multiple-ip-addresses-powershell.md). Если вы будете вручную устанавливать частный IP-адрес в операционной системе, убедитесь, что он соответствует частному IP-адресу, назначенному [сетевому интерфейсу](virtual-network-network-interface-addresses.md#change-ip-address-settings) Azure. Иначе соединение с виртуальной машиной может быть потеряно. Ознакомьтесь с дополнительными сведениями о параметрах [частных IP-адресов](virtual-network-network-interface-addresses.md#private). Никогда не следует вручную назначать общедоступный IP-адрес для виртуальной машины Azure в ее операционной системе.

## <a name="change-the-allocation-method-for-a-private-ip-address-assigned-to-a-network-interface"></a>Изменение метода распределения для частного IP-адреса, назначенного сетевому интерфейсу

Частный IP-адрес назначается сетевой карте с помощью статического или динамического метода распределения. Динамические IP-адреса можно изменить после запуска виртуальной машины, ранее пребывавшей в состоянии "Остановлено" ("Освобождено"). Это может вызвать проблемы, если в виртуальной машине размещается служба, требующая один и тот же IP-адрес даже после перезапуска виртуальной машины, пребывающей в состоянии "Остановлено" ("Освобождено"). Статические IP-адреса хранятся до удаления виртуальной машины. Чтобы изменить метод распределения IP-адреса, выполните следующий скрипт, который изменяет метод распределения с динамического на статический. Если метод распределения для текущего частного IP-адреса *статический*, измените его на *динамический* перед выполнением скрипта.

```powershell
$RG = "TestRG"
$NIC_name = "testnic1"

$nic = Get-AzureRmNetworkInterface -ResourceGroupName $RG -Name $NIC_name
$nic.IpConfigurations[0].PrivateIpAllocationMethod = 'Static'
Set-AzureRmNetworkInterface -NetworkInterface $nic 
$IP = $nic.IpConfigurations[0].PrivateIpAddress

Write-Host "The allocation method is now set to"$nic.IpConfigurations[0].PrivateIpAllocationMethod"for the IP address" $IP"." -NoNewline
```

Если имя сетевой карты вам неизвестно, можно просмотреть список сетевых карт в группе ресурсов, введя следующую команду:

```powershell
Get-AzureRmNetworkInterface -ResourceGroupName $RG | Where-Object {$_.ProvisioningState -eq 'Succeeded'} 
```

## <a name="next-steps"></a>Дополнительная информация

Ознакомьтесь с дополнительными сведениями об управлении [параметрами IP-адресов](virtual-network-network-interface-addresses.md).
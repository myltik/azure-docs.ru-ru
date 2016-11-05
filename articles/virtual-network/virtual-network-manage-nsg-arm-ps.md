---
title: Управление группами безопасности сети с использованием PowerShell в Resource Manager | Microsoft Docs
description: Дополнительные сведения об управлении существующими группами безопасности сети с помощью PowerShell в Resource Manager
services: virtual-network
documentationcenter: na
author: jimdial
manager: carmonm
editor: ''
tags: azure-resource-manager

ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/14/2016
ms.author: jdial

---
# Управление группами безопасности сети с помощью Powershell
[!INCLUDE [virtual-network-manage-arm-selectors-include.md](../../includes/virtual-network-manage-nsg-arm-selectors-include.md)]

[!INCLUDE [virtual-network-manage-nsg-intro-include.md](../../includes/virtual-network-manage-nsg-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)]

Классическая модель развертывания.

[!INCLUDE [virtual-network-manage-nsg-arm-scenario-include.md](../../includes/virtual-network-manage-nsg-arm-scenario-include.md)]

[!INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## Извлечение информации
Вы можете просмотреть существующие группы безопасности сети, получить правила для существующей группы и узнать, с какими ресурсами она связана.

### Просмотр существующих групп безопасности сети
Чтобы просмотреть список всех групп безопасности сети в подписке, выполните командлет `Get-AzureRmNetworkSecurityGroup`, как показано ниже.

    Get-AzureRmNetworkSecurityGroup

Ожидаемый результат:

    Name                 : NSG-BackEnd
    ResourceGroupName    : RG-NSG
    Location             : westus
    Id                   : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/
                           Microsoft.Network/networkSecurityGroups/NSG-BackEnd
    Etag                 : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    ResourceGuid         : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
    ProvisioningState    : Succeeded
    Tags                 :                            
    SecurityRules        : [...]
    DefaultSecurityRules : [...]
    NetworkInterfaces    : [...]
    Subnets              : [...]

    Name                 : NSG-FrontEnd
    ResourceGroupName    : RG-NSG
    Location             : eastus
    Id                   : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/NRP-RG/providers/
                           Microsoft.Network/networkSecurityGroups/NSG-FrontEnd
    Etag                 : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    ResourceGuid         : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
    ProvisioningState    : Succeeded
    Tags                 : 
    SecurityRules        : [...]
    DefaultSecurityRules : [...]
    NetworkInterfaces    : [...]
    Subnets              : [...]

    Name                 : WEB1
    ResourceGroupName    : RG101
    Location             : eastus2
    Id                   : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG101/providers/M
                           icrosoft.Network/networkSecurityGroups/WEB1
    Etag                 : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    ResourceGuid         : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
    ProvisioningState    : Succeeded
    Tags                 : 
    SecurityRules        : [...]
    DefaultSecurityRules : [...]
    NetworkInterfaces    : [...]
    Subnets              : [...]


Чтобы просмотреть список групп безопасности сети в определенной группе ресурсов, выполните командлет `Get-AzureRmNetworkSecurityGroup`, как показано ниже.

    Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG

Ожидаемые выходные данные:

    Name                 : NSG-BackEnd
    ResourceGroupName    : RG-NSG
    Location             : westus
    Id                   : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/
                           Microsoft.Network/networkSecurityGroups/NSG-BackEnd
    Etag                 : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    ResourceGuid         : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
    ProvisioningState    : Succeeded
    Tags                 :                            
    SecurityRules        : [...]
    DefaultSecurityRules : [...]
    NetworkInterfaces    : [...]
    Subnets              : [...]

    Name                 : NSG-FrontEnd
    ResourceGroupName    : RG-NSG
    Location             : eastus
    Id                   : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/NRP-RG/providers/
                           Microsoft.Network/networkSecurityGroups/NSG-FrontEnd
    Etag                 : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    ResourceGuid         : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
    ProvisioningState    : Succeeded
    Tags                 : 
    SecurityRules        : [...]
    DefaultSecurityRules : [...]
    NetworkInterfaces    : [...]
    Subnets              : [...]

### Перечисление всех правил для группы безопасности сети
Чтобы просмотреть правила группы безопасности сети с именем **NSG-FrontEnd**, выполните командлет `Get-AzureRmNetworkSecurityGroup`, как показано ниже.

    Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG -Name NSG-FrontEnd | Select SecurityRules -ExpandProperty SecurityRules

Ожидаемые выходные данные:

    Name                     : rdp-rule
    Id                       : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/                           Microsoft.Network/networkSecurityGroups/NSG-FrontEnd/securityRules/rdp-rule
    Etag                     : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    ProvisioningState        : Succeeded
    Description              : Allow RDP
    Protocol                 : Tcp
    SourcePortRange          : *
    DestinationPortRange     : 3389
    SourceAddressPrefix      : Internet
    DestinationAddressPrefix : *
    Access                   : Allow
    Priority                 : 100
    Direction                : Inbound

    Name                     : web-rule
    Id                       : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/                           Microsoft.Network/networkSecurityGroups/NSG-FrontEnd/securityRules/web-rule
    Etag                     : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    ProvisioningState        : Succeeded
    Description              : Allow HTTP
    Protocol                 : Tcp
    SourcePortRange          : *
    DestinationPortRange     : 80
    SourceAddressPrefix      : Internet
    DestinationAddressPrefix : *
    Access                   : Allow
    Priority                 : 101
    Direction                : Inbound

> [!NOTE]
> Можно также использовать `Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG -Name "NSG-FrontEnd" | Select DefaultSecurityRules -ExpandProperty DefaultSecurityRules` для получения списка правил по умолчанию из группы безопасности сети **NSG-FrontEnd**.
> 
> 

### Просмотр связей для группы безопасности сети
Чтобы просмотреть, с какими ресурсами связана группа безопасности сети **NSG-FrontEnd**, выполните командлет `Get-AzureRmNetworkSecurityGroup`, как показано ниже.

    Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG -Name NSG-FrontEnd

Найдите свойства **NetworkInterfaces** и **Subnets**, как показано ниже:

    NetworkInterfaces    : []
    Subnets              : [
                             {
                               "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd",
                               "IpConfigurations": []
                             }
                           ]

В приведенном выше примере группа безопасности сети не связана с сетевыми адаптерами, но связана с подсетью **FrontEnd**.

## Управление правилами
Можно добавлять правила для существующей группы безопасности сети, изменять существующие правила и удалять их.

### Добавление правила
Чтобы добавить правило, разрешающее **входящий** трафик через порт **443** с любого компьютера в группу безопасности сети **NSG-FrontEnd**, выполните шаги ниже.

1. Выполните командлет `Get-AzureRmNetworkSecurityGroup`, чтобы извлечь существующую группу безопасности сети и сохранить ее в переменной, как показано ниже.
   
        $nsg = Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG `
            -Name NSG-FrontEnd
2. Выполните командлет `Add-AzureRmNetworkSecurityRuleConfig`, как показано ниже.
   
        Add-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg `
            -Name https-rule `
            -Description "Allow HTTPS" `
            -Access Allow `
            -Protocol Tcp `
            -Direction Inbound `
            -Priority 102 `
            -SourceAddressPrefix * `
            -SourcePortRange * `
            -DestinationAddressPrefix * `
            -DestinationPortRange 443
3. Чтобы сохранить изменения в группе, выполните командлет `Set-AzureRmNetworkSecurityGroup`, как показано ниже.
   
        Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg
   
    Ожидаемый результат, показаны только правила безопасности:
   
        Name                 : NSG-FrontEnd
        ...
        SecurityRules        : [
                                 {
                                   "Name": "rdp-rule",
                                   ...
                                 },
                                 {
                                   "Name": "web-rule",
                                   ...
                                 },
                                 {
                                   "Name": "https-rule",
                                   "Etag": "W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"",
                                   "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd/securityRules/https-rule",
                                   "Description": "Allow HTTPS",
                                   "Protocol": "Tcp",
                                   "SourcePortRange": "*",
                                   "DestinationPortRange": "443",
                                   "SourceAddressPrefix": "*",
                                   "DestinationAddressPrefix": "*",
                                   "Access": "Allow",
                                   "Priority": 102,
                                   "Direction": "Inbound",
                                   "ProvisioningState": "Succeeded"
                                 }
                               ]

### Изменение правила
Чтобы изменить ранее созданное правило, разрешающее входящий трафик только из **Интернета**, выполните шаги ниже.

1. Выполните командлет `Get-AzureRmNetworkSecurityGroup`, чтобы извлечь существующую группу безопасности сети и сохранить ее в переменной, как показано ниже.
   
        $nsg = Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG `
            -Name NSG-FrontEnd
2. Выполните командлет `Set-AzureRmNetworkSecurityRuleConfig`, как показано ниже.
   
        Set-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg `
            -Name https-rule `
            -Description "Allow HTTPS" `
            -Access Allow `
            -Protocol Tcp `
            -Direction Inbound `
            -Priority 102 `
            -SourceAddressPrefix * `
            -SourcePortRange Internet `
            -DestinationAddressPrefix * `
            -DestinationPortRange 443
3. Чтобы сохранить изменения в группе, выполните командлет `Set-AzureRmNetworkSecurityGroup`, как показано ниже.
   
        Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg
   
    Ожидаемый результат, показаны только правила безопасности:
   
        Name                 : NSG-FrontEnd
        ...
        SecurityRules        : [
                                 {
                                   "Name": "rdp-rule",
                                   ...
                                 },
                                 {
                                   "Name": "web-rule",
                                   ...
                                 },
                                 {
                                   "Name": "https-rule",
                                   "Etag": "W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"",
                                   "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd/securityRules/https-rule",
                                   "Description": "Allow HTTPS",
                                   "Protocol": "Tcp",
                                   "SourcePortRange": "*",
                                   "DestinationPortRange": "443",
                                   "SourceAddressPrefix": "Internet",
                                   "DestinationAddressPrefix": "*",
                                   "Access": "Allow",
                                   "Priority": 102,
                                   "Direction": "Inbound",
                                   "ProvisioningState": "Succeeded"
                                 }
                               ]

### Удаление правила
1. Выполните командлет `Get-AzureRmNetworkSecurityGroup`, чтобы извлечь существующую группу безопасности сети и сохранить ее в переменной, как показано ниже.
   
        $nsg = Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG `
            -Name NSG-FrontEnd
2. Выполните командлет `Remove-AzureRmNetworkSecurityRuleConfig`, как показано ниже.
   
        Remove-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg `
            -Name https-rule
3. Чтобы сохранить изменения в группе, выполните командлет `Set-AzureRmNetworkSecurityGroup`, как показано ниже.
   
        Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg
   
    Ожидаемый результат, показаны только правила безопасности. Обратите внимание, что **https-rule** больше не отображается:
   
        Name                 : NSG-FrontEnd
        ...
        SecurityRules        : [
                                 {
                                   "Name": "rdp-rule",
                                   ...
                                 },
                                 {
                                   "Name": "web-rule",
                                   ...
                                 }
                               ]

## Управление связями
Группу безопасности сети можно связать с сетевыми адаптерами и подсетями. Можно также отменить связь группы безопасности сети с любым ресурсом.

### Связывание группы безопасности сети с сетевым адаптером
Чтобы связать группу безопасности сети **NSG-FrontEnd** с сетевым адаптером **TestNICWeb1**, выполните шаги ниже.

1. Выполните командлет `Get-AzureRmNetworkSecurityGroup`, чтобы извлечь существующую группу безопасности сети и сохранить ее в переменной, как показано ниже.
   
        $nsg = Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG `
            -Name NSG-FrontEnd
2. Выполните командлет `Get-AzureRmNetworkInterface`, чтобы извлечь существующий сетевой адаптер и сохранить его в переменной, как показано ниже.
   
        $nic = Get-AzureRmNetworkInterface -ResourceGroupName RG-NSG `
            -Name TestNICWeb1
3. Задайте для свойства **NetworkSecurityGroup** переменной **NIC** значение переменной **NSG**, как показано ниже.
   
        $nic.NetworkSecurityGroup = $nsg
4. Чтобы сохранить изменения в адаптере, выполните командлет `Set-AzureRmNetworkInterface`, как показано ниже.
   
        Set-AzureRmNetworkInterface -NetworkInterface $nic
   
    Ожидаемый результат, показано только свойство **NetworkSecurityGroup**:
   
        NetworkSecurityGroup : {
                                 "SecurityRules": [],
                                 "DefaultSecurityRules": [],
                                 "NetworkInterfaces": [],
                                 "Subnets": [],
                                 "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd"
                               }

### Отмена связи с сетевым адаптером для группы безопасности сети
Чтобы отвязать группу безопасности сети **NSG-FrontEnd** от сетевого адаптера **TestNICWeb1**, выполните шаги ниже.

1. Выполните командлет `Get-AzureRmNetworkSecurityGroup`, чтобы извлечь существующую группу безопасности сети и сохранить ее в переменной, как показано ниже.
   
        $nsg = Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG `
            -Name NSG-FrontEnd
2. Выполните командлет `Get-AzureRmNetworkInterface`, чтобы извлечь существующий сетевой адаптер и сохранить его в переменной, как показано ниже.
   
        $nic = Get-AzureRmNetworkInterface -ResourceGroupName RG-NSG `
            -Name TestNICWeb1
3. Задайте для свойства **NetworkSecurityGroup** переменной **NIC** значение **$null**, как показано ниже.
   
        $nic.NetworkSecurityGroup = $null
4. Чтобы сохранить изменения в адаптере, выполните командлет `Set-AzureRmNetworkInterface`, как показано ниже.
   
        Set-AzureRmNetworkInterface -NetworkInterface $nic
   
    Ожидаемый результат, показано только свойство **NetworkSecurityGroup**:
   
        NetworkSecurityGroup : null

### Отмена связи с подсетью для группы безопасности сети
Чтобы отвязать группу безопасности сети **NSG-FrontEnd** от подсети **FrontEnd**, выполните шаги ниже.

1. Выполните командлет `Get-AzureRmVirtualNetwork`, чтобы извлечь существующую виртуальную сеть и сохранить ее в переменной, как показано ниже.
   
        $vnet = Get-AzureRmVirtualNetwork -ResourceGroupName RG-NSG `
            -Name TestVNet
2. Выполните командлет `Get-AzureRmVirtualNetworkSubnetConfig`, чтобы извлечь подсеть **FrontEnd** и сохранить ее в переменной, как показано ниже.
   
        $subnet = Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet `
            -Name FrontEnd 
3. Задайте для свойства **NetworkSecurityGroup** переменной **subnet** значение **$null**, как показано ниже.
   
        $subnet.NetworkSecurityGroup = $null
4. Чтобы сохранить изменения в подсети, выполните командлет `Set-AzureRmVirtualNetwork`, как показано ниже.
   
        Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
   
    Ожидаемый результат, показаны только свойства подсети **FrontEnd**. Обратите внимание, что свойства **NetworkSecurityGroup** нет:
   
            ...
            Subnets           : [
                                  {
                                    "Name": "FrontEnd",
                                    "Etag": "W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"",
                                    "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd",
                                    "AddressPrefix": "192.168.1.0/24",
                                    "IpConfigurations": [
                                      {
                                        "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/networkInterfaces/TestNICWeb2/ipConfigurations/ipconfig1"
                                      },
                                      {
                                        "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/networkInterfaces/TestNICWeb1/ipConfigurations/ipconfig1"
                                      }
                                    ],
                                    "ProvisioningState": "Succeeded"
                                  },
                                    ...
                                ]

### Связывание группы NSG с подсетью
Чтобы снова связать группу безопасности сети **NSG-FrontEnd** с подсетью **FrontEnd**, выполните шаги ниже.

1. Выполните командлет `Get-AzureRmVirtualNetwork`, чтобы извлечь существующую виртуальную сеть и сохранить ее в переменной, как показано ниже.
   
        $vnet = Get-AzureRmVirtualNetwork -ResourceGroupName RG-NSG `
            -Name TestVNet
2. Выполните командлет `Get-AzureRmVirtualNetworkSubnetConfig`, чтобы извлечь подсеть **FrontEnd** и сохранить ее в переменной, как показано ниже.
   
        $subnet = Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet `
            -Name FrontEnd 
3. Выполните командлет `Get-AzureRmNetworkSecurityGroup`, чтобы извлечь существующую группу безопасности сети и сохранить ее в переменной, как показано ниже.
   
        $nsg = Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG `
            -Name NSG-FrontEnd
4. Задайте для свойства **NetworkSecurityGroup** переменной **subnet** значение **$null**, как показано ниже.
   
        $subnet.NetworkSecurityGroup = $nsg
5. Чтобы сохранить изменения в подсети, выполните командлет `Set-AzureRmVirtualNetwork`, как показано ниже.
   
        Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
   
    Ожидаемый результат, показано только свойство **NetworkSecurityGroup** подсети **FrontEnd**:
   
        ...
        "NetworkSecurityGroup": {
                                  "SecurityRules": [],
                                  "DefaultSecurityRules": [],
                                  "NetworkInterfaces": [],
                                  "Subnets": [],
                                  "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd"
                                }
        ...

## Удаление группы NSG
Группу безопасности сети можно удалить только в том случае, если она не связана с ресурсами. Чтобы удалить группу безопасности сети, выполните следующие действия.

1. Чтобы проверить ресурсы, связанные с группой, выполните команду `azure network nsg show`, как показано в разделе [Просмотр связей для группы безопасности сети](#View-NSGs-associations).
2. Если группа связана с сетевыми адаптерами, выполните `azure network nic set`, как показано в разделе [Отмена связи с сетевым адаптером для группы безопасности сети](#Dissociate-an-NSG-from-a-NIC), для каждого сетевого адаптера.
3. Если группа связана с подсетью, выполните `azure network vnet subnet set`, как показано в разделе [Отмена связи с подсетью для группы безопасности сети](#Dissociate-an-NSG-from-a-subnet), для каждой подсети.
4. Чтобы удалить группу безопасности сети, выполните командлет `Remove-AzureRmNetworkSecurityGroup`, как показано ниже.
   
        Remove-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG -Name NSG-FrontEnd -Force
   
   > [!NOTE]
   > Параметр **-Force** гарантирует, что удаление не потребуется подтверждать.
   > 
   > 

## Дальнейшие действия
* [Включите ведение журнала](virtual-network-nsg-manage-log.md) для групп безопасности сети.

<!---HONumber=AcomDC_0810_2016-->
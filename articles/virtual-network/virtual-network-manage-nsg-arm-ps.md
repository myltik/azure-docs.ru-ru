---
title: Управление группами безопасности сети с помощью Azure PowerShell | Документация Майкрософт
description: Узнайте, как управлять группами безопасности сети с помощью PowerShell.
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
tags: azure-resource-manager
ms.assetid: 3706ce6c-d9ae-46cb-a048-f0a4e84dc5cc
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/14/2016
ms.author: jdial
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ca7f4926ca4edf9d20612aca74f6ae5f0ed847b3
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/11/2018
---
# <a name="manage-network-security-groups-using-powershell"></a>Управление группами безопасности сети с помощью PowerShell

[!INCLUDE [virtual-network-manage-arm-selectors-include.md](../../includes/virtual-network-manage-nsg-arm-selectors-include.md)]

[!INCLUDE [virtual-network-manage-nsg-intro-include.md](../../includes/virtual-network-manage-nsg-intro-include.md)]

> [!NOTE]
> В Azure предлагаются две модели развертывания для создания ресурсов и работы с ними: [модель Resource Manager и классическая модель](../resource-manager-deployment-model.md). В этой статье описывается использование модели развертывания c помощью Resource Manager. Для большинства новых развертываний мы рекомендуем использовать эту модель вместо классической.
>

[!INCLUDE [virtual-network-manage-nsg-arm-scenario-include.md](../../includes/virtual-network-manage-nsg-arm-scenario-include.md)]

[!INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## <a name="retrieve-information"></a>Извлечение информации
Вы можете просмотреть существующие группы безопасности сети, получить правила для существующей группы и узнать, с какими ресурсами она связана.

### <a name="view-existing-nsgs"></a>Просмотр существующих групп безопасности сети
Чтобы просмотреть все существующие группы безопасности сети в подписке, выполните командлет `Get-AzureRmNetworkSecurityGroup`.

Ожидаемый результат:

    Name                 : NSG-BackEnd
    ResourceGroupName    : RG-NSG
    Location             : westus
    Id                   : /subscriptions/[Subscription Id]/resourceGroups/RG-NSG/providers/
                           Microsoft.Network/networkSecurityGroups/NSG-BackEnd
    Etag                 : W/"[Id]"
    ResourceGuid         : [Id]
    ProvisioningState    : Succeeded
    Tags                 :                            
    SecurityRules        : [...]
    DefaultSecurityRules : [...]
    NetworkInterfaces    : [...]
    Subnets              : [...]

    Name                 : NSG-FrontEnd
    ResourceGroupName    : RG-NSG
    Location             : eastus
    Id                   : /subscriptions/[Subscription Id]/resourceGroups/NRP-RG/providers/
                           Microsoft.Network/networkSecurityGroups/NSG-FrontEnd
    Etag                 : W/"[Id]"
    ResourceGuid         : [Id]
    ProvisioningState    : Succeeded
    Tags                 : 
    SecurityRules        : [...]
    DefaultSecurityRules : [...]
    NetworkInterfaces    : [...]
    Subnets              : [...]

    Name                 : WEB1
    ResourceGroupName    : RG101
    Location             : eastus2
    Id                   : /subscriptions/[Subscription Id]/resourceGroups/RG101/providers/M
                           icrosoft.Network/networkSecurityGroups/WEB1
    Etag                 : W/"[Id]"
    ResourceGuid         : [Id]
    ProvisioningState    : Succeeded
    Tags                 : 
    SecurityRules        : [...]
    DefaultSecurityRules : [...]
    NetworkInterfaces    : [...]
    Subnets              : [...]


Чтобы просмотреть список групп безопасности сети в определенной группе ресурсов, выполните командлет `Get-AzureRmNetworkSecurityGroup`.

Ожидаемые выходные данные:

    Name                 : NSG-BackEnd
    ResourceGroupName    : RG-NSG
    Location             : westus
    Id                   : /subscriptions/[Subscription Id]/resourceGroups/RG-NSG/providers/
                           Microsoft.Network/networkSecurityGroups/NSG-BackEnd
    Etag                 : W/"[Id]"
    ResourceGuid         : [Id]
    ProvisioningState    : Succeeded
    Tags                 :                            
    SecurityRules        : [...]
    DefaultSecurityRules : [...]
    NetworkInterfaces    : [...]
    Subnets              : [...]

    Name                 : NSG-FrontEnd
    ResourceGroupName    : RG-NSG
    Location             : eastus
    Id                   : /subscriptions/[Subscription Id]/resourceGroups/NRP-RG/providers/
                           Microsoft.Network/networkSecurityGroups/NSG-FrontEnd
    Etag                 : W/"[Id]"
    ResourceGuid         : [Id]
    ProvisioningState    : Succeeded
    Tags                 : 
    SecurityRules        : [...]
    DefaultSecurityRules : [...]
    NetworkInterfaces    : [...]
    Subnets              : [...]

### <a name="list-all-rules-for-an-nsg"></a>Перечисление всех правил для группы безопасности сети
Чтобы просмотреть правила группы безопасности сети с именем **NSG-FrontEnd**, введите следующую команду:

```powershell
Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG -Name NSG-FrontEnd | Select SecurityRules -ExpandProperty SecurityRules
```

Ожидаемые выходные данные:

    Name                     : rdp-rule
    Id                       : /subscriptions/[Subscription Id]/resourceGroups/RG-NSG/providers/                           Microsoft.Network/networkSecurityGroups/NSG-FrontEnd/securityRules/rdp-rule
    Etag                     : W/"[Id]"
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
    Id                       : /subscriptions/[Subscription Id]/resourceGroups/RG-NSG/providers/                           Microsoft.Network/networkSecurityGroups/NSG-FrontEnd/securityRules/web-rule
    Etag                     : W/"[Id]"
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

### <a name="view-nsgs-associations"></a>Просмотр связей для группы безопасности сети
Чтобы просмотреть, к каким ресурсам привязана группа безопасности сети **NSG-FrontEnd**, выполните следующую команду:

```powershell
Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG -Name NSG-FrontEnd
```

Найдите свойства **NetworkInterfaces** и **Subnets**, как показано ниже.

    NetworkInterfaces    : []
    Subnets              : [
                             {
                               "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd",
                               "IpConfigurations": []
                             }
                           ]

В предыдущем примере группа безопасности сети не привязана к сетевым интерфейсам, но связана с подсетью **FrontEnd**.

## <a name="manage-rules"></a>Управление правилами
Можно добавлять правила для существующей группы безопасности сети, изменять существующие правила и удалять их.

### <a name="add-a-rule"></a>Добавление правила
Чтобы добавить правило, разрешающее **входящий** трафик через порт **443** с любого компьютера в группу безопасности сети **NSG-FrontEnd**, выполните следующие действия:

1. Чтобы получить существующую группу безопасности сети и сохранить ее в переменной, выполните следующую команду:

    ```powershell   
    $nsg = Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG -Name NSG-FrontEnd
    ```

2. Чтобы добавить правило к группе безопасности сети, выполните следующую команду:

    ```powershell
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
    ```

3. Чтобы сохранить изменения в группе безопасности сети, выполните следующую команду:

    ```powershell
    Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg
    ```
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
                                   "Etag": "W/\"[Id]\"",
                                   "Id": "/subscriptions/[Subscription Id]/resourceGroups/RG-NSG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd/securityRules/https-rule",
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

### <a name="change-a-rule"></a>Изменение правила
Чтобы изменить ранее созданное правило, разрешающее входящий трафик только из **Интернета** , выполните шаги ниже.

1. Чтобы получить существующую группу безопасности сети и сохранить ее в переменной, выполните следующую команду:

    ```powershell 
    $nsg = Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG -Name NSG-FrontEnd
    ```

2. Выполните следующую команду с новыми параметрами правила:

    ```powershell
    Set-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg `
    -Name https-rule `
    -Description "Allow HTTPS" `
    -Access Allow `
    -Protocol Tcp `
    -Direction Inbound `
    -Priority 102 `
    -SourceAddressPrefix Internet `
    -SourcePortRange * `
    -DestinationAddressPrefix * `
    -DestinationPortRange 443
    ```

3. Чтобы сохранить изменения в группе безопасности сети, выполните следующую команду:

    ```powershell
    Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg
    ```

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
                                   "Etag": "W/\"[Id]\"",
                                   "Id": "/subscriptions/[Subscription Id]/resourceGroups/RG-NSG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd/securityRules/https-rule",
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

### <a name="delete-a-rule"></a>Удаление правила
1. Чтобы получить существующую группу безопасности сети и сохранить ее в переменной, выполните следующую команду:

    ```powershell
    $nsg = Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG -Name NSG-FrontEnd
    ```

2. Чтобы удалить правило из группы безопасности сети, выполните следующую команду:

    ```powershell
    Remove-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg -Name https-rule
    ```

3. Чтобы сохранить изменения в группе безопасности сети, выполните следующую команду:

    ```powershell
    Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg
    ```

    Ожидаемый результат, показаны только правила безопасности. Обратите внимание, что **https-rule** больше не отображается.
   
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

## <a name="manage-associations"></a>Управление связями
Группу безопасности сети можно связать с сетевыми адаптерами и подсетями. Можно также отменить связь группы безопасности сети с любым ресурсом.

### <a name="associate-an-nsg-to-a-nic"></a>Связывание группы безопасности сети с сетевым адаптером
Чтобы привязать группу безопасности сети **NSG-FrontEnd** к сетевому интерфейсу **TestNICWeb1**, выполните следующие действия:

1. Чтобы получить существующую группу безопасности сети и сохранить ее в переменной, выполните следующую команду:

    ```powershell
    $nsg = Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG -Name NSG-FrontEnd
    ```

2. Чтобы получить сетевой интерфейс и сохранить его в переменной, выполните следующую команду:

    ```powershell
    $nic = Get-AzureRmNetworkInterface -ResourceGroupName RG-NSG -Name TestNICWeb1
    ```

3. Задайте для свойства **NetworkSecurityGroup** переменной **NIC** значение переменной **NSG** с помощью следующей команды:

    ```powershell
    $nic.NetworkSecurityGroup = $nsg
    ```

4. Чтобы сохранить изменения в сетевом интерфейсе, выполните следующую команду:

    ```powershell
    Set-AzureRmNetworkInterface -NetworkInterface $nic
    ```
   
    Ожидаемый результат, показано только свойство **NetworkSecurityGroup** :
   
        NetworkSecurityGroup : {
                                 "SecurityRules": [],
                                 "DefaultSecurityRules": [],
                                 "NetworkInterfaces": [],
                                 "Subnets": [],
                                 "Id": "/subscriptions/[Subscription Id]/resourceGroups/RG-NSG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd"
                               }

### <a name="dissociate-an-nsg-from-a-nic"></a>Отмена связи с сетевым адаптером для группы безопасности сети
Чтобы отменить связь группы безопасности сети **NSG-FrontEnd** с сетевым интерфейсом **TestNICWeb1**, выполните следующие действия:

1. Чтобы получить сетевой интерфейс и сохранить его в переменной, выполните следующую команду:

    ```powershell
    $nic = Get-AzureRmNetworkInterface -ResourceGroupName RG-NSG -Name TestNICWeb1
    ```

2. Задайте для свойства **NetworkSecurityGroup** переменной **NIC** значение **$null** с помощью следующей команды:

    ```powershell
    $nic.NetworkSecurityGroup = $null
    ```

3. Чтобы сохранить изменения в сетевом интерфейсе, выполните следующую команду:

    ```powershell
    Set-AzureRmNetworkInterface -NetworkInterface $nic
    ```
   
    Ожидаемый результат, показано только свойство **NetworkSecurityGroup** :
   
        NetworkSecurityGroup : null

### <a name="dissociate-an-nsg-from-a-subnet"></a>Отмена связи с подсетью для группы безопасности сети
Чтобы отменить связь группы безопасности сети **NSG-FrontEnd** с подсетью **FrontEnd**, выполните следующие действия:

1. Чтобы получить существующую виртуальную сеть и сохранить ее в переменной, выполните следующую команду:

    ```powershell
    $vnet = Get-AzureRmVirtualNetwork -ResourceGroupName RG-NSG -Name TestVNet
    ```

2. Чтобы получить подсеть **FrontEnd** и сохранить ее в переменной, выполните следующую команду:

    ```powershell
    $subnet = Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name FrontEnd
    ```
 
3. Задайте для свойства **NetworkSecurityGroup** переменной **subnet** значение **$null** с помощью следующей команды:

    ```powershell
    $subnet.NetworkSecurityGroup = $null
    ```

4. Чтобы сохранить изменения в подсети, выполните следующую команду:

    ```powershell
    Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
    ```

    Ожидаемый результат, показаны только свойства подсети **FrontEnd** . Обратите внимание, что свойства **NetworkSecurityGroup**нет:
   
            ...
            Subnets           : [
                                  {
                                    "Name": "FrontEnd",
                                    "Etag": "W/\"[Id]\"",
                                    "Id": "/subscriptions/[Subscription Id]/resourceGroups/RG-NSG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd",
                                    "AddressPrefix": "192.168.1.0/24",
                                    "IpConfigurations": [
                                      {
                                        "Id": "/subscriptions/[Subscription Id]/resourceGroups/RG-NSG/providers/Microsoft.Network/networkInterfaces/TestNICWeb2/ipConfigurations/ipconfig1"
                                      },
                                      {
                                        "Id": "/subscriptions/[Subscription Id]/resourceGroups/RG-NSG/providers/Microsoft.Network/networkInterfaces/TestNICWeb1/ipConfigurations/ipconfig1"
                                      }
                                    ],
                                    "ProvisioningState": "Succeeded"
                                  },
                                    ...
                                ]

### <a name="associate-an-nsg-to-a-subnet"></a>Связывание группы NSG с подсетью
Чтобы привязать группу безопасности сети **NSG-FrontEnd** к подсети **FronEnd** снова, выполните следующие действия:

1. Чтобы получить существующую виртуальную сеть и сохранить ее в переменной, выполните следующую команду:

    ```powershell
    $vnet = Get-AzureRmVirtualNetwork -ResourceGroupName RG-NSG -Name TestVNet
    ```

2. Чтобы получить подсеть **FrontEnd** и сохранить ее в переменной, выполните следующую команду:

    ```powershell
    $subnet = Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name FrontEnd
    ```
 
3. Чтобы получить существующую группу безопасности сети и сохранить ее в переменной, выполните следующую команду:

    ```powershell
    $nsg = Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG -Name NSG-FrontEnd
    ```

4. Чтобы задать для свойства **NetworkSecurityGroup** переменной **subnet** значение **$null**, выполните следующую команду:

    ```powershell
    $subnet.NetworkSecurityGroup = $nsg
    ```

5. Чтобы сохранить изменения в подсети, выполните следующую команду:

    ```powershell
    Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
    ```

    Ожидаемый результат, показано только свойство **NetworkSecurityGroup** для подсети **FrontEnd**.
   
        ...
        "NetworkSecurityGroup": {
                                  "SecurityRules": [],
                                  "DefaultSecurityRules": [],
                                  "NetworkInterfaces": [],
                                  "Subnets": [],
                                  "Id": "/subscriptions/[Subscription Id]/resourceGroups/RG-NSG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd"
                                }
        ...

## <a name="delete-an-nsg"></a>Удаление группы NSG
Группу безопасности сети можно удалить только в том случае, если она не связана с ресурсами. Чтобы удалить группу безопасности сети, выполните следующие действия.

1. Чтобы проверить ресурсы, связанные с группой, выполните команду `azure network nsg show` , как показано в разделе [Просмотр связей для группы безопасности сети](#View-NSGs-associations).
2. Если группа связана с сетевыми адаптерами, выполните `azure network nic set` , как показано в разделе [Отмена связи с сетевым адаптером для группы безопасности сети](#Dissociate-an-NSG-from-a-NIC) , для каждого сетевого адаптера. 
3. Если группа связана с подсетью, выполните `azure network vnet subnet set` , как показано в разделе [Отмена связи с подсетью для группы безопасности сети](#Dissociate-an-NSG-from-a-subnet) , для каждой подсети.
4. Чтобы удалить группу безопасности сети, выполните следующую команду:

    ```powershell
    Remove-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG -Name NSG-FrontEnd -Force
    ```
   
   > [!NOTE]
   > Параметр `-Force` гарантирует, что удаление не потребуется подтверждать.
   > 

## <a name="next-steps"></a>Дополнительная информация
* [Включите ведение журнала](virtual-network-nsg-manage-log.md) для групп безопасности сети.


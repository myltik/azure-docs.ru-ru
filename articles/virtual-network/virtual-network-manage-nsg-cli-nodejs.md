---
title: "Управление группами безопасности сети (Azure CLI 1.0) | Документация Майкрософт"
description: "Узнайте, как управлять группами безопасности сети с помощью интерфейса командной строки Azure (CLI) версии 1.0."
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/21/2017
ms.author: jdial
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 63f2f6dde56c1b5c4b3ad2591700f43f6542874d
ms.openlocfilehash: 2e53c3ff2ffbef95d6b72ca6afb3b4de377f0389
ms.contentlocale: ru-ru
ms.lasthandoff: 02/28/2017

---
# <a name="manage-network-security-groups-using-the-azure-cli-10"></a>Управление группами безопасности сети с помощью Azure CLI 1.0

## <a name="cli-versions-to-complete-the-task"></a>Версии интерфейса командной строки для выполнения задачи 

Вы можете выполнить задачу, используя одну из следующих версий интерфейса командной строки. 

- [Azure CLI 1.0](#View-existing-NSGs) — интерфейс командной строки для классической модели развертывания и модели развертывания Resource Manager. 
- [Azure CLI 2.0](virtual-network-manage-nsg-arm-cli.md) — это интерфейс командной строки нового поколения для модели развертывания Resource Manager (описывается в этой статье).

[!INCLUDE [virtual-network-manage-nsg-intro-include.md](../../includes/virtual-network-manage-nsg-intro-include.md)]

> [!NOTE]
> В Azure предлагаются две модели развертывания для создания ресурсов и работы с ними: [модель Resource Manager и классическая модель](../azure-resource-manager/resource-manager-deployment-model.md). В этой статье описывается использование модели развертывания c помощью Resource Manager. Для большинства новых развертываний мы рекомендуем использовать эту модель вместо классической.
> 

[!INCLUDE [virtual-network-manage-nsg-arm-scenario-include.md](../../includes/virtual-network-manage-nsg-arm-scenario-include.md)]

[!INCLUDE [azure-cli-prerequisites-include.md](../../includes/azure-cli-prerequisites-include.md)]

## <a name="retrieve-information"></a>Извлечение информации
Вы можете просмотреть существующие группы безопасности сети, получить правила для существующей группы и узнать, с какими ресурсами она связана.

### <a name="view-existing-nsgs"></a>Просмотр существующих групп безопасности сети
Чтобы просмотреть список групп безопасности сети в определенной группе ресурсов, выполните команду `azure network nsg list`, как показано ниже.

```azurecli
azure network nsg list --resource-group RG-NSG
```

Ожидаемые выходные данные:

    info:    Executing command network nsg list
    + Getting the network security groups
    data:    Name          Location
    data:    ------------  --------
    data:    NSG-BackEnd   westus
    data:    NSG-FrontEnd  westus
    info:    network nsg list command OK

### <a name="list-all-rules-for-an-nsg"></a>Перечисление всех правил для группы безопасности сети
Чтобы просмотреть правила группы безопасности сети с именем **NSG-FrontEnd`azure network nsg show`, выполните команду **, как показано ниже. 

```azurecli
azure network nsg show --resource-group RG-NSG --name NSG-FrontEnd
```

Ожидаемые выходные данные:

    info:    Executing command network nsg show
    + Looking up the network security group "NSG-FrontEnd"
    data:    Id                              : /subscriptions/[Subscription Id]/resourceGroups/RG-NSG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd
    data:    Name                            : NSG-FrontEnd
    data:    Type                            : Microsoft.Network/networkSecurityGroups
    data:    Location                        : westus
    data:    Provisioning state              : Succeeded
    data:    Tags                            : displayName=NSG - Front End
    data:    Security group rules:
    data:    Name                           Source IP          Source Port  Destination IP  Destination Port  Protocol  Direction  Access  Priority
    data:    -----------------------------  -----------------  -----------  --------------  ----------------  --------  ---------  ------  --------
    data:    rdp-rule                       Internet           *            *               3389              Tcp       Inbound    Allow   100
    data:    web-rule                       Internet           *            *               80                Tcp       Inbound    Allow   101
    data:    AllowVnetInBound               VirtualNetwork     *            VirtualNetwork  *                 *         Inbound    Allow   65000
    data:    AllowAzureLoadBalancerInBound  AzureLoadBalancer  *            *               *                 *         Inbound    Allow   65001
    data:    DenyAllInBound                 *                  *            *               *                 *         Inbound    Deny    65500
    data:    AllowVnetOutBound              VirtualNetwork     *            VirtualNetwork  *                 *         Outbound   Allow   65000
    data:    AllowInternetOutBound          *                  *            Internet        *                 *         Outbound   Allow   65001
    data:    DenyAllOutBound                *                  *            *               *                 *         Outbound   Deny    65500
    info:    network nsg show command OK

> [!NOTE]
> Можно также использовать `azure network nsg rule list --resource-group RG-NSG --nsg-name NSG-FrontEnd` для получения списка правил из группы безопасности сети **NSG-FrontEnd**.
>

### <a name="view-nsg-associations"></a>Просмотр связей для группы безопасности сети

Чтобы просмотреть, с какими ресурсами связана группа безопасности сети **NSG-FrontEnd`azure network nsg show`, выполните команду **, как показано ниже. Обратите внимание, что единственная разница — использование параметра **--json** .

```azurecli
azure network nsg show --resource-group RG-NSG --name NSG-FrontEnd --json
```

Найдите свойства **networkInterfaces** и **subnets**, как показано ниже.

    "networkInterfaces": [],
    ...
    "subnets": [
        {
            "id": "/subscriptions/[Subscription Id]/resourceGroups/RG-NSG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd"
        }
    ],
    ...

В приведенном выше примере группа безопасности сети не связана с сетевыми адаптерами, но связана с подсетью **FrontEnd**.

## <a name="manage-rules"></a>Управление правилами
Можно добавлять правила для существующей группы безопасности сети, изменять существующие правила и удалять их.

### <a name="add-a-rule"></a>Добавление правила
Чтобы добавить правило, разрешающее **входящий** трафик через порт **443** с любого компьютера в группу безопасности сети **NSG-FrontEnd**, выполните следующую команду:

```azurecli
azure network nsg rule create --resource-group RG-NSG \
    --nsg-name NSG-FrontEnd \
    --name allow-https \
    --description "Allow access to port 443 for HTTPS" \
    --protocol Tcp \
    --source-address-prefix * \
    --source-port-range * \
    --destination-address-prefix * \
    --destination-port-range 443 \
    --access Allow \
    --priority 102 \
    --direction Inbound
```

Ожидаемые выходные данные:

    info:    Executing command network nsg rule create
    + Looking up the network security rule "allow-https"
    + Creating a network security rule "allow-https"
    + Looking up the network security group "NSG-FrontEnd"
    data:    Id                              : /subscriptions/[Subscription Id]/resourceGroups/RG-NSG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd/securityRules/allow-https
    data:    Name                            : allow-https
    data:    Type                            : Microsoft.Network/networkSecurityGroups/securityRules
    data:    Provisioning state              : Succeeded
    data:    Description                     : Allow access to port 443 for HTTPS
    data:    Source IP                       : *
    data:    Source Port                     : *
    data:    Destination IP                  : *
    data:    Destination Port                : 443
    data:    Protocol                        : Tcp
    data:    Direction                       : Inbound
    data:    Access                          : Allow
    data:    Priority                        : 102
    info:    network nsg rule create command OK

### <a name="change-a-rule"></a>Изменение правила
Чтобы изменить созданное ранее правило, разрешающее входящий трафик только из **Интернета**, выполните следующую команду:

```azurecli
azure network nsg rule set --resource-group RG-NSG \
    --nsg-name NSG-FrontEnd \
    --name allow-https \
    --source-address-prefix Internet
```

Ожидаемые выходные данные:

    info:    Executing command network nsg rule set
    + Looking up the network security group "NSG-FrontEnd"
    + Setting a network security rule "allow-https"
    + Looking up the network security group "NSG-FrontEnd"
    data:    Id                              : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd/securityRules/allow-https
    data:    Name                            : allow-https
    data:    Type                            : Microsoft.Network/networkSecurityGroups/securityRules
    data:    Provisioning state              : Succeeded
    data:    Description                     : Allow access to port 443 for HTTPS
    data:    Source IP                       : Internet
    data:    Source Port                     : *
    data:    Destination IP                  : *
    data:    Destination Port                : 443
    data:    Protocol                        : Tcp
    data:    Direction                       : Inbound
    data:    Access                          : Allow
    data:    Priority                        : 102
    info:    network nsg rule set command OK

### <a name="delete-a-rule"></a>Удаление правила
Чтобы удалить созданное ранее правило, выполните следующую команду:

```azurecli
azure network nsg rule delete --resource-group RG-NSG \
    --nsg-name NSG-FrontEnd \
    --name allow-https \
    --quiet
```

> [!NOTE]
> Параметр `--quiet` гарантирует, что удаление не потребуется подтверждать.
>

Ожидаемые выходные данные:

    info:    Executing command network nsg rule delete
    + Looking up the network security group "NSG-FrontEnd"
    + Deleting network security rule "allow-https"
    info:    network nsg rule delete command OK

## <a name="manage-associations"></a>Управление связями
Группу безопасности сети можно связать с сетевыми адаптерами и подсетями. Можно также отменить связь группы безопасности сети с любым ресурсом.

### <a name="associate-an-nsg-to-a-nic"></a>Связывание группы безопасности сети с сетевым адаптером
Чтобы привязать группу безопасности сети **NSG-FrontEnd** к сетевому интерфейсу **TestNICWeb1**, выполните следующую команду:

```azurecli
azure network nic set --resource-group RG-NSG \
    --name TestNICWeb1 \
    --network-security-group-name NSG-FrontEnd
```

Ожидаемые выходные данные:

    info:    Executing command network nic set
    + Looking up the network interface "TestNICWeb1"
    + Looking up the network security group "NSG-FrontEnd"
    + Updating network interface "TestNICWeb1"
    + Looking up the network interface "TestNICWeb1"
    data:    Id                              : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/networkInterfaces/TestNICWeb1
    data:    Name                            : TestNICWeb1
    data:    Type                            : Microsoft.Network/networkInterfaces
    data:    Location                        : westus
    data:    Provisioning state              : Succeeded
    data:    MAC address                     : 00-0D-3A-30-A1-F8
    data:    Enable IP forwarding            : false
    data:    Tags                            : displayName=NetworkInterfaces - Web
    data:    Network security group          : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd
    data:    Virtual machine                 : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Compute/virtualMachines/Web1
    data:    IP configurations:
    data:      Name                          : ipconfig1
    data:      Provisioning state            : Succeeded
    data:      Public IP address             : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/publicIPAddresses/TestPIPWeb1
    data:      Private IP address            : 192.168.1.5
    data:      Private IP Allocation Method  : Dynamic
    data:      Subnet                        : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd
    data:
    info:    network nic set command OK

### <a name="dissociate-an-nsg-from-a-nic"></a>Отмена связи с сетевым адаптером для группы безопасности сети

Чтобы отменить связь между группой безопасности сети **NSG-FrontEnd** и сетевым интерфейсом **TestNICWeb1**, выполните следующую команду:

```azurecli
azure network nic set --resource-group RG-NSG --name TestNICWeb1 --network-security-group-id ""
```

> [!NOTE]
> Обратите внимание на значение "" (пустое) в параметре `network-security-group-id`. Это позволяет удалить связь с группой. Нельзя сделать то же самое с параметром `network-security-group-name`.
> 

Ожидаемый результат:

    info:    Executing command network nic set
    + Looking up the network interface "TestNICWeb1"
    + Updating network interface "TestNICWeb1"
    + Looking up the network interface "TestNICWeb1"
    data:    Id                              : /subscriptions/[Subscription Id]/resourceGroups/RG-NSG/providers/Microsoft.Network/networkInterfaces/TestNICWeb1
    data:    Name                            : TestNICWeb1
    data:    Type                            : Microsoft.Network/networkInterfaces
    data:    Location                        : westus
    data:    Provisioning state              : Succeeded
    data:    MAC address                     : 00-0D-3A-30-A1-F8
    data:    Enable IP forwarding            : false
    data:    Tags                            : displayName=NetworkInterfaces - Web
    data:    Virtual machine                 : /subscriptions/[Subscription Id]/resourceGroups/RG-NSG/providers/Microsoft.Compute/virtualMachines/Web1
    data:    IP configurations:
    data:      Name                          : ipconfig1
    data:      Provisioning state            : Succeeded
    data:      Public IP address             : /subscriptions/[Subscription Id]/resourceGroups/RG-NSG/providers/Microsoft.Network/publicIPAddresses/TestPIPWeb1
    data:      Private IP address            : 192.168.1.5
    data:      Private IP Allocation Method  : Dynamic
    data:      Subnet                        : /subscriptions/[Subscription Id]/resourceGroups/RG-NSG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd
    data:
    info:    network nic set command OK

### <a name="dissociate-an-nsg-from-a-subnet"></a>Отмена связи с подсетью для группы безопасности сети
Чтобы отменить связь между группой безопасности сети **NSG-FrontEnd** и подсетью **FrontEnd**, выполните следующую команду:

```azurecli
azure network vnet subnet set --resource-group RG-NSG \
    --vnet-name TestVNet \
    --name FrontEnd \
    --network-security-group-id ""
```

Ожидаемые выходные данные:

    info:    Executing command network vnet subnet set
    + Looking up the subnet "FrontEnd"
    + Setting subnet "FrontEnd"
    + Looking up the subnet "FrontEnd"
    data:    Id                              : /subscriptions/[Subscription Id]/resourceGroups/RG-NSG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd
    data:    Type                            : Microsoft.Network/virtualNetworks/subnets
    data:    ProvisioningState               : Succeeded
    data:    Name                            : FrontEnd
    data:    Address prefix                  : 192.168.1.0/24
    data:    IP configurations:
    data:      /subscriptions/[Subscription Id]/resourceGroups/RG-NSG/providers/Microsoft.Network/networkInterfaces/TestNICWeb2/ipConfigurations/ipconfig1
    data:      /subscriptions/[Subscription Id]/resourceGroups/RG-NSG/providers/Microsoft.Network/networkInterfaces/TestNICWeb1/ipConfigurations/ipconfig1
    data:
    info:    network vnet subnet set command OK

### <a name="associate-an-nsg-to-a-subnet"></a>Связывание группы NSG с подсетью
Чтобы снова связать группу безопасности сети **NSG-FrontEnd** с подсетью **FrontEnd**, выполните следующую команду:

```azurecli
azure network vnet subnet set --resource-group RG-NSG \
    --vnet-name TestVNet \
    --name FrontEnd \
    --network-security-group-name NSG-FronEnd
```

> [!NOTE]
> Приведенная выше команда работает только потому, что группа безопасности сети **NSG-FrontEnd** находится в той же группе ресурсов, что и виртуальная сеть **TestVNet**. Если группа безопасности сети находится в другой группе ресурсов, необходимо использовать вместо этого параметр `--network-security-group-id` и указать полный идентификатор для группы безопасности сети. Идентификатор можно получить, выполнив команду `azure network nsg show --resource-group RG-NSG --name NSG-FrontEnd --json` и найдя свойство **id**. 
> 

Ожидаемые выходные данные:

        info:    Executing command network vnet subnet set
        + Looking up the subnet "FrontEnd"
        + Looking up the network security group "NSG-FrontEnd"
        + Setting subnet "FrontEnd"
        + Looking up the subnet "FrontEnd"
        data:    Id                              : /subscriptions/[Subscription Id]/resourceGroups/RG-NSG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd
        data:    Type                            : Microsoft.Network/virtualNetworks/subnets
        data:    ProvisioningState               : Succeeded
        data:    Name                            : FrontEnd
        data:    Address prefix                  : 192.168.1.0/24
        data:    Network security group          : [object Object]
        data:    IP configurations:
        data:      /subscriptions/[Subscription Id]resourceGroups/RG-NSG/providers/Microsoft.Network/networkInterfaces/TestNICWeb2/ipConfigurations/ipconfig1
        data:      /subscriptions/[Subscription Id]/resourceGroups/RG-NSG/providers/Microsoft.Network/networkInterfaces/TestNICWeb1/ipConfigurations/ipconfig1
        data:
        info:    network vnet subnet set command OK

## <a name="delete-an-nsg"></a>Удаление группы NSG
Группу безопасности сети можно удалить только в том случае, если она не связана с ресурсами. Чтобы удалить группу безопасности сети, выполните следующие действия.

1. Чтобы проверить ресурсы, связанные с группой, выполните команду `azure network nsg show` , как показано в разделе [Просмотр связей для группы безопасности сети](#View-NSGs-associations).
2. Если группа связана с сетевыми адаптерами, выполните `azure network nic set` , как показано в разделе [Отмена связи с сетевым адаптером для группы безопасности сети](#Dissociate-an-NSG-from-a-NIC) , для каждого сетевого адаптера. 
3. Если группа связана с подсетью, выполните `azure network vnet subnet set` , как показано в разделе [Отмена связи с подсетью для группы безопасности сети](#Dissociate-an-NSG-from-a-subnet) , для каждой подсети.
4. Чтобы удалить группу безопасности сети, выполните следующую команду:

    ```azurecli
    azure network nsg delete --resource-group RG-NSG --name NSG-FrontEnd --quiet
    ```

    Ожидаемые выходные данные:

        info:    Executing command network nsg delete
        + Looking up the network security group "NSG-FrontEnd"
        + Deleting network security group "NSG-FrontEnd"
        info:    network nsg delete command OK

## <a name="next-steps"></a>Дальнейшие действия
* [Включите ведение журнала](virtual-network-nsg-manage-log.md) для групп безопасности сети.



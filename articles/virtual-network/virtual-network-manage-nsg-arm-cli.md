---
title: Управление группами безопасности сети (Azure CLI) | Документация Майкрософт
description: Узнайте, как управлять группами безопасности сети с помощью интерфейса командной строки Azure.
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ed17d314-07e6-4c7f-bcf1-a8a2535d7c14
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/21/2017
ms.author: jdial
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ee43bfd85bdda16cb496ec938c266c72f0d5688b
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/10/2018
---
# <a name="manage-network-security-groups-using-the-azure-cli"></a>Управление группами безопасности сети с помощью Azure CLI

[!INCLUDE [virtual-network-manage-arm-selectors-include.md](../../includes/virtual-network-manage-nsg-arm-selectors-include.md)]

[!INCLUDE [virtual-network-manage-nsg-intro-include.md](../../includes/virtual-network-manage-nsg-intro-include.md)]

> [!NOTE]
> В Azure предлагаются две модели развертывания для создания ресурсов и работы с ними: [модель Resource Manager и классическая модель](../resource-manager-deployment-model.md). В этой статье описывается использование модели развертывания c помощью Resource Manager. Для большинства новых развертываний мы рекомендуем использовать эту модель вместо классической.
> 

[!INCLUDE [virtual-network-manage-nsg-arm-scenario-include.md](../../includes/virtual-network-manage-nsg-arm-scenario-include.md)]

## <a name="prerequisite"></a>Предварительные требования
Установите и настройте последнюю версию [Azure CLI 2.0](/cli/azure/install-az-cli2) (если вы еще этого не сделали), а затем войдите с использованием учетной записи Azure, выполнив команду [az login](/cli/azure/reference-index#az_login). 

## <a name="view-existing-nsgs"></a>Просмотр существующих групп безопасности сети
Чтобы просмотреть список групп безопасности сети в определенной группе ресурсов, выполните команду [az network nsg list](/cli/azure/network/nsg#az_network_nsg_list) с форматом выходных данных `-o table`:

```azurecli
az network nsg list -g RG-NSG -o table
```

Ожидаемые выходные данные:

    Location    Name          ProvisioningState    ResourceGroup    ResourceGuid
    ----------  ------------  -------------------  ---------------  ------------------------------------
    centralus   NSG-BackEnd   Succeeded            RG-NSG           <guid>
    centralus   NSG-FrontEnd  Succeeded            RG-NSG           <guid>

## <a name="list-all-rules-for-an-nsg"></a>Перечисление всех правил для группы безопасности сети
Чтобы просмотреть группу безопасности сети **NSG-FrontEnd**, выполните команду [az network nsg show](/cli/azure/network/nsg#az_network_nsg_show), используя [фильтр запроса JMESPATH](/cli/azure/query-az-cli2) и формат выходных данных `-o table`:

```azurecli
    az network nsg show \
    --resource-group RG-NSG \
    --name NSG-FrontEnd \
    --query '[defaultSecurityRules[],securityRules[]][].{Name:name,Desc:description,Access:access,Direction:direction,DestPortRange:destinationPortRange,DestAddrPrefix:destinationAddressPrefix,SrcPortRange:sourcePortRange,SrcAddrPrefix:sourceAddressPrefix}' \
    -o table
```

Ожидаемые выходные данные:

    Name                           Desc                                                    Access    Direction    DestPortRange    DestAddrPrefix    SrcPortRange    SrcAddrPrefix
    -----------------------------  ------------------------------------------------------  --------  -----------  ---------------  ----------------  --------------  -----------------
    AllowVnetInBound               Allow inbound traffic from all VMs in VNET              Allow     Inbound      *                VirtualNetwork    *               VirtualNetwork
    AllowAzureLoadBalancerInBound  Allow inbound traffic from azure load balancer          Allow     Inbound      *                *                 *               AzureLoadBalancer
    DenyAllInBound                 Deny all inbound traffic                                Deny      Inbound      *                *                 *               *
    AllowVnetOutBound              Allow outbound traffic from all VMs to all VMs in VNET  Allow     Outbound     *                VirtualNetwork    *               VirtualNetwork
    AllowInternetOutBound          Allow outbound traffic from all VMs to Internet         Allow     Outbound     *                Internet          *               *
    DenyAllOutBound                Deny all outbound traffic                               Deny      Outbound     *                *                 *               *
    rdp-rule                                                                               Allow     Inbound      3389             *                 *               Internet
    web-rule                                                                               Allow     Inbound      80               *                 *               Internet
> [!NOTE]
> С помощью команды [az network nsg rule list](/cli/azure/network/nsg/rule#az_network_nsg_rule_list) можно вывести список только пользовательских правил для группы безопасности сети.
>

## <a name="view-nsg-associations"></a>Просмотр связей для группы безопасности сети

Чтобы просмотреть, с какими ресурсами связана группа **NSG-FrontEnd**, выполните команду `az network nsg show`. 

```azurecli
az network nsg show -g RG-NSG -n nsg-frontend --query '[subnets,networkInterfaces]'
```

Найдите в выходных данных свойства **networkInterfaces** и **subnets**.

```json
[
  [
    {
      "addressPrefix": null,
      "etag": null,
      "id": "/subscriptions/<guid>/resourceGroups/RG-NSG/providers/Microsoft.Network/virtualNetworks/TestVNET/subnets/FrontEnd",
      "ipConfigurations": null,
      "name": null,
      "networkSecurityGroup": null,
      "provisioningState": null,
      "resourceGroup": "RG-NSG",
      "resourceNavigationLinks": null,
      "routeTable": null
    }
  ],
  null
]
```

В примере выше группа безопасности сети не связана ни с одним сетевым интерфейсом, но связана с подсетью **FrontEnd**.

## <a name="add-a-rule"></a>Добавление правила
Чтобы добавить правило, разрешающее **входящий** трафик через порт **443** с любого компьютера в группу безопасности сети **NSG-FrontEnd**, выполните следующую команду:

```azurecli
az network nsg rule create  \
--resource-group RG-NSG \
--nsg-name NSG-FrontEnd  \
--name allow-https \
--description "Allow access to port 443 for HTTPS" \
--access Allow \
--protocol Tcp  \
--direction Inbound \
--priority 102 \
--source-address-prefix "*"  \
--source-port-range "*"  \
--destination-address-prefix "*" \
--destination-port-range "443"
```

Ожидаемые выходные данные:

```json
{
  "access": "Allow",
  "description": "Allow access to port 443 for HTTPS",
  "destinationAddressPrefix": "*",
  "destinationPortRange": "443",
  "direction": "Inbound",
  "etag": "W/\"<guid>\"",
  "id": "/subscriptions/<guid>/resourceGroups/RG-NSG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd/securityRules/allow-https",
  "name": "allow-https",
  "priority": 102,
  "protocol": "Tcp",
  "provisioningState": "Succeeded",
  "resourceGroup": "RG-NSG",
  "sourceAddressPrefix": "*",
  "sourcePortRange": "*"
}
```

## <a name="change-a-rule"></a>Изменение правила
Чтобы изменить ранее созданное правило, разрешающее входящий трафик только из **Интернета**, выполните команду [az network nsg rule update](/cli/azure/network/nsg/rule#az_network_nsg_rule_update).

```azurecli
az network nsg rule update \
--resource-group RG-NSG \
--nsg-name NSG-FrontEnd \
--name allow-https \
--source-address-prefix Internet
```

Ожидаемые выходные данные:

```json
{
"access": "Allow",
"description": "Allow access to port 443 for HTTPS",
"destinationAddressPrefix": "*",
"destinationPortRange": "443",
"direction": "Inbound",
"etag": "W/\"<guid>\"",
"id": "/subscriptions/<guid>/resourceGroups/RG-NSG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd/securityRules/allow-https",
"name": "allow-https",
"priority": 102,
"protocol": "Tcp",
"provisioningState": "Succeeded",
"resourceGroup": "RG-NSG",
"sourceAddressPrefix": "Internet",
"sourcePortRange": "*"
}
```

## <a name="delete-a-rule"></a>Удаление правила
Чтобы удалить созданное ранее правило, выполните следующую команду:

```azurecli
az network nsg rule delete \
--resource-group RG-NSG \
--nsg-name NSG-FrontEnd \
--name allow-https
```


## <a name="associate-an-nsg-to-a-nic"></a>Связывание группы безопасности сети с сетевым адаптером
Чтобы связать группу безопасности сети **NSG-FrontEnd** с сетевой картой **TestNICWeb1**, выполните команду [az network nic update](/cli/azure/network/nic#az_network_nic_update):

```azurecli
az network nic update \
--resource-group RG-NSG \
--name TestNICWeb1 \
--network-security-group NSG-FrontEnd    
```

Ожидаемые выходные данные:

```json
{
  "dnsSettings": {
    "appliedDnsServers": [],
    "dnsServers": [],
    "internalDnsNameLabel": null,
    "internalDomainNameSuffix": "k0wkaguidnqrh0ud.gx.internal.cloudapp.net",
    "internalFqdn": null
  },
  "enableAcceleratedNetworking": false,
  "enableIpForwarding": false,
  "etag": "W/\"<guid>\"",
  "id": "/subscriptions/<guid>/resourceGroups/RG-NSG/providers/Microsoft.Network/networkInterfaces/TestNICWeb1",
  "ipConfigurations": [
    {
      "applicationGatewayBackendAddressPools": null,
      "etag": "W/\"<guid>\"",
      "id": "/subscriptions/<guid>/resourceGroups/RG-NSG/providers/Microsoft.Network/networkInterfaces/TestNICWeb1/ipConfigurations/ipconfig1",
      "loadBalancerBackendAddressPools": null,
      "loadBalancerInboundNatRules": null,
      "name": "ipconfig1",
      "primary": true,
      "privateIpAddress": "192.168.1.6",
      "privateIpAddressVersion": "IPv4",
      "privateIpAllocationMethod": "Static",
      "provisioningState": "Succeeded",
      "publicIpAddress": null,
      "resourceGroup": "RG-NSG",
      "subnet": {
        "addressPrefix": null,
        "etag": null,
        "id": "/subscriptions/<guid>/resourceGroups/RG-NSG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd",
        "ipConfigurations": null,
        "name": null,
        "networkSecurityGroup": null,
        "provisioningState": null,
        "resourceGroup": "RG-NSG",
        "resourceNavigationLinks": null,
        "routeTable": null
      }
    }
  ],
  "location": "centralus",
  "macAddress": "00-0D-3A-91-A9-60",
  "name": "TestNICWeb1",
  "networkSecurityGroup": {
    "defaultSecurityRules": null,
    "etag": null,
    "id": "/subscriptions/<guid>/resourceGroups/RG-NSG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd",
    "location": null,
    "name": null,
    "networkInterfaces": null,
    "provisioningState": null,
    "resourceGroup": "RG-NSG",
    "resourceGuid": null,
    "securityRules": null,
    "subnets": null,
    "tags": null,
    "type": null
  },
  "primary": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "RG-NSG",
  "resourceGuid": "<guid>",
  "tags": {},
  "type": "Microsoft.Network/networkInterfaces",
  "virtualMachine": null
}
```

## <a name="dissociate-an-nsg-from-a-nic"></a>Отмена связи с сетевым адаптером для группы безопасности сети

Чтобы удалить связь между группой безопасности сети **NSG-FrontEnd** и сетевой картой **TestNICWeb1**, выполните команду [az network nsg rule update](/cli/azure/network/nsg/rule#az_network_nsg_rule_update) еще раз, но замените аргумент `--network-security-group` пустой строкой (`""`).

```azurecli
az network nic update --resource-group RG-NSG --name TestNICWeb3 --network-security-group ""
```

В выходных данных для ключа `networkSecurityGroup` задано значение null.

## <a name="dissociate-an-nsg-from-a-subnet"></a>Отмена связи с подсетью для группы безопасности сети
Чтобы удалить связь между группой безопасности сети **NSG-FrontEnd** и подсетью **FrontEnd**, выполните команду [az network nsg rule update](/cli/azure/network/nsg/rule#az_network_nsg_rule_update) еще раз, но замените аргумент `--network-security-group` пустой строкой (`""`).

```azurecli
az network vnet subnet update \
--resource-group RG-NSG \
--vnet-name testvnet \
--name FrontEnd \
--network-security-group ""
```

В выходных данных для ключа `networkSecurityGroup` задано значение null.

## <a name="associate-an-nsg-to-a-subnet"></a>Связывание группы NSG с подсетью
Чтобы снова связать группу безопасности сети **NSG-FrontEnd** с подсетью **FrontEnd**, выполните следующую команду:

```azurecli
az network vnet subnet update \
--resource-group RG-NSG \
--vnet-name testvnet \
--name FrontEnd \
--network-security-group NSG-FrontEnd
```

В выходных данных для ключа `networkSecurityGroup` задано значение, подобное следующему:

```json
"networkSecurityGroup": {
    "defaultSecurityRules": null,
    "etag": null,
    "id": "/subscriptions/0e220bf6-5caa-4e9f-8383-51f16b6c109f/resourceGroups/RG-NSG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd",
    "location": null,
    "name": null,
    "networkInterfaces": null,
    "provisioningState": null,
    "resourceGroup": "RG-NSG",
    "resourceGuid": null,
    "securityRules": null,
    "subnets": null,
    "tags": null,
    "type": null
  }
  ```

## <a name="delete-an-nsg"></a>Удаление группы NSG
Группу безопасности сети можно удалить только в том случае, если она не связана с ресурсами. Чтобы удалить группу безопасности сети, выполните следующие действия:

1. Чтобы проверить ресурсы, связанные с группой, выполните команду `azure network nsg show` , как показано в разделе [Просмотр связей для группы безопасности сети](#View-NSGs-associations).
2. Если группа связана с сетевыми адаптерами, выполните `azure network nic set` , как показано в разделе [Отмена связи с сетевым адаптером для группы безопасности сети](#Dissociate-an-NSG-from-a-NIC) , для каждого сетевого адаптера. 
3. Если группа связана с подсетью, выполните `azure network vnet subnet set` , как показано в разделе [Отмена связи с подсетью для группы безопасности сети](#Dissociate-an-NSG-from-a-subnet) , для каждой подсети.
4. Чтобы удалить группу безопасности сети, выполните следующую команду:

    ```azurecli
    az network nsg delete --resource-group RG-NSG --name NSG-FrontEnd
    ```
## <a name="next-steps"></a>Дополнительная информация
* [Включите ведение журнала](virtual-network-nsg-manage-log.md) для групп безопасности сети.


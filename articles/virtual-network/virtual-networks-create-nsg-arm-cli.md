---
title: Создание групп безопасности сети с помощью Azure CLI | Документация Майкрософт
description: Узнайте, как создавать и развертывать группы безопасности сети с помощью Azure CLI 1.0.
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 9ea82c09-f4a6-4268-88bc-fc439db40c48
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/17/2017
ms.author: jdial
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 4827fabf1d8cde366dda8b3a782a2fefe01db8d5
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/31/2018
---
# <a name="create-network-security-groups-using-the-azure-cli"></a>Создание групп безопасности сети с помощью Azure CLI 1.0

[!INCLUDE [virtual-networks-create-nsg-selectors-arm-include](../../includes/virtual-networks-create-nsg-selectors-arm-include.md)]

[!INCLUDE [virtual-networks-create-nsg-intro-include](../../includes/virtual-networks-create-nsg-intro-include.md)]

[!INCLUDE [virtual-networks-create-nsg-scenario-include](../../includes/virtual-networks-create-nsg-scenario-include.md)]

Для работы приведенных в этом руководстве команд Azure CLI требуется наличие простой среды на основе предыдущего сценария. 

## <a name="create-the-nsg-for-the-frontend-subnet"></a>Создание группы безопасности сети для подсети `FrontEnd`

Чтобы создать группу безопасности сети *NSG-FrontEnd* по описанному выше сценарию, сделайте следующее:

1. Установите и настройте последнюю версию [Azure CLI 2.0](/cli/azure/install-az-cli2) (если вы еще этого не сделали), а затем войдите с использованием учетной записи Azure, выполнив команду [az login](/cli/azure/reference-index#az_login). 

2. Создайте группу безопасности сети, выполнив команду [azure network nsg create](/cli/azure/network/nsg#az_network_nsg_create). 

    ```azurecli
    az network nsg create \
    --resource-group testrg \
    --name NSG-FrontEnd \
    --location centralus 
    ```

    Параметры
   
   * `--resource-group` — имя группы ресурсов, в которой создается группа безопасности сети. В данном сценарии это *TestRG*.
   * `--location` — регион Azure, в котором создается группа безопасности сети. В нашем случае это *westus*.
   * `--name` — имя новой группы безопасности сети. В данном сценарии это *NSG-FrontEnd*.

    В результате вы получите довольно много сведений, включая список всех правил по умолчанию. В следующем примере показаны правила по умолчанию с использованием фильтра запроса JMESPATH с форматом выходных данных `table`:

    ```azurecli
    az network nsg show \
    -g testrg \
    -n nsg-frontend \
    --query 'defaultSecurityRules[].{Access:access,Desc:description,DestPortRange:destinationPortRange,Direction:direction,Priority:priority}' \
    -o table
    ```
   
   Выходные данные:

        Access    Desc                                                    DestPortRange    Direction      Priority
        
        Allow     Allow inbound traffic from all VMs in VNET              *                Inbound           65000
        Allow     Allow inbound traffic from azure load balancer          *                Inbound           65001
        Deny      Deny all inbound traffic                                *                Inbound           65500
        Allow     Allow outbound traffic from all VMs to all VMs in VNET  *                Outbound          65000
        Allow     Allow outbound traffic from all VMs to Internet         *                Outbound          65001
        Deny      Deny all outbound traffic                               *                Outbound          65500



3. Создайте правило, которое разрешает доступ к порту 3389 (RDP) из Интернета, выполнив команду [azure network nsg rule create](/cli/azure/network/nsg/rule#az_network_nsg_rule_create).

    > [!NOTE]
    > В зависимости от используемой оболочки может потребоваться изменить символ `*` в следующих аргументах, чтобы не расширять их перед выполнением.
   
    ```azurecli
    az network nsg rule create \
    --resource-group testrg \
    --nsg-name NSG-FrontEnd \
    --name rdp-rule \
    --access Allow \
    --protocol Tcp \
    --direction Inbound \
    --priority 100 \
    --source-address-prefix Internet \
    --source-port-range "*" \
    --destination-address-prefix "*" \
    --destination-port-range 3389
    ```
   
    Ожидаемые выходные данные:
   
    ```json
    {
        "access": "Allow",
        "description": null,
        "destinationAddressPrefix": "*",
        "destinationPortRange": "3389",
        "direction": "Inbound",
        "etag": "W/\"<guid>\"",
        "id": "/subscriptions/<guid>/resourceGroups/testrg/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd/securityRules/rdp-rule",
        "name": "rdp-rule",
        "priority": 100,
        "protocol": "Tcp",
        "provisioningState": "Succeeded",
        "resourceGroup": "testrg",
        "sourceAddressPrefix": "Internet",
        "sourcePortRange": "*"
    }
    ```

    Параметры

    * `--resource-group testrg` — группа ресурсов, которую следует использовать. Обратите внимание, что в ней не учитывается регистр.
    * `--nsg-name NSG-FrontEnd` — имя группы безопасности сети, в которой будет создано правило.
    * `--name rdp-rule` — имя нового правила.
    * `--access Allow` — уровень доступа для правила (Deny или Allow).
    * `--protocol Tcp` — протокол (TCP, UDP или "*").
    * `--direction Inbound` — направление подключения (Inbound или Outbound).
    * `--priority 100` — приоритет правила.
    * `--source-address-prefix Internet` — префикс адреса источника в CIDR или использование тегов по умолчанию.
    * `--source-port-range "*"` — исходный порт или диапазон портов. Порт, открывший подключение.
    * `--destination-address-prefix "*"` — префикс адреса назначения в CIDR или использование тегов по умолчанию.
    * `--destination-port-range 3389` — конечный порт или диапазон портов. Порт, принимающий запрос на подключение.



4. Создайте правило, которое разрешает доступ к порту 80 (HTTP) из Интернета, выполнив команду **azure network nsg rule create**.
   
    ```azurecli
    az network nsg rule create \
    --resource-group testrg \
    --nsg-name NSG-FrontEnd \
    --name web-rule \
    --access Allow \
    --protocol Tcp \
    --direction Inbound \
    --priority 200 \
    --source-address-prefix Internet \
    --source-port-range "*" \
    --destination-address-prefix "*" \
    --destination-port-range 80
    ```
   
    Ожидаемые выходные данные:
   
    ```json
    {
        "access": "Allow",
        "description": null,
        "destinationAddressPrefix": "*",
        "destinationPortRange": "80",
        "direction": "Inbound",
        "etag": "W/\"<guid>\"",
        "id": "/subscriptions/<guid>/resourceGroups/testrg/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd/securityRules/web-rule",
        "name": "web-rule",
        "priority": 200,
        "protocol": "Tcp",
        "provisioningState": "Succeeded",
        "resourceGroup": "testrg",
        "sourceAddressPrefix": "Internet",
        "sourcePortRange": "*"
    }
    ```

5. Привяжите группу безопасности сети к подсети **FrontEnd**, выполнив команду [az network vnet subnet update](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_update).
        
    ```azurecli
    az network vnet subnet update \
    --vnet-name TestVNET \
    --name FrontEnd \
    --resource-group testrg \
    --network-security-group NSG-FrontEnd
    ```
   
    Ожидаемые выходные данные:
   
    ```json
    {
        "addressPrefix": "192.168.1.0/24",
        "etag": "W/\"<guid>\"",
        "id": "/subscriptions/<guid>/resourceGroups/testrg/providers/Microsoft.Network/virtualNetworks/TestVNET/subnets/FrontEnd",
        "ipConfigurations": [
            {
            "etag": null,
            "id": "/subscriptions/<guid>/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNIC/ipConfigurations/ipconfig1",
            "name": null,
            "privateIpAddress": null,
            "privateIpAllocationMethod": null,
            "provisioningState": null,
            "publicIpAddress": null,
            "resourceGroup": "TestRG",
            "subnet": null
            }
        ],
        "name": "FrontEnd",
        "networkSecurityGroup": {
            "defaultSecurityRules": null,
            "etag": null,
            "id": "/subscriptions/<guid>f/resourceGroups/testrg/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd",
            "location": null,
            "name": null,
            "networkInterfaces": null,
            "provisioningState": null,
            "resourceGroup": "testrg",
            "resourceGuid": null,
            "securityRules": null,
            "subnets": null,
            "tags": null,
            "type": null
        },
        "provisioningState": "Succeeded",
        "resourceGroup": "testrg",
        "resourceNavigationLinks": null,
        "routeTable": null
    }
    ```

## <a name="create-the-nsg-for-the-backend-subnet"></a>Создание группы безопасности сети для подсети `BackEnd`
Чтобы создать группу безопасности сети *NSG-BackEnd* по описанному выше сценарию, сделайте следующее:

1. Создайте группу безопасности сети `NSG-BackEnd`, выполнив команду **az network nsg create**.
   
    ```azurecli
    az network nsg create \
    --resource-group testrg \
    --name NSG-BackEnd \
    --location centralus
    ```
   
    Как и на предыдущем шаге 2, в результате вы получите довольно много сведений, включая правила по умолчанию.
   
2. Создайте правило, которое разрешает доступ к порту 1433 (SQL) из подсети `FrontEnd`, выполнив команду **azure network nsg rule create**.
   
    ```azurecli
    az network nsg rule create \
    --resource-group testrg \
    --nsg-name NSG-BackEnd \
    --name sql-rule \
    --access Allow \
    --protocol Tcp \
    --direction Inbound \
    --priority 100 \
    --source-address-prefix 192.168.1.0/24 \
    --source-port-range "*" \
    --destination-address-prefix "*" \
    --destination-port-range 1433
    ```
   
    Ожидаемые выходные данные:

    ```json  
    {
    "access": "Allow",
    "description": null,
    "destinationAddressPrefix": "*",
    "destinationPortRange": "1433",
    "direction": "Inbound",
    "etag": "W/\"<guid>\"",
    "id": "/subscriptions/<guid>/resourceGroups/testrg/providers/Microsoft.Network/networkSecurityGroups/NSG-BackEnd/securityRules/sql-rule",
    "name": "sql-rule",
    "priority": 100,
    "protocol": "Tcp",
    "provisioningState": "Succeeded",
    "resourceGroup": "testrg",
    "sourceAddressPrefix": "192.168.1.0/24",
    "sourcePortRange": "*"
    }
    ```

3. Создайте правило, которое запрещает доступ к Интернету, выполнив команду **azure network nsg rule create**.
   
    ```azurecli
    az network nsg rule create \
    --resource-group testrg \
    --nsg-name NSG-BackEnd \
    --name web-rule \
    --access Deny \
    --protocol Tcp  \
    --direction Outbound  \
    --priority 200 \
    --source-address-prefix "*" \
    --source-port-range "*" \
    --destination-address-prefix "*" \
    --destination-port-range "*"
    ```
   
    Ожидаемые выходные данные:
   
    ```json
    {
    "access": "Deny",
    "description": null,
    "destinationAddressPrefix": "*",
    "destinationPortRange": "*",
    "direction": "Outbound",
    "etag": "W/\"<guid>\"",
    "id": "/subscriptions/<guid>/resourceGroups/testrg/providers/Microsoft.Network/networkSecurityGroups/NSG-BackEnd/securityRules/web-rule",
    "name": "web-rule",
    "priority": 200,
    "protocol": "Tcp",
    "provisioningState": "Succeeded",
    "resourceGroup": "testrg",
    "sourceAddressPrefix": "*",
    "sourcePortRange": "*"
    }
    ```

4. Привяжите группу безопасности сети к подсети `BackEnd`, выполнив команду **az network vnet subnet set**.
   
    ```azurecli
    az network vnet subnet update \
    --vnet-name TestVNET \
    --name BackEnd \
    --resource-group testrg \
    --network-security-group NSG-BackEnd
    ```
   
    Ожидаемые выходные данные:
   
    ```json
    {
    "addressPrefix": "192.168.2.0/24",
    "etag": "W/\"<guid>\"",
    "id": "/subscriptions/<guid>/resourceGroups/testrg/providers/Microsoft.Network/virtualNetworks/TestVNET/subnets/BackEnd",
    "ipConfigurations": null,
    "name": "BackEnd",
    "networkSecurityGroup": {
        "defaultSecurityRules": null,
        "etag": null,
        "id": "/subscriptions/<guid>/resourceGroups/testrg/providers/Microsoft.Network/networkSecurityGroups/NSG-BackEnd",
        "location": null,
        "name": null,
        "networkInterfaces": null,
        "provisioningState": null,
        "resourceGroup": "testrg",
        "resourceGuid": null,
        "securityRules": null,
        "subnets": null,
        "tags": null,
        "type": null
    },
    "provisioningState": "Succeeded",
    "resourceGroup": "testrg",
    "resourceNavigationLinks": null,
    "routeTable": null
    }
    ```

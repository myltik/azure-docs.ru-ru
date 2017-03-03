---
title: "Создание виртуальной сети с помощью Azure CLI 2.0 | Документация Майкрософт"
description: "Узнайте, как создать виртуальную сеть с помощью Azure CLI 2.0 в модели Resource Manager."
services: virtual-network
documentationcenter: 
author: jimdial
manager: carmonm
editor: 
tags: azure-resource-manager
ms.assetid: 75966bcc-0056-4667-8482-6f08ca38e77a
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2016
ms.author: jdial
translationtype: Human Translation
ms.sourcegitcommit: 617ac4672b24d339c5d4c0b671de7fb19cd9af91
ms.openlocfilehash: 3cbb679048a0cc1121b221bda8fc1e3df0e307c3
ms.lasthandoff: 02/17/2017


---
# <a name="create-a-virtual-network-using-the-azure-cli"></a>Создание виртуальной сети с помощью интерфейса командной строки Azure

[!INCLUDE [virtual-networks-create-vnet-intro](../../includes/virtual-networks-create-vnet-intro-include.md)]

Azure предоставляет две модели развертывания: с помощью Azure Resource Manager и классическую. Для создания ресурсов корпорация Майкрософт рекомендует использовать модель развертывания с помощью Resource Manager. Дополнительные сведения о различиях между двумя моделями см. в статье [Azure Resource Manager vs. classic deployment: Understand deployment models and the state of your resources](../azure-resource-manager/resource-manager-deployment-model.md) (Azure Resource Manager и классическое развертывание. Общие сведения о моделях развертывания и состоянии ресурсов).

## <a name="cli-versions-to-complete-the-task"></a>Версии интерфейса командной строки для выполнения задачи
Вы можете выполнить задачу, используя одну из следующих версий интерфейса командной строки.

- [Azure CLI 1.0](virtual-networks-create-vnet-arm-cli-nodejs.md) — интерфейс командной строки для классической модели развертывания и модели развертывания Resource Manager.
- [Azure CLI 2.0 (предварительная версия)](#create-a-virtual-network) — интерфейс командной строки нового поколения для модели развертывания с помощью Resource Manager (описывается в этой статье).
 
    Виртуальную сеть также можно создать с помощью Resource Manager, используя другие инструменты, либо с помощью классической модели развертывания, выбрав другой вариант из следующего списка:

> [!div class="op_single_selector"]
- [Портал](virtual-networks-create-vnet-arm-pportal.md)
- [PowerShell](virtual-networks-create-vnet-arm-ps.md)
- [ИНТЕРФЕЙС КОМАНДНОЙ СТРОКИ](virtual-networks-create-vnet-arm-cli.md)
- [Шаблон](virtual-networks-create-vnet-arm-template-click.md)
- [Портал (классический)](virtual-networks-create-vnet-classic-pportal.md)
- [PowerShell (классическая модель)](virtual-networks-create-vnet-classic-netcfg-ps.md)
- [Интерфейс командной строки (классическая модель)](virtual-networks-create-vnet-classic-cli.md)

[!INCLUDE [virtual-networks-create-vnet-scenario-include](../../includes/virtual-networks-create-vnet-scenario-include.md)]


## <a name="create-a-virtual-network"></a>Создать виртуальную сеть

Чтобы создать виртуальную сеть с помощью Azure CLI 2.0, сделайте следующее.

1. Установите и настройте последнюю версию [Azure CLI 2.0 (предварительная версия)](/cli/azure/install-az-cli2), затем войдите в систему с учетной записью Azure, выполнив команду [az login](/cli/azure/#login).

2. Создайте группу ресурсов для виртуальной сети, выполнив команду [az group create](/cli/azure/group#create) с аргументами `--name` и `--location`.

    ```azurecli
    az group create --name myVNet --location centralus
    ```

3. Создайте виртуальную сеть и подсеть.

    ```azurecli
    az network vnet create \
        --name TestVNet \
        --resource-group myVNet \
        --location centralus \
        --address-prefix 192.168.0.0/16 \
        --subnet-name FrontEnd \
        --subnet-prefix 192.168.1.0/24
    ```

    Ожидаемые выходные данные:
   
    ```json
    {
        "newVNet": {
            "addressSpace": {
            "addressPrefixes": [
                "192.168.0.0/16"
            ]
            },
            "dhcpOptions": {
            "dnsServers": []
            },
            "provisioningState": "Succeeded",
            "resourceGuid": "<guid>",
            "subnets": [
            {
                "etag": "W/\"<guid>\"",
                "id": "/subscriptions/<guid>/resourceGroups/myVNet/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd",
                "name": "FrontEnd",
                "properties": {
                "addressPrefix": "192.168.1.0/24",
                "provisioningState": "Succeeded"
                },
                "resourceGroup": "myVNet"
            }
            ]
        }
    }
    ```

    Используемые параметры:

    - `--name TestVNet`: имя виртуальной сети, которую нужно будет создать.
    - `--resource-group myVNet`: имя группы ресурсов, которая управляет ресурсом. 
    - `--location centralus`: расположение для развертывания.
    - `--address-prefix 192.168.0.0/16`: префикс и блок адреса.  
    - `--subnet-name FrontEnd`: имя подсети.
    - `--subnet-prefix 192.168.1.0/24`: префикс и блок адреса.

    Чтобы вывести на экран основные данные, которые используются в следующей команде, можно отправить запрос к виртуальной сети с помощью [фильтра запроса](/cli/azure/query-az-cli2).

    ```azurecli
    az network vnet list --query '[?name==`TestVNet`].{Where:location,Name:name,Group:resourceGroup}' -o table
    ```

    В результате будут возвращены следующие выходные данные:

        Where      Name      Group
        ---------  --------  -------
        centralus  TestVNet  myVNet

4. Создайте подсеть.

    ```azurecli
    az network vnet subnet create \
        --address-prefix 192.168.2.0/24 \
        --name BackEnd \
        --resource-group myVNet \
        --vnet-name TestVNet
    ```
   
    Ожидаемые выходные данные:

    ```json
    {
    "addressPrefix": "192.168.2.0/24",
    "etag": "W/\"<guid> \"",
    "id": "/subscriptions/<guid>/resourceGroups/myVNet/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/BackEnd",
    "ipConfigurations": null,
    "name": "BackEnd",
    "networkSecurityGroup": null,
    "provisioningState": "Succeeded",
    "resourceGroup": "myVNet",
    "resourceNavigationLinks": null,
    "routeTable": null
    }
    ```

    Используемые параметры:

    - `--address-prefix 192.168.2.0/24`: блок CIDR подсети.
    - `--name BackEnd`: имя новой подсети.
    - `--resource-group myVNet`: группа ресурсов.
    - `--vnet-name TestVNet`: имя виртуальной сети, в которую входит подсеть.

5. Запросите свойства новой виртуальной сети.

    ```azurecli
    az network vnet show \
    -g myVNET \
    -n TestVNet \
    --query '{Name:name,Where:location,Group:resourceGroup,Status:provisioningState,SubnetCount:subnets | length(@)}' \
    -o table
    ```
   
    Ожидаемые выходные данные:
   
        Name      Where      Group    Status       SubnetCount
        --------  ---------  -------  ---------  -------------
        TestVNet  centralus  myVNet   Succeeded              2

6. Запросите свойства подсетей.

    ```azurecli
    az network vnet subnet list \
    -g myvnet \
    --vnet-name testvnet \
    --query '[].{Name:name,CIDR:addressPrefix,Status:provisioningState}' \
    -o table
    ```

    Ожидаемые выходные данные:

        Name      CIDR            Status
        --------  --------------  ---------
        FrontEnd  192.168.1.0/24  Succeeded
        BackEnd   192.168.2.0/24  Succeeded

## <a name="next-steps"></a>Дальнейшие действия

Инструкции по подключению:

- Сведения о подключении виртуальной машины к виртуальной сети см. в статье о [создании виртуальной машины Linux](../virtual-machines/virtual-machines-linux-quick-create-cli.md). Вместо создания виртуальной сети и подсети с помощью действий, описанных в этой статье, виртуальную машину можно подключить к имеющейся виртуальной сети и подсети.
- Сведения об установке подключения между виртуальными сетями см. в статье [Настройка подключения между виртуальными сетями на портале Azure](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md).
- Сведения о подключении виртуальной сети к локальной сети с использованием виртуальной частной сети типа "сеть — сеть" или канала ExpressRoute см. в статьях [Добавление подключения типа "сеть — сеть" к виртуальной сети с помощью существующего подключения VPN-шлюза](../vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md) и [Подключение виртуальной сети к каналу ExpressRoute](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md).

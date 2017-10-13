---
title: "Управление маршрутизацией и виртуальными модулями с помощью Azure CLI 2.0 | Документация Майкрософт"
description: "Сведения о том, как управлять маршрутизацией и виртуальными модулями с помощью Azure CLI 2.0."
services: virtual-network
documentationcenter: na
author: jimdial
manager: carmonm
editor: 
tags: azure-resource-manager
ms.assetid: 5452a0b8-21a6-4699-8d6a-e2d8faf32c25
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/12/2017
ms.author: jdial
ms.openlocfilehash: e5d9519998346619093f443b740c8904283f76e8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="create-user-defined-routes-udr-using-the-azure-cli-20"></a>Создание определяемых пользователем маршрутов с помощью Azure CLI 2.0

> [!div class="op_single_selector"]
> * [PowerShell](virtual-network-create-udr-arm-ps.md)
> * [Интерфейс командной строки Azure](virtual-network-create-udr-arm-cli.md)
> * [Шаблон](virtual-network-create-udr-arm-template.md)
> * [PowerShell (классическое развертывание)](virtual-network-create-udr-classic-ps.md)
> * [Интерфейс командной строки (классическое развертывание)](virtual-network-create-udr-classic-cli.md)

## <a name="cli-versions-to-complete-the-task"></a>Версии интерфейса командной строки для выполнения задачи 

Вы можете выполнить задачу, используя одну из следующих версий интерфейса командной строки. 

- [Azure CLI 1.0](virtual-network-create-udr-arm-cli-nodejs.md) — интерфейс командной строки для классической модели развертывания и модели развертывания Resource Manager. 
- [Azure CLI 2.0](#Create-the-UDR-for-the-front-end-subnet) — это интерфейс командной строки нового поколения для модели развертывания Resource Manager (описывается в этой статье).

[!INCLUDE [virtual-networks-create-nsg-intro-include](../../includes/virtual-networks-create-nsg-intro-include.md)]

[!INCLUDE [virtual-networks-create-nsg-scenario-include](../../includes/virtual-networks-create-nsg-scenario-include.md)]

[!INCLUDE [virtual-network-create-udr-intro-include.md](../../includes/virtual-network-create-udr-intro-include.md)]

[!INCLUDE [virtual-network-create-udr-scenario-include.md](../../includes/virtual-network-create-udr-scenario-include.md)]

Для приведенных ниже примеров команд интерфейса командной строки Azure требуется уже созданная простая среда, основанная на приведенном выше сценарии. Чтобы выполнять команды в том виде, в котором они представлены в этом документе, сначала создайте тестовую среду, развернув [этот шаблон](http://github.com/telmosampaio/azure-templates/tree/master/IaaS-NSG-UDR-Before), нажмите **Deploy to Azure**(Развернуть в Azure), при необходимости замените значения параметров по умолчанию и следуйте указаниям на портале.


## <a name="create-the-udr-for-the-front-end-subnet"></a>Создание определяемого пользователем маршрута для интерфейсной подсети
Чтобы создать таблицу маршрутов и маршрут, необходимые для подсети переднего плана, на основании приведенного выше сценария, выполните следующие действия.

1. Создайте таблицу маршрутов для интерфейсной подсети, выполнив команду [az network route-table create](/cli/azure/network/route-table#create).

    ```azurecli
    az network route-table create \
    --resource-group testrg \
    --location centralus \
    --name UDR-FrontEnd
    ```

    Выходные данные:

    ```json
    {
    "etag": "W/\"<guid>\"",
    "id": "/subscriptions/<guid>/resourceGroups/testrg/providers/Microsoft.Network/routeTables/UDR-FrontEnd",
    "location": "centralus",
    "name": "UDR-FrontEnd",
    "provisioningState": "Succeeded",
    "resourceGroup": "testrg",
    "routes": [],
    "subnets": null,
    "tags": null,
    "type": "Microsoft.Network/routeTables"
    }
    ```

2. Создайте маршрут, который отправляет весь трафик, направляемый во внутреннюю подсеть (192.168.2.0/24), на виртуальную машину **FW1** (192.168.0.4), с помощью команды [az network route-table route create](/cli/azure/network/route-table/route#create).

    ```azurecli 
    az network route-table route create \
    --resource-group testrg \
    --name RouteToBackEnd \
    --route-table-name UDR-FrontEnd \
    --address-prefix 192.168.2.0/24 \
    --next-hop-type VirtualAppliance \
    --next-hop-ip-address 192.168.0.4
    ```

    Выходные данные:

    ```json
    {
    "addressPrefix": "192.168.2.0/24",
    "etag": "W/\"<guid>\"",
    "id": "/subscriptions/<guid>/resourceGroups/testrg/providers/Microsoft.Network/routeTables/UDR-FrontEnd/routes/RouteToBackEnd",
    "name": "RouteToBackEnd",
    "nextHopIpAddress": "192.168.0.4",
    "nextHopType": "VirtualAppliance",
    "provisioningState": "Succeeded",
    "resourceGroup": "testrg"
    }
    ```
    Параметры

    * **--route-table-name**. Имя таблицы маршрутов, куда будет добавлен маршрут. В данном сценарии это *UDR-FrontEnd*.
    * **--address-prefix**. Префикс адреса для подсети, в которую адресованы пакеты. В данном сценарии это *192.168.2.0/24*.
    * **--next-hop-type**. Тип объекта, куда будет отправляться трафик. Возможные значения: *VirtualAppliance*, *VirtualNetworkGateway*, *VNETLocal*, *Internet* или *None*.
    * **--next-hop-ip-address**. IP-адрес следующего прыжка. В нашем случае это *192.168.0.4*.

3. Выполните команду [az network vnet subnet update](/cli/azure/network/vnet/subnet#update), чтобы сопоставить созданную ранее таблицу маршрутов с подсетью **FrontEnd**.

    ```azurecli
    az network vnet subnet update \
    --resource-group testrg \
    --vnet-name testvnet \
    --name FrontEnd \
    --route-table UDR-FrontEnd
    ```

    Выходные данные:

    ```json
    {
    "addressPrefix": "192.168.1.0/24",
    "etag": "W/\"<guid>\"",
    "id": "/subscriptions/<guid>/resourceGroups/testrg/providers/Microsoft.Network/virtualNetworks/testvnet/subnets/FrontEnd",
    "ipConfigurations": null,
    "name": "FrontEnd",
    "networkSecurityGroup": null,
    "provisioningState": "Succeeded",
    "resourceGroup": "testrg",
    "resourceNavigationLinks": null,
    "routeTable": {
        "etag": null,
        "id": "/subscriptions/<guid>/resourceGroups/testrg/providers/Microsoft.Network/routeTables/UDR-FrontEnd",
        "location": null,
        "name": null,
        "provisioningState": null,
        "resourceGroup": "testrg",
        "routes": null,
        "subnets": null,
        "tags": null,
        "type": null
        }
    }
    ```

    Параметры
    
    * **--vnet-name**. Имя виртуальной сети, в которой расположена подсеть. В данном сценарии это *TestVNet*.

## <a name="create-the-udr-for-the-back-end-subnet"></a>Создание определяемого пользователем маршрута для серверной подсети

Чтобы создать таблицу маршрутов и маршрут, необходимые для серверной подсети, на основании приведенного выше сценария, выполните следующие действия:

1. Чтобы создать таблицу маршрутов для серверной подсети, выполните следующую команду:

    ```azurecli
    az network route-table create \
    --resource-group testrg \
    --name UDR-BackEnd \
    --location centralus
    ```

2. Чтобы создать маршрут в таблице маршрутов для отправки всего трафика, предназначенного для интерфейсной подсети (192.168.1.0/24), в виртуальную машину **FW1** (192.168.0.4), выполните следующую команду:

    ```azurecli
    az network route-table route create \
    --resource-group testrg \
    --name RouteToFrontEnd \
    --route-table-name UDR-BackEnd \
    --address-prefix 192.168.1.0/24 \
    --next-hop-type VirtualAppliance \
    --next-hop-ip-address 192.168.0.4
    ```

3. Чтобы сопоставить таблицу маршрутов с подсетью **BackEnd**, выполните следующую команду:

    ```azurecli
    az network vnet subnet update \
    --resource-group testrg \
    --vnet-name testvnet \
    --name BackEnd \
    --route-table UDR-BackEnd
    ```

## <a name="enable-ip-forwarding-on-fw1"></a>Включение IP-пересылки на FW1

Чтобы включить IP-пересылку в сетевом интерфейсе, используемом **FW1**, выполните следующие действия:

1. Выполните команду [az network nic show](/cli/azure/network/nic#show) с фильтром JMESPATH, чтобы отобразить текущее значение **enable-ip-forwarding** для параметра **enableIpForwarding**. Оно должно быть равно *false*.

    ```azurecli
    az network nic show \
    --resource-group testrg \
    --nname nicfw1 \
    --query 'enableIpForwarding' -o tsv
    ```

    Выходные данные:

        false

2. Чтобы включить IP-пересылку, выполните следующую команду:

    ```azurecli
    az network nic update \
    --resource-group testrg \
    --name nicfw1 \
    --ip-forwarding true
    ```

    Вы можете изучить выходные данные, передаваемые потоком в консоль, или просто повторить проверку для конкретного значения **enableIpForwarding**.

    ```azurecli
    az network nic show -g testrg -n nicfw1 --query 'enableIpForwarding' -o tsv
    ```

    Выходные данные:

        true

    Параметры

    **--ip-forwarding**: *true* или *false*.


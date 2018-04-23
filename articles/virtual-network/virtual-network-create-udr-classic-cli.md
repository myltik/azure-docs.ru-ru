---
title: Контроль маршрутизации в виртуальной сети Azure с помощью интерфейса командной строки (классическая модель) | Документация Майкрософт
description: Сведения об управлении маршрутизацией и виртуальными сетями с помощью интерфейса командной строки Azure в классической модели развертывания
services: virtual-network
documentationcenter: na
author: genli
manager: cshepard
editor: ''
tags: azure-service-management
ms.assetid: ca2b4638-8777-4d30-b972-eb790a7c804f
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2016
ms.author: genli
ms.openlocfilehash: 820387d35b912d01a8a296bd1a80edecf781cbb5
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/18/2018
---
# <a name="control-routing-and-use-virtual-appliances-classic-using-the-azure-cli"></a>Управление маршрутизацией и использование виртуальных модулей (классический режим) с помощью интерфейса командной строки Azure

> [!div class="op_single_selector"]
> * [PowerShell](tutorial-create-route-table-powershell.md)
> * [интерфейс командной строки Azure](tutorial-create-route-table-cli.md)
> * [PowerShell (классическая модель)](virtual-network-create-udr-classic-ps.md)
> * [Интерфейс командной строки (классическая модель)](virtual-network-create-udr-classic-cli.md)

[!INCLUDE [virtual-network-create-udr-intro-include.md](../../includes/virtual-network-create-udr-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

В этой статье рассматривается классическая модель развертывания. Вы также можете [управлять маршрутизацией и использовать виртуальные модули с помощью модели развертывания диспетчера ресурсов](tutorial-create-route-table-cli.md).

[!INCLUDE [virtual-network-create-udr-scenario-include.md](../../includes/virtual-network-create-udr-scenario-include.md)]

Для приведенных ниже примеров команд интерфейса командной строки Azure требуется уже созданная простая среда, основанная на приведенном выше сценарии. Для выполнения команд в том виде, в каком они представлены в данном документе, создайте среду, описанную в разделе о [создании виртуальной сети (классический режим) с помощью интерфейса командной строки Azure](virtual-networks-create-vnet-classic-cli.md).

[!INCLUDE [azure-cli-prerequisites-include.md](../../includes/azure-cli-prerequisites-include.md)]

## <a name="create-the-udr-for-the-front-end-subnet"></a>Создание определяемого пользователем маршрута для подсети переднего плана
Чтобы создать таблицу маршрутов и маршрут, необходимые для подсети переднего плана, на основании приведенного выше сценария, выполните следующие действия.

1. Чтобы переключиться в классический режим, выполните следующую команду:

    ```azurecli
    azure config mode asm
    ```

    Выходные данные:

        info:    New mode is asm

2. Чтобы создать таблицу маршрутов для интерфейсной подсети, выполните следующую команду:

    ```azurecli
    azure network route-table create -n UDR-FrontEnd -l uswest
    ```
   
    Выходные данные:
   
        info:    Executing command network route-table create
        info:    Creating route table "UDR-FrontEnd"
        info:    Getting route table "UDR-FrontEnd"
        data:    Name                            : UDR-FrontEnd
        data:    Location                        : West US
        info:    network route-table create command OK
   
    Параметры
   
   * **-l (или --location)**. Регион Azure, в котором будет создана группа безопасности сети. В нашем случае это *westus*.
   * **-n (или --name)**. Имя новой группы безопасности сети. В данном сценарии это *NSG-FrontEnd*.
3. Чтобы создать маршрут в таблице маршрутов для отправки всего трафика, предназначенного для серверной подсети (192.168.2.0/24), в виртуальную машину **FW1** (192.168.0.4), выполните следующую команду:

    ```azurecli
    azure network route-table route set -r UDR-FrontEnd -n RouteToBackEnd -a 192.168.2.0/24 -t VirtualAppliance -p 192.168.0.4
    ```

    Выходные данные:
   
        info:    Executing command network route-table route set
        info:    Getting route table "UDR-FrontEnd"
        info:    Setting route "RouteToBackEnd" in a route table "UDR-FrontEnd"
        info:    network route-table route set command OK
   
    Параметры
   
   * **-r (или --route-table-name)**. Имя таблицы маршрутов, куда будет добавлен маршрут. В данном сценарии это *UDR-FrontEnd*.
   * **-a (или --address-prefix)**. Префикс адреса для подсети, в которую адресованы пакеты. В данном сценарии это *192.168.2.0/24*.
   * **-t (или --next-hop-type)**. Тип объекта, куда будет отправляться трафик. Возможные значения: *VirtualAppliance*, *VirtualNetworkGateway*, *VNETLocal*, *Internet* или *None*.
   * **-p (или --next-hop-ip-address**). IP-адрес следующего прыжка. В нашем случае это *192.168.0.4*.
4. Чтобы сопоставить созданную таблицу маршрутов с подсетью **FrontEnd**, выполните следующую команду:

    ```azurecli
    azure network vnet subnet route-table add -t TestVNet -n FrontEnd -r UDR-FrontEnd
    ```
   
    Выходные данные:
   
        info:    Executing command network vnet subnet route-table add
        info:    Looking up the subnet "FrontEnd"
        info:    Looking up network configuration
        info:    Looking up network gateway route tables in virtual network "TestVNet" subnet "FrontEnd"
        info:    Associating route table "UDR-FrontEnd" and subnet "FrontEnd"
        info:    Looking up network gateway route tables in virtual network "TestVNet" subnet "FrontEnd"
        data:    Route table name                : UDR-FrontEnd
        data:      Location                      : West US
        data:      Routes:
        info:    network vnet subnet route-table add command OK    
   
    Параметры
   
   * **-t (или --vnet-name)**. Имя виртуальной сети, в которой расположена подсеть. В нашем случае это *TestVNet*.
   * **-n (или --subnet-name**). Имя подсети, куда будет добавлена таблица маршрутов. В данном сценарии это *FrontEnd*.

## <a name="create-the-udr-for-the-back-end-subnet"></a>Создание определяемого пользователем маршрута для серверной подсети
Чтобы создать таблицу маршрутов и маршрут, необходимые для серверной подсети, на основании сценария, выполните следующие действия:

1. Чтобы создать таблицу маршрутов для серверной подсети, выполните следующую команду:

    ```azurecli
    azure network route-table create -n UDR-BackEnd -l uswest
    ```

2. Чтобы создать маршрут в таблице маршрутов для отправки всего трафика, предназначенного для интерфейсной подсети (192.168.1.0/24), в виртуальную машину **FW1** (192.168.0.4), выполните следующую команду:

    ```azurecli
    azure network route-table route set -r UDR-BackEnd -n RouteToFrontEnd -a 192.168.1.0/24 -t VirtualAppliance -p 192.168.0.4
    ```

3. Чтобы сопоставить таблицу маршрутов с подсетью **BackEnd**, выполните следующую команду:

    ```azurecli
    azure network vnet subnet route-table add -t TestVNet -n BackEnd -r UDR-BackEnd
    ```


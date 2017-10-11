---
title: "Создание виртуальной сети с помощью Azure CLI 1.0 | Документация Майкрософт"
description: "Узнайте, как создать виртуальную сеть с помощью Azure CLI 1.0 в модели Resource Manager."
services: virtual-network
documentationcenter: 
author: jimdial
manager: carmonm
editor: 
tags: azure-resource-manager
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/16/2017
ms.author: jdial
ms.openlocfilehash: f0649c5c8c04dda72d2f147601efb37217f9bade
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/11/2017
---
# <a name="create-a-virtual-network-using-the-azure-cli"></a>Создание виртуальной сети с помощью интерфейса командной строки Azure

[!INCLUDE [virtual-networks-create-vnet-intro](../../includes/virtual-networks-create-vnet-intro-include.md)]

Azure предоставляет две модели развертывания: с помощью Azure Resource Manager и классическую. Для создания ресурсов корпорация Майкрософт рекомендует использовать модель развертывания с помощью Resource Manager. Дополнительные сведения о различиях между двумя моделями см. в статье [Azure Resource Manager vs. classic deployment: Understand deployment models and the state of your resources](../azure-resource-manager/resource-manager-deployment-model.md) (Azure Resource Manager и классическое развертывание. Общие сведения о моделях развертывания и состоянии ресурсов).

## <a name="cli-versions-to-complete-the-task"></a>Версии интерфейса командной строки для выполнения задачи
Вы можете выполнить задачу, используя одну из следующих версий интерфейса командной строки.

- [Azure CLI 2.0](virtual-networks-create-vnet-arm-cli.md) — это интерфейс командной строки нового поколения для модели развертывания Resource Manager.
- [Azure CLI 1.0](#create-a-virtual-network) — интерфейс командной строки для классической модели развертывания и модели развертывания Resource Manager (в этой статье).

 
[!INCLUDE [virtual-networks-create-vnet-scenario-include](../../includes/virtual-networks-create-vnet-scenario-include.md)]

## <a name="create-a-virtual-network"></a>Создать виртуальную сеть

Чтобы создать виртуальную сеть с помощью интерфейса командной строки Azure, сделайте следующее:

1. Установите и настройте интерфейс командной строки Azure, следуя инструкциям в [этой статье](../cli-install-nodejs.md).

2. Создайте виртуальную сеть и подсеть.

    ```azurecli
    azure network vnet create --vnet TestVNet -e 192.168.0.0 -i 16 -n FrontEnd -p 192.168.1.0 -r 24 -l "Central US"
    ```

    Ожидаемые выходные данные:
   
            info:    Executing command network vnet create
            + Looking up network configuration
            + Looking up locations
            + Setting network configuration
            info:    network vnet create command OK

    Используемые параметры:

   * **--vnet**. Имя виртуальной сети, которую нужно будет создать. В данном сценарии это *TestVNet*
   * **-e (или --address-space)**. Адресное пространство виртуальной сети. В данном сценарии это *192.168.0.0*
   * **-i (или -cidr)**. Маска подсети в формате CIDR. В данном сценарии это *16*.
   * **-n (или --subnet-name**). Имя первой подсети. В данном сценарии это *FrontEnd*.
   * **-p (или --subnet-start-ip)**. Начальный IP-адрес для подсети или адресное пространство подсети. В нашем сценарии это *192.168.1.0*.
   * **-r (или --subnet-cidr)**. Маска подсети в формате CIDR для подсети. В данном сценарии это *24*.
   * **-l (или --location)**. Регион Azure, в котором создается виртуальная сеть. В данном сценарии это *Central US*.

3. Создайте подсеть.

    ```azurecli
    azure network vnet subnet create -t TestVNet -n BackEnd -a 192.168.2.0/24
    ```
   
    Ожидаемые выходные данные:

            info:    Executing command network vnet subnet create
            + Looking up network configuration
            + Creating subnet "BackEnd"
            + Setting network configuration
            + Looking up the subnet "BackEnd"
            + Looking up network configuration
            data:    Name                            : BackEnd
            data:    Address prefix                  : 192.168.2.0/24
            info:    network vnet subnet create command OK

    Используемые параметры:

   * **-t (или --vnet-name**. Имя виртуальной сети, в которой будет создана подсеть. В данном сценарии это *TestVNet*.
   * **-n (или --name)**. Имя новой подсети. В нашем сценарии это *BackEnd*.
   * **-a (или --address-prefix)**. Блок подсети CIDR. В данном сценарии это *192.168.2.0/24*.
   
4. Вот как можно просмотреть свойства новой виртуальной сети.

    ```azurecli
    azure network vnet show
    ```
   
    Ожидаемые выходные данные:
   
            info:    Executing command network vnet show
            Virtual network name: TestVNet
            + Looking up the virtual network sites
            data:    Name                            : TestVNet
            data:    Location                        : Central US
            data:    State                           : Created
            data:    Address space                   : 192.168.0.0/16
            data:    Subnets:
            data:      Name                          : FrontEnd
            data:      Address prefix                : 192.168.1.0/24
            data:
            data:      Name                          : BackEnd
            data:      Address prefix                : 192.168.2.0/24
            data:
            info:    network vnet show command OK

## <a name="next-steps"></a>Дальнейшие действия

Инструкции по подключению:

- Сведения о подключении виртуальной машины к виртуальной сети см. в статье о [создании виртуальной машины Linux](../virtual-machines/linux/quick-create-cli.md). Вместо создания виртуальной сети и подсети с помощью действий, описанных в этой статье, виртуальную машину можно подключить к имеющейся виртуальной сети и подсети.
- Сведения об установке подключения между виртуальными сетями см. в статье [Настройка подключения между виртуальными сетями на портале Azure](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md).
- Сведения о подключении виртуальной сети к локальной сети с использованием виртуальной частной сети типа "сеть — сеть" или канала ExpressRoute см. в статьях [Добавление подключения типа "сеть — сеть" к виртуальной сети с помощью существующего подключения VPN-шлюза](../vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md) и [Подключение виртуальной сети к каналу ExpressRoute](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md).
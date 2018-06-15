---
title: включение файла
description: включение файла
services: virtual-network
author: genlin
ms.service: virtual-network
ms.topic: include
ms.date: 04/13/2018
ms.author: genli
ms.custom: include file
ms.openlocfilehash: 749cc5a5e5b8417abe602b7e37c103a26cc4dc03
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2018
ms.locfileid: "31805188"
---
## <a name="how-to-create-a-classic-vnet-using-azure-cli"></a>Создание классической виртуальной сети с помощью интерфейса командной строки Azure
Интерфейс командной строки Azure можно использовать для управления ресурсами Azure из командной строки с любого компьютера под управлением Windows, Linux или OSX.

1. Если вы еще не пользовались Azure CLI, ознакомьтесь со статьей [Установка и настройка CLI Azure](../articles/cli-install-nodejs.md) и следуйте инструкциям вплоть до выбора учетной записи Azure и подписки.
2. Чтобы создать виртуальную сеть и подсеть, выполните команду **azure network vnet create**:
   
            azure network vnet create --vnet TestVNet -e 192.168.0.0 -i 16 -n FrontEnd -p 192.168.1.0 -r 24 -l "Central US"
   
    Ожидаемые выходные данные:
   
            info:    Executing command network vnet create
            + Looking up network configuration
            + Looking up locations
            + Setting network configuration
            info:    network vnet create command OK
   
   * **--vnet**. Имя виртуальной сети, которую нужно будет создать. В нашем сценарии это *TestVNet*.
   * **-e (или --address-space)**. Адресное пространство виртуальной сети. В нашем сценарии это *192.168.0.0*.
   * **-i (или -cidr)**. Маска подсети в формате CIDR. В нашем сценарии это *16*.
   * **-n (или --subnet-name**). Имя первой подсети. В нашем сценарии это *FrontEnd*.
   * **-p (или --subnet-start-ip)**. Начальный IP-адрес для подсети или адресное пространство подсети. В нашем сценарии это *192.168.1.0*.
   * **-r (или --subnet-cidr)**. Маска подсети в формате CIDR для подсети. В нашем сценарии это *24*.
   * **-l (или --location)**. Регион Azure, в котором создается виртуальная сеть. В нашем сценарии это *Central US*.
3. Чтобы создать подсеть, выполните команду **azure network vnet subnet create**:
   
            azure network vnet subnet create -t TestVNet -n BackEnd -a 192.168.2.0/24
   
    Ожидаемый результат выполнения предыдущей команды.
   
            info:    Executing command network vnet subnet create
            + Looking up network configuration
            + Creating subnet "BackEnd"
            + Setting network configuration
            + Looking up the subnet "BackEnd"
            + Looking up network configuration
            data:    Name                            : BackEnd
            data:    Address prefix                  : 192.168.2.0/24
            info:    network vnet subnet create command OK
   
   * **-t (или --vnet-name**. Имя виртуальной сети, в которой будет создана подсеть. В нашем сценарии это *TestVNet*.
   * **-n (или --name)**. Имя новой подсети. В нашем сценарии это *BackEnd*.
   * **-a (или --address-prefix)**. Блок подсети CIDR. В нашем сценарии это *192.168.2.0/24*.
4. Чтобы просмотреть свойства новой виртуальной сети, выполните команду **azure network vnet show**:
   
            azure network vnet show
   
    Ожидаемый результат выполнения предыдущей команды:
   
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


---
title: Создание внутренней подсистемы балансировки нагрузки с помощью классической версии Azure CLI | Документация Майкрософт
description: Узнайте, как создать внутренний балансировщик нагрузки в классической модели развертывания с помощью интерфейса командной строки Azure.
services: load-balancer
documentationcenter: na
author: genlin
manager: cshepard
editor: ''
tags: azure-service-management
ms.assetid: becbbbde-a118-4269-9444-d3153f00bf34
ms.service: load-balancer
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/23/2017
ms.author: genli
ms.openlocfilehash: 8f0ac03ff7b749e47692d03d65502df0a19bb758
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/11/2018
ms.locfileid: "34068629"
---
# <a name="get-started-creating-an-internal-load-balancer-classic-using-the-azure-cli"></a>Приступая к созданию внутреннего балансировщика нагрузки (классический режим) с помощью Azure CLI

> [!div class="op_single_selector"]
> * [PowerShell](../load-balancer/load-balancer-get-started-ilb-classic-ps.md)
> * [Интерфейс командной строки Azure](../load-balancer/load-balancer-get-started-ilb-classic-cli.md)
> * [Облачные службы](../load-balancer/load-balancer-get-started-ilb-classic-cloud.md)

[!INCLUDE [load-balancer-get-started-ilb-intro-include.md](../../includes/load-balancer-get-started-ilb-intro-include.md)]

> [!IMPORTANT]
> В Azure предлагаются две модели развертывания для создания ресурсов и работы с ними: [модель Resource Manager и классическая модель](../azure-resource-manager/resource-manager-deployment-model.md).  В этой статье рассматривается использование классической модели развертывания. Для большинства новых развертываний Майкрософт рекомендует использовать модель диспетчера ресурсов. Узнайте, как [выполнить эти действия с помощью модели Resource Manager](load-balancer-get-started-ilb-arm-cli.md).

[!INCLUDE [load-balancer-get-started-ilb-scenario-include.md](../../includes/load-balancer-get-started-ilb-scenario-include.md)]

## <a name="to-create-an-internal-load-balancer-set-for-virtual-machines"></a>Создание набора внутренних балансировщиков нагрузки для виртуальных машин

Чтобы создать набор внутренних подсистем балансировки нагрузки и серверы, которые будут направлять в него свой трафик, сделайте следующее:

1. Создайте экземпляр внутренней подсистемы балансировки нагрузки. Он будет служить конечной точкой для входящего трафика, который необходимо распределять между серверами в созданном выше наборе.
2. Добавьте конечные точки, соответствующие виртуальным машинам, которые будут принимать входящий трафик.
3. Настройте серверы, чтобы они передавали трафик на виртуальный IP-адрес экземпляра внутренней подсистемы балансировки нагрузки.

## <a name="step-by-step-creating-an-internal-load-balancer-using-cli"></a>Пошаговая процедура создания внутреннего балансировщика нагрузки с помощью интерфейса командной строки

Это руководство описывает создание внутреннего балансировщика нагрузки на основе приведенного выше сценария.

1. Если вы еще не пользовались Azure CLI, ознакомьтесь со статьей [Установка и настройка CLI Azure](../cli-install-nodejs.md) и следуйте инструкциям вплоть до выбора учетной записи Azure и подписки.
2. Выполните команду **azure config mode** , чтобы переключиться в классический режим, как показано ниже.

    ```azurecli
    azure config mode asm
    ```

    Ожидаемые выходные данные:

        info:    New mode is asm

## <a name="create-endpoint-and-load-balancer-set"></a>Создание набора балансировщика нагрузки и конечной точки

Сценарий предполагает наличие виртуальных машин "DB1" и "DB2" в облачной службе с именем "mytestcloud". Обе виртуальные машины используют виртуальную сеть с именем "testvnet" с подсетью "subnet-1".

В этом руководстве создается набор внутренних подсистем балансировки нагрузки с использованием портов 1433 и 1433 в качестве частного и локального портов соответственно.

Это распространенный сценарий, где у вас есть виртуальные машины SQL в серверной части, использующие внутренний балансировщик нагрузки, чтобы гарантировать, что серверы базы данных не будут предоставляться напрямую, используя общедоступный IP-адрес.

### <a name="step-1"></a>Шаг 1

Создайте набор внутренних балансировщиков нагрузки с помощью `azure network service internal-load-balancer add`.

```azurecli
azure service internal-load-balancer add --serviceName mytestcloud --internalLBName ilbset --subnet-name subnet-1 --static-virtualnetwork-ipaddress 192.168.2.7
```

Дополнительные сведения см. в `azure service internal-load-balancer --help`.

Можно проверить свойства внутреннего балансировщика нагрузки с помощью команды `azure service internal-load-balancer list` *имя облачной службы*.

Ниже следует пример выходных данных:

    azure service internal-load-balancer list my-testcloud
    info:    Executing command service internal-load-balancer list
    + Getting cloud service deployment
    data:    Name    Type     SubnetName  StaticVirtualNetworkIPAddress
    data:    ------  -------  ----------  -----------------------------
    data:    ilbset  Private  subnet-1    192.168.2.7
    info:    service internal-load-balancer list command OK


### <a name="step-2"></a>Шаг 2

Настройте набор внутренних балансировщиков нагрузки при добавлении первой конечной точки. В этом шаге нужно связать конечную точку, виртуальную машину и порт пробы с набором внутренних подсистем балансировки нагрузки.

```azurecli
azure vm endpoint create db1 1433 --local-port 1433 --protocol tcp --probe-port 1433 --probe-protocol tcp --probe-interval 300 --probe-timeout 600 --internal-load-balancer-name ilbset
```

### <a name="step-3"></a>Шаг 3.

Проверьте конфигурацию балансировщика нагрузки с помощью `azure vm show` *имя виртуальной машины*

```azurecli
azure vm show DB1
```

Выходной файл будет выглядеть так:

    azure vm show DB1
    info:    Executing command vm show
    + Getting virtual machines
    data:    DNSName "mytestcloud.cloudapp.net"
    data:    Location "East US 2"
    data:    VMName "DB1"
    data:    IPAddress "192.168.2.4"
    data:    InstanceStatus "ReadyRole"
    data:    InstanceSize "Standard_D1"
    data:    Image "a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-R2-20151022-en.us-127GB.vhd"
    data:    OSDisk hostCaching "ReadWrite"
    data:    OSDisk name "db1-DB1-0-201511120457370846"
    data:    OSDisk mediaLink "https://XXXX.blob.core.windows.net/vhd"
    data:    OSDisk sourceImageName "a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-R2-20151022-en.us-127GB.vhd"
    data:    OSDisk operatingSystem "Windows"
    data:    OSDisk iOType "Standard"
    data:    ReservedIPName ""
    data:    VirtualIPAddresses 0 address "137.116.64.107"
    data:    VirtualIPAddresses 0 name "db1ContractContract"
    data:    VirtualIPAddresses 0 isDnsProgrammed true
    data:    VirtualIPAddresses 1 address "192.168.2.7"
    data:    VirtualIPAddresses 1 name "ilbset"
    data:    Network Endpoints 0 localPort 5986
    data:    Network Endpoints 0 name "PowerShell"
    data:    Network Endpoints 0 port 5986
    data:    Network Endpoints 0 protocol "tcp"
    data:    Network Endpoints 0 virtualIPAddress "137.116.64.107"
    data:    Network Endpoints 0 enableDirectServerReturn false
    data:    Network Endpoints 1 localPort 3389
    data:    Network Endpoints 1 name "Remote Desktop"
    data:    Network Endpoints 1 port 60173
    data:    Network Endpoints 1 protocol "tcp"
    data:    Network Endpoints 1 virtualIPAddress "137.116.64.107"
    data:    Network Endpoints 1 enableDirectServerReturn false
    data:    Network Endpoints 2 localPort 1433
    data:    Network Endpoints 2 name "tcp-1433-1433"
    data:    Network Endpoints 2 port 1433
    data:    Network Endpoints 2 loadBalancerProbe port 1433
    data:    Network Endpoints 2 loadBalancerProbe protocol "tcp"
    data:    Network Endpoints 2 loadBalancerProbe intervalInSeconds 300
    data:    Network Endpoints 2 loadBalancerProbe timeoutInSeconds 600
    data:    Network Endpoints 2 protocol "tcp"
    data:    Network Endpoints 2 virtualIPAddress "192.168.2.7"
    data:    Network Endpoints 2 enableDirectServerReturn false
    data:    Network Endpoints 2 loadBalancerName "ilbset"
    info:    vm show command OK

## <a name="create-a-remote-desktop-endpoint-for-a-virtual-machine"></a>Создание конечной точки удаленного рабочего стола для виртуальной машины

Вы можете создать конечную точку удаленного рабочего стола для пересылки трафика с общего порта на локальный порт для конкретной виртуальной машины с помощью `azure vm endpoint create`.

```azurecli
azure vm endpoint create web1 54580 -k 3389
```

## <a name="remove-virtual-machine-from-load-balancer"></a>Удаление виртуальной машины из балансировщика нагрузки

Можно удалить виртуальную машину из набора внутренних балансировщиков нагрузки путем удаления соответствующей конечной точки. После удаления конечной точки виртуальная машина больше не входит в набор балансировщиков нагрузки.

Используя приведенный выше пример, можно удалить конечную точку, созданную для виртуальной машины "DB1", из внутреннего балансировщика нагрузки "ilbset" с помощью команды `azure vm endpoint delete`.

```azurecli
azure vm endpoint delete DB1 tcp-1433-1433
```

Дополнительные сведения см. в `azure vm endpoint --help`.

## <a name="next-steps"></a>Дополнительная информация

[Настройка режима распределения балансировщика нагрузки с помощью соответствия исходному IP-адресу](load-balancer-distribution-mode.md)

[Настройка параметров времени ожидания простоя TCP для подсистемы балансировки нагрузки](load-balancer-tcp-idle-timeout.md)

---
title: Создание доступной в Интернете внутренней подсистемы балансировки нагрузки с помощью Azure CLI | Документация Майкрософт
description: Узнайте, как создать балансировщик нагрузки для Интернета в классической модели развертывания с помощью интерфейса командной строки Azure.
services: load-balancer
documentationcenter: na
author: genlin
manager: cshepard
tags: azure-service-management
ms.assetid: e433a824-4a8a-44d2-8765-a74f52d4e584
ms.service: load-balancer
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/23/2017
ms.author: genli
ms.openlocfilehash: bacf135da25a5315e61922179db9a29fa8a152f1
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/11/2018
---
# <a name="get-started-creating-an-internet-facing-load-balancer-classic-in-the-azure-cli"></a>Приступая к созданию балансировщика нагрузки (классический режим) для Интернета в Azure CLI

> [!div class="op_single_selector"]
> * [PowerShell](../load-balancer/load-balancer-get-started-internet-classic-ps.md)
> * [Интерфейс командной строки Azure](../load-balancer/load-balancer-get-started-internet-classic-cli.md)
> * [облачных служб Azure](../load-balancer/load-balancer-get-started-internet-classic-cloud.md)

[!INCLUDE [load-balancer-get-started-internet-intro-include.md](../../includes/load-balancer-get-started-internet-intro-include.md)]

> [!IMPORTANT]
> Прежде чем приступить к работе с ресурсами Azure, обратите внимание на то, что в настоящее время в Azure существует две модели развертывания: классическая модель развертывания и модель развертывания с помощью Azure Resource Manager. Обязательно изучите [модели и инструменты развертывания](../azure-classic-rm.md) , прежде чем приступить к работе с какими бы то ни было ресурсами Azure. Для просмотра документации о средствах развертывания выбирайте соответствующие вкладки в верхней части данной статьи. В этой статье рассматривается классическая модель развертывания. Вы также можете [узнать, как создать балансировщик нагрузки для Интернета с помощью диспетчера ресурсов Azure](load-balancer-get-started-internet-arm-ps.md).

[!INCLUDE [load-balancer-get-started-internet-scenario-include.md](../../includes/load-balancer-get-started-internet-scenario-include.md)]

## <a name="create-an-internet-facing-load-balancer-using-cli"></a>Создание подсистемы балансировки нагрузки для Интернета с помощью CLI

Это руководство описывает создание балансировщика нагрузки для Интернета на основе приведенного выше сценария.

1. Если вы еще не пользовались Azure CLI, ознакомьтесь со статьей [Установка и настройка CLI Azure](../cli-install-nodejs.md) и следуйте инструкциям вплоть до выбора учетной записи Azure и подписки.
2. Выполните команду **azure config mode** , чтобы переключиться в классический режим, как показано ниже.

    ```azurecli
    azure config mode asm
    ```

    Ожидаемые выходные данные:

        info:    New mode is asm

## <a name="create-endpoint-and-load-balancer-set"></a>Создание набора балансировщика нагрузки и конечной точки

Сценарий предполагает, что были созданы виртуальные машины "web1" и "web2".
В этом руководстве будет создан набор балансировщика нагрузки с использованием общего порта 80 и локального порта 80. Для пробы также задается порт 80, а набору балансировщика нагрузки присваивается имя lbset.

### <a name="step-1"></a>Шаг 1

Создайте первую конечную точку и набор балансировщика нагрузки, используя `azure network vm endpoint create` для виртуальной машины web1.

```azurecli
azure vm endpoint create web1 80 --local-port 80 --protocol tcp --probe-port 80 --load-balanced-set-name lbset
```

### <a name="step-2"></a>Шаг 2

Добавьте в набор балансировщика нагрузки вторую виртуальную машину "web2".

```azurecli
azure vm endpoint create web2 80 --local-port 80 --protocol tcp --probe-port 80 --load-balanced-set-name lbset
```

### <a name="step-3"></a>Шаг 3.

Проверьте конфигурацию балансировщика нагрузки с помощью `azure vm show` .

```azurecli
azure vm show web1
```

Выходные данные должны выглядеть следующим образом:

    data:    DNSName "contoso.cloudapp.net"
    data:    Location "East US"
    data:    VMName "web1"
    data:    IPAddress "10.0.0.5"
    data:    InstanceStatus "ReadyRole"
    data:    InstanceSize "Standard_D1"
    data:    Image "a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-R2-2015
    6-en.us-127GB.vhd"
    data:    OSDisk hostCaching "ReadWrite"
    data:    OSDisk name "joaoma-1-web1-0-201509251804250879"
    data:    OSDisk mediaLink "https://XXXXXXXXXXXXXXX.blob.core.windows.
    /vhds/joaomatest-web1-2015-09-25.vhd"
    data:    OSDisk sourceImageName "a699494373c04fc0bc8f2bb1389d6106__Windows-Se
    r-2012-R2-20150916-en.us-127GB.vhd"
    data:    OSDisk operatingSystem "Windows"
    data:    OSDisk iOType "Standard"
    data:    ReservedIPName ""
    data:    VirtualIPAddresses 0 address "XXXXXXXXXXXXXXXX"
    data:    VirtualIPAddresses 0 name "XXXXXXXXXXXXXXXXXXXX"
    data:    VirtualIPAddresses 0 isDnsProgrammed true
    data:    Network Endpoints 0 loadBalancedEndpointSetName "lbset"
    data:    Network Endpoints 0 localPort 80
    data:    Network Endpoints 0 name "tcp-80-80"
    data:    Network Endpoints 0 port 80
    data:    Network Endpoints 0 loadBalancerProbe port 80
    data:    Network Endpoints 0 loadBalancerProbe protocol "tcp"
    data:    Network Endpoints 0 loadBalancerProbe intervalInSeconds 15
    data:    Network Endpoints 0 loadBalancerProbe timeoutInSeconds 31
    data:    Network Endpoints 0 protocol "tcp"
    data:    Network Endpoints 0 virtualIPAddress "XXXXXXXXXXXX"
    data:    Network Endpoints 0 enableDirectServerReturn false
    data:    Network Endpoints 1 localPort 5986
    data:    Network Endpoints 1 name "PowerShell"
    data:    Network Endpoints 1 port 5986
    data:    Network Endpoints 1 protocol "tcp"
    data:    Network Endpoints 1 virtualIPAddress "XXXXXXXXXXXX"
    data:    Network Endpoints 1 enableDirectServerReturn false
    data:    Network Endpoints 2 localPort 3389
    data:    Network Endpoints 2 name "Remote Desktop"
    data:    Network Endpoints 2 port 58081
    info:    vm show command OK

## <a name="create-a-remote-desktop-endpoint-for-a-virtual-machine"></a>Создание конечной точки удаленного рабочего стола для виртуальной машины

Вы можете создать конечную точку удаленного рабочего стола для пересылки трафика с общего порта на локальный порт для конкретной виртуальной машины с помощью `azure vm endpoint create`.

```azurecli
azure vm endpoint create web1 54580 -k 3389
```

## <a name="remove-virtual-machine-from-load-balancer"></a>Удаление виртуальной машины из балансировщика нагрузки

Вам необходимо удалить конечную точку, сопоставленную с набором балансировщика нагрузки, из виртуальной машины. После удаления конечной точки виртуальная машина больше не входит в набор балансировщика нагрузки.

Используя приведенный выше пример, можно удалить конечную точку, созданную для виртуальной машины "web1", из "lbset" балансировщика нагрузки с помощью команды `azure vm endpoint delete`.

```azurecli
azure vm endpoint delete web1 tcp-80-80
```

> [!NOTE]
> Вы можете отобразить дополнительные параметры для управления конечными точками с помощью команды `azure vm endpoint --help`.

## <a name="next-steps"></a>Дополнительная информация

[Приступая к настройке внутренней подсистемы балансировки нагрузки](load-balancer-get-started-ilb-arm-ps.md)

[Настройка режима распределения подсистемы балансировки нагрузки](load-balancer-distribution-mode.md)

[Настройка параметров времени ожидания простоя TCP для подсистемы балансировки нагрузки](load-balancer-tcp-idle-timeout.md)

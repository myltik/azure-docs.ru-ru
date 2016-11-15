---
title: "Создание подсистемы балансировки нагрузки с помощью Azure CLI в классической модели развертывания | Документация Майкрософт"
description: "Узнайте, как создать внутренний балансировщик нагрузки в классической модели развертывания с помощью интерфейса командной строки Azure."
services: load-balancer
documentationcenter: na
author: sdwheeler
manager: carmonm
editor: 
tags: azure-service-management
ms.assetid: becbbbde-a118-4269-9444-d3153f00bf34
ms.service: load-balancer
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/09/2016
ms.author: sewhee
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 4364d3bcdffd278bef35b224a8e22062814ca490


---
# <a name="get-started-creating-an-internal-load-balancer-classic-using-the-azure-cli"></a>Приступая к созданию внутреннего балансировщика нагрузки (классический режим) с помощью Azure CLI
[!INCLUDE [load-balancer-get-started-ilb-classic-selectors-include.md](../../includes/load-balancer-get-started-ilb-classic-selectors-include.md)]

[!INCLUDE [load-balancer-get-started-ilb-intro-include.md](../../includes/load-balancer-get-started-ilb-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-classic-include.md)]

Узнайте, как [выполнить эти действия с помощью модели Resource Manager](load-balancer-get-started-ilb-arm-cli.md).

[!INCLUDE [load-balancer-get-started-ilb-scenario-include.md](../../includes/load-balancer-get-started-ilb-scenario-include.md)]

## <a name="to-create-an-internal-load-balancer-set-for-virtual-machines"></a>Создание набора внутренних балансировщиков нагрузки для виртуальных машин
Чтобы создать набор внутренних балансировщиков нагрузки и серверы, которые будут направлять на него свой трафик, выполните следующее:

1. Создайте экземпляр внутренней балансировки нагрузки. Он будет служить конечной точкой для входящего трафика, который необходимо распределять по серверам в наборе балансировки нагрузки.
2. Добавьте конечные точки, соответствующие виртуальным машинам, которые будут принимать входящий трафик.
3. Настройте серверы, которые будут отправлять трафик для балансировки нагрузки, таким образом, чтобы они передавали трафик на виртуальный IP-адрес экземпляра внутренней балансировки нагрузки.

## <a name="step-by-step-creating-an-internal-load-balancer-using-cli"></a>Пошаговая процедура создания внутреннего балансировщика нагрузки с помощью интерфейса командной строки
Это руководство описывает создание внутреннего балансировщика нагрузки на основе приведенного выше сценария.

1. Если вы еще не пользовались Azure CLI, ознакомьтесь со статьей [Установка и настройка CLI Azure](../xplat-cli-install.md) и следуйте инструкциям вплоть до выбора учетной записи Azure и подписки.
2. Выполните команду **azure config mode** , чтобы переключиться в классический режим, как показано ниже.
   
        azure config mode asm
   
    Ожидаемые выходные данные:
   
        info:    New mode is asm

## <a name="create-endpoint-and-load-balancer-set"></a>Создание набора балансировщика нагрузки и конечной точки
Сценарий предполагает наличие виртуальных машин "DB1" и "DB2" в облачной службе с именем "mytestcloud". Обе виртуальные машины используют виртуальную сеть с именем "testvnet" с подсетью "subnet-1".

В этом руководстве будет создан набор внутренних балансировщиков нагрузки с использованием порта 1433 в качестве частного порта и порта 1433 в качестве локального порта.

Это распространенный сценарий, где у вас есть виртуальные машины SQL в серверной части, использующие внутренний балансировщик нагрузки, чтобы гарантировать, что серверы базы данных не будут предоставляться напрямую, используя общедоступный IP-адрес.

### <a name="step-1"></a>Шаг 1
Создайте набор внутренних балансировщиков нагрузки с помощью `azure network service internal-load-balancer add`.

     azure service internal-load-balancer add -r mytestcloud -n ilbset -t subnet-1 -a 192.168.2.7

Используемые параметры:

**-r** — имя облачной службы;<BR>
**-n** — имя внутренней подсистемы балансировки нагрузки;<BR>
**-t** — имя подсети (той же подсети с виртуальными машинами для добавления во внутреннюю подсистему балансировки нагрузки);<BR>
**-a** — (необязательно) добавление виртуальной машины со статическим частным IP-адресом.<BR>

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


## <a name="step-2"></a>Шаг 2
Настройте набор внутренних балансировщиков нагрузки при добавлении первой конечной точки. В этом шаге нужно связать конечную точку, виртуальную машину и порт пробы с набором внутренних балансировщиков нагрузки.

    azure vm endpoint create db1 1433 -k 1433 tcp -t 1433 -r tcp -e 300 -f 600 -i ilbset

Используемые параметры:

**-k** — порт локальной виртуальной машины;<BR>
**-t** — порт пробы;<BR>
**-r** — протокол пробы;<BR>
**-e** — интервал между пробами в секундах;<BR>
**-f** — время ожидания в секундах; <BR>
**-i** — имя внутренней подсистемы балансировки нагрузки. <BR>

## <a name="step-3"></a>Шаг 3.
Проверьте конфигурацию балансировщика нагрузки с помощью `azure vm show` *имя виртуальной машины*

    azure vm show DB1

Выходные данные должны выглядеть следующим образом:

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

    azure vm endpoint create web1 54580 -k 3389


## <a name="remove-virtual-machine-from-load-balancer"></a>Удаление виртуальной машины из балансировщика нагрузки
Можно удалить виртуальную машину из набора внутренних балансировщиков нагрузки путем удаления соответствующей конечной точки. После удаления конечной точки виртуальная машина больше не входит в набор балансировщиков нагрузки.

 Используя приведенный выше пример, можно удалить конечную точку, созданную для виртуальной машины "DB1", из внутреннего балансировщика нагрузки "ilbset" с помощью команды `azure vm endpoint delete`.

    azure vm endpoint delete DB1 tcp-1433-1433


Дополнительные сведения см. в `azure vm endpoint --help`.

## <a name="next-steps"></a>Дальнейшие действия
[Настройка режима распределения балансировщика нагрузки с помощью соответствия исходному IP-адресу](load-balancer-distribution-mode.md)

[Настройка параметров времени ожидания простоя TCP для подсистемы балансировки нагрузки](load-balancer-tcp-idle-timeout.md)




<!--HONumber=Nov16_HO2-->



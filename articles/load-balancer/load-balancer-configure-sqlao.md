---
title: "Настройка балансировки нагрузки для SQL Server AlwaysOn | Документы Microsoft"
description: "Настройка балансировки нагрузки для работы с SQL Server Always On и узнайте, как использовать PowerShell для создания балансировки нагрузки для реализации SQL Server"
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
ms.assetid: d7bc3790-47d3-4e95-887c-c533011e4afd
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: 5e890f8314c8f191dbfa6c6818d810b91d0e829d
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/11/2017
---
# <a name="configure-a-load-balancer-for-sql-server-always-on"></a>Настройка балансировки нагрузки для SQL Server AlwaysOn

[!INCLUDE [load-balancer-basic-sku-include.md](../../includes/load-balancer-basic-sku-include.md)]

Группы обеспечения доступности AlwaysOn в SQL Server теперь может работать с внутренней подсистемы балансировки нагрузки. Группа доступности — решение основной SQL Server для высокого уровня доступности и аварийного восстановления. Прослушиватель группы доступности позволяет клиентским приложениям без проблем подключаться к первичной реплике, независимо от числа реплик в конфигурации.

Имя прослушивателя (DNS) сопоставляется с балансировкой нагрузки IP-адресом. Подсистема балансировки нагрузки Azure направляет входящий трафик только на основной сервер в наборе реплик.

Поддержка балансировщик внутренней нагрузки можно использовать для конечных точек SQL Server Always On (прослушиватель). Теперь вы можете контролировать доступность прослушивателя. IP-адрес с балансировкой нагрузки можно выбрать из определенной подсети в виртуальной сети.

С помощью внутреннего загрузить балансировки прослушивание конечной точки SQL Server (например, Server = tcp:ListenerName, 1433, базы данных = имя базы данных), доступен только для:

* Службы и виртуальные машины в той же виртуальной сети.
* Службы и виртуальных машин из подключенных локальным сетям.
* Службы и виртуальных машин из взаимосвязанных виртуальных сетей.

![Внутренняя Подсистема балансировки нагрузки SQL Server Always On](./media/load-balancer-configure-sqlao/sqlao1.png)

## <a name="add-an-internal-load-balancer-to-the-service"></a>Добавления внутренней подсистемы балансировки нагрузки для службы

1. В следующем примере настройки виртуальной сети, которая содержит подсеть с именем «подсеть-1":

    ```powershell
    Add-AzureInternalLoadBalancer -InternalLoadBalancerName ILB_SQL_AO -SubnetName Subnet-1 -ServiceName SqlSvc
    ```
2. Добавление конечных точек с балансировкой нагрузки для внутренней подсистемы балансировки нагрузки на каждой виртуальной Машине.

    ```powershell
    Get-AzureVM -ServiceName SqlSvc -Name sqlsvc1 | Add-AzureEndpoint -Name "LisEUep" -LBSetName "ILBSet1" -Protocol tcp -LocalPort 1433 -PublicPort 1433 -ProbePort 59999 -ProbeProtocol tcp -ProbeIntervalInSeconds 10 -
    DirectServerReturn $true -InternalLoadBalancerName ILB_SQL_AO | Update-AzureVM

    Get-AzureVM -ServiceName SqlSvc -Name sqlsvc2 | Add-AzureEndpoint -Name "LisEUep" -LBSetName "ILBSet1" -Protocol tcp -LocalPort 1433 -PublicPort 1433 -ProbePort 59999 -ProbeProtocol tcp -ProbeIntervalInSeconds 10 -DirectServerReturn $true -InternalLoadBalancerName ILB_SQL_AO | Update-AzureVM
    ```

    В предыдущем примере имеется две виртуальные машины называется «sqlsvc1» и «sqlsvc2», работающих в облачной службе «Sqlsvc». После создания внутренний балансировщик нагрузки с `DirectServerReturn` переключения, добавьте конечные точки с балансировкой нагрузки для внутренней подсистемы балансировки нагрузки. Конечные точки с балансировкой нагрузки позволяют SQL Server для настройки прослушивателей группы доступности.

Дополнительные сведения о SQL Server Always On см. в разделе [настроить внутренний балансировщик нагрузки для группы доступности Always On в Azure](../virtual-machines/windows/sql/virtual-machines-windows-portal-sql-alwayson-int-listener.md).

## <a name="see-also"></a>См. также
* [Перед началом настройки внешняя Подсистема балансировки нагрузки](load-balancer-get-started-internet-arm-ps.md)
* [Приступая к настройке внутренней подсистемы балансировки нагрузки](load-balancer-get-started-ilb-arm-ps.md)
* [Настройка режима распределения подсистемы балансировки нагрузки](load-balancer-distribution-mode.md)
* [Настройка параметров времени ожидания простоя TCP для подсистемы балансировки нагрузки](load-balancer-tcp-idle-timeout.md)

---
title: "Настройка подсистемы балансировки нагрузки для групп доступности Always On SQL Server | Документация Майкрософт"
description: "Узнайте, как настроить подсистему балансировки нагрузки для работы с группами доступности Always On SQL Server и создать подсистему балансировки нагрузки для реализации SQL Server с помощью PowerShell."
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
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/11/2017
---
# <a name="configure-a-load-balancer-for-sql-server-always-on"></a>Настройка подсистемы балансировки нагрузки для групп доступности Always On SQL Server

[!INCLUDE [load-balancer-basic-sku-include.md](../../includes/load-balancer-basic-sku-include.md)]

Теперь группы доступности Always On на платформе SQL Server можно запускать с помощью внутренней подсистемы балансировки нагрузки. Группа доступности — это основное решение SQL Server, обеспечивающее высокий уровень доступности и аварийное восстановление. Прослушиватель группы доступности позволяет клиентским приложениям с легкостью подключаться к первичной реплике, независимо от числа реплик в конфигурации.

Имя прослушивателя (DNS) сопоставляется с IP-адресом с балансировкой нагрузки. Azure Load Balancer направляет входящий трафик на сервер-источник в наборе реплик.

Поддержку внутренней подсистемы балансировки нагрузки можно использовать для конечных точек (прослушивателя) групп доступности Always On SQL Server. Теперь вы можете контролировать доступность прослушивателя. IP-адрес с балансировкой нагрузки можно выбрать из определенной подсети в виртуальной сети.

При использовании внутренней подсистемы балансировки нагрузки для прослушивателя к конечной точке сервера SQL Server (например, Server=tcp:ListenerName,1433;Database=DatabaseName) могут получать доступ только:

* службы и виртуальные машины в одной виртуальной сети;
* службы и виртуальные машины из подключенных локальных сетей;
* службы и виртуальные машины из взаимосвязанных виртуальных сетей.

![Внутренняя подсистема балансировки нагрузки для групп доступности Always On SQL Server](./media/load-balancer-configure-sqlao/sqlao1.png)

## <a name="add-an-internal-load-balancer-to-the-service"></a>Добавление внутренней подсистемы балансировки нагрузки в службу

1. В следующем примере мы настроим виртуальную сеть, содержащую подсеть с именем Subnet-1:

    ```powershell
    Add-AzureInternalLoadBalancer -InternalLoadBalancerName ILB_SQL_AO -SubnetName Subnet-1 -ServiceName SqlSvc
    ```
2. Добавьте конечные точки с балансировкой нагрузки для внутренней подсистемы балансировки нагрузки на каждой виртуальной машине:

    ```powershell
    Get-AzureVM -ServiceName SqlSvc -Name sqlsvc1 | Add-AzureEndpoint -Name "LisEUep" -LBSetName "ILBSet1" -Protocol tcp -LocalPort 1433 -PublicPort 1433 -ProbePort 59999 -ProbeProtocol tcp -ProbeIntervalInSeconds 10 -
    DirectServerReturn $true -InternalLoadBalancerName ILB_SQL_AO | Update-AzureVM

    Get-AzureVM -ServiceName SqlSvc -Name sqlsvc2 | Add-AzureEndpoint -Name "LisEUep" -LBSetName "ILBSet1" -Protocol tcp -LocalPort 1433 -PublicPort 1433 -ProbePort 59999 -ProbeProtocol tcp -ProbeIntervalInSeconds 10 -DirectServerReturn $true -InternalLoadBalancerName ILB_SQL_AO | Update-AzureVM
    ```

    В предыдущем примере вы использовали две виртуальные машины (sqlsvc1 и sqlsvc2) в облачной службе Sqlsvc. Создав внутреннюю подсистему балансировки нагрузки с помощью параметра `DirectServerReturn`, добавьте в нее конечные точки с балансировкой нагрузки. Это позволит SQL Server настроить прослушиватели групп доступности.

Дополнительные сведения о группах доступности Always On SQL Server см. в статье [Настройка подсистемы балансировки нагрузки для группы доступности AlwaysOn в Azure](../virtual-machines/windows/sql/virtual-machines-windows-portal-sql-alwayson-int-listener.md).

## <a name="see-also"></a>См. также
* [Создание общедоступной подсистемы балансировки нагрузки в Resource Manager с помощью PowerShell](load-balancer-get-started-internet-arm-ps.md)
* [Приступая к настройке внутренней подсистемы балансировки нагрузки](load-balancer-get-started-ilb-arm-ps.md)
* [Настройка режима распределения подсистемы балансировки нагрузки](load-balancer-distribution-mode.md)
* [Настройка параметров времени ожидания простоя TCP для подсистемы балансировки нагрузки](load-balancer-tcp-idle-timeout.md)

---
title: "Настройка балансировщика нагрузки для SQL AlwaysOn | Документация Майкрософт"
description: "Информация о настройке подсистемы балансировки нагрузки для работы с AlwaysOn SQL и использовании Powershell для создания подсистемы балансировки нагрузки для реализации SQL"
services: load-balancer
documentationcenter: na
author: kumudd
manager: timlt
ms.assetid: d7bc3790-47d3-4e95-887c-c533011e4afd
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/24/2016
ms.author: kumud
translationtype: Human Translation
ms.sourcegitcommit: 1a1c3c15c51b1e441f21158510e92cc8de057352
ms.openlocfilehash: 75f05f003b691ee6464168453fa7935f1fae166e

---

# <a name="configure-load-balancer-for-sql-always-on"></a>Настройка подсистемы балансировки нагрузки для AlwaysOn SQL

Группы доступности AlwaysOn SQL Server теперь можно использовать совместно с внутренней подсистемой балансировки нагрузки. Группа доступности — это флагманское решение SQL Server, обеспечивающее высокий уровень доступности и аварийное восстановление. Прослушиватель групп доступности позволяет клиентским приложениям с легкостью подключаться к первичной реплике, независимо от числа реплик в конфигурации.

Имя прослушивателя (DNS) сопоставляется с IP-адресом с балансировкой нагрузки, и балансировщик нагрузки Azure направляет входящий трафик только на сервер-источник в наборе реплик.

Поддержку внутренней подсистемы балансировки нагрузки можно использовать для конечных точек (прослушивателя) AlwaysOn SQL Server. Теперь вы можете контролировать доступность прослушивателя и выбирать IP-адрес с балансировкой нагрузки конкретной подсети в вашей виртуальной сети (VNet).

При использовании внутренней подсистемы балансировки нагрузки для прослушивателя к конечной точке сервера SQL Server (например, Server=tcp:ListenerName,1433;Database=DatabaseName) могут получать доступ только:

* Службы и виртуальные машины в одной виртуальной сети
* Службы и виртуальные машины из подключенной локальной сети
* Службы и виртуальные машины из взаимосвязанных виртуальных сетей

![ILB_SQLAO_NewPic](./media/load-balancer-configure-sqlao/sqlao1.png)

Рис. 1. Группа SQL AlwaysOn, в которой настроена подсистема балансировки нагрузки с выходом в Интернет

## <a name="add-internal-load-balancer-to-the-service"></a>Добавление внутренней подсистемы балансировки нагрузки в службу

1. В следующем примере мы настроим виртуальную сеть, содержащую подсеть с именем Subnet-1.

    ```powershell
    Add-AzureInternalLoadBalancer -InternalLoadBalancerName ILB_SQL_AO -SubnetName Subnet-1 -ServiceName SqlSvc
    ```
2. Добавление конечных точек с балансировкой нагрузки для внутренней подсистемы балансировки нагрузки на каждой виртуальной машине

    ```powershell
    Get-AzureVM -ServiceName SqlSvc -Name sqlsvc1 | Add-AzureEndpoint -Name "LisEUep" -LBSetName "ILBSet1" -Protocol tcp -LocalPort 1433 -PublicPort 1433 -ProbePort 59999 -ProbeProtocol tcp -ProbeIntervalInSeconds 10 -
    DirectServerReturn $true -InternalLoadBalancerName ILB_SQL_AO | Update-AzureVM

    Get-AzureVM -ServiceName SqlSvc -Name sqlsvc2 | Add-AzureEndpoint -Name "LisEUep" -LBSetName "ILBSet1" -Protocol tcp -LocalPort 1433 -PublicPort 1433 -ProbePort 59999 -ProbeProtocol tcp -ProbeIntervalInSeconds 10 -DirectServerReturn $true -InternalLoadBalancerName ILB_SQL_AO | Update-AzureVM
    ```

    В приведенном выше примере две виртуальные машины с именами sqlsvc1 и sqlsvc2 работают в облачной службе Sqlsvc. Создав внутренний балансировщик нагрузки с параметром `DirectServerReturn`, вы добавите в него конечные точки с балансировкой нагрузки, чтобы разрешить SQL настраивать прослушиватели для групп доступности.

Подробные указания см. в статье [Настройка внутреннего балансировщика нагрузки для группы доступности AlwaysOn в Azure](../virtual-machines/virtual-machines-windows-portal-sql-alwayson-int-listener.md?toc=%2fazure%2fload-balancer%2ftoc.json).

## <a name="see-also"></a>См. также
[Приступая к настройке подсистемы балансировки нагрузки, доступной в Интернете](load-balancer-get-started-internet-arm-ps.md)

[Приступая к настройке внутренней подсистемы балансировки нагрузки](load-balancer-get-started-ilb-arm-ps.md)

[Настройка режима распределения подсистемы балансировки нагрузки](load-balancer-distribution-mode.md)

[Настройка параметров времени ожидания простоя TCP для подсистемы балансировки нагрузки](load-balancer-tcp-idle-timeout.md)



<!--HONumber=Nov16_HO3-->



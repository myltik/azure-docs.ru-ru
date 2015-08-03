<properties 
   pageTitle="Настройка подсистемы балансировки нагрузки для AlwaysOn SQL | Microsoft Azure"
   description="Информация о настройке подсистемы балансировки нагрузки для работы с AlwaysOn SQL и использовании Powershell для создания подсистемы балансировки нагрузки для реализации SQL"
   services="load-balancer"
   documentationCenter="na"
   authors="joaoma"
   manager="adinah"
   editor="tysonn" />
<tags 
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="05/01/2015"
   ms.author="joaoma" />

# AlwaysOn SQL

Группы доступности AlwaysOn SQL Server теперь можно использовать совместно с внутренней подсистемой балансировки нагрузки. Группа доступности — это флагманское решение SQL Server, обеспечивающее высокий уровень доступности и аварийное восстановление. Прослушиватель групп доступности позволяет клиентским приложениям с легкостью подключаться к первичной реплике, независимо от числа реплик в конфигурации.

Имя прослушивателя (DNS) сопоставляется с IP-адресом с балансировкой нагрузки, и подсистема балансировки нагрузки Azure направляет входящий трафик только на сервер-источник в наборе реплик.


Поддержку внутренней подсистемы балансировки нагрузки можно использовать для конечных точек (прослушивателя) AlwaysOn SQL Server. Теперь вы можете контролировать доступность прослушивателя и выбирать IP-адрес с балансировкой нагрузки конкретной подсети в вашей виртуальной сети (VNet).

При использовании внутренней подсистемы балансировки нагрузки для прослушивателя к конечной точке сервера SQL Server (например, Server=tcp:ListenerName,1433;Database=DatabaseName) могут получать доступ только:

службы и виртуальные машины, расположенные в одной виртуальной сети, службы и виртуальные машины, расположенные в локальной сети, и службы и виртуальные машины, расположенные во взаимосвязанных виртуальных сетях.

![ILB_SQLAO_NewPic](./media/load-balancer-configure-sqlao/sqlao1.jpg)


Внутреннюю подсистему балансировки нагрузки можно настроить только с помощью PowerShell.


## Добавление внутренней подсистемы балансировки нагрузки в службу 

### Шаг 1.

В следующем примере мы настроим виртуальную сеть, содержащую подсеть с именем Subnet-1:

	Add-AzureInternalLoadBalancer -InternalLoadBalancerName ILB_SQL_AO -SubnetName Subnet-1 -ServiceName SqlSvc

Шаг 2.

## Добавление конечных точек с балансировкой нагрузки для внутренней подсистемы балансировки нагрузки на каждой виртуальной машине

	Get-AzureVM -ServiceName SqlSvc -Name sqlsvc1 | Add-AzureEndpoint -Name "LisEUep" -LBSetName "ILBSet1" -Protocol tcp -LocalPort 1433 -PublicPort 1433 -ProbePort 59999 -ProbeProtocol tcp -ProbeIntervalInSeconds 10 –
	DirectServerReturn $true -InternalLoadBalancerName ILB_SQL_AO | Update-AzureVM 
	Get-AzureVM -ServiceName SqlSvc -Name sqlsvc2 | Add-AzureEndpoint -Name "LisEUep" -LBSetName "ILBSet1" -Protocol tcp -LocalPort 1433 -PublicPort 1433 -ProbePort 59999 -ProbeProtocol tcp -ProbeIntervalInSeconds 10 –DirectServerReturn $true -InternalLoadBalancerName ILB_SQL_AO | Update-AzureVM


## См. также

[Приступая к настройке подсистемы балансировки нагрузки, доступной в Интернете](load-balancer-internet-getstarted.md)

[Приступая к настройке внутренней подсистемы балансировки нагрузки](load-balancer-internal-getstarted.md)

[Настройка режима распределения подсистемы балансировки нагрузки](load-balancer-distribution-mode.md)

[Настройка параметров времени ожидания простоя TCP для подсистемы балансировки нагрузки](load-balancer-tcp-idle-timeout.md)
 

<!---HONumber=July15_HO4-->
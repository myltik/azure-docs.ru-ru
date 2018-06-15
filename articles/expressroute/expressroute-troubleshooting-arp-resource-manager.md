---
title: Получение таблиц ARP. Устранение неполадок ExpressRoute (Resource Manager) | Документация Майкрософт
description: На этой странице приводятся инструкции по получению таблиц ARP для канала ExpressRoute.
documentationcenter: na
services: expressroute
author: ganesr
manager: carolz
editor: tysonn
ms.assetid: 0a6bf1d5-6baf-44dd-87d3-1ebd2fd08bdc
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/30/2017
ms.author: ganesr
ms.openlocfilehash: a65b1ba2998eae33b3e73bd2492fbbf025eb5946
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/21/2017
ms.locfileid: "23013139"
---
# <a name="getting-arp-tables-in-the-resource-manager-deployment-model"></a>Получение таблиц ARP в модели развертывания с помощью Resource Manager
> [!div class="op_single_selector"]
> * [PowerShell — Resource Manager](expressroute-troubleshooting-arp-resource-manager.md)
> * [PowerShell — классическая модель](expressroute-troubleshooting-arp-classic.md)
> 
> 

В этой статье описана процедура изучения таблиц ARP для канала ExpressRoute. 

> [!IMPORTANT]
> Данный документ предназначен для диагностики и устранения простых проблем. Он не заменит услуг службы поддержки Майкрософт. Если вам не удается устранить проблему, используя рекомендации, описанные ниже, то необходимо отправить запрос в [службу поддержки Майкрософт](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) .
> 
> 

## <a name="address-resolution-protocol-arp-and-arp-tables"></a>Протокол ARP и таблицы ARP
Протокол ARP — это протокол уровня 2, определенный в стандарте [RFC 826](https://tools.ietf.org/html/rfc826). Протокол ARP используется для сопоставления адреса Ethernet (MAC-адреса) с IP-адресом.

Таблица ARP обеспечивает сопоставление IPv4-адреса и MAC-адреса для конкретного пиринга. Таблица ARP для пиринга канала ExpressRoute содержит следующие сведения о каждом интерфейсе (первичном и вторичном).

1. Сопоставление локального IP-адреса интерфейса маршрутизатора с MAC-адресом.
2. Сопоставление IP-адреса интерфейса маршрутизатора ExpressRoute с MAC-адресом.
3. Длительность сопоставления.

Таблицы ARP помогают проверить конфигурацию уровня 2 и устранить проблемы с подключением на базовом уровне 2. 

Пример таблицы ARP. 

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           10.0.0.1   ffff.eeee.dddd
          0 Microsoft         10.0.0.2   aaaa.bbbb.cccc


В следующем разделе приведены сведения о том, как можно просмотреть таблицы ARP, видимые на граничных маршрутизаторах ExpressRoute. 

## <a name="prerequisites-for-learning-arp-tables"></a>Необходимые условия для изучения таблиц ARP
Прежде чем продолжить, убедитесь, что выполнены следующие условия.

* Настроен действительный канал ExpressRoute по крайней мере с одним пирингом. Канал должен быть полностью настроен поставщиком услуг подключения. Вы (или ваш поставщик услуг подключения) должны настроить хотя бы один из пирингов (частный или общедоступный пиринг Azure либо пиринг Майкрософт) для этого канала.
* Настроены диапазоны IP-адресов, используемые для настройки пирингов (частных или общедоступных пирингов Azure либо пирингов Майкрософт). Просмотрите примеры назначения IP-адресов на [странице требований к маршрутизации ExpressRoute](expressroute-routing.md) , чтобы понять, как IP-адреса сопоставляются с интерфейсами на вашей стороне и на стороне ExpressRoute. Сведения о конфигурации пиринга можно получить, просмотрев [страницу настройки пиринга ExpressRoute](expressroute-howto-routing-arm.md).
* Получена информация от вашей группы сетевых администраторов или поставщика услуг подключения о MAC-адресах интерфейсов, используемых для этих IP-адресов.
* Требуется последняя версия модуля PowerShell для Azure (1.50 или более поздняя версия).

## <a name="getting-the-arp-tables-for-your-expressroute-circuit"></a>Получение таблиц ARP для канала ExpressRoute
В этом разделе описано, как просмотреть таблицы ARP для пиринга с помощью PowerShell. Прежде чем продолжить, вам или вашему поставщику услуг подключения нужно настроить пиринг. Каждый канал имеет два пути (первичный и вторичный). Вы можете просмотреть эти пути в таблице ARP независимо друг от друга.

### <a name="arp-tables-for-azure-private-peering"></a>Таблицы ARP для частного пиринга Azure
Следующий командлет предоставляет таблицы ARP для частного пиринга Azure.

        # Required Variables
        $RG = "<Your Resource Group Name Here>"
        $Name = "<Your ExpressRoute Circuit Name Here>"

        # ARP table for Azure private peering - Primary path
        Get-AzureRmExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType AzurePrivatePeering -DevicePath Primary

        # ARP table for Azure private peering - Secodary path
        Get-AzureRmExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType AzurePrivatePeering -DevicePath Secondary 

Ниже приведен пример выходных данных для одного из путей.

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           10.0.0.1   ffff.eeee.dddd
          0 Microsoft         10.0.0.2   aaaa.bbbb.cccc


### <a name="arp-tables-for-azure-public-peering"></a>Таблицы ARP для общедоступного пиринга Azure
Следующий командлет предоставляет таблицы ARP для общедоступного пиринга Azure.

        # Required Variables
        $RG = "<Your Resource Group Name Here>"
        $Name = "<Your ExpressRoute Circuit Name Here>"

        # ARP table for Azure public peering - Primary path
        Get-AzureRmExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType AzurePublicPeering -DevicePath Primary

        # ARP table for Azure public peering - Secodary path
        Get-AzureRmExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType AzurePublicPeering -DevicePath Secondary 


Ниже приведен пример выходных данных для одного из путей.

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           64.0.0.1   ffff.eeee.dddd
          0 Microsoft         64.0.0.2   aaaa.bbbb.cccc


### <a name="arp-tables-for-microsoft-peering"></a>Таблицы ARP для пиринга Майкрософт
Следующий командлет предоставляет таблицы ARP для пиринга Майкрософт.

        # Required Variables
        $RG = "<Your Resource Group Name Here>"
        $Name = "<Your ExpressRoute Circuit Name Here>"

        # ARP table for Microsoft peering - Primary path
        Get-AzureRmExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType MicrosoftPeering -DevicePath Primary

        # ARP table for Microsoft peering - Secodary path
        Get-AzureRmExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType MicrosoftPeering -DevicePath Secondary 


Ниже приведен пример выходных данных для одного из путей.

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           65.0.0.1   ffff.eeee.dddd
          0 Microsoft         65.0.0.2   aaaa.bbbb.cccc


## <a name="how-to-use-this-information"></a>Как использовать эти сведения
С помощью таблицы ARP для пиринга можно проверить конфигурацию и подключение уровня 2. В этом разделе описывается, как будут выглядеть таблицы ARP в различных сценариях.

### <a name="arp-table-when-a-circuit-is-in-operational-state-expected-state"></a>Таблица ARP, когда канал находится в рабочем состоянии (ожидаемое состояние)
* В таблице ARP будет отображаться запись для локальной стороны с действительным IP-адресом и MAC-адресом, а также аналогичная запись со стороны сети Майкрософт. 
* Последний октет локального IP-адреса всегда будет нечетным числом.
* Последний октет IP-адреса сети Майкрософт всегда будет четным числом.
* Одинаковый MAC-адрес будет отображаться со стороны сети Майкрософт для всех 3 пирингов (первичных или вторичных). 

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           65.0.0.1   ffff.eeee.dddd
          0 Microsoft         65.0.0.2   aaaa.bbbb.cccc

### <a name="arp-table-when-on-premises--connectivity-provider-side-has-problems"></a>Таблица ARP в случае проблем на стороне локальной сети или поставщика услуг подключения
В случае проблем с локальной средой или поставщиком услуг подключения в таблице ARP может быть отображена только одна запись или локальный MAC-адрес может быть отображен не полностью. Это будет сопоставление MAC-адреса и IP-адреса, используемого на стороне сети Майкрософт. 
  
       Age InterfaceProperty IpAddress  MacAddress    
       --- ----------------- ---------  ----------    
         0 Microsoft         65.0.0.2   aaaa.bbbb.cccc

или
       
       Age InterfaceProperty IpAddress  MacAddress    
       --- ----------------- ---------  ----------   
         0 On-Prem           65.0.0.1   Incomplete
         0 Microsoft         65.0.0.2   aaaa.bbbb.cccc


> [!NOTE]
> Отправьте запрос в службу поддержки своего поставщика услуг подключения, чтобы устранить подобные проблемы. Если в таблице ARP нет IP-адресов интерфейсов, сопоставленных с MAC-адресами, ознакомьтесь со следующими сведениями.
> 
> 1. Если первый IP-адрес подсети /30 назначен для связи между маршрутизатором MSEE-PR и MSEE используется интерфейсом MSEE-PR, Azure всегда использует второй IP-адрес для маршрутизаторов MSEE.
> 2. Убедитесь, что клиентские (C-тег) и служебные теги (S-тег) виртуальной сети соответствуют паре MSEE-PR и MSEE.
> 

### <a name="arp-table-when-microsoft-side-has-problems"></a>Таблица ARP в случае проблем на стороне сети Майкрософт
* Вы не увидите таблицу ARP для пиринга при наличии проблем на стороне сети Майкрософт. 
* Отправьте запрос в [службу поддержки Майкрософт](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). Укажите, что у вас проблема с подключением уровня 2. 

## <a name="next-steps"></a>Дальнейшие действия
* Проверка конфигураций уровня 3 для канала ExpressRoute.
  * Получение сводки маршрутов для определения состояния сеансов BGP. 
  * Получение таблицы маршрутов для определения того, какие префиксы объявляются в ExpressRoute.
* Проверка передачи данных путем просмотра входящих и выходящих байтов.
* Отправьте запрос в [службу поддержки Майкрософт](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) , если у вас по-прежнему возникают проблемы.


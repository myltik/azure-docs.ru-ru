---
title: Примеры конфигурации маршрутизатора клиента ExpressRoute | Документация Майкрософт
description: Эта страница содержит примеры конфигурации для маршрутизаторов серий Cisco ASA и Juniper MX.
documentationcenter: na
services: expressroute
author: cherylmc
manager: carmonm
editor: ''
ms.assetid: 564826bc-017a-4683-a385-37c9fa814948
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/10/2016
ms.author: cherylmc
ms.openlocfilehash: 032e584dc5abf59e9e3e8d80673b402f1fbf721b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
ms.locfileid: "23013109"
---
# <a name="router-configuration-samples-to-set-up-and-manage-routing"></a>Примеры конфигурации маршрутизатора для настройки и управления маршрутизацией
Эта страница содержит примеры конфигурации интерфейса и маршрутизации для маршрутизаторов серий Cisco IOS-XE и Juniper MX. Эти примеры имеют только справочный характер и не должны использоваться как есть. Подходящие конфигурации для своей сети можно выработать совместно с поставщиком. 

> [!IMPORTANT]
> Примеры на этой странице имеют исключительно справочный характер. Для получения подходящей конфигурации, которая удовлетворяет вашим потребностям, необходимо провести совместную работу со специалистами по продажам или техническими специалистами поставщика и вашими сетевыми специалистами. Корпорация Майкрософт не предоставляет поддержки по вопросам, связанным с конфигурациями, перечисленными на этой странице. Для получения поддержки вам необходимо обратиться к поставщику устройства.
> 
> 

## <a name="mtu-and-tcp-mss-settings-on-router-interfaces"></a>Параметры MTU и TCP MSS в интерфейсах маршрутизатора
* Значение MTU для интерфейса ExpressRoute составляет 1500, что является типичным значением MTU по умолчанию для интерфейса Ethernet на маршрутизаторе. Если маршрутизатор не имеет другого значения MTU по умолчанию, то и значение в интерфейсе маршрутизатора можно не указывать.
* В отличие от VPN-шлюза Azure, MSS TCP для канала ExpressRoute не нужно указывать.

Приведенные ниже примеры конфигурации маршрутизатора применяются ко всем пирингам. Для получения дополнительных сведений о маршрутизации обратитесь к статьям [Сеансы пиринга ExpressRoute](expressroute-circuit-peerings.md) и [Требования ExpressRoute к маршрутизации](expressroute-routing.md).


## <a name="cisco-ios-xe-based-routers"></a>Маршрутизаторы на основе Cisco IOS-XE
Примеры из этого раздела можно применить к любому маршрутизатору, работающем под управлением ОС семейства IOS-XE.

### <a name="1-configuring-interfaces-and-sub-interfaces"></a>1. Настройка интерфейсов и дочерние интерфейсы
Вам потребуются дочерние интерфейсы для каждого пиринга в каждом маршрутизаторе при подключении к Microsoft. Дочерний интерфейс можно идентифицировать с помощью идентификатора виртуальной локальной сети или с помощью накопленной пары идентификаторов виртуальных сетей и IP-адреса.

**Определение интерфейса Dot1Q**

В этом примере приведено определение вложенного интерфейса для вложенного интерфейса с одним идентификатором виртуальной локальной сети. Идентификатор виртуальной локальной сети уникален в пределах одного пиринга. Последний октет адреса IPv4 всегда будет нечетным числом.

    interface GigabitEthernet<Interface_Number>.<Number>
     encapsulation dot1Q <VLAN_ID>
     ip address <IPv4_Address><Subnet_Mask>

**Определение интерфейса QinQ**

В этом примере приведено определение вложенного интерфейса для вложенного интерфейса с двумя идентификаторами виртуальной локальной сети. Идентификатор внешней виртуальной локальной сети (s-тег) остается постоянным для всех пирингов, если используется. Идентификатор внешней виртуальной локальной сети (c-тег) уникален для каждого пиринга. Последний октет адреса IPv4 всегда будет нечетным числом.

    interface GigabitEthernet<Interface_Number>.<Number>
     encapsulation dot1Q <s-tag> seconddot1Q <c-tag>
     ip address <IPv4_Address><Subnet_Mask>

### <a name="2-setting-up-ebgp-sessions"></a>2. Настройка сеансов eBGP
Для каждого пиринга необходимо настроить сеанс BGP с Майкрософт. Следующий пример позволяет настроить сеанс BGP с Майкрософт. Если IPv4-адрес, используемый для вложенного интерфейса, a.b.c.d, то IP-адрес соседа BGP (Майкрософт) будет таким: a.b.c.d+1. Последний октет IPv4-адреса соседа BGP всегда будет четным числом.

    router bgp <Customer_ASN>
     bgp log-neighbor-changes
     neighbor <IP#2_used_by_Azure> remote-as 12076
     !        
     address-family ipv4
     neighbor <IP#2_used_by_Azure> activate
     exit-address-family
    !

### <a name="3-setting-up-prefixes-to-be-advertised-over-the-bgp-session"></a>3. Настройка префиксов, объявляемых в сеансе BGP
Маршрутизатор можно настроить для объявления выбранных префиксов в Майкрософт. Это можно сделать с помощью примера ниже.

    router bgp <Customer_ASN>
     bgp log-neighbor-changes
     neighbor <IP#2_used_by_Azure> remote-as 12076
     !        
     address-family ipv4
      network <Prefix_to_be_advertised> mask <Subnet_mask>
      neighbor <IP#2_used_by_Azure> activate
     exit-address-family
    !

### <a name="4-route-maps"></a>4. Карты маршрутизации
Для фильтрации префиксов, распространяемых в сети, можно использовать карты маршрутизации и списки префиксов. Для выполнения этой задачи можно использовать следующий пример. Убедитесь, что вы задали соответствующие настройки списков префиксов.

    router bgp <Customer_ASN>
     bgp log-neighbor-changes
     neighbor <IP#2_used_by_Azure> remote-as 12076
     !        
     address-family ipv4
      network <Prefix_to_be_advertised> mask <Subnet_mask>
      neighbor <IP#2_used_by_Azure> activate
      neighbor <IP#2_used_by_Azure> route-map <MS_Prefixes_Inbound> in
     exit-address-family
    !
    route-map <MS_Prefixes_Inbound> permit 10
     match ip address prefix-list <MS_Prefixes>
    !


## <a name="juniper-mx-series-routers"></a>Маршрутизаторы серии Juniper MX
Примеры из этого раздела можно применять ко всем маршрутизаторам серии Juniper MX.

### <a name="1-configuring-interfaces-and-sub-interfaces"></a>1. Настройка интерфейсов и дочерние интерфейсы

**Определение интерфейса Dot1Q**

В этом примере приведено определение вложенного интерфейса для вложенного интерфейса с одним идентификатором виртуальной локальной сети. Идентификатор виртуальной локальной сети уникален в пределах одного пиринга. Последний октет адреса IPv4 всегда будет нечетным числом.

    interfaces {
        vlan-tagging;
        <Interface_Number> {
            unit <Number> {
                vlan-id <VLAN_ID>;
                family inet {
                    address <IPv4_Address/Subnet_Mask>;
                }
            }
        }
    }


**Определение интерфейса QinQ**

В этом примере приведено определение вложенного интерфейса для вложенного интерфейса с двумя идентификаторами виртуальной локальной сети. Идентификатор внешней виртуальной локальной сети (s-тег) остается постоянным для всех пирингов, если используется. Идентификатор внешней виртуальной локальной сети (c-тег) уникален для каждого пиринга. Последний октет адреса IPv4 всегда будет нечетным числом.

    interfaces {
        <Interface_Number> {
            flexible-vlan-tagging;
            unit <Number> {
                vlan-tags outer <S-tag> inner <C-tag>;
                family inet {
                    address <IPv4_Address/Subnet_Mask>;
                }                           
            }                               
        }                                   
    }                           

### <a name="2-setting-up-ebgp-sessions"></a>2. Настройка сеансов eBGP
Для каждого пиринга необходимо настроить сеанс BGP с Майкрософт. Следующий пример позволяет настроить сеанс BGP с Майкрософт. Если IPv4-адрес, используемый для вложенного интерфейса, a.b.c.d, то IP-адрес соседа BGP (Майкрософт) будет таким: a.b.c.d+1. Последний октет IPv4-адреса соседа BGP всегда будет четным числом.

    routing-options {
        autonomous-system <Customer_ASN>;
    }
    }
    protocols {
        bgp { 
            group <Group_Name> { 
                peer-as 12076;              
                neighbor <IP#2_used_by_Azure>;
            }                               
        }                                   
    }

### <a name="3-setting-up-prefixes-to-be-advertised-over-the-bgp-session"></a>3. Настройка префиксов, объявляемых в сеансе BGP
Маршрутизатор можно настроить для объявления выбранных префиксов в Майкрософт. Это можно сделать с помощью примера ниже.

    policy-options {
        policy-statement <Policy_Name> {
            term 1 {
                from protocol OSPF;
        route-filter <Prefix_to_be_advertised/Subnet_Mask> exact;
                then {
                    accept;
                }
            }
        }
    }
    protocols {
        bgp { 
            group <Group_Name> { 
                export <Policy_Name>
                peer-as 12076;              
                neighbor <IP#2_used_by_Azure>;
            }                               
        }                                   
    }


### <a name="4-route-maps"></a>4. Карты маршрутизации
Для фильтрации префиксов, распространяемых в сети, можно использовать карты маршрутизации и списки префиксов. Для выполнения этой задачи можно использовать следующий пример. Убедитесь, что вы задали соответствующие настройки списков префиксов.

    policy-options {
        prefix-list MS_Prefixes {
            <IP_Prefix_1/Subnet_Mask>;
            <IP_Prefix_2/Subnet_Mask>;
        }
        policy-statement <MS_Prefixes_Inbound> {
            term 1 {
                from {
        prefix-list MS_Prefixes;
                }
                then {
                    accept;
                }
            }
        }
    }
    protocols {
        bgp { 
            group <Group_Name> { 
                export <Policy_Name>
                import <MS_Prefixes_Inbound>
                peer-as 12076;              
                neighbor <IP#2_used_by_Azure>;
            }                               
        }                                   
    }

## <a name="next-steps"></a>Дальнейшие действия
Дополнительные сведения см. в разделе [Вопросы и ответы по ExpressRoute](expressroute-faqs.md).


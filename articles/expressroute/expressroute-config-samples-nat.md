---
title: Примеры конфигурации маршрутизатора клиента ExpressRoute | Документация Майкрософт
description: Эта страница содержит примеры конфигурации для маршрутизаторов серий Cisco ASA и Juniper MX.
documentationcenter: na
services: expressroute
author: cherylmc
manager: carmonm
editor: ''
ms.assetid: d6ea716f-d5ee-4a61-92b0-640d6e7d6974
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/10/2016
ms.author: cherylmc
ms.openlocfilehash: 83a7da2db537a3c900e90432455d59e8ac56d917
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
ms.locfileid: "23012979"
---
# <a name="router-configuration-samples-to-set-up-and-manage-nat"></a>Примеры конфигурации маршрутизатора для настройки и управления NAT
Эта страница содержит примеры конфигурации NAT для маршрутизаторов серий Cisco ASA и Juniper SRX. Эти примеры имеют только справочный характер и не должны использоваться как есть. Подходящие конфигурации для своей сети можно выработать совместно с поставщиком. 

> [!IMPORTANT]
> Примеры на этой странице имеют исключительно справочный характер. Для получения подходящей конфигурации, которая удовлетворяет вашим потребностям, необходимо провести совместную работу со специалистами по продажам или техническими специалистами поставщика и вашими сетевыми специалистами. Корпорация Майкрософт не предоставляет поддержки по вопросам, связанным с конфигурациями, перечисленными на этой странице. Для получения поддержки вам необходимо обратиться к поставщику устройства.
> 
> 

* Примеры конфигурации маршрутизатора относятся к пирингам Azure Public и Microsoft. Вы не должны настраивать NAT для частного пиринга Azure. Для получения дополнительных сведений обратитесь к статьям [Сеансы пиринга ExpressRoute](expressroute-circuit-peerings.md) и [Требования ExpressRoute к NAT](expressroute-nat.md).

* Для подключений к Интернету и ExpressRoute ДОЛЖНЫ использоваться отдельные пулы IP-адресов NAT. Использование одного пула IP-адресов NAT для подключения к Интернету и ExpressRoute приведет к асимметричной маршрутизации и потере подключения.


## <a name="cisco-asa-firewalls"></a>Брандмауэры Cisco ASA
### <a name="pat-configuration-for-traffic-from-customer-network-to-microsoft"></a>Конфигурация NAT для трафика от сети клиента в Майкрософт
    object network MSFT-PAT
      range <SNAT-START-IP> <SNAT-END-IP>


    object-group network MSFT-Range
      network-object <IP> <Subnet_Mask>

    object-group network on-prem-range-1
      network-object <IP> <Subnet-Mask>

    object-group network on-prem-range-2
      network-object <IP> <Subnet-Mask>

    object-group network on-prem
      network-object object on-prem-range-1
      network-object object on-prem-range-2

    nat (outside,inside) source dynamic on-prem pat-pool MSFT-PAT destination static MSFT-Range MSFT-Range

### <a name="pat-configuration-for-traffic-from-microsoft-to-customer-network"></a>Конфигурация PAT для трафика из Майкрософт в клиентскую сеть

**Интерфейсы и направление:**

    Source Interface (where the traffic enters the ASA): inside
    Destination Interface (where the traffic exits the ASA): outside

**Конфигурация:**

Пул преобразования сетевых адресов:

    object network outbound-PAT
        host <NAT-IP>

Целевой сервер:

    object network Customer-Network
        network-object <IP> <Subnet-Mask>

Группа объектов для клиентских IP-адресов

    object-group network MSFT-Network-1
        network-object <MSFT-IP> <Subnet-Mask>

    object-group network MSFT-PAT-Networks
        network-object object MSFT-Network-1

Команды преобразования сетевых адресов:

    nat (inside,outside) source dynamic MSFT-PAT-Networks pat-pool outbound-PAT destination static Customer-Network Customer-Network


## <a name="juniper-srx-series-routers"></a>Маршрутизаторы серии Juniper SRX
### <a name="1-create-redundant-ethernet-interfaces-for-the-cluster"></a>1. Создайте избыточные интерфейсы Ethernet для кластера.
    interfaces {
        reth0 {
            description "To Internal Network";
            vlan-tagging;
            redundant-ether-options {
                redundancy-group 1;
            }
            unit 100 {
                vlan-id 100;
                family inet {
                    address <IP-Address/Subnet-mask>;
                }
            }
        }
        reth1 {
            description "To Microsoft via Edge Router";
            vlan-tagging;
            redundant-ether-options {
                redundancy-group 2;
            }
            unit 100 {
                description "To Microsoft via Edge Router";
                vlan-id 100;
                family inet {
                    address <IP-Address/Subnet-mask>;
                }
            }
        }
    }


### <a name="2-create-two-security-zones"></a>2. Создайте две зоны безопасности.
* Доверенная зона предназначена для внутренней сети, а недоверенная зона — для внешней сети, направленной на граничные маршрутизаторы.
* Назначьте соответствующие интерфейсы зонам.
* Включите службы для интерфейсов.

    security {       zones {           security-zone Trust {               host-inbound-traffic {                   system-services {                       ping;                   }                   protocols {                       bgp;                   }               }               interfaces {                   reth0.100;               }           }           security-zone Untrust {               host-inbound-traffic {                   system-services {                       ping;                   }                   protocols {                       bgp;                   }               }               interfaces {                   reth1.100;               }           }       }   }


### <a name="3-create-security-policies-between-zones"></a>3. Создайте политики безопасности между зонами.
    security {
        policies {
            from-zone Trust to-zone Untrust {
                policy allow-any {
                    match {
                        source-address any;
                        destination-address any;
                        application any;
                    }
                    then {
                        permit;
                    }
                }
            }
            from-zone Untrust to-zone Trust {
                policy allow-any {
                    match {
                        source-address any;
                        destination-address any;
                        application any;
                    }
                    then {
                        permit;
                    }
                }
            }
        }
    }


### <a name="4-configure-nat-policies"></a>4. Настройте политики NAT.
* Создайте два пула NAT. Один будет использоваться для исходящего трафика NAT в Майкрософт, а второй — для трафика от Майкрософт клиенту.
* Создайте правила для преобразования сетевых адресов соответствующего трафика.
  
       security {
           nat {
               source {
                   pool SNAT-To-ExpressRoute {
                       routing-instance {
                           External-ExpressRoute;
                       }
                       address {
                           <NAT-IP-address/Subnet-mask>;
                       }
                   }
                   pool SNAT-From-ExpressRoute {
                       routing-instance {
                           Internal;
                       }
                       address {
                           <NAT-IP-address/Subnet-mask>;
                       }
                   }
                   rule-set Outbound_NAT {
                       from routing-instance Internal;
                       to routing-instance External-ExpressRoute;
                       rule SNAT-Out {
                           match {
                               source-address 0.0.0.0/0;
                           }
                           then {
                               source-nat {
                                   pool {
                                       SNAT-To-ExpressRoute;
                                   }
                               }
                           }
                       }
                   }
                   rule-set Inbound-NAT {
                       from routing-instance External-ExpressRoute;
                       to routing-instance Internal;
                       rule SNAT-In {
                           match {
                               source-address 0.0.0.0/0;
                           }
                           then {
                               source-nat {
                                   pool {
                                       SNAT-From-ExpressRoute;
                                   }
                               }
                           }
                       }
                   }
               }
           }
       }

### <a name="5-configure-bgp-to-advertise-selective-prefixes-in-each-direction"></a>5. Настройки BGP для использования выборочных префиксов в каждом направлении
Обратитесь к примерам на странице [Примеры настройки маршрутизации ](expressroute-config-samples-routing.md) .

### <a name="6-create-policies"></a>6. Создайте политики.
    routing-options {
                  autonomous-system <Customer-ASN>;
    }
    policy-options {
        prefix-list Microsoft-Prefixes {
            <IP-Address/Subnet-Mask;
            <IP-Address/Subnet-Mask;
        }
        prefix-list private-ranges {
            10.0.0.0/8;
            172.16.0.0/12;
            192.168.0.0/16;
            100.64.0.0/10;
        }
        policy-statement Advertise-NAT-Pools {
            from {
                protocol static;
                route-filter <NAT-Pool-Address/Subnet-mask> prefix-length-range /32-/32;
            }
            then accept;
        }
        policy-statement Accept-from-Microsoft {
            term 1 {
                from {
                    instance External-ExpressRoute;
                    prefix-list-filter Microsoft-Prefixes orlonger;
                }
                then accept;
            }
            term deny {
                then reject;
            }
        }
        policy-statement Accept-from-Internal {
            term no-private {
                from {
                    instance Internal;
                    prefix-list-filter private-ranges orlonger;
                }
                then reject;
            }
            term bgp {
                from {
                    instance Internal;
                    protocol bgp;
                }
                then accept;
            }
            term deny {
                then reject;
            }
        }
    }
    routing-instances {
        Internal {
            instance-type virtual-router;
            interface reth0.100;
            routing-options {
                static {
                    route <NAT-Pool-IP-Address/Subnet-mask> discard;
                }
                instance-import Accept-from-Microsoft;
            }
            protocols {
                bgp {
                    group customer {
                        export <Advertise-NAT-Pools>;
                        peer-as <Customer-ASN-1>;
                        neighbor <BGP-Neighbor-IP-Address>;
                    }
                }
            }
        }
        External-ExpressRoute {
            instance-type virtual-router;
            interface reth1.100;
            routing-options {
                static {
                    route <NAT-Pool-IP-Address/Subnet-mask> discard;
                }
                instance-import Accept-from-Internal;
            }
            protocols {
                bgp {
                    group edge-router {
                        export <Advertise-NAT-Pools>;
                        peer-as <Customer-Public-ASN>;
                        neighbor <BGP-Neighbor-IP-Address>;
                    }
                }
            }
        }
    }

## <a name="next-steps"></a>Дополнительная информация
Дополнительные сведения см. в разделе [Вопросы и ответы по ExpressRoute](expressroute-faqs.md).


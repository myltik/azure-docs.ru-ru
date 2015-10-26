<properties
   pageTitle="Примеры конфигурации маршрутизатора клиента ExpressRoute | Microsoft Azure"
   description="Эта страница содержит примеры конфигурации для маршрутизаторов серий Cisco ASA и Juniper MX."
   documentationCenter="na"
   services="expressroute"
   authors="cherylmc"
   manager="carolz"
   editor="" />
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article" 
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/12/2015"
   ms.author="cherylmc"/>

# Примеры конфигурации маршрутизатора для настройки и управления NAT

Эта страница содержит примеры конфигурации NAT для маршрутизаторов серий Cisco ASA и Juniper MX. Эти примеры имеют только справочный характер и не должны использоваться как есть. Подходящие конфигурации для своей сети можно выработать совместно с поставщиком.

>[AZURE.IMPORTANT]Примеры на этой странице имеют исключительно справочный характер. Для получения подходящей конфигурации, которая удовлетворяет вашим потребностям, необходимо провести совместную работу со специалистами по продажам или техническими специалистами поставщика и вашими сетевыми специалистами. Корпорация Майкрософт не предоставляет поддержки по вопросам, связанным с конфигурациями, перечисленными на этой странице. Для получения поддержки вам необходимо обратиться к поставщику устройства.

Примеры конфигурации маршрутизатора относятся к пирингам Azure Public и Microsoft. Вы не должны настраивать NAT для частного пиринга Azure. Для получения дополнительных сведений обратитесь к статьям [Сеансы пиринга ExpressRoute](expressroute-circuit-peerings.md) и [Требования ExpressRoute к NAT](expressroute-nat.md).

**Примечание.** Для подключений к Интернету и ExpressRoute ДОЛЖНЫ использоваться отдельные пулы IP-адресов NAT. Использование одного пула IP-адресов NAT для подключения к Интернету и ExpressRoute приведет к асимметричной маршрутизации и потере подключения.

## Брандмауэры Cisco ASA

### Конфигурация NAT для трафика от сети клиента в Майкрософт

    object network MSFT-PAT
      range <SNAT-START-IP> <SNAT-END-IP>
    
    
    object-group network MSFT-Range
      network-object <IP> <Subnet_Mask>
    
    object-group network on-prem-range-1
      network-object <IP> <Subnet_Mask>
    
    object-group network on-prem-range-2
      network-object <IP> <Subnet_Mask>
    
    object-group network on-prem
      network-object object on-prem-range-1
      network-object object on-prem-range-2
    
    nat (outside,inside) source dynamic on-prem pat-pool MSFT-PAT destination static MSFT-Range MSFT-Range



## Маршрутизаторы серии Juniper MX 

### 1\. Создайте избыточные интерфейсы Ethernet для кластера.

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
	                address <IP_Address/Subnet_mask>;
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
	                address <IP_Address/Subnet_mask>;
	            }
	        }
	    }
	}


### 2\. Создайте две зоны безопасности.

 - Доверенная зона предназначена для внутренней сети, а недоверенная зона — для внешней сети, направленной на граничные маршрутизаторы.
 - Назначьте соответствующие интерфейсы зонам.
 - Включите службы для интерфейсов.


	security { zones { security-zone Trust { host-inbound-traffic { system-services { ping; } protocols { bgp; } } interfaces { reth0.100; } } security-zone Untrust { host-inbound-traffic { system-services { ping; } protocols { bgp; } } interfaces { reth1.100; } } } }


### 3\. Создайте политики безопасности между зонами.
 
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


### 4\. Настройте политики NAT.
 - Создайте два пула NAT. Один будет использоваться для исходящего трафика NAT в Майкрософт, а второй — для трафика от Майкрософт клиенту.
 - Создайте правила для преобразования сетевых адресов соответствующего трафика.

		security {
		    nat {
		        source {
		            pool SNAT_To_ExpressRoute {
		                routing-instance {
		                    External_ExpressRoute;
		                }
		                address {
		                    <NAT_IP_address/Subnet_mask>;
		                }
		            }
		            pool SNAT_From_ExpressRoute {
		                routing-instance {
		                    Internal;
		                }
		                address {
		                    <NAT_IP_address/Subnet_mask>;
		                }
		            }
		            rule-set Outbound_NAT {
		                from routing-instance Internal;
		                to routing-instance External_ExpressRoute;
		                rule SNAT_Out {
		                    match {
		                        source-address 0.0.0.0/0;
		                    }
		                    then {
		                        source-nat {
		                            pool {
		                                SNAT_To_ExpressRoute;
		                            }
		                        }
		                    }
		                }
		            }
		            rule-set Inbound_NAT {
		                from routing-instance External_ExpressRoute;
		                to routing-instance Internal;
		                rule SNAT_In {
		                    match {
		                        source-address 0.0.0.0/0;
		                    }
		                    then {
		                        source-nat {
		                            pool {
		                                SNAT_From_ExpressRoute;
		                            }
		                        }
		                    }
		                }
		            }
		        }
		    }
		}


### 5\. Настройки BGP для использования выборочных префиксов в каждом направлении

Обратитесь к примерам на странице [Примеры настройки маршрутизации ](expressroute-config-samples-routing.md).

### 6\. Создайте политики.

	routing-options {
	    	      autonomous-system <Customer_ASN>;
	}
	policy-options {
	    prefix-list Microsoft_Prefixes {
	        <IP_Address/Subnet_Mask;
	        <IP_Address/Subnet_Mask;
	    }
	    prefix-list private-ranges {
	        10.0.0.0/8;
	        172.16.0.0/12;
	        192.168.0.0/16;
	        100.64.0.0/10;
	    }
	    policy-statement Advertise_NAT_Pools {
	        from {
	            protocol static;
	            route-filter <NAT_Pool_Address/Subnet_mask> prefix-length-range /32-/32;
	        }
	        then accept;
	    }
	    policy-statement Accept_from_Microsoft {
	        term 1 {
	            from {
	                instance External_ExpressRoute;
	                prefix-list-filter Microsoft_Prefixes orlonger;
	            }
	            then accept;
	        }
	        term deny {
	            then reject;
	        }
	    }
	    policy-statement Accept_from_Internal {
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
	                route <NAT_Pool_IP_Address/Subnet_mask> discard;
	            }
	            instance-import Accept_from_Microsoft;
	        }
	        protocols {
	            bgp {
	                group customer {
	                    export <Advertise_NAT_Pools>;
	                    peer-as <Customer_ASN_1>;
	                    neighbor <BGP_Neighbor_IP_Address>;
	                }
	            }
	        }
	    }
	    External_ExpressRoute {
	        instance-type virtual-router;
	        interface reth1.100;
	        routing-options {
	            static {
	                route <NAT_Pool_IP_Address/Subnet_mask> discard;
	            }
	            instance-import Accept_from_Internal;
	        }
	        protocols {
	            bgp {
	                group edge_router {
	                    export <Advertise_NAT_Pools>;
	                    peer-as <Customer_Public_ASN>;
	                    neighbor <BGP_Neighbor_IP_Address>;
	                }
	            }
	        }
	    }
	}

## Дальнейшие действия

Дополнительные сведения см. в разделе [Вопросы и ответы по ExpressRoute](expressroute-faqs.md).

<!---HONumber=Oct15_HO3-->
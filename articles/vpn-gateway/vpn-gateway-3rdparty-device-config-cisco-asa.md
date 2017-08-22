---
title: "Пример конфигурации. Подключение устройства Cisco ASA к VPN-шлюзам Azure | Документация Майкрософт"
description: "В этой статье приведен пример конфигурации для подключения устройства Cisco ASA к VPN-шлюзам Azure."
services: vpn-gateway
documentationcenter: na
author: yushwang
manager: rossort
editor: 
tags: 
ms.assetid: a8bfc955-de49-4172-95ac-5257e262d7ea
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/20/2017
ms.author: yushwang
ms.translationtype: HT
ms.sourcegitcommit: d941879aee6042b38b7f5569cd4e31cb78b4ad33
ms.openlocfilehash: 10466b8928e2cd687f7961a2956b6d60823b82be
ms.contentlocale: ru-ru
ms.lasthandoff: 07/10/2017


---
# <a name="sample-configuration-cisco-asa-device-ikev2no-bgp"></a>Пример конфигурации. Устройство Cisco ASA (IKEv2/без BGP)
В этой статье приведен пример конфигурации для подключения устройств Cisco ASA к VPN-шлюзам Azure.

## <a name="device-at-a-glance"></a>Краткий обзор устройства

|                        |                                   |
| ---                    | ---                               |
| Поставщик устройства          | Cisco                             |
| Модель устройства           | ASA (устройство адаптивной безопасности) |
| Целевая версия         | 8.4+                              |
| Проверенная модель           | ASA 5505                          |
| Проверенные версии         | 9.2                               |
| Версия IKE            | **IKEv2**                         |
| BGP                    | **Нет**                            |
| Тип VPN-шлюза Azure | Пример VPN-шлюза **на основе маршрута**       |
|                        |                                   |

> [!NOTE]
> 1. В примере конфигурации ниже устройство Cisco ASA подключается к VPN-шлюзу Azure **на основе маршрута**, используя пользовательскую политику IPsec/IKE с параметром UserPolicyBasedTrafficSelectors, как описано в [этой статье](vpn-gateway-connect-multiple-policybased-rm-ps.md).
> 2. Для этого требуется, чтобы устройства ASA использовали **IKEv2** с конфигурацией на основе списка доступа, а не на основе VTI.
> 3. Мы рекомендуем ознакомиться со спецификациями поставщиков устройств VPN, чтобы убедиться, что политика поддерживается локальными устройствами VPN.

## <a name="vpn-device-requirements"></a>Требования к VPN-устройствам
В VPN-шлюзах Azure используются стандартные наборы протоколов IPsec/IKE для установки VPN-туннелей S2S. Подробные параметры протокола IPsec/IKE и алгоритмы шифрования по умолчанию для VPN-шлюзов Azure см. в статье [VPN-устройства и параметры IPsec/IKE для подключений типа "сеть — сеть" через VPN-шлюз](vpn-gateway-about-vpn-devices.md). Дополнительно можно указать точную комбинацию алгоритмов шифрования и уровней стойкости ключа для определенного подключения, как описано в статье [Требования к шифрованию и VPN-шлюзы Azure](vpn-gateway-about-compliance-crypto.md). В случае выбора конкретного сочетания алгоритмов шифрования и уровней стойкости ключей, убедитесь, что используется соответствующая спецификация на VPN-устройствах.

## <a name="single-vpn-tunnel"></a>Одиночный VPN-туннель
Эта топология состоит из одного VPN-туннеля S2S между VPN-шлюзом Azure и локальным устройством VPN. При необходимости можно также настроить BGP поверх VPN-туннеля.

![одиночный туннель](./media/vpn-gateway-3rdparty-device-config-cisco-asa/singletunnel.png)

Детальные пошаговые инструкции для создания конфигурации Azure см. в разделе [Одиночный VPN-туннель](vpn-gateway-3rdparty-device-config-overview.md#singletunnel).

### <a name="network-and-vpn-gateway-information"></a>Сведения о сети и VPN-шлюзе
В этом разделе перечислены параметры для этого примера.

| **Параметр**                | **Значение**                    |
| ---                          | ---                          |
| Префиксы адресов виртуальной сети        | 10.11.0.0/16;<br>10.12.0.0/16 |
| IP-адрес VPN-шлюза Azure         | Azure_Gateway_Public_IP      |
| Префиксы адресов в локальной среде | 10.51.0.0/16<br>10.52.0.0/16 |
| IP-адрес VPN-устройства в локальной среде    | OnPrem_Device_Public_IP     |
| *ASN для BGP виртуальной сети                | 65010                        |
| *IP-адрес узла BGP Azure           | 10.12.255.30                 |
| *ASN BGP в локальной среде         | 65050                        |
| *IP-адрес узла BGP в локальной среде     | 10.52.255.254                |
|                              |                              |

* (*) Необязательные параметры только для BGP.

### <a name="ipsecike-policy--parameters"></a>Политика и параметры IPsec/IKE

Указанная ниже таблица содержит список алгоритмов и параметров IPsec/IKE, используемых в этом примере. Проверьте спецификацию VPN-устройства, чтобы убедиться, что все перечисленные выше алгоритмы поддерживаются моделями VPN-устройства и версиями встроенного ПО.

| **IPsec/IKEv2**  | **Значение**                            |
| ---              | ---                                  |
| Шифрование IKEv2 | AES256                               |
| Проверка целостности IKEv2  | SHA384                               |
| Группа DH         | DHGroup24                            |
| Шифрование IPsec | AES256                               |
| Целостность IPsec  | SHA1                                 |
| Группа PFS        | PFS24                                |
| Время существования QM SA   | 7200 секунд                         |
| Селектор трафика | UsePolicyBasedTrafficSelectors $True |
| Общий ключ   | PreSharedKey                         |
|                  |                                      |

- (*) Если GCM-AES используется как алгоритм шифрования IPsec, целостность IPsec на некоторых устройствах должна быть равна NULL.

### <a name="device-notes"></a>Заметки об устройстве

>[!NOTE]
>
> 1. Для поддержки IKEv2 необходима ASA версии 8.4 и выше.
> 2. Установите ASA версии 9.x для более высокого уровня поддержки групп DH и PFS (за пределами группы 5).
> 3. Для шифрования IPsec с целостностью AES-GCM и IPsec с поддержкой SHA-256, SHA-384, SHA-512 необходимо устройство ASA версии 9.x на новом оборудовании ASA. ASA 5505, 5510, 5520, 5540, 5550 и 5580 **не** поддерживаются. (Для подтверждения проверьте спецификацию поставщика.)
>


### <a name="sample-device-configurations"></a>Примеры конфигурации устройств
Приведенный ниже скрипт предоставляет пример конфигурации на основе топологии и параметров выше. Конфигурация туннеля VPN типа "сеть — сеть" состоит из следующих элементов:

1. Интерфейсы и маршруты.
2. Списки доступа.
3. Политика и параметры IKE (фаза 1 или основной режим).
4. Политика и параметры IKE (фаза 2 или быстрый режим).
5. Другие параметры (фиксация TCP MSS и т. д.).

>[!IMPORTANT] 
>Выполните дополнительную указанную ниже конфигурацию и замените заполнители фактическими значениями.
> 
> - Конфигурация для внутренних и внешних интерфейсов.
> - Маршруты для внутренних (частных), внешних (общедоступных) сетей.
> - Обеспечьте уникальность имен и номеров политики устройства.
> - Убедитесь, что на устройстве поддерживаются алгоритмы шифрования.
> - Замените следующие заполнители фактическими значениями:
>   - Имя внешнего интерфейса: outside;
>   - Azure_Gateway_Public_IP;
>   - OnPrem_Device_Public_IP;
>   - IKE Pre_Shared_Key;
>   - Имена виртуальной сети и шлюза локальной сети (VNetName, LNGName).
>   - Префиксы адресов виртуальной сети и локальной сети.
>   - Правильные маски сети.

#### <a name="sample-configuration"></a>Пример конфигурации

```
! Sample ASA configuration for connecting to Azure VPN gateway
!
! Tested hardware: ASA 5505
! Tested version:  ASA version 9.2(4)
!
! Replace the following place holders with your actual values:
!   - Interface names - default are "outside" and "inside"
!   - <Azure_Gateway_Public_IP>
!   - <OnPrem_Device_Public_IP>
!   - <Pre_Shared_Key>
!   - <VNetName>*
!   - <LNGName>* ==> LocalNetworkGateway - the Azure resource that represents the
!     on-premises network, specifies network prefixes, device public IP, BGP info, etc.
!   - <PrivateIPAddress> ==> Replace it with a private IP address if applicable
!   - <Netmask> ==> Replace it with appropriate netmasks
!   - <Nexthop> ==> Replace it with the actual nexthop IP address
!
! (*) Must be unique names in the device configuration
!
! ==> Interface & route configurations
!
!     > <OnPrem_Device_Public_IP> address on the outside interface or vlan
!     > <PrivateIPAddress> on the inside interface or vlan; e.g., 10.51.0.1/24
!     > Route to connect to <Azure_Gateway_Public_IP> address
!
!     > Example:
!
!       interface Ethernet0/0
!        switchport access vlan 2
!       exit
!
!       interface vlan 1
!        nameif inside
!        security-level 100
!        ip address <PrivateIPAddress> <Netmask>
!       exit
!
!       interface vlan 2
!        nameif outside
!        security-level 0
!        ip address <OnPrem_Device_Public_IP> <Netmask>
!       exit
!
!       route outside 0.0.0.0 0.0.0.0 <NextHop IP> 1
!
! ==> Access lists
!
!     > Most firewall devices deny all traffic by default. Create access lists to
!       (1) Allow S2S VPN tunnels between the ASA and the Azure gateway public IP address
!       (2) Construct traffic selectors as part of IPsec policy or proposal
!
access-list outside_access_in extended permit ip host <Azure_Gateway_Public_IP> host <OnPrem_Device_Public_IP>
!
!     > Object group that consists of all VNet prefixes (e.g., 10.11.0.0/16 &
!       10.12.0.0/16)
!
object-group network Azure-<VNetName>
 description Azure virtual network <VNetName> prefixes
 network-object 10.11.0.0 255.255.0.0
 network-object 10.12.0.0 255.255.0.0
exit
!
!     > Object group that corresponding to the <LNGName> prefixes.
!       E.g., 10.51.0.0/16 and 10.52.0.0/16. Note that LNG = "local network gateway".
!       In Azure network resource, a local network gateway defines the on-premises
!       network properties (address prefixes, VPN device IP, BGP ASN, etc.)
!
object-group network <LNGName>
 description On-Premises network <LNGName> prefixes
 network-object 10.51.0.0 255.255.0.0
 network-object 10.52.0.0 255.255.0.0
exit
!
!     > Specify the access-list between the Azure VNet and your on-premises network.
!       This access list defines the IPsec SA traffic selectors.
!
access-list Azure-<VNetName>-acl extended permit ip object-group <LNGName> object-group Azure-<VNetName>
!
!     > No NAT required between the on-premises network and Azure VNet
!
nat (inside,outside) source static <LNGName> <LNGName> destination static Azure-<VNetName> Azure-<VNetName>
!
! ==> IKEv2 configuration
!
!     > General IKEv2 configuration - enable IKEv2 for VPN
!
group-policy DfltGrpPolicy attributes
 vpn-tunnel-protocol ikev1 ikev2
exit
!
crypto isakmp identity address
crypto ikev2 enable outside
!
!     > Define IKEv2 Phase 1/Main Mode policy
!       - Make sure the policy number is not used
!       - integrity and prf must be the same
!       - DH group 14 and above require ASA version 9.x.
!
crypto ikev2 policy 1
 encryption       aes-256
 integrity        sha384
 prf              sha384
 group            24
 lifetime seconds 86400
exit
!
!     > Set connection type and pre-shared key
!
tunnel-group <Azure_Gateway_Public_IP> type ipsec-l2l
tunnel-group <Azure_Gateway_Public_IP> ipsec-attributes
 ikev2 remote-authentication pre-shared-key <Pre_Shared_Key> 
 ikev2 local-authentication  pre-shared-key <Pre_Shared_Key> 
exit
!
! ==> IPsec configuration
!
!     > IKEv2 Phase 2/Quick Mode proposal
!       - AES-GCM and SHA-2 requires ASA version 9.x on newer ASA models. ASA
!         5505, 5510, 5520, 5540, 5550, 5580 are not supported.
!       - ESP integrity must be null if AES-GCM is configured as ESP encryption
!
crypto ipsec ikev2 ipsec-proposal AES-256
 protocol esp encryption aes-256
 protocol esp integrity  sha-1
exit
!
!     > Set access list & traffic selectors, PFS, IPsec protposal, SA lifetime
!       - This sample uses "Azure-<VNetName>-map" as the crypto map name
!       - ASA supports only one crypto map per interface, if you already have
!         an existing crypto map assigned to your outside interface, you must use
!         the same crypto map name, but with a different sequence number for
!         this policy
!       - "match address" policy uses the access-list "Azure-<VNetName>-acl" defined 
!         previously
!       - "ipsec-proposal" uses the proposal "AES-256" defined previously 
!       - PFS groups 14 and beyond requires ASA version 9.x.
!
crypto map Azure-<VNetName>-map 1 match address Azure-<VNetName>-acl
crypto map Azure-<VNetName>-map 1 set pfs group24
crypto map Azure-<VNetName>-map 1 set peer <Azure_Gateway_Public_IP>
crypto map Azure-<VNetName>-map 1 set ikev2 ipsec-proposal AES-256
crypto map Azure-<VNetName>-map 1 set security-association lifetime seconds 7200
crypto map Azure-<VNetName>-map interface outside
!
! ==> Set TCP MSS to 1350
!
sysopt connection tcpmss 1350
!
```

## <a name="simple-debugging-commands"></a>Простые команды отладки

Ниже приведены некоторые команды ASA для отладки.

1. Отображение сопоставления безопасности IPsec и IKE.
    - show crypto ipsec sa;
    - show crypto ikev2 sa.
2. Ввод режима отладки. Это может привести к искаженной работе консоли.
    - debug crypto ikev2 platform <level>;
    - debug crypto ikev2 protocol <level>.
3. Вывод текущих конфигураций.
    - show run — отображает текущие конфигурации на устройстве. Вы можете использовать различные вложенные команды, чтобы отобразить конкретные части конфигурации. Например, show run crypto, show run access-list, show run tunnel-group и т. д.


## <a name="next-steps"></a>Дальнейшие действия
Инструкции по настройке распределенных подключений (локальных и между виртуальными сетями) с конфигурацией "активный — активный" см. в статье [Настройка VPN-подключений типа "сеть — сеть" в режиме "активный — активный" для VPN-шлюзов Azure с помощью Azure Resource Manager и PowerShell](vpn-gateway-activeactive-rm-powershell.md).



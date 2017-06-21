---
title: "VPN-устройства для распределенных подключений Azure | Документация Майкрософт"
description: "В этой статье описываются VPN-устройства и параметры IPsec для подключений типа &quot;сеть — сеть&quot; через VPN-шлюз, а также приводятся ссылки на примеры и инструкции по настройке."
services: vpn-gateway
documentationcenter: na
author: yushwang
manager: rossort
editor: 
tags: azure-resource-manager, azure-service-management
ms.assetid: ba449333-2716-4b7f-9889-ecc521e4d616
ms.service: vpn-gateway
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/14/2017
ms.author: yushwang;cherylmc
ms.translationtype: Human Translation
ms.sourcegitcommit: db18dd24a1d10a836d07c3ab1925a8e59371051f
ms.openlocfilehash: 8a7419c7a759060dc91f11ec94085ff0afd4a457
ms.contentlocale: ru-ru
ms.lasthandoff: 06/15/2017


---
# <a name="about-vpn-devices-and-ipsecike-parameters-for-site-to-site-vpn-gateway-connections"></a>VPN-устройства и параметры IPsec/IKE для подключений типа "сеть — сеть" через VPN-шлюз

Для настройки распределенного VPN-подключения типа "сеть — сеть" через VPN-шлюз требуется VPN-устройство. Подключения типа "сеть — сеть" можно использовать для создания гибридного решения, а также когда требуется безопасное подключение между локальной и виртуальной сетями. В этой статье перечислены параметры IPsec/IKE для VPN-шлюзов Azure и проверенные устройства VPN, подключаемые к VPN-шлюзам Azure.


> [!IMPORTANT]
> Ознакомьтесь с [известными проблемами совместимости устройств](#known), если вы столкнулись с проблемами подключения между локальными VPN-устройствами и VPN-шлюзами Azure. 


###<a name="items-to-note-when-viewing-the-tables"></a>На что следует обратите внимание при просмотре таблицы:

* Изменилась терминология, связанная с VPN-шлюзами Azure. Функциональность осталась прежней, изменились только названия.
  * Статическая маршрутизация — на основе политик (PolicyBased).
  * Динамическая маршрутизация — на основе маршрутов (RouteBased).
* Спецификации высокопроизводительных VPN-шлюзов и VPN-шлюзов типа RouteBased одинаковы, если не указано иное. Например, проверенные VPN-устройства, совместимые с VPN-шлюзами RouteBased, также совместимы с высокопроизводительными VPN-шлюзами Azure.

> [!NOTE]
> При настройке подключения типа "сеть — сеть" для VPN-устройства необходимо указывать общедоступный IP-адрес IPv4.
>                

## <a name="devicetable"></a>Проверенные VPN-устройства и руководства по конфигурации устройства
Мы утвердили набор стандартных VPN-устройств в сотрудничестве с поставщиками устройств. Все устройства их приведенного ниже списка семейств должны работать с VPN-шлюзами Azure. Инструкции по проверке типа шлюза, который необходимо создать для настраиваемого решения, см. в статье [Основные сведения о VPN-шлюзах Azure](vpn-gateway-about-vpngateways.md).

Чтобы настроить VPN-устройство, обратитесь к ссылкам, соответствующим семейству устройств. Ссылки на инструкции по настройке будут предоставляться по мере возможности. За поддержкой VPN-устройства обратитесь к его изготовителю.

|**поставщик**          |**Семейство устройств**     |**Минимальная версия ОС** |**Инструкции по настройке PolicyBased** |**Инструкции по настройке RouteBased** |
| ---                | ---                  | ---                   | ---            | ---           |
| A10 Networks, Inc. |Thunder CFW           |ACOS 4.1.1             |Не совместимо  |[Руководство по настройке](https://www.a10networks.com/resources/deployment-guides/a10-thunder-cfw-ipsec-vpn-interoperability-azure-vpn-gateways)|
| Allied Telesis     |VPN-маршрутизаторы серии AR |2.9.2                  |Скоро     |Не совместимо  |
| Barracuda Networks, Inc. |Брандмауэр Barracuda NextGen серии F |PolicyBased: 5.4.3<br>RouteBased: 6.2.0 |[Руководство по настройке](https://techlib.barracuda.com/NGF/AzurePolicyBasedVPNGW) |[Руководство по настройке](https://techlib.barracuda.com/NGF/AzureRouteBasedVPNGW) |
| Barracuda Networks, Inc. |Брандмауэр Barracuda NextGen серии Х |Barracuda Firewall 6.5 |[Руководство по настройке](https://techlib.barracuda.com/BFW/ConfigAzureVPNGateway) |Не совместимо |
| Brocade            |Vyatta 5400 vRouter   |Virtual Router 6.6R3 GA|[Руководство по настройке](http://www1.brocade.com/downloads/documents/html_product_manuals/vyatta/vyatta_5400_manual/wwhelp/wwhimpl/js/html/wwhelp.htm#href=VPN_Site-to-Site%20IPsec%20VPN/Preface.1.1.html) |Не совместимо |
| Check Point |Security Gateway |R77.30 |[Руководство по настройке](https://supportcenter.checkpoint.com/supportcenter/portal?eventSubmit_doGoviewsolutiondetails=&solutionid=sk101275) |[Руководство по настройке](https://supportcenter.checkpoint.com/supportcenter/portal?eventSubmit_doGoviewsolutiondetails=&solutionid=sk101275) |
| Cisco              |ASA       |8.3 |[Примеры конфигурации](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Cisco/Current/ASA) |Не совместимо |
| Cisco |ASR |PolicyBased: IOS 15.1<br>RouteBased: IOS 15.2 |[Примеры конфигурации](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Cisco/Current/ASR) |[Примеры конфигурации](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Cisco/Current/ASR) |
| Cisco |ISR |PolicyBased: IOS 15.0<br>RouteBased*: IOS 15.1 |[Примеры конфигурации](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Cisco/Current/ISR) |[Примеры настройки*](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Cisco/Current/ISR) |
| Citrix |NetScaler MPX, SDX, VPX |10.1 и выше |[Руководство по настройке](https://docs.citrix.com/en-us/netscaler/11-1/system/cloudbridge-connector-introduction/cloudbridge-connector-azure.html) |Не совместимо |
| F5 |Серия BIG-IP |12.0 |[Руководство по настройке](https://devcentral.f5.com/articles/connecting-to-windows-azure-with-the-big-ip) |[Руководство по настройке](https://devcentral.f5.com/articles/big-ip-to-azure-dynamic-ipsec-tunneling) |
| Fortinet |FortiGate |FortiOS 5.4.2 |  |[Руководство по настройке](http://cookbook.fortinet.com/ipsec-vpn-microsoft-azure-54) |
| Internet Initiative Japan (IIJ) |Серия SEIL |SEIL/X 4.60<br>SEIL/B1 4.60<br>SEIL/x86 3.20 |[Руководство по настройке](http://www.iij.ad.jp/biz/seil/ConfigAzureSEILVPN.pdf) |Не совместимо |
| Juniper |SRX |PolicyBased: JunOS 10.2<br>Routebased: JunOS 11.4 |[Примеры конфигурации](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/SRX) |[Примеры конфигурации](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/SRX) |
| Juniper |Серия J |PolicyBased: JunOS 10.4r9<br>RouteBased: JunOS 11.4 |[Примеры конфигурации](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/JSeries) |[Примеры конфигурации](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/JSeries) |
| Juniper |ISG |ScreenOS 6.3 |[Примеры конфигурации](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/ISG) |[Примеры конфигурации](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/ISG) |
| Juniper |SSG |ScreenOS 6.2 |[Примеры конфигурации](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/SSG) |[Примеры конфигурации](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/SSG) |
| Microsoft |Служба маршрутизации и удаленного доступа |Windows Server 2012 |Не совместимо |[Примеры конфигурации](http://go.microsoft.com/fwlink/p/?LinkId=717761) |
| Open Systems AG |Шлюз безопасности Mission Control |Недоступно |[Руководство по настройке](https://www.open.ch/_pdf/Azure/AzureVPNSetup_Installation_Guide.pdf) |Не совместимо |
| Openswan |Openswan |2.6.32 |(Ожидается в ближайшее время) |Не совместимо |
| Palo Alto Networks |Все устройства под управлением PAN-OS |PAN-OS<br>PolicyBased: 6.1.5 или более поздней версии<br>RouteBased: 7.1.4 |[Руководство по настройке](https://live.paloaltonetworks.com/t5/Configuration-Articles/How-to-Configure-VPN-Tunnel-Between-a-Palo-Alto-Networks/ta-p/59065) |[Руководство по настройке](https://live.paloaltonetworks.com/t5/Integration-Articles/Configuring-IKEv2-VPN-for-Microsoft-Azure-Environment/ta-p/60340) |
| SonicWall |Серия TZ и NSA<br>Серия SuperMassive<br>Серия NSA класса E |SonicOS 5.8.x<br>SonicOS 5.9.x<br>SonicOS 6.x |[Configuration guide for SonicOS 6.2](http://documents.software.dell.com/sonicos/6.2/microsoft-azure-configuration-guide?ParentProduct=646) (Руководство по настройке SonicOS 6.2)<br>[Configuration guide for SonicOS 5.9](http://documents.software.dell.com/sonicos/5.9/microsoft-azure-configuration-guide?ParentProduct=850) (Руководство по настройке SonicOS 5.9) |[Configuration guide for SonicOS 6.2](http://documents.software.dell.com/sonicos/6.2/microsoft-azure-configuration-guide?ParentProduct=646) (Руководство по настройке SonicOS 6.2)<br>[Configuration guide for SonicOS 5.9](http://documents.software.dell.com/sonicos/5.9/microsoft-azure-configuration-guide?ParentProduct=850) (Руководство по настройке SonicOS 5.9) |
| WatchGuard |Все |Fireware XTM<br> PolicyBased: 11.11.x<br>RouteBased: 11.12.x |[Руководство по настройке](http://watchguardsupport.force.com/publicKB?type=KBArticle&SFDCID=kA2F00000000LI7KAM&lang=en_US) |[Руководство по настройке](http://watchguardsupport.force.com/publicKB?type=KBArticle&SFDCID=kA22A000000XZogSAG&lang=en_US)|

* Маршрутизаторы ISR серии 7200 поддерживают только VPN типа PolicyBased.

## <a name="additionaldevices"></a>Непроверенные VPN-устройства
Даже если ваше устройство не указано в представленной таблице проверенных VPN-устройств, оно может поддерживать подключение типа "сеть — сеть". Чтобы получить дополнительную информацию и инструкции по настройке, обратитесь к изготовителю устройства.

## <a name="editing"></a>Изменение примеров конфигурации устройств
После скачивания предоставленного примера конфигурации VPN-устройства некоторые значения необходимо заменить в соответствии с параметрами вашей среды.

### <a name="to-edit-a-sample"></a>Чтобы изменить образец, выполните описанные ниже действия

1. Откройте пример с помощью блокнота.
2. Найдите и замените все строки <*text*> значениями, отражающими свойства вашей среды. Не забудьте добавить "<" и ">". Если указывается имя, оно должно быть уникальным. Если команда не работает, обратитесь к документации изготовителя устройства.

| **Пример текста** | **Изменить на** |
| --- | --- |
| &lt;RP_OnPremisesNetwork&gt; |Выбранное имя для данного объекта. Пример: myOnPremisesNetwork. |
| &lt;RP_AzureNetwork&gt; |Выбранное имя для данного объекта. Пример: myAzureNetwork. |
| &lt;RP_AccessList&gt; |Выбранное имя для данного объекта. Пример: myAzureAccessList. |
| &lt;RP_IPSecTransformSet&gt; |Выбранное имя для данного объекта. Пример: myIPSecTransformSet. |
| &lt;RP_IPSecCryptoMap&gt; |Выбранное имя для данного объекта. Пример: myIPSecCryptoMap. |
| &lt;SP_AzureNetworkIpRange&gt; |Укажите диапазон. Пример: 192.168.0.0. |
| &lt;SP_AzureNetworkSubnetMask&gt; |Укажите маску подсети. Пример: 255.255.0.0. |
| &lt;SP_AzureNetworkIpRange&gt; |Укажите локальный диапазон. Пример: 10.2.1.0. |
| &lt;SP_AzureNetworkSubnetMask&gt; |Укажите локальную маску подсети. Пример: 255.255.255.0. |
| &lt;SP_AzureGatewayIpAddress&gt; |Эта информация относится к виртуальной сети и находится на портале управления в поле **IP-адрес шлюза**. |
| &lt;SP_PresharedKey&gt; |Эта информация относится к виртуальной сети и находится на портале управления в разделе «Управление ключами». |

## <a name="ipsec"></a>Параметры IPsec/IKE
> [!NOTE]
> Хотя VPN-шлюз Azure и поддерживает перечисленные ниже значения, сейчас на нем нельзя указать или выбрать конкретную комбинацию алгоритмов или параметров. Все ограничения необходимо указывать на локальном VPN-устройстве. Кроме того, необходимо установить для **MSS** значение **1350**.
> 
>

В таблицах ниже приведены следующие сведения:

* SA — сопоставление безопасности.
* Этап 1 IKE также называется "Главный режим".
* Этап 2 IKE также называется "Быстрый режим".

### <a name="ike-phase-1-main-mode-parameters"></a>Параметры этапа 1 IKE (главный режим)
| **Свойство**          |**PolicyBased**    | **RouteBased**    |
| ---                   | ---               | ---               |
| Версия IKE           |IKEv1              |IKEv2              |
| Группа Диффи — Хелмана  |Группа 2 (1024 бита) |Группа 2 (1024 бита) |
| Метод проверки подлинности |Общий ключ     |Общий ключ     |
| Алгоритмы шифрования и хэширования |1. AES256, SHA256<br>2) AES256, SHA1<br>3. AES128, SHA1<br>4. 3DES, SHA1 |1. AES256, SHA1<br>2) AES256, SHA256<br>3. AES128, SHA1<br>4. AES128, SHA256<br>5. 3DES, SHA1<br>6. 3DES, SHA256 |
| Срок действия SA           |28 800 сек     |28 800 сек     |

### <a name="ike-phase-2-quick-mode-parameters"></a>Параметры этапа 2 IKE (быстрый режим)
| **Свойство**                  |**PolicyBased**| **RouteBased**                              |
| ---                           | ---           | ---                                         |
| Версия IKE                   |IKEv1          |IKEv2                                        |
| Алгоритмы шифрования и хэширования |1. AES256, SHA256<br>2) AES256, SHA1<br>3. AES128, SHA1<br>4. 3DES, SHA1 |[Предложения по сопоставлению безопасности в быстром режиме на основе маршрутизации](#RouteBasedOffers) |
| Срок действия SA (время)            |3600 секунд  |27 000 секунд                                |
| Срок действия SA (байты)           |102 400 000 КБ | -                                           |
| Полная безопасность пересылки (PFS) |Нет             |[Предложения по сопоставлению безопасности в быстром режиме на основе маршрутизации](#RouteBasedOffers) |
| Обнаружение неиспользуемых одноранговых узлов (DPD)     |Не поддерживается  |Поддерживаются                                    |


### <a name ="RouteBasedOffers"></a>Предложения по сопоставлению безопасности IPsec для VPN на основе маршрутов (в быстром режиме)
В следующей таблице перечислены предложения по сопоставлению безопасности IPsec (в быстром режиме). Предложения перечислены в порядке предпочтения представления или принятия предложения.

#### <a name="azure-gateway-as-initiator"></a>Шлюз Azure в качестве инициатора
|-  |**Шифрование**|**Аутентификация**|**Группа PFS**|
|---| ---          |---               |---          |
| 1 |GCM AES256    |GCM (AES256)      |None         |
| 2 |AES256        |SHA1              |None         |
| 3 |3DES          |SHA1              |None         |
| 4 |AES256        |SHA256            |None         |
| 5 |AES128        |SHA1              |None         |
| 6 |3DES          |SHA256            |None         |

#### <a name="azure-gateway-as-responder"></a>Шлюз Azure в качестве ответчика
|-  |**Шифрование**|**Аутентификация**|**Группа PFS**|
|---| ---          | ---              |---          |
| 1 |GCM AES256    |GCM (AES256)      |None         |
| 2 |AES256        |SHA1              |None         |
| 3 |3DES          |SHA1              |None         |
| 4 |AES256        |SHA256            |None         |
| 5 |AES128        |SHA1              |None         |
| 6 |3DES          |SHA256            |None         |
| 7 |DES           |SHA1              |None         |
| 8 |AES256        |SHA1              |1            |
| 9 |AES256        |SHA1              |2            |
| 10|AES256        |SHA1              |14           |
| 11|AES128        |SHA1              |1            |
| 12|AES128        |SHA1              |2            |
| 13.|AES128        |SHA1              |14           |
| 14|3DES          |SHA1              |1            |
| 15|3DES          |SHA1              |2            |
| 16|3DES          |SHA256            |2            |
| 17|AES256        |SHA256            |1            |
| 18|AES256        |SHA256            |2            |
| 19|AES256        |SHA256            |14           |
| 20|AES256        |SHA1              |24           |
| 21|AES256        |SHA256            |24           |
| 22|AES128        |SHA256            |None         |
| 23|AES128        |SHA256            |1            |
| 24|AES128        |SHA256            |2            |
| 25|AES128        |SHA256            |14           |
| 26|3DES          |SHA1              |14           |

* Можно указать NULL-шифрование ESP IPsec для высокопроизводительных VPN-шлюзов и VPN-шлюзов типа RouteBased. NULL-шифрование не защищает данные при передаче. Его следует использовать только в случаях, когда требуется максимальная пропускная способность и минимальная задержка.  Клиенты могут выбрать его в случаях, когда данные передаются между виртуальными сетями или где-то в решении применяется шифрование.
* Для распределенных подключений через Интернет используйте параметры по умолчанию VPN-шлюзов Azure с алгоритмами шифрования и хэширования, перечисленными в таблицах выше, чтобы обеспечить безопасность обмена важными данными.

## <a name="known"></a>Известные проблемы совместимости устройств

> [!IMPORTANT]
> Известно о нескольких проблемах совместимости между VPN-устройствами сторонних производителей и VPN-шлюзами Azure. Команда разработчиков Azure активно сотрудничает с поставщиками для устранения проблем, перечисленных ниже. По мере устранения проблем данная страница будет дополняться наиболее актуальными сведениями. Рекомендуется периодически просматривать ее.
>
>

### <a name="feb-16-2017"></a>16 февраля 2017 г.

**Устройства Palo Alto Networks с версией PAN-OS, предшествующей 7.1.4,** для VPN на основе маршрутов Azure: если используются VPN-устройства от Palo Alto Networks с версией PAN-OS, предшествующей 7.1.4, и возникают проблемы с подключением к VPN-шлюзам Azure на основе маршрутов, выполните приведенные ниже действия.

1. Проверьте версию встроенного ПО устройства Palo Alto Networks. Если версия PAN-OS предшествует версии 7.1.4, выполните обновление до версии 7.1.4.
2. На устройстве Palo Alto Networks измените время существования этапа 2 сопоставления безопасности (или быстрого режима сопоставления безопасности) на 28 800 секунд (8 часов) при подключении к VPN-шлюзу Azure.
3. Если проблема с подключением не исчезнет, отправьте запрос в службу поддержки на портале Azure.


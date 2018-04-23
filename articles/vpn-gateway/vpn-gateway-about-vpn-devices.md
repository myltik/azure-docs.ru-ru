---
title: VPN-устройства для распределенных подключений Azure | Документация Майкрософт
description: В этой статье описываются VPN-устройства и параметры IPsec для подключений типа "сеть — сеть" через VPN-шлюз, а также приводятся ссылки на примеры и инструкции по настройке.
services: vpn-gateway
documentationcenter: na
author: yushwang
manager: rossort
editor: ''
tags: azure-resource-manager, azure-service-management
ms.assetid: ba449333-2716-4b7f-9889-ecc521e4d616
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/29/2018
ms.author: yushwang
ms.openlocfilehash: b3d9d45da0fb62445867d13c9dff7502af77e8a8
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/20/2018
---
# <a name="about-vpn-devices-and-ipsecike-parameters-for-site-to-site-vpn-gateway-connections"></a>VPN-устройства и параметры IPsec/IKE для подключений типа "сеть — сеть" через VPN-шлюз

Для настройки распределенного VPN-подключения типа "сеть — сеть" через VPN-шлюз требуется VPN-устройство. Подключения типа "сеть — сеть" можно использовать для создания гибридного решения, а также когда требуется безопасное подключение между локальной и виртуальной сетями. В этой статье перечислены проверенные VPN-устройства и параметры IPsec/IKE для VPN-шлюзов.

> [!IMPORTANT]
> Ознакомьтесь с [известными проблемами совместимости устройств](#known), если вы столкнулись с проблемами подключения между локальными VPN-устройствами и VPN-шлюзами.
>

### <a name="items-to-note-when-viewing-the-tables"></a>На что следует обратите внимание при просмотре таблицы:

* Изменилась терминология, связанная с VPN-шлюзами Azure. Изменились только названия. Функциональность осталась прежней,
  * Статическая маршрутизация — на основе политик (PolicyBased).
  * Динамическая маршрутизация — на основе маршрутов (RouteBased).
* Спецификации высокопроизводительных VPN-шлюзов и VPN-шлюзов типа RouteBased одинаковы, если не указано иное. Например, проверенные VPN-устройства, совместимые с VPN-шлюзами RouteBased, также совместимы с высокопроизводительными VPN-шлюзами.

## <a name="devicetable"></a>Проверенные VPN-устройства и руководства по конфигурации устройства

> [!NOTE]
> При настройке подключения типа "сеть — сеть" для VPN-устройства необходимо указывать общедоступный IP-адрес IPv4.
>

В сотрудничестве с поставщиками устройств мы утвердили набор стандартных VPN-устройств. Все устройства из приведенного ниже списка семейств должны работать с VPN-шлюзами. Чтобы понять, какой тип VPN (PolicyBased или RouteBased) использовать для VPN-шлюза, который необходимо настроить, ознакомьтесь с [этим разделом](vpn-gateway-about-vpn-gateway-settings.md#vpntype).

Чтобы настроить VPN-устройство, обратитесь к ссылкам, соответствующим семейству устройств. Ссылки на инструкции по настройке будут предоставляться по мере возможности. За поддержкой VPN-устройства обратитесь к его изготовителю.

|**поставщик**          |**Семейство устройств**     |**Минимальная версия ОС** |**Инструкции по настройке PolicyBased** |**Инструкции по настройке RouteBased** |
| ---                | ---                  | ---                   | ---            | ---           |
| A10 Networks, Inc. |Thunder CFW           |ACOS 4.1.1             |Не совместимо  |[Руководство по настройке](https://www.a10networks.com/resources/deployment-guides/a10-thunder-cfw-ipsec-vpn-interoperability-azure-vpn-gateways)|
| Allied Telesis     |VPN-маршрутизаторы серии AR |2.9.2                  |Скоро     |Не совместимо  |
| Barracuda Networks, Inc. |Брандмауэр Barracuda NextGen серии F |PolicyBased: 5.4.3<br>RouteBased: 6.2.0 |[Руководство по настройке](https://techlib.barracuda.com/NGF/AzurePolicyBasedVPNGW) |[Руководство по настройке](https://techlib.barracuda.com/NGF/AzureRouteBasedVPNGW) |
| Barracuda Networks, Inc. |Брандмауэр Barracuda NextGen серии Х |Barracuda Firewall 6.5 |[Руководство по настройке](https://techlib.barracuda.com/BFW/ConfigAzureVPNGateway) |Не совместимо |
| Brocade            |Vyatta 5400 vRouter   |Virtual Router 6.6R3 GA|[Руководство по настройке](http://www1.brocade.com/downloads/documents/html_product_manuals/vyatta/vyatta_5400_manual/wwhelp/wwhimpl/js/html/wwhelp.htm#href=VPN_Site-to-Site%20IPsec%20VPN/Preface.1.1.html) |Не совместимо |
| Check Point |Security Gateway |R77.30 |[Руководство по настройке](https://supportcenter.checkpoint.com/supportcenter/portal?eventSubmit_doGoviewsolutiondetails=&solutionid=sk101275) |[Руководство по настройке](https://supportcenter.checkpoint.com/supportcenter/portal?eventSubmit_doGoviewsolutiondetails=&solutionid=sk101275) |
| Cisco              |ASA       |8.3<br>8.4+ (IKEv2*) |[Примеры конфигурации](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Cisco/Current/ASA) |[Руководство по настройке*](vpn-gateway-3rdparty-device-config-cisco-asa.md) |
| Cisco |ASR |PolicyBased: IOS 15.1<br>RouteBased: IOS 15.2 |[Примеры конфигурации](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Cisco/Current/ASR) |[Примеры конфигурации](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Cisco/Current/ASR) |
| Cisco |ISR |PolicyBased: IOS 15.0<br>RouteBased*: IOS 15.1 |[Примеры конфигурации](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Cisco/Current/ISR) |[Примеры конфигурации](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Cisco/Current/ISR) \** |
| Cisco |Meraki |Недоступно |Не совместимо |Не совместимо |
| Citrix |NetScaler MPX, SDX, VPX |10.1 и выше |[Руководство по настройке](https://docs.citrix.com/en-us/netscaler/11-1/system/cloudbridge-connector-introduction/cloudbridge-connector-azure.html) |Не совместимо |
| F5 |Серия BIG-IP |12.0 |[Руководство по настройке](https://devcentral.f5.com/articles/connecting-to-windows-azure-with-the-big-ip) |[Руководство по настройке](https://devcentral.f5.com/articles/big-ip-to-azure-dynamic-ipsec-tunneling) |
| Fortinet |FortiGate |FortiOS 5.6 |  |[Руководство по настройке](http://cookbook.fortinet.com/ipsec-vpn-microsoft-azure-56/) |
| Internet Initiative Japan (IIJ) |Серия SEIL |SEIL/X 4.60<br>SEIL/B1 4.60<br>SEIL/x86 3.20 |[Руководство по настройке](http://www.iij.ad.jp/biz/seil/ConfigAzureSEILVPN.pdf) |Не совместимо |
| Juniper |SRX |PolicyBased: JunOS 10.2<br>Routebased: JunOS 11.4 |[Примеры конфигурации](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/SRX) |[Примеры конфигурации](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/SRX) |
| Juniper |Серия J |PolicyBased: JunOS 10.4r9<br>RouteBased: JunOS 11.4 |[Примеры конфигурации](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/JSeries) |[Примеры конфигурации](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/JSeries) |
| Juniper |ISG |ScreenOS 6.3 |[Примеры конфигурации](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/ISG) |[Примеры конфигурации](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/ISG) |
| Juniper |SSG |ScreenOS 6.2 |[Примеры конфигурации](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/SSG) |[Примеры конфигурации](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/SSG) |
| Microsoft |Служба маршрутизации и удаленного доступа |Windows Server 2012 |Не совместимо |[Примеры конфигурации](http://go.microsoft.com/fwlink/p/?LinkId=717761) |
| Open Systems AG |Шлюз безопасности Mission Control |Недоступно |[Руководство по настройке](https://www.open.ch/_pdf/Azure/AzureVPNSetup_Installation_Guide.pdf) |Не совместимо |
| Palo Alto Networks |Все устройства под управлением PAN-OS |PAN-OS<br>PolicyBased: 6.1.5 или более поздней версии<br>RouteBased: 7.1.4 |[Руководство по настройке](https://live.paloaltonetworks.com/t5/Configuration-Articles/How-to-Configure-VPN-Tunnel-Between-a-Palo-Alto-Networks/ta-p/59065) |[Руководство по настройке](https://live.paloaltonetworks.com/t5/Integration-Articles/Configuring-IKEv2-VPN-for-Microsoft-Azure-Environment/ta-p/60340) |
| ShareTech | UTM нового поколения (серия NU) | 9.0.1.3 | Не совместимо | [Руководство по настройке](http://www.sharetech.com.tw/images/file/Solution/NU_UTM/S2S_VPN_with_Azure_Route_Based_en.pdf) |
| SonicWall |Серия TZ и NSA<br>Серия SuperMassive<br>Серия NSA класса E |SonicOS 5.8.x<br>SonicOS 5.9.x<br>SonicOS 6.x |Не совместимо |[Руководство по настройке](https://www.sonicwall.com/support/knowledge-base/170505320011694) |
| Sophos | Брандмауэр следующего поколения XG | XG версии 17 | | [Руководство по настройке](https://community.sophos.com/kb/127546) |
| Ubiquiti | EdgeRouter | EdgeOS v1.10 |  | [BGP через IKEv2 или IPsec](https://na01.safelinks.protection.outlook.com/?url=https%3A%2F%2Fhelp.ubnt.com%2Fhc%2Fen-us%2Farticles%2F115012374708&data=02%7C01%7Cmaafiri%40microsoft.com%7C7580cdf59eb94528c0de08d4f9fd78bd%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C636408314443168072&sdata=2EF5KFljZwtAGQDSm8%2FF2f6DqI2bkmA2qKG4u0rPgbQ%3D&reserved=0)<br><br>[VTI через IKEv2 или IPsec](https://na01.safelinks.protection.outlook.com/?url=https%3A%2F%2Fhelp.ubnt.com%2Fhc%2Fen-us%2Farticles%2F115012305347&data=02%7C01%7Cmaafiri%40microsoft.com%7C7580cdf59eb94528c0de08d4f9fd78bd%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C636408314443168072&sdata=ycgiDJCOQYTPN7sAEBSigphzC6mBaADz%2FgdCOm7TsXA%3D&reserved=0)
| WatchGuard |Все |Fireware XTM<br> PolicyBased: 11.11.x<br>RouteBased: 11.12.x |[Руководство по настройке](http://watchguardsupport.force.com/publicKB?type=KBArticle&SFDCID=kA2F00000000LI7KAM&lang=en_US) |[Руководство по настройке](http://watchguardsupport.force.com/publicKB?type=KBArticle&SFDCID=kA22A000000XZogSAG&lang=en_US)|

> [!NOTE]
>
> (*) Версии Cisco ASA 8.4+ с поддержкой IKEv2 могут подключаться к VPN-шлюзу Azure при помощи настраиваемой политики IPsec/IKE с параметром UsePolicyBasedTrafficSelectors. Дополнительные сведения см. в этой [статье](vpn-gateway-connect-multiple-policybased-rm-ps.md).
>
> (\**) Маршрутизаторы ISR серии 7200 поддерживают только VPN типа PolicyBased.

## <a name="configscripts"></a>Загрузка сценариев конфигурации VPN-устройства из Azure

Для некоторых устройств можно загрузить сценарии конфигурации непосредственно из Azure. Дополнительные сведения см. в статье [Загрузка сценариев конфигурации VPN-устройств для VPN-подключений типа "сеть — сеть"](vpn-gateway-download-vpndevicescript.md).

### <a name="devices-with-available-configuration-scripts"></a>Устройства с доступными сценариями конфигурации

[!INCLUDE [scripts](../../includes/vpn-gateway-device-configuration-scripts.md)]

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

> [!IMPORTANT]
> 1. Таблицы содержат комбинации алгоритмов и параметров VPN-шлюзов Azure, используемые в стандартной конфигурации. Для VPN-шлюзов на основе маршрутов, созданных с помощью модели развертывания на основе Azure Resource Management, можно указать пользовательскую политику для каждого отдельного подключения. Дополнительные сведения см. в инструкциях по [настройке политик IPsec/IKE](vpn-gateway-ipsecikepolicy-rm-powershell.md).
>
> 2. Кроме того, необходимо установить для TCP **MSS** значение **1350**. Если же VPN-устройства не поддерживают фиксацию MSS, в качестве альтернативы в интерфейсе туннеля можно указать для **MTU** **1400** байт.
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
| Алгоритмы шифрования и хэширования |1. AES256, SHA256<br>2. AES256, SHA1<br>3. AES128, SHA1<br>4. 3DES, SHA1 |1. AES256, SHA1<br>2. AES256, SHA256<br>3. AES128, SHA1<br>4. AES128, SHA256<br>5. 3DES, SHA1<br>6. 3DES, SHA256 |
| Срок действия SA           |28 800 сек     |28 800 сек     |

### <a name="ike-phase-2-quick-mode-parameters"></a>Параметры этапа 2 IKE (быстрый режим)

| **Свойство**                  |**PolicyBased**| **RouteBased**                              |
| ---                           | ---           | ---                                         |
| Версия IKE                   |IKEv1          |IKEv2                                        |
| Алгоритмы шифрования и хэширования |1. AES256, SHA256<br>2. AES256, SHA1<br>3. AES128, SHA1<br>4. 3DES, SHA1 |[Предложения по сопоставлению безопасности в быстром режиме на основе маршрутизации](#RouteBasedOffers) |
| Срок действия SA (время)            |3600 секунд  |27 000 секунд                                |
| Срок действия SA (байты)           |102 400 000 КБ | -                                           |
| Полная безопасность пересылки (PFS) |Нет              |[Предложения по сопоставлению безопасности в быстром режиме на основе маршрутизации](#RouteBasedOffers) |
| Обнаружение неиспользуемых одноранговых узлов (DPD)     |Не поддерживается  |Поддерживаются                                    |


### <a name ="RouteBasedOffers"></a>Предложения по сопоставлению безопасности IPsec для VPN на основе маршрутов (в быстром режиме)

В следующей таблице перечислены предложения по сопоставлению безопасности IPsec (в быстром режиме). Предложения перечислены в порядке предпочтения представления или принятия предложения.

#### <a name="azure-gateway-as-initiator"></a>Шлюз Azure в качестве инициатора

|-  |**Шифрование**|**Аутентификация**|**Группа PFS**|
|---| ---          |---               |---          |
| 1 |GCM AES256    |GCM (AES256)      |None         |
| 2 |AES256        |SHA1              |None         |
| 3 |3DES          |SHA1              |None         |
| 4. |AES256        |SHA256            |None         |
| 5 |AES128        |SHA1              |None         |
| 6 |3DES          |SHA256            |None         |

#### <a name="azure-gateway-as-responder"></a>Шлюз Azure в качестве ответчика

|-  |**Шифрование**|**Аутентификация**|**Группа PFS**|
|---| ---          | ---              |---          |
| 1 |GCM AES256    |GCM (AES256)      |None         |
| 2 |AES256        |SHA1              |Нет         |
| 3 |3DES          |SHA1              |None         |
| 4. |AES256        |SHA256            |None         |
| 5 |AES128        |SHA1              |Нет         |
| 6 |3DES          |SHA256            |Нет         |
| 7 |DES           |SHA1              |None         |
| 8 |AES256        |SHA1              |1            |
| 9 |AES256        |SHA1              |2            |
| 10|AES256        |SHA1              |14           |
| 11|AES128        |SHA1              |1            |
| 12|AES128        |SHA1              |2            |
| 13|AES128        |SHA1              |14           |
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

* Вы можете указать NULL-шифрование ESP IPsec для высокопроизводительных VPN-шлюзов и VPN-шлюзов типа RouteBased. NULL-шифрование не защищает данные при передаче. Его следует использовать только в случаях, когда требуется максимальная пропускная способность и минимальная задержка. Клиенты могут выбрать его в случаях, когда данные передаются между виртуальными сетями или где-то в решении применяется шифрование.
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

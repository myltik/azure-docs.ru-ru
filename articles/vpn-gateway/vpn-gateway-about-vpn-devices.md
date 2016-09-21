<properties 
   pageTitle="О VPN-устройствах для подключений VPN-шлюзов типа ";сеть — сеть"; в виртуальных сетях Azure | Microsoft Azure"
   description="В этой статье рассматриваются VPN-устройства и параметры IPsec для подключения VPN-шлюза типа ";сеть — сеть";. Статья содержит ссылки на инструкции по настройке и примеры."
   services="vpn-gateway"
   documentationCenter="na"
   authors="yushwang"
   manager="rossort"
   editor=""
  tags="azure-resource-manager, azure-service-management"/>
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/13/2016"
   ms.author="yushwang;cherylmc" />

# О VPN-устройствах для подключений VPN-шлюзов типа "сеть — сеть"

Для настройки VPN-подключения типа "сеть — сеть" требуется VPN-устройство. Подключения типа "сеть — сеть" можно использовать для создания гибридного решения или в случае, когда требуется безопасное подключение между локальной и виртуальной сетями. В этой статье рассматриваются совместимые VPN-устройства и параметры конфигурации.

>[AZURE.NOTE] При настройке подключения типа "сеть — сеть" для VPN-устройства необходимо указывать общедоступный IP-адрес IPv4.

Если устройство не отображается в таблице [проверенных VPN-устройств](#devicetable), см. раздел [Непроверенные VPN-устройства](#additionaldevices) в этой статье. Возможно, ваше устройство будет работать в Azure. За поддержкой VPN-устройства обратитесь к его изготовителю.

**На что следует обратите внимание при просмотре таблицы:**

- Изменилась терминология, связанная со статической и динамической маршрутизацией. Скорее всего, вы столкнетесь и с той, и с другой. Функциональность осталась прежней, изменились только названия.
	- Статическая маршрутизация — на основе политик (PolicyBased).
	- Динамическая маршрутизация — на основе маршрутов (RouteBased).
- Спецификации высокопроизводительных VPN-шлюзов и VPN-шлюзов типа RouteBased одинаковы, если не указано иное. Например, проверенные VPN-устройства, совместимые с VPN-шлюзами RouteBased, также совместимы с высокопроизводительными VPN-шлюзами Azure.


## <a name="devicetable"></a>Проверенные VPN-устройства 

Мы утвердили набор стандартных VPN-устройств в сотрудничестве с поставщиками устройств. Все устройства их приведенного ниже списка семейств должны работать с VPN-шлюзами Azure. Инструкции по проверке типа шлюза, который необходимо создать для настраиваемого решения, см. в статье [О VPN-устройствах для подключений VPN-шлюзов типа "сеть — сеть"](vpn-gateway-about-vpngateways.md).

Чтобы настроить VPN-устройство, обратитесь к ссылкам, соответствующим семейству устройств. За поддержкой VPN-устройства обратитесь к его изготовителю.



| **поставщик** | **Семейство устройств** | **Минимальная версия ОС** | **PolicyBased** | **RouteBased** |
|---------------------------------|----------------------------------------------------------|----------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Allied Telesis | VPN-маршрутизаторы серии AR | 2\.9.2 | Скоро | Не совместимо |
| Barracuda Networks, Inc. | Брандмауэр Barracuda NextGen серии F | PolicyBased: 5.4.3, RouteBased: 6.2.0 | [Инструкции по настройке](https://techlib.barracuda.com/NGF/AzurePolicyBasedVPNGW) | [Инструкции по настройке](https://techlib.barracuda.com/NGF/AzureRouteBasedVPNGW) |
| Barracuda Networks, Inc. | Брандмауэр Barracuda NextGen серии Х | Barracuda Firewall 6.5 | [Barracuda Firewall](https://techlib.barracuda.com/BFW/ConfigAzureVPNGateway) | Не совместимо |
| Brocade | Vyatta 5400 vRouter | Virtual Router 6.6R3 GA | [Инструкции по настройке](http://www1.brocade.com/downloads/documents/html_product_manuals/vyatta/vyatta_5400_manual/wwhelp/wwhimpl/js/html/wwhelp.htm#href=VPN_Site-to-Site%20IPsec%20VPN/Preface.1.1.html) | Не совместимо |
| Check Point | Security Gateway | R75.40, R75.40VS | [Инструкции по настройке](https://supportcenter.checkpoint.com/supportcenter/portal?eventSubmit_doGoviewsolutiondetails=&solutionid=sk101275) | [Инструкции по настройке](https://supportcenter.checkpoint.com/supportcenter/portal?eventSubmit_doGoviewsolutiondetails=&solutionid=sk101275) |
| Cisco | ASA | 8\.3 | [Примеры Cisco](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Cisco/Current/ASA) | Не совместимо |
| Cisco | ASR | IOS 15.1 (PolicyBased), IOS 15.2 (RouteBased) | [Примеры Cisco](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Cisco/Current/ASR) | [Примеры Cisco](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Cisco/Current/ASR) |
| Cisco | ISR | IOS 15.0 (PolicyBased), IOS 15.1 (RouteBased*) | [Примеры Cisco](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Cisco/Current/ISR) | [Примеры Cisco*](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Cisco/Current/ISR) |
| Citrix | NetScaler MPX, SDX, VPX |10\.1 и выше | [Инструкции по интеграции](https://docs.citrix.com/ru-RU/netscaler/11-1/system/cloudbridge-connector-introduction/cloudbridge-connector-azure.html) | Не совместимо |
| Dell SonicWALL | Серия TZ, серия NSA, серия SuperMassive, серия NSA класса E | SonicOS 5.8.x, [SonicOS 5.9.x](http://documents.software.dell.com/sonicos/5.9/microsoft-azure-configuration-guide/supported-platforms?ParentProduct=850), [SonicOS 6.x](http://documents.software.dell.com/sonicos/6.2/microsoft-azure-configuration-guide/supported-platforms?ParentProduct=646) | [Инструкции — SonicOS 6.2](http://documents.software.dell.com/sonicos/6.2/microsoft-azure-configuration-guide?ParentProduct=646) [инструкции — SonicOS 5.9](http://documents.software.dell.com/sonicos/5.9/microsoft-azure-configuration-guide?ParentProduct=850) | [Инструкции — SonicOS 6.2](http://documents.software.dell.com/sonicos/6.2/microsoft-azure-configuration-guide?ParentProduct=646) [инструкции — SonicOS 5.9](http://documents.software.dell.com/sonicos/5.9/microsoft-azure-configuration-guide?ParentProduct=850) |
| F5 | Серия BIG-IP | 12\.0 | [Инструкции по настройке](https://devcentral.f5.com/articles/connecting-to-windows-azure-with-the-big-ip) | [Инструкции по настройке](https://devcentral.f5.com/articles/big-ip-to-azure-dynamic-ipsec-tunneling) |
| Fortinet | FortiGate | FortiOS 5.2.7 | [Инструкции по настройке](http://docs.fortinet.com/d/fortigate-configuring-ipsec-vpn-between-a-fortigate-and-microsoft-azure) | [Инструкции по настройке](http://docs.fortinet.com/d/fortigate-configuring-ipsec-vpn-between-a-fortigate-and-microsoft-azure) |
| Internet Initiative Japan (IIJ) | Серия SEIL | SEIL/X 4.60, SEIL/B1 4.60, SEIL/x86 3.20 | [Инструкции по настройке](http://www.iij.ad.jp/biz/seil/ConfigAzureSEILVPN.pdf) | Не совместимо |
| Juniper | SRX | JunOS 10.2 (PolicyBased), JunOS 11.4 (RouteBased) | [Примеры Juniper](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/SRX) | [Примеры Juniper](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/SRX) |
| Juniper | Серия J | JunOS 10.4r9 (PolicyBased), JunOS 11.4 (RouteBased) | [Примеры Juniper](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/JSeries) | [Примеры Juniper](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/JSeries) |
| Juniper | ISG | ScreenOS 6.3 (PolicyBased и RouteBased) | [Примеры Juniper](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/ISG) | [Примеры Juniper](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/ISG) |
| Juniper | SSG | ScreenOS 6.2 (PolicyBased и RouteBased) | [Примеры Juniper](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/SSG) | [Примеры Juniper](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/SSG) |
| Microsoft | Служба маршрутизации и удаленного доступа | Windows Server 2012 | Не совместимо | [Примеры Майкрософт](http://go.microsoft.com/fwlink/p/?LinkId=717761) |
| Open Systems AG | Шлюз безопасности Mission Control | Недоступно | [Руководство по установке](https://www.open.ch/_pdf/Azure/AzureVPNSetup_Installation_Guide.pdf) | [Руководство по установке](https://www.open.ch/_pdf/Azure/AzureVPNSetup_Installation_Guide.pdf) |
| Openswan | Openswan | 2\.6.32 | (Ожидается в ближайшее время) | Не совместимо |
| Palo Alto Networks | Все устройства под управлением PAN-OS | PAN-OS 6.1.5 и более поздней версии (PolicyBased), PAN-OS 7.0.5 и более поздней версии (RouteBased) | [Инструкции по настройке](https://live.paloaltonetworks.com/t5/Configuration-Articles/How-to-Configure-VPN-Tunnel-Between-a-Palo-Alto-Networks/ta-p/59065) | [Инструкции по настройке](https://live.paloaltonetworks.com/t5/Integration-Articles/Configuring-IKEv2-VPN-for-Microsoft-Azure-Environment/ta-p/60340) |
| WatchGuard | Все | Fireware XTM v11.x | [Инструкции по настройке](http://customers.watchguard.com/articles/Article/Configure-a-VPN-connection-to-a-Windows-Azure-virtual-network/) | Не совместимо |

* Маршрутизаторы ISR серии 7200 поддерживают только VPN типа PolicyBased.

## <a name="additionaldevices"></a>Непроверенные VPN-устройства

Даже если ваше устройство не указано в представленной таблице проверенных VPN-устройств, оно может поддерживать подключение типа "сеть — сеть". Убедитесь, что ваше VPN-устройство соответствует минимальным требованиям, указанным в разделе "Требования к шлюзу" статьи [О шлюзах VPN](vpn-gateway-about-vpngateways.md#gateway-requirements). Устройства, соответствующие минимальным требованиям, также должны хорошо работать с VPN-шлюзами. Чтобы получить дополнительную информацию и инструкции по настройке, обратитесь к изготовителю устройства.


## Изменение примеров конфигурации устройств

После скачивания предоставленного примера конфигурации VPN-устройства некоторые значения необходимо заменить в соответствии с параметрами вашей среды.

**Чтобы изменить образец, выполните описанные ниже действия**

1. Откройте пример с помощью блокнота.
1. Найдите и замените все строки <*text*> значениями, отражающими свойства вашей среды. Не забудьте добавить "<" и ">". Если указывается имя, оно должно быть уникальным. Если команда не работает, обратитесь к документации изготовителя устройства.

| **Пример текста** | **Изменить на** |
|----------------------------------|----------------------------------------------------------------------------------------------------------------------|
| &lt;RP\_OnPremisesNetwork&gt; | Выбранное имя для данного объекта. Пример: myOnPremisesNetwork. |
| &lt;RP\_AzureNetwork&gt; | Выбранное имя для данного объекта. Пример: myAzureNetwork. |
| &lt;RP\_AccessList&gt; | Выбранное имя для данного объекта. Пример: myAzureAccessList. |
| &lt;RP\_IPSecTransformSet&gt; | Выбранное имя для данного объекта. Пример: myIPSecTransformSet. |
| &lt;RP\_IPSecCryptoMap&gt; | Выбранное имя для данного объекта. Пример: myIPSecCryptoMap. |
| &lt;SP\_AzureNetworkIpRange&gt; | Укажите диапазон. Пример: 192.168.0.0. |
| &lt;SP\_AzureNetworkSubnetMask&gt; | Укажите маску подсети. Пример: 255.255.0.0. |
| &lt;SP\_OnPremisesNetworkIpRange&gt; | Укажите локальный диапазон. Пример: 10.2.1.0. |
| &lt;SP\_OnPremisesNetworkSubnetMask&gt; | Укажите локальную маску подсети. Пример: 255.255.255.0. |
| &lt;SP\_AzureGatewayIpAddress&gt; | Эта информация относится к виртуальной сети и находится на портале управления в поле **IP-адрес шлюза**. |
| &lt;SP\_PresharedKey&gt; | Эта информация относится к виртуальной сети и находится на портале управления в разделе «Управление ключами». |



## Параметры IPsec

>[AZURE.NOTE] Хотя VPN-шлюз Azure и поддерживает перечисленные ниже значения, сейчас на нем нельзя указать или выбрать конкретную комбинацию. Все ограничения необходимо указывать на локальном VPN-устройстве. Кроме того, необходимо установить для MSS значение 1350.

### Настройка этапа 1 IKE

| **Свойство** | **PolicyBased** | **VPN-шлюз типа RouteBased и стандартный или высокопроизводительный VPN-шлюз** |
|----------------------------------------------------|--------------------------------|------------------------------------------------------------------|
| Версия IKE | IKEv1 | IKEv2 |
| Группа Диффи — Хелмана | Группа 2 (1024 бита) | Группа 2 (1024 бита) |
| Метод проверки подлинности | Общий ключ | Общий ключ |
| Алгоритмы шифрования | AES256 AES128 3DES | AES256 3DES |
| Алгоритм хэширования | SHA1(SHA128) | SHA1(SHA128), SHA2(SHA256) |
| Время существования сопоставления безопасности первого этапа (время) | 28 800 сек | 10 800 секунд |


### Настройка этапа 2 IKE

| **Свойство** | **PolicyBased** | **VPN-шлюз типа RouteBased и стандартный или высокопроизводительный VPN-шлюз** |
|--------------------------------------------------------------------------|------------------------------------------------|--------------------------------------------------------------------|
| Версия IKE | IKEv1 | IKEv2 |
| Алгоритм хэширования | SHA1(SHA128) | SHA1(SHA128) |
| Время существования сопоставления безопасности второго этапа (время) | 3600 секунд | 3600 секунд |
Время существования сопоставления безопасности второго этапа (пропускная способность) | 102 400 000 КБ | - | | Предложения для шифрования и аутентификации IPsec SA (в порядке предпочтения) | 1. ESP-AES256 2. ESP-AES128 3. ESP-3DES 4. Н/д | См. *предложения по сопоставлению безопасности IPsec для шлюзов типа RouteBased* (ниже) || Полная безопасность пересылки (PFS) | Нет | Нет* || Обнаружение неиспользуемых одноранговых узлов | Не поддерживается | Поддерживается |

* Шлюз Azure в качестве ответчика по протоколу IKE может принять группы DH 1, 2, 5, 14, 24 с обеспечением полной безопасности пересылки (PFS).

### Предложения по сопоставлению безопасности IPsec для шлюзов типа RouteBased

В таблице ниже перечислены предложения для шифрования с помощью сопоставлений безопасности и аутентификации IPsec. Предложения перечислены в порядке предпочтения представления или принятия предложения.

| **Предложения для шифрования с помощью сопоставлений безопасности и аутентификации IPsec** | **Шлюз Azure в качестве инициатора** | **Шлюз Azure в качестве ответчика** |
|---------------------------------------------------|--------------------------------------------------------------|--------------------------------------------------------------|
| 1 | ESP AES\_256 SHA | ESP AES\_128 SHA |
| 2 | ESP AES\_128 SHA | ESP 3\_DES MD5 |
| 3 | ESP 3\_DES MD5 | ESP 3\_DES SHA |
| 4 | ESP 3\_DES SHA | AH SHA1 с ESP AES\_128 с HMAC со значением NULL |
| 5 | AH SHA1 с ESP AES\_256 с HMAC со значением NULL | AH SHA1 с ESP 3\_DES с HMAC со значением NULL |
| 6 | AH SHA1 с ESP AES\_128 с HMAC со значением NULL | AH MD5 с ESP 3\_DES с HMAC со значением NULL, без предлагаемого времени существования |
| 7 | AH SHA1 с ESP 3\_DES с HMAC со значением NULL | AH SHA1 с ESP 3\_DES SHA1, без времени существования |
| 8 | AH MD5 с ESP 3\_DES с HMAC со значением NULL, без предлагаемого времени существования | AH MD5 с ESP 3\_DES MD5, без времени существования |
| 9 | AH SHA1 с ESP 3\_DES SHA1, без времени существования | ESP DES MD5 |
| 10 | AH MD5 с ESP 3\_DES MD5, без времени существования | ESP DES SHA1, без времени существования |
| 11 | ESP DES MD5 | AH SHA1 с ESP DES с HMAC со значением NULL, без предлагаемого времени существования |
| 12 | ESP DES SHA1, без времени существования | AH MD5 с ESP DES с HMAC со значением NULL, без предлагаемого времени существования |
| 13\. | AH SHA1 с ESP DES с HMAC со значением NULL, без предлагаемого времени существования | AH SHA1 с ESP DES SHA1, без времени существования |
| 14 | AH MD5 с ESP DES с HMAC со значением NULL, без предлагаемого времени существования | AH MD5 с ESP DES MD5, без времени существования |
| 15 | AH SHA1 с ESP DES SHA1, без времени существования | ESP SHA, без времени существования |
| 16 | AH MD5 с ESP DES MD5, без времени существования | ESP MD5, без времени существования |
| 17 | - | AH SHA, без времени существования || 18 | - | AH MD5, без времени существования |


- Можно указать NULL-шифрование ESP IPsec для высокопроизводительных VPN-шлюзов и VPN-шлюзов типа RouteBased. NULL-шифрование не защищает данные при передаче. Его следует использовать только в случаях, когда требуется максимальная пропускная способность и минимальная задержка. Клиенты могут выбрать его в случаях, когда данные передаются между виртуальными сетями или где-то в решении применяется шифрование.

- Для распределенных подключений через Интернет используйте параметры по умолчанию VPN-шлюзов Azure с алгоритмами шифрования и хэширования, перечисленными в таблицах выше, чтобы обеспечить безопасность обмена важными данными.

<!---HONumber=AcomDC_0914_2016-->
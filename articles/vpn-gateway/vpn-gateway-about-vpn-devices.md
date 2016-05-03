<properties 
   pageTitle="О VPN-устройствах для подключений VPN-шлюзов типа ";сеть-сеть"; в виртуальных сетях Azure | Microsoft Azure"
   description="Узнайте о VPN-устройствах и параметрах IPsec для подключений VPN-шлюзов типа ";сеть-сеть";. Подключения типа ";сеть-сеть"; можно использовать для гибридных конфигураций. Данная статья содержит ссылки на инструкции по настройке конфигурации и образцы кода для устройств VPN-шлюзов."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
  tags="azure-resource-manager, azure-service-management"/>
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="04/25/2016"
   ms.author="cherylmc" />

# О VPN-устройствах для подключений VPN-шлюзов типа "сеть-сеть"

Для настройки VPN-подключения типа "сеть-сеть" (S2S) требуется VPN-устройство. Подключения типа "сеть-сеть" можно использовать для создания гибридного решения или в случае, когда требуется безопасное подключение между локальной и виртуальной сетями. В этой статье рассматриваются совместимые VPN-устройства и параметры конфигурации. Обратите внимание, что при настройке подключения типа "сайт—сайт" для VPN-устройства необходимо указывать общедоступный IPv4-адрес.

Если устройство не отображается в таблице проверенных VPN-устройств, см. в этой статье раздел "Непроверенные VPN-устройства". Возможно, ваше устройство будет работать в Azure. За поддержкой VPN-устройства обратитесь к его изготовителю.

**На что следует обратите внимание при просмотре таблицы:**

- Изменилась терминология, связанная со статической и динамической маршрутизацией. Скорее всего, вы столкнетесь и с той, и с другой. Функциональность осталась прежней, изменились только названия.
	- Статическая маршрутизация = на основе политик
	- Динамическая маршрутизация = на основе маршрутов 
- Спецификации высокопроизводительного VPN-шлюза и VPN-шлюза на основе маршрута одинаковы, если не указано иное. Например, проверенные VPN-устройства, совместимые с VPN-шлюзами Azure на основе маршрута, также совместимы с новыми высокопроизводительными VPN-шлюзами Azure. 


## Проверенные VPN-устройства 

Мы утвердили набор стандартных VPN-устройств в сотрудничестве с поставщиками устройств. Все устройства в перечисленном ниже списке семейств должны работать с VPN-шлюзами Azure. Инструкции по проверке типа шлюза, который необходимо создать для настраиваемого решения, см. в статье [VPN-шлюзы](vpn-gateway-about-vpngateways.md).

Чтобы настроить VPN-устройство, обратитесь к ссылкам, соответствующим семейству устройств.



| **поставщик** | **Семейство устройств** | **Минимальная версия ОС** | **На основе политик** | **На основе маршрутов** |
|---------------------------------|----------------------------------------------------------|----------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Allied Telesis | VPN-маршрутизаторы серии AR | 2\.9.2 | Скоро | Не совместимо |
| Barracuda Networks, Inc. | Брандмауэр Barracuda NextGen серии F | Для политик: 5.4.3. Для маршрутов: 6.2.0. | [Инструкции по настройке](https://techlib.barracuda.com/NGF/AzurePolicyBasedVPNGW) | [Инструкции по настройке](https://techlib.barracuda.com/NGF/AzureRouteBasedVPNGW) |
| Barracuda Networks, Inc. | Брандмауэр Barracuda NextGen серии Х | Barracuda Firewall 6.5 | [Barracuda Firewall](https://techlib.barracuda.com/BFW/ConfigAzureVPNGateway) | Не совместимо |
| Brocade | Vyatta 5400 vRouter | Virtual Router 6.6R3 GA | [Инструкции по настройке](http://www1.brocade.com/downloads/documents/html_product_manuals/vyatta/vyatta_5400_manual/wwhelp/wwhimpl/js/html/wwhelp.htm#href=VPN_Site-to-Site%20IPsec%20VPN/Preface.1.1.html) | Не совместимо |
| Check Point | Security Gateway | R75.40, R75.40VS | [Инструкции по настройке](https://supportcenter.checkpoint.com/supportcenter/portal?eventSubmit_doGoviewsolutiondetails=&solutionid=sk101275) | [Инструкции по настройке](https://supportcenter.checkpoint.com/supportcenter/portal?eventSubmit_doGoviewsolutiondetails=&solutionid=sk101275) |
| Cisco | ASA | 8\.3 | [Примеры Cisco](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Cisco/Current/ASA) | Не совместимо |
| Cisco | ASR | IOS 15.1 (на основе политик), IOS 15.2 (на основе маршрутов) | [Примеры Cisco](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Cisco/Current/ASR) | [Примеры Cisco](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Cisco/Current/ASR) |
| Cisco | ISR | IOS 15.0 (на основе политик), IOS 15.1 (на основе маршрутов) | [Примеры Cisco](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Cisco/Current/ISR) | [Примеры Cisco](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Cisco/Current/ISR) |
| Citrix | Устройство CloudBridge MPX или виртуальное устройство VPX | Недоступно | [Инструкции по интеграции](https://www.citrix.com/welcome.html?resource=%2Fdownloads%2Fcloudbridge%2Fbetas-and-tech-previews%2Fcloudbridge-azure-integration) | Не совместимо |
| Dell SonicWALL | Серия TZ, серия NSA, серия SuperMassive, серия NSA класса E | SonicOS 5.8.x, [SonicOS 5.9.x](http://documents.software.dell.com/sonicos/5.9/microsoft-azure-configuration-guide/supported-platforms?ParentProduct=850), [SonicOS 6.x](http://documents.software.dell.com/sonicos/6.2/microsoft-azure-configuration-guide/supported-platforms?ParentProduct=646) | [Инструкции — SonicOS 6.2](http://documents.software.dell.com/sonicos/6.2/microsoft-azure-configuration-guide?ParentProduct=646) [инструкции — SonicOS 5.9](http://documents.software.dell.com/sonicos/5.9/microsoft-azure-configuration-guide?ParentProduct=850) | [Инструкции — SonicOS 6.2](http://documents.software.dell.com/sonicos/6.2/microsoft-azure-configuration-guide?ParentProduct=646) [инструкции — SonicOS 5.9](http://documents.software.dell.com/sonicos/5.9/microsoft-azure-configuration-guide?ParentProduct=850) |
| F5 | Серия BIG-IP | Недоступно | [Инструкции по настройке](https://devcentral.f5.com/articles/connecting-to-windows-azure-with-the-big-ip) | Не совместимо |
| Fortinet | FortiGate | FortiOS 5.0.7 | [Инструкции по настройке](http://docs.fortinet.com/fortigate/admin-guides) | [Инструкции по настройке](http://docs.fortinet.com/fortigate/admin-guides) |
| Internet Initiative Japan (IIJ) | Серия SEIL | SEIL/X 4.60, SEIL/B1 4.60, SEIL/x86 3.20 | [Инструкции по настройке](http://www.iij.ad.jp/biz/seil/ConfigAzureSEILVPN.pdf) | Не совместимо |
| Juniper | SRX | JunOS 10.2 (на основе политик), JunOS 11.4 (на основе маршрутов) | [Примеры Juniper](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/SRX) | [Примеры Juniper](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/SRX) |
| Juniper | Серия J | JunOS 10.4r9 (на основе политик), JunOS 11.4 (на основе маршрутов) | [Примеры Juniper](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/JSeries) | [Примеры Juniper](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/JSeries) |
| Juniper | ISG | ScreenOS 6.3 (на основе политик и на основе маршрутов) | [Примеры Juniper](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/ISG) | [Примеры Juniper](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/ISG) |
| Juniper | SSG | ScreenOS 6.2 (на основе политик и на основе маршрутов) | [Примеры Juniper](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/SSG) | [Примеры Juniper](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/SSG) |
| Microsoft | Служба маршрутизации и удаленного доступа | Windows Server 2012 | Не совместимо | [Примеры Майкрософт](http://go.microsoft.com/fwlink/p/?LinkId=717761) |
| Open Systems AG | Шлюз безопасности Mission Control | Недоступно | [Руководство по установке](https://www.open.ch/_pdf/Azure/AzureVPNSetup_Installation_Guide.pdf) | [Руководство по установке](https://www.open.ch/_pdf/Azure/AzureVPNSetup_Installation_Guide.pdf) |
| Openswan | Openswan | 2\.6.32 | (Ожидается в ближайшее время) | Не совместимо |
| Palo Alto Networks | Все устройства под управлением PAN-OS 5.0 или более поздней версии | PAN-OS 5x или более поздняя версия | [Palo Alto Networks](https://support.paloaltonetworks.com/) | Не совместимо |
| WatchGuard | Все | Fireware XTM v11.x | [Инструкции по настройке](http://customers.watchguard.com/articles/Article/Configure-a-VPN-connection-to-a-Windows-Azure-virtual-network/) | Не совместимо |


## Непроверенные VPN-устройства

Если ваше устройство не отображается в представленной выше таблице проверенных VPN-устройств, оно может поддерживать подключение типа "сайт — сайт". Убедитесь, что ваше VPN-устройство соответствует минимальным требованиям, указанным в разделе "Требования к шлюзу" статьи [О шлюзах VPN](vpn-gateway-about-vpngateways.md#gateway-requirements). Устройства, соответствующие минимальным требованиям, также должны хорошо работать с VPN-шлюзами. Чтобы получить дополнительную информацию и инструкции по настройке, обратитесь к изготовителю устройства.


## Изменение примеров конфигурации устройств

После скачивания предоставленного примера конфигурации VPN-устройства некоторые значения необходимо заменить в соответствии с параметрами вашей среды.

**Чтобы изменить образец, выполните описанные ниже действия**

1. Откройте пример с помощью блокнота. 
1. Найдите и замените все строки <*text*> значениями, отражающими свойства вашей среды. Не забудьте добавить < and >. Если указывается имя, оно должно быть уникальным. Если команда не работает, обратитесь к документации изготовителя устройства.

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

>[AZURE.NOTE] Хотя VPN-шлюз Azure и поддерживает перечисленные ниже значения, в данный момент на нем нельзя указать или выбрать конкретную комбинацию. Все ограничения необходимо указывать на локальном VPN-устройстве.

### Настройка этапа 1 IKE

| **Свойство** | **На основе политик** | **На основе маршрутов и стандартного или высокопроизводительного VPN-шлюза** |
|----------------------------------------------------|--------------------------------|------------------------------------------------------------------|
| Версия IKE | IKEv1 | IKEv2 |
| Группа Диффи-Хелмана | Группа 2 (1024 бита) | Группа 2 (1024 бита) |
| Метод проверки подлинности | Общий ключ | Общий ключ |
| Алгоритмы шифрования | AES256 AES128 3DES | AES256 3DES |
| Алгоритм хэширования | SHA1(SHA128) | SHA1(SHA128), SHA2(SHA256) |
| Время существования сопоставления безопасности первого этапа (время) | 28 800 сек | 10 800 секунд |


### Настройка этапа 2 IKE

| **Свойство** | **На основе политик** | **На основе маршрутов и стандартного или высокопроизводительного VPN-шлюза** |
|--------------------------------------------------------------------------|------------------------------------------------|--------------------------------------------------------------------|
| Версия IKE | IKEv1 | IKEv2 |
| Алгоритм хэширования | SHA1(SHA128) | SHA1(SHA128) |
| Время существования сопоставления безопасности второго этапа (время) | 3600 секунд | 3600 секунд |
|Время существования сопоставления безопасности второго этапа (пропускная способность) | 102 400 000 КБ | - |
| Предложения для шифрования и аутентификации IPsec SA (в порядке предпочтения) | 1. ESP-AES256 2. ESP-AES128 3. ESP-3DES 4. Н/д | См. *предложения для сопоставлений безопасности IPsec для шлюзов на основе маршрутов* (ниже) |
| Полная безопасность пересылки (PFS) | Нет | Да (группы DH 1, 2, 5, 14, 24) |
| Обнаружение неиспользуемых одноранговых узлов | Не поддерживается | Поддерживается |

### Предложения для сопоставлений безопасности IPsec для шлюзов на основе маршрутов

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
| 17 | - | AH SHA, без времени существования |
| 18 | - | AH MD5, без времени существования |


- Можно указать NULL-шифрование ESP IPsec с высокопроизводительными VPN-шлюзами на основе маршрутизации. NULL-шифрование не защищает данные при передаче. Его следует использовать только в случаях, когда требуется максимальная пропускная способность и минимальная задержка. Клиенты могут выбрать его в случаях, когда данные передаются между виртуальными сетями или где-то в решении применяется другое шифрование.

- Для распределенных подключений через Интернет используйте параметры по умолчанию VPN-шлюзов Azure с алгоритмами шифрования и хэширования, перечисленными в таблице выше, чтобы обеспечить безопасность обмена важными данными.

<!---HONumber=AcomDC_0427_2016-->
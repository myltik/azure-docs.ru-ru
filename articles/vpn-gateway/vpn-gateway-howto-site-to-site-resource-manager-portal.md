.<properties
   pageTitle="Создание виртуальной сети с VPN-подключением типа ";сеть — сеть"; при помощи Azure Resource Manager и портала Azure | Microsoft Azure"
   description="Узнайте, как создать виртуальную сеть с помощью модели развертывания Resource Manager, а затем подключить ее к локальной сети с помощью подключения типа ";сеть — сеть"; через VPN-шлюз."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/31/2016"
   ms.author="cherylmc"/>

# Создание виртуальной сети с подключением типа "сеть — сеть" с помощью портала Azure

> [AZURE.SELECTOR]
- [Портал Azure](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
- [Классический портал Azure](vpn-gateway-site-to-site-create.md)
- [PowerShell — Resource Manager](vpn-gateway-create-site-to-site-rm-powershell.md)


В этой статье мы покажем вам, как создать виртуальную сеть, соединенную с локальной сетью через VPN-подключение типа "сеть — сеть", используя **модель развертывания с помощью Azure Resource Manager** и портал Azure. Подключения типа "сеть — сеть" можно использовать для распределенных и гибридных конфигураций.

![Схема](./media/vpn-gateway-howto-site-to-site-resource-manager-portal/s2srmportal.png)



### Модели развертывания и средства для подключений типа "сеть — сеть"

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

[AZURE.INCLUDE [vpn-gateway-table-site-to-site-table](../../includes/vpn-gateway-table-site-to-site-include.md)]

Если вы хотите объединить виртуальные сети, не создавая соединение с локальным расположением, см. статью [Настройка подключения между виртуальными сетями](vpn-gateway-vnet-vnet-rm-ps.md).

## Перед началом работы

Перед началом настройки убедитесь, что у вас есть следующие компоненты.

- Совместимое VPN-устройство и пользователь, который может настроить его. См. статью [О VPN-устройствах](vpn-gateway-about-vpn-devices.md). Если вы не умеете настраивать VPN-устройство или не знаете диапазоны IP-адресов в своей локальной сети, вам следует найти того, кто сможет предоставить вам нужную информацию.

- Внешний общедоступный IP-адрес для VPN-устройства. Этот IP-адрес не может располагаться вне преобразования сетевых адресов (NAT).
	
- Подписка Azure. Если у вас нет подписки Azure, вы можете [активировать преимущества для подписчиков MSDN](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) или [зарегистрировать бесплатную учетную запись](http://azure.microsoft.com/pricing/free-trial/).

### <a name="values"></a>Примеры значений конфигурации для этого упражнения


Выполняя эти шаги в качестве упражнения, используйте следующие значения.

- Имя виртуальной сети: TestVNet1
- Адресное пространство: 10.11.0.0/16 и 10.12.0.0/16
- Подсети:
	- FrontEnd: 10.11.0.0/24
	- BackEnd: 10.12.0.0/24
	- GatewaySubnet: 10.12.255.0/27
- Группа ресурсов: TestRG1
- Расположение: восточная часть США
- DNS-сервер: 8.8.8.8
- Имя шлюза: VNet1GW
- Общедоступный IP-адрес: VNet1GWIP
- Тип VPN: на основе маршрутов
- Тип подключения: "сеть — сеть" (IPsec)
- Тип шлюза: VPN
- Имя шлюза локальной сети: Site2
- Имя подключения: VNet1toSite2



## 1\. Создать виртуальную сеть 

Если у вас уже есть виртуальная сеть, проверьте совместимость параметров со структурой VPN-шлюза. Обратите особое внимание на подсети, которые могут пересекаться с другими сетями. В таком случае подключение не будет работать правильно. Если виртуальная сеть настроена правильно, переходите к действиям из раздела [Выбор DNS-сервера](#dns).

### Создание виртуальной сети

[AZURE.INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-basic-vnet-rm-portal-include.md)]

## 2) Добавление дополнительного адресного пространства и подсетей

После создания виртуальной сети в нее можно добавить дополнительное адресное пространство и подсети.

[AZURE.INCLUDE [vpn-gateway-additional-address-space](../../includes/vpn-gateway-additional-address-space-include.md)]

## <a name="dns"></a>3. Выбор DNS-сервера

Если вы создаете эту конфигурацию в качестве упражнения, при выборе DNS-сервера можно использовать [такие значения](#values).

### Выбор DNS-сервера

[AZURE.INCLUDE [vpn-gateway-add-dns-rm-portal](../../includes/vpn-gateway-add-dns-rm-portal-include.md)]

## 4\. Создание подсети шлюза

Прежде чем подключать шлюз к виртуальной сети, нужно создать подсеть шлюза для виртуальной сети, к которой необходимо подключиться. Создаваемой подсети шлюза необходимо присвоить имя *GatewaySubnet*, иначе она не будет работать как следует.

Для префикса подсети шлюза для некоторых конфигураций требуется подсеть с длиной префикса 28 бит (/28) или больше, чтобы вместить количество IP-адресов, необходимое для пула. Это означает, что подсеть шлюза должна иметь префикс /28, /27, /26 и т. д. Вы можете создать подсеть большого размера в рамках этого руководства, чтобы учесть будущие дополнения к конфигурации.

Если вы создаете эту конфигурацию в качестве упражнения, при создании подсети шлюза можно использовать [такие значения](#values).

### Создание подсети шлюза

[AZURE.INCLUDE [vpn-gateway-no-nsg](../../includes/vpn-gateway-no-nsg-include.md)]

[AZURE.INCLUDE [vpn-gateway-add-gwsubnet-rm-portal](../../includes/vpn-gateway-add-gwsubnet-rm-portal-include.md)]

## 5\. Создание шлюза виртуальной сети

Если вы создаете эту конфигурацию в качестве упражнения, при создании шлюза можно использовать [такие значения](#values).

### Создание шлюза виртуальной сети

[AZURE.INCLUDE [vpn-gateway-add-gw-rm-portal](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]

## 6\. Создание локального сетевого шлюза

Термин *шлюз локальной сети* означает локальное расположение. Шлюзу локальной сети необходимо присвоить имя, по которому на него сможет ссылаться служба Azure.

Если вы создаете эту конфигурацию в качестве упражнения, эти [значения](#values) можно использовать для справки при добавлении локального сайта.

### Создание локального сетевого шлюза

[AZURE.INCLUDE [vpn-gateway-add-lng-rm-portal](../../includes/vpn-gateway-add-lng-rm-portal-include.md)]

## 7\. Настройка VPN-устройства

[AZURE.INCLUDE [vpn-gateway-configure-vpn-device-rm](../../includes/vpn-gateway-configure-vpn-device-rm-include.md)]

## 8\. Создание VPN-подключения типа "сеть — сеть"

Теперь вам нужно создать VPN-подключение типа "сеть — сеть" между шлюзом виртуальной сети и VPN-устройством. Обязательно подставьте собственные значения. Общий ключ должен соответствовать значению, использованному в конфигурации VPN-устройства.

Прежде чем начинать работу с этим разделом, убедитесь, что создание шлюза виртуальной сети и шлюзов локальных сетей завершено. Если вы создаете эту конфигурацию в качестве упражнения, при создании подключения можно использовать [такие значения](#values).

### Создание VPN-подключения


[AZURE.INCLUDE [vpn-gateway-add-site-to-site-connection-rm-portal](../../includes/vpn-gateway-add-site-to-site-connection-rm-portal-include.md)]

## 9\. Проверка VPN-подключения

VPN-подключение можно проверить на портале или с помощью PowerShell.

[AZURE.INCLUDE [vpn-gateway-verify-connection-rm](../../includes/vpn-gateway-verify-connection-rm-include.md)]

## Дальнейшие действия

- Установив подключение, можно добавить виртуальные машины в виртуальные сети. Дополнительную информацию о виртуальных машинах см. в [схеме обучения](https://azure.microsoft.com/documentation/learning-paths/virtual-machines).

- Сведения о BGP см. в статьях [Обзор использования BGP с VPN-шлюзами Azure](vpn-gateway-bgp-overview.md) и [Настройка BGP на VPN-шлюзах Azure с помощью Azure Resource Manager и PowerShell](vpn-gateway-bgp-resource-manager-ps.md).

<!---HONumber=AcomDC_0831_2016-->
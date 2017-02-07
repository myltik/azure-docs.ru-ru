---
title: "Соединение локальной сети с виртуальною сетью Azure с помощью подключения типа &quot;сеть — сеть&quot; и портала | Документация Майкрософт"
description: "Сведения о создании подключения IPsec между локальной сетью и виртуальной сетью Azure через общедоступный Интернет. Они помогут вам создать подключение типа &quot;сеть — сеть&quot; с использованием VPN-шлюза и портала."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 827a4db7-7fa5-4eaf-b7e1-e1518c51c815
ms.service: vpn-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/23/2017
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: eea00841708212789e14fa8717d83dd81d472bac
ms.openlocfilehash: c99788aded849933289555dd133b8146d6b2bdf2


---
# <a name="create-a-vnet-with-a-site-to-site-connection-using-the-azure-portal"></a>Создание виртуальной сети с подключением типа "сеть — сеть" с помощью портала Azure
> [!div class="op_single_selector"]
> * [Resource Manager — портал Azure](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
> * [Resource Manager — PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md)
> * [Классическая модель — классический портал](vpn-gateway-site-to-site-create.md)
> 
> 

В этой статье мы покажем вам, как создать виртуальную сеть, соединенную с локальной сетью через подключение VPN-шлюза типа "сеть — сеть", используя модель развертывания с помощью Azure Resource Manager и портал Azure. Подключения типа "сеть — сеть" можно использовать для распределенных и гибридных конфигураций.

![Схема подключения типа "сеть — сеть" через VPN-шлюз](./media/vpn-gateway-howto-site-to-site-resource-manager-portal/site-to-site-diagram.png)

### <a name="deployment-models-and-methods-for-site-to-site-connections"></a>Модели развертывания и способы настройки подключений типа "сеть — сеть"
[!INCLUDE [deployment models](../../includes/vpn-gateway-deployment-models-include.md)]

Следующая таблица показывает модели развертывания, доступные в настоящее время, и методы для конфигурации "сеть — сеть". Когда появится статья с руководством по конфигурации, мы разместим прямую ссылку на нее в этой таблице.

[!INCLUDE [site-to-site table](../../includes/vpn-gateway-table-site-to-site-include.md)]

#### <a name="additional-configurations"></a>Дополнительные конфигурации
Если вы хотите объединить виртуальные сети, не создавая соединение с локальным расположением, см. статью о [настройке подключения между виртуальными сетями](vpn-gateway-vnet-vnet-rm-ps.md). Если вы хотите добавить подключение "сеть — сеть" к виртуальной сети, в которой уже есть подключение, см. статью о [добавлении подключения "сеть — сеть" к виртуальной сети с существующим подключением через VPN-шлюз](vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md).

## <a name="before-you-begin"></a>Перед началом работы
Перед началом настройки убедитесь, что у вас есть следующие компоненты.

* Совместимое VPN-устройство и пользователь, который может настроить его. См. статью о [VPN-устройствах](vpn-gateway-about-vpn-devices.md). Если вы не умеете настраивать VPN-устройство или не знаете диапазоны IP-адресов в своей локальной сети, вам следует найти того, кто сможет предоставить вам нужную информацию.
* Внешний общедоступный IP-адрес для VPN-устройства. Этот IP-адрес не может располагаться вне преобразования сетевых адресов (NAT).
* Подписка Azure. Если у вас нет подписки Azure, вы можете [активировать преимущества для подписчиков MSDN](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) или [зарегистрировать бесплатную учетную запись](http://azure.microsoft.com/pricing/free-trial).

### <a name="a-namevaluesasample-configuration-values-for-this-exercise"></a><a name="values"></a>Примеры значений конфигурации для этого упражнения
Выполняя эти шаги в качестве упражнения, используйте следующие значения.

* **Имя виртуальной сети:** TestVNet1
* **Адресное пространство:** 10.11.0.0/16 и 10.12.0.0/16
* **Подсети:**
  * FrontEnd: 10.11.0.0/24
  * BackEnd: 10.12.0.0/24
  * GatewaySubnet: 10.12.255.0/27
* **Группа ресурсов:** TestRG1
* **Расположение:** восточная часть США.
* **DNS-сервер:** 8.8.8.8
* **Имя шлюза:** VNet1GW
* **Общедоступный IP-адрес:** VNet1GWIP
* **Тип VPN:** на основе маршрутов.
* **Тип подключения:** "сеть — сеть" (IPsec)
* **Тип шлюза:** VPN.
* **Имя шлюза локальной сети:** Site2
* **Имя подключения:** VNet1toSite2

## <a name="a-namecreatvneta1-create-a-virtual-network"></a><a name="CreatVNet"></a>1. Создать виртуальную сеть
Если у вас уже есть виртуальная сеть, проверьте совместимость параметров со структурой VPN-шлюза. Обратите особое внимание на подсети, которые могут пересекаться с другими сетями. В таком случае подключение не будет работать правильно. Если виртуальная сеть настроена правильно, переходите к действиям из раздела [Выбор DNS-сервера](#dns) .

### <a name="to-create-a-virtual-network"></a>Создание виртуальной сети
[!INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-basic-vnet-rm-portal-include.md)]

## <a name="a-namesubnetsa2-add-additional-address-space-and-subnets"></a><a name="subnets"></a>2. Добавление дополнительного адресного пространства и подсетей
После создания виртуальной сети в нее можно добавить дополнительное адресное пространство и подсети.

[!INCLUDE [vpn-gateway-additional-address-space](../../includes/vpn-gateway-additional-address-space-include.md)]

## <a name="a-namednsa3-specify-a-dns-server"></a><a name="dns"></a>3. Выбор DNS-сервера
### <a name="to-specify-a-dns-server"></a>Выбор DNS-сервера
[!INCLUDE [vpn-gateway-add-dns-rm-portal](../../includes/vpn-gateway-add-dns-rm-portal-include.md)]

## <a name="a-namegatewaysubneta4-create-a-gateway-subnet"></a><a name="gatewaysubnet"></a>4. Создание подсети шлюза
Прежде чем подключать шлюз к виртуальной сети, нужно создать подсеть шлюза для виртуальной сети, к которой необходимо подключиться. По возможности рекомендуется создать подсеть шлюза с использованием блока CIDR с маской /28 или /27, чтобы обеспечить достаточно IP-адресов для удовлетворения дополнительных будущих требований к конфигурации.

Если вы создаете эту конфигурацию в качестве упражнения, при создании подсети шлюза можно использовать [такие значения](#values) .

### <a name="to-create-a-gateway-subnet"></a>Создание подсети шлюза
[!INCLUDE [vpn-gateway-add-gwsubnet-rm-portal](../../includes/vpn-gateway-add-gwsubnet-rm-portal-include.md)]

## <a name="a-namevnetgatewaya5-create-a-virtual-network-gateway"></a><a name="VNetGateway"></a>5. Создание шлюза виртуальной сети
Если вы создаете эту конфигурацию в качестве упражнения, можно использовать [примеры значений конфигурации](#values).

### <a name="to-create-a-virtual-network-gateway"></a>Создание шлюза виртуальной сети
[!INCLUDE [vpn-gateway-add-gw-rm-portal](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]

## <a name="a-namelocalnetworkgatewaya6-create-a-local-network-gateway"></a><a name="LocalNetworkGateway"></a>6. Создание локального сетевого шлюза
Термин "шлюз локальной сети" означает локальное расположение. Шлюзу локальной сети необходимо присвоить имя, по которому на него сможет ссылаться служба Azure. 

Если вы создаете эту конфигурацию в качестве упражнения, можно использовать [примеры значений конфигурации](#values).

### <a name="to-create-a-local-network-gateway"></a>Создание локального сетевого шлюза
[!INCLUDE [vpn-gateway-add-lng-rm-portal](../../includes/vpn-gateway-add-lng-rm-portal-include.md)]

## <a name="a-namevpndevicea7-configure-your-vpn-device"></a><a name="VPNDevice"></a>7. Настройка устройства VPN
[!INCLUDE [vpn-gateway-configure-vpn-device-rm](../../includes/vpn-gateway-configure-vpn-device-rm-include.md)]

## <a name="a-namecreateconnectiona8-create-a-site-to-site-vpn-connection"></a><a name="CreateConnection"></a>8. Создание VPN-подключения типа "сеть — сеть"
Теперь вам нужно создать VPN-подключение типа "сеть — сеть" между шлюзом виртуальной сети и VPN-устройством. Обязательно подставьте собственные значения. Общий ключ должен соответствовать значению, использованному в конфигурации VPN-устройства. 

Прежде чем начинать работу с этим разделом, убедитесь, что создание шлюза виртуальной сети и шлюзов локальных сетей завершено. Если вы создаете эту конфигурацию в качестве упражнения, при создании подключения можно использовать [такие значения](#values) .

### <a name="to-create-the-vpn-connection"></a>Создание VPN-подключения
[!INCLUDE [vpn-gateway-add-site-to-site-connection-rm-portal](../../includes/vpn-gateway-add-site-to-site-connection-rm-portal-include.md)]

## <a name="a-nameverifyconnectiona9-verify-the-vpn-connection"></a><a name="VerifyConnection"></a>9. Проверка VPN-подключения
VPN-подключение можно проверить на портале или с помощью PowerShell.

[!INCLUDE [vpn-gateway-verify-connection-rm](../../includes/vpn-gateway-verify-connection-rm-include.md)]

## <a name="next-steps"></a>Дальнейшие действия
*  Установив подключение, можно добавить виртуальные машины в виртуальные сети. Дополнительные сведения о виртуальных машинах см. [здесь](https://docs.microsoft.com/azure/#pivot=services&panel=Compute).
*  Сведения о BGP см. в статьях [Обзор использования BGP с VPN-шлюзами Azure](vpn-gateway-bgp-overview.md) и [Настройка BGP на VPN-шлюзах Azure с помощью Azure Resource Manager и PowerShell](vpn-gateway-bgp-resource-manager-ps.md).




<!--HONumber=Jan17_HO4-->



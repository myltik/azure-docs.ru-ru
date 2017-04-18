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
ms.date: 04/04/2017
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: d6f4caebeeced1286f24dd5fcb4f5fc7d8591785
ms.lasthandoff: 04/12/2017


---
# <a name="create-a-site-to-site-connection-in-the-azure-portal"></a>Создание подключения типа "сеть — сеть" на портале Azure

Подключение типа "сеть — сеть" (S2S) через VPN-шлюз — это подключение через туннель VPN по протоколу IPsec/IKE (IKEv1 или IKEv2). Для этого типа подключения требуется локальное VPN-устройство, которому назначен общедоступный IP-адрес и которое не расположено за NAT. Подключения типа "сеть — сеть" можно использовать для распределенных и гибридных конфигураций.

![Схема подключения типа "сеть — сеть" через VPN-шлюз](./media/vpn-gateway-howto-site-to-site-resource-manager-portal/site-to-site-diagram.png)

В этой статье мы покажем вам, как создать виртуальную сеть, соединенную с локальной сетью через подключение VPN-шлюза типа "сеть — сеть", используя модель развертывания с помощью Azure Resource Manager и портал Azure. Эту конфигурацию также можно создать, используя разные средства развертывания, либо с помощью классической модели развертывания, выбрав вариант из следующего списка:

> [!div class="op_single_selector"]
> * [Resource Manager — портал Azure](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
> * [Resource Manager — PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md)
> * [Классическая модель — портал Azure](vpn-gateway-howto-site-to-site-classic-portal.md)
> * [Классическая модель — классический портал](vpn-gateway-site-to-site-create.md)
>
>


#### <a name="additional-configurations"></a>Дополнительные конфигурации
Если вы хотите объединить виртуальные сети, не создавая соединение с локальным расположением, см. статью о [настройке подключения между виртуальными сетями](vpn-gateway-vnet-vnet-rm-ps.md). Если вы хотите добавить подключение "сеть — сеть" к виртуальной сети, в которой уже есть подключение, см. статью о [добавлении подключения "сеть — сеть" к виртуальной сети с существующим подключением через VPN-шлюз](vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md).

## <a name="before-you-begin"></a>Перед началом работы

[!INCLUDE [deployment models](../../includes/vpn-gateway-deployment-models-include.md)]

Перед началом настройки убедитесь, что у вас есть следующие компоненты.

* Совместимое VPN-устройство и пользователь, который может настроить его. См. статью о [VPN-устройствах](vpn-gateway-about-vpn-devices.md).
* Если вы не знаете пространства IP-адресов в своей локальной сети, найдите того, кто сможет предоставить вам нужную информацию. Подключения типа "сеть —сеть" не могут содержать перекрывающиеся пространства адресов.
* Внешний общедоступный IP-адрес для VPN-устройства. Этот IP-адрес не может располагаться вне преобразования сетевых адресов (NAT).
* Подписка Azure. Если у вас нет подписки Azure, вы можете [активировать преимущества для подписчиков MSDN](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) или [зарегистрировать бесплатную учетную запись](http://azure.microsoft.com/pricing/free-trial).

### <a name="values"></a>Примеры значений
При выполнении этих шагов в качестве упражнения можно использовать следующие примеры значений.

* **Имя виртуальной сети:** TestVNet1
* **Адресное пространство:** 
    * 10.11.0.0/16;
    * 10.12.0.0/16 (необязательно для этого упражнения).
* **Подсети:**
  * FrontEnd: 10.11.0.0/24
  * BackEnd: 10.12.0.0/24 (необязательно для этого упражнения).
  * Подсеть шлюза: 10.11.255.0/27.
* **Группа ресурсов:** TestRG1
* **Расположение:** восточная часть США.
* **DNS-сервер**: IP-адрес вашего DNS-сервера.
* **Имя шлюза виртуальной сети:** VNet1GW.
* **Общедоступный IP-адрес:** VNet1GWIP
* **Тип VPN:** на основе маршрутов.
* **Тип подключения:** "сеть — сеть" (IPsec)
* **Тип шлюза:** VPN.
* **Имя шлюза локальной сети:** Site2
* **Имя подключения:** VNet1toSite2

## <a name="CreatVNet"></a>1. Создать виртуальную сеть

[!INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-basic-vnet-s2s-rm-portal-include.md)]

## <a name="dns"></a>2. Выбор DNS-сервера
Сервер DNS обязательно указывать при подключениях типа "сеть —сеть". Но если для ресурсов, развернутых в вашей виртуальной сети, требуется разрешение имен, необходимо указать DNS-сервер. Этот параметр позволяет указать DNS-сервер, который вы хотите использовать для разрешения имен в этой виртуальной сети. Он не приводит к созданию DNS-сервера.

[!INCLUDE [vpn-gateway-add-dns-rm-portal](../../includes/vpn-gateway-add-dns-rm-portal-include.md)]

## <a name="gatewaysubnet"></a>3. Создание подсети шлюза
Необходимо создать подсеть шлюза для VPN-шлюза. Подсеть шлюза содержит IP-адреса, которые используются службами VPN-шлюза. Если возможно, создайте подсеть шлюза, используя блок CIDR /28 или /27. Так вы получите достаточно IP-адресов, чтобы удовлетворить будущие потребности при работе со шлюзом.

[!INCLUDE [vpn-gateway-add-gwsubnet-rm-portal](../../includes/vpn-gateway-add-gwsubnet-s2s-rm-portal-include.md)]

## <a name="VNetGateway"></a>4. Создание шлюза виртуальной сети

[!INCLUDE [vpn-gateway-add-gw-s2s-rm-portal](../../includes/vpn-gateway-add-gw-s2s-rm-portal-include.md)]

## <a name="LocalNetworkGateway"></a>5. Создание локального сетевого шлюза
Термин "шлюз локальной сети" означает локальное расположение. Параметры, указанные для шлюза локальной сети, определяют адресные пространства, которые маршрутизируются на локальное VPN-устройство.

[!INCLUDE [vpn-gateway-add-lng-s2s-rm-portal](../../includes/vpn-gateway-add-lng-s2s-rm-portal-include.md)]

## <a name="VPNDevice"></a>6. Настройка устройства VPN
[!INCLUDE [vpn-gateway-configure-vpn-device-rm](../../includes/vpn-gateway-configure-vpn-device-rm-include.md)]

## <a name="CreateConnection"></a>7. Создание VPN-подключения типа "сеть — сеть"

На этом шаге вам нужно создать VPN-подключение типа "сеть — сеть" между шлюзом виртуальной сети и локальным VPN-устройством. Прежде чем начинать работу с этим разделом, убедитесь, что создание шлюза виртуальной сети и шлюзов локальных сетей завершено.

[!INCLUDE [vpn-gateway-add-site-to-site-connection-rm-portal](../../includes/vpn-gateway-add-site-to-site-connection-s2s-rm-portal-include.md)]

## <a name="VerifyConnection"></a>8. Проверка VPN-подключения

[!INCLUDE [Azure portal](../../includes/vpn-gateway-verify-connection-portal-rm-include.md)]

## <a name="next-steps"></a>Дальнейшие действия
*  Установив подключение, можно добавить виртуальные машины в виртуальные сети. Дополнительные сведения о виртуальных машинах см. [здесь](https://docs.microsoft.com/azure/#pivot=services&panel=Compute).
*  Сведения о BGP см. в статьях [Обзор использования BGP с VPN-шлюзами Azure](vpn-gateway-bgp-overview.md) и [Настройка BGP на VPN-шлюзах Azure с помощью Azure Resource Manager и PowerShell](vpn-gateway-bgp-resource-manager-ps.md).



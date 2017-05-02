---
title: "Настройка принудительного туннелирования для подключений типа &quot;сеть — сеть&quot; с помощью Resource Manager | Документация Майкрософт"
description: "Как перенаправлять или принудительно туннелировать весь интернет-трафик обратно в локальное расположение."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: cbe58db8-b598-4c9f-ac88-62c865eb8721
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/11/2017
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: 0d9afb1554158a4d88b7f161c62fa51c1bf61a7d
ms.openlocfilehash: aae7271e62552d8393ed3b0c5b31f104fae4dbbc
ms.lasthandoff: 04/12/2017


---
# <a name="configure-forced-tunneling-using-the-azure-resource-manager-deployment-model"></a>Настройка принудительного туннелирования с помощью модели развертывания Azure Resource Manager

Оно позволяет перенаправлять или "принудительно направлять" весь Интернет-трафик обратно в локальное расположение через VPN типа "сеть — сеть" для проверки и аудита. Это критически важное требование безопасности, имеющееся в большинстве корпоративных ИТ-политик. Без принудительного туннелирования Интернет-трафик из виртуальных машин в Azure будет всегда поступать из инфраструктуры сети Azure непосредственно в Интернет, без возможности его проверки или аудита. Неавторизованный доступ в Интернет может привести к раскрытию информации или другим нарушениям безопасности.

[!INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)] 

В этой статье описано, как выполнить настройку принудительного туннелирования для виртуальных сетей, созданных с помощью модели развертывания Resource Manager. Принудительное туннелирование можно настроить с помощью PowerShell, но не на портале. Если вы хотите настроить принудительное туннелирование для классической модели развертывания, выберите соответствующую статью из раскрывающегося списка ниже:

> [!div class="op_single_selector"]
> * [PowerShell — классическая модель](vpn-gateway-about-forced-tunneling.md)
> * [PowerShell — Resource Manager](vpn-gateway-forced-tunneling-rm.md)
> 
> 


## <a name="about-forced-tunneling"></a>Сведения о принудительном туннелировании
На схеме ниже показан принцип работы принудительного туннелирования. 

![Принудительное туннелирование](./media/vpn-gateway-forced-tunneling-rm/forced-tunnel.png)

В примере выше для интерфейсной подсети не применяется принудительное туннелирование. Рабочие нагрузки в интерфейсной подсети могут продолжать принимать запросы клиентов непосредственно из Интернета и отвечать на них. Для подсетей среднего уровня и внутренних подсетей применяется принудительное туннелирование. Любые исходящие подключения из этих двух подсетей к Интернету будут принудительно перенаправлены обратно на локальный сайт через один из VPN-туннелей, работающих по протоколу S2S.

Это позволяет ограничивать и проверять доступ в Интернет с виртуальных машин или облачных служб в Azure, при этом поддерживая необходимую многоуровневую архитектуру служб. Кроме того, вы можете применять принудительное туннелирование для всех виртуальных сетей, если в них нет рабочих нагрузок, требующих взаимодействия с Интернетом.

## <a name="requirements-and-considerations"></a>Требования и рекомендации
В Azure принудительное туннелирование настраивается с помощью определяемых пользователем маршрутов виртуальной сети. Перенаправление трафика на локальный сайт выполняется с помощью маршрута по умолчанию к VPN-шлюзу Azure. Сведения об определяемых пользователем маршрутах см. в статье [Что такое определяемые пользователем маршруты и IP-пересылка?](../virtual-network/virtual-networks-udr-overview.md)

* Каждая подсеть виртуальной сети имеет встроенные системные таблицы маршрутизации. Системная таблица маршрутизации содержит три указанные ниже группы маршрутов.
  
  * **Маршруты локальной виртуальной сети.** Они ведут непосредственно к виртуальным машинам назначения в той же виртуальной сети.
  * **Локальные маршруты.** Ведут к VPN-шлюзу Azure.
  * **Маршрут по умолчанию.** Ведет напрямую в Интернет. Пакеты, предназначенные для частных IP-адресов, не входящих в предыдущие два маршрута, будут удалены.
* В этой процедуре используются определяемые пользователем маршруты для создания таблицы маршрутизации с целью добавления маршрута по умолчанию и последующего сопоставления таблицы маршрутизации с подсетями вашей виртуальной сети для включения принудительного туннелирования в этих подсетях.
* Принудительное туннелирование должно быть связано с виртуальной сетью, в которой есть VPN-шлюз на основе маршрута. Из числа локальных межорганизационных сайтов, подключенных к виртуальной сети, необходимо выбрать "сайт по умолчанию".
* С помощью этого механизма невозможно настроить принудительное туннелирование ExpressRoute. Такое туннелирование включается, когда предлагается маршрут по умолчанию с помощью сеансов пиринга BGP ExpressRoute. Дополнительные сведения см. в [документации по ExpressRoute](https://azure.microsoft.com/documentation/services/expressroute/).

## <a name="configuration-overview"></a>Общие сведения о настройке
Следующая процедура поможет создать группу ресурсов и виртуальную сеть. Затем вы создадите VPN-шлюз и настроите принудительное туннелирование. В этом примере виртуальная сеть MultiTier-VNet содержит три подсети (*Frontend*, *Midtier* и *Backend*) с четырьмя распределенными подключениями *DefaultSiteHQ* и тремя *Branches*.

Выполнив указанные ниже действия, можно настроить подключение *DefaultSiteHQ* в качестве подключения к сайту по умолчанию для принудительного туннелирования, а также настроить принудительное туннелирование для подсетей Midtier и Backend.

## <a name="before-you-begin"></a>Перед началом работы
Перед началом настройки убедитесь, что у вас есть следующие компоненты.

* Подписка Azure. Если у вас нет подписки Azure, вы можете [активировать преимущества для подписчиков MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) или [зарегистрировать бесплатную учетную запись](https://azure.microsoft.com/pricing/free-trial/).
* Вам потребуется установить последнюю версию командлетов PowerShell Azure Resource Manager (1.0 или более позднюю версию). Дополнительные сведения об установке командлетов PowerShell см. в статье [Установка и настройка Azure PowerShell](/powershell/azureps-cmdlets-docs).

## <a name="configure-forced-tunneling"></a>Настройка принудительного туннелирования
1. В консоли PowerShell войдите в свою учетную запись Azure. Командлет запрашивает учетные данные входа для вашей учетной записи Azure. После выполнения входа он загружает параметры учетной записи, чтобы они были доступны в Azure PowerShell.

  ```powershell
  Login-AzureRmAccount
  ```
2. Получите список подписок Azure.

 ```powershell
 Get-AzureRmSubscription
 ```
3. Укажите подписку, которую нужно использовать.

  ```powershell
  Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"
  ```
4. Создайте группу ресурсов.

  ```powershell
  New-AzureRmResourceGroup -Name "ForcedTunneling" -Location "North Europe"
  ```
5. Создайте виртуальную сеть и укажите подсети.

  ```powershell 
  $s1 = New-AzureRmVirtualNetworkSubnetConfig -Name "Frontend" -AddressPrefix "10.1.0.0/24"
  $s2 = New-AzureRmVirtualNetworkSubnetConfig -Name "Midtier" -AddressPrefix "10.1.1.0/24"
  $s3 = New-AzureRmVirtualNetworkSubnetConfig -Name "Backend" -AddressPrefix "10.1.2.0/24"
  $s4 = New-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -AddressPrefix "10.1.200.0/28"
  $vnet = New-AzureRmVirtualNetwork -Name "MultiTier-VNet" -Location "North Europe" -ResourceGroupName "ForcedTunneling" -AddressPrefix "10.1.0.0/16" -Subnet $s1,$s2,$s3,$s4
  ```
6. Создайте шлюзы локальной сети.

  ```powershell
  $lng1 = New-AzureRmLocalNetworkGateway -Name "DefaultSiteHQ" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -GatewayIpAddress "111.111.111.111" -AddressPrefix "192.168.1.0/24"
  $lng2 = New-AzureRmLocalNetworkGateway -Name "Branch1" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -GatewayIpAddress "111.111.111.112" -AddressPrefix "192.168.2.0/24"
  $lng3 = New-AzureRmLocalNetworkGateway -Name "Branch2" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -GatewayIpAddress "111.111.111.113" -AddressPrefix "192.168.3.0/24"
  $lng4 = New-AzureRmLocalNetworkGateway -Name "Branch3" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -GatewayIpAddress "111.111.111.114" -AddressPrefix "192.168.4.0/24"
  ```
7. Создайте таблицу маршрутизации и правило маршрутизации.

  ```powershell
  New-AzureRmRouteTable –Name "MyRouteTable" -ResourceGroupName "ForcedTunneling" –Location "North Europe"
  $rt = Get-AzureRmRouteTable –Name "MyRouteTable" -ResourceGroupName "ForcedTunneling" 
  Add-AzureRmRouteConfig -Name "DefaultRoute" -AddressPrefix "0.0.0.0/0" -NextHopType VirtualNetworkGateway -RouteTable $rt
  Set-AzureRmRouteTable -RouteTable $rt
  ```
8. Сопоставьте таблицу маршрутизации с подсетями "Midtier" и "Backend".

  ```powershell
  $vnet = Get-AzureRmVirtualNetwork -Name "MultiTier-Vnet" -ResourceGroupName "ForcedTunneling"
  Set-AzureRmVirtualNetworkSubnetConfig -Name "MidTier" -VirtualNetwork $vnet -AddressPrefix "10.1.1.0/24" -RouteTable $rt
  Set-AzureRmVirtualNetworkSubnetConfig -Name "Backend" -VirtualNetwork $vnet -AddressPrefix "10.1.2.0/24" -RouteTable $rt
  Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
  ```
9. Создайте шлюза с узлом по умолчанию. Выполнение этого шага занимает некоторое время (иногда 45 минут или более), так как выполняется создание и настройка шлюза.<br> **-GatewayDefaultSite** — это параметр командлета, благодаря которому работает конфигурация принудительной маршрутизации. Поэтому настройте этот параметр должным образом. Он доступен только в PowerShell 1.0 или более поздней версии.

  ```powershell
  $pip = New-AzureRmPublicIpAddress -Name "GatewayIP" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -AllocationMethod Dynamic
  $gwsubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet
  $ipconfig = New-AzureRmVirtualNetworkGatewayIpConfig -Name "gwIpConfig" -SubnetId $gwsubnet.Id -PublicIpAddressId $pip.Id
  New-AzureRmVirtualNetworkGateway -Name "Gateway1" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -IpConfigurations $ipconfig -GatewayType Vpn -VpnType RouteBased -GatewayDefaultSite $lng1 -EnableBgp $false
  ```
10. Установите VPN-подключения "сайт — сайт".

  ```powershell
  $gateway = Get-AzureRmVirtualNetworkGateway -Name "Gateway1" -ResourceGroupName "ForcedTunneling"
  $lng1 = Get-AzureRmLocalNetworkGateway -Name "DefaultSiteHQ" -ResourceGroupName "ForcedTunneling" 
  $lng2 = Get-AzureRmLocalNetworkGateway -Name "Branch1" -ResourceGroupName "ForcedTunneling" 
  $lng3 = Get-AzureRmLocalNetworkGateway -Name "Branch2" -ResourceGroupName "ForcedTunneling" 
  $lng4 = Get-AzureRmLocalNetworkGateway -Name "Branch3" -ResourceGroupName "ForcedTunneling" 
    
  New-AzureRmVirtualNetworkGatewayConnection -Name "Connection1" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -VirtualNetworkGateway1 $gateway -LocalNetworkGateway2 $lng1 -ConnectionType IPsec -SharedKey "preSharedKey"
  New-AzureRmVirtualNetworkGatewayConnection -Name "Connection2" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -VirtualNetworkGateway1 $gateway -LocalNetworkGateway2 $lng2 -ConnectionType IPsec -SharedKey "preSharedKey"
  New-AzureRmVirtualNetworkGatewayConnection -Name "Connection3" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -VirtualNetworkGateway1 $gateway -LocalNetworkGateway2 $lng3 -ConnectionType IPsec -SharedKey "preSharedKey"
  New-AzureRmVirtualNetworkGatewayConnection -Name "Connection4" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -VirtualNetworkGateway1 $gateway -LocalNetworkGateway2 $lng4 -ConnectionType IPsec -SharedKey "preSharedKey"
    
  Get-AzureRmVirtualNetworkGatewayConnection -Name "Connection1" -ResourceGroupName "ForcedTunneling"
  ```


---
title: "Соединение локальной сети с виртуальною сетью Azure с помощью подключения типа &quot;сеть — сеть&quot; и PowerShell | Документация Майкрософт"
description: "Сведения о создании подключения IPsec между локальной сетью и виртуальной сетью Azure через общедоступный Интернет. Они помогут вам создать подключение типа &quot;сеть — сеть&quot; с использованием VPN-шлюза и PowerShell."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: fcc2fda5-4493-4c15-9436-84d35adbda8e
ms.service: vpn-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/01/2017
ms.author: cherylmc
ms.translationtype: Human Translation
ms.sourcegitcommit: 64bd7f356673b385581c8060b17cba721d0cf8e3
ms.openlocfilehash: f485dc6a52488b44bbd0e68432d3fd2bcdb060a9
ms.contentlocale: ru-ru
ms.lasthandoff: 05/02/2017


---
# <a name="create-a-vnet-with-a-site-to-site-vpn-connection-using-powershell"></a>Создание виртуальной сети с VPN-подключением типа "сеть — сеть" с помощью PowerShell

В этой статье показано, как с помощью PowerShell создавать подключение VPN-шлюза типа "сеть — сеть" от локальной сети к виртуальной. Приведенные в этой статье инструкции относятся к модели развертывания с помощью Resource Manager. Эту конфигурацию также можно создать с помощью разных средств или моделей развертывания, выбрав вариант из следующего списка:

> [!div class="op_single_selector"]
> * [Resource Manager — портал Azure](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
> * [Resource Manager — PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md)
> * [Resource Manager — интерфейс командной строки](vpn-gateway-howto-site-to-site-resource-manager-cli.md)
> * [Классическая модель — портал Azure](vpn-gateway-howto-site-to-site-classic-portal.md)
> * [Классическая модель — классический портал](vpn-gateway-site-to-site-create.md)
> 
>


![Схема подключения типа "сеть — сеть" через VPN-шлюз](./media/vpn-gateway-create-site-to-site-rm-powershell/site-to-site-connection-diagram.png)

Подключение VPN-шлюза типа "сеть — сеть" используется для подключения между локальной сетью и виртуальной сетью Azure через туннель VPN по протоколу IPsec/IKE (IKEv1 или IKEv2). Для этого типа подключения требуется локальное VPN-устройство, которому назначен внешний общедоступный IP-адрес. Дополнительные сведения о VPN-шлюзах см. в [этой статье](vpn-gateway-about-vpngateways.md).

## <a name="before-you-begin"></a>Перед началом работы

Перед началом настройки убедитесь, что удовлетворены следующие требования:

* Убедитесь, что выбрана модель развертывания с помощью Resource Manager. [!INCLUDE [deployment models](../../includes/vpn-gateway-deployment-models-include.md)] 
* Совместимое VPN-устройство и пользователь, который может настроить его. Дополнительные сведения о совместимых устройствах VPN и их настройке см. в [этой статье](vpn-gateway-about-vpn-devices.md).
* Внешний общедоступный IPV4-адрес для VPN-устройства. Этот IP-адрес не может располагаться вне преобразования сетевых адресов (NAT).
* Если вы не знаете диапазоны IP-адресов в своей конфигурации локальной сети, найдите того, кто сможет предоставить вам нужную информацию. При создании этой конфигурации необходимо указать префиксы диапазона IP-адресов, которые Azure будет направлять к локальному расположению. Ни одна из подсетей локальной сети не может перекрывать виртуальные подсети, к которым вы хотите подключиться.
* Последняя версия командлетов PowerShell для Azure Resource Manager. Дополнительные сведения об установке командлетов PowerShell см. в статье [Как установить и настроить Azure PowerShell](/powershell/azure/overview).

### <a name="example-values"></a>Примеры значений

В примерах этой статьи мы используем следующие значения. Эти значения можно использовать для создания тестовой среды или для лучшего понимания примеров в этой статье.

```
#Example values

VnetName                = testvnet 
ResourceGroup           = testrg 
Location                = West US 
AddressSpace            = 10.0.0.0/16 
SubnetName              = Subnet1 
Subnet                  = 10.0.1.0/28 
GatewaySubnet           = 10.0.0.0/27
LocalNetworkGatewayName = LocalSite
LNG Public IP           = <VPN device IP address> 
Local Address Prefixes  = 10.0.0.0/24','20.0.0.0/24
Gateway Name            = vnetgw1
PublicIP                = gwpip
Gateway IP Config       = gwipconfig1 
VPNType                 = RouteBased 
GatewayType             = Vpn 
ConnectionName          = myGWConnection
```

## <a name="Login"></a>1. Подключение к подписке

[!INCLUDE [vpn-gateway-ps-login](../../includes/vpn-gateway-ps-login-include.md)]

## <a name="VNet"></a>2. Создание виртуальной сети и подсети шлюза

Если у вас нет виртуальной сети, создайте ее. При создании виртуальной сети убедитесь, что указанные адресные пространства не перекрываются ни с одним из адресных пространств, которые используются в локальной сети. Для этой конфигурации необходима также подсеть шлюза. Шлюз виртуальной сети использует подсеть шлюза, которая содержит IP-адреса, используемые службами VPN-шлюза. При создании подсеть шлюза нужно назвать GatewaySubnet. Если будет присвоено другое имя, вы создадите подсеть, но Azure не будет рассматривать ее как подсеть шлюза.

Размер указанной подсети шлюза зависит от конфигурации VPN-шлюза, которую вы хотите создать. Хотя можно создать подсеть шлюза размером /29, рекомендуется создать подсеть большего размера, включающую несколько адресов, выбрав значение /27 или /28. Использование более крупной подсети для шлюза позволяет создавать достаточное число IP-адресов с учетом возможных конфигураций в будущем.

[!INCLUDE [vpn-gateway-no-nsg](../../includes/vpn-gateway-no-nsg-include.md)]

### <a name="to-create-a-virtual-network-and-a-gateway-subnet"></a>Создание виртуальной сети и подсети шлюза

В этом примере создается виртуальная сеть и подсеть шлюза. Если у вас уже есть виртуальная сеть, в которую необходимо добавить подсеть шлюза, см. раздел [Добавление подсети шлюза в созданную виртуальную сеть](#gatewaysubnet).

Создайте группу ресурсов:

```powershell
New-AzureRmResourceGroup -Name testrg -Location 'West US'
```

Создайте свою виртуальную сеть. 

1. Задайте переменные.

  ```powershell
  $subnet1 = New-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.0.0/27
  $subnet2 = New-AzureRmVirtualNetworkSubnetConfig -Name 'Subnet1' -AddressPrefix '10.0.1.0/28'
  ```
2. Создайте виртуальную сеть.

  ```powershell
  New-AzureRmVirtualNetwork -Name testvnet -ResourceGroupName testrg `
  -Location 'West US' -AddressPrefix 10.0.0.0/16 -Subnet $subnet1, $subnet2
  ```

### <a name="gatewaysubnet"></a>Добавление подсети шлюза в уже созданную виртуальную сеть

1. Задайте переменные.

  ```powershell
  $vnet = Get-AzureRmVirtualNetwork -ResourceGroupName testrg -Name testvnet
  ```
2. Создайте подсеть шлюза.

  ```powershell
  Add-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.0.0/27 -VirtualNetwork $vnet
  ```
3. Теперь нужно настроить конфигурацию.

  ```powershell
  Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
  ```

## 3. <a name="localnet"></a>Создание шлюза локальной сети

Обычно термин "шлюз локальной сети" означает локальное расположение. Присвойте сайту имя, по которому Azure может обращаться к этому сайту, а затем укажите IP-адрес локального VPN-устройства, к которому вы подключитесь. Вы можете также указать префиксы IP-адресов, которые будут направляться через VPN-шлюз к VPN-устройству. Указываемые префиксы адресов расположены в локальной сети. При изменении локальной сети вы сможете без проблем обновить эти префиксы.

Используйте следующие значения:

* Параметр *GatewayIPAddress* — это IP-адрес локального VPN-устройства. Ваше VPN-устройство не может располагаться вне преобразования сетевых адресов (NAT).
* Параметр *AddressPrefix* — локальное адресное пространство.

- Чтобы добавить шлюз локальной сети с одним префиксом адреса, используйте этот пример:

  ```powershell
  New-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg `
  -Location 'West US' -GatewayIpAddress '23.99.221.164' -AddressPrefix   '10.0.0.0/24'
  ```

- Чтобы добавить шлюз локальной сети с несколькими префиксами адреса, используйте этот пример:

  ```powershell
  New-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg `
  -Location 'West US' -GatewayIpAddress '23.99.221.164' -AddressPrefix @('10.0.0.0/24','20.0.0.0/24')
  ```

- Изменение префиксов IP-адресов для локального сетевого шлюза<br>
Иногда префиксы адресов шлюза локальной сети могут изменяться. Действия по изменению префиксов IP-адресов зависят от того, создано ли подключение через VPN-шлюз. См. раздел [Изменение префиксов IP-адресов для локального сетевого шлюза](#modify) этой статьи.

## <a name="PublicIP"></a>4. Запрос общедоступного IP-адреса

VPN-шлюз должен иметь общедоступный IP-адрес. Сначала запросите ресурс IP-адреса, а затем укажите его при создании шлюза виртуальной сети. IP-адрес динамически назначается ресурсу при создании VPN-шлюза. В настоящее время VPN-шлюз поддерживает только *динамическое* выделение общедоступных IP-адресов. Вы не можете запросить назначение статического общедоступного IP-адреса. Однако это не означает, что IP-адрес изменяется после назначения VPN-шлюзу. Общедоступный IP-адрес изменяется только после удаления и повторного создания шлюза. При изменении размера, сбросе или других внутренних операциях обслуживания или обновления IP-адрес VPN-шлюза не изменяется.

Запросите общедоступный IP-адрес, который будет назначен VPN-шлюзу виртуальной сети.

```powershell
$gwpip= New-AzureRmPublicIpAddress -Name gwpip -ResourceGroupName testrg -Location 'West US' -AllocationMethod Dynamic
```

## <a name="GatewayIPConfig"></a>5. Создание конфигурации IP-адресации шлюза

Конфигурация шлюза определяет используемые подсеть и общедоступный IP-адрес. Используйте следующий пример, чтобы создать конфигурацию шлюза:

```powershell
$vnet = Get-AzureRmVirtualNetwork -Name testvnet -ResourceGroupName testrg
$subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
$gwipconfig = New-AzureRmVirtualNetworkGatewayIpConfig -Name gwipconfig1 -SubnetId $subnet.Id -PublicIpAddressId $gwpip.Id
```

## <a name="CreateGateway"></a>6. Создание VPN-шлюза

Создайте VPN-шлюз виртуальной сети. Создание VPN-шлюза может занять от 45 минут и более.

Используйте следующие значения:

* Для конфигурации "сеть — сеть" задайте для параметра *-GatewayType* значение *Vpn*. Тип шлюза всегда зависит от реализуемой конфигурации. Например, для других конфигураций шлюза может потребоваться тип шлюза ExpressRoute.
* У параметра *-VpnType* может быть значение *RouteBased* (в некоторых документах такой шлюз называется шлюзом с динамической маршрутизацией) или *PolicyBased* (в некоторых документах — шлюз со статической маршрутизацией). Дополнительные сведения о типах VPN-шлюзов см. в статье [Основные сведения о VPN-шлюзах Azure](vpn-gateway-about-vpngateways.md).
* У параметра *-GatewaySku* может быть значение Basic, Standard или HighPerformance. К определенным номерам SKU применяются ограничения настройки. Дополнительные сведения см. в разделе о [номерах SKU шлюзов](vpn-gateway-about-vpngateways.md#gateway-skus).

```powershell
New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg `
-Location 'West US' -IpConfigurations $gwipconfig -GatewayType Vpn `
-VpnType RouteBased -GatewaySku Standard
```

## <a name="ConfigureVPNDevice"></a>7. Настройка устройства VPN

[!INCLUDE [Configure VPN device](../../includes/vpn-gateway-configure-vpn-device-rm-include.md)]

Чтобы найти общедоступный IP-адрес виртуального сетевого шлюза с помощью PowerShell, используйте следующий пример:

```powershell
Get-AzureRmPublicIpAddress -Name GW1PublicIP -ResourceGroupName TestRG
```

## <a name="CreateConnection"></a>8. Создание VPN-подключения

Далее вам нужно создать VPN-подключение типа "сеть — сеть" между шлюзом виртуальной сети и VPN-устройством. Обязательно подставьте собственные значения. Общий ключ должен соответствовать значению, использованному в конфигурации VPN-устройства. Обратите внимание, что для подключения типа "сеть — сеть" параметр -ConnectionType имеет значение *IPsec*.

1. Задайте переменные.
  ```powershell
  $gateway1 = Get-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
  $local = Get-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg
  ```

2. Создайте подключение.
  ```powershell
  New-AzureRmVirtualNetworkGatewayConnection -Name MyGWConnection -ResourceGroupName testrg `
  -Location 'West US' -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local `
  -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'
  ```

Через некоторое время будет установлено подключение.

## <a name="toverify"></a>9. Проверка VPN-подключения
Существует несколько разных способов для проверки VPN-подключения.

[!INCLUDE [Verify connection](../../includes/vpn-gateway-verify-connection-ps-rm-include.md)]

## <a name="connectVM"></a>Подключение к виртуальной машине

[!INCLUDE [Connect to VM](../../includes/vpn-gateway-connect-vm-s2s-include.md)]


## <a name="modify"></a>Изменение префиксов IP-адресов для шлюза локальной сети

Если префиксы IP-адресов, которые должны перенаправляться к локальному расположению, изменились, можно изменить шлюз локальной сети. Мы приводим два набора инструкций. Выбор тех или других инструкций зависит от того, создано ли у вас подключение через шлюз.

[!INCLUDE [Modify prefixes](../../includes/vpn-gateway-modify-ip-prefix-rm-include.md)]

## <a name="modifygwipaddress"></a>Изменение IP-адреса шлюза для шлюза локальной сети

[!INCLUDE [Modify gw IP](../../includes/vpn-gateway-modify-lng-gateway-ip-rm-include.md)]

## <a name="next-steps"></a>Дальнейшие действия

*  Установив подключение, можно добавить виртуальные машины в виртуальные сети. Дополнительные сведения о виртуальных машинах см. [здесь](https://docs.microsoft.com/azure/#pivot=services&panel=Compute).
* Сведения о BGP см. в статьях [Обзор использования BGP с VPN-шлюзами Azure](vpn-gateway-bgp-overview.md) и [Настройка BGP на VPN-шлюзах Azure с помощью Azure Resource Manager и PowerShell](vpn-gateway-bgp-resource-manager-ps.md).


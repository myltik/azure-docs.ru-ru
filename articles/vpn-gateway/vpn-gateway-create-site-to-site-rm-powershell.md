---
title: Соединение локальной сети с виртуальною сетью Azure с помощью подключения типа "сеть — сеть" и PowerShell | Документация Майкрософт
description: Сведения о создании подключения IPsec между локальной сетью и виртуальной сетью Azure через общедоступный Интернет. Они помогут вам создать подключение типа "сеть — сеть" с использованием VPN-шлюза и PowerShell.
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: fcc2fda5-4493-4c15-9436-84d35adbda8e
ms.service: vpn-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/28/2018
ms.author: cherylmc
ms.openlocfilehash: 6ee23beaa450129be7c173da9efc687cc3790a15
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/03/2018
---
# <a name="create-a-vnet-with-a-site-to-site-vpn-connection-using-powershell"></a>Создание виртуальной сети с VPN-подключением типа "сеть — сеть" с помощью PowerShell

В этой статье показано, как с помощью PowerShell создавать подключение VPN-шлюза типа "сеть — сеть" от локальной сети к виртуальной. Приведенные в этой статье инструкции относятся к модели развертывания с помощью Resource Manager. Эту конфигурацию также можно создать с помощью разных средств или моделей развертывания, выбрав вариант из следующего списка:

> [!div class="op_single_selector"]
> * [портал Azure](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
> * [PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md)
> * [ИНТЕРФЕЙС КОМАНДНОЙ СТРОКИ](vpn-gateway-howto-site-to-site-resource-manager-cli.md)
> * [Портал Azure (классический)](vpn-gateway-howto-site-to-site-classic-portal.md)
> 
>

Подключение VPN-шлюза типа "сеть — сеть" используется для подключения между локальной сетью и виртуальной сетью Azure через туннель VPN по протоколу IPsec/IKE (IKEv1 или IKEv2). Для этого типа подключения требуется локальное VPN-устройство, которому назначен внешний общедоступный IP-адрес. Дополнительные сведения о VPN-шлюзах см. в [этой статье](vpn-gateway-about-vpngateways.md).

![Схема подключения типа "сеть — сеть" через VPN-шлюз](./media/vpn-gateway-create-site-to-site-rm-powershell/site-to-site-diagram.png)

## <a name="before"></a>Перед началом работы

Перед началом настройки убедитесь, что удовлетворены следующие требования:

* Убедитесь, что у вас есть совместимое VPN–устройство и пользователь, который может настроить его. Дополнительные сведения о совместимых устройствах VPN и их настройке см. в [этой статье](vpn-gateway-about-vpn-devices.md).
* Убедитесь, что у вас есть общедоступный IPv4–адрес для вашего VPN–устройства. Этот IP-адрес не может располагаться вне преобразования сетевых адресов (NAT).
* Если вы не знаете диапазоны IP-адресов в своей конфигурации локальной сети, найдите того, кто сможет предоставить вам нужную информацию. При создании этой конфигурации необходимо указать префиксы диапазона IP-адресов, которые Azure будет направлять к локальному расположению. Ни одна из подсетей локальной сети не может перекрывать виртуальные подсети, к которым вы хотите подключиться.

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

### <a name="running-powershell-locally"></a>Запуск PowerShell в локальной среде

Чтобы установить и использовать PowerShell локально, потребуется установить последнюю версию командлетов PowerShell для Azure Resource Manager. Командлеты PowerShell обновляются часто, и вам, как правило, необходимо обновить командлеты PowerShell, чтобы получить новейшие функциональные возможности. Если вы не обновите командлеты PowerShell, при указании значений может произойти сбой. 

Чтобы определить, какая версия используется, выполните командлет Get-Module -ListAvailable AzureRM. Если необходимо выполнить обновление, см. статью об [установке модуля Azure PowerShell](/powershell/azure/install-azurerm-ps). Подробнее: [Установка и настройка Azure PowerShell](/powershell/azure/overview).
Если модуль PowerShell запущен локально, необходимо также выполнить командлет Login-AzureRmAccount, чтобы создать подключение к Azure.


### <a name="example"></a>Примеры значений

В примерах этой статьи мы используем следующие значения. Эти значения можно использовать для создания тестовой среды или для лучшего понимания примеров в этой статье.

```
#Example values

VnetName                = VNet1
ResourceGroup           = TestRG1
Location                = East US 
AddressSpace            = 10.1.0.0/16 
SubnetName              = Frontend 
Subnet                  = 10.1.0.0/24 
GatewaySubnet           = 10.1.255.0/27
LocalNetworkGatewayName = Site1
LNG Public IP           = <On-premises VPN device IP address> 
Local Address Prefixes  = 10.101.0.0/24, 10.101.1.0/24
Gateway Name            = VNet1GW
PublicIP                = VNet1GWPIP
Gateway IP Config       = gwipconfig1 
VPNType                 = RouteBased 
GatewayType             = Vpn 
ConnectionName          = VNet1toSite1

```

## <a name="VNet"></a>1. Создание виртуальной сети и подсети шлюза

Если у вас нет виртуальной сети, создайте ее. При создании виртуальной сети убедитесь, что указанные адресные пространства не перекрываются ни с одним из адресных пространств, которые используются в локальной сети. 

>[!NOTE]
>Для подключения этой виртуальной сети к локальному расположению вам необходимо обратиться к администратору локальной сети, чтобы выделить диапазон IP-адресов, который будет использоваться специально для этой виртуальной сети. Если повторяющийся диапазон адресов существует на обеих сторонах VPN-подключения, трафик не будет маршрутизироваться должным образом. Кроме того, если вы хотите подключить виртуальную сеть к другой виртуальной сети, адресное пространство не должно пересекаться с другой виртуальной сетью. Спланируйте конфигурацию сети соответствующим образом.
>
>

### <a name="about-the-gateway-subnet"></a>Сведения о подсети шлюза

[!INCLUDE [About gateway subnets](../../includes/vpn-gateway-about-gwsubnet-include.md)]

[!INCLUDE [No NSG warning](../../includes/vpn-gateway-no-nsg-include.md)]

### <a name="vnet"></a>Создание виртуальной сети и подсети шлюза

В этом примере создается виртуальная сеть и подсеть шлюза. Если у вас уже есть виртуальная сеть, в которую необходимо добавить подсеть шлюза, см. раздел [Добавление подсети шлюза в созданную виртуальную сеть](#gatewaysubnet).

Создайте группу ресурсов:

```azurepowershell-interactive
New-AzureRmResourceGroup -Name TestRG1 -Location 'East US'
```

Создайте свою виртуальную сеть.

1. Задайте переменные.

  ```azurepowershell-interactive
  $subnet1 = New-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.1.255.0/27
  $subnet2 = New-AzureRmVirtualNetworkSubnetConfig -Name 'Frontend' -AddressPrefix 10.1.0.0/24
  ```
2. Создайте виртуальную сеть.

  ```azurepowershell-interactive
  New-AzureRmVirtualNetwork -Name VNet1 -ResourceGroupName TestRG1 `
  -Location 'East US' -AddressPrefix 10.1.0.0/16 -Subnet $subnet1, $subnet2
  ```

### <a name="gatewaysubnet"></a>Добавление подсети шлюза в уже созданную виртуальную сеть

Если у вас уже есть виртуальная сеть, но необходимо добавить подсеть шлюза, следуйте инструкциям из этого раздела.

1. Задайте переменные.

  ```azurepowershell-interactive
  $vnet = Get-AzureRmVirtualNetwork -ResourceGroupName TestRG1 -Name TestVet1
  ```
2. Создайте подсеть шлюза.

  ```azurepowershell-interactive
  Add-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.1.255.0/27 -VirtualNetwork $vnet
  ```
3. Теперь нужно настроить конфигурацию.

  ```azurepowershell-interactive
  Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
  ```

## 2. <a name="localnet"></a>Создание шлюза локальной сети

Обычно термин "шлюз локальной сети" означает локальное расположение. Присвойте сайту имя, по которому Azure может обращаться к этому сайту, а затем укажите IP-адрес локального VPN-устройства, к которому вы подключитесь. Вы можете также указать префиксы IP-адресов, которые будут направляться через VPN-шлюз к VPN-устройству. Указываемые префиксы адресов расположены в локальной сети. При изменении локальной сети вы сможете без проблем обновить эти префиксы.

Используйте следующие значения:

* Параметр *GatewayIPAddress* — это IP-адрес локального VPN-устройства. Ваше VPN-устройство не может располагаться вне преобразования сетевых адресов (NAT).
* Параметр *AddressPrefix* — локальное адресное пространство.

Чтобы добавить шлюз локальной сети с одним префиксом адреса, используйте этот пример:

  ```azurepowershell-interactive
  New-AzureRmLocalNetworkGateway -Name Site1 -ResourceGroupName TestRG1 `
  -Location 'East US' -GatewayIpAddress '23.99.221.164' -AddressPrefix '10.101.0.0/24'
  ```

Чтобы добавить шлюз локальной сети с несколькими префиксами адреса, используйте этот пример:

  ```azurepowershell-interactive
  New-AzureRmLocalNetworkGateway -Name Site1 -ResourceGroupName TestRG1 `
  -Location 'East US' -GatewayIpAddress '23.99.221.164' -AddressPrefix @('10.101.0.0/24','10.101.1.0/24')
  ```

Изменение префиксов IP-адресов для локального сетевого шлюза<br>
Иногда префиксы адресов шлюза локальной сети могут изменяться. Действия по изменению префиксов IP-адресов зависят от того, создано ли подключение через VPN-шлюз. См. раздел [Изменение префиксов IP-адресов для локального сетевого шлюза](#modify) этой статьи.

## <a name="PublicIP"></a>3. Запрос общедоступного IP-адреса

VPN-шлюз должен иметь общедоступный IP-адрес. Сначала запросите ресурс IP-адреса, а затем укажите его при создании шлюза виртуальной сети. IP-адрес динамически назначается ресурсу при создании VPN-шлюза. В настоящее время VPN-шлюз поддерживает только *динамическое* выделение общедоступных IP-адресов. Вы не можете запросить назначение статического общедоступного IP-адреса. Однако это не означает, что IP-адрес изменяется после назначения VPN-шлюзу. Общедоступный IP-адрес изменяется только после удаления и повторного создания шлюза. При изменении размера, сбросе или других внутренних операциях обслуживания или обновления IP-адрес VPN-шлюза не изменяется.

Запросите общедоступный IP-адрес, который будет назначен VPN-шлюзу виртуальной сети.

```azurepowershell-interactive
$gwpip= New-AzureRmPublicIpAddress -Name VNet1GWPIP -ResourceGroupName TestRG1 -Location 'East US' -AllocationMethod Dynamic
```

## <a name="GatewayIPConfig"></a>4. Создание конфигурации IP-адресации шлюза

Конфигурация шлюза определяет используемые подсеть и общедоступный IP-адрес. Используйте следующий пример, чтобы создать конфигурацию шлюза:

```azurepowershell-interactive
$vnet = Get-AzureRmVirtualNetwork -Name VNet1 -ResourceGroupName TestRG1
$subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
$gwipconfig = New-AzureRmVirtualNetworkGatewayIpConfig -Name gwipconfig1 -SubnetId $subnet.Id -PublicIpAddressId $gwpip.Id
```

## <a name="CreateGateway"></a>5. Создание VPN-шлюза

Создайте VPN-шлюз виртуальной сети.

Используйте следующие значения:

* Для конфигурации "сеть — сеть" задайте для параметра *-GatewayType* значение *Vpn*. Тип шлюза всегда зависит от реализуемой конфигурации. Например, для других конфигураций шлюза может потребоваться тип шлюза ExpressRoute.
* У параметра *-VpnType* может быть значение *RouteBased* (в некоторых документах такой шлюз называется шлюзом с динамической маршрутизацией) или *PolicyBased* (в некоторых документах — шлюз со статической маршрутизацией). Дополнительные сведения о типах VPN-шлюзов см. в статье [Основные сведения о VPN-шлюзах Azure](vpn-gateway-about-vpngateways.md).
* Выберите SKU шлюза, который нужно использовать. К определенным номерам SKU применяются ограничения настройки. Дополнительные сведения см. в разделе о [номерах SKU шлюзов](vpn-gateway-about-vpn-gateway-settings.md#gwsku). Если при создании VPN-шлюза вы получаете сообщение об ошибке с -GatewaySku, убедитесь, что установлена последняя версия командлетов PowerShell.

```azurepowershell-interactive
New-AzureRmVirtualNetworkGateway -Name VNet1GW -ResourceGroupName TestRG1 `
-Location 'East US' -IpConfigurations $gwipconfig -GatewayType Vpn `
-VpnType RouteBased -GatewaySku VpnGw1
```

После выполнения этой команды для завершения настройки шлюза может потребоваться до 45 минут.

## <a name="ConfigureVPNDevice"></a>6. Настройка устройства VPN

Для подключения типа "сеть — сеть" к локальной сети требуется VPN-устройство. На этом этапе мы настроим VPN-устройство. Чтобы настроить локальное VPN-устройство, вам потребуется следующее:

- Общий ключ. Это тот же общий ключ, который указывается при создании VPN-подключения "сеть — сеть". В наших примерах мы используем простые общие ключи. Для практического использования рекомендуется создавать более сложные ключи.
- Общедоступный IP-адрес шлюза виртуальной сети. Общедоступный IP-адрес можно просмотреть с помощью портала Azure, PowerShell или CLI. Чтобы найти общедоступный IP-адрес виртуального сетевого шлюза с помощью PowerShell, используйте следующий пример:

  ```azurepowershell-interactive
  Get-AzureRmPublicIpAddress -Name GW1PublicIP -ResourceGroupName TestRG1
  ```

[!INCLUDE [Configure VPN device](../../includes/vpn-gateway-configure-vpn-device-rm-include.md)]


## <a name="CreateConnection"></a>7. Создание VPN-подключения

Далее вам нужно создать VPN-подключение типа "сеть — сеть" между шлюзом виртуальной сети и VPN-устройством. Обязательно подставьте собственные значения. Общий ключ должен соответствовать значению, использованному в конфигурации VPN-устройства. Обратите внимание, что для подключения типа "сеть — сеть" параметр -ConnectionType имеет значение *IPsec*.

1. Задайте переменные.
  ```azurepowershell-interactive
  $gateway1 = Get-AzureRmVirtualNetworkGateway -Name VNet1GW -ResourceGroupName TestRG1
  $local = Get-AzureRmLocalNetworkGateway -Name Site1 -ResourceGroupName TestRG1
  ```

2. Создайте подключение.
  ```azurepowershell-interactive
  New-AzureRmVirtualNetworkGatewayConnection -Name VNet1toSite1 -ResourceGroupName TestRG1 `
  -Location 'East US' -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local `
  -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'
  ```

Через некоторое время будет установлено подключение.

## <a name="toverify"></a>8. Проверка VPN-подключения

Существует несколько разных способов для проверки VPN-подключения.

[!INCLUDE [Verify connection](../../includes/vpn-gateway-verify-connection-ps-rm-include.md)]

## <a name="connectVM"></a>Подключение к виртуальной машине

[!INCLUDE [Connect to a VM](../../includes/vpn-gateway-connect-vm-s2s-include.md)]


## <a name="modify"></a>Изменение префиксов IP-адресов для локального сетевого шлюза

Если префиксы IP-адресов, которые должны перенаправляться к локальному расположению, изменились, можно изменить шлюз локальной сети. Мы приводим два набора инструкций. Выбор тех или других инструкций зависит от того, создано ли у вас подключение через шлюз.

[!INCLUDE [Modify prefixes](../../includes/vpn-gateway-modify-ip-prefix-rm-include.md)]

## <a name="modifygwipaddress"></a>Изменение IP-адреса шлюза для локального сетевого шлюза

[!INCLUDE [Modify gateway IP address](../../includes/vpn-gateway-modify-lng-gateway-ip-rm-include.md)]

## <a name="next-steps"></a>Дополнительная информация

*  Установив подключение, можно добавить виртуальные машины в виртуальные сети. Дополнительные сведения о виртуальных машинах см. [здесь](https://docs.microsoft.com/azure/#pivot=services&panel=Compute).
* Сведения о BGP см. в статьях [Обзор использования BGP с VPN-шлюзами Azure](vpn-gateway-bgp-overview.md) и [Настройка BGP на VPN-шлюзах Azure с помощью Azure Resource Manager и PowerShell](vpn-gateway-bgp-resource-manager-ps.md).
* Сведения о создании VPN-подключения типа "сеть — сеть" с помощью шаблона Azure Resource Manager см. в разделе [Создание VPN-подключения типа "сеть — сеть"](https://azure.microsoft.com/resources/templates/101-site-to-site-vpn-create/).
* Сведения о создании VPN-подключения типа "виртуальная сеть — виртуальная сеть" с помощью шаблона Azure Resource Manager см. в разделе [Deploy HBase geo replication](https://azure.microsoft.com/resources/templates/101-hdinsight-hbase-replication-geo/) (Развертывание георепликации HBase).
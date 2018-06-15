---
title: включение файла
description: включение файла
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: include
ms.date: 03/22/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 7e33d4ed7100287ef6b22aa4c90fd52671363902
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/19/2018
ms.locfileid: "31613625"
---
Для выполнения этой задачи используйте виртуальную сеть со значениями, приведенными в справочном списке для настройки ниже. В этом списке также приведены дополнительные параметры и имена. Мы не используем этот список непосредственно ни на каком из шагов, несмотря на то, что добавляем переменные согласно значениям в этом списке. Вы можете скопировать список для справки, заменив значения собственными.

**Справочный список для конфигурации**

* Имя виртуальной сети: TestVNet.
* Адресное пространство виртуальной сети: 192.168.0.0/16.
* Группа ресурсов — TestRG.
* Имя подсети Subnet1: FrontEnd. 
* Адресное пространство Subnet1: 192.168.1.0/24.
* Имя подсети шлюза: GatewaySubnet; подсеть шлюза всегда необходимо называть *GatewaySubnet*.
* Адресное пространство шлюза подсети: 192.168.200.0/26.
* Расположение — East US.
* Имя шлюза: GW.
* IP-имя шлюза: GWIP.
* Имя конфигурации IP-адресов шлюза: gwipconf.
* Type = "ExpressRoute". Для настройки ExpressRoute требуется этот тип.
* Общедоступное IP-имя шлюза: gwpip.

## <a name="add-a-gateway"></a>Добавление шлюза
1. Подключитесь к своей подписке Azure.

  ```powershell 
  Connect-AzureRmAccount
  Get-AzureRmSubscription 
  Select-AzureRmSubscription -SubscriptionName "Name of subscription"
  ```
2. Объявите переменные для этого упражнения. Не забудьте изменить пример в соответствии с параметрами, которые вы хотите использовать.

  ```powershell 
  $RG = "TestRG"
  $Location = "East US"
  $GWName = "GW"
  $GWIPName = "GWIP"
  $GWIPconfName = "gwipconf"
  $VNetName = "TestVNet"
  ```
3. Сохраните объект виртуальной сети как переменную.

  ```powershell
  $vnet = Get-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $RG
  ```
4. Создайте подсеть шлюза своей виртуальной сети. Подсеть шлюза должна иметь имя GatewaySubnet. Потребуется создать шлюз с префиксом /27 или больше (26, 25 и т. д.).

  ```powershell
  Add-AzureRmVirtualNetworkSubnetConfig -Name GatewaySubnet -VirtualNetwork $vnet -AddressPrefix 192.168.200.0/26
  ```
5. Теперь нужно настроить конфигурацию.

  ```powershell
  $vnet = Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
  ```
6. Сохраните подсеть шлюза как переменную.

  ```powershell
  $subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
  ```
7. Запросите общедоступный IP-адрес. IP-адрес запрашивается перед созданием шлюза. Указать необходимый IP-адрес нельзя, он выделяется динамически. Этот IP-адрес будет использоваться на следующем этапе конфигурации. Параметр AllocationMethod должен иметь значение Dynamic.

  ```powershell
  $pip = New-AzureRmPublicIpAddress -Name $GWIPName  -ResourceGroupName $RG -Location $Location -AllocationMethod Dynamic
  ```
8. Создайте конфигурацию для шлюза. Конфигурация шлюза определяет используемые подсеть и общедоступный IP-адрес. На этом шаге вы задаете конфигурацию, которая будет использоваться при создании шлюза. Но пока объект шлюза не создается. Используйте следующий пример, чтобы создать конфигурацию шлюза.

  ```powershell
  $ipconf = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfName -Subnet $subnet -PublicIpAddress $pip
  ```
9. Создайте шлюз. На этом шаге особенно важен параметр **-GatewayType** . Необходимо использовать значение **ExpressRoute**. После выполнения этих командлетов на создание шлюза может потребоваться 45 минут или больше.

  ```powershell
  New-AzureRmVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG -Location $Location -IpConfigurations $ipconf -GatewayType Expressroute -GatewaySku Standard
  ```

## <a name="verify-the-gateway-was-created"></a>Проверка создания шлюза
Используйте следующую команду, чтобы проверить, был ли создан шлюз:

```powershell
Get-AzureRmVirtualNetworkGateway -ResourceGroupName $RG
```

## <a name="resize-a-gateway"></a>Изменение размера шлюза
Существует несколько [номеров SKU шлюзов](../articles/expressroute/expressroute-about-virtual-network-gateways.md). Чтобы изменить SKU шлюза в любое время, можно использовать следующую команду.

> [!IMPORTANT]
> Эта команда не работает для шлюза UltraPerformance. Чтобы заменить шлюз на UltraPerformance, сначала удалите существующий шлюз ExpressRoute, а затем создайте шлюз UltraPerformance. Чтобы заменить шлюз UltraPerformance обычным шлюзом, сначала удалите шлюз UltraPerformance, а затем создайте новый шлюз.
> 
> 

```powershell
$gw = Get-AzureRmVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG
Resize-AzureRmVirtualNetworkGateway -VirtualNetworkGateway $gw -GatewaySku HighPerformance
```

## <a name="remove-a-gateway"></a>Удаление шлюза
Чтобы удалить шлюз, используйте следующую команду:

```powershell
Remove-AzureRmVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG
```

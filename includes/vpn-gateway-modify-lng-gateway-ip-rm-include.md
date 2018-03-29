---
title: включение файла
description: включение файла
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: e4d6c23bc7bf9b3228f851ab38ec587bc8552455
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/23/2018
---
### <a name="gwipnoconnection"></a> Изменение шлюза локальной сети с использованием GatewayIpAddress при отсутствии подключения к шлюзу

Если общедоступный IP-адрес VPN-устройства, к которому вы хотите подключиться, изменился, измените шлюз локальной сети в соответствии с изменениями. Используйте пример ниже, чтобы изменить шлюз локальной сети, к которому нет подключения.

При изменении этого значения вы также можете изменить префиксы адресов. Не забудьте указать имеющееся имя шлюза локальной сети для перезаписи текущих параметров. Если используется другое имя, необходимо создать новый шлюз локальной сети вместо перезаписи существующего.

```powershell
New-AzureRmLocalNetworkGateway -Name MyLocalNetworkGWName `
-Location "West US" -AddressPrefix @('10.0.0.0/24','20.0.0.0/24','30.0.0.0/24') `
-GatewayIpAddress "5.4.3.2" -ResourceGroupName MyRGName
```

### <a name="gwipwithconnection"></a> Изменение шлюза локальной сети с использованием GatewayIpAddress, если подключение к шлюзу установлено

Если общедоступный IP-адрес VPN-устройства, к которому вы хотите подключиться, изменился, измените шлюз локальной сети в соответствии с изменениями. Если шлюз уже подключен, сначала вам нужно удалить подключение. Затем вы сможете изменить IP-адрес шлюза и создать новое подключение. При этом вы также можете изменить префиксы адресов. После этого VPN-подключение будет некоторое время недоступно. Не удаляйте VPN-шлюз при изменении IP-адреса шлюза. Необходимо удалить только подключение.
 

1. Удалите подключение. Вы можете найти имя своего подключения с помощью командлета Get-AzureRmVirtualNetworkGatewayConnection.

  ```powershell
  Remove-AzureRmVirtualNetworkGatewayConnection -Name MyGWConnectionName `
  -ResourceGroupName MyRGName
  ```
2. Измените значение GatewayIpAddress. При этом вы также можете изменить префиксы адресов. Не забудьте указать существующее имя шлюза локальной сети для перезаписи текущих параметров. Иначе вы создадите новый шлюз локальной сети, а не перезапишете существующий.

  ```powershell
  New-AzureRmLocalNetworkGateway -Name MyLocalNetworkGWName `
  -Location "West US" -AddressPrefix @('10.0.0.0/24','20.0.0.0/24','30.0.0.0/24') `
  -GatewayIpAddress "104.40.81.124" -ResourceGroupName MyRGName
  ```
3. Создайте подключение. В этом примере мы настраиваем тип подключения IPsec. При повторном создании подключения используйте тип соединения, указанный для вашей конфигурации. Дополнительные типы подключений см. на странице с [командлетами PowerShell](https://msdn.microsoft.com/library/mt603611.aspx).  Чтобы получить имя VirtualNetworkGateway, запустите командлет Get-AzureRmVirtualNetworkGateway.
   
    Задайте переменные.

  ```powershell
  $local = Get-AzureRMLocalNetworkGateway -Name MyLocalNetworkGWName -ResourceGroupName MyRGName `
  $vnetgw = Get-AzureRmVirtualNetworkGateway -Name RMGateway -ResourceGroupName MyRGName
  ```
   
    Создайте подключение.

  ```powershell 
  New-AzureRmVirtualNetworkGatewayConnection -Name MyGWConnectionName -ResourceGroupName MyRGName `
  -Location "West US" `
  -VirtualNetworkGateway1 $vnetgw `
  -LocalNetworkGateway2 $local `
  -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'
  ```
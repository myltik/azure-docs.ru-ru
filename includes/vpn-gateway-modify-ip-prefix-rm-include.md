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
ms.openlocfilehash: e81e083b012c4cc29f2d6c09f8254025d712a97c
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/23/2018
---
### <a name="noconnection"></a>Изменение префиксов IP-адресов для шлюза локальной сети при отсутствии подключения шлюза

Чтобы добавить дополнительные префиксы адресов:

```powershell
$local = Get-AzureRmLocalNetworkGateway -Name MyLocalNetworkGWName -ResourceGroupName MyRGName `
Set-AzureRmLocalNetworkGateway -LocalNetworkGateway $local `
-AddressPrefix @('10.0.0.0/24','20.0.0.0/24','30.0.0.0/24')
```

Чтобы удалить префиксы адресов, используйте фрагмент кода ниже.<br>
Не указывайте префиксы, которые больше не нужны. В этом примере нам больше не нужен префикс 20.0.0.0/24 (из предыдущего примера), поэтому мы обновим сетевой шлюз и исключим этот префикс.

```powershell
$local = Get-AzureRmLocalNetworkGateway -Name MyLocalNetworkGWName -ResourceGroupName MyRGName `
Set-AzureRmLocalNetworkGateway -LocalNetworkGateway $local `
-AddressPrefix @('10.0.0.0/24','30.0.0.0/24')
```

### <a name="withconnection"></a>Изменение префиксов IP-адресов для шлюза локальной сети при наличии подключения шлюза

Если шлюз уже подключен и вам нужно добавить или удалить префиксы IP-адресов, указанные для локального сетевого шлюза, выполните приведенные ниже шаги именно в такой последовательности. После этого VPN-подключение будет некоторое время недоступно. При изменении префиксов IP-адресов не нужно удалять VPN-шлюз. Необходимо удалить только подключение.


1. Удалите подключение.

  ```powershell
  Remove-AzureRmVirtualNetworkGatewayConnection -Name MyGWConnectionName -ResourceGroupName MyRGName
  ```
2. Измените префиксы адресов для локального сетевого шлюза.
   
  Задайте переменную для локального сетевого шлюза.

  ```powershell
  $local = Get-AzureRmLocalNetworkGateway -Name MyLocalNetworkGWName -ResourceGroupName MyRGName
  ```
   
  Измените префиксы.
   
  ```powershell
  Set-AzureRmLocalNetworkGateway -LocalNetworkGateway $local `
  -AddressPrefix @('10.0.0.0/24','20.0.0.0/24','30.0.0.0/24')
  ```
3. Создайте подключение. В этом примере мы настраиваем тип подключения IPsec. При повторном создании подключения используйте тип соединения, указанный для вашей конфигурации. Дополнительные типы подключений см. на странице с [командлетами PowerShell](https://msdn.microsoft.com/library/mt603611.aspx).
   
  Задайте переменную для шлюза виртуальной сети.

  ```powershell
  $gateway1 = Get-AzureRmVirtualNetworkGateway -Name RMGateway  -ResourceGroupName MyRGName
  ```
   
  Создайте подключение. В этом примере используется переменная $local, присвоенная на шаге 2.

  ```powershell
  New-AzureRmVirtualNetworkGatewayConnection -Name MyGWConnectionName `
  -ResourceGroupName MyRGName -Location 'West US' `
  -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local `
  -ConnectionType IPsec `
  -RoutingWeight 10 -SharedKey 'abc123'
  ```
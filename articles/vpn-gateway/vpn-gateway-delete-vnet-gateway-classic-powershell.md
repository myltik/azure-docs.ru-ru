---
title: Удаление шлюза виртуальной сети с помощью PowerShell (классическая модель Azure) | Документация Майкрософт
description: Удаление шлюза виртуальной сети с помощью PowerShell в классической модели развертывания.
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: vpn-gateway
ms.devlang: na
ms.topic: ''
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/11/2017
ms.author: cherylmc
ms.openlocfilehash: b1bc18307227a728e2bc8fd95e30fdc1cbdb8c59
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/21/2017
ms.locfileid: "24994500"
---
# <a name="delete-a-virtual-network-gateway-using-powershell-classic"></a>Удаление шлюза виртуальной сети с помощью PowerShell (классическая модель)
> [!div class="op_single_selector"]
> * [Resource Manager — портал Azure](vpn-gateway-delete-vnet-gateway-portal.md)
> * [Resource Manager — PowerShell](vpn-gateway-delete-vnet-gateway-powershell.md)
> * [Классическая модель: PowerShell](vpn-gateway-delete-vnet-gateway-classic-powershell.md)
>
>

В этой статье описано, как удалить VPN-шлюз в классической модели развертывания с помощью PowerShell. После удаления шлюза виртуальной сети измените файл конфигурации сети, чтобы удалить элементы, которые больше не используются.

##<a name="connect"></a>Шаг 1. Подключение к Azure

### <a name="1-install-the-latest-powershell-cmdlets"></a>1. Установите последнюю версию командлетов Azure PowerShell.

Скачайте и установите последнюю версию командлетов PowerShell для управления службами Azure. Подробнее: [Установка и настройка Azure PowerShell](/powershell/azure/overview).

### <a name="2-connect-to-your-azure-account"></a>2. Подключитесь к своей учетной записи Azure. 

Откройте консоль PowerShell с повышенными правами и подключитесь к своей учетной записи. Для подключения используйте следующий пример кода:

```powershell
Add-AzureAccount
```

## <a name="export"></a>Шаг 2. Экспорт и просмотр файла конфигурации сети

Создайте каталог на компьютере, а затем экспортируйте в него файл конфигурации сети. С помощью этого файла можно просмотреть сведения о текущей конфигурации, а также изменить конфигурацию сети.

В этом примере файл конфигурации сети экспортируется в каталог C:\AzureNet.

```powershell
Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
```

Откройте файл в текстовом редакторе и просмотрите имя для классической виртуальной сети. При создании виртуальной сети на портале Azure полное имя, которое используется Azure, не отображается на портале. Например, если виртуальной сеть отображается на портале Azure с именем ClassicVNet1, то в файле конфигурации сети она может иметь гораздо более длинное имя. Это имя может выглядеть примерно следующим образом: Group ClassicRG1 ClassicVNet1. Имена виртуальных сетей перечислены как **VirtualNetworkSite name =**. При выполнении командлетов PowerShell используйте имена из файла конфигурации сети.

## <a name="delete"></a>Шаг 3. Удаление шлюза виртуальной сети

При удалении шлюза виртуальной сети все подключения к виртуальной сети через шлюз будут отключены. Если к виртуальной сети подключены клиенты P2S, то они будут отключены без предупреждения.

В этом примере удаляется шлюз виртуальной сети. Обязательно используйте полное имя виртуальной сети из файла конфигурации сети.

```powershell
Remove-AzureVNetGateway -VNetName "Group ClassicRG1 ClassicVNet1"
```

При успешном выполнении возвращается следующий результат:

```
Status : Successful
```

## <a name="modify"></a>Шаг 4. Изменение файла конфигурации сети

При удалении шлюза виртуальной сети командлет не изменяет файл конфигурации сети. Необходимо изменить файл, чтобы удалить элементы, которые больше не используются. В следующих разделах описывается, как изменить скачанный файл конфигурации сети.

### <a name="lnsref"></a>Ссылки на сайты локальной сети

Чтобы удалить данные ссылок на сайты, внесите изменения в элемент **ConnectionsToLocalNetwork/LocalNetworkSiteRef**. Удаление ссылки на локальный сайт Azure инициирует удаление туннеля. В зависимости от созданной конфигурации элемент **LocalNetworkSiteRef** может отсутствовать.

```
<Gateway>
   <ConnectionsToLocalNetwork>
     <LocalNetworkSiteRef name="D1BFC9CB_Site2">
       <Connection type="IPsec" />
     </LocalNetworkSiteRef>
   </ConnectionsToLocalNetwork>
 </Gateway>
```

Пример:

```
<Gateway>
   <ConnectionsToLocalNetwork>
   </ConnectionsToLocalNetwork>
 </Gateway>
```

###<a name="lns"></a>Сайты локальной сети

Удалите все локальные сайты, которые больше не используются. В зависимости от созданной конфигурации элемент **LocalNetworkSite** может отсутствовать.

```
<LocalNetworkSites>
  <LocalNetworkSite name="Site1">
    <AddressSpace>
      <AddressPrefix>192.168.0.0/16</AddressPrefix>
    </AddressSpace>
    <VPNGatewayAddress>5.4.3.2</VPNGatewayAddress>
  </LocalNetworkSite>
  <LocalNetworkSite name="Site3">
    <AddressSpace>
      <AddressPrefix>192.168.0.0/16</AddressPrefix>
    </AddressSpace>
    <VPNGatewayAddress>57.179.18.164</VPNGatewayAddress>
  </LocalNetworkSite>
 </LocalNetworkSites>
```

В этом примере мы удалили только Site3.

```
<LocalNetworkSites>
  <LocalNetworkSite name="Site1">
    <AddressSpace>
      <AddressPrefix>192.168.0.0/16</AddressPrefix>
    </AddressSpace>
    <VPNGatewayAddress>5.4.3.2</VPNGatewayAddress>
  </LocalNetworkSite>
 </LocalNetworkSites>
```

### <a name="clientaddresss"></a>Пул адресов клиента

Если к виртуальной сети установлено подключение типа "точка — сеть", то должен быть элемент **VPNClientAddressPool**. Удалите пулы адресов клиента, которые соответствуют удаленному шлюзу виртуальной сети.

```
<Gateway>
    <VPNClientAddressPool>
      <AddressPrefix>10.1.0.0/24</AddressPrefix>
    </VPNClientAddressPool>
  <ConnectionsToLocalNetwork />
 </Gateway>
```

Пример:

```
<Gateway>
  <ConnectionsToLocalNetwork />
 </Gateway>
```

### <a name="gwsub"></a>Подсеть шлюза

Удалите подсеть шлюза (элемент **GatewaySubnet**), которая соответствует виртуальной сети.

```
<Subnets>
   <Subnet name="FrontEnd">
     <AddressPrefix>10.11.0.0/24</AddressPrefix>
   </Subnet>
   <Subnet name="GatewaySubnet">
     <AddressPrefix>10.11.1.0/29</AddressPrefix>
   </Subnet>
 </Subnets>
```

Пример:

```
<Subnets>
   <Subnet name="FrontEnd">
     <AddressPrefix>10.11.0.0/24</AddressPrefix>
   </Subnet>
 </Subnets>
```

## <a name="upload"></a>Шаг 5. Передача файла конфигурации сети

Сохраните изменения и передайте файл конфигурации сети в Azure. Обязательно измените путь к файлу в соответствии с вашей средой.

```powershell
Set-AzureVNetConfig -ConfigurationPath C:\AzureNet\NetworkConfig.xml
```

При успешном выполнении возвращается примерно следующий результат:

```
OperationDescription        OperationId                      OperationStatus                                                
--------------------        -----------                      ---------------                                           
Set-AzureVNetConfig         e0ee6e66-9167-cfa7-a746-7casb9   Succeeded
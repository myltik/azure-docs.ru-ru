---
title: Подключение классических виртуальных сетей к виртуальным сетям Resource Manager с помощью PowerShell | Документация Майкрософт
description: Создание VPN-подключения между классическими виртуальными сетями и виртуальными сетями Resource Manager с помощью VPN-шлюза и PowerShell.
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: jpconnock
editor: ''
tags: azure-service-management,azure-resource-manager
ms.assetid: f17c3bf0-5cc9-4629-9928-1b72d0c9340b
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/13/2018
ms.author: cherylmc
ms.openlocfilehash: 65faf1a4f78244d9fdd03b6415bf2cadac923504
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/19/2018
ms.locfileid: "31603697"
---
# <a name="connect-virtual-networks-from-different-deployment-models-using-powershell"></a>Подключение виртуальных сетей из различных моделей развертывания с использованием PowerShell

Эта статья поможет подключить классические виртуальные сети к виртуальным сетям Resource Manager, чтобы ресурсы, находящиеся в разных моделях развертывания, могли взаимодействовать между собой. В описанных здесь действиях используется PowerShell, однако эту конфигурацию можно создать и с помощью портала Azure, выбрав соответствующую статью в списке.

> [!div class="op_single_selector"]
> * [Портал](vpn-gateway-connect-different-deployment-models-portal.md)
> * [PowerShell](vpn-gateway-connect-different-deployment-models-powershell.md)
> 
> 

Подключение к классической виртуальной сети к виртуальной сети Resource Manager похоже на подключение виртуальной сети к локальному сайту. В обоих типах подключений используется VPN-шлюз для создания защищенного туннеля, использующего IPsec/IKE. Подключение можно создать между виртуальными сетями, которые находятся в разных подписках и разных регионах. Вы также можете создать подключение между виртуальными сетями, которые уже подключены к локальным сетям. В этом случае для них должен быть настроен динамический шлюз или шлюз на основе маршрутов. Дополнительные сведения о подключениях типа "виртуальная сеть — виртуальная сеть" см. в разделе [Часто задаваемые вопросы о подключениях типа "виртуальная сеть — виртуальная сеть"](#faq) в конце этой статьи. 

Если у вас нет шлюза виртуальной сети и вы не хотите его создавать, то вместо этого можно подключить виртуальные сети с помощью пиринга виртуальных сетей. При пиринговой связи между виртуальными сетями VPN-шлюз не используется. Дополнительную информацию см. в статье [Пиринговая связь между виртуальными сетями](../virtual-network/virtual-network-peering-overview.md).

## <a name="before"></a>Перед началом работы

Далее приведены инструкции, которые помогут вам настроить динамический шлюз или шлюз на основе маршрутов для каждой виртуальной сети и создать VPN-подключение между шлюзами. Эта конфигурация не поддерживает статические шлюзы и шлюзы на основе политик.

### <a name="pre"></a>Предварительные требования

* Обе виртуальные сети уже созданы.
* Диапазоны адресов для виртуальных сетей не перекрываются между собой или с другими диапазонами подключений, к которым могут быть подключены шлюзы.
* В системе установлена последняя версия командлетов Azure PowerShell. Дополнительные сведения см. в статье [Как установить и настроить Azure PowerShell](/powershell/azure/overview). Командлеты управления службой и Resource Manager должны быть установлены. 

### <a name="exampleref"></a>Примеры настроек

Эти значения можно использовать для создания тестовой среды или для лучшего понимания примеров в этой статье.

**Параметры классической виртуальной сети**

Имя виртуальной сети = ClassicVNet <br>
Расположение: Западная часть США <br>
Адресные пространства виртуальной сети = 10.0.0.0/24 <br>
Подсеть-1 = 10.0.0.0/27 <br>
Шлюз подсети = 10.0.0.32/29 <br>
Имя локальной сети = RMVNetLocal <br>
Тип шлюза = DynamicRouting

**Параметры виртуальной сети Resource Manager**

Имя виртуальной сети = RMVNet <br>
Группа ресурсов = RG1 <br>
Адресные пространства виртуальной сети = 192.168.0.0/16 <br>
Подсеть-1 = 192.168.1.0/24 <br>
Шлюз подсети = 192.168.0.0/26 <br>
Расположение = восточная часть США <br>
Общедоступное IP-имя шлюза: gwpip <br>
Шлюз локальной сети = ClassicVNetLocal <br>
Имя шлюза виртуальной сети = RMGateway <br>
Конфигурация IP-адресации шлюза = gwipconfig

## <a name="createsmgw"></a>Раздел 1. Настройка классической виртуальной сети
### <a name="1-download-your-network-configuration-file"></a>1. Скачивание файла конфигурации сети
1. Запустите консоль PowerShell с повышенными правами и войдите в свою учетную запись Azure. Следующий командлет запрашивает учетные данные входа для вашей учетной записи Azure. После выполнения входа он скачивает параметры учетной записи, чтобы они были доступны в Azure PowerShell. В этом разделе используются классические командлеты Azure PowerShell для управления службами.

  ```powershell
  Add-AzureAccount
  ```

  Получите подписку Azure.

  ```powershell
  Get-AzureSubscription
  ```

  При наличии нескольких подписок выберите подписку, которую вы хотите использовать.

  ```powershell
  Select-AzureSubscription -SubscriptionName "Name of subscription"
  ```
2. Экспортируйте файл конфигурации сети Azure, выполнив приведенную ниже команду. При необходимости можно изменить расположение файла для экспорта.

  ```powershell
  Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
  ```
3. Откройте скачанный XML-файл, чтобы внести изменения. Пример файла конфигурации сети см. в [схеме конфигурации сети](https://msdn.microsoft.com/library/jj157100.aspx).

### <a name="2-verify-the-gateway-subnet"></a>2. Проверка подсети шлюза
В элементе **VirtualNetworkSites** добавьте подсеть шлюза в виртуальную сеть, если она еще не создана. При работе с файлом конфигурации сети для подсети шлюза необходимо задать имя GatewaySubnet. В противном случае Azure не удастся распознать и использовать ее в качестве подсети шлюза.

[!INCLUDE [vpn-gateway-no-nsg-include](../../includes/vpn-gateway-no-nsg-include.md)]

**Пример.**

    <VirtualNetworkSites>
      <VirtualNetworkSite name="ClassicVNet" Location="West US">
        <AddressSpace>
          <AddressPrefix>10.0.0.0/24</AddressPrefix>
        </AddressSpace>
        <Subnets>
          <Subnet name="Subnet-1">
            <AddressPrefix>10.0.0.0/27</AddressPrefix>
          </Subnet>
          <Subnet name="GatewaySubnet">
            <AddressPrefix>10.0.0.32/29</AddressPrefix>
          </Subnet>
        </Subnets>
      </VirtualNetworkSite>
    </VirtualNetworkSites>

### <a name="3-add-the-local-network-site"></a>3. Добавление сайта локальной сети
Добавляемый сайт локальной сети представляет собой виртуальную сеть RM, к которой необходимо подключиться. Добавьте в файл элемент **LocalNetworkSites**, если его еще нет. На этом этапе настройки значение VPNGatewayAddress может быть любым допустимым общедоступным IP-адресом, так как шлюз для виртуальной сети RM еще не создан. После создания шлюза этот заполнитель необходимо заменить правильным общедоступным IP-адресом, назначенным шлюзу RM.

    <LocalNetworkSites>
      <LocalNetworkSite name="RMVNetLocal">
        <AddressSpace>
          <AddressPrefix>192.168.0.0/16</AddressPrefix>
        </AddressSpace>
        <VPNGatewayAddress>13.68.210.16</VPNGatewayAddress>
      </LocalNetworkSite>
    </LocalNetworkSites>

### <a name="4-associate-the-vnet-with-the-local-network-site"></a>4. Связывание виртуальной сети с сайтом локальной сети
В этом разделе необходимо указать сайт локальной сети, который требуется подключить к виртуальной сети. В нашем случае это виртуальная сеть RM, указанная ранее. Убедитесь, что имена совпадают. На этом этапе шлюз еще не создается. Пока только указывается локальная сеть, к которой будет подключен шлюз.

        <Gateway>
          <ConnectionsToLocalNetwork>
            <LocalNetworkSiteRef name="RMVNetLocal">
              <Connection type="IPsec" />
            </LocalNetworkSiteRef>
          </ConnectionsToLocalNetwork>
        </Gateway>

### <a name="5-save-the-file-and-upload"></a>5. Сохранение и передача файла
Сохраните файл, а затем импортируйте его в Azure, выполнив приведенную ниже команду. Обязательно измените путь к файлу в соответствии с вашей средой.

```powershell
Set-AzureVNetConfig -ConfigurationPath C:\AzureNet\NetworkConfig.xml
```

Вы увидите аналогичный результат, свидетельствующий о том, что импорт выполнен успешно.

        OperationDescription        OperationId                      OperationStatus                                                
        --------------------        -----------                      ---------------                                                
        Set-AzureVNetConfig        e0ee6e66-9167-cfa7-a746-7casb9    Succeeded 

### <a name="6-create-the-gateway"></a>6. Создание шлюза

Перед запуском этого примера ознакомьтесь с файлом конфигурации сети, который вы скачали, чтобы узнать, какие именно имена ожидает Azure. Файл конфигурации сети содержит значения для классических виртуальных сетей. Иногда при создании параметров классической виртуальной сети на портале Azure имена классических виртуальных сетей в файле конфигурации сети изменяются из-за различий в моделях развертывания. Например, если на портале Azure вы создали классическую виртуальную сеть Classic VNet в группе ресурсов ClassicRG, то ее имя, сохраняемое в файле конфигурации сети, преобразуется в Group ClassicRG Classic VNet. Если указывается имя виртуальной сети с пробелами, заключите его в кавычки.


Чтобы создать шлюз для динамической маршрутизации, используйте следующий пример.

```powershell
New-AzureVNetGateway -VNetName ClassicVNet -GatewayType DynamicRouting
```

Проверить состояние шлюза можно с помощью командлета **Get-AzureVNetGateway**.

## <a name="creatermgw"></a>Раздел 2. Настройка шлюза виртуальной сети RM
Чтобы создать VPN-шлюз для виртуальной сети RM, следуйте приведенным ниже инструкциям. Не приступайте к выполнению следующих действий до получения общедоступного IP-адреса шлюза классической виртуальной сети. 

1. В консоли PowerShell войдите в свою учетную запись Azure. Следующий командлет запрашивает учетные данные входа для вашей учетной записи Azure. После выполнения входа он скачивает параметры учетной записи, чтобы они были доступны в Azure PowerShell.

  ```powershell
  Connect-AzureRmAccount
  ``` 
   
  Получите список подписок Azure.

  ```powershell
  Get-AzureRmSubscription
  ```
   
  При наличии нескольких подписок укажите подписку, которую вы хотите использовать.

  ```powershell
  Select-AzureRmSubscription -SubscriptionName "Name of subscription"
  ```
2. Создайте локальный сетевой шлюз. В виртуальной сети термин "шлюз локальной сети" обычно означает локальное расположение. В этом случае термин "шлюз локальной сети" указывает на классическую виртуальную сеть. Присвойте этому расположению имя, с помощью которого служба Azure сможет ссылаться на него, а также укажите префикс адресного пространства. С помощью указанного префикса IP-адреса служба Azure может определить, какой трафик отправлять в локальное расположение. Если позже понадобится изменить эти сведения, перед созданием шлюза можно изменить значения и снова запустить пример.
   
   **-Name** — это имя, которое будет использоваться для ссылки на шлюз локальной сети.<br>
   **-AddressPrefix** — это диапазон адресов для классической виртуальной сети.<br>
   **-GatewayIpAddress** — это общедоступный IP-адрес шлюза классической виртуальной сети. Укажите в следующем примере правильный IP-адрес.<br>

  ```powershell
  New-AzureRmLocalNetworkGateway -Name ClassicVNetLocal `
  -Location "West US" -AddressPrefix "10.0.0.0/24" `
  -GatewayIpAddress "n.n.n.n" -ResourceGroupName RG1
  ```
3. Запросите общедоступный IP-адрес, который нужно выделить для шлюза виртуальной сети Resource Manager. Указать необходимый вам IP-адрес нельзя. IP-адрес выделяется для шлюза виртуальной сети динамически. Но это не значит, что IP-адрес изменится. IP-адрес шлюза виртуальной сети изменяется только после его удаления и повторного создания. При изменении размера, сбросе или других внутренних операциях обслуживания или обновления IP-адрес шлюза не изменяется.

  На этом этапе также задается переменная, которая будет использоваться позже.

  ```powershell
  $ipaddress = New-AzureRmPublicIpAddress -Name gwpip `
  -ResourceGroupName RG1 -Location 'EastUS' `
  -AllocationMethod Dynamic
  ```

4. Проверьте наличие подсети шлюза в виртуальной сети. Если подсети шлюза нет, добавьте ее. Присвойте подсети шлюза имя *GatewaySubnet*.
5. Получите подсеть, используемую для шлюза, выполнив приведенную ниже команду. На этом этапе мы также зададим переменную, которая будет использоваться на следующем этапе.
   
   **-Name** — это имя виртуальной сети Resource Manager.<br>
   **-ResourceGroupName** — это группа ресурсов, с которой связана виртуальная сеть. Подсеть шлюза для этой виртуальной сети уже должна существовать и иметь имя *GatewaySubnet* .<br>

  ```powershell
  $subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name GatewaySubnet `
  -VirtualNetwork (Get-AzureRmVirtualNetwork -Name RMVNet -ResourceGroupName RG1)
  ``` 

6. Создайте конфигурацию IP-адресации шлюза. Конфигурация шлюза определяет используемые подсеть и общедоступный IP-адрес. Используйте следующий пример, чтобы создать конфигурацию шлюза.

  В параметры **-SubnetId** и **-PublicIpAddressId** должны передаваться объекты свойства идентификатора из подсети и IP-адреса соответственно. Нельзя использовать простую строку. Эти переменные задаются на этапе запроса общедоступного IP-адреса и этапе получения подсети.

  ```powershell
  $gwipconfig = New-AzureRmVirtualNetworkGatewayIpConfig `
  -Name gwipconfig -SubnetId $subnet.id `
  -PublicIpAddressId $ipaddress.id
  ```
7. Создайте шлюз виртуальной сети Resource Manager, выполнив приведенную ниже команду. `-VpnType` должен иметь значение *RouteBased*. Для создания шлюза потребуется 45 минут или больше.

  ```powershell
  New-AzureRmVirtualNetworkGateway -Name RMGateway -ResourceGroupName RG1 `
  -Location "EastUS" -GatewaySKU Standard -GatewayType Vpn `
  -IpConfigurations $gwipconfig `
  -EnableBgp $false -VpnType RouteBased
  ```
8. После создания VPN-шлюза скопируйте общедоступный IP-адрес. Он понадобится при настройке параметров локальной сети для классической виртуальной сети. Для получения общедоступного IP-адреса можно использовать следующий командлет. В возврате должен быть указан общедоступный IP-адрес *IpAddress*.

  ```powershell
  Get-AzureRmPublicIpAddress -Name gwpip -ResourceGroupName RG1
  ```

## <a name="localsite"></a>Раздел 3. Изменение параметров локального сайта виртуальной сети

В этом разделе вы будете иметь дело с классической виртуальной сетью. Вы замените заполнитель IP-адреса, указанный при настройке параметров локального сайта, который будет использоваться для подключения к шлюзу виртуальной сети Resource Manager. 

1. Экспортируйте файл конфигурации сети.

  ```powershell
  Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
  ```
2. С помощью текстового редактора измените значение VPNGatewayAddress. Замените заполнитель IP-адреса общедоступным IP-адресом шлюза Resource Manager, затем сохраните изменения.

  ```
  <VPNGatewayAddress>13.68.210.16</VPNGatewayAddress>
  ```
3. Импортируйте измененный файл конфигурации сети в Azure.

  ```powershell
  Set-AzureVNetConfig -ConfigurationPath C:\AzureNet\NetworkConfig.xml
  ```

## <a name="connect"></a>Раздел 4. Создание подключения между шлюзами
Для создания подключения между шлюзами требуется PowerShell. Возможно, потребуется добавить учетную запись Azure, чтобы использовать классическую версию командлетов PowerShell. Чтобы сделать это, используйте командлет **Add-AzureAccount**.

1. Задайте общий ключ в консоли PowerShell. Перед выполнением командлетов ознакомьтесь с файлом конфигурации сети, который вы скачали, чтобы узнать, какие именно имена ожидает Azure. Если указывается имя виртуальной сети с пробелами, заключите его в одиночные кавычки.<br><br>В приведенном ниже примере **-VNetName** — это имя классической виртуальной сети, а **-LocalNetworkSiteName** — имя, определенное для сайта локальной сети. **-SharedKey** — это значение, которое следует создать и задать. В примере мы использовали abc123, но вы можете создать и использовать что-нибудь посложнее. Важно отметить, что заданное здесь значение должно совпадать со значением, которое вы укажете на следующем шаге при создании подключения. В результате выполнения должно отобразиться состояние **Успешно**.

  ```powershell
  Set-AzureVNetGatewayKey -VNetName ClassicVNet `
  -LocalNetworkSiteName RMVNetLocal -SharedKey abc123
  ```
2. Создайте VPN-подключение, выполнив следующие команды.
   
  Задайте переменные.

  ```powershell
  $vnet01gateway = Get-AzureRMLocalNetworkGateway -Name ClassicVNetLocal -ResourceGroupName RG1
  $vnet02gateway = Get-AzureRmVirtualNetworkGateway -Name RMGateway -ResourceGroupName RG1
  ```
   
  Создайте подключение. Обратите внимание, что для параметра **-ConnectionType** используется значение IPsec, а не Vnet2Vnet.

  ```powershell
  New-AzureRmVirtualNetworkGatewayConnection -Name RM-Classic -ResourceGroupName RG1 `
  -Location "East US" -VirtualNetworkGateway1 `
  $vnet02gateway -LocalNetworkGateway2 `
  $vnet01gateway -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'
  ```

## <a name="verify"></a>Раздел 5. Проверка подключений

### <a name="to-verify-the-connection-from-your-classic-vnet-to-your-resource-manager-vnet"></a>Проверка подключения классической виртуальной сети к виртуальной сети Resource Manager

#### <a name="powershell"></a>PowerShell

[!INCLUDE [vpn-gateway-verify-connection-ps-classic](../../includes/vpn-gateway-verify-connection-ps-classic-include.md)]

#### <a name="azure-portal"></a>Портал Azure

[!INCLUDE [vpn-gateway-verify-connection-azureportal-classic](../../includes/vpn-gateway-verify-connection-azureportal-classic-include.md)]


### <a name="to-verify-the-connection-from-your-resource-manager-vnet-to-your-classic-vnet"></a>Проверка подключения виртуальной сети Resource Manager к классической виртуальной сети

#### <a name="powershell"></a>PowerShell

[!INCLUDE [vpn-gateway-verify-ps-rm](../../includes/vpn-gateway-verify-connection-ps-rm-include.md)]

#### <a name="azure-portal"></a>Портал Azure

[!INCLUDE [vpn-gateway-verify-connection-portal-rm](../../includes/vpn-gateway-verify-connection-portal-rm-include.md)]

## <a name="faq"></a>Часто задаваемые вопросы о подключениях типа "виртуальная сеть — виртуальная сеть"

[!INCLUDE [vpn-gateway-vnet-vnet-faq](../../includes/vpn-gateway-faq-vnet-vnet-include.md)]

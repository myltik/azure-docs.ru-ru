---
title: "Настройка VPN сайтами через Microsoft пиринг для Azure ExpressRoute | Документы Microsoft"
description: "Настройте подключение к Azure через канал пиринга ExpressRoute Microsoft с помощью VPN-шлюза сайт сайт для IPsec/IKE."
documentationcenter: na
services: expressroute
author: cherylmc
manager: timlt
editor: 
ms.assetid: 
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/06/2017
ms.author: cherylmc
ms.openlocfilehash: 64203e2cbac1206224f0e0ad8b7d364f19ad0332
ms.sourcegitcommit: 4ac89872f4c86c612a71eb7ec30b755e7df89722
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/07/2017
---
# <a name="configure-a-site-to-site-vpn-over-expressroute-microsoft-peering"></a>Настройка VPN сайтами через пиринг ExpressRoute Майкрософт

Эта статья поможет вам настроить безопасное зашифрованное подключение между локальной сетью и Azure виртуальных сетей (Vnet) через подключение ExpressRoute закрытый. Настройка безопасный туннель через ExpressRoute обеспечивает обмен данными с конфиденциальностью, повторов, подлинность и целостность.

## <a name="architecture"></a>Архитектура

Вы можете использовать Microsoft пиринг для создания туннеля VPN IPsec/IKE сеть сеть между выбранной локальными сетями и виртуальными сетями Azure.

  ![Общие сведения о подключениях](./media/site-to-site-vpn-over-microsoft-peering/IPsecER_Overview.png)

>[!NOTE]
>При настройке через виртуальную Частную сеть для по Майкрософт пиринг, взимается плата для шлюза VPN и исходящий трафик VPN. Дополнительные сведения см. в разделе [цены VPN-шлюз](https://azure.microsoft.com/pricing/details/vpn-gateway).
>
>

Для высокой доступности и избыточности можно настроить несколько туннелей через канал ExpressRoute на две пары MSEE PE и включить балансировку нагрузки между туннели.

  ![Варианты обеспечения высокой доступности](./media/site-to-site-vpn-over-microsoft-peering/HighAvailability.png)

VPN-туннели через пиринг Майкрософт может быть прекращена с помощью VPN-шлюза, или с помощью соответствующих сети виртуального устройства Уязвимости сети доступны через Azure Marketplace. Вы можете менять маршруты статически или динамически зашифрованные туннелях без предоставления exchange маршрута для базового пиринг Майкрософт. В примерах в этой статье BGP (отличающиеся от сеанса BGP, используемый для создания пиринг Майкрософт) используется для динамического обмена префиксы зашифрованные туннелях.

>[!IMPORTANT]
>На локальной стороне обычно пиринг Майкрософт прекращается в сети Периметра и частного пиринга завершается в зоне основной сети. Две зоны будут выделены с помощью брандмауэров. При настройке Microsoft пиринг исключительно для включение безопасного туннелирования через ExpressRoute, не забудьте фильтрации только общедоступные IP-адреса интерес, начало объявления через пиринг Майкрософт.
>
>

## <a name="workflow"></a>Рабочий процесс

1. Настройка Microsoft пиринг для своего канала ExpressRoute.
2. Рекламировать выбранного Azure региональные открытых префиксов к локальной сети через пиринг Майкрософт.
3. Настройка VPN-шлюза и установить IPsec-туннели
4. Настройка локального устройства VPN.
5. Создание соединения IPsec/IKE сайт сайт.
6. (Необязательно) Настройте брандмауэры и фильтрации на локальное устройство VPN.
7. Тестирование и проверка связи IPsec через канал ExpressRoute.

## <a name="peering"></a>1. настроить пиринг Microsoft

Чтобы настроить сеть сеть VPN-подключения через ExpressRoute, необходимо использовать пиринг ExpressRoute Майкрософт.

* Чтобы настроить новый канал ExpressRoute, начните с [необходимые компоненты ExpressRoute](expressroute-prerequisites.md) статьи, а затем [создать и изменить канал ExpressRoute](expressroute-howto-circuit-arm.md).

* Если вы уже есть канал ExpressRoute, но не имеют настроить пиринг Майкрософт, настроить пиринг Майкрософт с помощью [создания и изменения пиринга за канал ExpressRoute](expressroute-howto-routing-arm.md#msft) статьи.

После настройки канала и пиринг Майкрософт, можно легко просмотреть ее с помощью **Обзор** на портале Azure.

![схемы](./media/site-to-site-vpn-over-microsoft-peering/ExpressRouteCkt.png)

## <a name="routefilter"></a>2. Настройка фильтров маршрута

Фильтр маршрутов позволяет вам идентифицировать службы, которые можно использовать через пиринг Майкрософт по каналу ExpressRoute. Это по сути белый список всех значений BGP сообщества. 

![фильтр маршрутов](./media/site-to-site-vpn-over-microsoft-peering/route-filter.png)

В этом примере развертывания присутствует только в *Azure Западная часть США 2* области. Правило фильтра маршрута добавляется только объявления Azure Западная часть США 2 региональные префиксы, со значением сообщества BGP *12076:51026*. Укажите региональные префиксов, которые вы хотите разрешить, выбрав **управление правило**.

В фильтре маршрута необходимо также выбрать каналы ExpressRoute, к которому применяется фильтр маршрутов. Вы можете каналы ExpressRoute, выбрав **добавить схемы**. На предыдущем рисунке фильтра маршрутов связан в примере канал ExpressRoute.

### <a name="configfilter"></a>2.1. Настройка фильтра маршрутов

Настройки фильтра маршрута. Пошаговые инструкции см. в разделе [Настройка фильтров маршрута для пиринга Майкрософт](how-to-routefilter-portal.md).

### <a name="verifybgp"></a>2.2 Проверка маршрутов BGP

После успешного создания Microsoft пиринга по ваш канал ExpressRoute и связанный фильтр маршрутов с канала, можно проверить, маршруты BGP, полученных от MSEEs на устройствах PE, пиринг с MSEEs. Команды проверки зависит от операционной системы устройства PE.

#### <a name="cisco-examples"></a>Примеры Cisco

В этом примере используется команда Cisco IOS-XE. В примере виртуального маршрутизации и пересылки экземпляр (VRF) используется для изолирования трафика пиринга.

```
show ip bgp vpnv4 vrf 10 summary
```

Следующие частичного выходные данные показывают, что префиксы 68 были получены от соседнего узла *.243.229.34 с 12076 ASN (MSEE):

```
...

Neighbor        V           AS MsgRcvd MsgSent   TblVer  InQ OutQ Up/Down  State/PfxRcd
X.243.229.34    4        12076   17671   17650    25228    0    0 1w4d           68
```

Чтобы просмотреть список префиксов, полученных от соседнего узла, используйте следующий пример:

```
sh ip bgp vpnv4 vrf 10 neighbors X.243.229.34 received-routes
```

Для подтверждения, что вы получили правильный набор префиксов, вы можете кросс проверить. Следующие выходные данные команды Azure PowerShell перечень префиксов, объявленных через пиринг для каждой из служб и для каждого региона Azure корпорации Майкрософт:

```powershell
Get-AzureRmBgpServiceCommunity
```

## <a name="vpngateway"></a>3. Настройка шлюза VPN и туннелей IPsec

В этом разделе VPN-туннели IPsec создаются между шлюзом Azure VPN и локальным устройством VPN. В примерах используется Cisco облачной службы маршрутизатора (CSR1000) VPN-устройства.

На следующей диаграмме показан IPsec VPN туннелей между локальным устройством VPN 1 и пары экземпляр шлюза Azure VPN. Установить два туннеля IPsec VPN между локальным устройством VPN 2 пары экземпляр шлюза Azure VPN не показан на схеме и не указываются сведения о конфигурации. Однако наличие дополнительных VPN-туннели способствует улучшению высокого уровня доступности.

  ![VPN-туннели](./media/site-to-site-vpn-over-microsoft-peering/EstablishTunnels.png)

Через пару туннель IPsec сеанс eBGP устанавливается для обмена маршрутами частной сети. На следующей диаграмме показан eBGP сеанс, осуществляемый через пару туннель IPsec:

  ![сеансы eBGP через туннель пары](./media/site-to-site-vpn-over-microsoft-peering/TunnelBGP.png)

На следующей диаграмме показаны абстрактные Общие сведения о примере сети:

  ![Пример сети](./media/site-to-site-vpn-over-microsoft-peering/OverviewRef.png)

### <a name="about-the-azure-resource-manager-template-examples"></a>Информация о примерах шаблона диспетчера ресурсов Azure

В примерах шлюза VPN и завершения туннель IPsec настраиваются с помощью шаблона диспетчера ресурсов Azure. Если вы не знакомы с помощью шаблонов диспетчера ресурсов или понять принципы шаблона диспетчера ресурсов, см. [понять структуру и синтаксис шаблоны Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md). Этот шаблон в этом разделе создает Гринфилд среды Azure (VNet). Однако при наличии существующей виртуальной сети, он может ссылаться и в шаблоне. Если вы не знакомы с конфигурациями сеть сеть IPsec/IKE шлюза VPN, см. раздел [создания подключения сеть сеть](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md).

>[!NOTE]
>Необходимо использовать шаблоны Azure Resource Manager для создания такой конфигурации. Можно создать конфигурацию с помощью портала Azure или PowerShell.
>
>

### <a name="variables3"></a>3.1 объявление переменных

В этом примере объявления переменных соответствуют примере сети. При объявлении переменных, измените этот раздел, в соответствии с вашей средой.

* Переменная **localAddressPrefix** представляет собой массив значений локальных IP-адресов для завершения туннели IPsec.
* **GatewaySku** определяет пропускную способность VPN. Дополнительные сведения о gatewaySku и vpnType см. в разделе [параметры конфигурации VPN-шлюз](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md#gwsku). О ценах см. в разделе [цены VPN-шлюз](https://azure.microsoft.com/pricing/details/vpn-gateway).
* Задать **vpnType** для **routebased**.

```json
"variables": {
  "virtualNetworkName": "SecureVNet",       // Name of the Azure VNet
  "azureVNetAddressPrefix": "10.2.0.0/24",  // Address space assigned to the VNet
  "subnetName": "Tenant",                   // subnet name in which tenants exists
  "subnetPrefix": "10.2.0.0/25",            // address space of the tenant subnet
  "gatewaySubnetPrefix": "10.2.0.224/27",   // address space of the gateway subnet
  "localGatewayName": "localGW1",           // name of remote gateway (on-premises)
  "localGatewayIpAddress": "X.243.229.110", // public IP address of the on-premises VPN device
  "localAddressPrefix": [
    "172.16.0.1/32",                        // termination of IPsec tunnel-1 on-premises 
    "172.16.0.2/32"                         // termination of IPsec tunnel-2 on-premises 
  ],
  "gatewayPublicIPName1": "vpnGwVIP1",    // Public address name of the first VPN gateway instance
  "gatewayPublicIPName2": "vpnGwVIP2",    // Public address name of the second VPN gateway instance 
  "gatewayName": "vpnGw",                 // Name of the Azure VPN gateway
  "gatewaySku": "VpnGw1",                 // Azure VPN gateway SKU
  "vpnType": "RouteBased",                // type of VPN gateway
  "sharedKey": "string",                  // shared secret needs to match with on-premise configuration
  "asnVpnGateway": 65000,                 // BGP Autonomous System number assigned to the VPN Gateway 
  "asnRemote": 65010,                     // BGP Autonmous Syste number assigned to the on-premises device
  "bgpPeeringAddress": "172.16.0.3",      // IP address of the remote BGP peer on-premises
  "connectionName": "vpn2local1",
  "vnetID": "[resourceId('Microsoft.Network/virtualNetworks', variables('virtualNetworkName'))]",
  "gatewaySubnetRef": "[concat(variables('vnetID'),'/subnets/','GatewaySubnet')]",
  "subnetRef": "[concat(variables('vnetID'),'/subnets/',variables('subnetName'))]",
  "api-version": "2017-06-01"
},
```

### <a name="vnet"></a>3.2 Создание виртуальной сети (VNet)

Если VPN-туннели объединяемых существующей виртуальной сети, этот шаг можно пропустить.

```json
{
  "apiVersion": "[variables('api-version')]",
  "type": "Microsoft.Network/virtualNetworks",
  "name": "[variables('virtualNetworkName')]",
  "location": "[resourceGroup().location]",
  "properties": {
    "addressSpace": {
      "addressPrefixes": [
        "[variables('azureVNetAddressPrefix')]"
      ]
    },
    "subnets": [
      {
        "name": "[variables('subnetName')]",
        "properties": {
          "addressPrefix": "[variables('subnetPrefix')]"
        }
      },
      {
        "name": "GatewaySubnet",
        "properties": {
          "addressPrefix": "[variables('gatewaySubnetPrefix')]"
        }
      }
    ]
  },
  "comments": "Create a Virtual Network with Subnet1 and Gatewaysubnet"
},
```

### <a name="ip"></a>3.3 задайте общих IP-адресов для экземпляров шлюза VPN
 
Назначьте общедоступный IP-адрес для каждого экземпляра шлюза VPN.

```json
{
  "apiVersion": "[variables('api-version')]",
  "type": "Microsoft.Network/publicIPAddresses",
    "name": "[variables('gatewayPublicIPName1')]",
    "location": "[resourceGroup().location]",
    "properties": {
      "publicIPAllocationMethod": "Dynamic"
    },
    "comments": "Public IP for the first instance of the VPN gateway"
  },
  {
    "apiVersion": "[variables('api-version')]",
    "type": "Microsoft.Network/publicIPAddresses",
    "name": "[variables('gatewayPublicIPName2')]",
    "location": "[resourceGroup().location]",
    "properties": {
      "publicIPAllocationMethod": "Dynamic"
    },
    "comments": "Public IP for the second instance of the VPN gateway"
  },
```

### <a name="termination"></a>3.4 укажите окончание туннеля VPN локальной (шлюз локальной сети)

VPN-устройства локальной называются **шлюз локальной сети**. В следующем фрагменте json также указывает удаленного однорангового узла BGP подробные сведения:

```json
{
  "apiVersion": "[variables('api-version')]",
  "type": "Microsoft.Network/localNetworkGateways",
  "name": "[variables('localGatewayName')]",
  "location": "[resourceGroup().location]",
  "properties": {
    "localNetworkAddressSpace": {
      "addressPrefixes": "[variables('localAddressPrefix')]"
    },
    "gatewayIpAddress": "[variables('localGatewayIpAddress')]",
    "bgpSettings": {
      "asn": "[variables('asnRemote')]",
      "bgpPeeringAddress": "[variables('bgpPeeringAddress')]",
      "peerWeight": 0
    }
  },
  "comments": "Local Network Gateway (referred to your on-premises location) with IP address of remote tunnel peering and IP address of remote BGP peer"
},
```

### <a name="creategw"></a>3.5 создании шлюза VPN

В этом разделе шаблон настраивает VPN-шлюз с необходимыми параметрами для активных конфигурации. Не забывайте о следующих требованиях:

* Создание шлюза VPN с **routebased «.»** VpnType. Этот параметр является обязательным, если вы хотите включить маршрутизации BGP между шлюзом VPN и VPN локальной.
* Для установления VPN-туннели между двух экземпляров шлюза VPN и заданного локальное устройство в режим активный / активный **«activeActive»** параметра равным **true** шаблона диспетчера ресурсов . Чтобы получить представление о высокой доступности шлюзах VPN, в разделе [высокий уровень доступности подключения шлюза VPN](../vpn-gateway/vpn-gateway-highlyavailable.md).
* Чтобы настроить eBGP сеансов между VPN-туннели, необходимо указать два различных номера ASN с обеих сторон. Рекомендуется указывать закрытый номера ASN. Дополнительные сведения см. в разделе [Обзор BGP и Azure VPN шлюзы](../vpn-gateway/vpn-gateway-bgp-overview.md).

```json
{
"apiVersion": "[variables('api-version')]",
"type": "Microsoft.Network/virtualNetworkGateways",
"name": "[variables('gatewayName')]",
"location": "[resourceGroup().location]",
"dependsOn": [
  "[concat('Microsoft.Network/publicIPAddresses/', variables('gatewayPublicIPName1'))]",
  "[concat('Microsoft.Network/publicIPAddresses/', variables('gatewayPublicIPName2'))]",
  "[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]"
],
"properties": {
  "ipConfigurations": [
    {
      "properties": {
        "privateIPAllocationMethod": "Dynamic",
        "subnet": {
          "id": "[variables('gatewaySubnetRef')]"
        },
        "publicIPAddress": {
          "id": "[resourceId('Microsoft.Network/publicIPAddresses',variables('gatewayPublicIPName1'))]"
        }
      },
      "name": "vnetGtwConfig1"
    },
    {
      "properties": {
        "privateIPAllocationMethod": "Dynamic",
        "subnet": {
          "id": "[variables('gatewaySubnetRef')]"
        },
        "publicIPAddress": {
          "id": "[resourceId('Microsoft.Network/publicIPAddresses',variables('gatewayPublicIPName2'))]"
        }
      },
          "name": "vnetGtwConfig2"
        }
      ],
      "sku": {
        "name": "[variables('gatewaySku')]",
        "tier": "[variables('gatewaySku')]"
      },
      "gatewayType": "Vpn",
      "vpnType": "[variables('vpnType')]",
      "enableBgp": true,
      "activeActive": true,
      "bgpSettings": {
        "asn": "[variables('asnVpnGateway')]"
      }
    },
    "comments": "VPN Gateway in active-active configuration with BGP support"
  },
```

### <a name="ipsectunnel"></a>3.6 установления туннели IPsec

Финальное действие скрипта создает туннели IPsec между шлюзом Azure VPN и локальным устройством VPN.

```json
{
  "apiVersion": "[variables('api-version')]",
  "name": "[variables('connectionName')]",
  "type": "Microsoft.Network/connections",
  "location": "[resourceGroup().location]",
  "dependsOn": [
    "[concat('Microsoft.Network/virtualNetworkGateways/', variables('gatewayName'))]",
    "[concat('Microsoft.Network/localNetworkGateways/', variables('localGatewayName'))]"
  ],
  "properties": {
    "virtualNetworkGateway1": {
      "id": "[resourceId('Microsoft.Network/virtualNetworkGateways', variables('gatewayName'))]"
    },
    "localNetworkGateway2": {
      "id": "[resourceId('Microsoft.Network/localNetworkGateways', variables('localGatewayName'))]"
    },
    "connectionType": "IPsec",
    "routingWeight": 0,
    "sharedKey": "[variables('sharedKey')]",
    "enableBGP": "true"
  },
  "comments": "Create a Connection type site-to-site (IPsec) between the Azure VPN Gateway and the VPN device on-premises"
  }
```

## <a name="device"></a>4. Настройка локального устройства VPN

Шлюз Azure VPN совместима с многих устройств VPN от разных поставщиков. Сведения о конфигурации и устройств, которые были проверены для работы с VPN-шлюза см. в разделе [сведения о VPN-устройства](../vpn-gateway/vpn-gateway-about-vpn-devices.md).

Во время настройки устройства VPN, необходимо следующее:

* Общий ключ. Это один общий ключ, указываемое при создании сеть сеть VPN-подключение. В примерах используется базовый общий ключ. Для практического использования рекомендуется создавать более сложные ключи.
* Общедоступный IP-адрес шлюза VPN. Общедоступный IP-адрес можно просмотреть с помощью портала Azure, PowerShell или CLI. Чтобы найти общедоступный IP-адрес шлюза VPN с помощью портала Azure, перейдите к шлюзам виртуальной сети, а затем щелкните имя шлюза.

Обычно eBGP одноранговые узлы подключены напрямую (часто через ГЛОБАЛЬНУЮ сеть). Однако при настройке eBGP через VPN-туннели IPsec через пиринг Майкрософт ExpressRoute существуют несколько доменов маршрутизации между узлами eBGP. Используйте **ebgp-мульти-прыжка** команда для установления связи соседа eBGP между ними не-непосредственно подключенные одноранговых узлов. Целое число, которое соответствует ebgp-мульти-прыжка команда указывает значение времени ЖИЗНИ в пакетах BGP. Команда **eibgp пути максимум 2** обеспечивает балансировку нагрузки трафика между двумя контурами BGP.

### <a name="cisco1"></a>Пример CSR1000 Cisco

В следующем примере показано конфигурации Cisco CSR1000 на виртуальной машине Hyper-V, как устройство VPN локальной:

```
!
crypto ikev2 proposal az-PROPOSAL
 encryption aes-cbc-256 aes-cbc-128 3des
 integrity sha1
 group 2
!
crypto ikev2 policy az-POLICY
 proposal az-PROPOSAL
!
crypto ikev2 keyring key-peer1
 peer azvpn1
  address 52.175.253.112
  pre-shared-key secret*1234
 !
!
crypto ikev2 keyring key-peer2
 peer azvpn2
  address 52.175.250.191
  pre-shared-key secret*1234
 !
!
!
crypto ikev2 profile az-PROFILE1
 match address local interface GigabitEthernet1
 match identity remote address 52.175.253.112 255.255.255.255
 authentication remote pre-share
 authentication local pre-share
 keyring local key-peer1
!
crypto ikev2 profile az-PROFILE2
 match address local interface GigabitEthernet1
 match identity remote address 52.175.250.191 255.255.255.255
 authentication remote pre-share
 authentication local pre-share
 keyring local key-peer2
!
crypto ikev2 dpd 10 2 on-demand
!
!
crypto ipsec transform-set az-IPSEC-PROPOSAL-SET esp-aes 256 esp-sha-hmac
 mode tunnel
!
crypto ipsec profile az-VTI1
 set transform-set az-IPSEC-PROPOSAL-SET
 set ikev2-profile az-PROFILE1
!
crypto ipsec profile az-VTI2
 set transform-set az-IPSEC-PROPOSAL-SET
 set ikev2-profile az-PROFILE2
!
!
interface Loopback0
 ip address 172.16.0.3 255.255.255.255
!
interface Tunnel0
 ip address 172.16.0.1 255.255.255.255
 ip tcp adjust-mss 1350
 tunnel source GigabitEthernet1
 tunnel mode ipsec ipv4
 tunnel destination 52.175.253.112
 tunnel protection ipsec profile az-VTI1
!
interface Tunnel1
 ip address 172.16.0.2 255.255.255.255
 ip tcp adjust-mss 1350
 tunnel source GigabitEthernet1
 tunnel mode ipsec ipv4
 tunnel destination 52.175.250.191
 tunnel protection ipsec profile az-VTI2
!
interface GigabitEthernet1
 description External interface
 ip address x.243.229.110 255.255.255.252
 negotiation auto
 no mop enabled
 no mop sysid
!
interface GigabitEthernet2
 ip address 10.0.0.1 255.255.255.0
 negotiation auto
 no mop enabled
 no mop sysid
!
router bgp 65010
 bgp router-id interface Loopback0
 bgp log-neighbor-changes
 network 10.0.0.0 mask 255.255.255.0
 network 10.1.10.0 mask 255.255.255.128
 neighbor 10.2.0.228 remote-as 65000
 neighbor 10.2.0.228 ebgp-multihop 5
 neighbor 10.2.0.228 update-source Loopback0
 neighbor 10.2.0.228 soft-reconfiguration inbound
 neighbor 10.2.0.228 filter-list 10 out
 neighbor 10.2.0.229 remote-as 65000    
 neighbor 10.2.0.229 ebgp-multihop 5
 neighbor 10.2.0.229 update-source Loopback0
 neighbor 10.2.0.229 soft-reconfiguration inbound
 maximum-paths eibgp 2
!
ip route 0.0.0.0 0.0.0.0 10.1.10.1
ip route 10.2.0.228 255.255.255.255 Tunnel0
ip route 10.2.0.229 255.255.255.255 Tunnel1
!
```

## <a name="firewalls"></a>5. Настройка фильтрации устройств VPN и брандмауэров (необязательно)

Настройте брандмауэр и фильтрацией в соответствии с требованиями.

## <a name="testipsec"></a>6. Тестирование и проверка туннель IPsec

Можно проверить состояние туннелей IPsec шлюза Azure VPN с помощью команд Powershell:

```powershell
Get-AzureRmVirtualNetworkGatewayConnection -Name vpn2local1 -ResourceGroupName myRG | Select-Object  ConnectionStatus,EgressBytesTransferred,IngressBytesTransferred | fl
```

Выходные данные примера:

```powershell
ConnectionStatus        : Connected
EgressBytesTransferred  : 17734660
IngressBytesTransferred : 10538211
```

Чтобы проверить состояние туннели на экземплярах шлюза Azure VPN независимо друг от друга, используйте следующий пример:

```powershell
Get-AzureRmVirtualNetworkGatewayConnection -Name vpn2local1 -ResourceGroupName myRG | Select-Object -ExpandProperty TunnelConnectionStatus
```

Выходные данные примера:

```powershell
Tunnel                           : vpn2local1_52.175.250.191
ConnectionStatus                 : Connected
IngressBytesTransferred          : 4877438
EgressBytesTransferred           : 8754071
LastConnectionEstablishedUtcTime : 11/04/2017 17:03:30

Tunnel                           : vpn2local1_52.175.253.112
ConnectionStatus                 : Connected
IngressBytesTransferred          : 5660773
EgressBytesTransferred           : 8980589
LastConnectionEstablishedUtcTime : 11/04/2017 17:03:13
```

Можно также проверить состояние туннеля для VPN-устройства локальной.

Пример CSR1000 Cisco.

```
show crypto session detail
show crypto ikev2 sa
show crypto ikev2 session detail
show crypto ipsec sa
```

Выходные данные примера:

```
csr1#show crypto session detail

Crypto session current status

Code: C - IKE Configuration mode, D - Dead Peer Detection
K - Keepalives, N - NAT-traversal, T - cTCP encapsulation
X - IKE Extended Authentication, F - IKE Fragmentation
R - IKE Auto Reconnect

Interface: Tunnel1
Profile: az-PROFILE2
Uptime: 00:52:46
Session status: UP-ACTIVE
Peer: 52.175.250.191 port 4500 fvrf: (none) ivrf: (none)
      Phase1_id: 52.175.250.191
      Desc: (none)
  Session ID: 3
  IKEv2 SA: local 10.1.10.50/4500 remote 52.175.250.191/4500 Active
          Capabilities:DN connid:3 lifetime:23:07:14
  IPSEC FLOW: permit ip 0.0.0.0/0.0.0.0 0.0.0.0/0.0.0.0
        Active SAs: 2, origin: crypto map
        Inbound:  #pkts dec'ed 279 drop 0 life (KB/Sec) 4607976/433
        Outbound: #pkts enc'ed 164 drop 0 life (KB/Sec) 4607992/433

Interface: Tunnel0
Profile: az-PROFILE1
Uptime: 00:52:43
Session status: UP-ACTIVE
Peer: 52.175.253.112 port 4500 fvrf: (none) ivrf: (none)
      Phase1_id: 52.175.253.112
      Desc: (none)
  Session ID: 2
  IKEv2 SA: local 10.1.10.50/4500 remote 52.175.253.112/4500 Active
          Capabilities:DN connid:2 lifetime:23:07:17
  IPSEC FLOW: permit ip 0.0.0.0/0.0.0.0 0.0.0.0/0.0.0.0
        Active SAs: 2, origin: crypto map
        Inbound:  #pkts dec'ed 668 drop 0 life (KB/Sec) 4607926/437
        Outbound: #pkts enc'ed 477 drop 0 life (KB/Sec) 4607953/437
```

Протокол линии на виртуальный интерфейс туннеля (VTI) не изменяется на «вверх», до завершения IKE фазы 2. Следующая команда проверяет сопоставления безопасности:

```
csr1#show crypto ikev2 sa

IPv4 Crypto IKEv2  SA

Tunnel-id Local                 Remote                fvrf/ivrf            Status
2         10.1.10.50/4500       52.175.253.112/4500   none/none            READY
      Encr: AES-CBC, keysize: 256, PRF: SHA1, Hash: SHA96, DH Grp:2, Auth sign: PSK, Auth verify: PSK
      Life/Active Time: 86400/3277 sec

Tunnel-id Local                 Remote                fvrf/ivrf            Status
3         10.1.10.50/4500       52.175.250.191/4500   none/none            READY
      Encr: AES-CBC, keysize: 256, PRF: SHA1, Hash: SHA96, DH Grp:2, Auth sign: PSK, Auth verify: PSK
      Life/Active Time: 86400/3280 sec

IPv6 Crypto IKEv2  SA

csr1#show crypto ipsec sa | inc encaps|decaps
    #pkts encaps: 177, #pkts encrypt: 177, #pkts digest: 177
    #pkts decaps: 296, #pkts decrypt: 296, #pkts verify: 296
    #pkts encaps: 554, #pkts encrypt: 554, #pkts digest: 554
    #pkts decaps: 746, #pkts decrypt: 746, #pkts verify: 746
```

### <a name="verifye2e"></a>Проверьте сетевое подключение и всестороннее между внутренней сети в локальной и виртуальной сети Azure

Если туннели IPsec высока и статические маршруты заданы правильно, следует IP-адреса удаленного узла BGP:

```
csr1#ping 10.2.0.228
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 10.2.0.228, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 5/5/5 ms

#ping 10.2.0.229
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 10.2.0.229, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 4/5/6 ms
```

### <a name="verifybgp"></a>Проверка сеансов BGP через IPsec

Для шлюза Azure VPN Проверьте состояние узла BGP:

```powershell
Get-AzureRmVirtualNetworkGatewayBGPPeerStatus -VirtualNetworkGatewayName vpnGtw -ResourceGroupName SEA-C1-VPN-ER | ft
```

Выходные данные примера:

```powershell
  Asn ConnectedDuration LocalAddress MessagesReceived MessagesSent Neighbor    RoutesReceived State    
  --- ----------------- ------------ ---------------- ------------ --------    -------------- -----    
65010 00:57:19.9003584  10.2.0.228               68           72   172.16.0.10              2 Connected
65000                   10.2.0.228                0            0   10.2.0.228               0 Unknown  
65000 07:13:51.0109601  10.2.0.228              507          500   10.2.0.229               6 Connected
```

Чтобы проверить список префиксов сети, полученных через eBGP VPN концентратор локально, можно отфильтровать по атрибут «Источник»:

```powershell
Get-AzureRmVirtualNetworkGatewayLearnedRoute -VirtualNetworkGatewayName vpnGtw -ResourceGroupName myRG  | Where-Object Origin -eq "EBgp" |ft
```

В выходных данных примера ASN 65010 — номер автономной системы BGP VPN на локальном компьютере.

```powershell
AsPath LocalAddress Network      NextHop     Origin SourcePeer  Weight
------ ------------ -------      -------     ------ ----------  ------
65010  10.2.0.228   10.1.10.0/25 172.16.0.10 EBgp   172.16.0.10  32768
65010  10.2.0.228   10.0.0.0/24  172.16.0.10 EBgp   172.16.0.10  32768
```

Чтобы просмотреть список объявленных маршрутов:

```powershell
Get-AzureRmVirtualNetworkGatewayAdvertisedRoute -VirtualNetworkGatewayName vpnGtw -ResourceGroupName myRG -Peer 10.2.0.228 | ft
```

Выходные данные примера:

```powershell
AsPath LocalAddress Network        NextHop    Origin SourcePeer Weight
------ ------------ -------        -------    ------ ---------- ------
       10.2.0.229   10.2.0.0/24    10.2.0.229 Igp                  0
       10.2.0.229   172.16.0.10/32 10.2.0.229 Igp                  0
       10.2.0.229   172.16.0.5/32  10.2.0.229 Igp                  0
       10.2.0.229   172.16.0.1/32  10.2.0.229 Igp                  0
65010  10.2.0.229   10.1.10.0/25   10.2.0.229 Igp                  0
65010  10.2.0.229   10.0.0.0/24    10.2.0.229 Igp                  0
```

Пример для CSR1000 Cisco в локальной среде.

```
csr1#show ip bgp neighbors 10.2.0.228 routes
BGP table version is 7, local router ID is 172.16.0.10
Status codes: s suppressed, d damped, h history, * valid, > best, i - internal,
              r RIB-failure, S Stale, m multipath, b backup-path, f RT-Filter,
              x best-external, a additional-path, c RIB-compressed,
              t secondary path,
Origin codes: i - IGP, e - EGP, ? - incomplete
RPKI validation codes: V valid, I invalid, N Not found

     Network          Next Hop            Metric LocPrf Weight Path
 *>   10.2.0.0/24      10.2.0.228                             0 65000 i
 r>   172.16.0.1/32    10.2.0.228                             0 65000 i
 r>   172.16.0.2/32    10.2.0.228                             0 65000 i
 r>   172.16.0.3/32   10.2.0.228                             0 65000 i

Total number of prefixes 4
```

Список сетей, объявлена с помощью CSR1000 Cisco локальный шлюз Azure VPN могут быть перечислены с помощью следующей команды:

```powershell
csr1#show ip bgp neighbors 10.2.0.228 advertised-routes
BGP table version is 7, local router ID is 172.16.0.10
Status codes: s suppressed, d damped, h history, * valid, > best, i - internal,
              r RIB-failure, S Stale, m multipath, b backup-path, f RT-Filter,
              x best-external, a additional-path, c RIB-compressed,
              t secondary path,
Origin codes: i - IGP, e - EGP, ? - incomplete
RPKI validation codes: V valid, I invalid, N Not found

     Network          Next Hop            Metric LocPrf Weight Path
 *>   10.0.0.0/24      0.0.0.0                  0         32768 i
 *>   10.1.10.0/25     0.0.0.0                  0         32768 i

Total number of prefixes 2
```

## <a name="next-steps"></a>Дальнейшие действия

* [Настройка решения "Монитор производительности сети" для ExpressRoute](how-to-npm.md)

* [Добавление подключения сеть сеть с помощью существующего VPN-подключения шлюза виртуальной сети](../vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md)
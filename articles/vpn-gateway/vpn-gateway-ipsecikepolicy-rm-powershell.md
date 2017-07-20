---
title: "Настройка политики IPsec/IKE для VPN-подключений типа &quot;сеть — сеть&quot; или &quot;виртуальная сеть — виртуальная сеть&quot;. Azure Resource Manager: PowerShell | Документация Майкрософт"
description: "В этой статье описана настройка политики IPsec/IKE для подключений типа &quot;сеть — сеть&quot; или &quot;виртуальная сеть — виртуальная сеть&quot; с VPN-шлюзами Azure с помощью Azure Resource Manager и PowerShell."
services: vpn-gateway
documentationcenter: na
author: yushwang
manager: rossort
editor: 
tags: azure-resource-manager
ms.assetid: 238cd9b3-f1ce-4341-b18e-7390935604fa
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/12/2017
ms.author: yushwang
ms.translationtype: Human Translation
ms.sourcegitcommit: db18dd24a1d10a836d07c3ab1925a8e59371051f
ms.openlocfilehash: 12502c91f7dff01651e3edcfd1dfa6a9d5ffe234
ms.contentlocale: ru-ru
ms.lasthandoff: 06/15/2017


---
# <a name="configure-ipsecike-policy-for-s2s-vpn-or-vnet-to-vnet-connections"></a>Настройка политики IPsec/IKE для VPN-подключений типа "сеть — сеть" или "виртуальная сеть — виртуальная сеть"

В этой статье содержится пошаговое описание процесса по настройке политики IPsec/IKE для VPN-подключений типа "сеть — сеть" или "виртуальная сеть — виртуальная сеть" с помощью модели развертывания диспетчера ресурсов и PowerShell.

## <a name="about-ipsec-and-ike-policy-parameters-for-azure-vpn-gateways"></a>Параметры политики IPsec и IKE для VPN-шлюзов Azure
Стандарт протоколов IPsec и IKE поддерживает широкий набор алгоритмов шифрования в различных сочетаниях. Ознакомьтесь [с требованиями к шифрованию и VPN-шлюзами Azure](vpn-gateway-about-compliance-crypto.md), чтобы узнать больше о том, как обеспечить следование требованиям безопасности или соответствия для подключений между локальными и виртуальными сетями.

Эта статья содержит инструкции по созданию и настройке политики IPsec/IKE, а также ее применению к новому или имеющемуся подключению.

* [Часть 1. Рабочий процесс, необходимый для создания и настройки политики IPsec/IKE](#workflow)
* [Часть 2. Поддерживаемые алгоритмы шифрования и уровни стойкости ключей](#params)
* [Часть 3. Создание нового подключения VPN типа "сеть — сеть" с помощью политики IPsec/IKE](#crossprem)
* [Часть 4. Создание нового подключения VPN типа "виртуальная сеть — виртуальная сеть" с помощью политики IPsec/IKE](#vnet2vnet)
* [Часть 5. Управление политикой IPsec/IKE (а также ее создание, добавление и удаление) для подключения](#managepolicy)

> [!IMPORTANT]
> 1. Обратите внимание, что политика IPsec/IKE будет актуальна только для ***стандартных*** ***высокопроизводительных*** VPN-шлюзов ***на основе маршрутов***.
> 2. Можно указать только ***одну*** комбинацию политик для каждого подключения.
> 3. Вам следует указать все алгоритмы и параметры для IKE (основной режим) и IPsec (быстрый режим). Указать частичную политику нельзя.
> 4. Мы рекомендуем ознакомиться со спецификациями поставщиков устройств VPN, чтобы убедиться, что политика поддерживается локальными устройствами VPN. Если политики несовместимы, невозможно будет установить VPN-подключения типа "сеть — сеть" или "виртуальная сеть — виртуальная сеть".

## <a name ="workflow"></a>Часть 1. Рабочий процесс, необходимый для создания и настройки политики IPsec/IKE
В этом разделе описывается рабочий процесс, необходимый для создания и обновления политики IPsec/IKE для VPN-подключений типа "сеть — сеть" или "виртуальная сеть — виртуальная сеть":
1. Создание виртуальной сети и VPN-шлюза.
2. Создание шлюза локальной сети для локального подключения или другой виртуальной сети и шлюза для подключения типа "виртуальная сеть — виртуальная сеть".
3. Создание политики IPsec/IKE с выбранными алгоритмами и параметрами.
4. Создание подключения (IPsec или VNet2VNet) с помощью политики IPsec/IKE.
5. Добавление, обновление и удаление политики IPsec/IKE для существующего подключения.

Инструкции в этой статье помогут установить и настроить политики IPsec/IKE, как показано на схеме ниже:

![ipsec-ike-policy](./media/vpn-gateway-ipsecikepolicy-rm-powershell/ipsecikepolicy.png)

## <a name ="params"></a>Часть 2. Поддерживаемые алгоритмы шифрования и уровни стойкости ключей

В следующей таблице перечислены поддерживаемые алгоритмы шифрования и уровни стойкости ключей, которые могут настроить клиенты.

| **IPsec/IKEv2**  | **Варианты**                                                                 |
| ---              | ---                                                                         |
| Шифрование IKEv2 | AES256, AES192, AES128, DES3, DES                                           |
| Проверка целостности IKEv2  | SHA384, SHA256, SHA1, MD5                                                   |
| Группа DH         | ECP384, ECP256, DHGroup24, DHGroup14, DHGroup2048, DHGroup2, DHGroup1, нет |
| Шифрование IPsec | GCMAES256, GCMAES192, GCMAES128, AES256, AES192, AES128, DES3, DES, нет    |
| Целостность IPsec  | GCMASE256, GCMAES192, GCMAES128, SHA256, SHA1, MD5                          |
| Группа PFS        | ECP384, ECP256, PFS24, PFS2048, PFS14, PFS2, PFS1, нет                     |
| Время существования QM SA*  | Секунды (целое число) и килобайты (целое число)                                      |
| Селектор трафика | UsePolicyBasedTrafficSelectors** ($True/$False - default $False)            |
|                  |                                                                             |

> [!NOTE]
> * (*) Время существования SA основного режима IKEv2 составляет 28 800 секунд на VPN-шлюзах Azure
> * (**) Если указать для параметра UsePolicyBasedTrafficSelectors значение $True для подключения, это позволит настроить VPN-шлюз Azure для локального подключения к брандмауэру VPN на основе политики. Если вы включили параметр PolicyBasedTrafficSelectors, необходимо обеспечить соответствующие селекторы трафика для VPN-устройства, которые определены с помощью всех комбинаций префиксов локальной сети (шлюза локальной сети) с префиксами виртуальной сети Azure, а не разрешать совпадение любого префикса с любым. Например, если префиксы локальной сети — 10.1.0.0/16 и 10.2.0.0/16, а префиксы виртуальной сети — 192.168.0.0/16 и 172.16.0.0/16, необходимо указать следующие селекторы трафика:
>   * 10.1.0.0/16 <====> 192.168.0.0/16;
>   * 10.1.0.0/16 <====> 172.16.0.0/16;
>   * 10.2.0.0/16 <====> 192.168.0.0/16;
>   * 10.2.0.0/16 <====> 172.16.0.0/16.

Дополнительные сведения о селекторах трафика на основе политик см. в статье [Подключение VPN-шлюзов Azure к нескольким локальным VPN-устройствам на основе политики с помощью PowerShell](vpn-gateway-connect-multiple-policybased-rm-ps.md).

## <a name ="crossprem"></a>Часть 3. Создание нового подключения VPN типа "сеть — сеть" с помощью политики IPsec/IKE

В этом разделе описаны действия по созданию VPN-подключения типа "сеть — сеть" с помощью политики IPsec/IKE. С помощью инструкций ниже вы сможете создать подключение, как показано на схеме:

![s2s-policy](./media/vpn-gateway-ipsecikepolicy-rm-powershell/s2spolicy.png)

Пошаговые инструкции по созданию VPN-подключения типа "сеть — сеть" см. в статье [Создание виртуальной сети с VPN-подключением типа "сеть — сеть" с помощью PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md).

### <a name="before-you-begin"></a>Перед началом работы
* Убедитесь в том, что у вас уже есть подписка Azure. Если у вас нет подписки Azure, вы можете [активировать преимущества для подписчиков MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) или [зарегистрировать бесплатную учетную запись](https://azure.microsoft.com/pricing/free-trial/).
* Вам потребуется установить командлеты PowerShell диспетчера ресурсов Azure. Дополнительные сведения об установке командлетов PowerShell см. в разделе [Общие сведения об Azure PowerShell](/powershell/azure/overview).

### <a name="step-1---create-the-virtual-network-vpn-gateway-and-local-network-gateway"></a>Шаг 1. Создание виртуальной сети, VPN-шлюза и шлюза локальной сети

#### <a name="1-declare-your-variables"></a>1. Объявление переменных
В этом упражнении мы начнем с объявления переменных. Обязательно замените значения своими при настройке для рабочей среды.

```powershell
$Sub1          = "<YourSubscriptionName>"
$RG1           = "TestPolicyRG1"
$Location1     = "East US 2"
$VNetName1     = "TestVNet1"
$FESubName1    = "FrontEnd"
$BESubName1    = "Backend"
$GWSubName1    = "GatewaySubnet"
$VNetPrefix11  = "10.11.0.0/16"
$VNetPrefix12  = "10.12.0.0/16"
$FESubPrefix1  = "10.11.0.0/24"
$BESubPrefix1  = "10.12.0.0/24"
$GWSubPrefix1  = "10.12.255.0/27"
$DNS1          = "8.8.8.8"
$GWName1       = "VNet1GW"
$GW1IPName1    = "VNet1GWIP1"
$GW1IPconf1    = "gw1ipconf1"
$Connection16  = "VNet1toSite6"

$LNGName6      = "Site6"
$LNGPrefix61   = "10.61.0.0/16"
$LNGPrefix62   = "10.62.0.0/16"
$LNGIP6        = "131.107.72.22"
```

#### <a name="2-connect-to-your-subscription-and-create-a-new-resource-group"></a>2) Подключение к подписке Azure и создание группы ресурсов
Для работы с командлетами диспетчера ресурсов необходимо перейти в режим PowerShell. Дополнительные сведения см. в статье [Использование Azure PowerShell с диспетчером ресурсов Azure](../powershell-azure-resource-manager.md).

Откройте консоль PowerShell и подключитесь к своей учетной записи. Для подключения используйте следующий пример.

```powershell
Login-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName $Sub1
New-AzureRmResourceGroup -Name $RG1 -Location $Location1
```

#### <a name="3-create-the-virtual-network-vpn-gateway-and-local-network-gateway"></a>3. Создание виртуальной сети, VPN-шлюза и шлюза локальной сети
В следующем примере создается виртуальная сеть — TestVNet1 с тремя подсетями и VPN-шлюзом. При замене значений важно, чтобы вы назвали подсеть шлюза именем GatewaySubnet. Если вы используете другое имя, шлюз не будет создан.

```powershell
$fesub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName1 -AddressPrefix $FESubPrefix1
$besub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName1 -AddressPrefix $BESubPrefix1
$gwsub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName1 -AddressPrefix $GWSubPrefix1

New-AzureRmVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1 -Location $Location1 -AddressPrefix $VNetPrefix11,$VNetPrefix12 -Subnet $fesub1,$besub1,$gwsub1

$gw1pip1    = New-AzureRmPublicIpAddress -Name $GW1IPName1 -ResourceGroupName $RG1 -Location $Location1 -AllocationMethod Dynamic
$vnet1      = Get-AzureRmVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1
$subnet1    = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet1
$gw1ipconf1 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GW1IPconf1 -Subnet $subnet1 -PublicIpAddress $gw1pip1

New-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1 -Location $Location1 -IpConfigurations $gw1ipconf1 -GatewayType Vpn -VpnType RouteBased -GatewaySku HighPerformance

New-AzureRmLocalNetworkGateway -Name $LNGName6 -ResourceGroupName $RG1 -Location $Location1 -GatewayIpAddress $LNGIP6 -AddressPrefix $LNGPrefix61,$LNGPrefix62
```

### <a name="step-2---creat-a-s2s-vpn-connection-with-an-ipsecike-policy"></a>Шаг 2. Создание подключения VPN типа "сеть — сеть" с помощью политики IPsec/IKE

#### <a name="1-create-an-ipsecike-policy"></a>1. Создайте политику IPsec/IKE.
Пример сценария ниже создает политику IPsec/IKE со следующими параметрами и алгоритмами:
* IKEv2: AES256, SHA384, DHGroup24
* IPsec: AES256, SHA256, PFS24, SA Lifetime 7200 seconds & 2048KB

```powershell
$ipsecpolicy6 = New-AzureRmIpsecPolicy -IkeEncryption AES256 -IkeIntegrity SHA384 -DhGroup DHGroup24 -IpsecEncryption AES256 -IpsecIntegrity SHA256 -PfsGroup PFS24 -SALifeTimeSeconds 7200 -SADataSizeKilobytes 2048
```

#### <a name="2-create-the-s2s-vpn-connection-with-the-ipsecike-policy"></a>2) Создание подключения VPN типа "сеть — сеть" с помощью политики IPsec/IKE
Создайте подключение VPN типа "сеть — сеть" и примените созданную выше политику IPsec/IKE.

```powershell
$vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
$lng6 = Get-AzureRmLocalNetworkGateway  -Name $LNGName6 -ResourceGroupName $RG1

New-AzureRmVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng6 -Location $Location1 -ConnectionType IPsec -IpsecPolicies $ipsecpolicy6 -SharedKey 'AzureA1b2C3'
```

При необходимости можно добавить "-UsePolicyBasedTrafficSelectors $True", чтобы создать командлет подключения для включения VPN-шлюза Azure для локального подключения к VPN-устройствам на основе политики, как описано выше.

> [!IMPORTANT]
> После указания для подключения политики IPsec/IKE VPN-шлюз Azure будет только отправлять и принимать предложения IPsec/IKE с определенными алгоритмами шифрования и уровнями стойкости ключей для этого подключения. Локальное VPN-устройство для подключения должно использовать или принимать точную комбинацию политик. В противном случае VPN-туннель типа "сеть — сеть" установлен не будет.


## <a name ="vnet2vnet"></a>Часть 4. Создание нового подключения VPN типа "виртуальная сеть — виртуальная сеть" с помощью политики IPsec/IKE
Этапы создания подключения типа "виртуальная сеть — виртуальная сеть" с помощью политики IPsec/IKE схожи с этапами, описанными при создании подключения VPN типа "сеть — сеть". Примеры сценариев ниже создают подключение, как показано на схеме:

![v2v-policy](./media/vpn-gateway-ipsecikepolicy-rm-powershell/v2vpolicy.png)

Дополнительные сведения о создании подключения типа "виртуальная сеть — виртуальная сеть" см. в статье [Настройка подключения VPN-шлюза между виртуальными сетями с помощью PowerShell](vpn-gateway-vnet-vnet-rm-ps.md). Чтобы создать и настроить сеть TestVNet1 и VPN-шлюз, сначала следует выполнить инструкции из [третьей части](#crossprem). 

### <a name="step-1---create-the-second-virtual-network-and-vpn-gateway"></a>Шаг 1. Создание второй виртуальной сети и VPN-шлюза

#### <a name="1-declare-your-variables"></a>1. Объявление переменных
Не забудьте заменить значения теми, которые вы хотите использовать для конфигурации.

```powershell
$RG2          = "TestPolicyRG2"
$Location2    = "East US 2"
$VNetName2    = "TestVNet2"
$FESubName2   = "FrontEnd"
$BESubName2   = "Backend"
$GWSubName2   = "GatewaySubnet"
$VNetPrefix21 = "10.21.0.0/16"
$VNetPrefix22 = "10.22.0.0/16"
$FESubPrefix2 = "10.21.0.0/24"
$BESubPrefix2 = "10.22.0.0/24"
$GWSubPrefix2 = "10.22.255.0/27"
$DNS2         = "8.8.8.8"
$GWName2      = "VNet2GW"
$GW2IPName1   = "VNet2GWIP1"
$GW2IPconf1   = "gw2ipconf1"
$Connection21 = "VNet2toVNet1"
$Connection12 = "VNet1toVNet2"
```

#### <a name="2-create-the-second-virtual-network-and-vpn-gateway-in-the-new-resource-group"></a>2) Создание второй виртуальной сети и VPN-шлюза в новой группе ресурсов
```powershell
New-AzureRmResourceGroup -Name $RG2 -Location $Location2

$fesub2 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName2 -AddressPrefix $FESubPrefix2
$besub2 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName2 -AddressPrefix $BESubPrefix2
$gwsub2 = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName2 -AddressPrefix $GWSubPrefix2

New-AzureRmVirtualNetwork -Name $VNetName2 -ResourceGroupName $RG2 -Location $Location2 -AddressPrefix $VNetPrefix21,$VNetPrefix22 -Subnet $fesub2,$besub2,$gwsub2

$gw2pip1    = New-AzureRmPublicIpAddress -Name $GW2IPName1 -ResourceGroupName $RG2 -Location $Location2 -AllocationMethod Dynamic
$vnet2      = Get-AzureRmVirtualNetwork -Name $VNetName2 -ResourceGroupName $RG2
$subnet2    = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet2
$gw2ipconf1 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GW2IPconf1 -Subnet $subnet2 -PublicIpAddress $gw2pip1

New-AzureRmVirtualNetworkGateway -Name $GWName2 -ResourceGroupName $RG2 -Location $Location2 -IpConfigurations $gw2ipconf1 -GatewayType Vpn -VpnType RouteBased -GatewaySku HighPerformance
```

### <a name="step-2---creat-a-vnet-tovnet-connection-with-the-ipsecike-policy"></a>Шаг 2. Создайте подключение типа "виртуальная сеть — виртуальная сеть" с помощью политики IPsec/IKE
Аналогично созданию подключения VPN типа "сеть — сеть" создайте политику IPsec/IKE, а затем примените ее к новому подключению.

#### <a name="1-create-an-ipsecike-policy"></a>1. Создайте политику IPsec/IKE.
Пример сценария ниже создает другую политику IPsec/IKE со следующими параметрами и алгоритмами:
* IKEv2: AES128, SHA1, DHGroup14
* IPsec: GCMAES128, GCMAES128, PFS14, SA Lifetime 7200 seconds & 4096KB

```powershell
$ipsecpolicy2 = New-AzureRmIpsecPolicy -IkeEncryption AES128 -IkeIntegrity SHA1 -DhGroup DHGroup14 -IpsecEncryption GCMAES128 -IpsecIntegrity GCMAES128 -PfsGroup PFS14 -SALifeTimeSeconds 7200 -SADataSizeKilobytes 4096
```

#### <a name="2-create-vnet-to-vnet-connections-with-the-ipsecike-policy"></a>2) Создание подключений типа "виртуальная сеть — виртуальная сеть" с помощью политики IPsec/IKE
Создайте подключение VPN типа "виртуальная сеть — виртуальная сеть" и примените созданную выше политику IPsec/IKE. В этом примере оба шлюза находятся в одной подписке. Поэтому можно создать и настроить оба подключения с помощью одной политики IPsec/IKE в одном сеансе PowerShell.

```powershell
$vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
$vnet2gw = Get-AzureRmVirtualNetworkGateway -Name $GWName2  -ResourceGroupName $RG2

New-AzureRmVirtualNetworkGatewayConnection -Name $Connection12 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -VirtualNetworkGateway2 $vnet2gw -Location $Location1 -ConnectionType Vnet2Vnet -IpsecPolicies $ipsecpolicy2 -SharedKey 'AzureA1b2C3'

New-AzureRmVirtualNetworkGatewayConnection -Name $Connection21 -ResourceGroupName $RG2 -VirtualNetworkGateway1 $vnet2gw -VirtualNetworkGateway2 $vnet1gw -Location $Location2 -ConnectionType Vnet2Vnet -IpsecPolicies $ipsecpolicy2 -SharedKey 'AzureA1b2C3'
```

> [!IMPORTANT]
> После указания для подключения политики IPsec/IKE VPN-шлюз Azure будет только отправлять и принимать предложения IPsec/IKE с определенными алгоритмами шифрования и уровнями стойкости ключей для этого подключения. Политики IPsec для двух подключений должны быть одинаковы, в противном случае подключение VPN типа "виртуальная сеть — виртуальная сеть" установлено не будет.

После выполнения этих действий подключение будет установлено в течение нескольких минут и вы получите следующую топологию сети, как показано в начале:

![ipsec-ike-policy](./media/vpn-gateway-ipsecikepolicy-rm-powershell/ipsecikepolicy.png)


## <a name ="managepolicy"></a>Часть 5. Обновление политики IPsec/IKE для подключения
В последнем разделе описывается управление политикой IPsec/IKE для существующего подключения типа "сеть — сеть" или "виртуальная сеть — виртуальная сеть". Действия ниже помогут выполнить следующие операции подключения:

1. Показать политику подключения IPsec/IKE.
2. Добавить или обновить политику IPsec/IKE для подключения.
3. Удалить политику IPsec/IKE из подключения.

Те же действия применимы к подключениям типа "сеть — сеть" или "виртуальная сеть — виртуальная сеть".

> [!IMPORTANT]
> Политика IPsec/IKE поддерживается только для *стандартных* и *высокопроизводительных* VPN-шлюзов на основе маршрутов. Она не работает для шлюзов со SKU класса "Базовый" или VPN-шлюзов на основе политики.

#### <a name="1-show-the-ipsecike-policy-of-a-connection"></a>1. Отображение политики подключения IPsec/IKE
В примере ниже показано, как получить настроенную для подключения политику IPsec/IKE. Примеры сценариев являются продолжением примеров выше.

```powershell
$RG1          = "TestPolicyRG1"
$Connection16 = "VNet1toSite6"
$connection6  = Get-AzureRmVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1
$connection6.IpsecPolicies
```

Последняя команда отображает текущую политику IPsec/IKE, настроенную для подключения (если таковое имеется). Выходные данные ниже отображаются для подключения:

    SALifeTimeSeconds   : 3600
    SADataSizeKilobytes : 2048
    IpsecEncryption     : AES256
    IpsecIntegrity      : SHA256
    IkeEncryption       : AES256
    IkeIntegrity        : SHA384
    DhGroup             : DHGroup24
    PfsGroup            : PFS24

Если политика IPsec/IKE не настроена, команда (PS> $connection6.policy) вернет пустой список. Это не значит, что для подключения не настроена политика IPsec/IKE. Это значит, что отсутствует настраиваемая политика IPsec/IKE. Фактическое подключение будет использовать политику по умолчанию, согласованную между локальным VPN-устройством и VPN-шлюзом Azure.

#### <a name="2-add-or-update-an-ipsecike-policy-for-a-connection"></a>2) Добавление или обновление политики IPsec/IKE для подключения
Шаги по добавлению новой политики или обновлению существующей (для подключения) остаются теми же. Создайте новую политику, а затем примените ее к подключению.

```powershell
$RG1          = "TestPolicyRG1"
$Connection16 = "VNet1toSite6"
$connection6  = Get-AzureRmVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1

$newpolicy6   = New-AzureRmIpsecPolicy -IkeEncryption AES128 -IkeIntegrity SHA1 -DhGroup DHGroup14 -IpsecEncryption GCMAES128 -IpsecIntegrity GCMAES128 -PfsGroup None -SALifeTimeSeconds 3600 -SADataSizeKilobytes 2048

Set-AzureRmVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection6 -IpsecPolicies $newpolicy6
```

Чтобы включить параметр UsePolicyBasedTrafficSelectors при подключении к локальному VPN-устройству на основе политики, добавьте параметр -UsePolicyBaseTrafficSelectors в командлет или установите для него значение $False, чтобы отключить его.

```powershell
Set-AzureRmVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection6 -IpsecPolicies $newpolicy6 -UsePolicyBasedTrafficSelectors $True
```

Вы можете получить подключение еще раз, чтобы проверить, обновилась ли политика.

```powershell
$connection6  = Get-AzureRmVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1
$connection6.IpsecPolicies
```

В последней строке вы должны увидеть следующие выходные данные:

    SALifeTimeSeconds   : 3600
    SADataSizeKilobytes : 2048
    IpsecEncryption     : GCMAES128
    IpsecIntegrity      : GCMAES128
    IkeEncryption       : AES128
    IkeIntegrity        : SHA1
    DhGroup             : DHGroup14--
    PfsGroup            : None

#### <a name="3-remove-an-ipsecike-policy-from-a-connection"></a>3. Удаление политики IPsec/IKE из подключения
После удаления настраиваемой политики из подключения VPN-шлюз Azure вернется к [списку предложений IPsec/IKE по умолчанию](vpn-gateway-about-vpn-devices.md) и снова возобновит согласование для локального VPN-устройства.

```powershell
$RG1           = "TestPolicyRG1"
$Connection16  = "VNet1toSite6"
$connection6   = Get-AzureRmVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1

$currentpolicy = $connection6.IpsecPolicies[0]
$connection6.IpsecPolicies.Remove($currentpolicy)

Set-AzureRmVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection6
```

Вы снова можете использовать тот же сценарий выше, чтобы проверить, была ли политика удалена из подключения.

## <a name="next-steps"></a>Дальнейшие действия
Дополнительные сведения о селекторах трафика на основе политик см. в статье [Подключение VPN-шлюзов Azure к нескольким локальным VPN-устройствам на основе политики с помощью PowerShell](vpn-gateway-connect-multiple-policybased-rm-ps.md).

Установив подключение, можно добавить виртуальные машины в виртуальные сети. Инструкции см. в статье о [создании виртуальной машины](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).


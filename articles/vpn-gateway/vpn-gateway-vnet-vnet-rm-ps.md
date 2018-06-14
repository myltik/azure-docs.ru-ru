---
title: Установка подключения "виртуальная сеть — виртуальная сеть" между виртуальными сетями Azure | Документация Майкрософт
description: Установка подключения "виртуальная сеть — виртуальная сеть" между виртуальными сетями с помощью PowerShell.
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: jpconnock
editor: ''
tags: azure-resource-manager
ms.assetid: 0683c664-9c03-40a4-b198-a6529bf1ce8b
ms.service: vpn-gateway
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/14/2018
ms.author: cherylmc
ms.openlocfilehash: a96326e23a2ed913ccab8a23666a4f408157d3da
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/19/2018
ms.locfileid: "31603986"
---
# <a name="configure-a-vnet-to-vnet-vpn-gateway-connection-using-powershell"></a>Настройка подключения VPN-шлюза между виртуальными сетями с помощью PowerShell

Эта статья поможет установить подключение "виртуальная сеть — виртуальная сеть" между виртуальными сетями. Виртуальные сети могут относиться к одному или разным регионам и к одной или разным подпискам. При подключении виртуальных сетей из разных подписок подписки не обязательно должны быть связаны с одним и тем же клиентом Active Directory.

Приведенные в этой статье инструкции относятся к модели развертывания с помощью Resource Manager и PowerShell. Эту конфигурацию также можно создать с помощью разных средств или моделей развертывания, выбрав вариант из следующего списка:

> [!div class="op_single_selector"]
> * [портал Azure](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
> * [PowerShell](vpn-gateway-vnet-vnet-rm-ps.md)
> * [интерфейс командной строки Azure](vpn-gateway-howto-vnet-vnet-cli.md)
> * [Портал Azure (классический)](vpn-gateway-howto-vnet-vnet-portal-classic.md)
> * [Подключение с использованием разных моделей развертывания — портал Azure](vpn-gateway-connect-different-deployment-models-portal.md)
> * [Подключение с использованием разных моделей развертывания — PowerShell](vpn-gateway-connect-different-deployment-models-powershell.md)
>
>

## <a name="about"></a>Сведения о подключении виртуальных сетей

Подключить виртуальные сети можно несколькими способами. В следующих разделах описаны различные способы подключения виртуальных сетей.

### <a name="vnet-to-vnet"></a>Подключение типа "виртуальная сеть — виртуальная сеть"

Чтобы легко подключать виртуальные сети, рекомендуем настроить подключение "виртуальная сеть — виртуальная сеть". Подключение "виртуальная сеть — виртуальная сеть" (VNet2VNet) между виртуальными сетями похоже на создание подключения "сеть — сеть" (IPsec) к локальному расположению.  В обоих типах подключений применяется VPN-шлюз для создания защищенного туннеля, использующего IPsec/IKE. При обмене данными оба типа подключений работают одинаково. Различие между этими типами заключается в способе настройки шлюза локальной сети. При создании подключения "виртуальная сеть — виртуальная сеть" диапазон адресов для шлюза локальной сети не отображается. Он создается и заполняется автоматически. Если вы обновите диапазон адресов для одной виртуальной сети, другая виртуальная сеть автоматически определит маршрут к обновленному диапазону адресов. Как правило, намного проще и быстрее создать подключение "виртуальная сеть — виртуальная сеть", чем подключение "сеть — сеть" между виртуальными сетями.

### <a name="site-to-site-ipsec"></a>Подключение "сеть — сеть" (IPsec)

При работе со сложной конфигурацией сети можно подключить виртуальные сети с помощью подключения [сеть — сеть](vpn-gateway-create-site-to-site-rm-powershell.md), а не "виртуальная сеть — виртуальная сеть". Используя подключение "сеть — сеть", вы вручную создаете и настраиваете локальные сетевые шлюзы. Шлюз локальной сети для каждой виртуальной сети воспринимает другую виртуальную сеть как локальный сайт. Это позволит вам указать дополнительный диапазон адресов для шлюза локальной сети, чтобы маршрутизировать трафик. Если диапазон адресов для виртуальной сети изменится, вам потребуется обновить соответствующий шлюз локальной сети, чтобы отразить эти изменения. Он не обновляется автоматически.

### <a name="vnet-peering"></a>Пиринговая связь между виртуальными сетями

Вы можете подключить виртуальные сети с помощью пиринга виртуальных сетей. При пиринге виртуальных сетей не используется VPN-шлюз и применяются другие ограничения. Кроме того, [цены на пиринг виртуальных сетей](https://azure.microsoft.com/pricing/details/virtual-network) рассчитываются не так, как [цены на VPN-шлюз "виртуальная сеть — виртуальная сеть"](https://azure.microsoft.com/pricing/details/vpn-gateway). Дополнительную информацию см. в статье [Пиринговая связь между виртуальными сетями](../virtual-network/virtual-network-peering-overview.md).

## <a name="why"></a>Зачем создавать подключение "виртуальная сеть — виртуальная сеть"

Возможно, вам потребуется подключить виртуальные сети с помощью подключения "виртуальная сеть — виртуальная сеть" по следующим причинам.

* **Межрегиональная географическая избыточность и географическое присутствие**

  * Вы можете настроить собственную георепликацию или синхронизацию с безопасной связью без прохождения трафика через конечные точки с выходом в Интернет.
  * С помощью Azure Load Balancer и диспетчера трафика можно настроить высокодоступную рабочую нагрузку с геоизбыточностью в нескольких регионах Azure. Одним из важных примеров является настройка SQL Always On с группами доступности, распределенными между несколькими регионами Azure.
* **Региональные многоуровневые приложения с изоляцией или административной границей**

  * В одном регионе можно настроить многоуровневые приложения с несколькими виртуальными сетями, которые связаны друг с другом из-за требований к изоляции или административных требований.

Подключение типа "виртуальная сеть — виртуальная сеть" можно комбинировать с многосайтовыми конфигурациями. Это позволяет настраивать топологии сети, совмещающие распределенные подключения с подключениями между виртуальными сетями.

## <a name="steps"></a>Какие действия по созданию подключения "виртуальная сеть — виртуальная сеть" следует использовать?

В этой статье приведены два разных набора действий. Один предназначен для [виртуальных сетей в одной подписке](#samesub), а другой — для [виртуальных сетей в разных подписках](#difsub).
Основное различие между этими наборами заключается том, что во втором случае вам нужно использовать отдельные сеансы PowerShell при настройке подключений к виртуальным сетям, которые принадлежат к разным подпискам. 

Для этого вы можете объединить конфигурации или выбрать ту, с которой предпочитаете работать. Во всех конфигурациях используется подключение "виртуальная сеть — виртуальная сеть". Сетевой трафик передается между виртуальными сетями, которые напрямую подключены друг к другу. В этом руководстве трафик не перенаправляется из TestVNet4 в TestVNet5.

* [Виртуальные сети в рамках одной подписки:](#samesub) в инструкциях по работе с этой конфигурацией используются сети TestVNet1 и TestVNet4.

  ![Схема подключения между виртуальными сетями](./media/vpn-gateway-vnet-vnet-rm-ps/v2vrmps.png)

* [Виртуальные сети в разных подписках:](#difsub) в инструкциях по работе с этой конфигурацией используются сети TestVNet1 и TestVNet5.

  ![Схема подключения между виртуальными сетями](./media/vpn-gateway-vnet-vnet-rm-ps/v2vdiffsub.png)

## <a name="samesub"></a>Подключение виртуальных сетей из одной подписки

### <a name="before-you-begin"></a>Перед началом работы

Прежде всего вам необходимо установить последнюю версию командлетов PowerShell для Azure Resource Manager (4.0 или более позднюю). См. дополнительные сведения об [установке и настройке командлетов Azure PowerShell](/powershell/azure/overview).

### <a name="Step1"></a>Шаг 1. Планирование диапазонов IP-адресов

Далее мы создадим две виртуальные сети с соответствующими конфигурациями и подсетями шлюзов. Затем мы создадим VPN-подключение между двумя виртуальными сетями. В конфигурации сети важно задать диапазоны IP-адресов. Имейте в виду, необходимо убедиться в том, что ни один из диапазонов виртуальных сетей или диапазонов локальных сетей никак не перекрываются. В этих примерах мы не включаем DNS-сервер. Сведения о разрешении имен виртуальных машин см. в [этой статье](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).

В примерах мы используем следующие значения:

**Значения для TestVNet1:**

* Имя виртуальной сети: TestVNet1
* Группа ресурсов: TestRG1
* Расположение: восточная часть США
* TestVNet1: 10.11.0.0/16 и 10.12.0.0/16
* FrontEnd: 10.11.0.0/24
* BackEnd: 10.12.0.0/24
* GatewaySubnet: 10.12.255.0/27
* Имя шлюза: VNet1GW
* Общедоступный IP-адрес: VNet1GWIP
* Тип VPN: RouteBased
* Подключение (1–4): VNet1toVNet4
* Подключение (1–5): VNet1toVNet5 (для виртуальных сетей в разных подписках)
* Тип подключения: VNet2VNet

**Значения для TestVNet4:**

* Имя виртуальной сети: TestVNet4
* TestVNet2: 10.41.0.0/16 и 10.42.0.0/16
* FrontEnd: 10.41.0.0/24
* BackEnd: 10.42.0.0/24
* GatewaySubnet: 10.42.255.0/27
* Группа ресурсов: TestRG4
* Расположение: Запад США
* Имя шлюза: VNet4GW
* Общедоступный IP-адрес: VNet4GWIP
* Тип VPN: RouteBased
* Подключение: VNet4toVNet1
* Тип подключения: VNet2VNet


### <a name="Step2"></a>Шаг 2. Создание и настройка TestVNet1

1. Объявите переменные. В этом примере объявлены переменные со значениями для этого упражнения. В большинстве случаев эти значения вам нужно заменить собственными. Но вы можете использовать указанные переменные, чтобы ознакомиться с этим типом конфигурации. Измените переменные (при необходимости), а затем скопируйте и вставьте их в консоль PowerShell.

  ```powershell
  $Sub1 = "Replace_With_Your_Subcription_Name"
  $RG1 = "TestRG1"
  $Location1 = "East US"
  $VNetName1 = "TestVNet1"
  $FESubName1 = "FrontEnd"
  $BESubName1 = "Backend"
  $GWSubName1 = "GatewaySubnet"
  $VNetPrefix11 = "10.11.0.0/16"
  $VNetPrefix12 = "10.12.0.0/16"
  $FESubPrefix1 = "10.11.0.0/24"
  $BESubPrefix1 = "10.12.0.0/24"
  $GWSubPrefix1 = "10.12.255.0/27"
  $GWName1 = "VNet1GW"
  $GWIPName1 = "VNet1GWIP"
  $GWIPconfName1 = "gwipconf1"
  $Connection14 = "VNet1toVNet4"
  $Connection15 = "VNet1toVNet5"
  ```

2. Подключитесь к учетной записи. Для подключения используйте следующий пример кода:

  ```powershell
  Connect-AzureRmAccount
  ```

  Просмотрите подписки учетной записи.

  ```powershell
  Get-AzureRmSubscription
  ```

  Укажите подписку, которую нужно использовать.

  ```powershell
  Select-AzureRmSubscription -SubscriptionName $Sub1
  ```
3. Создайте новую группу ресурсов.

  ```powershell
  New-AzureRmResourceGroup -Name $RG1 -Location $Location1
  ```
4. Создайте конфигурации подсети для TestVNet1. В этом примере создается виртуальная сеть с именем TestVNet1 и три подсети: GatewaySubnet, FrontEnd и Backend. При замене значений важно, чтобы вы назвали подсеть шлюза именем GatewaySubnet. Если вы используете другое имя, создание шлюза завершится сбоем.

  В следующем примере используются переменные, заданные ранее. В этом примере в подсети шлюза используется длина префикса /27. Хотя можно создать подсеть шлюза размером /29, рекомендуется создать подсеть большего размера, включающую несколько адресов, выбрав по крайней мере значение /28 или /27. Таким образом, у вас будет достаточно адресов, чтобы добавить дополнительные конфигурации в будущем.

  ```powershell
  $fesub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName1 -AddressPrefix $FESubPrefix1
  $besub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName1 -AddressPrefix $BESubPrefix1
  $gwsub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName1 -AddressPrefix $GWSubPrefix1
  ```
5. Создайте TestVNet1.

  ```powershell
  New-AzureRmVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1 `
  -Location $Location1 -AddressPrefix $VNetPrefix11,$VNetPrefix12 -Subnet $fesub1,$besub1,$gwsub1
  ```
6. Запросите выделение общедоступного IP-адреса для шлюза, который будет создан для виртуальной сети. Учтите, что параметр AllocationMethod является динамическим. Указать необходимый IP-адрес нельзя. Он выделяется для шлюза динамически. 

  ```powershell
  $gwpip1 = New-AzureRmPublicIpAddress -Name $GWIPName1 -ResourceGroupName $RG1 `
  -Location $Location1 -AllocationMethod Dynamic
  ```
7. Создайте конфигурацию шлюза. Конфигурация шлюза определяет используемые подсеть и общедоступный IP-адрес. Используйте следующий пример, чтобы создать конфигурацию шлюза.

  ```powershell
  $vnet1 = Get-AzureRmVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1
  $subnet1 = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet1
  $gwipconf1 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfName1 `
  -Subnet $subnet1 -PublicIpAddress $gwpip1
  ```
8. Создайте шлюз для TestVNet1. На этом шаге вы создадите шлюз для виртуальной сети TestVNet1. Для подключений типа VNet-to-VNet необходимо использовать тип VPN RouteBased. Создание шлюза часто занимает 45 минут и более, в зависимости от выбранного SKU шлюза.

  ```powershell
  New-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1 `
  -Location $Location1 -IpConfigurations $gwipconf1 -GatewayType Vpn `
  -VpnType RouteBased -GatewaySku VpnGw1
  ```

### <a name="step-3---create-and-configure-testvnet4"></a>Шаг 3. Создание и настройка TestVNet4

После настройки TestVNet1 создайте TestVNet4. Следуйте инструкциям ниже, заменив при необходимости значения своими. Этот шаг можно выполнять в том же сеансе PowerShell, так как мы работаем в той же подписке.

1. Объявите переменные. Не забудьте заменить значения теми, которые вы хотите использовать для конфигурации.

  ```powershell
  $RG4 = "TestRG4"
  $Location4 = "West US"
  $VnetName4 = "TestVNet4"
  $FESubName4 = "FrontEnd"
  $BESubName4 = "Backend"
  $GWSubName4 = "GatewaySubnet"
  $VnetPrefix41 = "10.41.0.0/16"
  $VnetPrefix42 = "10.42.0.0/16"
  $FESubPrefix4 = "10.41.0.0/24"
  $BESubPrefix4 = "10.42.0.0/24"
  $GWSubPrefix4 = "10.42.255.0/27"
  $GWName4 = "VNet4GW"
  $GWIPName4 = "VNet4GWIP"
  $GWIPconfName4 = "gwipconf4"
  $Connection41 = "VNet4toVNet1"
  ```
2. Создайте новую группу ресурсов.

  ```powershell
  New-AzureRmResourceGroup -Name $RG4 -Location $Location4
  ```
3. Создайте конфигурации подсети для TestVNet4.

  ```powershell
  $fesub4 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName4 -AddressPrefix $FESubPrefix4
  $besub4 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName4 -AddressPrefix $BESubPrefix4
  $gwsub4 = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName4 -AddressPrefix $GWSubPrefix4
  ```
4. Создайте TestVNet4.

  ```powershell
  New-AzureRmVirtualNetwork -Name $VnetName4 -ResourceGroupName $RG4 `
  -Location $Location4 -AddressPrefix $VnetPrefix41,$VnetPrefix42 -Subnet $fesub4,$besub4,$gwsub4
  ```
5. Запросите общедоступный IP-адрес.

  ```powershell
  $gwpip4 = New-AzureRmPublicIpAddress -Name $GWIPName4 -ResourceGroupName $RG4 `
  -Location $Location4 -AllocationMethod Dynamic
  ```
6. Создайте конфигурацию шлюза.

  ```powershell
  $vnet4 = Get-AzureRmVirtualNetwork -Name $VnetName4 -ResourceGroupName $RG4
  $subnet4 = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet4
  $gwipconf4 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfName4 -Subnet $subnet4 -PublicIpAddress $gwpip4
  ```
7. Создание шлюза TestVNet4 Создание шлюза часто занимает 45 минут и более, в зависимости от выбранного SKU шлюза.

  ```powershell
  New-AzureRmVirtualNetworkGateway -Name $GWName4 -ResourceGroupName $RG4 `
  -Location $Location4 -IpConfigurations $gwipconf4 -GatewayType Vpn `
  -VpnType RouteBased -GatewaySku VpnGw1
  ```

### <a name="step-4---create-the-connections"></a>Шаг 4. Создание подключений

1. Получите оба шлюза для виртуальных сетей. Если оба шлюза находятся в одной подписке, как в этом примере, этот шаг можно выполнить в одном сеансе PowerShell.

  ```powershell
  $vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1
  $vnet4gw = Get-AzureRmVirtualNetworkGateway -Name $GWName4 -ResourceGroupName $RG4
  ```
2. Создайте подключение между TestVNet1 и TestVNet4. На этом шаге вы создадите подключение между TestVNet1 и TestVNet4. Вы увидите ссылки на общий ключ в примерах. Можно использовать собственные значения для общего ключа. Важно, чтобы общий ключ в обоих подключениях был одинаковым. Создание подключения может занять некоторое время.

  ```powershell
  New-AzureRmVirtualNetworkGatewayConnection -Name $Connection14 -ResourceGroupName $RG1 `
  -VirtualNetworkGateway1 $vnet1gw -VirtualNetworkGateway2 $vnet4gw -Location $Location1 `
  -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'
  ```
3. Создайте подключение между TestVNet4 и TestVNet1. Этот шаг аналогичен приведенному выше, за исключением того, что создается подключение из сети TestVNet4 в сеть TestVNet1. Убедитесь, что общие ключи совпадают. Подключение установится через несколько минут.

  ```powershell
  New-AzureRmVirtualNetworkGatewayConnection -Name $Connection41 -ResourceGroupName $RG4 `
  -VirtualNetworkGateway1 $vnet4gw -VirtualNetworkGateway2 $vnet1gw -Location $Location4 `
  -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'
  ```
4. Проверьте подключение. См. раздел о [проверке подключения](#verify).

## <a name="difsub"></a>Подключение виртуальных сетей из разных подписок

В этом сценарии мы создадим подключение между TestVNet1 и TestVNet5. TestVNet1 и TestVNet5 находятся в разных подписках. Подписки не обязательно должны быть связаны с одним и тем же клиентом Active Directory. Разница между этими действиями и предыдущими заключается в том, что часть настройки нужно выполнить в отдельном сеансе PowerShell в контексте второй подписки, особенно если две подписки используются разными организациями.

### <a name="step-5---create-and-configure-testvnet1"></a>Шаг 5. Создание и настройка TestVNet1

Чтобы создать и настроить сеть TestVNet1 и VPN-шлюз для нее, необходимо выполнить [шаг 1](#Step1) и [шаг 2](#Step2) из предыдущего раздела. В этой конфигурации не нужно создавать TestVNet4 из предыдущего раздела. Но если вы создадите эту виртуальную сеть, она не помешает при выполнении этих шагов. Выполнив шаг 1 и 2, перейдите к шагу 6, чтобы создать сеть TestVNet5. 

### <a name="step-6---verify-the-ip-address-ranges"></a>Шаг 6. Проверка диапазонов IP-адресов

Важно проследить, чтобы пространство IP-адресов для новой виртуальной сети TestVNet5 не пересекалось с диапазонами других виртуальных сетей или диапазонами шлюзов локальной сети. В этом примере виртуальные сети могут принадлежать разным организациям. В этом упражнении используйте следующие значения для сети TestVNet5.

**Значения для TestVNet5:**

* Имя виртуальной сети: TestVNet5
* Группа ресурсов: TestRG5
* Расположение: восточная часть Японии
* TestVNet5: 10.51.0.0/16 и 10.52.0.0/16
* FrontEnd: 10.51.0.0/24
* BackEnd: 10.52.0.0/24
* GatewaySubnet: 10.52.255.0.0/27
* Имя шлюза: VNet5GW
* Общедоступный IP-адрес: VNet5GWIP
* Тип VPN: RouteBased
* Подключение: VNet5toVNet1
* Тип подключения: VNet2VNet

### <a name="step-7---create-and-configure-testvnet5"></a>Шаг 7. Создание и настройка TestVNet5

Это действие необходимо выполнить в контексте новой подписки. Эту часть может выполнить администратор в другой организации, которой принадлежит подписка.

1. Объявите переменные. Не забудьте заменить значения теми, которые вы хотите использовать для конфигурации.

  ```powershell
  $Sub5 = "Replace_With_the_New_Subcription_Name"
  $RG5 = "TestRG5"
  $Location5 = "Japan East"
  $VnetName5 = "TestVNet5"
  $FESubName5 = "FrontEnd"
  $BESubName5 = "Backend"
  $GWSubName5 = "GatewaySubnet"
  $VnetPrefix51 = "10.51.0.0/16"
  $VnetPrefix52 = "10.52.0.0/16"
  $FESubPrefix5 = "10.51.0.0/24"
  $BESubPrefix5 = "10.52.0.0/24"
  $GWSubPrefix5 = "10.52.255.0/27"
  $GWName5 = "VNet5GW"
  $GWIPName5 = "VNet5GWIP"
  $GWIPconfName5 = "gwipconf5"
  $Connection51 = "VNet5toVNet1"
  ```
2. Подключитесь к подписке 5. Откройте консоль PowerShell и подключитесь к своей учетной записи. Для подключения используйте следующий пример.

  ```powershell
  Connect-AzureRmAccount
  ```

  Просмотрите подписки учетной записи.

  ```powershell
  Get-AzureRmSubscription
  ```

  Укажите подписку, которую нужно использовать.

  ```powershell
  Select-AzureRmSubscription -SubscriptionName $Sub5
  ```
3. Создайте новую группу ресурсов.

  ```powershell
  New-AzureRmResourceGroup -Name $RG5 -Location $Location5
  ```
4. Создайте конфигурации подсети для TestVNet5.

  ```powershell
  $fesub5 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName5 -AddressPrefix $FESubPrefix5
  $besub5 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName5 -AddressPrefix $BESubPrefix5
  $gwsub5 = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName5 -AddressPrefix $GWSubPrefix5
  ```
5. Создайте TestVNet5.

  ```powershell
  New-AzureRmVirtualNetwork -Name $VnetName5 -ResourceGroupName $RG5 -Location $Location5 `
  -AddressPrefix $VnetPrefix51,$VnetPrefix52 -Subnet $fesub5,$besub5,$gwsub5
  ```
6. Запросите общедоступный IP-адрес.

  ```powershell
  $gwpip5 = New-AzureRmPublicIpAddress -Name $GWIPName5 -ResourceGroupName $RG5 `
  -Location $Location5 -AllocationMethod Dynamic
  ```
7. Создайте конфигурацию шлюза.

  ```powershell
  $vnet5 = Get-AzureRmVirtualNetwork -Name $VnetName5 -ResourceGroupName $RG5
  $subnet5  = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet5
  $gwipconf5 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfName5 -Subnet $subnet5 -PublicIpAddress $gwpip5
  ```
8. Создайте шлюз TestVNet5.

  ```powershell
  New-AzureRmVirtualNetworkGateway -Name $GWName5 -ResourceGroupName $RG5 -Location $Location5 `
  -IpConfigurations $gwipconf5 -GatewayType Vpn -VpnType RouteBased -GatewaySku VpnGw1
  ```

### <a name="step-8---create-the-connections"></a>Шаг 8. Создание подключений

Так как шлюзы из нашего примера находятся в разных подписках, мы разделили этот шаг на два сеанса PowerShell, обозначенные как [подписка 1] и [подписка 5].

1. **[Подписка 1]**. Получите шлюз виртуальной сети для подписки 1 Войдите в систему и подключитесь к подписке 1, а затем выполните следующую команду:

  ```powershell
  $vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1
  ```

  Скопируйте выходные данные следующих элементов и отправьте их администратору подписки 5 по электронной почте или другим способом.

  ```powershell
  $vnet1gw.Name
  $vnet1gw.Id
  ```

  Эти два элемента будут иметь значения, похожие на выходные данные в нашем примере:

  ```
  PS D:\> $vnet1gw.Name
  VNet1GW
  PS D:\> $vnet1gw.Id
  /subscriptions/b636ca99-6f88-4df4-a7c3-2f8dc4545509/resourceGroupsTestRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW
  ```
2. **[Подписка 5]**. Получите шлюз виртуальной сети для подписки 5. Войдите в систему и подключитесь к подписке 5, а затем выполните следующую команду:

  ```powershell
  $vnet5gw = Get-AzureRmVirtualNetworkGateway -Name $GWName5 -ResourceGroupName $RG5
  ```

  Скопируйте выходные данные следующих элементов и отправьте их администратору подписки 1 по электронной почте или другим способом.

  ```powershell
  $vnet5gw.Name
  $vnet5gw.Id
  ```

  Эти два элемента будут иметь значения, похожие на выходные данные в нашем примере:

  ```
  PS C:\> $vnet5gw.Name
  VNet5GW
  PS C:\> $vnet5gw.Id
  /subscriptions/66c8e4f1-ecd6-47ed-9de7-7e530de23994/resourceGroups/TestRG5/providers/Microsoft.Network/virtualNetworkGateways/VNet5GW
  ```
3. **[Подписка 1]**. Создайте подключение между TestVNet1 и TestVNet5. На этом шаге вы создадите подключение между TestVNet1 и TestVNet5. Разница здесь заключается в том, что вы не можете напрямую получить $vnet5gw, так как это значение используется в другой подписке. Вам необходимо создать новый объект PowerShell с помощью значений, переданных из подписки 1 на предыдущих этапах. Используйте пример ниже. Замените имя, идентификатор и общий ключ своими значениями. Важно, чтобы общий ключ в обоих подключениях был одинаковым. Создание подключения может занять некоторое время.

  Подключитесь к подписке 1, а затем выполните следующую команду:

  ```powershell
  $vnet5gw = New-Object Microsoft.Azure.Commands.Network.Models.PSVirtualNetworkGateway
  $vnet5gw.Name = "VNet5GW"
  $vnet5gw.Id   = "/subscriptions/66c8e4f1-ecd6-47ed-9de7-7e530de23994/resourceGroups/TestRG5/providers/Microsoft.Network/virtualNetworkGateways/VNet5GW"
  $Connection15 = "VNet1toVNet5"
  New-AzureRmVirtualNetworkGatewayConnection -Name $Connection15 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -VirtualNetworkGateway2 $vnet5gw -Location $Location1 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'
  ```
4. **[Подписка 5]**. Создайте подключение между TestVNet5 и TestVNet1. Этот шаг аналогичен приведенному выше, за исключением того, что создается подключение из сети TestVNet5 в сеть TestVNet1. На этом шаге процесс создания объекта PowerShell аналогичный, но с использованием значений, полученных из подписки 1. Проследите на этом шаге, чтобы общие ключи совпадали.

  Подключитесь к подписке 5, а затем выполните следующую команду:

  ```powershell
  $vnet1gw = New-Object Microsoft.Azure.Commands.Network.Models.PSVirtualNetworkGateway
  $vnet1gw.Name = "VNet1GW"
  $vnet1gw.Id = "/subscriptions/b636ca99-6f88-4df4-a7c3-2f8dc4545509/resourceGroups/TestRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW "
  $Connection51 = "VNet5toVNet1"
  New-AzureRmVirtualNetworkGatewayConnection -Name $Connection51 -ResourceGroupName $RG5 -VirtualNetworkGateway1 $vnet5gw -VirtualNetworkGateway2 $vnet1gw -Location $Location5 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'
  ```

## <a name="verify"></a>Проверка подключения

[!INCLUDE [vpn-gateway-no-nsg-include](../../includes/vpn-gateway-no-nsg-include.md)]

[!INCLUDE [verify connections powershell](../../includes/vpn-gateway-verify-connection-ps-rm-include.md)]

## <a name="faq"></a>Часто задаваемые вопросы о подключениях типа "виртуальная сеть — виртуальная сеть"

[!INCLUDE [vpn-gateway-vnet-vnet-faq](../../includes/vpn-gateway-faq-vnet-vnet-include.md)]

## <a name="next-steps"></a>Дополнительная информация

* Установив подключение, можно добавить виртуальные машины в виртуальные сети. Дополнительную информацию см. в [документации по виртуальным машинам](https://docs.microsoft.com/azure/#pivot=services&panel=Compute).
* Сведения о BGP см. в статьях [Обзор использования BGP с VPN-шлюзами Azure](vpn-gateway-bgp-overview.md) и [Настройка BGP на VPN-шлюзах Azure с помощью Azure Resource Manager и PowerShell](vpn-gateway-bgp-resource-manager-ps.md).

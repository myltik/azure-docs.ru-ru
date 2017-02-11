---
title: "Инфраструктура и подключение к SAP HANA в Azure (крупные экземпляры) | Документация Майкрософт"
description: "Настройте требуемую инфраструктуру для подключения, чтобы использовать SAP HANA в Azure (крупные экземпляры)."
services: virtual-machines-linux
documentationcenter: 
author: RicksterCDN
manager: timlt
editor: 
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/01/2016
ms.author: rclaus
translationtype: Human Translation
ms.sourcegitcommit: a3e99cf86cbe6ae18366b13047d8c2994ff398f4
ms.openlocfilehash: 9adf67b706f866fbb41c2b82970eb0e44aada5c6


---
# <a name="infrastructure-and-connectivity-to-sap-hana-on-azure-large-instances"></a>Инфраструктура и подключение к SAP HANA в Azure (крупные экземпляры)

После завершения процесса покупки SAP HANA в Azure (крупные экземпляры) со службой поддержки корпоративных учетных записей Майкрософт необходимо указать следующее:

- Имя клиента.
- Корпоративные контактные сведения (адрес электронной почты и номер телефона).
- Технические контактные сведения (адрес электронной почты и номер телефона).
- Технические сетевые контактные сведения (адрес электронной почты и номер телефона).
- Регион развертывания Azure (по состоянию на сентябрь 2016 г. западная или восточная часть США).
- Подтверждение номера SKU SAP HANA в Azure (крупные экземпляры) (конфигурация).
- Для каждого региона Azure, в котором выполняется развертывание:
  - диапазон IP-адресов с маской /29 для одноранговых подключений;
  - блок CIDR (используется для пула NAT крупных экземпляров HANA, мы рекомендуем использовать маску /24).
- Для каждой виртуальной сети Azure, подключающейся к крупным экземплярам HANA, независимо от региона Azure:
  - один или несколько диапазонов IP-адресов с маской /28 или /27 (для подсети шлюза виртуальной сети клиента);
  - один или несколько блоков CIDR (для подсети клиента виртуальной сети клиента, мы рекомендуем маску /24).
- Данные для каждой системы крупных экземпляров HANA:
  - требуемое имя узла;
  - требуемый IP-адрес из пула NAT.
- Номер для подписки Azure, к которой будет напрямую подключаться SAP HANA в Azure (крупные экземпляры).
- Имя ИД безопасности SAP HANA для экземпляра SAP HANA (требуется для создания необходимых томов дисков SAP HANA).

После того как вы укажете эти сведения, специалисты корпорации Майкрософт подготовят SAP HANA в Azure (крупные экземпляры).

Затем вам будут предоставлены сведения о настройке сети. С их помощью можно сделать следующее:

- Подключить виртуальные сети Azure к каналу ExpressRoute, по которому они подключаются к крупным экземплярам HANA.
  - Для Azure Resource Manager:
     - ключи авторизации;
     - код узла ExpressRoute.
- Получить доступ к крупным экземплярам HANA по настроенному каналу ExpressRoute и виртуальной сети Azure.

## <a name="creating-an-azure-vnet"></a>Создание виртуальной сети Azure

Эту виртуальную сеть Azure следует создать с использованием модели развертывания с помощью Azure Resource Manager. Это решение не поддерживает старую модель развертывания Azure, известную как ASM.

В создаваемой виртуальной сети Azure должна быть по крайней мере одна подсеть клиента и шлюза. Им нужно назначить диапазоны IP-адресов в соответствии со сведениями, отправленными в корпорацию Майкрософт.

> [!IMPORTANT] 
> Виртуальной сети в подписке Azure следует назначать только блоки адресов клиента и шлюза. Блоки адресов пула P2P и NAT не должны быть связаны с адресными пространствами виртуальной сети и подсети, так как они находятся за пределами подписки Azure.

Вы можете использовать несколько подсетей клиентов (используя не смежные диапазоны адресов). Однако, как упоминалось ранее, их нужно отправить в корпорацию Майкрософт заранее.

Для этих подсетей клиента можно использовать любой стандарт именования. Тем не менее **каждой виртуальной подсети должна соответствовать только одна подсеть шлюза**, подключающаяся к каналу ExpressRoute для SAP HANA в Azure (крупные экземпляры). **Эту подсеть шлюза нужно называть &quot;GatewaySubnet&quot;**, чтобы обеспечить правильное размещение шлюза ExpressRoute.

> [!WARNING] 
> Крайне важно, чтобы подсеть шлюза называлась &quot;GatewaySubnet&quot;.

Виртуальную сеть можно создать с помощью портала Azure, PowerShell, шаблона Azure или Azure CLI (см. статью [Создание виртуальной сети с помощью портала Azure](../../virtual-network/virtual-networks-create-vnet-arm-pportal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)).

## <a name="creating-a-gateway-subnet"></a>Создание подсети шлюза

После создания виртуальной сети Azure в ней нужно создать шлюз ExpressRoute, чтобы связать эту виртуальную сеть с каналом ExpressRoute, подключающимся к клиенту пользователя по метке крупного экземпляра.

> [!NOTE] 
> Это может занять до 30 минут, так как при этом создается шлюз в обозначенной подписке Azure, который затем подключается к указанной виртуальной сети Azure.

Если шлюз уже существует, проверьте, является ли он шлюзом ExpressRoute. В противном случае шлюз необходимо удалить и повторно создать в качестве шлюза ExpressRoute. Если шлюз ExpressRoute уже установлен, а виртуальная сеть Azure подключена к каналу ExpressRoute для локального подключения, перейдите к разделу "Связывание виртуальных сетей" ниже.

- Используйте новый [портал Azure](https://portal.azure.com/) или PowerShell, чтобы создать VPN-шлюз ExpressRoute, подключенный к виртуальной сети.
  - Если вы используете портал Azure, добавьте новый **шлюз виртуальной сети**, а затем в качестве типа шлюза выберите **ExpressRoute**.
  - Если же вы выбрали PowerShell, сначала скачайте и используйте последнюю версию [пакета SDK для Azure PowerShell](https://azure.microsoft.com/downloads/) для оптимальной работы. Следующие команды создают шлюз ExpressRoute. Текст, перед которым стоит символ _$_ — это определяемые пользователем переменные, которые нужно обновить, используя ваши сведения.

```
# These Values should already exist, update to match your environment
$myAzureRegion = "eastus"
$myGroupName = "SAP-East-Coast"
$myVNetName = "VNet01"

# These values are used to create the gateway, update for how you wish the GW components to be named
$myGWName = "VNet01GW"
$myGWConfig = "VNet01GWConfig"
$myGWPIPName = "VNet01GWPIP"
$myGWSku = "HighPerformance" # Supported values for HANA Large Instances are: HighPerformance or UltraPerformance

# These Commands create the Public IP and ExpressRoute Gateway
$vnet = Get-AzureRmVirtualNetwork -Name $myVNetName -ResourceGroupName $myGroupName
$subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
New-AzureRmPublicIpAddress -Name $myPIPName -ResourceGroupName $myGroupName `
-Location $myAzureRegion -AllocationMethod Dynamic
$gwpip = Get-AzureRmPublicIpAddress -Name $myPIPName -ResourceGroupName $myGroupName
$gwipconfig = New-AzureRmVirtualNetworkGatewayIpConfig -Name $myGWConfig -SubnetId $subnet.Id `
-PublicIpAddressId $gwpip.Id

New-AzureRmVirtualNetworkGateway -Name $myGWName -ResourceGroupName $myGroupName -Location $myAzureRegion `
-IpConfigurations $gwipconfig -GatewayType ExpressRoute `
-GatewaySku $myGWSku -VpnType PolicyBased -EnableBgp $true
```

В этом примере использовался номер SKU шлюза HighPerformance. Имеются следующие варианты: HighPerformance или UltraPerformance. Это единственные номера SKU шлюза, которые поддерживает SAP HANA в Azure (крупные экземпляры).

## <a name="linking-vnets"></a>Связывание виртуальных сетей

Теперь, когда для виртуальной сети Azure создан шлюз ExpressRoute, используйте сведения об авторизации, предоставленные корпорацией Майкрософт, для подключения шлюза ExpressRoute к каналу ExpressRoute SAP HANA в Azure (крупные экземпляры), созданного именно для этого. Это можно сделать только с помощью PowerShell (сейчас эта возможность не поддерживается на портале Azure).

- Сделайте следующее для каждого шлюза виртуальной сети, используя разные AuthGUID для каждого подключения. Первые две записи, приведенные ниже, получены из сведений, предоставленных корпорацией Майкрософт. Кроме того, каждый AuthGUID связан с конкретной виртуальной сетью и шлюзом.

```
# Populate with information provided by Microsoft Onboarding team
$PeerID = "/subscriptions/9cb43037-9195-4420-a798-f87681a0e380/resourceGroups/Customer-USE-Circuits/providers/Microsoft.Network/expressRouteCircuits/Customer-USE01-5Gb"
$AuthGUID = "76d40466-c458-4d14-adcf-3d1b56d1cd61"

# Your ExpressRoute Gateway Information
$myGroupName = "SAP-East-Coast"
$myGWName = "VNet01GW"
$myGWLocation = "East US"

# Define the name for your connection
$myConnectionName = "VNet01GWConnection"

# Create a new connection between the ER Circuit and your Gateway using the Authorization
$gw = Get-AzureRmVirtualNetworkGateway -Name $myGWName -ResourceGroupName $myGroupName
    
New-AzureRmVirtualNetworkGatewayConnection -Name $myConnectionName `
-ResourceGroupName $myGroupName -Location $myGWLocation -VirtualNetworkGateway1 $gw `
-PeerId $PeerID -ConnectionType ExpressRoute -AuthorizationKey $AuthGUID
```

Возможно, вам потребуется сделать это несколько раз, если вы хотите подключить шлюз к нескольким разным каналам ExpressRoute, связанным с вашей подпиской.

## <a name="adding-more-ip-addresses-or-subnets"></a>Добавление дополнительных IP-адресов или подсетей

При добавлении дополнительных IP-адресов или подсетей используйте портал Azure, PowerShell или интерфейс командной строки.

Если вы еще не объявили адресное пространство диапазона дополнительных IP-адресов в решении "SAP HANA для управления службами Azure", откройте запрос на поддержку Azure, чтобы добавить его. Получив подтверждение, выполните следующие шаги.

Чтобы создать дополнительные подсети с помощью портала Azure, см. статью [Создание виртуальной сети с помощью портала Azure](../../virtual-network/virtual-networks-create-vnet-arm-pportal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json), а для создания с помощью PowerShell см. статью [Создание виртуальной сети с помощью PowerShell](../../virtual-network/virtual-networks-create-vnet-arm-ps.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="adding-vnets"></a>Добавление виртуальных сетей

Подключив одну или несколько виртуальных сетей Azure, вы, возможно, захотите добавить дополнительные виртуальные сети, которые обращаются к SAP HANA в Azure (крупные экземпляры). Сначала отправьте запрос на поддержку Azure и укажите в нем сведения о конкретном развертывании Azure и адресное пространство диапазона IP-адресов для подсетей клиента и шлюза для дополнительных виртуальных сетей Azure. Затем специалисты службы поддержки "SAP HANA для управления службами Azure" предоставят сведения, необходимые для подключения дополнительных виртуальных сетей и ExpressRoute.

Процедура добавления новой виртуальной сети Azure

1. Выполните первый шаг в процессе внедрения (см. раздел _Создание виртуальной сети Azure_ выше).
2. Выполните второй шаг в процессе внедрения (см. раздел _Создание подсети шлюза_ выше).
3. Откройте запрос на поддержку Azure, указав сведения о новой виртуальной сети, и запросите новый ключ авторизации для подключения дополнительных виртуальных сетей к каналу ExpressRoute крупных экземпляров HANA.
4. Получив уведомление о завершении авторизации, используйте сведения об авторизации, предоставленные корпорацией Майкрософт, чтобы выполнить третий шаг в процессе внедрения (см. раздел _Связывание виртуальных сетей_ выше).

## <a name="increasing-expressroute-circuit-bandwidth"></a>Повышение пропускной способности канала ExpressRoute

Обратитесь в службу поддержки решения "SAP HANA для управления службами Azure". Если вам порекомендовали увеличить пропускную способность канала ExpressRoute SAP HANA в Azure (крупные экземпляры), создайте запрос на поддержку Azure. (Вы можете отправить запрос о повышении пропускной способности одного канала до максимального показателя — 10 Гбит/с.) После завершения операции вы получите уведомление. Чтобы таким образом повысить производительность в Azure, не нужно больше ничего делать.

## <a name="adding-an-additional-expressroute-circuit"></a>Добавление дополнительного канала ExpressRoute

Обратитесь в службу поддержки решения "SAP HANA для управления службами Azure", если вам порекомендовали добавить дополнительный канал ExpressRoute. Отправьте запрос на поддержку Azure для создания канала ExpressRoute (а также для получения сведений об авторизации для подключения к нему). Перед этим определите адресное пространство, которое будет использоваться в виртуальной сети, чтобы специалисты службы поддержки решения "SAP HANA для управления службами Azure" предоставили вам возможность авторизации.

После создания канала и завершения настройки решения "SAP HANA для управления службами Azure" вы получите уведомление со сведениями, необходимыми, чтобы продолжить. Выполните шаги, описанные выше, чтобы создать виртуальную сеть и подключить ее к этому дополнительному каналу. Вы не сможете подключить виртуальные сети Azure к этому дополнительному каналу, если они уже подключены к другому каналу ExpressRoute.

## <a name="deleting-a-subnet"></a>Удаление подсети

Чтобы удалить подсеть виртуальной сети, можно использовать портал Azure, PowerShell или интерфейс командной строки. При удалении адресного пространства следует сообщить в службу поддержки решения "SAP HANA для управления службами Azure" об изменении адресного пространства адресов, чтобы его можно было удалить из диапазонов, которые можно использовать для SAP HANA в Azure (крупные экземпляры).

Хотя на сайте Azure.com нет конкретных рекомендаций по удалению подсетей, этот процесс противоположен процессу добавления. Дополнительные сведения о создании подсетей см. в статье [Создание виртуальной сети с помощью портала Azure](../../virtual-network/virtual-networks-create-vnet-arm-pportal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) на портале Azure.

## <a name="deleting-a-vnet"></a>Удаление виртуальной сети

При удалении виртуальной сети используйте портал Azure, PowerShell или интерфейс командной строки. Специалисты службы поддержки решения "SAP HANA для управления службами Azure" удаляют существующую авторизацию для канала ExpressRoute HANA SAP в Azure (крупные экземпляры), а также удаляют диапазоны IP-адресов (для шлюза и клиента) для взаимодействия с крупными экземплярами HANA.

После удаления виртуальной сети откройте запрос на поддержку Azure, чтобы указать адресные пространства диапазона IP-адресов, которые следует удалить.

Хотя на сайте Azure.com нет конкретных рекомендаций по удалению виртуальных сетей, этот процесс противоположен процессу добавления (описано выше). Дополнительные сведения о создании виртуальных сетей см. в статьях [Создание виртуальной сети с помощью портала Azure](../../virtual-network/virtual-networks-create-vnet-arm-pportal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) и [Создание виртуальной сети с помощью PowerShell](../../virtual-network/virtual-networks-create-vnet-arm-ps.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Чтобы убедиться, что все удалено, удалите следующие компоненты:

- **Для Azure Resource Manager:** подключение ExpressRoute, шлюз виртуальной сети, общедоступный IP-адрес шлюза виртуальной сети и виртуальную сеть.
- **Для классической виртуальной машины:** шлюз виртуальной сети и виртуальную сеть.

## <a name="deleting-an-expressroute-circuit"></a>Удаление канала ExpressRoute

Чтобы удалить дополнительный канал ExpressRoute SAP HANA в Azure (крупные экземпляры), откройте запрос на поддержку Azure в службу поддержки решения "SAP HANA для управления службами Azure" и укажите, что нужно удалить канал. В подписке Azure вы можете по желанию удалить или оставить виртуальную сеть. Тем не менее необходимо удалить подключение (при использовании Azure Resource Manager) или удалить связь (при использовании классической модели) между каналом ExpressRoute крупных экземпляров HANA и связанным шлюзом виртуальной сети.

Если требуется также удалить виртуальную сеть, следуйте соответствующим указаниям в предыдущем разделе.





<!--HONumber=Dec16_HO2-->



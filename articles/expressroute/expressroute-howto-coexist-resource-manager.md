<properties
   pageTitle="Настройка параллельных подключений ExpressRoute и VPN-подключений типа ";сеть — сеть"; для модели развертывания Resource Manager | Microsoft Azure"
   description="В этой статье описывается настройка параллельных подключений ExpressRoute и VPN-подключений типа ";сеть — сеть"; для модели развертывания Resource Manager."
   documentationCenter="na"
   services="expressroute"
   authors="charwen"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="05/04/2016"
   ms.author="charleywen"/>

# Настройка параллельных подключений ExpressRoute и "сеть — сеть" для модели развертывания Resource Manager

> [AZURE.SELECTOR]
- [PowerShell — Resource Manager](expressroute-howto-coexist-resource-manager.md)
- [PowerShell — классическая модель](expressroute-howto-coexist-classic.md)

Возможность настройки VPN типа "сеть-сеть" и ExpressRoute дает целый ряд преимуществ. Вы можете настроить VPN типа "сеть — сеть" как защищенный путь отработки отказа для ExressRoute или использовать эту VPN для подключения к сайтам, не подключенным через ExpressRoute. В этой статье мы рассмотрим порядок действия в каждом из этих вариантов. Эта статья посвящена модели развертывания Resource Manager. Эта конфигурация недоступна на портале Azure.


**О моделях развертывания Azure**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

>[AZURE.IMPORTANT] Перед выполнением приведенных ниже инструкций необходимо настроить каналы ExpressRoute. Прежде чем следовать дальше, выполните инструкции по [созданию канала ExpressRoute](expressroute-howto-circuit-arm.md) и [настройке маршрутизации](expressroute-howto-routing-arm.md).

## Квоты и ограничения

- **Транзитная маршрутизация не поддерживается.** Нельзя настроить маршрутизацию (через Azure) между локальной сетью, подключенной к VPN типа "сеть — сеть", и локальной сетью, подключенной к ExpressRoute.
- **Принудительное туннелирование нельзя включить в VPN-шлюзе типа "сеть — сеть".** Весь интернет-трафик можно направить в локальную сеть через ExpressRoute только "принудительно". 
- **Только стандартные или высокопроизводительные шлюзы.** Для шлюза ExpressRoute и шлюза VPN типа "сеть — сеть" необходимо использовать стандартный шлюз или высокопроизводительный шлюз. Сведения о номерах SKU шлюзов см. в разделе [Номера SKU шлюзов](../vpn-gateway/vpn-gateway-about-vpngateways.md).
- **Только VPN-шлюз на основе маршрутов.** Необходимо использовать VPN-шлюз на основе маршрутов. Информацию о VPN-шлюзе на основе маршрутов см. в статье о [VPN-шлюзах](../vpn-gateway/vpn-gateway-about-vpngateways.md).
- **Требуется статическая маршрутизация.** Если локальная сеть подключена и к ExpressRoute, и к VPN типа "сеть — сеть", необходимо использовать статический маршрут, настроенный в локальной сети для маршрутизации VPN-подключения типа "сеть — сеть" к Интернету.
- **Шлюз ExpressRoute необходимо настроить в первую очередь.** Шлюз ExpressRoute необходимо создать перед добавлением шлюза VPN типа "сеть — сеть".


## Схемы конфигурации

### Настройка VPN типа "сеть-сеть" как пути отработки отказа для ExpressRoute

VPN-подключение типа "сеть-сеть" можно настроить как службу архивации для ExpressRoute. Это относится только к виртуальным сетям, привязанным к пути пиринга Azure Private. Для служб, доступ к которым осуществляется через пиринг Azure Public или Microsoft, решения для отработки отказа на основе VPN не существует. Канал ExpressRoute всегда является основной ссылкой. Данные передаются по пути VPN типа "сеть-сеть" только в том случае, если канал ExpressRoute не справляется с этой задачей.

![Существуют одновременно](media/expressroute-howto-coexist-resource-manager/scenario1.jpg)

### Настройка VPN типа "сеть-сеть" для подключения к сайтам, не подключенным через ExpressRoute

Сети можно настроить таким образом, чтобы одни из них подключались непосредственно к Azure по VPN типа "сеть-сеть", а другие — через ExpressRoute.

![Существуют одновременно](media/expressroute-howto-coexist-resource-manager/scenario2.jpg)

>[AZURE.NOTE] Настроить виртуальную сеть как транзитный маршрутизатор нельзя.

## Выбор действий для использования

Существует два различных набора процедур для настройки сосуществующих подключений. Выбор процедуры настройки зависит от того, существует ли уже виртуальная сеть, к которой необходимо подключиться, или требуется создать новую виртуальную сеть.


- У меня нет виртуальной сети, и мне нужно ее создать.
	
	Если у вас еще нет виртуальной сети, выполните эти шаги, чтобы создать ее с помощью модели развертывания Resource Manager, а также создать новое подключение ExpressRoute и VPN-подключение типа "сеть — сеть". Для настройки выполните действия из раздела [Создание новой виртуальной сети и параллельных подключений](#new).

- У меня уже есть виртуальная сеть с моделью развертывания Resource Manager.

	Возможно, у вас уже имеется виртуальная сеть, а также подключения к VPN типа "сеть-сеть" или к ExpressRoute. В разделе [Настройка параллельных подключений для существующей виртуальной сети](#add) описано, как удалить шлюз, а затем создать новое подключение ExpressRoute и VPN-подключение типа "сеть — сеть". Обратите внимание, что при создании новых подключений, необходимо выполнять действия в строго определенном порядке. При создании шлюзов и подключений не пользуйтесь инструкциями, взятыми из других статей.

	В этой процедуре для создания одновременно существующих подключений потребуется удалить имеющийся шлюз, а затем настроить новые шлюзы. Это означает, что у вас будет перерыв в работе подключений между организациями, на время удаления и повторного создания шлюза и подключений, но вам не потребуется выполнять миграцию всех виртуальных машин и служб в новую виртуальную сеть. Виртуальные машины и службы смогут по-прежнему осуществлять связь через балансировщик нагрузки во время настройки шлюза, если они настроены соответствующим образом.


## <a name="new"></a>Создание новой виртуальной сети и параллельных подключений

В этой процедуре подробно рассматривается создание виртуальной сети, а также параллельных подключений ExpressRoute и "сеть-сеть".
	
1. Вам потребуется установить последнюю версию командлетов Azure PowerShell. Дополнительные сведения об установке командлетов PowerShell см. в статье [Установка и настройка Azure PowerShell](../powershell-install-configure.md). Обратите внимание, что командлеты, которые будут использоваться для этой конфигурации, могут немного отличаться от знакомых вам. Обязательно используйте командлеты, указанные в инструкциях.

2. Войдите в свою учетную запись и настройте среду.
	
		login-AzureRmAccount
		Select-AzureRmSubscription -SubscriptionName 'yoursubscription'
		$location = "Central US"
		$resgrp = New-AzureRmResourceGroup -Name "ErVpnCoex" -Location $location

3. Создайте виртуальную сеть и подсеть шлюза. Дополнительные сведения о настройке виртуальной сети см. в статье [о конфигурации виртуальной сети Azure](../virtual-network/virtual-networks-create-vnet-arm-ps.md).

	>[AZURE.IMPORTANT] Подсеть шлюза должна иметь префикс /27 или более короткий префикс (например, /26 или /25).
	
	Создайте виртуальную сеть.

		$vnet = New-AzureRmVirtualNetwork -Name "CoexVnet" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -AddressPrefix "10.200.0.0/16" 

	Добавьте подсети.

		Add-AzureRmVirtualNetworkSubnetConfig -Name "App" -VirtualNetwork $vnet -AddressPrefix "10.200.1.0/24"
		Add-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet -AddressPrefix "10.200.255.0/24"

	Сохраните конфигурацию виртуальной сети.

		$vnet = Set-AzureRmVirtualNetwork -VirtualNetwork $vnet

4. <a name="gw"></a>Создайте шлюз ExpressRoute. См. дополнительные сведения [о конфигурации шлюза ExpressRoute](expressroute-howto-add-gateway-resource-manager.md). Параметр GatewaySKU может иметь значение *Standard* или *HighPerformance*.

		$gwSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet
		$gwIP = New-AzureRmPublicIpAddress -Name "ERGatewayIP" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -AllocationMethod Dynamic
		$gwConfig = New-AzureRmVirtualNetworkGatewayIpConfig -Name "ERGatewayIpConfig" -SubnetId $gwSubnet.Id -PublicIpAddressId $gwIP.Id
		$gw = New-AzureRmVirtualNetworkGateway -Name "ERGateway" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -IpConfigurations $gwConfig -GatewayType "ExpressRoute" -GatewaySku Standard 

5. Свяжите шлюз ExpressRoute с каналом ExpressRoute. После выполнения этого действия подключение локальной сети к Azure через ExpressRoute будет установлено. Дополнительные сведения об операции связывания см. в статье [Связывание виртуальных сетей с каналами ExpressRoute](expressroute-howto-linkvnet-arm.md).

		$ckt = Get-AzureRmExpressRouteCircuit -Name "YourCircuit" -ResourceGroupName "YourCircuitResourceGroup"
		New-AzureRmVirtualNetworkGatewayConnection -Name "ERConnection" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -VirtualNetworkGateway1 $gw -PeerId $ckt.Id -ConnectionType ExpressRoute

6. <a name="vpngw"></a>Далее создайте VPN-шлюз типа "сеть — сеть". Дополнительные сведения о настройке VPN-шлюза см. в статье [Настройка подключения между виртуальными сетями с помощью Azure Resource Manager и PowerShell](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md). Параметр GatewaySKU может иметь значение *Standard* или *HighPerformance*. Параметр VpnType должен иметь значение *RouteBased*.

		$gwSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet
		$gwIP = New-AzureRmPublicIpAddress -Name "VPNGatewayIP" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -AllocationMethod Dynamic
		$gwConfig = New-AzureRmVirtualNetworkGatewayIpConfig -Name "VPNGatewayIpConfig" -SubnetId $gwSubnet.Id -PublicIpAddressId $gwIP.Id
		New-AzureRmVirtualNetworkGateway -Name "VPNGateway" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -IpConfigurations $gwConfig -GatewayType "Vpn" -VpnType "RouteBased" -GatewaySku "Standard"

7. Создайте сущность VPN-шлюза локального сайта. Эта команда не настраивает локальный VPN-шлюз. Она только позволяет указать параметры локального шлюза, такие как общедоступный IP-адрес и локальное адресное пространство, чтобы VPN-шлюз Azure мог подключиться к нему.
	>[AZURE.NOTE] Если локальная сеть имеет несколько маршрутов, их все можно передать как массив. $MyLocalNetworkAddress = @("10.100.0.0/16","10.101.0.0/16","10.102.0.0/16")

		$localVpn = New-AzureRmLocalNetworkGateway -Name "LocalVPNGateway" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -GatewayIpAddress *<Public IP>* -AddressPrefix '10.100.0.0/16'

8. Настройте локальное VPN-устройство для подключения к новому VPN-шлюзу Azure. Дополнительные сведения о настройке VPN-устройства см. в разделе [Настройка VPN-устройства](../vpn-gateway/vpn-gateway-about-vpn-devices.md).

9. Свяжите VPN-шлюз к сети типа "сеть-сеть" в Azure с локальным шлюзом.

		$azureVpn = Get-AzureRmVirtualNetworkGateway -Name "VPNGateway" -ResourceGroupName $resgrp.ResourceGroupName
		New-AzureRmVirtualNetworkGatewayConnection -Name "VPNConnection" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -VirtualNetworkGateway1 $azureVpn -LocalNetworkGateway2 $localVpn -ConnectionType IPsec -SharedKey <yourkey>


## <a name="add"></a>Настройка параллельных подключений для существующей виртуальной сети

При наличии существующей виртуальной сети проверьте размер подсети шлюза. Если размер подсети шлюза — /28 или /29, необходимо сначала удалить шлюз виртуальной сети и увеличить размер подсети шлюза. В этом разделе показано, как это сделать.

Если размер подсети шлюза — /27 или больше, а виртуальная сеть подключается с помощью ExpressRoute, можно пропустить описанные ниже шаги и перейти к шагу 6 ([Создание VPN-шлюза подключения типа "сеть — сеть"](#vpngw)) в предыдущем разделе.

>[AZURE.NOTE] При удалении существующего шлюза локальные пользователи потеряют подключение к виртуальной сети на время выполнения этой настройки.

1. Вам потребуется установить последнюю версию командлетов Azure PowerShell. Дополнительную информацию об установке командлетов PowerShell см. в статье [Установка и настройка Azure PowerShell](../powershell-install-configure.md). Обратите внимание, что командлеты, которые будут использоваться для этой конфигурации, могут немного отличаться от знакомых вам. Обязательно используйте командлеты, указанные в инструкциях. 

2. Удалите для сети типа "сеть — сеть" существующий VPN-шлюз или шлюз ExpressRoute.

		Remove-AzureRmVirtualNetworkGateway -Name <yourgatewayname> -ResourceGroupName <yourresourcegroup>

3. Удалите подсеть шлюза.
		
		$vnet = Get-AzureRmVirtualNetwork -Name <yourvnetname> -ResourceGroupName <yourresourcegroup> 
		Remove-AzureRmVirtualNetworkSubnetConfig -Name GatewaySubnet -VirtualNetwork $vnet

4. Добавьте подсеть шлюза с префиксом /27 или больше.
	>[AZURE.NOTE] Если в виртуальной сети недостаточно свободных IP-адресов для увеличения размера подсети шлюза, необходимо добавить дополнительные пространства IP-адресов.

		$vnet = Get-AzureRmVirtualNetwork -Name <yourvnetname> -ResourceGroupName <yourresourcegroup>
		Add-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet -AddressPrefix "10.200.255.0/24"

	Сохраните конфигурацию виртуальной сети.

		$vnet = Set-AzureRmVirtualNetwork -VirtualNetwork $vnet

5. На этом этапе вы будете иметь виртуальную сеть без шлюзов. Чтобы создать новые шлюзы и завершить подключение, перейдите к шагу 4 ([Создайте шлюз ExpressRoute](#gw)) из предыдущего раздела.

## Добавление конфигурации "точка — сеть" к VPN-шлюзу
Чтобы добавить конфигурацию "точка — сеть" к VPN-шлюзу при настройке параллельных подключений, выполните следующее.

1. Добавьте пул адресов VPN-клиента. 

		$azureVpn = Get-AzureRmVirtualNetworkGateway -Name "VPNGateway" -ResourceGroupName $resgrp.ResourceGroupName
		Set-AzureRmVirtualNetworkGatewayVpnClientConfig -VirtualNetworkGateway $azureVpn -VpnClientAddressPool "10.251.251.0/24"

2. Отправьте корневой сертификат VPN в Azure для VPN-шлюза. В этом примере подразумевается, что корневой сертификат хранится на локальном компьютере, где выполняются следующие командлеты PowerShell.

		$p2sCertFullName = "RootErVpnCoexP2S.cer"
		$p2sCertMatchName = "RootErVpnCoexP2S"
		$p2sCertToUpload=get-childitem Cert:\CurrentUser\My | Where-Object {$_.Subject -match $p2sCertMatchName}
		if ($p2sCertToUpload.count -eq 1){
		    write-host "cert found"
		} else {
		    write-host "cert not found"
		    exit
		} 
		$p2sCertData = [System.Convert]::ToBase64String($p2sCertToUpload.RawData)
		Add-AzureRmVpnClientRootCertificate -VpnClientRootCertificateName $p2sCertFullName -VirtualNetworkGatewayname $azureVpn.Name -ResourceGroupName $resgrp.ResourceGroupName -PublicCertData $p2sCertData

Дополнительные сведения см. в статье [Настройка подключения типа "точка — сеть" к виртуальной сети с помощью PowerShell](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md).

## Дальнейшие действия

Дополнительную информацию об ExpressRoute см. в статье [Часто задаваемые вопросы об ExpressRoute](expressroute-faqs.md).

<!---HONumber=AcomDC_0511_2016-->
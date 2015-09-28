<properties
   pageTitle="Создание виртуальной сети с VPN-подключением типа ";сеть-сеть"; с помощью диспетчера ресурсов Azure и PowerShell | Microsoft Azure"
   description="В этой статье поэтапно описывается создание виртуальной сети с помощью диспетчера ресурсов и ее подключение к локальной сети с помощью подключения типа «сеть —сеть» через VPN-шлюз. В ней также содержатся инструкции по изменению префиксов IP-адресов для существующих локальных сайтов."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carolz"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/14/2015"
   ms.author="cherylmc"/>

# Создание виртуальной сети с VPN-подключением типа «сеть —сеть» с помощью PowerShell

> [AZURE.SELECTOR]
- [Azure portal](vpn-gateway-site-to-site-create.md)
- [PowerShell - Resource Manager](vpn-gateway-create-site-to-site-rm-powershell.md)

В этой статье мы покажем вам, как создать виртуальную сеть, соединенную с локальною сетью через VPN-подключение типа «сеть — сеть», с использованием модели развертывания, в которой применяется диспетчер ресурсов Azure.

>[AZURE.IMPORTANT]Обратите внимание, что в настоящее время Azure поддерживает две модели развертывания: классическую и с диспетчером ресурсов. Перед началом настройки внимательно изучите модели и средства развертывания. Сведения о моделях развертывания можно найти в статье [Модели развертывания Azure](../azure-classic-rm.md).

Вы можете использовать расположенные выше вкладки, чтобы выбрать статьи о моделях и средствах развертывания. Например, если вы хотите создать подключение типа «сеть —сеть» через VPN-шлюз с помощью классической модели развертывания (на портале Azure), щелкните вкладку **Портал Azure**. Откроется статья [Создание виртуальной сети с VPN-подключением типа «сеть —сеть» с помощью портала Azure](vpn-gateway-site-to-site-create.md).



## Подготовка

Перед началом настройки убедитесь, что у вас есть следующее.

- Совместимое VPN-устройство и пользователь, который может настроить его. Ознакомьтесь со статьей [Сведения о VPN-устройствах](vpn-gateway-about-vpn-devices.md).
- Внешний общедоступный IP-адрес для VPN-устройства. Этот IP-адрес не может располагаться вне преобразования сетевых адресов (NAT).
- Последняя версия командлетов Azure PowerShell. Чтобы загрузить и установить последнюю версию, см. раздел Windows PowerShell на [странице загрузки](http://azure.microsoft.com/downloads/). 
- Подписка Azure. Если у вас нет подписки Azure, вы можете [активировать преимущества для подписчиков MSDN](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) или [зарегистрироваться в бесплатной пробной версии](http://azure.microsoft.com/pricing/free-trial/).

>[AZURE.IMPORTANT]Если вы не умеете настраивать VPN-устройство или не знаете диапазоны IP-адресов в своей локальной сети, вам следует найти того, кто сможет предоставить вам нужную информацию.

## Подключение к подписке 


Откройте консоль PowerShell и подключитесь к своей учетной записи. Для подключения используйте следующий пример.

		Add-AzureAccount

Если у вас несколько подписок, используйте командлет *Select-AzureSubscription*, чтобы подключиться к нужной подписке.

		Select-AzureSubscription "yoursubscription"

Затем переключитесь в режим диспетчера ресурсов Azure. В скором времени изменение режима будет необязательным, а командлеты станут автоматически доступными для обоих режимов.

		Switch-AzureMode -Name AzureResourceManager


## Создание виртуальной сети и подсети шлюза

- Если у вас уже есть виртуальная сеть с подсетью шлюза, сразу переходите к разделу [Добавление локального сайта](#add-your-local-site). 
- Если у вас есть виртуальная сеть, к которой нужно добавить подсеть шлюза, перейдите к разделу [Добавление подсети шлюза к виртуальной сети](#gatewaysubnet).

### Создание виртуальной сети и подсети шлюза

Используйте приведенный ниже пример, чтобы создать виртуальную сеть и подсеть шлюза. Подставьте собственные значения.

Сначала создайте группу ресурсов.

	
		New-AzureResourceGroup -Name testrg -Location 'West US'

Затем создайте виртуальную сеть. Убедитесь, что указанные адресные пространства не перекрываются ни с одним из адресных пространств, которые используются в локальной сети.

Следующий пример создает виртуальную сеть с именем *testvnet*и две подсети: *GatewaySubnet* и *Subnet1*. Важно, чтобы одна из подсетей имела имя *GatewaySubnet*. Если вы используете другое имя, подключение не будет настроено.

		$subnet1 = New-AzureVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.0.0/28
		$subnet2 = New-AzureVirtualNetworkSubnetConfig -Name 'Subnet1' -AddressPrefix '10.0.1.0/28'
		New-AzurevirtualNetwork -Name testvnet -ResourceGroupName testrg -Location 'West US' -AddressPrefix 10.0.0.0/16 -Subnet $subnet1, $subnet2

### <a name="gatewaysubnet"></a>Добавление подсети шлюза в виртуальную сеть (необязательно)

Этот шаг требуется только в том случае, если вам нужно добавить подсеть шлюза в виртуальную сеть.

Если вы хотите добавить подсеть шлюза к существующей виртуальной сети, можно создать подсеть шлюза, используя следующий пример. Обязательно присвойте подсети шлюза имя GatewaySubnet. Если будет присвоено другое имя, вы создадите подсеть, но Azure не будет воспринимать ее как подсеть шлюза.

		$vnet = Get-AzureVirtualNetwork -ResourceGroupName testrg -Name testvnet
		Add-AzureVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.3.0/28 -VirtualNetwork $vnet

Теперь нужно настроить конфигурацию.

		Set-AzureVirtualNetwork -VirtualNetwork $vnet

## Добавление локального сайта

В виртуальной сети *локальный сайт* обычно означает локальное расположение. Этому сайту необходимо присвоить имя, по которому на него может ссылаться служба Azure.

Нужно также указать префикс адресного пространства для локального сайта. С помощью указанного префикса IP-адреса служба Azure сможет определить, какой трафик отправлять на локальный сайт. Это значит, что вам нужно будет указать каждый префикс адреса, который необходимо связать с локальным сайтом. При изменении локальной сети вы с легкостью можете обновить эти префиксы.

При использовании примеров PowerShell обратите внимание на следующее.
	
- Параметр *GatewayIPAddress* содержит IP-адрес локального VPN-устройства. Ваше VPN-устройство не может располагаться вне преобразования сетевых адресов (NAT). 
- Параметр *AddressPrefix* определяет локальное адресное пространство.

Чтобы добавить локальный сайт с одним префиксом адреса, используйте этот пример:

		New-AzureLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg -Location 'West US' -GatewayIpAddress '23.99.221.164' -AddressPrefix '10.5.51.0/24'

Чтобы добавить локальный сайт с несколькими адресами, используйте этот пример:

		New-AzureLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg -Location 'West US' -GatewayIpAddress '23.99.221.164' -AddressPrefix @('10.0.0.0/24','20.0.0.0/24')


### Изменение префиксов IP-адресов для локального сайта

Иногда префиксы адресов локального сайта могут изменяться. Действия по изменению префиксов IP-адресов будут разными в зависимости от того, создано ли подключение через VPN-шлюз. См. раздел [Изменение префиксов IP-адресов локального сайта](#modify-ip-address-prefixes-for-a-local-site).



## Запрос общедоступного IP-адреса для шлюза

Затем вам понадобится запросить общедоступный IP-адрес, который нужно выделить для шлюза VPN виртуальной сети Azure. Он отличается от IP-адреса, назначенного VPN-устройству. Этот адрес присваивается шлюзу VPN Azure отдельно. Вы не можете указать необходимый IP-адрес, так как он динамически выделяется для шлюза. Этот IP-адрес будет использоваться при настройке локального VPN-устройства для подключения к шлюзу.

Используйте следующий пример PowerShell. Для этого адреса нужно использовать метод динамического распределения (Dynamic).

		$gwpip= New-AzurePublicIpAddress -Name gwpip -ResourceGroupName testrg -Location 'West US' -AllocationMethod Dynamic

## Создание конфигурации IP-адресации шлюза

Конфигурация шлюза определяет используемые подсеть и общедоступный IP-адрес. Используйте следующий пример, чтобы создать конфигурацию шлюза.


		$vnet = Get-AzureVirtualNetwork -Name testvnet -ResourceGroupName testrg
		$subnet = Get-AzureVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
		$gwipconfig = New-AzureVirtualNetworkGatewayIpConfig -Name gwipconfig1 -SubnetId $subnet.Id -PublicIpAddressId $gwpip.Id 

## Создание шлюза

На этом шаге вы создадите шлюз виртуальной сети. Обратите внимание, что создание шлюза занимает некоторое время.

Используйте следующие значения:

- Тип шлюза — *Vpn*.
- У параметра VpnType может быть значение RouteBased* (в некоторых документах такой шлюз называется шлюзом с динамической маршрутизацией) или *Policy Based* (такой шлюз также известен как шлюз со статической маршрутизацией). Дополнительные сведения о типах VPN-шлюзов см. в статье [Шлюзы VPN](vpn-gateway-about-vpngateways.md). 	

		New-AzureVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg -Location 'West US' -IpConfigurations $gwipconfig -GatewayType Vpn -VpnType RouteBased

## Настройка устройства VPN

На этом этапе вам понадобится общедоступный IP-адрес шлюза виртуальной сети для настройки локального VPN-устройства. Обратитесь к производителю устройства, чтобы получить конкретные сведения о конфигурации. Также дополнительные сведения есть в статье [Сведения о VPN-устройствах](http://go.microsoft.com/fwlink/p/?linkid=615099).

Чтобы найти общедоступный IP-адрес шлюза виртуальной сети, используйте следующий пример.

	Get-AzurePublicIpAddress -Name gwpip -ResourceGroupName testrg

## Создание VPN-подключения

Затем создайте VPN-подключение типа "сеть-сеть" между шлюзом виртуальной сети и локальным VPN-устройством. Обязательно подставьте собственные значения. Общий ключ должен соответствовать значению, использованному в конфигурации VPN-устройства.

		$gateway1 = Get-AzureVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
		$local = Get-AzureLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg

		New-AzureVirtualNetworkGatewayConnection -Name localtovon -ResourceGroupName testrg -Location 'West US' -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'

Через некоторое время будет установлено подключение.

## Проверка VPN-подключения

Сейчас VPN-подключения типа «сеть —сеть», созданные с помощью диспетчера ресурсов, не отображаются на портале предварительной версии. Чтобы проверить успешность соединения, используйте команду *get-azurevirtualnetworkgatewayconnection -Debug*. Позднее мы предоставим отдельный командлет для этого, а также возможность просматривать подключения на портале предварительной версии.

Вы можете использовать командлет из следующего примера, заменив значения теми, которые используются у вас. При появлении запроса выберите ответ *A*, то есть «Все».

		Get-AzureVirtualNetworkGatewayConnection -Name localtovon -ResourceGroupName testrg -Debug

 После завершения работы командлета просмотрите результаты, которые он выдал. В следующем примере показано, что подключение установлено (состояние *Connected*), а также указан объем полученных и отправленных данных.

	Body:
	{
	  "name": "localtovon",
	  "id":
	"/subscriptions/086cfaa0-0d1d-4b1c-9455-f8e3da2a0c7789/resourceGroups/testrg/providers/Microsoft.Network/connections/loca
	ltovon",
	  "properties": {
	    "provisioningState": "Succeeded",
	    "resourceGuid": "1c484f82-23ec-47e2-8cd8-231107450446b",
	    "virtualNetworkGateway1": {
	      "id":
	"/subscriptions/086cfaa0-0d1d-4b1c-9455-f8e3da2a0c7789/resourceGroups/testrg/providers/Microsoft.Network/virtualNetworkGa
	teways/vnetgw1"
	    },
	    "localNetworkGateway2": {
	      "id":
	"/subscriptions/086cfaa0-0d1d-4b1c-9455-f8e3da2a0c7789/resourceGroups/testrg/providers/Microsoft.Network/localNetworkGate
	ways/LocalSite"
	    },
	    "connectionType": "IPsec",
	    "routingWeight": 10,
	    "sharedKey": "abc123",
	    "connectionStatus": "Connected",
	    "ingressBytesTransferred": 33509044,
	    "egressBytesTransferred": 4142431
	  }


## Изменение префиксов IP-адресов локального сайта

Если вам нужно изменить префиксы своего локального сайта, выполните следующие действия. Мы приводим два набора инструкций. Выбор между ними определяется тем, создано ли у вас подключение через VPN-шлюз.

### Добавление или удаление префиксов, если нет подключения через VPN-шлюз


- Чтобы **добавить** дополнительные префиксы локального сайта, который уже создан, но не имеет подключение через VPN-шлюз, используйте следующий пример.

		$local = Get-AzureLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg
		Set-AzureLocalNetworkGateway -LocalNetworkGateway $local -AddressPrefix @('10.0.0.0/24','20.0.0.0/24','30.0.0.0/24')



- Чтобы **удалить** префикс адресов локального сайта, который не имеет VPN-подключения, используйте следующий пример. Не указывайте префиксы, которые больше не нужны. В данном примере нам больше не нужен префикс 20.0.0.0/24 (из предыдущего примера), поэтому мы обновим локальный сайт и исключим этот префикс.

		local = Get-AzureLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg
		Set-AzureLocalNetworkGateway -LocalNetworkGateway $local -AddressPrefix @('10.0.0.0/24','30.0.0.0/24')

### Добавление или удаление префиксов, если есть подключение через VPN-шлюз

Если вы уже создали VPN-подключение и желаете добавить или удалить префиксы IP-адресов, указанные для вашего локального сайта, вам следует в правильной последовательности выполнить следующие шаги. При этом ваше VPN-подключение будет некоторое время недоступно, так как вы будете удалять и заново создавать шлюз. Но вам не потребуется повторно настраивать локальный VPN-маршрутизатор, если только вы не решите изменить ранее установленные значения, поскольку IP-адрес для подключения уже был запрошен.

 
1. Удалите подключения шлюза. 
2. Измените префиксы IP-адресов локального сайта. 
3. Создайте новое подключение шлюза. 

Если нужно, используйте следующий пример.


		$gateway1 = Get-AzureVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
		$local = Get-AzureLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg

		remove-AzureVirtualNetworkGatewayConnection -Name vnetgw1 -ResourceGroupName testrg

		$local = Get-AzureLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg
		Set-AzureLocalNetworkGateway -LocalNetworkGateway $local -AddressPrefix @('10.0.0.0/24','20.0.0.0/24','30.0.0.0/24')
	

		New-AzureVirtualNetworkGatewayConnection -Name localtovon -ResourceGroupName testrg -Location 'West US' -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'



## Дальнейшие действия

Добавьте виртуальную машину в виртуальную сеть. См. статью [Создание виртуальной машины](../virtual-machines/virtual-machines-windows-tutorial.md).

<!---HONumber=Sept15_HO3-->
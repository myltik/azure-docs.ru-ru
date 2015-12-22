<properties
   pageTitle="Создание виртуальной сети с VPN-подключением типа ";сеть-сеть"; с помощью диспетчера ресурсов Azure и PowerShell | Microsoft Azure"
   description="В этой статье поэтапно описывается создание виртуальной сети с помощью модели диспетчера ресурсов и ее подключение к локальной сети с помощью подключения типа «сеть — сеть» через VPN-шлюз. Подключения типа «сеть — сеть» можно использовать для гибридных конфигураций. В ней также содержатся инструкции по изменению префиксов IP-адресов для существующих локальных сайтов."
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
   ms.date="12/14/2015"
   ms.author="cherylmc"/>

# Создание виртуальной сети с VPN-подключением типа «сеть —сеть» с помощью PowerShell

> [AZURE.SELECTOR]
- [Azure Classic Portal](vpn-gateway-site-to-site-create.md)
- [PowerShell - Resource Manager](vpn-gateway-create-site-to-site-rm-powershell.md)

В этой статье мы покажем вам, как создать виртуальную сеть, соединенную с локальною сетью через VPN-подключение типа «сеть — сеть», с использованием модели развертывания, в которой применяется диспетчер ресурсов Azure. Если вы ищете другую модель развертывания для этой конфигурации, воспользуйтесь вкладками вверху страницы для выбора нужной статьи. Если вы хотите объединить виртуальные сети, не создавая соединение с локальным расположением, см. статью [Настройка подключения между виртуальными сетями](vpn-gateway-vnet-vnet-rm-ps.md).


**О моделях развертывания Azure**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## Перед началом работы

Перед началом настройки убедитесь, что у вас есть следующее.

- Совместимое VPN-устройство и пользователь, который может настроить его. См. статью [О VPN-устройствах](vpn-gateway-about-vpn-devices.md). Если вы не умеете настраивать VPN-устройство или не знаете диапазоны IP-адресов в своей локальной сети, вам следует найти того, кто сможет предоставить вам нужную информацию.

- Внешний общедоступный IP-адрес для VPN-устройства. Этот IP-адрес не может располагаться вне преобразования сетевых адресов (NAT).
	
- Подписка Azure. Если у вас нет подписки Azure, вы можете [активировать преимущества для подписчиков MSDN](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) или [зарегистрироваться в бесплатной пробной версии](http://azure.microsoft.com/pricing/free-trial/).

## Установка модулей PowerShell

Вам потребуется последняя версия командлетов диспетчера ресурсов PowerShell Azure для настройки соединения.

[AZURE.INCLUDE [vpn-gateway-ps-rm-howto](../../includes/vpn-gateway-ps-rm-howto-include.md)]

## 1\. Подключение к подписке 


Для работы с командлетами диспетчера ресурсов необходимо перейти в режим PowerShell. Дополнительную информацию см. в разделе [Использование Windows PowerShell с диспетчером ресурсов](../powershell-azure-resource-manager.md).

Откройте консоль PowerShell и подключитесь к своей учетной записи. Для подключения используйте следующий пример.

		    Login-AzureRmAccount

Просмотрите подписки учетной записи.

		    Get-AzureRmSubscription 

Укажите подписку, которую нужно использовать.

		    Select-AzureRmSubscription -Subscriptionid "GUID of subscription"


## 2\. Создание виртуальной сети и подсети шлюза

- Если у вас уже есть виртуальная сеть с подсетью шлюза, сразу переходите к разделу **Шаг 3. Добавление локального сайта**. 
- Если у вас есть виртуальная сеть, к которой нужно добавить подсеть шлюза, перейдите к разделу [Добавление подсети шлюза к виртуальной сети](#gatewaysubnet).

### Создание виртуальной сети и подсети шлюза

Используйте приведенный ниже пример, чтобы создать виртуальную сеть и подсеть шлюза. Подставьте собственные значения.

Сначала создайте группу ресурсов.

	
		New-AzureRmResourceGroup -Name testrg -Location 'West US'

Затем создайте виртуальную сеть. Убедитесь, что указанные адресные пространства не перекрываются ни с одним из адресных пространств, которые используются в локальной сети.

В следующем примере создается виртуальная сеть с именем *testvnet* и две подсети: *GatewaySubnet* и *Subnet1*. Важно, чтобы одна из подсетей имела имя *GatewaySubnet*. Если вы используете другое имя, подключение не будет настроено.

		$subnet1 = New-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.0.0/28
		$subnet2 = New-AzureRmVirtualNetworkSubnetConfig -Name 'Subnet1' -AddressPrefix '10.0.1.0/28'
		New-AzureRmVirtualNetwork -Name testvnet -ResourceGroupName testrg -Location 'West US' -AddressPrefix 10.0.0.0/16 -Subnet $subnet1, $subnet2

### <a name="gatewaysubnet"></a>Добавление подсети шлюза к виртуальной сети (необязательно)

Этот шаг требуется только в том случае, если вам нужно добавить подсеть шлюза в созданную ранее виртуальную сеть.

Если вы хотите добавить подсеть шлюза к существующей виртуальной сети, можно создать подсеть шлюза, используя следующий пример. Обязательно присвойте подсети шлюза имя GatewaySubnet. Если будет присвоено другое имя, вы создадите подсеть, но Azure не будет воспринимать ее как подсеть шлюза.

		$vnet = Get-AzureRmVirtualNetwork -ResourceGroupName testrg -Name testvnet
		Add-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.3.0/28 -VirtualNetwork $vnet

Теперь нужно настроить конфигурацию.

		Set-AzureRmVirtualNetwork -VirtualNetwork $vnet

## 3\. Добавление локального сайта

В виртуальной сети термин *локальный сайт* обычно означает локальное расположение. Этому сайту необходимо присвоить имя, по которому на него может ссылаться служба Azure.

Нужно также указать префикс адресного пространства для локального сайта. С помощью указанного префикса IP-адреса служба Azure сможет определить, какой трафик отправлять на локальный сайт. Это значит, что вам нужно будет указать каждый префикс адреса, который необходимо связать с локальным сайтом. При изменении локальной сети вы с легкостью можете обновить эти префиксы.

При использовании примеров PowerShell обратите внимание на следующее.
	
- Параметр *GatewayIPAddress* — это IP-адрес локального VPN-устройства. Ваше VPN-устройство не может располагаться вне преобразования сетевых адресов (NAT). 
- Параметр *AddressPrefix* — локальное адресное пространство.

Чтобы добавить локальный сайт с одним префиксом адреса, используйте этот пример:

		New-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg -Location 'West US' -GatewayIpAddress '23.99.221.164' -AddressPrefix '10.5.51.0/24'

Чтобы добавить локальный сайт с несколькими адресами, используйте этот пример:

		New-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg -Location 'West US' -GatewayIpAddress '23.99.221.164' -AddressPrefix @('10.0.0.0/24','20.0.0.0/24')

### Изменение префиксов IP-адресов для локального сайта

Иногда префиксы адресов локального сайта могут изменяться. Действия по изменению префиксов IP-адресов будут разными в зависимости от того, создано ли подключение через VPN-шлюз. См. раздел [Изменение префиксов IP-адресов локального сайта](#to-modify-ip-address-prefixes-for-a-local-site).


## 4\. Запрос общедоступного IP-адреса для шлюза

Затем вам понадобится запросить общедоступный IP-адрес, который нужно выделить для шлюза VPN виртуальной сети Azure. Он отличается от IP-адреса, назначенного VPN-устройству. Этот адрес присваивается шлюзу VPN Azure отдельно. Вы не можете указать необходимый IP-адрес, так как он динамически выделяется для шлюза. Этот IP-адрес будет использоваться при настройке локального VPN-устройства для подключения к шлюзу.

Используйте следующий пример PowerShell. Для этого адреса нужно использовать метод динамического распределения (Dynamic).

		$gwpip= New-AzureRmPublicIpAddress -Name gwpip -ResourceGroupName testrg -Location 'West US' -AllocationMethod Dynamic

## 5\. Создание конфигурации IP-адресации шлюза

Конфигурация шлюза определяет используемые подсеть и общедоступный IP-адрес. Используйте следующий пример, чтобы создать конфигурацию шлюза.


		$vnet = Get-AzureRmVirtualNetwork -Name testvnet -ResourceGroupName testrg
		$subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
		$gwipconfig = New-AzureRmVirtualNetworkGatewayIpConfig -Name gwipconfig1 -SubnetId $subnet.Id -PublicIpAddressId $gwpip.Id 

## 6\. Создание шлюза

На этом шаге вы создадите шлюз виртуальной сети. Обратите внимание, что создание шлюза может занять много времени — 20 минут или больше.

Используйте следующие значения:

- Тип шлюза — *Vpn*.
- Параметр VpnType может иметь значение RouteBased* (в некоторых документах такой шлюз называется шлюзом с динамической маршрутизацией) или *Policy Based* (в некоторых документах — шлюз со статической маршрутизацией). Дополнительные сведения о типах VPN-шлюзов см. в статье [Шлюзы VPN](vpn-gateway-about-vpngateways.md). 	

		New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg -Location 'West US' -IpConfigurations $gwipconfig -GatewayType Vpn -VpnType RouteBased

## 7\. Настройка устройства VPN

На этом этапе вам понадобится общедоступный IP-адрес шлюза виртуальной сети для настройки локального VPN-устройства. Обратитесь к производителю устройства, чтобы получить конкретные сведения о конфигурации. Дополнительные сведения см. также в статье [О VPN-устройствах для подключений виртуальной сети типа «сеть — сеть»](http://go.microsoft.com/fwlink/p/?linkid=615099).

Чтобы найти общедоступный IP-адрес шлюза виртуальной сети, используйте следующий пример.

	Get-AzureRmPublicIpAddress -Name gwpip -ResourceGroupName testrg

## 8\. Создание VPN-подключения

Затем создайте VPN-подключение типа "сеть-сеть" между шлюзом виртуальной сети и локальным VPN-устройством. Обязательно подставьте собственные значения. Общий ключ должен соответствовать значению, использованному в конфигурации VPN-устройства.

		$gateway1 = Get-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
		$local = Get-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg

		New-AzureRmVirtualNetworkGatewayConnection -Name localtovon -ResourceGroupName testrg -Location 'West US' -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'

Через некоторое время будет установлено подключение.

## 9\. Проверка VPN-подключения

Сейчас VPN-подключения типа «сеть —сеть», созданные с помощью диспетчера ресурсов, не отображаются на портале предварительной версии. Чтобы проверить успешность подключения, используйте команду *Get-AzureRmVirtualNetworkGatewayConnection –Debug*. Позднее мы предоставим отдельный командлет для этого, а также возможность просматривать подключения на портале предварительной версии.

Вы можете использовать командлет из следующего примера, заменив значения теми, которые используются у вас. При появлении запроса выберите ответ *A*, то есть All (Все).

		Get-AzureRmVirtualNetworkGatewayConnection -Name localtovon -ResourceGroupName testrg -Debug

 После завершения работы командлета просмотрите результаты, которые он выдал. В следующем примере показано, что подключение установлено (состояние *Connected*), а также указан объем полученных и отправленных данных в байтах.

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


- Чтобы **добавить** дополнительные префиксы адресов для созданного локального сайта, у которого пока нет подключения через VPN-шлюз, используйте следующий пример.

		$local = Get-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg
		Set-AzureRmLocalNetworkGateway -LocalNetworkGateway $local -AddressPrefix @('10.0.0.0/24','20.0.0.0/24','30.0.0.0/24')


- Чтобы **удалить** префикс адреса локального сайта, у которого нет VPN-подключения, используйте приведенный ниже пример. Не указывайте префиксы, которые больше не нужны. В данном примере нам больше не нужен префикс 20.0.0.0/24 (из предыдущего примера), поэтому мы обновим локальный сайт и исключим этот префикс.

		local = Get-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg
		Set-AzureRmLocalNetworkGateway -LocalNetworkGateway $local -AddressPrefix @('10.0.0.0/24','30.0.0.0/24')

### Добавление или удаление префиксов, если есть подключение через VPN-шлюз

Если вы уже создали VPN-подключение и желаете добавить или удалить префиксы IP-адресов, указанные для вашего локального сайта, вам следует в правильной последовательности выполнить следующие шаги. При этом ваше VPN-подключение будет некоторое время недоступно, так как вы будете удалять и заново создавать шлюз. Но вам не потребуется повторно настраивать локальный VPN-маршрутизатор, если только вы не решите изменить ранее установленные значения, поскольку IP-адрес для подключения уже был запрошен.

 
1. Удалите подключения шлюза. 
2. Измените префиксы IP-адресов локального сайта. 
3. Создайте новое подключение шлюза. 

Если нужно, используйте следующий пример.


		$gateway1 = Get-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
		$local = Get-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg

		remove-AzureRmVirtualNetworkGatewayConnection -Name vnetgw1 -ResourceGroupName testrg

		$local = Get-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg
		Set-AzureRmLocalNetworkGateway -LocalNetworkGateway $local -AddressPrefix @('10.0.0.0/24','20.0.0.0/24','30.0.0.0/24')
	

		New-AzureRmVirtualNetworkGatewayConnection -Name localtovon -ResourceGroupName testrg -Location 'West US' -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'


## Дальнейшие действия

Установив подключение, можно добавить виртуальные машины в виртуальные сети. Инструкции см. в статье [Создание виртуальной машины под управлением Windows на портале Azure](../virtual-machines/virtual-machines-windows-tutorial.md).

<!---HONumber=AcomDC_1217_2015-->
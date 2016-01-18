<properties 
   pageTitle="Создание пользовательской проверки для шлюза приложений с помощью PowerShell в диспетчере ресурсов | Microsoft Azure"
   description="Научить создавать пользовательские проверки для шлюза приложений с помощью PowerShell в диспетчере ресурсов"
   services="application-gateway"
   documentationCenter="na"
   authors="joaoma"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>
<tags  
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="12/17/2015"
   ms.author="joaoma" />

# Создание пользовательской проверки для шлюза приложений с помощью PowerShell для диспетчера ресурсов Azure

[AZURE.INCLUDE [azure-probe-intro-include](../../includes/application-gateway-create-probe-intro-include.md)]


[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)] [classic deployment model](application-gateway-create-probe-classic-ps.md).


[AZURE.INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]


### Шаг 1. 

Выполните проверку подлинности с помощью Login-AzureRmAccount

	Login-AzureRmAccount

### Шаг 2.

Проверка подписок для учетной записи

		get-AzureRmSubscription 

Вам будет предложено пройти проверку подлинности с вашими учетными данными.<BR>

### Шаг 3. 

Выберите, какие подписки Azure будут использоваться. <BR>


		Select-AzureRmSubscription -Subscriptionid "GUID of subscription"


### Шаг 4.

Создайте группу ресурсов (пропустите этот шаг, если вы используете существующую группу).

    New-AzureRmResourceGroup -Name appgw-rg -location "West US"

Диспетчер ресурсов Azure требует, чтобы все группы ресурсов указывали расположение. Оно используется в качестве расположения по умолчанию для всех ресурсов данной группы. Убедитесь, что во всех командах для создания шлюза приложений используется одна группа ресурсов.

В приведенном выше примере мы создали группу ресурсов под названием appgw-RG с расположением West US (запад США).

## Создание виртуальной сети и подсети для шлюза приложений

С помощью следующих шагов вы создадите виртуальную сеть и подсеть для шлюза приложения.

### Шаг 1	
	

Назначение диапазона адресов 10.0.0.0/24 в переменную подсети, которая будет использоваться для создания виртуальной сети

	$subnet = New-AzureRmVirtualNetworkSubnetConfig -Name subnet01 -AddressPrefix 10.0.0.0/24

### Шаг 2.	

Создается виртуальная сеть с именем "appgwvnet" в группе ресурсов "appgw-rg" для региона запада США при помощи префикса 10.0.0.0/16 с подсетью 10.0.0.0/24

	$vnet = New-AzureRmVirtualNetwork -Name appgwvnet -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $subnet


### Шаг 3.

Задайте значение переменной подсети на дальнейших этапах создания шлюза приложений
		
	$subnet=$vnet.Subnets[0]

## Создание общедоступного IP-адреса для конфигурации внешнего интерфейса


Создается ресурс общедоступного IP-адреса "publicIP01" в группе ресурсов "appgw-rg" для западного региона США.

	$publicip = New-AzureRmPublicIpAddress -ResourceGroupName appgw-rg -name publicIP01 -location "West US" -AllocationMethod Dynamic
 

## Создание объекта конфигурации шлюза приложений с пользовательской проверкой 

Перед созданием шлюза приложений необходимо настроить все элементы конфигурации. Следующие шаги создают необходимые элементы конфигурации для ресурса шлюза приложений


### Шаг 1

Создание конфигурации IP шлюза приложений с именем gatewayIP01. При запуске шлюза приложений он получит IP-адрес из настроенной подсети и будет маршрутизировать сетевой трафик на IP-адреса пула серверных IP-адресов. Помните, что для каждого экземпляра потребуется отдельный IP-адрес.

	$gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $subnet

 
### Шаг 2


На этом этапе будет выполнена настройка серверной части пула IP-адресов под именем pool01 с IP-адресами 134.170.185.46, 134.170.188.221, 134.170.185.50. Эти адреса будут использоваться для получения сетевого трафика от внешней конечной точки с выделенным IP-адресом. Вы замените приведенные выше IP-адреса и добавите конечные точки IP-адресов своего приложения.

	$pool = New-AzureRmApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 134.170.185.46, 134.170.188.221,134.170.185.50


### Шаг 3.


На этом шаге настраивается пользовательская проверка работоспособности.

Используемые параметры:

- **-Interval** — задает интервал проверки работоспособности в секундах 
- **-Timeout** — определяет время ожидания для проверки ответа HTTP
- **-Hostname и -path** — полный путь URL, который вызывается шлюзом приложений для определения работоспособности экземпляра. Например, если у вас веб-сайт http://contoso.com/, то пользовательскую проверку работоспособности для получения успешного ответа HTTP можно настроить как "http://contoso.com/path/custompath.htm". 
- **- UnhealthyThreshold** — количество неудачных ответов HTTP, по достижении которого экземпляр внутреннего сервера считается *неработоспособным*



	$probe = New-AzureRmApplicationGatewayProbeConfig -Name probe01 -Protocol Http -HostName "contoso.com" -Path "/path/path.htm" -Interval 30 -Timeout 120 -UnhealthyThreshold 8


### Шаг 4.

Настраиваются параметры шлюза приложений poolsetting01 для трафика в пуле внутренних серверов. На этом шаге также настраивается время ожидания ответа пула внутренних серверов на запрос шлюза приложений. При достижении этого времени ожидания шлюз приложений отменит запрос. Это отличается от проверки времени ожидания, которое относится только к ответу пула внутренних серверов на проверку работоспособности.

	$poolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name poolsetting01 -Port 80 -Protocol Http -CookieBasedAffinity Disabled -Probe $probe -RequestTimeout 80


### Шаг 5

Настройка IP-порта интерфейсной части под именем frontendport01, в данном случае для конечной точки с общедоступным IP-адресом.

	$fp = New-AzureRmApplicationGatewayFrontendPort -Name frontendport01  -Port 80

### Шаг 6

Создание конфигурации IP интерфейсной части fipconfig01 и связывание общедоступного IP-адреса с конфигурацией IP интерфейсной части.


	$fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig -Name fipconfig01 -PublicIPAddress $publicip

### Шаг 7

Создание прослушивателя listener01 и связывание порта интерфейсной части с конфигурацией IP интерфейсной части.

	$listener = New-AzureRmApplicationGatewayHttpListener -Name listener01  -Protocol Http -FrontendIPConfiguration $fipconfig -FrontendPort $fp

### Шаг 8 

Создание правила маршрутизации rule01 для подсистемы балансировки нагрузки для настройки ее поведения.

	$rule = New-AzureRmApplicationGatewayRequestRoutingRule -Name rule01 -RuleType Basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool

### Шаг 9.

Настройка размера экземпляра шлюза приложений

	$sku = New-AzureRmApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2


>[AZURE.NOTE]Значение параметра *InstanceCount* по умолчанию — 2 (максимальное значение — 10). По умолчанию для параметра *GatewaySize* используется значение Medium. Можно выбрать Standard\_Small, Standard\_Medium или Standard\_Large.

## Создание шлюза приложений с помощью командлета New-AzureRmApplicationGateway

Создание шлюза приложений со всеми элементами конфигурации, описанными выше. В этом примере шлюз приложений называется appgwtest.

	$appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Location "West US" -BackendAddressPools $pool -Probes $probe -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku

## Добавление проверки для существующего шлюза приложений

Для добавления проверки для существующего шлюза приложений необходимо выполнить четыре действия.

### Шаг 1 

Загрузка ресурса шлюза приложений в переменную PowerShell с помощью `Get-AzureRmApplicationGateway`

	$getgw =  Get-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg

### Шаг 2

Добавление проверки к существующей конфигурации шлюза.

	$probe = Add-AzureRmApplicationGatewayProbeConfig -ApplicationGateway $getgw -Name probe01 -Protocol Http -HostName "contoso.com" -Path "/path/custompath.htm" -Interval 30 -Timeout 120 -UnhealthyThreshold 8

   
В примере пользовательская проверка настроена для проверки URL-адреса contoso.com/path/custompath.htm каждые 30 секунд. Настроено пороговое значение времени ожидания в 120 секунд с максимальным количеством неудачных запросов 8.

### Шаг 3.

Добавление проверки к конфигурации пула внутренних серверов и времени ожидания с помощью `-Set-AzureRmApplicationGatewayBackendHttpSettings`


	 $getgw = Set-AzureRmApplicationGatewayBackendHttpSettings -ApplicationGateway $getgw -Name $getgw.BackendHttpSettingsCollection.name -Port 80 -Protocol Http -CookieBasedAffinity Disabled -Probe $probe -RequestTimeout 120

### Шаг 4.

Сохранение конфигурации шлюза приложений с помощью `Set-AzureRmApplicationGateway`

	Set-AzureRmApplicationGateway -ApplicationGateway $getgw -verbose

## Удаление проверки из существующего шлюза приложений

Ниже приведены инструкции по удалению пользовательской проверки из существующего шлюза приложений.

### Шаг 1. 

Загрузка ресурса шлюза приложений в переменную PowerShell с помощью `Get-AzureRmApplicationGateway`

	$getgw =  Get-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg


### Шаг 2 

Удаление конфигурации проверки из шлюза приложений с помощью `Remove-AzureRmApplicationGatewayProbeConfig`

	$getgw = Remove-AzureRmApplicationGatewayProbeConfig -ApplicationGateway $getgw -Name $getgw.Probes.name

### Шаг 3. 

Обновление параметров пула внутренних серверов для удаления проверки и времени ожидания с помощью `-Set-AzureRmApplicationGatewayBackendHttpSettings`


	 $getgw=Set-AzureRmApplicationGatewayBackendHttpSettings -ApplicationGateway $getgw -Name $getgw.BackendHttpSettingsCollection.name -Port 80 -Protocol http -CookieBasedAffinity Disabled

### Шаг 4.
	
Сохранение конфигурации шлюза приложений с помощью `Set-AzureRmApplicationGateway`

	Set-AzureRmApplicationGateway -ApplicationGateway $getgw -verbose

<!---HONumber=AcomDC_0107_2016-->
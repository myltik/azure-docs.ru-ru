<properties
   pageTitle="Создание пользовательской проверки для шлюза приложений с помощью PowerShell в диспетчере ресурсов | Microsoft Azure"
   description="Узнайте, как создать пользовательскую проверку для шлюза приложений с помощью PowerShell в диспетчере ресурсов."
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


### Шаг 1

Выполните проверку подлинности с помощью Login-AzureRmAccount.

	Login-AzureRmAccount

### Шаг 2

Просмотрите подписки учетной записи.

		get-AzureRmSubscription

Вам будет предложено указать свои учетные данные для проверки подлинности.<BR>

### Шаг 3.

Выберите, какие подписки Azure будут использоваться. <BR>


		Select-AzureRmSubscription -Subscriptionid "GUID of subscription"


### Шаг 4.

Создайте группу ресурсов (если вы используете существующую группу, пропустите этот шаг).

    New-AzureRmResourceGroup -Name appgw-rg -location "West US"

В диспетчере ресурсов Azure для всех групп ресурсов должно быть указано расположение. Оно используется в качестве расположения по умолчанию для всех ресурсов данной группы. Убедитесь, что во всех командах для создания шлюза приложений используется одна группа ресурсов.

В приведенном выше примере мы создали группу ресурсов под названием "appgw-RG" с расположением "West US" (запад США).

## Создание виртуальной сети и подсети для шлюза приложения

С помощью следующих шагов вы создадите виртуальную сеть и подсеть для шлюза приложения.

### Шаг 1


Назначьте диапазон адресов 10.0.0.0/24 переменной подсети, которая будет использоваться для создания виртуальной сети.

	$subnet = New-AzureRmVirtualNetworkSubnetConfig -Name subnet01 -AddressPrefix 10.0.0.0/24

### Шаг 2

Создайте виртуальную сеть с именем "appgwvnet" в группе ресурсов "appgw-rg" для региона запада США при помощи префикса 10.0.0.0/16 с подсетью 10.0.0.0/24.

	$vnet = New-AzureRmVirtualNetwork -Name appgwvnet -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $subnet


### Шаг 3.

Назначьте переменную подсети для дальнейших этапов создания шлюза приложений.

	$subnet=$vnet.Subnets[0]

## Создание общедоступного IP-адреса для конфигурации интерфейсной части


Создайте ресурс общедоступного IP-адреса "publicIP01" в группе ресурсов "appgw-rg" для западного региона США.

	$publicip = New-AzureRmPublicIpAddress -ResourceGroupName appgw-rg -name publicIP01 -location "West US" -AllocationMethod Dynamic


## Создание объекта конфигурации шлюза приложений с пользовательской проверкой

Перед созданием шлюза приложений необходимо настроить все элементы конфигурации. В ходе следующих шагов создаются необходимые элементы конфигурации для ресурса шлюза приложений.


### Шаг 1

Создайте конфигурацию IP шлюза приложений с именем "gatewayIP01". При запуске шлюз приложений получает IP-адрес из настроенной подсети. Затем шлюз маршрутизирует сетевой трафик на IP-адреса из пула внутренних IP-адресов. Помните, что для каждого экземпляра требуется отдельный IP-адрес.

	$gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $subnet


### Шаг 2


Настройте серверную часть пула IP-адресов под именем "pool01" с IP-адресами 134.170.185.46, 134.170.188.221, 134.170.185.50. Эти адреса будут использоваться для получения сетевого трафика от конечной точки с внешним IP-адресом. Вы замените приведенные выше IP-адреса и добавите конечные точки IP-адресов своего приложения.

	$pool = New-AzureRmApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 134.170.185.46, 134.170.188.221,134.170.185.50


### Шаг 3.


На этом шаге настраивается пользовательская проверка работоспособности.

Используемые параметры:

- **-Interval** — задает интервал проверки работоспособности в секундах.
- **-Timeout** — определяет время ожидания для проверки ответа HTTP.
- **-Hostname и -path** — полный путь URL, который вызывается шлюзом приложений для определения работоспособности экземпляра. Например, если у вас веб-сайт http://contoso.com/, то пользовательскую проверку работоспособности для получения успешного ответа HTTP можно настроить как "http://contoso.com/path/custompath.htm".
- **-UnhealthyThreshold** — количество неудачных ответов HTTP, по достижении которого экземпляр внутреннего сервера считается *неработоспособным*.

<BR>

	$probe = New-AzureRmApplicationGatewayProbeConfig -Name probe01 -Protocol Http -HostName "contoso.com" -Path "/path/path.htm" -Interval 30 -Timeout 120 -UnhealthyThreshold 8


### Шаг 4.

Настройте параметры шлюза приложений "poolsetting01" для трафика в пуле внутренних серверов. На этом шаге также настраивается время ожидания ответа пула внутренних серверов на запрос шлюза приложений. При достижении этого времени ожидания шлюз приложений отменит запрос. Это отличается от времени ожидания проверки, которое относится только к ответу пула внутренних серверов на проверку работоспособности.

	$poolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name poolsetting01 -Port 80 -Protocol Http -CookieBasedAffinity Disabled -Probe $probe -RequestTimeout 80


### Шаг 5

Настройте IP-порт интерфейсной части под именем "frontendport01" для конечной точки с общедоступным IP-адресом.

	$fp = New-AzureRmApplicationGatewayFrontendPort -Name frontendport01  -Port 80

### Шаг 6

Создайте конфигурацию IP интерфейсной части "fipconfig01" и свяжите общедоступный IP-адрес с конфигурацией IP интерфейсной части.


	$fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig -Name fipconfig01 -PublicIPAddress $publicip

### Шаг 7

Создайте прослушиватель "listener01" и свяжите порт интерфейсной части с конфигурацией IP интерфейсной части.

	$listener = New-AzureRmApplicationGatewayHttpListener -Name listener01  -Protocol Http -FrontendIPConfiguration $fipconfig -FrontendPort $fp

### Шаг 8

Создайте правило маршрутизации "rule01" для балансировщика нагрузки для настройки его поведения.

	$rule = New-AzureRmApplicationGatewayRequestRoutingRule -Name rule01 -RuleType Basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool

### Шаг 9.

Настройте размер экземпляра шлюза приложений.

	$sku = New-AzureRmApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2


>[AZURE.NOTE]  Значение параметра *InstanceCount* по умолчанию — 2 (максимальное значение — 10). По умолчанию для параметра *GatewaySize* используется значение Medium. Можно выбрать Standard\_Small, Standard\_Medium или Standard\_Large.

## Создание шлюза приложений с помощью командлета New-AzureRmApplicationGateway

Создайте шлюз приложений со всеми элементами конфигурации, описанными выше. В этом примере шлюз приложений называется "appgwtest".

	$appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Location "West US" -BackendAddressPools $pool -Probes $probe -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku

## Добавление проверки для существующего шлюза приложений

Для добавления проверки для существующего шлюза приложений необходимо выполнить четыре действия.

### Шаг 1

Загрузка ресурса шлюза приложений в переменную PowerShell с помощью командлета **Get-AzureRmApplicationGateway**.

	$getgw =  Get-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg

### Шаг 2

Добавление проверки к существующей конфигурации шлюза.

	$probe = Add-AzureRmApplicationGatewayProbeConfig -ApplicationGateway $getgw -Name probe01 -Protocol Http -HostName "contoso.com" -Path "/path/custompath.htm" -Interval 30 -Timeout 120 -UnhealthyThreshold 8


В примере пользовательская проверка настроена для проверки URL-адреса contoso.com/path/custompath.htm каждые 30 секунд. Настроено пороговое значение времени ожидания в 120 секунд с максимальным количеством неудачных запросов 8.

### Шаг 3.

Добавление проверки к конфигурации параметра пула серверной части и времени ожидания с помощью командлета **-Set-AzureRmApplicationGatewayBackendHttpSettings**.


	 $getgw = Set-AzureRmApplicationGatewayBackendHttpSettings -ApplicationGateway $getgw -Name $getgw.BackendHttpSettingsCollection.name -Port 80 -Protocol Http -CookieBasedAffinity Disabled -Probe $probe -RequestTimeout 120

### Шаг 4.

Сохранение конфигурации в шлюз приложений с помощью командлета **Set-AzureRmApplicationGateway**.

	Set-AzureRmApplicationGateway -ApplicationGateway $getgw -verbose

## Удаление проверки из существующего шлюза приложений

Ниже приведены инструкции по удалению пользовательской проверки из существующего шлюза приложений.

### Шаг 1

Загрузка ресурса шлюза приложений в переменную PowerShell с помощью командлета **Get-AzureRmApplicationGateway**.

	$getgw =  Get-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg


### Шаг 2

Удаление конфигурации проверки из шлюза приложений с помощью командлета **Remove-AzureRmApplicationGatewayProbeConfig**.

	$getgw = Remove-AzureRmApplicationGatewayProbeConfig -ApplicationGateway $getgw -Name $getgw.Probes.name

### Шаг 3.

Обновление параметра серверной части для удаления параметра проверки и времени ожидания с помощью командлета **-Set-AzureRmApplicationGatewayBackendHttpSettings**.


	 $getgw=Set-AzureRmApplicationGatewayBackendHttpSettings -ApplicationGateway $getgw -Name $getgw.BackendHttpSettingsCollection.name -Port 80 -Protocol http -CookieBasedAffinity Disabled

### Шаг 4.

Сохранение конфигурации в шлюз приложений с помощью командлета **Set-AzureRmApplicationGateway**.

	Set-AzureRmApplicationGateway -ApplicationGateway $getgw -verbose

<!---HONumber=AcomDC_0128_2016-->
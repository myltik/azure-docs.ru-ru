<properties
   pageTitle="Создание шлюза приложений для размещения нескольких сайтов | Microsoft Azure"
   description="Эта страница содержит инструкции по созданию и настройке шлюза приложений Azure для размещения нескольких веб-приложений в одном шлюзе."
   documentationCenter="na"
   services="application-gateway"
   authors="amsriva"
   manager="rossort"
   editor="amsriva"/>
<tags
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="07/07/2016"
   ms.author="amsriva"/>


# Создание шлюза приложений для размещения нескольких веб-приложений

Размещение нескольких сайтов позволяет развернуть в одном шлюзе приложений не одно, а несколько веб-приложений. Чтобы определить, какой прослушиватель должен принимать трафик, шлюз приложений проверяет наличие заголовка узла во входящем HTTP-запросе. Затем прослушиватель направляет трафик в соответствующий серверный пул согласно определениям правил шлюза. В веб-приложениях с поддержкой протокола SSL шлюз приложений использует расширение SNI (указание имени сервера) для выбора соответствующего прослушивателя веб-трафика.

Как правило, размещение нескольких сайтов используется для балансировки нагрузки запросов к разным веб-доменам между различными внутренними пулами серверов. Подобным образом в одном шлюзе приложений можно разместить несколько поддоменов одного корневого домена.

## Сценарий
В следующем примере шлюз приложений обслуживает трафик сайтов contoso.com и fabrikam.com с помощью двух внутренних пулов серверов: contoso и fabrikam. Аналогичную конфигурацию можно использовать для размещения таких поддоменов, как app.contoso.com и blog.contoso.com.


## Перед началом работы

1. Установите последнюю версию командлетов Azure PowerShell, используя установщик веб-платформы. Вы можете скачать и установить последнюю версию из раздела **Windows PowerShell** на [странице загрузок](https://azure.microsoft.com/downloads/).
2. Создайте виртуальную сеть и подсеть для шлюза приложений. Убедитесь, что подсеть не используется виртуальной машиной или облачным развертыванием. Сам шлюз приложений должен находиться в подсети виртуальной сети.
3. Для использования шлюза приложений существующие серверы или серверы, для которых в виртуальной сети созданы конечные точки либо же назначен общедоступный или виртуальный IP-адрес, добавляются в пул тыловых серверов.



## Что необходимо для создания шлюза приложений?


- **Пул тыловых серверов**. Список IP-адресов тыловых серверов. Указанные IP-адреса должны относиться к подсети виртуальной сети либо представлять собой общедоступные или виртуальные IP-адреса. Можно также использовать полное доменное имя.
- **Параметры пула тыловых серверов**. Каждый пул имеет такие параметры, как порт, протокол и сходство на основе файлов cookie. Эти параметры привязываются к пулу и применяются ко всем серверам в этом пуле.
- **Внешний порт**. Общедоступный порт, открытый в шлюзе приложений. Трафик поступает на этот порт, а затем перенаправляется на один из тыловых серверов.
- **Прослушиватель**. У прослушивателя есть внешний порт, протокол (Http или Https — с учетом регистра) и имя SSL-сертификата (в случае настройки разгрузки SSL). Для шлюзов приложений с поддержкой нескольких сайтов также добавляются имя узла и индикаторы SNI.
- **Правило**. Правило связывает прослушиватель и пул тыловых серверов, а также определяет, в какой пул тыловых серверов следует направлять трафик, поступающий на определенный прослушиватель.

## Создание шлюза приложений

Ниже приведены пошаговые инструкции по созданию шлюза приложений.

1. Создание группы ресурсов для диспетчера ресурсов.
2. Создание виртуальной сети, подсети и общедоступного IP-адреса для шлюза приложений.
3. Создание объекта конфигурации шлюза приложений.
4. Создание ресурса шлюза приложений.

## Создание группы ресурсов для диспетчера ресурсов

Убедитесь, что у вас установлена последняя версия Azure PowerShell. Дополнительные сведения см. в статье [Использование Windows PowerShell с диспетчером ресурсов](../powershell-azure-resource-manager.md).

### Шаг 1

Вход в Azure

	Login-AzureRmAccount

Вам будет предложено указать свои учетные данные для проверки подлинности.

### Шаг 2

Просмотрите подписки учетной записи.

	Get-AzureRmSubscription

### Шаг 3.

Выберите подписку Azure.

	Select-AzureRmSubscription -SubscriptionName "Name of subscription"

### Шаг 4.

Создайте группу ресурсов. Если вы используете существующую группу, пропустите этот шаг.

    New-AzureRmResourceGroup -Name appgw-RG -location "East Asia"

В качестве альтернативы можно также создать теги группы ресурсов для шлюза приложений:
	
	$resourceGroup = New-AzureRmResourceGroup -Name appgw-RG -Location "East Asia" -Tags @{Name = "testtag"; Value = "Application Gateway multiple site"} 

В диспетчере ресурсов Azure для всех групп ресурсов должно быть указано расположение. Это расположение используется в качестве расположения по умолчанию для всех ресурсов данной группы. Убедитесь, что во всех командах для создания шлюза приложений используется одна группа ресурсов.

В приведенном выше примере мы создали группу ресурсов appgw-RG в регионе "Восточная Азия".

>[AZURE.NOTE] Если вам нужно настроить пользовательскую пробу для шлюза приложений, ознакомьтесь со статьей [Создание пользовательской проверки для шлюза приложений с помощью PowerShell для диспетчера ресурсов Azure](application-gateway-create-probe-ps.md). Дополнительные сведения см. в статье [Обзор мониторинга работоспособности шлюза приложений](application-gateway-probe-overview.md).

## Создание виртуальной сети и подсети для шлюза приложений

В следующем примере показано создание виртуальной сети с помощью диспетчера ресурсов.

### Шаг 1

Назначьте диапазон адресов 10.0.0.0/24 переменной подсети, которая будет использоваться для создания виртуальной сети.

	$subnet = New-AzureRmVirtualNetworkSubnetConfig -Name subnet01 -AddressPrefix 10.0.0.0/24


### Шаг 2

Создайте виртуальную сеть с именем "appgwvnet" в группе ресурсов "appgw-rg" для региона запада США при помощи префикса 10.0.0.0/16 с подсетью 10.0.0.0/24.

	$vnet = New-AzureRmVirtualNetwork -Name appgwvnet -ResourceGroupName appgw-RG -Location "East Asia" -AddressPrefix 10.0.0.0/16 -Subnet $subnet


### Шаг 3.

Назначьте переменную подсети для дальнейших этапов создания шлюза приложений.

	$subnet=Get-AzureRmVirtualNetworkSubnetConfig -Name subnet01 -VirtualNetwork $vnet

## Создание общедоступного IP-адреса для конфигурации интерфейсной части

Создайте ресурс общедоступного IP-адреса "publicIP01" в группе ресурсов "appgw-rg" для западного региона США.

	$publicip = New-AzureRmPublicIpAddress -ResourceGroupName appgw-RG -name publicIP01 -location "East Asia" -AllocationMethod Dynamic

IP-адрес назначается шлюзу приложений при запуске службы.

## Создание конфигурации шлюза приложений

Перед созданием шлюза приложений необходимо настроить все элементы конфигурации. В ходе следующих шагов создаются необходимые элементы конфигурации для ресурса шлюза приложений.

### Шаг 1

Создайте конфигурацию IP шлюза приложений с именем "gatewayIP01". При запуске шлюз приложений получает IP-адрес из настроенной подсети, а после шлюз маршрутизирует сетевой трафик на IP-адреса из внутреннего пула IP-адресов. Помните, что для каждого экземпляра требуется отдельный IP-адрес.


	$gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $subnet


### Шаг 2

Настройте внутренние пулы IP-адресов pool1 и pool2 с IP-адресами 10.0.0.100, 10.0.0.101,10.0.0.102 и 10.0.0.103, 10.0.0.104, 10.0.0.105, соответственно.


	$pool1 = New-AzureRmApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 10.0.0.100, 10.0.0.101,10.0.0.102 
	$pool2 = New-AzureRmApplicationGatewayBackendAddressPool -Name pool02 -BackendIPAddresses 10.0.0.103, 10.0.0.104, 10.0.0.105

В этом примере используются два пула внутренних серверов для маршрутизации сетевого трафика на основе запрошенного сайта. Один пул принимает трафик от сайта contoso.com, а другой — от сайта fabrikam.com. Вам необходимо заменить приведенные выше IP-адреса и добавить IP-адреса конечных точек своего приложения. Вместо внутренних IP-адресов для серверных экземпляров можно также использовать общедоступные IP-адреса, полное доменное имя или сетевую карту виртуальной машины. Используйте параметр -BackendFQDNs в PowerShell, чтобы указать полные доменные имена, а не IP-адреса.

### Шаг 3.

Настройте параметры шлюзов приложений с именами poolsetting01 и poolsetting02 для балансировки нагрузки сетевого трафика в пуле серверной части. В этом примере вы настроите различные параметры пулов тыловых серверов. Для каждого пула тыловых серверов параметры можно настроить отдельно.


	$poolSetting01 = New-AzureRmApplicationGatewayBackendHttpSettings -Name "besetting01" -Port 80 -Protocol Http -CookieBasedAffinity Disabled -RequestTimeout 120
	$poolSetting02 = New-AzureRmApplicationGatewayBackendHttpSettings -Name "besetting02" -Port 80 -Protocol Http -CookieBasedAffinity Enabled -RequestTimeout 240

### Шаг 4.

Настройте внешний IP-адрес, используя конечную точку с общедоступным IP-адресом.

	$fipconfig01 = New-AzureRmApplicationGatewayFrontendIPConfig -Name "frontend1" -PublicIPAddress $publicip

### Шаг 5

Настройте внешний порт для шлюза приложений.

	$fp01 = New-AzureRmApplicationGatewayFrontendPort -Name "fep01" -Port 443

### Шаг 6

Настройте два SSL-сертификата для обоих веб-сайтов, которые будут обслуживаться в этом примере. Один сертификат предназначен для трафика contoso.com, а другой — для трафика fabrikam.com. Это должны быть сертификаты, выданные веб-сайтам центром сертификации. Самозаверяющие сертификаты поддерживаются, но их не рекомендуется использовать трафика в рабочих средах.

	$cert01 = New-AzureRmApplicationGatewaySslCertificate -Name  contosocert -CertificateFile <file path> -Password <password>
	$cert02 = New-AzureRmApplicationGatewaySslCertificate -Name fabrikamcert -CertificateFile <file path> -Password <password>


### Шаг 7

Настройте два прослушивателя для пары веб-сайтов в данном примере. На этом шаге следует настроить прослушиватели для общедоступного IP-адреса, порта и узла, используемых для получения входящего сетевого трафика. Параметр HostName необходим для поддержки нескольких сайтов. Ему нужно присвоить имя соответствующего веб-сайта, для которого будет приниматься трафик. Параметру RequireServerNameIndication следует присвоить значение true для веб-сайтов, которым требуется поддержка протокола SSL в сценарии с несколькими узлами. Если требуется поддержка протокола SSL, необходимо также указать SSL-сертификат, используемый для защиты трафика этого веб-приложения. Сочетание параметров прослушивателя FrontendIPConfiguration, FrontendPort и HostName должно быть уникальным. Каждый прослушиватель может поддерживать один сертификат.

	$listener01 = New-AzureRmApplicationGatewayHttpListener -Name "listener01" -Protocol Https -FrontendIPConfiguration $fipconfig01 -FrontendPort $fp01 -HostName "contoso11.com" -RequireServerNameIndication true  -SslCertificate $cert01
	$listener02 = New-AzureRmApplicationGatewayHttpListener -Name "listener02" -Protocol Https -FrontendIPConfiguration $fipconfig01 -FrontendPort $fp01 -HostName "fabrikam11.com" -RequireServerNameIndication true -SslCertificate $cert02

### Шаг 8

Создайте два параметра правила для пары веб-приложений в данном примере. Правило объединяет прослушиватели, серверные пулы и параметры протокола HTTP. На этом шаге следует настроить шлюз приложений для использования базового правила маршрутизации для каждого веб-сайта. Трафик, передаваемый на каждый веб-сайт, получает настроенный для него прослушиватель, после чего трафик направляется в заданный северный пул с использованием свойств, указанных в BackendHttpSettings.

	$rule01 = New-AzureRmApplicationGatewayRequestRoutingRule -Name "rule01" -RuleType Basic -HttpListener $listener01 -BackendHttpSettings $poolSetting01 -BackendAddressPool $pool1
	$rule02 = New-AzureRmApplicationGatewayRequestRoutingRule -Name "rule02" -RuleType Basic -HttpListener $listener02 -BackendHttpSettings $poolSetting02 -BackendAddressPool $pool2

### Шаг 9.

Настройте количество экземпляров и размер шлюза приложений.

	$sku = New-AzureRmApplicationGatewaySku -Name "Standard_Medium" -Tier Standard -Capacity 2

## Создание шлюза приложений

Создайте шлюз приложений со всеми объектами конфигурации, описанными выше.

	$appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-RG -Location "East Asia" -BackendAddressPools $pool1,$pool2 -BackendHttpSettingsCollection $poolSetting01, $poolSetting02 -FrontendIpConfigurations $fipconfig01 -GatewayIpConfigurations $gipconfig -FrontendPorts $fp01 -HttpListeners $listener01, $listener02 -RequestRoutingRules $rule01, $rule02 -Sku $sku -SslCertificates $cert01, $cert02 	


>[AZURE.IMPORTANT] Подготовка шлюза приложений — это длительная операция, которая может занять некоторое время.

## Получение DNS-имени шлюза приложений

Получите информацию о шлюзе приложений и соответствующее IP- или DNS-имя с помощью элемента PublicIPAddress, связанного со шлюзом приложений. DNS-имя шлюза приложений должно использоваться для создания записи CNAME, указывающей двум веб-приложениям на это DNS-имя. Использование записи A не рекомендуется, так как виртуальный IP-адрес может измениться после перезапуска приложения шлюза.
	
	Get-AzureRmPublicIpAddress -ResourceGroupName appgw-RG -name publicIP01
		
	Name                     : publicIP01
	ResourceGroupName        : appgw-RG
	Location                 : eastasia
	Id                       : /subscriptions/<subscription_id>/resourceGroups/appgw-RG/providers/Microsoft.Network/publicIPAddresses/publicIP01
	Etag                     : W/"00000d5b-54ed-4907-bae8-99bd5766d0e5"
	ResourceGuid             : 00000000-0000-0000-0000-000000000000
	ProvisioningState        : Succeeded
	Tags                     : 
	PublicIpAllocationMethod : Dynamic
	IpAddress                : xx.xx.xxx.xx
	PublicIpAddressVersion   : IPv4
	IdleTimeoutInMinutes     : 4
	IpConfiguration          : {
	                             "Id": "/subscriptions/<subscription_id>/resourceGroups/appgw-RG/providers/Microsoft.Network/applicationGateways/appgwtest/frontendIP
	                           Configurations/frontend1"
	                           }
	DnsSettings              : {
	                             "Fqdn": "00000000-0000-xxxx-xxxx-xxxxxxxxxxxx.cloudapp.net"
	                           }

<!---HONumber=AcomDC_0921_2016-->
<properties 
   pageTitle="Настройка шлюза приложений для разгрузки SSL с помощью диспетчера ресурсов Azure | Microsoft Azure"
   description="На этой странице приводятся инструкции по созданию шлюза приложений с разгрузкой SSL с помощью диспетчера ресурсов Azure."
   documentationCenter="na"
   services="application-gateway"
   authors="joaoma"
   manager="jdial"
   editor="tysonn"/>
<tags 
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="hero-article" 
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services" 
   ms.date="11/24/2015"
   ms.author="joaoma"/>

# Настройка шлюза приложений для разгрузки SSL с помощью диспетчера ресурсов Azure

> [AZURE.SELECTOR]
-[Azure Classic Powershell](application-gateway-ssl.md)
-[Azure Resource Manager Powershell](application-gateway-ssl-arm.md)

 Шлюз приложений можно настроить на завершение сеанса SSL в шлюзе, что позволит избежать выполнения дорогостоящей задачи SSL-шифрования на веб-ферме. Кроме того, разгрузка SSL упрощает процесс установки внешнего сервера и управление веб-приложением.


>[AZURE.IMPORTANT] Прежде чем приступить к работе с ресурсами Azure, обратите внимание на следующее. В настоящее время Azure поддерживает две модели развертывания: классическую и с помощью диспетчера ресурсов. Перед работой с любыми ресурсами Azure обязательно ознакомьтесь с [моделями и средствами развертывания](azure-classic-rm.md). Документацию по средствам см. на соответствующих вкладках в верхней части этой статьи. В этом документе рассказывается, как создать шлюз приложения с помощью диспетчера ресурсов Azure. Сведения об использовании классической модели см. в статье [Настройка шлюза приложений для разгрузки SSL с помощью модели классического развертывания Azure](application-gateway-ssl.md).



## Перед началом работы

1. Используя установщик веб-платформы, установите последнюю версию командлетов Azure PowerShell. Последнюю версию для установки можно загрузить в разделе **Windows PowerShell** на странице [Загрузки](http://azure.microsoft.com/downloads/).
2. Вы создадите виртуальную сеть и подсеть для шлюза приложения. Убедитесь, что подсеть не используется виртуальной машиной или облачным развертыванием. Сам шлюз приложений должен располагаться в подсети виртуальной сети.
3. Для использования шлюза приложений настраиваются существующие серверы или серверы, для которых в виртуальной сети созданы конечные точки или назначен открытый или виртуальный IP-адрес.

## Что необходимо для создания шлюза приложений?
 

- **Пул тыловых серверов**: список IP-адресов тыловых серверов. Указанные IP-адреса должны относиться к подсети виртуальной сети либо представлять собой общедоступные или виртуальные IP-адреса. 
- **Параметры пула тыловых серверов**: такие настройки каждого пула, как порт, протокол и сходство на основе файлов cookie. Эти параметры привязываются к пулу и применяются ко всем серверам в этом пуле.
- **Внешний порт**: общедоступный порт, открытый в шлюзе приложений. Трафик поступает в этот порт, а затем перенаправляется на один из внутренних серверов.
- **Прослушиватель**: у прослушивателя есть внешний порт, протокол (Http или Https с учетом регистра) и имя SSL-сертификата (при настройке разгрузки SSL). 
- **Правило**: правило связывает прослушиватель и пул тыловых серверов, а также определяет, в какой пул тыловых серверов следует направлять трафик, поступающий в определенный прослушиватель. Сейчас поддерживается только *основное* правило. *Основное* правило предусматривает циклический перебор при распределении нагрузки.

**Дополнительные примечания по настройке конфигурации:**

Чтобы настроить конфигурацию SSL-сертификатов, протокол в элементе **HttpListener** следует изменить на *Https* (с учетом регистра). Элемент **SslCertificate** нужно добавить к **HttpListener** со значением переменной, настроенной для SSL-сертификата. Внешний порт следует изменить на 443.

**Включение сходства на основе файлов cookie**: шлюз приложений можно настроить так, чтобы запросы от клиентского сеанса всегда направлялись на одну виртуальную машину веб-фермы. Это делается путем внедрения файлов cookie сеанса, что позволяет шлюзу перенаправлять трафик соответствующим образом. Чтобы включить сходство на основе файлов cookie, в элементе **BackendHttpSettings** присвойте параметру **CookieBasedAffinity** значение *Enabled*.

 
## Создание нового шлюза приложений

Разница между использованием классической модели развертывания Azure и диспетчера ресурсов Azure заключается в порядке создания шлюза приложения и элементов, которые нужно настроить.

При использовании диспетчера ресурсов все элементы, которые будут включены в единый ресурс шлюза приложений, сначала настраиваются по отдельности.


Ниже приведены пошаговые инструкции по созданию шлюза приложений.

1. Создание группы ресурсов для диспетчера ресурсов
2. Создание виртуальной сети, подсети и общедоступного IP-адреса для шлюза приложений
3. Создание объекта конфигурации шлюза приложений
4. Создание ресурса шлюза приложений


## Создание группы ресурсов для диспетчера ресурсов

Убедитесь, что выбран режим PowerShell для использования командлетов ARM. Дополнительные сведения см. в статье [Использование Windows PowerShell с диспетчером ресурсов](powershell-azure-resource-manager.md).

### Шаг 1

		PS C:\> Login-AzureRmAccount



### Шаг 2

Проверка подписок для учетной записи

		PS C:\> get-AzureRmSubscription 

Вам будет предложено пройти проверку подлинности с вашими учетными данными.<BR>

### Шаг 3. 

Выберите, какие подписки Azure будут использоваться. <BR>


		PS C:\> Select-AzureRmSubscription -Subscriptionid "GUID of subscription"


### Шаг 4.

Создайте группу ресурсов (пропустите этот шаг, если вы используете существующую группу).

    New-AzureRmResourceGroup -Name appgw-rg -location "West US"

Диспетчер ресурсов Azure требует, чтобы все группы ресурсов указывали расположение. Оно используется в качестве расположения по умолчанию для всех ресурсов данной группы. Убедитесь, что во всех командах по созданию шлюза приложений используется одна группа ресурсов.

В приведенном выше примере мы создали группу ресурсов под названием appgw-RG с расположением West US (запад США).

## Создание виртуальной сети и подсети для шлюза приложений

В следующем примере показано создание виртуальной сети с помощью диспетчера ресурсов.

### Шаг 1	
	
	$subnet = New-AzureRmVirtualNetworkSubnetConfig -Name subnet01 -AddressPrefix 10.0.0.0/24

Назначение диапазона адресов 10.0.0.0/24 в переменную подсети, которая будет использоваться для создания виртуальной сети

### Шаг 2.	
	$vnet = New-AzureRmVirtualNetwork -Name appgwvnet -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $subnet

Создание виртуальной сети с именем appgwvnet в группе ресурсов appw-rg для региона запада США при помощи префикса 10.0.0.0/16 с подсетью 10.0.0.0/24

### Шаг 3.

	$subnet=$vnet.Subnets[0]

Присвоение объекта подсети переменной $subnet для выполнения следующих действий.
	
## Создание общедоступного IP-адреса для конфигурации внешнего интерфейса

	$publicip = New-AzureRmPublicIpAddress -ResourceGroupName appgw-rg -name publicIP01 -location "West US" -AllocationMethod Dynamic

Создание ресурса общедоступного IP-адреса publicIP01 в группе ресурсов appw-rg для западного региона США.


## Создание объекта конфигурации шлюза приложений

### Шаг 1

	$gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $subnet

Создание IP-конфигурации шлюза приложений с именем gatewayIP01. При запуске шлюз приложений получает IP-адрес из настроенной подсети. Затем шлюз маршрутизирует сетевой трафик на IP-адреса пула серверных IP-адресов. Помните, что для каждого экземпляра потребуется отдельный IP-адрес.
 
### Шаг 2

	$pool = New-AzureRmApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 134.170.185.46, 134.170.188.221,134.170.185.50

На этом этапе будет выполнена настройка серверной части пула IP-адресов pool01 с IP-адресами 134.170.185.46, 134.170.188.221, 134.170.185.50. Эти адреса будут использоваться для получения сетевого трафика от внешней конечной точки с выделенным IP-адресом. Замените IP-адрес в предыдущем примере IP-адресом конечных точек вашего веб-приложения.

### Шаг 3.

	$poolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name poolsetting01 -Port 80 -Protocol Http -CookieBasedAffinity Enabled

Настройка параметров шлюза приложений poolsetting01 для балансировки нагрузки сетевого трафика в пуле серверной части.

### Шаг 4.

	$fp = New-AzureRmApplicationGatewayFrontendPort -Name frontendport01  -Port 443

На этом шаге настраивается IP-порт интерфейсной части с именем frontendport01, в данном случае для конечной точки с общедоступным IP-адресом.

### Шаг 5 

	$cert = New-AzureRmApplicationGatewaySslCertificate -Name cert01 -CertificateFile <full path for certificate file> -Password ‘<password>’

Настройка сертификата для SSL-соединения. Сертификат должен быть в формате PFX-файла, пароль к сертификату должен содержать от 4 до 12 символов.

### Шаг 6

	$fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig -Name fipconfig01 -PublicIPAddress $publicip

Создается конфигурация IP интерфейсной части fipconfig01 и связывается общедоступный IP-адрес с конфигурацией IP интерфейсной части.

### Шаг 7

	$listener = New-AzureRmApplicationGatewayHttpListener -Name listener01  -Protocol Https -FrontendIPConfiguration $fipconfig -FrontendPort $fp -SslCertificate $cert


На этом этапе создается прослушиватель listener01 и связывается порт интерфейсной части с конфигурацией IP интерфейсной части и сертификатом.

### Шаг 8 

	$rule = New-AzureRmApplicationGatewayRequestRoutingRule -Name rule01 -RuleType Basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool

Создаются правила маршрутизации rule01 для подсистемы балансировки нагрузки для настройки ее поведения.

### Шаг 9.

	$sku = New-AzureRmApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2

Настройка размера экземпляра шлюза приложений.

>[AZURE.NOTE] Значение параметра *InstanceCount* по умолчанию — 2 (максимальное значение — 10). Значение *GatewaySize* по умолчанию — Medium. Можно выбрать Standard\_Small, Standard\_Medium или Standard\_Large.

## Создание шлюза приложений с помощью командлета New-AzureApplicationGateway

	$appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appw-rg -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku -SslCertificates $cert

Будет создан шлюз приложений со всеми элементами конфигурации, описанными выше. В этом примере шлюз приложений называется appgwtest.


## Запуск шлюза приложений

Настроенный шлюз можно запустить с помощью командлета `Start-AzureRmApplicationGateway`. Выставление счетов для шлюза приложений начинается после запуска шлюза.


**Примечание**. Выполнение командлета `Start-AzureRmApplicationGateway` занимает до 15–20 минут.

В приведенном ниже примере шлюз приложений называется appgwtest, а группа ресурсов — app-rg.


### Шаг 1

Получите объект шлюза приложений и свяжите его с переменной $getgw.
 
	$getgw =  Get-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName app-rg

### Шаг 2
	 
Запустите шлюз приложений с помощью командлета `Start-AzureRmApplicationGateway`.

	 Start-AzureRmApplicationGateway -ApplicationGateway $getgw  

	

## Проверка состояния шлюза приложений

Проверьте состояние шлюза с помощью командлета `Get-AzureRmApplicationGateway`. Если командлет *Start-AzureApplicationGateway* на предыдущем этапе был выполнен успешно, параметр State должен получить значение *Running*.

	Get-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg

	Sku                               : Microsoft.Azure.Commands.Network.Models.PSApplicationGatewaySku
	GatewayIPConfigurations           : {gatewayip01}
	SslCertificates                   : {}
	FrontendIPConfigurations          : {frontendip01}
	FrontendPorts                     : {frontendport01}
	BackendAddressPools               : {pool01}
	BackendHttpSettingsCollection     : {setting01}
	HttpListeners                     : {listener01}
	RequestRoutingRules               : {rule01}
	OperationalState                  : 
	ProvisioningState                 : Succeeded
	GatewayIpConfigurationsText       : [
                                      {
                                        "Subnet": {
                                          "Id": "/subscriptions/###############################/resourceGroups/appgw-rg
                                    /providers/Microsoft.Network/virtualNetworks/vnet01/subnets/subnet01"
                                        },
                                        "ProvisioningState": "Succeeded",
                                        "Name": "gatewayip01",
                                        "Etag": "W/"ddb0408e-a54c-4501-a7f8-8487c3530bd7"",
                                        "Id": "/subscriptions/###############################/resourceGroups/appgw-rg/p
                                    roviders/Microsoft.Network/applicationGateways/appgwtest/gatewayIPConfigurations/gatewayip
                                    01"
                                      }
                                    ]
	SslCertificatesText               : []
	FrontendIpConfigurationsText      : [
                                      {
                                        "PrivateIPAddress": null,
                                        "PrivateIPAllocationMethod": "Dynamic",
                                        "Subnet": null,
                                        "PublicIPAddress": {
                                          "Id": "/subscriptions/###############################/resourceGroups/appgw-rg
                                    /providers/Microsoft.Network/publicIPAddresses/publicip01"
                                        },
                                        "ProvisioningState": "Succeeded",
                                        "Name": "frontendip01",
                                        "Etag": "W/"ddb0408e-a54c-4501-a7f8-8487c3530bd7"",
                                        "Id": "/subscriptions/###############################/resourceGroups/appgw-rg/p
                                    roviders/Microsoft.Network/applicationGateways/appgwtest/frontendIPConfigurations/frontend
                                    ip01"
                                      }
                                    ]
	FrontendPortsText                 : [
                                      {
                                        "Port": 80,
                                        "ProvisioningState": "Succeeded",
                                        "Name": "frontendport01",
                                        "Etag": "W/"ddb0408e-a54c-4501-a7f8-8487c3530bd7"",
                                        "Id": "/subscriptions/###############################/resourceGroups/appgw-rg/p
                                    roviders/Microsoft.Network/applicationGateways/appgwtest/frontendPorts/frontendport01"
                                      }
                                    ]
	BackendAddressPoolsText           : [
                                      {
                                        "BackendAddresses": [
                                          {
                                            "Fqdn": null,
                                            "IpAddress": "134.170.185.46"
                                          },
                                          {
                                            "Fqdn": null,
                                            "IpAddress": "134.170.188.221"
                                          },
                                          {
                                            "Fqdn": null,
                                            "IpAddress": "134.170.185.50"
                                          }
                                        ],
                                        "BackendIpConfigurations": [],
                                        "ProvisioningState": "Succeeded",
                                        "Name": "pool01",
                                        "Etag": "W/"ddb0408e-a54c-4501-a7f8-8487c3530bd7"",
                                        "Id": "/subscriptions/###############################/resourceGroups/appgw-rg/p
                                    roviders/Microsoft.Network/applicationGateways/appgwtest/backendAddressPools/pool01"
                                      }
                                    ]
	BackendHttpSettingsCollectionText : [
                                      {
                                        "Port": 80,
                                        "Protocol": "Http",
                                        "CookieBasedAffinity": "Disabled",
                                        "ProvisioningState": "Succeeded",
                                        "Name": "setting01",
                                        "Etag": "W/"ddb0408e-a54c-4501-a7f8-8487c3530bd7"",
                                        "Id": "/subscriptions/###############################/resourceGroups/appgw-rg/p
                                    roviders/Microsoft.Network/applicationGateways/appgwtest/backendHttpSettingsCollection/set
                                    ting01"
                                      }
                                    ]
	HttpListenersText                 : [
                                      {
                                        "FrontendIpConfiguration": {
                                          "Id": "/subscriptions/###############################/resourceGroups/appgw-rg
                                    /providers/Microsoft.Network/applicationGateways/appgwtest/frontendIPConfigurations/fronte
                                    ndip01"
                                        },
                                        "FrontendPort": {
                                          "Id": "/subscriptions/###############################/resourceGroups/appgw-rg
                                    /providers/Microsoft.Network/applicationGateways/appgwtest/frontendPorts/frontendport01"
                                        },
                                        "Protocol": "Http",
                                        "SslCertificate": null,
                                        "ProvisioningState": "Succeeded",
                                        "Name": "listener01",
                                        "Etag": "W/"ddb0408e-a54c-4501-a7f8-8487c3530bd7"",
                                        "Id": "/subscriptions/###############################/resourceGroups/appgw-rg/p
                                    roviders/Microsoft.Network/applicationGateways/appgwtest/httpListeners/listener01"
                                      }
                                    ]
	RequestRoutingRulesText           : [
                                      {
                                        "RuleType": "Basic",
                                        "BackendAddressPool": {
                                          "Id": "/subscriptions/###############################/resourceGroups/appgw-rg
                                    /providers/Microsoft.Network/applicationGateways/appgwtest/backendAddressPools/pool01"
                                        },
                                        "BackendHttpSettings": {
                                          "Id": "/subscriptions/###############################/resourceGroups/appgw-rg
                                    /providers/Microsoft.Network/applicationGateways/appgwtest/backendHttpSettingsCollection/s
                                    etting01"
                                        },
                                        "HttpListener": {
                                          "Id": "/subscriptions/###############################/resourceGroups/appgw-rg
                                    /providers/Microsoft.Network/applicationGateways/appgwtest/httpListeners/listener01"
                                        },
                                        "ProvisioningState": "Succeeded",
                                        "Name": "rule01",
                                        "Etag": "W/"ddb0408e-a54c-4501-a7f8-8487c3530bd7"",
                                        "Id": "/subscriptions/###############################/resourceGroups/appgw-rg/p
                                    roviders/Microsoft.Network/applicationGateways/appgwtest/requestRoutingRules/rule01"
                                      }
                                    ]
	ResourceGroupName                 : appgw-rg
	Location                          : westus
	Tag                               : {}
	TagsTable                         : 
	Name                              : appgwtest
	Etag                              : W/"ddb0408e-a54c-4501-a7f8-8487c3530bd7"
	Id                                : /subscriptions/###############################/resourceGroups/appgw-rg/providers/Microsoft.Network/applicationGateways/appgwtest




## Дальнейшие действия

Инструкции по настройке шлюза приложений для использования внутреннего балансировщика нагрузки см. в статье [Создание шлюза приложений с внутренним балансировщиком нагрузки](application-gateway-ilb.md).

Дополнительные сведения о параметрах балансировки нагрузки в целом см. в статьях:

- [Подсистема балансировщика нагрузки Azure](https://azure.microsoft.com/documentation/services/load-balancer/)
- [Azure Traffic Manager](https://azure.microsoft.com/documentation/services/traffic-manager/)

<!----HONumber=AcomDC_1203_2015-->
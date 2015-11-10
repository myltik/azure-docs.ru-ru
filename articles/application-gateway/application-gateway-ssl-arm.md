<properties 
   pageTitle="Настройка шлюза приложений для разгрузки SSL с помощью диспетчера ресурсов Azure | Microsoft Azure"
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
   ms.date="10/28/2015"
   ms.author="joaoma"/>

# Настройка шлюза приложений для разгрузки SSL с помощью диспетчера ресурсов Azure


 Шлюз приложений можно настроить на завершение сеанса SSL в шлюзе, что позволит избежать выполнения дорогостоящей задачи SSL-шифрования на веб-ферме. Кроме того, разгрузка SSL упрощает процесс установки внешнего сервера и управление веб-приложением.


>[AZURE.IMPORTANT]Прежде чем приступить к работе с ресурсами Azure, обратите внимание на то, что в настоящее время Azure имеет две модели развертывания: классическую и диспетчер ресурсов. Обязательно изучите [модели и средства развертывания](azure-classic-rm.md), прежде чем начинать работать с какими бы то ни было ресурсами Azure. Документацию для различных средств см. на соответствующих вкладках в верхней части этой статьи. В данном документе рассказывается, как создать шлюз приложения с помощью развертывания Диспетчера ресурсов Azure. Сведения об использовании классической модели см. в статье [Настройка шлюза приложений для разгрузки SSL с помощью PowerShell (классическая модель)](application-gateway-ssl.md).



## Перед началом работы

1. Используя установщик веб-платформы, установите последнюю версию командлетов Azure PowerShell. В разделе **Windows PowerShell** на [странице загрузки](http://azure.microsoft.com/downloads/) можно загрузить и установить последнюю версию.
2. Вы создадите виртуальную сеть и подсеть для шлюза приложения. Убедитесь, что подсеть не используется виртуальной машиной или облачным развертыванием. Сам шлюз приложений должен располагаться в подсети виртуальной сети.
3. Для использования шлюза приложений настраиваются либо существующие серверы, либо серверы, для которых в виртуальной сети созданы конечные точки или назначен открытый или виртуальный IP-адрес.

## Что необходимо для создания шлюза приложений?
 

- **Пул внутренних серверов**: список IP-адресов внутренних серверов. Указанные IP-адреса должны относиться к подсети виртуальной сети либо представлять собой общедоступные или виртуальные IP-адреса. 
- **Параметры пула внутренних серверов**: у каждого пула есть такие параметры, как порт, протокол и сходство на основе файлов cookie. Эти параметры привязываются к пулу и применяются ко всем серверам в этом пуле.
- **Внешний порт**: общий порт, открытый в шлюзе приложений. Трафик поступает в этот порт, а затем перенаправляется на один из внутренних серверов.
- **Прослушиватель**: имеет внешний порт, протокол (Http или Https, с учетом регистра) и имя SSL-сертификата (при настройке разгрузки SSL). 
- **Правило**: связывает прослушиватель и пул внутренних серверов, а также определяет, в какой пул внутренних серверов должен направляться трафик, попадающий в определенный прослушиватель. Сейчас поддерживается только *основное* правило. *Основное* правило предусматривает циклическое распределение нагрузки.

**Дополнительные примечания по настройке конфигурации:**

для настройки конфигурации SSL-сертификатов протокол в **HttpListener** следует изменить на *Https* (с учетом регистра). Элемент **SslCertificate** должен быть добавлен к **HttpListener** со значением переменной, настроенной для SSL-сертификата. Внешний порт следует изменить на 443.

**Включение соответствия на основе файле cookie**: шлюз приложений можно настроить таким образом, чтобы запросы от одного и того же клиентского сеанса всегда направлялись на одну и ту же виртуальную машину веб-фермы. Это делается путем внедрения файлов cookie сеанса, что позволяет шлюзу перенаправлять трафик соответствующим образом. Чтобы включить соответствия на основе файлов cookie, присвойте параметру **CookieBasedAffinity** значение *Enabled* (Включено) в элементе **BackendHttpSettings** (Настройки HTTP внутреннего сервера).

 
## Создание нового шлюза приложений

Разница между использованием Azure Classic и диспетчера ресурсов Azure состоит в порядке, в котором вы будете создавать шлюз приложений, и элементах, которые нужно будет настроить.

При использовании диспетчера ресурсов все элементы, которые планируется включить в состав шлюза приложений, будут настраиваться отдельно, а затем собираться в единый ресурс шлюза приложений.


Ниже приведены пошаговые инструкции по созданию шлюза приложений.

1. Создание группы ресурсов для диспетчера ресурсов.
2. Создание виртуальной сети, подсети и публичного IP-адреса для шлюза приложений
3. Создание объекта конфигурации шлюза приложений
4. Создать ресурс шлюза приложений.


## Создание группы ресурсов для диспетчера ресурсов

Убедитесь, что выбран режим PowerShell для использования командлетов ARM. Дополнительную информацию см. в статье [Использование Azure PowerShell с диспетчером ресурсов Azure](powershell-azure-resource-manager.md).

### Шаг 1

    Switch-AzureMode -Name AzureResourceManager

### Шаг 2

Войдите в свою учетную запись Azure.


    Add-AzureAccount

Вам будет предложено указать свои учетные данные для проверки подлинности.


### Шаг 3.

Выберите подписку Azure.

    Select-AzureSubscription -SubscriptionName "MySubscription"

Чтобы просмотреть перечень доступных подписок, воспользуйтесь командлетом Get-AzureSubscription.


### Шаг 4.

Создайте группу ресурсов (пропустите этот шаг, если вы используете существующую группу).

    New-AzureResourceGroup -Name appgw-rg -location "West US"

Диспетчер ресурсов Azure требует, чтобы все группы ресурсов указывали расположение. Оно используется в качестве расположения по умолчанию для всех ресурсов данной группы. Убедитесь, что во всех командах для создания шлюза приложений используется одна группа ресурсов.

В приведенном выше примере мы создали группу ресурсов под названием appgw-RG с расположением West US (запад США).

## Создание виртуальной сети и подсети для шлюза приложений

В следующем примере показано создание виртуальной сети с помощью диспетчера ресурсов.

### Шаг 1	
	
	$subnet = New-AzureVirtualNetworkSubnetConfig -Name subnet01 -AddressPrefix 10.0.0.0/24

Назначение диапазона адресов 10.0.0.0/24 в переменную подсети, которая будет использоваться для создания виртуальной сети

### Шаг 2.	
	$vnet = New-AzurevirtualNetwork -Name appgwvnet -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $subnet

Создание виртуальной сети с именем appgwvnet в группе ресурсов appw-rg для региона запада США при помощи префикса 10.0.0.0/16 с подсетью 10.0.0.0/24

### Шаг 3.

	$subnet=$vnet.Subnets[0]

Присвоение объекта подсети переменной $subnet для выполнения следующих действий.
	
## Создание общедоступного IP-адреса для конфигурации внешнего интерфейса

	$publicip = New-AzurePublicIpAddress -ResourceGroupName appgw-rg -name publicIP01 -location "West US" -AllocationMethod Dynamic

Создание ресурса общедоступного IP-адреса publicIP01 в группе ресурсов appw-rg для западного региона США.


## Создание объекта конфигурации шлюза приложений

### Шаг 1

	$gipconfig = New-AzureApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $subnet

Создание конфигурации IP шлюза приложений с именем gatewayIP01. При запуске шлюза приложений он получит IP-адрес из настроенной подсети и будет маршрутизировать сетевой трафик на IP-адреса пула серверных IP-адресов. Помните, что для каждого экземпляра потребуется отдельный IP-адрес.
 
### Шаг 2

	$pool = New-AzureApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 134.170.185.46, 134.170.188.221,134.170.185.50

На этом этапе будет выполнена настройка серверной части пула IP-адресов pool01 с IP-адресами 134.170.185.46, 134.170.188.221, 134.170.185.50. Эти адреса будут использоваться для получения сетевого трафика от внешней конечной точки с выделенным IP-адресом. Замените IP-адрес в предыдущем примере IP-адресом конечных точек вашего веб-приложения.

### Шаг 3.

	$poolSetting = New-AzureApplicationGatewayBackendHttpSettings -Name poolsetting01 -Port 80 -Protocol Http -CookieBasedAffinity Enabled

На этом этапе настраиваются параметры шлюза приложений poolsetting01 для балансировки нагрузки сетевого трафика в пуле серверной части.

### Шаг 4.

	$fp = New-AzureApplicationGatewayFrontendPort -Name frontendport01  -Port 443

На этом шаге настраивается IP-порт интерфейсной части с именем frontendport01, в данном случае для конечной точки с общедоступным IP-адресом.

### Шаг 5 

	$cert = New-AzureApplicationGatewaySslCertificate -Name cert01 -CertificateFile <full path for certificate file> -Password ‘<password>’

Настройка сертификата для SSL-соединения. Сертификат должен быть в формате PFX-файла, пароль к сертификату должен содержать от 4 до 12 символов.

### Шаг 6

	$fipconfig = New-AzureApplicationGatewayFrontendIPConfig -Name fipconfig01 -PublicIPAddress $publicip

Создается конфигурация IP интерфейсной части fipconfig01 и связывается общедоступный IP-адрес с конфигурацией IP интерфейсной части.

### Шаг 7

	$listener = New-AzureApplicationGatewayHttpListener -Name listener01  -Protocol Http -FrontendIPConfiguration $fipconfig -FrontendPort $fp -SslCertificate $cert


На этом этапе создается прослушиватель listener01 и связывается порт интерфейсной части с конфигурацией IP интерфейсной части и сертификатом.

### Шаг 8 

	$rule = New-AzureApplicationGatewayRequestRoutingRule -Name rule01 -RuleType Basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool

Создаются правила маршрутизации rule01 для подсистемы балансировки нагрузки для настройки ее поведения.

### Шаг 9.

	$sku = New-AzureApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2

Настраивается размер экземпляра шлюза приложений.

>[AZURE.NOTE]Значение параметра *InstanceCount* (Количество экземпляров) по умолчанию — 2, максимальное значение — 10. Значение *GatewaySize* (размер шлюза) по умолчанию — Medium (Средний). Можно выбрать Standard\_Small, Standard\_Medium или Standard\_Large.

## Создание шлюза приложений при помощи New-AzureApplicationGateway

	$appgw = New-AzureApplicationGateway -Name appgwtest -ResourceGroupName appw-rg -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku -SslCertificates $cert

Создается шлюз приложений со всеми элементами конфигурации, описанными выше. В этом примере шлюз приложений называется appgwtest.


## Запуск шлюза приложений

После настройки запустите шлюз с помощью командлета `Start-AzureApplicationGateway`. Выставление счетов для шлюза приложений начинается после запуска шлюза.


**Примечание.** Выполнение командлета `Start-AzureApplicationGateway` занимает до 15–20 минут.

В приведенном далее примере шлюз приложений называется appgwtest, а группа ресурсов называется app-rg.


### Шаг 1

Получение объекта шлюза приложений и связывание его с переменной $getgw.
 
	$getgw =  Get-AzureApplicationGateway -Name appgwtest -ResourceGroupName app-rg

### Шаг 2
	 
Запуск шлюза приложений с помощью `Start-AzureApplicationGateway`.

	 Start-AzureApplicationGateway -ApplicationGateway $getgw  

	

## Проверьте состояние шлюза приложений

Состояние шлюза можно проверить с помощью командлета `Get-AzureApplicationGateway`. Если командлет *Start-AzureApplicationGateway* на предыдущем этапе был выполнен успешно, параметр State (Состояние) должен получить значение *Running* (Работает), а параметры Vip и DnsName — действительные значения.

В данном примере показан рабочий шлюз приложений, готовый к приему трафика, отправляемого по адресу `http://<generated-dns-name>.cloudapp.net`.

	Get-AzureApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg

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


Указания по настройке шлюза приложений для использования с ILB см. в статье [Создание шлюза приложений с внутренней подсистемой балансировки нагрузки (ILB)](application-gateway-ilb.md).

Дополнительные сведения о параметрах балансировки нагрузки в целом см. в статьях:

- [Подсистема балансировщика нагрузки Azure](https://azure.microsoft.com/documentation/services/load-balancer/)
- [Диспетчер трафика Azure](https://azure.microsoft.com/documentation/services/traffic-manager/)

<!---HONumber=Nov15_HO2-->
---
title: "Как использовать управление API Azure в виртуальных сетях со шлюзом приложений | Документация Майкрософт"
description: "Узнайте, как установить и настроить управление API Azure во внутренней виртуальной сети с интерфейсным шлюзом приложений (WAF)"
services: api-management
documentationcenter: 
author: solankisamir
manager: kjoshi
editor: antonba
ms.assetid: a8c982b2-bca5-4312-9367-4a0bbc1082b1
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2017
ms.author: sasolank
translationtype: Human Translation
ms.sourcegitcommit: a87349518f7494dda49e52ed160509a4ffeb7775
ms.openlocfilehash: a58ec5f6d62d2b48d6cf85d997b2deac95310505


---
# <a name="integrate-api-management-in-an-internal-vnet-with-application-gateway"></a>Интеграция управления API во внутреннюю сеть со шлюзом приложений 

##<a name="overview"> </a> Обзор
 
Служба управления API может работать в виртуальной сети в режиме внутренней сети, и тогда она будет доступна только из этой виртуальной сети. Шлюз приложений Azure — это служба PaaS, выполняющая функции подсистемы балансировки нагрузки на сетевом уровне 7. Это служба обратного прокси-сервера, которая содержит также брандмауэр веб-приложения (WAF).

Объединяя возможности службы управления API, работающей во внутренней виртуальной сети, и интерфейса шлюза приложений, вы можете реализовать следующие сценарии.

* Использовать один ресурс управления API одновременно и для внешних, и для внутренних потребителей.
* Использовать один ресурс управления API, определив для него в службе управления API подмножество API-интерфейсов, доступных для внешних потребителей.
* Создать простой способ включать и отключать доступ из Интернета к управлению API. 

##<a name="scenario"> </a> Сценарий
Здесь мы рассмотрим, как можно использовать одну службу управления API одновременно для внутренних и внешних потребителей и как сделать ее единым интерфейсом для локальных и облачных API. После этого вы сможете предоставить некоторое подмножество этих API-интерфейсов (выделены зеленым цветом) для внешнего использования, применив функцию шлюза приложений PathBasedRouting.

В этой схеме работы все API-интерфейсы управляются только из виртуальной сети. Внутренние потребители (выделены оранжевым цветом) могут обращаться и к внутренним, и к внешним API. Внутренние потребители получают дополнительные преимущества благодаря тому, что трафик никогда не выходит в Интернет, а канал Express Route обеспечивает высокую скорость.

![Маршрут URL-адреса](./media/api-management-using-with-vnet/api-management-howto-integrate-internal-vnet-appgateway.png)

## <a name="before-you-begin"> </a> Перед началом работы

1. Установите последнюю версию командлетов Azure PowerShell, используя установщик веб-платформы. Последнюю версию можно загрузить и установить в разделе **Windows PowerShell** на [странице загрузок](https://azure.microsoft.com/downloads/).
2. Создайте виртуальную сеть и отдельные подсети в ней для службы управления API и для шлюза приложений. 
3. Если для этой виртуальной сети вы намерены использовать настраиваемый DNS-сервер, создайте его до начала развертывания, а также убедитесь, что создаваемая в новой подсети виртуальная машина может разрешать адреса всех конечных точек служб Azure.

## <a name="what-is-required-to-create-an-integration-between-api-management-and-application-gateway"></a>Что нужно для того, чтобы интегрировать управление API со шлюзом приложений?

* **Пул тыловых серверов**. Это внутренний виртуальный IP-адрес службы управления API.
* **Параметры внутреннего пула серверов**. Каждый пул имеет такие параметры, как порт, протокол и сходство на основе файлов cookie. Эти параметры привязываются к пулу и применяются ко всем серверам в этом пуле.
* **Интерфейсный порт**. Общедоступный порт, открытый в шлюзе приложений. Трафик поступает на этот порт, а затем перенаправляется на один из тыловых серверов.
* **Прослушиватель**. У прослушивателя есть интерфейсный порт, протокол (Http или Https — с учетом регистра) и имя SSL-сертификата (в случае настройки разгрузки SSL).
* **Правило**. Правило связывает прослушиватель и внутренний пул серверов, а также определяет, в какой внутренний пул серверов следует направлять трафик, поступающий на определенный прослушиватель.
* **Пользовательские проверки работоспособности**. Шлюз приложений по умолчанию использует проверки на основе IP-адреса, чтобы найти активные серверы в пуле BackendAddressPool. Служба управления API отвечает только на те запросы, которые имеют правильный заголовок узла, поэтому стандартные проверки не дают результата. Вам следует определить пользовательскую проверку работоспособности, чтобы шлюз приложений мог проверять работоспособность службы и передавать в нее запросы.
* **Сертификат для пользовательского домена**. Чтобы осуществлять доступ из Интернета в службу управления API, выполните сопоставление CNAME имени узла с DNS-именем интерфейса шлюза приложений. Эти же имя узла и сертификат применяются и для управления API. Таким образом, управление API будет считать запросы через шлюз приложений допустимыми и будет отвечать на них.
## <a name="overview-steps"> </a> Действия по интеграции управления API со шлюзом приложений 

1. Создание группы ресурсов для диспетчера ресурсов.
2. Создание виртуальной сети, подсети и общедоступного IP-адреса для шлюза приложений. Создайте еще одну подсеть для управления API.
3. Создайте службу управления API в режиме внутренней виртуальной сети в той подсети, которую вы создали ранее.
4. Занесите в настройки службы управления API пользовательское доменное имя.
5. Создание объекта конфигурации шлюза приложений.
6. Создайте ресурс шлюза приложений.
7. Создайте сопоставление CNAME имени узла прокси-сервера управления API с общедоступным DNS-именем ресурса шлюза приложений.

## <a name="create-a-resource-group-for-resource-manager"></a>Создание группы ресурсов для диспетчера ресурсов

Убедитесь, что у вас установлена последняя версия Azure PowerShell. Дополнительные сведения см. в статье [Использование Windows PowerShell с диспетчером ресурсов](../powershell-azure-resource-manager.md).

### <a name="step-1"></a>Шаг 1

Вход в Azure

```powershell
Login-AzureRmAccount
```

Вам будет предложено указать свои учетные данные для проверки подлинности.<BR>

### <a name="step-2"></a>Шаг 2

Проверьте и выберите подписки для учетной записи.

```powershell
Get-AzureRmSubscription -Subscriptionid "GUID of subscription" | Select-AzureRmSubscription
```

### <a name="step-3"></a>Шаг 3.

Создайте группу ресурсов. Если вы используете существующую группу, пропустите этот шаг.

```powershell
New-AzureRmResourceGroup -Name apim-appGw-RG -Location "West US"
```
В диспетчере ресурсов Azure для всех групп ресурсов должно быть указано расположение. Оно используется в качестве расположения по умолчанию для всех ресурсов данной группы. Убедитесь, что во всех командах для создания шлюза приложений используется одна группа ресурсов.

## <a name="create-a-virtual-network-and-a-subnet-for-the-application-gateway"></a>Создание виртуальной сети и подсети для шлюза приложений.

В следующем примере показано создание виртуальной сети с помощью диспетчера ресурсов.

### <a name="step-1"></a>Шаг 1

Назначьте переменной subnet диапазон адресов 10.0.0.0/24. Эта переменная будет использоваться для шлюза приложений при создании виртуальной сети.

```powershell
$appgatewaysubnet = New-AzureRmVirtualNetworkSubnetConfig -Name appgateway01 -AddressPrefix 10.0.0.0/24
```

### <a name="step-2"></a>Шаг 2

Назначьте переменной subnet диапазон адресов 10.0.1.0/24. Эта переменная будет использоваться для управления API при создании виртуальной сети.

```powershell
$apimsubnet = New-AzureRmVirtualNetworkSubnetConfig -Name apim01 -AddressPrefix 10.0.1.0/24
```

### <a name="step-3"></a>Шаг 3.

Создайте виртуальную сеть с именем **appgwvnet** в группе ресурсов **apim-appGw-RG** для региона "Западная часть США", назначив ей префикс 10.0.0.0/16. Создайте в ней подсети 10.0.0.0/24 и 10.0.1.0/24.

```powershell
$vnet = New-AzureRmVirtualNetwork -Name appgwvnet -ResourceGroupName apim-appGw-RG -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $appgatewaysubnet,$apimsubnet
```

### <a name="step-4"></a>Шаг 4.

Назначьте переменную для подсети, которая будет использоваться далее.

```powershell
$appgatewaysubnetdata=$vnet.Subnets[0]
$apimsubnetdata=$vnet.Subnets[1]
```
## <a name="create-an-api-management-service-in-internal-vnet-mode"></a>Создание службы управления API в режиме внутренней виртуальной сети

В следующем примере демонстрируется создание службы управления API во внутренней виртуальной сети.

### <a name="step-1"></a>Шаг 1
Создайте объект виртуальной сети для управления API, используя созданную выше подсеть $apimsubnetdata.

```powershell
$apimVirtualNetwork = New-AzureRmApiManagementVirtualNetwork -Location "West US" -SubnetResourceId $apimsubnetdata.Id
```
### <a name="step-2"></a>Шаг 2
Создайте службу управления API в виртуальной сети.

```powershell
$apimService = New-AzureRmApiManagement -ResourceGroupName "apim-appGw-RG" -Location "West US" -Name "ContosoApi" -Organization Contoso -AdminEmail admin@contoso.com -VirtualNetwork $apimVirtualNetwork -VpnType "Internal" -Sku "Premium"
```
Когда завершится выполнение предыдущей команды, выполните инструкции из раздела [о настройке DNS для доступа к службе управления API во внутренней виртуальной сети][api-management-using-with-internal-vnet.md#apim-dns-configuration].

## <a name="update-api-management-service-with-custom-domain-name"></a>Занесение пользовательского доменного имени в настройки службы управления API

Теперь мы применим пользовательское доменное имя для прокси-сервера конечной точки службы управления API, к которому хотим предоставить доступ из Интернета.

### <a name="step-1"></a>Шаг 1
Загрузите сертификат с закрытым ключом, который подтверждает право создания пользовательского доменного имени для вашего домена, например `*.contoso.net`. 

```powershell
$certUploadResult = Import-AzureRmApiManagementHostnameCertificate -ResourceGroupName "apim-appGw-RG" -Name "ContosoApi" -HostnameType "Proxy" -PfxPath <full path to .pfx file> -PfxPassword <password for certificate file>
```

### <a name="step-2"></a>Шаг 2
После загрузки сертификата, который подтверждает наши права на домен `*.contoso.net`, мы можем настроить для прокси-сервера объект конфигурации имени узла и назначить ему имя узла `api.contoso.net`. 

```powershell
$proxyHostnameConfig = New-AzureRmApiManagementHostnameConfiguration -CertificateThumbprint $certUploadResult.Thumbprint -Hostname "api.contoso.net"
$result = Set-AzureRmApiManagementHostnames -Name "ContosoApi" -ResourceGroupName "apim-appGw-RG" –PortalHostnameConfiguration $proxyHostnameConfig
```

## <a name="create-a-public-ip-address-for-the-front-end-configuration"></a>Создание общедоступного IP-адреса для конфигурации интерфейсной части

Создайте ресурс общедоступного IP-адреса с именем **publicIP01** в группе ресурсов **apim-appGw-RG** для региона "Западная часть США".

```powershell
$publicip = New-AzureRmPublicIpAddress -ResourceGroupName apim-appGw-RG -name publicIP01 -location "West US" -AllocationMethod Dynamic
```

IP-адрес назначается шлюзу приложений при запуске службы.

## <a name="create-application-gateway-configuration"></a>Создание конфигурации шлюза приложений

Перед созданием шлюза приложений необходимо настроить все элементы конфигурации. В ходе следующих шагов создаются необходимые элементы конфигурации для ресурса шлюза приложений.

### <a name="step-1"></a>Шаг 1

Создайте конфигурацию IP-адресов шлюза приложений с именем **gatewayIP01**. При запуске шлюз приложений получает IP-адрес из настроенной подсети. Затем шлюз маршрутизирует сетевой трафик на IP-адреса из внутреннего пула IP-адресов. Помните, что для каждого экземпляра требуется отдельный IP-адрес.

```powershell
$gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $appgatewaysubnetdata
```

### <a name="step-2"></a>Шаг 2

Настройте интерфейсный порт IP для конечной точки с общедоступным IP-адресом. Это порт, к которому подключаются пользователи.

```powershell
$fp01 = New-AzureRmApplicationGatewayFrontendPort -Name 'port01'  -Port 443
```
### <a name="step-3"></a>Шаг 3.

Настройте внешний IP-адрес, используя конечную точку с общедоступным IP-адресом.

```powershell
$fipconfig01 = New-AzureRmApplicationGatewayFrontendIPConfig -Name "frontend1" -PublicIPAddress $publicip
```

### <a name="step-4"></a>Шаг 4.

Настройте сертификат для шлюза приложений. Этот сертификат используется для шифрования и расшифровки трафика на шлюзе приложений.

```powershell
$cert = New-AzureRmApplicationGatewaySslCertificate -Name cert01 -CertificateFile <full path to .pfx file> -Password <password for certificate file>
```

### <a name="step-5"></a>Шаг 5

Создайте прослушиватель HTTP для шлюза приложений. Назначьте используемую конфигурацию IP внешнего интерфейса, порт и сертификат SSL.

```powershell
$listener = New-AzureRmApplicationGatewayHttpListener -Name listener01 -Protocol Https -FrontendIPConfiguration $fipconfig01 -FrontendPort $fp01 -SslCertificate $cert
```

### <a name="step-6"></a>Шаг 6

Здесь мы создадим пользовательскую проверку для конечной точки `ContosoApi` прокси-сервера службы управления API. По умолчанию на всех службах управления API для конечной точки проверки работоспособности используется путь `/status-0123456789abcdef`. Когда в общедоступной службе Azure создается служба `contosoapi.azure-api.net`, для ее прокси-сервера по умолчанию назначается имя узла `contosoapi`.

```powershell
$apimprobe = New-AzureRmApplicationGatewayProbeConfig -Name apimproxyprobe -Protocol Https -HostName "contosoapi.azure-api.net" -Path "/status-0123456789abcdef" -Interval 30 -Timeout 120 -UnhealthyThreshold 8
```

### <a name="step-7"></a>Шаг 7

Передайте используемый сертификат в ресурсы внутреннего пула с поддержкой протокола SSL.

```powershell
$authcert = New-AzureRmApplicationGatewayAuthenticationCertificate -Name 'whitelistcert1' -CertificateFile <full path to .cer file>
```

### <a name="step-8"></a>Шаг 8

Настройте параметр шлюза приложений **apimPoolSetting** для трафика в пуле тыловых серверов. На этом шаге также настраивается время ожидания ответа пула внутренних серверов на запрос шлюза приложений. При достижении этого времени ожидания шлюз приложений отменяет запрос. Это значение отличается от времени ожидания проверки, которое относится только к ответу пула внутренних серверов на проверку работоспособности.

```powershell
$apimPoolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name apimPoolSetting -Port 443 -Protocol Https -CookieBasedAffinity Disabled -Probe $apimprobe -AuthenticationCertificates $authcert -RequestTimeout 180
```

### <a name="step-9"></a>Шаг 9.

Настройте пул IP-адресов серверной части с именем **apimbackend**, указав для него внутренний виртуальный IP-адрес, созданный ранее для службы управления API.

```powershell
$apimProxyBackendPool = New-AzureRmApplicationGatewayBackendAddressPool -Name apimbackend -BackendIPAddresses $apimService.StaticIPs[0]
```

### <a name="step-10"></a>Шаг 10
Настройте пути URL-правил для пулов тыловых серверов. Для управления API можно настроить несколько API-интерфейсов, например `Echo API (/echo/), Calculator API (/calc/) etc.`. Мы можем разрешить доступ из Интернета только к интерфейсу `Echo API`. 

В следующем примере создается простое правило для маршрутизации трафика от пути /echo/ к пулу тыловых серверов apimProxyBackendPool.

```powershell
$echoapiRule = New-AzureRmApplicationGatewayPathRuleConfig -Name "externalapis" -Paths "/echo/*" -BackendAddressPool $apimProxyBackendPool -BackendHttpSettings $apimPoolSetting

$urlPathMap = New-AzureRmApplicationGatewayUrlPathMapConfig -Name "urlpathmap" -PathRules $echoapiRule -DefaultBackendAddressPool $apimProxyBackendPool -DefaultBackendHttpSettings $apimPoolSetting
```

Предыдущий шаг гарантирует, что через шлюз приложений будут проходить только запросы для пути /echo. В ответ на запросы из Интернета на другие API-интерфейсы, настроенные в управлении API, шлюз приложений будет возвращать ошибку 404. 

### <a name="step-11"></a>Шаг 11

Создайте параметр правила. Этот шаг позволяет настроить шлюз приложений для маршрутизации на основе URL-путей.

```powershell
$rule01 = New-AzureRmApplicationGatewayRequestRoutingRule -Name "rule1" -RuleType PathBasedRouting -HttpListener $listener -UrlPathMap $urlPathMap
```

### <a name="step-12"></a>Шаг 12

Настройте количество экземпляров и размер шлюза приложений. Здесь мы используем [SKU WAF] [../application-gateway/application-gateway-webapplicationfirewall-overview.md], чтобы повысить уровень безопасности для ресурса управления API.

```powershell
$sku = New-AzureRmApplicationGatewaySku -Name WAF_Medium -Tier WAF -Capacity 2
```

### <a name="step-13"></a>Шаг 13

Для WAF мы настроим режим защиты.
```powershell
$config = New-AzureRmApplicationGatewayWebApplicationFirewallConfiguration -Enabled $true -FirewallMode "Prevention"
```

## <a name="create-application-gateway"></a>Создание шлюза приложений

Создайте шлюз приложений со всеми объектами конфигурации, описанными выше.

```powershell
$appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName apim-appGw-RG -Location "West US" -BackendAddressPools $apimProxyBackendPool -BackendHttpSettingsCollection $apimPoolSetting  -FrontendIpConfigurations $fipconfig01 -GatewayIpConfigurations $gipconfig -FrontendPorts $fp01 -HttpListeners $listener -UrlPathMaps $urlPathMap -RequestRoutingRules $rule01 -Sku $sku -WebApplicationFirewallConfig $config -SslCertificates $cert -AuthenticationCertificates $authcert -Probes $apimprobe
```

## <a name="cname-the-api-management-proxy-hostname-to-public-dns-name-of-application-gateway-resource"></a>Создание сопоставления CNAME имени узла прокси-сервера управления API с общедоступным DNS-именем ресурса шлюза приложений

После создания шлюза следует настроить внешний интерфейс для обмена данными. Если вы используете общедоступный IP-адрес, шлюзу приложений требуется динамически назначаемое непонятное имя DNS. Чтобы настроить запись CNAME интерфейсного IP-адреса, получите сведения о шлюзе приложений и соответствующее IP- или DNS-имя с помощью элемента PublicIPAddress, связанного со шлюзом приложений. В записи CNAME следует использовать DNS-имя шлюза приложений, и она должна перенаправлять настроенное выше имя узла прокси-сервера `api.contoso.net` на это DNS-имя. Использование записи A не рекомендуется, так как виртуальный IP-адрес может измениться после перезапуска приложения шлюза.

```powershell
Get-AzureRmPublicIpAddress -ResourceGroupName apim-appGw-RG -Name publicIP01
```

##<a name="summary"> </a> Сводка
Служба управления API Azure в виртуальной сети предоставляет шлюз, выполняющий функцию единого интерфейса для управления доступом ко всем API-интерфейсам, как локальным, так и облачным. Она позволяет получить подробную информацию о том, кто и как использует API-интерфейсы. Интеграция шлюза приложений с управлением API дает вам дополнительную гибкость при предоставлении доступа к API-интерфейсам через Интернет, а также позволяет использовать брандмауэр веб-приложения в качестве интерфейса для экземпляра управления API.

##<a name="next-steps"> </a> Дальнейшие действия
* Дополнительные сведения о шлюзе приложений Azure:
  * [Обзор шлюза приложений](../application-gateway/application-gateway-introduction.md)
  * [Application Gateway Web Application Firewall (preview)](../application-gateway/application-gateway-webapplicationfirewall-overview.md) (Брандмауэр веб-приложения шлюза приложений (предварительная версия))
* Дополнительные сведения об управлении API в виртуальной сети:
  * [How to use Azure API Management with virtual networks](api-management-using-with-vnet.md) (Использование управления API Azure в виртуальных сетях)





<!--HONumber=Feb17_HO1-->



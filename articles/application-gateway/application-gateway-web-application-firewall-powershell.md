<properties
   pageTitle="Настройка брандмауэра веб-приложения на новом или существующем шлюзе приложений | Microsoft Azure"
   description="Эта статья содержит рекомендации о том, как приступить к работе с брандмауэром веб-приложения на существующем или новом шлюзе приложений."
   documentationCenter="na"
   services="application-gateway"
   authors="georgewallace"
   manager="carmonm"
   editor="tysonn"/>
<tags
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/26/2016"
   ms.author="gwallace"/>

# Настройка брандмауэра веб-приложения на новом или существующем шлюзе приложений

> [AZURE.SELECTOR]
- [Портал Azure](application-gateway-web-application-firewall-portal.md)
- [PowerShell и диспетчер ресурсов Azure](application-gateway-web-application-firewall-powershell.md)

Шлюз приложений — это балансировщик нагрузки уровня 7. Он отвечает за отработку отказов и эффективную маршрутизацию HTTP-запросов между разными серверами (облачными и локальными). Шлюз приложений выполняет многие функции контроллера доставки приложений (ADC), включая балансировку нагрузки HTTP, определение сходства сеансов на основе файлов cookie, разгрузку SSL, выполнение пользовательской проверки работоспособности, поддержку нескольких сайтов и т. д. Полный список поддерживаемых функций представлен в обзоре шлюза приложений.

Брандмауэр веб-приложения (WAF) в шлюзе приложений Azure защищает веб-приложения от таких распространенных сетевых атак, как атаки путем внедрения кода SQL, атаки межсайтовых сценариев и захваты сеанса.

В следующей статье рассказывается, как [добавить брандмауэр веб-приложения в существующий шлюз приложения](#add-web-application-firewall-to-an-existing-application-gateway) и как [создать шлюз приложений с брандмауэром веб-приложения](#create-an-application-gateway-with-web-application-firewall).

![Изображение для сценария][scenario]

## Различия в конфигурации WAF

Если вы прочитали раздел [Создание шлюза приложений с помощью PowerShell](application-gateway-create-gateway-arm.md), то уже понимаете, какие параметры SKU нужно настроить при создании шлюза приложений. При настройке SKU для шлюза приложений доступны дополнительные параметры, относящиеся к WAF. Какие-либо изменения самого шлюза приложений не требуются.

**SKU**. Обычный шлюз приложений без WAF поддерживает размеры **Standard\_Small**, **Standard\_Medium** и **Standard\_Large**. После добавления WAF становятся доступны еще два SKU, **WAF\_Medium** и **WAF\_Large**. WAF не поддерживается в шлюзах приложения уровня "Мелкий".

**Уровень**. Доступные значения: **Стандартный** или **WAF**. При использовании брандмауэра веб-приложения требуется выбрать уровень **WAF**.

**Режим**. Этот параметр задает режим WAF. Допустимые значения: **Обнаружение** и **Предотвращение**. Если WAF работает в режиме обнаружения, все угрозы заносятся в файл журнала. В режиме предотвращения события по-прежнему регистрируются в журнале, но злоумышленник получает от шлюза приложений ответ "403 — не авторизовано".

## Добавление брандмауэра веб-приложения в существующий шлюз приложений

Убедитесь, что у вас установлена последняя версия Azure PowerShell. Дополнительные сведения см. в статье [Использование Windows PowerShell с диспетчером ресурсов](../powershell-azure-resource-manager.md).

### Шаг 1

Войдите в свою учетную запись Azure.

    Login-AzureRmAccount

### Шаг 2

Выберите подписку для этого сценария.

    Select-AzureRmSubscription -SubscriptionName "<Subscription name>"

### Шаг 3.

Получите шлюз, в который следует добавить брандмауэр веб-приложения.

    $gw = Get-AzureRmApplicationGateway -Name "AdatumGateway" -ResourceGroupName "MyResourceGroup"


### Шаг 4.

Настройте SKU брандмауэра веб-приложения. Доступные размеры: **WAF\_Large** и **WAF\_Medium**. При использовании брандмауэра веб-приложения должен быть задан уровень **WAF**.

    $gw | Set-AzureRmApplicationGatewaySku -Name WAF_Large -Tier WAF

### Шаг 5

Настройте параметры WAF, как указано в следующем примере.

Для параметра **WafMode** можно указать значение "Обнаружение" или "Предотвращение".

    $config = Add-AzureRmApplicationGatewayWafConfig -Enabled $true -WafMode "Prevention" -ApplicationGateway $gw

### Шаг 6

Примените к шлюзу приложений параметры, определенные на предыдущем шаге.

    Set-AzureRmApplicationGateway -ApplicationGateway $gw

Эта команда обновляет шлюз приложений, добавляя брандмауэр веб-приложения. Рекомендуется ознакомиться с [диагностикой шлюза приложений](application-gateway-diagnostics.md), чтобы понять, как просматривать журналы шлюза приложений. Из-за особенностей системы безопасности WAF необходимо регулярно просматривать журналы, чтобы понимать состояние безопасности веб-приложений.

## Создание шлюза приложений с брандмауэром веб-приложения

Ниже от начала до конца описывается процесс создания шлюза приложений с брандмауэром веб-приложения.

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

    Select-AzureRmsubscription -SubscriptionName "<Subscription name>"

### Шаг 4.

Создайте группу ресурсов. Если вы используете существующую группу, пропустите этот шаг.

    New-AzureRmResourceGroup -Name appgw-rg -Location "West US"

В диспетчере ресурсов Azure для всех групп ресурсов должно быть указано расположение. Это расположение используется в качестве расположения по умолчанию для всех ресурсов данной группы. Убедитесь, что во всех командах для создания шлюза приложений используется одна группа ресурсов.

В приведенном выше примере мы создали группу ресурсов appgw-RG в расположении West US (западная часть США).

>[AZURE.NOTE] Если вам нужно настроить пользовательскую пробу для шлюза приложений, ознакомьтесь со статьей [Создание пользовательской проверки для шлюза приложений с помощью PowerShell для диспетчера ресурсов Azure](application-gateway-create-probe-ps.md). Дополнительные сведения см. в статье [Обзор мониторинга работоспособности шлюза приложений](application-gateway-probe-overview.md).

### Шаг 5

Назначьте диапазон адресов подсети для самого шлюза приложений.

    $gwSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name 'appgwsubnet' -AddressPrefix 10.0.0.0/24

> [AZURE.NOTE] У подсети для приложения должно быть не менее 28 битов маски. Это значение позволяет оставить в подсети 10 доступных адресов для экземпляров шлюза приложений. Если подсеть будет меньше, то вы не сможете в будущем добавить экземпляры шлюза приложений.

### Шаг 6

Назначьте диапазон адресов для внутреннего пула адресов.

    $nicSubnet = New-AzureRmVirtualNetworkSubnetConfig  -Name 'appsubnet' -AddressPrefix 10.0.2.0/24

### Шаг 7

Создайте виртуальную сеть с указанными выше подсетями в группе ресурсов, созданной на шаге [Создание группы ресурсов](#create-the-resource-group).

    $vnet = New-AzureRmvirtualNetwork -Name 'appgwvnet' -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $gwSubnet, $nicSubnet

### Шаг 8

Получите ресурс виртуальной сети и ресурсы подсетей, которые будут использоваться при выполнении следующих действий.

    $vnet = Get-AzureRmvirtualNetwork -Name 'appgwvnet' -ResourceGroupName appgw-rg
    $gwSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name 'appgwsubnet' -VirtualNetwork $vnet
    $nicSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name 'appsubnet' -VirtualNetwork $vnet

### Шаг 9.

Создайте ресурс общедоступного IP-адреса для шлюза приложений. Этот общедоступный IP-адрес используется на одном из следующих шагов.

    $publicip = New-AzureRmPublicIpAddress -ResourceGroupName appgw-rg -name 'appgwpip' -Location "West US" -AllocationMethod Dynamic

> [AZURE.IMPORTANT] Шлюз приложений не поддерживает использование общедоступного IP-адреса, для которого метка домена была определена при создании. Поддерживается только общедоступный IP-адрес с динамически создаваемой меткой домена. Если для шлюза приложений требуется понятное DNS-имя, то рекомендуется использовать в качестве псевдонима запись CNAME.

### Шаг 10

Перед созданием шлюза приложений необходимо настроить все элементы конфигурации. В ходе следующих шагов создаются необходимые элементы конфигурации для ресурса шлюза приложений.

Создайте конфигурацию IP шлюза приложений, в которой указано, какую подсеть использует шлюз приложений. При запуске шлюз приложений получает IP-адрес из настроенной подсети. Затем шлюз маршрутизирует сетевой трафик на IP-адреса из внутреннего пула IP-адресов. Помните, что для каждого экземпляра требуется отдельный IP-адрес.

    $gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name 'gwconfig' -Subnet $gwSubnet

### Шаг 11

Настройте IP-адреса внутренних веб-серверов во внутреннем пуле IP-адресов. Эти IP-адреса будут использоваться для получения сетевого трафика от конечной точки с интерфейсным IP-адресом. Замените приведенные ниже IP-адреса и добавьте IP-адреса конечных точек своего приложения.

    $pool = New-AzureRmApplicationGatewayBackendAddressPool -Name 'pool01' -BackendIPAddresses 1.1.1.1, 2.2.2.2, 3.3.3.3

### Шаг 12

Настройте параметры HTTP серверной части шлюза приложений. В параметрах HTTP укажите сертификат, переданный на предыдущем шаге.

    $poolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name 'setting01' -Port 443 -Protocol Https -CookieBasedAffinity Enabled -AuthenticationCertificates $authcert

### Шаг 13

Настройте интерфейсный порт IP для конечной точки с общедоступным IP-адресом. Это порт, к которому подключаются пользователи.

    $fp = New-AzureRmApplicationGatewayFrontendPort -Name 'port01'  -Port 443

### Шаг 14

Создайте конфигурацию IP внешнего интерфейса, в которой частный или общедоступный IP-адрес сопоставляется с внешним интерфейсом шлюза приложений. На следующем шаге общедоступный IP-адрес, указанный на предыдущем шаге, будет связан конфигурацией IP внешнего интерфейса.

    $fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig -Name 'fip01' -PublicIPAddress $publicip

### Шаг 15

Создайте прослушиватель HTTP для шлюза приложений. Назначьте используемую конфигурацию IP внешнего интерфейса, порт и сертификат SSL.

	$listener = New-AzureRmApplicationGatewayHttpListener -Name listener01 -Protocol Https -FrontendIPConfiguration $fipconfig -FrontendPort $fp -SslCertificate $cert

### Шаг 16

Создайте правило маршрутизации для балансировщика нагрузки, которое настраивает его поведение. В этом примере создается базовое правило с циклическим перебором.

    $rule = New-AzureRmApplicationGatewayRequestRoutingRule -Name 'rule01' -RuleType basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool
   
### Шаг 17

Настройте размер экземпляра шлюза приложений.

    $sku = New-AzureRmApplicationGatewaySku -Name WAF_Medium -Tier WAF -Capacity 2

>[AZURE.NOTE]  Можно выбрать размер **WAF\_Medium** или **WAF\_Large**. В случае использования WAF должен быть выбран уровень **WAF**. Емкость — любое число от 1 до 10.

### Шаг 18

Настройте режим WAF, допустимые значения: **Предотвращение** и **Обнаружение**.

    $config = New-AzureRmApplicationGatewayWafConfig -Enabled $true -WafMode "Prevention"

### Шаг 19

Создайте шлюз приложений со всеми элементами конфигурации, описанными выше. В этом примере шлюз приложений называется "appgwtest".

	$appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku -WafConfig $config

## Дальнейшие действия

Узнайте, как настроить ведение журнала диагностики и как регистрировать в журнале события, которые обнаружил или предотвратил брандмауэр веб-приложения, посетив страницу [Ведение журнала диагностики для шлюза приложений](application-gateway-diagnostics.md).


[scenario]: ./media/application-gateway-web-application-firewall-powershell/scenario.png

<!---HONumber=AcomDC_0928_2016-->
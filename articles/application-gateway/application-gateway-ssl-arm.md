<properties
   pageTitle="Настройка шлюза приложений для разгрузки SSL с помощью диспетчера ресурсов Azure | Microsoft Azure"
   description="На этой странице приводятся инструкции по созданию шлюза приложений с разгрузкой SSL с помощью диспетчера ресурсов Azure."
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
   ms.date="09/09/2016"
   ms.author="gwallace"/>


# <a name="configure-an-application-gateway-for-ssl-offload-by-using-azure-resource-manager"></a>Настройка шлюза приложений для разгрузки SSL с помощью диспетчера ресурсов Azure

> [AZURE.SELECTOR]
-[Портал Azure](application-gateway-ssl-portal.md)
-[PowerShell для Azure Resource Manager](application-gateway-ssl-arm.md)
-[PowerShell для классической модели Azure](application-gateway-ssl.md)

 Шлюз приложений Azure можно настроить на завершение сеанса SSL в шлюзе, что позволит избежать выполнения дорогостоящей задачи SSL-шифрования на веб-ферме. Кроме того, разгрузка SSL упрощает процесс установки внешнего сервера и управления веб-приложением.


## <a name="before-you-begin"></a>Перед началом работы

1. Установите последнюю версию командлетов Azure PowerShell, используя установщик веб-платформы. Скачать и установить последнюю версию вы можете в разделе **Windows PowerShell** на [странице загрузок](https://azure.microsoft.com/downloads/).
2. Создайте виртуальную сеть и подсеть для шлюза приложений. Убедитесь, что подсеть не используется виртуальной машиной или облачным развертыванием. Сам шлюз приложений должен находиться в подсети виртуальной сети.
3. Для использования шлюза приложений настраиваются существующие серверы или серверы, для которых в виртуальной сети созданы конечные точки либо же назначен общедоступный или виртуальный IP-адрес.

## <a name="what-is-required-to-create-an-application-gateway?"></a>Что необходимо для создания шлюза приложений?


- **Внутренний пул серверов**. Список IP-адресов внутренних серверов. Указанные IP-адреса должны относиться к подсети виртуальной сети либо представлять собой общедоступные или виртуальные IP-адреса.
- **Параметры внутреннего пула серверов**. Каждый пул имеет такие параметры, как порт, протокол и сходство на основе файлов cookie. Эти параметры привязываются к пулу и применяются ко всем серверам в этом пуле.
- **Интерфейсный порт**. Общедоступный порт, открытый в шлюзе приложений. Трафик поступает на этот порт, а затем перенаправляется на один из тыловых серверов.
- **Прослушиватель**. У прослушивателя есть интерфейсный порт, протокол (Http или Https, с учетом регистра) и имя SSL-сертификата (при настройке разгрузки SSL).
- **Правило**. Правило связывает прослушиватель и внутренний пул серверов, а также определяет, в какой внутренний пул серверов следует направлять трафик, поступающий на определенный прослушиватель. В настоящее время поддерживается только *основное* правило. *Основное* правило предусматривает циклическое распределение нагрузки.

**Дополнительные примечания по настройке конфигурации**

Чтобы настроить конфигурацию SSL-сертификатов, протокол в элементе **HttpListener** следует изменить на *Https* (с учетом регистра). Элемент **SslCertificate** нужно добавить в **HttpListener**, указав в качестве значения переменную, настроенную для SSL-сертификата. Внешний порт следует изменить на 443.

**Включение сходства на основе файлов cookie**. Шлюз приложений можно настроить так, чтобы запросы от клиентского сеанса всегда направлялись на одну виртуальную машину в веб-ферме. Это делается с помощью внедрения файлов cookie сеанса, что позволяет шлюзу перенаправлять трафик соответствующим образом. Чтобы включить сходство на основе файлов cookie, присвойте параметру **CookieBasedAffinity** в элементе *BackendHttpSettings* значение **Enabled**.


## <a name="create-an-application-gateway"></a>Создание шлюза приложений

Разница между использованием классической модели развертывания Azure и Azure Resource Manager заключается в порядке создания шлюза приложений и элементов, которые нужно настроить.

При использовании Resource Manager все компоненты, которые будут включены в единый ресурс шлюза приложений, сначала настраиваются по отдельности.


Ниже приведены пошаговые инструкции по созданию шлюза приложений.

1. Создание группы ресурсов для диспетчера ресурсов
2. Создание виртуальной сети, подсети и общедоступного IP-адреса для шлюза приложений
3. Создание объекта конфигурации шлюза приложений
4. Создание ресурса шлюза приложений.


## <a name="create-a-resource-group-for-resource-manager"></a>Создание группы ресурсов для диспетчера ресурсов

Для работы с командлетами диспетчера ресурсов Azure необходимо перейти в режим PowerShell. Дополнительные сведения см. в статье [Использование Windows PowerShell с диспетчером ресурсов](../powershell-azure-resource-manager.md).

### <a name="step-1"></a>Шаг 1

    Login-AzureRmAccount

### <a name="step-2"></a>Шаг 2

Просмотрите подписки учетной записи.

    Get-AzureRmSubscription

Вам будет предложено указать свои учетные данные для аутентификации.<BR>

### <a name="step-3"></a>Шаг 3.

Выберите подписку Azure. <BR>


    Select-AzureRmSubscription -Subscriptionid "GUID of subscription"


### <a name="step-4"></a>Шаг 4.

Создайте группу ресурсов. Если вы используете существующую группу, пропустите этот шаг.

    New-AzureRmResourceGroup -Name appgw-rg -Location "West US"

В диспетчере ресурсов Azure для всех групп ресурсов должно быть указано расположение. Оно используется в качестве расположения по умолчанию для всех ресурсов данной группы. Убедитесь, что во всех командах для создания шлюза приложений используется одна группа ресурсов.

В приведенном выше примере мы создали группу ресурсов с именем appgw-RG и расположением West US (западная часть США).

## <a name="create-a-virtual-network-and-a-subnet-for-the-application-gateway"></a>Создание виртуальной сети и подсети для шлюза приложений

В следующем примере показано создание виртуальной сети с помощью диспетчера ресурсов.

### <a name="step-1"></a>Шаг 1

    $subnet = New-AzureRmVirtualNetworkSubnetConfig -Name subnet01 -AddressPrefix 10.0.0.0/24

Этот пример назначает диапазон адресов 10.0.0.0/24 переменной подсети, которая будет использоваться для создания виртуальной сети.

### <a name="step-2"></a>Шаг 2

    $vnet = New-AzureRmVirtualNetwork -Name appgwvnet -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $subnet

Этот пример создает виртуальную сеть с именем appgwvnet в группе ресурсов appgw-rg для региона West US с помощью префикса 10.0.0.0/16 с подсетью 10.0.0.0/24.

### <a name="step-3"></a>Шаг 3.

    $subnet = $vnet.Subnets[0]

Этот пример присваивает объект подсети переменной $subnet для выполнения следующих действий.

## <a name="create-a-public-ip-address-for-the-front-end-configuration"></a>Создание общедоступного IP-адреса для конфигурации интерфейсной части

    $publicip = New-AzureRmPublicIpAddress -ResourceGroupName appgw-rg -name publicIP01 -location "West US" -AllocationMethod Dynamic

Этот пример создает ресурс общедоступного IP-адреса publicIP01 в группе ресурсов appgw-rg для региона West US.


## <a name="create-an-application-gateway-configuration-object"></a>Создание объекта конфигурации шлюза приложений

### <a name="step-1"></a>Шаг 1

    $gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $subnet

Этот пример создает конфигурацию IP-адреса шлюза приложений с именем gatewayIP01. При запуске шлюз приложений получает IP-адрес из настроенной подсети. Затем шлюз маршрутизирует сетевой трафик на IP-адреса из внутреннего пула IP-адресов. Помните, что для каждого экземпляра требуется отдельный IP-адрес.

### <a name="step-2"></a>Шаг 2

    $pool = New-AzureRmApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 134.170.185.46, 134.170.188.221,134.170.185.50

Этот пример настраивает пул внутренних IP-адресов с именем pool01 и IP-адресами 134.170.185.46, 134.170.188.221,134.170.185.50. Эти значения IP-адресов будут использоваться для получения сетевого трафика от конечной точки с интерфейсным IP-адресом. Замените IP-адрес в предыдущем примере IP-адресом конечных точек вашего веб-приложения.

### <a name="step-3"></a>Шаг 3.

    $poolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name poolsetting01 -Port 80 -Protocol Http -CookieBasedAffinity Enabled

Этот пример настраивает параметр шлюза приложений poolsetting01 для балансировки нагрузки сетевого трафика в пуле серверной части.

### <a name="step-4"></a>Шаг 4.

    $fp = New-AzureRmApplicationGatewayFrontendPort -Name frontendport01  -Port 443

Этот пример настраивает внешний IP-порт с именем frontendport01 для конечной точки с общедоступным IP-адресом.

### <a name="step-5"></a>Шаг 5

    $cert = New-AzureRmApplicationGatewaySslCertificate -Name cert01 -CertificateFile <full path for certificate file> -Password ‘<password>’

Этот пример настраивает сертификат для SSL-соединения. Сертификат должен быть в формате PFX, а пароль к сертификату должен содержать от 4 до 12 символов.

### <a name="step-6"></a>Шаг 6

    $fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig -Name fipconfig01 -PublicIPAddress $publicip

Этот пример создает конфигурацию внешнего IP-адреса с именем fipconfig01 и связывает с ней общедоступный IP-адрес.

### <a name="step-7"></a>Шаг 7

    $listener = New-AzureRmApplicationGatewayHttpListener -Name listener01  -Protocol Https -FrontendIPConfiguration $fipconfig -FrontendPort $fp -SslCertificate $cert


Этот пример создает прослушиватель listener01 и связывает внешний порт с конфигурацией внешнего IP-адреса и сертификатом.

### <a name="step-8"></a>Шаг 8

    $rule = New-AzureRmApplicationGatewayRequestRoutingRule -Name rule01 -RuleType Basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool

Этот пример создает правило маршрутизации rule01 для настройки поведения подсистемы балансировки нагрузки.

### <a name="step-9"></a>Шаг 9.

    $sku = New-AzureRmApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2

Этот пример настраивает размер экземпляра шлюза приложений.

>[AZURE.NOTE]  Значение параметра *InstanceCount* по умолчанию — 2 (максимальное значение — 10). Значение *GatewaySize* (Размер шлюза) по умолчанию — Medium (Средний). Можно выбрать Standard_Small, Standard_Medium или Standard_Large.

## <a name="create-an-application-gateway-by-using-new-azureapplicationgateway"></a>Создание шлюза приложений с помощью командлета New-AzureApplicationGateway

    $appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku -SslCertificates $cert

Этот пример создает шлюз приложений со всеми элементами конфигурации, описанными выше. В этом примере шлюз приложений называется appgwtest.

## <a name="next-steps"></a>Дальнейшие действия

Указания по настройке шлюза приложений для использования с внутренним балансировщиком нагрузки см. в статье [Создание шлюза приложений с внутренним балансировщиком нагрузки (ILB)](application-gateway-ilb.md).

Дополнительные сведения о параметрах балансировки нагрузки в целом см. в статьях:

- [Подсистема балансировщика нагрузки Azure](https://azure.microsoft.com/documentation/services/load-balancer/)
- [Azure Traffic Manager](https://azure.microsoft.com/documentation/services/traffic-manager/)



<!--HONumber=Oct16_HO2-->



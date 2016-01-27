<properties
   pageTitle="Создание, запуск или удаление шлюза приложений с помощью диспетчера ресурсов Azure | Microsoft Azure"
   description="На этой странице приводятся инструкции по созданию, настройке, запуску и удалению шлюза приложений Azure с помощью диспетчера ресурсов Azure."
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


# Создание, запуск или удаление шлюза приложений с помощью диспетчера ресурсов Azure

Шлюз приложений — это балансировщик нагрузки уровня 7. Он отвечает за отработку отказов и эффективную маршрутизацию HTTP-запросов между разными серверами (облачными и локальными). Шлюз приложений выполняет такие функции доставки приложений: балансировка HTTP-нагрузки, определение сходства сеансов по файлам cookie, разгрузка SSL.


> [AZURE.SELECTOR]
- [Azure Classic PowerShell steps](application-gateway-create-gateway.md)
- [Azure Resource Manager PowerShell](application-gateway-create-gateway-arm.md)
- [Azure Resource Manager template ](application-gateway-create-gateway-arm-template.md)


<BR>


В этой статье рассказывается, как создавать, настраивать, запускать и удалять шлюз приложений.


>[AZURE.IMPORTANT]Прежде чем приступить к работе с ресурсами Azure, обратите внимание на то, что в настоящее время в Azure существует две модели развертывания: классическая модель развертывания и развертывание с помощью диспетчера ресурсов. Обязательно изучите [модели и средства развертывания](azure-classic-rm.md), прежде чем начинать работать с любыми ресурсами Azure. Для просмотра документации о средствах развертывания выбирайте соответствующие вкладки в верхней части данной статьи. В этом документе рассматривается создание шлюза приложений с помощью диспетчера ресурсов Azure. Сведения об использовании классической версии см. в статье [Создание, запуск или удаление шлюза приложений](application-gateway-create-gateway.md).



## Перед началом работы

1. Установите последнюю версию командлетов Azure PowerShell, используя установщик веб-платформы. Скачать и установить последнюю версию вы можете в разделе **Windows PowerShell** на [странице загрузок](http://azure.microsoft.com/downloads/).
2. Вы создадите виртуальную сеть и подсеть для шлюза приложения. Убедитесь, что подсеть не используется виртуальной машиной или облачным развертыванием. Сам шлюз приложений должен находиться в подсети виртуальной сети.
3. Для использования шлюза приложений настраиваются существующие серверы или серверы, для которых в виртуальной сети созданы конечные точки либо же назначен общедоступный или виртуальный IP-адрес.

## Что необходимо для создания шлюза приложений?


- **Пул тыловых серверов**. Список IP-адресов тыловых серверов. Указанные IP-адреса должны относиться к подсети виртуальной сети либо представлять собой общедоступные или виртуальные IP-адреса.
- **Параметры пула тыловых серверов**. Каждый пул имеет такие параметры, как порт, протокол и сходство на основе файлов cookie. Эти параметры привязываются к пулу и применяются ко всем серверам в этом пуле.
- **Внешний порт**. Общедоступный порт, открытый в шлюзе приложений. Трафик поступает на этот порт, а затем перенаправляется на один из тыловых серверов.
- **Прослушиватель**. У прослушивателя есть внешний порт, протокол (Http или Https — с учетом регистра) и имя SSL-сертификата (при настройке разгрузки SSL).
- **Правило**. Правило связывает прослушиватель и пул тыловых серверов, а также определяет, в какой пул тыловых серверов следует направлять трафик, поступающий на определенный прослушиватель. Сейчас поддерживается только *основное* правило. *Основное* правило предусматривает циклический перебор при распределении нагрузки.



## Создание нового шлюза приложений

Разница между использованием классической модели развертывания Azure и диспетчера ресурсов Azure заключается в порядке создания шлюза приложения и элементов, которые нужно настроить.

При использовании диспетчера ресурсов все элементы, которые будут включены в единый ресурс шлюза приложений, сначала настраиваются по отдельности.


Ниже приведены пошаговые инструкции по созданию шлюза приложений.

1. Создание группы ресурсов для диспетчера ресурсов.
2. Создание виртуальной сети, подсети и общедоступного IP-адреса для шлюза приложений.
3. Создание объекта конфигурации шлюза приложений.
4. Создание ресурса шлюза приложений.


## Создание группы ресурсов для диспетчера ресурсов

Убедитесь, что у вас установлена последняя версия Azure PowerShell. Дополнительные сведения см. в статье [Использование Azure PowerShell с диспетчером ресурсов Azure](powershell-azure-resource-manager.md).

### Шаг 1

		Login-AzureRmAccount



### Шаг 2

Просмотрите подписки учетной записи.

		Get-AzureRmSubscription

Вам будет предложено указать свои учетные данные для проверки подлинности.<BR>

### Шаг 3.

Выберите, какие подписки Azure будут использоваться. <BR>


		Select-AzureRmSubscription -Subscriptionid "GUID of subscription"


### Шаг 4.

Создайте группу ресурсов (если вы используете существующую группу, пропустите этот шаг).

    New-AzureRmResourceGroup -Name appgw-rg -location "West US"

В диспетчере ресурсов Azure для всех групп ресурсов должно быть указано расположение. Оно используется в качестве расположения по умолчанию для всех ресурсов данной группы. Убедитесь, что во всех командах для создания шлюза приложений используется одна группа ресурсов.

В приведенном выше примере мы создали группу ресурсов с именем appgw-RG и расположением West US (западная часть США).


>[AZURE.NOTE]Если необходимо настроить пользовательский зонд для шлюза приложений, см. статью [Create an application gateway with custom probes by using PowerShell](application-gateway-create-probe-ps.md) (Создание пользовательского зонда для шлюза приложений Azure (классическая версия) с помощью PowerShell). Ознакомьтесь с дополнительными сведениями в статье [Наблюдение за работоспособностью и пользовательские зонды](application-gateway-probe-overview.md).



## Создание виртуальной сети и подсети для шлюза приложений

В следующем примере показано создание виртуальной сети с помощью диспетчера ресурсов.

### Шаг 1

Назначьте диапазон адресов 10.0.0.0/24 переменной подсети, которая будет использоваться для создания виртуальной сети.

	$subnet = New-AzureRmVirtualNetworkSubnetConfig -Name subnet01 -AddressPrefix 10.0.0.0/24


### Шаг 2

Создайте виртуальную сеть с именем appgwvnet в группе ресурсов appgw-rg для региона West US с помощью префикса 10.0.0.0/16 с подсетью 10.0.0.0/24.

	$vnet = New-AzureRmVirtualNetwork -Name appgwvnet -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $subnet


### Шаг 3.

Назначьте переменную подсети для дальнейших этапов создания шлюза приложений.

	$subnet=$vnet.Subnets[0]

## Создание общедоступного IP-адреса для конфигурации интерфейсной части

Создайте ресурс общедоступного IP-адреса с именем publicIP01 в группе ресурсов appgw-rg для региона West US.

	$publicip = New-AzureRmPublicIpAddress -ResourceGroupName appgw-rg -name publicIP01 -location "West US" -AllocationMethod Dynamic


## Создание объекта конфигурации шлюза приложений

Перед созданием шлюза приложений необходимо настроить все элементы конфигурации. В ходе следующих шагов создаются необходимые элементы конфигурации для ресурса шлюза приложений.

### Шаг 1

Создайте конфигурацию IP-адреса шлюза приложений с именем gatewayIP01. При запуске шлюз приложений получает IP-адрес из настроенной подсети. Затем шлюз маршрутизирует сетевой трафик на IP-адреса из пула внутренних IP-адресов. Помните, что для каждого экземпляра требуется отдельный IP-адрес.


	$gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $subnet


### Шаг 2

Настройте пул внутренних IP-адресов с именем pool01 и IP-адресами 134.170.185.46, 134.170.188.221, 134.170.185.50. Эти адреса будут использоваться для получения сетевого трафика от конечной точки с внешним IP-адресом. Вы замените приведенные выше IP-адреса и добавите конечные точки IP-адресов своего приложения.

	$pool = New-AzureRmApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 134.170.185.46, 134.170.188.221,134.170.185.50



### Шаг 3.

Настройте параметры шлюза приложений с именем poolsetting01 для балансировки нагрузки сетевого трафика в пуле серверной части.

	$poolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name poolsetting01 -Port 80 -Protocol Http -CookieBasedAffinity Disabled


### Шаг 4.

Настройте внешний IP-порт с именем frontendport01 для конечной точки с общедоступным IP-адресом.

	$fp = New-AzureRmApplicationGatewayFrontendPort -Name frontendport01  -Port 80


### Шаг 5

Создайте конфигурацию внешнего IP-адреса с именем fipconfig01 и свяжите с ней общедоступный IP-адрес.

	$fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig -Name fipconfig01 -PublicIPAddress $publicip


### Шаг 6

Создайте прослушиватель с именем listener01 и свяжите внешний порт с конфигурацией внешнего IP-адреса.

	$listener = New-AzureRmApplicationGatewayHttpListener -Name listener01  -Protocol Http -FrontendIPConfiguration $fipconfig -FrontendPort $fp

### Шаг 7

Создайте правило маршрутизации с именем rule01 для настройки поведения балансировщика нагрузки.

	$rule = New-AzureRmApplicationGatewayRequestRoutingRule -Name rule01 -RuleType Basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool

### Шаг 8

Настройте размер экземпляра шлюза приложений.

	$sku = New-AzureRmApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2

>[AZURE.NOTE]Значение параметра *InstanceCount* по умолчанию — 2 (максимальное значение — 10). По умолчанию для параметра *GatewaySize* используется значение Medium. Можно выбрать Standard\_Small, Standard\_Medium или Standard\_Large.

## Создание шлюза приложений с помощью командлета New-AzureRmApplicationGateway

Создайте шлюз приложений со всеми элементами конфигурации, описанными выше. В этом примере шлюз приложений называется appgwtest.

	$appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku


## Удаление шлюза приложений

Удалить шлюз приложений можно так.

1. Чтобы остановить шлюз, используйте командлет **Stop-AzureRmApplicationGateway**.
2. Чтобы удалить шлюз, используйте командлет **Remove-AzureRmApplicationGateway**.
3. Убедитесь, что шлюз удален, с помощью командлета **Get-AzureRmApplicationGateway**.

### Шаг 1

Получите объект шлюза приложений и свяжите его с переменной $getgw.

	$getgw =  Get-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg

### Шаг 2

Чтобы остановить шлюз приложений, используйте командлет **Stop-AzureRmApplicationGateway**.

	Stop-AzureRmApplicationGateway -ApplicationGateway $getgw  


Когда шлюз будет остановлен, удалите службу с помощью командлета **Remove-AzureRmApplicationGateway**.


	Remove-AzureRmApplicationGateway -Name $appgwtest -ResourceGroupName appgw-rg -Force



>[AZURE.NOTE]Чтобы отключить запрос подтверждения, можно указать параметр **-force**.


Убедитесь, что служба удалена, с помощью командлета **Get-AzureRmApplicationGateway**. Этот шаг не является обязательным.


	Get-AzureRmApplicationGateway -Name appgwtest-ResourceGroupName appgw-rg


## Дальнейшие действия

Сведения о настройке разгрузки SSL см. в статье [Настройка шлюза приложений для разгрузки SSL с помощью классической модели развертывания](application-gateway-ssl.md).

Сведения о настройке шлюза приложений для использования внутреннего балансировщика нагрузки см. в статье [Создание шлюза приложений с внутренним балансировщиком нагрузки (ILB)](application-gateway-ilb.md).

Дополнительные сведения о параметрах балансировки нагрузки в целом см. в статьях:

- [Подсистема балансировщика нагрузки Azure](https://azure.microsoft.com/documentation/services/load-balancer/)
- [Диспетчер трафика Azure](https://azure.microsoft.com/documentation/services/traffic-manager/)

<!----HONumber=AcomDC_0114_2016-->
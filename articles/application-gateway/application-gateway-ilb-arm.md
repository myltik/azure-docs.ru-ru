<properties
   pageTitle="Создание и настройка шлюза приложений с внутренним балансировщиком нагрузки (ILB) с помощью диспетчера ресурсов Azure | Microsoft Azure"
   description="На этой странице приводятся инструкции по созданию, настройке, запуску и удалению шлюза приложений Azure с внутренним балансировщиком нагрузки (ILB) с помощью диспетчера ресурсов Azure."
   documentationCenter="na"
   services="application-gateway"
   authors="georgewallace"
   manager="carmonm"
   editor="tysonn"/>
<tags
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="04/05/2016"
   ms.author="gwallace"/>


# Создание шлюза приложений с внутренним балансировщиком нагрузки (ILB) с помощью диспетчера ресурсов Azure

> [AZURE.SELECTOR]
- [Руководство для классической модели Azure](application-gateway-ilb.md)
- [Руководство для Resource Manager PowerShell](application-gateway-ilb-arm.md)

Шлюз приложений Azure можно настроить на использование виртуального IP-адреса, доступного в Интернете, или внутренней конечной точки, недоступной в Интернете. Эта точка также называется конечной точкой внутреннего балансировщика нагрузки (ILB). Настройка шлюза с ILB подходит для внутренних бизнес-приложений, недоступных в Интернете. Кроме того, этот вариант конфигурации можно использовать для служб и уровней многоуровневого приложения, размещенного в периметре безопасности без доступа к Интернету, но требующего циклического перебора нагрузки, закрепления сеансов или завершения SSL-запросов.

В этой статье приводятся пошаговые инструкции по настройке шлюза приложений с использованием ILB.

## Перед началом работы

1. Установите последнюю версию командлетов Azure PowerShell, используя установщик веб-платформы. Скачать и установить последнюю версию вы можете в разделе **Windows PowerShell** на [странице загрузок](https://azure.microsoft.com/downloads/).
2. Вы создадите виртуальную сеть и подсеть для шлюза приложений. Убедитесь, что подсеть не используется виртуальной машиной или облачным развертыванием. Сам шлюз приложений должен находиться в подсети виртуальной сети.
3. Для использования шлюза приложений настраиваются существующие серверы или серверы, для которых в виртуальной сети созданы конечные точки либо же назначен общедоступный или виртуальный IP-адрес.

## Что необходимо для создания шлюза приложений?


- **Пул тыловых серверов**. Список IP-адресов тыловых серверов. Указанные IP-адреса должны относиться к одной виртуальной сети (но не входить в подсеть шлюза приложений) либо представлять собой общедоступные или виртуальные IP-адреса.
- **Параметры пула тыловых серверов**. Каждый пул имеет такие параметры, как порт, протокол и сходство на основе файлов cookie. Эти параметры привязываются к пулу и применяются ко всем серверам в этом пуле.
- **Внешний порт**. Общедоступный порт, открытый в шлюзе приложений. Трафик поступает на этот порт, а затем перенаправляется на один из тыловых серверов.
- **Прослушиватель**. У прослушивателя есть внешний порт, протокол (Http или Https — с учетом регистра) и имя SSL-сертификата (при настройке разгрузки SSL).
- **Правило**. Правило связывает прослушиватель и пул тыловых серверов, а также определяет, в какой пул тыловых серверов следует направлять трафик, поступающий на определенный прослушиватель. Сейчас поддерживается только *основное* правило. *Основное*правило предусматривает циклический перебор при распределении нагрузки.



## Создание нового шлюза приложений

Разница между использованием классической модели развертывания Azure и диспетчера ресурсов Azure заключается в порядке создания шлюза приложения и элементов, которые нужно настроить. При использовании диспетчера ресурсов все элементы, которые будут включены в единый ресурс шлюза приложений, сначала настраиваются по отдельности.


Ниже приведены пошаговые инструкции по созданию шлюза приложений.

1. Создание группы ресурсов для диспетчера ресурсов.
2. Создание виртуальной сети и подсети для шлюза приложений.
3. Создание объекта конфигурации шлюза приложений.
4. Создание ресурса шлюза приложений.


## Создание группы ресурсов для диспетчера ресурсов

Для работы с командлетами диспетчера ресурсов Azure необходимо перейти в режим PowerShell. Дополнительные сведения см. в статье [Использование Windows PowerShell с диспетчером ресурсов](../powershell-azure-resource-manager.md).

### Шаг 1

		Login-AzureRmAccount

### Шаг 2

Просмотрите подписки учетной записи.

		get-AzureRmSubscription

Вам будет предложено пройти проверку подлинности с вашими учетными данными.<BR>

### Шаг 3.

Выберите, какие подписки Azure будут использоваться. <BR>


		Select-AzureRmSubscription -Subscriptionid "GUID of subscription"


### Шаг 4.

Создайте группу ресурсов (если вы используете существующую группу, пропустите этот шаг).

    New-AzureRmResourceGroup -Name appgw-rg -location "West US"

В диспетчере ресурсов Azure для всех групп ресурсов должно быть указано расположение. Оно используется в качестве расположения по умолчанию для всех ресурсов данной группы. Убедитесь, что во всех командах для создания шлюза приложений используется одна группа ресурсов.

В приведенном выше примере мы создали группу ресурсов с именем appgw-RG и расположением West US (западная часть США).

## Создание виртуальной сети и подсети для шлюза приложений

В следующем примере показано создание виртуальной сети с помощью диспетчера ресурсов.

### Шаг 1

	$subnetconfig = New-AzureRmVirtualNetworkSubnetConfig -Name subnet01 -AddressPrefix 10.0.0.0/24

Назначение диапазона адресов 10.0.0.0/24 переменной подсети, которая будет использоваться для создания виртуальной сети.

### Шаг 2

	$vnet = New-AzureRmVirtualNetwork -Name appgwvnet -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $subnetconfig

На этом шаге создается виртуальная сеть с именем appgwvnet в группе ресурсов appgw-rg для региона West US с помощью префикса 10.0.0.0/16 с подсетью 10.0.0.0/24.

### Шаг 3.

	$subnet=$vnet.subnets[0]

Присвоение объекта подсети переменной $subnet для выполнения следующих действий.

## Создание объекта конфигурации шлюза приложений

### Шаг 1

	$gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $subnet

Создание конфигурации IP-адреса шлюза приложений с именем gatewayIP01. При запуске шлюз приложений получает IP-адрес из настроенной подсети. Затем шлюз маршрутизирует сетевой трафик на IP-адреса из пула внутренних IP-адресов. Помните, что для каждого экземпляра требуется отдельный IP-адрес.


### Шаг 2

	$pool = New-AzureRmApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 134.170.185.46, 134.170.188.221,134.170.185.50

Настройка пула внутренних IP-адресов с именем pool01 и IP-адресами 134.170.185.46, 134.170.188.221,134.170.185.50. Эти адреса будут использоваться для получения сетевого трафика от конечной точки с внешним IP-адресом. Вы замените приведенные выше IP-адреса и добавите конечные точки IP-адресов своего приложения.

### Шаг 3.

	$poolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name poolsetting01 -Port 80 -Protocol Http -CookieBasedAffinity Disabled

Настройка параметров шлюза приложений с именем poolsetting01 для балансировки нагрузки сетевого трафика в пуле серверной части.

### Шаг 4.

	$fp = New-AzureRmApplicationGatewayFrontendPort -Name frontendport01  -Port 80

Настройка внешнего IP-порта с именем frontendport01 для ILB.

### Шаг 5

	$fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig -Name fipconfig01 -Subnet $subnet

Создание конфигурации внешнего IP-адреса с именем fipconfig01 и ее связывание с частным IP-адресом из текущей подсети виртуальной сети.

### Шаг 6

	$listener = New-AzureRmApplicationGatewayHttpListener -Name listener01  -Protocol Http -FrontendIPConfiguration $fipconfig -FrontendPort $fp

Создание прослушивателя с именем listener01 и связывание внешнего порта с конфигурацией внешнего IP-адреса.

### Шаг 7

	$rule = New-AzureRmApplicationGatewayRequestRoutingRule -Name rule01 -RuleType Basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool

Создание правила маршрутизации с именем rule01 для настройки поведения балансировщика нагрузки.

### Шаг 8

	$sku = New-AzureRmApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2

Настройка размера экземпляра шлюза приложений.

>[AZURE.NOTE]  Значение параметра *InstanceCount* по умолчанию — 2 (максимальное значение — 10). По умолчанию для параметра *GatewaySize* используется значение Medium. Можно выбрать Standard\_Small, Standard\_Medium или Standard\_Large.

## Создание шлюза приложений с помощью командлета New-AzureApplicationGateway

Создание шлюза приложений со всеми элементами конфигурации, описанными выше. В этом примере шлюз приложений называется "appgwtest".


	$appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku

Будет создан шлюз приложений со всеми элементами конфигурации, описанными выше. В этом примере шлюз приложений называется appgwtest.


## Удаление шлюза приложений

Чтобы удалить шлюз приложений, выполните указанные ниже действия.

1. Чтобы остановить шлюз, используйте командлет **Stop-AzureRmApplicationGateway**.
2. Чтобы удалить шлюз, используйте командлет **Remove-AzureRmApplicationGateway**.
3. Убедитесь, что шлюз удален, с помощью командлета **Get-AzureApplicationGateway**.


### Шаг 1

Получите объект шлюза приложений и свяжите его с переменной $getgw.

	$getgw =  Get-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg

### Шаг 2

Чтобы остановить шлюз приложений, используйте командлет **Stop-AzureRmApplicationGateway**. В этом примере командлет **Stop-AzureRmApplicationGateway** приведен в первой строке, а за ним следуют выходные данные.

	PS C:\> Stop-AzureRmApplicationGateway -ApplicationGateway $getgw  

	VERBOSE: 9:49:34 PM - Begin Operation: Stop-AzureApplicationGateway
	VERBOSE: 10:10:06 PM - Completed Operation: Stop-AzureApplicationGateway
	Name       HTTP Status Code     Operation ID                             Error
	----       ----------------     ------------                             ----
	Successful OK                   ce6c6c95-77b4-2118-9d65-e29defadffb8

Когда шлюз будет остановлен, удалите службу с помощью командлета **Remove-AzureRmApplicationGateway**.


	PS C:\> Remove-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Force

	VERBOSE: 10:49:34 PM - Begin Operation: Remove-AzureApplicationGateway
	VERBOSE: 10:50:36 PM - Completed Operation: Remove-AzureApplicationGateway
	Name       HTTP Status Code     Operation ID                             Error
	----       ----------------     ------------                             ----
	Successful OK                   055f3a96-8681-2094-a304-8d9a11ad8301

>[AZURE.NOTE] Если указать параметр **-force**, запрос на подтверждение удаления не появится.


Чтобы убедиться, что служба удалена, используйте командлет **Get-AzureRmApplicationGateway**. Этот шаг не является обязательным.


	PS C:\>Get-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg

	VERBOSE: 10:52:46 PM - Begin Operation: Get-AzureApplicationGateway

	Get-AzureApplicationGateway : ResourceNotFound: The gateway does not exist.
	.....

## Дальнейшие действия

Сведения о настройке разгрузки SSL см. в статье [Настройка шлюза приложений для разгрузки SSL](application-gateway-ssl.md).

Указания по настройке шлюза приложений для использования с ILB см. в статье [Создание шлюза приложений с внутренней подсистемой балансировки нагрузки (ILB)](application-gateway-ilb.md).

Дополнительные сведения о параметрах балансировки нагрузки в целом см. в статьях:

- [Подсистема балансировщика нагрузки Azure](https://azure.microsoft.com/documentation/services/load-balancer/)
- [Azure Traffic Manager](https://azure.microsoft.com/documentation/services/traffic-manager/)

<!---HONumber=AcomDC_0810_2016-->
<properties 
   pageTitle="Создание и настройка шлюза приложений с внутренней подсистемой балансировщика нагрузки (ILB) в виртуальной сети | Microsoft Azure"
   description="На этой странице приводятся инструкции по созданию, настройке, запуску и удалению шлюза приложений Azure при помощи диспетчера ресурсов Azure."
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
   ms.date="08/07/2015"
   ms.author="joaoma"/>


# Создание шлюза приложений с внутренней подсистемой балансировщика нагрузки (ILB) при помощи диспетчера ресурсов Azure

> [AZURE.SELECTOR]
- [Azure classic steps](application-gateway-ilb.md)
- [Resource Manager Powershell steps](application-gateway-ilb-arm.md)

Шлюз приложений можно настроить на использование виртуального IP-адреса с выходом в Интернет или с внутренней конечной точкой без выхода в Интернет, которая называется также конечной точкой внутренней подсистемы балансировщика нагрузки (ILB). Настройка шлюза с ILB подходит для внутренних бизнес-приложений без доступа в Интернет. Кроме того, этот вариант конфигурации можно использовать для служб и уровней многоуровневого приложения, размещенного в периметре безопасности без доступа к Интернету, но требующего циклического перебора нагрузки, закрепления сеансов или завершения SSL-запросов. В этой статье приводятся пошаговые инструкции по настройке шлюза приложений с использованием ILB.

## Перед началом работы

1. Используя установщик веб-платформы, установите последнюю версию командлетов Azure PowerShell. Вы можете скачать ее в разделе **Windows PowerShell** на [странице загрузки](http://azure.microsoft.com/downloads/), а затем установить.
2. Вы создадите виртуальную сеть и подсеть для шлюза приложения. Убедитесь, что подсеть не используется виртуальной машиной или облачным развертыванием. Сам шлюз приложений должен располагаться в подсети виртуальной сети.
3. Для использования шлюза приложений настраиваются либо существующие серверы, либо серверы, для которых в виртуальной сети созданы конечные точки или назначен открытый или виртуальный IP-адрес.

## Что необходимо для создания шлюза приложений?
 

- **Пул внутренних серверов.** Список IP-адресов внутренних серверов. Указанные IP-адреса должны относиться к подсети виртуальной сети либо представлять собой общедоступные или виртуальные IP-адреса. 
- **Параметры пула внутренних серверов.** Каждый пул имеет такие параметры, как порт, протокол и сходство на основе файлов cookie. Эти параметры привязываются к пулу и применяются ко всем серверам в этом пуле.
- **Внешний порт.** Общий порт, открытый в шлюзе приложений. Трафик поступает в этот порт, а затем перенаправляется на один из внутренних серверов.
- **Прослушиватель.** Прослушиватель имеет внешний порт, протокол (HTTP или HTTPS, с учетом регистра) и имя SSL-сертификата (если настраивается разгрузка SSL). 
- **Правило.** Правило связывает прослушиватель и пул внутренних серверов и определяет, в какой пул внутренних серверов должен направляться трафик, попадающий в определенный прослушиватель. Сейчас поддерживается только *основное* правило. *Основное* правило предусматривает циклическое распределение нагрузки.


 
## Создание нового шлюза приложений

Разница между использованием Azure Classic и диспетчера ресурсов Azure состоит в порядке, в котором вы будете создавать шлюз приложений, и элементах, которые нужно будет настроить. При использовании диспетчера ресурсов все элементы, которые планируется включить в состав шлюза приложений, будут настраиваться отдельно, а затем собираться в единый ресурс шлюза приложений.


Ниже приведены пошаговые инструкции по созданию шлюза приложений.

1. Создание группы ресурсов для диспетчера ресурсов
2. Создание виртуальной сети и подсети для шлюза приложений
3. Создание объекта конфигурации шлюза приложений
4. Создать ресурс шлюза приложений.


## Создание группы ресурсов для диспетчера ресурсов

Убедитесь, что выбран режим PowerShell для использования командлетов ARM. Дополнительную информацию см. в статье "Использование [Windows PowerShell с диспетчером ресурсов](powershell-azure-resource-manager.md)".

### Шаг 1

    PS C:\> Switch-AzureMode -Name AzureResourceManager

### Шаг 2

Войдите в свою учетную запись Azure.


    PS C:\> Add-AzureAccount

Вам будет предложено указать свои учетные данные для проверки подлинности.


### Шаг 3.

Выберите подписку Azure.

    PS C:\> Select-AzureSubscription -SubscriptionName "MySubscription"

Чтобы просмотреть перечень доступных подписок, воспользуйтесь командлетом Get-AzureSubscription.


### Шаг 4.

Создайте группу ресурсов (пропустите этот шаг, если вы используете существующую группу).

    PS C:\> New-AzureResourceGroup -Name appgw-rg -location "West US"

Диспетчер ресурсов Azure требует, чтобы все группы ресурсов указывали расположение. Оно используется в качестве расположения по умолчанию для всех ресурсов данной группы. Убедитесь, что во всех командах для создания шлюза приложений используется одна группа ресурсов.

В приведенном выше примере мы создали группу ресурсов под названием appgw-rg с расположением West US (запад США).

## Создание виртуальной сети и подсети для шлюза приложений

В следующем примере показано создание виртуальной сети с помощью диспетчера ресурсов.

### Шаг 1	
	
	$subnet = New-AzureVirtualNetworkSubnetConfig -Name subnet01 -AddressPrefix 10.0.0.0/24

Назначение диапазона адресов 10.0.0.0/24 в переменную подсети, которая будет использоваться для создания виртуальной сети

### Шаг 2
	
	$vnet = New-AzurevirtualNetwork -Name appgwvnet -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $subnet

Создание виртуальной сети с именем appgwvnet в группе ресурсов appw-rg для региона запада США при помощи префикса 10.0.0.0/16 с подсетью 10.0.0.0/24
	

## Создание объекта конфигурации шлюза приложений

### Шаг 1

	$gipconfig = New-AzureApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $subnet

Создание IP-конфигурации шлюза приложений с именем gatewayIP01. При запуске шлюза приложений он получит IP-адрес из настроенной подсети и будет маршрутизировать сетевой трафик на IP-адреса пула серверных IP-адресов. Помните, что для каждого экземпляра потребуется отдельный IP-адрес.
 
### Шаг 2

	$pool = New-AzureApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 134.170.185.46, 134.170.188.221,134.170.185.50

На этом этапе будет выполнена настройка серверной части пула IP-адресов под именем pool01 с IP-адресами 134.170.185.46, 134.170.188.221, 134.170.185.50. Эти адреса будут использоваться для получения сетевого трафика от конечной точки внешнего IP-адреса. Вы замените вышеуказанные IP-адреса и добавите конечные точки IP-адресов вашего приложения.

### Шаг 3

	$poolSetting = New-AzureApplicationGatewayBackendHttpSettings -Name poolsetting01 -Port 80 -Protocol HTTP -CookieBasedAffinity Disabled

Настройка параметров шлюза приложений poolsetting01 для обеспечения сбалансированного по нагрузке сетевого трафика в пуле серверной части.

### Шаг 4.

	$fp = New-AzureApplicationGatewayFrontendPort -Name frontendport01  -Port 80

Настройка порта IP-адреса интерфейсной части с именем frontendport01 для ILB.

### Шаг 5

	$fipconfig = New-AzureApplicationGatewayFrontendIPConfig -Name $fipconfigName -Subnet $subnet

Создание IP-конфигурации интерфейсной части, связывающей частный IP-адрес и текущую подсеть виртуальной сети.

### Шаг 6

	$listener = New-AzureApplicationGatewayHttpListener -Name $listenerName  -Protocol http -FrontendIPConfiguration $fipconfig -FrontendPort $fp

Создание прослушивателя, связывающего порт интерфейсной части с IP-конфигурацией интерфейсной части.

### Шаг 7 

	$rule = New-AzureApplicationGatewayRequestRoutingRule -Name $ruleName -RuleType basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool

Создание правила маршрутизации для подсистемы балансировки нагрузки, настраивая таким образом поведение подсистемы балансировки нагрузки.

### Шаг 8

	$sku = New-AzureApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2

Настройка размера экземпляра шлюза приложений

>[AZURE.NOTE]Значение параметра *InstanceCount* (Количество экземпляров) по умолчанию — 2 (максимальное значение — 10). Значение *GatewaySize* (Размер шлюза) по умолчанию — Medium. Можно выбрать размер Small (Малый), Medium (Средний) или Large (Большой).

## Создание шлюза приложений при помощи New-AzureApplicationGateway

	$appgw = New-AzureApplicationGateway -Name appgwtest -ResourceGroupName $rgname -Location $location -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku

Создание шлюза приложений со всеми элементами конфигурации, описанными выше. В этом примере шлюз приложений называется appgwtest.




## Запуск шлюза

Настроив шлюз, запустите его с помощью командлета `Start-AzureApplicationGateway`. Выставление счетов для шлюза приложений начинается после запуска шлюза.


**Примечание.** Выполнение командлета `Start-AzureApplicationGateway` занимает до 15–20 минут.

В приведенном далее примере шлюз приложений называется appgwtest, а группа ресурсов называется app-rg.


### Шаг 1

Получение объекта шлюза приложений и связывание его с переменной $getgw.
 
	$getgw =  Get-AzureApplicationGateway -Name appgwtest -ResourceGroupName app-rg

### Шаг 2
	 
Запуск шлюза приложений с помощью `Start-AzureApplicationGateway`.

	PS C:\> Start-AzureApplicationGateway -ApplicationGateway $getgw  

	PS C:\> Start-AzureApplicationGateway AppGwTest 

	VERBOSE: 7:59:16 PM - Begin Operation: Start-AzureApplicationGateway 
	VERBOSE: 8:05:52 PM - Completed Operation: Start-AzureApplicationGateway
	Name       HTTP Status Code     Operation ID                             Error 
	----       ----------------     ------------                             ----
	Successful OK                   fc592db8-4c58-2c8e-9a1d-1c97880f0b9b

## Проверьте состояние шлюза приложений

Проверить состояние шлюза можно с помощью командлета `Get-AzureApplicationGateway`. Если командлет *Start-AzureApplicationGateway* на предыдущем этапе был выполнен успешно, параметр State (Состояние) должен получить значение *Running* (Работает), а параметры виртуального IP-адреса и DnsName — действительные значения.

В данном примере показан рабочий шлюз приложений, готовый к приему трафика, отправляемого по адресу `http://<generated-dns-name>.cloudapp.net`.

	PS C:\> Get-AzureApplicationGateway -Name appgwtest -ResourceGroupName app-rg

	VERBOSE: 8:09:28 PM - Begin Operation: Get-AzureApplicationGateway 
	VERBOSE: 8:09:30 PM - Completed Operation: Get-AzureApplicationGateway
	Name          : AppGwTest 
	Description   : 
	VnetName      : appgwvnet 
	Subnets       : {Subnet01} 
	InstanceCount : 2 
	GatewaySize   : Medium 
	State         : Running 
	Vip           : 138.91.170.26 
	DnsName       : appgw-1b8402e8-3e0d-428d-b661-289c16c82101.cloudapp.net


## Удаление шлюза приложений

Чтобы удалить шлюз приложений, выполните указанные ниже действия.

1. Остановите шлюз с помощью командлета `Stop-AzureApplicationGateway`. 
2. Удалите шлюз с помощью командлета `Remove-AzureApplicationGateway`.
3. Проверьте, удален ли шлюз, с помощью командлета `Get-AzureApplicationGateway`.

В данном примере командлет `Stop-AzureApplicationGateway` показан в первой строке, а за ним следуют выходные данные.

### Шаг 1

Получение объекта шлюза приложений и связывание его с переменной $getgw.
 
	$getgw =  Get-AzureApplicationGateway -Name appgwtest -ResourceGroupName app-rg

### Шаг 2
	 
Остановите шлюз приложений с помощью `Stop-AzureApplicationGateway`.

	PS C:\> Stop-AzureApplicationGateway -ApplicationGateway $getgw  

	VERBOSE: 9:49:34 PM - Begin Operation: Stop-AzureApplicationGateway 
	VERBOSE: 10:10:06 PM - Completed Operation: Stop-AzureApplicationGateway
	Name       HTTP Status Code     Operation ID                             Error 
	----       ----------------     ------------                             ----
	Successful OK                   ce6c6c95-77b4-2118-9d65-e29defadffb8

Когда шлюз перейдет в состояние Stopped (Остановлен), удалите службу с помощью командлета `Remove-AzureApplicationGateway`.


	PS C:\> Remove-AzureApplicationGateway -Name $appgwName -ResourceGroupName $rgname -Force

	VERBOSE: 10:49:34 PM - Begin Operation: Remove-AzureApplicationGateway 
	VERBOSE: 10:50:36 PM - Completed Operation: Remove-AzureApplicationGateway
	Name       HTTP Status Code     Operation ID                             Error 
	----       ----------------     ------------                             ----
	Successful OK                   055f3a96-8681-2094-a304-8d9a11ad8301

>[AZURE.NOTE]Используйте ключ -force, чтобы не выводить запрос о подтверждении удаления
>

Проверьте, удалена ли служба, с помощью командлета `Get-AzureApplicationGateway`. Этот шаг не является обязательным.


	PS C:\>Get-AzureApplicationGateway -Name appgwtest-ResourceGroupName app-rg

	VERBOSE: 10:52:46 PM - Begin Operation: Get-AzureApplicationGateway 

	Get-AzureApplicationGateway : ResourceNotFound: The gateway does not exist. 
	.....

## Дальнейшие действия

Инструкции по настройке разгрузки SSL см. в статье [Настройка шлюза приложений для разгрузки SSL](application-gateway-ssl.md).

Инструкции по настройке шлюза приложений для использования с ILB см. в статье [Создание шлюза приложений с внутренней подсистемой балансировщика нагрузки (ILB)](application-gateway-ilb.md).

Дополнительные сведения о параметрах балансировки нагрузки в целом см. в статьях:

- [Подсистема балансировщика нагрузки Azure](https://azure.microsoft.com/documentation/services/load-balancer/)
- [Диспетчер трафика Azure](https://azure.microsoft.com/documentation/services/traffic-manager/)

<!---HONumber=August15_HO7-->
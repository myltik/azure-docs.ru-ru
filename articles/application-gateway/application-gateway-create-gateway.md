<properties 
   pageTitle="Создание, запуск или удаление шлюза приложений | Microsoft Azure"
   description="На этой странице приводятся инструкции по созданию, настройке, запуску и удалению шлюза приложений Azure."
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
   ms.date="07/29/2015"
   ms.author="joaoma"/>

# Создание, запуск или удаление шлюза приложений

Данный выпуск позволяет создать шлюз приложения, используя PowerShell или вызовы API REST. Портал и поддержка CLI будут реализованы в следующем выпуске. В этой статье рассказывается, как создать и настроить, запустить и удалить шлюз приложений.

## Перед началом работы

1. Используя установщик веб-платформы, установите последнюю версию командлетов Azure PowerShell. Загрузить и установить последнюю версию можно в разделе **Windows PowerShell** на [странице загрузки](http://azure.microsoft.com/downloads/).
2. Убедитесь, что у вас есть рабочая виртуальная сеть с действительной подсетью. Убедитесь, что подсеть не используется виртуальной машиной или облачным развертыванием. Сам шлюз приложений должен располагаться в подсети виртуальной сети.
3. Для использования шлюза приложений настраиваются либо существующие серверы, либо серверы, для которых в виртуальной сети созданы конечные точки или назначен открытый или виртуальный IP-адрес.

## Что необходимо для создания шлюза приложений?
 

При использовании команды New-AzureApplicationGateway для создания шлюза приложений настройки не задаются, и только что созданный ресурс нужно настраивать с помощью XML-файла или объекта конфигурации.


Доступны следующие значения.

- **Пул внутренних серверов.** Список IP-адресов внутренних серверов. Указанные IP-адреса должны относиться к подсети виртуальной сети либо представлять собой общедоступные или виртуальные IP-адреса. 
- **Параметры пула внутренних серверов.** Каждый пул имеет такие параметры, как порт, протокол и сходство на основе файлов cookie. Эти параметры привязываются к пулу и применяются ко всем серверам в этом пуле.
- **Внешний порт.** Общий порт, открытый в шлюзе приложений. Трафик поступает в этот порт, а затем перенаправляется на один из внутренних серверов.
- **Прослушиватель.** Прослушиватель имеет внешний порт, протокол (HTTP или HTTPS, с учетом регистра) и имя SSL-сертификата (если настраивается разгрузка SSL). 
- **Правило.** Правило связывает прослушиватель и пул внутренних серверов и определяет, в какой пул внутренних серверов должен направляться трафик, попадающий в определенный прослушиватель. Сейчас поддерживается только *основное* правило. *Основное* правило предусматривает циклическое распределение нагрузки.


 
## Создание нового шлюза приложений

Чтобы создать шлюз приложений, необходимо выполнить действия в определенном порядке:

1. Создать ресурс шлюза приложений.
2. Создать XML-файл конфигурации или объект конфигурации.
3. Применить конфигурацию к созданному ресурсу шлюза приложений.

### Создание ресурса шлюза приложений

**Чтобы создать шлюз**, используйте командлет `New-AzureApplicationGateway`, подставив свои собственные значения. Обратите внимание, что выставление счетов для шлюза начинается не на данном этапе, а позднее, после успешного запуска шлюза.

В следующем примере показано создание нового шлюза приложений с использованием виртуальной сети testvnet1 и подсети subnet-1.

    
	PS C:\> New-AzureApplicationGateway -Name AppGwTest -VnetName testvnet1 -Subnets @("Subnet-1")

	VERBOSE: 4:31:35 PM - Begin Operation: New-AzureApplicationGateway 
	VERBOSE: 4:32:37 PM - Completed Operation: New-AzureApplicationGateway
	Name       HTTP Status Code     Operation ID                             Error 
	----       ----------------     ------------                             ----
	Successful OK                   55ef0460-825d-2981-ad20-b9a8af41b399


 *Description* (Описание), *InstanceCount* (Количество экземпляров) и *GatewaySize* (Размер шлюза) — необязательные параметры.


**Чтобы проверить,** создан ли шлюз, используйте командлет `Get-AzureApplicationGateway`.




	PS C:\> Get-AzureApplicationGateway AppGwTest
	Name          : AppGwTest
	Description   : 
	VnetName      : testvnet1
	Subnets       : {Subnet-1}
	InstanceCount : 2
	GatewaySize   : Medium
	State         : Stopped
	VirtualIPs    : {}
	DnsName       :

>[AZURE.NOTE]Значение параметра *InstanceCount* (Количество экземпляров) по умолчанию — 2, максимальное значение — 10. Значение *GatewaySize* (Размер шлюза) по умолчанию — Medium (Средний). Можно выбрать размер Small (Малый), Medium (Средний) или Large (Большой).


 Параметры *Vip* (Виртуальный IP-адрес) и *DnsName* (DNS-имя) отображаются без значений, поскольку шлюз еще не запущен. Эти значения будут заданы, как только шлюз перейдет в рабочее состояние.

## Настройка шлюза приложений

Шлюз приложений можно настроить с помощью XML-файла или объекта конфигурации.

## Настройка шлюза приложений с помощью XML-файла 

В следующем примере все параметры шлюза приложений настраиваются и применяются к ресурсу шлюза приложений при помощи XML-файла.

### Шаг 1.  

Скопируйте приведенный ниже текст и вставьте его в блокнот.

	<?xml version="1.0" encoding="utf-8"?>
	<ApplicationGatewayConfiguration xmlns:i="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/windowsazure">
	    <FrontendPorts>
	        <FrontendPort>
	            <Name>(name-of-your-frontend-port)</Name>
	            <Port>(port number)</Port>
	        </FrontendPort>
	    </FrontendPorts>
	    <BackendAddressPools>
	        <BackendAddressPool>
	            <Name>(name-of-your-backend-pool)</Name>
	            <IPAddresses>
	                <IPAddress>(your-IP-address-for-backend-pool)</IPAddress>
	                <IPAddress>(your-second-IP-address-for-backend-pool)</IPAddress>
	            </IPAddresses>
	        </BackendAddressPool>
	    </BackendAddressPools>
	    <BackendHttpSettingsList>
	        <BackendHttpSettings>
	            <Name>(backend-setting-name-to-configure-rule)</Name>
	            <Port>80</Port>
	            <Protocol>[Http|Https]</Protocol>
	            <CookieBasedAffinity>Enabled</CookieBasedAffinity>
	        </BackendHttpSettings>
	    </BackendHttpSettingsList>
	    <HttpListeners>
	        <HttpListener>
	            <Name>(name-of-the-listener)</Name>
	            <FrontendPort>(name-of-your-frontend-port)</FrontendPort>
	            <Protocol>[Http|Https]</Protocol>
	        </HttpListener>
	    </HttpListeners>
	    <HttpLoadBalancingRules>
	        <HttpLoadBalancingRule>
	            <Name>(name-of-load-balancing-rule)</Name>
	            <Type>basic</Type>
	            <BackendHttpSettings>(backend-setting-name-to-configure-rule)</BackendHttpSettings>
	            <Listener>(name-of-the-listener)</Listener>
	            <BackendAddressPool>(name-of-your-backend-pool)</BackendAddressPool>
	        </HttpLoadBalancingRule>
	    </HttpLoadBalancingRules>
	</ApplicationGatewayConfiguration>

Измените значения в скобках для элементов конфигурации. Сохраните файл с расширением .XML.

>[AZURE.IMPORTANT]В элементе протокола HTTP или HTTPS учитывается регистр.

В приведенном ниже примере показано, как использовать файл конфигурации при настройке шлюза приложений, чтобы сбалансировать нагрузку HTTP-трафика на общем порте 80 и направить сетевой трафик на внутренний порт 80 между двумя IP-адресами.

	<?xml version="1.0" encoding="utf-8"?>
	<ApplicationGatewayConfiguration xmlns:i="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/windowsazure">
	    <FrontendPorts>
	        <FrontendPort>
	            <Name>FrontendPort1</Name>
	            <Port>80</Port>
	        </FrontendPort>
	    </FrontendPorts>
	    <BackendAddressPools>
	        <BackendAddressPool>
	            <Name>BackendPool1</Name>
	            <IPAddresses>
	                <IPAddress>10.0.0.1</IPAddress>
	                <IPAddress>10.0.0.2</IPAddress>
	            </IPAddresses>
	        </BackendAddressPool>
	    </BackendAddressPools>
	    <BackendHttpSettingsList>
	        <BackendHttpSettings>
	            <Name>BackendSetting1</Name>
	            <Port>80</Port>
	            <Protocol>Http</Protocol>
	            <CookieBasedAffinity>Enabled</CookieBasedAffinity>
	        </BackendHttpSettings>
	    </BackendHttpSettingsList>
	    <HttpListeners>
	        <HttpListener>
	            <Name>HTTPListener1</Name>
	            <FrontendPort>FrontendPort1</FrontendPort>
	            <Protocol>Http</Protocol>
	        </HttpListener>
	    </HttpListeners>
	    <HttpLoadBalancingRules>
	        <HttpLoadBalancingRule>
	            <Name>HttpLBRule1</Name>
	            <Type>basic</Type>
	            <BackendHttpSettings>BackendSetting1</BackendHttpSettings>
	            <Listener>HTTPListener1</Listener>
	            <BackendAddressPool>BackendPool1</BackendAddressPool>
	        </HttpLoadBalancingRule>
	    </HttpLoadBalancingRules>
	</ApplicationGatewayConfiguration>





### Шаг 2.

Теперь шлюз приложений необходимо настроить. Используйте командлет `Set-AzureApplicationGatewayConfig` с XML-файлом конфигурации.


	PS C:\> Set-AzureApplicationGatewayConfig -Name AppGwTest -ConfigFile "D:\config.xml"

	VERBOSE: 7:54:59 PM - Begin Operation: Set-AzureApplicationGatewayConfig 
	VERBOSE: 7:55:32 PM - Completed Operation: Set-AzureApplicationGatewayConfig
	Name       HTTP Status Code     Operation ID                             Error 
	----       ----------------     ------------                             ----
	Successful OK                   9b995a09-66fe-2944-8b67-9bb04fcccb9d

## Настройка шлюза приложений с помощью объекта конфигурации

В следующем примере показано, как настроить шлюз приложений с помощью объектов конфигурации. Все элементы конфигурации должны быть настроены отдельно и затем добавлены в объект конфигурации шлюза приложений. После создания объекта конфигурации используйте команду `Set-AzureApplicationGateway`, чтобы применить конфигурацию к ранее созданному ресурсу шлюза приложений.

>[AZURE.NOTE]Прежде чем присваивать значения каждому объекту конфигурации, необходимо определить, в каком типе объекта PowerShell он будет храниться. Первая строка для создания отдельных элементов определяет, какое имя объекта Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model(имя объекта) будет использоваться.

### Шаг 1.

Создайте все отдельные элементы конфигурации.

Создайте внешний IP-адрес:

	PS C:\> $fip = New-Object Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.FrontendIPConfiguration 
	PS C:\> $fip.Name = "fip1" 
	PS C:\> $fip.Type = "Private" 
	PS C:\> $fip.StaticIPAddress = "10.0.0.5" 

Создайте внешний порт:
	
	PS C:\> $fep = New-Object Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.FrontendPort 
	PS C:\> $fep.Name = "fep1" 
	PS C:\> $fep.Port = 80
	
Создайте пул внутренних серверов.

 Определите IP-адреса, которые будут добавлены в пул внутренних серверов:


	PS C:\> $servers = New-Object Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.BackendServerCollection 
	PS C:\> $servers.Add("10.0.0.1") 
	PS C:\> $servers.Add("10.0.0.2")

 С помощью объекта $server добавьте значения для объекта пула внутренних серверов ($pool):

	PS C:\> $pool = New-Object Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.BackendAddressPool 
	PS C:\> $pool.BackendServers = $servers 
	PS C:\> $pool.Name = "pool1"

Создайте параметр пула внутренних серверов:

	PS C:\> $setting = New-Object Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.BackendHttpSettings 
	PS C:\> $setting.Name = "setting1" 
	PS C:\> $setting.CookieBasedAffinity = "enabled" 
	PS C:\> $setting.Port = 80 
	PS C:\> $setting.Protocol = "http"

Создайте прослушиватель:

	PS C:\> $listener = New-Object Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.HttpListener 
	PS C:\> $listener.Name = "listener1" 
	PS C:\> $listener.FrontendPort = "fep1" 
	PS C:\> $listener.FrontendIP = "fip1" 
	PS C:\> $listener.Protocol = "http" 
	PS C:\> $listener.SslCert = ""

Создайте правило:

	PS C:\> $rule = New-Object Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.HttpLoadBalancingRule 
	PS C:\> $rule.Name = "rule1" 
	PS C:\> $rule.Type = "basic" 
	PS C:\> $rule.BackendHttpSettings = "setting1" 
	PS C:\> $rule.Listener = "listener1" 
	PS C:\> $rule.BackendAddressPool = "pool1"
 
### Шаг 2.

Назначьте все отдельные элементы конфигурации объекту конфигурации шлюза приложений ($appgwconfig):

Добавьте к конфигурации внешний IP-адрес:

	PS C:\> $appgwconfig = New-Object Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.ApplicationGatewayConfiguration
	PS C:\> $appgwconfig.FrontendIPConfigurations = New-Object "System.Collections.Generic.List[Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.FrontendIPConfiguration]" 
	PS C:\> $appgwconfig.FrontendIPConfigurations.Add($fip)
 
Добавьте к конфигурации внешний порт:

	PS C:\> $appgwconfig.FrontendPorts = New-Object "System.Collections.Generic.List[Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.FrontendPort]" 
	PS C:\> $appgwconfig.FrontendPorts.Add($fep)

Добавьте к конфигурации пул внутренних серверов:

	PS C:\> $appgwconfig.BackendAddressPools = New-Object "System.Collections.Generic.List[Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.BackendAddressPool]" 
	PS C:\> $appgwconfig.BackendAddressPools.Add($pool)  

Добавьте к конфигурации параметр пула внутренних серверов:

	PS C:\> $appgwconfig.BackendHttpSettingsList = New-Object "System.Collections.Generic.List[Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.BackendHttpSettings]" 
	PS C:\> $appgwconfig.BackendHttpSettingsList.Add($setting) 

Добавьте к конфигурации прослушиватель:

	PS C:\> $appgwconfig.HttpListeners = New-Object "System.Collections.Generic.List[Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.HttpListener]" 
	PS C:\> $appgwconfig.HttpListeners.Add($listener)

Добавьте к конфигурации правило:

	PS C:\> $appgwconfig.HttpLoadBalancingRules = New-Object "System.Collections.Generic.List[Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.HttpLoadBalancingRule]" 
	PS C:\> $appgwconfig.HttpLoadBalancingRules.Add($rule) 

### Шаг 3

Примените объект конфигурации к ресурсу шлюза приложений с помощью командлета `Set-AzureApplicationGatewayConfig`:
 
	Set-AzureApplicationGatewayConfig -Name AppGwTest -Config $appgwconfig

## Запуск шлюза

Настроив шлюз, запустите его с помощью командлета `Start-AzureApplicationGateway`. Выставление счетов для шлюза приложений начинается после запуска шлюза.


**Примечание.** Выполнение командлета `Start-AzureApplicationGateway` может занять 15–20 минут.



	PS C:\> Start-AzureApplicationGateway AppGwTest 

	VERBOSE: 7:59:16 PM - Begin Operation: Start-AzureApplicationGateway 
	VERBOSE: 8:05:52 PM - Completed Operation: Start-AzureApplicationGateway
	Name       HTTP Status Code     Operation ID                             Error 
	----       ----------------     ------------                             ----
	Successful OK                   fc592db8-4c58-2c8e-9a1d-1c97880f0b9b

## Проверка состояния шлюза

Проверить состояние шлюза можно с помощью командлета `Get-AzureApplicationGateway`. Если командлет *Start-AzureApplicationGateway* в предыдущем шаге был выполнен успешно, параметр State должен получить значение *Running* (Работает), а параметры Vip и DnsName — допустимые значения.

В этом примере показан работающий шлюз приложений, готовый к приему трафика, отправляемого по адресу `http://<generated-dns-name>.cloudapp.net`.

	PS C:\> Get-AzureApplicationGateway AppGwTest 

	VERBOSE: 8:09:28 PM - Begin Operation: Get-AzureApplicationGateway 
	VERBOSE: 8:09:30 PM - Completed Operation: Get-AzureApplicationGateway
	Name          : AppGwTest 
	Description   : 
	VnetName      : testvnet1 
	Subnets       : {Subnet-1} 
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

В этом примере в первой строке показан командлет `Stop-AzureApplicationGateway`, а затем выходные данные.

	PS C:\> Stop-AzureApplicationGateway AppGwTest 

	VERBOSE: 9:49:34 PM - Begin Operation: Stop-AzureApplicationGateway 
	VERBOSE: 10:10:06 PM - Completed Operation: Stop-AzureApplicationGateway
	Name       HTTP Status Code     Operation ID                             Error 
	----       ----------------     ------------                             ----
	Successful OK                   ce6c6c95-77b4-2118-9d65-e29defadffb8

Когда шлюз перейдет в состояние Stopped (Остановлен), удалите службу с помощью командлета `Remove-AzureApplicationGateway`.


	PS C:\> Remove-AzureApplicationGateway AppGwTest 

	VERBOSE: 10:49:34 PM - Begin Operation: Remove-AzureApplicationGateway 
	VERBOSE: 10:50:36 PM - Completed Operation: Remove-AzureApplicationGateway
	Name       HTTP Status Code     Operation ID                             Error 
	----       ----------------     ------------                             ----
	Successful OK                   055f3a96-8681-2094-a304-8d9a11ad8301

Проверьте, удалена ли служба, с помощью командлета `Get-AzureApplicationGateway`. Этот шаг не является обязательным.


	PS C:\> Get-AzureApplicationGateway AppGwTest 

	VERBOSE: 10:52:46 PM - Begin Operation: Get-AzureApplicationGateway 

	Get-AzureApplicationGateway : ResourceNotFound: The gateway does not exist. 
	.....

## Дальнейшие действия

Инструкции по настройке разгрузки SSL см. в статье [Настройка шлюза приложений для разгрузки SSL](application-gateway-ssl.md).

Инструкции по настройке шлюза приложений для использования с ILB см. в статье [Создание шлюза приложений с внутренней подсистемой балансировщика нагрузки (ILB)](application-gateway-ilb.md).

Дополнительные сведения о параметрах балансировки нагрузки в целом см. в статьях:

- [Подсистема балансировки нагрузки Azure](https://azure.microsoft.com/documentation/services/load-balancer/)
- [Azure Traffic Manager](https://azure.microsoft.com/documentation/services/traffic-manager/)

<!---HONumber=July15_HO5-->
<properties 
   pageTitle="Настройка шлюза приложения для разгрузки SSL | Microsoft Azure"
   description="Эта статья содержит инструкции по настройке разгрузки SSL в шлюза приложений Azure."
   documentationCenter="na"
   services="application-gateway"
   authors="joaoma"
   manager="jdial"
   editor="tysonn"/>
<tags 
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="get-started-article" 
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services" 
   ms.date="06/30/2015"
   ms.author="joaoma"/>

# Настройка шлюза приложений для разгрузки SSL

Шлюз приложений можно настроить на завершение сеанса SSL в шлюзе, что позволит избежать дорогостоящего SSL-шифрования на веб-ферме. Кроме того, разгрузка SSL упрощает процесс установки внешнего сервера и управление приложением.

## Перед началом работы

1. Используя установщик веб-платформы, установите последнюю версию командлетов Azure PowerShell. Загрузить и установить последнюю версию можно в разделе **Windows PowerShell** на [странице загрузки](http://azure.microsoft.com/downloads/).
2. Убедитесь, что у вас есть рабочая виртуальная сеть с действительной подсетью.
3. Убедитесь, что у вас есть внутренние серверы в виртуальной сети или с назначенным общедоступным IP или виртуальным IP-адресом.

Чтобы настроить разгрузку SSL в шлюзе приложений, выполните перечисленные ниже действия в указанном порядке.

1. [Создание нового шлюза приложений](#create-a-new-application-gateway)
2. [Загрузка SSL-сертификатов](#upload-ssl-certificates) 
3. [Настройка шлюза](#configure-the-gateway)
4. [Настройка конфигурации шлюза](#set-the-gateway-configuration)
5. [Запуск шлюза](#start-the-gateway)
6. [Проверка состояния шлюза](#verify-the-gateway-status)


## Создание нового шлюза приложений

**Для создания шлюза** используйте командлет `New-AzureApplicationGateway`, подставив в него свои значения. Обратите внимание, что выставление счетов для шлюза начинается не на данном этапе, а позднее, после успешного запуска шлюза.

В данном примере командлет показан в первой строке, а затем выходные данные.

	PS C:> New-AzureApplicationGateway -Name AppGwTest -VnetName testvnet1 -Subnets @("Subnet-1")

	VERBOSE: 4:31:35 PM - Begin Operation: New-AzureApplicationGateway 
	VERBOSE: 4:32:37 PM - Completed Operation: New-AzureApplicationGateway
	Name       HTTP Status Code     Operation ID                             Error 
	----       ----------------     ------------                             ----
	Successful OK                   55ef0460-825d-2981-ad20-b9a8af41b399

**Чтобы проверить** создание шлюза, используйте командлет `Get-AzureApplicationGateway`.


В примере параметры *Description* (Описание), *InstanceCount* (Количество экземпляров) и *GatewaySize* (Размер шлюза) являются необязательными. Значение параметра *InstanceCount* (Количество экземпляров) по умолчанию — 2, максимальное значение — 10. Значение *GatewaySize* (размер шлюза) по умолчанию — Medium (Средний). Также доступны значения Small (Маленький) и Large (Большой). Параметры *Vip* и *DnsName* отображаются без значений, поскольку шлюз еще не запущен. Эти значения будут заданы, как только шлюз перейдет в рабочее состояние.

В данном примере командлет показан в первой строке, а затем выходные данные.

	PS C:> Get-AzureApplicationGateway AppGwTest

	VERBOSE: 4:39:39 PM - Begin Operation:
	Get-AzureApplicationGateway VERBOSE: 4:39:40 PM - Completed 
	Operation: Get-AzureApplicationGateway
	Name: AppGwTest	
	Description: 
	VnetName: testvnet1 
	Subnets: {Subnet-1} 
	InstanceCount: 2 
	GatewaySize: Medium 
	State: Stopped 
	VirtualIPs: 
	DnsName:


## Загрузка SSL-сертификатов 

Чтобы загрузить на шлюз приложений сертификаты сервера в формате *PFX*, используйте командлет `Add-AzureApplicationGatewaySslCertificate`. Имя сертификата выбирается пользователем и должно быть уникальным в пределах шлюза приложений. В операциях управления сертификатами в шлюзе приложений сертификат указывается по имени.

В данном примере командлет показан в первой строке, а затем выходные данные. Подставьте в пример свои собственные значения.

	PS C:> Add-AzureApplicationGatewaySslCertificate  -Name AppGwTest -CertificateName GWCert -Password <password> -CertificateFile <full path to pfx file> 
	
	VERBOSE: 5:05:23 PM - Begin Operation: Get-AzureApplicationGatewaySslCertificate 
	VERBOSE: 5:06:29 PM - Completed Operation: Get-AzureApplicationGatewaySslCertificate
	Name       HTTP Status Code     Operation ID                             Error 
	----       ----------------     ------------                             ----
	Successful OK                   21fdc5a0-3bf7-2c12-ad98-192e0dd078ef

Затем проверьте загрузку сертификата. Используйте для этого командлет `Get-AzureApplicationGatewayCertificate`.

В данном примере командлет показан в первой строке, а затем выходные данные.

	PS C:> Get-AzureApplicationGatewaySslCertificate AppGwTest 

	VERBOSE: 5:07:54 PM - Begin Operation: Get-AzureApplicationGatewaySslCertificate 
	VERBOSE: 5:07:55 PM - Completed Operation: Get-AzureApplicationGatewaySslCertificate
	Name           : SslCert 
	SubjectName    : CN=gwcert.app.test.contoso.com 
	Thumbprint     : AF5ADD77E160A01A6......EE48D1A 
	ThumbprintAlgo : sha1RSA
	State..........: Provisioned


## Настройка шлюза

Конфигурация шлюза приложений состоит из нескольких значений. Значения конфигурации могут быть взаимосвязаны.

Доступны следующие значения.
 
- **Пул внутренних серверов:** список IP-адресов для внутренних серверов. Указываемые IP-адреса должны относиться к подсети виртуальной сети или представлять собой общедоступные IP или виртуальные IP-адреса. 
- **Параметры пула внутренних серверов:** каждый пул имеет такие параметры, как порт, протокол и соответствие на основе файлов cookie. Эти параметры привязываются к пулу и применяются ко всем серверам в этом пуле.
- **Внешний порт:** общий порт, открытый в шлюзе приложений. Трафик поступает в этот порт, а затем перенаправляется на один из внутренних серверов.
- **Прослушиватель:** прослушиватель имеет внешний порт, протокол (Http или Https; регистр имеет значение) и имя SSL-сертификата (если настраивается разгрузка SSL). 
- **Правило:** правило связывает прослушиватель и пул внутренних серверов и определяет, в какой пул внутренних серверов должен направляться трафик, попадающий в определенный прослушиватель. В настоящее время поддерживается только *основное* правило. *Основное* правило предусматривает циклическое распределение нагрузки.

**Дополнительные примечания по настройке конфигурации:**

для настройки конфигурации SSL-сертификатов протокол в **HttpListener** следует изменить на *Https* (с учетом регистра). К **HttpListener** необходимо добавить элемент **SslCert** (SSL-сертификат) со значением, соответствующим имени, которое использовалось выше при загрузке SSL-сертификатов. Внешний порт следует изменить на 443.

**Включение соответствия на основе файле cookie**: шлюз приложений можно настроить таким образом, чтобы запросы от одного и того же клиентского сеанса всегда направлялись на одну и ту же виртуальную машину веб-фермы. Это делается путем внедрения файлов cookie сеанса, что позволяет шлюзу перенаправлять трафик соответствующим образом. Чтобы включить соответствия на основе файлов cookie, присвойте параметру **CookieBasedAffinity** значение *Enabled* (Включено) в элементе **BackendHttpSettings** (Настройки HTTP внутреннего сервера).



Настроить конфигурацию можно либо путем создания объекта конфигурации, либо с помощью XML-файла конфигурации. Чтобы создать конфигурацию с помощью XML-файла конфигурации, используйте приведенный ниже пример.

**Пример XML-конфигурации**


	    <?xml version="1.0" encoding="utf-8"?>
	<ApplicationGatewayConfiguration xmlns:i="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/windowsazure">
	    <FrontendIPConfigurations />
	    <FrontendPorts>
	        <FrontendPort>
	            <Name>FrontendPort1</Name>
	            <Port>443</Port>
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
	            <Protocol>Https</Protocol>
	            <SslCert>GWCert</SslCert>
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


## Настройка конфигурации шлюза

Теперь шлюз приложений необходимо настроить. Для этого можно использовать командлет `Set-AzureApplicationGatewayConfig` либо с объектом конфигурации, либо с XML-файлом конфигурации.


	PS C:> Set-AzureApplicationGatewayConfig -Name AppGwTest -ConfigFile D:\config.xml

	VERBOSE: 7:54:59 PM - Begin Operation: Set-AzureApplicationGatewayConfig 
	VERBOSE: 7:55:32 PM - Completed Operation: Set-AzureApplicationGatewayConfig
	Name       HTTP Status Code     Operation ID                             Error 
	----       ----------------     ------------                             ----
	Successful OK                   9b995a09-66fe-2944-8b67-9bb04fcccb9d

## Запуск шлюза

После настройки запустите шлюз с помощью командлета `Start-AzureApplicationGateway`. Выставление счетов для шлюза приложений начинается после запуска шлюза.


**Примечание.** Выполнение командлета `Start-AzureApplicationGateway` занимает до 15–20 минут.

   
	PS C:> Start-AzureApplicationGateway AppGwTest 

	VERBOSE: 7:59:16 PM - Begin Operation: Start-AzureApplicationGateway 
	VERBOSE: 8:05:52 PM - Completed Operation: Start-AzureApplicationGateway
	Name       HTTP Status Code     Operation ID                             Error 
	----       ----------------     ------------                             ----
	Successful OK                   fc592db8-4c58-2c8e-9a1d-1c97880f0b9b


## Проверка состояния шлюза

Проверить состояние шлюза можно с помощью командлета `Get-AzureApplicationGateway`. Если командлет *Start-AzureApplicationGateway* на предыдущем этапе был выполнен успешно, параметр State (Состояние) должен получить значение *Running* (Работает), а параметры Vip и DnsName — действительные значения.

В данном примере показан рабочий шлюз приложений, готовый к приему трафика.

	PS C:> Get-AzureApplicationGateway AppGwTest 

	Name          : AppGwTest2
	Description   : 
	VnetName      : testvnet1
	Subnets       : {Subnet-1}
	InstanceCount : 2
	GatewaySize   : Medium
	State         : Running
	VirtualIPs    : {23.96.22.241}
	DnsName       : appgw-4c960426-d1e6-4aae-8670-81fd7a519a43.cloudapp.net


## Дальнейшие действия


Дополнительные сведения о параметрах балансировки нагрузки в целом см. в статьях:

- [Подсистема балансировщика нагрузки Azure](https://azure.microsoft.com/documentation/services/load-balancer/)
- [Диспетчер трафика Azure](https://azure.microsoft.com/documentation/services/traffic-manager/)

<!---HONumber=July15_HO3-->
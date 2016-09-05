<properties 
   pageTitle="Создание и настройка шлюза приложений с внутренней подсистемой балансировщика нагрузки (ILB) в виртуальной сети | Microsoft Azure"
   description="Данная страница содержит инструкции по настройке шлюза приложений Azure с конечной точкой с внутренней балансировкой нагрузки."
   documentationCenter="na"
   services="application-gateway"
   authors="georgewallace"
   manager="jdial"
   editor="tysonn"/>
<tags 
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="article" 
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services" 
   ms.date="08/19/2016"
   ms.author="gwallace"/>

# Создание шлюза приложений с внутренней подсистемой балансировщика нагрузки (ILB)

> [AZURE.SELECTOR]
- [Руководство для классической модели Azure](application-gateway-ilb.md)
- [Руководство для Resource Manager PowerShell](application-gateway-ilb-arm.md)


Шлюз приложений можно настроить на использование виртуального IP-адреса с выходом в Интернет или с внутренней конечной точкой без выхода в Интернет, которая называется также конечной точкой внутренней подсистемы балансировщика нагрузки (ILB). Настройка шлюза с внутренним балансировщиком нагрузки подходит для внутренних бизнес-приложений без доступа в Интернет. Кроме того, этот вариант конфигурации можно использовать для служб и уровней многоуровневого приложения, размещенного в периметре безопасности без доступа к Интернету, но требующего циклического перебора нагрузки, закрепления сеансов или завершения SSL-запросов. В этой статье приводятся пошаговые инструкции по настройке шлюза приложений с использованием внутреннего балансировщика нагрузки.

## Перед началом работы

1. Используя установщик веб-платформы, установите последнюю версию командлетов Azure PowerShell. Вы можете скачать ее в разделе **Windows PowerShell** на [странице загрузки](https://azure.microsoft.com/downloads/), а затем установить.
2. Убедитесь, что у вас есть рабочая виртуальная сеть с действительной подсетью.
3. Убедитесь, что у вас есть внутренние серверы в виртуальной сети или с назначенным общедоступным IP или виртуальным IP-адресом.


Чтобы создать шлюз приложений, выполните следующие действия в указанном порядке.

1. [Создание шлюза приложений](#create-a-new-application-gateway)
2. [Настройка шлюза](#configure-the-gateway)
3. [Настройка конфигурации шлюза](#set-the-gateway-configuration)
4. [Запуск шлюза](#start-the-gateway)
4. [Проверка шлюза](#verify-the-gateway-status)



## Создание шлюза приложений

**Для создания шлюза** используйте командлет `New-AzureApplicationGateway`, подставив в него свои значения. Обратите внимание, что выставление счетов для шлюза начинается не на данном этапе, а позднее, после успешного запуска шлюза.

	PS C:\> New-AzureApplicationGateway -Name AppGwTest -VnetName testvnet1 -Subnets @("Subnet-1")

	VERBOSE: 4:31:35 PM - Begin Operation: New-AzureApplicationGateway 
	VERBOSE: 4:32:37 PM - Completed Operation: New-AzureApplicationGateway
	Name       HTTP Status Code     Operation ID                             Error 
	----       ----------------     ------------                             ----
	Successful OK                   55ef0460-825d-2981-ad20-b9a8af41b399

**Чтобы проверить** создание шлюза, используйте командлет `Get-AzureApplicationGateway`.

В примере параметры *Description* (Описание), *InstanceCount* (Количество экземпляров) и *GatewaySize* (Размер шлюза) являются необязательными. Значение параметра *InstanceCount* (Количество экземпляров) по умолчанию — 2, максимальное значение — 10. Значение *GatewaySize* (Размер шлюза) по умолчанию — Medium (Средний). Также доступны значения Small (Маленький) и Large (Большой). Параметры *Vip* и *DnsName* отображаются без значений, поскольку шлюз еще не запущен. Эти значения будут заданы после его запуска.

	PS C:\> Get-AzureApplicationGateway AppGwTest

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


## Настройка шлюза

Конфигурация шлюза приложений состоит из нескольких значений. Значения конфигурации могут быть взаимосвязаны.
 
Доступны следующие значения.

- **Пул внутренних серверов:** список IP-адресов для внутренних серверов. Указываемые IP-адреса должны относиться к подсети виртуальной сети или представлять собой общедоступные IP или виртуальные IP-адреса.
- **Параметры пула внутренних серверов:** каждый пул имеет такие параметры, как порт, протокол и соответствие на основе файлов cookie. Эти параметры привязываются к пулу и применяются ко всем серверам в этом пуле.
- **Внешний порт:** общий порт, открытый в шлюзе приложений. Трафик поступает в этот порт, а затем перенаправляется на один из внутренних серверов.
- **Прослушиватель:** прослушиватель имеет внешний порт, протокол (Http или Https; регистр имеет значение) и имя SSL-сертификата (если настраивается разгрузка SSL).
- **Правило:** правило связывает прослушиватель и пул внутренних серверов и определяет, в какой пул внутренних серверов должен направляться трафик, попадающий в определенный прослушиватель. В настоящее время поддерживается только *основное* правило. *Основное* правило предусматривает циклическое распределение нагрузки.

Настроить конфигурацию можно либо путем создания объекта конфигурации, либо с помощью XML-файла конфигурации. Чтобы создать конфигурацию с помощью XML-файла конфигурации, используйте приведенный ниже пример.



Обратите внимание на следующее.


- Элемент *FrontendIPConfiguration* (IP-конфигурация внешнего интерфейса) описывает данные ILB, которые относятся к настройке шлюза приложений с использованием ILB.

- Параметр *Type* (Тип) IP-адреса внутреннего интерфейса должен иметь значение Private (Частный).

- Элементу *StaticIPAddress* должен быть присвоен необходимый внутренний IP-адрес, по которому шлюз получает трафик. Обратите внимание, что элемент *StaticIPAddress* (Статический IP-адрес) не является обязательным. Если он не задан, выбирается доступный внутренний IP-адрес из развернутой подсети.

- Значение элемента *Name* (Имя) в параметре *FrontendIPConfiguration* (Конфигурация внутреннего интерфейса) используется в элементе *FrontendIP* (IP-адрес внутреннего интерфейса) параметра HTTPListener для ссылки на параметр FrontendIPConfiguration (Конфигурация внутреннего интерфейса).

 **Пример XML-конфигурации**

 

		<?xml version="1.0" encoding="utf-8"?>
		<ApplicationGatewayConfiguration xmlns:i="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/windowsazure">
			<FrontendIPConfigurations>
				<FrontendIPConfiguration>
					<Name>fip1</Name> 
					<Type>Private</Type> 
					<StaticIPAddress>10.0.0.10</StaticIPAddress> 
				</FrontendIPConfiguration>
			</FrontendIPConfigurations>
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
					<FrontendIP>fip1</FrontendIP>
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
	


## Настройка конфигурации шлюза

Теперь шлюз приложений необходимо настроить. Для этого можно использовать командлет `Set-AzureApplicationGatewayConfig` с объектом конфигурации или с XML-файлом конфигурации.

	PS C:\> Set-AzureApplicationGatewayConfig -Name AppGwTest -ConfigFile D:\config.xml

	VERBOSE: 7:54:59 PM - Begin Operation: Set-AzureApplicationGatewayConfig 
	VERBOSE: 7:55:32 PM - Completed Operation: Set-AzureApplicationGatewayConfig
	Name       HTTP Status Code     Operation ID                             Error 
	----       ----------------     ------------                             ----
	Successful OK                   9b995a09-66fe-2944-8b67-9bb04fcccb9d

## Запуск шлюза

Настроенный шлюз можно запустить с помощью командлета `Start-AzureApplicationGateway`. Выставление счетов для шлюза приложений начинается после запуска шлюза.


> [AZURE.NOTE] Выполнение командлета `Start-AzureApplicationGateway` занимает до 15–20 минут.
   
	PS C:\> Start-AzureApplicationGateway AppGwTest 

	VERBOSE: 7:59:16 PM - Begin Operation: Start-AzureApplicationGateway 
	VERBOSE: 8:05:52 PM - Completed Operation: Start-AzureApplicationGateway
	Name       HTTP Status Code     Operation ID                             Error 
	----       ----------------     ------------                             ----
	Successful OK                   fc592db8-4c58-2c8e-9a1d-1c97880f0b9b

## Проверка состояния шлюза

Проверить состояние шлюза можно с помощью командлета `Get-AzureApplicationGateway`. Если командлет *Start-AzureApplicationGateway* на предыдущем этапе был выполнен успешно, параметр State (Состояние) должен получить значение *Running* (Работает), а параметры виртуального IP-адреса и DnsName — действительные значения. В данном примере командлет показан в первой строке, а затем выходные данные. В этом примере шлюз запущен и готов принимать трафик.

> [AZURE.NOTE] В данном примере шлюз приложений настроен для приема трафика в заданной конечной точке внутреннего балансировщика нагрузки 10.0.0.10.

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
	VirtualIPs    : {10.0.0.10}
	DnsName       : appgw-b2a11563-2b3a-4172-a4aa-226ee4c23eed.cloudapp.net

## Дальнейшие действия


Дополнительные сведения о параметрах балансировки нагрузки в целом см. в статьях:

- [Подсистема балансировщика нагрузки Azure](https://azure.microsoft.com/documentation/services/load-balancer/)
- [Диспетчер трафика Azure](https://azure.microsoft.com/documentation/services/traffic-manager/)

<!---HONumber=AcomDC_0824_2016-->
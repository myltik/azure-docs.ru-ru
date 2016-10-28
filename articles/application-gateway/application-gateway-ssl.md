<properties
   pageTitle="Настройка шлюза приложений для разгрузки SSL с помощью классического развертывания| Microsoft Azure"
   description="В этой статье приводятся инструкции по созданию шлюза приложений с разгрузкой SSL с помощью классической модели развертывания Azure."
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

# Настройка шлюза приложений для разгрузки SSL с помощью классической модели развертывания

> [AZURE.SELECTOR]
-[Azure portal](application-gateway-ssl-portal.md)
-[Azure Resource Manager PowerShell](application-gateway-ssl-arm.md)
-[Azure Classic PowerShell](application-gateway-ssl.md)

Шлюз приложений Azure можно настроить на завершение сеанса SSL в шлюзе, что позволит избежать выполнения дорогостоящей задачи SSL-шифрования на веб-ферме. Кроме того, разгрузка SSL упрощает процесс установки внешнего сервера и управления веб-приложением.


## Перед началом работы

1. Установите последнюю версию командлетов Azure PowerShell, используя установщик веб-платформы. Последнюю версию можно загрузить и установить в разделе **Windows PowerShell** на [странице загрузок](https://azure.microsoft.com/downloads/).
2. Убедитесь, что у вас есть рабочая виртуальная сеть с действительной подсетью. Убедитесь, что подсеть не используется виртуальной машиной или облачным развертыванием. Сам шлюз приложений должен находиться в подсети виртуальной сети.
3. Для использования шлюза приложений настраиваются существующие серверы или серверы, для которых в виртуальной сети созданы конечные точки либо же назначен общедоступный или виртуальный IP-адрес.

Чтобы настроить разгрузку SSL в шлюзе приложений, выполните перечисленные ниже действия в указанном порядке:

1. [Создание шлюза приложений](#create-an-application-gateway)
2. [Загрузка SSL-сертификатов](#upload-ssl-certificates)
3. [Настройка шлюза](#configure-the-gateway)
4. [Настройка конфигурации шлюза](#set-the-gateway-configuration)
5. [Запуск шлюза](#start-the-gateway)
6. [Проверка состояния шлюза](#verify-the-gateway-status)


## Создание шлюза приложений

Для создания шлюза используйте командлет **New-AzureApplicationGateway**, подставив в него свои значения. Выставление счетов для шлюза начинается не на данном этапе, а позднее, после успешного запуска шлюза.

	New-AzureApplicationGateway -Name AppGwTest -VnetName testvnet1 -Subnets @("Subnet-1")

Чтобы проверить, создан ли шлюз, используйте командлет **Get-AzureApplicationGateway**.

В примере параметры *Description* (Описание), *InstanceCount* (Количество экземпляров) и *GatewaySize* (Размер шлюза) являются необязательными. Значение параметра *InstanceCount* (Количество экземпляров) по умолчанию — 2, максимальное значение — 10. Значение *GatewaySize* (Размер шлюза) по умолчанию — Medium (Средний). Также доступны значения Small (Маленький) и Large (Большой). Параметры *VirtualIPs* и *DnsName* отображаются без значений, поскольку шлюз еще не запущен. Эти значения будут определены после запуска шлюза.

	Get-AzureApplicationGateway AppGwTest

## Загрузка SSL-сертификатов

Чтобы загрузить на шлюз приложений сертификаты сервера в формате *pfx*, используйте командлет **Add-AzureApplicationGatewaySslCertificate**. Имя сертификата выбирается пользователем и должно быть уникальным в пределах шлюза приложений. В операциях управления сертификатами в шлюзе приложений сертификат указывается по имени.

В следующем примере приведен командлет; замените значения в нем собственными.

	Add-AzureApplicationGatewaySslCertificate  -Name AppGwTest -CertificateName GWCert -Password <password> -CertificateFile <full path to pfx file>

Затем проверьте загрузку сертификата. Используйте командлет **Get AzureApplicationGatewayCertificate**.

В данном примере командлет показан в первой строке, а затем выходные данные.

	Get-AzureApplicationGatewaySslCertificate AppGwTest

	VERBOSE: 5:07:54 PM - Begin Operation: Get-AzureApplicationGatewaySslCertificate
	VERBOSE: 5:07:55 PM - Completed Operation: Get-AzureApplicationGatewaySslCertificate
	Name           : SslCert
	SubjectName    : CN=gwcert.app.test.contoso.com
	Thumbprint     : AF5ADD77E160A01A6......EE48D1A
	ThumbprintAlgo : sha1RSA
	State..........: Provisioned

>[AZURE.NOTE] Пароль сертификата должен содержать 4–12 знаков, букв или цифр. Специальные знаки не допускаются.

## Настройка шлюза

Конфигурация шлюза приложений состоит из нескольких значений. Значения конфигурации могут быть взаимосвязаны.

Доступны следующие значения.

- **Back-end server pool** (Внутренний пул серверов). Список IP-адресов внутренних серверов. Указанные IP-адреса должны относиться к подсети виртуальной сети либо представлять собой общедоступные или виртуальные IP-адреса.
- **Back-end server pool settings** (Параметры внутреннего пула серверов). Каждый пул имеет такие параметры, как порт, протокол и сходство на основе файлов cookie. Эти параметры привязываются к пулу и применяются ко всем серверам в этом пуле.
- **Внешний порт**. Общедоступный порт, открытый в шлюзе приложений. Трафик поступает на этот порт, а затем перенаправляется на один из тыловых серверов.
- **Прослушиватель**. У прослушивателя есть внешний порт, протокол (Http или Https — с учетом регистра) и имя SSL-сертификата (в случае настройки разгрузки SSL).
- **Правило**. Правило связывает прослушиватель и внутренний пул серверов, а также определяет, в какой внутренний пул серверов следует направлять трафик, поступающий на определенный прослушиватель. Сейчас поддерживается только *основное* правило. *Основное* правило предусматривает циклический перебор при распределении нагрузки.

**Дополнительные примечания по настройке конфигурации**

Чтобы настроить конфигурацию SSL-сертификатов, протокол в элементе **HttpListener** следует изменить на *Https* (с учетом регистра). К **HttpListener** добавляется элемент **SslCert** со значением, соответствующим имени, которое использовалось выше при передаче SSL-сертификатов. Внешний порт следует изменить на 443.

**Включение сходства на основе файлов cookie**. Шлюз приложений можно настроить так, чтобы запросы от клиентского сеанса всегда направлялись на одну виртуальную машину в веб-ферме. Это делается с помощью внедрения файлов cookie сеанса, что позволяет шлюзу перенаправлять трафик соответствующим образом. Чтобы включить сходство на основе файлов cookie, присвойте параметру **CookieBasedAffinity** в элементе **BackendHttpSettings** значение *Enabled*.



Настроить конфигурацию можно либо путем создания объекта конфигурации, либо с помощью XML-файла конфигурации. Чтобы создать конфигурацию с помощью XML-файла конфигурации, используйте следующий пример.

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

Теперь шлюз приложений необходимо настроить. Для этого можно использовать командлет **Set-AzureApplicationGatewayConfig** с объектом конфигурации или с XML-файлом конфигурации.

	Set-AzureApplicationGatewayConfig -Name AppGwTest -ConfigFile D:\config.xml

## Запуск шлюза

Настроенный шлюз можно запустить с помощью командлета **Start-AzureApplicationGateway**. Выставление счетов для шлюза приложений начинается после запуска шлюза.


**Примечание.** На выполнение командлета **Start-AzureApplicationGateway** может потребоваться до 15–20 минут.

	Start-AzureApplicationGateway AppGwTest

## Проверка состояния шлюза

Чтобы проверить состояние шлюза, используйте командлет **Get-AzureApplicationGateway**. Если командлет **Start-AzureApplicationGateway** на предыдущем этапе был выполнен успешно, параметр State должен иметь значение *Работает*, а параметры *VirtualIPs* и *DnsName* должны иметь действительные значения.

В данном примере показан рабочий шлюз приложений, готовый к приему трафика.

	Get-AzureApplicationGateway AppGwTest

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
- [Azure Traffic Manager](https://azure.microsoft.com/documentation/services/traffic-manager/)

<!---HONumber=AcomDC_0921_2016-->
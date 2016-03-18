<properties
   pageTitle="Создание пользовательской проверки для шлюза приложений с помощью PowerShell в классической модели развертывания | Microsoft Azure"
   description="Узнайте, как создавать пользовательские проверки для шлюза приложений с помощью PowerShell в классической модели развертывания."
   services="application-gateway"
   documentationCenter="na"
   authors="joaoma"
   manager="carmonm"
   editor=""
   tags="azure-service-management"
/>
<tags  
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="12/17/2015"
   ms.author="joaoma" />

# Создание пользовательской проверки для шлюза приложений (классического) Azure с помощью PowerShell


[AZURE.INCLUDE [azure-probe-intro-include](../../includes/application-gateway-create-probe-intro-include.md)].

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-classic-include.md)] [Resource Manager model](application-gateway-create-probe-ps.md).


[AZURE.INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]


## Создание нового шлюза приложений

Создание шлюза приложений:

1. Создать ресурс шлюза приложений.
2. Создать XML-файл конфигурации или объект конфигурации.
3. Применить конфигурацию к созданному ресурсу шлюза приложений.

### Создание ресурса шлюза приложений

Для создания шлюза используйте командлет **New-AzureApplicationGateway**, подставив в него свои значения. Обратите внимание, что выставление счетов для шлюза начинается не на данном этапе, а позднее, после успешного запуска шлюза.

В следующем примере создается новый шлюз приложений с использованием виртуальной сети "testvnet1" и подсети "subnet-1".


	PS C:\> New-AzureApplicationGateway -Name AppGwTest -VnetName testvnet1 -Subnets @("Subnet-1")

	VERBOSE: 4:31:35 PM - Begin Operation: New-AzureApplicationGateway
	VERBOSE: 4:32:37 PM - Completed Operation: New-AzureApplicationGateway
	Name       HTTP Status Code     Operation ID                             Error
	----       ----------------     ------------                             ----
	Successful OK                   55ef0460-825d-2981-ad20-b9a8af41b399


 *Description*, *InstanceCount* и *GatewaySize* — необязательные параметры.


Чтобы проверить создание шлюза, используйте командлет **Get-AzureApplicationGateway**.


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

>[AZURE.NOTE]Значение параметра *InstanceCount* по умолчанию — 2 (максимальное значение — 10). По умолчанию для параметра *GatewaySize* используется значение Medium. Можно выбрать значения Small, Medium или Large.


 Параметры *VirtualIPs* и *DnsName* отображаются без значений, поскольку шлюз еще не запущен. Эти значения будут заданы, как только шлюз перейдет в рабочее состояние.

## Настройка шлюза приложений

Можно настроить шлюз приложений с помощью XML-файла или объекта конфигурации.

## Настройка шлюза приложений с помощью XML-файла

В следующем примере все параметры шлюза приложений настраиваются и применяются к ресурсу шлюза приложений при помощи XML-файла.

### Шаг 1  

Скопируйте следующий текст в Блокнот.


	<ApplicationGatewayConfiguration xmlns:i="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/windowsazure">
    <FrontendIPConfigurations>
        <FrontendIPConfiguration>
            <Name>fip1</Name>
            <Type>Private</Type>
        </FrontendIPConfiguration>
    </FrontendIPConfigurations>    
	<FrontendPorts>
        <FrontendPort>
            <Name>port1</Name>
            <Port>80</Port>
        </FrontendPort>
    </FrontendPorts>
    <Probes>
        <Probe>
            <Name>Probe01</Name>
            <Protocol>Http</Protocol>
            <Host>contoso.com</Host>
            <Path>/path/custompath.htm</Path>
            <Interval>15</Interval>
            <Timeout>15</Timeout>
            <UnhealthyThreshold>5</UnhealthyThreshold>
        </Probe>
    <BackendAddressPools>
        <BackendAddressPool>
            <Name>pool1</Name>
            <IPAddresses>
                <IPAddress>1.1.1.1</IPAddress>
				<IPAddress>2.2.2.2</IPAddress>
            </IPAddresses>
        </BackendAddressPool>
    </BackendAddressPools>
    <BackendHttpSettingsList>
        <BackendHttpSettings>
            <Name>setting1</Name>
            <Port>80</Port>
            <Protocol>Http</Protocol>
            <CookieBasedAffinity>Enabled</CookieBasedAffinity>
            <RequestTimeout>120</RequestTimeout>
            <Probe>Probe01</Probe>
        </BackendHttpSettings>
    </BackendHttpSettingsList>
    <HttpListeners>
        <HttpListener>
            <Name>listener1</Name>
            <FrontendIP>fip1</FrontendIP>
	    <FrontendPort>port1</FrontendPort>
            <Protocol>Http</Protocol>
        </HttpListener>
    </HttpListeners>
    <HttpLoadBalancingRules>
        <HttpLoadBalancingRule>
            <Name>lbrule1</Name>
            <Type>basic</Type>
            <BackendHttpSettings>setting1</BackendHttpSettings>
            <Listener>listener1</Listener>
            <BackendAddressPool>pool1</BackendAddressPool>
        </HttpLoadBalancingRule>
    </HttpLoadBalancingRules>
	</ApplicationGatewayConfiguration>


Измените значения в скобках для элементов конфигурации. Сохраните файл с расширением XML.

В приведенном ниже примере показано, как использовать файл конфигурации, чтобы настроить шлюз приложений для балансировки нагрузки HTTP-трафика на общем порте 80 и направления сетевого трафика на внутренний порт 80 между двумя IP-адресами с помощью пользовательской проверки.

>[AZURE.IMPORTANT]В элементе протокола HTTP или HTTPS учитывается регистр.


Новый элемент конфигурации <Probe> добавляется для настройки пользовательских проверок работоспособности.

Используются следующие параметры конфигурации:

- **Name** — имя пользовательской проверки.
- **Protocol** — используемый протокол (возможные значения: HTTP или HTTPS).
- **Host** и **Path** — полный путь URL, который вызывается шлюзом приложений для определения работоспособности экземпляра. Например, если у вас веб-сайт http://contoso.com/, то пользовательскую проверку работоспособности для получения успешного ответа HTTP можно настроить как "http://contoso.com/path/custompath.htm".
- **Interval** — задает интервал проверки работоспособности в секундах.
- **Timeout** — определяет время ожидания для проверки ответа HTTP.
- **UnhealthyThreshold** — количество неудачных ответов HTTP, по достижении которого экземпляр внутреннего сервера считается *неработоспособным*.

Имя проверки указывается в конфигурации <BackendHttpSettings> для назначения пула внутренних серверов, который будет использовать параметры пользовательской проверки.

## Добавление конфигурации пользовательской проверки к существующему шлюзу приложений

Изменение текущей конфигурации шлюза приложений состоит из трех шагов: получение XML-файла конфигурации, внесение изменений для пользовательской проверки и настройка шлюза приложений с использованием новых параметров XML.

### Шаг 1

Получите XML-файл с помощью командлета Get-AzureApplicationGatewayConfig. Эта команда экспортирует XML-файл, после чего в него можно будет внести изменения для пользовательской проверки.

	get-AzureApplicationGatewayConfig -Name <application gateway name> -Exporttofile "<path to file>"


### Шаг 2

Откройте XML-файл в текстовом редакторе. Добавьте фрагмент `<probe>` после `<frontendport>`.

	<Probes>
        <Probe>
            <Name>Probe01</Name>
            <Protocol>Http</Protocol>
            <Host>contoso.com</Host>
            <Path>/path/custompath.htm</Path>
            <Interval>15</Interval>
            <Timeout>15</Timeout>
            <UnhealthyThreshold>5</UnhealthyThreshold>
        </Probe>

В разделе backendHttpSettings XML-файла добавьте имя проверки, как показано в следующем примере:

        <BackendHttpSettings>
            <Name>setting1</Name>
            <Port>80</Port>
            <Protocol>Http</Protocol>
            <CookieBasedAffinity>Enabled</CookieBasedAffinity>
            <RequestTimeout>120</RequestTimeout>
            <Probe>Probe01</Probe>
        </BackendHttpSettings>

Сохраните XML-файл.


### Шаг 3.

Обновите конфигурацию шлюза приложений с использованием нового файла XML с помощью командлета **Set-AzureApplicationGatewayConfig**. Эта команда обновит шлюз приложений с новой конфигурацией.

	set-AzureApplicationGatewayConfig -Name <application gateway name> -Configfile "<path to file>"


## Дальнейшие действия

Указания по настройке разгрузки SSL см. в статье [Настройка шлюза приложений для разгрузки SSL](application-gateway-ssl.md).

Указания по настройке шлюза приложений для использования с ILB см. в статье [Создание шлюза приложений с внутренним балансировщиком нагрузки (ILB)](application-gateway-ilb.md).

<!---HONumber=AcomDC_0114_2016-->
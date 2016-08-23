<properties
   pageTitle="Создание пользовательской проверки для шлюза приложений с помощью PowerShell в классической модели развертывания | Microsoft Azure"
   description="Узнайте, как создавать пользовательские проверки для шлюза приложений с помощью PowerShell в классической модели развертывания."
   services="application-gateway"
   documentationCenter="na"
   authors="georgewallace"
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
   ms.date="08/09/2016"
   ms.author="gwallace" />

# Создание пользовательской проверки для шлюза приложений (классического) Azure с помощью PowerShell

> [AZURE.SELECTOR]
- [Портал Azure](application-gateway-create-probe-portal.md)
- [PowerShell и диспетчер ресурсов Azure](application-gateway-create-probe-ps.md)
- [Классическая модель — Azure PowerShell](application-gateway-create-probe-classic-ps.md)

<BR>

[AZURE.INCLUDE [azure-probe-intro-include](../../includes/application-gateway-create-probe-intro-include.md)].

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-classic-include.md)] Узнайте, как [выполнить эти действия с помощью модели Resource Manager](application-gateway-create-probe-ps.md).

[AZURE.INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]


## Создание шлюза приложений

Создание шлюза приложений:

1. Создайте ресурс шлюза приложений.
2. Создайте XML-файл конфигурации или объект конфигурации.
3. Применить конфигурацию к созданному ресурсу шлюза приложений.

### Создание ресурса шлюза приложений

Для создания шлюза используйте командлет **New-AzureApplicationGateway**, подставив в него свои значения. Выставление счетов для шлюза начинается не на данном этапе, а позднее, после успешного запуска шлюза.

В следующем примере создается шлюз приложений с использованием виртуальной сети testvnet1 и подсети subnet-1.


	PS C:\> New-AzureApplicationGateway -Name AppGwTest -VnetName testvnet1 -Subnets @("Subnet-1")

	VERBOSE: 4:31:35 PM - Begin Operation: New-AzureApplicationGateway
	VERBOSE: 4:32:37 PM - Completed Operation: New-AzureApplicationGateway
	Name       HTTP Status Code     Operation ID                             Error
	----       ----------------     ------------                             ----
	Successful OK                   55ef0460-825d-2981-ad20-b9a8af41b399


 *Description*, *InstanceCount* и *GatewaySize* — необязательные параметры.


Чтобы проверить, создан ли шлюз, используйте командлет **Get-AzureApplicationGateway**.


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

>[AZURE.NOTE]  Значение параметра *InstanceCount* по умолчанию — 2 (максимальное значение — 10). По умолчанию для параметра *GatewaySize* используется значение Medium. Можно выбрать значения Small, Medium или Large.


 Параметры *VirtualIPs* и *DnsName* отображаются без значений, так как шлюз еще не запущен. Эти значения будут заданы после его запуска.

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
      </Probes>
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

В приведенном ниже примере показано, как использовать файл конфигурации, чтобы настроить шлюз приложений для балансировки нагрузки HTTP-трафика на общем порте 80 и направления сетевого трафика на внутренний порт 80 между двумя IP-адресами с помощью пользовательской пробы.

>[AZURE.IMPORTANT] В элементе протокола HTTP или HTTPS учитывается регистр.


Новый элемент конфигурации <Probe> добавляется для настройки пользовательских проверок работоспособности.

Используются следующие параметры конфигурации:

- **Name** — имя пользовательской пробы.
- **Protocol** — используемый протокол (возможные значения: HTTP или HTTPS).
- **Host** и **Path** — полный путь URL-адреса, который вызывается шлюзом приложений для определения работоспособности экземпляра. Например, если у вас есть веб-сайт http://contoso.com/, то пользовательскую пробу работоспособности для получения успешного ответа HTTP можно настроить в формате http://contoso.com/path/custompath.htm.
- **Interval** — задает интервал между пробами в секундах.
- **Timeout** — определяет время ожидания для проверки ответа HTTP.
- **UnhealthyThreshold** — количество неудачных ответов HTTP, по достижении которого серверный экземпляр считается *неработоспособным*.

Имя пробы указывается в конфигурации <BackendHttpSettings> для назначения внутреннего пула, который будет использовать параметры пользовательской пробы.

## Добавление конфигурации пользовательской проверки к существующему шлюзу приложений

Изменение текущей конфигурации шлюза приложений состоит из трех шагов: получение XML-файла конфигурации, внесение изменений для пользовательской проверки и настройка шлюза приложений с использованием новых параметров XML.

### Шаг 1

Получите XML-файл с помощью командлета Get-AzureApplicationGatewayConfig. Он экспортирует XML-файл конфигурации, который можно изменить, чтобы добавить параметры пробы.

	get-AzureApplicationGatewayConfig -Name <application gateway name> -Exporttofile "<path to file>"


### Шаг 2

Откройте XML-файл в текстовом редакторе. Добавьте раздел `<probe>` после `<frontendport>`.

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

В разделе backendHttpSettings XML-файла добавьте имя пробы, как показано в следующем примере.

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

Обновите конфигурацию шлюза приложений с использованием нового XML-файла, выполнив командлет **Set-AzureApplicationGatewayConfig**. Он команда обновит шлюз приложений с учетом новой конфигурации.

	set-AzureApplicationGatewayConfig -Name <application gateway name> -Configfile "<path to file>"


## Дальнейшие действия

Указания по настройке разгрузки SSL см. в статье [Настройка шлюза приложений для разгрузки SSL](application-gateway-ssl.md).

Указания по настройке шлюза приложений для использования с внутренним балансировщиком нагрузки см. в статье [Создание шлюза приложений с внутренней подсистемой балансировщика нагрузки (ILB)](application-gateway-ilb.md).

<!---HONumber=AcomDC_0810_2016-->
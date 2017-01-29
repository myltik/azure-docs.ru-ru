---
title: "Создание пользовательской проверки для шлюза приложений с помощью PowerShell в классической модели развертывания | Документация Майкрософт"
description: "Узнайте, как создавать пользовательские проверки для шлюза приложений с помощью PowerShell в классической модели развертывания."
services: application-gateway
documentationcenter: na
author: georgewallace
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 338a7be1-835c-48e9-a072-95662dc30f5e
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/13/2016
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: aaf13418331f29287399621cb911e4b9f5b33dc0
ms.openlocfilehash: a995495f003edbff6cd0a4a15d09585458664f78


---
# <a name="create-a-custom-probe-for-azure-application-gateway-classic-by-using-powershell"></a>Создание пользовательской проверки для шлюза приложений (классического) Azure с помощью PowerShell

> [!div class="op_single_selector"]
> * [Портал Azure](application-gateway-create-probe-portal.md)
> * [PowerShell и диспетчер ресурсов Azure](application-gateway-create-probe-ps.md)
> * [Классическая модель — Azure PowerShell](application-gateway-create-probe-classic-ps.md)


[!INCLUDE [azure-probe-intro-include](../../includes/application-gateway-create-probe-intro-include.md)]

> [!IMPORTANT]
> В Azure предлагаются две модели развертывания для создания ресурсов и работы с ними: [модель диспетчера ресурсов и классическая модель](../azure-resource-manager/resource-manager-deployment-model.md). В этой статье рассматривается использование классической модели развертывания. Для большинства новых развертываний Майкрософт рекомендует использовать модель диспетчера ресурсов. Узнайте, как [выполнить эти действия с помощью модели Resource Manager](application-gateway-create-probe-ps.md).

[!INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## <a name="create-an-application-gateway"></a>Создание шлюза приложений

Создание шлюза приложений:

1. Создайте ресурс шлюза приложений.
2. Создайте XML-файл конфигурации или объект конфигурации.
3. Применить конфигурацию к созданному ресурсу шлюза приложений.

### <a name="create-an-application-gateway-resource"></a>Создание ресурса шлюза приложений.

Для создания шлюза используйте командлет `New-AzureApplicationGateway`, подставив в него свои значения. Выставление счетов для шлюза начинается не на данном этапе, а позднее, после успешного запуска шлюза.

В следующем примере создается шлюз приложений с использованием виртуальной сети testvnet1 и подсети subnet-1.

```powershell
New-AzureApplicationGateway -Name AppGwTest -VnetName testvnet1 -Subnets @("Subnet-1")
```

Чтобы проверить создание шлюза, используйте командлет `Get-AzureApplicationGateway`.

```powershell
Get-AzureApplicationGateway AppGwTest
```

> [!NOTE]
> Значение параметра *InstanceCount* по умолчанию — 2 (максимальное значение — 10). По умолчанию для параметра *GatewaySize* используется значение Medium. Можно выбрать значения Small, Medium или Large.
> 
> 

Параметры *VirtualIPs* и *DnsName* отображаются без значений, так как шлюз еще не запущен. Эти значения будут определены после запуска шлюза.

## <a name="configure-an-application-gateway"></a>Настройка шлюза приложений

Можно настроить шлюз приложений с помощью XML-файла или объекта конфигурации.

## <a name="configure-an-application-gateway-by-using-xml"></a>Настройка шлюза приложений с помощью XML-файла

В следующем примере все параметры шлюза приложений настраиваются и применяются к ресурсу шлюза приложений при помощи XML-файла.  

### <a name="step-1"></a>Шаг 1

Скопируйте следующий текст в Блокнот.

```xml
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
```

Измените значения в скобках для элементов конфигурации. Сохраните файл с расширением XML.

В приведенном ниже примере показано, как использовать файл конфигурации, чтобы настроить шлюз приложений для балансировки нагрузки HTTP-трафика на общем порте 80 и направления сетевого трафика на внутренний порт 80 между двумя IP-адресами с помощью пользовательской проверки.

> [!IMPORTANT]
> В элементе протокола HTTP или HTTPS учитывается регистр.

Новый элемент конфигурации \<Probe\> добавляется для настройки пользовательских проверок работоспособности.

Используются следующие параметры конфигурации:

* **Name** — имя пользовательской пробы.
* **Protocol** — используемый протокол (возможные значения: HTTP или HTTPS).
* **Host** и **Path** — полный путь URL-адреса, который вызывается шлюзом приложений для определения работоспособности экземпляра. Например, если у вас есть веб-сайт http://contoso.com/, пользовательскую пробу работоспособности для получения успешного ответа HTTP можно настроить в формате http://contoso.com/path/custompath.htm.
* **Interval** — задает интервал между пробами в секундах.
* **Timeout** — определяет время ожидания для проверки ответа HTTP.
* **UnhealthyThreshold** — количество неудачных ответов HTTP, по достижении которого серверный экземпляр считается *неработоспособным*.

Имя пробы указывается в конфигурации \<BackendHttpSettings\> для назначения пула серверной части, который будет использовать параметры пользовательской пробы.

## <a name="add-a-custom-probe-configuration-to-an-existing-application-gateway"></a>Добавление конфигурации пользовательской проверки к существующему шлюзу приложений

Изменение текущей конфигурации шлюза приложений состоит из трех шагов: получение XML-файла конфигурации, внесение изменений для пользовательской проверки и настройка шлюза приложений с использованием новых параметров XML.

### <a name="step-1"></a>Шаг 1

Получите XML-файл с помощью командлета `Get-AzureApplicationGatewayConfig`. Он экспортирует XML-файл конфигурации, который нужно изменить, чтобы добавить параметры пробы.

```powershell
Get-AzureApplicationGatewayConfig -Name "<application gateway name>" -Exporttofile "<path to file>"
```

### <a name="step-2"></a>Шаг 2

Откройте XML-файл в текстовом редакторе. Добавьте раздел `<probe>` после `<frontendport>`.

```xml
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
```

В разделе backendHttpSettings XML-файла добавьте имя пробы, как показано в следующем примере.

```xml
    <BackendHttpSettings>
        <Name>setting1</Name>
        <Port>80</Port>
        <Protocol>Http</Protocol>
        <CookieBasedAffinity>Enabled</CookieBasedAffinity>
        <RequestTimeout>120</RequestTimeout>
        <Probe>Probe01</Probe>
    </BackendHttpSettings>
```

Сохраните XML-файл.

### <a name="step-3"></a>Шаг 3.

Обновите конфигурацию шлюза приложения с помощью нового XML-файла, используя командлет `Set-AzureApplicationGatewayConfig`. Он обновит шлюз приложений с учетом новой конфигурации.

```powershell
Set-AzureApplicationGatewayConfig -Name "<application gateway name>" -Configfile "<path to file>"
```

## <a name="next-steps"></a>Дальнейшие действия

Указания по настройке разгрузки SSL см. в статье о [настройке шлюза приложений для разгрузки SSL](application-gateway-ssl.md).

Указания по настройке шлюза приложений для использования с внутренним балансировщиком нагрузки см. в статье [Создание шлюза приложений с внутренней подсистемой балансировщика нагрузки (ILB)](application-gateway-ilb.md).




<!--HONumber=Dec16_HO3-->



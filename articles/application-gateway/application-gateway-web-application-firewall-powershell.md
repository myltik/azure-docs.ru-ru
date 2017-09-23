---
title: "Настройка брандмауэра веб-приложения для шлюза приложений Azure | Документация Майкрософт"
description: "Эта статья содержит рекомендации о том, как приступить к работе с брандмауэром веб-приложения на имеющемся или новом шлюзе приложений."
documentationcenter: na
services: application-gateway
author: davidmu1
manager: timlt
editor: tysonn
ms.assetid: 670b9732-874b-43e6-843b-d2585c160982
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/03/2017
ms.author: davidmu
ms.translationtype: HT
ms.sourcegitcommit: a0b98d400db31e9bb85611b3029616cc7b2b4b3f
ms.openlocfilehash: 3d01763709e58d25047a6dec4361bcf6553d54e0
ms.contentlocale: ru-ru
ms.lasthandoff: 08/29/2017

---
# <a name="configure-a-web-application-firewall-on-a-new-or-existing-application-gateway"></a>Настройка брандмауэра веб-приложения на новом или существующем шлюзе приложения

> [!div class="op_single_selector"]
> * [Портал Azure](application-gateway-web-application-firewall-portal.md)
> * [PowerShell](application-gateway-web-application-firewall-powershell.md)
> * [Интерфейс командной строки Azure](application-gateway-web-application-firewall-cli.md)

Узнайте, как создать шлюз приложений с брандмауэром веб-приложения (WAF), а также узнайте о добавлении WAF в имеющийся шлюз приложений.

WAF в шлюзе приложений Azure защищает веб-приложения от таких распространенных сетевых атак, как атаки путем внедрения кода SQL, атаки межсайтовых сценариев и захваты сеанса.

 Шлюз приложений — это балансировщик нагрузки уровня 7. Он отвечает за отработку отказов и выполнение маршрутизации HTTP-запросов между разными серверами (облачными и локальными). Шлюз приложений предоставляет множество функций контроллера доставки приложений (ADC):

 * балансировка нагрузки HTTP;
 * сходство сеансов на основе файлов cookie;
 * разгрузка SSL;
 * пользовательские пробы работоспособности;
 * поддержка функции мультисайта.
 
 Полный список поддерживаемых функций представлен в [обзоре шлюза приложений](application-gateway-introduction.md).

В этой статье также показано, как [добавлять WAF в имеющийся шлюз приложения](#add-web-application-firewall-to-an-existing-application-gateway). Здесь также показано, как [создать шлюз приложений, использующий WAF](#create-an-application-gateway-with-web-application-firewall).

![Образ для сценария][scenario]

## <a name="waf-configuration-differences"></a>Различия в конфигурации WAF

Если вы прочли статью [Создание, запуск или удаление шлюза приложений с помощью диспетчера ресурсов Azure](application-gateway-create-gateway-arm.md), то уже понимаете, какие параметры SKU нужно настроить при создании шлюза приложений. При настройке SKU для шлюза приложений доступны дополнительные параметры, относящиеся к WAF. Какие-либо изменения самого шлюза приложений не требуются.

| **Параметр** | **Дополнительные сведения**
|---|---|
|**SKU** |Обычный шлюз приложений без WAF поддерживает размеры **Standard\_Small**, **Standard\_Medium** и **Standard\_Large**. После добавления WAF становятся доступны еще два SKU, **WAF\_Medium** и **WAF\_Large**. WAF не поддерживается в шлюзах приложения уровня "Мелкий".|
|**Уровень** | Доступные значения: **Стандартный** или **WAF**. При использовании WAF необходимо выбрать **WAF**.|
|**Режим** | Этот параметр определяет режим WAF. Допустимые значения: **Обнаружение** и **Предотвращение**. Если WAF работает в режиме **обнаружения**, все угрозы заносятся в файл журнала. В режиме **предотвращения** события по-прежнему регистрируются в журнале, но злоумышленник получает от шлюза приложений ответ "403 — не авторизовано".|

## <a name="add-a-web-application-firewall-to-an-existing-application-gateway"></a>Добавление брандмауэра веб-приложения в имеющийся шлюз приложений

Убедитесь, что у вас установлена последняя версия Azure PowerShell. Дополнительные сведения см. в статье [Управление ресурсами с помощью Azure PowerShell и Resource Manager](../powershell-azure-resource-manager.md).

1. Войдите в учетную запись Azure.

    ```powershell
    Login-AzureRmAccount
    ```

2. Выберите подписку для этого сценария.

    ```powershell
    Select-AzureRmSubscription -SubscriptionName "<Subscription name>"
    ```

3. Извлеките шлюз, в котором необходимо добавить WAF.

    ```powershell
    $gw = Get-AzureRmApplicationGateway -Name "AdatumGateway" -ResourceGroupName "MyResourceGroup"
    ```

4. Настройте номер SKU для WAF. Доступные размеры: **WAF\_Large** и **WAF\_Medium**. При использовании WAF следует задать категорию **WAF**. Подтвердите емкость, когда задаете номер SKU.

    ```powershell
    $gw | Set-AzureRmApplicationGatewaySku -Name WAF_Large -Tier WAF -Capacity 2
    ```

5. Настройте параметры WAF, как указано в следующем примере. Для параметра **FirewallMode** можно указать значение **Предотвращение** и **Обнаружение**.

    ```powershell
    $gw | Set-AzureRmApplicationGatewayWebApplicationFirewallConfiguration -Enabled $true -FirewallMode Prevention
    ```

6. Обновите шлюз приложений с помощью параметров, определенных на предыдущем шаге.

    ```powershell
    Set-AzureRmApplicationGateway -ApplicationGateway $gw
    ```

Эта команда обновляет шлюз приложений, добавляя в него WAF. Чтобы узнать, как просматривать журналы шлюза приложений, ознакомьтесь со статьей [Работоспособность серверной части, журналы диагностики и метрики для шлюза приложений](application-gateway-diagnostics.md). Из-за особенностей системы безопасности WAF необходимо регулярно просматривать журналы, чтобы понимать состояние безопасности веб-приложений.

## <a name="create-an-application-gateway-with-a-web-application-firewall"></a>Создание шлюза приложений с брандмауэром веб-приложения

Ниже описаны шаги для создания шлюза приложений с WAF.

Убедитесь, что у вас установлена последняя версия Azure PowerShell. Дополнительные сведения см. в статье [Управление ресурсами с помощью Azure PowerShell и Resource Manager](../powershell-azure-resource-manager.md).

1. Войдите на портал Azure, выполнив команду `Login-AzureRmAccount`. Вам будет предложено указать свои учетные данные для проверки подлинности.

2. Проверьте подписки учетной записи, выполнив команду `Get-AzureRmSubscription`.

3. Выберите необходимую подписку Azure.

    ```powershell
    Select-AzureRmsubscription -SubscriptionName "<Subscription name>"
    ```

### <a name="create-a-resource-group"></a>Создание группы ресурсов

Создайте группу ресурсов для шлюза приложений.

```powershell
New-AzureRmResourceGroup -Name appgw-rg -Location "West US"
```

В диспетчере ресурсов Azure для всех групп ресурсов должно быть указано расположение. Это расположение используется в качестве расположения по умолчанию для всех ресурсов данной группы. Убедитесь, что во всех командах для создания шлюза приложений используется одна группа ресурсов.

В приведенном выше примере мы создали группу ресурсов appgw-RG в расположении "Западная часть США".

> [!NOTE]
> Если вам нужно настроить пользовательскую пробу для шлюза приложений, ознакомьтесь со статьей [Создание пользовательской проверки для шлюза приложений с помощью PowerShell для диспетчера ресурсов Azure](application-gateway-create-probe-ps.md). Дополнительные сведения см. в статье [Обзор мониторинга работоспособности шлюза приложений](application-gateway-probe-overview.md).

### <a name="configure-a-virtual-network"></a>Настройка виртуальной сети

Шлюзу приложений требуется собственная подсеть. На этом шаге создается виртуальная сеть с адресным пространством 10.0.0.0/16 и две подсети: одна для шлюза приложений и одна для членов внутреннего пула.

```powershell
# Create a subnet configuration object for the application gateway subnet. A subnet for an application should have a minimum of 28 mask bits. This value leaves 10 available addresses in the subnet for application gateway instances. With a smaller subnet, you might not be able to add more instances of your application gateway in the future.
$gwSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name 'appgwsubnet' -AddressPrefix 10.0.0.0/24

# Create a subnet configuration object for the back-end pool members subnet.
$nicSubnet = New-AzureRmVirtualNetworkSubnetConfig  -Name 'appsubnet' -AddressPrefix 10.0.2.0/24

# Create the virtual network with the previously created subnets.
$vnet = New-AzureRmvirtualNetwork -Name 'appgwvnet' -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $gwSubnet, $nicSubnet
```

### <a name="configure-the-public-ip-address"></a>Настройка общедоступного IP-адреса

Для обработки внешних запросов шлюзу приложений необходим общедоступный IP-адрес. Это общедоступный IP-адрес не должен иметь определений `DomainNameLabel` для использования шлюзом приложений.

```powershell
# Create a public IP address for use with the application gateway. Defining the `DomainNameLabel` during creation is not supported for use with the application gateway.
$publicip = New-AzureRmPublicIpAddress -ResourceGroupName appgw-rg -name 'appgwpip' -Location "West US" -AllocationMethod Dynamic
```

### <a name="configure-the-application-gateway"></a>Настройка шлюза приложений

```powershell
# Create an IP configuration to configure which subnet the application gateway uses. When the application gateway starts, it picks up an IP address from the configured subnet and routes network traffic to the IP addresses in the back-end IP pool.
$gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name 'gwconfig' -Subnet $gwSubnet

# Create a back-end pool to hold the addresses or NIC handles for the application that the application gateway is protecting.
$pool = New-AzureRmApplicationGatewayBackendAddressPool -Name 'pool01' -BackendIPAddresses 1.1.1.1, 2.2.2.2, 3.3.3.3

# Upload the authentication certificate to be used to communicate with the back-end servers.
$authcert = New-AzureRmApplicationGatewayAuthenticationCertificate -Name 'whitelistcert1' -CertificateFile <full path to .cer file>

# Configure the back-end HTTP settings to be used to define how traffic is routed to the back-end pool. The authentication certificate used in the previous step is added to the back-end HTTP settings.
$poolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name 'setting01' -Port 443 -Protocol Https -CookieBasedAffinity Enabled -AuthenticationCertificates $authcert

# Create a front-end port to be used by the listener.
$fp = New-AzureRmApplicationGatewayFrontendPort -Name 'port01'  -Port 443

# Create a front-end IP configuration to associate the public IP address with the application gateway.
$fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig -Name 'fip01' -PublicIPAddress $publicip

# Configure the certificate for the application gateway. This certificate is used to decrypt and re-encrypt the traffic on the application gateway.
$cert = New-AzureRmApplicationGatewaySslCertificate -Name cert01 -CertificateFile <full path to .pfx file> -Password <password for certificate file>

# Create the HTTP listener for the application gateway. Assign the front-end IP configuration, port, and SSL certificate to use.
$listener = New-AzureRmApplicationGatewayHttpListener -Name listener01 -Protocol Https -FrontendIPConfiguration $fipconfig -FrontendPort $fp -SslCertificate $cert

# Create a load-balancer routing rule that configures the load balancer behavior. In this example, a basic round-robin rule is created.
$rule = New-AzureRmApplicationGatewayRequestRoutingRule -Name 'rule01' -RuleType basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool

# Configure the SKU of the application gateway.
$sku = New-AzureRmApplicationGatewaySku -Name WAF_Medium -Tier WAF -Capacity 2

# Define the SSL policy to use.
$policy = New-AzureRmApplicationGatewaySslPolicy -PolicyType Predefined -PolicyName AppGwSslPolicy20170401S

# Configure the WAF configuration settings.
$config = New-AzureRmApplicationGatewayWebApplicationFirewallConfiguration -Enabled $true -FirewallMode "Prevention"

# Create the application gateway by using all the previously created configuration objects.
$appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku -WebApplicationFirewallConfig $config -SslCertificates $cert -AuthenticationCertificates $authcert
```

> [!NOTE]
> Для защиты шлюзов приложений, созданных с помощью базовой конфигурации WAF, настраивается CRS 3.0.

## <a name="get-an-application-gateway-dns-name"></a>Получение DNS-имени шлюза приложений

После создания шлюза следует настроить внешний интерфейс для обмена данными. Если вы используете общедоступный IP-адрес, шлюзу приложений требуется динамически назначаемое непонятное имя DNS. Чтобы пользователи гарантированно попали в шлюз приложений, используйте запись CNAME, чтобы указать общедоступную конечную точку шлюза приложений. Дополнительные сведения см. в статье [Настройка пользовательского доменного имени для облачной службы Azure](../cloud-services/cloud-services-custom-domain-name-portal.md). 

Чтобы настроить псевдоним, извлеките подробные сведения о шлюзе приложений и соответствующий IP-адрес или DNS-имя с помощью элемента PublicIPAddress, связанного со шлюзом приложений. Используйте DNS-имя шлюза приложений для создания записи CNAME, указывающей двум веб-приложениям на это DNS-имя. Мы не рекомендуем использовать записи типа А, так как виртуальный IP-адрес может измениться при перезапуске шлюза приложений.

```powershell
Get-AzureRmPublicIpAddress -ResourceGroupName appgw-RG -Name publicIP01
```

```
Name                     : publicIP01
ResourceGroupName        : appgw-RG
Location                 : westus
Id                       : /subscriptions/<subscription_id>/resourceGroups/appgw-RG/providers/Microsoft.Network/publicIPAddresses/publicIP01
Etag                     : W/"00000d5b-54ed-4907-bae8-99bd5766d0e5"
ResourceGuid             : 00000000-0000-0000-0000-000000000000
ProvisioningState        : Succeeded
Tags                     : 
PublicIpAllocationMethod : Dynamic
IpAddress                : xx.xx.xxx.xx
PublicIpAddressVersion   : IPv4
IdleTimeoutInMinutes     : 4
IpConfiguration          : {
                                "Id": "/subscriptions/<subscription_id>/resourceGroups/appgw-RG/providers/Microsoft.Network/applicationGateways/appgwtest/frontendIP
                            Configurations/frontend1"
                            }
DnsSettings              : {
                                "Fqdn": "00000000-0000-xxxx-xxxx-xxxxxxxxxxxx.cloudapp.net"
                            }
```

## <a name="next-steps"></a>Дальнейшие действия

Чтобы узнать, как настроить ведение журнала диагностики для регистрации событий, обнаруженных или предотвращенных с помощью WAF, ознакомьтесь со статьей [Работоспособность серверной части, журналы диагностики и метрики для шлюза приложений](application-gateway-diagnostics.md).

[scenario]: ./media/application-gateway-web-application-firewall-powershell/scenario.png


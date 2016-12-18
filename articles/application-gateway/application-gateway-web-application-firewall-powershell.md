---
title: "Настройка брандмауэра веб-приложения на новом или существующем шлюзе приложений | Документация Майкрософт"
description: "Эта статья содержит рекомендации о том, как приступить к работе с брандмауэром веб-приложения на существующем или новом шлюзе приложений."
documentationcenter: na
services: application-gateway
author: georgewallace
manager: carmonm
editor: tysonn
ms.assetid: 670b9732-874b-43e6-843b-d2585c160982
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/16/2016
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: 486ed096bba7accfb3ca54aa97fb0b57b756d291
ms.openlocfilehash: ba7bff77a52c8e432b175d1db3d9dec66ec36a2b


---
# <a name="configure-web-application-firewall-on-a-new-or-existing-application-gateway"></a>Настройка брандмауэра веб-приложения на новом или существующем шлюзе приложений

> [!div class="op_single_selector"]
> * [Портал Azure](application-gateway-web-application-firewall-portal.md)
> * [PowerShell и диспетчер ресурсов Azure](application-gateway-web-application-firewall-powershell.md)
> 
> 

Брандмауэр веб-приложения (WAF) в шлюзе приложений Azure защищает веб-приложения от таких распространенных сетевых атак, как атаки путем внедрения кода SQL, атаки межсайтовых сценариев и захваты сеанса.

Шлюз приложений — это балансировщик нагрузки уровня 7. Он отвечает за отработку отказов и эффективную маршрутизацию HTTP-запросов между разными серверами (облачными и локальными). Шлюз приложений выполняет многие функции контроллера доставки приложений (ADC), включая балансировку нагрузки HTTP, определение сходства сеансов на основе файлов cookie, разгрузку SSL, выполнение пользовательской проверки работоспособности, поддержку нескольких сайтов и т. д. Полный список поддерживаемых функций представлен в обзоре шлюза приложений.

В следующей статье рассказывается, как [добавить брандмауэр веб-приложения в существующий шлюз приложений](#add-web-application-firewall-to-an-existing-application-gateway) и как [создать шлюз приложений с брандмауэром веб-приложения](#create-an-application-gateway-with-web-application-firewall).

![Изображение для сценария][scenario]

## <a name="waf-configuration-differences"></a>Различия в конфигурации WAF

Если вы прочитали раздел [Создание шлюза приложений с помощью PowerShell](application-gateway-create-gateway-arm.md), то уже понимаете, какие параметры SKU нужно настроить при создании шлюза приложений. При настройке SKU для шлюза приложений доступны дополнительные параметры, относящиеся к WAF. Какие-либо изменения самого шлюза приложений не требуются.

**SKU**. Обычный шлюз приложений без WAF поддерживает размеры **Standard\_Small**, **Standard\_Medium** и **Standard\_Large**. После добавления WAF становятся доступны еще два SKU, **WAF\_Medium** и **WAF\_Large**. WAF не поддерживается в шлюзах приложения уровня "Мелкий".

**Уровень**. Доступные значения: **Стандартный** или **WAF**. При использовании брандмауэра веб-приложения требуется выбрать уровень **WAF**.

**Режим**. Этот параметр определяет режим WAF. Допустимые значения: **Обнаружение** и **Предотвращение**. Если WAF работает в режиме обнаружения, все угрозы заносятся в файл журнала. В режиме предотвращения события по-прежнему регистрируются в журнале, но злоумышленник получает от шлюза приложений ответ "403 — не авторизовано".

## <a name="add-web-application-firewall-to-an-existing-application-gateway"></a>Добавление брандмауэра веб-приложения в существующий шлюз приложений

Убедитесь, что у вас установлена последняя версия Azure PowerShell. Дополнительные сведения см. в статье [Использование Windows PowerShell с диспетчером ресурсов](../powershell-azure-resource-manager.md).

### <a name="step-1"></a>Шаг 1

Войдите в свою учетную запись Azure.

```powershell
Login-AzureRmAccount
```

### <a name="step-2"></a>Шаг 2

Выберите подписку для этого сценария.

```powershell
Select-AzureRmSubscription -SubscriptionName "<Subscription name>"
```

### <a name="step-3"></a>Шаг 3.

Получите шлюз, в который следует добавить брандмауэр веб-приложения.

```powershell
$gw = Get-AzureRmApplicationGateway -Name "AdatumGateway" -ResourceGroupName "MyResourceGroup"
```

### <a name="step-4"></a>Шаг 4.

Настройте SKU брандмауэра веб-приложения. Доступные размеры: **WAF\_Large** и **WAF\_Medium**. Для использования брандмауэра веб-приложения требуется уровень **WAF**, а емкость необходимо подтвердить при определении SKU.

```powershell
$gw | Set-AzureRmApplicationGatewaySku -Name WAF_Large -Tier WAF -Capacity 2
```

### <a name="step-5"></a>Шаг 5

Настройте параметры WAF, как указано в следующем примере.

Для параметра **WafMode** можно указать значение "Обнаружение" или "Предотвращение".

```powershell
$gw | Set-AzureRmApplicationGatewayWebApplicationFirewallConfiguration -Enabled $true -FirewallMode Prevention
```

### <a name="step-6"></a>Шаг 6

Примените к шлюзу приложений параметры, определенные на предыдущем шаге.

```powershell
Set-AzureRmApplicationGateway -ApplicationGateway $gw
```

Эта команда обновляет шлюз приложений, добавляя брандмауэр веб-приложения. Рекомендуется ознакомиться с [диагностикой шлюза приложений](application-gateway-diagnostics.md) , чтобы понять, как просматривать журналы шлюза приложений. Из-за особенностей системы безопасности WAF необходимо регулярно просматривать журналы, чтобы понимать состояние безопасности веб-приложений.

## <a name="create-an-application-gateway-with-web-application-firewall"></a>Создание шлюза приложений с брандмауэром веб-приложения

Ниже от начала до конца описывается процесс создания шлюза приложений с брандмауэром веб-приложения.

Убедитесь, что у вас установлена последняя версия Azure PowerShell. Дополнительные сведения см. в статье [Использование Windows PowerShell с диспетчером ресурсов](../powershell-azure-resource-manager.md).

### <a name="step-1"></a>Шаг 1

Вход в Azure

```powershell
Login-AzureRmAccount
```

Вам будет предложено указать свои учетные данные для проверки подлинности.

### <a name="step-2"></a>Шаг 2

Просмотрите подписки учетной записи.

```powershell
Get-AzureRmSubscription
```

### <a name="step-3"></a>Шаг 3.

Выберите подписку Azure.

```powershell
Select-AzureRmsubscription -SubscriptionName "<Subscription name>"
```

### <a name="step-4"></a>Шаг 4.

Создайте группу ресурсов. Если вы используете существующую группу, пропустите этот шаг.

```powershell
New-AzureRmResourceGroup -Name appgw-rg -Location "West US"
```

В диспетчере ресурсов Azure для всех групп ресурсов должно быть указано расположение. Это расположение используется в качестве расположения по умолчанию для всех ресурсов данной группы. Убедитесь, что во всех командах для создания шлюза приложений используется одна группа ресурсов.

В приведенном выше примере мы создали группу ресурсов appgw-RG в расположении West US (западная часть США).

> [!NOTE]
> Если вам нужно настроить пользовательскую пробу для шлюза приложений, ознакомьтесь со статьей [Создание пользовательской проверки для шлюза приложений с помощью PowerShell для диспетчера ресурсов Azure](application-gateway-create-probe-ps.md). Дополнительные сведения см. в статье [Обзор мониторинга работоспособности шлюза приложений](application-gateway-probe-overview.md).
> 
> 

### <a name="step-5"></a>Шаг 5

Назначьте диапазон адресов подсети для самого шлюза приложений.

```powershell
$gwSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name 'appgwsubnet' -AddressPrefix 10.0.0.0/24
```

> [!NOTE]
> У подсети для приложения должно быть не менее 28 битов маски. Это значение позволяет оставить в подсети 10 доступных адресов для экземпляров шлюза приложений. Если подсеть будет меньше, то вы не сможете в будущем добавить экземпляры шлюза приложений.
> 
> 

### <a name="step-6"></a>Шаг 6

Назначьте диапазон адресов для внутреннего пула адресов.

```powershell
$nicSubnet = New-AzureRmVirtualNetworkSubnetConfig  -Name 'appsubnet' -AddressPrefix 10.0.2.0/24
```

### <a name="step-7"></a>Шаг 7

Создайте виртуальную сеть с указанными выше подсетями в группе ресурсов, созданной на шаге [Создание группы ресурсов](#create-the-resource-group)

```powershell
$vnet = New-AzureRmvirtualNetwork -Name 'appgwvnet' -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $gwSubnet, $nicSubnet
```

### <a name="step-8"></a>Шаг 8

Получите ресурс виртуальной сети и ресурсы подсетей, которые будут использоваться при выполнении следующих действий.

```powershell
$vnet = Get-AzureRmvirtualNetwork -Name 'appgwvnet' -ResourceGroupName appgw-rg
$gwSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name 'appgwsubnet' -VirtualNetwork $vnet
$nicSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name 'appsubnet' -VirtualNetwork $vnet
```

### <a name="step-9"></a>Шаг 9.

Создайте ресурс общедоступного IP-адреса для шлюза приложений. Этот общедоступный IP-адрес используется на одном из следующих шагов.

```powershell
$publicip = New-AzureRmPublicIpAddress -ResourceGroupName appgw-rg -name 'appgwpip' -Location "West US" -AllocationMethod Dynamic
```

> [!IMPORTANT]
> Шлюз приложений не поддерживает использование общедоступного IP-адреса, для которого метка домена была определена при создании. Поддерживается только общедоступный IP-адрес с динамически создаваемой меткой домена. Если для шлюза приложений требуется понятное DNS-имя, то рекомендуется использовать в качестве псевдонима запись CNAME.
> 
> 

### <a name="step-10"></a>Шаг 10

Перед созданием шлюза приложений необходимо настроить все элементы конфигурации. В ходе следующих шагов создаются необходимые элементы конфигурации для ресурса шлюза приложений.

Создайте конфигурацию IP шлюза приложений, в которой указано, какую подсеть использует шлюз приложений. При запуске шлюз приложений получает IP-адрес из настроенной подсети. Затем шлюз маршрутизирует сетевой трафик на IP-адреса из внутреннего пула IP-адресов. Помните, что для каждого экземпляра требуется отдельный IP-адрес.

```powershell
$gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name 'gwconfig' -Subnet $gwSubnet
```

### <a name="step-11"></a>Шаг 11

Настройте IP-адреса внутренних веб-серверов во внутреннем пуле IP-адресов. Эти IP-адреса будут использоваться для получения сетевого трафика от конечной точки с интерфейсным IP-адресом. Замените приведенные ниже IP-адреса и добавьте IP-адреса конечных точек своего приложения.

```powershell
$pool = New-AzureRmApplicationGatewayBackendAddressPool -Name 'pool01' -BackendIPAddresses 1.1.1.1, 2.2.2.2, 3.3.3.3
```

### <a name="step-12"></a>Шаг 12

Передайте используемый сертификат в ресурсы внутреннего пула с поддержкой протокола SSL.

```powershell
$authcert = New-AzureRmApplicationGatewayAuthenticationCertificate -Name 'whitelistcert1' -CertificateFile <full path to .cer file>
```

### <a name="step-13"></a>Шаг 13

Настройте параметры HTTP серверной части шлюза приложений. В параметрах HTTP укажите сертификат, переданный на предыдущем шаге.

```powershell
$poolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name 'setting01' -Port 443 -Protocol Https -CookieBasedAffinity Enabled -AuthenticationCertificates $authcert
```

### <a name="step-14"></a>Шаг 14

Настройте интерфейсный порт IP для конечной точки с общедоступным IP-адресом. Это порт, к которому подключаются пользователи.

```powershell
$fp = New-AzureRmApplicationGatewayFrontendPort -Name 'port01'  -Port 443
```

### <a name="step-15"></a>Шаг 15

Создайте конфигурацию IP внешнего интерфейса, в которой частный или общедоступный IP-адрес сопоставляется с внешним интерфейсом шлюза приложений. На следующем шаге общедоступный IP-адрес, указанный на предыдущем шаге, будет связан конфигурацией IP внешнего интерфейса.

```powershell
$fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig -Name 'fip01' -PublicIPAddress $publicip
```

### <a name="step-16"></a>Шаг 16

Настройте сертификат для шлюза приложений. Этот сертификат используется для шифрования и расшифровки трафика на шлюзе приложений.

```powershell
$cert = New-AzureRmApplicationGatewaySslCertificate -Name cert01 -CertificateFile <full path to .pfx file> -Password <password for certificate file>
```

### <a name="step-17"></a>Шаг 17

Создайте прослушиватель HTTP для шлюза приложений. Назначьте используемую конфигурацию IP внешнего интерфейса, порт и сертификат SSL.

```powershell
$listener = New-AzureRmApplicationGatewayHttpListener -Name listener01 -Protocol Https -FrontendIPConfiguration $fipconfig -FrontendPort $fp -SslCertificate $cert
```

### <a name="step-18"></a>Шаг 18

Создайте правило маршрутизации для балансировщика нагрузки, которое настраивает его поведение. В этом примере создается базовое правило с циклическим перебором.

```powershell
$rule = New-AzureRmApplicationGatewayRequestRoutingRule -Name 'rule01' -RuleType basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool
```

### <a name="step-19"></a>Шаг 19

Настройте размер экземпляра шлюза приложений.

```powershell
$sku = New-AzureRmApplicationGatewaySku -Name WAF_Medium -Tier WAF -Capacity 2
```

> [!NOTE]
> Можно выбрать размер **WAF\_Medium** или **WAF\_Large**. В случае использования WAF должен быть выбран уровень **WAF**. Емкость — любое число от 1 до 10.
> 
> 

### <a name="step-20"></a>Шаг 20

Настройте режим WAF, допустимые значения: **Предотвращение** и **Обнаружение**.

```powershell
$config = New-AzureRmApplicationGatewayWafConfig -Enabled $true -WafMode "Prevention"
```

### <a name="step-21"></a>Шаг 21

Создайте шлюз приложений со всеми элементами конфигурации, описанными выше. В этом примере шлюз приложений называется "appgwtest".

```powershell
$appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku -WebApplicationFirewallConfig $config -SslCertificates $cert -AuthenticationCertificates $authcert
```

## <a name="get-application-gateway-dns-name"></a>Получение DNS-имени шлюза приложений

После создания шлюза следует настроить внешний интерфейс для обмена данными. Если вы используете общедоступный IP-адрес, шлюзу приложений требуется динамически назначаемое непонятное имя DNS. Чтобы гарантировать попадание пользователей на шлюз приложений, можно использовать запись CNAME, чтобы указать общедоступную конечную точку шлюза приложения. [Настройка пользовательского имени домена в Azure](../cloud-services/cloud-services-custom-domain-name-portal.md). Получите информацию о шлюзе приложений и соответствующее IP- или DNS-имя с помощью элемента PublicIPAddress, связанного со шлюзом приложений. DNS-имя шлюза приложений должно использоваться для создания записи CNAME, указывающей двум веб-приложениям на это DNS-имя. Использование записи A не рекомендуется, так как виртуальный IP-адрес может измениться после перезапуска приложения шлюза.

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

Узнайте, как настроить ведение журнала диагностики и как регистрировать в журнале события, которые обнаружил или предотвратил брандмауэр веб-приложения, посетив страницу [диагностикой шлюза приложений](application-gateway-diagnostics.md)

[Сценарий]: ./media/application-gateway-web-application-firewall-powershell/scenario.png



<!--HONumber=Nov16_HO3-->



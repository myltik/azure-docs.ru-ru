---
title: "Создание шлюза приложений для размещения нескольких сайтов | Документация Майкрософт"
description: "Эта страница содержит инструкции по созданию и настройке шлюза приложений Azure для размещения нескольких веб-приложений в одном шлюзе."
documentationcenter: na
services: application-gateway
author: amsriva
manager: rossort
editor: amsriva
ms.assetid: b107d647-c9be-499f-8b55-809c4310c783
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/12/2016
ms.author: amsriva
ms.openlocfilehash: d42efa7d359f5c87c14afbfd138328b37c8ae6c2
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/11/2017
---
# <a name="create-an-application-gateway-for-hosting-multiple-web-applications"></a>Создание шлюза приложений для размещения нескольких веб-приложений

> [!div class="op_single_selector"]
> * [Портал Azure](application-gateway-create-multisite-portal.md)
> * [PowerShell и диспетчер ресурсов Azure](application-gateway-create-multisite-azureresourcemanager-powershell.md)

Размещение нескольких сайтов позволяет развернуть в одном шлюзе приложений не одно, а несколько веб-приложений. Чтобы определить, какой прослушиватель должен принимать трафик, шлюз приложений проверяет наличие заголовка узла во входящем HTTP-запросе. Затем прослушиватель направляет трафик в соответствующий внутренний пул согласно определениям правил шлюза. В веб-приложениях с поддержкой протокола SSL шлюз приложений использует расширение SNI (указание имени сервера) для выбора соответствующего прослушивателя веб-трафика. Как правило, размещение нескольких сайтов используется для балансировки нагрузки запросов к разным веб-доменам между различными внутренними пулами серверов. Подобным образом в одном шлюзе приложений можно разместить несколько поддоменов одного корневого домена.

## <a name="scenario"></a>Сценарий

В следующем примере шлюз приложений обслуживает трафик сайтов contoso.com и fabrikam.com с помощью двух внутренних пулов серверов: contoso и fabrikam. Аналогичную конфигурацию можно использовать для размещения таких поддоменов, как app.contoso.com и blog.contoso.com.

![imageURLroute](./media/application-gateway-create-multisite-azureresourcemanager-powershell/multisite.png)

## <a name="before-you-begin"></a>Перед началом работы

1. Установите последнюю версию командлетов Azure PowerShell, используя установщик веб-платформы. Последнюю версию можно загрузить и установить в разделе **Windows PowerShell** на [странице загрузок](https://azure.microsoft.com/downloads/).
2. Серверы, добавляемые во внутренний пул для использования шлюза приложений, должны находиться в отдельной подсети виртуальной сети, или их конечные точки должны находиться в этой подсети либо иметь назначенный общедоступный или виртуальный IP-адрес.

## <a name="requirements"></a>Требования

* **Внутренний пул серверов**. Список IP-адресов внутренних серверов. Указанные IP-адреса должны относиться к подсети виртуальной сети либо представлять собой общедоступные или виртуальные IP-адреса. Можно также использовать полное доменное имя.
* **Параметры внутреннего пула серверов**. Каждый пул имеет такие параметры, как порт, протокол и сходство на основе файлов cookie. Эти параметры привязываются к пулу и применяются ко всем серверам в этом пуле.
* **Интерфейсный порт**. Общедоступный порт, открытый в шлюзе приложений. Трафик поступает на этот порт, а затем перенаправляется на один из тыловых серверов.
* **Прослушиватель**. У прослушивателя есть интерфейсный порт, протокол (Http или Https — с учетом регистра) и имя SSL-сертификата (в случае настройки разгрузки SSL). Для шлюзов приложений с поддержкой нескольких сайтов также добавляются имя узла и индикаторы SNI.
* **Правило**. Правило связывает прослушиватель и внутренний пул серверов, а также определяет, в какой внутренний пул серверов следует направлять трафик, поступающий на определенный прослушиватель. Правила обрабатываются в том порядке, в котором они указаны, а трафик направляется через первое подходящее правило независимо от точности. Например, если для одного порта имеется правило с базовым прослушивателем и правило с многосайтовым прослушивателем, для обеспечения правильной работы правило с многосайтовым прослушивателем должно быть указано перед правилом с базовым прослушивателем.

## <a name="create-an-application-gateway"></a>Создание шлюза приложений

Ниже приведены пошаговые инструкции по созданию шлюза приложений.

1. Создание группы ресурсов для диспетчера ресурсов.
2. Создание виртуальной сети, подсетей и общедоступного IP-адреса для шлюза приложений.
3. Создание объекта конфигурации шлюза приложений.
4. Создание ресурса шлюза приложений.

## <a name="create-a-resource-group-for-resource-manager"></a>Создание группы ресурсов для диспетчера ресурсов

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
Select-AzureRmSubscription -Subscriptionid "GUID of subscription"
```

### <a name="step-4"></a>Шаг 4.

Создайте группу ресурсов. Если вы используете существующую группу, пропустите этот шаг.

```powershell
New-AzureRmResourceGroup -Name appgw-RG -location "West US"
```

В качестве альтернативы можно также создать теги группы ресурсов для шлюза приложений:

```powershell
$resourceGroup = New-AzureRmResourceGroup -Name appgw-RG -Location "West US" -Tags @{Name = "testtag"; Value = "Application Gateway multiple site"}
```

В диспетчере ресурсов Azure для всех групп ресурсов должно быть указано расположение. Это расположение используется в качестве расположения по умолчанию для всех ресурсов данной группы. Убедитесь, что во всех командах для создания шлюза приложений используется одна группа ресурсов.

В приведенном выше примере мы создали группу ресурсов **appgw-RG** в расположении **West US** (Западная часть США).

> [!NOTE]
> Если вам нужно настроить пользовательскую пробу для шлюза приложений, ознакомьтесь со статьей [Создание пользовательской проверки для шлюза приложений с помощью PowerShell для диспетчера ресурсов Azure](application-gateway-create-probe-ps.md). Дополнительные сведения см. в статье [Обзор мониторинга работоспособности шлюза приложений](application-gateway-probe-overview.md).

## <a name="create-a-virtual-network-and-subnets"></a>Создание виртуальной сети и подсетей

В следующем примере показано создание виртуальной сети с помощью диспетчера ресурсов. На этом шаге создаются две подсети. Первая подсеть предназначена для шлюза приложений. Шлюзу приложений требуется собственная подсеть для размещения его экземпляров. В этой подсети могут развертываться только другие шлюзы приложений. Вторая подсеть используется для размещения внутренних серверов приложений.

### <a name="step-1"></a>Шаг 1

Назначьте переменной subnet диапазон адресов 10.0.0.0/24, который будет использоваться для размещения шлюза приложений.

```powershell
$subnet = New-AzureRmVirtualNetworkSubnetConfig -Name appgatewaysubnet -AddressPrefix 10.0.0.0/24
```
### <a name="step-2"></a>Шаг 2

Назначьте переменной subnet2 диапазон адресов 10.0.1.0/24, который будет использоваться для внутренних пулов.

```powershell
$subnet2 = New-AzureRmVirtualNetworkSubnetConfig -Name backendsubnet -AddressPrefix 10.0.1.0/24
```

### <a name="step-3"></a>Шаг 3.

В группе ресурсов **appgw-rg** для региона "West US" (Западная часть США) создайте виртуальную сеть **appgwvnet** с префиксом 10.0.0.0/16 и подсетями 10.0.0.0/24 и 10.0.1.0/24.

```powershell
$vnet = New-AzureRmVirtualNetwork -Name appgwvnet -ResourceGroupName appgw-RG -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $subnet,$subnet2
```

### <a name="step-4"></a>Шаг 4.

Назначьте переменную подсети для дальнейших этапов создания шлюза приложений.

```powershell
$appgatewaysubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name appgatewaysubnet -VirtualNetwork $vnet
$backendsubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name backendsubnet -VirtualNetwork $vnet
```

## <a name="create-a-public-ip-address-for-the-front-end-configuration"></a>Создание общедоступного IP-адреса для конфигурации интерфейсной части

Создайте ресурс общедоступного IP-адреса с именем **publicIP01** в группе ресурсов **appgw-rg** для региона "Западная часть США".

```powershell
$publicip = New-AzureRmPublicIpAddress -ResourceGroupName appgw-RG -name publicIP01 -location "West US" -AllocationMethod Dynamic
```

IP-адрес назначается шлюзу приложений при запуске службы.

## <a name="create-application-gateway-configuration"></a>Создание конфигурации шлюза приложений

Перед созданием шлюза приложений необходимо настроить все элементы конфигурации. В ходе следующих шагов создаются необходимые элементы конфигурации для ресурса шлюза приложений.

### <a name="step-1"></a>Шаг 1

Создайте конфигурацию IP-адресов шлюза приложений с именем **gatewayIP01**. При запуске шлюз приложений получает IP-адрес из настроенной подсети, а после шлюз маршрутизирует сетевой трафик на IP-адреса из внутреннего пула IP-адресов. Помните, что для каждого экземпляра требуется отдельный IP-адрес.

```powershell
$gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $appgatewaysubnet
```

### <a name="step-2"></a>Шаг 2

Настройте для внутренних пулов IP-адресов **pool01** и **pool2** IP-адреса: **134.170.185.46**, **134.170.188.221**, **134.170.185.50** для **pool1** и **134.170.186.46**, **134.170.189.221**, **134.170.186.50** для **pool2**.

```powershell
$pool1 = New-AzureRmApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 10.0.1.100, 10.0.1.101, 10.0.1.102
$pool2 = New-AzureRmApplicationGatewayBackendAddressPool -Name pool02 -BackendIPAddresses 10.0.1.103, 10.0.1.104, 10.0.1.105
```

В этом примере используются два пула внутренних серверов для маршрутизации сетевого трафика на основе запрошенного сайта. Один пул принимает трафик от сайта contoso.com, а другой — от сайта fabrikam.com. Вам необходимо заменить приведенные выше IP-адреса и добавить IP-адреса конечных точек своего приложения. Вместо внутренних IP-адресов для серверных экземпляров можно также использовать общедоступные IP-адреса, полное доменное имя или сетевую карту виртуальной машины. Чтобы указать полные доменные имена, а не IP-адреса, используйте параметр -BackendFQDNs в PowerShell.

### <a name="step-3"></a>Шаг 3.

Настройте параметры шлюзов приложений **poolsetting01** и **poolsetting02** для балансировки нагрузки сетевого трафика во внутреннем пуле. В этом примере вы настроите различные параметры пулов тыловых серверов. Для каждого пула тыловых серверов параметры можно настроить отдельно.

```powershell
$poolSetting01 = New-AzureRmApplicationGatewayBackendHttpSettings -Name "besetting01" -Port 80 -Protocol Http -CookieBasedAffinity Disabled -RequestTimeout 120
$poolSetting02 = New-AzureRmApplicationGatewayBackendHttpSettings -Name "besetting02" -Port 80 -Protocol Http -CookieBasedAffinity Enabled -RequestTimeout 240
```

### <a name="step-4"></a>Шаг 4.

Настройте внешний IP-адрес, используя конечную точку с общедоступным IP-адресом.

```powershell
$fipconfig01 = New-AzureRmApplicationGatewayFrontendIPConfig -Name "frontend1" -PublicIPAddress $publicip
```

### <a name="step-5"></a>Шаг 5

Настройте внешний порт для шлюза приложений.

```powershell
$fp01 = New-AzureRmApplicationGatewayFrontendPort -Name "fep01" -Port 443
```

### <a name="step-6"></a>Шаг 6

Настройте два SSL-сертификата для обоих веб-сайтов, которые будут обслуживаться в этом примере. Один сертификат предназначен для трафика contoso.com, а другой — для трафика fabrikam.com. Это должны быть сертификаты, выданные веб-сайтам центром сертификации. Самозаверяющие сертификаты поддерживаются, но их не рекомендуется использовать трафика в рабочих средах.

```powershell
$cert01 = New-AzureRmApplicationGatewaySslCertificate -Name contosocert -CertificateFile <file path> -Password <password>
$cert02 = New-AzureRmApplicationGatewaySslCertificate -Name fabrikamcert -CertificateFile <file path> -Password <password>
```

### <a name="step-7"></a>Шаг 7

Настройте два прослушивателя для пары веб-сайтов в данном примере. На этом шаге следует настроить прослушиватели для общедоступного IP-адреса, порта и узла, используемых для получения входящего сетевого трафика. Параметр HostName необходим для поддержки нескольких сайтов. Ему нужно присвоить имя соответствующего веб-сайта, для которого будет приниматься трафик. Параметру RequireServerNameIndication следует присвоить значение true для веб-сайтов, которым требуется поддержка протокола SSL в сценарии с несколькими узлами. Если требуется поддержка протокола SSL, необходимо также указать SSL-сертификат, используемый для защиты трафика этого веб-приложения. Сочетание параметров прослушивателя FrontendIPConfiguration, FrontendPort и HostName должно быть уникальным. Каждый прослушиватель может поддерживать один сертификат.

```powershell
$listener01 = New-AzureRmApplicationGatewayHttpListener -Name "listener01" -Protocol Https -FrontendIPConfiguration $fipconfig01 -FrontendPort $fp01 -HostName "contoso11.com" -RequireServerNameIndication true  -SslCertificate $cert01
$listener02 = New-AzureRmApplicationGatewayHttpListener -Name "listener02" -Protocol Https -FrontendIPConfiguration $fipconfig01 -FrontendPort $fp01 -HostName "fabrikam11.com" -RequireServerNameIndication true -SslCertificate $cert02
```

### <a name="step-8"></a>Шаг 8

Создайте два параметра правила для пары веб-приложений в данном примере. Правило объединяет прослушиватели, серверные пулы и параметры протокола HTTP. На этом шаге следует настроить шлюз приложений для использования базового правила маршрутизации для каждого веб-сайта. Трафик, передаваемый на каждый веб-сайт, получает настроенный для него прослушиватель, после чего трафик направляется в заданный северный пул с использованием свойств, указанных в BackendHttpSettings.

```powershell
$rule01 = New-AzureRmApplicationGatewayRequestRoutingRule -Name "rule01" -RuleType Basic -HttpListener $listener01 -BackendHttpSettings $poolSetting01 -BackendAddressPool $pool1
$rule02 = New-AzureRmApplicationGatewayRequestRoutingRule -Name "rule02" -RuleType Basic -HttpListener $listener02 -BackendHttpSettings $poolSetting02 -BackendAddressPool $pool2
```

### <a name="step-9"></a>Шаг 9.

Настройте количество экземпляров и размер шлюза приложений.

```powershell
$sku = New-AzureRmApplicationGatewaySku -Name "Standard_Medium" -Tier Standard -Capacity 2
```

## <a name="create-application-gateway"></a>Создание шлюза приложений

Создайте шлюз приложений со всеми объектами конфигурации, описанными выше.

```powershell
$appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-RG -Location "West US" -BackendAddressPools $pool1,$pool2 -BackendHttpSettingsCollection $poolSetting01, $poolSetting02 -FrontendIpConfigurations $fipconfig01 -GatewayIpConfigurations $gipconfig -FrontendPorts $fp01 -HttpListeners $listener01, $listener02 -RequestRoutingRules $rule01, $rule02 -Sku $sku -SslCertificates $cert01, $cert02
```

> [!IMPORTANT]
> Подготовка шлюза приложений — это длительная операция, которая может занять некоторое время.
> 
> 

## <a name="get-application-gateway-dns-name"></a>Получение DNS-имени шлюза приложений

После создания шлюза следует настроить внешний интерфейс для обмена данными. Если вы используете общедоступный IP-адрес, шлюзу приложений требуется динамически назначаемое непонятное имя DNS. Чтобы гарантировать попадание пользователей на шлюз приложений, можно использовать запись CNAME, чтобы указать общедоступную конечную точку шлюза приложений. [Настройка пользовательского имени домена в Azure](../cloud-services/cloud-services-custom-domain-name-portal.md). Получите информацию о шлюзе приложений и соответствующее IP- или DNS-имя с помощью элемента PublicIPAddress, связанного со шлюзом приложений. DNS-имя шлюза приложений должно использоваться для создания записи CNAME, указывающей двум веб-приложениям на это DNS-имя. Использование записи A не рекомендуется, так как виртуальный IP-адрес может измениться после перезапуска приложения шлюза.

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

Узнайте, как защитить веб-сайты с помощью [брандмауэра веб-приложения шлюза приложений](application-gateway-webapplicationfirewall-overview.md).


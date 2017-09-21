---
title: "Создание шлюза приложений с помощью правил маршрутизации URL-адресов | Документация Майкрософт"
description: "На этой странице приводятся инструкции по созданию и настройке шлюза приложений Azure с помощью правил маршрутизации URL-адресов."
documentationcenter: na
services: application-gateway
author: davidmu1
manager: timlt
editor: tysonn
ms.assetid: d141cfbb-320a-4fc9-9125-10001c6fa4cf
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/03/2017
ms.author: davidmu
ms.translationtype: HT
ms.sourcegitcommit: d24c6777cc6922d5d0d9519e720962e1026b1096
ms.openlocfilehash: b1ed7d5693ff7e6730255462411d462694b730e1
ms.contentlocale: ru-ru
ms.lasthandoff: 09/14/2017

---
# <a name="create-an-application-gateway-by-using-path-based-routing"></a>Создание шлюза приложений с помощью маршрутизации на основе пути

> [!div class="op_single_selector"]
> * [Портал Azure](application-gateway-create-url-route-portal.md)
> * [PowerShell и диспетчер ресурсов Azure](application-gateway-create-url-route-arm-ps.md)
> * [Azure CLI 2.0](application-gateway-create-url-route-cli.md)

Маршрутизация на основе пути позволяет связывать маршруты на основе URL-пути HTTP-запроса. При такой маршрутизации проверяется, настроен ли для URL-адресов в шлюзе приложений маршрут к внутреннему пулу, после чего сетевой трафик передается в указанный внутренний пул. Как правило, маршрутизация на основе URL-адресов используется для распределения запросов содержимого разных типов между разными пулами тыловых серверов.

Шлюз приложений Azure использует два типа правил: правила простой маршрутизации и правила маршрутизации на основе пути. Простая маршрутизация предоставляет службу с циклическим перебором для внутренних пулов. При маршрутизации на основе пути, помимо циклического распределения, для выбора внутреннего пула также используется шаблон пути URL-адреса запроса.

## <a name="scenario"></a>Сценарий

В следующем примере шлюз приложений обслуживает трафик сайта contoso.com с помощью двух внутренних пулов: пула серверов обработки видео и пула серверов обработки изображений.

Запросы к http://contoso.com/image* направляются в пул серверов обработки изображений образов (**pool1**), а запросы к http://contoso.com/video* направляются в пул серверов обработки видео (**pool2**). Если ни один из шаблонов пути не подходит, выбирается пул серверов по умолчанию (**pool1**).

![Маршрут URL-адреса](./media/application-gateway-create-url-route-arm-ps/figure1.png)

## <a name="before-you-begin"></a>Перед началом работы

1. Установите последнюю версию командлетов Azure PowerShell, используя установщик веб-платформы. Скачать и установить последнюю версию вы можете в разделе **Windows PowerShell** на [странице загрузок](https://azure.microsoft.com/downloads/).
2. Создайте виртуальную сеть и подсеть для шлюза приложений. Убедитесь, что эта подсеть не используется виртуальными машинами или облачными развертываниями. Сам шлюз приложений должен находиться в подсети виртуальной сети.
3. Убедитесь, что серверы, добавляемые во внутренний пул для шлюза приложений, существуют, и что для них созданы конечные точки либо в виртуальной сети, либо с назначенными общедоступными или виртуальными IP-адресами.

## <a name="requirements-to-create-an-application-gateway"></a>Требования к созданию шлюза приложений

* **Внутренний пул серверов**. Список IP-адресов внутренних серверов. Указанные IP-адреса должны относиться к подсети виртуальной сети либо представлять собой общедоступные или виртуальные IP-адреса.
* **Параметры внутреннего пула серверов**. Порт, протокол и сходство на основе файлов cookie. Эти параметры привязываются к пулу и применяются ко всем серверам в нем.
* **Интерфейсный порт**. Общедоступный порт, открытый в шлюзе приложений. Трафик поступает на этот порт, а затем перенаправляется на один из внутренних серверов.
* **Прослушиватель**. У прослушивателя есть интерфейсный порт, протокол (Http или Https — с учетом регистра) и имя SSL-сертификата (в случае настройки разгрузки SSL).
* **Правило**. Правило связывает прослушиватель и внутренний пул серверов, а также определяет, в какой пул следует направлять трафик, поступающий на прослушиватель.

## <a name="create-an-application-gateway"></a>Создание шлюза приложений

Разница между использованием классического портала Azure и Azure Resource Manager заключается в порядке создания шлюза приложений и элементов, которые нужно настроить.

При использовании Resource Manager все элементы, которые будут включены в единый ресурс шлюза приложений, сначала настраиваются по отдельности.

Ниже приведен порядок действий при удалении шлюза приложений.

1. Создание группы ресурсов для диспетчера ресурсов.
2. Создание виртуальной сети, подсети и общедоступного IP-адреса для шлюза приложений.
3. Создание объекта конфигурации шлюза приложений.
4. Создание ресурса шлюза приложений.

## <a name="create-a-resource-group-for-resource-manager"></a>Создание группы ресурсов для диспетчера ресурсов

Убедитесь, что у вас установлена последняя версия Azure PowerShell. Дополнительные сведения см. в статье [Использование Windows PowerShell с диспетчером ресурсов](../powershell-azure-resource-manager.md).

### <a name="step-1"></a>Шаг 1

Войдите в Azure.

```powershell
Login-AzureRmAccount
```

Вам будет предложено указать свои учетные данные для проверки подлинности.<BR>

### <a name="step-2"></a>Шаг 2

Просмотрите подписки учетной записи.

```powershell
Get-AzureRmSubscription
```

### <a name="step-3"></a>Шаг 3.

Выберите подписку Azure. <BR>

```powershell
Select-AzureRmSubscription -Subscriptionid "GUID of subscription"
```

### <a name="step-4"></a>Шаг 4.

Создайте группу ресурсов. Если используется существующая группа ресурсов, пропустите этот шаг.

```powershell
$resourceGroup = New-AzureRmResourceGroup -Name appgw-RG -Location "West US"
```

В качестве альтернативы можно создать теги группы ресурсов для шлюза приложений.

```powershell
$resourceGroup = New-AzureRmResourceGroup -Name appgw-RG -Location "West US" -Tags @{Name = "testtag"; Value = "Application Gateway URL routing"} 
```

Azure Resource Manager требует, чтобы для группы ресурсов было указано расположение по умолчанию, которое используется для всех ресурсов в этой группе. Убедитесь, что во всех командах для создания шлюза приложений используется одна группа ресурсов.

В приведенном выше примере мы создали группу ресурсов appgw-RG в расположении "Западная часть США".

> [!NOTE]
> Если вам нужно настроить пользовательскую пробу для шлюза приложений, ознакомьтесь со статьей [Создание пользовательской проверки для шлюза приложений с помощью PowerShell для диспетчера ресурсов Azure](application-gateway-create-probe-ps.md). Дополнительные сведения см. в статье [Обзор мониторинга работоспособности шлюза приложений](application-gateway-probe-overview.md).
> 
> 

## <a name="create-a-virtual-network-and-a-subnet-for-the-application-gateway"></a>Создание виртуальной сети и подсети для шлюза приложений.

В следующем примере показано создание виртуальной сети с помощью диспетчера ресурсов. В этом примере создается виртуальная сеть для шлюза приложений. Шлюзу приложений требуется собственная подсеть. Поэтому подсеть, создаваемая для шлюза приложений, меньше, чем адресное пространство виртуальной сети. При этом в той же виртуальной сети можно настраивать и другие ресурсы, включая, помимо прочего, веб-серверы.

### <a name="step-1"></a>Шаг 1

Назначьте диапазон адресов 10.0.0.0/24 переменной подсети, которая будет использоваться для создания виртуальной сети.  Это действие создает объект конфигурации подсети для шлюза приложений, который используется в следующем примере.

```powershell
$subnet = New-AzureRmVirtualNetworkSubnetConfig -Name subnet01 -AddressPrefix 10.0.0.0/24
```

### <a name="step-2"></a>Шаг 2

Создайте виртуальную сеть **appgwvnet** в группе ресурсов **appgw-rg** для региона "Западная часть США", указав префикс 10.0.0.0/16 и подсеть 10.0.0.0/24. Это действие завершает настройку виртуальной сети с одной подсетью для шлюза приложений.

```powershell
$vnet = New-AzureRmVirtualNetwork -Name appgwvnet -ResourceGroupName appgw-RG -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $subnet
```

### <a name="step-3"></a>Шаг 3.

Назначьте переменную для подсети, которая будет использоваться далее. Эта переменная будет передана в командлет `New-AzureRMApplicationGateway` на предстоящем шаге.

```powershell
$subnet=$vnet.Subnets[0]
```

## <a name="create-a-public-ip-address-for-the-front-end-configuration"></a>Создание общедоступного IP-адреса для конфигурации интерфейсной части

Создайте ресурс общедоступного IP-адреса с именем **publicIP01** в группе ресурсов **appgw-rg** для региона "Западная часть США". Чтобы получать запросы для балансировки нагрузки, шлюз приложений может использовать общедоступный IP-адрес, внутренний IP-адрес или сразу оба.  В этом примере используется только общедоступный IP-адрес. В следующем примере для создаваемого общедоступного IP-адреса не настроено DNS-имя, так как шлюз приложений не поддерживает пользовательские DNS-имена для общедоступных IP-адресов.  Если для общедоступной конечной точки требуется пользовательское имя, создайте запись CNAME, указывающую на автоматически созданное DNS-имя для общедоступного IP-адреса.

```powershell
$publicip = New-AzureRmPublicIpAddress -ResourceGroupName appgw-RG -name publicIP01 -location "West US" -AllocationMethod Dynamic
```

IP-адрес назначается шлюзу приложений при запуске службы.

## <a name="create-the-application-gateway-configuration"></a>Создание конфигурации шлюза приложений

Перед созданием шлюза приложений необходимо настроить все элементы конфигурации. В ходе следующих шагов создаются необходимые элементы конфигурации для ресурса шлюза приложений.

### <a name="step-1"></a>Шаг 1

Создайте конфигурацию IP-адресов шлюза приложений с именем **gatewayIP01**. При запуске шлюз приложений получает IP-адрес из настроенной подсети. Затем шлюз маршрутизирует сетевой трафик на IP-адреса во внутреннем пуле IP-адресов. Помните, что для каждого экземпляра требуется отдельный IP-адрес.

```powershell
$gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $subnet
```

### <a name="step-2"></a>Шаг 2

Настройте для внутренних пулов IP-адресов **pool1** и **pool2** IP-адреса **pool1** и **pool2**. Это IP-адреса ресурсов, в которых размещается веб-приложение, которое будет защищено с помощью шлюза приложений. Работоспособность этих участников внутреннего пула проверяется с помощью базовых или пользовательских проб. Затем при поступлении запросов в шлюз приложений к ним перенаправляется трафик. Внутренние пулы могут использоваться в нескольких правилах в шлюзе приложений. Это означает, что один внутренний пул можно использовать для нескольких веб-приложений, которые находятся на одном узле.

```powershell
$pool1 = New-AzureRmApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 134.170.185.46, 134.170.188.221, 134.170.185.50

$pool2 = New-AzureRmApplicationGatewayBackendAddressPool -Name pool02 -BackendIPAddresses 134.170.186.47, 134.170.189.222, 134.170.186.51
```

В этом примере используются два внутренних пула для маршрутизации сетевого трафика на основе URL-пути. Один пул принимает трафик для URL-пути /video, а другой — для пути /image. Замените приведенные выше IP-адреса и добавьте IP-адреса конечных точек своего приложения. 

### <a name="step-3"></a>Шаг 3.

Настройте параметры шлюзов приложений **poolsetting01** и **poolsetting02** для балансировки нагрузки сетевого трафика во внутреннем пуле. В этом примере вы настроите различные параметры пулов тыловых серверов. Параметры каждого внутреннего пула можно настроить отдельно.  Внутренние параметры HTTP используются правилами для перенаправления трафика соответствующим участникам внутреннего пула. Они определяют протокол и порт, используемые для отправки трафика участникам внутреннего пула. Сеансы на основе файлов cookie также определяются с помощью внутренних параметров HTTP. Если эта функция включена, служба сходства сеансов на основе файлов cookie отправляет для каждого пакета трафик в ту же серверную часть, что и для предыдущих запросов.

```powershell
$poolSetting01 = New-AzureRmApplicationGatewayBackendHttpSettings -Name "besetting01" -Port 80 -Protocol Http -CookieBasedAffinity Disabled -RequestTimeout 120

$poolSetting02 = New-AzureRmApplicationGatewayBackendHttpSettings -Name "besetting02" -Port 80 -Protocol Http -CookieBasedAffinity Enabled -RequestTimeout 240
```

### <a name="step-4"></a>Шаг 4.

Настройте внешний IP-адрес, используя конечную точку с общедоступным IP-адресом. Объект конфигурации внешнего IP-адреса используется прослушивателем для сопоставления внешнего IP-адреса с прослушивателем.

```powershell
$fipconfig01 = New-AzureRmApplicationGatewayFrontendIPConfig -Name "frontend1" -PublicIPAddress $publicip
```

### <a name="step-5"></a>Шаг 5

Настройте внешний порт для шлюза приложений. Объект конфигурации интерфейсного порта используется прослушивателем для определения порта, на котором шлюз приложений ожидает передачи трафика в прослушиватель.

```powershell
$fp01 = New-AzureRmApplicationGatewayFrontendPort -Name "fep01" -Port 80
```

### <a name="step-6"></a>Шаг 6

Настройте прослушиватель для обслуживания общедоступного IP-адреса и порта, используемых для получения входящего сетевого трафика. В следующем примере используются ранее настроенная внешняя IP-конфигурация, конфигурация интерфейсных портов и протокол (Http или Https с учетом регистра) и настраивается прослушиватель. В этом примере прослушиватель прослушивает трафик HTTP через порт 80 для общедоступного IP-адреса, который был создан ранее.

```powershell
$listener = New-AzureRmApplicationGatewayHttpListener -Name "listener01" -Protocol Http -FrontendIPConfiguration $fipconfig01 -FrontendPort $fp01
```

### <a name="step-7"></a>Шаг 7

Настройте пути URL-правил для пулов тыловых серверов. На этом этапе настраивается относительный путь, который шлюз приложений использует для сопоставления URL-пути с внутренним пулом, который назначается для обработки входящего трафика.

> [!IMPORTANT]
> Каждый путь должен начинаться с "/", а звездочка допускается только в конце. Примеры допустимых значений: /xyz, /xyz* или /xyz/*. Строка, передаваемая для сопоставления пути, не должна содержать никакого текста после первого знака "?" или "#", и сами эти знаки не допускаются. 

В следующем примере создаются два правила: одно для пути маршрутизации трафика /image во внутренний пул **pool1**, а другое для пути маршрутизации трафика /video во внутренний пул **pool2**. Эти правила гарантируют, что трафик для каждого набора URL-адресов направляется в серверную часть. Например, файл http://contoso.com/image/figure1.jpg передается в **pool1**, а http://contoso.com/video/example.mp4 — в **pool2**.

```powershell
$imagePathRule = New-AzureRmApplicationGatewayPathRuleConfig -Name "pathrule1" -Paths "/image/*" -BackendAddressPool $pool1 -BackendHttpSettings $poolSetting01

$videoPathRule = New-AzureRmApplicationGatewayPathRuleConfig -Name "pathrule2" -Paths "/video/*" -BackendAddressPool $pool2 -BackendHttpSettings $poolSetting02
```

При настройке сопоставления для пути правил также настраивается пул серверной части по умолчанию, который будет использоваться, если путь не соответствует ни одному из предустановленных правил. Например, файл http://contoso.com/shoppingcart/test.html передается в **pool1**, так как этот пул определен как пул по умолчанию для несоответствующего трафика.

```powershell
$urlPathMap = New-AzureRmApplicationGatewayUrlPathMapConfig -Name "urlpathmap" -PathRules $videoPathRule, $imagePathRule -DefaultBackendAddressPool $pool1 -DefaultBackendHttpSettings $poolSetting02
```

### <a name="step-8"></a>Шаг 8

Создайте параметр правила. Этот шаг позволяет настроить шлюз приложений для маршрутизации на основе URL-путей. Определенная ранее переменная `$urlPathMap` теперь используется для создания правила на основе пути. На этом шаге мы связываем правило с прослушивателем и сопоставлением URL-пути, созданным ранее.

```powershell
$rule01 = New-AzureRmApplicationGatewayRequestRoutingRule -Name "rule1" -RuleType PathBasedRouting -HttpListener $listener -UrlPathMap $urlPathMap
```

### <a name="step-9"></a>Шаг 9.

Настройте количество экземпляров и размер шлюза приложений.

```powershell
$sku = New-AzureRmApplicationGatewaySku -Name "Standard_Small" -Tier Standard -Capacity 2
```

## <a name="create-an-application-gateway"></a>Создание шлюза приложений

Создайте шлюз приложений со всеми объектами конфигурации, описанными выше.

```powershell
$appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-RG -Location "West US" -BackendAddressPools $pool1,$pool2 -BackendHttpSettingsCollection $poolSetting01, $poolSetting02 -FrontendIpConfigurations $fipconfig01 -GatewayIpConfigurations $gipconfig -FrontendPorts $fp01 -HttpListeners $listener -UrlPathMaps $urlPathMap -RequestRoutingRules $rule01 -Sku $sku
```

## <a name="get-an-application-gateway-dns-name"></a>Получение DNS-имени шлюза приложений

После создания шлюза настраивается внешний интерфейс для обмена данными. Если вы используете общедоступный IP-адрес, шлюзу приложений требуется динамически назначаемое непонятное имя DNS. Чтобы клиенты могли попасть в шлюз приложений, можно использовать запись CNAME для указания общедоступной конечной точки шлюза приложений. Дополнительные сведения см. в статье [Настройка пользовательского доменного имени для облачной службы Azure](../cloud-services/cloud-services-custom-domain-name-portal.md).

Чтобы настроить запись CNAME интерфейсного IP-адреса, получите сведения о шлюзе приложений и соответствующий IP-адрес или DNS-имя с помощью элемента PublicIPAddress, связанного со шлюзом приложений. Используйте DNS-имя шлюза приложений для создания записи CNAME. Мы не рекомендуем использовать записи A, так как виртуальный IP-адрес может измениться после перезапуска шлюза приложений.

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

Если вы хотите узнать больше о разгрузке SSL, прочитайте статью [Настройка шлюза приложений для разгрузки SSL с помощью диспетчера ресурсов Azure](application-gateway-ssl-arm.md).



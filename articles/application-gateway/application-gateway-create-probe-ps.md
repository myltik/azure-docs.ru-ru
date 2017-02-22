---
title: "Создание пользовательской пробы для шлюза приложений Azure с помощью PowerShell | Документация Майкрософт"
description: "Узнайте, как создать пользовательскую проверку для шлюза приложений с помощью PowerShell в диспетчере ресурсов."
services: application-gateway
documentationcenter: na
author: georgewallace
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 68feb660-7fa4-4f69-a7e4-bdd7bdc474db
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/23/2017
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: fd5960a4488f2ecd93ba117a7d775e78272cbffd
ms.openlocfilehash: 794797d9c42ec7f2fc351bab109147e45ce06070


---
# <a name="create-a-custom-probe-for-azure-application-gateway-by-using-powershell-for-azure-resource-manager"></a>Создание пользовательской проверки для шлюза приложений с помощью PowerShell для диспетчера ресурсов Azure

> [!div class="op_single_selector"]
> * [Портал Azure](application-gateway-create-probe-portal.md)
> * [PowerShell и диспетчер ресурсов Azure](application-gateway-create-probe-ps.md)
> * [Классическая модель — Azure PowerShell](application-gateway-create-probe-classic-ps.md)

[!INCLUDE [azure-probe-intro-include](../../includes/application-gateway-create-probe-intro-include.md)]

> [!NOTE]
> В Azure предлагаются две модели развертывания для создания ресурсов и работы с ними: [модель Resource Manager и классическая модель](../azure-resource-manager/resource-manager-deployment-model.md).  В этой статье описывается использование модели развертывания c помощью Resource Manager. Для большинства новых развертываний мы рекомендуем использовать эту модель вместо [классической](application-gateway-create-probe-classic-ps.md).

[!INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

### <a name="step-1"></a>Шаг 1

Используйте `Login-AzureRmAccount` для аутентификации.

```powershell
Login-AzureRmAccount
```

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
New-AzureRmResourceGroup -Name appgw-rg -Location "West US"
```

В диспетчере ресурсов Azure для всех групп ресурсов должно быть указано расположение. Это расположение используется в качестве расположения по умолчанию для всех ресурсов данной группы. Убедитесь, что во всех командах для создания шлюза приложений используется одна группа ресурсов.

В приведенном выше примере мы создали группу ресурсов с именем **appgw-RG** и расположением **Западная часть США**.

## <a name="create-a-virtual-network-and-a-subnet-for-the-application-gateway"></a>Создание виртуальной сети и подсети для шлюза приложений.

С помощью следующих шагов вы создадите виртуальную сеть и подсеть для шлюза приложения.

### <a name="step-1"></a>Шаг 1

Назначьте диапазон адресов 10.0.0.0/24 переменной подсети, которая будет использоваться для создания виртуальной сети.

```powershell
$subnet = New-AzureRmVirtualNetworkSubnetConfig -Name subnet01 -AddressPrefix 10.0.0.0/24
```

### <a name="step-2"></a>Шаг 2

Создайте виртуальную сеть с именем **appgwvnet** в группе ресурсов **appgw-rg** для региона "Западная часть США" при помощи префикса 10.0.0.0/16 с подсетью 10.0.0.0/24.

```powershell
$vnet = New-AzureRmVirtualNetwork -Name appgwvnet -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $subnet
```

### <a name="step-3"></a>Шаг 3.

Назначьте переменную подсети для дальнейших этапов создания шлюза приложений.

```powershell
$subnet = $vnet.Subnets[0]
```

## <a name="create-a-public-ip-address-for-the-front-end-configuration"></a>Создание общедоступного IP-адреса для конфигурации интерфейсной части

Создайте ресурс общедоступного IP-адреса с именем **publicIP01** в группе ресурсов **appgw-rg** для региона "Западная часть США".

```powershell
$publicip = New-AzureRmPublicIpAddress -ResourceGroupName appgw-rg -Name publicIP01 -Location "West US" -AllocationMethod Dynamic
```

## <a name="create-an-application-gateway-configuration-object-with-a-custom-probe"></a>Создание объекта конфигурации шлюза приложений с пользовательской проверкой

Перед созданием шлюза приложений необходимо настроить все элементы конфигурации. В ходе следующих шагов создаются необходимые элементы конфигурации для ресурса шлюза приложений.

### <a name="step-1"></a>Шаг 1

Создайте конфигурацию IP-адресов шлюза приложений с именем **gatewayIP01**. При запуске шлюз приложений получает IP-адрес из настроенной подсети. Затем шлюз маршрутизирует сетевой трафик на IP-адреса из внутреннего пула IP-адресов. Помните, что для каждого экземпляра требуется отдельный IP-адрес.

```powershell
$gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $subnet
```

### <a name="step-2"></a>Шаг 2

Настройте для внутреннего пула IP-адресов **pool01** IP-адреса **134.170.185.46, 134.170.188.221 и 134.170.185.50**. Эти значения IP-адресов будут использоваться для получения сетевого трафика от конечной точки с интерфейсным IP-адресом. Вам нужно заменить приведенные выше IP-адреса и добавить конечные точки IP-адресов своего приложения.

```powershell
$pool = New-AzureRmApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 134.170.185.46, 134.170.188.221, 134.170.185.50
```

### <a name="step-3"></a>Шаг 3.

На этом шаге настраивается пользовательская проверка работоспособности.

Используемые параметры:

* **Interval** — задает интервал между пробами в секундах.
* **Timeout** — определяет время ожидания для проверки ответа HTTP.
* **Hostname и path** — полный путь URL-адреса, который вызывается шлюзом приложений для определения работоспособности экземпляра. Например, если у вас есть веб-сайт **http://contoso.com**, то пользовательскую пробу работоспособности для получения успешного ответа HTTP можно настроить в формате **http://contoso.com/path/custompath.htm**.
* **UnhealthyThreshold** — количество неудачных ответов HTTP, по достижении которого серверный экземпляр считается **неработоспособным**.

```powershell
$probe = New-AzureRmApplicationGatewayProbeConfig -Name probe01 -Protocol Http -HostName "contoso.com" -Path "/path/path.htm" -Interval 30 -Timeout 120 -UnhealthyThreshold 8
```

### <a name="step-4"></a>Шаг 4.

Настройте параметр шлюза приложений **poolsetting01** для трафика во внутреннем пуле. На этом шаге также настраивается время ожидания ответа пула внутренних серверов на запрос шлюза приложений. При достижении этого времени ожидания шлюз приложений отменяет запрос. Это значение отличается от времени ожидания проверки, которое относится только к ответу пула внутренних серверов на проверку работоспособности.

```powershell
$poolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name poolsetting01 -Port 80 -Protocol Http -CookieBasedAffinity Disabled -Probe $probe -RequestTimeout 80
```

### <a name="step-5"></a>Шаг 5

Настройте IP-порт интерфейсной части с именем **frontendport01** для конечной точки с общедоступным IP-адресом.

```powershell
$fp = New-AzureRmApplicationGatewayFrontendPort -Name frontendport01 -Port 80
```

### <a name="step-6"></a>Шаг 6

Создайте для интерфейсной части конфигурацию IP-адресов с именем **fipconfig01** и свяжите с ней общедоступный IP-адрес.

```powershell
$fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig -Name fipconfig01 -PublicIPAddress $publicip
```

### <a name="step-7"></a>Шаг 7

Создайте прослушиватель с именем **listener01** и свяжите интерфейсный порт с конфигурацией IP-адресов интерфейсной части.

```powershell
$listener = New-AzureRmApplicationGatewayHttpListener -Name listener01  -Protocol Http -FrontendIPConfiguration $fipconfig -FrontendPort $fp
```

### <a name="step-8"></a>Шаг 8

Создайте для балансировщика нагрузки правило маршрутизации **rule01**, которое будет определять его поведение.

```powershell
$rule = New-AzureRmApplicationGatewayRequestRoutingRule -Name rule01 -RuleType Basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool
```

### <a name="step-9"></a>Шаг 9.

Настройте размер экземпляра шлюза приложений.

```powershell
$sku = New-AzureRmApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2
```

> [!NOTE]
> Значение параметра **InstanceCount** по умолчанию — 2 (максимальное значение — 10). Значение **GatewaySize** (Размер шлюза) по умолчанию — Medium (Средний). Можно выбрать **Standard_Small**, **Standard_Medium** или **Standard_Large**. 

## <a name="create-an-application-gateway-by-using-new-azurermapplicationgateway"></a>Создание шлюза приложений с помощью командлета New-AzureRmApplicationGateway

Создайте шлюз приложений со всеми элементами конфигурации, описанными выше. В этом примере шлюз приложений имеет имя **appgwtest**.

```powershell
$appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Location "West US" -BackendAddressPools $pool -Probes $probe -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku
```

## <a name="add-a-probe-to-an-existing-application-gateway"></a>Добавление проверки для существующего шлюза приложений

Для добавления проверки для существующего шлюза приложений необходимо выполнить четыре действия.

### <a name="step-1"></a>Шаг 1

Загрузка ресурса шлюза приложений в переменную PowerShell с помощью `Get-AzureRmApplicationGateway`.

```powershell
$getgw =  Get-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg
```

### <a name="step-2"></a>Шаг 2

Добавление проверки к существующей конфигурации шлюза.

```powershell
$getgw = Add-AzureRmApplicationGatewayProbeConfig -ApplicationGateway $getgw -Name probe01 -Protocol Http -HostName "contoso.com" -Path "/path/custompath.htm" -Interval 30 -Timeout 120 -UnhealthyThreshold 8
```

В примере пользовательская проверка настроена для проверки URL-адреса contoso.com/path/custompath.htm каждые 30 секунд. Настроено пороговое значение времени ожидания в 120 секунд с максимальным количеством неудачных запросов 8.

### <a name="step-3"></a>Шаг 3.

Добавление пробы в конфигурацию внутреннего пула и времени ожидания с помощью `Set-AzureRmApplicationGatewayBackendHttpSettings`.

```powershell
    $getgw = Set-AzureRmApplicationGatewayBackendHttpSettings -ApplicationGateway $getgw -Name $getgw.BackendHttpSettingsCollection.name -Port 80 -Protocol Http -CookieBasedAffinity Disabled -Probe $probe -RequestTimeout 120
```

### <a name="step-4"></a>Шаг 4.

Сохранение конфигурации шлюза приложений с помощью `Set-AzureRmApplicationGateway`.

```powershell
Set-AzureRmApplicationGateway -ApplicationGateway $getgw
```

## <a name="remove-a-probe-from-an-existing-application-gateway"></a>Удаление проверки из существующего шлюза приложений

Ниже приведены инструкции по удалению пользовательской проверки из существующего шлюза приложений.

### <a name="step-1"></a>Шаг 1

Загрузка ресурса шлюза приложений в переменную PowerShell с помощью `Get-AzureRmApplicationGateway`.

```powershell
$getgw =  Get-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg
```

### <a name="step-2"></a>Шаг 2

Удаление конфигурации пробы из шлюза приложений с помощью `Remove-AzureRmApplicationGatewayProbeConfig`.

```powershell
$getgw = Remove-AzureRmApplicationGatewayProbeConfig -ApplicationGateway $getgw -Name $getgw.Probes.name
```

### <a name="step-3"></a>Шаг 3.

Обновление параметров внутреннего пула для удаления пробы и времени ожидания с помощью `Set-AzureRmApplicationGatewayBackendHttpSettings`.

```powershell
    $getgw = Set-AzureRmApplicationGatewayBackendHttpSettings -ApplicationGateway $getgw -Name $getgw.BackendHttpSettingsCollection.name -Port 80 -Protocol http -CookieBasedAffinity Disabled
```

### <a name="step-4"></a>Шаг 4.

Сохранение конфигурации шлюза приложений с помощью `Set-AzureRmApplicationGateway`. 

```powershell
Set-AzureRmApplicationGateway -ApplicationGateway $getgw
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

Сведения о настройке разгрузки SSL см. в статье [Настройка шлюза приложений для разгрузки SSL с помощью диспетчера ресурсов Azure](application-gateway-ssl-arm.md)




<!--HONumber=Jan17_HO4-->



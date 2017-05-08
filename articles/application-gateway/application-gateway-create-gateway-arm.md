---
title: "Создание и администрирование шлюза приложений Azure с помощью PowerShell | Документация Майкрософт"
description: "На этой странице приводятся инструкции по созданию, настройке, запуску и удалению шлюза приложений Azure с помощью диспетчера ресурсов Azure."
documentationcenter: na
services: application-gateway
author: georgewallace
manager: timlt
editor: tysonn
ms.assetid: 866e9b5f-0222-4b6a-a95f-77bc3d31d17b
ms.service: application-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/04/2017
ms.author: gwallace
ms.translationtype: Human Translation
ms.sourcegitcommit: 64bd7f356673b385581c8060b17cba721d0cf8e3
ms.openlocfilehash: 8a0eb841b1a41a14e443b6ce93e6b8fb8985a803
ms.contentlocale: ru-ru
ms.lasthandoff: 05/02/2017


---
# <a name="create-start-or-delete-an-application-gateway-by-using-azure-resource-manager"></a>Создание, запуск или удаление шлюза приложений с помощью диспетчера ресурсов Azure

> [!div class="op_single_selector"]
> * [портал Azure](application-gateway-create-gateway-portal.md)
> * [PowerShell и диспетчер ресурсов Azure](application-gateway-create-gateway-arm.md)
> * [Классическая модель — Azure PowerShell](application-gateway-create-gateway.md)
> * [Шаблон диспетчера ресурсов Azure](application-gateway-create-gateway-arm-template.md)
> * [Интерфейс командной строки Azure](application-gateway-create-gateway-cli.md)

Шлюз приложений — это балансировщик нагрузки уровня 7. Он отвечает за отработку отказов и эффективную маршрутизацию HTTP-запросов между разными серверами (облачными и локальными).
Шлюз приложений выполняет многие функции контроллера доставки приложений (ADC), включая балансировку нагрузки HTTP, определение сходства сеансов на основе файлов cookie, разгрузку SSL, выполнение пользовательской проверки работоспособности, поддержку нескольких сайтов и т. д.

Полный список поддерживаемых функций представлен в [обзоре шлюза приложений](application-gateway-introduction.md).

В этой статье рассказывается, как создавать, настраивать, запускать и удалять шлюз приложений.

> [!IMPORTANT]
> Прежде чем приступить к работе с ресурсами Azure, обратите внимание на то, что в настоящее время в Azure существует две модели развертывания: классическая модель развертывания и модель Resource Manager. Внимательно изучите [модели и средства развертывания](../azure-classic-rm.md), прежде чем начинать работать с любыми ресурсами Azure. Для просмотра документации о средствах развертывания выбирайте соответствующие вкладки в верхней части данной статьи. В этом документе рассматривается создание шлюза приложений с помощью Azure Resource Manager. Сведения об использовании классической модели развертывания см. в статье [Создание, запуск или удаление шлюза приложений](application-gateway-create-gateway.md).

## <a name="before-you-begin"></a>Перед началом работы

1. Установите последнюю версию командлетов Azure PowerShell, используя установщик веб-платформы. Вы можете скачать и установить последнюю версию из раздела **Windows PowerShell** на [странице загрузок](https://azure.microsoft.com/downloads/).
1. Если у вас есть виртуальная сеть, выберите в ней пустую подсеть или создайте другую подсеть для использования только шлюзом приложений. Шлюз приложений можно развернуть только в той виртуальной сети, где находятся ресурсы, которые нужно развернуть на базе шлюза приложений.
1. Для использования шлюза приложений настраиваются существующие серверы или серверы, для которых в виртуальной сети созданы конечные точки либо же назначен общедоступный или виртуальный IP-адрес.

## <a name="what-is-required-to-create-an-application-gateway"></a>Что необходимо для создания шлюза приложений?

* **Внутренний пул серверов.** Список IP-адресов, полных доменных имен или сетевых адаптеров внутренних серверов. Если используются IP-адреса, они должны относиться к подсети виртуальной сети либо представлять собой общедоступные или виртуальные IP-адреса.
* **Параметры внутреннего пула серверов**. Каждый пул имеет такие параметры, как порт, протокол и сходство на основе файлов cookie. Эти параметры привязываются к пулу и применяются ко всем серверам в этом пуле.
* **Интерфейсный порт**. Общедоступный порт, открытый в шлюзе приложений. Трафик поступает на этот порт, а затем перенаправляется на один из тыловых серверов.
* **Прослушиватель**. У прослушивателя есть интерфейсный порт, протокол (Http или Https — с учетом регистра) и имя SSL-сертификата (в случае настройки разгрузки SSL).
* **Правило**. Правило связывает прослушиватель и внутренний пул серверов, а также определяет, в какой внутренний пул серверов следует направлять трафик, поступающий на определенный прослушиватель.

## <a name="create-an-application-gateway"></a>Создание шлюза приложений

Разница между использованием классической модели развертывания Azure и модели Azure Resource Manager заключается в порядке создания шлюза приложения и элементов, которые нужно настроить.

При использовании Resource Manager все элементы, которые будут включены в единый ресурс шлюза приложений, сначала настраиваются по отдельности.

Ниже приведены пошаговые инструкции по созданию шлюза приложений.

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
New-AzureRmResourceGroup -Name appgw-rg -Location "West US"
```

В диспетчере ресурсов Azure для всех групп ресурсов должно быть указано расположение. Это расположение используется в качестве расположения по умолчанию для всех ресурсов данной группы. Убедитесь, что во всех командах для создания шлюза приложений используется одна группа ресурсов.

В приведенном выше примере мы создали группу ресурсов с именем **appgw-RG** и расположением **Западная часть США**.

> [!NOTE]
> Если вам нужно настроить пользовательскую пробу для шлюза приложений, ознакомьтесь со статьей [Создание пользовательской проверки для шлюза приложений с помощью PowerShell для Azure Resource Manager](application-gateway-create-probe-ps.md). Дополнительные сведения см. в статье [Обзор мониторинга работоспособности шлюза приложений](application-gateway-probe-overview.md).

## <a name="create-a-virtual-network-and-a-subnet"></a>Создание виртуальной сети и подсети

В следующем примере показано создание виртуальной сети с помощью диспетчера ресурсов. В этом примере создается виртуальная сеть для шлюза приложений. Шлюзу приложений требуется собственная подсеть, поэтому эта подсеть меньше, чем адресное пространство виртуальной сети. При использовании подсети меньшего размера можно настраивать другие ресурсы, включая, помимо прочего, веб-серверы, в той же виртуальной сети.

### <a name="step-1"></a>Шаг 1

Назначьте диапазон адресов 10.0.0.0/24 переменной подсети, которая будет использоваться для создания виртуальной сети. Это действие создает объект конфигурации подсети для шлюза приложений, который используется в следующем примере.

```powershell
$subnet = New-AzureRmVirtualNetworkSubnetConfig -Name subnet01 -AddressPrefix 10.0.0.0/24
```

### <a name="step-2"></a>Шаг 2

Создайте виртуальную сеть с именем **appgwvnet** в группе ресурсов **appgw-rg** для региона "Западная часть США" при помощи префикса 10.0.0.0/16 с подсетью 10.0.0.0/24. Это действие завершает настройку виртуальной сети с одной подсетью для шлюза приложений.

```powershell
$vnet = New-AzureRmVirtualNetwork -Name appgwvnet -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $subnet
```

### <a name="step-3"></a>Шаг 3.

Назначьте переменную для подсети, которая будет использоваться далее. Эта переменная будет передана в командлет `New-AzureRMApplicationGateway`.

```powershell
$subnet=$vnet.Subnets[0]
```

## <a name="create-a-public-ip-address"></a>Создание общедоступного IP-адреса

Создайте ресурс общедоступного IP-адреса с именем **publicIP01** в группе ресурсов **appgw-rg** для региона "Западная часть США". Чтобы получать запросы для балансировки нагрузки, шлюз приложений может использовать общедоступный IP-адрес, внутренний IP-адрес или сразу оба.  В этом примере используется общедоступный IP-адрес. В следующем примере для создания общего IP-адреса DNS-имя не настраивается.  Шлюз приложений не поддерживает пользовательские DNS-имена в общедоступных IP-адресах.  Если для общедоступной конечной точки требуется пользовательское имя, необходимо создать запись CNAME, указывающую на автоматически созданное DNS-имя для общедоступного IP-адреса.

```powershell
$publicip = New-AzureRmPublicIpAddress -ResourceGroupName appgw-rg -name publicIP01 -location "West US" -AllocationMethod Dynamic
```

> [!NOTE]
> IP-адрес назначается шлюзу приложений при запуске службы.

## <a name="create-the-application-gateway-configuration-objects"></a>Создание объекта конфигурации шлюза приложений

Перед созданием шлюза приложений необходимо настроить все элементы конфигурации. В ходе следующих шагов создаются необходимые элементы конфигурации для ресурса шлюза приложений.

### <a name="step-1"></a>Шаг 1

Создайте конфигурацию IP-адресов шлюза приложений с именем **gatewayIP01**. При запуске шлюз приложений получает IP-адрес из настроенной подсети. Затем шлюз маршрутизирует сетевой трафик на IP-адреса из внутреннего пула IP-адресов. Помните, что для каждого экземпляра требуется отдельный IP-адрес.

```powershell
$gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $subnet
```

### <a name="step-2"></a>Шаг 2

Настройте для внутреннего пула IP-адресов **pool01** IP-адреса для **pool1**. Эти IP-адреса являются IP-адресами ресурсов, где размещается веб-приложение, которое будет защищено с помощью шлюза приложений. Работоспособность этих участников серверного пула проверяется с помощью базовых или пользовательских проб.  Затем при поступлении запросов в шлюз приложений к ним перенаправляется трафик. Серверные пулы могут использоваться несколькими правилами в шлюзе приложений. Это означает, что один серверный пул может использоваться для нескольких веб-приложений, которые находятся на том же узле.

```powershell
$pool = New-AzureRmApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 134.170.185.46, 134.170.188.221, 134.170.185.50
```

В этом примере используются два пула тыловых серверов для маршрутизации сетевого трафика на основе URL-пути. Один пул принимает трафик для URL-пути /video, а другой — для пути /image. Замените приведенные выше IP-адреса и добавьте IP-адреса конечных точек своего приложения.

### <a name="step-3"></a>Шаг 3.

Настройте параметры шлюза приложений с именем **poolsetting01** для балансировки нагрузки сетевого трафика во внутреннем пуле. Для каждого пула тыловых серверов параметры можно настроить отдельно.  Серверные параметры HTTP используются правилами для перенаправления трафика соответствующим участникам серверного пула. Серверные параметры HTTP определяют протокол и порт, используемый для отправки трафика участникам серверного пула. Сеансы на основе файлов cookie также определяются с помощью серверных параметров HTTP.  Если этот параметр включен, соответствие сеансу на основе файлов cookie отправляет для каждого пакета трафик в ту же серверную часть, что и для предыдущих запросов.

```powershell
$poolSetting01 = New-AzureRmApplicationGatewayBackendHttpSettings -Name "besetting01" -Port 80 -Protocol Http -CookieBasedAffinity Disabled -RequestTimeout 120
```

### <a name="step-4"></a>Шаг 4.

Настройте внешний порт для шлюза приложений. Объект конфигурации интерфейсного порта используется прослушивателем для определения порта, на котором шлюз приложений прослушивает трафик, поступающий в прослушиватель.

```powershell
$fp = New-AzureRmApplicationGatewayFrontendPort -Name frontendport01  -Port 80
```

### <a name="step-5"></a>Шаг 5

Настройте внешний IP-адрес, используя конечную точку с общедоступным IP-адресом. Объект конфигурации внешнего IP-адреса используется прослушивателем для соотношения внешнего IP-адреса с прослушивателем.

```powershell
$fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig -Name fipconfig01 -PublicIPAddress $publicip
```

### <a name="step-6"></a>Шаг 6

Создайте прослушиватель. Этот шаг позволяет настроить прослушиватель для общедоступного IP-адреса и порта, используемых для получения входящего сетевого трафика. В следующем примере берется ранее настроенная конфигурация внешнего IP-адреса, конфигурация интерфейсного порта и протокол (HTTP или HTTPS) и настраивается прослушиватель. В этом примере прослушиватель прослушивает трафик HTTP через порт 80 для общедоступного IP-адреса, который был создан ранее.

```powershell
$listener = New-AzureRmApplicationGatewayHttpListener -Name listener01 -Protocol Http -FrontendIPConfiguration $fipconfig -FrontendPort $fp
```

### <a name="step-7"></a>Шаг 7

Создайте для балансировщика нагрузки правило маршрутизации **rule01**, которое будет определять его поведение. Параметры серверного пула, прослушиватель и серверный пул, созданные ранее, составляют правило. Трафик, определенный на основе критериев, перенаправляется в соответствующую серверную часть.

```powershell
$rule = New-AzureRmApplicationGatewayRequestRoutingRule -Name rule01 -RuleType Basic -BackendHttpSettings $poolSetting01 -HttpListener $listener -BackendAddressPool $pool
```

### <a name="step-8"></a>Шаг 8

Настройте количество экземпляров и размер шлюза приложений.

```powershell
$sku = New-AzureRmApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2
```

> [!NOTE]
> Значение параметра **InstanceCount** по умолчанию — 2 (максимальное значение — 10). Значение **GatewaySize** (Размер шлюза) по умолчанию — Medium (Средний). Можно выбрать **Standard_Small**, **Standard_Medium** или **Standard_Large**.

## <a name="create-the-application-gateway"></a>Создание шлюза приложений

Создайте шлюз приложений со всеми элементами конфигурации, описанными выше. В этом примере шлюз приложений имеет имя **appgwtest**.

```powershell
$appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku
```

Извлеките сведения о виртуальном IP-адресе и сервере DNS шлюза приложений из ресурса с общедоступным IP-адресом, присоединенного к этому шлюзу.

```powershell
Get-AzureRmPublicIpAddress -Name publicIP01 -ResourceGroupName appgw-rg  
```

## <a name="delete-the-application-gateway"></a>Удаление шлюза приложений

Удалить шлюз приложений можно так.

### <a name="step-1"></a>Шаг 1

Получите объект шлюза приложений и свяжите его с переменной `$getgw`.

```powershell
$getgw = Get-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg
```

### <a name="step-2"></a>Шаг 2

С помощью командлета `Stop-AzureRmApplicationGateway` остановите шлюз приложений.

```powershell
Stop-AzureRmApplicationGateway -ApplicationGateway $getgw
```

Когда шлюз будет остановлен, удалите службу с помощью командлета `Remove-AzureRmApplicationGateway`.

```powershell
Remove-AzureRmApplicationGateway -Name $appgwtest -ResourceGroupName appgw-rg -Force
```

> [!NOTE]
> Если указать параметр **-force** , запрос на подтверждение удаления не появится.

Для проверки того, удалена ли служба, используйте командлет `Get-AzureRmApplicationGateway`. Этот шаг не является обязательным.

```powershell
Get-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg
```

## <a name="get-application-gateway-dns-name"></a>Получение DNS-имени шлюза приложений

После создания шлюза следует настроить внешний интерфейс для обмена данными. Если вы используете общедоступный IP-адрес, шлюзу приложений требуется динамически назначаемое непонятное имя DNS. Чтобы гарантировать попадание пользователей на шлюз приложений, можно использовать запись CNAME, чтобы указать общедоступную конечную точку шлюза приложений. [Настройка пользовательского имени домена в Azure](../cloud-services/cloud-services-custom-domain-name-portal.md). Чтобы найти динамически созданное DNS-имя, получите сведения о шлюзе приложений и соответствующее IP- или DNS-имя с помощью элемента PublicIPAddress, связанного со шлюзом приложений. DNS-имя шлюза приложений должно использоваться для создания записи CNAME, указывающей двум веб-приложениям на это DNS-имя. Использование записи A не рекомендуется, так как виртуальный IP-адрес может измениться после перезапуска приложения шлюза.

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

## <a name="delete-all-resources"></a>Удаление всех ресурсов

Чтобы удалить все ресурсы, созданные в этой статье, выполните следующие действия:

```powershell
Remove-AzureRmResourceGroup -Name appgw-RG
```

## <a name="next-steps"></a>Дальнейшие действия

Чтобы настроить разгрузку SSL, ознакомьтесь с [настройкой шлюза приложений для разгрузки SSL](application-gateway-ssl.md).

Указания по настройке шлюза приложений для использования с внутренним балансировщиком нагрузки см. в статье [Создание шлюза приложений с внутренней подсистемой балансировщика нагрузки (ILB)](application-gateway-ilb.md).

Дополнительные сведения о параметрах балансировки нагрузки в целом см. в статьях:

* [Подсистема балансировщика нагрузки Azure](https://azure.microsoft.com/documentation/services/load-balancer/)
* [Azure Traffic Manager](https://azure.microsoft.com/documentation/services/traffic-manager/)



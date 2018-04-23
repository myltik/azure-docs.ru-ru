---
title: Использование шлюза приложения Azure с внутренней подсистемой балансировки нагрузки с помощью PowerShell | Документация Майкрософт
description: На этой странице приводятся инструкции по созданию, настройке, запуску и удалению шлюза приложений Azure с внутренним балансировщиком нагрузки (ILB) с помощью диспетчера ресурсов Azure.
documentationcenter: na
services: application-gateway
author: davidmu1
manager: timlt
editor: tysonn
ms.assetid: 75cfd5a2-e378-4365-99ee-a2b2abda2e0d
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/23/2017
ms.author: davidmu
ms.openlocfilehash: dd6455622a7fecd99c23aef1b181035ffe6061dd
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/19/2018
---
# <a name="create-an-application-gateway-with-an-internal-load-balancer-ilb-by-using-azure-resource-manager"></a>Создание шлюза приложений с внутренним балансировщиком нагрузки (ILB) с помощью диспетчера ресурсов Azure

> [!div class="op_single_selector"]
> * [Классическая модель — Azure PowerShell](application-gateway-ilb.md)
> * [PowerShell и диспетчер ресурсов Azure](application-gateway-ilb-arm.md)

Шлюз приложений Azure можно настроить на использование виртуального IP-адреса, доступного в Интернете, или внутренней конечной точки, недоступной в Интернете. Эта точка также называется конечной точкой внутреннего балансировщика нагрузки (ILB). Настройка шлюза с ILB подходит для внутренних бизнес-приложений, недоступных в Интернете. Кроме того, этот вариант конфигурации можно использовать для служб и уровней многоуровневого приложения, размещенного в периметре безопасности без доступа к Интернету, но требующего циклического перебора нагрузки, закрепления сеансов или завершения SSL-запросов.

В этой статье приводятся пошаговые инструкции по настройке шлюза приложений с использованием ILB.

## <a name="before-you-begin"></a>Перед началом работы

1. Установите последнюю версию командлетов Azure PowerShell, используя установщик веб-платформы. Последнюю версию можно загрузить и установить в разделе **Windows PowerShell** на [странице загрузок](https://azure.microsoft.com/downloads/).
2. Вы создадите виртуальную сеть и подсеть для шлюза приложений. Убедитесь, что подсеть не используется виртуальной машиной или облачным развертыванием. Сам шлюз приложений должен находиться в подсети виртуальной сети.
3. Для использования шлюза приложений настраиваются существующие серверы или серверы, для которых в виртуальной сети созданы конечные точки либо же назначен общедоступный или виртуальный IP-адрес.

## <a name="what-is-required-to-create-an-application-gateway"></a>Что необходимо для создания шлюза приложений?

* **Внутренний пул серверов**. Список IP-адресов внутренних серверов. Указанные IP-адреса должны относиться к одной виртуальной сети (но не входить в подсеть шлюза приложений) либо представлять собой общедоступные или виртуальные IP-адреса.
* **Параметры внутреннего пула серверов**. Каждый пул имеет такие параметры, как порт, протокол и сходство на основе файлов cookie. Эти параметры привязываются к пулу и применяются ко всем серверам в этом пуле.
* **Интерфейсный порт**. Общедоступный порт, открытый в шлюзе приложений. Трафик поступает на этот порт, а затем перенаправляется на один из тыловых серверов.
* **Прослушиватель**. У прослушивателя есть интерфейсный порт, протокол (Http или Https — с учетом регистра) и имя SSL-сертификата (в случае настройки разгрузки SSL).
* **Правило**. Правило связывает прослушиватель и внутренний пул серверов, а также определяет, в какой внутренний пул серверов следует направлять трафик, поступающий на определенный прослушиватель. В настоящее время поддерживается только *основное* правило. *Основное* правило предусматривает циклическое распределение нагрузки.

## <a name="create-an-application-gateway"></a>Создание шлюза приложений

Разница между использованием классической модели развертывания Azure и модели Azure Resource Manager заключается в порядке создания шлюза приложения и элементов, которые нужно настроить.
При использовании Resource Manager все элементы, которые будут включены в единый ресурс шлюза приложений, сначала настраиваются по отдельности.

Ниже приведены пошаговые инструкции по созданию шлюза приложений.

1. Создание группы ресурсов для диспетчера ресурсов.
2. Создание виртуальной сети и подсети для шлюза приложений.
3. Создание объекта конфигурации шлюза приложений.
4. Создание ресурса шлюза приложений.

## <a name="create-a-resource-group-for-resource-manager"></a>Создание группы ресурсов для диспетчера ресурсов.

Для работы с командлетами диспетчера ресурсов Azure необходимо перейти в режим PowerShell. Дополнительные сведения см. в статье [Использование Windows PowerShell с диспетчером ресурсов](../powershell-azure-resource-manager.md).

### <a name="step-1"></a>Шаг 1

```powershell
Connect-AzureRmAccount
```

### <a name="step-2"></a>Шаг 2

Просмотрите подписки учетной записи.

```powershell
Get-AzureRmSubscription
```

Вам будет предложено указать свои учетные данные для аутентификации.

### <a name="step-3"></a>Шаг 3.

Выберите, какие подписки Azure будут использоваться.

```powershell
Select-AzureRmSubscription -Subscriptionid "GUID of subscription"
```

### <a name="step-4"></a>Шаг 4.

Создайте группу ресурсов (если вы используете существующую группу, пропустите этот шаг).

```powershell
New-AzureRmResourceGroup -Name appgw-rg -location "West US"
```

Диспетчер ресурсов Azure требует, чтобы все группы ресурсов указывали расположение. Оно используется в качестве расположения по умолчанию для всех ресурсов данной группы. Убедитесь, что во всех командах для создания шлюза приложений используется одна группа ресурсов.

В приведенном выше примере мы создали группу ресурсов appgw-rg в расположении West US (западная часть США).

## <a name="create-a-virtual-network-and-a-subnet-for-the-application-gateway"></a>Создание виртуальной сети и подсети для шлюза приложений.

В следующем примере показано создание виртуальной сети с помощью диспетчера ресурсов.

### <a name="step-1"></a>Шаг 1

```powershell
$subnetconfig = New-AzureRmVirtualNetworkSubnetConfig -Name subnet01 -AddressPrefix 10.0.0.0/24
```

На этом шаге выполняется назначение диапазона адресов 10.0.0.0/24 переменной подсети, которая будет использоваться для создания виртуальной сети.

### <a name="step-2"></a>Шаг 2

```powershell
$vnet = New-AzureRmVirtualNetwork -Name appgwvnet -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $subnetconfig
```

На этом шаге создается виртуальная сеть appgwvnet в группе ресурсов appgw-rg для региона West US с помощью префикса 10.0.0.0/16 с подсетью 10.0.0.0/24.

### <a name="step-3"></a>Шаг 3.

```powershell
$subnet = $vnet.subnets[0]
```

На этом шаге выполняется присвоение объекта подсети переменной $subnet для выполнения следующих действий.

## <a name="create-an-application-gateway-configuration-object"></a>Создание объекта конфигурации шлюза приложений.

### <a name="step-1"></a>Шаг 1

```powershell
$gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $subnet
```

На этом шаге создается конфигурация IP-адреса шлюза приложений с именем gatewayIP01. При запуске шлюз приложений получает IP-адрес из настроенной подсети. Затем шлюз маршрутизирует сетевой трафик на IP-адреса из пула внутренних IP-адресов. Помните, что для каждого экземпляра требуется отдельный IP-адрес.

### <a name="step-2"></a>Шаг 2

```powershell
$pool = New-AzureRmApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 10.1.1.8,10.1.1.9,10.1.1.10
```

В этом шаге выполняется настройка внутреннего пула IP-адресов pool01 с IP-адресами 10.1.1.8, 10.1.1.9, 10.1.1.10. Эти адреса будут использоваться для получения сетевого трафика от конечной точки с интерфейсным IP-адресом. Замените приведенные выше IP-адреса и добавьте IP-адреса конечных точек своего приложения.

### <a name="step-3"></a>Шаг 3.

```powershell
$poolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name poolsetting01 -Port 80 -Protocol Http -CookieBasedAffinity Disabled
```

На этом шаге осуществляется настройка параметров шлюза приложений poolsetting01 для балансировки нагрузки сетевого трафика в пуле серверной части.

### <a name="step-4"></a>Шаг 4.

```powershell
$fp = New-AzureRmApplicationGatewayFrontendPort -Name frontendport01  -Port 80
```

На этом шаге выполняется настройка внешнего IP-порта с именем frontendport01 для внутренней подсистемы балансировки нагрузки (ILB).

### <a name="step-5"></a>Шаг 5

```powershell
$fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig -Name fipconfig01 -Subnet $subnet
```

На этом шаге выполняется создание конфигурации внешнего IP-адреса с именем fipconfig01 и ее связывание с частным IP-адресом из текущей подсети виртуальной сети.

### <a name="step-6"></a>Шаг 6

```powershell
$listener = New-AzureRmApplicationGatewayHttpListener -Name listener01  -Protocol Http -FrontendIPConfiguration $fipconfig -FrontendPort $fp
```

На этом шаге выполняется создание прослушивателя listener01 и связывание внешнего порта с конфигурацией внешнего IP-адреса.

### <a name="step-7"></a>Шаг 7

```powershell
$rule = New-AzureRmApplicationGatewayRequestRoutingRule -Name rule01 -RuleType Basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool
```

На этом шаге выполняется создание правила маршрутизации rule01 для настройки поведения подсистемы балансировки нагрузки.

### <a name="step-8"></a>Шаг 8

```powershell
$sku = New-AzureRmApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2
```

На этом шаге выполняется настройка размера экземпляра шлюза приложений.

> [!NOTE]
> Значение параметра *InstanceCount* по умолчанию — 2 (максимальное значение — 10). Значение *GatewaySize* (Размер шлюза) по умолчанию — Medium (Средний). Можно выбрать Standard_Small, Standard_Medium или Standard_Large.

## <a name="create-an-application-gateway-by-using-new-azureapplicationgateway"></a>Создание шлюза приложений с помощью командлета New-AzureApplicationGateway

Создайте шлюз приложений со всеми элементами конфигурации, описанными выше. В этом примере шлюз приложений называется "appgwtest".

```powershell
$appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku
```

На этом шаге создается шлюз приложений со всеми элементами конфигурации, описанными выше. В этом примере шлюз приложений называется appgwtest.

## <a name="delete-an-application-gateway"></a>Удаление шлюза приложений

Чтобы удалить шлюз приложений, по порядку выполните указанные ниже действия.

1. Остановите шлюз с помощью командлета `Stop-AzureRmApplicationGateway`.
2. Удалите шлюз с помощью командлета `Remove-AzureRmApplicationGateway`.
3. С помощью командлета `Get-AzureApplicationGateway` проверьте, удален ли шлюз.

### <a name="step-1"></a>Шаг 1

Получите объект шлюза приложений и свяжите его с переменной $getgw.

```powershell
$getgw =  Get-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg
```

### <a name="step-2"></a>Шаг 2

С помощью командлета `Stop-AzureRmApplicationGateway` остановите шлюз приложений. В данном примере командлет `Stop-AzureRmApplicationGateway` показан в первой строке, а за ним следуют выходные данные.

```powershell
Stop-AzureRmApplicationGateway -ApplicationGateway $getgw  
```

```
VERBOSE: 9:49:34 PM - Begin Operation: Stop-AzureApplicationGateway
VERBOSE: 10:10:06 PM - Completed Operation: Stop-AzureApplicationGateway
Name       HTTP Status Code     Operation ID                             Error
----       ----------------     ------------                             ----
Successful OK                   ce6c6c95-77b4-2118-9d65-e29defadffb8
```

Когда шлюз будет остановлен, удалите службу с помощью командлета `Remove-AzureRmApplicationGateway`.

```powershell
Remove-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Force
```

```
VERBOSE: 10:49:34 PM - Begin Operation: Remove-AzureApplicationGateway
VERBOSE: 10:50:36 PM - Completed Operation: Remove-AzureApplicationGateway
Name       HTTP Status Code     Operation ID                             Error
----       ----------------     ------------                             ----
Successful OK                   055f3a96-8681-2094-a304-8d9a11ad8301
```

> [!NOTE]
> Если указать параметр **-force** , запрос на подтверждение удаления не появится.

Для проверки того, удалена ли служба, используйте командлет `Get-AzureRmApplicationGateway`. Этот шаг не является обязательным.

```powershell
Get-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg
```

```
VERBOSE: 10:52:46 PM - Begin Operation: Get-AzureApplicationGateway

Get-AzureApplicationGateway : ResourceNotFound: The gateway does not exist.
```

## <a name="next-steps"></a>Дополнительная информация

Чтобы настроить разгрузку SSL, ознакомьтесь с [настройкой шлюза приложений для разгрузки SSL](application-gateway-ssl.md).

Указания по настройке шлюза приложений для использования с внутренним балансировщиком нагрузки см. в статье [Создание шлюза приложений с внутренней подсистемой балансировки нагрузки (ILB)](application-gateway-ilb.md).

Дополнительные сведения о параметрах балансировки нагрузки в целом см. в статьях:

* [Подсистема балансировщика нагрузки Azure](https://azure.microsoft.com/documentation/services/load-balancer/)
* [Azure Traffic Manager](https://azure.microsoft.com/documentation/services/traffic-manager/)


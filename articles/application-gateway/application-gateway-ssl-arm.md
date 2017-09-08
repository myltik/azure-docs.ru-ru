---
title: "Настройка разгрузки SSL для шлюза приложений Azure с помощью PowerShell | Документация Майкрософт"
description: "В этой статье приводятся инструкции по созданию шлюза приложений с разгрузкой SSL с помощью Azure Resource Manager."
documentationcenter: na
services: application-gateway
author: georgewallace
manager: timlt
editor: tysonn
ms.assetid: 3c3681e0-f928-4682-9d97-567f8e278e13
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/19/2017
ms.author: gwallace
ms.translationtype: HT
ms.sourcegitcommit: 1c730c65194e169121e3ad1d1423963ee3ced8da
ms.openlocfilehash: 4bcd002a805f1584323f5baf75f791f2fb64201f
ms.contentlocale: ru-ru
ms.lasthandoff: 08/30/2017

---
# <a name="configure-an-application-gateway-for-ssl-offload-by-using-azure-resource-manager"></a>Настройка шлюза приложений для разгрузки SSL с помощью диспетчера ресурсов Azure

> [!div class="op_single_selector"]
> * [портал Azure](application-gateway-ssl-portal.md)
> * [PowerShell и диспетчер ресурсов Azure](application-gateway-ssl-arm.md)
> * [Классическая модель — Azure PowerShell](application-gateway-ssl.md)
> * [Azure CLI 2.0](application-gateway-ssl-cli.md)

Шлюз приложений Azure можно настроить на завершение сеанса SSL в шлюзе, что позволит избежать выполнения дорогостоящей задачи SSL-шифрования на веб-ферме. Кроме того, разгрузка SSL упрощает процесс установки внешнего сервера и управления веб-приложением.

## <a name="before-you-begin"></a>Перед началом работы

1. Установите последнюю версию командлетов Azure PowerShell, используя установщик веб-платформы. Скачать и установить последнюю версию вы можете в разделе **Windows PowerShell** на [странице загрузок](https://azure.microsoft.com/downloads/).
2. Создайте виртуальную сеть и подсеть для шлюза приложений. Убедитесь, что подсеть не используется виртуальной машиной или облачным развертыванием. Сам шлюз приложений должен находиться в подсети виртуальной сети.
3. Для использования шлюза приложений настраиваются имеющиеся серверы или серверы, для которых в виртуальной сети созданы конечные точки либо же назначен общедоступный или виртуальный IP-адрес.

## <a name="what-is-required-to-create-an-application-gateway"></a>Что необходимо для создания шлюза приложений?

* **Внутренний пул серверов**. Список IP-адресов внутренних серверов. Указанные IP-адреса должны относиться к подсети виртуальной сети либо представлять собой общедоступные или виртуальные IP-адреса.
* **Параметры внутреннего пула серверов**. Каждый пул имеет такие параметры, как порт, протокол и сходство на основе файлов cookie. Эти параметры привязываются к пулу и применяются ко всем серверам в этом пуле.
* **Интерфейсный порт**. Общедоступный порт, открытый в шлюзе приложений. Трафик поступает на этот порт, а затем перенаправляется на один из тыловых серверов.
* **Прослушиватель**. У прослушивателя есть интерфейсный порт, протокол (HTTP или HTTPS, с учетом регистра) и имя SSL-сертификата (при настройке разгрузки SSL).
* **Правило**. Правило связывает прослушиватель и внутренний пул серверов, а также определяет, в какой внутренний пул серверов следует направлять трафик, поступающий на определенный прослушиватель. В настоящее время поддерживается только *основное* правило. *Основное* правило предусматривает циклическое распределение нагрузки.

**Дополнительные примечания по настройке конфигурации**

Чтобы настроить конфигурацию SSL-сертификатов, протокол в элементе **HttpListener** следует изменить на **Https** (с учетом регистра). Добавьте элемент **SslCertificate** в **HttpListener**, указав в качестве значения переменную, настроенную для SSL-сертификата. Внешний порт следует изменить на **443**.

**Включение сходства на основе файлов cookie**. Шлюз приложений можно настроить так, чтобы запросы от клиентского сеанса всегда направлялись на одну виртуальную машину в веб-ферме. Для этого необходимо вставить файлы cookie сеанса. Таким образом, шлюз сможет перенаправлять трафик соответствующим образом. Чтобы включить сходство на основе файлов cookie, присвойте параметру **CookieBasedAffinity** в элементе **BackendHttpSettings** значение **Enabled**.

## <a name="create-an-application-gateway"></a>Создание шлюза приложений

Разница между использованием классической модели развертывания Azure и Azure Resource Manager заключается в порядке создания шлюза приложений и элементов, которые нужно настроить.

При использовании Resource Manager все компоненты, которые будут включены в единый ресурс шлюза приложений, сначала настраиваются по отдельности.

Ниже приведены пошаговые инструкции по созданию шлюза приложений.

1. [Создание группы ресурсов для Resource Manager](#create-a-resource-group-for-resource-manager).
2. [Создание виртуальной сети, подсети и общедоступного IP-адреса для шлюза приложений](#create-virtual-network-subnet-and-public-IP-for-the-application-gateway).
3. [Создание объекта конфигурации шлюза приложений](#create-an-application-gateway-configuration-object).
4. [Создание ресурса шлюза приложений](#create-an-application-gateway-resource).

## <a name="create-a-resource-group-for-resource-manager"></a>Создание группы ресурсов для диспетчера ресурсов

Для работы с командлетами диспетчера ресурсов Azure необходимо перейти в режим PowerShell. Дополнительные сведения см. в статье [Использование Azure PowerShell с диспетчером ресурсов Azure](../powershell-azure-resource-manager.md).

   1. Введите следующую команду:

   ```powershell
   Login-AzureRmAccount
   ```

   2. Чтобы проверить подписки для учетной записи, введите следующие команды:

   ```powershell
   Get-AzureRmSubscription
   ```

   Вам будет предложено указать свои учетные данные для проверки подлинности.

   3. Чтобы выбрать подписку Azure, введите следующую команду:

   ```powershell
   Select-AzureRmSubscription -Subscriptionid "GUID of subscription"
   ```

   4. Чтобы создать группу ресурсов, введите следующую команду: Если используется существующая группа ресурсов, пропустите этот шаг.

   ```powershell
   New-AzureRmResourceGroup -Name appgw-rg -Location "West US"
   ```

В диспетчере ресурсов Azure для всех групп ресурсов должно быть указано расположение. Оно используется в качестве расположения по умолчанию для всех ресурсов данной группы. Убедитесь, что во всех командах для создания шлюза приложений используется одна группа ресурсов.

В предыдущем примере мы создали группу ресурсов **appgw-RG** в расположении **Западная часть США**.

## <a name="create-a-virtual-network-and-a-subnet-for-the-application-gateway"></a>Создание виртуальной сети и подсети для шлюза приложений.

В следующем примере показано создание виртуальной сети с помощью диспетчера ресурсов.

   1. Введите следующую команду:

   ```powershell
   $subnet = New-AzureRmVirtualNetworkSubnetConfig -Name subnet01 -AddressPrefix 10.0.0.0/24
   ```

   Этот пример назначает диапазон адресов **10.0.0.0/24** переменной подсети, которая будет использоваться для создания виртуальной сети.

   2. Введите следующую команду:

   ```powershell
   $vnet = New-AzureRmVirtualNetwork -Name appgwvnet -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $subnet
   ```

   Этот пример создает виртуальную сеть **appgwvnet** в группе ресурсов **appgw-rg** для региона **Западная часть США** с помощью префикса **10.0.0.0/16** с подсетью **10.0.0.0/24**.

   3. Введите следующую команду:

   ```powershell
   $subnet = $vnet.Subnets[0]
   ```

   Этот пример присваивает объект подсети переменной **$subnet** для выполнения следующих действий.

## <a name="create-a-public-ip-address-for-the-front-end-configuration"></a>Создание общедоступного IP-адреса для конфигурации интерфейсной части

Чтобы создать общедоступный IP-адрес для интерфейсной конфигурации, введите следующую команду:

```powershell
$publicip = New-AzureRmPublicIpAddress -ResourceGroupName appgw-rg -name publicIP01 -location "West US" -AllocationMethod Dynamic
```

Этот пример создает ресурс общедоступного IP-адреса **publicIP01** в группе ресурсов **appgw-rg** для региона **Западная часть США**.

## <a name="create-an-application-gateway-configuration-object"></a>Создание объекта конфигурации шлюза приложений

   1. Чтобы создать объект конфигурации шлюза приложений, введите следующую команду:

   ```powershell
   $gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $subnet
   ```

   Этот пример создает конфигурацию IP-адреса шлюза приложений с именем **gatewayIP01**. При запуске шлюз приложений получает IP-адрес из настроенной подсети. Затем шлюз маршрутизирует сетевой трафик на IP-адреса из внутреннего пула IP-адресов. Помните, что для каждого экземпляра требуется отдельный IP-адрес.

   2. Введите следующую команду:

   ```powershell
   $pool = New-AzureRmApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 134.170.185.46, 134.170.188.221,134.170.185.50
   ```

   Этот пример настраивает пул внутренних IP-адресов с именем **pool01** и IP-адресами **134.170.185.46**, **134.170.188.221** и **134.170.185.50**. Эти значения IP-адресов будут использоваться для получения сетевого трафика от конечной точки с интерфейсным IP-адресом. Замените IP-адрес в предыдущем примере IP-адресом конечных точек вашего веб-приложения.

   3. Введите следующую команду:

   ```powershell
   $poolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name poolsetting01 -Port 80 -Protocol Http -CookieBasedAffinity Enabled
   ```

   Этот пример настраивает параметр шлюза приложений **poolsetting01** для балансировки нагрузки сетевого трафика во внутреннем пуле.

   4. Введите следующую команду:

   ```powershell
   $fp = New-AzureRmApplicationGatewayFrontendPort -Name frontendport01  -Port 443
   ```

   Этот пример настраивает внешний IP-порт **frontendport01** для конечной точки с общедоступным IP-адресом.

   5. Введите следующую команду:

   ```powershell
   $cert = New-AzureRmApplicationGatewaySslCertificate -Name cert01 -CertificateFile <full path for certificate file> -Password "<password>"
   ```

   Этот пример настраивает сертификат для SSL-соединения. Сертификат должен быть в формате PFX, а пароль к сертификату должен содержать от 4 до 12 символов.

   6. Введите следующую команду:

   ```powershell
   $fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig -Name fipconfig01 -PublicIPAddress $publicip
   ```

   Этот пример создает конфигурацию внешнего IP-адреса с именем **fipconfig01** и связывает с ней общедоступный IP-адрес.

   7. Введите следующую команду:

   ```powershell
   $listener = New-AzureRmApplicationGatewayHttpListener -Name listener01  -Protocol Https -FrontendIPConfiguration $fipconfig -FrontendPort $fp -SslCertificate $cert
   ```

   Этот пример создает прослушиватель **listener01** и связывает внешний порт с конфигурацией внешнего IP-адреса и сертификатом.

   8. Введите следующую команду:

   ```powershell
   $rule = New-AzureRmApplicationGatewayRequestRoutingRule -Name rule01 -RuleType Basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool
   ```

   Этот пример создает правило маршрутизации **rule01** для настройки поведения подсистемы балансировки нагрузки.

   9. Введите следующую команду:

   ```powershell
   $sku = New-AzureRmApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2
   ```

   Этот пример настраивает размер экземпляра шлюза приложений.

   > [!NOTE]
   > Значение параметра **InstanceCount** по умолчанию — **2** (максимальное значение — 10). По умолчанию для параметра **GatewaySize** используется значение **Средний**. Можно выбрать Standard_Small, Standard_Medium или Standard_Large.

   10. Введите следующую команду:

   ```powershell
   $policy = New-AzureRmApplicationGatewaySslPolicy -PolicyType Predefined -PolicyName AppGwSslPolicy20170401S
   ```

   На этом шаге выполняется настройка политики SSL для шлюза приложений. Дополнительные сведения см. в статье [Настройка версий политики SSL и комплектов шифров на шлюзе приложений](application-gateway-configure-ssl-policy-powershell.md).

## <a name="create-an-application-gateway-by-using-new-azureapplicationgateway"></a>Создание шлюза приложений с помощью командлета New-AzureApplicationGateway

Введите следующую команду:

```powershell
$appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku -SslCertificates $cert -SslPolicy $policy
```

Этот пример создает шлюз приложений со всеми элементами конфигурации, описанными выше. В этом примере шлюз приложений называется **appgwtest**.

## <a name="get-the-application-gateway-dns-name"></a>Получение DNS-имени шлюза приложений

После создания шлюза следует настроить внешний интерфейс для обмена данными. Если вы используете общедоступный IP-адрес, шлюзу приложений требуется динамически назначаемое непонятное имя DNS. Чтобы пользователи гарантированно попали на шлюз приложений, можно использовать запись CNAME для указания общедоступной конечной точки шлюза приложений. Дополнительные сведения см. в статье [Настройка пользовательского доменного имени для облачной службы Azure](../cloud-services/cloud-services-custom-domain-name-portal.md). 

Чтобы получить DNS-имя шлюза приложений, получите сведения о шлюзе приложений и соответствующее IP- или DNS-имя с помощью элемента **PublicIPAddress**, связанного со шлюзом приложений. Используйте DNS-имя шлюза приложений для создания записи CNAME, указывающей двум веб-приложениям на это DNS-имя. Мы не рекомендуем использовать записи типа A, так как виртуальный IP-адрес может измениться после перезапуска шлюза приложения.


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

Указания по настройке шлюза приложений для использования с внутренним балансировщиком нагрузки см. в статье [Создание шлюза приложений с внутренней подсистемой балансировщика нагрузки (ILB)](application-gateway-ilb.md).

Дополнительные сведения о параметрах балансировки нагрузки в целом см. в статьях:

* [Подсистема балансировщика нагрузки Azure](https://azure.microsoft.com/documentation/services/load-balancer/)
* [Azure Traffic Manager](https://azure.microsoft.com/documentation/services/traffic-manager/)


---
title: Настройка сквозного режима связи SSL для шлюза приложений Azure
description: В этой статье описывается настройка сквозного режима связи SSL для шлюза приложений Azure с помощью PowerShell.
services: application-gateway
documentationcenter: na
author: vhorne
manager: jpconnock
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 3/27/2018
ms.author: victorh
ms.openlocfilehash: 7e259936dce433683dd135171ee1c5626bf23739
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2018
ms.locfileid: "32154200"
---
# <a name="configure-end-to-end-ssl-by-using-application-gateway-with-powershell"></a>Настройка сквозного режима связи SSL для шлюза приложений с помощью PowerShell

## <a name="overview"></a>Обзор

Шлюз приложений Azure поддерживает сквозное шифрование трафика. Для этого он завершает SSL-соединение. Затем шлюз применяет правила маршрутизации к трафику, повторно шифрует пакет и пересылает его в соответствующую серверную часть согласно определенным правилам маршрутизации. Любой ответ веб-сервера проходит через тот же процесс на пути к пользователю.

Шлюз приложений поддерживает определение пользовательских вариантов SSL и отключение следующих версий протокола: **TLSv1.0**, **TLSv1.1** и **TLSv1.2**. Также шлюз поддерживает определение комплектов шифров для использования и их приоритет. Дополнительные сведения о настраиваемых параметрах SSL см. в статье [Общие сведения о политике SSL шлюза приложений](application-gateway-SSL-policy-overview.md).

> [!NOTE]
> Протоколы SSL 2.0 и SSL 3.0 отключены по умолчанию, и их нельзя включать. Они считаются небезопасными и не могут использоваться со шлюзом приложений.

![Изображение для сценария][scenario]

## <a name="scenario"></a>Сценарий

В этом сценарии вы узнаете, как создать шлюз приложений со сквозным режимом связи SSL с помощью PowerShell.

Вы узнаете:

* как создать группу ресурсов с именем **appgw-rg**;
* как создать виртуальную сеть **appgwvnet** с адресным пространством **10.0.0.0/16**;
* как создать две подсети, **appgwsubnet** и **appsubnet**;
* как создать небольшой шлюз приложений, поддерживающий сквозное шифрование SSL и ограничивающий определенные версии протокола SSL и комплекты шифров.

## <a name="before-you-begin"></a>Перед началом работы

Чтобы настроить сквозной режим связи SSL для шлюза приложений, требуются сертификат для шлюза и сертификаты для внутренних серверов. Сертификат шлюза используется для шифрования и расшифровки трафика, передаваемого по протоколу SSL. Сертификат шлюза должен быть представлен в формате PFX (файл обмена личной информацией). Этот формат файла позволяет экспортировать закрытый ключ, необходимый шлюзу приложений для шифрования и расшифровки трафика.

Чтобы использовать сквозное шифрование SSL, серверная часть должна быть внесена в список разрешений с помощью шлюза приложений. Необходимо передать общий сертификат серверных частей в шлюз приложений. Это гарантирует, что шлюз приложений взаимодействует только с известными экземплярами серверной части, тем самым защищая сквозной обмен данными.

Процесс настройки описан в следующих разделах.

## <a name="create-the-resource-group"></a>Создание группы ресурсов

В этом разделе описывается создание группы ресурсов, содержащей шлюз приложений.


   1. Войдите в учетную запись Azure.

   ```powershell
   Connect-AzureRmAccount
   ```


   2. Выберите подписку для этого сценария.

   ```powershell
   Select-AzureRmsubscription -SubscriptionName "<Subscription name>"
   ```


   3. Создайте группу ресурсов. Если используется существующая группа ресурсов, пропустите этот шаг.

   ```powershell
   New-AzureRmResourceGroup -Name appgw-rg -Location "West US"
   ```

## <a name="create-a-virtual-network-and-a-subnet-for-the-application-gateway"></a>Создание виртуальной сети и подсети для шлюза приложений.

Следующий пример создает виртуальную сеть и две подсети. Одна подсеть используется для размещения шлюза приложений, а другая — для внутренних серверов, на которых размещается веб-приложение.


   1. Назначьте диапазон адресов подсети для шлюза приложений.

   ```powershell
   $gwSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name 'appgwsubnet' -AddressPrefix 10.0.0.0/24
   ```

   > [!NOTE]
   > Подсети, настроенные для шлюза приложений, должны иметь соответствующий размер. У шлюза приложений может быть до 10 экземпляров. Для каждого экземпляра требуется отдельный IP-адрес из подсети. Слишком маленький размер подсети может отрицательно сказаться на возможности масштабирования шлюза приложений.
   > 
   > 

   2. Назначьте диапазон адресов для внутреннего пула адресов.

   ```powershell
   $nicSubnet = New-AzureRmVirtualNetworkSubnetConfig  -Name 'appsubnet' -AddressPrefix 10.0.2.0/24
   ```

   3. Создайте виртуальную сеть с подсетями, определенными на предыдущих шагах.

   ```powershell
   $vnet = New-AzureRmvirtualNetwork -Name 'appgwvnet' -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $gwSubnet, $nicSubnet
   ```

   4. Получите ресурс виртуальной сети и ресурсы подсетей, которые будут использоваться при выполнении следующих действий.

   ```powershell
   $vnet = Get-AzureRmvirtualNetwork -Name 'appgwvnet' -ResourceGroupName appgw-rg
   $gwSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name 'appgwsubnet' -VirtualNetwork $vnet
   $nicSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name 'appsubnet' -VirtualNetwork $vnet
   ```

## <a name="create-a-public-ip-address-for-the-front-end-configuration"></a>Создание общедоступного IP-адреса для конфигурации интерфейсной части

Создайте ресурс общедоступного IP-адреса для шлюза приложений. Этот общедоступный IP-адрес используется на одном из следующих шагов.

```powershell
$publicip = New-AzureRmPublicIpAddress -ResourceGroupName appgw-rg -Name 'publicIP01' -Location "West US" -AllocationMethod Dynamic
```

> [!IMPORTANT]
> Шлюз приложений не поддерживает использование общедоступного IP-адреса, созданного с определенной меткой домена. Поддерживается только общедоступный IP-адрес с динамически создаваемой меткой домена. Если для шлюза приложений требуется понятное DNS-имя, мы рекомендуем использовать в качестве псевдонима запись CNAME.

## <a name="create-an-application-gateway-configuration-object"></a>Создание объекта конфигурации шлюза приложений.

Перед созданием шлюза приложений необходимо настроить все элементы конфигурации. В ходе следующих шагов создаются необходимые элементы конфигурации для ресурса шлюза приложений.

   1. Создайте IP-конфигурацию шлюза приложений. Она определяет, какую подсеть использует шлюз приложений. При запуске шлюз приложений получает IP-адрес из настроенной подсети. Затем шлюз маршрутизирует сетевой трафик на IP-адреса во внутреннем пуле IP-адресов. Помните, что для каждого экземпляра требуется отдельный IP-адрес.

   ```powershell
   $gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name 'gwconfig' -Subnet $gwSubnet
   ```


   2. Создайте IP-конфигурацию внешнего интерфейса, в которой частный или общедоступный IP-адрес сопоставляется с внешним интерфейсом шлюза приложений. На следующем шаге общедоступный IP-адрес, указанный на предыдущем шаге, будет связан конфигурацией IP внешнего интерфейса.

   ```powershell
   $fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig -Name 'fip01' -PublicIPAddress $publicip
   ```

   3. Настройте IP-адрес внутренних веб-серверов во внутреннем пуле IP-адресов. Эти IP-адреса будут использоваться для получения сетевого трафика от конечной точки с интерфейсным IP-адресом. Замените IP-адреса в примере и добавьте конечные точки IP-адресов своего приложения.

   ```powershell
   $pool = New-AzureRmApplicationGatewayBackendAddressPool -Name 'pool01' -BackendIPAddresses 1.1.1.1, 2.2.2.2, 3.3.3.3
   ```

   > [!NOTE]
   > Для внутренних серверов вместо IP-адресов можно также использовать полные доменные имена. Для этого используйте параметр **-BackendFqdns**. 


   4. Настройте интерфейсный порт IP для конечной точки с общедоступным IP-адресом. Это порт, к которому подключаются пользователи.

   ```powershell
   $fp = New-AzureRmApplicationGatewayFrontendPort -Name 'port01'  -Port 443
   ```

   5. Настройте сертификат для шлюза приложений. Этот сертификат используется для шифрования и расшифровки трафика на шлюзе приложений.

   ```powershell
   $passwd = ConvertTo-SecureString  <certificate file password> -AsPlainText -Force 
   $cert = New-AzureRmApplicationGatewaySSLCertificate -Name cert01 -CertificateFile <full path to .pfx file> -Password $passwd 
   ```

   > [!NOTE]
   > Этот пример настраивает сертификат для SSL-соединения. Сертификат должен иметь формат PFX, а пароль к сертификату должен содержать от 4 до 12 символов.

   6. Создайте прослушиватель HTTP для шлюза приложений. Назначьте используемую IP-конфигурацию внешнего интерфейса, порт и SSL-сертификат.

   ```powershell
   $listener = New-AzureRmApplicationGatewayHttpListener -Name listener01 -Protocol Https -FrontendIPConfiguration $fipconfig -FrontendPort $fp -SSLCertificate $cert
   ```

   7. Передайте сертификат, который будет использоваться для ресурсов внутреннего пула, поддерживающих протокол SSL.

   > [!NOTE]
   > Проба по умолчанию получает открытый ключ из привязки SSL *по умолчанию* по IP-адресу серверной части и сравнивает полученное значение открытого ключа со значением, которое предоставляете вы. 
   
   > Если на серверной части используются заголовки узлов и указание имени сервера (SNI), полученный открытый ключ не обязательно будет ключом сайта, на который направляется интернет-трафик. Если вы сомневаетесь, перейдите по адресу https://127.0.0.1/ на внутренних серверах, чтобы проверить, какой сертификат используется для привязки SSL *по умолчанию*. В этом разделе используйте открытый ключ из этого запроса. Если в привязках HTTPS используются заголовки узлов и указание имен сервера (SNI) и вы не получаете ответ и сертификат, после того как вручную отправили в браузере запрос на адрес https://127.0.0.1/ для внутренних серверов, на них необходимо настроить привязку SSL по умолчанию. Если этого не сделать, пробы будут завершены ошибкой и серверная часть не будет добавлена в список разрешений.

   ```powershell
   $authcert = New-AzureRmApplicationGatewayAuthenticationCertificate -Name 'whitelistcert1' -CertificateFile C:\users\gwallace\Desktop\cert.cer
   ```

   > [!NOTE]
   > Сертификат, предоставленный на этом шаге, должен быть открытым ключом сертификата в формате PFX, хранящимся в серверной части. Экспортируйте сертификат (не корневой) в формате CER, установленный на внутреннем сервере, и используйте его на этом шаге. Это действие добавляет серверную часть в список разрешений шлюза приложений.

   8. Настройте параметры HTTP для серверной части шлюза приложений. В параметрах HTTP назначьте сертификат, переданный на предыдущем шаге.

   ```powershell
   $poolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name 'setting01' -Port 443 -Protocol Https -CookieBasedAffinity Enabled -AuthenticationCertificates $authcert
   ```
   9. Создайте правило маршрутизации для балансировщика нагрузки, которое настраивает его поведение. В этом примере создается базовое правило с циклическим перебором.

   ```powershell
   $rule = New-AzureRmApplicationGatewayRequestRoutingRule -Name 'rule01' -RuleType basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool
   ```

   10. Настройте размер экземпляра шлюза приложений. Доступные размеры: **Standard\_Small**, **Standard\_Medium** и **Standard\_Large**.  Доступны значения емкости: от **1** до **10**.

   ```powershell
   $sku = New-AzureRmApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2
   ```

   > [!NOTE]
   > Для тестирования можно выбрать число экземпляров, равное 1. Важно знать, что если указать число экземпляров менее двух, то развертывание не попадает под действие соглашения об уровне обслуживания, и поэтому это не рекомендуется. Мелкие шлюзы предназначены для разработки и тестирования, а не для производства.

   11. Настройте политику SSL для шлюза приложений. Шлюз приложений поддерживает возможность установить минимальную версию для версии протокола SSL.

   Ниже приведен список значений для версий протокола, которые можно определить:

    - **TLSv1_0**;
    - **TLSv1_1**;
    - **TLSv1_2**.
    
   В следующем примере показано, как задать **TLSv1_2** в качестве минимальной версии протокола и разрешить только шифры **TLS\_ECDHE\_ECDSA\_WITH\_AES\_128\_GCM\_SHA256**, **TLS\_ECDHE\_ECDSA\_WITH\_AES\_256\_GCM\_SHA384** и **TLS\_RSA\_WITH\_AES\_128\_GCM\_SHA256**.

   ```powershell
   $SSLPolicy = New-AzureRmApplicationGatewaySSLPolicy -MinProtocolVersion TLSv1_2 -CipherSuite "TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256", "TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384", "TLS_RSA_WITH_AES_128_GCM_SHA256"
   ```

## <a name="create-the-application-gateway"></a>Создание шлюза приложений

Создайте шлюз приложений в соответствии с действиями, выполненными на предыдущих шагах. Выполнение этого процесса занимает много времени.

```powershell
$appgw = New-AzureRmApplicationGateway -Name appgateway -SSLCertificates $cert -ResourceGroupName "appgw-rg" -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku -SSLPolicy $SSLPolicy -AuthenticationCertificates $authcert -Verbose
```

## <a name="limit-ssl-protocol-versions-on-an-existing-application-gateway"></a>Ограничение версий протокола SSL на имеющемся шлюзе приложений

Выше было описано создание шлюза приложения со сквозным режимом связи SSL и отключение определенных версий протокола SSL. В следующем примере отключаются определенные политики SSL на существующем шлюзе приложений.

   1. Получите шлюз приложений, который требуется обновить.

   ```powershell
   $gw = Get-AzureRmApplicationGateway -Name AdatumAppGateway -ResourceGroupName AdatumAppGatewayRG
   ```

   2. Определите политику SSL. Ниже представлен пример отключения версий **TLSv1.0** и **TLSv1.1** и разрешения только комплектов шифров **TLS\_ECDHE\_ECDSA\_WITH\_AES\_128\_GCM\_SHA256**, **TLS\_ECDHE\_ECDSA\_WITH\_AES\_256\_GCM\_SHA384**, и **TLS\_RSA\_WITH\_AES\_128\_GCM\_SHA256**.

   ```powershell
   Set-AzureRmApplicationGatewaySSLPolicy -MinProtocolVersion TLSv1_2 -PolicyType Custom -CipherSuite "TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256", "TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384", "TLS_RSA_WITH_AES_128_GCM_SHA256" -ApplicationGateway $gw

   ```

   3. Наконец, обновите шлюз. Обратите внимание, что последний шаг занимает много времени. По завершении на шлюзе приложений будет настроен сквозной режим связи SSL.

   ```powershell
   $gw | Set-AzureRmApplicationGateway
   ```

## <a name="get-an-application-gateway-dns-name"></a>Получение DNS-имени шлюза приложений

После создания шлюза следует настроить внешний интерфейс для обмена данными. Если вы используете общедоступный IP-адрес, шлюзу приложений требуется динамически назначаемое непонятное имя DNS. Чтобы пользователи гарантированно попали на шлюз приложений, можно использовать запись CNAME для указания общедоступной конечной точки шлюза приложений. Дополнительные сведения см. в статье [Настройка пользовательского доменного имени для облачной службы Azure](../cloud-services/cloud-services-custom-domain-name-portal.md). 

Чтобы настроить псевдоним, извлеките подробные сведения о шлюзе приложений и соответствующий IP-адрес или DNS-имя с помощью элемента **PublicIPAddress**, связанного со шлюзом приложений. Используйте DNS-имя шлюза приложений для создания записи CNAME, указывающей двум веб-приложениям на это DNS-имя. Мы не рекомендуем использовать записи типа A, так как виртуальный IP-адрес может измениться после перезапуска шлюза приложения.

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

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения об усилении безопасности веб-приложений с помощью брандмауэра веб-приложения в шлюзе приложений см. в статье [Брандмауэр веб-приложения (WAF)](application-gateway-webapplicationfirewall-overview.md).

[scenario]: ./media/application-gateway-end-to-end-SSL-powershell/scenario.png

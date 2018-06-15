---
title: Настройка разгрузки SSL для шлюза приложений Azure с помощью PowerShell (классическая модель) | Документация Майкрософт
description: В этой статье приводятся инструкции по созданию шлюза приложений с разгрузкой SSL с помощью классической модели развертывания Azure.
documentationcenter: na
services: application-gateway
author: vhorne
manager: jpconnock
editor: tysonn
ms.assetid: 63f28d96-9c47-410e-97dd-f5ca1ad1b8a4
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/23/2017
ms.author: victorh
ms.openlocfilehash: e620730b86d648c1ac9db7a9e6faa7a2d206b46e
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/04/2018
ms.locfileid: "33205386"
---
# <a name="configure-an-application-gateway-for-ssl-offload-by-using-the-classic-deployment-model"></a>Настройка шлюза приложений для разгрузки SSL с помощью классической модели развертывания

> [!div class="op_single_selector"]
> * [портал Azure](application-gateway-ssl-portal.md)
> * [PowerShell и диспетчер ресурсов Azure](application-gateway-ssl-arm.md)
> * [Классическая модель — Azure PowerShell](application-gateway-ssl.md)
> * [Azure CLI 2.0](application-gateway-ssl-cli.md)

Шлюз приложений Azure можно настроить на завершение сеанса SSL в шлюзе, что позволит избежать выполнения дорогостоящей задачи SSL-шифрования на веб-ферме. Кроме того, разгрузка SSL упрощает процесс установки внешнего сервера и управления веб-приложением.

## <a name="before-you-begin"></a>Перед началом работы

1. Установите последнюю версию командлетов Azure PowerShell, используя установщик веб-платформы. Скачать и установить последнюю версию вы можете в разделе **Windows PowerShell** на [странице загрузок](https://azure.microsoft.com/downloads/).
2. Убедитесь, что у вас есть рабочая виртуальная сеть с действительной подсетью. Убедитесь, что подсеть не используется виртуальной машиной или облачным развертыванием. Сам шлюз приложений должен находиться в подсети виртуальной сети.
3. Для использования шлюза приложений настраиваются имеющиеся серверы или серверы, для которых в виртуальной сети созданы конечные точки либо же назначен общедоступный или виртуальный IP-адрес.

Чтобы настроить разгрузку SSL в шлюзе приложений, выполните перечисленные ниже действия в указанном порядке:

1. [Создание шлюза приложений](#create-an-application-gateway)
2. [Загрузка SSL-сертификатов](#upload-ssl-certificates)
3. [Настройка шлюза](#configure-the-gateway)
4. [Настройка конфигурации шлюза](#set-the-gateway-configuration)
5. [Запуск шлюза](#start-the-gateway)
6. [Проверка состояния шлюза](#verify-the-gateway-status)

## <a name="create-an-application-gateway"></a>Создание шлюза приложений

Для создания шлюза введите командлет `New-AzureApplicationGateway`, подставив в него свои значения. Выставление счетов для шлюза начинается не на данном этапе, а позднее, после успешного запуска шлюза.

```powershell
New-AzureApplicationGateway -Name AppGwTest -VnetName testvnet1 -Subnets @("Subnet-1")
```

Чтобы проверить создание шлюза, введите командлет `Get-AzureApplicationGateway`.

В примере параметры **Description** (Описание), **InstanceCount** (Количество экземпляров) и **GatewaySize** (Размер шлюза) являются необязательными. Значение параметра **InstanceCount** по умолчанию — **2** (максимальное значение — **10**). По умолчанию для параметра **GatewaySize** используется значение **Средний**. Также доступны значения Small (Маленький) и Large (Большой). Параметры **VirtualIPs** и **DnsName** отображаются без значений, так как шлюз еще не запущен. Эти значения будут определены после запуска шлюза.

```powershell
Get-AzureApplicationGateway AppGwTest
```

## <a name="upload-ssl-certificates"></a>Загрузка SSL-сертификатов

Чтобы загрузить на шлюз приложений сертификаты сервера в формате PFX, введите командлет `Add-AzureApplicationGatewaySslCertificate`. Имя сертификата выбирается пользователем и должно быть уникальным в пределах шлюза приложений. В операциях управления сертификатами в шлюзе приложений сертификат указывается по имени.

В следующем примере приведен командлет. Замените значения в нем собственными.

```powershell
Add-AzureApplicationGatewaySslCertificate  -Name AppGwTest -CertificateName GWCert -Password <password> -CertificateFile <full path to pfx file>
```

Затем проверьте загрузку сертификата. Введите командлет `Get-AzureApplicationGatewayCertificate`.

В следующем примере командлет показан в первой строке, а за ним следуют выходные данные.

```powershell
Get-AzureApplicationGatewaySslCertificate AppGwTest
```

```
VERBOSE: 5:07:54 PM - Begin Operation: Get-AzureApplicationGatewaySslCertificate
VERBOSE: 5:07:55 PM - Completed Operation: Get-AzureApplicationGatewaySslCertificate
Name           : SslCert
SubjectName    : CN=gwcert.app.test.contoso.com
Thumbprint     : AF5ADD77E160A01A6......EE48D1A
ThumbprintAlgo : sha1RSA
State..........: Provisioned
```

> [!NOTE]
> Пароль сертификата должен содержать 4–12 символов, состоящих из букв или цифр. Специальные знаки не допускаются.

## <a name="configure-the-gateway"></a>Настройка шлюза

Конфигурация шлюза приложений состоит из нескольких значений. Значения конфигурации могут быть взаимосвязаны.

Доступны следующие значения.

* **Внутренний пул серверов**. Список IP-адресов внутренних серверов. Указанные IP-адреса должны относиться к подсети виртуальной сети либо представлять собой общедоступные или виртуальные IP-адреса.
* **Параметры внутреннего пула серверов**. Каждый пул имеет такие параметры, как порт, протокол и сходство на основе файлов cookie. Эти параметры привязываются к пулу и применяются ко всем серверам в этом пуле.
* **Интерфейсный порт**. Общедоступный порт, открытый в шлюзе приложений. Трафик поступает на этот порт, а затем перенаправляется на один из тыловых серверов.
* **Прослушиватель**. У прослушивателя есть интерфейсный порт, протокол (Http или Https — с учетом регистра) и имя SSL-сертификата (в случае настройки разгрузки SSL).
* **Правило**. Правило связывает прослушиватель и внутренний пул серверов, а также определяет, в какой внутренний пул серверов следует направлять трафик, поступающий на определенный прослушиватель. В настоящее время поддерживается только *основное* правило. *Основное* правило предусматривает циклическое распределение нагрузки.

**Дополнительные примечания по настройке конфигурации**

Чтобы настроить конфигурацию SSL-сертификатов, протокол в элементе **HttpListener** следует изменить на **Https** (с учетом регистра). Добавьте к **HttpListener** элемент **SslCert** со значением, соответствующим имени, которое использовалось выше при [передаче SSL-сертификатов](#upload-ssl-certificates). Внешний порт следует изменить на **443**.

**Включение сходства на основе файлов cookie**. Шлюз приложений можно настроить так, чтобы запросы от клиентского сеанса всегда направлялись на одну виртуальную машину в веб-ферме. Для этого необходимо вставить файлы cookie сеанса. Таким образом, шлюз сможет перенаправлять трафик соответствующим образом. Чтобы включить сходство на основе файлов cookie, присвойте параметру **CookieBasedAffinity** в элементе **BackendHttpSettings** значение **Enabled**.

Настроить конфигурацию можно либо путем создания объекта конфигурации, либо с помощью XML-файла конфигурации.
Чтобы создать конфигурацию с помощью XML-файла конфигурации, введите следующий пример.


```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationGatewayConfiguration xmlns:i="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/windowsazure">
    <FrontendIPConfigurations />
    <FrontendPorts>
        <FrontendPort>
            <Name>FrontendPort1</Name>
            <Port>443</Port>
        </FrontendPort>
    </FrontendPorts>
    <BackendAddressPools>
        <BackendAddressPool>
            <Name>BackendPool1</Name>
            <IPAddresses>
                <IPAddress>10.0.0.1</IPAddress>
                <IPAddress>10.0.0.2</IPAddress>
            </IPAddresses>
        </BackendAddressPool>
    </BackendAddressPools>
    <BackendHttpSettingsList>
        <BackendHttpSettings>
            <Name>BackendSetting1</Name>
            <Port>80</Port>
            <Protocol>Http</Protocol>
            <CookieBasedAffinity>Enabled</CookieBasedAffinity>
        </BackendHttpSettings>
    </BackendHttpSettingsList>
    <HttpListeners>
        <HttpListener>
            <Name>HTTPListener1</Name>
            <FrontendPort>FrontendPort1</FrontendPort>
            <Protocol>Https</Protocol>
            <SslCert>GWCert</SslCert>
        </HttpListener>
    </HttpListeners>
    <HttpLoadBalancingRules>
        <HttpLoadBalancingRule>
            <Name>HttpLBRule1</Name>
            <Type>basic</Type>
            <BackendHttpSettings>BackendSetting1</BackendHttpSettings>
            <Listener>HTTPListener1</Listener>
            <BackendAddressPool>BackendPool1</BackendAddressPool>
        </HttpLoadBalancingRule>
    </HttpLoadBalancingRules>
</ApplicationGatewayConfiguration>
```

## <a name="set-the-gateway-configuration"></a>Настройка конфигурации шлюза

Теперь необходимо настроить шлюз приложений. Для этого можно ввести командлет `Set-AzureApplicationGatewayConfig` либо с объектом конфигурации, либо с XML-файлом конфигурации.

```powershell
Set-AzureApplicationGatewayConfig -Name AppGwTest -ConfigFile D:\config.xml
```

## <a name="start-the-gateway"></a>Запуск шлюза

Настроенный шлюз можно запустить с помощью командлета `Start-AzureApplicationGateway`. Выставление счетов для шлюза приложений начинается после запуска шлюза.

> [!NOTE]
> Выполнение командлета `Start-AzureApplicationGateway` может занять 15–20 минут.
>
>

```powershell
Start-AzureApplicationGateway AppGwTest
```

## <a name="verify-the-gateway-status"></a>Проверка состояния шлюза

Проверить состояние шлюза можно с помощью командлета `Get-AzureApplicationGateway`. Если на предыдущем этапе командлет `Start-AzureApplicationGateway` выполнен успешно, у параметра **State** должно быть значение **Running**, а у параметров **VirtualIPs** и **DnsName** — допустимые записи.

В данном примере показан рабочий шлюз приложений, готовый к приему трафика.

```powershell
Get-AzureApplicationGateway AppGwTest
```

```
Name          : AppGwTest2
Description   :
VnetName      : testvnet1
Subnets       : {Subnet-1}
InstanceCount : 2
GatewaySize   : Medium
State         : Running
VirtualIPs    : {23.96.22.241}
DnsName       : appgw-4c960426-d1e6-4aae-8670-81fd7a519a43.cloudapp.net
```

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о параметрах балансировки нагрузки в целом см. в статьях:

* [Подсистема балансировщика нагрузки Azure](https://azure.microsoft.com/documentation/services/load-balancer/)
* [Azure Traffic Manager](https://azure.microsoft.com/documentation/services/traffic-manager/)

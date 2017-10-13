---
title: "Настройка разгрузки SSL для шлюза приложений Azure с помощью Azure CLI 2.0 | Документация Майкрософт"
description: "В этой статье приводятся инструкции по созданию шлюза приложений с разгрузкой SSL с помощью Azure CLI 2.0."
documentationcenter: na
services: application-gateway
author: davidmu1
manager: timlt
editor: tysonn
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/26/2017
ms.author: davidmu
ms.openlocfilehash: a2c4062db821e39e1af4fa1d54da0121d3993db4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="configure-an-application-gateway-for-ssl-offload-by-using-azure-cli-20"></a>Настройка шлюза приложений для разгрузки SSL с помощью Azure CLI 2.0

> [!div class="op_single_selector"]
> * [Портал Azure](application-gateway-ssl-portal.md)
> * [PowerShell и диспетчер ресурсов Azure](application-gateway-ssl-arm.md)
> * [Классическая модель — Azure PowerShell](application-gateway-ssl.md)
> * [Azure CLI 2.0](application-gateway-ssl-cli.md)

Шлюз приложений Azure можно настроить на завершение сеанса SSL в шлюзе, что позволит избежать выполнения дорогостоящей задачи SSL-шифрования на веб-ферме. Разгрузка SSL также упрощает управление сертификатами на сервере переднего плана.

## <a name="prerequisite-install-the-azure-cli-20"></a>Предварительные требования. Установка Azure CLI 2.0

Для выполнения действий, описанных в этой статье, требуется [установить интерфейс командной строки Azure для Mac, Linux и Windows (Azure CLI)](https://docs.microsoft.com/en-us/cli/azure/install-az-cli2).

## <a name="required-components"></a>Необходимые компоненты

* **Внутренний пул серверов**. Список IP-адресов внутренних серверов. Указанные IP-адреса должны относиться к подсети виртуальной сети либо представлять собой общедоступные или виртуальные IP-адреса.
* **Параметры внутреннего пула серверов**. Каждый пул имеет такие параметры, как порт, протокол и сходство на основе файлов cookie. Эти параметры привязываются к пулу и применяются ко всем серверам в этом пуле.
* **Интерфейсный порт**. Общедоступный порт, открытый в шлюзе приложений. Трафик поступает на этот порт, а затем перенаправляется на один из тыловых серверов.
* **Прослушиватель**. У прослушивателя есть интерфейсный порт, протокол (HTTP или HTTPS, с учетом регистра) и имя SSL-сертификата (при настройке разгрузки SSL).
* **Правило**. Правило связывает прослушиватель и внутренний пул серверов, а также определяет, в какой внутренний пул серверов следует направлять трафик, поступающий на определенный прослушиватель. В настоящее время поддерживается только *основное* правило. *Основное* правило предусматривает циклическое распределение нагрузки.

**Дополнительные примечания по настройке конфигурации**

Чтобы настроить конфигурацию SSL-сертификатов, протокол в элементе **HttpListener** следует изменить на *Https* (с учетом регистра). Добавьте элемент **SslCertificate** в **HttpListener**, указав в качестве значения переменную, настроенную для SSL-сертификата. Внешний порт следует изменить на **443**.

**Включение сходства на основе файлов cookie**. Шлюз приложений можно настроить так, чтобы запросы от клиентского сеанса всегда направлялись на одну виртуальную машину в веб-ферме. Для этого необходимо вставить файлы cookie сеанса. Таким образом, шлюз сможет перенаправлять трафик соответствующим образом. Чтобы включить сходство на основе файлов cookie, присвойте параметру **CookieBasedAffinity** в элементе *BackendHttpSettings* значение **Enabled**.

## <a name="configure-ssl-offload-on-an-existing-application-gateway"></a>Настройка разгрузки SSL на имеющемся шлюзе приложений

Введите следующие команды, чтобы настроить разгрузку SSL для имеющегося шлюза приложения.

```azurecli-interactive
#!/bin/bash

# Create a new front end port to be used for SSL
az network application-gateway frontend-port create \
  --name sslport \
  --port 443 \
  --gateway-name "AdatumAppGateway" \
  --resource-group "AdatumAppGatewayRG"

# Upload the .pfx certificate for SSL offload
az network application-gateway ssl-cert create \
  --name "newcert" \
  --cert-file /home/azureuser/self-signed/AdatumAppGatewayCert.pfx \
  --cert-password P@ssw0rd \
  --gateway-name "AdatumAppGateway" \
  --resource-group "AdatumAppGatewayRG"

# Create a new listener referencing the port and certificate created earlier
az network application-gateway http-listener create \
  --frontend-ip "appGatewayFrontendIP" \
  --frontend-port sslport  \
  --name sslListener \
  --ssl-cert newcert \
  --gateway-name "AdatumAppGateway" \
  --resource-group "AdatumAppGatewayRG"

# Create a new back-end pool to be used
az network application-gateway address-pool create \
  --gateway-name "AdatumAppGateway" \
  --resource-group "AdatumAppGatewayRG" \
  --name "appGatewayBackendPool2" \
  --servers 10.0.0.7 10.0.0.8

# Create a new back-end HTTP settings using the new probe
az network application-gateway http-settings create \
  --name "settings2" \
  --port 80 \
  --cookie-based-affinity Enabled \
  --protocol "Http" \
  --gateway-name "AdatumAppGateway" \
  --resource-group "AdatumAppGatewayRG"

# Create a new rule linking the listener to the back-end pool
az network application-gateway rule create \
  --name "rule2" \
  --rule-type Basic \
  --http-settings settings2 \
  --http-listener ssllistener \
  --address-pool temp1 \
  --gateway-name "AdatumAppGateway" \
  --resource-group "AdatumAppGatewayRG"

```

## <a name="create-an-application-gateway-with-ssl-offload"></a>Создание шлюза приложений с разгрузкой SSL

В следующем примере создается шлюз приложений с разгрузкой SSL. Сертификат и пароль для сертификата необходимо обновить для действительного закрытого ключа.

```azurecli-interactive
#!/bin/bash

# Creates an application gateway with SSL offload
az network application-gateway create \
  --name "AdatumAppGateway3" \
  --location "eastus" \
  --resource-group "AdatumAppGatewayRG2" \
  --vnet-name "AdatumAppGatewayVNET2" \
  --cert-file /home/azureuser/self-signed/AdatumAppGatewayCert.pfx \
  --cert-password P@ssw0rd \
  --vnet-address-prefix "10.0.0.0/16" \
  --subnet "Appgatewaysubnet" \
  --subnet-address-prefix "10.0.0.0/28" \
  --frontend-port 443 \
  --servers "10.0.0.5 10.0.0.4" \
  --capacity 2 \
  --sku "Standard_Small" \
  --http-settings-cookie-based-affinity "Enabled" \
  --http-settings-protocol "Http" \
  --frontend-port "80" \
  --routing-rule-type "Basic" \
  --http-settings-port "80" \
  --public-ip-address "pip" \
  --public-ip-address-allocation "dynamic"
```

## <a name="get-an-application-gateway-dns-name"></a>Получение DNS-имени шлюза приложений

После создания шлюза следует настроить внешний интерфейс для обмена данными.  Если вы используете общедоступный IP-адрес, шлюзу приложений требуется динамически назначаемое непонятное имя DNS. Чтобы пользователи гарантированно попали на шлюз приложений, можно использовать запись CNAME для указания общедоступной конечной точки шлюза приложений. Дополнительные сведения см. в статье [Настройка пользовательского доменного имени для облачной службы Azure](../cloud-services/cloud-services-custom-domain-name-portal.md). 

Чтобы настроить псевдоним, извлеките подробные сведения о шлюзе приложений и соответствующий IP-адрес или DNS-имя с помощью элемента **PublicIPAddress**, связанного со шлюзом приложений. Используйте DNS-имя шлюза приложений для создания записи CNAME, указывающей двум веб-приложениям на это DNS-имя. Мы не рекомендуем использовать записи A, так как виртуальный IP-адрес может измениться после перезапуска шлюза приложения.


```azurecli-interactive
az network public-ip show --name "pip" --resource-group "AdatumAppGatewayRG"
```

```
{
  "dnsSettings": {
    "domainNameLabel": null,
    "fqdn": "8c786058-96d4-4f3e-bb41-660860ceae4c.cloudapp.net",
    "reverseFqdn": null
  },
  "etag": "W/\"3b0ac031-01f0-4860-b572-e3c25e0c57ad\"",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AdatumAppGatewayRG/providers/Microsoft.Network/publicIPAddresses/pip2",
  "idleTimeoutInMinutes": 4,
  "ipAddress": "40.121.167.250",
  "ipConfiguration": {
    "etag": null,
    "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AdatumAppGatewayRG/providers/Microsoft.Network/applicationGateways/AdatumAppGateway2/frontendIPConfigurations/appGatewayFrontendIP",
    "name": null,
    "privateIpAddress": null,
    "privateIpAllocationMethod": null,
    "provisioningState": null,
    "publicIpAddress": null,
    "resourceGroup": "AdatumAppGatewayRG",
    "subnet": null
  },
  "location": "eastus",
  "name": "pip2",
  "provisioningState": "Succeeded",
  "publicIpAddressVersion": "IPv4",
  "publicIpAllocationMethod": "Dynamic",
  "resourceGroup": "AdatumAppGatewayRG",
  "resourceGuid": "3c30d310-c543-4e9d-9c72-bbacd7fe9b05",
  "tags": {
    "cli[2] owner[administrator]": ""
  },
  "type": "Microsoft.Network/publicIPAddresses"
}
```

## <a name="next-steps"></a>Дальнейшие действия

Указания по настройке шлюза приложений для использования с внутренним балансировщиком нагрузки см. в статье [Создание шлюза приложений с внутренней подсистемой балансировщика нагрузки (ILB)](application-gateway-ilb.md).

Дополнительные сведения о параметрах балансировки нагрузки в целом см. в статьях:

* [Подсистема балансировщика нагрузки Azure](https://azure.microsoft.com/documentation/services/load-balancer/)
* [Azure Traffic Manager](https://azure.microsoft.com/documentation/services/traffic-manager/)

---
title: "Настройка брандмауэра веб-приложения для шлюза приложений Azure | Документация Майкрософт"
description: "Эта статья содержит рекомендации о том, как приступить к работе с брандмауэром веб-приложения на существующем или новом шлюзе приложений."
documentationcenter: na
services: application-gateway
author: georgewallace
manager: timlt
editor: tysonn
ms.assetid: 670b9732-874b-43e6-843b-d2585c160982
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/20/2017
ms.author: gwallace
ms.translationtype: HT
ms.sourcegitcommit: 8b857b4a629618d84f66da28d46f79c2b74171df
ms.openlocfilehash: ac6c629ceaf1a8036643f593ce3d7ef9ea096ef8
ms.contentlocale: ru-ru
ms.lasthandoff: 08/04/2017

---
# <a name="configure-web-application-firewall-on-a-new-or-existing-application-gateway-with-azure-cli"></a>Настройка брандмауэра веб-приложения на новом или имеющемся шлюзе приложений с помощью Azure CLI

> [!div class="op_single_selector"]
> * [Портал Azure](application-gateway-web-application-firewall-portal.md)
> * [PowerShell](application-gateway-web-application-firewall-powershell.md)
> * [Интерфейс командной строки Azure](application-gateway-web-application-firewall-cli.md)

Узнайте, как создать шлюз приложений с брандмауэром веб-приложения или добавить брандмауэр веб-приложения в существующий шлюз приложений.

Брандмауэр веб-приложения (WAF) в шлюзе приложений Azure защищает веб-приложения от таких распространенных сетевых атак, как атаки путем внедрения кода SQL, атаки межсайтовых сценариев и захваты сеанса.

Шлюз приложений — это балансировщик нагрузки уровня 7. Он отвечает за отработку отказов и эффективную маршрутизацию HTTP-запросов между разными серверами (облачными и локальными). Шлюз приложений выполняет многие функции контроллера доставки приложений (ADC), включая балансировку нагрузки HTTP, определение сходства сеансов на основе файлов cookie, разгрузку SSL, выполнение пользовательской проверки работоспособности, поддержку нескольких сайтов и т. д. Полный список поддерживаемых функций представлен в [обзоре шлюза приложений](application-gateway-introduction.md).

В следующей статье рассказывается, как [добавить брандмауэр веб-приложения в существующий шлюз приложений](#add-web-application-firewall-to-an-existing-application-gateway) и как [создать шлюз приложений с брандмауэром веб-приложения](#create-an-application-gateway-with-web-application-firewall).

![Изображение для сценария][scenario]

## <a name="prerequisite-install-the-azure-cli-20"></a>Предварительные требования. Установка Azure CLI 2.0

Для выполнения действий, описанных в этой статье, требуется [установить интерфейс командной строки Azure для Mac, Linux и Windows (Azure CLI)](https://docs.microsoft.com/en-us/cli/azure/install-az-cli2).

## <a name="waf-configuration-differences"></a>Различия в конфигурации WAF

Если вы прочли статью [Создание шлюза приложений с помощью Azure CLI 2.0](application-gateway-create-gateway-cli.md), то уже понимаете, какие параметры SKU нужно настроить при создании шлюза приложений. При настройке SKU для шлюза приложений доступны дополнительные параметры, относящиеся к WAF. Какие-либо изменения самого шлюза приложений не требуются.

| **Параметр** | **Дополнительные сведения**
|---|---|
|**SKU** |Обычный шлюз приложений без WAF поддерживает размеры **Standard\_Small**, **Standard\_Medium** и **Standard\_Large**. После добавления WAF становятся доступны еще два SKU, **WAF\_Medium** и **WAF\_Large**. WAF не поддерживается в шлюзах приложения уровня "Мелкий".|
|**Режим** | Этот параметр определяет режим WAF. Допустимые значения: **Обнаружение** и **Предотвращение**. Если WAF работает в режиме обнаружения, все угрозы заносятся в файл журнала. В режиме предотвращения события по-прежнему регистрируются в журнале, но злоумышленник получает от шлюза приложений ответ "403 — не авторизовано".|

## <a name="add-web-application-firewall-to-an-existing-application-gateway"></a>Добавление брандмауэра веб-приложения в существующий шлюз приложений

Следующая команда изменяет имеющийся стандартный шлюз приложений на шлюз приложений с поддержкой WAF.

```azurecli-interactive
#!/bin/bash

az network application-gateway waf-config set \
  --enabled true \
  --firewall-mode Prevention \
  --gateway-name "AdatumAppGateway" \
  --resource-group "AdatumAppGatewayRG"
```

Эта команда обновляет шлюз приложений, добавляя в него брандмауэр веб-приложения. Ознакомьтесь с [диагностикой шлюза приложений](application-gateway-diagnostics.md), чтобы понять, как просматривать журналы шлюза приложений. Из-за особенностей системы безопасности WAF необходимо регулярно просматривать журналы, чтобы понимать состояние безопасности веб-приложений.

## <a name="create-an-application-gateway-with-web-application-firewall"></a>Создание шлюза приложений с брандмауэром веб-приложения

Следующая команда создает шлюз приложений с брандмауэром веб-приложения.

```azurecli-interactive
#!/bin/bash

az network application-gateway create \
  --name "AdatumAppGateway2" \
  --location "eastus" \
  --resource-group "AdatumAppGatewayRG" \
  --vnet-name "AdatumAppGatewayVNET2" \
  --vnet-address-prefix "10.0.0.0/16" \
  --subnet "Appgatewaysubnet2" \
  --subnet-address-prefix "10.0.0.0/28" \
 --servers "10.0.0.5 10.0.0.4" \
  --capacity 2 
  --sku "WAF_Medium" \
  --http-settings-cookie-based-affinity "Enabled" \
  --http-settings-protocol "Http" \
  --frontend-port "80" \
  --routing-rule-type "Basic" \
  --http-settings-port "80" \
  --public-ip-address "pip2" \
  --public-ip-address-allocation "dynamic" \
  --tags "cli[2] owner[administrator]"
```

> [!NOTE]
> Для защиты шлюзов приложений, созданных с помощью базовой конфигурации брандмауэра веб-приложения, настраивается CRS 3.0.

## <a name="get-application-gateway-dns-name"></a>Получение DNS-имени шлюза приложений

После создания шлюза следует настроить внешний интерфейс для обмена данными. Если вы используете общедоступный IP-адрес, шлюзу приложений требуется динамически назначаемое непонятное имя DNS. Чтобы гарантировать попадание пользователей на шлюз приложений, можно использовать запись CNAME, чтобы указать общедоступную конечную точку шлюза приложений. [Настройка пользовательского имени домена в Azure](../cloud-services/cloud-services-custom-domain-name-portal.md). Чтобы настроить запись CNAME, получите сведения о шлюзе приложений и соответствующее IP- или DNS-имя с помощью элемента PublicIPAddress, связанного со шлюзом приложений. DNS-имя шлюза приложений должно использоваться для создания записи CNAME, указывающей двум веб-приложениям на это DNS-имя. Использование записи A не рекомендуется, так как виртуальный IP-адрес может измениться после перезапуска приложения шлюза.

```azurecli-interactive
#!/bin/bash

az network public-ip show \
  --name pip2 \
  --resource-group "AdatumAppGatewayRG"
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

Сведения о настройке правил WAF см. в статье [Настройка правил брандмауэра веб-приложения с помощью Azure CLI 2.0](application-gateway-customize-waf-rules-cli.md).

[scenario]: ./media/application-gateway-web-application-firewall-cli/scenario.png


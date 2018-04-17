---
title: Настройка высокодоступных портов для Azure Load Balancer | Документы Майкрософт
description: Сведения о том, как использовать высокодоступные порты для балансировки нагрузки внутреннего трафика на всех портах.
services: load-balancer
documentationcenter: na
author: rdhillon
manager: timlt
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/21/20178
ms.author: kumud
ms.openlocfilehash: 7f7f8e254e0ed0556446e7b08eaf46ec59977f62
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/23/2018
---
# <a name="configure-high-availability-ports-for-an-internal-load-balancer"></a>Настройка высокодоступных портов для внутренней подсистемы балансировки нагрузки

Эта статья содержит пример развертывания высокодоступных портов для внутреннего балансировщика нагрузки. Сведения о конкретных конфигурациях виртуальных сетевых устройств (NVA) см. на веб-сайтах соответствующих поставщиков.

>[!NOTE]
>Azure Load Balancer поддерживает два типа: категории "Базовый" и "Стандартный". В этой статье рассматривается Azure Load Balancer категории "Стандартный". Дополнительные сведения о Load Balancer категории "Базовый" см. в статье [Обзор балансировщика нагрузки Azure](load-balancer-overview.md).

На рисунке приведена конфигурация для примера развертывания, описанного в этой статье:

- Виртуальные сетевые устройства развертываются в серверном пуле внутреннего балансировщика нагрузки с конфигурацией высокодоступных портов. 
- UDR, применяемый в подсети промежуточной сети, направляет весь трафик на виртуальные сетевые устройства, совершая следующий прыжок по виртуальному IP-адресу внутреннего балансировщика нагрузки. 
- Внутренний балансировщик нагрузки распределяет трафик на одно из активных виртуальных сетевых устройств согласно алгоритму балансировки нагрузки.
- Виртуальное сетевое устройство обрабатывает трафик и перенаправляет его исходное расположение в подсети серверной части.
- Обратный путь может использовать тот же маршрут, если в подсети серверной части настроен соответствующий UDR. 

![Пример развертывания высокодоступных портов](./media/load-balancer-configure-ha-ports/haports.png)


## <a name="preview-sign-up"></a>Регистрация в предварительной версии

Чтобы зарегистрироваться в предварительной версии компонента "Порты с высоким уровнем доступности" в Azure Load Balancer уровня "Стандартный", зарегистрируйте свою подписку, чтобы получить доступ, используя Azure CLI 2.0 или PowerShell. Зарегистрируйте подписку для [предварительной версии Load Balancer уровня "Стандартный"](https://aka.ms/lbpreview#preview-sign-up).

>[!NOTE]
>На регистрацию предварительных версий Load Balancer уровня "Стандартный" может уйти до часа.

## <a name="configure-high-availability-ports"></a>Настройка портов высокого уровня доступности

Чтобы настроить порты высокого уровня доступности, настройте внутреннюю подсистему балансировки нагрузки с NVA в пуле серверной части. Настройте соответствующую конфигурацию проверки состояния подсистемы балансировки нагрузки, чтобы определить работоспособность NVA, правило подсистемы балансировки нагрузки и порты высокого уровня доступности. Общая конфигурация для балансировщика нагрузки описана в статье [Начало работы](load-balancer-get-started-ilb-arm-portal.md). Эта статья посвящена конфигурации высокодоступных портов.

Конфигурация включает в себя установку значения **0** для интерфейсного порта и серверного порта. Задайте значение протокола **Все**. Эта статья описывает, как настроить высокодоступные порты с помощью портала Azure, PowerShell и Azure CLI 2.0.

### <a name="configure-a-high-availability-ports-load-balancer-rule-with-the-azure-portal"></a>Настройка правила балансировщика нагрузки с высокодоступными портами с помощью портала Azure

Чтобы настроить порты высокого уровня доступности с помощью портала Azure, установите флажок **Высокодоступные порты**. При его установке соответствующая конфигурация портов и протокола заполняется автоматически. 

![Конфигурация портов высокого уровня доступности с помощью портала Azure](./media/load-balancer-configure-ha-ports/haports-portal.png)


### <a name="configure-a-high-availability-ports-load-balancing-rule-via-the-resource-manager-template"></a>Настройка правила балансировки нагрузки высокодоступных портов с помощью шаблона Resource Manager

Вы можете настроить высокодоступные порты с помощью версии API 2017-08-01 для Microsoft.Network/loadBalancers в ресурсе Load Balancer. В следующем фрагменте кода JSON показаны изменения в конфигурации подсистемы балансировки нагрузки для высокодоступных портов через REST API.

```json
    {
        "apiVersion": "2017-08-01",
        "type": "Microsoft.Network/loadBalancers",
        ...
        "sku":
        {
            "name": "Standard"
        },
        ...
        "properties": {
            "frontendIpConfigurations": [...],
            "backendAddressPools": [...],
            "probes": [...],
            "loadBalancingRules": [
             {
                "properties": {
                    ...
                    "protocol": "All",
                    "frontendPort": 0,
                    "backendPort": 0
                }
             }
            ],
       ...
       }
    }
```

### <a name="configure-a-high-availability-ports-load-balancer-rule-with-powershell"></a>Настройка правила балансировщика нагрузки с высокодоступными портами с помощью PowerShell

Воспользуйтесь следующей командой, чтобы создать правило балансировщика нагрузки с высокодоступными портами при создании внутреннего балансировщика нагрузки с помощью PowerShell:

```powershell
lbrule = New-AzureRmLoadBalancerRuleConfig -Name "HAPortsRule" -FrontendIpConfiguration $frontendIP -BackendAddressPool $beAddressPool -Probe $healthProbe -Protocol "All" -FrontendPort 0 -BackendPort 0
```

### <a name="configure-a-high-availability-ports-load-balancer-rule-with-azure-cli-20"></a>Настройка правила балансировщика нагрузки с высокодоступными портами с помощью Azure CLI 2.0

На шаге 4 процедуры [создания набора внутреннего балансировщика нагрузки](load-balancer-get-started-ilb-arm-cli.md) используйте следующую команду, чтобы создать правило балансировщика нагрузки с высокодоступными портами:

```azurecli
azure network lb rule create --resource-group contoso-rg --lb-name contoso-ilb --name haportsrule --protocol all --frontend-port 0 --backend-port 0 --frontend-ip-name feilb --backend-address-pool-name beilb
```

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о [высокодоступных портах](load-balancer-ha-ports-overview.md).

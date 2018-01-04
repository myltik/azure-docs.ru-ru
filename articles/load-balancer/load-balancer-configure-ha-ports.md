---
title: "Настройка высокодоступных портов для Azure Load Balancer | Документы Майкрософт"
description: "Узнайте, как использовать порты высокой доступности для внутреннего трафика на всех портах балансировки нагрузки"
services: load-balancer
documentationcenter: na
author: rdhillon
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/02/2017
ms.author: kumud
ms.openlocfilehash: 36bc3d7a35f41384706cbc7101457d00848639b2
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/11/2017
---
# <a name="configure-high-availability-ports-for-an-internal-load-balancer"></a>Настройка портов высокой доступности для внутренней подсистемы балансировки нагрузки

Эта статья содержит пример развертывания высокой доступности портов на внутренний балансировщик нагрузки. Дополнительные сведения о конфигурации, сетевых виртуальных устройств (NVAs) см. соответствующий поставщик веб-сайтов.

>[!NOTE]
> Функция порты высокой доступности в настоящее время находится в предварительной версии. Во время предварительного просмотра компонент может имеет тот же уровень доступности и надежности как функциях, которые в общем доступности выпуска. Дополнительные сведения см. в статье [Дополнительные условия использования предварительных выпусков Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

На иллюстрации следующая конфигурация пример развертывания, описанные в этой статье.

- NVAs развертываются в пуле внутренней внутренний балансировщик нагрузки конфигурации портов высокой доступности. 
- Маршрут (UDR) определяемого пользователем применять маршрутов подсети DMZ весь трафик к NVAs, делая следующего прыжка как внутренний виртуальный IP-адрес подсистемы балансировки нагрузки. 
- Внутренний балансировщик нагрузки распределяет трафик к одной из активных NVAs по алгоритму подсистемы балансировки нагрузки.
- Оценку Уязвимости обрабатывает трафик и перенаправляет его исходное расположение в конечной подсети.
- Если соответствующий UDR настраивается в конечной подсети обратный путь может занять тот же маршрут. 

![Пример развертывания порты высокой доступности](./media/load-balancer-configure-ha-ports/haports.png)


## <a name="preview-sign-up"></a>Регистрация в предварительной версии

Чтобы принять участие в предварительной версии функции высокой доступности порты в стандартных подсистемы балансировки нагрузки Azure, зарегистрируйте подписку для получения доступа с помощью Azure CLI 2.0 или PowerShell. Зарегистрировать подписку для [Предварительная версия подсистемы балансировки нагрузки Standard](https://aka.ms/lbpreview#preview-sign-up).

>[!NOTE]
>Регистрация стандартных Preview подсистемы балансировки нагрузки может занять до одного часа.

## <a name="configure-high-availability-ports"></a>Настройка портов высокой доступности

Чтобы настроить порты высокой доступности, настройте внутренний балансировщик нагрузки с NVAs в пуле серверной части. Настройте соответствующие конфигурации подсистемы балансировки нагрузки работоспособности проверки для определения работоспособности Уязвимости и правила подсистемы балансировки нагрузки с портами высокой доступности. Охваченных конфигурации связанные с подсистемой балансировки нагрузки Общие [начать](load-balancer-get-started-ilb-arm-portal.md). В этой статье указаны конфигурации портов высокой доступности.

Конфигурация фактически включает в себя параметр интерфейсный порт и номер порта серверной части для **0**. Задайте значение протокола **все**. В этой статье описываются способы настройки портов высокой доступности с помощью портала Azure, PowerShell и Azure CLI 2.0.

### <a name="configure-a-high-availability-ports-load-balancer-rule-with-the-azure-portal"></a>Настройка правила подсистемы балансировки нагрузки порты высокой доступности с помощью портала Azure

Чтобы настроить порты высокой доступности с помощью портала Azure, выберите **HA порты** флажок. При его установке соответствующая конфигурация портов и протокола заполняется автоматически. 

![Конфигурация портов высокой доступности через портал Azure](./media/load-balancer-configure-ha-ports/haports-portal.png)


### <a name="configure-a-high-availability-ports-load-balancing-rule-via-the-resource-manager-template"></a>Настройка правила балансировки нагрузки высокой доступности портов через шаблона диспетчера ресурсов

Можно настроить порты высокой доступности с помощью версии API 2017 г-08-01 для Microsoft.Network/loadBalancers в ресурсе подсистемы балансировки нагрузки. В следующем фрагменте JSON показан изменения в конфигурации подсистемы балансировки нагрузки для высокой доступности портов через REST API:

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

### <a name="configure-a-high-availability-ports-load-balancer-rule-with-powershell"></a>Настройка правила подсистемы балансировки нагрузки порты высокой доступности с помощью PowerShell

Для создания правила подсистемы балансировки нагрузки высокой доступности портов при создании внутренней подсистемы балансировки нагрузки с помощью PowerShell, используйте следующую команду:

```powershell
lbrule = New-AzureRmLoadBalancerRuleConfig -Name "HAPortsRule" -FrontendIpConfiguration $frontendIP -BackendAddressPool $beAddressPool -Probe $healthProbe -Protocol "All" -FrontendPort 0 -BackendPort 0
```

### <a name="configure-a-high-availability-ports-load-balancer-rule-with-azure-cli-20"></a>Настройка правила подсистемы балансировки нагрузки порты высокой доступности с помощью Azure CLI 2.0

На шаге 4 [Создание комплекта балансировки нагрузки внутренней](load-balancer-get-started-ilb-arm-cli.md), используйте следующую команду для создания правила подсистемы балансировки нагрузки высокой доступности порты:

```azurecli
azure network lb rule create --resource-group contoso-rg --lb-name contoso-ilb --name haportsrule --protocol all --frontend-port 0 --backend-port 0 --frontend-ip-name feilb --backend-address-pool-name beilb
```

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о [высокой доступности порты](load-balancer-ha-ports-overview.md).

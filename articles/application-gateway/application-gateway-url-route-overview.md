---
title: Общие сведения о маршрутизации содержимого на основе URL-адресов | Документация Майкрософт
description: Эта страница содержит общие сведения о маршрутизации содержимого шлюза приложений на основе URL-адресов, настройки UrlPathMap и правила PathBasedRouting.
documentationcenter: na
services: application-gateway
author: vhorne
manager: jpconnock
editor: ''
ms.assetid: 4409159b-e22d-4c9a-a103-f5d32465d163
ms.service: application-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/09/2017
ms.author: victorh
ms.openlocfilehash: f6108b5ac628b8bc2c1d74dcc871f96115094859
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/03/2018
ms.locfileid: "32770344"
---
# <a name="url-path-based-routing-overview"></a>Общие сведения о маршрутизации на основе URL-путей

Маршрутизация на основе URL-путей позволяет направлять трафик в пулы тыловых серверов в зависимости от URL-путей поступающих запросов. 

Один из сценариев — это маршрутизация запросов содержимого различных типов в различные пулы тыловых серверов.

В следующем примере шлюз приложений обслуживает трафик веб-сайта contoso.com с трех пулов тыловых серверов, например VideoServerPool, ImageServerPool и DefaultServerPool.

![imageURLroute](./media/application-gateway-url-route-overview/figure1.png)

Запросы для http://contoso.com/video/* направляются в VideoServerPool, а запросы для http://contoso.com/images/* — в ImageServerPool. Если ни один из шаблонов пути не подходит, выбирается пул DefaultServerPool.

> [!IMPORTANT]
> Правила обрабатываются в том порядке, в котором они указаны на портале. Мы настоятельно рекомендуем в первую очередь настроить многосайтовые прослушиватели, чтобы настроить базовый прослушиватель.  Это гарантирует, что трафик будет перенаправляться на правильный внутренний сервер. Если базовый прослушиватель стоит первым в списке и совпадает с входящим запросом, он будет обрабатываться прослушивателем.

## <a name="urlpathmap-configuration-element"></a>Элемент конфигурации UrlPathMap

Элемент urlPathMap используется для определения шаблонов пути при сопоставлении с пулом тыловых серверов. Ниже приведен пример кода, который является фрагментом элемента urlPathMap из файла шаблона.

```json
"urlPathMaps": [{
    "name": "{urlpathMapName}",
    "id": "/subscriptions/{subscriptionId}/../microsoft.network/applicationGateways/{gatewayName}/urlPathMaps/{urlpathMapName}",
    "properties": {
        "defaultBackendAddressPool": {
            "id": "/subscriptions/    {subscriptionId}/../microsoft.network/applicationGateways/{gatewayName}/backendAddressPools/{poolName1}"
        },
        "defaultBackendHttpSettings": {
            "id": "/subscriptions/{subscriptionId}/../microsoft.network/applicationGateways/{gatewayName}/backendHttpSettingsList/{settingname1}"
        },
        "pathRules": [{
            "name": "{pathRuleName}",
            "properties": {
                "paths": [
                    "{pathPattern}"
                ],
                "backendAddressPool": {
                    "id": "/subscriptions/{subscriptionId}/../microsoft.network/applicationGateways/{gatewayName}/backendAddressPools/{poolName2}"
                },
                "backendHttpsettings": {
                    "id": "/subscriptions/{subscriptionId}/../microsoft.network/applicationGateways/{gatewayName}/backendHttpsettingsList/{settingName2}"
                }
            }
        }]
    }
}]
```

> [!NOTE]
> Параметр PathPattern представляет список шаблонов пути для сопоставления. Каждый шаблон должен начинаться с косой черты (/). Символ * может быть только в конце после косой черты. Строка, передаваемая для сопоставления пути, не должна содержать никакого текста после первого символа ? или # — эти символы не допускаются.

Дополнительные сведения см. в статье [Resource Manager template using URL-based routing](https://azure.microsoft.com/documentation/templates/201-application-gateway-url-path-based-routing) (Шаблон Resource Manager с использованием маршрутизации на основе URL-адресов).

## <a name="pathbasedrouting-rule"></a>Правило PathBasedRouting

Правило RequestRoutingRule типа PathBasedRouting используется для привязки прослушивателя к urlPathMap. Все получаемые запросы для этого прослушивателя распределяются согласно политике, указанной в urlPathMap.
Фрагмент правила PathBasedRouting:

```json
"requestRoutingRules": [
    {

"name": "{ruleName}",
"id": "/subscriptions/{subscriptionId}/../microsoft.network/applicationGateways/{gatewayName}/requestRoutingRules/{ruleName}",
"properties": {
    "ruleType": "PathBasedRouting",
    "httpListener": {
        "id": "/subscriptions/{subscriptionId}/../microsoft.network/applicationGateways/{gatewayName}/httpListeners/<listenerName>"
    },
    "urlPathMap": {
        "id": "/subscriptions/{subscriptionId}/../microsoft.network/applicationGateways/{gatewayName}/ urlPathMaps/{urlpathMapName}"
    },

}
    }
]
```

## <a name="next-steps"></a>Дополнительная информация

Ознакомившись с маршрутизацией на основе URL-адресов, создайте шлюз приложений с соответствующими правилами маршрутизации, как указано в статье о [создании шлюза приложений с помощью маршрутизации на основе URL-адресов](application-gateway-create-url-route-portal.md) .

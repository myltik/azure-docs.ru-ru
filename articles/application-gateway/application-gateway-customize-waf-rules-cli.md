---
title: "Настройка правил брандмауэра веб-приложения для шлюза приложений Azure с помощью Azure CLI 2.0 | Документация Майкрософт"
description: "Эта статья содержит сведения о том, как настроить правила брандмауэра веб-приложения в шлюзе приложений с помощью Azure CLI 2.0."
documentationcenter: na
services: application-gateway
author: georgewallace
manager: timlt
editor: tysonn
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.custom: 
ms.workload: infrastructure-services
ms.date: 07/26/2017
ms.author: gwallace
ms.translationtype: HT
ms.sourcegitcommit: 8b857b4a629618d84f66da28d46f79c2b74171df
ms.openlocfilehash: cfc01caf5c8ca11f790a6640ec57e3d14c1b7767
ms.contentlocale: ru-ru
ms.lasthandoff: 08/04/2017

---

# <a name="customize-web-application-firewall-rules-through-the-azure-cli-20"></a>Настройка правил брандмауэра веб-приложения с помощью Azure CLI 2.0

> [!div class="op_single_selector"]
> * [Портал Azure](application-gateway-customize-waf-rules-portal.md)
> * [PowerShell](application-gateway-customize-waf-rules-powershell.md)
> * [Azure CLI 2.0](application-gateway-customize-waf-rules-cli.md)

Брандмауэр веб-приложения шлюза приложений обеспечивает защиту веб-приложений с помощью наборов правил OWASP CRS. Некоторые правила могут приводить к ложным срабатываниям и блокировке реального трафика.  Поэтому в шлюзе приложений можно настраивать правила и группы правил в шлюзе приложений с включенным брандмауэром веб-приложений. Дополнительные сведения о конкретных правилах и группах правил см. в статье [List of web application firewall CRS rule groups and rules offered](application-gateway-crs-rulegroups-rules.md) (Список правил и групп правил CRS для брандмауэра веб-приложения).

## <a name="view-rule-groups-and-rules"></a>Просмотр правил и групп правил

Ниже приведены примеры просмотра правил и групп правил, которые можно настроить в шлюзе приложений с включенным WAF.

### <a name="view-rule-groups"></a>Просмотр групп правил

```azurecli-interactive
az network application-gateway waf-config list-rule-sets --type OWASP
```

Далее представлен сокращенный ответ из предыдущего примера.

```
[
  {
    "id": "/subscriptions//resourceGroups//providers/Microsoft.Network/applicationGatewayAvailableWafRuleSets/",
    "location": null,
    "name": "OWASP_3.0",
    "provisioningState": "Succeeded",
    "resourceGroup": "",
    "ruleGroups": [
      {
        "description": "",
        "ruleGroupName": "REQUEST-910-IP-REPUTATION",
        "rules": null
      },
      ...
    ],
    "ruleSetType": "OWASP",
    "ruleSetVersion": "3.0",
    "tags": null,
    "type": "Microsoft.Network/applicationGatewayAvailableWafRuleSets"
  },
  {
    "id": "/subscriptions//resourceGroups//providers/Microsoft.Network/applicationGatewayAvailableWafRuleSets/",
    "location": null,
    "name": "OWASP_2.2.9",
    "provisioningState": "Succeeded",
    "resourceGroup": "",
   "ruleGroups": [
      {
        "description": "",
        "ruleGroupName": "crs_20_protocol_violations",
        "rules": null
      },
      ...
    ],
    "ruleSetType": "OWASP",
    "ruleSetVersion": "2.2.9",
    "tags": null,
    "type": "Microsoft.Network/applicationGatewayAvailableWafRuleSets"
  }
]
```

### <a name="view-rules-in-a-rule-group"></a>Просмотр правил в группе правил

В примере ниже показано, как просмотреть правила в указанной группе правил.

```azurecli-interactive
az network application-gateway waf-config list-rule-sets --group "REQUEST-910-IP-REPUTATION"
```

В результатах далее представлен сокращенный ответ из предыдущего примера.

```
[
  {
    "id": "/subscriptions//resourceGroups//providers/Microsoft.Network/applicationGatewayAvailableWafRuleSets/",
    "location": null,
    "name": "OWASP_3.0",
    "provisioningState": "Succeeded",
    "resourceGroup": "",
    "ruleGroups": [
      {
        "description": "",
        "ruleGroupName": "REQUEST-910-IP-REPUTATION",
        "rules": [
          {
            "description": "Rule 910011",
            "ruleId": 910011
          },
          ...
        ]
      }
    ],
    "ruleSetType": "OWASP",
    "ruleSetVersion": "3.0",
    "tags": null,
    "type": "Microsoft.Network/applicationGatewayAvailableWafRuleSets"
  }
]
```

## <a name="disable-rules"></a>Отключение правил

В следующем примере отключаются правила `910018` и `910017` в шлюзе приложений.

```azurecli-interactive
az network application-gateway waf-config set --resource-group AdatumAppGatewayRG --gateway-name AdatumAppGateway --enabled true --rule-set-version 3.0 --disabled-rules 910018 910017
```

## <a name="next-steps"></a>Дальнейшие действия

После настройки с отключением правил узнайте, как просматривать журналы WAF, ознакомившись с разделом [Ведение журнала диагностики](application-gateway-diagnostics.md#diagnostic-logging).

[fig1]: ./media/application-gateway-customize-waf-rules-portal/1.png
[1]: ./media/application-gateway-customize-waf-rules-portal/figure1.png
[2]: ./media/application-gateway-customize-waf-rules-portal/figure2.png
[3]: ./media/application-gateway-customize-waf-rules-portal/figure3.png

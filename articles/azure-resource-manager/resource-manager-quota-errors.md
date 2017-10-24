---
title: "Ошибки квоты Azure | Документация Майкрософт"
description: "Описывается, как устранить ошибки квоты ресурсов."
services: azure-resource-manager,azure-portal
documentationcenter: 
author: tfitzmac
manager: timlt
editor: 
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: support-article
ms.date: 09/13/2017
ms.author: tomfitz
ms.openlocfilehash: 6a8024a12c4a79e92e37df0a56b6e6bd0cb6a8d9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="resolve-errors-for-resource-quotas"></a>Устранение ошибок квот ресурсов

В этой статье описываются ошибки квоты, которые могут возникнуть при развертывании ресурсов.

## <a name="symptom"></a>Симптом

При развертывании шаблона, создающего ресурсы, превышающие ваши квоты Azure, возникнет ошибка развертывания следующего вида.

```
Code=OperationNotAllowed
Message=Operation results in exceeding quota limits of Core.
Maximum allowed: 4, Current in use: 4, Additional requested: 2.
```

Вы можете также увидеть следующую ошибку.

```
Code=ResourceQuotaExceeded
Message=Creating the resource of type <resource-type> would exceed the quota of <number>
resources of type <resource-type> per resource group. The current resource count is <number>,
please delete some resources of this type before creating a new one.
```

## <a name="cause"></a>Причина:

Квоты применяются к группам ресурсов, подпискам, учетным записям и другим областям. Например, для подписки может быть настроено ограничение числа ядер для региона. При попытке развертывания виртуальной машины с большим количеством ядер, чем разрешено, вы получите сообщение о том, что квота превышена.
Дополнительные сведения о квотах Azure см. в статье [Подписка Azure, границы, квоты и ограничения службы](../azure-subscription-service-limits.md).

## <a name="solution"></a>Решение

### <a name="solution-1"></a>Решение 1

Чтобы узнать квоты виртуальной машины, выполните команду `az vm list-usage` в Azure CLI.

```azurecli
az vm list-usage --location "South Central US"
```

Возвращаемые данные:

```azurecli
[
  {
    "currentValue": 0,
    "limit": 2000,
    "name": {
      "localizedValue": "Availability Sets",
      "value": "availabilitySets"
    }
  },
  ...
]
```

### <a name="solution-2"></a>Решение 2

Чтобы узнать квоты виртуальной машины, выполните команду **Get-AzureRmVMUsage** в PowerShell.

```powershell
Get-AzureRmVMUsage -Location "South Central US"
```

Возвращаемые данные:

```powershell
Name                             Current Value Limit  Unit
----                             ------------- -----  ----
Availability Sets                            0  2000 Count
Total Regional Cores                         0   100 Count
Virtual Machines                             0 10000 Count
```

### <a name="solution-3"></a>Решение 3

Если необходимо повысить квоту, перейдите на портал и зарегистрируйте проблему в службе поддержки, чтобы поднять свою квоту для региона, в котором требуется осуществить развертывание.

> [!NOTE]
> Следует помнить, что для групп ресурсов квоты устанавливаются для каждого отдельного региона, а не для всей подписки. Если необходимо развернуть 30 ядер в западной части США, необходимо запросить 30 ядер управления ресурсами в этом регионе. Если необходимо развернуть 30 ядер в любом из регионов, к которым у вас есть доступ, следует запросить 30 ядер Resource Manager во всех регионах.
>
>
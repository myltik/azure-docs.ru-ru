---
title: "Подписки на Azure события сетки с помощью шаблона"
description: "Создайте подписку на события сетки с помощью шаблона диспетчера ресурсов."
services: event-grid
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.topic: article
ms.date: 01/05/2018
ms.author: tomfitz
ms.openlocfilehash: 2b9f55f8e944d688b622e30a773e1a34698f22ec
ms.sourcegitcommit: 1d423a8954731b0f318240f2fa0262934ff04bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/05/2018
---
# <a name="use-resource-manager-template-for-event-grid-subscription"></a>Шаблон с помощью диспетчера ресурсов для подписки на события сетки

В этой статье показано, как использовать шаблон диспетчера ресурсов Azure для создания подписки на события сетки. Формат, который можно использовать зависит от ли подписке событий группы ресурсов или событий, для определенного типа ресурсов. В этой статье показаны оба формата.

## <a name="subscribe-to-resource-group-events"></a>Подписаться на события группы ресурсов

При подписке на события группы ресурсов, используйте `Microsoft.EventGrid/eventSubscriptions` для типа ресурса. Тип точки для события, используйте либо `WebHook` или `EventHub`.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "resources": [
        {
            "type": "Microsoft.EventGrid/eventSubscriptions",
            "name": "mySubscription",
            "apiVersion": "2017-09-15-preview",
            "properties": {
                "destination": {
                    "endpointType": "WebHook",
                    "properties": {
                        "endpointUrl": "https://requestb.in/ynboxiyn"
                    }
                },
                "filter": {
                    "subjectBeginsWith": "",
                    "subjectEndsWith": "",
                    "isSubjectCaseSensitive": false,
                    "includedEventTypes": ["All"]
                }
            }
        }
    ]
}
```

При развертывании этого шаблона в группу ресурсов, можно подписаться на события для данной группы ресурсов.

## <a name="subscribe-to-resource-events"></a>Подписаться на события ресурсов

При подписке на события ресурсов, нужно связать подписку на соответствующий ресурс, включая имя и тип ресурса в определении подписки. Тип ресурса, используйте `<provider-namespace>/<resource-type>/providers/eventSubscriptions`. Имя, используйте `<resource-name>/Microsoft.EventGrid/<subscription-name>`. Тип точки для события, используйте либо `WebHook` или `EventHub`.

Приведенный ниже показано, как подписаться на события хранилища больших двоичных объектов.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storageName": {
            "type": "string"
        }
    },
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts/providers/eventSubscriptions",
            "name": "[concat(parameters('storageName'), '/Microsoft.EventGrid/myStorageSubscription')]",
            "apiVersion": "2017-09-15-preview",
            "properties": {
                "destination": {
                    "endpointType": "WebHook",
                    "properties": {
                        "endpointUrl": "https://requestb.in/ynboxiyn"
                    }
                },
                "filter": {
                    "subjectBeginsWith": "",
                    "subjectEndsWith": "",
                    "isSubjectCaseSensitive": false
                }
            }
        }
    ]
}
```

## <a name="next-steps"></a>Дальнейшие действия

* Общие сведения о службе "Сетка событий" см. в разделе [Общие сведения о службе "Сетка событий Azure"](overview.md).
* Введение в диспетчер ресурсов см. в разделе [Обзор диспетчера ресурсов Azure](../azure-resource-manager/resource-group-overview.md).
* Сведения о том, как приступить к использованию службы "Сетка событий", см. в статье [Создание и перенаправление пользовательских событий с помощью Azure CLI и службы "Сетка событий"](custom-event-quickstart.md).
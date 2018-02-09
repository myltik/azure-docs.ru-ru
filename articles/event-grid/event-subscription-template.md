---
title: "Создание подписки в службе \"Сетка событий Azure\" с помощью шаблона"
description: "Создайте подписку в службе \"Сетка событий\" с помощью шаблона Resource Manager."
services: event-grid
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.topic: article
ms.date: 01/30/2018
ms.author: tomfitz
ms.openlocfilehash: ee0b2c228ae4ea53c0ee9794529aa190334ceed9
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/01/2018
---
# <a name="use-resource-manager-template-for-event-grid-subscription"></a>Создание подписки в службе "Сетка событий Azure" с помощью шаблона Resource Manager

В этой статье показано, как с помощью шаблона Azure Resource Manager создавать подписки в службе "Сетка событий". Используемый формат отличается в зависимости от того, подписываетесь вы на события группы ресурсов или на события для определенного типа ресурса. В этой статье описаны оба формата.

## <a name="subscribe-to-resource-group-events"></a>Подписка на события группы ресурсов

При подписке на события группы ресурсов используйте тип ресурсов `Microsoft.EventGrid/eventSubscriptions`. Для типа точки события используйте `WebHook` или `EventHub`.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "resources": [
        {
            "type": "Microsoft.EventGrid/eventSubscriptions",
            "name": "mySubscription",
            "apiVersion": "2018-01-01",
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

При развертывании этого шаблона в группе ресурсов вы подписываетесь на события для этой группы ресурсов.

## <a name="subscribe-to-resource-events"></a>Подписка на события ресурса

При подписке на события ресурса нужно связать подписку с соответствующим ресурсом, добавив имя и тип ресурса в определение подписки. В качестве типа ресурса используйте `<provider-namespace>/<resource-type>/providers/eventSubscriptions`. В качестве имени — `<resource-name>/Microsoft.EventGrid/<subscription-name>`. Для типа точки события используйте `WebHook` или `EventHub`.

В приведенном ниже примере показано, как подписаться на события хранилища BLOB-объектов.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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
            "apiVersion": "2018-01-01",
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

## <a name="next-steps"></a>Дополнительная информация

* Общие сведения о службе "Сетка событий" см. в разделе [Общие сведения о службе "Сетка событий Azure"](overview.md).
* Общие сведения о Resource Manager см. в [этой статье](../azure-resource-manager/resource-group-overview.md).
* Сведения о том, как приступить к использованию службы "Сетка событий", см. в статье [Создание и перенаправление пользовательских событий с помощью Azure CLI и службы "Сетка событий"](custom-event-quickstart.md).
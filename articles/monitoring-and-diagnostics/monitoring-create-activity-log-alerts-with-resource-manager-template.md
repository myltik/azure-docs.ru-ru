---
title: "Создание оповещения журнала действий с помощью шаблона Resource Manager | Документация Майкрософт"
description: "Получение уведомлений при создании ресурсов в Azure."
author: anirudhcavale
manager: orenr
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/06/2017
ms.author: ancav
ms.openlocfilehash: b30912c44bd66f8c6fca548dc905f750e05c8621
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/09/2018
---
# <a name="create-an-activity-log-alert-with-a-resource-manager-template"></a>Создание оповещения журнала действий с помощью шаблона Resource Manager
В этой статье показано, как можно использовать [шаблон Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates) для настройки оповещений журнала действий. С помощью шаблонов можно легко настроить большое количество предупреждений, которые активируются по определенным условиям события журнала действий в рамках автоматического развертывания.

Основными шагами являются:

1. Создайте шаблон в виде JSON-файла, который описывает создание оповещения журнала действий.

2. Разверните шаблон, используя [любой метод развертывания](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy).

## <a name="resource-manager-template-for-an-activity-log-alert"></a>Шаблон Resource Manager для оповещения журнала действий
Чтобы создать оповещение журнала действий с помощью шаблона Resource Manager, создайте ресурс типа `microsoft.insights/activityLogAlerts`. Затем следует заполнить все связанные свойства. Вот шаблон, создающий оповещение журнала действий.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "activityLogAlertName": {
      "type": "string",
      "metadata": {
        "description": "Unique name (within the Resource Group) for the Activity log alert."
      }
    },
    "activityLogAlertEnabled": {
      "type": "bool",
      "defaultValue": true,
      "metadata": {
        "description": "Indicates whether or not the alert is enabled."
      }
    },
    "actionGroupResourceId": {
      "type": "string",
      "metadata": {
        "description": "Resource Id for the Action group."
      }
    }
  },
  "resources": [   
    {
      "type": "Microsoft.Insights/activityLogAlerts",
      "apiVersion": "2017-04-01",
      "name": "[parameters('activityLogAlertName')]",      
      "location": "Global",
      "properties": {
        "enabled": "[parameters('activityLogAlertEnabled')]",
        "scopes": [
            "[subscription().id]"
        ],        
        "condition": {
          "allOf": [
            {
              "field": "category",
              "equals": "Administrative"
            },
            {
              "field": "operationName",
              "equals": "Microsoft.Resources/deployments/write"
            },
            {
              "field": "resourceType",
              "equals": "Microsoft.Resources/deployments"
            }
          ]
        },
        "actions": {
          "actionGroups":
          [
            {
              "actionGroupId": "[parameters('actionGroupResourceId')]"
            }
          ]
        }
      }
    }
  ]
}
```

Примеры шаблонов для оповещений журнала действий см. в [коллекции шаблонов для быстрого начала работы с Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Insights).

> [!NOTE]

> Можно также воспользоваться дополнительными возможностями для создания правил генерации оповещений журнала действий, щелкнув колонку "Монитор" > [Оповещения (предварительная версия)](monitoring-overview-unified-alerts.md). Дополнительные сведения о создании таких правил см. в [этой статье](monitoring-activity-log-alerts-new-experience.md).

## <a name="next-steps"></a>Дополнительная информация
- Узнайте больше об [оповещениях](monitoring-overview-alerts.md).
- Узнайте, как добавить [группы действий с помощью шаблона Resource Manager](monitoring-create-action-group-with-resource-manager-template.md).
- Узнайте, как [создать оповещение журнала действий, чтобы отслеживать все операции системы автомасштабирования в своей подписке](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-alert).
- Узнайте, как [создать оповещение журнала действий, чтобы отслеживать все ошибки автомасштабирования в своей подписке](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-failed-alert).

---
title: "Создание оповещения журнала действий с помощью шаблона Resource Manager | Документация Майкрософт"
description: "Получение уведомлений при создании ресурсов в Azure."
author: anirudhcavale
manager: carmonm
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/31/2017
ms.author: ancav
translationtype: Human Translation
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: 239b8fe2a7724bb34e7060c90af73825e61d8398
ms.lasthandoff: 04/12/2017


---
# <a name="create-an-activity-log-alert-with-a-resource-manager-template"></a>Создание оповещения журнала действий с помощью шаблона Resource Manager
В этой статье показано, как можно использовать [шаблон Azure Resource Manager](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-authoring-templates) для настройки оповещений журнала действий. Это позволяет автоматически настраивать оповещения для ресурсов при их создании в ходе автоматизированного развертывания.

Основные этапы:

1.    Создайте шаблон в виде JSON-файла, который описывает создание оповещения журнала действий.
2.    [Разверните шаблон с помощью любого метода развертывания](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy).

Ниже описано, как сначала создать шаблон Resource Manager только для оповещения журнала действий, а затем — как это сделать во время создания другого ресурса.

## <a name="resource-manager-template-for-an-activity-log-alert"></a>Шаблон Resource Manager для оповещения журнала действий
Чтобы создать оповещение журнала действий с помощью шаблона Resource Manager, создайте ресурс типа `microsoft.insights/activityLogAlerts` и заполните все связанные свойства. Ниже приведен шаблон, который создает оповещение журнала действий.

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
      "apiVersion": "2017-03-01-preview",
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

## <a name="next-steps"></a>Дальнейшие действия
Узнайте больше об [оповещениях](monitoring-overview-alerts.md).  
Узнайте, как добавить [группы действий с помощью шаблона Resource Manager](monitoring-create-action-group-with-resource-manager-template.md).


---
title: "Политики ресурсов Azure для тегов | Документация Майкрософт"
description: "Статья содержит примеры политик ресурсов для управления тегами ресурсов."
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/30/2017
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: 197ebd6e37066cb4463d540284ec3f3b074d95e1
ms.openlocfilehash: 6e71fd9eda822478fa0555aa44908a4094fe8de2
ms.lasthandoff: 03/31/2017


---
# <a name="apply-resource-policies-for-tags"></a>Применение политик ресурсов для тегов

Этот раздел содержит общие правила политики, которые можно применять, чтобы обеспечить согласованное использование тегов для ресурсов.

Применение политики тегов к группе ресурсов или подпискам с существующими ресурсами не позволяет применить ее к этим ресурсам задним числом. Чтобы применить политику к этим ресурсам, активируйте обновление существующих ресурсов, как показано в разделе [Активация обновления существующих ресурсов](#trigger-updates-to-existing-resources).

## <a name="ensure-all-resources-in-a-resource-group-have-a-tagvalue"></a>Обеспечение всех ресурсов в группе ресурсов значением или тегом

Общим требованием является наличие определенного тега и значения у всех ресурсов в группе ресурсов. Это требование обычно необходимо для отслеживания затрат по отделу. Должны быть выполнены следующие условия.

* Обязательные тег и значение добавляются к новым и обновленным ресурсам, которые не имеют тегов.
* Обязательные тег и значение добавляются к новым и обновленным ресурсам, имеющим другие теги (но не имеющим обязательные тег и значение).
* Обязательные тег и значение невозможно удалить из каких-либо существующих ресурсов.

Это требование можно выполнить, применив к группе ресурсов три следующие политики:

* [добавление тега](#append-tag); 
* [добавление тега к другим тегам](#append-tag-with-other-tags);
* [требование тега и значения](#require-tag-and-value).

### <a name="append-tag"></a>Добавление тега

Следующее правило политики добавляет тег costCenter с предопределенным значением, если теги отсутствуют.

```json
{
  "if": {
    "field": "tags",
    "exists": "false"
  },
  "then": {
    "effect": "append",
    "details": [
      {
        "field": "tags",
        "value": {"costCenter":"myDepartment" }
      }
    ]
  }
}
```

### <a name="append-tag-with-other-tags"></a>Добавление тега к другим тегам

Следующее правило политики добавляет тег costCenter с предопределенным значением при отсутствии этого тега и наличии других тегов.

```json
{
  "if": {
    "allOf": [
      {
        "field": "tags",
        "exists": "true"
      },
      {
        "field": "tags.costCenter",
        "exists": "false"
      }
    ]
  },
  "then": {
    "effect": "append",
    "details": [
      {
        "field": "tags.costCenter",
        "value": "myDepartment"
      }
    ]
  }
}
```

### <a name="require-tag-and-value"></a>Требование тега и значения

Следующее правило политики запрещает обновление или создание ресурсов, которым не назначен тег costCenter с предопределенным значением.

```json
{
  "if": {
    "not": {
      "field": "tags.costCenter",
      "equals": "myDepartment"
    }
  },
  "then": {
    "effect": "deny"
  }
}
```

## <a name="require-tags-for-a-resource-type"></a>Требование тегов для типа ресурса
В следующем примере показано, как вложить логические операторы, согласно которым приложение будет помечать тегами только ресурсы определенного типа (в этом случае это учетные записи хранения).

```json
{
    "if": {
        "allOf": [
          {
            "not": {
              "field": "tags",
              "containsKey": "application"
            }
          },
          {
            "field": "type",
            "equals": "Microsoft.Storage/storageAccounts"
          }
        ]
    },
    "then": {
        "effect": "audit"
    }
}
```

## <a name="require-tag"></a>Требование тега
Следующая политика отклоняет запросы без тега с ключом costCenter (может иметь любое значение).

```json
{
  "if": {
    "not" : {
      "field" : "tags",
      "containsKey" : "costCenter"
    }
  },
  "then" : {
    "effect" : "deny"
  }
}
```

## <a name="trigger-updates-to-existing-resources"></a>Активация обновления существующих ресурсов

Следующий сценарий PowerShell активирует обновление существующих ресурсов для применения политик тегов, добавленных вами.

```powershell
$group = Get-AzureRmResourceGroup -Name "ExampleGroup" 

$resources = Find-AzureRmResource -ResourceGroupName $group.ResourceGroupName 

foreach($r in $resources)
{
    try{
        $r | Set-AzureRmResource -Tags ($a=if($_.Tags -eq $NULL) { @{}} else {$_.Tags}) -Force -UsePatchSemantics
    }
    catch{
        Write-Host  $r.ResourceId + "can't be updated"
    }
}
```

## <a name="next-steps"></a>Дальнейшие действия
* После определения правила политики (как показано в приведенных выше примерах) необходимо создать определение политики и назначить ей область. Областью может быть подписка, группа ресурсов или ресурс. Сведения о назначении политик с помощью портала см. в статье [Назначение политик ресурсов и управление ими с помощью портала Azure](resource-manager-policy-portal.md). Сведения о назначении политик с помощью REST API, PowerShell или Azure CLI см. в статье [Назначение политик ресурсов и управление ими](resource-manager-policy-create-assign.md).
* Введение в политики ресурсов представлено в разделе [Общие сведения о политике ресурсов](resource-manager-policy.md).
* Руководство по использованию Resource Manager для эффективного управления подписками в организациях см [Azure enterprise scaffold - prescriptive subscription governance](resource-manager-subscription-governance.md) (Шаблон Azure для организаций. Рекомендуемая система управления подпиской).



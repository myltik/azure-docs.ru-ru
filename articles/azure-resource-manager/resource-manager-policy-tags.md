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
ms.date: 04/20/2017
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: 2c33e75a7d2cb28f8dc6b314e663a530b7b7fdb4
ms.openlocfilehash: 04338b62d942774368149b27e8b35713b77f8d7c
ms.lasthandoff: 04/21/2017


---
# <a name="apply-resource-policies-for-tags"></a>Применение политик ресурсов для тегов

Этот раздел содержит общие правила политики, которые можно применять, чтобы обеспечить согласованное использование тегов для ресурсов.

Применение политики тегов к группе ресурсов или подпискам с существующими ресурсами не позволяет применить ее к этим ресурсам задним числом. Чтобы применить политику к этим ресурсам, активируйте обновление существующих ресурсов, как показано в разделе [Активация обновления существующих ресурсов](#trigger-updates-to-existing-resources).

## <a name="ensure-all-resources-in-a-resource-group-have-a-tagvalue"></a>Обеспечение всех ресурсов в группе ресурсов значением или тегом

Общим требованием является наличие определенного тега и значения у всех ресурсов в группе ресурсов. Это требование обычно необходимо для отслеживания затрат по отделу. Должны быть выполнены следующие условия.

* Обязательные тег и значение добавляются к новым и обновленным ресурсам, у которых нет тега.
* Обязательные тег и значение невозможно удалить из каких-либо существующих ресурсов.

Это требование можно выполнить, применив к группе ресурсов две встроенные политики.

| ИД | Описание |
| ---- | ---- |
| 2a0e14a6-b0a6-4fab-991a-187a4f81c498 | Применяет обязательный тег и его значение по умолчанию, если его не задал пользователь. |
| 1e30110a-5ceb-460c-a204-c1c3969c6d62 | Принудительно применяет обязательный тег и его значение. |

### <a name="powershell"></a>PowerShell

Следующий сценарий PowerShell назначает группе ресурсов два определения встроенных политик. Перед запуском сценария назначьте группе ресурсов все необходимые теги. Каждый тег в группе ресурсов нужен для содержащегося в ней ресурса. Чтобы охватить все группы ресурсов в подписке, не указывайте параметр `-Name` при получении групп ресурсов.

```powershell
$appendpolicy = Get-AzureRmPolicyDefinition | Where-Object {$_.Name -eq '2a0e14a6-b0a6-4fab-991a-187a4f81c498'}
$denypolicy = Get-AzureRmPolicyDefinition | Where-Object {$_.Name -eq '1e30110a-5ceb-460c-a204-c1c3969c6d62'}

$rgs = Get-AzureRMResourceGroup -Name ExampleGroup

foreach($rg in $rgs)
{
    $tags = $rg.Tags
    foreach($key in $tags.Keys){
        $key 
        $tags[$key]
        New-AzureRmPolicyAssignment -Name ("append"+$key+"tag") -PolicyDefinition $appendpolicy -Scope $rg.ResourceId -tagName $key -tagValue  $tags[$key]
        New-AzureRmPolicyAssignment -Name ("denywithout"+$key+"tag") -PolicyDefinition $denypolicy -Scope $rg.ResourceId -tagName $key -tagValue  $tags[$key]
    }
}
```

После назначения политик можно активировать обновление для всех существующих ресурсов, чтобы применить добавленные политики тегов. Следующий сценарий сохраняет другие теги, которые существовали в ресурсах:

```powershell
$group = Get-AzureRmResourceGroup -Name "ExampleGroup" 

$resources = Find-AzureRmResource -ResourceGroupName $group.ResourceGroupName 

foreach($r in $resources)
{
    try{
        $r | Set-AzureRmResource -Tags ($a=if($r.Tags -eq $NULL) { @{}} else {$r.Tags}) -Force -UsePatchSemantics
    }
    catch{
        Write-Host  $r.ResourceId + "can't be updated"
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

## <a name="next-steps"></a>Дальнейшие действия
* После определения правила политики (как показано в приведенных выше примерах) необходимо создать определение политики и назначить ей область. Областью может быть подписка, группа ресурсов или ресурс. Сведения о назначении политик с помощью портала см. в статье [Назначение политик ресурсов и управление ими с помощью портала Azure](resource-manager-policy-portal.md). Сведения о назначении политик с помощью REST API, PowerShell или Azure CLI см. в статье [Назначение политик ресурсов и управление ими](resource-manager-policy-create-assign.md).
* Введение в политики ресурсов представлено в разделе [Общие сведения о политике ресурсов](resource-manager-policy.md).
* Руководство по использованию Resource Manager для эффективного управления подписками в организациях см [Azure enterprise scaffold - prescriptive subscription governance](resource-manager-subscription-governance.md) (Шаблон Azure для организаций. Рекомендуемая система управления подпиской).



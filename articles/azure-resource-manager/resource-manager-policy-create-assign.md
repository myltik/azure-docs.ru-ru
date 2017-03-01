---
title: "Назначение политик ресурсов Azure и управление ими | Документация Майкрософт"
description: "Описывается применение политик ресурсов Azure к подпискам и группам ресурсов и просмотр политик ресурсов."
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
ms.date: 02/10/2017
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: 0e1ee94504ebff235c1da9128e0ac68c2b28bc59
ms.openlocfilehash: 944eafeb67df4baefa99172c1082259a95e84afe
ms.lasthandoff: 02/21/2017


---
# <a name="assign-and-manage-resource-policies"></a>Назначение политик ресурсов и управление ими

Чтобы реализовать политику, необходимо выполнить три шага:

1. Определить правила политики в формате JSON.
2. Создать определение политики в подписке на основе правил в формате JSON, созданных на предыдущем шаге. После этого политика станет доступной для назначения, но ее правила еще не будут применены к подписке.
3. Назначить политику какой-либо области (например, подписке или группе ресурсов). После этого правила политики будут применены.

В Azure доступно несколько предопределенных политик, что может сократить число определяемых вами политик. Если предопределенные политики подходят для вашего случая, пропустите первые два шага и назначьте предопределенные политики области.

В этом разделе рассматриваются действия, необходимые для создания определения политики и назначения ей области. В нем не рассматривается синтаксис, используемый для создания определения политики. Сведения о синтаксисе политики см. в разделе [Общие сведения о политике ресурсов](resource-manager-policy.md).

## <a name="rest-api"></a>Интерфейс REST API

### <a name="create-policy-definition"></a>Создание определения политики

Для создания политики вы можете использовать [API REST для определений политик](/rest/api/resources/policydefinitions). API REST позволяет создавать и удалять определения политик и получать сведения о существующих определениях.

Чтобы создать определение политики, выполните следующую команду.

```HTTP
PUT https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.authorization/policydefinitions/{policyDefinitionName}?api-version={api-version}
```

Добавьте текст запроса. Ниже приведен соответствующий пример.

```json
{
  "properties": {
    "parameters": {
      "allowedLocations": {
        "type": "array",
        "metadata": {
          "description": "The list of locations that can be specified when deploying resources",
          "strongType": "location",
          "displayName": "Allowed locations"
        }
      }
    },
    "displayName": "Allowed locations",
    "description": "This policy enables you to restrict the locations your organization can specify when deploying resources.",
    "policyRule": {
      "if": {
        "not": {
          "field": "location",
          "in": "[parameters('allowedLocations')]"
        }
      },
      "then": {
        "effect": "deny"
      }
    }
  }
}
```

### <a name="assign-policy"></a>Назначение политики

Вы можете применить определение политики к требуемой области с помощью [API REST для назначений политик](/rest/api/resources/policyassignments). API REST позволяет создавать и удалять назначения политик и получать сведения о существующих назначениях.

Чтобы создать назначение политики, выполните следующую команду:

```HTTP
PUT https://management.azure.com /subscriptions/{subscription-id}/providers/Microsoft.authorization/policyassignments/{policyAssignmentName}?api-version={api-version}
```

{policy-assignment} — это имя назначения политики.

Добавьте текст запроса. Ниже приведен соответствующий пример.

```json
{
  "properties":{
    "displayName":"West US only policy assignment on the subscription ",
    "description":"Resources can only be provisioned in West US regions",
    "parameters": {
      "listOfAllowedLocations": { "value": ["West US", "West US 2"] }
     },
    "policyDefinitionId":"/subscriptions/{subscription-id}/providers/Microsoft.Authorization/policyDefinitions/{definition-name}",
      "scope":"/subscriptions/{subscription-id}"
  },
}
```

### <a name="view-policy"></a>Просмотр политики
Чтобы получить политику, используйте операцию [получения определения политики](https://docs.microsoft.com/rest/api/resources/policydefinitions#PolicyDefinitions_Get).

### <a name="get-aliases"></a>Получение псевдонимов
Вы можете получить псевдонимы с помощью REST API.

```HTTP
GET /subscriptions/{id}/providers?$expand=resourceTypes/aliases&api-version=2015-11-01
```

В следующем примере показано определение псевдонима. Как видите, псевдоним определяет пути в различных версиях API, даже если имя свойства изменено. 

```json
"aliases": [
    {
      "name": "Microsoft.Storage/storageAccounts/sku.name",
      "paths": [
        {
          "path": "properties.accountType",
          "apiVersions": [
            "2015-06-15",
            "2015-05-01-preview"
          ]
        },
        {
          "path": "sku.name",
          "apiVersions": [
            "2016-01-01"
          ]
        }
      ]
    }
]
```

## <a name="powershell"></a>PowerShell

### <a name="create-policy-definition"></a>Создание определения политики
Определение политики можно создать с помощью командлета `New-AzureRmPolicyDefinition`. В следующем примере создается определение политики, разрешающей использовать только ресурсы в Северной и Западной Европе.

```powershell
$policy = New-AzureRmPolicyDefinition -Name regionPolicyDefinition -Description "Policy to allow resource creation only in certain regions" -Policy '{    
  "if" : {
    "not" : {
      "field" : "location",
      "in" : ["northeurope" , "westeurope"]
    }
  },
  "then" : {
    "effect" : "deny"
  }
}'
```            

Выходные данные сохраняются в объекте `$policy`, который используется при назначении политики. 

Вместо ввода JSON в качестве параметра можно указать путь к JSON-файлу, содержащему правило политики.

```powershell
$policy = New-AzureRmPolicyDefinition -Name regionPolicyDefinition -Description "Policy to allow resource creation only in certain regions" -Policy "c:\policies\storageskupolicy.json"
```

### <a name="assign-policy"></a>Назначение политики

Применить политику к требуемой области можно с помощью командлета `New-AzureRmPolicyAssignment`.

```powershell
New-AzureRmPolicyAssignment -Name regionPolicyAssignment -PolicyDefinition $policy -Scope /subscriptions/{subscription-id}/resourceGroups/{resource-group-name}
```

### <a name="view-policy-assignment"></a>Просмотр назначения политики

Чтобы получить политику, используйте следующий командлет.

```powershell
(Get-AzureRmPolicyAssignment -Id "/subscriptions/{guid}/providers/Microsoft.Authorization/policyDefinitions/{definition-name}").Properties.policyRule | ConvertTo-Json
```

Он возвращает код JSON определения политики.

### <a name="remove-policy-assignment"></a>Удаление назначения политики 

Чтобы удалить назначение политики, воспользуйтесь приведенной ниже командой.

```powershell
Remove-AzureRmPolicyAssignment -Name regionPolicyAssignment -Scope /subscriptions/{subscription-id}/resourceGroups/{resource-group-name}
```

## <a name="azure-cli-20"></a>Azure CLI 2.0

### <a name="create-policy-definition"></a>Создание определения политики

Определение политики можно создать с помощью Azure CLI 2.0, выполнив в нем команду определения политики. В следующем примере создается политика, разрешающая использовать только ресурсы в Северной и Западной Европе.

```azurecli
az policy definition create --name regionPolicyDefinition --description "Policy to allow resource creation only in certain regions" --rules '{    
  "if" : {
    "not" : {
      "field" : "location",
      "in" : ["northeurope" , "westeurope"]
    }
  },
  "then" : {
    "effect" : "deny"
  }
}'    
```

### <a name="assign-policy"></a>Назначение политики

Политику можно применить к требуемой области с помощью команды назначения политики.

```azurecli
az policy assignment create --name regionPolicyAssignment --policy regionPolicyDefinition --scope /subscriptions/{subscription-id}/resourceGroups/{resource-group-name}
```

### <a name="view-policy-definition"></a>Просмотр определения политики
Чтобы получить определение политики, используйте следующую команду.

```azurecli
az policy definition show --name regionPolicyAssignment
```

### <a name="remove-policy-assignment"></a>Удаление назначения политики 

Чтобы удалить назначение политики, воспользуйтесь приведенной ниже командой.

```azurecli
az policy assignment delete --name regionPolicyAssignment --scope /subscriptions/{subscription-id}/resourceGroups/{resource-group-name}
```

## <a name="azure-cli-10"></a>Azure CLI 1.0

### <a name="create-policy-definition"></a>Создание определения политики

Определение политики можно создать с помощью интерфейса командной строки Azure, выполнив в нем команду определения политики. В следующем примере создается политика, разрешающая использовать только ресурсы в Северной и Западной Европе.

```azurecli
azure policy definition create --name regionPolicyDefinition --description "Policy to allow resource creation only in certain regions" --policy-string '{    
  "if" : {
    "not" : {
      "field" : "location",
      "in" : ["northeurope" , "westeurope"]
    }
  },
  "then" : {
    "effect" : "deny"
  }
}'    
```

Вместо указания политики в командной строке можно указать путь к JSON-файлу, содержащему политику.

```azurecli
azure policy definition create --name regionPolicyDefinition --description "Policy to allow resource creation only in certain regions" --policy "path-to-policy-json-on-disk"
```

### <a name="assign-policy"></a>Назначение политики

Политику можно применить к требуемой области с помощью команды назначения политики.

```azurecli
azure policy assignment create --name regionPolicyAssignment --policy-definition-id /subscriptions/{subscription-id}/providers/Microsoft.Authorization/policyDefinitions/{policy-name} --scope    /subscriptions/{subscription-id}/resourceGroups/{resource-group-name}
```

Область — это указанное имя группы ресурсов. Если значение параметра policy-definition-id неизвестно, то его можно получить с помощью интерфейса командной строки Azure. 

```azurecli
azure policy definition show {policy-name}
```

### <a name="view-policy"></a>Просмотр политики
Чтобы получить политику, используйте следующую команду.

```azurecli
azure policy definition show {definition-name} --json
```

### <a name="remove-policy-assignment"></a>Удаление назначения политики 

Чтобы удалить назначение политики, воспользуйтесь приведенной ниже командой.

```azurecli
azure policy assignment delete --name regionPolicyAssignment --scope /subscriptions/{subscription-id}/resourceGroups/{resource-group-name}
```

## <a name="next-steps"></a>Дальнейшие действия
* Руководство по использованию Resource Manager для эффективного управления подписками в организациях см [Azure enterprise scaffold - prescriptive subscription governance](resource-manager-subscription-governance.md) (Шаблон Azure для организаций. Рекомендуемая система управления подпиской).



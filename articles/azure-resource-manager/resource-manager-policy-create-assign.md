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
ms.date: 09/19/2017
ms.author: tomfitz
ms.openlocfilehash: 64bdd6ed41e98079c8d4112e895aaeddcd629282
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="assign-and-manage-resource-policies"></a>Назначение политик ресурсов и управление ими

Чтобы реализовать политику, необходимо выполнить три шага:

1. Просмотрите определения политик (включая встроенных политики, предоставленные Azure) и определите, не существует ли уже в вашей подписке политика, отвечающая вашим требованиям.
2. Если такая политика существует, получите ее имя.
3. Если она не существует, определите правило политики в формате JSON и добавьте его в качестве определения политики в свою подписку. После этого политика станет доступной для назначения, но ее правила еще не будут применены к подписке.
4. В любом случае назначьте политику какой-либо области (например, подписке или группе ресурсов). После этого правила политики будут применены.

В этой статье рассматриваются действия, необходимые для создания определения политики и назначения его области с помощью REST API, PowerShell или Azure CLI. Если вы предпочитаете назначать политики с помощью портала, то см. статью [Назначение политик ресурсов и управление ими с помощью портала Azure](resource-manager-policy-portal.md). В этой статье не рассматривается синтаксис, используемый для создания определения политики. Сведения о синтаксисе политики см. в разделе [Общие сведения о политике ресурсов](resource-manager-policy.md).

## <a name="exclusion-scopes"></a>Области исключения

При назначении политики можно исключить область. Благодаря этому назначать политики становится проще, так как назначая политику на уровне подписки, вы можете указать, где эта политика не будет применяться. Например, в вашей подписке может содержаться группа ресурсов, предназначенная для сетевой инфраструктуры. Отделы по работе с приложениями развертывают свои ресурсы в другие группы ресурсов. Вы не хотите, чтобы эти отделы создавали сетевые ресурсы, так как это может привести к проблемам безопасности. При этом вы хотите разрешить сетевые ресурсы в группе ресурсов сети. Вам следует назначить политику на уровне подписки, но исключить группу сетевых ресурсов. Вы можете определить несколько вложенных областей.

```json
{
    "properties":{
        "policyDefinitionId":"<ID for policy definition>",
        "notScopes":[
            "/subscriptions/<subid>/resourceGroups/networkresourceGroup1"
        ]
    }
}
```

Если вы указываете область исключения в назначении, используйте API версии **2017-06-01-preview**.

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
      "allowedLocations": { "value": ["northeurope", "westus"] }
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

Прежде чем продолжить работу с примерами PowerShell, [установите последнюю версию](/powershell/azure/install-azurerm-ps) Azure PowerShell. Параметры политики были добавлены в версии 3.6.0. Если установлена более ранняя версия, примеры возвращают ошибку из-за того, что параметр не найден.

### <a name="view-policy-definitions"></a>Просмотр определений политик
Чтобы просмотреть все определения политик в подписке, используйте приведенную ниже команду.

```powershell
Get-AzureRmPolicyDefinition
```

Она возвращает все доступные определения политик, включая встроенные политики. Каждая политика возвращается в приведенном ниже формате.

```powershell
Name               : e56962a6-4747-49cd-b67b-bf8b01975c4c
ResourceId         : /providers/Microsoft.Authorization/policyDefinitions/e56962a6-4747-49cd-b67b-bf8b01975c4c
ResourceName       : e56962a6-4747-49cd-b67b-bf8b01975c4c
ResourceType       : Microsoft.Authorization/policyDefinitions
Properties         : @{displayName=Allowed locations; policyType=BuiltIn; description=This policy enables you to
                     restrict the locations your organization can specify when deploying resources. Use to enforce
                     your geo-compliance requirements.; parameters=; policyRule=}
PolicyDefinitionId : /providers/Microsoft.Authorization/policyDefinitions/e56962a6-4747-49cd-b67b-bf8b01975c4c
```

Прежде чем продолжить создание определения политики, рассмотрите встроенные политики. Если вы обнаружите встроенную политику, которая применяет необходимые ограничения, то можете пропустить создание определения политики. Вместо этого назначьте встроенную политику требуемой области.

### <a name="create-policy-definition"></a>Создание определения политики
Определение политики можно создать с помощью командлета `New-AzureRmPolicyDefinition`.

Чтобы создать определение политики из файла, передайте путь в файл. Для внешнего файла:

```powershell
$definition = New-AzureRmPolicyDefinition `
    -Name denyCoolTiering `
    -DisplayName "Deny cool access tiering for storage" `
    -Policy 'https://raw.githubusercontent.com/Azure/azure-policy-samples/master/samples/Storage/storage-account-access-tier/azurepolicy.rules.json'
```

Для локального файла:

```powershell
$definition = New-AzureRmPolicyDefinition `
    -Name denyCoolTiering `
    -Description "Deny cool access tiering for storage" `
    -Policy "c:\policies\coolAccessTier.json"
```

Для создания определения политики с помощью встроенных правил:

```powershell
$definition = New-AzureRmPolicyDefinition -Name denyCoolTiering -Description "Deny cool access tiering for storage" -Policy '{
  "if": {
    "allOf": [
      {
        "field": "type",
        "equals": "Microsoft.Storage/storageAccounts"
      },
      {
        "field": "kind",
        "equals": "BlobStorage"
      },
      {
        "not": {
          "field": "Microsoft.Storage/storageAccounts/accessTier",
          "equals": "cool"
        }
      }
    ]
  },
  "then": {
    "effect": "deny"
  }
}'
```            

Выходные данные сохраняются в объекте `$definition`, который используется при назначении политики. 

В следующем примере создается определение политики, которое включает параметры:

```powershell
$policy = '{
    "if": {
        "allOf": [
            {
                "field": "type",
                "equals": "Microsoft.Storage/storageAccounts"
            },
            {
                "not": {
                    "field": "location",
                    "in": "[parameters(''allowedLocations'')]"
                }
            }
        ]
    },
    "then": {
        "effect": "Deny"
    }
}'

$parameters = '{
    "allowedLocations": {
        "type": "array",
        "metadata": {
          "description": "The list of locations that can be specified when deploying storage accounts.",
          "strongType": "location",
          "displayName": "Allowed locations"
        }
    }
}' 

$definition = New-AzureRmPolicyDefinition -Name storageLocations -Description "Policy to specify locations for storage accounts." -Policy $policy -Parameter $parameters 
```

### <a name="assign-policy"></a>Назначение политики

Применить политику к требуемой области можно с помощью командлета `New-AzureRmPolicyAssignment`. В следующем примере политика назначается группе ресурсов.

```powershell
$rg = Get-AzureRmResourceGroup -Name "ExampleGroup"
New-AzureRMPolicyAssignment -Name accessTierAssignment -Scope $rg.ResourceId -PolicyDefinition $definition
```

Чтобы назначить политику, которой требуются параметры, создайте объект с этими значениями. В следующем примере извлекается встроенная политика и передаются значения параметров.

```powershell
$rg = Get-AzureRmResourceGroup -Name "ExampleGroup"
$definition = Get-AzureRmPolicyDefinition -Id /providers/Microsoft.Authorization/policyDefinitions/e5662a6-4747-49cd-b67b-bf8b01975c4c
$array = @("West US", "West US 2")
$param = @{"listOfAllowedLocations"=$array}
New-AzureRMPolicyAssignment -Name locationAssignment -Scope $rg.ResourceId -PolicyDefinition $definition -PolicyParameterObject $param
```

### <a name="view-policy-assignment"></a>Просмотр назначения политики

Чтобы получить назначение конкретной политики, используйте следующую команду.

```powershell
$rg = Get-AzureRmResourceGroup -Name "ExampleGroup"
(Get-AzureRmPolicyAssignment -Name accessTierAssignment -Scope $rg.ResourceId
```

Чтобы просмотреть правило политики для определения политики, используйте следующую команду:

```powershell
(Get-AzureRmPolicyDefinition -Name coolAccessTier).Properties.policyRule | ConvertTo-Json
```

### <a name="remove-policy-assignment"></a>Удаление назначения политики 

Чтобы удалить назначение политики, воспользуйтесь приведенной ниже командой.

```powershell
Remove-AzureRmPolicyAssignment -Name regionPolicyAssignment -Scope /subscriptions/{subscription-id}/resourceGroups/{resource-group-name}
```

## <a name="azure-cli"></a>Интерфейс командной строки Azure

### <a name="view-policy-definitions"></a>Просмотр определений политик
Чтобы просмотреть все определения политик в подписке, используйте приведенную ниже команду.

```azurecli
az policy definition list
```

Она возвращает все доступные определения политик, включая встроенные политики. Каждая политика возвращается в приведенном ниже формате.

```azurecli
{                                                            
  "description": "This policy enables you to restrict the locations your organization can specify when deploying resources. Use to enforce your geo-compliance requirements.",                      
  "displayName": "Allowed locations",
  "id": "/providers/Microsoft.Authorization/policyDefinitions/e56962a6-4747-49cd-b67b-bf8b01975c4c",
  "name": "e56962a6-4747-49cd-b67b-bf8b01975c4c",
  "policyRule": {
    "if": {
      "not": {
        "field": "location",
        "in": "[parameters('listOfAllowedLocations')]"
      }
    },
    "then": {
      "effect": "Deny"
    }
  },
  "policyType": "BuiltIn"
}
```

Прежде чем продолжить создание определения политики, рассмотрите встроенные политики. Если вы обнаружите встроенную политику, которая применяет необходимые ограничения, то можете пропустить создание определения политики. Вместо этого назначьте встроенную политику требуемой области.

### <a name="create-policy-definition"></a>Создание определения политики

Определение политики можно создать с помощью Azure CLI, выполнив в нем команду определения политики.

Для создания определения политики с помощью встроенных правил:

```azurecli
az policy definition create --name denyCoolTiering --description "Deny cool access tiering for storage" --rules '{
  "if": {
    "allOf": [
      {
        "field": "type",
        "equals": "Microsoft.Storage/storageAccounts"
      },
      {
        "field": "kind",
        "equals": "BlobStorage"
      },
      {
        "not": {
          "field": "Microsoft.Storage/storageAccounts/accessTier",
          "equals": "cool"
        }
      }
    ]
  },
  "then": {
    "effect": "deny"
  }
}'    
```

### <a name="assign-policy"></a>Назначение политики

Политику можно применить к требуемой области с помощью команды policy assignment. В следующем примере политика назначается группе ресурсов.

```azurecli
az policy assignment create --name coolAccessTierAssignment --policy coolAccessTier --scope /subscriptions/{subscription-id}/resourceGroups/{resource-group-name}
```

### <a name="view-policy-assignment"></a>Просмотр назначения политики

Чтобы просмотреть назначение политики, укажите имя назначения политики и область.

```azurecli
az policy assignment show --name coolAccessTierAssignment --scope "/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}"
```

### <a name="remove-policy-assignment"></a>Удаление назначения политики 

Чтобы удалить назначение политики, воспользуйтесь приведенной ниже командой.

```azurecli
az policy assignment delete --name coolAccessTier --scope /subscriptions/{subscription-id}/resourceGroups/{resource-group-name}
```

## <a name="next-steps"></a>Дальнейшие действия
* Руководство по использованию Resource Manager для эффективного управления подписками в организациях см [Azure enterprise scaffold - prescriptive subscription governance](resource-manager-subscription-governance.md) (Шаблон Azure для организаций. Рекомендуемая система управления подпиской).


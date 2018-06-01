---
title: Структура определения службы "Политика Azure"
description: Описывается, как определение политики ресурсов, описывающее условия применения данной политики и соответствующий эффект, используется службой "Политика Azure", чтобы установить соглашения о ресурсах в вашей организации.
services: azure-policy
author: DCtheGeek
ms.author: dacoulte
ms.date: 05/07/2018
ms.topic: conceptual
ms.service: azure-policy
manager: carmonm
ms.openlocfilehash: 1937792290d973f3aee7fa3c0714f4667c21e79a
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/16/2018
ms.locfileid: "34194654"
---
# <a name="azure-policy-definition-structure"></a>Структура определения службы "Политика Azure"

Определение политики ресурсов, используемое службой "Политика Azure" и описывающее условия применения данной политики и соответствующий эффект, позволяет установить соглашения о ресурсах в вашей организации. Это соглашения помогут вам контролировать расходы и управлять ресурсами. Например, можно указать, что разрешены только определенные типы виртуальных машин. Кроме того, можно требовать наличие определенного тега для каждого ресурса. Политики наследуются всеми дочерними ресурсами. Это значит, что политики, применяемые к группе ресурсов, применяются также ко всем ресурсам в этой группе.

Схему, используемую службой "Политика Azure", можно найти здесь: [https://schema.management.azure.com/schemas/2016-12-01/policyDefinition.json](https://schema.management.azure.com/schemas/2016-12-01/policyDefinition.json)

Для создания определения политики используется JSON. Определение политики содержит следующие элементы:

- mode;
- parameters
- display name
- description
- policy rule
  - logical evaluation
  - effect

В следующем примере JSON показана политика, которая налагает ограничения на расположения для развертывания ресурсов.

```json
{
    "properties": {
        "mode": "all",
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

Все примеры шаблонов политик Azure [доступны здесь](json-samples.md).

## <a name="mode"></a>Mode

**Режим** определяет типы ресурсов, которые будут оцениваться для политики. Ниже приведены поддерживаемые режимы.

- `all`: оценка групп ресурсов и всех типов ресурсов.
- `indexed`: оцениваются только типы ресурсов, которые поддерживают теги и расположение.

В большинстве случаев рекомендуется задать для параметра **mode** значение `all`. Во всех определениях политик, создаваемых на портале, используется режим `all`. Если используется PowerShell или Azure CLI, необходимо указать параметр **mode** вручную. Если в определении политики не задано значение для параметра **mode**, оно считается значением по умолчанию для `all` в Azure PowerShell и для `null` в Azure CLI, что эквивалентно `indexed` для обратной совместимости.

`indexed` следует использовать при создании политик, которые будут принудительно применять теги или расположения. Это не обязательно, но помешает отображению ресурсов, которые не поддерживают теги и расположения, в качестве несоответствующих в результатах проверки соответствия. Единственным исключением являются **группы ресурсов**. В политиках, которые пытаются принудительно применить расположение или теги к группе ресурсов, следует задать для параметра **mode** значение `all` и явно указать целевой тип `Microsoft.Resources/subscriptions/resourceGroup`. Пример см. в статье о [принудительном применении тегов к группам ресурсов](scripts/enforce-tag-rg.md).

## <a name="parameters"></a>Параметры

Параметры помогают упростить управление политиками за счет сокращения числа определений политик. Параметры можно рассматривать как поля в форме: `name`, `address`, `city`, `state`. Эти параметры никогда не меняются, однако их значения изменяются в зависимости от того, как пользователь заполняет форму. Точно так же параметры работают при создании политик. Добавив параметры в определение политики, вы сможете повторно использовать ее в различных сценариях, указывая разные значения.

Например, можно определить политику для свойства ресурса, чтобы ограничить расположения, в которых могут развертываться ресурсы. В этом случае при создании политики можно объявить следующие параметры.

```json
"parameters": {
    "allowedLocations": {
        "type": "array",
        "metadata": {
            "description": "The list of allowed locations for resources.",
            "displayName": "Allowed locations",
            "strongType": "location"
        }
    }
}
```

Типом параметра может быть строка или массив. Свойство метаданных используется в таких инструментах, как портал Azure, для отображения полезных для пользователя сведений.

В свойстве метаданных можно использовать **strongType**, чтобы предоставить список с множественным выбором параметров на портале Azure.  К допустимым значениям для **strongType** относится следующее:

- `"location"`
- `"resourceTypes"`
- `"storageSkus"`
- `"vmSKUs"`
- `"existingResourceGroups"`
- `"omsWorkspace"`

В правилах политики полученные параметры используются так:

```json
{
    "field": "location",
    "in": "[parameters('allowedLocations')]"
}
```

## <a name="definition-location"></a>Расположение определения

При создании определения инициативы или политики важно указывать определение расположения.

Расположение определения указывает область, которой можно назначить определение инициативы или политики. Расположение может быть задано как группа управления или подписка.

> [!NOTE]
> Если вы планируете применять это определение политики к нескольким подпискам, расположение должно быть группой управления, содержащей подписки, которым вы назначите инициативу или политику.

## <a name="display-name-and-description"></a>Отображаемое имя и описание

Параметры **displayName** и **description** позволяют идентифицировать определение политики и описать контекст для ее использования.

## <a name="policy-rule"></a>Правило политики

Правило политики состоит из блоков **If** и **Then**. В блоке **If** указываются одно или несколько условий. Они определяют, когда применяется эта политика. В этих условиях можно использовать логические операторы, чтобы точно определить сценарии для использования политики.

В блоке **Then** описываются результаты, которые вступают в силу при соблюдении условий из блока **If**.

```json
{
    "if": {
        <condition> | <logical operator>
    },
    "then": {
        "effect": "deny | audit | append | auditIfNotExists | deployIfNotExists"
    }
}
```

### <a name="logical-operators"></a>Логические операторы

Ниже перечислены поддерживаемые логические операторы.

- `"not": {condition  or operator}`
- `"allOf": [{condition or operator},{condition or operator}]`
- `"anyOf": [{condition or operator},{condition or operator}]`

Оператор **not** инвертирует результат условия. Оператор **allOf** действует как логическая операция **And**, то есть требует соблюдения всех входящих в него условий. Оператор **anyOf** действует как логическая операция **Or**, то есть проверяет соблюдение хотя бы одного из входящих в него условий.

Допускается вложение логических операторов. В следующем примере представлена операция **not**, вложенная в операцию **allOf**.

```json
"if": {
    "allOf": [{
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
```

### <a name="conditions"></a>Условия

Условие определяет, соответствует ли свойство **field** определенным параметрам. Поддерживаются такие условия:

- `"equals": "value"`
- `"notEquals": "value"`
- `"like": "value"`
- `"notLike": "value"`
- `"match": "value"`
- `"notMatch": "value"`
- `"contains": "value"`
- `"notContains": "value"`
- `"in": ["value1","value2"]`
- `"notIn": ["value1","value2"]`
- `"containsKey": "keyName"`
- `"notContainsKey": "keyName"`
- `"exists": "bool"`

При использовании условий **like** и **notLike** можно указать в значении подстановочный знак (*).

При использовании условий **match** и **notMatch** укажите `#` для представления цифры, `?` для буквы и любой другой символ — для представления фактического символа. Примеры см. в статье [Разрешение на использование нескольких шаблонов имен](scripts/allow-multiple-name-patterns.md).

### <a name="fields"></a>Поля

Условия создаются на основе полей. Поле представляет свойства в полезных данных запроса ресурса, используемые для описания состояния ресурса.  

Поддерживаются следующие поля.

- `name`
- `fullName`
  - Возвращает полное имя ресурса, включая все родительские ресурсы (например, myServer/myDatabase).
- `kind`
- `type`
- `location`
- `tags`
- `tags.tagName`
- `tags[tagName]`
  - Этот синтаксис в скобках поддерживает имена тегов, содержащие точки.
- Список псевдонимов свойств указан в разделе [Псевдонимы](#aliases).

### <a name="alternative-accessors"></a>Альтернативные методы доступа

**Field** (поле) — основной метод доступа, используемый в правилах политики. Он непосредственно проверяет ресурс, который вычисляется. Однако политика поддерживает еще один метод доступа — **source**.

```json
"source": "action",
"equals": "Microsoft.Compute/virtualMachines/write"
```

**Source** поддерживает только одно значение, **action**. Action возвращает действие авторизации запроса, который оценивается. Действия авторизации предоставляются в разделе авторизации [журнала действий](../monitoring-and-diagnostics/monitoring-activity-log-schema.md).

Когда политика оценивает имеющиеся ресурсы в фоновом режиме, она задает в качестве **action** действие авторизации `/write` для типа ресурса.

### <a name="effect"></a>Результат

Политика поддерживает следующие типы действий:

- **Deny** создает событие в журнале аудита и отклоняет запрос;
- **Audit** создает событие в журнале аудита, но выполняет запрос;
- **Append** добавляет в запрос некоторый набор полей;
- **AuditIfNotExists** включает аудит, если ресурс не существует;
- **DeployIfNotExists** развертывает ресурс, если он еще не существует. Сейчас этот эффект поддерживается только с помощью встроенных политик.

Для типа **append**необходимо указать следующие сведения:

```json
"effect": "append",
"details": [{
    "field": "field name",
    "value": "value of the field"
}]
```

Значением может быть строка или объект формата JSON.

С помощью параметров **AuditIfNotExists** и **DeployIfNotExists** можно проверить существование связанного ресурса и применить правило и соответствующее действие, если такой ресурс не существует. Например можно потребовать, чтобы наблюдатель за сетями был развернут для всех виртуальных сетей.
Пример аудита наличия развернутого расширения для виртуальных машин приведен в разделе [Проверка наличия расширения](scripts/audit-ext-not-exist.md).

## <a name="aliases"></a>Псевдонимы

Псевдонимы свойств позволяют обращаться к определенным свойствам для типа ресурса. Псевдонимы позволяют ограничить значения или условия, разрешенные для свойства ресурса. Каждый псевдоним сопоставляется с путями в разных версиях API для заданного типа ресурса. Во время оценки политики модуль политики получает путь свойства для этой версии API.

Список псевдонимов постоянно пополняется. Чтобы определить, какие псевдонимы в настоящее время поддерживаются службой "Политика Azure", используйте один из следующих методов:

- Azure PowerShell

  ```azurepowershell-interactive
  # Login first with Connect-AzureRmAccount if not using Cloud Shell

  $azContext = Get-AzureRmContext
  $azProfile = [Microsoft.Azure.Commands.Common.Authentication.Abstractions.AzureRmProfileProvider]::Instance.Profile
  $profileClient = New-Object -TypeName Microsoft.Azure.Commands.ResourceManager.Common.RMProfileClient -ArgumentList ($azProfile)
  $token = $profileClient.AcquireAccessToken($azContext.Subscription.TenantId)
  $authHeader = @{
      'Content-Type'='application/json'
      'Authorization'='Bearer ' + $token.AccessToken
  }

  # Invoke the REST API
  $response = Invoke-RestMethod -Uri 'https://management.azure.com/providers/?api-version=2017-08-01&$expand=resourceTypes/aliases' -Method Get -Headers $authHeader

  # Create an Array List to hold discovered aliases
  $aliases = New-Object System.Collections.ArrayList

  foreach ($ns in $response.value) {
      foreach ($rT in $ns.resourceTypes) {
          if ($rT.aliases) {
              foreach ($obj in $rT.aliases) {
                  $alias = [PSCustomObject]@{
                      Namespace       = $ns.namespace
                      resourceType    = $rT.resourceType
                      alias           = $obj.name
                  }
                  $aliases.Add($alias) | Out-Null
              }
          }
      }
  }

  # Output the list, sort, and format. You can customize with Where-Object to limit as desired.
  $aliases | Sort-Object -Property Namespace, resourceType, alias | Format-Table
  ```

- Инфраструктура CLI Azure

  ```azurecli-interactive
  # Login first with az login if not using Cloud Shell

  # Get Azure Policy aliases for a specific Namespace
  az provider show --namespace Microsoft.Automation --expand "resourceTypes/aliases" --query "resourceTypes[].aliases[].name"
  ```

- REST API или ARMClient

  ```http
  GET https://management.azure.com/providers/?api-version=2017-08-01&$expand=resourceTypes/aliases
  ```

## <a name="initiatives"></a>Инициативы

Инициативы дают возможность сгруппировать несколько связанных определений политик, чтобы упростить их назначение и управление ими, так как это позволяет работать с группой как с единым элементом. Например, можно сгруппировать все связанные определения политик тегов в одной инициативе. Вместо назначения каждой политики по отдельности вы сможете применить инициативу.

В следующем примере показано, как создать инициативу для обработки двух тегов, `costCenter` и `productName`. Для применения значения тега по умолчанию используются две встроенные политики.

```json
{
    "properties": {
        "displayName": "Billing Tags Policy",
        "policyType": "Custom",
        "description": "Specify cost Center tag and product name tag",
        "parameters": {
            "costCenterValue": {
                "type": "String",
                "metadata": {
                    "description": "required value for Cost Center tag"
                }
            },
            "productNameValue": {
                "type": "String",
                "metadata": {
                    "description": "required value for product Name tag"
                }
            }
        },
        "policyDefinitions": [{
                "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62",
                "parameters": {
                    "tagName": {
                        "value": "costCenter"
                    },
                    "tagValue": {
                        "value": "[parameters('costCenterValue')]"
                    }
                }
            },
            {
                "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/2a0e14a6-b0a6-4fab-991a-187a4f81c498",
                "parameters": {
                    "tagName": {
                        "value": "costCenter"
                    },
                    "tagValue": {
                        "value": "[parameters('costCenterValue')]"
                    }
                }
            },
            {
                "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62",
                "parameters": {
                    "tagName": {
                        "value": "productName"
                    },
                    "tagValue": {
                        "value": "[parameters('productNameValue')]"
                    }
                }
            },
            {
                "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/2a0e14a6-b0a6-4fab-991a-187a4f81c498",
                "parameters": {
                    "tagName": {
                        "value": "productName"
                    },
                    "tagValue": {
                        "value": "[parameters('productNameValue')]"
                    }
                }
            }
        ]
    },
    "id": "/subscriptions/<subscription-id>/providers/Microsoft.Authorization/policySetDefinitions/billingTagsPolicy",
    "type": "Microsoft.Authorization/policySetDefinitions",
    "name": "billingTagsPolicy"
}
```

## <a name="next-steps"></a>Дополнительная информация

- Ознакомьтесь с примерами шаблонов политик Azure [здесь](json-samples.md).
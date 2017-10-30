---
title: "Политики ресурсов Azure | Документация Майкрософт"
description: "Использование политик Azure Resource Manager, обеспечивающих согласованную настройку свойств ресурсов при развертывании. Политики можно применять на уровне подписки или группы ресурсов."
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: abde0f73-c0fe-4e6d-a1ee-32a6fce52a2d
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/09/2017
ms.author: tomfitz
ms.openlocfilehash: cfdbf35b76b6a7f3cddb2deb35dfc475e0fc600f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="resource-policy-overview"></a>Общие сведения о политике ресурсов
Политики ресурсов позволяют настроить определенные соглашения для ресурсов в организации. Это соглашения помогут вам контролировать расходы и управлять ресурсами. Например, можно указать, что разрешены только определенные типы виртуальных машин. Кроме того, можно требовать наличие определенного тега для каждого ресурса. Политики наследуются всеми дочерними ресурсами. Это значит, что политики, применяемые к группе ресурсов, применяются также ко всем ресурсам в этой группе.

При работе с политиками нужно знать две важные концепции:

* определение политики описывает, когда и как политика будет применяться;
* назначение политики привязывает эту политику к определенной области (к подписке или группе ресурсов).

В этой статье рассматривается только определение политики. Сведения о назначении политик см. в статьях [Назначение политик ресурсов и управление ими с помощью портала Azure](resource-manager-policy-portal.md) и [Назначение политик ресурсов и управление ими](resource-manager-policy-create-assign.md).

Политики оцениваются при создании и обновлении ресурсов (операции PUT и PATCH).

## <a name="how-is-it-different-from-rbac"></a>Чем это отличается от управления доступом на основе ролей?
Существует ряд ключевых различий между политиками и управлением доступом на основе ролей (RBAC). RBAC определяет действия **пользователя** в различных областях. Например, если вам назначена роль участника для группы ресурсов в требуемой области, вы можете вносить изменения в эту группу ресурсов. Политика определяет свойства **ресурсов** во время их развертывания. Например, с помощью политик можно управлять типами ресурсов, которые можно подготовить, или ограничить расположения, в которых можно подготовить ресурсы. В отличие от RBAC, политика представляет собой систему разрешения по умолчанию и явного запрета. 

Чтобы использовать политики, нужно пройти аутентификацию с помощью RBAC. В частности, для учетной записи должны быть предоставлены:

* `Microsoft.Authorization/policydefinitions/write` разрешение на создание политики;
* `Microsoft.Authorization/policyassignments/write` разрешение на назначение политики. 

Эти разрешения не включаются в роль **Участник**.

## <a name="built-in-policies"></a>Встроенные политики

В Azure доступно несколько встроенных определений политик, которые сокращают число соответствующих политик. Прежде чем приступить к работе с определениями политик, следует проверить, не обеспечивает ли какая-либо встроенная политика нужные вам ограничения. Определения встроенных политик приведены ниже:

* Allowed locations;
* Допустимые типы ресурсов
* Allowed storage account SKUs;
* Allowed virtual machine SKUs;
* Apply tag and default value;
* Enforce tag and value;
* Not allowed resource types;
* Require SQL Server version 12.0;
* Require storage account encryption.

Любую из этих политик можно назначить с помощью [портала](resource-manager-policy-portal.md), [PowerShell](resource-manager-policy-create-assign.md#powershell) или [Azure CLI](resource-manager-policy-create-assign.md#azure-cli).

## <a name="policy-definition-structure"></a>Структура определения политики
Для создания определения политики используется JSON. Определение политики содержит следующие элементы:

* mode;
* parameters
* display name
* description
* policy rule
  * logical evaluation
  * effect

В следующем примере показана политика, которая налагает ограничения на набор расположений для развертывания ресурсов.

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

## <a name="mode"></a>Режим

Рекомендуется задать для параметра `mode` значение `all`. Если задать значение **all**, группы ресурсов и все типы ресурсов будут оцениваться в соответствии с политикой. На портале используется значение **all** для всех политик. Если используется PowerShell или Azure CLI, необходимо указать параметр `mode` и присвоить ему значение **all**.
 
Ранее политика оценивалась только для тех типов ресурсов, которые поддерживали теги и расположение. Режим `indexed` продолжает это поведение. Если используется режим **all**, политики также оцениваются для типов ресурсов, которые не поддерживают теги и расположение. [Подсеть виртуальной сети](https://github.com/Azure/azure-policy-samples/tree/master/samples/Network/enforce-nsg-on-subnet) — пример нового добавленного типа. Кроме того, при заданном режиме **all** оцениваются группы ресурсов. Например, можно [принудительно применять теги в группе ресурсов](https://github.com/Azure/azure-policy-samples/tree/master/samples/ResourceGroup/enforce-resourceGroup-tags). 

## <a name="parameters"></a>Параметры
Использование параметров помогает упростить управление политиками за счет сокращения числа определений политики. Вы можете определить политику для свойства ресурса (например, чтобы ограничить набор расположений для развертывания этих ресурсов), включив в определение параметры. Затем это определение политики можно неоднократно использовать в разных сценариях, передавая в него разные значения (например, набор расположений для определенной подписки) при назначении политики.

А параметры объявляются при создании определения политики.

```json
"parameters": {
  "allowedLocations": {
    "type": "array",
    "metadata": {
      "description": "The list of allowed locations for resources.",
      "displayName": "Allowed locations"
    }
  }
}
```

Типом параметра может быть строка или массив. Свойство метаданных используется в таких инструментах, как портал Azure, для отображения удобных для пользователя сведений. 

В правилах политики полученные параметры используются так: 

```json
{ 
    "field": "location",
    "in": "[parameters('allowedLocations')]"
}
```

## <a name="display-name-and-description"></a>Отображаемое имя и описание

Параметры **displayName** и **description** позволяют идентифицировать определение политики и описать контекст для ее использования.

## <a name="policy-rule"></a>Правило политики

Правило политики состоит из блоков **If** и **Then**. В блоке **If** указываются одно или несколько условий. Они определяют, когда применяется эта политика. В этих условиях можно использовать логические операторы, чтобы точно определить сценарии для использования политики. В блоке **Then** описываются результаты, которые вступают в силу при соблюдении условий из блока **If**.

```json
{
  "if": {
    <condition> | <logical operator>
  },
  "then": {
    "effect": "deny | audit | append"
  }
}
```

### <a name="logical-operators"></a>Логические операторы
Ниже перечислены поддерживаемые логические операторы.

* `"not": {condition  or operator}`
* `"allOf": [{condition or operator},{condition or operator}]`
* `"anyOf": [{condition or operator},{condition or operator}]`

Оператор **not** инвертирует результат условия. Оператор **allOf** действует как логическая операция **And**, то есть требует соблюдения всех входящих в него условий. Оператор **anyOf** действует как логическая операция **Or**, то есть проверяет соблюдение хотя бы одного из входящих в него условий.

Допускается вложение логических операторов. В следующем примере представлена операция **not**, вложенная в операцию **allOf**. 

```json
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
```

### <a name="conditions"></a>Условия
Условие определяет, соответствует ли свойство **field** определенным параметрам. Поддерживаются такие условия:

* `"equals": "value"`
* `"like": "value"`
* `"match": "value"`
* `"contains": "value"`
* `"in": ["value1","value2"]`
* `"containsKey": "keyName"`
* `"exists": "bool"`

В значении для условия **like** можно использовать подстановочный знак (*).

При использовании условия **match** для цифры используйте `#`, для буквы — `?` и любые другие соответствующие символы. Примеры приведены в разделе [Применение политик ресурсов для имен и текста](resource-manager-policy-naming-convention.md).

### <a name="fields"></a>Поля
Условия создаются на основе полей. Поле представляет свойства в полезных данных запроса ресурса, используемые для описания состояния ресурса.  

Поддерживаются следующие поля.

* `name`
* `kind`
* `type`
* `location`
* `tags`
* `tags.*` 
* Список псевдонимов свойств указан в разделе [Псевдонимы](#aliases).

### <a name="effect"></a>Результат
Политика поддерживает три типа результатов — `deny`, `audit`, `append`, `AuditIfNotExists` и `DeployIfNotExists`. 

* **deny** создает событие в журнале аудита и отклоняет запрос.
* **audit** создает событие в журнале аудита, но выполняет запрос.
* **append** добавляет в запрос некоторый набор полей. 
* **AuditIfNotExists** включает аудит, если ресурс не существует.
* **DeployIfNotExists** развертывает ресурс, если он еще не существует. Сейчас этот эффект поддерживается только с помощью встроенных политик.

Для типа **append**необходимо указать следующие сведения:

```json
"effect": "append",
"details": [
  {
    "field": "field name",
    "value": "value of the field"
  }
]
```

Значением может быть строка или объект формата JSON. 

С помощью параметров **AuditIfNotExists** и **DeployIfNotExists** можно проверить существование дочернего ресурса и применить правило, если этот ресурс не существует. Например можно потребовать, чтобы наблюдатель за сетями был развернут для всех виртуальных сетей.

Пример аудита при отсутствии развернутого расширения для виртуальных машин см. в разделе, посвященном [аудиту расширений виртуальных машин](https://github.com/Azure/azure-policy-samples/blob/master/samples/Compute/audit-vm-extension/azurepolicy.json).

## <a name="aliases"></a>Псевдонимы

Псевдонимы свойств позволяют обращаться к определенным свойствам для типа ресурса. Псевдонимы позволяют ограничить значения или условия, разрешенные для свойства ресурса. Каждый псевдоним сопоставляется с путями в разных версиях API для заданного типа ресурса. Во время оценки политики модуль политики получает путь свойства для этой версии API.

**Microsoft.Cache/Redis**

| Alias | Описание |
| ----- | ----------- |
| Microsoft.Cache/Redis/enableNonSslPort | Указывает, включен ли на сервере Redis порт без поддержки SSL (6379). |
| Microsoft.Cache/Redis/shardCount | Задает число сегментов, создаваемых в кэше кластера уровня "Премиум".  |
| Microsoft.Cache/Redis/sku.capacity | Задает размер развертываемого кэша Redis.  |
| Microsoft.Cache/Redis/sku.family | Задает используемое семейство SKU. |
| Microsoft.Cache/Redis/sku.name | Задает тип развертываемого кэша Redis. |

**Microsoft.Cdn/profiles**

| Alias | Описание |
| ----- | ----------- |
| Microsoft.CDN/profiles/sku.name | Задает имя ценовой категории. |

**Microsoft.Compute/disks**

| Alias | Описание |
| ----- | ----------- |
| Microsoft.Compute/imageOffer | Задает предложение образа платформы или образа Marketplace, используемого для создания виртуальной машины. |
| Microsoft.Compute/imagePublisher | Задает издатель образа платформы или образа Marketplace, используемого для создания виртуальной машины. |
| Microsoft.Compute/imageSku | Задает номер SKU образа платформы или образа Marketplace, используемого для создания виртуальной машины. |
| Microsoft.Compute/imageVersion | Задает версию образа платформы или образа Marketplace, используемого для создания виртуальной машины. |


**Microsoft.Compute/virtualMachines**

| Alias | Описание |
| ----- | ----------- |
| Microsoft.Compute/imageId | Задайте идентификатор образа, используемого для создания виртуальной машины. |
| Microsoft.Compute/imageOffer | Задает предложение образа платформы или образа Marketplace, используемого для создания виртуальной машины. |
| Microsoft.Compute/imagePublisher | Задает издатель образа платформы или образа Marketplace, используемого для создания виртуальной машины. |
| Microsoft.Compute/imageSku | Задает номер SKU образа платформы или образа Marketplace, используемого для создания виртуальной машины. |
| Microsoft.Compute/imageVersion | Задает версию образа платформы или образа Marketplace, используемого для создания виртуальной машины. |
| Microsoft.Compute/licenseType | Указывает, используется ли для образа или диска локальная лицензия. Это значение используется только для образов, содержащих операционную систему Windows Server.  |
| Microsoft.Compute/virtualMachines/imageOffer | Задает предложение образа платформы или образа Marketplace, используемого для создания виртуальной машины. |
| Microsoft.Compute/virtualMachines/imagePublisher | Задает издатель образа платформы или образа Marketplace, используемого для создания виртуальной машины. |
| Microsoft.Compute/virtualMachines/imageSku | Задает номер SKU образа платформы или образа Marketplace, используемого для создания виртуальной машины. |
| Microsoft.Compute/virtualMachines/imageVersion | Задает версию образа платформы или образа Marketplace, используемого для создания виртуальной машины. |
| Microsoft.Compute/virtualMachines/osDisk.Uri | Задает универсальный код ресурса (URI) виртуального жесткого диска. |
| Microsoft.Compute/virtualMachines/sku.name | Задайте размер виртуальной машины. |

**Microsoft.Compute/virtualMachines/extensions**

| Alias | Описание |
| ----- | ----------- |
| Microsoft.Compute/virtualMachines/extensions/publisher | Задает имя издателя расширения. |
| Microsoft.Compute/virtualMachines/extensions/type | Задает тип расширения. |
| Microsoft.Compute/virtualMachines/extensions/typeHandlerVersion | Задает версию расширения. |

**Microsoft.Compute/virtualMachineScaleSets**

| Alias | Описание |
| ----- | ----------- |
| Microsoft.Compute/imageId | Задайте идентификатор образа, используемого для создания виртуальной машины. |
| Microsoft.Compute/imageOffer | Задает предложение образа платформы или образа Marketplace, используемого для создания виртуальной машины. |
| Microsoft.Compute/imagePublisher | Задает издатель образа платформы или образа Marketplace, используемого для создания виртуальной машины. |
| Microsoft.Compute/imageSku | Задает номер SKU образа платформы или образа Marketplace, используемого для создания виртуальной машины. |
| Microsoft.Compute/imageVersion | Задает версию образа платформы или образа Marketplace, используемого для создания виртуальной машины. |
| Microsoft.Compute/licenseType | Указывает, используется ли для образа или диска локальная лицензия. Это значение используется только для образов, содержащих операционную систему Windows Server. |
| Microsoft.Compute/VirtualMachineScaleSets/computerNamePrefix | Задает префикс имени компьютера для всех виртуальных машин в масштабируемом наборе. |
| Microsoft.Compute/VirtualMachineScaleSets/osdisk.imageUrl | Задает универсальный код ресурса (URI) большого двоичного объекта для пользовательского образа. |
| Microsoft.Compute/VirtualMachineScaleSets/osdisk.vhdContainers | Задает URL-адреса контейнеров, которые используются для хранения ОС для масштабируемого набора. |
| Microsoft.Compute/VirtualMachineScaleSets/sku.name | Задает размер виртуальных машин в масштабируемом наборе. |
| Microsoft.Compute/VirtualMachineScaleSets/sku.tier | Задает уровень виртуальных машин в масштабируемом наборе. |
  
**Microsoft.Network/applicationGateways**

| Alias | Описание |
| ----- | ----------- |
| Microsoft.Network/applicationGateways/sku.name | Задает размер шлюза. |

**Microsoft.Network/virtualNetworkGateways**

| Alias | Описание |
| ----- | ----------- |
| Microsoft.Network/virtualNetworkGateways/gatewayType | Задает тип шлюза виртуальной сети. |
| Microsoft.Network/virtualNetworkGateways/sku.name | Задает номера SKU шлюза. |

**Microsoft.Sql/servers**

| Alias | Описание |
| ----- | ----------- |
| Microsoft.Sql/servers/version | Задает версию сервера. |

**Microsoft.Sql/databases**

| Alias | Описание |
| ----- | ----------- |
| Microsoft.Sql/servers/databases/edition | Задает выпуск базы данных. |
| Microsoft.Sql/servers/databases/elasticPoolName | Задает имя эластичного пула, в котором размещена база данных. |
| Microsoft.Sql/servers/databases/requestedServiceObjectiveId | Задает идентификатор настроенного целевого уровня обслуживания для базы данных. |
| Microsoft.Sql/servers/databases/requestedServiceObjectiveName | Задает имя настроенного целевого уровня обслуживания для базы данных.  |

**Microsoft.Sql/elasticpools**

| Alias | Описание |
| ----- | ----------- |
| servers/elasticpools | Microsoft.Sql/servers/elasticPools/dtu | Задает совокупное число общих единиц DTU для эластичного пула базы данных. |
| servers/elasticpools | Microsoft.Sql/servers/elasticPools/edition | Задает выпуск эластичного пула. |

**Microsoft.Storage/storageAccounts**

| Alias | Описание |
| ----- | ----------- |
| Microsoft.Storage/storageAccounts/accessTier | Задает уровень доступа, используемый для выставления счетов. |
| Microsoft.Storage/storageAccounts/accountType | Задает имя SKU. |
| Microsoft.Storage/storageAccounts/enableBlobEncryption | Указывает, шифрует ли служба данные, хранящиеся в службе хранилища BLOB-объектов. |
| Microsoft.Storage/storageAccounts/enableFileEncryption | Указывает, шифрует ли служба данные, хранящиеся в службе хранилища файлов. |
| Microsoft.Storage/storageAccounts/sku.name | Задает имя SKU. |
| Microsoft.Storage/storageAccounts/supportsHttpsTrafficOnly | Позволяет разрешить только передачу трафика HTTPS в службу хранилища. |

## <a name="policy-sets"></a>Наборы политик

Наборы политик позволяют сгруппировать несколько связанных определений политик. Набор политик упрощает назначение и управление, потому что с группой можно работать как с единым элементом. Например, можно сгруппировать все связанные политики тегов в одном наборе политик. Вместо назначения каждой политики по отдельности применяется весь набор.
 
В следующем примере показано, как создать набор политик для обработки двух тегов (costCenter и productName). В нем используются две встроенные политики для применения значения тега по умолчанию и принудительной установки значения тега. Набор политик объявляет два параметра для многократного использования: costCenterValue и productNameValue. Он ссылается на два встроенных определения политик множество раз с различными параметрами. Для каждого параметра можно указать либо фиксированное значение, как показано для tagName, либо значение параметра из набора политик, как показано для tagValue.

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
        "policyDefinitions": [
            {
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

Набор политик можно добавить с помощью команды PowerShell **New-AzureRMPolicySetDefinition**.

Для операций REST следует использовать версию API **2017-06-01-preview**, как показано в следующем примере:

```
PUT /subscriptions/<subId>/providers/Microsoft.Authorization/policySetDefinitions/billingTagsPolicySet?api-version=2017-06-01-preview
```

## <a name="next-steps"></a>Дальнейшие действия
* Определив правило политики, назначьте эту политику для области. Сведения о назначении политик с помощью портала см. в статье [Назначение политик ресурсов и управление ими с помощью портала Azure](resource-manager-policy-portal.md). Сведения о назначении политик с помощью REST API, PowerShell или Azure CLI см. в статье [Назначение политик ресурсов и управление ими](resource-manager-policy-create-assign.md).
* Примеры политик см. в [репозитории GitHub для политик ресурсов Azure](https://github.com/Azure/azure-policy-samples).
* Руководство по использованию Resource Manager для эффективного управления подписками в организациях см [Azure enterprise scaffold - prescriptive subscription governance](resource-manager-subscription-governance.md) (Шаблон Azure для организаций. Рекомендуемая система управления подпиской).
* Схема политики опубликована на странице [http://schema.management.azure.com/schemas/2015-10-01-preview/policyDefinition.json](http://schema.management.azure.com/schemas/2015-10-01-preview/policyDefinition.json). 


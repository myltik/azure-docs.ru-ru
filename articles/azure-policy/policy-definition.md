---
title: Структура определения политики Azure |Документация Майкрософт
description: Описывается, как определение политики ресурсов, описывающее условия применения данной политики и соответствующее действие, используется службой "Политика Azure", чтобы установить соглашения о ресурсах в вашей организации.
services: azure-policy
keywords: ''
author: DCtheGeek
ms.author: dacoulte
ms.date: 04/18/2018
ms.topic: article
ms.service: azure-policy
ms.custom: ''
ms.openlocfilehash: 8b89e1c8ccfcfd7b53ecdd9172590424d1c7ae4c
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2018
---
# <a name="azure-policy-definition-structure"></a>Структура определения службы "Политика Azure"

Определение политики ресурсов, используемое службой "Политика Azure" и описывающее условия применения данной политики и соответствующее действие, позволяет установить соглашения о ресурсах в вашей организации. Это соглашения помогут вам контролировать расходы и управлять ресурсами. Например, можно указать, что разрешены только определенные типы виртуальных машин. Кроме того, можно требовать наличие определенного тега для каждого ресурса. Политики наследуются всеми дочерними ресурсами. Это значит, что политики, применяемые к группе ресурсов, применяются также ко всем ресурсам в этой группе.

Для создания определения политики используется JSON. Определение политики содержит следующие элементы:

* mode;
* parameters
* display name
* description
* policy rule
  * logical evaluation
  * effect

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
* `all`: оценка групп ресурсов и всех типов ресурсов.
* `indexed`: оцениваются только типы ресурсов, которые поддерживают теги и расположение.

В большинстве случаев рекомендуется задать для параметра **mode** значение `all`. Во всех определениях политик, создаваемых на портале, используется режим `all`. Если используется PowerShell или Azure CLI, необходимо указать параметр **mode** вручную. Если в определении политики не задано значение для параметра **mode**, для обеспечения обратной совместимости используется значение по умолчанию `indexed`.

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

* `"location"`
* `"resourceTypes"`
* `"storageSkus"`
* `"vmSKUs"`
* `"existingResourceGroups"`
* `"omsWorkspace"`

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
* `"notEquals": "value"`
* `"like": "value"`
* `"notLike": "value"`
* `"match": "value"`
* `"notMatch": "value"`
* `"contains": "value"`
* `"notContains": "value"`
* `"in": ["value1","value2"]`
* `"notIn": ["value1","value2"]`
* `"containsKey": "keyName"`
* `"notContainsKey": "keyName"`
* `"exists": "bool"`

При использовании условий **like** и **notLike** можно указать в значении подстановочный знак (*).

При использовании условий **match** и **notMatch** укажите `#` для представления цифры, `?` для буквы и любой другой символ — для представления фактического символа. Примеры приведены в разделе [Утвержденные образы виртуальных машин](scripts/allowed-custom-images.md).

### <a name="fields"></a>Поля
Условия создаются на основе полей. Поле представляет свойства в полезных данных запроса ресурса, используемые для описания состояния ресурса.  

Поддерживаются следующие поля.

* `name`
* `fullName`
  * Возвращает полное имя ресурса, включая все родительские ресурсы (например, "myServer/myDatabase").
* `kind`
* `type`
* `location`
* `tags`
* `tags.tagName`
* `tags[tagName]`
  * Этот синтаксис в скобках поддерживает имена тегов, содержащие точки.
* Список псевдонимов свойств указан в разделе [Псевдонимы](#aliases).

### <a name="alternative-accessors"></a>Альтернативные методы доступа
**Field** (поле) — основной метод доступа, используемый в правилах политики. Он непосредственно проверяет ресурс, который вычисляется. Однако политика поддерживает еще один метод доступа — **source**.

```json
"source": "action",
"equals": "Microsoft.Compute/virtualMachines/write"
```

**Source** поддерживает только одно значение, **action**. Action возвращает действие авторизации запроса, который оценивается. Действия авторизации предоставляются в разделе авторизации [журнала действий](../monitoring-and-diagnostics/monitoring-activity-log-schema.md).

Когда политика оценивает существующие ресурсы в фоновом режиме, она задает в качестве **action** действие авторизации `/write` для типа ресурса.

### <a name="effect"></a>Результат
Политика поддерживает следующие типы действий:

* **Deny** создает событие в журнале аудита и отклоняет запрос;
* **Audit** создает событие в журнале аудита, но выполняет запрос;
* **Append** добавляет в запрос некоторый набор полей;
* **AuditIfNotExists** включает аудит, если ресурс не существует;
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

С помощью параметров **AuditIfNotExists** и **DeployIfNotExists** можно проверить существование связанного ресурса и применить правило и соответствующее действие, если такой ресурс не существует. Например можно потребовать, чтобы наблюдатель за сетями был развернут для всех виртуальных сетей.
Пример аудита наличия развернутого расширения для виртуальных машин приведен в разделе [Проверка наличия расширения](scripts/audit-ext-not-exist.md).


## <a name="aliases"></a>Псевдонимы

Псевдонимы свойств позволяют обращаться к определенным свойствам для типа ресурса. Псевдонимы позволяют ограничить значения или условия, разрешенные для свойства ресурса. Каждый псевдоним сопоставляется с путями в разных версиях API для заданного типа ресурса. Во время оценки политики модуль политики получает путь свойства для этой версии API.

**Microsoft.Cache/Redis**

| Alias | ОПИСАНИЕ |
| ----- | ----------- |
| Microsoft.Cache/Redis/enableNonSslPort | Указывает, включен ли на сервере Redis порт без поддержки SSL (6379). |
| Microsoft.Cache/Redis/shardCount | Задает число сегментов, создаваемых в кэше кластера уровня "Премиум".  |
| Microsoft.Cache/Redis/sku.capacity | Задает размер развертываемого кэша Redis.  |
| Microsoft.Cache/Redis/sku.family | Задает используемое семейство SKU. |
| Microsoft.Cache/Redis/sku.name | Задает тип развертываемого кэша Redis. |

**Microsoft.Cdn/profiles**

| Alias | ОПИСАНИЕ |
| ----- | ----------- |
| Microsoft.CDN/profiles/sku.name | Задает имя ценовой категории. |

**Microsoft.Compute/disks**

| Alias | ОПИСАНИЕ |
| ----- | ----------- |
| Microsoft.Compute/imageOffer | Задает предложение образа платформы или образа Marketplace, используемого для создания виртуальной машины. |
| Microsoft.Compute/imagePublisher | Задает издатель образа платформы или образа Marketplace, используемого для создания виртуальной машины. |
| Microsoft.Compute/imageSku | Задает номер SKU образа платформы или образа Marketplace, используемого для создания виртуальной машины. |
| Microsoft.Compute/imageVersion | Задает версию образа платформы или образа Marketplace, используемого для создания виртуальной машины. |


**Microsoft.Compute/virtualMachines**

| Alias | ОПИСАНИЕ |
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
| Microsoft.Compute/virtualMachines/availabilitySet.id | Задает идентификатор группы доступности для виртуальной машины. |

**Microsoft.Compute/virtualMachines/extensions**

| Alias | ОПИСАНИЕ |
| ----- | ----------- |
| Microsoft.Compute/virtualMachines/extensions/publisher | Задает имя издателя расширения. |
| Microsoft.Compute/virtualMachines/extensions/type | Задает тип расширения. |
| Microsoft.Compute/virtualMachines/extensions/typeHandlerVersion | Задает версию расширения. |

**Microsoft.Compute/virtualMachineScaleSets**

| Alias | ОПИСАНИЕ |
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

| Alias | ОПИСАНИЕ |
| ----- | ----------- |
| Microsoft.Network/applicationGateways/sku.name | Задает размер шлюза. |

**Microsoft.Network/virtualNetworkGateways**

| Alias | ОПИСАНИЕ |
| ----- | ----------- |
| Microsoft.Network/virtualNetworkGateways/gatewayType | Задает тип шлюза виртуальной сети. |
| Microsoft.Network/virtualNetworkGateways/sku.name | Задает номера SKU шлюза. |

**Microsoft.Sql/servers**

| Alias | ОПИСАНИЕ |
| ----- | ----------- |
| Microsoft.Sql/servers/version | Задает версию сервера. |

**Microsoft.Sql/databases**

| Alias | ОПИСАНИЕ |
| ----- | ----------- |
| Microsoft.Sql/servers/databases/edition | Задает выпуск базы данных. |
| Microsoft.Sql/servers/databases/elasticPoolName | Задает имя эластичного пула, в котором размещена база данных. |
| Microsoft.Sql/servers/databases/requestedServiceObjectiveId | Задает идентификатор настроенного целевого уровня обслуживания для базы данных. |
| Microsoft.Sql/servers/databases/requestedServiceObjectiveName | Задает имя настроенного целевого уровня обслуживания для базы данных.  |

**Microsoft.Sql/elasticpools**

| Alias | ОПИСАНИЕ |
| ----- | ----------- |
| servers/elasticpools | Microsoft.Sql/servers/elasticPools/dtu | Задает совокупное число общих единиц DTU для эластичного пула базы данных. |
| servers/elasticpools | Microsoft.Sql/servers/elasticPools/edition | Задает выпуск эластичного пула. |

**Microsoft.Storage/storageAccounts**

| Alias | ОПИСАНИЕ |
| ----- | ----------- |
| Microsoft.Storage/storageAccounts/accessTier | Задает уровень доступа, используемый для выставления счетов. |
| Microsoft.Storage/storageAccounts/accountType | Задает имя SKU. |
| Microsoft.Storage/storageAccounts/enableBlobEncryption | Указывает, шифрует ли служба данные, хранящиеся в службе хранилища BLOB-объектов. |
| Microsoft.Storage/storageAccounts/enableFileEncryption | Указывает, шифрует ли служба данные, хранящиеся в службе хранилища файлов. |
| Microsoft.Storage/storageAccounts/sku.name | Задает имя SKU. |
| Microsoft.Storage/storageAccounts/supportsHttpsTrafficOnly | Позволяет разрешить только передачу трафика HTTPS в службу хранилища. |
| Microsoft.Storage/storageAccounts/networkAcls.virtualNetworkRules[*].id | Проверяет, включена ли конечная точка службы виртуальной сети. |

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

## <a name="next-steps"></a>Дополнительная информация

- Ознакомьтесь с примерами шаблонов политик Azure [здесь](json-samples.md).

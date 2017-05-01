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
ms.date: 03/30/2017
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: d75088bd83b0b70c889388c95331bb56fe9ba15b
ms.lasthandoff: 04/03/2017


---
# <a name="resource-policy-overview"></a>Общие сведения о политике ресурсов
Политики ресурсов позволяют настроить определенные соглашения для ресурсов в организации. Это соглашения помогут вам контролировать расходы и управлять ресурсами. Например, вы можете разрешить создавать только определенные типы виртуальных машин или настроить, чтобы у каждого ресурса обязательно присутствовал определенный тег. Политики наследуются всеми дочерними ресурсами. Это значит, что политики, применяемые к группе ресурсов, применяются также ко всем ресурсам в этой группе.

При работе с политиками нужно знать две важные концепции:

* определение политики описывает, когда и как политика будет применяться;
* назначение политики привязывает эту политику к определенной области (к подписке или группе ресурсов).

В этой статье рассматривается только определение политики. Сведения о назначении политик см. в статьях [Назначение политик ресурсов и управление ими с помощью портала Azure](resource-manager-policy-portal.md) и [Назначение политик ресурсов и управление ими](resource-manager-policy-create-assign.md).

В Azure доступно несколько встроенных определений политик, которые сокращают число соответствующих политик. Если одно из встроенных определений политик подходит для вашего сценария, назначьте его соответствующей области.

Политики оцениваются при создании и обновлении ресурсов (операции PUT и PATCH).

> [!NOTE]
> Сейчас политика не вычисляет типы ресурсов, которые не поддерживают теги, вид и расположение, например тип ресурса Microsoft.Resources/deployments. Их поддержка будет добавлена в будущем. Чтобы избежать проблем с обратной совместимостью, необходимо явно указывать тип при создании политик. Например, политика тегов, которая не указывает типы, применяется для всех типов. В этом случае может произойти ошибка развертывания шаблона, если существует вложенный ресурс, который не поддерживает тег, и в вычисление политики был добавлен тип ресурса развертывания. 
> 
> 

## <a name="how-is-it-different-from-rbac"></a>Чем это отличается от управления доступом на основе ролей?
Существует ряд ключевых различий между политиками и управлением доступом на основе ролей (RBAC). RBAC определяет действия **пользователя** в различных областях. Например, если вам назначена роль участника для группы ресурсов в требуемой области, вы можете вносить изменения в эту группу ресурсов. Политика определяет свойства **ресурсов** во время их развертывания. Например, с помощью политик можно управлять типами ресурсов, которые могут быть подготовлены, или ограничить расположения, в которых могут быть подготовлены ресурсы. В отличие от RBAC, политика представляет собой систему разрешения по умолчанию и явного запрета. 

Чтобы использовать политики, нужно пройти аутентификацию с помощью RBAC. В частности, для учетной записи должны быть предоставлены:

* `Microsoft.Authorization/policydefinitions/write` разрешение на создание политики;
* `Microsoft.Authorization/policyassignments/write` разрешение на назначение политики. 

Эти разрешения не включаются в роль **Участник**.

## <a name="policy-definition-structure"></a>Структура определения политики
Для создания определения политики используется JSON. Определение политики содержит следующие элементы:

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

Допускается вложение логических операторов. В следующем примере представлены вложенные операторы **Not** и **And**. 

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
* `"contains": "value"`
* `"in": ["value1","value2"]`
* `"containsKey": "keyName"`
* `"exists": "bool"`

В значении для условия **like** можно использовать подстановочный знак (*).

### <a name="fields"></a>Поля
Условия создаются на основе полей. Поле представляет свойства в полезных данных запроса ресурса, используемые для описания состояния ресурса.  

Поддерживаются следующие поля.

* `name`
* `kind`
* `type`
* `location`
* `tags`
* `tags.*` 
* Псевдонимы свойств

Псевдонимы свойств позволяют обращаться к определенным свойствам для типа ресурса. Поддерживается следующие псевдонимы.

* Microsoft.CDN/profiles/sku.name
* Microsoft.Compute/virtualMachines/imageOffer
* Microsoft.Compute/virtualMachines/imagePublisher
* Microsoft.Compute/virtualMachines/sku.name
* Microsoft.Compute/virtualMachines/imageSku 
* Microsoft.Compute/virtualMachines/imageVersion
* Microsoft.SQL/servers/databases/edition
* Microsoft.SQL/servers/databases/elasticPoolName
* Microsoft.SQL/servers/databases/requestedServiceObjectiveId
* Microsoft.SQL/servers/databases/requestedServiceObjectiveName
* Microsoft.SQL/servers/elasticPools/dtu
* Microsoft.SQL/servers/elasticPools/edition
* Microsoft.SQL/servers/version
* Microsoft.Storage/storageAccounts/accessTier
* Microsoft.Storage/storageAccounts/enableBlobEncryption
* Microsoft.Storage/storageAccounts/sku.name
* Microsoft.Web/serverFarms/sku.name

### <a name="effect"></a>Результат
Политика поддерживает три типа результатов — `deny`, `audit` и `append`. 

* **deny** создает событие в журнале аудита и отклоняет запрос.
* **audit** создает событие в журнале аудита, но выполняет запрос.
* **append** добавляет в запрос некоторый набор полей. 

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

## <a name="policy-examples"></a>Примеры политик

Следующие статьи содержат примеры политик.

* Примеры политик для тегов см. в статье [Apply resource policies for tags](resource-manager-policy-tags.md) (Применение политик ресурсов для тегов).
* Примеры политик для хранения см. в статье [Применение политик ресурсов Azure для учетных записей хранения](resource-manager-policy-storage.md).
* Примеры политик для виртуальных машин есть в статьях о применении политик к виртуальным машинам Azure Resource Manager [для Linux](../virtual-machines/linux/policy.md?toc=%2fazure%2fazure-resource-manager%2ftoc.json) и [для Windows](../virtual-machines/windows/policy.md?toc=%2fazure%2fazure-resource-manager%2ftoc.json).

### <a name="allowed-resource-locations"></a>Набор допустимых расположений ресурсов
Чтобы указать, какие расположения можно использовать, см. пример из раздела [Структура определения политики](#policy-definition-structure). Чтобы назначить это определение политики, используйте встроенную политику с идентификатором ресурса `/providers/Microsoft.Authorization/policyDefinitions/e56962a6-4747-49cd-b67b-bf8b01975c4c`.

### <a name="not-allowed-resource-locations"></a>Набор недопустимых расположений ресурсов
Чтобы указать, какие расположения нельзя использовать, используйте следующее определение политики:

```json
{
  "properties": {
    "parameters": {
      "notAllowedLocations": {
        "type": "array",
        "metadata": {
          "description": "The list of locations that are not allowed when deploying resources",
          "strongType": "location",
          "displayName": "Not allowed locations"
        }
      }
    },
    "displayName": "Not allowed locations",
    "description": "This policy enables you to block locations that your organization can specify when deploying resources.",
    "policyRule": {
      "if": {
        "field": "location",
        "in": "[parameters('notAllowedLocations')]"
      },
      "then": {
        "effect": "deny"
      }
    }
  }
}
```

### <a name="allowed-resource-types"></a>Допустимые типы ресурсов
В следующем примере показана политика, допускающая развертывание только для типов ресурсов Microsoft.Resources, Microsoft.Compute, Microsoft.Storage и Microsoft.Network. Все остальные развертывания будут отклонены:

```json
{
  "if": {
    "not": {
      "anyOf": [
        {
          "field": "type",
          "like": "Microsoft.Resources/*"
        },
        {
          "field": "type",
          "like": "Microsoft.Compute/*"
        },
        {
          "field": "type",
          "like": "Microsoft.Storage/*"
        },
        {
          "field": "type",
          "like": "Microsoft.Network/*"
        }
      ]
    }
  },
  "then": {
    "effect": "deny"
  }
}
```

### <a name="set-naming-convention"></a>Соглашение об именовании
В следующем примере показано, как использовать подстановочный знак в условии **like**. Это условие означает следующее: если имя не соответствует заданному шаблону (namePrefix\*nameSuffix), запрос будет отклонен.

```json
{
  "if": {
    "not": {
      "field": "name",
      "like": "namePrefix*nameSuffix"
    }
  },
  "then": {
    "effect": "deny"
  }
}
```

## <a name="next-steps"></a>Дальнейшие действия
* Определив правило политики, назначьте эту политику для области. Сведения о назначении политик с помощью портала см. в статье [Назначение политик ресурсов и управление ими с помощью портала Azure](resource-manager-policy-portal.md). Сведения о назначении политик с помощью REST API, PowerShell или Azure CLI см. в статье [Назначение политик ресурсов и управление ими](resource-manager-policy-create-assign.md).
* Руководство по использованию Resource Manager для эффективного управления подписками в организациях см [Azure enterprise scaffold - prescriptive subscription governance](resource-manager-subscription-governance.md) (Шаблон Azure для организаций. Рекомендуемая система управления подпиской).
* Схема политики опубликована на странице [http://schema.management.azure.com/schemas/2015-10-01-preview/policyDefinition.json](http://schema.management.azure.com/schemas/2015-10-01-preview/policyDefinition.json). 



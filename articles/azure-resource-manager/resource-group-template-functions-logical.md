---
title: Логические функции шаблона Azure Resource Manager | Документация Майкрософт
description: Описываются функции, используемые в шаблоне Azure Resource Manager для определения логических значений.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: ''
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/05/2017
ms.author: tomfitz
ms.openlocfilehash: d8a7ae412fc80dff7bd91c1cdc5d4fcd985e07f4
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
ms.locfileid: "34359073"
---
# <a name="logical-functions-for-azure-resource-manager-templates"></a>Логические функции для шаблонов Azure Resource Manager

Resource Manager предоставляет ряд функций для выполнения сравнений в шаблонах.

* [and](#and) (и);
* [bool](#bool);
* [if](#if) (если);
* [not](#not) (не);
* [or](#or) (или).

## <a name="and"></a>и
`and(arg1, arg2)`

Проверяет, соответствуют ли истине два значения параметров.

### <a name="parameters"></a>Параметры

| Параметр | Обязательно | type | ОПИСАНИЕ |
|:--- |:--- |:--- |:--- |
| arg1 |Yes |Логическое |Первое значение, которое необходимо проверить на соответствие истине. |
| arg2 |Yes |Логическое |Второе значение, которое необходимо проверить на соответствие истине. |

### <a name="return-value"></a>Возвращаемое значение

Возвращает результат **True** (Истина), если оба значения соответствуют истине. В противном случае — **False** (Ложь).

### <a name="examples"></a>Примеры

В следующем [примере шаблона](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/andornot.json) показано, как использовать логические функции.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [ ],
    "outputs": {
        "andExampleOutput": {
            "value": "[and(bool('true'), bool('false'))]",
            "type": "bool"
        },
        "orExampleOutput": {
            "value": "[or(bool('true'), bool('false'))]",
            "type": "bool"
        },
        "notExampleOutput": {
            "value": "[not(bool('true'))]",
            "type": "bool"
        }
    }
}
```

Выходные данные из предыдущего примера:

| ИМЯ | type | Значение |
| ---- | ---- | ----- |
| andExampleOutput | Bool | Ложь |
| orExampleOutput | Bool | Истина |
| notExampleOutput | Bool | Ложь |

Развернуть этот пример шаблона с помощью Azure CLI можно так:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/andornot.json
```

Развернуть этот пример шаблона с помощью PowerShell можно так:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/andornot.json
```

## <a name="bool"></a>bool
`bool(arg1)`

Преобразует параметр в логическое значение.

### <a name="parameters"></a>Параметры

| Параметр | Обязательно | type | ОПИСАНИЕ |
|:--- |:--- |:--- |:--- |
| arg1 |Yes |строка или целое число |Значение, которое необходимо преобразовать в логическое. |

### <a name="return-value"></a>Возвращаемое значение
Логическое выражение преобразованного значения.

### <a name="examples"></a>Примеры

В следующем [примере шаблона](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/bool.json) показано, как использовать функцию bool со строкой или целым числом.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "trueString": {
            "value": "[bool('true')]",
            "type" : "bool"
        },
        "falseString": {
            "value": "[bool('false')]",
            "type" : "bool"
        },
        "trueInt": {
            "value": "[bool(1)]",
            "type" : "bool"
        },
        "falseInt": {
            "value": "[bool(0)]",
            "type" : "bool"
        }
    }
}
```

Выходные данные из предыдущего примера со значениями по умолчанию:

| ИМЯ | type | Значение |
| ---- | ---- | ----- |
| trueString | Bool | Истина |
| falseString | Bool | Ложь |
| trueInt | Bool | Истина |
| falseInt | Bool | Ложь |

Развернуть этот пример шаблона с помощью Azure CLI можно так:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/bool.json
```

Развернуть этот пример шаблона с помощью PowerShell можно так:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/bool.json
```

## <a name="if"></a>if
`if(condition, trueValue, falseValue)`

Возвращает значение в зависимости от того, выполняется ли условие.

### <a name="parameters"></a>Параметры

| Параметр | Обязательно | type | ОПИСАНИЕ |
|:--- |:--- |:--- |:--- |
| condition |Yes |Логическое |Значение, которое необходимо проверить на соответствие истине. |
| trueValue |Yes | строка, целое число, объект или массив |Возвращаемое значение, если условие выполняется. |
| falseValue |Yes | строка, целое число, объект или массив |Возвращаемое значение, если условие не выполняется. |

### <a name="return-value"></a>Возвращаемое значение

Возвращает второй параметр, если первый параметр имеет значение **True** (Истина). В противном случае возвращает третий параметр.

### <a name="remarks"></a>Примечания

Эту функцию можно использовать, чтобы условно задавать свойство ресурса. Следующий пример не является полным шаблоном, но в нем показаны соответствующие части для условной настройки группы доступности.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        ...
        "availabilitySet": {
            "type": "string",
            "allowedValues": [
                "yes",
                "no"
            ]
        }
    },
    "variables": {
        ...
        "availabilitySetName": "availabilitySet1",
        "availabilitySet": {
            "id": "[resourceId('Microsoft.Compute/availabilitySets',variables('availabilitySetName'))]"
        }
    },
    "resources": [
        {
            "condition": "[equals(parameters('availabilitySet'),'yes')]",
            "type": "Microsoft.Compute/availabilitySets",
            "name": "[variables('availabilitySetName')]",
            ...
        },
        {
            "apiVersion": "2016-03-30",
            "type": "Microsoft.Compute/virtualMachines",
            "properties": {
                "availabilitySet": "[if(equals(parameters('availabilitySet'),'yes'), variables('availabilitySet'), json('null'))]",
                ...
            }
        },
        ...
    ],
    ...
}
```

### <a name="examples"></a>Примеры

В следующем [примере шаблона](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/if.json) показано, как использовать функцию `if`:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
    ],
    "outputs": {
        "yesOutput": {
            "type": "string",
            "value": "[if(equals('a', 'a'), 'yes', 'no')]"
        },
        "noOutput": {
            "type": "string",
            "value": "[if(equals('a', 'b'), 'yes', 'no')]"
        }
    }
}
```

Выходные данные из предыдущего примера:

| ИМЯ | type | Значение |
| ---- | ---- | ----- |
| yesOutput | Строка | Да |
| noOutput | Строка | Нет |

Развернуть этот пример шаблона с помощью Azure CLI можно так:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/if.json
```

Развернуть этот пример шаблона с помощью PowerShell можно так:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/if.json
```

## <a name="not"></a>not
`not(arg1)`

Преобразует логическое значение в противоположное ему значение.

### <a name="parameters"></a>Параметры

| Параметр | Обязательно | type | ОПИСАНИЕ |
|:--- |:--- |:--- |:--- |
| arg1 |Yes |Логическое |Значение, которое необходимо преобразовать. |

### <a name="return-value"></a>Возвращаемое значение

Возвращает значение **True** (Истина), если параметр имеет значение **False** (Ложь). Возвращает значение **False** (Ложь), если параметр имеет значение **True** (Истина).

### <a name="examples"></a>Примеры

В следующем [примере шаблона](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/andornot.json) показано, как использовать логические функции.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [ ],
    "outputs": {
        "andExampleOutput": {
            "value": "[and(bool('true'), bool('false'))]",
            "type": "bool"
        },
        "orExampleOutput": {
            "value": "[or(bool('true'), bool('false'))]",
            "type": "bool"
        },
        "notExampleOutput": {
            "value": "[not(bool('true'))]",
            "type": "bool"
        }
    }
}
```

Выходные данные из предыдущего примера:

| ИМЯ | type | Значение |
| ---- | ---- | ----- |
| andExampleOutput | Bool | Ложь |
| orExampleOutput | Bool | Истина |
| notExampleOutput | Bool | Ложь |

Развернуть этот пример шаблона с помощью Azure CLI можно так:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/andornot.json
```

Развернуть этот пример шаблона с помощью PowerShell можно так:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/andornot.json
```

В следующем [примере шаблона](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/not-equals.json) используется **not** и [equals](resource-group-template-functions-comparison.md#equals).

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
    ],
    "outputs": {
        "checkNotEquals": {
            "type": "bool",
            "value": "[not(equals(1, 2))]"
        }
    }
```

Выходные данные из предыдущего примера:

| ИМЯ | type | Значение |
| ---- | ---- | ----- |
| checkNotEquals | Bool | Истина |

Развернуть этот пример шаблона с помощью Azure CLI можно так:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/not-equals.json
```

Развернуть этот пример шаблона с помощью PowerShell можно так:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/not-equals.json
```

## <a name="or"></a>или
`or(arg1, arg2)`

Проверяет, соответствует ли истине одно из значений параметров.

### <a name="parameters"></a>Параметры

| Параметр | Обязательно | type | ОПИСАНИЕ |
|:--- |:--- |:--- |:--- |
| arg1 |Yes |Логическое |Первое значение, которое необходимо проверить на соответствие истине. |
| arg2 |Yes |Логическое |Второе значение, которое необходимо проверить на соответствие истине. |

### <a name="return-value"></a>Возвращаемое значение

Возвращает результат **True** (Истина), если одно из значений соответствует истине. В противном случае — **False** (Ложь).

### <a name="examples"></a>Примеры

В следующем [примере шаблона](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/andornot.json) показано, как использовать логические функции.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [ ],
    "outputs": {
        "andExampleOutput": {
            "value": "[and(bool('true'), bool('false'))]",
            "type": "bool"
        },
        "orExampleOutput": {
            "value": "[or(bool('true'), bool('false'))]",
            "type": "bool"
        },
        "notExampleOutput": {
            "value": "[not(bool('true'))]",
            "type": "bool"
        }
    }
}
```

Выходные данные из предыдущего примера:

| ИМЯ | type | Значение |
| ---- | ---- | ----- |
| andExampleOutput | Bool | Ложь |
| orExampleOutput | Bool | Истина |
| notExampleOutput | Bool | Ложь |

Развернуть этот пример шаблона с помощью Azure CLI можно так:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/andornot.json
```

Развернуть этот пример шаблона с помощью PowerShell можно так:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/andornot.json
```

## <a name="next-steps"></a>Дополнительная информация
* Описание разделов в шаблоне Azure Resource Manager см. в статье [Создание шаблонов Azure Resource Manager](resource-group-authoring-templates.md).
* Инструкции по объединению нескольких шаблонов см. в статье [Использование связанных шаблонов в Azure Resource Manager](resource-group-linked-templates.md).
* Указания по выполнению заданного количества циклов итерации при создании типа ресурса см. в статье [Создание нескольких экземпляров ресурсов в Azure Resource Manager](resource-group-create-multiple.md).
* Указания по развертыванию созданного шаблона см. в статье, посвященной [развертыванию приложения с помощью шаблона Azure Resource Manager](resource-group-template-deploy.md).


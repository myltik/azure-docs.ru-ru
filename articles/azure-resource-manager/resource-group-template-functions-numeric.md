---
title: Числовые функции шаблона Azure Resource Manager | Документация Майкрософт
description: Описывает функции, используемые в шаблоне Azure Resource Manager для работы с числами.
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
ms.date: 11/08/2017
ms.author: tomfitz
ms.openlocfilehash: 4fc17b997c44560199e65edb01d20c6a24e49877
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
ms.locfileid: "34360008"
---
# <a name="numeric-functions-for-azure-resource-manager-templates"></a>Числовые функции для шаблонов Azure Resource Manager

Диспетчер ресурсов предоставляет следующие функции для работы с целыми числами:

* [добавление](#add)
* [copyIndex](#copyindex)
* [div](#div)
* [float](#float)
* [int](#int)
* [max](#max)
* [min](#min)
* [mod (модуль)](#mod)
* [mul](#mul)
* [sub](#sub)

<a id="add" />

## <a name="add"></a>добавление
`add(operand1, operand2)`

Возвращает сумму двух указанных целочисленных значений.

### <a name="parameters"></a>Параметры

| Параметр | Обязательно | type | ОПИСАНИЕ |
|:--- |:--- |:--- |:--- | 
|операнд1 |Yes |int |Первое слагаемое. |
|операнд2 |Yes |int |Второе слагаемое. |

### <a name="return-value"></a>Возвращаемое значение

Целое число, содержащее сумму параметров.

### <a name="example"></a>Пример

В следующем [примере шаблона](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/add.json) суммируются два параметра.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "first": {
            "type": "int",
            "defaultValue": 5,
            "metadata": {
                "description": "First integer to add"
            }
        },
        "second": {
            "type": "int",
            "defaultValue": 3,
            "metadata": {
                "description": "Second integer to add"
            }
        }
    },
    "resources": [
    ],
    "outputs": {
        "addResult": {
            "type": "int",
            "value": "[add(parameters('first'), parameters('second'))]"
        }
    }
}
```

Выходные данные из предыдущего примера со значениями по умолчанию:

| ИМЯ | type | Значение |
| ---- | ---- | ----- |
| addResult | int | 8 |

Развернуть этот пример шаблона с помощью Azure CLI можно так:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/add.json
```

Развернуть этот пример шаблона с помощью PowerShell можно так:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/add.json 
```

<a id="copyindex" />

## <a name="copyindex"></a>copyIndex
`copyIndex(loopName, offset)`

Возвращает индекс цикла итерации. 

### <a name="parameters"></a>Параметры

| Параметр | Обязательно | type | ОПИСАНИЕ |
|:--- |:--- |:--- |:--- |
| loopName | Нет  | строка | Имя цикла для получения итерации. |
| offset |Нет  |int |Число, добавляемое к отсчитываемому от нуля значению итерации. |

### <a name="remarks"></a>Примечания

Эта функция всегда используется с объектом **copy**. Если значение **offset** не указано, возвращается текущее значение итерации. Значение итерации начинается с нуля. Циклы итерации можно использовать при определении ресурсов или переменных.

Свойство **loopName** позволяет указать, на какую итерацию ссылается copyIndex: ресурса или свойства. Если значение **loopName** не указано, используется текущая итерация типа ресурса. Укажите значение **loopName** при выполнении итерации по свойству. 
 
Полное описание использования **copyIndex** см. в статье [Создание нескольких экземпляров ресурсов в Azure Resource Manager](resource-group-create-multiple.md).

Пример использования **copyIndex** при определении переменной см. в разделе [Переменные](resource-group-authoring-templates.md#variables).

### <a name="example"></a>Пример

В следующем примере представлен цикл копирования, в котором значение индекса включается в имя. 

```json
"resources": [ 
  { 
    "name": "[concat('examplecopy-', copyIndex())]", 
    "type": "Microsoft.Web/sites", 
    "copy": { 
      "name": "websitescopy", 
      "count": "[parameters('count')]" 
    }, 
    ...
  }
]
```

### <a name="return-value"></a>Возвращаемое значение

Целое число, представляющее текущей индекс итерации.

<a id="div" />

## <a name="div"></a>div
`div(operand1, operand2)`

Возвращает целую часть частного от деления двух указанных целочисленных значений.

### <a name="parameters"></a>Параметры

| Параметр | Обязательно | type | ОПИСАНИЕ |
|:--- |:--- |:--- |:--- |
| операнд1 |Yes |int |Делимое. |
| операнд2 |Yes |int |Делитель. Не может иметь значение 0. |

### <a name="return-value"></a>Возвращаемое значение

Целое число, представляющее деление.

### <a name="example"></a>Пример

В следующем [примере шаблона](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/div.json) один параметр делится на другой.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "first": {
            "type": "int",
            "defaultValue": 8,
            "metadata": {
                "description": "Integer being divided"
            }
        },
        "second": {
            "type": "int",
            "defaultValue": 3,
            "metadata": {
                "description": "Integer used to divide"
            }
        }
    },
    "resources": [
    ],
    "outputs": {
        "divResult": {
            "type": "int",
            "value": "[div(parameters('first'), parameters('second'))]"
        }
    }
}
```

Выходные данные из предыдущего примера со значениями по умолчанию:

| ИМЯ | type | Значение |
| ---- | ---- | ----- |
| divResult | int | 2 |

Развернуть этот пример шаблона с помощью Azure CLI можно так:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/div.json
```

Развернуть этот пример шаблона с помощью PowerShell можно так:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/div.json 
```

<a id="float" />

## <a name="float"></a>float;
`float(arg1)`

Преобразует значение в число с плавающей запятой. Эта функция используется только при передаче пользовательских параметров в приложение, такое как приложение логики.

### <a name="parameters"></a>Параметры

| Параметр | Обязательно | type | ОПИСАНИЕ |
|:--- |:--- |:--- |:--- |
| arg1 |Yes |строка или целое число |Значение, которое необходимо преобразовать в число с плавающей запятой. |

### <a name="return-value"></a>Возвращаемое значение
Число с плавающей запятой.

### <a name="example"></a>Пример

В следующем примере показано, как с помощью функции float передать параметры в приложение логики:

```json
{
    "type": "Microsoft.Logic/workflows",
    "properties": {
        ...
        "parameters": {
            "custom1": {
                "value": "[float('3.0')]"
            },
            "custom2": {
                "value": "[float(3)]"
            },
```

<a id="int" />

## <a name="int"></a>int
`int(valueToConvert)`

Преобразует указанное значение в целое число.

### <a name="parameters"></a>Параметры

| Параметр | Обязательно | type | ОПИСАНИЕ |
|:--- |:--- |:--- |:--- |
| valueToConvert |Yes |строка или целое число |Значение, которое необходимо преобразовать в целое число. |

### <a name="return-value"></a>Возвращаемое значение

Целое число преобразованного значения.

### <a name="example"></a>Пример

В следующем [примере шаблона](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/int.json) указанное пользователем значение параметра преобразуется в целое число.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "stringToConvert": { 
            "type": "string",
            "defaultValue": "4"
        }
    },
    "resources": [
    ],
    "outputs": {
        "intResult": {
            "type": "int",
            "value": "[int(parameters('stringToConvert'))]"
        }
    }
}
```

Выходные данные из предыдущего примера со значениями по умолчанию:

| ИМЯ | type | Значение |
| ---- | ---- | ----- |
| intResult | int | 4. |

Развернуть этот пример шаблона с помощью Azure CLI можно так:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/int.json
```

Развернуть этот пример шаблона с помощью PowerShell можно так:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/int.json
```

<a id="max" />

## <a name="max"></a>max
`max (arg1)`

Возвращает максимальное значение из массива целых чисел или разделенный запятыми список целых чисел.

### <a name="parameters"></a>Параметры

| Параметр | Обязательно | type | ОПИСАНИЕ |
|:--- |:--- |:--- |:--- |
| arg1 |Yes |массив целых чисел или разделенный запятыми список целых чисел |Коллекция, для которой необходимо получить максимальное значение. |

### <a name="return-value"></a>Возвращаемое значение

Целое число, представляющее максимальное значение из коллекции.

### <a name="example"></a>Пример

В следующем [примере шаблона](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/max.json) показано, как использовать функцию max с массивом и списком целых чисел.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "arrayToTest": {
            "type": "array",
            "defaultValue": [0,3,2,5,4]
        }
    },
    "resources": [],
    "outputs": {
        "arrayOutput": {
            "type": "int",
            "value": "[max(parameters('arrayToTest'))]"
        },
        "intOutput": {
            "type": "int",
            "value": "[max(0,3,2,5,4)]"
        }
    }
}
```

Выходные данные из предыдущего примера со значениями по умолчанию:

| ИМЯ | type | Значение |
| ---- | ---- | ----- |
| arrayOutput | int | 5 |
| intOutput | int | 5 |

Развернуть этот пример шаблона с помощью Azure CLI можно так:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/max.json
```

Развернуть этот пример шаблона с помощью PowerShell можно так:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/max.json
```

<a id="min" />

## <a name="min"></a>Min
`min (arg1)`

Возвращает минимальное значение из массива целых чисел или разделенный запятыми список целых чисел.

### <a name="parameters"></a>Параметры

| Параметр | Обязательно | type | ОПИСАНИЕ |
|:--- |:--- |:--- |:--- |
| arg1 |Yes |массив целых чисел или разделенный запятыми список целых чисел |Коллекция, для которой необходимо получить минимальное значение. |

### <a name="return-value"></a>Возвращаемое значение

Целое число, представляющее минимальное значение из коллекции.

### <a name="example"></a>Пример

В следующем [примере шаблона](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/min.json) показано, как использовать функцию min с массивом и списком целых чисел.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "arrayToTest": {
            "type": "array",
            "defaultValue": [0,3,2,5,4]
        }
    },
    "resources": [],
    "outputs": {
        "arrayOutput": {
            "type": "int",
            "value": "[min(parameters('arrayToTest'))]"
        },
        "intOutput": {
            "type": "int",
            "value": "[min(0,3,2,5,4)]"
        }
    }
}
```

Выходные данные из предыдущего примера со значениями по умолчанию:

| ИМЯ | type | Значение |
| ---- | ---- | ----- |
| arrayOutput | int | 0 |
| intOutput | int | 0 |

Развернуть этот пример шаблона с помощью Azure CLI можно так:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/min.json
```

Развернуть этот пример шаблона с помощью PowerShell можно так:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/min.json
```

<a id="mod" />

## <a name="mod"></a>mod (модуль)
`mod(operand1, operand2)`

Возвращает остаток от деления двух указанных целочисленных значений.

### <a name="parameters"></a>Параметры

| Параметр | Обязательно | type | ОПИСАНИЕ |
|:--- |:--- |:--- |:--- |
| операнд1 |Yes |int |Делимое. |
| операнд2 |Yes |int |Делитель, не может быть равен 0. |

### <a name="return-value"></a>Возвращаемое значение
Целое число, представляющее остаток.

### <a name="example"></a>Пример

В следующем [примере шаблона](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/mod.json) возвращается остаток от деления одного параметра на другой.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "first": {
            "type": "int",
            "defaultValue": 7,
            "metadata": {
                "description": "Integer being divided"
            }
        },
        "second": {
            "type": "int",
            "defaultValue": 3,
            "metadata": {
                "description": "Integer used to divide"
            }
        }
    },
    "resources": [
    ],
    "outputs": {
        "modResult": {
            "type": "int",
            "value": "[mod(parameters('first'), parameters('second'))]"
        }
    }
}
```

Выходные данные из предыдущего примера со значениями по умолчанию:

| ИМЯ | type | Значение |
| ---- | ---- | ----- |
| modResult | int | 1 |

Развернуть этот пример шаблона с помощью Azure CLI можно так:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/mod.json
```

Развернуть этот пример шаблона с помощью PowerShell можно так:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/mod.json
```

<a id="mul" />

## <a name="mul"></a>mul
`mul(operand1, operand2)`

Возвращает произведение двух указанных целочисленных значений.

### <a name="parameters"></a>Параметры

| Параметр | Обязательно | type | ОПИСАНИЕ |
|:--- |:--- |:--- |:--- |
| операнд1 |Yes |int |Первый множитель. |
| операнд2 |Yes |int |Второй множитель. |

### <a name="return-value"></a>Возвращаемое значение

Целое число, представляющее произведение.

### <a name="example"></a>Пример

В следующем [примере шаблона](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/mul.json) один параметр умножается на другой.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "first": {
            "type": "int",
            "defaultValue": 5,
            "metadata": {
                "description": "First integer to multiply"
            }
        },
        "second": {
            "type": "int",
            "defaultValue": 3,
            "metadata": {
                "description": "Second integer to multiply"
            }
        }
    },
    "resources": [
    ],
    "outputs": {
        "mulResult": {
            "type": "int",
            "value": "[mul(parameters('first'), parameters('second'))]"
        }
    }
}
```

Выходные данные из предыдущего примера со значениями по умолчанию:

| ИМЯ | type | Значение |
| ---- | ---- | ----- |
| mulResult | int | 15 |

Развернуть этот пример шаблона с помощью Azure CLI можно так:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/mul.json
```

Развернуть этот пример шаблона с помощью PowerShell можно так:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/mul.json
```

<a id="sub" />

## <a name="sub"></a>sub
`sub(operand1, operand2)`

Возвращает разность двух указанных целочисленных значений.

### <a name="parameters"></a>Параметры

| Параметр | Обязательно | type | ОПИСАНИЕ |
|:--- |:--- |:--- |:--- |
| операнд1 |Yes |int |Уменьшаемое. |
| операнд2 |Yes |int |Вычитаемое. |

### <a name="return-value"></a>Возвращаемое значение
Целое число, представляющее разность.

### <a name="example"></a>Пример

В следующем [примере шаблона](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/sub.json) один параметр вычитается из другого.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "first": {
            "type": "int",
            "defaultValue": 7,
            "metadata": {
                "description": "Integer subtracted from"
            }
        },
        "second": {
            "type": "int",
            "defaultValue": 3,
            "metadata": {
                "description": "Integer to subtract"
            }
        }
    },
    "resources": [
    ],
    "outputs": {
        "subResult": {
            "type": "int",
            "value": "[sub(parameters('first'), parameters('second'))]"
        }
    }
}
```

Выходные данные из предыдущего примера со значениями по умолчанию:

| ИМЯ | type | Значение |
| ---- | ---- | ----- |
| subResult | int | 4. |

Развернуть этот пример шаблона с помощью Azure CLI можно так:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/sub.json
```

Развернуть этот пример шаблона с помощью PowerShell можно так:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/sub.json
```

## <a name="next-steps"></a>Дополнительная информация
* Описание разделов в шаблоне Azure Resource Manager см. в статье [Создание шаблонов Azure Resource Manager](resource-group-authoring-templates.md).
* Инструкции по объединению нескольких шаблонов см. в статье [Использование связанных шаблонов в Azure Resource Manager](resource-group-linked-templates.md).
* Указания по выполнению заданного количества циклов итерации при создании типа ресурса см. в статье [Создание нескольких экземпляров ресурсов в Azure Resource Manager](resource-group-create-multiple.md).
* Указания по развертыванию созданного шаблона см. в статье, посвященной [развертыванию приложения с помощью шаблона Azure Resource Manager](resource-group-template-deploy.md).


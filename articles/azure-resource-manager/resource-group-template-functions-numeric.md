---
title: "Числовые функции шаблона Azure Resource Manager | Документация Майкрософт"
description: "Описывает функции, используемые в шаблоне Azure Resource Manager для работы с числами."
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
ms.date: 04/26/2017
ms.author: tomfitz
ms.translationtype: Human Translation
ms.sourcegitcommit: 54b5b8d0040dc30651a98b3f0d02f5374bf2f873
ms.openlocfilehash: 5844540801a6f0ff593b3f404f6815473c65a52e
ms.contentlocale: ru-ru
ms.lasthandoff: 04/28/2017


---
# <a name="numeric-functions-for-azure-resource-manager-templates"></a>Числовые функции для шаблонов Azure Resource Manager

Диспетчер ресурсов предоставляет следующие функции для работы с целыми числами:

* [добавление](#add)
* [copyIndex](#copyindex)
* [div](#div)
* [float](#float)
* [int](#int)
* [min](#min)
* [max](#max)
* [mod (модуль)](#mod)
* [mul](#mul)
* [sub](#sub)

<a id="add" />

## <a name="add"></a>добавление
`add(operand1, operand2)`

Возвращает сумму двух указанных целочисленных значений.

### <a name="parameters"></a>Параметры

| Параметр | Обязательно | Тип | Описание |
|:--- |:--- |:--- |:--- | 
|операнд1 |Да |int |Первое слагаемое. |
|операнд2 |Да |int |Второе слагаемое. |

### <a name="examples"></a>Примеры

В следующем примере суммируются два параметра.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "first": {
            "type": "int",
            "metadata": {
                "description": "First integer to add"
            }
        },
        "second": {
            "type": "int",
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

### <a name="return-value"></a>Возвращаемое значение

Целое число, содержащее сумму параметров.

<a id="copyindex" />

## <a name="copyindex"></a>copyIndex
`copyIndex(offset)`

Возвращает индекс цикла итерации. 

### <a name="parameters"></a>Параметры

| Параметр | Обязательно | Тип | Описание |
|:--- |:--- |:--- |:--- |
| offset |Нет |int |Число, добавляемое к отсчитываемому от нуля значению итерации. |

### <a name="remarks"></a>Примечания

Эта функция всегда используется с объектом **copy**. Если значение **offset** не указано, возвращается текущее значение итерации. Значение итерации начинается с нуля. Полное описание использования **copyIndex** см. в статье [Создание нескольких экземпляров ресурсов в Azure Resource Manager](resource-group-create-multiple.md).

### <a name="examples"></a>Примеры

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

| Параметр | Обязательно | Тип | Описание |
|:--- |:--- |:--- |:--- |
| операнд1 |Да |int |Делимое. |
| операнд2 |Да |int |Делитель. Не может иметь значение 0. |

### <a name="examples"></a>Примеры

В следующем примере один параметр делится на другой.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "first": {
            "type": "int",
            "metadata": {
                "description": "Integer being divided"
            }
        },
        "second": {
            "type": "int",
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

### <a name="return-value"></a>Возвращаемое значение

Целое число, представляющее деление.

<a id="float" />

## <a name="float"></a>float;
`float(arg1)`

Преобразует значение в число с плавающей запятой. Эта функция используется только при передаче пользовательских параметров в приложение, такое как приложение логики.

### <a name="parameters"></a>Параметры

| Параметр | Обязательно | Тип | Описание |
|:--- |:--- |:--- |:--- |
| arg1 |Да |строка или целое число |Значение, которое необходимо преобразовать в число с плавающей запятой. |

### <a name="examples"></a>Примеры

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

### <a name="return-value"></a>Возвращаемое значение
Число с плавающей запятой.

<a id="int" />

## <a name="int"></a>int
`int(valueToConvert)`

Преобразует указанное значение в целое число.

### <a name="parameters"></a>Параметры

| Параметр | Обязательно | Тип | Описание |
|:--- |:--- |:--- |:--- |
| valueToConvert |Да |строка или целое число |Значение, которое необходимо преобразовать в целое число. |

### <a name="examples"></a>Примеры

В следующем примере указанное пользователем значение параметра преобразуется в целое число.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "appId": { "type": "string" }
    },
    "variables": { 
        "intValue": "[int(parameters('appId'))]"
    },
    "resources": [
    ],
    "outputs": {
        "divResult": {
            "type": "int",
            "value": "[variables('intValue')]"
        }
    }
}
```

### <a name="return-value"></a>Возвращаемое значение

Целое число.

<a id="min" />

## <a name="min"></a>Min
`min (arg1)`

Возвращает минимальное значение из массива целых чисел или разделенный запятыми список целых чисел.

### <a name="parameters"></a>Параметры

| Параметр | Обязательно | Тип | Описание |
|:--- |:--- |:--- |:--- |
| arg1 |Да |массив целых чисел или разделенный запятыми список целых чисел |Коллекция, для которой необходимо получить минимальное значение. |

### <a name="examples"></a>Примеры

В следующем примере показано, как использовать функцию min с массивом и списком целых чисел:

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

### <a name="return-value"></a>Возвращаемое значение

Целое число, представляющее минимальное значение из коллекции.

<a id="max" />

## <a name="max"></a>max
`max (arg1)`

Возвращает максимальное значение из массива целых чисел или разделенный запятыми список целых чисел.

### <a name="parameters"></a>Параметры

| Параметр | Обязательно | Тип | Описание |
|:--- |:--- |:--- |:--- |
| arg1 |Да |массив целых чисел или разделенный запятыми список целых чисел |Коллекция, для которой необходимо получить максимальное значение. |

### <a name="examples"></a>Примеры

В следующем примере показано, как использовать функцию max с массивом и списком целых чисел:

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

### <a name="return-value"></a>Возвращаемое значение

Целое число, представляющее максимальное значение из коллекции.

<a id="mod" />

## <a name="mod"></a>mod (модуль)
`mod(operand1, operand2)`

Возвращает остаток от деления двух указанных целочисленных значений.

### <a name="parameters"></a>Параметры

| Параметр | Обязательно | Тип | Описание |
|:--- |:--- |:--- |:--- |
| операнд1 |Да |int |Делимое. |
| операнд2 |Да |int |Делитель, не может быть равен 0. |

### <a name="examples"></a>Примеры

В следующем примере возвращается остаток от деления одного параметра на другой.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "first": {
            "type": "int",
            "metadata": {
                "description": "Integer being divided"
            }
        },
        "second": {
            "type": "int",
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

### <a name="return-value"></a>Возвращаемое значение
Целое число, представляющее остаток.

<a id="mul" />

## <a name="mul"></a>mul
`mul(operand1, operand2)`

Возвращает произведение двух указанных целочисленных значений.

### <a name="parameters"></a>Параметры

| Параметр | Обязательно | Тип | Описание |
|:--- |:--- |:--- |:--- |
| операнд1 |Да |int |Первый множитель. |
| операнд2 |Да |int |Второй множитель. |

### <a name="examples"></a>Примеры

В следующем примере один параметр умножается на другой.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "first": {
            "type": "int",
            "metadata": {
                "description": "First integer to multiply"
            }
        },
        "second": {
            "type": "int",
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

### <a name="return-value"></a>Возвращаемое значение

Целое число, представляющее произведение.

<a id="sub" />

## <a name="sub"></a>sub
`sub(operand1, operand2)`

Возвращает разность двух указанных целочисленных значений.

### <a name="parameters"></a>Параметры

| Параметр | Обязательно | Тип | Описание |
|:--- |:--- |:--- |:--- |
| операнд1 |Да |int |Уменьшаемое. |
| операнд2 |Да |int |Вычитаемое. |

### <a name="examples"></a>Примеры

В следующем примере один параметр вычитается из другого.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "first": {
            "type": "int",
            "metadata": {
                "description": "Integer subtracted from"
            }
        },
        "second": {
            "type": "int",
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

### <a name="return-value"></a>Возвращаемое значение
Целое число, представляющее разность.

## <a name="next-steps"></a>Дальнейшие действия
* Описание разделов в шаблоне Azure Resource Manager см. в статье [Создание шаблонов Azure Resource Manager](resource-group-authoring-templates.md).
* Инструкции по объединению нескольких шаблонов см. в статье [Использование связанных шаблонов в Azure Resource Manager](resource-group-linked-templates.md).
* Указания по выполнению заданного количества циклов итерации при создании типа ресурса см. в статье [Создание нескольких экземпляров ресурсов в Azure Resource Manager](resource-group-create-multiple.md).
* Указания по развертыванию созданного шаблона см. в статье, посвященной [развертыванию приложения с помощью шаблона Azure Resource Manager](resource-group-template-deploy.md).



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
ms.date: 06/13/2017
ms.author: tomfitz
ms.translationtype: Human Translation
ms.sourcegitcommit: db18dd24a1d10a836d07c3ab1925a8e59371051f
ms.openlocfilehash: f14bc4e8091eb1f0dccb761d9df1c931b9b77732
ms.contentlocale: ru-ru
ms.lasthandoff: 06/15/2017


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

### <a name="return-value"></a>Возвращаемое значение

Целое число, содержащее сумму параметров.

### <a name="example"></a>Пример

В следующем примере суммируются два параметра.

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

| Имя | Тип | Значение |
| ---- | ---- | ----- |
| addResult | int | 8 |

<a id="copyindex" />

## <a name="copyindex"></a>copyIndex
`copyIndex(loopName, offset)`

Возвращает индекс цикла итерации. 

### <a name="parameters"></a>Параметры

| Параметр | Обязательно | Тип | Описание |
|:--- |:--- |:--- |:--- |
| loopName | Нет | string | Имя цикла для получения итерации. |
| offset |Нет |int |Число, добавляемое к отсчитываемому от нуля значению итерации. |

### <a name="remarks"></a>Примечания

Эта функция всегда используется с объектом **copy**. Если значение **offset** не указано, возвращается текущее значение итерации. Значение итерации начинается с нуля.

Свойство **loopName** позволяет указать, на какую итерацию ссылается copyIndex: ресурса или свойства. Если значение **loopName** не указано, используется текущая итерация типа ресурса. Укажите значение **loopName** при выполнении итерации по свойству. 
 
Полное описание использования **copyIndex** см. в статье [Создание нескольких экземпляров ресурсов в Azure Resource Manager](resource-group-create-multiple.md).

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

| Параметр | Обязательно | Тип | Описание |
|:--- |:--- |:--- |:--- |
| операнд1 |Да |int |Делимое. |
| операнд2 |Да |int |Делитель. Не может иметь значение 0. |

### <a name="return-value"></a>Возвращаемое значение

Целое число, представляющее деление.

### <a name="example"></a>Пример

В следующем примере один параметр делится на другой.

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

| Имя | Тип | Значение |
| ---- | ---- | ----- |
| divResult | int | 2 |

<a id="float" />

## <a name="float"></a>float;
`float(arg1)`

Преобразует значение в число с плавающей запятой. Эта функция используется только при передаче пользовательских параметров в приложение, такое как приложение логики.

### <a name="parameters"></a>Параметры

| Параметр | Обязательно | Тип | Описание |
|:--- |:--- |:--- |:--- |
| arg1 |Да |строка или целое число |Значение, которое необходимо преобразовать в число с плавающей запятой. |

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

| Параметр | Обязательно | Тип | Описание |
|:--- |:--- |:--- |:--- |
| valueToConvert |Да |строка или целое число |Значение, которое необходимо преобразовать в целое число. |

### <a name="return-value"></a>Возвращаемое значение

Целое число преобразованного значения.

### <a name="example"></a>Пример

В следующем примере указанное пользователем значение параметра преобразуется в целое число.

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

| Имя | Тип | Значение |
| ---- | ---- | ----- |
| intResult | int | 4. |


<a id="min" />

## <a name="min"></a>Min
`min (arg1)`

Возвращает минимальное значение из массива целых чисел или разделенный запятыми список целых чисел.

### <a name="parameters"></a>Параметры

| Параметр | Обязательно | Тип | Описание |
|:--- |:--- |:--- |:--- |
| arg1 |Да |массив целых чисел или разделенный запятыми список целых чисел |Коллекция, для которой необходимо получить минимальное значение. |

### <a name="return-value"></a>Возвращаемое значение

Целое число, представляющее минимальное значение из коллекции.

### <a name="example"></a>Пример

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

Выходные данные из предыдущего примера со значениями по умолчанию:

| Имя | Тип | Значение |
| ---- | ---- | ----- |
| arrayOutput | int | 0 |
| intOutput | int | 0 |

<a id="max" />

## <a name="max"></a>max
`max (arg1)`

Возвращает максимальное значение из массива целых чисел или разделенный запятыми список целых чисел.

### <a name="parameters"></a>Параметры

| Параметр | Обязательно | Тип | Описание |
|:--- |:--- |:--- |:--- |
| arg1 |Да |массив целых чисел или разделенный запятыми список целых чисел |Коллекция, для которой необходимо получить максимальное значение. |

### <a name="return-value"></a>Возвращаемое значение

Целое число, представляющее максимальное значение из коллекции.

### <a name="example"></a>Пример

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

Выходные данные из предыдущего примера со значениями по умолчанию:

| Имя | Тип | Значение |
| ---- | ---- | ----- |
| arrayOutput | int | 5 |
| intOutput | int | 5 |

<a id="mod" />

## <a name="mod"></a>mod (модуль)
`mod(operand1, operand2)`

Возвращает остаток от деления двух указанных целочисленных значений.

### <a name="parameters"></a>Параметры

| Параметр | Обязательно | Тип | Описание |
|:--- |:--- |:--- |:--- |
| операнд1 |Да |int |Делимое. |
| операнд2 |Да |int |Делитель, не может быть равен 0. |

### <a name="return-value"></a>Возвращаемое значение
Целое число, представляющее остаток.

### <a name="example"></a>Пример

В следующем примере возвращается остаток от деления одного параметра на другой.

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

| Имя | Тип | Значение |
| ---- | ---- | ----- |
| modResult | int | 1 |

<a id="mul" />

## <a name="mul"></a>mul
`mul(operand1, operand2)`

Возвращает произведение двух указанных целочисленных значений.

### <a name="parameters"></a>Параметры

| Параметр | Обязательно | Тип | Описание |
|:--- |:--- |:--- |:--- |
| операнд1 |Да |int |Первый множитель. |
| операнд2 |Да |int |Второй множитель. |

### <a name="return-value"></a>Возвращаемое значение

Целое число, представляющее произведение.

### <a name="example"></a>Пример

В следующем примере один параметр умножается на другой.

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

| Имя | Тип | Значение |
| ---- | ---- | ----- |
| mulResult | int | 15 |

<a id="sub" />

## <a name="sub"></a>sub
`sub(operand1, operand2)`

Возвращает разность двух указанных целочисленных значений.

### <a name="parameters"></a>Параметры

| Параметр | Обязательно | Тип | Описание |
|:--- |:--- |:--- |:--- |
| операнд1 |Да |int |Уменьшаемое. |
| операнд2 |Да |int |Вычитаемое. |

### <a name="return-value"></a>Возвращаемое значение
Целое число, представляющее разность.

### <a name="example"></a>Пример

В следующем примере один параметр вычитается из другого.

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

| Имя | Тип | Значение |
| ---- | ---- | ----- |
| subResult | int | 4. |

## <a name="next-steps"></a>Дальнейшие действия
* Описание разделов в шаблоне Azure Resource Manager см. в статье [Создание шаблонов Azure Resource Manager](resource-group-authoring-templates.md).
* Инструкции по объединению нескольких шаблонов см. в статье [Использование связанных шаблонов в Azure Resource Manager](resource-group-linked-templates.md).
* Указания по выполнению заданного количества циклов итерации при создании типа ресурса см. в статье [Создание нескольких экземпляров ресурсов в Azure Resource Manager](resource-group-create-multiple.md).
* Указания по развертыванию созданного шаблона см. в статье, посвященной [развертыванию приложения с помощью шаблона Azure Resource Manager](resource-group-template-deploy.md).



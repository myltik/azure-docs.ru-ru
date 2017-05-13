---
title: "Массивы и объекты в функциях шаблона Azure Resource Manager | Документация Майкрософт"
description: "Описывает функции, используемые в шаблоне Azure Resource Manager для работы с массивами и объектами."
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
ms.openlocfilehash: 34fc513b6d4408e341fc5a723ca743daee39b85d
ms.contentlocale: ru-ru
ms.lasthandoff: 04/28/2017


---
# <a name="array-and-object-functions-for-azure-resource-manager-templates"></a>Функции массивов и объектов для шаблонов Azure Resource Manager 

Resource Manager предоставляет ряд функций для работы с массивами и объектами.

* [array](#array).
* [coalesce](#coalesce)
* [concat](#concat)
* [contains](#contains)
* [createArray](#createarray)
* [empty](#empty)
* [first](#first)
* [intersection](#intersection)
* [last](#last)
* [длина](#length)
* [min](#min)
* [max](#max)
* [range](#range)
* [skip](#skip)
* [take](#take)
* [union](#union)

Чтобы получить массив строковых значений, разделенных определенным значением, используйте [split](resource-group-template-functions-string.md#split).

<a id="array" />

## <a name="array"></a>array
`array(convertToArray)`

Преобразует значение в массив.

### <a name="parameters"></a>Параметры

| Параметр | Обязательно | Тип | Описание |
|:--- |:--- |:--- |:--- |
| convertToArray |Да |целое число, строка, массив или объект |Значение, которое необходимо преобразовать в массив. |

### <a name="examples"></a>Примеры

В следующем примере показано, как использовать функцию array с различными типами:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "intToConvert": {
            "type": "int",
            "defaultValue": 1
        },
        "stringToConvert": {
            "type": "string",
            "defaultValue": "a"
        },
        "objectToConvert": {
            "type": "object",
            "defaultValue": {"a": "b", "c": "d"}
        }
    },
    "resources": [
    ],
    "outputs": {
        "intOutput": {
            "type": "array",
            "value": "[array(parameters('intToConvert'))]"
        },
        "stringOutput": {
            "type": "array",
            "value": "[array(parameters('stringToConvert'))]"
        },
        "objectOutput": {
            "type": "array",
            "value": "[array(parameters('objectToConvert'))]"
        }
    }
}
```

### <a name="return-value"></a>Возвращаемое значение

Массив.

<a id="coalesce" />

## <a name="coalesce"></a>coalesce
`coalesce(arg1, arg2, arg3, ...)`

Возвращает из параметров первое значение, отличное от null. Пустые строки, пустые массивы и пустые объекты не имеют значение null.

### <a name="parameters"></a>Параметры

| Параметр | Обязательно | Тип | Описание |
|:--- |:--- |:--- |:--- |
| arg1 |Да |целое число, строка, массив или объект |Первое значение, которое проверяется на соответствие значению null. |
| дополнительные аргументы |Нет |целое число, строка, массив или объект |Дополнительные значения, которые проверяются на соответствие значению null. |

### <a name="examples"></a>Примеры

В следующем примере показаны выходные данные для разных случаев использования coalesce.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "objectToTest": {
            "type": "object",
            "defaultValue": {"first": null, "second": null}
        }
    },
    "resources": [
    ],
    "outputs": {
        "stringOutput": {
            "type": "string",
            "value": "[coalesce(parameters('objectToTest').first, parameters('objectToTest').second, 'fallback')]"
        },
        "intOutput": {
            "type": "int",
            "value": "[coalesce(parameters('objectToTest').first, parameters('objectToTest').second, 1)]"
        },
        "objectOutput": {
            "type": "object",
            "value": "[coalesce(parameters('objectToTest').first, parameters('objectToTest').second, parameters('objectToTest'))]"
        },
        "arrayOutput": {
            "type": "array",
            "value": "[coalesce(parameters('objectToTest').first, parameters('objectToTest').second, array(1))]"
        }
    }
}
```

### <a name="return-value"></a>Возвращаемое значение

Значение первых параметров, отличных от null, которое может быть строкой, целым числом, массивом или объектом. Null, если все параметры имеют значение null. 

<a id="concat" />

## <a name="concat"></a>concat
`concat(arg1, arg2, arg3, ...)`

Объединяет несколько массивов и возвращает объединенный массив или объединяет несколько строковых значений и возвращает объединенную строку. 

### <a name="parameters"></a>Параметры

| Параметр | Обязательно | Тип | Описание |
|:--- |:--- |:--- |:--- |
| arg1 |Да |массив или строка |Первый массив или строка для объединения. |
| дополнительные аргументы |Нет |массив или строка |Дополнительные массивы или строки в последовательном порядке для объединения. |

Эта функция может принимать любое количество аргументов, а также строки или массивы параметров.

### <a name="examples"></a>Примеры

В следующем примере показано, как объединить два массива.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": { 
        "firstArray": { 
            "type": "array", 
            "defaultValue": [ 
                "1-1", 
                "1-2", 
                "1-3" 
            ] 
        },
        "secondArray": {
            "type": "array", 
            "defaultValue": [ 
                "2-1", 
                "2-2",
                "2-3" 
            ] 
        }
    },
    "resources": [
    ],
    "outputs": {
        "return": {
            "type": "array",
            "value": "[concat(parameters('firstArray'), parameters('secondArray'))]"
        }
    }
}
```

В следующем примере показано, как объединить два строковых значения и получить объединенную строку.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "prefix": {
            "type": "string",
            "defaultValue": "prefix"
        }
    },
    "resources": [],
    "outputs": {
        "concatOutput": {
            "value": "[concat(parameters('prefix'), uniqueString(resourceGroup().id))]",
            "type" : "string"
        }
    }
}
```

### <a name="return-value"></a>Возвращаемое значение
Строка или массив объединенных значений.

<a id="contains" />

## <a name="contains"></a>contains
`contains(container, itemToFind)`

Проверяет, содержит ли массив значение, содержит ли объект ключ или содержит ли строка подстроку.

### <a name="parameters"></a>Параметры

| Параметр | Обязательно | Тип | Описание |
|:--- |:--- |:--- |:--- |
| container |Да |массив, объект или строка |Значение, содержащее значение, которое необходимо найти. |
| itemToFind |Да |строка или целое число |Значение, которое необходимо найти. |

### <a name="examples"></a>Примеры

В следующем примере показано, как использовать функцию contains с различными типами:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "stringToTest": {
            "type": "string",
            "defaultValue": "OneTwoThree"
        },
        "objectToTest": {
            "type": "object",
            "defaultValue": {"one": "a", "two": "b", "three": "c"}
        },
        "arrayToTest": {
            "type": "array",
            "defaultValue": ["one", "two", "three"]
        }
    },
    "resources": [
    ],
    "outputs": {
        "stringTrue": {
            "type": "bool",
            "value": "[contains(parameters('stringToTest'), 'e')]"
        },
        "stringFalse": {
            "type": "bool",
            "value": "[contains(parameters('stringToTest'), 'z')]"
        },
        "objectTrue": {
            "type": "bool",
            "value": "[contains(parameters('objectToTest'), 'one')]"
        },
        "objectFalse": {
            "type": "bool",
            "value": "[contains(parameters('objectToTest'), 'a')]"
        },
        "arrayTrue": {
            "type": "bool",
            "value": "[contains(parameters('arrayToTest'), 'three')]"
        },
        "arrayFalse": {
            "type": "bool",
            "value": "[contains(parameters('arrayToTest'), 'four')]"
        }
    }
}
```

### <a name="return-value"></a>Возвращаемое значение

**True**, если элемент найден. В противном случае — **False**.

<a id="createarray" />

## <a name="createarray"></a>createarray
`createArray (arg1, arg2, arg3, ...)`

Создает массив из параметров.

### <a name="parameters"></a>Параметры

| Параметр | Обязательно | Тип | Описание |
|:--- |:--- |:--- |:--- |
| arg1 |Да |Строка, целое число, массив или объект |Первое значение в массиве. |
| дополнительные аргументы |Нет |Строка, целое число, массив или объект |Дополнительные значения в массиве. |

### <a name="examples"></a>Примеры

В следующем примере показано, как использовать функцию createArray с различными типами:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "objectToTest": {
            "type": "object",
            "defaultValue": {"one": "a", "two": "b", "three": "c"}
        },
        "arrayToTest": {
            "type": "array",
            "defaultValue": ["one", "two", "three"]
        }
    },
    "resources": [
    ],
    "outputs": {
        "stringArray": {
            "type": "array",
            "value": "[createArray('a', 'b', 'c')]"
        },
        "intArray": {
            "type": "array",
            "value": "[createArray(1, 2, 3)]"
        },
        "objectArray": {
            "type": "array",
            "value": "[createArray(parameters('objectToTest'))]"
        },
        "arrayArray": {
            "type": "array",
            "value": "[createArray(parameters('arrayToTest'))]"
        }
    }
}
```

### <a name="return-value"></a>Возвращаемое значение

Массив.

<a id="empty" />

## <a name="empty"></a>empty

`empty(itemToTest)`

Определяет, являются ли пустыми массив, объект или строка.

### <a name="parameters"></a>Параметры

| Параметр | Обязательно | Тип | Описание |
|:--- |:--- |:--- |:--- |
| itemToTest |Да |массив, объект или строка |Значение, которое необходимо проверить на наличие содержимого. |

### <a name="examples"></a>Примеры

В следующем примере проверяется, являются ли пустыми массив, объект и строка.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "testArray": {
            "type": "array",
            "defaultValue": []
        },
        "testObject": {
            "type": "object",
            "defaultValue": {}
        },
        "testString": {
            "type": "string",
            "defaultValue": ""
        }
    },
    "resources": [
    ],
    "outputs": {
        "arrayEmpty": {
            "type": "bool",
            "value": "[empty(parameters('testArray'))]"
        },
        "objectEmpty": {
            "type": "bool",
            "value": "[empty(parameters('testObject'))]"
        },
        "stringEmpty": {
            "type": "bool",
            "value": "[empty(parameters('testString'))]"
        }
    }
}
```

### <a name="return-value"></a>Возвращаемое значение

Возвращает результат **True**, если значение пустое. В противном случае — **False**.

<a id="first" />

## <a name="first"></a>first
`first(arg1)`

Возвращает первый элемент массива или первый знак строки.

### <a name="parameters"></a>Параметры

| Параметр | Обязательно | Тип | Описание |
|:--- |:--- |:--- |:--- |
| arg1 |Да |массив или строка |Значение, из которого необходимо извлечь первый элемент или знак. |

### <a name="examples"></a>Примеры

В следующем примере показано, как использовать функцию first с массивом и строкой:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "arrayToTest": {
            "type": "array",
            "defaultValue": ["one", "two", "three"]
        }
    },
    "resources": [
    ],
    "outputs": {
        "arrayOutput": {
            "type": "string",
            "value": "[first(parameters('arrayToTest'))]"
        },
        "stringOutput": {
            "type": "string",
            "value": "[first('One Two Three')]"
        }
    }
}
```

### <a name="return-value"></a>Возвращаемое значение

Тип (строка, целое число, массив или объект) первого элемента в массиве или строка первого знака.

<a id="intersection" />

## <a name="intersection"></a>пересечению
`intersection(arg1, arg2, arg3, ...)`

Возвращает из параметров один массив или объект с общими элементами.

### <a name="parameters"></a>Параметры

| Параметр | Обязательно | Тип | Описание |
|:--- |:--- |:--- |:--- |
| arg1 |Да |массив или объект |Первое значение для поиска общих элементов. |
| arg2 |Да |массив или объект |Второе значение для поиска общих элементов. |
| дополнительные аргументы |Нет |массив или объект |Дополнительные значения для поиска общих элементов. |

### <a name="examples"></a>Примеры

В следующем примере показано, как использовать функцию intersection с массивами и объектами:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "firstObject": {
            "type": "object",
            "defaultValue": {"one": "a", "two": "b", "three": "c"}
        },
        "secondObject": {
            "type": "object",
            "defaultValue": {"one": "a", "two": "z", "three": "c"}
        },
        "firstArray": {
            "type": "array",
            "defaultValue": ["one", "two", "three"]
        },
        "secondArray": {
            "type": "array",
            "defaultValue": ["two", "three"]
        }
    },
    "resources": [
    ],
    "outputs": {
        "objectOutput": {
            "type": "object",
            "value": "[intersection(parameters('firstObject'), parameters('secondObject'))]"
        },
        "arrayOutput": {
            "type": "array",
            "value": "[intersection(parameters('firstArray'), parameters('secondArray'))]"
        }
    }
}
```

### <a name="return-value"></a>Возвращаемое значение

Массив или объект с общими элементами.

<a id="last" />

## <a name="last"></a>last
`last (arg1)`

Возвращает последний элемент массива или последний знак строки.

### <a name="parameters"></a>Параметры

| Параметр | Обязательно | Тип | Описание |
|:--- |:--- |:--- |:--- |
| arg1 |Да |массив или строка |Значение, из которого необходимо извлечь последний элемент или знак. |

### <a name="examples"></a>Примеры

В следующем примере показано, как использовать функцию last с массивом и строкой:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "arrayToTest": {
            "type": "array",
            "defaultValue": ["one", "two", "three"]
        }
    },
    "resources": [
    ],
    "outputs": {
        "arrayOutput": {
            "type": "string",
            "value": "[last(parameters('arrayToTest'))]"
        },
        "stringOutput": {
            "type": "string",
            "value": "[last('One Two Three')]"
        }
    }
}
```

### <a name="return-value"></a>Возвращаемое значение

Тип (строка, целое число, массив или объект) последнего элемента в массиве или строка последнего знака.

<a id="length" />

## <a name="length"></a>длина
`length(arg1)`

Возвращает число элементов в массиве или знаков в строке.

### <a name="parameters"></a>Параметры

| Параметр | Обязательно | Тип | Описание |
|:--- |:--- |:--- |:--- |
| arg1 |Да |массив или строка |Массив, который необходимо использовать для получения числа элементов, или строка — для получения числа знаков. |

### <a name="examples"></a>Примеры

В следующем примере показано, как использовать функцию length с массивом и строкой:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "arrayToTest": {
            "type": "array",
            "defaultValue": [
                "one",
                "two",
                "three"
            ]
        },
        "stringToTest": {
            "type": "string",
            "defaultValue": "One Two Three"
        }
    },
    "resources": [],
    "outputs": {
        "arrayLength": {
            "type": "int",
            "value": "[length(parameters('arrayToTest'))]"
        },
        "stringLength": {
            "type": "int",
            "value": "[length(parameters('stringToTest'))]"
        }
    }
}
```

Эту функцию можно использовать с массивом для указания числа итераций при создании ресурсов. В следующем примере параметр **siteNames** ссылается на массив имен для использования при создании веб-сайтов.

```json
"copy": {
    "name": "websitescopy",
    "count": "[length(parameters('siteNames'))]"
}
```

Дополнительные сведения об использовании этой функции с массивом см. в статье [Создание нескольких экземпляров ресурсов в Azure Resource Manager](resource-group-create-multiple.md).

### <a name="return-value"></a>Возвращаемое значение

Целое число. 

<a id="min" />

## <a name="min"></a>Min
`min(arg1)`

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

Целое число, представляющее минимальное значение.

<a id="max" />

## <a name="max"></a>max
`max(arg1)`

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

Целое число, представляющее максимальное значение.

<a id="range" />

## <a name="range"></a>range
`range(startingInteger, numberOfElements)`

Создает массив целых чисел, используя начальное целое число и количество элементов.

### <a name="parameters"></a>Параметры

| Параметр | Обязательно | Тип | Описание |
|:--- |:--- |:--- |:--- |
| startingInteger |Да |int |Первое целое число в массиве. |
| numberofElements |Да |int |Количество целых чисел в массиве. |

### <a name="examples"></a>Примеры

В следующем примере показано, как использовать функцию range:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "startingInt": {
            "type": "int",
            "defaultValue": 5
        },
        "numberOfElements": {
            "type": "int",
            "defaultValue": 3
        }
    },
    "resources": [],
    "outputs": {
        "rangeOutput": {
            "type": "array",
            "value": "[range(parameters('startingInt'),parameters('numberOfElements'))]"
        }
    }
}
```

### <a name="return-value"></a>Возвращаемое значение

Массив целых чисел.

<a id="skip" />

## <a name="skip"></a>skip
`skip(originalValue, numberToSkip)`

Возвращает массив, содержащий все элементы из исходного массива, начиная с заданной позиции в массиве, или строку, содержащую все знаки из исходной строки, начиная с заданной позиции в строке.

### <a name="parameters"></a>Параметры

| Параметр | Обязательно | Тип | Описание |
|:--- |:--- |:--- |:--- |
| originalValue |Да |массив или строка |Массив или строка, используемые для пропуска. |
| numberToSkip |Да |int |Число элементов или знаков, которые необходимо пропустить. Если это значение меньше или равно 0, то возвращаются все элементы или знаки в значении. Если это значение превышает длину массива или строки, то возвращается пустой массив или пустая строка. |

### <a name="examples"></a>Примеры

В следующем примере пропускается заданное число элементов в массиве и заданное число знаков в строке.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "testArray": {
            "type": "array",
            "defaultValue": [
                "one",
                "two",
                "three"
            ]
        },
        "elementsToSkip": {
            "type": "int",
            "defaultValue": 2
        },
        "testString": {
            "type": "string",
            "defaultValue": "one two three"
        },
        "charactersToSkip": {
            "type": "int",
            "defaultValue": 4
        }
    },
    "resources": [],
    "outputs": {
        "arrayOutput": {
            "type": "array",
            "value": "[skip(parameters('testArray'),parameters('elementsToSkip'))]"
        },
        "stringOutput": {
            "type": "string",
            "value": "[skip(parameters('testString'),parameters('charactersToSkip'))]"
        }
    }
}
```

### <a name="return-value"></a>Возвращаемое значение

Массив или строка.

<a id="take" />

## <a name="take"></a>take
`take(originalValue, numberToTake)`

Возвращает массив, содержащий указанное число элементов, считая от начала массива, или строку, содержащую указанное число знаков, считая от начала строки.

### <a name="parameters"></a>Параметры

| Параметр | Обязательно | Тип | Описание |
|:--- |:--- |:--- |:--- |
| originalValue |Да |массив или строка |Массив или строка, из которых берутся элементы. |
| numberToTake |Да |int |Число элементов или знаков, которые необходимо взять. Если это значение меньше или равно 0, то возвращается пустой массив или строка. Если это значение превышает длину заданного массива или строки, то возвращаются все элементы в массиве или строке. |

### <a name="examples"></a>Примеры

В следующем примере из массива извлекается заданное число элементов, а из строки — заданное число знаков.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "testArray": {
            "type": "array",
            "defaultValue": [
                "one",
                "two",
                "three"
            ]
        },
        "elementsToTake": {
            "type": "int",
            "defaultValue": 2
        },
        "testString": {
            "type": "string",
            "defaultValue": "one two three"
        },
        "charactersToTake": {
            "type": "int",
            "defaultValue": 2
        }
    },
    "resources": [],
    "outputs": {
        "arrayOutput": {
            "type": "array",
            "value": "[take(parameters('testArray'),parameters('elementsToTake'))]"
        },
        "stringOutput": {
            "type": "string",
            "value": "[take(parameters('testString'),parameters('charactersToTake'))]"
        }
    }
}
```

### <a name="return-value"></a>Возвращаемое значение

Массив или строка.

<a id="union" />

## <a name="union"></a>union
`union(arg1, arg2, arg3, ...)`

Возвращает из параметров один массив или объект со всеми элементами. Повторяющиеся значения или ключи включаются только один раз.

### <a name="parameters"></a>Параметры

| Параметр | Обязательно | Тип | Описание |
|:--- |:--- |:--- |:--- |
| arg1 |Да |массив или объект |Первое значение для объединения элементов. |
| arg2 |Да |массив или объект |Второе значение для объединения элементов. |
| дополнительные аргументы |Нет |массив или объект |Дополнительные значения для объединения элементов. |

### <a name="examples"></a>Примеры

В следующем примере показано, как использовать функцию union с массивами и объектами:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "firstObject": {
            "type": "object",
            "defaultValue": {"one": "a", "two": "b", "three": "c"}
        },
        "secondObject": {
            "type": "object",
            "defaultValue": {"four": "d", "five": "e", "six": "f"}
        },
        "firstArray": {
            "type": "array",
            "defaultValue": ["one", "two", "three"]
        },
        "secondArray": {
            "type": "array",
            "defaultValue": ["four", "five"]
        }
    },
    "resources": [
    ],
    "outputs": {
        "objectOutput": {
            "type": "object",
            "value": "[union(parameters('firstObject'), parameters('secondObject'))]"
        },
        "arrayOutput": {
            "type": "array",
            "value": "[union(parameters('firstArray'), parameters('secondArray'))]"
        }
    }
}
```

### <a name="return-value"></a>Возвращаемое значение

Массив или объект.

## <a name="next-steps"></a>Дальнейшие действия
* Описание разделов в шаблоне Azure Resource Manager см. в статье [Создание шаблонов Azure Resource Manager](resource-group-authoring-templates.md).
* Инструкции по объединению нескольких шаблонов см. в статье [Использование связанных шаблонов в Azure Resource Manager](resource-group-linked-templates.md).
* Указания по выполнению заданного количества циклов итерации при создании типа ресурса см. в статье [Создание нескольких экземпляров ресурсов в Azure Resource Manager](resource-group-create-multiple.md).
* Указания по развертыванию созданного шаблона см. в статье, посвященной [развертыванию приложения с помощью шаблона Azure Resource Manager](resource-group-template-deploy.md).



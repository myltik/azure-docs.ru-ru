---
title: "Строковые функции шаблона Azure Resource Manager | Документация Майкрософт"
description: "Описывает функции, используемые в шаблоне Azure Resource Manager для работы со строками."
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
ms.openlocfilehash: 9b75d0ede3ec1b291936ee0a53778afe10ba91db
ms.contentlocale: ru-ru
ms.lasthandoff: 04/28/2017


---
# <a name="string-functions-for-azure-resource-manager-templates"></a>Строковые функции для шаблонов Azure Resource Manager

Диспетчер ресурсов предоставляет следующие функции для работы со строками:

* [base64](#base64)
* [base64ToJson](#base64tojson)
* [base64ToString](#base64tostring)
* [bool](#bool);
* [concat](#concat)
* [contains](#contains)
* [dataUri](#datauri)
* [dataUriToString](#datauritostring)
* [empty](#empty)
* [endsWith](#endswith)
* [first](#first)
* [indexOf](#indexof)
* [last](#last)
* [lastIndexOf](#lastindexof)
* [длина](#length)
* [padLeft](#padleft)
* [replace](#replace)
* [skip](#skip)
* [split](#split)
* [startsWith](resource-group-template-functions-string.md#startswith)
* [string](#string)
* [substring](#substring)
* [take](#take)
* [toLower](#tolower)
* [toUpper](#toupper)
* [trim](#trim)
* [uniqueString](#uniquestring)
* [uri](#uri)
* [uriComponent](resource-group-template-functions-string.md#uricomponent)
* [uriComponentToString](resource-group-template-functions-string.md#uricomponenttostring)

<a id="base64" />

## <a name="base64"></a>base64
`base64(inputString)`

Возвращает входную строку в кодировке Base64.

### <a name="parameters"></a>Параметры

| Параметр | Обязательно | Тип | Описание |
|:--- |:--- |:--- |:--- |
| входная_строка |Да |string |Значение, которое нужно вернуть в кодировке base64. |

### <a name="examples"></a>Примеры

В следующем примере показано, как использовать функцию base64.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "stringData": {
            "type": "string",
            "defaultValue": "one, two, three"
        },
        "jsonFormattedData": {
            "type": "string",
            "defaultValue": "{'one': 'a', 'two': 'b'}"
        }
    },
    "variables": {
        "base64String": "[base64(parameters('stringData'))]",
        "base64Object": "[base64(parameters('jsonFormattedData'))]"
    },
    "resources": [
    ],
    "outputs": {
        "base64Output": {
            "type": "string",
            "value": "[variables('base64String')]"
        },
        "toStringOutput": {
            "type": "string",
            "value": "[base64ToString(variables('base64String'))]"
        },
        "toJsonOutput": {
            "type": "object",
            "value": "[base64ToJson(variables('base64Object'))]"
        }
    }
}
```

### <a name="return-value"></a>Возвращаемое значение

Строка, содержащая представление в кодировке base64.

<a id="base64tojson" />

## <a name="base64tojson"></a>base64ToJson
`base64tojson`

Преобразует представление в кодировке base64 в объект JSON.

### <a name="parameters"></a>Параметры

| Параметр | Обязательно | Тип | Описание |
|:--- |:--- |:--- |:--- |
| base64Value |Да |string |Представление в кодировке base64, которое необходимо преобразовать в объект JSON. |

### <a name="examples"></a>Примеры

В следующем примере функция base64ToJson используется для преобразования значения в кодировке base64:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "stringData": {
            "type": "string",
            "defaultValue": "one, two, three"
        },
        "jsonFormattedData": {
            "type": "string",
            "defaultValue": "{'one': 'a', 'two': 'b'}"
        }
    },
    "variables": {
        "base64String": "[base64(parameters('stringData'))]",
        "base64Object": "[base64(parameters('jsonFormattedData'))]"
    },
    "resources": [
    ],
    "outputs": {
        "base64Output": {
            "type": "string",
            "value": "[variables('base64String')]"
        },
        "toStringOutput": {
            "type": "string",
            "value": "[base64ToString(variables('base64String'))]"
        },
        "toJsonOutput": {
            "type": "object",
            "value": "[base64ToJson(variables('base64Object'))]"
        }
    }
}
```

### <a name="return-value"></a>Возвращаемое значение

Объект JSON.

<a id="base64tostring" />

## <a name="base64tostring"></a>base64ToString
`base64ToString(base64Value)`

Преобразует представление в кодировке base64 в строку.

### <a name="parameters"></a>Параметры

| Параметр | Обязательно | Тип | Описание |
|:--- |:--- |:--- |:--- |
| base64Value |Да |string |Представление в кодировке base64, которое необходимо преобразовать в строку. |

### <a name="examples"></a>Примеры

В следующем примере функция base64ToString используется для преобразования значения в кодировке base64:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "stringData": {
            "type": "string",
            "defaultValue": "one, two, three"
        },
        "jsonFormattedData": {
            "type": "string",
            "defaultValue": "{'one': 'a', 'two': 'b'}"
        }
    },
    "variables": {
        "base64String": "[base64(parameters('stringData'))]",
        "base64Object": "[base64(parameters('jsonFormattedData'))]"
    },
    "resources": [
    ],
    "outputs": {
        "base64Output": {
            "type": "string",
            "value": "[variables('base64String')]"
        },
        "toStringOutput": {
            "type": "string",
            "value": "[base64ToString(variables('base64String'))]"
        },
        "toJsonOutput": {
            "type": "object",
            "value": "[base64ToJson(variables('base64Object'))]"
        }
    }
}
```

### <a name="return-value"></a>Возвращаемое значение

Строка преобразованного значения в кодировке base64.

<a id="bool" />

## <a name="bool"></a>bool
`bool(arg1)`

Преобразует параметр в логическое значение.

### <a name="parameters"></a>Параметры

| Параметр | Обязательно | Тип | Описание |
|:--- |:--- |:--- |:--- |
| arg1 |Да |строка или целое число |Значение, которое необходимо преобразовать в логическое. |

### <a name="examples"></a>Примеры

В следующем примере показано, как использовать функцию bool со строкой или целым числом:

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

### <a name="return-value"></a>Возвращаемое значение
Логическое значение.

<a id="concat" />

## <a name="concat"></a>concat
`concat (arg1, arg2, arg3, ...)`

Объединяет несколько строковых значений и возвращает объединенную строку или объединяет несколько массивов и возвращает объединенный массив.

### <a name="parameters"></a>Параметры

| Параметр | Обязательно | Тип | Описание |
|:--- |:--- |:--- |:--- |
| arg1 |Да |строка или массив |Первое значение для сцепки. |
| дополнительные аргументы |Нет |string |Дополнительные значения в последовательном порядке для сцепки. |

### <a name="examples"></a>Примеры

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

### <a name="return-value"></a>Возвращаемое значение
Строка или массив объединенных значений.

<a id="contains" />

## <a name="contains"></a>contains
`contains (container, itemToFind)`

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

<a id="datauri" />

## <a name="datauri"></a>dataUri
`dataUri(stringToConvert)`

Преобразует значение в универсальный код ресурса (URI) данных.

### <a name="parameters"></a>Параметры

| Параметр | Обязательно | Тип | Описание |
|:--- |:--- |:--- |:--- |
| stringToConvert |Да |string |Значение, которое необходимо преобразовать в URI данных. |

### <a name="examples"></a>Примеры

В следующем примере значение преобразуется в URI данных, а URI данных преобразуется в строку:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "stringToTest": {
            "type": "string",
            "defaultValue": "Hello"
        },
        "dataFormattedString": {
            "type": "string",
            "defaultValue": "data:;base64,SGVsbG8sIFdvcmxkIQ=="
        }
    },
    "resources": [],
    "outputs": {
        "dataUriOutput": {
            "value": "[dataUri(parameters('stringToTest'))]",
            "type" : "string"
        },
        "toStringOutput": {
            "type": "string",
            "value": "[dataUriToString(parameters('dataFormattedString'))]"
        }
    }
}
```

### <a name="return-value"></a>Возвращаемое значение

Строка в формате URI данных.

<a id="datauritostring" />

## <a name="datauritostring"></a>dataUriToString
`dataUriToString(dataUriToConvert)`

Преобразует значение в формате URI данных в строку.

### <a name="parameters"></a>Параметры

| Параметр | Обязательно | Тип | Описание |
|:--- |:--- |:--- |:--- |
| dataUriToConvert |Да |string |Значение URI данных, которое необходимо преобразовать. |

### <a name="examples"></a>Примеры

В следующем примере значение преобразуется в URI данных, а URI данных преобразуется в строку:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "stringToTest": {
            "type": "string",
            "defaultValue": "Hello"
        },
        "dataFormattedString": {
            "type": "string",
            "defaultValue": "data:;base64,SGVsbG8sIFdvcmxkIQ=="
        }
    },
    "resources": [],
    "outputs": {
        "dataUriOutput": {
            "value": "[dataUri(parameters('stringToTest'))]",
            "type" : "string"
        },
        "toStringOutput": {
            "type": "string",
            "value": "[dataUriToString(parameters('dataFormattedString'))]"
        }
    }
}
```

### <a name="return-value"></a>Возвращаемое значение

Строка, содержащая преобразованное значение.

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

<a id="endswith" />

## <a name="endswith"></a>endsWith
`endsWith(stringToSearch, stringToFind)`

Определяет, заканчивается ли строка определенным значением. При сравнении регистр не учитывается.

### <a name="parameters"></a>Параметры

| Параметр | Обязательно | Тип | Описание |
|:--- |:--- |:--- |:--- |
| stringToSearch |Да |string |Значение, содержащее элемент, который необходимо найти. |
| stringToFind |Да |string |Значение, которое необходимо найти. |

### <a name="examples"></a>Примеры

В следующем примере показано, как использовать функции startsWith и endsWith:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "startsTrue": {
            "value": "[startsWith('abcdef', 'ab')]",
            "type" : "bool"
        },
        "startsCapTrue": {
            "value": "[startsWith('abcdef', 'A')]",
            "type" : "bool"
        },
        "startsFalse": {
            "value": "[startsWith('abcdef', 'e')]",
            "type" : "bool"
        },
        "endsTrue": {
            "value": "[endsWith('abcdef', 'ef')]",
            "type" : "bool"
        },
        "endsCapTrue": {
            "value": "[endsWith('abcdef', 'F')]",
            "type" : "bool"
        },
        "endsFalse": {
            "value": "[endsWith('abcdef', 'e')]",
            "type" : "bool"
        }
    }
}
```

### <a name="return-value"></a>Возвращаемое значение

**True**, если последний знак или знаки строки соответствуют значению. В противном случае — **False**.

<a id="first" />

## <a name="first"></a>first
`first(arg1)`

Возвращает первый знак строки или первый элемент массива.

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

Строка первого знака или тип (строка, целое число, массив или объект) первого элемента в массиве.

<a id="indexof" />

## <a name="indexof"></a>indexOf
`indexOf(stringToSearch, stringToFind)`

Возвращает первую позицию значения в строке. При сравнении регистр не учитывается.

### <a name="parameters"></a>Параметры

| Параметр | Обязательно | Тип | Описание |
|:--- |:--- |:--- |:--- |
| stringToSearch |Да |string |Значение, содержащее элемент, который необходимо найти. |
| stringToFind |Да |string |Значение, которое необходимо найти. |

### <a name="examples"></a>Примеры

В следующем примере показано, как использовать функции indexOf и lastIndexOf:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "firstT": {
            "value": "[indexOf('test', 't')]",
            "type" : "int"
        },
        "lastT": {
            "value": "[lastIndexOf('test', 't')]",
            "type" : "int"
        },
        "firstString": {
            "value": "[indexOf('abcdef', 'CD')]",
            "type" : "int"
        },
        "lastString": {
            "value": "[lastIndexOf('abcdef', 'AB')]",
            "type" : "int"
        },
        "notFound": {
            "value": "[indexOf('abcdef', 'z')]",
            "type" : "int"
        }
    }
}
```

### <a name="return-value"></a>Возвращаемое значение

Целое число, представляющее позицию искомого элемента. Значение отсчитывается, начиная с нуля. Если элемент не найден, то возвращается значение -1.


<a id="last" />

## <a name="last"></a>last
`last (arg1)`

Возвращает последний знак строки или последний элемент массива.

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

Строка последнего знака или тип (строка, целое число, массив или объект) последнего элемента в массиве.

<a id="lastindexof" />

## <a name="lastindexof"></a>lastIndexOf
`lastIndexOf(stringToSearch, stringToFind)`

Возвращает последнюю позицию значения в строке. При сравнении регистр не учитывается.

### <a name="parameters"></a>Параметры

| Параметр | Обязательно | Тип | Описание |
|:--- |:--- |:--- |:--- |
| stringToSearch |Да |string |Значение, содержащее элемент, который необходимо найти. |
| stringToFind |Да |string |Значение, которое необходимо найти. |

### <a name="examples"></a>Примеры

В следующем примере показано, как использовать функции indexOf и lastIndexOf:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "firstT": {
            "value": "[indexOf('test', 't')]",
            "type" : "int"
        },
        "lastT": {
            "value": "[lastIndexOf('test', 't')]",
            "type" : "int"
        },
        "firstString": {
            "value": "[indexOf('abcdef', 'CD')]",
            "type" : "int"
        },
        "lastString": {
            "value": "[lastIndexOf('abcdef', 'AB')]",
            "type" : "int"
        },
        "notFound": {
            "value": "[indexOf('abcdef', 'z')]",
            "type" : "int"
        }
    }
}
```

### <a name="return-value"></a>Возвращаемое значение

Целое число, представляющее последнюю позицию искомого элемента. Значение отсчитывается, начиная с нуля. Если элемент не найден, то возвращается значение -1.


<a id="length" />

## <a name="length"></a>длина
`length(string)`

Возвращает число знаков в строке или элементов в массиве.

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

### <a name="return-value"></a>Возвращаемое значение

Целое число. 

<a id="padleft" />

## <a name="padleft"></a>padLeft
`padLeft(valueToPad, totalLength, paddingCharacter)`

Возвращает выровненную по правому краю строку, добавляя символы в левую часть до достижения общее указанной длины.

### <a name="parameters"></a>Параметры

| Параметр | Обязательно | Тип | Описание |
|:--- |:--- |:--- |:--- |
| значение_для_заполнения  |Да |строка или целое число |Значение, выравниваемое по правому краю. |
| общая_длина |Да |int |Общее число символов в возвращаемой строке. |
| символ_заполнения |Нет |один знак |Символ, используемый для заполнения левой части до достижения общей длины. Значение по умолчанию — пробел. |

Если длина исходной строки превышает число знаков для заполнения, то знаки не добавляются.

### <a name="examples"></a>Примеры

В следующем примере показано, как заполнить указанное пользователем значение параметра, добавляя знак нуля до достижения общего числа знаков. 

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "testString": {
            "type": "string",
            "defaultValue": "123"
        },
        "totalCharacters": {
            "type": "int",
            "defaultValue": 10
        }
    },
    "resources": [],
    "outputs": {
        "stringOutput": {
            "type": "string",
            "value": "[padLeft(parameters('testString'),parameters('totalCharacters'),'0')]"
        }
    }
}
```

### <a name="return-value"></a>Возвращаемое значение

Строка, содержащая по крайней мере число указанных знаков.

<a id="replace" />

## <a name="replace"></a>replace
`replace(originalString, oldCharacter, newCharacter)`

Возвращает новую строку, где все экземпляры одного символа заменены другим символом.

### <a name="parameters"></a>Параметры

| Параметр | Обязательно | Тип | Описание |
|:--- |:--- |:--- |:--- |
| исходная_строка |Да |string |Значение, в котором все экземпляры одного знака будут заменены другим знаком. |
| старый_символ |Да |string |Символ, который удаляется из исходной строки. |
| новый_символ |Да |string |Символ, добавляемый вместо удаляемого символа. |

### <a name="examples"></a>Примеры

В следующем примере показано, как удалить все тире из предоставленной пользователем строки.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "testString": {
            "type": "string",
            "defaultValue": "123-123-1234"
        }
    },
    "resources": [],
    "outputs": {
        "stringOutput": {
            "type": "string",
            "value": "[replace(parameters('testString'),'-', '')]"
        }
    }
}
```

### <a name="return-value"></a>Возвращаемое значение

Строка с замененными знаками.

<a id="skip" />

## <a name="skip"></a>skip
`skip(originalValue, numberToSkip)`

Возвращает строку, содержащую все знаки из исходной строки, начиная с заданной позиции, или массив, содержащий все элементы из исходного массива, начиная с заданной позиции.

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

<a id="split" />

## <a name="split"></a>split
`split(inputString, delimiter)`

Возвращает массив строк, содержащий подстроки входной строки, разделенные переданными разделителями.

### <a name="parameters"></a>Параметры

| Параметр | Обязательно | Тип | Описание |
|:--- |:--- |:--- |:--- |
| входная_строка |Да |string |Строка для разделения. |
| delimiter |Да |строка или массив строк |Разделитель для разбиения строки. |

### <a name="examples"></a>Примеры

В следующем примере входная строка разбивается с помощью запятой или точки с запятой.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "firstString": {
            "type": "string",
            "defaultValue": "one,two,three"
        },
        "secondString": {
            "type": "string",
            "defaultValue": "one;two,three"
        }
    },
    "variables": {
        "delimiters": [ ",", ";" ]
    },
    "resources": [],
    "outputs": {
        "firstOutput": {
            "type": "array",
            "value": "[split(parameters('firstString'),',')]"
        },
        "secondOutput": {
            "type": "array",
            "value": "[split(parameters('secondString'),variables('delimiters'))]"
        }
    }
}
```

### <a name="return-value"></a>Возвращаемое значение

Массив строк.

<a id="startswith" />

## <a name="startswith"></a>startsWith
`startsWith(stringToSearch, stringToFind)`

Определяет, начинается ли строка с определенного значения. При сравнении регистр не учитывается.

### <a name="parameters"></a>Параметры

| Параметр | Обязательно | Тип | Описание |
|:--- |:--- |:--- |:--- |
| stringToSearch |Да |string |Значение, содержащее элемент, который необходимо найти. |
| stringToFind |Да |string |Значение, которое необходимо найти. |

### <a name="examples"></a>Примеры

В следующем примере показано, как использовать функции startsWith и endsWith:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "startsTrue": {
            "value": "[startsWith('abcdef', 'ab')]",
            "type" : "bool"
        },
        "startsCapTrue": {
            "value": "[startsWith('abcdef', 'A')]",
            "type" : "bool"
        },
        "startsFalse": {
            "value": "[startsWith('abcdef', 'e')]",
            "type" : "bool"
        },
        "endsTrue": {
            "value": "[endsWith('abcdef', 'ef')]",
            "type" : "bool"
        },
        "endsCapTrue": {
            "value": "[endsWith('abcdef', 'F')]",
            "type" : "bool"
        },
        "endsFalse": {
            "value": "[endsWith('abcdef', 'e')]",
            "type" : "bool"
        }
    }
}
```

### <a name="return-value"></a>Возвращаемое значение

**True**, если первый знак или знаки строки соответствуют значению. В противном случае — **False**.


<a id="string" />

## <a name="string"></a>string
`string(valueToConvert)`

Преобразует указанное значение в строку.

### <a name="parameters"></a>Параметры

| Параметр | Обязательно | Тип | Описание |
|:--- |:--- |:--- |:--- |
| valueToConvert |Да | Любой |Значение, которое необходимо преобразовать в строку. Можно преобразовать любой тип значения, включая объекты и массивы. |

### <a name="examples"></a>Примеры

В следующем примере показано, как преобразовать различные типы значений в строки:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "testObject": {
            "type": "object",
            "defaultValue": {
                "valueA": 10,
                "valueB": "Example Text"
            }
        },
        "testArray": {
            "type": "array",
            "defaultValue": [
                "a",
                "b",
                "c"
            ]
        },
        "testInt": {
            "type": "int",
            "defaultValue": 5
        }
    },
    "resources": [],
    "outputs": {
        "objectOutput": {
            "type": "string",
            "value": "[string(parameters('testObject'))]"
        },
        "arrayOutput": {
            "type": "string",
            "value": "[string(parameters('testArray'))]"
        },
        "intOutput": {
            "type": "string",
            "value": "[string(parameters('testInt'))]"
        }
    }
}
```

### <a name="return-value"></a>Возвращаемое значение

Строка.

<a id="substring" />

## <a name="substring"></a>substring
`substring(stringToParse, startIndex, length)`

Возвращает подстроку, которая начинается с указанной позиции и содержит указанное количество символов.

### <a name="parameters"></a>Параметры

| Параметр | Обязательно | Тип | Описание |
|:--- |:--- |:--- |:--- |
| stringToParse |Да |string |Исходная строка, из которой извлекается подстрока. |
| startIndex |Нет |int |Отсчитываемая от нуля позиция первого знака для подстроки. |
| длина |Нет |int |Число символов в подстроке. Этот параметр должен ссылаться на позицию в строке. |

### <a name="examples"></a>Примеры

В следующем примере из параметра извлекается подстрока.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "testString": {
            "type": "string",
            "defaultValue": "one two three"
        }
    },
    "resources": [],
    "outputs": {
        "substringOutput": {
            "value": "[substring(parameters('testString'), 4, 3)]",
            "type": "string"
        }
    }
}
```

Следующий пример завершается ошибкой "Параметры индекса и длины должны относиться к расположению в строке. Параметр индекса: 0, параметр длины: 11, параметр длины строкового параметра: 10".

```json
"parameters": {
    "inputString": { "type": "string", "value": "1234567890" }
},
"variables": { 
    "prefix": "[substring(parameters('inputString'), 0, 11)]"
}
```

<a id="take" />

## <a name="take"></a>take
`take(originalValue, numberToTake)`

Возвращает строку, содержащую указанное число знаков, считая от начала строки, или массив, содержащий указанное число элементов, считая от начала массива.

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

<a id="tolower" />

## <a name="tolower"></a>toLower
`toLower(stringToChange)`

Преобразует указанную строку в нижний регистр.

### <a name="parameters"></a>Параметры

| Параметр | Обязательно | Тип | Описание |
|:--- |:--- |:--- |:--- |
| изменяемая_строка |Да |string |Значение, преобразовываемое в нижний регистр. |

### <a name="examples"></a>Примеры

В следующем примере значение параметра преобразуется в нижний регистр и в верхний регистр.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "testString": {
            "type": "string",
            "defaultValue": "One Two Three"
        }
    },
    "resources": [],
    "outputs": {
        "toLowerOutput": {
            "value": "[toLower(parameters('testString'))]",
            "type": "string"
        },
        "toUpperOutput": {
            "type": "string",
            "value": "[toUpper(parameters('testString'))]"
        }
    }
}
```

<a id="toupper" />

## <a name="toupper"></a>toUpper
`toUpper(stringToChange)`

Преобразует указанную строку в верхний регистр.

### <a name="parameters"></a>Параметры

| Параметр | Обязательно | Тип | Описание |
|:--- |:--- |:--- |:--- |
| изменяемая_строка |Да |string |Значение, преобразовываемое в верхний регистр. |

### <a name="examples"></a>Примеры

В следующем примере значение параметра преобразуется в нижний регистр и в верхний регистр.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "testString": {
            "type": "string",
            "defaultValue": "One Two Three"
        }
    },
    "resources": [],
    "outputs": {
        "toLowerOutput": {
            "value": "[toLower(parameters('testString'))]",
            "type": "string"
        },
        "toUpperOutput": {
            "type": "string",
            "value": "[toUpper(parameters('testString'))]"
        }
    }
}
```

<a id="trim" />

## <a name="trim"></a>trim
`trim (stringToTrim)`

Удаляет все начальные и конечные знаки пробела из указанной строки.

### <a name="parameters"></a>Параметры

| Параметр | Обязательно | Тип | Описание |
|:--- |:--- |:--- |:--- |
| stringToTrim |Да |string |Обрезаемое значение. |

### <a name="examples"></a>Примеры

В следующем примере из параметра удаляются пробелы.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "testString": {
            "type": "string",
            "defaultValue": "    one two three   "
        }
    },
    "resources": [],
    "outputs": {
        "return": {
            "type": "string",
            "value": "[trim(parameters('testString'))]"
        }
    }
}
```

<a id="uniquestring" />

## <a name="uniquestring"></a>uniqueString
`uniqueString (baseString, ...)`

Создает детерминированную хэш-строку на основании значений, указанных как параметры. 

### <a name="parameters"></a>Параметры

| Параметр | Обязательно | Тип | Описание |
|:--- |:--- |:--- |:--- |
| baseString |Да |string |Значение, используемое в хэш-функции для создания уникальной строки. |
| Дополнительные параметры (если необходимы) |Нет |string |Можно добавить столько строк, сколько необходимо для создания значения, которое задает уровень уникальности. |

### <a name="remarks"></a>Примечания

Эта функция полезна в тех случаях, когда необходимо создать уникальное имя ресурса. Указываются значения параметров, которые ограничивают область уникальности результата. Можно указать, является ли уникальным имя в подписке, группе ресурсов или развертывании. 

Возвращаемое значение — не произвольная строка, а, скорее, результат хэш-функции. Возвращаемое значение содержит 13 знаков. Оно не является глобально уникальным. Вы можете добавить к значению префикс из своего соглашения об именовании, чтобы создать значимое имя. В следующем примере показан формат возвращаемого значения. Фактическое значение зависит от указанных параметров.

    tcvhiyu5h2o5o

### <a name="examples"></a>Примеры

В следующих примерах показывается, как использовать uniqueString при создании уникального значения для часто используемых уровней.

Уникальное в пределах подписки.

```json
"[uniqueString(subscription().subscriptionId)]"
```

Уникальное в пределах группы ресурсов.

```json
"[uniqueString(resourceGroup().id)]"
```

Уникальное в пределах развертывания для группы ресурсов.

```json
"[uniqueString(resourceGroup().id, deployment().name)]"
```

В следующем примере показано, как создать уникальное имя учетной записи хранения на основе вашей группы ресурсов. Внутри группы ресурсов имена не будут уникальными, если создавать их таким способом.

```json
"resources": [{ 
    "name": "[concat('storage', uniqueString(resourceGroup().id))]", 
    "type": "Microsoft.Storage/storageAccounts", 
    ...
```

### <a name="return-value"></a>Возвращаемое значение

Строка, содержащая 13 знаков

<a id="uri" />

## <a name="uri"></a>uri
`uri (baseUri, relativeUri)`

Создает абсолютный URI, объединяя строки baseUri и relativeUri.

### <a name="parameters"></a>Параметры

| Параметр | Обязательно | Тип | Описание |
|:--- |:--- |:--- |:--- |
| baseUri |Да |string |Строка базового универсального кода ресурса (URI). |
| relativeUri |Да |string |Строка относительного универсального кода ресурса (URI), добавляемая к строке базового универсального кода ресурса (URI). |

Значение параметра **baseUri** может включать в себя определенный файл, однако при построении универсального кода ресурса (URI) используется только базовый путь. Например, если передать `http://contoso.com/resources/azuredeploy.json` в качестве параметра baseUri, то базовый универсальный код ресурса (URI) будет иметь значение `http://contoso.com/resources/`.

### <a name="examples"></a>Примеры

В следующем примере показано создание ссылки на вложенный шаблон в зависимости от значения параметра родительского шаблона.

```json
"templateLink": "[uri(deployment().properties.templateLink.uri, 'nested/azuredeploy.json')]"
```

В следующем примере показано, как использовать функции uri, uriComponent и uriComponentToString:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "variables": {
        "uriFormat": "[uri('http://contoso.com/resources/', 'nested/azuredeploy.json')]",
        "uriEncoded": "[uriComponent(variables('uriFormat'))]" 
    },
    "resources": [
    ],
    "outputs": {
        "uriOutput": {
            "type": "string",
            "value": "[variables('uriFormat')]"
        },
        "componentOutput": {
            "type": "string",
            "value": "[variables('uriEncoded')]"
        },
        "toStringOutput": {
            "type": "string",
            "value": "[uriComponentToString(variables('uriEncoded'))]"
        }
    }
}
```

### <a name="return-value"></a>Возвращаемое значение

Строка, представляющая абсолютный URI для базового и относительного значений.

<a id="uricomponent" />

## <a name="uricomponent"></a>uriComponent
`uricomponent(stringToEncode)`

Кодирует URI.

### <a name="parameters"></a>Параметры

| Параметр | Обязательно | Тип | Описание |
|:--- |:--- |:--- |:--- |
| stringToEncode |Да |string |Значение для кодирования. |

### <a name="examples"></a>Примеры

В следующем примере показано, как использовать функции uri, uriComponent и uriComponentToString:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "variables": {
        "uriFormat": "[uri('http://contoso.com/resources/', 'nested/azuredeploy.json')]",
        "uriEncoded": "[uriComponent(variables('uriFormat'))]" 
    },
    "resources": [
    ],
    "outputs": {
        "uriOutput": {
            "type": "string",
            "value": "[variables('uriFormat')]"
        },
        "componentOutput": {
            "type": "string",
            "value": "[variables('uriEncoded')]"
        },
        "toStringOutput": {
            "type": "string",
            "value": "[uriComponentToString(variables('uriEncoded'))]"
        }
    }
}
```

### <a name="return-value"></a>Возвращаемое значение

Строка со значением, закодированным в формате URI.

<a id="uricomponenttostring" />

## <a name="uricomponenttostring"></a>uriComponentToString
`uriComponentToString(uriEncodedString)`

Возвращает строку со значением, закодированным в формате URI.

### <a name="parameters"></a>Параметры

| Параметр | Обязательно | Тип | Описание |
|:--- |:--- |:--- |:--- |
| uriEncodedString |Да |string |Значение, закодированное в формате URI, которое необходимо преобразовать в строку. |

### <a name="examples"></a>Примеры

В следующем примере показано, как использовать функции uri, uriComponent и uriComponentToString:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "variables": {
        "uriFormat": "[uri('http://contoso.com/resources/', 'nested/azuredeploy.json')]",
        "uriEncoded": "[uriComponent(variables('uriFormat'))]" 
    },
    "resources": [
    ],
    "outputs": {
        "uriOutput": {
            "type": "string",
            "value": "[variables('uriFormat')]"
        },
        "componentOutput": {
            "type": "string",
            "value": "[variables('uriEncoded')]"
        },
        "toStringOutput": {
            "type": "string",
            "value": "[uriComponentToString(variables('uriEncoded'))]"
        }
    }
}
```

### <a name="return-value"></a>Возвращаемое значение

Декодированная строка со значением, закодированным в формате URI.

## <a name="next-steps"></a>Дальнейшие действия
* Описание разделов в шаблоне Azure Resource Manager см. в статье [Создание шаблонов Azure Resource Manager](resource-group-authoring-templates.md).
* Инструкции по объединению нескольких шаблонов см. в статье [Использование связанных шаблонов в Azure Resource Manager](resource-group-linked-templates.md).
* Указания по выполнению заданного количества циклов итерации при создании типа ресурса см. в статье [Создание нескольких экземпляров ресурсов в Azure Resource Manager](resource-group-create-multiple.md).
* Указания по развертыванию созданного шаблона см. в статье, посвященной [развертыванию приложения с помощью шаблона Azure Resource Manager](resource-group-template-deploy.md).



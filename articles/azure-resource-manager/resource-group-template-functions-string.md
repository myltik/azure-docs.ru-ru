---
title: Строковые функции шаблона Azure Resource Manager | Документация Майкрософт
description: Описывает функции, используемые в шаблоне Azure Resource Manager для работы со строками.
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
ms.openlocfilehash: 33a49a9fb66240382b0bb4e0bedbb07b8d78a763
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
ms.locfileid: "34360365"
---
# <a name="string-functions-for-azure-resource-manager-templates"></a>Строковые функции для шаблонов Azure Resource Manager

Диспетчер ресурсов предоставляет следующие функции для работы со строками:

* [base64](#base64)
* [base64ToJson](#base64tojson)
* [base64ToString](#base64tostring)
* [concat](#concat)
* [contains](#contains)
* [dataUri](#datauri)
* [dataUriToString](#datauritostring)
* [empty](#empty)
* [endsWith](#endswith)
* [first](#first)
* [guid](#guid)
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

| Параметр | Обязательно | type | ОПИСАНИЕ |
|:--- |:--- |:--- |:--- |
| входная_строка |Yes |строка |Значение, которое нужно вернуть в кодировке base64. |

### <a name="return-value"></a>Возвращаемое значение

Строка, содержащая представление в кодировке base64.

### <a name="examples"></a>Примеры

В следующем [примере шаблона](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/base64.json) показано, как использовать функцию base64.

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

Выходные данные из предыдущего примера со значениями по умолчанию:

| ИМЯ | type | Значение |
| ---- | ---- | ----- |
| base64Output | Строка | b25lLCB0d28sIHRocmVl |
| toStringOutput | Строка | one, two, three |
| toJsonOutput | Объект. | {"one": "a", "two": "b"} |

Развернуть этот пример шаблона с помощью Azure CLI можно так:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/base64.json
```

Развернуть этот пример шаблона с помощью PowerShell можно так:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/base64.json
```

<a id="base64tojson" />

## <a name="base64tojson"></a>base64ToJson
`base64tojson`

Преобразует представление в кодировке base64 в объект JSON.

### <a name="parameters"></a>Параметры

| Параметр | Обязательно | type | ОПИСАНИЕ |
|:--- |:--- |:--- |:--- |
| base64Value |Yes |строка |Представление в кодировке base64, которое необходимо преобразовать в объект JSON. |

### <a name="return-value"></a>Возвращаемое значение

Объект JSON.

### <a name="examples"></a>Примеры

В следующем [примере шаблона](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/base64.json) функция base64ToJson используется для преобразования значения в кодировке base64.

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

Выходные данные из предыдущего примера со значениями по умолчанию:

| ИМЯ | type | Значение |
| ---- | ---- | ----- |
| base64Output | Строка | b25lLCB0d28sIHRocmVl |
| toStringOutput | Строка | one, two, three |
| toJsonOutput | Объект. | {"one": "a", "two": "b"} |

Развернуть этот пример шаблона с помощью Azure CLI можно так:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/base64.json
```

Развернуть этот пример шаблона с помощью PowerShell можно так:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/base64.json
```

<a id="base64tostring" />

## <a name="base64tostring"></a>base64ToString
`base64ToString(base64Value)`

Преобразует представление в кодировке base64 в строку.

### <a name="parameters"></a>Параметры

| Параметр | Обязательно | type | ОПИСАНИЕ |
|:--- |:--- |:--- |:--- |
| base64Value |Yes |строка |Представление в кодировке base64, которое необходимо преобразовать в строку. |

### <a name="return-value"></a>Возвращаемое значение

Строка преобразованного значения в кодировке base64.

### <a name="examples"></a>Примеры

В следующем [примере шаблона](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/base64.json) функция base64ToString используется для преобразования значения в кодировке base64.

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

Выходные данные из предыдущего примера со значениями по умолчанию:

| ИМЯ | type | Значение |
| ---- | ---- | ----- |
| base64Output | Строка | b25lLCB0d28sIHRocmVl |
| toStringOutput | Строка | one, two, three |
| toJsonOutput | Объект. | {"one": "a", "two": "b"} |

Развернуть этот пример шаблона с помощью Azure CLI можно так:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/base64.json
```

Развернуть этот пример шаблона с помощью PowerShell можно так:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/base64.json
```

<a id="concat" />

## <a name="concat"></a>concat
`concat (arg1, arg2, arg3, ...)`

Объединяет несколько строковых значений и возвращает объединенную строку или объединяет несколько массивов и возвращает объединенный массив.

### <a name="parameters"></a>Параметры

| Параметр | Обязательно | type | ОПИСАНИЕ |
|:--- |:--- |:--- |:--- |
| arg1 |Yes |строка или массив |Первое значение для сцепки. |
| дополнительные аргументы |Нет  |строка |Дополнительные значения в последовательном порядке для сцепки. |

### <a name="return-value"></a>Возвращаемое значение
Строка или массив объединенных значений.

### <a name="examples"></a>Примеры

В следующем [примере шаблона](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/concat-string.json) показано, как объединить два строковых значения и получить сцепленную строку.

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
            "value": "[concat(parameters('prefix'), '-', uniqueString(resourceGroup().id))]",
            "type" : "string"
        }
    }
}
```

Выходные данные из предыдущего примера со значениями по умолчанию:

| ИМЯ | type | Значение |
| ---- | ---- | ----- |
| concatOutput | Строка | prefix-5yj4yjf5mbg72 |

Развернуть этот пример шаблона с помощью Azure CLI можно так:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/concat-string.json
```

Развернуть этот пример шаблона с помощью PowerShell можно так:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/concat-string.json
```

В следующем [примере шаблона](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/concat-array.json) показано, как объединить два массива.

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

Выходные данные из предыдущего примера со значениями по умолчанию:

| ИМЯ | type | Значение |
| ---- | ---- | ----- |
| return | Массив, | ["1-1", "1-2", "1-3", "2-1", "2-2", "2-3"] |

Развернуть этот пример шаблона с помощью Azure CLI можно так:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/concat-array.json
```

Развернуть этот пример шаблона с помощью PowerShell можно так:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/concat-array.json
```

<a id="contains" />

## <a name="contains"></a>contains
`contains (container, itemToFind)`

Проверяет, содержит ли массив значение, содержит ли объект ключ или содержит ли строка подстроку.

### <a name="parameters"></a>Параметры

| Параметр | Обязательно | type | ОПИСАНИЕ |
|:--- |:--- |:--- |:--- |
| container |Yes |массив, объект или строка |Значение, содержащее значение, которое необходимо найти. |
| itemToFind |Yes |строка или целое число |Значение, которое необходимо найти. |

### <a name="return-value"></a>Возвращаемое значение

**True**, если элемент найден. В противном случае — **False**.

### <a name="examples"></a>Примеры

В следующем [примере шаблона](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/contains.json) показано, как использовать функцию contains с различными типами.

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

Выходные данные из предыдущего примера со значениями по умолчанию:

| ИМЯ | type | Значение |
| ---- | ---- | ----- |
| stringTrue | Bool | Истина |
| stringFalse | Bool | Ложь |
| objectTrue | Bool | Истина |
| objectFalse | Bool | Ложь |
| arrayTrue | Bool | Истина |
| arrayFalse | Bool | Ложь |

Развернуть этот пример шаблона с помощью Azure CLI можно так:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/contains.json
```

Развернуть этот пример шаблона с помощью PowerShell можно так:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/contains.json
```

<a id="datauri" />

## <a name="datauri"></a>dataUri
`dataUri(stringToConvert)`

Преобразует значение в универсальный код ресурса (URI) данных.

### <a name="parameters"></a>Параметры

| Параметр | Обязательно | type | ОПИСАНИЕ |
|:--- |:--- |:--- |:--- |
| stringToConvert |Yes |строка |Значение, которое необходимо преобразовать в URI данных. |

### <a name="return-value"></a>Возвращаемое значение

Строка в формате URI данных.

### <a name="examples"></a>Примеры

В следующем [примере шаблона](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/datauri.json) значение преобразуется в универсальный код ресурса (URI) данных, а URI данных преобразуется в строку.

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

Выходные данные из предыдущего примера со значениями по умолчанию:

| ИМЯ | type | Значение |
| ---- | ---- | ----- |
| dataUriOutput | Строка | data:text/plain;charset=utf8;base64,SGVsbG8= |
| toStringOutput | Строка | Привет, мир! |

Развернуть этот пример шаблона с помощью Azure CLI можно так:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/datauri.json
```

Развернуть этот пример шаблона с помощью PowerShell можно так:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/datauri.json
```

<a id="datauritostring" />

## <a name="datauritostring"></a>dataUriToString
`dataUriToString(dataUriToConvert)`

Преобразует значение в формате URI данных в строку.

### <a name="parameters"></a>Параметры

| Параметр | Обязательно | type | ОПИСАНИЕ |
|:--- |:--- |:--- |:--- |
| dataUriToConvert |Yes |строка |Значение URI данных, которое необходимо преобразовать. |

### <a name="return-value"></a>Возвращаемое значение

Строка, содержащая преобразованное значение.

### <a name="examples"></a>Примеры

В следующем [примере шаблона](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/datauri.json) значение преобразуется в универсальный код ресурса (URI) данных, а URI данных преобразуется в строку.

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

Выходные данные из предыдущего примера со значениями по умолчанию:

| ИМЯ | type | Значение |
| ---- | ---- | ----- |
| dataUriOutput | Строка | data:text/plain;charset=utf8;base64,SGVsbG8= |
| toStringOutput | Строка | Привет, мир! |

Развернуть этот пример шаблона с помощью Azure CLI можно так:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/datauri.json
```

Развернуть этот пример шаблона с помощью PowerShell можно так:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/datauri.json
```

<a id="empty" /> 

## <a name="empty"></a>empty
`empty(itemToTest)`

Определяет, являются ли пустыми массив, объект или строка.

### <a name="parameters"></a>Параметры

| Параметр | Обязательно | type | ОПИСАНИЕ |
|:--- |:--- |:--- |:--- |
| itemToTest |Yes |массив, объект или строка |Значение, которое необходимо проверить на наличие содержимого. |

### <a name="return-value"></a>Возвращаемое значение

Возвращает результат **True**, если значение пустое. В противном случае — **False**.

### <a name="examples"></a>Примеры

В следующем [примере шаблона](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/empty.json) проверяется, являются ли пустыми массив, объект и строка.

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

Выходные данные из предыдущего примера со значениями по умолчанию:

| ИМЯ | type | Значение |
| ---- | ---- | ----- |
| arrayEmpty | Bool | Истина |
| objectEmpty | Bool | Истина |
| stringEmpty | Bool | Истина |

Развернуть этот пример шаблона с помощью Azure CLI можно так:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/empty.json
```

Развернуть этот пример шаблона с помощью PowerShell можно так:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/empty.json
```

<a id="endswith" />

## <a name="endswith"></a>endsWith
`endsWith(stringToSearch, stringToFind)`

Определяет, заканчивается ли строка определенным значением. При сравнении регистр не учитывается.

### <a name="parameters"></a>Параметры

| Параметр | Обязательно | type | ОПИСАНИЕ |
|:--- |:--- |:--- |:--- |
| stringToSearch |Yes |строка |Значение, содержащее элемент, который необходимо найти. |
| stringToFind |Yes |строка |Значение, которое необходимо найти. |

### <a name="return-value"></a>Возвращаемое значение

**True**, если последний знак или знаки строки соответствуют значению. В противном случае — **False**.

### <a name="examples"></a>Примеры

В следующем [примере шаблона](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/startsendswith.json) показано, как использовать функции startsWith и endsWith.

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

Выходные данные из предыдущего примера со значениями по умолчанию:

| ИМЯ | type | Значение |
| ---- | ---- | ----- |
| startsTrue | Bool | Истина |
| startsCapTrue | Bool | Да |
| startsFalse | Bool | Ложь |
| endsTrue | Bool | Истина |
| endsCapTrue | Bool | Истина |
| endsFalse | Bool | Ложь |

Развернуть этот пример шаблона с помощью Azure CLI можно так:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/startsendswith.json
```

Развернуть этот пример шаблона с помощью PowerShell можно так:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/startsendswith.json
```

<a id="first" />

## <a name="first"></a>first
`first(arg1)`

Возвращает первый знак строки или первый элемент массива.

### <a name="parameters"></a>Параметры

| Параметр | Обязательно | type | ОПИСАНИЕ |
|:--- |:--- |:--- |:--- |
| arg1 |Yes |массив или строка |Значение, из которого необходимо извлечь первый элемент или знак. |

### <a name="return-value"></a>Возвращаемое значение

Строка первого знака или тип (строка, целое число, массив или объект) первого элемента в массиве.

### <a name="examples"></a>Примеры

В следующем [примере шаблона](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/first.json) показано, как использовать функцию first с массивом и строкой.

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

Выходные данные из предыдущего примера со значениями по умолчанию:

| ИМЯ | type | Значение |
| ---- | ---- | ----- |
| arrayOutput | Строка | one |
| stringOutput | Строка | O |

Развернуть этот пример шаблона с помощью Azure CLI можно так:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/first.json
```

Развернуть этот пример шаблона с помощью PowerShell можно так:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/first.json
```

## <a name="guid"></a>GUID

`guid (baseString, ...)`

Создает значение в формате глобального уникального идентификатора на основе значений, указанных как параметры.

### <a name="parameters"></a>Параметры

| Параметр | Обязательно | type | ОПИСАНИЕ |
|:--- |:--- |:--- |:--- |
| baseString |Yes |строка |Значение, используемое в хэш-функции для создания GUID. |
| Дополнительные параметры (если необходимы) |Нет  |строка |Можно добавить столько строк, сколько необходимо для создания значения, которое задает уровень уникальности. |

### <a name="remarks"></a>Примечания

Эта функция полезна, если нужно создать значение в формате глобального уникального идентификатора. Указываются значения параметров, которые ограничивают область уникальности результата. Можно указать, является ли уникальным имя в подписке, группе ресурсов или развертывании.

Возвращаемое значение — не произвольная строка, а, скорее, результат хэш-функции. Возвращаемое значение содержит 36 символов. Оно не является глобально уникальным.

В следующих примерах показывается, как использовать GUID при создании уникального значения для часто используемых уровней.

Уникальное в пределах подписки.

```json
"[guid(subscription().subscriptionId)]"
```

Уникальное в пределах группы ресурсов.

```json
"[guid(resourceGroup().id)]"
```

Уникальное в пределах развертывания для группы ресурсов.

```json
"[guid(resourceGroup().id, deployment().name)]"
```

### <a name="return-value"></a>Возвращаемое значение

Строка, содержащая 36 символов, в формате глобального уникального идентификатора.

### <a name="examples"></a>Примеры

В следующем [примере шаблона](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/guid.json) возвращаются результаты выполнения GUID.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "variables": {},
    "resources": [],
    "outputs": {
        "guidPerSubscription": {
            "value": "[guid(subscription().subscriptionId)]",
            "type": "string"
        },
        "guidPerResourceGroup": {
            "value": "[guid(resourceGroup().id)]",
            "type": "string"
        },
        "guidPerDeployment": {
            "value": "[guid(resourceGroup().id, deployment().name)]",
            "type": "string"
        }
    }
}
```

Развернуть этот пример шаблона с помощью Azure CLI можно так:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/guid.json
```

Развернуть этот пример шаблона с помощью PowerShell можно так:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/guid.json
```

<a id="indexof" />

## <a name="indexof"></a>indexOf
`indexOf(stringToSearch, stringToFind)`

Возвращает первую позицию значения в строке. При сравнении регистр не учитывается.

### <a name="parameters"></a>Параметры

| Параметр | Обязательно | type | ОПИСАНИЕ |
|:--- |:--- |:--- |:--- |
| stringToSearch |Yes |строка |Значение, содержащее элемент, который необходимо найти. |
| stringToFind |Yes |строка |Значение, которое необходимо найти. |

### <a name="return-value"></a>Возвращаемое значение

Целое число, представляющее позицию искомого элемента. Значение отсчитывается, начиная с нуля. Если элемент не найден, то возвращается значение -1.

### <a name="examples"></a>Примеры

В следующем [примере шаблона](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/indexof.json) показано, как использовать функции indexOf и lastIndexOf.

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

Выходные данные из предыдущего примера со значениями по умолчанию:

| ИМЯ | type | Значение |
| ---- | ---- | ----- |
| firstT | int | 0 |
| lastT | int | 3 |
| firstString | int | 2 |
| lastString | int | 0 |
| notFound | int | -1 |

Развернуть этот пример шаблона с помощью Azure CLI можно так:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/indexof.json
```

Развернуть этот пример шаблона с помощью PowerShell можно так:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/indexof.json
```

<a id="last" />

## <a name="last"></a>last
`last (arg1)`

Возвращает последний знак строки или последний элемент массива.

### <a name="parameters"></a>Параметры

| Параметр | Обязательно | type | ОПИСАНИЕ |
|:--- |:--- |:--- |:--- |
| arg1 |Yes |массив или строка |Значение, из которого необходимо извлечь последний элемент или знак. |

### <a name="return-value"></a>Возвращаемое значение

Строка последнего знака или тип (строка, целое число, массив или объект) последнего элемента в массиве.

### <a name="examples"></a>Примеры

В следующем [примере шаблона](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/last.json) показано, как использовать функцию last с массивом и строкой.

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

Выходные данные из предыдущего примера со значениями по умолчанию:

| ИМЯ | type | Значение |
| ---- | ---- | ----- |
| arrayOutput | Строка | three |
| stringOutput | Строка | e |

Развернуть этот пример шаблона с помощью Azure CLI можно так:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/last.json
```

Развернуть этот пример шаблона с помощью PowerShell можно так:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/last.json
```

<a id="lastindexof" />

## <a name="lastindexof"></a>lastIndexOf
`lastIndexOf(stringToSearch, stringToFind)`

Возвращает последнюю позицию значения в строке. При сравнении регистр не учитывается.

### <a name="parameters"></a>Параметры

| Параметр | Обязательно | type | ОПИСАНИЕ |
|:--- |:--- |:--- |:--- |
| stringToSearch |Yes |строка |Значение, содержащее элемент, который необходимо найти. |
| stringToFind |Yes |строка |Значение, которое необходимо найти. |

### <a name="return-value"></a>Возвращаемое значение

Целое число, представляющее последнюю позицию искомого элемента. Значение отсчитывается, начиная с нуля. Если элемент не найден, то возвращается значение -1.

### <a name="examples"></a>Примеры

В следующем [примере шаблона](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/indexof.json) показано, как использовать функции indexOf и lastIndexOf.

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

Выходные данные из предыдущего примера со значениями по умолчанию:

| ИМЯ | type | Значение |
| ---- | ---- | ----- |
| firstT | int | 0 |
| lastT | int | 3 |
| firstString | int | 2 |
| lastString | int | 0 |
| notFound | int | -1 |

Развернуть этот пример шаблона с помощью Azure CLI можно так:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/indexof.json
```

Развернуть этот пример шаблона с помощью PowerShell можно так:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/indexof.json
```

<a id="length" />

## <a name="length"></a>длина
`length(string)`

Возвращает число знаков в строке или элементов в массиве.

### <a name="parameters"></a>Параметры

| Параметр | Обязательно | type | ОПИСАНИЕ |
|:--- |:--- |:--- |:--- |
| arg1 |Yes |массив или строка |Массив, который необходимо использовать для получения числа элементов, или строка — для получения числа знаков. |

### <a name="return-value"></a>Возвращаемое значение

Целое число. 

### <a name="examples"></a>Примеры

В следующем [примере шаблона](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/length.json) показано, как использовать функцию length с массивом и строкой.

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

Выходные данные из предыдущего примера со значениями по умолчанию:

| ИМЯ | type | Значение |
| ---- | ---- | ----- |
| arrayLength | int | 3 |
| stringLength | int | 13 |

Развернуть этот пример шаблона с помощью Azure CLI можно так:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/length.json
```

Развернуть этот пример шаблона с помощью PowerShell можно так:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/length.json
```

<a id="padleft" />

## <a name="padleft"></a>padLeft
`padLeft(valueToPad, totalLength, paddingCharacter)`

Возвращает выровненную по правому краю строку, добавляя символы в левую часть до достижения общее указанной длины.

### <a name="parameters"></a>Параметры

| Параметр | Обязательно | type | ОПИСАНИЕ |
|:--- |:--- |:--- |:--- |
| значение_для_заполнения  |Yes |строка или целое число |Значение, выравниваемое по правому краю. |
| общая_длина |Yes |int |Общее число символов в возвращаемой строке. |
| символ_заполнения |Нет  |один знак |Символ, используемый для заполнения левой части до достижения общей длины. Значение по умолчанию — пробел. |

Если длина исходной строки превышает число знаков для заполнения, то знаки не добавляются.

### <a name="return-value"></a>Возвращаемое значение

Строка, содержащая по крайней мере число указанных знаков.

### <a name="examples"></a>Примеры

В следующем [примере шаблона](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/padleft.json) показано, как заполнить указанное пользователем значение параметра, добавляя знак нуля до достижения общего числа знаков. 

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "testString": {
            "type": "string",
            "defaultValue": "123"
        }
    },
    "resources": [],
    "outputs": {
        "stringOutput": {
            "type": "string",
            "value": "[padLeft(parameters('testString'),10,'0')]"
        }
    }
}
```

Выходные данные из предыдущего примера со значениями по умолчанию:

| ИМЯ | type | Значение |
| ---- | ---- | ----- |
| stringOutput | Строка | 0000000123 |

Развернуть этот пример шаблона с помощью Azure CLI можно так:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/padleft.json
```

Развернуть этот пример шаблона с помощью PowerShell можно так:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/padleft.json
```

<a id="replace" />

## <a name="replace"></a>replace
`replace(originalString, oldString, newString)`

Возвращает новую строку, в которой все экземпляры одной строки заменены другой строкой.

### <a name="parameters"></a>Параметры

| Параметр | Обязательно | type | ОПИСАНИЕ |
|:--- |:--- |:--- |:--- |
| исходная_строка |Yes |строка |Значение, в котором все экземпляры одной строки заменены другой строкой. |
| oldString |Yes |строка |Строка, которая удаляется из исходной строки. |
| newString |Yes |строка |Строка, добавляемая вместо удаляемой строки. |

### <a name="return-value"></a>Возвращаемое значение

Строка с замененными знаками.

### <a name="examples"></a>Примеры

В приведенном ниже [примере шаблона](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/replace.json) показано, как удалить все тире из предоставленной пользователем строки и как заменить часть строки другой строкой.

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
        "firstOutput": {
            "type": "string",
            "value": "[replace(parameters('testString'),'-', '')]"
        },
        "secodeOutput": {
            "type": "string",
            "value": "[replace(parameters('testString'),'1234', 'xxxx')]"
        }
    }
}
```

Выходные данные из предыдущего примера со значениями по умолчанию:

| ИМЯ | type | Значение |
| ---- | ---- | ----- |
| firstOutput | Строка | 1231231234 |
| secodeOutput | Строка | 123-123-xxxx |

Развернуть этот пример шаблона с помощью Azure CLI можно так:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/replace.json
```

Развернуть этот пример шаблона с помощью PowerShell можно так:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/replace.json
```

<a id="skip" />

## <a name="skip"></a>skip
`skip(originalValue, numberToSkip)`

Возвращает строку, содержащую все знаки из исходной строки, начиная с заданной позиции, или массив, содержащий все элементы из исходного массива, начиная с заданной позиции.

### <a name="parameters"></a>Параметры

| Параметр | Обязательно | type | ОПИСАНИЕ |
|:--- |:--- |:--- |:--- |
| originalValue |Yes |массив или строка |Массив или строка, используемые для пропуска. |
| numberToSkip |Yes |int |Число элементов или знаков, которые необходимо пропустить. Если это значение меньше или равно 0, то возвращаются все элементы или знаки в значении. Если это значение превышает длину массива или строки, то возвращается пустой массив или пустая строка. |

### <a name="return-value"></a>Возвращаемое значение

Массив или строка.

### <a name="examples"></a>Примеры

В следующем [примере шаблона](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/skip.json) пропускается заданное число элементов в массиве и заданное число знаков в строке.

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

Выходные данные из предыдущего примера со значениями по умолчанию:

| ИМЯ | type | Значение |
| ---- | ---- | ----- |
| arrayOutput | Массив, | ["three"] |
| stringOutput | Строка | two three |

Развернуть этот пример шаблона с помощью Azure CLI можно так:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/skip.json
```

Развернуть этот пример шаблона с помощью PowerShell можно так:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/skip.json
```

<a id="split" />

## <a name="split"></a>split
`split(inputString, delimiter)`

Возвращает массив строк, содержащий подстроки входной строки, разделенные переданными разделителями.

### <a name="parameters"></a>Параметры

| Параметр | Обязательно | type | ОПИСАНИЕ |
|:--- |:--- |:--- |:--- |
| входная_строка |Yes |строка |Строка для разделения. |
| delimiter |Yes |строка или массив строк |Разделитель для разбиения строки. |

### <a name="return-value"></a>Возвращаемое значение

Массив строк.

### <a name="examples"></a>Примеры

В следующем [примере шаблона](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/split.json) входная строка разбивается с помощью запятой или точки с запятой.

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

Выходные данные из предыдущего примера со значениями по умолчанию:

| ИМЯ | type | Значение |
| ---- | ---- | ----- |
| firstOutput | Массив, | ["one", "two", "three"] |
| secondOutput | Массив, | ["one", "two", "three"] |

Развернуть этот пример шаблона с помощью Azure CLI можно так:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/split.json
```

Развернуть этот пример шаблона с помощью PowerShell можно так:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/split.json
```

<a id="startswith" />

## <a name="startswith"></a>startsWith
`startsWith(stringToSearch, stringToFind)`

Определяет, начинается ли строка с определенного значения. При сравнении регистр не учитывается.

### <a name="parameters"></a>Параметры

| Параметр | Обязательно | type | ОПИСАНИЕ |
|:--- |:--- |:--- |:--- |
| stringToSearch |Yes |строка |Значение, содержащее элемент, который необходимо найти. |
| stringToFind |Yes |строка |Значение, которое необходимо найти. |

### <a name="return-value"></a>Возвращаемое значение

**True**, если первый знак или знаки строки соответствуют значению. В противном случае — **False**.

### <a name="examples"></a>Примеры

В следующем [примере шаблона](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/startsendswith.json) показано, как использовать функции startsWith и endsWith.

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

Выходные данные из предыдущего примера со значениями по умолчанию:

| ИМЯ | type | Значение |
| ---- | ---- | ----- |
| startsTrue | Bool | Истина |
| startsCapTrue | Bool | Да |
| startsFalse | Bool | Ложь |
| endsTrue | Bool | Истина |
| endsCapTrue | Bool | Истина |
| endsFalse | Bool | Ложь |

Развернуть этот пример шаблона с помощью Azure CLI можно так:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/startsendswith.json
```

Развернуть этот пример шаблона с помощью PowerShell можно так:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/startsendswith.json
```

<a id="string" />

## <a name="string"></a>строка
`string(valueToConvert)`

Преобразует указанное значение в строку.

### <a name="parameters"></a>Параметры

| Параметр | Обязательно | type | ОПИСАНИЕ |
|:--- |:--- |:--- |:--- |
| valueToConvert |Yes | Любой |Значение, которое необходимо преобразовать в строку. Можно преобразовать любой тип значения, включая объекты и массивы. |

### <a name="return-value"></a>Возвращаемое значение

Строка преобразованного значения.

### <a name="examples"></a>Примеры

В следующем [примере шаблона](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/string.json) показано, как преобразовать различные типы значений в строки.

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

Выходные данные из предыдущего примера со значениями по умолчанию:

| ИМЯ | type | Значение |
| ---- | ---- | ----- |
| objectOutput | Строка | {"valueA":10,"valueB":"Example Text"} |
| arrayOutput | Строка | ["a","b","c"] |
| intOutput | Строка | 5 |

Развернуть этот пример шаблона с помощью Azure CLI можно так:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/string.json
```

Развернуть этот пример шаблона с помощью PowerShell можно так:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/string.json
```

<a id="substring" />

## <a name="substring"></a>substring
`substring(stringToParse, startIndex, length)`

Возвращает подстроку, которая начинается с указанной позиции и содержит указанное количество символов.

### <a name="parameters"></a>Параметры

| Параметр | Обязательно | type | ОПИСАНИЕ |
|:--- |:--- |:--- |:--- |
| stringToParse |Yes |строка |Исходная строка, из которой извлекается подстрока. |
| startIndex |Нет  |int |Отсчитываемая от нуля позиция первого знака для подстроки. |
| длина |Нет  |int |Число символов в подстроке. Этот параметр должен ссылаться на позицию в строке. |

### <a name="return-value"></a>Возвращаемое значение

Подстрока.

### <a name="remarks"></a>Примечания

Если substring выходит за пределы строки, происходит ошибка выполнения функции. Следующий пример завершается ошибкой "Параметры индекса и длины должны относиться к расположению в строке. Параметр индекса: 0, параметр длины: 11, параметр длины строкового параметра: 10".

```json
"parameters": {
    "inputString": { "type": "string", "value": "1234567890" }
},
"variables": { 
    "prefix": "[substring(parameters('inputString'), 0, 11)]"
}
```

### <a name="examples"></a>Примеры

В следующем [примере шаблона](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/substring.json) из параметра извлекается подстрока.

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

Выходные данные из предыдущего примера со значениями по умолчанию:

| ИМЯ | type | Значение |
| ---- | ---- | ----- |
| substringOutput | Строка | two |

Развернуть этот пример шаблона с помощью Azure CLI можно так:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/substring.json
```

Развернуть этот пример шаблона с помощью PowerShell можно так:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/substring.json
```

<a id="take" />

## <a name="take"></a>take
`take(originalValue, numberToTake)`

Возвращает строку, содержащую указанное число знаков, считая от начала строки, или массив, содержащий указанное число элементов, считая от начала массива.

### <a name="parameters"></a>Параметры

| Параметр | Обязательно | type | ОПИСАНИЕ |
|:--- |:--- |:--- |:--- |
| originalValue |Yes |массив или строка |Массив или строка, из которых берутся элементы. |
| numberToTake |Yes |int |Число элементов или знаков, которые необходимо взять. Если это значение меньше или равно 0, то возвращается пустой массив или строка. Если это значение превышает длину заданного массива или строки, то возвращаются все элементы в массиве или строке. |

### <a name="return-value"></a>Возвращаемое значение

Массив или строка.

### <a name="examples"></a>Примеры

В следующем [примере шаблона](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/take.json) из массива извлекается заданное число элементов, а из строки — заданное число знаков.

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

Выходные данные из предыдущего примера со значениями по умолчанию:

| ИМЯ | type | Значение |
| ---- | ---- | ----- |
| arrayOutput | Массив, | ["one", "two"] |
| stringOutput | Строка | on |

Развернуть этот пример шаблона с помощью Azure CLI можно так:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/take.json
```

Развернуть этот пример шаблона с помощью PowerShell можно так:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/take.json
```

<a id="tolower" />

## <a name="tolower"></a>toLower
`toLower(stringToChange)`

Преобразует указанную строку в нижний регистр.

### <a name="parameters"></a>Параметры

| Параметр | Обязательно | type | ОПИСАНИЕ |
|:--- |:--- |:--- |:--- |
| изменяемая_строка |Yes |строка |Значение, преобразовываемое в нижний регистр. |

### <a name="return-value"></a>Возвращаемое значение

Возвращает строку, преобразованную в нижний регистр.

### <a name="examples"></a>Примеры

В следующем [примере шаблона](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/tolower.json) значение параметра преобразуется в нижний и в верхний регистр.

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

Выходные данные из предыдущего примера со значениями по умолчанию:

| ИМЯ | type | Значение |
| ---- | ---- | ----- |
| toLowerOutput | Строка | one two three |
| toUpperOutput | Строка | ONE TWO THREE |

Развернуть этот пример шаблона с помощью Azure CLI можно так:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/tolower.json
```

Развернуть этот пример шаблона с помощью PowerShell можно так:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/tolower.json
```

<a id="toupper" />

## <a name="toupper"></a>toUpper
`toUpper(stringToChange)`

Преобразует указанную строку в верхний регистр.

### <a name="parameters"></a>Параметры

| Параметр | Обязательно | type | ОПИСАНИЕ |
|:--- |:--- |:--- |:--- |
| изменяемая_строка |Yes |строка |Значение, преобразовываемое в верхний регистр. |

### <a name="return-value"></a>Возвращаемое значение

Возвращает строку, преобразованную в верхний регистр.

### <a name="examples"></a>Примеры

В следующем [примере шаблона](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/tolower.json) значение параметра преобразуется в нижний и в верхний регистр.

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

Выходные данные из предыдущего примера со значениями по умолчанию:

| ИМЯ | type | Значение |
| ---- | ---- | ----- |
| toLowerOutput | Строка | one two three |
| toUpperOutput | Строка | ONE TWO THREE |

Развернуть этот пример шаблона с помощью Azure CLI можно так:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/tolower.json
```

Развернуть этот пример шаблона с помощью PowerShell можно так:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/tolower.json
```

<a id="trim" />

## <a name="trim"></a>trim
`trim (stringToTrim)`

Удаляет все начальные и конечные знаки пробела из указанной строки.

### <a name="parameters"></a>Параметры

| Параметр | Обязательно | type | ОПИСАНИЕ |
|:--- |:--- |:--- |:--- |
| stringToTrim |Yes |строка |Обрезаемое значение. |

### <a name="return-value"></a>Возвращаемое значение

Строка без пробелов в начале и в конце.

### <a name="examples"></a>Примеры

В следующем [примере шаблона](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/trim.json) из параметра удаляются пробелы.

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

Выходные данные из предыдущего примера со значениями по умолчанию:

| ИМЯ | type | Значение |
| ---- | ---- | ----- |
| return | Строка | one two three |

Развернуть этот пример шаблона с помощью Azure CLI можно так:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/trim.json
```

Развернуть этот пример шаблона с помощью PowerShell можно так:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/trim.json
```

<a id="uniquestring" />

## <a name="uniquestring"></a>uniqueString
`uniqueString (baseString, ...)`

Создает детерминированную хэш-строку на основании значений, указанных как параметры. 

### <a name="parameters"></a>Параметры

| Параметр | Обязательно | type | ОПИСАНИЕ |
|:--- |:--- |:--- |:--- |
| baseString |Yes |строка |Значение, используемое в хэш-функции для создания уникальной строки. |
| Дополнительные параметры (если необходимы) |Нет  |строка |Можно добавить столько строк, сколько необходимо для создания значения, которое задает уровень уникальности. |

### <a name="remarks"></a>Примечания

Эта функция полезна в тех случаях, когда необходимо создать уникальное имя ресурса. Указываются значения параметров, которые ограничивают область уникальности результата. Можно указать, является ли уникальным имя в подписке, группе ресурсов или развертывании. 

Возвращаемое значение — не произвольная строка, а, скорее, результат хэш-функции. Возвращаемое значение содержит 13 знаков. Оно не является глобально уникальным. Вы можете добавить к значению префикс из своего соглашения об именовании, чтобы создать значимое имя. В следующем примере показан формат возвращаемого значения. Фактическое значение зависит от указанных параметров.

    tcvhiyu5h2o5o

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

Строка, содержащая 13 символов.

### <a name="examples"></a>Примеры

В следующем [примере шаблона](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/uniquestring.json) возвращаются результаты выполнения uniquestring.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "uniqueRG": {
            "value": "[uniqueString(resourceGroup().id)]",
            "type" : "string"
        },
        "uniqueDeploy": {
            "value": "[uniqueString(resourceGroup().id, deployment().name)]",
            "type" : "string"
        }
    }
}
```

Развернуть этот пример шаблона с помощью Azure CLI можно так:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/uniquestring.json
```

Развернуть этот пример шаблона с помощью PowerShell можно так:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/uniquestring.json
```

<a id="uri" />

## <a name="uri"></a>uri
`uri (baseUri, relativeUri)`

Создает абсолютный URI, объединяя строки baseUri и relativeUri.

### <a name="parameters"></a>Параметры

| Параметр | Обязательно | type | ОПИСАНИЕ |
|:--- |:--- |:--- |:--- |
| baseUri |Yes |строка |Строка базового универсального кода ресурса (URI). |
| relativeUri |Yes |строка |Строка относительного универсального кода ресурса (URI), добавляемая к строке базового универсального кода ресурса (URI). |

Значение параметра **baseUri** может включать в себя определенный файл, однако при построении универсального кода ресурса (URI) используется только базовый путь. Например, если передать `http://contoso.com/resources/azuredeploy.json` в качестве параметра baseUri, то базовый универсальный код ресурса (URI) будет иметь значение `http://contoso.com/resources/`.

### <a name="return-value"></a>Возвращаемое значение

Строка, представляющая абсолютный URI для базового и относительного значений.

### <a name="examples"></a>Примеры

В следующем примере показано создание ссылки на вложенный шаблон в зависимости от значения параметра родительского шаблона.

```json
"templateLink": "[uri(deployment().properties.templateLink.uri, 'nested/azuredeploy.json')]"
```

В следующем [примере шаблона](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/uri.json) показано, как использовать функции uri, uriComponent и uriComponentToString.

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

Выходные данные из предыдущего примера со значениями по умолчанию:

| ИМЯ | type | Значение |
| ---- | ---- | ----- |
| uriOutput | Строка | http://contoso.com/resources/nested/azuredeploy.json |
| componentOutput | Строка | http%3A%2F%2Fcontoso.com%2Fresources%2Fnested%2Fazuredeploy.json |
| toStringOutput | Строка | http://contoso.com/resources/nested/azuredeploy.json |

Развернуть этот пример шаблона с помощью Azure CLI можно так:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/uri.json
```

Развернуть этот пример шаблона с помощью PowerShell можно так:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/uri.json
```

<a id="uricomponent" />

## <a name="uricomponent"></a>uriComponent
`uricomponent(stringToEncode)`

Кодирует URI.

### <a name="parameters"></a>Параметры

| Параметр | Обязательно | type | ОПИСАНИЕ |
|:--- |:--- |:--- |:--- |
| stringToEncode |Yes |строка |Значение для кодирования. |

### <a name="return-value"></a>Возвращаемое значение

Строка со значением, закодированным в формате URI.

### <a name="examples"></a>Примеры

В следующем [примере шаблона](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/uri.json) показано, как использовать функции uri, uriComponent и uriComponentToString.

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

Выходные данные из предыдущего примера со значениями по умолчанию:

| ИМЯ | type | Значение |
| ---- | ---- | ----- |
| uriOutput | Строка | http://contoso.com/resources/nested/azuredeploy.json |
| componentOutput | Строка | http%3A%2F%2Fcontoso.com%2Fresources%2Fnested%2Fazuredeploy.json |
| toStringOutput | Строка | http://contoso.com/resources/nested/azuredeploy.json |

Развернуть этот пример шаблона с помощью Azure CLI можно так:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/uri.json
```

Развернуть этот пример шаблона с помощью PowerShell можно так:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/uri.json
```

<a id="uricomponenttostring" />

## <a name="uricomponenttostring"></a>uriComponentToString
`uriComponentToString(uriEncodedString)`

Возвращает строку со значением, закодированным в формате URI.

### <a name="parameters"></a>Параметры

| Параметр | Обязательно | type | ОПИСАНИЕ |
|:--- |:--- |:--- |:--- |
| uriEncodedString |Yes |строка |Значение, закодированное в формате URI, которое необходимо преобразовать в строку. |

### <a name="return-value"></a>Возвращаемое значение

Декодированная строка со значением, закодированным в формате URI.

### <a name="examples"></a>Примеры

В следующем [примере шаблона](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/uri.json) показано, как использовать функции uri, uriComponent и uriComponentToString.

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

Выходные данные из предыдущего примера со значениями по умолчанию:

| ИМЯ | type | Значение |
| ---- | ---- | ----- |
| uriOutput | Строка | http://contoso.com/resources/nested/azuredeploy.json |
| componentOutput | Строка | http%3A%2F%2Fcontoso.com%2Fresources%2Fnested%2Fazuredeploy.json |
| toStringOutput | Строка | http://contoso.com/resources/nested/azuredeploy.json |

Развернуть этот пример шаблона с помощью Azure CLI можно так:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/uri.json
```

Развернуть этот пример шаблона с помощью PowerShell можно так:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/uri.json
```

## <a name="next-steps"></a>Дополнительная информация
* Описание разделов в шаблоне Azure Resource Manager см. в статье [Создание шаблонов Azure Resource Manager](resource-group-authoring-templates.md).
* Инструкции по объединению нескольких шаблонов см. в статье [Использование связанных шаблонов в Azure Resource Manager](resource-group-linked-templates.md).
* Указания по выполнению заданного количества циклов итерации при создании типа ресурса см. в статье [Создание нескольких экземпляров ресурсов в Azure Resource Manager](resource-group-create-multiple.md).
* Указания по развертыванию созданного шаблона см. в статье, посвященной [развертыванию приложения с помощью шаблона Azure Resource Manager](resource-group-template-deploy.md).


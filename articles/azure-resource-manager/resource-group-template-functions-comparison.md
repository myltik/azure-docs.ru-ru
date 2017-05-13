---
title: "Функции сравнения в шаблонах Azure Resource Manager | Документация Майкрософт"
description: "Описывает функции, используемые в шаблоне Azure Resource Manager для сравнения значений."
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
ms.openlocfilehash: 7f19efa7e09b0dce43851019f94285b2887c46d5
ms.contentlocale: ru-ru
ms.lasthandoff: 04/28/2017


---
# <a name="comparison-functions-for-azure-resource-manager-templates"></a>Функции сравнения для шаблонов Azure Resource Manager

Resource Manager предоставляет ряд функций для выполнения сравнений в шаблонах.

* [equals](#equals)
* [less](#less)
* [lessOrEquals](#lessorequals)
* [greater](#greater)
* [greaterOrEquals](#greaterorequals)

<a id="equals" />

## <a name="equals"></a>equals (равно)
`equals(arg1, arg2)`

Проверяет, являются ли два значения равными.

### <a name="parameters"></a>Параметры

| Параметр | Обязательно | Тип | Описание |
|:--- |:--- |:--- |:--- |
| arg1 |Да |целое число, строка, массив или объект |Первое значение, которое необходимо проверить на равенство. |
| arg2 |Да |целое число, строка, массив или объект |Второе значение, которое необходимо проверить на равенство. |

### <a name="examples"></a>Примеры

Пример шаблона проверяет различные типы значений на равенство. Все значения по умолчанию возвращают результат True.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "firstInt": {
            "type": "int",
            "defaultValue": 1
        },
        "secondInt": {
            "type": "int",
            "defaultValue": 1
        },
        "firstString": {
            "type": "string",
            "defaultValue": "a"
        },
        "secondString": {
            "type": "string",
            "defaultValue": "a"
        },
        "firstArray": {
            "type": "array",
            "defaultValue": ["a", "b"]
        },
        "secondArray": {
            "type": "array",
            "defaultValue": ["a", "b"]
        },
        "firstObject": {
            "type": "object",
            "defaultValue": {"a": "b"}
        },
        "secondObject": {
            "type": "object",
            "defaultValue": {"a": "b"}
        }
    },
    "resources": [
    ],
    "outputs": {
        "checkInts": {
            "type": "bool",
            "value": "[equals(parameters('firstInt'), parameters('secondInt') )]"
        },
        "checkStrings": {
            "type": "bool",
            "value": "[equals(parameters('firstString'), parameters('secondString'))]"
        },
        "checkArrays": {
            "type": "bool",
            "value": "[equals(parameters('firstArray'), parameters('secondArray'))]"
        },
        "checkObjects": {
            "type": "bool",
            "value": "[equals(parameters('firstObject'), parameters('secondObject'))]"
        }
    }
}
```

### <a name="return-value"></a>Возвращаемое значение

Возвращает результат **True**, если значения равны. В противном случае — **False**.

<a id="less" />

## <a name="less"></a>less
`less(arg1, arg2)`

Проверяет, является ли первое значение меньшим, чем второе.

### <a name="parameters"></a>Параметры

| Параметр | Обязательно | Тип | Описание |
|:--- |:--- |:--- |:--- |
| arg1 |Да |целое число или строка |Первое значение для сравнения (является ли меньшим). |
| arg2 |Да |целое число или строка |Второе значение для сравнения (является ли меньшим). |

### <a name="examples"></a>Примеры

Пример шаблона проверяет, является ли первое значение меньшим, чем второе.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "firstInt": {
            "type": "int"
        },
        "secondInt": {
            "type": "int"
        },
        "firstString": {
            "type": "string"
        },
        "secondString": {
            "type": "string"
        }
    },
    "resources": [
    ],
    "outputs": {
        "checkInts": {
            "type": "bool",
            "value": "[less(parameters('firstInt'), parameters('secondInt') )]"
        },
        "checkStrings": {
            "type": "bool",
            "value": "[less(parameters('firstString'), parameters('secondString'))]"
        }
    }
}
```

### <a name="return-value"></a>Возвращаемое значение

Возвращает результат **True**, если первое значение меньше второго. В противном случае — **False**.

<a id="lessorequals" />

## <a name="lessorequals"></a>lessOrEquals
`lessOrEquals(arg1, arg2)`

Проверяет, является ли первое значение меньшим, чем второе, или равным ему.

### <a name="parameters"></a>Параметры

| Параметр | Обязательно | Тип | Описание |
|:--- |:--- |:--- |:--- |
| arg1 |Да |целое число или строка |Первое значение для сравнения (является ли меньшим или равным). |
| arg2 |Да |целое число или строка |Второе значение для сравнения (является ли меньшим или равным). |

### <a name="examples"></a>Примеры

Пример шаблона проверяет, является ли первое значение меньшим, чем второе, или равным ему.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "firstInt": {
            "type": "int"
        },
        "secondInt": {
            "type": "int"
        },
        "firstString": {
            "type": "string"
        },
        "secondString": {
            "type": "string"
        }
    },
    "resources": [
    ],
    "outputs": {
        "checkInts": {
            "type": "bool",
            "value": "[lessOrEquals(parameters('firstInt'), parameters('secondInt') )]"
        },
        "checkStrings": {
            "type": "bool",
            "value": "[lessOrEquals(parameters('firstString'), parameters('secondString'))]"
        }
    }
}
```

### <a name="return-value"></a>Возвращаемое значение

Возвращает результат **True**, если первое значение меньше или равно второму. В противном случае — **False**.

<a id="greater" />

## <a name="greater"></a>greater
`greater(arg1, arg2)`

Проверяет, является ли первое значение большим, чем второе.

### <a name="parameters"></a>Параметры

| Параметр | Обязательно | Тип | Описание |
|:--- |:--- |:--- |:--- |
| arg1 |Да |целое число или строка |Первое значение для сравнения (является ли большим). |
| arg2 |Да |целое число или строка |Второе значение для сравнения (является ли большим). |

### <a name="examples"></a>Примеры

Пример шаблона проверяет, является ли первое значение большим, чем второе.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "firstInt": {
            "type": "int"
        },
        "secondInt": {
            "type": "int"
        },
        "firstString": {
            "type": "string"
        },
        "secondString": {
            "type": "string"
        }
    },
    "resources": [
    ],
    "outputs": {
        "checkInts": {
            "type": "bool",
            "value": "[greater(parameters('firstInt'), parameters('secondInt') )]"
        },
        "checkStrings": {
            "type": "bool",
            "value": "[greater(parameters('firstString'), parameters('secondString'))]"
        }
    }
}
```

### <a name="return-value"></a>Возвращаемое значение

Возвращает результат **True**, если первое значение больше второго. В противном случае — **False**.

<a id="greaterorequals" />

## <a name="greaterorequals"></a>greaterOrEquals
`greaterOrEquals(arg1, arg2)`

Проверяет, является ли первое значение большим, чем второе, или равным ему.

### <a name="parameters"></a>Параметры

| Параметр | Обязательно | Тип | Описание |
|:--- |:--- |:--- |:--- |
| arg1 |Да |целое число или строка |Первое значение для сравнения (является ли большим или равным). |
| arg2 |Да |целое число или строка |Второе значение для сравнения (является ли большим или равным). |

### <a name="examples"></a>Примеры

Пример шаблона проверяет, является ли первое значение большим, чем второе, или равным ему.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "firstInt": {
            "type": "int"
        },
        "secondInt": {
            "type": "int"
        },
        "firstString": {
            "type": "string"
        },
        "secondString": {
            "type": "string"
        }
    },
    "resources": [
    ],
    "outputs": {
        "checkInts": {
            "type": "bool",
            "value": "[greaterOrEquals(parameters('firstInt'), parameters('secondInt') )]"
        },
        "checkStrings": {
            "type": "bool",
            "value": "[greaterOrEquals(parameters('firstString'), parameters('secondString'))]"
        }
    }
}
```

### <a name="return-value"></a>Возвращаемое значение

Возвращает результат **True**, если первое значение больше или равно второму. В противном случае — **False**.

## <a name="next-steps"></a>Дальнейшие действия
* Описание разделов в шаблоне Azure Resource Manager см. в статье [Создание шаблонов Azure Resource Manager](resource-group-authoring-templates.md).
* Инструкции по объединению нескольких шаблонов см. в статье [Использование связанных шаблонов в Azure Resource Manager](resource-group-linked-templates.md).
* Указания по выполнению заданного количества циклов итерации при создании типа ресурса см. в статье [Создание нескольких экземпляров ресурсов в Azure Resource Manager](resource-group-create-multiple.md).
* Указания по развертыванию созданного шаблона см. в статье, посвященной [развертыванию приложения с помощью шаблона Azure Resource Manager](resource-group-template-deploy.md).



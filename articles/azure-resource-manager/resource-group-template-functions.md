---
title: "Функции шаблона Resource Manager | Документация Майкрософт"
description: "Описывает функции, используемые в шаблоне диспетчера ресурсов Azure для извлечения значений, работы со строками и числовыми значениями и получения сведений о развертывании."
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 0644abe1-abaa-443d-820d-1966d7d26bfd
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/22/2016
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: 33e6b9ba880f56d967b49d0b89e61d1b531e8376
ms.openlocfilehash: 1d8cb6894399a7863392a7f11bde69d75d4685c3
ms.lasthandoff: 02/16/2017


---
# <a name="azure-resource-manager-template-functions"></a>Функции шаблонов диспетчера ресурсов Azure
В этом разделе описаны все функции, которые можно использовать в шаблоне Azure Resource Manager.

Функции шаблонов и их параметры зависят от регистра. Например, Resource Manager одинаково преобразует **variables('var1')** и **VARIABLES('VAR1')**. При вычислении функция сохранит регистр, если его изменение не является ее явным предназначением (например, функции toUpper и toLower). Для некоторых типов ресурсов требования к регистру могут не зависеть от того, как происходит вычисление функций.

## <a name="numeric-functions"></a>Числовые функции
Диспетчер ресурсов предоставляет следующие функции для работы с целыми числами:

* [добавление](#add)
* [copyIndex](#copyindex)
* [div](#div)
* [int](#int)
* [mod (модуль)](#mod)
* [mul](#mul)
* [sub](#sub)

<a id="add" />

### <a name="add"></a>добавление
`add(operand1, operand2)`

Возвращает сумму двух указанных целочисленных значений.

| Параметр | Обязательно | Тип | Описание |
|:--- |:--- |:--- |:--- | 
|операнд1 |Да |Целое число  |Первое слагаемое. |
|операнд2 |Да |Целое число  |Второе слагаемое. |

В следующем примере суммируются два параметра.

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
    ...
    "outputs": {
      "addResult": {
        "type": "int",
        "value": "[add(parameters('first'), parameters('second'))]"
      }
    }

<a id="copyindex" />

### <a name="copyindex"></a>copyIndex
`copyIndex(offset)`

Возвращает индекс цикла итерации. 

| Параметр | Обязательно | Тип | Описание |
|:--- |:--- |:--- |:--- |
| offset |Нет |Целое число  |Число, добавляемое к отсчитываемому от нуля значению итерации. |

Эта функция всегда используется с объектом **copy**. Если значение **offset** не указано, возвращается текущее значение итерации. Значение итерации начинается с нуля. Полное описание использования **copyIndex** см. в статье [Создание нескольких экземпляров ресурсов в Azure Resource Manager](resource-group-create-multiple.md).

В следующем примере представлен цикл копирования, в котором значение индекса включается в имя. 

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


<a id="div" />

### <a name="div"></a>div
`div(operand1, operand2)`

Возвращает целую часть частного от деления двух указанных целочисленных значений.

| Параметр | Обязательно | Тип | Описание |
|:--- |:--- |:--- |:--- |
| операнд1 |Да |Целое число  |Делимое. |
| операнд2 |Да |Целое число  |Делитель. Не может иметь значение 0. |

В следующем примере один параметр делится на другой.

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
    ...
    "outputs": {
      "divResult": {
        "type": "int",
        "value": "[div(parameters('first'), parameters('second'))]"
      }
    }

<a id="int" />

### <a name="int"></a>int
`int(valueToConvert)`

Преобразует указанное значение в целое число.

| Параметр | Обязательно | Тип | Описание |
|:--- |:--- |:--- |:--- |
| valueToConvert |Да |Строка или целое число |Значение, которое необходимо преобразовать в целое число. |

В следующем примере указанное пользователем значение параметра преобразуется в целое число.

    "parameters": {
        "appId": { "type": "string" }
    },
    "variables": { 
        "intValue": "[int(parameters('appId'))]"
    }


<a id="mod" />

### <a name="mod"></a>mod (модуль)
`mod(operand1, operand2)`

Возвращает остаток от деления двух указанных целочисленных значений.

| Параметр | Обязательно | Тип | Описание |
|:--- |:--- |:--- |:--- |
| операнд1 |Да |Целое число  |Делимое. |
| операнд2 |Да |Целое число  |Делитель, не может быть равен 0. |

В следующем примере возвращается остаток от деления одного параметра на другой.

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
    ...
    "outputs": {
      "modResult": {
        "type": "int",
        "value": "[mod(parameters('first'), parameters('second'))]"
      }
    }

<a id="mul" />

### <a name="mul"></a>mul
`mul(operand1, operand2)`

Возвращает произведение двух указанных целочисленных значений.

| Параметр | Обязательно | Тип | Описание |
|:--- |:--- |:--- |:--- |
| операнд1 |Да |Целое число  |Первый множитель. |
| операнд2 |Да |Целое число  |Второй множитель. |

В следующем примере один параметр умножается на другой.

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
    ...
    "outputs": {
      "mulResult": {
        "type": "int",
        "value": "[mul(parameters('first'), parameters('second'))]"
      }
    }

<a id="sub" />

### <a name="sub"></a>sub
`sub(operand1, operand2)`

Возвращает разность двух указанных целочисленных значений.

| Параметр | Обязательно | Тип | Описание |
|:--- |:--- |:--- |:--- |
| операнд1 |Да |Целое число  |Уменьшаемое. |
| операнд2 |Да |Целое число  |Вычитаемое. |

В следующем примере один параметр вычитается из другого.

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
    ...
    "outputs": {
      "subResult": {
        "type": "int",
        "value": "[sub(parameters('first'), parameters('second'))]"
      }
    }

## <a name="string-functions"></a>Строковые функции
Диспетчер ресурсов предоставляет следующие функции для работы со строками:

* [base64](#base64)
* [concat](#concat)
* [длина](#lengthstring)
* [padLeft](#padleft)
* [replace](#replace)
* [skip](#skipstring)
* [split](#split)
* [string](#string)
* [substring](#substring)
* [take](#takestring)
* [toLower](#tolower)
* [toUpper](#toupper)
* [trim](#trim)
* [uniqueString](#uniquestring)
* [uri](#uri)

<a id="base64" />

### <a name="base64"></a>base64
`base64 (inputString)`

Возвращает входную строку в кодировке Base64.

| Параметр | Обязательно | Тип | Описание |
|:--- |:--- |:--- |:--- |
| входная_строка |Да |Строка |Значение, которое нужно вернуть в кодировке base64. |

В следующем примере показано, как использовать функцию base64.

    "variables": {
      "usernameAndPassword": "[concat(parameters('username'), ':', parameters('password'))]",
      "authorizationHeader": "[concat('Basic ', base64(variables('usernameAndPassword')))]"
    }

<a id="concat" />

### <a name="concat---string"></a>concat для строк
`concat (string1, string2, string3, ...)`

Объединяет несколько строковых значений и возвращает результирующую строку. 

| Параметр | Обязательно | Тип | Описание |
|:--- |:--- |:--- |:--- |
| строка1 |Да |Строка |Первое значение для сцепки. |
| дополнительные строки |Нет |Строка |Дополнительные значения в последовательном порядке для сцепки. |

Эта функция может принимать любое количество аргументов, а также строки или массивы параметров. Пример сцепки массивов см. в разделе [concat для массива](#concatarray).

В следующем примере показано, как объединить несколько строк для возврата объединенной строки.

    "outputs": {
        "siteUri": {
          "type": "string",
          "value": "[concat('http://', reference(resourceId('Microsoft.Web/sites', parameters('siteName'))).hostNames[0])]"
        }
    }


<a id="lengthstring" />

### <a name="length---string"></a>length для строки
`length(string)`

Возвращает число символов в строке.

| Параметр | Обязательно | Тип | Описание |
|:--- |:--- |:--- |:--- |
| string |Да |Строка |Значение, для которого вычисляется количество знаков. |

Пример вычисления длины для массива см. в разделе [length для массива](#length).

В следующем примере возвращается число символов в строке. 

    "parameters": {
        "appName": { "type": "string" }
    },
    "variables": { 
        "nameLength": "[length(parameters('appName'))]"
    }


<a id="padleft" />

### <a name="padleft"></a>padLeft
`padLeft(valueToPad, totalLength, paddingCharacter)`

Возвращает выровненную по правому краю строку, добавляя символы в левую часть до достижения общее указанной длины.

| Параметр | Обязательно | Тип | Описание |
|:--- |:--- |:--- |:--- |
| значение_для_заполнения  |Да |Строка или целое число |Значение, выравниваемое по правому краю. |
| общая_длина |Да |Целое число  |Общее число символов в возвращаемой строке. |
| символ_заполнения |Нет |Отдельный знак |Символ, используемый для заполнения левой части до достижения общей длины. Значение по умолчанию — пробел. |

В следующем примере показано, как заполнить предоставленное пользователем значение параметра, добавляя символ нуля, пока длина строки не достигнет 10 символов. Если исходное значение параметра длиннее 10 символов, символы не добавляются.

    "parameters": {
        "appName": { "type": "string" }
    },
    "variables": { 
        "paddedAppName": "[padLeft(parameters('appName'),10,'0')]"
    }

<a id="replace" />

### <a name="replace"></a>replace
`replace(originalString, oldCharacter, newCharacter)`

Возвращает новую строку, где все экземпляры одного символа заменены другим символом.

| Параметр | Обязательно | Тип | Описание |
|:--- |:--- |:--- |:--- |
| исходная_строка |Да |Строка |Значение, в котором все экземпляры одного знака будут заменены другим знаком. |
| старый_символ |Да |Строка |Символ, который удаляется из исходной строки. |
| новый_символ |Да |Строка |Символ, добавляемый вместо удаляемого символа. |

В следующем примере показано, как удалить все тире из предоставленной пользователем строки.

    "parameters": {
        "identifier": { "type": "string" }
    },
    "variables": { 
        "newidentifier": "[replace(parameters('identifier'),'-','')]"
    }

<a id="skipstring" />

### <a name="skip---string"></a>skip для строки
`skip(originalValue, numberToSkip)`

Возвращает строку, содержащую все символы из исходной строки, начиная с заданной позиции.

| Параметр | Обязательно | Тип | Описание |
|:--- |:--- |:--- |:--- |
| originalValue |Да |Строка |Строка, используемая для пропуска. |
| numberToSkip |Да |Целое число  |Число символов, которые нужно пропустить. Если это значение меньше или равно 0, возвращаются все символы в строке. Если это значение превышает длину строки, возвращается пустая строка. |

Пример использования см. в разделе [skip для массива](#skip).

В следующем примере пропускается заданное число символов в строке.

    "parameters": {
      "first": {
        "type": "string",
        "metadata": {
          "description": "Value to use for skipping"
        }
      },
      "second": {
        "type": "int",
        "metadata": {
          "description": "Number of characters to skip"
        }
      }
    },
    "resources": [
    ],
    "outputs": {
      "return": {
        "type": "string",
        "value": "[skip(parameters('first'),parameters('second'))]"
      }
    }


<a id="split" />

### <a name="split"></a>split
`split(inputString, delimiterString)`

`split(inputString, delimiterArray)`

Возвращает массив строк, содержащий подстроки входной строки, разделенные переданными разделителями.

| Параметр | Обязательно | Тип | Описание |
|:--- |:--- |:--- |:--- |
| входная_строка |Да |Строка |Строка для разделения. |
| delimiter |Да |Строка или массив строк. |Разделитель для разбиения строки. |

В следующем примере входная строка разделяется с помощью запятой.

    "parameters": {
        "inputString": { "type": "string" }
    },
    "variables": { 
        "stringPieces": "[split(parameters('inputString'), ',')]"
    }

В следующем примере входная строка разбивается с помощью запятой или точки с запятой.

    "variables": {
      "stringToSplit": "test1,test2;test3",
      "delimiters": [ ",", ";" ]
    },
    "resources": [ ],
    "outputs": {
      "exampleOutput": {
        "value": "[split(variables('stringToSplit'), variables('delimiters'))]",
        "type": "array"
      }
    }

<a id="string" />

### <a name="string"></a>string
`string(valueToConvert)`

Преобразует указанное значение в строку.

| Параметр | Обязательно | Тип | Описание |
|:--- |:--- |:--- |:--- |
| valueToConvert |Да | Любой |Значение, которое необходимо преобразовать в строку. Можно преобразовать любой тип значения, включая объекты и массивы. |

В следующем примере указанные пользователем значения параметра преобразуются в строку.

    "parameters": {
      "jsonObject": {
        "type": "object",
        "defaultValue": {
          "valueA": 10,
          "valueB": "Example Text"
        }
      },
      "jsonArray": {
        "type": "array",
        "defaultValue": [ "a", "b", "c" ]
      },
      "jsonInt": {
        "type": "int",
        "defaultValue": 5
      }
    },
    "variables": { 
      "objectString": "[string(parameters('jsonObject'))]",
      "arrayString": "[string(parameters('jsonArray'))]",
      "intString": "[string(parameters('jsonInt'))]"
    }

<a id="substring" />

### <a name="substring"></a>substring
`substring(stringToParse, startIndex, length)`

Возвращает подстроку, которая начинается с указанной позиции и содержит указанное количество символов.

| Параметр | Обязательно | Тип | Описание |
|:--- |:--- |:--- |:--- |
| stringToParse |Да |Строка |Исходная строка, из которой извлекается подстрока. |
| startIndex |Нет |Целое число  |Отсчитываемая от нуля позиция первого знака для подстроки. |
| длина |Нет |Целое число  |Число символов в подстроке. |

Следующий пример извлекает три первых символа из параметра.

    "parameters": {
        "inputString": { "type": "string" }
    },
    "variables": { 
        "prefix": "[substring(parameters('inputString'), 0, 3)]"
    }

<a id="takestring" />

### <a name="take---string"></a>take для строки
`take(originalValue, numberToTake)`

Возвращает часть строки с указанным числом символов, считая от начала строки.

| Параметр | Обязательно | Тип | Описание |
|:--- |:--- |:--- |:--- |
| originalValue |Да |Строка |Значение, из которого извлекаются знаки. |
| numberToTake |Да |Целое число  |Количество символов, которые нужно извлечь. Если это значение меньше или равно 0, возвращается пустая строка. Если это значение превышает длину заданной строки, возвращаются все символы в строке. |

Пример использования см. в разделе [take для массива](#take).

В следующем примере из строки извлекается заданное число символов.

    "parameters": {
      "first": {
        "type": "string",
        "metadata": {
          "description": "Value to use for taking"
        }
      },
      "second": {
        "type": "int",
        "metadata": {
          "description": "Number of characters to take"
        }
      }
    },
    "resources": [
    ],
    "outputs": {
      "return": {
        "type": "string",
        "value": "[take(parameters('first'), parameters('second'))]"
      }
    }

<a id="tolower" />

### <a name="tolower"></a>toLower
`toLower(stringToChange)`

Преобразует указанную строку в нижний регистр.

| Параметр | Обязательно | Тип | Описание |
|:--- |:--- |:--- |:--- |
| изменяемая_строка |Да |Строка |Значение, преобразовываемое в нижний регистр. |

В следующем примере предоставленное пользователем значение параметра преобразуется в нижний регистр.

    "parameters": {
        "appName": { "type": "string" }
    },
    "variables": { 
        "lowerCaseAppName": "[toLower(parameters('appName'))]"
    }

<a id="toupper" />

### <a name="toupper"></a>toUpper
`toUpper(stringToChange)`

Преобразует указанную строку в верхний регистр.

| Параметр | Обязательно | Тип | Описание |
|:--- |:--- |:--- |:--- |
| изменяемая_строка |Да |Строка |Значение, преобразовываемое в верхний регистр. |

В следующем примере предоставленное пользователем значение параметра преобразуется в верхний регистр.

    "parameters": {
        "appName": { "type": "string" }
    },
    "variables": { 
        "upperCaseAppName": "[toUpper(parameters('appName'))]"
    }

<a id="trim" />

### <a name="trim"></a>trim
`trim (stringToTrim)`

Удаляет все начальные и конечные знаки пробела из указанной строки.

| Параметр | Обязательно | Тип | Описание |
|:--- |:--- |:--- |:--- |
| stringToTrim |Да |Строка |Обрезаемое значение. |

Следующий пример удаляет пробелы из значения параметра, предоставленного пользователем.

    "parameters": {
        "appName": { "type": "string" }
    },
    "variables": { 
        "trimAppName": "[trim(parameters('appName'))]"
    }

<a id="uniquestring" />

### <a name="uniquestring"></a>uniqueString
`uniqueString (baseString, ...)`

Создает детерминированную хэш-строку на основании значений, указанных как параметры. 

| Параметр | Обязательно | Тип | Описание |
|:--- |:--- |:--- |:--- |
| baseString |Да |Строка |Значение, используемое в хэш-функции для создания уникальной строки. |
| Дополнительные параметры (если необходимы) |Нет |Строка |Можно добавить столько строк, сколько необходимо для создания значения, которое задает уровень уникальности. |

Эта функция полезна в тех случаях, когда необходимо создать уникальное имя ресурса. Указываются значения параметров, которые ограничивают область уникальности результата. Можно указать, является ли уникальным имя в подписке, группе ресурсов или развертывании. 

Возвращаемое значение — не произвольная строка, а, скорее, результат хэш-функции. Возвращаемое значение содержит 13 знаков. Оно не является глобально уникальным. Вы можете добавить к значению префикс из своего соглашения об именовании, чтобы создать значимое имя. В следующем примере показан формат возвращаемого значения. Фактическое значение зависит от указанных параметров.

    tcvhiyu5h2o5o

В следующих примерах показывается, как использовать uniqueString при создании уникального значения для часто используемых уровней.

Уникальное в пределах подписки.

    "[uniqueString(subscription().subscriptionId)]"

Уникальное в пределах группы ресурсов.

    "[uniqueString(resourceGroup().id)]"

Уникальное в пределах развертывания для группы ресурсов.

    "[uniqueString(resourceGroup().id, deployment().name)]"

В следующем примере показано, как создать уникальное имя учетной записи хранения на основе вашей группы ресурсов. Внутри группы ресурсов имена не будут уникальными, если создавать их таким способом.

    "resources": [{ 
        "name": "[concat('storage', uniqueString(resourceGroup().id))]", 
        "type": "Microsoft.Storage/storageAccounts", 
        ...



<a id="uri" />

### <a name="uri"></a>uri
`uri (baseUri, relativeUri)`

Создает абсолютный URI, объединяя строки baseUri и relativeUri.

| Параметр | Обязательно | Тип | Описание |
|:--- |:--- |:--- |:--- |
| baseUri |Да |Строка |Строка базового универсального кода ресурса (URI). |
| relativeUri |Да |Строка |Строка относительного универсального кода ресурса (URI), добавляемая к строке базового универсального кода ресурса (URI). |

Значение параметра **baseUri** может включать в себя определенный файл, однако при построении универсального кода ресурса (URI) используется только базовый путь. Например, если передать `http://contoso.com/resources/azuredeploy.json` в качестве параметра baseUri, то базовый универсальный код ресурса (URI) будет иметь значение `http://contoso.com/resources/`.

В следующем примере показано создание ссылки на вложенный шаблон в зависимости от значения параметра родительского шаблона.

    "templateLink": "[uri(deployment().properties.templateLink.uri, 'nested/azuredeploy.json')]"

## <a name="array-functions"></a>Функции массивов
Диспетчер ресурсов предоставляет ряд функций для работы с массивами значений.

* [concat](#concatarray)
* [длина](#length)
* [skip](#skip)
* [take](#take)

Чтобы получить массив строковых значений, разделенных определенным значением, используйте [split](#split).

<a id="concatarray" />

### <a name="concat---array"></a>сoncat для массива
`concat (array1, array2, array3, ...)`

Объединяет несколько массивов и возвращает результирующий массив. 

| Параметр | Обязательно | Тип | Описание |
|:--- |:--- |:--- |:--- |
| массив1 |Да |Массив, |Первый массив для сцепки. |
| Дополнительные массивы |Нет |Массив, |Дополнительные массивы в последовательном порядке для сцепки. |

Эта функция может принимать любое количество аргументов, а также строки или массивы параметров. Пример сцепки строк см. в разделе [concat для строк](#concat).

В следующем примере показано, как объединить два массива.

    "parameters": {
        "firstarray": {
            type: "array"
        }
        "secondarray": {
            type: "array"
        }
     },
     "variables": {
         "combinedarray": "[concat(parameters('firstarray'), parameters('secondarray'))]"
     }


<a id="length" />

### <a name="length---array"></a>length для массива
`length(array)`

Возвращает количество элементов в массиве.

| Параметр | Обязательно | Тип | Описание |
|:--- |:--- |:--- |:--- |
| array |Да |Массив, |Массив, для которого нужно получить число элементов. |

Эту функцию можно использовать с массивом для указания числа итераций при создании ресурсов. В следующем примере параметр **siteNames** ссылается на массив имен для использования при создании веб-сайтов.

    "copy": {
        "name": "websitescopy",
        "count": "[length(parameters('siteNames'))]"
    }

Дополнительные сведения об использовании этой функции с массивом см. в статье [Создание нескольких экземпляров ресурсов в Azure Resource Manager](resource-group-create-multiple.md). 

Пример вычисления длины для строкового значения см. в разделе [length для строки](#lengthstring).

<a id="skip" />

### <a name="skip---array"></a>skip для массива
`skip(originalValue, numberToSkip)`

Возвращает массив всех элементов массива после указанного числа элементов.

| Параметр | Обязательно | Тип | Описание |
|:--- |:--- |:--- |:--- |
| originalValue |Да |Массив, |Массив, используемый для пропуска. |
| numberToSkip |Да |Целое число  |Число элементов, которые нужно пропустить. Если это значение меньше или равно 0, возвращаются все элементы в массиве. Если это значение превышает длину массива, возвращается пустой массив. |

Пример использования skip см. в разделе [skip для строки](#skipstring).

В следующем примере в массиве пропускается заданное число элементов.

    "parameters": {
      "first": {
        "type": "array",
        "metadata": {
          "description": "Values to use for skipping"
        },
        "defaultValue": [ "one", "two", "three" ]
      },
      "second": {
        "type": "int",
        "metadata": {
          "description": "Number of elements to skip"
        }
      }
    },
    "resources": [
    ],
    "outputs": {
      "return": {
        "type": "array",
        "value": "[skip(parameters('first'), parameters('second'))]"
      }
    }

<a id="take" />

### <a name="take---array"></a>take для массива
`take(originalValue, numberToTake)`

Возвращает массив с указанным числом элементов, считая от начала массива.

| Параметр | Обязательно | Тип | Описание |
|:--- |:--- |:--- |:--- |
| originalValue |Да |Массив, |Массив, из которого берутся элементы. |
| numberToTake |Да |Целое число  |Число элементов, которые нужно извлечь. Если это значение меньше или равно 0, возвращается пустой массив. Если это значение превышает длину заданного массива, возвращаются все элементы в массиве. |

Пример использования в разделе [take для строки](#takestring).

В следующем примере из массива извлекается заданное число элементов.

    "parameters": {
      "first": {
        "type": "array",
        "metadata": {
          "description": "Values to use for taking"
        },
        "defaultValue": [ "one", "two", "three" ]
      },
      "second": {
        "type": "int",
        "metadata": {
          "description": "Number of elements to take"
        }
      }
    },
    "resources": [
    ],
    "outputs": {
      "return": {
        "type": "array",
        "value": "[take(parameters('first'),parameters('second'))]"
      }
    }

## <a name="deployment-value-functions"></a>Функции для параметров развертывания
Диспетчер ресурсов предоставляет следующие функции для получения значений из разделов шаблонов и значений, связанных с развертыванием:

* [deployment](#deployment)
* [parameters](#parameters)
* [variables](#variables)

Сведения о получении значений из ресурсов, групп ресурсов или подписки см. в разделе [Функции для работы с ресурсами](#resource-functions).

<a id="deployment" />

### <a name="deployment"></a>Развертывание
`deployment()`

Возвращает сведения о текущей операции развертывания.

Эта функция возвращает объект, который передается во время развертывания. Свойства в возвращаемом объекте зависят от того, передается ли объект развертывания в виде ссылки или встроенного объекта. 

Когда объект развертывания передается встроенным (например, при использовании параметра **-TemplateFile** в Azure PowerShell для определения локального файла), формат возвращаемого объекта будет таким:

    {
        "name": "",
        "properties": {
            "template": {
                "$schema": "",
                "contentVersion": "",
                "parameters": {},
                "variables": {},
                "resources": [
                ],
                "outputs": {}
            },
            "parameters": {},
            "mode": "",
            "provisioningState": ""
        }
    }

Когда объект передается в виде ссылки, как, например, при использовании параметра **-TemplateUri** для указания на удаленный объект, объект возвращается в следующем формате. 

    {
        "name": "",
        "properties": {
            "templateLink": {
                "uri": ""
            },
            "template": {
                "$schema": "",
                "contentVersion": "",
                "parameters": {},
                "variables": {},
                "resources": [],
                "outputs": {}
            },
            "parameters": {},
            "mode": "",
            "provisioningState": ""
        }
    }

В следующем примере показано использование deployment() для ссылки на другой шаблон в зависимости от URI родительского шаблона.

    "variables": {  
        "sharedTemplateUrl": "[uri(deployment().properties.templateLink.uri, 'shared-resources.json')]"  
    }  

<a id="parameters" />

### <a name="parameters"></a>parameters
`parameters (parameterName)`

Возвращает значение параметра. Указанное имя параметра должно быть определено в разделе параметров шаблона.

| Параметр | Обязательно | Тип | Описание |
|:--- |:--- |:--- |:--- |
| имя_параметра |Да |Строка |Имя параметра, который требуется вернуть. |

В следующем примере показано упрощенное использование функции parameters.

    "parameters": { 
      "siteName": {
          "type": "string"
      }
    },
    "resources": [
       {
          "apiVersion": "2014-06-01",
          "name": "[parameters('siteName')]",
          "type": "Microsoft.Web/Sites",
          ...
       }
    ]

<a id="variables" />

### <a name="variables"></a>variables
`variables (variableName)`

Возвращает значение переменной. Указанное имя переменной должно быть определено в разделе переменных шаблона.

| Параметр | Обязательно | Тип | Описание |
|:--- |:--- |:--- |:--- |
| variableName |Да |Строка |Имя переменной, которую необходимо вернуть. |

В следующем примере используется значение переменной.

    "variables": {
      "storageName": "[concat('storage', uniqueString(resourceGroup().id))]"
    },
    "resources": [
      {
        "type": "Microsoft.Storage/storageAccounts",
        "name": "[variables('storageName')]",
        ...
      }
    ],

## <a name="resource-functions"></a>Функции для работы с ресурсами
Диспетчер ресурсов предоставляет следующие функции для получения значений ресурсов:

* [listKeys и list{Value}](#listkeys)
* [providers](#providers)
* [reference](#reference)
* [resourceGroup](#resourcegroup)
* [resourceId](#resourceid)
* [subscription](#subscription)

Получение значений параметров, переменных или текущего развертывания описано в разделе [Функции для параметров развертывания](#deployment-value-functions).

<a id="listkeys" />
<a id="list" />

### <a name="listkeys-and-listvalue"></a>listKeys и list{Value}
`listKeys (resourceName or resourceIdentifier, apiVersion)`

`list{Value} (resourceName or resourceIdentifier, apiVersion)`

Возвращает значения для любого типа ресурса, который поддерживает операцию list. Наиболее распространенным вариантом применения является **listKeys**. 

| Параметр | Обязательно | Тип | Описание |
|:--- |:--- |:--- |:--- |
| имя_ресурса или идентификатор_ресурса |Да |Строка |Уникальный идентификатор ресурса. |
| версия_API |Да |Строка |Версия API для состояния среды выполнения ресурса. Как правило, указывается в формате **гггг-мм-дд**. |

Любая операция, которая начинается с **list** , может быть использована в шаблоне как функция. Это относится к не только к **listKeys**, но и к таким операциям, как **list**, **listAdminKeys** и **listStatus**. Чтобы определить, какие типы ресурсов поддерживают операцию list, используйте следующую команду PowerShell.

    Get-AzureRmProviderOperation -OperationSearchString *  | where {$_.Operation -like "*list*"} | FT Operation

Или получите список с помощью Azure CLI. В следующем примере извлекаются все операции для **apiapps**и используется служебная программа JSON [jq](http://stedolan.github.io/jq/download/) , которая фильтрует только операции list.

    azure provider operations show --operationSearchString */apiapps/* --json | jq ".[] | select (.operation | contains(\"list\"))"

Идентификатор ресурса можно задать с помощью [функции resourceId](#resourceid) или в формате **{пространство_имен_поставщика}/{тип_ресурса}/{имя_ресурса}**.

В следующем примере показано, как получить в разделе выходных данных первичный и вторичный ключи из учетной записи хранения.

    "outputs": { 
      "listKeysOutput": { 
        "value": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('storageAccountName')), '2016-01-01')]", 
        "type" : "object" 
      } 
    } 

Возвращаемый функцией listKeys объект имеет следующий формат:

    {
      "keys": [
        {
          "keyName": "key1",
          "permissions": "Full",
          "value": "{value}"
        },
        {
          "keyName": "key2",
          "permissions": "Full",
          "value": "{value}"
        }
      ]
    }

<a id="providers" />

### <a name="providers"></a>providers
`providers (providerNamespace, [resourceType])`

Возвращает сведения о поставщике ресурсов и поддерживаемых типах ресурсов. Если тип ресурса не указан, функция возвращает все типы, поддерживаемые для поставщика ресурсов.

| Параметр | Обязательно | Тип | Описание |
|:--- |:--- |:--- |:--- |
| пространство_имен_поставщика |Да |Строка |Пространство имен поставщика. |
| тип_ресурса |Нет |Строка |Тип ресурса в указанном пространстве имен. |

Все поддерживаемые типы возвращаются в следующем формате. Упорядочение массива не гарантируется.

    {
        "resourceType": "",
        "locations": [ ],
        "apiVersions": [ ]
    }

Следующий пример показывает, как использовать функцию provider:

    "outputs": {
        "exampleOutput": {
            "value": "[providers('Microsoft.Storage', 'storageAccounts')]",
            "type" : "object"
        }
    }

<a id="reference" />

### <a name="reference"></a>reference
`reference (resourceName or resourceIdentifier, [apiVersion])`

Возвращает объект, представляющий состояние выполнения другого ресурса.

| Параметр | Обязательно | Тип | Описание |
|:--- |:--- |:--- |:--- |
| имя_ресурса или идентификатор_ресурса |Да |Строка |Имя или уникальный идентификатор ресурса. |
| версия_API |Нет |Строка |Версия API для указанного ресурса. Если ресурс не предоставляется в рамках того же шаблона, необходимо включить этот параметр. Как правило, указывается в формате **гггг-мм-дд**. |

Функция **reference** получает свое значение из состояния среды выполнения, и поэтому ее невозможно использовать в разделе переменных. Она может использоваться в разделе выходных данных шаблона.

С помощью функции reference вы прямо объявляете, что один ресурс зависит от другого, если ресурс, на который указывает ссылка, предоставляется в том же шаблоне. При этом свойство **dependsOn** использовать не нужно. Расчет функции выполняется только после развертывания ресурса, на который указывает ссылка.

Следующий пример ссылается на учетную запись хранения, которая развертывается в том же шаблоне.

    "outputs": {
        "NewStorage": {
            "value": "[reference(parameters('storageAccountName'))]",
            "type" : "object"
        }
    }

Следующий пример ссылается на учетную запись хранения, которая не развертывается в этом шаблоне, но существует в той же группе ресурсов, что и развертываемые ресурсы.

    "outputs": {
        "ExistingStorage": {
            "value": "[reference(concat('Microsoft.Storage/storageAccounts/', parameters('storageAccountName')), '2016-01-01')]",
            "type" : "object"
        }
    }

Можно получить конкретное значение из возвращенного объекта, например универсальный код ресурса (URI) конечной точки большого двоичного объекта, как показано ниже.

    "outputs": {
        "BlobUri": {
            "value": "[reference(concat('Microsoft.Storage/storageAccounts/', parameters('storageAccountName')), '2016-01-01').primaryEndpoints.blob]",
            "type" : "string"
        }
    }

Следующий пример ссылается на учетную запись хранения в другой группе ресурсов.

    "outputs": {
        "BlobUri": {
            "value": "[reference(resourceId(parameters('relatedGroup'), 'Microsoft.Storage/storageAccounts/', parameters('storageAccountName')), '2016-01-01').primaryEndpoints.blob]",
            "type" : "string"
        }
    }

Свойства в объекте, возвращаемом из функции **reference** , зависят от типа ресурса. Чтобы просмотреть имена и значения свойств для типа ресурса, создайте в разделе **outputs** простой шаблон, который возвращает объект. Если ресурс этого типа уже существует, то шаблон просто возвращает объект, не развертывая новых ресурсов. Если у вас нет существующего ресурса этого типа, то шаблон развертывает только этот тип и возвращает объект. Затем добавьте эти свойства в другие шаблоны, которым необходимо динамически извлечь значения в процессе развертывания. 

<a id="resourcegroup" />

### <a name="resourcegroup"></a>resourceGroup
`resourceGroup()`

Возвращает объект, который представляет текущую группу ресурсов. 

Возвращаемый объект имеет следующий формат:

    {
      "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}",
      "name": "{resourceGroupName}",
      "location": "{resourceGroupLocation}",
      "tags": {
      },
      "properties": {
        "provisioningState": "{status}"
      }
    }

В следующем примере расположение группы ресурсов используется для назначения расположения веб-сайту.

    "resources": [
       {
          "apiVersion": "2014-06-01",
          "type": "Microsoft.Web/sites",
          "name": "[parameters('siteName')]",
          "location": "[resourceGroup().location]",
          ...
       }
    ]

<a id="resourceid" />

### <a name="resourceid"></a>resourceId
`resourceId ([subscriptionId], [resourceGroupName], resourceType, resourceName1, [resourceName2]...)`

Возвращает уникальный идентификатор ресурса. 

| Параметр | Обязательно | Тип | Описание |
|:--- |:--- |:--- |:--- |
| subscriptionId |Нет |Строка (в формате GUID) |Значение по умолчанию — текущая подписка. Укажите это значение, если нужно получить ресурс из другой подписки. |
| имя_группы_ресурсов |Нет |Строка |Значение по умолчанию — текущая группа ресурсов. Укажите это значение, если нужно получить ресурс из другой группы ресурсов. |
| тип_ресурса |Да |Строка |Тип ресурса, включая пространство имен поставщика ресурсов. |
| имя_ресурса1 |Да |Строка |Имя ресурса. |
| имя_ресурса2 |Нет |Строка |Имя следующего ресурса, если ресурс является вложенным. |

Используйте эту функцию в том случае, когда имя ресурса является неоднозначным или не было предоставлено в пределах того же шаблона. Идентификатор возвращается в следующем формате:

    /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/{resourceProviderNamespace}/{resourceType}/{resourceName}

В следующем примере показано, как получить идентификаторы ресурсов для веб-сайта и базы данных. Веб-сайт находится в группе ресурсов с именем **myWebsitesGroup** , а база данных — в текущей группе ресурсов для этого шаблона.

    [resourceId('myWebsitesGroup', 'Microsoft.Web/sites', parameters('siteName'))]
    [resourceId('Microsoft.SQL/servers/databases', parameters('serverName'), parameters('databaseName'))]

Эта функция часто необходима при использовании учетной записи хранения или виртуальной сети в альтернативной группе ресурсов. Учетная запись хранения и виртуальная сеть могут использоваться несколькими группами ресурсов, поэтому их не следует удалять при удалении одной группы ресурсов. В следующем примере показано, как ресурс из внешней группы ресурсов можно легко использовать:

    {
      "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
          "virtualNetworkName": {
              "type": "string"
          },
          "virtualNetworkResourceGroup": {
              "type": "string"
          },
          "subnet1Name": {
              "type": "string"
          },
          "nicName": {
              "type": "string"
          }
      },
      "variables": {
          "vnetID": "[resourceId(parameters('virtualNetworkResourceGroup'), 'Microsoft.Network/virtualNetworks', parameters('virtualNetworkName'))]",
          "subnet1Ref": "[concat(variables('vnetID'),'/subnets/', parameters('subnet1Name'))]"
      },
      "resources": [
      {
          "apiVersion": "2015-05-01-preview",
          "type": "Microsoft.Network/networkInterfaces",
          "name": "[parameters('nicName')]",
          "location": "[parameters('location')]",
          "properties": {
              "ipConfigurations": [{
                  "name": "ipconfig1",
                  "properties": {
                      "privateIPAllocationMethod": "Dynamic",
                      "subnet": {
                          "id": "[variables('subnet1Ref')]"
                      }
                  }
              }]
           }
      }]
    }

<a id="subscription" />

### <a name="subscription"></a>subscription
`subscription()`

Возвращает сведения о подписке в следующем формате.

    {
        "id": "/subscriptions/#####",
        "subscriptionId": "#####",
        "tenantId": "#####"
    }

В следующем примере показана функция subscription, вызываемая в разделе выходных данных. 

    "outputs": { 
      "exampleOutput": { 
          "value": "[subscription()]", 
          "type" : "object" 
      } 
    } 


## <a name="next-steps"></a>Дальнейшие действия
* Описание разделов в шаблоне Azure Resource Manager см. в статье [Создание шаблонов Azure Resource Manager](resource-group-authoring-templates.md).
* Инструкции по объединению нескольких шаблонов см. в статье [Использование связанных шаблонов в Azure Resource Manager](resource-group-linked-templates.md).
* Указания по выполнению заданного количества циклов итерации при создании типа см. в разделе [Создание нескольких экземпляров ресурсов в Azure Resource Manager](resource-group-create-multiple.md).
* Указания по развертыванию созданного шаблона см. в статье [Развертывание приложения с использованием шаблона диспетчера ресурсов Azure](resource-group-template-deploy.md).



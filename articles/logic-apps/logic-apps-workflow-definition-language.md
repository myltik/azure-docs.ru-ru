---
title: Схема языка определения рабочих процессов в Azure Logic Apps | Документация Майкрософт
description: Написание пользовательских определений рабочих процессов для Azure Logic Apps с помощью языка определения рабочих процессов
services: logic-apps
author: ecfan
manager: SyntaxC4
editor: ''
documentationcenter: ''
ms.assetid: 26c94308-aa0d-4730-97b6-de848bffff91
ms.service: logic-apps
ms.workload: logic-apps
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: reference
ms.date: 04/30/2018
ms.author: estfan
ms.openlocfilehash: 14b273841d1fc15df635eb3b41b02ad77cbef90d
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/07/2018
ms.locfileid: "33775287"
---
# <a name="logic-apps-workflow-definitions-with-the-workflow-definition-language-schema"></a>Определения рабочих процессов приложений логики с помощью схемы языка определений рабочих процессов

Когда вы создаете рабочий процесс приложения логики с помощью [Azure Logic Apps](../logic-apps/logic-apps-overview.md), базовое определение вашего рабочего процесса описывает фактическую логику, выполняемую для этого приложения. Это описание следует структуре, которая определяется и проверяется с помощью схемы языка определения рабочих процессов, использующей формат [JSON (нотация объектов JavaScript)](https://www.json.org/). 
  
## <a name="workflow-definition-structure"></a>Структура определения рабочего процесса

Определение рабочего процесса имеет по крайней мере один триггер, который создает ваше приложение логики, а также одно или несколько действий, выполняемых вашим приложением. 

Ниже приведена высокоуровневая структура определения рабочего процесса.  
  
```json
"definition": {
  "$schema": "<workflow-definition-language-schema-version>",
  "contentVersion": "<workflow-definition-version-number>",
  "parameters": { "<workflow-parameter-definitions>" },
  "triggers": { "<workflow-trigger-definitions>" },
  "actions": { "<workflow-action-definitions>" },
  "outputs": { "<workflow-output-definitions>" }
}
```
  
| Элемент | Обязательно | ОПИСАНИЕ | 
|---------|----------|-------------| 
| Определение | Yes | Начальный элемент определения рабочего процесса. | 
| $schema | Только в случае использования внешней ссылки на определение рабочего процесса | Расположение файла схемы JSON, который описывает версию языка определения рабочих процессов: <p>`https://schema.management.azure.com/schemas/2016-06-01/Microsoft.Logic.json`</p> |   
| contentVersion | Нет  | Номер версии для вашего определения рабочего процесса (по умолчанию — 1.0.0.0). Чтобы помочь определить и подтвердить правильное определение при развертывании рабочего процесса, укажите значение для использования. | 
| parameters | Нет  | Определения для одного или нескольких параметров, которые передают данные в рабочий процесс <p><p>Максимальное количество параметров: 50. | 
| триггеры; | Нет  | Определения для одного или нескольких триггеров, которые создают рабочий процесс Вы можете определить более одного триггера, но только, используя язык определения рабочих процессов, а не визуально через конструктор логических приложений. <p><p>Максимальное количество триггеров: 10. | 
| actions | Нет  | Определения для одного или нескольких действий, которые будут запущены во время выполнения рабочего процесса. <p><p>Максимальное количество действий: 250. | 
| outputs | Нет  | Определения для выходных данных, которые возвращаются из рабочего процесса. <p><p>Максимальное количество типов выходных данных: 10. |  
|||| 

## <a name="parameters"></a>Параметры

В разделе `parameters` нужно определить все параметры рабочего процесса, для которых приложение логики принимает входные данные во время развертывания. Во время развертывания требуется объявить параметры и их значения. Прежде чем использовать эти параметры в других разделах рабочего процесса, объявите все параметры в этих разделах. 

Ниже приведена общая структура определения параметров:  

```json
"parameters": {
  "<parameter-name>": {
    "type": "<parameter-type>",
    "defaultValue": "<default-parameter-value>",
    "allowedValues": [ <array-with-permitted-parameter-values> ],
    "metadata": { 
      "key": { 
        "name": "<key-value>"
      } 
    }
  }
},
```

| Элемент | Обязательно | type | ОПИСАНИЕ |  
|---------|----------|------|-------------|  
| Тип | Yes | int, float, string, securestring, bool, массив, объект JSON, secureobject <p><p>**Примечание**. Для всех паролей, ключей и секретов используйте типы `securestring` и `secureobject`, потому что операция `GET` не возвращает эти типы. | Тип параметра. |
| defaultValue | Нет  | То же, что `type` | Значение параметра по умолчанию, если значение не задано при создании экземпляра рабочего процесса. | 
| allowedValues | Нет  | То же, что `type` | Массив со значениями, которые может принимать параметр. |  
| metadata | Нет  | Объект JSON | Любые другие параметры, например, имя или читаемое описание вашего приложения логики либо данные времени разработки, используемые Visual Studio или другими средствами. |  
||||

## <a name="triggers-and-actions"></a>Триггеры и действия  

В определении рабочего процесса разделы `triggers` и `actions` определяют вызовы, которые происходят во время выполнения рабочего процесса. Синтаксис и дополнительные сведения об этих разделах см. в статье [Triggers and actions for workflow definitions in Azure Logic Apps](../logic-apps/logic-apps-workflow-actions-triggers.md) (Триггеры и действия для определений рабочих процессов в Azure Logic Apps).
  
## <a name="outputs"></a>outputs 

В разделе `outputs` определите данные, которые ваш рабочий процесс может вернуть при завершении работы. Например, чтобы отслеживать определенное состояние или значение из каждого выполнения, укажите, что в возвращенных выходных данных рабочего процесса должны содержаться такие данные. 

> [!NOTE]
> При ответе на входящие запросы из REST API службы не используйте `outputs`. Вместо этого используйте тип действия `Response`. Дополнительные сведения см. в статье [Triggers and actions for workflow definitions in Azure Logic Apps](../logic-apps/logic-apps-workflow-actions-triggers.md) (Триггеры и действия для определений рабочих процессов в Azure Logic Apps).

Ниже приведена общая структура определения выходных данных. 

```json
"outputs": {
  "<key-name>": {  
    "type": "<key-type>",  
    "value": "<key-value>"  
  }
} 
```

| Элемент | Обязательно | type | ОПИСАНИЕ | 
|---------|----------|------|-------------| 
| <*key-name*> | Yes | Строка | Имя ключа для возвращаемого значения в выходных данных. |  
| Тип | Yes | int, float, string, securestring, bool, массив, объект JSON | Тип возвращаемого значения в выходных данных. | 
| value | Yes | То же, что `type` | Возвращаемое значение в выходных данных. |  
||||| 

Чтобы получить результат выполнения рабочего процесса, просмотрите журнал выполнения и подробности приложения логики на портале Azure или используйте [REST API рабочих процессов](https://docs.microsoft.com/rest/api/logic/workflows). Вы также можете передавать выходные данные во внешние системы, например, PowerBI, для создания панелей мониторинга. 

<a name="expressions"></a>

## <a name="expressions"></a>Выражения

При работе с JSON вы можете иметь литеральные значения, которые существуют во время разработки, например:

```json
"customerName": "Sophia Owen", 
"rainbowColors": ["red", "orange", "yellow", "green", "blue", "indigo", "violet"], 
"rainbowColorsCount": 7 
```

Вы также можете использовать значения, которые не существуют до начала времени выполнения. Чтобы представить эти значения, можно использовать *выражения*, которые вычисляются во время выполнения. Выражение представляет собой последовательность, которая может содержать одну или несколько [функций](#functions), [операторов](#operators), переменных, явных значений или констант. В определении рабочего процесса вы можете использовать выражение в любом месте в строчном значении JSON, указав его вместе с префиксом "@". При вычислении выражения, представляющего значение JSON, тело выражения извлекается без символа @ и всегда приводит к другому значению JSON. 

Например, вы можете получить значение для ранее определенного свойства `customerName`, используя функцию [parameters()](../logic-apps/workflow-definition-language-functions-reference.md#parameters) в выражении, и присвоить это значение свойству `accountName`:

```json
"customerName": "Sophia Owen", 
"accountName": "@parameters('customerName')"
```

*Интерполяция строк* также позволяет использовать несколько выражений внутри строк, которые начинаются символом @ и заключены в фигурные скобки ({}). Ниже приведен синтаксис.

```json
@{ "<expression1>", "<expression2>" }
```

Результат всегда является строкой, что делает эту функцию аналогичной функции `concat()`, например: 

```json
"customerName": "First name: @{parameters('firstName')} Last name: @{parameters('lastName')}"
```

Если у вас есть строковый литерал, начинающийся с символа @, добавьте перед символом @ другой символ @ в качестве escape-символа: @@.

В примерах ниже показано, как вычисляются выражения.

| Значение JSON | Результат |
|------------|--------| 
| "Sophia Owen" | Возвращаются символы: "Sophia Owen". |
| "array[1]" | Возвращаются символы: "array[1]". |
| "\@@\" | Эти символы возвращаются в виде строки с одним символом: "@". |   
| \"\@\" | Эти символы возвращаются в виде строки с двумя символами: " @". |
|||

Для этих примеров предположим, что вы определяете "myBirthMonth" равным "January", а "myAge" равным числу 42:  
  
```json
"myBirthMonth": "January",
"myAge": 42
```

В примерах ниже показано, как вычисляются выражения.

| Выражение JSON | Результат |
|-----------------|--------| 
| "@parameters('myBirthMonth')" | Возвращается строка: "January". |  
| "@{parameters('myBirthMonth')}" | Возвращается строка: "January". |  
| "@parameters('myAge')" | Возвращается число: 42. |  
| "@{parameters('myAge')}" | Возвращается число как строка: "42". |  
| "My age is @{parameters('myAge')}" | Возвращается строка: "My age is 42". |  
| "@concat('My age is ', string(parameters('myAge')))" | Возвращается строка: "My age is 42". |  
| "My age is @@{parameters('myAge')}" | Возвращается строка, содержащая выражение: "My age is @{parameters('myAge')}`. | 
||| 

При работе с визуальными элементами в конструкторе Logic Apps вы можете создавать выражения с помощью построителя выражений, например: 

![Конструктор Logic Apps > Построитель выражений](./media/logic-apps-workflow-definition-language/expression-builder.png)

Когда вы закончите, выражение появится для соответствующего свойства в определении рабочего процесса, например свойства `searchQuery`:

```json
"Search_tweets": {
  "inputs": {
    "host": {
      "connection": {
       "name": "@parameters('$connections')['twitter']['connectionId']"
      }
    }
  },
  "method": "get",
  "path": "/searchtweets",
  "queries": {
    "maxResults": 20,
    "searchQuery": "Azure @{concat('firstName','', 'LastName')}"
  }
},
```

<a name="operators"></a>

## <a name="operators"></a>Операторы

В [выражениях](#expressions) и [функциях](#functions) операторы выполняют определенные задачи, такие как ссылка на свойство или значение в массиве. 

| Оператор | Задача | 
|----------|------|
| ' | Чтобы использовать строковый литерал как входные данные или указать его в выражениях и функциях, поместите строку в одинарные кавычки, например `'<myString>'`. Не используйте двойные кавычки (""), так как они конфликтуют с форматированием JSON вокруг всего выражения. Например:  <p>**Правильно**: length('Hello'). </br>**Неправильно**: length("Hello"). <p>Когда вы передаете массивы или числа, их не нужно заключать в какие-либо символы. Например:  <p>**Правильно**: length([1, 2, 3]). </br>**Неправильно**: length("[1, 2, 3]"). | 
| [] | Чтобы указать ссылку на значение в определенной позиции (индексе) в массиве, используйте квадратные скобки. Например, чтобы получить второй элемент в массиве: <p>`myArray[1]` | 
| . | Для указания ссылки на свойство в объекте используйте оператор "точка". Например, чтобы получить свойство `name` объекта JSON `customer`: <p>`"@parameters('customer').name"` | 
| ? | Оператор "вопросительный знак" позволяет ссылаться на отсутствующие свойства объекта без ошибок времени выполнения. Например, следующее выражение можно использовать для обработки отсутствующих выходных данных триггера: <p>`@coalesce(trigger().outputs?.body?.<someProperty>, '<property-default-value>')` | 
||| 

<a name="functions"></a>

## <a name="functions"></a>Функции Azure

Некоторые выражения получают значения из действий среды выполнения, которые могут не существовать в начале выполнения приложения логики. Чтобы ссылаться или работать с этими значениями в выражениях, вы можете использовать *функции*. Например, вы можете использовать математические функции для вычислений, такие как функция [add()](../logic-apps/workflow-definition-language-functions-reference.md#add), которая возвращает сумму из целых чисел или чисел с плавающей запятой. 

Вот лишь несколько примеров задач, которые вы можете выполнять с помощью функций: 

| Задача | Синтаксис функции | Результат | 
| ---- | --------------- | -------------- | 
| Возвращает строку символов в нижнем регистре. | toLower('<*текст*>') <p>Например, toLower('Hello'). | "hello" | 
| Возвращение глобального уникального идентификатора (GUID). | guid() |"c2ecc88d-88c8-4096-912c-d6f2e2b138ce" | 
|||| 

В этом примере показано, как вы можете получить значение из параметра `customerName` и присвоить его свойству `accountName` с помощью функции [parameters()](../logic-apps/workflow-definition-language-functions-reference.md#parameters) в выражении:

```json
"accountName": "@parameters('customerName')"
```

Вот некоторые другие общие способы использования функций в выражениях:

| Задача | Синтаксис функции в выражении | 
| ---- | -------------------------------- | 
| Выполнить операцию с элементом, передав этот элемент функции. | "@<*имя_функции*>(<*элемент*>)" | 
| 1. Получить значение *параметра*, используя вложенную функцию `parameters()`. </br>2. Выполнить операцию с результатом, передав это значение *функции*. | "@<*имя_функции*>(parameters('<*имя_параметра*>'))" | 
| 1. Получить результат из вложенной внутренней *функции*. </br>2. Передать результат внешней функции (*имя_функции2*). | "@<*имя_функции2*>(<*имя_функции*>(<*элемент*>))" | 
| 1. Получить результат выполнения *функции*. </br>2. Учитывая, что результат является объектом со свойством *имя_свойства*, получить значение этого свойства. | "@<*имя_функции*>(<*элемент*>).<*имя_свойства*>" | 
||| 

Например, функция `concat()` может принимать два или более строковых значения в качестве параметров. Эта функция объединяет эти строки в одну. Вы можете передать строковые литералы, например "Sophia" и "Owen", чтобы получить комбинированную строку "SophiaOwen":

```json
"customerName": "@concat('Sophia', 'Owen')"
```

Или можно получить строковые значения параметров. В этом примере используется функция `parameters()` в каждом параметре `concat()` и параметры `firstName` и `lastName`. Затем передайте полученные строки в функцию `concat()`, чтобы получить комбинированную строку, например "SophiaOwen":

```json
"customerName": "@concat(parameters('firstName'), parameters('lastName'))"
```

В любом случае в обоих примерах присвойте результат свойству `customerName`. 

Подробные сведения о каждой функции см. в [алфавитном справочном документе](../logic-apps/workflow-definition-language-functions-reference.md).
Или же вы можете продолжить изучать основное предназначение функций.

<a name="string-functions"></a>

### <a name="string-functions"></a>Строковые функции

Для работы со строками вы можете использовать эти строковые функции, а также некоторые [функции для коллекций](#collection-functions). Строковые функции работают только со строками. 

| Строковая функция | Задача | 
| --------------- | ---- | 
| [concat](../logic-apps/workflow-definition-language-functions-reference.md#concat) | Объединяет две или более строк и возвращает объединенную строку. | 
| [endsWith](../logic-apps/workflow-definition-language-functions-reference.md#endswith) | Проверяет, заканчивается ли строка определенной подстрокой. | 
| [guid](../logic-apps/workflow-definition-language-functions-reference.md#guid) | Создает глобально уникальный идентификатор (GUID) в виде строки. | 
| [indexOf](../logic-apps/workflow-definition-language-functions-reference.md#indexof) | Возвращает начальную позицию подстроки. | 
| [lastIndexOf](../logic-apps/workflow-definition-language-functions-reference.md#lastindexof) | Возвращает конечную позицию подстроки. | 
| [replace](../logic-apps/workflow-definition-language-functions-reference.md#replace) | Заменяет подстроку указанной строкой и возвращает обновленную строку. | 
| [split](../logic-apps/workflow-definition-language-functions-reference.md#split) | Возвращает массив, который содержит все символы из строки, отделенные разделителем. | 
| [startsWith](../logic-apps/workflow-definition-language-functions-reference.md#startswith) | Проверяет, начинается ли строка с определенной подстроки. | 
| [substring](../logic-apps/workflow-definition-language-functions-reference.md#substring) | Возвращает символы из строки, начиная с указанной позиции. | 
| [toLower](../logic-apps/workflow-definition-language-functions-reference.md#toLower) | Возвращает строку символов в нижнем регистре. | 
| [toUpper](../logic-apps/workflow-definition-language-functions-reference.md#toUpper) | Возвращает строку символов в верхнем регистре. | 
| [trim](../logic-apps/workflow-definition-language-functions-reference.md#trim) | Удаляет все начальные и конечные пробелы и возвращает обновленную строку. | 
||| 

<a name="collection-functions"></a>

### <a name="collection-functions"></a>Функции для коллекций

Для работы с коллекциями, обычно массивами, строками, а иногда и словарями, вы можете использовать следующие функции. 

| Функция для коллекций | Задача | 
| ------------------- | ---- | 
| [contains](../logic-apps/workflow-definition-language-functions-reference.md#contains) | Проверяет наличие определенного элемента в коллекции. |
| [empty](../logic-apps/workflow-definition-language-functions-reference.md#empty) | Проверяет, является ли коллекция пустой. | 
| [first](../logic-apps/workflow-definition-language-functions-reference.md#first) | Возвращает первый элемент из коллекции. | 
| [intersection](../logic-apps/workflow-definition-language-functions-reference.md#intersection) | Возвращает коллекцию, которая содержит *только* общие элементы в указанных коллекциях. | 
| [join](../logic-apps/workflow-definition-language-functions-reference.md#join) | Возвращает строку, содержащую *все* элементы из массива, в которой каждый символ отделен разделителем. | 
| [last](../logic-apps/workflow-definition-language-functions-reference.md#last) | Возвращает последний элемент из коллекции. | 
| [длина](../logic-apps/workflow-definition-language-functions-reference.md#length) | Возвращает число элементов в строке или массиве. | 
| [skip](../logic-apps/workflow-definition-language-functions-reference.md#skip) | Удаляет элементы из начала коллекции и возвращает *все другие элементы*. | 
| [take](../logic-apps/workflow-definition-language-functions-reference.md#take) | Возвращает элементы, расположенные в начале коллекции. | 
| [union](../logic-apps/workflow-definition-language-functions-reference.md#union) | Возвращает коллекцию, которая содержит *все* элементы из указанных коллекций. | 
||| 

<a name="comparison-functions"></a>

### <a name="comparison-functions"></a>Функции сравнения

Чтобы работать с условиями, сравнивать значения и результаты выражений или оценивать различные типы логики, можно использовать следующие функции сравнения. Подробные сведения о каждой функции см. в [алфавитном справочном документе](../logic-apps/workflow-definition-language-functions-reference.md).

| Функция сравнения | Задача | 
| ------------------- | ---- | 
| [and](../logic-apps/workflow-definition-language-functions-reference.md#and) (и); | Проверяет, истинны ли все выражения. | 
| [equals](../logic-apps/workflow-definition-language-functions-reference.md#equals) | Проверяет, эквивалентны ли оба значения. | 
| [greater](../logic-apps/workflow-definition-language-functions-reference.md#greater) | Проверяет, является ли первое значение большим, чем второе. | 
| [greaterOrEquals](../logic-apps/workflow-definition-language-functions-reference.md#greaterOrEquals) | Проверяет, является ли первое значение большим, чем второе, или равным ему. | 
| [if](../logic-apps/workflow-definition-language-functions-reference.md#if) (если); | Проверьте, какое значение имеет выражение: true или false. Возвращает указанное значение на основе результата. | 
| [less](../logic-apps/workflow-definition-language-functions-reference.md#less) | Проверяет, является ли первое значение меньшим, чем второе. | 
| [lessOrEquals](../logic-apps/workflow-definition-language-functions-reference.md#lessOrEquals) | Проверяет, является ли первое значение меньшим, чем второе, или равным ему. | 
| [not](../logic-apps/workflow-definition-language-functions-reference.md#not) (не); | Проверяет, имеет ли выражение значение false. | 
| [or](../logic-apps/workflow-definition-language-functions-reference.md#or) (или). | Проверяет, является ли хотя бы одно выражение истинным. |
||| 

<a name="conversion-functions"></a>

### <a name="conversion-functions"></a>Функции преобразования

Чтобы изменить тип или формат значения, можно использовать приведенные ниже функции преобразования. Например, вы можете изменить значение с логического на целочисленное. Чтобы узнать, как приложения логики обрабатывают типы содержимого во время преобразования, см. статью [Обработка типов содержимого в приложениях логики](../logic-apps/logic-apps-content-type.md). Подробные сведения о каждой функции см. в [алфавитном справочном документе](../logic-apps/workflow-definition-language-functions-reference.md).

| Функция преобразования | Задача | 
| ------------------- | ---- | 
| [array](../logic-apps/workflow-definition-language-functions-reference.md#array). | Возвращает массив из одного экземпляра указанных входных данных. Для использования нескольких входных данных см. раздел [createArray](../logic-apps/workflow-definition-language-functions-reference.md#createArray). | 
| [base64](../logic-apps/workflow-definition-language-functions-reference.md#base64) | Возвращает версию строки с кодировкой base64 для заданной строки. | 
| [base64ToBinary](../logic-apps/workflow-definition-language-functions-reference.md#base64ToBinary) | Возвращает двоичную версию строки с кодировкой base64. | 
| [base64ToString](../logic-apps/workflow-definition-language-functions-reference.md#base64ToString) | Возвращает строковую версию строки с кодировкой base64. | 
| [binary](../logic-apps/workflow-definition-language-functions-reference.md#binary) | Возвращает двоичную версию входного значения. | 
| [bool](../logic-apps/workflow-definition-language-functions-reference.md#bool); | Возвращает логическую версию входного значения. | 
| [createArray](../logic-apps/workflow-definition-language-functions-reference.md#createArray) | Возвращает массив из нескольких экземпляров входных данных. | 
| [dataUri](../logic-apps/workflow-definition-language-functions-reference.md#dataUri) | Возвращает URI данных входного значения. | 
| [dataUriToBinary](../logic-apps/workflow-definition-language-functions-reference.md#dataUriToBinary) | Возвращает двоичную версию строки URI данных. | 
| [dataUriToString](../logic-apps/workflow-definition-language-functions-reference.md#dataUriToString) | Возвращает строковую версию URI данных. | 
| [decodeBase64](../logic-apps/workflow-definition-language-functions-reference.md#decodeBase64) | Возвращает строковую версию строки с кодировкой base64. | 
| [decodeDataUri](../logic-apps/workflow-definition-language-functions-reference.md#decodeDataUri) | Возвращает двоичную версию строки URI данных. | 
| [decodeUriComponent](../logic-apps/workflow-definition-language-functions-reference.md#decodeUriComponent) | Возвращает строку, которая заменяет escape-символы декодированными версиями. | 
| [encodeUriComponent](../logic-apps/workflow-definition-language-functions-reference.md#encodeUriComponent) | Возвращает строку, которая заменяет символы, опасные для URL-адреса, escape-символами. | 
| [float](../logic-apps/workflow-definition-language-functions-reference.md#float) | Возвращает значение с плавающей запятой в качестве входного значения. | 
| [int](../logic-apps/workflow-definition-language-functions-reference.md#int) | Возвращает целочисленную версию строки. | 
| [json](../logic-apps/workflow-definition-language-functions-reference.md#json) | Возвращает значение типа JSON либо объект для строки или XML. | 
| [string](../logic-apps/workflow-definition-language-functions-reference.md#string) | Возвращает строковую версию входного значения. | 
| [uriComponent](../logic-apps/workflow-definition-language-functions-reference.md#uriComponent) | Возвращает кодированную версию URI для входного значения, заменив символы, опасные для URL-адреса, на escape-символы. | 
| [uriComponentToBinary](../logic-apps/workflow-definition-language-functions-reference.md#uriComponentToBinary) | Возвращает двоичную версию строки с закодированным URI. | 
| [uriComponentToString](../logic-apps/workflow-definition-language-functions-reference.md#uriComponentToString) | Возвращает строковую версию строки с закодированным URI. | 
| [xml](../logic-apps/workflow-definition-language-functions-reference.md#xml) | Возвращает XML-версию строки. | 
||| 

<a name="math-functions"></a>

### <a name="math-functions"></a>Математические функции

Для работы с целыми числами и числами с плавающей запятой можно использовать следующие математические функции. Подробные сведения о каждой функции см. в [алфавитном справочном документе](../logic-apps/workflow-definition-language-functions-reference.md).

| Математическая функция | Задача | 
| ------------- | ---- | 
| [добавление](../logic-apps/workflow-definition-language-functions-reference.md#add) | Возвращает результат сложения двух чисел. | 
| [div](../logic-apps/workflow-definition-language-functions-reference.md#div) | Возвращает результат деления двух чисел. | 
| [max](../logic-apps/workflow-definition-language-functions-reference.md#max) | Возвращает наибольшее значение из набора чисел или массива. | 
| [min](../logic-apps/workflow-definition-language-functions-reference.md#min) | Возвращает наименьшее значение из набора чисел или массива. | 
| [mod (модуль)](../logic-apps/workflow-definition-language-functions-reference.md#mod) | Возвращает остаток результата деления двух чисел. | 
| [mul](../logic-apps/workflow-definition-language-functions-reference.md#mul) | Возвращает результат умножения двух чисел. | 
| [rand](../logic-apps/workflow-definition-language-functions-reference.md#rand) | Возвращает случайное целое число из указанного диапазона. | 
| [range](../logic-apps/workflow-definition-language-functions-reference.md#range) | Возвращает массив целых чисел, который начинается с заданного целого числа. | 
| [sub](../logic-apps/workflow-definition-language-functions-reference.md#sub) | Вычитает второе число из первого числа и возвращает результат. | 
||| 

<a name="date-time-functions"></a>

### <a name="date-and-time-functions"></a>Функции даты и времени

Чтобы работать с датами и временем, вы можете использовать эти функции даты и времени.
Подробные сведения о каждой функции см. в [алфавитном справочном документе](../logic-apps/workflow-definition-language-functions-reference.md).

| Функция даты и времени | Задача | 
| --------------------- | ---- | 
| [addDays](../logic-apps/workflow-definition-language-functions-reference.md#addDays) | Добавляет количество дней к метке времени. | 
| [addHours](../logic-apps/workflow-definition-language-functions-reference.md#addHours) | Добавляет количество часов к метке времени. | 
| [addMinutes](../logic-apps/workflow-definition-language-functions-reference.md#addMinutes) | Добавляет количество минут к метке времени. | 
| [addSeconds](../logic-apps/workflow-definition-language-functions-reference.md#addSeconds) | Добавляет количество секунд к метке времени. |  
| [addToTime](../logic-apps/workflow-definition-language-functions-reference.md#addToTime) | Добавляет количество единиц времени к метке времени. См. раздел [getFutureTime](../logic-apps/workflow-definition-language-functions-reference.md#getFutureTime). | 
| [convertFromUtc](../logic-apps/workflow-definition-language-functions-reference.md#convertFromUtc) | Преобразовывает метку времени формата UTC в целевой часовой пояс. | 
| [convertTimeZone](../logic-apps/workflow-definition-language-functions-reference.md#convertTimeZone) | Преобразовывает метку времени из исходного часового пояса в целевой. | 
| [convertToUtc](../logic-apps/workflow-definition-language-functions-reference.md#convertToUtc) | Преобразует метку времени с исходным часовым поясом в формат UTC. | 
| [dayOfMonth](../logic-apps/workflow-definition-language-functions-reference.md#dayOfMonth) | Возвращает компонент дня месяца из метки времени. | 
| [dayOfWeek](../logic-apps/workflow-definition-language-functions-reference.md#dayOfWeek) | Возвращает компонент дня недели из метки времени. | 
| [dayOfYear](../logic-apps/workflow-definition-language-functions-reference.md#dayOfYear) | Возвращает компонент дня года из метки времени. | 
| [formatDateTime](../logic-apps/workflow-definition-language-functions-reference.md#formatDateTime) | Возвращает дату из метки времени. | 
| [getFutureTime](../logic-apps/workflow-definition-language-functions-reference.md#getFutureTime) | Возвращает текущую метку времени, а также указанные единицы времени. См. раздел [addToTime](../logic-apps/workflow-definition-language-functions-reference.md#addToTime). | 
| [getPastTime](../logic-apps/workflow-definition-language-functions-reference.md#getPastTime) | Возвращает текущую метку времени, вычитая указанные единицы времени. См. раздел [subtractFromTime](../logic-apps/workflow-definition-language-functions-reference.md#subtractFromTime). | 
| [startOfDay](../logic-apps/workflow-definition-language-functions-reference.md#startOfDay) | Возвращает начало дня для метки времени. | 
| [startOfHour](../logic-apps/workflow-definition-language-functions-reference.md#startOfHour) | Возвращает начало часа для метки времени. | 
| [startOfMonth](../logic-apps/workflow-definition-language-functions-reference.md#startOfMonth) | Возвращает начало месяца для метки времени. | 
| [subtractFromTime](../logic-apps/workflow-definition-language-functions-reference.md#subtractFromTime) | Вычитает количество единиц времени из метки времени. См. раздел [getPastTime](../logic-apps/workflow-definition-language-functions-reference.md#getPastTime). | 
| [ticks](../logic-apps/workflow-definition-language-functions-reference.md#ticks) | Возвращает значение свойства `ticks` для указанной метки времени. | 
| [utcNow](../logic-apps/workflow-definition-language-functions-reference.md#utcNow) | Возвращает текущую метку времени в виде строки. | 
||| 

<a name="workflow-functions"></a>

### <a name="workflow-functions"></a>Функции рабочего процесса

Эти функции рабочего процесса могут помочь:

* получить сведения об экземпляре рабочего процесса во время выполнения; 
* работать с входными данными, используемыми при создании экземпляра приложения логики;
* ссылаться на выходные данные из триггеров и действий.

Например, вы можете ссылаться на выходные данные одного действия и использовать их в действии, которое выполняется позже. Подробные сведения о каждой функции см. в [алфавитном справочном документе](../logic-apps/workflow-definition-language-functions-reference.md).

| Функция рабочего процесса | Задача | 
| ----------------- | ---- | 
| [action](../logic-apps/workflow-definition-language-functions-reference.md#action) | Возвращает выходные данные текущего действия во время выполнения или значения из других пар "имя — значение" JSON. См. раздел об [actions](../logic-apps/workflow-definition-language-functions-reference.md#actions). | 
| [actionBody](../logic-apps/workflow-definition-language-functions-reference.md#actionBody) | Возвращает результат действия `body` во время выполнения. См. раздел [body](../logic-apps/workflow-definition-language-functions-reference.md#body). | 
| [actionOutputs](../logic-apps/workflow-definition-language-functions-reference.md#actionOutputs) | Возвращает результат действия во время выполнения. См. раздел [actions](../logic-apps/workflow-definition-language-functions-reference.md#actions). | 
| [actions](../logic-apps/workflow-definition-language-functions-reference.md#actions) | Возвращает выходные данные действия во время выполнения или значения из других пар "имя — значение" JSON. См. раздел [action](../logic-apps/workflow-definition-language-functions-reference.md#action).  | 
| [body](#body) | Возвращает результат действия `body` во время выполнения. См. раздел [actionBody](../logic-apps/workflow-definition-language-functions-reference.md#actionBody). | 
| [formDataMultiValues](../logic-apps/workflow-definition-language-functions-reference.md#formDataMultiValues) | Создает массив значений, которые соответствуют имени ключа в выходных данных *form-data* или *form-encoded* действия. | 
| [formDataValue](../logic-apps/workflow-definition-language-functions-reference.md#formDataValue) | Возвращает значение, которое соответствует имени ключа в выходных данных *form-data* или *form-encoded* действия. | 
| [Item](../logic-apps/workflow-definition-language-functions-reference.md#item) | При использовании внутри повторяющегося действия в массиве возвращает текущий элемент массива во время текущей итерации действия. | 
| [items](../logic-apps/workflow-definition-language-functions-reference.md#items) | При использовании внутри цикла for-each или do-until возвращает текущий элемент из указанного цикла.| 
| [listCallbackUrl](../logic-apps/workflow-definition-language-functions-reference.md#listCallbackUrl) | Возвращает URL-адрес обратного вызова, который вызывает триггер или действие. | 
| [multipartBody](../logic-apps/workflow-definition-language-functions-reference.md#multipartBody) | Возвращает текст указанной части выходных данных действия, которые состоят из нескольких частей. | 
| [parameters](../logic-apps/workflow-definition-language-functions-reference.md#parameters) | Возвращает значение параметра, описанного в определении вашего приложения логики. | 
| [trigger](../logic-apps/workflow-definition-language-functions-reference.md#trigger) | Возвращает выходные данные триггера во время выполнения или значения из других пар "имя — значение" JSON. См. разделы [triggerOutputs](#triggerOutputs) и [triggerBody](../logic-apps/workflow-definition-language-functions-reference.md#triggerBody). | 
| [triggerBody](../logic-apps/workflow-definition-language-functions-reference.md#triggerBody) | Возвращает выходные данные `body` триггера во время выполнения. См. раздел [trigger](../logic-apps/workflow-definition-language-functions-reference.md#trigger). | 
| [triggerFormDataValue](../logic-apps/workflow-definition-language-functions-reference.md#triggerFormDataValue) | Возвращает одно значение, соответствующее имени ключа, из выходных данных *form-data* или *form-encoded* триггера. | 
| [triggerMultipartBody](../logic-apps/workflow-definition-language-functions-reference.md#triggerMultipartBody) | Возвращает текст указанной части выходных данных триггера, которые состоят из нескольких частей. | 
| [triggerFormDataMultiValues](../logic-apps/workflow-definition-language-functions-reference.md#triggerFormDataMultiValues) | Создает массив значений, которые соответствуют имени ключа в выходных данных *form-data* или *form-encoded* триггера. | 
| [triggerOutputs](../logic-apps/workflow-definition-language-functions-reference.md#triggerOutputs) | Возвращает выходные данные триггера во время выполнения или значения из других пар "имя — значение" JSON. См. раздел [trigger](../logic-apps/workflow-definition-language-functions-reference.md#trigger). | 
| [variables](../logic-apps/workflow-definition-language-functions-reference.md#variables) | Возвращает значение для указанной переменной. | 
| [workflow](../logic-apps/workflow-definition-language-functions-reference.md#workflow) | Возвращает все сведения о самом рабочем процессе во время выполнения. | 
||| 

<a name="uri-parsing-functions"></a>

### <a name="uri-parsing-functions"></a>Функции анализа URI

Чтобы работать с идентификаторами URI и получать различные значения их свойств, вы можете использовать эти функции анализа URI. Подробные сведения о каждой функции см. в [алфавитном справочном документе](../logic-apps/workflow-definition-language-functions-reference.md).

| Функция анализа URI | Задача | 
| -------------------- | ---- | 
| [uriHost](../logic-apps/workflow-definition-language-functions-reference.md#uriHost) | Возвращает значение `host` для URI. | 
| [uriPath](../logic-apps/workflow-definition-language-functions-reference.md#uriPath) | Возвращает значение `path` для URI. | 
| [uriPathAndQuery](../logic-apps/workflow-definition-language-functions-reference.md#uriPathAndQuery) | Возвращает значения `path` и `query` для URI. | 
| [uriPort](../logic-apps/workflow-definition-language-functions-reference.md#uriPort) | Возвращает значение `port` для URI. | 
| [uriQuery](../logic-apps/workflow-definition-language-functions-reference.md#uriQuery) | Возвращает значение `query` для URI. | 
| [uriScheme](../logic-apps/workflow-definition-language-functions-reference.md#uriScheme) | Возвращает значение `scheme` для URI. | 
||| 

<a name="manipulation-functions"></a>

### <a name="json-and-xml-functions"></a>Функции JSON и XML

Для работы с объектами JSON и узлами XML вы можете использовать следующие функции обработки. Подробные сведения о каждой функции см. в [алфавитном справочном документе](../logic-apps/workflow-definition-language-functions-reference.md).

| Функция обработки | Задача | 
| --------------------- | ---- | 
| [addProperty](../logic-apps/workflow-definition-language-functions-reference.md#addProperty) | Добавляет свойство и его значения или пару "имя — значение" в объект JSON и возвращает обновленный объект. | 
| [coalesce](../logic-apps/workflow-definition-language-functions-reference.md#coalesce) | Возвращает первое ненулевое значение из одного или нескольких параметров. | 
| [removeProperty](../logic-apps/workflow-definition-language-functions-reference.md#removeProperty) | Удаляет свойство из объекта JSON и возвращает обновленный объект. | 
| [setProperty](../logic-apps/workflow-definition-language-functions-reference.md#setProperty) | Задает значение свойства объекта JSON и возвращает обновленный объект. | 
| [xpath](../logic-apps/workflow-definition-language-functions-reference.md#xpath) | Проверяет XML на наличие узлов или значений, которые соответствуют выражению XPath, и возвращает соответствующие узлы или значения. | 
||| 

## <a name="next-steps"></a>Дополнительная информация

* Дополнительные сведения о [действиях и триггерах языка определения рабочих процессов](../logic-apps/logic-apps-workflow-actions-triggers.md).
* Дополнительные сведения, о том как программным способом создавать приложения логики и управлять ими с помощью [REST API рабочих процессов](https://docs.microsoft.com/rest/api/logic/workflows).

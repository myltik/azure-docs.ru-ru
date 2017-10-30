---
title: "Расширение OpenAPI для пользовательских соединителей — Azure Logic Apps | Документация Майкрософт"
description: "Расширение OpenAPI для пользовательских соединителей с помощью дополнительных функций"
author: ecfan
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: 
ms.service: logic-apps
ms.workload: logic-apps
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/22/2017
ms.author: LADocs; estfan
ms.openlocfilehash: e8565019bd4631043485224c73c2fe60e633951c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="extend-your-custom-connectors-openapi-with-advanced-functionality"></a>Расширение OpenAPI для пользовательских соединителей с помощью дополнительных функций

Чтобы создать пользовательские соединители для Azure Logic Apps, Microsoft Flow или Microsoft PowerApps, необходимо предоставить файл определения OpenAPI, который является независимым от языка документом, пригодным для машинного чтения, описывающим операции и параметры API. При создании пользовательских соединителей для Logic Apps и Flow вместе с готовой функциональной возможностью OpenAPI можно также включать эти расширения OpenAPI:

* [`summary`](#summary)
* [`x-ms-summary`](#x-ms-summary)
* [`description`](#description)
* [`x-ms-visibility`](#x-ms-visibility)
* [`x-ms-dynamic-values`](#x-ms-dynamic-values)
* [`x-ms-dynamic-schema`](#x-ms-dynamic-schema)

Ниже приведены дополнительные сведения об этих расширениях.

<a name="summary"></a>

## <a name="summary"></a>summary

Задает заголовок для действия (операции). </br>
Область применения: операции. </br>
Рекомендуется: использовать *регистр, как в предложениях,* для `summary`. </br>
Например, "При добавлении события в календарь" или "Отправить электронное письмо".

![summary для каждой операции](./media/custom-connector-openapi-extensions/summary.png)

``` json
"actions" {
  "Send_an_email": {
    /// Other action properties here...
    "summary": "Send an email",
    /// Other action properties here...
  }
},
```

<a name="x-ms-summary"></a>

## <a name="x-ms-summary"></a>x-ms-summary

Задает заголовок для сущности. </br>
Область применения: параметры, схема ответа. </br>
Рекомендуется: использовать *заглавные буквы* для `x-ms-summary`. </br>
Пример: "Идентификатор календаря", "Субъект", "Описание события" и т. д

![x-ms-summary для каждой сущности](./media/custom-connector-openapi-extensions/x-ms-summary.png)

``` json
"actions" {
  "Send_an_email": {
    /// Other action properties here...
    "parameters": [ 
      {
        /// Other parameters here...
        "x-ms-summary": "Subject",
        /// Other parameters here...
      }
    ]
  }
},
```

<a name="description"></a>

## <a name="description"></a>Описание

Задает подробное описание функциональных возможностей операции, формата и функций сущности. </br>
Область применения: операции, параметры, схема ответа. </br>
Рекомендуется: использовать *регистр, как в предложениях,* для `description`. </br>
Например, "Эта операция активируется при добавлении нового события в календарь", "Укажите тему письма" и так далее.

![description для каждой операции или сущности](./media/custom-connector-openapi-extensions/description.png)

``` json
"actions" {
  "Send_an_email": {
     "description": "Specify the subject of the mail",
     /// Other action properties here...
  }
},
```

<a name="visibility"></a>

## <a name="x-ms-visibility"></a>x-ms-visibility

Указывает видимость сущности в пользовательском интерфейсе. </br>
Возможные значения: `important`, `advanced`, `internal`. </br>
Область применения: операции, параметры, схемы.

* Сначала пользователь видит операции и параметры, помеченные как `important`.
* Операции и параметры, помеченные как `advanced`, скрыты в дополнительном меню.
* Операции и параметры, помеченные как `internal`, недоступны для пользователя.

> [!NOTE] 
> Для параметров типа `internal` и `required` **нужно** задать значения по умолчанию.

Пример: в меню **Дополнительные сведения** и **Показывать дополнительные параметры** скрыты операции и параметры, помеченные как `advanced`.

![x-ms-visibility для отображения или скрытия операций и параметров](./media/custom-connector-openapi-extensions/x-ms-visibility.png)

``` json
"actions" {
  "Send_an_email": {
     /// Other action properties here...
     "parameters": [
         {
           "name": "Subject",
           "type": "string",
           "description": "Specify the subject of the mail",
           "x-ms-summary": "Subject",
           "x-ms-visibility": "important",
           /// Other parameter properties here...
         }
     ]
     /// Other action properties here...
  }
},
```

<a name="x-ms-dynamic-values"></a>

## <a name="x-ms-dynamic-values"></a>x-ms-dynamic-values

Отображает заполненный список пользователю для выбора входных параметров операции. </br>
Область применения: параметры. </br>
Как использовать: добавить объект `x-ms-dynamic-values` в определение параметра. Ознакомьтесь с [примером OpenAPI](https://procsi.blob.core.windows.net/blog-images/sampleDynamicSwagger.json).

![x-ms-dynamic-values для отображения списков](./media/custom-connector-openapi-extensions/x-ms-dynamic-values.png)

### <a name="properties-for-x-ms-dynamic-values"></a>Свойства x-ms-dynamic-values

| Имя | Обязательный или необязательный | Описание | 
| ---- | -------------------- | ----------- | 
| **OperationID** | Обязательно | Операция вызова для заполнения списка | 
| **value-path** | Обязательно | Строка пути в объекте свойства `value-collection`, которая ссылается на значение параметра. </br>Если свойство `value-collection` не указано, ответ вычисляется в качестве массива. | 
| **value-title** | Необязательно | Строка пути в объекте свойства `value-collection`, которая ссылается на описание значения. </br>Если свойство `value-collection` не указано, ответ вычисляется в качестве массива. | 
| **value-collection** | Необязательно | Строка пути, значением которой является массив объектов в полезных данных ответа | 
| **parameters** | Необязательно | Объект, свойства которого указывают входные параметры, необходимые для вызова операций с динамическими значениями | 
|||| 

Ниже приведен пример, показывающий свойства в `x-ms-dynamic-values`.

``` json
"x-ms-dynamic-values": {
  "operationId": "PopulateDropdown",
  "value-path": "name",
  "value-title": "properties/displayName",
  "value-collection": "value",
  "parameters": {
     "staticParameter": "<value>",
     "dynamicParameter": {
        "parameter": "<value-to-pass-to-dynamicParameter>"
     }
  }
}
```

## <a name="example-all-the-openapi-extensions-up-to-this-point"></a>Пример: все расширения OpenAPI

``` json
"/api/lists/{listID-dynamic}": {
    "get": {
        "description": "Get items from a single list - uses dynamic values and outputs dynamic schema",
        "summary": "Gets items from the selected list",
        "operationID": "GetListItems",
        "parameters": [
           {
             "name": "listID-dynamic",
             "type": "string",
             "in": "path",
             "description": "Select the list from where you want outputs",
             "required": true,
             "x-ms-summary": "Select List",
             "x-ms-dynamic-values": {
                "operationID": "GetLists",
                "value-path": "id",
                "value-title": "name"
             }
           }
        ]
    }
}
```

<a name="x-ms-dynamic schema"></a>

## <a name="x-ms-dynamic-schema"></a>x-ms-dynamic-schema

Указывает, что схема или ответ для текущего параметра являются динамическими. Этот объект может вызвать операцию, определяемую значением этого поля, динамически обнаружить схему и отобразить соответствующий пользовательский интерфейс для сбора данных, вводимых пользователем, или отобразить доступные поля. 

Область применения: параметры, ответ.

Как использовать: добавить объект `x-ms-dynamic-schema` к параметру запроса или определению текста ответа. Ознакомьтесь с [примером OpenAPI](https://procsi.blob.core.windows.net/blog-images/sampleDynamicSwagger.json).

В этом примере показано, как изменяется форма входных данных на основе элемента, выбранного пользователем из раскрывающегося списка:

![x-ms-dynamic-schema для динамических параметров](./media/custom-connector-openapi-extensions/x-ms-dynamic-schema.png)

В этом примере показано, как изменяется форма выходных данных на основе элемента, выбранного пользователем из раскрывающегося списка. В этой версии пользователь выбирает элемент "Автомобили":

![x-ms-dynamic-schema-response для выбранного элемента "Автомобили"](./media/custom-connector-openapi-extensions/x-ms-dynamic-schema-output1.png)

В этой версии пользователь выбирает элемент "Продукты":

![x-ms-dynamic-schema-response для выбранного элемента "Продукты"](./media/custom-connector-openapi-extensions/x-ms-dynamic-schema-output2.png)

### <a name="properties-for-x-ms-dynamic-schema"></a>Свойства x-ms-dynamic-schema

| Имя | Обязательный или необязательный | Описание | 
| ---- | -------------------- | ----------- | 
| **OperationID** | Обязательно | Операция вызова для получения схемы | 
| **parameters** | Обязательно | Объект, свойства которого указывают входные параметры, необходимые для вызова операции динамической схемы | 
| **value-path** |Необязательно | Строка пути, которая ссылается на свойство, имеющее схему. </br>Если она не указана, предполагается, что ответ содержит схему в свойствах корневого объекта. | 
|||| 

Ниже приведен пример динамического параметра:

``` json
{
  "name": "dynamicListSchema",
  "in": "body",
  "description": "Dynamic schema for items in the selected list",
  "schema": {
    "type": "object",
    "x-ms-dynamic-schema": {
        "operationID": "GetListSchema",
        "parameters": {
          "listID": {
            "parameter": "listID-dynamic"
          }
        },
        "value-path": "items"
    }
  }
}
```

Ниже приведен пример динамического ответа:

``` json
"DynamicResponseGetListSchema": {
   "type": "object",
   "x-ms-dynamic-schema": {
      "operationID": "GetListSchema",
      "parameters": {
         "listID": {
            "parameter": "listID-dynamic"
         }
      },
      "value-path": "items"
    }
}
```

## <a name="next-steps"></a>Дальнейшие действия

* [Describe custom APIs and custom connectors with Postman](../logic-apps/custom-connector-api-postman-collection.md) (Описание пользовательских API и соединителей с помощью Postman)
* [Register custom connectors in Azure Logic Apps](../logic-apps/logic-apps-custom-connector-register.md) (Регистрация пользовательских соединителей в Azure Logic Apps)
* [Регистрация настраиваемого соединителя](https://ms.flow.microsoft.com/documentation/register-custom-api/#register-your-custom-connector)
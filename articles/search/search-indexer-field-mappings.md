---
title: "Сопоставление полей в индексаторах Поиска Azure"
description: "Настройка сопоставлений полей индексатора в поиске Azure для выявления различий в названиях полей и представлениях данных"
services: search
documentationcenter: 
author: chaosrealm
manager: pablocas
editor: 
ms.assetid: 0325a4de-0190-4dd5-a64d-4e56601d973b
ms.service: search
ms.devlang: rest-api
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 10/27/2016
ms.author: eugenesh
translationtype: Human Translation
ms.sourcegitcommit: fc2f30569acc49dd383ba230271989eca8a14423
ms.openlocfilehash: 57e91f070d9a42882a56e708f12b1ce238ed9191

---

# <a name="field-mappings-in-azure-search-indexers"></a>Сопоставление полей в индексаторах Поиска Azure
При использовании индексаторов поиска Azure может возникнуть ситуация, когда входные данные не вписываются в схему целевого индекса. В таком случае можно преобразовать данные в желаемый формат, используя **сопоставления полей** .

Примеры ситуаций, в которых могут пригодиться сопоставления полей:

* Источник данных содержит поле `_id`, но поиск Azure не допускает, чтобы имена полей начинались с символа подчеркивания. Сопоставление полей позволяет "переименовать" поле.
* Вам нужно заполнить несколько полей в индексе, указав одни и те же данные источников данных, например, если к этим полям нужно применить разные анализаторы. Сопоставления полей позволяют разветвить поле источника данных.
* Данные необходимо шифровать или расшифровывать в кодировке Base64. Сопоставления полей поддерживают несколько **функций сопоставления**, включая шифрование и расшифровку в кодировке Base64.   

## <a name="setting-up-field-mappings"></a>Настройка сопоставлений полей
Сопоставления полей можно добавить при создании нового индексатора с использованием API для [создание индексатора](https://msdn.microsoft.com/library/azure/dn946899.aspx) . Сопоставлениями полей в индексаторе индексирования можно управлять с помощью API для [обновления индексатора](https://msdn.microsoft.com/library/azure/dn946892.aspx) .

Сопоставление полей состоит из трех частей:

1. Параметр `sourceFieldName`— представляет поле в источнике данных. Это — обязательное свойство.
2. Необязательный параметр `targetFieldName`— представляет поле в индексе поиска. Если этот параметр опущен, используется такое же имя, как в источнике данных.
3. Необязательный параметр `mappingFunction`— позволяет преобразовывать данные, используя одну из нескольких предопределенных функций. Полный список функций представлен [ниже](#mappingFunctions).

Сопоставления полей добавляются в массив `fieldMappings` в определении индексатора.

Например, так можно устранить расхождения в именах полей:

```JSON

PUT https://[service name].search.windows.net/indexers/myindexer?api-version=[api-version]
Content-Type: application/json
api-key: [admin key]
{
    "dataSourceName" : "mydatasource",
    "targetIndexName" : "myindex",
    "fieldMappings" : [ { "sourceFieldName" : "_id", "targetFieldName" : "id" } ]
}
```

Индексатор может содержать несколько сопоставлений полей. Например, так можно разветвить поле:

```JSON

"fieldMappings" : [
    { "sourceFieldName" : "text", "targetFieldName" : "textStandardEnglishAnalyzer" },
    { "sourceFieldName" : "text", "targetFieldName" : "textSoundexAnalyzer" },
]
```

> [!NOTE]
> В поиске Azure для устранения расхождений между именами полей и функций в сопоставлениях полей используется сравнение без учета регистра. Это удобно (допускаются ошибки в выборе регистра), но означает, что источник данных или индекс не может содержать поля, которые различаются только регистром.  
>
>

<a name="mappingFunctions"></a>

## <a name="field-mapping-functions"></a>Функции сопоставления полей
В настоящее время поддерживаются следующие функции:

* [base64Encode](#base64EncodeFunction)
* [base64Decode](#base64DecodeFunction)
* [extractTokenAtPosition](#extractTokenAtPositionFunction)
* [jsonArrayToStringCollection](#jsonArrayToStringCollectionFunction)

<a name="base64EncodeFunction"></a>

### <a name="base64encode"></a>base64Encode
Выполняет *безопасное* кодирование строки входных данных в Base64. Входные данные должны быть в кодировке UTF-8.

#### <a name="sample-use-case"></a>Примеры вариантов использования
Ключ документа в поиске Azure может содержать только безопасные символы URL (поскольку клиенты должны иметь возможность обращаться к документу, например, через API поиска). Если ваши данные содержат небезопасные символы URL и вы хотите применить их для того, чтобы заполнить поле ключа в индексе поиска, воспользуйтесь этой функцией.   

#### <a name="example"></a>Пример
```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "Path",
    "targetFieldName" : "UrlSafePath",
    "mappingFunction" : { "name" : "base64Encode" }
  }]
```

<a name="base64DecodeFunction"></a>

### <a name="base64decode"></a>base64Decode
Выполняет декодирование входной строки в Base64. Входные данные поступают в строку в кодировке Base64, *безопасную для URL* .

#### <a name="sample-use-case"></a>Примеры вариантов использования
Значения настраиваемых метаданных BLOB-объекта должны быть в кодировке ASCII. Для представления произвольных юникод-строк в настраиваемые метаданные BLOB-объекта можно использовать кодировку Base64. Тем не менее, чтобы поиск был значимым, можно использовать эту функцию для того, чтобы при заполнении индекса поиска снова превратить зашифрованные данные в "обычные" строки.  

#### <a name="example"></a>Пример
```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "Base64EncodedMetadata",
    "targetFieldName" : "SearchableMetadata",
    "mappingFunction" : { "name" : "base64Decode" }
  }]
```

<a name="extractTokenAtPositionFunction"></a>

### <a name="extracttokenatposition"></a>extractTokenAtPosition
Разбивает поле строки, используя заданный разделитель, и выбирает маркер из указанной позиции в результате разбиения.

Например, если при входных данных `Jane Doe` параметр `delimiter` имеет значение `" "` (пробел), а параметр `position` — значение 0, то результатом будет `Jane`; если параметр `position` имеет значение 1, то результатом будет `Doe`. Если позиция ссылается на несуществующий маркер, выдается ошибка.

#### <a name="sample-use-case"></a>Примеры вариантов использования
Источник данных содержит поле `PersonName`, и вам нужно индексировать его как два отдельных поля — `FirstName` и `LastName`. Эта функция позволяет разбить входные данные, используя пробел как разделитель.

#### <a name="parameters"></a>Параметры
* `delimiter`: строка, которая используемая как разделитель при разбиении входной строки.
* `position`: целочисленная нулевая позиция маркера, которую нужно выбрать после разбиения входной строки.    

#### <a name="example"></a>Пример
```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "PersonName",
    "targetFieldName" : "FirstName",
    "mappingFunction" : { "name" : "extractTokenAtPosition", "parameters" : { "delimiter" : " ", "position" : 0 } }
  },
  {
    "sourceFieldName" : "PersonName",
    "targetFieldName" : "LastName",
    "mappingFunction" : { "name" : "extractTokenAtPosition", "parameters" : { "delimiter" : " ", "position" : 1 } }
  }]
```

<a name="jsonArrayToStringCollectionFunction"></a>

### <a name="jsonarraytostringcollection"></a>jsonArrayToStringCollection
Преобразует строку, отформатированную как массив строк JSON, в массив строк, который можно использовать для заполнения поля `Collection(Edm.String)` в индексе.

Например, если исходной строкой является `["red", "white", "blue"]`, то целевое поле типа `Collection(Edm.String)` будет заполнено следующими тремя значениями: `red`, `white` и `blue`. Если входные значения нельзя проанализировать как массивы строк JSON, выдается ошибка.

#### <a name="sample-use-case"></a>Примеры вариантов использования
База данных SQL Azure не имеет встроенного типа данных, сопоставленного с полями `Collection(Edm.String)` в поиске Azure естественным образом. Чтобы заполнить поля в коллекции строк, отформатируйте исходные данные как строку JSON и воспользуйтесь этой функцией.

#### <a name="example"></a>Пример
```JSON

"fieldMappings" : [
  { "sourceFieldName" : "tags", "mappingFunction" : { "name" : "jsonArrayToStringCollection" } }
]
```

## <a name="help-us-make-azure-search-better"></a>Помогите нам усовершенствовать службу поиска Azure
Если вам нужна какая-либо функция или у вас есть идеи, которые можно было бы реализовать, сообщите об этом на [сайте UserVoice](https://feedback.azure.com/forums/263029-azure-search/).



<!--HONumber=Nov16_HO3-->



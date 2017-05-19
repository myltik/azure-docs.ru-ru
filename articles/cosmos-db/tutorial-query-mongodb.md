---
title: "Как выполнять запросы к данным в базе данных Azure Cosmos DB с помощью API DocumentDB | Документация Майкрософт"
description: "Узнайте, как выполнять запросы с помощью API DocumentDB в базе данных Azure Cosmos DB"
services: cosmosdb
documentationcenter: 
author: mimig1
manager: jhubbard
editor: 
tags: 
ms.assetid: 
ms.service: cosmosdb
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: 
ms.date: 05/10/2017
ms.author: mimig
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 696ff01c7ac65215777cfe0ae3412ff91a09135b
ms.contentlocale: ru-ru
ms.lasthandoff: 05/10/2017


---

# <a name="azure-cosmos-db-how-to-query-with-api-for-mongodb"></a>Как выполнять запросы к данным в базе данных Azure Cosmos DB с помощью API для MongoDB

[API для MongoDB](../documentdb/documentdb-protocol-mongodb.md) базы данных Azure Cosmos DB поддерживает [запросы оболочки MongoDB](https://docs.mongodb.com/manual/tutorial/query-documents/). 

В этой статье рассматриваются следующие задачи: 

> [!div class="checklist"]
> * Запрос данных с помощью MongoDB.

## <a name="sample-document"></a>Пример документа

В запросах в этой статье используется следующий образец документа.

```json
{
  "id": "WakefieldFamily",
  "parents": [
      { "familyName": "Wakefield", "givenName": "Robin" },
      { "familyName": "Miller", "givenName": "Ben" }
  ],
  "children": [
      {
        "familyName": "Merriam", 
        "givenName": "Jesse", 
        "gender": "female", "grade": 1,
        "pets": [
            { "givenName": "Goofy" },
            { "givenName": "Shadow" }
        ]
      },
      { 
        "familyName": "Miller", 
         "givenName": "Lisa", 
         "gender": "female", 
         "grade": 8 }
  ],
  "address": { "state": "NY", "county": "Manhattan", "city": "NY" },
  "creationDate": 1431620462,
  "isRegistered": false
}
```
## <a id="examplequery1"></a> Пример запроса 1 

С учетом приведенного выше образца документа сведений о семье следующий запрос возвращает документы, у которых поле идентификации соответствует `WakefieldFamily`.

**Запрос**
    
    db.families.find({ id: “WakefieldFamily”})

**Результаты**

    {
    "_id": "ObjectId(\"58f65e1198f3a12c7090e68c\")",
    "id": "WakefieldFamily",
    "parents": [
      {
        "familyName": "Wakefield",
        "givenName": "Robin"
      },
      {
        "familyName": "Miller",
        "givenName": "Ben"
      }
    ],
    "children": [
      {
        "familyName": "Merriam",
        "givenName": "Jesse",
        "gender": "female",
        "grade": 1,
        "pets": [
          { "givenName": "Goofy" },
          { "givenName": "Shadow" }
        ]
      },
      {
        "familyName": "Miller",
        "givenName": "Lisa",
        "gender": "female",
        "grade": 8
      }
    ],
    "address": {
      "state": "NY",
      "county": "Manhattan",
      "city": "NY"
    },
    "creationDate": 1431620462,
    "isRegistered": false
    }

## <a id="examplequery2"></a> Пример запроса 2 

Следующий запрос возвращает перечень всех детей в семье. 

**Запрос**
    
    db.familes.find( { id: “WakefieldFamily” }, { children: true } )

**Результаты**

    {
    "_id": "ObjectId("58f65e1198f3a12c7090e68c")",
    "children": [
      {
        "familyName": "Merriam",
        "givenName": "Jesse",
        "gender": "female",
        "grade": 1,
        "pets": [
          { "givenName": "Goofy" },
          { "givenName": "Shadow" }
        ]
      },
      {
        "familyName": "Miller",
        "givenName": "Lisa",
        "gender": "female",
        "grade": 8
      }
    ]
    }


## <a id="examplequery3"></a> Пример запроса 3 

Следующий запрос возвращает перечень всех семей, которые зарегистрированы. 

**Запрос**
    
    db.families.find( { "isRegistered" : true })
**Результаты**
: документ не будет возвращен. 

## <a id="examplequery4"></a> Пример запроса 4

Следующий запрос возвращает перечень всех семей, которые не зарегистрированы. 

**Запрос**
    
    db.families.find( { "isRegistered" : false })
**Результаты**

     {
    "_id": ObjectId("58f65e1198f3a12c7090e68c"),
    "id": "WakefieldFamily",
    "parents": [{
        "familyName": "Wakefield",
        "givenName": "Robin"
    }, {
        "familyName": "Miller",
        "givenName": "Ben"
    }],
    "children": [{
        "familyName": "Merriam",
        "givenName": "Jesse",
        "gender": "female",
        "grade": 1,
        "pets": [{
            "givenName": "Goofy"
        }, {
            "givenName": "Shadow"
        }]
    }, {
        "familyName": "Miller",
        "givenName": "Lisa",
        "gender": "female",
        "grade": 8
    }],
    "address": {
        "state": "NY",
        "county": "Manhattan",
        "city": "NY"
    },
    "creationDate": 1431620462,
    "isRegistered": false
}

## <a id="examplequery5"></a> Пример запроса 5

Следующий запрос возвращает перечень всех семей, которые не зарегистрированы и живут в штате Нью-Йорк. 

**Запрос**
    
     db.families.find( { "isRegistered" : false, "address.state" : "NY" })

**Результаты**

     {
    "_id": ObjectId("58f65e1198f3a12c7090e68c"),
    "id": "WakefieldFamily",
    "parents": [{
        "familyName": "Wakefield",
        "givenName": "Robin"
    }, {
        "familyName": "Miller",
        "givenName": "Ben"
    }],
    "children": [{
        "familyName": "Merriam",
        "givenName": "Jesse",
        "gender": "female",
        "grade": 1,
        "pets": [{
            "givenName": "Goofy"
        }, {
            "givenName": "Shadow"
        }]
    }, {
        "familyName": "Miller",
        "givenName": "Lisa",
        "gender": "female",
        "grade": 8
    }],
    "address": {
        "state": "NY",
        "county": "Manhattan",
        "city": "NY"
    },
    "creationDate": 1431620462,
    "isRegistered": false
}


## <a id="examplequery6"></a> Пример запроса 6

Следующий запрос возвращает все семьи, где дети ходят в 8-й класс.

**Запрос**
  
     db.families.find( { children : { $elemMatch: { grade : 8 }} } )

**Результаты**

     {
    "_id": ObjectId("58f65e1198f3a12c7090e68c"),
    "id": "WakefieldFamily",
    "parents": [{
        "familyName": "Wakefield",
        "givenName": "Robin"
    }, {
        "familyName": "Miller",
        "givenName": "Ben"
    }],
    "children": [{
        "familyName": "Merriam",
        "givenName": "Jesse",
        "gender": "female",
        "grade": 1,
        "pets": [{
            "givenName": "Goofy"
        }, {
            "givenName": "Shadow"
        }]
    }, {
        "familyName": "Miller",
        "givenName": "Lisa",
        "gender": "female",
        "grade": 8
    }],
    "address": {
        "state": "NY",
        "county": "Manhattan",
        "city": "NY"
    },
    "creationDate": 1431620462,
    "isRegistered": false
}

## <a id="examplequery7"></a> Пример запроса 7

Следующий запрос возвращает семьи, где 3 детей.

**Запрос**
  
      db.Family.find( {children: { $size:3} } )

**Результаты**

Результаты не будут выведены, так как у семей в нашем файле не более 2 детей. Этот запрос будет успешным и вернет полный документ, только если параметр будет равен 2.

## <a name="next-steps"></a>Дальнейшие действия

В этом руководстве вы выполнили следующее:

> [!div class="checklist"]
> * Вы узнали, как выполнять запросы с помощью MongoDB. 

Теперь вы можете приступать к следующему руководству, чтобы узнать, как глобально распределять данные.

> [!div class="nextstepaction"]
> [Глобальное распределение данных](tutorial-global-distribution-documentdb.md)



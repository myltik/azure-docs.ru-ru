---
title: Как выполнять запросы в базе данных Azure Cosmos DB с помощью API MongoDB | Документация Майкрософт
description: Узнайте, как выполнять запросы с помощью API MongoDB в базе данных Azure Cosmos DB
services: cosmos-db
author: SnehaGunda
manager: kfile
ms.service: cosmos-db
ms.component: cosmosdb-mongo
ms.devlang: na
ms.topic: tutorial
ms.date: 03/29/2018
ms.author: sngun
ms.custom: mvc
ms.openlocfilehash: 539e41dbd4d9e115c2d8daf90405d7683bcaa462
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/05/2018
ms.locfileid: "34763193"
---
# <a name="tutorial-query-azure-cosmos-db-by-using-the-mongodb-api"></a>Руководство. Выполнение запросов в Azure Cosmos DB с использованием API MongoDB

[API для MongoDB](mongodb-introduction.md) базы данных Azure Cosmos DB поддерживает [запросы оболочки MongoDB](https://docs.mongodb.com/manual/tutorial/query-documents/). 

В этой статье рассматриваются следующие задачи: 

> [!div class="checklist"]
> * Запрос данных с помощью MongoDB.

Чтобы начать работу, просмотрите это видео, в котором руководитель программы Azure Cosmos DB Энди Хоу (Andy Hoh) рассказывает о запросах к MongoDB:

>[!VIDEO https://www.youtube.com/tVk8S7lFWMA]

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
    
    db.families.find( { id: “WakefieldFamily” }, { children: true } )

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
**Результаты**: документ не будет возвращен. 

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

Результаты не будут выведены, так как у семей в документе не больше двоих детей. Этот запрос будет успешным и вернет полный документ, только если параметр будет равен 2.

## <a name="next-steps"></a>Дополнительная информация

В этом руководстве вы выполнили следующее:

> [!div class="checklist"]
> * Вы узнали, как выполнять запросы с помощью MongoDB. 

Теперь вы можете приступать к следующему руководству, чтобы узнать, как глобально распределять данные.

> [!div class="nextstepaction"]
> [Глобальное распределение данных](tutorial-global-distribution-sql-api.md)


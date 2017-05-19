---
title: "Как выполнять запросы с помощью SQL в базе данных Azure Cosmos DB | Документация Майкрософт"
description: "Узнайте, как выполнять запросы с данными DocumentDB с помощью SQL в базе данных Azure Cosmos DB"
services: cosmosdb
documentationcenter: 
author: mimig1
manager: jhubbard
editor: 
tags: 
ms.assetid: 
ms.service: cosmosdb
ms.custom: tutorial-develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: 
ms.date: 05/10/2017
ms.author: mimig
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: dd34ff43e78175b0d6a6e38bbd1303070f6549ab
ms.contentlocale: ru-ru
ms.lasthandoff: 05/10/2017


---

# <a name="azure-cosmos-db-how-to-query-using-sql"></a>Как выполнять запросы с помощью SQL в базе данных Azure Cosmos DB

[API DocumentDB](../documentdb/documentdb-introduction.md) базы данных Azure Cosmos DB поддерживает запросы к документам с помощью SQL. В этой статье содержится пример документа и два примера SQL-запросов и результатов.

В этой статье рассматриваются следующие задачи: 

> [!div class="checklist"]
> * Выполнение запросов к данным с помощью SQL.

## <a name="sample-document"></a>Пример документа

Запросы SQL в этой статье используют следующий пример документа.

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
## <a name="where-can-i-run-sql-queries"></a>Где могут выполняться SQL-запросы

Вы можете выполнять запросы с помощью обозревателя данных на портале Azure, через [REST API и пакеты SDK](../documentdb/documentdb-query-collections-query-explorer.md) и даже через [площадку для запросов](https://www.documentdb.com/sql/demo), которая выполняет запросы с использованием существующего набора примера данных.

Дополнительные сведения об SQL-запросах см. в статье:
* [SQL-запросы и синтаксис SQL в DocumentDB](../documentdb/documentdb-sql-query.md)

## <a name="prerequisites"></a>Предварительные требования

В этом руководстве предполагается, что у вас есть учетная запись и коллекция базы данных Azure Cosmos DB. У вас их нет? Завершите [краткое руководство](create-mongodb-nodejs.md) или [руководство разработчика](tutorial-develop-mongodb.md), чтобы создать учетную запись и коллекцию.

## <a name="example-query-1"></a>Пример запроса 1

С учетом приведенного выше образца документа со сведениями о семье следующий SQL-запрос возвращает документы, в которых поле идентификатора соответствует `WakefieldFamily`. Так как это инструкция `SELECT *`, результатом выполнения запроса является сформированный документ JSON:

**Запрос**

    SELECT * 
    FROM Families f 
    WHERE f.id = "WakefieldFamily"

**Результаты**

    [{
        "id": "AndersenFamily",
        "lastName": "Andersen",
        "parents": [
           { "firstName": "Thomas" },
           { "firstName": "Mary Kay"}
        ],
        "children": [
           {
               "firstName": "Henriette Thaulow", "gender": "female", "grade": 5,
               "pets": [{ "givenName": "Fluffy" }]
           }
        ],
        "address": { "state": "WA", "county": "King", "city": "seattle" },
        "creationDate": 1431620472,
        "isRegistered": true
    }]

## <a name="example-query-2"></a>Пример запроса 2

Следующий запрос возвращает все заданные имена детей в семье, идентификатор которых соответствует `WakefieldFamily`, упорядоченные по классу обучения.

**Запрос**

    SELECT c.givenName 
    FROM Families f 
    JOIN c IN f.children 
    WHERE f.id = 'WakefieldFamily'
    ORDER BY f.children.grade ASC

**Результаты**

    [
      { "givenName": "Jesse" }, 
      { "givenName": "Lisa"}
    ]


## <a name="next-steps"></a>Дальнейшие действия

В этом руководстве вы выполнили следующее:

> [!div class="checklist"]
> * Вы научились выполнять запросы с помощью SQL.  

Теперь вы можете приступать к следующему руководству, чтобы узнать, как глобально распределять данные.

> [!div class="nextstepaction"]
> [Глобальное распределение данных](tutorial-global-distribution-documentdb.md)



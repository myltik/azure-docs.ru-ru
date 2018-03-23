---
title: Использование API MongoDB для создания приложений Azure Cosmos DB | Документация Майкрософт
description: Руководство, в котором создается оперативная база данных с помощью интерфейсов API Azure Cosmos DB для MongoDB.
keywords: примеры mongodb
services: cosmos-db
author: AndrewHoh
manager: jhubbard
editor: ''
documentationcenter: ''
ms.assetid: fb38bc53-3561-487d-9e03-20f232319a87
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/22/2017
ms.author: anhoh
ms.openlocfilehash: 3d4b3bf36bdc93fdd1a65f5c8fdcfe2237d23aa9
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/17/2018
---
# <a name="build-an-azure-cosmos-db-api-for-mongodb-app-using-nodejs"></a>Создание приложения API для MongoDB в Azure Cosmos DB с помощью Node.js
> [!div class="op_single_selector"]
> * [.NET](sql-api-get-started.md)
> * [.NET Core](sql-api-dotnetcore-get-started.md)
> * [Java](sql-api-java-get-started.md)
> * [Node.js для MongoDB](mongodb-samples.md)
> * [Node.js](sql-api-nodejs-get-started.md)
> * [C++](sql-api-cpp-get-started.md)
>  
>

В этом примере показано, как создать консольное приложение API для MongoDB в Azure Cosmos DB с помощью Node.js.

Чтобы использовать этот пример, необходимо выполнить следующие действия.

* [Создание](create-mongodb-dotnet.md#create-account) приложения API для MongoDB в Azure Cosmos DB.
* Получить сведения о [строке подключения](connect-mongodb-account.md) MongoDB.

## <a name="create-the-app"></a>Создание приложения

1. Создайте файл *app.js*, а затем скопируйте и вставьте в него приведенный ниже код.

    ```nodejs
    var MongoClient = require('mongodb').MongoClient;
    var assert = require('assert');
    var ObjectId = require('mongodb').ObjectID;
    var url = 'mongodb://<username>:<password>@<endpoint>.documents.azure.com:10255/?ssl=true';

    var insertDocument = function(db, callback) {
    db.collection('families').insertOne( {
            "id": "AndersenFamily",
            "lastName": "Andersen",
            "parents": [
                { "firstName": "Thomas" },
                { "firstName": "Mary Kay" }
            ],
            "children": [
                { "firstName": "John", "gender": "male", "grade": 7 }
            ],
            "pets": [
                { "givenName": "Fluffy" }
            ],
            "address": { "country": "USA", "state": "WA", "city": "Seattle" }
        }, function(err, result) {
        assert.equal(err, null);
        console.log("Inserted a document into the families collection.");
        callback();
    });
    };
    
    var findFamilies = function(db, callback) {
    var cursor =db.collection('families').find( );
    cursor.each(function(err, doc) {
        assert.equal(err, null);
        if (doc != null) {
            console.dir(doc);
        } else {
            callback();
        }
    });
    };
    
    var updateFamilies = function(db, callback) {
    db.collection('families').updateOne(
        { "lastName" : "Andersen" },
        {
            $set: { "pets": [
                { "givenName": "Fluffy" },
                { "givenName": "Rocky"}
            ] },
            $currentDate: { "lastModified": true }
        }, function(err, results) {
        console.log(results);
        callback();
    });
    };
    
    var removeFamilies = function(db, callback) {
    db.collection('families').deleteMany(
        { "lastName": "Andersen" },
        function(err, results) {
            console.log(results);
            callback();
        }
    );
    };
    
    MongoClient.connect(url, function(err, db) {
    assert.equal(null, err);
    insertDocument(db, function() {
        findFamilies(db, function() {
        updateFamilies(db, function() {
            removeFamilies(db, function() {
                db.close();
            });
        });
        });
    });
    });
    ```

2. Замените следующие переменные в файле *app.js* параметрами учетной записи (узнайте, как найти [строку подключения](connect-mongodb-account.md)):
   
    ```nodejs
    var url = 'mongodb://<endpoint>:<password>@<endpoint>.documents.azure.com:10255/?ssl=true';
    ```
     
3. Откройте удобный для вас терминал, выполните команду **npm install mongodb --save**, а затем запустите приложение с помощью **node app.js**.

## <a name="next-steps"></a>Дополнительная информация
* Узнайте, как [использовать MongoChef](mongodb-mongochef.md) с учетной записью API для MongoDB в Azure Cosmos DB.

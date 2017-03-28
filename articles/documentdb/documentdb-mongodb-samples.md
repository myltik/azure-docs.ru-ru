---
title: "Использование API MongoDB для создания приложений DocumentDB | Документы Майкрософт"
description: "Руководство по NoSQL, в котором создается оперативная база данных с помощью API DocumentDB для MongoDB."
keywords: "примеры mongodb"
services: documentdb
author: AndrewHoh
manager: jhubbard
editor: 
documentationcenter: 
ms.assetid: fb38bc53-3561-487d-9e03-20f232319a87
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/06/2017
ms.author: anhoh
translationtype: Human Translation
ms.sourcegitcommit: 72b2d9142479f9ba0380c5bd2dd82734e370dee7
ms.openlocfilehash: 867ec5d0b27e790f3b00c94a4e5d14e4b2b17f73
ms.lasthandoff: 03/08/2017


---
# <a name="build-a-documentdb-api-for-mongodb-app-using-nodejs"></a>Создание приложения DocumentDB: API для MongoDB с помощью Node.js
> [!div class="op_single_selector"]
> * [.NET](documentdb-get-started.md)
> * [.NET Core](documentdb-dotnetcore-get-started.md)
> * [Java](documentdb-java-get-started.md)
> * [Node.js для MongoDB](documentdb-mongodb-samples.md)
> * [Node.js](documentdb-nodejs-get-started.md)
> * [C++](documentdb-cpp-get-started.md)
>  
>

В этом примере показано, как создать консольное приложение DocumentDB: API для MongoDB с помощью Node.js.

Чтобы использовать этот пример, необходимо выполнить следующие действия.

* [Создать](documentdb-create-mongodb-account.md) учетную запись Azure "DocumentDB: API для MongoDB".
* Получить сведения о [строке подключения](documentdb-connect-mongodb-account.md) MongoDB.

## <a name="create-the-app"></a>Создание приложения

1. Создайте файл *app.js*, а затем скопируйте и вставьте в него приведенный ниже код.

    ```nodejs
    var MongoClient = require('mongodb').MongoClient;
    var assert = require('assert');
    var ObjectId = require('mongodb').ObjectID;
    var url = 'mongodb://<endpoint>:<password>@<endpoint>.documents.azure.com:10250/?ssl=true';

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

2. Замените следующие переменные в файле *app.js* параметрами учетной записи (узнайте, как найти [строку подключения](documentdb-connect-mongodb-account.md)):
   
    ```nodejs
    var url = 'mongodb://<endpoint>:<password>@<endpoint>.documents.azure.com:10250/?ssl=true';
    ```
     
3. Откройте удобный для вас терминал, выполните команду **npm install mongodb --save**, а затем запустите приложение с помощью **node app.js**.

## <a name="next-steps"></a>Дальнейшие действия
* Узнайте, как [использовать MongoChef](documentdb-mongodb-mongochef.md) с учетной записью "DocumentDB: API для MongoDB".


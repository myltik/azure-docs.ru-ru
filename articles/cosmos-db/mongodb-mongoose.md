---
title: Использование платформы Mongoose с помощью Azure Cosmos DB | Документация Майкрософт
description: Сведения о подключении приложения Mongoose Node.js к Azure Cosmos DB.
services: cosmos-db
author: romitgirdhar
manager: kfile
ms.service: cosmos-db
ms.component: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 01/08/2018
ms.author: rogirdh
ms.openlocfilehash: 3c2a1299bec954a1b00b3315113c0967171b0001
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/05/2018
ms.locfileid: "34795872"
---
# <a name="azure-cosmos-db-using-the-mongoose-framework-with-azure-cosmos-db"></a>Azure Cosmos DB. Использование платформы Mongoose с помощью Azure Cosmos DB

Это руководство содержит сведения об использовании [платформы Mongoose](http://mongoosejs.com/) при сохранении данных в Azure Cosmos DB. В этом пошаговом руководстве используется API MongoDB для Azure Cosmos DB. Для тех из вас, кто еще не знаком с Mongoose, — это платформа объектного моделирования для MongoDB в Node.js, которая представляет собой простое решение на основе схемы для моделирования данных приложения.

Azure Cosmos DB — это глобально распределенная многомодельная служба базы данных Майкрософт. Вы можете быстро создавать и запрашивать документы, пары "ключ — значение" и базы данных графов, используя преимущества возможностей глобального распределения и горизонтального масштабирования базы данных Azure Cosmos DB.

## <a name="prerequisites"></a>предварительным требованиям

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

[Node.js](https://nodejs.org/) версии v0.10.29 или выше.

## <a name="create-an-azure-cosmos-db-account"></a>создание учетной записи Azure Cosmos DB;

Давайте создадим учетную запись Azure Cosmos DB. Если у вас уже есть учетная запись, которую вы собираетесь использовать, можно перейти к шагу [Настройка приложения Node.js](#SetupNode). Если вы используете эмулятор Azure Cosmos DB, выполните действия, описанные в статье об [эмуляторе Azure Cosmos DB](local-emulator.md), чтобы настроить эмулятор и сразу перейти к [настройке приложения Node.js](#SetupNode).

[!INCLUDE [cosmos-db-create-dbaccount-mongodb](../../includes/cosmos-db-create-dbaccount-mongodb.md)]

## <a name="set-up-your-nodejs-application"></a>Настройка приложения Node.js

>[!Note]
> Если вы просто хотите ознакомиться с примером кода, а не настраивать приложение, клонируйте [пример](https://github.com/Azure-Samples/Mongoose_CosmosDB), используемый для этого руководства, и создайте приложение Mongoose на Node.js в Azure Cosmos DB.

1. Чтобы создать приложение Node.js в выбранной папке, выполните следующую команду в командной строке узла.

    ```npm init```

    Ответьте на вопросы, чтобы приступить к работе с проектом.

1. Добавьте новый файл в папку и назовите его ```index.js```.
1. Установите необходимые пакеты, используя один из параметров ```npm install```.
    * Mongoose: ```npm install mongoose --save```.
    * Dotenv (если вы хотите загрузить секреты из ENV-файла): ```npm install dotenv --save```.

    >[!Note]
    > Параметр ```--save``` позволяет добавить зависимость в файл package.json.

1. Импортируйте зависимости в файл index.js.
    ```JavaScript
    var mongoose = require('mongoose');
    var env = require('dotenv').load();    //Use the .env file to load the variables
    ```

1. Добавьте строку подключения и имя Cosmos DB в файл ```.env```.

    ```JavaScript
    COSMOSDB_CONNSTR={Your MongoDB Connection String Here}
    COSMOSDB_DBNAME={Your DB Name Here}
    ```

1. Подключитесь к Azure Cosmos DB с помощью платформы Mongoose, добавив следующий код в конец файла index.js.
    ```JavaScript
    mongoose.connect(process.env.COSMOSDB_CONNSTR+process.env.COSMOSDB_DBNAME+"?ssl=true&replicaSet=globaldb"); //Creates a new DB, if it doesn't already exist

    var db = mongoose.connection;
    db.on('error', console.error.bind(console, 'connection error:'));
    db.once('open', function() {
    console.log("Connected to DB");
    });
    ```
    >[!Note]
    > Здесь переменные среды загружаются в виде process.env.{имя_переменной}, используя пакет npm dotenv.

    Подключившись к Azure Cosmos DB, вы можете приступить к настройке объектных моделей в Mongoose.

## <a name="caveats-to-using-mongoose-with-azure-cosmos-db"></a>Предостережения относительно использования Mongoose с Azure Cosmos DB

Для каждой создаваемой вами модели Mongoose создает коллекцию MongoDB в фоновом режиме. Тем не менее, учитывая модель выставления счетов Azure Cosmos DB с тарификацией за каждую коллекцию, это может оказаться не самым выгодным решением, если у вас есть несколько объектных моделей, которые неплотно заполнены.

В этом руководстве рассматриваются обе модели. Сначала ознакомимся с пошаговым руководством по хранению одного типа данных в коллекции. Это фактическое поведение для Mongoose.

Mongoose также использует концепцию, называемую [Дискриминаторы](http://mongoosejs.com/docs/discriminators.html). "Дискриминаторы" — это механизм наследования схемы. Он позволяет иметь несколько моделей с пересекающимися схемами поверх той же базовой коллекции MongoDB.

Вы можете хранить различные модели данных в одной и той же коллекции, а затем использовать предложения фильтра во время запроса, чтобы извлекать только необходимые данные.

### <a name="one-collection-per-object-model"></a>Одна коллекция на объектную модель

Поведение Mongoose по умолчанию — создавать коллекцию MongoDB каждый раз, когда создается объектная модель. В этом разделе описано, как это сделать с помощью MongoDB для Azure Cosmos DB. Если у вас есть объектные модели с большими объемами данных, для этого метода рекомендуется использовать Azure Cosmos DB. Это стандартная операционная модель для Mongoose, поэтому вы можете быть с ней знакомы, если работали с Mongoose.

1. Откройте ```index.js``` еще раз.

1. Создайте определение схемы Family (семья).

    ```JavaScript
    const Family = mongoose.model('Family', new mongoose.Schema({
        lastName: String,
        parents: [{
            familyName: String,
            firstName: String,
            gender: String
        }],
        children: [{
            familyName: String,
            firstName: String,
            gender: String,
            grade: Number
        }],
        pets:[{
            givenName: String
        }],
        address: {
            country: String,
            state: String,
            city: String
        }
    }));
    ```

1. Создайте объект Family (семья).

    ```JavaScript
    const family = new Family({
        lastName: "Volum",
        parents: [
            { firstName: "Thomas" },
            { firstName: "Mary Kay" }
        ],
        children: [
            { firstName: "Ryan", gender: "male", grade: 8 },
            { firstName: "Patrick", gender: "male", grade: 7 }
        ],
        pets: [
            { givenName: "Blackie" }
        ],
        address: { country: "USA", state: "WA", city: "Seattle" }
    });
    ```

1. Наконец, сохраните объект в Azure Cosmos DB. При этом в фоновом режиме будет создана коллекция.

    ```JavaScript
    family.save((err, saveFamily) => {
        console.log(JSON.stringify(saveFamily));
    });
    ```

1. Теперь создайте другие схему и объект. В этот раз создайте их для мест отдыха (VacationDestinations), которые могут заинтересовать семьи.
    1. Как и в прошлом примере, создайте схему.
    ```JavaScript
    const VacationDestinations = mongoose.model('VacationDestinations', new mongoose.Schema({
        name: String,
        country: String
    }));
    ```

    1. Создайте и сохраните пример объекта (к этой схеме можно добавить несколько объектов).
    ```JavaScript
    const vacaySpot = new VacationDestinations({
        name: "Honolulu",
        country: "USA"
    });

    vacaySpot.save((err, saveVacay) => {
        console.log(JSON.stringify(saveVacay));
    });
    ```

1. Теперь при входе на портал Azure вы увидите две коллекции, созданные в Azure Cosmos DB.

    ![Руководство по Node.js. Снимок экрана: портал Azure с учетной записью Azure Cosmos DB и выделенными именами коллекций. База данных Node][mutiple-coll]

1. Наконец, выполните считывание данных с Azure Cosmos DB. Так как используется стандартная операционная модель Mongoose, операции чтения идентичны таким же операциям с Mongoose.

    ```JavaScript
    Family.find({ 'children.gender' : "male"}, function(err, foundFamily){
        foundFamily.forEach(fam => console.log("Found Family: " + JSON.stringify(fam)));
    });
    ```

### <a name="using-mongoose-discriminators-to-store-data-in-a-single-collection"></a>Хранение данных в одной коллекции с помощью дискриминаторов Mongoose

Чтобы оптимизировать затраты каждой коллекции Azure Cosmos DB, в этом методе используются [дискриминаторы Mongoose](http://mongoosejs.com/docs/discriminators.html). Дискриминаторы дают возможность определить отличительный ключ, который позволяет хранить, различать и фильтровать разные объектные модели.

В этом примере вы создадите базовую объектную модель, определите отличительный ключ и добавите Family и VacationDestinations в качестве расширения к базовой модели.

1. Настройте базовую конфигурацию и определите ключ дискриминатора.

    ```JavaScript
    const baseConfig = {
        discriminatorKey: "_type", //If you've got a lot of different data types, you could also consider setting up a secondary index here.
        collection: "alldata"   //Name of the Common Collection
    };
    ```

1. Затем определите общую объектную модель.

    ```JavaScript
    const commonModel = mongoose.model('Common', new mongoose.Schema({}, baseConfig));
    ```

1. Теперь определите модель Family. Обратите внимание, что вместо ```mongoose.model``` используется ```commonModel.discriminator```. Кроме того, к схеме Mongoose добавлена базовая конфигурация. Итак, ```FamilyType``` — это ключ дискриминатора.

    ```JavaScript
    const Family_common = commonModel.discriminator('FamilyType', new     mongoose.Schema({
        lastName: String,
        parents: [{
            familyName: String,
            firstName: String,
            gender: String
        }],
        children: [{
            familyName: String,
            firstName: String,
           gender: String,
            grade: Number
        }],
        pets:[{
            givenName: String
        }],
        address: {
            country: String,
            state: String,
            city: String
        }
    }, baseConfig));
    ```

1. Аналогичным образом добавьте другую схему, на этот раз для VacationDestinations. В этом случае ключ дискриминатора — это ```VacationDestinationsType```.

    ```JavaScript
    const Vacation_common = commonModel.discriminator('VacationDestinationsType', new mongoose.Schema({
        name: String,
        country: String
    }, baseConfig));
    ```

1. Наконец, создайте объекты для модели и сохраните ее.
    1. Добавьте объекты к модели Family.
    ```JavaScript
    const family_common = new Family_common({
        lastName: "Volum",
        parents: [
            { firstName: "Thomas" },
            { firstName: "Mary Kay" }
        ],
        children: [
            { firstName: "Ryan", gender: "male", grade: 8 },
            { firstName: "Patrick", gender: "male", grade: 7 }
        ],
        pets: [
            { givenName: "Blackie" }
        ],
        address: { country: "USA", state: "WA", city: "Seattle" }
    });

    family_common.save((err, saveFamily) => {
        console.log("Saved: " + JSON.stringify(saveFamily));
    });
    ```

    1. Затем добавьте объекты к модели VacationDestinations и сохраните ее.
    ```JavaScript
    const vacay_common = new Vacation_common({
        name: "Honolulu",
        country: "USA"
    });

    vacay_common.save((err, saveVacay) => {
        console.log("Saved: " + JSON.stringify(saveVacay));
    });
    ```

1. Теперь, если вернуться на портал Azure, вы заметите, что у вас есть лишь одна коллекция ```alldata``` с данными Family и VacationDestinations.

    ![Руководство по Node.js. Снимок экрана: портал Azure с учетной записью Azure Cosmos DB и выделенным именем коллекции. База данных Node][alldata]

1. Кроме того, обратите внимание, что каждый объект имеет еще один атрибут с именем ```__type```, который позволяет различать две разные объектные модели.

1. Наконец, выполните считывание данных, хранящихся в Azure Cosmos DB. Mongoose позволяет фильтровать данные на основе модели. Таким образом, вам не нужно будет выполнять другие действия при считывании данных. Просто укажите свою модель (в этом случае ```Family_common```), и Mongoose выполнит фильтрацию по DiscriminatorKey.

    ```JavaScript
    Family_common.find({ 'children.gender' : "male"}, function(err, foundFamily){
        foundFamily.forEach(fam => console.log("Found Family (using discriminator): " + JSON.stringify(fam)));
    });
    ```

Как видите, работать с дискриминаторами Mongoose очень легко. Если у вас есть приложение, использующее платформу Mongoose, это руководство поможет настроить и запустить его в API MongoDB в Azure Cosmos DB без большого количества изменений.

## <a name="clean-up-resources"></a>Очистка ресурсов

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения об операциях, операторах, этапах, командах и параметрах MongoDB, поддерживаемых API MongoDB в Azure Cosmos DB, см. в статье [Поддержка возможностей и синтаксиса MongoDB в API-интерфейсе MongoDB](mongodb-feature-support.md).

[alldata]: ./media/mongodb-mongoose/mongo-collections-alldata.png
[mutiple-coll]: ./media/mongodb-mongoose/mongo-mutliple-collections.png

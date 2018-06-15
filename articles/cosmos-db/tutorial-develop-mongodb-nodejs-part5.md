---
title: Руководство по MongoDB, Angular и Node — часть 5 | Документация Майкрософт
description: Часть 5 серии руководств по созданию в Azure Cosmos DB приложения MongoDB с помощью Angular и Node и тех же API, которые используются для MongoDB
services: cosmos-db
author: SnehaGunda
manager: kfile
editor: ''
ms.service: cosmos-db
ms.component: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 09/05/2017
ms.author: sngun
ms.custom: mvc
ms.openlocfilehash: 2b6e3f66c6fb16adf6a8a1c51af61afb26aad59f
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/05/2018
ms.locfileid: "34798667"
---
# <a name="create-a-mongodb-app-with-angular-and-azure-cosmos-db---part-5-use-mongoose-to-connect-to-azure-cosmos-db"></a>Создание приложения MongoDB с помощью Angular и Azure Cosmos DB. Часть 5. Использование Mongoose для подключения к Azure Cosmos DB

Из этого руководства в нескольких частях вы узнаете, как создать приложение [API MongoDB](mongodb-introduction.md), написанное на Node.js с использованием Express, Angular и базы данных Azure Cosmos DB.

Часть 5 руководства основана на [части 4](tutorial-develop-mongodb-nodejs-part4.md). Здесь рассматриваются следующие задачи:

> [!div class="checklist"]
> * Использование Mongoose для подключения к Azure Cosmos DB.
> * Получение данных строки подключения из Azure Cosmos DB.
> * Создание модели Hero.
> * Создание службы Hero для получения данных hero.
> * Локальный запуск приложения

## <a name="video-walkthrough"></a>Видеоруководство

> [!VIDEO https://www.youtube.com/embed/sI5hw6KPPXI]


## <a name="prerequisites"></a>предварительным требованиям

Перед переходом к этой части руководства убедитесь, что выполнены все задачи из [части 4](tutorial-develop-mongodb-nodejs-part4.md).

> [!TIP]
> В этом руководстве изложены пошаговые инструкции по созданию приложения. Готовое приложение можно скачать из [репозитория angular-cosmosdb](https://github.com/Azure-Samples/angular-cosmosdb) на GitHub.

## <a name="use-mongoose-to-connect-to-azure-cosmos-db"></a>подключение к Azure Cosmos DB с помощью Mongoose;

1. Установите модуль npm mongoose, который обычно используется API для обмена данными с MongoDB.

    ```bash
    npm i mongoose --save
    ```

2. Теперь создайте файл с именем **mongo.js** в папке **server**. Добавьте в этот файл все ваши сведения о подключении для базы данных Azure Cosmos DB.

3. Скопируйте код ниже в **mongo.js**. Этот код выполняет следующие действия:
    * Требует установки Mongoose.
    * Переопределяет объект Promise Mongo, чтобы использовался базовый объект Promise, который встроен в ES6 или ES2015 и более поздних версий.
    * Вызывает файл env, который позволяет задавать определенные настройки в зависимости от среды — промежуточной, производственной или среды разработки. Мы создадим этот файл в ближайшее время.
    * Включает строку подключения MongoDB, которая будет задана в файле env.
    * Создает функцию подключения, которая вызывает Mongoose.

    ```javascript
    const mongoose = require('mongoose');
    /**
     * Set to Node.js native promises
     * Per http://mongoosejs.com/docs/promises.html
     */
    mongoose.Promise = global.Promise;

    const env = require('./env/environment');

    // eslint-disable-next-line max-len
    const mongoUri = `mongodb://${env.accountName}:${env.key}@${env.accountName}.documents.azure.com:${env.port}/${env.databaseName}?ssl=true`;

    function connect() {
     mongoose.set('debug', true);
     return mongoose.connect(mongoUri, { useMongoClient: true });
    }

    module.exports = {
      connect,
      mongoose
    };
    ```
    
4. В области обозревателя создайте папку с именем **environment** в папке **server**. В папке **environment** создайте файл с именем **environment.js**.

5. Как и в файле mongo.js, нам нужно включать `dbName`, `key` и `cosmosPort`. Поэтому скопируйте следующий код в **environment.js**.

    ```javascript
    // TODO: replace if yours are different
    module.exports = {
      accountName: 'your-cosmosdb-account-name-goes-here',
      databaseName: 'admin', 
      key: 'your-key-goes-here',
      port: 10255
    };
    ```

## <a name="get-the-connection-string-information"></a>Получение данных строки подключения

1. В **environment.js** измените значение `port` на 10255 (свой порт Cosmos DB можно узнать на портале Azure).

    ```javascript
    const port = 10255;
    ```

2. В **environment.js** измените значение `accountName` на имя учетной записи Azure Cosmos DB, которая была создана на [шаге 4](tutorial-develop-mongodb-nodejs-part4.md). 

3. Получите первичный ключ для учетной записи Azure Cosmos DB, использовав следующую команду CLI в окне терминала: 

    ```azure-cli-interactive
    az cosmosdb list-keys --name <cosmosdb-name> -g myResourceGroup
    ```    
    
    * `<cosmosdb-name>` — имя учетной записи Azure Cosmos DB, которую вы создали на [шаге 4](tutorial-develop-mongodb-nodejs-part4.md).

4. Скопируйте первичный ключ в файл environment.js как значение `key`.

    Теперь приложение располагает всеми данными, необходимыми для подключения к Azure Cosmos DB. Эти сведения также можно получить на портале. Дополнительные сведения см. в разделе [Получение строки подключения MongoDB для настройки](connect-mongodb-account.md#GetCustomConnection). Имя пользователя на портале равнозначно dbName в environments.js. 

## <a name="create-a-hero-model"></a>Создание модели Hero

1.  В области обозревателя создайте файл **hero.model.js** в папке **server**.

2. Скопируйте код ниже в **hero.model.js**. Этот код выполняет следующие действия:
   * Требует установки Mongoose.
   * Создает схему с идентификатором, именем и фразой.
   * Создает с помощью схемы модель.
   * Экспортирует модель. 
   * Коллекция должна называться Heroes (а не Heros, как бы она называлась по умолчанию в соответствии с правилами именования Mongoose для множественного числа).

   ```javascript
   const mongoose = require('mongoose');

   const Schema = mongoose.Schema;

   const heroSchema = new Schema(
     {
       id: { type: Number, required: true, unique: true },
       name: String,
       saying: String
     },
     {
       collection: 'Heroes'
     }
   );

   const Hero = mongoose.model('Hero', heroSchema);

   module.exports = Hero;
   ```

## <a name="create-a-hero-service"></a>Создание службы Hero

1.  В области обозревателя создайте файл **hero.service.js** в папке **server**.

2. Скопируйте код ниже в **hero.service.js**. Этот код выполняет следующие действия:
   * Извлекает только что созданную модель.
   * Устанавливает подключение к базе данных.
   * Создает переменную docquery, которая использует метод hero.find, чтобы определить запрос, возвращающий все элементы hero.
   * Выполняет запрос с помощью docquery.exec с разрешением на получение списка всех элементов hero, для состояния ответа которых установлено значение 200. 
   * Если для состояния установлено значение 500, возвращается сообщение об ошибке.
   * Так как мы используем модули, извлекаются элементы hero. 

   ```javascript
   const Hero = require('./hero.model');

   require('./mongo').connect();

   function getHeroes() {
     const docquery = Hero.find({});
     docquery
       .exec()
       .then(heroes => {
         res.status(200).json(heroes);
       })
       .catch(error => {
         res.status(500).send(error);
         return;
       });
   }

   module.exports = {
     getHeroes
   };
   ```

## <a name="add-the-hero-service-to-routesjs"></a>Добавление службы Hero в routes.js

1. В Visual Studio Code в **routes.js** закомментируйте функцию `res.send`, которая отправляет пример данных hero, и вместо нее добавьте строку для вызова функции `heroService.getHeroes`.

    ```javascript
    router.get('/heroes', (req, res) => {
      heroService.getHeroes(req, res);
    //  res.send(200, [
    //      {"id": 10, "name": "Starlord", "saying": "oh yeah"}
    //  ])
    });
    ```

2. В **routes.js** добавьте для службы Hero команду require:

    ```javascript
    const heroService = require('./hero.service'); 
    ```

3. В **hero.service.js** обновите функцию getHeroes, чтобы она принимала параметры `req` и `res`, как показано ниже:

    ```javascript
    function getHeroes(req, res) {
    ```

    Давайте выделим пару минут на проверку и просмотрим цепочку вызовов. Цепочка начинается с файла `index.js`, в котором настраивается сервер узла, и записи, которая настраивает и определяет наши маршруты. Затем файл routes.js обращается к службе Hero и дает ей команду получить наши функции, например getHeroes, а также передать запрос и ответ. Теперь наступает черед файла hero.service.js. Он захватывает модель и подключается к Mongo. При вызове он выполняет команду getHeroes и возвращает ответ 200. Затем он занимает свое место в цепочке. 

## <a name="run-the-app"></a>Запуск приложения

1. Теперь давайте снова запустим приложение. В Visual Studio Code сохраните все изменения, нажмите кнопку **Отладка** слева ![Значок отладки в Visual Studio Code](./media/tutorial-develop-mongodb-nodejs-part5/debug-button.png), затем — кнопку **Начать отладку** ![Значок начала отладки в Visual Studio Code](./media/tutorial-develop-mongodb-nodejs-part5/start-debugging-button.png).

3. Теперь в браузере откройте окно инструментов для разработчиков, выберите вкладку параметров сети и перейдите по адресу http://localhost:3000. Вот наше приложение.

    ![Новая учетная запись Azure Cosmos DB на портале Azure](./media/tutorial-develop-mongodb-nodejs-part5/azure-cosmos-db-heroes-app.png)

   В нашем приложении пока еще нет элементов hero. Но в следующей части руководства мы добавим функции Put, Push и Delete, чтобы добавлять, обновлять и удалять элементы hero из пользовательского интерфейса с использованием подключений Mongoose к нашей базе данных Azure Cosmos DB. 

## <a name="next-steps"></a>Дополнительная информация

В этой части руководства мы выполнили следующую задачу:

> [!div class="checklist"]
> * использовали интерфейс API Mongoose для подключения приложения Hero к Azure Cosmos DB; 
> * добавили в приложение функции для получения элементов hero.

Теперь вы можете приступить к следующей части руководства и добавить в приложение функции Post, Put и Delete.

> [!div class="nextstepaction"]
> [Добавление в приложение функций Post, Put и Delete](tutorial-develop-mongodb-nodejs-part6.md)

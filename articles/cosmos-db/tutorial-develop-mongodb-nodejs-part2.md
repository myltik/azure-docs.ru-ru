---
title: Руководство по MongoDB, Angular и Node — часть 2 | Документация Майкрософт
description: Часть 2 серии руководств по созданию приложения MongoDB в Azure Cosmos DB с помощью Angular и Node и тех же API, которые используются для MongoDB.
services: cosmos-db
documentationcenter: ''
author: SnehaGunda
manager: kfile
editor: ''
ms.assetid: ''
ms.service: cosmos-db
ms.workload: ''
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 09/05/2017
ms.author: sngun
ms.custom: mvc
ms.openlocfilehash: 9d3c3209807a201f11d4d0a4ddd905f332a3951f
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2018
---
# <a name="create-a-mongodb-app-with-angular-and-azure-cosmos-db---part-2-create-a-nodejs-express-app-with-the-angular-cli"></a>Создание приложения MongoDB с помощью Angular и Azure Cosmos DB. Часть 2. Создание приложения Node.js Express с помощью Angular CLI 

Из этого руководства в нескольких частях вы узнаете, как создать приложение [API MongoDB](mongodb-introduction.md), написанное на Node.js с использованием Express, Angular и базы данных Azure Cosmos DB.

Часть 2 руководства основана на [введении](tutorial-develop-mongodb-nodejs.md). Здесь рассматриваются следующие задачи:

> [!div class="checklist"]
> * установка Angular CLI и TypeScript;
> * создание нового проекта с помощью Angular;
> * создание приложения с помощью платформы Express;
> * тестирование приложения в Postman.

## <a name="video-walkthrough"></a>Видеоруководство

> [!VIDEO https://www.youtube.com/embed/lIwJIYcGSUg]

## <a name="prerequisites"></a>предварительным требованиям

Прежде чем начинать работу с этой частью руководства, обязательно просмотрите [вводный видеоролик](tutorial-develop-mongodb-nodejs.md).

Для работы с этим руководством также требуется следующее: 
* [Node.js](https://nodejs.org/) версии 8.4.0 или более поздней;
* [Postman](https://www.getpostman.com/)
* [Visual Studio Code](https://code.visualstudio.com/) или любой другой редактор кода.

> [!TIP]
> В этом руководстве изложены пошаговые инструкции по созданию приложения. Готовое приложение можно скачать из [репозитория angular-cosmosdb](https://github.com/Azure-Samples/angular-cosmosdb) на GitHub.

## <a name="install-the-angular-cli-and-typescript"></a>Установка Angular CLI и TypeScript

1. Откройте командную строку Windows или окно терминала Mac и установите Angular CLI.

    ```bash
    npm install -g @angular/cli
    ```

2. Введите следующую команду в командной строке, чтобы установить TypeScript. 

    ```bash
    npm install -g typescript
    ```

## <a name="use-the-angular-cli-to-create-a-new-project"></a>Создание проекта с помощью Angular CLI

1. В командной строке укажите папку, в которой требуется создать новый проект, а затем выполните приведенную ниже команду. Эта команда создает новую папку и проект с именем angular-cosmosdb и устанавливает компоненты Angular, необходимые для нового приложения. Также она устанавливает исходный код в папке src/client (-sd src/client), использует минимальные настройки (--minimal) и указывает, что в проекте используется SASS (CSS-подобный синтаксис с флагом ---style scss).

    ```bash
    ng new angular-cosmosdb -sd src/client --minimal --style scss
    ```

2. После выполнения команды перейдите в папку src/client.

    ```bash
    cd angular-cosmosdb
    ```

3. Затем откройте папку в Visual Studio Code.

    ```bash
    code .
    ```

## <a name="build-the-app-using-the-express-framework"></a>Создание приложения с помощью платформы Express

1. В Visual Studio Code в области **Обозреватель** щелкните правой кнопкой мыши папку **src**, выберите пункт **Создать папку** и присвойте новой папке имя *server*.

2. В области **Обозреватель** щелкните правой кнопкой мыши папку **server**, выберите **Создать файл** и присвойте новому файлу имя *index.js*.

3. Вернитесь в командную строку и введите указанную ниже команду, чтобы установить средство синтаксического анализа текста. Это поможет нашему приложению проанализировать данные JSON, передаваемые через API-интерфейсы.

    ```bash
    npm i express body-parser --save
    ```

4. В Visual Studio Code скопируйте приведенный ниже код в файл index.js. Этот код выполняет следующие действия:
    * ссылается на платформу Express;
    * вызывает средство синтаксического анализа текста для чтения данных JSON в тексте запросов;
    * использует встроенную функцию path;
    * задает переменные для корневого каталога, чтобы было проще найти расположение кода;
    * задает порт;
    * запускает платформу Express;
    * указывает приложению, как следует использовать ПО промежуточного слоя, которое будет применяться для обслуживания сервера;
    * отображает все содержимое папки dist (статическое содержимое);
    * обслуживает приложение и отображает файл index.html для запросов GET, не найденных на сервере (для прямых ссылок);
    * запускает сервер с помощью команды app.listen;
    * использует стрелочную функцию для проверки активности порта.
    
   ```node
   const express = require('express');
   const bodyParser = require('body-parser');
   const path = require('path');
   const routes = require('./routes');

   const root = './';
   const port = process.env.PORT || '3000';
   const app = express();

   app.use(bodyParser.json());
   app.use(bodyParser.urlencoded({ extended: false }));
   app.use(express.static(path.join(root, 'dist')));
   app.use('/api', routes);
   app.get('*', (req, res) => {
     res.sendFile('dist/index.html', {root});
   });

   app.listen(port, () => console.log(`API running on localhost:${port}`));
   ```

5. В Visual Studio Code в области **Обозреватель** щелкните правой кнопкой мыши папку **server** и выберите пункт **Создать файл**. Присвойте новому файлу имя *routes.js*. 

6. Скопируйте приведенный ниже код в файл **routes.js**. Этот код выполняет следующие действия:
   * ссылается на маршрутизатор Express;
   * получает элементы hero;
   * отправляет данные JSON для определенного элемента hero.

   ```node
   const express = require('express');
   const router = express.Router();

   router.get('/heroes', (req, res) => {
    res.send(200, [
       {"id": 10, "name": "Starlord", "saying": "oh yeah"}
    ])
   });

   module.exports=router;
   ```

7. Сохраните все измененные файлы. 

8. В Visual Studio Code нажмите кнопку **отладки** ![значок отладки в Visual Studio Code](./media/tutorial-develop-mongodb-nodejs-part2/debug-button.png), нажмите кнопку шестеренки ![значок шестеренки в Visual Studio Code](./media/tutorial-develop-mongodb-nodejs-part2/gear-button.png) и выберите **Node.js**, чтобы создать конфигурацию.

   В Visual Studio Code откроется новый файл launch.json.

8. В 11-й строке файла launch.json замените `"program": "${file}"` на `"program": "${workspaceRoot}/src/server/index.js"` и сохраните файл.

9. Нажмите кнопку **Начать отладку** ![значок отладки в Visual Studio Code](./media/tutorial-develop-mongodb-nodejs-part2/start-debugging-button.png), чтобы запустить приложение.

    Приложение должно запуститься без ошибок.

## <a name="use-postman-to-test-the-app"></a>Тестирование приложения с помощью Postman

1. Теперь откройте Postman и введите `http://localhost:3000/api/heroes` в поле GET (Получить). 

2. Нажмите кнопку **Send** (Отправить) и получите от приложения ответ в формате JSON. 

    Этот ответ показывает, что приложение работает локально. 

    ![Запрос и ответ в Postman](./media/tutorial-develop-mongodb-nodejs-part2/azure-cosmos-db-postman.png)


## <a name="next-steps"></a>Дополнительная информация

В этой части руководства мы выполнили следующую задачу:

> [!div class="checklist"]
> * создали проект Node.js с помощью Angular CLI;
> * тестировали приложение с помощью Postman.

Теперь можно перейти к следующей части руководства, чтобы создать пользовательский интерфейс.

> [!div class="nextstepaction"]
> [Создание пользовательского интерфейса с помощью Angular](tutorial-develop-mongodb-nodejs-part3.md)

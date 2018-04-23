---
title: Создание веб-приложения Node.js для Azure Cosmos DB | Документы Майкрософт
description: В этом руководстве по Node.js описывается использование Microsoft Azure Cosmos DB для хранения данных и доступа к ним из веб-приложения Node.js Express, размещенного на веб-сайтах Azure.
keywords: Разработка приложений, учебник по базе данных, изучение node.js, учебник по node.js
services: cosmos-db
documentationcenter: nodejs
author: SnehaGunda
manager: kfile
ms.assetid: 9da9e63b-e76a-434e-96dd-195ce2699ef3
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 03/23/2018
ms.author: sngun
ms.openlocfilehash: 6a7d1b961245a47015bdb96fd8653d04586238b3
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2018
---
# <a name="_Toc395783175"></a>Создание веб-приложения Node.js с использованием Azure Cosmos DB
> [!div class="op_single_selector"]
> * [.NET](sql-api-dotnet-application.md)
> * [Node.js](sql-api-nodejs-application.md)
> * [Java](sql-api-java-application.md)
> * [Python](sql-api-python-application.md)
> 
> 

В этом руководстве по Node.js показано, как использовать API Azure Cosmos DB и API SQL для хранения данных и обеспечения доступа к ним из приложения Node.js Express, размещенного на веб-сайтах Azure. Вы создадите простое веб-приложение для управления задачами (приложение ToDo), позволяющее создавать, извлекать и выполнять задачи. Задачи будут храниться в виде документов JSON в Azure Cosmos DB. В этом руководстве описано, как создать и развернуть приложение, а также объясняется каждый фрагмент кода.

![Снимок экрана приложения "Мой список дел", созданного с помощью этого учебника](./media/sql-api-nodejs-application/cosmos-db-node-js-mytodo.png)

Нет времени на изучение учебника, и вы просто хотите получить готовое решение? Хорошо, получите полный пример решения на сайте [GitHub][GitHub]. Инструкции по запуску приложения см. в [файле сведений](https://github.com/Azure-Samples/documentdb-node-todo-app/blob/master/README.md).

## <a name="_Toc395783176"></a>Предварительные требования
> [!TIP]
> Этот учебник по Node.js разработан для читателей, обладающих определенным опытом использования Node.js и веб-сайтов Azure.
> 
> 

Перед выполнением инструкций, приведенных в этой статье, следует убедиться, что установлены следующие компоненты:

* Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу. 

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [Node.js][Node.js] версии 0.10.29 или более поздней. Корпорация Майкрософт рекомендует использовать Node.js 6.10 или более поздней версии.
* [Генератор Express](http://www.expressjs.com/starter/generator.html) (его можно установить через `npm install express-generator -g`).
* [Git][Git].

## <a name="_Toc395637761"></a>Шаг 1. Создание учетной записи базы данных Azure Cosmos DB
Давайте сначала создадим учетную запись Azure Cosmos DB. Если у вас уже есть учетная запись или вы используете эмулятор Azure Cosmos DB в этом руководстве, можно перейти к разделу [Шаг 2. Создание приложения Node.js](#_Toc395783178).

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

[!INCLUDE [cosmos-db-keys](../../includes/cosmos-db-keys.md)]

## <a name="_Toc395783178"></a>Шаг 2. Создание приложения Node.js
Теперь создадим базовый проект Node.js «Привет, мир!» с помощью платформы [Express](http://expressjs.com/) .

1. Откройте предпочитаемый терминал, например командную строку Node.js.
2. Перейдите в каталог, в котором будет сохранено новое приложение.
3. С помощью генератора Express создайте новое приложение с именем **todo**.
   
        express todo
4. Откройте новый каталог **todo** и установите зависимости.
   
        cd todo
        npm install
5. Запустите новое приложение.
   
        npm start
6. Новое приложение можно просмотреть, перейдя в браузере по адресу [http://localhost:3000](http://localhost:3000).
   
    ![Изучение Node.js — снимок экрана приложения "Привет, мир" в окне браузера](./media/sql-api-nodejs-application/cosmos-db-node-js-express.png)

    Затем, чтобы остановить приложение, нажмите клавиши CTRL+C в окне терминала, затем щелкните клавишу **Y** для завершения пакетного задания (только на компьютерах Windows).

## <a name="_Toc395783179"></a>Шаг 3. Установка дополнительных модулей
Файл **package.json** является одним из файлов, создаваемых в корневой папке проекта. Этот файл содержит список дополнительных модулей, необходимых для приложения Node.js. Позднее, при развертывании этого приложения на веб-сайтах Azure, этот файл будет использоваться для определения модулей, которые должны быть установлены в Azure для поддержки вашего приложения. Для этого учебного примера нам нужно установить еще два пакета.

1. Вернувшись в терминал, установите модуль **async** с помощью npm.
   
        npm install async --save
2. Установите модуль **documentdb** с помощью npm. В этом модуле заключена вся "магия" Azure Cosmos DB.
   
        npm install documentdb --save

## <a name="_Toc395783180"></a>Шаг 4. Использование службы Azure Cosmos DB в приложении Node
После завершения начальной установки и настройки давайте перейдем к собственно написанию кода приложения, использующего Azure Cosmos DB.

### <a name="create-the-model"></a>Создание модели
1. В каталоге проекта создайте каталог с именем **models** в том же каталоге, что и файл package.json.
2. В каталоге **models** создайте файл с именем **task-model.js**. Этот файл будет содержать модель для задач, создаваемых нашим приложением.
3. В том же каталоге **models** создайте еще один новый файл с именем **cosmosdb-manager.js**. Этот файл будет содержать некоторый полезный многократно используемый код, который будет задействован на протяжении нашего приложения. 
4. Скопируйте следующий код в файл **cosmosdb-manager.js**
    ```nodejs
    let DocumentDBClient = require('documentdb').DocumentClient;

    module.exports = {
    getOrCreateDatabase: (client, databaseId, callback) => {
        let querySpec = {
        query: 'SELECT * FROM root r WHERE r.id = @id',
        parameters: [{ name: '@id', value: databaseId }]
        };

        client.queryDatabases(querySpec).toArray((err, results) => {
        if (err) {
            callback(err);
        } else {
            if (results.length === 0) {
            let databaseSpec = { id: databaseId };
            client.createDatabase(databaseSpec, (err, created) => {
                callback(null, created);
            });
            } else {
            callback(null, results[0]);
            }
        }
        });
    },

    getOrCreateCollection: (client, databaseLink, collectionId, callback) => {
        let querySpec = {
        query: 'SELECT * FROM root r WHERE r.id=@id',
        parameters: [{ name: '@id', value: collectionId }]
        };

        client.queryCollections(databaseLink, querySpec).toArray((err, results) => {
        if (err) {
            callback(err);
        } else {
            if (results.length === 0) {
            let collectionSpec = { id: collectionId };
            client.createCollection(databaseLink, collectionSpec, (err, created) => {
                callback(null, created);
            });
            } else {
            callback(null, results[0]);
            }
        }
        });
    }
    };
    ```
5. Сохраните и закройте файл **cosmosdb-manager.js**.
6. В начале файла **task-model.js** добавьте следующий код для обращения к созданным выше файлам **DocumentDBClient** и **cosmosdb-manager.js**: 

    ```nodejs
    let DocumentDBClient = require('documentdb').DocumentClient;
    let docdbUtils = require('./cosmosdb-manager.js');
    ```
7. Далее будет добавлен код для определения и экспорта объекта Task. Этот код отвечает за инициализацию нашего объекта Task и настройку используемых базы данных и коллекции документов.  

    ```nodejs
    function TaskModel(documentDBClient, databaseId, collectionId) {
      this.client = documentDBClient;
      this.databaseId = databaseId;
      this.collectionId = collectionId;
   
      this.database = null;
      this.collection = null;
    }
   
    module.exports = TaskModel;
    ```
8. Затем добавьте следующий код, чтобы определить дополнительные методы в объекте Task, обеспечивающие взаимодействие с данными, хранящимися в Azure Cosmos DB.

    ```nodejs
    let DocumentDBClient = require('documentdb').DocumentClient;
    let docdbUtils = require('./cosmosdb-manager');

    function TaskModel(documentDBClient, databaseId, collectionId) {
    this.client = documentDBClient;
    this.databaseId = databaseId;
    this.collectionId = collectionId;

    this.database = null;
    this.collection = null;
    }

    TaskModel.prototype = {
    init: function(callback) {
        let self = this;

        docdbUtils.getOrCreateDatabase(self.client, self.databaseId, function(err, db) {
        if (err) {
            callback(err);
        } else {
            self.database = db;
            docdbUtils.getOrCreateCollection(self.client, self.database._self, self.collectionId, function(err, coll) {
            if (err) {
                callback(err);
            } else {
                self.collection = coll;
            }
            });
        }
        });
    },

    find: function(querySpec, callback) {
        let self = this;

        self.client.queryDocuments(self.collection._self, querySpec).toArray(function(err, results) {
        if (err) {
            callback(err);
        } else {
            callback(null, results);
        }
        });
    },

    addItem: function(item, callback) {
        let self = this;

        item.date = Date.now();
        item.completed = false;

        self.client.createDocument(self.collection._self, item, function(err, doc) {
        if (err) {
            callback(err);
        } else {
            callback(null, doc);
        }
        });
    },

    updateItem: function(itemId, callback) {
        let self = this;

        self.getItem(itemId, function(err, doc) {
        if (err) {
            callback(err);
        } else {
            doc.completed = true;

            self.client.replaceDocument(doc._self, doc, function(err, replaced) {
            if (err) {
                callback(err);
            } else {
                callback(null, replaced);
            }
            });
        }
        });
    },

    getItem: function(itemId, callback) {
        let self = this;
        let querySpec = {
        query: 'SELECT * FROM root r WHERE r.id = @id',
        parameters: [{ name: '@id', value: itemId }]
        };

        self.client.queryDocuments(self.collection._self, querySpec).toArray(function(err, results) {
        if (err) {
            callback(err);
        } else {
            callback(null, results[0]);
        }
        });
    }
    };

    module.exports = TaskModel;
    ```
9. Сохраните и закройте файл **task-model.js**. 

### <a name="create-the-controller"></a>Создание контроллера
1. В каталоге проекта **routes** создайте новый файл с именем **tasklist.js**. 
2. Добавьте в **tasklist.js**следующий код. Он загружает модули DocumentDBClient и async, используемые файлом **tasklist.js**. Он также определяет функцию **TaskList**, передающую экземпляр объекта **Task**, заданного ранее:
   
    ```nodejs
    let DocumentDBClient = require('documentdb').DocumentClient;
    let async = require('async');

    function TaskList(taskModel) {
    this.taskModel = taskModel;
    }

    module.exports = TaskList;
    ```
3. Продолжайте добавлять в файл **tasklist.js** методы, используемые для **showTasks, addTask** и **completeTasks**:
   
   ```nodejs
    TaskList.prototype = {
    showTasks: function(req, res) {
        let self = this;

        let querySpec = {
        query: 'SELECT * FROM root r WHERE r.completed=@completed',
        parameters: [
            {
            name: '@completed',
            value: false
            }
        ]
        };

        self.taskModel.find(querySpec, function(err, items) {
        if (err) {
            throw err;
        }

        res.render('index', {
            title: 'My ToDo List ',
            tasks: items
        });
        });
    },

    addTask: function(req, res) {
        let self = this;
        let item = req.body;

        self.taskModel.addItem(item, function(err) {
        if (err) {
            throw err;
        }

        res.redirect('/');
        });
    },

    completeTask: function(req, res) {
        let self = this;
        let completedTasks = Object.keys(req.body);

        async.forEach(
        completedTasks,
        function taskIterator(completedTask, callback) {
            self.taskModel.updateItem(completedTask, function(err) {
            if (err) {
                callback(err);
            } else {
                callback(null);
            }
            });
        },
        function goHome(err) {
            if (err) {
            throw err;
            } else {
            res.redirect('/');
            }
        }
        );
    }
    };
    ```        
4. Сохраните и закройте файл **tasklist.js** .

### <a name="add-configjs"></a>Добавление config.js
1. В каталоге проекта создайте новый файл с именем **config.js**.
2. Добавьте в файл **config.js**следующий код. Он определяет значения и параметры конфигурации, необходимые нашему приложению.
   
    ```nodejs
    let config = {}
   
    config.host = process.env.HOST || "[the URI value from the Azure Cosmos DB Keys page on http://portal.azure.com]";
    config.authKey = process.env.AUTH_KEY || "[the PRIMARY KEY value from the Azure Cosmos DB Keys page on http://portal.azure.com]";
    config.databaseId = "ToDoList";
    config.collectionId = "Items";
   
    module.exports = config;
    ```
3. В файле **config.js** замените значения HOST и AUTH_KEY значениями, найденными на странице "Ключи" учетной записи Azure Cosmos DB на [портале Microsoft Azure](https://portal.azure.com).
4. Сохраните и закройте файл **config.js** .

### <a name="modify-appjs"></a>Изменение app.js
1. В каталоге проекта откройте файл **app.js** . Этот файл был создан ранее, при создании веб-приложения Express.
2. Добавьте следующий код в начало файла **app.js**
   
    ```nodejs
    var DocumentDBClient = require('documentdb').DocumentClient;
    var config = require('./config');
    var TaskList = require('./routes/tasklist');
    var TaskModel = require('./models/taskModel');
    ```
3. Этот код определяет используемый файл конфигурации, продолжая считывать из него значения в переменные, которыми мы вскоре воспользуемся.
4. Замените следующие две строки в файле **app.js** :
   
    ```nodejs
    app.use('/', index);
    app.use('/users', users); 
    ```
   
    следующим фрагментом:
   
    ```nodejs
    let docDbClient = new DocumentDBClient(config.host, {
        masterKey: config.authKey
    });
    let taskModel = new TaskModel(docDbClient, config.databaseId, config.collectionId);
    let taskList = new TaskList(taskModel);
    taskModel.init();
   
    app.get('/', taskList.showTasks.bind(taskList));
    app.post('/addtask', taskList.addTask.bind(taskList));
    app.post('/completetask', taskList.completeTask.bind(taskList));
    app.set('view engine', 'jade');
    ```
5. Эти строки определяют новый экземпляр нашего объекта **TaskModel** с новым подключением к Azure Cosmos DB (с использованием значений, считанных из файла **config.js**), инициализируют объект задачи, а затем привязывают действия формы к методам в нашем контроллере **TaskList**. 
6. Наконец, сохраните и закройте файл **app.js**. Мы почти закончили.

## <a name="_Toc395783181"></a>Шаг 5. Построение пользовательского интерфейса
Теперь давайте обратим наше внимание на создание пользовательского интерфейса, чтобы пользователь смог взаимодействовать с нашим приложением. Созданное нами приложение Express использует в качестве обработчика представлений **Jade**. Дополнительные сведения о Jade см. на сайте [http://jade-lang.com/](http://jade-lang.com/).

1. Файл **layout.jade** в каталоге **views** используется как глобальный шаблон для других файлов **.jade**. На этом шаге он будет изменен для использования [Twitter Bootstrap](https://github.com/twbs/bootstrap)— набора средств, упрощающих разработку привлекательного веб-сайта. 
2. Откройте файл **layout.jade**, расположенный в папке **views**, и замените его содержимое следующим.

    ```
    doctype html
    html
      head
        title= title
        link(rel='stylesheet', href='//ajax.aspnetcdn.com/ajax/bootstrap/3.3.2/css/bootstrap.min.css')
        link(rel='stylesheet', href='/stylesheets/style.css')
      body
        nav.navbar.navbar-inverse.navbar-fixed-top
          div.navbar-header
            a.navbar-brand(href='#') My Tasks
        block content
        script(src='//ajax.aspnetcdn.com/ajax/jQuery/jquery-1.11.2.min.js')
        script(src='//ajax.aspnetcdn.com/ajax/bootstrap/3.3.2/bootstrap.min.js')
    ```

    Он позволяет эффективно сообщить подсистеме **Jade** о том, что необходимо выполнить прорисовку HTML-кода для нашего приложения, и создает **block** с именем **content**, где можно указать разметку для страниц содержимого.

    Сохраните и закройте файл **layout.jade** .

3. Теперь откройте файл **index.jade** — представление, которое будет использоваться нашим приложением, — и замените содержимое файла следующим:
   
        extends layout
        block content
           h1 #{title}
           br
        
           form(action="/completetask", method="post")
             table.table.table-striped.table-bordered
               tr
                 td Name
                 td Category
                 td Date
                 td Complete
               if (typeof tasks === "undefined")
                 tr
                   td
               else
                 each task in tasks
                   tr
                     td #{task.name}
                     td #{task.category}
                     - var date  = new Date(task.date);
                     - var day   = date.getDate();
                     - var month = date.getMonth() + 1;
                     - var year  = date.getFullYear();
                     td #{month + "/" + day + "/" + year}
                     td
                       input(type="checkbox", name="#{task.id}", value="#{!task.completed}", checked=task.completed)
             button.btn.btn-primary(type="submit") Update tasks
           hr
           form.well(action="/addtask", method="post")
             .form-group
               label(for="name") Item Name:
               input.form-control(name="name", type="textbox")
             .form-group
               label(for="category") Item Category:
               input.form-control(name="category", type="textbox")
             br
             button.btn(type="submit") Add item
   

Этот код дополняет макет и предоставляет содержимое для заполнителя **content**, который мы ранее видели в файле **layout.jade**.
   
В макете мы создали две формы HTML.

Первая форма содержит таблицу для наших данных и кнопку, позволяющую обновлять элементы путем POST-запроса к методу **/completetask** нашего контроллера.
    
Вторая форма содержит два поля ввода и кнопку, позволяющую создать новый элемент путем POST-запроса к методу **/addtask** нашего контроллера.

Этого должно быть достаточно для работы нашего приложения.

## <a name="_Toc395783181"></a>Шаг 6. Локальный запуск приложения
1. Чтобы протестировать приложение на локальном компьютере, выполните `npm start` в терминале для запуска приложения, а затем обновите в браузере страницу [http://localhost:3000](http://localhost:3000). Страница должна выглядеть так, как на рисунке ниже:
   
    ![Снимок экрана приложения «Мой список дел» в окне браузера](./media/sql-api-nodejs-application/cosmos-db-node-js-localhost.png)

    > [!TIP]
    > Если появляется сообщение об ошибке отступа в файле layout.jade или index.jade, выровняйте две первые строки в этих двух файлах по левому краю без пробелов. Если перед первыми двумя строками есть пробелы, удалите их, сохраните оба файла, а затем обновите окно браузера. 

2. Используйте поля "Элемент", "Имя элемента" и "Категория", чтобы указать новую задачу, а затем щелкните **Добавить элемент**. Будет создан документ в Azure Cosmos DB с заданными свойствами. 
3. Страница должна обновиться, чтобы отобразить только что созданный элемент в списке ToDo.
   
    ![Снимок экрана приложения с новым элементом в списке дел](./media/sql-api-nodejs-application/cosmos-db-node-js-added-task.png)
4. Чтобы завершить задачу, просто установите флажок в столбце "Завершено" и нажмите кнопку **Обновить задачи**. После этого созданный документ будет обновлен и удален из представления.

5. Чтобы остановить приложение, нажмите клавиши CTRL + C в окне терминала и нажмите клавишу **Y** для завершения пакетного задания.

## <a name="_Toc395783182"></a>Шаг 7. Развертывание проекта приложения на веб-сайтах Azure
1. Если это еще не сделано, включите репозиторий git для веб-сайта Azure. Соответствующие инструкции см. в статье [Развертывание локального репозитория Git в службе приложений Azure](../app-service/app-service-deploy-local-git.md).
2. Добавьте веб-сайт Azure в качестве удаленного репозитория git.
   
        git remote add azure https://username@your-azure-website.scm.azurewebsites.net:443/your-azure-website.git
3. Выполните развертывание методом Push на удаленный репозиторий.
   
        git push azure master
4. Через несколько секунд Visual Studio завершит публикацию вашего веб-приложения и запустит браузер, где вы увидите свое творение, запущенное в Azure!

    Поздравляем! Вы только что создали свое первое веб-приложение Node.js Express с использованием Azure Cosmos DB и опубликовали его на веб-сайтах Azure.

    Если вы хотите скачать полный пример приложения или сослаться на него во время работы с этим руководством, файл примера приложения находится в репозитории [GitHub][GitHub].

## <a name="_Toc395637775"></a>Дальнейшие действия

* Хотите выполнять проверку масштабирования и производительности с помощью Azure Cosmos DB? Дополнительные сведения см. в статье о [проверке производительности и масштабирования с помощью Azure Cosmos DB](performance-testing.md).
* Узнайте, как выполнять [мониторинг учетной записи Azure Cosmos DB](monitor-accounts.md).
* Отправьте запросы образцу набора данных в [Площадке для запросов](https://www.documentdb.com/sql/demo).
* Изучите [документацию по базе данных Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/).

[Node.js]: http://nodejs.org/
[Git]: http://git-scm.com/
[GitHub]: https://github.com/Azure-Samples/documentdb-node-todo-app


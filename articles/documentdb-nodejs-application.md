<properties 
	pageTitle="Создание веб-приложения Node.js с использованием DocumentDB | Azure" 
	description="Узнайте, как использовать Microsoft Azure DocumentDB для хранения данных и доступа к ним из веб-приложения Node.js Express, размещенного в службе Веб-сайты Azure." 
	services="documentdb" 
	documentationCenter="nodejs" 
	authors="ryancrawcour" 
	manager="jhubbard" 
	editor="cgronlun"/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="nodejs" 
	ms.topic="hero-article" 
	ms.date="03/20/2015" 
	ms.author="ryancraw"/>

# <a name="_Toc395783175"></a>Создание веб-приложения Node.js с использованием DocumentDB

В этом учебнике показано, как использовать службу Azure DocumentDB для хранения данных и доступа к ним из приложения Node.js Express, размещенного в службе Веб-сайты Azure.

Прежде чем приступить к работе, рекомендуется просмотреть следующий видеоролик, где Эндрю Лю (Andrew Liu) показывает, как подготовить учетную запись базы данных Azure DocumentDB и хранить документы JSON в приложении Node.js. 

> [AZURE.VIDEO azure-demo-getting-started-with-azure-documentdb-on-nodejs-in-linux]

Затем вернитесь в эту статью, из которой вы узнаете ответы на следующие вопросы:

- Как работать с DocumentDB, используя модуль npm documentdb?
- Как развернуть веб-приложение в службе Веб-сайты Azure?

Руководствуясь этим учебником, вы создадите простое веб-приложение
для управления задачами, позволяющее создавать, извлекать и
выполнять задачи. Эти задачи будут храниться в виде документов JSON в Azure
DocumentDB.

![Screen shot of the My Todo List application created in this tutorial](./media/documentdb-nodejs-application/image1.png)

Вам не хватает времени на прохождение учебника, и вы хотите просто получить готовое решение из GitHub? Никаких проблем, оно находится [здесь](https://github.com/Azure/azure-documentdb-node/tree/master/tutorial/todo).

## <a name="_Toc395783176"></a>Предварительные требования

> [AZURE.TIP] В данном учебнике предполагается, что у вас есть некоторый опыт использования Node.js и службы Веб-сайты Azure.

Перед выполнением инструкций, приведенных в данной статье, убедитесь,
что имеются следующие компоненты:

- Активная учетная запись Azure. Если ее нет, можно создать бесплатную пробную учетную запись всего за несколько минут. Дополнительные сведения см. в разделе [Бесплатная пробная версия Azure](../../pricing/free-trial/).
- [Node.js][] версии v0.10.29 или более поздней.
- [Генератор Express](http://www.expressjs.com/starter/generator.html) (его можно установить через `npm install express-generator -g`)
- [Git][].

## <a name="_Toc395637761"></a>Шаг 1. Создание учетной записи базы данных DocumentDB

Давайте начнем с создания учетной записи DocumentDB. Если у вас уже есть учетная запись, перейдите к подразделу [Шаг 2. Создание нового приложения Node.js](#_Toc395783178).

[AZURE.INCLUDE [documentdb-create-dbaccount](../includes/documentdb-create-dbaccount.md)]

[AZURE.INCLUDE [documentdb-keys](../includes/documentdb-keys.md)]

## <a name="_Toc395783178"></a>Шаг 2. Создание нового приложения Node.js

А теперь создадим простейший проект Hello World на базе Node.js, используя платформу [Express](http://expressjs.com/).

1. Откройте привычный терминал.

2. Используйте генератор Express для создания нового приложения с именем **todo**.

		express todo

3. Откройте новый каталог **todo** и установите зависимости.

		cd todo
		npm install

4. Запустите новое приложение.

		npm start

5. Для просмотра нового приложения можно перейти по адресу [http://localhost:3000](http://localhost:3000) в браузере.

	![Screenshot of the Hello World application in a browser window](./media/documentdb-nodejs-application/image12.png)

## <a name="_Toc395783179"></a>Шаг 3. Установка дополнительных модулей

Среди файлов в корневой папке проекта находится файл **package.json**.
. Этот файл содержит список дополнительных модулей,
необходимых вашему приложению Node.js. Позднее, при развертывании этого
приложения на веб-сайте Azure, этот файл будет использоваться, чтобы определить,
какие модули должны быть установлены в Azure для поддержки приложения. В рамках этого учебника нам требуется установить еще два пакета.

1. Вернитесь в терминал и установите модуль **async** через npm.

		npm install async --save

1. Установите модуль **documentdb** через npm. Именно этот модуль отвечает за "магию" DocumentDB.

		npm install documentdb --save

3. Быстрая проверка файла **package.json** приложения должна помочь выявить дополнительные модули. Этот файл сообщает Azure, какие пакеты следует скачать и установить при запуске вашего приложения. Это должно быть похоже на приведенный ниже пример.

	![Screenshot of the package.json tab](./media/documentdb-nodejs-application/image17.png)

       Этот код сообщает Node (а затем и Azure), что ваше приложение зависит от этих дополнительных модулей.

## <a name="_Toc395783180"></a>Шаг 4. Использование службы DocumentDB в приложении Node

После завершения начальной установки и настройки, давайте перейдем к, собственно, написанию кода приложения, использующего Azure DocumentDB.

### Создание модели

1. В каталоге проекта создайте новый каталог с именем **models**.
2. В каталоге **models** создайте файл с именем **taskDao.js**. Этот файл будет содержать модель для задач, создаваемых вашим приложением.
3. В том же каталоге **models** создайте другой файл с именем **docdbUtils.js**. Этот файл будет содержать полезный код для многократного использования в разных частях приложения. 
4. Скопируйте следующий код в файл **docdbUtils.js**.

		var DocumentDBClient = require('documentdb').DocumentClient;
			
		var DocDBUtils = {
		    getOrCreateDatabase: function (client, databaseId, callback) {
		        var querySpec = {
		            query: 'SELECT * FROM root r WHERE r.id=@id',
		            parameters: [{
		                name: '@id',
		                value: databaseId
		            }]
		        };
		
		        client.queryDatabases(querySpec).toArray(function (err, results) {
		            if (err) {
		                callback(err);
		
		            } else {
		                if (results.length === 0) {
		                    var databaseSpec = {
		                        id: databaseId
		                    };
		
		                    client.createDatabase(databaseSpec, function (err, created) {
		                        callback(null, created);
		                    });
		
		                } else {
		                    callback(null, results[0]);
		                }
		            }
		        });
		    },
		
		    getOrCreateCollection: function (client, databaseLink, collectionId, callback) {
		        var querySpec = {
		            query: 'SELECT * FROM root r WHERE r.id=@id',
		            parameters: [{
		                name: '@id',
		                value: collectionId
		            }]
		        };
		        
		        client.queryCollections(databaseLink, querySpec).toArray(function (err, results) {
		            if (err) {
		                callback(err);
		
		            } else {		
		                if (results.length === 0) {
		                    var collectionSpec = {
		                        id: collectionId
		                    };
		
		                    client.createCollection(databaseLink, collectionSpec, function (err, created) {
		                        callback(null, created);
		                    });
		
		                } else {
		                    callback(null, results[0]);
		                }
		            }
		        });
		    }
		};
				
		module.exports = DocDBUtils;
		
3. Сохраните и закройте файл **docdbUtils.js**.

4. В начале файла **taskDao.js** добавьте следующий код, чтобы задать ссылку на созданные ранее файлы **DocumentDBClient** и **docdbUtils.js**:

        var DocumentDBClient = require('documentdb').DocumentClient;
		var docdbUtils = require('./docdbUtils');

4. Далее будет добавлен код для определения и экспорта объекта Task. Он отвечает за инициализацию нашего объекта Task и настройку базы данных и коллекции документов, которые мы будем использовать.

		function TaskDao(documentDBClient, databaseId, collectionId) {
		  this.client = documentDBClient;
		  this.databaseId = databaseId;
		  this.collectionId = collectionId;
		
		  this.database = null;
		  this.collection = null;
		}
		
		module.exports = TaskDao;

5. Затем добавьте следующий код, чтобы определить дополнительные методы для объекта Task, обеспечивающего взаимодействие с данными, хранящимися в DocumentDB.

		TaskDao.prototype = {
		    init: function (callback) {
		        var self = this;
		
		        docdbUtils.getOrCreateDatabase(self.client, self.databaseId, function (err, db) {
		            if (err) {
		                callback(err);

		            } else {
		                self.database = db;
		                docdbUtils.getOrCreateCollection(self.client, self.database._self, self.collectionId, function (err, coll) {
		                    if (err) {
		                        callback(err);
		
		                    } else {
		                        self.collection = coll;
		                    }
		                });
		            }
		        });
		    },
		
		    find: function (querySpec, callback) {
		        var self = this;
		
		        self.client.queryDocuments(self.collection._self, querySpec).toArray(function (err, results) {
		            if (err) {
		                callback(err);
		
		            } else {
		                callback(null, results);
		            }
		        });
		    },
		
		    addItem: function (item, callback) {
		        var self = this;
		
		        item.date = Date.now();
		        item.completed = false;
		
		        self.client.createDocument(self.collection._self, item, function (err, doc) {
		            if (err) {
		                callback(err);
		
		            } else {
		                callback(null, doc);
		            }
		        });
		    },
		
		    updateItem: function (itemId, callback) {
		        var self = this;
		
		        self.getItem(itemId, function (err, doc) {
		            if (err) {
		                callback(err);
		
		            } else {
		                doc.completed = true;
		
		                self.client.replaceDocument(doc._self, doc, function (err, replaced) {
		                    if (err) {
		                        callback(err);
		
		                    } else {
		                        callback(null, replaced);
		                    }
		                });
		            }
		        });
		    },
		
		    getItem: function (itemId, callback) {
		        var self = this;
		
		        var querySpec = {
		            query: 'SELECT * FROM root r WHERE r.id=@id',
		            parameters: [{
		                name: '@id',
		                value: itemId
		            }]
		        };
		
		        self.client.queryDocuments(self.collection._self, querySpec).toArray(function (err, results) {
		            if (err) {
		                callback(err);
		
		            } else {
		                callback(null, results[0]);
		            }
		        });
		    }
		};

6. Сохраните и закройте файл **taskDao.js**. 

### Создание контроллера

1. В каталоге **routes** проекта создайте файл  с именем **tasklist.js**. 
2. Добавьте следующий код в файл **tasklist.js**. Он загружает модули DocumentDBClient и async, используемые файлом **tasklist.js**. Кроме того, он определяет функцию **TaskList**, в которую передается экземпляр определенного ранее объекта **Task**:

		var DocumentDBClient = require('documentdb').DocumentClient;
		var async = require('async');
		
		function TaskList(taskDao) {
		  this.taskDao = taskDao;
		}
		
		module.exports = TaskList;

3. Продолжайте дополнять файл **tasklist.js**, добавив методы **showTasks, addTask** и **completeTasks**:
		
		TaskList.prototype = {
		    showTasks: function (req, res) {
		        var self = this;
		
		        var querySpec = {
		            query: 'SELECT * FROM root r WHERE r.completed=@completed',
		            parameters: [{
		                name: '@completed',
		                value: false
		            }]
		        };
		
		        self.taskDao.find(querySpec, function (err, items) {
		            if (err) {
		                throw (err);
		            }
		
		            res.render('index', {
		                title: 'My ToDo List ',
		                tasks: items
		            });
		        });
		    },
		
		    addTask: function (req, res) {
		        var self = this;
		        var item = req.body;
		
		        self.taskDao.addItem(item, function (err) {
		            if (err) {
		                throw (err);
		            }
		
		            res.redirect('/');
		        });
		    },
		
		    completeTask: function (req, res) {
		        var self = this;
		        var completedTasks = Object.keys(req.body);
		
		        async.forEach(completedTasks, function taskIterator(completedTask, callback) {
		            self.taskDao.updateItem(completedTask, function (err) {
		                if (err) {
		                    callback(err);
		                } else {
		                    callback(null);
		                }
		            });
		        }, function goHome(err) {
		            if (err) {
		                throw err;
		            } else {
		                res.redirect('/');
		            }
		        });
		    }
		};


4. Сохраните и закройте файл **tasklist.js**.
 
### Добавление config.json

1. В каталоге проекта создайте новый файл с именем **config.js**.
2. Добавьте в файл **config.json** следующий код. Он определяет значения и параметры конфигурации, необходимые нашему приложению.

		var config = {}
		
		config.host = process.env.HOST || "[the URI value from the DocumentDB Keys blade on http://portal.azure.com]";
		config.authKey = process.env.AUTH_KEY || "[the PRIMARY KEY value from the DocumentDB Keys blade on http://portal.azure.com]";
		config.databaseId = "ToDoList";
		config.collectionId = "Items";
		
		module.exports = config;

3. В файле **config.js** обновите значения HOST и AUTH_KEY, используя значения с выноски "Ключи" своей учетной записи DocumentDB на [портале предварительной версии Azure](http://portal.azure.com):

4. Сохраните и закройте файл **config.js**.
 
### Изменение app.js

1. Откройте файл **app.js** в каталоге проекта. Этот файл был создан ранее - при создании веб-приложения Express.
2. Добавьте в начало файла **app.js** следующий код.
	
		var DocumentDBClient = require('documentdb').DocumentClient;
		var config = require('./config');
		var TaskList = require('./routes/tasklist');
		var TaskDao = require('./models/taskDao');

3. Данный код задает используемый файл конфигурации и считывает значения из этого файла в некоторые переменные, которыми мы скоро воспользуемся.
4. Замените две следующих строки в файле **app.js**:

		app.use('/', routes);
		app.use('/users', users); 

      на следующий фрагмент кода:

		var docDbClient = new DocumentDBClient(config.host, {
		    masterKey: config.authKey
		});
		var taskDao = new TaskDao(docDbClient, config.databaseId, config.collectionId);
		var taskList = new TaskList(taskDao);
		taskDao.init();
		
		app.get('/', taskList.showTasks.bind(taskList));
		app.post('/addtask', taskList.addTask.bind(taskList));
		app.post('/completetask', taskList.completeTask.bind(taskList));


6. Эти строки определяют новый экземпляр нашего объекта **TaskDao** с новым подключением к DocumentDB (с использованием считанных из файла **config.js** значений), инициализируют объект задачи и привязывают действия формы к методам нашего контроллера **TaskList**. 

7. Сохраните и закройте файл **app.js**, уже почти все готово.
 
## <a name="_Toc395783181"></a>Шаг 5. Создание пользовательского интерфейса

Теперь давайте обратим наше внимание на создание пользовательского интерфейса, чтобы пользователь смог взаимодействовать с нашим приложением. Созданное нами приложение Express использует **Jade** в качестве обработчика представлений. Дополнительные сведения о Jade см. на сайте [http://jade-lang.com/](http://jade-lang.com/).

1. Файл **layout.jade** в каталоге **views** используется как глобальный шаблон для других файлов **.jade**. На этом шаге он будет изменен для использования [Twitter Bootstrap](https://github.com/twbs/bootstrap) - набора инструментов, упрощающих разработку привлекательного веб-сайта. 
2. Откройте файл **layout.jade** в папке **views** и замените его содержимое следующим кодом:
	
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



	Он позволяет эффективно сообщить подсистеме **Jade** о том, что необходимо выполнить прорисовку HTML-кода для нашего приложения, и создает **block** с именем **content**, где можно указать разметку для страниц содержимого .
	Сохраните и закройте файл **layout.jade**.

4. Теперь откройте файл **index.jade**, представление, которое будет использоваться нашим приложением, и замените содержимое файла следующим кодом:

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
		    button.btn(type="submit") Update tasks
		  hr
		  form.well(action="/addtask", method="post")
		    label Item Name:
		    input(name="name", type="textbox")
		    label Item Category:
		    input(name="category", type="textbox")
		    br
		    button.btn(type="submit") Add item

	Это позволяет расширить разметку и предоставить содержимое для заполнителя **content**, который мы ранее видели в файле **layout.jade**.
	
	В макете мы создали две формы HTML. 
	Первая форма содержит таблицу для наших данных и кнопку, позволяющую обновлять элементы за счет передачи в метод **/completetask** нашего контроллера.
	Вторая форма содержит два поля ввода и кнопку, позволяющую создать новый элемент, выполнив передачу в метод **/addtask** контроллера.
	
	Этого должно быть достаточно для работы нашего приложения.

5. Откройте файл **style.css** в каталоге **public\stylesheets** и замените код следующим:

		body {
		  padding: 50px;
		  font: 14px "Lucida Grande", Helvetica, Arial, sans-serif;
		}
		a {
		  color: #00B7FF;
		}
		.well label {
		  display: block;
		}
		.well input {
		  margin-bottom: 5px;
		}
		.btn {
		  margin-top: 5px;
		  border: outset 1px #C8C8C8;
		}

	Сохраните и закройте файл **style.css**.

## <a name="_Toc395783181"></a>Шаг 6. Локальный запуск приложения

1. Чтобы протестировать приложение на локальной виртуальной машине, запустите `npm start` в терминале для запуска приложения и открытия браузера на странице, аналогичной приведенной ниже:

	![Screenshot of the MyTodo List application in a browser window](./media/documentdb-nodejs-application/image18.png)


2. Введите информацию об элементе, имени элемента и категории в соответствующих полях,
а затем нажмите кнопку **Добавить элемент**.

3. Страница должна обновиться, чтобы отобразить новый элемент в списке дел.
.

	![Screenshot of the application with a new item in the ToDo list](./media/documentdb-nodejs-application/image19.png)

4. Чтобы выполнить задачу, просто установите флажок в столбце "Выполнить"
и нажмите кнопку **Обновить задачи**.

## <a name="_Toc395783182"></a>Шаг 7. Разверните приложение на веб-сайтах Azure

1. Если вы еще не сделали это, включите репозиторий git для своего веб-сайта Azure. Инструкции по выполнению данной процедуры приведены [здесь](web-sites-publish-source-control-git.md#step4).

2. Добавьте свой веб-сайт Azure в качестве git remote.

		git remote add azure https://username@your-azure-website.scm.azurewebsites.net:443/your-azure-website.git

3. Выполните развертывание с помощью принудительной передачи на remote.

		git push azure master

4. Через несколько секунд git завершит публикацию вашего веб-приложения и
запустит браузер, где вы сможете увидеть свое творение,
запущенное в Azure!

## <a name="_Toc395637775"></a>Дальнейшие действия

Поздравляем! Вы только что создали свое первое веб-приложение Node.js Express
с помощью Azure DocumentDB и опубликовали его в службе веб-сайты Azure.

Исходный код для этого примера приложения можно скачать [здесь](https://github.com/Azure/azure-documentdb-node/tree/master/tutorial/todo).

  [Node.js]: http://nodejs.org/
  [Git]: http://git-scm.com/
  [Портал управления Azure]: http://portal.azure.com

<!--HONumber=49-->
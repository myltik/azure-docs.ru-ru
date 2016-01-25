<properties
	pageTitle="Веб-приложение Node.js, использующее службу таблиц Azure"
	description="В этом учебнике объясняется, как использовать службу таблиц Azure для сохранения данных из приложения Node.js, которое размещено в веб-приложениях службы приложений Azure."
	tags="azure-portal"
	services="app-service\web, storage"
	documentationCenter="nodejs"
	authors="rmcmurray"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="storage"
	ms.workload="storage"
	ms.tgt_pltfrm="na"
	ms.devlang="nodejs"
	ms.topic="article"
	ms.date="01/09/2016"
	ms.author="robmcm"/>



# Веб-приложение Node.js, использующее службу таблиц Azure

## Обзор

В этом учебнике показано, как использовать службу таблиц на базе службы управления данными Azure для хранения данных и доступа к ним из приложения [node], размещенного в среде веб-приложений [службы приложений Azure](http://go.microsoft.com/fwlink/?LinkId=529714). Этот учебный курс разработан для читателей, обладающих определенным опытом использования Node и [Git].

Вы узнаете:

* как использовать для установки модулей Node диспетчер npm (node package manager);

* как работать со службой таблиц Azure;

* как использовать Azure CLI для создания веб-приложения.

Руководствуясь этим учебником, вы создадите простое веб-приложение для управления списком дел, позволяющее создавать, извлекать и выполнять задачи. Задачи хранятся в службе таблиц.

Вот готовое приложение:

![Веб-страница, показывающая пустой список задач][node-table-finished]

>[AZURE.NOTE]Чтобы приступить к работе со службой приложений Azure до создания учетной записи Azure, перейдите к разделу [Пробное использование службы приложений](http://go.microsoft.com/fwlink/?LinkId=523751), где вы можете быстро создать кратковременное веб-приложение начального уровня в службе приложений. Никаких кредитных карт и обязательств.


## Предварительные требования

Перед выполнением инструкций, приведенных в этой статье, следует убедиться, что установлены следующие компоненты:

* [node] версии 0.10.24 или выше

* [Git.]


[AZURE.INCLUDE [create-account-and-websites-note](../../includes/create-account-and-websites-note.md)]

## Создайте учетную запись хранения.

Создайте учетную запись хранения Azure. Это приложение использует ее для хранения списка дел.

1.  Войдите на [портал Azure](https://portal.azure.com).

2. Щелкните значок **Создать** в левом нижнем углу страницы, затем выберите **Данные + Хранилище** > **Хранилище**. Присвойте учетной записи хранения уникальное имя и создайте для нее новую [группу ресурсов](../resource-group-overview.md).

  	![Кнопка «Создать»](./media/storage-nodejs-use-table-storage-web-site/configure-storage.png)

	После создания учетной записи хранения на кнопке **Уведомления**загорится зеленым слово **УСПЕШНО** и откроется колонка учетной записи хранения, в которой будет видно, что учетная запись относится к созданной вами группе ресурсов.

5. В колонке учетной записи хранения выберите **Параметры** > **Ключи**. Скопируйте первичный ключ доступа в буфер обмена.

    ![Ключ доступа][portal-storage-access-keys]


##Установка модулей и создание шаблонов

В этом разделе вы создадите новое приложение Node и добавите пакеты модулей с помощью npm. Для этого приложения вы используете модули [Express] и [Azure]. Модуль Express предоставляет платформу Model View Controller (Контроллер представления модели) для Node, а модули Azure предоставляют возможность подключения к службе таблиц.

### Установка модуля Express и формирование шаблонов

1. Из командной строки создайте новый каталог **tasklist** и перейдите в него.  

2. Введите следующую команду для установки модуля Express.

		npm install express-generator@4.2.0 -g

    В зависимости от операционной системы вам может потребоваться указать «sudo» перед командой:

		sudo npm install express-generator@4.2.0 -g

    Выходные данные выглядят следующим образом:

		express-generator@4.2.0 /usr/local/lib/node_modules/express-generator
		├── mkdirp@0.3.5
		└── commander@1.3.2 (keypress@0.1.0)

	> [AZURE.NOTE]Параметр -g указывает на то, что модуль устанавливается глобально. В результате мы сможем использовать **express** для создания шаблонов веб-приложения без необходимости вводить дополнительные сведения о пути.

4. Чтобы создать шаблоны для этого приложения, введите команду **express**:

        express

	Выходные данные этой команды выглядят следующим образом:

		   create : .
		   create : ./package.json
		   create : ./app.js
		   create : ./public
		   create : ./public/images
		   create : ./routes
		   create : ./routes/index.js
		   create : ./routes/users.js
		   create : ./public/stylesheets
		   create : ./public/stylesheets/style.css
		   create : ./views
		   create : ./views/index.jade
		   create : ./views/layout.jade
		   create : ./views/error.jade
		   create : ./public/javascripts
		   create : ./bin
		   create : ./bin/www

		   install dependencies:
		     $ cd . && npm install

		   run the app:
		     $ DEBUG=my-application ./bin/www

	В каталоге **tasklist** появится несколько новых папок и файлов.

### Установка дополнительных модулей

Модуль **express**, в частности, создает файл **package.json**. Этот файл содержит список зависимостей модуля. Позднее, при развертывании приложения в веб-приложениях службы приложений, этот файл будет определять, какие модули должны быть установлены в Azure.

В командной строке введите следующую команду, чтобы установить модули, описанные в файле **package.json**: Может потребоваться использовать «sudo» в командной строке.

    npm install

Выходные данные этой команды выглядят следующим образом:

	debug@0.7.4 node_modules\debug

	cookie-parser@1.0.1 node_modules\cookie-parser
	├── cookie-signature@1.0.3
	└── cookie@0.1.0

    [...]


Теперь введите следующую команду, чтобы установить модули [azure], [node-uuid], [nconf] и [async]\:

	npm install azure-storage node-uuid async nconf --save

Флаг **--save** добавляет записи для этих модулей в файл **package.json**.

Выходные данные этой команды выглядят следующим образом:

	async@0.9.0 node_modules\async

	node-uuid@1.4.1 node_modules\node-uuid

	nconf@0.6.9 node_modules\nconf
	├── ini@1.2.1
	├── async@0.2.9
	└── optimist@0.6.0 (wordwrap@0.0.2, minimist@0.0.10)

	[...]


## Создание приложения

Теперь все готово к созданию приложения.

### Создание модели

*Модель* — это объект, который представляет данные в приложении. В нашем приложении единственной моделью является объект задачи, который представляет собой элемент списка дел. У каждой задачи есть следующие поля:

- PartitionKey
- RowKey
- name (строка)
- category (строка)
- completed (логическое значение)

Поля **PartitionKey** и **RowKey** используются службой таблиц в качестве ключей. Дополнительную информацию см. в разделе [Общие сведения о модели данных службы таблиц](https://msdn.microsoft.com/library/azure/dd179338.aspx).


1. В каталоге **tasklist** создайте новый каталог с именем **models**.

2. В каталоге **models** создайте новый файл с именем **task.js**. Этот файл будет содержать модель для задач, создаваемых приложением.

3. В начале файла **task.js** добавьте следующий код для ссылки на необходимые библиотеки:

        var azure = require('azure-storage');
  		var uuid = require('node-uuid');
		var entityGen = azure.TableUtilities.entityGenerator;

4. Добавьте приведенный ниже код, чтобы определить и экспортировать объект Task. Этот объект отвечает за подключение к таблице.

  		module.exports = Task;

		function Task(storageClient, tableName, partitionKey) {
		  this.storageClient = storageClient;
		  this.tableName = tableName;
		  this.partitionKey = partitionKey;
		  this.storageClient.createTableIfNotExists(tableName, function tableCreated(error) {
		    if(error) {
		      throw error;
		    }
		  });
		};

5. Добавьте следующий код, определяющий для объекта Task дополнительные методы, которые позволяют взаимодействовать с данными, хранящимися в таблице:

		Task.prototype = {
		  find: function(query, callback) {
		    self = this;
		    self.storageClient.queryEntities(this.tableName, query, null, function entitiesQueried(error, result) {
		      if(error) {
		        callback(error);
		      } else {
		        callback(null, result.entries);
		      }
		    });
		  },

		  addItem: function(item, callback) {
		    self = this;
		    // use entityGenerator to set types
			// NOTE: RowKey must be a string type, even though
            // it contains a GUID in this example.
		    var itemDescriptor = {
		      PartitionKey: entityGen.String(self.partitionKey),
		      RowKey: entityGen.String(uuid()),
		      name: entityGen.String(item.name),
		      category: entityGen.String(item.category),
		      completed: entityGen.Boolean(false)
		    };
		    self.storageClient.insertEntity(self.tableName, itemDescriptor, function entityInserted(error) {
		      if(error){  
		        callback(error);
		      }
		      callback(null);
		    });
		  },

		  updateItem: function(rKey, callback) {
		    self = this;
		    self.storageClient.retrieveEntity(self.tableName, self.partitionKey, rKey, function entityQueried(error, entity) {
		      if(error) {
		        callback(error);
		      }
		      entity.completed._ = true;
		      self.storageClient.updateEntity(self.tableName, entity, function entityUpdated(error) {
		        if(error) {
		          callback(error);
		        }
		        callback(null);
		      });
		    });
		  }
		}

6. Сохраните и закройте файл **task.js**.

### Создание контроллера

*Контроллер* обрабатывает HTTP-запросы и отображает HTML-ответ.

1. В каталоге **tasklist/routes** создайте новый файл с именем **tasklist.js** и откройте его в текстовом редакторе.

2. Добавьте в **tasklist.js** следующий код. Он загружает модули azure и async, используемые **tasklist.js**. Он также определяет функцию **TaskList**, передающую экземпляр объекта **Task**, определенного ранее:

		var azure = require('azure-storage');
		var async = require('async');

		module.exports = TaskList;

3. Определите объект **TaskList**.

		function TaskList(task) {
		  this.task = task;
		}


4. Добавьте в объект **TaskList** следующие методы:

		TaskList.prototype = {
		  showTasks: function(req, res) {
		    self = this;
		    var query = new azure.TableQuery()
		      .where('completed eq ?', false);
		    self.task.find(query, function itemsFound(error, items) {
		      res.render('index',{title: 'My ToDo List ', tasks: items});
		    });
		  },

		  addTask: function(req,res) {
		    var self = this
		    var item = req.body.item;
		    self.task.addItem(item, function itemAdded(error) {
		      if(error) {
		        throw error;
		      }
		      res.redirect('/');
		    });
		  },

		  completeTask: function(req,res) {
		    var self = this;
		    var completedTasks = Object.keys(req.body);
		    async.forEach(completedTasks, function taskIterator(completedTask, callback) {
		      self.task.updateItem(completedTask, function itemsUpdated(error) {
		        if(error){
		          callback(error);
		        } else {
		          callback(null);
		        }
		      });
		    }, function goHome(error){
		      if(error) {
		        throw error;
		      } else {
		       res.redirect('/');
		      }
		    });
		  }
		}


### Изменение app.js

1. В каталоге **tasklist** откройте файл **app.js**. Этот файл был создан ранее с помощью команды **express**.

2. В начале файла добавьте следующий код для загрузки модуля azure, задайте имя таблицы, ключ раздела, а также учетные данные хранения, используемые в этом примере:

		var azure = require('azure-storage');
		var nconf = require('nconf');
		nconf.env()
		     .file({ file: 'config.json', search: true });
		var tableName = nconf.get("TABLE_NAME");
		var partitionKey = nconf.get("PARTITION_KEY");
		var accountName = nconf.get("STORAGE_NAME");
		var accountKey = nconf.get("STORAGE_KEY");

	> [AZURE.NOTE]nconf загружает значения конфигурации либо из переменных среды, либо из файла **config.json**, который мы создадим позднее.

3. Прокрутите файл app.js вниз до появления следующей строки:

		app.use('/', routes);
		app.use('/users', users);

	Замените вышеприведенные строки на код, приведенный ниже. Код инициализирует экземпляр <strong>Task</strong>, используя подключение к вашей учетной записи хранения. Он передается в <strong>TaskList</strong>, где будет использоваться для обмена данными со службой таблиц:

		var TaskList = require('./routes/tasklist');
		var Task = require('./models/task');
		var task = new Task(azure.createTableService(accountName, accountKey), tableName, partitionKey);
		var taskList = new TaskList(task);

		app.get('/', taskList.showTasks.bind(taskList));
		app.post('/addtask', taskList.addTask.bind(taskList));
		app.post('/completetask', taskList.completeTask.bind(taskList));

4. Сохраните файл **app.js**.

### Изменение представления индекса

1. Откройте файл **tasklist/views/index.jade** в текстовом редакторе.

2. Замените все содержимое файла следующим кодом. Он определяет представление, в котором отображаются существующие задачи и содержится форма для добавления новых задач и пометки существующих как завершенных.

		extends layout

		block content
		  h1= title
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
		            td #{task.name._}
		            td #{task.category._}
		            - var day   = task.Timestamp._.getDate();
		            - var month = task.Timestamp._.getMonth() + 1;
		            - var year  = task.Timestamp._.getFullYear();
		            td #{month + "/" + day + "/" + year}
		            td
		              input(type="checkbox", name="#{task.RowKey._}", value="#{!task.completed._}", checked=task.completed._)
		    button.btn(type="submit") Update tasks
		  hr
		  form.well(action="/addtask", method="post")
		    label Item Name:
		    input(name="item[name]", type="textbox")
		    label Item Category:
		    input(name="item[category]", type="textbox")
		    br
		    button.btn(type="submit") Add item

3. Сохраните и закройте файл **index.jade**.

### Изменение глобального макета

Файл **layout.jade** в каталоге **views** используется как глобальный шаблон для других **JADE**-файлов. На этом шаге он будет изменен таким образом, чтобы использовать [Twitter Bootstrap](https://github.com/twbs/bootstrap) — набор средств, с помощью которых можно легко создать привлекательный веб-сайт.

Загрузите и извлеките файлы [Twitter Bootstrap](http://getbootstrap.com/). Скопируйте файл **bootstrap.min.css** из папки Bootstrap **css** в каталог **public/stylesheets** своего приложения.

В папке **views** откройте файл **layout.jade** и замените его содержимое на следующее:

	doctype html
	html
	  head
	    title= title
	    link(rel='stylesheet', href='/stylesheets/bootstrap.min.css')
	    link(rel='stylesheet', href='/stylesheets/style.css')
	  body.app
	    nav.navbar.navbar-default
	      div.navbar-header
	      a.navbar-brand(href='/') My Tasks
	    block content

### Создание файла конфигурации

Для локального запуска приложения мы поместим учетные данные службы хранилища Azure в файл конфигурации. Создайте файл **config.json* *со следующим кодом JSON:

	{
		"STORAGE_NAME": "<storage account name>",
		"STORAGE_KEY": "<storage access key>",
		"PARTITION_KEY": "mytasks",
		"TABLE_NAME": "tasks"
	}

Замените **имя учетной записи хранения** именем учетной записи, которую создали ранее, а **ключ доступа к хранилищу** — первичным ключом доступа к своей учетной записи хранения. Например:

	{
	    "STORAGE_NAME": "nodejsappstorage",
	    "STORAGE_KEY": "KG0oDd..."
	    "PARTITION_KEY": "mytasks",
	    "TABLE_NAME": "tasks"
	}

Сохраните этот файл *на один уровень выше* каталога **tasklist**, как показано в ниже:

	parent/
	  |-- config.json
	  |-- tasklist/

Это позволит избежать включения файла конфигурации в исходный элемент управления, в результате чего он может оказаться общедоступным. При развертывании приложения в среде Azure вместо файла конфигурации мы будем использовать переменные среды.


## Локальный запуск приложения

Для проверки приложения на локальном компьютере выполните следующие действия:

1. В командной строке измените каталоги на каталог **tasklist**.

2. Используйте следующую команду для локального запуска приложения:

        npm start

3. Откройте веб-браузер и перейдите по адресу http://127.0.0.1:3000.

	Появится веб-страница, как в примере ниже.

	![Веб-страница, показывающая пустой список задач][node-table-finished]

4. Чтобы создать новый элемент списка, введите имя и категорию и щелкните **Add Item** (Добавить элемент).

6. Чтобы пометить задачу как выполненную, установите флажок **Complete** (Выполнено) и щелкните **Update Tasks** (Обновить задачи).

	![Изображение нового элемента в списке задач][node-table-list-items]

Хотя приложение выполняется локально, его данные хранятся в службе таблиц Azure.

## Развертывание приложения в Azure

В действиях, описанных в этом разделе, для создания веб-приложения в службе приложений используются средства командной строки Azure, а затем для развертывания приложения применяется Git. Для выполнения этих действий необходимо наличие подписки Azure.

> [AZURE.NOTE]Эти действия также можно выполнить с помощью [портала Azure](https://portal.azure.com). См. статью [Создание и развертывание веб-приложения Node.js в службе приложений Azure].
>
> Если это первое созданное вами веб-приложение, для его развертывания необходимо использовать портал Azure.

Сначала установите [интерфейс командной строки Azure], выполнив в командной строке следующую команду:

	npm install azure-cli -g

### Импорт параметров публикации

На этом шаге вы загрузите файл, содержащий сведения о вашей подписке.

1. Введите следующую команду:

		azure account download

	Эта команда запускает браузер и открывает страницу загрузки. Если будет предложено, войдите с учетной записью, которая связана с вашей подпиской Azure.

	<!-- ![The download page][download-publishing-settings] -->Скачивание файла должно начаться автоматически. Если этого не произошло, можно щелкнуть ссылку в начале страницы, чтобы скачать файл вручную. Сохраните файл и запомните путь к нему.

2. Введите следующую команду, чтобы импортировать параметры.

		azure account import <path-to-file>

	Укажите путь и имя файла параметров публикации, загруженного на предыдущем шаге.

3. После импорта параметров удалите файл параметров публикации. Он больше не нужен и содержит важные сведения, касающиеся вашей подписки Azure.

### Создание веб-приложения службы приложений

1. В командной строке измените каталоги на каталог **tasklist**.

2. Создайте веб-приложение, используя следующую команду.

		azure site create --git

	Вам будет предложено указать имя и расположение веб-приложения. Введите уникальное имя и выберите то же географическое расположение, что и у вашей учетной записи хранения Azure.

	Параметр `--git` создает в Azure репозиторий Git для этого веб-приложения. Он также инициализирует репозиторий Git в текущем каталоге, если его там нет, и добавляет [удаленный репозиторий Git] с именем «azure», которое используется для публикации приложения в среде Azure. Наконец, он создает файл **web.config**, который содержит параметры, используемые средой Azure для размещения приложений Node. Если параметр `--git` не указан, но в каталоге есть репозиторий Git, то данная команда все равно создаст удаленный репозиторий «azure».

	После выполнения этой команды должен появиться результат, похожий на следующий. Обратите внимание на то, что строка, начинающаяся с **Website created at** (Веб-сайт, созданный по адресу), содержит URL-адрес веб-приложения.

		info:   Executing command site create
		help:   Need a site name
		Name: TableTasklist
		info:   Using location southcentraluswebspace
		info:   Executing `git init`
		info:   Creating default .gitignore file
		info:   Creating a new web site
		info:   Created web site at  tabletasklist.azurewebsites.net
		info:   Initializing repository
		info:   Repository initialized
		info:   Executing `git remote add azure https://username@tabletasklist.azurewebsites.net/TableTasklist.git`
		info:   site create command OK

	> [AZURE.NOTE]Если это первое веб-приложение службы приложений для вашей подписки, вам будет указано использовать портал Azure для создания этого приложения. Дополнительные сведения см. в разделе [Создание веб-приложения Node.js и его развертывание в службу приложений Azure].

### Настройка переменных среды

На этом шаге в конфигурацию веб-приложения в Azure добавляются переменные среды. В командной строке введите следующую команду:

	azure site appsetting add
		STORAGE_NAME=<storage account name>;STORAGE_KEY=<storage access key>;PARTITION_KEY=mytasks;TABLE_NAME=tasks


Замените **<storage account name>** именем учетной записи хранения, которую вы создали ранее, а **<storage access key>** — первичным ключом доступа к учетной записи хранения. (Используйте те же значения, что и в файле config.json, который создали ранее.)

Переменные среды также можно задать на [портале Azure](https://portal.azure.com):

1.  Откройте колонку веб-приложения, выбрав **Обзор** > **Веб-приложения** > <имя вашего приложения>.

1.  В колонке веб-приложения щелкните **Все параметры** > **Параметры приложения**.

  	<!-- ![Top Menu](./media/storage-nodejs-use-table-storage-web-site/PollsCommonWebSiteTopMenu.png) -->

1.  Прокрутите содержимое вниз до раздела **Параметры приложения** и добавьте пары «ключ-значение».

  	![Параметры приложения](./media/storage-nodejs-use-table-storage-web-site/storage-tasks-appsettings.png)

1. Щелкните **СОХРАНИТЬ**.


### Публикация приложения

Чтобы опубликовать приложение, зафиксируйте файлы с кодом в репозитории Git и отправьте их в azure/master.

1. Задайте учетные данные развертывания.

		azure site deployment user set <name> <password>

2. Добавьте и зафиксируйте файлы своего приложения.

		git add .
		git commit -m "adding files"

3. Отправьте зафиксированные данные в веб-приложение службы приложений:

		git push azure master

	В качестве целевой ветви используйте **master**. В конце развертывания должно появиться заявление, похожее на следующее:

		To https://username@tabletasklist.azurewebsites.net/TableTasklist.git
 		 * [new branch]      master -> master

4. После завершения операции отправки перейдите по полученному ранее URL-адресу веб-приложения с помощью команды `azure create site`, чтобы просмотреть приложение.


## Дальнейшие действия

Хотя в действиях этой статьи описывается использование службы таблиц для хранения информации, можно также использовать MongoDB. Дополнительные сведения см. в статье [Создание веб-приложения Node.js в Azure с MongoDB на виртуальной машине].

## Дополнительные ресурсы

[Интерфейс командной строки Azure]

## Изменения
* Руководство по переходу от веб-сайтов к службе приложений см. в статье [Служба приложений Azure и существующие службы Azure](http://go.microsoft.com/fwlink/?LinkId=529714).

[Создание веб-приложения Node.js и его развертывание в службу приложений Azure]: web-sites-nodejs-develop-deploy-mac.md
[Создание и развертывание веб-приложения Node.js в службе приложений Azure]: web-sites-nodejs-develop-deploy-mac.md
[Continuous deployment using GIT in Azure App Service]: web-sites-publish-source-control.md
[Azure Developer Center]: /develop/nodejs/


[node]: http://nodejs.org
[Git]: http://git-scm.com
[Git.]: http://git-scm.com
[Express]: http://expressjs.com
[for free]: http://windowsazure.com
[удаленный репозиторий Git]: http://git-scm.com/docs/git-remote

[Создание веб-приложения Node.js в Azure с MongoDB на виртуальной машине]: web-sites-nodejs-store-data-mongodb.md
[интерфейс командной строки Azure]: ../xplat-cli-install.md

[Continuous deployment using GIT in Azure App Service]: web-sites-publish-source-control.md
[azure]: https://github.com/Azure/azure-sdk-for-node
[node-uuid]: https://www.npmjs.com/package/node-uuid
[nconf]: https://www.npmjs.com/package/nconf
[async]: https://www.npmjs.com/package/async

[Azure Portal]: https://portal.azure.com


[node-table-finished]: ./media/storage-nodejs-use-table-storage-web-site/table_todo_empty.png
[node-table-list-items]: ./media/storage-nodejs-use-table-storage-web-site/table_todo_list.png
[download-publishing-settings]: ./media/storage-nodejs-use-table-storage-web-site/azure-account-download-cli.png
[portal-new]: ./media/storage-nodejs-use-table-storage-web-site/plus-new.png
[portal-storage-account]: ./media/storage-nodejs-use-table-storage-web-site/new-storage.png
[portal-quick-create-storage]: ./media/storage-nodejs-use-table-storage-web-site/quick-storage.png
[portal-storage-access-keys]: ./media/storage-nodejs-use-table-storage-web-site/manage-access-keys.png

[go-to-dashboard]: ./media/storage-nodejs-use-table-storage-web-site/go_to_dashboard.png
[web-configure]: ./media/storage-nodejs-use-table-storage-web-site/sql-task-configure.png
[app-settings-save]: ./media/storage-nodejs-use-table-storage-web-site/savebutton.png
[app-settings]: ./media/storage-nodejs-use-table-storage-web-site/storage-tasks-appsettings.png

[Create and deploy a Node.js application to an Azure Web Site]: web-sites-nodejs-develop-deploy-mac.md
 

<!---HONumber=AcomDC_0114_2016-->
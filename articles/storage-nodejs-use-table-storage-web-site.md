<properties linkid="dev-nodejs-tutorials-web-site-with-storage" urlDisplayName="Website with Storage" pageTitle="Node.js website with table storage | Microsoft Azure" metaKeywords="Azure table storage Node.js, Azure Node.js application, Azure Node.js tutorial, Azure Node.js example" description="A tutorial that teaches you how to use the Azure Table service to store data from a Node application hosted on an Azure website." metaCanonical="" services="web-sites,storage" documentationCenter="Node.js" title="Node.js Web Application using the Azure Table Service" authors="larryfr" solutions="" manager="" editor="" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="nodejs" ms.topic="article" ms.date="01/01/1900" ms.author="larryfr"></tags>

# Веб-приложение Node.js, использующее службу таблиц Azure

В этом учебном курсе показано, как использовать службу таблиц, предоставляемую службой управления данными Azure, для хранения данных и доступа к ним из приложения [node][], размещенного в Azure. Этот учебный курс разработан для читателей, обладающих определенным опытом использования Node и [Git][].

Вы узнаете:

-   Как использовать для установки модулей Node диспетчер npm (node package manager)

-   Как работать со службой таблиц Azure

-   Как использовать средства командной строки Azure для Mac и Linux для создания веб-сайта Azure

В ходе учебного курса вы создадите простое веб-приложение для управления задачами, позволяющее создавать, извлекать и выполнять задачи. Задачи хранятся в службе таблиц.

Файлы проекта для этого учебного курса будут храниться в каталоге с именем **tasklist**, завершенное приложение будет выглядеть примерно следующим образом:

![Веб-страница, показывающая пустой список задач][]

> [WACOM.NOTE] Этот учебный курс ссылается на папку **tasklist**. Полный путь к этой папке опущен, поскольку семантика путей зависит от операционной системы. Эту папку нужно создать в легкодоступном месте локальной файловой системы, например,**~/node/tasklist** или **c:\\node\\tasklist**.

> [WACOM.NOTE] Многие из описанных ниже действий могут быть выполнены из командной строки. Для выполнения этих действий используйте командную строку своей операционной системы, например **cmd.exe** (Windows) или **Bash** (Unix Shell). На компьютерах с OS X доступ к командной строке можно получить через приложение Terminal.

## Предварительные требования

Перед выполнением инструкций, приведенных в этой статье, следует убедиться, что установлены следующие компоненты:

-   [node][] версии 0.10.24 или выше

-   [Git][]

-   Текстовый редактор

-   Веб-браузер

[WACOM.INCLUDE [создание-учетная запись-и-веб-сайты-примечание][]]

## Создание учетной записи хранения

Выполните следующие действия для создания учетной записи хранения. Эта учетная запись будет использоваться в последующих действиях данного учебного курса.

1.  Откройте веб-браузер и перейдите на [Портал Azure][]. При необходимости войдите в систему, используя данные своей подписки Azure.

2.  В нижней части окна портала щелкните **+ СОЗДАТЬ**, а затем выберите **Учетная запись хранения**.

    ![+Создать][]

    ![учетная запись хранения][]

3.  Выберите **Быстрое создание**, а затем введите URL-адрес и регион или территорию для этой учетной записи хранения. Так как в этом учебном курсе глобальная репликация не требуется, снимите флажок **Включить георепликацию**. В заключение щелкните "Создать учетную запись хранения".

    ![Быстро создать][]

    Запишите введенный URL-адрес, так как он будет использоваться в последующих действиях в качестве имени учетной записи.

4.  После создания учетной записи хранения выберите **Управление ключами** в нижней части страницы. При этом появятся первичный и вторичный ключи доступа для этой учетной записи хранения. Скопируйте и сохраните первичный ключ доступа, а затем нажмите кнопку с галочкой.

    ![ключи доступа][]

## Установка модулей и создание шаблонов

В этом разделе вы создадите новое приложение Node и добавите пакеты модулей с помощью npm. Для приложения списка задач будут использованы модули [Express][] и [Azure][]. Модуль Express предоставляет платформу Model View Controller (Контроллер представления модели) для Node, а модули Azure предоставляют возможность подключения к службе таблиц.

### Установка модуля Express и формирование шаблонов

1.  В командной строке измените каталоги на каталог **tasklist**. Если каталог **tasklist** не существует, создайте его.

2.  Введите следующую команду для установки Express.

        npm install express-generator@4.2.0 -g

    > [WACOM.NOTE] При использовании параметра "-g" в некоторых операционных системах может появиться сообщение об ошибке **Ошибка: EPERM, chmod '/usr/local/bin/express'** и предложение попробовать использовать учетную запись с правами администратора. В этом случае необходимо с помощью команды **sudo** запустить npm с более высоким уровнем привилегий.

    Результат этой команды должен выглядеть аналогично следующему:

        express-generator@4.2.0 C:\Users\username\AppData\Roaming\npm\node_modules\express-generator
        ├── mkdirp@0.3.5
        └── commander@1.3.2 (keypress@0.1.0)

    > [WACOM.NOTE] Параметр "-g", используемый при установке модуля express, устанавливает его глобальным образом. Это делается для того, чтобы можно было получить доступ к команде **express** для формирования шаблонов веб-сайта без необходимости вводить дополнительные сведения о путях.\*\*

3.  Для создания шаблонов, которые будут использоваться для данного приложения, используйте команду **express**:

        express

    Результат этой команды должен выглядеть аналогично следующему:

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

    После выполнения этой команды в каталоге **tasklist** должно появиться несколько новых папок и файлов.

### Установка дополнительных модулей

Файл **package.json** является одним из файлов, созданных командой **express**. Этот файл содержит список дополнительных модулей, необходимых для приложения Express. Позднее, при развертывании этого приложения на веб-сайте Azure, этот файл будет использоваться, чтобы определить, какие модули должны быть установлены в Azure для поддержки приложения.

1.  В командной строке измените каталоги на папку **tasklist** и введите следующую команду для установки модулей, описанных в файле **package.json**:

        npm install

    Результат этой команды должен выглядеть аналогично следующему:

        debug@0.7.4 node_modules\debug

        static-favicon@1.0.2 node_modules\static-favicon

        morgan@1.0.1 node_modules\morgan
        └── bytes@0.3.0

        cookie-parser@1.0.1 node_modules\cookie-parser
        ├── cookie-signature@1.0.3
        └── cookie@0.1.0

        body-parser@1.0.2 node_modules\body-parser
        ├── qs@0.6.6
        ├── raw-body@1.1.7 (string_decoder@0.10.25-1, bytes@1.0.0)
        └── type-is@1.1.0 (mime@1.2.11)

        express@4.2.0 node_modules\express
        ├── parseurl@1.0.1
        ├── merge-descriptors@0.0.2
        ├── utils-merge@1.0.0
        ├── cookie@0.1.2
        ├── escape-html@1.0.1
        ├── cookie-signature@1.0.3
        ├── debug@0.8.1
        ├── fresh@0.2.2
        ├── qs@0.6.6
        ├── range-parser@1.0.0
        ├── methods@1.0.0
        ├── buffer-crc32@0.2.1
        ├── serve-static@1.1.0
        ├── path-to-regexp@0.1.2
        ├── send@0.3.0 (debug@0.8.0, mime@1.2.11)
        ├── type-is@1.1.0 (mime@1.2.11)
        └── accepts@1.0.1 (negotiator@0.4.7, mime@1.2.11)

        jade@1.3.1 node_modules\jade
        ├── commander@2.1.0
        ├── character-parser@1.2.0
        ├── mkdirp@0.3.5
        ├── monocle@1.1.51 (readdirp@0.2.5)
        ├── constantinople@2.0.1 (uglify-js@2.4.15)
        ├── transformers@2.1.0 (promise@2.0.0, css@1.0.8, uglify-js@2.2.5)
        └── with@3.0.0 (uglify-js@2.4.15)

    Будут установлены все стандартные модули, необходимые для Express.

2.  Далее используйте следующую команду для установки [azure][Azure], модулей [node-uuid], [nconf] и [async] локально, а также сохранения записи о них в файле **package.json**:

        npm install azure-storage node-uuid async nconf --save

    Результат этой команды должен выглядеть аналогично следующему:

        async@0.9.0 node_modules\async

        node-uuid@1.4.1 node_modules\node-uuid

        nconf@0.6.9 node_modules\nconf
        ├── ini@1.2.1
        ├── async@0.2.9
        └── optimist@0.6.0 (wordwrap@0.0.2, minimist@0.0.10)

        azure-storage@0.3.0 node_modules\azure-storage
        ├── extend@1.2.1
        ├── xmlbuilder@0.4.3
        ├── mime@1.2.11
        ├── validator@3.1.0
        ├── underscore@1.4.4
        ├── xml2js@0.2.7 (sax@0.5.2)
        └── request@2.27.0 (forever-agent@0.5.2, aws-sign@0.3.0, json-stringify-safe@5.0.0, tunnel-agent@0.3.0, qs@0.6.6, oauth-sign@0.3.0, cookie-jar@0.3.0, form-data@0.1.4, hawk@1.0.0, http-signature@0.10.0)

## Использование службы таблиц в приложении Node

В этом разделе базовое приложение, созданное командой **express**, будет расширено путем добавления файла **task.js**, содержащего модель для ваших задач. Также будет изменено существующее приложение **app.js** и создан новый файл контроллера **tasklist.js** для использования этой модели.

### Создание модели

1.  В каталоге **tasklist** создайте новый каталог с именем **models**.

2.  В каталоге **models** создайте новый файл с именем **task.js**. Этот файл будет содержать модель для задач, создаваемых приложением.

3.  В начале файла **task.js** добавьте следующий код для ссылки на необходимые библиотеки:

        var azure = require('azure-storage');
        var uuid = require('node-uuid');
        var entityGen = azure.TableUtilities.entityGenerator;

4.  Далее будет добавлен код для определения и экспорта объекта Task. Этот объект отвечает за подключение к таблице.

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

5.  Затем добавьте следующий код, чтобы определить дополнительные методы для объекта Task, обеспечивающего взаимодействие с данными, хранящимися в таблице:

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

6.  Сохраните и закройте файл **task.js**.

### Создание контроллера

1.  В каталоге **tasklist/routes** создайте новый файл с именем **tasklist.js** и откройте его в текстовом редакторе.

2.  Добавьте в **tasklist.js** следующий код. Он загружает модули azure и async, используемые **tasklist.js**. Он также определяет функцию **TaskList**, передающую экземпляр объекта **Task**, определенного ранее:

        var azure = require('azure-storage');
        var async = require('async');

        module.exports = TaskList;

        function TaskList(task) {
          this.task = task;
        }

3.  Продолжит добавления в файл **tasklist.js**, добавляя методы, используемые для **showTasks**, **addTask** и **completeTasks**:

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

4.  Сохраните файл **tasklist.js**.

### Изменение app.js

1.  В каталоге **tasklist** откройте файл **app.js** в текстовом редакторе. Этот файл был создан ранее с помощью команды **express**.

2.  В начале файла добавьте следующий код для загрузки модуля azure, задайте имя таблицы, partitionKey, а также учетные данные хранилища, используемые в этом примере:

        var azure = require('azure-storage');
        var nconf = require('nconf');
        nconf.env()
             .file({ file: 'config.json'});
        var tableName = nconf.get("TABLE_NAME");
        var partitionKey = nconf.get("PARTITION_KEY");
        var accountName = nconf.get("STORAGE_NAME");
        var accountKey = nconf.get("STORAGE_KEY");

    > [WACOM.NOTE] nconf загружает значения конфигурации из обеих переменных среды или файла **config.json**, который будет создан позднее.

3.  Прокрутите файл app.js вниз до появления следующей строки:

        app.use('/', routes);
        app.use('/users', users);

    Замените вышеприведенные строки на код, приведенный ниже. Он инициализирует экземпляр **Task**, используя подключение к вашей учетной записи хранения. Он передается в **TaskList**, где будет использоваться для взаимодействия со службой таблиц:

        var TaskList = require('./routes/tasklist');
        var Task = require('./models/task');
        var task = new Task(azure.createTableService(accountName, accountKey), tableName, partitionKey);
        var taskList = new TaskList(task);

        app.get('/', taskList.showTasks.bind(taskList));
        app.post('/addtask', taskList.addTask.bind(taskList));
        app.post('/completetask', taskList.completeTask.bind(taskList));

4.  Сохраните файл **app.js**.

### Изменение представления индекса

1.  Измените каталоги на каталог **views** и откройте файл **index.jade** в текстовом редакторе.

2.  Заменит содержимое файла **index.jade** на код, приведенный ниже. Он определяет представление для отображения существующих задач, а также форму для добавления новых задач и пометки существующих задач как завершенных.

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

3.  Сохраните и закройте файл **index.jade**.

### Изменение глобального макета

Файл **layout.jade** в каталоге **views** используется как глобальный шаблон для других файлов **.jade**. На этом шаге он будет изменен для использования [Twitter Bootstrap][] — набора средств, упрощающих разработку привлекательного веб-сайта.

1.  Загрузите и извлеките файлы [Twitter Bootstrap][1]. Скопируйте файл **bootstrap.min.css** из папки **bootstrap\\dist\\css** в каталог **public\\stylesheets** своего приложения tasklist.

2.  В папке **views** откройте файл **layout.jade** в текстовом редакторе и замените его содержимое на следующее:

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

3.  Сохраните файл **layout.jade**.

### Создание файла конфигурации

Файл **config.json** содержит строку подключения, используемую для подключения к базе данных SQL, и считывается приложением во время выполнения. Чтобы создать этот файл, выполните следующие действия:

1.  В каталоге **tasklist** создайте новый файл с именем **config.json** и откройте его в текстовом редакторе.

2.  Содержание файла **config.json** должно быть похоже на следующее:

        {
            "STORAGE_NAME": "storage account name",
            "STORAGE_KEY": "storage access key",
            "PARTITION_KEY": "mytasks",
            "TABLE_NAME": "tasks"
        }

    Замените **storage account name** на имя учетной записи хранения, созданной ранее. Замените **storage access key** на первичный ключ доступа к вашей учетной записи хранения.

3.  Сохраните файл.

## Локальный запуск приложения

Для проверки приложения на локальном компьютере выполните следующие действия:

1.  В командной строке измените каталоги на каталог **tasklist**.

2.  Используйте следующую команду для локального запуска приложения:

        npm start

3.  Откройте веб-браузер и перейдите по адресу <http://127.0.0.1:3000>. Должна появиться веб-страница, похожая на следующую:

    ![Веб-страница, показывающая пустой список задач][]

4.  Используйте предоставленные поля **Имя элемента** и **Категория элемента** для ввода данных, а затем нажмите **Добавить элемент**.

5.  Страница должна обновиться, чтобы показать элемент в таблице списка задач.

    ![Изображение нового элемента в списке задач][]

6.  Чтобы завершить задачу, просто установите флажок в столбце "Завершено" и нажмите кнопку **Обновить задачи**.

7.  Чтобы остановить процесс Node, перейдите в командную строку и нажмите сочетание клавиш **CTRL** и **C**.

## Развертывание приложения в Azure

В действиях, описанных в этом разделе, для создания нового веб-сайта Azure используются средства командной строки Azure, а затем для развертывания приложения используется репозиторий Git. Для выполнения этих действий необходимо наличие подписки Azure.

> [WACOM.NOTE] Эти действия также можно выполнить с помощью портала Azure. Инструкции по использованию портала Azure для развертывания приложения Node.js см. в статье [Создание и развертывание приложения Node.js на веб-сайте Azure][].

> [WACOM.NOTE] Если это первый созданный вами веб-сайт Azure, для развертывания приложения необходимо использовать портал Azure.

### Создание подписки Azure

Если у вас еще нет подписки Azure, можно зарегистрироваться [бесплатно][Портал Azure]. После регистрации выполните следующие шаги, чтобы продолжить учебник.

[WACOM.INCLUDE [antares-iaas-регистрация][]]

### Установка средств командной строки Azure для Mac и Linux

Для установки средств командной строки используйте следующую команду:

    npm install azure-cli -g

> [WACOM.NOTE] Дополнительную информацию см. в разделе [Установка и настройка межплатформенного интерфейса командной строки Azure][];

> [WACOM.NOTE] Хотя средства командной строки были созданы в основном для пользователей Mac и Linux, они основаны на Node.js и должны работать в любой системе, поддерживающей работу Node.

### Импорт параметров публикации

Перед использованием средств командной строки с Azure необходимо сначала загрузить файл, содержащий сведения о подписке. Выполните следующие действия, чтобы загрузить и импортировать этот файл.

1.  В командной строке измените каталоги на каталог **tasklist**.

2.  Введите следующую команду, чтобы запустить браузер, и перейдите на страницу загрузки. В ответ на приглашение выполните вход с использованием учетной записи, связанной с вашей подпиской.

        azure account download

    ![Страница загрузки][]

    Загрузка файла должна начаться автоматически. Если этого не произошло, можно щелкнуть ссылку в начале страницы, чтобы загрузить файл вручную.

3.  После завершения загрузки файла используйте для импорта параметров следующую команду:

        azure account import <path-to-file>

    Укажите путь и имя файла параметров публикации, загруженного на предыдущем шаге. Результат выполнения команды должен быть похож на следующее:

        info:   Executing command account import
        info:   Setting service endpoint to: management.core.windows.net
        info:   Setting service port to: 443
        info:   Found subscription: YourSubscription
        info:   Setting default subscription to: YourSubscription
        warn:   The 'C:\users\username\downloads\YourSubscription-6-7-2012-credentials.publishsettings' file contains sensitive information.
        warn:   Remember to delete it now that it has been imported.
        info:   Account publish settings imported successfully
        info:   account import command OK

4.  По завершении импорта следует удалить файл параметров публикации, так как он больше не нужен и содержит важные сведения, касающиеся подписки Azure.

### Создание веб-сайта Azure

1.  В командной строке измените каталоги на каталог **tasklist**.

2.  Используйте следующую команду для создания нового веб-сайта Azure:

        azure site create --git

    Будет предложено указать имя веб-сайта и центр обработки данных, в котором веб-сайт будет создан. Выберите уникальное имя и центр обработки данных, расположенный недалеко от вашего местоположения.

    Параметр `--git` создает репозиторий Git на Azure для этого веб-сайта. При отсутствии репозитория Git он также будет инициализирован в текущем каталоге. Также будет создан [удаленный репозиторий Git][] с именем "azure", который будет использоваться для публикации приложения в Azure. Наконец, будет создан файл **web.config**, содержащий настройки, используемые Azure для размещения приложений Node.

    > [WACOM.NOTE] Если эта команда выполняется из каталога, который уже содержит репозиторий Git, каталог не будет инициализирован заново.

    > [WACOM.NOTE] Если параметр "`--git`" отсутствует, но каталог содержит репозиторий Git, удаленный репозиторий "azure" все же будет создан.

    После выполнения этой команды должен появиться результат, похожий на следующий. Обратите внимание, что строка, начинающаяся с **Web site created at** (Веб-сайт, созданный по адресу), содержит URL-адрес веб-сайта.

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

    > [WACOM.NOTE] Если это первый веб-сайт Azure для вашей подписки, необходимо использовать портал для создания веб-сайта. Дополнительные сведения см. в статье [Создание и развертывание приложения Node.js на веб-сайте Azure].

### Публикация приложения

1.  В окне Terminal измените каталоги на каталог **tasklist**, если он еще не является текущим.

2.  Используйте следующие команды для добавления и последующего сохранения файлов в локальном репозитории Git:

        git add .
        git commit -m "adding files"

3.  При принудительной отправке последних изменений репозитория Git на веб-сайт Azure необходимо указать, что целевой ветвью, используемой для контента этого веб-сайта, является **master**.

        git push azure master

    В конце развертывания должно появиться заявление, похожее на следующее:

        To https://username@tabletasklist.azurewebsites.net/TableTasklist.git
         * [new branch]      master -> master

4.  После завершения операции принудительной отправки перейдите на веб-сайт с помощью команды "`azure create site`" для просмотра приложения.

### Переключение на переменную среды

Ранее был реализован код, выполняющий поиск переменных среды или загрузки значения из файла **config.json**. При следующих действиях в конфигурации веб-сайта будет создана пара "ключ/значение", обеспечивающая реальный доступ к приложению с помощью переменной среды.

1.  На портале управления щелкните **Вебсайты**, a затем выберите свой веб-сайт.

    ![Откройте панель мониторинга веб-сайта][]

2.  Щелкните **НАСТРОЙКА** и найдите на странице раздел **параметры приложения**.

    ![ссылка настройки][]

3.  В разделе **параметры приложения** введите **ИМЯ\_ХРАНИЛИЩА** в поле **КЛЮЧ**, а также имя своей учетной записи хранения в поле **ЗНАЧЕНИЕ**. Щелкните значок галочки для перехода к следующему полю. Повторите этот процесс для следующих ключей и значений:

    -   **STORAGE\_KEY** - ключ доступа к вашей учетной записи хранения

    -   **PARTITION\_KEY** - 'mytasks'

    -   **TABLE\_NAME** - 'tasks'

    ![параметры приложения][]

4.  И наконец, щелкните значок **СОХРАНИТЬ** в нижней части страницы, чтобы принять это изменение в среде выполнения.

    ![сохранение параметров приложения][]

5.  В командной строке измените каталоги на каталог **tasklist** и введите следующую команду для удаления файла **config.json**:

        git rm config.json
        git commit -m "Removing config file"

6.  Выполните следующую команду, чтобы развернуть изменения в Azure:

        git push azure master

После развертывания изменений в Azure веб-приложение должно продолжить работать, поскольку оно теперь считывает строку подключения из записи **параметры приложения**. Для проверки измените значение записи **STORAGE\_KEY** в записи **параметры приложения** на недействительное значение. После сохранения этого значения веб-сайт должен перестать работать из-за неправильного значения ключа доступа к хранилищу.

## Дальнейшие действия

Хотя в действиях этой статьи описывается использование службы таблиц для хранения информации, можно также использовать MongoDB. Дополнительные сведения см. в статье [Веб-приложение Node.js с MongoDB][].

## Дополнительные ресурсы

[Средства командной строки Azure для Mac и Linux]

[Создание и развертывание приложения Node.js на веб-сайтах Azure]: /ru-ru/documentation/articles/web-sites-nodejs-develop-deploy-mac/
[Публикация на веб-сайтах Azure с использованием репозитория Git]: /ru-ru/documentation/articles/web-sites-publish-source-control/
[Центре разработчиков Azure]: /en-us/develop/nodejs/

  [node]: http://nodejs.org
  [Git]: http://git-scm.com
  [Веб-страница, показывающая пустой список задач]: ./media/storage-nodejs-use-table-storage-web-site/table_todo_empty.png
  [создание-учетная запись-и-веб-сайты-примечание]: ../includes/create-account-and-websites-note.md
  [Портал Azure]: http://windowsazure.com
  [+Создать]: ./media/storage-nodejs-use-table-storage-web-site/plus-new.png
  [учетная запись хранения]: ./media/storage-nodejs-use-table-storage-web-site/new-storage.png
  [Быстро создать]: ./media/storage-nodejs-use-table-storage-web-site/quick-storage.png
  [ключи доступа]: ./media/storage-nodejs-use-table-storage-web-site/manage-access-keys.png
  [Express]: http://expressjs.com
  [Azure]: https://github.com/Azure/azure-sdk-for-node
  [Twitter Bootstrap]: https://github.com/twbs/bootstrap
  [1]: http://getbootstrap.com/
  [Изображение нового элемента в списке задач]: ./media/storage-nodejs-use-table-storage-web-site/table_todo_list.png
  [Создание и развертывание приложения Node.js на веб-сайте Azure]: /ru-ru/documentation/articles/web-sites-nodejs-develop-deploy-mac/
  [antares-iaas-регистрация]: ../includes/antares-iaas-signup.md
  [Установка и настройка межплатформенного интерфейса командной строки Azure]: /ru-ru/documentation/articles/xplat-cli/
  [Средства командной строки Azure для Mac и Linux]: /ru-ru/documentation/articles/xplat-cli/
  [Страница загрузки]: ./media/storage-nodejs-use-table-storage-web-site/azure-account-download-cli.png
  [удаленный репозиторий Git]: http://git-scm.com/docs/git-remote
  [Откройте панель мониторинга веб-сайта]: ./media/storage-nodejs-use-table-storage-web-site/go_to_dashboard.png
  [ссылка настройки]: ./media/storage-nodejs-use-table-storage-web-site/sql-task-configure.png
  [параметры приложения]: ./media/storage-nodejs-use-table-storage-web-site/storage-tasks-appsettings.png
  [сохранение параметров приложения]: ./media/storage-nodejs-use-table-storage-web-site/savebutton.png
  [Веб-приложение Node.js с MongoDB]: /ru-ru/documentation/articles/web-sites-nodejs-store-data-mongodb/
  [Публикация на веб-сайтах Azure с использованием репозитория Git]: /ru-ru/documentation/articles/web-sites-publish-source-control/

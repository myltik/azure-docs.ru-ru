<properties urlDisplayName="Website with MongoDB" pageTitle="Веб-сайт node.js с MongoDB с MongoLab - Azure "metaKeywords ="" description="Узнайте, как создать веб-сайт Azure на Node.js, который подключается к экземпляру MongoDB, размещенному в MongoLab." metaCanonical="" services="web-sites,virtual-machines" documentationCenter="nodejs" title="Create a Node.js Application on Azure with MongoDB using the MongoLab Add-On" authors="chris@mongolab.com" solutions="" manager="mongolab; partners@mongolab.com" editor="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="nodejs" ms.topic="article" ms.date="09/17/2014" ms.author="chris@mongolab.com" />






# Создание приложения Node.js в Azure с MongoDB с помощью надстройки MongoLab

<p><em>Автор: Эрик Седор (Eric Sedor), MongoLab</em></p>

Приветствую, искатели приключений! Добро пожаловать в MongoDB как услуга. Изучив данный учебник, вы научитесь:

1. [Подготовка базы данных][provision] - надстройка [MongoLab](http://mongolab.com), доступная в Магазине Azure, обеспечивает доступ к базе данных MongoDB, размещенной в облаке и управляемой с помощью облачной платформы базы данных MongoLab.
2. [Создание приложения][create] - это простое приложение Node.js для обслуживания списка задач.
3. [Развертывание приложения][deploy] - одновременное использование нескольких обработчиков конфигурации значительно упрощает передачу кода.
4. [Управление базой данных][manage] - наконец, мы покажем веб-портал управления базами данных MongoLab, на котором вы можете с легкостью искать, визуализировать и изменять данные.

При работе с этим учебником можно в любое время отправить сообщение электронной почты на адрес [support@mongolab.com](mailto:support@mongolab.com), если у вас возникнут какие-либо вопросы.

Прежде чем продолжить, убедитесь, что установлены следующие компоненты:

* [Node.js] версии не ниже 0.10.29

* [Git]

[WACOM.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

## Быстрый запуск
Если у вас есть опыт работы с Магазином Azure, воспользуйтесь этим разделом, чтобы быстро начать работу. В противном случае изучите приведенный ниже раздел [Подготовка базы данных][provision].
 
1. Откройте Магазин Azure.  
![Store][button-store]
2. Щелкните надстройку MongoLab.  
![MongoLab][entry-mongolab]
3. Щелкните надстройку MongoLab в списке надстроек, затем выберите пункт **Сведения о подключении**.  
![ConnectionInfoButton][button-connectioninfo]  
4. Скопируйте MONGOLAB_URI в буфер обмена.  
![ConnectionInfoScreen][screen-connectioninfo]  
**Этот универсальный код ресурса (URI) содержит имя пользователя базы данных и пароль.  Считайте это конфиденциальной информацией и не передавайте ее.**
5. Добавьте значение в список строк подключения в меню конфигурации веб-приложения Azure:  
![WebSiteConnectionStrings][focus-website-connectinfo]
6. в поле **Имя** введите MONGOLAB\_URI.
7. В качестве **Значения** вставьте строку подключения, полученную в предыдущем разделе.
8. Выберите пункт **Настраиваемый** в раскрывающемся списке "Тип" (вместо значения по умолчанию **SQLAzure**).
9. Выполните команду "npm install mongoose", чтобы получить Mongoose - драйвер узла MongoDB.
10. Настройте обработчик в коде, чтобы получить универсальный код ресурса (URI) подключения MongoLab из переменной среды и выполнить подключение:

        var mongoose = require('mongoose');  
 		...
 		var connectionString = process.env.CUSTOMCONNSTR_MONGOLAB_URI
 		...
 		mongoose.connect(connectionString);

Примечание. Приложение Azure добавит префикс **CUSTOMCONNSTR\_** к первоначально объявленной строке подключения, поэтому код ссылается на **CUSTOMCONNSTR\_MONGOLAB\_URI.**, а не **MONGOLAB\_URI**.

Теперь перейдем к полному учебнику...

<h2><a name="provision"></a>Подготовка базы данных</h2>

[WACOM.INCLUDE [howto-provision-mongolab](../includes/howto-provision-mongolab.md)]

<h2><a name="create"></a>Создание приложения</h2>

В этом разделе вы настроите среду разработки и создадите код для веб-приложения простого списка задач с помощью Node.js, Express и MongoDB. [Express] предоставляет инфраструктуру просмотра контроллера для узла, а [Mongoose] - это драйвер для обмена данными с MongoDB в узле.

### Настройка

#### Формирование шаблонов и установка модулей

1. В командной строке создайте каталог **tasklist** и перейдите в него. Они и будет являться каталогом вашего проекта.
2. Введите следующую команду для установки Express.

		npm install express -g
 
	"-g" указывает на глобальный режим, который используется для предоставления доступа к модулю <strong>express</strong> без указания пути к каталогу. Если будет получена <strong>Ошибка: EPERM, chmod '/usr/local/bin/express'</strong>, используйте <strong>sudo</strong> для запуска npm с более высоким уровнем привилегий.

    Результат этой команды должен выглядеть аналогично следующему:

		express@4.9.1 C:\Users\mongolab\AppData\Roaming\npm\node_modules\express
		├── merge-descriptors@0.0.2
		├── utils-merge@1.0.0
		├── fresh@0.2.4
		├── cookie@0.1.2
		├── range-parser@1.0.2
		├── escape-html@1.0.1
		├── cookie-signature@1.0.5
		├── finalhandler@0.2.0
		├── vary@1.0.0
		├── media-typer@0.3.0
		├── parseurl@1.3.0
		├── serve-static@1.6.2
		├── methods@1.1.0
		├── path-to-regexp@0.1.3
		├── depd@0.4.5
		├── qs@2.2.3
		├── on-finished@2.1.0 (ee-first@1.0.5)
		├── debug@2.0.0 (ms@0.6.2)
		├── proxy-addr@1.0.1 (ipaddr.js@0.1.2)
		├── etag@1.3.1 (crc@3.0.0)
		├── send@0.9.2 (destroy@1.0.3, ms@0.6.2, mime@1.2.11)
		├── accepts@1.1.0 (negotiator@0.4.7, mime-types@2.0.1)
		└── type-is@1.5.1 (mime-types@2.0.1)
 
3. Чтобы создать шаблоны, которые будут применяться для этого приложения, используйте команду **express**:

    express

    Обратите внимание, что в этом учебнике используется Express версии 4.x.x. Если в системе уже установлен генератор приложений Express 3, сначала удалите его.

    npm uninstall -g express

    Теперь установите новый генератор для версии 4.x.x.

    npm install -g express-generator

	После запуска команды **express** выходные результаты должны выглядеть следующим образом:

		create : .
		create : ./package.json
		create : ./app.js
		create : ./public
		create : ./public/javascripts
		create : ./public/images
		create : ./public/stylesheets
		create : ./public/stylesheets/style.css
		create : ./routes
		create : ./routes/index.js
		create : ./routes/users.js
		create : ./views
		create : ./views/index.jade
		create : ./views/layout.jade
		create : ./views/error.jade
		create : ./bin
		create : ./bin/www

		install dependencies:
		$ cd . && npm install


	После выполнения этой команды в каталоге **tasklist** должно появиться несколько новых папок и файлов.
	
4. Введите следующую команду, чтобы установить модули, описанные в файле **package.json**:

        npm install

    Результат этой команды должен выглядеть аналогично следующему:

		cookie-parser@1.3.3 node_modules/cookie-parser
		├── cookie@0.1.2
		└── cookie-signature@1.0.5

		debug@2.0.0 node_modules/debug
		└── ms@0.6.2

		serve-favicon@2.1.4 node_modules/serve-favicon
		├── ms@0.6.2
		├── fresh@0.2.4
		└── etag@1.3.1 (crc@3.0.0)

		morgan@1.3.1 node_modules/morgan
		├── basic-auth@1.0.0
		├── depd@0.4.5
		└── on-finished@2.1.0 (ee-first@1.0.5)

		express@4.9.1 node_modules/express
		├── utils-merge@1.0.0
		├── merge-descriptors@0.0.2
		├── cookie@0.1.2
		├── fresh@0.2.4
		├── escape-html@1.0.1
		├── range-parser@1.0.2
		├── cookie-signature@1.0.5
		├── finalhandler@0.2.0
		├── vary@1.0.0
		├── media-typer@0.3.0
		├── serve-static@1.6.2
		├── parseurl@1.3.0
		├── methods@1.1.0
		├── path-to-regexp@0.1.3
		├── depd@0.4.5
		├── qs@2.2.3
		├── etag@1.3.1 (crc@3.0.0)
		├── on-finished@2.1.0 (ee-first@1.0.5)
		├── proxy-addr@1.0.1 (ipaddr.js@0.1.2)
		├── send@0.9.2 (destroy@1.0.3, ms@0.6.2, mime@1.2.11)
		├── type-is@1.5.1 (mime-types@2.0.1)
		└── accepts@1.1.0 (negotiator@0.4.7, mime-types@2.0.1)

		body-parser@1.8.2 node_modules/body-parser
		├── media-typer@0.3.0
		├── raw-body@1.3.0
		├── bytes@1.0.0
		├── depd@0.4.5
		├── on-finished@2.1.0 (ee-first@1.0.5)
		├── qs@2.2.3
		├── iconv-lite@0.4.4
		└── type-is@1.5.1 (mime-types@2.0.1)

		jade@1.6.0 node_modules/jade
		├── character-parser@1.2.0
		├── commander@2.1.0
		├── void-elements@1.0.0
		├── mkdirp@0.5.0 (minimist@0.0.8)
		├── monocle@1.1.51 (readdirp@0.2.5)
		├── transformers@2.1.0 (promise@2.0.0, css@1.0.8, uglify-js@2.2.5)
		├── constantinople@2.0.1 (uglify-js@2.4.15)
		└── with@3.0.1 (uglify-js@2.4.15)

	Файл **package.json** является одним из файлов, созданных командой **express**. Этот файл содержит список дополнительных модулей, необходимых для приложения Express. Позднее, при развертывании этого приложения на веб-сайте Azure, этот файл будет использоваться, чтобы определить, какие модули должны быть установлены в Azure для поддержки приложения.

5. Затем введите следующую команду, чтобы установить модуль Mongoose локально, а также сохранить запись для него в файле **package.json**:

		npm install mongoose --save

	Результат этой команды должен выглядеть аналогично следующему:

		mongoose@3.8.16 node_modules/mongoose
		├── regexp-clone@0.0.1
		├── muri@0.3.1
		├── sliced@0.0.5
		├── hooks@0.2.1
		├── mpath@0.1.1
		├── mpromise@0.4.3
		├── ms@0.1.0
		├── mquery@0.8.0 (debug@0.7.4)
		└── mongodb@1.4.9 (readable-stream@1.0.31, kerberos@0.0.3, bson@0.2.12)
	
### Код

Когда наша среда и формирование шаблонов готовы, мы расширим базовое приложение, созданное с помощью команды **express**, путем добавления файла **task.js**, который содержит модель для ваших задач. Также будет изменен существующий файл **app.js** и создан новый файл контроллера **tasklist.js** для использования этой модели.

#### Создание модели

1. В каталоге **tasklist** создайте новый каталог с именем **models**.

2. В каталоге **models** создайте новый файл с именем **task.js**. Этот файл будет содержать модель для задач, создаваемых приложением.

3. Добавьте следующий код в файл **task.js**:

        var mongoose = require('mongoose'), 
          Schema = mongoose.Schema;

        var TaskSchema = new Schema({
	      itemName      : String, 
	      itemCategory  : String, 
	      itemCompleted : { type: Boolean, default: false },
	      itemDate      : { type: Date, default: Date.now }
        });

        module.exports = mongoose.model('TaskModel', TaskSchema);

5. Сохраните и закройте файл **task.js**.

#### Создание контроллера

1. В каталоге **tasklist/routes** создайте новый файл с именем **tasklist.js** и откройте его в текстовом редакторе.

2. Добавьте следующий код в файл **tasklist.js**. Он загружает модуль mongoose и модель задач, определенную в **task.js**. Функция TaskList используется для создания подключения к серверу MongoDB на основе значения **подключения** и предоставляет методы **showTasks**, **addTask** и **completeTasks**:

		var mongoose = require('mongoose'), 
 		  task = require('../models/task.js');

		module.exports = TaskList;

		function TaskList(connection) {
		  mongoose.connect(connection);
		}

		TaskList.prototype = {
		  showTasks: function(req, res) {
		    task.find({ itemCompleted : false }, function foundTasks(err, items) {
		    res.render('index', { title: 'My ToDo List', tasks: items })
		    });
		  },

		  addTask: function(req,res) {
		    var item = req.body;
		    var newTask = new task();
		    newTask.itemName = item.itemName;
		    newTask.itemCategory = item.itemCategory;
		    newTask.save(function savedTask(err) {
		      if(err) {
		        throw err;
		      }
		    });
		    res.redirect('/');
		  },


		  completeTask: function(req,res) {
		    var completedTasks = req.body;
		    for(taskId in completedTasks) {
		      if(completedTasks[taskId]=='true') {
		        var conditions = { _id: taskId };
		        var updates = { itemCompleted: completedTasks[taskId] };
		        task.update(conditions, updates, function updatedTask(err) {
		          if(err) {
		            throw err;
		          }
		        });
		      }
		    }
		    res.redirect('/');
		  }
		}

3. Сохраните файл **tasklist.js**.

#### Изменение представления индекса

1. Измените каталоги на каталог **views** и откройте файл **index.jade** в текстовом редакторе.

2. Замените содержимое файла **index.jade** на код, приведенный ниже. Он определяет представление для отображения существующих задач, а также форму для добавления новых задач и пометки существующих задач как завершенных.

		h1 #{title}
		form(action="/completetask", method="post")
		  table(border="1")
		    tr
		      td Name
		      td Category
		      td Date
		      td Complete
		    each task in tasks
		      tr
		        td #{task.itemName}
		        td #{task.itemCategory}
		        - var day   = task.itemDate.getDate();
		        - var month = task.itemDate.getMonth() + 1;
		        - var year  = task.itemDate.getFullYear();
		        td #{month + "/" + day + "/" + year}
		        td
		          input(type="checkbox", name="#{task._id}", value="#{!task.itemCompleted}", checked=task.itemCompleted)
		  input(type="submit", value="Update tasks")
		hr
		form(action="/addtask", method="post")
		  table(border="1") 
		    tr
		      td Item Name: 
		      td 
		        input(name="itemName", type="textbox")
		    tr
		      td Item Category: 
		      td 
		        input(name="itemCategory", type="textbox")
		  input(type="submit", value="Add item")

3. Сохраните и закройте файл **index.jade**.

#### Замена файла app.js

1. В каталоге **tasklist** откройте файл **app.js** в текстовом редакторе. Этот файл был создан ранее с помощью команды **express**.
2. Добавьте следующий код в начало файла **app.js**. Он инициализирует функцию **TaskList** со строкой подключения для сервера MongoDB:

		var TaskList = require('./routes/tasklist');
		var taskList = new TaskList(process.env.CUSTOMCONNSTR_MONGOLAB_URI);

 	Обратите внимание на вторую строку. Выполняется доступ к переменной среды, которая содержит сведения о подключении для экземпляра mongo и будет настроена позднее. При наличии локального экземпляра mongo, работающего в целях разработки, может потребоваться временно установить это значение на "localhost" вместо "process.env.CUSTOMCONNSTR_MONGOLAB_URI".

3. Найдите следующие строки:
		
		app.use('/', routes);
		app.use('/users', users);

	Замените их на следующие строки:

		app.get('/', taskList.showTasks.bind(taskList));
		app.post('/addtask', taskList.addTask.bind(taskList));
		app.post('/completetask', taskList.completeTask.bind(taskList));

	Это добавляет функции, определенные в **tasklist.js**, как маршруты.

4. Чтобы инициализировать приложение, перейдите к нижней части файла **app.js** и добавьте следующую строку:

		app.listen(3000); // Listen on port 3000
		module.exports = app;

5. Сохраните файл **app.js**.

<h2><a name="deploy"></a>Развертывание приложения</h2>

Завершив разработку приложения, следует создать веб-сайт Azure для его размещения, настроить этот веб-сайт и развернуть код. Центральной частью этого раздела является использование строки подключения MongoDB (URI). Вы настроите на своем веб-сайте переменную среды с этим универсальным кодом ресурса (URI), чтобы не включать его в используемый код.  URI следует рассматривать как конфиденциальную информацию, так как он содержит учетные данные для подключения к базе данных.

В действиях, описанных в этом разделе, для создания нового веб-сайта Azure используются средства командной строки Azure, а затем для развертывания приложения используется репозиторий Git. Для выполнения этих действий необходимо наличие подписки Azure.

### Установка средств командной строки Azure для Mac и Linux

Для установки средств командной строки используйте следующую команду:
	
	npm install azure-cli -g

Если вы уже установили <strong>Пакет Azure SDK для Node.js</strong> из <a href="/ru-ru/develop/nodejs/">Центр разработчиков Azure</a>, то средства командной строки должны быть уже установлены. Дополнительные сведения см. в разделе <a href="/ru-ru/develop/nodejs/how-to-guides/command-line-tools/">Средство командной строки Azure для Mac и Linux</a>.

Хотя программы командной строки Azure были созданы в основном для пользователей Mac и Linux, они основаны на Node.js и должны работать в любой системе, поддерживающей Node.

### Импорт параметров публикации

Перед использованием средств командной строки с Azure необходимо сначала загрузить файл, содержащий сведения о подписке. Выполните следующие действия, чтобы загрузить и импортировать этот файл.

1. В командной строке введите следующую команду, чтобы запустить браузер, и перейдите на страницу загрузки. В ответ на приглашение выполните вход с использованием учетной записи, связанной с вашей подпиской.

		azure account download
	
	![The download page][download-publishing-settings]
	
	Загрузка файла должна начаться автоматически. Если этого не произошло, можно щелкнуть ссылку в начале страницы, чтобы загрузить файл вручную.

2. После завершения загрузки файла используйте для импорта параметров следующую команду:

		azure account import <path-to-file>
		
	Укажите путь и имя файла параметров публикации, загруженного на предыдущем шаге. Результат выполнения команды должен быть похож на следующее:
	
		info:   Executing command account import
		info:   Found subscription: subscriptionname
		info:   Setting default subscription to: subscriptionname
		warn:   The '/Users/mongolab/.azure/publishSettings.xml' file contains sensitive information.
		warn:   Remember to delete it now that it has been imported.
		info:   Account publish settings imported successfully
		info:   account import command OK


3. По завершении импорта следует удалить файл параметров публикации, так как он больше не нужен и содержит важные сведения, касающиеся подписки Azure.

### Создание нового веб-сайта и передача кода

Создать веб-сайт в Azure очень просто. Если это ваш первый веб-сайт Azure, необходимо использовать портал. Если уже имеется по крайней мере один, перейдите к шагу 7.

1. На портале Azure нажмите кнопку **Создать**.    
![New][button-new]
2. Последовательно выберите пункты **Среда выполнения приложений > Веб-сайт > Быстро создать**. 
![CreateSite][screen-mongolab-newwebsite]
3. Введите префикс URL-адреса. Выберите нужное имя, но помните, что оно должно быть уникальным (скорее всего, имя mymongoapp не будет доступно).
4. Щелкните **Создать веб-сайт**.
5. После завершения создания веб-сайта щелкните его имя в списке веб-сайтов. Появится панель мониторинга веб-сайта.  
![WebSiteDashboard][screen-mongolab-websitedashboard]
6. Щелкните **Настройка развертывания из системы управления версиями** в разделе **Сводка**, выберите GitHub и введите требуемые имя пользователя и пароль git. Этот пароль будет использоваться при передаче данных на веб-сайт (см. шаг 9).  
7. Если веб-сайт был создан в соответствии с указанными выше действиями, то следующая команда приведет к завершению процесса. Но если у вас уже есть несколько веб-сайтов Azure, вы можете пропустить описанные выше шаги и создать новый веб-сайт, используя ту же команду. В каталоге проекта **tasklist** выполните следующие действия: 

		azure site create myuniquesitename --git  
	Замените имя myuniquesitename уникальным именем, которое следует использовать в качестве имени веб-сайта. Если веб-сайт создается в рамках этой команды, появится запрос на указание центра обработки данных, в котором будет размещен этот сайт. Выберите центр обработки данных, который географически расположен неподалеку от вашей базы данных MongoLab.
	
	Параметр "--git" обеспечит создание:
	A. локального репозитория Git в папке **tasklist**, если таковой не существует.
	А. [удаленного репозитория Git] с именем "azure", который будет использоваться для публикации приложения в Azure.
	A. файла [iisnode.yml], который содержит настройки, используемые Azure для размещения приложений узла.
	A. файла .gitignore, который используется для предотвращения публикации в .git папки node-modules.  
	  
	После выполнения этой команды должен появиться результат, похожий на следующий. Обратите внимание, что строка, начинающаяся с **Created website at** (Созданный веб-сайт по адресу), содержит URL-адрес веб-сайта.

		info:   Executing command site create
		info:   Using location southcentraluswebspace
		info:   Executing `git init`
		info:   Creating default web.config file
		info:   Creating a new web site
		info:   Created web site at  mongodbtasklist.azurewebsites.net
		info:   Initializing repository
		info:   Repository initialized
		info:   Executing `git remote add azure http://gitusername@myuniquesitename.azurewebsites.net/mongodbtasklist.git`
		info:   site create command OK

8. Используйте следующие команды для добавления и последующего сохранения файлов в локальном репозитории Git:

		git add .
		git commit -m "adding files"

9. Передача кода:

		git push azure master  
	При принудительной отправке последних изменений репозитория Git на веб-сайт Azure необходимо указать, что целевой ветвью, используемой для контента этого веб-сайта, является **master**. Если появится приглашение для ввода пароля, введите пароль, созданный ранее при настройке публикации git для вашего веб-сайта.
	
	Должен появиться результат, аналогичный приведенному ниже. По мере выполнения развертывания Azure будет загружать все модули npm. 

		Counting objects: 17, done.
		Delta compression using up to 8 threads.
		Compressing objects: 100% (13/13), done.
		Writing objects: 100% (17/17), 3.21 KiB, done.
		Total 17 (delta 0), reused 0 (delta 0)
		remote: New deployment received.
		remote: Updating branch 'master'.
		remote: Preparing deployment for commit id 'ef276f3042'.
		remote: Preparing files for deployment.
		remote: Running NPM.
		...
		remote: Deploying Web.config to enable Node.js activation.
		remote: Deployment successful.
		To https://username@mongodbtasklist.azurewebsites.net/MongoDBTasklist.git
 		 * [new branch]      master -> master
 
You're almost done!

### Настройка среды
Помните process.env.CUSTOMCONNSTR\_MONGOLAB\_URI в коде? Нам нужно указать в этой переменной среды значение, полученное в Azure во время подготовки базы данных.

#### Получение строки подключения к MongoLab

[WACOM.INCLUDE [howto-get-connectioninfo-mongolab](../includes/howto-get-connectioninfo-mongolab.md)]

#### Добавление строки подключения в число переменных среды веб-сайта

[WACOM.INCLUDE [howto-save-connectioninfo-mongolab](../includes/howto-save-connectioninfo-mongolab.md)]

## Готово!

Запустите "azure site browse" из каталога проекта, чтобы автоматически открыть браузер, или откройте браузер и вручную перейдите по URL-адресу веб-сайта (myuniquesite.azurewebsites.net):

![A webpage displaying an empty tasklist][node-mongo-finished]

<h2><a name="manage"></a>Управление базой данных</h2>

[WACOM.INCLUDE [howto-access-mongolab-ui](../includes/howto-access-mongolab-ui.md)]

Поздравляем! Вы только что запустили приложение Node.js на основе размещенной в MongoLab базы данных MongoDB! Теперь, когда создана база данных MongoLab, можно обратиться по адресу [support@mongolab.com](mailto:support@mongolab.com) по любым вопросам или проблемам, связанным с вашей базой данных, а также для получения справки по MongoDB или самому драйверу узла. Удачи!



[screen-mongolab-websitedashboard]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/screen-mongolab-websitedashboard.png
[screen-mongolab-newwebsite]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/screen-mongolab-newwebsite.png
[button-new]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/button-new.png
[button-store]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/button-store.png
[entry-mongolab]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/entry-mongolab.png 
[button-connectioninfo]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/button-connectioninfo.png
[screen-connectioninfo]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/dialog-mongolab_connectioninfo.png
[focus-website-connectinfo]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/focus-mongolab-websiteconnectionstring.png
[provision]: #provision
[create]: #create
[deploy]: #deploy
[manage]: #manage
[Node.js]: http://nodejs.org
[MongoDB]: http://www.mongodb.org
[Git]: http://git-scm.com
[Express]: http://expressjs.com
[Mongoose]: http://mongoosejs.com
[бесплатно]: /ru-ru/pricing/free-trial
[Удаленный репозиторий Git]: http://git-scm.com/docs/git-remote
[azure-sdk-for-node]: https://github.com/WindowsAzure/azure-sdk-for-node
[iisnode.yml]: https://github.com/WindowsAzure/iisnode/blob/master/src/samples/configuration/iisnode.yml
[Средство командной строки Azure для Mac и Linux]: /ru-ru/develop/nodejs/how-to-guides/command-line-tools/
[Центр разработчиков Azure]: /ru-ru/develop/nodejs/
[Создание и развертывание приложения Node.js на веб-сайтах Azure]: /ru-ru/develop/nodejs/tutorials/create-a-website-(mac)/
[Публикация на веб-сайтах Azure с использованием репозитория Git]: /ru-ru/develop/nodejs/common-tasks/publishing-with-git/
[MongoLab]: http://mongolab.com
[Веб-приложение node.js с хранилищем в MongoDB (виртуальная машина)]: /ru-ru/develop/nodejs/tutorials/website-with-mongodb-(mac)/
[node-mongo-finished]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/todo_list_noframe.png
[node-mongo-express-results]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/express_output.png
[download-publishing-settings]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/azure-account-download-cli.png
[import-publishing-settings]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/azureimport.png
[mongolab-create]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/mongolab-create.png
[mongolab-view]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/mongolab-view.png




<!--HONumber=35.2-->

<properties
	pageTitle="Создание веб-приложения Node.js в Azure с MongoDB с помощью надстройки MongoLab"
	description="Узнайте, как в службе приложений Azure создать веб-приложение Node.js, которое подключается к экземпляру MongoDB, размещенному в MongoLab."
	tags="azure-classic-portal"
	services="app-service\web, virtual-machines"
	documentationCenter="nodejs"
	authors="chrisckchang"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="app-service-web"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="nodejs"
	ms.topic="article"
	ms.date="04/20/2014"
	ms.author="mwasson"/>






# Создание веб-приложения Node.js в Azure с MongoDB с помощью надстройки MongoLab


<p><em>Автор: Эрик Седор (Eric Sedor), MongoLab</em></p>

Приветствуем вас, искатели приключений! Добро пожаловать в MongoDB как услуга. Изучив данный учебник, вы научитесь:

1. [Подготавливать базу данных][provision]. Надстройка [MongoLab](http://mongolab.com), доступная в Azure Marketplace, позволяет получать доступ к базе данных MongoDB, размещенной в облаке Azure, и управляется облачной платформой базы данных MongoLab.
2. [Создание приложения][create] – это простое приложение Node.js для обслуживания списка задач.
3. [Развертывать приложение][deploy]. Объединив вместе несколько обработчиков конфигурации, мы значительно упрощаем перемещение кода в [веб-приложения службы приложений Azure](http://go.microsoft.com/fwlink/?LinkId=529714).
4. [Управлять базой данных][manage] — наконец, мы покажем портал управления базами данных MongoLab, где можно без труда искать, визуализировать и изменять данные.

Если во время работы с этим учебником у вас возникнут какие-либо вопросы, обращайтесь с ними по адресу [support@mongolab.com](mailto:support@mongolab.com).

Прежде чем продолжить, убедитесь, что установлены следующие компоненты:

* [Node.js] версии не ниже 0.10.29;

* [Git].

[AZURE.INCLUDE [create-account-and-websites-note](../../includes/create-account-and-websites-note.md)]

## Быстрый запуск
Если у вас есть опыт работы с Магазином Azure, воспользуйтесь этим разделом, чтобы быстро начать работу. В противном случае изучите приведенный ниже раздел [Подготовка базы данных][provision].

1. Откройте Azure Marketplace, щелкнув **Создать** > **Marketplace**.  
<!-- ![Store][button-store] -->
2. Щелкните надстройку **MongoLab**.  
![MongoLab][entry-mongolab]
3. Щелкните надстройку **MongoLab** в списке надстроек, а затем выберите пункт **Сведения о подключении**.  
![ConnectionInfoButton][button-connectioninfo]  
4. Скопируйте **MONGOLAB_URI** в буфер обмена.  
![ConnectionInfoScreen][screen-connectioninfo]

	>[AZURE.NOTE]Этот универсальный код ресурса (URI) содержит имя пользователя базы данных и пароль. Считайте это конфиденциальной информацией и не передавайте ее.

5. Добавьте значение в список **Строки подключения** в меню **Конфигурация** веб-приложения в службе приложений Azure:  
![WebAppConnectionStrings][focus-website-connectinfo]
6. В поле **Имя** введите **MONGOLAB_URI**.
7. В качестве **Значение** вставьте строку подключения, полученную в предыдущем разделе.
8. В раскрывающемся списке «Тип» выберите пункт **Настраиваемый** (вместо значения по умолчанию **SQLAzure**).
9. Выполните команду `npm install mongoose`, чтобы скачать Mongoose — драйвер узла MongoDB.
10. Настройте обработчик в коде, чтобы получить универсальный код ресурса (URI) подключения MongoLab из переменной среды и выполнить подключение:

        var mongoose = require('mongoose');  
 		...
 		var connectionString = process.env.CUSTOMCONNSTR_MONGOLAB_URI
 		...
 		mongoose.connect(connectionString);

Примечание. Azure добавляет префикс **CUSTOMCONNSTR_** в исходно объявленную строку подключения, поэтому в коде присутствует ссылка на **CUSTOMCONNSTR_MONGOLAB_URI.**, а не на **MONGOLAB_URI**.

Теперь перейдем к полному учебнику...

<a name="provision"></a>
## Подготовка базы данных

[AZURE.INCLUDE [howto-provision-mongolab](../../includes/howto-provision-mongolab.md)]

<a name="create"></a>
## Создание приложения

В этом разделе вы настроите среду разработки и создадите код для веб-приложения простого списка задач с помощью Node.js, Express и MongoDB. Модуль [Express] предоставляет платформу View Controller для Node, а драйвер[Mongoose] предназначен для взаимодействия с MongoDB.

### Настройка

#### Формирование шаблонов и установка модулей

1. В командной строке создайте каталог **tasklist** и перейдите в него. Они и будет являться каталогом вашего проекта.
2. Введите следующую команду для установки Express.

		npm install express -g

	Код `-g` указывает на глобальный режим, который используется для предоставления доступа к модулю <strong>express</strong> без указания пути к каталогу. Если появится сообщение об ошибке <strong>Error: EPERM, chmod '/usr/local/bin/express'</strong>, используйте режим <strong>sudo</strong>, чтобы запустить диспетчер NPM на более высоком, привилегированном уровне.

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

3. Для создания шаблонов, которые будут использоваться для данного приложения, используйте команду **express**:

    	express

    Обратите внимание, что в этом учебнике используется Express версии 4.x.x. Если в системе уже установлен генератор приложений Express 3, сначала удалите его.

    	npm uninstall -g express

    Теперь установите новый генератор для версии 4.x.x.

    	npm install -g express-generator

	Результаты выполнения команды **express** будут выглядеть приблизительно так:

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

4. Введите следующую команду для установки модулей, описанных в файле **package.json**:

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

	Файл **package.json** является одним из файлов, созданных командой **express**. Этот файл содержит список дополнительных модулей, необходимых для приложения Express. Позднее, при развертывании этого приложения в веб-приложениях службы приложений Azure, с помощью этого файла можно будет определить, какие модули необходимо установить в Azure для поддержки приложения.

5. Затем введите следующую команду, чтобы локально установить модуль Mongoose, а также сохранить соответствующую запись в файл **package.json**:

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

После подготовки среды и формирования шаблонов мы расширим базовое приложение, созданное с помощью команды **express**, добавив файл **task.js**, который содержит модель для ваших задач. Также будет изменен существующий файл **app.js** и создан новый файл контроллера **tasklist.js** для использования этой модели.

#### Создание модели

1. В каталоге **tasklist** создайте новый каталог с именем **models**.

2. В каталоге **models** создайте новый файл с именем **task.js**. Этот файл будет содержать модель для задач, создаваемых приложением.

3. Добавьте в файл **task.js** следующий код:

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

2. Добавьте в **tasklist.js** следующий код. Он загружает модуль mongoose и модель задач, определенную в **task.js**. Функция TaskList используется для создания подключения к серверу MongoDB на основе значения **connection** и предоставляет методы **showTasks**, **addTask** и **completeTasks**:

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

2. Заменит содержимое файла **index.jade** на код, приведенный ниже. Он определяет представление для отображения существующих задач, а также форму для добавления новых задач и пометки существующих задач как завершенных.

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

 	Обратите внимание на вторую строку. Выполняется доступ к переменной среды, которая содержит сведения о подключении для экземпляра mongo и будет настроена позднее. При наличии локального экземпляра Mongo, используемого в целях разработки, может понадобиться временно задать для этого параметра значение localhost вместо `process.env.CUSTOMCONNSTR_MONGOLAB_URI`.

3. Найдите следующие строки.

		app.use('/', routes);
		app.use('/users', users);

	Замените их на следующие строки:

		app.get('/', taskList.showTasks.bind(taskList));
		app.post('/addtask', taskList.addTask.bind(taskList));
		app.post('/completetask', taskList.completeTask.bind(taskList));

	Это добавляет функции, определенные в **tasklist.js**, как маршруты.

4. Чтобы инициализировать приложение, перейдите в нижнюю часть файла **app.js** и добавьте следующую строку:

		app.listen(3000); // Listen on port 3000
		module.exports = app;

5. Сохраните файл **app.js**.

<a name="deploy"></a>
## Развертывание приложения

После разработки приложения пришло время создать веб-приложение в службе приложений Azure для его размещения и настройки, а также развертывания кода. Центральной частью этого раздела является использование строки подключения MongoDB (URI). Вы собираетесь настроить переменную среды в своем веб-приложении с использованием этого универсального кода ресурса (URI), чтобы не включать URI в свой код. URI следует рассматривать как конфиденциальную информацию, так как он содержит учетные данные для подключения к вашей базе данных.

В действиях, описанных в этом разделе, для создания нового веб-приложения в службе приложений Azure используются программы командной строки Azure для Mac, Linux и Windows, а затем для развертывания приложения применяется Git. Для выполнения этих действий необходимо наличие подписки Azure.

### Установка интерфейса командной строки Azure

Чтобы установить интерфейс командной строки Azure, используйте следующую команду:

	npm install azure-cli -g

Если вы уже установили пакет <strong>SDK для Azure для Node.js</strong> из <a href="/develop/nodejs/">Центра разработчиков Azure</a>, то интерфейс командной строки Azure должен быть уже установлен. Дополнительную информацию см. в статье об <a href="../virtual-machines-command-line-tools.md">интерфейсе командной строки Azure</a>.

Хотя инструменты интерфейса командной строки Azure были созданы в основном для пользователей Mac и Linux, они основаны на Node.js и должны работать в любой системе, поддерживающей Node.

### Импорт параметров публикации

Перед использованием интерфейса командной строки Azure необходимо сначала загрузить файл, содержащий информацию о подписке. Выполните следующие действия, чтобы загрузить и импортировать этот файл.

1. В командной строке введите следующую команду, чтобы запустить браузер, и перейдите на страницу загрузки. В ответ на приглашение выполните вход с использованием учетной записи, связанной с вашей подпиской.

		azure account download

	![Страница загрузки][download-publishing-settings]

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

### Создание веб-приложения и передача кода

Создать веб-приложение в службе приложений Azure очень просто. Если это ваше первое веб-приложение Azure, необходимо использовать портал. Если уже имеется по крайней мере один, перейдите к шагу 7.

1. На портале Azure нажмите кнопку **Создать**.  
![Создать][button-new]
2. Выберите **Среда выполнения приложений > Веб-приложение > Быстрое создание**.
<!-- ![Create Web App][screen-mongolab-newwebsite] -->
3. Введите префикс URL-адреса. Выберите нужное имя, но помните, что оно должно быть уникальным (скорее всего, имя mymongoapp не будет доступно).
4. Щелкните **Создать веб-приложение**.
5. По завершении создания веб-приложения щелкните его имя в списке веб-приложений. Откроется панель мониторинга веб-приложения.
<!-- ![Web App Dashboard][screen-mongolab-websitedashboard] -->
6. Щелкните **Настройка развертывания из системы управления версиями** в разделе **Сводка**, выберите GitHub и введите требуемые имя пользователя и пароль Git. Этот пароль будет использоваться при передаче данных в веб-приложение (см. шаг 9).  
7. Если веб-приложение создано путем выполнения указанных выше действий, то следующая команда приведет к завершению процесса. Однако если у вас уже есть несколько веб-приложений, вы можете пропустить описанные выше шаги и создать веб-приложение, используя ту же команду. В каталоге проекта **tasklist** выполните следующие действия:

		azure site create myuniquewebappname --git  
	Замените "myuniquewebappname" на уникальное имя веб-приложения. Если веб-приложение создается в рамках этой команды, вам будет предложено указать центр обработки данных, в котором оно будет размещено. Выберите центр обработки данных, расположенный недалеко от вашей базы данных MongoLab.

	Параметр `--git` создаст: * локальный репозиторий Git в папке **tasklist**, если такого репозитория еще нет; * [удаленный репозиторий Git] с именем azure, который будет использоваться для публикации приложения в Azure; * файл [iisnode.yml] с параметрами, используемыми Azure для размещения приложений Node; * GITIGNORE-файл, используемый для предотвращения публикации в .git папки node-modules.

	После выполнения этой команды должен появиться результат, похожий на следующий. Обратите внимание, что строка, начинающаяся с **Created site at** (Создан сайт по адресу), содержит URL-адрес веб-приложения.

		info:   Executing command site create
		info:   Using location southcentraluswebspace
		info:   Executing `git init`
		info:   Creating default web.config file
		info:   Creating a new web site
		info:   Created web site at  mongodbtasklist.azurewebsites.net
		info:   Initializing repository
		info:   Repository initialized
		info:   Executing `git remote add azure http://gitusername@myuniquewebappname.azurewebsites.net/mongodbtasklist.git`
		info:   site create command OK

8. Используйте следующие команды для добавления и последующего сохранения файлов в локальном репозитории Git:

		git add .
		git commit -m "adding files"

9. Передача кода:

		git push azure master  

	При принудительной отправке последних изменений репозитория Git в веб-приложение службы приложений Azure необходимо указать, что целевой ветвью, используемой для контента этого веб-приложения, является **master**. Если вам будет предложено ввести пароль, введите пароль, который вы создали ранее при настройке публикации Git для вашего веб-приложения.

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

Все почти готово!

### Настройка среды
Помните о process.env.CUSTOMCONNSTR_MONGOLAB_URI в коде? Нам нужно указать в этой переменной среды значение, полученное в Azure во время подготовки базы данных .

#### Получение строки подключения к MongoLab

[AZURE.INCLUDE [howto-get-connectioninfo-mongolab](../../includes/howto-get-connectioninfo-mongolab.md)]

#### Добавление строки подключения в число переменных среды веб-приложения

[AZURE.INCLUDE [howto-save-connectioninfo-mongolab](../../includes/howto-save-connectioninfo-mongolab.md)]

## Готово!

Выполните команду `azure site browse` в каталоге проекта, чтобы браузер открылся автоматически, или вручную откройте браузер и перейдите по URL-адресу веб-приложения (myuniquewebappname.azurewebsites.net):

![Веб-страница, показывающая пустой список задач][node-mongo-finished]

<a name="manage"></a>
## Управление базой данных

[AZURE.INCLUDE [howto-access-mongolab-ui](../../includes/howto-access-mongolab-ui.md)]

Поздравляем! Вы только что запустили приложение Node.js на основе размещенной в MongoLab базы данных MongoDB! Теперь, когда база данных MongoLab создана, вы можете обращаться по адресу [support@mongolab.com](mailto:support@mongolab.com) по любым вопросам, связанным с вашей базой данных, а также для получения справки по MongoDB или непосредственно драйверу узла. Удачи!

## Изменения
* Указания по изменениям при переходе от веб-сайтов к службе приложений см. в разделе [Служба приложений Azure и ее влияние на существующие службы Azure](http://go.microsoft.com/fwlink/?LinkId=529714).
* Руководство по смене старого портала на новый см. в статье [Краткий справочник по навигации на портале Azure](http://go.microsoft.com/fwlink/?LinkId=529715).

>[AZURE.NOTE]Если вы хотите приступить к работе со службой приложений Azure до создания учетной записи Azure, перейдите к разделу [Пробное использование службы приложений](http://go.microsoft.com/fwlink/?LinkId=523751), где вы можете быстро создать кратковременное веб-приложение начального уровня в службе приложений. Никаких кредитных карт и обязательств.


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
[for free]: /pricing/free-trial
[удаленный репозиторий Git]: http://git-scm.com/docs/git-remote
[azure-sdk-for-node]: https://github.com/WindowsAzure/azure-sdk-for-node
[iisnode.yml]: https://github.com/WindowsAzure/iisnode/blob/master/src/samples/configuration/iisnode.yml
[Azure CLI]: ../virtual-machines-command-line-tools.md
[Azure Developer Center]: /develop/nodejs/
[Create and deploy a Node.js application to Azure Web Sites]: /develop/nodejs/tutorials/create-a-website-(mac)/
[Continuous deployment using GIT in Azure App Service]: web-sites-publish-source-control.md
[MongoLab]: http://mongolab.com
[Node.js Web Application with Storage on MongoDB (Virtual Machine)]: /develop/nodejs/tutorials/website-with-mongodb-(mac)/
[node-mongo-finished]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/todo_list_noframe.png
[node-mongo-express-results]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/express_output.png
[download-publishing-settings]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/azure-account-download-cli.png
[import-publishing-settings]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/azureimport.png
[mongolab-create]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/mongolab-create.png
[mongolab-view]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/mongolab-view.png
 

<!---HONumber=August15_HO6-->
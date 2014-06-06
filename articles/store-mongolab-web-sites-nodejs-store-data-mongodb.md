<properties linkid="develop-nodejs-tutorials-web-site-with-mongodb-mongolab" urlDisplayName="Веб-сайт с MongoDB" pageTitle="Веб-сайт Node.js с MongoDB на MongoLab - Azure" metaKeywords="" description="Узнайте, как создать веб-сайт Node.js Azure, который подключается к экземпляру MongoDB, размещенному в MongoLab." metaCanonical="" services="web-sites,virtual-machines" documentationCenter="Node.js" title="Создание приложения Node.js в Azure с MongoDB с использованием надстройки MongoLab" authors=""  solutions="" writer="" manager="" editor="" />





# Создание приложения Node.js в Azure с MongoDB с помощью надстройки MongoLab

<p><em>Автор: Эрик Седор, MongoLab</em></p>

Поздравление, искатели приключений! Добро пожаловать в MongoDB как услуга. Изучив данный учебник, вы научитесь:

1. [Подготавливать базу данных][provision] – надстройка [MongoLab](http://mongolab.com) в Магазине Azure предоставляет вам базу данных MongoDB, которая размещена в облаке Azure и управляется облачной платформой базы данных MongoLab.
1. [Создание приложения][create] – это простое приложение Node.js для обслуживания списка задач.
1. [Развертывание приложения][deploy] – объединяя вместе несколько обработчиков конфигурации, мы значительно упрощаем перемещение кода.
1. [Управление базой данных][manage] – наконец, мы покажем портал управления базами данных MongoLab, где можно без труда искать, визуализировать и изменять данные.

При работе с этим учебником можно в любое время отправить сообщение электронной почты на адрес [support@mongolab.com](mailto:support@mongolab.com), если у вас возникнут какие-либо вопросы.

Прежде чем продолжить, убедитесь, что установлены следующие компоненты:

* [Node.js] версии 0.8.14+

* [Git]

[WACOM.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

## Быстрый запуск
Если у вас есть опыт работы с Магазином Azure, воспользуйтесь этим разделом для быстрого запуска проекта. В противном случае изучите приведенный ниже раздел [Подготовка базы данных][provision].
 
1. Откройте Магазин Azure.  
![Магазин][button-store]
1. Щелкните надстройку MongoLab.  
![MongoLab][entry-mongolab]
1. Щелкните надстройку MongoLab в списке надстроек, затем выберите пункт **Сведения о подключении**.  
![ConnectionInfoButton][button-connectioninfo]  
1. Скопируйте MONGOLAB_URI в буфер обмена.  
![ConnectionInfoScreen][screen-connectioninfo]  
**Этот URI содержит имя пользователя и пароль вашей базы данных.  Считайте это конфиденциальной информацией и не передавайте ее.**
1. Добавьте значение в список "Строки подключения" в меню "Конфигурация" веб-приложения Azure:  
![WebSiteConnectionStrings][focus-website-connectinfo]
1. В поле **Имя** введите MONGOLAB\_URI.
1. В качестве **Значение** вставьте строку подключения, полученную в предыдущем разделе.
1. В раскрывающемся списке выберите пункт **Настраиваемый** (вместо значения по умолчанию **SQLAzure**).
1. Выполните `npm install mongoose` для получения Mongoose, драйвера узла MongoDB.
1. Настройте обработчик в коде, чтобы получить URI подключения MongoLab из переменной среды и выполнить подключение:

        var mongoose = require('mongoose');  
 		...
 		var connectionString = process.env.CUSTOMCONNSTR_MONGOLAB_URI
 		...
 		mongoose.connect(connectionString);

Обратите внимание! Azure добавляет префикс **CUSTOMCONNSTR\_** к исходно объявленной строке подключения, поэтому в коде имеется ссылка на **CUSTOMCONNSTR\_MONGOLAB\_URI.**, а не на **MONGOLAB\_URI**.

Теперь перейдем к полному учебнику...

<h2><a name="provision"></a>Подготовка базы данных</h2>

[WACOM.INCLUDE [howto-provision-mongolab](../includes/howto-provision-mongolab.md)]

<h2><a name="create"></a>Создание приложения</h2>

В этом разделе вы настроите среду разработки и создайте код для веб-приложения базового списка задач с помощью Node.js, Express и MongoDB. [Express] предоставляет инфраструктуру просмотра контроллера для узла, тогда как [Mongoose] — это драйвер для связи с MongoDB на узле.

### Настройка

#### Формирование шаблонов и установка модулей

1. В командной строке создайте каталог **tasklist** и перейдите в него. Они и будет являться каталогом вашего проекта.
2. Введите следующую команду для установки Express.

		npm install express -g
 
	`-g` указывает на глобальный режим, который используется для предоставления доступа к модулю <strong>express</strong> без указания пути к каталогу. При получении <strong>Error: EPERM, chmod '/usr/local/bin/express'</strong> используйте режим <strong>sudo</strong> для запуска npm на более высоком, привилегированном уровне.

    Результат этой команды должен выглядеть аналогично следующему:

		express@3.3.4 C:\Users\larryfr\AppData\Roaming\npm\node_modules\express
		├── methods@0.0.1
		├── fresh@0.1.0
		├── cookie-signature@1.0.1
		├── range-parser@0.0.4
		├── buffer-crc32@0.2.1
		├── cookie@0.1.0
		├── debug@0.7.2
		├── mkdirp@0.3.5
		├── commander@1.2.0 (keypress@0.1.0)
		├── send@0.1.3 (mime@1.2.9)
		└── connect@2.8.4 (uid2@0.0.2, pause@0.0.1, qs@0.6.5, bytes@0.2.0, formidable@1.0.14)
 
3. Для создания шаблонов, которые будут использоваться для данного приложения, используйте команду **express**:

    express

	Результат этой команды должен выглядеть аналогично следующему:

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
   		create : ./views
   		create : ./views/layout.jade
   		create : ./views/index.jade
		
   		dont forget to install dependencies:
   		$ cd . && npm install

	После выполнения этой команды в каталоге **tasklist** должно появиться несколько новых папок и файлов.
	
4. Введите следующую команду для установки модулей, описанных в файле **package.json**:

        npm install

    Результат этой команды должен выглядеть аналогично следующему:

		express@3.3.4 node_modules\express
		├── methods@0.0.1
		├── fresh@0.1.0
		├── range-parser@0.0.4
		├── cookie-signature@1.0.1
		├── buffer-crc32@0.2.1
		├── cookie@0.1.0
		├── debug@0.7.2
		├── mkdirp@0.3.5
		├── commander@1.2.0 (keypress@0.1.0)
		├── send@0.1.3 (mime@1.2.9)
		└── connect@2.8.4 (uid2@0.0.2, pause@0.0.1, qs@0.6.5, bytes@0.2.0, formidable@1.0.14)

		jade@0.33.0 node_modules\jade
		├── character-parser@1.0.2
		├── mkdirp@0.3.5
		├── commander@1.2.0 (keypress@0.1.0)
		├── with@1.1.0 (uglify-js@2.3.6)
		├── constantinople@1.0.1 (uglify-js@2.3.6)
		├── transformers@2.0.1 (promise@2.0.0, css@1.0.8, uglify-js@2.2.5)
		└── monocle@0.1.48 (readdirp@0.2.5)

	Файл **package.json** является одним из файлов, созданных командой **express**. Этот файл содержит список дополнительных модулей, необходимых для приложения Express. Позднее, при развертывании этого приложения на веб-сайте Azure, этот файл будет использоваться, чтобы определить, какие модули должны быть установлены в Azure для поддержки приложения.

5. Затем введите следующую команду, чтобы локально установить модуль Mongoose, а также сохранить соответствующую запись в файл **package.json**:

		npm install mongoose --save

	Результат этой команды должен выглядеть аналогично следующему:

		mongoose@3.6.15 node_modules\mongoose
		├── regexp-clone@0.0.1
		├── sliced@0.0.3
		├── muri@0.3.1
		├── hooks@0.2.1
		├── mpath@0.1.1
		├── ms@0.1.0
		├── mpromise@0.2.1 (sliced@0.0.4)
		└── mongodb@1.3.11 (bson@0.1.9, kerberos@0.0.3)

    На любое сообщение об установке анализатора bson C++ можно не обращать внимания.
	
### Код

После подготовки среды и формирования шаблонов, мы расширим базовое приложение, созданное с помощью команды **express**, добавив файл **task.js**, который содержит модель для ваших задач. Также будет изменено существующее приложение **app.js** и создан новый файл контроллера **tasklist.js** для использования этой модели.

#### Создание модели

1. В каталоге **tasklist** создайте новый каталог с именем **models**

2. В каталоге **models** создайте новый файл с именем **task.js** Этот файл будет содержать модель для задач, создаваемых приложением.

3. Добавьте в файл **task.js** следующий код:

        var mongoose = require('mongoose')
	      , Schema = mongoose.Schema;

        var TaskSchema = new Schema({
	        itemName      : String
	      , itemCategory  : String
	      , itemCompleted : { type: Boolean, default: false }
	      , itemDate      : { type: Date, default: Date.now }
        });

        module.exports = mongoose.model('TaskModel', TaskSchema);

5. Сохраните и закройте файл **task.js**.

#### Создание контроллера

1. В каталоге **tasklist/routes** создайте новый файл с именем **tasklist.js** и откройте его в текстовом редакторе.

2. Добавьте следующий код в **tasklist.js**. Он загружает модуль mongoose и модель задач, определенную в **task.js** Функция TaskList используется для создания подключения к серверу MongoDB на основе значения **connection** и предоставляет методы **showTasks**, **addTask** и **completeTasks**:

		var mongoose = require('mongoose')
	      , task = require('../models/task.js');

		module.exports = TaskList;

		function TaskList(connection) {
  		  mongoose.connect(connection);
		}

		TaskList.prototype = {
  		  showTasks: function(req, res) {
      	    task.find({itemCompleted: false}, function foundTasks(err, items) {
      		  res.render('index',{title: 'My ToDo List ', tasks: items})
    		});
  		  },

  		  addTask: function(req,res) {
    		var item = req.body.item;
    		newTask = new task();
    		newTask.itemName = item.name;
    		newTask.itemCategory = item.category;
    		newTask.save(function savedTask(err){
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
		        input(name="item[name]", type="textbox")
		    tr
		      td Item Category: 
		      td 
		        input(name="item[category]", type="textbox")
		  input(type="submit", value="Add item")

3. Сохраните и закройте файл **index.jade**.

#### Замена app.js

1. В каталоге **tasklist** откройте файл **app.js** в текстовом редакторе. Этот файл был создан ранее с помощью команды **express**.
1. Добавьте следующий код в начало файла **app.js**. Он проинициализирует **TaskList** со строкой подключения для сервера MongoDB:

		var TaskList = require('./routes/tasklist');
		var taskList = new TaskList(process.env.CUSTOMCONNSTR_MONGOLAB_URI);

 	Обратите внимание на вторую строку. Выполняется доступ к переменной среды, которая содержит сведения о подключении для экземпляра mongo и будет настроена позднее. При наличии локального экземпляра Mongo, работающего в целях разработки, может понадобиться временно задать для этого параметра значение localhost вместо `process.env.CUSTOMCONNSTR_MONGOLAB_URI`.

2. Найдите строки, начинающиеся с `app.get` и замените их следующими строками:

		app.get('/', taskList.showTasks.bind(taskList));
		app.post('/addtask', taskList.addTask.bind(taskList));
		app.post('/completetask', taskList.completeTask.bind(taskList));

	Это добавляет функции, определенные в **tasklist.js** как маршруты.

4. Сохраните файл **app.js**.

<h2><a name="deploy"></a>Развертывание приложения</h2>

После разработки приложения пришло время создать веб-сайт Azure для его размещения, настроить этот веб-сайт и развернуть код. Центральной частью этого раздела является использование строки подключения MongoDB (URI). Вы собираетесь настроить переменную среды на своем веб-сайте с использованием этого URI, чтобы не включать URI в свой код.  URI следует рассматривать как конфиденциальную информацию, так как он содержит учетные данные для подключения к базе данных.

Для выполнения действий, приведенных в этом разделе, используются средства командной строки Azure, с помощью которых можно создать новый веб-сайт Azure, а затем использовать Git для развертывания приложения. Для выполнения этих шагов необходимо наличие подписки Azure.

### Установка средств командной строки Azure для Mac и Linux

Для установки средств командной строки используйте следующую команду:
	
	npm install azure-cli -g

Если вы уже установили <strong>Azure SDK для Node.js</strong>, используя <a href="/ru-ru/develop/nodejs/">Центр разработчиков Azure</a>, то средства командной строки должны быть уже установлены. Дополнительные сведения см. в статье <a href="/ru-ru/develop/nodejs/how-to-guides/command-line-tools/">Средства командной строки Azure для Mac и Linux</a>.

Хотя средства командной строки Azure были созданы в основном для пользователей Mac и Linux, они основаны на Node.js и должны работать в любой системе, поддерживающей работу Node.

### Импорт параметров публикации

Перед использованием средств командной строки Azure необходимо сначала загрузить файл, содержащий сведения о подписке. Выполните следующие действия, чтобы загрузить и импортировать этот файл.

1. В командной строке введите следующую команду, чтобы запустить браузер, и перейдите на страницу загрузки. В ответ на приглашение выполните вход с использованием учетной записи, связанной с вашей подпиской.

		azure account download
	
	![Страница загрузки][download-publishing-settings]
	
	Загрузка файла должна начаться автоматически. Если этого не произошло, можно щелкнуть ссылку в начале страницы, чтобы загрузить файл вручную.

3. После завершения загрузки файла используйте для импорта параметров следующую команду:

		azure account import <path-to-file>
		
	Укажите путь и имя файла параметров публикации, загруженного на предыдущем шаге. Результат выполнения команды должен быть похож на следующее:
	
		info:   Executing command account import
		info:   Found subscription: subscriptionname
		info:   Setting default subscription to: subscriptionname
		warn:   The '/Users/user1/.azure/publishSettings.xml' file contains sensitive information.
		warn:   Remember to delete it now that it has been imported.
		info:   Account publish settings imported successfully
		info:   account import command OK


4. По завершении импорта следует удалить файл параметров публикации, так как он больше не нужен и содержит важные сведения, касающиеся подписки Azure.

### Создание нового веб-сайта и передача кода

Создание веб-сайта в Azure не представляет труда. Если это ваш первый веб-сайт Azure, необходимо использовать портал. Если уже имеется по крайней мере один, перейдите к шагу 7.

1. На портале Azure щелкните **Создать**.    
![Создать][button-new]
1. Выберите **Среда выполнения приложений > Веб-сайт > Быстро создать**. 
![CreateSite][screen-mongolab-newwebsite]
1. Введите префикс URL-адреса. Выберите предпочтительное имя, однако помните, что оно должно быть уникальным (скорее всего, 'mymongoapp' не будет доступным).
1. Щелкните **Создать веб-сайт**.
1. После завершения создания веб-сайта, щелкните имя веб-сайта в списке веб-сайтов. Откроется панель мониторинга веб-сайта.  
![WebSiteDashboard][screen-mongolab-websitedashboard]
1. Щелкните **Настроить публикацию Git** в разделе **сводка** и введите имя пользователя и пароль пользователя git. Этот пароль будет использоваться при передаче данных на веб-сайт (см. шаг 9).  
1. Если веб-сайт был создан в соответствии с указанными выше действиями, то следующая команда приведет к завершению процесса. Тем не менее при наличии более чем одного веб-сайта Azure можно пропустить описанные выше шаги и создать новый веб-сайт, используя ту же команду. В каталоге проекта **tasklist** выполните следующие действия: 

		azure site create myuniquesitename --git  
	Замените 'myuniquesitename' на уникальное имя созданного веб-сайта. Если веб-сайт создается в качестве этой команды, появится запрос на указание центра обработки данных, в котором будет размещен этот сайт. Выберите центр обработки данных, который географически расположен невдалеке от вашей базы данных MongoLab.
	
	Параметры `--git` приведет к созданию
	A. локального репозитория Git в папке **tasklist**, если таковой не существует.
	A. [удаленного Git] с именем 'azure', который будет использоваться для публикации приложения в Azure.
	A. файла [iisnode.yml], который содержит настройки, используемые Azure для размещения приложений узла.
	A. файла .gitignore, который используется для предотвращения публикации в .git папки node-modules.  
	  
	После выполнения этой команды должен появиться результат, похожий на следующий. Обратите внимание, что строка, начинающаяся с **Created web site at** (Создан веб-сайт) содержит URL-адрес веб-сайта.

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

1. Используйте следующие команды для добавления и последующего сохранения файлов в локальном репозитории Git:

		git add .
		git commit -m "adding files"

1. Передача кода:

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
 
Все почти готово!

### Настройка среды
Помните process.env.CUSTOMCONNSTR\_MONGOLAB\_URI в коде? Нам нужно заполнить эту переменную среды значением, предоставленным в Azure во время подготовки базы данных MongoLab.

#### Получение строки подключения к MongoLab

[WACOM.INCLUDE [howto-get-connectioninfo-mongolab](../includes/howto-get-connectioninfo-mongolab.md)]

#### Добавьте строку соединения к переменным среды веб-сайта

[WACOM.INCLUDE [howto-save-connectioninfo-mongolab](../includes/howto-save-connectioninfo-mongolab.md)]

## Все готово!

Выполните `azure site browse` в каталоге проекта для автоматического открытия браузера или откройте браузер и вручную перейдите к URL-адресу веб-сайта (myuniquesite.azurewebsites.net):

![Веб-страница, показывающая пустой список задач][node-mongo-finished]

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
[удаленного Git]: http://git-scm.com/docs/git-remote
[azure-sdk-for-node]: https://github.com/WindowsAzure/azure-sdk-for-node
[iisnode.yml]: https://github.com/WindowsAzure/iisnode/blob/master/src/samples/configuration/iisnode.yml
[Средство командной строки Azure для Mac и Linux]: /ru-ru/develop/nodejs/how-to-guides/command-line-tools/
[Центр разработчиков Azure]: /ru-ru/develop/nodejs/
[Создание и развертывание приложения Node.js на веб-сайтах Azure]: /ru-ru/develop/nodejs/tutorials/create-a-website-(mac)/
[Публикация на веб-сайтах Azure с использованием репозитория Git]: /ru-ru/develop/nodejs/common-tasks/publishing-with-git/
[MongoLab]: http://mongolab.com
[Веб-приложение Node.js с хранилищем в MongoDB (виртуальная машина)]: /ru-ru/develop/nodejs/tutorials/website-with-mongodb-(mac)/
[node-mongo-finished]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/todo_list_noframe.png
[node-mongo-express-results]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/express_output.png
[download-publishing-settings]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/azure-account-download-cli.png
[import-publishing-settings]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/azureimport.png
[mongolab-create]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/mongolab-create.png
[mongolab-view]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/mongolab-view.png





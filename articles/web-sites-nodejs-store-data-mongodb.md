<properties 
	pageTitle="Веб-сайт Node.js с MongoDB на ВМ - учебник по Azure" 
	description="Учебник, в котором показано, как использовать MongoDB для хранения данных и доступа к ним из приложения Node, размещенного в Azure." 
	services="web-sites, virtual-machines" 
	documentationCenter="nodejs" 
	authors="blackmist" 
	writer="" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="nodejs" 
	ms.topic="article" 
	ms.date="09/17/2014" 
	ms.author="larryfr"/>


# Создание приложения Node.js в Azure с MongoDB на виртуальной машине

В этом учебнике показано, как использовать базу данных [MongoDB], размещенную на виртуальной машине Azure, для хранения данных и доступа к ним из приложения [node], размещенного на веб-сайте Azure. [MongoDB] является популярной, высокопроизводительной базой данных NoSQL с открытым исходным кодом.

Вы узнаете:

* Как настроить виртуальную машину с операционной системой Ubuntu и базой данных MongoDB с помощью VM Depot.
* Как получить доступ к MongoDB из приложения Node.
* Как использовать межплатформенные средства Azure для создания веб-сайта Azure.

В ходе учебного курса вы создадите простое веб-приложение для управления задачами, позволяющее создавать, извлекать и выполнять задачи. Эти задачи хранятся в MongoDB.

> [AZURE.NOTE] В этом учебнике используется экземпляр MongoDB, установленный на виртуальной машине. Если же используется размещенный в другом месте экземпляр MongoDB, предоставленный MongoLabs, см. статью <a href="/ru-ru/develop/nodejs/tutorials/website-with-mongodb-mongolab/">Создание приложения Node.js в Azure с MongoDB с помощью надстройки MongoLab</a>.
 
Файлы проекта для этого учебного курса будут храниться в каталоге с именем **tasklist**, завершенное приложение будет выглядеть примерно следующим образом:

![A web page displaying an empty tasklist][node-mongo-finished]

> [AZURE.NOTE] Во многих действиях из описанных ниже упоминается использование командной строки. Для выполнения этих действий используйте командную строку своей операционной системы, например __Windows PowerShell__ (Windows) или __Bash__ (Unix). На компьютерах с OS X доступ к командной строке можно получить через приложение Terminal.

##Предварительные требования

В действиях в этом учебнике используется Node.js, поэтому в среде разработки должна быть установлена последняя версия [Node.js][node].

Кроме того, репозиторий [Git] должен быть доступен из командной строки в среде разработки, так как он используется для развертывания приложения на веб-сайте Azure.

[AZURE.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

##Создание виртуальной машины

<!--В этом учебном курсе предполагается, что вы создали виртуальную машину в Azure. После создания виртуальной машины необходимо установить на ней MongoDB.

* Создание виртуальной машины Linux и установка MongoDB описаны в разделе [Установка MongoDB на виртуальной машине Linux].

После создания виртуальной машины в Azure и установки MongoDB обязательно запомните DNS-имя виртуальной машины (например, "testlinuxvm.cloudapp.net") и внешний порт для MongoDB, указанный в конечной точке.  Эта информация потребуется позже в данном учебном курсе..-->

Хотя можно создать новую VM и затем установить на нее MongoDB, следуя [руководству по установке MongoDB][installguides], большая часть работы уже была проделана сообществом и доступна в VM Depot. Далее показано, как использовать образ из VM Depot, в котором база данных MongoDB уже установлена и настроена. 

> [AZURE.NOTE] Образ сообщества, использованный в этом учебнике, хранит данные MongoDB на диске с ОС. Хотя для учебных целей этого достаточно, хранение данных MongoDB на диске данных обеспечивает большую производительность. Процедуру создания новой виртуальной машины с диском данных и сохранением данных MongoDB на диск данных см. в разделе [Установка MongoDB под Linux в Azure][mongodbonazure].

1. Войдите на [портал управления Azure][azureportal], выберите __Virtual Machines__, __Images__, а затем - __VM Depot__.

	![screenshot of selecting VM Depot][selectdepo]

2. Выберите образ, содержащий MongoDB. В данном случае выбран Ubuntu, чтобы показать только образы, основанные на дистрибутиве Ubuntu Linux. Наконец, я выбираю образ MongoDB v2.2.3 on Hardened Ubuntu.

	![screenshot of selected mongodb v2.2.3 on hardened ubuntu image][selectedimage]

	> [AZURE.NOTE] Выберите элемент __More__ для просмотра всей информации об образе. Для некоторых образов необходимо задать дополнительные настройки после создания виртуальной машины с помощью образа.

	Щелкните стрелку в нижней части, чтобы перейти к следующему экрану.

3. Выберите регион и учетную запись хранения, которые будут использоваться для хранения VHD для этого образа. Установите флажок для продолжения.
	
	![screenshot of choose a storage account][selectstorage]

	> [AZURE.NOTE] После этого начнется копирование образа из VM Depot в указанную учетную запись хранения. Это может занять определенное время - 15 минут или более.

4. После изменения состояния образа на __Pending registration__ выберите __Register__ и введите понятное имя нового образа. Установите флажок для продолжения.

	![screenshot of registering an image][register]

5. Когда состояние образа изменится на __Available__, выберите __+ New__, __Virtual Machine__, __From Gallery__. В ответ на __Choose an Image__ выберите __My Images__, а затем выберите образ, созданный на предыдущих шагах. Щелкните стрелку для продолжения.

	![screenshot of the image][myimage]

6. Введите имя, размер и имя пользователя виртуальной машины. Щелкните стрелку для продолжения.

	![screenshot of the vm name, user name, etc.][vmname]

	>[AZURE.NOTE] В этом учебнике вам не требуется удаленно подключаться к VM с использованием протокола SSH. Выберите **Использовать пароль** и введите пароль, если вы не знакомы с использованием сертификата SSH.
	>
	> Дополнительную информацию об использовании SSH на VM с Linux на платформе Azure см. в разделе [Как использовать SSH в Linux для Azure][sshazure].

7. Выберите, следует ли использовать новую или существующую облачную службу, а также выберите регион, в котором будет создана VM. Щелкните стрелку для продолжения.

	![screenshot of the vm configuration][vmconfig]

8. Настройте дополнительные конечные точки для VM. Так как мы будем обращаться к MongoDB на этой VM, добавьте новую конечную точку, используя следующие данные:

	* Name - MongoDB
	* Protocol - TCP
	* Public port - 27017
	* private port - 27017

	Чтобы предоставить доступ к веб-порталу MongoDB, добавьте другую конечную точку, используя следующую информацию:

	* Name - MongoDBWeb
	* Protocol - TCP
	* Public port - 28017
	* Private port - 28017
	
	Наконец, поставьте галочку, чтобы настроить VM.

	![screenshot of the endpoint configuration][vmendpoint]

9. После изменения состояния виртуальной машины на __Running__ вы сможете открыть в браузере адрес __http://&lt;YourVMDNSName&gt;.cloudapp.net:28017/__, чтобы проверить работу MongoDB. В нижней части страницы должен отображаться журнал со сведениями о службе, аналогично следующим данным:

		Fri Mar  7 18:57:16 [initandlisten] MongoDB starting : pid=1019 port=27017 dbpath=/var/lib/mongodb 64-bit host=localhost.localdomain
           18:57:16 [initandlisten] db version v2.2.3, pdfile version 4.5
           18:57:16 [initandlisten] git version: f570771a5d8a3846eb7586eaffcf4c2f4a96bf08
           18:57:16 [initandlisten] build info: Linux ip-10-2-29-40 2.6.21.7-2.ec2.v1.2.fc8xen #1 SMP Fri Nov 20 17:48:28 EST 2009 x86_64 BOOST_LIB_VERSION=1_49
           18:57:16 [initandlisten] options: { config: "/etc/mongodb.conf", dbpath: "/var/lib/mongodb", logappend: "true", logpath: "/var/log/mongodb/mongodb.log" }
           18:57:16 [initandlisten] journal dir=/var/lib/mongodb/journal
           18:57:16 [initandlisten] recover : no journal files present, no recovery needed
           18:57:17 [initandlisten] waiting for connections on port 27017

	Если журнал содержит ошибки, обратитесь к [документации MongoDB][mongodocs] для их устранения.


##Установка модулей и создание шаблонов

В этом разделе вы создадите новое приложение Node в среде разработки и добавите пакеты модулей с помощью npm. Для приложения списка задач будут использованы модули [Express] и [Mongoose]. Модуль Express предоставляет платформу Model View Controller (Контроллер представления модели) для Node, а Mongoose - это драйвер для взаимодействия с MongoDB.

###Установка модуля Express и формирование шаблонов

1. В командной строке измените каталоги на каталог **tasklist**. Если каталог **tasklist** не существует, создайте его.

	> [AZURE.NOTE] Этот учебник ссылается на папку __tasklist__. Полный путь к этой папке опущен, поскольку семантика путей зависит от операционной системы. Эту папку нужно создать в легкодоступном месте вашей локальной файловой системы, таком как __~/node/tasklist__ или __c:\node\tasklist__

2. Введите следующую команду для установки команды express:

	npm install express-generator -g
 
	> [AZURE.NOTE] При использовании параметра "-g" в некоторых операционных системах может появиться сообщение об ошибке ___Error: EPERM, chmod '/usr/local/bin/express'___ и предложение попробовать использовать учетную запись с правами администратора. В этом случае необходимо с помощью команды  `sudo` запустить npm с более высоким уровнем привилегий.

    Результат этой команды должен выглядеть аналогично следующему:

		express-generator@4.0.0 C:\Users\username\AppData\Roaming\npm\node_modules\express-generator
		├── mkdirp@0.3.5
		└── commander@1.3.2 (keypress@0.1.0)                                                                         
 
	> [AZURE.NOTE] Параметр -g, используемый при установке модуля express, устанавливает его глобальным образом. Это делается для того, чтобы можно было получить доступ к команде ___express___ для скаффолдинга веб-сайта без необходимости вводить дополнительную информацию о путях.

4. Чтобы создать шаблоны, которые будут применяться для этого приложения, используйте команду **express**:

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

3. Скопируйте файл **tasklist/bin/www** в файл с именем **server.js** в папке **tasklist**. На веб-сайтах Azure предполагается, что точкой входа приложения Node.js будет либо **server.js** либо **app.js**. Поскольку **app.js** уже существует, но не является точкой входа, следует использовать **server.js**.

4. Отредактируйте файл **server.js** удалив один из знаков "." в следующей строке.

		var app = require('../app');

	Измененная строка должна выглядеть следующим образом.

		var app = require('./app');

	Это необходимо, поскольку **server.js** (ранее **bin/www**) теперь находится в той же папке, что и требуемый файл **app.js**.

###Установка дополнительных модулей

Файл **package.json** является одним из файлов, созданных командой **express**. Этот файл содержит список дополнительных модулей, необходимых для приложения Express. Позднее, при развертывании этого приложения на веб-сайте Azure, этот файл будет использоваться, чтобы определить, какие модули должны быть установлены в Azure для поддержки приложения.
	
1. В папке **tasklist** выполните следующую команду для установки модулей, описанных в файле **package.json**:

        npm install

    Результат этой команды должен выглядеть аналогично следующему:

		debug@0.7.4 node_modules\debug
		
		cookie-parser@1.0.1 node_modules\cookie-parser
		├── cookie-signature@1.0.3
		└── cookie@0.1.0
		
		morgan@1.0.0 node_modules\morgan
		└── bytes@0.2.1
		
		body-parser@1.0.2 node_modules\body-parser
		├── qs@0.6.6
		├── raw-body@1.1.4 (bytes@0.3.0)
		└── type-is@1.1.0 (mime@1.2.11)
		
		express@4.0.0 node_modules\express
		├── methods@0.1.0
		├── parseurl@1.0.1
		├── merge-descriptors@0.0.2
		├── utils-merge@1.0.0
		├── escape-html@1.0.1
		├── cookie-signature@1.0.3
		├── fresh@0.2.2
		├── range-parser@1.0.0
		├── buffer-crc32@0.2.1
		├── qs@0.6.6
		├── cookie@0.1.0
		├── path-to-regexp@0.1.2
		├── send@0.2.0 (mime@1.2.11)
		├── type-is@1.0.0 (mime@1.2.11)
		├── accepts@1.0.0 (negotiator@0.3.0, mime@1.2.11)
		└── serve-static@1.0.1 (send@0.1.4)
		
		jade@1.3.1 node_modules\jade
		├── character-parser@1.2.0
		├── commander@2.1.0
		├── mkdirp@0.3.5
		├── monocle@1.1.51 (readdirp@0.2.5)
		├── constantinople@2.0.0 (uglify-js@2.4.13)
		├── with@3.0.0 (uglify-js@2.4.13)
		└── transformers@2.1.0 (promise@2.0.0, css@1.0.8, uglify-js@2.2.5)                                                                

	Будут установлены все стандартные модули, используемые приложением Express.

2. Затем введите следующую команду, чтобы локально установить модуль Mongoose, а также сохранить соответствующую запись в файл **package.json**:

		npm install mongoose --save

	Результат этой команды должен выглядеть аналогично следующему:

		mongoose@3.8.8 node_modules\mongoose
		├── regexp-clone@0.0.1
		├── sliced@0.0.5
		├── muri@0.3.1
		├── hooks@0.2.1
		├── mpath@0.1.1
		├── mpromise@0.4.3
		├── ms@0.1.0
		├── mquery@0.5.3
		└── mongodb@1.3.23 (kerberos@0.0.3, bson@0.2.5)         

    > [AZURE.NOTE] На любое сообщение об установке анализатора bson C++ можно не обращать внимания.

##Использование MongoDB в приложении node

В этом разделе базовое приложение, созданное командой **express**, будет расширено путем добавления файла **task.js**, содержащего модель для ваших задач. Также будет изменен существующий файл **app.js** и создан новый файл контроллера **tasklist.js** для использования этой модели.

### Создание модели

1. В каталоге **tasklist** создайте новый каталог с именем **models**.

2. В каталоге **models** создайте новый файл с именем **task.js**. Этот файл будет содержать модель для задач, создаваемых приложением.

3. В начало файла **task.js** добавьте следующий код для ссылки на необходимые библиотеки:

        var mongoose = require('mongoose'),
	        Schema = mongoose.Schema;

4. Далее будет добавлен код для определения и экспорта модели. Эта модель будет использоваться для взаимодействия с базой данных MongoDB.

        var TaskSchema = new Schema({
	        itemName      : String,
	        itemCategory  : String,
	        itemCompleted : { type: Boolean, default: false },
	        itemDate      : { type: Date, default: Date.now }
        });

        module.exports = mongoose.model('TaskModel', TaskSchema);

5. Сохраните и закройте файл **task.js**.

###Создание контроллера

1. В каталоге **tasklist/routes** создайте новый файл с именем **tasklist.js** и откройте его в текстовом редакторе.

2. Добавьте в **tasklist.js** следующий код. Он загружает модуль mongoose и модель задач, определенную в **task.js**. Функция TaskList используется для создания подключения к серверу MongoDB на основе значения **connection**:

		var mongoose = require('mongoose'),
	        task = require('../models/task.js');

		module.exports = TaskList;

		function TaskList(connection) {
  		  mongoose.connect(connection);
		}

2. Продолжите добавление в файл **tasklist.js**, добавляя методы, используемые для **showTasks**, **addTask** и **completeTasks**:

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

### Изменение app.js

1. В каталоге **tasklist** откройте файл **app.js** в текстовом редакторе. Этот файл был создан ранее с помощью команды **express**.

2. Добавьте следующий код в начало файла **app.js**. Он инициализирует **TaskList** со строкой подключения для сервера MongoDB:

        var TaskList = require('./routes/tasklist');
		var taskList = new TaskList(process.env.MONGODB_URI);

	Обратите внимание на вторую строку. Выполняется доступ к переменной среды, которая содержит сведения о подключении для экземпляра mongo и будет настроена позднее. При наличии локального экземпляра Mongo, работающего в целях разработки, может понадобиться временно задать для этого параметра значение localhost вместо process.env.MONGODB_URI.

3. Добавьте следующие строки:

		app.use('/', routes);
		app.use('/users', users);

	Замените две строки выше следующим кодом:

    	app.get('/', taskList.showTasks.bind(taskList));
    	app.post('/addtask', taskList.addTask.bind(taskList));
    	app.post('/completetask', taskList.completeTask.bind(taskList));

	Это добавляет функции, определенные в **tasklist.js**, как маршруты.

4. Сохраните файл **app.js**.

###Изменение представления индекса

1. Измените каталоги на каталог **views** и откройте файл **index.jade** в текстовом редакторе.

2. Замените содержимое файла **index.jade** на код, приведенный ниже. Он определяет представление для отображения существующих задач, а также форму для добавления новых задач и пометки существующих задач как завершенных.

		h1= title
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

<!-- ##Локальный запуск приложения

Для проверки приложения на локальном компьютере выполните следующие действия:

1. В командной строке измените каталоги на каталог **tasklist**.

2. Задайте значение переменной среды MONGODB_URI в вашей среде разработки, которое будет указывать на виртуальную машину с базой данных MongoDB. В приведенных ниже примерах замените __mymongodb__ на имя вашей виртуальной машины.

	В системе Windows используйте следующую команду для задания переменной среды.

		set MONGODB_URI=mongodb://mymongodb.cloudapp.net/tasks

	В системе OS X или системах на основе Linux используйте следующую команду для задания переменной среды.

		set MONGODB_URI=mongodb://mymongodb.cloudapp.net/tasks
		export MONGODB_URI

	Это даст приложению команду подключаться к MongoDB на созданной ранее виртуальной машине __mymongodb.cloudapp.net__ и использовать БД с именем 'tasks'.

2. Используйте следующую команду для локального запуска приложения:

        node app.js

3. Откройте браузер и перейдите по адресу http://localhost:3000. Должна появиться веб-страница, похожая на следующую:

    ![A webpage displaying an empty tasklist][node-mongo-finished]

4. Используйте предоставленные поля **Имя элемента** и **Имя категории** для ввода данных, а затем нажмите кнопку **Добавить элемент**.

    ![An image of the add item field with populated values.][node-mongo-add-item]

5. Страница должна обновиться, чтобы показать элемент в таблице списка задач.

    ![An image of the new item in the list of tasks][node-mongo-list-items]

6. Чтобы завершить задачу, просто установите флажок в столбце "Завершено" и нажмите кнопку **Обновить задачи**. Хотя после нажатия кнопки **Обновить задачи** никакие визуальные изменения не наблюдаются, ввод документа в MongoDB был помечен как завершенный.

7. Чтобы остановить процесс Node, перейдите в командную строку и нажмите клавиши **CTRL**+**C**. -->

##Развертывание приложения в Azure

В действиях, описанных в этом разделе, для создания нового веб-сайта Azure используются средства командной строки Azure, а затем для развертывания приложения используется репозиторий Git. Для выполнения этих действий необходимо наличие подписки Azure.

> [AZURE.NOTE] Эти действия также можно выполнить с помощью портала Azure. Указания по использованию портала Azure для развертывания приложения Node.js см. в статье <a href="/ru-ru/develop/nodejs/tutorials/create-a-website-(mac)/">Создание и развертывание приложения Node.js на веб-сайте Azure</a>.

> [AZURE.NOTE] Если это первый созданный вами веб-сайт Azure, для развертывания приложения необходимо использовать портал Azure.

###Установка межплатформенного интерфейса командной строки

Межплатформенный интерфейс командной строки Azure (xplat-cli) позволяет выполнять операции управления службами Azure. Если вы еще не установили и не настроили интерфейс xplat-cli в среде разработки, изучите раздел [Установка и настройка межплатформенного интерфейса командной строки Azure][xplatcli].

###Создание веб-сайта Azure

1. В командной строке измените каталоги на каталог **tasklist**.

2. Используйте следующую команду для создания нового веб-сайта Azure. Замените значение 'myuniquesitename' на уникальное имя вашего сайта. Это значение используется как часть URL-адреса для созданного веб-сайта.

		azure site create myuniquesitename --git
		
	Будет предложено указать центр обработки данных, в котором будет создан веб-сайт. Выберите центр обработки данных, расположенный недалеко от вашего местоположения.
	
	Параметр `--git` создаст репозиторий Git локально в папке **tasklist**, если он отсутствует. Будет также создан [удаленный репозиторий Git] с именем  'azure', который будет использоваться для публикации приложения в Azure. Будет создан файл [iisnode.yml], который содержит настройки, используемые Azure для размещения приложений Node. Наконец, также будет создан файл .gitignore, чтобы исключить папку Node-modules из публикации в .git.
	
	> [AZURE.NOTE] Если эта команда выполняется из каталога, который уже содержит репозиторий Git, каталог не будет инициализирован заново.
	
	> [AZURE.NOTE] Если параметр --git отсутствует, но каталог содержит репозиторий Git, то удаленный репозиторий 'azure' все равно будет создан.
	
	После выполнения этой команды должен появиться результат, похожий на следующий. Обратите внимание, что строка, начинающаяся с **Created website at** (Созданный веб-сайт по адресу), содержит URL-адрес веб-сайта.

		info:   Executing command site create
		info:   Using location southcentraluswebspace
		info:   Executing `git init`
		info:   Creating default web.config file
		info:   Creating a new web site
		info:   Created web site at  mongodbtasklist.azurewebsites.net
		info:   Initializing repository
		info:   Repository initialized
		info:   Executing `git remote add azure http://username@mongodbtasklist.azurewebsites.net/mongodbtasklist.git`
		info:   site create command OK

	> [AZURE.NOTE]> Если это первый веб-сайт Azure для вашей подписки, необходимо использовать портал для создания веб-сайта. Дополнительную информацию см. в статье <a href="/ru-ru/develop/nodejs/tutorials/create-a-website-(mac)/">Создание и развертывание приложения Node.js на веб-сайте Azure</a>.

###Задание переменной среды MONGODB_URI

Приложение ожидает, что строка подключения для экземпляра MongoDB будет доступна в переменной среды MONGODB_URI. Чтобы задать для веб-сайта это значение, используйте следующую команду:

	azure site config add MONGODB_URI=mongodb://mymongodb.cloudapp.net/tasks

При этом будет создан новый параметр приложения для веб-сайта, который будет использоваться для заполнения переменной среды MONGODB_URI, считываемой веб-сайтом. Замените значение 'mymongodb.cloudapp.net' именем виртуальной машины, на которой была установлена MongoDB.

###Публикация приложения

1. В окне Terminal измените каталоги на каталог **tasklist**, если он еще не является текущим.

2. Используйте следующие команды для добавления и последующего сохранения файлов в локальном репозитории Git:

		git add .
		git commit -m "adding files"

3. При принудительной отправке последних изменений репозитория Git на веб-сайт Azure необходимо указать, что целевой ветвью, используемой для контента этого веб-сайта, является **master**.

		git push azure master
	
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
 
4. После завершения операции принудительной отправки перейдите на веб-сайт с помощью команды `azure site browse` для просмотра приложения.

##Дальнейшие действия

Хотя в действиях этой статьи описывается использование MongoDB для хранения информации, можно также использовать табличную службу Azure. Дополнительные сведения см. в статье [Веб-приложение Node.js со службой таблиц Azure].

Чтобы понять, как использовать размещенный в другом месте экземпляр MongoDB, предоставленный MongoLabs, см. статью [Создание приложения Node.js в Azure с MongoDB с помощью надстройки MongoLab](/ru-ru/develop/nodejs/tutorials/website-with-mongodb-mongolab/).

Информацию о защите MongoDB см. в разделе [Безопасность MongoDB][mongosecurity].

##Дополнительные ресурсы

[Средство командной строки Azure для Mac и Linux]
[Создание и развертывание приложения Node.js на веб-сайтах Azure]
[Публикация на веб-сайтах Azure с использованием репозитория Git]

[mongosecurity]: http://docs.mongodb.org/manual/security/
[node]: http://nodejs.org
[MongoDB]: http://www.mongodb.org
[Git]: http://git-scm.com
[Express]: http://expressjs.com
[Mongoose]: http://mongoosejs.com
[for free]: /ru-ru/pricing/free-trial
[Git remote]: http://git-scm.com/docs/git-remote
[azure-sdk-for-node]: https://github.com/WindowsAzure/azure-sdk-for-node
[iisnode.yml]: https://github.com/WindowsAzure/iisnode/blob/master/src/samples/configuration/iisnode.yml
[Программа командной строки Azure для Mac и Linux]: /ru-ru/develop/nodejs/how-to-guides/command-line-tools/
[Центр разработчиков Azure]: /ru-ru/develop/nodejs/
[Создание и развертывание приложения Node.js на веб-сайтах Azure]: /ru-ru/develop/nodejs/tutorials/create-a-website-(mac)/
[Публикация на веб-сайтах Azure с использованием репозитория Git]: /ru-ru/develop/nodejs/common-tasks/publishing-with-git/
[Установка MongoDB на виртуальной машине Linux]: /ru-ru/manage/linux/common-tasks/mongodb-on-a-linux-vm/
[Веб-приложение node.js, использующее службу таблиц Azure]: /ru-ru/develop/nodejs/tutorials/web-site-with-storage/
[node-mongo-finished]: ./media/store-mongodb-web-sites-nodejs-use-mac/todo_list_empty.png
[node-mongo-express-results]: ./media/store-mongodb-web-sites-nodejs-use-mac/express_output.png
[node-mongo-add-item]: ./media/store-mongodb-web-sites-nodejs-use-mac/todo_add_item.png
[node-mongo-list-items]: ./media/store-mongodb-web-sites-nodejs-use-mac/todo_list_items.png
[download-publishing-settings]: ./media/store-mongodb-web-sites-nodejs-use-mac/azure-account-download-cli.png
[installguides]: http://docs.mongodb.org/manual/installation/
[azureportal]: https://manage.windowsazure.com/
[mongodocs]: http://docs.mongodb.org/manual/
[xplatcli]: /ru-ru/documentation/articles/xplat-cli/

[selectdepo]: ./media/web-sites-nodejs-store-data-mongodb/browsedepot.png
[selectedimage]: ./media/web-sites-nodejs-store-data-mongodb/selectimage.png
[selectstorage]: ./media/web-sites-nodejs-store-data-mongodb/storageaccount.png
[register]: ./media/web-sites-nodejs-store-data-mongodb/register.png
[myimage]: ./media/web-sites-nodejs-store-data-mongodb/myimages.png
[vmname]: ./media/web-sites-nodejs-store-data-mongodb/vmname.png
[vmconfig]: ./media/web-sites-nodejs-store-data-mongodb/vmconfig.png
[vmendpoint]: ./media/web-sites-nodejs-store-data-mongodb/endpoints.png
[sshazure]: http://azure.microsoft.com/documentation/articles/linux-use-ssh-key/
[mongodbonazure]: http://docs.mongodb.org/ecosystem/tutorial/install-mongodb-on-linux-in-azure/ 

<!--HONumber=42-->

<properties linkid="develop-nodejs-tutorials-web-site-with-mongodb-mongolab" urlDisplayName="Website with MongoDB" pageTitle="Node.js website with MongoDB on MongoLab - Azure" metaKeywords="" description="Learn how to create a Node.js Azure Website that connects to a MongoDB instance hosted on MongoLab." metaCanonical="" services="web-sites,virtual-machines" documentationCenter="nodejs" title="Create a Node.js Application on Azure with MongoDB using the MongoLab Add-On" authors="eric@mongolab.com" solutions="" manager="" editor="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="nodejs" ms.topic="article" ms.date="09/17/2014" ms.author="eric@mongolab.com"></tags>

# Создание приложения Node.js в Azure с MongoDB с помощью надстройки MongoLab

*Автор: Эрик Седор (Eric Sedor), MongoLab*

Приветствую, искатели приключений! Добро пожаловать в MongoDB как услуга. Изучив данный учебник, вы научитесь:

1.  [Подготавливать базу данных][Подготавливать базу данных] — надстройка [MongoLab][MongoLab], доступная в Магазине Azure, позволяет получать доступ к базе данных MongoDB, размещенной в облаке Azure, и управляется облачной платформой базы данных MongoLab.
2.  [Создание приложения][Создание приложения] – это простое приложение Node.js для обслуживания списка задач.
3.  [Развертывать приложение][Развертывать приложение] — объединяя вместе несколько обработчиков конфигурации, мы значительно упрощаем перемещение кода.
4.  [Управлять базой данных][Управлять базой данных] — наконец, мы покажем портал управления базами данных MongoLab, где можно без труда искать, визуализировать и изменять данные.

Если во время работы с этим учебником у вас возникнут какие-либо вопросы, обращайтесь с ними по адресу [support@mongolab.com](mailto:support@mongolab.com).

Прежде чем продолжить, убедитесь, что установлены следующие компоненты:

-   [Node.js][Node.js] версии не ниже 0.8.14.

-   [Git][Git]

[WACOM.INCLUDE [создать-учетная запись-и-веб-сайт-примечание][создать-учетная запись-и-веб-сайт-примечание]]

## Быстрый запуск

Если у вас есть опыт работы с Магазином Azure, воспользуйтесь этим разделом, чтобы быстро начать работу. В противном случае изучите приведенный ниже раздел [Подготовка базы данных][Подготавливать базу данных].

1.  Перейдите на веб-сайт Магазина Azure.<br/>
    ![Магазин][Магазин]
2.  Щелкните надстройку MongoLab.<br/>
    ![MongoLab][1]
3.  Щелкните надстройку MongoLab в списке надстроек и выберите пункт **Сведения о подключении**.<br/>
    ![ConnectionInfoButton][ConnectionInfoButton]
4.  Скопируйте значение MONGOLAB\_URI в буфер обмена.<br/>
    ![ConnectionInfoScreen][ConnectionInfoScreen]<br/>
    **Этот универсальный код ресурса (URI) содержит имя пользователя и пароль к вашей базе данных. Считайте это конфиденциальной информацией и не передавайте ее.**
5.  Добавьте значение в список «Строки подключения», который можно найти в меню «Конфигурация» веб-приложения Azure:<br/>
    ![WebSiteConnectionStrings][WebSiteConnectionStrings]
6.  В качестве **Имени** введите MONGOLAB\_URI.
7.  В качестве **Значение** вставьте строку подключения, полученную в предыдущем разделе.
8.  В раскрывающемся списке «Тип» выберите пункт **Настраиваемый** (вместо значения по умолчанию **SQLAzure**).
9.  Запустите `npm install mongoose`, чтобы получить Mongoose, драйвер узла MongoDB.
10. Настройте обработчик в коде, чтобы получить универсальный код ресурса (URI) подключения MongoLab из переменной среды и выполнить подключение:

        var mongoose = require('mongoose');  
        ...
        var connectionString = process.env.CUSTOMCONNSTR_MONGOLAB_URI
        ...
        mongoose.connect(connectionString);

Примечание. Azure добавляет префикс **CUSTOMCONNSTR\_** к первоначально объявленной строке подключения, поэтому в коде содержится ссылка на **CUSTOMCONNSTR\_MONGOLAB\_URI.**, а не на **MONGOLAB\_URI**.

Теперь перейдем к полному учебнику...

## <a name="provision"></a>Подготовка базы данных

[WACOM.INCLUDE [howto-provision-mongolab][howto-provision-mongolab]]

## <a name="create"></a>Создание приложения

В этом разделе вы настроите среду разработки и создадите код для веб-приложения простого списка задач с помощью Node.js, Express и MongoDB. Модуль [Express][Express] предоставляет платформу View Controller для Node, а драйвер[Mongoose][Mongoose] предназначен для взаимодействия с MongoDB.

### Настройка

#### Формирование шаблонов и установка модулей

1.  В командной строке создайте каталог **tasklist** и перейдите в него. Они и будет являться каталогом вашего проекта.
2.  Введите следующую команду для установки Express.

        npm install express -g

    Код `-g` указывает на глобальный режим, который используется для предоставления доступа к модулю **express** без указания пути к каталогу. При появлении сообщения **Error: EPERM, chmod '/usr/local/bin/express'** используйте режим **sudo**, чтобы запустить диспетчер npm на более высоком, привилегированном уровне.

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

3.  Для создания шаблонов, которые будут использоваться для данного приложения, используйте команду **express**:

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

4.  Введите следующую команду для установки модулей, описанных в файле **package.json**:

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

5.  Затем введите следующую команду, чтобы локально установить модуль Mongoose, а также сохранить соответствующую запись в файл **package.json**:

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

После подготовки среды и формирования шаблонов мы расширим базовое приложение, созданное с помощью команды **express**, добавив файл **task.js**, который содержит модель для ваших задач. Также будет изменен существующий файл **app.js** и создан новый файл контроллера **tasklist.js** для использования этой модели.

#### Создание модели

1.  В каталоге **tasklist** создайте новый каталог с именем **models**.

2.  В каталоге **models** создайте новый файл с именем **task.js**. Этот файл будет содержать модель для задач, создаваемых приложением.

3.  Добавьте в файл **task.js** следующий код:

        var mongoose = require('mongoose')
          , Schema = mongoose.Schema;
        var TaskSchema = new Schema({
            itemName      : String
          , itemCategory  : String
          , itemCompleted : { type: Boolean, default: false }
          , itemDate      : { type: Date, default: Date.now }
        });

        module.exports = mongoose.model('TaskModel', TaskSchema);

4.  Сохраните и закройте файл **task.js**.

#### Создание контроллера

1.  В каталоге **tasklist/routes** создайте новый файл с именем **tasklist.js** и откройте его в текстовом редакторе.

2.  Добавьте в **tasklist.js** следующий код. Он загружает модуль mongoose и модель задач, определенную в **task.js**. Функция TaskList используется для создания подключения к серверу MongoDB на основе значения **connection** и предоставляет методы **showTasks**, **addTask** и **completeTasks**:

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

3.  Сохраните файл **tasklist.js**.

#### Изменение представления индекса

1.  Измените каталоги на каталог **views** и откройте файл **index.jade** в текстовом редакторе.

2.  Заменит содержимое файла **index.jade** на код, приведенный ниже. Он определяет представление для отображения существующих задач, а также форму для добавления новых задач и пометки существующих задач как завершенных.

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

3.  Сохраните и закройте файл **index.jade**.

#### Замена файла app.js

1.  В каталоге **tasklist** откройте файл **app.js** в текстовом редакторе. Этот файл был создан ранее с помощью команды **express**.
2.  Добавьте в начало файла **app.js** следующий код. Он инициализирует функцию **TaskList** со строкой подключения для сервера MongoDB:

        var TaskList = require('./routes/tasklist');
        var taskList = new TaskList(process.env.CUSTOMCONNSTR_MONGOLAB_URI);

    Обратите внимание на вторую строку. Выполняется доступ к переменной среды, которая содержит сведения о подключении для экземпляра mongo и будет настроена позднее. При наличии локального экземпляра Mongo, работающего в целях разработки, может понадобиться временно задать для этого параметра значение localhost вместо `process.env.CUSTOMCONNSTR_MONGOLAB_URI`.

3.  Найдите строки, начинающиеся с `app.get`, и замените их следующими строками:

        app.get('/', taskList.showTasks.bind(taskList));
        app.post('/addtask', taskList.addTask.bind(taskList));
        app.post('/completetask', taskList.completeTask.bind(taskList));

    Это добавляет функции, определенные в **tasklist.js**, как маршруты.

4.  Сохраните файл **app.js**.

## <a name="deploy"></a>Развертывание приложения

Завершив разработку приложения, следует создать веб-сайт Azure для его размещения, настроить этот веб-сайт и развернуть код. Центральной частью этого раздела является использование строки подключения MongoDB (URI). Вы настроите на своем веб-сайте переменную среды с этим универсальным кодом ресурса (URI), чтобы не включать его в используемый код. URI следует рассматривать как конфиденциальную информацию, так как он содержит учетные данные для подключения к базе данных.

В действиях, описанных в этом разделе, для создания нового веб-сайта Azure используются средства командной строки Azure, а затем для развертывания приложения используется репозиторий Git. Для выполнения этих действий необходимо наличие подписки Azure.

### Установка средств командной строки Azure для Mac и Linux

Для установки средств командной строки используйте следующую команду:

    npm install azure-cli -g

Если вы уже установили пакет **SDK для Azure для Node.js** из [Центра разработчиков Azure][Центра разработчиков Azure], то программы командной строки должны быть уже установлены. Дополнительную информацию см. в статье [Программы командной строки Azure для Mac и Linux][Программы командной строки Azure для Mac и Linux].

Хотя программы командной строки Azure были созданы в основном для пользователей Mac и Linux, они основаны на Node.js и должны работать в любой системе, поддерживающей Node.

### Импорт параметров публикации

Перед использованием средств командной строки с Azure необходимо сначала загрузить файл, содержащий сведения о подписке. Выполните следующие действия, чтобы загрузить и импортировать этот файл.

1.  В командной строке введите следующую команду, чтобы запустить браузер, и перейдите на страницу загрузки. В ответ на приглашение выполните вход с использованием учетной записи, связанной с вашей подпиской.

        azure account download

    ![Страница загрузки][Страница загрузки]

    Загрузка файла должна начаться автоматически. Если этого не произошло, можно щелкнуть ссылку в начале страницы, чтобы загрузить файл вручную.

2.  После завершения загрузки файла используйте для импорта параметров следующую команду:

        azure account import <path-to-file>

    Укажите путь и имя файла параметров публикации, загруженного на предыдущем шаге. Результат выполнения команды должен быть похож на следующее:

        info:   Executing command account import
        info:   Found subscription: subscriptionname
        info:   Setting default subscription to: subscriptionname
        warn:   The '/Users/user1/.azure/publishSettings.xml' file contains sensitive information.
        warn:   Remember to delete it now that it has been imported.
        info:   Account publish settings imported successfully
        info:   account import command OK

3.  По завершении импорта следует удалить файл параметров публикации, так как он больше не нужен и содержит важные сведения, касающиеся подписки Azure.

### Создание нового веб-сайта и передача кода

Создать веб-сайт в Azure очень просто. Если это ваш первый веб-сайт Azure, необходимо использовать портал. Если уже имеется по крайней мере один, перейдите к шагу 7.

1.  На портале Azure нажмите кнопку **Создать**.<br/>
    ![Создать][Создать]
2.  Выберите **Среда выполнения приложений -\> Веб-сайт -\> Быстро создать**.
    ![CreateSite][CreateSite]
3.  Введите префикс URL-адреса. Выберите нужное имя, но помните, что оно должно быть уникальным (скорее всего, имя mymongoapp не будет доступно).
4.  Щелкните **Создать веб-сайт**.
5.  После завершения создания веб-сайта щелкните его имя в списке веб-сайтов. Появится панель мониторинга веб-сайта.<br/>
    ![WebSiteDashboard][WebSiteDashboard]
6.  Щелкните **Настроить публикацию Git** в разделе **сводка** и введите имя пользователя и пароль пользователя git. Этот пароль будет использоваться при передаче данных на веб-сайт (см. шаг 9).
7.  Если веб-сайт был создан в соответствии с указанными выше действиями, то следующая команда приведет к завершению процесса. Но если у вас уже есть несколько веб-сайтов Azure, вы можете пропустить описанные выше шаги и создать новый веб-сайт, используя ту же команду. В каталоге проекта **tasklist** выполните следующие действия:

        azure site create myuniquesitename --git  

    Замените имя myuniquesitename уникальным именем, которое следует использовать в качестве имени веб-сайта. Если веб-сайт создается в рамках этой команды, появится запрос на указание центра обработки данных, в котором будет размещен этот сайт. Выберите центр обработки данных, который географически расположен неподалеку от вашей базы данных MongoLab.

    Параметр `--git` создаст:
    A. Локальный репозиторий Git в папке **tasklist**, если такого репозитория еще нет.
    Б. [Удаленный репозиторий Git][Удаленный репозиторий Git] с именем azure, который будет использоваться для публикации приложения в Azure.
    В. Файл [iisnode.yml][iisnode.yml] с параметрами, используемыми Azure для размещения приложений Node.
    Г. GITIGNORE-файл, используемый для предотвращения публикации в .git папки node-modules.

    После выполнения этой команды должен появиться результат, похожий на следующий. Обратите внимание, что строка, которая начинается с **Created website at**, содержит URL-адрес веб-сайта.

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

8.  Используйте следующие команды для добавления и последующего сохранения файлов в локальном репозитории Git:

        git add .
        git commit -m "adding files"

9.  Передача кода:

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

Помните о process.env.CUSTOMCONNSTR\_MONGOLAB\_URI в коде? Нам нужно указать в этой переменной среды значение, полученное в Azure во время подготовки базы данных .

#### Получение строки подключения к MongoLab

[WACOM.INCLUDE [howto-get-connectioninfo-mongolab][howto-get-connectioninfo-mongolab]]

#### Добавление строки подключения в число переменных среды веб-сайта

[WACOM.INCLUDE [howto-save-connectioninfo-mongolab][howto-save-connectioninfo-mongolab]]

## Готово!

Выполните `azure site browse` в каталоге проекта, чтобы браузер открылся автоматически, или вручную откройте браузер и перейдите по URL-адресу веб-сайта (myuniquesite.azurewebsites.net):

![Веб-страница, показывающая пустой список задач][Веб-страница, показывающая пустой список задач]

## <a name="manage"></a>Управление базой данных

[WACOM.INCLUDE [howto-access-mongolab-ui][howto-access-mongolab-ui]]

Поздравляем! Вы только что запустили приложение Node.js на основе размещенной в MongoLab базы данных MongoDB! Теперь, когда база данных MongoLab создана, вы можете обращаться по адресу [support@mongolab.com](mailto:support@mongolab.com) по любым вопросам, связанным с вашей базой данных, а также для получения справки по MongoDB или непосредственно драйверу узла. Удачи!

  [Подготавливать базу данных]: #provision
  [MongoLab]: http://mongolab.com
  [Создание приложения]: #create
  [Развертывать приложение]: #deploy
  [Управлять базой данных]: #manage
  [\<a href="mailto:support@mongolab.com"\>support@mongolab.com\</a\>]: mailto:support@mongolab.com
  [Node.js]: http://nodejs.org
  [Git]: http://git-scm.com
  [создать-учетная запись-и-веб-сайт-примечание]: ../includes/create-account-and-websites-note.md
  [Магазин]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/button-store.png
  [1]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/entry-mongolab.png
  [ConnectionInfoButton]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/button-connectioninfo.png
  [ConnectionInfoScreen]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/dialog-mongolab_connectioninfo.png
  [WebSiteConnectionStrings]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/focus-mongolab-websiteconnectionstring.png
  [howto-provision-mongolab]: ../includes/howto-provision-mongolab.md
  [Express]: http://expressjs.com
  [Mongoose]: http://mongoosejs.com
  [Центра разработчиков Azure]: /ru-ru/develop/nodejs/
  [Программы командной строки Azure для Mac и Linux]: /ru-ru/develop/nodejs/how-to-guides/command-line-tools/
  [Страница загрузки]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/azure-account-download-cli.png
  [Создать]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/button-new.png
  [CreateSite]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/screen-mongolab-newwebsite.png
  [WebSiteDashboard]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/screen-mongolab-websitedashboard.png
  [Удаленный репозиторий Git]: http://git-scm.com/docs/git-remote
  [iisnode.yml]: https://github.com/WindowsAzure/iisnode/blob/master/src/samples/configuration/iisnode.yml
  [howto-get-connectioninfo-mongolab]: ../includes/howto-get-connectioninfo-mongolab.md
  [howto-save-connectioninfo-mongolab]: ../includes/howto-save-connectioninfo-mongolab.md
  [Веб-страница, показывающая пустой список задач]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/todo_list_noframe.png
  [howto-access-mongolab-ui]: ../includes/howto-access-mongolab-ui.md

<properties linkid="develop-node-website-with-mongodb-mac" urlDisplayName="Website with MongoDB" pageTitle="Node.js website with MongoDB on a VM - Azure tutorial" metaKeywords="Azure tutorial MongoDB, MongoDB store data, access data MongoDB Node, Azure Node app" description="A tutorial that teaches you how to use MongoDB to store and access data from a Node application hosted on Azure." metaCanonical="http://www.windowsazure.com/ru-ru/develop/nodejs/tutorials/website-with-mongodb-mongolab/" services="web-sites,virtual-machines" documentationCenter="nodejs" title="Node.js Web Application with Storage on MongoDB (Virtual Machine)" authors="larryfr"  solutions="" writer="" manager="" editor=""  />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="nodejs" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr" />


# Создание приложения Node.js в Azure с MongoDB на виртуальной машине

В этом учебном курсе показано, как использовать базу данных [MongoDB][MongoDB], размещенную на виртуальной машине Azure, для хранения данных и доступа к ним из приложения [node][node], размещенного на веб-сайте Azure. [MongoDB][MongoDB] является популярной, высокопроизводительной базой данных NoSQL с открытым исходным кодом.

Вы узнаете:

-   Как настроить виртуальную машину с операционной системой Ubuntu и базой данных MongoDB с помощью VM Depot.
-   Как получить доступ к MongoDB из приложения Node.
-   Как использовать межплатформенные средства Azure для создания веб-сайта Azure.

В ходе учебного курса вы создадите простое веб-приложение для управления задачами, позволяющее создавать, извлекать и выполнять задачи. Эти задачи хранятся в MongoDB.

> [WACOM.NOTE] В этом учебном курсе используется экземпляр MongoDB, установленный на виртуальной машине. Если же используется размещенный в другом месте экземпляр MongoDB, предоставленный MongoLabs, см. статью [Создание приложения Node.js в Azure с MongoDB с помощью надстройки MongoLab][Создание приложения Node.js в Azure с MongoDB с помощью надстройки MongoLab].

Файлы проекта для этого учебного курса будут храниться в каталоге с именем **tasklist**, завершенное приложение будет выглядеть примерно следующим образом:

![Веб-страница, показывающая пустой список задач][Веб-страница, показывающая пустой список задач]

> [WACOM.NOTE] Многие из описанных ниже действий могут быть выполнены из командной строки. Для выполнения этих действий используйте командную строку своей операционной системы, например **Windows PowerShell** (Windows) или **Bash** (Unix Shell). На компьютерах с OS X доступ к командной строке можно получить через приложение Terminal.

## Предварительные требования

В действиях в этом учебнике используется Node.js, поэтому в среде разработки должна быть установлена последняя версия [Node.js][node].

Кроме того, репозиторий [Git][Git] должен быть доступен из командной строки в среде разработки, так как он используется для развертывания приложения на веб-сайте Azure.

[WACOM.INCLUDE [создать-учетная запись-и-веб-сайт-примечание](../includes/create-account-and-websites-note.md)]

## Создание виртуальной машины

<!--This tutorial assumes you have created a virtual machine in Azure. After creating the virtual machine you need to install MongoDB on the virtual machine:  * To create a Linux virtual machine and install MongoDB, see [Installing MongoDB on a Linux Virtual machine].  After you have created the virtual machine in Azure and installed MongoDB, be sure to remember the DNS name of the virtual machine ("testlinuxvm.cloudapp.net", for example) and the external port for MongoDB that you specified in the endpoint.  You will need this information later in the tutorial.-->

Хотя можно создать новую ВМ и затем установить на нее MongoDB, следуя [руководству по установке MongoDB][руководству по установке MongoDB], большая часть работы уже была проделана сообществом и доступна в VM Depot. Далее показано, как использовать образ из VM Depot, в котором база данных MongoDB уже установлена и настроена.

> [WACOM.NOTE] Образ сообщества, использованный в этом учебнике, хранит данные MongoDB на диске с ОС. Хотя для учебных целей этого достаточно, хранение данных MongoDB на диске данных обеспечивает большую производительность. Процедуру создания новой виртуальной машины с диском данных и сохранения данных MongoDB на диск данных см. в разделе [Установка MongoDB под Linux в Azure][Установка MongoDB под Linux в Azure].

1.  Войдите на [портал управления Azure][портал управления Azure], выберите **Виртуальные машины**, выберите **Образы** и **VM Depot**.

    ![снимок экрана выбора VM Depot][снимок экрана выбора VM Depot]

2.  Выберите образ, содержащий MongoDB. В данном случае выбран Ubuntu, чтобы показать только образы, основанные на дистрибутиве Ubuntu Linux. Наконец, я выбираю образ MongoDB v2.2.3 on Hardened Ubuntu.

    ![снимок экрана выбора образа MongoDB v2.2.3 on Hardened Ubuntu][снимок экрана выбора образа MongoDB v2.2.3 on Hardened Ubuntu]

    > [WACOM.NOTE] Выберите элемент **Дополнительно** для просмотра всех сведений об образе. Для некоторых образов необходимо задать дополнительные настройки после создания виртуальной машины с помощью образа.

    Щелкните стрелку в нижней части, чтобы перейти к следующему экрану.

3.  Выберите регион и учетную запись хранения, которые будут использоваться для хранения VHD для этого образа. Установите флажок для продолжения.

    ![снимок экрана выбора учетной записи хранения][снимок экрана выбора учетной записи хранения]

    > [WACOM.NOTE] После этого начнется копирование образа из VM Depot в указанную учетную запись хранения. Это может занять определенное время — 15 минут или более.

4.  После изменения состояния образа на **Ожидается регистрация** выберите элемент **Регистрация** и введите понятное имя нового образа. Установите флажок для продолжения.

    ![снимок экрана регистрации образа][снимок экрана регистрации образа]

5.  После изменения состояния образа на **Доступно** выберите **+ Создать**, **Виртуальная машина**, **Из коллекции**. В ответ на запрос **выбора образа** выберите **Мои образы**, а затем выберите образ, созданный на предыдущих шагах. Щелкните стрелку для продолжения.

    ![снимок экрана образа][снимок экрана образа]

6.  Введите имя, размер и имя пользователя виртуальной машины. Щелкните стрелку для продолжения.

    ![снимок экрана имени ВМ, имени пользователя и т. д.][снимок экрана имени ВМ, имени пользователя и т. д.]

    > [WACOM.NOTE] В этом учебнике вам не требуется удаленно подключаться к ВМ с использованием протокола SSH. Выберите **Использовать пароль** и введите пароль, если вы не знакомы с использованием сертификата SSH.
    >
    > Дополнительные сведения об использовании SSH на ВМ с Linux в платформе Azure см. в разделе [Как использовать SSH с Linux на Azure][Как использовать SSH с Linux на Azure].

7.  Выберите, следует ли использовать новую или существующую облачную службу, а также выберите регион, в котором будет создана ВМ. Щелкните стрелку для продолжения.

    ![снимок экрана конфигурации ВМ][снимок экрана конфигурации ВМ]

8.  Настройте дополнительные конечные точки для ВМ. Так как мы будем обращаться к MongoDB на этой ВМ, добавьте новую конечную точку, используя следующие данные:

    -   Имя — MongoDB
    -   Протокол — TCP
    -   Общий порт — 27017
    -   Закрытый порт — 27017

    Чтобы предоставить доступ к веб-порталу MongoDB, добавьте другую конечную точку, используя следующую информацию:

    -   Имя — MongoDBWeb
    -   Протокол — TCP
    -   Общий порт — 28017
    -   Закрытый порт — 28017

    Наконец, поставьте галочку, чтобы настроить ВМ.

    ![снимок экрана настройки конечной точки][снимок экрана настройки конечной точки]

9.  После изменения состояния виртуальной машины на **Выполняется** вы сможете открыть в браузере адрес **http://\<YourVMDNSName\>.cloudapp.net:28017/**, чтобы проверить работу MongoDB. В нижней части страницы должен отображаться журнал со сведениями о службе, аналогично следующим данным:

        Fri Mar  7 18:57:16 [initandlisten] MongoDB starting : pid=1019 port=27017 dbpath=/var/lib/mongodb 64-bit host=localhost.localdomain
           18:57:16 [initandlisten] db version v2.2.3, pdfile version 4.5
           18:57:16 [initandlisten] git version: f570771a5d8a3846eb7586eaffcf4c2f4a96bf08
           18:57:16 [initandlisten] build info: Linux ip-10-2-29-40 2.6.21.7-2.ec2.v1.2.fc8xen #1 SMP Fri Nov 20 17:48:28 EST 2009 x86_64 BOOST_LIB_VERSION=1_49
           18:57:16 [initandlisten] options: { config: "/etc/mongodb.conf", dbpath: "/var/lib/mongodb", logappend: "true", logpath: "/var/log/mongodb/mongodb.log" }
           18:57:16 [initandlisten] journal dir=/var/lib/mongodb/journal
           18:57:16 [initandlisten] recover : no journal files present, no recovery needed
           18:57:17 [initandlisten] waiting for connections on port 27017

    Если журнал содержит ошибки, обратитесь к [документации MongoDB][документации MongoDB] для их устранения.

## Установка модулей и создание шаблонов

В этом разделе вы создадите новое приложение Node в среде разработки и добавите пакеты модулей с помощью npm. Для приложения списка задач будут использованы модули [Express][Express] и [Mongoose][Mongoose]. Модуль Express предоставляет платформу Model View Controller (Контроллер представления модели) для Node, а Mongoose — это драйвер для взаимодействия с MongoDB.

### Установка модуля Express и формирование шаблонов

1.  В командной строке измените каталоги на каталог **tasklist**. Если каталог **tasklist** не существует, создайте его.

    > [WACOM.NOTE] Этот учебный курс ссылается на папку **tasklist**. Полный путь к этой папке опущен, поскольку семантика путей зависит от операционной системы. Эту папку нужно создать в легкодоступном месте локальной файловой системы, например **~/node/tasklist** или **c:\\node\\tasklist**

2.  Введите следующую команду для установки команды express:

    npm install express-generator -g

    > [WACOM.NOTE] При использовании параметра "-g" в некоторых операционных системах может появиться сообщение об ошибке ***Ошибка: EPERM, chmod '/usr/local/bin/express'*** и предложение попробовать использовать учетную запись с правами администратора. В этом случае необходимо с помощью команды `sudo` запустить npm с более высоким уровнем привилегий.

    Результат этой команды должен выглядеть аналогично следующему:

        express-generator@4.0.0 C:\Users\username\AppData\Roaming\npm\node_modules\express-generator
        ├── mkdirp@0.3.5
        └── commander@1.3.2 (keypress@0.1.0)                                                                         

    > [WACOM.NOTE] Параметр "-g", используемый при установке модуля express, устанавливает его глобальным образом. Это делается для того, чтобы можно было получить доступ к команде ***express*** для формирования шаблонов веб-сайта без необходимости вводить дополнительные сведения о путях.

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

4.  Скопируйте файл **tasklist/bin/www** в файл с именем **server.js** в папке **tasklist**. Веб-сайты Azure предполагают, что точкой входа приложения Node.js будет либо **server.js**, либо **app.js**. Поскольку **app.js** уже существует, но не является точкой входа, следует использовать **server.js**.

5.  Отредактируйте файл **server.js**, удалив один из знаков "." в следующей строке.

        var app = require('../app');

    Измененная строка должна выглядеть следующим образом.

        var app = require('./app');

    Это необходимо, поскольку **server.js** (ранее **bin/www**,) теперь находится в той же папке, что и требуемый файл **app.js**.

### Установка дополнительных модулей

Файл **package.json** является одним из файлов, созданных командой **express**. Этот файл содержит список дополнительных модулей, необходимых для приложения Express. Позднее, при развертывании этого приложения на веб-сайте Azure, этот файл будет использоваться, чтобы определить, какие модули должны быть установлены в Azure для поддержки приложения.

1.  В папке **tasklist** выполните следующую команду для установки модулей, описанных в файле **package.json**:

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

2.  Затем введите следующую команду, чтобы локально установить модуль Mongoose, а также сохранить соответствующую запись в файл **package.json**:

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

    > [WACOM.NOTE] На любое сообщение об установке анализатора bson C++ можно не обращать внимания.

## Использование MongoDB в приложении node

В этом разделе базовое приложение, созданное командой **express**, будет расширено путем добавления файла **task.js**, содержащего модель для ваших задач. Также будет изменен существующий файл **app.js** и создан новый файл контроллера **tasklist.js** для использования этой модели.

### Создание модели

1.  В каталоге **tasklist** создайте новый каталог с именем **models**.

2.  В каталоге **models** создайте новый файл с именем **task.js**. Этот файл будет содержать модель для задач, создаваемых приложением.

3.  В начале файла **task.js** добавьте следующий код для ссылки на необходимые библиотеки:

        var mongoose = require('mongoose'),
            Schema = mongoose.Schema;

4.  Далее будет добавлен код для определения и экспорта модели. Эта модель будет использоваться для взаимодействия с базой данных MongoDB.

        var TaskSchema = new Schema({
            itemName      : String,
            itemCategory  : String,
            itemCompleted : { type: Boolean, default: false },
            itemDate      : { type: Date, default: Date.now }
        });

        module.exports = mongoose.model('TaskModel', TaskSchema);

5.  Сохраните и закройте файл **task.js**.

### Создание контроллера

1.  В каталоге **tasklist/routes** создайте новый файл с именем **tasklist.js** и откройте его в текстовом редакторе.

2.  Добавьте в **tasklist.js** следующий код. Он загружает модуль mongoose и модель задач, определенную в **task.js**. Функция TaskList используется для создания подключения к серверу MongoDB на основе значения **connection**:

        var mongoose = require('mongoose'),
            task = require('../models/task.js');

        module.exports = TaskList;

        function TaskList(connection) {
          mongoose.connect(connection);
        }

3.  Продолжит добавления в файл **tasklist.js**, добавляя методы, используемые для **showTasks**, **addTask** и **completeTasks**:

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

4.  Сохраните файл **tasklist.js**.

### Изменение app.js

1.  В каталоге **tasklist** откройте файл **app.js** в текстовом редакторе. Этот файл был создан ранее с помощью команды **express**.

2.  Добавьте следующий код в начало файла **app.js**. Он инициализирует **TaskList** со строкой подключения для сервера MongoDB:

        var TaskList = require('./routes/tasklist');
		var taskList = new TaskList(process.env.MONGODB_URI);

	Обратите внимание на вторую строку. Выполняется доступ к переменной среды, которая содержит сведения о подключении для экземпляра mongo и будет настроена позднее. При наличии локального экземпляра Mongo, работающего в целях разработки, может понадобиться временно задать для этого параметра значение localhost вместо process.env.MONGODB\_URI.

3.  Добавьте следующие строки:

        app.use('/', routes);
        app.use('/users', users);

    Замените две строки выше следующим кодом:

        app.get('/', taskList.showTasks.bind(taskList));
        app.post('/addtask', taskList.addTask.bind(taskList));
        app.post('/completetask', taskList.completeTask.bind(taskList));

    Это добавляет функции, определенные в **tasklist.js**, как маршруты.

4.  Сохраните файл **app.js**.

### Изменение представления индекса

1.  Измените каталоги на каталог **views** и откройте файл **index.jade** в текстовом редакторе.

2.  Заменит содержимое файла **index.jade** на код, приведенный ниже. Он определяет представление для отображения существующих задач, а также форму для добавления новых задач и пометки существующих задач как завершенных.

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

3.  Сохраните и закройте файл **index.jade**.

<!-- ##Run your application locally  To test the application on your local machine, perform the following steps:  1. From the command-line, change directories to the **tasklist** directory.  2. Set the MONGODB_URI environment variable on your development environment to point to the virtual machine hosting MongoDB. In the examples below, replace __mymongodb__ with your virtual machine name.      On a Windows system, use the following to set the environment variable.          set MONGODB_URI=mongodb://mymongodb.cloudapp.net/tasks      On an OS X or Linux-based system, use the following to set the environment variable.          set MONGODB_URI=mongodb://mymongodb.cloudapp.net/tasks         export MONGODB_URI      This will instruct the application to connect to MongoDB on the __mymongodb.cloudapp.net__ virtual machine created earlier, and to use a DB named 'tasks'.  2. Use the following command to launch the application locally:          node app.js  3. Open a web browser and navigate to http://localhost:3000. This should display a web page similar to the following:      ![A webpage displaying an empty tasklist][node-mongo-finished]  4. Use the provided fields for **Item Name** and **Item Category** to enter information, and then click **Add item**.      ![An image of the add item field with populated values.][node-mongo-add-item]  5. The page should update to display the item in the ToDo List table.      ![An image of the new item in the list of tasks][node-mongo-list-items]  6. To complete a task, simply check the checkbox in the Complete column, and then click **Update tasks**. While there is no visual change after clicking **Update tasks**, the document entry in MongoDB has now been marked as completed.  7. To stop the node process, go to the command-line and press the **CTRL** and **C** keys. -->

## Развертывание приложения в Azure

В действиях, описанных в этом разделе, для создания нового веб-сайта Azure используются средства командной строки Azure, а затем для развертывания приложения используется репозиторий Git. Для выполнения этих действий необходимо наличие подписки Azure.

> [WACOM.NOTE] Эти действия также можно выполнить с помощью портала Azure. Инструкции по использованию портала Azure для развертывания приложения Node.js см. в статье [Создание и развертывание приложения Node.js на веб-сайте Azure][Создание и развертывание приложения Node.js на веб-сайте Azure].

> [WACOM.NOTE] Если это первый созданный вами веб-сайт Azure, для развертывания приложения необходимо использовать портал Azure.

### Установка межплатформенного интерфейса командной строки Azure

Межплатформенный интерфейс командной строки Azure (xplat-cli) позволяет выполнять операции управления службами Azure. Если вы еще не установили и не настроили интерфейс xplat-cli в среде разработки, изучите раздел [Установка и настройка межплатформенного интерфейса командной строки Azure][Установка и настройка межплатформенного интерфейса командной строки Azure].

### Создание веб-сайта Azure

1.  В командной строке измените каталоги на каталог **tasklist**.

2.  Используйте следующую команду для создания нового веб-сайта Azure. Замените myuniquesitename уникальным именем созданного веб-сайта. Это значение используется как часть URL-адреса для созданного веб-сайта.

        azure site create myuniquesitename --git

    Будет предложено указать центр обработки данных, в котором будет создан веб-сайт. Выберите центр обработки данных, расположенный недалеко от вашего местоположения.

    Параметр `--git` создаст репозиторий Git локально в папке **tasklist**, если он отсутствует. Также будет создан [удаленный репозиторий Git][удаленный репозиторий Git] с именем "azure", который будет использоваться для публикации приложения в Azure. Будет создан файл [iisnode.yml][iisnode.yml], который содержит настройки, используемые Azure для размещения приложений Node. Наконец, также будет создан файл .gitignore, чтобы исключить папку Node-modules из публикации в .git.

    > [WACOM.NOTE] Если эта команда выполняется из каталога, который уже содержит репозиторий Git, каталог не будет инициализирован заново.

    > [WACOM.NOTE] Если параметр --git отсутствует, но каталог содержит репозиторий Git, то удаленный репозиторий azure все равно будет создан.

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

    > [WACOM.NOTE] Если это первый веб-сайт Azure для вашей подписки, необходимо использовать портал для создания веб-сайта. Дополнительные сведения см. в статье [Создание и развертывание приложения Node.js на веб-сайте Azure][Создание и развертывание приложения Node.js на веб-сайте Azure].

### Задание переменной среды MONGODB\_URI

Приложение ожидает, что строка подключения для экземпляра MongoDB будет доступна в переменной среды MONGODB\_URI. Чтобы задать для веб-сайта это значение, используйте следующую команду:

    azure site config add MONGODB_URI=mongodb://mymongodb.cloudapp.net/tasks

При этом будет создан новый параметр приложения для веб-сайта, который будет использоваться для заполнения переменной среды MONGODB\_URI, считываемой веб-сайтом. Замените значение "mymongodb.cloudapp.net" именем виртуальной машины, на которой была установлена MongoDB.

### Публикация приложения

1.  В окне Terminal измените каталоги на каталог **tasklist**, если он еще не является текущим.

2.  Используйте следующие команды для добавления и последующего сохранения файлов в локальном репозитории Git:

        git add .
        git commit -m "adding files"

3.  При принудительной отправке последних изменений репозитория Git на веб-сайт Azure необходимо указать, что целевой ветвью, используемой для контента этого веб-сайта, является **master**.

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

4.  После завершения операции принудительной отправки перейдите на веб-сайт с помощью команды `azure site browse` для просмотра приложения.

## Дальнейшие действия

Хотя в действиях этой статьи описывается использование MongoDB для хранения информации, можно также использовать табличную службу Azure. Дополнительные сведения см. в статье [Веб-приложение Node.js со службой таблиц Azure][Веб-приложение Node.js со службой таблиц Azure].

Чтобы понять, как использовать размещенный в другом месте экземпляр MongoDB, предоставленный MongoLabs, см. статью [Создание приложения Node.js в Azure с MongoDB с помощью надстройки MongoLab][Создание приложения Node.js в Azure с MongoDB с помощью надстройки MongoLab].

Сведения о защите MongoDB см. в разделе [Безопасность MongoDB][Безопасность MongoDB].

## Дополнительные ресурсы

[Средства командной строки Azure для Mac и Linux][Создание и развертывание приложения Node.js на веб-сайте Azure]
[Публикация на веб-сайтах Azure с использованием репозитория Git][Публикация на веб-сайтах Azure с использованием репозитория Git]

  [MongoDB]: http://www.mongodb.org
  [node]: http://nodejs.org
  [Создание приложения Node.js в Azure с MongoDB с помощью надстройки MongoLab]: /ru-ru/develop/nodejs/tutorials/website-with-mongodb-mongolab/
  [Веб-страница, показывающая пустой список задач]: ./media/store-mongodb-web-sites-nodejs-use-mac/todo_list_empty.png
  [Git]: http://git-scm.com
  [руководству по установке MongoDB]: http://docs.mongodb.org/manual/installation/
  [Установка MongoDB под Linux в Azure]: http://docs.mongodb.org/ecosystem/tutorial/install-mongodb-on-linux-in-azure/
  [портал управления Azure]: https://manage.windowsazure.com/
  [снимок экрана выбора VM Depot]: ./media/web-sites-nodejs-store-data-mongodb/browsedepot.png
  [снимок экрана выбора образа MongoDB v2.2.3 on Hardened Ubuntu]: ./media/web-sites-nodejs-store-data-mongodb/selectimage.png
  [снимок экрана выбора учетной записи хранения]: ./media/web-sites-nodejs-store-data-mongodb/storageaccount.png
  [снимок экрана регистрации образа]: ./media/web-sites-nodejs-store-data-mongodb/register.png
  [снимок экрана образа]: ./media/web-sites-nodejs-store-data-mongodb/myimages.png
  [снимок экрана имени ВМ, имени пользователя и т. д.]: ./media/web-sites-nodejs-store-data-mongodb/vmname.png
  [Как использовать SSH с Linux на Azure]: http://www.windowsazure.com/ru-ru/documentation/articles/linux-use-ssh-key/
  [снимок экрана конфигурации ВМ]: ./media/web-sites-nodejs-store-data-mongodb/vmconfig.png
  [снимок экрана настройки конечной точки]: ./media/web-sites-nodejs-store-data-mongodb/endpoints.png
  [документации MongoDB]: http://docs.mongodb.org/manual/
  [Express]: http://expressjs.com
  [Mongoose]: http://mongoosejs.com
  [Создание и развертывание приложения Node.js на веб-сайте Azure]: /ru-ru/develop/nodejs/tutorials/create-a-website-(mac)/
  [Установка и настройка межплатформенного интерфейса командной строки Azure]: /ru-ru/documentation/articles/xplat-cli/
  [удаленный репозиторий Git]: http://git-scm.com/docs/git-remote
  [iisnode.yml]: https://github.com/WindowsAzure/iisnode/blob/master/src/samples/configuration/iisnode.yml
  [Веб-приложение Node.js со службой таблиц Azure]: /ru-ru/develop/nodejs/tutorials/web-site-with-storage/
  [Безопасность MongoDB]: http://docs.mongodb.org/manual/security/
  [Публикация на веб-сайтах Azure с использованием репозитория Git]: /ru-ru/develop/nodejs/common-tasks/publishing-with-git/

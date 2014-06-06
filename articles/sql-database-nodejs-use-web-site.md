<properties linkid="develop-nodejs-tutorials-web-site-with-sql-database" urlDisplayName="Веб-сайт с базой данных SQL" pageTitle="Веб-сайт Node.js с базой данных SQL — учебник по Azure" metaKeywords="" description="Узнайте, как создать веб-сайт Node.js, который обращается к базе данных SQL и развернут в Azure" metaCanonical="" services="web-sites,sql-database" documentationCenter="Node.js" title="Веб-приложение Node.js, использующее базу данных SQL Azure" authors=""  solutions="" writer="" manager="" editor=""  />





# Веб-приложение Node.js, использующее базу данных SQL Azure

В этом учебном курсе показано, как использовать базу данных SQL, предоставляемую службой управления данными Azure, для хранения данных и доступа к ним из приложения [node], размещенного в Azure. Этот учебный курс разработан для читателей, обладающих определенным опытом использования Node и [Git].

Вы узнаете следующее:

* Как создать веб-сайт Azure и базу данных SQL с использованием портала управления Azure

* Как использовать для установки модулей Node диспетчер npm (node package manager)

* Как работать с базой данных SQL с помощью модуля node-sqlserver

* Как использовать параметры приложения для указания значений времени выполнения для приложения

В ходе учебного курса вы создадите простое веб-приложение для управления задачами, позволяющее создавать, извлекать и выполнять задачи. Эти задачи хранятся в базе данных SQL.
 
Файлы проекта для этого учебного курса будут храниться в каталоге с именем **tasklist**, завершенное приложение будет выглядеть примерно следующим образом:

![Веб-страница, показывающая пустой список задач][node-sql-finished]

<div class="dev-callout">
<b>Примечание.</b>
<p>Применяемый в этом учебнике драйвер Microsoft для Node.JS для SQL Server в данный момент доступен в качестве предварительной версии и использует компоненты времени выполнения, доступные только в операционных системах Microsoft Windows и Azure.</p>
</div>

<div class="dev-callout">
<strong>Примечание.</strong>
<p>Этот учебник ссылается на папку <strong>tasklist</strong>. Полный путь к этой папке опущен, поскольку семантика путей зависит от операционной системы. Эту папку нужно создать в легкодоступном месте вашей локальной файловой системы, таком как <strong>~/node/tasklist</strong> или <strong>c:\node\tasklist</strong></p>
</div>

<div class="dev-callout">
<strong>Примечание.</strong>
<p>Многие из описанных ниже действий могут быть выполнены из командной строки. Для выполнения этих действий используйте командную строку своей операционной системы, например <strong>cmd.exe</strong> (Windows) или <strong>Bash</strong> (Unix Shell). На компьютерах с OS X доступ к командной строке можно получить с помощью приложения Terminal.</p>
</div>

##Предварительные требования

Перед выполнением инструкций, приведенных в этой статье, следует убедиться, что установлены следующие компоненты:

* [node] версии 0.6.14 или выше

* [Git]

* Библиотеки Microsoft SQL Server Native Client — предоставляются в составе [пакета дополнительных компонентов Microsoft SQL Server 2012]

* Текстовый редактор

* Веб-браузер

<!--div chunk="../../Shared/Chunks/create-account-and-websites-note.md" /-->

##Создание веб-сайта с базой данных

Выполните следующие действия для создания веб-сайта Azure и базы данных SQL.

1. Войдите на [портал управления Azure][management-portal].
2. Щелкните значок **+ Создать** в нижнем левом углу портала.

	![Создание нового веб-сайта Azure][new-website]

3. Щелкните **ВЕБ-САЙТ**, а затем **НАСТРАИВАЕМОЕ СОЗДАНИЕ**.

	![Настраиваемое создание нового веб-сайта][custom-create]

	Введите значение в поле **URL-адрес**, выберите **Создать новую базу данных SQL** в раскрывающемся списке **БАЗА ДАННЫХ**, а затем выберите центр обработки данных для вашего веб-сайта в раскрывающемся списке **РЕГИОН**. Щелкните стрелку в нижней части диалогового окна.

	![Заполните сведения о веб-сайте][website-details-sqlazure]

4. Введите значение **ИМЯ** для базы данных, выберите **ВЫПУСК** [(WEB или BUSINESS)][sql-database-editions], выберите **МАКС. РАЗМЕР** для базы данных, выберите значение параметра **СОРТИРОВКА**, а затем выберите **СОЗДАТЬ сервер базы данных SQL**. Щелкните стрелку в нижней части диалогового окна.

	![Заполните параметры базы данных SQL][database-settings]

5. Введите имя и пароль администратора (и подтвердите пароль), выберите регион, где будет создан новый сервер базы данных SQL, и установите флажок **Разрешить службам Azure доступ к серверу**.

	![Создать новый сервер базы данных SQL][create-server]

	После создания веб-сайта появится сообщение **Создание веб-сайта "[ИМЯ САЙТА]" успешно завершено**. Теперь можно включить публикацию Git.

6. В списке веб-сайтов щелкните имя веб-сайта, чтобы открыть панель мониторинга быстрого запуска для этого веб-сайта.

	![Откройте панель мониторинга веб-сайта][go-to-dashboard]


7. В нижней части страницы "Быстрый запуск" щелкните **Настроить публикацию Git**. 

	![Настроить публикацию Git][setup-git-publishing]

8. Чтобы включить публикацию в Git, необходимо указать имя пользователя и пароль. Запишите создаваемые имя пользователя и пароль. (Если ранее уже был настроен репозиторий Git, этот шаг будет пропущен.)

	![Создание учетных данных для публикации][portal-git-username-password]

	На настройку репозитория потребуется несколько секунд.


9. После настройки репозитория появятся инструкции для передачи ваших файлов приложений в репозиторий. Запишите эти инструкции — они потребуются позже.

	![Инструкции для Git][git-instructions]

##Получение сведений о подключении к базе данных SQL

Чтобы подключиться к экземпляру базы данных SQL, который работает на веб-сайтах Azure, необходимо знать сведения о подключении. Чтобы получить сведения о подключении к базе данных SQL, выполните следующие действия:

1. На портале управления Azure щелкните **СВЯЗАННЫЕ РЕСУРСЫ**, а затем щелкните имя нужной базы данных.

	![Связанные ресурсы][linked-resources]

2. Щелкните **Просмотреть строки подключения**.

	![Строка подключения][connection-string]
	
3. Запишите строку подключения в разделе **ODBC** открывшегося диалогового окна, так как она понадобится позже.

##Разработка таблицы задач

Чтобы создать таблицу базы данных, используемую для хранения элементов приложения списка задач, выполните следующие действия:

1. На портале управления Azure выберите свою базу данных SQL и щелкните элемент **УПРАВЛЕНИЕ** в нижней части страницы. В случае появления сообщения о том, что текущий IP-адрес не является частью правил брандмауэра, нажмите кнопку **ОК** для добавления этого IP-адреса.

	![кнопка управления][sql-azure-manage]

2. Выполните вход с помощью имени и пароля, выбранных при создании сервера базы данных.

	![имя для управления базой данных][sql-azure-login]

3. В левой нижней части страницы выберите **Разработка**, а затем **Новая таблица**.

	![Новая таблица][sql-new-table]

4. Введите для параметра **Имя таблицы** значение "tasks" и установите флажок **Удостоверение** для столбца **ИД**.

	![установка "tasks" в качестве имени таблицы и выбор удостоверения][table-name-identity]

5. Измените значение **Column1** на **name**, а значение **Column2** на **category**. Добавьте два новых столбца, нажав кнопку **Добавить столбец**. Первый новый столбец должен иметь имя **created** и тип **date**. Второй новый столбец должен иметь имя **completed** и тип **bit**. Оба новых столбца должны быть помечены как **Обязательный**.

	![структура готовой таблицы][completed-table]

6. Нажмите кнопку **Сохранить**, чтобы сохранить внесенные в таблицу изменения. Теперь можно закрыть страницу управления базой данных SQL.

##Установка модулей и создание шаблонов

В этом разделе вы создадите новое приложение Node и добавите пакеты модулей с помощью npm. Для приложения списка задач будут использованы модули [express] и [node-sqlserver]. Модуль Express предоставляет платформу Model View Controller (Контроллер представления модели) для Node, а модули node-sqlserver предоставляют возможность подключения к базе данных SQL Azure.

###Установка модуля Express и создание шаблонов

1. Из командной строки перейдите в каталог **tasklist**. Если каталог **tasklist** не существует, создайте его.

2. Введите следующую команду для установки Express.

		npm install express -g

    <div class="dev-callout">
	<strong>Примечание.</strong>
	<p>При использовании параметра "-g" в некоторых операционных системах может появиться сообщение об ошибке <strong>Error: EPERM, chmod "/usr/local/bin/express"</strong> и предложение попробовать использовать учетную запись с правами администратора. В этом случае необходимо с помощью команды <strong>sudo</strong> запустить npm с более высоким уровнем привилегий.</p>
	</div>

    Результат этой команды должен выглядеть аналогично следующему:

		express@2.5.9 /usr/local/lib/node_modules/express
		├── mime@1.2.4
		├── mkdirp@0.3.0
		├── qs@0.4.2
		└── connect@1.8.7

	<div class="dev-callout">
	<strong>Примечание.</strong>
	<p>Параметр "-g", используемый при установке модуля express, устанавливает его глобальным образом. Это делается для того, чтобы можно было получить доступ к команде <strong>express</strong> для формирования шаблонов веб-сайта без необходимости вводить дополнительные сведения о путях.</p>
	</div>

4. Для создания шаблонов, которые будут использоваться для данного приложения, используйте команду **express**:

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

###Установка дополнительных модулей

1. В командной строке перейдите в папку **tasklist** и введите следующую команду для установки модулей, описанных в файле **package.json**:

        npm install

    Результат этой команды должен выглядеть аналогично следующему:

		express@2.5.8 ./node_modules/express
		├── mime@1.2.4
		├── qs@0.4.2
		├── mkdirp@0.3.0
		└── connect@1.8.7
		jade@0.26.0 ./node_modules/jade
		├── commander@0.5.2
		└── mkdirp@0.3.0

	Будут установлены все стандартные модули, необходимые для Express.

2. Далее используйте следующую команду для добавления модуля nconf. Этот модуль будет использоваться приложением для считывания строки подключения базы данных из файла конфигурации.

	npm install nconf -save

2. Далее загрузите двоичную версию драйвера Microsoft для Node.JS для SQL Server из [Центра загрузки].

3. Извлеките архив в каталог **tasklist\\node\_modules**.

4. Запустите файл **msnodesql-install.cmd** в каталоге **tasklist\\node\_modules**. Это приведет к созданию подкаталога **msnodesql** в разделе **node\_modules** и к перемещению файлов драйвера в эту новую структуру каталогов.

5. Удалите файл **msnodesql-install.cmd**, он больше не нужен.

##Использование базы данных SQL в приложении Node

В этом разделе вы расширите возможности созданного базового приложения с помощью команды **express**, изменив имеющийся **app.js**, и создадите новые файлы **index.js** для использования созданной ранее базы данных.

###Изменение контроллера

1. В каталоге **tasklist/routes** откройте файл **index.js** в текстовом редакторе.

2. Замените существующий код в файле **index.js** на следующий код. При этом загружаются модули msnodesql и nconf, затем nconf используется для загрузки строки подключения из переменной среды с именем **SQL\_CONN** или из значения **SQL\_CONN** в файле **config.json**.

		var sql = require('msnodesql')
		    , nconf = require('nconf');

		nconf.env()
	         .file({ file: 'config.json' });
		var conn = nconf.get("SQL_CONN");

2. Продолжайте доработку файла **index.js**, добавив методы **index** и **updateItem**. Метод **index** возвращает все незавершенные задачи из базы данных, а метод **updateItem** помечает выбранные задачи как завершенные.

		exports.index = function(req, res) {
		    var select = "select * from tasks where completed = 0";
		    sql.query(conn, select, function(err, items) {
		        if(err)
		            throw err;
		        res.render('index', { title: 'My ToDo List ', tasks: items });
		    });
		};

		exports.updateItem = function(req, res) {
		    var item = req.body.item;
		    if(item) {
		        var insert = "insert into tasks (name, category, created, completed) values (?, ?, GETDATE(), 0)";
		        sql.query(conn, insert, [item.name, item.category], function(err) {
		            if(err)
		                throw err;
		            res.redirect('/');
		        });
		    } else {
		        var completed = req.body.completed;
		        if(!completed.forEach)
		            completed = [completed];
		        var update = "update tasks set completed = 1 where id in (" + completed.join(",") + ")";
		        sql.query(conn, update, function(err) {
		            if(err)
		                throw err;
		            res.redirect('/');
		        });
		    }
		}

3. Сохраните файл **index.js**.

###Изменить app.js

1. В каталоге **tasklist** откройте файл **app.js** в текстовом редакторе. Этот файл был создан ранее с помощью команды **express**.

2. Прокрутите файл app.js вниз до появления следующего кода.

		app.configure('development', function(){
  		app.use(express.errorHandler());
		});

3. Теперь вставьте следующий код.


        app.get('/', routes.index);
		app.post('/', routes.updateItem);


 При этом в метод **updateItem**, который был ранее добавлен в файл **index.js**, добавляется новый маршрут. 

       		
3. Сохраните файл **app.js**.

###Изменение представления индекса

1. Перейдите в каталог **views** и откройте файл **index.jade** в текстовом редакторе.

2. Замените содержимое файла **index.jade** на код, приведенный ниже. Он определяет представление для отображения существующих задач, а также форму для добавления новых задач и пометки существующих задач как завершенных.

		h1= title
		br

		form(action="/", method="post")
		  table(class="table table-striped table-bordered")
		    thead
		      tr
		        td Name
		        td Category
		        td Date
		        td Complete
		    tbody
		    each task in tasks
		      tr
		        td #{task.name}
		        td #{task.category}
		        td #{task.created}
		        td
		          input(type="checkbox", name="completed", value="#{task.ID}", checked=task.completed == 1)
		  button(type="submit", class="btn") Update tasks
		hr

		form(action="/", method="post", class="well")
		  label Item Name:
		  input(name="item[name]", type="textbox")
		  label Item Category:
		  input(name="item[category]", type="textbox")
		  br
		  button(type="submit", class="btn") Add Item

3. Сохраните и закройте файл **index.jade**.

###Изменение глобального макета

Файл **layout.jade** в каталоге **views** используется как глобальный шаблон для других файлов **.jade**. На этом шаге он будет изменен для использования [Twitter Bootstrap](https://github.com/twbs/bootstrap) — набора средств, упрощающих разработку привлекательного веб-сайта.

1. Загрузите и извлеките файлы [Twitter Bootstrap](http://getbootstrap.com/). Скопируйте файл **bootstrap.min.css** из папки **bootstrap\\css** в каталог **public\\stylesheets** своего приложения списка задач.

2. В папке **views** откройте файл **layout.jade** в текстовом редакторе и замените его содержимое на следующее:

		!!!html
		html
		  head
		    title= title
		    meta(http-equiv='X-UA-Compatible', content='IE=10')
		    link(rel='stylesheet', href='/stylesheets/style.css')
		    link(rel='stylesheet', href='/stylesheets/bootstrap.min.css')
		  body(class='app')
		    div(class='navbar navbar-fixed-top')
		      .navbar-inner
		        .container
		          a(class='brand', href='/') My Tasks
		    .container!= body

3. Сохраните файл **layout.jade**.

###Создание файла конфигурации

Файл **config.json** содержит строку подключения, используемую для подключения к базе данных SQL, и считывается файлом **index.js** во время выполнения. Чтобы создать этот файл, выполните следующие действия:

1. В каталоге **tasklist** создайте новый файл с именем **config.json** и откройте его в текстовом редакторе.

2. Содержание файла **config.json** должно быть похоже на следующее:

		{
		  "SQL_CONN" : "connection_string"
		}

	Замените **connection_string** на возвращенное ранее значение строки подключения ODBC.

3. Сохраните файл.

##Локальный запуск приложения

Для проверки приложения на локальном компьютере выполните следующие действия:

1. Из командной строки перейдите в каталог **tasklist**.

2. Используйте следующую команду для локального запуска приложения:

        node app.js

3. Откройте браузер и перейдите по адресу http://127.0.0.1:3000. Должна появиться веб-страница, похожая на следующую:

    ![Веб-страница, показывающая пустой список задач][node-sql-empty]

4. Используйте предоставленные поля **Имя элемента** и **Имя категории** для ввода данных, а затем нажмите кнопку **Добавить элемент**.

5. Страница должна обновиться, чтобы показать элемент в таблице списка задач.

    ![Изображение нового элемента в списке задач][node-sql-list-items]

6. Чтобы завершить задачу, просто установите флажок в столбце "Завершено" и нажмите кнопку **Обновить задачи**.

7. Чтобы остановить процесс Node, перейдите в командную строку и нажмите сочетание клавиш **CTRL** и **C**.

##Развертывание приложения в Azure

В этом разделе вы воспользуетесь шагами по развертыванию, доступными после создания веб-сайта, для публикации приложения в Azure.

###Публикация приложения

1. В окне командной строки перейдите в каталог **tasklist**, если он еще не является текущим.

2. Используйте следующие команды, чтобы инициализировать локальный репозиторий git для приложения, добавить в него файлы приложения и передать эти файлы в Azure.

		git init
		git add .
		git commit -m "adding files"
		git remote add azure [URL for remote repository]
		git push azure master
	
	В конце развертывания должно появиться заявление, похожее на следующее:
	
		To https://username@tabletasklist.azurewebsites.net/TableTasklist.git
 		 * [new branch]      master -> master

4. После завершения операции принудительной отправки перейдите на **http://[имя сайта].azurewebsites.net/** для просмотра приложения.

###Переключение на переменную среды

Ранее был реализован код, выполняющий поиск переменной среды **SQL_CONN** для строки подключения или загружающий значение из файла **config.json**. При следующих действиях в конфигурации веб-сайта будет создана пара "ключ/значение", обеспечивающая реальный доступ к приложению с помощью переменной среды.

1. На портале управления Azure щелкните **Веб-сайты**, а затем выберите свой веб-сайт.

	![Откройте панель мониторинга веб-сайта][go-to-dashboard]

2. Щелкните **НАСТРОЙКА** и найдите на странице раздел **параметры приложения**. 

	![ссылка настройки][web-configure]

3. В разделе **параметры приложения** введите значение **SQL_CONN** в поле **КЛЮЧ** и строку подключения ODBC в поле **ЗНАЧЕНИЕ**. Наконец, установите флажок.

	![параметры приложения][app-settings]

4. И, наконец, щелкните значок **СОХРАНИТЬ** в нижней части страницы, чтобы принять это изменение в среде выполнения.

	![сохранение параметров приложения][app-settings-save]

5. В командной строке перейдите в каталог **tasklist** и введите следующую команду для удаления файла **config.json**:

		git rm config.json
		git commit -m "Removing config file"

6. Выполните следующую команду, чтобы развернуть изменения в Azure.

		git push azure master

После развертывания изменений в Azure веб-приложение должно продолжить работать, поскольку оно теперь считывает строку подключения из записи **параметры приложения**. Для проверки измените значение записи **SQL_CONN** в **параметрах приложения** на неправильное значение. После сохранения этого значения веб-сайт должен перестать работать из-за неправильного значения строки подключения.

##Дальнейшие действия

* [Веб-приложение Node.js с MongoDB]

* [Веб-приложение Node.js с табличным хранилищем]

##Дополнительные ресурсы

[Средство командной строки Azure для Mac и Linux]    
[Создание и развертывание приложения Node.js на веб-сайтах Azure]: /ru-ru/develop/nodejs/tutorials/create-a-website-(mac)/
[Публикация на веб-сайтах Azure с использованием репозитория Git]: /ru-ru/develop/nodejs/common-tasks/publishing-with-git/
[Центр разработчиков Azure]: /ru-ru/develop/nodejs/
[Веб-приложение Node.js с табличным хранилищем]: /ru-ru/develop/nodejs/tutorials/web-site-with-storage/

[node]: http://nodejs.org
[Git]: http://git-scm.com
[express]: http://expressjs.com
[бесплатно]: http://windowsazure.com
[Удаленный репозиторий Git]: http://git-scm.com/docs/git-remote

[Веб-приложение Node.js с MongoDB]: ../store-mongolab-web-sites-nodejs-store-data-mongodb/
[Средство командной строки Azure для Mac и Linux]: /ru-ru/develop/nodejs/how-to-guides/command-line-tools/
[Создание и развертывание приложения Node.js на веб-сайте Azure]: ./web-site-with-mongodb-Mac
[Публикация на веб-сайтах Azure с использованием репозитория Git]: ../CommonTasks/publishing-with-git
[Портал Azure]: http://windowsazure.com
[management-portal]: https://manage.windowsazure.com/
[node-sqlserver]: https://github.com/WindowsAzure/node-sqlserver
[пакета дополнительных компонентов Microsoft SQL Server 2012]: http://www.microsoft.com/ru-ru/download/details.aspx?id=29065
[sql-database-editions]: http://msdn.microsoft.com/ru-ru/library/windowsazure/ee621788.aspx
[Центра загрузки]: http://www.microsoft.com/ru-ru/download/details.aspx?id=29995
[Twitter Bootstrap]: http://twitter.github.com/bootstrap/

[app-settings-save]: ./media/sql-database-nodejs-use-web-site/savebutton.png
[web-configure]: ./media/sql-database-nodejs-use-web-site/sql-task-configure.png
[app-settings]: ./media/sql-database-nodejs-use-web-site/appsettings.png
[connection-string]: ./media/sql-database-nodejs-use-web-site/connection_string.jpg
[website-details-sqlazure]: ./media/sql-database-nodejs-use-web-site/website_details_sqlazure.jpg
[database-settings]: ./media/sql-database-nodejs-use-web-site/database_settings.jpg
[create-server]: ./media/sql-database-nodejs-use-web-site/create_server.jpg
[go-to-dashboard]: ./media/sql-database-nodejs-use-web-site/go_to_dashboard.png
[setup-git-publishing]: ./media/sql-database-nodejs-use-web-site/setup_git_publishing.png
[portal-git-username-password]: ./media/sql-database-nodejs-use-web-site/git-deployment-credentials.png


[git-instructions]: ./media/sql-database-nodejs-use-web-site/git-instructions.png
[linked-resources]: ./media/sql-database-nodejs-use-web-site/linked_resources.jpg
[new-website]: ./media/sql-database-nodejs-use-web-site/new_website.jpg
[custom-create]: ./media/sql-database-nodejs-use-web-site/custom_create.png

[node-sql-finished]: ./media/sql-database-nodejs-use-web-site/sql_todo_final.png
[node-sql-empty]: ./media/sql-database-nodejs-use-web-site/sql_todo_empty.png
[node-sql-list-items]: ./media/sql-database-nodejs-use-web-site/sql_todo_list.png





[sql-azure-manage]: ./media/sql-database-nodejs-use-web-site/sql-manage.png
[sql-azure-login]: ./media/sql-database-nodejs-use-web-site/sqlazurelogin.png
[sql-new-table]: ./media/sql-database-nodejs-use-web-site/new-table.png
[table-name-identity]: ./media/sql-database-nodejs-use-web-site/table-name-identity.png
[completed-table]: ./media/sql-database-nodejs-use-web-site/table-columns.png


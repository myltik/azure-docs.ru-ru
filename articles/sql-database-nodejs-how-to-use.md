<properties linkid="develop-node-how-to-sql-database" urlDisplayName="База данных SQL" pageTitle="Использование базы данных SQL (Node.js) - руководство по функциональным возможностям Azure" metaKeywords="" description="Сведения об использовании базы данных Azure из Node.js." metaCanonical="" services="sql-database" documentationCenter="Node.js" title="Сведения о доступе к базе данных SQL Azure из Node.js" authors=""  solutions="" writer="" manager="" editor=""  />





#Сведения о доступе к базе данных SQL Azure из Node.js

В этом руководстве демонстрируются основы использования драйвера Microsoft для Node.JS в SQL Server в целях получения доступа к базе данных SQL Azure. Приведенные здесь сценарии включают **создание базы данных SQL** и **подключение к базе данных SQL**. В этом руководстве освещается создание базы данных SQL [в предварительной версии портала управления][preview-portal].

##Оглавление

* [Основные понятия](#Concepts)
* [Практическое руководство. Настройка среды](#Setup)
* [Практическое руководство. Создание базы данных SQL](#CreateServer)
* [Практическое руководство. Получение сведений о подключении к базе данных SQL](#ConnectionInfo)
* [Практическое руководство. Подключение к экземпляру базы данных SQL](#Connect)
* [Рекомендации по развертыванию Azure](#Deploy)
* [Дальнейшие действия](#NextSteps)

<h2><a id="Concepts"></a>Основные понятия</h2>

###Что такое база данных SQL Azure

База данных SQL Azure предоставляет собой систему управления реляционной базой данных для Azure и основана на технологии SQL Server. С базой данных SQL можно легко подготовить и развернуть решения реляционных баз данных в облаке и воспользоваться преимуществами центра распределенных данных, которые представляют собой корпоративные преимущества доступности, масштабируемости и безопасности вместе со встроенной защитой данных и возможностями самостоятельной регенерации.

##Что такое драйвер Microsoft для Node.JS для SQL Server

Драйвер Microsoft для Node.JS для SQL Server позволяет разработчикам получать доступ к данным, хранящимся в Microsoft SQL Server или базе данных SQL Azure из приложения Node.js. В настоящее время драйвер доступен только в виде предварительной версии; дополнительные возможности будут интегрироваться в проект сразу же после завершения. Дополнительные сведения о драйвере см. на [странице Github] проекта драйвера Microsoft для Node.JS для SQL Server и соответствующей [Вики].

<div class="dev-callout">
<b>Примечание.</b>
<p>Драйвер Microsoft для Node.JS для SQL Server в данный момент доступен в качестве предварительной версии и использует компоненты времени выполнения, доступные только в операционных системах Microsoft Windows и Azure.</p>
</div>

<h2><a id="Setup"></a>Практическое руководство. Настройка среды</h2>

###Установка собственного клиента SQL Server

Драйвер Microsoft SQL Server для Node.js нуждается в наличии собственного клиента SQL Server. В то время как собственный клиент автоматически становится доступным при развертывании приложения в Azure, он может быть установлен и в локальной среде. Можно установить собственный клиент SQL Server со страницы загрузки [пакета дополнительных компонентов Microsoft SQL Server 2012].

<div class="dev-callout">
<b>Примечание.</b>
<p>Собственный клиент SQL Server доступен только для операционной системы Microsoft Windows. Хотя этот драйвер доступен в Azure, вы не сможете протестировать свое приложение локально, используя сведения, приведенные в этой статье, если ведете разработку в операционной системе, отличной от Microsoft Windows.</p>
</div>

###Установка Node.js

Node.js может быть установлен с [http://nodejs.org/#download](http://nodejs.org/#download). Если пакет установки не доступен для вашей операционной системы, можно собрать Node.js их исходного кода.

<h2><a id="CreateServer"></a>Практическое руководство. Создание базы данных SQL</h2>

Выполните следующие действия для создания базы данных SQL Azure.

1. Выполните вход на [предварительную версию портала управления][preview-portal].
2. Щелкните значок **+ Создать** в нижнем левом углу портала.

	![Создание нового веб-сайта Azure][new-website]

3. Щелкните **База данных SQL**, затем **Настраиваемое создание**.

	![Настраиваемое создание новой базы данных SQL][custom-create]

4. Введите значение для параметра **NAME** базы данных, выберите **EDITION** (WEB или BUSINESS), выберите **MAX SIZE** для базы данных, выберите **COLLATION** и выберите **Создать сервер базы данных SQL**. Щелкните стрелку в нижней части диалогового окна. (Обратите внимание, что если база данных SQL была создана ранее, можно выбрать существующий сервер в раскрывающемся списке **Выберите сервер**.)

	![Заполните параметры базы данных SQL][database-settings]

5. Введите имя и пароль администратора (и подтвердите пароль), выберите регион, где будет создана новая база данных SQL, и установите флажок `Разрешить службам Azure доступ к серверу`.

	![Создать новый сервер базы данных SQL][create-server]

Чтобы просмотреть сведения о сервере и базе данных, щелкните **Базы данных SQL** в предварительной версии портала управления. Можно щелкнуть **DATABASES** или **SERVERS** для просмотра соответствующих сведений.

![Просмотр сведений о сервере и базе данных][sql-dbs-servers]

<h2><a id="ConnectionInfo"></a>Практическое руководство. Получение сведений о подключении к базе данных SQL</h2>

Чтобы получить сведения о подключении к базе данных, щелкните **БАЗЫ ДАННЫХ SQL** на портале, затем щелкните имя базы данных.

![Просмотр сведений о базе данных][go-to-db-info]

Затем щелкните **Просмотреть строки подключения**

![Показать строки подключения][show-connection-string]

В разделе ODBC появившегося окна обратите внимание на значения строки подключения. Это строка подключения, которая будет использоваться при подключении к базе данных SQL из приложения узла. Ваш пароль – это пароль, который вы использовали при создании базы данных SQL.

<h2><a id="Connect"></a>Практическое руководство. Подключение к экземпляру базы данных SQL</h2>

###Установка node-sqlserver

Драйвер Microsoft для Node.JS для SQL Server доступен как собственный модуль node-sqlserver. Двоичная версия этого модуля доступна в [центре загрузки]. Чтобы использовать двоичную версию, выполните следующие действия:

1. Извлеките двоичный архив в каталог **node\_modules** вашего приложения.
2. Запустите файл **node-sqlserver-install.cmd**, извлеченный из архива. Это приведет к созданию подкаталога **node-sqlserver** в разделе **node\_modules** и к перемещению файлов драйвера в эту новую структуру каталогов.
3. Удалите файл **node-sqlserver-install.cmd**, он больше не нужен.

  

<div class="dev-callout">
<b>Примечание.</b>
<p>Можно также установить модуль node-sqlserver с помощью служебной программы npm; однако это вызовет node-gyp для создания двоичной версии модуля в системе.</p>
</div>

###Укажите строку подключения

Чтобы использовать модуль node-sqlserver, необходимо указать это в своем приложении и указать строку подключения. Строка подключения должна иметь значение ODBC, возвращенное в разделе [Практическое руководство. Получение сведений о подключении к базе данных SQL](#ConnectionInfo) этой статьи. Код должен выглядеть аналогично следующему:

    var sql = require('node-sqlserver');
	var conn_str = "Driver={SQL Server Native Client 10.0};Server=tcp:{dbservername}.database.windows.net,1433;Database={database};Uid={username};Pwd={password};Encrypt=yes;Connection Timeout=30;";

###Запрос к базе данных

Запросы можно выполнить, указав инструкцию Transact-SQL с методом **query**. Следующий код создает HTTP-сервер и возвращает данные из строк **ID**, **Column1**, и **Column2** в таблице **Test** при просмотре веб-страницы:

	var http = require('http')
	var port = process.env.port||3000;
	http.createServer(function (req, res) {
	    sql.query(conn_str, "SELECT * FROM TestTable", function (err, results) {
	        if (err) {
	            res.writeHead(500, { 'Content-Type': 'text/plain' });
	            res.write("Got error :-( " + err);
	            res.end("");
	            return;
	        }
	        res.writeHead(200, { 'Content-Type': 'text/plain' });
	        for (var i = 0; i < results.length; i++) {
	            res.write("ID: " + results[i].ID + " Column1: " + results[i].Column1 + " Column2: " + results[i].Column2);
	        }
	        res.end("; Done.");
	    });
	}).listen(port);

Хотя в приведенном выше примере показано, как возвратить все строки в наборе результатов за один раз, можно также возвратить объект инструкции, который позволяет подписываться на события. Это позволяет получать отдельные строки и столбцы по мере их возвращения. В следующем примере показано, как это сделать:

	var stmt = sql.query(conn_str, "SELECT * FROM TestTable");
	stmt.on('meta', function (meta) { console.log("We've received the metadata"); });
	stmt.on('row', function (idx) { console.log("We've started receiving a row"); });
	stmt.on('column', function (idx, data, more) { console.log(idx + ":" + data);});
	stmt.on('done', function () { console.log("All done!"); });
	stmt.on('error', function (err) { console.log("We had an error: " + err); });

<h2><a id="Deploy"></a>Рекомендации по развертыванию Azure</h2>

<div class="dev-callout">
<b>Примечание.</b>
<p>Следующая информация основана на работе с предварительной версией драйвера Microsoft для Node.JS для SQL Server. Сведения в этом разделе может не оказаться самыми новыми в отношении модуля node-sqlserver в Azure. Для получения последних сведений о <a href="https://github.com/WindowsAzure/node-sqlserver">драйвере Microsoft для Node.JS для SQL Server посетите страницу этого проекта</a> на Github.</p>
</div>

Azure не установит модуль node-sqlserver динамически во время выполнения, поэтому необходимо убедиться, что в развертывании вашего приложения включена двоичная версия модуля. Вы можете проверить, что развертывание содержит двоичную версию модуля, убедившись, что существует следующая структура каталогов, в которой содержатся приведенные ниже файлы:

	application directory
		node_modules
			node-sqlserver
				lib

Каталог **node-sqlserver** должен содержать файл **package.json**. Каталог **lib** должен содержать файл **sql.js** и **sqlserver.node**, который представляет собой компилированную форму модуля node-sqlserver.

Дополнительные сведения о развертывании приложения Node.js в Azure см. в разделах [Создание и развертывание приложения Node.js на веб-сайте Azure] и [Облачная служба Node.js].

<h2><a id="NextSteps"></a>Дальнейшие действия</h2>

* [Введение в драйвер Microsoft для Node.JS для SQL Server]
* [Драйвер Microsoft для Node.JS для SQL Server на Github.com]

[Облачная служба Node.js]: /ru-ru/develop/nodejs/tutorials/getting-started/
[Создание и развертывание приложения Node.js на веб-сайте Azure]: /ru-ru/develop/nodejs/tutorials/create-a-website-(mac)/
[Введение в драйвер Microsoft для Node.JS для SQL Server]: http://blogs.msdn.com/b/sqlphp/archive/2012/06/08/introducing-the-microsoft-driver-for-node-js-for-sql-server.aspx
[странице Github]: https://github.com/WindowsAzure/node-sqlserver
[Драйвер Microsoft для Node.JS для SQL Server на Github.com]: https://github.com/WindowsAzure/node-sqlserver
[Вики]: https://github.com/WindowsAzure/node-sqlserver/wiki
[Установка Python и пакета SDK]: /ru-ru/develop/python/common-tasks/install-python/
[пакета дополнительных компонентов Microsoft SQL Server 2012]: http://www.microsoft.com/ru-ru/download/details.aspx?id=29065
[preview-portal]: https://manage.windowsazure.com
[центре загрузки]: http://www.microsoft.com/ru-ru/download/details.aspx?id=29995

[new-website]: ./media/sql-database-nodejs-how-to-use/new_website.jpg
[custom-create]: ./media/sql-database-nodejs-how-to-use/create_custom_sql_db.jpg
[database-settings]: ./media/sql-database-nodejs-how-to-use/new-sql-db.png
[create-server]: ./media/sql-database-nodejs-how-to-use/db-server-settings.png
[sql-dbs-servers]: ./media/sql-database-nodejs-how-to-use/sql-dbs-portal.png



[go-to-db-info]: ./media/sql-database-nodejs-how-to-use/go-to-db-info.png
[show-connection-string]: ./media/sql-database-nodejs-how-to-use/show-connection-string.png


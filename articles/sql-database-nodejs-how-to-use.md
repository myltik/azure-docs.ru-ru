<properties linkid="develop-node-how-to-sql-database" urlDisplayName="SQL Database" pageTitle="How to use SQL Database (Node.js) - Azure feature guide" metaKeywords="" description="Learn how to use Azure SQL Database from Node.js." metaCanonical="" services="sql-database" documentationCenter="nodejs" title="How to Access Azure SQL Database from Node.js" authors="larryfr" solutions="" manager="" editor="" />

<tags ms.service="sql-database" ms.workload="data-management" ms.tgt_pltfrm="na" ms.devlang="nodejs" ms.topic="article" ms.date="01/01/1900" ms.author="larryfr"></tags>

# Доступ к базе данных SQL Windows Azure из Node.js

В этом руководстве демонстрируются основы использования драйвера Microsoft для Node.JS в SQL Server в целях получения доступа к базе данных Azure SQL. Приведенные здесь сценарии включают **создание базы данных SQL** и **подключение к базе данных SQL**. В этом руководстве описан процесс создания базы данных SQL с помощью [портала управления Azure][портала управления Azure].

## Оглавление

-   [Основные понятия][Основные понятия]
-   [Практическое руководство. Настройка среды разработки][Практическое руководство. Настройка среды разработки]
-   [Практическое руководство. Создание базы данных SQL Azure][Практическое руководство. Создание базы данных SQL Azure]
-   [Практическое руководство. Получение сведений о подключении к базе данных SQL][Практическое руководство. Получение сведений о подключении к базе данных SQL]
-   [Практическое руководство. Подключение к экземпляру базы данных SQL][Практическое руководство. Подключение к экземпляру базы данных SQL]
-   [Рекомендации по развертыванию в Azure][Рекомендации по развертыванию в Azure]
-   [Дальнейшие действия][Дальнейшие действия]

## <span id="Concepts"></span></a>Основные понятия

### Что такое база данных SQL Azure

База данных SQL Azure предоставляет собой систему управления реляционной базой данных для Azure и основана на технологии SQL Server. С базой данных SQL можно легко подготовить и развернуть решения реляционных баз данных в облаке и воспользоваться преимуществами центра распределенных данных, которые представляют собой корпоративные преимущества доступности, масштабируемости и безопасности вместе со встроенной защитой данных и возможностями самостоятельной регенерации.

## What is the Microsoft Driver for Node.JS for SQL Server

The Microsoft Driver for Node.JS for SQL Server allows developers to access data stored in Microsoft SQL Server or Azure SQL Database from a Node.js application. В настоящее время драйвер доступен только в виде предварительной версии; дополнительные возможности будут интегрироваться в проект сразу же после завершения. Дополнительные сведения о драйвере см. на [странице Github][странице Github] проекта драйвера Microsoft для Node.JS для SQL Server и соответствующей [Вики][Вики].

<div class="dev-callout">
<b>Примечание.</b>
<p>Драйвер Microsoft для Node.JS для SQL Server в данный момент доступен в качестве предварительной версии и использует компоненты времени выполнения, доступные только в операционных системах Microsoft Windows и Azure.</p>
</div>

## <span id="Setup"></span></a>Практическое руководство. Настройка среды разработки

### Установка собственного клиента SQL Server

Драйвер Microsoft SQL Server для Node.js нуждается в наличии собственного клиента SQL Server. В то время как собственный клиент автоматически становится доступным при развертывании приложения в Azure, он может быть установлен и в локальной среде. Можно установить собственный клиент SQL Server со страницы загрузки [пакета дополнительных компонентов Microsoft SQL Server 2012][пакета дополнительных компонентов Microsoft SQL Server 2012].

<div class="dev-callout">
<b>Примечание.</b>
<p>Собственный клиент SQL Server доступен только для операционной системы Microsoft Windows. Хотя этот драйвер доступен в Azure, вы не сможете протестировать свое приложение локально, используя сведения, приведенные в этой статье, если ведете разработку в операционной системе, отличной от Microsoft Windows.</p>
</div>

### Установка Node.js

Node.js можно установить с сайта [][]<http://nodejs.org/#download></a>. Если пакет установки не доступен для вашей операционной системы, можно собрать Node.js их исходного кода.

## <span id="CreateServer"></span></a>Практическое руководство. Создание базы данных SQL Azure

Выполните следующие действия для создания базы данных SQL Azure.

1.  Войдите на [портал управления][портала управления Azure].
2.  Щелкните значок **+ Создать** в нижнем левом углу портала.

    ![Создание нового веб-сайта Azure][Создание нового веб-сайта Azure]

3.  Щелкните **База данных SQL**, затем **Настраиваемое создание**.

    ![Настраиваемое создание новой базы данных SQL][Настраиваемое создание новой базы данных SQL]

4.  Введите значение **ИМЯ** для базы данных, выберите **ВЫПУСК** (WEB или BUSINESS), выберите для базы данных **МАКС. РАЗМЕР**, выберите значение параметра **СОРТИРОВКА**, а затем команду **СОЗДАТЬ сервер базы данных SQL**. Щелкните стрелку в нижней части диалогового окна. (Обратите внимание, что если база данных SQL была создана ранее, можно выбрать существующий сервер в раскрывающемся списке **Выберите сервер**.)

    ![Заполните параметры базы данных SQL][Заполните параметры базы данных SQL]

5.  Введите имя и пароль администратора (и подтвердите пароль), выберите регион, где будет создана новая база данных SQL, и установите флажок `Allow Azure Services to access the server`.

    ![Создать новый сервер базы данных SQL][Создать новый сервер базы данных SQL]

Чтобы просмотреть сведения о сервере и базе данных, щелкните **Базы данных SQL** на портале управления Azure. Затем для просмотра соответствующих сведений можно выбрать **БАЗЫ ДАННЫХ** или **СЕРВЕРЫ**.

![Просмотр сведений о сервере и базе данных][Просмотр сведений о сервере и базе данных]

## <span id="ConnectionInfo"></span></a>Практическое руководство. Получение сведений о подключении к базе данных SQL

Чтобы получить сведения о подключении к базе данных, щелкните **БАЗЫ ДАННЫХ SQL** на портале, затем щелкните имя базы данных.

![Просмотр сведений о базе данных][Просмотр сведений о базе данных]

Затем щелкните **Просмотреть строки подключения**

![Показать строки подключения][Показать строки подключения]

В разделе ODBC появившегося окна обратите внимание на значения строки подключения. Это строка подключения, которая будет использоваться при подключении к базе данных SQL из приложения узла. Ваш пароль – это пароль, который вы использовали при создании базы данных SQL.

## <span id="Connect"></span></a>Практическое руководство. Подключение к экземпляру базы данных SQL

### Установка node-sqlserver

Драйвер Microsoft для Node.JS для SQL Server доступен как собственный модуль node-sqlserver. Двоичная версия этого модуля доступна в [центре загрузки][центре загрузки]. Чтобы использовать двоичную версию, выполните следующие действия:

1.  Извлеките двоичный архив в каталог **node\_modules** вашего приложения.
2.  Запустите файл **node-sqlserver-install.cmd**, извлеченный из архива. Это приведет к созданию подкаталога **node-sqlserver** в **node\_modules** и к перемещению файлов драйвера в эту новую структуру каталогов.
3.  Удалите файл **node-sqlserver-install.cmd**, он больше не нужен.

<div class="dev-callout">
<b>Примечание.</b>
<p>Можно также установить модуль node-sqlserver с помощью служебной программы npm; однако это вызовет node-gyp для создания двоичной версии модуля в системе.</p>
</div>

### Укажите строку подключения

Чтобы использовать модуль node-sqlserver, необходимо указать это в своем приложении и указать строку подключения. Строка подключение должна быть ODBC-значением, полученным в разделе этой статьи [Руководство. Получение сведений о подключении к базе данных SQL][Практическое руководство. Получение сведений о подключении к базе данных SQL]. Код должен выглядеть аналогично следующему:

    var sql = require('node-sqlserver');
    var conn_str = "Driver={SQL Server Native Client 10.0};Server=tcp:{dbservername}.database.windows.net,1433;Database={database};Uid={username};Pwd={password};Encrypt=yes;Connection Timeout=30;";

### Запрос к базе данных

Запросы можно выполнить, указав инструкцию Transact-SQL с методом **query**. При просмотре веб-страницы следующий код создает HTTP-сервер и возвращает данные из строк **ID**, **Column1** и **Column2** таблицы **Test**:

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

## <span id="Deploy"></span></a>Рекомендации по развертыванию в Azure

<div class="dev-callout">
<b>Примечание.</b>
<p>Следующая информация основана на работе с предварительной версией драйвера Microsoft для Node.JS для SQL Server. Сведения в этом разделе о модуле node-sqlserver в Azure могут оказаться не самыми последними. Свежую информацию о <a href="https://github.com/WindowsAzure/node-sqlserver">драйвере Microsoft для Node.JS для SQL Server</a> см. на его странице на Github.</p>
</div>

Azure не установит модуль node-sqlserver динамически во время выполнения, поэтому необходимо убедиться, что в развертывание вашего приложения включена двоичная версия модуля. Вы можете проверить, что развертывание содержит двоичную версию модуля, убедившись, что существует следующая структура каталогов, в которой содержатся приведенные ниже файлы:

    application directory
        node_modules
            node-sqlserver
                lib

Каталог **node-sqlserver** должен содержать файл **package.json**. Каталог **lib** должен содержать файлы **sql.js** и **sqlserver.node**, которые представляют собой компилированную форму модуля node-sqlserver.

Дополнительные сведения о развертывании приложения Node.js в Azure см. в разделах [Создание и развертывание приложения Node.js на веб-сайте Azure][Создание и развертывание приложения Node.js на веб-сайте Azure] и [Облачная служба Node.js][Облачная служба Node.js].

## <span id="NextSteps"></span></a>Дальнейшие действия

-   [Введение в драйвер Microsoft для Node.JS для SQL Server][Введение в драйвер Microsoft для Node.JS для SQL Server]
-   [Драйвер Microsoft для Node.JS для SQL Server на Github.com][странице Github]

  [портала управления Azure]: https://manage.windowsazure.com
  [Основные понятия]: #Concepts
  [Практическое руководство. Настройка среды разработки]: #Setup
  [Практическое руководство. Создание базы данных SQL Azure]: #CreateServer
  [Практическое руководство. Получение сведений о подключении к базе данных SQL]: #ConnectionInfo
  [Практическое руководство. Подключение к экземпляру базы данных SQL]: #Connect
  [Рекомендации по развертыванию в Azure]: #Deploy
  [Дальнейшие действия]: #NextSteps
  [странице Github]: https://github.com/WindowsAzure/node-sqlserver
  [Вики]: https://github.com/WindowsAzure/node-sqlserver/wiki
  [пакета дополнительных компонентов Microsoft SQL Server 2012]: http://www.microsoft.com/ru-ru/download/details.aspx?id=29065
  []: http://nodejs.org/#download
  [Создание нового веб-сайта Azure]: ./media/sql-database-nodejs-how-to-use/new_website.jpg
  [Настраиваемое создание новой базы данных SQL]: ./media/sql-database-nodejs-how-to-use/create_custom_sql_db.jpg
  [Заполните параметры базы данных SQL]: ./media/sql-database-nodejs-how-to-use/new-sql-db.png
  [Создать новый сервер базы данных SQL]: ./media/sql-database-nodejs-how-to-use/db-server-settings.png
  [Просмотр сведений о сервере и базе данных]: ./media/sql-database-nodejs-how-to-use/sql-dbs-portal.png
  [Просмотр сведений о базе данных]: ./media/sql-database-nodejs-how-to-use/go-to-db-info.png
  [Показать строки подключения]: ./media/sql-database-nodejs-how-to-use/show-connection-string.png
  [центре загрузки]: http://www.microsoft.com/ru-ru/download/details.aspx?id=29995
  [Создание и развертывание приложения Node.js на веб-сайте Azure]: /ru-ru/develop/nodejs/tutorials/create-a-website-(mac)/
  [Облачная служба Node.js]: /ru-ru/develop/nodejs/tutorials/getting-started/
  [Введение в драйвер Microsoft для Node.JS для SQL Server]: http://blogs.msdn.com/b/sqlphp/archive/2012/06/08/introducing-the-microsoft-driver-for-node-js-for-sql-server.aspx

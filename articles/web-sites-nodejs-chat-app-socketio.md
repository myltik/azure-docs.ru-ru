<properties 
	pageTitle="Создание приложения для разговоров на Node.js с использованием Socket.IO в службе приложений Azure" 
	description="В этом учебнике рассматривается использование Socket.io в веб-приложениях Node.js, размещенных в Azure." 
	services="app-service\web" 
	documentationCenter="nodejs" 
	authors="MikeWasson" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="nodejs" 
	ms.topic="article" 
	ms.date="03/24/2015" 
	ms.author="mwasson"/>




# Создание приложения для разговоров на Node.js с использованием Socket.IO в службе приложений Azure

Socket.IO обеспечивает связь в режиме реального времени между сервером Node.js и клиентами с помощью WebSockets. Он также поддерживает переход к другим протоколам (например, длинному опросу), работающим со старыми браузерами. В этом учебнике детально описано размещение приложения для разговоров на основе Socket.IO в качестве веб-приложения Azure и показано, как выполнять [масштабирование](#scale-out) приложения с помощью [кэша Redis для Azure](http://azure.microsoft.com/documentation/services/cache). Дополнительные сведения о Socket.IO см. на веб-сайте [http://socket.io/][socketio].

> [AZURE.NOTE] Процедуры в этом задании применяются для [веб-сайтов службы приложений](http://go.microsoft.com/fwlink/?LinkId=529714). Информацию об облачных службах см. в статье <a href="http://www.windowsazure.com/develop/nodejs/tutorials/app-using-socketio/">Создание приложения для разговора на Node.js с использованием Socket.IO в облачной службе Azure</a>.


## Скачивание примера разговора

Для этого проекта будет использоваться пример чата из [Репозитория GitHub
Socket.IO]. Выполните следующие действия, чтобы скачать пример
и добавить его в проект, созданный ранее.

1.  Скачайте выпуск проекта Socket.IO [в архиве ZIP или ][GZ] (для этого документа использовалась версия 1.3.5)


3.  Распакуйте архив и скопируйте каталог **examples\\chat** в новое место, например **\\node\\chat**.

## Изменение app.js и установка модулей

1.  Переименуйте файл **index.js** в **app.js**. Благодаря этому Azure сможет распознать, что это приложение Node.js.

1.  Откройте файл **app.js** в текстовом редакторе. Измените строку с текстом "var io = require('../..')(server);", как показано ниже:

		var express = require('express');
		var app = express();
		var server = require('http').createServer(app);
		// var io = require('../..')(server);
        // New:
		var io = require('socket.io')(server);
		var port = process.env.PORT || 3000;


3. Откройте **package.json** и добавьте ссылку на socket.io в `dependencies`:

        "dependencies": {
		  "express": "3.4.8",
		  "socket.io": "1.3.5"
		}

4. В командной строке измените каталоги на каталог **\\node\\chat**, а затем выполните команду "npm", чтобы установить модули, необходимые для этого приложения:

        npm install

    Будет выполнена установка модулей в подпапку с именем **node_modules**.

## Создание веб-приложения Azure

Выполните следующие действия, чтобы создать веб-приложение Azure, включить публикацию Git, а затем включить поддержку WebSocket для этого веб-приложения.

> [AZURE.NOTE] Для работы с этим учебником требуется учетная запись Azure. Если ее нет, можно создать бесплатную пробную учетную запись всего за несколько минут. Дополнительные сведения см. в разделе <a href="http://www.windowsazure.com/pricing/free-trial/?WT.mc_id=A7171371E" target="_blank">Бесплатная пробная версия Azure</a>.

1. Установите кроссплатформенный интерфейс командной строки Azure (xplat-cli) и подключитесь к своей подписке Azure. См. статью [Установка и настройка кроссплатформенного интерфейса командной строки Azure](xplat-cli).

2. Если вы настраиваете репозиторий в Azure впервые, потребуется создать для него учетную запись. В xplat-cli введите команду:

		azure site deployment user set [username] [password] 


3. Измените каталог на **\\node\chat** и выполните следующую команду, чтобы создать новое веб-приложение Azure и локальный репозиторий Git. Эта команда также создает удаленный Git с именем 'azure'.

		azure site create mysitename --git

	Замените 'mysitename' уникальным именем созданного веб-приложения.

2. Поместите существующие файлы в локальный репозиторий посредством следующих команд:

		git add .
		git commit -m "Initial commit"

3. Передайте файлы в репозиторий службы приложений Azure с помощью следующей команды:

		git push azure master

	Вы будете получать сообщения о статусе по мере импорта модулей на сервере. После завершения этого процесса приложение будет размещаться в вашем веб-приложении Azure.

 	> [AZURE.NOTE] Во время установки модуля могут появляться сообщения об ошибках: 'Импортированный проект ... не найден'. Эти сообщения можно спокойно проигнорировать.

4. В Socket.IO используются сокеты WebSockets, которые по умолчанию не включены в Azure. Для включения веб-сокетов воспользуйтесь следующей командой:

		azure site set -w

	При появлении запроса введите имя веб-приложения.

	>[AZURE.NOTE]
	>Команда 'azure site set -w' будет работать только в кроссплатформенном интерфейсе командной строки Azure версии 0.7.4 или более поздней. Вы также можете включить поддержку WebSocket с помощью [портала Azure](https://portal.azure.com).
	>
	>Чтобы включить WebSockets с помощью портала Azure, щелкните веб-приложение в колонке веб-приложений и откройте **Все параметры** > **Параметры приложения**. В разделе **Веб-сокеты** щелкните **Вкл.**. Нажмите кнопку **Сохранить**.
	
5. Для просмотра веб-сайта на платформе Azure выполните следующую команду, чтобы запустить веб-браузер и перейти к размещенному веб-приложению:

		azure site browse

Теперь ваше приложение работает на платформе Azure и может передавать сообщения разговора между различными клиентами с использованием Socket.IO.

##Масштабирование

Приложения Socket.IO можно масштабировать при использовании __адаптера__ для распределения сообщений и событий между несколькими экземплярами приложения. Хотя доступно несколько адаптеров, адаптер [socket.io-redis](https://github.com/automattic/socket.io-redis) можно легко использовать с функцией кэша Redis для Azure.

> [AZURE.NOTE] Дополнительным требованием к масштабированию решений Socket.IO является поддержка прикрепленных сеансов. Прикрепленные сеансы включены для веб-приложений Azure по умолчанию через маршрутизацию запросов Azure. Дополнительную информацию см. в разделе [Территориальные группы экземпляров в веб-сайтах Azure](http://azure.microsoft.com/blog/2013/11/18/disabling-arrs-instance-affinity-in-windows-azure-web-sites/).

###Создание кэша Redis

Выполните шаги, приведенные в разделе [Создание кэша Azure Redis](http://go.microsoft.com/fwlink/p/?linkid=398592&clcid=0x409), чтобы создать новый кэш.

> [AZURE.NOTE] Сохраните значения __Имя узла__ и __Первичный ключ__ кэша, так как они понадобятся позднее.

###Добавление модулей redis и socket.io-redis

1. В командной строке перейдите в каталог __\\node\\chat__ и выполните следующую команду:

		npm install socket.io-redis@0.1.4 redis@0.12.1 --save

	> [AZURE.NOTE] Версии, указанные в этой команде, использовались при тестировании данной процедуры.

2. Измените файл __app.js__, добавив следующие строки сразу после строки "var io = require('socket.io')(server);"

		var pub = require('redis').createClient(6379,'redishostname', {auth_pass: 'rediskey', return_buffers: true});
		var sub = require('redis').createClient(6379,'redishostname', {auth_pass: 'rediskey', return_buffers: true});
		
		var redis = require('socket.io-redis');
		io.adapter(redis({pubClient: pub, subClient: sub}));

	Замените __redishostname__ и __rediskey__ именем узла и ключом кэша Redis.

	Будет создан клиент публикации и подписки для ранее созданного кэша Redis. С помощью клиентов и адаптера будет настроен Socket.IO для использования кэша Redis, что обеспечит передачу сообщений и событий между экземплярами вашего приложения.

	> [AZURE.NOTE] Хотя адаптер __socket.io-redis__ может взаимодействовать непосредственно с Redis, текущая версия не поддерживает проверку подлинности, требуемую кэшем Redis для Azure. Поэтому первоначальное соединение создается с помощью модуля __redis__, а затем клиент передается адаптеру __socket.io-redis__.
	> 
	> Хотя кэш Azure Redis поддерживает безопасные подключения через порт 6380, модули, использованные в данном примере, по состоянию на 14.7.2014 не поддерживают безопасные подключения. В приведенном выше коде используется небезопасный порт по умолчанию - 6380.

3. Сохраните измененный файл __app.js__

###Подтверждение изменений и повторное развертывание

В командной строке в каталоге __\\node\\chat__ выполните следующую команду для подтверждения изменений и повторного развертывания приложения.

	git add .
	git commit -m "implementing scale out"
	git push azure master

Как только изменения будут отправлены на сервер, вы сможете масштабировать сайт между несколькими экземплярами следующей командой:

	Масштабированные экземпляры веб-сайтов Azure --instances #

где __#__ - количество экземпляров, которое требуется создать. 

Вы можете подключиться к своему веб-приложению из разных браузеров и с разных компьютеров, чтобы убедиться, что сообщения должным образом отправляются всем клиентам.

## Устранение неполадок

###Ограничения на подключения

Веб-приложения Azure доступны в нескольких SKU, которые определяют ресурсы, доступные для вашего сайта. Сюда входит количество разрешенных соединений WebSocket. Дополнительные сведения см. на странице [Цены на ][веб-приложения].

###Не отправляются сообщения через WebSockets

Если браузеры клиентов по-прежнему переходят к длинному опросу и не используют WebSockets, возможны следующие причины.

* **Попробуйте ограничить протоколы, оставив только WebSockets**

	Чтобы Socket.IO использовал в качестве протокола сообщений WebSockets, как сервер, так и клиент должны поддерживать WebSockets. Если тот или другой его не поддерживает, Socket.IO согласует другой протокол, например длинный опрос. Список протоколов, используемых Socket.IO по умолчанию: "websocket, htmlfile, xhr-polling, jsonp-polling". Вы можете выбрать принудительное использование только протокола WebSockets, добавив следующий код в файл **app.js** после строки с текстом ", nicknames = {};".

		io.configure(function() {
		  io.set('transports', ['websocket']);
		});

	> [AZURE.NOTE] Обратите внимание, что старые браузеры, не поддерживающие WebSockets, не смогут подключиться к сайту, если вышеуказанный код будет активен, так как он ограничивает обмен данными протоколом WebSockets.

* **Используйте SSL**

	WebSockets использует некоторые малоиспользуемые заголовки HTTP, например **Upgrade**. Некоторые промежуточные сетевые устройства, например прокси-серверы, могут удалять эти заголовки. Чтобы избежать этого, можно установить подключение WebSocket через SSL.

	Простой способ сделать это - настроить для Socket.IO протокол `match origin protocol`. При этом Socket.IO будет защищать соединения WebSockets тем же образом, что и исходные запросы HTTP/HTTPS к веб-странице. Если браузер использует для посещения сайта URL-адрес с протоколом HTTPS, последующий обмен данными WebSocket через Socket.IO будет защищен с помощью SSL.

	Чтобы включить такую конфигурацию в этом примере, добавьте следующий код в файл **app.js** после строки с текстом ", nicknames = {};".

		io.configure(function() {
		  io.set('match origin protocol', true);
		});

* **Проверьте настройки web.config**

	Веб-приложения Azure с приложениями Node.js используют файл **web.config** для маршрутизации входящих запросов к приложению Node.js. Чтобы WebSockets работал правильно с приложениями Node.js, в файле **web.config** должна быть следующая запись:

		<webSocket enabled="false"/>

	Это позволяет отключить модуль WebSocket служб IIS, который содержит собственную реализацию модулей WebSocket и конфликтует с модулями WebSocket, используемыми специально для Node.js, например с Socket.IO. Если эта строка отсутствует или для нее задано значение `true`, возможно, из-за этого в приложении не будет работать транспорт WebSocket.

	Обычно приложения Node.js не включают файл **web.config**, поэтому веб-сайты Azure автоматически будут создавать его для приложений Node.js при их развертывании. Поскольку этот файл автоматически создается на сервере, следует использовать для его просмотра URL-адрес с протоколами FTP и FTPS. URL-адреса с протоколами FTP и FTPS для сайта можно найти на портале управления Azure: выберите веб-сайт, а затем ссылку **Панель мониторинга**. URL-адреса будут выведены в разделе **Сводка**.

	> [AZURE.NOTE] Файл **web.config** создается веб-сайтами Azure, только если его нет в самом приложении. Если в корне проекта приложения есть файл **web.config**, веб-приложения Azure будут использовать его.

	Если запись отсутствует или для нее установлено значение `true`, следует создать файл **web.config** в корне приложения Node.js и задать значение `false`.  Для справки: ниже представлен файл **web.config** по умолчанию для приложения, использующего **app.js** в качестве точки входа.

		<?xml version="1.0" encoding="utf-8"?>
		<!--
		     This configuration file is required if iisnode is used to run node processes behind
		     IIS or IIS Express.  For more information, visit:
		
		     https://github.com/tjanczuk/iisnode/blob/master/src/samples/configuration/web.config
		-->
		
		<configuration>
		  <system.webServer>
		    <!-- Visit http://blogs.msdn.com/b/windowsazure/archive/2013/11/14/introduction-to-websockets-on-windows-azure-web-sites.aspx for more information on WebSocket support -->
		    <webSocket enabled="false" />
		    <handlers>
		      <!-- Indicates that the server.js file is a node.js web app to be handled by the iisnode module -->
		      <add name="iisnode" path="app.js" verb="*" modules="iisnode"/>
		    </handlers>
		    <rewrite>
		      <rules>
		        <!-- Do not interfere with requests for node-inspector debugging -->
		        <rule name="NodeInspector" patternSyntax="ECMAScript" stopProcessing="true">
		          <match url="^app.js\/debug[\/]?" />
		        </rule>
		
		        <!-- First we consider whether the incoming URL matches a physical file in the /public folder -->
		        <rule name="StaticContent">
		          <action type="Rewrite" url="public{REQUEST_URI}"/>
		        </rule>
		
		        <!-- All other URLs are mapped to the node.js web app entry point -->
		        <rule name="DynamicContent">
		          <conditions>
		            <add input="{REQUEST_FILENAME}" matchType="IsFile" negate="True"/>
		          </conditions>
		          <action type="Rewrite" url="app.js"/>
		        </rule>
		      </rules>
		    </rewrite>
		    <!--
		      Вы можете управлять размещением узла в службах IIS, используя следующие параметры:
		        * watchedFiles - список разделенных точкой с запятой файлов, в которых будут отслеживаться изменения для перезапуска сервера;
		        * node_env - будет передан на узел в качестве переменной среды NODE_ENV;
		        * debuggingEnabled - определяет, включен ли встроенный отладчик.
		
		      Полный список параметров см. на странице https://github.com/tjanczuk/iisnode/blob/master/src/samples/configuration/web.config
		    -->
		    <!--<iisnode watchedFiles="web.config;*.js"/>-->
		  </system.webServer>
		</configuration>

	> [AZURE.NOTE] Если приложение использует не **app.js**, а другую точку входа, необходимо заменить все повторяющиеся **app.js** правильными точками входа. Например, заменить **app.js** на **server.js**.

>[AZURE.NOTE] Если вы хотите начать работу со службой приложений Azure еще до создания учетной записи Azure, перейдите на страницу [ознакомительной версии службы приложений](http://go.microsoft.com/fwlink/?LinkId=523751). Там вы сможете сразу создать свое первое веб-приложение, правда срок его службы будет ограничен. Никаких кредитных карт и обязательств.

##Дальнейшие действия

В данном учебнике рассмотрено создание приложения для разговоров, размещенного в веб-приложении Azure. Это приложение также можно разместить в качестве облачной службы Azure. Соответствующие инструкции см. в статье [Создание приложения для разговоров на Node.js с использованием Socket.IO в облачной службе Azure][cloudservice].

## Изменения
* Сведения об изменении веб-сайтов на службу приложений см. в статье [Служба приложений Azure и ее влияние на существующие службы Azure](http://go.microsoft.com/fwlink/?LinkId=529714)
* Сведения о переходе со старого портала на новый см. в статье [Справочная информация о навигации по предварительной версии портала](http://go.microsoft.com/fwlink/?LinkId=529715).

[socketio]: http://socket.io/
[completed-app]: ./media/web-sites-nodejs-chat-app-socketio/websitesocketcomplete.png
[Socket.IO GitHub repository]: https://github.com/Automattic/socket.io
[release]: https://github.com/Automattic/socket.io/releases
[cloudservice]: /develop/nodejs/tutorials/app-using-socketio/

[chat-example-view]: ./media/web-sites-nodejs-chat-app-socketio/socketio-2.png
[npm-output]: ./media/web-sites-nodejs-chat-app-socketio/socketio-7.png
[pricing]: /pricing/details/web-sites/

<!--HONumber=49-->
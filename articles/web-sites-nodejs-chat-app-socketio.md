<properties 
	pageTitle="Веб-сайт Node.js с помощью Socket.io - Учебник Azure" 
	description="Учебник, в котором демонстрируется использование Socket.io на веб-сайте Node.js, размещенном в Azure." 
	services="web-sites" 
	documentationCenter="nodejs" 
	authors="blackmist" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="nodejs" 
	ms.topic="article" 
	ms.date="09/17/2014" 
	ms.author="larryfr"/>




#Создание приложения для разговора Node.js с Socket.IO на веб-сайте Azure

Socket.IO обеспечивает связь в режиме реального времени между сервером Node.js и клиентами с помощью WebSockets. Он также поддерживает переход к другим протоколам (например, длинному опросу), работающим со старыми браузерами. Этот учебный курс поможет выполнить размещение приложения разговора на основе Socket.IO в качестве веб-сайта Azure. Дополнительные сведения о Socket.IO см. на сайте [http://socket.io/][socketio].

> [AZURE.NOTE] Процедуры в этом задании применяются для веб-сайтов Azure. Информацию по облачным службам см. в разделе <a href="http://azure.microsoft.com/develop/nodejs/tutorials/app-using-socketio/">Cоздание приложения для разговора на Node.js с использованием Socket.IO в облачной службе Azure</a>.


## <a id="Download"></a>Скачивание примера разговора

Для этого проекта будет использоваться пример чата из [Репозитория GitHub
Socket.IO]. Чтобы скачать пример, сделайте следующее.
и добавьте его в проект, созданный ранее.

1.  Скачайте [выпуск в архиве ZIP или GZ][выпуск] проекта Socket.IO (для этого документа использовалась версия 1.0.6)


3.  Извлеките архив и скопируйте каталог **examples\\chat**
    в новое место. Например: 
    **\\node\\chat**.

## <a id="Modify"></a>Изменение App.js и установка модулей

Перед развертыванием приложения в Azure необходимо
внести некоторые незначительные изменения.

1.  Переименуйте файл **index.js** в **app.js**. Благодаря этому Azure сможет распознать, что это приложение Node.js.

1.  Откройте файл **app.js** в Блокноте или другом текстовом редакторе.

2.  Найдите раздел **Module dependencies** в начале файла app.js и измените строку с текстом `var io = require('../..')(server);` на `var io = require('socket.io')(server);`, как показано ниже.

		var express = require('express');
		var app = express();
		var server = require('http').createServer(app);
		// var io = require('../..')(server);
		var io = require('socket.io')(server);
		var port = process.env.PORT || 3000;


Cохранив изменения в app.js, выполните следующие действия для
установки необходимых модулей::

1.  В командной строке измените каталоги на каталог **\\node\\chat**, а затем выполните следующую команду, чтобы установить модули, необходимые для этого приложения:

        npm install

    Будут установлены модули, указанные в файле package.json. После
    завершения команды будет выведен результат,
    подобный этому:

	    express@3.4.8 node_modules\express
		├── methods@0.1.0
		├── merge-descriptors@0.0.1
		├── debug@0.8.1
		├── cookie-signature@1.0.1
		├── range-parser@0.0.4
		├── fresh@0.2.0
		├── buffer-crc32@0.2.1
		├── cookie@0.1.0
		├── mkdirp@0.3.5
		├── commander@1.3.2 (keypress@0.1.0)
		├── send@0.1.4 (mime@1.2.11)
		└── connect@2.12.0 (uid2@0.0.3, pause@0.0.1, qs@0.6.6, bytes@0.2.1, raw-body@1.1.2, batch@0.5.0, negotiator@0.3.0, multiparty@2.2.0)

2.  Поскольку этот пример изначально был частью Socket.IO в репозитории GitHub
    и напрямую ссылался на библиотеку Socket.IO по
    относительному пути, Socket.IO не указывалась в файле package.json,
    поэтому необходимо его установить, выполнив следующую команду:

        npm install socket.io@1.0.6 -save

	> [AZURE.NOTE] Хотя более новые версии Socket.IO и могут работать согласно действиям, приведенным в этой статье, помните, что процедура проверялась на версии 1.0.6.

## <a id="Publish"></a>Создание веб-сайта Azure

Выполните следующие действия, чтобы создать веб-сайт Windows Azure, включить публикацию Git, а затем включить поддержку WebSocket для этого веб-сайта.

> [AZURE.NOTE] Для работы с этим учебником требуется учетная запись Azure. Если ее нет, можно создать бесплатную пробную учетную запись всего за несколько минут. Дополнительные сведения см. в разделе <a href="http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A7171371E" target="_blank">Бесплатная пробная версия Azure</a>.

1. В командной строке измените каталоги на каталог **\\node\chat** и при помощи следующей команды создайте новый веб-сайт Azure, а затем включите Git-репозиторий для этого веб-сайта и локального каталога. Будет также создан удаленный Git-репозиторий с именем 'azure'.

		azure site create mysitename --git

	Замените 'mysitename' уникальным именем созданного веб-сайта.

2. Поместите существующие файлы в локальный репозиторий посредством следующих команд:

		git add .
		git commit -m "Initial commit"

3. Передайте файлы в репозиторий веб-сайта Azure посредством следующей команды:

		git push azure master

	Вы будете получать сообщения о статусе по мере импорта модулей на сервере. По завершении этого процесса приложение будет размещаться на вашем веб-сайте Azure.

 	> [AZURE.NOTE] Во время установки модуля могут возникать такие ошибки, как 'The imported project ... was not found'. Эти сообщения можно спокойно проигнорировать.

4. В Socket.IO используются сокеты WebSockets, которые по умолчанию не включены в Azure. Для включения веб-сокетов воспользуйтесь следующей командой:

		azure site set -w

	При выводе запроса введите имя веб-сайта.

	>[AZURE.NOTE]
	>Команда 'azure site set -w' действует только в межплатформенном интерфейсе командной строки Azure версии 0.7.4 или более поздней. Можно также включить поддержку WebSocket с помощью портала управления Azure.
	>
	>Чтобы включить WebSockets с помощью [портала управления Azure](https://manage.windowsazure.com), перейдите на страницу настройки веб-сайта, выберите "ВКЛ" для записи "Подключения Web Sockets", затем нажмите кнопку "Сохранить".
	>	
	>![websockets](./media/web-sites-nodejs-chat-app-socketio/websockets.png)
	
5. Для просмотра веб-сайта на платформе Azure выполните следующую команду, чтобы запустить веб-браузер и перейти к размещенному веб-сайту:

		azure site browse

Приложение теперь выполняется в Azure и может передавать сообщения разговора.
чата между разными клиентами, используя Socket.IO.

> [AZURE.NOTE] Чтобы упростить процесс, в этом примере мы ограничили разговор пользователями, подключенными к одному экземпляру. Это означает, что если облачная служба создает два экземпляра рабочей роли, пользователи смогут общаться только с другими пользователями, подключенными к тому же экземпляру рабочей роли. Чтобы масштабировать приложение для работы с несколькими экземплярами роли, можно использовать технологию Service Bus, которая позволяет передавать состояние хранилища Socket.IO между несколькими экземплярами. Примеры использования см. в разделах "Очереди служебной шины" и "Тома служебной шины" в <a href="https://github.com/WindowsAzure/azure-sdk-for-node">репозитории GitHub Azure SDK для Node.js</a>.

##Масштабирование

Приложения Socket.IO могут быть масштабированы при использовании __адаптера__ для распределения сообщений и событий между несколькими экземплярами приложения. Хотя доступно несколько адаптеров, адаптер [socket.io-redis](https://github.com/automattic/socket.io-redis) можно легко использовать с функцией кэша Azure Redis Cache.

> [AZURE.NOTE] Дополнительным требованием к масштабированию решений Socket.IO является поддержка прикрепленных сеансов. Прикрепленные сеансы включены для веб-сайтов Azure по умолчанию через маршрутизацию запросов Azure. Дополнительную информацию см. в разделе [Территориальные группы экземпляров в веб-сайтах Azure](http://azure.microsoft.com/blog/2013/11/18/disabling-arrs-instance-affinity-in-windows-azure-web-sites/).

###Создание кэша Redis

Выполните шаги, приведенные в разделе [Создание кэша Azure Redis](http://go.microsoft.com/fwlink/p/?linkid=398592&clcid=0x409), чтобы создать новый кэш.

> [AZURE.NOTE] Сохраните значения __Host name__ и __Primary key__ кэша: они понадобятся позднее.

###Добавление модулей redis и socket.io-redis

1. В командной строке перейдите в каталог __\\node\\chat__ и выполните следующую команду:

		npm install socket.io-redis@0.1.3 redis@0.11.0 --save

	> [AZURE.NOTE] Версии, указанные в этой команде, использовались при тестировании данной процедуры.

2. Отредактируйте файл __app.js__, добавив следующий текст сразу после `var io = require('socket.io')(server);`

		var pub = require('redis').createClient(6379,'redishostname', {auth_pass: 'rediskey', return_buffers: true});
		var sub = require('redis').createClient(6379,'redishostname', {auth_pass: 'rediskey', return_buffers: true});
		
		var redis = require('socket.io-redis');
		io.adapter(redis({pubClient: pub, subClient: sub}));


	Замените __redishostname__ и __rediskey__ именем узла и ключом кэша Redis.

	Будет создан клиент публикации и подписки для ранее созданного кэша Redis. С помощью клиентов и адаптера будет настроен Socket.IO для использования кэша Redis, что обеспечит передачу сообщений и событий между экземплярами вашего приложения.

	> [AZURE.NOTE] Хотя адаптер __socket.io-redis__ может взаимодействовать непосредственно с Redis, текущая версия (по состоянию на 14.7.2014) не поддерживает аутентификацию, требуемую кэшем Azure Redis. Поэтому первоначальное соединение создается с помощью модуля __redis__, а затем клиент передается адаптеру __socket.io-redis__.
	> 
	> Хотя кэш Azure Redis поддерживает безопасные подключения через порт 6380, модули, использованные в данном примере, по состоянию на 14.7.2014 не поддерживают безопасные подключения. В приведенном выше коде используется небезопасный порт по умолчанию - 6380.

3. Сохраните изменения в __app.js__.

###Подтверждение изменений и повторное развертывание

В командной строке в каталоге __\\node\\chat__ выполните следующую команду для подтверждения изменений и повторного развертывания приложения.

	git add .
	git commit -m "implementing scale out"
	git push azure master

Как только изменения будут отправлены на сервер, вы сможете масштабировать сайт между несколькими экземплярами следующей командой:

	Масштабированные экземпляры веб-сайтов Azure --instances #

где __#__  - количество экземпляров, которое требуется создать. 

Вы можете подключиться к своему веб-сайту с разных браузеров и компьютеров и убедиться, что сообщения должным образом отправляются всем клиентам.

##<a id="tshooting"></a>Устранение неполадок

###Ограничения на подключения

Веб-сайты Azure доступны в нескольких SKU, что означает ресурсы, доступные для вашего сайта. Сюда входит количество разрешенных соединений WebSocket. Дополнительную информацию см. в разделе [Цены на веб-сайты][pricing].

###Не отправляются сообщения через WebSockets

Если браузеры клиентов по-прежнему переходят к длинному опросу и не используют WebSockets, возможны следующие причины.

* **Попробуйте ограничить протоколы, оставив только WebSockets**

	Чтобы Socket.IO использовал в качестве протокола сообщений WebSockets, как сервер, так и клиент должны поддерживать WebSockets. Если тот или другой его не поддерживает, Socket.IO согласует другой протокол, например длинный опрос. Список протоколов по умолчанию, используемых Socket.IO: ` websocket, htmlfile, xhr-polling, jsonp-polling`. Вы можете выбрать принудительное использование только протокола WebSockets, добавив следующий код в файл **app.js** после строки с текстом `, nicknames = {};`..

		io.configure(function() {
		  io.set('transports', ['websocket']);
		});

	> [AZURE.NOTE] Обратите внимание, что старые браузеры, не поддерживающие WebSockets, не смогут подключиться к сайту, если вышеуказанный код будет активен, так как он ограничивает обмен данными протоколом WebSockets.

* **Используйте SSL**

	WebSockets использует некоторые малоиспользуемые заголовки HTTP, например **Upgrade**. Некоторые промежуточные сетевые устройства, например прокси-серверы, могут удалять эти заголовки. Чтобы избежать этого, можно установить подключение WebSocket через SSL.

	Простой способ сделать это - настроить для Socket.IO протокол `match origin protocol`. При этом Socket.IO будет защищать соединения WebSockets тем же образом, что и исходные запросы HTTP/HTTPS к веб-странице. Если браузер использует для посещения сайта URL-адрес с протоколом HTTPS, последующий обмен данными WebSocket через Socket.IO будет защищен с помощью SSL.

	Чтобы включить такую конфигурацию в данном примере, добавьте следующий код в файл **app.js** после строки с текстом `, nicknames = {};`.

		io.configure(function() {
		  io.set('match origin protocol', true);
		});

* **Проверьте настройки web.config**

	Веб-сайты Azure с приложениями Node.js используют файл **web.config** для маршрутизации входящих запросов к приложению Node.js. Чтобы WebSockets работал правильно с приложениями Node.js, в файле **web.config** должна быть следующая запись:

		<webSocket enabled="false"/>

	Это позволяет отключить модуль WebSocket служб IIS, который содержит собственную реализацию модулей WebSocket и конфликтует с модулями WebSocket, используемыми специально для Node.js, например с Socket.IO. Если эта строка отсутствует или для нее задано значение `true`, возможно, из-за этого в приложении не будет работать транспорт WebSocket.

	Обычно приложения Node.js не включают файл **web.config**, поэтому веб-сайты Azure автоматически будут создавать его для приложений Node.js при их развертывании. Поскольку этот файл автоматически создается на сервере, следует использовать для его просмотра URL-адрес с протоколами FTP и FTPS. URL-адреса с протоколами FTP и FTPS для сайта можно найти на портале управления Azure: выберите веб-сайт, а затем ссылку **Панель мониторинга**. URL-адреса будут выведены в разделе **Сводка**.

	> [AZURE.NOTE] Файл **web.config** создается веб-сайтами Azure, только если его нет в самом приложении. Если в корне проекта приложения имеется файл **web.config**, веб-сайты Azure будут использовать его.

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
		      <!-- Indicates that the server.js file is a node.js site to be handled by the iisnode module -->
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
		
		        <!-- All other URLs are mapped to the node.js site entry point -->
		        <rule name="DynamicContent">
		          <conditions>
		            <add input="{REQUEST_FILENAME}" matchType="IsFile" negate="True"/>
		          </conditions>
		          <action type="Rewrite" url="app.js"/>
		        </rule>
		      </rules>
		    </rewrite>
		    <!--
		      You can control how Node is hosted within IIS using the following options:
		        * watchedFiles: semi-colon separated list of files that will be watched for changes to restart the server
		        * node_env: will be propagated to node as NODE_ENV environment variable
		        * debuggingEnabled - controls whether the built-in debugger is enabled
		
		      See https://github.com/tjanczuk/iisnode/blob/master/src/samples/configuration/web.config for a full list of options
		    -->
		    <!--<iisnode watchedFiles="web.config;*.js"/>-->
		  </system.webServer>
		</configuration>

	> [AZURE.NOTE] Если приложение использует не **app.js**, а другую точку входа, необходимо заменить все повторяющиеся **app.js** правильными точками входа. Например, заменить **app.js** на **server.js**.

##Дальнейшие действия

В данном учебном курсе было рассмотрено создание приложения для чата, размещенного на веб-сайте Azure. Это приложение также можно разместить в качестве облачной службы Azure. Указания о том, как это сделать, см. в разделе [Cоздание приложения для разговора на Node.js с использованием Socket.IO в облачной службе Azure][cloudservice].

[socketio]: http://socket.io/
[completed-app]: ./media/web-sites-nodejs-chat-app-socketio/websitesocketcomplete.png
[Socket.IO GitHub repository]: https://github.com/Automattic/socket.io
[release]: https://github.com/Automattic/socket.io/releases
[cloudservice]: /ru-ru/develop/nodejs/tutorials/app-using-socketio/
	
[chat-example-view]: ./media/web-sites-nodejs-chat-app-socketio/socketio-2.png
[npm-output]: ./media/web-sites-nodejs-chat-app-socketio/socketio-7.png
[pricing]: /ru-ru/pricing/details/web-sites/

<!--HONumber=42-->

<properties urlDisplayName="Website Using Socket.IO" pageTitle="Веб-сайт Node.js с использованием Socket.io - учебный курс Azure" metaKeywords="учебник Azure Node.js socket.io, Azure Node.js socket.io, учебник Azure Node.js" description="A tutorial that demonstrates using socket.io in a node.js website hosted on Azure." metaCanonical="" services="web-sites" documentationCenter="nodejs" title="Build a Node.js Chat Application with Socket.IO on an Azure Website" authors="larryfr" solutions="" videoId="" scriptId="" manager="wpickett" editor="mollybos" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="nodejs" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr" />




#Создание приложения для разговора Node.js с Socket.IO на веб-сайте Azure

Socket.IO обеспечивает связь в режиме реального времени между сервером Node.js и клиентами с помощью WebSockets. Он также поддерживает переход к другим протоколам (например, длинному опросу), работающим со старыми браузерами. Этот учебный курс поможет выполнить размещение приложения разговора на основе Socket.IO в качестве веб-сайта Azure. Дополнительные сведения о Socket.IO см. на веб-сайте [http://socket.io/][socketio].

> [WACOM.NOTE]Процедуры в этом задании применяются для веб-сайтов Azure; сведения по облачным службам см. в разделе <a href="http://www.windowsazure.com/ru-ru/develop/nodejs/tutorials/app-using-socketio/">Cоздание приложения для чата на Node.js с использованием Socket.IO в облачной службе Azure</a>.


## <a id="Download"></a>Загрузка примера разговора

Для этого проекта будет использоваться пример чата из [Socket.IO
Репозиторий GitHub]. Для загрузки примера выполните следующие действия
и добавьте его в проект, созданный ранее.

1.  Загрузите [выпуск в архиве ZIP или GZ][выпуск] проекта Socket.IO (для этого документа использовалась версия 1.0.6)


3.  Извлеките архив и скопируйте каталог **examples\\chat**
    в новое место. Например, 
    **\\node\\chat**.

## <a id="Modify"></a>Изменение App.js и установка модулей

Перед развертыванием приложения в Azure необходимо
внести некоторые незначительные изменения.

1.  Переименуйте файл **index.js** в **app.js**. Благодаря этому Azure сможет распознать, что это приложение Node.js.

1.  Откройте файл **app.js** в Блокноте или другом текстовом редакторе.

2.  Найдите раздел **Module dependencies** в начале файла app.js и измените строку `var io = require('../..')(server);` на `var io = require('socket.io')(server);` как показано ниже:

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

    Будет выполнена установка модулей, перечисленных в файле package.json. После
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

	> [WACOM.NOTE] Хотя более новые версии Socket.IO и могут работать согласно действиям, приведенным в этой статье, помните, что процедура проверялась на версии 1.0.6.

## <a id="Publish"></a>Создание веб-сайта Azure

Выполните следующие действия, чтобы создать веб-сайт Windows Azure, включить публикацию Git, а затем включить поддержку WebSocket для этого веб-сайта.

> [WACOM.NOTE]Для работы с этим учебником требуется учетная запись Azure. Если ее нет, можно создать бесплатную пробную учетную запись всего за несколько минут. Дополнительные сведения см. в разделе <a href="http://www.windowsazure.com/ru-ru/pricing/free-trial/?WT.mc_id=A7171371E" target="_blank">Azure Free Trial</a>.

1. From the command-line, change directories to the **\\node\chat** directory and use the following command to create a new Azure Website and enable a Git repository for the website and the local directory. This will also create a Git remote named 'azure'.

		azure site create mysitename --git

	You must replace 'mysitename' with a unique name for your website.

2. Commit the existing files to the local repository by using the following commands:

		git add .
		git commit -m "Initial commit"

3. Push the files to the Azure Website repository with the following command:

		git push azure master

	You will receive status messages as modules are imported on the server. Once this process has completed, the application will be hosted on your Azure Website.

 	> [WACOM.NOTE] During module installation, you may notice errors that 'The imported project ... was not found'. These can safely be ignored.

4. Socket.IO uses WebSockets, which are not enabled by default on Azure. To enable web sockets, use the following command:

		azure site set -w

	If prompted, enter the name of the website.

	>[WACOM.NOTE]
	>The 'azure site set -w' command will only work with version 0.7.4 or higher of the Azure Cross-Platform Command-Line Interface. You can also enable WebSocket support using the Azure Management Portal.
	>
	>To enable WebSockets using the [Azure Management Portal](https://manage.windowsazure.com), select the Configure page for your website, select 'ON' for the Web Sockets entry, and then click Save.
	>	
	>![websockets](./media/web-sites-nodejs-chat-app-socketio/websockets.png)
	
5. To view the website on Azure, use the following command to launch your web browser and navigate to the hosted website:

		azure site browse

Your application is now running on Azure, and can relay chat
messages between different clients using Socket.IO.

> [WACOM.NOTE] For simplicity, this sample is limited to chatting between users connected to the same instance. This means that if the cloud service creates two worker role instances, users will only be able to chat with others connected to the same worker role instance. To scale the application to work with multiple role instances, you could use a technology like Service Bus to share the Socket.IO store state across instances. For examples, see the Service Bus Queues and Topics usage samples in the <a href="https://github.com/WindowsAzure/azure-sdk-for-node">Azure SDK for Node.js GitHub repository</a>.

##Scale out

Socket.IO applications can be scaled out by using an __adapter__ to distribute messages and events between multiple application instances. While there are several adapters available, the [socket.io-redis](https://github.com/automattic/socket.io-redis) adapter can be easily used with the Azure Redis Cache feature.

> [WACOM.NOTE] An additional requirement for scaling out a Socket.IO solution is support for sticky sessions. Sticky sessions are enabled by default for Azure Websites through Azure Request Routing. For more information, see [Instance Affinity in Azure Web Sites](http://azure.microsoft.com/blog/2013/11/18/disabling-arrs-instance-affinity-in-windows-azure-web-sites/)

###Create a Redis cache

Perform the steps in [Create a cache in Azure Redis Cache](http://go.microsoft.com/fwlink/p/?linkid=398592&clcid=0x409) to create a new cache.

> [WACOM.NOTE] Save the __Host name__ and __Primary key__ for your cache, as these will be needed in the next steps.

###Add the redis and socket.io-redis modules

1. From a command-line, change to the __\\node\\chat__ directory and use the following command.

		npm install socket.io-redis@0.1.3 redis@0.11.0 --save

	> [WACOM.NOTE] The versions specified in this command are the versions used when testing this article.

2. Modify the __app.js__ file to add the following lines immediately after `var io = require('socket.io')(server);`

		var pub = require('redis').createClient(6379,'redishostname', {auth_pass: 'rediskey', return_buffers: true});
		var sub = require('redis').createClient(6379,'redishostname', {auth_pass: 'rediskey', return_buffers: true});
		
		var redis = require('socket.io-redis');
		io.adapter(redis({pubClient: pub, subClient: sub}));


	Replace __redishostname__ and __rediskey__ with the host name and key for your Redis cache.

	This will create a publish and subscribe client to the Redis cache created previously. The clients are then used with the adapter to configure Socket.IO to use the Redis cache for passing messages and events between instances of your application

	> [WACOM.NOTE] While the __socket.io-redis__ adapter can communicate directly to Redis, the current version (as of 7/14/2014) does not support the authentication required by Azure Redis cache. So the initial connection is created using the __redis__ module, then the client is passed to the __socket.io-redis__ adapter.
	> 
	> While Azure Redis Cache supports secure connections using port 6380, the modules used in this example do not support secure connections as of 7/14/2014. The above code uses the default, unsecure port of 6380.

3. Save the modified __app.js__

###Commit changes and redeploy

From the command-line in the __\\node\\chat__ directory, use the following commands to commit changes and redeploy the application.

	git add .
	git commit -m "implementing scale out"
	git push azure master

Once the changes have been pushed to the server, you can scale your site across multiple instances by using the following command.

	azure site scale instances --instances #

Where __#__ is the number of instances to create. 

You can connect to your website from multiple browsers or computers to verify that messages are correctly sent to all clients.

##<a id="tshooting"></a>Troubleshooting

###Connection limits

Azure Websites is available in multiple SKUs, which determine the resources available to your site. This includes the number of allowed WebSocket connections. For more information, see the [Web Sites Pricing page][pricing].

###Messages aren't being sent using WebSockets

If client browsers keep falling back to long polling instead of using WebSockets, it may be because of one of the following.

* **Try limiting the transport to just WebSockets**

	In order for Socket.IO to use WebSockets as the messaging transport, both the server and client must support WebSockets. If one or the other does not, Socket.IO will negotiate another transport, such as long polling. The default list of transports used by Socket.IO is ` websocket, htmlfile, xhr-polling, jsonp-polling`. You can force it to only use WebSockets by adding the following code to the **app.js** file, after the line containing `, nicknames = {};`.

		io.configure(function() {
		  io.set('transports', ['websocket']);
		});

	> [WACOM.NOTE] Note that older browsers that do not support WebSockets will not be able to connect to the site while the above code is active, as it restricts communication to WebSockets only.

* **Use SSL**

	WebSockets relies on some lesser used HTTP headers, such as the **Upgrade** header. Some intermediate network devices, such as web proxies, may remove these headers. To avoid this problem, you can establish the WebSocket connection over SSL.

	An easy way to accomplish this is to configure Socket.IO to `match origin protocol`. This instructs Socket.IO to secure WebSockets communication the same as the originating HTTP/HTTPS request for the web page. If a browser uses an HTTPS URL to visit your website, subsequent WebSocket communications through Socket.IO will be secured over SSL.

	To modify this example to enable this configuration, add the following code to the **app.js** file after the line containing `, nicknames = {};`.

		io.configure(function() {
		  io.set('match origin protocol', true);
		});

* **Verify web.config settings**

	Azure Websites that host Node.js applications use the **web.config** file to route incoming requests to the Node.js application. For WebSockets to function correctly with Node.js applications, the **web.config** must contain the following entry.

		<webSocket enabled="false"/>

	Это отключает модуль IIS WebSockets, включающий собственную реализацию WebSockets и конфликтующий со специфичными для Node.js модулями WebSocket, такими как Socket.IO. Если эта строка отсутствует или для нее установлено значение `true`, это может быть причиной некорректной работы протокола WebSocket для вашего приложения.

	Обычно приложения Node.js не включают файл **web.config**, поэтому веб-сайты Azure автоматически будут создавать его для приложений Node.js при их развертывании. Поскольку этот файл автоматически создается на сервере, следует использовать для его просмотра URL-адрес с протоколами FTP и FTPS. URL-адреса с протоколами FTP и FTPS для сайта можно найти на портале управления Azure: выберите веб-сайт, а затем ссылку **Панель мониторинга**. URL-адреса будут выведены в разделе **Сводка**.

	> [WACOM.NOTE] Файл **web.config** создается веб-сайтами Azure, только если его нет в самом приложении. Если в корне проекта приложения имеется файл **web.config**, веб-сайты Azure будут использовать его.

	Если запись отсутствует или для нее установлено значение `true`, следует создать файл **web.config** в корне приложения Node.js и задать значение `false`.  Для справки: ниже представлен файл **web.config** по умолчанию для приложения, использующего в качестве точки входа **app.js**.

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

	> [WACOM.NOTE] Если приложение использует точку входа, отличную от **app.js**, следует заменить все вхождения **app.js** соответствующей точкой входа. Например, заменить **app.js** на **server.js**.

##Дальнейшие действия

В данном учебном курсе было рассмотрено создание приложения для чата, размещенного на веб-сайте Azure. Это приложение также можно разместить в качестве облачной службы Azure. Инструкции о том, как это сделать, см. в разделе [Cоздание приложения для чата на Node.js с использованием Socket.IO в облачной службе Azure][cloudservice].

[socketio]: http://socket.io/
[completed-app]: ./media/web-sites-nodejs-chat-app-socketio/websitesocketcomplete.png
[Репозиторий Socket.IO в GitHub]: https://github.com/Automattic/socket.io
[release]: https://github.com/Automattic/socket.io/releases
[cloudservice]: /ru-ru/develop/nodejs/tutorials/app-using-socketio/

[chat-example-view]: ./media/web-sites-nodejs-chat-app-socketio/socketio-2.png
[npm-output]: ./media/web-sites-nodejs-chat-app-socketio/socketio-7.png
[pricing]: /ru-ru/pricing/details/web-sites/

---
title: "Создание приложения для разговоров на Node.js с использованием Socket.IO в службе приложений Azure"
description: "В этом учебнике рассматривается использование Socket.io в веб-приложениях Node.js, размещенных в Azure."
services: app-service\web
documentationcenter: nodejs
author: rmcmurray
manager: erikre
editor: 
ms.assetid: c4c4af36-3ecf-4619-b586-ca90d53ce35b
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 12/22/2016
ms.author: robmcm
translationtype: Human Translation
ms.sourcegitcommit: b1a633a86bd1b5997d5cbf66b16ec351f1043901
ms.openlocfilehash: e3de500e3dc64abbbe7a5983db0ad14a0a9b76c8


---
# <a name="create-a-nodejs-chat-application-with-socketio-in-azure-app-service"></a>Создание приложения для разговоров на Node.js с использованием Socket.IO в службе приложений Azure
Socket.IO обеспечивает связь в режиме реального времени между сервером Node.js и клиентами с помощью WebSockets. Он также поддерживает переход к другим протоколам (например, длинному опросу), работающим со старыми браузерами. В этом руководстве детально описано размещение приложения чата на основе Socket.IO в качестве веб-приложения Azure и показано, как выполнять масштабирование с помощью [кэша Redis для Azure]. Дополнительные сведения о Socket.IO см. на веб-сайте <http://socket.io/>.

> [!NOTE]
> Процедуры в этой задаче применяются к [веб-приложениям службы приложений]. Информацию об облачных службах см. в статье [Создание приложения для разговора Node.js с Socket.IO в облачной службе Azure].
> 
> 

## <a name="download-the-chat-example"></a>Скачивание примера разговора
Для этого проекта мы будем использовать пример разговора из [репозитория GitHub Socket.IO]. Выполните следующие действия, чтобы скачать пример и добавить его в ранее созданный проект.

1. Загрузите выпуск проекта Socket.IO [в архиве ZIP или GZ] (в данном документе использована версия 1.3.5).
2. Извлеките содержимое архива и скопируйте каталог **examples\\chat** в новое расположение. Например, в **\\node\\chat**.

## <a name="modify-appjs-and-install-modules"></a>Изменение app.js и установка модулей
1. Переименуйте файл **index.js** в **app.js**. Благодаря этому Azure сможет распознать, что это приложение Node.js.
2. Откройте файл **app.js** в текстовом редакторе. Измените строку, содержащую `var io = require('../..')(server);` , как показано ниже.
   
       var express = require('express');
       var app = express();
       var server = require('http').createServer(app);
       // var io = require('../..')(server);
       // New:
       var io = require('socket.io')(server);
       var port = process.env.PORT || 3000;
3. Откройте файл **package.json** и добавьте ссылку на socket.io в `dependencies`, как показано ниже.
   
        "dependencies": {
          "express": "3.4.8",
          "socket.io": "1.3.5"
        }
4. В командной строке перейдите в каталог **\\node\\chat**, а затем выполните команду npm, чтобы установить модули, необходимые для этого приложения.
   
        npm install
   
    Будет выполнена установка модулей во вложенную папку **node_modules**.

## <a name="create-an-azure-web-app"></a>Создание веб-приложения Azure
Выполните следующие действия, чтобы создать веб-приложение Azure, включить публикацию Git, а затем включить поддержку WebSocket для этого веб-приложения.

> [!NOTE]
> Для работы с этим учебником требуется учетная запись Azure. Если ее нет, можно создать бесплатную пробную учетную запись всего за несколько минут. Дополнительные сведения см. в разделе <a href="http://www.windowsazure.com/pricing/free-trial/?WT.mc_id=A7171371E" target="_blank">Бесплатная пробная версия Azure</a>;
> 
> 

1. Установите интерфейс командной строки Azure (Azure CLI) и подключитесь к своей подписке Azure. Ознакомьтесь со статьей [Установка и настройка интерфейса командной строки Azure](../xplat-cli-install.md).
2. Если вы настраиваете репозиторий в Azure впервые, потребуется создать для него учетные данные для входа. В интерфейсе командной строки Azure введите команду:
   
        azure site deployment user set [username] [password]
3. Перейдите в каталог **\\node\chat** и выполните следующую команду, чтобы создать новое веб-приложение Azure и локальный репозиторий Git. Эта команда также создает удаленный Git с именем azure.
   
        azure site create mysitename --git
   
    Замените mysitename уникальным именем созданного веб-приложения.
4. Поместите существующие файлы в локальный репозиторий посредством следующих команд:
   
        git add .
        git commit -m "Initial commit"
5. Передайте файлы в репозиторий службы веб-приложений Azure с помощью следующей команды:
   
        git push azure master
   
    При появлении запроса введите свои учетные данные из шага 2. Вы будете получать сообщения о статусе по мере импорта модулей на сервере. После завершения этого процесса приложение будет размещаться в вашем веб-приложении Azure.
   
   > [!NOTE]
   > Во время установки модуля могут возникнуть ошибки "Импортированный проект ... не найден". Эти сообщения можно спокойно проигнорировать.
   > 
   > 
6. В Socket.IO используются сокеты WebSockets, которые по умолчанию не включены в Azure. Для включения веб-сокетов воспользуйтесь следующей командой:
   
        azure site set -w
   
    При появлении запроса введите имя веб-приложения.
   
   > [!NOTE]
   > Команда azure site set -w действует только в интерфейсе командной строки Azure версии 0.7.4 или более поздней версии. Вы также можете включить поддержку WebSocket с помощью [портала Azure](https://portal.azure.com).
   > 
   > Чтобы включить WebSockets с помощью портала Azure, щелкните веб-приложение в колонке "Веб-приложения" и откройте **Все параметры** > **Параметры приложения**. В разделе **Веб-сокеты** щелкните **Включить**. Нажмите кнопку **Сохранить**.
   > 
   > 
7. Для просмотра веб-сайта на платформе Azure выполните следующую команду, чтобы запустить веб-браузер и перейти к размещенному веб-приложению:
   
        azure site browse

Теперь ваше приложение работает на платформе Azure и может передавать сообщения разговора между различными клиентами с использованием Socket.IO.

## <a name="scale-out"></a>Масштабирование
Приложения Socket.IO можно масштабировать с помощью **адаптера**, чтобы распределить сообщения и события между несколькими экземплярами приложения. Хотя доступно несколько адаптеров, адаптер [socket.io-redis] можно легко использовать с функцией кэша Azure Redis Cache.

> [!NOTE]
> Дополнительным требованием к масштабированию решений Socket.IO является поддержка прикрепленных сеансов. Прикрепленные сеансы включены для веб-приложений Azure по умолчанию через маршрутизацию запросов Azure. Дополнительные сведения см. в разделе [Сходство экземпляров на веб-сайтах Azure].
> 
> 

### <a name="create-a-redis-cache"></a>Создание кэша Redis
Выполните шаги, приведенные в разделе [Создание кэша Azure Redis] , чтобы создать новый кэш.

> [!NOTE]
> Сохраните значения **Имя узла** и **Первичный ключ** кэша, так как они понадобятся позднее.
> 
> 

### <a name="add-the-redis-and-socketio-redis-modules"></a>Добавление модулей redis и socket.io-redis
1. В командной строке перейдите в каталог **\\\node\\chat** и выполните следующую команду.
   
        npm install socket.io-redis@0.1.4 redis@0.12.1 --save
   
   > [!NOTE]
   > Версии, указанные в этой команде, использовались при тестировании данной процедуры.
   > 
   > 
2. Измените файл **app.js**, добавив приведенные ниже строки сразу после `var io = require('socket.io')(server);`.
   
        var pub = require('redis').createClient(6379,'redishostname', {auth_pass: 'rediskey', return_buffers: true});
        var sub = require('redis').createClient(6379,'redishostname', {auth_pass: 'rediskey', return_buffers: true});
   
        var redis = require('socket.io-redis');
        io.adapter(redis({pubClient: pub, subClient: sub}));
   
    Замените **redishostname** и **rediskey** именем узла и ключом кэша Redis.
   
    Будет создан клиент публикации и подписки для ранее созданного кэша Redis. С помощью клиентов и адаптера будет настроен Socket.IO для использования кэша Redis, что обеспечит передачу сообщений и событий между экземплярами вашего приложения.
   
   > [!NOTE]
   > Хотя адаптер **socket.io-redis** может взаимодействовать непосредственно с Redis, текущая версия не поддерживает аутентификацию, требуемую кэшем Redis для Azure. Поэтому первоначальное подключение создается с помощью модуля **redis**, а затем клиент передается адаптеру **socket.io-redis**.
   > 
   > Хотя кэш Azure Redis поддерживает безопасные подключения через порт 6380, модули, использованные в данном примере, по состоянию на 14.7.2014 не поддерживают безопасные подключения. В приведенном выше коде используется небезопасный порт по умолчанию — 6379.
   > 
   > 
3. Сохраните измененный файл **app.js**.

### <a name="commit-changes-and-redeploy"></a>Подтверждение изменений и повторное развертывание
В командной строке в каталоге **\\node\\chat** выполните следующую команду для подтверждения изменений и повторного развертывания приложения.

    git add .
    git commit -m "implementing scale out"
    git push azure master

Как только изменения будут отправлены на сервер, вы сможете масштабировать сайт между несколькими экземплярами следующей командой:

    azure site scale instances --instances #

где **#** — количество экземпляров, которое требуется создать.

Вы можете подключиться к своему веб-приложению из разных браузеров и с разных компьютеров, чтобы убедиться, что сообщения должным образом отправляются всем клиентам.

## <a name="troubleshooting"></a>Устранение неполадок
### <a name="connection-limits"></a>Ограничения на подключения
Веб-приложения Azure доступны в нескольких номерах SKU, которые определяются ресурсами, доступными для вашего сайта. Сюда входит количество разрешенных соединений WebSocket. Дополнительные сведения см. на странице [цен на веб-приложения].

### <a name="messages-arent-being-sent-using-websockets"></a>Не отправляются сообщения через WebSockets
Если браузеры клиентов по-прежнему переходят к длинному опросу и не используют WebSockets, возможны следующие причины.

* **Попробуйте ограничить протоколы, оставив только WebSockets**
  
    Чтобы Socket.IO использовал в качестве протокола сообщений WebSockets, как сервер, так и клиент должны поддерживать WebSockets. Если тот или другой его не поддерживает, Socket.IO согласует другой протокол, например длинный опрос. Список протоколов по умолчанию, используемых Socket.IO: ` websocket, htmlfile, xhr-polling, jsonp-polling`. Вы можете указать принудительное использование только протокола WebSockets, добавив следующий код в файл **app.js** после строки с `, nicknames = {};`.
  
        io.configure(function() {
          io.set('transports', ['websocket']);
        });
  
  > [!NOTE]
  > Обратите внимание, что старые браузеры, не поддерживающие WebSockets, не смогут подключиться к сайту, если вышеуказанный код будет активен, так как он ограничивает обмен данными протоколом WebSockets.
  > 
  > 
* **Использование SSL**
  
    WebSockets использует некоторые малоиспользуемые заголовки HTTP, например **Upgrade** . Некоторые промежуточные сетевые устройства, например прокси-серверы, могут удалять эти заголовки. Чтобы избежать этого, можно установить подключение WebSocket через SSL.
  
    Простой способ сделать это — настроить для Socket.IO протокол `match origin protocol`. При этом Socket.IO будет защищать соединения WebSockets тем же образом, что и исходные запросы HTTP/HTTPS к веб-странице. Если браузер использует для посещения сайта URL-адрес с протоколом HTTPS, последующий обмен данными WebSocket через Socket.IO будет защищен с помощью SSL.
  
    Чтобы включить такую конфигурацию в данном примере, добавьте приведенный ниже код в файл **app.js** после строки с `, nicknames = {};`.
  
        io.configure(function() {
          io.set('match origin protocol', true);
        });
* **Проверка настроек web.config**
  
    Веб-приложения Azure с приложениями Node.js используют файл **web.config** для маршрутизации входящих запросов к приложению Node.js. Чтобы WebSockets работал правильно с приложениями Node.js, в файле **web.config** должна быть следующая запись:
  
        <webSocket enabled="false"/>
  
    Это позволяет отключить модуль WebSocket служб IIS, который содержит собственную реализацию модулей WebSocket и конфликтует с модулями WebSocket, используемыми специально для Node.js, например с Socket.IO. Если эта строка отсутствует или для нее задано значение `true`, возможно, из-за этого в приложении не будет работать транспорт WebSocket.
  
    Обычно приложения Node.js не включают файл **web.config** , поэтому веб-сайты Azure автоматически будут создавать его для приложений Node.js при их развертывании. Поскольку этот файл автоматически создается на сервере, следует использовать для его просмотра URL-адрес с протоколами FTP и FTPS. URL-адреса с протоколами FTP и FTPS для вашего сайта можно найти на классическом портале Azure: выберите свое веб-приложение, а затем ссылку **Панель мониторинга** . URL-адреса будут выведены в разделе **Сводка** .
  
  > [!NOTE]
  > Файл **web.config** создается веб-сайтами Azure, только если его нет в самом приложении. Если в корне проекта приложения есть файл **web.config** , веб-приложения Azure будут использовать его.
  > 
  > 
  
    Если запись отсутствует или для нее установлено значение `true`, следует создать файл **web.config** в корне приложения Node.js и задать значение `false`.  Для справки ниже представлен файл **web.config** по умолчанию для приложения, использующего **app.js** в качестве точки входа.
  
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
              You can control how Node is hosted within IIS using the following options:
                * watchedFiles: semi-colon separated list of files that will be watched for changes to restart the server
                * node_env: will be propagated to node as NODE_ENV environment variable
                * debuggingEnabled - controls whether the built-in debugger is enabled
  
              See https://github.com/tjanczuk/iisnode/blob/master/src/samples/configuration/web.config for a full list of options
            -->
            <!--<iisnode watchedFiles="web.config;*.js"/>-->
          </system.webServer>
        </configuration>
  
    Если приложение использует точку входа, отличную от **app.js**, то следует заменить все вхождения **app.js** соответствующей точкой входа. Например, заменить **app.js** на **server.js**.

> [!NOTE]
> Чтобы приступить к работе со службой приложений Azure до создания учетной записи Azure, перейдите к разделу [Пробное использование службы приложений], где вы можете быстро создать кратковременное веб-приложение начального уровня в службе приложений. Никаких кредитных карт и обязательств.
> 
> 

## <a name="next-steps"></a>Дальнейшие действия
В данном учебнике рассмотрено создание приложения для разговоров, размещенного в веб-приложении Azure. Это приложение также можно разместить в качестве облачной службы Azure. Инструкции о том, как это сделать, см. в разделе [Создание приложения для разговора Node.js с Socket.IO в облачной службе Azure].

Дополнительные сведения см. также в [центре по разработке для Node.js].

## <a name="whats-changed"></a>Изменения
* Руководство по переходу от веб-сайтов к службе приложений представлено в статье [Служба приложений Azure и существующие службы Azure].

<!-- URL List -->

[кэша Redis для Azure]: /documentation/services/redis-cache/
[веб-приложениям службы приложений]: http://go.microsoft.com/fwlink/?LinkId=529714
[цен на веб-приложения]: http://go.microsoft.com/fwlink/?LinkId=511643
[Создание приложения для разговора Node.js с Socket.IO в облачной службе Azure]: ../cloud-services/cloud-services-nodejs-chat-app-socketio.md
[Install and Configure the Azure CLI]: ../xplat-cli-install.md
[Служба приложений Azure и существующие службы Azure]: http://go.microsoft.com/fwlink/?LinkId=529714
[центре по разработке для Node.js]: /develop/nodejs/
[Пробное использование службы приложений]: https://azure.microsoft.com/try/app-service/
[Сходство экземпляров на веб-сайтах Azure]: https://azure.microsoft.com/blog/2013/11/18/disabling-arrs-instance-affinity-in-windows-azure-web-sites/
[Создание кэша Azure Redis]: ../redis-cache/cache-dotnet-how-to-use-azure-redis-cache.md

[socket.io-redis]: https://github.com/socketio/socket.io-redis
[репозитория GitHub Socket.IO]: https://github.com/socketio/socket.io
[в архиве ZIP или GZ]: https://github.com/socketio/socket.io/releases

<!-- IMG List -->

[chat-example-view]: ./media/web-sites-nodejs-chat-app-socketio/socketio-2.png
[npm-output]: ./media/web-sites-nodejs-chat-app-socketio/socketio-7.png
[completed-app]: ./media/web-sites-nodejs-chat-app-socketio/websitesocketcomplete.png



<!--HONumber=Feb17_HO3-->



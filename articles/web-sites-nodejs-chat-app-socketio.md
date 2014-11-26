<properties linkid="dev-nodejs-website-with-socketio" urlDisplayName="Website Using Socket.IO" pageTitle="Node.js Website using Socket.io - Azure tutorial" metaKeywords="Azure Node.js socket.io tutorial, Azure Node.js socket.io, Azure Node.js tutorial" description="A tutorial that demonstrates using socket.io in a node.js website hosted on Azure." metaCanonical="" services="web-sites" documentationCenter="nodejs" title="Build a Node.js Chat Application with Socket.IO on an Azure Website" authors="larryfr" solutions="" videoId="" scriptId="" manager="paulettm" editor="mollybos" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="nodejs" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr" />

# Создание приложения для разговора Node.js с Socket.IO на веб-сайте Azure

Socket.IO обеспечивает связь в режиме реального времени между сервером Node.js и клиентами с помощью WebSockets. Он также поддерживает переход к другим протоколам (например, длинному опросу), работающим со старыми браузерами. Этот учебный курс поможет выполнить размещение приложения разговора на основе Socket.IO в качестве веб-сайта Azure. Дополнительные сведения о Socket.IO см. на веб-сайте [][]<http://socket.io/></a>.

> [WACOM.NOTE] Процедуры в этом задании применяются для веб-сайтов Azure; сведения по облачным службам см. в разделе [Cоздание приложения разговора Node.js с Socket.IO в облачной службе Azure][Cоздание приложения разговора Node.js с Socket.IO в облачной службе Azure].

## <span id="Download"></span></a> Загрузка примера разговора

Для этого проекта будет использоваться пример разговора из [репозитория Socket.IO
в GitHub][репозитория Socket.IO
в GitHub]. Чтобы загрузить пример и добавить его в созданный ранее проект,
выполните перечисленные ниже действия.

1.  Загрузите выпуск проекта Socket.IO [в архиве ZIP или GZ][в архиве ZIP или GZ] (в данном документе использована версия 1.0.6).

2.  Распакуйте архив и скопируйте каталог **examples\\chat**
    в новое местоположение, например
    **\\node\\chat**.

## <span id="Modify"></span></a>Изменение App.js и установка модулей

Перед развертыванием приложения в Azure необходимо
внести несколько незначительных изменений.

1.  Переименуйте файл **index.js** в **app.js**. Благодаря этому Azure сможет распознать, что это приложение Node.js.

2.  Откройте файл **app.js** в Блокноте или другом текстовом редакторе.

3.  Найдите раздел **Module dependencies** в начале файла app.js и измените строку с текстом `var io = require('../..')(server);` на `var io = require('socket.io')(server);`, как показано ниже.

        var express = require('express');
        var app = express();
        var server = require('http').createServer(app);
        // var io = require('../..')(server);
        var io = require('socket.io')(server);
        var port = process.env.PORT || 3000;

После сохранения изменений app.js выполните следующие действия,
чтобы установить необходимые модули.

1.  В командной строке измените каталоги на каталог **\\node\\chat**, а затем выполните следующую команду, чтобы установить модули, необходимые для этого приложения:

        npm install

    Будет выполнена установка модулей, перечисленных в файле package.json. Результат
    выполнения команды должен иметь примерно следующий
    вид:

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

2.  Так как этот пример изначально был частью репозитория Socket.IO
    в GitHub и на него прямо указывает ссылка библиотеки Socket.IO
    по относительному пути, Socket.IO не указана в файле
    package.json, поэтому необходимо установить ее, выполнив следующую команду:

        npm install socket.io@1.0.6 -save

    > [WACOM.NOTE] Более новые версии Socket.IO могут работать с шагами, приведенными в этом разделе, но испытана процедура была с версией 1.0.6.

## <span id="Publish"></span></a>Создание веб-сайта Azure

Выполните следующие действия, чтобы создать веб-сайт Windows Azure, включить публикацию Git, а затем включить поддержку WebSocket для этого веб-сайта.

> [WACOM.NOTE] Чтобы выполнить работу с этим учебником, необходимо использовать учетную запись Azure. Если ее нет, можно создать бесплатную пробную учетную запись всего за несколько минут. Дополнительные сведения см. в разделе [Бесплатная пробная версия Azure][Бесплатная пробная версия Azure].

1.  В командной строке измените каталоги на каталог **\\node\\chat** и при помощи следующей команды создайте новый веб-сайт Azure, а затем включите Git-репозиторий для этого веб-сайта и локального каталога. Будет также создан удаленный Git-репозиторий с именем "azure".

        azure site create mysitename --git

    Замените mysitename уникальным именем созданного веб-сайта.

2.  Поместите существующие файлы в локальный репозиторий посредством следующих команд:

        git add .
        git commit -m "Initial commit"

3.  Передайте файлы в репозиторий веб-сайта Azure посредством следующей команды:

        git push azure master

    Вы будете получать сообщения о статусе по мере импорта модулей на сервере. По завершении этого процесса приложение будет размещаться на вашем веб-сайте Azure.

    > [WACOM.NOTE] Во время установки модуля могут возникнуть ошибки "Импортированный проект... не найден". Эти сообщения можно спокойно проигнорировать.

4.  В Socket.IO используются сокеты WebSockets, которые по умолчанию не включены в Azure. Для включения веб-сокетов воспользуйтесь следующей командой:

        azure site set -w

    При выводе запроса введите имя веб-сайта.

    > [WACOM.NOTE]
    > Команда "azure site set -w" действует только в межплатформенном интерфейсе командной строки Azure версии 0.7.4 или более поздней. Можно также включить поддержку WebSocket с помощью портала управления Azure.
    >
    > Чтобы включить WebSockets с помощью [портала управления Azure][портала управления Azure], перейдите на страницу настройки веб-сайта, выберите "ВКЛ" для записи "Подключения Web Sockets", затем нажмите кнопку "Сохранить".
    >
    > ![websockets][websockets]

5.  Для просмотра веб-сайта на платформе Azure выполните следующую команду, чтобы запустить веб-браузер и перейти к размещенному веб-сайту:

        azure site browse

Теперь ваше приложение выполняется на платформе Azure и может передавать сообщения
разговора между различными клиентами с использованием Socket.IO.

> [WACOM.NOTE] Для простоты в этом примере разговор ограничен пользователями, подключенными к одному экземпляру. Это означает, что если облачная служба создает два экземпляра рабочей роли, пользователи смогут общаться только с другими пользователями, подключенными к тому же экземпляру рабочей роли. Чтобы масштабировать приложение для работы с несколькими экземплярами роли, можно использовать технологию Service Bus, которая позволяет передавать состояние хранилища Socket.IO между несколькими экземплярами. Примеры см. в примерах использования очередей и разделов Service Bus в [репозитории пакета Azure SDK для Node.js на GitHub][репозитории пакета Azure SDK для Node.js на GitHub].

## Масштабирование

Приложения Socket.IO могут быть масштабированы при использовании **адаптера** для распределения сообщений и событий между несколькими экземплярами приложения. Хотя доступно несколько адаптеров, адаптер [socket.io-redis][socket.io-redis] можно легко использовать с функцией кэша Azure Redis Cache.

> [WACOM.NOTE] Дополнительным требованием к масштабированию решений Socket.IO является поддержка прикрепленных сеансов. Прикрепленные сеансы включены для веб-сайтов Azure по умолчанию через маршрутизацию запросов Azure. Дополнительные сведения см. в разделе [Территориальные группы экземпляров в веб-сайтах Azure][Территориальные группы экземпляров в веб-сайтах Azure].

### Создание кэша Redis

Выполните шаги, приведенные в разделе [Создание кэша Azure Redis][Создание кэша Azure Redis], чтобы создать новый кэш.

> [WACOM.NOTE] Сохраните значения **Имя узла** и **Первичный ключ** кэша: они понадобятся позднее.

### Добавление модулей redis и socket.io-redis

1.  В командной строке перейдите в каталог **\\node\\chat** и выполните следующую команду:

        npm install socket.io-redis@0.1.3 redis@0.11.0 --save

    > [WACOM.NOTE] Версии, указанные в этой команде, использовались при тестировании данной процедуры.

2.  Отредактируйте файл **app.js**, добавив следующий текст сразу после `var io = require('socket.io')(server);`

        var pub = require('redis').createClient(6379,'redishostname', {auth_pass: 'rediskey', return_buffers: true});
        var sub = require('redis').createClient(6379,'redishostname', {auth_pass: 'rediskey', return_buffers: true});

        var redis = require('socket.io-redis');
        io.adapter(redis({pubClient: pub, subClient: sub}));

    Замените **redishostname** и **rediskey** именем узла и ключом кэша Redis.

    Будет создан клиент публикации и подписки для ранее созданного кэша Redis. С помощью клиентов и адаптера будет настроен Socket.IO для использования кэша Redis, что обеспечит передачу сообщений и событий между экземплярами вашего приложения.

    > [WACOM.NOTE] Хотя адаптер **socket.io-redis** может взаимодействовать непосредственно с Redis, текущая версия (по состоянию на 14.7.2014) не поддерживает проверку подлинности, требуемую кэшем Azure Redis. Поэтому первоначальное соединение создается с помощью модуля **redis**, а затем клиент передается адаптеру **socket.io-redis**.
    >
    > Хотя кэш Azure Redis поддерживает безопасные подключения через порт 6380, модули, использованные в данном примере, по состоянию на 14.7.2014 не поддерживают безопасные подключения. В коде выше используется небезопасный порт 6380 по умолчанию.

3.  Сохраните файл **app.js**.

### Подтверждение изменений и повторное развертывание

В командной строке в каталоге **\\node\\chat** выполните следующую команду для подтверждения изменений и повторного развертывания приложения.

    git add .
    git commit -m "implementing scale out"
    git push azure master

Как только изменения будут отправлены на сервер, вы сможете масштабировать сайт между несколькими экземплярами следующей командой:

    azure site scale instances --instances #

где **\#** — количество экземпляров, которое требуется создать.

Вы можете подключиться к своему веб-сайту с разных браузеров и компьютеров и убедиться, что сообщения должным образом отправляются всем клиентам.

## <span id="tshooting"></span></a>Устранение неполадок

### Ограничения на подключения

Веб-сайты Azure доступны в нескольких SKU, что означает ресурсы, доступные для вашего сайта. Сюда входит количество разрешенных соединений WebSocket. Дополнительные сведения см. в разделе [Цены на веб-сайты][Цены на веб-сайты].

### Не отправляются сообщения через WebSockets

Если браузеры клиентов по-прежнему переходят к длинному опросу и не используют WebSockets, возможны следующие причины.

-   **Попробуйте ограничить протоколы, оставив только WebSockets**

    Чтобы Socket.IO использовал в качестве протокола сообщений WebSockets, как сервер, так и клиент должны поддерживать WebSockets. Если тот или другой его не поддерживает, Socket.IO согласует другой протокол, например длинный опрос. Список протоколов по умолчанию, используемых Socket.IO: `websocket, htmlfile, xhr-polling, jsonp-polling`. Вы можете выбрать принудительное использование только протокола WebSockets, добавив следующий код в файл **app.js** после строки с текстом `, nicknames = {};`.

        io.configure(function() {
          io.set('transports', ['websocket']);
        });

    > [WACOM.NOTE] Обратите внимание, что старые браузеры, не поддерживающие WebSockets, не смогут подключиться к сайту, если вышеуказанный код будет активен, так как он ограничивает обмен данными протоколом WebSockets.

-   **Используйте SSL**

    WebSockets использует некоторые малоиспользуемые заголовки HTTP, например **Upgrade**. Некоторые промежуточные сетевые устройства, например прокси-серверы, могут удалять эти заголовки. Чтобы избежать этого, можно установить подключение WebSocket через SSL.

    Простой способ сделать это — настроить для Socket.IO протокол `match origin protocol`. При этом Socket.IO будет защищать соединения WebSockets тем же образом, что и исходные запросы HTTP/HTTPS к веб-странице. Если браузер использует для посещения сайта URL-адрес с протоколом HTTPS, последующий обмен данными WebSocket через Socket.IO будет защищен с помощью SSL.

    Чтобы включить такую конфигурацию в данном примере, добавьте следующий код в файл **app.js** после строки с текстом `, nicknames = {};`.

        io.configure(function() {
          io.set('match origin protocol', true);
        });

-   **Проверьте настройки web.config**

    Веб-сайты Azure с приложениями Node.js используют файл **web.config** для маршрутизации входящих запросов к приложению Node.js. Чтобы WebSockets работал правильно с приложениями Node.js, в файле **web.config** должна быть следующая запись:

        <webSocket enabled="false"/>

    Это отключает модуль IIS WebSockets, включающий собственную реализацию WebSockets и конфликтующий со специфичными для Node.js модулями WebSocket, такими как Socket.IO. Если эта строка отсутствует или для нее установлено значение `true`, это может быть причиной некорректной работы протокола WebSocket для вашего приложения.

    Обычно приложения Node.js не включают файл **web.config**, поэтому веб-сайты Azure автоматически будут создавать его для приложений Node.js при их развертывании. Поскольку этот файл автоматически создается на сервере, следует использовать для его просмотра URL-адрес с протоколами FTP и FTPS. URL-адреса с протоколами FTP и FTPS для сайта можно найти на портале управления Azure: выберите веб-сайт, а затем ссылку **Панель мониторинга**. URL-адреса будут выведены в разделе **Сводка**.

    > [WACOM.NOTE] Файл **web.config** создается веб-сайтами Azure, только если приложение не предоставляет его само. Если в корне проекта приложения имеется файл **web.config**, веб-сайты Azure будут использовать его.

    Если запись отсутствует или для нее установлено значение `true`, следует создать файл **web.config** в корне приложения Node.js и задать значение `false`. Для справки: ниже представлен файл **web.config** по умолчанию для приложения, использующего **app.js** в качестве точки входа.

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

## Дальнейшие действия

В данном учебном курсе было рассмотрено создание приложения разговора, размещенного на веб-сайте в Azure. Это приложение также можно разместить в качестве облачной службы Azure. Инструкции о том, как это сделать, см. в разделе [Создание приложения разговора Node.js с Socket.IO на основе облачной службы Azure][Создание приложения разговора Node.js с Socket.IO на основе облачной службы Azure]

  []: http://socket.io/
  [Cоздание приложения разговора Node.js с Socket.IO в облачной службе Azure]: http://www.windowsazure.com/ru-ru/develop/nodejs/tutorials/app-using-socketio/
  [в архиве ZIP или GZ]: https://github.com/Automattic/socket.io/releases
  [Бесплатная пробная версия Azure]: http://www.windowsazure.com/ru-ru/pricing/free-trial/?WT.mc_id=A7171371E
  [портала управления Azure]: https://manage.windowsazure.com
  [websockets]: ./media/web-sites-nodejs-chat-app-socketio/websockets.png
  [репозитории пакета Azure SDK для Node.js на GitHub]: https://github.com/WindowsAzure/azure-sdk-for-node
  [socket.io-redis]: https://github.com/automattic/socket.io-redis
  [Территориальные группы экземпляров в веб-сайтах Azure]: http://azure.microsoft.com/blog/2013/11/18/disabling-arrs-instance-affinity-in-windows-azure-web-sites/
  [Создание кэша Azure Redis]: http://go.microsoft.com/fwlink/p/?linkid=398592&clcid=0x409
  [Цены на веб-сайты]: /ru-ru/pricing/details/web-sites/
  [Создание приложения разговора Node.js с Socket.IO на основе облачной службы Azure]: /ru-ru/develop/nodejs/tutorials/app-using-socketio/

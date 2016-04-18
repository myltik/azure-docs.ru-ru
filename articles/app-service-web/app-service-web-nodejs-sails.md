<properties
	pageTitle="Развертывание веб-приложения Sails.js в службе приложений Azure"
	description="Сведения о развертывании приложения Node.js в службе приложений Azure. В этом учебнике показано, как развернуть веб-приложение Sails.js."
	services="app-service\web"
	documentationCenter="nodejs"
	authors="cephalin"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="app-service-web"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="nodejs"
	ms.topic="article"
	ms.date="03/31/2016"
	ms.author="cephalin"/>

# Развертывание веб-приложения Sails.js в службе приложений Azure

В этом учебнике показано, как развернуть веб-приложение Sails.js в службе приложений Azure. При работе с учебником вы получите некоторые общие знания о настройке приложения Node.js для запуска в службе приложений.

## Предварительные требования

- Node.js. Двоичные файлы установки доступны [здесь](https://nodejs.org/).
- Sails.js. Инструкции по установке доступны [здесь](http://sailsjs.org/get-started).
- Общие знания о работе Sails.js. Этот учебник не содержит рекомендаций по решению проблем, связанных с управлением Sails.js в целом.
- Git. Двоичные файлы установки доступны [здесь](http://www.git-scm.com/downloads).
- Azure CLI. Инструкции по установке доступны [здесь](../xplat-cli-install.md).
- Учетная запись Microsoft Azure. Если у вас нет учетной записи, можно [подписаться на бесплатную пробную версию](/pricing/free-trial/?WT.mc_id=A261C142F) или [активировать преимущества для подписчиков Visual Studio](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F).

>[AZURE.NOTE] Чтобы увидеть службу приложений Azure в действии до создания учетной записи Azure, перейдите на страницу [пробного использования службы приложений](http://go.microsoft.com/fwlink/?LinkId=523751). Там можно быстро создать кратковременное приложение начального уровня в службе приложений. Для этого не потребуется ни кредитная карта, ни какие-либо обязательства.

## Шаг 1. Создание приложения Sails.js в среде разработки

Сначала создайте приложение Sails.js по умолчанию, выполнив следующие действия:

1. Откройте терминал и `CD` в рабочий каталог.

2. Создайте новое приложение Sails.js и запустите его:

        sails new <appname>
        cd <appname>
        sails lift

    Убедитесь, что домашняя страница по умолчанию http://localhost:1377 открывается.

## Шаг 2. Создание ресурса для приложения службы приложений в Azure

Затем создайте ресурс для приложения службы приложений. Позже вы развернете в него свое приложение.

1. В том же терминале выполните вход в Azure следующим образом:

        azure login

    Следуйте указаниям, чтобы продолжить вход в браузере с помощью учетной записи Майкрософт в рамках вашей подписки Azure.

2. Убедитесь, что вы по-прежнему находитесь в корневом каталоге проекта Sails.js. Создайте ресурс приложения службы приложений Azure с уникальным именем приложения, используя приведенную ниже команду. URL-адрес веб-приложения будет таким: http://&lt;appname>.azurewebsites.net.

        azure site create --git <appname>

    Следуйте указаниям, чтобы выбрать регион Azure для развертывания. Если вы еще не настроили учетные данные развертывания Git/FTP для своей подписки Azure, вам также будет предложено создать их.

    После создания ресурса для приложения службы приложений:

    - приложение Sails.js инициализировано для Git;
    - локальный репозиторий, инициализированный для Git, подключен к новому приложению службы приложений как удаленный ресурс Git с соответствующим именем "azure";
    - файл iisnode.yml создан в корневом каталоге. Этот файл можно применить для настройки узла [iisnode](https://github.com/tjanczuk/iisnode), который служба приложений использует для запуска приложений Node.js.

## Шаг 3. Настройка и развертывание приложения Sails.js

 Работа с приложением Sails.js в службе приложений состоит из трех основных этапов:

 - Настройка приложения для его запуска в службе приложений
 - Развертывание приложения в службу приложений
 - Устранение неполадок развертывания с помощью журналов stderr и stdout

Выполните следующие действия.

1. Откройте новый файл iisnode.yml в корневом каталоге и добавьте в него следующие две строки:

        loggingEnabled: true
        logDirectory: iisnode

    Теперь ведение журнала для iisnode включено. Дополнительные сведения о том, как это работает, см. в разделе [Получение журналов stdout и stderr из iisnode](app-service-web-nodejs-sails.md#iisnodelog).

2. Откройте файл config/env/production.js для настройки рабочей среды и установите `port` и `hookTimeout`:

        module.exports = {

            // Use process.env.port to handle web requests to the default HTTP port
            port: process.env.port,
            // Increase hooks timout to 30 seconds
            // This avoids the Sails.js error documented at https://github.com/balderdashy/sails/issues/2691
            hookTimeout: 30000,

            ...
        };

    Сведения об этих параметрах конфигурации можно найти в [документации Sails.js](http://sailsjs.org/documentation/reference/configuration/sails-config).

    Далее необходимо убедиться, что [Grunt](https://www.npmjs.com/package/grunt) совместим с сетевыми дисками Azure. На момент написания этой статьи Grunt может выдавать сообщение об ошибке ["ENOTSUP: операция не поддерживается для данного сокета"](https://github.com/isaacs/node-glob/issues/205). Это вызвано тем, что сейчас он использует устаревший пакет [glob](https://www.npmjs.com/package/glob) (версии 3.1.21), который не поддерживает сетевые диски. Выполнив следующие действия, можно заставить Grunt использовать пакет [glob версии 5.0.14 или более поздней версии](https://github.com/isaacs/node-glob/commit/bf3381e90e283624fbd652835e1aefa55d45e2c7).

3. Поскольку `npm install` уже запущен при создании приложения, создайте файл npm-shrinkwrap.json в корневом каталоге проекта:

        npm shrinkwrap

4. Откройте файл npm-shrinkwrap.json, найдите код json для `"grunt":` и добавьте зависимость для необходимой версии glob. Результирующий код json должен выглядеть следующим образом:

        "grunt": {
            "version": "0.4.5",
            "from": "grunt@0.4.5",
            "resolved": "https://registry.npmjs.org/grunt/-/grunt-0.4.5.tgz",
            "dependencies": {
                "glob": {
                    "version": "5.0.14",
                    "from": "glob@5.0.14",
                    "resolved": "https://registry.npmjs.org/glob/-/glob-5.0.14.tgz"
                }
            }
        },

5. Найти все ссылки на glob, указав в строке поиска `"glob":`. Если в любой из ссылок указана версия 3.1.21 или более ранняя версия, измените код json на следующий:

        "glob": {
            "version": "5.0.14",
            "from": "glob@5.0.14",
            "resolved": "https://registry.npmjs.org/glob/-/glob-5.0.14.tgz"
        }

6. Сохраните изменения и проверьте их, чтобы убедиться, что приложение все еще работает локально:

        npm install
        sails lift

4. Затем разверните приложение в Azure с помощью git:

        git add .
        git commit -m "<your commit message>"
        git push azure master

5. Наконец, просто запустите приложение Azure в браузере:

        azure site browse

    Вы должны увидеть ту же домашнюю страницу Sails.js.
    
    ![](./media/app-service-web-nodejs-sails/sails-in-azure.png)

## Устранение неполадок развертывания

При сбое приложения Sails.js в службе приложений найдите журналы stderr, которые помогут в ее устранении. Дополнительные сведения см. в разделе [Получение журналов stdout и stderr из iisnode](app-service-web-nodejs-sails.md#iisnodelog). Если приложение запущено успешно, в журнале stdout должно появиться знакомое сообщение:

                .-..-.

    Sails              <|    .-..-.
    v0.12.1             |\
                        /|.\
                        / || \
                    ,'  |'  \
                    .-'.-==|/_--'
                    `--'-------' 
    __---___--___---___--___---___--___
    ____---___--___---___--___---___--___-__

    Server lifted in `D:\home\site\wwwroot`
    To see your app, visit http://localhost:\\.\pipe\a76e8111-663e-449d-956e-5c5deff2d304
    To shut down Sails, press <CTRL> + C at any time.

## Подключение к базе данных в Azure

Чтобы подключиться к базе данных Azure, создайте в Azure базу данных выбранного типа (база данных SQL Azure, MySQL, MongoDB, кэш Redis для Azure и т. д.) и используйте соответствующий [адаптер хранилища данных](https://github.com/balderdashy/sails#compatibility) для подключения к ней. Действия, описанные в этом разделе, показывают, как подключиться к базе данных SQL Azure.

1. Следуйте инструкциям в [этом](../sql-database/sql-database-get-started.md) руководстве, чтобы создать пустую базу данных SQL Azure на новом сервере SQL. Параметры брандмауэра по умолчанию разрешают службам Azure (например службе приложений) подключаться к этому серверу.

2. Из терминала установите адаптер SQL Server:

        npm install sails-sqlserver --save

    Поскольку вы изменили файл package.json, необходимо повторно создать файл npm-shrinkwrap.json. Вы сделаете это позже.
    
3. Удалите каталог node\_modules/.

4. Запустите `npm shrinkwrap`.

5. Снова откройте файл npm-shrinkwrap.json и обновите версии пакета `glob` так же, как в предыдущем разделе.

    Теперь вернитесь к основной задаче.
        
3. Откройте файл config/connections.js и добавьте следующий код json в список адаптеров:

        sqlserver: {
            adapter: 'sails-sqlserver',
            user: process.env.dbuser,
            password: process.env.dbpassword,
            host: process.env.sqlserver, 
            database: process.env.dbname,
            options: {
                encrypt: true   // use this for Azure databases
            }
        },

4. Каждую переменную среды (`process.env.*`) необходимо задать в службе приложений. Для этого выполните следующие команды из терминала:

        azure site appsetting add dbuser="<database server administrator>"
        azure site appsetting add dbpassword="<database server password>"
        azure site appsetting add sqlserver="<database server name>.database.windows.net"
        azure site appsetting add dbname="<database name>"
        
4. Откройте файл config/env/production.js для настройки рабочей среды и установите `connection` и `migrate` в объекте JSON `models`:

        models: {
            connection: 'sqlserver',
            migrate: 'alter'
        },

4. В терминале [создайте](http://sailsjs.org/documentation/reference/command-line-interface/sails-generate) [проектный API](http://sailsjs.org/documentation/concepts/blueprints) для Sails.js, как обычно. Например:

         sails generate api mywidget
     
5. Сохраните все изменения, отправьте их в Azure и перейдите к своему приложению, чтобы убедиться, что оно работает.

        git add .
        git commit -m "<your commit message>"
        git push azure master
        azure site browse

6. Теперь обратитесь к проектному API, только что созданному в браузере. Например:

        http://<appname>.azurewebsites.net/widget/create
    
    API должен возвращать созданный объект обратно в окно браузера:
    
        {"id":1,"createdAt":"2016-03-28T23:08:01.000Z","updatedAt":"2016-03-28T23:08:01.000Z"}

## Дополнительные ресурсы

- [Приступая к работе с веб-приложениями Node.js в службе приложений Azure](app-service-web-nodejs-get-started.md)
- [Использование модулей Node.js с приложениями Azure](../nodejs-use-node-modules-azure-apps.md)

<!---HONumber=AcomDC_0406_2016-->
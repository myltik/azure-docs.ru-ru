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
	ms.date="09/23/2016"
	ms.author="cephalin"/>

# Развертывание веб-приложения Sails.js в службе приложений Azure

В этом учебнике показано, как развернуть веб-приложение Sails.js в службе приложений Azure. При работе с учебником вы получите некоторые общие знания о настройке приложения Node.js для запуска в службе приложений.

У вас должен быть опыт работы с Sails.js. Этот учебник не содержит рекомендаций по решению проблем, связанных с управлением Sails.js в целом.


## Предварительные требования

- [Node.js](https://nodejs.org/)
- [Sails.js](http://sailsjs.org/get-started)
- [Git.](http://www.git-scm.com/downloads)
- [Интерфейс командной строки Azure](../xplat-cli-install.md)
- Учетная запись Microsoft Azure. Если у вас нет учетной записи, можно [подписаться на бесплатную пробную версию](/pricing/free-trial/?WT.mc_id=A261C142F) или [активировать преимущества для подписчиков Visual Studio](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F).

>[AZURE.NOTE] Чтобы увидеть службу приложений Azure в действии до создания учетной записи Azure, перейдите на страницу [пробного использования службы приложений](http://go.microsoft.com/fwlink/?LinkId=523751). Там можно быстро создать кратковременное приложение начального уровня в службе приложений. Для этого не потребуется ни кредитная карта, ни какие-либо обязательства.

## Шаг 1. Создание приложения Sails.js в локальной среде

Сначала быстро создайте приложение Sails.js по умолчанию в своей среде разработки, выполнив следующее.

1. Откройте терминал и `CD` в рабочий каталог.

2. Создайте приложение Sails.js и запустите его.

        sails new <appname>
        cd <appname>
        sails lift

    Убедитесь, что домашняя страница по умолчанию http://localhost:1377 открывается.

## Шаг 2. Создание ресурса приложения Azure

Теперь создайте ресурс службы приложений в Azure. Позже вы развернете в него свое приложение.

1. Войдите в Azure.
1. В том же окне терминала переключитесь на режим ASM и войдите в Azure.

        azure config mode asm
        azure login

    Следуйте указаниям, чтобы продолжить вход в браузере с помощью учетной записи Майкрософт в рамках своей подписки Azure.

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

    Теперь ведение журнала для iisnode включено. Дополнительные сведения о том, как это работает, см. в разделе [Получение журналов stdout и stderr из iisnode](app-service-web-nodejs-get-started.md#iisnodelog).

2. Откройте файл config/env/production.js для настройки рабочей среды и установите `port` и `hookTimeout`.

        module.exports = {

            // Use process.env.port to handle web requests to the default HTTP port
            port: process.env.port,
            // Increase hooks timout to 30 seconds
            // This avoids the Sails.js error documented at https://github.com/balderdashy/sails/issues/2691
            hookTimeout: 30000,

            ...
        };

    Сведения об этих параметрах конфигурации можно найти в [документации Sails.js](http://sailsjs.org/documentation/reference/configuration/sails-config).

    Далее необходимо убедиться, что [Grunt](https://www.npmjs.com/package/grunt) совместим с сетевыми дисками Azure. Версии Grunt, предшествующие 1.0.0, используют устаревший пакет [glob](https://www.npmjs.com/package/glob) (до версии 5.0.14), который не поддерживает сетевые диски.

3. Откройте файл package.json и измените версию `grunt` на `1.0.0`, а затем удалите все пакеты `grunt-*`. Свойство `dependencies` должно выглядеть следующим образом.

        "dependencies": {
            "ejs": "<leave-as-is>",
            "grunt": "1.0.0",
            "include-all": "<leave-as-is>",
            "rc": "<leave-as-is>",
            "sails": "<leave-as-is>",
            "sails-disk": "<leave-as-is>",
            "sails-sqlserver": "<leave-as-is>"
        },

3. Добавьте следующее свойство `engines` в файл package.json, чтобы задать нужную нам версию Node.js.

        "engines": {
            "node": "6.6.0"
        },

6. Сохраните изменения и проверьте их, чтобы убедиться, что приложение все еще работает локально. Для этого удалите папку `node_modules`, а затем выполните следующую команду.

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
    v0.12.4             |\
                        /|.\
                        / || \
                    ,'  |'  \
                    .-'.-==|/_--'
                    `--'-------' 
    __---___--___---___--___---___--___
    ____---___--___---___--___---___--___-__

    Server lifted in `D:\home\site\wwwroot`
    To see your app, visit http://localhost:\\.\pipe\c775303c-0ebc-4854-8ddd-2e280aabccac
    To shut down Sails, press <CTRL> + C at any time.

Степень детализации журналов stdout определяется в файле [config/log.js](http://sailsjs.org/#!/documentation/concepts/Logging).

## Подключение к базе данных в Azure

Чтобы подключиться к базе данных Azure, создайте в Azure базу данных выбранного типа (база данных SQL Azure, MySQL, MongoDB, кэш Redis для Azure и т. д.) и используйте соответствующий [адаптер хранилища данных](https://github.com/balderdashy/sails#compatibility) для подключения к ней. Действия, описанные в этом разделе, показывают, как подключиться к базе данных MySQL в Azure.

1. Следуйте инструкциям в [этом](../store-php-create-mysql-database.md) учебнике, чтобы создать базу данных MySQL в Azure.

2. Из командной строки терминала установите адаптер MySQL.

        npm install sails-mysql --save

3. Откройте файл config/connections.js и добавьте в список следующий объект соединения:

        mySql: {
            adapter: 'sails-mysql',
            user: process.env.dbuser,
            password: process.env.dbpassword,
            host: process.env.dbhost, 
            database: process.env.dbname,
            options: {
                encrypt: true
            }
        },

4. Каждую переменную среды (`process.env.*`) необходимо задать в службе приложений. Для этого выполните следующие команды из терминала. Все необходимые сведения о подключении находятся на портале Azure (см. раздел [Подключение к базе данных MySQL](../store-php-create-mysql-database.md#connect)).

        azure site appsetting add dbuser="<database user>"
        azure site appsetting add dbpassword="<database password>"
        azure site appsetting add dbhost="<database hostname>"
        azure site appsetting add dbname="<database name>"
        
    Чтобы предотвратить доступ системы управления версиями (Git) к конфиденциальным данным, в код приложения Azure необходимо добавить собственные параметры. Далее необходимо настроить такие же данные о подключении для среды разработки.

4. Откройте файл config/local.js и добавьте следующий объект соединения:

        connections: {
            mySql: {
                user: "<database user>",
                password: "<database password>",
                host: "<database hostname>", 
                database: "<database name>",
            },
        },
    
    Эта конфигурация позволяет переопределить параметры локальной среды в файле config/connections.js. Этот файл входит в список исключений, определенный в GITIGNORE-файле по умолчанию для проекта, поэтому его нельзя сохранить в репозитории Git. Теперь подключаться к базе данных MySQL можно как из веб-приложения Azure, так из локальной среды разработки.

4. Чтобы настроить рабочую среду, откройте файл config/env/production.js и добавьте в него следующий объект `models`.

        models: {
            connection: 'mySql',
            migrate: 'safe'
        },

4. Чтобы настроить среду разработки, откройте файл config/env/development.js и добавьте в него следующий объект `models`.

        models: {
            connection: 'mySql',
            migrate: 'alter'
        },

    Объект `migrate: 'alter'` позволяет использовать функции миграции, чтобы легко создавать и обновлять таблицы в базе данных MySQL. Так как приложение Sails.js не поддерживает параметр `migrate: 'alter'`, в рабочей среде Azure используется параметр `migrate: 'safe'` (дополнительные сведения см. в [документации по Sails.js](http://sailsjs.org/documentation/concepts/models-and-orm/model-settings)).

4. Чтобы создать базу данных с возможностями миграции Sails.js, в терминале [создайте](http://sailsjs.org/documentation/reference/command-line-interface/sails-generate) [проектный API](http://sailsjs.org/documentation/concepts/blueprints) для Sails.js, следуя обычной процедуре, а затем выполните команду `sails lift`. Например:

         sails generate api mywidget
         sails lift

    После выполнения этой команды создается пустая модель `mywidget`, но с ее помощью можно подтвердить наличие связи с базой данных. При выполнении команды `sails lift` для моделей, используемых приложением, создаются отсутствующие таблицы.

6. Обратитесь к проектному API, только что созданному в браузере. Например:

        http://localhost:1337/mywidget/create
    
    API должен возвратить созданный объект в окно браузера, что будет означать, что база данных успешно создана.

        {"id":1,"createdAt":"2016-09-23T13:32:00.000Z","updatedAt":"2016-09-23T13:32:00.000Z"}

5. Теперь отправьте изменения в Azure и перейдите к своему приложению, чтобы убедиться, что оно работает.

        git add .
        git commit -m "<your commit message>"
        git push azure master
        azure site browse

6. Обратитесь к проектному API веб-приложения Azure. Например:

        http://<appname>.azurewebsites.net/mywidget/create

    Если API возвращает другую новую запись, это значит, что между веб-приложением Azure и базой данных MySQL осуществляется взаимодействие.

## Дополнительные ресурсы

- [Приступая к работе с веб-приложениями Node.js в службе приложений Azure](app-service-web-nodejs-get-started.md)
- [Использование модулей Node.js с приложениями Azure](../nodejs-use-node-modules-azure-apps.md)

<!---HONumber=AcomDC_0928_2016-->
---
title: "Развертывание веб-приложения Sails.js в службе приложений Azure | Документация Майкрософт"
description: "Сведения о развертывании приложения Node.js в службе приложений Azure. В этом учебнике показано, как развернуть веб-приложение Sails.js."
services: app-service\web
documentationcenter: nodejs
author: cephalin
manager: erikre
editor: 
ms.assetid: 8877ddc8-1476-45ae-9e7f-3c75917b4564
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 12/16/2016
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: 1818f48b94754589a2dc5d89c2ba24d2ff2681bc
ms.lasthandoff: 03/21/2017


---
# <a name="deploy-a-sailsjs-web-app-to-azure-app-service"></a>Развертывание веб-приложения Sails.js в службе приложений Azure
В этом учебнике показано, как развернуть веб-приложение Sails.js в службе приложений Azure. При работе с учебником вы получите некоторые общие знания о настройке приложения Node.js для запуска в службе приложений.

Здесь вы овладеете такими полезными навыками:

* Настройка запуска приложения Sails.js в службе приложений.
* Развертывание приложения в службе приложений из командной строки.
* Устранение неполадок развертывания с помощью журналов stderr и stdout.
* Сохранение переменных среды вне системы управления версиями.
* Получение доступа к переменным среды Azure из приложения.
* Подключение к базе данных (MongoDB).

У вас должен быть опыт работы с Sails.js. Этот учебник не содержит рекомендаций по решению проблем, связанных с управлением Sails.js в целом.

## <a name="cli-versions-to-complete-the-task"></a>Версии интерфейса командной строки для выполнения задачи

Вы можете выполнить задачу, используя одну из следующих версий интерфейса командной строки.

- [Azure CLI 1.0](app-service-web-nodejs-sails-cli-nodejs.md) — интерфейс командной строки для классической модели развертывания и модели развертывания Resource Manager.
- [Azure CLI 2.0](app-service-web-nodejs-sails.md) — интерфейс командной строки следующего поколения для модели развертывания с помощью Resource Manager.

## <a name="prerequisites"></a>Предварительные требования
* [Node.js](https://nodejs.org/)
* [Sails.js](http://sailsjs.org/get-started)
* [Git.](http://www.git-scm.com/downloads)
* [Интерфейс командной строки Azure](../cli-install-nodejs.md)
* Учетная запись Microsoft Azure. Если у вас нет учетной записи, [подпишитесь на бесплатную пробную версию](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F) или [активируйте преимущества для подписчиков Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F).

> [!NOTE]
> [Пробное использование службы приложений](https://azure.microsoft.com/try/app-service/) возможно даже без учетной записи Azure. Вы можете создать приложение начального уровня и экспериментировать с ним в течение часа. Для этого вам не нужно указывать данные кредитной карты или брать на себя какие-либо обязательства.
> 
> 

## <a name="step-1-create-a-sailsjs-app-locally"></a>Шаг 1. Создание приложения Sails.js в локальной среде
Сначала быстро создайте приложение Sails.js по умолчанию в своей среде разработки, выполнив следующее.

1. Откройте терминал и `CD` в рабочий каталог.
2. Создайте приложение Sails.js и запустите его.

        sails new <appname>
        cd <appname>
        sails lift

    Убедитесь, что домашняя страница по умолчанию http://localhost:1377 открывается.

## <a name="step-2-create-the-azure-app-resource"></a>Шаг 2. Создание ресурса приложения Azure
Теперь создайте ресурс службы приложений в Azure. Позже вы развернете в него свое приложение.

1. Войдите в Azure.
2. В том же окне терминала переключитесь на режим ASM и войдите в Azure.

        azure config mode asm
        azure login

    Следуйте указаниям, чтобы продолжить вход в браузере с помощью учетной записи Майкрософт в рамках своей подписки Azure.

3. Укажите пользователя развертывания для службы приложений. Вы развернете код позже с помощью учетных данных.
   
        azure site deployment user set --username <username> --pass <password>

3. Убедитесь, что вы по-прежнему находитесь в корневом каталоге проекта Sails.js. Создайте ресурс приложения службы приложений Azure с уникальным именем приложения, используя приведенную ниже команду. URL-адрес веб-приложения будет таким: http://&lt;имя_приложения>.azurewebsites.net.

        azure site create --git <appname>

    Следуйте указаниям, чтобы выбрать регион Azure для развертывания. После создания ресурса для приложения службы приложений:

   * приложение Sails.js инициализировано для Git;
   * локальный репозиторий, инициализированный для Git, подключен к новому приложению службы приложений как удаленный ресурс Git с соответствующим именем "azure";
   * файл iisnode.yml создан в корневом каталоге. Этот файл можно применить для настройки узла [iisnode](https://github.com/tjanczuk/iisnode), который служба приложений использует для запуска приложений Node.js.

## <a name="step-3-configure-and-deploy-your-sailsjs-app"></a>Шаг 3. Настройка и развертывание приложения Sails.js
 Работа с приложением Sails.js в службе приложений состоит из трех основных этапов:

* Настройка приложения для его запуска в службе приложений
* Развертывание приложения в службу приложений
* Устранение неполадок развертывания с помощью журналов stderr и stdout

Выполните следующие действия.

1. Откройте новый файл iisnode.yml в корневом каталоге и добавьте в него следующие две строки:

        loggingEnabled: true
        logDirectory: iisnode

    Теперь для сервера [iisnode](https://github.com/tjanczuk/iisnode), который служба приложений Azure использует для запуска приложений Node.js, включено ведение журнала. 
    Дополнительные сведения о том, как это работает, см. в разделе  [Получение журналов stdout и stderr из iisnode](app-service-web-nodejs-get-started.md#iisnodelog).
2. Откройте файл config/env/production.js для настройки рабочей среды и установите `port` и `hookTimeout`.

        module.exports = {

            // Use process.env.port to handle web requests to the default HTTP port
            port: process.env.port,
            // Increase hooks timout to 30 seconds
            // This avoids the Sails.js error documented at https://github.com/balderdashy/sails/issues/2691
            hookTimeout: 30000,

            ...
        };

    Сведения об этих параметрах конфигурации можно найти в  [документации Sails.js](http://sailsjs.org/documentation/reference/configuration/sails-config).

4. Добавьте следующее свойство `engines` в файл package.json, чтобы задать нужную нам версию Node.js.

        "engines": {
            "node": "6.9.1"
        },
5. Сохраните изменения и проверьте их, чтобы убедиться, что приложение все еще работает локально. Для этого удалите папку `node_modules` , а затем выполните следующую команду.

        npm install
        sails lift
6. Затем разверните приложение в Azure с помощью git:

        git add .
        git commit -m "<your commit message>"
        git push azure master
7. Наконец, просто запустите приложение Azure в браузере:

        azure site browse

    Вы должны увидеть ту же домашнюю страницу Sails.js.

    ![](./media/app-service-web-nodejs-sails/sails-in-azure.png)

## <a name="troubleshoot-your-deployment"></a>Устранение неполадок развертывания
При сбое приложения Sails.js в службе приложений найдите журналы stderr, которые помогут в ее устранении.
Дополнительные сведения см. в разделе [Получение журналов stdout и stderr из iisnode](app-service-web-nodejs-get-started.md#get-stdout-and-stderr-logs-from-iisnode).
Если приложение запущено успешно, в журнале stdout должно появиться знакомое сообщение:

                   .-..-.
    
       Sails              <|    .-..-.
       v0.12.11            |\
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

Степень детализации журналов stdout определяется в файле [config/log.js](http://sailsjs.org/#!/documentation/concepts/Logging) .

## <a name="connect-to-a-database-in-azure"></a>Подключение к базе данных в Azure
Чтобы подключиться к базе данных Azure, создайте в Azure базу данных выбранного типа (база данных SQL Azure, MySQL, MongoDB, кэш Redis для Azure и т. д.) и используйте соответствующий [адаптер хранилища данных](https://github.com/balderdashy/sails#compatibility) для подключения к ней. Действия, описанные в этом разделе, помогут вам подключиться к базе данных MongoDB с помощью базы данных [Azure DocumentDB](../documentdb/documentdb-protocol-mongodb.md), которая поддерживает подключения к клиенту MongoDB.

1. [Создайте учетную запись DocumentDB с поддержкой протокола MongoDB.](../documentdb/documentdb-create-mongodb-account.md)
2. [Создайте коллекцию и базу данных DocumentDB.](../documentdb/documentdb-create-collection.md) Имя коллекции не имеет значения, но при подключении из Sails.js необходимо знать имя базы данных.
3. [Найдите сведения о подключении к базе данных DocumentDB.](../documentdb/documentdb-connect-mongodb-account.md#a-idgetcustomconnectiona-get-the-mongodb-connection-string-to-customize)
2. В терминале командной строки установите адаптер MongoDB:

        npm install sails-mongo --save

3. Откройте файл config/connections.js и добавьте в список следующий объект соединения:

        docDbMongo: {
            adapter: 'sails-mongo',
            user: process.env.dbuser,
            password: process.env.dbpassword,
            host: process.env.dbhost,
            port: process.env.dbport,
            database: process.env.dbname,
            ssl: true
        },

    > [!NOTE] 
    > Параметр `ssl: true` важен, так как он требуется для [Azure DocumentDB](../documentdb/documentdb-connect-mongodb-account.md#connection-string-requirements). 
    >
    >

4. Каждую переменную среды (`process.env.*`) необходимо задать в службе приложений. Для этого выполните следующие команды из терминала. Используйте сведения о подключении для базы данных DocumentDB.

        azure site appsetting add dbuser="<database user>"
        azure site appsetting add dbpassword="<database password>"
        azure site appsetting add dbhost="<database hostname>"
        azure site appsetting add dbport="<database port>"
        azure site appsetting add dbname="<database name>"

    Чтобы предотвратить доступ системы управления версиями (Git) к конфиденциальным данным, в код приложения Azure необходимо добавить собственные параметры. Далее необходимо настроить такие же данные о подключении для среды разработки.
5. Откройте файл config/local.js и добавьте следующий объект соединения:

        connections: {
            docDbMongo: {
                user: "<database user>",
                password: "<database password>",
                host: "<database hostname>",
                database: "<database name>",
                ssl: true
            },
        },

    Эта конфигурация позволяет переопределить параметры локальной среды в файле config/connections.js. Этот файл входит в список исключений, определенный в GITIGNORE-файле по умолчанию для проекта, поэтому его нельзя сохранить в репозитории Git. Теперь подключаться к базе данных DocumentDB (MongoDB) можно как из веб-приложения Azure, так и из локальной среды разработки.
6. Чтобы настроить рабочую среду, откройте файл config/env/production.js и добавьте в него следующий объект `models` .

        models: {
            connection: 'docDbMongo',
            migrate: 'safe'
        },
7. Чтобы настроить среду разработки, откройте файл config/env/development.js и добавьте в него следующий объект `models`.

        models: {
            connection: 'docDbMongo',
            migrate: 'alter'
        },

    Объект `migrate: 'alter'` позволяет использовать функции миграции, чтобы легко создавать и обновлять коллекции и таблицы в базе данных. Так как приложение Sails.js не поддерживает параметр `migrate: 'alter'`, в (рабочей) среде Azure используется параметр `migrate: 'safe'` (дополнительные сведения см. в  [документации по Sails.js](http://sailsjs.org/documentation/concepts/models-and-orm/model-settings)).
8. Чтобы создать базу данных с возможностями миграции Sails.js, в терминале [создайте](http://sailsjs.org/documentation/reference/command-line-interface/sails-generate) [проектный API](http://sailsjs.org/documentation/concepts/blueprints) для Sails.js, следуя обычной процедуре, а затем выполните команду `sails lift`. Например:

         sails generate api mywidget
         sails lift

    После выполнения этой команды создается пустая модель `mywidget` , но с ее помощью можно подтвердить наличие связи с базой данных.
    При выполнении команды `sails lift` для моделей, используемых приложением, создаются отсутствующие коллекции или таблицы.
9. Обратитесь к проектному API, только что созданному в браузере. Например:

        http://localhost:1337/mywidget/create

    API должен вернуть созданный объект в окно браузера, что будет означать, что база данных успешно создана.

        {"id":1,"createdAt":"2016-09-23T13:32:00.000Z","updatedAt":"2016-09-23T13:32:00.000Z"}
10. Теперь отправьте изменения в Azure и перейдите к своему приложению, чтобы убедиться, что оно работает.

         git add .
         git commit -m "<your commit message>"
         git push azure master
         azure site browse
11. Обратитесь к проектному API веб-приложения Azure. Например:

         http://<appname>.azurewebsites.net/mywidget/create

     Если API возвращает другую новую запись, это значит, что между веб-приложением Azure и базой данных DocumentDB (MongoDB) осуществляется взаимодействие.

## <a name="more-resources"></a>Дополнительные ресурсы
* [Приступая к работе с веб-приложениями Node.js в службе приложений Azure](app-service-web-nodejs-get-started.md)
* [Использование модулей Node.js с приложениями Azure](../nodejs-use-node-modules-azure-apps.md)


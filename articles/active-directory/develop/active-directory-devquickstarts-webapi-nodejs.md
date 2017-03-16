---
title: "Начало работы с Node.js в Azure AD | Документация Майкрософт"
description: "Практическое руководство по созданию на основе Node.js веб-интерфейса REST API, который интегрируется с Azure AD для аутентификации."
services: active-directory
documentationcenter: nodejs
author: xerners
manager: mbaldwin
editor: 
ms.assetid: 7654ab4c-4489-4ea5-aba9-d7cdc256e42a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: javascript
ms.topic: article
ms.date: 01/07/2017
ms.author: brandwe
translationtype: Human Translation
ms.sourcegitcommit: 682f6d0919df20cd3643abf04e1d5192be116399
ms.openlocfilehash: f20c2c2c37692d439ca8e023cf9d5a48cd42c973
ms.lasthandoff: 02/24/2017


---
# <a name="get-started-with-web-apis-for-nodejs"></a>Начало работы с веб-API для Node.js
[!INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

*Passport* — промежуточный слой проверки подлинности для Node.js. Этот слой имеет гибкую модульную структуру, которая позволяет незаметно размещать его в любом веб-приложении на основе Express или Restify. Он также позволяет использовать разные стратегии с поддержкой аутентификации на основе имени пользователя и пароля, учетных записей Facebook, Twitter и пр. Мы разработали стратегию для Microsoft Azure Active Directory (Azure AD). Теперь мы установим этот модуль и добавим подключаемый модуль Microsoft Azure Active Directory `passport-azure-ad`.

Для этого необходимо выполнить следующие действия.

1. зарегистрировать приложение в Azure AD;
2. настроить приложение для использования подключаемого модуля Passport `passport-azure-ad`;
3. настроить клиентское приложение для вызова методов веб-API приложения со списком дел.

Код в этом учебнике размещен на портале [GitHub](https://github.com/Azure-Samples/active-directory-node-webapi).

> [!NOTE]
> В этой статье не рассматривается реализация входа, регистрации и управления профилями с помощью Azure AD B2C. Она посвящена вызову веб-API после того, как пользователь прошел проверку подлинности.  Мы рекомендуем перед началом работы ознакомиться с обзорной статьей, посвященной [интеграции с Azure Active Directory](active-directory-how-to-integrate.md).
>
>

Полный исходный код для этого рабочего примера мы опубликовали в GitHub на условиях лицензии MIT, поэтому вы можете без проблем клонировать (а еще лучше — разветвлять) его, а также оставлять отзывы и отправлять запросы на включение внесенных изменений.

## <a name="about-nodejs-modules"></a>Информация о модулях Node.js
В этом пошаговом руководстве мы используем модули Node.js. Модули — это загружаемые пакеты JavaScript, дающую приложению определенную функциональность. Обычно для установки модулей применяется средство NPM для командной строки Node.js, которое запускается в каталоге установки NPM. Но некоторые модули, например модуль HTTP, уже включены в основной пакет Node.js.

Установленные модули сохраняются в каталоге **node_modules**, который находится в корневом каталоге установки Node.js. Каждый модуль в каталоге **node_modules** использует свой собственный каталог **node_modules**, в котором хранит свои модули-зависимости. Кроме того, отдельный каталог **node_modules** создается для каждого требуемого модуля. Такая рекурсивная структура каталога представляет цепочку зависимостей.

Эта структура цепочки зависимостей занимает много дискового пространства. Но зато она гарантирует, что всегда будут выполнены все зависимости и что версии модулей, используемых в разработке и в производственной среде, будут совпадать. Это делает более предсказуемым поведение рабочего приложения и предотвращает проблемы с управлением версиями, которые могут повлиять на работу пользователей.

## <a name="step-1-register-an-azure-ad-tenant"></a>Шаг 1. Регистрация клиента Azure AD
Для использования этого примера вам понадобится клиент Azure Active Directory. Если вы не знаете, что это такое и как его получить, ознакомьтесь с [этой статьей о клиенте Azure AD](active-directory-howto-tenant.md).

## <a name="step-2-create-an-application"></a>Этап 2. Создание приложения
Затем создайте приложение в каталоге, которое предоставит Azure AD сведения, необходимые для безопасного взаимодействия с вашим приложением.  В нашем примере и клиентское приложение, и веб-API представлены одним **идентификатором приложения**, так как они представляют одно приложение логики.  Создайте приложение, выполнив [эти указания](active-directory-how-applications-are-added.md). Если вы создаете бизнес-приложение, возможно, [вам будут полезны эти дополнительные инструкции](../active-directory-applications-guiding-developers-for-lob-applications.md).

Выполните эти действия, чтобы создать приложение.

1. Войдите на [портал Azure](https://portal.azure.com).

2. В меню сверху выберите учетную запись. В списке **Каталог** выберите клиент Active Directory, в котором вы решили зарегистрировать приложение.

3. Выберите **Больше служб** в меню слева, а затем выберите **Azure Active Directory**.

4. Щелкните **Регистрация приложений**, а затем выберите **Добавить**.

5. Следуйте инструкциям на экране, чтобы создать **веб-приложение и (или) веб-API**.

      * **Имя** приложения служит его описанием для конечных пользователей.

      * **URL-адрес входа** — это базовый URL-адрес вашего приложения.  В примере кода указан URL-адрес по умолчанию: `https://localhost:8080`.

      * **URI идентификатора приложения** — это уникальный идентификатор вашего приложения. Здесь принято использовать адрес вида `https://<tenant-domain>/<app-name>`, например такой: `https://contoso.onmicrosoft.com/my-first-aad-app`.

6. Когда вы закончите регистрацию, Azure AD назначит приложению уникальный идентификатор. Это значение вам понадобится в следующих разделах, поэтому не забудьте скопировать его на странице приложения.

7. Создайте **ключ** приложения на странице **Параметры** и сохраните его. Скоро он вам понадобится.

## <a name="step-3-download-nodejs-for-your-platform"></a>Шаг 3. Скачивание Node.js для своей платформы
Чтобы успешно использовать этот пример, необходимо иметь рабочую установку Node.js.

Установите Node.js с сайта [http://nodejs.org](http://nodejs.org).

## <a name="step-4-install-mongodb-on-your-platform"></a>Шаг 4. Установка MongoDB на вашей платформе
Чтобы успешно использовать этот пример, необходимо иметь рабочую установку MongoDB. MongoDB вам нужен для того, чтобы интерфейс REST API оставался неизменным при использовании нескольких экземпляров сервера.

Установите MongoDB с сайта [http://mongodb.org](http://www.mongodb.org).

> [!NOTE]
> В этом руководстве мы предполагаем, что вы используете для MongoDB заданные по умолчанию конечные точки установки и сервера. На момент написания этой статьи для них используется значение mongodb://localhost.
>
>

## <a name="step-5-install-the-restify-modules-in-your-web-api"></a>Шаг 5. Установка модулей Restify для веб-API
Мы будем использовать Restify для создания интерфейса REST API. Restify — это минималистичная и гибкая платформа для приложений Node.j, созданная на основе Express. Она располагает широким набором функций, позволяющих реализовать интерфейсы REST API поверх Connect.

### <a name="install-restify"></a>Установка Restify
1. В командной строке перейдите в каталог **azuread**. Если каталог **azuread** не существует, создайте его.

        `cd azuread - or- mkdir azuread; cd azuread`

2. Введите следующую команду:

    `npm install restify`

    Эта команда устанавливает Restify.

#### <a name="did-you-get-an-error"></a>Вы получили сообщение об ошибке?
В некоторых операционных системах при использовании параметра NPM может появиться такое сообщение об ошибке (**Error: EPERM, chmod '/usr/local/bin/..'**). Оно может сопровождаться предложением использовать учетную запись с правами администратора. Если вы увидите такую ошибку, запустите NPM с более высоким уровнем привилегий, используя команду sudo.

#### <a name="did-you-get-an-error-regarding-dtrace"></a>Вы получили сообщение об ошибке, которая относится к DTRACE?
При установке Restify может появиться примерно следующее сообщение об ошибке:

```Shell
clang: error: no such file or directory: 'HD/azuread/node_modules/restify/node_modules/dtrace-provider/libusdt'
make: *** [Release/DTraceProviderBindings.node] Error 1
gyp ERR! build error
gyp ERR! stack Error: `make` failed with exit code: 2
gyp ERR! stack     at ChildProcess.onExit (/usr/local/lib/node_modules/npm/node_modules/node-gyp/lib/build.js:267:23)
gyp ERR! stack     at ChildProcess.EventEmitter.emit (events.js:98:17)
gyp ERR! stack     at Process.ChildProcess._handle.onexit (child_process.js:789:12)
gyp ERR! System Darwin 13.1.0
gyp ERR! command "node" "/usr/local/lib/node_modules/npm/node_modules/node-gyp/bin/node-gyp.js" "rebuild"
gyp ERR! cwd /Volumes/Development HD/azuread/node_modules/restify/node_modules/dtrace-provider
gyp ERR! node -v v0.10.11
gyp ERR! node-gyp -v v0.10.0
gyp ERR! not ok
npm WARN optional dep failed, continuing dtrace-provider@0.2.8
```
Restify предоставляет мощный механизм для трассировки вызовов REST с помощью DTrace. Но во многих операционных системах DTrace отсутствует. Эти ошибки можно игнорировать.

Результат этой команды должен выглядеть примерно так:

    restify@2.6.1 node_modules/restify
    ├── assert-plus@0.1.4
    ├── once@1.3.0
    ├── deep-equal@0.0.0
    ├── escape-regexp-component@1.0.2
    ├── qs@0.6.5
    ├── tunnel-agent@0.3.0
    ├── keep-alive-agent@0.0.1
    ├── lru-cache@2.3.1
    ├── node-uuid@1.4.0
    ├── negotiator@0.3.0
    ├── mime@1.2.11
    ├── semver@2.2.1
    ├── spdy@1.14.12
    ├── backoff@2.3.0
    ├── formidable@1.0.14
    ├── verror@1.3.6 (extsprintf@1.0.2)
    ├── csv@0.3.6
    ├── http-signature@0.10.0 (assert-plus@0.1.2, asn1@0.1.11, ctype@0.5.2)
    └── bunyan@0.22.0 (mv@0.0.5)


## <a name="step-6-install-passportjs-in-your-web-api"></a>Шаг 6. Установка Passport.js для веб-интерфейса API
[Passport](http://passportjs.org/) — промежуточный слой проверки подлинности для Node.js. Этот слой имеет гибкую модульную структуру, которая позволяет незаметно размещать его в любом веб-приложении на основе Express или Restify. Он также позволяет использовать разные стратегии с поддержкой аутентификации на основе имени пользователя и пароля, учетных записей Facebook, Twitter и пр.

Мы разработали стратегию для Azure Active Directory. Мы установим этот модуль, а затем добавим подключаемый модуль стратегии Azure Active Directory.

1. В командной строке перейдите в каталог **azuread**.

2. Введите следующую команду для установки passport.js:

    `npm install passport`

    Результат этой команды должен выглядеть примерно так:

``
        passport@0.1.17 node_modules\passport
        ├── pause@0.0.1
        └── pkginfo@0.2.3
``

## <a name="step-7-add-passport-azure-ad-to-your-web-api"></a>Шаг 7. Добавление Passport-Azure-AD для веб-API
Теперь мы добавим стратегию OAuth с помощью набора стратегий `passport-azure-ad`, который устанавливает подключение Azure AD к Passport. В этом примере REST API мы будем использовать эту стратегию для токенов носителя.

> [!NOTE]
> Платформа OAuth2 позволяет выдавать маркеры любого известного типа, но в большинстве случаев используются маркеры только нескольких типов. Для защиты конечных точек чаще всего применяются токены носителя. Это самый распространенный тип выдаваемых маркеров в OAuth2. Во многих реализациях предполагается, что токены носителя — это единственный тип выдаваемых маркеров.
>
>

В командной строке перейдите в каталог **azuread**.

Введите следующую команду для установки модуля `passport-azure-ad module` для Passport.js:

`npm install passport-azure-ad`

Результат этой команды должен выглядеть примерно так:


    passport-azure-ad@1.0.0 node_modules/passport-azure-ad
    ├── xtend@4.0.0
    ├── xmldom@0.1.19
    ├── passport-http-bearer@1.0.1 (passport-strategy@1.0.0)
    ├── underscore@1.8.3
    ├── async@1.3.0
    ├── jsonwebtoken@5.0.2
    ├── xml-crypto@0.5.27 (xpath.js@1.0.6)
    ├── ursa@0.8.5 (bindings@1.2.1, nan@1.8.4)
    ├── jws@3.0.0 (jwa@1.0.1, base64url@1.0.4)
    ├── request@2.58.0 (caseless@0.10.0, aws-sign2@0.5.0, forever-agent@0.6.1, stringstream@0.0.4, tunnel-agent@0.4.1, oauth-sign@0.8.0, isstream@0.1.2, extend@2.0.1, json-stringify-safe@5.0.1, node-uuid@1.4.3, qs@3.1.0, combined-stream@1.0.5, mime-types@2.0.14, form-data@1.0.0-rc1, http-signature@0.11.0, bl@0.9.4, tough-cookie@2.0.0, hawk@2.3.1, har-validator@1.8.0)
    └── xml2js@0.4.9 (sax@0.6.1, xmlbuilder@2.6.4)



## <a name="step-8-add-mongodb-modules-to-your-web-api"></a>Шаг 8. Добавление модулей MongoDB в веб-интерфейс API
Мы используем MongoDB в качестве хранилища данных. Поэтому нам потребуется широко распространенный подключаемый модуль Mongoose для управления моделями и схемами. Еще нам нужно установить драйвер базы данных для MongoDB (который также называется MongoDB).

 `npm install mongoose`

## <a name="step-9-install-additional-modules"></a>Шаг 9. Установка дополнительных модулей
Далее мы установим остальные необходимые модули.

1. В окне командной строки перейдите в каталог **azuread** или убедитесь, что вы уже находитесь в нем.

    `cd azuread`

2. Введите следующие команды для установки необходимых модулей в каталог **node_modules**:

    * `npm install assert-plus`
    * `npm install bunyan`
    * `npm update`

## <a name="step-10-create-a-serverjs-with-your-dependencies"></a>Шаг 10. Создание server.js с зависимостями
Основная часть функций для сервера веб-API реализована в файле server.js. В этот файл мы включим основную часть нашего кода. Для использования в рабочей среде мы рекомендуем разделить код функций на небольшие файлы, например отдельные маршруты и контроллеры. В этом демонстрационном примере мы включим все эти механизмы в файл server.js.

1. В окне командной строки перейдите в каталог **azuread** или убедитесь, что вы уже находитесь в нем.

    `cd azuread`

2. Создайте файл `server.js` в любом удобном редакторе и добавьте в него следующие сведения:

    ```Javascript
        'use strict';

        /**
         * Module dependencies.
         */

        var fs = require('fs');
        var path = require('path');
        var util = require('util');
        var assert = require('assert-plus');
        var bunyan = require('bunyan');
        var getopt = require('posix-getopt');
        var mongoose = require('mongoose/');
        var restify = require('restify');
        var passport = require('passport');
      var BearerStrategy = require('passport-azure-ad').BearerStrategy;
    ```

3. Сохраните файл. Мы вернемся к нему чуть позже.

## <a name="step-11-create-a-config-file-to-store-your-azure-ad-settings"></a>Шаг 11. Создание файла конфигурации для сохранения параметров Azure AD
Этот файл кода передает в Passport.js параметры конфигурации из вашего портала Azure Active Directory. Эти значения конфигурации были созданы, когда вы добавляли веб-интерфейс API на портал, выполняя первую часть этого руководства. Сейчас мы объясним, какие значения нужно указать для этих параметров в скопированном фрагменте кода.

1. В окне командной строки перейдите в каталог **azuread** или убедитесь, что вы уже находитесь в нем.

    `cd azuread`

2. Создайте файл `config.js` в любом удобном редакторе и добавьте в него следующие сведения:

    ```Javascript
         exports.creds = {
             mongoose_auth_local: 'mongodb://localhost/tasklist', // Your mongo auth uri goes here
             clientID: 'your client ID',
             audience: 'your application URL',
            // you cannot have users from multiple tenants sign in to your server unless you use the common endpoint
          // example: https://login.microsoftonline.com/common/.well-known/openid-configuration
             identityMetadata: 'https://login.microsoftonline.com/<your tenant id>/.well-known/openid-configuration',
             validateIssuer: true, // if you have validation on, you cannot have users from multiple tenants sign in to your server
             passReqToCallback: false,
             loggingLevel: 'info' // valid are 'info', 'warn', 'error'. Error always goes to stderr in Unix.

         };
    ```
3. Сохраните файл.

## <a name="step-12-add-configuration-values-to-your-serverjs-file"></a>Шаг 12: Добавление значений конфигурации в файл server.js
Нам нужно прочитать эти значения из файла конфигурации, созданного для нашего приложения. Для этого добавим в приложение файл с расширением .config в качестве требуемого ресурса. Затем мы настроим глобальные переменные, соответствующие переменным в документе config.js.

1. В окне командной строки перейдите в каталог **azuread** или убедитесь, что вы уже находитесь в нем.

    `cd azuread`

2. Откройте файл `server.js` в любом удобном редакторе и добавьте в него следующие сведения:

    ```Javascript
    var config = require('./config');
    ```
3. Затем добавьте новый раздел в `server.js` со следующим кодом:

    ```Javascript
    var options = {
        // The URL of the metadata document for your app. We will put the keys for token validation from the URL found in the jwks_uri tag of the in the metadata.
        identityMetadata: config.creds.identityMetadata,
        clientID: config.creds.clientID,
        validateIssuer: config.creds.validateIssuer,
        audience: config.creds.audience,
        passReqToCallback: config.creds.passReqToCallback,
        loggingLevel: config.creds.loggingLevel

    };

    // Array to hold logged in users and the current logged in user (owner).
    var users = [];
    var owner = null;

    // Our logger.
    var log = bunyan.createLogger({
        name: 'Azure Active Directory Bearer Sample',
             streams: [
            {
                stream: process.stderr,
                level: "error",
                name: "error"
            },
            {
                stream: process.stdout,
                level: "warn",
                name: "console"
            }, ]
    });

      // If the logging level is specified, switch to it.
      if (config.creds.loggingLevel) { log.levels("console", config.creds.loggingLevel); }

    // MongoDB setup.
    // Set up some configuration.
    var serverPort = process.env.PORT || 8080;
    var serverURI = (process.env.PORT) ? config.creds.mongoose_auth_mongohq : config.creds.mongoose_auth_local;
    ```

4. Сохраните файл.

## <a name="step-13-add-the-mongodb-model-and-schema-information-by-using-mongoose"></a>Шаг 13. Добавление сведений о модели и схеме MongoDB с помощью Mongoose
Теперь мы соберем все три файла, подготовленные на предыдущих шагах, и поместим их в службу REST API.

В этом руководстве для хранения наших задач мы будем использовать MongoDB, как упоминалось выше в шаге 4.

В файле `config.js`, который мы создали на шаге 11, для базы данных использовалось имя `tasklist`. Именно это имя мы указали в конце URL-адреса для подключения в параметре **mogoose_auth_local**. Не нужно заранее создавать эту базу данных в MongoDB. MongoDB самостоятельно создаст ее при первом запуске нашего серверного приложения (если эта база данных еще не существует).

Теперь, когда мы указали серверу, какую базу данных MongoDB мы хотим использовать, необходимо написать дополнительный код для создания модели и схемы задач для нашего сервера.

### <a name="discussion-of-the-model"></a>Описание модели
Мы используем очень простую модель схемы. Вы можете дополнить ее в соответствии с потребностями.

Name — имя исполнителя, назначенного для задачи. Тип **Строка**.

Task — это сама задача. Тип **Строка**.

Date — запланированная дата выполнения задачи. Тип **Дата и время**.

Completed — флаг, обозначающий завершение задачи. Тип **Логическое значение**.

### <a name="creating-the-schema-in-the-code"></a>Создание схемы в коде
1. В окне командной строки перейдите в каталог **azuread** или убедитесь, что вы уже находитесь в нем.

    `cd azuread`

2. Откройте файл `server.js` в любом удобном редакторе и добавьте следующие сведения под элементом конфигурации:

    ```Javascript
    // Connect to MongoDB.
    global.db = mongoose.connect(serverURI);
    var Schema = mongoose.Schema;
    log.info('MongoDB Schema loaded');

    // Here we create a schema to store our tasks and users. It's a fairly simple schema for now.
    var TaskSchema = new Schema({
        owner: String,
        task: String,
        completed: Boolean,
        date: Date
    });

    // Use the schema to register a model.
    mongoose.model('Task', TaskSchema);
    var Task = mongoose.model('Task');
    ```
Как видно из кода, сначала мы создаем схему данных. Затем мы создаем объект модели, который используем для хранения наших данных во всех сегментах кода, где определяются **маршруты**.

## <a name="step-14-add-our-routes-for-our-task-rest-api-server"></a>Шаг 14. Добавление маршрутов для сервера задач REST API
Теперь у нас есть модель базы данных, с которой можно работать. Давайте добавим маршруты, которые мы будем использовать для сервера REST API.

### <a name="about-routes-in-restify"></a>О маршрутах в Restify
Маршруты работают в Restify точно так же, как и при использовании стека Express. Вы определяете маршруты с помощью идентификатора URI, который, как предполагается, будут вызывать клиентские приложения. Как правило, свои маршруты вы определяете в отдельном файле. Для этого примера мы поместим маршруты прямо в файл server.js. Для систем в рабочей среде мы рекомендуем вынести их в отдельный файл.

Типичный шаблон Restify для маршрута выглядит следующим образом:

```Javascript
function createObject(req, res, next) {

// Do work on object.

 _object.name = req.params.object; // passed value is in req.params under object

 ///...

return next(); // Keep the server going.
}

....

server.post('/service/:add/:object', createObject); // Calls createObject on routes that match this.

```


Это базовый шаблон. В Restify (как и в Express) доступны очень мощные функциональные возможности, например для определения типов приложений и выполнения сложной маршрутизации между несколькими конечными точками. В нашем примере мы используем самые простые маршруты.

### <a name="add-default-routes-to-our-server"></a>Добавление маршрутов по умолчанию на наш сервер
Теперь нужно добавить маршруты для базовых операций CRUD (создание, извлечение, обновление и удаление данных).

1. В окне командной строки перейдите в каталог **azuread** или убедитесь, что вы уже находитесь в нем.

    `cd azuread`

2. Откройте файл `server.js` в любом удобном редакторе и добавьте следующие сведения под ранее внесенными значениями для базы данных:

```Javascript

/**
 *
 * APIs for our REST Task server.
 */

// Create a task.

function createTask(req, res, next) {

    // Restify currently has a bug which doesn't allow you to set default headers.
    // These headers comply with CORS and allow us to mongodbServer our response to any origin.

    res.header("Access-Control-Allow-Origin", "*");
    res.header("Access-Control-Allow-Headers", "X-Requested-With");

    // Create a new task model, fill it, and save it to Mongodb.
    var _task = new Task();

    if (!req.params.task) {
        req.log.warn('createTodo: missing task');
        next(new MissingTaskError());
        return;
    }

    _task.owner = owner;
    _task.task = req.params.task;
    _task.date = new Date();

    _task.save(function(err) {
        if (err) {
            req.log.warn(err, 'createTask: unable to save');
            next(err);
        } else {
            res.send(201, _task);

        }
    });

    return next();

}


// Delete a task by name.

function removeTask(req, res, next) {

    Task.remove({
        task: req.params.task,
        owner: owner
    }, function(err) {
        if (err) {
            req.log.warn(err,
                'removeTask: unable to delete %s',
                req.params.task);
            next(err);
        } else {
            log.info('Deleted task:', req.params.task);
            res.send(204);
            next();
        }
    });
}

// Delete all tasks.

function removeAll(req, res, next) {
    Task.remove();
    res.send(204);
    return next();
}


// Get a specific task based on name.

function getTask(req, res, next) {

    log.info('getTask was called for: ', owner);
    Task.find({
        owner: owner
    }, function(err, data) {
        if (err) {
            req.log.warn(err, 'get: unable to read %s', owner);
            next(err);
            return;
        }

        res.json(data);
    });

    return next();
}

/// Simple returns the list of TODOs that were loaded.

function listTasks(req, res, next) {
    // Restify currently has a bug which doesn't allow you to set default headers.
    // These headers comply with CORS and allow us to mongodbServer our response to any origin.

    res.header("Access-Control-Allow-Origin", "*");
    res.header("Access-Control-Allow-Headers", "X-Requested-With");

    log.info("listTasks was called for: ", owner);

    Task.find({
        owner: owner
    }).limit(20).sort('date').exec(function(err, data) {

        if (err) {
            return next(err);
        }

        if (data.length > 0) {
            log.info(data);
        }

        if (!data.length) {
            log.warn(err, "There is no tasks in the database. Did you initialize the database as stated in the README?");
        }

        if (!owner) {
            log.warn(err, "You did not pass an owner when listing tasks.");
        } else {

            res.json(data);

        }
    });

    return next();
}

```

### <a name="add-error-handling-in-our-apis"></a>Добавление обработки ошибок в интерфейсы API
```

///--- Errors for communicating something interesting back to the client.

function MissingTaskError() {
    restify.RestError.call(this, {
        statusCode: 409,
        restCode: 'MissingTask',
        message: '"task" is a required parameter',
        constructorOpt: MissingTaskError
    });

    this.name = 'MissingTaskError';
}
util.inherits(MissingTaskError, restify.RestError);


function TaskExistsError(owner) {
    assert.string(owner, 'owner');

    restify.RestError.call(this, {
        statusCode: 409,
        restCode: 'TaskExists',
        message: owner + ' already exists',
        constructorOpt: TaskExistsError
    });

    this.name = 'TaskExistsError';
}
util.inherits(TaskExistsError, restify.RestError);


function TaskNotFoundError(owner) {
    assert.string(owner, 'owner');

    restify.RestError.call(this, {
        statusCode: 404,
        restCode: 'TaskNotFound',
        message: owner + ' was not found',
        constructorOpt: TaskNotFoundError
    });

    this.name = 'TaskNotFoundError';
}

util.inherits(TaskNotFoundError, restify.RestError);
```


## <a name="step-15-create-your-server"></a>Шаг 15. Создание сервера
Мы уже определили базу данных и создали все маршруты. Осталось только добавить экземпляр сервера, который обрабатывает вызовы.

В Restify (и в Express) реализованы разные возможности для настройки сервера REST API. Но в этом примере мы снова ограничимся самым простым вариантом настройки.

```Javascript
/**
 * Our server.
 */


var server = restify.createServer({
    name: "Azure Active Directroy TODO Server",
    version: "2.0.1"
});

// Ensure we don't drop data on uploads.
server.pre(restify.pre.pause());

// Clean up sloppy paths like //todo//////1//.
server.pre(restify.pre.sanitizePath());

// Handle annoying user agents (curl).
server.pre(restify.pre.userAgentConnection());

// Set a per request bunyan logger (with requestid filled in).
server.use(restify.requestLogger());

// Allow five requests per second by IP, and burst to 10.
server.use(restify.throttle({
    burst: 10,
    rate: 5,
    ip: true,
}));

// Use the common stuff you probably want.
server.use(restify.acceptParser(server.acceptable));
server.use(restify.dateParser());
server.use(restify.queryParser());
server.use(restify.gzipResponse());
server.use(restify.bodyParser({
    mapParams: true
})); // Allow for JSON mapping to REST.
```

## <a name="step-16-add-the-routes-to-the-server-without-authentication-for-now"></a>Шаг 16. Добавление маршрутов на сервер (пока без аутентификации)
```Javascript
/// Now the real handlers. Here we just CRUD.
/**
/*
/* Each of these handlers is protected by our OIDCBearerStrategy by invoking 'oidc-bearer'.
/* In the pasport.authenticate() method. We set 'session: false' because REST is stateless and
/* we don't need to maintain session state. You can experiment with removing API protection
/* by removing the passport.authenticate() method as follows:
/*
/* server.get('/tasks', listTasks);
/*
**/
server.get('/tasks', listTasks);
server.get('/tasks', listTasks);
server.get('/tasks/:owner', getTask);
server.head('/tasks/:owner', getTask);
server.post('/tasks/:owner/:task', createTask);
server.post('/tasks', createTask);
server.del('/tasks/:owner/:task', removeTask);
server.del('/tasks/:owner', removeTask);
server.del('/tasks', removeTask);
server.del('/tasks', removeAll, function respond(req, res, next) {
res.send(204);
next();
});
// Register a default '/' handler.
server.get('/', function root(req, res, next) {
var routes = [
'GET /',
'POST /tasks/:owner/:task',
'POST /tasks (for JSON body)',
'GET /tasks',
'PUT /tasks/:owner',
'GET /tasks/:owner',
'DELETE /tasks/:owner/:task'
];
res.send(200, routes);
next();
});
server.listen(serverPort, function() {
var consoleMessage = '\n Microsoft Azure Active Directory Tutorial';
consoleMessage += '\n +++++++++++++++++++++++++++++++++++++++++++++++++++++';
consoleMessage += '\n %s server is listening at %s';
consoleMessage += '\n Open your browser to %s/tasks\n';
consoleMessage += '+++++++++++++++++++++++++++++++++++++++++++++++++++++ \n';
consoleMessage += '\n !!! why not try a $curl -isS %s | json to get some ideas? \n';
consoleMessage += '+++++++++++++++++++++++++++++++++++++++++++++++++++++ \n\n';
});
```

## <a name="step-17-run-the-server-before-adding-oauth-support"></a>Шаг 17. Запуск сервера (пока без поддержки OAuth)
Прежде чем добавить аутентификацию, давайте протестируем сервер.

Проще всего это сделать с помощью curl из командной строки. Но прежде чем выполнять тест, нужно подготовить служебную программу, которая сможет анализировать выходные данные в формате JSON.

1. Установите средство для работы JSON, которое мы будем использовать во всех следующих примерах:

    `$npm install -g jsontool`

    Это обеспечивает глобальную установку средства JSON. Теперь все готово для экспериментов с сервером.

2. Сначала убедитесь, что запущен экземпляр MongoDB:

    `$sudo mongod`

3. Затем перейдите в другой каталог и запустите curl.

    `$ cd azuread`
    `$ node server.js`

    `$ curl -isS http://127.0.0.1:8080 | json`

    ```Shell
    HTTP/1.1 200 OK
    Connection: close
    Content-Type: application/json
    Content-Length: 171
    Date: Tue, 14 Jul 2015 05:43:38 GMT
    [
    "GET /",
    "POST /tasks/:owner/:task",
    "POST /tasks (for JSON body)",
    "GET /tasks",
    "PUT /tasks/:owner",
    "GET /tasks/:owner",
    "DELETE /tasks/:owner/:task"
    ]
    ```

4. Затем можно добавить задачу следующим образом:

    `$ curl -isS -X POST http://127.0.0.1:8080/tasks/brandon/Hello`

    Ответ должен быть следующим:

        ```Shell
        HTTP/1.1 201 Created
        Connection: close
        Access-Control-Allow-Origin: *
        Access-Control-Allow-Headers: X-Requested-With
        Content-Type: application/x-www-form-urlencoded
        Content-Length: 5
        Date: Tue, 04 Feb 2014 01:02:26 GMT
        Hello
        ```
    Для перечисления задач для Brandon можно использовать следующий способ:

        `$ curl -isS http://127.0.0.1:8080/tasks/brandon/`

Если все это работает, можно добавить OAuth к нашему серверу REST API.

У вас уже есть сервер REST API с MongoDB!

## <a name="step-18-add-authentication-to-our-rest-api-server"></a>Шаг 18. Подключение аутентификации для сервера REST API
Теперь, когда у нас запущен сервер REST API, мы можем использовать его для работы с Azure AD.

В окне командной строки перейдите в каталог **azuread** или убедитесь, что вы уже находитесь в нем.

`cd azuread`

### <a name="use-the-oidcbearerstrategy-that-is-included-with-passport-azure-ad"></a>Использование стратегии OIDCBearerStrategy, включенной в passport-azure-ad
Мы создали стандартный сервер REST TODO без какой-либо авторизации. Самое время добавить такую функцию.

1. Сначала необходимо указать, что будет использоваться Passport. Сделайте это сразу после конфигурации другого сервера:

    ```Javascript
            // Let's start using Passport.js.

            server.use(passport.initialize()); // Starts passport.
            server.use(passport.session()); // Provides session support.
    ```
    > [!TIP]
    > При написании API-интерфейсов старайтесь связывать данные с уникальными параметрами маркера, которые пользователь не сможет подделать. Когда наш сервер сохраняет элементы списка дел, он использует идентификатор объекта, используемый в маркере для пользователя (он хранится в свойстве token.oid), и помещает его в поле owner. Благодаря этому только владелец сможет получить доступ к своим элементам списка дел. Значение owner не отображается в API, поэтому внешний пользователь может запросить элементы списка дел другого пользователя, даже если он прошел проверку подлинности.                    

2. Теперь мы применим стратегию носителя, которая поставляется вместе с `passport-azure-ad`. Просмотрите код, а остальное мы объясним чуть ниже. Вставьте этот текст после добавленного ранее:

```Javascript
    /**
    /*
    /* Calling the OIDCBearerStrategy and managing users.
    /*
    /* Passport pattern provides the need to manage users and info tokens
    /* with a FindorCreate() method that must be provided by the implementor.
    /* Here we just auto-register any user and implement a FindById().
    /* You'll want to do something smarter.
    **/

    var findById = function(id, fn) {
        for (var i = 0, len = users.length; i < len; i++) {
            var user = users[i];
            if (user.sub === id) {
                log.info('Found user: ', user);
                return fn(null, user);
            }
        }
        return fn(null, null);
    };


    var bearerStrategy = new BearerStrategy(options,
        function(token, done) {
            log.info('verifying the user');
            log.info(token, 'was the token retreived');
            findById(token.sub, function(err, user) {
                if (err) {
                    return done(err);
                }
                if (!user) {
                    // "Auto-registration"
                    log.info('User was added automatically as they were new. Their sub is: ', token.sub);
                    users.push(token);
                    owner = token.sub;
                    return done(null, token);
                }
                owner = token.sub;
                return done(null, user, token);
            });
        }
    );

    passport.use(bearerStrategy);
```

Для Passport свойственно аналогичное поведение для всех стратегий (Twitter, Facebook и т. д.); это поведение учитывают все разработчики модулей стратегий. Просматривая стратегию, можно увидеть, что она принимает функцию с параметрами token и done. Стратегия будет возвращена, когда завершит свою работу. После этого нам нужно сохранить пользователя и сохранить маркер, чтобы не запрашивать его каждый раз заново.

> [!IMPORTANT]
> Приведенный выше код обрабатывает данные о любом пользователе, который выполняет аутентификацию на сервере. Это называется автоматической регистрацией. На рабочих серверах мы не рекомендуем разрешать вход без прохождения определенного процесса регистрации, который вы сочтете приемлемым. Это типично для клиентских приложений, в которых разрешается регистрация через Facebook, но затем следуют запросы на предоставление дополнительной информации. Если бы мы создавали обычную программу, а не простое средство для командной строки, мы могли бы извлечь адрес электронной почты из полученного объекта маркера и затем запросить у пользователя дополнительную информацию. Так как это всего лишь тестовый сервер, мы просто добавим пользователей в базу данных в памяти.
>
>

### <a name="protect-some-endpoints"></a>Защита некоторых конечных точек
Для защиты конечных точек следует указать нужный протокол в вызове функции `passport.authenticate()`.

Можно изменить маршрут в коде сервера, чтобы сделать нечто более интересное.

```Javascript
server.get('/tasks', passport.authenticate('oauth-bearer', {
session: false
}), listTasks);
server.get('/tasks', passport.authenticate('oauth-bearer', {
session: false
}), listTasks);
server.get('/tasks/:owner', passport.authenticate('oauth-bearer', {
session: false
}), getTask);
server.head('/tasks/:owner', passport.authenticate('oauth-bearer', {
session: false
}), getTask);
server.post('/tasks/:owner/:task', passport.authenticate('oauth-bearer', {
session: false
}), createTask);
server.post('/tasks', passport.authenticate('oauth-bearer', {
session: false
}), createTask);
server.del('/tasks/:owner/:task', passport.authenticate('oauth-bearer', {
session: false
}), removeTask);
server.del('/tasks/:owner', passport.authenticate('oauth-bearer', {
session: false
}), removeTask);
server.del('/tasks', passport.authenticate('oauth-bearer', {
session: false
}), removeTask);
server.del('/tasks', passport.authenticate('oauth-bearer', {
session: false
}), removeAll, function respond(req, res, next) {
res.send(204);
next();
});
```

## <a name="step-19-run-your-server-application-again-and-ensure-it-rejects-you"></a>Шаг 19. Снова запустите свой сервер приложения и убедитесь, что он вас отклоняет.
Снова используем `curl`, чтобы определить, если у нас сейчас защита OAuth2 применительно к нашим конечным точкам. Этот тест мы выполним до того, как запускать клиентские пакеты SDK для этой конечной точки. Возвращенные заголовки должны подтвердить, что мы все сделали правильно.

1. Сначала убедитесь, что запущен экземпляр MongoDB:

    `$sudo mongod`

2. Затем перейдите в рабочий каталог и запустите curl.

      `$ cd azuread`
      `$ node server.js`

3. Попробуйте выполнить простую команду POST.

    `$ curl -isS -X POST http://127.0.0.1:8080/tasks/brandon/Hello`

    ```Shell
    HTTP/1.1 401 Unauthorized
    Connection: close
    WWW-Authenticate: Bearer realm="Users"
    Date: Tue, 14 Jul 2015 05:45:03 GMT
    Transfer-Encoding: chunked
    ```

Здесь мы хотим увидеть именно ответ 401. Он указывает, что уровень Passport пытается перенаправить нас к конечной точке авторизации. Это именно то, что нам нужно.

## <a name="next-steps"></a>Дальнейшие действия
Мы уже сделали с этим сервером все, что можно реализовать без использования клиента OAuth2. Теперь вам будет нужно поработать с дополнительным пошаговым руководством.

Теперь вы знаете, как реализовать интерфейс API REST с использованием Restify и OAuth2. У вас есть код, на основе которого можно продолжить разработку и освоить новые методы.

Если вы намерены более подробно изучать ADAL, мы рекомендуем использовать поддерживаемые клиенты ADAL, которые указаны в списке ниже.

Клонируйте их на своем компьютере разработчика и настройте, как описано в этом пошаговом руководстве.

[ADAL для iOS](https://github.com/MSOpenTech/azure-activedirectory-library-for-ios)

[ADAL для Android](https://github.com/MSOpenTech/azure-activedirectory-library-for-android)

[!INCLUDE [active-directory-devquickstarts-additional-resources](../../../includes/active-directory-devquickstarts-additional-resources.md)]


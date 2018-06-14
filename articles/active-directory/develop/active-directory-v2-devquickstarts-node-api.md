---
title: Защита веб-API Azure Active Directory v2.0 с помощью Node.js | Документы Майкрософт
description: Узнайте, как создать веб-API .NET Node.js, принимающий маркеры доступа личных учетных записей Майкрософт, а также рабочих и учебных учетных записей.
services: active-directory
documentationcenter: nodejs
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 0b572fc1-2aaf-4cb6-82de-63010fb1941d
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: javascript
ms.topic: article
ms.date: 05/13/2017
ms.author: celested
ms.reviewer: nacanuma
ms.custom: aaddev
ms.openlocfilehash: 19ad25c7b08ff073097cacf3be359772ca0a327f
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/14/2018
ms.locfileid: "34158376"
---
# <a name="secure-a-web-api-by-using-nodejs"></a>Защита веб-API с помощью Node.js
> [!NOTE]
> Не все сценарии и компоненты Azure Active Directory поддерживаются конечной точкой версии 2.0. Чтобы определить, стоит ли вам использовать конечную точку версии 2.0 или 1.0, ознакомьтесь с [ограничениями версии 2.0](active-directory-v2-limitations.md).
> 
> 

При использовании конечной точки Azure Active Directory (Azure AD) версии 2.0 можно использовать маркеры доступа [OAuth 2.0](active-directory-v2-protocols.md) для защиты веб-API. С помощью маркеров доступа OAuth 2.0 пользователи с личными учетными записями Майкрософт и рабочими или учебными учетными записями могут безопасно обращаться в вашему веб-API.

*Passport* — промежуточный слой проверки подлинности для Node.js. Он отличается гибкой модульной структурой, которая позволяет сравнительно незаметно размещать его в любом приложении на основе Express или веб-приложении Restify. В Passport полный набор стратегий поддерживает процесс проверки подлинности с помощью имени пользователя и пароля, Facebook, Twitter и проч. Мы разработали стратегию для Azure AD. В этой статье описывается установка модуля и последующее добавление подключаемого модуля `passport-azure-ad` Azure AD.

## <a name="download"></a>Загрузка
Код в этом учебнике размещен на портале [GitHub](https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-nodejs). Для выполнения действий в этом руководстве вы можете [скачать заготовку приложения как ZIP-файл](https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-nodejs/archive/skeleton.zip) или клонировать структуру:

```git clone --branch skeleton https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-nodejs.git```

Можно также воспользоваться готовым приложением в конце этого руководства.

## <a name="1-register-an-app"></a>Шаг 1. Регистрация приложения
Создайте приложение на странице [apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) или выполните [эти подробные указания](active-directory-v2-app-registration.md), чтобы зарегистрировать приложение. Не забудьте выполнить следующие действия.

* Скопируйте **идентификатор приложения**, назначенный приложению. Он потребуется для работы с этим руководством.
* Добавьте для приложения **мобильную** платформу.
* Скопируйте значение **URI перенаправления** с портала. Необходимо использовать значение URI по умолчанию — `urn:ietf:wg:oauth:2.0:oob`.

## <a name="2-install-nodejs"></a>Шаг 2. Установка Node.js
Чтобы использовать пример для этого руководства, необходимо [установить Node.js](http://nodejs.org).

## <a name="3-install-mongodb"></a>Шаг 3. Установка MongoDB
Чтобы успешно использовать этот пример, необходимо [установить MongoDB](http://www.mongodb.org). В этом примере MongoDB используется, чтобы интерфейс REST API устойчиво работал с несколькими экземплярами сервера.

> [!NOTE]
> В этой статье предполагается, что вы используете установку по умолчанию и конечные точки сервера для MongoDB: mongodb://localhost.
> 
> 

## <a name="4-install-the-restify-modules-in-your-web-api"></a>Шаг 4. Установка модулей Restify для веб-API
Мы используем Resitfy для построения интерфейса REST API. Restify — это минималистичная и гибкая платформа для приложений Node.j, созданная на основе Express. Restify имеет широкий набор функций, которые можно использовать для создания интерфейсов REST API поверх Connect.

### <a name="install-restify"></a>Установка Restify
1.  В командной строке смените каталог на **azuread**.

    `cd azuread`

    Если каталог **azuread** не существует, создайте его.

    `mkdir azuread`

2.  Установите Restify.

    `npm install restify`

    Выходные данные этой команды должны выглядеть следующим образом:

    ```
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
    └── bunyan@0.22.0(mv@0.0.5)
    ```

#### <a name="did-you-get-an-error"></a>Вы получили сообщение об ошибке?
В некоторых операционных системах при использовании команды `npm` может появиться сообщение: `Error: EPERM, chmod '/usr/local/bin/..'`. Оно сопровождается запросом на использование учетной записи с правами администратора. В этом случае необходимо с помощью команды `sudo` запустить `npm` с более высоким уровнем привилегий.

#### <a name="did-you-get-an-error-related-to-dtrace"></a>Вы получили сообщение об ошибке, которая относится к DTrace?
При установке Restify может появиться следующее сообщение:

```Shell
clang: error: no such file or directory: 'HD/azuread/node_modules/restify/node_modules/dtrace-provider/libusdt'
make: *** [Release/DTraceProviderBindings.node] Error 1
gyp ERR! build error
gyp ERR! stack Error: `make` failed with exit code: two
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

Restify предоставляет мощный механизм для трассировки вызовов REST с помощью DTrace. Однако во многих операционных системах DTrace отсутствует. Это сообщение об ошибке можно игнорировать.


## <a name="5-install-passportjs-in-your-web-api"></a>Шаг 5. Установка Passport.js для веб-интерфейса API
1.  В командной строке смените каталог на **azuread**.

2.  Установите Passport.js.

    `npm install passport`

    Выходные данные этой команды должны выглядеть следующим образом:

    ```
     passport@0.1.17 node_modules\passport
    ├── pause@0.0.1
    └── pkginfo@0.2.3
    ```

## <a name="6-add-passport-azure-ad-to-your-web-api"></a>Шаг 6. Добавление Passport-Azure-AD в веб-API
Затем добавьте стратегию OAuth с помощью passport-azuread. `passport-azuread` — это набор стратегий, который устанавливает подключение Azure AD к Passport. В этом примере REST API мы будем использовать эту стратегию для токенов носителя.

> [!NOTE]
> Платформа OAuth 2.0 позволяет выдавать маркеры любого известного типа, но в большинстве случаев используются маркеры только нескольких типов. Маркер-носитель обычно используется для защиты конечных точек. Это самый распространенный тип выдаваемых маркеров в OAuth 2.0. Во многих реализациях OAuth 2.0 предполагается, что маркер-носитель — это единственный тип выданного токена.
> 
> 

1.  В командной строке смените каталог на **azuread**.

    `cd azuread`

2.  Установите модуль Passport.js `passport-azure-ad`.

    `npm install passport-azure-ad`

    Выходные данные этой команды должны выглядеть следующим образом:

    ```
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
    ```

## <a name="7-add-mongodb-modules-to-your-web-api"></a>Шаг 7. Добавление модулей MongoDB в веб-интерфейс API
В этом примере мы используем MongoDB в качестве хранилища данных. 

1.  Установите Mongoose, широко используемый подключаемый модуль для управления моделями и схемами. 

    `npm install mongoose`

2.  Установите драйвер базы данных для MongoDB (который также называется MongoDB).

    `npm install mongodb`

## <a name="8-install-additional-modules"></a>Шаг 8. Установка дополнительных модулей
Установите остальные необходимые модули.

1.  В командной строке смените каталог на **azuread**.

    `cd azuread`

2.  Введите следующие команды. Они используются для установки следующих модулей в каталоге node_modules:

    *   `npm install crypto`
    *   `npm install assert-plus`
    *   `npm install posix-getopt`
    *   `npm install util`
    *   `npm install path`
    *   `npm install connect`
    *   `npm install xml-crypto`
    *   `npm install xml2js`
    *   `npm install xmldom`
    *   `npm install async`
    *   `npm install request`
    *   `npm install underscore`
    *   `npm install grunt-contrib-jshint@0.1.1`
    *   `npm install grunt-contrib-nodeunit@0.1.2`
    *   `npm install grunt-contrib-watch@0.2.0`
    *   `npm install grunt@0.4.1`
    *   `npm install xtend@2.0.3`
    *   `npm install bunyan`
    *   `npm update`

## <a name="9-create-a-serverjs-file-for-your-dependencies"></a>Шаг 9. Создание файла Server.js для зависимостей
Основная часть функций для сервера веб-API реализована в файле Server.js. Добавьте в этот файл основную часть своего кода. В производственных целях можно разделить функционал на небольшие файлы, такие как отдельные маршруты и контроллеры. В этой статье мы используем Server.js.

1.  В командной строке смените каталог на **azuread**.

    `cd azuread`

2.  С помощью редактора по своему усмотрению создайте файл server.js. Добавьте в файл следующие данные:

    ```Javascript
    'use strict';
    /**
    * Module dependencies.
    */
    var util = require('util');
    var assert = require('assert-plus');
    var mongoose = require('mongoose/');
    var bunyan = require('bunyan');
    var restify = require('restify');
    var config = require('./config');
    var passport = require('passport');
    var OIDCBearerStrategy = require('passport-azure-ad').OIDCStrategy;
    ```

3.  Сохраните файл. Вы вернетесь к нему позже.

## <a name="10-create-a-config-file-to-store-your-azure-ad-settings"></a>Шаг 10. Создание файла конфигурации для сохранения параметров Azure AD
Данный фрагмент кода передает параметры конфигурации с вашего портала Azure AD в файл Passport.js. Эти значения конфигурации были созданы, когда вы добавляли веб-API на портал, выполняя первую часть этого руководства. Мы объясним, какие значения нужно указать для этих параметров в скопированном фрагменте кода.

1.  В командной строке смените каталог на **azuread**.

    `cd azuread`

2.  В редакторе создайте файл Config.js. Добавьте следующие данные:

    ```Javascript
    // Don't commit this file to your public repos. This config is for first-run.
    exports.creds = {
    mongoose_auth_local: 'mongodb://localhost/tasklist', // Your Mongo auth URI goes here.
    issuer: 'https://sts.windows.net/**<your application id>**/',
    audience: '<your redirect URI>',
    identityMetadata: 'https://login.microsoftonline.com/common/.well-known/openid-configuration' // For Microsoft, you should never need to change this.
    };

    ```



### <a name="required-values"></a>Обязательные значения

*   **IdentityMetadata**. Здесь модуль `passport-azure-ad` будет искать данные конфигурации для поставщика удостоверений (IDP), а также ключи для проверки маркеров JWT. Если вы используете Azure AD, возможно, эти данные изменять не нужно.

*   **audience**— это универсальный код ресурса (URI) перенаправления с портала.

> [!NOTE]
> Рекомендуется часто менять ключи. Убедитесь, что вы всегда извлекаете данные из URL-адреса openid_keys и что приложение имеет доступ к Интернету.
> 
> 

## <a name="11-add-the-configuration-to-your-serverjs-file"></a>Шаг 11. Добавление конфигурации в файл Server.js
Приложение должно считывать значения из только что созданного файла конфигурации. Добавьте в приложение файл с расширением .config в качестве требуемого ресурса. Задайте глобальным переменным значения из файла Config.js.

1.  В командной строке смените каталог на **azuread**.

    `cd azuread`

2.  В редакторе откройте файл Server.js. Добавьте следующие данные:

    ```Javascript
    var config = require('./config');
    ```

3.  Добавьте новый раздел в Server.js.

    ```Javascript
    // Pass these options in to the ODICBearerStrategy.
    var options = {
    // The URL of the metadata document for your app. Put the keys for token validation from the URL found in the jwks_uri tag in the metadata.
    identityMetadata: config.creds.identityMetadata,
    issuer: config.creds.issuer,
    audience: config.creds.audience
    };
    // Array to hold signed-in users and the current signed-in user (owner).
    var users = [];
    var owner = null;
    // Your logger
    var log = bunyan.createLogger({
    name: 'Microsoft Azure Active Directory Sample'
    });
    ```

## <a name="12-add-the-mongodb-model-and-schema-information-by-using-mongoose"></a>Шаг 12. Добавление сведений о модели и схеме MongoDB с помощью Mongoose
Затем подключите эти три файла в службе REST API.

В этой статье мы используем MongoDB для хранения наших задач. Этот вопрос обсуждается на *шаге 4*.

В файле Config.js, созданном на шаге 11, база данных называется *tasklist*. Это вы указали в конце URL-адреса подключения mongoose_auth_local. Не нужно заранее создавать эту базу данных в MongoDB. База данных создается при первом запуске серверного приложения (при условии, что база данных еще не существует).

Вы указали серверу, какую базу данных MongoDB следует использовать. Далее необходимо написать дополнительный код для создания модели и схемы для задач сервера.

### <a name="the-model"></a>Модель
Модель схемы очень проста. При необходимости ее можно расширить. 

Модель схемы имеет следующие значения:

*   **Имя**. Кому назначено это задание. Это **строковое** значение.
*   **Задача**. Имя задачи. Это **строковое** значение.
*   **Дата**. Дата ожидаемого выполнения задачи. Это значение **datetime**.
*   **Завершено**. Статус завершения задачи. Это **логическое** значение.

### <a name="create-the-schema-in-the-code"></a>Создание схемы в коде
1.  В командной строке смените каталог на **azuread**.

    `cd azuread`

2.  В редакторе откройте файл Server.js. Под записью конфигурации добавьте следующие сведения:

    ```Javascript
    // MongoDB setup.
    // Set up some configuration.
    var serverPort = process.env.PORT || 8080;
    var serverURI = (process.env.PORT) ? config.creds.mongoose_auth_mongohq : config.creds.mongoose_auth_local;
    // Connect to MongoDB.
    global.db = mongoose.connect(serverURI);
    var Schema = mongoose.Schema;
    log.info('MongoDB Schema loaded');
    ```

Этот код подключается к серверу MongoDB. Он также возвращает объект схемы.

#### <a name="using-the-schema-create-your-model-in-the-code"></a>С помощью схемы создайте модель в коде
После предыдущего кода добавьте следующий код:

```Javascript
// Create a basic schema to store your tasks and users.
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

Как видно из кода, сначала вы создаете схему данных. Затем создайте объект модели. Используйте объект модели для хранения данных в коде при определении **маршрутов**.

## <a name="13-add-your-routes-for-your-task-rest-api-server"></a>Шаг 13. Добавление маршрутов для сервера REST API задачи
Теперь модель базы данных готова к использованию. Добавьте маршруты, которые будут использоваться для сервера REST API.

### <a name="about-routes-in-restify"></a>О маршрутах в Restify
Маршруты в Restify действуют точно так же, как и при использовании стека Express. Вы определяете маршруты с помощью идентификатора URI, который, как предполагается, будут вызывать клиентские приложения. Как правило, свои маршруты вы определяете в отдельном файле. В этом руководстве мы помещаем маршруты в файл Server.js. Для систем в рабочей среде мы рекомендуем вынести их в отдельный файл.

Типичный шаблон для маршрута Restify выглядит следующим образом:

```Javascript
function createObject(req, res, next) {
// Do work on object.
_object.name = req.params.object; // Passed value is in req.params under object.
///...
return next(); // Keep the server going.
}
....
server.post('/service/:add/:object', createObject); // calls createObject on routes that match this.
```


Это шаблон на самом базовом уровне. В Restify (как и в Express) доступны очень мощные функциональные возможности, например для определения типов приложений и выполнения сложной маршрутизации между несколькими конечными точками.

#### <a name="add-default-routes-to-your-server"></a>Добавление на сервер маршрутов по умолчанию
Добавьте маршруты для базовых операций CRUD (**создание**, **извлечение**, **обновление** и **удаление** данных).

1.  В командной строке смените каталог на **azuread**.

    `cd azuread`

2.  В редакторе откройте файл Server.js. Добавьте следующие сведения под записями о базе данных, которые вы добавили ранее:

    ```Javascript
    /**
    *
    * APIs for your REST task server
    */
    // Create a task.
    function createTask(req, res, next) {
    // Resitify currently has a bug that doesn't allow you to set default headers.
    // These headers comply with CORS, and allow you to use MongoDB Server as your response to any origin.
    res.header("Access-Control-Allow-Origin", "*");
    res.header("Access-Control-Allow-Headers", "X-Requested-With");
    // Create a new task model, fill it, and save it to MongoDB.
    var _task = new Task();
    if (!req.params.task) {
    req.log.warn({
    params: p
    }, 'createTodo: missing task');
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
    /// Returns the list of TODOs that were loaded.
    function listTasks(req, res, next) {
    // Resitify currently has a bug that doesn't allow you to set default headers.
    // These headers comply with CORS, and allow us to use MongoDB Server as our response to any origin.
    res.header("Access-Control-Allow-Origin", "*");
    res.header("Access-Control-Allow-Headers", "X-Requested-With");
    log.info("listTasks was called for: ", owner);
    Task.find({
    owner: owner
    }).limit(20).sort('date').exec(function(err, data) {
    if (err)
    return next(err);
    if (data.length > 0) {
    log.info(data);
    }
    if (!data.length) {
    log.warn(err, "There are no tasks in the database. Add one!");
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

### <a name="add-error-handling-for-the-routes"></a>Добавление обработки ошибок для маршрутов
Добавьте процедуру для обработки ошибок, чтобы сообщать клиенту о возникающих проблемах.

Добавьте следующий код после кода, который вы уже написали:

```Javascript
///--- Errors for communicating something more information back to the client.
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


## <a name="14-create-your-server"></a>Шаг 14. Создание сервера
Осталось только добавить экземпляр сервера. Экземпляр сервера управляет вызовами.

Restify (и Express) предлагают эффективные возможности настройки, которые можно использовать на сервере REST API. В этом руководстве используется базовая процедура настройки.

```Javascript
/**
* Your server
*/
var server = restify.createServer({
name: "Microsoft Azure Active Directory TODO Server",
version: "2.0.1"
});
// Ensure that you don't drop data on uploads.
server.pre(restify.pre.pause());
// Clean up imprecise paths like //todo//////1//.
server.pre(restify.pre.sanitizePath());
// Handle annoying user agents (curl).
server.pre(restify.pre.userAgentConnection());
// Set a per-request Bunyan logger (with requestid filled in).
server.use(restify.requestLogger());
// Allow 5 requests/second by IP address, and burst to 10.
server.use(restify.throttle({
burst: 10,
rate: 5,
ip: true,
}));
// Use common commands, such as:
server.use(restify.acceptParser(server.acceptable));
server.use(restify.dateParser());
server.use(restify.queryParser());
server.use(restify.gzipResponse());
server.use(restify.bodyParser({
mapParams: true
}));
```
## <a name="15-add-the-routes-without-authentication-for-now"></a>Шаг 15. Добавление маршрутов (в настоящее время без аутентификации)
```Javascript
/// Use CRUD to add the real handlers.
/**
/*
/* Each of these handlers is protected by your Open ID Connect Bearer strategy. Invoke 'oidc-bearer'
/* in the pasport.authenticate() method. Because REST is stateless, set 'session: false'. You 
/* don't need to maintain session state. You can experiment with removing API protection.
/* To do this, remove the passport.authenticate() method:
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
// Register a default '/' handler
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
## <a name="16-run-the-server"></a>Шаг 16. Запуск сервера
Перед добавлением функции проверки подлинности рекомендуется проверить сервер.

Проще всего это сделать с помощью curl из командной строки. Для этого требуется простая служебная программа, которую можно использовать для анализа выходных данных в формате JSON. 

1.  Установите средство JSON, которое используется в следующих примерах:

    `$npm install -g jsontool`

    Это обеспечивает глобальную установку средства JSON.

2.  Убедитесь, что ваш экземпляр MongoDB работает.

    `$sudo mongod`

3.  Измените каталог на **azuread** и запустите curl:

    `$ cd azuread`
    `$ node server.js`

    `$ curl -isS http://127.0.0.1:8080 | json`

    ```Shell
    HTTP/1.1 2.0OK
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

4.  Добавьте задачу:

    `$ curl -isS -X POST http://127.0.0.1:8888/tasks/brandon/Hello`

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

5.  Перечисление задач для Brandon:

    `$ curl -isS http://127.0.0.1:8080/tasks/brandon/`

Если все эти команды выполняются без ошибок, можно приступать к добавлению OAuth на сервер REST API.

*Теперь у вас есть сервер REST API с MongoDB!*

## <a name="17-add-authentication-to-your-rest-api-server"></a>Шаг 17. Добавление функции проверки подлинности на сервер REST API
Теперь, когда у вас есть работающий REST API, настройте его для использования с Azure AD.

В командной строке смените каталог на **azuread**.

`cd azuread`

### <a name="use-the-oidcbearerstrategy-thats-included-with-passport-azure-ad"></a>Использование стратегии OIDCBearerStrategy, включенной в passport-azure-ad
На данный момент вы создали стандартный сервер REST TODO без какой-либо авторизации. Теперь добавьте проверку подлинности.

Сначала укажите, что вы хотите использовать Passport. Сделайте это сразу после конфигурации сервера.

```Javascript
// Start using Passport.js.

server.use(passport.initialize()); // Starts passport
server.use(passport.session()); // Provides session support
```

> [!TIP]
> При написании интерфейсов API рекомендуется всегда связывать данные с уникальными параметрами маркера, которые пользователь не сможет подделать. Когда сервер сохраняет элементы списка дел (TODO), он делает это на основании идентификатора подписки пользователя в маркере (вызываемого с помощью token.sub). token.sub указывается в поле owner (владелец). Благодаря этому только владелец сможет получить доступ к своим элементам списка дел (TODO). Никто другой не может получить доступ к элементам списка дел (TODO), которые были введены. В API владелец не раскрывается. Внешний пользователь может запросить элементы списка дел (TODO) других пользователей, даже если они прошли проверку подлинности.
> 
> 

Теперь используйте стратегию Open ID Connect Bearer, включенную в состав `passport-azure-ad`. Вставьте это после добавленного ранее:

```Javascript
/**
/*
/* Calling the OIDCBearerStrategy and managing users.
/*
/* Because of the Passport pattern, you need to manage users and info tokens
/* with a FindorCreate() method. The method must be provided by the implementor.
/* In the following code, you autoregister any user and implement a FindById().
/* It's a good idea to do something more advanced.
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
var oidcStrategy = new OIDCBearerStrategy(options,
function(token, done) {
log.info('verifying the user');
log.info(token, 'was the token retrieved');
findById(token.sub, function(err, user) {
if (err) {
return done(err);
}
if (!user) {
// "Auto-registration"
log.info('User was added automatically, because they were new. Their sub is: ', token.sub);
users.push(token);
owner = token.sub;
return done(null, token);
}
owner = token.sub;
return done(null, user, token);
});
}
);
passport.use(oidcStrategy);
```

Для Passport свойственно аналогичное поведение для всех стратегий (Twitter, Facebook и т. д.). Все авторы стратегии придерживаются этого шаблона. Передайте стратегию `function()`, которая использует маркер и значение `done` в качестве параметров. Стратегия возвращается после того, как выполнит всю свою работу. Сохраните данные пользователя и маркер, чтобы не задавать их в следующий раз повторно.

> [!IMPORTANT]
> Приведенный выше код принимает любого пользователя, который может пройти проверку подлинности на сервере. Это называется автоматической регистрацией. На рабочих серверах не нужно разрешать другим пользователям входить без выбранной вами регистрации. Это обычное поведение в приложениях для потребителей. Приложение можно зарегистрировать в Facebook, но потребуется ввести дополнительные сведения. Если в этом руководстве вы не использовали программу командной строки, сообщение электронной почты можно извлечь из возвращаемого объекта маркера. Затем вы можете попросить пользователя ввести дополнительные сведения. Так как это всего лишь тестовый сервер, просто добавьте пользователей в базу данных в памяти.
> 
> 

### <a name="protect-endpoints"></a>Защита конечных точек
Для защиты конечных точек необходимо указать вызов **passport.authenticate()** с протоколом, который вы хотите использовать.

Вы можете изменить маршрут в коде сервера для более продвинутого использования:

```Javascript
server.get('/tasks', passport.authenticate('oidc-bearer', {
session: false
}), listTasks);
server.get('/tasks', passport.authenticate('oidc-bearer', {
session: false
}), listTasks);
server.get('/tasks/:owner', passport.authenticate('oidc-bearer', {
session: false
}), getTask);
server.head('/tasks/:owner', passport.authenticate('oidc-bearer', {
session: false
}), getTask);
server.post('/tasks/:owner/:task', passport.authenticate('oidc-bearer', {
session: false
}), createTask);
server.post('/tasks', passport.authenticate('oidc-bearer', {
session: false
}), createTask);
server.del('/tasks/:owner/:task', passport.authenticate('oidc-bearer', {
session: false
}), removeTask);
server.del('/tasks/:owner', passport.authenticate('oidc-bearer', {
session: false
}), removeTask);
server.del('/tasks', passport.authenticate('oidc-bearer', {
session: false
}), removeTask);
server.del('/tasks', passport.authenticate('oidc-bearer', {
session: false
}), removeAll, function respond(req, res, next) {
res.send(204);
next();
});
```

## <a name="18-run-your-server-application-again"></a>Шаг 18. Повторный запуск серверного приложения
Снова воспользуйтесь curl, чтобы определить, активирована ли защита OAuth 2.0 применительно к конечным точкам. Сделайте это до запуска любого клиентского пакета SDK для этой конечной точки. Возвращаемые заголовки должны содержать сведения о правильной работе функции проверки подлинности.

1.  Убедитесь, что ваш экземпляр MongoDB работает.

    `$sudo mongod`

2.  Измените каталог на **azuread**, а затем используйте curl:

    `$ cd azuread`

    `$ node server.js`

3.  Попробуйте выполнить базовую команду POST:

    `$ curl -isS -X POST http://127.0.0.1:8080/tasks/brandon/Hello`

    ```Shell
    HTTP/1.1 401 Unauthorized
    Connection: close
    WWW-Authenticate: Bearer realm="Users"
    Date: Tue, 14 Jul 2015 05:45:03 GMT
    Transfer-Encoding: chunked
    ```

Ответ 401 указывает, что уровень Passport пытается перенаправить нас к конечной точке авторизации. Это именно то, что нам нужно.

*Теперь у вас есть служба REST API, использующая OAuth 2.0!*

Вы уже сделали с этим сервером все, что можно реализовать без использования клиента, совместимого с OAuth 2.0. Для работы с клиентом необходимо изучить дополнительное руководство.

## <a name="next-steps"></a>Дополнительная информация
Полный пример (без ваших значений конфигурации) можно загрузить в виде [ZIP-архива](https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-nodejs/archive/complete.zip). Кроме того, его можно клонировать из GitHub:

```git clone --branch complete https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-nodejs.git```

Теперь можно перейти к более сложным темам. Вы можете попытаться [защитить веб-приложение Node.js с помощью конечной точки версии 2.0](active-directory-v2-devquickstarts-node-web.md).

Ниже приведены некоторые дополнительные ресурсы.

* [Вход для пользователей учетных записей Майкрософт и Azure AD в одном приложении](active-directory-appmodel-v2-overview.md)
* [Тег StackOverflow "azure-active-directory"](http://stackoverflow.com/questions/tagged/azure-active-directory)

### <a name="get-security-updates-for-our-products"></a>Получение обновлений системы безопасности для наших продуктов
Мы рекомендуем вам зарегистрироваться для получения уведомлений о нарушениях безопасности. Это можно сделать, подписавшись на уведомления безопасности консультационных служб на странице [технического центра безопасности](https://technet.microsoft.com/security/dd252948).


<properties
	pageTitle="Приступая к работе с Azure AD NodeJS | Microsoft Azure"
	description="Практическое руководство по созданию веб-интерфейса REST API с помощью Node.js, который интегрируется с Azure AD для проверки подлинности."
	services="active-directory"
	documentationCenter="nodejs"
	authors="brandwe"
	manager="mbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="javascript"
	ms.topic="article"
	ms.date="09/16/2016"
	ms.author="brandwe"/>

# Приступая к работе с веб-интерфейсом API для узла

[AZURE.INCLUDE [active-directory-devguide](../../includes/active-directory-devguide.md)]

**Passport** — промежуточный слой проверки подлинности для Node.js. Он имеет очень гибкую и модульную структуру, которая позволяет сравнительно незаметно размещать данный слой в любом приложении на основе Express или в веб-приложении Resitify. Полный набор стратегий поддерживает процесс проверки подлинности с помощью имени пользователя и пароля, Facebook, Twitter и проч. Мы разработали стратегию для Microsoft Azure Active Directory. Мы установим этот модуль, а затем добавим подключаемый модуль Microsoft Azure Active Directory `passport-azure-ad`.

Для этого вам необходимо:

1. зарегистрировать приложение в Azure AD;
2. Настроить приложение для использования подключаемого модуля azure-ad-passport Passport.
3. Настроить клиентское приложение для вызова методов веб-API приложения To Do List.

Код в этом учебнике размещен на портале [GitHub](https://github.com/Azure-Samples/active-directory-node-webapi).

> [AZURE.NOTE] В этой статье не рассматривается реализация входа, регистрации и управления профилями с помощью Azure AD B2C. Она посвящена вызову веб-API после того, как пользователь прошел проверку подлинности. Если вы еще этого не сделали, сначала ознакомьтесь с документом [Интеграция с Azure Active Directory](active-directory-how-to-integrate.md), в котором приводятся основные сведения об Azure Active Directory.


Мы выпустили полный исходный код для этого рабочего примера в GitHub на условиях лицензии MIT, поэтому вы можете спокойно клонировать (или даже разветвлять) его, а также отправлять отзывы и запросы на включение внесенных изменений.

## О модулях Node.js

В настоящем пошаговом руководстве мы будем использовать модули Node.js. Модули — это загружаемые пакеты JavaScript, дающую приложению определенную функциональность. Обычно вы устанавливаете модули с помощью средства командной строки Node.js NPM в каталоге установки NPM, но некоторые модули, например модуль HTTP, содержатся в основном пакете Node.js. Установленные модули сохраняются в каталоге node\_modules в корневом каталоге установки Node.js. В каждом модуле в каталоге node\_modules содержится свой каталог node\_modules, в котором находятся модули-зависимости, и в каждом требуемом модуле имеется каталог node\_modules. Такая рекурсивная структура каталога представляет цепочку зависимостей.

Структура цепочки зависимостей приводит к увеличению объема приложения, но гарантирует, что все зависимости учтены и что версия модулей, используемых в процессе разработки, также будет применяться в производственной среде. Это делает более предсказуемым поведение рабочего приложения и предотвращает проблемы с управлением версиями, которые могут повлиять на работу пользователей.

## 1\. Регистрация клиента Azure AD

Для использования этого примера потребуется клиент Azure Active Directory. Если вы не знаете точно, что такое клиент или как можно его получить, перейдите к разделу [Как получить клиент Azure AD](active-directory-howto-tenant.md).

## 2) Создание приложения

Теперь необходимо создать приложение в вашем каталоге. Оно будет передавать в Azure AD сведения, необходимые для безопасного обмена данными с вашим приложением. В этом случае как клиентское приложение, так и веб-API будут представлены одним **идентификатором приложения**, так как они включают в себя одно приложение логики. Создайте приложение, выполнив [эти указания](active-directory-how-applications-are-added.md). Если вы создаете бизнес-приложение, возможно, [вам будут полезны эти дополнительные инструкции](active-directory-applications-guiding-developers-for-lob-applications.md).

Не забудьте сделать следующее.

- Выполните вход на портале управления Azure.
- В левой панели навигации нажмите **Active Directory**.
- Выберите клиент, в котором нужно зарегистрировать приложение.
- Перейдите во вкладку **Приложения** и нажмите кнопку Добавить в нижней панели.
- Следуйте инструкциям на экране, а затем создайте новое **веб-приложение и/или WebAPI**.
    - **Имя** приложения отображает его описание конечным пользователям.
    - **URL-адрес входа** — это базовый URL-адрес вашего приложения. Значение по умолчанию в примере кода — `https://localhost:8080`.
    - **URI идентификатора приложения** — это уникальный идентификатор вашего приложения. Соглашение заключается в использовании `https://<tenant-domain>/<app-name>`, например, `https://contoso.onmicrosoft.com/my-first-aad-app`
- После завершения регистрации Azure AD назначает приложению уникальный идентификатор клиента. Это значение понадобится в следующих разделах, поэтому его стоит скопировать из вкладки «Настройка».

- НАПОМИНАНИЕ. Создайте для своего приложения **секрет приложения** и скопируйте его. Скоро он вам понадобится.
- НАПОМИНАНИЕ. Скопируйте **идентификатор приложения**, назначенный приложению. Он также вам скоро понадобится.


## 3\. Загрузка node.js для своей платформы
Чтобы успешно использовать этот пример, необходимо иметь рабочую установку Node.js.

Установите Node.js с сайта [http://nodejs.org](http://nodejs.org).

## 4\. Установка MongoDB на своей платформе

Чтобы успешно использовать этот пример, необходимо иметь рабочую установку MongoDB. Мы будем использовать MongoDB, чтобы сделать наш интерфейс REST API постоянным для экземпляров сервера.

Установите MongoDB с сайта [http://mongodb.org](http://www.mongodb.org).

> [AZURE.NOTE] В данном руководстве предполагается, что вы используете стандартный установочный пакет и стандартные конечные точки сервера для MongoDB, которыми на момент написания этой статьи являются mongodb://localhost.


## 5\. Установка модулей Restify для веб-API

Мы будем использовать Resitfy для построения нашего интерфейса REST API. Restify представляет собой минимальную и гибкую платформу приложений Node.js, полученную на основе Express, которая имеет широкий набор возможностей, позволяющих создавать интерфейсы REST API поверх Connect.

### Установка Restify

В командной строке перейдите в каталог azuread. Если каталог **azuread** не существует, создайте его.

`cd azuread - or- mkdir azuread; cd azuread`

Введите следующую команду:

`npm install restify`

Эта команда устанавливает Restify.

#### Вы получили сообщение об ошибке?

При использовании npm в некоторых операционных системах может появиться сообщение об ошибке Error: EPERM, chmod '/usr/local/bin/..' и предложение попробовать использовать учетную запись с правами администратора. В этом случае необходимо с помощью команды sudo запустить npm с более высоким уровнем привилегий.

#### Вы получили сообщение об ошибке, которая относится к DTRACE?

При установке Restify может появиться примерно следующее сообщение:

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


Restify предоставляет мощный механизм для трассировки вызовов REST с помощью DTrace. Однако во многих операционных системах DTrace отсутствует. Эти ошибки можно игнорировать.


Результат этой команды должен выглядеть аналогично следующему:


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


## 6\. Установка Passport.js в веб-API

[Passport](http://passportjs.org/) — промежуточный слой проверки подлинности для Node.js. Он имеет очень гибкую и модульную структуру, которая позволяет сравнительно незаметно размещать данный слой в любом приложении на основе Express или в веб-приложении Resitify. Полный набор стратегий поддерживает процесс проверки подлинности с помощью имени пользователя и пароля, Facebook, Twitter и проч. Мы разработали стратегию для Azure Active Directory. Мы установим этот модуль, а затем добавим подключаемый модуль стратегии Azure Active Directory.

В командной строке перейдите в каталог azuread.

Введите следующую команду для установки passport.js

`npm install passport`

Результат этой команды должен выглядеть примерно следующим образом:

	passport@0.1.17 node_modules\passport
	├── pause@0.0.1
	└── pkginfo@0.2.3

## 7\. Добавление Passport-Azure-AD в веб-API

Далее мы добавим стратегию OAuth с помощью набора стратегий passport-azure-ad, соединяющих Azure Active Directory с Passport. Мы будем использовать эту стратегию для токенов носителя в этом примере Rest API.

> [AZURE.NOTE] Несмотря на то что OAuth2 предоставляет платформу, в которой может быть выдан маркер любого известного типа, широкое распространение получили маркеры только некоторых типов. В результате для защиты конечных точек появились маркеры носителя. Маркеры носителя относятся типу наиболее часто выдаваемых маркеров в OAuth2, и во многих реализациях предполагается, что выдаются только маркеры данного типа.

В командной строке перейдите в каталог azuread.

Введите следующую команду, чтобы установить модуль passport-azure-ad Passport.js.

`npm install passport-azure-ad`

Результат этой команды должен выглядеть примерно следующим образом:

	``
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



## 8\. Добавление модулей MongoDB в веб-API

Мы будем использовать MongoDB в качестве хранилища данных. По этой причине нам нужно установить широко используемый подключаемый модуль для управления моделями и схемами, которые называются Mongoose, а также драйвер базы данных для MongoDB, также называемый MongoDB.


* `npm install mongoose`

## 9\. Установка дополнительных модулей

Далее мы установим остальные необходимые модули.


В окне командной строки перейдите в каталог **azuread**, если вы еще в нем не находитесь:

`cd azuread`


Введите следующие команды для установки следующих модулей в каталоге node\_modules:

* `npm install assert-plus`
* `npm install bunyan`
* `npm update`


## 10\. Создание файла server.js с зависимостями

Файл server.js будет выполнять основную часть нашего функционала для нашего сервера веб-интерфейса API. Мы добавим в этот файл основную часть нашего кода. В производственных целях рекомендуется разделить функционал на небольшие файлы, такие как отдельные маршруты и контроллеры. В этой демонстрации для данного функционала мы будем использовать server.js.

В окне командной строки перейдите в каталог **azuread**, если вы еще в нем не находитесь:

`cd azuread`

Создайте файл `server.js` в любом удобном редакторе и добавьте следующие сведения:

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

Сохраните файл. Мы вернемся к нему позже.

## 11\. Создание файла конфигурации для хранения параметров Azure AD

Данный файл кода передает параметры конфигурации из вашего портала Azure Active Directory в Passport.js. Эти значения конфигурации были созданы при добавлении веб-интерфейса API на портал в первой части настоящего пошагового руководства. Мы объясним, что нужно будет сделать со значениями этих параметров после копирования кода.


В окне командной строки перейдите в каталог **azuread**, если вы еще в нем не находитесь:

`cd azuread`

Создайте файл `config.js` в любом удобном редакторе и добавьте следующие сведения:

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
Сохраните файл.

## 12\. Добавление конфигурации в файл server.js

Нам нужно считать эти значения из файла конфигурации, созданного для нашего приложения. Для этого мы просто добавляем файл .config как требуемый ресурс в нашем приложении, а затем настраиваем глобальные переменные в соответствии с переменными в документе config.js

В окне командной строки перейдите в каталог **azuread**, если вы еще в нем не находитесь:

`cd azuread`

Откройте свой файл `server.js` в любом удобном редакторе и добавьте следующие сведения:

```Javascript
var config = require('./config');
```
Затем добавьте новый раздел в `server.js` со следующим кодом:

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

// array to hold logged in users and the current logged in user (owner)
var users = [];
var owner = null;

// Our logger
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

  // if logging level specified, switch to it.
  if (config.creds.loggingLevel) { log.levels("console", config.creds.loggingLevel); }

// MongoDB setup
// Setup some configuration
var serverPort = process.env.PORT || 8080;
var serverURI = (process.env.PORT) ? config.creds.mongoose_auth_mongohq : config.creds.mongoose_auth_local;
```

Сохраните файл.



## 13\. Добавление сведений о модели и схеме MongoDB с помощью Moongoose

Теперь вся эта подготовка начинает себя оправдывать, так как мы помещаем вместе следующие три файла в службу REST API.

В данном пошаговом руководстве для хранения наших задач мы будем использовать MongoDB, как описано в ***шаге 4***.

Если вы помните, применительно к файлу `config.js`, созданному на ***шаге 11***, мы вызывали нашу базу данных `tasklist`, так как именно ее мы указали в конце нашего URL-адреса подключения mogoose\_auth\_local. Заранее создавать эту базу данных в MongoDB не нужно. Она создаст ее для нас при первом запуске нашего приложения сервера (если она еще не существует).

Теперь, когда мы указали серверу, какую базу данных MongoDB мы бы хотели использовать, необходимо написать дополнительный код для создания модели и схемы для наших задач на сервере.

#### Описание модели

Наша модель схемы является очень простой. При необходимости ее можно развернуть.

ИМЯ — имя, назначенное задаче. ***Строка***

ЗАДАЧА — сама задача. ***Строка***

Дата — дата выполнения задачи. ***Дата и время***

ЗАВЕРШЕНО — завершена задача или нет. ***ЛОГИЧЕСКИЙ***

#### Создание схемы в коде


В окне командной строки перейдите в каталог **azuread**, если вы еще в нем не находитесь:

`cd azuread`

Откройте свой файл `server.js` в любом удобном редакторе и добавьте следующие сведения под элементом конфигурации:

```Javascript
// Connect to MongoDB
global.db = mongoose.connect(serverURI);
var Schema = mongoose.Schema;
log.info('MongoDB Schema loaded');

// Here we create a schema to store our tasks and users. Pretty simple schema for now.
var TaskSchema = new Schema({
    owner: String,
    task: String,
    completed: Boolean,
    date: Date
});

// Use the schema to register a model
mongoose.model('Task', TaskSchema);
var Task = mongoose.model('Task');
```
Как видно из кода, мы создаем нашу схему, а затем объект модели, который мы будем использовать для хранения наших данных в при определении наших ***маршрутов***.

## 14\. Добавление маршрутов для сервера REST API задачи

Теперь, когда у нас есть модель базы данных для работы, добавим маршруты, которые мы будем использовать для нашего сервера REST API.

### О маршрутах в Restify

Маршруты работают в Restify точно так же, как и при использовании стека Express. Вы определяете маршруты с помощью идентификатора URI, который, как предполагается, будут вызывать клиентские приложения. Как правило, свои маршруты вы определяете в отдельном файле. Для наших целей мы поместим наши маршруты в файл server.js. Для использования в рабочей среде рекомендуем разделить их на свои собственные файлы.

Типичный шаблон для маршрута Restify выглядит следующим образом:

```Javascript
function createObject(req, res, next) {

// do work on Object

 _object.name = req.params.object; // passed value is in req.params under object

 ///...

return next(); // keep the server going
}

....

server.post('/service/:add/:object', createObject); // calls createObject on routes that match this.

```


Это шаблон на самом базовом уровне. Resitfy (и Express) предоставляет более мощные функциональные возможности, например определение типов приложений и выполнение сложной маршрутизации между разными конечными точками. В нашем случае мы сохраним эти маршруты очень простым образом.

### 1\. Добавление маршрутов по умолчанию на наш сервер

Теперь нужно добавить базовые маршруты CRUD для создания, извлечения, обновления и удаления.

В окне командной строки перейдите в каталог **azuread**, если вы еще в нем не находитесь:

`cd azuread`

Откройте свой файл `server.js` в любом удобном редакторе и добавьте следующие сведения под ранее внесенными значениями в базе данных:

```Javascript

/**
 *
 * APIs for our REST Task server
 */

// Create a task

function createTask(req, res, next) {

    // Resitify currently has a bug which doesn't allow you to set default headers
    // This headers comply with CORS and allow us to mongodbServer our response to any origin

    res.header("Access-Control-Allow-Origin", "*");
    res.header("Access-Control-Allow-Headers", "X-Requested-With");

    // Create a new task model, fill it up and save it to Mongodb
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


// Delete a task by name

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

// Delete all tasks

function removeAll(req, res, next) {
    Task.remove();
    res.send(204);
    return next();
}


// Get a specific task based on name

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
    // Resitify currently has a bug which doesn't allow you to set default headers
    // This headers comply with CORS and allow us to mongodbServer our response to any origin

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
            log.warn(err, "There is no tasks in the database. Did you initalize the database as stated in the README?");
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

### 2) Добавление в API обработки ошибок

```

///--- Errors for communicating something interesting back to the client

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


## 15\. Создание сервера

Мы определили базу данных, у нас есть маршруты, и нам остается только добавить наш экземпляр сервера, который будет управлять нашими вызовами.

В Restify (и Express) имеются разнообразные возможности для настройки, которую можно выполнять для сервера REST API. Но, и в данном случае мы будем использовать самый базовый вариант настройки для наших целей.

```Javascript
/**
 * Our Server
 */


var server = restify.createServer({
    name: "Azure Active Directroy TODO Server",
    version: "2.0.1"
});

// Ensure we don't drop data on uploads
server.pre(restify.pre.pause());

// Clean up sloppy paths like //todo//////1//
server.pre(restify.pre.sanitizePath());

// Handles annoying user agents (curl)
server.pre(restify.pre.userAgentConnection());

// Set a per request bunyan logger (with requestid filled in)
server.use(restify.requestLogger());

// Allow 5 requests/second by IP, and burst to 10
server.use(restify.throttle({
    burst: 10,
    rate: 5,
    ip: true,
}));

// Use the common stuff you probably want
server.use(restify.acceptParser(server.acceptable));
server.use(restify.dateParser());
server.use(restify.queryParser());
server.use(restify.gzipResponse());
server.use(restify.bodyParser({
    mapParams: true
})); // Allows for JSON mapping to REST
```

## 16\. Добавление маршрутов на сервер (пока без проверки подлинности)

```Javascript
/// Now the real handlers. Here we just CRUD
/**
/*
/* Each of these handlers are protected by our OIDCBearerStrategy by invoking 'oidc-bearer'
/* in the pasport.authenticate() method. We set 'session: false' as REST is stateless and
/* we don't need to maintain session state. You can experiement removing API protection
/* by removing the passport.authenticate() method like so:
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

## 17\. Запуск сервера перед добавлением поддержки OAuth

Прежде чем добавить аутентификацию, протестируйте сервер.

Самый простой способ для этого — использовать curl в командной строке. Перед этим нам потребуется простая служебная программа, которая позволяет анализировать выходные данные в формате JSON. Для этого необходимо установить инструмент json, так как он применяется во всех примерах, приведенных ниже.

`$npm install -g jsontool`

Это обеспечивает глобальную установку средства JSON. Теперь, когда мы это сделали, вернемся к серверу.

Сначала убедитесь, выполняется ли ваш экземпляр monogoDB.

`$sudo mongod`

Затем перейдите в каталог и запустите curling.

`$ cd azuread` `$ node server.js`

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

Затем можно добавить задачу следующим образом:

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

Если все это работает, мы готовы к добавлению OAuth к серверу REST API.

**У вас уже есть сервер REST API с MongoDB!**


## 18\. Добавление функции проверки подлинности на сервер REST API

Теперь, когда у нас есть работающий REST API (примите наши поздравления!), сделаем его полезным для Azure AD.

В окне командной строки перейдите в каталог **azuread**, если вы еще в нем не находитесь:

`cd azuread`

### 1\. Использование стратегии OIDCBearerStrategy, включенной в passport-azure-ad

Мы создали стандартный сервер REST TODO без какой-либо авторизации. Самое время добавить такую функцию.

Сначала необходимо указать, что будет использоваться Passport. Сделайте это сразу после конфигурации другого сервера:

```Javascript
// Let's start using Passport.js

server.use(passport.initialize()); // Starts passport
server.use(passport.session()); // Provides session support
```

> [AZURE.TIP]
При записи интерфейсов API всегда нужно связывать данные с уникальными параметрами маркера, которые пользователь не сможет подделать. Когда этот сервер сохраняет элементы TODO, он делает это на основании идентификатора объекта пользователя в маркере (вызываемого с помощью token.oid), который мы указываем в поле owner. Таким образом только этот пользователь сможет получать доступ к своим элементам TODO. Значение owner не отображается в API, поэтому внешний пользователь может запросить элементы TODO другого пользователя, даже если для них пройдена аутентификация.

Теперь мы используем стратегию Bearer, включенную в состав passport-azure-ad. Просмотрите код и его краткое описание ниже. Вставьте это после добавленного ранее:

```Javascript
/**
/*
/* Calling the OIDCBearerStrategy and managing users
/*
/* Passport pattern provides the need to manage users and info tokens
/* with a FindorCreate() method that must be provided by the implementor.
/* Here we just autoregister any user and implement a FindById().
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

Passport использует аналогичную схему для всех своих стратегий (Twitter, Facebook и т. д.), представленных всеми модулями записи стратегий. Просматривая стратегию, можно увидеть, что мы передаем function(), у которой есть маркер, и обрабатываем параметры. Стратегия возвратится к нам после выполнения всей своей работы. После ее возврата потребуется сохранить пользователя и спрятать маркер, так как нам больше не придется его запрашивать.

> [AZURE.IMPORTANT]
Приведенный выше код принимает всех пользователей, которые прошли аутентификацию на сервере. Это называется автоматической регистрацией. На рабочих серверах не нужно разрешать другим пользователям входить без выбранной вами регистрации. Это типично для клиентских приложений, в которых разрешается регистрация через Facebook и, помимо этого, требуется ввести дополнительную информацию. Если бы это не была программа командной строки, мы могли бы извлечь электронный адрес из возвращенного объекта маркера и затем запросить дополнительную информацию. Так как это всего лишь тестовый сервер, мы просто добавим их в базу данных в памяти.

### 2) Защита некоторых конечных точек

Для защиты конечных точек необходимо указать вызов `passport.authenticate()` с протоколом, который вы хотите использовать.

Изменим наш маршрут в коде сервера, чтобы сделать нечто более интересное:

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

## 19\. Повторный запуск серверного приложения для проверки отказа в доступе

Снова используем `curl`, чтобы определить, если у нас сейчас защита OAuth2 применительно к нашим конечным точкам. Мы это сделаем до запуска любого клиентского пакета SDK для этой конечной точки. Возвращаемые заголовки должны содержать достаточные сведения, чтобы мы поняли, что двигаемся в правильном направлении.

Прежде всего убедитесь, что ваш экземпляр monogoDB работает.

  $sudo mongod

Затем перейдите в каталог и запустите curling.

  $ cd azuread $ node server.js

Попробуйте выполнить базовую команду POST:

`$ curl -isS -X POST http://127.0.0.1:8080/tasks/brandon/Hello`

```Shell
HTTP/1.1 401 Unauthorized
Connection: close
WWW-Authenticate: Bearer realm="Users"
Date: Tue, 14 Jul 2015 05:45:03 GMT
Transfer-Encoding: chunked
```

401 — это искомый ответ, так как он указывает, что уровень Passport пытается перенаправлять к конечной точке authorize, а это именно то, что нам нужно.

## Поздравляем! Теперь у вас есть служба REST API, в которой применяется OAuth2.

До сих пор вы успешно применяли этот сервер без использования клиента, совместимого с OAuth2. Теперь вам будет нужно поработать с дополнительным пошаговым руководством.

Если вы просто искали сведения о реализации интерфейса REST API с использованием Restify и OAuth2, то у вас уже есть нужный код, чтобы продолжать разработку своей службы и применять приведенный пример.

Если вам интересно узнать о следующих шагах по изучению ADAL, то ниже вы найдете список некоторых поддерживаемых клиентов ADAL, которые мы рекомендуем использовать:

Просто клонируйте их на своем компьютере разработчика и настройте нужным образом, как указано в этом пошаговом руководстве.

[ADAL для iOS](https://github.com/MSOpenTech/azure-activedirectory-library-for-ios)

[ADAL для Android](https://github.com/MSOpenTech/azure-activedirectory-library-for-android)


[AZURE.INCLUDE [active-directory-devquickstarts-additional-resources](../../includes/active-directory-devquickstarts-additional-resources.md)]

<!---HONumber=AcomDC_0921_2016-->